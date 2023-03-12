# Frontity - LogRocket 博客的 5 种替代方案

> 原文：<https://blog.logrocket.com/5-alternatives-to-frontity/>

如果你使用 React 作为前端库，并使用 WordPress 后端来支持你的博客或网络应用，如果你没有使用 Frontity 这样的框架，你可能会做错。

React 无疑是最受欢迎的前端库，根据 W3Techs 的数据，WordPress 为互联网上近 40%的网站提供支持。 [Frontity](https://frontity.org/) 将这些强大的技术结合在一起，帮助你创建服务器端的、动态的、无头的 WordPress 站点和博客。最棒的是，它是免费和开源的。

在本指南中，我们将向您展示如何使用 Frontity，并向您介绍一些使用 React 构建 WordPress 站点的替代解决方案。

## 什么是前沿性？

Frontity [将自己](https://github.com/frontity/frontity)描述为“一个基于 ReactJS 开发 WordPress 主题的免费开源框架。”本质上，它抽象出与这些技术相关的复杂配置和其他学习曲线，以帮助您无缝地创建一个网站。

Frontity 使用 WordPress 作为创建和管理网站内容的无头 CMS。WordPress REST-API 充当了 WordPress 和 React 之间的中间人，它允许您检索内容并无缝地生成 HTML。

React 实际上是用来为你的内容服务的。这是在 Node.js 服务器上单独运行的，为你的网站或博客产生一个很好的 SSR。

以下是使用 Frontity 的一些优点。

*   **可扩展**:我们都知道 WordPress 是如何通过不同的插件来创建各种功能的，Frontity 也提供了这种开箱即用的可扩展性。插件，主题和扩展可以安装和激活，不需要任何代码变化。
*   SSR 和 SEO 友好的 : Frontity 开箱即用地进行服务器端渲染。这种方法对于动态内容非常有效，对于 SEO 也非常有用
*   闪电般的加载速度:我喜欢 Frontity 的一点是加载速度。Frontity 发送一个导航就绪的 HTML 页面，可以立即使用，因此初始加载几乎是即时的
*   **零设置要求**:连接所需的一切都已经为您设置好了(React、webpack、Babel、routing 等)。)这样你就可以直接开始创建你的网站了
*   **易用性** : Frontity 负责处理与连接 WordPress 和 React 相关的复杂性

对于上下文，让我们快速浏览一下如何在您的项目中使用 Frontity。

第一步是创建一个新的 Frontity 项目。内置的 CLI 工具使您能够用一个命令创建新的 Frontity 应用程序。

要安装 Frontity CLI，请执行以下操作:

```
npm i frontity

npx frontity create my-frontity-app

```

接下来，为你的新网站选择一个主题。如果你刚开始接触 Frontity，火星主题是一个很好的选择，可以帮助你了解它是如何工作的。

您也可以通过输入您的电子邮件地址来订阅 Frontity 的时事通讯。

```
cd my-frontity-app

```

成功安装后，导航到您的新项目文件夹。您的 Frontity 项目将按如下方式构建。

```
my-frontity-app/
|__ node_modules/
|__ package.json
|__ frontity.settings.js
|__ favicon.ico
|__ packages/
    |__ mars-theme/

```

启动开发服务器就像输入以下命令一样简单。

```
npx frontity dev

```

一旦您的开发服务器启动，它将自动打开您的浏览器，您将看到一个设计精美的博客。服务器将监视对`packages`文件夹中的文件所做的任何更改。

下一步是[将你的 Frontity 应用程序连接到 WordPress 后端实例](https://docs.frontity.org/getting-started/connecting-to-wordpress)。要更详细地了解 Frontity 如何工作，请查看“[Frontity](https://blog.logrocket.com/getting-started-with-frontity/)入门”

## 前沿的 5 种选择

Frontity 在前端和后端分别使用 React 和 WordPress 构建应用程序，它对 markdown、Joomla 等其他内容和数据源的支持也很棒。是有限的。

让我们探索 Frontity 的一些替代方案，并查看一些用例，以帮助您为下一个 React 项目选择正确的解决方案。

### 1.盖茨比

Gatsby 是一个开源框架，旨在帮助你使用 React 库创建网站和应用。有了各种各样的插件，你可以构建具有性能、可伸缩性和安全性的优秀网站。

《盖茨比》是发展最快的框架。有了合适的插件，你可以用任何你想要的 CMS 来构建任何类型的网络应用或博客。虽然盖茨比可以和 WordPress 一起工作，但这不是它的重点。您需要处理一些额外的配置和工具来使它兼容。

盖茨比可以和 WordPress 一起工作，但是重点不在 WordPress 上。因此，一些额外的配置和额外的工具将由开发人员处理。

### 2.Nuxt.js

[Nuxt.js](https://nuxtjs.org/) 是一个开源框架，旨在增强 Vue 开发者的能力，简化开发者体验。

当基于你的 Vue 应用生成一个静态网站时，Nuxt 是一个很好的工具。您不需要构建自己的服务器，但是您仍然可以获得 SEO 的好处，因为 Nuxt 可以预呈现所有页面和必要的 HTML。

最棒的是，当 Nuxt 驱动你的前端，你享受 SSR 的好处时，你可以无缝地从 WordPress 发送你的内容。

这个[后端开发者社区博客](https://masteringbackend.com)是一个用 Nuxt.js 和 WordPress 构建网站的很好的例子。了解更多关于[如何在 JAMstack](https://www.smashingmagazine.com/2020/02/headless-wordpress-site-jamstack/) 上创建一个无头 WordPress 站点。

### 3.Next.js

[Next.js](https://nextjs.org/) 是为 React 开发者提供的[服务器端渲染框架](https://blog.logrocket.com/why-you-should-render-react-on-the-server-side-a50507163b79/)。它内置了许多功能，例如混合静态和服务器渲染、类型脚本支持、智能绑定、路由预取等等。

Next.js 与 Nuxt.js 相同，但用于 React 生态系统。它使您能够在一个地方享受所有的 SSR 和 SEO 优势。

像盖茨比一样，Next 可以与 WordPress 一起工作，但它并不是为这样做而设计的。这需要一些额外的配置和第三方工具。

### 4.网格体

[Gridsome](https://gridsome.org/) 让创建变得容易，[生成静态网站和应用](https://blog.logrocket.com/how-to-set-up-your-gridsome-app-to-use-typescript/)默认是快速的。

就我个人而言，我使用 Nuxt.js 进行服务器端渲染。也就是说，Gridsome 是我最喜欢的 Vue 静电发生器。我现在的作品集网站是用 Gridsome 搭建的；你可以在这里看一下[库。](https://github.com/Kaperskyguru/my-portfolio-gridsome)

Gridsome 使用来自不同来源的数据，如内容 API、WordPress 等 headless CMS 和其他 web 服务，使构建 JAMstack 网站变得简单而容易。你可以从包括 WordPress 在内的任何无头 CMS 上发布内容。

### 5.Wuxt

Wuxt 结合了 Nuxt 和 WordPress 的力量来帮助你构建令人敬畏的前端应用程序。Wuxt 为 Vue 生态系统带来了现成的 Nuxt 和 WordPress 集成。

由于 Wuxt 仍在开发中，Vue 开发者应该比 Frontity 更容易上手，因为他们会更熟悉 Vue 的结构。还有一个内置的 WordPress 插件，使得通过 REST 访问内容变得简单而轻松。

对于 Vue/Nuxt 开发者来说，这是 Frontity 的直接替代方案。虽然它仍在积极开发中，但你可以先看看官方库。

## 最后的话

在本指南中，我们回顾了如何使用 Frontity 通过 WordPress 和 React 构建网站，并介绍了流行库的五种可行替代方案。一如既往，最佳选择取决于您的项目需求和您的经验水平。

你更喜欢在你的 React/WordPress 项目中使用哪个框架？在评论里告诉我们，继续编码！

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。