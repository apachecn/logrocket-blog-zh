# 比较 Kotlin 挂起和运行阻塞函数

> 原文：<https://blog.logrocket.com/kotlin-suspend-runblocking-functions/>

Kotlin 编程语言进入 Android 生态系统引起了很多不满，并永远改变了经验丰富和新手程序员开发 Android 的方式。在新的函数式编程范例和大量可供尝试的新 API 之间，Kotlin 将永远改变我们使用 Android 的方式。

最值得注意的是协程的引入，它重新定义了我们处理并发性、异步性和多线程的方法。正如 [Kotlin 的官方文档所解释的那样](https://kotlinlang.org/docs/coroutines-basics.html)，协同程序赋予程序员启动轻量级线程的能力，这些线程可以暂停任何给定线程的代码块的执行。

这种新方法允许程序员创建与 UI 线程异步运行的协同程序。它们可以阻塞主线程或另一个线程，也可以同时并行运行。协程甚至可以在一个线程中启动，在另一个线程中返回。

简而言之，协程赋予开发人员实现多线程策略的能力，这种策略不需要产生新线程的昂贵事务，而是处理多个协程，这些协程可以从任何线程启动并从另一个线程恢复，同时使用更少量的工作线程。

关于 Kotlin 的协程，我们可以探索更多的信息，但是在本文中，我将把注意力集中在这种模式的两个主要方面:`suspend`和`runBlocking`关键字之间的区别，以及这两个概念如何与协程模式相关联。

向前跳:

> *kot Lin 协程中几乎所有的东西都围绕着`suspend`函数。——[德尔夫斯塔克](https://www.delftstack.com/howto/kotlin/suspended-function-kotlin/)*

## 科特林悬浮函数

让我们从定义`suspend`和`runBlocking`这两个关键字的确切含义开始。

`suspend`关键字是一个函数修饰符，它表示给定的函数被做成一个暂停函数*。*根据 [Kotlin 的官方文档](https://kotlinlang.org/docs/coroutines-basics.html#extract-function-refactoring)，挂起函数可以像常规函数一样在协程内部使用，但是它们有一个额外的特性，允许它们在协程内部使用其他挂起函数:

```
suspend fun firstFunction() {
    delay(1000L) // delay is also a suspending function
    println("This is a suspend function")
}

```

值得注意的是`suspend`函数不能像其他函数一样执行。正如 [DelftStack 解释](https://www.delftstack.com/howto/kotlin/suspended-function-kotlin/)的那样，挂起函数需要通过另一个`suspend`函数调用，或者从协程本身调用。这意味着任何给定的程序都必须构建一个`coroutineScope`，以便开始执行所有的挂起函数:

```
suspend fun secondFunction() = coroutineScope { // this: CoroutineScope
    launch { // launch is a suspending function
        delay(500L)
        print("LogRocket!")
    }
    print("Hello")
}

```

## 锅碗瓢盆

在我们像上面承诺的那样研究`runBlocking`函数之前，我们首先需要走一个小弯路，快速定义什么是`coroutineScope`。

一个`coroutineScope`允许任何给定的线程跳到协程世界，并与程序的其余部分同时执行代码。正如我们上面建议的，一个`coroutineScope`允许启动和执行进一步的协程或挂起功能。

此外，正如 [Kotlin 的官方文档](https://kotlinlang.org/docs/coroutines-basics.html#scope-builder)所解释的，当一个`coroutineScope`被创建时，直到所有被启动的子进程完成，它才是完整的。上面的`coroutineScope`函数也是一个暂停函数，就像任何一个`suspend`函数一样。

不幸的是，`coroutineScope`的程度和性质不是本文的主题。关于`coroutineScope`及其不同用法的更多信息，请查看我的同事[关于协同程序世界的文章](https://blog.logrocket.com/understanding-kotlin-coroutines/)。

## 阻塞`coroutineScope`

回到当前的主题——`runBlocking`函数是一个 couroutine 构建器，或者像 [Kotlin 的网站](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/run-blocking.html)更好地解释的那样，它在常规函数的非协同世界和代码块的花括号内的协同世界之间架起了一座桥梁:

```
fun main() = runBlocking { // this: CoroutineScope
    println("We're inside a blocking CoroutineScope")
}

```

您会注意到，最后一个定义听起来可能类似于为`coroutineScope`给出的定义。这是因为`runBlocking`函数在其内部创建了一个`coroutineScope`，就像上面的暂停函数`coroutineScope`一样。类似地，他们都在完成自己之前等待他们所有的孩子完成。

常规`coroutineScope`和`runBlocking`函数的主要区别有两个。首先，`coroutineScope`是一个暂停函数，`runBlocking`不是，相反，它只是一个常规函数。这种差异是由于这样一个事实，即`runBlocking`阻止调用线程进一步执行，直到它完成，而另一种方法只是将执行暂停到一个工作线程中:

```
fun main() {
    runBlocking { // this: CoroutineScope
        launch {
            delay(500L)
            println("the")
        }   
        println("Follow")
    }
    println("execution.")
}

```

如上面的例子所示，`runBlocking`函数实现的是创建一个`coroutineScope`,从这里执行进一步的协程，同时阻塞调用线程。从那里，它启动另一个`suspend`函数，当初始作用域继续执行并打印初始字符串`"Follow"`时，该函数会自我延迟。一旦第二个协程中的延迟完成，它将打印第二个字符串`"the"`，随后是阻塞范围的完成，并通过打印最后一个字符串`"execution"`来结束程序。

## `suspend`和`runBlocking`功能是如何关联的？

至此，我们已经浏览了大量信息。我们了解到`suspend`函数并不比常规函数多多少，除了它们需要被一个协程或者另一个`suspend`函数自己调用。我们还了解了什么是`coroutineScope`，并深入研究了一个`coroutineScope`的例子，它阻止了调用线程的进一步执行。

但是这一切有什么联系呢？让我们将这两个主要观点放在同一个程序中，进一步探讨它们之间的关系:

```
fun main() { 
    runBlocking { // this: CoroutineScope
    suspendingWork()
        println("the")
    }
    println("execution.")
}

suspend fun suspendingWork() {
    delay(1000L)
    println("Follow")
}

```

```
**Output:**
> Follow
> the
> execution.

```

在上面的例子中，你会注意到我们在同一个代码片段中使用了之前探索过的所有思想。函数`main()`由一个`runBlocking`作用域组成，该作用域阻塞线程，直到它完成执行，随后是一个简单的 print 语句。

按照执行流程，我们从进入`runBlocking()`范围块开始，同时阻止线程进一步执行。从那里，用`suspendingWork()`调用一个暂停函数，阻塞范围依次跟随它，直到它用程序的第一个打印语句`"Follow"`结束执行。程序一回到阻塞的`coroutineScope`块，就打印第二条语句`"the"`。最后，阻塞`coroutineScope`释放线程，并最终允许最后一个打印语句在程序的最后读取`"execution"`。

这是一个口，但希望每个人都能跟上。至少，执行的流程应该通过跟随试图用简单英语形成的句子来记录。

在下一节中，我将提供更多的例子以及它们的输出，以进一步展示在`runBlocking`范围内运行的`suspend`函数和代码在打印语句的执行流程方面有什么不同。

## `suspend`和`runBlocking`功能的更多示例

我们通过充实一个示例结束了上一节，该示例演示了一个`suspend`函数相对于`runBlocking`函数的关系，以及它们在彼此周围的行为方式。在这一节中，我们将探索更多的例子来进一步强调这两个概念的属性和行为，并更好地理解如何以及何时使用它们。

### 示例 1

对于第一个例子，我们将从上一节结束时的例子开始，但是稍微做了一点修改，展示了对`suspendingWork()`函数的调用在`launch` suspend 函数中被挂起。

应该注意的是，这一微小的差异极大地改变了操作的顺序，因为`suspendingWork()`中的代码块被挂起到它自己的协程中，并在它继续执行之前延迟给定的协程一秒钟。此时，阻塞范围内的初始 print 语句已经被执行了，因为挂起的工作允许该范围继续执行。最后，执行阻塞范围之外的 print 语句，就像前面的例子一样:

```
fun main() { 
    runBlocking { // this: CoroutineScope
        launch {
            suspendingWork()
        }
        println("Follow")
    }
    println("execution.")
}

suspend fun suspendingWork() {
    delay(1000L)
    println("the")
}

```

```
**Output:**
> Follow
> the
> execution.

```

### 示例 2

对于第二个例子，我们探索了从同一个阻塞范围同时启动同一个`suspendingWork()`函数的两个不同实例的想法，后面跟着一个简单的 print 语句，读为`"Hello"`。

这个例子中的输出非常明显，但是值得注意的是，每当两个不同的`suspend`函数像这样同时启动时，大多数情况下它们将按照创建时的顺序返回。对这个例子的一个简单修改，我们对单个`suspend`函数的调用进行编号，将在编译器中展示这一点:

```
fun main() = runBlocking { // this: CoroutineScope
    launch {
        suspendingWork()
  }
  launch {
    suspendingWork()
  }
    println("Hello")
}

suspend fun suspendingWork() {
    delay(1000L)
    println("LogRocket!")
}

```

```
Output:
> Hello
> LogRocket!
> LogRocket!

```

### 示例 3

这个例子有点复杂。我们又回到了跟踪执行的过程中，新的`suspend`函数只是打印它接收到的参数。这一次，我们将函数端的两个不同版本挂起到它们自己的协程中，同时用我们的第一个 print 语句在阻塞范围内调用同一个函数。`delay`调用应该给出哪个挂起的工作首先被打印，像往常一样，在阻塞范围之外的打印语句被打印在最后:

```
fun main() {
    runBlocking { // this: CoroutineScope
        launch {
            delay(500L)
            supendedPrint("printing")
        }
        launch {
            supendedPrint("the")
        }
        supendedPrint("Follow")
    }
    println("execution.")
}

suspend fun supendedPrint(value: String) {
    println(value)
}

```

```
Output:
> Follow
> the
> printing
> execution.

```

### 实例 4

对于这个例子，让我们回到一个老朋友。`suspend`函数现在由一个自己的`coroutineScope`组成，这意味着它可以随意在其中生成更多的协程，就像`runBlocking`函数一样。

这个例子的目的是强调，无论是常规的`coroutineScope`还是阻塞的`coroutineScope`都不会完成执行，直到它的所有子进程完成它们自己。

因为初始的`runBlocking`函数启动了`suspend`函数`suspendingWork()`，它创建了自己的`coroutineScope`，所以在`runBlocking`调用之外的打印语句将会等到这个程序中的其他所有东西都完成执行之后，才会轮到它执行:

```
fun main() {
    runBlocking { // this: CoroutineScope
        suspendingWork()
        println("of")
    }
    println("execution.")
}

suspend fun suspendingWork() = coroutineScope { // this: CoroutineScope
    launch {
        delay(2000L)
        println("flow")
    }
    launch {
        delay(1000L)
        println("the")
    }
    println("Follow")
}

```

```
Output:
> Follow
> the
> flow
> of
> execution.

```

### 实例 5

在这最后一个例子中，我们将所有这些放在一起。我们首先阻塞调用`main()`的线程，并在其中打印第一个字符串。深入到`suspendingWork()`的第一个`suspend`函数，我们创建了另一个`coroutineScope`，其中我们挂起了另外两个协程并打印了我们的第二个字符串。

从这里开始，执行流程或多或少与上面的例子相同，然后返回到在继续执行之前一直等待内部`coroutineScope`的阻塞范围。在这里，我们在脱离阻塞范围和打印最后一条语句之前，打印倒数第二个字符串。

应该注意的是，第二个`suspend`函数可以从程序中的任何地方调用，除了在`main()`内部，只要我们不在阻塞`coroutineScope`内部:

```
fun main() {
    runBlocking { // this: CoroutineScope
        suspendedPrint("Try")
            suspendingWork()
        suspendedPrint("of")
    }
    println("execution.")
}

suspend fun suspendingWork() {
    coroutineScope { // this: CoroutineScope
        launch {
            delay(2000L)
            suspendedPrint("the")
        }
        launch {
            delay(1000L)
            suspendedPrint("follow")
        }
        suspendedPrint("to")
    }
    suspendedPrint("flow")
}

suspend fun suspendedPrint(value: String) {
    println(value)
}

```

```
Output:
> Try
> to
> follow
> the
> flow
> of
> execution.

```

## 结论

我们在这里已经看了很多材料，也看了很多例子。我们现在应该知道`suspend`和`runBlocking`函数之间的区别，以及它们之间的关系。

此外，我们学习了什么是`coroutineScope`,如何使用它，以及它与`runBlocking`作用域的关系，作用域本身是一个`coroutineScope`,以及如何只允许这些类型的作用域以及其他挂起函数被称为`suspend`函数。

从我们已经讲过的例子中，还可以推导出更多的例子。如果有兴趣的话，我推荐使用新的 [Kotlin Playground tool](https://play.kotlinlang.org/#eyJ2ZXJzaW9uIjoiMS43LjEwIiwicGxhdGZvcm0iOiJqYXZhIiwiYXJncyI6IiIsIm5vbmVNYXJrZXJzIjp0cnVlLCJ0aGVtZSI6ImlkZWEiLCJjb2RlIjoiaW1wb3J0IGtvdGxpbnguY29yb3V0aW5lcy4qXG5cblxuZnVuIG1haW4oKSB7XG4gICAgXG59In0=) 来快速编译和运行简单的 Kotlin 程序。

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)