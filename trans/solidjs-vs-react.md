# SolidJS 与 React:比较声明式 UI 库

> 原文：<https://blog.logrocket.com/solidjs-vs-react/>

想知道 SolidJS 和 React JavaScript 库之间的区别吗？我也是。在本文中，我们将揭示它们之间的相似之处和不同之处，使您能够根据您的用例来决定哪一个工作得最好。

React 已经存在很长时间了，它在很大程度上革新和影响了 JavaScript 生态系统。另一方面，Solid 对该党来说是新生事物，大有可为。[你可以在这里了解更多关于 Solid 的知识](https://blog.logrocket.com/introduction-solidjs/)。

## solidus 和 react 之间的相似性

Solid 和 React 共享几乎相同的设计理念并不是什么新闻，任何在这两个库中都编写过代码的人都会很快理解这一点。让我们回顾一下是什么使固体和反应如此相似。

### JSX 一流的支持

JSX 是“JavaScript XML”的简称，你可以把它看作是编写 React 或固体组件的语法糖。这意味着您可以分别使用`document.createElement`或`React.createElement`函数创建没有 JSX 的 Solid 或 React 代码。

JSX 允许你用 HTML 描述用户界面的样子，同时给你 JavaScript 的全部力量。除了作为视觉辅助，在 Solid 或 React 中使用 JSX 对调试也很有用，因为它显示了更直观的警告和错误信息。

### 陈述性质

Solid 和 React 都是声明性的，因为当数据改变时，它们有效地更新并呈现正确的组件。

在声明式编程中，您只需描述所需 UI 的最终状态，呈现引擎将确定最佳执行方式。与命令式的代码编写方式相比，声明式组件更容易调试，可读性更强。

### 单向数据流

数据流的单向模式意味着数据只有一种方式传输到应用程序的其他部分。在 Solid 和 React 等库中，这意味着您通常需要将子组件嵌套在更高级的父组件中。

单向模式的数据流具有以下优势:

*   你对你的数据有更多的控制，所以你会有更少的错误
*   更多的控制意味着调试轻而易举
*   高效率，因为系统有边界，并且已经知道哪个组件拥有哪个数据

## SolidJS 和 React 的区别:SolidJS 为什么这么快？

尽管拥有几乎相同的设计理念，但有几个特性将两个 JavaScript 库区分开来。我们将在本节中讨论其中的一些差异。

### 缺少虚拟 DOM

Solid 和 [React](https://blog.logrocket.com/tag/react/) 最显著的区别之一是没有虚拟 DOM。与使用主 DOM 会降低应用程序速度的观点相反，这是 Solid 如此高性能的原因之一。

虚拟 DOM 是主 DOM 的轻量级实现。过去，JavaScript 库使用虚拟 DOM，因为它比 DOM 本身相对更快。毕竟，DOM 不是为处理大规模的频繁更新而构建的，所以 JavaScript 库对虚拟 DOM 进行更改，然后将这些更改与真实 DOM 进行比较，并合并两者。

这是 React、 [Vue.js](https://blog.logrocket.com/tag/vue/) 和 Riot.js 等许多库和框架使用的解决方案，但今天，Svelte 和 Solid 的创建者已经将虚拟 DOM 描述为只会降低库性能的纯开销。他们已经找到了替代的、更快的选择，包括使用真实的 DOM(击败了其他虚拟 DOM 竞争对手)。

Solid 在不使用虚拟 DOM 的情况下实现这种高性能速度的方法是将其模板编译成真实的 DOM 节点，并将更新封装在细粒度的反应中。这样，当您的状态更新时，只有依赖于它的代码才会运行。

### 组件不会重新渲染

与 React 不同，Solid 中的组件只渲染一次，并且只有组件中使用的 JSX 表达式和基本体才会更新。

这为 Solid 带来了性能提升，因为每次使用组件时都不必重新渲染。Solid 的反应能力足以跟踪组件本身内部发生的变化。

### 细粒度反应性

React 本身并不完全是“反应性的”，这一点 [React 团队承认](https://reactjs.org/docs/design-principles.html#scheduling)。这不应该以任何方式让任何希望使用 React 的人倒胃口，因为它仍然可以完成工作，并且受到全球成千上万开发者和公司的喜爱。

然而，Solid 在制造时就考虑到了反应性，并以此作为卖点。它使用术语“细粒度反应”来描述其快速更新数据的性能速度和能力。

## 固体与反应一目了然

在本节中，我们将根据以下指标比较这两个库:

*   开发者体验
*   表演
*   社区和生态系统
*   文档和资源

### 开发者体验

Solid 和 React 都有很好的开发者体验。这是因为 Solid 和 React 共享一些相同的理念，例如单向数据流、读/写分离和不可变接口(除了虚拟 DOM 的实现，正如我们前面讨论的)。

Solid 还提供一流的 JSX 和类型脚本支持，以及一些类似于 React 的功能，如上下文、门户、SSR、水合等等。

### 表演

在性能方面，Solid beats React 和几乎所有其他 JavaScript 库都是最快的。

不要误解我的意思，React 非常快，但它的潜力受到虚拟 DOM 实现和对干净代码架构的追求的限制。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

固体的高性能速度可以归因于一种被称为颗粒反应性的设计原则，这是创作者在本文中谈到的。

JS 框架基准测试为我们提供了一种可视化所有 JavaScript 库的方法，以及它们与普通 JavaScript 的比较。正如我们在性能、内存使用和启动时间测试中看到的，Solid trumps 比大多数库更接近普通 JS 的速度。

### 社区和生态系统

毫无疑问，React 赢得了这一市场，因为它拥有庞大的第三方 UI 组件库、工具和资源生态系统。

在撰写本文时，React 以超过[180，000 Github Stars](https://github.com/facebook/react)和每周 870 万 [npm 下载](https://www.npmjs.com/package/react)的速度进入，而 Solid 以超过[13，000 Github Stars](https://github.com/solidjs/solid)和每周 11.5 万 [npm 下载](https://www.npmjs.com/package/solid-js)的速度进入。

关于第三方库，Solid 还没有很多，但是它有第一方库。它的第一方库相当于其他 JavaScript 库中的其他流行库，比如路由的 [Solid App Router](https://github.com/solidjs/solid-app-router) ，编写组件测试的 [Solid Testing 库](https://github.com/solidjs/solid-testing-library)，动画的 [Solid Transition Group](https://github.com/solidjs/solid-transition-group) 。

React 的数字让 Solid 相形见绌，但我们必须考虑到一个事实，即 Solid 对竞争来说是相当新的，因为它在 2018 年开源，现在有超过 [70 名贡献者](https://github.com/solidjs/solid#contributors)。它的增长并不惊人，但却很稳定。

如果你喜欢亲自动手解决问题，或者为一个图书馆的早期发展做贡献，那么 Solid 就是你的不二之选。

### 文档和资源

特定框架或库的资源或文档的数量和质量取决于生态系统的建立方式。

因为 React 已经存在很长时间了，它有很好的文档以及一个巨大的资源生态系统，新手和高级开发人员都可以遵循。

虽然 Solid 是一个相当新的产品，但它的创造者 Ryan Carniato 做了很多工作，通过制作关于 Solid 发展的视频和博客帖子来展示 Solid。社区成员也在图书馆上创建了许多有用的资源和指南。Solid 在其网站上也为新来者准备了适当的文档。

## 最终裁决

在本文中，我们已经讨论了相似性和差异，甚至基于开发人员经验、社区和文档以及其他指标对 Solid 和 React 进行了比较。

我们可以看到，Solid 在性能和开发人员体验方面做得不错，这是最重要的基准。但是它成为最常用图书馆的最大障碍是它的社区和生态系统。

让我们面对现实吧，大多数人和公司只会选择 React，因为它背后有庞大的生态系统和资源支持。我的意思是，React 中几乎所有东西都有一个 npm 包。在这个阶段，Solid 无法与之竞争，所以如果 Solid 希望他们的图书馆被大规模采用，在社区建设方面还有很多工作要做。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)