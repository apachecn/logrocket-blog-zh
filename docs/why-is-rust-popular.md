# Rust 为什么这么受欢迎？- LogRocket 博客

> 原文：<https://blog.logrocket.com/why-is-rust-popular/>

根据 2020 年的 StackOverflow 调查， Rust 是 2020 年最受欢迎的编程语言，超过 86%的人愿意再次使用它。

这不是巧合，因为 [Rust](https://www.rust-lang.org/) 不仅为开发人员提供了性能和安全性的特殊组合，还为初级开发人员提供了更快交付代码的工具。

Rust 于 2015 年正式发布，为了成为今天这样受人喜爱的语言，它经历了许多变化和更新。在这篇文章中，我们将讨论什么是 Rust，它的好处，以及在劳动力和辅助项目中的用例。

## Rust:它是什么，为什么使用它？

Rust 是一种静态类型的、多范例编程语言，专注于安全性和性能。Rust 的设计考虑了安全、速度和效率。

它提供了零成本的抽象、泛型、功能特性，并且对于许多开发人员来说，这些解决了其他低级语言的大多数问题，比如内存错误和构建并发程序。

Rust 已经被用于构建许多伟大的网站和产品，如 [1Password](https://1password.com/) 、 [Figma](https://blog.figma.com/rust-in-production-at-figma-e10a0ec31929) 、 [NPM](http://www.npmjs.com/) 、 [Coursera](https://medium.com/coursera-engineering/rust-docker-in-production-coursera-f7841d88e6ed) 、 [Mozilla](https://www.mozilla.org/en-US/) 、 [Atlassian](https://www.atlassian.com/) 、 [Dropbox](http://www.wired.com/2016/03/epic-story-dropboxs-exodus-amazon-cloud-empire/) 等等。

想看更多用 Rust 创造的令人惊叹的产品，你可以查看 Rust 生产用户的链接[。](https://www.rust-lang.org/production/users)

Rust 经常被开发人员引用为系统编程的首选语言，因为它结合了一流的速度和非常低的资源使用量，同时还提供了标准服务器语言的安全性。Rust 解决了与 C/C++相关的问题，比如垃圾收集和安全性。

现在来说说 Rust 为什么会这么受欢迎。

## 生锈的好处

### Rust 具有零成本的抽象

Rust 最好的特性之一是零成本抽象，这意味着你不必为你不使用的特性付费，所以无论你使用抽象还是“手动”实现，都要考虑速度、内存消耗等成本。，是一样的。

通过零成本抽象、编译时内存检查和垃圾收集，Rust 不在运行时检查和收集内存，而是在编译时跟踪代码的生命周期。这意味着不管你使用循环还是闭包，它们都编译成同一个程序集。

对于软件工程师来说，围绕系统编程的许多问题都是内存错误。他们的目标是设计一个在运行时具有高质量代码管理、可读性和高质量性能的项目。

为了实现这一点，工程师们试图限制代码优化和内存开销，这会导致间接内存访问，从而影响运行时的性能。Rust 用零成本的抽象解决了这个问题。

### Rust 内置了对并发的支持

并发性是指同一程序的多个副本同时运行，并且在执行期间，这些程序副本相互通信。

Rust 内置了对多线程的支持，凭借其所有权和借用规则，开发人员已经能够使用该语言编写更好的并发代码——这一切都是因为 Rust 防止了编译时的数据竞争。

### Rust 提供所有权和安全性

所有权通常被认为是铁锈最独特的特征。所有权使 Rust 能够在不需要垃圾收集器的情况下保证内存安全。说到低级编程语言，有两种类型的内存:堆栈和堆。

堆栈是一种内存类型，用于分配已知内存的动态内存，如整数和字符串。堆用于将来可能会改变的内存，这意味着大多数开发人员为堆中的项目预算空间，然后将它们签入堆栈中的空间。这种方法的一个问题是，有时可以将两个变量赋给堆中的相同数据。

为了解决这个问题，Rust 只允许一个变量拥有一段数据。一旦数据被赋给另一个变量，它要么被移动，要么被复制。

关于安全和安全性的话题，Rust 被认为是最安全第一的语言之一。与其他语言不同，Rust 在编译时分析程序的内存编译，通常可以防止因内存管理不善而出现的错误。当然，这使得垃圾收集在 Rust 应用程序中没有必要。

Rust 还允许开发人员通过添加单词 [unsafe 来自定义定制和实现。](https://doc.rust-lang.org/book/ch19-01-unsafe-rust.html)

### Rust 拥有全面且令人印象深刻的文档

Rust 经常被认为是一种学习曲线很陡的语言，通常不适合初学者。尽管如此，还是有很多开发者引用了 Rust 的完整文档作为采用这种语言的理由。

几个参加调查的人表示，这些文档极大地提高了他们对 Rust 的了解，他们被认为是不熟悉 Rust 的中级开发人员。

### Rust 有一个不断增长的社区

当编程语言和工具拥有一个用户和人们可以交互的社区时，它们就会成长。因为 Rust 很受欢迎，它现在在用户中有很强的社区意识。

因为它创建于 2010 年，并被开发人员和公司采用，所以它聚集了众多用户、专家和爱好者，使新手可以很容易地使用 playground 获得帮助并解决 Rust 的问题。

Rust 有一个[社区页面](https://www.rust-lang.org/community)，链接到 Discord 频道和一个所有 Rust 事物的论坛。这些团体在互联网上不断涌现，致力于与 Rust 合作，帮助 Rust 生态系统中的开发者。

## 什么时候应该使用 Rust？

由于 Rust 的安全特性，它在编译过程中可以用来控制某些类型的错误。但是它更适合用于性能很重要并且需要处理大量数据的应用程序。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

生锈适用于以下情况:

**构建嵌入式系统和区块链应用:** Rust 是许多区块链应用背后的语言，因为它能够在很少或没有计算负载的情况下处理网络中的多个请求。Rust 被用来构建区块链系统，如 [Polkadot](https://polkadot.network/) ，一个用于处理任务的区块链网络，以及 [Parity.io](https://www.parity.io/) ，一个以太坊的网络替代品。

Web 浏览器和引擎: Rust 是由设计师在 Mozilla 开发[伺服](https://servo.org/)浏览器引擎时改进的，据说甚至微软也在使用 Rust 重新设计其一些性能关键的组件。

**操作系统:**一些语言已经完全使用 Rust 编写，其中包括:[氧化还原](https://www.redox-os.org/)，一个基于微内核设计的 UNIX 风格的操作系统和[鞭炮](https://firecracker-microvm.github.io/)，一个开源虚拟化工具，用于为无服务器服务构建安全容器和虚拟机。

创建 web 项目: Rust 已经被用于开发引人注目的基于 web 的项目，而像 [rocket](https://rocket.rs/) 和 [gotham](https://gotham.rs/) 这样的工具已经被用于创建像 Dropbox 和 Coursera 这样的生产级 web 项目。

**构建命令行工具:** Rust 是构建命令行应用程序和工具的首选语言，因为它速度快、安全且支持跨平台。

## 结论

从它的可用性到对它所提供功能的日益增长的需求，我们可以看到为什么 Rust 的受欢迎程度正在上升，并且不会很快消退。

Rust 社区非常活跃，并不断发布更新和工具来改进 Rust。由于它在创建安全系统方面的能力和声誉，Rust 据说在未来几年仍然很受欢迎。

Rust 的安全性、速度和效率(即，它能够帮助开发人员更快地编写高性能代码)是它在未来几年将继续受到开发人员社区喜爱的原因。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。