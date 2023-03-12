# Kotlin 中的类型转换:不安全与安全

> 原文：<https://blog.logrocket.com/type-casting-kotlin-unsafe-vs-safe/>

让我们来看看你需要知道的一切，以便像专业人士一样开始处理安全和不安全的 Kotlin 类型铸造。

面向对象编程语言的一个不可或缺的特性是将一种类型转换成另一种类型的能力。为了使开发人员能够实现这一目标，这些编程语言引入了类型转换的概念。

类型转换提供了将特定类型转换成更小或更大类型的能力。但是可以想象，这个转换操作是很棘手的，可能会失败或者导致意想不到的结果。这就是为什么 Kotlin 引入了两个操作符来为您提供处理类型转换所需的一切:安全操作符和不安全操作符。

掌握 Kotlin 中的类型转换需要时间，但它在语言中起着如此重要的作用，每个 Kotlin 开发人员都应该能够有效地使用它。所以，让我们深入 Kotlin 的类型转换，看看你需要学习掌握安全和不安全的类型转换，以及更多。

## 什么是类型转换？

类型转换也称为类型转换，是将变量从一种数据类型转换为另一种数据类型的过程。这通常通过一些操作符或特定的语法来实现。此外，在某些情况下，它可以由解释器或编译器在后台自动执行。类型转换操作的一个例子是将整数转换为字符串。

将一种类型转换为更小的类型称为向下转换。较小的类型是在内存中占用较少字节的类型，或者表示继承层次结构中当前类型的子类型。类似地，将一种类型转换为更大的类型称为向上转换。较大的类型是在内存中占用更多字节的类型，或者表示继承层次结构中当前类型的父类型。

请记住，类型转换是一个容易出错的操作，不应该轻松地执行。因此，意外的强制转换可能会导致异常和致命错误。这就是为什么您应该知道如何正确地使用它们，对它们有什么期望，以及当它们发生时如何防止或处理错误。

让我们在科特林找到这一切。

## Kotlin 类型转换与 Java 类型转换

由于大多数 Kotlin 开发人员以前都是 Java 开发人员，或者仍然使用这两种技术，因此在类型转换时，有必要指出这两种语言之间的差异。

详细来说， [Java 支持从小类型到大类型的隐式类型转换](https://docs.oracle.com/javase/specs/jls/se18/html/jls-5.html)。例如，可以将一个`int`变量赋给一个`long`变量，而无需显式转换:

```
// this code is valid in Java
int number1 = 42;
long number2 = number1; // implicit type conversion performed 

```

这个 Java 片段是有效的，不会导致任何错误。在赋值期间，Java 执行隐式类型转换，将`number1`从`int`转换为`long`类型。在赋值结束时，`number2`是一个`long`类型的变量，存储了`number1`值的`long`表示。

相反，Kotlin 不支持从小类型到大类型的隐式类型转换。这意味着没有显式的强制转换或类型转换，`Int`变量不能转换为`Long`变量:

```
// this code is invalid in Kotlin
val number1: Int = 42
val number42: Long = number1 // Type mismatch: inferred type is Int but Long was expected

```

这个 Kotlin 代码片段无效，会导致“类型不匹配:推断的类型是`Int`但应该是`Long`”错误。

如果你想在 Kotlin 中将一个`Int`转换成一个`Long`，你需要通过`[toLong()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/to-long.html)`函数执行一个显式的类型转换操作，如下所示:

```
// this code is valid in Kotlin
val number1: Int = 42
val number2: Long = number1.toLong()

```

在赋值结束时，`number2`正确地存储了`number1`值的`Long`表示。

请注意，Kotlin 附带了几个函数，用于将较小的类型转换为较大的类型，反之亦然。因此，如果您想在 Kotlin 上对基本类型执行类型转换，可以考虑使用 Kotlin 提供的转换实用函数。

## 使用 Kotlin 转换运算符的显式类型转换

基本类型转换通过显式类型转换在 Kotlin 中进行。之所以这样叫它，是因为如果你想进行类型转换，你必须使用 Kotlin 提供的两个转换操作符之一。

具体来说，您应该知道两个强制转换运算符:

1.  不安全的投操作员: [**为**](https://kotlinlang.org/docs/typecasts.html#unsafe-cast-operator)
2.  安全投算符: [**为？**](https://kotlinlang.org/docs/typecasts.html#safe-nullable-cast-operator)

现在让我们更深入地研究这两者，并学习如何通过例子来使用它们，以及何时最好使用其中的一个。

### 不安全的强制转换运算符:`as`

`as` Kotlin 强制转换操作符被称为不安全的，因为它在强制转换无法执行时抛出一个`[ClassCastException](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-class-cast-exception/)`。换句话说，它被认为是不安全的，因为每当显式强制转换不可行时，它都会抛出异常。

您可以如下使用`as`运算符:

```
var foo: Any = "Hello, World!"
val str1: String = foo as String
println(str1)

```

在这个例子中，假设在编译时不知道`foo`变量的类型。事实上，`[Any](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/)`是 Kotlin 类层次结构的根，这意味着每个 Kotlin 类都有`Any`作为超类。换句话说，任何 Kotlin 变量都可以将`Any`作为类型。

但是正如您所看到的，`foo`存储了一个字符串，这就是为什么在通过使用`as`的显式类型转换将`foo`赋值给`str1`时所执行的类型转换操作会起作用。运行时，该代码片段打印:

```
Hello, World!

```

注意，如果没有`as`，代码片段将返回一个“类型不匹配:推断的类型是 Any，但应该是 String”错误。

但是并不是所有的显式强制转换都是成功的。让我们来看看这个例子:

```
var foo: Any = 42
val str1: String = foo as String
println(str1)

```

此代码将导致以下错误:

```
Exception in thread "main" java.lang.ClassCastException: class java.lang.Integer cannot be cast to class java.lang.String

```

这就是为什么`foo`存储一个`Int`值，它不能被转换成`String`类型。

类似地，当涉及可空类型时，`as` cast 运算符可能会失败，如下所示:

```
var foo: Any? = null
val str1: String = foo as String
println(str1)

```

在本例中，您试图将可空类型转换为不可空类型。这将导致“null 不能转换为非 null 类型`kotlin.String`”错误。为了避免这种情况，只需将`str1`声明为可空类型，如下所示:

```
var foo: Any? = null
val str1: String? = foo as String?
println(str1)

```

在这种情况下，显式强制转换将正确无误地执行，并在终端中打印以下行:

```
null

```

[阅读这篇文章，了解你需要知道的关于 Kotlin 零安全的一切](https://blog.logrocket.com/complete-guide-null-safety-kotlin/)。

因此，每次通过`as`不安全操作符执行显式强制转换时，都应该考虑到可能会抛出一个`ClassCastException`。如果希望防止此错误导致应用程序崩溃，必须按如下方式处理它:

```
var foo: Any = 42

try {
    val str1: String = foo as String  
    // ... 
} catch (e : ClassCastException) {
    println ("Cast failed!")
}

```

这是使用 Kotlin 不安全强制转换操作符时避免错误的唯一方法。

### 安全强制转换运算符:`as?`

`as?` Kotlin 强制转换操作符被称为安全的，因为它在强制转换无法执行时返回`null`。换句话说，它被认为是安全的，因为它允许你避免异常，在失败时返回`null`。这也意味着当使用它时，接收器变量的类型应该总是可空的。否则，将会抛出一个错误，如下面的代码片段所示:

```
var foo: Any = "Hello, World!"
val str1: String = foo as? String
println(str1)

```

这个例子将返回一个“类型不匹配:推断的类型是`String?`，但需要的是`String`”错误，因为`str1`没有可空的类型。要让它工作，你所要做的就是将`str1`声明为`String?`:

```
var foo: Any = "Hello, World!"
val str1: String? = foo as? String
println(str1)

```

这将打印出:

```
Hello, World!

```

因此，`as?`安全强制转换操作符总是需要可空类型。

现在，让我们看看它在返回带有上述`as`的`ClassCastException`的同一个代码片段中的行为:

```
var foo: Any = 42
val str1: String? = foo as? String
println(str1)

```

这不会再失败了。相反，它会打印:

```
null

```

这是因为 42 是一个`Int`，不能转换为`String`，如前所述。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

因此，当使用安全强制转换操作符时，不再需要`try … catch …`语句。另一方面，请记住，接收器类型将总是可空的。因此，您应该始终考虑转换失败的情况，并且接收器变量具有`null`值。

您可以按如下方式处理这两种情况:

```
// ...

val str1: String? = foo as? String

if (str1 == null) {
    // Cast failed!

    // Here, you must access str1 attributes and methods with the ? operator
    // e.g. 
    // println(str1?.uppercase())

    // ...
} else {
    // Cast succeeded!

    // Here, you can access str1 attributes and methods directly
    // the ? operator is not required
    // e.g. 
    // println(str1.uppercase())
}

```

注意在`else`分支中，Kotlin 自动将`str1`转换为不可空的类型。这允许您将`str1`视为从未发生过的显式安全强制转换，并且总是具有所需的类型。

### 不安全的造型运算符与安全的造型运算符

总之，不安全和安全的 Kotlin 类型转换操作符都允许您执行显式类型转换。

当转换失败时，`as`不安全操作符返回一个异常。这意味着您应该小心使用它，并且只有在您确信强制转换将会成功时才使用它，例如在继承层次结构中将一个类型强制转换为它的超类型时。

相反，当强制转换失败时，`as?`安全操作符返回`null`。这意味着您可以更轻松地使用它，因为失败的强制转换不会使您的应用程序崩溃。另一方面，它涉及到可空类型，你必须知道如何处理它们。此外，您应该避免可空类型，因为它们不是绝对必要的。

## 使用 Kotlin 智能转换的隐式类型转换

尽管这不是本文的目标，但值得注意的是，Kotlin 也通过所谓的智能类型转换支持隐式类型转换。

### 智能铸造

Kotlin 智能转换功能基于`[is](https://kotlinlang.org/docs/typecasts.html#is-and-is-operators)` [和](https://kotlinlang.org/docs/typecasts.html#is-and-is-operators) `[!is](https://kotlinlang.org/docs/typecasts.html#is-and-is-operators)` [运算符](https://kotlinlang.org/docs/typecasts.html#is-and-is-operators)。这些允许您执行运行时检查，以确定变量是否属于给定的类型。您可以按照以下示例中的说明使用它们:

```
val foo1 : Any = "Hello, World!"

if (foo1 is String) {
println(foo1.uppercase())
}

val foo2 : Any = 42

// same as
// if !(foo is String) {
if (foo2 !is String) { 
    println("foo2 is not a String")
} 

```

如果执行，将会打印:

```
HELLO, WORLD!
foo2 is not a String

```

现在，让我们试着理解 Kotlin smat cast 特性是如何工作的。具体来说，智能强制转换使用`is` -check 来推断不可变变量的类型，并在需要时在编译时自动插入安全强制转换。当满足这个条件时，您可以避免在代码中编写显式类型转换，让编译器自动为您编写，如下面的示例所示:

```
val foo : Any = "Hello, World!"
// println(foo.length) -> "Unresolved reference: length" error

if (foo is String) {
    // Smart cast performed!

    // foo is now a String
    println(foo.length)
}

```

如您所见，`foo`拥有类型`Any`。这意味着如果你试图访问`length`属性，你会得到一个错误。这是因为`Any`没有这样的属性。但是`foo`店有一个`String`。因此，`is`检查将会成功，并且`foo`将被智能转换为`String`。因此，`foo`可以被视为`if`语句中的`String`。

总之，智能强制转换使您能够避免声明显式强制转换，避免在代码中填充无用的和可避免的指令。不过，理解它的工作原理可能会很棘手。这就是为什么你应该阅读 Kotlin 官方文档的[页并了解更多。](https://kotlinlang.org/docs/typecasts.html#smart-casts)

## 结论

在本文中，我们研究了什么是类型转换，Java 和 Kotlin 如何处理它，Kotlin 为您提供了哪些操作符，以及如何、何时以及为什么使用它们。如图所示，Kotlin 提供了两种类型转换方法。第一种涉及显式强制转换，需要两个操作符:`as`不安全强制转换操作符和`as?`安全强制转换操作符。

虽然看起来很相似，但它们有不同的意义和行为，尤其是在失败的情况下。所以，你千万不要混淆它们。在这里，我们看到了开始掌握它们并始终选择正确操作员所需的一切。

类型转换的第二种方法涉及隐式转换，并且基于智能转换 Kotlin 特性。这是一个强大的工具，可以避免用无用的显式强制转换填充代码，并让编译器为您推断正确的类型。

感谢阅读！我希望这篇文章对你有所帮助。如果有任何问题、意见或建议，请随时联系我。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)