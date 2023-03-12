# web 开发和桌面应用程序的最佳 Rust 框架

> 原文：<https://blog.logrocket.com/the-best-rust-frameworks-to-check-out-in-2019/>

开发人员可以使用带有 [Rust](https://www.rust-lang.org/) 的框架来构建快速、高度可伸缩、内存高效的软件。无论您是在构建 CLI 工具、服务器或客户端的 web 应用、网络服务、嵌入式系统软件还是桌面软件，Rust 都足以满足您的所有编程需求。

框架通常是工具、助手和库的组合，提供了一种(快速、轻松地)构建、测试和运行应用程序的方法。它为构建软件奠定了基础。选择框架时要注意的关键方面是它的架构和特性(比如对定制的支持、灵活性、可扩展性、安全性、与其他库的兼容性等)。

本文旨在展示几种不同领域的 Rust 框架，突出它们的优点和缺点。最后，您应该能够对适合您需求的 Rust 框架做出明智的决定。

## rust web 开发框架

凭借 GitHub 上的 8k stars，Rocket 是最成熟、生产就绪、全栈的 web 框架之一。它的主要优点是,[不会像许多框架那样让你为了一个高效的环境而牺牲速度，反之亦然。它有助于编写快速、安全的 web 应用程序，并且不会牺牲灵活性、可用性或类型安全性。](https://medium.com/@lholznagel/comparing-nodejs-and-rust-http-frameworks-response-times-5738dfa1843d)

其他值得注意的酷事包括:

*   它支持 JSON 开箱即用，所以你不需要有另一个依赖
*   表单处理简单易行。它不会让错误的表单请求通过，因此您的代码不会中断。它让您可以完全控制应用程序的所有方面
*   它带有内置的模板支持
*   您可以轻松地查看、添加或删除加密或未加密的 cookies
*   它类型检查路由 URL
*   它使用内置的测试库轻松地在您的应用程序上运行单元测试
*   您可以按照自己的方式为开发、试运行和生产配置您的应用程序

我最喜欢的特性是不必担心样板代码。Rocket 大量使用 Rust 的代码生成工具来提供一个干净的 API，所以如果你想构建一个服务器渲染的 web app，Rocket 是一个很棒的工具。查看更多关于超级易懂的[文档](https://rocket.rs/v0.4/guide/)。

Actix 是一个服务器渲染的框架。该架构基于 Rust 非常强大的 actor 系统，并标榜自己是一个有趣的网络框架。根据这个[基准](https://www.techempower.com/benchmarks/#section=data-r18)，Actix 是最快的 web 框架。它非常适合用硬逻辑和组件编写服务。您还可以以异步方式使用这些服务。

它还提供了很多特性(比如日志、http/2 等。)开箱。根据您的偏好，它的样板代码可以帮助您快速入门，或者如果您正在编写一个简单的应用程序，它就显得有些多余。这是一个很棒的框架，有适当的文档。在此了解如何开始[。即使对完全的初学者来说，这也是容易做到的。](https://actix.rs/docs/)

Nickel 也是一个用于构建服务器呈现的 web 应用程序的 web 框架。对于用 Rust 编写的 web 应用程序来说，它是一个简单而轻量级的基础。它的 [API](https://docs.rs/nickel/0.11.0/nickel/) 受到了流行的 JavaScript express 框架的启发。

它对你应该如何写你的代码没有自己的看法，这意味着它没有一个模板或者给出具体的做事方法，并且有一个更渐进的学习曲线。Nickel 支持用 [mustache.rs](https://github.com/spullara/mustache.rs) 定义模板，你需要做的就是创建模板。

Nickel 可以很容易地将 JSON 数据直接映射到您的`struct`上，默认情况下，Nickel 用它的默认`ErrorHandler`捕捉所有错误，并尝试采取合理的行动。因此不需要编写自己的自定义错误处理程序。

路由可以像/fizzBuzz 一样简单，使用参数、通配符甚至双通配符。中间件是 Nickel 的扩展点。它与现有的中间件一起开箱即用，如果您愿意，您可以对其进行扩展。[入门指南](https://nickel-org.github.io/getting-started.html)可以马上帮你。

受 [Elm](https://elm-lang.org/) 和 [React](https://reactjs.org/) 的启发，Yew 是一个用 [WebAssembly](https://webassembly.org) 构建多线程 web 客户端应用的现代框架，通常被称为 WASM。它包含许多出色的功能，例如:

*   可重用组件架构
*   另一个很酷的功能是服务。它们允许跨组件重用相同的逻辑。这些服务是无状态的。它利用了代理的概念，代理也用于跨组件共享数据，并为应用程序提供整体状态
*   它使用 [WebWorkers API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API) 在单独的线程中生成代理，并使用一个附加到线程的本地调度程序来执行并发任务。这使得用 Rust 编写的浏览器中的高并发应用成为可能

一些缺点包括:

*   测试并不完全有益健康。是的，您可以测试单个服务，但是目前还没有办法测试组件或代理。在撰写本文时，集成和端到端测试都无法在 plain Rust 中完成
*   红豆杉似乎处于早期阶段，因此，没有一个适当的官方指导如何使用它

## 为桌面应用程序使用 Rust

与 JavaScript 的 [Electron](https://electronjs.org/) 非常相似，Azul 是一个免费的、功能性的、即时模式的 GUI 框架，用于快速开发用 Rust 编写的桌面应用程序，由 Mozilla WebRender 渲染引擎支持。它包含许多有用的功能:

*   跨平台 GUI 工具包(Windows、Linux、Mac)
*   即时模式 API，窗口小部件通过函数组合来组成
*   CSS 样式引擎，基于 flexbox 的布局
*   常见用户界面元素的内置控件
*   通过功能组合创建自定义小部件
*   SVG 渲染引擎，2D 绘图助手
*   OpenGL 集成
*   异步 I/O 帮助函数
*   XML 序列化和热重装，内置 XML-to-Rust 编译器
*   单一部署二进制文件、最小二进制文件大小(大约 5MB 全包)、CPU(0–4%)和 RAM 使用量(总共约 50MB)
*   硬件加速的 OpenGL 渲染(0.5-4 毫秒)
*   react-like DOM differing 用于增量布局和样式更改

**缺点包括:**

*   它没有一个广泛的社区，仍然处于早期阶段。尽管如此，它已经可以生产了。

这里 开始使用[。](https://github.com/maps4print/azul/wiki)

Conrod 是一个用于 Rust 的可移植的 2D 图形用户界面库。它提供了一个即时模式的 API，该 API 包装了保留模式的小部件状态图，允许暴露一个简单、健壮和反应式的界面，同时接近传统的保留 GUI 框架的性能。

中间模式指的是用户界面 API 的一种风格，其中 GUI 部件使用更新或绘制循环中的函数进行实例化，这与更传统的“保留模式”截然不同，后者在设置阶段构建部件类型。

功能包括:

*   一种易于使用的风格，适用于高度动态的界面和那些需要频繁同步应用程序状态的界面
*   高性能
*   然而，它需要大量的稳定性，在我看来，它还不是一个完全健壮的框架，但由于它的即时模式 API 方法，它显示了很大的前景

从这里开始[。](https://docs.rs/conrod/0.61.1/conrod/guide/chapter_2/index.html)

基于非常成熟的跨平台用户界面库 [Qt](https://wiki.qt.io/Main) 。大多数语言都有包装器，当然 Rust 也不例外。

它拥有 Qt 的所有巨大优势，当然还有 rust 的标志性特征——小二进制文件。这些功能包括:

*   开发允许通过简单的重新编译将应用程序移植到多个平台
*   它提高了开发效率，缩短了上市时间，使应用程序面向未来
*   使用 Qt 进行开发简化了技术策略，并最终降低了成本
*   通过在所有屏幕和平台上部署一个代码来节省时间
*   Rust 中的编码给了开发人员更大的控制力，以及使用大量现有库的可能性
*   代码被编译成全速运行的本机二进制文件(不需要使用虚拟机)
*   它的跨平台软件开发使得为所有用户创建直观的体验变得容易，无论使用什么系统

**缺点包括:**

*   它固有地遭受了 Qt 带来的同样的缺点，比如 QObject 和 QWidget 不是线程安全的
*   用户体验(UX)远非顺畅。在桌面平台上，更容易开发原生用户体验，因为有更多的通用模式和小部件，同时，移动 UX 是不同的，缺乏平台特定的东西
*   当默认使用 QT GUI 组件时，您不会得到 iOS 和 Android 的理想外观和感觉

Qt 是一个经过验证的跨平台应用程序开发框架，Rust 的加入使这个框架变得强大，可能是最成熟的 GUI Rust 框架。由于 Rust Qt 绑定生成器在 Rust 中被重写，现在可以作为一个货箱使用，所以您不必担心庞大的构建和接口复杂性。这使得普通情况下的开发变得非常简单。

## 结论

作为一种低级语言，Rust 非常适合用传统的方式制作用户界面，使用本地 API。可悲的是，在当今世界，通常涉及到支持许多平台，使用原生 API 对许多人来说是没有吸引力的选择。

然而，Rust 的表现力和高级抽象使它成为构建错综复杂的用户界面的理想选择。不幸的是，对于什么是最好的抽象还没有达成共识，特别是对于网络的独特性。使用适合您的用例的方法，并根据需要进行迭代。

还有更多基于 Rust 的 web 和 GUI 框架正处于起步阶段，比如 [core foundation](https://docs.rs/core-foundation/0.6.4/core_foundation/) 、[德鲁伊](https://github.com/xi-editor/druid)、[冰封](https://docs.rs/iced/0.1.0-alpha/iced/)、 [relm](https://docs.rs/relm/0.17.0/relm/) 和 [web renderer](https://doc.servo.org/webrender/index.html) 等等。你可以查看它们，贡献或者甚至构建你自己的框架。

关于 Rust 框架在 2020 年及以后的评估，请查看 Rust web 框架的当前状态。

快乐编码😀。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。