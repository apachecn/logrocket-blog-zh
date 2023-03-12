# 探索 Kotlin 中条件和循环的高级用法

> 原文：<https://blog.logrocket.com/advanced-uses-conditions-loops-kotlin/>

循环和条件是软件程序中制定算法的基本概念。

循环结构允许程序员连续执行一系列指令，直到满足给定的条件。它们还可以用于迭代具有连续值的东西，比如字符串或数组集合。

在本文中，我们将看看在 Kotlin 中使用条件和循环的一些基本和高级方法。我们将涵盖:

## Kotlin 中的条件和循环概述

在 Kotlin 中，有几种数据结构可以包含一系列值。这些 [Kotlin 数据结构的一些例子包括字符串](https://blog.logrocket.com/how-use-compareto-other-string-actions-kotlin/)、数组、范围和映射。

通常，出于各种原因，您可能想要迭代它们的值。例如，您可能想要在列表中查找特定的项，或者您想要将数组中的每个数字乘以 2，或者您想要获取字符串的子集。

无论是哪种情况，`for`循环都允许您遍历集合并顺序访问集合中的每个值。从技术上讲，只要提供迭代器，就可以在`for`循环中迭代任何东西——比如字符串、数组、范围等等。

当一次迭代达到它的目的时——比如在列表中找到一个给定的条目——它必须被终止。实现这一点最常见的方法是在循环中使用条件表达式。

条件表达式的计算结果是`true`或`false`。在 Kotlin 中，用于计算条件表达式的三个控制语句是`if`、`else`和`when`。

一个`if`语句允许你指定如果一个条件表达式的计算结果为`true`时应该运行的代码，而`when`允许你根据条件表达式的实际值指定多个代码分支来运行。

此外，`else`块可以嵌套在`if`和`when`语句中，以指定在条件评估为`false`时运行的替代代码。

`if`和`when`都可以做表达式。在这种情况下，第一个匹配分支的值成为整个表达式的值。这使得可以像在 JavaScript 等其他语言中使用三元运算符一样使用它们。

回到循环，您可以在每次迭代中执行`if`检查，并在检查结果为`true`时终止循环。

您将在接下来的章节中了解到这一点以及更多。

## 在 Kotlin 条件表达式中使用`if`和`else`

让我们从基础开始。

在 Kotlin 中，如果给定的条件表达式计算结果为`true`，则`if`用于执行一系列指令(代码)。例如，在下面的代码块中，`if`检查变量`a`是否大于变量`b`。如果是，它将变量`aIsGreater`设置为`true`。

```
var a:Int = 25
var b:Int = 10

var aIsGreater:Boolean = false

if (a > b) aIsGreater = true

println(aIsGreater) // prints true

```

如果指定的条件评估为`false`，您有时会想要执行一个单独的代码块。这是使用`else`完成的。

回到我们的第一个例子，如果`a`不大于`b`，我们将把`false`赋给变量`aIsGreater`并打印输出。我们还将交换`a`和`b`的值，使`b`大于`a`。代码如下:

```
var a:Int = 10
var b:Int = 25

var aIsGreater:Boolean = false

if (a > b) {
    aIsGreater = true
} else {
    aIsGreater = false
}

println(aIsGreater)// prints false

```

您还可以在条件表达式中使用`if`——同样，在 Kotlin 中不需要三元运算符:

```
// Used as an expression:
val aIsGreater = if (a > b) true else false

// "if" expression can also have blocks:
val aIsGreater = if(a > b) {
    true
} else {
    false
} 

```

Anytime `if`用在条件表达式中，并且有代码块作为分支，那么块中的最后一个表达式就是块的值。在上面的例子中，根据条件表达式，它可能是`true`或`false`。

## 在 Kotlin 条件表达式中使用`when`和`else`

接下来，我们有`when`，它评估一个条件，然后将该值与其分支中的值进行比较。如果有匹配，它将执行相应的代码。否则，它将退回到`else`块。

如果`when`内的分支没有覆盖所有可能的情况，则需要`else`块。在下面的例子中，变量`day`被设置为`1`。因此，将执行第一个分支上的代码:

```
val day:Int = 1    

when (day) {
   1 -> print("Today is Monday!")
   2 -> print("Today is Tuesday!")
   3 -> print("Today is Wednesday!")
   4 -> print("Today is Thursday!")
   5 -> print("Today is Friday!")
   6 -> print("Today is Saturday!")       
   else -> {
        print("Oh, so it's Sunday then")
   }
}

```

假设我们将日期设置为`7`，它与分支上的任何值都不匹配。在运行代码时，else 块将运行，输出`Oh, so it’s Sunday then`。

您还可以为多种情况定义常见行为。例如，在下面的`when`块中，我们为`1`、`2`和`3`输出相同的消息:

```
val day:Int = 1
when (day) {
   1, 2, 3 -> print("It's any of the first three days of the week")
   4 -> print("Today is Thursday!")
   5 -> print("Today is Friday!")
   6 -> print("Today is Saturday!")       
   else -> {
        print("Oh, so it's Sunday then")
   }
}

```

在两种情况下，您也可以在`when`块中使用`in`表达式。首先，检查一个项目是否是集合中的[。否则，检查从条件表达式返回的数值是否在给定的范围内。以下示例演示了这两种情况:](https://blog.logrocket.com/guide-to-map-interface-in-kotlin/#collections-framework-in-kotlin)

```
val today = 8
val daysOfTheWeek = arrayOf(1, 2, 3, 4, 5, 6, 7)

when (today) {
  in 1..7, in daysOfTheWeek -> print("This is a valid day and part of the weekdays!")
  !in 1..7 -> print("This is not a valid day!")       
  else -> {
    print("Neither of the two")
  }
}

```

为了清楚起见，我们不必在上面的例子中定义`else`块，因为`when`块中的分支涵盖了所有可能的情况——要么是一周中的某一天，要么不是。我只是把它留在那里，让它变得完整和彻底。

## 科特林`for`循环

在 Kotlin 中，`for`循环结构用于遍历一个 iterable，并对每个迭代实例执行一致的操作。

假设您有一个水果集合，对于每种水果，您想打印它的名称和索引。下面是如何在 Kotlin 中使用`for`循环来实现这一点:

```
val fruits = arrayOf("orange", "apple", "mango", "pear", "banana")
for (i in 0..fruits.size-1)
{
   println(fruits[i] + " "+ "is at index"+ " " + i)
}

```

以下是输出结果:

```
orange is at index 0 
apple is at index 1 
mango is at index 2 
pear is at index 3 
banana is at index 4

```

您还可以使用范围表达式迭代一系列数字:

```
for (i in 1..10) {
    println(i) // prints 1-10
}

```

或者定义更高级的循环方法，比如向后迭代，每三步只计算一次:

```
for (i in 12 downTo 0 step 3) {
    println(i)
}

```

`downTo`用于逆向迭代数字。所以在上面的例子中，我们从 12 数到零，在每个数字之间跳过三步。结果如下:

```
12 
9 
6 
3 
0

```

## `while`和`do...while`

只要给定的条件为真，`while`和`do...while`都允许你继续做某事。两者的区别在于检查条件的时间——在执行代码块之前还是之后。

在执行代码块之前，将首先检查条件是否为真。如果是这样，它将在再次检查条件之前执行代码块。这个循环将继续下去，直到条件评估为`false`。

`do...while`将首先执行代码块，然后检查条件是否为真。如果是这样，它将在再次检查之前执行该块，依此类推。基本上，不管条件如何，`do...while`的主体至少执行一次。

下面的`while`循环执行代码块，只要`x`小于或等于`6`:

```
var x = 1
while (x <= 6) {
   println("Number $x")
   ++x
}

```

结果是:

```
Line 1 
Line 2 
Line 3 
Line 4 
Line 5 
Line 6

```

只要`x`小于或等于`6`，下面的`do...while`循环也会执行代码块。但是，在检查条件之前，它首先执行块:

```
var x = 1
do{
  println("Number $x")
  ++x
} while (x <= 6)

```

上面的代码给出了与`while`循环示例相同的输出。在继续之前，有一些重要的事情你应该记住。

如果你留心的话，你可能会问下面这个问题:“如果条件继续为`true`并且永远不会计算为`false`呢？”答案是它导致了一个被称为无限循环的问题。

无限循环或无限循环是一种永远不会结束并无限继续下去的循环，除非发生外部干预——通常，页面开始冻结，浏览器会提示您关闭选项卡。

当您在程序中使用循环时，您必须确保循环有一个终止点——条件计算结果为`false`的点。否则，会导致无限循环。

回到上两个`while`和`do...while`循环的例子，你可以看到我们在每次迭代中增加了变量`x`。这种移动确保了循环最终终止——在这种情况下，当`x`大于`6`时。

下面是一个无限循环的例子:

```
var x = 1
while (x <= 6) {
   println("Number $x")
}

```

这是一个无限循环，因为`x`在每次迭代中总是为 1，因此`while`检查的结果总是为`true`。

接下来，我们将看看如何控制循环中程序执行的流程。

## 使用`break`和`continue`控制循环执行

当您在 Kotlin 程序中执行循环时，您可以使用`break`和`continue`跳转表达式来控制循环内的代码执行流程。

### `break`表情

在 Kotlin 程序中执行循环时，`break`表达式用于在条件变为`true`时终止循环。

让我们看看下面的例子。一旦我们找到了`myLuckyNumber`，继续迭代其余的就没有意义了，所以我们使用了`break`表达式来终止循环:

```
val myLuckyNumber = 5
for (i in 1..10) {

   if(i == myLuckyNumber) {
       break
   }
   println("Current no is "+ i +". I want to stop at 4")
}

```

一旦`for`迭代到达`5`，它就终止循环。以下是输出结果:

```
Current no is 1\. I want to stop at 4
Current no is 2\. I want to stop at 4
Current no is 3\. I want to stop at 4
Current no is 4\. I want to stop at 4

```

`break`表达式不仅用于在循环结束前终止循环，还用于终止无限循环。假设上面的循环打算永远继续下去(当`i`等于 10 时不会终止)，像我们在上面的例子中那样使用`break`也会终止循环。

### `continue`表情

`continue`表达式用于跳过循环中的特定迭代。

我们来看看下面这个例子，和上一个差不多。这一次，我们不想迭代`myUnluckyNumber`，所以我们通过使用`continue`表达式跳过它:

```
val myUnluckyNumber = 3
for (i in 1..10) {

   if(i == myUnluckyNumber) {
       continue
   }
   println("Current no is "+ i +". I don't want to see number 3, it's bad!")
}

```

以下是输出结果:

```
Current no is 1\. I don't want to see number 3, it's bad! 
Current no is 2\. I don't want to see number 3, it's bad! 
Current no is 4\. I don't want to see number 3, it's bad! 
Current no is 5\. I don't want to see number 3, it's bad! 
Current no is 6\. I don't want to see number 3, it's bad! 
Current no is 7\. I don't want to see number 3, it's bad!

```

## 摘要

让我们回顾一下我们在本文中讨论的关于 Kotlin 编程语言中的条件和循环。

`if`用于在给定条件为真时执行一系列代码。您可以将一个`else`块链接到它，并定义如果条件为`false`时应该执行的代码。

`when`计算一个条件，然后将该值与其分支中的任何一个值进行比较。如果有匹配，它将运行相应的代码。如果没有匹配，它将退回到`else`块——在这种情况下，必须提供这个块。

一个`for`循环可以用来遍历任何提供迭代器的东西，可以是字符串、数组、映射等等。

始终确保可以终止循环，以防止产生无限循环。要在给定条件变为`true`时终止循环，可以在循环内的`if`检查中使用`break`表达式。

当给定条件为`true`时，要跳过循环中的特定迭代，可以在循环中的`if`检查中使用`continue`表达式。

感谢您的阅读，祝您本周愉快！

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)