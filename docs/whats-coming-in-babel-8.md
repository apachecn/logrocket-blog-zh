# 巴别塔 8 有什么消息

> 原文：<https://blog.logrocket.com/whats-coming-in-babel-8/>

TC39 ( [技术委员会 39](https://www.ecma-international.org/memento/tc39-rf-tg.htm)——在“ECMAScript”规范下对 JavaScript 语言进行标准化的机构)发布了 JavaScript 的 ES2015 版本，俗称 ES6。开发人员越来越多地采用 ES6，但浏览器并不支持 ES2015 的所有功能，因此需要工具来使用 JavaScript 编程语言的最新功能。

最初名为 [6to5](https://babeljs.io/blog/2015/02/15/not-born-to-die) 的 Babel 正是做到了这一点——它使得将 ES6 代码转换成可以由旧的 JavaScript 引擎运行的向后兼容版本成为可能。将一种语言编写的代码编译成同一种语言的另一种形式的过程称为 Transpiling(转换+编译)，在这种情况下，将 ES6+编写的 JavaScript 代码转换成 ES5。

## 巴别塔是什么？

根据平台[官方文件](https://babeljs.io/docs/en/index.html):

> Babel 是一个工具链，主要用于将 ECMAScript 2015+代码转换为当前和旧版本浏览器或环境中向后兼容的 JavaScript 版本

Babel 是一个 JavaScript transpiler，它提供了旧环境中 JavaScript 语言的新特性。它提供了可用的[预置](https://babeljs.io/docs/en/presets)和[插件](https://babeljs.io/docs/en/plugins)，使代码转换、语法转换和多填充功能成为可能，这些功能在您的目标环境中是不存在的。

在这篇文章中，我们将会看到巴别塔工具的第 8 版在之前版本的一些特性和改进。

## 新 JSX 变换

在这个 [RFC](https://github.com/reactjs/rfcs/blob/createlement-rfc/text/0000-create-element-changes.md) 中的 React 团队已经提供了创建 JSX 元素的新方法，并简化了`React.createElement()`的工作方式。

巴别塔 8 将包括一个新的 [JSX 变换](https://github.com/reactjs/rfcs/blob/createlement-rfc/text/0000-create-element-changes.md)，这将使 JSX 元素[的创建和实例化在 React 和 React-like 库中得到相当大的改进。](https://babeljs.io/docs/en/babel-preset-react)

这个新的转换将支持`React.jsx`而不是`React.createElement`。它还会在需要的时候自动导入`"react"`，这样你就不用手动导入`"react"`了。

这种转换还具有以下行为:

*   把孩子当作道具而不是明确的论据
*   单独传递`key`作为显式参数，而不是通过道具
*   处于开发模式
    *   传递一个标志来确定它是否是静态的
    *   与其他道具分开传递`__source`和`__self`

```
React.createElement(type, props, children)
```

成为

```
React.jsx(type, props, key)
```

例如，该输入:

```
function Foo() {
  return <div />;
}
```

会变成:

```
import { jsx as _jsx } from "react/jsx-runtime";
function Foo() {
  return _jsx("div", ...);
}
```

您可以通过将`{ "runtime": "automatic" }`(相对于`"classic"`)传递给`@babel/preset-react`(或`@babel/plugin-transform-react-jsx`)来启用这个新的转换:

```
{
  "presets": [
    ["@babel/preset-react", {
      "runtime": "automatic"
    }]
  ]
}
```

从巴别塔 8 开始，`"automatic"`将是默认运行时。

您还可以通过安装[实验版](https://reactjs.org/blog/2019/10/22/react-release-channels.html#experimental-channel)来尝试 React 中用于实例化 JSX 元素的新功能，因为它尚未在稳定版中提供:

```
npm install [email protected] [email protected]
```

## 完全支持 TypeScript 3.8

此版本还将包括对 [TypeScript 3.8](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-8.html) 的全面支持，该版本引入了显式的仅类型导入和导出(即`export type { foo }`)，在 Babel 的第 8 版中，您将能够显式地将导入和导出标记为仅类型:

```
import type { SomeThing } from "./some-module.js";

export type { SomeThing };
```

这将允许 Babel 安全地决定哪些导入或导出用于类型，哪些用于值。

## 使用流/类型脚本时维护类字段

这个特性会让 Babel 忽略未初始化的类字段，如果流插件在类属性插件之前运行，或者类属性插件没有使用，就会发生这种情况。

Babel 目前转换如下声明的代码:

```
class Foo { x: string }
```

成为:

```
class Foo {}
```

这个特性在版本 8 中将会看到相同代码:

```
class Foo { x }
```

希望保持旧行为的用户可以使用流评论(如[官方推荐的](https://github.com/facebook/flow/issues/6811#issuecomment-496808192)):

```
class Bar extends Foo {
  x: ?number;
  /*:: y: number; */
}
```

或者使用添加到类属性插件( [#9141](https://github.com/babel/babel/pull/9141) )的`ignoreUninitialized`选项。

## 放弃对`core-js 2`的支持

Babel 将不再支持版本 2 的 core-js，这是 JavaScript 标准库的一个聚合填充，支持最新的 ECMAScript 标准和库提议。

Babel 将放弃对它的支持，因为它在 V8 浏览器引擎(最流行的 JavaScript 引擎和支持 Chrome 的引擎)上引入了去优化。这个问题会影响正则表达式、迭代器、一些数组方法、类型化数组、promises，有时会导致大约 100 倍的性能下降。

巴别塔 8 将使用 [core-js 版本 3](https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md) 。

## 使用 useSpread 变换 JSX 扩散属性

这个特性改变了`transform-react-jsx`插件的当前行为，它通过使用巴别塔的`extend`助手或者调用`Object.assign`来处理道具传播`{...Props}`。使用此功能，您现在可以使用 spread 运算符将此类实例转换为内嵌对象。

根据[文档](https://github.com/babel/babel/pull/10572)，您可以使用`@babel/preset-react`或`@babel/plugin-transform-react-jsx`的`useSpread`选项来启用它:

```
{
 presets: [
  ["@babel/react", { useSpread: true }]
  ]
}
```

如果您的代码需要在不支持对象传播的环境中运行，您可以使用`@babel/preset-env`(推荐)或`@babel/plugin-proposal-object-rest-spread`。

如果你想将`Object.assign`下传到 Babel 的`_extends`助手(这是当前的默认行为)，你还需要启用`@babel/plugin-transform-object-assign`。

## 禁用子模块导出

Babel 8 将不允许导入不同包的内部文件，它将在每个包中添加`exports: false`,明确声明每个包不提供子模块导出。

尝试类似`import "@babel/core/src/config"`的东西会失败，因为这个文件属于内部实现细节。

## `Declare`流程中的字段

流团队为类字段添加了对`declare`修饰符的支持，因为类字段提议指定未初始化的类字段被初始化为`undefined`。这与巴别塔对心流的处理截然不同，它只是忽略了它们。

Babel 8 在类字段中包含对`declare`关键字的支持:

```
class Foo {
  x: ?string; // A "real" field
  declare y: number; // A type-only field
}
```

## 入门指南

让我们看看如何在我们的项目中开始使用 Babel。

本教程假设读者具备以下条件:

安装 [Babel CLI](https://babeljs.io/docs/en/babel-cli) 工具，这个工具允许我们从命令行编译 JavaScript 文件。

我们可以通过运行以下命令在本地安装 Babel CLI:

```
#Using NPM
npm install --save-dev @babel/core @babel/cli

#Using Yarn
yarn add -D @babel/core @babel/cli
```

运行这个命令后，您应该会看到在您的`package.json`文件中作为`devDependencies`添加的包。

要编译文件，请运行命令

```
npx babel filename.js
```

或者，如果希望将编译后的代码输出到文件中，也可以运行以下命令:

```
npx babel filename.js -o compiled-filename.js
```

你也可以使用[在线回复](https://babeljs.io/repl)来编译你的代码。

## 结论

为了减轻开发人员从 Babel 5 迁移到 Babel 6，然后从 Babel 6 迁移到 Babel 7 的痛苦。Babel 8 将只包含一些突破性的变化，并为它们提供一个简单的迁移策略。

Babel 8 目前完成了不到 50%，没有设定的到期日期，预计将在 8.0.0 中首次出现突破性的变化，然后将在同一天发布一个次要版本，其中将包含所有错误修复和新功能，否则将在 8.0.0 中发布。

Babel 是一个重要的工具，几乎每一个 JavaScript 工具都依赖于它，你可以阅读[发布计划](https://github.com/babel/babel/issues/10746)并查看预计将发布的其他功能。

你怎么想呢?哪些新功能让你印象深刻？请在评论区告诉我。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)