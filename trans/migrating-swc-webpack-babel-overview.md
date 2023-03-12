# 迁移到 SWC:简要概述

> 原文：<https://blog.logrocket.com/migrating-swc-webpack-babel-overview/>

网络技术每年都在继续发展。现代编程语言需要现代的运行环境，尽管现代 web 浏览器通常支持很酷的新功能，但旧版本的浏览器不支持。

但是，如果 web 开发人员需要构建兼容新旧技术的网站，他们如何编写仍然可以在旧浏览器中运行的现代代码呢？

这就是 JavaScript 编译器发挥作用的地方。与其他语言的编译器类似，JavaScript 将现代 JS 代码编译成可以在旧浏览器中运行的代码，而不会损失功能。

## 使用 webpack 和 Babel 的缺点

Webpack 和 [Babel](https://babeljs.io/) 是捆绑现代 JavaScript 代码的标准工具。Babel 主要用于将现代 JavaScript 转换成旧版本，以便旧版本的浏览器能够支持。

使用这些工具的最大缺点是创建时间会变得很长，尤其是对于大型项目。因此，缩短构建时间可以简化您的项目——最终节省时间、金钱和开发人员的神经。

## 什么是 SWC？

一个提高 JavaScript 编译工具性能的项目是 SWC。这是一个用 [Rust](https://www.rust-lang.org/) 编写的编译器，据称比 [Babel](https://babeljs.io/) 快得多。例如，它的效率就是[为什么 Next.js 从 Babel 转到 SWC](https://nextjs.org/blog/next-11-1#adopting-rust-based-swc) 。

> 在早期的测试中，以前使用 Babel 的代码转换从**~ 500 毫秒下降到约 10 毫秒**，使用 SWC 的 Terser 的代码缩减从**~ 250 毫秒下降到约 30 毫秒**。总的来说，这使得**的构建速度是**的两倍。–next . js 变更日志，版本 11.1

所以，SWC 比 webpack 和 Babel 快得多，但是如果你已经用这些工具启动了你的项目呢？有可能从这种设置切换到 SWC 工具吗？

是的，它是！让我们看看怎么做。

## 从 webpack 和 Babel 迁移到 SWC

假设您有一个简单的 web 应用程序，其中包含一个 HTML 文件和一些 JavaScript 文件。这些文件与 webpack 捆绑在一起，JavaScript 文件用 Babel 编译。

应该如何进行捆绑，应该为哪种文件类型使用哪种编译器，以及许多配置选项都是在一个 webpack 配置文件中定义的，该文件可能类似于以下内容:

```
const path = require('path');  

module.exports = {  
  entry: './src/index.js',  
 output: {  
    filename: 'main.js',  
 path: path.resolve(__dirname, 'dist'),  
 },  
 module: {  
    rules: [  
      {  
        test: /\.?js$/,  
         exclude: /node_modules/,  
         use: {  
          loader: "babel-loader",  
         }  
      },  
 ],  
 },  
};

```

现在，如果你想使用 SWC 来捆绑你的文件[，而不是使用巴别塔](https://blog.logrocket.com/why-you-should-use-swc/)，你有两个选择。

你可以更换编译器，继续使用 webpack，也可以使用 SWC 自己的 bundler，名为 spack，可以完全取代 webpack，连同 Babel。

我们先从第二个选项开始。

## 用 SWC 的 spack bundler 完全取代 webpack 和 Babel

要完全取代 Babel 和 webpack，可以使用名为 spack 的[内置捆绑器。它将在您运行时自动安装:](https://swc.rs/docs/spack-basic)

```
npm i --save-dev @swc/core swc-loader

```

现在，类似于`webpack.config.js`文件，您需要在项目的根目录下创建一个名为`spack.config.js`的文件，其格式如下:

```
module.exports = {
    entry: { web: __dirname + "/src/index.js", },
    output: { path: __dirname + "/lib", },
};

```

你可以参考[官方文档](https://swc.rs/docs/spack-basic)来看看你能用它设置什么其他配置。

如果你想看真实生活中的例子，你可以访问 SWC 项目的 GitHub 页面，这里有一些简单的例子来最好地展示 spack 和 SWC 是如何工作的。

要构建您的项目，请在项目的根目录下运行`npx spock`。该脚本将自动获取配置，并使用 SWC 编译您的 JavaScript 代码，然后将其放入您定义的输出路径中。

> **注意:**在写这篇文章的时候，我不建议将 spack 用于生产项目，因为它仍在开发中。文档中缺少信息，许多特性，比如定制加载器，以及创建多个包的目前都不工作或者没有文档。

由于 spack 未完成的文档和开发，这个选项可能更安全也更容易，它使用 SWC [代替巴别塔作为编译器](https://swc.rs/docs/usage-swc-loader)。为此，您需要安装加载程序，如下所示:

```
npm i --save-dev @swc/core swc-loader

```

接下来，你要把`babel-loader`换成已经安装好的`swc-loader`。

如果您没有定义编译器，您可以简单地将以下内容添加到您的 webpack 配置中，而不是替换它。

```
module.exports = {  
  entry: './src/index.js',  
 output: {  
    filename: 'main.js',  
 path: path.resolve(__dirname, 'dist'),  
 },  
 module: {  
    rules: [  
      {  
        test: /\.m?js$/,  
        exclude: /(node_modules|bower_components)/,  
         use: {  
          // `.swcrc` can be used to configure swc  
             loader: "swc-loader"  
         }  
      }  
    ],  
 },  
};

```

就是这样！webpack 现在将使用快如闪电的 SWC 编译器代替 Babel 来编译你的代码。

## 使用 SWC 配置和优化项目

要使用定制配置，您可以创建一个`.swcrc`文件，您可以在其中定制您需要的任何配置。

如果你使用 [Terser](https://terser.org/) 来缩小你的输出文件，SWC 也为你提供了从头开始做同样的缩小的选项。

你可以在`.swcrc`文件中定义[这个选项](https://swc.rs/docs/config-js-minify)。大多数选项与 Terser 插件的选项相似，这意味着将 Terser 插件中的选项复制到`.swcrc`中的 minify 字段就足够了。

## 使用 SWC 的注意事项

当使用 spack 作为 webpack 和 Babel 的完全替代品时，有几个注意事项需要考虑。首先，您的项目不一定是生产就绪的，因为到目前为止，spack 的许多特性还没有准备好和/或缺失。

但是，如果您只使用编译器，您的项目可能会用于生产中。Next.js 11.1 现在使用它，并且已经表明它相当稳定。

尽管如此，我还是要小心，因为它还是新的，而且，尽管 Next 已经在使用它，他们正在与创建这个库的开发者密切合作。

值得注意的是，仍然有几个[未解决的问题](https://github.com/swc-project/swc/issues)，文档也不够详细，无法配置更高级的用例。

## 结论

如果在管道中完成，构建、捆绑和编译现代 web 应用程序可能会很乏味且成本高昂。SWC 通过简化捆绑和编译过程直面这一挑战。

此外，迁移 Babel 非常容易，到目前为止结果令人难以置信。鉴于这个项目很年轻，而且已经有了很大的潜力，我很高兴看到它的发展。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)