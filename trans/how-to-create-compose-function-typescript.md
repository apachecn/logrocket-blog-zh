# 如何在 TypeScript 中创建合成函数

> 原文：<https://blog.logrocket.com/how-to-create-compose-function-typescript/>

在 TypeScript 中组合函数是函数式编程中的一个核心概念，它将多个函数组合成一个函数，该函数可以执行您可能需要的任意数量的任务。

函数组合可以用许多编程语言实现，包括 TypeScript。在本文中，我们将学习如何在 TypeScript 中创建类型化的`compose`和`pipe`函数，以及如何使用这些函数在 TypeScript 中执行函数合成。

*向前跳转:*

## 打字稿功能组合

TypeScript 中的函数组合可以通过将一个函数的输出作为输入传递给另一个函数来完成。

这个过程可以用多个函数重复，形成一个函数链，可以很容易地组合在一起执行更复杂的任务。

函数组合可用于创建更具可读性和可维护性的代码，因为它允许您定义小的、可重用的函数，这些函数可以很容易地组合起来执行更大的任务—这有助于减少代码重复，并使理解和调试代码变得更容易。

函数式编程中有两种常见的函数组合方法:`compose`和`pipe`。`compose`功能从右向左组合功能，而`pipe`功能从左向右组合功能。

选择使用哪种方法取决于手头任务的具体需求。当我们进入一个用例时，您应该理解什么时候使用这两种方法。

## `compose`和`pipe`功能是什么？

`compose`和`pipe`函数是高阶函数，它们接受一个或多个函数作为参数，并返回一个组合了输入函数功能的新函数。

换句话说，它把功能拉出来，让事情变得更干净、更容易处理。

### `compose`功能

`compose`函数从右到左组合函数，因此每个函数的输出作为输入传递给链中的下一个函数。

例如，让我们来看看以下函数:

```
function add(x: number): number {
  return x + 5;
}

function multiply(x: number): number {
  return x * 10;
}

function divide(x: number): number {
  return x / 2;
}

```

我们可以使用`compose`函数来创建一个新函数，它首先将参数相乘，然后将它们相加，最后将结果除以 2，如下所示:

```
function compose<T, U, V, Y>(f: (x: T) => U, g: (y: Y) => T, h: (z: V) => Y): (x: V) => U {
  return (x: V) => f(g(h(x)));
}

const composedFunction = compose(
  divide,
  add,
  multiply
);

console.log(composedFunction(4)); // returns (4 * 10 + 5) / 2 = 22.5

```

上面对`compose`函数的定义定义了四个可能的类型参数，我们用它们来定义三个函数参数和返回类型。

这个定义很容易混淆，但是我们将在下一节中使用`Array.prototype.reduce`方法和泛型类型定义来解决这个问题。

### `pipe`功能

`pipe`函数从左到右组合函数，因此每个函数的输出作为输入传递给链中的前一个函数。

使用与前面相同的示例函数，我们可以使用`pipe`函数创建一个新函数，该函数首先将其参数相除，然后将它们相加，最后将结果乘以 2。看一看，这里:

```
function pipe<T, U, V, Y>(f: (x: T) => U, g: (y: U) => V, h: (z: V) => Y): (x: T) => Y {
  return (x: T) =>  h(g(f(x)));
}

const pipedFunction = pipe(
  divide,
  add,
  multiply
);

console.log(pipedFunction(4)); // returns ((4 / 2) + 5) * 10 = 70

```

## 使用`Array.prototype.reduce`创建一个`compose`函数

在本教程中，我们将使用`Array.prototype.reduce`方法来创建自定义函数并将它们链接到一个`compose`函数中。

`Array.prototype.reduce()`方法是一个高阶函数，它将给定的函数应用于数组的每个元素，产生一个输出值。用于减少数组元素的函数称为“reducer”函数，它接受两个参数:“accumulator”和正在处理的当前元素。

累加器是先前调用 reducer 函数的结果；它用数组的第一个元素初始化，或者用可选的初始值(如果提供)初始化。

现在，让我们应用上面关于`Array.prototype.reduce()`的定义来创建一个`compose`函数，如下所示:

```
const compose = <T>(fn1: (a: T) => T, ...fns: Array<(a: T) => T>) =>
  fns.reduce((prevFn, nextFn) => value => prevFn(nextFn(value)), fn1); 

```

这段代码定义了一个名为`compose`的函数，它接受一个函数列表(`fn1`、`fns`)，并返回一个执行这些函数组合的新函数。

第一个函数`fn1`被定义为接受类型`T`的值并返回值`T`的函数。

其余的函数`fns`被定义为一个函数数组，它也接受类型为`T`的值，并返回类型为`T`的值。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

> **注意，**注意我们执行的顺序:`nextFn`函数在`prevFn`函数之前。

如果我们用这个新的定义来对照前面的例子，将会产生同样的结果，但是，让我们用下面的例子来看看这些函数是如何可视化地链接在一起的:

```
const func1 = (v: string) => `func1(${v})`;
const func2 = (v: string) => `func2(${v})`;
const func3 = (v: string) => `func3(${v})`;
const composedFunction = compose(func1, func2, func3);
console.log(composedFunction("value")); // func1(func2(func3(value))) 

```

## 使用`Array.prototype.reduce`创建管道功能

管道函数类似于 Unix 管道操作符，其中一个命令的输出作为输入传递给下一个命令。

我们还将使用`Array.prototype.reduce()`来创建一个类型化的`pipe`函数，但是相反，我们将颠倒回调参数的执行顺序，如下所示:

```
const pipe = <T>(fn1: (a: T) => T, ...fns: Array<(a: T) => T>) =>
  fns.reduce((prevFn, nextFn) => value => nextFn(prevFn(value)), fn1); 

```

上面的代码与`compose`函数相同，除了我们翻转了回调参数。

我们可以使用前面用于`compose`函数的例子来测试它，这将给出链式操作的`pipe`版本:

```
const pipedFunction = pipe(func1, func2, func3);
console.log(pipedFunction); // func3(func2(func1(value))) 

```

既然我们已经演示了如何在 TypeScript 中构造一个函数，那么在结束本文之前，让我们探索一下如何通过扩展函数的参数来修改`pipe`函数。

## 扩展管道函数的参数

目前，`pipedFunction`只定义了一个参数，但是会有这样的情况，我们需要处理多条输入数据，并将汇总的输出传递给下一个函数。

这就提出了一个问题，我们如何设计`pipe`函数来提供这样的支持。让我们来了解一下；请看下面:

```
const pipe = <T extends any[], U>(
  fn1: (...args: T) => U,
  ...fns: Array<(a: U) => U>
) => {
  const piped = fns.reduce((prevFn, nextFn) => (value: U) => nextFn(prevFn(value)), value => value);
  return (...args: T) => piped(fn1(...args));
};

```

在上面的代码片段中，我们将`fn1`重新定义为一个函数，它接受数量可变的类型`T`(任何类型的元组)的参数，并返回类型`U`的值。`fns`参数仍然是一个 rest 参数，它表示一个函数数组，该数组接受一个`U`类型的参数，并返回一个`U`类型的值。

`pipe`函数返回一个新函数，该函数接受可变数量的`T`类型的参数，并返回一个`U`类型的值。

这个新函数按顺序执行管道中的所有函数，从`fn1`开始，到`fns`中的最后一个函数结束，同时将每个函数的输出作为输入传递给管道中的下一个函数。

让我们通过向第一个函数提供多个参数来重新定义测试示例，从而测试新的定义:

```
const func1 = (v1: string, v2: string) => `func1(${v1}, ${v2}, ...)`;

const pipedFunction = pipe(func1, func2, func3);
console.log(pipedFunction); // func3(func2(func1(value1, value2, ...))) 

```

在这方面，你可能会发现自己对`compose`函数感到疑惑；我们也能延长它吗？嗯，不可能像定义`pipe`函数那样定义`compose`函数的类型。这是因为`pipe`的类型由传递给它的第一个函数的类型决定，而`compose`的类型由最后一个函数的类型决定。

然而，以这种方式定义`compose`的类型需要在参数列表的开头使用 rest 参数，这是目前[不支持的。](https://github.com/microsoft/TypeScript/issues/1360)

## 结论

总之，`compose`和`pipe`函数是函数式编程中的有用工具，用于将多个函数组合成一个函数或函数链。

这些函数可以通过定义小的、可重用的函数并组合它们来执行更复杂的任务，从而帮助您创建更可读、更整洁和更易维护的代码。

在本文中，我们学习了如何使用泛型和内置的`Array.prototype.reduce()`方法在 TypeScript 中创建类型化的`compose`和`pipe`函数，还学习了如何使用这些函数在 TypeScript 中执行函数组合。

无论您是函数式编程的新手还是有经验的开发人员，理解`compose`和`pipe`函数的概念可以帮助您编写更高效和有效的代码。请告诉我您为您的 TypeScript 项目或一般项目创建合成函数的经历！

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.