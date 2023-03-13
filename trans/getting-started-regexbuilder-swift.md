# Swift 上的 RegexBuilder 入门

> 原文：<https://blog.logrocket.com/getting-started-regexbuilder-swift/>

关于正则表达式有一句古老的谚语:“有些人在遇到问题时，会想‘我知道，我会使用正则表达式。’现在他们有两个问题。“这证明了正则表达式是多么混乱和复杂。

这就是 Swift 语言版本 5.7 的 [RegexBuilder](https://developer.apple.com/documentation/RegexBuilder) 大放异彩之处。RegexBuilder 简化了正则表达式的编写，使它们更具可读性。在本文中，我们将介绍 RegexBuilder 的入门知识，包括使用各种 RegexBuilder 组件，如`CharacterClass`、`Currency`和`date`。

向前跳:

## 在 Xcode 上建立一个快速游乐场

您可以在许多平台上使用 Swift 语言，包括 Linux。Linux 支持 RegexBuilder，但在本教程中，我们将在 Mac 上使用 Swift，因为我们使用的是来自`UIKit`库的方法，该库只在 Mac 上可用。

首先，打开 Xcode。然后创建一个 Swift Playground app。之后，导航到菜单中的**文件**，点击**新建>游乐场**。将其命名为 **RegexBuilderPlayground** 。您将看到默认代码，它导入了 **UIKit** 并声明了变量 greeting **:**

![Variable Greeting](img/bf332ed7509e6f1122fb069e47657292.png)

## 使用正则表达式 API

在学习如何使用新的 RegexBuilder API 之前，您应该熟悉原来的 [Regex API](https://developer.apple.com/documentation/swift/regex) 。

用下面的代码替换创建新操场时得到的默认代码:

```
import UIKit

let regex = /\[email protected]\w+/
let match = "[email protected]".firstMatch(of: regex)
print(match!.0)

```

编译并运行代码，您将得到以下结果:

```
[email protected]

```

如您所见，正则表达式是用这个神秘的语法编写的:`/\[[email protected]](/cdn-cgi/l/email-protection)\w+/`。

`\d`表示一个数字，`\d+`表示一个或多个数字，`@`表示字面上的 *@，* `\w`表示单词字符，`\w+`表示一个或多个单词字符。`/`是正则表达式语法的边界。

下一行是如何使用`firstMatch`方法将字符串与正则表达式匹配。结果就是 [`match`](https://developer.apple.com/documentation/swift/regex/match) 的对象。你用`0`方法得到完全匹配，如果有的话。

## RegexBuilder API

现在，是时候用`RegexBuilder` API 检查等价代码了。有一个快捷方式可以将旧的正则表达式语法转换成`RegexBuilder`语法。突出显示旧的正则表达式语法并右键单击(按住 Control 按钮的同时单击),您应该会看到一个选项，将旧的正则表达式语法重构为新的`RegexBuilder`语法:

![Refactor Syntax To RegexBuilder Syntax](img/b1b1c75d96e8b6a5ce98809f175ce392.png)

新的正则表达式语法如下所示:

```
let regex = Regex {
    OneOrMore(.digit)
    "@"
    OneOrMore(.word)
}

```

有了这种新语法，你再也不用担心`\d`是什么意思了。在`RegexBuilder` API 中，晦涩的`\d+`已经被更友好的语法`OneOrMore(.digit)`所取代。`OneOrMore(.digit)`的意思很清楚。与`\w+`的情况相同，它的替换语法`OneOrMore(.word)`要清晰得多。

另外，请注意已经添加了`RegexBuilder`的导入行:

```
import RegexBuilder

```

## RegexBuilder 量词

[`OneOrMore`](https://developer.apple.com/documentation/regexbuilder/oneormore) 是量词。在遗留 API 中，量词是`*`，表示零或更多，`+`，表示一或更多，`?`，表示零或一，`{n,m}`，表示至少`n`次重复，最多`m`次重复。

如果你想让`@`的左边变成可选的，你可以使用 [`Optionally`](https://developer.apple.com/documentation/regexbuilder/optionally) 量词:

```
let regex2 = Regex {
    Optionally(.digit)
    "@"
    OneOrMore(.word)
}

```

上面的代码表示`/\[[email protected]](/cdn-cgi/l/email-protection)\w+/`。

如果你想要在`@`的左边最少四位数，最多六位数呢？你可以用 [`Repeat`](https://developer.apple.com/documentation/regexbuilder/repeat) :

```
let regex3 = Regex {
    Repeat(4...6) {
        .digit
    }
    "@"
    OneOrMore(.word)
}

```

## 匹配 RegexBuilder 组件

让我们从头开始学习`RegexBuilder`。添加以下代码:

```
let text = "Writer/Arjuna Sky Kok/$1,000/December 4, 2022"
let text2 = "Illustrator/Karen O'Reilly/$350/November 30, 2022"

```

这个例子演示了您为 LogRocket 工作，需要解析自由职业者的付款文本。`text`变量表示 LogRocket 最迟应在 2022 年 12 月 4 日支付阿尔诸那天空角 1000 美元的写作服务费。`text2`变量表示 LogRocket 应该在 2022 年 11 月 30 日支付 Karen O'Reilly $350 的插图服务费用。

您希望将文本解析为四个部分，即工作部分、姓名部分、付款金额和付款期限。

### 使用`ChoiceOf`表示选择

让我们从工作组件开始。根据上面的代码，工作要么是“作家”，要么是“插图画家”您可以创建一个表达选择的正则表达式。

添加以下代码:

```
let job = Regex {
    ChoiceOf {
        "Writer"
        "Illustrator"
    }
}

```

如代码所示，您使用了 [`ChoiceOf`](https://developer.apple.com/documentation/regexbuilder/choiceof) 来表示选择。你把你想选的东西放在`ChoiceOf`块里面。你不只限于两种选择。您可以添加更多选择，但每个选择都需要一条专线。在遗留 API 中，您可以使用`|`。

您可以通过添加以下代码将其与`text`变量匹配:

```
if let jobMatch = text.firstMatch(of: job) {
    let (wholeMatch) = jobMatch.output
    print(wholeMatch)
}

```

如果您编译并运行该程序，您将得到以下输出:

```
Writer

```

这意味着您的正则表达式匹配作业组件。如果你愿意，你可以用`text2`变量来测试它。

### `CharacterClass`

现在，让我们进入下一个部分:名字。名称由一个或多个单词字符、可选的空格和单引号字符定义。一般来说，名字可以比这个更复杂。但是对于我们的例子，这个定义就足够了。

这是您的名称组件的正则表达式:

```
let name = Regex {
    OneOrMore(
        ChoiceOf {
            CharacterClass(.word)
            CharacterClass(.whitespace)
            "'"
        }
    )
}

```

你看过`OneOrMore`和`ChoiceOf`。不过还有一个新的成分: [`CharacterClass`](https://developer.apple.com/documentation/regexbuilder/characterclass) 。在遗留 API 中，这相当于`\d`、`\s`、`\w`等等。它代表了一类人物。

`CharacterClass(.word)`表示单词字符，如 a、b、c、d 等。`CharacterClass(.whitespace)`表示空格、制表符等空白。除了`.word`和`.space`，你还有[几个角色职业](https://developer.apple.com/documentation/regexbuilder/characterclass/regexcomponent-implementations)。如果你想要一个数字`CharacterClass`，你可以写`CharacterClass(.digit)`来代表 1、2、3 等等。

因此，名称由一个或多个单词字符、任何空格和单引号字符组成。

您可以使用`text`变量尝试这个正则表达式:

```
if let nameMatch = "Karen O'Reilly".firstMatch(of: name) {
    let (wholeMatch) = nameMatch.output
    print(wholeMatch)
}

```

输出是您所期望的:

```
Karen O'Reilly

```

### 货币

现在，让我们进入下一个环节:付款。要匹配的文本是“$ 1000”或“$350”。您可以创建一个复杂的正则表达式，通过检查$符号和可选的逗号来匹配这两笔付款。然而，有一种更简单的方法:

```
let USlocale = Locale(identifier: "en_US")
let payment = Regex {
    One(.localizedCurrency(code: "USD", locale: USlocale))
}

```

您可以将`.localizedCurrency`与 USD 代码和美国地区一起使用。这样，如果您想匹配另一种货币的付款，例如“1，000”，您可以更改代码和区域设置。

正则表达式组件 [`One`](https://developer.apple.com/documentation/regexbuilder/one) 类似于`OneOrMore`。它表示一个表达式只出现一次。

您可以通过将以下代码添加到文件中，然后编译并运行该程序来查看结果:

```
if let paymentMatch = text.firstMatch(of: payment) {
    let (wholeMatch) = paymentMatch.output
    print(wholeMatch)
}

```

结果和之前的结果有点不一样。你会得到:

```
1000

```

结果不是`$1,000`，而是原始数`1000`。在后台，`RegexBuilder`将匹配的文本转换成一个整数。

### 日期

date 有一个等价的正则表达式。您想要解析日期组件`December 4, 2022`。你可以采取同样的方法。您不需要创建自定义的正则表达式来解析日期。您可以通过添加以下代码来使用一个`date`正则表达式组件:

```
let date = Regex {
    One(.date(.long, locale: USlocale, timeZone: .gmt))
}

```

这一次，您将`.date`与`.long`参数一起使用，使用相同的地区和 GMT 时区。您想要解析的日期“2022 年 12 月 4 日”是长格式的。如果使用不同格式的日期，您将使用不同的参数。

现在，您应该通过添加以下代码并运行程序来测试它:

```
if let dateMatch = text.firstMatch(of: date) {
    let (wholeMatch) = dateMatch.output
    print(wholeMatch)
}

```

结果是日期格式，而不是精确的字符串:

```
2022-12-04 00:00:00 +0000

```

正如支付案例一样，`RegexBuilder`将匹配的文本转换成日期。

## 捕获匹配的文本

现在，您想要组合所有的`RegexBuilder`代码来匹配全文。您可以堆叠所有的`Regex`块:

```
let separator = Regex { "/" }
let regexCode = Regex {
    job
    separator
    name
    separator
    payment
    separator
    date
}

```

所以你可以给一个变量一个子集正则表达式，并在一个更大的`Regex`块中使用它。

那么你应该用两个文本来测试它:

```
if let match = text.firstMatch(of: regexCode) {
    let (wholeMatch) = match.output
    print(wholeMatch)
}

if let match2 = text2.firstMatch(of: regexCode) {
    let (wholeMatch) = match2.output
    print(wholeMatch)
}

```

输出是完美的:

```
Writer/Arjuna Sky Kok/$1,000/December 4, 2022
Illustrator/Karen O'Reilly/$350/November 30, 2022

```

但是我们并不满意，因为我们想要捕捉每个组件，而不是整个组件。添加以下代码:

```
let regexCodeWithCapture = Regex {
    Capture {
        job
    }
    separator
    Capture {
        name
    }
    separator
    Capture {
        payment
    }
    separator
    Capture {
        date
    }
}

```

我们把想要捕获的组件放在 [`Capture`](https://developer.apple.com/documentation/regexbuilder/capture) 块中。在这种情况下，我们将四个组件放入块中。

这样，当将文本与正则表达式匹配时，您可以访问捕获的组件。在传统的`Regex` API 中，我们称之为反向引用。添加以下代码以获取捕获的组件:

```
if let matchWithCapture = text.firstMatch(of: regexCodeWithCapture) {
    let (wholeMatch) = matchWithCapture.output
    print(wholeMatch.0)
    print(wholeMatch.1)
    print(wholeMatch.2)
    print(wholeMatch.3)
    print(wholeMatch.4)
}

```

编译并运行该程序，您将得到以下输出:

```
Writer/Arjuna Sky Kok/$1,000/December 4, 2022
Writer
Arjuna Sky Kok
1000
2022-12-04 00:00:00 +0000

```

`0`方法指的是完全匹配。`1`方法指向第一个被捕获的组件，即作业组件。然后`2`是名字，`3`是付款，`4`是日期。您没有`5`方法，因为您只捕获了四个组件。

## 结论

在本文中，您学习了如何使用`RegexBuilder`编写正则表达式。您首先使用旧的 API 编写了一个正则表达式，然后将其转换为新的语法。这展示了正则表达式是如何变得更容易阅读的。您已经回顾了一些概念，如量词、选择、字符类、货币和日期。最后，您捕获了正则表达式的组件。

这篇文章只是触及了`RegexBuilder`的皮毛。有些东西你还没有学会，比如重复行为和使用`TryCapture`捕获组件。你也可以在的[文档中了解`RegexBuilder` API 的发展。这篇文章的代码可以在这个](https://github.com/apple/swift-evolution/blob/main/proposals/0351-regex-builder.md) [GitHub 库](https://github.com/arjunaskykok/getting-started-with-regexbuilder)上找到。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)