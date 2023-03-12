# 是什么让理性如此伟大？

> 原文：<https://blog.logrocket.com/what-makes-reasonml-so-great-c2c2fc215ccb/>

### ReasonML 到底是什么？

ReasonML 是由脸书*创建的 OCaml 语言*的*语法扩展。理性的创造者没有创造一种全新的语言，而是选择建立在 [OCaml](https://ocaml.org/) 之上，这是一种久经考验的函数式系统编程语言，自 20 世纪 90 年代末就已经存在。*

其实 React 和 React(2018 web 开发的世界里需要零介绍)同根同源。许多最初的 React 原型都是用非常类似于 OCaml(标准 ml)的语言完成的，React 和 React 共用同一个创建者！

此外，相当长一段时间以来，脸书一直在 Messenger.com 问题上使用理性，因此，正如我们将看到的那样，语言的演变是一种实际需要，而不是崇高的理想。

与他们在 React 上所做的类似，脸书在实际添加语言之前，会在内部测试所有新添加的语言

就我个人而言，我喜欢这种关于理性的说法——这种语言是为了解决生产级应用程序中的现实问题而创建的。而且，正如你可能已经猜到的，生产并不总是“实验”的最佳场所。

### 是什么让理性如此伟大？

#### 它支持本机和 JavaScript 作为编译目标

这是一堆现代语言/框架正在寻找的圣杯。支持本机和 JavaScript 作为编译器目标允许代码“编写一次，在任何地方运行”。

因为 OCaml 已经编译成汇编，所以本地支持是内置的。Reason 支持通过 [BuckleScript](https://bucklescript.github.io/) 项目编译成可读的 JavaScript，这个项目是在彭博创建的，作为一种用 OCaml 编写前端的方式。因为 Reason 本质上是 OCaml，所以添加对 JavaScript 编译的支持是 OCaml 生态系统的“免费”支持。

因为 Reason 支持 JavaScript 作为编译目标，所以它有办法通过 FFI(外部函数接口)与现有的 JavaScript 代码对话。这确保了类型保持真实，同时允许您通过使用您已经知道并喜欢的库来加速开发。

事实上，因为这个 FFI 与 JavaScript 的互操作性，Reason 已经有了 [React 绑定](https://reasonml.github.io/reason-react/)！

#### 坚如磐石的系统

作为一个理性的前端 JavaScript 开发者，这根本不是我所习惯的事情。由于 JavaScript 是一种具有类型强制的动态类型语言，您不可避免地会遇到两种情况。

**运行时错误**

动态类型化的一个场景是由于类型不匹配导致的运行时错误。当你试图点击某个东西时，你见过`undefined is not a function`错误吗？还是`Cannot read property 'x' of undefined`？这两个错误都来自于试图以不应该使用的方式对代码中的部分进行操作。例如，在`null`上调用`Array.prototype.map`会抛出一个错误，在某些情况下*甚至会让你的应用程序*崩溃。

当然，我们绝对不希望类型错误使我们的应用程序崩溃。然而，避免这些错误是非常困难的，尤其是如果你有一个大型的应用程序，有很多来自后端 API 的动态数据。

**检查和测试**

这就把我们带到了动态应用程序中的第二个场景:大量的类型检查和测试，以确保流经应用程序的数据正是您所期望的。如果是这样的话，您将经常看到类似这样的代码:

```
// `myData` is expected to be an array of strings, but sometimes it can return as `null` from the backend
if (Array.isArray(myData)) {
  // operate on the data
}
```

然而，它并没有止步于来自 API 的动态数据。很多时候，当重构一个模块时，它与应用程序其余部分的交互方式可能会改变。如果你不做你的尽职调查，并且更新*依赖于*你重构的所有东西，你也有运行时错误的风险。

在这些场景中，您最好希望您有一个坚如磐石的测试套件来帮助您找出问题所在。在 JavaScript 中进行这些类型的重构可能是危险的，尤其是在较大的应用程序中。

然而，在像 Reason 这样的健全类型语言中，许多运行时问题被转化为编译时问题。你不必担心你的应用程序因为忘记添加额外的函数参数而崩溃，你会得到一个编译器错误。这意味着你可以省去所有的运行时类型检查，只需*编写你的代码*去做你想让它做的事情。

**打字稿、流程和冗长度**

现在你可能会想，“那么打字稿和流呢？”—毕竟，它们不会带来全新语法的开销。然而，尽管在类型化的 JavaScript 中*有可能*实现很多安全性，但这并不意味着这很容易*。*类型系统的强度往往取决于你，也就是开发人员，当你处于崩溃状态，编译器对你大喊大叫时，你更倾向于把你所有的代码都打成`any`类型，只是为了发布你的代码。此外，在我看来，在 TypeScript 中键入所有内容以实现额外的安全性可能会变得相当冗长。

Reason 的类型系统*坚如磐石，*并且因为编译器推断出你写的大部分类型，所以它不会很冗长。由于我一直在玩弄理性，我发现让编译器捕捉我的大多数错误，缩短反馈循环并向我展示我做错了什么是一种非常愉快的体验。点击一个处理程序不正确的按钮后，我得到的不是黑屏，而是一个编译器错误，告诉我*错误的确切位置，以及如何修复它。*

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

**重构**

最后，在一种健全类型的语言中，重构变得轻而易举。在 JavaScript 中，重构是危险的，除非你有一个可靠的测试套件。有了理性，你就可以改变效用函数，重塑对象结构，或者重命名任何变量。编译器会指出所有代码更改的地方，你所要做的就是跟着面包屑走。一旦你的程序编译成功，你就可以确信它实际上不会抛出任何运行时错误。

我记得看过一个关于 Messenger.com 脸书的案例研究，他们说在迁移了相当大一部分应用程序后，进行主要重构的时间从几天减少到了几个小时。

*注意:当和许多人谈论 Reason 时，我经常被问到的一个问题是一个实体类型系统是否可以被单元和集成测试覆盖所取代。我的回答大多是视情况而定。你可以通过测试获得 100%的类型覆盖率，但是你要花很多时间为边缘情况编写测试(如果我传递一个字符串作为参数呢？一个数组？一个整数？).此外，您可能需要记录流过程序的类型(类似于 JSDoc ),以便更容易跟踪。类型系统不会捕捉你程序中的所有错误，也不应该取代单元/集成测试(你仍然需要测试你的业务逻辑)。然而，它可以帮助测试所有的边缘情况，并且您将更快地获得关于突破性变更的反馈。试一试——我不认为我会喜欢这个类型系统，我感到惊喜。*

#### 默认情况下是不可变的和功能性的，但是仍然支持变异和副作用

默认情况下，Reason 和 OCaml 的语法支持纯函数范式。

例如，Reason 如何处理带有多个参数的函数。

```
let myFunction = (a, b, c, d) => a + b + c + d;
```

Reason 自动生成带有多个参数的函数，所以这个函数会编译成这样:

```
let myFunction = a => b => c => d => a + b + c + d;
```

自动的 curry 使得在你进行的过程中部分地应用参数变得非常容易，而不是做一些代码体操来确保当你实际调用函数时，你已经得到了所有需要的数据。直到最后一个*参数*被应用，这个函数才真正被执行。

其次，Reason 中的大部分数据结构和类型都是*默认不可变的。*当你通过`let`声明一个变量时，它是不可变的——你不能重新分配变量或改变它的值。*记录*(相当于 Reason 中的一个对象)中的字段不能被修改，你必须创建一个新的记录来覆盖你想要修改的字段。

话虽如此，有时你只需要把事情做好，而解决手头问题的最清晰的方法恰好是写一点命令性代码或者在你的程序中引入一点不变性。Reason 允许你将变量声明为可变的，但是你必须明确地说“我希望这个东西是可变的，我不是无意中使它发生了变异”。

下面是语法的样子:

```
/* immutable variable */
let num = 1;
/* mutable variable */
let mutableNum = ref(1);
mutableNum := 2 /* Reassign the value of the variable */
```

可变记录字段共享一个类似的语法，强制您将字段声明为可变的:

```
type record = { 
  a: int, 
  mutable b: int,
}
let myRecord = { a: 1, b: 2 };
myRecord.b = 4; /* We can change b, but not a! */
```

默认情况下冻结我们的记录和变量可以防止许多意外错误。然而，有了*能力*去做像突变和命令式循环这样的事情(Reason 仍然支持`for`循环，你不需要为每件事递归！)把另一个工具放在你的工具袋里。

Reason / OCaml 在默认情况下是纯的，这很棒——纯代码往往更清晰，更容易跟踪。然而，纯程序在某些时候需要产生副作用:它们需要写入控制台，呈现到 DOM，或者进行 API 调用。拥有编写不纯的副作用代码的能力让我们能够编写真正的程序并投入生产。总的来说，这种语言感觉非常实用——鼓励纯粹的功能性代码，但在需要时允许命令性代码。

#### 如果您来自 JavaScript 领域，语法不会让人感觉很陌生

所以，当然，默认的声音类型和函数范例很棒，但是学习一门新语言真的值得吗？如果真的很勤奋，坚持使用我已经知道的工具和库，不是更容易吗？

在这种情况下，不是真的。Reason 背后的团队特别注意让语法对编程初学者和从 JavaScript 生态系统中迁移出来的人都友好。语法如此接近，以至于下面的函数在*中对 JavaScript 和 Reason 都有效*

```
let add = (a, b) => a + b;
```

诚然，这个例子*非常简单*，但是它表明 Reason *中的语法感觉非常像 JavaScript。*对我来说，感觉好像你用了很多 JavaScript，清理了语法，去掉了类，并添加了一些功能性的好东西(比如`|>`管道语法，尽管 JavaScript 可能很快也会这样)。

然而，如果你来自 JavaScript，那么 Reason *的语法中是否有一些东西是外来的，但是 Reason docs 做了一项*惊人的工作*来解释这些新的语言结构如何工作以及如何有效地使用它们。*

关于 Reason 最酷的语言特性之一是组合了*变体*和*模式匹配*。

一个*变体*是一种特殊的类型——它存在于其他语言中，但是如果你来自 JavaScript，它可能有点陌生。TypeScript 中最接近变体类型的是*枚举*。

下面是变体语法的样子:

```
type vehicle =
  | Car
  | Plane
  | Boat;
```

但是，变种不止于此！它们也可以携带参数，就像函数一样！这允许我们将数据*和枚举一起传递。*

```
type vehicle = 
  | Car(string)
  | Plane
  | Boat;
let bmw = Car("BMW");
```

即使就其本身而言，变体类型也是超级强大的，但是当我们将 Reason 的*模式匹配*放入组合中时，我们看到的是天作之合。

模式匹配看起来类似于 JavaScript 中的`switch/case`语句，语法稍微简洁一些。我们可以对我们的 variant 类型进行模式匹配，并在每种情况下抛出一个字符串(稍后您会注意到我们如何能够将参数用于 variant)。

```
let action = switch(value) {
  /* `++` is the Reason syntax for string concatenation */
  | Car(make) => "It's a " ++ make
  | Plane => "It's a plane!"
  | Boat => "It's a boat!"
}
```

如果我们忘记处理我们的`switch`语句的`Boat`分支，编译器将抛出一个警告，告诉我们还没有处理所有可能的情况！这鼓励我们处理*每一个可能的场景*或者在我们的模式匹配中创建默认案例。

然而，魔法并不止于此。我们可以对任何合理的值进行模式匹配，包括数组、整数等等。

```
/* Pattern-matching on an array */
switch(arr) {
  | [] => "It's empty"
  | [a] => "Only 1 item"
  | [a, b] when b == 2 => "2 items, and the 2nd is 2!"
  | _ => "all other cases get handled here!"
}
```

在 Reason 语法中有很多其他的好东西，所以如果你有兴趣看一看，可以看看这个比较 Reason 和 JavaScript 的[备忘单](https://reasonml.github.io/docs/en/syntax-cheatsheet)。

### 好的，你已经说服我了…我该如何开始呢？

如果这篇文章让你对 Reason 感到兴奋，并且你正在寻找开始，我已经策划了几个链接，让你立刻开始运行！

首先，去看看[原因文档](https://reasonml.github.io/docs/en/what-and-why)。它们写得非常好，并在不断改进，它们将让您熟悉语法背后的设计决策、最佳实践和未来目标。

此外，如果您对使用 Reason 进行 web 开发感兴趣，您肯定也想看看 BuckleScript 文档。最后，如果你想在 React 应用程序中使用 ReasonReact，这里有相关的教程和文档！😀

最后，如果你在寻求帮助，请不要犹豫，发微博给我或者评论这篇文章！你也可以在[原因不和频道](https://discordapp.com/invite/reasonml)上插话，那里的人很好。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.