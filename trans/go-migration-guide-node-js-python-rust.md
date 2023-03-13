# Go 迁移指南:Node.js、Python 和 Rust

> 原文：<https://blog.logrocket.com/go-migration-guide-node-js-python-rust/>

Go 编程语言以其表达能力、简单的语法和其他特性而闻名。Go 的主要目标是构建可伸缩的、可靠的软件系统，因为它的性能、并发支持、简单性以及通过非世代并发、三色标记和清除垃圾收集器的高效内存管理。

多年来，Go 已经在许多领域获得了广泛采用，尤其是服务器端开发。出于可伸缩性和其他原因，许多公司和开发人员已经转向使用其他语言开发他们的应用程序。Go 还提供了跨一些支持语言的交叉编译，如 C 和 [C++](https://blog.logrocket.com/exploring-carbon-new-superset-c-plus-plus/) ，并且有许多工具可以将其他语言交叉编译到 Go，反之亦然。

迁移到一种新的编程语言可能很复杂，这是一个需要仔细规划和考虑的过程；然而，成功的迁移可能证明对您的应用有益。

本文概述了从 [Node.js、Python](https://blog.logrocket.com/node-js-vs-python-how-to-choose-the-best-technology-develop-backend/) 或 [Rust to Go](https://blog.logrocket.com/when-to-use-rust-when-to-use-golang/) 的迁移过程。我们还将讨论成功迁移的最佳实践和技巧。

*向前跳转:*

## 迁移过程

在语言之间进行迁移之前，您首先要考虑的事情之一是语言特性和您想要利用的特性之间的权衡。

移植代码库不仅仅是重写或翻译代码库以获得相同的功能。各种编程语言有不同的特性，并在多个特性上进行权衡。

例如，大多数垃圾收集语言都以内存管理的快速访问为代价，这对于构建像操作系统和浏览器这样的低级软件可能很重要。这里有几个可行的步骤来迁移你现有的代码库。

### 将您现有的代码库迁移到 Go

首先，您需要分析您当前的代码库，以识别迁移中可能出现的潜在问题。比如第三方库支持，功能支持等等。下一步是规划您的迁移过程。

这一步需要您为迁移过程划分或创建一个时间表，以便您的代码库被正确地迁移，而不会破坏现有的代码，并且迁移的需求是可用的。

一旦满足了所有要求，您就可以将现有的代码转换或交叉编译成您选择的语言了——在这种情况下，开始吧。Go 生态系统中有自动和手动代码转换的工具，比如 [gopherjs](https://github.com/gopherjs/gopherjs) 或者 [gopy](https://github.com/go-python/gopy) 。

在转换/交叉编译成目标语言之后，您需要测试代码，以确保所有的功能都按计划运行，并且实现了移植的本质。您可以执行[单元测试](https://blog.logrocket.com/a-deep-dive-into-unit-testing-in-go/)、[集成测试](https://blog.logrocket.com/advanced-unit-testing-patterns-go/)，以及性能和压力测试，以确保您的应用程序是稳定的、可伸缩的和可靠的。

典型迁移过程的最后阶段是部署。如果代码按计划运行并且迁移成功，您就可以部署到支持新语言的新的或现有的基础设施。

一个成功的迁移过程是这样的:迁移的最终目标得以实现，代码按计划运行，新特性得到利用。迁移可能很昂贵，尤其是对于大型代码库。因此，为了成功的迁移过程，您必须考虑上述步骤。

## 从 Python 迁移到 Go

Python 是一种高级的、垃圾收集的、解释的编程语言，应用于 web 开发、数据分析、科学计算和人工智能。像 Go 一样，Python 因其简单性和可读性而闻名，并且这种语言有一个由第三方库和框架组成的繁荣的生态系统。

将 Python 代码迁移到 Go 意味着您正在迁移一个基于 web 的应用程序。这是因为，在撰写本文时，Go 还没有因为其他应用程序而出名，比如 Python。从 Python 移植 AI 和数据分析代码库到 Go 可能有风险。

将 Python 代码库迁移到 Go 是提高应用程序可伸缩性的好方法。你可以利用 Go 的[并发特性](https://blog.logrocket.com/concurrency-patterns-golang-waitgroups-goroutines/)和数据类型。一旦您熟悉了这两种语言的语法，您就可以手动或自动地迁移您的代码。

### 手动迁移

对于手动迁移，您需要考虑数据类型和语法的差异，同时注意您想要利用的特性和编程语言的差异。

以下是一个 Python 函数的示例，该函数将一组数字作为参数，并返回这些数字的总和:

```
# python
def sum_numbers(numbers):
    total = 0
    for number in numbers:
        total += number
    return total

```

下面是代码的 Go 等价物:

```
// golang
func sumNumbers(numbers []int) int {
    total := 0
    for _, number := range numbers {
        total += number
    }
    return total
}

```

Go 不使用列表。该数据类型被称为[片](https://blog.logrocket.com/go-generics-past-designs-present-release-features/#examples-functions-go-generics:~:text=in%20Go%20today.-,Sorting%20a%20slice%20of%20any%20type,-//%20import%20the%20constraint)。此外，缩进在 Go 中不像在 Python 中那样重要。此外，Python 不像 Go 要求的那样需要函数的返回类型。这些是手动迁移需要考虑的众多因素之一。

### 自动迁移

对于自动迁移，您可以使用代码转换库来完成所有的代码重写，同时手动测试代码是否按预期工作。这里有几个库，可以用来转换 Python 代码。

[gopy 包](https://github.com/go-python/gopy)是一个 Go 库，用于在 Go 中编写 Python 扩展。该包提供了从 Python 调用 Go 函数的功能，反之亦然，以及许多其他功能。

在工作目录的终端中运行以下命令来安装 gopy 包:

```
# shell
go get github.com/go-python/gopy

```

下面是一个示例，说明如何使用 gopy 包将 Python 文件加载到包中，从而将 Python 代码转换为 Go 代码:

```
// golang
package main

import (
        "fmt"

        "github.com/go-python/gopy"
)

func main() {
        // Initialize the Python interpreter
        initErr := gopy.Initialize()
        if initErr != nil {
                fmt.Println(initErr)
                return
        }
        defer gopy.Finalize()

        // Convert the Python code to Go code
        convertedCode, convertErr := gopy.Convert("python_code.py")
        if convertErr != nil {
                fmt.Println(convertErr)
                return
        }

        fmt.Println(convertedCode)
}

```

在代码示例中，`Convert`函数将 Python 文件的路径作为参数，并以字符串形式返回等价的 Go 代码。

[py2go 包](https://github.com/jianyuan/py2go)提供了一个命令行实用程序，用于将 Python 代码转换为更广泛的 Python 结构(从类到函数和模块),并生成惯用的 go 代码。

[pigo 包](https://pkg.go.dev/github.com/esimov/pigo/core)包含将 Python 代码转换为 C 的工具，然后可以在编译期间编译并与 go 运行时链接，以生成 Go 可执行文件。它可以处理各种 Python 特性，比如类、函数和模块，并且可以生成有效的清晰的 C 代码。

请记住，在为任何用例部署代码之前，您必须测试代码转换的状态。

## 从 Rust 迁移到 Go

据[官网](https://www.rust-lang.org/)介绍，Rust 是一种流行的系统编程语言，运行速度快得惊人，防止 segfaults，保证线程安全。 [Rust](https://blog.logrocket.com/current-state-rust-web-frameworks/) 由 Mozilla 基金会于 2010 年开发，因其性能、可靠性和安全特性在开发者中赢得了声誉。

Rust 和 Go 都是用于构建可伸缩系统的现代编程语言。Rust 和 Go 用于构建服务于许多用户的可伸缩 web 应用程序。

从设计上来说，Rust 和 Go 在语法和其他方面都非常不同。Go 使用垃圾收集器管理内存，不像 Rust 使用[借用检查器](https://blog.logrocket.com/introducing-the-rust-borrow-checker/)来执行其[所有权规则](https://blog.logrocket.com/understanding-ownership-in-rust/)以实现灵活性和高效的内存管理。

语言的另一个[区别是 Rust 是静态类型的，而 Go 是动态类型的。最后，这些语言有不同的并发方法。Rust 使用一个由](https://blog.logrocket.com/comparing-elixir-rust-go/)[线程安全引用和互斥](https://blog.logrocket.com/deep-dive-concurrency-rust-programming-language/)组成的系统来确保安全的数据可访问性，而 Go 使用[通道](https://blog.logrocket.com/how-use-go-channels/)和 [goroutines](https://blog.logrocket.com/concurrency-patterns-golang-waitgroups-goroutines/) 进行多线程通信。

将 Rust 代码移植到 Go 代码的过程是相同的，反之亦然。您需要遵循上面讨论的迁移过程。

这是一个典型的 Rust 函数，你必须指定函数的返回类型。

```
// rust
fn add(x: i32, y: i32) -> i32 {
    x + y
}

```

`add`函数将两个`32-bit`整数相加，并返回等价的`32-bit`整数。

下面是`add`函数的 Go 等价函数，其中返回类型是从`return`语句中推断出来的:

```
// golang
func add(x int, y int) int {
    return x + y
}

```

你需要掌握这两种语言来进行手动和自动转换，因为这两种语言之间有很多差异。与 Rust 相比，Go 更容易学习和使用，因为 Rust 的学习曲线很陡。你可以用[这本初学者指南](https://blog.logrocket.com/getting-up-to-speed-with-rust/)温习铁锈。

不幸的是，在撰写本文时，还没有用于 Rust 和 Go 之间自动迁移的代码转换工具。然而，如果你想把你的 Go 代码库移植到 Rust，有一些像 [golang-rs](https://github.com/bk-rs/golang-rs) 这样的包可以帮助你把 [Go 结构](https://blog.logrocket.com/exploring-structs-interfaces-go/)转换成 [Rust 结构](https://blog.logrocket.com/fundamentals-for-using-structs-in-rust/)。

## 从 Node.js 迁移到 Go

JavaScript 是一种通用的编程语言，主要用于创建交互式动态网站和网络应用。JavaScript 可以在浏览器、移动设备和许多引擎上运行，这使得 JavaScript 适合[为许多环境构建前端和后端](https://blog.logrocket.com/building-web-components-with-webc-vanilla-javascript/)应用程序。

JavaScript 和 Go 都被用作后端语言，但这两种语言之间存在差异。比如 JavaScript 是动态类型的语言，而 Go 是静态类型的。Go 有对并发的支持，JavaScript 没有对并发操作的原生支持。

从 JavaScript 到 Go 的手动代码迁移过程与上面的过程相同。当您手动浏览每一行并重写相应的代码时，您将需要相当好的两种语言的专业知识。

JavaScript 中有一个将两个整数相加的函数:

```
// javascript
function add(x, y) {
  return x + y;
}

```

下面是用 Go 编写的相应代码:

```
// golang
func add(x int, y int) int {
  return x + y
}

```

Go 函数需要输入参数和返回类型。但是，JavaScript 使用半列，没有缩进。

对于自动代码迁移，您可以使用 gopherjs 包将 JavaScript 代码转换成 Go，反之亦然，尽管 JavaScript to Go 功能还不成熟。

在工作目录的终端中运行这个命令来安装 gopherjs 包:

```
# shell
go get github.com/gopherjs/gopherjs

```

下面是一个将 JavaScript 函数转换成 Go 代码的例子:

```
// golang
import (
        "fmt"
        "github.com/gopherjs/gopherjs/js"
)

func main() {
        // Convert the JavaScript code to Go code using GopherJS
        convertedCode := js.Global.Get("eval").Invoke("(function() { return 'Hello, World!'; })()")

        // Print the result of the converted code
        fmt.Println(convertedCode.String()) // Output: "Hello, World!"
}

```

该示例使用 JavaScript 中的`eval`函数执行一个 JavaScript 函数，该函数返回字符串“Hello，World！”与`fmt`包装一起印刷在控制台上。

## 结论

本文是 Rust、Node.js 和 Python 编程语言的迁移指南。您了解了迁移过程以及语言和 Go 之间的差异，并且概述了使用一些 Go 包的自动代码迁移。

在我看来，代码迁移过程中最关键的一步是测试代码的错误，你不应该为了避免破坏现有的功能而跳过这一步。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.