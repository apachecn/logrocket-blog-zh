# 调试 Node.js 中的异步操作

> 原文：<https://blog.logrocket.com/debugging-async-operations-in-node-js/>

你曾经在 Node.js 中调试过异步操作吗？

为什么我的回调没有被调用？为什么我的程序挂起？哪个异步操作导致了该问题？如果你已经问过类似的问题，那么你就会知道诊断有多困难，以及为什么我们需要尽可能多的帮助。

在 JavaScript 中使用异步操作时，我们可能会遇到很多麻烦，但是 Node.js 有一个新工具可以帮助我们减轻痛苦。它叫做[异步钩子 API](https://nodejs.org/api/async_hooks.html) ，我们可以用它来理解我们的应用程序中的异步操作是怎么回事。

但是，Node.js API 本身是非常低级的，对于任何严肃的 Node.js 应用程序来说，您都会被正在进行的大量异步操作所淹没，其中大多数操作您都不会关心！这对普通开发人员来说不是很有用。

与其他关于这个主题的博客不同，这篇博客不会只是向你重复 Node.js 文档。相反，我将向您展示一个简单但非常有用的高级异步调试库，它构建在异步钩子 API 之上。

您将了解创建这样的库所涉及的一些困难，以及如何避开它们。在这篇博文之后，你应该对如何构建你自己的异步调试库，或者说，如何升级我的库有所了解。

## 获取示例代码

这篇博文的示例代码是 GitHub 上的[。](https://github.com/ashleydavis/debugging-async-operations-in-nodejs)

我已经在 Node.js v12.6.0 上测试了这段代码，但是它应该也可以在从 v8.17.0 开始的任何版本上运行。在 Node.js 的不同版本和不同平台上，结果可能会有所不同。如果您发现任何问题，请在 GitHub 上记录问题。

要运行本文中的示例，请创建示例代码存储库的本地克隆，然后运行 npm install:

```
git clone https://github.com/ashleydavis/debugging-async-operations-in-nodejs
cd debugging-async-operations-in-nodejs
npm install
```

## 背景

我实际上是在开发 [Data-Forge Notebook](https://www.data-forge-notebook.com/) 时开发的这段代码，用户可以评估他们的笔记本，并让代码在 Node.js 的一个单独的专用实例中运行。

笔记本编辑器在评估过程中会显示一个进度指示器，因此它需要知道评估何时完成。只有通过跟踪正在进行的异步操作的数量才能知道这一点。

我花了很多时间才弄清楚 Node.js 中跟踪异步操作的复杂性和边缘情况。我在这里提供了一个简化的异步调试代码库，希望它能帮助您理解自己应用程序中的异步操作。

## 基础知识

让我们快速了解一下基本情况。这已经被大量的博客文章所覆盖，并且在 Node.js 文档中也有足够的覆盖。

下面的清单 1 显示了初始化节点所需的简单代码。js 异步挂钩 API，这样我们就可以开始跟踪异步操作。

##### [清单 1](https://github.com/ashleydavis/debugging-async-operations-in-nodejs/blob/7ca8c9dae2c39ba73a2ee6cc1c168e0b83b1ceb1/lib/async-tracker.js#L110) :初始化 Node.js 异步钩子 API

```
this.asyncHook = async_hooks.createHook({ 
    init: (asyncId, type, triggerAsyncId, resource) => {
        this.addAsyncOperation(asyncId, type, triggerAsyncId, resource);
    },
    destroy: asyncId => {
        this.removeAsyncOperation(asyncId, "it was destroyed");
    },
    promiseResolve: asyncId => {
        this.removeAsyncOperation(asyncId, "it was resolved");
    },
});

this.asyncHook.enable();
```

在清单 1 中，我们有一个单独的`init`回调，每当创建一个新的异步操作时都会调用这个回调。然后，我们将这个异步操作添加到我们的动态列表中。

我们还可以看到，一个操作有两种结束方式:通过`destroy`或`promiseResolve`。这迎合了传统的异步操作和承诺。此时，我们可以从动态列表中删除异步操作。

这很简单，不是吗？

如果跟踪异步操作如此简单，那么我们为什么需要比这更进一步呢？让我们找出答案。

## 为什么我们需要更高级别的异步调试库？

不幸的是，Node.js 异步钩子 API 太低级了。在一个大的应用程序中，我们可能在任何给定的时间都有大量的异步操作在运行。它们中的大部分都不是问题，跟踪所有的问题也没有太大的帮助，因为找到一个特定的问题就像大海捞针一样。

相反，我们应该能够跟踪由受限代码段创建的异步操作，然后我们可以逐步缩小我们的问题域，以找到那些有问题的操作。

这就是为什么我构建了更高级别的异步调试器(你可以在代码库中的 [lib 目录](https://github.com/ashleydavis/debugging-async-operations-in-nodejs/tree/master/lib)下找到它的代码)。它使我们能够集中精力，以便我们能够明智地缩小问题范围，并对问题的根源进行三角测量。

此外，我们希望了解异步操作之间的关系，这样我们就可以沿着从异步操作到其祖先的(可能很长的)链条追溯到原始代码行。

## 我们必须解决的困难

为了在我们的应用程序中有效地调试异步操作，我们必须面对以下困难:

1.  控制台日志记录是异步的。如果您的代码做了大量的日志记录，那么它会生成大量多余的异步操作。这使得干草堆变得更大。
2.  控制台日志记录创建至少一个全局异步操作(例如，用于标准输出)，根据异步挂钩 API，该操作生成一个未完成的异步操作。Node.js 还有其他全局变量，当它们的模块被导入到您的应用程序中时，可能会导致突出的异步操作。
3.  异步操作可以发生在复杂的链中。我们需要能够链接相关的异步操作，这样我们就可以追溯到整个链，找到原始代码。

我们将在这篇博文中解决这些问题。我已经给它们编了号，这样我就可以回头查阅了。

## 最简单的例子

让我向您展示使用异步调试器库的最简单的例子。清单 2 展示了一个跟踪简单超时操作的例子。

##### [清单 2](https://github.com/ashleydavis/debugging-async-operations-in-nodejs/blob/master/example-1.js) :使用异步调试器跟踪最简单的异步操作

```
const { AsyncDebugger } = require("./lib/async-debugger.js");

function doTimeout() {
    console.log("Starting timeout.");

    setTimeout(() => {
        console.log("Timeout finished.");
    }, 2000);
}

const asyncDebugger = new AsyncDebugger();
asyncDebugger.notifyComplete(() => console.log("All done!"));
asyncDebugger.startTracking("test-1", doTimeout);

console.log("End of script");
```

在清单 2 中，我们希望将异步操作的跟踪限制在函数`doTimeout`中。这是一个创建超时的简单函数，但是请想象一下，在一个真实的场景中，这里会启动一个复杂的异步操作链。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

对`notifyComplete`和`startTracking`的调用展示了配置异步调试器的两种主要方式。通过`notifyComplete`，我们设置了一个回调函数，当所有的异步操作完成后，这个函数将被调用。

这只关心实际被跟踪的异步操作，在这个例子中，这只关心在`doTimeout`函数中启动的异步操作。任何在`doTimeout`之外发起的异步操作都会被异步调试器忽略。

函数`startTracking`开始跟踪异步操作。这里我们传入了`doTimeout`函数。异步调试器调用这个函数，并使用低级 API 来跟踪它发起的异步操作。

您应该运行`[example-1.js](https://github.com/ashleydavis/debugging-async-operations-in-nodejs/blob/master/example-1.js)`中的代码，看看会发生什么:

```
node example-1.js
```

您将看到创建了五个低级异步操作来支持我们的超时:

```
%% add 4, type = TTYWRAP, parent = 3, context = 3, test-1 #ops = 1, total #ops = 1
%% add 5, type = SIGNALWRAP, parent = 3, context = 3, test-1 #ops = 2, total #ops = 2
Starting timeout.
%% add 6, type = TickObject, parent = 3, context = 3, test-1 #ops = 3, total #ops = 3
%% add 7, type = Timeout, parent = 3, context = 3, test-1 #ops = 4, total #ops = 4
End of script
%% remove 6, reason = it was destroyed, context = 3, test-1 #ops = 3, total #ops = 3
Timeout finished.
%% add 1082, type = TickObject, parent = 7, context = 3, test-1 #ops = 4, total #ops = 4
%% remove 7, reason = it was destroyed, context = 3, test-1 #ops = 3, total #ops = 3
%% remove 1082, reason = it was destroyed, context = 3, test-1 #ops = 2, total #ops = 2

```

你可能会问的第一个问题是，为什么我们有这么多的超时异步操作？超时本身只需要一个异步操作；其他操作由`console.log`生成，它恰好是异步的(困难 1)。

这里真正的问题是我们的应用程序已经挂起了。这并不是我们正在调试的代码的真正问题(没有任何问题)；相反，这是我们如何跟踪全局异步操作的问题(第二个难点)。

我的第一个想法是，我们需要强制垃圾收集并清理剩余的异步操作。这可能是一个问题，但这里不是这样，我稍后将再次回到垃圾收集问题。

我们可以在`[example-2.js](https://github.com/ashleydavis/debugging-async-operations-in-nodejs/blob/master/example-2.js)`中看到这个问题的解决方案。这与`example-1.js`相同，但是在我们开始跟踪之前增加了一个对`console.log`的调用。令人惊讶的是，这使得代码像预期的那样工作！现在运行它，看看会发生什么:

```
node example-2.js
```

现在您将看到我们的`notifyComplete`回调被调用，程序正常退出。这是为什么呢？

通过将`console.log`放在代码之外，我们强制在异步调试器的范围之外创建全局标准输出通道。因此它不知道也不关心。因为调试器知道的所有异步操作都已解决，所以它停止检查，我们的程序因此被允许退出。

为了让我们的调试器工作，我们必须改变我们的代码，这是相当恼人的，但是我还没有找到另一种方法来处理这种相当尴尬的情况。

## 调试一系列异步操作

现在我们已经知道了使用异步调试器库的基础，让我们用它来跟踪一个更复杂的异步操作的源代码。

在清单 3 中，您可以看到一个嵌套超时的例子。

##### [清单 3](https://github.com/ashleydavis/debugging-async-operations-in-nodejs/blob/master/example-3.js) :调试嵌套超时

```
function doTimeout() {
    console.log("Starting timeout.");

    setTimeout(() => {

        setTimeout(() => {
            console.log("Timeout finished.");
        }, 2000);

    }, 2000);
}
```

我们想追踪清单 3 中的嵌套超时，直到它起源的代码。显然，在这个简单的例子中，我们可以直接在我们正在查看的代码中看到这一点。这是因为代码在同一位置，易于阅读。

但是，想象一个更复杂的情况，其中异步链中有来自单独代码文件的链接。在这种情况下，跟踪异步操作链就不那么容易了。

运行`example-3.js`查看它生成的输出:

```
Starting up!
Starting timeout.
%% add 7, type = TickObject, parent = 6, context = 6, test-1 #ops = 1, total #ops = 1
%% add 8, type = Timeout, parent = 6, context = 6, test-1 #ops = 2, total #ops = 2
End of script
%% remove 7, reason = it was destroyed, context = 6, test-1 #ops = 1, total #ops = 1
%% add 1163, type = Timeout, parent = 8, context = 6, test-1 #ops = 2, total #ops = 2
%% remove 8, reason = it was destroyed, context = 6, test-1 #ops = 1, total #ops = 1
Timeout finished.
%% add 2323, type = TickObject, parent = 1163, context = 6, test-1 #ops = 2, total #ops = 2
%% remove 1163, reason = it was destroyed, context = 6, test-1 #ops = 1, total #ops = 1
%% remove 2323, reason = it was destroyed, context = 6, test-1 #ops = 0, total #ops = 0
```

您可以在上面的输出中看到内部超时(操作`1163`)如何与外部超时(操作`8`)相关联。

Node.js async hooks API 并不能让你很容易地关联异步操作链(困难 3)。然而，我的异步调试库将为您建立这些连接。

在清单 4 中，我展示了如何调试运行在标签`test-1`(我们的嵌套超时)下的代码。这将打印异步操作的树/链以及它们起源的代码行。

##### [清单 4](https://github.com/ashleydavis/debugging-async-operations-in-nodejs/blob/master/example-4.js) :调试特定的异步操作

```
asyncDebugger.notifyComplete(() => {
   asyncDebugger.debug("test-1");
});
```

这里的输出向您显示了异步操作的树、类型、状态和原始调用堆栈:

```
 |- 7 - TickObject - completed                                                                                                            
 |    at AsyncDebugger.addAsyncOperation (async-debugger.js:216:15)           
 |    at AsyncHook.init (async-debugger.js:163:26)                            
 |    at emitInitNative (internal/async_hooks.js:134:43)                                                                                  
 |    at emitInitScript (internal/async_hooks.js:341:3)                                                                                   
 |    at new TickObject (internal/process/task_queues.js:102:7)                                                                           
 |    at process.nextTick (internal/process/task_queues.js:130:14)                                                                        
 |    at onwrite (_stream_writable.js:472:15)                                                                                             
 |    at afterWriteDispatched (internal/stream_base_commons.js:149:5)                                                                     
 |    at writeGeneric (internal/stream_base_commons.js:137:3)                                                                             
 |    at WriteStream.Socket._writeGeneric (net.js:698:11)                                                                                 
 |- 8 - Timeout - completed                                                                                                               
 |    at AsyncDebugger.addAsyncOperation (async-debugger.js:216:15)           
 |    at AsyncHook.init (async-debugger.js:163:26)                            
 |    at emitInitNative (internal/async_hooks.js:134:43)                                                                                  
 |    at emitInitScript (internal/async_hooks.js:341:3)                                                                                   
 |    at initAsyncResource (internal/timers.js:147:5)                                                                                     
 |    at new Timeout (internal/timers.js:178:3)                                                                                           
 |    at setTimeout (timers.js:142:19)                                                                                                    
 |    at doTimeout (example-4.js:14:5)                                            
 |    at async-debugger.js:76:13                                              
 |    at AsyncResource.runInAsyncScope (async_hooks.js:172:16)                                                                            
 |   |- 1164 - Timeout - completed                                                                                                        
 |   |    at AsyncDebugger.addAsyncOperation (async-debugger.js:216:15)       
 |   |    at AsyncHook.init (async-debugger.js:163:26)                        
 |   |    at emitInitNative (internal/async_hooks.js:134:43)                                                                              
 |   |    at emitInitScript (internal/async_hooks.js:341:3)                                                                               
 |   |    at initAsyncResource (internal/timers.js:147:5)                                                                                 
 |   |    at new Timeout (internal/timers.js:178:3)                                                                                       
 |   |    at setTimeout (timers.js:142:19)                                                                                                
 |   |    at Timeout._onTimeout (example-4.js:16:9)                               
 |   |    at listOnTimeout (internal/timers.js:531:17)                                                                                    
 |   |    at processTimers (internal/timers.js:475:7)                                                                                     
 |   |   |- 2288 - TickObject - completed                                                                                                 
 |   |   |    at AsyncDebugger.addAsyncOperation (async-debugger.js:216:15)   
 |   |   |    at AsyncHook.init (async-debugger.js:163:26)                    
 |   |   |    at emitInitNative (internal/async_hooks.js:134:43)                                                                          
 |   |   |    at emitInitScript (internal/async_hooks.js:341:3)                                                                           
 |   |   |    at new TickObject (internal/process/task_queues.js:102:7)                                                                   
 |   |   |    at process.nextTick (internal/process/task_queues.js:130:14)                                                                
 |   |   |    at onwrite (_stream_writable.js:472:15)                                                                                     
 |   |   |    at afterWriteDispatched (internal/stream_base_commons.js:149:5)                                                             
 |   |   |    at writeGeneric (internal/stream_base_commons.js:137:3)                                                                     
 |   |   |    at WriteStream.Socket._writeGeneric (net.js:698:11)
```

## 跟踪异步操作的图形

那么异步调试器如何连接异步操作之间的关系呢？在内部，它构建了一个树形数据结构来管理关系并连接子异步操作和父异步操作。

每当 Node.js 异步钩子 API 通知一个新的异步操作时，它也会给我们父操作的 ID。我们可以用它来查找父操作的记录，然后添加新操作作为子操作。因此，我们可以构建一个树形数据结构来表示异步操作家族。

如果在我们的记录中没有找到父操作，我们可以将新操作记录为树中的一个新根(因此实际上我们可以有多个树，这取决于我们跟踪了多少段代码)。

## 跟踪异步操作的源

因此异步调试器可以在树中链接相关的异步操作。我们可以遍历树来找到发起异步操作的调用栈。为此，我们必须生成一个调用堆栈，并根据异步操作记录它。幸运的是，JavaScript 使得捕获当前调用堆栈变得非常容易，如清单 5 所示。

##### [清单 5](https://github.com/ashleydavis/debugging-async-operations-in-nodejs/blob/8deb9caff48bcfc82cab59f17b7f37ba60d548db/lib/async-debugger.js#L167) :在 JavaScript 中捕获调用堆栈

```
const error = {};
Error.captureStackTrace(error);

const stack = error.stack.split("\n").map(line => line.trim());
```

## 监控特定的代码段

没有必要监控应用程序中的所有异步操作。那只会让事情变得更加混乱。会有太多的噪音和太少的信号。为了找到问题的根源，我们需要逐步限制问题隐藏的空间，直到它无处可藏。

异步调试器通过`startTracking`函数实现了这一点。Node.js 异步挂钩 API 在启用时是一个钝工具。它通知我们应用程序中的每一个新的异步操作——甚至那些我们不感兴趣的操作。这里的技巧是知道哪些异步操作是相关的，这样我们就可以专注于这些操作。

我们可以通过强制我们希望调试的所有操作嵌套在一个已知的父操作下来实现这一点。当我们知道父操作的 ID 时，我们可以使用我们的树数据结构在父操作和任何后代操作之间建立连接。因此，我们可以知道任何给定的异步操作是否相关，是否应该被跟踪。

但是我们如何生成父操作呢？我们将使用`AsyncResource`类来合成一个人工异步操作。然后，我们可以捕获父操作的异步 ID，并使用它来标识要跟踪的子操作。

清单 6 展示了这是如何在异步调试器中实现的。异步挂钩函数`executionAsyncId`用于检索合成异步操作的异步 ID。然后，我们在父操作的上下文中运行用户代码。子进程生成的任何异步操作现在都将自动链接到父进程。

##### [清单 6](https://github.com/ashleydavis/debugging-async-operations-in-nodejs/blob/8deb9caff48bcfc82cab59f17b7f37ba60d548db/lib/async-debugger.js#L71) :合成一个父异步操作

```
const executionContext = new async_hooks.AsyncResource(label);
executionContext.runInAsyncScope(() => {
    const executionContextAsyncId = async_hooks.executionAsyncId();           
    // ... code omitted here …
    userCode(); // Run the user 
});
```

## 控制垃圾收集

还有一个我们应该解决的问题，但不幸的是，我无法在一个简单的代码示例中复制它。在更复杂的情况下，我发现 Node.js 垃圾收集器的间歇性会将一些异步操作记录为活动状态，时间比实际情况要长。

这显然只是 Node.js 异步钩子 API 如何报告异步操作移除的问题。这实际上不是一个生产问题，但是当试图调试异步操作时，它有时会使事情变得混乱。

我们可以通过定期强制垃圾收集来解决这个问题。如清单 7 所示，这是通过函数`global.gc`实现的。

##### [清单 7](https://github.com/ashleydavis/debugging-async-operations-in-nodejs/blob/8deb9caff48bcfc82cab59f17b7f37ba60d548db/lib/async-debugger.js#L9) :强制调用垃圾收集器

```
if (global.gc) {
    global.gc();
}
```

对`global.gc`的调用被包装在一个`if`语句中。正常情况下，`gc`函数不可用，而`if`语句允许异步调试器在正常情况下运行。为了公开`gc`函数，我们需要使用 Node.js 命令行参数`--expose-gc`。

试着像这样运行它:

```
node --expose-gc example-2.js
```

就像我说的，我找不到简单的方法来证明这一点。但是如果您正在调试一个更复杂的情况，您可能会发现您有未完成的异步操作可以通过强制垃圾收集来清理。

## 调试异步调试代码

如果您正在编写自己的异步调试库(或者对我的库进行升级)，您肯定会在某个时候需要调试您的调试代码。最简单的方法是使用控制台日志，但不幸的是，我们不能简单地使用`console.log`。

这个函数本身是异步的(困难 1)，我们不应该从调试代码中调用任何新的异步操作。这将导致更多的异步操作，并可能导致更多的混乱。

如果您仔细阅读调试器代码，您会发现我在多个地方使用了`fs.writeSync`(这里是[，例如](https://github.com/ashleydavis/debugging-async-operations-in-nodejs/blob/8deb9caff48bcfc82cab59f17b7f37ba60d548db/lib/async-debugger.js#L101))来生成调试输出。清单 8 展示了一个例子。

##### [清单 8](https://github.com/ashleydavis/debugging-async-operations-in-nodejs/blob/8deb9caff48bcfc82cab59f17b7f37ba60d548db/lib/async-debugger.js#L9) :强制调用垃圾收集器

```
fs.writeSync(1, `total #ops: ${this.getNumAsyncOps()}\n`);
```

允许我们同步写入一个文件。请注意，我们正在写入文件描述符 1。这指的是标准输出通道，所以它与使用`console.log`非常相似，但是它不是异步的。

## 结论

在这篇博文中，您已经了解了如何使用我的异步调试库来调试 Node.js 中的异步操作。

现在，您可以调试自己的异步代码，构建自己的调试库，或者升级我的调试库。

祝你成功解决异步问题！

## 资源

200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

## 部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

.

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.