# 生产中应该用 Svelte 吗？- LogRocket 博客

> 原文：<https://blog.logrocket.com/should-you-use-svelte-production/>

***编者按**:这篇文章最后更新于 2022 年 10 月 17 日，以反映 Svelte 的新功能以及 SvelteKit 的增加。*

随着单页面应用程序的兴起，我们已经看到了 web 应用程序的逻辑和功能的重大转变。方便的是，您现在可以在客户端执行以前在服务器端执行的大多数操作。

我们无法再用普通的 JavaScript 处理所有这些复杂性，这只是时间问题，导致了今天最流行的 JavaScript 框架的兴起。为了弥补 JavaScript 语言本身的缺陷，这些框架给了我们很多新的特性，比如数据绑定、通过 DOM diffing 简化 DOM 操作、状态管理和传统架构。但是，代价是什么呢？

虽然我自己是一个重度框架用户，尤其是 Vue，但有时，感觉框架做的比我们需要的多得多，老实说，这可能会成为一个问题。对我来说幸运的是，[不久前我遇到了 svelite](https://blog.logrocket.com/build-spa-svelte-svelte-spa-router/)，并在一个制作项目中试用了它。这是令人兴奋的，所以我在这里，对苗条赞不绝口。

在这篇文章中，我们将深入探讨苗条，回顾一些可能影响你在生产中使用苗条的决定的因素。我们开始吧！

## 目录

## 什么是苗条？

Angular、React、Vue 等框架和库都是在浏览器中运行的。每当您运行用这些框架创建的应用程序时，框架会在您自己的应用程序代码执行之前首先启动，这导致了两个明显的缺点。

首先，框架代码和您的应用程序代码都被导出，导致您的应用程序在导出到生产环境时比它应该的要大。此外，在框架被引导的阶段，在执行过程中有一个初始延迟；但是，后续执行速度更快。

Svelte 帮助我们解决了这两个问题。Svelte 是一个框架和编译器，它在编译时将 HTML、CSS 和 JavaScript 代码编译成较小的独立 JavaScript 代码。使用 Svelte 的方法，只有您的业务逻辑而没有额外的框架被发送给您的应用程序用户。

## 性能和捆绑包大小比较

为了理解使用 svelet 的一些好处，我们将回顾一个比较 svelet 与 Vue、React 和 Angular 的基准测试。我使用克劳斯斯特的 [基准工具](https://krausest.github.io/js-framework-benchmark/current.html) 进行了基准测试。

![Svelte Startup Metrics Comparison](img/72e1dd73d33ee8e8c47618ad5690804a.png)

Svelte startup metrics comparison

谈到交互性和压缩后资源转移的时间，Svelte 显然是赢家:

![Svelte Memory Usage Framework Comparison](img/b38a703a797ce1052189016223b533cc.png)

Svelte memory usage comparison

在内存使用方面，Svelte 显然也名列前茅。

## 使用 Svelte 构建的流行网站

如果你决定在生产中使用苗条，你可以肯定你不是一个人。许多知名公司也已经在使用它了。据[Svelte . dev](https://svelte.dev/)报道，一些已经在使用 svelite 的热门公司，包括 [GoDaddy](https://svelte.dev/whos-using-svelte/godaddy.svg) 、 [NameCoach](https://name-coach.com/) 、 [Rakuten](https://global.rakuten.com/corp/) 、 [1Password](https://1password.com/) 、[New York Times](https://nytimes.com/)、 [Creative Tims](https://www.creative-tim.com/templates/svelte?ref=svelte.dev) 和 [mail.ru](https://mail.ru/) 。

## 生产中应该用 Svelte 吗？

Svelte 承诺了良好的开发者体验。当您进行转换时，您将享受到以下一些好处:

### 赞成的意见

Svelte 因其最小的学习曲线而非常容易学习而自豪。因为你是用 HTML，CSS，JavaScript 写苗条组件，所以你可以在五分钟左右开始写苗条的 app。

如前所述，Svelte 是一个编译器。在构建过程中，您的瘦组件被转换成普通的 JavaScript。因此，您可以避免在浏览器中运行代码之前启动或引导框架的开销，从而加快执行速度。

如果您使用过我之前提到的其他框架，您可能已经看到在您的应用程序中创建可重用组件是多么有帮助。Svelte 也是以这种基于组件的开发方法为核心构建的。就像 Vue 一样，你可以完全使用 Svelte 构建你的应用程序，或者只是将它添加到应用程序的某些部分。

使用 Svelte 的开箱即用的作用域样式，您可以对一个组件进行样式化，而不用担心 CSS 泄漏到其他组件。此外，状态管理、模板、服务器端渲染、动画和插件系统是 Svelte 开箱即用的众多工具中的一部分。

虽然 Svelte 仍然是一个相对较新的框架，但它的社区已经在快速增长。你可以在 [Discord](https://discord.com/invite/Hmv446f) 上加入关于苗条身材的讨论，在 [StackOverflow](https://stackoverflow.com/questions/tagged/svelte) 上也有超过 3500 个问题。

### 主要缺点

因为 Svelte 仍然相对较新，所以它还没有像其他框架一样拥有庞大的社区。然而，随着框架的发展，这种情况必然会改变。

当谈到开发工具和软件包时，在撰写本文时可供苗条的开发人员选择的选项有限。然而，随着社区的发展，越来越多的开发者开始使用 Svelte，这个问题可能会逐渐消失。

## 苗条套装

SvelteKit 是一个 [web 应用程序框架，它提供了灵活的、基于文件系统的路由](https://blog.logrocket.com/exploring-sveltekit-the-newest-svelte-based-framework/)以及有趣的开发体验。使用 SvelteKit，您不必担心牺牲 SEO、渐进式增强或 Svelte 提供的任何性能。

### 内置路由系统

SvelteKit 包括一个基于文件系统的路由器，允许您轻松地将特定文件夹中的 Svelte 组件文件路由为 URL 路径。

换句话说，通过简单地创建`routes/about.svelte`和`routes/contact.svelte`，这些组件文件被立即分别路由到`/about`和`/contact`。这伴随着加载页面数据、创建布局、处理路由错误和重定向等其他机制。

### 资产处理

在幕后，SvelteKit 集成了 Vite，并将自动处理导入的资产以提高性能。出于同样的原因，您可以轻松地动态转换文件，比如将`.avif`图像文件转换为`.webp`，或者为不同的设备呈现不同大小的响应图像。

### 搜索引擎优化，服务人员，等等

处理搜索引擎优化是一件轻而易举的事。它包括一种机制，可以轻松地将服务人员集成到您的项目中。它在设计时就考虑到了可访问性，并且包含了一些额外的特性，使您的开发过程更加愉快。

## 结论

在本文中，我们考虑了苗条框架的优点和缺点。毫无疑问，利大于弊。虽然 Svelte 可能不是解决开发人员可能遇到的每个问题的完美解决方案，但它有很多优点。这仅仅是苗条的开始。既然《苗条身材》的创作者 Rich Harris 已经加入 Vercel 团队，全职致力于《苗条身材》的研发，我们可以对《苗条身材》充满信心。

我希望你喜欢这篇文章。编码快乐！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)