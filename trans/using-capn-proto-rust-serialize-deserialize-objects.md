# 在 Rust 中使用 Cap'n Proto 来序列化和反序列化对象

> 原文：<https://blog.logrocket.com/using-capn-proto-rust-serialize-deserialize-objects/>

当对象很大很复杂时，使用 JSON 和 XML 这样的格式进行序列化会变得低效。原因之一是这些格式将对象的结构与其包含的数据一起存储。

这种方法允许您在没有模式的情况下直接序列化和反序列化对象。然而，当对象非常复杂时，就占用的空间以及编码和解码的过程而言，它变得低效。

Cap'n Proto 是一个数据序列化器，就像 JSON 和 XML 一样。但是与 JSON 和 XML 不同，它对编码和解码对象都使用模式。

模式的使用消除了在编码时存储对象结构的需要。由于不存储对象的结构，编码和解码非常有效。

在本文中，您将了解 Cap'n Proto，它是如何工作的，以及如何在 Rust 项目中使用它。我们将涵盖:

为了阅读本文，您需要具备以下先决条件:

## Cap'n Proto 概述

Cap'n Proto 是一种数据交换格式，就像 JSON、XML 等等。

数据交换格式允许你[执行序列化和反序列化](https://blog.logrocket.com/rust-serialization-whats-ready-for-production-today/)。序列化是将对象编码成字节。反序列化是指将字节解码回对象。

这些工具帮助开发人员在不同的计算机程序之间交换对象。

与 JSON 和许多其他数据交换格式不同，Cap'n Proto 的大多数操作都需要一个模式。这个模式包括编码、解码和创建对象。

模式是描述对象结构的程序。即使在 Cap'n Proto 中，通常也有专门的语言来编写模式。在用 Cap'n Proto 编写模式之后，您需要将模式编译成您想要使用的编程语言。

Cap'n Proto 有一个用于从模式创建对象的构建器，一个用于将对象编码成字节的序列化器，以及一个用于将字节解码成对象的读取器。下图概述了这三个组件:

![Three Part Diagram Stacked Vertically. Part One Labeled Building An Object. From Left To Right, Shows Schema Used By Builder To Create Object. Part Two Labeled Encoding. From Left To Right, Shows Object Being Encoded By Serializer Into Bytes. Part Three Labeled Decoding. From Left To Right, Shows Input Of Schema And Encoded Bytes Being Decoded By Reader Into Object.](img/597fb30824d041652dfbbee3460a1d75.png)

除了将 Cap'n Proto 用作数据序列化器之外，您还可以将它用于 RPC 系统、数据库系统等等。在本文中，我们将关注 Rust 中 Cap'n Proto 的序列化和反序列化。

## Rust 中的 Cap'n Proto 入门

在这一节中，我将指导您定义必要的依赖项，创建一个模式，并为 Rust 项目编译使用 Cap'n Proto 的模式。

但是在开始之前，您需要初始化一个项目。运行下面的命令来初始化一个新项目:

```
$ cargo new --bin cap-n-proto-project 

```

现在，打开您的项目的`Cargo.toml`文件，添加以下内容来定义在 Rust 项目中使用 Cap'n Proto 的依赖关系:

```
[dependencies]
capnp  = "0.14"

[build-dependencies]
capnpc = "0.14"

```

下一步是创建一个模式。为此，首先创建一个名为`schemas`的文件夹。然后，在`schemas`文件夹中创建一个`person_schema.capnp`文件。最后，将以下内容写入您的`person_schema.capnp`文件:

```
@0x8512a370fcfefa23;

struct Person {
 name @0 :Text;
 age @1 :UInt8;
}

```

在模式中，第一行是唯一的文件标识符。您可以通过在终端中运行`capnp id`来生成一个新的。

文件 ID 下的`struct`是一个名为`Person`的数据结构，有两个字段:`name`和`age`。

为了允许编码的对象与更新的模式兼容，Cap'n Proto 要求您在每个字段后包含一个 ID。每个 ID 以一个`@`符号开头，后面跟着一个整数。您应该使用的第一个数字是`0`。

当添加新字段时，您应该放置一个新的数字标签。例如:

```
 name @0 :Text;
 age @1 :UInt8;
 new_field @2 :DataType;

```

创建模式后，按照下面的步骤设置其编译。

首先，[安装 Cap'n Proto 可执行文件](https://capnproto.org/install.html)。编译架构需要可执行文件。

接下来，在`src`文件夹中创建一个`build.rs`文件。然后，将这段代码写入您的`build.rs`文件:

```
extern crate capnpc;

fn main () {
  capnpc::CompilerCommand::new()
    .output_path("src/")
    .src_prefix("schemas/")
    .file("schemas/person_schema.capnp")
    .run().unwrap();

```

让我们暂停一会儿，了解一下`build.rs`文件中发生了什么。上面的代码首先用`CompilerCommand`初始化一个编译器。然后，它注册一个`output_path`或目录，用于存放编译后的代码。

接下来，`.src_prefix`设置一个前缀，这样编译器就可以知道在给编译后的代码命名时要忽略哪些字符。然后，它提供一个到您想要编译的模式的`.file()`路径。最后，它执行编译器。

现在，让我们回到设置模式编译。打开您的`Cargo.toml`文件，注册运行`cargo build`时要执行的`src/build.rs`:

```
[package]
name = "proto-dev"
version = "0.1.0"
edition = "2021"
build = "src/build.rs"    # <-- this line

```

添加了上面的代码后，无论何时运行`cargo build`，`cargo`都会在构建项目时编译模式。最后，在你的终端上运行`cargo build`命令。

当项目构建完成后，您会在`src`中看到一个`person_schema_capnp.rs`文件。

## 序列化对象

现在您已经设置好了一切，是时候用 Cap'n Proto 做些实际的事情了。在这一节中，我将向您展示如何用您的模式创建一个对象，以及如何序列化该对象。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

下面是创建和序列化对象的代码。清除`main.rs`文件并将其粘贴到其中:

```
use capnp::message::Builder;
use capnp::serialize;

pub mod person_schema_capnp;

fn main() {
   let mut message = Builder::new_default();

   let mut person = message.init_root::<person_schema_capnp::person::Builder>();
   person.set_name("John");
   person.set_age(23);

   let data = serialize::write_message_to_words(&message);
   println!("{:?}", data);
}

```

在上面的前两行代码中，我们从`capnp`导入了`Builder`和`serialize`。`Builder`允许您用模式构建一个对象，而`serialize`允许您序列化该对象。

然后我们从`person_schema_capnp.rs`导入模式，然后初始化一个消息生成器。

在`fn main()`下的第九行代码中，我们用模式的`Builder`在`message`中设置了一个`person`对象。在下面两行中，我们在对象中保存了两个值:`name`和`age`。

最后，我们将对象序列化为一个向量，然后在之后的行中打印出这个向量。

## 反序列化对象

既然您已经看到了如何在 Rust 中使用 Cap'n Proto 创建对象并将其序列化为矢量，那么现在就应该将矢量反序列化为对象了。在这一节中，我将向您展示如何操作。

首先，您需要从`capnp`导入`ReaderOptions`:

```
use capnp::message::ReaderOptions;

```

接下来，用`serialize`创建一个阅读器:

```
let reader = serialize::read_message(
       data.as_slice(),
       ReaderOptions::new()
   ).unwrap();

```

在上面的例子中，`data`是我们之前将`person`对象序列化成的向量。在将矢量传递给`serialize::read_message()`之前，你需要将矢量转换成切片。这就是我们用`data.as_slice()`的原因。

最后，使用`reader`变量和`person_schema_capnp.rs`模式的`Reader`来组装对象，如下所示:

```
let person = reader.get_root::<person_schema_capnp::person::Reader>().unwrap();

```

为了验证组装对象是否有效，您可以在下面添加这一行，以打印出我们存储在对象中的名称:

```
let name = person.get_name().unwrap();
println!("Name: {name}");

```

结合我们在上一节和本节中所做的，我们将在我们的`main.rs`文件中得到以下内容:

```
use capnp::message::Builder;
use capnp::message::ReaderOptions;
use capnp::serialize;

pub mod person_schema_capnp;

fn main() {

   // Creating object
   let mut message = Builder::new_default();
   let mut person = message.init_root::<person_schema_capnp::person::Builder>();
   person.set_name("John");
   person.set_age(23);

   // Serializing object
   let data = serialize::write_message_to_words(&message);
   println!("{:?}", data);

   // Deserializing object
   let reader = serialize::read_message(
       data.as_slice(),
       ReaderOptions::new()
   ).unwrap();

   let person = reader.get_root::<person_schema_capnp::person::Reader>().unwrap();
   let name = person.get_name().unwrap();
   println!("Name: {name}");
}

```

一旦您在终端中运行下面的`cargo run`命令，您将得到如下输出:

```
$ cargo run
   Compiling cap-n-proto-project v0.1.0 (/path/to/project)
    Finished dev [unoptimized + debuginfo] target(s) in 2.55s
     Running `target/debug/cap-n-proto-project`
[0, 0, 0, 0, 4, 0, 0, 0, 0, 0, 0, 0, 1, 0, 1, 0, 23, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 42, 0, 0, 0, 74, 111, 104, 110, 0, 0, 0, 0]
Name: John

```

输出显示了序列化的对象和打印的`Name`值，这验证了我们在 Rust 中使用 Cap'n Proto 成功地序列化和反序列化了我们的对象。

## 结论

在本文中，您了解了 Cap'n Proto 以及如何在 Rust 中将它用作对象序列化器。

为了进一步加深你的知识，请务必查看 GitHub 上的 [Cap'n Proto 以及查看 Rust 文档的](https://github.com/capnproto/capnproto-rust)[。](https://docs.capnproto-rust.org/capnp/index.html)

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。