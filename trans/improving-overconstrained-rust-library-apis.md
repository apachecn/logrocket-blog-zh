# 改进过度约束的 Rust 库 API

> 原文：<https://blog.logrocket.com/improving-overconstrained-rust-library-apis/>

在我之前的一篇文章“如何编写垃圾代码”中，我警告过你不要过度使用泛型。对于二进制或任何代码的初始版本，这仍然是一个好主意。

然而，在设计 Rust library crate APIs 时，您通常可以使用泛型来获得良好的效果:对我们的输入更加宽容可以为调用者提供避免一些分配的机会，或者找到更适合他们的输入数据的不同表示。

在本指南中，我们将演示如何在不损失任何功能的情况下使 Rust library APIs 更加宽松。但是在我们开始之前，让我们检查一下这样做可能带来的负面影响。

首先，泛型函数为类型系统提供了更少的关于什么是什么的信息。如果一个具体的类型现在变成了一个`impl`，编译器将很难推断出每个表达式的类型(并且可能更容易失败)。这可能需要您的用户添加更多的类型注释来编译他们的代码，从而导致更糟糕的人机工程学。

此外，通过指定一个具体的类型，我们正好得到一个编译成结果代码的函数版本。对于泛型，我们要么支付动态调度的运行时成本，要么选择[单态化](https://en.wikipedia.org/wiki/Monomorphization)，冒着二进制代码膨胀为多个版本的风险——在 Rust lingo 中，我们选择`dyn Trait` vs. `impl Trait`。

您选择折衷的哪一点主要取决于用例。请注意，动态分派有一些运行时成本，但是代码膨胀也会增加缓存未命中，从而对性能产生负面影响。一如既往，测量两次，编码一次。

即便如此，对于所有公共方法，还是有一些经验法则可以遵循。

*向前跳转:*

## 一部分特征

如果可以的话，用切片(`&[T]`)代替`&Vec<T>`(那个其实有 [clippy lint](https://rust-lang.github.io/rust-clippy/master/index.html#ptr_arg) )。您的调用者可能会使用一个`[VecDeque](https://doc.rust-lang.org/std/collections/struct.VecDeque.html)`，它有一个`[.make_continuous()](https://doc.rust-lang.org/std/collections/struct.VecDeque.html#method.make_contiguous)`方法返回一个`&mut [T]`，而不是一个`[Vec](https://doc.rust-lang.org/std/vec/struct.Vec.html)`，或者一个数组。

如果你也可以取两个切片，`[VecDeque::as_slices](https://doc.rust-lang.org/std/collections/struct.VecDeque.html#method.as_slices)`可以为你的用户工作而不移动任何值。当然，你仍然需要知道你的用例来决定它是否值得。

如果只解引用 slice 元素，可以使用`&[impl Deref<Target = T>]`。注意，除了`[Deref](https://doc.rust-lang.org/std/ops/trait.Deref.html)`，还有`[AsRef](https://doc.rust-lang.org/std/convert/trait.AsRef.html)`特征，这在路径处理中经常使用，因为`std`方法可能需要一个`AsRef<T>`来进行廉价的引用转换。

例如，如果您采用一组文件路径，`&[impl AsRef<Target = Path>]`将比`&[String]`处理更多的类型:

```
fn run_tests(
    config: &compiletest::Config,
    filters: &[String],
    mut tests: Vec<tester::TestDescAndFn>,
) -> Result<bool, io::Error> { 
    // much code omitted for brevity
    for filter in filters {
        if dir_path.ends_with(&*filter) {
            // etc.
        }
    }
    // ..
}

```

上述内容可以表示为:

```
fn run_tests(
    config: &compiletest::Config,
    filters: &[impl std::convert::AsRef<Path>],
    mut tests: Vec<tester::TestDescAndFn>,
) -> Result<bool, io::Error> { 
// ..

```

现在`filters`可能是`String`、`&str`，甚至是`Cow<'_, OsStr>`的一部分。对于可变类型，有`[AsMut<T>](https://doc.rust-lang.org/std/convert/trait.AsMut.html)`。类似地，如果我们要求任何对`T`的引用在相等、顺序和散列方面与`T`本身相同，我们可以使用`[Borrow<T>](https://doc.rust-lang.org/std/borrow/trait.Borrow.html)` / `[BorrowMut<T>](https://doc.rust-lang.org/std/borrow/trait.BorrowMut.html)`来代替。

这到底是什么意思？这意味着实现`Borrow`的类型必须保证`a.borrow() == b.borrow()`、`a.borrow() < b.borrow()`和`a.borrow().hash()`分别返回与`a == b`、`a < b`和`a.hash()`相同的结果，如果该类型分别实现`[Eq](https://doc.rust-lang.org/std/cmp/trait.Eq.html)`、`[Ord](https://doc.rust-lang.org/std/cmp/trait.Ord.html)`和`[Hash](https://doc.rust-lang.org/std/hash/trait.Hash.html)`。

## 让我们重新迭代

类似地，如果您只迭代一个`str` ing 片的字节，除非您的代码以某种方式要求`str`和`String`保证正确工作的 UTF 8-ness，您可以简单地采用一个`AsRef<[u8]>`参数。

一般来说，如果你只迭代一次，你甚至可以选择一个`[Iterator<Item = T>](https://doc.rust-lang.org/std/iter/trait.Iterator.html)`。这允许你的用户提供他们自己的迭代器，这些迭代器可能使用非连续的内存片，在你的代码中穿插其他操作，甚至动态计算你的输入。这样做，您甚至不需要将 item 类型设为泛型，因为如果需要的话，迭代器通常可以很容易地生成一个`T`。

实际上，如果你的代码只迭代一次，你可以使用一个`impl Iterator<Item = impl Deref<Target = T>>`；如果你不止一次需要这些东西，可以用一两片。如果您的迭代器返回自己拥有的项，比如最近添加的数组`IntoIterator` s，您可以放弃`impl Deref`而使用`impl Iterator<Item = T>`。

不幸的是，`IntoIterator`的`into_iter`将消耗`self`，所以没有一种通用的方法可以让我们多次迭代——除非，也许，采用一个`impl Iterator<_> + Clone`的参数，但是那个`[Clone](https://doc.rust-lang.org/std/clone/trait.Clone.html)`操作可能代价很高，所以我不建议使用它。

## 走进树林

与性能无关，但通常受欢迎的是对`impl Into<_>`参数的隐式转换。这样往往可以做出一个感觉很神奇的 API，但是要注意:`[Into](https://doc.rust-lang.org/std/convert/trait.Into.html)`转换可能会很贵。

尽管如此，还是有一些技巧可以帮助你赢得可用性。例如，用一个`Into<Option<T>>`代替一个`[Option<T>](https://doc.rust-lang.org/std/option/enum.Option.html)`会让你的用户省略`Some`。例如:

```
use std::collections::HashMap;

fn with_optional_args<'a>(
    _foo: u32,
    bar: impl Into<Option<&'a str>>,
    baz: impl Into<Option<HashMap<String, u32>>>
) {
    let _bar = bar.into();
    let _baz = baz.into();
    // etc.
}

// we can call this in various ways:
with_optional_args(1, "this works", None);
with_optional_args(2, None, HashMap::from([("boo".into(), 0)]));
with_optional_args(3, None, None);

```

同样，可能存在以高成本方式实现`Into<Option<T>>`的类型。这是我们可以在漂亮的 API 和显而易见的成本之间做出选择的又一个例子。一般来说，选择后者通常被认为是 Rust 的惯用做法。

## 控制代码膨胀

Rust 使泛型代码单态化。这意味着，对于调用您的函数所使用的每个唯一类型，将会生成并优化使用该特定类型的所有代码版本。

这样做的好处是，它会导致内联和其他优化，从而赋予 Rust 我们都知道并喜爱的出色性能。它还有一个缺点，就是可能会生成大量代码。

作为一个可能的极端例子，考虑以下函数:

```
use std::fmt::Display;

fn frobnicate_array<T: Display, const N: usize>(array: [T; N]) {
    for elem in array {
        // ...2kb of generated machine code
    }
}

```

即使我们只是迭代，这个函数也会为每个条目类型和数组长度进行实例化。不幸的是，没有办法既避免代码膨胀又避免复制/克隆，因为所有这些迭代器都在类型中包含了它们的大小。

如果我们可以处理被引用的项目，我们可以不调整大小，而是迭代切片:

```
use std::fmt::Display;

fn frobnicate_slice<T: Display>(slice: &[T]) {
    for elem in slice {
        // ...2kb of generated machine code
    }
}

```

这将至少为每个项目类型生成一个版本。即便如此，假设我们只使用数组或切片进行迭代。然后我们可以分解出一个依赖于类型的`frobnicate_item`方法。此外，我们可以决定是使用静态调度还是动态调度:

```
use std::fmt::Display;

/// This gets instantiated for each type it's called with
fn frobnicate_with_static_dispatch(_item: impl Display) {
    todo!()
}

/// This gets instantiated once, but adds some overhead for dynamic dispatch
/// also we need to go through a pointer
fn frobnicate_with_dynamic_dispatch(_item: &dyn Display) {
    todo!()
}

```

外层的`frobnicate_array`方法现在只包含一个循环和一个方法调用，没有那么多代码需要实例化。避免了代码膨胀！

一般来说，好好看看你的方法的接口，看看泛型在哪里被使用或者被抛弃，这是一个好主意。在这两种情况下，都有一个自然的边界，在这个边界上，我们可以分解出一个函数来移除泛型。

如果你不想要所有这些类型，并且可以增加一点点编译时间，你可以使用我的 [momo](https://github.com/llogiq/momo) 箱来提取一般特征，比如`AsRef`或`Into`。

## 代码膨胀有什么不好？

在某些背景下，代码膨胀有一个不幸的后果:今天的 CPU 采用了缓存层次结构。虽然这些方法在处理本地数据时速度非常快，但它们会导致使用上非常非线性的影响。如果你的代码占用了更多的缓存，它可能会使其他代码运行得更慢！所以[阿姆达尔定律](https://en.wikipedia.org/wiki/Amdahl%27s_law)不再帮你在处理内存的时候找到优化的地方。

首先，这意味着通过测量微基准来孤立地优化代码的一部分可能会适得其反(因为整个代码实际上可能会变得更慢)。另一方面，当编写库代码时，优化库可能会使用户代码简单化。但是无论是你还是他们都无法从微基准测试中学到这一点。

那么，我们应该如何决定何时使用动态调度，何时生成多个副本呢？这里我没有明确的规则，但是我注意到动态调度在 Rust 中肯定没有被充分利用！首先，它有被认为较慢的污名(这并不完全错误，考虑到 vtable 查找确实增加了一些开销)。第二，通常不清楚如何到达那里[，同时避免分配](https://llogiq.github.io/2020/03/14/ootb.html)。

即便如此，如果测量显示从动态调度到静态调度是有益的，Rust 也使得从动态调度到静态调度变得足够容易，并且由于动态调度可以节省大量编译时间，所以我建议尽可能从动态调度开始，并且只有当测量显示它更快时才进行单态调度。这给了我们快速的周转时间，从而有更多的时间来提高其他地方的性能。最好是有一个实际的应用程序来度量，而不是一个微基准。

这就结束了我关于如何在 Rust 库代码中有效使用泛型的长篇大论。去吧，快乐地生锈吧！

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。