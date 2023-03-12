# 从 webpack 切换到包裹- LogRocket 博客

> 原文：<https://blog.logrocket.com/switching-to-parcel-from-webpack/>

在过去的一年中，您可能已经听说了一种叫做 Parcel 的新构建工具。

对于那些不知道的人来说，Parcel 将自己描述为*“一个 web 应用捆绑商，以其开发者经验而与众不同。它利用多核处理提供极快的性能，并且不需要任何配置。*

现在构建工具来了又去，webpack 和 Browserify 等工具仍在使用，那么为什么我们需要一个新的工具，为什么我要花时间写关于 package 的文章呢？

## 因为 webpack 很棒，但它对初学者并不友好

一开始，我不得不说，我认为 webpack 有很多很棒的用例，在 web 开发中肯定有它的位置。但是尽管 webpack 帮助简化了 web 开发过程，但它需要大量的初始设置。

大多数 web 开发人员通常只需要一个处理萨斯/SCSS、CSS 供应商前缀、巴别塔和代码精简器的设置。我们可能还希望在每个文件名的末尾添加散列，从而破坏缓存。哦，如果我们的构建工具也知道如何在开发过程中构建一个实时服务器，那么我会给你一些加分！

因此，即使大多数开发设置将总是(并且仅仅)使用前面提到的，当使用 webpack 时，我们总是必须设置它。这是包裹进入虚拟水的地方。

## 设置

为了演示这一点，我们将采用一个典型的 webpack 设置，它具有前面提到的所有插件，然后将其替换为 package。

准备好了吗？

让我们看一下我们的`webpack.config.js`文件，看看我们到底要转换什么:

```
module.exports = {
  entry: { main: './src/js/main.js' },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[chunkhash].js'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.s[c|a]ss$/,
        use: ['style-loader', MiniCssExtractPlugin.loader, 'css-loader', 'postcss-loader', 'sass-loader']
      }
    ]
  },
  plugins: [
    new CleanWebpackPlugin('dist', {}),
    new MiniCssExtractPlugin({
      filename: 'style.[contenthash].css',
    }),
    new HtmlWebpackPlugin({
      inject: false,
      hash: true,
      template: './src/html/index.html',
      filename: 'index.html'
    }),
    new WebpackMd5Hash()
  ]
};
```

为了完成这个设置，除了安装`webpack`和`webpack-cli`之外，我还必须安装以下依赖项:`autoprefixer`、`babel-core`、`babel-loader, babel-preset-env`、`clean-webpack-plugin`、`css-loader`、`extract-text-webpack-plugin`、`html-webpack-plugin`、`mini-css-extract-plugin`、`node-sass`、`postcss-loader`、`sass-loader`、`style-loader`和`webpack-md5-hash`。我还需要建立一个`postcss.config.js`文件，以便供应商前缀能够工作。

正如你所看到的，对于 2018 年的开发者来说，获得通常相当正常的设置涉及到相当多的配置。

## 开关

简单回顾一下，为了与我们的 webpack 设置保持一致，我们希望我们的包裹设置能够处理以下事情:

*   萨斯 Sass
*   巴别塔；
*   供应商前缀；
*   代码精简；
*   缓存破坏/散列；
*   现场服务器搅打。

那么，我们如何用 Parcel 实现完全相同的设置呢？

首先，我们必须通过在终端中运行下面的命令来安装包:`npm install -g parcel-bundler`。你会注意到我已经在全球范围内安装了它；这使我们可以运行 parcel 命令，而无需包含整个文件路径。

如果您愿意，您可以选择省略`-g`并将它安装在项目内部。我发现当我尝试使用本地版本时，我不得不在终端中运行确切的文件路径，因为输入`parcel`根本不起作用。相反，我不得不在终端运行`node node_modules/parcel-bundler/bin/cli.js`。

然后，我创建了一个初始项目结构，如下所示:

*/距离*

*/src/js/script.js*

*/src/SCS/styles . SCS*

*Index.html*

我进入项目根目录，在终端中运行`npm init -y`，以便创建一个`package.json`文件。

我在`script.js`和`styles.scss`中放了一些初始代码作为测试代码。没什么特别的:我在`script.js`创建了一个 const，看看它是否会被编译成 var，并写了一些非常基本的嵌套 SCSS 代码，看看它是否会被编译成 CSS。

因此，关于 Parcel 的奇妙之处在于，它将简单地查看您在`index.html`中拥有的任何脚本标签，并将传输它找到的代码。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

所以在我们的例子中，我在我的`index.html`中有如下内容:

```
<html>
  <body>
    <h1>Hello, Parcel!</h1>
    <script src="src/js/script.js"></script>
  </body>
</html>
```

然后我从终端运行`parcel index.html`，几秒钟后，Parcel 已经将一些文件编译到了`/dist`目录中，并在端口 1234 启动了一个本地服务器。我打开浏览器，进入`localhost:1234`，我的代码就在那里！我检查了编译后的脚本文件(它也有一个哈希后缀来处理缓存破坏),可以看到 const 确实被转换成了 var。

所以在我不需要配置任何东西的情况下，Parcel 已经整理出了一个本地服务器，使用 Babel 将 ES6 代码向下传输到 ES5，甚至负责文件散列！我也不必写任何特殊的东西来告诉 Parcel 输入和输出文件在哪里。这是非常甜蜜的！

后来我甚至发现，我们可以让 Parcel 打开您的默认浏览器并加载页面，只需在您的命令末尾添加`-open`(因此我们将改为运行`parcel index.html -open`)。

## 但是等一下，我的 CSS 文件呢？

嗯，我还没有引用过它，所以我想我们需要解决这个问题。

所以我必须安装`node-sass`才能让它工作。我在候机厅跑了`npm install node-sass`。然后，我在我的`script.js`文件的顶部添加了`import '../scss/styles.scss'`，运行`parcel index.html`，令我惊讶的是，package 把一切都整理好了。

我在我的 dist 文件夹中有一个单独的文件，用于 HTML、CSS 和 JS——它也用了 package 2.28s 来编译一切，这相当快！在 dist 版本的`index.html`中，Parcel 甚至在 CSS 文件中添加了一个链接标签，在最初的 src 版本中，我甚至都懒得对其进行排序！

我还应该注意到，据我所知，导入也可以使用普通的旧 CSS，所以如果我没有使用 SCSS，我可以导入普通的 CSS 并跳过`node-sass`的 npm 安装。

## 好吧，但是添加供应商前缀呢？

我在我的 SCSS 中添加了一个`::placeholder`标签，看看会发生什么，但是编译的 CSS 代码不包含任何前缀。原来这是 Parcel 在其设置中包含的另一个东西！我们所要做的就是在我们项目的根目录下包含一个`.postcssrc`文件(在我们的`index.html`文件旁边),并在其中包含以下内容:

```
{
    "plugins": {
        "autoprefixer": true
    }
}
```

我再次运行包`index.html`，检查输出的 CSS 文件，它现在有一堆厂商前缀样式用于`::placeholder`——哇！

在这一点上，您可能想知道 package 如何在没有`.babelrc`文件的情况下使用 Babel，因为我们需要一个`.postcssrc`文件来处理供应商前缀。事实证明，Parcel 是为 Babel 预先配置的，可以为市场份额超过 1%的浏览器编译代码。所以，如果你想找一个更小众的目标，你只需要一个`.babelrc`文件。

## 好了，这里的最后一个测试是代码缩减

到目前为止，我的代码还没有被精简，我想这是意料之中的，因为我还在开发环境中。让我们看看运行`parcel build index.html`会发生什么。

不会吧！HTML 缩小了，CSS 缩小了，JS 缩小了！一切都被缩小了！

因此，令人惊讶的是，package 已经处理了我们的 webpack 设置所处理的一切，并且只需要两部分配置:

1.  `npm install node-sass`
2.  一个`.postcssrc`文件

对我来说，这是一个不可思议的壮举，并且我可以看到这对于希望使用现代工具的新开发人员和想要快速设置或者已经厌倦了 webpack 的复杂性的有经验的开发人员来说是非常有用的。

## 结论

我喜欢包裹的功能。这感觉像是一个超级接近于零的配置设置，这对于加速开发过程来说是非常棒的，因为它让我专注于构建东西而不是配置设置。

它做我需要它做的一切，没有头痛。

在编译上也比 webpack 快很多！*我应该指出，我确实注意到我的 2015 款 Macbook Air 上的风扇不时发出声音，我只能想象这是由于使用多核处理的包裹——所以它比 webpack 构建得更快，但需要你的计算机做更多的工作。*

我想澄清的一件事是，这种“零配置”的想法总的来说有点误导。整个“零配置”概念的大部分只是简单地归结为在设置中抽象掉了多少东西。

默认情况下，webpack 试图尽可能地扩展，而默认情况下，Parcel 提供了许多最常用的特性。这对我们大多数人来说都是轻而易举的事。令人惊讶的是，这甚至是可能的，但我只是想阐明这一点，因为我认为理解我们正在使用的工具正在为我们做什么是超级重要的。

最后，你一定要给帕奇一个机会。但是现在还不要排除 webpack，如果你发现自己需要一个更少人走的路，它可能就是你的路线。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

* * *