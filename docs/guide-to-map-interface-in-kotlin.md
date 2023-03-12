# Kotlin 中的地图界面使用指南

> 原文：<https://blog.logrocket.com/guide-to-map-interface-in-kotlin/>

如果你对 [Android 应用程序开发](https://blog.logrocket.com/kotlin-vs-flutter-android-development/)或 Kotlin 多平台开发感兴趣，你可能会在 Kotlin 中遇到 collections 框架。

Kotlin 有一套全面的工具来管理收藏。集合在大多数编程语言中被广泛使用，它们由相同种类的片段或项目组成。

了解 Kotlin collections 框架以及如何在应用程序开发中有效地使用它，将有助于您掌握这种编程语言及其功能。

在本文中，您将了解 Kotlin 中的`Map`接口，以及它的用法和与编码示例的关联。

## Kotlin 中的集合框架

通过使用集合，您可以存储、更新、检索和聚合数据点。Kotlin 标准库为管理集合提供了一套全面的工具。

默认情况下，Kotlin 中的集合是只读的。您不需要修改集合的内容，而是需要构建一个应用了修改的新集合，然后您可以在应用程序中安全地移动它，而旧集合保持不变。

Kotlin 科特林系列分为两类:

*   不可变集合
*   可变集合

以下集合类型与 Kotlin 相关:

*   Kotlin 列表:通过索引访问元素的有序集合。(注意:元素可以在一个列表中出现多次)
*   科特林集:独特元素的集合；这意味着一组没有重复的对象
*   Kotlin Map:一组键值对。键是唯一的，每个键映射到一个值

除此之外，Kotlin 还有集合的可变变体:`[MutableList](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/)`、`[MutableSet](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-set/)`和`[MutableMap](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/)`，允许您修改集合，比如添加或删除元素。

根据需求，可变类型在需要频繁操作数据的地方非常有用；一个很好的例子就是以列表形式显示的 API 调用结果，带有过滤器或搜索查询。

## 科特林地图简介

`Map`在其他编程语言中也称为字典或关联数组。作为集合框架的一部分，Kotlin 中的地图在默认情况下是不可变的。这意味着一旦生成了`Map`中的元素，就不能对其进行编辑、删除或更新。

然而，Kotlin 使用户能够创建可变的地图，或者创建后可以更改的地图。要求是，如果你想创建一个可改变的`Map`，你必须使用可改变的地图类型。

要使用 Kotlin 中的`Map`接口，需要使用它的函数，称为`mapOf()`或`mapOf<k,v>()`。

这里显示了一个`Map`对象的例子:

```
val peopleToCarsOwned = mapOf(
   "Jack" to "Mercedes",
   "John" to "Audi",
   "Will" to "Jaguar"
)
println(peopleToCarsOwned)

// This will print the output:
// {Jack=Mercedes, John=Audi, Will=Jaguar}

println(carsOwned["John"])
// This will print the output:
// Audi

```

在关联数据时，键值对在 Kotlin 中被广泛使用。因此，`Map`成为 Kotlin 中管理结构化数据的常用方法之一。

有几个函数可以应用于`Map`来处理结果所需的相关数据。现在让我们来看看一些常见的功能。

### 检索数据

Kotlin `Map`访问键值对的能力至关重要。若要获取键和值，请使用 get 方法。`getKey`用于获取指定键的值。如果键不存在，将返回一个`null`值。

`getValue`方法返回与值相关联的键。但是，如果 get 函数中给定的值没有关联的键，那么`getValue`方法会提供额外的选项。

在这种情况下，可以使用类似于`getOrElse`和`getOrDefault`的方法。如果找不到钥匙，`getOrElse`允许您更换不同的钥匙。如果找不到密钥，`getOrDefault`会提供您请求的默认密钥。

### 过滤数据

当使用`Map`和访问键值对时，过滤数据以找到特定的信息和键值对是有用的。像 Kotlin 中的其他集合一样，`Map`可以用`filter`函数过滤。

`FilterKey`和`filterValue`类似于`get`方法，因为它们允许您通过键或值进行过滤。当应用过滤器时，返回一个新的仅包含过滤元素的`Map`。例如，如果您使用`filterKey`，您将获得一个完全由指定按键组成的`Map`。

看看下面的例子:

```
open class Car(val name: String, val manufacturedIn: Int) {
    override fun toString() = name
}

val cars = mapOf(
    "Aston Martin" to 2015,
    "Lamborghini" to 2000,
    "BMW" to 1990,
    "Mercedes Benz" to 1980
)

val newCars = cars.filter {
    it.manufacturedIn >= 2000
}

println(newCars)
// This will return the output: [Aston Martin, Lamborghini]

```

如果您需要否定一个条件，那么`filterNot`函数就派上了用场，它的用法与`filter`函数类似，如下所示:

```
// Kotlin
val oldCars = cars.filterNot {
    it.age >= 2000
}

println(oldCars)
// This will return the output: [BMW, Mercedes Benz]

```

### 编辑数据

因为默认情况下`Map`是不可变的，如果你想修改`Map`，你需要先创建一个可变的`Map`。同样值得注意的是，键不能被更改，但是可以被删除——当您编辑一个条目时，您正在修改一个键的值。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

您可以创建新的键-值组合，更新现有键的值，或者完全消除键-值组合。要向 Kotlin `Map`添加新数据，需要使用`put`方法。

但是，如果您输入的键已经存在于您的`Map`中，您可以使用`put`方法来替换该键的现有值。使用`remove`，你可以从你的 Kotlin `Map`中完全删除条目。

## 在 Kotlin 中使用地图

看看下面的例子和场景，更好地理解 Kotlin 中的`Map`接口是如何工作的:

```
val genericMap: Map<Int, String> = mapOf<Int,String>(
    1 to "Jack",
    4 to "John",
    3 to "Will"
)

for(key in genericMap.keys) {  
    println("Element at key position $key: ${genericMap.get(key)}")
}  

// This will print the output:
// Element at key position 1: Jack
// Element at key position 4: John
// Element at key position 3: Will

```

如果您不能为`Map`指定任何类型的键和值，那么它可以立刻接受不同类型的键-值对。这是可能的，因为`Map`实现在内部使用了`<Any, Any>`声明。看看这个例子:

```
val customMap = mapOf(
    1 to "Rick",
    4 to "Tesla",
    3 to True,
    "Train" to "Station",
    "Rocket" to "Launch"
    )

for(key in customMap.keys) {  
    println("Element at key position $key: ${customMap.get(key)}")
}

// This will print the output:
// Element at key position 1: Rick
// Element at key position 4: Tesla
// Element at key position 3: True
// Element at key position Train: Station
// Element at key position Rocket: Launch

```

## 结论

虽然深入了解`Map`界面如何在 Kotlin 中运行不太可能是增强你的应用程序的必要条件，但基本了解`Map`如何在 Kotlin 中运行是必不可少的。

这种数据结构支持用户对现代移动应用程序越来越多的期望。

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)