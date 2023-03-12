# 用 DLL 插件改进你的 webpack 构建

> 原文：<https://blog.logrocket.com/speed-up-your-webpack-build-with-the-dll-plugin/>

作为一名 JavaScript 开发人员，您可能有很多机会接触到 webpack，无论是在用 React 捆绑前端资产还是传输一些 TypeScript Node.js 代码时。

大多数情况下，您不必直接与 webpack 交互。相反，您作为构建工具的依赖项间接地与 webpack 交互。但是如果您开发这些构建工具，或者管理您自己的 webpack 配置，本教程将帮助您缩短构建时间。

我们将使用 DLL 插件，webpack 在其文档中承诺“极大地改善加载时间”。

## 它是如何工作的？

DLL 插件创建了两个东西:

*   一个`manifest.json`文件
*   不经常改变的一组模块

如果没有启用 DLL 插件，webpack 会编译您的代码库中的所有文件，不管它是否被修改过。这导致编译时间比需要的时间长。

但是有一种方法可以告诉 webpack 不要麻烦地重新编译几乎不变的库:例如，您的`node_modules`文件夹中的库。

这就是 DLL 插件的用武之地。它捆绑了您指定为很少更改的代码(例如，供应商库)，并且不再编译它们，极大地改进了构建时间。

DLL 插件通过创建一个`manifest.json`文件来做到这一点。该文件用于将导入请求映射到捆绑模块。当从其他包向一个模块发出导入请求时，webpack 检查在`manifest.json`文件中是否有一个条目指向该模块。如果是这样，它会跳过构建该模块。

## 概观

DLL 插件应该用于很难改变的代码包，比如你的供应商包。因此，您需要一个单独的 webpack 配置文件。在此了解如何创建供应商捆绑包[。](https://blog.logrocket.com/guide-performance-optimization-webpack/)

在本教程中，我们将使用两种 webpack 配置。这些将被命名为`webpack.config.js`和`webpack.vendor.config.js`。

`webpack.config.js`将是非供应商代码的主要配置；即经常修改的代码。

`webpack.vendor.config.js`将用于你不变的包，就像`node_modules`中的库。

要使用 DLL 插件，必须在适当的 webpack 配置中安装两个插件:

DllReferencePlugin → `webpack.config.js`
DllPlugin → `webpack.vendor.config.js`

我们将使用 webpack 版本 4.x，因为 5.x 仍处于测试阶段。但是，它们的配置相似。

### 配置 DLL 插件(`webpack.vendor.config.js`)

DLL 插件有以下强制选项:

*   `name`:这是 DLL 函数的名称。什么都可以叫。我们将称之为`vendor_lib`。
*   `path`:这是输出的清单 json 文件的路径。它必须是绝对路径。我们将把它存储在根目录下名为“build”的文件夹中。该文件将被称为`vendor-manifest.json`。

为了指定路径，我们应该像这样使用`path.join`:

```
path.join(__dirname, 'build', 'vendor-manifest.json')
```

在`webpack.vendor.config.js`文件中，确保`output.library`与 DLL 插件`name`选项相同。

包括任意多的入口点。在这个例子中，我包含了一些非常重要的库。使用这个插件时，你的输出文件夹无关紧要。

下面是`webpack.vendor.config.js`现在的样子:

```
var webpack = require('webpack')
const path = require('path');
module.exports = {
    mode: 'development',
    entry: {
        vendor: ['lodash', 'react', 'angular', 'bootstrap', 'd3', 'jquery', 'highcharts', 'vue']
    },
    output: {
        filename: 'vendor.bundle.js',
        path: path.join(__dirname, 'build'),
        library: 'vendor_lib'
    },
    plugins: [
        new webpack.DllPlugin({
            name: 'vendor_lib',
            path: path.join(__dirname, 'build', 'vendor-manifest.json')
        })
    ]
}
```

### 配置 DllReferencePlugin ( `webpack.config.js`)

DllReferencePlugin 有两个必填字段:

*   `context`:这是包含构建文件夹的目录的绝对路径。在本教程中，将其作为`__dirname`。
*   `manifest`:这是 DLL 的清单 json 文件的绝对路径。我们将把它设置为`path.join(__dirname, 'build', 'vendor-manifest.json')`。

你的`webpack.config.js`应该是这样的:

```
const webpack = require("webpack")
var path = require("path");
// const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
const SpeedMeasurePlugin = require("speed-measure-webpack-plugin");
const smp = new SpeedMeasurePlugin();
module.exports = smp.wrap({
  mode: 'development',
  entry: {
    app: ['./src/index.js']
  },
  output: {
    filename: 'main.bundle.js',
    path: path.join(__dirname, 'build')
  },
  plugins: [
    new webpack.DllReferencePlugin({
      context: __dirname,
      manifest: path.join(__dirname, 'build', 'vendor-manifest.json')
    }),
    // new BundleAnalyzerPlugin()
  ]
})
```

至此，我们完成了 DLL 插件的设置。

### 构建包

#### 生成 DLL `manifest.json`

您首先需要使用`webpack.vendor.config.js`配置运行 webpack，它会生成`webpack.config.js`工作所需的`vendor.manifest.json`。这个构建可以在每个开发会话开始时完成，当它的配置发生变化时，或者当供应商捆绑包中的库版本发生变化时。

将这个脚本添加到您的`package.json`文件中。它将创建清单 json 文件和供应商包:

```
"scripts": {
    "buildVendor": "webpack --config webpack.vendor.config"
}
```

在随后的代码更改中，您只需使用`webpack.config.js`。

#### 构建主包

然后为主包添加一个构建脚本:

```
  "scripts": {
    "buildVendor": "webpack --config webpack.vendor.config",
    "build": "webpack --config webpack.config.js"
  }
```

## 基准

为了测试这个插件，我在`src/index.js`文件中实例化了一个简单的 Vue.js 应用程序。它将导入一些重要的依赖项:

```
import Vue from "vue"
import lodash from 'lodash'
import 'react'
import 'angular'
import 'bootstrap'
import 'd3'
import 'jquery'
import 'highcharts'
export default function createApp() {
  // vendor()
  const el = document.createElement("div")
  el.setAttribute("id", "app")
  document.body.appendChild(el)
  console.log("hello")
  new Vue({
    el: "#app",
    render: h => h("h1", "Hello world")
  })
}
document.addEventListener('DOMContentLoaded', () => {
  createApp()
})
```

为了导入由 webpack config 创建的两个包，我们需要将以下脚本标记添加到`index.html`头中:

```
<head>
  <title>Webpack DllPlugin Test</title>
  <script src="/build/vendor.bundle.js"></script>
  <script src="/build/main.bundle.js"></script>
</head>
```

使用[speed-measure-web pack-plugin](https://www.npmjs.com/package/speed-measure-webpack-plugin)测试包给出了以下基准:

规格:i5-6200U 8gb 内存 windows 10

**使用 DllPlugin(平均 3 次构建)**
构建厂商捆绑:
* 3370 毫秒

建筑主束:
146.6ms

**无 DllPlugin(平均 3 次构建)**
构建供应商捆绑:
3312 毫秒

建筑主包:
3583.6ms

假设您只在编码会话开始时构建了供应商包，并且在一个会话中重新加载了一百次，下面是您将花费在等待上的总时间:

**带 DllPlugin**
3370+(146.6 * 100)= 18030 ms

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

**无 DllPlugin**
3312+(3583.6 * 100)= 361672 ms

这意味着构建时间减少了 95%!带来难以置信的生产率提高。

## 结论

这种优化不会以任何方式应用到您的产品构建中。它只缓存指定的包来加速开发构建。

查看 [GitHub repo](https://github.com/scroobius-pip/webpackdlltutorial) 获取教程代码。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)