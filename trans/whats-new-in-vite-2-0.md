# Vite 2.0 中的新功能- LogRocket 博客

> 原文：<https://blog.logrocket.com/whats-new-in-vite-2-0/>

[Vite](https://github.com/vitejs/vite) 是前端的构建工具。它提供了一个快速和自以为是的构建工具，使用插件提供高度可定制的 API。

Vite 在内部使用 [Rollup.js](https://rollupjs.org/guide/en/) 进行捆绑。它是平台无关的，这意味着它通过模板支持许多流行的前端库，包括 React、Vue.js、Preact 和 vanilla JavaScript。

这个非捆绑 JavaScript 环境的最新版本提供了许多令人兴奋的新特性。Vite 2.0 于 2021 年 2 月 16 日发布，具有完全重新设计的架构、新的插件系统、开箱即用的一流 CSS 支持等等。

在本指南中，我们将[向您介绍 Vite](https://blog.logrocket.com/getting-started-with-vite/) 并仔细看看 Vite 2.0 附带的一些主要变化。

以下是我们将要介绍的内容:

## Vite 是什么？

Vite 是一个构建工具，最初是为 Vue.js 开发的。随着新的更新，Vite 现在支持大多数 web 框架。

Vite 通过[本地 ESM](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) 提供源代码。有条件的动态导入只有在当前屏幕需要导入时才被处理。浏览器承担捆绑源代码的工作。Vite 只在浏览器请求时按需提供和转换源代码。

## Vite 是如何工作的？

在 [ES2016](https://developers.google.com/web/shows/ttt/series-2/es2015) 中首次引入 ES 模块时，浏览器对 [ES6 模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)的支持普遍较差。现在许多现代浏览器都支持本地 ES 模块，您可以本地使用`import`和`export`语句，使用脚本标签中的`type+"module"`属性指定您正在导入一个模块，就可以在 HTML 中包含导入:

```
<script type="module" src="filename.js"></script>

```

源代码中的 ES 导入语法直接提供给浏览器。任何支持原生`<script module>`的浏览器都会自动解析它们，然后为每次导入发出 HTTP 请求。dev 服务器拦截来自浏览器的 HTTP 请求，并在必要时执行代码转换。

这使得 Vite 服务器非常快:冷启动时钟大约为[140 毫秒，而 Vue-CLI 为 1900 毫秒](https://www.getrevue.co/profile/vuenewsletter/issues/180-say-hi-to-vite-a-brand-new-extremely-fast-development-setup-for-vue-so-fast-it-feels-instant-242032)。

## Vite 2.0 中的新特性

现在我们已经了解了 Vite 是什么以及它是如何工作的，让我们更详细地看看 Vite 2.0 中的新特性，并探索最新版本如何帮助改进整体开发人员体验。

### 更快的构建

使用`esbuild`，Vite 2.0 的构建速度更快。 [`esbuild`](https://github.com/evanw/esbuild) 是用围棋写成的 bundler。它比其他捆扎机快 10-100 倍。

Vite 2.0 使用`esbuild`将 CommonJS 模块转换为 ESM 进行依赖。根据 [Vite 2.0 发行说明](https://vitejs.dev/blog/announcing-vite2.html) [s](https://vitejs.dev/blog/announcing-vite2.html) ，使用`esbuild`而不是 Rollup 会导致构建时的巨大性能提升。目前，`esbuild`用于预绑定依赖项，但是 Vite 团队计划在未来完全转移到`esbuild`。

### 框架无关的

Vite 2.0 为许多流行的框架提供了高质量的样板文件，包括 Vue.js、React、Preact 等等。它还提供了一个普通的 JavaScript 样板文件。支持类型脚本的现成模板有 React 和 Typescript、Vue 和 TypeScript 等。

鉴于其与框架无关的特性，Vite 有助于促进跨框架的统一工具体验。

### 新插件系统

Vite 的新插件系统通过识别构建类型、访问配置和开发服务器配置(仅举几个例子)来改善开发人员的体验。它兼容许多现成的 Rollup.js 插件。

新的插件系统公开了 API 来为开发服务器添加中间件，并使用定制的热模块重载处理。插件系统是基于 WMR。新系统通过提供 Vite 特定的操作扩展了 Rollup 插件系统。

Vite 现在有了对 SSR 的实验性支持。它支持针对 [Vue 3](https://blog.logrocket.com/new-features-in-vue-3-and-how-to-use-them-2/) 和 React.js 的 SSR，Vite 提供 API 和构造来高效地加载和更新基于 ESM 的源代码，并自动外部化 CommonJS 兼容的依赖项。

Vite SSR 是很低级的功能；该团队的目标是为更高层次的特性提供工具。

在产品构建中，SSR 可以与 Vite 完全分离。它还可以使用相同的设置支持预渲染。

### 改进的 CSS 支持

Vite 2.0 引入了新的 CSS 特性，比如 CSS 拆分、URL 重新定位等等。Vite 支持开箱即用的特性，不需要任何配置。使用 Vite 的解析器增强了 CSS 中的`@import`和`url()`路径，以考虑别名和 npm 依赖性。

## 使用 Vite 2.0 构建 React 应用程序

简单来说，Vite 2.0 看起来很惊艳。让我们通过构建一个非常基本的、基本的 React 应用程序来测试 Vite 2.0。

首先使用 Vite 2.0 样板文件的 React 模板:

```
yarn create @vitejs/app my-react-app --template react-ts

```

文件夹结构如下:

![Vite20 React Folder Structure](img/99834d290da807618e83005c8acf55a6.png)

Vite 2.0 in React folder structure

现在，您的 Vite、React 和 TypeScript 样板文件已经准备好了。

您可以使用`npm i`或`yarn`命令安装依赖项。安装完依赖项后，使用`yarn dev`命令启动开发服务器。

![Dependencies Installed Start Server Yarn Command](img/efc4529086defdb702af504617d033cf.png)

## 结论

Vite 最初是作为 Vue.js 的开发服务器，但随着时间的推移，它演变成了一个成熟的前端工具解决方案。Vite 2.0 提供了一个自以为是的 web 开发工具。在生产过程中使用`esbuild`进行开发人员构建和汇总捆绑可以增强开发人员的整体体验。可靠的开箱即用配置使 Vite 成为您下一个开发项目的可靠解决方案。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。