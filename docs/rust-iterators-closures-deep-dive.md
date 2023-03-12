# 深入探究 Rust 迭代器和闭包

> 原文：<https://blog.logrocket.com/rust-iterators-closures-deep-dive/>

如果你还不知道，Rust 是一种很棒的编程语言。事实上，它太棒了，以至于 Stack Overflow [在过去的六年里把它选为最受欢迎的语言。](https://insights.stackoverflow.com/survey/2021#technology-most-loved-dreaded-and-wanted)

Rust 是一种系统编程语言。虽然 C 和 C++等语言提供了对内存管理和垃圾收集等组件的细粒度控制，但由于语法的复杂性，它们使得构建现代应用程序更加困难。

与此同时，像 Python 和 JavaScript 这样的语言——虽然对开发人员的生产力很有帮助——却缺乏低级语言所提供的性能和安全性。

这就是生锈的原因。Rust 结合了编程的简易性和对核心系统级配置的访问。它从一开始就具有内存安全性、并发性和安全性，是构建可伸缩的高性能应用程序的最佳选择。

本文不是对 Rust 的介绍。我最近写了一篇关于[开始使用 Rust](https://www.hardcoder.io/rust-getting-started-with-the-most-loved-programming-language/) 的文章，所以如果你是新手的话可以看看。

在本文中，我们将详细研究迭代器和闭包的中间 Rust 概念。

(有趣的事实:Rust 非常重视双引号和分号。来自 JS/Python 背景，我学习 rust 的大部分时间都花在了调试这些问题上。)

## Rust 中的迭代器

迭代是遍历一组值的过程。您可能熟悉像“for 循环”、“while 循环”和“for each 循环”这样的循环

在 Rust 中，迭代器帮助我们实现了循环的过程。在其他语言中，您可以直接开始遍历一个值数组。然而，在 Rust 中，你必须首先声明一个迭代器。

让我们看一个简单的数组例子。我将声明一个命名年龄数组，我们将在本节中使用它。

```
let ages = [27,35,40,10,19];

```

现在，让我们通过调用`iter()`函数将其声明为可循环数组。

```
let ages_iterator = ages.iter();

```

如果这看起来令人困惑，不要担心。一旦我们看到一个实际的实现，你就会更好地理解它。

让我们循环遍历这些值，并一个接一个地打印出来。

```
for age in ages_iterator {
  println!("Age = {:?}",age);
}

```

您可以看到，我们使用了`ages_iterator`而不是 ages 向量来执行循环操作。

默认情况下，所有的信任列表(数组、向量、映射)都是不可迭代的。使用`iter()`函数，我们告诉 Rust 给定的数组可以用于循环。

这也被称为迭代器“懒惰”类似于函数在被调用之前不做任何事情，`iter()`函数用于调用数组中的迭代。

### 迭代器特征

如果你不知道什么是铁锈性状，[这篇文章](https://blog.rust-lang.org/2015/05/11/traits.html)将为你提供关于性状的详细信息。简单来说，traits 类似于 C++中的抽象类或者 Java 中的接口。

特征是 Rust 中抽象的基础，它们允许我们定义可以跨不同 Rust 类型共享的方法。

想象一下，编写一个具有打印摘要消息功能的类。您可以调用这个类并调用它的方法来打印出不同的消息，比如脸书邮报或 Twitter tweet。类似地，您可以编写一个打印出摘要的特征，并且您可以[实现](https://doc.rust-lang.org/std/keyword.impl.html)(继承)该特征来打印一篇文章或一条 tweet。

在 Rust 中，所有迭代器都实现了一个名为“iterator”的特性，这个特性有一个名为`next()`的方法。下面是来自官方 Rust 文档的关于迭代器特征如何定义的代码。

```
pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;

    // methods with default implementations elided
}

```

不要担心新的语法——您需要理解的是，当您附加了`iter()`方法以使值列表可迭代时，它将迭代器特性与类似`next()`的方法一起实现。

我们来详细看看`next()`方法。

### `next()`方法

当您不想使用循环遍历迭代器时,`next()`方法非常有用。简单地调用这个方法就可以一个接一个地返回元素。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们不使用循环而是使用`next()`方法打印出我们的年龄数组。注意，我们使用`mut`关键字将迭代器声明为可变的；这是因为 Rust 中的所有变量在默认情况下都是不可变的。

```
fn main(){
    let ages = [27,35,40,10,19];

    let mut ages_iterator = ages.iter();

    // display the iterator
    println!("{:?}",ages_iterator);

    // display each element in array
    println!("{:?}",ages_iterator.next());
    println!("{:?}",ages_iterator.next());
    println!("{:?}",ages_iterator.next());
    println!("{:?}",ages_iterator.next());
    println!("{:?}",ages_iterator.next());
    println!("{:?}",ages_iterator.next());

    // display the iterator
    println!("{:?}",ages_iterator);

    // display the array
    println!("{:?}",ages);
}// example code for using the next method. 

```

在上面的例子中，我们首先声明一个可变迭代器，然后使用`next()`方法打印 ages 数组中的每一项。以下是这段代码的输出:

```
Iter([27, 35, 40, 10, 19])
Some(27)
Some(35)
Some(40)
Some(10)
Some(19)
None
Iter([])
[27, 35, 40, 10, 19]

```

可以看到每个`next()`方法都返回第一个元素，然后将它从数组中移除。最后一个`next()`方法返回`None`，因为数组中没有值了。

您还应该注意到，`next()`方法清除的是`ages_iterator`，而不是实际的数组本身。当我们打印迭代器和数组时，可以看到迭代器是空的，但数组不是。

现在你已经知道了 Rust 中迭代器的工作原理，让我们看看闭包是如何在 Rust 中实现的。

## 生锈的盖子

简单地说，闭包是匿名函数，即使在代码块之外执行，它也可以访问代码块的局部范围。

对于闭包，有两件事非常重要。它们总是匿名函数(或内联函数),它们的主要特征是让我们能够访问局部范围(环境)。

下面是声明闭包的语法:

```
let closure_name = |param1, param2| -> return_type {.....  closure logic …..};

```

你可以看到`closure_name`是一个存储闭包的变量，但是实际的闭包是一个匿名函数。

虽然您必须为函数中的`params`以及返回值定义数据类型，但是闭包可以自动处理它，但是传递给闭包的值的数据类型应该是一致的，否则它会在编译时抛出错误。为了向闭包传递参数，我们使用双管符号(`||`)。

让我们编写一个简单的闭包，它将给定值递增 1:

```
fn main() {
    let mut my_val = 0;
    let mut increment_closure = || {
        my_val = my_val + 1;
        println!("Value : {:?}",my_val);
    };
    increment_closure();
    increment_closure();
    increment_closure();
    increment_closure();
    increment_closure();
}

```

在上面的代码中，您可以看到我们声明了一个名为`increment_closure`的闭包，它将变量`my_val`的值递增 1，并打印出`my_val`的当前值。

下面是这段代码的输出:

```
Value : 1
Value : 2
Value : 3
Value : 4
Value : 5

```

您可以看到闭包保留了变量的值。注意，我们在闭包中使用了关键字`mut`。这是因为您必须明确地告诉 Rust，闭包将会修改环境。

### 移动闭包

一般来说，闭包会创建对其作用域中的实体的引用，但是还有另一种类型的闭包，称为移动闭包，它拥有它所使用的所有变量的所有权。

我们使用`move`关键字来定义一个移动闭包。下面是语法:

```
let closure_name = move |param1, param2| -> return_type {.....  closure logic …..};

```

移动闭包是在处理高级 Rust 特性(比如并发性)时使用的，因此它超出了本文的范围。要深入了解 Rust 闭包，[查看这篇文章](https://medium.com/coding-rust/best-explanation-of-closure-in-rust-2b20210eba53)。

现在您已经理解了闭包是如何工作的，您可能想知道为什么我们首先需要它们。闭包主要用于抽象。

如果您有一个想要更新的全局变量，任何函数都可以修改该变量。然而，编写一个闭包来更新一个变量可以确保只有那个闭包可以访问那个变量——这是 Rust 设计的一部分，以确保一个高度安全的编程语言。

## 结论

与大多数编程语言不同，Rust 中不能使用循环迭代一组值(数组、向量、地图)。我们必须调用`iter()`方法来使列表可迭代。您还可以使用`next()`方法遍历列表中的值，一次一个。

闭包是匿名的内联函数，可以访问它的环境。它们用于抽象环境，以便环境的变量只能由有限的实体修改。虽然普通闭包会创建对它们所处理的值的引用，但是移动闭包可以获得环境中值的所有权。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。