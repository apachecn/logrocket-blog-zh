# 类型注释(也称为类型注释):强类型，弱持有

> 原文：<https://blog.logrocket.com/types-as-comments-strong-types-weakly-held/>

最近，一个名为[类型注释](https://github.com/tc39/proposal-type-annotations)(以前称为注释类型)的新 ECMAScript 提案被披露。该提议的目的是允许类型注释成为有效的 JavaScript 语法，尽管 JavaScript 引擎会忽略该语法。

吉尔·塔亚尔、丹尼尔·罗森瓦瑟、罗慕洛·辛特拉、罗布·帕尔默和其他人正在研究这项提案。这些人中有许多来自 TypeScript 社区——然而，这个提议的存在并不仅仅是为了 TypeScript 的利益。

这是一个有争议的话题。作为一个定期(和长期)的 TypeScript 用户，这里有一个提案的描述和一些想法。

## 提议是什么？

类型注释(或者有人称之为“tomments”)是一个允许在 JavaScript 代码中包含类型的提议。考虑下面这段类型脚本:

```
const theAnswer: number = 42;

```

目前，这不是有效的 JavaScript。如果您尝试在 JavaScript 引擎中运行它，您会得到一个错误，因为类型不是 JavaScript 语法的一部分。

![Types in the Chrome Console](img/e2c4b99947f274af0415f2cd21b1fa87.png)

有趣的是，已经可以通过 JSDoc 标准在 JavaScript 中存储类型了。我以前写过 TypeScript 和 JSDoc 是如何连接的。本质上，需要注意的是 JSDoc 允许在 JavaScript 注释的上下文中存储类型声明。

已经可以用有效的 JavaScript 编写代码示例，表达 JSDoc 中的类型。看起来是这样的:

```
/** @type {number} */
const theAnswer = 42;

```

这是可行的，但是需要两行代码，而不是一行。该提议允许类型被直接表达，而不是被写成注释。因此，与其编写 JSDoc 的等价物，不如想象一下 JavaScript 对以下内容感到满意:

```
const theAnswer: number = 42;

```

这就是这个建议的含义。

## 不是什么？

既然我们理解了这个提议是什么，让我们考虑它不是什么。

类型注释并不是对特定类型系统的认可。此外，它不是浏览器中的类型检查或 Node.js 中的类型检查。

让我们逐一考虑一下。有许多语言允许我们对 JavaScript 进行类型检查:TypeScript、Flow、Hegel 和其他语言都扮演着这个角色。它们很相似，但是语法不同，做的事情也不同。

它们的共同点是类型在其句法或语法中所处的空间。这个提议实质上是说“嘿，我们可能没有不同的方法来描述类型，但是我们同意类型应该存在于哪里——让我们标准化它。”

这就是为什么这个提案以前称之为“类型作为注释”的术语很关键:JavaScript 运行时会忽略这些类型。它们将被忽略的事实表明，没有任何现有的类型系统会被该建议“钦点”。

请考虑以下情况:

```
const theAnswer: gibberish = 42;

```

这既不是 TypeScript，也不是 Flow 双方都会对上述内容有所抱怨。但是如果类型注释的提议被采纳，JavaScript 将完全没有问题。

重申一下:该提议并不是对任何给定类型系统的认可，因此 JavaScript 中没有引入运行时类型检查。

## 为什么要这样做呢？

值得看一看丹尼尔·罗森瓦瑟的帖子，他在那里宣布了这个提议。丹尼尔是打字团队的一员，也是这项提议的支持者之一，其他支持者还有彭博的[罗布·帕尔默](https://twitter.com/robpalmer2)和伊加利亚的[罗慕洛·辛特拉](https://twitter.com/romulocintra)。

丹尼尔说:

> 今天，您可以在编辑器中创建一个. js 文件，并开始以 JSDoc 注释的形式在类型中散布。
> 
> ```
> /**
>  * @param a {number}
>  * @param b {number}
>  */
> function add(a, b) {
>     return a + b;
> }
> 
> ```
> 
> 因为这些只是注释，它们根本不会改变您的代码如何运行——它们只是一种文档形式，但是 TypeScript 使用它们来为您提供更好的 JavaScript 编辑体验……该功能使得无需构建步骤即可获得一些 TypeScript 体验变得非常方便，您可以将它用于小脚本、基本网页、Node.js 中的服务器代码等。
> 
> 不过，您会注意到这有点冗长——我们喜欢内循环对于编写 JavaScript 来说是多么轻量级，但是我们忽略了 TypeScript 对于编写类型来说是多么方便。
> 
> 如果我们两样都有呢？
> 
> 如果在 JavaScript 中我们可以拥有完全被忽略的类似于 TypeScript 语法的东西——有点像注释——会怎么样？
> 
> ```
> function add(a: number, b: number) {
>     return a + b;
> }
> 
> ```

我从中得到的是，带有类型注释的 JavaScript 将是对开发人员更友好的 JSDoc。

## 这是我一直想要的 JSDoc！

这个想法真的让我产生了共鸣。我是 JSDoc 的长期用户。让我解释一下为什么我觉得它有用。

在 TypeScript 出现之前，我想要的是带有静态类型的 JavaScript。打字稿大多是*那种。至少在我选择使用它的方式上。*

 *我不用`enum` s、`namespace` s、`decorator` s 等。这很重要，因为这些特征的每个步骤都有一个发射方面；使用其中之一将需要 transpilation 创建特殊的 JavaScript 来表示自定义的 TypeScript 实现的特性。所有其他的打字稿特征都被翻译删除；没有执行特征。

因此，通过子集化 TypeScript 的特性，我们可以选择只使用那些没有 emit 方面的特性。通过做出这样的选择，如果我们愿意在 JavaScript *中使用 JSDoc 语法而不是 TypeScript 的*，那么就可以只使用 JavaScript。社区中有很多人已经在像 [webpack](https://github.com/webpack/webpack) 这样的大型项目中这样做了。感谢像 VS Code 这样的编辑器，我们不会失去类型检查，也不会失去重构的可能性。

JSDoc 很棒，但是不可否认它比编写 TypeScript 更冗长。如果采用类型注释，我们将能够在 JavaScript 文件中编写类型脚本。如果我们愿意，我们也可以使用 TypeScript 来进行类型检查。但是我们不需要在运行之前转换我们的代码——我们可以直接运行我们的源代码。太棒了。

## 争议与妥协

到目前为止，正如我们所看到的提议，这个故事是 JavaScript 变得“类型宽容”的故事之一因此，Flow/TypeScript/Hegel 等人的语法在将来会被认为是有效的 JavaScript。

这描绘了一幅 JavaScript 作为动态语言被改变以适应那些喜欢静态类型的人的感受的画面。如果你看一下黑客新闻上的讨论和这个提议的问题，很明显有一部分 JavaScript 开发者认为这个提议是完全不受欢迎的。

虽然最狂热的动态语言倡导者不太可能改变他们的想法，但值得考虑一下这个提议的细微差别。事实上，这项建议是双向的；为了适应成为 JavaScript 原生的类型，像 TypeScript 这样的语言可能会做出一些改变来适应。

## 泛型调用和类型脚本

有一些情况适用，其中最重要的是泛型调用。[引用提案](https://github.com/giltayar/proposal-types-as-comments#generic-invocations):

> 可以在 TypeScript 中显式指定泛型函数调用或泛型类实例化[的类型参数。](https://www.typescriptlang.org/docs/handbook/2/functions.html#specifying-type-arguments)
> 
> ```
> // TypeScript
> add<number>(4, 5);
> new Point<bigint>(4n, 5n);
> 
> ```
> 
> 上面的语法已经是用户可能依赖的有效 JavaScript，所以我们不能照原样使用这个语法。

所以，如果这个提议成功了，用 JavaScript 编写今天的样式类型脚本在泛型调用的情况下将*不起作用。*

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

如果我们继续阅读提案，它说:

> 我们期待某种形式的新语法可以用来解决这种歧义。
> 目前没有提出具体的解决方案，但一个示例选项是使用一个句法前缀，如`::`
> 
> ```
> // Types as Comments - example syntax solution
> add::<number>(4, 5)
> new Point::<bigint>(4n, 5n)
> 
> ```
> 
> JavaScript 运行时会忽略这些类型参数(`::<type>`)。在 TypeScript 中采用这种明确的语法也是合理的。

这最后一句话意义重大。让我们再读一遍:

> 在 TypeScript 中也采用这种明确的语法是合理的。

虽然这不是一个绝对的承诺，但这肯定表明 TypeScript 愿意改变自己的语法，以符合作为类型化 JavaScript 的标准化内容。

就个人而言，我不喜欢提议的新语法，但我理解其基本原理。新的通用调用语法肯定是我可以接受的。对于 TypeScript 团队来说，接受修改语言以符合提议的想法是件好事。这对他们来说不是零成本的。这表明，要让这一提议落地，将有许多方面的妥协。很可能流量也会受到类似的影响。

## 结论

当你在网上看到关于类型注释/类型的各种讨论时，很明显有很多强烈的意见。该提案甚至还没有达到第一阶段(采纳所需的潜在 4 个阶段)。这可能是一个无法实现的特性，或者可能需要很长时间才能达成一致的设计。

就个人而言，我希望这最终会成为语言的一部分。我不仅喜欢运行原始的 JS，我还看到了通过允许类型直接存在于 JavaScript 中，将人们从 JavaScript 移植到 TypeScript 的好处。

据说预测是非常困难的，所以很难确切知道这项提议对语言和生态系统的长期影响。这肯定会降低使用 JavaScript 静态类型的门槛，因此，可能会导致更大的采用，从而减少用户领域的错误。时间会证明一切。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.*