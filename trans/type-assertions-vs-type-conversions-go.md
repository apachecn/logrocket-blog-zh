# Go - LogRocket 博客中的类型断言与类型转换

> 原文：<https://blog.logrocket.com/type-assertions-vs-type-conversions-go/>

在本文中，我们将使用一些例子来探索 Go 中的类型断言和类型转换操作。

如果您想在进行过程中运行这些片段，您需要:

## 您将在 Go 中了解到什么是类型转换

在本文的学习过程中，您将了解到以下关于 Go 中类型转换的主要主题:

## 什么是类型断言？

类型断言(顾名思义)用于断言给定变量的类型。在 Go 中，这是通过检查空接口变量的底层类型来完成的。

## 什么是类型转换？

类型转换是将变量从一种类型转换为另一种指定类型的过程。例如，我们可以将 int 值转换为 float64。

## Go 中的类型断言

在 Go 中，类型断言的语法是`t := i.(type)`。下面是完整类型断言操作的一个片段:

```
// type-lessons.go

package main

func main() {
  var i interface{} = "a string"
  t := i.(string) // "a string"
}

```

类型断言操作由三个主要元素组成:

*   `i`，这是我们声明类型的变量。这个变量必须定义为一个接口
*   `type`，这是我们声明变量的类型(比如 string，int，float64 等)
*   `t`，如果我们的类型断言是正确的，这是一个存储变量`i`的值的变量

应该进一步注意，当类型断言不成功时，它会抛出一个错误，在 Go 中称为“panic”。

从上面我们可以看到，要使类型断言成功，我们需要首先指定正确的类型，但是在一些情况下，我们可能不确定类型，并且希望防止在我们的脚本中出现未被注意到的混乱。

我们可以用两种方式处理类型断言中的不确定性，现在我们来看看。

### **来自类型断言的第二个值**

第一种选择是在类型断言的左边使用第二个变量。类型断言可以返回两个值；第一个值(和上面的`t`一样，是我们正在检查的变量的值)；第二个是断言是否成功的布尔指示符。

```
// type-lessons.go

...
var i interface{} = "a string"
t, ok := i.(string)

```

我们的第二个变量`ok`是一个布尔值，它保存我们的类型断言是否正确。因此，在我们上面的例子中，`ok`应该是`true`，因为`i`属于类型`string`。

但是，这个方法只对验证变量类型的直觉有用，不会引发混乱。如果我们不确定变量的类型，我们可以使用我们的第二个选项，这叫做类型切换。

### **型** **开关**

这类似于普通的 switch 语句，它切换变量的可能类型，而不是值的普通切换。这里，我们将从接口变量中提取类型，并在几个类型选项之间切换。

```
// type-lessons.go

package main

func main() {
  var testVar interface{} = 42.56

  switch t := testVar.(type) {
    case string: 
      fmt.Println("Variable is of type string!")
    case int:
      fmt.Println("Variable is of type int!")
    case float32:
      fmt.Println("Variable is of type float32!")
    default:
      fmt.Println("Variable type unknown")
  }
}

```

### **清空** **界面**

正如我们之前提到的，Go 中的类型断言只能在[接口](https://blog.logrocket.com/exploring-structs-interfaces-go/)上执行。你可能会问，什么是接口，什么是空接口，怎么会有类型？好吧，让我们来看看。

接口是变量类型的方法和属性的定义。空接口是在初始化之前不指定属性、方法和类型的接口。

例如:

```
var test interface{} = "help!" 

```

我们上面的`test`变量被定义为一个空接口，但是通过赋值获得了类型`string`。类型断言在接口变量上执行，以断言它们的基础类型。

### **类型**

我们已经讨论了空接口以及它们如何在初始化时获得它们的类型，但是你可能也想知道确切的类型是什么。

Go 中的类型定义了变量的存储方式以及变量可用的方法。在围棋中，有 string、rune、int、bool 等基本类型，其他类型也可以建立在这些基础上。

## 类型转换

我们已经讨论了什么是类型，现在让我们来讨论类型转换。

类型转换只是将一个值从一种类型转换为另一种类型，但在 Go 中有一个警告，即类型只能在相关或相似的类型之间转换。让我们看一个例子:

```
// type-conversion.go

package main

import (
  "fmt"
  "strings"
)

type myString string

func (m myString) capitalize() myString {
    capStr := strings.ToUpper(string(m))

    return myString(capStr)
}

func main() {
    fmt.Println("Hello World!")

    var m myString = "test"

    fmt.Println(m.capitalize())
}

```

在上面的例子中，我们使用基本类型`string`定义了一个类型`myString`。这意味着`myString`继承了`string`的数据结构，但是我们可以给它自己的方法，这是类型`string`所没有的，就像我们例子中的方法`capitalize`。

我们还可以在我们的例子中看到，我们能够将类型`myString`转换为`string`，并将`string`转换为`myString`。这是因为它们共享相似的数据结构。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

另一个可以显式转换的类型的例子是`int`到`float64`，反之亦然。这里有一个简单的例子:

```
// type-conversion-int.go

package main

import "fmt"

func main() {
  var simpleInt int = 3

  var simpleFloat float64 = 4.5

  // fmt.Println("This will throw an error: ", simpleInt + simpleFloat)

  fmt.Println("This will work correctly: ", simpleInt + int(simpleFloat))

  fmt.Println("This will work correctly too: ", float64(simpleInt) + simpleFloat)
}

```

> 如果尝试，我们代码片段的第 12 行将失败，因为 Go 不允许对不匹配的类型进行数值运算。

在上面的例子中，我们能够在`int`和`float64`之间进行显式转换，就像我们能够在`myString`和`string`之间进行转换一样，因为它们都有相似的数据结构——但是我们不能将`string`转换为`int`。

## 结论

类型断言和类型转换之间一个非常显著的区别是各自的语法——类型断言的语法是`variable.(type)`，而类型转换的语法是`type(variable)`。

然而，除此之外，我们看到类型断言依赖于一个接口变量，该接口变量接受通过值赋值的类型，以便提取底层类型，而类型转换依赖于类型的数据结构的相似性，以便它们可以转换。