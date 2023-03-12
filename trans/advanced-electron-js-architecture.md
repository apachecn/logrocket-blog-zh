# advanced electron . js architecture-log rocket 博客

> 原文：<https://blog.logrocket.com/advanced-electron-js-architecture/>

不久前，我开始从事一个名为 [taggr](https://taggr.ai/) 的兼职项目，这是一个完全离线的交互式照片探索应用。开发 taggr 需要我从应用程序复杂性的最低层向上导航，尝试多种架构方法并探索每种方法的局限性。

在本文中，我们将讨论使用[electronic](https://blog.logrocket.com/building-cross-platform-desktop-apps-with-svelte-electron-and-reloadly/)构建桌面应用程序的不同架构方法的权衡。我们将分析每种方法的缺点，并介绍一种旨在解决它们的体系结构。

本文中呈现的蓝图是一个正在进行的努力的结果，该努力的目的是找到一种方法，使我这个单独的开发人员能够通过利用标准的 web 工具来管理应用程序的复杂性并满足性能需求。让我们开始吧！

> 注意:你可以跟随这个 [GitHub 库](https://github.com/aperkaz/yarn-workspaces-electron)。

## Electron.js 简介

在过去的几年里，JavaScript 在浏览器领域的使用显著增加，这主要得益于 React、Vue 和 Angular 等库和框架的帮助。类似地，我们已经看到 JavaScript 通过 Node.js、Deno 和 React Native 超越了浏览器。

Electron.js 就是这些框架中的一个。自 2013 年发布以来， [Electron](https://www.electronjs.org) 已经发展成为构建跨平台桌面应用程序最常用的框架之一。VS Code、Slack、Twitch 以及其他许多流行的桌面应用程序都是使用 Electron 构建的。

### 电子如何工作

Electron 在其二进制文件中嵌入了 Chromium 和 Node.js，使 web 开发人员无需编写本机代码就能编写桌面应用程序。Electron 实现了一个由主进程和呈现器进程组成的多进程模型，类似于 Chromium 浏览器。

每个应用程序的窗口都是一个渲染进程，它在窗口级别隔离代码执行。主进程负责应用程序生命周期管理、窗口管理或渲染进程，以及系统菜单、通知和托盘图标等本机 API。

每个应用程序都由一个主进程和数量可变的渲染进程组成。渲染进程可以用于 JavaScript 代码执行，并且可以在没有 UI 的情况下隐藏。

![High Level Electron Arch Two Render Processes](img/648af4c2fa5a3413740e12df12b16d6f.png)

> 注意:电子并不是构建跨平台桌面应用程序的唯一选择。其他[替代方案](https://github.com/sudhakar3697/electron-alternatives)提供了更少的资源消耗和更轻的可执行程序，但是没有一个共享社区、学习资源或电子的广泛采用。

## 电子入门

如果您还不熟悉 electronic，那么入门非常容易，尤其是因为 Node.js 和 JavaScript 的知识是可以转移的。

电子提供了抽象和熟悉的语言，减少了上市时间和开发成本。本质上，Electron 为桌面应用程序开发所做的事情类似于 React Native 为移动开发所做的事情。

Electron 还管理应用程序更新的构建和部署，使跨平台应用程序保持同步版本变得容易。您可以通过自动更新和在运行时加载远程资产来实现这一点。

然而，电子的好处并不是没有代价的。电子版附带了 Chromium 和 Node.js 环境，这导致电子版应用程序比其本地实现的应用程序消耗更多的资源。因此，人们对电子的生存能力有不同的看法。

此外，复杂的电子应用程序带来了与底层架构相关的性能和开发人员体验挑战。让我们通过分析三个不同的应用程序示例来深入考虑这些权衡。

## 特定于应用的权衡

让我们来看看三个具有不同复杂性的虚构应用程序的高级架构。请记住，我们的应用程序分析并不旨在面面俱到，而是旨在梳理您可以使用 electronic 构建的潜在应用程序。

### 低复杂度应用

先说一个低复杂度的 app。对于我们的例子，我们将考虑把一个网页打包成一个桌面应用程序。示例包括即时消息应用、数据分析仪表板和在线流媒体应用。

许多企业提供他们成功的基于网络的应用程序的桌面版本，使我们成为一个常见的用例。我们将使用 Electron 在 Chromium 上运行应用，[消除不必要的多填充](https://www.electronjs.org/docs/tutorial/performance#5-unnecessary-polyfills)并提供一个统一的用户界面，而不是一个异构的浏览器环境。

#### 低复杂度应用的主要特性:

*   代码将在 web 应用程序和桌面应用程序之间共享
*   更新周期将在 web 应用程序和桌面应用程序之间共享
*   桌面应用程序将加载与 web 应用程序相同的资源，并在 Chromium 中呈现它们
*   后端(如果适用)将保持不变
*   从桌面和 web 应用程序都可以以相同的方式访问后端
*   依赖于浏览器支持的功能，如 [WebWorkers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) 和 [WebGL](https://developer.mozilla.org/en-US/docs/Glossary/WebGL) ，将无需更改即可跨平台运行
*   我们将使用标准的 web 开发工具

#### 低复杂度应用的高级架构

作为一个架构示例，我们将使用桌面应用程序作为 [Telegram chat](https://web.telegram.org) web 应用程序。Electron 将充当现有 web 应用程序的包装器，无需对后端进行任何更改。

![Low Complexity App Electron Architecture](img/d11cbe03044cfc97d3e56536eec7d728.png)

对于这种类型的应用程序，设置电子很容易！web 应用程序代码库级别不需要任何更改。

### 中等复杂度的应用程序

像 Spotify 这样的音乐流媒体应用程序，使用本地缓存提供离线流媒体支持，是中等复杂程度应用程序的典型例子。桌面应用程序可以使用电子构建本地缓存层。

与低复杂度的应用程序类似，中等复杂度的应用程序也可以作为 web 应用程序的补充。主要区别是提供离线支持的能力。因此，这些应用在概念上与具有离线支持的渐进式 web 应用( [PWAs](https://blog.logrocket.com/why-you-should-turn-your-app-into-a-pwa/) )相关。

#### 主要特点:

*   大多数代码可以在 web 和桌面应用程序之间共享(例如，在一个 UI 层中)
*   桌面应用程序将有一个本地缓存实现，它将拦截后端请求，填充缓存，并在离线时提供缓存的结果
*   我们需要使用高级电子 API 来检查桌面应用程序是在线还是离线
*   更新周期不一定在 web 和桌面之间共享。桌面将使用其离线用户界面从静态文件加载用户界面，并使用缓存创建自定义请求层
*   除了定制请求模块之外，您可以利用标准的 web 开发工具，定制请求模块必须针对 Electron 进行开发和调整

#### 高层架构

让我们想象一下，我们的流媒体应用程序播放一首每日歌曲。如果没有互联网连接，它将提供可用的缓存歌曲。

![Medium Complexity App Electron Architecture](img/aff287a7c7b01c2e162a1cf9569ea39a.png)

如上面的模式所述，UI 将由本地资产而不是 CDN 提供，请求层必须定制为支持缓存。虽然这个例子相对简单，但是代码共享和缓存需求最终会增加复杂性，需要定制的电子代码。

### 高复杂性应用程序

对于最高级别的复杂性，我们来看看类似于 [sharp](https://github.com/lovell/sharp) 这样的批量图像处理 app。该应用程序必须能够处理成千上万的图像，并完全离线工作。

离线应用与前面两个例子有很大的不同。具体来说，典型的后端工作负载，如图像处理，将通过创建一个离线应用程序在 Electron 中执行。

### 主要特点:

*   我们的大部分代码将为桌面应用程序定制
*   该应用程序将有自己的发布周期
*   后端将从电子内部运行(即，从渲染过程)
*   可以使用标准的 web 开发工具，但是这取决于所定义的架构
*   我们可能需要使用本地模块，如数据库访问、图像处理或机器学习
*   多个进程可能需要低级别的电子 API 访问，特别是对于[进程间通信](https://blog.logrocket.com/handling-interprocess-communications-in-electron-applications-like-a-pro/) (IPC)

#### 高层架构

对于架构方案，我们来考虑一下上面说的离线图像处理 app。

![High Complexity Electron App Architecture](img/f259c01077cb30d8972fb92c6a10d434.png)

该模式按照电子文档构建应用程序，这带来了一些限制。首先，在隐藏的渲染器进程中运行长时间的 CPU 密集型操作时，性能会明显下降。

注意不要在主进程中运行操作[。这样做可能会阻塞主进程，导致应用程序冻结或崩溃。](https://www.electronjs.org/docs/tutorial/performance#3-blocking-the-main-process)

此外，将业务逻辑和传输层耦合到电子 API 限制了重用标准 web 开发工具的选择。主进程和渲染器进程之间的通信使用 IPC，当在两个渲染进程之间通信时，需要主进程往返。

如果您的应用程序属于低复杂度或中等复杂度类别，那么恭喜您！离线应用程序中出现的许多令人头疼的问题不会出现在你身上。但是，如果您的 app 需求属于高复杂度范围，还是有希望的！

## 高级架构提案

当我们考虑离线应用中的问题，如性能下降、渲染进程之间的往返通信以及整体开发人员体验时，我们需要一个专门的架构:

![Advanced Modularized Architecture Proposal](img/53627a590cc53f72497210e0ff31f499.png)

提议的架构基于以下支柱:

*   前端和后端共享的代码被提取到单个模块中
*   UI 代码是电子不可知的，因此可以应用 web 开发最佳实践
*   UI 和页面路由是使用受控组件和集中式应用程序状态构建的
*   后端从单独的 Node.js 进程运行
*   前端和后端模块通过消息传递进行通信

让我们详细了解每个模块！

> 注意:堆栈的各个部分纯粹是根据个人喜好选择的，并且可以互换。比如，你可以把 TypeScript 换成 JavaScript，React 换成 Vue，Redux 换成 MobX，或者 npm 包换成代码共享，而不是 Yarn 工作空间。只要尊重上面提到的支柱，您就可以自由选择整个堆栈。

## 共享模块

共享模块负责前端和后端模块共享的代码和类型。它使您能够将两个模块作为单独的实体进行开发，同时仍然共享与领域相关的代码和类型。

代码共享是通过使用 [Yarn workspaces](https://classic.yarnpkg.com/en/docs/workspaces/) 来实现的，这是将模块发布为 npm 包、发布并对其进行版本控制的一种简单替代方法。

### 主要特点:

*   类型脚本代码库
*   消息传递通信的类型:包含前端和后端所需的有效负载和消息处理程序
*   领域模型和实体
*   日志和事件报告等共享工具

## 前端模块

前端模块负责所有 UI 的事情。它包含我们应用程序的组件和动画，但不包含业务逻辑。在生产中，Electron 从生成的静态文件中为其提供服务。

### 主要特点:

*   可以访问共享模块的 Typescript 基本代码
*   使用 React 以创建 React 应用程序为模板构建用户界面
*   使用 Redux 作为状态管理器，它确定性地定义了 UI 的呈现状态
*   通过消息传递与后端通信:前端公开一个消息处理程序，它监听来自后端的消息，并相应地修改 Redux 存储
*   使用故事书隔离组件开发

## 带有电子模块的后端

后端模块包含后端代码库和电子设置代码。业务逻辑和长时间运行的操作，比如图像处理，将在一个单独的 Node.js 进程中运行，这样 UI 就不会受到性能下降的影响。

### 主要特点:

*   Typescript 代码库，可以访问共享模块
*   后端作为分叉的 Node.js 进程运行，这提高了长时间运行和计算开销大的任务的性能
*   对本机依赖项的访问
*   执行预构建步骤，将本机依赖项与电子版本相匹配
*   包含所需的电子配置和打包脚本

## 通信层

前端和后端使用与`[node-ipc](https://github.com/RIAEvangelist/node-ipc)`的进程间消息传递进行通信。消息传递允许`async`和基于事件的通信。

`async`交流最适合短命作战。前端可以等到后端处理完消息后立即得到结果。

基于事件的通信更适合长时间的操作，比如批处理。当任务在后端处理时，它会发送事件，这些事件会在 Redux 中修改前端的应用程序状态。后端可以异步完成长时间运行的任务，并定期更新 UI 显示的进度。

### 主要特点:

*   `node-ipc`作为交流库
*   共享模块中完全类型化的消息负载和处理程序
*   异步和基于消息的通信支持

## 结论

对于使用不同的 web 技术构建跨平台桌面应用程序来说，electronic 是一个很好的选择。尽管 Electron 在低复杂度的应用中易于使用，但是随着复杂度的增加，性能和开发人员体验的限制将会显现出来。

提议的架构旨在为高复杂性应用程序提供一个良好的概念基础。当然，它可能需要根据用例进行扩展，但我发现它为许多类型的应用程序提供了良好的基础。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)