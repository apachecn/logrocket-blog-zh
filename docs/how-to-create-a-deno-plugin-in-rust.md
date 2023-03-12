# 如何在 Rust 中创建 Deno 插件

> 原文：<https://blog.logrocket.com/how-to-create-a-deno-plugin-in-rust/>

Deno 是用 Rust 和 V8 构建的一个新的 JavaScript 运行时，使您能够在浏览器之外运行 JavaScript。 [Deno 比 Node.js](https://blog.logrocket.com/what-is-deno/) 更安全，因为它默认限制网络和文件系统访问。

Deno 很酷的一点是，你可以用 Rust 编写插件，并在 Deno 代码中使用它们。在本教程中，我们将向你展示如何在 Rust 中创建 Deno 插件。

我们将讨论以下内容:

## 为什么要用 Rust 写 Deno 插件？

Deno 中的插件通常会提高性能，并提供对更广泛工具的访问。

由于其高性能的本质，插件经常被用于繁重任务的计算，如图像处理。插件还可以让你访问各种用其他语言编写的库，包括高质量的 Rust crates。

## Deno 插件项目结构

插件项目结构与任何 Deno 模块相同。出于本教程的目的，您可以使用[这个样板文件](https://github.com/BrunnerLivio/deno-module-starter):

```
git clone https://github.com/anshulrgoyal/deno-rust-starter.git my_module

```

首先，为插件构建 Rust 样板文件:

```
cd my_module/native
cargo build

```

接下来，运行一个测试来验证 Deno 正在选择正确的库:

```
cd my_module/native
deno test --unstable --allow-plugin

```

样板文件在`native`目录中包含一个 Rust 项目，在根目录中包含一个 Deno 模块。

## 构建一个 Rust 项目

Rust 项目编译由 Deno 运行时加载的动态库。库的文件类型和名称取决于操作系统。Rust 项目可能会编译成一个`so`文件——`dylib`或`dll`——编译后的文件名也可能不同。样板文件可以处理三种主要平台:Linux、macOS 和 Windows。

```
[package]
name = "native"
version = "0.1.0"
authors = ["anshul <[email protected]>"]
edition = "2018"

[lib]
name = "native"
crate-type = ["cdylib"]

[dependencies]
deno_core = "0.75.0"

├── README.md
├── deps.ts
├── mod.ts
├── mod_test.ts
├── native
│   ├── Cargo.lock
│   ├── Cargo.toml
│   ├── src
│       └── lib.rs
├── test.ts
├── test_deps.ts
└── tsconfig.json

```

`mod.ts`文件是另一个应用程序使用您的模块导入的主文件。

## 添加 Rust 代码

在本教程中，我们将向您展示如何使用`oxipng`箱构建 PNG 优化器。每个 Deno 插件必须导出`deno_plugin_init`函数，并注册插件导出的所有方法。

`#[no_mangle]`属性告诉编译器不要改变函数的名称:

```
#[no_mangle]
pub fn deno_plugin_init(interface: &mut dyn Interface) {
 // register the function. Pass name and function to register method
    interface.register_op("hello_world",hello_world);
}

```

## 创建优化器函数

每个导出的函数都有相同的签名。Deno 插件只能导出函数。这些函数可以是同步或异步的，这取决于返回类型。

```
fn optimise(_interface: &mut dyn Interface,
    zero_copy: &mut [ZeroCopyBuf],
) -> Op {
    // get first argument
    let first=zero_copy.first().unwrap();
    let opts: oxipng::Options = Default::default();
    // convert vector
    let result = oxipng::optimize_from_memory(&first.to_vec(), &opts).unwrap();
    // move to heap so that deno can use it
    Op::Sync(Box::from(result))
}

```

该函数的第二个参数包含一个缓冲区数组。数组中的每个缓冲区表示调用时传递给导出函数的参数。这些缓冲区根据需要被序列化为字符串或其他数据类型。

上面的代码获取`zero_copy`的第一个元素，并将其传递给`optimize_from_memory`。数组的第一个元素是从 Deno 代码调用时传递给`optimize`函数的文件。文件以字节形式传递。该函数处理文件并将结果作为一个`Box`返回。返回类型是带有两个变量`sync`和`async`的`Op`枚举。

使用`cargo build`命令构建代码。现在这个插件可以在 Deno 中使用了。

## 在 Deno 中加载一个 Rust 插件

现在插件已经编译好了，让我们用 Deno 加载它。

该插件仍在开发中，是不稳定 API 的一部分，所以需要`--unstable`标志，就像`--allow-plugin`一样。

```
let path = ""
// check the type of OS to load correct file
if (Deno.build.os === "linux") {
// linux file emited by rust compiler
  path = "./native/target/debug/libnative.so"
} else if (Deno.build.os === "windows") {
// windows file emited by rust compiler
  path = "./native/target/debug/native.dll"
} else if (Deno.build.os === "darwin") {
// macos file emited by rust comipler
  path = "./native/target/debug/libnative.dylib"
}
// load plugin from file system
const rid = Deno.openPlugin(path);
// Get available methods on plugin
//@ts-Expect-Error
const { optimise:optimise_native } = (Deno as any).core.ops();

export async function optimise(fileName: string): Promise<Uint8Array> {
// reading a file
  const file = await Deno.open(fileName);
// getting content
  const value = await Deno.readAll(file)
// closing file
  await Deno.close(file.rid)
// running the native plugin method using Deno dispatch method
  return (Deno as any).core.dispatch(optimise_native, value)
}

```

使用`openPlugin`方法加载每个插件。然后，使用`ops`方法获取方法标识符，该方法标识符执行插件导出的代码。

`dispatch`用于运行原生插件导出的代码。第一个参数是方法标识符；其余的为本机函数传递。在这种情况下，文件被传递。

## 编写异步插件

由于 Deno 是单线程的，所以阻塞主线程是不明智的。Deno 允许你从原生函数返回一个 future，你可以用它和 OS 线程一起编写一个不阻塞主线程的函数。

```
fn optimise_async(_interface: &mut dyn Interface,
    zero_copy: &mut [ZeroCopyBuf],
) -> Op {
// get first argument
    let first=zero_copy.first().unwrap();
    let opts: oxipng::Options = Default::default();
    let arg=first.to_vec();
// create a new future
    let fut = async move {
// create a channel to send result once done to main thread
        let (tx, rx) = futures::channel::oneshot::channel::<oxipng::PngResult<Vec<u8>>>();
// create a new thread
        std::thread::spawn(move || {
// perform work
          let result = oxipng::optimize_from_memory(&arg, &opts);
// send result to main thread
          tx.send(result).unwrap();
        });
// receive the result
        let result=rx.await;
// create a boxed slice
        let result_box = result.unwrap().unwrap().into_boxed_slice();
// return boxed slice from the future
        result_box
      };
// return the future
    Op::Async(fut.boxed())
}

```

使用`async`块创建一个未来，并作为装箱的未来返回。Deno 处理未来的完成，并通知插件的 Deno 端。信道用于新线程和主线程之间的通信。

Deno 代码不需要太多更新——只需要一个新的`asyncHandler`来处理任务的完成:

```
let path = ""
if (Deno.build.os === "linux") {
  path = "./native/target/debug/libnative.so"
} else if (Deno.build.os === "windows") {
  path = "./native/target/debug/native.dll"
} else if (Deno.build.os === "darwin") {
  path = "./native/target/debug/libnative.dylib"
}
const rid = Deno.openPlugin(path);

const { optimise_async } = (Deno as any).core.ops();

export async function optimise(fileName: string){
  const file = await Deno.open(fileName);
  const value = await Deno.readAll(file);
  await Deno.close(file.rid);
// new handler
  (Deno as any).core.setAsyncHandler(optimise_async, (response:any) => {
    Deno.writeFile("l.png",response)
  });
// executing the native code.
  (Deno as any).core.dispatch(optimise_async,value);
}
await optimise("t.png")

await Deno.close(rid);

```

## 结论

在本教程中，我们介绍了如何使用 Rust 构建一个简单的 Deno 插件，以及如何使用 Rust futures 和`deno_core` crate 创建一个异步插件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

Rust 有一个大的生态系统，有高质量的板条箱。你可以通过创建插件在 Deno 中使用所有这些板条箱。无论是图像处理插件、数据库连接器等。，访问 Rust 插件有助于扩展 Deno 生态系统。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。