# 如何将 Next.js 应用到您现有的应用程序中

> 原文：<https://blog.logrocket.com/how-to-adopt-next-js-into-your-existing-application/>

[服务器端渲染](https://blog.logrocket.com/why-you-should-render-react-on-the-server-side-a50507163b79/)，也被称为 SSR，对前端开发者社区产生了如此巨大的影响，React 宣布计划正式[将服务器组件](https://blog.logrocket.com/what-you-need-to-know-about-react-server-components/)引入该库。您可能想知道这有什么大惊小怪的，以及为什么人们会选择 SSR 而不是 React 主要使用的常规客户端渲染。

SSR 有几个好处:

*   从 SEO(搜索引擎优化)中获得更好的结果，因为搜索引擎更容易抓取关键字的 SSR 渲染站点，并显示在相关内容的 SERPs(搜索引擎结果页面)上
*   SMO(社交媒体优化)的优势源于在社交媒体平台上分享时显示预览
*   比客户端呈现的应用程序加载更快，使 SSR 站点响应更快，并提供更好的用户体验

这些优势导致了 Jamstack 架构的兴起，该架构旨在通过利用预渲染(在构建期间在服务器上编译资产)来提供更快、更一致的 web 体验。

Next.js 是 React 社区中最受欢迎的 SSR 包之一，它允许开发人员在使用 React 构建网站时利用 SSR 的优势。

Next.js 通过一个名为 Create Next App 的包提供了一个类似于 Create React App 的零配置设置过程。今天，我们将看看如何在现有的 React 应用程序上使用 Next.js。这将允许您将 SSR 添加到现有项目中。

## 迁移过程

为了向您展示这种迁移是如何完成的，我们将从[以前的教程](https://blog.logrocket.com/how-to-build-react-components-for-codebases-that-use-javascript-and-typescript/)中迁移这个[项目](https://github.com/austinroy/react-ts-component-demo.git)，以使用 Next.js

第一步是克隆 repo，您可以使用 SSH、HTTPS 或新的 GitHub CLI 来完成:

```
//SSH
git clone [email protected]:austinroy/react-ts-component-demo.git
//HTTPS
git clone https://github.com/austinroy/react-ts-component-demo.git
//GitHub CLI
gh repo clone austinroy/react-ts-component-demo

```

用`yarn`命令安装依赖项，用`yarn start`运行应用程序。你应该在你的浏览器上看到一些类似于这个沙盒的基本星球大战信息。

### 更新脚本和依赖关系

这个应用程序是使用 Create React App 设置的，它使用`react-scripts`作为它的脚本运行器。您需要用`next`来替换它。

从项目中卸载`react-scripts`并添加`next`:

```
yarn remove react-scripts

yarn add next

```

我们使用 React Router 进行页面导航，但是 Next.js 使用了一种不同的路由方法，我们也将讨论这一点。您还需要卸载`react-router-dom`:

```
yarn remove react-router-dom

```

我们的下一步是在`package.json`文件的脚本部分更新任何使用了`react-scripts`的脚本来使用`next`:

```
...
"scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  },
...

```

`next dev`在`localhost:3000`上运行开发服务器，`next build`编译应用的生产就绪版本，而`next start`运行应用的生产版本。

最后，将`.next`加到`.gitignore`上。

### 路线和链接

正如我们上面提到的，Next.js 处理路由和页面链接的方法与 React Router 和`react-router-dom`完全不同。它有一个基于文件系统的路由器，构建在 pages 的[概念之上，其中一个页面是从`pages`目录中的`.js`、`.jsx`、`.ts`或`.tsx`文件导出的 React 组件。](https://nextjs.org/docs/basic-features/pages)

为了实现，我们需要创建一个`pages`目录，并将应用程序中的所有`Route`组件转换成`pages`中的文件。

```
mkdir pages && touch pages/index.tsx pages/WithoutTS.jsx

```

现在让我们创建我们的主页。将以下代码添加到您的`pages/index.tsx`文件中:

```
import { DisplayWithTS } from '../src/pages/WithTS'
const TSComponent = () => {
    return(
      <div className="App">
          <a href="/">With TS</a>
          <br/>
          <a href="/WithoutTS">Without TS</a>
          <br/>
          <br/>
          <DisplayWithTS />
      </div>
    )
  }

  export default TSComponent

```

为了演示 JavaScript 组件，我们将构建 WithoutTS 页面。将以下代码添加到`pages/WithoutTS`文件中:

```
import { DisplayWithoutTS } from '../src/pages/WithoutTS'
const JSCOmponent = () => {
    return(
      <div className="App">
          <a href="/">With TS</a>
          <br/>
          <a href="/WithoutTS">Without TS</a>
          <br/>
          <br/>
          <DisplayWithoutTS />
      </div>
    )
  }

  export default JSCOmponent

```

### 式样

与创建 React 应用程序类似，`next`允许您将样式导入到组件中。然而，在 Next.js 中，它们需要作为 [CSS 模块](https://nextjs.org/docs/basic-features/built-in-css-support)来处理。

但是，您可以通过在`pages`目录中创建一个定制的`_app.js`文件来添加全局样式表:

```
touch pages/_app.js

```

将样式导入`_app.js`并添加以下代码:

```
import '../src/App.css'

export default function App({ Component, pageProps }) {
   return <Component className="App" {...pageProps} />
}

```

创建`_app.js`后，您需要重启 dev 服务器来加载更改。执行此操作并检查您的浏览器；现在，您已经完全复制了代码沙箱中应用程序的先前外观。就这样，你的应用程序现在用 Next.js 呈现在服务器端了，非常好！

### 清除

现在，您可以从`src`目录中删除 Create React App 生成的文件，如`App.tsx`、`App.test.tsx`、`index.ts`、`index.css`和`serviceWorker.ts`，因为 Next.js 现在处理这些文件正在做的一切。

您还可以根据自己的喜好稍微调整一下文件夹结构，但是要记住，Next.js 在这样做的时候使用了基于文件系统的路由机制。

## 其他需要调查的事情

现在，您已经准备好开始将您的旧项目迁移到 Next.js。然而，对于更复杂的项目，您可能需要更深入地研究。

*   对`window`、`localStorage`、`navigator`和其他[Web API](https://developer.mozilla.org/en-US/docs/Web/API)的访问——这些通常是现成的客户端渲染应用程序可用的，但由于 Next.js 使用预渲染，您将只需要在您的 SSR 应用程序的客户端访问它们。一种方法是使用`useEffect`挂钩
*   图像优化——虽然我们的演示应用程序中没有图像，但是大多数 web 应用程序都会在其中的某个地方使用图像资源。Next.js 提供了一个定制的`Image`组件。 [`next/image`](https://nextjs.org/docs/api-reference/next/image) 是 HTML `<img>`元素的扩展，为现代 web 而进化。当浏览器支持时，它为调整大小、优化和提供像 [WebP](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Image_types) 这样的现代格式的图像提供构建时优化
*   环境变量–`.env`与 Create React App 一样，Next.js 也支持变量或秘密。然而，您需要将前缀`REACT_APP_`改为`NEXT_PUBLIC_`,以使它们在构建时可用
*   对于 SEO 来说，Next.js 提供了一个内置的组件，可以将标签添加到应用程序页面的`head`中的`[next/head](https://nextjs.org/docs/api-reference/next/head)`下。

总的来说，Next.js 是目前最好的 SSR 选项之一。在应用程序中使用服务器端渲染对用户体验都有好处，比如更快的加载速度，以及通过增加与 SEO 和 SMO 的兼容性来增加曝光度。

将 Next.js 添加到您已经存在的 React 应用程序中是一个相当简单的过程，可以很好地了解前端 web 开发的未来，让许多开发人员对即将到来的 React 服务器组件感到兴奋。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。