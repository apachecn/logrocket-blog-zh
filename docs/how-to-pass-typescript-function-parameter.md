# 如何将 TypeScript 函数作为参数传递

> 原文：<https://blog.logrocket.com/how-to-pass-typescript-function-parameter/>

**目录**

作为一种编程语言，JavaScript 依赖于一级函数的概念，这意味着函数被视为任何其他变量，如数字、字符串或数组。这样做的好处之一是，函数可以传递给其他函数，从函数返回，或者分配给变量，以便以后需要时调用。

这个特性在异步代码中被大量使用，其中函数经常被传递给异步函数，通常被称为[回调](https://www.w3schools.com/js/js_callback.asp)。但是当我们使用 TypeScript 时，这可能很难使用。

TypeScript 为我们提供了添加静态类型和[转换检查](https://dev.to/kealanparr/compiling-vs-transpiling-3h9i)的巨大好处，它可以帮助我们更好地记录我们期望在函数中加入什么类型的变量——但是如果我们需要传递函数会发生什么呢？

显然，我们需要对这些函数进行类型化，但是我们如何对它们进行类型化，以及如何在 TypeScript 中传递函数呢？

在本教程中，您将了解 TypeScript 函数以及如何在您的应用程序中将它们作为参数传递。

## 什么是 TypeScript 函数类型？

键入简单变量可能是大多数 TypeScript 开发人员熟悉的事情。但是为一个函数构造一个类型有点困难。

一个[函数类型](https://www.typescriptlang.org/docs/handbook/functions.html#writing-the-function-type)(注意:这个链接重定向到旧的 TypeScript 文档，但是它有一个比新的更清晰的例子)由函数接受的参数类型和函数的返回类型组成。

我们可以举一个[非常简单的例子](https://www.typescriptlang.org/play?#code/MYewdgzgLgBNBOBLMBzRAzAnjAvDAFAKYA2MAXDAIZiYCU5cUSquAfDAN4zyFQCu8MDBIwA1DABEEmAF8YAWABQoSLDB8AtgCNCSLLgIiK1Og3XbdbTt14ChAOU074RYvRlKlxXjCiFoBgjIaFgA3Ep+AXjmzhiYoUA)来说明这一点:

```
const stringify = (el : any) : string => { return el + "" } 
const numberify = (el : any) : number => { return Number(el) }

let test = stringify;
test = numberify;

```

上面的例子，如果用 JavaScript 实现，将会很好地工作，没有任何问题。

但是现在我们已经使用了 TypeScript，当我们试图转换代码时会抛出错误。

```
- Type '(el: any) => number' is not assignable to type '(el: any) => string'.
-   Type 'number' is not assignable to type 'string'.

```

这里抛出的错误消息非常具有描述性:`stringify`和`numberify`函数是不可互换的。

它们不能互换地赋给`test`变量，因为它们有冲突的类型。它们收到的参数是相同的(一个类型为`any`的参数)，但是因为它们的返回类型不同，所以我们会收到错误。

我们[可以改变这里的返回类型](https://www.typescriptlang.org/play?ssl=5&ssc=18&pln=1&pc=1#code/MYewdgzgLgBNBOBLMBzRAzAnjAvDAFAKYA2MAXDAIZiYCU5MYArgLYBGh8uAfDAN4x4hKE3hgYARhgBfGAFgAUKEixm7Thmx4ipCtToM1HLjl4ChIsTAByrYzvrTFi4sJhRC0XHChJUmgG5FDy88Iw0sAKA)来证明我们的理论是正确的。

```
const stringify = (el : any) : number => { return 1 } 
const numberify = (el : any) : number => { return Number(el) }

let test = stringify;
test = numberify;

```

上面的代码现在像预期的那样工作，唯一的不同是我们改变了`stringify`函数来匹配`numberify`函数的类型。事实上，返回类型破坏了这个例子。

## 在 TypeScript 中使用参数号

有趣的是，许多其他语言不仅基于参数类型和返回类型，还基于函数的参数数量来创建这些函数类型。

让我们制作最后一个示例来扩展我们上一个工作示例。

```
const stringify = (el : any, el2: number) : number => { return 1 } 
const numberify = (el : any) : number => { return Number(el) }

let test = stringify;
test = numberify;

```

熟悉其他语言的开发人员可能会认为上面的函数示例是不可互换的，因为它通常被称为[函数重载](https://en.wikipedia.org/wiki/Function_overloading)。

尽管这个例子没有抛出错误，但是它在 TypeScript 中是完全合法的，因为 TypeScript 实现了所谓的 [duck typing](https://stackoverflow.com/questions/4205130/what-is-duck-typing) 。

这是一个小注意事项，但是记住这一点很重要:在 TypeScript 中，函数的类型定义中不使用参数的数量。

## 在 TypeScript 中键入我们的函数示例

现在我们确切地知道了如何为我们的函数构造类型。我们只需要确保我们输入了在 TypeScript 中传递的函数。

让我们一起再来解决一个失败的例子。

```
const parentFunction = (el : () ) : number => { return el() } 

```

上面的例子不管用，但是它抓住了我们需要的东西。

我们需要将它传递给父函数(回调函数),稍后可以调用它。那么，我们需要改变什么呢？

1.  我们需要键入`el`函数参数
2.  我们需要输入传递给`el`函数的参数(如果需要的话)

完成此操作后，我们的示例应该如下所示:

```
const parentFunction = (el : () => any ) : number => { return el() } 

```

这个具体的例子不需要参数，但是如果需要的话，它应该是这样的:

```
const parentFunction = (el : (arg: string) => any ) : number => { return el("Hello :)") } 

```

这个例子相对简单，以便于解释 TypeScript 函数的概念，但是如果您有更复杂的类型，您可能需要花费大量的时间来输入所有的内容。

社区维护了大量 TypeScript 中常用的高质量开源类型，称为 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) ，它可以帮助您简化和加快您需要使用的类型。

## 结论

我希望本文对您有所帮助，让您更好地理解将函数作为参数传递给其他函数的 TypeScript 环境。

回调通常依赖于这种方法，所以在任何成熟的 TypeScript 代码库中，您都会经常看到回调的大量使用。编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.