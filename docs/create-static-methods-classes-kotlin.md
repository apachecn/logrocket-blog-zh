# 如何在 Kotlin 中创建静态方法和类

> 原文：<https://blog.logrocket.com/create-static-methods-classes-kotlin/>

Kotlin 和 Java 中面向对象模型的一个关键区别是静态方法和类的定义。一般来说，静态类不需要被实例化就可以使用。类似地，要调用一个静态方法，我们只需要知道它的名字，而不需要实例化一个定义了这样一个方法的类的对象。

在 Java 中，类可以很容易地声明静态字段或方法。然而，静态类的定义有点复杂。另一方面，Kotlin 通过诸如包级函数、`object`和伴随函数`object`等原生构造极大地简化了工作。

在本文中，我们首先要看一下声明静态类和方法的遗留 Java 方式。其次，我们将看到如何在科特林用更少的努力实现同样的事情。然后，我们将比较 Kotlin 方法在代码可重用性方面的优势。

目录:

## Java 中的静态类和方法

在面向对象编程(OOP)中，静态方法和字段对于建模不需要绑定到类实例的公共值或操作非常有用。例如，`Math`类包含几个静态字段和方法，用于公共常数(如 pi)和数学运算(如 max 和 min):

```
public final class Math {
    public static final double E = 2.7182818284590452354;
    public static final double PI = 3.14159265358979323846;

    public static int max(int a, int b) { ... }
    public static int min(int a, int b) { ... }
}
```

另一方面，静态类更难定义。在 Java 中，只有嵌套类(即在另一个类中定义的类)可以声明为静态的。它们不需要对外部类(声明它们的类)的引用。因此，如果没有外部类的实例，我们可能无法实例化非静态嵌套类，但是静态类是独立的。

此外，类装入器在静态类第一次被使用时装入它们的代码，而不是在装入封闭类时装入。这允许我们减少应用程序的内存占用。

例如，我们可能希望使用静态类来实现线程安全的单例类，而无需付出同步`getInstance`方法的代价:

```
public class Singleton {
    private Singleton(){ }       

    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }

    // …
}
```

在上面的例子中，使用一个静态类来保存对 singleton 实例的引用给了我们一些很好的属性:

*   类加载器只在第一次被访问时加载`SingletonHolder`(通过`Singleton::getInstance()`)；
*   当一个 Java 类被加载时，它的所有静态属性都被初始化。因此，`SingletonHolder::INSTANCE`在第一次使用之前被实例化；
*   `SingletonHolder::INSTANCE`可以被声明为`final`，即使它是惰性初始化的；
*   类加载器本身是线程安全的，这使得前两个属性是线程安全的

除了性能原因之外，静态类通常用于提高代码的可读性和可维护性，因为我们可以使用它们来将组件移动到更靠近它们被使用的地方。

## Kotlin 中的静态类

作为 Java，Kotlin 允许我们在代码中定义嵌套类。然而，在 Kotlin 中，嵌套类在默认情况下是静态的。也就是说，默认情况下，Kotlin 中的嵌套类不包含对封闭类的引用:

```
class Car(val model: String) {
    class Engine(val fuel: String)
}

fun main() {
    val engine = Car.Engine("Gasoline")
    val car = Car("SomeModel")

    println(engine.fuel)
    println(car.model)
}
```

在上面的例子中，我们在类`Car`中定义了一个嵌套类`Engine`。正如我们所看到的，我们可以分别实例化`Car`和`Engine`。特别是，我们创建了一个`Engine`的实例，而没有提供对一个`Car`对象的引用。`Engine`在`Car`内部定义的唯一线索是它的限定名`Car.Engine`。上面的例子打印了`Gasoline`和`SomeModel`。

Kotlin 中的静态类可以访问封闭类的伴生对象的属性。我们将在下面看到更多关于同伴的内容。

如果我们想在 Kotlin 中定义一个非静态嵌套类，我们必须将其声明为`inner`:

```
class Car(val model: String) {
    inner class Engine(val fuel: String) {
                val forModel = [email protected]
    }
}

fun main() {
    val engine = Car("SomeModel").Engine("Gasoline")
    println("${engine.forModel} - ${engine.fuel}")
}
```

既然`Engine`被标记为`inner`，我们必须创建一个`Car`的实例，并用它来实例化`Engine`。从`Engine`内部，我们可以使用`[[email protected]](/cdn-cgi/l/email-protection)`引用外部对象。该示例打印了`SomeModel - Gasoline`。

与 Java 类似，嵌套类也可以在封闭类的方法范围内声明。在这种情况下，新类将是局部类型。

Kotlin 方法的主要好处是，默认情况下，它限制了内存泄漏的风险。在 Java 中，人们更容易忽略这样一个事实，即给定的嵌套类持有对封闭类的引用。另一方面，在 Kotlin 中，默认情况下不存在这样的引用。

是使用内部类还是静态类很大程度上取决于我们对领域建模的方式。当然，静态类允许更大的代码可重用性，因为我们不需要实例化封闭类，同时让我们定义(可能)彼此靠近的依赖组件。

## Kotlin 中的静态方法

Kotlin 极大地简化了我们定义静态方法或变量的方式。特别是，它使用(companion) `object` s 和包级函数来实现。

### 包级函数

Kotlin 不仅仅是一种面向对象的语言，因为它也支持函数式编程范式:这就是包级函数的来源。顾名思义，它们是不属于给定类的函数(或成员),而是在包中定义的。通常，它们是独立于任何其他类的实用函数。

例如，我们可以用它们来实现方便的函数来初始化一个类。假设我们有一个名为`Point`的类来模拟笛卡尔平面中的一个点:

```
package com.logrocket.blog
```

```
class Point(val x: Int, val y: Int) {
    override fun toString(): String = "Point($x, $y)"
}
```

然后，在不同的包中，我们可以定义以下函数:

```
// In file factory.kt
package com.logrocket.blog.utils

import com.logrocket.blog.Point

val centerPoint = Point(x = 0, y = 0)

fun onXAxis(x: Int) = Point(x, y = 0)

fun onYAxis(y: Int) = Point(x = 0, y)
```

然后，我们可以通过导入来使用上面的函数和值:

```
package com.logrocket.blog

import com.logrocket.blog.utils.centerPoint
import com.logrocket.blog.utils.onXAxis

fun main() {
    val point = onXAxis(5)
    println(centerPoint)
    println(point)
}
```

上面的主函数打印字符串`Point(0, 0)`和`Point(5, 0)`。

注意我们是如何在`com.logrocket.blog.utils`包中定义两个包级函数的，`onXAxis`和`onYAxis`。我们还定义了一个包级别的值`centerPoint`。函数和值都可以在不引用任何封闭类的情况下访问，就像我们在 Java 中所做的那样:我们只需要导入它们。

包级函数和值是 Java 中静态字段和方法的语法糖。Kotlin 编译器所做的是生成一个以 Kotlin 文件命名的 Java 类，其中包含静态方法和字段。例如，`com.logrocket.blog.utils.factory.kt`中的函数将被编译成一个名为`com.logrocket.blog.utils.FactoryKt`的类(其中类名是使用文件名和 PascalCase 中的`Kt,`构建的):

```
package com.logrocket.blog.utils

// Generated class
class FactoryKt {
    public static Point centerPoint = new Point(0, 0);

    public static Point onXAxis(int x) {
            return new Point(x, 0);
    }

    public static Point onYAxis(int y) {
            return new Point(0, y);
    }
}
```

如果我们想改变生成的 Java 类的名称，我们可以使用`@JvmName`注释。例如，如果我们将注释`@file:JvmName("PointFactory")`放在 *factory.kt* 的开头，那么生成的类将被命名为`PointFactoryKt`而不是`FactoryKt`。这样的注释必须出现在`package`指令之前。

最后，如果我们希望更多的实用函数被编译到同一个生成的 Java 类中，或者如果我们已经有了一个名为`pointfactory.kt`的文件，我们可以使用`@JvmMultifileClass`注释。这样，编译器将生成一个具有指定名称的 Java facade 类，以及所有 Kotlin 文件中具有相同`JvmName`的所有声明。

### 目标

通过在 Kotlin 中声明一个`object`,我们定义了一个 singleton，即一个只有一个实例的类。这样的实例是在第一次使用时以线程安全的方式延迟创建的。

例如，我们可以定义下面的`object`来对我们上面定义的函数和值进行分组:

```
object PointFactory {
    val center = Point(x = 0, y = 0)
    fun onXAxis(x: Int) = Point(x, y = 0)
    fun onYAxis(y: Int) = Point(x = 0, y)
}
```

然后，与之前不同，我们必须指定`object`的名称来访问它的功能。换句话说，一个`object`定义了一个范围:

```
val point = PointFactory.onYAxis(5)

```

因为每个 Kotlin `object`只有一个实例，所以`object`的限定名足以访问它的成员。这与只包含静态方法或变量的 Java 类相似，但略有不同。在后一种情况下，我们可以实例化那个 Java 类，次数不限(假设构造函数不是`private`)。在这种情况下，对于类的每个不同实例，静态变量都是相同的。另一方面，对于 Kotlin `object` s，我们只有一个实例。

### 伴随物体

在上面的例子中，`PointFactory`与`Point`类紧密相关，因为它包含几个实例化一个点的方法。对于这种情况，我们可以让它成为一个同伴`object`，让这种紧密耦合更加明确:

```
class Point(val x: Int, val y: Int) {
    companion object {
                val center = Point(x = 0, y = 0)
                fun onXAxis(x: Int) = Point(x, y = 0)
                fun onYAxis(y: Int) = Point(x = 0, y)
    }

    override fun toString(): String = "Point($x, $y)"
}
```

有了 companion `object` s，我们可以声明方法并将它们绑定到给定的类，而不是它的实例。至于“正常”的“T1”，同伴“T2”都是单身。因此，我们可以通过指定类名来引用函数:

```
val point = Point.onYAxis(5)
```

### 比较

Kotlin 为我们提供了三种不同的解决方案来定义静态方法或字段。

包级函数和值是最惯用的方式。通常不需要[在一个类中限定实用方法](https://blog.logrocket.com/comparing-kotlin-scope-functions/)的范围。在这种情况下，包级别的成员是一个很好的选择，可以提高代码的可重用性。事实上，大多数标准库都是使用它们实现的。

然而，`object` s 和同伴`object` s 确实有一些优点。例如，它们允许更好地限定方法和字段的范围。包级成员的主要缺点之一是它们污染了大多数 ide 中可用的自动完成建议，使得选择正确的函数更加困难。对象的作用域解决了这个问题。

## 结论

严格地说，在纯面向对象的编程思想中，一切都最好在类内部定义。然而，正如我们在上面看到的，我们经常需要不同的方法来放置在现有的类中。例如，当实用程序方法操作一个类但不表示该类的行为时，就会发生这种情况。

在像 Java 这样的语言中，通常的做法是定义`Utils`或`Helper`类，这些类充满了在某个类中作用域不同的方法。这很容易失去控制，并导致具有不同职责和异构方法的类非常难以阅读、维护和重用。

另一方面，Kotlin 不仅仅是面向对象的语言。它支持其他编程范例，比如函数式编程范例。因此，它不像 Java 那样严格地采用面向对象，例如，允许我们定义不依赖于任何类的函数。

一方面，这提高了代码的可重用性和可维护性。此外，我们可以使用包结构和可见性关键字来选择代码库的哪一部分可以使用给定的函数或`object`。更好的是，有了 companion `object` s，我们可以定义尽可能接近它所操作的类的实用程序代码。另一方面，我们应该注意科特林方法的自由性和灵活性。例如，没有什么可以阻止我们定义一个可变的包级变量，本质上是一个全局变量，这可能是非常有害的。

正如现代编程语言中常见的那样，我们有许多方法来对同一事物建模并获得相同的结果。因此，找出什么是正确的结构并恰当地使用它总是一个经验和感觉的问题。

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)