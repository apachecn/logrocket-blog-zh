# Kotlin 中的零安全完全指南

> 原文：<https://blog.logrocket.com/complete-guide-null-safety-kotlin/>

编程语言最常见的问题之一是访问带有`null`值的变量会在运行时导致空引用异常。这导致了在编程时可能难以解决的几个问题。

这就是为什么一些编程语言引入了指示变量可为空的可能性，或者换句话说，它可以接受`null`值。因此，任何变量都不能包含`null`值，除非您明确声明它可以。这样，您可以避免`null`引用的危险，并且不必等待运行时抛出异常或错误。

Kotlin 从第一次发布就支持这种可能性，它被称为空安全。这个特性无疑代表了 Kotlin 编程语言最酷、最漂亮、最具创新性的特性之一。这就是为什么知道如何正确使用它是如此重要。

所以，让我们深入到科特林的零安全中，了解你应该知道的一切。

## 不可空类型与可空类型

与 Java 中的情况相反，Kotlin 区分了不能保存`null`的引用和可以保存的引用。第一类被称为不可空引用，它们的类型必须是不可空的类型。另一方面，第二种被称为可空引用，并且必须是可空类型。

当像在 Java 中一样在 Kotlin 中初始化变量时，使用的是不可空的类型。这是因为 Kotlin 在默认情况下强加了严格的空安全。让我们看看这意味着什么:

```
// fooString is a non-nullable reference
var fooString: String = "foo" 

```

当试图给`fooString`一个`null`值时，Java 和 Kotlin 在处理`null`值时的真正区别变得很明显，如下所示:

```
fooString = null

```

在 Kotlin 中，这会导致编译错误，因为`fooString`变量是用不可空的类型声明的。在 Java 中，这不会导致任何错误，除了在运行时试图调用方法或访问`fooString`上的字段时出现 [`NullPointerException`](https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/lang/NullPointerException.html) 。

原因是 Java 不支持空安全，不可空类型不存在。换句话说，任何变量总是一个可空的引用，除了定制逻辑，没有办法避免`null`值。因此，Java 中的任何引用都可能默认为`null`。

即使在 Kotlin 中，变量也可以接受`null`值，但是你必须显式声明它。在上面的例子中，您可以通过改变`fooString`变量的类型并用相应的可空类型替换它来实现:

```
// fooString will now be able to accept null values
var fooString: String? = "foo"

```

正如您所看到的，通过在类型名的末尾添加`?`字符，您使变量成为一个可空的引用。这就是可空类型在 Kotlin 中的用法。

现在，下面一行代码不会导致任何编译时错误，就像在 Java 中一样:

```
fooString = null

```

可空类型也可以作为[泛型](https://it.wikipedia.org/wiki/Generics_Java)使用，但是同样，您必须显式地将它们声明为可空:

```
// a list do no accepting null values
val nonNullableList: List<Int> = listOf(1, 2, 3, 4)

// a list accepting null values
val nullableList: List<Int?> = listOf(1, 2, 3, null)

```

可空类型在处理强制转换时也很有用。这是因为如果对象与目标类型不匹配，操作将导致 [`ClassCastException`](https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/lang/ClassCastException.html) 。但是 Kotlin 引入了安全的强制转换操作符`as?`，它在强制转换操作失败时返回`null`:

```
// safe cast operator in action
val safeIntVariable: Int? = originalVariable as? Int

// regular cast operator in action
// a ClassCastException may be thrown at runtime 
val intVariable: Int = originalVariable as Int

```

## 基本零安全

学习如何正确处理 Kotlin 中的空安全需要时间。幸运的是，Kotlin 是一种通用的编程语言，它支持两种方法来简化空安全的处理，尤其是对于初学者。让我们看看他们的行动。

### 显式检查`null`

如果你想避免使用先进的技术来解决 Kotlin 中的空安全问题，你所要做的就是使用你在 Java 中使用的相同方法来避免 [`NullPointerException`](https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/lang/NullPointerException.html) 。基本上，在通过调用一个可空变量字段的方法来访问它之前，您总是必须显式地检查变量是否不为空，并分别处理两种可能的情况。

这可以通过一个`if-else`表达式轻松实现:

```
var fooString: String? = "foo"

// ...

// Explicitly checking for the null value 
// and handling the two possible cases separately
val fooStringlength = if (fooString != null) { 
 fooString.length 
} else {
  -1
}

```

Kotlin 编译器足够聪明，可以跟踪代码逻辑，并理解当`fooString`为空时有一个后备逻辑。因此，编译时不会抛出任何错误，因为严格的 Kotlin null 安全性如预期的那样得到了实施。事实上，`fooString`变量只有在有合适的值时才会被访问。

这种方法的主要问题是，只有当要检查的变量是不可变的时，它才有效。具体来说，它只适用于在检查和使用之间不被修改的局部变量，或者具有后备不可重写字段值的`val`类成员。这是因为编译器无法确保可空变量在检查后没有被更改为`null`。

### 过滤`null`值

当处理一个可空类型的集合时，您可以通过过滤它们来简单地将它们从等式中移除。这可以通过使用任何 [Kotlin 集合](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/)附带的`[filterNotNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-not-null.html)`方法轻松实现，如下所示:

```
val nullableList: List<Int?> = listOf(1, 2, 3, null)
// filterNotNull() returns a list with no longer a nullable type
val intList: List<Int> = nullableList.filterNotNull()

```

如您所见，`filterNonNull()`方法返回了相应的不可空类型的列表，从而隐式地解决了空安全处理问题。

## 使用 Kotlin 运算符的高级零安全性

Kotlin 附带了一些自定义操作符，这些操作符代表了正确解决空安全问题的推荐的高级方法。让我们来学习如何使用它们。

### 安全呼叫操作员`?.`

[Kotlin 安全调用操作符`?.`](https://kotlinlang.org/docs/null-safety.html#safe-calls) 允许你访问一个字段或者调用一个可空变量的方法。特别是，只有当变量的引用不为空时，该操作符才对变量执行操作。否则返回`null`。让我们通过一个例子来看看它的作用:

```
var fooString: String? = "foo"

// ...

// it returns fooString's length, or null if fooString in null
println(fooString?.length)

// it returns fooString's value in uppercase, or null if fooString in null
println(fooString?.uppercase())

```

另外，这个操作符在执行多个链调用时特别有用。在这种情况下，如果任何属性为`null`，链调用将返回`null`:

```
fooCity?.mayor?.name?.uppercase()

```

换句话说，如果链调用中的任何变量不是`null`，则返回大写的`fooCity`的市长名称。否则，`null`就返回了。

请记住，安全调用操作符也可以用在赋值的左侧。发生的情况是，如果安全调用链中的一个属性是`null`，那么右边的表达式不被求值，结果赋值被跳过:

```
fooCity?.mayor?.name = "Alicia"

```

在这种情况下，只有当`fooCity`及其`mayor`属性不是`null`时，才执行赋值。

此外，该操作符可以与`[let()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/let.html)`范围函数一起使用，仅对非空值执行特定操作:

```
val nullableList: List<Int?> = listOf(1, 2, 3, null)

for (number in nullableList) {
    // printing only nun-null values
    number?.let { 
      println(it) 
    }
}

```

点击了解更多关于 [Kotlin 的范围函数。](https://kotlinlang.org/docs/scope-functions.html)

### 猫王操作员`?:`

[猫王操作符`?`](https://en.wikipedia.org/wiki/Elvis_operator) 的 Kotlin 实现:允许您在原始可空变量为`null`时返回默认值。换句话说，如果`?:`操作符之前的表达式不是`null`，Elvis 操作符将返回它。

否则，它将表达式返回到`?:`操作符的右边。这意味着只有当左边的表达式是`null`时，右边的表达式才会被求值。否则，它将被完全跳过。让我们看看下面的实际情况:

```
val length = fooString?.length ?: -1

```

这个表达式读起来就像一个完整的`if-else`表达式，如下所示:

```
val length: Int = if (fooString != null) {
  fooString.length
} else { 
  -1
}

```

注意`throw`和`return`是常规的 Kotlin 表达式。这意味着它们可以用在`?:`操作器的右侧:

```
fun foo(fooCity: FooCity): String? {
    // returning null if fooCity has no mayor
    val mayor = fooCity.mayor ?: return null

    // trhowing an exception is mayor has no name
    val mayorName = mayor.name ?: throw IllegalArgumentException("The mayor must have a name")

    return mayorName
}

```

### 非空断言运算符`!!`

Kotlin [非空断言操作符`!!`](https://kotlinlang.org/docs/null-safety.html#the-operator) 抛出一个 Kotlin [`NullPointerException`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-null-pointer-exception/) 如果它所应用的变量是`null`。该运算符的作用是将任何值转换为非空类型，并通过在其他情况下引发异常来确保它不为空:

```
var fooString: String? = "foo"

// ...

// fooString should not be null, 
// otherwise a NullPointerException will be thrown
val length = fooString!!.length

```

应该小心使用该运算符。特别是，当您拥有的信息比编译器所能拥有的更多，并且您确信在使用可空变量时它不能为空时。

## 空安全 Kotlin 操作符在起作用

您已经看到了在 Kotlin 中处理空安全的基本和高级方法。因此，您已经准备好通过三个真实世界的例子来看 Kotlin 操作符的运行。

### `?.`操作员

让我们通过一个例子来看看`?.`操作符的作用:

```
val names: List<String?> = listOf("Mary", null, "James", "Jennifer")

fun printAllUppercase(values: List<String?>) {
  for (value in values) {
    // the ?. operator is mandatory to avoid compile-time errors
    println(value?.uppercase())
  }  
}

fun printAllNonNull(values: List<String?>) {
  for (value in values) {
    // printing only when value is not null
    value?.let {
      println(it)
    }
  }  
}

printAllUppercase(names)
println("-----------")
printAllNonNull(names)

```

如果执行，该代码片段将返回:

```
MARY
null
JAMES
JENNIFER
-----------
Mary
James
Jennifer

```

正如您所看到的，在第一种情况下，所有的名字都被打印出来，而在第二种情况下，只考虑非空的名字。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### `?:`操作员

让我们通过一个例子来看看`?`:操作符的作用:

```
val names: List<String?> = listOf("Mary", null, "James", "Jennifer")

fun printAllOrMissing(values: List<String?>) {
  for (value in values) {
    println(value ?: "<Missing name>")
  }  
}

printAllOrMissing(names)

```

运行时，该代码片段返回:

```
Mary
<Missing name>
James
Jennifer

```

如您所见，由于 Elvis 操作符，名称列表中的`null`值被替换为`printAllOrMissing`函数中的默认`<Missing name>`字符串。

### `!!`操作员

让我们通过一个例子来看看`!!`操作符的作用:

```
class User {
    var name: String? = null
    var surname: String? = null
}

val fooUser = User()
fooUser.name = "John"
fooUser.surname = "Smith"

println("${fooUser.name!!.uppercase()} ${fooUser.surname!!.uppercase()}")

```

如果运行，将打印以下结果:

```
JOHN SMITH

```

在这种情况下，您可以确保`name`和`surname`字段在被访问时不会成为`null`。另一方面，编译器不能推断出这一点，因为它们不满足前面定义的不变性要求。

因此，如果省略了`!!`操作符，编译时会抛出以下两个错误:

```
Smart cast to 'String' is impossible, because 'fooUser.name' is a mutable property that could have been changed by this time

Smart cast to 'String' is impossible, because 'fooUser.name' is a mutable property that could have been changed by this time

```

## 结论

在本文中，我们研究了 Kotlin 空安全代表了什么，如何正确地解决它，以及通过哪些技术和方法。如图所示，Kotlin 提供了许多操作符和内置函数来处理空安全，并为您提供了更大的行动自由。另外，Kotlin 从第一天起就支持这些操作符，使得空安全成为该语言最重要的特性之一。

由于 Kotlin 变量在默认情况下是不可空的，如果您习惯于用 Java 编程，可能会遇到一些问题。这就是为什么我们学习了 Java 和 Kotlin 的默认行为之间的主要[差异。此外，我们还深入研究了 Kotlin null 安全操作符，了解它们是如何工作的，何时使用，为什么使用，以及如何使用。](https://blog.logrocket.com/kotlin-vs-java-for-android-development/)

感谢阅读！我希望这篇文章对你有所帮助。如果有任何问题、意见或建议，请随时联系我。

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)