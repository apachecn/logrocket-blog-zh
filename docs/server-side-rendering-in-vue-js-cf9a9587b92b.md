# Vue.js 中的服务器端渲染

> 原文：<https://blog.logrocket.com/server-side-rendering-in-vue-js-cf9a9587b92b/>

![](img/97ecae5141f8547562c38e0930a3bd17.png)

### 介绍

Vue.js 是使用起来最简单的 JavaScript 框架之一，事实上，我称之为轻量级冠军。一旦熟悉了 JavaScript 的基础知识，就很容易学会 Vue.js 并开始构建丰富的 web 应用程序。

但使用任何 JavaScript 框架的一个缺点是，在浏览器执行应用程序的 JavaScript 包之前，页面是不可见的。

例如，如果你看一下下面的代码，当你加载一个 Vue.js 应用程序时，你会发现你看到的只有`元素。在 Vue.js JavaScript 代码运行之前，不会填充内容。`

 ````
<html>
  <head> … </head>
  <body>
    <div id=app></div> 
  </body>
  <script src=”dist/build.js”></script>
</html>
```

这意味着用户必须等待更长时间才能看到任何东西。如果爬虫不能快速看到页面的内容，也会影响 SEO。

为了解决这些问题，我们可以做**服务器端渲染(SSR)** ，这是服务器在服务请求之前运行 Vue.js 代码*的地方，这样 HTML 一到达客户端就被填充到页面上。*

在本教程中，我将向您展示如何在 Vue.js 应用程序中启动和运行 SSR。

### 服务器端渲染入门

我们将从 [vue-cli 的](https://github.com/vuejs/vue-cli) [webpack-simple](https://github.com/vuejs-templates/webpack-simple) 模板开始，为我们提供一个 vue 模板脚手架。

如果您没有安装`vue-cli`，您可以通过运行以下命令来安装:

```
npm install -g vue-cli
```

安装完`vue-cli`后，我们通过运行以下命令创建一个新项目:

```
#Create the project 
vue init webpack-simple ssr-vue 
cd ssr-vue  
# Install dependencies
npm install
```

我们还需要服务器端渲染所需的其他三个包，它们是:

*   `Express`:对于服务器
*   `vue-server-renderer`:渲染捆绑包
*   我们将使用它来合并两个网络包包

我们可以通过运行以下命令来安装它们:

```
npm i express vue-server-renderer webpack-merge
```

现在我们已经准备好为 SSR 配置我们的 Vue 应用程序

接下来，我们需要在`src`文件夹中创建一个名为`server.js`的文件，并将以下内容粘贴到其中:

> 使用 JSFiddle 代码编辑器在线测试您的 JavaScript、CSS、HTML 或 CoffeeScript。

在上面的代码块中，我们注意到该代码与`src/main.js`中的代码非常相似。唯一不同的是，在这里，我们添加了上下文，然后返回一个承诺。

这是因为这是我们希望 webpack 为服务器运行和返回预渲染调用而准备的脚本。

### 创建 webpack 服务器配置

接下来，我们需要配置 webpack 来准备和编译我们的 Vue 应用程序，以便它可以被服务器执行。在最后一步中，我们已经创建了`server.js`文件。在这一步中，我们将配置 webpack 来编译文件及其依赖项，同时返回一个可执行包。

让我们在根文件夹中创建一个名为`webpack.server.config.js`的新文件，并添加:

> 使用 JSFiddle 代码编辑器在线测试您的 JavaScript、CSS、HTML 或 CoffeeScript。

这里我们已经定义了代码的入口路径，也定义了文件的输出应该存储在哪里。

接下来，我们还要求 webpack 对结果包进行压缩和丑化。

### 更新我们的 npm 脚本

我们已经创建了用于创建可执行服务器包的 webpack 配置。但是，我们需要指定一个命令，使 npm 能够运行这个 webpack 配置。

为此，让我们将`package.json`的脚本部分替换为:

```
"scripts": {
        "dev": "cross-env NODE_ENV=development webpack-dev-server --open --hot",
        "build": "cross-env NODE_ENV=production webpack --progress --hide-modules",
        "build-server": "cross-env NODE_ENV=production webpack --config webpack.server.config.js --progress --hide-modules"
    },
```

在上面的代码中，我们会注意到添加了一个名为`build-server`的额外命令。这个命令将通过使用`webpack.server.config.js`文件调用 webpack 来构建我们的可执行服务器包。

现在，让我们通过运行以下命令来构建我们的普通包和服务器包:

```
//build client/browser javascript bundle
npm run build
//build executable server bundle 
npm run build-server
```

### 创建服务器入口点

对于服务器，我们将使用`Node js`。这解释了为什么我们之前安装了 Express。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们在根文件夹中创建一个名为`entry.js`的文件，并复制以下内容:

> 使用 JSFiddle 代码编辑器在线测试您的 JavaScript、CSS、HTML 或 CoffeeScript。

在上面的代码中，我们有:

需要将成为应用程序框架的`Express`库

*   需要`fs`库，我们将使用它来同步读取文件
*   需要`path`库，这在处理文件路径时会很有帮助。
*   使用`fs`库将`bundle`变量设置为我们的`server.js`文件(可执行服务器包)的流内容
*   使用将`rendered`变量设置为我们的服务器包的解析和呈现值。
*   将我们的 index.html 文件的内容流式传输到一个名为`index`的变量中。
*   将`Express`实例实例化为一个名为`app`的变量。
*   使用`/dist`路径作为静态文件路径，并将其设置为从`dist`文件夹提供服务
*   在通配符路径中，我们调用了`renderer.renderToString`函数，该函数将 javascript 执行为字符串，并返回结果输出。
*   我们现在已经用结果输出替换了`的出现。`
`*   现在，如果我们启动我们的应用程序，并访问相应的 URL，看起来什么都没有改变。`

 `但是，如果我们看一下我们的页面源代码，我们会发现我们生成的 HTML 是:

使用 JSFiddle 代码编辑器在线测试您的 JavaScript、CSS、HTML 或 CoffeeScript。

> Test your JavaScript, CSS, HTML or CoffeeScript online with JSFiddle code editor.

我们在这里注意到元素`<div id=app>`现在已经用它需要的全部元素填充了它的全部`innerHTML`。这是更好的搜索引擎优化和网络爬虫解析。此外，它还消除了页面加载后的等待时间。

结论

### 在这篇文章中，我们看到了如何在 Vue.js 应用程序中设置服务器端渲染，以及它为什么有用。如果您想阅读上面显示的完整代码示例，您可以查看这个包含整个项目的 GitHub 资源库。

此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个标签页或窗口中注销。重新加载以刷新您的会话。重新加载以刷新您的会话。

> You can’t perform that action at this time. You signed in with another tab or window. You signed out in another tab or window. Reload to refresh your session. Reload to refresh your session.

像用户一样体验您的 Vue 应用

## 调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

Modernize how you debug your Vue apps - [Start monitoring for free](https://lp.logrocket.com/blg/vue-signup).``