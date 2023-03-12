# 探索碳，C++的新超集

> 原文：<https://blog.logrocket.com/exploring-carbon-new-superset-c-plus-plus/>

C++是 C 编程语言的继承者，毫无疑问是当今最相关的语言之一。C++支持 Python 机器学习、JavaScript 库、游戏开发和现代编程语言中使用的许多其他工具。

> 对于每一个重大的挑战，如果你找到了解决方案，你会在底部某个你看不到的地方找到一些 C++。比雅尼·斯特劳斯特鲁普

C++也是较早的面向对象编程语言之一，具有适应性强的设计、多功能性和广泛的兼容性。C++启发了 Java 等更新的语言，使这种语言可以用于开发游戏、医疗设备、人工智能和控制系统以及许多其他应用程序。

许多现代编程语言如 Rust 和 Golang 如雨后春笋般涌现，允许开发人员灵活地编写干净、安全的代码，比他们的前辈更少麻烦。然而，旧的编程语言仍然为我们日常生活中运行的许多代码库提供动力。

我们也看到编程语言推出了为 C/C++等老语言提供解决方案。在当今世界，它们是 JavaScript、TypeScript、Objective-C、Swift、Java 和 Kotlin。

在最近结束的 CPP 北方会议上，谷歌宣布他们已经开发了 C++的继任者:Carbon 编程语言。在本文中，我们将讨论什么是 Carbon，它的特性，它与 C++的区别，如何设置它，等等。

我们开始吧！

## 碳是什么？

> 在 C++中，有一种更小、更简洁的语言正在努力发展。比雅尼·斯特劳斯特鲁普

Carbon 是一种开源的、静态类型的、编译过的编程语言，最初是由 Google 为继承 C++而构建的。Carbon 为开发人员提供了现代编程实践，如泛型、模块化代码组织和简单语法。

Carbon 希望能与 C++的性能和可伸缩性相媲美。该语言旨在实现与 C++的双向互操作性，以便移植和采用。这意味着你可以将 C++代码库转换成 Carbon，反之亦然，也可以使用现有的 C++库。

Carbon 也非常友好，对 C++开发人员来说有一个温和的学习曲线，为现有的软件设计和架构提供更多的表达和支持。

碳仍然是一项实验，处于发展阶段。尽管如此，许多帮助你编写你会喜欢的 Carbon 代码的特性还没有被添加。

## Carbon 编程语言的特点

Carbon 与 C++有一些非常相似的地方。它融入了现代编程实践、对 C++的支持、互操作性、内存安全和泛型。这些是 Carbon 编程语言的几个显著特征，我们现在来看一下其中的一些。

### 互用性

从逻辑上讲，像 Carbon 这样的后继语言需要与它的前一种语言兼容。Carbon 与 C++是双向兼容的，你可以使用任何一种语言。让我们来看看它的互操作性:

```
// Source code by the authors of the Carbon programming language

// C++ code used in both Carbon and C++:
struct Circle {
  float r;
};

// Carbon exposing a function for C++:
package Geometry api;
import Cpp library "circle.h";
import Math;

fn PrintTotalArea(circles: Slice(Cpp.Circle)) {
  var area: f32 = 0;
  for (c: Cpp.Circle in circles) {
    area += Math.Pi * c.r * c.r;
  }
  Print("Total area: {0}", area);
}

```

在上面的例子中，Carbon 代码使用`circle.h` C++库来打印一个圆的面积。

```
// Source code by the authors of the Carbon programming language

// C++ calling Carbon:
#include <vector>
#include "circle.h"
#include "geometry.carbon.h"

auto main(int argc, char** argv) -> int {
  std::vector<Circle> circles = {{1.0}, {2.0}};
  // Carbon's `Slice` supports implicit construction from `std::vector`,
  // similar to `std::span`.
  Geometry::PrintTotalArea(circles);
  return 0;
}

```

上面的 C++代码使用`geometry.carbon.h`库，用提供的参数打印一个圆的面积。请注意，Carbon 代码更加易读和直观。

### 现代仿制药系统

泛型是现代编程语言中众多优秀特性之一，包括 Rust 和 Go。Carbon 具有一个现代的泛型系统，带有检查过的定义和选择模板，可以与现有的 C++代码无缝互操作。

碳的通用系统是一个值得关注的系统。它将确保泛型定义的类型检查，以避免编译时定义检查的开销。它还将确保健壮的检查接口，以减少对实现的意外依赖，并创建重要的显式契约。

### 内存安全改进

内存安全是使用 C++的一个重要任务。Carbon 的目标是通过跟踪未初始化状态、增加初始化执行和阻止初始化错误来解决内存安全问题。

Carbon 将以基本的 API 和习惯用法为特色，以支持调试中的动态边界检查。它还将支持具有全面的默认调试模式的强化版本。Carbon 的安全策略也可以采用一些保证内存安全的编程模型。

根据您对内存安全的偏好，您可以在调试、性能和加固模式下运行 Carbon 程序。你可以在 GitHub 文档中阅读更多关于 [Carbon 的安全策略](https://github.com/carbon-language/carbon-lang/blob/trunk/docs/project/principles/safety_strategy.md)的信息。

## Carbon 与 C++

由于 Carbon 是新的，而且这种语言还没有准备好投入生产，所以你只能根据它们共有的特性来比较 Carbon 和 C++。C++已经存在了一段时间，开发人员已经能够分享他们对这种语言的失望。希望 Carbon 能像 TypeScript 为 JavaScript 所做的那样，将众多的 C++问题最小化。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

下面是 Carbon 和 C++的简单比较:

Carbon 成功接替 C++的概率在我看来是比较高的，尤其是考虑到 C++的状态和语言背后的公司。进一步考虑两种语言之间的双向互操作性，开发人员不一定会错过 C++的特性。

Go 和 Dart 已经在他们的目标领域被大量采用，Carbon 可能会非常成功。

## 碳入门

Carbon 仍处于实验阶段，所以这种语言还远未准备好投入生产。但是，您仍然可以尝试这种语言的当前状态，并考虑为它的发展做出贡献！

现在，我们将讨论如何使用 Carbon 的资源管理器来检查代码库和使用这种语言。

### 安装碳纤维

你首先需要在你的电脑上安装家酿软件。你可以[查看这些安装说明，在 Linux 和 macOS 上安装 Homebrew](https://docs.brew.sh/Installation) 。

首先用家酿软件包管理器安装 Bazelisk:

```
brew install bazelisk

```

接下来，安装`llvm`并导出`PATH`变量:

```
brew install llvm
export PATH="$(brew --prefix llvm)/bin:${PATH}"

```

克隆 Carbon 存储库并将其移动到您机器上的目录中:

```
git clone https://github.com/carbon-language/carbon-lang
cd carbon-lang

```

最后，构建并运行 Carbon explorer:

```
bazel run //explorer -- ./explorer/testdata/print/format_only.carbon

```

就是这样！现在你可以开始用 Carbon 做实验，看看这种语言是如何工作的。

## Carbon 语法概述

Carbon 的语法实际上非常类似于 Rust 和其他一些语言的语法。让我们看一下 Carbon 语法的概述，包括变量、循环、条件、函数和类。

### 碳中的变量

可以用`var`关键字声明变量。Carbon 是静态类型的，所以您必须指定一个数据类型。

```
var hello: auto = "Hello, world!";

```

`auto`数据类型是一种通用数据类型，可以用于任何变量。就像 C++一样，每条语句都要以分号结尾。

### 对于碳环

Carbon 采用 c 风格的 for 循环，包括一系列 for 循环功能。

```
for (var name: String in names) { // names is an array
    Print(name);
}

```

for 循环遍历一个数组，并打印该数组的元素。

### 碳的功能

Carbon 的函数与 Rust 的非常相似，除了您必须显式声明参数变量。

```
fn Sum(var a: i32, var b: i32) -> i32 {
    return a + b;
}

```

`Sum`函数在调用时返回参数的总和。

### 碳纤维条件句

Carbon 也使用 c 风格的条件语句。

```
fn check(var value: i32) -> i32 {
  if(value == 7) {
    Print(value);
  } else {
    Print(0);
  }
}

```

`check`函数检查参数输入是否等于`7`，如果等于则打印`value`，如果 if 语句无效则打印`0`。

### 碳的种类

Carbon 提供了一个关键字，用于定义带有标识符的类和任何类型的字段。

```
class Human {
  var phone: i32;
  var age: i32;
  var name: String;
}

```

`Human`类有`phone`、`age`和`name`字段。您可以为操作声明任意数量的字段。

## 展望碳的未来

Google 和 Carbon 的团队设想 Carbon 是一种快速的、可扩展的语言，能够发展和支持在现代操作系统、硬件和环境上运行的性能关键型软件。Carbon 也将朝着向后或向前兼容以及该语言的稳定应用二进制接口(ABI)的方向发展。

Carbon 的实验性但可工作的版本计划在 2022 年底公开，完整版将在 2024-2025 年推出。你可以查看[语言目标](https://github.com/carbon-language/carbon-lang/blob/trunk/docs/project/goals.md)的详细概述。

## 结论

在本文中，我们概述了 Carbon 及其特性，将 Carbon 与其前身 C++进行了比较，研究了如何安装 Carbon 及其语法如何工作，并展望了 Carbon 的未来发展方向。

Carbon 正在采用一种包含电池的方法来开发这种语言。这个库在 GitHub 上是实时的，在这里你可以为构建一个每个人都喜欢的 C++后继者的讨论和开发做出贡献。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)