# Fleet:改进 Rust 的 Cargo - LogRocket 博客的构建工具

> 原文：<https://blog.logrocket.com/introducing-fleet-improving-rusts-cargo/>

根据 [2021 Rust 调查](https://blog.rust-lang.org/2022/02/15/Rust-Survey-2021.html#challenges-ahead)，Rust 的长编译时间仍然是开发者的一大担忧，也是需要进一步改进的领域。尤其是当涉及到大型项目或有许多依赖项的箱子时，Rust 对运行时性能的关注超过了对编译时性能的关注，这变得相当糟糕，甚至在调试版本中也是如此。这对开发人员的体验有负面影响，这也是一些开发人员仍然不愿意尝试 Rust 的原因。

无论如何，在可预见的未来，Rust 的构建时间将继续变慢，尤其是对于较大的项目。虽然可以做一些调整来改善这种情况，但是设置它们并跟上新的发展是很麻烦的，比如改进构建时间的标志和配置选项。

在本文中，我们将探索 [Fleet](https://fleet.rs/) ，这是一个构建工具，本质上是一个用于缩短 Rust 构建时间的单一工具解决方案，既适用于本地开发，也适用于 CI/CD 管道。

## 舰队入门？

Fleet 的重点是易用性。Fleet 的目标不一定是重新发明轮子，彻底检查或重组 Rust 构建的工作方式，而是包装现有的构建工具，将优化整合到一个可配置的直观工具中，以加快构建速度。它可以在 Linux、Windows 和 Mac OS 上运行。

不幸的是，在撰写本文时，[舰队仍处于测试阶段](https://github.com/dimensionhq/fleet)，并且只支持夜间`rustc`。然而，它正在积极开发中，将它转移到`stable`工具链是即将到来的改进的一部分。也就是说，如果你不喜欢马上使用 Fleet，或者你当前的项目设置与 Fleet 不兼容，那么有一些好消息。您可以手动进行大多数优化。在本文的后面，我们将快速浏览它们，分享一些资源，您可以从中了解更多。

首先，让我们从学习如何安装 Fleet 并在项目中使用它开始。

## 安装和使用车队

要安装 Fleet，您需要在您的机器上安装 Rust。完成后，只需打开您的终端并执行相应的安装脚本:

对于 Linux:

```
curl -L get.fleet.rs | sh

```

对于 Windows:

```
iwr -useb windows.fleet.rs | iex

```

完成后，您可以使用四个命令行参数之一来设置 Fleet:

*   `-h / --help`:打印帮助信息
*   `-V, / --version`:打印版本信息
*   `build`:建立车队项目
*   运行一个车队项目

你可以在舰队[文档](https://fleet.rs/docs/commands/run)中查看`run`和`build`的附加可选参数。这些[有点类似于 Cargo，但它不是 1:1 的替代品](https://blog.logrocket.com/demystifying-cargo-in-rust/)，所以如果你在项目方面有特殊需求，请务必查看不同的配置选项。

如果您计划在使用和不使用 Fleet 的情况下对构建时间进行基准测试，请确保运行干净的构建，并牢记缓存和预加载。虽然 Fleet 声称在某些构建上比 Cargo 快五倍，但就编译速度而言，您的项目实际性能提升的大小将取决于许多不同的因素，包括您试图编译的代码及其依赖关系以及您的硬件，SSD 与 WSL (Windows System for Linux)。

在任何情况下，如果您目前觉得您的项目构建非常缓慢，安装 Fleet 并尝试看看它是否能改善这种情况。就设置而言，Fleet 根本不需要时间。

除了本地开发改进之外，Fleet 的另一个重要目标是改进 CI/CD 管道。如果你有兴趣为你的自动化构建尝试 Fleet，一定要查看他们关于用 GitHub for Linux 和 Windows 设置它的文档。

## 最佳化

在撰写本文时，Fleet 主要关注四种不同的优化:Ramdisk、通过设置优化构建、Sccache 和一个自定义链接器。你可以在这个 [GitHub 票](https://github.com/dimensionhq/fleet/issues/34)中找到一个简短的描述，但是这个列表很可能会随着时间的推移而改变，特别是当舰队移动到`stable`并得到进一步发展的时候。

让我们逐一检查不同的优化，看看它们实际上做了什么。下面的内容不是详尽的描述，而是对不同技术的粗略概述，以及一些关于如何使用它们的技巧和资源。在本文的最后，还有一篇精彩文章的链接，描述了如何在 Rust 中手动提高编译时间。

### 随机盘

Ramdisk，或 Ramdrive，本质上只是一块 RAM，它被当作硬盘来使用，以提高速度，并在某些情况下减轻硬盘的压力。

这种优化的想法是将您的构建的`/target`文件夹放在一个 Ramdisk 上以加速构建。如果你已经有一个固态硬盘，这只会略微提高构建时间。但是，如果您使用 WSL(Linux 的 Windows 子系统)或非 SSD 硬盘，Ramdisk 有可能大幅提高性能。

有很多关于如何为不同的操作系统创建内存磁盘的教程，但是作为起点，你可以使用下面两篇关于 Mac OS 和 Linux 的文章。

### 构建配置

Fleet 通过使用编译器选项和标志来操纵构建配置，以提高性能。

这方面的一个例子是增加`codegen-units`。当编译代码时，这实质上增加了 LLVM 中的并行性，但这是以潜在的运行时性能为代价的。

对于调试版本来说，这通常不是问题，因为开发人员的经验和更快的版本很重要，但是对于发布版本来说肯定是这样。你可以在文档中了解更多关于[这个标志的信息。](https://doc.rust-lang.org/stable/rustc/codegen-options/index.html#codegen-units)

手动设置`codegen-units`相当简单，只需将其添加到您的`~/.cargo/config.toml`中的`rustflags`中即可:

```
[target.x86_64-unknown-linux-gnu]
rustflags = ["-C", "codegen-units=256"]

```

然而，如上所述，对于发布版本，您应该明确地将其覆盖回`1`。

另一个选择是降低调试版本的优化级别。虽然这意味着运行时性能会受到影响，但编译器要做的工作更少，这通常是您在代码库上迭代时想要的。然而，这可能有例外；您可以在文档中阅读更多关于[优化级别的信息。](https://doc.rust-lang.org/stable/rustc/codegen-options/index.html#opt-level)

要将优化级别设置为最低设置，请将以下代码添加到您的`~/.cargo/config.toml`文件中:

```
[target.x86_64-unknown-linux-gnu]
rustflags = ["-C", "opt-level=0"]

```

同样，请确保只为调试版本而不是发布版本设置此选项。您不希望在生产二进制文件中有完全未优化的代码。

如前所述，对于较低的优化级别，您可以尝试添加`share-generics`标志，这样可以在项目中的多个箱子之间共享泛型，从而潜在地避免编译器重复工作。

例如，对于 Linux，您可以将这个添加到您的`~/.cargo/config.toml`:

```
[target.x86_64-unknown-linux-gnu]
rustflags = ["-Z", "share-generics=y"]

```

### Sccache

下一个优化是使用 Mozilla 的 [sccache](https://github.com/mozilla/sccache) 。Sccache 是一个编译器缓存工具，这意味着它试图缓存编译结果，例如，跨项目或板条箱，将它们存储在磁盘上，本地或云存储。

如果您有几个项目，这些项目有很多，有时是很大的依赖项，这就特别有用。缓存编译这些不同项目的结果可以防止编译器重复工作。

特别是在 CI/CD 管道的环境中，构建通常在没有任何本地现有缓存的新产生的实例或容器的环境中执行，云支持的 sccache 可以大大缩短构建时间。每次构建运行时，缓存都会更新，并可由后续构建重用。

Fleet 无缝地将 sccache 引入到它的构建中，但是手动完成这项工作也不是特别困难。只需遵循 sccache 的[安装和使用](https://github.com/mozilla/sccache#usage)说明。

### 自定义链接器

最后，Fleet 还配置并使用定制的链接器来提高构建性能。特别是对于有深度依赖树的大型项目，编译器会花大量时间进行链接。在这些情况下，使用尽可能最快的链接器可以大大缩短编译时间。

下面的列表包括适用于每个操作系统的正确链接器:

*   Linux: `clang + lld`然而，Linux 可能很快就会使用[模具](https://github.com/rui314/mold)
*   视窗:`rust-lld.exe`
*   苹果操作系统:`zld`

配置自定义链接器并不特别困难。本质上，它归结为安装链接器，然后配置 Cargo 来使用它。例如，在 Mac OS 上使用`zld`可以通过向您的`~/.cargo/config`添加以下配置来实现:

```
[target.x86_64-apple-darwin]
rustflags = ["-C", "link-arg=-fuse-ld=<path to zld>"]

```

在 Linux 上，`lld`或`mold`是 Rust 的最佳选择。由于[许可证问题](https://github.com/dimensionhq/fleet/issues/13)，Fleet 还没有使用`mold`，但是您可以通过简单的按照[模具文档](https://github.com/rui314/mold#how-to-use)中的步骤在您的本地版本中使用它。

在这个简短的概述之后，如果你现在不愿意使用 Fleet，另一个改善构建时间的极好资源是 Matthias Endler 关于这个主题的[博客文章](https://endler.dev/2020/rust-compile-times/)。

## 结论

Fleet 有很大的潜力，特别是对于不喜欢忙于构建管道或构建过程的开发人员。它为构建速度提供了一个强大的多平台和多环境优化的一体化包，所以如果您正在为构建时间而奋斗，那么它非常值得一试。

除此之外，我们还谈到了 Fleet 在后台执行的一些优化，以及如果您愿意花一点时间了解它们的作用以及如何在您的设置中引入它们，它们将如何帮助您减轻编译速度的痛苦。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

也就是说，通常，缓慢构建时间背后的原因是一个项目依赖于非常多或巨大的板条箱。

以极简主义的心态管理好你的依赖关系，意味着只引入你所需要的最小版本，或者从头开始构建所需的功能，而不是添加一个现有的机箱，这不仅会使你的构建时间很短，还会降低代码的复杂性，增加代码的可维护性。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。