# 使用 npm 查询实现更好的依赖性管理

> 原文：<https://blog.logrocket.com/npm-query-better-dependency-management/>

从版本 8.16.0 开始，npm CLI 上提供了一个新的子命令，`query`提供了一种强大的新方法来检查和了解 Node.js 项目的依赖关系。随之而来的是一个强大的新的`query`语法，基于一个熟悉的范例:CSS。没错，你现在可以使用带有`[npm query](https://docs.npmjs.com/cli/v8/commands/npm-query)`的特殊 CSS 选择器来更好地理解你的项目的依赖树。

在本文中，我们将讨论检查依赖关系的重要性，回顾`npm query`示例，深入研究这个新命令的语法，并探索格式化和操作其输出的不同方法。

*向前跳转:*

## 为什么要检查依赖关系？

首先，这些为什么有用？以下是人们想要清楚了解其依赖关系的一些原因:

*   **安全审计**:掌握安全漏洞(以及后续补丁)对于交付高质量的现代软件至关重要。围绕依赖性检查的良好工具使得修复这些问题更加容易和快速
*   **稳定性**:当项目依赖关系中的错误导致问题时，能够快速识别受影响的版本并推出修复程序是至关重要的
*   **捆绑包大小**:强大的依赖检查工具可以帮助软件团队将较小的捆绑包交付给最终用户并提高性能
*   清晰明了:更好地掌握安装了哪些软件包以及它们的预期用途，有助于工程师做出更好的决策，编写更好的代码

## 基本示例

在深入到命令参数和基于 CSS 的语法的本质之前，这里有一些基本的例子来展示这个新的`npm query`命令提供的能力和精确性。

### 许可证审计

假设您正在编写闭源、盈利的软件，并且需要确保您的依赖项都没有在 GPL 许可下发布(这要求下游用户也在 GPL 或兼容许可下发布他们的软件)。要暴露潜在的问题包，您可以使用下面的`query`命令:

```
npm query "[license=GPL]"

```

***注意，*** *此处无意作为法律意见；如果你有关于软件许可的具体问题，你应该咨询律师*

### 安装后脚本检查

一些软件包在安装后运行脚本，您可能想要检查这些脚本以确保它们没有做任何邪恶的事情。使用`npm query`，您可以很容易地找到任何注册了`postinstall`脚本的依赖项:

```
npm query ":attr(scripts, [postinstall])"

```

查看 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/CSS/attr)来复习 CSS `:attr()`函数是如何工作的。

## 句法

选择特定依赖项的主要方法类似于 CSS ID 选择器。该命令将列出安装的`lodash`的所有副本:

```
npm query "#lodash"

```

如果您想验证是否安装了某个特定的版本，比如说`4.17.21`，只需修改命令如下:

```
npm query "#[email protected]"

```

`npm query`也支持 semver 范围，只要您使用以上命令的更详细版本(它使用了一个`name`属性选择器和一个`semver`伪选择器):

```
npm query "[name=lodash]:semver(^4)"

```

我们已经开始看到这种基于 CSS 的语法所提供的灵活性。只需几个核心概念(如下所述)，您就能够利用以前的 CSS 经验来构造查询，回答关于依赖树的非常具体的问题。

### 依赖组

依赖组(例如，`prod`依赖和`dev`依赖)用熟悉的 CSS 类语法表示。所以下面是如何对所有`dev`依赖的`query`:

```
npm query ".dev"

```

这个`query`也返回`dev`组中的所有可传递依赖关系。但是，假设您想将列表限制为项目的直接`dev`依赖项。

您可以利用代表您的项目的`:root`伪选择器和 CSS 子组合子`>`来限制`query`的范围:

```
npm query ":root > .dev"

```

### 特殊伪选择器

除了标准的 CSS 伪选择器(如`:not()`、`:has()`、`:empty`等)。)，`npm query`添加了一些特殊的伪选择器，专门用于查询依赖关系树:

*   `:private`–选择在 package.json 文件中标记为私有的依赖项(`"private": true`)
*   `:deduped`–选择重复数据删除的依赖项
*   `:overridden`–选择已被覆盖的依赖项
*   `:extraneous`–选择先前状态留下的依赖关系，因为它们不再依赖于依赖关系树中的任何包
*   `:missing`–选择其他程序包请求但在磁盘上找不到的依赖项
*   `:semver(<spec>)`–选择符合指定语义版本规范的依赖项
*   `:path(<path>)`–选择与指定路径匹配的依赖项
*   `:type(<type>)`–选择给定类型的依赖项，如`git`(对于 git repo)、`directory`(对于本地目录)等。；参见这份全面的[可能类型列表](https://github.com/npm/npm-package-arg#result-object)

### 熟悉的 CSS 范例

有了上面列出的特殊伪选择器，可以充分利用 CSS 的全部能力来解锁一些真正强大的查询，深入到依赖关系树中。下面是一些特别有用的 CSS 概念的复习:

*   **组合子**:我们之前使用的`>`组合子选择与右边选择器匹配的依赖项，只要它们是左边选择器的直接后代(子代)。组合子是相似的，但是它作用于所有的后代(子代、孙代等等)。`~`组合器允许选择兄弟姐妹
*   **属性选择器**:属性选择器，像`[key=value]`一样，选择具有被设置为`value`的属性`key`的依赖项。这只对`package.json`中的字符串值有效；对于更强大的属性选择，使用`:attr()` CSS 函数
*   **高级选择器** : `*`是一个特殊的选择器，就像在 CSS 中一样，选择所有的依赖项。可以使用`,`选择器将多个选择器序列连接在一起，这将产生来自任一侧选择器的结果的联合

## 格式化和操作输出

默认情况下，`npm query`输出大量的 JSON，使用换行符和两个空格的缩进级别进行格式化。这非常适合将数据发送到 JSON 文件供其他程序使用，但是对于希望在控制台中阅读输出的人来说就不太好了。

### 在带有`jq`的终端中使用

`[jq](https://stedolan.github.io/jq/)`是一个流行且快速的 JSON 处理器，通常直接在命令行中使用。关于如何使用`jq`，网上有很多很棒的教程和例子，包括[官方 jq 手册](https://stedolan.github.io/jq/manual/)，但是这里有一个快速的例子来展示它的威力。

让我们重用本文前面的`query`来选择所有使用`postinstall`脚本(`npm query ":attr(scripts, [postinstall])"`)的依赖项，这次只显示它们的名称、版本和磁盘上的路径:

```
npm query ":attr(scripts, [postinstall])" | jq '.[] | "\(.name)@\(.version) - \(.path)"'

```

以下是一些输出示例:

```
"[email protected] - /workspaces/proutils.dev/node_modules/esbuild"
"[email protected] - /workspaces/proutils.dev/node_modules/svelte-preprocess"

```

### 程序用法

为了更好的控制，以及对结果进行高级处理和分析的能力，npm 发布了一个名为 [Arborist](https://www.npmjs.com/package/@npmcli/arborist) 的包，用于 Node.js 程序中。它具有基于 promise 的 API，并支持与 CLI 版本相同的 CSS 选择器。想了解更多关于树木学家的细节，请看一下[的官方文件](https://docs.npmjs.com/cli/v8/using-npm/dependency-selectors#programmatic-usage)。

## 如何用`npm query`改进依赖管理

在本文中，我们快速浏览了功能强大的新`npm query` CLI 命令。通过精心制作的`query`，您可以更容易地深入了解依赖关系树。

有很多用例，包括`[npm query](https://docs.npmjs.com/cli/v8/commands/npm-query#extended-use-cases--queries)`[文档](https://docs.npmjs.com/cli/v8/commands/npm-query#extended-use-cases--queries)中的一大串。为了让你的思维车轮开启可能性，这里只是一些用例，作为开发团队可能对他们的依赖栈的问题。还提供了有助于回答这些问题的查询:

*   我是否需要运行`npm install`因为我丢失了队友添加的一些依赖项？
*   有没有既依赖`lodash`又依赖`underscore`的包？
    *   `npm query "*:has(> #lodash ~ #underscore)"`
*   哪些包是依赖关系树中的叶节点？
    *   `npm query "*:not(*:has(> *))"`

你还能想到哪些有用的查询？

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.