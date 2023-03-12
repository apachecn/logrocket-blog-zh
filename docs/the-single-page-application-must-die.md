# 单页应用程序必须死-日志火箭博客

> 原文：<https://blog.logrocket.com/the-single-page-application-must-die/>

*免责声明*这里的观点很大程度上是我自己的，而不是 LogRocket 的观点。

另一个免责声明是，在过去的十年里，我几乎只从事各种各样的单页应用程序。

## SPA 之旅(单页应用程序)

单页应用程序的一个可能定义是:

> 单页应用程序是只需要在 web 浏览器中加载一个页面的 web 应用程序。

我对单页应用程序的定义是任何只依赖客户端渲染(CSR)的应用程序。

对高度交互式用户界面(UI)的日益渴求导致越来越多的 JavaScript 代码被推送到浏览器。Javascript MV*框架是从杂乱无章的代码库中发展出来的，旨在拨乱反正。

[Backbone.js](https://backbonejs.org/) 是第一个 JavaScript MV*框架，它打开了通往地狱的闸门，让大量的 JavaScript 被发送到浏览器并被浏览器解析。这导致浏览器中运行的 JavaScript 从 REST API 调用的 JSON 响应而不是服务器呈现动态 HTML。臭名昭著的 loading spinner 现在从原始沼泽中脱颖而出，在 web 开发的历史时间线上占据了一席之地。

紧跟在 Backbone.js 之后的是街区新成员 [EmberJS](https://emberjs.com/) 、 [AngularJS](https://angularjs.org/) 和当前的热门人物 [React](https://reactjs.org/) 。今天，使用 JavaScript MV*框架可能比不使用更常见，因为我们希望我们的 web 应用程序像它们的桌面应用程序一样运行。

我不打算列出通常对 SPA(单页应用程序)的抱怨，包括 SEO、性能问题和代码复杂性。我相信这些问题有可行的解决方案，比如为网络爬虫提供不同的内容，为性能问题进行代码分割。

## 渐进增强是平衬

> 构建适合每个人的网络

我对单页面应用程序的主要问题是，它们通常不会使用[渐进增强](https://www.gov.uk/service-manual/technology/using-progressive-enhancement)开始使用。

渐进增强曾经是阿杜流行的概念，但 SPA 的兴起使其停滞不前，因为开发人员更愿意处理只有现代浏览器才允许的崭新而闪亮的世界。发展中国家网速慢的用户或者某些辅助技术的用户怎么办？我们睁一只眼闭一只眼，以确保我们的简历保持相关性。

如果您使用 React、Angular、Ember 或其他什么工具中的 CLI 工具创建一个新的 SPA，那么您是在假设您正在处理一个乌托邦世界。该代码预计将运行在一个现代化的浏览器上，该浏览器运行在一个高速网络上，具有各种功能。

渐进增强的广义定义是:

> 渐进式改进是一种网页设计策略，首先强调核心网页内容。然后，在最终用户的浏览器/互联网连接允许的情况下，这种策略会在内容之上逐步添加更细致、技术上更严格的呈现层和功能。—维基百科

这意味着我们从最小的分母开始，并添加 JavaScript 等增强功能，而不是从一个前提开始，即[服务工作者](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)将充当代理并缓存内容以供重复访问。

如果我们想要更广泛的浏览器和设备网络，那么我们需要确保我们第一次访问一个站点时，第一个页面请求是服务器呈现的，最好是来自同构的 web 应用程序。

如果我们采用这种方法，那么我们的网站可以在禁用 JavaScript 的情况下工作，这是渐进增强的圣杯。

我们还应该使用与渐进式 web 应用程序(PWA)相关的技术，稍后会详细介绍。

## React 应用程序中的服务器端呈现(SSR)与客户端呈现(CSR)

我将使用 React 作为示例框架来概述这两种类型的呈现之间的差异。

主要区别在于，对于服务器端呈现(SSR ),服务器对浏览器的响应是准备好呈现的页面的 HTML，而对于客户端呈现(CSR ),浏览器得到的是一个相当空的文档，其中包含指向 JavaScript 和 CSS 的链接。

在这两种情况下，React 都需要下载，并经历构建虚拟 DOM 和附加事件以使页面交互的相同过程——但对于 SSR，用户可以在所有这些发生的同时开始查看页面。对于 CSR 世界，您需要等待上述所有情况发生，然后将虚拟 DOM 移动到浏览器 DOM，以便页面可见。

服务器端渲染的性能优势被夸大了，并被歪曲成事实，就像一个政客在被揭露时使用的一样。

## 单页应用程序和渐进式 web 应用程序

PWA 是一个 web 应用程序，它使用现代 web 功能向用户提供类似应用程序的体验。前面的定义是一个非常空泛的解释，但我认为任何申请要成为 PWA，都必须满足以下三个标准:

*   使用 HTTPS 服务(安全)
*   拥有一个包含最少图标的有效 web 清单文件
*   向获取事件处理程序注册一个服务工作器，并提供最低限度的脱机支持

## 应用程序外壳模型

出于某种原因，许多人认为渐进式网络应用程序(PWA)是单页应用程序(SPA ),因为他们经常使用谷歌推广的[应用程序外壳模型](https://developers.google.com/web/fundamentals/architecture/app-shell)。

应用程序的外壳在*应用程序外壳模型的上下文中*是为渐进式 web 应用程序的用户界面提供动力所需的最少的 HTML、CSS 和 JavaScript，并且是确保可靠的良好性能的组件之一。

```
<!doctype html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="theme-color" content="#000000">
  <link rel="shortcut icon" href="/favicon.ico">
  <title>My PWA</title>
</head>

<body>
  <div id="root"></div>
</body>

</html>
```

第一次加载应该很快，并立即缓存。**缓存**意味着 shell 文件通过网络加载一次，然后保存到本地设备。用户随后每次打开应用程序时，shell 文件都会从本地设备的缓存中加载，这导致启动速度极快。

如果你用 [create-react-app](https://github.com/facebook/create-react-app) 创建一个新的应用程序，那么 [workbox npm 包](https://github.com/GoogleChrome/workbox)也会被安装，它是一个渐进式 web 应用程序的库集合。workbox 生成的 index.html 是一个基本的 HTML 文件，其中包含 webpack 在构建时添加的 JavaScript 脚本标签和 CSS 链接标签。

这种方法依赖于主动缓存 shell(使用[服务工作者](https://developers.google.com/web/fundamentals/primers/service-workers)让应用程序运行。接下来，使用 JavaScript 为每个页面加载动态内容。一个应用程序外壳模型导致极快的重复访问和类似本机的交互。

由`create-react-app`生成的代码仅由客户端渲染。没有服务器为第一次加载生成完整的 HTML 请求。我们期待代码运行在具有现代功能的现代浏览器上。这个世界没有进步的想法。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 混合方法将渐进式增强添加到渐进式 web 应用程序中

这两种方法都有一定的优势，所以最佳方法是利用两者的优势。

如果您恰当地使用了服务器端呈现，那么服务器最初应该用一个完整的 HTML 文档来响应任何导航请求，其中包含特定于所请求的 URL 的内容，而不是一个简单的应用程序外壳。

不支持服务工作者的浏览器可以继续向服务器发送导航请求，服务器可以继续用完整的 HTML 文档响应它们。

下面是一个渲染函数，我用它来服务器渲染 React 组件。我正在使用[可加载组件](https://loadable-components.com/) `ChunkExtractor`来加载足够的 JavaScript 和 CSS，用于使用代码分割的特定 URL。

```
export async function render({ req, res }: RendererOptions): Promise<void> {
  const extractor = new ChunkExtractor({
    entrypoints: ['client'],
    statsFile,
  });

  const context: StaticRouterContext = {};

  const html = renderToString(
    extractor.collectChunks(
      <StaticRouter location={req.url} context={context}>
        <Routes />
      </StaticRouter>,
    ),
  );

  res.status(HttpStatusCode.Ok).send(`
    <!doctype html>
    <html lang="en">
      <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <meta httpEquiv="X-UA-Compatible" content="IE=edge" />
        <meta charSet="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        ${extractor.getStyleTags()}   
      </head>
      <body>
        <div id="root">${html}</div>
        ${extractor.getScriptTags()}
      </body>
    </html>
`);
}
```

在第一次加载时，会呈现一个完整的 HTML 文档，即使禁用了 JavaScript，它仍然可以工作。

一旦第一次加载完成， [react-router 的浏览器路由器](https://reacttraining.com/react-router/web/guides/quick-start)接管导航的控制，并有效地触发客户端渲染。

```
import React from 'react';
import { Routes } from '../../routes';
import { BrowserRouter } from 'react-router-dom';

export const App: React.FC = () => (
  <BrowserRouter>
    <Routes />
  </BrowserRouter>
);
```

## 服务人员呢？

这种加载内容的方法使用的混合策略不依赖于服务人员，因此即使不支持服务人员的浏览器也可以从实现中受益。

对于支持服务人员的浏览器，我们仍然可以利用应用程序外壳模型。每当用户触发应用程序内部的导航时，服务工作人员拦截 fetch 事件上的请求，并将响应添加到[缓存](https://developer.mozilla.org/en-US/docs/Web/API/Cache)。下一次导航到同一个 URL 被触发时，服务人员可以从缓存中加载内容并立即交付，而无需通过网络。

服务人员为所有导航请求返回相同的应用程序外壳 HTML 文档。

## 服务人员实施

为了让应用程序外壳工作，我们需要让服务人员缓存一个通用的应用程序外壳 HTML 文件。我们可以在服务器上配置一个类似`/app-shell`的特殊路径来返回一个骨架 HTML 文件，让服务工人在安装服务工人的时候取回来。

我使用 webpack 和 [workbox-webpack-plugin](https://developers.google.com/web/tools/workbox/modules/workbox-webpack-plugin) 来生成服务工作者配置文件。

下面是一个服务人员模板文件的缩小版本。

```
self.__precacheManifest = [].concat(self.__precacheManifest || []);

// active new service worker as long as it's installed
workbox.clientsClaim();
workbox.skipWaiting();

// suppress warnings if revision is not provided
workbox.precaching.suppressWarnings();

// precahce and route asserts built by webpack
workbox.precaching.precacheAndRoute(self.__precacheManifest, {});

// return app shell for all navigation requests
workbox.routing.registerNavigationRoute('/app-shell');
```

在上面的代码中，`self.__precacheManifest`变量存储了所有需要预缓存的 URL。

对`workbox.precaching.precacheAndRoute()`的调用告诉服务工作者在其安装过程中获取并缓存所有这些 URL，并使用缓存的版本来服务所有未来匹配的请求。

`workbox.routing.registerNavigationRoute('/app-shell');`指示服务人员，每当有新 URL 的导航请求时，不返回该 URL 的 HTML，而是返回以前缓存的 shell HTML 文件。

我们所需要的只是在我们的 express 应用程序中返回应用程序外壳框架的路径:

```
app.use('/app-shell', (req, res) => {
  res.status(HttpStatusCode.Ok).send(`
    <!doctype html>
    <html lang="en">
      <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <meta httpEquiv="X-UA-Compatible" content="IE=edge" />
        <meta charSet="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
      <!-- css link tags -->
      </head>
      <body>
        <div id="root"></div>
       <!-- js script tags -->
      </body>
    </html>
  `);
});
```

令我惊讶的是，这种模式并不普遍。我认为这是开创性的。

## 收场白

单页应用程序让渐进式改进退居二线。JAMstack 和其他类似的框架对渐进式增强视而不见，对我来说这是一种倒退。我们将旧设备视为向后兼容。网络经常被吹捧为人人共享，但在这个世界上并非如此。

遵循应用程序外壳模型的渐进式网络应用程序速度极快，但前提是你使用的浏览器支持服务人员。使用从同构的 JavaScript 应用程序呈现完整的 HTML 文档，然后让服务人员介入，这是我们应该努力的方向。我们还没有进入乌托邦，但我们可以给境况不佳的进步增强运动注入一些活力。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)