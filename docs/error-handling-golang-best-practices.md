# Go 中的错误处理:最佳实践- LogRocket 博客

> 原文：<https://blog.logrocket.com/error-handling-golang-best-practices/>

***编者按**:这篇文章最后一次更新和准确性验证是在 2022 年 11 月 18 日。*

处理 Go 中的错误需要一种不同于其他主流编程语言的传统方法，比如 JavaScript，它使用了`try...catch`语句，或者 Python 使用了它的`try… except`块。开发人员经常误用 Go 的特性来处理错误。

在本文中，我们将考虑您在处理 Go 应用程序中的错误时应该遵循的最佳实践。为了理解本文，您需要对 Go 如何工作有一个基本的了解。我们开始吧！

## 空白标识符

[空白标识符](https://golang.org/doc/effective_go.html#blank)是一个匿名占位符。您可以像在声明中使用任何其他标识符一样使用它，但是它不引入绑定。空白标识符提供了一种方法来忽略赋值中的左手值，并避免程序中未使用的导入和变量周围的编译器错误。

将错误赋给空白标识符而不是正确处理它们是不安全的，这意味着您已经决定显式忽略已定义函数的值:

```
result, _ := iterate(x,y)

if value > 0 {
  // ensure you check for errors before results.
}

```

这背后的基本原理可能是您不希望函数出错。但是，这可能会在程序中产生层叠效果。最佳实践是尽可能处理错误。

## 通过多个返回值处理错误

处理错误的一种方法是利用 Go 中的函数支持多个返回值这一事实。因此，您可以在定义函数的结果旁边传递一个错误变量:

```
func iterate(x, y int) (int, error) {

}

```

在上面的代码片段中，如果我们认为函数有可能失败，我们必须返回预定义的`error`变量。`error`是在 Go 的[内置包](https://pkg.go.dev/builtin@go1.19.2)中声明的接口类型，其零值为`nil`:

```
type error interface {
   Error() string
 }

```

通常，返回一个`error`意味着有问题，返回`nil`意味着没有错误:

```
result, err := iterate(x, y)
 if err != nil {
  // handle the error appropriately
 } else {
  // you're good to go
 }

```

每当调用`iterate`函数并且`err`不等于`nil`时，返回的错误应该得到适当的处理。

一种选择是创建一个重试或清理机制的实例。用这种方式处理错误的唯一缺点是没有 Go 编译器的强制。您必须决定您创建的函数如何返回错误。

您可以定义一个`error`结构并将它放在返回值的位置。一种方法是使用内置的`errorString`结构。你也可以在 [Go 的源代码](https://go.dev/src/errors/errors.go)中找到下面的代码:

```
package errors

 func New(text string) error {
     return &errorString {
         text
     }
 }

 type errorString struct {
     s string
 }

 func(e * errorString) Error() string {
     return e.s
 }

```

在上面的代码示例中，`errorString`嵌入了一个由`Error`方法返回的`string`。要创建自定义错误，您必须定义您的`error`结构，并使用[方法集](https://golang.org/ref/spec#Method_sets)将一个函数关联到您的结构:

```
// Define an error struct
type CustomError struct {
    msg string
}
// Create a function Error() string and associate it to the struct.
func(error * CustomError) Error() string {
    return error.msg
}
// Then create an error object using MyError struct.
func CustomErrorInstance() error {
    return &CustomError {
        "File type not supported"
    }
}

```

然后，新创建的自定义错误可以被重新构造，以使用内置的`error`结构:

```
 import "errors"
func CustomeErrorInstance() error {
    return errors.New("File type not supported")
}

```

内置的`error`结构的一个限制是它没有堆栈跟踪，这使得很难定位错误发生的位置。在打印出来之前，错误可能会通过许多函数。

要解决这个问题，您可以安装`[pkg/errors](https://github.com/pkg/errors/blob/master/errors.go)`包，它提供了基本的错误处理原语，比如堆栈跟踪记录、错误包装、解包和格式化。要安装此软件包，请在您的终端中运行以下命令:

```
go get github.com/pkg/errors

```

当您需要添加堆栈跟踪或任何其他信息来使调试错误更容易时，使用`New`或`Errorf`函数来提供记录您的堆栈跟踪的错误。`Errorf`实现了 [`fmt.Formatter`](https://golang.org/pkg/fmt/#Formatter) 接口，让你使用`fmt`包符文、`%s`、`%v`和`%+v`格式化你的错误:

```
import(
    "github.com/pkg/errors"
    "fmt"
)
func X() error {
    return errors.Errorf("Could not write to file")
}

func customError() {
    return X()
}

func main() {
    fmt.Printf("Error: %+v", customError())
}

```

要打印堆栈跟踪而不是普通的错误消息，您必须在格式模式中使用`%+v`而不是`%v`。堆栈跟踪将类似于下面的代码示例:

```
Error: Could not write to file
main.X
 /Users/raphaelugwu/Go/src/golangProject/error_handling.go:7
main.customError
 /Users/raphaelugwu/Go/src/golangProject/error_handling.go:15
main.main
 /Users/raphaelugwu/Go/src/golangProject/error_handling.go:19
runtime.main
 /usr/local/opt/go/libexec/src/runtime/proc.go:192
runtime.goexit
 /usr/local/opt/go/libexec/src/runtime/asm_amd64.s:2471

```

## 推迟、恐慌和恢复

虽然 Go 没有例外，但它有一种类似的机制，称为[延迟、死机和恢复](https://blog.golang.org/defer-panic-and-recover)。Go 的思想是，在 JavaScript 中添加像`try/catch/finally`语句这样的异常会导致复杂的代码，并鼓励程序员将太多的基本错误(如无法打开文件)标记为异常。

你不应该像使用`throw/catch/finally`一样使用`defer/panic/recover`。您应该只在出现意外的、不可恢复的故障时保留它。

Defer 是一种将函数调用放入堆栈的语言机制。当主机函数完成时，每个延迟函数以相反的顺序执行，[，而不管是否调用了紧急](https://blog.logrocket.com/type-assertions-vs-type-conversions-go/)。延迟机制对于清理资源非常有用:

```
package main

import (
        "fmt"
)

func A() {
        defer fmt.Println("Keep calm!")
        B()
}
func B() {
        defer fmt.Println("Else...")
        C()
}
func C() {
        defer fmt.Println("Turn on the air conditioner...")
        D()
}
func D() {
        defer fmt.Println("If it's more than 30 degrees...")
}
func main() {
        A()
}

```

上面的代码将编译如下:

```
If it's more than 30 degrees...
Turn on the air conditioner...
Else...
Keep calm!

```

`panic`是停止正常执行流程的内置函数。当你在代码中调用`panic`时，意味着你已经决定你的调用者不能解决问题。因此，你应该只在极少数情况下使用`panic`,在那种情况下，你的代码或者任何集成你的代码的人继续下去都不安全。

下面的代码示例演示了`panic`的工作方式:

```
package main

import (
        "errors"
        "fmt"
)

func A() {
        defer fmt.Println("Then we can't save the earth!")
        B()
}
func B() {
        defer fmt.Println("And if it keeps getting hotter...")
        C()
}
func C() {
        defer fmt.Println("Turn on the air conditioner...")
        Break()
}
func Break() {
        defer fmt.Println("If it's more than 30 degrees...")
        panic(errors.New("Global Warming!!!"))

}
func main() {
        A()
}

```

上面的示例将编译如下:

```
If it's more than 30 degrees...
Turn on the air conditioner...
And if it keeps getting hotter...
Then we can't save the earth!
panic: Global Warming!!!

goroutine 1 [running]:
main.Break()
        /tmp/sandbox186240156/prog.go:22 +0xe0
main.C()
        /tmp/sandbox186240156/prog.go:18 +0xa0
main.B()
        /tmp/sandbox186240156/prog.go:14 +0xa0
main.A()
        /tmp/sandbox186240156/prog.go:10 +0xa0
main.main()
        /tmp/sandbox186240156/prog.go:26 +0x20

Program exited: status 2.

```

如上图，当`panic`被使用且未被处理时，执行流程停止，所有延迟的函数以相反的顺序执行，并打印堆栈跟踪。

您可以使用内置的`recover`函数来处理`panic`并返回紧急调用传递的值。`recover`必须总是在`defer`函数中被调用，否则，它将返回`nil`:

```
package main

import (
        "errors"
        "fmt"
)

func A() {
        defer fmt.Println("Then we can't save the earth!")
        defer func() {
                if x := recover(); x != nil {
                        fmt.Printf("Panic: %+v\n", x)
                }
        }()
        B()
}
func B() {
        defer fmt.Println("And if it keeps getting hotter...")
        C()
}
func C() {
        defer fmt.Println("Turn on the air conditioner...")
        Break()
}
func Break() {
        defer fmt.Println("If it's more than 30 degrees...")
        panic(errors.New("Global Warming!!!"))

}
func main() {
        A()
}

```

正如您在上面的代码示例中看到的，`recover`防止了整个执行流程停止。我们添加了一个`panic`函数，因此编译器将返回以下内容:

```
If it's more than 30 degrees...
Turn on the air conditioner...
And if it keeps getting hotter...
Panic: Global Warming!!!
Then we can't save the earth!

Program exited.

```

要将错误报告为返回值，必须在调用`panic`函数的同一个 goroutine 中调用 [`recover`函数，从`recover`函数中检索一个错误结构，并将其传递给一个变量:](https://blog.logrocket.com/concurrency-patterns-golang-waitgroups-goroutines/)

```
package main

import (
        "errors"
        "fmt"
)

func saveEarth() (err error) {

        defer func() {
                if r := recover(); r != nil {
                        err = r.(error)
                }
        }()
        TooLate()
        return
}
func TooLate() {
        A()
        panic(errors.New("Then there's nothing we can do"))
}

func A() {
        defer fmt.Println("If it's more than 100 degrees...")
}
func main() {
        err := saveEarth()
        fmt.Println(err)
}

```

每个延迟函数都将在函数调用之后、return 语句之前执行。因此，您可以在执行 return 语句之前设置返回的变量。上面的代码示例将编译如下:

```
If it's more than 100 degrees...
Then there's nothing we can do

Program exited.

```

## 错误包装

以前，Go 中的错误包装只能通过像`pkg/errors`这样的包来访问。然而， [Go v1.13 引入了对错误包装](https://go.dev/doc/go1.13)的支持。

根据[发布说明](https://golang.org/doc/go1.13#error_wrapping):

> 一个错误`e`可以通过提供一个返回`w`的`Unwrap`方法来包装另一个错误`w`。程序可以使用`e`和`w`，允许`e`为`w`提供额外的上下文或者重新解释它，同时仍然允许程序基于`w`做出决定。

为了创建包装的错误，`fmt.Errorf`有一个`%w`动词，为了检查和解开错误，`error`包中增加了几个函数。

`errors.Unwrap`基本上是检查和暴露程序中的潜在错误。如果`Err`的类型包含返回错误的`Unwrap`方法，它将返回在`Err`上调用`Unwrap`方法的结果。否则，`Unwrap`返回 0:

```
package errors

type Wrapper interface{
  Unwrap() error
}

```

下面是`Unwrap`方法的一个实现示例:

```
func(e*PathError)Unwrap()error{
  return e.Err
}

```

使用`errors.Is`功能，您可以将误差值与标记值进行比较。

该函数不是将 sentinel 值与一个错误进行比较，而是将其与错误链中的每个错误进行比较。它还对一个错误实现了一个`Is`方法，这样一个错误即使不是一个标记值也可以把它自己作为一个标记发布:

```
func Is(err, target error) bool

```

在上面的基本实现中，`Is`检查并报告`err`或其链中的任何`errors`是否等于目标值，即标记值。

`errors.As`函数提供了一种转换成特定错误类型的方法。它在错误链中寻找匹配 sentinel 值的第一个错误，如果找到，它将 sentinel 值设置为该错误值，返回`true`:

```
package main

import (
        "errors"
        "fmt"
        "os"
)

func main() {
        if _, err := os.Open("non-existing"); err != nil {
                var pathError *os.PathError
                if errors.As(err, &pathError) {
                        fmt.Println("Failed at path:", pathError.Path)
                } else {
                        fmt.Println(err)
                }
        }

}

```

你可以在 [Go 的源代码](https://golang.org/pkg/errors/#As)中找到这段代码。以下是编译结果:

```
Failed at path: non-existing

Program exited.

```

如果错误的具体值可赋给标记值所指向的值，则该错误与标记值匹配。如果 sentinel 值不是指向实现 error 的类型或任何接口类型的非零指针，则`As`将会出错。如果`err`为`nil`，则`As`返回 false。

## 摘要

Go 社区最近在支持各种编程概念方面取得了令人印象深刻的进步，并引入了更简洁和简单的方法来处理错误。你对如何处理或处理围棋程序中可能出现的错误有什么想法吗？请在下面的评论中告诉我。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)