# Rust 元组的实际用例

> 原文：<https://blog.logrocket.com/practical-use-cases-rust-tuples/>

元组是不同类型的任意数量的值的集合。当函数必须返回多个结果时，它们有时很有用。

在本文中，我们将深入 Rust 中的元组，特别是 Rust `struct`的不同之处。

目录:

## Rust 元组概述

Rust 的[文档将元组](https://doc.rust-lang.org/std/primitive.tuple.html)定义为值的“有限异构序列”:

*   “有限”意味着它们具有固定的长度，也就是说，它们可以存储的值的数量是固定的。在计算机科学中，一个元组的长度也称为“arity”；不同算术的元组形成不同的类型
*   “异构”意味着元组的元素可能具有不同的类型
*   “序列”意味着我们使用元素的索引来访问元组中的元素。换句话说，元组的元素没有名称

我们可以简单地通过在括号内写一个逗号分隔的值列表来创建一个元组，可以选择指定类型:

```
fn main() {
    let immutable: (String, u8) = (String::from("LogRocket"), 120);
    println!("{} {}", immutable.0, immutable.1);
}
```

在上面的例子中，我们创建了一个名为`immutable`的简单元组，其中第一个值是一个`String`，第二个值是一个带`8`位的无符号整数。然后，从`0`开始，我们通过索引访问元组的元素来打印它们。运行时，该示例打印“LogRocket 120”。

默认情况下，元组是不可变的。例如，如果我们试图给上面的元组中的一个元素赋值，我们会得到一个错误:

```
fn main() {
    let immutable: (String, u8) = (String::from("LogRocket"), 120);
    immutable.1 = 142; // cannot assign
}
```

要将元组定义为可变的，我们必须在声明时指定`mut`:

```
fn main() {
    let mut mutable = (String::from("LogRocket"), 120);
    mutable.1 = 142;
    println!("{} {}", mutable.0, mutable.1);
}
```

在这两种情况下，我们都可以析构一个元组来初始化其他变量:

```
fn main() {
    let mut mutable = (String::from("LogRocket"), 120);
    let (name, value) = mutable;
    println!("{name}");
}
```

上面的程序只会打印“LogRocket”。

最后，我们可以使用`type`为元组类型分配一个别名:

```
type Test = (String, u8);

fn main() {
    let immutable: Test = (String::from("LogRocket"), 120);
    println!("{} {}", immutable.0, immutable.1);
}
```

这使得代码更具可读性。然而，需要注意的是，我们用`type`定义的只是一个别名。上面的`Test`和`(String, u8)`在结构上是等价的。

## 结构和元组结构

在本节中，我们将介绍元组的两种替代形式，即`struct` s 和元组`struct` s。如果元组是具有匿名字段的匿名类型，`struct` s 是具有命名字段的命名类型。Tuple `struct`处于中间位置，因为它们为我们提供了带有匿名字段的命名类型。

### 结构

与元组相似，它们可以处理不同类型的多个值。主要的区别是一个`struct`的元素都有一个名字。因此，我们不是通过索引来访问它们，而是通过名称来访问它们:

```
struct Person {
name: String,
age: u8
}

fn main() {
let p = Person {
name: String::from("Jhon Doe"),
age: 21
};

println!("Name: {}, age: {}", p.name, p.age);
}
```

一般来说，命名一个`struct`的元素的能力使得后者更加灵活。因此，出于可读性的考虑，通常最好使用`struct`而不是元组。尽管如此，这并不总是正确的，我们将在下一节看到。

### 元组 struts

元组`struct`与`struct`相同，唯一的区别是我们不必命名它们的字段:

```
struct Wrapper(u8);

fn main() {
    let w = Wrapper{0: 10};
    println!("{}", w.0);
}
```

由于字段没有名称，我们必须通过索引来访问它们。在上面的例子中，我们定义了一个`struct`来包装一个`u8`。然后，为了读和写它，我们必须使用它的索引`0`来引用它。

## 比较元组、`struct` s 和元组`struct` s

这一节比较了元组、`struct` s 和元组`struct` s。首先，我们来看看一些关键的底层差异。然后，我们将看到它们的实际用例。

### 实现差异

由于元组`struct`只是没有字段名称的`struct`，在本节中，我们将主要比较元组和元组`struct`的情况。我们将看到元组`struct`的情况对于`struct`也是如此。事实上，元组`struct`被分解成常规的`struct`。

首先，默认情况下，tuple `struct`的元素是私有的，不能在定义它们的模块之外访问。另外，元组`struct`定义了一个类型。因此，具有相同类型字段的两个元组`struct`是两种不同的类型。

另一方面，元素属于同一类型的两个元组定义一个类型。换句话说，元组在结构上是等价的，而元组`struct`不是。

其次，我们可以给元组`struct`添加属性，比如`#[must_use]`(当一个值没有被使用时发出警告)或者`#[derive(...)]`(在`struct`中自动实现 traits)。另一方面，元组不能有属性，但在默认情况下会实现一些特征。

第三，元组`struct`默认实现`Fn*`家族，允许它们像函数一样被调用，或者作为参数传递给更高阶的函数:

```
struct Name(String);

fn main() {
    let strings = [String::from("Log"), String::from("Rocket")];

    let names = strings.map(Name);
}
```

此外，tuple `struct`支持 struct update 语法，简化了我们创建一个新的`struct`实例的方式，其中大部分值等于同一个`struct`的另一个实例:

```
struct Person(String, u8);

fn main() {
    let john = Person {
            0: String::from("John"),
            1: 32
    };
    let another_john = Person {
            1: 25,
            ..john
    };

    println!("name: {}, age: {}", another_john.0, another_john.1);
}
```

在上面的例子中，我们首先创建了一个`Person`、`john`的实例，然后用它来创建一个新的实例`another_john`，只编辑由`Person`定义的字段的子集。

最后，我们可以在元组`struct`上定义方法，但是我们不能在元组上定义方法:

```
struct Person(String, u8);

impl Person {
    fn is_adult(&self) -> bool {
            return &self.1 >= &18;
    }
}

fn main() {
    let p = Person {
            0: String::from("John"),
            1: 20
    };

    println!("{}", p.is_adult());
}
```

### 用例

根据经验，只要类型的名称带有语义信息，我们就应该使用 tuple `struct` s。然后，当有多个字段时，我们应该移动到`struct` s。

尽管如此，在某些情况下，没有名字更具可读性。例如，`slice`的`as_chunks`方法定义如下:

```
pub fn as_chunks<const N: usize>(&self) -> (&[[T; N]], &[T])
```

它基本上输入一个常量`N`，定义块的大小，并返回一个元组，其中第一个元素是大小为`N`的块的数组，而第二个元素是数组的余数(即不足以组成新块的最后一个值)。

在这种情况下，类型本身清楚地表明结果的每个元素代表什么。因此，有名字可能是矫枉过正。在这种情况下，元组是一个很好的解决方案。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

尽管如此，我们通常处理复杂的数据类型，其中有名字有助于我们阅读代码。例如，考虑一个操纵图书信息的程序，特别是书名、作者和价格。如果我们用一个元组来建模，我们可能会得出:

```
type Book = (String, String, f32)
```

然而，我们对`Book`的定义包含两个`String`字段。我们怎么知道哪个代表标题，哪个代表作者？在这种情况下，`struct`是一个更好的选择:

```
struct Book {
    title: String,
    author: String,
    price: f32
}
```

尽管如此，有时我们可能会发现自己只使用了一种元素。例如，我们可能希望这样做，以使用类型来进一步增强代码的清晰度。考虑一个用三个部分构建 URL 的函数:

*   子整环
*   域名
*   一级域名

首先，我们可能会想出一个具有以下特征的函数:

```
fn make_url(
    subdomain: String,
    domain_name: String,
    top_level_domain: String
) -> String {
    todo!();
}

fn main() {
    make_url(
        String::from("www"),
        String::from("mydomain"),
        String::from("com")
    );
}
```

不过，我们在单个结构中仍然有很多的`String`,很容易混淆参数的顺序。此外，Rust 不支持命名参数，所以我们必须记住正确的顺序。

使用元组`struct` s，我们可以编写一个更加自解释的签名:

```
struct Subdomain(String);
struct DomainName(String);
struct TopLevelDomain(String);
struct URL(String);

fn make_url(
    subdomain: Subdomain,
    domain_name: DomainName,
    top_level_domain: TopLevelDomain
) -> URL {
    todo!();
}

fn main() {
    make_url(
            Subdomain(String::from("www")),
            DomainName(String::from("mydomain")),
            TopLevelDomain(String::from("com"))
    );
}
```

在上面的例子中，我们利用元组`struct`包装`String`，在这种情况下，命名每个`struct`的字段是没有意义的。事实上，这会使代码更难阅读。

## 结论

在这篇文章中，我们深入研究了 Rust 中的元组。首先，我们简要了解了它们是什么以及如何使用它们。其次，我们简要介绍了可能的替代方案，即`struct` s 和元组`struct` s。最后，我们通过查看每个替代方案的底层实施细节和实际使用案例来比较所有这些替代方案。

最后，它们可以用来完成更多几乎相同的用例。像往常一样，在选择中真正重要的是它们如何影响代码的可读性和可维护性。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。