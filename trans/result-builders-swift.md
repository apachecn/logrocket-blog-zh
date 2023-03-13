# Swift 结果构建器入门

> 原文：<https://blog.logrocket.com/result-builders-swift/>

使用 SwiftUI 的 iOS 软件工程师应该熟悉以下语法:

```
VStack(alignment: .leading) {
    Image(hotel.picture)
    Text(hotel.name)
    Text(hotel.price)
}

```

在这个块中，有三个语句，它们是`VStack`的一部分。这些语句仅由[换行符](https://en.wikipedia.org/wiki/Newline)分隔。

本质上，这段代码告诉您，您有一个垂直堆栈(`VStack`)，它由三个堆叠的组件组成，包括一个图像(`Image`)、一个文本(`Text`)和另一个文本(`Text`)。

您知道吗，您也可以在常规 Swift 代码中使用这种语法，而不仅仅是在 SwiftUI 中。

这种语法称为结果生成器，以前称为函数生成器。自 5.4 版以来，结果生成器一直是 Swift 的一部分，这意味着您可以在 Linux 中使用该功能。在本文中，我们将探索如何用`buildBlock`函数以及更高级的函数，如`buildOptional`、`buildArray`和`buildExpression`来编写结果构建器。

我们将涵盖:

## 结果建设者的重要性

为了理解结果构建器的重要性，您需要想象一个没有结果构建器特性的情况。

例如，您可以尝试创建一个简单的函数来接受将要连接在一起的字符串组件，而不是使用接受将要垂直堆叠的组件的`a VStack`。说你喂的功能:`"Hello World!"`、`"Hello LogRocket!"`、`"Hello Swift!"`。它会给你这个结果:`"Hello World!, Hello LogRocket!, Hello Swift!"`。

你打算怎么做？一个简单的方法是创建一个接受组件的函数:

```
func StringStack(_ a: String, _ b: String, _ c: String) -> String {
    a + ", " + b + ", " + c
}

```

然后，您可以调用这个函数:

```
StringStack("Hello World!", "Hello LogRocket!", "Hello Swift!")

```

很好用，但是看起来不是很干净。即使你把论点按行拆分，看起来还是很难看:

```
StringStack(
  "Hello World!,
  "Hello LogRocket!",
  "Hello Swift!"
)

```

逗号毁了一切！但我们想去掉的不仅仅是烦人的逗号。还有一个因素。

假设您用一个逗号将这三个字符串组合在一起，但是您想选择将其更改为其他内容。您可以修改该功能:

```
func StringStack(_ separator: String, _ a: String, _ b: String, _ c: String) -> String {
    a + separator + " " + b + separator + " " + c
}

```

但是当您执行此操作时，问题变得很明显:

```
StringStack(
  "-",
  "Hello World!,
  "Hello LogRocket!",
  "Hello Swift!"
)

```

在上面的代码块中，很难区分是要将四个字符串堆叠在一起，还是要将三个字符串堆叠在一起，用破折号隔开。

如果您使用结果生成器，您可以这样做:

```
StringStack(separator: "-") {
    "Hello World!"
    "Hello LogRocket!"
    "Hello Swift!"
}

```

分隔符和字符串组件之间的目标分隔变得非常清楚。分隔符参数不会与要连接的字符串放在一起。

## 在 XCode 上设置游乐场

打开 **XCode** 。然后创建一个 Swift Playground app。之后，导航到菜单中的**文件**，点击**新建>游乐场……**。将其命名为 **ResultBuildersPlayground** 。您将看到默认代码，它导入了 **UIKit** 并声明了变量 **greeting** 。然后，清空代码。

但是，因为 Swift 上的结果构建器功能不依赖于 iOS 或 Mac，所以您不必使用 XCode。你可以在 Linux 甚至在线 Swift 编译器上使用 Swift，比如 [SwiftFiddle](https://swiftfiddle.com) 。

## 用`buildBlock`构建结果生成器

继续我们上一个例子，假设您想要将`StringStack`方法转换成声明性代码。将以下代码添加到操场:

```
@resultBuilder class ConvertFunctionToResultBuilder {
    static func buildBlock(_ a: String, _ b: String, _ c: String) -> String {
        a + ", " + b + ", " + c
    }
}

```

刚才，您创建了一个包含`buildBlock`静态方法的类，并用`@resultBuilder`对其进行了注释。在`buildBlock`中，你添加了组合字符串的函数。`ConvertFunctionToResultBuilder`不一定是类，可以是枚举或结构。但是在类/枚举/结构内部，必须有一个名为`buildBlock`的静态方法。

接下来，您可以像在 SwiftUI 中一样创建声明性代码:

```
@ConvertFunctionToResultBuilder func MyFirstDeclarativeCode() -> String {
    "Hello World!"
    "Hello LogRocket!"
    "Hello Swift!"
}

```

现在，这才像话。最后一步，然后你可以看到结果的全部荣耀！

让我们执行新功能:

```
print(MyFirstDeclarativeCode())

```

最后，您可以编译代码并运行它。这是结果:

```
Hello World!, Hello LogRocket!, Hello Swift!

```

一切都好！除此之外，如果您查看组合字符串操作的实现，您只能接受三个组件。我们希望更加灵活。

您可以使用变量参数来实现这一点。将`buildBlock`方法改为如下所示:

```
    static func buildBlock(_ strings: String...) -> String {
        strings.joined(separator: ", ")
    }

```

然后将另一个字符串添加到`MyFirstDeclarativeCode`:

```
@ConvertFunctionToResultBuilder func MyFirstDeclarativeCode() -> String {
    "Hello World!"
    "Hello LogRocket!"
    "Hello Swift!"
    "Hello Result Builders!"
}

```

重新编译代码并运行它，得到以下结果:

```
Hello World!, Hello LogRocket!, Hello Swift!, Hello Result Bulders!

```

## 使用`buildOptional`建立可选条件

块中的内容不必像字符串那样简单。它们可以是一个`if`条件，意味着元素可以根据一个变量或条件出现。

假设您想为酒店中的客人准备一份早餐。为此，您可以创建一个结果构建器:

```
@resultBuilder
struct BreakfastBuilder {
    static func buildBlock(_ food: String...) -> String {
        food.joined(separator: ", ")
    }

    static func buildOptional(_ drink: String?) -> String {
        return drink ?? ""
    }
}

```

注意，除了必需的`buildBlock`之外，还有另一个名为`buildOptional`的静态函数。

您可以创建另一个函数来显示真正的早餐:

```
@BreakfastBuilder func makeBreakfast(_ drink: Bool) -> String {
    "Egg"
    "Bread"
    if drink {
        "Milk"
    }
}

```

这里，你可以看到在通常的字符串旁边有一个条件语句。该条件块将被执行，结果将被发送到`buildOptional`。然后`buildOptional`的结果会和其他字符串一起发送到`buildBlock`。

您可以在不同的条件下执行它:

```
print(makeBreakfast(false))
print(makeBreakfast(true))

```

早餐会有所不同，这取决于您是否想要饮料:

```
Egg, Bread, 
Egg, Bread, Milk

```

## 使用`buildEither`建立`if`条件

有了`buildOptional`，你就有了一个没有可选分支的`if`条件或`else`条件。但是有了`buildEither`，你就可以拥有了。

您可以使用相同的示例，但这次使用`buildEither`:

```
@resultBuilder
struct BreakfastBuilder2 {
    static func buildBlock(_ food: String...) -> String {
        food.joined(separator: ", ")
    }

    static func buildEither(first drink: String) -> String {
        return drink
    }

    static func buildEither(second drink: String) -> String {
        return drink + " with sugar"
    }
}

```

所以你增加了`buildEither`而不是`buildOptional`，但是你需要两个这样的函数，而不是一个。请记住，在结果构建器函数的一个块中，您将有一个`if`条件和一个`else`条件。

然后，您可以用这个结果生成器来注释该函数:

```
@BreakfastBuilder2 func makeBreakfast2(_ drinkCoffee: Bool) -> String {
    "Egg"
    "Bread"
    if drinkCoffee {
        "Coffee"
    } else {
        "Tea"
    }
}

```

`Coffee`将被送到第一个`buildEither`，而`Tea`将被送到第二个`buildEither`。您可以执行函数来确认这一点:

```
print(makeBreakfast2(false))
print(makeBreakfast2(true))

```

这是结果:

```
Egg, Bread, Tea with sugar
Egg, Bread, Coffee

```

请记住，第二个`buildEither`在参数后面添加了`with sugar`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 使用`buildArray`来使用数组

在用结果生成器注释的函数块中使用字符串。但是如果你也能使用一个字符串数组不是很好吗？例如，您可能想将食物和饮料放在一起。

这就是`buildArray`派上用场的地方。

首先，创建一个结果生成器，如下所示:

```
@resultBuilder
struct BreakfastBuilder3 {
    static func buildBlock(_ food: String...) -> String {
        food.joined(separator: ", ")
    }

    static func buildArray(_ drinks: [String]) -> String {
        "Drinks: " + drinks.joined(separator: ", ")
    }
}

```

这一次，添加了`buildArray`，它接受一个数组并返回一个`String`，与`buildBlock`接受的参数类型相同。返回的结果稍后会发送给`buildBlock`。

现在，添加将用这个结果生成器注释的函数:

```
@BreakfastBuilder3 func makeBreakfast3() -> String {
    "Egg"
    "Bread"
    for d in ["Coffee", "Tea"] {
        "\(d)"
    }
}

```

要在块中传递数组参数，不需要传递文本数组，而是创建 for-loop 语句。在每次迭代中，添加一个数组元素。在这种情况下，它是一个字符串。稍后，所有这些字符串将被放入一个数组并发送到`buildArray`。

然后你需要执行这个函数:

```
print(makeBreakfast3())

```

结果显示了来自`buildArray`的串联字符串:

```
Egg, Bread, Drinks: Coffee, Tea

```

## 使用`buildExpression`接受块中的不同元素

在我们之前的所有例子中，我们在一个块中使用了`String`类型的元素。但是如果你想和字符串一起使用另一种类型的元素呢？您可以通过在一个块中使用`buildExpression`来处理不同种类的元素。

像往常一样，创建一个结果生成器:

```
@resultBuilder
struct BreakfastBuilder4 {
    static func buildBlock(_ food: String...) -> String {
        food.joined(separator: ", ")
    }

    static func buildExpression(_ food: String) -> String {
        food
    }

    static func buildExpression(_ tissueAmount: Int) -> String {
        "\(tissueAmount) pieces of tissue"
    }
}

```

你加了两个`buildExpression`一个是给`String`的，它只是原样返回参数。但是第二个`buildExpression`更有趣，因为它接受整数。然后，它在字符串中插入整数参数。

现在，是时候创建一个接受字符串和整数的函数了:

```
@BreakfastBuilder4 func makeBreakfast4() -> String {
    "Egg"
    "Bread"
    5
}

```

`Egg`和`Bread`将被送到第一个`buildExpression`。但是`5`会被送到第二个`buildExpression`。然后来自这两个`buildExpressions`的结果将被发送到`buildBlock`。

最后，执行这个函数:

```
print(makeBreakfast4())

```

结果是意料之中的:

```
Egg, Bread, 5 pieces of tissue

```

## 结论

在本文中，我们使用了 result builders 特性，它可以使您的代码像 SwiftUI 中的代码一样具有声明性。我们探讨了使用结果构建器的原因，这是为了让代码看起来更整洁。然后，我们使用`@resultBuilder`创建一个结果构建器，用`buildBlock`静态方法定义一个块的实现。最后，我们学习了其他积木构建方法，如`buildOptional`、`buildEither`、`buildArray`和`buildExpressions`来创建更高级的积木。

本文只是触及了结果构建器的皮毛。你还可以做更多的事情，比如[学习](https://developer.apple.com/videos/play/wwdc2021/10253/) [如何用结果生成器](https://developer.apple.com/videos/play/wwdc2021/10253/)编写 DSL。这篇文章的代码可以在这个 [GitHub 库](https://github.com/arjunaskykok/getting-started-with-result-builders-on-swift)上找到。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)