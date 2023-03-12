# TypeScript - LogRocket 博客中命名元组的真实用例

> 原文：<https://blog.logrocket.com/use-cases-named-tuples-typescript/>

## 介绍

元组扩展了数组数据类型的功能。使用元组，我们可以很容易地构造特殊类型的数组，其中元素相对于索引或位置是固定类型的。由于 TypeScript 的性质，这些元素类型在初始化时是已知的。本质上，使用元组，我们可以定义可以存储在数组中每个位置的数据类型。

元组更像是高级数组，具有确保类型安全的额外特性，特别是在我们需要考虑包含固定数量的多种已知类型元素的列表的情况下。

数组和元组之间的主要区别在于，当我们给元组赋值时，这些值必须以相同的顺序匹配元组声明中定义的类型。另一方面，数组可以使用`any`类型或按位 OR ( `|`)运算符支持多种类型，但是元素的顺序或结构不起作用。

在本教程中，我们将介绍 TypeScript 中命名元组的真实用例及应用。我们将了解这种数据类型的重要性，以及为什么在某些情况下它是首选。

最终，我们将亲眼看到这种数据类型如何有助于改进 TypeScript 语言，根据改进的文档、可维护的代码和开发人员的生产力，允许更严格的规则。

### 先决条件

在我们开始之前，读者应该熟悉 TypeScript 和一般类型的基础知识。要了解关于这个主题的更多信息，请查看 TypeScript 文档的这一[部分](https://www.typescriptlang.org/docs/handbook/basic-types.html)。现在让我们开始吧。

## 数组和元组数据类型简介

在我们开始探索 TypeScript 中元组的用例之前，让我们简单地探索一些可以使用数组的简单案例，以及元组如何能够很好地(甚至更好地)适应相同的场景。

在 TS 中，我们可以声明一个特定数据类型的数组。例如，我们可以通过指定元素的类型后跟方括号来声明一个数字数组:`[]`。让我们看看如何做到这一点:

```
let arr: number[];

arr = [1, 2, 3];
```

从上面的例子中我们可以看到，为了确保类型安全(这使得我们的代码更容易注释和文档化)，我们需要使用数组，这允许像这样的情况，我们有一个特定数据类型的列表。事实上，这是像 TypeScript 这样的类型化语言的本质。

对于具有多种数据类型的数组，我们可以利用`any`类型或`|`(按位 or)运算符。但是，在这种情况下，数据的顺序并不是一成不变的。让我们看下面的例子:

```
let arr: (string | number)[];
arr = ['Alex', 2020];
console.log(arr);
```

从上面的例子中，我们可以决定在字符串之前传递数字，它仍然有效。在这种情况下，当数组被实例化时，我们传递数据的顺序并不重要，只要我们有指定类型的组合。这正是元组旨在解决的问题。

有了元组，我们可以有一个多种数据类型的列表，其中我们传递数据类型的顺序必须符合声明元组时的顺序。本质上，元组的结构需要保持不变。让我们看一个例子来更好地理解这个概念:

```
let tup: [string, number];

tup = ['Alex', 19087]
```

在上面的例子中，我们可以看到我们用两种基本数据类型声明了一个元组:字符串和数字。注意，当我们调用`tup`变量时，我们还必须按照元素类型声明的顺序传递它们。本质上，我们不能在`index 0`有一个数字，在`index 1`有一个字符串，就像这样:

```
tup = [19087, 'Alex]
```

如果我们这样做了，我们将得到如下所示的错误:

```
TSError: ⨯ Unable to compile TypeScript:
index.ts:6:8 - error TS2322: Type 'number' is not assignable to type 'string'.

6 tup = [19087, 'Alex']
         ~~~~~
index.ts:6:15 - error TS2322: Type 'string' is not assignable to type 'number'.

6 tup = [19087, 'Alex']
```

从前面的例子中我们可以看到，我们声明了一个数字数组并用值初始化它。只要我们只处理数字类型的元素，这种方法就有效。

为了考虑具有多种数据类型的数组，我们可以利用`any`类型或`|`操作符，尽管在这种情况下，数据的顺序或结构没有保证，这可能不是我们想要的。

然而，使用元组，我们可以确保数据类型的严格性以及我们想要传递的数据的顺序。元组允许在具有固定数量元素的元素类型周围指定已知的类型边界。

## 类型脚本元组的用例

因为元组允许我们在数组中定义固定类型和顺序，所以当处理以顺序方式彼此相关的数据时(顺序很重要)，元组是最好的。这样，我们可以很容易地以预定的方式访问元素，使我们期望的响应在行为上是可预测的。

下面，我们将基于 [3.0 版本](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-0.html#tuples-in-rest-parameters-and-spread-expressions)探索 TS 中元组类型的更多用例，这些用例通常围绕在函数签名中提取和传播参数列表。

### 1.在 rest 参数中使用元组

[rest 参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)语法将参数收集到一个数组变量中，然后扩展它们。随着 TypeScript 3.0 的发布，我们现在可以将 tuple 类型的 rest 参数扩展为离散参数。这意味着当一个`tuple`类型被用作 rest 参数时，它会被展平到参数列表的其余部分中。

> 简单来说，当 rest 参数是元组类型时，元组类型可以扩展为一系列参数列表。

考虑下面的例子:

```
declare function example(...args: [string, number]): void;
```

rest 参数将元组类型的元素扩展为离散参数。当调用该函数时，表示为 rest 参数的`args`被展开，看起来与下面的函数签名完全一样:

```
declare function example(args0: string, args1: number): void;
```

因此，rest 参数语法将“参数溢出”收集到数组或元组中。总之，元组类型迫使我们将适当的类型传递给各自的函数签名。更多细节可以在 TypeScript 3.0 发布博客文章[这里](https://devblogs.microsoft.com/typescript/announcing-typescript-3-0/#tuples-and-parameters)找到。

### 2.用元组扩展表达式

[spread 语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)将数组或对象的元素扩展到它的元素中。使用 TypeScript 3.0，spread 运算符还可以扩展元组的元素。当函数调用包括元组类型的扩展表达式作为自变量时，扩展表达式被扩展为对应于元组类型的元素的自变量序列。

让我们看下面的例子:

```
type Value = [number, number];

const sample = (...value: Value) => {
  // do  something with value here
};

// create a type
let sampleTuple: Value;

sampleTuple = [20, 40];

// Passing the values as literals:
sample(20, 40);

// Passing indexes to the corresponding sampleTuple tuple
sample(sampleTuple[0], sampleTuple[1]);

// Using the spread operator to pass the full sampleTuple tuple
sample(...sampleTuple);
```

> **N.B.** 从上面的例子可以看出，我们已经声明了一个 tuple 类型，并将其作为参数传递给了函数 signature。

当函数被调用时，我们可以直接传递参数或者通过它们各自的索引。但是，对于将元组作为参数传递给函数调用来说，使用 spread 操作符是一个快速而干净的选择。

由于扩展运算符的性质，参数被扩展为与元组类型的元素相对应的参数列表。

### 3.破坏值

因为元组是隐藏的数组，所以我们可以像析构数组一样析构它们。值得注意的是，析构变量获取相应元组元素的类型。让我们看一个例子:

```
let tuple: [number, string, boolean];

tuple = [7, "hello", true];

let [a, b, c] = tuple; 

// a: number, b: string, c: boolean
```

### 类型脚本元组的其他用例

*   当我们打算从函数调用中同时返回响应和错误结果时，可以使用元组来加强类型安全
*   我们可以使用元组将相似的数据或有效载荷分组在一起，例如笛卡尔坐标，在自定义 csv 文件中强制元素类型
*   我们还可以使用元组来创建由多个其他类型组成的特定类型
*   元组通常可以用作函数签名或调用的参数列表

> **N.B.** ，参数列表不仅仅是类型的有序列表。为了使元组作为参数列表工作，在 TypeScript 3.0 版本中做了一些改进。你可以在这里查看[。](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-0.html?o=60894&t=1604332880&z=222920&sh=1#tuples-in-rest-parameters-and-spread-expressions)

## 结论

TypeScript 元组类似于具有固定数量元素的数组。它们为我们提供了一个固定大小的容器，可以存储多种类型的值，其中顺序和结构非常重要。

当我们确切知道一个数组中允许多少种类型时，最好使用这种数据类型。正如我们所知，指定超出原始定义长度的索引将导致 TypeScript 编译器出错。

注意，虽然可以通过元组元素的索引来修改它们的值，但是我们必须确保在声明元组变量时匹配所提供的类型。这是因为一旦声明，我们就不能改变元组中元素的类型甚至大小。

有了本文中强调的特性，就有可能设计强类型的高阶函数，这些函数可以转换函数及其参数列表，并在本质上确保一个健壮的、文档完善的、可维护的代码库，这是我们使用 TypeScript 的核心原因。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.