# 理解 Promise.all 在 JavaScript - LogRocket 博客中

> 原文：<https://blog.logrocket.com/understanding-promise-all-in-javascript/>

在 JavaScript 中引入承诺之前，我们对异步任务使用了大量回调。看到回调被用于异步任务是很常见的，因为许多开发人员可能仍然认为回调和承诺是相同的，但事实上，它们不是。

当承诺被原生引入 JavaScript 时，它绝对是一个游戏改变者。在很多项目中，回调的使用被运行异步任务的承诺所取代，承诺成为了它的主要替代品。承诺在某些方面类似于回调，但是更容易理解语法和代码。

在 JavaScript 中处理承诺时，我们有很多方法可以帮助我们。在本文中，我们将讨论`Promise.all`方法。

为了理解`Promise.all`方法是如何工作的，首先，我们需要理解 JavaScript 中的承诺是如何工作的。

## 承诺

JavaScript 是单线程的，这意味着我们一次只能运行一个代码块。它按顺序执行代码，并且在运行下一个代码之前必须完成代码的执行。

承诺表示异步操作的未来结果。JavaScript 中经常使用承诺来处理异步任务。

承诺是一个将在未来返回一个值的对象，它可以是一个解析的值，这意味着承诺是成功的，也可以是一个拒绝的值，这意味着发生了错误。承诺只会返回一次值，这意味着如果承诺返回一个错误，它只会返回一次。

[承诺有三种可能的互斥状态](https://github.com/domenic/promises-unwrapping):

*   履行——如果`promise.then(f)`会“尽快”给`f`打电话，承诺就履行了
*   拒绝——如果`promise.then(undefined, r)`将“尽快”呼叫`r`,则承诺被拒绝
*   待定——如果承诺既未履行也未被拒绝，则该承诺待定

有时我们可能会听到承诺是`settled`。这意味着这个承诺不是`fulfilled`就是`rejected`，`settled`不是一个状态，只是为了方便使用。

为了创建一个承诺，我们使用了`new`关键字，并且在`Promise`对象内部，我们传递了一个函数。这个函数叫做`executor`，它有两个参数，`resolve`表示成功，`reject`表示错误:

```
const firstPromise = new Promise((resolve, reject) => { 
  ... 
});
```

在承诺中，有一个条件，这是你放置逻辑的地方。如果条件满足，我们使用`resolve`参数为我们返回成功。如果出现错误，`reject`参数将为承诺返回一个错误:

```
const firstPromise = new Promise((resolve, reject) => {
  const sum = () => 1 + 1;
  if (sum() === 2) resolve("Success");
  else reject("Error");
});
```

## 链接

承诺链是让承诺如此伟大和易于使用的东西之一。我们可以执行一系列异步任务，每个任务在前一个任务完成后立即执行。

我们可以使用一个`.then`块来链接我们的承诺，从这个块返回的任何东西都成为一个解析的承诺:

```
const firstPromise = new Promise((resolve, reject) => {
  const sum = () => 1 + 1;
  if (sum() === 2) resolve("Success");
  else reject("Error");
});
firstPromise
  .then(success => console.log("success: ", success));
```

`.then`块的美妙之处在于我们可以一个接一个地执行额外的异步动作。对于错误处理，我们可以使用`.catch`块:

```
const firstPromise = new Promise((resolve, reject) => {
  const sum = () => 1 + 1;
  if (sum() === 2) resolve("Success");
  else reject("Error");
});
firstPromise
  .then(success => console.log("success: ", success))
  .catch(error => console.log("error: ", error));
```

您可以通过使用回调或承诺来执行异步操作。但还是有区别的。

如果你使用回调来执行异步操作，在某些情况下你可能会有太多的嵌套函数，这就是所谓的[回调地狱](http://callbackhell.com/)。太多的嵌套函数会导致您的代码无法阅读和管理。你可以通过使用承诺来解决这个问题，有了承诺，你就可以有更多可读和可管理的代码。

承诺是运行异步任务的一种更干净的方式。承诺提供了 catch 机制，这是回调所没有的。承诺允许更干净、更好、更实用的代码。

既然我们已经谈了一点承诺，让我们来看看`Promise.all`。

## `Promise.all`

`Promise.all`方法将异步操作带到了一个全新的水平，并帮助我们在 JavaScript 中聚合和执行一组承诺。

`Promise.all`只是一个接受承诺数组作为输入的承诺。当所有的承诺都解决了，它就解决了；如果其中一个承诺被拒绝，它就被拒绝了。

您在代码中积累了很多承诺，并且您希望一次性执行所有这些异步操作，而不必为此使用一些奇怪的东西，例如`for`循环。你怎么做到的？

对于这个用例，您有两种选择:

1.  您可以一个接一个地履行所有承诺——您可以一个接一个地运行这些承诺，或者将它们链接起来，并在数据可用时立即进行处理
2.  您可以执行所有的承诺，将它们作为数组输入传递给`Promise.all`，该方法将返回值

在这种情况下，更好的解决方案是使用`Promise.all`方法。它将执行所有承诺，返回单个承诺，并在所有通过的承诺都被解析时进行解析:

```
const allpromises = Promise.all([Promise1, Promise2, Promise3, Promise4, ...]);
```

记住，只有当数组中传递的所有承诺都成功返回时，`Promise.all`方法才会返回`resolve`。如果数组中只有一个承诺返回 rejected，那么`Promise.all`方法将返回 rejected。

举个例子，假设我们有一个函数叫做`sum`。这个函数将只为我们返回一些操作的值:

```
const sum = (a, b) => a + b;
```

现在，假设我们有五个承诺，在每个承诺中，我们将使用`sum`函数和`if`语句，比较值。如果是`true`，我们将返回一条成功消息，如果是`false`，我们将返回一条错误消息:

```
const first = new Promise((resolve, reject) => {
  const value = sum(1, 1);
  if (value === 2) resolve(value);
  else reject(value);
});

const second = new Promise((resolve, reject) => {
  const value = sum(2, 2);
  if (value === 4) resolve(value);
  else reject(value);
});

const third = new Promise((resolve, reject) => {
  const value = sum(3, 3);
  if (value === 6) resolve(value);
  else reject(value);
});

const fourth = new Promise((resolve, reject) => {
  const value = sum(4, 4);
  if (value === 8) resolve(value);
  else reject(value);
});

const fifth = new Promise((resolve, reject) => {
  const value = sum(5, 5);
  if (value === 10) resolve(value);
  else reject(value);
});
```

为了一次执行所有承诺，我们将一个数组输入传递给`Promise.all`:

```
const allPromises = Promise.all([first, second, third, fourth, fifth]);
```

现在，我们只需要调用我们的单个承诺`allPromises`，它将返回给我们一组解析的值:

```
allpromises.then(success => console.log('sucess: ', success)).catch(error => console.log('error: ', error));
// Result
// sucess: [ 2, 4, 2, 8, 10 ]
```

如果其中一个承诺返回一个错误，我们的单个承诺也将返回一个错误。在我们的例子中，在`fifth`承诺中，我们将传递值`5`和`6`作为`sum`函数的参数。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

当然，这会返回一个错误，因为`5` + `6`不是`10`。这将导致我们的单个承诺返回一个错误:

```
const fifth = new Promise((resolve, reject) => {
  const value = sum(5, 6);
  if (value === 10) resolve(value);
  else reject(value);
});

const allpromises = Promise.all([first, second, third, fourth, fifth]);
allpromises.then(success => console.log('sucess: ', success)).catch(error => console.log('error: ', error));

// Result
// error:  11
```

## `Promise.all`对`Promise.allSettled`

您有许多想要履行的承诺，但是如果您想要返回所有的值，不管您的承诺中是否有错误，那么`Promise.all`可能不是您的最佳解决方案。

你可以用`Promise.allSettled`的方法。该方法将返回一个承诺，该承诺将在所有承诺完成或被拒绝后得到解决。

让我们使用最后一个例子，不使用`Promise.all`方法，我们将使用`Promise.allSettled`方法:

```
const allpromises = Promise.allSettled([first, second, third, fourth, fifth]);
allpromises.then(success => console.log('sucess: ', success)).catch(error => console.log('error: ', error));

// Result
// success:  [
//   { status: 'fulfilled', value: 2 },
//   { status: 'fulfilled', value: 4 },
//   { status: 'fulfilled', value: 6 },
//   { status: 'fulfilled', value: 8 },
//   { status: 'rejected', reason: 11 }
// ]
```

## 何时使用

要使用`Promise.all`方法，你首先需要知道你需要实现什么。`Promise.all`方法在某些情况下非常有用，例如:

1.  你正在执行的任务是相互依赖的，你想知道是否所有的承诺都成功完成了
2.  您需要向不同的 API 发出请求，在所有的响应之后，您希望对结果做些什么

`Promise.all`是在 JavaScript 中实现并发的一个很好的方法，当你有多个承诺并且你想执行所有的承诺时，它是在 JavaScript 中执行并发异步操作的最好方法之一。

## 结论

在本文中，我们介绍了 JavaScript 中的一些承诺，并了解了更多关于一个名为`Promise.all`的承诺方法。这个方法是一个非常有用的方法，可以聚合和执行许多承诺，并返回一个包含数组中所有值的承诺。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.