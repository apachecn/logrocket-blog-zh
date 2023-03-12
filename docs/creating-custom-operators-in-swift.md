# 在 Swift 中创建自定义操作符

> 原文：<https://blog.logrocket.com/creating-custom-operators-in-swift/>

## 目录

运算符是任何编程语言的基本构造之一。它们被表示为符号并具有各种相关的属性，理解它们对于掌握任何编程语言都是至关重要的。

在本文中，我们将了解 Swift 附带的一些操作符，并创建我们自己的操作符。

## Swift 中的操作员是什么？

在我们开始研究 Swift 为我们提供的不同运营商并创建自定义运营商之前，让我们先了解一些与它们相关的基本术语。
这里有一个例子:

```
let a = 5
let b = 10
let sum = a + b

```

在这里，我们可以看到一些被定义的变量和常量，以及一些符号(例如，`=`和`+`)。让我们在这里定义两个术语:

*   运算符:用于执行逻辑、计算或赋值操作的任何符号。在上面的例子中，`=`用于赋值，`+`用于加法，因此是运算符
*   操作数:被执行操作的变量，例如，在第 1 行我们有`a`作为操作数，在第 3 行，我们有`a`和`b`作为两个操作数，操作符`+`被应用到这两个操作数上

## Swift 中的操作员类型

有三种不同类型的运算符，它们由它们所处理的操作数的数量来定义。

1.  一元运算符:只处理一个操作数的运算符(例如，`=`和`!`)
2.  二元运算符:对两个操作数进行运算，如`+`、`-`、`*`等。
3.  三元运算符:作用于三个操作数，例如`?:`

## 运算符符号

符号定义了运算符与操作数一起使用时的位置。又有三种类型:

*   `Infix`:操作数之间使用运算符时。二元和三元运算符总是`infixed`
*   `Prefix`:运算符用在操作数之前，如`++`、`--`、`!`
*   `Postfix`:运算符用在操作数之后，如`…`

## Swift 中的运算符优先级和结合性

在 Swift 中使用运算符时，您还应该知道运算符执行的优先顺序。运算符优先级只对`infix`运算符有影响，因为它们作用于多个操作数。

例如，如果一个表达式中有多个操作符，Swift 需要知道哪个操作符需要首先执行。

```
var result = 4 + 6 * 2 // 6*2 is done first which yields 12, then we do 4 + 12 = 16

```

在上面的例子中，`*`比`+`更优先，这就是为什么`6*2`在`4+6`之前执行。

如果在一个表达式中使用了两个具有相同优先级的操作符，它们将依赖于它们的结合性。结合性定义了当操作符的优先级相同时，表达式开始解析的方向。

关联性有两种类型:

1.  `left`:表示先解析左边的表达式
2.  `right`:这意味着右边的表达式将首先被解析

例如，我们有以下语句:

```
let result = 5 + 10 - 2 //13

```

因为`+`和`-`具有相同的优先级，所以 Swift 退回到操作符的结合性。`+`和`-`具有`left`的结合性，因此我们从左边解析表达式，以便首先计算`+`。

以下是 Swift 遵循的优先顺序表。该表按照优先级递减的顺序列出了优先级。

| **优先名** | **操作员包括** |
| --- | --- |
| `BitwiseShiftPrecendence` | `<<`、`>>`、`&<<`、`&>>` |
| `MultiplicationPrecedence` | `*`、`/`、`%`、`&`、`&*` |
| `AdditionPrecedence` | `+`、`-`、`&#124;`、`^`、`&+`、`&-` |
| `RangeFormationPrecedence` | `..<`，`...` |
| `CastingPrecedence` | `is`、`as`、`as?`、`as!` |
| `NilCoalescingPrecedence` | `??` |
| `ComparisonPrecedence` | `<`、`<=`、`>`、`>=`、`==`、`!=`、`===`、`!==`、`~=`、`.<`、`.<=`、`.>`、`.>=`、`.==`、`.!=` |
| `LogicalConjunctionPrecedence` | `&&`，`.&` |
| `LogicalDisjunctionPrecedence` | `&#124;&#124;`、`.&#124;`、`.^` |
| `TernaryPrecedence` | `?:` |
| `AssignmentPrecedence` | `=`、`*=`、`/=`、`%=`、`+=`、`-=`、`<<=`、`>>=`、`&=`、`&#124;=`、`^=`、`&*=`、`&+=`、`&-=`、`&<<=`、`&>>=`、`.&=`、`.&#124;=`、`.^=` |

既然我们已经了解了什么是 Swift 运营商以及如何使用它们的基本知识，那么让我们来看看 Swift 附带的受欢迎的运营商。

## Swift 中的常见操作员

Swift 附带的最常见的运营商分为以下几类:

### 1.赋值运算符

赋值运算符用于给常量或变量赋值。符号是`=`。

### 2.算术运算符

这些运算符用于执行基本的算术运算，如`+`、`-`、`*`、`/`、`%`。

### 3.逻辑运算符

逻辑运算符用于组合两个条件以给出单个布尔值输出，例如`!`、`&&`和`||`。

### 4.比较运算符

这些运算符用于比较数字并给出一个`true`或`false`输出:`>`、`<`、`==`、`>=`、`<=`和`!=`。

### 5.三元运算符

这个操作符是一个速记操作符，用于编写 if-else 条件。尽管它做了同样的工作，但将它视为 if-else 条件的替代并不是一个好主意，因为它妨碍了可读性。

因此，当您有不同的代码块要在不同的条件下运行时，最好使用 if-else 块。对于更短、更简单的情况，请使用三元运算符。运算符`?:`的用法如下:

```
var value = 5 > 2 ? "5 is greater than 2" : "5 is less than 2"

```

### 6.零合并算子

这是一个在特定变量为零的情况下返回默认值的简写运算符。运算符符号为`??`，常用如下:

```
var identity: String? = nil
let nonNillableIdentity: String = identity ?? "Jane Doe" // since `identity` is nil, we return a default value of Jane Doe and store it in the nonNilalbleIdentity variable

```

### 7.范围运算符

这些运算符用于定义范围，例如`…`和`..<`。它们主要与数组/字符串一起使用来提取子数组/字符串。

```
var array = [1,2,3,4,5]
let a = array[1...3] // [2,3,4]
let b = array[1..<3] // [2,3]
let c = array[2...] // [3,4,5]
let d = array[...3] // [1,2,3,4]

```

有关所有基本操作符及其描述的列表，请阅读 [Swift 文档](https://docs.swift.org/swift-book/LanguageGuide/BasicOperators.html)。

## 在 Swift 中创建自定义操作员

现在我们已经看到了 Swift 附带的操作符，让我们创建自己的操作符。自定义运算符的定义通常有三个目的:

1.  定义一个全新的操作符，因为您认为符号的含义比有名字的函数更能表达操作符的功能
2.  定义一个运算符，该运算符存在于数字或字符串等基本数据类型中，但不存在于您定义的类或结构中
3.  您希望重写已经为您的类和结构存在的运算符，但是您需要它有不同的行为

让我们用例子来看看这三个目的。

### 使用新符号创建新运算符

假设您想要定义一个带有符号`△`的操作符，它计算一个给定了两条边的直角三角形的斜边。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
infix operator △
func △(lhs: Double, rhs: Double) -> Double {
    return sqrt(lhs*lhs + rhs*rhs)
}

var hypotenuse = 3△4 // 5

```

### 为自定义类或结构创建运算符

我们知道,`+`操作符用于将两个数字相加，但是假设我们想要添加名为`Velocity`的结构的两个实例，这是一个二维实体:

```
struct Velocity {
    var xVelocity: Double
    var yVelocity: Double
}

```

如果我们运行下面的命令，您会得到一个编译器错误:

```
let velA = Velocity(xVelocity: 2, yVelocity: 4)
let velB = Velocity(xVelocity: 2, yVelocity: 4)
let velC = velA + velB // Binary operator + cannot be applied to two Velocity operands

```

在这种情况下，定义一个负责添加两个`Velocity`实例的`+`操作符是有意义的:

```
extension Velocity {
    static func +(lhs: Velocity, rhs: Velocity) -> Velocity {
            return Velocity(xVelocity: lhs.xVelocity + rhs.xVelocity,
                            yVelocity: lhs.yVelocity + rhs.yVelocity)
    }
}

```

现在，前面的语句将正确执行。

### 覆盖已经存在的用于类/结构的 Swift 运算符

假设您有一个用于定义超市商品的结构。

```
struct Item: Equatable {
    let id = UUID()
    let itemName: String
    let itemType: String
    let itemPrice: Double
}

```

当我们需要比较两个项目时，例如，如果两个项目具有相同的`itemName`、`itemType`和`itemPrice`，我们需要认为它们相等，而不管它们的`id`是什么。

如果您运行下面这段代码，您会发现这两个变量不相等:

```
let detergentA = Item(itemName: "Tide", itemType: "Detergent", itemPrice: 56.5)
let detergentB = Item(itemName: "Tide", itemType: "Detergent", itemPrice: 56.5)
let areDetergentsEqual = detergentA == detergentB // false

```

原因是实例`detergentA`和`detergentB`具有不同的`ids`。在这种情况下，我们需要覆盖`==`操作符和自定义逻辑来确定等式。

```
extension Item {
    static func ==(lhs: Item, rhs: Item) -> Bool {
        return lhs.itemName == rhs.itemName &&
               lhs.itemPrice == rhs.itemPrice &&
               lhs.itemType == rhs.itemType
    }
}

```

现在我们知道了定义自定义操作符的不同场景，让我们来定义自定义操作符。

## 在 Swift 中定义自定义操作员

自定义操作符的定义就像 Swift 中的函数定义一样。您可以定义两种类型的运算符:

1.  全局运算符
2.  特定于类/结构的运算符

该定义如下所示:

```
func <operator symbol>(parameters) { 
 // operator logic
}

```

根据是`infix`还是`prefix` / `postfix`运算符，您可以定义不同类型的参数。

如果是一个`infix`操作符，你可以有两个参数，如果是一个`prefix` / `postfix`操作符，你可以有一个参数。

### 全局运算符

对于全局操作符，我们首先用符号关键字(`infix`、`prefix`或`postfix`)和`operator`关键字定义操作符，如下所示:

```
<notation> operator <operator symbol>

```

这里有一个例子:

```
infix operator ^ // the operator is an infix operator with the symbol ^

```

### 类/结构的运算符

当我们想为一个类或结构定义一个操作符时，我们需要将操作符函数定义为一个静态函数。如果操作符是一个`infix`操作符(即函数签名中有两个参数)，符号参数是可选的，否则需要指定`prefix`或`postfix`:

```
extension <class name> {
  static <notation> func ^(parameters) { // note that this needs to be static function
    // operator logic
  }
}

```

鉴于我们已经研究了如何定义像`+`和`==`这样的基本自定义操作符，让我们回顾一些例子，看看如何定义自定义复合操作符或改变参数本身的操作符。

我们将通过为前面的`Velocity`结构定义一个定制的复合算术运算符来实现这一点:

```
extension Velocity {
    static func +=(lhs: inout Velocity, rhs: Velocity) { // note that the first parameter is an inout variable
        lhs.xVelocity = lhs.xVelocity + rhs.xVelocity
        lhs.yVelocity = lhs.yVelocity + rhs.yVelocity
    }
}

```

现在让我们执行下面的语句:

```
var velA = Velocity(xVelocity: 5, yVelocity: 5)
var velB = Velocity(xVelocity: 5, yVelocity: 5)
velA += velB
print(velA) // Velocity(xvelocity: 10.0, yVelocity: 10.0)

```

现在，我们将指定自定义操作符的符号。

## 在 Swift 中指定自定义运算符的符号

为了定义自定义操作符的符号，对应的符号有三个关键字:

1.  `infix`
2.  `postfix`
3.  `prefix`

当定义一个全局操作符时，你需要首先定义操作符的符号，然后定义它的函数。注意，这不是一个强制的步骤，但是如果你想定义全局操作符或者希望给它分配一个优先级，这是必要的(我们将在下一节讨论)。

让我们再来看看斜边算子，它被定义为全局算子。您可以看到，我们首先用符号和符号定义运算符，然后实现其逻辑:

```
infix operator △ // define the operator with its notation and symbol
func △(lhs: Double, rhs: Double) -> Double { // implement the operator logic
    return sqrt(lhs*lhs + rhs*rhs)
}

var hypotenuse = 3△4 // 5

```

在为自己的类/结构定义运算符时，可以在方法定义中直接使用 notation 关键字。例如，让我们也为`Velocity`定义一个求反操作符，它对`xVelocity`和`yVelocity`属性求反。该操作员将被定义为`prefix`操作员:

```
extension Velocity {
    // defining a prefix negation operator
    static prefix func -(operand: Velocity) -> Velocity {
        return Velocity(xVelocity: -val.xVelocity, yVelocity: -val.yVelocity)
    }
}

let vel = Velocity(xVelocity: 4, yVelocity: 4)
print(-vel) // xVelocity: -4, yVelocity: -4

```

## 设置自定义 Swift 运营商的优先级

还可以使用优先级关键字定义运算符的优先级，如上面的优先级表中所述。这通常在定义步骤中完成:

```
infix operator ~: AdditionPrecedence // now this operator's priority is the same at the operators mentioned in the AdditionPrecedence
func ~(lhs: Int, rhs: Int) -> Double {
  return sqrt(Double(lhs*lhs-rhs*rhs))
}

```

这样，编译器就知道哪个操作符需要先执行。如果没有指定优先级，则默认为高于`TernaryPrecedence`的`DefaultPrecedence`。

## 结论

这让我们得出结论，Swift 中有哪些运算符，以及您如何创建自己的运算符。尽管它们使用起来非常简单，但是理解它们是非常重要的，因为它们是任何编程语言最基础的构造之一。

您应该熟悉基本运算符，因为您将在代码中频繁使用它们，并且，当涉及到自定义运算符时，仅当符号的原始含义对您有意义时才定义它们。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)