# Swift enums:示例概述- LogRocket 博客

> 原文：<https://blog.logrocket.com/swift-enums-an-overview-with-examples/>

Swift 中的枚举(或简称 enums)为一组相关值定义了一个公共类型。根据 [Swift 文档](https://docs.swift.org/swift-book/LanguageGuide/Enumerations.html)，枚举使您能够在代码中以类型安全的方式使用这些值。当您有许多不同的选项要编码时，枚举就特别方便了。

在 Swift 中使用`enum`类似于编写在满足条件时返回布尔值的代码，例如`true`或`false`。然而，在枚举的情况下，可以有两个以上的可能值。

把枚举想象成一个恒温器。有一系列值可能与结果相匹配，例如，低、中、高、极端。根据匹配的大小写，我们需要运行一些指定的代码。

枚举是一种特殊类型的变量，专门用于开关和条件。Swift 枚举案例没有唯一的默认整数值(像数组一样)，不像 TypeScript 和 Objective C 这样的语言，第一个元素的值是`0`，第二个元素的值是`1`，以此类推。

在本教程中，我们将涵盖 Swift 枚举的所有基础知识，包括:

## Swift 枚举语法

要在 Swift 中定义 enum，请使用关键字`enum`，后跟 enum 的名称。Swift 中的枚举名称应遵循 PascalCase 命名约定，即复合词中每个单词的首字母大写。

```
// starts with the keyword 'enum'and follows the PascalCase naming convention
enum EnumerationNmae {
    // our enum values (case) goes in here
  }

```

下面详细介绍如何在枚举中声明值:

```
enum Direction {
    case up
    case down
    case left
    case right
}

```

枚举中声明的值——`up`、`down`、`left`和`right`——被称为枚举案例。我们使用 case 关键字引入一个新的枚举案例。

与`if-else`语句相反，枚举在`switch`语句中特别有用。这是因为在 switch 语句中，Swift 知道 enum 持有的所有值，因此它将确保您涵盖 enum 中的所有情况或添加默认情况。

## `CaseIterable`在枚举情况下

`CaseIterable`是一种类型，它提供一个枚举的所有值的集合。它用于遍历枚举中的所有事例。

为此，在枚举的名称后添加`CaseIterable`。有了这些，Swift 将通过一个名为`allCases`的枚举类型的属性让我们访问所有案例的集合。

```
enum CarBrand: String, CaseIterable {
    case Mercedes = "Known for luxury and iconic design. Definitely my fav!"
    case Toyota = "Known for high-quality, high-value cars"
    case Volkswagen = "This is the people's car"
    case Ford = "Known for crisp handling, absorbent ride, and solid feel"
    case Honda = "Known for their well-built, reliable cars"
}

```

要访问我们的`CarBrand`枚举中所有案例的集合，我们可以这样做:

```
print(CarBrand.allCases.count)
// expected output: 5

```

在上面的例子中，我们编写了`CarBrand.allCases`来访问包含`CarBrand`枚举的所有事例的集合。`count`方法给出了集合中元素的数量。

我们可以通过在枚举中的所有案例上使用一个`for`循环来更进一步。

让我们使用`allCases`方法将原始值打印到我们的枚举案例中:

```
// create an enum with a CaseIterable type

enum CarBrand: String, CaseIterable {
    case Mercedes = "Known for luxury and iconic design. Definitely my fav!"
    case Toyota = "Known for high-quality, high-value cars"
    case Volkswagen = "This is the people's car"
    case Ford = "Known for crisp handling, absorbent ride, and solid feel"
    case Honda = "Known for their well-built, reliable cars"
}

// we are looping through our enum and accessing all its raw values
for brand in CarBrand.allCases {
    print(brand.rawValue)
}
// expected output:
// Known for luxury and iconic design. Definitely my fav!
// Known for high-quality, high-value cars
// This is the people's car
// Known for crisp handling, absorbent ride, and solid feel
// Known for their well-built, reliable cars

```

## 枚举原始值

在我们的枚举中，我们可以声明一个原始值类型。这实质上意味着将一个值附加到枚举大小写中。

为了更好地理解枚举中的原始值，让我们创建一个 string 类型的枚举(它可以是任何类型)来保存不同品牌的汽车以及每个品牌已知的属性(这些将是原始值):

```
// Enum with raw values

enum CarBrand: String {
    case Mercedes = "Known for luxury and iconic design. Definitely my fav!"
    case Toyota = "Known for high-quality, high-value cars"
    case Volkswagen = "This is the people's car"
    case Ford = "Known for crisp handling, absorbent ride, and solid feel"
    case Honda = "Known for their well-built, reliable cars"
}

```

要为您的枚举设置一个值，您需要为它分配一个数据类型。在上面的例子中，我们使用了一种类型的`String`。

我们的枚举用例的每个原始值必须是唯一的字符串、字符或任何整数或浮点类型的值。这意味着两个 case 语句的值不能相同。参见下面的代码:

```
enum CarBrand: String {
    case Toyota = "High value cars."
    case Volkswagen = "High value cars."
}

```

上面的代码抛出了一个错误“`Raw value for enum case is not unique`”，因为两个 case 值完全相同。

既然我们已经创建了 enum，每个 enumeration case 都有一个原始值，那么让我们创建一个将返回各种汽车品牌的`rawValue`的函数。此处的原始值代表每个品牌的已知属性:

```
func carKnownFor(brand: CarBrand) -> String {
    return brand.rawValue
} 

carKnownFor(brand: .Ford)
carKnownFor(brand: .Mercedes)

// expected output: 
// "Known for luxury and iconic design. Definitely my fav!"
// "Known for crisp handling, absorbent ride, and solid feel"

```

## 枚举关联值

Swift 中枚举的最佳特性之一是，您可以将您定义的值附加到每个`case`中的枚举。这使您能够将附加信息附加到您的枚举，以便它们能够表示更有意义的数据。

例如，假设我们有一个定义奔驰汽车价格的枚举。根据价格，我们想确定用户是否能买得起这种型号的汽车。我们可以在`enum`中将价格与汽车品牌相关联，如下例所示:

```
// enum with associated values
enum MercedesModelPrice {
   case MayBach(price: Int)
   case AMG_GT(price: Int)
   case Metris(price: Int)
   case SprinterWagon(price: Int)
   case GClass
}
```

```
// enum without associated values 
enum MercedesModelPrice {
   case MayBach
   case AMG_GT
   case Metris
   case SprinterWagon
   case GClass
}

```

现在，让我们创建一个函数来检查用户是否买得起奔驰:

```
func getMercedesPrice(for mercedesModel: MercedesModelPrice) {
   switch mercedesModel {
   case .MayBach(price: let price) where price >= 100_000:
    print("You just bought yourself a new car")

   case .Metris(price: let price) where price >= 86_000:
    print("You just bought yourself a new car")

   case .AMG_GT(price: let price)  where price >= 74_000:
    print("You just bought yourself a new car")

   case .SprinterWagon(price: let price) where price >= 45_000:
     print("You just bought yourself a new car")

   case .GClass, .SprinterWagon, .Metris, .AMG_GT, .MayBach:
    print("Insufficient funds. You cant' afford this car at the moment")

   }
}

// Calling our function
getMercedesPrice(for: .SprinterWagon(price: 200_000))
// expected output: You just bought yourself a new car

```

注意我们如何使用关键字`where`来过滤特定价格的`case`。

值得注意的是,`case`语句的顺序很重要。将最后一个`case`语句(`case .GClass, .SprinterWagon, .Metris, .AMG_GT, .MayBach:`)放在最前面意味着我们总是将结果作为匹配附加到这个案例中。

## 枚举方法

除了在我们的 enum 中定义枚举案例，我们还可以在我们的`enum`中定义方法，就像这样:

```
enum Weekday {
    case Monday
    case Tuesday
    case Wednesday
    case Thursday
    case Friday
    case Saturday
    case Sunday

    func dayType() -> String {
        switch self {
        case .Sunday, .Saturday:
            return  "Weekend"
        default:
            return "Weekday"
        }
     }
}

Weekday.Monday.dayType()
// this will return "Weekday"
Weekday.Sunday.dayType()
// this will return "Weekend"

```

在上面的代码片段中，我们在`dayType`方法中使用 switch 语句，根据星期几返回`Weekend`或`Weekday`作为输出。

## Swift 枚举中的计算属性

也可以在枚举中使用计算属性来代替函数。

让我们用一个计算属性替换上面代码中的`dayType`函数:

```
enum Weekday {
    case Monday
    case Tuesday
    case Wednesday
    case Thursday
    case Friday
    case Saturday
    case Sunday

    var dayType: String {
        self == .Saturday || self == .Sunday ? "Weekend" : "Weekday"
    }

}

Weekday.Monday.dayType
// this will return "Weekday"
Weekday.Sunday.dayType
// this will return "Weekend"
>
```

## 结论

快速枚举是简化代码的一种有效方法。在本教程中，我们讲述了`enums`的语法以及如何定义它们，如何创建枚举案例，以及如何在`switch`语句中使用它们。我们还将`CaseIterable`定义为一种提供所有枚举值集合的类型。我们讨论了枚举值(原始值和关联值)以及如何在我们的代码库中使用它们。最后，我们演示了如何在 Swift 枚举中使用计算属性来代替函数。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)