# benchmarking bundlers 2020:roll up vs . Parcel vs . web pack-log rocket 博客

> 原文：<https://blog.logrocket.com/benchmarking-bundlers-2020-rollup-parcel-webpack/>

捆绑器是所有现代 web 应用程序的基础技术，或者更具体地说，是所有 JavaScript 应用程序的基础技术。随着前端世界随着更多客户端渲染应用的发展，关于如何有效捆绑大量 JS 的想法开始出现。

从认知上来说，随着选项数量的增加，选择变得困难。在这里，我们将分析当今顶级捆扎机的技术和非技术能力，使您的决策变得简单而明智。

我们将涵盖:

为了比较技术能力，我们选择了 [React 脸书像素](https://github.com/zsajjad/react-facebook-pixel)作为一个库，以及一个非常基本的 [React 应用](https://github.com/zsajjad/bundlers-comparison)作为样本，对这些捆绑器进行基准测试。

这种比较并不是要从这些伟大的工具中选出一个赢家；相反，它是为了帮助你更容易做出决定。所有这些捆绑器绝对是由伟大的人管理的伟大的工具，它们在某种程度上都是超级棒的。所有的维护者、贡献者、赞助者和支持者，干杯🍻

## 配置

在前端世界中，配置一个包是最受诅咒也是最复杂的领域之一。对于小规模的应用程序，人们可能会觉得这应该非常简单。尽管如此，随着应用程序规模的增长，我们需要更复杂的配置来保持我们的应用程序高效和高性能。

我们目睹了开发者之间的许多争论，关于为一个小应用程序配置现代技术堆栈是多么乏味。这些争论和随后被大多数社区采用的通用模式导致许多捆绑商提供零配置解决方案。

尽管几乎所有的捆绑器都声称它是零配置的，但对它们中的任何一个来说都是不可能的。它更多的是关于快速配置和尽可能舒适地保持配置指南。

所有这些 bundlers 都在这个地区有他们的红色和蓝色。在这里，我们分享为 React 脸书像素生成分发包的配置。它将让您对这些捆扎机的外观有所了解。

### 网络包

```
const path = require('path');
const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
  entry: ['./src/index.js'],
  output: {
    path: path.join(__dirname, 'dist'),
    filename: 'fb-pixel-webpack.js',
    libraryTarget: 'umd',
    library: 'ReactPixel',
  },
  module: {
    rules: [
      {
        use: 'babel-loader',
        test: /\.js$/,
        exclude: /node_modules/,
      },
    ],
  },
  resolve: {
    extensions: ['.js'],
  },
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        terserOptions: {
          warnings: false,
          compress: {
            comparisons: false,
          },
          parse: {},
          mangle: true,
          output: {
            comments: false,
            ascii_only: true,
          },
        },
        parallel: true,
        cache: true,
        sourceMap: true,
      }),
    ],
    nodeEnv: 'production',
    sideEffects: true,
  },
};

```

### 卷曲

```
import babel from '@rollup/plugin-babel';
import { nodeResolve } from '@rollup/plugin-node-resolve';
import { terser } from 'rollup-plugin-terser';
import filesize from 'rollup-plugin-filesize';
import progress from 'rollup-plugin-progress';
import visualizer from 'rollup-plugin-visualizer';

export default {
  input: 'src/index.js',
  output: [
    {
      file: 'dist/fb-pixel.js',
      format: 'cjs',
      name: 'ReactPixel',
      exports: 'named',
    },
  ],
  plugins: [
    terser(),
    babel({ babelHelpers: 'bundled' }),
    nodeResolve(),
    // All of following are just for beautification, not required for bundling purpose
    progress(),
    visualizer(),
    filesize(),
  ],
};

```

### package . js

对于 Parcel，我们不需要任何配置，因为默认配置足以处理我们的库。下面是我们使用的命令:

```
bash
    "bundle:parcel": "parcel build src/index.js --experimental-scope-hoisting --out-file fb-pixel-parcel.js",

```

以下是我对此的结论:

*   webpack 仍然要求我们使用 ES5 语法，这使得它有点问题
*   Rollup 的语法更简单，看起来非常适合管理库
*   Parcel v2 即将推出配置文件支持，并提供出色的默认配置来扩展复杂的应用程序

**1️⃣汇总 2️⃣包裹 3️⃣网络包**

## 特征

为了保持对新的和更复杂的网络应用程序的竞争力，这些捆绑器都提供了大多数现代应用程序所需的所有功能。

web.dev 团队最近推出了一个名为 [Tooling 的新项目。报告](https://bundlers.tooling.report/)的目标是通过直接比较工具的特性集，为您的下一个项目选择合适的工具。

就捆扎机而言，该团队在六个维度和 61 个特征测试中对它们进行了比较。这份报告让我们深入了解了所有这些捆扎机所提供的服务。这里我们总结了这些测试的结果。

### 代码拆分

通过代码分割，我们的意思是提取共享包中的公共依赖项或模块，并确保只下载和执行页面所需的代码。代码分割是保持大规模应用程序高效的一个重要方面。web.dev 团队根据八个标准评估了每个捆绑器[。结果如下。](https://bundlers.tooling.report/code-splitting/)

#### 结果:

**1️⃣汇总[6/8] 2️⃣网络包[4/8] 3️⃣包裹[3.5/8]**

这些捆绑器都不能根据其他捆绑器使用的导出来拆分模块。但除此之外，Rollup 还名列前茅，因为它通过了所有其他测试。

### 散列法

为了降低应用程序的加载时间，资源应该在下载一次后在客户端缓存和重用。要使资源的缓存无效，可以更改资源名称。这种改变可以通过将版本标识符与资源名称相关联来完成。

构建工具可以根据文件的内容生成版本标识符。如果文件内容改变，它将有一个新的版本 ID；否则，它保持不变，导致客户端重用缓存的结果。

为了避免过多的缓存无效，捆绑器必须确保正确实施无效“级联”。这意味着每个更新的 JS 和非 JS 资产都应该有一个新的 hash，所有引用该资产的 JS 包都需要更新以引用新的 URL——因此，更新的内容和引用该资产的 JS 的新 hash，等等。

捆绑器根据 [10 种不同的缓存标准](https://bundlers.tooling.report/hashing/)进行比较。

#### 结果:

**1️⃣包裹[8.5/10] 2️⃣网络包裹[8/10] 3️⃣汇总[6/10]**

Parcel 站在这里的顶端，因为它用一个真正令人印象深刻的特性击败了 web pack:bundle hashes 基于最终编译的代码，这意味着注释中的变化不会影响 bundle hashes。

### 非 JavaScript 资源

Web 应用不仅仅是 JavaScript 它们包括许多其他资源，包括丰富的内容、字体、序列化数据以及 HTML 和 CSS。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

最近，我们已经看到 JS 作为一个中心点出现，保存和放置所有这些资产。虽然 JS 不允许进口这些非 JS 资产，但捆绑商现在让这成为可能。记住代码分割和散列特性，处理这些资产变得更加复杂。

Bundlers 认为应用程序是一个图形。它将每个资源作为与它导入的所有其他资源相连接的节点来处理。这使得在哈希和基于使用的转换(如 CSS 中的命名空间)之后修改资源 URL 变得更加容易。对于这一特征类别，捆绑器通过 16 个标准进行了[比较。](https://bundlers.tooling.report/non-js-resources/)

#### 结果:

**1️⃣网络包裹[15.5/16] 2️⃣汇总[15/16] 3️⃣包裹[9.5/16]**

在处理资源方面，Parcel 在竞争中远远落后。Rollup 和 webpack 仍然是势均力敌，因为两者现在几乎都提供了捆绑非 JS 资源所需的一切。

### 输出模块格式

现代浏览器现在支持 ECMAScript 模块(ESM)，但是支持旧版本的浏览器意味着我们必须将 JS 转换成 CommonJS。这个部分只有三个标准。

#### 结果:

**1️⃣汇总[3/3] 2️⃣网络包[2/3] 2️⃣包裹[2/3]**

Rollup 在这方面处于领先地位，因为其他两家公司都无法生成 ESM 捆绑包。

### 转换

在现代应用中采用捆绑器的一个重要推动力是代码和资产的转换。这些变换中的一些是通用的，例如压缩、缩小等。而另一些则面向一组特定的资产。这些转换通常旨在支持不同版本的浏览器和优化。

web.dev 团队确定了[七个标准](https://bundlers.tooling.report/transformations/)来比较捆绑商的转换能力。

#### 结果:

**1️⃣网络包[6/7] 1️⃣汇总[6/7] 3️⃣包裹[4.5/7]**

尽管 webpack 和 Rollup 都不能从动态导入的模块中消除死代码，但这两个都通过了所有其他测试，包括 Brotli 压缩支持。

## 标杆管理

今天的网络捆绑器不仅仅用于创建生产版本。相反，我们的日常发展在很大程度上取决于他们的表现。如前所述，我们创建了一个小的 React 应用程序来测试捆绑速度和生成的捆绑包的大小。

这些基准测试在以下时间进行:

MacBook Pro (15 英寸，2018) | 2.2 GHz 6 核英特尔酷睿 i7 | 16 GB 2400 MHz DDR4 |镭龙 Pro 555X 4 GB，英特尔 UHD 显卡 630 1536 MB

### 捆绑速度

| **网络包【4 . 41 . 2】** | **Rollup [2.26.10]** | **包裹【2 . 0 . 0-β1】** | **开发首次构建** |
| 1383 毫秒🌟 | 5500 毫秒 | 3730 毫秒 | **开发重新加载** |
| 38 毫秒🌟 | 667 毫秒 | 190 毫秒 | **发布版本** |
| 2996 毫秒🌟 | 9600 毫秒 | 4670 毫秒 | **库发布** |
| 2740 毫秒 | 1660 毫秒 | 1340 毫秒🌟 | 对于应用程序开发来说，webpack 4 是一个明显的赢家，它为开发和生产环境提供了最快的构建时间。Parcel 在库捆绑方面迈出了一大步，所需时间几乎是 webpack 的一半。 |

构建大小

### **网络包【4 . 41 . 2】**

| **Rollup [2.26.10]** | **包裹【2 . 0 . 0-β1】** | **App 发布** | 132KB |
| 127.81 千字节🌟 | 128.31 千字节 | **库发布** | 6KB |
| 3KB🌟 | 5KB | 就大小而言，Rollup 在这里领先，Parcel v2 紧随其后。 | 请通过在评论区分享您的结果或在我们的知识库中提出问题来帮助改进这个基准。 |

证明文件

由于其复杂性，webpack 一直是最受诅咒的库之一，但是它的文档在过去几年中有所改进。许多开发人员一直在分享他们的经验，并且有许多资源可以用来了解 webpack 的复杂性。某些特性仍然没有文档记录，其中大部分是真正的高级用例所需要的。

## Rollup 有很好的文档，并且有大量的资源可以用来深入学习它。你可能会发现选择插件有些困难，因为大多数插件都不是官方的。然而，它是库开发者的首选解决方案，因为官方和活动插件足以覆盖大多数用例。

Parcel v2 仍处于测试阶段，文档工作正在进行中。因为它已经为 onboarding 插件建立了标准，这将有助于它的发展。

插件和生态系统

说到插件就没什么好比较的了。大多数常见用例的插件对所有捆绑器都可用，但每个插件的质量可能会有很大差异。

## webpack 有[大量官方插件](https://github.com/webpack-contrib/awesome-webpack#webpack-plugins)，选择简单快捷。Rollup 有[很多社区插件](https://github.com/rollup/awesome)，既有积极维护的也有停滞不前的。人们必须付出一些努力来测试和决定什么最适合他们。

Parcel 为 v1 的[插件提供了一个独特的机制，其中你根本不需要配置插件——只需安装它们并让它们运行。对于 v2，有一个正在开发的配置设置，它将为复杂的用例提供更多的功能。](https://github.com/parcel-bundler/awesome-parcel)

结论

无论您是新的还是经验丰富的前端开发人员，您都可能听说过关于 bundlers 的辩论——或者您自己也参与了一些辩论。webpack 因其灵活性而受到称赞，但也因其复杂性而受到诅咒。对于库来说，汇总被认为是优秀的。package 已经产生了很大的影响，一旦 v2 退出测试，它很可能会产生更大的影响。

## 选择什么？正如我们前面所说的，这取决于您的需求集。我希望这种比较有助于你更容易做出决定。

荣誉奖

积雪场是镇上的新生事物，但它为未来提供了合理的[基础。](https://blog.logrocket.com/snowpack-vs-webpack/)

## Poi 是一个关于 webpack 的人性化包装器。这种捆扎机介于包裹和网袋之间。

*   基于 Rust 的捆扎机 Pax 承诺提供更高的速度。

*   [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

## .

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

The LogRocket Redux middleware package adds an extra layer of visibility into your user sessions. LogRocket logs all actions and state from your Redux stores.

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).