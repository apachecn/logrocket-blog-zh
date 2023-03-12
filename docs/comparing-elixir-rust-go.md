# 仙丹与 Rust 和 Go 的比较

> 原文：<https://blog.logrocket.com/comparing-elixir-rust-go/>

编程从来都是为了快速高效地完成任务。新开发人员通常开始使用 JavaScript 或 Python 等语言编写代码，这些语言具有构建大规模应用程序的出色特性。

但是，随着应用程序的增长，可伸缩性、并发性和容错变得与应用程序功能一样重要。任何拥有合理数量客户的公司都会有不止一个语言堆栈，因为有些语言比其他语言更好地处理特定的任务。这就是像 Elixir、Rust 和 Go 这样的语言出现的原因。

在系统编程语言之战中，Elixir、Rust 和 Go 脱颖而出，成为开发者的三大选择。在本文中，我们将研究 Elixir，并简要比较它与 Go 和 Rust 的性能。

*向前跳转:*

## 什么是二郎？

在深入研究 Elixir 之前，让我们快速了解一下 Erlang 是什么。

[Erlang](https://www.erlang.org/) 诞生于 20 世纪 80 年代，旨在解决可靠的电话切换问题。它的名字来源于爱立信语言，因为它是在爱立信计算机科学实验室创建的，用于构建下一代电话交换机。

Erlang 提供了创建高度可伸缩的并发代码的特性，以及令人印象深刻的正常运行时间和容错能力。Erlang 的一个额外优势是 [BEAM 虚拟机](https://www.erlang.org/blog/a-brief-beam-primer/)，这是一个将 Erlang 代码编译成字节码的快速编译器。

## 什么是仙丹？

[Elixir](https://elixir-lang.org/) 是构建在 Erlang 之上的函数式并发编程语言。像 Erlang 一样，Elixir 使用 BEAM VM 工作。这使得 Erlang VM 具有更高的可扩展性和生产率，同时保持与 Erlang 生态系统的兼容性。

创造者 [Jose Valim](https://github.com/josevalim) 将 Elixir 设计成一种高并发、低延迟的语言，能够处理大量数据。Elixir 还使用了 Ruby 的一些最好的特性，尤其是它的语法相似性。

灵药属于[消息传递](https://en.wikipedia.org/wiki/Message_passing)语言的范畴。与大多数直接调用函数来执行动作的语言不同，消息传递语言基于接收到的消息来确定它们的动作。例如，他们可以在收到订单时生成发票。

在[微服务架构](https://blog.logrocket.com/methods-for-microservice-communication/)的时代，像 Elixir 这样的语言提供了构建无缝协同工作的并发应用程序的工具。既然你明白了什么是仙丹，那就来看看仙丹能做什么，不能做什么。

## 使用长生不老药的好处

### 并发性和可伸缩性

并发性和可伸缩性是 Elixir 的核心理念。并发是指同时但独立地运行多个进程的能力。这是通过使用 Erlang VM 实现的。

Elixir 的并发模型基于 [Actors](https://elixirschool.com/en/lessons/intermediate/concurrency) 。在分布式体系结构中，参与者是纯粹通过消息传递与其他流程进行通信的流程。并发性与消息传递架构相结合，使得 Elixir 成为构建分布式应用程序的绝佳选择。

可伸缩性也内置于 Elixir 中。由于 Elixir 中构建的应用程序是一组独立的独立进程，它们之间的所有信息都通过消息传递。这使得 Elixir 应用程序高度可伸缩。如果一个函数需要更多的资源，你需要做的就是复制现有的函数，并将它们添加到应用程序中。

### 容错

长生不老药计划理论上可以永远运行。作为开发人员，您理解编写和维护代码的困难。维护一个有许多移动部件的系统更具挑战性，尤其是在分布式架构中。

Elixir 使用[管理器和应用程序模型](https://elixir-lang.org/getting-started/mix-otp/supervisor-and-application.html)来构建容错系统。在 Elixir 中，您将拥有一个启动应用程序中其他进程的顶级管理进程。这些子流程可以有更多的主管和他们自己的子流程。这种递归结构也称为监督树。

任何进程的管理者都可以重新启动所有失败的子进程。此外，如果主管愿意，他可以失败，将决策权委托给其上一级主管。这种进程的监督和应用模型是 Elixir 容错背后的核心原因。

### 更容易的学习曲线

与 Rust 和 Go 等语言相比，Elixir 的学习曲线更容易。这是因为 Elixir 在语法上与 Ruby 相似。Elixir 还被设计成更容易采用，不像 Erlang，它有一个陡峭的学习曲线。

更容易的学习曲线有助于建立灵丹妙药团队和培训内部人员。这也可以从不断增长的社区对 Elixir 的支持中看出来，尽管它是一种相对较新的语言。

## 长生不老药

### 动态类型化

虽然 [Rust](https://blog.logrocket.com/tag/rust/) 和 [Go](https://blog.logrocket.com/tag/go/) 是静态类型的语言，但 Elixir 是动态类型的。区别很简单:在 Rust and Go 中执行程序之前，必须先编译程序。这导致编译时间比 Elixir 慢。然而，这意味着 Rust 和 Go 在速度和内存消耗方面可以胜过 Elixir。

### 函数式语言

Elixir 是一种纯函数式编程语言。Go 和 Rust 提供了面向对象，使得构建复杂的类更加容易。然而，这是有意的。Elixir 用于构建相互通信的分布式进程的集合，因此类和对象对于像 Elixir 这样的语言来说是多余的。

Elixir 是一种相对较新的编程语言，因此寻找开发人员和支持可能是一个挑战。开发团队通常为特定的用例选择像 Elixir 这样的语言，所以 Elixir 社区仍然需要努力将其列入最常用的编程语言列表。

## 谁用长生不老药？

*   Adobe:使用 Elixir 构建客户端/云应用程序，实现无缝协作的摄影工作流程
*   Discord:使用 Elixir 进行大规模消息传递，管理数百万并发用户
*   摩托罗拉:将 Erlang 和 Elixir 用于需要可靠和容错的关键任务通信系统

## 灵丹妙药与围棋的比较:速度、规模和安全性

Go 是一种静态类型和编译的编程语言。Go 作为一种系统编程语言越来越受欢迎，并且是少数几种支持编写 AWS lambda 函数的语言之一:

*   速度:因为 Go 是一种编译语言，所以它在速度方面提供了更好的性能。编译后的程序在速度上总是更好，但是在开发时间上有所折衷
*   鳞片:如果你主要关心的是鳞片，仙丹是一个更好的选择。Go 提供了可伸缩性，但在从头开始扩展应用程序方面，Elixir 遥遥领先
*   安全性:Go 提供了内存安全特性，但是 Elixir 具有更好的内存安全性，因为它只能与消息传递架构一起工作。由于 Elixir 进程只能共享内存，不能直接相互调用，因此与 Go 相比，Elixir 在安全性方面得分更高

## 仙丹与铁锈的比较:速度、规模和安全性

Rust 是开发人员社区中最受欢迎的编程语言之一。Rust 的主要卖点是速度和内存安全。如果你想了解更多关于 Rust 的内容，可以看看我关于 Rust 迭代器和闭包的文章:

*   速度:由于 Rust 也是一种像 Go 一样的编译语言，它在速度方面提供了更好的性能
*   伸缩性:由于其内置的并发性和容错设计，Elixir 更容易伸缩。监督者模型还使 Elixir 程序更容易构建分布式应用程序。Rust 也提供并发性，但是 Elixir 在分布式架构中表现更好
*   安全性:保证内存安全是开发者选择 Rust 的主要原因。由于变量和内存受到严格控制，Rust 提供了比 Elixir 应用程序更好的安全性

## 药剂、铁锈和围棋性能的基准

让我们看看 Elixir、Go 和 Rust 之间的一些性能指标评测。我们将通过运行两个程序来看看这些语言的性能——一个`"Hello, World!"`函数和一个[谱归一化函数](https://paperswithcode.com/method/spectral-normalization)。

该函数在屏幕上打印文本`"Hello, world!"`，而谱范数函数计算矩阵的最大奇异值。

### `Hello, World!` : [仙丹](https://github.com/hanabi1224/Programming-Language-Benchmarks/blob/main/bench/algorithm/helloworld/1.ex)vs[围棋](https://github.com/hanabi1224/Programming-Language-Benchmarks/blob/main/bench/algorithm/helloworld/1.go)vs[铁锈](https://github.com/hanabi1224/Programming-Language-Benchmarks/blob/main/bench/algorithm/helloworld/1.rs)

```
  +-----------+--------------------+-----------------+------------+
  |   Metric  |       Elixir       |       Go        |   Rust     |
  +-----------+--------------------+-----------------+------------+
  |  Time     | 212 ms             | 1.7 ms          | 1.6 ms     |
  |  Memory   | 45.5 MB            | 2.0 MB          | 0.6 MB     |
  +-----------+--------------------+-----------------+------------+

```

### 谱范:[仙丹](https://github.com/hanabi1224/Programming-Language-Benchmarks/blob/main/bench/algorithm/spectral-norm/1.ex)vs[围棋](https://github.com/hanabi1224/Programming-Language-Benchmarks/blob/main/bench/algorithm/spectral-norm/1.go)vs[铁锈](https://github.com/hanabi1224/Programming-Language-Benchmarks/blob/main/bench/algorithm/spectral-norm/8-m.rs)

```
  +-----------+--------------------+-----------------+------------+
  |   Metric  |       Elixir       |       Go        |   Rust     |
  +-----------+--------------------+-----------------+------------+
  |  Time     | timeout            | 4892 ms         | 1058 ms    |
  |  Memory   | 0.0 MB             | 5.5 MB          | 2.6 MB     |
  +-----------+--------------------+-----------------+------------+

```

如你所见，Rust 远远胜过 Go 和 Elixir。Elixir 在运行计算密集型操作(如 spectral norm)时也会超时，因此对于复杂算法(如神经网络)来说，它不是一个很好的选择。

## 摘要

尽管是一种相对较新的编程语言，但 Elixir 有许多实际应用。分布式系统，包括[区块链](https://blog.logrocket.com/tag/blockchain/)和物联网服务，可以利用 Elixir 构建可靠的容错架构。随着越来越多的社区支持和更容易的学习曲线，Elixir 是一种值得花时间学习的优秀语言。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。