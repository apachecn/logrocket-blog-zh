# 在 Node.js 中使用 setTimeout()和其他计时器 API

> 原文：<https://blog.logrocket.com/using-settimeout-timer-apis-nodejs/>

Node.js 有各种内置的方法，与其他服务器端编程语言相比，这些方法使得管理异步任务更加容易。事件循环用于实现 Node.js 的异步功能。

一个[事件循环是一个单线程半无限循环](https://blog.logrocket.com/a-complete-guide-to-the-node-js-event-loop/)，它使用一组队列顺序执行 I/O 或定时器回调。事件循环有六个不同的阶段:计时器、挂起的回调、空闲或准备、轮询、检查和关闭回调。在本文中，我们将关注计时器阶段。我们将看看`setTimeout()`、`clearTimeout()`和其他 Node.js 定时器函数的例子。

## 内容

## 设置 Node.js 计时器函数

Node.js 计时器模块包含在设定的时间后运行代码的函数。定时器方法是全局可用的，以模仿浏览器 JavaScript API，因此它们不需要通过`require()`导入。

让我们探索 Node.js 中的计时器函数:

### `setTimeout()`

`setTimeout()`可用于设置经过一段时间(以毫秒为单位)后的代码执行。它类似于浏览器中 JavaScript API 的`window.setTimeout()`函数。另一方面，一串代码不能被传递来执行。

`setTimeout()`函数有三个参数:

*   第一:要运行的函数
*   秒:以毫秒为单位的延迟
*   第三个(可选):要传递给函数的参数

这里有一个`setTimeout()`函数的例子:

```
const myTimeout = setTimeout(myGreeting, 5000, "Pascal");
function myGreeting(arg) {
  console.log("Welcome back " + arg)
}

```

由于调用了`setTimeout`，上面例子中的函数`myGreeting()`将在接近`5000`毫秒(或 5 秒)后执行。由于事件循环中运行的其他正在执行的代码，超时的执行可能会稍有延迟。

第三个参数`Pascal`是一个字符串，它作为参数传递给`myGreeting()`函数。

`setTimeout()`返回一个`Timeout`对象，该对象可用于使用称为`clearTimeout()`的`clear`方法终止超时。`setTimeout()`功能的一个真实应用是电子商务应用程序中的快闪销售倒计时。

### `setInterval()`

`setInterval()`执行无限循环。这对于重复一段毫秒级延迟的代码非常有用。`SetInterval()`和`setTimeout()`一样，需要三个参数:

*   第一:要执行的功能
*   秒:以毫秒为单位的延迟
*   第三个(可选):要传递给函数的参数

这里有一个`setInterval()`函数的例子:

```
function myInterval() {
  console.log('Catch me if you can!');
}
setInterval(myInterval, 3000);

```

在上面的例子中，函数`myInterval()`将每隔`3000`毫秒(3 秒)运行一次，直到停止。与`setTimeout()`类似，由于其他代码操作可能占用事件循环，延迟时间可能不准确。

`setInterval()`产生一个`Timeout`对象，可用于参考和调整先前设置的间隔。`setInterval()`功能的一个实际应用是在一个加密交易应用程序中以 60 秒的间隔获取以太坊的最新价格。

`setImmediate()`函数在当前事件循环周期完成后运行代码。`setImmediate()`类似于`setTimeout()`，延时为 0 毫秒。

换句话说，`setImmediate()`方法会在脚本中的每条语句执行完之后执行代码。`setImmediate()`需要两个参数:

*   第一:要执行的功能
*   Second(可选):函数的参数

这里有一个`setImmediate()`函数的例子:

```
console.log('before setImmediate');

const myImmediate = setImmediate((arg) => {
  console.log(`Hello and welcome ${arg}!`);
}, 'Pascal');
console.log('After setImmediate');

for(let i=0; i<10; i++){
  console.log(i);
}
```

在上面的例子中，`setImmediate()`函数将在所有可执行代码完成后运行。

输出如下所示:

```
before immediate
after immediate
0
1
2
3
4
5
6
7
8
9
Hello and welcome Pascal!

```

如果您有一个长时间的 CPU 绑定进程，您希望使其异步，以便事件循环可以继续运行并处理其他请求；是一个不错的选择。

## 清除 Node.js 定时器函数

`setTimeout()`、`setInterval()`和`setImmediate()`都返回一个对象，分别用来引用`Timeout`或`Immediate`对象。Node.js 提供了`clear`函数，用于停止或取消`Timeout`或`Immediate`对象的执行。

### `clearTimeout()`

`clearTimeout()`函数用于取消`setTimeout()`函数创建的`Timeout`对象:

```
const myTimeout = setTimeout(myGreeting, 5000, "Pascal");
function myGreeting(arg) {
  console.log("Welcome back " + arg)
}

clearTimeout(myTimeout)

```

### `clearInterval()`

`clearInterval()`函数暂停由`setInterval()`函数创建的间隔对象:

```
function myInterval() {
  console.log('Catch me if you can!');
}
setInterval(myInterval, 3000);

clearInterval(myInterval)

```

`clearImmediate()`函数停止由`setImmediate()`函数返回的直接对象:

```
console.log('before setImmediate');

const myImmediate = setImmediate((arg) => {
  console.log(`Hello and welcome ${arg}!`);
}, 'Pascal');
console.log('After setImmediate');

for(let i=0; i<10; i++){
  console.log(i);
}

clearImmediate(myImmediate);

```

## 其他定时器功能

### `unref()`

`unref()`是一个`Timeout`方法，通知`Timeout`对象不再需要 Node.js 事件循环。因为在这种情况下没有其他事件保持事件循环继续，所以它将在执行对`setTimeout()`的回调之前结束:

```
const myTimeout = setTimeout(myGreeting, 5000, "Pascal");
function myGreeting(arg) {
  console.log("Welcome back " + arg)
}

// Checking the myTimeout.hasRef() object
console.log(myTimeout.hasRef());

// Unreferencing
console.log(myTimeout.unref());

```

对于上面的示例，输出如下:

```
true
Timeout {
  _idleTimeout: 5000,
  _idlePrev: [TimersList],
  _idleNext: [TimersList],
  _idleStart: 68,
  _onTimeout: [Function: myGreeting],
  _timerArgs: [Array],
  _repeat: null,
  _destroyed: false,
  [Symbol(refed)]: false,
  [Symbol(kHasPrimitive)]: false,
  [Symbol(asyncId)]: 2,
  [Symbol(triggerId)]: 1
}

```

控制台上没有打印`myGreeting()`功能。注意`[Symbol(refed)]`被设置为`false`。

### `ref()`

如果`Timeout`对象的事件循环不再活动，则调用`ref()`方法。`ref()`方法仅在`timeout.unref()`被调用后使用，你需要再次访问`Timeout`对象:

```
const myTimeout = setTimeout(myGreeting, 5000, "Pascal");
function myGreeting(arg) {
  console.log("Welcome back " + arg)
}

// Checking the myTimeout.hasRef() object
console.log(myTimeout.hasRef());

// Unreferencing
console.log(myTimeout.unref());

// Referencing again
console.log(myTimeout.ref());

```

对于上述示例，输出如下:

```
true
Timeout {
  _idleTimeout: 5000,
  _idlePrev: [TimersList],
  _idleNext: [TimersList],
  _idleStart: 73,
  _onTimeout: [Function: myGreeting],
  _timerArgs: [Array],
  _repeat: null,
  _destroyed: false,
  [Symbol(refed)]: false,
  [Symbol(kHasPrimitive)]: false,
  [Symbol(asyncId)]: 2,
  [Symbol(triggerId)]: 1
}
Timeout {
  _idleTimeout: 5000,
  _idlePrev: [TimersList],
  _idleNext: [TimersList],
  _idleStart: 73,
  _onTimeout: [Function: myGreeting],
  _timerArgs: [Array],
  _repeat: null,
  _destroyed: false,
  [Symbol(refed)]: true,
  [Symbol(kHasPrimitive)]: false,
  [Symbol(asyncId)]: 2,
  [Symbol(triggerId)]: 1
}
Welcome back Pascal

```

控制台上印有`myGreeting()`功能，您会注意到`[Symbol(refed)]`被设置为`true`。

## 结论

Node.js 中使用定时器来在某个时间执行一个函数，或者延迟程序或代码的执行。在本文中，我们讨论了 Node.js 中设置或清除代码执行的不同计时器函数。我们看了`setTimeout()`、`setInterval()`、`setImmediate()`、`clearTimeout()`、`clearInterval()`和`clearImmediate()`的例子。我们还讨论了两个额外的定时器功能:`unref()`和`ref()`。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.