# 为什么你应该从 Ruby 转到 Crystal 

> 原文：<https://blog.logrocket.com/why-you-should-switch-from-ruby-to-crystal/>

## 爱 Ruby？试试水晶

你爱露比吗？你希望有一个更好的 Ruby 版本，像 C 一样有超快的性能吗？你的祈祷得到了回应。让我把你介绍给克里斯托。互联网称之为“像红宝石一样光滑，像 C 语言一样快”。

从一开始，开发人员就因为 Ruby 编程语言的简单性、对开发人员满意度和生产力的关注而爱上了它。

Ruby(和 Rails 框架)在过去十年中催生了一些传奇的初创公司，包括 Twitter、Github、AirBnB、Shopify、Soundcloud 等等。

然而，Ruby 的主要缺点是性能和可伸缩性。作为一种解释型语言，Ruby 的性能不如 C/C++、Rust 和 Go。

尽管并非不可能，但是很难扩展 Ruby on Rails 应用程序。此外，Ruby 中缺少静态类型，这使得随着开发团队的成长，重构代码变得很困难。另一方面，Crystal 拥有 Ruby 的所有优点，却没有它的缺点。

✔️ Crystal 拥有 Ruby 一样漂亮的语法
✔️ Crystal 的性能堪比 C
✔️ Crystal 支持原生 c 绑定
✔️静态类型和强大的宏用于元编程
✔️ Crystal 拥有 Rails 和 Sinatra 一样的 web 框架

Crystal 的语法和 Ruby 的几乎一样。

你将能够在 Crystal shell 中直接编写 Ruby 代码，反之亦然。像 Ruby 一样，Crystal 专注于让开发人员开心和高效。Ruby 开发人员对 Crystal 语法如鱼得水。以这个例子为例(斐波那契数列函数🌀):

```
def fib(n)
  if (n<=2)
    return 1
  else
    return (fib(n-1) + fib(n-2))
  end
end
```

这个代码片段对于 Ruby 和 Crystal 是相同的。由于语法相似，从一个迁移到另一个绝对轻而易举。

安装 Crystal 并设置您的开发环境也非常简单。你可以在任何 Linux 发行版或 Mac 上用几个简单的命令安装 Crystal。在这里看一下官方[指南。但是，您需要安装一个 Linux 子系统来在 windows 上运行 Crystal。建议 Ubuntu 18.04 或更高版本。](https://crystal-lang.org/install/)

### 惊人的表现

在大多数基准测试中，Crystal 的性能优于 Go、Python 和 Node.js。下面是几个链接，比较了 Crystal 与其他编程语言的性能:

🚆 👉[性能基准](https://github.com/the-benchmarker/web-frameworks)
🚆 👉[性能对比](https://github.com/drujensen/fib?ref=hackernoon.com)

Crystal 构建在 LLVM 框架之上。这就是 Crystal 炽热表现背后的秘密。LLVM 是将 C/C++代码编译成字节码的同一个框架。

### 晶体中易结合碳

使用 Crystal，您可以毫不费力地绑定到现有的 C 库和代码。这允许开发人员使用原生 C 库处理应用程序中的低级任务，进一步提高性能。

这里有一个用 Crystal 做 C 绑定的例子。

假设我们有以下用 C 编写的简单函数:

```
#include <stdio.h>
void hello(const char * name){
  printf("Say Hello %s!\n", name);
}
```

首先，我们可以用`gcc -c hello.c -o hello.o`编译它，然后在 Crystal 脚本中调用它，如下所示:

```
#hello.cr
@[Link(ldflags: "#{__DIR__}/hello.o")]
lib Say 
  fun hello(name : LibC::Char*) : Void
end
Say.hello("to 🔮")
```

### 静态打字

Crystal 最棒的特性之一是它的静态类型系统。它帮助开发人员在编译时捕捉错误。Ruby 和 Python 等动态类型语言允许开发人员轻松编写代码并快速交付特性。然而，随着时间的推移，随着我们的代码库和团队的成长，重构和维护这些代码变得越来越困难。

嗯，Crystal 对这个问题有一个独特的解决方案。

你可以像任何动态类型语言一样用 Crystal 编写代码。编译器只会抱怨，并要求您在不明确的情况下显式指定类型。

### Web 框架

让我们来看看适用于 Crystal 的 web 框架。如果你喜欢 Rails 或者 Phoenix，那么你会喜欢 Crystal 的 Amber 框架。

[Amber](https://amberframework.org/) 围绕 Rails 的相同理念构建(约定胜于配置)。另一个有前途的框架是 [Lucky](https://www.luckyframework.org/) ，也是受 Rails 启发。这两个框架都有令人惊叹的文档和一个热情的开发人员社区，他们积极地为代码库做出贡献。

另一方面，如果你是 Flask、Express.js (Node.js)和 Sinatra 等轻量级 web 框架的粉丝，那么就来看看 [Kemal](https://kemalcr.com/) 。还不服气？

好吧，看看这些 web 框架的[性能基准](http://techempower.com/benchmarks)。与其他 web 框架相比，Crystal 框架表现非常好，Crystal 框架的响应时间在亚毫秒级。

### 元编程怎么样？

Crystal 有一个强大的宏系统用于元编程。它可以用于一系列事情，比如基本的模板、AST 检查、类型检查和运行任意的外部程序。【来源:[https://crystal-lang.org](https://crystal-lang.org)

以这段代码片段为例。我们可以用这个简单的函数生成模块、类和结构:

```
macro define_class(module_name, class_name, method, content)
  module {{module_name}}
    class {{class_name}}
      def initialize(@name : String)
      end
      def {{method}}
        {{content}} + @name
      end
    end
  end
end
# This generates:
#     module Foo
#       class Bar
#         def initialize(@name : String)
#         end
#
#         def say
#           "hi " + @name
#         end
#       end
#     end
define_class Foo, Bar, say, "hi "
p Foo::Bar.new("John").say # => "hi John"
```

你可以点击[链接](https://crystal-lang.org/reference/syntax_and_semantics/macros.html)了解更多关于 Crystal macros 的信息。

## 让我们谈谈并发性

Crystal 使用一种叫做纤程的东西来实现并发。纤程类似于操作系统线程，但是轻量级的。执行由流程内部管理。一个程序可以产生多个纤程，Crystal 将高效地执行它们以优化性能。

纤程和围棋套路很像。下面是一个产生纤维的例子:

```
spawn do
  # ...
  socket.gets
  # ...
end
spawn do
  # ...
  sleep 5.seconds
  #  ...
end
```

【来源:[https://crystal-lang.org/reference/guides/concurrency.html](https://crystal-lang.org/reference/guides/concurrency.html)

Crystal 允许不同光纤之间的数据通信，而不必共享内存或担心锁。Crystal 通过渠道实现这一点，渠道的灵感来自于 [CSP](https://en.wikipedia.org/wiki/Communicating_sequential_processes) (通信顺序流程)

排比怎么样？

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

目前 Crystal 不支持并行。然而，该团队正在努力将这一功能纳入稳定版本。

如果你很好奇，想研究一下，请随意阅读关于并发模型的 crystal [文档。](https://crystal-lang.org/reference/guides/concurrency.html)

## 那么，有什么条件呢？

如果你开发软件已经有一段时间了，你应该已经知道每项技术都有其局限性。Crystal 的主要缺点是它是一个新手。许多人认为 Crystal 及其生态系统还不成熟，没有做好生产准备。

虽然在某种程度上这是真的，但这也为你提前适应这个闪亮的新工具提供了可能性。电动汽车初创公司 Nikola Motors 正在生产中使用 Crystal 来驱动他们的软件。你可以在这里阅读他们的故事[。](https://manas.tech/blog/2020/02/11/nikola-motor-company)

可用于 Crystal 的库也有些有限。与 Ruby 的 Gems 或 Node 的 npm 包相比，Crystal 的可用库没有那么多样化。

最后，如果你试图建立一个利基产品，并解决一个非常具体的问题，你可能无法找到文档。例如，我试图用 Crystal 剥离 GraphQL 服务器。虽然 Github 中有一些很好的例子，但我找不到任何关于这个主题的合适的文档或教程。

## 结论

Crystal 带来了 Ruby 的所有优点，没有它的任何限制。从 Ruby 移植到 Crystal 很容易，反之亦然。Crystal 很快，有漂亮的类似 Ruby 的语法、静态类型和强大的功能，比如元编程的宏。然而，像每种技术一样，Crystal 也有自己的局限性。

Crystal 的主要限制是它不像 Ruby、Node 或 Python 那样成熟。随着越来越多热情的开发人员支持 Crystal，它可能很快就会成为 web 开发领域的下一个大玩家。作为一个 Rubyist 本人，我知道我正在密切关注 Crystal 的发展。

## 额外资源

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).