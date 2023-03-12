# TypeScript 4.2 中的新特性- LogRocket 博客

> 原文：<https://blog.logrocket.com/whats-new-in-typescript-4-2/>

# TypeScript 4.2 中的新增功能

## 2021 年 4 月 20 日 2 分钟读完 794

TypeScript 的 4.2 版本于 2021 年 2 月 23 日发布，它提供了许多不错的特性、错误修复和性能改进。以下是你应该知道的最重要的几个。

TypeScript 4.2 的主要增强功能

## 元组类型中更灵活的 rest 元素

### 在 4.2 之前，rest 元素只能出现在元组类型的末尾。例如，如果我们正在对一个双人游戏的状态进行建模，我们可以使用这个元组类型来保存我们的两个`Player`和一个他们玩过的`Move`的列表:

在 4.2 中，`Move`不必在类型的末尾；他们可能在中间，甚至在开始。如果需要，我们现在可以如下扩展我们的类型，以包括轮到哪个玩家了。为了更加清晰，我们将利用 TypeScript 对带标签的元组元素的支持(这在 4.2 中并不新鲜):

```
let gameState: [Player, Player, ...Move[]];

```

增强了对编译程序中包含哪些文件的可见性

```
let gameState: [player1: Player, player2: Player, ...moves: Move[], currentTurn: number];

```

### TypeScript 的编译器(`tsc`)现在包含了一个新的标志`--explainFiles`，它以一种简单的文本格式输出编译中包含的文件列表以及它们为什么在那里的基本推理。在`tsconfig.json`中开发或微调编译器配置时，这非常有帮助。

这个特性是调试构建时问题的良好开端，我希望它在未来的版本中变得更加健壮和强大(例如，一个 JSON 输出格式，用于接收到其他工具中进行更高级的分析)。

给定一个默认的 TypeScript 配置和一个带有简单的`console.log('hello, world!');`的`index.ts`文件，下面是来自`--explainFiles`标志的一些示例输出:

使用 GitHub 上的[这个示例库(包括](https://github.com/mjswensen/typescript-4.2) `[devcontainer.json](https://github.com/mjswensen/typescript-4.2)` [)亲自尝试一下。](https://github.com/mjswensen/typescript-4.2)

```
node_modules/typescript/lib/lib.d.ts
  Default library
node_modules/typescript/lib/lib.es5.d.ts
  Library referenced via 'es5' from file 'node_modules/typescript/lib/lib.d.ts'
node_modules/typescript/lib/lib.dom.d.ts
  Library referenced via 'dom' from file 'node_modules/typescript/lib/lib.d.ts'
node_modules/typescript/lib/lib.webworker.importscripts.d.ts
  Library referenced via 'webworker.importscripts' from file 'node_modules/typescript/lib/lib.d.ts'
node_modules/typescript/lib/lib.scripthost.d.ts
  Library referenced via 'scripthost' from file 'node_modules/typescript/lib/lib.d.ts'
index.ts
  Root file specified for compilation

```

更好地支持未使用的析构变量

### 当析构元组或数组时，有时您感兴趣的元素没有出现在列表的开头。在这些情况下，“一次性”变量名如`_`或`a`、`b`、`c`等。，用于不感兴趣的元素。

然而，在 4.2 版之前，打开`noUnusedLocals`编译器选项时，这些未使用的局部变量会导致 TypeScript 抛出错误。

现在，只需在未使用的变量名前加上`_`即可，TypeScript 会很乐意忽略这些变量，如果它们未被使用，也不会抛出错误。例如，当从 CSV 电子表格的行中提取数据位时，这一新功能特别有用:

在这种情况下，在未使用的变量名前面加上`_`是一种常见的约定；这是工具作者精心构建并支持用户现有行为的一个例子。

```
function* getCsvRows(): Generator<string[], void, void> { /* ... */ }

for (const row of getCsvRows()) {
  // Destructure row, utilizing only the 1st, 4th, and 6th columns.
  const [id, _1, _2, name, _3, country] = row;
  // ... do something with id, name, and country
}

```

更智能的类型系统和性能改进

### 与任何 TypeScript 版本一样，也有许多较小的增强，它们本身并不是开创性的，但它们使 TypeScript 逐渐变得更好，使用起来更舒适。仅举几个例子:

试图在基本类型上使用`in`操作符时的一个有用错误。这通常是一个运行时错误(在 JavaScript 中)，但是现在在 TypeScript 中可以在编译时捕获

*   元组大小的内部限制与扩展语法结合使用，以提高编译性能
*   更好地解析和解释普通 JavaScript 文件
*   一个名为`--noPropertyAccessFromIndexSignature`的新标志，可以帮助减少在某些情况下对象属性名称拼写错误的错误
*   更多详细信息和资源

## 可以在 GitHub 上的 [TypeScript 项目发布页面以及 TypeScript 博客](https://github.com/microsoft/TypeScript/releases)上的[发布公告中看到所有增强功能的完整列表。这些是 TypeScript 4.2 版本的最大亮点。要更深入地了解这些变化或了解关于 TypeScript 的更多信息，请查阅以下资源:](https://devblogs.microsoft.com/typescript/announcing-typescript-4-2)

[LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

## LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

.

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.