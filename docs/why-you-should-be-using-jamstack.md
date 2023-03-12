# 为什么你应该使用 JAMstack 

> 原文：<https://blog.logrocket.com/why-you-should-be-using-jamstack/>

[Obinna Ekwuno Follow](https://blog.logrocket.com/author/obinnaekwuno/) Writer of all things technical and inspirational. Developer and Community Advocate. In a love-love relationship with JavaScript🔥

# 为什么应该使用 JAMstack

## 

2019 年 6 月 20 日 3 分钟阅读 1094 年

在发展的世界中工作可能有点令人困惑，许多趋势会打断我们的正常生活，给我们一个糟糕的 FOMO 案例。我们大部分时间都卡在学习、再学习和忘记之间。有时，新趋势与我们已经在使用的技术没有什么不同，但其他时候，它会改变你的生活，或者在这种情况下，改变你的堆栈。

在这篇文章中，我们将关注 [JAMstack](https://jamstack.org/) 以及它是如何给我们构建 web 平台的方式带来巨大变化的。我们将探索这种简化技术堆栈的方法如何缩短开发时间，提高性能和安全性。

## 我们一直在做什么？

传统上，我们通过设置服务器来帮助与数据库进行交互，从而构建网站和 web 应用程序，是的，这是可行的，但我们也必须花费大量时间来担心性能和设置这些设施。这些平台的安全协议成本也很高，而且由于我们将信息从服务器端转移到客户端，平台可能容易出现安全风险。

## 什么是 JAMstack？

它不是鸟，它不是飞机……不，它不是超人。jam stack(JavaScript，API & Markdown)是一种构建网站和应用的新方式，它提供了更好的性能、更高的安全性、更低的扩展成本和更好的开发者体验。它通过将大部分功能保留在客户端并将所有其他功能抽象给第三方 API 来实现这一点。基本上，所有繁重的工作都由 API 来完成。

随着对 web 性能和出色用户体验的重视，出现了一些趋势:

*   PWA(离线应用程序，可靠，像本地一样的 UX)
*   [无服务器](https://serverless.com/)(简化的后端、一流的服务、无限的可扩展性，无需考虑服务器的物理位置)
*   [GraphQL](https://graphql.org/) (使用特定、快速变化的数据库模式的应用程序的标准化数据访问)
*   SSG(使用 CDN 技术对预渲染内容页面进行边缘缓存)

以上趋势都是独立发展的，直到 JAMstack 来了，统一了。现在 JAMStack 出现了，人们开始远离服务器。与此同时，他们追求速度、优化和简洁的网站。

遇见书库

## 在我们进一步讨论 JAMstack 如何实现其令人惊叹的功能之前，让我们来看看其他的堆栈以及它们是如何工作的，以便更好地理解 JAMstack。在它出现之前，开发者使用不同的工具来建立网络平台和应用程序。

灯

### [LAMP](https://www.digitalocean.com/community/tags/lamp-stack?type=tutorials) 代表一个 Linux 托管网站的基础。LAMP 栈由 Linux 操作系统(L)、一个 [Apache Web 服务器](https://www.techopedia.com/definition/4851/apache-web-server) (A)、一个 MySQL 数据库层(M)和一个 PHP 脚本层(P)组成。这个堆栈的主要问题是它有一个学习曲线。这增加了设置功能性应用程序的时间，并且最终会导致具有并发用户的应用程序的性能问题以及安全问题。

意思是

### 该堆栈由 MongoDB (M)文档数据库、后端 web 应用程序框架 Express.js (E)、前端 web 应用程序框架 Angular.js (A)和 JavaScript 运行时环境 Node.js (N)组成。这里的平台从头到尾都是用 JavaScript 构建的，对于大型的非关系型数据集，这是非常值得推荐的，这里唯一的问题是所有的事情都是用 JavaScript 完成的。

MERN

### 该堆栈由 MongoDB (M)文档数据库、后端 web 应用程序框架 Express.js (E)、用于构建用户界面的 JavaScript 库 React (R)和 JavaScript 运行时环境 Node.js (N)组成。同样，平台从头到尾都是用 JavaScript 构建的，这里唯一的问题是所有事情都是用 JavaScript 完成的。

其他堆栈如何工作

## 构建和托管是耦合的

*   用户请求一个页面。在数据库、后端代码、服务器、浏览器和缓存层之间的一系列(长)交互之后，文件得到处理和服务
*   核心更新通常通过 FTP 被推送到生产服务器。必须维护或更新数据库
*   内容更新通过传统的内容管理系统推送，如 WordPress 或 Drupal
*   JAMstack 工作流

## 构建和托管是*分离的*

*   用户请求一个页面。该文件已经被编译并从 CDN 直接提供给浏览器
*   核心更新通过 Git 推送；网站完全通过现代构建工具如静态网站生成器(Gatsby，Hugo)来重新构建
*   内容更新通过 Git 或静态网站 CMS 推送
*   詹姆斯塔克超能力

## 您可能想知道为什么需要使用另一个堆栈。我有五个理由让你使用 JAMstack:

**更好的性能**:使用 JAMstack 构建有助于在部署时生成页面，因为它们主要存储为标记，并且可以通过 CDN 交付。

*   **高安全性**:静态网站存在漏洞的可能性非常低，因为它只是通过 CDN 提供的 HTML 文件和外部 API 处理。这降低了网站遭受攻击的风险。将 SSL 证书与[集成起来很容易，让我们加密](https://letsencrypt.org/)。此外，它利用服务器端微服务 API 来保护网站。
*   **便宜且快速的部署** : JAMstack 网站可以轻松部署，无需经历配置数据库和保护数据库的麻烦。我们只处理易于安装和运行的静态文件。
*   **牛逼的开发者体验**:使用 JAMstack 最重要的一个好处就是学习起来并不困难。仅仅凭借 HTML、CSS 和 JS 经验，开发者就可以构建复杂的网站。站点生成器的使用消除了为内容和营销维护单独堆栈的需要。
*   伟大的社区:jam stack 社区正在不断发展壮大。对各种功能的新支持每天都在涌现。更令人兴奋的是正在创建的[当地社区聚会](https://jamstack.org/community/)。[在线社区](https://gitter.im/jamstack/community)也令人惊叹。
*   另一个值得考虑的特性是将核心动态功能集成到 JAMstack 网站中的能力。这项技术有能力处理动态的服务器端特性，用像[无服务器](https://serverless.com/)这样的工具处理后端功能。用[表单礼包](https://formspree.io/)或[网络表单](https://www.netlify.com/docs/form-handling/)进行表单处理。完整的搜索功能[谷歌搜索](https://cse.google.com/cse/)、 [lunr.js](https://lunrjs.com/) 和 [Algolia](https://www.algolia.com/) ，使用 [Cloudinary](https://cloudinary.com/) 处理最佳图像交付。你所要做的就是即插即用。

结论

## 在本文中，我们确立了使用 JAMstack 开发现代 web 平台的优势。我强烈建议学习更多关于 JAMstack 的知识，无缝地构建更快的平台。

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).