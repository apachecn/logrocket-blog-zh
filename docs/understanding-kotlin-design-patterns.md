# 在 Kotlin - LogRocket 博客中理解设计模式

> 原文：<https://blog.logrocket.com/understanding-kotlin-design-patterns/>

如果你在这里，那么你可能听说过科特林。在本文中，我们将回顾 Kotlin 是什么，它为什么有用，以及什么是设计模式。

然后，我们将看看最重要和最广泛使用的 Kotlin 设计模式(如 provider 模式和许多其他模式)，并附有代码片段和示例用例。

我们将涵盖:

## Java 谁？Kotlin 如何提供更简单的编码方式

JetBrains 的人们创造了 Kotlin 编程语言，以使他们的生活更容易。他们一直在使用 Java，当时这种语言降低了他们的生产力。解决方案是 Kotlin，一种开源的静态类型编程语言。

Kotlin 是一种面向对象的编程语言，它也使用函数式编程，大大减少了样板代码的数量。它有助于提高程序员的生产力，并提供了一种开发本机应用程序的现代方法。

如今，在 Android 开发中， [Kotlin 比 Java 更受青睐](https://blog.logrocket.com/kotlin-vs-java-for-android-development/)。

## 科特林设计模式:不是所有的英雄都穿斗篷

软件开发需要大量的创造性和跳出框框的思考，因为你经常会面临需要独特解决方案的复杂问题。但是每次你试图解决某件事情的时候都要重新发明轮子是不可行的——也没有必要。

在软件设计领域，你会经常面对其他人曾经面对过的情况。足够幸运的是，我们现在有一些可重复使用的解决方案来解决这些重复出现的问题。

这些范例以前已经被使用和测试过。剩下要做的就是清楚地理解问题，以确定适合您需求的合适的设计模式。

## Kotlin 设计模式的类型

有三种主要的设计模式:创造型、结构型和行为型。这些类别中的每一个都回答了关于我们的类以及我们如何使用它们的不同问题。

在接下来的几节中，我们将深入讨论每个设计模式，并理解如何使用 Kotlin 特性来实现这些模式。我们还将回顾每个类别中最流行的设计模式:我们将定义它们，涵盖一些示例用例，并查看一些代码。

## 什么是创造性的设计模式？

顾名思义，这个类别的模式关注于对象是如何被创建的。使用这样的模式将确保我们的代码是灵活的和可重用的。

Kotlin 中有多种创造性的设计模式，但是在这一节中，我们将重点介绍工厂方法、抽象工厂方法(你也可以[参考微软的提供者模型](https://en.wikipedia.org/wiki/Provider_model))、单例模式和构建器模式。

### 工厂和抽象工厂(提供者模型)方法

这两种创建模式有一些相似之处，但是它们的实现方式不同，并且有不同的用例。

工厂方法模式基于为超类的初始化步骤定义抽象方法。这种 Kotlin 设计模式允许各个子类定义它们的初始化和创建方式。

与其他创建模式(如 builder 模式)相比，工厂方法模式不需要编写单独的类，只需要一个或多个包含初始化逻辑的附加方法。

这种模式的一个明显例子是众所周知的 Android 的`RecyclerView.Adapter`类，特别是它的`onCreateViewHolder()`方法。该方法基于特定列表元素的内容实例化一个新的`ViewHolder`，如下所示:

```
class MyRecyclerViewAdapter : RecyclerView.Adapter<RecyclerView.ViewHolder>() {
   override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): RecyclerView.ViewHolder {
       return when (viewType) {
           0 -> HeaderViewHolder()
           1 -> SeparatorViewHolder()
           else -> ContentViewHolder()
       }
   }
}
```

在这个例子中，`onCreateViewHolder()`方法是在`RecyclerView.Adapter` 超类上定义的，这个超类又被适配器的内部
代码使用。

通过在超类中抽象方法，适配器允许它的实现子类根据它们的需要为它们的视图持有者定义初始化逻辑。

另一方面，Kotlin 中的抽象工厂方法——像微软的提供者模型一样，[使用一个](https://docs.microsoft.com/en-us/dotnet/api/system.configuration.provider.providerbase?view=dotnet-plat-ext-6.0) `[ProviderBase](https://docs.microsoft.com/en-us/dotnet/api/system.configuration.provider.providerbase?view=dotnet-plat-ext-6.0)` [类](https://docs.microsoft.com/en-us/dotnet/api/system.configuration.provider.providerbase?view=dotnet-plat-ext-6.0)——依赖于创建一个接口，允许一系列密切相关的类被实例化。

一个例子是为不同制造商生产汽车零件的工厂:

```
abstract class CarPartsFactory { 
    fun buildEngine(/* engine parts */): Engine
    fun buildChassis(/* chassis materials */): Chassis
}

class CarPartsFactoryProvider { 
  inline fun <reified M : Manufacturer> createFactory(): CarPartsFactory { 
    return when (M::class) { 
        Manufacturer.Audi -> AudiPartsFactory()
        Manufacturer.Toyota -> ToyotaPartsFactory()
        }
    }
}
class AudiPartsFactory: CarPartsFactory() { 
    override fun buildEngine(...): AudiEngine
    override fun buildChassis(...): AudiChassis
}

class ToyotaPartsFactory: CarPartsFactory() { 
    override fun buildEngine(...): ToyotaEngine
    override fun buildChassis(...): ToyotaChassis
}

// Usage:
val factoryProvider = CarPartsFactoryProvider()
val partsFactory = factoryProvider.createFactory<Manufacturer.Toyota>()
val engine = partsFactory.buildEngine()
```

在这个例子中，接口充当了独立工厂应该制造什么样的汽车部件以及使用什么材料(参数)的蓝图。然后，这些工厂(子类)将根据特定于这些制造商的要求和流程来制造零件。

### 单一设计模式

单一模式可能是最著名的设计模式之一。大多数使用 OOP 的开发人员以前都遇到过这种设计模式。然而，[这也是最被误用和误解的模式之一](https://blog.logrocket.com/youre-wrong-about-singletons/)。我们将在这一部分的最后更详细地讨论原因。

这种设计模式使开发人员能够定义一个在整个项目中只实例化一次的类。使用它的每个地方都将使用同一个实例，从而减少内存使用并确保一致性。

#### 什么时候我们需要使用单体设计模式？

一般来说，当处理多线程应用程序(例如日志、缓存)时，当确保可靠的单一事实来源很重要时，我们会使用单体设计模式。

根据定义，一个`Singleton`类只会被实例化一次，要么是急切地(当类被加载时)，要么是懒惰地(当它第一次被访问时)。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

下面的例子展示了在 Java 中定义一个`Singleton`类的最简单的方法之一。我们关注 Java 是因为它比 Kotlin 更具表现力，这让我们理解了使模式工作的概念:

```
// Java implementation
public class Singleton { 
     private static Singleton instance = null;

     private Singleton() { 
   // Initialization code
     }
     public static Singleton getInstance() { 
         if (instance == null) { 
           instance = Singleton()
         }

   return instance;
     }
     // Class implementation...
}
```

注意，`private`构造函数和静态`getInstance()`方法确保类直接负责它何时被实例化以及如何被访问。

现在，让我们看看如何在 Kotlin 中实现单例模式:

```
// Kotlin implementation
object Singleton { 
    // class implementation
}
```

在 Kotlin 中，实现一个`Singleton`对象是一个直接包含的特性，开箱即用，线程安全。模式也在第一次访问时被实例化，类似于上面的 Java 实现。

你可以在官方文档中阅读更多关于 Kotlin 对象表达式和声明的内容。请注意，虽然这个特性看起来类似于 Kotlin 中的伴随对象，但伴随对象更多地被认为是定义 Kotlin 中的静态字段和方法。

单体可以有很多用例，包括:

*   维护系统内部的全局状态
*   实现其他设计模式，如外观或不同的工厂模式
*   提供了一种直接访问全局数据的方式

#### 为什么单例模式被认为被广泛滥用？

不仅单例模式被广泛误用和误解，而且在各种场合，它甚至被称为反模式。之所以这样，是因为它的大部分好处也可以被认为是它最大的弊端。

例如，这种设计模式使得向应用程序添加全局状态变得非常容易，而众所周知，如果代码库变得越来越大，就很难维护全局状态。

此外，它可以在任何地方、任何时间被访问的事实使得理解系统的依赖层次更加困难。如果存在对单例方法和字段的大量依赖，简单地移动一些类或将一个实现换成另一个实现会变得非常麻烦。

任何使用过包含大量全局或静态组件的代码库的人都知道为什么这是一个问题。

接下来，由于`Singleton`类直接负责创建、维护和公开它的单一状态，这打破了[单一责任原则](https://stackify.com/solid-design-principles/)。

最后，由于在运行时每个`Singleton`类只能有一个对象实例，测试也变得更加困难。当一个不同的类依赖于 Singleton 的一个字段或方法时，这两个组件将紧密耦合。

因为`Singleton`方法不能(轻易地)被模仿或替换成假的实现，所以完全隔离地对依赖类进行单元测试是不可能的。

虽然将每一个高级组件都定义为单例组件看起来很有吸引力，但是我们不鼓励这样做。有时利大于弊，所以我们不应该过度使用这种模式，并且您应该始终确保您和您的团队都理解其中的含义。

### 生成器设计模式

构建器模式对于需要复杂初始化逻辑或者必须根据输入参数高度可配置的类特别有用。

例如，如果我们要实现一个`Car`类，它将组成汽车的所有不同部分作为构造函数的参数，那么这个构造函数的参数列表可能会相当长。某些参数是可选的，而其他参数是强制的，这只会增加复杂性。

在 Java 中，这意味着为每个可能的输入参数组合编写一个单独的构造函数方法，更不用说在实例化期间还需要组装 car 的单独而繁琐的初始化逻辑了。这就是构建器模式的用处。

当使用构建器设计模式时，我们必须为正在讨论的类定义一个`Builder`类(在我们的例子中是`Car`)，它将负责分别获取所有参数，并实际实例化结果对象:

```
class Car(val engine: Engine, val turbine: Turbine?, val wheels: List<Wheel>, ...) {

   class Builder {
       private lateinit var engine: Engine
       private var turbine: Turbine? = null
       private val wheels: MutableList<Wheel> = mutableListOf()
       fun setEngine(engine: Engine): Builder {
           this.engine = engine
           return this
       }
       fun setTurbine(turbine: Turbine): Builder {
           this.turbine = turbine
           return this
       }
       fun addWheels(wheels: List<Wheel>): Builder {
           this.wheels.addAll(wheels)
           return this
       }
       fun build(): Car {
           require(engine.isInitialized) { "The car needs an engine" }
           require(wheels.size < 4) { "The car needs at least 4 wheels" }
           return Car(engine, turbine, wheels)
       }
   }
}
```

如您所见，`Builder`直接负责处理初始化逻辑，同时还处理不同的配置(可选和强制参数)和前提条件(最小轮数)。

请注意，所有配置方法都返回构建器的当前实例。这样做是为了通过以下面的
方式链接这些方法来轻松创建对象:

```
val car = Car.Builder()
    .setEngine(...)
    .setTurbine(...)
    .addWheels(...)
    .build()
```

虽然 builder 设计模式对于 Java 等语言来说是一个非常强大的工具，但我们看到它在 Kotlin 中很少使用，因为可选参数已经作为一种语言特性存在。这样，配置和初始化逻辑都可以用一个构造函数直接处理，如下所示:

```
data class Car(val engine: Engine, val turbine: Turbine? = null, val wheels: List<Wheel>) {
    init { /* Validation logic */ }
}
```

这对于简单的对象来说很好，比如`data classes`，其复杂性主要来自于可选参数。

对于具有更复杂逻辑的更大的类，强烈建议将初始化代码提取到一个单独的类中，比如一个`Builder`。

这有助于维护单一责任原则，因为类不再负责如何创建和验证它。它还允许更优雅地对初始化逻辑进行单元测试。

## 什么是结构设计模式？

我们需要结构设计模式，因为我们将在项目中定义某些结构。我们希望能够正确识别类和对象之间的关系，以简化项目结构的方式组合它们。

在下面的小节中，我们将详细介绍适配器设计模式和装饰器设计模式，并简要回顾一些外观设计模式的用例。

### 适配器设计模式

这种模式的名称暗示了它所完成的工作:它填补了两个不兼容接口之间的空白，使它们能够协同工作，就像你在欧洲插座上使用美国手机充电器时使用的适配器一样。

在软件开发中，当需要将一些数据转换成不同的格式时，通常会使用适配器。例如，您从后端接收的数据可能需要在您的存储库/UI 中以不同的方式表示。

当您需要使用默认情况下不兼容的两个类来完成操作时，适配器也很有用。

让我们看一个 Kotlin 中适配器设计模式的例子:

```
class Car(...)  {
fun move() { /* Implementation */ }
}
interface WaterVehicle { 
    fun swim()
}
class CarWaterAdapter(private val adaptee: Car): WaterVehicle {
    override fun swim() { 
// whatever is needed to make the car work on water
// ... might be easier to just use a boat instead
        startSwimming(adaptee)
    }
}

// Usage
val standardCar: Car = Car(...)
val waterCar: WaterVehicle = CarWaterAdapter(standardCar)
waterCar.swim()
```

在上面的代码中，我们实际上是在`standardCar`(即，adaptee)上创建了一个包装器。通过这样做，我们使它的功能适应不同的环境，否则它将是不兼容的(例如，一场水上比赛)。

### 装饰设计模式

装饰器设计模式也属于结构模式的范畴，因为它允许您向现有的类分配新的行为。同样，我们通过创建具有上述行为的包装器来实现这一点。

这里值得注意的是，包装器将只实现被包装对象已经定义的方法。当创建一个合格的包装器作为装饰器时，你将在你的类中添加一些行为，而不会以任何方式改变它的结构。

```
class BasicCar {
    fun drive() { /* Move from A to B */ }
}

class OffroadCar : BasicCar {
    override fun drive() {
       initialiseDrivingMode()
       super.drive()
    }
    private fun initialiseDrivingMode() { 
       /* Configure offroad driving mode */ 
    }
}
```

在上面的例子中，我们从一辆基本型汽车开始，它在城市和维护良好的道路上行驶很好。然而，当你在森林里到处都是雪的时候，像这样的车帮不了你太多。

你并没有改变汽车的基本功能(例如，将乘客从 A 点送到 B 点)。相反，你只是增强了它的行为——换句话说，它现在可以在一条艰难的道路上带你从 A 点到 B 点，同时保证你的安全。

这里的关键是我们只做了一些配置逻辑，之后我们仍然通过调用`super.drive()`来依赖标准的驱动逻辑。

您可能认为这与适配器模式的工作方式非常相似——您不会错。设计模式通常有很多相似之处，但是一旦你弄清楚它们各自的目标是什么，以及为什么你会使用其中的任何一种，它们就会变得更加直观。

在这个例子中，适配器模式的重点是使看似不兼容的接口(例如，汽车和困难的道路)一起工作，而装饰器的目标是丰富现有基类的功能。

### 立面设计模式

我们不打算对 facade 模式进行过多的描述，但是考虑到它是如此的普遍，还是值得一提的。facade 模式的主要目标是为一组相关组件提供一个更简单、更统一的接口。

这里有一个有用的类比:汽车是一台复杂的机器，由数百个不同的零件协同工作组成。然而，我们并不直接与所有这些部件进行交互，而只是与少数几个我们可以从驾驶座接触到的部件(例如踏板、车轮、按钮)进行交互，这些部件充当了我们使用汽车的简化界面。

我们可以在软件设计中应用同样的原理。

假设您正在开发一个框架或库。通常，你会有几十个，几百个，甚至几千个具有复杂结构和交互的类。

这些库的消费者不应该关心所有这些复杂性，所以你应该定义一个简化的 API，让他们可以利用你的工作。这就是门面模式在起作用。

或者，假设一个屏幕需要计算和显示来自多个数据源的复杂信息。与其将所有这些都暴露给 UI 层，不如定义一个 facade 来连接所有这些依赖项，并以正确的格式公开数据，以便立即呈现。

对于这种情况，facade 模式是一个很好的解决方案。

## 什么是行为设计模式？

行为设计模式更关注事物的算法方面。它们为对象之间的交互和关联提供了更精确的方式，也为我们作为开发人员理解对象的职责提供了更精确的方式。

### 观察者设计模式

任何使用过反应式库(如 RxJava、LiveData、Kotlin Flow 或任何反应式 JavaScript 库)的人都应该熟悉 observer 模式。它本质上描述了两个对象之间的通信关系，其中一个是`observable`，另一个是`observer`。

这与生产者-消费者模型非常相似，在该模型中，消费者主动等待从生产者处接收输入。每次接收到新的事件或数据时，消费者将调用其预定义的方法来处理所述事件。

```
val observable: Flow<Int> = flow { 
    while (true) { 
        emit(Random.nextInt(0..1000))
        delay(100)
    }
}

val observerJob = coroutineScope.launch {
observable.collect { value -> 
println("Received value $value")
    }
}
```

在上面的例子中，我们创建了一个异步的`Flow`，它每秒钟发出一次随机整数值。在这之后，我们将这个`Flow`收集到一个从我们当前作用域启动的独立协程中，并为每个新事件定义了一个处理程序。

我们在这个例子中所做的就是打印由`Flow`接收的值。源`Flow`将在它被收集时开始发射，并且将无限期地继续，或者直到收集器的协程被取消。

Kotlin 的`Flow`基于协程，这使得它成为并发处理和编写反应式系统的非常强大的工具，但是从官方文档中可以了解到更多关于[不同类型的流，比如热流或冷流。](https://kotlinlang.org/docs/flow.html)

观察者模式是一种干净直观的反应式编写代码的方式。反应式编程一直是通过 LiveData 在 Android 上实现现代应用程序 ui 的首选方式。最近，随着 [Rx 系列库](https://blog.logrocket.com/tag/rxjs/)的加入和异步流的引入，它开始流行起来。

### 战略设计模式

当我们有一系列可以互换的相关算法或解决方案，并且我们需要决定在运行时使用哪一个时，策略模式是有用的。

我们可以通过定义算法签名的接口来抽象算法，并允许实际的实现来确定所使用的算法。

例如，假设我们对一个包含用户输入的对象有多个验证步骤。对于这个用例，我们可以定义并应用以下接口:

```
fun interface ValidationRule { 
fun validate(input: UserInput): Boolean
}
class EmailValidation: ValidationRule { 
override fun validate(input: UserInput) =  validateEmail(input.emailAddress)
}

val emailValidation = EmailValidation()
val dateValidation: ValidationRule = { userInput -> validateDate(userInput.date) }

val userInput = getUserInput()
val validationRules: List<ValidationRule> = listOf(emailValidation, dateValidation)
val isValidInput = validationRules.all { rule -> rule.validate(userInput) }
```

在运行时，我们添加到列表中的验证规则中的每个算法都将被执行。只有当所有的`validate()`方法都成功时，`isValidInput`才会为真。

注意接口定义中的关键字`fun`。这告诉编译器下面的接口是一个函数接口，这意味着它有且只有一个方法，让我们通过匿名函数方便地实现它，就像我们对`dateValidation`规则所做的那样。

## 结论

在本文中，我们回顾了 Kotlin 设计模式的类型，并了解了在 Kotlin 中何时以及如何使用一些最常用的设计模式。我希望这篇文章为您澄清了这个话题，并向您展示了如何在您的项目中应用这些模式。

你通常在项目中使用什么模式，在什么情况下使用？你还想了解其他 Kotlin 设计模式吗？如果你有任何问题，不要害怕在评论区或我的[媒体博客](https://bianca-dragomir.medium.com/)上寻求帮助。感谢您的阅读。

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