# for Android 队列指南

> 原文：<https://blog.logrocket.com/kotlin-queue-guide-for-android/>

Kotlin 编程语言是 Android 开发的绝佳选择。然而，像任何编程语言一样，它[需要考虑时间和空间的复杂性](https://blog.logrocket.com/kotlin-vs-java-android-development/#what-kotlin)并利用最佳方法来解决这些问题。因此，扎实掌握 Kotlin 中的数据结构和算法将对保证更好的代码效率起到至关重要的作用。

其中一个数据结构是 Kotlin 队列，这是一个集合接口，用于按照 FIFO 概念存储和删除数据，这意味着第一个进来的值将是第一个出去的值。在本文中，我们将深入探讨 Kotlin 队列，涵盖不同的类型及其优点。要跟随本教程，您需要以下内容:

*   科特林的基础知识
*   安装了 Android Studio 或 IntelliJ IDE

## 目录

我们开始吧！

## 什么是科特林队列？

队列是 Kotlin 中的一种接口集合，它使您能够根据自己的偏好以更有效的方式组织数据。队列接口最流行的实现是`[LinkedList](https://developer.android.com/reference/kotlin/java/util/LinkedList)`，其他的包括`ArrayDeque`和`PriorityQueue`。

## 为什么我们在 Android 开发中需要队列？

队列在 Android 开发中的重要性怎么强调都不为过。队列在存储数据或事件以供以后处理的任何地方起缓冲区的作用。本质上，队列是一个集合，用于在处理之前保存元素。

除了基本的收集操作之外，Kotlin 队列还提供额外的操作，比如插入、提取和检查。这些方法有两种形式，一种是当操作失败时抛出异常，另一种是返回特殊值，如`null`、`true`或`false`。

Kotlin 队列的一些重要应用包括多程序设计、网络和作业调度以及共享资源。多程序设计包括在系统主存中运行的多个程序；将这些程序组织成一个队列确保了系统的有序性。当一台计算机被安排一个接一个地执行特定数量的作业时，就发生了作业调度。这些分配给处理器的作业被组织成一个队列。最后，队列充当单个共享资源的等待列表。

此外，队列还用于交换机或路由器等网络设备中。另一个应用程序是邮件队列，它包含一个存储数据和控制邮件文件的目录。

在 Kotlin 中，我们可以使用队列轻松管理大量数据或处理进程间通信。

## 科特林的队列类型

Kotlin 中有四种主要的队列类型:简单队列、优先级队列、循环队列和双端队列。

### 简单队列

在简单队列(也称为线性队列)中，元素的插入(称为入队操作)发生在后端，元素的移除(称为出队操作)发生在前端。

### 优先队列

优先级队列根据某种优先级排列队列中的元素。例如，如果具有最高值的元素具有优先级，它将创建一个值顺序递减的队列。或者，如果具有最低值的元素具有最高优先级，它将创建一个值顺序递增的队列。

### 循环排队

线性队列和循环队列实际上非常相似。循环队列中的元素充当一个环，这意味着最后一个元素连接到第一个元素。当有空闲空间时，内存的使用效率会更高。当队列中的特定位置没有元素时，可以将另一个元素添加到该位置。

### 出列

在出列(也称为双端队列)中，可以在队列的两端插入或移除元素，这与只有一端的其他队列不同。然而，它可能不服从 FIFO 操作。

## 队列的实现

要在 Kotlin 中实现我们的队列，我们有两个选择。首先，顺序分配涉及使用数组实现队列，我们可以用数组来组织有限数量的元素。另一方面，`LinkedList`分配涉及使用`LinkedList`实现队列，它可以组织无限数量的元素。

## 创建 Kotlin 队列

下面的代码片段展示了使用`LinkedList`在 Kotlin 中实例化队列的过程:

```
import java.util.LinkedList

fun main(args: Array<String>)
{
     val namesOfVideoGames = LinkedList<String>()
     namesOfVideoGames.addAll(list.Of("playstation5","Sega","X-Box","Nintendo"));

     for (games in namesOfVideoGames){
         println(games)
        }
}

```

在实例化过程之后，我们使用一个`forEach`循环列出添加到队列中的所有元素:

```
output:
playstation5
Sega
X-Box
Nintendo

```

## 科特林队列的方法:`enqueue`、`dequeue`、`isEmpty`和`peek`

队列有四种操作:

*   `dequeue`:删除队列前面的元素并返回
*   `enqueue`:在队列后面插入一个元素，如果操作成功返回`true`
*   `isEmpty`:使用`count`属性检查队列是否为空
*   `peek`:返回队列前面的元素，但不删除它

### 在队列中添加元素

```
import java.util.LinkedList
fun main()
{
        val queueExample = LinkedList<Int>()

    queueExample.add(1)
    queueExample.add(2)

    print(queueA.poll())
}

```

上面的代码显示了将数值`1`和`2`添加到`queueExample`中。

`poll()`和`remove()`方法的不同之处仅在于队列为空时它们各自的行为。`poll()`方法返回一个`null`值，而`remove()`方法抛出一个异常。队列接口的`poll()`方法返回并移除容器前端的元素:

```
public A remove() {
    A y = poll();
    if (y != null)
        return y;
    else
        throw new NoSuchElementException();
}

val universityStudentQueue: Queue<String> = LinkedList<String>(mutableListOf("Peter", "Joe", "Elena", "Rocky", "Groovy"))
  println(universityStudentQueue) 
  universityStudentQueue.add("Barack")
  println(universityStudentQueue)

```

上面的代码片段显示了向队列列表添加字符串值。这里，我们使用一组名称值来实例化`universityStudentQueue`，稍后我们打印这些名称，它们是 Peter、Joe、Elena、Rocky 和 Groovy。然而，这个名单上又多了一个名字，巴拉克。请注意，这个新值按照严格的 FIFO 概念，位于队列的最后一个位置。

### 将元素添加到队列的末尾

我们可以使用`addLast`方法或`add`方法将元素添加到队列的末尾:

```
import java.util.LinkedList

fun main(args: Array<String>)
{
    var universityStudentQueue = LinkedList<String>();
    universityStudentQueue.addAll(listOf("Mary", "Glory", "Becky"))

   universityStudentQueue.addLast("Michael")
}

```

### `OfferLast`:安全地将元素添加到队列中

下面的代码演示了一种替代`addLast`方法的安全方法，称为`OfferLast`。如果达到或超过了队列容量限制，它将引发异常。当`add`元素失败时，返回`false`:

```
import java.util.LinkedList

fun main(args: Array<String>)
{
    var universityStudentQueue = LinkedList<String>();
    universityStudentQueue.addAll(listOf("Mary", "Glory", "Becky"))

    val offerLast: Boolean = planetsQueue.offerLast("Jupiter")

    println("Offer last result =  $offerLast")

    for(student in universityStudentQueue)
    {
        println(student)
    }
}

Output:
Offer last result = true
Mary
Glory
Becky

```

### `pollFirst`:从队列中安全地删除元素

`pollFirst`是`removeFirst`函数的替代函数，在未能从队列中移除元素时返回`null`,而不是抛出异常:

```
import java.util.LinkedList

fun main(args: Array<String>)
{
    var universityStudentQueue = LinkedList<String>();
    universityStudentQueue.addAll(listOf("Elena", "Peter", "Roosevelt"))

    var student = universityStudentQueue.pollFirst()
    println(student)

    student = universityStudentQueue.pollFirst()
    println(student)

    student = universityStudentQueue.pollFirst()
    println(student)

    student = universityStudentQueue.pollFirst()
    println(student)
}

Output:
Elena
Peter
Roosevelt
null

```

队列的`pollFirst`函数的最后一个结果返回`null`,因为队列是空的，并且`pollFirst`在该点没有更多的元素要移除。

### 从队列中安全地获取元素

`peekFirst`或`peekFunction`用于从队列中选取第一个元素。它是`getFirst`函数或`first`属性的替代，当没有检索到元素时，返回`null`而不是抛出异常:

```
import java.util.LinkedList

fun main(args: Array<String>)
{
    var universityStudentQueue = LinkedList<String>();
    universityStudentQueue.addAll(listOf("Owoitakata", "Lucky", "Leroey"))

    var student = universityStudentQueue.peekFirst()
    println("Peekfirst $student")

    student = universityStudentQueue.pollFirst()
    println("Removed $student")

    student = universityStudentQueue.pollFirst()
    println("Removed $student")

    student = universityStudentQueue.pollFirst()
    println("Removed $student")

    student = universityStudentQueue.peekFirst()
    println("PeekFirst $student")
}

Output:
Peekfirst Owoitakata
Removed Owoitakata
Removed Lucky
Removed Leroey
PeekFirst null

```

## 在 Kotlin 中处理队列错误

作为一种异步编程范式，Kotlin 需要良好的异常和错误处理技术。

每当您启动一个异步操作时，它将无任何错误地运行，并以结果结束。这构成了真正的威胁，因为程序执行过程中出现的错误可能会被忽视。与任何未处理的异常一样，应用程序通常会崩溃。

假设任何异步操作都将成功无误地运行，这是一个非常冒险的举动。为了充分理解协程执行中的错误和异常处理，首先理解这些错误和异常是如何传播的是很重要的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在 Kotlin 中，只有未检查的异常，这些异常只能在运行时被捕获。抛出异常的一般方法是使用`throw`表达式，如下面的代码片段所示:

```
throw Exception("Exception occurred here")

```

注意，所有的异常类都是`Throwable`类的后代。Kotlin 队列中的一些常见异常包括:

*   `NullPointerException`:通常在试图调用`null`对象的属性或方法时抛出
*   `Arithmetic Exception`:执行无效的算术数值运算时抛出
*   `SecurityException`:当存在安全违规时抛出
*   `ArrayIndexOutOfBoundException`:当我们试图访问一个数组的无效索引值时抛出

让我们将下面的代码片段作为算术异常的示例:

```
fun main(args : Array<String>){
    var number = 500 / 0      // arithmetic exception is thrown here
    println(number)
}

```

Kotlin 中处理异常的四种常见方法是:

*   `try...catch`
*   `try-catch-finally`
*   `try-finally`

### `try...catch`

`try`块编写您认为可能抛出异常的语句，而`catch`块编写异常处理代码:

```
fun main(args: Array<String>)
{        
        try
    {
        var i: Int = 100;
        throw Exception("Exception thrown in first try!")
    }
    catch(e: Exception)
    {
        e.printStackTrace()
    }
    println("Successful!")        
}

```

### `Try-catch-finally`

由于有了`finally`块，`try-catch-finally`异常非常重要，它有助于您编写始终执行的代码。因此，无论`try`块中发生什么，无论是否发生异常，都会执行`finally`块中的语句:

```
fun main(args: Array<String>)
{        
        try
    {
        var i: Int = 1000;
        throw Exception("Exception thrown in first try!")
    }
    catch(e: Exception)
    {
        e.printStackTrace()
    }
    finally{
        println("Finally Block Result!")
    }
}

```

### `Try-finally`

`try-finally`异常只有`try`和`finally`块。它很有用，因为它有助于块确保正确释放资源:

```
fun main(args: Array<String>)
{        
        try
    {
        var i: Int = 10;
        throw Exception("Throwing Exception!")
    }
    finally
    {
        println("Break the rules!");
    }
}

```

## 在基于队列的系统上重试失败的项目

在基于队列的系统中，我们可以实现重试逻辑，使系统能够执行特定的代码片段指定的次数。在下面的代码中，我们用指定的最大重试值将代码写在循环内的`try...catch`块中:

```
import kotlin.random.Random

const val MAXIMUM_NUMBER_OF_RETRIES = 4

fun main() {
    for (i in 0..MAXIMUM_NUMBER_OF_RETRIES) {
        try {
            // generate 0 or 1 with equal probability
            val zeroOrOne = Random.nextInt(2)
           println("The random number is.. $zeroOrOne")

            // 50% probability of "ArithmeticException: / by zero"
            val rand = 1 / zeroOrOne

           // don't retry on success
            break
        } catch (e: ArithmeticException) {
           // handle exception
           println(e.message) // log exception

           // Sleep time is 1 seconds before retrying
          Thread.sleep(1000)

          // throw exception if the last re-try fails
          if (i == MAXIMUM_NUMBER_OF_RETRIES) {
                throw e
            }
        }
    }
}

```

以下是输出:

```
The random number is...0
/ by zero
The random number is...0
/ by zero
The random number is...0
/ by zero
The random number is...1

```

在上面的代码片段中，输出值各不相同。如果代码抛出一个`ArithmeticException`，则控制转到`catch`块。处理此异常后，每秒钟重试一次。在所有重试都失败后，最后一次重试失败，系统抛出异常。

## 结论

在本教程中，我们深入探讨了 Kotlin 队列，包括它们的创建、操作、异常，并讨论了错误发生时的处理方法。我们首先介绍了 Kotlin 队列及其在 Android 开发中的优势。然后，我们讲述了 Kotlin 队列实例化的过程，以及队列用来执行某些操作的一些方法。

我想你也会同意 Kotlin 队列数据结构在提高代码效率方面提供了巨大的优势。编码快乐！

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)