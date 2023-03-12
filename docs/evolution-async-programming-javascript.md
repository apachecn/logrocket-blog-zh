# JavaScript - LogRocket 博客中异步编程的演变

> 原文：<https://blog.logrocket.com/evolution-async-programming-javascript/>

## 介绍

按照设计，JavaScript 是单线程的，这意味着它一次只能处理一个操作。因为我们的程序只有一个执行线程可以运行，于是就产生了一个问题:我们如何在不阻塞执行线程的情况下执行一个长时间运行的操作？好了，欢迎来到异步编程。

JavaScript 中的异步编程提供了一种很好的处理操作(I/O)的方式，这些操作不是立即执行的，因此没有立即响应。不是等待长时间运行的操作返回，阻塞进程中的执行线程，而是将它们委托给[回调](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function)，这些回调是当这些操作最终返回时调用的函数。

> 在这种情况下，执行线程帮助跟踪被称为子例程的活动运行操作，以及该子例程在执行后何时应该将控制返回给它的调用子例程。

如今，有许多应用程序需要某种形式的异步行为。当谈到用 JavaScript 解释这些概念时，生成网络或 AJAX 请求提供了一个很好的用例。

在本文中，我们将使用回调、承诺和异步/等待来说明异步 JavaScript 的概念，并解释它们是如何工作的。

## JavaScript 的本质以及我们为什么需要异步编程

前面，我们了解到 JavaScript 是具有全局执行上下文的单线程。这意味着，从本质上讲，JavaScript 是与单个[调用栈](https://en.wikipedia.org/wiki/Call_stack)同步的[。因此，代码将按照它被调用的顺序执行，通常称为后进先出(LIFO)方法。](https://developer.mozilla.org/en-US/docs/Glossary/synchronous)

例如，假设我们要执行两个函数 A 和 B，其中函数 B 依赖于 A 的输出来运行。假设函数 A 花了一些时间返回函数 B 开始执行所需的输出，我们最终阻塞了程序的操作线程。这种行为导致应用程序非常慢，不利于用户体验。

让我们看一个 JavaScript 中同步或阻塞操作的例子。

```
const fs = require('fs')

const A = (filePath) => {
  const data = fs.readFileSync(filePath) 
  return data.toString()
}

const B  = () => {
  const result = A('./file.md')
  if (result) {
    for (i=0; i < result.length; i++) {
       console.log(i)
    }
  }
  console.log('Result is back from function A')
}

B()

// output is shown below
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
10
Result is back from function A
```

在上面的例子中，我们在继续执行`B()`中的代码逻辑之前，等待第 9 行函数`A()`的输出。目前，这很好——直到我们必须读取一个非常大的文件。在这种情况下，在我们得到执行`B()`所需的输入之前，需要很长时间等待`A()`完成。同样，这是不可取的，

> **注意** **1** :根据上面的输出，`B()`被调用并被推到调用栈顶。当它同步执行完所有的代码逻辑之后——包括执行函数`A()`——它被弹出堆栈，线程又可以使用了。这里有一个[链接](https://repl.it/@firebase007/HorribleOliveRobot)到操场来运行示例代码。
> 
> **注 2**:`readFileSync`函数是 Node.js 中`fs`模块的内置方法，它从指定路径输入的文件中同步读取。

因此，对于同步调用或操作，[事件循环](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)无法继续执行任何其他 JavaScript 代码，直到该操作完成。

### JavaScript 中的 async 解决什么？

异步编程使得许多输入/输出操作同时发生成为可能。对于 JavaScript，这可以通过事件循环、调用堆栈和异步 API(如回调)来实现。

让我们看一个异步操作的例子来更好地理解:

```
const fs = require('fs')

const A = (filePath, callback) => {
  return fs.readFile(filePath, (error, result) => {
    if (error) {
    return callback(error, null)
    }
    return callback(null, result)
  })
}

const B  = () => {
   // a callback function attached
  A('./file.md',  (error, result) => {
    if (result) {
    for (i=0; i < result.length; i++) {
       console.log(i)
    }
  }
})
  console.log('Result is not yet back from function A')
} 

B()

// output is shown below
Result is not yet back from function A
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
10
```

这里有一个[链接](https://repl.it/@firebase007/async-example)到操场来运行上面的代码示例。正如我们所看到的，我们已经定义了一个异步回调。因此，当调用`B()`时，功能`A()`不会立即执行。

相反，只有在 Node.js `readFile`模块完成了对文件内容的解析和读取之后，它才会这样做。因此，当代码运行时，我们看到第 21 行输出为`Result is not yet back from function A`的代码被立即执行。

在接下来的章节中，我们将学习回调和其他在 JavaScript 中不断发展的异步模式。但在此之前，我们先来谈谈事件循环。

## JavaScript 事件循环

根据我们之前的讨论，JavaScript 通过基于事件的回调处理非阻塞的输入/输出操作。在本节中，我们将通过事件循环、调用堆栈和回调 API 来了解代码的执行顺序，回调 API 是 JavaScript 中最早的用于 browser 和 Node.js 的异步 API。

根据 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop#Event_loop) 的说法，回调和 JS 中的并发模型通过事件循环成为可能。事件循环负责执行我们的代码，处理回调之类的事件，并调度其他排队的任务来执行。让我们用前面的回调例子来演示事件循环是如何工作的。

*   首先，在执行函数`B()`之前，[调用栈](https://developer.mozilla.org/en-US/docs/Glossary/Call_stack)和事件循环是空的。
*   当函数`B()`被执行时，它被压入调用栈。
*   因为第 14 行的第一个函数`A()`有一个回调函数，所以它被推送到回调队列进行处理。
*   同时，最后一行`console.log('Result is not yet back from function A')`被执行并离开调用堆栈。
*   在函数`A()`完成后，我们得到一个响应，然后执行转移到事件循环。
*   此时，调用堆栈是空的，因此 JS 执行上下文检查事件循环中是否有任何排队的任务。
*   控制链现在将函数`A()`从事件循环移到调用堆栈，然后在调用堆栈中执行该函数，并返回响应(result)。
*   此时，`result`现在是可用的，调用堆栈再次为空。
*   然后将`for`循环移动到调用堆栈中执行。
*   在`for`循环的每次迭代中，第 17 行上的`console.log`被移动到调用堆栈中执行，直到完成。
*   最后，因为现在执行已经完成，函数`B()`从调用堆栈中移除，这结束了典型的流程。

### 事件循环和调用堆栈

事件循环充当跟踪调用堆栈和回调队列的桥梁。当调用栈为空时，JS 执行环境偶尔会检查是否有任何东西在排队等待执行。如果是，事件循环从队列(FIFO)中取出第一个任务，并将其移动到调用堆栈，然后调用堆栈执行我们的代码。

> 调用堆栈是一种堆栈数据结构，它有助于跟踪程序中当前运行或执行的函数。对于堆栈数据结构，最后一个推入堆栈的项是第一个离开的项——更像 LIFO。

这里要注意的最后一点是，虽然回调不是 JavaScript 引擎实现的一部分，但它们是浏览器和节点都可用的 API。这些 API 不会将代码执行直接推送到调用堆栈上，因为这可能会干扰已经在执行的代码，从而导致事件循环。

## 回收

回调是 JavaScript 中处理异步行为的最早方法之一。正如我们在前面的异步示例中所看到的，回调是作为参数传递给另一个函数的函数，该函数随后通过响应执行。

本质上，异步操作完成后，返回的错误或响应由回调或其他类似的异步 API 处理，如 JavaScript 中的 promises 或 async/await。

> **注意**:按照惯例，传递给回调的第一个参数是错误，以及错误发生的原因，而第二个参数是响应数据或结果。

同样，创建回调可以像下面的例子一样简单。这里有一个到操场的[链接来运行代码。](https://repl.it/@firebase007/call-back-example-simple)

```
const callbackExample = (asyncPattern, callback) => {
  console.log(`This is an example, with a ${asyncPattern} passed an an argument`)
  callback()
}

const  testCallbackFunc = () => {
  console.log('Again, this is just a simple callback example')
}

// call our function and pass the testCallbackFunction as an argument
callbackExample('callback', testCallbackFunc)
```

### 回调的问题

应该注意的是，由于每个异步行为的结果发生在它自己的调用堆栈上，所以在抛出异常时，错误处理程序可能不在调用堆栈上。这可能会导致错误无法正确传播到调用函数

此外，还有可怕的“回调地狱”问题——太多嵌套的回调函数像意大利面条一样纠缠在一起。当这种情况发生时，失败不会被报告给正确的回调，因为我们甚至可能忘记处理每个回调中的所有错误。这对新开发人员来说尤其令人困惑。

```
const fs = require('fs')

const callbackHell = () => {
  return fs.readFile(filePath, (err, res)=> {
    if(res) {
      firstCallback(args, (err, res1) => { 
        if(res1) {
          secondCallback(args, (err, res2) => {
            if(res2) {
              thirdCallback(args,  (err, res3) => {
                  // and so on...
              }
            }
          }
        }
      }
    } 
  })
}
```

上面的例子显示了一个典型的回调地狱。处理这些问题的一种方法是将回调分成更小的函数，就像我们在前面的例子中所做的那样。此外，promises 和 async/await 可以解决一些相关的挑战。

### 将回拨转换为承诺

使用我们之前的基于回调的例子，在这一节中，我们将 promisify 它——重写它以使用 promise。我们走吧:

```
const fs = require('fs')

const A = (filePath) => {
  const promise = new Promise((resolve, reject) => {  
  return fs.readFile(filePath, (error, result) => {
    if (error) {
    reject(error)
    }
    resolve(result)
   })
 })
  return promise
}

const B  = () => {
  A('./file.md').then((data)=>{
     if(data) {
      for (i=0; i < data.length; i++) {
        console.log(i)
     }
   }
 }).catch((error)=>{
    // handle errors
    console.log(error)
  })
  console.log('Result is not yet back from function A')
}  

B()

// output as above
Result is not yet back from function A
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
10
```

> **注意**:正如我们在上面看到的，我们已经能够使用`Promise()`构造函数将之前的例子从回调转换为承诺。我们将在下一节深入探讨承诺。

在 Node 中将回调转换为承诺更加容易，因为通过内置的`util.promisify()`API,[改进了对承诺](https://nodejs.org/en/blog/release/v8.0.0/#improved-support-for-promises)的支持。这里有一个[链接](https://repl.it/@firebase007/callback-promise-example)到操场来运行代码。

## 承诺

承诺是表示异步调用最终完成或失败的对象。这意味着，就像回调一样，承诺直观地帮助我们处理没有立即执行的操作的错误和成功响应，尽管是以一种更好、更干净的方式。

在 ES2015 规范中，promise 是围绕常规回调函数的包装函数。为了构造一个承诺，我们使用了`Promise()`构造函数，正如我们在前面的例子中看到的，将回调转换或承诺为承诺。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`Promise()`构造函数有两个参数:resolve 和 reject，它们都是回调函数。我们可以在回调中运行一个异步操作，如果成功就解析，如果失败就拒绝。下面是我们如何使用构造函数声明一个承诺:

```
const promiseExample = new Promise((resolve, reject) => {
    // run an async action and check for the success or failure
    if (success) {
      resolve('success value of async operation')
    }
    else {
      reject(throw new Error('Something happened while executing async action'))
  }
})
```

上面的函数返回一个新的承诺，该承诺最初处于待定状态。在这种情况下，`resolve`和`reject`充当回调函数。当承诺以成功值解决时，我们说它现在处于实现状态。另一方面，当它返回错误或被拒绝时，我们说它处于拒绝状态。为了利用上述承诺:

```
promiseExample.then((data) => {
  console.log(data) // 'success value of async operation'
}).catch((error) => {
  console.log(error) // 'Something happened while executing async action'
}).finally(() => {
  console.log('I will always run when the promise must have settled')
})
```

> **注意**:在上面的例子中，`finally`块帮助处理其他东西——例如，当承诺被解决或者已经完成操作时，清理逻辑。它并不意味着处理 promise 结果，而是处理任何其他清理代码。

此外，我们可以手动将价值转换为承诺，如下所示:

```
const value = 100

const promisifiedValue = Promise.resolve(value)

console.log(promisifiedValue)

promisifiedValue.then(val => console.log(val)).catch(err => console.log(err))

//output below
Promise { 100 }
Promise { <pending> }
100
```

> **注意**:这也适用于用`Promise.reject(**new** Error('Rejected'))`拒绝承诺。

### `Promise.all`

`Promise.all`返回一个承诺，该承诺等待数组中的所有承诺解析，然后解析为这些承诺返回的值的数组，通常与原始数组的顺序相同。如果数组中的任何承诺被拒绝，`Promise.all`的结果本身也被拒绝。签名如下所示:

```
Promise.all([promise1, promise2]).then(([res1, res2]) => console.log('Results', res1, res2))
```

在上面的例子中，`promise1`和`promise2`都是返回承诺的函数。要了解更多关于`Promise.all`的信息，请看一下 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)中关于承诺的精彩文档。

### 承诺链

履行承诺的一个妙处是连锁。我们可以将几个`then`链接在一起，以转换早期承诺的返回值，或者一个接一个地运行其他额外的异步操作。以我们之前的例子为例，让我们看看如何将承诺串联起来:

```
const value = 100

const promisifiedValue = Promise.resolve(value)

promisifiedValue.then( (val) => {
  console.log(val) // 100
  return val + 100
}).then( (val) => {
  console.log(val) // 200
})
// and so on
```

### 承诺的问题

最常见的 promise 反模式有:

*   延迟的反模式，当“延迟的”对象被无缘无故地创建时，会使您的代码冗长且难以理解
*   `.then`(成功，失败)反模式，使用承诺作为美化的回调

关于这些话题的更多细节可以通过蓝鸟维基找到[这里](https://github.com/petkaantonov/bluebird/wiki/Promise-anti-patterns#the-deferred-anti-pattern)。

## 异步/等待

多年来，JavaScript 从 2015 年标准化的回调到承诺，再到 2017 年标准化的异步/等待。异步函数允许我们编写一个异步程序，就像它是同步的一样。特别重要的是，我们刚刚在上一节中讨论了承诺，因为异步函数在幕后使用承诺。

因此，理解承诺如何工作是理解 async/await 的关键。

异步函数的签名由 function 关键字前的单词`async`标记。此外，可以通过在方法名前写`async`来使方法异步。当这样的函数或方法被调用时，它返回一个承诺。它一回来，承诺就解决了；如果抛出异常，承诺将被拒绝。

每个异步函数实际上都是一个`[AsyncFunction](%22https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction)`对象。例如，假设我们有一个返回承诺的异步函数:

```
const asyncFun = () => {
  return new Promise( resolve => {
    // simulate a promise by waiting for 3 seconds before resolving or returning with a value
    setTimeout(() => resolve('Promise value returned'), 3000)
  })
}
```

现在我们可以用一个异步函数包装上面的承诺，并在函数中等待承诺的结果。代码片段如下所示:

```
// add async before the func name
async function asyncAwaitExample() {
  // await the result of the promise here
  const result = await asyncFun()
  console.log(result)  // 'Promise value returned' after 3 seconds
}
```

注意，在上面的例子中，`await`将暂停承诺的执行，直到它被解决。关于 async/await 的更多细节可以通过 MDN 在找到[。](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await)

### 异步/等待解决什么

在处理异步行为时，Async/await 提供了更简洁的语法。虽然承诺伴随着许多样板文件，但异步函数在它的基础上构建了一个抽象。因此，异步函数只是常规承诺的语法糖衣。总之，对于异步函数:

1.  最终的代码更加简洁，因此也更容易调试
2.  错误处理要简单得多，因为它依赖于`try…catch`，就像其他同步代码一样，等等。

### 顶级等待

[到](https://github.com/tc39/proposal-top-level-await)p 级等待，目前处于 ECMAScript 规范的第 3 阶段，允许开发者在异步函数之外使用`await`关键字。在此之前，浏览器和节点都不支持这种语言的特性。

因此，根据我们前面的 async/await 示例，如果我们这样做了:

```
// here the returned `asyncFun()`promise is not wrapped in an async
const result = await asyncFun()

console.log(result) 
// this would throw a SyntaxError: await is only valid in async function
```

在此之前，为了模拟这种行为，我们利用了立即调用的函数表达式:

```
const fetch = require("node-fetch")
(async function() {
  const data = await fetch(url)
  console.log(data.json())
}())
```

本质上，由于我们习惯于在代码中使用 async/await，现在可以单独使用`await`关键字，想象一个模块可以在后台充当一个大的异步函数。

有了这个新的顶级 await 特性，下面的代码片段就像您期望的 async/await 函数那样工作。在这种情况下，它使 es 模块能够充当全局异步函数。

```
const result = await asyncFun()

console.log(result)  // 'Promise value returned'
```

> **注意**:要了解更多关于顶级 await 特性的用例及注意事项，我们可以在这里看一下 V8 文档[。](https://v8.dev/features/top-level-await)

## JavaScript 中的异步与并行

正如我们前面讨论的，JavaScript 有一个基于事件循环和异步 API 的并发模型。另一方面，在主流浏览器的支持下，web workers 使得在后台线程中并行运行操作成为可能，与操作的主执行线程分离。

### Web Worker API

异步函数有一些限制。正如我们前面所学的，我们可以通过使用回调、承诺或 async/await 来使我们的代码异步。当我们想要调度和处理长时间运行的操作时，这些浏览器和节点 API 真的很方便。

但是，如果我们有一个需要很长时间才能解决的高度计算密集型任务，比如一个非常大的`for`循环，该怎么办呢？在这种情况下，我们可能需要另一个专用线程来处理这些操作，释放主线程来做其他工作。这就是 Web Worker API 发挥作用的地方。它引入了并行执行我们代码的可能性。

异步函数有其局限性，并且只能解决与 JavaScript 的单一执行线程相关的一小部分问题。Web workers 通过为我们的程序引入一个独立的线程来执行 JavaScript 代码，而不会阻塞事件循环。

让我们用一个例子来理解如何创建一个 web worker:

```
const worker = new Worker('file.js')
```

根据上面的内容，我们用构造函数创建了一个新的 worker。我们还指定了要在工作线程中执行的脚本的路径。因为它们在后台的独立线程中运行，所以要执行的代码包含在一个单独的 JavaScript 文件中。

为了向专门的工作人员发送消息，我们可以使用`[postMessage()](https://developer.mozilla.org/en-US/docs/Web/API/Worker/postMessage)` API 和`[Worker.onmessage](https://developer.mozilla.org/en-US/docs/Web/API/Worker/onmessage)`事件处理程序。要终止一个工人，我们可以调用`[terminate()](https://developer.mozilla.org/en-US/docs/Web/API/Worker/terminate)`方法。要了解更多信息，请查看 MDN 文档的[本节](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers)和[本节](https://developer.mozilla.org/en-US/docs/Web/API/Worker)。

### Web worker 限制

网络工作者受到限制，因为他们:

*   无权访问浏览器 DOM
*   有不同的全局范围，称为`WorkerGlobalScope`
*   强制从同一来源加载文件

## 结论

在本文中，我们研究了 JavaScript 中异步编程的发展，从回调到承诺再到异步/等待。我们还回顾了 Web Worker API。

我们已经看到回调是传递给其他函数的简单函数，并且只在事件完成时执行。我们还看到回调和承诺是等价的，因为回调可以被包装以公开基于承诺的接口，反之亦然。

此外，我们已经看到异步函数在后台独立运行，不会干扰应用程序的主线程。由于它们的性质，它们可以在准备好的时候返回响应(数据或错误),因此不会干扰应用程序中其他正在运行的进程。

我们还学习了 web 工作者如何从程序的主执行线程中分离出一个新的线程。

要了解更多关于这些概念的知识，关于异步 JavaScript 和其他主题的 MDN 文档总是一个很好的起点。

再次感谢您的阅读，请在下面的评论区留下您的问题和评论，或者联系 [Twitter](https://twitter.com/alex_nnakwue) 。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.