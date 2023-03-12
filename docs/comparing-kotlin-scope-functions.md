# 比较 Kotlin 范围函数

> 原文：<https://blog.logrocket.com/comparing-kotlin-scope-functions/>

Kotlin 编程语言是为 Java 虚拟机(JVM)设计的，它结合了面向对象和函数式编程特性，以及其他编程范例。在 Android 开发中，Kotlin 提供了一个独特的功能，称为范围函数，然而，许多开发人员在处理这些函数时会遇到一些困难。

作为一名 Android 移动开发人员，充分掌握这一概念非常重要，这是应用程序开发中至关重要的一部分。Kotlin 的魅力来自于独特的特性，这些特性使它适合前端和后端开发。在本教程中，我们将涵盖以下内容:

要跟随本教程，您需要以下内容:

我们开始吧！

## 什么是范围函数？

在 Kotlin 中，作用域函数用于执行对象作用域内的代码块。通常，您可以使用作用域函数包装一个变量或一组逻辑，并返回一个对象文本作为结果。因此，我们可以不用这些对象的名字来访问它们。科特林中有[五种作用域函数:`let`、`with`、`run`、`apply`、`also`。让我们考虑一下这些例子及其独特的用例。](https://kotlinlang.org/docs/scope-functions.html)

基于它们相似的操作，这五个作用域函数之间有许多相似之处，然而，它们在返回 lambda 结果还是上下文对象上有所不同。它们也因您是使用`this`还是`it`关键字引用上下文对象而有所不同。

## `let`功能

`let`函数有许多应用，但它通常用于防止`NullPointerException`发生。`let`函数返回 lambda 结果，上下文对象是`it`标识符。让我们考虑下面的例子:

```
fun main (){
val name: String? = null

println(name!!.reversed)
println(name.length)
}

```

在上面的代码片段中，我们给`name`变量分配了一个`null`值。然后，我们通过包含一个`NotNull`断言操作符`(!!)`来输出字符串的`reverse`和`length`，以断言值不是`null`，因为我们有一个可空的字符串名称。因为我们是在一个`null`值上调用函数，这导致了一个`NullPointerException`。然而，我们可以通过使用带有以下代码的`let`函数来防止这种情况:

```
fun main (){

val name: String? = null

name?.let{
println(it.reversed)
println(it.length)
}
}

```

我们将代码放在`let`函数的 lambda 表达式中，并用`it`标识符替换上下文对象名。为了防止`NullPointerException`，我们把一个`safe call operator`、`( ?.)`，就放在我们的`name`对象之后。

只有当`name`对象是`NotNull`时，`safe call operator`才会设置一个条件并指示我们的程序执行代码。在这个例子中，我们不需要使用`NotNull`断言`(!!)`。

接下来，我们将为变量`name`分配一个字符串值`“I love Kotlin”`。然后，我们通过将 lambda 值保存在一个名为`lengthOfString`的变量中来返回这个字符串值:

```
fun main (){

val name: String? = "I love Kotlin!!"

val lengthOfString = name?.let{
println(it.reversed)
println(it.length)
}
println(lengthOfString)
}

```

## `with`功能

`with`函数有一个`return type`作为 lambda 结果，上下文对象是`this`关键字，它指的是对象本身。让我们考虑下面代码片段中的例子:

```
class Person{
   var firstName: String = "Elena Wilson"
   var age: Int = 28
}
fun main() {
  val person = Person()
  println(person.firstName)
  println(person.age)
}

```

在上面的代码片段中，我们创建了一个`Person`类，并分配了一些属性`firstName`和`age`。接下来，在我们的主函数中，我们使用用于`cli`输出的`println`打印出值。

假设我们在`Person`类中有超过二十个属性，这会导致多次代码重复。我们可以通过使用`with`函数并使用`this`关键字在 lambda 表达式中传递`person`对象来纠正这个问题:

```
n){
 println(this.firstName)
 println(this.age)
}

```

这里的上下文对象指的是执行操作的`person`对象。`with`函数的返回值是一个 lambda 结果。假设我们决定给`age`加 10 年，并将该值存储在一个名为`personInfo`的变量中，变量的类型为`integer`:

```
val person = Person()
val personInfo : String = with (person){
 println(this.firstName)
 println(this.age)
 age + 10
"I love the game of football"
}
println(personInfo)
}

```

产生的值是`“I love the game of football”`。总之，`with`函数返回一个 lambda 函数，并使用`this`关键字作为上下文对象。

## `run`功能

`run`函数返回 lambda 结果，我们通过使用`this`关键字来引用上下文对象。`run`功能是`with`和`let`功能的组合。让我们考虑下面代码片段中的例子:

```
fun main {

val person: Person? = Person()
val bio = person?.run {
 println(name)
 println(age)
"LogRocket rocks!!!"
   }
println(bio)
}

```

假设我们决定给`person`对象赋一个空值，我们必须防止`NullPointerException`发生。我们可以通过用`person`对象调用`run`函数来实现这一点。接下来，我们将返回 lambda 函数`bio`。

## `apply`功能

`apply`是高阶函数。`apply`函数返回一个上下文对象，上下文对象返回`this`。让我们考虑下面的例子:

```
val car = Car()
  var carName: String = ""
  var carColor: String = ""

fun main {

 val car = Car().apply {
 carName = "Lamborghini"
 carColor = "Navy blue"
   }
}
 with(car){
 println(carName)
 println(carColor)
  }

```

## `also`功能

`also`函数类似于前面的函数，用于在初始化后对特定对象执行操作。`also`函数返回上下文对象，使用`it`标识符可以引用上下文对象。让我们参考下面的代码片段了解更多细节:

```
fun main(){

val numberList: mutableList<Int> = mutableListOf(1,2,4,5)
    numberList.also{
println("The list of items are: $numberList")

numberList.add(6)
println("The list of items after adding an element are: $numberList")
numberList.remove(4)

println("The list of items after removing an element are: $numberList")
    }
}

```

从上面的代码中，我们创建了一个具有五个整数值的`numbersList`对象，并在`numbersList`对象下执行了一些操作。然后我们利用了`also`函数。注意，在`also`函数中，我们可以通过使用`it`标识符来引用`numberList`，如下面的代码片段所示:

```
fun main(){

val numberList: mutableList<Int> = mutableListOf(1,2,4,5)
     val multipleNumbers = numberList.also {
println("The list of items are: $it")

it.add(6)
println("The list of items after adding an element are: $it")

it.remove(4)
println("The list of items after removing an element are: $it")
    }
println("The original numbers are: $numberList")
println("The multipleNumbers are: $multipleNumbers)
}

```

实现`also`功能的另一种方式是使用`it`和`also`关键字，如下面的代码片段所示。我们使用`also`函数通过给`Eden Peter`赋值来修改`firstName`变量的值:

```
fun main {

 val person = Person().apply {
 firstName = "Eden Elenwoke"
 age = 22
   }
 with(person){
 println(firstName)
 println(age)
  }

person.also{
 it.firstName = "Eden Peter"
println("My new name is: + ${it.firstName}")
 }
}

```

## 何时以及如何使用 Kotlin 范围函数

起初，在正确的地方使用范围函数可能看起来有点棘手，但这很大程度上取决于我们想要用 project 实现什么。让我们参考下面的总结作为指导，告诉我们对于每个独特的用例使用哪个范围函数:

*   `apply`:您想要配置或初始化一个对象
*   `with`:你想对一个非空对象进行操作
*   `let`:你想在一个可空的对象上执行一个 lambda 函数并避免`NullPointException`
*   `run`:你想对一个可空的对象进行操作，执行一个 lambda 表达式，避免`NullPointerException`。这是`with`和`let`功能特性的组合
*   `also`:您想要执行一些额外的对象操作和配置

## Kotlin 范围函数与普通函数的比较

让我们用几个例子来比较一下作用域函数和普通函数。让我们考虑一个普通的函数，使用一个名为`Student`的`class`，它有三个属性:`studentName`、`studentNumber`和`studentAge`，如下所示:

```
Class Student {
   var studentName : String? = null
   var studentNumber : String? = null
   var studentAge : Int? = null
}

```

通过下面的代码片段，我们实例化了我们的类并为其赋值:

```
val student = Student ()
student.studentName = "Peter Aideloje"
student.studentNumber = 08012345678
student.studentAge = 28

```

使用一个`scope function`可以帮助我们用更少的代码以更简单、更干净的方式获得与上面相同的结果。让我们将上面的表达式与下面代码片段中的`scope`函数进行比较:

```
val person = Student().apply{
    studentName = "Peter Aideloje"
    studentNumber = 08012345678
    studentAge = 28
}

```

在上面的代码片段中，我们实例化了`Student`对象并调用了`apply`函数。然后，我们在 lambda 表达式中指定`studentName`、`studentNumber`和`studentAge`属性。

当我们在上面的例子中比较 scope 函数和 normal 函数时，我们注意到我们成功地消除了代码重复，其中`student`对象名重复了多次。使用范围函数使我们的代码更加简洁易读，我们初始化属性时没有使用`student`对象名。

## 使用范围函数的好处

从上面函数比较部分的例子中，我们已经认识到使用范围函数的一些好处:

*   简化样板代码
*   更简洁和精确的代码
*   减少代码重复
*   增强的代码可读性

为了进一步阅读，你也可以查阅官方的 Kotlin 文档。

## 结论

在本文中，我们介绍了 Kotlin 中的五个范围函数。我们还考虑了一些独特的用例，回顾了何时使用每个范围函数。我们比较了作用域函数和普通函数，最后回顾了使用作用域函数的好处。

随着市场上越来越多的 Android 设备，Android 开发越来越受欢迎，Kotlin 编程语言的知识将变得更加重要。我希望这篇文章是有帮助的，如果你有任何问题，请随时留下评论。编码快乐！

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)