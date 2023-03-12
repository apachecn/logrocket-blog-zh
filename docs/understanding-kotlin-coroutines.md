# 了解 Kotlin 协同程序

> 原文：<https://blog.logrocket.com/understanding-kotlin-coroutines/>

在您的程序员生涯中，您可能已经处理过一些常见的问题，比如线程饥饿、回调地狱以及线程被阻塞的时间过长。事实是，使用线程并不容易，尤其是如果您的系统是以异步方式的例程为目标的话。

许多语言已经开发了异步编码的简化——比如 Go 的 [Goroutines](https://tour.golang.org/concurrency/1) ,它们基本上是由 Go 运行时管理的轻量级线程。Closure 通过其用于异步编程的 [core.async](https://github.com/clojure/core.async) 工具，Node.js 通过臭名昭著的事件循环，现在 Kotlin 通过[协程](https://kotlinlang.org/docs/coroutines-guide.html)提供了类似的特性。

在本文中，我们将探索 Kotlin 协程的新兴领域，试图展示它们如何简化该语言中的异步编程。

## Kotlin 协同程序的独特之处是什么？

Kotlin 没有其他语言拥有的默认异步功能，比如 JavaScript 中内置的用于`async`和`await`的保留字。相反，JetBrains 在 [kotlinx-coroutines](https://github.com/Kotlin/kotlinx.coroutines) 库下发布了一组新的协同程序，其中有几个高级协同程序用于各种任务，如`launch`和`async`等。

看看下面这个摘自 JetBrains 提供的[游乐场](https://play.kotlinlang.org/)环境的例子:

```
suspend fun main() = coroutineScope {
    launch { 
       delay(1000)
       println("Kotlin Coroutines World!") 
    }
    println("Hello")
}

```

你认为哪一行将被首先打印？如果你的回答是“你好”，那你就对了这是因为我们将`launch`块延迟了一秒，而第二次打印没有延迟。

本质上，协程只不过是一个简单的轻量级线程。就像我们以前使用 Java 一样，它们需要显式启动，这可以通过`coroutineScope`环境下的`launch`协程构建器来完成(例如，在全局范围内，协程与应用程序的寿命一样长)。

`coroutineScope`构建器创建一个协程作用域，它在执行自己的完成之前等待所有子协程完成。

对于那些希望将不同的协程归入一个更全球化的协程的人来说，这是一个很好的特性。这是一个与`[runBlocking](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/run-blocking.html)`非常相似的概念，它阻塞当前线程等待`coroutineScope`带来的暂停模式。

在上面的例子中，`delay`函数使用了`Thread`作用域，可以替换为:

```
launch { 
    Thread.sleep(1000)
    println("Kotlin Coroutines World!") 
}

```

反过来，`launch`函数可以被等价函数`Thread`代替。

在示例中修改它时要小心，因为`delay`函数，也是`suspend`函数，只能从协程或另一个`suspend`函数调用。

基于这些术语，我们的代码示例将迁移到以下内容:

```
import kotlinx.coroutines.*
import kotlin.concurrent.thread

suspend fun main() = coroutineScope {
    thread { 
       Thread.sleep(1000)
       println("Kotlin Coroutines World!") 
    }
    println("Hello")
}

```

协程的一个很大的优点是，它们可以在线程中挂起它们的执行，它们可以在线程中运行任意多次。这意味着我们节省了大量资源，因为等待执行完成的无限停止线程不再是经验法则。

但是，如果您想等待特定的协程完成，也可以这样做:

```
val job = GlobalScope.launch {
    delay(1000L)
    println("Coroutines!")
}
println("Hello,")
job.join()

```

我们在这里创建的引用被称为后台[作业](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/index.html)，它是一个可取消的任务，其生命周期以其完成而告终。`join`函数一直等到协程完成。

在您希望对某些协程完成的同步状态有更多控制的情况下，这是一个非常有用的概念。但是科特林是怎么做到的呢？

## 连续传球风格

CPS(延续传递风格)是一种编程类型，它允许控制流以延续的形式显式传递，即作为计算机程序流的控制状态的抽象表示。这非常类似于 JavaScript 中著名的回调函数。

为了更好地理解它，我们来看看`[Continuation](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/-continuation/)`界面:

```
interface Continuation<in T> {
    val context: CoroutineContext
    fun resume(value: T)
    fun resumeWith(result: Result<T>)
    fun resumeWithException(exception: Throwable)
}

```

它表示返回类型为`T`的值的暂停点之后的延续。其主要目标和功能包括:

*   `context`:链接到该延续的上下文
*   `resumeXXX`:恢复执行相应协程的函数，结果不同

太好了！现在，让我们来看一个更实际的例子。假设您正在处理一个普通的函数，它通过一个挂起函数从数据库中检索信息:

```
suspend fun slowQueryById(id: Int): Data {
  delay(1000)

  return Data(id = id, ... )
}

```

假设这里的`delay`函数模拟了您为获得数据结果而必须运行的缓慢查询。

在幕后，Kotlin 通过另一个被称为[状态机](https://github.com/Kotlin/kotlin-coroutines/blob/master/kotlin-coroutines-informal.md#state-machines)的概念将协程转换成一种回调函数，而不是创建许多新函数。

## 取消与超时

我们已经学习了如何创建后台作业，以及如何等待它们完成。我们还看到，这些作业是可取消的结构，这意味着如果您对它们的结果不再感兴趣，您可能会想要取消它们，而不是等待它们完成。

在这种情况下，只需调用`cancel`函数:

```
job.cancel()

```

但是，有时您希望在取消某些操作或等待它们完成之前，为它们设置一个限制。这就是暂停变得方便的地方。

如果一个给定的操作花费的时间比它应该花费的时间长，那么`timeout`配置将确保抛出一个适当的异常，以便您做出相应的反应:

```
runBlocking {
    withTimeout(2000L) {
        repeat(100) {
            delay(500L)
        }
    }
}

```

如果操作超过了我们设置的两秒钟的时间限制，就会抛出一个`CancellationException`错误。

通过`withTimeoutOrNull`模块可以实现另一种版本。让我们看一个例子:

```
import kotlinx.coroutines.*

suspend fun main() = runBlocking<Unit> {
    withTimeoutOrNull(350) {
        for (i in 1..5) {
            delay(100)          
            println("Current number: $i")
        }
    }
}

```

这里，只打印数字 1 到 3，因为超时设置为 350 毫秒。我们每次迭代都有 100ms 的延迟，只够填充我们`for`的三个值。

这对于您不希望抛出异常的场景也很好。

## 进行异步

如果您以前使用过 JavaScript，您可能习惯于创建`async`函数，并确保在同步块中预期结果时使用`await`函数。

Kotlin 允许我们通过`async`协程做同样的事情。假设您想要启动两个不同的硬处理线程，并等待两个结果都返回到主线程。下面的例子展示了 Kotlin 如何利用 Java 的特性，比如`[Future](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Future.html)`:

```
val thread1 = async(CommonPool) { 
    // hard processing 1
}
val thread2 = async(CommonPool) { 
    // hard processing 2
}

runBlocking {
    thread1.await()
    thread2.await()
}

```

`async`函数创建一个新的协程，并返回其未来结果作为`Deferred`的实现。当结果`Deferred`被取消时，正在运行的协程也被取消。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

反过来，`Deferred`是一个不阻塞的可取消的未来——即，它是一个有结果的`Job`。

当两个硬处理协同程序启动时，主协同程序通过`runBlocking`执行调用被挂起，只有在两个线程结果可用后才会被恢复。这样，我们可以提高性能，因为两个协程将并行执行。

## 为异步数据流构建流

Kotlin 还为我们提供了一个处理异步数据流的好方法。有时，您需要您的流发出值，通过一些外部异步函数转换它们，收集结果，并成功地完成流或出现异常。

如果是这样的话，我们可以利用`[Flow<T>](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/index.html)`类型。让我们以下面的例子为例，它迭代一个数字序列并打印它的每个值:

```
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*

suspend fun main() = runBlocking<Unit> {
    (1..3).asFlow().collect { value -> println("Current number: $value") }
}

```

如果您习惯于使用 Java Streams API 或其他语言的类似版本，那么这段代码可能对您来说非常熟悉。

Kotlin 还为`map`和`filter`操作提供了辅助函数，即使它们可能有长时间运行的异步调用:

```
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*

suspend fun main() = runBlocking<Unit> {
    (1..5).asFlow()
        .filter{ number -> number % 2 == 0 } // only even numbers
        .map{ number -> convertToStr(number) } // converts to string
        .collect { value -> println(value) }
}

suspend fun convertToStr(request: Int): String {
    delay(1000)
    return "Current number: $request"
}

```

## 结论

很高兴看到 Kotlin 朝着创建一个更加异步和无阻塞的世界又迈进了一步。尽管 Kotlin 协程相对较新，但它们已经利用了其他语言长期以来从这种范式中汲取的巨大潜力。

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)