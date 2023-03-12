# MDX v2 中的新功能

> 原文：<https://blog.logrocket.com/whats-new-mdx-v2/>

经过两年半的等待开发，MDX v2 终于发布了。MDX v2 在性能和语法等方面对 MDX v1 的功能进行了一些急需的改进，并增加了一些功能，如支持 JavaScript 表达式和更多 JSX 运行时。

在本文中，我们将探讨 MDX v2 发行说明中强调的一些惊人改进，包括:

尽情享受吧！

## 改进的 MDX 语法格式

与第一个版本相比，新改进的 MDX 语法使得在 JSX 使用 MDX 更加容易。根据 MDX 团队的发布帖子，

> 我们花了很多时间思考和尝试不同的方法来改进格式。最初，这种格式非常接近 Markdown，以及 Markdown 中的 HTML 的工作方式。我们发现旧的行为经常会产生意想不到的结果。在版本 2 中，我们稍微改变了格式，使之更接近 JS(X)的工作方式。

在旧格式中，MDX 语法被转换成相对的 Markdown 格式，如下所示:

*   `<div>*italicize*</div>`转换为`<div>*italicize*</div>`
*   `<div># header one</div>`转换为`<div># header one</div>`

但是这导致了一系列的解析问题。使用新的语法，MDX 代码将直接转换为其等效的 JSX 格式:

*   `<div>*italicize*</div>`变成了`<div><em>italicize</em></div>`
*   `<div># header one</div>`变成了`<div><h1>header one</h1></div>`

此更新承诺解决 MDX v1 语法中遇到的解析问题。点击此[链接](https://mdxjs.com/docs/what-is-mdx/#mdx-syntax)了解更多关于 MDX v2 语法的信息。

## 支持 JavaScript 表达式

MDX v2 支持向 MDX 代码添加表达式，如 JavaScript 表达式。表达式可以添加到 MDX 文档中花括号内的任何地方，不需要限制在文档的某些区域。

现在，您可以在 MDX 代码中直接执行算术运算。例如，这段代码:

```
# Quick maths -> { (2+2) * Math.PI}

```

产生以下输出:

```
Quick maths -> 12.566370614359172

```

表达式的大括号也可以为空或包含注释。

```
{/* This is a lovely comment! */}

```

要了解有关 MDX 表达式的更多信息，请访问此[链接](https://mdxjs.com/docs/what-is-mdx/#expressions)。

## 增加和扩展了对 JSX 运行时的支持

MDX v2 增加了对更多 JavaScript 运行时和包的支持。MDX 的 v1 版本主要与 Babel、webpack 和 React 一起工作，并且不能够容易地与其他工具一起使用。

这在 v2 版本中已经改变。Babel、webpack 和 React 现在是可选的，MDX 现在可以用于:

请访问 [MDX 迁移页面](https://mdxjs.com/docs/getting-started/#getting-started)了解更多信息。

## 支持 ES 模块

MDX v2 已经完全转变为仅支持 ECMAScript 模块(ESM)。虽然 MDX v1 支持 ESM，但 v2 只能与支持 ESM 的工具配合使用。因此，像下面这样的导入无法在 MDX v2 上运行。

*   `const data = require('./data')`
*   `import { foo } from 'foo/lib/main'`

你得改为 ESM JavaScript。

*   `import data from './data.js'`
*   `import { foo } from 'foo/lib/main.js'`

点击了解有关 MDX v2 ESM 支持[的更多信息。](https://mdxjs.com/docs/troubleshooting-mdx/#esm)

## 改进的文档

整个 MDX 文档从上到下都进行了重写，以便为试图修改 MDX v2 的人们提供更好的体验。所有不一致和重复的内容也已从文档网站上删除。

文档网站也用 MDX 重新构建，提供了更优化的体验和性能。据 MDX 团队称，与以前的版本相比，新的文档网站在所有性能和可访问性测试中得分都很高。

## 建筑改进

为了提高新版本的性能和功能，整个 MDX 体系结构已经过重写。新架构具有:

*   更快的错误标记
*   更好的 MDX 语法
*   支持更多的运行时和包

但是最令人兴奋的特性是改进的[抽象语法树(AST)](https://www.telerik.com/blogs/asts-markdown-and-mdx) 。新的 AST 更详细地描述了 MDX v2 语法，这将通过允许插件以新的方式增强 MDX 来改善 MDX 周围的生态系统，并帮助 MDX 解决以前可能导致崩溃的边缘情况。

MDX 团队声称，改进的架构比以前的版本编译时间快 25 %,生成代码的速度快 100 %,包的大小比使用 MDX v1 编写的包小 250%。

您可以在发行说明中了解更多关于改进架构的信息。

## 类型脚本支持

改进的 MDX 体系结构还提供了 TypeScript 支持。根据 MDX 团队的说法，

> 所有的 [@mdx-js/*包](https://mdxjs.com/packages/)现在都通过 JSDoc 注释用 TypeScript 完全类型化了。这意味着我们的 API 是作为 TypeScript 类型公开的，而且我们的项目在内部是类型安全的。

要为 MDX 文档添加 TypeScript 支持，请安装 [@types/mdx](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/HEAD/types/mdx) 。

```
npm install @types/mdx

```

TypeScript 应该在安装后自动获取 MDX 文档。

## 将 React、Vue 和 Preact 应用迁移到 MDX v2

在将您的应用迁移到 MDX v2 之前，请确保您使用的是最新版本的 React、Preact 或 Vue 3。此外，请注意，没有 ESM 支持的工具将无法在 MDX v2 中工作，您可以在此了解有关 MDX v2 ESM 支持的更多信息[。](https://mdxjs.com/docs/troubleshooting-mdx/#esm)

您可以使用以下命令更新 React 版本。

```
npm install --save [email protected]

```

然后，用下面的命令安装 [(@mdx-js/react)](https://mdxjs.com/packages/react/) 。

```
npm install @mdx-js/react 

```

要将 MDX v2 添加到 Vue CLI，首先，使用以下命令更新您的 Vue 应用程序。

```
npm update -g @vue/cli

```

用下面的命令安装 [(@mdx-js/vue)](https://mdxjs.com/packages/vue/) 。

```
npm install @mdx-js/vue 

```

要将 MDX v2 添加到 Preact 应用程序中，首先要安装 Preact 的最新版本。

```
npm install preact

```

然后用下面的命令安装 [(@mdx-js/preact)](https://mdxjs.com/packages/preact/) 。

```
npm install  @mdx-js/preact

```

有关如何从 MDX v1 迁移到 MDX v2 的更多说明，请访问 [MDX 迁移指南](https://mdxjs.com/migrating/v2/#mdx-jsloader)。

## 可能的 MDX v2 突破性更改

由于新的语法，升级到新版本后，您可能会从 MDX 文件中得到错误。仔细研究错误消息，了解您遇到了什么错误以及如何修复这些错误。以下是一些常见的问题:

*   `Could not parse import/exports with acorn: $error` —当您使用无效的`import`或`export`语句时，会出现这种情况
*   `Unexpected end of file in expression, expected a corresponding closing brace for `{`: $error` —当有左花括号而没有右花括号时，会出现这种情况
*   `Could not parse expression with acorn: $error` —当花括号内的表达式无效时，会出现这种情况
*   当花括号中的表达式过多时，就会出现这种情况

更多错误及解决方法，请参见 [MDX v2 故障排除指南](https://mdxjs.com/docs/troubleshooting-mdx/)。

### ESM 支持的迁移问题

如果您在将 MDX v2 与特定工具集成时遇到问题，这很可能是因为 MDX v2 ESM 支持。与 MDX v1 不同，MDX v2 只能与支持 ESM 的工具一起使用。您可以在此了解有关 MDX v2 ESM 支持[的更多信息。](https://mdxjs.com/docs/troubleshooting-mdx/#esm)

## 结论

在本文中，我们讨论了新发布的 MDX v2。我们讨论了架构和语法的改进，它们可能的突破性变化，以及如何在 React、Vue 和 Preact 应用程序中迁移到新版本。

新发布的 MDX v2 为 MDX 语言带来了一些急需的改进。由于减少了解析问题，改进的语法应该使 MDX 更便于使用。对更多 JSX 运行时的支持也应该增加它被更多前端框架采用的可能性。

更多的改进还在后面——现在，我们知道对带有 [`let/const/var`的 JavaScript 变量声明的支持预计将在 v2.1](https://github.com/mdx-js/mdx/issues/1046) 中发布，而 [MDX 插件](https://github.com/mdx-js/mdx/issues/741)预计将在 v2.2 中发布

要了解更多信息，您可以访问 [GitHub](https://github.com/mdx-js/mdx/issues/1041) 上的 MDX 发布说明。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)