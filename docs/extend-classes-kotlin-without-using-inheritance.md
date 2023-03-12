# 如何在不使用继承的情况下扩展 Kotlin 中的类

> 原文：<https://blog.logrocket.com/extend-classes-kotlin-without-using-inheritance/>

我们都使用过继承的过程，即面向对象编程(OOP)的概念，其中任何给定的类都可以从另一个类继承所有的变量字段和功能。

继承属性的类称为子类，而提供基线属性的类称为父类。这种模式在 Android 应用程序中随处可见。

我们所有的活动类都不可避免地扩展了超类`Activity`来覆盖和操作它的一些最基本的功能，比如它的生命周期。

随着 Kotlin 的到来，我们接触到了一些以前 Java 中没有的新思想，例如 Kotlin 扩展，它提供了用新功能扩展类的能力，而不必继承或继承它，正如 [Kotlin 的官方文档](https://kotlinlang.org/docs/extensions.html)所解释的。

因为经典继承和当代 Kotlin 扩展提供了相似的性质，所以很难决定利用这些工具中的哪一个来解决手头的问题。这就是为什么这些概念通常是并排对比的。

在本教程中，我们将比较和对比继承和 Kotlin 扩展，以了解各自的优缺点。以下是我们将要介绍的内容:

## Kotlin 中的常规继承是什么？

让我们使用`Activity`类来扩展我们之前的例子。传统上，Java 中的继承是用关键字`extends`来表示的，这使得读者可以非常清楚地看到发生了什么:

```
public class MyActivity extends Activity {
    ...

```

如今在 Kotlin 中，继承模式语法不像以前那样明显了:

```
class MyActivity() : Activity() {
    ...

```

默认情况下，正如 [Kotlin 官方文档](https://kotlinlang.org/docs/inheritance.html)所描述的，Kotlin 中的所有类都从一个公共超类`Any`继承，该超类是所有没有显式声明的类的默认父类。

`Any`有三个所有类都继承的主要方法:`equals(), hasCode(), toString()`。所有的 Kotlin 类都是 final，所以它们不能被继承。要使一个类可继承，关键字`open`需要出现在类签名的开头，这也使它们成为非最终的。

```
open class Activity() { ... }

class MyActivity() : Activity() {
    ...

```

继承还允许子类从父类访问所有受保护的字段、函数和变量，这些通常对所有外部调用方是隐藏的。如果访问还不够，子类还可以覆盖超类中的函数或变量，只要它们对子类可见并且是非 final 的。

```
open class Activity() { ... }

class MyActivity(): Activity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        ...
    }
    ...
}

```

有关继承的更多详细信息，请查看 Java 的官方文档中关于 OOP 继承的更传统的方法，或者查看[科特林关于该主题的更现代的方法](https://kotlinlang.org/docs/inheritance.html#derived-class-initialization-order)。

## 什么是 Kotlin 扩展？

Kotlin 扩展允许我们扩展一个类，而不需要继承，或者不使用支持这种功能的设计模式，例如[装饰设计模式](https://sourcemaking.com/design_patterns/decorator)。

我们可以扩展各种类的功能，包括最终类、第三方库类等。，而不需要创建子类。因为我们没有对基类进行子类化，所以扩展函数只能访问公共字段，而不像常规继承类那样保护公共字段。

从扩展中添加的功能是静态解析的，可以像任何其他常规方法一样正常调用，也可以从已扩展的类的任何实例中调用。

以下是我们在每个 Android 应用程序中执行的一个非常常见的功能示例:

```
fun Activity.requestPermission(permission: String, requestCode: Int) {
    ActivityCompat.requestPermissions(this, arrayOf(permission), requestCode)
}

```

请求权限通常需要一个`Activity`实例，这是将此类函数作为扩展函数添加到通用`Activity`类中的绝佳机会。它可以从任何特定的`Activity`类实现中直接调用。

```
open class Activity() { ... }

fun Activity.requestPermission(permission: String, requestCode: Int) {
    ActivityCompat.requestPermissions(this, arrayOf(permission), requestCode)
}

class MyActivity: Activity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        ...
        requestPermission(
            permission = Manifest.permission.ACCESS_FINE_LOCATION,
            requestCode = 2022
    }
    ...
}

```

通过扩展一个类，我们并没有在其中插入新的属性。相反，我们正在创建新的函数，可以使用点符号(.)在这种类型的变量上。

新的 Kotlin 扩展范例还引入了扩展变量，其工作方式类似于扩展函数，只是因为我们没有对基类的完全可见性或访问权，所以扩展变量不能完全由基类备份。他们也不能从基类访问受保护的字段。

为了更深入地分析 Kotlin 扩展可以提供的可能性范围，[查看我的 LogRocketeer 同事关于这个主题的文章](https://blog.logrocket.com/everything-you-need-to-know-about-kotlin-extensions/)。

## 继承与 Kotlin 扩展

让我们比较和对比一下它们之间的主要区别。

### 在 Kotlin 中修改类的类型

我们将从每个概念可以修改的类的类型开始。虽然继承只能继承非最终类的属性，即 Kotlin 中的开放类，但 Kotlin 扩展可以为所有类型的类添加功能，而不管它们的“开放性”

```
open class InheritableClass() { … }

```

### 将属性扩展到类中

Kotlin 扩展可以将属性扩展到类中，而不需要子类化。当谈到基类的可访问性时，继承的子类可以从父类访问所有受保护的变量，而扩展不能以同样的方式访问其基类的受保护字段。

```
open class InheritableClass() { … } // This class can be inherited from
class InheritingClass : InheritableClass() { … } // This class is inheriting, but may not be inherited from
fun InheritingClass.someExtension() { … } // Extension may happen in non-open classes

```

### 添加新变量

尽管子类可以向父类的对象实例声明中添加新变量，但 Kotlin 扩展不能添加使用扩展变量备份的变量，因为这些变量只能静态处理。

```
open class InheritableClass() { … }
class InheritingClass : InheritableClass() { … }

```

下面是一个比较表，从结构上更直观地展示了这些差异:

|  | **添加功能** | **添加功能而不创建子类** | **扩展最终课程(非开放)** | **添加带有支持字段的变量** | **访问受保护的字段和功能** |
| *Inheritance* | ✅ | ❌ | ❌ | ✅ | ✅ |
| *科特林扩展* | ✅ | ✅ | ✅ | ❌ | ❌ |

## 结论

当然，不同的工具和概念适用于不同的情况。Kotlin 扩展非常适合为任何给定的类创建快速访问功能，不管它来自哪里。它们可以很好地替代旧的 Java 概念中的`utility`类，因为我们现在可以简单地添加新的函数，这些函数很容易被发现和利用，而不需要太多的样板代码。

另一方面，每当我们需要创建相关类的层次结构时，使用继承是理想的，因为它允许自然分组，这要归功于它在这种层次结构的成员之间创建的父子关系。它还允许继承类覆盖父类以前的功能，每当我们需要相似的对象以稍微不同的方式操作时，这是非常有效的。

继承和扩展各有利弊。我们可能更喜欢其中的一个，这取决于你正在做的项目的类型。我希望这篇概述能帮助您更好地理解 Kotlin 扩展和继承。感谢阅读！

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)