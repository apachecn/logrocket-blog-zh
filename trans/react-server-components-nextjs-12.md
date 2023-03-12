# Next.js 12 - LogRocket 博客中的 React 服务器组件

> 原文：<https://blog.logrocket.com/react-server-components-nextjs-12/>

人们一直在努力为 React 应用提供一致的渲染解决方案。到 2020 年 12 月，React 团队引入了 React 服务器组件的概念——在本文中缩写为“RSC”——这是他们即将推出的版本 React 18 的一个功能。

RSC 是一个实验性的、选择加入的特性，旨在通过在服务器上呈现包括组件在内的所有内容来减少我们通常交给客户端的工作。这种方法产生零包大小的组件，从而缩短了 web 页面的加载时间。

Next.js 背后的公司 Vercel 一直在与 React 团队合作，推出他们的最新版本 Next.js 12，该版本将 React 18(包括 RSC)直接集成到框架中。[查看 Next.js 12 特性的完整列表](https://nextjs.org/blog/next-12)。

## 先决条件

对于本教程，您需要:

*   Node.js 的最新版本
*   Next.js 的基础知识
*   React 18 安装在您的项目中

*注意，* *React 18 还在 alpha 中，* *它* *还不适宜在刚刚生产时使用。此外，RSC 将不会与 React 18* *的第一个版本一起发布，并且只在某些框架中可用——如* *Next.js* *和**Hydrogen——打着实验的旗号。这篇文章只能作为我们期待稳定版本时的指南。*

在我们深入 RSC 之前，让我们看看 Next.js 提供的以前的预渲染解决方案

## 回顾过去

Next.js 介绍了几种预呈现内容的巧妙技术，包括:

### 服务器端呈现(SSR)

在服务器端呈现中，您的应用程序的数据在服务器上提取，并且为每条路线生成 HTML 页面并发送给用户。当收到时，用户的浏览器运行客户端 JavaScript 代码，使生成的 HTML 具有交互性，这就是所谓的水合作用。

这样，当你的用户第一次登陆你的页面时，他们就有内容可以看，而不是提供一个空白的白屏(*内心畏缩*–*骨架，至少？)同时获取外部数据——单页 React 应用程序就是这种情况。*

 *### 静态站点生成(SSG)

我们所知道的传统的基于 JavaScript 的网站实际上是静态网站。这里，编译和呈现网站的过程是在运行时在浏览器中完成的。Next.js 通过在构建时编译和呈现网站对此进行了改进。

输出是一堆静态文件、HTML 文件以及 JavaScript 和 CSS 等资产。与 SSR 类似，这种方法为您的用户预先呈现网站的内容，而不依赖于他们的互联网速度来在屏幕上显示内容。

Next.js 还有其他预渲染方法，比如

尽管取得了成功，SSR 和 SSG 都有他们的挫折。SSR 网站的托管成本很高，随着应用程序变大，SSG 会大幅增加构建时间。[选择前广泛阅读](https://frontend-digest.com/client-side-rendering-vs-server-side-rendering-vs-static-site-generation-2a0702cbb08d?gi=8064817c7490)。

这就是英国皇家莎士比亚剧团来帮忙的地方。让我们进入正题吧！

## React 服务器组件实际上是做什么的？

简单地说， [React 服务器组件](https://blog.logrocket.com/what-you-need-to-know-about-react-server-components/)是存储在服务器上的组件。因此，它们有两个主要优势:

*   直接访问后端/数据库，这使得获取数据更快
*   它们对应用程序的整体包大小没有任何影响。有了它，您甚至可以为您可能需要的任何功能导入大型库:

```
npm install largest-package
```

但是，因为 RSC 没有连接到浏览器，所以它们没有客户端交互性，因此不能管理状态。这意味着不支持像`useState`、`useEffect`和[这样的钩子以及其他一些 next . js API](https://nextjs.org/docs/advanced-features/react-18#unsupported-nextjs-apis)。

当与客户端组件和`Suspense`结合使用时，React 服务器组件——使用`.server.js`扩展创建——可以通过 HTTP 流预呈现内容。

[HTTP 流](https://www.pubnub.com/learn/glossary/what-is-http-streaming/)是一种推式数据传输技术，它允许 web 服务器通过一个保持无限开放的 HTTP 连接向客户端连续发送数据。这是在服务器和客户端之间传输动态数据的有效方法。

举个例子:

```
// my-rsc.server.js
import {db} from 'database'

// access backend 
const posts = db.posts.get(id);

return (
  <Page>
  <Suspense fallback={<Spinner/>}> 
      <BlogPosts posts={posts} />
    <Suspense/>
    <Footer />
  <Page/>
)

```

当点击这个路径时，组件呈现页面上的所有内容，同时显示一个回退组件`Spinner`作为`BlogPosts`的后台数据。返回的数据随后流入客户端组件`BlogPosts`。

本质上，`Suspense`阻止需要额外计算的子组件阻塞整个应用程序，从而让我们击败标准的 React 瀑布架构。

## 在 Next.js 12 中使用 React 服务器组件

可以通过在终端中运行以下命令来安装 Next.js 12:

```
npx create-next-app nextjs12-project

```

这将使用最新版本创建一个新的 Next.js 应用程序。

接下来，使用以下命令安装 React 18 的测试版:

```
npm install [email protected] [email protected] [email protected]

```

这将 React 版本更新到 18。

下载完成后，继续编辑位于文件夹根目录下的`next.config.js`文件，并添加以下代码:

```
// next.config.js
module.exports = {
  experimental: {
    concurrentFeatures: true,
    serverComponents: true,
  },
}

```

这个配置同时启用 React 服务器组件和[并发模式](https://reactjs.org/docs/concurrent-mode-suspense.html#what-is-suspense-exactly)。

并发模式使 React 应用程序能够保持响应，并根据用户的设备功能和网络速度进行调整。这个特点就是[悬念](https://reactjs.org/docs/concurrent-mode-suspense.html#what-is-suspense-exactly)的来源。

最后，创建一个包含以下内容的`pages/_document.js`文件:

```
// _document.js
import { Html, Head, Main, NextScript } from 'next/document'

export default function Document() {
  return (
    <Html>
      <Head />
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}

```

现在您已经准备好在您的应用程序中使用 RSC 了！

对于本教程，我将使用 Vercel 创建的 Hacker News 项目来解释 RSC 在 Next.js 12 中是如何使用的，而不是从头开始创建一个项目。[这里储存库](https://github.com/vercel/next-rsc-demo)。

打开终端，将存储库克隆到您的首选目录中:

```
[email protected] ~/Desktop/codebase/code
λ git clone https://github.com/vercel/next-rsc-demo.git

```

在文本编辑器中打开这个项目，让我们检查一下文件。我们将从`pages/index.js`开始:

```
// index.js
export default function Page() {
  return (
    <div className="container">
      {/* code ... */}
      <h1>React Server Components in Next.js</h1>
      {/* code ... */}
      <h2>React Server Components with Streaming</h2>
      <section>
        <a href="/rsc" target="_blank">
          RSC + HTTP Streaming
        </a>
      </section>
      {/* code ... */}
    </div>
  );
}

```

这个索引页面包含其他路线的链接，每个路线都显示相同的黑客新闻应用程序，但呈现方法不同。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们将重点关注`pages/rsc.server.js`中的 RSC 方法:

```
// rsc.server.js
import { Suspense } from 'react'

// Shared Components
import Spinner from '../components/spinner'

// Server Components
import SystemInfo from '../components/server-info.server'

// Client Components
import Page from '../components/page.client'
import Story from '../components/story.client'
import Footer from '../components/footer.client'

// Utils
import fetchData from '../lib/fetch-data'
import { transform } from '../lib/get-item'
import useData from '../lib/use-data'

function StoryWithData({ id }) {
  const data = useData(`s-${id}`, () => fetchData(`item/${id}`).then(transform))
  return <Story {...data} />
}

function NewsWithData() {
  const storyIds = useData('top', () => fetchData('topstories'))
  return (
    <>
      {storyIds.slice(0, 30).map((id) => {
        return (
          <Suspense fallback={<Spinner />} key={id}>
            <StoryWithData id={id} />
          </Suspense>
        )
      })}
    </>
  )
}

export default function News() {
  return (
    <Page>
      <Suspense fallback={<Spinner />}>
        <NewsWithData />
      </Suspense>
      <Footer />
      <SystemInfo />
    </Page>
  )
}

```

该组件以博客页面为特色，其中`NewsWithData`组件——负责获取帖子的组件——被包装在`Suspense`中:

```
<Suspense fallback={<Spinner />}>
  <NewsWithData />
</Suspense>

```

这里，`NewsWithData`使用了两个函数`fetchData`和`useData`，从 API 中获取所有帖子的`storyIds`。然后，它为每个帖子设计了一个`StoryWithData`组件。然后，
`StoryWithData`使用这些函数获取每个帖子的内容，并将其传输到`Story`客户端组件。

```
// rsc.server.js 
function StoryWithData({ id }) {
  const data = useData(`s-${id}`, () => fetchData(`item/${id}`).then(transform))
 // Story is client component imported in a server component
  return <Story {...data} />
}

```

`Story.client.js`在 UI 中显示帖子，并通过为每个帖子实现 upvotes 来为应用程序添加客户端交互性。

```
// story.client.js
// client components are regularreact components you're already familiar with

// client component can use state
import { useState } from 'react'

export default function Story({
  id,
  title,
  date,
  url,
  user,
  score,
  commentsCount,
}) {
  const { host } = url ? new URL(url) : { host: '#' }
  const [voted, setVoted] = useState(false)
  return (
    {/*代码...*/}
  )
}
```

与`pages/csr.js`中的客户端渲染方法相比，可以在项目上观察到 RSC 的现场效果。

在节流网络上，使用 CSR 的页面明显较慢，因为浏览器将数据获取与组件的水合作用结合在一起。[点击这里观看演示](https://youtu.be/WlP2TB2ORL4?t=445)。

## 结论

React 服务器组件是一个惊人的特性，因为它们有效地处理渲染，并让我们构建跨服务器和客户端的应用程序。组件现在可以更快地加载，因为大部分计算都从客户端转移到了服务器组件。RSC 的正式发布将为未来 React 应用程序的架构带来变化。

## 参考

由于这仍然是一个突破性的话题，请参考以下材料了解更多信息:

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)*