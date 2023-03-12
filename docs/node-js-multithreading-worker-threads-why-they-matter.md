# Node.js 多线程:工作线程及其重要性

> 原文：<https://blog.logrocket.com/node-js-multithreading-worker-threads-why-they-matter/>

***编者的** **注**:本帖于 2022 年 1 月 18 日更新，包含了一些关于 Web Workers API 和 Web Workers 总体的新信息，改进并添加了关键术语的定义，并反映了对`worker_threads`模块的稳定支持。*

自从 [Node.js v10.5.0](https://nodejs.org/en/blog/release/v10.5.0/) 发布后，又有一个新的`worker_threads`模块可用，这个模块从 Node.js v12 LTS 开始就一直很稳定。

这个工作线程模块到底是什么，我们为什么需要它？在本帖中，我们将讨论工作线程以及:

*   JavaScript 和 Node.js 中实现并发的历史原因
*   我们可能发现的问题及其当前的解决方案
*   工作者线程并行处理的未来

## 单线程 JavaScript 的历史

JavaScript 被认为是一种在浏览器中运行的单线程编程语言。单线程意味着在同一个进程中(在这种情况下是浏览器，或者只是现代浏览器中的当前选项卡)任何时候都只执行一组指令。

这对开发人员来说更容易，因为 JavaScript 最初只是一种用于向网页添加交互、表单验证等的语言——不需要多线程的复杂性。

Ryan Dahl 在创建 Node.js 时将这一限制视为一个机会。他希望实现一个基于异步 I/O 的服务器端平台，以避免对线程的需求，并使事情变得更加简单。

但是并发可能是一个很难解决的问题。让多个线程访问同一个内存会产生很难重现和修复的争用情况。

## Node.js 是单线程的吗？

实际上，我们的 Node.js 应用程序只是单线程的。我们可以并行运行，但我们不创建线程或同步它们。虚拟机和操作系统为我们并行运行 I/O，当需要将数据发送回我们的 JavaScript 代码时，是 JavaScript 在单线程中运行。

换句话说，除了我们的 JavaScript 代码之外，一切都是并行运行的。JavaScript 代码的同步块总是一次运行一个:

```
let flag = false
function doSomething() {
  flag = true
  // More code (that doesn't change `flag`)...

  // We can be sure that `flag` here is true.
  // There's no way another code block could have changed
  // `flag` since this block is synchronous.
}

```

如果我们所做的只是异步 I/O，这就太好了。我们的代码由运行速度很快并将数据传递给文件和流的小部分同步块组成，因此我们的 JavaScript 代码非常快，不会阻塞其他 JavaScript 代码的执行。

等待 I/O 事件发生所花费的时间比执行 JavaScript 代码所花费的时间要多得多。让我们看一个简单的例子:

```
db.findOne('SELECT ... LIMIT 1', function(err, result) {
  if (err) return console.error(err)
  console.log(result)
})
console.log('Running query')
setTimeout(function() {
  console.log('Hey there')
}, 1000)

```

也许这个数据库查询需要一分钟，但是在调用查询后会立即显示“正在运行查询”消息。我们将在调用查询后一秒钟看到“嘿，你好”消息，而不管查询是否仍在运行。

我们的 Node.js 应用程序只是调用函数，并不阻止其他代码的执行。当查询完成时，它将通过回调得到通知，我们将收到结果。

## 需要线程来执行 CPU 密集型任务

如果我们需要做同步密集型的事情，比如在大型数据集中的内存中进行复杂的计算，会发生什么？那么我们可能会有一个同步的代码块，它花费了很多时间，并且会阻塞其余的代码。

想象一下，一次计算需要 10 秒钟。如果我们正在运行一个 web 服务器，这意味着所有其他请求都会因为这个计算而被阻塞至少 10 秒钟。那是一场灾难；任何超过 100 毫秒的都可能太多。

JavaScript 和 Node.js 并不打算用于 CPU 密集型任务。由于 JavaScript 是单线程的，这将冻结浏览器中的 UI，并将 Node.js 中的任何 I/O 事件排队。

回到前面的例子，假设我们现在有一个返回几千个结果的查询，我们需要解密 JavaScript 代码中的值:

```
db.findAll('SELECT ...', function(err, results) {
  if (err) return console.error(err)

  // Heavy computation and many results
  for (const encrypted of results) {
    const plainText = decrypt(encrypted)
    console.log(plainText)
  }
})

```

一旦有结果，我们将在回拨中得到。然后，在我们的回调完成执行之前，不会执行其他 JavaScript 代码。

通常，正如我们之前所说的，代码很少并且足够快，但是在这种情况下，我们有许多结果，并且我们需要对它们执行繁重的计算。这可能需要几秒钟的时间，任何其他 JavaScript 执行将在这段时间内排队，这意味着如果我们在同一个应用程序中运行服务器，我们可能会在这段时间内阻塞所有用户。

## 为什么 JavaScript 中永远不会有多线程

因此，在这一点上，许多人可能认为我们的解决方案应该是在 Node.js 核心中添加一个新模块，并允许我们创建和同步线程。

但那是不可能的。如果我们给 JavaScript 添加线程，那么我们正在改变语言的本质。我们不能仅仅将线程作为一组新的可用类或函数来添加——我们可能需要改变语言来支持多线程。目前支持它的语言都有关键字，比如`synchronized`，以使线程能够协作。

遗憾的是，我们没有一个好的方法来解决 Node.js 等成熟的服务器端平台中的这种用例。例如，在 Java 中，[甚至一些数值类型也不是原子的](https://dzone.com/articles/longdouble-are-not-atomic-in-java)；如果不同步它们的访问，可能会导致两个线程改变一个变量的值。

结果是，在两个线程都访问了变量之后，一个线程更改了几个字节，另一个线程更改了几个字节，因此不会产生任何有效值。

## 简单的解决方案:同步代码分割

Node.js 不会计算事件队列中的下一个代码块，直到上一个代码块执行完毕。因此，我们可以做的一件简单的事情是将我们的代码分成更小的同步代码块，并调用`setImmediate(callback)`来告诉 Node.js 我们完成了。这样，它可以继续执行队列中未完成的事情；或者，换句话说，它可以移动到事件循环的下一次迭代(或“滴答”)。

让我们来看看如何重构前面例子中的一些代码来利用这一点。假设我们有一个要处理的大型数组，数组中的每一项都需要 CPU 密集型处理:

```
const arr = [/*large array*/]
for (const item of arr) {
  // do heavy stuff for each item on the array
}
// code that runs after the whole array is executed

```

就像我们之前说的，如果我们这样做，处理整个数组将花费太多时间，并且 JavaScript 的其余执行将被阻塞。让我们把它分成更小的块，并使用`setImmediate(callback)`:

```
const crypto = require('crypto')

const arr = new Array(200).fill('something')
function processChunk() {
  if (arr.length === 0) {
    // code that runs after the whole array is executed
  } else {
    console.log('processing chunk');
    // pick 10 items and remove them from the array
    const subarr = arr.splice(0, 10)
    for (const item of subarr) {
      // do heavy stuff for each item on the array
      doHeavyStuff(item)
    }
    // Put the function back in the queue
    setImmediate(processChunk)
  }
}

processChunk()

function doHeavyStuff(item) {
  crypto.createHmac('sha256', 'secret').update(new Array(10000).fill(item).join('.')).digest('hex')
}

// This is just for confirming that we can continue
// doing things
let interval = setInterval(() => {
  console.log('tick!')
  if (arr.length === 0) clearInterval(interval)
}, 0)

```

现在，我们可以在每次事件循环运行时处理 10 个项目，并调用`setImmediate(callback)`,这样如果程序需要做其他事情，它将在这 10 个项目的块之间完成。我添加了一个`setInterval()`来演示这一点。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

如您所见，代码变得更加复杂。而且很多时候算法比这个复杂很多，很难知道把`setImmediate()`放在哪里才能找到好的平衡。此外，现在的代码是异步的，如果我们依赖第三方库，我们可能无法将执行分成更小的块。

## 在后台运行并行进程，没有线程

因此，`setImmediate()`对于一些简单的用例来说已经足够了，但是它远不是一个理想的解决方案。我们可以不用线程进行并行处理吗？是啊！

我们需要的是某种后台处理，一种运行带有输入的任务的方式，它可以使用任何数量的 CPU 和时间来将结果返回给主应用程序。大概是这样的:

```
// Runs `script.js` in a new environment without sharing memory.
const service = createService('script.js')
// We send an input and receive an output
service.compute(data, function(err, result) {
  // result available here
})

```

事实上，我们已经可以在 Node.js 中进行后台处理了:我们可以[派生出流程](https://nodejs.org/api/child_process.html#child_process_child_process_fork_modulepath_args_options)并使用消息传递来完成这一任务，您可以想象将消息从一个流程传递到另一个流程。这实现了以下目标:

*   主进程可以通过发送和接收事件与子进程进行通信
*   没有共享内存
*   所有交换的数据都是“克隆的”，这意味着在一端改变数据不会在另一端改变它
*   如果我们不共享内存，我们就没有竞争条件，我们也不需要线程！

好吧，等一下。这是一个解决方案，但不是理想的解决方案。派生一个进程既昂贵又缓慢——这意味着从头开始运行一个新的虚拟机并使用大量内存，因为进程不共享内存。

我们可以重用相同的分叉流程吗？当然，但是在分叉的流程中发送不同的繁重、同步执行的工作负载会产生两个问题:

1.  您可能不会阻塞主应用程序，但是分叉的进程一次只能处理一个任务
2.  如果一个任务使流程崩溃，它将使发送到同一流程的所有任务都没有完成

如果你有两个任务——一个需要`10s`，另一个需要`1s`，按照这个顺序——等待`10s`执行第二个任务并不理想。如果这个任务因为另一个进程的阻碍而无法执行，那就更不理想了。

由于我们正在分叉进程，我们希望利用我们的操作系统的调度和我们机器的所有核心。就像你可以同时听音乐和浏览互联网一样，你可以分叉两个进程，并行执行所有的任务。

为了修复这些问题，我们需要多个分叉，而不是一个。但是我们需要限制分叉进程的数量，因为每个进程都将所有虚拟机代码复制到内存中，这意味着每个进程只有几兆字节，启动时间也不短。

## 使用`worker-farm`来池化线程

与数据库连接一样，我们需要一个随时可用的进程池，在每个进程池中一次运行一个任务，并在任务完成后重用该进程。这看起来实现起来很复杂，如果我们从头开始构建它，那就更复杂了！

让我们用`[worker-farm](https://www.npmjs.com/package/worker-farm)`来帮助我们:

```
// main app
const workerFarm = require('worker-farm')
const service = workerFarm(require.resolve('./script'))

service('hello', function (err, output) {
  console.log(output)
})

// script.js
// This will run in forked processes
module.exports = (input, callback) => {
  callback(null, input + ' ' + world)
}

```

问题解决了吗？虽然我们已经解决了这个问题，但是我们仍然比多线程解决方案使用了更多的内存。

与分叉进程相比，线程在资源方面仍然是非常轻量级的。这就是工作者线程诞生的原因。

## 什么是工作线程？

工作线程有独立的上下文。它们使用消息传递与主进程交换信息，因此我们避免了常规线程所具有的竞争条件问题！但是它们确实生活在同一个进程中，所以它们使用的内存要少得多。

您可以与工作线程共享内存，并传递专门用于此目的的`ArrayBuffer`或`SharedArrayBuffer`对象。只有在需要处理大量数据的 CPU 密集型任务时，才使用它们。在[这篇文章](https://blog.logrocket.com/use-cases-for-node-workers/)中讨论了一些使用节点工作器的 CPU 密集型任务的例子。它们允许您避免对数据序列化的需求。

## **为多个任务使用工作线程**

如果您运行 Node.js ≥ v10.5.0，您现在就可以开始使用`worker_threads`模块。但是，如果您使用的是≤ 11.7.0 的任何版本，您需要在调用 Node.js 时使用`--experimental-worker`标志来启用它。

请记住，创建一个 Worker——尽管它比派生一个流程便宜得多——也会根据您的需要使用太多的资源。在这种情况下，文件[建议你创建一个工人库](https://nodejs.org/api/worker_threads.html#:~:text=The%20above%20example%20spawns%20a%20Worker%20thread%20for%20each%20parseJSAsync()%20call.%20In%20practice%2C%20use%20a%20pool%20of%20Workers%20for%20these%20kinds%20of%20tasks.%20Otherwise%2C%20the%20overhead%20of%20creating%20Workers%20would%20likely%20exceed%20their%20benefit.)。

你可以在 npm 中寻找一个通用的[池实现](https://itnext.io/a-workerpool-from-scratch-in-typescript-and-node-c4352106ffde)或者一个特定的[池实现，而不是创建你自己的池实现。Node.js 提供了](https://www.npmjs.com/package/workerpool) [AsyncResource](https://nodejs.org/api/async_context.html#using-asyncresource-for-a-worker-thread-pool) 来提供对工作池的正确异步跟踪。

让我们看一个简单的例子。首先，我们将实现主文件，创建一个工作线程，并给它一些数据。API 是事件驱动的，但我将把它包装成一个承诺，在从 Worker 收到的第一条消息中解析:

```
// index.js
// run with node --experimental-worker index.js on Node.js 10.x
const { Worker } = require('worker_threads')

function runService(workerData) {
  return new Promise((resolve, reject) => {
    const worker = new Worker('./service.js', { workerData });
    worker.on('message', resolve);
    worker.on('error', reject);
    worker.on('exit', (code) => {
      if (code !== 0)
        reject(new Error(`Worker stopped with exit code ${code}`));
    })
  })
}

async function run() {
  const result = await runService('world')
  console.log(result);
}

run().catch(err => console.error(err))

```

正如您所看到的，这就像将文件名作为参数和我们希望工人处理的数据传递一样简单。请记住，这些数据是克隆的，不在任何共享内存中。

当我们完成时，我们通过监听`message`事件来等待工作线程给我们发送消息。实现服务:

```
const { workerData, parentPort } = require('worker_threads')

// You can do any heavy stuff here, in a synchronous way
// without blocking the "main thread"
parentPort.postMessage({ hello: workerData })

```

这里，我们需要两样东西:主应用程序发送给我们的`workerData`和一个将信息返回给主应用程序的方法。这是用有一个`postMessage`方法的`parentPort`来完成的，在这个方法中我们将传递我们处理的结果。

就是这样！这是最简单的例子，但是我们还可以构建更复杂的东西——例如，如果我们需要提供反馈，我们可以从工作线程发送多条消息来指示执行状态。

或者我们可以发送部分结果。假设您正在处理成千上万的图像。也许您想为每一个处理过的图像发送一条消息，但是您不想等到所有的图像都被处理完。

为了运行该示例，如果您使用的是 Node.js 11.7 之前的任何版本，请记住使用`--experimental-worker`标志:

```
node --experimental-worker index.js

```

欲了解更多信息，请查看`[worker_threads documentation](https://nodejs.org/docs/latest-v10.x/api/worker_threads.html)`。

## 什么是 Web Workers API？

也许你听说过 [Web Workers API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API) 。API 和`worker_threads`不一样，因为需求和技术条件不一样，但是可以解决浏览器运行时类似的问题。

Web Workers API 更加成熟，并且得到了现代浏览器的良好支持。如果您正在进行加密挖掘、压缩/解压缩、图像处理、计算机视觉(例如，人脸识别)等，它会非常有用。在您的 web 应用程序中。

### 介绍:Partytown

Web workers 也可以用来运行第三方脚本。从主线程运行繁重的脚本会导致站点出现 UX 问题，这并不理想，但是脱离主线程运行脚本会产生问题，因为我们无法直接访问主线程 API，如`window`、`document`或`localStorage`。

这就是 [Partytown](https://github.com/BuilderIO/partytown) 的用武之地。Partytown 是一个延迟加载的 6kB 包，可以帮助你解决上面提到的问题。您的第三方包将按预期运行，而不会影响主线程。如果你有兴趣尝试一下，请看[我们的另一篇探索图书馆的帖子](https://blog.logrocket.com/using-web-workers-boost-third-party-script-performance/)，或者[查看他们的文档](https://github.com/BuilderIO/partytown)进行更深入的讨论。

## 结论

如果您需要在 Node.js 应用程序中执行 CPU 密集型任务，那么,`worker_threads`是一个令人兴奋且有用的模块。它们提供了与不共享内存的线程相同的行为，从而避免了线程引入的潜在竞争条件。自从`worker_threads`模块在 Node.js v12 LTS 中变得稳定后，您应该可以放心地在生产级应用程序中使用它了！