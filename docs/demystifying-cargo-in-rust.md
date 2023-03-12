# 揭开 Rust 中货物的神秘面纱

> 原文：<https://blog.logrocket.com/demystifying-cargo-in-rust/>

Rust 是一种非常好的语言。然而，它附带了一个经常被误解的工具，叫做 [Cargo](https://doc.rust-lang.org/cargo/) 。

许多人在第一次接触 Rust 时，很难理解 Cargo 是做什么的，以及它是如何工作的。例如，你可能想知道为什么你需要用`cargo run`运行你的 Rust 代码。为什么你需要货物来运行你的 Rust 程序？为什么一个 Rust 程序会有这么多`cargo`活动？

在本教程中，我们的目标是消除围绕生锈货物的神秘。我们将讨论以下内容:

## 铁锈中的货物是什么？

[Cargo](https://doc.rust-lang.org/book/ch01-03-hello-cargo.html) 是 Rust 的构建系统和包管理器。使用这个工具，您将获得一个可重复的构建，因为它允许 Rust 包在清单中声明它们的依赖关系，`Cargo.toml`。当你通过`rustup`安装铁锈时，货物也被安装。

例如，当您在 Linux 或 macOS 上运行以下命令来安装 Rust 时，也会安装 Cargo:

```
curl https://sh.rustup.rs -sSf | sh

```

Cargo 帮助你成功编译你的 Rust 程序。它下载依赖项，编译你的包，并把它们上传到 Rust 项目注册处， [crates.io](https://crates.io/) 。

## 货物如何工作

Cargo 允许 Rust 包声明它们的依赖关系。这在`Cargo.toml`文件中完成。您所要做的就是在`Cargo.toml`文件中声明运行程序所需的依赖关系。接下来，Cargo 提取关于您的依赖项的所有必要信息，并将信息构建到`Cargo.lock`文件中。

`Cargo.lock`文件的功能是允许可重复的构建。因此，即使您共享您的项目，也不应该有不同构建的情况。

您可能想知道`Cargo.toml`和`Cargo.lock`文件有什么不同。`Cargo.toml`是一种[舱单](https://en.wikipedia.org/wiki/Manifest_(transportation)#:~:text=From%20Wikipedia%2C%20the%20free%2%20%20%20%20%20%20%20%20%20%20%200encyclopedia,of%20customs%20and%20other%20officials)，是一种包含旅客、货物等详细信息的文件。在 Rust 中，清单包含关于给定项目的详细信息，比如项目名称、版本、依赖项等。清单告诉 Cargo 它需要下载哪个依赖项来成功编译您的项目。

## 什么是`Cargo.lock`？

当你构建自己的项目并与他人分享时，Rust 如何确保你和你的朋友不会因为依赖项的不同版本而有不同的构建？这就是`Cargo.lock`的用武之地。

仅仅包含你的项目的构建信息。当您的项目成功构建时，所有的构建信息都存储在您的`Cargo.lock`文件中。这包括你的构建依赖的[精确版本。您可能想知道当您已经在`Cargo.toml`中指定了您的依赖版本时，为什么这很重要。](https://www.reddit.com/r/rust/comments/2ipwvx/why_cargolock/)

当您的依赖项版本被更新，并导致您的程序意外运行时，会发生什么情况？因为在`Cargo.toml`中有依赖项的精确版本，所以 Cargo 所做的就是在试图编译你的程序时下载它们，即使它得到了更新。然而，如果您有一个`Cargo.lock`文件，Cargo 将使用`Cargo.lock`文件中依赖项的精确副本。即使在最初用`Cargo.lock`成功构建之后，你也不必冒险让某个[永远依赖](https://semver.org/)破坏你的程序。

## `Cargo.lock`对`Cargo.toml`

关于`Cargo.lock`和`Cargo.toml`要注意的第一件事是，它们都包含项目的依赖关系。然而，`Cargo.toml`是开发者写的，`Cargo.lock`是 Cargo 维护的。

除了使用一个`Cargo.toml`文件之外，还使用一个`Cargo.lock`文件的原因是为了在所有机器上实现可重复的构建。当`Cargo.toml`文件存储永远的版本时，`Cargo.lock`在一个成功的构建过程中存储依赖的精确版本。

为了更好地理解这一点，让我们假设没有`Cargo.lock`文件，我们的依赖关系的永远限制是:

```
[dependencies]
serde = "1.0"
serde json = "1.0"

```

当您构建项目时，成功构建的确切的`serde`版本是`serde = "1.0.124"`。如果`serde`被更新，并且您与同事共享您的项目，他们可能会遇到一些错误，因为`serde`更新可能与您的项目不兼容。`Cargo.lock`通过允许 Cargo 比较`Cargo.lock`文件中的信息来解决依赖性问题。

## 货物和概况

使用 Rust 中的[概要文件，很容易改变一些编译器设置。例如，您可以通过在`Cargo.toml`文件中指定来更改优化级别:](https://blog.logrocket.com/an-introduction-to-profiling-a-rust-web-application/)

```
    [profile.dev
    opt-level = 1 # Use basic optimizations. 

```

这告诉 Cargo 在编译项目时使用优化级别`1`。

Rust 有四个内置配置文件:`dev`、`test`、`bench`和`release`。运行的命令取决于要选择的配置文件。您可以在`Cargo.toml`文件的`[profile]`表中更改编译器设置。

让我们看看默认的概要文件以及与每个概要文件相关的命令和命令行标志。

### `DEV`

当您运行命令`cargo build`时，编译器使用`dev`配置文件。这个概要文件用于正常的开发和调试。

```
       [profile.dev]
        opt-level = 0
        debug = true
        debug-assertions = true
        overflow-checks = true
        lto = false
        panic = 'unwind'
        incremental = true
        codegen-units = 256
        rpath = false

```

### `RELEASE`

`RELEASE`配置文件用于发布和生产。当您使用`cargo install`命令或`-- release flag`时，将使用该配置文件。

```
        [profile.release]
        opt-level = 3
        debug = false
        debug-assertions = false
        overflow-checks = false
        lto = false
        panic = 'unwind'
        incremental = false
        codegen-units = 16
        rpath = false

```

### `TEST`

当您在调试模式下为基准运行`cargo build`命令或者构建测试时，会用到`TEST`概要文件。

```
    [profile.test]  
    opt-level = 0
    debug = 2
    debug-assertions = true
    overflow-checks = true
    lto = false
    panic = 'unwind' 
    incremental = true
    codegen-units = 256
    rpath = false            

```

### `BENCH`

当您使用`--release flag`构建基准或者构建测试时，`BENCH`就会发挥作用。

```
    [profile.bench]            
    opt-level = 3
    debug = false
    debug-assertions = false
    overflow-checks = false
    lto = false
    panic = 'unwind'    
    incremental = false
    codegen-units = 16
    rpath = false                       

```

## 货物和工作空间

在 Rust 中，您可以创建共享一个`Cargo.lock`文件、输出目录和其他设置(如概要文件)的包集合。这个集合叫做`workspace`，组成工作空间的包叫做`workspace member`

工作空间在`Cargo.toml`文件中指定。这通常通过两种方式完成:使用`root package`或`virtual manifest`。

`root package`方法包括向您的`Cargo.toml`文件添加一个`[workspace]`表。如果`[package]`是工作空间的根包，那么`[workspace]`表可以添加到已经声明了`[package]`的`Cargo.toml`文件中。

通过`virtual manifest`方法，您可以创建一个`Cargo.toml`文件并添加一个`[workspace]`表。然而，`Cargo.toml`文件中没有`[package]`表。当您想将所有的包放在不同的目录中或者没有“主”包时，这可能是一个好的选择。

## 货物指令

以下是最流行的货运命令。为了方便起见，我们将它们分为以下几类:生成命令、清单命令、包命令和发布命令:

```
    # Build commands          
    cargo bench \[options\] [benchname] 
    cargo build [options]
    cargo check [options]
    cargo clean [options]
    cargo doc [options]
    cargo fetch [options]
    cargo fix [options]
    cargo run \[options\] [-- args]
    cargo rustc \[options\] [-- args]
    cargo rustdoc \[options\] [-- args]
    cargo test \[options\] [testname]

    # Analysis commands
    cargo clippy

    # Manifest commands
    cargo generate-lockfile [options]
    cargo locate-project [options]
    cargo metadata [options]
    cargo pkgid \[options\] [spec]
    cargo tree [options]
    cargo update [options]
    cargo vendor \[options\] [path]
    cargo verify-project [options]

    # Package commands
    cargo init \[options\] [path]
    cargo install [options]
    cargo new [options] path
    cargo search \[options\] [query...]
    cargo uninstall \[options\] [spec...]

    # Publishing commands
    cargo login \[options\] [token]
    cargo owner [options]
    cargo package [options]
    cargo publish [options]
    cargo yank [options] --vers version [crate]                  

```

关于货物命令的更多信息，查看官方 Rust 文档。

## 结论

没有货物，构建 Rust 应用程序是不可能的。此外，您需要 Cargo 命令来发布您的项目。简而言之，Cargo 为您的 Rust 项目编排了一个平滑的构建、编译和运行时。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我希望本指南能让你更好地理解什么是 Cargo，它是如何工作的，以及为什么它是任何 Rust 项目中如此重要的一部分。如果你想了解更多关于货物的信息，Rust 文档是一个很好的资源。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。