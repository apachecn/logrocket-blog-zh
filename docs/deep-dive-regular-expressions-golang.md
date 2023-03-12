# 使用 Golang 深入研究正则表达式

> 原文：<https://blog.logrocket.com/deep-dive-regular-expressions-golang/>

正则表达式是软件开发中每种编程语言的关键特征。当您需要创建将输入值与模式进行比较的数据验证逻辑时，这很方便。Golang 附带了一个内置的 [regexp](https://pkg.go.dev/regexp) 包，允许你编写任何复杂度的正则表达式。

在本文中，我们将讨论以下内容:

## 正则表达式函数的基本匹配

regexp 包提供了许多处理模式匹配的函数。在本文中，我们将对处理模式匹配的基本和最有用的函数进行实验。

### `MatchString`

`MatchString`函数检查输入字符串是否包含正则表达式模式的任何匹配。它接受一个正则表达式模式和一个字符串作为参数，如果匹配就返回`true`。如果模式编译失败，将返回错误。

以下是上述解释的一个片段:

```
package main
import (
    "fmt"
    "regexp"
)
func main() {
    inputText := "I love new york city"
    match, err := regexp.MatchString("[A-z]ork", inputText)
    if err == nil {
        fmt.Println("Match:", match)
    } else {
        fmt.Println("Error:", err)
    }
}

```

编译并执行上述代码，您将收到以下结果:

```
Match: true

```

### `FindStringIndex`

方法只返回第一个匹配，从左到右进行。匹配被表示为一个`int`片段，第一个值表示字符串中匹配的开始，第二个数字表示匹配的字符数。一个`nil`结果意味着没有找到匹配。

下面是一个`FindStringIndex`方法的用例示例:

```
package main
import (
    "fmt"
    "regexp"
)
func getSubstring(s string, indices []int) string {
    return string(s[indices[0]:indices[1]])
}
func main() {
    pattern := regexp.MustCompile("H[a-z]{4}|[A-z]ork")
    welcomeMessage := "Hello guys, welcome to new york city"
    firstMatchIndex := pattern.FindStringIndex(welcomeMessage)
    fmt.Println("First matched index", firstMatchIndex[0], "-", firstMatchIndex[1],
        "=", getSubstring(welcomeMessage, firstMatchIndex))
}

```

这里，我们编写了一个定制的`getSubstring`函数，它返回一个由来自`int`片的值和输入字符串组成的子字符串。regexp 包有内置的`FindString`函数，可以达到同样的效果。

编译并执行上述代码，您将收到以下结果:

```
First matched index 0 - 5 = Hello

```

### `FindString`

这个方法只返回第一个匹配的字符串，按照编译后的模式从左到右进行。

如果不匹配，将返回一个空字符串。如果没有匹配，返回值是一个空字符串，但是如果正则表达式成功匹配一个空字符串，返回值也是空的。如果您需要区分这些情况，请使用`FindStringIndex`或`FindStringSubmatch`方法。

下面是一个`FindString`方法的用例示例:

```
package main
import (
    "fmt"
    "regexp"
)
func main() {
    pattern := regexp.MustCompile("H[a-z]{4}|[A-z]ork")
    welcomeMessage := "Hello guys, welcome to new york city"
    firstMatchSubstring := pattern.FindString(welcomeMessage)
    fmt.Println("First matched substring:", firstMatchSubstring)
}

```

编译并执行上述代码，您将收到以下结果:

```
First matched substring: Hello

```

### `FindAllString(s string, n int)`

该方法接受一个字符串和`int`参数，并返回一个字符串片，该字符串片包含通过输入字符串中的编译模式在输入字符串中找到的所有匹配。最大匹配数由`int`参数`max`指定，`-1`表示没有限制。如果没有匹配，则返回一个`nil`结果。

下面是一个`FindAllString`方法的用例示例:

```
package main
import (
    "fmt"
    "regexp"
)
func main() {
    pattern := regexp.MustCompile("H[a-z]{4}|[A-z]ork")
    welcomeMessage := "Hello guys, welcome to new york city"
    allSubstringMatches := pattern.FindAllString(welcomeMessage, -1)
    fmt.Println(allSubstringMatches)
}

```

编译并执行上述代码，您将收到以下结果:

```
[Hello york]

```

### `FindAllStringIndex(s string, n int)`

`FindAllStringIndex`方法是`FindStringIndex`的`All`版本。它接受一个字符串和`int`参数，并返回模式的所有连续匹配的`int`片段的一个片段。如果没有匹配，则返回零结果。

用`0`的`int`参数调用`FindAll`方法不会返回任何结果，用`1`的`int`参数调用会返回左起第一个匹配字符串的`int`片的一片。`-1`的`int`参数返回模式所有连续匹配的`int`切片的一个切片。

返回的切片包含两个`int`值，第一个值表示字符串中匹配的开始，第二个数字表示匹配的字符数。

下面是一个`FindAllStringIndex`方法的用例示例:

```
package main
import (
    "fmt"
    "regexp"
)
func getSubstring(s string, indices []int) string {
    return string(s[indices[0]:indices[1]])
}
func main() {
    pattern := regexp.MustCompile("H[a-z]{4}|[A-z]ork")
    welcomeMessage := "Hello guys, welcome to new york city"
    allIndices := pattern.FindAllStringIndex(welcomeMessage, -1)
    fmt.Println(allIndices)
    for i, idx := range allIndices {
        fmt.Println("Index", i, "=", idx[0], "-",
            idx[1], "=", getSubstring(welcomeMessage, idx))
    }
}

```

这里，我们编写了一个基本的 for 循环，它调用我们的自定义函数`getSubstring`，该函数返回一个由来自`int`片的值和输入字符串组成的子字符串。

编译并执行上述代码，您将收到以下结果:

```
\[[0 5\] [27 31]]
Index 0 = 0 - 5 = Hello
Index 1 = 27 - 31 = york

```

## 编译和重用正则表达式模式

Compile 方法编译正则表达式模式，以便可以在更复杂的查询中重用。

```
attern, compileErr := regexp.Compile("[A-z]ork")

```

以下是如何用 Golang 编写编译模式:

```
package main
import (
    "fmt"
    "regexp"
)
func main() {
    pattern, compileErr := regexp.Compile("[A-z]ork")
    correctAnswer := "Yes, I love new york city"
    question := "Do you love new york city?"
    wrongAnswer := "I love dogs"
    if compileErr == nil {
        fmt.Println("Question:", pattern.MatchString(question))
        fmt.Println("Correct Answer:", pattern.MatchString(correctAnswer))
        fmt.Println("Wrong Answer:", pattern.MatchString(wrongAnswer))
    } else {
        fmt.Println("Error:", compileErr)
    }
}

```

因为模式只需要编译一次，所以效率更高。`MatchString`函数由`RegExp`类型的实例定义，该实例由`Compile`函数返回。

编译模式还可以让您使用正则表达式的特性和技术。

编译并执行上述代码，您将收到以下结果:

```
Question: true
Correct Answer: true
Wrong Answer: false 

```

## 使用正则表达式拆分字符串

`Split`方法使用正则表达式的匹配来分割字符串。它接受一个字符串和`int`参数，并返回一部分分割的子字符串。`-1`的`int`参数返回模式的所有连续匹配的子字符串的切片。

### `Split(s string, n int)`

```
package main
import (
    "fmt"
    "regexp"
)
func main() {
    pattern := regexp.MustCompile("guys|york")
    welcomeMessage := "Hello guys, welcome to new york city"
    split := pattern.Split(welcomeMessage, -1)
    for _, s := range split {
        if s != "" {
            fmt.Println("Substring:", s)
        }
    }
}

```

这里，我们编写了一个基本的`for`循环来打印每个拆分的子串。

可以用其他数字代替`-1`进行更多的实验。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

编译并执行上述代码，您将收到以下结果:

```
[Hello  , welcome to new   city]
Substring: Hello 
Substring: , welcome to new 
Substring:  city

```

## 子表达式

通过允许访问正则表达式的各个部分，子表达式使得从匹配区域中检索子字符串变得更加容易。子表达式用括号表示。它可用于识别模式中重要的内容区域:

```
pattern := regexp.MustCompile("welcome ([A-z]*) new ([A-z]*) city")

```

### `FindStringSubmatch`

`FindStringSubmatch`方法是一个子表达式方法，它与`FindString`方法做同样的事情，但是额外返回由表达式匹配的子字符串:

```
package main
import (
    "fmt"
    "regexp"
)
func main() {
    pattern := regexp.MustCompile("welcome ([A-z]*) new ([A-z]*) city")
    welcomeMessage := "Hello guys, welcome to new york city"
    subStr := pattern.FindStringSubmatch(welcomeMessage)
    fmt.Println(subStr)

    for _, s := range subStr {
        fmt.Println("Match:", s)
    }
}

```

这里，我们定义了两个子表达式，模式的每个变量组件一个。

编译并执行上述代码，您将收到以下结果:

```
[welcome to new york city to york]
Match: welcome to new york city
Match: to
Match: york

```

## 命名子表达式

还可以命名子表达式，以简化结果输出的处理。

下面是如何命名一个子表达式:在括号内，添加一个问号，后面跟着一个大写的`P`，然后是尖括号内的名称。

以下是上述解释的一个片段:

```
pattern := regexp.MustCompile("welcome (?P<val1>[A-z]*) new (?P<val2>[A-z]*) city")

```

这里，子表达式被命名为`val1`和`val2`。

## 子表达式的 regexp 方法

### `SubexpNames()`

此方法返回一个包含子表达式名称的切片，子表达式的名称按定义顺序表示:

```
package main
import (
    "fmt"
    "regexp"
)
func main() {
    pattern := regexp.MustCompile("welcome (?P<val1>[A-z]*) new (?P<val2>[A-z]*) city")
    replaced := pattern.SubexpNames()
    fmt.Println(replaced)
}

```

编译并执行上述代码，您将收到以下结果:

```
[ val1 val2]

```

### `NumSubexp()`

此方法返回子表达式的数目:

```
package main
import (
    "fmt"
    "regexp"
)
func main() {
    pattern := regexp.MustCompile("welcome (?P<val1>[A-z]*) new (?P<val2>[A-z]*) city")
    replaced := pattern.NumSubexp()
    fmt.Println(replaced)
}

```

编译并执行上述代码，您将收到以下结果:

```
2

```

查看 Golang 文档，了解子表达式的更多 regexp 方法。

## 使用正则表达式替换子字符串

regexp 包提供了许多替换子字符串的方法。

### `ReplaceAllString(src, repl string)`

该方法接受字符串和一个`template`参数，并用指定的模板替换字符串的匹配部分(`s`)，该模板在包含在结果中之前被扩展以容纳子表达式:

```
package main
import (
    "fmt"
    "regexp"
)
func main() {
    pattern := regexp.MustCompile("welcome (?P<val1>[A-z]*) new (?P<val2>[A-z]*) city")
    welcomeMessage := "Hello guys, welcome to new york city"
    template := "(value 1: ${val1}, value 2: ${val2})"
    replaced := pattern.ReplaceAllString(welcomeMessage, template)
    fmt.Println(replaced)
}

```

编译并执行上述代码，您将收到以下结果:

```
Hello guys, (value 1: to, value 2: york)

```

### `ReplaceAllLiteralString(src, repl string)`

该方法接受字符串和一个`template`参数，并用指定的模板替换字符串中匹配的部分，该模板包含在结果中，不会扩展为子表达式:

```
package main
import (
    "fmt"
    "regexp"
)
func main() {
    pattern := regexp.MustCompile("welcome (?P<val1>[A-z]*) new (?P<val2>[A-z]*) city")
    welcomeMessage := "Hello guys, welcome to new york city"
    template := "(value 1: ${val1}, value 2: ${val2})"
    replaced := pattern.ReplaceAllLiteralString(welcomeMessage, template)
    fmt.Println(replaced)
}

```

编译并执行上述代码，您将收到以下结果:

```
Hello guys, (value 1: ${val1}, value 2: ${val2})

```

## 使用函数替换匹配的内容

`ReplaceAllStringFunc`方法用函数生成的内容替换输入字符串的匹配部分:

```
package main
import (
    "fmt"
    "regexp"
)
func main() {
    pattern := regexp.MustCompile("welcome ([A-z]*) new ([A-z]*) city")
    welcomeMessage := "Hello guys, welcome to new york city"
    replaced := pattern.ReplaceAllStringFunc(welcomeMessage, func(s string) string {
        return "here is the replacement content for the matched string."
    })
    fmt.Println(replaced)
}

```

编译并执行上述代码，您将收到以下结果:

```
Hello guys, here is the replacement content for the matched string

```

## 结论

在本文中，我们探索了 go [regexp](https://pkg.go.dev/regexp) 包，以及它内置的处理基本到复杂的正则表达式匹配以及编译和重用正则表达式模式的方法。

您可以查看 go [regexp](https://pkg.go.dev/regexp) 文档，了解关于 Golang 正则表达式的更多信息。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)