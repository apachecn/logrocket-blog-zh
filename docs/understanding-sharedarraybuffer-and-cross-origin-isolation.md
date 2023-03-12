# 了解 SharedArrayBuffer 和跨源隔离

> 原文：<https://blog.logrocket.com/understanding-sharedarraybuffer-and-cross-origin-isolation/>

JavaScript 内存以二进制形式存储程序开发和运行时使用的每一条数据和指令。JavaScript，也称为 [ECMAScript](https://www.ecma-international.org/publications-and-standards/standards/ecma-262/) ，是一种[内存管理的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management)语言。

JavaScript 引擎自己访问和管理内存，它为编写和执行的每个程序或代码块分配内存。它还对内存中不再存在的数据执行垃圾收集。

尽管 JavaScript 是一种内存管理语言，但它也有助于管理数据。但是它有缺陷。例如，JavaScript 可以为特定的程序或变量分配比内存中所需的空闲空间更多的空间。在某些情况下，JavaScript 中的垃圾收集可能会很慢。

为了让开发人员能够在内存的多个线程之间分配和共享视图中的数据(使用类型化数组)，[引入了`ArrayBuffer`和`SharedArrayBuffer`特性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer)。

## 什么是`SharedArrayBuffer`？

当讨论`SharedArrayBuffer`时，我们可以很容易地将注意力集中在物理词汇上:“共享”、“数组”和“缓冲区”。

数组是一种数据结构，在编程中用于存储由不同数据类型(字符串、布尔值、数字和对象)组成的数据元素。缓冲区是内存存储的一部分，用于在发送或接收数据之前临时存储数据。

[`ArrayBuffer`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) 是一个与众不同的数组——它是一个字节数组，意味着只接受字节。

要在 JavaScript 中使用共享内存，您需要创建`SharedArrayBuffer`。这是通过使用`SharedArrayBuffer`对象来完成的，它创建了一个新的对象构造函数，用于在多个线程之间写入和共享数据。

## `SharedArrayBuffer`的历史

2018 年 1 月 5 日， [`SharedArrayBuffer`因现代](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer/SharedArrayBuffer) [CPU 架构](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html)发现的漏洞攻击，在各大浏览器禁用。

从那时起，`SharedArrayBuffer`在 Google Chrome v67 中重新启用，现在可以在启用了站点隔离功能的平台上使用，我们将在本文的后续部分中介绍。此更新可抵御 Spectre 漏洞攻击，并使您的站点更加安全。

下面，我们将探讨如何在 JavaScript 中使用`SharedArrayBuffer`来共享内存。首先，我们将共享内存，然后更新和同步它，最后，调试它。

## 在 JavaScript 中使用`SharedArrayBuffer`共享内存

使用`SharedArrayBuffer`的好处之一是能够在 JavaScript 中共享内存。在 JavaScript 中， [web workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) 作为一种在 JS 代码中创建线程的手段。

然而，web worker 也与`SharedArrayBuffer`一起使用，它通过直接指向存储或以前访问过每个数据的内存，允许 web worker 之间共享原始二进制数据。

让我们看一个如何使用`SharedArrayBuffer`共享内存的例子。

在我们开始之前，创建三个文件:**index.html**(我们链接脚本的地方)、 **script.js** (主线程)和 **worker.js** (工作线程)。

```
<!--index.html file-->

<DOCTYPE html>
    <html>
    <head>
        <title>using shared array buffer</title>
        <meta charset="UTF-8">
        <meta name="sharedArrayBuffer" description="using cross-orgiin-isolation in the web browser">
        <script type="text/JavaScript" src="script.js"></script>
    </head>
    <body>
        <h3>Take a look at your browser console :)</h3>
        <script type="text/JavaScript" src="worker.js"></script>
    </body>
    </html>

```

我们先来看看主线程(script.js 文件)。这里，我们访问 **worker.js** ，然后使用`SharedArrayBuffer` [对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer)创建一个共享内存，并将其单位长度字节数设置为`1024`(注意:可以使用任何所需的单位长度字节数)。

使用类型为`[Int16Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Int16Array)`的类型化数组来解释被传递的数据，我们将一个数字分配给类型化数组(`20`)以从主线程共享。我们使用`[postMessage](https://developer.mozilla.org/en-US/docs/Web/API/Worker/postMessage)`将缓冲区发送给工作线程。

```
/*MAIN THREAD*/

const newWorker = new Worker('worker.js');
const buffMemLength = new SharedArrayBuffer(1024); //byte length
let typedArr = new Int16Array(buffMemLength);
//original data
typedArr[0] = 20;
//sending the buffer to worker 
newWorker.postMessage(buffMemLength); 

```

为了与工作线程共享来自主线程的数据，我们设置了一个`eventListener`在收到数据时运行。这里，注意我们使用了一个`Int16`类型的数组来显示浏览器控制台中的数据。

```
/*WORKER THREAD*/

addEventListener('message', ({ data }) => {
    let arr = new Int16Array(data);
    console.group('[the worker thread]');
    console.log('Data received from the main thread: %i', arr[0]);
    console.groupEnd();
    postMessage('Updated');
})

```

在您的浏览器控制台中，您应该会看到以下内容:

```
[the worker thread]                      worker.js:7
Data received from main thread: 20       worker.js:8

```

## 更新和同步共享内存

自从 JavaScript 增加了`SharedArrayBuffer`之后，更新共享内存变得更加容易。使用前面的例子，我们将从工作线程中更新数据。

让我们将主线程中的原始`arr[0]`设置为上面在作用域(在工作线程中)中声明的`dataChanged`变量。

```
/*WORKER THREAD*/

let BYTE_PER_LENTH = 5;
addEventListener('message', ({ data }) => {
    var arr = new Int16Array(data);
    console.group('[worker thread]');
    console.log('Data received from main thread: %i', arr[0]);
    console.groupEnd();

    //updating the data from the worker thread 
    let dataChanged = 5 * BYTE_PER_LENTH;
    arr[0] = dataChanged;
    //Sending to the main thread 
    postMessage('Updated');
})

```

为了从工作线程中更新数据，我们调用一个将在主线程中触发的`onmessage`事件，表明数据是从工作线程中更新的。

```
/*MAIN THREAD*/

const newWorker = new Worker('worker.js');
const buffMemLength = new SharedArrayBuffer(1024); //byte length
var typedArr = new Int16Array(buffMemLength);
//original data
typedArr[0] = 20;
//sending the buffer to worker 
newWorker.postMessage(buffMemLength);

//onmessage event
newWorker.onmessage = (e) => {
    console.group('[the main thread]');
    console.log('Data updated from the worker thread: %i', typedArr[0]);
    console.groupEnd();
}

```

在浏览器控制台中，您应该会看到:

```
[the worker thread]                            worker.js:7
Data received from main thread: 20             worker.js:8
[the main thread]                              script.js:15
Data updated from the worker thread: 25        script.js:16

```

同步共享内存是很重要的，因为在实现时，同步会导致共享内存同时运行而不会被改变。为了在共享内存中整合同步，开发人员使用了[原子](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Atomics)操作。

`Atomics`在`wait()`和`notify()`方法的帮助下，确保每个进程在下一个进程之前被连续执行，并且所有从内存中读取或写入特定内存的数据被一个接一个地执行。

## 关于`SharedArrayBuffer`和跨原点隔离的最新更新

自 2021 年 5 月以来，JavaScript 中对共享内存进行了几次重要更新，包括跨起源隔离，使开发人员能够更有效地调试共享内存。它目前在 Firefox 79+和桌面 Chrome 上受到支持，但 Chrome 92 的更新将可以访问跨源隔离页面的网站。

要实现`SharedArrayBuffer`，您需要一个安全的环境，使用一个或多个响应头指令来限制访问。这被称为跨源隔离，尽管以前不鼓励使用共享内存，但它已被证明是保护网站安全的更好方法。

## 什么是跨原点隔离？

跨来源隔离是添加到浏览器中的新安全功能(截至 2021 年 4 月)。简而言之，这是在顶级文档上发送两个 HTTP 头(COOP 和 COEP)的结果。这些头文件使您的网站能够访问 web APIs，如`SharedArrayBuffer`，并防止外部攻击(幽灵攻击、跨源攻击等)。

以前，使用共享内存的网站可以在未经许可的情况下加载跨源内容。这些网站可能会与不同来源的窗口弹出窗口进行交互，这可能会导致安全违规或漏洞，从而获取网站上的用户信息。对于使用共享内存的网站来说，安全和保护用户信息变得非常重要。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 在您的网站上启用跨来源隔离

现在，我们对跨源隔离的背景有了更多的了解，让我们在我们的网站上启用它。

首先，在文档的顶层启用跨来源开放策略(COOP)头，用`same-origin`:

```
Cross-Origin-Opener-Policy: same-origin

```

此标题将页面与浏览器中的任何跨来源弹出窗口隔离开来，使它们无法访问文档或向它们发送直接消息。它还确保您的页面处于一个安全的上下文中，具有相同的顶级来源的页面。

接下来，发送一个跨来源嵌入器策略报头(COEP)，其值指示`require-CORP`(跨来源资源策略)。

```
Cross-Origin-Embedder-Policy: require-corp

```

这可以确保从您的网站加载的所有资源都已加载到 corp。COEP 标头打破了所有需要与浏览器中跨来源窗口进行通信的集成，例如来自第三方服务器的认证和支付(结账)。

通过在文档的顶层设置这些标题，您的网站现在处于一个安全的环境中，并提供对 web APIs 的访问。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.