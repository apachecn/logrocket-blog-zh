# Rust vs . Python:Rust 能取代 Python 吗？- LogRocket 博客

> 原文：<https://blog.logrocket.com/rust-vs-python-could-rust-replace-python/>

***编者按**:本文最后更新于 2022 年 7 月 12 日，以反映最新的调查数据。*

Rust 和 Python 都是流行的编程语言，在软件开发和数据科学中有重叠的用例。在过去的几年里，Rust 的采用显著增加，让许多人怀疑它是否最终会取代 Python 成为顶级编程语言。

在本文中，我们将比较 Rust 和 Python 编程语言，讨论每种语言适用的用例，回顾每种语言的优缺点，并探索为什么 Rust 在过去几年越来越受欢迎。我们开始吧！

我们将讨论以下内容:

什么是铁锈？

## Rust 是一种多代语言，它使开发人员能够构建可靠高效的软件。Rust 侧重于安全性和性能，类似于 C 和 C++，速度快，内存效率高，没有垃圾回收。Rust 集成了其他语言，也可以在嵌入式系统上运行。

Rust 拥有[优秀的文档](https://doc.rust-lang.org/book/)，一个友好的编译器，提供有用的错误消息，以及尖端的工具，包括集成的包管理器、构建工具、带有自动完成和类型检查的智能多编辑器支持、自动套用格式器等等。

Rust 是由 Mozilla Research 的 Graydon Hoare 在 2010 年推出的。尽管与 Python 相比，Rust 仍然是一门年轻的语言，但它有一个稳定增长的社区。事实上，Stack Overflow 的 [2021 年开发者调查](https://insights.stackoverflow.com/survey/2021#technology-most-loved-dreaded-and-wanted)中，86.98%的受访者认为 Rust 是他们 2021 年最喜欢的编程语言，而 2020 年只有 86.1%。

乍一看，Rust 被静态和强类型化似乎有些极端。然而，从长远来看，您会发现这有助于防止意外的代码行为。

Python 是什么？

## Python 是一种编程语言，旨在帮助开发者更高效地工作，更有效地集成系统。像 Rust 一样，Python 是多尺度的，并且被设计成可扩展的。如果速度是最重要的，你可以使用低级别的 API 调用，比如 [CPython](https://github.com/python/cpython) 。

Python 可以追溯到 1991 年，当时它是由吉多·范·罗苏姆介绍的，以其代码可读性、消除分号和花括号而闻名。

除了可扩展的特性，Python 还是一种解释型语言，这使得它比大多数编译型语言都要慢。鉴于 Python 的成熟，正如你所料，它有一个庞大的库生态系统和一个大型的专用社区。

什么时候应该使用 Rust？

## Rust 用于系统开发、操作系统、企业系统、微控制器应用、嵌入式系统、文件系统、浏览器组件、虚拟现实的模拟引擎等等。

Rust 是处理性能问题时的首选语言，因为它非常适合处理大量数据。它可以处理 CPU 密集型操作，如执行算法，这就是 Rust 比 Python 更适合系统开发的原因。

Rust 保证内存安全，并让您控制线程行为以及如何在线程之间分配资源。这使您能够构建复杂的系统，这让 Rust 比 Python 更胜一筹。

总的来说，您应该在以下情况下使用 Rust:

您的项目需要高性能

*   你正在构建复杂的系统
*   比起简单性，你更看重内存安全
*   什么时候应该使用 Python？

## Python 可以用于许多应用领域，包括 web 开发、数据科学和分析、人工智能和机器学习以及软件开发。总而言之，Python 是:

简单且易于编码

*   灵活的
*   包含大量面向数据的软件包和库
*   由优秀的工具和库生态系统支持
*   在以下情况下，您应该使用 Python:

你需要一种灵活的语言，支持 web 开发、数据科学和分析、机器学习和人工智能

*   你重视可读性和简单性
*   你需要一门对初学者友好的语言
*   比起性能，您更喜欢语法简单和开发速度
*   Rust 能取代 Python 吗？

## 鉴于 Rust 迅速增长的受欢迎程度和广泛的使用案例，它在不久的将来取代 Python 几乎是不可避免的。让我们考虑一些原因。

表演

### Rust 超越 Python 的一个主要原因是性能。因为 Rust 是直接编译成机器代码的，所以在你的代码和计算机之间没有虚拟机或者解释器。

相对于 Python 的另一个关键优势是 Rust 的线程和内存管理。虽然 Rust 没有 Python 那样的垃圾收集，但 Rust 中的编译器会强制检查无效的内存引用泄漏和其他危险或不规则的行为。

编译语言通常比解释语言快。但是让 Rust 处于不同水平的是，它几乎和 C 和 C++一样快，但是没有开销。

让我们看一个用 Python 编写的 O(log n)程序的例子，并使用迭代方法计算完成任务所需的时间:

上面的代码将返回以下输出:

```
import random
import datetime
def binary_searcher(search_key, arr):
    low = 0
    high = len(arr)-1
    while low <= high:
        mid = int(low + (high-low)//2)
        if search_key == arr[mid]:
            return True
        if search_key < arr[mid]:
            high = mid-1
        elif search_key > arr[mid]:
            low = mid+1
return False

```

现在，让我们看一个使用迭代方法用 Rust 编写的定时 O(log n)程序:

```
> python -m binny.py
It took 8.6μs to search

```

我们将得到以下输出:

```
>use rand::thread_rng;
use std::time::Instant;
use floating_duration::TimeFormat;

fn binary_searcher(search_key: i32, vec: &mut Vec<i32>) -> bool {
    let mut low: usize = 0;
    let mut high: usize = vec.len()-1;
    let mut _mid: usize = 0;
    while low <= high {
        _mid = low + (high-low)/2;
        if search_key == vec[_mid] {
            return true;
        }
        if search_key < vec[_mid] {
            high = _mid - 1;
        } else if search_key > vec[_mid] {
            low = _mid + 1;
        }
    }
    return false;
}

fn main() {
    let mut _rng = thread_rng();
    let mut int_vec = Vec::new();
    let max_num = 1000000;

    for num in 1..max_num {
        int_vec.push(num as i32);
    }
    let start = Instant::now();
    let _result = binary_searcher(384723, &mut int_vec);
    println!("It took: {} to search", TimeFormat(start.elapsed()));
   }

```

在没有任何优化技术的情况下，Rust 花费了 4.6 微秒，Python 花费了 8.6 微秒在同一台机器上执行类似的操作，这意味着 Python 花费的时间几乎是 Rust 的两倍。

```
> cargo run
Finished dev [unoptimized + debuginfo] target(s) in 0.04s
Running target\debug\algo_rusty.exe
It took: 4.6μs to search

```

安全性

### Rust 有一个非常严格的类型系统，它为开发人员提供了许多好处，如下所示:

更好的错误消息

*   避免 C/C++内存管理中的许多典型陷阱
*   更好的自动化文档设施
*   软件中的安全缺陷通常来自管理不当的内存或代码库中不可预见的错误。Rust 中的这种严格性有助于我们在这些错误出现在用户面前之前捕捉和防止它们。

内存管理

### 像大多数现代编程语言一样，Python 被设计成内存安全的。然而，在内存安全方面，即使没有垃圾收集，Rust 也能与 Python 一较高下。

Rust 采用了一种独特的方式来确保内存安全，这种方式涉及到一个所有权系统和一个[借用检查器](https://blog.logrocket.com/introducing-the-rust-borrow-checker/)，它可以确保引用和指针不会比它们所指向的数据存活得更久。

错误检查和诊断

### Python 和其他语言一样，提供了错误检查和日志记录机制。但是，当涉及到让开发人员知道哪里出了问题时，Rust 和 Python 之间有一些对比。

举一个 Python 变量错误的典型例子:

下面是 Python 的输出:

```
apple = 15
print('The available apples are:', apple)

```

让我们考虑 Rust 中一个类似的例子:

```
Traceback (most recent call last):
    File "binny.py", line 2, in <module>
      print('The available apples are:', aple)
    NameError: name 'aple' is not defined

```

Rust 的输出如下:

```
fn main() {
    let apple = 15;
     println!("The available apples are:", apple);
}

```

在上面的代码中，Rust 推荐了可能的变量，这些变量可能就是您想要键入的。Python 只会抛出错误，而不会给出如何修复它的建议。以下面的例子为例:

```
println!("The available apples are:", aple);
   ^^^^ help: a local variable with a similar name exists: `apple`

```

这段代码抛出了一个错误，因为 Rust 中的变量在默认情况下是不可变的。除非有关键字`out`，否则不能更改。我们将得到以下错误:

```
fn main() {
    let grass = 13;

    grass += 1;
}

```

修复的错误如下所示:

```
    let grass = 13;
      |         -----
      |         |
      |         first assignment to `grass`
      |         help: make this binding mutable: `mut grass`

```

如您所见，现在我们的代码没有抛出任何错误。除此之外，Rust 不允许不同的数据类型互相操作，除非它们被转换成相同的类型。

```
fn main() {
    let mut _grass: i32 = 13;

    _grass += 1;
}

```

由于这种行为，维护 Rust 代码库通常很容易。Rust 不允许更改，除非指定，但是 Python 允许这种性质的更改。

与大多数编译语言相比，Rust 由于其速度、有保证的内存安全性、可靠性、一致性和用户友好性而受到欢迎。在编程中，速度开始变得不需要动脑筋了。

随着技术的发展，它变得越来越快，试图在更短的时间内执行更多的任务，而没有太多的权衡。Rust 有助于实现这一点，而不会给开发人员设置障碍。随着技术试图推动其所能实现的边界，它也考虑系统的安全性和可靠性，这是 Rust 背后的主要思想。

并行计算

### 除了速度，Python 在并行计算方面也有局限性。Python 使用全局解释器锁(GIL ),它鼓励在同一时间只执行一个线程，以提高单线程的性能。这个过程是一个阻碍，因为你不能使用多个 CPU 核心进行密集计算。

如前所述，Stack Overflow 的 2021 年开发者调查中，86.98%的受访者称 Rust 是他们 2021 年最喜欢的编程语言:

![Rust Popularity Survey Results](img/8cd6f97257c0a53c955349a6a549c111.png)

同样，2020 年 HackerRank 开发者技能报告的受访者将 Rust 列为他们下一步计划学习的十大编程语言:

![2020 Survey Results](img/434ce74e2b6913889d6044a4463ec850.png)

相比之下， [2019 年的调查](https://research.hackerrank.com/developer-skills/2019)将 Rust 放在了名单的底部，这表明 Rust 开发者社区正在快速增长:

![2019 Survey Results](img/1c6363fa2a0c4420396504e51506c6dc.png)

正如这个数据所显示的，Rust 正在成为主流开发者社区的一部分。许多大公司都使用 Rust，一些开发者甚至用它来构建其他编程语言使用的库。著名的 Rust 用户包括 Mozilla、Dropbox、Atlassian、npm 和 Cloudflare 等等。

Amazon Web Service 也在 Lambda、EC2 和 S3 中对性能敏感的组件采用了 Rust。2019 年， [AWS 宣布赞助 Rust 项目](https://aws.amazon.com/blogs/opensource/aws-sponsorship-of-the-rust-project/)，此后一直为 Rust 提供 AWS SDK。

越来越多的公司开始用 Rust 等更高效的语言取代速度较慢的编程语言。没有其他语言像 Rust 一样在简单性和速度之间做出权衡。

铁锈:优点

| 铁锈:缺点 | Python:优点 | Python:缺点 | 快的 |
| --- | --- | --- | --- |
| 实质性学习曲线 | 使用方便 | 相对于低级语言，性能较差 | 减少与内存相关的错误 |
| 大量可供选择的图书馆 | 图书馆和社区支持的选择越来越多 | 强大的社区 |  |
| 结论 | Rust 已经发展成为一种常用的编程语言，它的应用越来越广泛。尽管 Python 在机器学习和数据科学社区中占有稳固的地位，Rust 很可能在未来被用作 Python 库的更有效的后端。 | Rust 有取代 Python 的巨大潜力。凭借其在应用、性能和速度方面的优势，Rust 不仅仅是一种编程语言，它还是一种思维方式。 | [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性 |

## 调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

.

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

## 现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。

Debugging Rust applications can be difficult, especially when users experience issues that are difficult to reproduce. If you’re interested in monitoring and tracking performance of your Rust apps, automatically surfacing errors, and tracking slow network requests and load time,

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

[LogRocket](https://lp.logrocket.com/blg/rust-signup) is like a DVR for web and mobile apps, recording literally everything that happens on your Rust app. Instead of guessing why problems happen, you can aggregate and report on what state your application was in when an issue occurred. LogRocket also monitors your app’s performance, reporting metrics like client CPU load, client memory usage, and more.

Modernize how you debug your Rust apps — [start monitoring for free](https://lp.logrocket.com/blg/rust-signup).