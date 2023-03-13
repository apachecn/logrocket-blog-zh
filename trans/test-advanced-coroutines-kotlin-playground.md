# 用 Kotlin Playground 测试高级协同程序

> 原文：<https://blog.logrocket.com/test-advanced-coroutines-kotlin-playground/>

测试是开发人员生活中必不可少的一部分。经过测试的代码更容易维护，测试也经常作为文档。

如果你曾经和 Kotlin 一起工作过，你可能已经写了很多测试，但是你有没有试过和 Kotlin Playground 一起工作？

在本文中，我们将演示如何使用 Kotlin Playground 来测试您的代码。我们还将分享一些可以用来简化异步代码的高级协同程序概念。

*向前跳转:*

## 什么是科特林游乐场？

有时候我们只是想要一个快速的方法来测试一些东西，但是打开 Android Studio 或另一个代码编辑器需要一些时间。如果有一种方法可以快速检验你的想法会怎么样？这就是科特林游乐场的意义。

Playground 是一个能够运行 Kotlin 代码的编辑器，最重要的是，它可以在你的浏览器上运行。它是由 JetBrains 开发和维护的。要访问它，请前往 play.kotlinlang.org。

Kotlin Playground 附带了所有的标准 Kotlin 库(集合、反射、协程等)。)但不支持添加新库，这意味着您只能使用它来原型化或测试依赖于标准 Kotlin 库的东西。

Kotlin Playground 的一个很棒的特性是，它可以让你轻松地与他人分享你写的代码。只需复制页面 URL 并将其发送给其他人，或者使用“共享代码”按钮来获得代码的可嵌入版本。

科特林游乐场还有一个隐藏的特色。如果您按住 Ctrl (Windows)或 Command (Mac)键四处单击，将会创建更多光标。如果您需要同时编辑多行代码，这非常有用:

![Creating Multiple Cursors with Kotlin Playground](img/906c19e7c01c7dbff2005fcfd881baa6.png)

自 2022 年 5 月起，Playground 也支持移动设备，所以你甚至不需要电脑来运行 Kotlin 代码。还添加了一个动作工具栏，允许您选择 Kotlin 版本、选择编译器和设置程序参数。

## 游乐场入门

为了在操场上运行代码，我们需要创建一个 main 方法；否则，我们将得到一个“在项目中没有找到 main 方法”的错误。

对于本教程，我将使用带有 JVM 编译器的 Kotlin v1.7.21。请确保还将以下导入添加到文件的顶部:

```
import kotlin.test.*
import kotlinx.coroutines.*

```

这样做之后，你应该有一个类似这样的操场:

![Playground Example, Running Kotlin Code](img/4f00d7b5e69a29706b3942019e52c2e2.png)

如果您按下紫色的 run 按钮，代码应该会编译，您应该不会看到任何错误。

## 了解高级 Kotlin 协同程序

在我们深入测试之前，让我们回顾一些 [Kotlin 协程概念](https://blog.logrocket.com/understanding-kotlin-coroutines/)。

`CoroutineContext`是协程的重要组成部分，它定义了协程的行为方式，并具有四个公共元素:

*   `Job`:控制协程的生命周期；默认情况下，这个元素是一个`Job` ，但是您也可以指定一个`SupervisorJob`
*   `CoroutineDispatcher`:定义协程应该在哪个线程组中执行；大多数时候你会使用`Main`或`IO`调度程序
*   `CoroutineExceptionHandler`:定义未捕获的异常应该发生什么
*   `CoroutineName`:为协程定义一个名称(本文后面会详细介绍)

如果您使用过`withContext` scope 函数，您可能会对它的性能感到疑惑。如果您有一个很长的操作链，并且其中许多操作使用`withContext`来确保工作在正确的线程中执行，这难道不会增加很大的性能开销吗？

如果我们看一下它的[实现](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/common/src/Builders.common.kt#L144)，我们会看到有两条快速路径。第一个将当前上下文与新上下文进行比较。如果它们是相同的，就没有必要改变线程。第二条路径比较调度程序。如果它们是相同的，也没有必要改变线程。

因此，如果调度程序是相同的，我们不必担心线程，开销是最小的。

流动是价值流。如果您需要合并它们，有一个简洁的操作符可以实现，即`combine`操作符允许您根据需要合并任意多的流。下面是它最简单的签名:

```
fun <T1, T2, R> Flow<T1>.combine(flow: Flow<T2>, transform: suspend (T1, T2) -> R): Flow<R>

```

让我们假设你有`flowA`和`flowB`并且你想合并它们。为此，请使用以下代码:

```
flowA.combine(flowB) { a, b ->
    // combine them and emit a new value
}

```

或者，您可以使用上面代码的顶级版本:

```
combine(flowA, flowB) { a, b ->
    // combine them and emit a new value
}

```

## 测试高级协同程序

现在，让我们继续测试。我们将看看如何测试正常的`suspend`功能，如何切换/注入测试调度程序，以及如何测试`launch` / `async`、`Job` / `SupervisorJob`和流。

### 测试正常`suspend`功能

让我们从定义一个`suspend`函数开始，这样我们就有东西来编写我们的测试了。假设我们有一个函数返回某个特性是否被启用:

```
suspend fun isEnabled(): Boolean {
    delay(1_000)
    return true
}

```

现在，让我们在 main 函数中编写测试。`assertTrue`由`kotlin.test`提供，断言我们传入的变量是`true`；否则，它会引发异常:

```
fun main() {
    runBlocking {
        val result = isEnabled()
          assertTrue(result)
    }
}

```

如果我们运行这段代码，我们将不会得到任何输出，因为测试已经通过，但是如果我们将`isEnabled`改为返回`false`，我们将得到以下错误:

```
Exception in thread "main" java.lang.AssertionError: Expected value to be true.

```

如果您想向`assertTrue`添加自定义消息，请使用以下内容:

```
assertTrue(result, "result should be true but wasn't")

```

这将导致以下输出:

```
Exception in thread "DefaultDispatcher-worker-1 @coroutine#1" java.lang.AssertionError: result should be true but wasn't

```

### 切换/注入测试调度程序

在代码中硬编码调度程序不是好的做法。只要有可能，您应该接受 dispatcher 作为您的类中的一个参数。

看一下下面的代码:

```
class Database {    
    private val scope = CoroutineScope(Dispatchers.IO)

    fun saveToDisk() {
        scope.launch {
            ...
          }
    }
}

```

这个很难测试。为了使事情更简单，我们可以将它更改如下:

```
class Database(private val scope: CoroutineScope) {
    fun saveToDisk() {
        scope.launch {
            ...
          }
    }
}

```

这样，可以在测试期间注入范围。

### 测试`launch` / `async`

`launch`和`async`可能是`Compose`中使用最多的功能，尤其是对 Android 开发者而言。那么，我们如何测试它们呢？

让我们从定义一个保存一些状态的简单函数开始。您不能调用`suspend`函数，因为在您的活动或片段中没有作用域。但是，使用协程可以帮助您避免阻塞主线程，当您将内容保存到后端时，可能会发生阻塞:

```
private val scope = CoroutineScope(Dispatchers.IO)

fun saveState() {
    scope.launch {
        // save state to backend, disk, ...
    }
}

```

假设我们没有数据库或服务器，让我们创建一个变量来假装我们做了一些事情:

```
private val state: Any? = null

fun saveState() {
    scope.launch {
        state = "application state"
    }
}

```

现在我们有了可测试的东西，让我们编写测试:

```
fun main() {
    runBlocking {
        saveState()
          assertNotNull(state)
    }
}

```

这应该可以，对吧？如果我们运行这段代码，我们会得到一个错误，说`state`是`null`。这段代码的问题是它调用了`saveState`，但并没有等待它执行，所以我们在操作完成前检查结果。

要解决这个问题，我们可以简单地在检查`state`之前添加一个小延迟，就像这样:

```
fun main() {
    runBlocking {
        saveState()
          delay(100)
          assertNotNull(state)
    }
}

```

这样，`saveState`就有时间在我们检查变量之前执行。然而，使用`delay`来测试您的代码并不是最佳实践。为什么正好是 100 毫秒而不是 200 毫秒？如果代码执行时间超过 100ms 怎么办？这就是为什么我们应该避免这种做法。在本文的稍后部分，我将向您展示一种更好的测试方法。

测试`async`也是类似的过程；让我们修改`saveState`，使其使用`async`:

```
fun saveState() {
    scope.launch {
        async { state = "application state" }.await()
    }
}

fun main() {
    runBlocking {
        saveState()
      delay(100)
          assertNotNull(state)
    }
}

```

现在，运行这段代码；你会看到它像预期的那样工作。

### 测试`Job` / `SupervisorJob`

接下来，我们来探讨一下如何测试`Jobs`。如果你使用`GlobalScope`，测试你的代码是非常困难的，因为你不能替换或模仿它。此外，因为你不能取消`GlobalScope`，如果你使用它，你基本上失去了对工作的控制。相反，我们将为我们的测试定义一个定制的范围，如果需要，我们可以控制这个范围:

```
private val scope = CoroutineScope(Dispatchers.Default)

```

我们可以定义一个变量来跟踪`Job`并且修改`saveState`来将`launch`的结果赋给这个变量:

```
private var job: Job? = null
private var state: Any? = null

fun saveState() {
    job = scope.launch {
        println("application state")
    }
}

```

现在，在主函数中，我们可以测试`saveState`函数:

```
fun main() {
    runBlocking {
        saveState()
          job?.join()
          assertNotNull(state)
    }
}

```

运行这个，你应该不会得到任何错误。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

你可能会问，“为什么我们需要使用`join`？”嗯，调用`launch`不会阻塞代码执行，所以我们需要使用`join`来阻止主函数退出。

现在我们知道了如何测试一个`Job`，让我们学习如何测试一个`SupervisorJob`。

一个`SupervisorJob`类似于一个`Job`，除了它的子节点可以彼此独立地失败。让我们从改变我们的工作范围开始，让我们有一个主管的工作:

```
val scope = CoroutineScope(SupervisorJob())

```

现在，在我们的主函数中，让我们添加另一个`launch`来抛出一个错误:

```
fun main() {
    runBlocking {
        scope.launch { throw error("launch1") }.join()
      saveState().join()
          assertNotNull(state)
    }
}

```

运行这个命令，您会在输出中看到一个错误。嗯，难道不应该防止这种情况发生吗？如果我们更详细地分析它，我们会看到它确实防止了错误冒泡，但并没有阻止它被记录。如果您在断言下面添加一个`println`语句，您会看到它实际上被打印出来了；因此，即使第一个`launch`出错，第二个也能运行。

### 测试流程

为了测试流，我们将从添加一个新的导入开始:

```
import kotlinx.coroutines.flow.*

```

接下来，让我们创建一个名为`observeData`的函数，它只返回一个流:

```
fun observeData(): Flow<String> {
    return flowOf("a", "b", "c")
}

```

现在，在我们的 main 方法中，我们可以使用 assertEquals 函数来比较期望值和实际值:

```
suspend fun main() = runBlocking<Unit> {
    val expected = listOf("a", "b", "c")
    assertEquals(expected, observeData().toList())
}

```

## 改进测试的策略

现在，我们对如何测试高级协程有了更好的理解，让我们来看看一些使协程测试和调试更容易的策略。

### 命名您的`CoroutineScope`

如果您有许多协同程序作用域，调试它们可能会很困难，因为它们都使用类似于@协同程序#1、@协同程序#2 等的命名约定。

为了使调试更容易，我们可以将`CoroutineName(...)`添加到`CoroutineScope`中，就像这样:

```
private val scope = CoroutineScope(Dispatchers.Default + CoroutineName("database"))

```

如果在该范围内出现故障，我们将得到如下错误:

```
Exception in thread "DefaultDispatcher-worker-1 @database#2" java.lang.IllegalStateException: …

```

### 遵循协程最佳实践

为了使测试更容易，请遵循以下协程最佳实践:

*   在类中注入调度程序:避免在类中硬编码调度程序。通过允许您替换它们，注入它简化了测试
*   避免:它让测试变得非常困难，这本身就是一个避免测试的好理由。这也使得控制作业的生命周期变得更加困难

## 结论

在本文中，我们探索了如何使用 Kotlin Playground 测试协程。我们还看了一些协程概念，比如`CoroutineContext`和`Flow`。最后，我们讨论了一些使测试更容易的策略。

现在轮到你了；学习的最好方法是通过练习。下次见！

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)