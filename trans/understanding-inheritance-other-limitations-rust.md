# 理解 Rust - LogRocket 博客中的继承和其他限制

> 原文：<https://blog.logrocket.com/understanding-inheritance-other-limitations-rust/>

***编者按:*** *这个 Rust guide 在 2022 年 8 月 3 日更新，加入了关于双向链表和异步代码中不`static`的借用* *东西* *的信息。*

作为 Rust 子版块的版主，我经常看到关于开发人员试图将他们各自的语言范式移植到 Rust 的帖子，结果好坏参半，成功的程度也各不相同。

在本指南中，我将描述开发人员在将其他语言范式移植到 Rust 时遇到的一些问题，并提出一些替代解决方案来帮助您解决 Rust 的局限性。我们开始吧！

## 铁锈的遗传

可以说，关于面向对象(OO)语言中缺失的特性，被问得最多的是继承。为什么 Rust 不让一个结构从另一个结构继承？Rust 继承很有趣。

你肯定会说，即使在面向对象的世界里，继承也有不好的名声，如果可能的话，从业者通常喜欢组合。但是你也可以争辩说，允许一个类型以不同的方式执行一个方法可能会提高性能，因此对于那些特定的实例是可取的。

这里有一个取自 Java 的经典例子:

```
interface Animal {
    void tell();
    void pet();
    void feed(Food food);
}

class Cat implements Animal {
    public void tell() { System.out.println("Meow"); }
    public void pet() { System.out.println("purr"); }
    public void feed(Food food) { System.out.println("lick"); }
}

// this implementation is probably too optimistic...
class Lion extends Cat {
    public void tell() { System.out.println("Roar"); }
}

```

第一部分可以用 traits 实现:

```
trait Animal {
    fn tell(&self);
    fn pet(&mut self);
    fn feed(&mut self, food: Food);
}

struct Cat;

impl Animal for Cat {
    fn tell(&self) { println!("Meow"); }
    fn pet(&mut self) { println!("purr");
    fn feed(&mut self, food: Food) { println!("lick"); }
}

```

但是第二部分就不那么容易了:

```
struct Lion;

impl Animal for Lion {
    fn tell(&self) { println!("Roar"); }
    // Error: Missing methods pet and feed
}

```

显然，最简单的方法是复制这些方法。是的，重复是不好的。复杂性也是如此。创建一个自由方法，如果需要对代码进行重复数据删除，可以从`Cat`和`Lion` `impl`调用该方法。

但是等等，等式的多态性部分呢？这就是事情变得复杂的地方。面向对象语言通常给你动态调度，Rust 让你在静态和动态调度之间选择，两者各有利弊。

```
// static dispatch
let cat = Cat;
cat.tell();

let lion = Lion;
lion.tell();

// dynamic dispatch via enum
enum AnyAnimal {
   Cat(Cat),
   Lion(Lion),
}

// `impl Animal for AnyAnimal` left as an exercise for the reader

let animals = [AnyAnimal::Cat(cat), AnyAnimal::Lion(lion)];
for animal in animals.iter() {
   animal.tell();
}

// dynamic dispatch via "fat" pointer including vtable
let animals = [&cat as &dyn Animal, &lion as &dyn Animal];
for animal in animals.iter() {
   animal.tell();
}

```

与垃圾收集语言不同，每个变量在编译时都必须有一个具体的类型。此外，对于 enum 的情况，委托 trait 的实现是繁琐的，但是像 [`ambassador`](https://docs.rs/ambassador/0.2.1) 这样的箱子可以有所帮助。

将功能委托给成员的一种相当简单的方法是使用多态性的`Deref`特征。定义在`deref`目标上的函数可以直接在 derefee 上调用。但是，请注意，这通常被[视为反模式](https://github.com/rust-unofficial/patterns/blob/ef80980ec74bbc0a189bb850036d5df4eb8c4621/anti_patterns/deref.md)。

最后，可以为所有实现其他特征的类实现一个特征。它需要专门化，这是目前的一个夜间特性(尽管有一个可用的[变通方法](https://github.com/dtolnay/case-studies/blob/master/autoref-specialization/README.md)，如果你不想写出所有需要的样板文件，甚至打包在一个[宏箱](https://crates.io/crates/spez)中)。特征很可能会相互继承，尽管它们只规定了行为，而不是数据。

## 双向链表和其他基于指针的数据结构

许多从 C++进入 Rust 的人首先想要实现一个“简单的”双向链表。他们很快了解到这实际上远不简单。

这是因为 Rust 希望明确所有权，因此双向链表需要对指针和引用进行非常复杂的处理。

新来的人可能会尝试编写以下结构:

```
struct MyLinkedList {
    value: T
    previous_node: Option<Box<T>>,
    next_node: Option<Box<T>>,
}

```

嗯，当他们注意到这个方法失败时，他们会添加`Option`和`Box`。但是，一旦他们试图实现插入，他们就会遇到令人不快的意外:

```
impl MyLinkedList {
    fn insert(&mut self, value: T) {
        let next_node = self.next_node.take();
        self.next_node = Some(Box::new(MyLinkedList {
            value,
            previous_node: Some(Box::new(*self)), // Ouch
            next_node,
        }));
    }
} 

```

当然，[借检查器](https://blog.logrocket.com/introducing-the-rust-borrow-checker/)不会允许这样。[值的所有权](https://blog.logrocket.com/understanding-ownership-in-rust/)完全混乱。拥有它所包含的数据，因此列表中的每个节点将被列表中的前一个和下一个节点所拥有。Rust 只允许每个数据有一个所有者，所以这至少需要一个`Rc`或`Arc`来工作。但是即使这样也会变得很麻烦，更不用说引用计数的开销了。

幸运的是，您不必编写双向链表，因为标准库已经包含了一个(`std::collections::LinkedList`)。此外，与简单的`Vec`相比，这很少会给你带来好的性能，所以你可能需要相应地测量。

如果你真的想写一个双向链表，你可以参考“[用完全过多的链表学习 Rust](https://rust-unofficial.github.io/too-many-lists/)”，这可能会帮助你既写链表，又在这个过程中学到很多关于[不安全 Rust](https://blog.logrocket.com/unsafe-rust-how-and-when-not-to-use-it/) 的知识。

**注意**，单链表绝对可以用盒子链来构建。事实上，Rust 编译器包含了一个[实现](https://github.com/rust-lang/rust/blob/f811f14006fa46030f1af714f7d640580d3ad822/compiler/rustc_data_structures/src/tiny_list.rs)。注意，链表仍然经常对性能不利。

这同样适用于图形结构，尽管您可能需要依赖来处理图形数据结构。 [`petgraph`](https://crates.io/crates/petgraph) 是目前最流行的，既提供了数据结构又提供了一些图形算法。如果您不想招致另一个依赖，使用一个`Vec`或 arena 来分配节点并使用索引而不是引用总是一个可行的方法。

## 自引用类型

当面对自引用类型的概念时，有理由问:“这是谁的？”同样，这是所有权故事中的一个小问题，借贷检查者通常对此并不满意。

当您有一个所有权关系，并且希望在一个结构中存储拥有和被拥有的对象时，您会遇到这个问题。天真地尝试这一点，你将很难让生命周期发挥作用。

我们只能猜测，许多 Rustaceans 人转向了不安全的代码，这是微妙的，真的很容易出错。当然，使用普通指针代替引用将消除您的生存期担忧，因为指针没有生存期。然而，这承担了手动管理生存期的责任。

幸运的是，有一些板条箱采用了解决方案并提供了一个安全的接口，例如[`ouroboros`](https://docs.rs/ouroboros/0.9.2/ouroboros/)`[self_cell](https://docs.rs/self_cell/0.8.0/self_cell/)`和`[one_self_cell](https://docs.rs/once_self_cell/0.6.3/once_self_cell/)`板条箱。

## 在`async fn`中借用不属于`static`的东西

经典的 Rust 非常依赖于借来的东西。这比按值传递要便宜，并且可以在代码中与多方共享。然而，一旦你去异步，并期望一切工作一样，你在一个糟糕的时间。

你看，每一次借用都有一个相关的生命周期，如果你试图借用比它还长的东西，我们著名而又令人畏惧的`borrowck`会告诉你。另一方面，使用异步代码，您正在生成状态机，按照设计，这些状态机需要包含它们在执行期间使用的所有数据，并且按照异步运行时的要求生存和死亡。因此，当数据包含借用时，借用的值必须一直存在到程序结束(这意味着`static`生存期)。

当 async Rust 被引入时，我们中的许多人试图在这里加上一个`async`,在那里加上一个`.await`,结果却发现借入检查器一个都没有。所以我们要么使用`.clone`要么使用`Arc`在运行时处理其内容的生命周期。有些事情我们想在任务之间分享，我们只需放入`static` [`OnceCell`](https://docs.rs/once_cell) 。

类似地，异步代码意味着能够跨越多个线程，所以包含在异步任务中的数据必须是`Send`的，因为运行时可能会随意将我们的任务推到另一个线程。同样，Rustaceans 要么将他们的数据更改为`Send`，要么用`Mutex`包装它(他们现在必须确保不要将它放在`await`上，以免导致死锁，这是一个容易犯的错误。幸运的是，有一个 [clippy lint](https://rust-lang.github.io/rust-clippy/master/index.html#await_holding_lock) 反对。

## 全局可变状态

来自 C 和/或 C++的人——或者更少情况下，来自动态语言的人——有时习惯于在他们的代码中创建和修改全局状态。例如，一个 Redditor 咆哮道“它是完全安全的，但是 Rust 不允许你这么做。”

下面是一个稍微简化的例子:

```
#include 
int i = 1;

int main() {
    std::cout << i;
    i = 2;
    std::cout << i;
}

```

在 Rust 中，这将大致转化为:

```
static I: u32 = 1;

fn main() {
    print!("{}", I);
    I = 2; // <- Error: Cannot mutate global state
    print!("{}", I);
}

```

许多 Rustaceans 人会告诉你，你只是不需要那个国家是全球性的。当然，在这么简单的例子中，这是真的。但是对于很多用例，你确实需要全局可变状态——例如，在一些嵌入式应用中。

当然有一种方法可以做到，使用`unsafe`。但是在你使用它之前，根据你的用例，你可能只想使用一个`Mutex`来确保万无一失。或者，如果初始化只需要一次变异，一个`OnceCell`或`lazy_static`就能巧妙地解决问题。或者，如果您真的只需要整数，那么`std::sync::Atomic*`类型已经涵盖了。

也就是说，特别是在嵌入式领域，每个字节都很重要，资源经常被映射到内存中，拥有一个可变的`static`通常是首选的解决方案。因此，如果您真的必须这样做，并且能够确保没有竞争条件会导致未定义的行为(一般来说，这将涉及一个锁或确保代码是单线程的)，它将看起来像这样:

```
static mut DATA_RACE_COUNTER: u32 = 1;

fn main() {
    print!("{}", DATA_RACE_COUNTER);
    // I solemny swear that I'm up to no good, and also single threaded.
    unsafe {
        DATA_RACE_COUNTER = 2;
    }
    print!("{}", DATA_RACE_COUNTER);
}

```

同样，除非真的需要，否则不应该这样做。如果你需要问这是不是一个好主意，答案是否定的。

## 只是初始化一个数组

新手可能会尝试声明如下数组:

```
let array: [usize; 512];

for i in 0..512 {
    array[i] = i;
}

```

这将失败，因为数组从未初始化。然后我们试着给它赋值，但是在没有告诉编译器的情况下，它甚至不会在栈上为我们保留一个写的位置。铁锈就是那样挑剔；它将数组与其内容区分开来。此外，在我们可以读取它们之前，它要求两者都被初始化。

通过初始化`let array = [0usize; 512];`，我们以双重初始化为代价解决了这个问题，这可能会也可能不会被优化掉——或者，取决于类型，甚至可能是不可能的。参见“[不安全生锈:如何以及何时(不)使用它](https://blog.logrocket.com/unsafe-rust-how-and-when-not-to-use-it/)”以获得解决方案。

从 Rust 1.63.0 开始，我们也有了可以用来完全安全地初始化数组的`[std::array::from_fn](https://doc.rust-lang.org/std/array/fn.from_fn.html)`函数:

```
// the closure `|i| i` will be called for each index from 0..512
// and can do arbitrary computation
let array: [usize; 512] = std::array::from_fn(|i| i);

```

## 结论

这就结束了我们在 Rust 中无法(轻松)完成的任务。

在本文中，我们讨论了 Rust 中的继承、双向链表、自引用类型、在`async fn`中借用非`static`的东西、全局可变状态以及初始化数组。

亲爱的读者，虽然肯定还有其他东西会因生锈而变得坚硬，但把它们都列出来会占用你我太多时间。

然而，尽管有这么多麻烦，Rust 也给了我们一些强大的抽象概念，可以用来帮助我们解决遇到的问题。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。