# 如何在 Kotlin 中使用 compareTo()和其他字符串操作

> 原文：<https://blog.logrocket.com/how-use-compareto-other-string-actions-kotlin/>

Kotlin 是一种静态类型的编程语言，这意味着每个变量都应该有一个固定的类型。有各种内置的 Kotlin 类型，其中一种是`String`类型。

我们使用`String`类型来存储字符串值，字符串值是由双引号括起来的字符序列。然后，我们可以用各种方式处理这些存储的字符串值。

让我们了解更多关于 Kotlin 琴弦的知识以及如何使用它们。在本指南中:

## 科特林的琴弦是什么？

让我们看一个例子来更好地理解 Kotlin 琴弦:

```
val firstName: String = "Kingsley"    

```

这里变量的名字是`firstName`，类型是`String`，双引号`""`里面的值是`Kingsley`。

因为我们马上给变量赋值等于`Kingsley`，所以我们可以取消类型定义。因此，下面的变量同样有效:

```
val firstName = "Kingsley"    

```

这种方便的 Kotlin 特性被称为类型推断。基本上就是说编译器可以自动推导出一个变量的类型，所以我们不需要特别指明。

## 在 Kotlin 中处理字符串

每当我们使用`""`创建一个字符串时，就会初始化一个新的`String`对象。这个对象为我们提供了几个内置的属性和方法来帮助我们处理字符串值。

属性为我们提供了关于给定字符串值的信息，比如它的长度、字符串中特定字符的位置等等。

方法是我们可以在对象上调用以直接操作其值的函数。例如，我们可以调用字符串上的方法来返回原始字符串的子集。

让我们来看看如何在 Kotlin 中执行一些常用的字符串操作。

### 通过索引检索 Kotlin 字符串的单个字符

字符串中的每个字符都由一个索引表示，索引只是一个整数值。和大多数编程语言一样，我们从 0 开始计算索引。字符串中第一个字符的索引为 0，第二个字符的索引为 1，依此类推。

我们可以使用字符串中的一个字符的唯一索引来检索它，就像这样:

```
fun main() {
    val greeting = "Hi Kingsley"       

    println(greeting[0]) // H
    println(greeting[1]) // i
    println(greeting[2]) // whitespace
    println(greeting[3]) // K
}

```

### 在 Kotlin 中检查字符串是否为空

我们可以使用方法`isEmpty()`检查变量是否有空字符串值。

下面是一个检查空字符串的示例:

```
fun main() {
    val emptyStr = ""       

    println(emptyStr.isEmpty()) // true
}

```

如果字符串为空，则`isEmpty()`返回布尔值`true`，如果字符串包含任何字符，则返回`false`。

### 在 Kotlin 中获取字符串的长度

假设你有一个程序，你想缩写所有超过给定长度的名字。为此，您必须首先获得每个字符串的长度。

length 属性返回字符串中的字符数:

```
fun main() {
    val greeting = "Hi Kingsley"       

    println(greeting.length) // 11
}

```

字符串中的所有字符都计算在内，包括空格。

### 在 Kotlin 中获取字符串子集

要切掉一段更长的字符串，使用 Kotlin 的`substring()`方法。

`substring()`提取位于提供的开始和结束索引之间的字符串部分。在下面的示例中，我们提取第 6 个索引和第 11 个索引之间的所有字符:

```
fun main() {
    val greeting = "Hello Worldd"       

    println(greeting.substring(6, 11)) // World
}

```

注意，不包括第 6 个索引处的字符，但包括第 11 个索引处的字符。

### 在 Kotlin 中连接琴弦

字符串串联是指两个或多个字符串被合并。合并两个或更多字符串的简单方法是使用加法运算符`+`:

```
fun main() {
    val firstName = "Kingsley"   
    val lastName = "Ubah"   
    val fullName = firstName + lastName

    println(fullName) // KingsleyUbah
}

```

您可以用空格或任何其他字符分隔两个字符串:

```
fun main() {
    val firstName = "Kingsley"   
    val lastName = "Ubah"   
    val fullName = firstName + " " + lastName

    println(fullName) // Kingsley Ubah
}

```

您也可以使用`+`将变量嵌入到一个更大的字符串中:

```
fun main() {
    val customer = "Kingsley"
    val totalAmount = 50
    val items = 6

    val msg = "Hi " + customer + ", your cart has a total of " + items + " items and they amount to " + totalAmount + " dollars"

    println(msg)
}

```

请注意，使用`+`重复连接会很快使您的代码更难阅读。您可以使用模板字符串来避免这个问题。

模板字符串允许你直接在主字符串中嵌入变量。使用模板字符串，很容易发现任何缺失的空格。

要在模板字符串中包含变量，请在每个变量名前加上美元符号`$`，如下例所示:

```
fun main() {
    val customer = "Kingsley"
    val totalAmount = 50
    val items = 6

    val msg = "Hi $customer, your cart has a total of $items items and they amount to $totalAmount dollars"

    println(msg) 
}

```

在 Kotlin 中，模板字符串是[让你的代码更干净、更易读的许多方法之一。](https://blog.logrocket.com/understanding-kotlin-generics/)

### 使用比较运算符比较 Kotlin 字符串

您可以在 Kotlin 中使用比较运算符来检查两个或多个 string 对象在结构上或引用上是否相等。

结构比较检查两个或多个对象是否具有相同的值。为了检查这种等式，我们使用双等号`==`。这里有一个基本的例子:

```
fun main() {
    val a = "Hello"
    val b = "Hallo"
    val c = "Hello"

    println(a == b)   // returns false
    println(a == c)   // returns true

}

```

因为`a`和`c`都有相同的值，将它们与`==`比较会返回`true`。

请记住，比较是区分大小写的。这意味着大写和小写字母被解释为不同，因此字符串“hello”与“Hello”不同。

引用比较检查两个或多个变量是否指向同一个对象。对于引用比较，我们使用三重等号`===`，如下例所示:

```
fun main() {
    val str_1 = "Hello"    
    val str_2 = "Hello"
    val str_3 = "Hello World"

    println(str_1 === str_2) // true     
    println(str_1 === str_3) // false
}

```

`str_1`和`str_2`之间的引用比较返回`true`，因为当进行第二次赋值时，Java 虚拟机(JVM)发现池中已经存在字符串“Hello”。

让第二个变量指向同一个字符串对象“Hello”，可以节省一些内存。

但是，JVM 为第三个变量赋值分配单独的内存，因为值不同。因此，`str_1`和`str_3`之间的比较返回`false`。

### 使用`equals()`函数比较 Kotlin 字符串

对于那些来自 Java 背景的人来说,`equals()`函数应该很熟悉。与我们之前看到的`==`类似，`equals()`检查两个对象是否有相同的内容。

让我们看一个使用这种简单方法比较字符串的例子。下面，我们定义了四个字符串变量。四个里面，只有`a`和`c`是一样的。

```
fun main() {
    val a = "Hello"
    val b = "Hallo"
    val c = "Hello"
    val d = "hello"

    println(a.equals(b))    // returns false
    println(a.equals(c))     // returns true
    println(a.equals(d, true))     // returns true
}

```

请注意，`equals()`检查在默认情况下是区分大小写的，因此字符串“hello”与“Hello”不同。

然而，与`==`不同，我们可以选择通过在`equals()`中传递第二个参数`true`来消除大小写敏感性。我们在最后一行中这样做了，我们将“hello”与“Hello”进行了比较，检查返回了`true`。

### 使用`compareTo()`函数比较 Kotlin 字符串

还可以用`compareTo()`比较 Kotlin 中的字符串。这种方法的基本语法如下:

```
mainStr.compareTo(otherStr)

```

前面的方法返回一个布尔值(`true`或`false`)，而`compareTo()`返回一个整数:

*   如果主字符串和另一个字符串相等，则返回`0`
*   如果另一个字符串的 ASCII 值大于主字符串，则返回负数
*   如果另一个字符串的 ASCII 值小于主字符串，则返回一个正数

```
// Example 1
fun main() {
val a = "Hallo"
val b = "Hello"

println(a.compareTo(b))    // returns -4

}

// Example 2
fun main() {
val a = "Hello"
val b = "Hallo"

println(a.compareTo(b))    // returns 4

}

// Example 3
fun main() {
val a = "Hello"
val b = "Hello"

println(a.compareTo(b))    // returns 0

}
```

默认情况下，字符串比较区分大小写。像使用`equals()`一样，您可以通过传递参数`true`来覆盖这种行为。

在下面的示例中，第一次检查返回一个负值，因为我们显式启用了区分大小写。第二个检查返回`0`，因为我们禁用了区分大小写。

```
fun main() {
    val upper: String = "Hello"
    val lower: String = "hello"

    println(upper.compareTo(lower, false))    // returns false (-32)
    println(upper.compareTo(lower, true))     // returns true (0)

}

```

### 替换 Kotlin 中的字符串

在 Kotlin 中，`String.replace()`方法用于替换字符串。基本语法如下:

```
mainString.replace(oldValue, newValue)

```

此方法检查主字符串中是否出现所提供的旧值，并用新值替换每个实例。这里有一个例子:

```
fun main() {
    var mainStr = "Replacing Strings in Python"
    val oldValue = "Python"
    val newValue = "Kotlin"

    val newStr = mainStr.replace(oldValue, newValue)

    println(newStr) // Replacing Strings in Kotlin
}

```

要禁用区分大小写，请将`true`作为第三个参数传递。让我们来看看实际情况:

```
var mainStr = "Replacing Strings in python"    
    val oldValue = "Python"
    val newValue = "Kotlin"

    var newStr = mainStr.replace(oldValue, newValue, true)
    println(newStr) // Replacing Strings in Kotlin

```

如您所见，“python”和“Python”之间的大小写差异被忽略了。

### Kotlin 中的字符串排序

在 Kotlin 中，我们可以通过对字符串连续执行方法`toCharArray()`、`sorted()`和`joinToString()`来按字母顺序对字符串进行排序。

假设您希望将字符串“elephant”重新排序为字母顺序:“aeehlnpt”。首先，用`toCharArray()`方法将字符串转换成数组。然后，使用`sorted()`对数组进行排序。最后，使用`joinToString()`将数组变回字符串:

```
fun main() {
    val str = "elephant"
    val arr = str.toCharArray()
    val sorted = arr.sorted().joinToString("")
    println(sorted) // aeehlnpt
}

```

您可以将所有内容包装在自定义函数中，并在需要对字符串进行排序时调用该函数:

```
fun sortByAlpha(str: String) {       
    val arr = str.toCharArray()
    println(arr.sorted().joinToString(""))

}
fun main() {
    sortByAlpha("elephant")
}

```

## 摘要

在本教程中，我们学习了 Kotlin 字符串以及如何执行一些常见的 Kotlin 字符串操作。这些包括获取字符串长度、排序字符串、连接字符串、比较字符串以及使用字符串模板使字符串可读。

我希望这份全面的 Kotlin 琴弦指南对你有所帮助。如果你想了解更多，Kotlin 提供了[大量关于字符串](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/)的文档，这些文档超出了我们在本文中讨论的属性和方法，涵盖了与[相关的概念，比如 Kotlin 扩展](https://blog.logrocket.com/everything-you-need-to-know-about-kotlin-extensions/)。

祝你一周愉快。

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)