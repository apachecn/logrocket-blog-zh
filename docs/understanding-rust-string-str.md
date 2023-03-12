# 理解 Rust 中的 String 和&str

> 原文：<https://blog.logrocket.com/understanding-rust-string-str/>

根据您在遇到 Rust 之前的编程背景，您可能会惊讶于 Rust 有不止一种类型的字符串，即`String`和`str`。

在本帖中，我们将看看`String`和`str`之间的区别，或者更具体地说，`String` / `&String`和`&str`之间的区别——尤其是当涉及到何时使用哪一个的问题时。

理解这篇文章中提到的概念将使你能够以更有效的方式使用 Rust 中的字符串，同时也能更好地理解其他人的代码，因为你将更好地理解为什么他们对字符串的处理做出某些决定背后的思维过程。

我们开始吧！

## 我们将涵盖的内容

首先，我们将着眼于更理论的方面，比如类型的不同结构以及在可变性和内存位置方面的差异。

然后，我们将着眼于这些差异的实际含义，讨论何时使用哪种类型以及为什么这样做有意义。

最后，我们将查看一些简单的代码示例，展示在 Rust 中使用字符串的不同方式。

如果您刚刚开始使用 Rust，这篇文章可能对您特别有用，因为它将向您解释为什么当您试图在程序中使用字符串和字符串操作来实现某些目标时，您的代码有时无法编译。

然而，即使您对 Rust 并不完全陌生，字符串对软件工程的许多不同领域都有着根本的相关性——至少对它们在您所使用的语言中是如何工作的有一个基本的了解是非常重要的。

## 理论

在这里，我们将在语言层面上讨论这些类型之间的区别，以及在使用它们时的一些含义。

一般来说，人们可以说这种差异归结于所有权和内存。Rust 中所有字符串类型的一个共同点是，它们总是保证是有效的 UTF-8。

## `String`

`String`是需要分配的自有类型。它的大小是动态的，因此它的大小在编译时是未知的，因为内部数组的容量可以随时改变。

类型本身是以下形式的结构:

```
pub struct String {
    vec: Vec<u8>,
}

```

因为它包含一个`Vec`，我们知道它有一个指向内存块、大小和容量的指针。

大小告诉我们字符串的长度，而容量告诉我们在我们需要重新分配之前它还能有多长。指针指向堆上的一个连续的字符数组，长度为`capacity`，其中有`size`个条目。

关于`String`的伟大之处在于它在使用方面非常灵活。我们总是可以用它创造新的、动态的字符串，并使它们变异。当然，这是有代价的，因为我们总是需要分配新的内存来创建它们。

## 什么是`&String`？

`&String`类型只是对一个`String`的引用。这意味着这不是一个拥有的类型，它的大小在编译时是已知的，因为它只是一个指向实际的`String`的指针。

关于`String`还没说的`&String`就没什么好说的了；只是因为它不是一个拥有的类型，我们可以传递它，只要我们引用的东西不超出范围，我们不需要担心分配。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

大多数情况下，`String`和`&String`之间的含义归结为[引用和借用](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html)。

`&String`的一个有趣的方面是它可以被 Rust 编译器解除强制转换为`&str`。就 API 灵活性而言，这非常好。但是，反过来就不行了:

```
fn main() {
    let s = "hello_world";
    let mut mutable_string = String::from("hello");

    coerce_success(&mutable_string);
    coerce_fail(s);
}

fn coerce_success(data: &str) { // compiles just fine, even though we put in a &String
    println!("{}", data);
}

fn coerce_fail(data: &String) { // error - expected &String, but found &str
    println!("{}", data);
}

```

## 什么是`&str`？

最后，我们来看看`&str`。因为`&str`只包含一个指向内存的指针(以及一个大小)，所以它的大小在编译时是已知的。

内存可以在堆上、堆栈上，或者直接来自可执行文件的静态内存。它不是一个拥有的类型，而是一个字符串片的只读引用。Rust 实际上保证了当`&str`在范围内时，底层内存不会改变，即使是跨线程的。

如上所述，`&String`可以被强制为`&str`，这使得`&str`成为函数参数的一个很好的候选，如果不需要可变性和所有权的话。

当需要一个不需要改变的字符串片段(视图)时，最好使用它。但是，请记住，`&str`只是一个指向`str`的指针，它在编译时大小未知，但本质上不是动态的，所以它的容量不能改变。

知道这一点很重要，但是如上所述，当`&str`存在时，`&str`指向的内存不能被更改，即使是`str`的所有者也不能更改。

让我们来看看这其中的一些实际含义。

## `&str`的实际含义

最重要的是，理解以上内容会让你写出 Rust 编译器乐意接受的更好的代码。

但是除此之外，还有一些关于 API 设计和性能的问题需要考虑。

就性能而言，您必须考虑到创建新的`String`总是会触发分配。如果您可以避免额外的分配，您应该总是这样做，因为它们会花费相当多的时间，并且会对您的运行时性能造成严重影响。

### 嵌套循环示例案例

考虑这样一种情况，在嵌套循环中，您总是需要程序中某个字符串的不同部分。如果你每次都创建新的`String`子字符串，你将不得不为每个子字符串分配内存，并且创建比你需要的更多的工作，因为你可以只使用一个`&str`字符串切片，一个你的基本`String`的只读视图。

在应用程序中传递数据也是如此。如果你传递拥有的`String`实例，而不是可变的借用(`&mut String`)或它们的只读视图(`&str`)，那么将会触发更多的分配，并且必须保留更多的内存。

所以就性能而言，知道何时发生分配以及何时不需要分配是很重要的，因为您可以在必要时重用字符串片。

### API 设计

当涉及到 API 设计时，事情变得有点复杂，因为你会对你的 API 有一定的目标，选择正确的类型对于你的 API 的消费者来说是非常重要的，以便能够达到你在他们面前提出的目标。

例如，由于`&String`可以被强制为`&str`，而不是反过来，如果你只需要一个字符串的只读视图，使用`&str`作为参数类型通常是有意义的。

此外，如果一个函数需要改变一个给定的字符串，传入一个`&str`是没有意义的，因为它将是不可变的，你必须从它创建一个`String`然后返回它。如果突变是你所需要的，`&mut String`就是你要走的路。

### 拥有的字符串

这同样适用于需要拥有的字符串的情况，例如将它发送给线程，或者创建需要拥有的字符串的结构。在这些情况下，你需要直接使用`String`，因为`&String`和`&str`都是借用类型。

在任何情况下，当所有权和可变性很重要时，`String`和`&str`之间的选择就变得很重要。在大多数情况下，如果您没有使用正确的类型，代码就不会编译，但是如果您没有很好地理解哪种类型具有哪些属性，以及在从一种类型转换到另一种类型的过程中会发生什么，您可能会创建令人困惑的 API，这些 API 会进行比所需更多的分配。

让我们看一些演示这些含义的代码示例。

## `String`和`&str`使用的代码示例

下面的例子展示了前面提到的一些情况，并包含了在每种情况下应该做什么的建议。

请记住，这些是孤立的、人为的示例，您应该在代码中做什么将取决于许多其他因素，但这些示例可以用作基本指南。

### 常量字符串

这是一个最简单的例子。如果您的应用程序中需要一个常量字符串，那么只有一个好的方法来实现它:

```
const CONST_STRING: &'static str = "some constant string";

```

`CONST_STRING`是只读的，将静态地存在于你的可执行文件中，并在执行时被加载到内存中。

### 字符串突变

如果你有一个`String`并且你想在函数中改变它，你可以使用`&mut String`作为参数:

```
fn main() {
    let mut mutable_string = String::from("hello ") 
    do_some_mutation(&mut mutable_string);
    println!("{}", mutable_string); // hello add this to the end
}

fn do_some_mutation(input: &mut String) {
    input.push_str("add this to the end"); 
}

```

这样，您可以改变您的实际`String`，而不必分配新的`String`。然而，在这种情况下也可能发生分配，例如，如果您的`String`的初始`capacity`太小而无法容纳新内容。

### 拥有的字符串

有些情况下，你需要一个自己的字符串；例如，当从函数中返回一个字符串时，或者如果您希望将它传递给另一个拥有所有权的线程(例如，返回值，或传递给另一个线程，或在运行时生成)。

```
fn main() {
    let s = "hello_world";
    println!("{}", do_something(s)); // HELLO_WORLD
}

fn do_something(input: &str) -> String {
    input.to_ascii_uppercase()
}

```

此外，如果我们需要传递拥有所有权的东西，我们实际上必须传入一个`String`:

```
struct Owned {
    bla: String,
}

fn create_owned(other_bla: String) -> Owned {
    Owned { bla: other_bla }
}

```

由于`Owned`需要一个自己拥有的字符串，如果我们传入一个`&String`或`&str`，我们将不得不在函数内部把它转换成一个`String`，触发一个分配，所以在这种情况下我们还不如传入一个`String`来开始。

## 只读参数/切片

如果我们不改变字符串，我们可以只使用`&str`作为参数类型。这甚至在`String`的情况下也能工作，因为`&String`可以被解除强制到`&str`:

```
const CONST_STRING: &'static str = "some constant string";

fn main() {
    let s = "hello_world";
    let mut mutable_string = String::from("hello");

    print_something(&mutable_string);
    print_something(s);
    print_something(CONST_STRING);
}

fn print_something(something: &str) {
    println!("{}", something);
}

```

如您所见，我们可以使用`&String`、`&'static str`和`&str`作为`print_something`方法的输入值。

## 结构

我们可以对结构使用`String`和`&str`。

重要的区别是，如果一个结构需要拥有它们的数据，你需要使用`String`。如果您使用`&str`，您需要使用 Rust lifetimes 并确保该结构不会比借用的字符串活得长，否则它将无法编译。

例如，这是行不通的:

```
struct Owned {
    bla: String,
}

struct Borrowed<'a> {
    bla: &'a str,
}

fn create_something() -> Borrowed { // error: nothing to borrow from
    let o = Owned {
        bla: String::from("bla"),
    };

    let b = Borrowed { bla: &o.bla };
    b
}

```

使用这段代码，Rust 编译器会给我们一个错误，即`Borrowed`包含一个被借用的值，但是被借用的`o`超出了作用域，因此当我们返回我们的`Borrowed`时，被借用的值超出了作用域。

我们可以通过传递要借用的值来解决这个问题:

```
struct Owned {
    bla: String,
}

struct Borrowed<'a> {
    bla: &'a str,
}

fn main() {
    let o = Owned {
        bla: String::from("bla"),
    };

    let b = create_something(&o.bla);
}

fn create_something(other_bla: &str) -> Borrowed {
    let b = Borrowed { bla: other_bla };
    b
}

```

这样，当我们返回`Borrowed`时，借用的值仍然在范围内。

## 结论

在这篇文章中，我们看了 Rust 字符串类型`String`和`str`之间的区别，看了这些区别是什么，以及它们应该如何影响你的 Rust 编码习惯。

此外，我们还看了一些代码示例，展示了一些在哪里使用哪种类型以及为什么使用的模式。

我希望这篇文章有助于消除您可能对为什么您的字符串相关代码不能在 Rust 中编译产生的一些困惑，或者如果您在 Rust 之旅中走得更远，有助于您编写更好的 Rust 代码。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。