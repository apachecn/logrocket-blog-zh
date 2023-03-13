# 了解 Jetpack 合成中的重组

> 原文：<https://blog.logrocket.com/jetpack-compose-recomposition/>

对事件变化的响应对于创建最佳用户界面非常重要。在许多情况下，用户没有输入正确的输入，并且当用户改变偏好或位置时，UI 必须改变。

为了使用户界面保持最新，UI 框架 Jetpack Compose 提出了一种叫做重组的技术。当用户输入改变时，重组更新用户界面。

例如，当用户按下按钮时，文本将作为重组的结果显示在屏幕上:

```
@Composable
fun ButtonCounter(clicks: Int, onClick: () -> Unit) {
    Button(onClick = onClick) {
        Text("Recomposition will occur when the button gets clicked and test is displayed on the screen")
    }
}

```

在本文中，您将了解更多关于 Jetpack 合成以及什么是重组的内容:

## 什么是 Jetpack Compose？

每个开发者都想创造出脱颖而出的应用。然而，有这么多的应用程序争夺注意力，想出一些新的和令人兴奋的东西可能会很棘手。这就是像 Android Jetpack Compose 这样的框架的用武之地！

Android Jetpack 是一个模块化库的集合，可以使您的应用程序更智能、更高效、更具吸引力。这些库建立在新的 Android 架构组件之上，因此它们旨在协同工作，以便更容易地构建高质量的应用程序。

Jetpack Compose 包含几个 API，有助于使您的应用程序更快、更智能、更吸引用户，同时减少您需要编写的代码量。

在创建用户界面时，Jetpack 很有帮助。它是一个工具包，为您提供构建原生 UI 的功能。它给你:

*   用户界面设计工具
*   科特林原料药
*   开发加速功能

## 使用 Jetpack Compose 的好处

以下是使用 Jetpack Compose 的好处:

### 用工具减少样板代码

使用 Android Jetpack Compose 的第一个也是最重要的好处是它减少了样板代码。在应用开发的前几周，你写的很多代码都是重复的。

Jetpack Compose toolkit 有几种方法可以帮助减少样板代码。它使用预先编写的代码，您可以通过配置将这些代码放入您的项目中。您还可以使用可视化工具来构建您的应用程序。一个例子是可视化视图编辑器工具。该工具允许您可视化地创建应用程序布局，而不是手动编写代码来这样做。如果你不太熟悉 Android 布局，这很有帮助。

### 使用各种代码示例和 API 编写更少的代码

Android Jetpack 附带了许多代码示例和 API，旨在更容易地编写更少的代码，同时仍能生成高质量的应用程序。这些 API 还使处理常见问题变得更加容易，比如管理状态和管理网络连接。它们还使得利用最新的 Android 创新来创建应用程序变得更加容易。

### 适应性布局

Jetpack Compose 具有灵活和自适应的布局，使您可以在不同的设备上以不同的兼容性构建 Android 应用程序。

Jetpack Compose Wear 为开发人员提供了用于构建智能手表应用程序的 UI 工具和功能，而 Jetpack Compose Mobile 则提供了用于构建移动应用程序的 UI 工具。Jetpack Compose Wear 和 Jetpack Compose Mobile[相似，只有一些差异](https://developer.android.com/training/wearables/compose)。这意味着你将使用你用来构建移动应用的大部分知识和代码来构建 Wear OS 应用。

### 您应该使用 Jetpack Compose 的其他原因:

*   它是广泛的，并迎合 Android Wear 操作系统，可折叠，平板电脑，chromeOS。当它适应不同的屏幕时，它是有反应的
*   Jetpack 最大限度地减少了耦合，这使得修改代码变得困难，并且优化了内聚性，这使得互操作性变得更加容易
*   使用 Jetpack Compose，您不必同时使用 Kotlin 和 XML。你只需要关注科特林的文件

## Jetpack Compose 中的 composables 和 recomposition 是什么？

在 Jetpack Compose 中，composable 是一个 UI 函数。一组可组合体以及它们与每个可组合体的关联方式被称为组合。当组成结构和关系发生变化时，这个过程称为重组。

重组的一个例子是当用户通过 UI 输入错误的输入时，Jetpack Compose 通过添加悲伤的动画或提示用户重置密码来响应。在重组之前，有了`invalidate()`方法。

重组很重要，因为它消除了每当输入改变时调用所有函数的需要。状态变量用于触发重组。重组在 Jetpack Compose 中是必不可少的，因为 Kotlin 文件的优先级高于 XML 文件。

许多 Android 开发人员将重组过程比作一个甜甜圈，以简化其工作方式；一个甜甜圈的中心有一个洞，这被视为范围。这个作用域用来显示重组是如何忽略作用域之外的函数的。

此外，重组不以任何特定的顺序或层次结构执行。下面是一个可组合的示例，当用户更改汽车型号时，它将进入重组阶段:

```
@Composable
fun CarSelector(
    model: String,
    names: List<String>,
    onNameClicked: (String) -> Unit
) {
    Column {
        Text(model, style = MaterialTheme.typography.h5)
        Divider()

           }
}

```

可组合有以下几个阶段:

1.  可组合进入组合过程
2.  可组合的内容通过重组过程进行修改
3.  可组合物离开组合物

与活动生命周期相比，这个生命周期没有那么复杂。一个 composable 可以被一个或多个方法多次调用。这将导致在一个组合中创建多个实例。将使用调用站点对可组合的实例进行排序和组织。如果从同一调用时间多次调用 composable，Jetpack Compose 将很难对调用进行排序。

Jetpack 使用一种称为间隙缓冲区的数据结构，它可以有效地存储数据并提高处理重组的性能。在重新组合期间，在第一次组合期间没有被调用的可组合组件也可以被调用，而那些在第一次组合期间被调用的可组合组件只有在状态改变时才会改变。在重组过程中，可以按任意顺序调用 Composables。

## 为什么重组在 Jetpack 撰写中很重要？

改变整个活动消耗更多的资源和电池百分比。这就是为什么重新呈现只改变了状态的组件很重要。

Jetpack Compose 中的重组侧重于更新已更改的元素。它能够做到这一点是因为它跟踪可组合状态。当存储的状态改变时，将触发重组。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

有时，在重组过程中，许多组件必须更改。为了保持同步，Jetpack Compose 并行运行重组以优化性能。

重组是为了在用户输入改变时保持 UI 最新。因此，如果用户在应用程序进行重组时更改了输入，compose 将取消正在进行的重组，并开始一个新的重组，该重组将根据新的用户输入调整 UI。

更新可组合组件时，不要使用 setter。相反，使用新数据调用可组合的。此外，在使用 composables 时，执行繁重的任务(如阅读首选项)也很重要。

## 结论

Jetpack Compose 通过消除对 XML 文件的需求并引入更多的 Kotlin 文件，无疑改善了开发人员的世界。这意味着开发人员不必花费更多的时间来掌握 XML 和创建难以调试的意大利面条式代码。

此外，诸如 [l](https://android-developers.googleblog.com/2022/05/whats-new-in-jetpack-compose.html#:~:text=features%20and%20improvements-,Lazy%20Layouts,-Lazy%20layouts%20continue) [azy 布局](https://android-developers.googleblog.com/2022/05/whats-new-in-jetpack-compose.html#:~:text=features%20and%20improvements-,Lazy%20Layouts,-Lazy%20layouts%20continue)和重组等功能大大改善了 Android 应用程序。重组必须被理解为能够构建响应性应用程序。用户不想知道你的应用性能为什么滞后。因此，使用 Jetpack Compose 来提高应用程序的性能和感觉非常重要。

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)