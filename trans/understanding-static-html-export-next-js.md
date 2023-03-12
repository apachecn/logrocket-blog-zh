# 了解 Next.js - LogRocket 博客中的静态 HTML 导出

> 原文：<https://blog.logrocket.com/understanding-static-html-export-next-js/>

Next.js 在 React 世界中以作为动态应用程序的优秀工具而闻名，因为它的一系列特性和呈现方法，允许它处理几乎任何您扔给它的东西。

但是，静态 HTML 导出是一个鲜为人知且未被充分利用的特性(您必须深入到文档的“高级特性”一节才能找到)。

静态 HTML 就像它听起来的那样:一系列静态的 HTML 文件。它们不需要服务器或其他特殊产品/服务来运行或使用。服务器需要做的唯一工作是托管文件供其他人使用，仅此而已。

Next.js 使得从应用程序中导出静态 HTML 变得简单，事实上简单得可笑。在本文中，我们将了解为什么要从 Next.js 应用程序中导出静态 HTML，以及如何做到这一点。在这里，我们将讨论:

所以，让我们开始吧！

## 为什么使用静态 HTML？

有三个主要原因可以让你选择使用静态 HTML: SEO 的好处，改进的首次绘制时间，以及代码的可移植性。让我们更深入地了解一下其中的每一项。

### SEO 的好处

当您托管静态 HTML 文件时，所有的内容都已经预先生成为将发送给用户的 HTML。这意味着当搜索引擎优化爬虫访问你的站点来索引内容时，他们可以很容易地看到所有可用的内容并通过它工作。

如果我们将其与使用客户端渲染的传统 React 应用程序进行比较，并且内容没有预先生成为静态 HTML 文件，那么 SEO 爬虫将无法访问 HTML 中的内容，这意味着它无法索引您的站点。

这就是为什么从 SEO 的角度强烈建议尽可能使用静态站点生成(SSG)而不是客户端渲染。静态 HTML 对于 SEO 总是更好。

### 改进了首次喷漆时间

将内容生成为静态 HTML 的另一个好处是，在请求时不需要在服务器上进行呈现，在客户端收到代码后也不需要呈现。这意味着可以更快地向用户显示(或绘制)内容。

这通常被称为首次绘制时间，但是，为什么这很重要呢？除了更快的网站为你的用户提供更好的用户体验，第一次上色时间是另一个影响网站整体 SEO 分数的因素，所以你的网站上色越快，你的 SEO 就越好。

### 代码可移植性

最后，正如我们前面提到的，运行静态 HTML 文件不需要任何特殊设备。它们可以托管在任何地方，并且不需要运行活动的 Node.js 服务器来处理诸如服务器端呈现(SSR)或重定向之类的事情。

这意味着有了静态 HTML，你对特定服务或设备的依赖减少了，灵活性增加了，你可以在任何你希望的地方给你的主机你的代码。

## 在 Next.js 中使用静态 HTML 导出

如前所述，让我们看看如何从 Next.js 应用程序中导出静态 HTML，以及这样做有多容易。

Next.js 有一个内置的命令，用于从应用程序中导出静态 HTML 这个命令是`next export`。

当您运行这个命令时，Next.js 会从预先生成的静态文件中构建一个 HTML 版本的应用程序，并将它们复制到正确的目录中，以便您可以随时取走和使用。

您现在可能想知道，我如何预生成静态文件？这就是`next build`命令的用武之地。

`next build`为`next export`创建要捆绑的 HTML 文件。当你调用`next build`，`getStaticProps`和`getStaticPaths`为你的`pages`目录中的每一页生成一个 HTML 文件。请注意，如果您设置了动态路线，将根据需要创建更多页面。

因为`next export`和`next build`是如此紧密地联系在一起，所以在 Next.js 应用程序中处理静态 HTML 生成的一种常见方法是修改`package.json`文件中的`build`命令。默认情况下，它看起来像这样:

```
"scripts": {
  "build": "next build",
},

```

但是，我们可以修改它来处理静态 HTML 的生成和导出。为此，将上面的`build`脚本改为如下所示:

```
"scripts": {
  "build": "next build && next export",
},
<
```

现在，当我们调用`build`脚本时，HTML 构建、导出并准备使用。

## Next.js 静态 HTML 导出中支持的功能

当构建一个静态站点并从 Next.js 应用程序中导出静态 HTML 时，可能会有一些限制(而且，确实有)。

但是，您可能希望/需要构建静态站点的大多数核心 Next.js 特性都是受支持的，甚至在导出到静态 HTML 时也是如此。

这些功能包括以下内容:

*   使用`getStaticPaths`时使用动态路线
*   使用`next/link`预取
*   预加载 JavaScript
*   动态导入
*   任何样式选项(如 [CSS 模块](https://blog.logrocket.com/a-deep-dive-into-css-modules/)或`styled-jsx`)
*   客户端数据提取
*   `[getStaticProps](https://blog.logrocket.com/getinitialprops-vs-getserversideprops-nextjs/#getserversideprops-getstaticprops)`
*   `[getStaticPaths](https://blog.logrocket.com/the-latest-features-in-next-js/#getstaticpaths)`
*   使用自定义加载程序的图像优化

正如您所看到的，从 Next.js 应用程序导出静态 HTML 时，仍然支持您在 Next.js 中使用的许多常见功能，如动态路由、预加载、预取链接和客户端数据提取。

## Next.js 静态 HTML 导出中不支持的功能

然而，不幸的是，并不全是阳光和彩虹。因为我们将应用程序导出到静态 HTML 文件，所以在导出文件时，我们会丢失任何需要 Node.js 服务器运行的特性，或者任何在构建过程中无法完成的动态逻辑。

这包括如下功能:

## 结论

所以，就这样了！在整篇文章中，我们研究了 Next.js 中与静态 HTML 导出相关的所有内容，从它是什么到我们为什么要使用它，再到使用它时支持和不支持的特性。

总的来说，静态 HTML 导出是 Next.js 内一个很棒的特性；它允许我们使用 Next.js 的额外功能构建网站，并作为一个整体做出反应，但仍然可以获得用静态 HTML 文件托管网站的速度和 SEO 好处。

我希望这篇关于 Next.js 中静态 HTML 导出的文章对您有所帮助。如果你这样做了，[请考虑在 Twitter](https://twitter.com/MrConerMurphy) 上关注我，在那里我发布了关于 JavaScript 生态系统和整个 web 开发的有用且可行的技巧和内容。

或者，如果你不喜欢 Twitter，[访问我的博客](https://conermurphy.com/blog)了解更多我的内容。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。