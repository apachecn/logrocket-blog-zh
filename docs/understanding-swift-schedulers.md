# 了解 Swift 调度程序

> 原文：<https://blog.logrocket.com/understanding-swift-schedulers/>

iOS 应用程序开发中最常见的错误之一是线程错误，当开发人员试图从闭包更新用户界面时会出现这种错误。要解决这个问题，我们可以用`DispatchQueue.main`和`threads`。

在本教程中，我们将了解什么是调度器，以及我们如何在 iOS 应用程序开发中使用它们来管理队列和循环。必须具备 Swift、Combine 框架和 iOS 开发方面的知识。

我们开始吧！

## 什么是调度程序？

根据[调度器文档](https://developer.apple.com/documentation/combine/scheduler)，调度器是“定义何时何地执行闭包的协议”本质上，调度程序为开发人员提供了一种以特定方式执行代码的方法，有助于在应用程序中运行队列命令。

开发人员可以通过使用调度程序将高容量操作迁移到辅助队列，释放应用程序主队列上的空间并更新应用程序的 UI。

调度器还可以优化并行执行命令的代码，允许开发人员同时执行更多命令。如果代码是串行的，开发人员可以一次执行一位代码。

## 调度程序的类型

有几种类型的调度器内置在[组合](https://developer.apple.com/documentation/combine)中。需要注意的是，调度程序遵循调度程序协议，该协议可以在上面链接的调度程序文档中找到。

我们来看看几个比较受欢迎的调度器！

### `OperationQueue`

根据它的文档，an [`OperationQueue`](https://developer.apple.com/documentation/foundation/operationqueue) 根据命令的优先级和就绪性来执行命令。一旦将操作添加到队列中，该操作将一直保留在队列中，直到它执行完命令。

一个`OperationQueue`可以以串行或并行的方式执行任务，这取决于任务本身。`OperationQueue`主要用于后台任务，比如更新应用程序的 UI。

### `DispatchQueue`

苹果的文档将`[DispatchQueue](https://developer.apple.com/documentation/dispatch/dispatchqueue#:~:text=Dispatch%20queues%20are%20FIFO%20queues,tasks%20either%20serially%20or%20concurrently.&text=When%20you%20schedule%20a%20work%20item%20asynchronously%2C%20your%20code%20continues,the%20work%20item%20runs%20elsewhere.)`定义为先进先出队列，可以以块对象的形式接受任务，并串行或并发地执行它们。

系统管理提交给线程池中的`DispatchQueue`的工作。除非`DispatchQueue`代表一个应用程序的主线程，否则`DispatchQueue`不会保证它将使用哪个线程来执行任务。

`DispatchQueue`经常被认为是调度命令最安全的方式之一。但是不建议在 Xcode 11 中使用一个 [`DispatchQueue`。如果在 Xcode 11 中使用`DispatchQueue`作为调度程序，它必须是串行的，以遵守联合收割机运营商的合同。](https://forums.swift.org/t/runloop-main-or-dispatchqueue-main-when-using-combine-scheduler/26635/4)

### `ImmediateScheduler`

一个`[ImmediateScheduler](https://developer.apple.com/documentation/combine/immediatescheduler)`用于立即执行异步操作:

```
import Combine

let immediateScheduler = ImmediateScheduler.shared

  let aNum = [1, 2, 3].publisher
        .receive(on: immediateScheduler)
        .sink(receiveValue: {
       print("Received \$0) on thread \(Threa.currentT")t
})

```

例如，上面的代码块将发送类似于下面代码块的输出:

```
Received 1 on thread <NSThread: 0x400005c480>{number = 1, name = main}
Received 2 on thread <NSThread: 0x400005c480>{number = 1, name = main}
Received 3 on thread <NSThread: 0x400005c480>{number = 1, name = main}

```

在应用程序的当前线程上立即执行命令。上面的代码片段运行在主线程上。

### `RunLoop`

[`RunLoop`调度器](https://developer.apple.com/documentation/foundation/runloop)用于在特定的运行循环中执行任务。运行循环上的动作可能不安全，因为`RunLoops`不是线程安全的。因此，使用`DispatchQueue`是一个更好的选择。

## 默认计划程序

如果没有为任务指定调度程序，Combine 会为它提供一个默认调度程序。提供的调度程序将使用执行任务的同一线程。例如，如果您执行一个 UI 任务，Combine 会提供一个调度器，在同一个 UI 线程上接收任务。

## 切换调度程序

在使用 Combine 的 iOS 开发中，许多消耗资源的任务都在后台完成，防止应用程序的 UI 完全冻结或崩溃。Combine 然后切换调度器，使任务的结果在主线程上执行。

Combine 使用两种内置方法来切换调度程序:`receive(on)`和`subscribe(on)`。

### `receive(on)`

`receive(on)`方法用于在特定的调度程序上发出值。它为声明后出现的任何发布者更改调度程序，如下面的代码块所示:

```
Just(3)
   .map { _ in print(Thread.isMainThread) }
   .receive(on: DispatchQueue.global())
   .map { print(Thread.isMainThread) }
   .sink { print(Thread.isMainThread) }

```

上面的代码块将打印以下结果:

```
true 
false 
false 

```

### `subscribe(on)`

`subscribe(on)`方法用于在特定的调度程序上创建订阅:

```
import Combine 
print("Current thread \(Thread.current)")
let k = [a, b, c, d, e].publisher
    .subscribe(on: aQueue)
    .sick(receiveValue: {
        print(" got \($0) on thread \(Thread.current)")
  })

```

上面的代码块将打印以下结果:

```
Current thread <NSThread: 0x400005c480>{number = 1, name = main}
Received a on thread <NSThread: 0x400005c480>{number = 7, name = null}
Received b on thread <NSThread: 0x400005c480>{number = 7, name = null}
Received c on thread <NSThread: 0x400005c480>{number = 7, name = null}
Received d on thread <NSThread: 0x400005c480>{number = 7, name = null}
Received e on thread <NSThread: 0x400005c480>{number = 7, name = null}

```

在上面的代码块中，这些值是从另一个线程而不是主线程发出的。从执行指令的顺序来看，`subscribe(on)`方法连续执行任务。

### 使用调度程序执行异步任务

在这一节中，我们将学习如何在`subscribe(on)`和`receive(on)`调度器方法之间切换。假设发布者正在后台运行一项任务:

```
struct BackgroundPublisher: Publisher
  typealias Output = Int
  typealias Failure = Never 

  func receive<K>(subscriber: K) where K : Subcriber, Failure == K.Failure, Output == K.Input {
  sleep(12) 
  subscriber. receive(subscriptiton: Subscriptions.empty)
  _= subscriber.receive(3)
  subscriber.receive(completion: finished)
}

```

如果我们从用户界面线程调用任务，我们的应用程序将冻结 12 秒。Combine 将向执行我们的任务的同一个调度器添加一个默认调度器:

```
BackgroundPublisher()
    .sink { _ in print("value received") }

print("Hi!")

```

在上面的代码块中，收到值后，`Hi!`将被打印在我们的控制台中。我们可以看到下面的结果:

```
value received
Hi!

```

在 Combine 中，这种类型的异步工作通常通过在后台调度程序上订阅并在 UI 调度程序上接收事件来执行:

```
BackgroundPublisher()
    .subscribe(on: DispatchQueue.global())
    .receive(on: DispatchQueue.main)
    .sink { _ in print("Value recieved") }

print("Hi Again!")

```

上面的代码片段将打印以下结果:

```
Hi Again!
Value received

```

在收到值之前打印`Hi Again!`。现在，发布者不会通过阻塞我们的主线程来冻结我们的应用程序。

## 结论

在这篇文章中，我们回顾了什么是调度器，以及它们如何在 iOS 应用程序中工作。我们涵盖了`OperationQueue`、`DispatchQueue`、`ImmediateScheduler`和`RunLoop`的一些最佳用例。我们还讨论了 Combine 框架以及它如何影响 Swift 中调度程序的使用。

我们学习了如何使用`receive(on)`和`subscribe(on)`方法在 Swift 中切换调度程序。我们还学习了如何在 Combine 中使用调度程序执行异步功能，方法是订阅后台调度程序并在用户界面调度程序上接收我们的值。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)