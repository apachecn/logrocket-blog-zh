# Kotlin 依赖注入:Koin vs. Hilt

> 原文：<https://blog.logrocket.com/kotlin-dependency-injection-koin-vs-hilt/>

依赖注入是一种广泛使用的技术，它允许程序员为任何类提供它的依赖，而不是让类自己获得它们。这种技术也被认为非常适合 Android 开发生态系统。正如 Android 的官方文档所建议的，依赖注入允许程序员为良好的应用程序架构打下基础，因为它具有多种优势，如代码的可重用性、易于重构以及易于测试。

由于它的许多优点，以及这种模式的解耦本质，依赖注入几乎是每个现代 Android 项目的必须。有这么多选择库和工具来实现依赖注入，或者 DI，很难决定使用什么框架。

在本文中，我们将探索两个最受欢迎的现代 Android 开发的 DI 库:[匕首之柄](https://dagger.dev/hilt/)和新人[柯恩](https://insert-koin.io/)。

向前跳:

## 柄

刀柄框架是匕首 DI 库上面的一层。更具体地说，Hilt 构建在 Dagger 2 库之上，该库目前由 Google 维护。

除了将 Dagger 实现简化到 Android 应用程序中，Hilt 的一些其他目标是标准化组件和范围集，以使 Dagger 的设置更容易，并提供一种简单的方法来为不同的构建类型提供绑定，正如其[文档](https://dagger.dev/hilt/)所解释的那样。

换句话说，使用 Hilt 本质上意味着使用 Dagger 2，它们可以同时存在于同一个项目中。让我们看看匕首之前，我们潜入剑柄。

## 匕首简史

2012 年， [Square 发布了用于快速依赖注入的 Dagger 库](https://github.com/square/dagger)。四年后，谷歌接手了这个项目，并推出了新的和改进的 Dagger 2，这个版本的库是基于 Hilt 构建的。

这个开源库是用 Java 编写的，在编译时生成普通的 Java 源代码来实现它的注入分析。

## 匕首是如何工作的？

Dagger/Hilt 使用一系列注释来指示为其依赖注入生成哪种代码。Dagger 和 Hilt 的主要区别在于，Hilt 框架会自动生成很多 Android 项目通常需要的 Dagger 设置代码，这让开发人员避免了遇到太多样板代码。

下面是如何使用`Application`对象初始化 Android 应用程序中的 Hilt 库:

```
@HiltAndroidApp
class App : Application() {
    // Don't forget to declare your Application object in the Manifest!
    ...
}

```

一旦你的 Hilt 应用程序被初始化，我们需要告诉 Hilt 哪些类可以作为依赖注入。

为此，我们需要将`@Inject`注释添加到类的构造函数中，以便 Dagger 声明它们进行注入:

```
class InjectedClass @Inject constructor(
    private val param: InjectedParam
) { ... }

class InjectedParam @Inject constructor() { ... }

```

注意，为注入而注释的类中的所有参数也需要被注释。

在注释了打算注入的类之后，您需要首先开始注释具有生命周期意识的入口点*、*或依赖容器，它们允许将依赖注入其中。

只有到那时，我们才能再次使用`@Inject`注释，根据需要带来依赖关系:

```
@AndroidEntryPoint
class MainActivity: FragmentActivity() {
    @Inject lateinit var injectedClass: InjectedClass

    override fun onCreate() {
        ...
    }
}

```

Hilt 可以做很多安卓职业的入口，包括`Application`、`BroadcastReceiver`、`View`、`Service`、`Activity`，但前提是要扩展`FragmentActivity`。`Fragment`也可以作为一个入口，但是根据 [Android codelab](https://developer.android.com/codelabs/android-hilt#4) 的说法，它要扩展 Jetpack 的`Fragment`类而不是旧的 Android 的。

### Dagger 编译时依赖

Dagger Hilt 框架是一个编译时依赖项。这意味着它生成的所有代码以及所有错误检查都发生在编译时，这会导致编译失败。这使得确定当前的 DI 策略是否有缺陷变得非常容易。

### 现代 Android 开发中的手柄(MAD)

Hilt 最大的优势在于 Google 将其整合到了他们的开发生态系统中。

Hilt 是 Android 推荐的实现依赖注入的方式，现在它和 [Android Jetpack](https://developer.android.com/jetpack?gclid=Cj0KCQjwnP-ZBhDiARIsAH3FSReWVeFGrEROLmhF6PBEXjLdXrl9rXPUBusN2fSdAa99GasQX_mkTygaAsYfEALw_wcB&gclsrc=aw.ds) 一起打包。

根据 [Android 的官方文档](https://developer.android.com/training/dependency-injection#hilt)，通过为项目中的每个 Android 类提供容器并自动管理它们的生命周期，Hilt 定义了一种在应用中实现 DI 的标准方式。

## 什么是 Koin？

Koin 是 Kotzilla 的一个开源库，几乎是匕首/刀柄的对立面。Koin 创建于 2017 年，完全用 Kotlin 编写，它提供了一种不同的实现依赖注入的方法。

Koin 实现依赖注入的方式与传统的依赖注入如此不同，以至于有人声称它根本不是依赖注入。

Koin 使用一种叫做服务定位器的模式，根据 Google 的说法，*，* [通过提供一个注册中心来工作，在那里类可以获得它们的依赖，而不是直接构造它们。同时，传统的依赖注入有另一个类为你提供依赖。](https://developer.android.com/topic/architecture#dependencies)

关于这场辩论的更多信息，请看 Elye 对这个话题的深入探讨。在本文中，当谈到 Koin 库及其用法时，我们将坚持依赖注入。

## Koin 是怎么工作的？

Koin 没有通过注释来引导自己，而是使用模块来声明所有的类或工厂，这些类或工厂将作为依赖项注入到其他类中。

下面是如何使用`Application`对象初始化 Android 应用程序的 Koin 库:

```
class App : Application() {
    // Don't forget to declare your Application object in the Manifest!
  override fun onCreate() {
    super.onCreate()
    // Start Koin
    startKoin {
      // Feed in Context
      androidContext(this)
            ...
    }
  } 
    ...
}

```

一旦 Koin 被初始化，添加和注入依赖项就很简单了。

所有的依赖项都需要存在于一个`module`对象中，然后直接提供给 Koin 实例。我们可以通过以下方式做到这一点:

```
class InjectedParam()
class InjectedClass(val subclass: InjectedParam)

val appModule = module {
    single { InjectedParam() }
    single { InjectedClass(get()) }
}

class App : Application() {
  override fun onCreate() {
    super.onCreate()
    // Start Koin
    startKoin {
      // Feed in Context
      androidContext(this)
            // Load dependencies module
            modules(appModule)
    }
  } 
    ...
}

```

一旦你的依赖项在一个模块中，你可以通过使用`inject`委托函数直接注入到你的源代码中:

```
class MainActivity : Activity() {
    val injectedClass: InjectedClass by inject()

    override fun onCreate() {
        ...
    }
}

```

### Koin 运行时依赖

不同于匕首/刀柄，Koin 是一个运行时依赖。这意味着它不仅不会在编译时生成任何代码，而且在您构建项目时也不会抱怨缺少依赖项。

相反，如果您的应用程序缺少依赖项，将会抛出一个异常，很可能会导致崩溃。

### 现代 Android 开发中的 koin(MAD)

Koin 最大的优势就是完全用 Kotlin 写的。随着 MAD 转向一个更加 Kotlinized 的生态系统，在 Kotlin 优先项目中使用 Kotlin 库有很多好处。

此外，因为它不是作为另一个库的一层而存在，所以 Koin 是非常轻量级的，不会给你的项目增加额外的编译时间。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 比较匕首和锦鲤的特点

现在我们已经研究了这两个库的背景和规范，让我们再来看看它们实现的一些细节，并比较它们以更好地理解它们的区别。

### 易用性

我觉得在比较匕首/剑柄和锦鲤的时候，第一个也是最重要的一个问题就是:哪个更好用？我们已经讨论了如何初始化和使用这两个库的最简单的例子，所以这实际上取决于个人偏好。

如果您是那种喜欢在整个项目中使用注释来更好地捕捉意图的开发人员，那么 Hilt 可能是更好的选择，这样您就可以使用这方面的开发。

然而，更广泛的观点是 Koin 比 Hilt 更容易管理。Koin 不仅使代码非常集中，而不是必须标记每个单独的对象，而且它还使用 Kotlin 最新和最棒的工具来方便访问和注入您的依赖项。如果你只是喜欢你的注释，那么 [Koin 有一个注释策略](https://insert-koin.io/docs/quickstart/android-annotations),它可以作为一种选择或者与它们的常规用法结合使用。

### 处理错误

知道你的依赖注入策略何时不完整，或者是否失败，是非常重要的。因为匕首/刀柄和 Koin 库的处理方式不同，这个任务在两个选项之间也有很大的不同。

虽然 Hilt 编译时依赖允许我们通过编译错误更快地发现错误，但 Koin 将正确构建，并且仅在错误的依赖被触发时才表达其运行时异常。

这两个库各有优缺点，因为 Hilt 的编译时分析也会导致给定应用程序的构建时间变慢。

### 对性能的影响

每个库的依赖类型对性能有不同的影响。

Hilt 的编译时分析和类生成极大地影响了应用程序的编译时间，但是所有的事情都会在构建生命周期中更早地得到排序，保持运行时性能不变。

另一方面，Koin 的运行时依赖项和代码分析会对应用程序的运行时性能产生轻微影响，因为所有的依赖项都必须在应用程序运行时进行排序，而不是事先进行排序。

## 匕首大战柯因

此时，你一定在问自己:那么，哪个更好呢？最终，这取决于你的项目的细节，规模，年龄，团队的整体经验。我不能为你开出解决方案——这是每个开发团队必须自己做出的决定！

如果您正在阅读本文，试图为您的项目选择这些库，我建议考虑以下几点:

1.  如果你有一个相当年轻或新的项目，考虑选择与你的项目共享相同主语言的库；也就是说，Java 项目可以使用 Dagger，而 Kotlin 项目更喜欢 Koin
2.  如果你认为自己是一个开发新手，不想被 DI 困扰，Koin 是一个不错的选择，因为它被认为更容易学习和使用
3.  如果你是一个喜欢阅读文档的开发者，Hilt 可能是一个更好的解决方案，因为 Android 开发者网站有完整的文档、用例，甚至还有一个官方代码实验室供你参考
4.  虽然我没有在这篇文章中讨论匕首 2 的高级用法，但是一个可能需要更高级的 DI 用法的项目可能应该选择剑柄，以便在需要时可以使用匕首的全部
5.  如果您的项目使用或打算使用 Kotlin 多平台工具来开发重用您的 Kotlin 代码的 iOS 应用程序，Koin 可能是一个更好的解决方案，因为它目前支持 DI 模式，作为 kot Lin iOS 多平台的一部分

如果这些指标还不够有说服力，这里有一个比较它们属性的图表，你可以照着做:

| 柄 | 柯因 | 程序设计语言 |
| --- | --- | --- |
| Java(Java) | 科特林？科特林 | 要求科特林 |
| ❌ | ✅ | Kotlin 多平台支持 |
| ❌ | ✅ | 开放源码 |
| 需要同意 | ✅ | 近似的包装尺寸(。zip) |
| 20 MB | 1 兆字节 | 依赖类型 |
| 建造时间 | 运行时间 | 依赖注入类型 |
| 传统 DI | 服务定位器 | 生成代码 |
| ✅ | ❌ | 构建时性能影响 |
| ❌ | ✅ | 运行时性能影响 |
| ✅ | ❌ | 错误处理 |
| 编译错误 | 运行时异常 | 本机组件兼容性 |
| 视图模型、工作管理器、导航、撰写 | 视图模型、工作管理器、合成 | 结论 |

## 选择依赖注入库不应该是一个负担。请记住，选择阿迪图书馆不是一个架构决策，而是实现细节的一部分*。*这意味着，无论您为项目选择什么，都要确保它以一种易于替换的方式集成，以防最初的决策过时，或者替代选项随着时间的推移变得更加方便！

LogRocket :即时重现你的安卓应用中的问题。

## [LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)

Start proactively monitoring your Android apps — [try LogRocket for free](hhttps://lp.logrocket.com/blg/kotlin-signup).