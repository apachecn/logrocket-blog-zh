# 比较 Go 调试工具- LogRocket 博客

> 原文：<https://blog.logrocket.com/comparing-go-debugging-tools/>

如果你在 Go 中构建软件应用，你知道代码并不总是像预期的那样工作。

这个问题可能会继续恶化，包括咒骂你的计算机，一遍又一遍地编译代码，看看它是否会偶然工作，以及无休止地搜索大块的代码，看看错误来自哪里。

但是，您可以通过选择众多 Go 调试工具中的一个来省去自己的麻烦。调试本身就是寻找代码中的 bug，但是没有任何工具的调试都是汗水和泪水。

在本文中，我们将通过查看一些最流行的 Go 调试工具的安装、调试过程和优缺点来比较它们，包括:

但是在我们开始之前，最好讨论一下什么是调试工具，为什么我们作为开发人员需要它们，以及开发人员面临的这些工具可以帮助解决的问题。

本文的唯一先决条件是熟悉 Go 编程语言。

## 什么是调试工具？

调试是检测、发现和解决软件应用程序中的缺陷或问题的过程。这就像是犯罪电影中的侦探同时又是杀人犯。

调试工具帮助您在执行代码时准确地发现这些错误，以便您可以删除或修复它们，帮助您一步一步或一行一行地找到它们。这些错误通常包括语法、语义和运行时错误。

使用调试工具并在代码行执行时暂停执行是开发人员选择使用它们的一个原因。这是您能够全面、快速掌握代码库中正在发生的事情的唯一方法。

使用这个工具，您可以立即看到发生了什么。这也是一个很好的学习方法，因为你实际上看到了计算机如何解释代码。

## 调试技术

让我们来看看 Go 中一些常见的调试技术，以快速检查代码中的错误。

### 使用`print`

这是一种向代码中添加额外的`print`语句的做法，这样您就可以看到控制台记录了什么。这样，您可以看到变量的值，表达式如何执行，并检查您是否达到了变量范围。

有很多东西可以看，但这取决于你需要在你的控制台上记录什么。

然而，如果您不使用任何调试工具，这是一个很好的技巧。使用`print`的一个好处是你可以随时看到你的变量和语句的流程。

但是，如果您构建一个具有大量功能的大型应用程序，没有比在您的代码库中拥有一个日志系统更好的替代方法了。

此外，使用`print`可能会很麻烦，因为到处都键入`print`会影响代码库的可读性。如果你发现错误时忘记删除它们，它们会变得太多，而且你无法关闭`print`。

### 使用调试器

大多数 ide 都有内置的调试器，可以帮助检测代码中的错误。这是使用`print`后调试代码的另一种快捷方式。调试器只能在 ide 中找到，比如 Visual Studio Code 或者 Eclipse。

使用调试器有助于发现断点，在代码中逐行移动，观察变量的流动。有了这些，你可以很快找到逻辑错误。

大多数开发人员将这种技术称为分步执行，因为它是逐行执行的，是检测 bug 的快速方法。

但是，尽管使用调试器仍然是最受欢迎的调试技术之一，但在非常大的应用程序中使用调试器可能会很乏味，这就是为什么我个人更喜欢使用日志，也就是说，将日志消息写入标准输出。

### 使用错误检测工具

这些工具可以根据错误的类型帮助找到任何违反错误的地方。这意味着这些工具可以发现某些类型的错误，比如通过检测内存错误来发现内存泄漏。

这些工具也被视为错误监控工具，因为它们主要提供可视化错误的能力，以了解发生了什么以及在哪里发生的。

其他常见的调试技术包括回溯和分治。

## 转到调试提示

在我们进入不同类型的 Go 调试工具之前，让我们讨论一下在[调试 Go 应用](https://blog.logrocket.com/tag/go/)时需要记住的一些注意事项。

### 了解您的调试器

如果你想使用 Delve 这样的调试器，确保你明白它是如何工作的；了解它的命令、如何设置断点以及使用调试器的其他基础知识。在我看来，你可以用这些工具有效地处理 bug。

### 在编码时考虑调试

您还应该有一个日志记录系统，以便在编码时快速记录信息。但是，不要忘记避免记录敏感信息，如密码或财务信息。

### 小心简单的错误

避免考虑最坏的情况；您的错误可能很简单，比如某处拼写错误或者使用了错误的变量名。总是最意想不到的事情。

## 转到调试工具

随着我们对调试和调试方法的理解，让我们回顾一些在 Go 中工作时可能有帮助的 Go 调试工具。这些工具应该让您更好地理解为什么使用它们时开发会容易得多。

### 探究

Go 的第一个也是最常见的调试器是 Delve。使用 Delve 的目的是为 Go 中的调试提供一个简单而全功能的工具。

Delve 很酷的一点是，它是作为 Go 的调试器从头开始构建的，所以这个工具的实际目标是 Go 编程语言，不像 ide 中的其他调试器可以用于各种语言。

这非常有帮助，因为它知道如何处理特定于 Go 的特性和功能，如 Goroutines、语法表达式、数据结构和运行时。

要获得 Delve，你必须在你的电脑上安装它。

#### 安装深坑

Delve 支持所有平台，包括 Linux、Windows 和 macOS。

macOS、Windows 和 Linux 的安装是一样的，您需要做的就是使用`go install`命令:

```
$ go install github.com/go-delve/delve/cmd/[email protected]

```

安装完成后，您可以运行`go help`来检查`dlv`保存在哪里。

如果在 macOS 上，您还必须安装开发人员工具来使用 Delve:

```
$ xcode-select --install

```

下面是 Delve 在调试时使用的一些命令:

```
       --accept-multiclient               Allows a headless server to accept multiple client connections.
      --allow-non-terminal-interactive   Allows interactive sessions of Delve that don't have a terminal as stdin, stdout and stderr
      --api-version int                  Selects API version when headless. New clients should use v2\. Can be reset via RPCServer.SetApiVersion. See Documentation/api/json-rpc/README.md. (default 1)
      --backend string                   Backend selection (see 'dlv help backend'). (default "default")
      --build-flags string               Build flags, to be passed to the compiler. For example: --build-flags="-tags=integration -mod=vendor -cover -v"
      --check-go-version                 Checks that the version of Go in use is compatible with Delve. (default true)
      --disable-aslr                     Disables address space randomization
      --headless                         Run debug server only, in headless mode.
  -h, --help                             help for dlv
      --init string                      Init file, executed by the terminal client.
  -l, --listen string                    Debugging server listen address. (default "127.0.0.1:0")
      --log                              Enable debugging server logging.
      --log-dest string                  Writes logs to the specified file or file descriptor (see 'dlv help log').
      --log-output string                Comma separated list of components that should produce debug output (see 'dlv help log')
      --only-same-user                   Only connections from the same user that started this instance of Delve are allowed to connect. (default true)
  -r, --redirect stringArray             Specifies redirect rules for target process (see 'dlv help redirect')
      --wd string                        Working directory for running the program.

```

#### 用 Delve 调试

要在 Delve 中启动调试会话，请运行以下命令来编译您的程序:

```
$ dlv debug

```

`dlv debug`编译并运行当前目录下的主包进行调试。也可以在命令后指定包，比如使用`dlv debug ./hello`:

```
# run delve
$ dlv debug ./hello
Type 'help' for list of commands
(dlv)

```

要了解更多关于使用 Delve 的信息，你可以随时使用`help`命令。

#### 探究缺点

随着时间的推移，Delve 变得越来越稳定和成熟，因为它在开始时使用起来很不舒服。使用 Go Delve 的唯一缺点是它没有使用命令的简单教程。

要开始，你可以[参考他们的 Github 获取更多信息](https://github.com/go-delve/delve/blob/master/Documentation/cli/getting_started.md)。

### 基因组数据库

GBD 是 GNU 项目的另一个 Go 调试器。与 Delve 不同，它不是为 Go 而构建的。

其[目前可供下载的版本是 11.1](https://www.gnu.org/software/gdb/download/ANNOUNCEMENT) ，发布于 2020 年 9 月。

有了 GDB，你可以检查你编译的 Go 包，只要你给它正确的调试信息。而且，你不需要接触你的源代码来调试，因为 GBD 会在程序执行的时候检查程序内部发生了什么。

GDB 可以在调试代码时做一些事情来查找 bug，包括:

*   开始你的程序并指定什么影响它的行为
*   在特定断点处停止程序
*   仔细检查代码的结果
*   允许修改你的代码，这样你就能看到程序中什么是正确的

#### 安装 GDB

GDB 适用于所有平台，包括 macOS、Windows 和 Linux，[但他们也把类似 UNIX 的系统作为目标](https://www.gnu.org/software/gdb/)。

在 macOS 上安装 GBD 非常简单明了:

```
brew install gdb

```

然而，在 Windows 上安装需要你从 min [GW](http://sourceforge.net/projects/mingw/files/) 下载安装程序。这将使用管理器为您安装 GDB。如果你使用 Linux 系统，你已经安装了 GDB。

要使用 GBD 开始调试过程，您必须编译您的程序。假设您的代码在`hello.go`文件中:

```
go build -gcflags "-N -l" hello.go

```

由于 GDB 不完全支持 Go，如果编译后 Go 编译器执行优化，GDB 很难正确调试。因此，使用`-N -l`可以防止 Go 执行默认优化。

接下来，使用`gdb`命令开始:

```
gdb hello

```

然后，使用命令`run`在 GDB 运行您的程序:

```
(gdb) run

```

当它运行时，您可以设置一个断点来阻止程序在该行运行:

```
 (gdb) b 5

```

我们的断点在`hello.go`文件的第一行。其他基本命令包括`print`、`execute`、`continue`以及其他一些命令。关于 GDB 的更多[调试技巧，你可以参考文档](http://www.gnu.org/software/gdb/)。

#### GDB 的缺点

如果你注意到，在 Go 中调试时使用 GDB 的一个缺点是，它不像 Delve 那样完全支持和理解 Go 语言。这意味着你必须阻止 Go 优化你的源代码，这样 GDB 才能正常运行。

此外，在 Windows 上安装 GDB 有点困难，不像 Delve 那样可以简单地使用 CLI 安装。一些人甚至抱怨说 Eclipse IDE 不能很好地与 GDB 兼容。

### `Println`

`PrintIn`是 Go 中默认的调试工具。它是一个打印到控制台的函数，并且是一个变量函数，这意味着它可以接受未知数量的参数。

它提供了一个简单的实践，将`print`语句添加到您的代码中，这样您就可以看到变量和表达式发生了什么。

使用`Println`不需要像其他工具一样安装，因为它是默认的 Go 工具:

```
 package main

// Importing fmt
import (
    "fmt"
)

func main() {

    const name = "Victor"

    fmt.Println("Hello", name)
}

```

使用这种方法非常有用，可以节省您的时间，因为您只需将变量打印到控制台就可以看到结果。这是对代码进行故障诊断、发现潜在问题的另一个好方法，总体来说是一个很好的日志记录工具。

#### `PrintIn`弊端

虽然这种调试方法可能更容易，但它也可能让人不知所措，因为要检查一百行代码并用`Println`进行聚类。

更好的替代方法是使用一个日志框架，将所有调试消息发送到一个日志文件中。

## 结论

我们看了一些用一些流行的调试工具调试代码的技巧。拥有合适的工具可能是一项挑战，而且有许多适合 Go 的工具。

Delve 是唯一专门为 Go 创建的调试工具，这使它成为显而易见的首选。

不幸的是，对于一些像 Godebug 这样突然贬值的工具，我们仍然希望未来会有更多的工具可供 Go 社区使用。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)