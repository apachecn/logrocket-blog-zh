# Go 模板库:性能比较

> 原文：<https://blog.logrocket.com/golang-template-libraries-performance-comparison/>

模板库是编程语言生态系统的重要组成部分。Go 的标准库提供了开箱即用的强大模板库，但在 [Go](https://blog.logrocket.com/tag/go/) 中也有多个社区构建的模板库供开发者使用。

由于有多种选择，很难为您的特定用例选择最佳选择。

本文将使用 Go 的基准测试工具，基于性能、功能和安全性对一些最流行的模板库进行比较。具体来说，我们将看看一些常见的操作——如条件操作、模板嵌套和循环等。—在模板中使用，并对它们进行基准测试，让您了解每个模板引擎的性能和内存占用。

基于对当今开发人员的使用和适用性，我决定使用四个 Go 模板库作为本文的基础，其他几个要么是预编译的，要么是稀疏维护的。我们今天要比较的库是`template/text`、`pongo2`、`liquid`和`quicktemplate`。

我们开始吧！

## `template/text`

Go 的标准[库](https://pkg.go.dev/text/template)提供了一个功能全面且强大的模板引擎。它是非常高性能和良好优化的。

### 表演

性能是任何计算机程序最重要的方面。Go 的标准在生产中被广泛使用，并且理所当然地成为开发人员的热门选择。

#### **解析**

如果您的用例要求模板经常变化，或者您需要在每次请求时加载模板，那么解析很快就成为一个非常重要的基准。`text`包提供了各种解析方法，比如从字符串或文件中解析，或者使用模式从文件系统中读取文件。

```
// template has loop, if statement function call etc.
BenchmarkGoParsing-10                      32030             37202 ns/op 
```

您可以在启动时解析模板并缓存它以提高性能。如果您的模板经常变化，那么您可以使用 cron，它在后台以固定的时间间隔更新。

#### 字符串替换

一个字符串替换是任何模板引擎最基本的功能，`text`在字符串替换方面是超级快的。

```
// template -> This is a simple string {{ .Name }} with value as string
BenchmarkGoStd-10                        4185343               288.8 ns/op 
```

当使用 struct 作为数据上下文时，字符串替换会更快—使用 map 可能会有点慢，因为它也会增加键查找的时间。

```
// template -> This is a simple string {{ index . "name" }} with value as string
BenchmarkGoStdMapWithIndex-10            1333495               852.7 ns/op 
```

正如你所见，使用地图是昂贵的。避免`index`功能允许优化上述模板。

```
// template -> This is a simple string {{ .Name }} with value as string`
BenchmarkGoStdMapWith-10                 3563234               338.5 ns/op 
```

`.`还允许我们在地图上执行关键字查找。

#### 条件式

条件是模板引擎的一个重要操作；低效的条件运算会导致性能下降。`text`包支持`if`操作，支持`and`和`or`操作。这两种方法都有多个参数，每个参数都是布尔类型。`text`为条件句提供合理的表现。

```
// template -> This is the file {{if and .First (eq .Second "value") (ne .Third "value")}}Got{{end}}. Git do
BenchmarkGoIfString-10                    506409              2323 ns/op 
```

上面演示的性能可以通过避免`eq`和`ne`调用来提高。`template`中的每个函数都是通过反射调用的，这会导致性能下降。

如果`eq .Second` `"value"`在代码中，那么编译器将能够优化它，因为具体类型在编译器类型中是已知的。如果所有的`eq`和`ne`调用都被一个简单的布尔函数代替，速度会更快。

```
// template -> This is the file {{if and .First .Four .Five}}Got{{end}}. Git do
BenchmarkGoIf-10                          987169              1186 ns/op 
```

(注意:在基准测试期间，字符串比较也包括在时间计算中)

#### 环

循环也由`text`包提供。`range`用于迭代切片、数组和映射。循环的性能取决于循环体中包含的操作。

```
// template ->`{{range .Six}}{{.}},{{end}}`
BenchmarkGoLoop-10                       1283661               919.8 ns/op 
```

类似地，在 map 上迭代也是有效的:

```
// template -> {{range $key, $value:=.Seven}}{{$key}},{{$value}}:{{end}}
BenchmarkGoLoopMap-10                     566796              2003 ns/op 
```

#### 函数调用

相反，函数调用允许开发人员在模板内更改和转换输入。但是，这通常会产生成本，因为函数调用是通过反射进行的。使用函数有很高的开销——如果可能的话，避免函数调用。

```
// template -> This is a simple string {{ noop }} with value as string
BenchmarkGoStdCallFunc-10                2611596               472.3 ns/op 
```

#### 嵌套模板

嵌套模板允许用户共享代码，避免不必要的重复代码。`text`支持调用嵌套模板。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
// template -> {{define "noop"}} This is {{.Second}} and {{.Third}} {{end}}
//
//{{template "noop" .}}

BenchmarkGoNested-10                     1868461               630.2 ns/op 
```

值得注意的是，`text`尤其拥有出色的社区支持，像`sprig`这样的库提供了大量可以在模板中使用的函数。对语法高亮显示和内置验证的支持是 Go 的标准库模板包的标准。

## `pongo2`

[`pongo2`](https://github.com/flosch/pongo2) 是一个社区构建的模板引擎，其语法受 Django-syntax 的启发。它是由社区为 Go 而建的。今天它非常受欢迎，在 [GitHub](https://github.com/flosch/pongo2) 上有超过 2K 颗星星。

### 表演

`pongo2`针对模板中的函数调用进行了很好的优化。它是一个完整的模板引擎，具有循环、条件和嵌套模板。

#### 从语法上分析

`pongo2`支持从字符串、字节、文件、缓存解析模板。我们可以看到，在性能方面，它比 Go 的标准库的模板包要快一点:

```
// template has loop, if, nested template and function call
BenchmarkPongoParsing-10                   38670             29153 ns/op 
```

#### 字符串替换

与`text`包相比，`pongo2`中的字符串替换要慢一些。它只支持 map for data context，导致额外的查找操作时间，这反映在下面的基准测试中。

```
// template -> This is a simple string {{ name }} with value as string
BenchmarkPongo2-10                       1815843               654.2 ns/op 
```

#### 条件式

`pongo2`为 if/else 提供了更加开发人员友好的语法。它更接近于用编程语言编写 if/else 的方式。从 pongo2 的性能角度来看，`if/else`比`text`包更昂贵。

```
// template -> Name is {% if First && Four && Five %}got{% endif %}. Go
BenchmarkPongo2String2If-10               709471              1528 ns/op 
```

#### 环

`pongo2`中的循环比`text`包稍慢:

```
// template -> {% for value in Six %} {{ value }}, {% endfor %}
BenchmarkPongo2String2Loop-10             650672              1796 ns/op 
```

地图上的循环也更慢:

```
// template -> {% for key,value in Seven %} {{key}},{{ value }}, {% endfor %}
BenchmarkPongo2String2LoopMap-10          359858              3182 ns/op 
```

#### 函数调用

`pongo2`中的函数调用比`text`包更快，因为它有一个在编译时已知的函数签名，并且函数不需要通过反射调用，这使得它更快。

```
// template -> This is a simple string {{ noop }} with value as string
BenchmarkPongo2StdCallFunc-10            4775058               261.9 ns/op 
```

#### 嵌套模板

`pongo2`宏是为嵌套模板性能提供的，比`text`包慢。

```
// template -> {% macro noop(first, second) %}
             This is {{first}} and {{second}}
             {% endmacro %}
             {{noop("anshul","goyal")}}

BenchmarkPongo2String2Nested-10           657597              1665 ns/op 
```

## `liquid`

[`liquid`](https://github.com/osteele/liquid) 是 Shopify 模板语言的社区构建实现。它提供了一个全功能的模板库。

### 表演

从我的研究来看，这很有成效。它是一个完整的模板引擎，具有循环、条件和嵌套模板。

#### 从语法上分析

`liquid`支持从字符串、字节、文件解析模板。性能方面，比 Go 的标准库模板包和`pongo2`要慢一点。

```
// template has loop, if, nested template and function call
BenchmarkLiquidParsing-10                       29710             40114 ns/op 
```

#### 字符串替换

字符串替换性能与`pongo2`相当，而`liquid`比`text`包稍慢。它只支持数据上下文的映射，导致额外的查找操作时间，如下面的基准所示。

```
// template -> This is a simple string {{ name }} with value as string
BenchmarkLiquidString-10                       1815843                676.0 ns/op 
```

#### 条件式

有非常开发人员友好的 if/else 语法。它更接近于 if/else 在其他已建立的编程语言中的编写方式。`if/else`在液体中的性能不如`text`包，但比`pongo2`快。

```
// template -> This is the file {%if First and  Four and Five %}Got{%endif%}. Git do
BenchmarkLiquidIf-10               709471              953.3 ns/op 
```

#### 环

`liquid`中的循环也比使用`text`包时慢。与`pongo2`相比，`liquid`的循环速度更慢:

```
// template -> {%for value in Six %}{{value}},{%endfor%}
BenchmarkLiquidLoop-10             650672              3067 ns/op 
```

#### 函数调用

`pongo2`中的函数调用比`liquid`更快，因为它有一个在编译时提取的函数签名，并且函数不需要通过反射调用，这使得它更快。

```
// template -> This is a simple string {{ noop }} with value as string
BenchmarkPongo2StdCallFunc-10            4775058               359.0 ns/op 
```

## `quicktemplate`

`quicktemplate`是预编译模板；它将模板转换成 Go 代码。它不允许开发人员在运行时更改代码。`quicktemplate`非常快，因为它不执行任何反射，一切都通过编译器优化器运行。

如果您的用例不需要频繁更新，那么`quicktemplate`可能是您非常好的选择。与`liquid`和`fasttemplate`(用于字符串替换)相比，`quicktemplate`的基准如下所示:

| 特征 | 液体 | [快速模板](https://github.com/valyala/fasttemplate) | [快速模板](https://github.com/valyala/quicktemplate) |
| --- | --- | --- | --- |
| 从语法上分析 | 40114 ns/op | 188.8 ns/op | 不适用的 |
| If 语句 | 953.3 ns/op | 不适用的 | 87.47 ns/升 |
| 带字符串的 if 语句 | 1144 ns/升 | N/A | 99.18 ns/升 |
| -好的 | 3067 ns/向上 | N/A | 268.8 ns/升 |
| 功能 | 359.0 ns/op | 不适用的 | 191.7 ns/op |
| 嵌套模板 | 不适用的 | N/A | 191.7 ns/升 |
| 字符串替换 | 676.0 ns/op | 75.21 ns/升 | 105.9 ns/升 |

`quicktemplate`是预编译的模板引擎(即模板转换成 Go 代码)。然后，编译器对 Go 代码进行优化，导致执行速度非常快。它还避免了反射，从而带来了巨大的性能提升。

(注意:`quicktemplate`提供了快速的模板执行，但代价是没有模板的运行时更新。`fasttemplate`仅支持字符串替换)

## 结论

`pongo2`和`text`都各有利弊。`pongo2`提供了比`text`更友好的语法，但是`text`提供了更好的整体性能。

您使用哪个模板库完全取决于哪个更适合您的特定项目需求。`text`预捆绑了 Go 的安装，这自然使它成为一个非常受欢迎的选择，而如果你的模板不需要经常改变，像`quicktemplate`这样的选项也是一个不错的选择，如果你不太喜欢使用 Go 的标准库，像`pongo2`这样的其他选项更容易使用。如果您只需要字符串替换，那么从性能角度来看，`fasttemplate`也是一个很好的选择。