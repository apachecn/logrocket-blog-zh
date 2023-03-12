# Rust web 框架的现状

> 原文：<https://blog.logrocket.com/current-state-rust-web-frameworks/>

***编者按*** *:本帖于 2022 年 1 月 15 日更新，将 Dioxus 添加到前端 web 框架的列表中，并更新我们对一些提到的框架的成熟度的考虑。*

## 介绍

Rust 是最受开发人员欢迎的语言之一，因为它具有开源、快速、可靠和高性能的特点。当在 Rust 中构建一个新的 API 时，考虑 web 框架对于前端和后端开发的优缺点是很重要的。

在本文中，我们将讨论什么是 web 框架，并为前端和后端开发中使用哪些当前框架提供建议。

## 什么是 web 框架？

web 框架是支持 web 应用程序开发的软件工具；web 框架的范围可以从面向微型应用的小型代码库到面向企业应用的大型代码库，以及介于两者之间的任何东西。

最广泛的 web 框架提供了支持数据库、模板、会话、迁移和其他实用程序的库，以加速开发过程。更简单的框架更关注静态内容呈现的前端管理。无论您的项目需要什么，web 框架都可以提供 web 服务、web 资源和 web APIs，开发团队需要它们来帮助实现他们的想法。

为您的项目选择合适的 web 框架时，您的开发团队应该考虑以下因素的相对重要性:

*   安全性
*   灵活性
*   社区成长
*   项目规模

根据项目的优先级，不同的 web 框架将帮助您解决最紧迫的开发需求。在本文中，我们将专门讨论用 Rust 构建的框架。

### 使用 web 框架有什么好处？

Web 框架使得 web 开发和构建桌面应用程序对于开发者来说更加容易。通过标准化构建过程和自动化常见的活动和任务，web 框架可以节省开发人员的时间，甚至促进重用代码以提高效率。

在接下来的章节中，我们将回顾 Rust 中的 web 框架，因为它们涉及前端和后端开发。然后，我们将根据稳定性、生产就绪性和项目规模来评估每个框架的阶段。

## 前端 web 框架和 Wasm

WebAssembly (Wasm)是一种相对较新的低级语言编码类型，可以在现代 web 浏览器中运行。它支持 C/C++、C#、Go 和 Rust，并带有针对字节码的目标编译，因此可以在 web 上以接近本机的性能运行。Wasm 输出和 JavaScript 一起运行，可以发布到`npm`和其他包。

Rust 使用一种叫做`wasm-pack`的工具来组装和包装 target Wasm 的板条箱。点击了解更多关于 Wasm 和 Rust [的入门知识。](https://blog.logrocket.com/getting-started-with-webassembly-and-rust/)

以下是一些为 Rust (Wasm)设计的前端 web 框架:

1.  [stdweb](#stdweb)
2.  红豆杉
3.  珀西
4.  [种子](#seed)
5.  [史密斯](#smithy)
6.  [索隆](#sauron)
7.  [Dioxus](#dioxus)
8.  [冰镇](#iced)
9.  [金牛座](#tauri)

我们将在接下来的章节中讨论这些问题。

## stdweb

stdweb 是一个前端标准库，它赋予 Rust 与 JavaScript web APIs 直接通信的能力。它旨在通过在语言之间提供简单的 API 绑定来提高速度和性能，让开发人员能够在 Rust 中创建成熟的 JavaScript 应用程序。

stdweb 支持闭包、任意结构和 web APIs 的标准组件，包括 DOM、事件和窗口。这里有几个[样例项目](https://github.com/koute/stdweb/tree/master/examples)的链接，让你了解它是如何工作的。

### 概观

*   稳定:是
*   生产就绪:否
*   项目规模:小型

## 紫杉

紫杉是 [stdweb](https://github.com/koute/stdweb) 的改进版本。它是一个基于组件的框架，类似于 React 和 Elm，支持多线程、基于组件的模式以及其他类似于 stdweb 的特性。

### 概观

*   稳定:是
*   生产就绪:否
*   项目规模:小型、中型和大型

## Ⅳ．　珀西

[Percy](https://chinedufn.github.io/percy/) 是一个为构建单页应用(spa)和管理 UI 而开发的工具包，包括针对特定浏览器和屏幕尺寸(桌面、移动)的优化。珀西仍然是一个进展中的工作；需要对更好的结构、最佳样板文件和一般错误修复进行改进。

### 概观

*   稳定:没有
*   生产就绪:否
*   项目规模:小型、中型和大型

## 种子

Seed 是一个前端框架，用于创建性能驱动的可靠的 web 应用程序，它也有一个类似 Elm 的架构。它有最少的配置和样板文件，并且有清晰的文档，使任何人都容易开始。

### 概观

*   稳定:是
*   生产就绪:否
*   项目规模:小型、中型和大型

## 铁匠铺

史密斯是用本地 Rust 代码编写的，这对开发者来说是一个简单的学习曲线。它支持插值和子组件、事件、状态管理、与 JavaScript 的交互、`smd!`宏(灵感来自 React 的 JSX)和用于部署的[服务器支持](https://github.com/rbalicki2/smithy-app-server)。

### 概观

*   稳定:是
*   生产就绪:是
*   项目规模:小型、中型和大型

## 索隆

Sauron 是一个微前端框架，其灵感来自于 Elm 架构。它支持事件、状态管理和组件。sauron 使用一个名为 [html2sauron](https://ivanceras.github.io/html2sauron/) 的库将 html 转换成 Sauron 视图代码，用于渲染优化。

### 概观

*   稳定:没有
*   生产就绪:否
*   项目规模:小型

## Dioxus

Dioxus 是一个 UI 库，被优雅地设计成类似 React——它是围绕一个虚拟 DOM 构建的，以支持构建 web、移动和桌面的跨平台应用。它支持基于组件的架构、并发和异步、props、内置的错误处理程序、状态管理等等。

### 概观

*   稳定:没有
*   生产就绪:否
*   项目规模:小型、中型和大型

## 冰冷的

[ice](https://iced.rs/)是一个用于跨平台开发的 GUI 库。它以 [Elm](https://elm-lang.org) 风格构建，支持反应式编程、内置小部件和异步，同时还具有模块化/可重用的基于组件的架构，具有类型安全和简单性。

### 概观

*   稳定:没有
*   生产就绪:否
*   项目规模:小型或中型

## 托里

Tauri 是一个 Rust 库，作为一个轻量级 JavaScript 框架构建，用于构建桌面应用程序，集成了任何前端技术，将 HTML、CSS 和 JS 渲染到您的 web 视图中，用于其 UI。

### 概观

*   稳定:是
*   生产就绪:否
*   项目规模:小型或中型

为了获得最好的文档和令人印象深刻的 JavaScript 内插支持，我建议您的选择应该由您的具体用例、库稳定性以及上面列出的每个框架的生产就绪性来决定。

## 后端 web 框架

后端开发是指 web app 的服务器端操作。它还描述了应用程序的核心操作，这些操作通常控制和处理其数据和操作，如提交表单或登录 web 帐户。后端开发主要关注数据管理，以及处理数据所需的数据库、脚本、自动化和架构。

典型后端开发框架的一些最常见的特性包括:

*   数据库管理
*   会议
*   模板
*   对象关系映射
*   迁移

Rust 为后端开发提供了各种 web 框架，包括来自不同供应商的工具、助手和库的组合，每一个都旨在提供一种高效、安全和灵活地构建、测试和运行应用的方法。我们将在接下来的章节中探索以下后端框架:

## 火箭

Rocket 是一个流行的 web 框架，它让开发者可以轻松地[编写快速的 web 应用](https://blog.logrocket.com/rust-web-apps-using-rocket-framework/),而不会忽略安全性、灵活性或功能。它支持测试库、cookies、流、路由、模板、数据库、ORM、样板文件等等。Rocket 还有一个庞大而活跃的开发者社区。

### 概观

*   稳定:没有
*   生产就绪:否
*   项目规模:小型、中型、大型

## Actix

像 Rocket 一样， [Actix](https://actix.rs/) 是另一个强大的后端 web 框架。Actix 的特色是基于 Rust 的 [actor 系统](https://docs.rs/axiom/0.2.1/axiom/#:~:text=Getting%20Started,actors%20for%20all%20processing%20activities.&text=An%20actor%20can%20be%20interacted,process%20a%20message%20only%20once.)的架构模式，并为构建写作服务和微应用程序做好了充分的准备。它支持路由、中间件、测试、WebSockets、databasea 和自动服务器重载，并且可以托管在 NGINX 上。Actix 可以用来构建一个全面的 web app 和 API。

### 概观

*   稳定:是
*   生产就绪:否
*   项目规模:小型或中型

## 哥谭市

[Gotham](https://gotham.rs/) 是一个灵活的 web 框架，专为稳定的 Rust 构建，旨在提升“稳定性、安全性、安全性和速度”它是静态类型的，从而确保应用程序在编译时总是被正确表达。哥谭在 [Tokio](https://tokio.rs/) 和 [hyper](https://hyper.rs/) 的帮助下提供异步支持。

Gotham 支持路由、提取器(类型安全数据请求)、中间件、状态共享和测试。Gotham 没有结构、样板或数据库支持。

### 概观

*   稳定:是
*   生产就绪:否
*   项目规模:小型、中型、大型

## 胡椒味大蒜酱

Rouille 是一个微型 web 框架，它通过一个解析 HTTP 请求的监听套接字采用线性请求和响应设计。它是为了让 Rust 用户简单易学而设计的。Rouille 通过 CGI、输入(请求头和请求体)、内容编码、代理、会话和 WebSockets 支持请求。

### 概观

*   稳定:是
*   生产就绪:否
*   项目规模:小型、中型

## 镍

Nickels 是一个用 Rust 编写的 web 应用程序的最小、轻量级框架。它的 API 受到了流行的 JavaScript 快速框架[的启发。它提供了灵活的路由、中间件、JSON 处理、自定义错误处理程序、模板和样板文件。](https://blog.logrocket.com/marble-js-vs-express-js-comparison)

### 概观

*   稳定:是
*   生产就绪:是
*   项目规模:小型、中型

## 推进器

[推进器](https://github.com/thruster-rs/Thruster)是一个快速可靠的 Rust web 框架，灵感来自 Koa 和 Express.js 的分层和设计。它是 SSL 就绪的，安全的，直观的，可测试的。推进器是为适应异步/等待而建造的，并为中间件、错误处理、数据库和测试提供支持。

### 概述:

*   稳定:没有
*   生产就绪:否
*   项目规模:小型

## 熨斗

Iron 是 Rust 的一个 web 框架，它是在 hyper 中构建的，专注于并发性、可扩展性和最小限度的过度。它可以跨多台机器水平扩展，或者在一台更强大的机器上运行多个线程。因为它被设计成可扩展和可插拔的，所以 Iron 主要将中间件、插件和修改器(第三方扩展)作为其主要组件。

Iron 提供了对路由、JSON 主体解析、URL 编码数据解析、会话和静态文件的支持。

### 概观

*   稳定:没有
*   生产就绪:否
*   项目规模:小型

## 潮汐

[Tide](https://github.com/http-rs/tide) 是一个类似于 Express.js (Node)、Sinatra (Ruby)和 Flask (Python)的用于快速开发的最小框架，它促进了在异步版本中构建 web 应用。

Tide 提供了对路由、认证、监听器、日志、模板引擎、中间件、测试和其他工具的支持。

### 概观

*   稳定:是
*   生产就绪:是
*   项目规模:小型、中型、大型

## 小球

Dropshot 是一个简单轻量的服务器端库，用于创建 REST APIs。它支持 [openAPI](https://spec.openapis.org/oas/v3.0.3) 规范、异步和日志。

### 概观

*   稳定:没有
*   生产就绪:否
*   项目规模:小型、中型

对于构建高级 web 应用程序，我推荐 Rocket、Actix 和 Tide(用于异步支持)来滚动您的后端。它们也都被 Rust 社区所接受，并且每个都提供了一个大的支持库。

## 结论

在为你的前端或后端开发项目选择正确的 web 框架时，重要的是要考虑以下几点:框架稳定吗？生产就绪了吗？它适合您的项目规模吗？

根据你的回答，你将开始使用 web 框架来提高 Rust 的效率、速度和生产力。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。