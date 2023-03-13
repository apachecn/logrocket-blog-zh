# Kotlin IntArray、Array 和 emptyList()

> 原文：<https://blog.logrocket.com/kotlin-intarray-array-emptylist/>

Kotlin 是一种非常强大的静态类型编程语言，允许我们编写非常有表现力而又简洁的代码。正如现代语言中常见的那样，这种表达能力通常意味着我们可以用几种方式实现相同的功能。一个典型的例子是根据我们的需要选择一个合适的集合类。

事实上，Kotlin 库提供了各种不同的集合，也就是说，将许多(可能是零个)集合项目进行类型分组。

在本文中，我们将研究 Kotlin 中的数组和整数。我们将分析表示整数数组的两种不同类型，`IntArray`和`Array`。最后，我们将比较`IntArray`和`Array with` 列表，它们是不同的集合类型。

*向前跳转:*

## `IntArray`对`Array`

Koltin 有两种不同的数组实现。

第一个是`Array`类，是类型`T`的泛型，代表类型`T`的元素数组。`Array`定义了几个方法，允许我们读/写给定索引处的元素，查询大小，等等。这些数组实现是*不变的*。因此，如果`T`是子类型`U`，那么`Array`不被认为是`Array`的子类型。

 对于原始类型，Kotlin 也为我们提供了专用的数组，比如`IntArray`或者`ShortArray`。在这些“原始”数组和它们的通用对应物(例如，`Array`)之间没有子类型。尽管如此，它们还是有相同的方法。

一个`IntArray`和一个`Array`之间的主要区别在于，前者被表示为一个`int[]`，而后者被编译成一个`Integer[]`。

### 类型与类别

Kotlin 中的`IntArray`和 an `Array`的实际区别，和 Java 中的`int`和`Integer`的区别基本相同。前者是基本类型，而不是类，存储代表给定整数的实际二进制值。后者是一个 Java 类，定义了一个`int`类型的字段。

作为一个类，`Integer`更有表现力，因为我们可以调用它的方法。尽管如此，`int`带来了更好的性能，因为使用`Integer`甚至会增加最简单计算的开销。

### 表演

在性能关键的情况下，`IntArray`比`Array`表现更好。根据 Kotlin Academy 的书*有效 Kotlin* 的[“第 55 项】可知，后者分配的字节数是前者的 5 倍。具体来说，为了存储 100 万个号码，`IntArray`需要 4，000，016 字节，而`Array`分配 20，000，040 字节。](https://kt.academy/article/ek-arrays)

处理原始数组也更快。同样，根据*有效科特林*的“第 55 项”，用`IntArray`计算一百万个整数的平均值要快 25%。

### 初始化

`IntArray`和的另一个区别。基本数组可以不初始化。更具体地说，默认情况下，数组的元素将被设置为`0`:

```
val intArr = IntArray(5)
println(intArr.joinToString(" "))
```

上面的例子将打印`0 0 0 0 0`。

另一方面，我们没有为`Array`提供这种方便的初始化。事实上，它的构造函数输入两个参数，一个是大小，另一个是有效的非空默认值:

```
val arrInt = Array<Int>(5) { 1 }
println(arrInt.joinToString(" "))
```

上面的例子会打印`1 1 1 1 1`。

我们也可以使用`null`值，但是结果数组的类型会有所不同——我们必须小心`nulls`:

```
val arrInt = arrayOfNulls<Int>(5) // Array<Int?>
println(arrInt.joinToString(" "))
```

上面的代码片段将打印出`null null null null null`，但是数组的类型现在是`Array<Int?>`而不是`Array`。

### 创造

Kotlin 还为我们提供了工厂函数来创建这两种类型的数组:

```
val intArray: IntArray = intArrayOf(0, 1, 2, 3)
val arrayInt: Array<Int> = arrayOf<Int>(0, 1, 2, 3)
```

### 转换

我们可以把一个`IntArray`变成一个`Array`，反之亦然，分别使用`IntArray::toTypedArray()`和`Array::toIntArray()`:

```
val arrayInt: Array<Int> = intArrayOf(0, 1, 2, 3).toTypedArray()
val intArray: IntArray = arrayOf<Int>(0, 1, 2, 3).toIntArray()
```

最后要注意的是，`IntArray`和`Array`的大小是固定的。一旦我们设置了元素的数量，我们就不能在数组中添加或删除元素。

## `emptyList()`

除了数组，Kotlin 还为我们提供了其他类型的集合。一个例子是`List`。Kotlin 中的列表和数组有几个不同之处。以下是主要区别:

*   **Class vs. list** : `Array`是一个类，而`List`和`MutableList`是不同实现的接口。数组是顺序的固定大小的内存区域，编译成 JVM 数组。然而，对于列表，这完全取决于实际的实现。例如，`ArrayList`使用了一个隐藏的数组，因此它的运行时性能与`Array`相似
*   **调整大小** : `MutableList`可调整大小，而`Array`和`List`不可调整大小
*   **可变性** : `Array`是可变的，而`List`不是。如果我们想修改列表中的元素，我们必须使用`MutableList`
*   **不变性对协方差** : `Array`和`MutableList are`对`T`不变，而`List`是协变的。这意味着如果`T`是`U`的子类型，那么`List`就是`List`的子类型
*   **类型**:`Array`和`MutableList`都是原始类型的优化数组类型

 我们可以使用`emptyList()`方法初始化一个空列表，然后用`List::toTypedArray()`将它转换成`Array`的一个实例，就像我们对`IntArray`所做的那样:

```
val list: List<Int> = emptyList()
val array: Array<Int> = list.toTypedArray()
```

## 在 Kotlin 中使用数组

让我们看看主要的函数，看看它们是如何在 Kotlin 中处理数组的。在下面的例子中，给出的方法适用于`IntArray`和`Array`。

### 获取和设置元素

使用数组时最常见的操作无疑是获取和设置元素。我们可以用`get`和`set`方法做到这一点。这样的方法经常被调用，以至于 Kotlin 为它们定义了语法糖:

```
val array = intArrayOf(1, 12, 856, 0, -10)

println(array[2]) // same as array.get(2)
array[2] = 78 // same as array.set(2, 78)
println(array[2])
```

上面的例子将打印出`856`和`78`，确认数组已经被修改。

但是，如果数组没有在给定的索引处定义，Kotlin 将抛出一个`IndexOutOfBoundsException`:

```
val array = intArrayOf(1, 12, 856, 0, -10)

println(array[7])
```

上面的示例将失败，并出现以下异常:

```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 7 out of bounds for length 5
```

### 遍历数组

我们可以使用`for`循环或`while`循环遍历 Kotlin 中的数组。但是，这些方法的水平都有点低。或者，Kotlin 为我们提供了通用的`forEach`函数，它比循环更具声明性:

```
val array = intArrayOf(1, 12, 856, 0, -10)

array.forEach{ println(it) }
```

上面的例子将打印数组的所有元素，每行一个。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

有时候，让 Kotlin 在遍历数组时为数组元素分配索引可能会很有用。我们可以用`forEachIndexed`做到这一点:

```
val array = intArrayOf(1, 12, 856, 0, -10)

array.forEachIndexed{ index, elem ->
    println("Element at index $index: $elem")
}
```

上面的示例将打印以下输出:

```
Element at index 0: 1 
Element at index 1: 12
Element at index 2: 856
Element at index 3: 0
Element at index 4: -10

```

我们也可以使用`fold`来遍历数组。当我们想从数组中计算一个值时，我们通常会这样做。例如，我们可以用它来对一个数组的所有元素求和:

```
val array = intArrayOf(1, 12, 856, 0, -10)

val sum = array.fold(0, { acc, elem ->
    acc + elem
})

println(sum)
```

当使用`fold`时，第一个参数是我们计算的初始值，在上面的例子中是`0`。第二个参数是一个二元函数，用于用数组的每个元素更新部分结果。这种函数的第一个参数是所谓的累加器，而第二个参数是数组的一个元素。上面的例子将打印出`859`，和预期的一样。

### 排序和反转数组

排序和反转数组是就地操作。这意味着它们不会返回一个新的数组，而是改变现有的数组。

为了对数组进行排序，我们可以调用它的`sort`方法:

```
val array = arrayOf(1, 12, 856, 0, -10)

array.sort()

println(array.joinToString(" "))
```

上面的例子将打印`-10 0 1 12 856`，显示数组被修改。事实上，`sort`方法返回了`Unit`。

类似地，要反转一个数组，我们可以使用`reverse`:

```
val array = arrayOf(1, 12, 856, 0, -10)

array.reverse()

println(array.joinToString(" "))
```

上面的例子将打印出`-10 0 856 12 1`，显示数组被反向放置。反转也可以是部分的，也就是说，我们可以指定一个起点和一个终点:

```
val array = arrayOf(1, 12, 856, 0, -10)

array.reverse(1, 3)

println(array.joinToString(" "))
```

在这种情况下，该示例将打印`1 856 12 0 -10`，其中索引`1`(包含)到`3`(不包含)的元素，即第二个和第三个元素被颠倒。

## 结论

在本文中，我们研究了`IntArray`和`Array`之间的差异，比较了创建每种类型实例的不同方式。我们讨论了这两种类型数组的不同用例，将它们与列表进行了比较，并了解了如何将列表转换为数组。

最后，我们探讨了一些最常见的数组操作，特别是如何设置或获取元素，以及如何遍历、排序和反转数组。

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)