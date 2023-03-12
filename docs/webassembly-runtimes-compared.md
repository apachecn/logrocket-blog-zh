# WebAssembly 运行时比较- LogRocket 博客

> 原文：<https://blog.logrocket.com/webassembly-runtimes-compared/>

## 导言和背景

随着最近 Wasmer 1.0 的发布，让我们全面看看 WebAssembly 及其流行的运行时，包括令人兴奋的 WASI 接口。

要跳转到比较部分，点击[这里](#comparison4interesting)。

### ISAs 和 WebAssembly

WebAssembly 可在服务器/客户端 web 应用程序中实现，强调可移植性和可预测性。与 [RISC-V](https://www.howtogeek.com/691224/an-open-alternative-to-intel-and-arm-what-is-risc-v/) 一起，它是一种相对较新的指令集架构(ISA ),在过去十年中开发，能够与主机语言并行运行。

isa 本身是代表计算机的抽象计算机科学模型。ISAs 在现实世界中的实施被称为实现—例如，中央处理器(CPU)。ISA 还决定了机器代码在包含该 ISA 的实现上的行为。

虽然 RISC-V 将为硬件/可扩展软件架构(例如，开源处理器)中的过程创新铺平道路，但 WebAssembly(通常简称为 Wasm)因提供编译而脱颖而出，尤其是针对浏览器。

简而言之，WebAssembly 可以与支持 web 浏览器的 JavaScript 引擎并行运行。可以对它编译其他高级语言— [Rust](https://blog.logrocket.com/getting-started-with-webassembly-and-rust/) ，C/C++等。—为获得更好的性能，使用其低级二进制格式，将小文件大小与卓越的速度结合起来。您没有编写 WebAssembly 你只是用它来编译。

## Wasm 运行时库概述:简史

### 它始于 Java

先说 Java。计算的典型梦想是兼容性:90 年代梦想了一个 Java 世界，在那里你可以“编写一次，在任何地方运行”(world 尽管这不是它最初的打算用途。这适用于简单的应用程序，但对于试图运行该代码的不同操作系统、版本和设备来说，这就成了一个问题。

WORA 变成了沃德(“编写一次，到处调试”)，还有其他问题，比如需要目标使用 JVM，然后必须用 Java 或另一种 JVM 语言进行额外的编程。JavaScript 离梦想更近了一步，成为了网络的通用语言。

### WebAssembly 进入舞台

这是 WebAssembly 的基本环境。这种紧凑的形式是为快速执行和小型二进制文件而设计的。它像 JavaScript 一样运行在沙盒中，在在线运行代码时提供保护，不会给系统带来额外的风险，并且与高级编程语言兼容。

在后面的部分中，我们将讨论 [WASI 接口](https://wasi.dev/)，它正在努力标准化基本操作，以使 WebAssembly 更接近于在任何地方运行曾经制作的代码的梦想。对于在浏览器外运行以下内容，WASI/Wasm 是比 JavaScript 更好的解决方案:

目前，在哪里运行的问题仍然是开发人员需要处理的一个重要因素，更不用说通过云和使用 [NoSQL 数据库](https://hostingdata.co.uk/nosql-database/)的内部管理非结构化混合数据流了。

### 技术概述:运行时库

当在浏览器中运行本机代码时，每次编译器将源代码翻译成相关的目标语言，都会导致程序代码大量增加。解决方法是使用具有编译器特定辅助功能的编译器。

![WebAssembly Wasm Compiler Auxillary Function](img/965264e70eecb722f861f99e0e38cd9e.png)

WebAssembly 还大大减少了浏览器的处理时间。由于这篇文章广泛地涉及了运行时，我们不会深入到运行时结构和表示的具体细节，如存储和堆栈，也不会深入到包含 WebAssembly 概念机制的其他结构元素，如值和模块实例及其辅助语法。

## 比较:4 个有趣的独立 Wasm 运行时

### 垫圈

[Wasmer](https://blog.logrocket.com/how-to-debug-wasm-and-achieve-a-reliable-stack-trace/) 的 1.0 版本于 2021 年 1 月刚刚发布，API 稳定，使用更方便。它提供了非常轻量级的容器，可以从任何地方执行，无论是云、桌面还是 IOT 设备。Wasmer 可嵌入到任何编程语言中，充当您嵌入到任何语言中的库；真正做到随处使用 WebAssembly。

*关键 Wasmer 特性:*

*   **可插拔性**:兼容各种编译框架，满足您的任何需求(例如 Cranelift)
*   **速度/安全**:能够在完全沙盒化的设置中以接近*本地速度*运行 Wasm。
*   **通用性**:可在任何平台(Windows、Linux 等)和芯片组上工作
*   **支持**:符合 WebAssembly 测试套件标准，拥有大量开发人员和贡献者社区支持

用一个命令安装 Wasmer。

### Wasmtime

Wasmtime 是一个高效、紧凑的解决方案，用于处理最新的 WASI/WebAssembly 创新。如果你使用的是以下语言，embed Wastime(也可以由字节码联盟托管):Rust，Python，C，.网，去吧。在联盟之外，您可以找到 Java、Perl、Targeting、Zig 和 Ruby 的嵌入 API。

*键 Wasmtime 特性:*

*   **紧凑**:要求不高的独立运行时，可以随着需求的增长而扩展。可以与小型芯片一起工作，也可以与大型服务器一起使用。几乎可嵌入任何应用
*   **容易修改**:提前调整 Wasmtime 进行预编译，使用光束生成光速代码，或者用于运行时解释。为您需要 Wasm 完成的任何事情进行配置
*   **极速**:兼容 Cranelift 执行高分辨率运行时机器编码
*   **兼容 WASI 的**:支持 API 的刷新，允许你通过 WASI 接口与主机一起实现
*   **支持**:符合 WebAssembly 测试套件标准，拥有大量开发人员和贡献者社区支持

[Wasmtime 安装/嵌入指南](https://wasmtime.dev/)。

### 朗讯

Lucet 于 2019 年 3 月 28 日[宣布](https://www.fastly.com/blog/announcing-lucet-fastly-native-webassembly-compiler-runtime)。这是一个不太为人所知的编译器/运行时，是 WebAssembly 自带的，也是希望执行应用程序中包含的潜在危险 Wasm 程序的开发人员的一个选择。

Bytecode Alliance 开源社区项目，您可以用它来运行不受信任的代码，无论是基础设施、设备还是操作系统——这是从 web 浏览器中使用的已有示例中获得的功能。

【Lucet 的主要特性:

*   快速专门化:一个开源项目，以接近本地的速度运行不可信的程序
*   **WASI 友好**:在浏览器外使用 WebAssembly。Lucet 在 Fastly edge 云上支持 WebAssembly 系统接口
*   **支持多种语言**:这包括 Typescript、Rust、C/C++，还有更多正在开发中
*   **大幅降低运行时内存占用**:任何快速协调的请求都可以由一个 WebAssembly 实例执行，该实例代表每个进程每秒数千个请求中的所有请求，没有额外的 [web 应用安全风险](https://www.clouddefense.ai/blog/web-application-security)
*   **提前(AOT)编译支持**:配置 AOT 编译以简化运行时开销和设计

他们的汇编指南需要更新。

### WebAssembly 微运行时(WAMR)

我们将讨论的最后一个独立运行时是 WebAssembly Micro Runtime (WAMR)。它占地面积很小，由三个组件组成:

1.  一个所谓的 iwasm VM 核心，它提供实时(JIT)和 AOT 编译，以及 web 汇编解释
2.  一个应用程序框架，允许构建多个 Wasm 应用程序，这些应用程序可以在设备上运行并用于物联网目的
3.  从云或主机环境进行动态/远程应用管理

*关键 WAMR (iwasm)特性:*

*   **快速**:以接近本地的速度运行(AOT)
*   **符合性**:符合 [W3C WASM](https://www.w3.org/TR/wasm-core-1/) MVP 的标准
*   **占用空间小**:二进制大小只有 85K(解释器)和 50K (AOT)，需要的内存资源很少

除了解决 bug 问题之外，这里的社区支持/新闻并不多。

## WASI:一个目光锐利的时代优势

### 浏览，还是不浏览

顾名思义，WebAssembly 旨在通过 web 在浏览器上运行。但是由于 WASI，它有潜力成为一个巨大的非网络未来。这个系统接口允许在网站内外运行 WebAssembly。

当使用 web 生态系统时，WebAssembly 能够与[现代浏览器 API](https://blog.logrocket.com/modern-browser-apis-to-use-today/)一起工作，保持与其标准安全模型的兼容性，并保持 web 的可移植性，同时保持适应未来发展的开发和设计。这是一个没有安全性权衡的高级系统，尤其是与 JavaScript 相比。

对于使用 WASI 在 web 之外运行 Wasm，独立的 Wasm 运行时可以充当解释器/“主机”在非 web 环境中使用它的承诺(有或没有 Node.js 这样的 JavaScript VM)从使用基本外壳的测试目的延伸到物联网设备、移动/桌面应用、数据中心服务器的商业应用，或者作为更大程序中的可执行文件。

### 什么是 WASI，为什么它如此令人兴奋？

WASI 代表 WebAssembly 系统接口，即 Wasm 的模块化系统接口。让我们从更高的层面来探索这一点，这样你就能了解为什么 WASI 如此令人兴奋。以下是 WASI 可能展现的几种方式(假设 WASI 的高层次目标得以实现):

#### 1.)跨平台应用/游戏

这里的梦想是从单个文件中获得跨平台游戏和应用，同时利用季后赛的 T2 力量。这背后的机制是:一个能够在任何包含 WebAssembly 运行时的平台上实现的二进制可执行文件——社区构建映射这些文件的库，WebAssembly 本身做基础工作。

#### 2.)重用源代码

这里的好处显而易见。您可以在各种平台上为您的应用架构重用源代码——例如，移动/桌面、服务器和客户端，甚至物联网。

#### 3.)能够执行 WASI 友好(Wasm)应用的单一运行时

这极大地减少了编译需求。不需要将不同的语言和它们独立的运行时缝合在一起——一个运行时能够实现每一种语言！

#### 4.)将许多目标组合成一个目标

这实际上是容器化——将许多应用程序及其依赖项编译成一个或几个 Wasm 文件。一切都以 Wasm 为主导，提高了可用性，减少了对挑剔容器的需求。

尽管如此，WASI 仍在发展中，所有这些例子都是高层次的概念化。

## 包扎

总结运行时选项:

*   Wasmer 以超快的速度对每一种编程语言都有最好的全面支持兼容性
*   Wasmtime 非常快，非常紧凑，具有良好的可配置性，但支持的语言较少
*   Lucet 是在大型应用程序中运行不受信任的 WebAssembly 程序的专用解决方案
*   WAMR 跑步时足迹很小

> **注意**，此列表并不详尽；有许多[令人敬畏的 WebAssembly 运行时](https://github.com/appcypher/awesome-wasm-runtimes)。

就这样——我们在本指南中涵盖了十年的时间跨度。我充实了 WebAssembly 最流行(也是最有前途)的运行时的一些关键特性:相似性和差异，从[改进浏览器渲染](https://blog.logrocket.com/how-browser-rendering-works-behind-the-scenes-6782b0e8fb10/)到非 web 可能性。我把这个放在 WASI 的总体视角下，因为 WASI 是基于最大的可移植性。

包含 WebAssembly 的程序将拥有这个额外的系统接口，作为构建其他程序的基础。这接受了真正通用接口的挑战，这是 Java 首先着手实现的，并与向数据科学的转变携手并进，以优化业务。

随着极端普遍性变得更加可能，安全将成为一个主要问题——在这个世界里，WASI 程序可以从任何来源执行。需要开发跨行业联合开发的新安全系统来满足这种需求，Lucet 运行时就是这样一个例子。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)