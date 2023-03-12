# Vlang vs. Rust:比较构建语言

> 原文：<https://blog.logrocket.com/v-lang-vs-rust-comparing-build-languages/>

## 介绍

Vlang 还是 Rust？它们之间有什么不同？它们在性能、内存管理、编译速度和其他重要功能方面有何不同？

在本文中，当我们基于各种关键属性比较 Vlang(称为“V”)和 Rust 时，我们将探索所有这些问题的答案。

## Vlang 与 Rust

Rust 是一种静态的强类型语言，旨在构建可靠高效的软件。另一方面，v 是一种简单的语言，能够构建可维护和可预测的程序。

尽管 Rust 因其安全性和并发性而广为人知，但它也保证了快速性能和编译时内存管理。因此 Rust 避免了其他系统语言如 C++的一些缺点，如数据竞争和崩溃。

作为游戏中的新玩家，V 是一种编译型编程语言，它承诺为简单、可读和可维护的代码提供高性能特性。它与 Go 和 Rust 非常[相似，与 Swift 和 Oberon 也有一些相似之处。更好的是，它还支持 C 代码到 V 的翻译(C++翻译特性正在开发中)。](https://blog.logrocket.com/when-to-use-rust-and-when-to-use-golang/)

Rust 的第一次稳定发布要追溯到 2015 年 5 月 15 日。在 Stack Overflow 的[调查](https://stackoverflow.blog/2020/01/20/what-is-rust-and-why-is-it-so-popular/)中，Rust 五次被开发者选为最受欢迎的编程语言。另一方面，V 是一种相对较新的编程语言，由于其令人印象深刻的性能、创新的内存管理和 C/C++翻译承诺，它正在迅速流行起来。

尽管 V 和 Rust 有一些共同的特性，但它们的编程方法却完全不同。这是可以预料的；它们都是针对不同的用例开发的，这将在接下来的章节中详细讨论。

### 全局变量

全局变量是具有全局范围的变量，这意味着它是整个程序的组成部分。

Rust 不推荐使用全局变量，但是在某些情况下，全局变量更可取(也更实用)。全局变量可以在 Rust 中使用静态可变变量或[不安全 Rust](https://doc.rust-lang.org/book/ch19-01-unsafe-rust.html) 。

另一方面，V 是为了能够构建可维护和可预测的软件而构建的，并且以其严格的方法而广为人知，所以它根本不允许全局变量。v 中也没有 null 或 undefined。

默认情况下，如果你想使用全局变量，你应该使用 Rust over V。

### 内存管理

编程语言处理内存管理的方式非常重要。Rust 和 V 都保证了编译时的内存安全，然而，相比之下，它们处理这一问题的方式却截然不同。

与其他许多使用垃圾收集器的编程语言不同，Rust 通过所有权和借用规则(也称为生存期)来确保内存管理。起初，你可能很难理解这些规则。

v 采用了一种不同的方法，使用它的自动释放引擎，在编译时隐式地添加必要的自由调用。这大大简化了内存管理过程。查看下面视频中 V 的编译时内存管理演示:

 [https://www.youtube.com/embed/gmB8ea8uLsM?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/gmB8ea8uLsM?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

### 结果和选项类型

除了 Rust 中可用的标准类型(布尔、数字、文本等。)，还有一个选项类型。这个类型可以包含一个值，也可以不包含任何值(因此是`none`)。结果和选项类型在 Rust 中非常常用，但是理解这些类型并有效地使用它们可能很困难。

在 V 中，这个过程非常简化。可以用一个`?`来代替函数的返回类型，让它返回一个值、一个错误或者一个都不返回。于是，V 将选项和结果类型组合成了`?`。有了这个功能，错误处理也容易多了。

看看下面的示例代码中`?`的用法，摘自 V [docs](https://vlang.io/docs;) :

```
fn (r Repo) search_user_by_id(id int) ?User {
        for user in r.users {
                if user.id == id {
        // V automatically wraps this into an option type 
                        return user
                }
        }
        return error('User $id not found')
}

```

### 参考和数值

在 Rust 中，在将它传递给一个函数之前，必须明确地在`value`、`reference`、`ref-counted`、`atomic-ref-counted`和其他一些类型之间进行选择。

使用 V，您可以简单地使用对象，并让编译器来决定是使用值还是引用。这节省了程序员的时间，因为不需要在代码中显式命名值或引用。

在下面的例子中，你可以看到 V 是如何给你自由来传递一个不可变的函数参数(像`foo`)或者通过值或者通过引用:

```
struct Foo {}
fn (foo Foo) bar_method() {
        // ...
}
fn bar_function(foo Foo) {
        // ...
}

```

### λ表达式

与 V 相比，Rust 是一种更通用的语言，支持各种各样的特性，包括对 Lambda 表达式的支持。

坚持其严格和简化的方法，V 对 Lambda 和 Lamda 类语法的支持非常少，并且不允许 Lambda 表达式有多个参数。

### 安全

v 不支持诸如 null、全局变量、未定义的值、变量隐藏或边界检查之类的东西。它提供了高度安全的功能，如选项/结果类型、泛型、默认不可变变量、纯函数和不可变结构。鉴于此，V 被认为是一种非常类型安全和内存安全的语言。

相比之下，Rust 仍然允许一些功能(尽管它非常不鼓励使用它们)；这些包括，例如，前面提到的全局变量和不安全 Rust，[，它们应该被有选择地使用](https://blog.logrocket.com/unsafe-rust-how-and-when-not-to-use-it/)。

### 表演

众所周知，性能是使用任何语言的一个基本因素。

v 的官方文档声称它和 C 一样快，具有最少的分配量和内置的序列化，没有任何运行时反射。此外，V 声称每个 CPU 内核每秒大约有 80k (Clang 后端)和 100 万行(x64 和 tcc 后端)代码的编译性能。

这并不是说 Rust 很慢——Rust 也提供了很高的编译速度。然而，与 V 相比，Rust 在达到市场上现有的出色速度方面还有一段路要走。

## 结论

Vlang 和 Rust 都是构建软件的顶级语言。它们是现代、紧凑、强大、高性能的语言，对编译时内存管理有很大的支持。每种编程语言都特意针对不同的事情进行了优化。

正如编程中经常出现的情况一样，语言的选择完全取决于您所从事的工作和您试图解决的问题。作为一名开发人员，V 和 Rust 都是一流的，值得您在学习这些语言的过程中投入时间和精力。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。