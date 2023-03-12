# 面向前端开发人员的 Go 入门- LogRocket 博客

> 原文：<https://blog.logrocket.com/getting-started-with-go-for-frontend-developers/>

## 介绍

来自纯 JavaScript 背景的 Web 开发人员自然会选择 Node.js 作为他们的后端语言。作为一个基于 JavaScript 的运行时环境，它代表了前端开发人员编写后端功能的最简单途径。然而，如今前端开发人员有很多选择来实现全栈。

由于其范例和理念，Go 编程语言可能不会成为首选。因此，前端开发人员可能会回避使用它，尤其是那些刚刚进入全栈开发的人。

在本文中，我们将探索这些范例和特性，以及[为什么值得尝试一下](https://blog.logrocket.com/why-is-go-overtaking-node-js/)后端/全栈 web 开发。我们还将探索一些新的模式，Go 的开发者体验，以及作为一个第一次使用 Go 的前端开发人员应该期待什么。

## Go 的使用案例

所有的编程语言都是为了处理一组用例或解决特定问题而创建的。Go 在效率和性能是重中之重的领域大放异彩，因为它可以很快被编译成机器代码。因此，它不需要解释器或虚拟机。

由于其编译特性，Go 代码可以很容易地部署在服务器基础设施上，并相应地跨各种安装进行更新。它还支持多种操作系统和处理器架构，这确实是一个巨大的优势。

此外，Go 已经成为开源基础设施软件的首选语言，一些最大和最受欢迎的项目完全用 Go 编写。比如 Docker，完全是用 Go 写的。

Go 的另一个优点是，它给了程序员对内存布局和分配相当大的控制权——比其他垃圾收集语言中典型的多得多。

Go 通过它的运行时系统实现垃圾收集、并发、栈管理等等。自动垃圾收集使并发代码更容易编写，Go 通过其正确管理分配内存的能力，在语言设计层面解决了这一开发人员的痛点。

事实上，语言设计直接影响易用性以及我们为项目建立概念证明的速度。Go 在这方面大放异彩，它提供了一组独特的特性，旨在用于高弹性和可伸缩的应用程序。

接下来，我们将探索并发性，这是 Go 中一个非常重要且备受关注的特性——尤其是对于习惯于 JavaScript 同步特性的开发人员而言。

## 并发性:Go 的一个关键特性

并发性使程序能够将其执行组织成几个独立的流，并在它们之间传递动作或活动。并发编程技术是有效使用 CPU 和提高应用程序性能的好方法。对于企业级应用或需要同时处理数千个请求的应用来说，这是一个必不可少的功能。

并发性是通过 goroutines 和通道构建到 Go 语言本身的核心中的。基本上，goroutines 允许应用程序同时执行几个任务。它们是廉价、轻量级的执行线程。这在吞吐量、延迟和可靠性方面为语言提供了更好的性能。

如果你尝试过用其他编程语言实现并发性，[尤其是 JavaScript](https://blog.logrocket.com/parallelism-concurrency-and-async-programming-in-node-js/) ，你就会明白这样做是相当复杂的。相比之下，生成一个 goroutine 就像在函数前添加关键字`go`一样简单。下面我们来看一个例子。

```
import "fmt"

package main

func doSomething(str sting) {
     for i := 1; i <= 3; i++ {
          fmt.Printf("%s: %d", str, i)
     }
}

func main() {
    // calling this function the normal way
    doSomething("Hello")

    // Running it inside a go routine
    go doSomething("World")

    go func() {
        fmt.Print("Go routines are awesome")
    }()
}
//add return value for func
```

正如我们前面提到的，通过 goroutines 和通道的 Go 并发原语使并发编程变得容易。利用其多核处理器架构和高效内存的能力是 Go code 运行一些当今最常用的应用程序的最大原因之一。

事实上，[根据其网站](https://www.golang.org)的说法，Go 是为联网系统构建的，以获得高级性能并充分利用多核机器:

> “这意味着每一个围棋程序都带有内置工具，如果开发人员正确使用，这些工具可以使我们编写的程序执行得更好。”

注意，有点矛盾的是，一个程序中可能只有一个线程，但却有成千上万个 goroutines。如果那个线程中的任何一个 goroutine 阻塞了，比如说，等待用户输入，那么就创建另一个 OS 线程，剩下的 go routine 被移到新的 OS 线程中。所有这些都由运行时处理，而我们，作为程序员，从这些复杂的细节中抽象出来，因为我们有一个干净的 API 来工作。

当然，在一个 goroutine 中执行动作意味着它需要能够与其他 go routine 通信。为了完成这个，Go 提供了通道。

通道是管道，允许两个并发的 goroutines 通过发送和接收值来相互对话。`channel <-`语法可用于将值发送到通道中，而`<-channel`语法可用于从通道中读取内容，如下所示:

```
package main
import "fmt"
func main() {
// make a new channel
messages := make(chan string)
go func() {
// write to channel
message <- "Hello World!"
}()
// read from channel
message :=  <-messages
fmt.Printf("I got the message %s", message)
}
```

> **N.B.** 当从一个通道读取时，我们阻塞它，直到我们得到一个消息。发送者和接收者都必须准备好；也就是说，在该通道有相应的接收器之前，我们无法发送。

## 为什么前端开发人员应该学习围棋

对于新手来说，创建基本的应用程序是轻而易举的事情，快速入门和构建也同样简单。Go 被设计成这样:它有非常高的编译速度，对并发和垃圾收集等特性的现成支持，当然，Go 有非常简单明了的语法。

如果你习惯于面向对象的编程风格，你会在 Go 中找到熟悉的概念。它有 struct 形式的类型和方法[，这允许面向对象风格的编程。没有类型层次结构；Go 中的方法更加通用，因为它们可以被定义为任何类型的数据，甚至是内置类型。](https://blog.logrocket.com/exploring-structs-and-interfaces-in-go/)

结构有方法和属性，类似于典型的面向对象编程语言中的类。例如:

```
package worker

import "fmt"

type Person struct {
    Name string
    Age int
}

func (p *Person) Talk(phrase string) {
    return fmt.Sprintf("%s says: %s", p.Name, phrase)
}
```

这里，`Person`是一个带有字段`Name`和`Age`的`struct`类型。`Talk`是一个在`Person`结构上定义的方法，它打印一些东西。我们可以这样使用:

```
person := &Person{"Vivan", 22}
person.Talk("Go is awesome")
```

让我们详细说明为什么 go 在最近几年里迅速流行起来，以及为什么寻求 Go 全栈的前端开发人员应该选择 Go。

### 简单的包装管理

Go 程序是由[包](https://golang.org/pkg/)构成的，其属性允许有效地管理依赖关系。Go 工具链有一个内置系统，用于管理版本化的相关包集，称为模块。模块在 [Go 1.11](https://golang.org/doc/go1.11#modules) 引入，从 [1.14](https://golang.org/doc/go1.14#introduction) 开始准备生产使用。

要使用模块创建项目，运行`[go mod init](https://golang.org/ref/mod#go-mod-init)`。这个命令创建一个`[go.mod](https://golang.org/ref/mod#go-get)`文件来跟踪依赖版本。要添加、升级或降级依赖项，请运行`go get` —例如`go get golang.org/x/[[email protected]](/cdn-cgi/l/email-protection)`。有关[创建模块](https://golang.org/doc/tutorial/create-module.html)的更多信息，以及关于[管理模块](https://golang.org/doc/#developing-modules)依赖性的更多指南，请访问官方网站。

### 更少的语言结构和关键字

更少的构造和关键字意味着很容易学会 Go 并在短时间内高效工作。Go 在速度和语法上和 C 类似。它有大约 25 个关键字，使它易于编写，总的来说，它是一种清晰简单的语言，允许开发人员编写可读和可维护的代码。

这种语言确实限制了你做某些事情的方式，尽管这种灵活性的妥协带来了更大的简单性。这可能意味着多写一点代码，但是有明确的解决方案。Go 强制一致性，这有助于它的可读性和可维护性。

Go 固执己见，推荐一种实现目标的惯用方式。这些直接来自基于语言设计的 Go 团队；有效的 Go 展示了其中的一些。它更喜欢组合而不是继承，它的类型系统非常优雅，允许添加行为或扩展特性以适应特定的用例，而无需紧密耦合组件。

与其他语言不同，Go 优先考虑可读性和可维护性。Go 的开发者只在语言中加入了相关的特性，并没有使语言变得复杂。

例如，标准库的新增内容很少，而且包含的门槛很高。创建者认为标准库中包含的代码承担了大量的持续维护成本。Go 标准库受制于 [Go 1 兼容性承诺](https://golang.org/doc/go1compat.html)(阻止修复 API 中的任何缺陷)和 Go [发布时间表](https://golang.org/s/releasesched)。

这阻止了用户快速获得标准库中的错误修复。因此，新代码应该位于标准库之外，并且可以通过`[go get](https://golang.org/cmd/go/)`命令访问。这样的代码可以有自己的维护者、发布周期和兼容性保证。

> **注意**，用户可以在 godoc.org[找到软件包并阅读其文档](https://godoc.org/)。

再举一个例子来展示这种语言经过深思熟虑的特性，当 Go 包中的函数名以大写字母开头时，这意味着它可以被导出，而所有小写名称的函数都不能。最后，但同样重要的是， [Go 现在支持泛型](https://blog.logrocket.com/past-present-future-go-generics/)。

### 默认情况下为强类型

Go 是一种静态类型语言。编译器努力确保代码不仅能正确编译，还能保证类型转换和兼容性。这有助于防止您在动态类型语言(如 JavaScript)中遇到的问题，在动态类型语言中，您只有在执行代码时才能发现问题。

### 静态代码分析

Go 非常依赖静态代码分析。例子包括用于文档的 godoc，用于代码格式化的 [gofmt](https://golang.org/cmd/gofmt/) ，用于代码风格林挺的 [golint](https://github.com/golang/lint) ，等等。静态代码分析给开发人员一种安全和安心做事的感觉。

### 内置代码测试工具

测试对任何程序来说都是必须的，所以每次你写代码的时候，你应该在实际函数旁边添加测试函数。Go 提供了一种简单的机制来与代码并行编写单元测试，这使得编写测试变得非常容易。

该工具还通过您的测试、基准测试和编写用于生成代码文档的示例代码，为理解代码覆盖率提供支持。

要创建一个测试文件，在与您的包源代码相同的目录中创建一个以`_test.go`结尾的新文件。在该文件中，导入`testing`并编写表单函数。我们可以在同一个目录下运行`go test`。该脚本找到测试函数，构建测试二进制文件，并运行它。

```
    func TestFoo(t *testing.T) {

    }
```

> **注意**，Go 提供了一种测试你写的包的方法。只需使用`go test`命令，您就可以测试任何`*_test.go`文件中编写的代码。

### 表演

当您开始构建生产质量的应用程序时，Go 可以很好地扩展。无论是小型微服务还是广泛的企业应用，Go 都能提供卓越的性能。

有助于其整体性能水平的 Go 特性包括并发性、编译器优化、非阻塞 I/O、值的高效存储机制、其运行时系统等等。Go 没有虚拟机，编译成机器码，所以程序执行很快。

正如我们前面提到的，Go 有一个内置的垃圾收集器，它可以监控和识别不再需要的占用内存，并释放它以供重用。这降低了由于代码封装导致的安全漏洞的风险，并且还提供了有效的内存管理。

### 内置的 HTML 模板引擎

Go 自带内置的 HTML 模板引擎，可以在`html/template`包中找到。语法起初并不直观，尤其是来自前端背景，但它实际上非常简单，可以完美地完成工作。

### 大型团队可以轻松支持 Go 应用

Go 代码有一个清晰、整洁的语法，学习起来很容易，所以已经和你一起工作的开发人员可以在几天内学会他们需要的一切。这也将有助于开发者轻松支持现有的应用。

此外，Go 有相当多的[在线课程](https://tour.golang.org/welcome/1)和大量工具可以使用，包括自动文档、静态代码分析、嵌入式测试环境、竞争条件检测等等。

Go 的另一个很好的特性是，它拒绝用未使用的变量或导入来编译程序，用短期的便利来换取长期的构建速度和程序清晰性。一个未使用的变量的存在可能表明一个 bug，而未使用的导入只会降低编译速度，随着程序代码(和更多开发人员)的积累，这种影响会变得很大。

正如你所看到的，Go 给开发者和商业利益相关者都带来了很多好处。这也是这种语言最近大受欢迎的原因之一。然而，Go 还不是一种完美的语言。在决定将 Go 用于您的项目之前，您应该考虑它的缺点。

## 最后的想法

Go 项目不仅仅是语言规范，还是一个工具链和生态系统，由组织和个人贡献的标准库和外部第三方库组成。创建这种语言的核心原因是帮助管理大型代码库和提高开发人员的生产率。

Go 已经被大型项目大量采用，由于它的工具、生态系统和语言设计，程序员正稳步向它前进，特别是构建大型和高度可伸缩的基础设施。对于任何有经验的开发者来说，开始使用 Go 都很简单，Go 主页上有各种说明，从[安装](https://golang.org/dl/)工具链到[学习](https://golang.org/doc/)一般的 Go 知识。

Go 也是一种简单但固执己见的语言。它的自动类型推断与动态语言非常相似，因此它为 JavaScript 开发人员提供了一种熟悉的感觉。此外，Go 的自动文档化允许我们自动地以与标准库和内置 API 相同的质量水平记录代码。

最后，凭借强大的标准库，its 在构建 APIs 后端方面表现出色。Go 也是跨平台的——它可以在 Linux、macOS 和 Windows 上运行——并且内置了用于代码林挺、代码生成、构建、测试等的有用工具。

关于为什么前端开发人员应该选择 Go 的更多细节，请查看 [Go 文档 FAQ 页面](https://golang.org/doc/faq)。谢谢大家！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)