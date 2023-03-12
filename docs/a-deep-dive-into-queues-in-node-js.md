# 深入探究 Node.js - LogRocket 博客中的队列

> 原文：<https://blog.logrocket.com/a-deep-dive-into-queues-in-node-js/>

排队是 Node.js 中用于有效处理异步操作的一项重要技术。

在本文中，我们将深入探讨 Node.js 中的队列:它们是什么，它们如何工作(与事件循环一起)，以及它们的各种类型。

## Node.js 中的队列是什么？

队列是 Node.js 中用于适当组织异步操作的数据结构。这些操作以不同的形式存在，包括 HTTP 请求、读写文件操作、流等等。

在 Node.js 中处理异步操作可能具有挑战性。

根据网络强度，HTTP 请求期间可能会出现不可预测的延迟(或者更糟，没有结果)。根据文件的大小，尝试使用 Node.js 读取或写入文件时也可能会有延迟。

与计时器和许多其他操作类似，异步操作的完成也可能是无限期的。

对于这些不同的持续时间，Node.js 需要能够有效地处理所有这些操作。

Node.js 不能处理基于 first-start-first-handle 或 first-finish-first-handle 的操作。

这可能不是一个好选择的一个原因是，一个异步操作可能包含另一个异步操作。

为第一个异步进程留出空间意味着必须先完成内部异步进程，然后才能考虑队列中的其他异步操作。

需要考虑的场景很多，所以最好的选择是有一个规则。这条规则影响事件循环和队列在 Node.js 中的工作方式。

让我们简单看看 Node.js 是如何处理异步操作的。

## 调用堆栈、事件循环和回调队列

调用堆栈跟踪当前正在执行的函数及其运行位置。当一个函数将要被执行时，它被添加到调用堆栈中。

这有助于 JavaScript 在执行一个函数后返回它的步骤。

回调队列是当异步操作在后台完成时，保存这些操作的回调函数的队列。

他们以先进先出的方式工作。在本文的后面，我们将研究不同类型的回调队列。

注意 Node.js 负责每一个异步活动，因为 JavaScript 可以用它的单线程性质阻塞线程。

它还负责在完成后台操作后向回调队列添加函数。JavaScript 与回调队列无关。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

同时，事件循环不断检查调用堆栈是否为空，这样它就可以从回调队列中选取一个函数并添加到调用堆栈中。事件循环仅在所有同步操作都已执行时检查队列。

那么，事件循环按照什么顺序从队列中选择回调函数呢？

首先，让我们看看五种主要类型的回调队列。

## 回调队列的类型

### 我排队

IO 操作是指涉及外部设备(如计算机内部设备)的操作。常见的操作包括读写文件操作、网络操作等。

这些操作应该是异步的，因为它们留给 Node.js 来处理。JavaScript 无法访问计算机的内部。

当要执行这样的操作时，JavaScript 会将它们转移到 Node.js 中进行后台处理。

完成后，它们被传输到 IO 回调队列，以便事件循环传输到调用堆栈执行。

### 计时器队列

涉及 Node.js (如`setTimeout()`和`setInterval()`)的[定时器特性的每个操作都被添加到定时器队列中。](https://nodejs.org/en/docs/guides/timers-in-node/%5C)

注意 [JavaScript 本身没有定时器特性](https://dillionmegida.com/p/browser-apis-and-javascript/#javascript-on-nodejs)。

它使用 Node.js 提供的 timer API(包含`setTimeout`)来执行与时间相关的操作。因此，计时器操作是异步的。

无论持续时间是 2 秒还是 0 秒，JavaScript 都会将与时间相关的操作交给 Node.js，然后完成这些操作并将其添加到计时器队列中。

例如:

```
setTimeout(function() {
        console.log('setTimeout');
    }, 0)
    console.log('yeah')

    # result
    yeah
    setTimeout
```

在处理异步操作的同时，JavaScript 继续执行其他操作。只有当所有同步操作都被处理后，事件循环才会进入回调队列。

### 微任务队列

该队列分为两个队列:

事件循环执行的每次迭代称为一个节拍。

`process.nextTick`是一个在下一个 tick 执行函数的函数(也叫事件循环的下一次迭代)。微任务队列存储这样的函数，以便它们可以在下一个节拍被执行。

这意味着事件循环必须在继续处理其他队列之前不断检查微任务队列中的这种功能。

*   第二队列保存被`[promises](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise)`延迟的功能。

正如我们已经看到的，在 IO 和定时器队列中，所有关于异步操作的事情都交给了异步函数

承诺不一样。在 promises 中，一个初始变量存储在 JavaScript 内存中(你可能见过，`<Pending>`)。

当异步操作完成时，Node.js 将该函数(附属于承诺)放入微任务队列中。同时，它用得到的结果更新 JavaScript 内存中的变量，这样函数就不会用`<Pending>`运行。

以下代码解释了承诺的工作原理:

```
let prom = new Promise(function (resolve, reject) {
        // delay execution
        setTimeout(function () {
            return resolve("hello");
        }, 2000);
    });
    console.log(prom);
    // Promise { <pending> }

    prom.then(function (response) {
        console.log(response);
    });
    // after 2000ms,
    // hello
```

关于微任务队列需要注意的一个重要特性是，事件循环在处理其他队列之前，会反复检查和执行微任务队列中的函数。

例如，当微任务队列完成时，比方说，一个定时器操作执行一个承诺操作，事件循环将在转移到定时器队列中的其他函数之前处理该承诺操作。

因此，微任务 qeuue 的优先级高于其他队列。

### 检查队列(也称为立即队列)

在 IO 队列中的所有回调函数被执行之后，这个队列中的回调函数被立即执行。

`setImmediate`是用于向该队列添加函数的函数。

例如:

```
const fs = require('fs');
setImmediate(function() {
    console.log('setImmediate');
})
// assume this operation takes 1ms
fs.readFile('path-to-file', function() {
    console.log('readFile')
})
// assume this operation takes 3ms
do...while...
```

当这个程序被执行时，Node.js 将`setImmediate`的回调函数添加到检查队列中。由于整个程序尚未完成，事件循环不会检查任何队列。

`readFile`操作是异步的，所以交给 Node.js，程序继续执行。

`do while`操作持续 3 毫秒。在此期间，`readFile`操作完成并被推送到 IO 队列。完成此操作后，事件循环开始检查队列。

尽管检查队列首先被填充，但只有在 IO 队列为空之后才会被考虑。因此，`readFile`在`setImmediate`之前被记录到控制台。

### 关闭队列

此队列存储与关闭事件操作相关联的函数。

例子包括如下:

*   [流关闭事件](https://nodejs.org/api/stream.html#stream_event_close)，当流关闭时发出。它表示不会再发出更多的事件。
*   服务器关闭时发出的 [http 关闭事件](https://nodejs.org/api/http.html#http_event_close)。

这些被认为是优先级最低的队列，因为这里的操作发生在较晚的时间。

在处理 promise 的函数之前，您不会希望在`close`事件中执行回调函数。当服务器已经关闭时，这个承诺函数会做什么？

### 队列的顺序

微任务队列被赋予最高优先级，然后是定时器队列、I/O 队列、检查队列，最后是关闭队列。

## 回调队列的一个例子

让我们看一个更大的例子来说明队列的类型和顺序:

```
const fs = require("fs");

// assume this operation takes a 2ms
fs.writeFile('./new-file.json', '...', function() {
    console.log('writeFile')
})

// assume this takes 10ms to complete
fs.readFile("./file.json", function(err, data) {
    console.log("readFile");
});

// don't assume, this actually takes 1ms
setTimeout(function() {
    console.log("setTimeout");
}, 1000);

// assume this operation takes 3ms
while(...) {
    ...
}

setImmediate(function() {
    console.log("setImmediate");
});

// promise that takes 4ms to resolve
let promise = new Promise(function (resolve, reject) {
    setTimeout(function () {
        return resolve("promise");
    }, 4000);
});
promise.then(function(response) {
    console.log(response)
})

console.log("last line");
```

下面是程序流程:

0 毫秒时，程序开始。

`fs.writeFile`在 Node.js 将回调函数添加到 IO 队列之前，在后台需要 2ms。

`fs.readFile`在 Node.js 将回调函数添加到 IO 队列之前，在后台花费 10ms。

`setTimeout`在 Node.js 将回调函数添加到定时器队列之前，在后台需要 1ms。

现在，while 操作(它是同步的)需要 3 毫秒。在此期间，线程被阻塞(记住 JavaScript 是单线程的)。

同样在此期间，`setTimeout`和`fs.writeFile`操作完成，它们的回调函数被分别添加到定时器和 IO 队列中。

现在的队列是:

```
// queues
Timer = [
    function () {
        console.log("setTimeout");
    },
];
IO = [
    function () {
        console.log("writeFile");
    },
];
```

`setImmediate`将回调函数添加到检查队列中:

```
js
// queues
Timer...
IO...
Check = [
    function() {console.log("setImmediate")}
]
```

在将 promise 操作添加到微任务队列之前，需要 4 毫秒的时间(在后台)进行解析。

最后一行是同步的，所以会立即执行:

```
# results
"last line"
```

所有同步活动都已完成，因此事件循环开始检查队列。它从定时器队列开始，因为微任务队列是空的:

```
// queues
Timer = [] // now empty
IO...
Check...

# results
"last line"
"setTimeout"
```

当事件循环继续执行队列中的回调函数时，`promise`操作完成并被添加到微任务队列中:

```
// queues
    Timer = [];
    Microtask = [
        function (response) {
            console.log(response);
        },
    ];
    IO = []; // now empty
    Check = []; // now empty immediately after IO

    # results
    "last line"
    "setTimeout"
    "writeFile"
    "setImmediate"
```

几秒钟后，`readFile`操作完成并被添加到 IO 队列:

```
// queues
    Timer = [];
    Microtask = []; // now empty
    IO = [
        function () {
            console.log("readFile");
        },
    ];
    Check = [];

    # results
    "last line"
    "setTimeout"
    "writeFile"
    "setImmediate"
    "promise"
```

最后，执行所有回调函数:

```
// queues
    Timer = []
    Microtask = []
    IO = [] // now empty again
    Check = [];

    # results
    "last line"
    "setTimeout"
    "writeFile"
    "setImmediate"
    "promise"
    "readFile"
```

这里需要注意三件事:

*   异步操作在被添加到队列之前取决于它们的持续时间。它们不依赖于它们在程序中的排列顺序。
*   事件循环在每次迭代处理其他队列之前，不断检查微任务队列。
*   即使在后台有另一个 IO 操作(`readFile`)时，事件循环也执行检查队列中的功能。它这样做是因为，在那个时候，IO 队列是空的。请记住，检查队列回调是在 IO 队列中的所有函数执行完之后立即运行的。

### 结论

JavaScript 是单线程的。每一个异步函数都由 Node.js 与计算机的内部功能一起处理。

Node.js 负责将回调函数(通过 JavaScript 附加到异步操作)添加到回调队列中。事件循环确定在每次迭代中接下来要执行的回调函数。

理解队列在 Node.js 中的工作方式可以让您更好地理解它，因为队列是环境的核心特性之一。Node.js 最流行的定义是`non-blocking`，意思是异步操作被正确处理。

这个特性是通过事件循环和回调队列实现的。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.