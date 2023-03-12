# Rust traits:一个深入探讨的博客

> 原文：<https://blog.logrocket.com/rust-traits-a-deep-dive/>

当学习 Rust 时，很可能你会很快遇到特征的概念。

特征允许我们跨类型共享行为，并促进代码重用。它们还使我们能够编写接受不同类型参数的函数，只要该类型以特征的形式实现了所需的行为。

在本教程中，我们将回顾 Rust 的基本特征，并涵盖一些你最常遇到的问题。

## 什么是铁锈性状？

特征是 Rust 中定义共享行为的一种方式。正如[锈由例](https://doc.rust-lang.org/rust-by-example/trait.html)所说:

> 一个`trait`是为未知类型`Self`定义的方法集合。它们可以访问在同一特征中声明的其他方法。

当我们想要定义一个可以应用于任何类型的函数时，我们使用特征。

## 铁锈的特性

首先，让我们看看一些特征基础，包括我们如何定义和实现它们，一些术语和语法，以及我们可以在特征上定义什么方法类型。

### 确定铁锈的特征

为了定义一个特征，我们使用`trait`关键字:

```
trait WithName {
    fn new(name: String) -> Self;

    fn get_name(&self) -> &str;

    fn print(&self) {
    println!("My name is {}", self.get_name())
    }
}

```

因为特征是定义共享行为的一种方式，所以我们可以(并且通常会)定义属于我们正在定义的特征的方法。虽然大多数特征是为了共享行为而存在的，但 Rust 也有一种叫做标记特征的东西。标记特征没有任何行为，但是用来给编译器一些保证。你可以在 [Rust 文档](https://doc.rust-lang.org/std/marker/index.html)中阅读更多关于`std::marker`模块的内容。

在上面的 trait 定义中，有三种方法的行为都稍有不同。我们一会儿会更仔细地看这个，但是首先，让我们检查一下`Self`和`&self`的用法。

### 什么是`Self`和`&self`？

在一个特征定义中(包括上面的一个)，我们可以访问一个特殊的类型:`Self`。`Self`是一个特殊的关键字，仅在类型定义、特征定义和`impl`块中可用(根据[Rust 文档](https://doc.rust-lang.org/std/keyword.Self.html))。

在特征定义中，它指的是实现类型。换句话说，如果我们要声明一个结构`A`并为其实现`WithName`特征，那么由`new`方法返回的`Self`类型将是`A`。对于结构`B`，类型应该是`B`。

`&self`参数是`self: &Self`的语法糖。这意味着方法的第一个参数是实现类型的实例。要要求对自身的可变引用，请使用`&mut self`。当在一个类型的实例上调用实例方法(`let name = x.get_name()`)时，`&self`参数是隐式的。

### 方法:实例、静态上下文和默认实现

因为 trait 方法中的`&self`参数需要实现类型的实例，所以这些方法被称为实例方法。不需要实例的 Trait 方法称为静态方法。与上面的`new`方法一样，这些方法通常用于实例化实现特征的类型。

方法也可以有默认的实现，比如`WithName`特征的`print`方法。这使得在实现 trait 时实现这个方法是可选的。如果未被重写，则使用默认实现。

### 在 Rust 中实现一个特性

要实现一个特征，为您想要实现该特征的类型声明一个`impl`块。语法是`impl <trait> for <type>`。您需要实现所有没有默认实现的方法。

例如，如果我们想为一个结构`Name(String)`实现`WithName`特征，我们可以这样做:

```
trait WithName {
    fn new(name: String) -> Self;

    fn get_name(&self) -> &str;

    fn print(&self) {
    println!("My name is {}", self.get_name())
    }
}

struct Name(String);

impl WithName for Name {
    fn new(name: String) -> Self {
    Name(name)
    }

    fn get_name(&self) -> &str {
    &self.0
    }
}

```

如果你的实现不完整，你信任的朋友编译器会让你知道。

### 从外部机箱导入特征

要从外部机箱导入特征，使用常规的`use`语句。例如，如果您想从 Serde 导入`Serialize`特征，Serde 是 Rust 的一个流行的[序列化/反序列化箱，您可以这样做:](https://blog.logrocket.com/rust-serialization-whats-ready-for-production-today/)

```
use serde::Serialize;

struct A;

impl Serialize for A {
    fn serialize<S>(
    &self,
    _: S,
    ) -> Result<<S as serde::Serializer>::Ok, <S as serde::Serializer>::Error>
    where
    S: serde::Serializer,
    {
    todo!()
    }
}

```

## 如何在 Rust 中使用特征

已经讨论了我们如何定义和实现特征，让我们来看看如何在实践中使用它们。

### 函数参数中的特征和特征界限

我们可以使用 traits 作为函数参数，允许函数接受任何可以做`x`的类型，其中`x`是由 trait 定义的一些行为。我们还可以使用特征界限来细化和限制泛型，比如说我们接受任何实现特定特征的类型`T`。

乍一看，这两种使用特征的方式听起来好像实现了同样的事情，但是有一个微妙的区别。在有多个参数的情况下，我们可以通过使用特征界限来确保它们是相同的类型。

考虑这两个函数:

```
use std::fmt::Debug;

fn f(a: &Debug, b: &Debug) {
    todo!()
}

fn g<T: Debug>(a: &T, b: &T) {
    todo!()
}

```

忽略它们不做任何事情的事实，函数`f`将接受实现`Debug`特征的任意两个参数，即使它们是两个不同的类型。另一方面，`g`将只接受相同类型的两个参数，但是该类型可以是实现`Debug`的任何类型。

换句话说，`g`的参数必须是相同的类型，而`f`的参数可以是相同的类型，但也可以是两种不同的类型。

### 回归性状

我们可以使用特征作为函数的返回类型。有两种不同的方法可以做到这一点:`impl Trait`和`Box<dyn Trait>`。同样，差异是微妙但重要的。

```
use std::fmt::Debug;

fn dyn_trait(n: u8) -> Box<dyn Debug> {
    todo!()
}

fn impl_trait(n: u8) -> impl Debug {
    todo!()
}

```

`dyn_trait`函数可以返回任意数量的实现`Debug`特征的类型，甚至可以根据输入参数返回不同的类型。这就是所谓的特征对象。“Rust 编程语言”这本书有一节是关于使用 trait 对象进行动态调度的[的，如果你想深入研究的话。](https://doc.rust-lang.org/book/ch17-02-trait-objects.html#using-trait-objects-that-allow-for-values-of-different-types)

另一方面，`impl_trait`方法只能返回实现`Debug`特征的单一类型。换句话说，函数将总是返回相同的类型。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

虽然这种差异可能使`impl Trait`看起来不如 trait 对象有用，但它有一个非常重要的特性(除了更容易键入和操作之外):可以用它来返回迭代器和闭包。这在本书关于特征的章节中有进一步的解释，“返回实现特征的类型”

### 特质组合

你可能想同时使用多种特征。这就是所谓的特质组合。要做到这一点，你要把这些特征加在一起:`T: Trait1 + Trait2 + Trait3`。

在写这篇文章的时候，还没有办法用稳定锈病的另一个名字来指代一个特定的特征组合。然而，这种被称为特征别名的功能在晚间频道上可以看到。在 [GitHub](https://github.com/rust-lang/rust/issues/41517) 上查看跟踪问题。

### 超级列车

Rust 有办法指定一个特征是另一个特征的扩展，给了我们类似于其他语言中子类化的东西。

若要创建子 trait，请指示它以与类型相同的方式实现父 trait:

```
trait MyDebug : std::fmt::Debug {
    fn my_subtrait_function(&self);
}

```

要实现这个新的子线索，您必须实现父线索上所有必需的方法以及子线索上所有必需的方法。

### 关联类型

当处理特征时，[关联类型](https://blog.thomasheartman.com/posts/on-generics-and-associated-types)是一个经常出现的术语。一个最突出的例子是在`Iterator`特征中，它用于指示`next`函数的返回类型应该是什么。它们与泛型(或类型参数)有许多相似之处，但也有一些关键的区别。

简而言之，泛型可以做任何关联类型可以做的事情。但是，使用关联类型会将实现类型限制为只有该特征的一个实现。这在很多情况下都很有用，比如在`[Iterator](https://doc.rust-lang.org/std/iter/trait.Iterator.html)`和`[Deref](https://doc.rust-lang.org/std/ops/trait.Deref.html)`特征中。

要阅读更多关于关联类型的内容，请查看本书标题为“[的部分，在 Trait 定义中指定占位符类型，并使用关联类型](https://doc.rust-lang.org/book/ch19-03-advanced-traits.html?highlight=associated,types#specifying-placeholder-types-in-trait-definitions-with-associated-types)。

### 特征常数

特征也可以有相关的常数。这种方法不如特征方法常见，但也有它的用处。像方法一样，常量可以提供默认值。

```
trait ConstTrait {
    const GREETING: &'static str;
    const NUMBER: i32 = 42;
}

```

### `#[derive]`

有些特征可以自动派生，这意味着编译器可以为您实现这些特征。 [Rust by Example](https://doc.rust-lang.org/rust-by-example/trait/derive.html) 很好地解释了如何做到这一点以及它是如何工作的。这在`std::fmt::Debug`特质(`#[derive(Debug)]`)和其他特质中很常见。

派生特征是为您的类型获得额外功能的一个很好的方法，而无需您自己动手。

### 孤儿规则

在处理 traits 时，您可能会遇到这样的情况:您希望用一个外部机箱中的 trait 来实现另一个外部机箱中的 type。这不起作用是因为所谓的孤儿规则:如果你不拥有特征或类型，你就不能为类型实现特征。但是如果你拥有其中一个，你就可以。

根据[这本书](https://doc.rust-lang.org/book/ch10-02-traits.html#traits-defining-shared-behavior)，

> 如果没有[这个]规则，两个板条箱可以为相同的类型实现相同的特征，Rust 不知道使用哪个实现。

如果您遇到需要解决这个限制的情况，您可以使用 [newtype 模式](https://doc.rust-lang.org/book/ch19-03-advanced-traits.html#using-the-newtype-pattern-to-implement-external-traits-on-external-types)。

## 进一步阅读

特征是 Rust 的一个重要组成部分，所以有很多内容需要讨论。希望本教程已经让您理解了什么是特征，它们是如何工作的，以及如何处理高级用例。

如果你想自己了解更多关于这些主题的知识，我强烈建议你看一下贯穿全文的参考资料。如果你想在阅读本指南后更深入，我推荐你查看“ [The Rust 编程语言](https://doc.rust-lang.org/book/title-page.html#the-rust-programming-language)”中关于[特征](https://doc.rust-lang.org/book/ch10-02-traits.html#traits-defining-shared-behavior)和[高级特征](https://doc.rust-lang.org/book/ch19-03-advanced-traits.html#using-the-newtype-pattern-to-implement-external-traits-on-external-types)的章节，以及《Rust by Example》中关于特征的[章节。](https://doc.rust-lang.org/rust-by-example/trait.html)

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。