# 对 Crystal 1.0 有什么期待

> 原文：<https://blog.logrocket.com/what-to-expect-from-crystal-1-0/>

Crystal 是一种类似 Ruby 的编程语言，具有 c 语言的强大功能。Ruby/Rails 开发人员尤其应该尝试这种语言，因为它在语法和纯粹的优雅方面与 Ruby 相似。Crystal 提供了 C 语言的性能和效率，C 语言主要用于轻松编写低级系统。

在本文中，我们将讨论以下主题，以及 Crystal 可以提供的一些基本功能:

## 什么是水晶？

Crystal 最初命名为 Joy，于 2011 年开始开发。第一个正式版本于 2014 年发布，此后一直在不断开发，直到 2021 年 3 月 Crystal 1.0 问世。

Crystal 是一种静态类型的系统编程语言，有几个重要的开发人员友好的目标。受 Ruby 的启发，Crystal 有一个温和的学习曲线，使其易于读写。它还在运行时提供类型检查，但并不总是需要定义变量类型或方法参数。

Crystal 背后的团队已经明确表示，它与 Ruby 的相似性并不是这种语言存在的理由。虽然受到 Ruby 生产力的启发，但 Crystal 使用 [LLVM](https://llvm.org/) 编译成本机代码，并使用类型推断，在编译时自动推断表达式的类型。

Crystal 中的并发工作方式与 [Go](https://golang.org/) 类似，它使用称为“纤程”的线程进行通信，而无需共享内存。这与平行不同；两个代码路径不能同时执行，但是依赖于前一个任务的完成，然后才能开始下一个任务。晶体纤程类似于 OS 线程，除了它们是轻量级的，并且由进程内部管理。

Crystal 还拥有通过编写绑定来调用 C 代码的能力，这使我们能够在两者之间传递数据。这使得我们可以利用两种语言的优势。

该团队还澄清说，Crystal 将向后兼容以前的版本，这意味着虽然内置库正在更新，但它们仍然可以在没有更新的情况下工作。这些内置库被分组为“碎片”,并使用 Git 分发。一个[碎片是一个水晶代码](https://crystal-lang.org/reference/the_shards_command/index.html)包，带有来自其各自存储库的内置命令，当你通过一个 YAML 文件指定它们时就可以使用。

在 Crystal 1.0 之前，以前的版本缺乏当前版本的重要语言和编译稳定性，这使得 Crystal 特别有用。未来的版本将包括错误修复和其他维护，并将被视为补丁版本。

## 开始使用 Crystal

Crystal 支持许多不同的平台，从 macOS 到 WindowsSubsystem for Linux。Crystal compiler 还不能在 Windows 上运行，但是它已经在未来版本的路线图上了。

如果你使用的是 macOS，你可以使用 [Homebrew](http://brew.sh/) ，它会帮助你安装最新版本:

```
brew update
brew install crystal

```

对于 windows subsystem for Linux(Ubuntu 和 Debian)，可以编写以下代码:

```
curl -fsSL https://crystal-lang.org/install.sh | sudo bash

```

贡献给 Crystal 意味着你必须从[源](https://crystal-lang.org/install/from_sources/)安装它。

## 编写我们的第一个 Crystal 应用和 HTTP 服务器

我们的第一个程序将是“Hello World”，因为它是一个大型、复杂的程序(开玩笑)！这是一个简单的“Hello World”在 Crystal 中的样子，我们将把它保存在一个`hello_world.cr`文件中:

```
puts "Hello World!"

```

上面代码中的`puts`代表“放字符串”整个程序主要是调用方法`puts`，然后输出`"Hello World!"`。

然后我们像这样运行它:

```
$ crystal hello_world.cr
Hello World!

```

HTTP 服务器代码示例如下所示:

```
require "http/server"

server = HTTP::Server.new do |context|
  context.response.content_type = "text/plain"
  context.response.print "Hello world! This is our server"
end

address = server.bind_tcp 3000
puts "Listening on http://#{address}"
server.listen

```

如果你熟悉这门语言，这就更有意义了，但是我们可以做一些事情:

```
require "http/server"

```

我们使用`require`来请求文件，文件中保存了我们需要的代码:

```
address = server.bind_tcp 3000

```

使用`bind_tcp`方法设置端口。这个方法来自于`object HTTP::Server`。

### 分配变量

分配变量的工作方式如下:

```
name = "Victor!"
age = 23

p! name
p! age

```

`p!`和`puts`类似，只是`p!`用代码打印表达式:

```
age         # => 23

```

重新分配变量是一项要求:

```
age = 23
p! age
age = 24
p! age

```

### 控制表达式

类似于控制表达式在其他语言中的工作方式，Crystal 使用了以下内容:

```
name = "Victor!"
age = 23
if age > 23
  name = "Matt"
elsif age < 23
  name = "Mike"
else
  name
end

```

逻辑`and`和`or`操作符也使用 Crystal。`and`是当且仅当操作数为真。以下示例与文档中的示例相同:

```
some_exp1 && some_exp2

# The above is the same as:
tmp = some_exp1
if tmp
  some_exp2
else
  tmp
end

```

逻辑`or`或`||`是`if`的语法糖:

```
some_exp1 || some_exp2

# The above is the same as:
tmp = some_exp1
if tmp
  tmp
else
  some_exp2
end

```

### 定义方法

为了在 Crystal 中定义一个方法，我们使用关键字`def`，后跟方法名。表达式后面是关键字`end`:

```
def greet
  puts "Hello Victor! It is nice to have you here"
end

greet()

```

几个方法可以有相同的定义，但有不同的参数。这被称为方法重载，它提高了程序的可读性:

```
def say_hello(arg : String)
  puts "You are #{arg}!"
end

def say_hello(age : Int32)
  puts "You are age" * age
end

say_hello "Victor"
say_hello 23

```

### 类型反射

Crystal 还通过提供`typeof`、`as`、`as?`、`responds_to`、`nil`和`is_a?`等方法来支持类型反射。

## Crystal 中的错误处理

Crystal 中的错误处理是通过引发和挽救异常来完成的。这意味着，对于遇到的每一个错误，您都需要引发一个异常，并通过在 rescue 子句中指定一个变量或者简单地输出一条消息来处理它(或者在 Crystal 中是`rescue`)。如果您阅读下面的代码，就会更容易理解:

```
begin
  raise "An Error!"
rescue
  puts "We caught it for you boss!"
end

# Output: Rescued!

```

在 rescue 子句中指定变量时，您现在可以访问异常:

```
begin
  raise "An Error!"
rescue ex
  puts ex.message
end

# Output: An Error!

```

`raise`是一个重载的方法，它接受`String`和`Exception`实例:

```
raise "An Error!"
raise Exception.new("An Error!")

```

## 水晶的未来

有了 450 多名 1.0 版开发者的贡献，很容易看出 Crystal 在未来将会大受欢迎。当前的版本是关于语言稳定性的，它为语言设置了一个很高的标准并向前迈进了一大步。

目前有 164 个[赞助商](https://crystal-lang.org/sponsors/)为 Crystal 的发展做出贡献。自版本 0.36.1 以来，已经有 25 个贡献者提交了 82 次；你可以在这里查看[发布的更新日志。](https://github.com/crystal-lang/crystal/releases/tag/1.0.0)

Crystal 团队仍然需要做一些调整，我希望在后续的维护版本中看到它们。路线图也已经为还需要做的事情准备好了。

Crystal 的[文档](https://crystal-lang.org/docs/)也将提供对事情如何工作的更好理解，并且一定要查看他们的[博客](https://crystal-lang.org/blog/)，团队在那里分享公告和发布说明。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)