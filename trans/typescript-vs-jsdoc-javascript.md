# 静态类型检查的 TypeScript 与 JSDoc JavaScript

> 原文：<https://blog.logrocket.com/typescript-vs-jsdoc-javascript/>

关于在构建项目时使用 JavaScript 还是 TypeScript 会产生更好的结果，还存在争议。使用 JSDoc 注释来键入 JavaScript 代码库的出现为这一讨论带来了新的动力。

在本指南中，我们将调查这看起来像什么，并得出一个(固执己见的)结论。

如果你在 2018 年与我交谈，我会坚定地建议使用 TypeScript，远离 JavaScript。

基本原理很简单:我非常相信静态类型在生产率和避免生产中的错误方面提供的价值。我知道这可能是一个有争议的问题，但这是我对这个问题的既定看法。其他意见都有。

TypeScript 一直有一个很好的静态类型故事。因为 JavaScript 是动态类型的，从历史上看，它不是。由于 TypeScript 对 JSDoc 的支持，现在可以对 JavaScript 进行静态类型检查。

## JSDoc JavaScript 是什么？

JSDoc 本身实际上可以追溯到 1999 年。据[维基百科](https://en.wikipedia.org/wiki/JSDoc):

> JSDoc 是一种用于注释 JavaScript 源代码文件的标记语言。使用包含 JSDoc 的注释，程序员可以添加描述他们正在创建的代码的应用程序编程接口的文档。
> 
> TypeScript 团队已经接受了 JSDoc 支持并使用它。现在可以使用 JSDoc 注释的[变体来提供 JavaScript 文件中的类型信息。](https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html)

这看起来像什么？以下面的`simpleTypeScript`语句为例:

```
let myString: string; 

```

这个 TypeScript 语句可以变成带有 JSDoc 注释的等效 JavaScript 语句:

```
/** @type {string} */
let myString; 

```

这是类型增强的 JavaScript，TypeScript 编译器可以理解并进行类型检查。

## 为什么要用 JSDoc JavaScript？

为什么要用 JSDoc JavaScript 而不是 TypeScript？嗯，有一系列可能的用例。

也许您正在编写简单的节点脚本，并且您想要一点类型安全来避免错误。或者您可能想尝试一下静态类型检查，但不完全提交。JSDoc 考虑到了这一点。或者，您的团队可能只是不希望有编译步骤。

事实上，这就是 webpack 团队的基本原理。一点历史:webpack 一直是 JavaScript 代码库。随着代码库越来越大，经常有关于使用静态类型的讨论。然而，不希望有编译步骤。

一段时间以来，TypeScript 一直在 JSDoc 的帮助下悄悄地添加对类型检查 JavaScript 的支持。最初的支持来自于 [TypeScript 2.3](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-3.html#errors-in-js-files-with---checkjs) 中的`--checkJs`编译器选项。

一个名叫 [Mohsen Azimi](https://twitter.com/mohsen____) 的社区成员开始试验性地使用这种方法来检查 webpack 代码库。他的 [PR](https://github.com/webpack/webpack/pull/6862) 最终成为一个测试案例，帮助改进了 TypeScript 对 JavaScript 的类型检查。

作为 webpack 工作的结果，TypeScript v2.9 附带了大量 JSDoc 改进。因为这是一个如此广泛使用的项目，这也有助于推广使用 JSDoc 对 JavaScript 代码库进行类型检查的方法。它证明了这种方法可以在非常大的代码库上工作。

如今，用 TypeScript 进行 JSDoc 类型检查是非常强大的。虽然与 TypeScript 不太一样(JSDoc 中并不支持所有的 TypeScript 语法)，但功能上的差距相当小。

今天，利用静态类型的所有优势构建 JavaScript 代码库是完全合理的选择。

## 为什么使用 TypeScript？

如果您正在启动一个项目，并且希望使用静态类型，那么如何在 JSDoc 的 TypeScript 和 JavaScript 之间做出选择呢？

嗯，除非您迫切需要避免编译步骤，否则我个人认为 TypeScript 是更好的选择，原因有很多。

首先，直接使用 TypeScript 的工具支持比 JSDoc JavaScript 更好。在写的时候，重构工具之类的东西。在编辑器中，使用 TypeScript 比使用 JSDoc JavaScript 更有效。也就是说，这些正在逐步改善。

其次，使用 JSDoc 显然“更吵”——它需要更多的击键来达到同样的类型安全级别。

考虑以下类型脚本:

```
function stringsStringStrings(p1: string, p2?: string, p3?: string, p4 = "test"): string {
  // ...
}

```

与等效的 JSDoc JavaScript 相比:

```
/**
 * @param {string}  p1
 * @param {string=} p2
 * @param {string} [p3]
 * @param {string} [p4="test"]
 * @return {string}
 */
function stringsStringStrings(p1, p2, p3, p4) {
  // ...
}

```

我可能因为对 TypeScript 的熟悉而有所偏见，但是我发现 TypeScript 比 JSDoc JavaScript 更容易阅读和理解。所有 JSDoc 注释都存在于注释中，而不是直接存在于语法中，这使得理解起来更加困难。(许多 VS 代码主题以非常暗淡的颜色呈现注释，这当然没有帮助。)

我支持 TypeScript 的最后一个原因可以归结为落入了“[成功之坑](https://blog.codinghorror.com/falling-into-the-pit-of-success/)”当谈到静态类型和 JavaScript 时，你就违背了初衷。您可以拥有它，但是您必须更加努力地工作，以确保您拥有静态类型的代码。

另一方面，当谈到静态打字和打字稿时，你是在用切割*的纹理。你必须努力摆脱静态打字。TypeScript 默认倾向于静态类型，而 JavaScript 默认倾向于远离静态类型。*

作为一个非常喜欢静态类型的人，你可以想象这对我有多有吸引力！

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## TypeScript 和 JSDoc JavaScript 哪个好？

总结一下，从某种程度上来说，我并没有感觉到人们使用 JavaScript 还是 TypeScript 的超级强烈。也就是说，拥有静态类型可能对新项目有益。

底线是:我渴望人们掉进成功的陷阱，所以我对新项目的建议是打字稿。

我自己也很喜欢 JSDoc，会经常用在小项目上。这是对 TypeScript 功能的极好补充。对于更大的项目，我更倾向于一开始就使用 TypeScript。

但是，真的，两者都是可靠的选择。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.