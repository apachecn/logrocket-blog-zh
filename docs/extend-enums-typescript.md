# 如何在 TypeScript - LogRocket 博客中扩展枚举

> 原文：<https://blog.logrocket.com/extend-enums-typescript/>

TypeScript 深受开发人员社区的喜爱，原因有很多，其中之一是因为它提供了对其中编写的代码的静态检查。

在开发生命周期的早期发现问题可以节省调试随机、模糊错误的时间，这些错误有时会在使用像 JavaScript 这样的动态语言时突然出现。

TypeScript 有助于提高代码的可预测性和文档化程度，使重构更加容易，并有助于减少您在生产应用程序运行时可能遇到的潜在错误。在过去的五年里，它 93%的开发者满意率和暴涨的[使用率](https://www.typescriptlang.org/)证明了它的受欢迎程度和影响力。

对 TypeScript 至关重要的一种语言机制是枚举。在本文中，我们将讨论:

## TypeScript 中的枚举是什么？

有趣的是，枚举不像大多数 TypeScript 那样是打字的一个特性——事实上，它们是增强语言的少数新特性之一。

枚举允许开发人员为变量定义一组严格的选项。例如:

```
enum Door {
  Open,
  Closed,
  Ajar // half open, half closed
}

```

enum 默认为 number enums，所以上面的 enum 本质上是一个以`0`、`1`和`2`为键的对象，这一点我们可以在 [transpiled](https://dev.to/kealanparr/compiling-vs-transpiling-3h9i) JavaScript 代码中看到。

```
"use strict";
var Door;
(function (Door) {
    Door[Door["Open"] = 0] = "Open";
    Door[Door["Closed"] = 1] = "Closed";
    Door[Door["Ajar"] = 2] = "Ajar"; // half open, half closed
})(Door || (Door = {}));
console.log(Door.FullyOpened);

```

在 TypeScript 中，还可以使用字符串枚举，如下所示:

```
enum Door {
  Open = "open",
  Closed = "closed",
  Ajar = "ajar" // half open, half closed
}

```

如果您随后使用这个`Door`枚举，您可以确保变量只使用枚举中指定的三个选项。所以，你不可能偶然错误地赋值，或者这样容易地产生错误。

如果您尝试使用另一个变量，它将抛出如下类型错误:

```
enum Door {
  Open = "open",
  Closed = "closed",
  Ajar = "ajar" // half open, half closed
}
console.log(Door.FulyOpened)

```

`Property 'FullyOpened' does not exist on type 'typeof Door'.`

## 为什么我们需要扩展一个枚举？

扩展是面向对象的四个支柱之一，也是 TypeScript 中的一个语言特性。扩展一个枚举允许你复制一个变量定义并添加一些额外的东西。

例如，您可能会尝试这样做:

```
enum Door {
  Open = "open",
  Closed = "closed",
  Ajar = "ajar" // half open, half closed
}

enum DoorFrame extends Door { // This will not work!
  Missing = "noDoor"
}

console.log(DoorFrame.Missing)

```

然后，我们可以在一个枚举中添加额外的属性，或者甚至将两个枚举合并在一起，仍然可以在我们的枚举上获得严格的类型，同时还可以在它们被定义后更改它们。

但是请注意，上面的代码片段不起作用！它无法传输文件并抛出四个不同的错误。

## 你能扩展 enums 吗？

简单的回答是不，你不能扩展枚举，因为 TypeScript 没有提供语言特性来扩展它们。但是，您可以利用一些变通方法来实现继承所能实现的效果。

### TypeScript 中的类型联合

```
enum Door {
  Open = "open",
  Closed = "closed",
  Ajar = "ajar" // half open, half closed
}

enum DoorFrame {
  Missing = "noDoor"
}

type DoorState = Door | DoorFrame; 

let door: DoorState;
door = Door.Ajar
console.log(door) // 'ajar'
door = DoorFrame.Missing
console.log(door) // 'noDoor'

```

在上面的代码块中，我们使用了一个[联合类型](https://www.typescriptlang.org/docs/handbook/unions-and-intersections.html)。该联合的作用就像一个“或”，这仅仅意味着`DoorState`类型要么是`Door`类型，要么是`DoorFrame`类型。

这意味着`DoorState`可以互换使用两个枚举中的任何一个变量。

然而，一个重要的警告是，它只能使用两个枚举的类型。在 TypeScript 中，不可能使用像`ajar`和`noDoor`这样的值。你需要使用像`DoorFrame.Missing`这样的类型，这是一个限制。

### 扩展语法

我们在前面的 transpiled 代码中已经看到，enum 变成了一个 JavaScript 对象，带有您的 enum 指定的键和值。

在 TypeScript 中，如果我们愿意，我们可以编写纯 JavaScript。事实上，这是 TypeScript 的一大优势。例如，您可以将所有的`file.js`重命名为`file.ts`，并关闭对代码的编译器检查。只要运行编译/传输步骤，一切都会很好，代码没有任何变化。

我们可以利用这一点，因为我们知道当我们的 enum 转换成 JavaScript 时，它将是一个 JavaScript 对象文字，并使用 spread 语法，[如下所示](https://wringing.it/blog/typescript-enum-composition/):

```
enum Move {
  LEFT = 'Left',
  RIGHT = 'Right',
  FORWARD = 'Forward',
  BACKWARD = 'Backward'
}
const myMove = {
  ...Move,
  JUMP: 'Jump'
}

```

然而，这个解决方案已经被描述过了，因为它不如 union 类型的解决方案好，因为它不如我们的第一个解决方案健壮。这是因为枚举的“组合”发生在运行时，而当我们使用类型联合时，类型检查可以发生在编译/转换时，而不是运行时。

## TypeScript 枚举最佳实践

我们已经讨论了如何在 Typescript 中扩展枚举，但是枚举并不是解决所有问题的灵丹妙药。如果使用不当，枚举会降低代码的可读性、可伸缩性和可维护性，而不是改善代码。

因此，让我们介绍一些在 TypeScript 中使用枚举时的最佳实践和常用模式。

### 1.避免异源枚举

我已经解释了我们可以有这样的字符串枚举:

```
enum Seasons {
  Summer = "Summer",
  Winter = "Winter",
  Spring = "Spring",
  Fall = "Fall"
}

```

除此之外还有这样的数字枚举:

```
enum Decision {
  Yes,
  No
}

```

但是，还有第三种类型的枚举，你可能不知道，称为[异构枚举](https://www.typescriptlang.org/docs/handbook/enums.html#heterogeneous-enums)。这是您可以在同一个枚举中使用字符串和数字枚举的地方。

文档中的一个例子是:

```
enum BooleanLikeHeterogeneousEnum {
  No = 0,
  Yes = "YES",
}

```

值得注意的是，即使是文档也不鼓励这种做法，因为在这种情况下，使用这种方法意味着您可能需要:

*   重新考虑这两个变量之间的关系
*   创建两个单独的枚举
*   使它们都符合一种数据类型

### 2.“枚举作为配置”反模式

有时，代码功能可能会被强制遵守枚举选项，这可能会很快变成反模式。

下面是一个例子:

```
enum Operators {
  Add,
  Subtract
}
function calculate(op: Operators, firstNumber: number, secondNumber: number) {
  switch(op) {
    case Operators.Add: return firstNumber + secondNumber
    case Operators.Subtract: return firstNumber - secondNumber
  }
} 

```

上面的代码看起来相当简单和安全，因为我们的例子确实是简单和安全的。

但是在大型代码库中，当您像这样将实现细节严格绑定到枚举类型时，可能会导致一些问题:

*   您创建了两个真实的来源(如果枚举发生变化，枚举和函数都需要更新)
*   这种模式将在代码中传播元数据
*   代码块不再是通用的

如果您需要做类似上面的事情，一个更简单(也更简洁)的模式可能是这样的。

```
const Operators = {

  Add: {
    id: 0,
    apply(firstNumber: number, secondNumber: number) { return firstNumber + secondNumber }
  },

  Subtract: {
    id: 1,
    apply(firstNumber: number, secondNumber: number) { return firstNumber - secondNumber }
  }
}

```

你可以在这里或者这里阅读更多关于这个模式的内容[。](https://medium.com/thousandeyes-engineering/the-distributed-enum-anti-pattern-3ebb23cbc5d8)

### 3.枚举最能代表的数据类型

有一种方法可以将代码中使用的不同类型的数据组合在一起:离散变量或连续变量。

离散变量是在它们的表示之间有空格的数据，并且只有几个表示。这里有几个例子:

*   一周中的几天
    *   孟人
    *   周二
    *   结婚
    *   周四
    *   Fri
    *   坐
    *   太阳
*   季节
    *   夏天
    *   冬天
    *   春天
    *   秋天

离散数据是放置在枚举中的一个很好的候选者，它可以帮助代码清晰和重用。连续数据是指落入连续序列中的没有间隙的数据，如数字。根据测量结果，这些参数可能会有所不同:

*   某人的体重
*   汽车的速度

离散数据是很好的候选数据，可以在枚举中使用，而连续数据不应该在枚举中使用。你能想象年龄的枚举吗？

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
enum Age {
  Zero,
  One,
  Two,
  Three,
  Four,
  Five,
  Six
}

```

这不是一个放置在 enum 中的好选择，因为它需要不断地更新和修改，导致维护的噩梦。

您应该只在枚举中添加离散的、高度稳定的数据类型。

## 结论

我希望这篇文章对你有所帮助，让你更好地理解什么是枚举，它们解决的问题，合并两个枚举的用例，以及如何实现它！黑客快乐。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.