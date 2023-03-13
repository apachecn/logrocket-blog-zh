# 如何在 Rust 中构建多态组件

> 原文：<https://blog.logrocket.com/build-polymorphic-components-rust/>

如果你熟悉[面向对象编程(OOP)](https://en.wikipedia.org/wiki/Object-oriented_programming) ，你可能听说过多态。多态是 OOP 的一个有用的特性，它描述了一个变量或接口在其层次树中展示不同对象行为的能力。

多态性允许您构建灵活的应用程序，在不破坏系统的情况下轻松适应运行时需求。除了减少样板代码库之外，多态性还确保了应用程序的组件不会彼此紧密耦合。

在本文中，我们将探索 Rust 编程语言中多态性的概念。我们还将展示在 Rust 中实现多态性的不同方法。

*向前跳转:*

## 理解 Rust 中的多态性

Rust 中多态性的基本理论与任何其他 OOP 语言相似。

考虑一个让不同生物行走的程序。你可以设计一个`walk`函数来接受一个特定生物的具体类型(比如一个`Cat`)作为参数。

现在，假设你想走一段`Dog`；您必须创建另一个接受`Dog`作为其参数的函数。

根据这个概念，这意味着每个生物都需要各自的功能来执行相应的`walk`,如下面的代码片段所示:

```
struct Cat;

impl Cat {
    fn walk(&self) -> (){
       println!("Cat is walking")
    }
}

struct Dog;

impl Dog {
    fn walk(&self) -> (){
       println!("Dog is walking")
    }
}

fn main() {
   let cat = Cat{};
   let dog = Dog {};
   cat.walk();
   dog.walk();
}
```

这是一个有缺陷的实现；它在你的应用程序中引入了重复的代码，也使得为每一个生物维护越来越多的功能变得困难。随着您在应用程序中创建越来越多的个体生物，函数的数量将呈指数增长。

使用多态性，您可以创建一个接受任何对象的函数，只要它执行特定的操作。在 creatures 示例的上下文中，该函数将接受任何实现了`walk()`方法的对象。

每个生物都有自己定制的`walk()`方法的实现，当它在各自的生物对象上被调用时就会运行。

## 在 Rust 中实现多态性

Rust 为通过[特征](https://blog.logrocket.com/rust-traits-a-deep-dive/)在应用程序中实现多态性提供了不同的选项。每种选择都有自己的特点和优势。在决定采用哪种实现时，需要考虑几个权衡因素。让我们来看看。

### 静态调度

静态分派的概念决定了在编译时调用`trait`的哪个实现，这是基于它作为参数接收的类型值。默认情况下，这是 Rust 程序执行操作的方式。

它使用[泛型](https://blog.logrocket.com/understanding-rust-generics/)来减少重复代码，允许您编写其他类型可以接受的函数或类型，而不必事先指定具体的类型。相反，您需要为这些类型提供变量，程序将在运行时调用这些变量。

以我们的 creatures 为例，下面是如何使用泛型实现多态性:

```
trait Walkable {
    fn walk(&self);
}

struct Cat;
struct Dog;

impl Walkable for Cat {
    fn walk(&self) {
        println!("Cat is walking");
    }
}

impl Walkable for Dog {
    fn walk(&self) {
        println!("Dog is walking");
    }
}

fn generic_walk<T: Walkable>(t: T) {
    t.walk();
}

fn main() {
    let cat = Cat{};
    let dog = Dog{};

    generic_walk(cat); // Cat is walking
    generic_walk(dog); // Dog is walking
}
```

上面的代码声明了一个包含方法`walk`的`Walkable`特征，没有返回类型。任何实现这一特征的结构或类型也必须为`walk`方法提供具体的实现。因此，声明的结构，`Cat`和`Dog`，它们都实现了特征，也各自提供了它们各自的`walk`方法的具体实现。

`generic_walk()`函数是一个通用函数，它接受任何类型，只要它实现了`Walkable`特征。该函数调用作为参数传递的对象的`walk()`方法，并运行特定于该类型的操作。

`main()`方法演示了`generic_walk()`函数如何在每次被调用时接受`Cat`和`Dog`对象，从而隐式调用每种类型的`walk()`方法。

#### 选择静态调度方法

静态分派特征使用[单态化](https://rustc-dev-guide.rust-lang.org/backend/monomorph.html)为作为参数传递的每种类型编译并创建`generic_walk()`函数的多个副本，因此，它知道每种类型的执行过程。这在性能方面非常有效，因为程序决定在编译时而不是运行时使用实现，但是这种效率是有代价的。

由于程序为每个传递的类型创建了一个`generic_walk()`函数的副本，这意味着它为每个副本分配了内存空间。如果您对提高性能感兴趣，并且不关心应用程序方案中二进制文件大小的增加，那么您应该考虑这种方法。

### 动态调度

在动态分派方法中，程序根据提供的具体类型决定在运行时执行的特征的实现。动态分派概念不同于静态分派，因为它不必创建它所执行的通用函数的多个副本。相反，它引用函数的单个副本，并确定运行时要执行的具体实现。

使用我们的 creatures 示例，下面是如何使用动态分派方法来实现多态性:

```
trait Walkable {
    fn walk(&self);
}

struct Cat;
struct Dog;

impl Walkable for Cat {
    fn walk(&self) {
        println!("Cat is walking");
    }
}

impl Walkable for Dog {
    fn walk(&self) {
        println!("Dog is walking");
    }
}

fn dynamic_dispatch(w: &dyn Walkable) {
    w.walk();
}

fn main() {
    let cat = Cat{};
    let dog = Dog{};

    dynamic_dispatch(cat); // Cat is walking
    dynamic_dispatch(dog); // Dog is walking
}
```

就像前面的静态分派示例一样，上面的代码片段包含了由`Cat`和`Dog`结构实现的`Walkable`特征。这里的显著区别是`dynamic_dispatch()`函数接受一个 trait 对象作为参数。

函数括号中的`[dyn](https://doc.rust-lang.org/std/keyword.dyn.html)`关键字用于表示该函数通过所提供的 trait 的具体实现来执行动态分派多态性。

如 main 方法所示，`dynamic_dispatch()`函数接受`Cat`和`Dog`对象，并隐式调用它们各自的`walk()`方法。

#### 选择动态调度方法

就性能而言，动态分派不如静态分派有效，特别是因为它必须在运行时确定要运行的实现及其执行依赖关系。然而，动态调度通过最小的内存使用弥补了这一缺陷。这是因为它不需要像静态分派方法那样创建多态函数的多个副本。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在需要优先考虑内存使用而不是性能的情况下，例如构建嵌入式系统或任何其他依赖于内存使用的程序时，动态调度方法非常有用。

### 枚举

[枚举](https://doc.rust-lang.org/book/ch06-00-enums.html)是数据结构，允许你用不同的变量来表示数据。在实现多态性时，它们也非常有用。

通过枚举，您可以使用[模式匹配](https://doc.rust-lang.org/book/ch06-02-match.html)对每个变量执行特定操作，如下所示:

```
    Cat{},
    Dog{},
}

impl Creature {

    pub fn walk(&self) {
        match self {
            Creature::Cat{} => println!("Cat is walking"),
            Creature::Dog{} => println!("Dog is walking")
        }
    }
}

fn enum_walk(c: Creature) {
    c.walk();
}

fn main() {
    let cat = Creature::Cat{};
    let dog = Creature::Dog{};

    enum_walk(cat); // Cat is walking
    enum_walk(dog); // Dog is walking
}
```

在上面的代码中，`Cat`和`Dog`结构被声明为`Creature`枚举中的变量。同样，使用模式匹配，当调用 enum 上的`walk()`方法时，为每个变量创建定制实现。

`enum_walk()`函数接受一个`Creature`枚举作为参数(即枚举的任何变体或派生物都是该函数的有效参数)。然后，它对运行时传递的变量调用`walk()`方法，如`main()`方法所示。

#### 选择枚举法

通过枚举构建多态程序通常被认为没有前面讨论的静态和动态分派方法复杂。但是，它会引入紧耦合。

枚举的所有变量都必须在枚举范围内声明，并且它不能被其他库中声明的类型扩展。如果您希望在应用程序的其他领域利用枚举的多态优势，这可能是一个限制。如果你有兴趣学习更多关于枚举的知识，这里有一个指南帮助你入门。

## 结论

在本文中，我们讨论了 OOP 中的多态性概念及其在 Rust 编程语言中的特性。我们还演示了如何使用静态分派、动态分派和枚举在 Rust 中实现多态性，并讨论了每种方法的优缺点。

本教程中提供的代码片段可以在 [GitHub](https://github.com/olu-damilare/rust-polymorphism) 上获得。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。