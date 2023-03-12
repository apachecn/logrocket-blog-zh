# 使用 Kotlin 数据类消除 Java POJO 样板

> 原文：<https://blog.logrocket.com/using-kotlin-data-classes-to-eliminate-java-pojo-boilerplates/>

Kotlin 相对于“普通”Java 所能提供的东西总是让我吃惊，数据类也不例外。在这篇文章中，我们将探索 Kotlin 的数据类如何从老派的 POJOs 中取出所有样板文件，内置的`equals`、`hashcode`和`copy`方法的力量，并学习使用生成的`componentN`帮助器进行简单的析构。最后，我们将检查混合继承和数据类时的一个小问题。

前进！

## 科特林是什么？

作为快速复习，Kotlin 是一种现代的、静态类型的语言，可以在 JVM 上编译使用。它经常被用在任何需要 Java 的地方，包括 Android 应用和后端服务器(使用 Java [Spring](https://spring.io) 或 Kotlin 自己的 [Ktor](https://ktor.io) )。

## Kotlin 的数据类与旧的 Java 习惯相比如何？

如果你以前在 Java 中设置过 POJO，你可能已经处理过一些样板代码:getters 和 setters，一个很好的用于调试的`toString`，一些用于`equals`和`hashCode`的覆盖，如果你想要可比性的话……生成、清洗、重复，对吗？

科特林不喜欢这些仪式。他们创造了一种特殊类型的`class`来处理:

*   基于您的构造函数参数生成的`equals`函数(而不是不太有用的内存引用)
*   基于这些构造函数参数的一个好的、人类可读的`toString()`值
*   一个任意克隆实例的`copy`函数，不需要在构造函数之间建立管道
*   使用 parens `()`的破坏能力

这些都是超越过去 POJO 标准的重大胜利。Kotlin 不仅会为您处理所有的 getters 和 setters(因为默认情况下构造函数参数是公开可用的)，而且它还免费为您提供了可比性！

让我们来学习如何使用这样一个庞大的类声明:

```
class UniversityStudentBreakfast {
  private int numEggs;
  public UniversityStudentBreakfast(int numEggs) {
    this.numEggs = numEggs;
  }
  public int getNumEggs() {
    return numEggs;
  }
  public void setNumEggs(int numEggs) {
    this.numEggs = numEggs;
  }
  @Override
   public boolean equals(Object o) {
     if (this == o) return true;
     if (o == null || getClass() != o.getClass()) return false;
     UniversityStudentBreakfast breakfast = (UniversityStudentBreakfast) o;
     return numEggs == breakfast.numEggs;
   }
   @Override
   public String toString() {
     return "UniversityStudentBreakfast(" +
             "numEggs='" + numEggs + '\'' +
             ')';
   }
  // don't get me started on copy-ability...
}

```

…然后把它变成一个漂亮的一行程序😄

```
data class UniversityStudentBreakfast(
  val numEggs: Int,
)

```

### 使用内置的`equals`特性

让我们从标准类的巨大增值开始:基于我们的构造函数参数的内置等式函数。

简而言之，Kotlin 将生成一个简洁的`equals`函数(加上一个补充的`hashCode`函数),它评估您的构造函数参数来比较您的类的实例:

```
data class UniversityStudentBreakfast(
  val numEggs: Int,
)
val student1Diet = UniversityStudentBreakfast(numEggs=2)
val student2Diet = UniversityStudentBreakfast(numEggs=2)
student1Diet == student2Diet // true
student1Diet.hashCode() == student2Diet.hashCode() // also true

```

⚠️ **注意**:在比较的时候，这为所有的构造函数参数调用`equals`函数。遗憾的是，这意味着当您的数据类包含列表或对其他类的引用时，内存引用问题可能会再次出现。

### 使用`toString`方法

是的，数据类给了你一个很好的`toString`助手来简化调试。我们没有为上面的`UniversityStudentBreakfast`类获取随机内存引用，而是获得了构造函数键到值的良好映射:

```
println(student1Diet)
// -> UniversityStudentBreakfast(numEggs=2)

```

### 使用`copy`特征

科特林的`copy`特性解决了传统类的一个常见缺陷:我们想利用一个现有的类，构建一个*与*略有不同的新类。传统上，有两种方法可以解决这个问题。第一种是手工将所有内容从一个构造函数传递到另一个构造函数:

```
val couponApplied = ShoppingCart(coupon="coupon", eggs=original.eggs, bread=original.bread, jam=original.jam...)

```

…但是这样做很讨厌，尤其是当我们有嵌套引用要担心重复的时候。选项二是简单地承认失败，并使用`[apply {...}](https://kotlinlang.org/docs/scope-functions.html#apply)`打开所有的突变:

```
val couponApplied = original.apply { coupon = "coupon" }

```

…但是如果您的团队正在使用函数式编程技术，您可能不喜欢这种方法。如果我们能有一个类似于`apply`的语法而不改变原始值就好了…

好消息是什么？如果你正在使用一个数据类，`copy`让你做到这一点！

```
data class ShoppingCart(
  val coupon: String, // just a regular "val" will work
  val eggs: Int,
  val bread: Int,
  ...
)
val original = checkoutLane.ringUpCustomer()
val couponApplied = original.copy(coupon="coupon")

```

你还会注意到`copy`只是一个普通的函数调用，没有 lambda 选项。这就是 Kotlin 编译器的美妙之处——它根据构造函数参数为您生成所有参数💪。

## 在科特林揭秘`componentN`

对于数据类，每个属性都可以作为一个组件使用 component1、component2 等扩展函数来访问。，其中数字对应于参数在构造函数中的位置。你也许可以举个例子:

```
data class MyFridge(
 val doesPastaLookSketchy: Boolean,
 val numEggsLeft: Int,
 val chiliOfTheWeek: String,
)
val fridge = MyFridge(
 doesPastaLookSketchy=true,
 numEggsLeft=0,
 chiliOfTheWeek="Black bean"
)
fridge.component1() // true
fridge.component2() // 0
fridge.component3() // "Black bean"

```

您可能会想，“好吧，但是我为什么要通过调用`component57()`来获取一个值呢？”公平的问题！你可能不会像这样直接调用这些助手*。然而，这些对于 Kotlin 在幕后完成析构非常有用。*

 *## 使用 Kotlin 数据类进行析构

假设我们在地图上有一对坐标。我们可以使用`Pair`类将这种类型表示为整数的`Pair`:

```
val coordinates = Pair<Int, Int>(255, 255)

```

那么我们如何从这里获取 x 和 y 值呢？我们可以使用之前看到的组件函数:

```
val x = coordinates.component1()
val y = coordinates.component2()

```

或者，我们可以在变量声明中使用`parens ()`进行析构:

```
val (x, y) = coordinates

```

不错！现在我们可以让 Kotlin 为我们调用那些难看的组件函数。

我们可以将同样的原则用于我们自己的数据类。例如，如果我们希望我们的坐标有第三个 z 维，我们可以创建一个很好的`Coordinates`类，就像这样:

```
data class Coordinates(
 val x: Int,
 val y: Int,
 val z: Int,
)

```

然后在我们认为合适的时候销毁👍。

```
val (x, y, z) = Coordinates(255, 255, 255)

```

**⚠️注释**:当参数顺序没有被暗示时，这可能会变得令人毛骨悚然。是的，在我们的`Coordinates`例子中，很明显`x`在`y`之前(T1 在`z`之前)。但是如果一个工程师心不在焉地将值`z`移到构造函数的顶部，他们可能会破坏整个代码库的析构语句！

## 继承的一个重要问题

随着您开始熟悉数据类，您可能会开始将它们用作各种场合的类型安全对象。

但不要这么快！当你开始面向对象时，问题就开始出现了。为了扩展我们之前的`Fridge`示例，假设您想要一个具有额外字段的特殊数据类来表示您自己的厨房混乱:

```
data class Fridge(
  val doesPastaLookSketchy: Boolean,
  val numEggsLeft: Int,
)
data class YourFridge(
  val servingsOfChickenNoodleLeft: Int,
) : Fridge()

```

换句话说，你想背过第一个`data class`，并保持平等和完整地复制特征。但是如果你在操场上尝试，你会得到一个讨厌的例外:

```
No value passed for parameter 'doesPastaLookSketchy'
No value passed for parameter 'numEggsLeft'

```

嗯，看起来我们需要复制我们的`Fridge`构造函数来允许我们所有的值通过。让我们这样做:

```
data class Fridge(
  open val doesPastaLookSketchy: Boolean,
  open val numEggsLeft: Int,
)
data class YourFridge(
  override val doesPastaLookSketchy: Boolean,
  override val numEggsLeft: Int,
  val servingsOfChickenNoodleLeft: Int,
) : Fridge(doesPastaLookSketchy, numEggsLeft)

```

…这给我们留下了一个非常不同的例外😬

```
Function 'component1' generated for the data class conflicts with member of supertype 'Fridge'
Function 'component2' generated for the data class conflicts with member of supertype 'Fridge'
This type is final, so it cannot be inherited from

```

现在看来，在这些构造函数参数上使用`override`有问题。这归结于 Kotlin 编译器的一个限制:为了让`componentN()`助手指向正确的值，数据类需要保持“最终”状态。

所以，一旦你设置了这些参数，它们就不能被否决(甚至不能被扩展)。

幸运的是，只要父类是*而不是*数据类，您*就可以*获得我们的继承。一个抽象类可能会帮我们完成这个任务:

```
abstract class Fridge(
  open val doesPastaLookSketchy: Boolean,
  open val numEggsLeft: Int,
)
data class YourFridge(
  override val doesPastaLookSketchy: Boolean,
  override val numEggsLeft: Int,
  val servingsOfChickenNoodleLeft: Int,
) : Fridge(doesPastaLookSketchy, numEggsLeft)

```

是的，我们仍然需要使用`override`来复制我们想要的参数，但是它确实为我们的数据类之间的共享参数提供了一些类型安全性，同时保持相等、复制和散列特性的正常工作。

## 结论

正如您所看到的，数据类提供了一些好处，而开发人员的开销几乎为零。这就是为什么我建议在几乎所有使用常规`class`的地方都使用`data`，以获得额外的可比性优势。所以，前进，重写一些 POJOs！

## LogRocket :即时重现你的安卓应用中的问题。

[![](img/b5ae4bd0ecde7aa9d5288746416d5e18.png)](https://lp.logrocket.com/blg/kotlin-signup)

[LogRocket](https://lp.logrocket.com/blg/kotlin-signup) 是一款 Android 监控解决方案，可以帮助您即时重现问题，确定 bug 的优先级，并了解您的 Android 应用程序的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 Android 应用程序— [免费试用 LogRocket】。](hhttps://lp.logrocket.com/blg/kotlin-signup)*