# 了解 Kotlin 泛型

> 原文：<https://blog.logrocket.com/understanding-kotlin-generics/>

Kotlin 是一种由 JetBrains 开发的编程语言，JetBrains 是 IntelliJ IDEA、Pycharm 和其他 ide 的幕后团队，它们使我们的程序员生活变得更加轻松。Kotlin 通过允许我们编写更简洁的代码来做到这一点，同时比其他编程语言(如 Java)更安全。

让我们看看 Kotlin，为什么我们需要 Kotlin 泛型，并深入了解泛型的概念。

以下是我们将在本指南中介绍的内容:

## 开发者为什么需要 Kotlin？

JetBrains 团队最初创建 Kotlin 是为了内部使用。Java 使得 JetBrains 代码库难以维护，所以他们需要一种更现代的语言。

因为 Kotlin 提供了完整的 Java 互操作性，所以它很容易在从头开始构建的项目和开发人员喜欢采用新方法的现有代码库上使用。Kotlin 已经取代 Java 成为开发 Android 应用程序的首选语言。

目前，[谷歌 Play 商店排名前 1000 的应用中有超过 80%使用 Kotlin](https://developer.android.com/kotlin/stories) ，后端开发者也开始越来越多地使用它。此外， [Kotlin 多平台](https://kotlinlang.org/docs/multiplatform.html)越来越受欢迎，而 [Jetpack Compose 被广泛用于新项目](https://blog.logrocket.com/building-faster-android-apps-jetpack-compose/)。

我们必须注意，Kotlin 是一种静态类型的编程语言，这意味着我们必须在编译时指定并了解所有变量的类型。

动态类型的语言，比如 Python，可以在编写代码时为开发人员提供更大的灵活性。然而，这种做法容易出现运行时错误，因为变量可以采用任何类型的任何值。

通过为我们的变量指定类型，我们可以保持一致，并编写更健壮的代码，也更容易维护和调试。为什么？因为编译时错误比运行时错误更容易发现和修复。

## 为什么我们在 Kotlin 中需要泛型？

使用像 Kotlin 这样的强类型语言有时会让开发人员感到受限制。

当我们还是计算机科学一年级的学生时，我们都喜欢 Python，因为它让我们可以写任何东西。但是因为我们不知道如何编写适当的代码和其他最佳实践，我们最终得到了一碗不可能调试的意大利面条代码。

别担心，这个问题有一个很好的解决方案！这种解决方案被称为泛型编程，通常捆绑了一些枯燥难懂的定义。

在本文中，我们将使用一种轻松的方法，重点帮助您理解这些概念，回顾:

*   什么是泛型？
*   泛型的目的是什么？
*   `class`、`subclass`、`type`和`subtype`的区别
*   定义方差、协方差、不变性和逆变
*   Kotlin 泛型`in`和`out`关键字如何映射到这些术语

在阅读的最后，你将完全准备好在任何项目中使用 Kotlin 泛型。

## 什么是泛型？

泛型编程是一种以灵活的方式编写代码的方式，就像我们在动态类型语言中一样。同时，泛型允许我们安全地编写代码，并且尽可能少地出现编译时错误。

在 Kotlin 中使用泛型使开发人员能够专注于为更广泛的问题创建可重用的解决方案或模板。

我们可以将模板定义为可用于各种情况的部分填充的解决方案。当我们实际使用那个解决方案(例如，一个类)并为它提供一个实际类型时，我们填补了这个空白。

### 类、子类、类型和子类型之间的区别

当阅读泛型类型和继承时，我们会注意到`class`、`subclass`、`type`和`subtype`这些词随处可见。它们之间到底有什么区别？

一个`class`是将使用它实例化的对象的蓝图。这些对象将继承该类中声明的所有字段和方法。

一个`subclass`是从另一个类派生的类。简单地说，我们的子类将继承父类中存在的所有方法和字段。

我们可以说这些对象都有相同的由类定义的`type`。类型应该主要关注对象的接口，而不是在实例化对象时使用的类中可以找到的具体实现。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

当一个类从另一个类继承一个类型或者实现一个特定的接口时，就会创建一个`subtype`。

现在让我们回到泛型，理解为什么我们在像 Kotlin 这样的静态类型语言中需要它们。

### 泛型如何有用的例子

在下一个代码片段中，我们定义了一个堆栈，它只能用于处理整数:

```
class IntStack {

    private val elements: MutableList<Int> = ArrayList() 

    fun pop(): Int {
        return elements.removeLast()
    }

    fun push(value: Int) {
        elements.add(value)
    }
    // ...
}
```

暂时没什么特别的。但是如果我们需要存储整数字符串，甚至是小狗，会发生什么呢？然后我们需要再创建两个类:`StringStack`和`PuppyStack`。

小狗堆栈和整数堆栈有什么不同吗(显然，除了更可爱之外)？当然不是。因此，没有必要为每种情况创建单独的类。创建一个可以在我们项目的任何地方使用的通用堆栈就足够了:

```
class Stack<T> {

    private val elements: MutableList<T> = ArrayList()

    fun pop(): T {
        return elements.removeLast()
    }

    fun push(value: T) {
        elements.add(value)
    }
    // ...
}
```

现在我们可以使用这种数据结构来堆叠我们想要的任何东西，不管它有多可爱或多无聊。

但是，如果我们需要对使用泛型类的情况施加一些限制，该怎么办呢？这些限制可能实现不适用于每一种情况的行为。这就是我们引入方差、协方差、逆变和不变性概念的地方。

### 差异

方差是指不同类型的组件之间的相互关系。例如，`List<Mammal>`和`List<Cat>`具有相同的基础类型(`List`)，但是不同的组件类型(`Mammal`和`Cat`)。

理解这两种类型的列表在我们的代码中的行为以及它们是否符合我们的目的是很重要的。例如，看看下面的代码片段:

```
open class Mammal { ... }
class Cat: Mammal() { ... }
class Dog: Mammal() { ... }

val animals: MutableList<out Mammal> = mutableListOf()
animals.add(Dog(), Cat())
```

在上面的代码中，方差告诉我们一个`Dog`和一个`Cat`在一个定义为`List<Mammal>`的列表中拥有相同的权限。

下面的代码也可以工作:

```
val dogs: List<Dog> = listOf(Dog())
val mammal: Mammal = dog.first()
```

### 协方差

协方差允许您为可用于该类的类型设置上限。如果我们使用上面定义的堆栈来说明这个概念，我们将使用关键字`out`。

对于一个具体的例子，我们可以看看 Kotlin 中的`List<>`的定义和实例化:

```
public interface List<out E> : Collection<E> { ... }
...
val numbers: List<Number> = listOf(1, 2, 3.0, 4, ...)
```

通过这样做，我们实际上为这个列表的元素定义了一个上限，并放松了对泛型类型的限制。

换句话说，每当我们从上面创建的列表中检索一个元素时，我们肯定知道该元素至少属于类型`Number`。因此，在处理列表元素时，我们可以安全地依赖于`Number`类的任何属性或行为。

让我们看一个不同的例子:

```
class PetOwner<T>

// !!! This won't work: it's a type mismatch
val petOwner1: PetOwner<Animal> = PetOwner<Cat>()        

// This will work: we tell the compiler that petOwner2 accepts lists of its type's subtypes too
val petOwner2: PetOwner<out Animal> = PetOwner<Cat>()
```

当我们想将我们的使用仅限于子类型时，协方差非常有用:

```
val mammals: List<out Mammal > = listOf(Dog(), Cat())
mammals.forEach { mammal -> mammal.move() }
```

通过用上面的语法实例化我们的`mammals`列表，我们确保只有类型`Mammal`的子类型可以包含在列表中并从列表中检索。

在更真实的场景中，我们可以想到一个超类`User`和两个子类`Moderator`和`ChatMember`。这两个子类可以一起存储在一个定义为`List<out User>`的列表中。

### 逆变

但是，如果我们只想对那些在我们的场景中拥有一定程度的权利和责任的成员进行操作，那该怎么办呢？

这就是我们想要设定下限的地方。更具体地说，当使用语法`Stack<in T>`时，我们只能操作最多属于类型`T`的对象。

```
val superUsersList: MutableList<in Moderator> = mutableListOf()
```

使用上面的语法，我们创建了一个只接受类型为`Moderator`及以上的对象的列表(比如`User`，`User`的超类型，如果有的话，以此类推)。

这里有一个更有趣的科特林矛盾的例子:

```
val userComparator: Comparator<User> = object: Comparator<User> {
  override fun compare(firstUser: User, secondUser: User): Int {
    return firstUser.rank - secondUser.rank
  }
}
val moderatorComparator: Comparator<in Moderator> = userComparator
```

上面的语法是正确的。我们正在做的是定义一个可以用于任何类型用户的比较器。然后，我们声明一个仅适用于仲裁者的比较器，并为其指定`users`比较器。这是可以接受的，因为`Moderator`是`User`的子类型。

这种情况是如何逆变的？`userCompare`比较器专门研究一个超类，而仲裁比较器是一个子类，可以根据它的超类赋予一个值。

这些概念在 Java 中的等价形式如下:

*   `List<out T>`在科特林是`List<? extends T>`在爪哇
*   `List<in T>`在科特林是`List<? super T>`在爪哇

### 不变性

不变性很容易理解:基本上，你用没有`in`或`out`关键字的泛型类型定义的每个类都被认为是不变的。这是因为使用泛型创建的类型之间没有关系。

让我们看一个例子来理清事情:

```
open class Animal

class Dog: Animal()

val animals: MutableList<Animal> = mutableListOf()
val dogs: MutableList<Dog> = mutableListOf()
```

在上面的例子中，我们看到`Dog`和`Animal`之间有一个清晰的关系:前者是后者的一个子类型。然而，我们不能说这两个列表变量的类型是相同的。这两者之间没有关系。因此，我们可以说`List`在其类型参数上是不变的。

默认情况下，所有 Kotlin 泛型类型都是不变的。例如，列表是不变的——正如我们在上面看到的。`in`和`out`关键字的目的是给一种语言引入变化，而这种语言的泛型类型不允许这样做。

## 限制泛型的使用

当在 Kotlin 中使用泛型时，我们还必须避免以可能导致我们出错的方式误用我们的方法和类。我们必须使用`in`和`out`来为我们的类型强加声明位置差异。

在某些情况下，我们必须在方法定义中使用泛型，这样传递给它们的参数将遵守一组先决条件。这些先决条件确保我们的代码可以实际运行。让我们来看一个例子:

```
open class User

class Moderator: User()

class ChatMember: User()
```

假设我们想根据一个标准(例如，他们的年龄)对我们的用户进行分类。我们的`User`类有一个`age`字段。但是我们如何为它们创建一个排序函数呢？这很容易，但是我们的用户必须实现`Comparable`接口。

更具体地说，我们的`User`类将扩展`Comparable`接口，并且它将实现`compareTo`方法。这样，我们确保了一个`User`对象知道如何与另一个用户进行比较。

```
fun <T: Comparable<T>> sort(list: List<T>): List<T> {
    return list.sorted()
}
```

从上面的函数声明中，我们知道我们可以在包含实现了`Comparable`接口的类的对象实例化的列表上严格使用`sort`方法。

如果我们在`Animal`的子类型上调用`sort`方法，编译器会抛出一个错误。然而，它将与`User`类一起工作，因为它实现了`compareTo`方法。

## Kotlin 中的类型擦除

有趣的是，正如 Java **，**一样，Kotlin 在编译我们的代码时会执行类型擦除。这意味着它首先检查我们的类型，要么确认我们正确地使用了它们，要么抛出错误，告诉我们下次做得更好。之后，它从我们的泛型类型中剥离类型信息。

编译器希望确保类型在运行时对我们不可用。这就是以下代码无法编译的原因:

```
class SimpleClass {

    fun doSomething(list: List<String>): Int {
...
    }

    fun doSomething(list: List<Int>): Int {
    ...
    }
}

fun main() {
    val obj = SimpleClass()
}
```

这是因为代码编译正确，两个方法实际上有不同的方法签名。然而，编译时的类型擦除去掉了我们用来声明列表的`String`和`Int`类型。

在运行时，我们只知道我们有两个列表，而不知道这两个列表中的对象是什么类型。从我们得到的错误中可以清楚地看到这个结果:

```
Exception in thread "main" java.lang.ClassFormatError: Duplicate method name "doSomething" with signature "(Ljava.util.List;)I" in class file SimpleClass
```

当编写我们的代码时，值得记住的是，类型擦除将在编译时发生。如果你真的想做我们在上面代码中做的事情，你需要在我们的方法上使用`@JvmName`注释:

```
@JvmName("doSomethingString") 
fun doSomething(list: List<String>): Int {
...
}

@JvmName("doSomethingInt")  
fun doSomething(list: List<Int>): Int {
...
}
```

## 结论

为了理解 Kotlin 泛型，我们在本文中介绍了几件事情。

我们首先阐明了在 Kotlin(和任何面向对象语言)中工作时类型和类之间的区别。随后，我们介绍了泛型的概念及其用途。

为了更深入地研究 Kotlin 泛型，我们查看了一些定义，并附有示例，向我们展示了与 Java(一种非常相似的语言)相比，Kotlin 如何使用和实现泛型。

我们还了解了 Kotlin 中的方差、协方差、逆变和不变性，并通过关键字`in`和`out`了解了如何(以及何时)在我们的项目中应用这些概念。

本文的关键要点是，为了保持代码的简单性、可维护性、健壮性和可伸缩性，可以在代码中使用泛型。我们确保我们的解决方案在需要时尽可能通用——同样重要的是，不要试图让一切通用化，从而使我们的生活变得复杂。

有时这种做法会使一切更难遵循和付诸实践，所以如果泛型不能给我们带来真正的价值，就不值得使用。

通过在 Kotlin 中使用泛型，我们避免了使用强制转换，并且我们在编译时而不是运行时捕获错误。编译器确保我们在执行类型擦除之前正确使用我们的类型。

我希望这对您有所帮助，并且澄清了与 Kotlin 泛型相关的概念。非常感谢你的阅读！

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)