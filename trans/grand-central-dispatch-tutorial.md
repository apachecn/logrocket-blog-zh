# 大中央调度教程-日志火箭博客

> 原文：<https://blog.logrocket.com/grand-central-dispatch-tutorial/>

由苹果公司于 2009 年发布的 Grand Central Dispatch 旨在管理并发运行的任务，将它们排队等待执行，并在后台调度它们在未被占用的处理器上执行。

在 Apache 许可下发布，GCD 包含一个开源库，通常称为 GCD 或 libdispatch。这个库在后台执行繁重的任务操作，保持主线程平稳运行，并提供更快的响应时间。

GCD 将任务作为一个不接受参数的空块或函数来处理。

我们将讨论 GCD 如何提供多线程和队列，以及探索 GCD 用它的技术和特性解决了什么问题。

## 大中央调度中的线程、多线程和队列

为了真正理解 GCD，我们将回顾线程、多线程和队列。

### 线

GCD 中的线程包含主线程和后台线程；所有任务都在这些线程上执行。保持主线程尽可能自由是很重要的，这样我们的用户界面才能保持快速响应。任何繁重的任务都必须推送到后台线程。

### 多线程操作

因为 CPU 一次执行一个任务，所以我们可以使用多线程让 CPU 在任务之间切换，允许它同时执行多个任务。

多线程提高了响应速度，减少了执行多个任务时的延迟，确保主线程不会中断。

### 长队

队列类似于公共汽车线路。想象人们在公共汽车站等着上车。队列中的第一个代表队列，进入总线并离开队列，这在编程中称为先进先出。

在 GCD 中，[队列是一组等待在线程上执行的代码块或函数](https://blog.logrocket.com/know-your-javascript-data-structures/#Queue)。

有两种类型的队列:

1.  一种串行队列，从头到尾执行任务，一次执行一个任务
2.  并发队列同时执行所有任务；任务根据正在执行的操作完成

相对于线程，并发队列在后台线程上工作，而主线程是串行队列。应用程序使用主队列串行执行任务，并将繁重的任务分派给并发队列。这就是中央调度中心的用武之地。

## 大中央调度解决什么？

如果不能在后台线程上执行繁重的任务，任务将会连续执行，从而降低性能。

但是通过创建队列并将代码块放入队列，GCD 将长而重的任务交给后台线程执行。队列中等待执行的代码块保存在闭包中。

## GCD 技术

GCD 使用的技术为执行并发代码提供了全面的支持。

我们将回顾一些对 iOS 开发者有用的 GCD 技术，包括:

*   如何用`DispatchQueue`安排和管理任务
*   使用`DispatchGroup`将任务作为一个单元进行分组和同步

### `DispatchQueue`

这就是 GCD 创建任务的地方，它将任务打包到一个块或函数中，并按照执行顺序或复杂度将其放入队列中。这使我们的任务队列保持有序，并有助于串行或并发执行它们。

`DispatchQueue`有三种类型:

*   主队列是串行的，在主线程上运行，用于与 UI 相关的操作
*   全局队列是并发队列，按照优先级顺序执行任务
*   自定义队列是自定义的串行和并发队列

用以下内容创建一个`DispatchQueue`:

```
let dispatchQueue = DispatchQueue(label: "myqueue")

```

然后，我们可以通过添加以下代码来同步或异步执行任务:

```
let dispatchQueue = DispatchQueue(label: "myqueue")

// Code executes synchronously
dispatchQueue.sync {
    print("Do something synchronous")
}

// Code executes asynchronously
dispatchQueue.async {
    print("Do something asynchronous")
}

```

如果我们只想同步执行我们的任务，我们添加以下内容:

```
let dispatchQueue = DispatchQueue(label: "myqueue")

// Task is synchronous
dispatchQueue.sync {
    // Set timeinterval
    Thread.sleep(forTimeInterval: 5)
    print("Do something synchronous")
}

print("Do something")

// Do something synchronous
// Do something

```

注意`print("Do something")`必须等待第一个任务完成。在这种情况下，第一个任务延迟 5s，执行，并继续下一个代码。

如果我们需要任务异步运行，`DispatchQueue`仍然可以帮助我们。异步运行任务时，它们在自己的时间内完成，而主线程仍然同步执行:

```
let dispatchQueue = DispatchQueue(label: "myqueue")

dispatchQueue.async {
    Thread.sleep(forTimeInterval: 1)
    print("Do something asynchronous")
}

print("Do something")

// Do something
// Do something asynchronous

```

因为我们为`"Do something asynchronous"`运行`dispatchQueue.async`，`"Do something"`首先返回。

### `DispatchGroup`

`DispatchGroup`的主要目的是等待数据。任务完成后，他们会等待整个组完成，然后再进行下一个操作。因此，当我们有多组并发任务时，一个`DispatchGroup`会在任务完成时通知我们。

当我们创建一个`DispatchGroup`时，我们可以创建一个定制的并发和串行队列，它们的异步任务链接到同一个组。

如果我们想在每个组中的任务完成时得到通知，我们可以使用`group.notify`，它来自我们之前声明的`DispatchGroup`:

```
let group = DispatchGroup()

// Concurrent queue
let queue = DispatchQueue(label: "com.logrocket.concurrentqueue", attributes: .concurrent)

// Link the Queue to Group
queue.async(group: group) {  
    Thread.sleep(forTimeInterval: 3)
    print("My first task")
}

// Serial queue
let queue2 = DispatchQueue(label: "com.logrocket.serialqueue", attributes: .serial)

// Link the Queue to Group
queue2.async(group: group) {  // Group Linked
    print("My second task")
}

// Notify us for completion of tasks on main thread.
group.notify(queue: DispatchQueue.main) {
    Thread.sleep(forTimeInterval: 1)
    print("All Concurrent task done")
}

print("Waiting for tasks")

/* Output:
  My Task
  Concurrent task done
  Waiting for tasks
*/

```

这类似于信号量，但是在我们的例子中，如果我们的任务不接触共享资源，我们必须使用`DispatchGroup`。

## 结论

有了 GCD，我们可以将管理线程的责任从主应用程序转移到操作系统。实现并发执行模式不再是开发人员必须负责的事情。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)