# Next.js 9.2 中的新特性

> 原文：<https://blog.logrocket.com/whats-new-in-next-js-9-2/>

在本文中，我们将分析最新版本的 Next.js 附带的所有新的闪亮特性。

## Next.js 是什么？

Next.js 自我标榜为 React 框架，用于静态页面、渐进式 web 应用、移动 web 应用、SEO 友好页面，尤其是服务器端渲染。它只需要一行命令就可以实现静态导出，并且附带了一个名为 JSX 的 CSS-in-JS 库。它还包括代码分割、通用渲染和热重装等功能。

根据最新的“[JavaScript 状态](https://blog.logrocket.com/state-of-javascript-what-are-the-most-in-demand-frontend-frameworks-in-2020/)”调查，Next.js 社区在过去 12 个月里大规模增长，留存率从 6%飙升到 24%[。愿意学习 Next.js 的新人数量也增加了近 10%。](https://2019.stateofjs.com/back-end/nextjs/)

让我们来详细看看框架最新版本附带的一些最值得注意的新特性: [Next.js 9.2](https://nextjs.org/blog/next-9-2) 。

## 对全局样式表的内置 CSS 支持

版本 5.0 中提供了使用扩展 Next.js 行为的`next-css`插件导入 CSS 的功能。随着时间的推移，下一个团队从使用该框架的公司那里得到了很多关于`next-css`插件的反馈。

作为回应，该团队决定将该插件作为核心 Next 产品的一部分。尽管该插件以前在处理导入时受到限制——例如导入的文件决定了整个应用程序的全局样式，而不是局限于组件级别——但下一个团队开发了一个解决方法。要开始在您的应用程序中使用 CSS 导入，您可以在`pages/_app.js`中导入 CSS 文件。

考虑下面的样式表，名为`styles.css`，位于项目的根中。

```
body {
  padding: 20px 20px 60px;
  margin: 0;
}

```

如果不存在，创建一个`[pages/_app.js](https://nextjs.org/docs/advanced-features/custom-app)`文件。然后，导入`styles.css`文件。

```
import '../styles.css'
// This default export is required in a new `pages/_app.js` file.
export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />
}

```

由于样式表本质上是全局的，所以必须将它们导入到自定义的 [`<App>`](https://nextjs.org/docs/advanced-features/custom-app) 组件中，以避免全局样式的类名和排序冲突。这使得当您在开发环境中编辑它们时，您的样式可以反映在您的应用程序中。

在生产中，所有的样式表将被缩小成一个`.css`文件，并通过下一个服务的`index.html`文件中的链接标签加载。这个特性是向后兼容的，如果您已经用另一个库实现了这个特性，这个特性在默认情况下是禁用的，以避免冲突。

## 对组件级样式的内置 CSS 模块支持

旧的`next-css`插件的另一个问题是，所有的`.css`文件要么被处理为全局样式，要么被处理为局部样式，并且没有同时启用这两种样式的选项。在这个新版本中，现在支持 CSS 模块，因此您可以同时使用全局 CSS 和 CSS 模块。

使用 CSS 模块，您可以通过类名在本地确定 CSS 的范围，并将其导入到应用程序中的任何位置，以实现范围或组件级样式。例如，考虑一下`components/`文件夹中的一个可重用的`Button`组件。

首先，用下面的内容创建`components/Button.module.css`。

```
/*
You do not need to worry about .error {} colliding with any other `.css` or
`.module.css` files!
*/
.error {
  color: white;
  background-color: red;
}

```

然后，创建`components/Button.js`，导入并使用上面的 CSS 文件。

```
import styles from './Button.module.css'
export function Button() {
  return (
    <button
      type="button"
      // Note how the "error" class is accessed as a property on the imported
      // `styles` object.
      className={styles.error}
    >
      Destroy
    </button>
  )
}
>
```

在这个版本中，CSS 模块是选择性加入的，只对扩展名为`.module.css`的文件启用；仍然支持普通的链接样式表和全局 CSS 文件。这个特性是向后兼容的，如果您已经用另一个库实现了这个特性，那么这个特性在默认情况下是禁用的，以避免冲突。

## 改进的代码分割策略

要加载 Next.js 应用程序，必须加载五个固定的 JavaScript 包来启动 React:主 js 文件、一个公共 JS 文件、下一个运行时包、Webpack 运行时包和动态导入。代码分割有助于优化加载所有这些文件的过程。

下一个团队最初采用的代码分割策略是针对 commons bundle 的。这是一个使用率启发式策略，以确保如果一个模块在超过一半的页面中被使用，它将被标记为一个模块；否则，它将被捆绑。虽然该团队发现这种方法是有益的，但随着时间的推移，他们意识到它可以进一步优化流程。

新策略允许您优化多个文件的公共块，包括当涉及许多页面类型时。这是从这个版本开始的默认流程。

新的分块实现利用 [HTTP/2](https://developers.google.com/web/fundamentals/performance/http2) 来提供更多更小的块。在新的启发下，无数的组块被创建用于各种目的:

*   每页的最小块
*   包含 React、ReactDOM、React 的调度器等的框架块。
*   超过 160kb 的任何`node_module`依赖项的库块(pre-minify/gzip)
*   所有页面使用的代码的公共块
*   尽可能多的共享块(由两个或更多页面使用)，优化应用程序的整体大小和初始加载速度
*   Next.js 客户端运行时
*   Webpack 运行时

## 包罗万象的动态路线

在下一个 9.0 版本中引入了动态路段。目标是在不使用定制服务器的情况下简化 Next.js 中的动态段。这个特性已经被广泛采用，下一个团队一直在尝试尽可能地优化它。

以前，动态路由并不涵盖所有路由。在新版本中，您现在可以通过使用`[...name]`语法来使用无所不包的路由。当您拥有由内容源(如 CMS)定义的嵌套结构时，这尤其有用。

例如，`pages/post/[...slug].js`将匹配`/post/a`、`/post/a/b`、`/post/a/b/c`等等。

`slug`在路由器查询对象中作为单个路径部分的数组提供。所以对于路径`post/foo/bar`，查询对象将是`{ slug: ['foo', 'bar'] }`

## 如何开始使用 Next.js 9.2

通过升级当前版本，您可以立即开始使用新版本。

```
npm i [email protected] [email protected] [email protected]

```

## 结论

Next 社区已经显示出令人印象深刻的增长数字，其近 900 名贡献者、 [44，000 多名 GitHub 明星](https://github.com/zeit/next.js)、大量[示例目录](https://github.com/zeit/next.js/tree/canary/examples)和 13，800 名成员的[频谱论坛](https://spectrum.chat/next-js?tab=posts)就是证明。随着团队继续专注于改善开发人员体验和优化下一个产品，这些数字将稳步增长。

Next 9.2 你最喜欢的功能是什么？

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。