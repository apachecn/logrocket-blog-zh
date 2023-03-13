# Kotlin 数据映射:比较 map()、flatMap()和 flatten()

> 原文：<https://blog.logrocket.com/kotlin-data-mapping-comparing-map-flatmap-flatten/>

虽然 Android 和 iOS 操作系统都越来越重视它们所采用的现代编程语言(分别是 Kotlin 和 Swift)引入的当代函数式编程模式和范式，但使用和操作集合的需求也随之呈指数增长。

Kotlin 标准库包含了一系列扩展函数，旨在将转换添加到集合中，这些函数也旨在满足对良好、快速和高效的集合操作日益增长的需求。

Kotlin 标准库提供了四种主要的集合转换类型:Map、Zip、Associate 和 Flatten。在本文中，我将只关注其中的两个；即 Map 和 Flatten，因为这组扩展函数具有特殊的关联，并且因为它们旨在组织相似的用例而永远压缩在一起。

在本文中，我们将讨论:

## 描绘地图

先从地图基础说起。`map()`的转换函数由[科特林的官方文档](https://kotlinlang.org/docs/collection-transformations.html#map)定义:

> 映射转换通过对另一个集合的元素执行函数的结果来创建一个集合。它将给定的 lambda 函数应用于每个后续元素，并返回 lambda 结果的列表。结果的顺序与元素的原始顺序相同。

为了更好地解释这意味着什么，让我们从一个简单的例子开始:

在本例中，我们有一个包含三个值的`Set`:数字 1、2 和 3，然后使用带有谓词的`map()`函数对其进行处理，该谓词将来自`numbers`集合的每个元素乘以`5`,并返回结果列表:

```
[5, 10, 15]

```

`map()`函数有一个对应的函数，可以更方便地利用元素的索引。也就是说，`mapIndexed()`非常适合在期望的转换中需要元素的索引时使用。

下面是一个简单的例子，使用之前相同的基本`Set`集合，后面是它的结果:

```
[1, 1, 1]

```

按照 Kotlin 的空安全倡议，这两个扩展函数也附带了它们的版本，允许结果集合忽略所有可能被给定转换无效的值。

`mapNotNull()`和`mapIndexedNotNull()`都是方便地创建的，记住在某些情况下仍然可能产生空值，它们的用法将在下一个代码块中展示:

```
[6, 3, 9]
[6, 3, 9]

```

最后，使用带有`Map`集合的地图转换提供了两个并行选项。想要应用到映射键的转换应该使用`mapKeys()`函数，而应用到映射值的转换应该使用`mapValues()`函数。

正如[官方文档对](https://kotlinlang.org/docs/collection-transformations.html#zip:~:text=When%20transforming%20maps%2C%20you)的解释，这两个函数都使用将地图条目作为参数的转换，因此您可以操作它的键和值:

平整土地

```
{FIRST=1, SECOND=2, THIRD=3, FOURTH=4}
{first=6, second=8, third=8, fourth=10}

```

## 正如 [Kotlin 关于集合转换的文档](https://kotlinlang.org/docs/collection-transformations.html)所解释的，操作嵌套集合有时需要使用标准库函数，这些库函数提供对嵌套集合元素的平面访问。有两个主要函数提供这种解决方案:`flatten()`和`flatMap()`，这两个函数都被认为是展平集合转换函数组的一部分。

首先是`flatten()`，这个函数将接受一个集合的集合，并返回一个包含嵌套集合曾经拥有的所有元素的单数`List`。

在第一个关于`flatten()`的例子中，我们采用了一个嵌套的`Set`列表，我们将把它们展平成一个简单显示所有值的`List`:

In this first example for `flatten()`, we take a nested list of `Set`s, and we’ll flatten them into a single `List` that will simply show all the values:

`flatten()`函数能够展平包含任何对象类型的嵌套集合。为了进一步证明这一点，这里有一个非常相似的例子，它使用了`String`:

```
[9, 8, 7, 6, 5, 4, 3, 2, 1, 0]

```

The `flatten()` function is capable of flattening nested collections containing any kind of object types. To further demonstrate that, here’s a very similar example that uses `String` instead:

此外，该函数不受嵌套集合中对象类型的限制。这意味着内部集合可以包含不同的对象类型，并且`flatten()`函数应该仍然能够构造一个类型为`Any`的结果`List`，它将包含不同类型的所有结果:

```
[Los Angeles, San Francisco, Sacramento, Ausitn, San Antonio, Houston, Dallas, Mexico City, Monterrey, Guadalajara]

```

绘制平地地图

Kotlin 提供的另一个扁平化集合转换函数是`flatMap()`。[根据其文档](https://kotlinlang.org/docs/collection-transformations.html#flatten:~:text=on%20v.1.7.20-,Another%20function,-%E2%80%93%20flatMap())，第二个函数的工作方式与`flatten()`非常相似，但是提供了额外的灵活性，通过采用一个将集合元素映射到另一个集合的函数，提供了一种处理嵌套集合的方法。因此，`flatMap()`返回所有元素返回值的单个列表。

```
[1, 2, 3, one, two, three, 1.0, 2.0, 3.0]

```

## `flatMap()`表现为对`map()`——以集合作为映射结果——和`flatten()`的后续调用。
——【kotlinlang.org 

让我们从一个我们用来测试`flatten()`的例子开始，但是为了强调这两个选项之间的区别，增加了一些变化:

> `flatMap()` behaves as a subsequent call of `map()` — with a collection as a mapping result — and `flatten()`.
> — [kotlinlang.org](https://kotlinlang.org/docs/collection-transformations.html#flatten)

在上面的例子中，我们回收了一个`flatten()`例子，但是我们使用了`flatMap()`函数来得到相同的结果。

正如这里所看到的，`flatMap()`函数不仅展平了嵌套集合，而且还获得了一个额外的机会，通过在内部集合上触发的`map()`函数，向内部集合类型添加转换——在本例中是不同的`Set`——以进行进一步的操作:

```
[9, 8, 7, 6, 5, 4, 3, 2, 1, 0]

```

In the example above, we’ve recycled one of the `flatten()` examples, but instead we’re using the `flatMap()` function to arrive at the same result.

在第二个`flatMap()`示例中，我们再次使用相同的代码，但是这次我们添加了一个额外的操作作为`map()`函数步骤的一部分，以便进一步揭示两个 Flatten 函数之间的主要差异。

在处理复杂的数据类或 POJOs 时,`flatMap()`函数特别有用，因为它提供了找到或进一步转换嵌套在第一层下面的信息的机会。

```
[6, 7, 8, 9, 4, 5, 1, 2, 3, 0]

```

在下面的例子中，我们将使用稍微复杂的`data class`来探索在`flatMap()`调用中更复杂的数据操作:

The `flatMap()` function is especially useful when dealing with complex data classes or POJOs, as it will give the opportunity to find or further transform the information that is nested underneath the first layer.

正如它的同胞`flatten()`所做的那样，`flatMap()`函数也能够展平不同类型的集合，这些集合最终被解释为类型`Any`的集合:

```
[Austin, San Antonio, Dallas, Houston, Los Angeles, San Francisco, Sacramento, Monterrey, Guadalajara, Mexico City]

```

最后，`flatMap()`函数还能够在其转换块中添加一个额外的`map()`转换，以便向嵌套集合组的最内层的集合添加更多的转换:

```
[one, two, three, 1, 2, 3]

```

最后一个例子展示了`flatMap()`函数的最高用法。一个`flatMap()`和一个后续的`map()`调用的组合将允许最通用和最细粒度的列表操作，同时为用户提供一个对任何需求的简单解释。

结论

```
[Austin:Texas, San Antonio:Texas, Dallas:Texas, Houston:Texas, Los Angeles:California, San Francisco:California, Sacramento:California, Monterrey:Mexico, Guadalajara:Mexico, Mexico City:Mexico]

```

所有三个 Kotlin 数据映射函数——`map()`、`flatten()`和`flatMap()`——都是专门处理 Kotlin 集合的一组转换函数的一部分。它们有助于解释和操作复杂和/或嵌套的集合，这些集合在默认状态下可能很难分析。

## 通过正确利用这些强大的功能，开发人员应该能够更好地从任何复杂的集合结构中提取所需的信息。这项技术对于最大化开发人员工作的效率和可靠性是必不可少的。在处理非常流行的[反应流](https://en.wikipedia.org/wiki/Reactive_Streams)编码范例时，正确使用这些方法可能会非常方便，RxJava 和 Kotlin 协同例程等流行的并发库使您能够做到这一点。

LogRocket :即时重现你的安卓应用中的问题。

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

## LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)

LogRocket also helps you increase conversion rates and product usage by showing you exactly how users are interacting with your app. LogRocket's product analytics features surface the reasons why users don't complete a particular flow or don't adopt a new feature.

Start proactively monitoring your Android apps — [try LogRocket for free](hhttps://lp.logrocket.com/blg/kotlin-signup).