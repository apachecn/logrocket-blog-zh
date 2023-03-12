# 清除未使用的 CSS 代码

> 原文：<https://blog.logrocket.com/purgecss-remove-unused-css-code/>

CSS 框架是预先准备好并随时可以使用的样式表的集合。开发人员选择 CSS 框架，以更直观、更高效、更符合标准的方式交付数字体验。

然而， [CSS 框架也会带来问题](https://blog.logrocket.com/top-utility-first-css-frameworks/)。你不太可能使用 CSS 框架中提供的每一个特性，这意味着未使用的代码将会在你的最终应用程序中留下。未使用的代码会导致更大的文件大小，损害性能，很容易丢失对每个项目的跟踪，并导致优化问题。

删除不必要的代码将使你的网站加载更快，因为浏览器将要求和解析更少的代码。在本教程中，我们将探索 [PurgeCSS](https://purgecss.com/) ，一个用于移除未使用的 CSS 代码的工具。将 PurgeCSS 作为我们开发工作流程的一部分，我们可以轻松地删除未使用的 CSS，从而产生更小的 CSS 文件，并从整体上改善我们的应用程序。我们开始吧！

## 为什么应该使用 PurgeCSS？

虽然 PurgeCSS 不是删除未使用的 CSS 的唯一工具，但由于其模块化、易用性和广泛的定制选项，它脱颖而出。

### 模块性

模块化使开发人员能够为特定的用例及框架创建模块提取器。提取器是一个函数，它读取文件的内容并提取所使用的 CSS 选择器列表。

PurgeCSS 提供了一个非常可靠的默认提取器，可以处理多种文件类型。然而，默认情况下，PurgeCSS 忽略包含特殊字符的未使用的 CSS 代码，如`@`、`:`和`/`。因此，PurgeCSS 可能不适合您正在使用的确切文件类型或 CSS 框架。

### 广泛的定制选项

PurgeCSS 有一个[广泛的选项，允许你根据自己的需要定制](https://purgecss.com/configuration.html#options)它的行为。例如，PurgeCSS 包括`fontFace`、`keyframes`、`extractors`、`css`等选项。定制可以提高采购的性能和效率。

### 使用方便

PurgeCSS 非常容易上手，并且包含完整的文档。在撰写本文时，PurgeCSS 凭借在 npm 和 [6.5k GitHub stars 上](https://github.com/FullHuman/purgecss)[190 万的周下载量受到开发者的喜爱。](https://www.npmjs.com/package/purgecss)

## 入门指南

首先，打开您的终端并运行以下命令，使用 create-react-app 安装 React:

```
npx create-react-app purgecss-tutorial

```

接下来，进入我们刚刚创建的`purgecss-tutorial`目录:

```
cd purgecss-tutorial

```

现在，继续安装 PurgeCSS 及其依赖项:

```
npm i --save-dev @fullhuman/postcss-purgecss glob-all purgecss-webpack-plugin

```

打开您的`App.js`文件并粘贴以下代码:

```
import React from 'react';
import "./App.css";

function App() {
  return <div className="App"></div>;
}

export default App;

```

在上面的代码中，我们创建了一个名为`App`的功能组件，并返回一个带有`App`的`classname`的`div`。

我们的`App.css`保持不变，因此它包含以下未使用的 CSS 代码:

```
.App {
  text-align: center;
}

.App-logo {
  height: 40vmin;
  pointer-events: none;
}

@media (prefers-reduced-motion: no-preference) {
  .App-logo {
    animation: App-logo-spin infinite 20s linear;
  }
}

.App-header {
  background-color: #282c34;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-size: calc(10px + 2vmin);
  color: white;
}

.App-link {
  color: #61dafb;
}

@keyframes App-logo-spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

```

打开您的`package.json`文件，并在脚本下添加下面一行:

```
"postbuild": "purgecss --css build/static/css/*.css --content build/index.html build/static/js/*.js --output build/static/css"

```

`post`是一个前缀，可以添加到任何 npm 脚本中，并在您运行主脚本时自动运行。在我们的例子中，`postbuild`在`build`脚本执行之后运行。

由`postbuild`执行的命令包含三个选项。选项指定 PurgeCSS 应该处理哪些 CSS 文件。它可以是文件名数组或 [globs](https://github.com/isaacs/node-glob/blob/master/README.md#glob-primer) 。`--content`选项类似于`--css`选项，指定 PurgeCSS 应该分析什么内容。`--output`选项指定了你应该将净化后的 CSS 文件写入哪个目录。默认情况下，它将结果放在控制台中。

本质上，由`postbuild`执行的命令做了以下事情:

1.  检查`build/static/css`中的每个 CSS 文件
2.  匹配文件中使用的选择器并移除任何未使用的 CSS
3.  在`build/static/css`中输出新的 CSS 文件

最后，弹出 Create React 应用程序以公开由原始 Create React 应用程序提供的 webpack 配置。弹出之后，我们将通过添加以下代码以及其余的导入来修改`config/webpack.prod.conf.js`文件:

```
// import PurgeCSS webpack plugin and glob-all
const PurgecssPlugin = require('purgecss-webpack-plugin')
const glob = require('glob-all')

```

在插件列表中的`new ManifestPlugin(...)`之前，添加以下代码:

```
    new PurgecssPlugin({
      paths: [paths.appHtml, ...glob.sync(`${paths.appSrc}/**/*`, { nodir: true })]
    }),

```

webpack 插件用一个路径数组指定 PurgeCSS 应该分析的内容。

要确认是否成功，请打开`build/static/css`中的 CSS 文件。输出看起来像下面的代码，只包含使用的 CSS:

```
body{margin:0;font-family:-apple-system,BlinkMacSystemFont,"Segoe UI","Roboto","Oxygen","Ubuntu","Cantarell","Fira Sans","Droid Sans","Helvetica Neue",sans-serif;-webkit-font-smoothing:antialiased;-moz-osx-font-smoothing:grayscale}.App{text-align:center}@keyframes App-logo-spin{0%{transform:rotate(0deg)}to{transform:rotate(1turn)}}

```

## 采购须知

PurgeCSS，以及其他一些 CSS 优化工具，如 PostCSS 和 cssnano，可以从 CSS 文件中去除注释。您可以指出在最终的 CSS 中保留哪些选择器是安全的。

有两种方法可以实现这一点，PurgeCSS 选项`safelist`或特殊的 CSS 特殊注释。

在我们的例子中，我们将直接在 CSS 文件中添加一个特殊的 CSS 注释。PurgeCSS 使用`/* purgecss start ignore */`和`/* purgecss end ignore */`来安全列出一系列规则。为了防止我们的评论被删除，我们添加了一个感叹号来告诉 PurgeCSS 这很重要。请参见下面的示例:

```
/*! purgecss start ignore */
h1 {
  color: pink;
  font-size: 2rem;
}
/*! purgecss end ignore */

```

在 PurgeCSS v2.0 之前，默认情况下会删除未使用的字体和关键帧代码。然而，当这些特性使用不当时，代码就会崩溃。默认情况下，未使用的字体和关键帧代码现在保持不变。您可以通过将`keyframes`和`font-faces`选项设置为`true`来改变这种默认行为。

## 结论

在本文中，我们探讨了 PurgeCSS，这是一个从代码中删除未使用的 CSS 的工具，从而减少文件大小并提高优化。我们讨论了 PurgeCSS 的主要产品，包括它的模块化、定制选项和易用性。然后，我们回顾了开始使用 PurgeCSS 所需的步骤以及需要考虑的一些注意事项。

即使你决定使用像 TailwindCSS、Bootstrap、MaterializeCSS 或 Foundation 这样的 CSS 框架，PurgeCSS 也应该可以完美地工作。我希望你喜欢这篇文章！

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。