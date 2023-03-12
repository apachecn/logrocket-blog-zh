# 具有共享数组缓冲区和 Rust WebAssembly 的节点工作线程

> 原文：<https://blog.logrocket.com/node-worker-threads-shared-array-buffers-rust-webassembly/>

WebAssembly 使 Rust 能够作为 JavaScript 代码的一部分运行，这提高了性能和稳定性。然而，并不是所有的 Rust 应用程序都可以在 WebAssembly 下运行，因为它最初被设计为在浏览器内部运行，并与基于浏览器的 JS 连接。这引入了安全问题和潜在的不良行为，这就是为什么大多数功能被阻止，包括 Rust 线程和多处理。它只是一种纯语言，有普通的`stdlib`和`web_sys`，是在 Rust 中使用 WebAssembly 功能的主要库。

与基于浏览器的 JavaScript 不同，Node.js 拥有各种操作系统级别的功能。但可悲的是，没有专门为 Node.js 功能设计的 WebAssembly 版本。为了解决这个问题，您可以创建一个基于 Rust 的 WebAssembly 项目，并在 Node.js 和 Rust 之间调用函数，当您的项目需要大量计算操作来创建安全点/函数时。

WebAssembly 最初被设计为 JavaScript 中的一个原子组件，并与基于事件的消息进行通信，就像 WebWorker 现在的工作方式一样。后来的实现消除了这种差距，今天的 WebAssembly 就像一个编译好的库，可以访问低级别的 API。

### 使用 WebAssembly 线程化

在 Node.js 中使用基于 Rust 的 WebAssembly 的目的是将一些计算繁重的部分从 Node.js 卸载到 Rust，这对于需要算法或内存优化的任务来说运行速度要快得多。Rust 编译器足够聪明，可以优化它的基本函数，这使得它运行得更快。然而，单线程 Node.js 和基于 Rust 的 WebAssembly 的组合(也可以在没有线程的情况下运行)不会为您节省太多时间或资源。

想法是[使用 Node.js 模块`worker_threads`](https://blog.logrocket.com/a-complete-guide-to-threads-in-node-js-4fa3898fe74f/) 来加速一个新的 Rust WebAssembly 计算，而不需要在主线程中等待同步操作。让我们演示如何从 JavaScript WebAssembly 包装器启动 Node.js 工作线程，如下所示:

```
const {Worker} = require('worker_threads');

...
const worker = new Worker('wasm-wrapper.js', {...someDataIfWeNeed});
worker.on('message', resolve);
worker.on('error', reject);
worker.on('exit', (code) => {
  if (code !== 0) {
        console.log(`Worker stopped with exit code ${code}`);
    }
});
…

```

`wasm-wrapper.js`不是实际的 Wasm 文件；`worker_thread`只旋转 JavaScript 文件，这些文件是可解析的，包含主要的执行过程。Wasm 文件本身只是一个库，它导出的函数可以从 JavaScript 访问，这就是为什么我们需要 JS 包装函数。

### 如何在 Rust 中进行真正的 WebAssembly 集成

在 Rust 参与 WebAssembly 之前，由于缺乏编译工具，很难将一个项目编译成 Wasm 文件。Rust 社区让进入 WebAssembly 变得异常简单。

首先，安装[货/锈](https://rust-lang.org)和`[wasm-pack](https://rustwasm.github.io/wasm-pack/installer/)`。一旦基本安装设置完成，您就可以开始编码了。

```
mod utils;

use wasm_bindgen::prelude::*;

// When the `wee_alloc` feature is enabled, use `wee_alloc` as the global
// allocator.
#[cfg(feature = "wee_alloc")]
#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

#[wasm_bindgen]
pub fn greet() -> String {
    String::from("Hello World!")
}

```

这是一句基本的“你好，世界！”用于生锈的 web 组件。通过用`wasm-pack`编译它，我们得到一个完整的 JS 包装器和 Wasm 文件。

```
~# wasm-pack build --target=nodejs

# Output files
pkg/<project_name>_bg.wasm
    <project_name>.js

```

我们不打算直接使用 Wasm 文件，因为它已经用 JS 文件中的 helper 函数包装起来了。

```
const {greet} = require('./pkg/<project_name>.js');
console.log(greet());

// This will print "Hello World!"

```

这个基本示例显示了使用 Node.js 集成 WebAssembly 是多么容易。

### WebAssembly 和工作线程

我们正处于可以在 Node.js 中调用 WebAssembly 函数的阶段。同样，实际的 Node.js 工作线程只是一个 js 文件，需要作为一个单独的线程来执行。

首先，让我们创建两个 JavaScript 文件，如下所示:

```
// main.js - the main executional file to start program from
const { Worker } = require('worker_threads');
const worker = new Worker('./worker.js');
worker.once('message', (message) => {
  console.log(message);
});

// worker.js - worker file to be called from main.js
const { parentPort } = require('worker_threads');
const {greet} = require('./pkg/<project_name>.js');
parentPort.once('message', (message) => {
  parentPort.postMessage(greet());
});

```

我们的问候消息将从主线程打印，但实际的 WebAssembly 是从工作线程执行的。使用这个基本原理，我们可以将可操作的 Wasm 代码作为一个单独的线程来执行和同步，并等待来自它的消息。

一些公司正在使用 WebAssembly 跨多个工作线程进行繁重的计算性 3D 渲染。这有助于保持 JavaScripts 的主事件循环无阻塞，同时跨多个 CPU 内核进行扩展。

想在 Wasm 和 JS 之间共享一个变量怎么办？这在理论上比在实践中要复杂一些，因为 Rust 变量借用和可变引用通常会完成它们的工作。然而，不可能从 JS 到 Rust 使用一个直接的变量，因为实际的通信通道经过共享的普通内存，这只是一个缓冲区。它采用 SharedBuffer 类型，有助于在不同类型的 JavaScript 和 Rust 数据模型之间传输数据。

### 从 JS 到 Wasm 的共享数组缓冲区

Rust Wasm 实现只支持几种类型的数组。最常见的是`&[u8]`字节数组，这是一种基于字节的普通数据表示。正如您在计算机科学基础课程中所了解的，所有数据都是由字节组成的。因此，您可以通过 Wasm 和 JS 传递实际的字节数组，它表示以某种格式编码的复杂对象。

例如，让我们修改 Rust 函数，将可变数组作为参数来处理。

```
...
#[wasm_bindgen]
pub fn greet(input: &mut [u8]) -> Vec<u8> {
    input[0] = 10; // just changing some value here
    Vec::from(input)
}
…

```

Rust 代码正在等待从 JS 内存中接收一个指向数组缓冲区的可变指针，因为它在同一个进程中，所以内存指针可以相互访问。因为它是数组的实际指针，而不是副本，所以我们可以改变数组中的值，这些改变将反映在原始的 JS 内存中。

```
const {greet} = require('./pkg/noders');

const arr = new Uint8Array(11);

console.log(greet(arr)); // [10, 0, 0...]

console.log(arr);  // [10, 0, 0...]

```

这个基本原理使您能够处理 Wasm 编译的对象和 JavaScript 之间的普通数据数组。当然，您有可能为 WebAssembly 构建一个完整的共享类型系统，因为一切都可以表示为一个普通的字节数组。还记得在 C 语言中，你不得不用指针作为实际结构来制作`memcpy(...)`吗？这可能是一个类似的场景，但还没有具体的用例。通常，一个普通的字节数组消息就可以了。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 结论

WebAssembly 将接管一些繁重的任务，我们围绕它构建的工具越多，我们就会越认真地对待它——特别是如果我们现在有能力在 JS 和 Wasm 之间共享内存的话。结合 Node.js 工作线程，我们有能力在许多 CPU 内核甚至 GPU 上扩展 JavaScript 代码，因为我们可以通过 Rust WebAssembly 访问 GPU。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.