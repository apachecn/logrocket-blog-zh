# 你需要知道的关于 Kotlin 扩展的一切

> 原文：<https://blog.logrocket.com/everything-you-need-to-know-about-kotlin-extensions/>

Kotlin 使您能够在不继承现有类的情况下，向现有类添加更多的功能。简而言之，这就是 Kotlin 扩展。

使用扩展很容易，只需要几行代码。它们允许您根据自己的需要修改第三方和内置的类。因此，它们是一个令人印象深刻的工具，每个 Kotlin 开发人员都应该知道如何使用它们。

现在让我们深入 Kotlin 扩展。首先，您将了解它们是什么以及它们是如何工作的。然后，是时候深入研究它们最相关的用途了。最后，您将学习如何在现实世界中使用它们。我们开始吧！

## 什么是 Kotlin 扩展？

正如官方文档中的[所解释的，Kotlin 允许你通过添加新特性来扩展一个类，而不必继承这个类或者使用](https://kotlinlang.org/docs/extensions.html)[装饰模式](https://en.wikipedia.org/wiki/Decorator_pattern)。多亏了科特林所说的*扩展*，这才成为可能。

特别是，Kotlin 扩展允许您向不能修改的类中添加函数。通过使用它们，您将能够调用这些新函数，就像它们是原始类的一部分一样。类似地，您可以使用这种机制向现有的类添加新的属性。你也可以扩展 Kotlin [伴随对象](https://kotlinlang.org/docs/object-declarations.html#companion-objects)。

可以想象，Kotlin 扩展是一个极其强大的工具。幸运的是，定义它们很容易，只需几行代码就可以完成，如下所示:

```
fun MutableList<String>.concatenateLowercase() : String {
    return this.map{ s -> s.lowercase() }.joinToString("")
}

```

注意扩展函数中的关键字`this`是如何对应于调用该函数的接收者对象的。

现在，每个`MutableList<String>`实例都有一个`concatenateLowercas()`函数，正如您在这里看到的:

```
val list = mutableListOf("First", "seConD", "ThIRd")
list.concatenateLowercase()

```

此代码片段打印:

```
firstsecondthird

```

## Kotlin 扩展如何工作

为了理解 Kotlin 扩展如何工作，您需要了解它们是如何被调度的。

特别是， [Kotlin 扩展是静态解析的](https://kotlinlang.org/docs/extensions.html#extensions-are-resolved-statically)。这意味着要调用的扩展函数是由编译时调用它的表达式的类型决定的，而不是由运行时计算该表达式得到的类型决定的。

让我们通过一个例子来更好地理解这一点:

```
open class Car
class Convertible: Car()

// defining the getType() extensions function on Car
fun Car.getType() = "Generic car"
// defining the getType() extensions function on Convertible
fun Convertible.getType() = "Convertible car"

fun getCarType(car: Car) : String {
   return convertible.getType()
}

fun main() {
    print(getConvertibleType(Convertible()))
}

```

这将打印:

```
Generic car

```

在 OOP ( [面向对象编程](https://en.wikipedia.org/wiki/Object-oriented_programming))逻辑中，你会期望它打印出“敞篷车”使用扩展函数时不会发生这种情况。事实上，被调用的`getType()`扩展函数来自编译时已知的`car`参数的声明类型，即`Car`类。

此外，您应该知道 Kotlin 扩展通常是在顶级作用域上定义的，直接位于包和导入行之下:

```
package com.logrocket.extensions

fun MutableList<String>.concatenateLowercase() : String {
    return this.map{ s -> s.lowercase() }.joinToString("")
}

```

然后，如果您需要在它的声明包之外使用它，您可以像对待任何外部依赖项一样导入它:

```
package com.logrocket.example

// importing the extension function defined in 
// the com.logrocket.extensions package
import com.logrocket.extensions.concatenateLowercase

fun main() {
    val list = mutableListOf("First", "seConD", "ThIRd")
    list.concatenateLowercase()
}

```

最后，您必须知道 Kotlin 扩展也可以在可空类型上定义。因此，即使对象变量的值为空，也可以对其调用 Kotlin 扩展函数。

具体来说，您可以通过手动检查 Kotlin 扩展函数体内的`this == null`来处理可空的 receiver 类型。请记住，在 null 检查之后，`this`将被自动转换为相应的非 null 类型。

让我们通过一个例子来看看这一点:

```
fun Any?.hashCode(): Int {
    if (this == null) {
      return 0
    }

    // 'this' is no longer nullable here    

    // fallback logic
    // calling the original hashCode method defined in the
    // Any class
    return hashCode()
}

```

如果你想了解更多关于 Kotlin 中零安全的工作原理，请阅读这篇文章。

## Kotlin 扩展的常见用法

Kotlin 扩展并不局限于函数。相反，这是一个多才多艺的有效机制，可以让你取得无穷的成果。现在让我们深入研究它最常见的用途。

### 扩展功能

这是 Kotlin 扩展特性最常见的用法。正如您已经看到的，向类中添加扩展函数非常容易，可以通过如下方式实现:

```
fun Int.doubleValue() : Int {
    return this * 2
}

```

通过这种方式，任何`Int`实例现在都将有一个`doubleValue()`函数返回两倍于其当前值的值。这是利用特殊的`this`关键字定义的。通过它，您可以访问静态解析的类型的对象实例，并使用它来实现您想要的逻辑。

### 扩展属性

使用 Kotlin 扩展，您还可以向现有的类添加新的属性。Kotlin 扩展属性可以定义如下例所示:

```
val <T> List<T>.penultimateElement: T?
    get() = if (size < 1)
        null
      else
        list.get(size - 2) 

```

这种属性允许您轻松地检索列表的倒数第二个元素(如果存在的话)。现在让我们看看如何访问它:

```
val list = mutableListOf("first", "second", "third")
print(list.penultimateElement)

```

这将在您的控制台中显示以下文本:

```
second

```

如您所见，这个新的扩展属性可以像任何其他普通属性一样被访问。它们的主要区别是扩展属性不能有初始值设定项。这意味着它们的值只能通过显式提供 getters 和 setters 来处理。

换句话说，下面的代码片段将返回“无法初始化扩展属性，因为它没有支持字段”错误:

```
var <T> List<T?>.penultimateElement: T? = null    
    get() = if (size < 1)
        null
      else
        get(size - 2)

```

这里的问题出在第一行，因为在 Kotlin 中，扩展属性不允许使用初始化器。

### 扩展伴随对象

如果一个类有一个[伴随对象](https://kotlinlang.org/docs/object-declarations.html#companion-objects)，你可以结合你刚刚学到的知识，为伴随对象定义扩展函数和/或属性。

由于可以通过使用`Companion`属性来访问伴随对象，所以您所要做的就是在扩展定义中指定它，以便将扩展添加到对象而不是类中。然后，就像伴随对象的任何其他属性或函数一样，可以只使用类名作为限定符来访问或调用它们，如下所示:

```
class FooClass {
    // defining an empty companion object
    companion object { }
}

fun FooClass.Companion.sayHello() { 
    println("Hello, World!") 
}

fun main() {
    FooClass.sayHello() // this is just like writing FooClass.Companion.sayHello()
}

```

运行时，该代码片段打印:

```
"Hello, World!"

```

## Kotlin 扩展的高级使用

扩展也可以在更复杂的情况下使用，但这是相当不常见的。然而，让我们在一个高级案例中看看它们的作用。

### 在类中定义扩展

一个类的扩展可以在另一个类中定义。您可能希望同时访问外部类和当前类。换句话说，在这种情况下有多个隐式接收者。

默认情况下，`this`指的是正在定义扩展的类型的对象。但是如果你想访问外部类，你可以通过使用`[this](https://kotlinlang.org/docs/this-expressions.html#qualified-this)`[语法](https://kotlinlang.org/docs/this-expressions.html#qualified-this)。让我们通过一个简单的例子来看看这是如何工作的:

```
class Foo() {
    fun sayHello() { 
        println("Hello, World!") 
    }

    fun sayBye() {
        println("Bye!")
    }
}

class User(val name: String, val foo: Foo) {
    // extending Foo with a new function
    fun Foo.sayGoodbye() {
        // calling the Foo sayBye() function
        this.sayBye()

        // calling the User sayGoodbye() function
        [email protected]()
    }

    fun sayGoodbye() {
        println("Goodbye, World!")
    }

    fun introduceYourself() {
        foo.sayHello() // Hello, World!
        println("My name is ${name}!")
        sayGoodbye() // "Goodbye, World!"
    }

    fun introduceYourselfWithExtendedFoo() {
        foo.sayHello() // Hello, World!
        println("My name is ${name}!")
        foo.sayGoodbye() // Bye!
                         // Goodbye, World!
    }
}

fun main() {
    val foo = Foo()

    User("Maria", foo).introduceYourself()
    println("---------------")
    User("Maria", foo).introduceYourselfWithExtendedFoo()

    // foo.saidGoodBye() --> Error! The extension function is unavailable outside User
}

```

以下是该示例打印的内容:

```
Hello, World!
My name is Maria!
Goodbye, World!
---------------
Hello, World!
My name is Maria!
Bye!
Goodbye, World!

```

如您所见，通过使用限定的`this`语法，可以访问`User` `sayGoodbye()`函数和`Foo`函数。在 Kotlin 中，扩展函数的隐式成员之间的冲突就是这样解决的。

## 运行中的 Kotlin 扩展

您已经看到了处理 Kotlin 扩展的基本和高级方法。现在，您已经准备好通过两个真实的例子来看它们的实际应用。

### 功能扩展

必须检索特定日期之后的一天是一项常见任务。您可以通过使用内置函数来实现这一点，但是您也可以定义一个具有更吸引人且易于记忆的名称的扩展函数，如下所示:

```
import java.time.LocalDate

fun LocalDate.getTomorrow() : LocalDate {
    return this.plusDays(1)
}

fun main() {
    val date = LocalDate.of(2022, 2, 15)
    println("Today:")
    println(date)

    println("\n----------\n")

    println("Tomorrow:")
    println(date.getTomorrow())
}

```

此代码片段打印:

```
Today:
2022-02-15

----------

Tomorrow:
2022-02-16

```

### 属性扩展

在 Kotlin 中处理日期时，能够方便地访问美国或欧洲格式的日期的字符串表示将非常有用。您可以使用两个扩展属性轻松实现这一点，如下所示:

```
import java.time.LocalDate
import java.text.SimpleDateFormat
import java.time.format.DateTimeFormatter

val LocalDate.americanFormatString : String
    get() = this.format(DateTimeFormatter.ofPattern("MM-dd-yyyy")).toString()

val LocalDate.europeanFormatString : String
    get() = this.format(DateTimeFormatter.ofPattern("dd-MM-yyyy")).toString()

fun main() {
    val date = LocalDate.of(2022, 2, 15)

    println("American format:")
    println(date.americanFormatString)

    println("\n----------\n")

    println("European format:")
    println(date.europeanFormatString)
}

```

运行时，它会打印:

```
American format:
02-15-2022

----------

European format:
15-02-2022

```

这样，每个`[LocalDate](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/time/LocalDate.html)`实例现在可以访问两个新的`americanFormatString`和`europeanFormatString`属性，这可以节省您的时间，避免重复和样板代码。

## 结论

在本文中，我们研究了 Kotlin 扩展代表什么，它们如何工作，以及如何和何时使用它们。如图所示，这个 Kotlin 特性代表了这种编程语言最酷的特性之一，它允许您在没有继承的情况下扩展来自第三方库的类。此外，您可以使用它们来扩展和修改内置的类以满足您的需求。

Kotlin 扩展允许您定制其他人定义的类，并且只在需要的时候和地方导入这些可选的扩展。因此，它们是每个 Kotlin 开发人员都应该能够掌握的强大工具。

感谢阅读！我希望这篇文章对你有所帮助。如果有任何问题、意见或建议，请随时联系我。

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[日志火箭](https://lp.logrocket.com/blg/kotlin-signup)是一款安卓监控解决方案，可以帮助您即时重现问题，区分错误优先级，并了解安卓应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)