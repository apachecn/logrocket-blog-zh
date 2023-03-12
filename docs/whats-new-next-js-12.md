# Next.js 12 中的新内容

> 原文：<https://blog.logrocket.com/whats-new-next-js-12/>

## 介绍

目前，Next.js 是 React 生态系统中最受欢迎的框架，随着 Next 12 的发布，情况只会越来越好。

接下来的 12 个特性包括性能优化、React 18 支持、中间件等等。让我们在下一节了解这些特性。

## 新功能

Next.js 世界里有一堆很棒的特性。在本节中，我们将详细了解它们。让我们开始吧。

### Rust 编译器

Next 12 的关键特性之一是性能优化。为了提高性能，Next.js 用可扩展的 Rust 编译器取代了 Babel 编译器，该编译器利用了本地编译的优势。

根据文档，SWC 在单线程上比 Babel 快 20 倍，在四核上快 70 倍。

借助新的 Rust 编译器，Next.js 承诺优化捆绑和编译，本地刷新速度提高 3 倍，生产构建速度提高 5 倍。

其结果是更快的生产构建时间和本地开发中的即时反馈，从而带来更好的开发人员体验和更快的开发时间。

### 中间件

Next 12 最酷的特性之一是中间件。根据文档，中间件使我们能够使用代码而不是配置。这意味着我们可以在请求完成之前运行代码，并且基于请求，我们可以通过重写、重定向、添加头等来修改响应。

中间件并不是一个新概念。像 Express.js 这样的框架使用中间件来拦截 HTTP 请求，并在它到达路由处理器之前对其进行处理。Next.js 同样使用中间件来给我们充分的灵活性。

要在 Next 中创建一个中间件，在 pages 目录中创建一个`_middleware.ts`文件。在文件内部，导出一个名为中间件的函数，如下所示:

```
import {NextFetchEvent, NextRequest, NextResponse} from 'next/server'
export function middleware (req, event) {
  // your code 
}

```

此外，如果我们只想让中间件在页面的子集上运行，我们可以通过在页面目录的子目录中创建`_middleware.ts`文件来将中间件的范围扩大到子目录。

有了中间件，我们可以实现诸如[认证](https://github.com/vercel/examples/tree/main/edge-functions)、[僵尸防护](https://github.com/vercel/examples/tree/main/edge-functions)、[重定向和重写](https://github.com/vercel/examples/tree/main/edge-functions)、[服务器端分析](https://github.com/vercel/examples/tree/main/edge-functions)、[日志](https://github.com/vercel/examples/tree/main/edge-functions)、[处理不支持的浏览器](https://github.com/vercel/examples/tree/main/edge-functions)等等。

下一个中间件 API 扩展了本地 Web API 对象`FetchEvent`、`Response`和`Request`，以便在基于传入请求配置响应时给我们更多的控制和灵活性。

下面是中间件函数签名:

```
import type { NextFetchEvent, NextRequest } from 'next/server';

export type Middleware = (request: NextRequest, event: NextFetchEvent) => 
  Promise<Response | undefined> | Response | undefined;

```

`NextFetchEvent`是本机`FetchEvent`对象的扩展，并添加了`waituntil`方法，该方法可用于在响应发送后扩展中间件的执行。因此，使用`waituntil`方法，我们可以发送一个响应，并在中间件中继续后台工作。

当使用类似 Sentry 的工具在发送响应后发送响应时间和错误的日志时,`waituntil`方法会很有用。

`NextRequest`对象扩展本地`Request`对象，而`NextResponse`对象扩展本地`Response`对象。

此外，Next.js 中间件函数在呈现 pages 目录中的每个页面之前运行，它使我们能够访问和修改传入的请求。

因此，中间件为我们提供了一种更有效的方式来共享页面之间的逻辑，从而保持代码的简洁和高效。

例如，为了在每个页面上验证用户身份验证凭证，通常的模式是在每个页面上导入身份验证逻辑。然而，有了中间件，我们只需在中间件中定义我们的逻辑，它就会自动在每个页面上运行。

此外，Next 中间件与 Vercel Edge 功能协同工作。 [Edge Functions](https://github.com/vercel/examples/tree/main/edge-functions) 是像 AWS Lamda 和 Google Cloud Functions 这样的无服务器功能，它们像 CDN 一样部署到 Vercel 的 [Edge 网络](https://vercel.com/docs/concepts/edge-network/overview)。这使得我们的服务器端逻辑更接近原点；因此，我们的最终用户可以在零代码开始的情况下获得极快的执行速度。

传统上，为了提高速度，网络内容从 CDN 提供给最终用户。但是这些是静态页面，我们失去了动态内容。此外，我们使用服务器端呈现来呈现来自服务器的内容，以获得动态内容，但是我们损失了速度。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

但是，通过像 CDN 一样将我们的中间件部署到边缘，我们将我们的服务器逻辑移动到靠近访问者的起点。这样做的结果是我们得到了速度和动力。

### React 18 支持

React 18 还在测试中。然而，React 18 的功能，如服务器端暂停和自动更新补丁，现在可以在 Next.js 12 中以实验性的标志提供，因为 Next 准备在 React 18 走向稳定版本时为其做准备。

React 18 支持 React 服务器组件和并发模式特性，如服务器端暂挂和 SSR 流。

悬念是一个组件，它允许您在呈现其子组件之前等待异步数据。在 React 18 之前，服务器上没有悬念，但 React 18 内置了对服务器端悬念和 SSR 流的支持，从而使我们能够使用 HTTP 流在服务器上呈现页面。

在接下来的 12 个版本中，要获得此功能，您可以选择实验性的并发功能:

```
...
experimental: {
  concurrentFeatures: true,
}
...

```

请注意，要使上面的代码工作，请确保您安装了 React 的测试版:

```
npm install [email protected] [email protected] [email protected]

```

一旦这个实验性的并发特性被启用，SSR 将使用与[中间件](https://nextjs.org/docs/middleware)相同的 [Edge 运行时](https://nextjs.org/docs/api-reference/edge-runtime)。

这样做的结果是，我们可以使用基于 ES 2020 的悬念数据抓取、 [`next/dynamic`](https://nextjs.org/docs/advanced-features/dynamic-import) ，以及带有悬念边界的 React.lazy:

```
import dynamic from 'next/dynamic'
import { lazy } from 'react'

// These two methods are identical:
const Users = dynamic(() => import('./user'), { suspense: true })
const Footer = lazy(() => import('./footer'))

const Dashboard = () => {
  return (
    <div>
      <Suspense fallback={<Spinner />}>
        <Users />
      </Suspense>
      <Suspense fallback={<Spinner />}>
        <Footer />
      </Suspense>
    </div>
  )
}

export default Dashboard;

```

React 服务器组件允许您从服务器上的 React 组件本地呈现 HTML。它使用 HTTP 流在服务器上渐进地呈现网页。使用 React 服务器组件，您可以立即从 edge 函数流式传输 HTML，并在数据进入时逐步显示更新。

此外，我们可以在 React 服务器组件内部直接获取数据，而不需要使用`getStaticProps`或`getServerSideProps`。当呈现 React 服务器组件时，它们不需要任何客户端 JavaScript。这使得最终用户下载的千字节数更少，页面呈现速度更快。

在接下来的 12 个版本中，您可以通过配置您的`next.config.js`文件来选择加入该体验功能:

```
...
experimental: {
  concurrentFeatures: true,
  serverComponents: true,
}
...

```

为了在 Next 中创建一个 React 服务器组件，我们只需在组件的文件名后面添加`.sever.js`。此外，为了创建一个客户机组件，我们将`.client.js`添加到组件的文件名中。

考虑下面的代码:

```
// pages/home.server.js
import React, { Suspense } from 'react'

import Users from '../components/users.server'
import Cart from '../components/cart.client'

const Home = () => {
  return (
    <div>
      <h1>React Server Component Demo</h1>
      <Suspense fallback={'Loading...'}>
        <Users />
      </Suspense>
      <Cart />
    </div>
  )
}

export default Home;

```

在上面的代码中，`Home`和`Users`组件都是服务器组件，不会包含在客户端运行时中。
`Home`和`Users`都将始终在服务器端渲染并传输到客户端，但`Cart`仍将在客户端进行水合，就像普通的 React 组件一样。

注意，根据文档，没有“服务器/客户端”扩展的组件将被视为“通用组件”，可以由双方使用和呈现，这取决于它是从哪里导入的。

### ES 模块支持和 URL 导入

在版本 11.1 中，Next 增加了对 ES 模块的实验性支持，但是在版本 12 中，这是默认的。

ES Modules 是 JavaScript 的官方 ECMAScript 模块标准，它受到 Node.js 和所有主流浏览器的支持。

接下来的 12 个模块优先于普通模块。但是，它仍然支持导入使用 CommonJS 的 NPM 模块。这有助于开发人员逐步采用 ES 模块，而不会中断更改。

此外，Next 12 实验性地支持使用 es 模块的包的 URL 导入。这意味着一个包可以直接从一个 URL 导入，不需要安装或者单独的构建步骤。此外，这些包被缓存在本地以支持离线开发。
这样做的结果是 Next.js 可以像处理本地依赖一样处理远程 HTTP(S)资源。

当 Next 检测到 URL 导入时，它会生成一个`next.lock`文件来跟踪远程资源。Next 支持服务器和客户端 URL 导入。

要使用此功能，请在`next.config.js`中添加允许的 URL 前缀:

```
module.exports = {
  experimental: {
    urlImports: ['https://cdn.skypack.dev']
  }
}

```

和导入模块:

```
import confetti from 'https://cdn.skypack.dev/canvas-confetti'

```

任何服务于 es 模块的 CDN 都可以工作。例如 jsDelivr、JSPM 和 unpkg。

### Bot 感知的 ISR 回退

[增量静态再生](https://vercel.com/docs/concepts/next.js/incremental-static-regeneration)或 ISR，使我们能够在构建了我们的站点之后增量更新静态页面，而不需要重建整个站点。使用 ISR，静态页面是在运行时动态生成的，而不是在构建时。

使用 ISR 时，Next.js 使用从`getStaticPath`函数返回的路径确定静态站点生成要生成的页面。因此，如果我们返回 1000 篇被浏览最多的文章的路径，这些页面是在构建时生成的。

我们应用程序中的其他页面可以通过使用`fallback:blocking`或`fallback:true`按需生成。

`fallback:blocking`是首选，因为当对尚未生成的页面发出请求时，Next 将第一次服务器呈现该页面，并从缓存中为后续请求提供服务。

但是当使用`fallback:true`时，Next.js 会在第一次请求时立即以加载状态提供一个静态页面。当数据完成加载时，Next 将重新呈现页面并缓存数据。

然而，有了机器人感知的 ISR 回退功能，当使用`fallback: true`搜索机器人等[网络爬虫时，Next.js 将自动呈现 ISR 页面。但是 Next 仍然会向非 crawler 用户代理提供一个带有加载状态的静态页面。因此，这阻止了爬行器索引加载状态。](https://nextjs.org/learn/seo/introduction-to-seo/webcrawlers)

### 使用 AVIF 的较小图像

Next.js 内置的图像优化 API 现在支持 AVIF 图像。这使得图像比`WebP`小 20%。

这个特性是可选的，可以通过修改`next.config.js`文件中的`image.format`属性来启用:

```
module.exports = {
  images: {
    formats: ['image/avif', 'image/webp']
  }
}

```

### 输出文件跟踪

通过将 Vercel 的`@verce/nft`包带到 Next.js 12 中，这一点得到了改善。这样，Nextjs 可以自动跟踪每个页面和 API 路由所需的文件，并在输出旁边输出这些跟踪结果。这允许集成商自动利用 Next 提供的跟踪。

## 结论

Next 12 是一个彻底的游戏改变者，它将改变我们构建 React 应用程序的方式，使之变得更好。尽管一些令人惊叹的功能还不稳定，但在我们等待 React 18 的稳定发布时，您仍然可以选择加入并在您的应用程序中使用它们。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。