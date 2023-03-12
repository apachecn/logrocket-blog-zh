# Node.js - LogRocket 博客中的 AbortController 完整指南

> 原文：<https://blog.logrocket.com/complete-guide-abortcontroller-node-js/>

## 介绍

JavaScript 是一种单线程编程语言。根据运行时环境的不同，JavaScript 引擎将异步进程(如发出网络请求、文件系统访问和其他耗时的任务)卸载给一些 API，以实现异步。

通常，我们期望异步操作的结果是成功还是失败。但是，该过程也可能比预期花费更多的时间，或者当您收到结果时可能不再需要它们。

因此，终止花费了过多时间的异步操作或者终止不需要其结果的异步操作是合乎逻辑的。然而，很长一段时间以来，在 Node.js 中以本机方式实现这一点是一项艰巨的挑战。

为了解决上述问题，在节点 v15.0.0 中引入了`AbortController`。它用于在节点中本地中止某些异步操作。本教程将是一个关于`AbortController`和`AbortSignal`API 的完整指南。

### 内容

## 对`AbortController` API 的介绍

正如上一节所强调的，`AbortController` API 成为了 v15.0.0 中 Node 的一部分。它是中止一些异步进程的一个方便的 API，类似于浏览器环境中的`AbortController`接口。

您需要创建一个`AbortController`类的实例来使用它:

```
const controller = new AbortController();

```

`AbortController`类的一个实例公开了`abort`方法和`signal`属性。

调用`abort`方法会发出`abort`事件，通知监视控制器的 abortable API 取消操作。您可以向`abort`方法传递一个可选的中止原因。如果不包括取消原因，则默认为`AbortError`。

为了监听`abort`事件，您需要使用`addEventListener`方法向控制器的`signal`属性添加一个事件监听器，以便运行一些代码来响应`abort`事件。删除事件监听器的一个等效方法是`removeEventListener`方法。

下面的代码展示了如何使用`addEventListener`和`removeEventListener`方法添加和移除`abort`事件监听器:

```
const controller = new AbortController();
const { signal } = controller;

const abortEventListener = (event) => {
  console.log(signal.aborted); // true
  console.log(signal.reason); // Hello World
};

signal.addEventListener("abort", abortEventListener);
controller.abort("Hello World");
signal.removeEventListener("abort", abortEventListener);

```

控制器的`signal`有一个`reason`属性，这就是你在取消时传递给`abort`方法的原因。它的初始值是`undefined`。属性`reason`的值改变为您作为参数传递给`abort`方法的原因，或者如果您在没有提供取消原因的情况下中止，则默认为`AbortError`。类似地，初始值为`false`的信号的`aborted`属性在中止后变为`true`。

与上面的例子不同，`AbortController` API 的实际使用包括将`signal`属性传递给任何可取消的异步 API。您可以将同一个`signal`属性传递给尽可能多的可取消 API。当您调用`abort`方法时，API 将等待控制器的“信号”来中止异步操作。

大多数内置的支持取消的 API 为您实现了开箱即用的取消。您将控制器的`signal`属性传递给 API，当您调用控制器的`abort`方法时，它会中止进程。

然而，要实现定制的基于可取消承诺的功能，您需要添加一个事件监听器，监听`abort`事件，并在事件被触发时从事件处理程序中取消流程。

## 如何在 Node.js 中使用`AbortController`

正如在简介中指出的，`AbortController` API 是 Node 的一个相对较新的补充。因此，目前有一些异步 API 支持它。这些 API 包括新的获取 API、计时器、`fs.readFile`、`fs.writeFile`、`http.request`和`https.request`。

我们将学习如何将 AbortController API 与一些提到的 API 一起使用。因为 API 以类似的方式与 bortController 一起工作，所以我们将只关注 Fetch 和`fs.readFile` API。

### 如何在获取 API 中使用`AbortController`

从历史上看，`node-fetch`一直是 Node 事实上的 HTTP 客户端。然而，随着[的引入，Node.js](https://blog.logrocket.com/fetch-api-node-js/) 中的 Fetch API 即将改变。虽然在撰写本文时还处于试验阶段，但 Fetch 是一种本地 API，您可以使用`AbortController` API 来控制它的行为。

如上所述，将`AbortController`实例的`signal`属性传递给任何可移植的、基于承诺的 API，如 Fetch。下面的例子说明了如何通过`AbortController` API 使用它:

```
const url = "https://jsonplaceholder.typicode.com/todos/1";

const controller = new AbortController();
const signal = controller.signal;

const fetchTodo = async () => {
  try {
    const response = await fetch(url, { signal });
    const todo = await response.json();
    console.log(todo);
  } catch (error) {
    if (error.name === "AbortError") {
      console.log("Operation timed out");
    } else {
      console.error(err);
    }
  }
};

fetchTodo();

controller.abort();

```

上面的小例子说明了如何将`AbortController` API 与节点中的 Fetch API 一起使用。然而，在现实世界的项目中，您不会像上面的代码那样启动一个异步操作并立即中止它。

另外值得强调的是`fetch`在 Node 中还是一个实验性的特性。它的特性在未来的版本中可能会有所变化。

### 如何将`AbortController`与`fs.readFile`一起使用

在前一节中，我们看了如何将`AbortController`与 Fetch API 一起使用。同样，您可以将这个 API 与其他可取消的 API 一起使用。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

您可以通过将控制器的`signal`属性传递给 API 的相应函数来实现这一点。下面的代码显示了如何使用`AbortController`和`fs.readFile`:

```
const fs = require("node:fs");

const controller = new AbortController();
const { signal } = controller;

fs.readFile("data.txt", { signal, encoding: "utf8" }, (error, data) => {
  if (error) {
    if (error.name === "AbortError") {
      console.log("Read file process aborted");
    } else {
      console.error(error);
    }
    return;
  }
  console.log(data);
});

controller.abort();

```

由于其他可取消的 API 与`AbortController`的工作方式相似，我们在这里就不介绍了。

## `AbortSignal`简介

每个`AbortController`类实例都有一个对应的`AbortSignal`类实例，可以使用`signal`属性访问。然而，`AbortSignal`有一些函数，比如`AbortSignal.timeout`静态方法，你也可以独立于`AbortController`使用。

`AbortSignal`类扩展了`EventTarget`类，可以接收`abort`事件。因此，您可以使用`addEventListener`和`removeEventListener`方法来添加和移除`abort`事件的监听器:

```
const controller = new AbortController();
const { signal } = controller;

signal.addEventListener(
  "abort",
  () => {
    console.log("First event handler");
  },
  { once: true }
);
signal.addEventListener(
  "abort",
  () => {
    console.log("Second event handler");
  },
  { once: true }
);

controller.abort();

```

如上例所示，您可以添加尽可能多的事件处理程序。调用控制器的`abort`方法将触发所有的事件监听器。中止异步进程后删除`abort`事件监听器是防止内存泄漏的标准做法。

您可以像上面那样将可选的第三个参数`{ once: true }`传递给`addEventListener`，而不是使用`removeEventListener`来移除事件监听器。可选的第三个参数将确保 Node 触发事件侦听器一次并删除它。

## 如何使用`AbortSignal`让 Node.js 中的异步操作超时

如上所述，除了与`AbortController`一起使用之外，`AbortSignal`类还有一些您可能需要的简便方法。这些方法之一是`AbortSignal.timeout`静态方法。顾名思义，您可以使用它在超时时中止可取消的异步流程。

它将毫秒数作为一个参数，并返回一个信号，您可以用它来超时一个可中止的操作。下面的代码展示了如何用 Fetch API 实现它:

```
const signal = AbortSignal.timeout(200);
const url = "https://jsonplaceholder.typicode.com/todos/1";

const fetchTodo = async () => {
  try {
    const response = await fetch(url, { signal });
    const todo = await response.json();
    console.log(todo);
  } catch (error) {
    if (error.name === "AbortError") {
      console.log("Operation timed out");
    } else {
      console.error(err);
    }
  }
};

fetchTodo();

```

您可以类似地将`AbortSignal.timeout`用于其他可移植的 API。

## 如何使用`AbortController`和`AbortSignal`实现一个可移植的 API

正如上一节所强调的，几个内置的异步 API 支持`AbortController` API。然而，您也可以实现一个使用`AbortController`的定制的基于承诺的 API。

像内置 API 一样，您的 API 应该将一个`AbortController`类实例的`signal`属性作为参数，如下例所示。这是所有能够使用`AbortController` API 的 API 的标准做法:

```
const myAbortableApi = (options = {}) => {
  const { signal } = options;

  if (signal?.aborted === true) {
    throw new Error(signal.reason);
  }

  const abortEventListener = () => {
    // Abort API from here
  };
  if (signal) {
    signal.addEventListener("abort", abortEventListener, { once: true });
  }
  try {
    // Run some asynchronous code
    if (signal?.aborted === true) {
      throw new Error(signal.reason);
    }
    // Run more asynchronous code
  } finally {
    if (signal) {
      signal.removeEventListener("abort", abortEventListener);
    }
  }
};

```

在上面的例子中，我们首先检查信号的`aborted`属性的值是否为`true`。如果是，这意味着控制器的`abort`方法已经被调用。因此，我们抛出一个错误。

就像前面提到的，您可以使用`addEventListener`方法注册`abort`事件监听器。为了防止内存泄漏，我们将把`{ once: true }`选项作为第三个参数传递给`addEventListener`方法。它在处理完`abort`事件后移除事件处理程序。

类似地，我们使用`finally`块中的`removeEventListener`移除了事件监听器，以防止内存泄漏。如果您不删除它，并且`myAbortableApi`函数成功运行而没有中止，那么您添加的事件监听器即使在退出该函数后仍会连接到`signal`。

## 结论

通常，异步流程可能会成功、失败、花费比预期更长的时间，或者当您收到结果时可能不需要它们。因此，取消花费了过多时间的异步操作或者取消不需要其结果的异步操作是合乎逻辑的。API 是一个方便的功能。

`AbortController` API 是全球可用的。因此，不需要导入。`AbortController`类的一个实例公开了`abort`方法和`signal`属性。`signal`属性是`AbortSignal`类的实例。每个`AbortController`类实例都有一个对应的 AbortSignal 类实例，您可以使用控制器的`signal`属性来访问它。

您将`signal`属性传递给一个可取消的异步 API，并调用控制器的`abort`方法来触发中止进程。如果内置 API 不符合您的用例，您还可以使用`AbortController`和`AbortSignal`实现一个定制的可移植 API。但是，请遵循上面提示的最佳实践来防止内存泄漏。

我错过什么了吗？请在下面的评论区留下您的评论。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.