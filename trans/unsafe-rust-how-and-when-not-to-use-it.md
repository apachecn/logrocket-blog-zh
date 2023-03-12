# 不安全的铁锈:如何以及何时(不)使用它

> 原文：<https://blog.logrocket.com/unsafe-rust-how-and-when-not-to-use-it/>

在本指南中，我们将解释你需要知道的关于`unsafe` Rust 的一切。我们将重点关注以下内容:

关于不安全生锈的神话

## 在我们解释如何以及何时在 Rust 中使用(或不使用)`unsafe`之前，我想先讲几个关于 Rust 中不安全代码的长期神话。

误解 1:所有 Rust 代码都是不安全的

### 不会。区别是微妙的，但是安全的 Rust 代码不能违反安全保证，只要编译器和它构建的不安全代码没有任何允许这种情况发生的错误。因此，与其他低级语言不同，在其他低级语言中，代码的安全性取决于每一行代码和编译器实现，您可以大大减少需要审计错误的攻击面。

[RustBelt](https://plv.mpi-sws.org/rustbelt/) 项目从数学上证明，如果你有一部分安全代码和一部分保护其不变量的不安全代码，只要不安全代码不允许，安全代码就不能破坏保证。

顺便说一句，不变量是一个不被一个类型的所有方法或一个模块的所有函数改变的条件。

从统计学上来说，在 Crates.io 上发现的 Rust 代码中，不到 1%是不安全的代码——这可能不到现有代码的 50 %,但仍然应该是一个足够有代表性的样本——而且许多 Crates 没有单独的`unsafe`行代码。

误解 2:它依赖于标准库代码，其中包含许多不安全的

### 是的，标准库比一般的机箱有更多不安全的代码，这是意料之中的，因为它提供的许多抽象不能在安全的 Rust 中有效地实现，或者根本不能实现。此外，我们可以放心，标准库已经接受了更多的审查，因此比一般的机箱更值得信赖。

这并不是说它是完美的——毕竟，在过去已经发现了缺陷。尽管如此，在验证和模糊化标准库的大部分方面仍有很大的努力，这使得所有 Rust 代码更加安全。

神话 3:一旦我写了`unsafe`，我就可以关闭借书检查器，召唤恶魔，打开痛苦维度的传送门等等。

### 不， [Rustonomicon](https://doc.rust-lang.org/nomicon/what-unsafe-does.html) 一丝不苟地列出了`unsafe`授予你的额外权力，作为交换，你必须保证维护代码中这一部分的安全不变量。例如，您可以:

取消引用原始指针

*   调用`unsafe`函数(包括 C 函数、编译器内部函数和原始分配器)
*   实现`unsafe`特性
*   变异静力学
*   访问`union`的字段
*   没有黑暗的仪式，也没有关闭任何借用检查。然而，即使那些听起来可能是良性的权力也可能会产生你应该注意的后果:

原始指针不能保证指向它们声称指向的数据的有效实例，因此取消引用它们可能会将您引入空白区域(很可能是一个分段错误)，或者更糟糕的是，引入类型混淆，在这种情况下，您会错误地解释数据，破坏程序的状态，并可能产生可利用的影响

*   调用`unsafe`函数会带来用不符合其安全要求的参数调用它们的风险，并可能产生可利用的效果
*   为不支持不变量的类型实现`unsafe`特征也可能导致调用者无意中导致他们的安全需求失败，并可能产生可利用的影响
*   在另一个线程中观察静态数据时对其进行变异可能会导致数据竞争和读取撕裂，这意味着读取的值不再需要与旧值或新值相关。根据读取的类型及其使用方式，这也可能导致具有潜在可利用效果的未定义行为
*   访问`union`的字段可能会让您将数据解释为不代表有效实例的类型，或者观察到未初始化的数据(如果类型长度不同，一个字段可能包含另一个字段的填充)，这两种情况都会导致未定义的行为和可能被利用的效果
*   因此，虽然不安全的代码并不像有些人说的那样可怕，但需要小心安全地处理它。然后，您可以在不安全的代码上编写安全的基础。

误解 4:只要没有人可以意外地用参数调用我的安全代码，使下面的不安全代码失败，我就没事

### 不。一旦你在不安全的代码上呈现了一个安全的接口，你的代码要么不管怎样都支持安全不变量，要么你的代码是不可靠的。

有些人对不健康的感觉非常强烈，没有理由被激怒。它仍然是一个 bug，你应该公开而冷静地解决它。如果这个 bug 可以通过更仔细的设计来解决，那就去做吧。与此同时，你可以公开声明你的代码是不健全的，用户需要格外小心，不要违反安全不变量。如果你想出了一个合理的设计，撤回所有发布的不合理版本，你和[报告漏洞](https://rustsec.org/)。

误解 5:我已经试着运行它，它运行良好

### 未定义行为的问题不在于它会直接失败。事实上，它可能永远不会失败。它也可能一直工作到您将代码投入生产，此时它可能会灾难性地失败。或者，它可能会工作，直到一个黑客尝试它，并制作正确的输入来破坏你不健全的代码。现在你所有的用户都有一个加密勒索木马在他们的电脑上。

即使运行多次，也不能保证下次一定会成功。正如哥伦比亚号航天飞机的灾难所表明的，仅仅因为它成功了 135 次并不意味着它在第 136 次尝试时不会失败。

但是如果它只发生在泄漏上呢？

### 泄漏无法可靠地避免，而且泄漏本身不会对内存安全造成任何危险——尽管如果您耗尽可用内存，操作系统可能会停止您的程序或干脆崩溃，但这在最坏的情况下会导致拒绝服务。因此，它被认为超出了内存安全保证的范围，`mem::forget`成为了一个安全函数。因此，如果您的代码依赖于某些值不泄漏来保证安全，那么在某个时候，泄漏可能会发生，安全保证的损失将由您承担。

请注意，这个神话是如此重要，直到 Rust 1.0 之前才最终允许安全代码中的泄漏。这种不安全来源的解决方案通常是泄漏放大——在尝试不安全操作之前，泄漏所有可能由泄漏导致的可观察到的损坏状态，并在之后将一切恢复原状。这样一个漏洞会变大，有时候大很多，但至少不能颠覆内存安全。

好吧，我明白了。一旦一行不安全的代码出现在我的机箱中，所有其他代码都必须小心不要违反安全规则

### 通常公认的边界是模块，板条箱是其中的一种特殊形式。所以通常的做法是在一个模块中创建`unsafe`代码。这通常不意味着从外部使用，但它有时可以是公共的，因为如果人们想承担随之而来的责任以换取性能(或其他东西)，他们可能会在代码中使用不安全的方法。

下一步是编写另一个模块，它将使用前面提到的不安全代码提供一个安全的接口。这个模块应该是允许所有其他用例的最小抽象——核心功能，如果你愿意的话。省去所有可以通过构建这个安全代码来实现的东西。这是需要仔细审核的部分。

最后，在你的安全 API 之上编写你希望人们使用的实际接口。由于您处于安全状态，这段代码不需要太多关注。如果你在核心接口实现上做得很好，编译器会支持它的所有保证。

既然我们已经驱散了与 Rust 代码相关的神话，在进入实际代码之前，我们只有一件事要讨论。

什么时候不写`unsafe`代码

## 通常，不安全的代码实际上是在追求性能的过程中使用的。但是正如我在“[如何编写垃圾代码](https://blog.logrocket.com/how-to-write-crap-rust-code)”中所写的，你应该总是有一个运行缓慢且安全的版本，哪怕只是作为测试的快照和基准。

不安全的代码有时会更快，但这并不意味着它一定会更快。相应地测量，如果结果是一样快或更快，则保留安全版本。

例如，在尝试加速一个[基准游戏](https://benchmarksgame-team.pages.debian.net/benchmarksgame/)条目作为练习时，我想通过使用数组而不是`Vec`来删除分配，这需要一些不安全的代码来处理未初始化的数据。然而，这比基于`Vec`的版本要慢，所以我放弃了这个努力。Cliff L. Biffle 在“[以危险的方式学会生锈](http://cliffle.com/p/dangerust/)”中写了类似的经历

有了`unsafe`代码，不仅编译器的保证更少，编译器的工作保证也更少，所以一些优化实际上可能被禁用，以避免破坏代码。因此，在进行切换之前，请务必进行测量，并准备好安全版本。

好了，我们开始吧！

处理未初始化的内存

## Rust 打 1.0.0 的时候有一个不安全的函数获取未初始化的内存:`std::mem::uninitialized()`(也有`std::mem::zeroed()`，但两者唯一的区别是后者用`0`字节填充返回的内存区域)。

这被广泛认为是一个坏主意，现在这个函数已经被弃用，人们被建议使用`std::mem::MaybeUninit`类型。`uninitialized`陷入困境的原因是，无论是在恐慌还是其他提前回报的情况下，其价值都可能隐含在`drop` ped 中。例如:

如果`this_function_may_panic`函数真的死机了，在我们开始`forget`调用它之前,`x`就被丢弃了。然而，删除未初始化的值是未定义的行为，因为删除通常是隐式的，所以很难避免这种情况。因此，`MaybeUninit`被设想用来处理潜在的未初始化数据。该类型永远不会自动删除(像`std::mem::ManuallyDrop`)，被编译器认为可能是未初始化的，并且有许多函数可以很好地处理未初始化的数据。

```
let x = std::mem::uninitialized();
this_function_may_panic();
mem::forget(x);

```

让我们回顾一下。我们不可能`std::ptr::read`从未初始化的内存中。我们甚至可能不引用它(为它创建一个`&`或`&mut`)，因为引用契约要求被引用的值是被引用类型的有效实例，而未初始化的数据通常不是(这里的一个例外是对一个或多个`MaybeUninit<_>`的引用，因为这显然不需要初始化)。

因此，我们也可能不删除它，因为这会创建一个可变引用(记住，`fn drop(&mut self)`)。我们可以将其转化为其他类型，这些类型的契约允许未初始化的数据(这仍然是创建未初始化值数组的规范方式)，或者将`std::ptr::write`转化为从其`as_mut_ptr()`方法中获得的指针，或者为其分配另一个`MaybeUninit`——仅此而已。注意，即使没有初始化，我们也可以给一个`MaybeUninit`赋值，因为这个类型不会被丢弃。

举个例子，假设我们想用一个函数创建一个数组。要么我们数组的组件类型不是`Copy`或者没有`const`初始化器，要么 LLVM 由于某种原因无法优化双存储。所以我们去`unsafe`:

如果任何一个`calculate_elem(_)`调用失败，整个`MaybeUninit`数组都将被丢弃。因为`MaybeUninit`没有`drop`它的内容，所以到目前为止计算的所有元素都会被泄露。

```
use std::mem::{MaybeUninit, transmute};

unsafe {
    // first part: initialize the array. This is one of the rare cases where
    // directly calling `assume_init` is OK, because an array of
    // `MaybeUninit` may contain uninitialized data.
    let mut array: [MaybeUninit<MyType>; 256] =
        MaybeUninit::uninit().assume_init();

    // second part: initialize the data. This is safe because we assign
    // to a `MaybeUninit`, which is exempt from `drop`.
    for (i, elem) in array.iter_mut().enumerate() {
        *elem = MaybeUninit::new(calculate_elem(i));
    }

    // third part: transmute to the initialized array. This works because
    // `MaybeUninit<T>` is guaranteed to have the same Layout as `T`.
    transmute::<_, [MyType; 256]>(array)
}

```

为了避免这种情况，我们需要一些额外的移动部件:

如果你认为初始化一个数组需要很多机器，那你就对了。还有，在这一点上，一定要衡量对性能的影响；我不能说一个`Vec<MyType>`将如何比较。

```
use std::mem::{forget, MaybeUninit, transmute};

// first extra part: We need a "guard" that drops all *initialized* elements
// on drop
struct Guard<'a> {
    // a mutable ref to access the array from
    array: &'a mut [MaybeUninit<MyType>; 256],
    // the index until which all elements are initialized
    index: usize,
}

impl Drop for Guard<'_> {
    // drop all elements that are initialized
    fn drop(&mut self) {
        for i in 0..self.index {
            unsafe {
                std::ptr::drop_in_place(self.array[i].as_mut_ptr());
            }
        }
    }
}

unsafe {
    let mut array: [MaybeUninit<MyType>; 256] =
        MaybeUninit::uninit().assume_init();

    // second extra part: here we initialize the guard. From here on, it
    // borrows our array mutably. All access will be done through the guard
    // (because the borrow checker won't let us access `array` directly
    // while it's mutably borrowed).
    let mut guard = Guard { array: &mut array, index: 0 };
    for i in 0..256 {
        guard.array[guard.index] = MaybeUninit::new(calculate_elem(i));
        // update the index so `drop` will include the newly created element.
        guard.index += 1;
    }
    // third extra part: forget the guard to avoid dropping the initialized
    // elements and also end the borrow.
    forget(guard);

    transmute::<_, [MyType; 256]>(array)
}

```

无论如何，这显示了处理未初始化数据的规范方法:识别一个不变量(“所有元素直到`index`都被初始化”)，维护它(“在写入一个元素后递增索引”)，并且您可以获得好处—在这种情况下，在出现混乱的情况下没有泄漏。

这种方法也适用于数据结构，尽管不变量的用法通常不同。例如，在将内存复制到更大后备存储器的调整大小操作之前的`Vec`中，长度被设置为零，并在操作完成后恢复(泄漏放大)。这确保了`drop`操作不会观察到未初始化或已经释放的内存。

根据您的数据结构，不变量可能相当神秘。例如，您可以使用一个位集来编码初始化的元素，这样就需要比普通数组多八分之一的内存，但允许设置或取消设置任意元素。在这种情况下，不变量将是“一个位被置位，该值被初始化。”Rust's `HashMap` s 基本上就是这么做的。

改变不变的事物

## Rust 关于别名的规则——意味着在每个给定的时间点有多少东西可以从一个位置读取或向一个位置写入——非常严格。但有时我们确实需要通融一下。

为了实现这一点，Rust 为(`unsafe`)类型赋予了内部可变性——您可以使用`get(&self)`方法从一个不可变的借用中获得一个可变指针(当然不是引用)。还有一个`get_mut(&mut self)`方法返回对内容的可变借用。

这意味着编译器将假设任何`UnsafeCell`中的内容都是别名。标准库在顶层提供了许多安全的抽象，特别是`Cell`、`RefCell`、`RwLock`、`Mutex`和各种`Atomic` *类型。例如，`AtomicBool`定义如下(为简洁起见，移除了注释):

当然，实现必须确保避免数据竞争，这可以通过 LLVM 内部函数使用实际的原子操作来实现。我还没有检查即将到来的 [Cranelift](https://github.com/bytecodealliance/cranelift) 后端做什么，但它似乎也有某种实现。

```
pub struct AtomicBool {
    v: UnsafeCell<u8>,
}

```

同样，在您直接使用`UnsafeCell`之前，请检查是否有任何安全包装器能为您工作，并确定使用`unsafe`是否能为您带来足够的性能提升(或其他好处),是否值得。

内在动机

## Rust 的标准库在`std::arch`模块中有一组针对每种 CPU 类型的内部函数。都定义为`unsafe`，主要是因为它们可能不会在你的 CPU 上实现。幸运的是，有一种规范的方法可以确保在编译时或运行时有一个匹配的 CPU。

让我们假设您已经以“正常”的方式构建了您的算法，查看了程序集，并认为自动矢量器本身做得不够好。是时候大展拳脚了。你将基本上用 Rust 编写汇编(因为`arch`内部函数被映射到单个机器指令)。

如上所述，您需要确保用户拥有正确的平台。以下代码显示了在编译时和运行时检查支持的方法:

这个例子只有针对`x86`和`x86_64`的专用代码，以及各种运行时检测到的 CPU 特性。如果你想让你的程序在其他平台上使用 SIMD 内部函数(比如 ARM-NEON)，你需要添加另一个`#[cfg]`d 模块声明。不用说，您最终会得到大量代码。

```
// compile time check
#[cfg(any(target_arch = "x86", target_arch = "x86_64"))]
mod simd {
    fn calculate() -> .. {
        // this is a std library macro that allows us to safely access CPU
        // features by doing runtime detection
        if is_x86_feature_detected!("avx2") {
            // code using avx2 intrinsics here
        } else if is_x86_feature_detected!("sse2")
            // code using sse2 intrinsics here
        } else {
            // code using no special features, using e.g. avx2 intrinsics
            // would be UB!
        }
    }
}

#[cfg(not(any(target_arch = "x86", target_arch = "x86_64")))]
mod simd {
    // fallback code here
}

```

除了可用性之外，一些指令还对对齐*有要求。*简单来说，对齐告诉我们一个地址的最后多少位必须为零。例如，一个 32 位的值可能有一个对齐方式`4`，这意味着其地址的最后两位应该为零。请参考库文档以获得细节，并参考最后一章以获得正确的帮助。

内嵌汇编

## 假设你正在写一个内核，需要一些非常奇怪的堆栈指针，或者其他一些绝对需要汇编的东西。Rust 有两个外语接口:一个到 C，一个到汇编。不幸的是，这既不稳定也不安全，所以您需要一个夜间编译器、`#[feature(asm)]`属性和一个`unsafe`块。显然，Rust 无法检查你在汇编代码中做了什么。

使用内联汇编的细节超出了本文的范围。请看[锈书章节](https://doc.rust-lang.org/1.8.0/book/inline-assembly.html)或 [RFC 正文](https://rust-lang.github.io/rfcs/2873-inline-asm.html)。就本文而言，内联汇编只是外来函数接口(FFI)的一个时髦变体。

连接外部功能

## 你有这么大的 C 代码库，想把它转移到 Rust，这是一项艰巨的任务。做到这一点的一个好方法是首先使用外来函数接口移动代码库的较小部分，然后一个模块一个模块地移动，直到整个东西都生锈了，你就可以扔掉 C 了(顺便说一下，这就是 [librsvg](https://people.gnome.org/~federico/blog/a-rust-api-for-librsvg.html) 一直在做的)。或者你想用 C++代码里的 Rust。

无论是哪种情况，你都必须在安全、被溺爱的铁锈世界和冷酷、漠不关心的世界之间架起一座桥梁。既然外面的世界很危险，你当然需要`unsafe`去做。

首先，确保你有正确的接口，以免你面临许多不愉快的调试时间。 [bindgen](https://github.com/rust-lang/rust-bindgen) (用于从 Rust 访问 C)和 [cbindgen](https://github.com/eqrion/cbindgen/) (用于从 C 访问 Rust)实用程序在这方面非常棒。

如果您从 C(或通过 C 接口从 C++访问 Rust ),请注意对象的生存期，并将 Rust 对象的生存期保存在 Rust 代码中——也就是说，让 Rust `drop`它们，反之亦然。众所周知，Rust 对谁拥有什么以及需要访问多长时间非常挑剔，所以记录下您的需求。

另一方面，如果你用 Rust 包装 C(或 C++，通过`extern C`)，你会发现 C 库通常也需要考虑数据的生命周期。一旦你有了简单的绑定，试着构造你的类型来考虑生命周期关系。非官方的模式书有一章是关于这个的。

如果你用 C++接口，你可能想使用 [cxx](https://docs.rs/cxx) 机箱。但是，请注意，与通常的绑定生成器不同，cxx 不会将您的函数标记为`unsafe`！作者在这里的论点是，部分在 Rust 中、部分在 C++中构建的绑定机制是安全的，并且由您来审核 C++端。您可能仍然希望将生成的接口包装在一个友好的接口中，希望它不会以不安全的方式被误用。

正如我们所见，编写不安全 Rust 比编写安全 Rust 需要更多的关注，因为你不能再依赖编译器来保护你。所以如果你走这条路，你最好尽你所能得到帮助:

米里

### [Miri](https://github.com/rust-lang/miri) 是 Rust MIR——这是 Rust 在移交给 LLVM 或 crane lift——解释器之前用来优化其程序的中间表示。可以用`rustup component add miri`通过 rustup 安装。通过编写`cargo miri`而不是简单的`cargo`来运行它——例如，`cargo miri test`将在解释器中运行您的测试。

Miri 有很多技巧来检测未定义的行为，比如访问未初始化的数据，如果有什么问题，它会告诉你。但是，它只会检测实际执行的代码路径上的未定义行为，所以它不能给你完全的保证。

碎布和锈迹

### Rust 的官方 lint 套件有许多 lint，可能对`unsafe`代码也有帮助。至少，`missing_safety_docs` lint 将帮助您记录所有不安全方法的需求。另外，Rust 编译器默认情况下不会激活所有的 lints 打电话给`rustc -W help`会给你一个最新的列表。

Prusti

### Prusti 仍在开发中(目前在更新到最新的稳定 Rust 时遇到了一些问题，所以最新的稳定版本针对一些 2018 Rust 编译器)，但它是一个非常有前途的工具，允许你从数学上验证关于代码的一些后置条件在给定的前提条件下成立。

基本上，你构造了一个关于你的代码的某些不变量成立的证明，这对于必须支持不安全代码的不变量的安全抽象是理想的。更多信息参见[用户指南](https://viperproject.github.io/prusti-dev/user-guide/)。

模糊器

### Rust Fuzz Book 列出了一些可以和 Rust 一起使用的 fuzzers。目前有使用[cargo-fuzzer](https://github.com/rust-fuzz/cargo-fuzz)/libfuzzer 和[American Fuzzy Lop](http://lcamtuf.coredump.cx/afl/)/[AFL . RS](https://github.com/rust-fuzz/afl.rs)的详细章节。两者都将为您的代码创建大量的测试输入，并运行它来寻找一些触发崩溃的组合。

为了检测未初始化内存的使用， [libdiffuzz](https://github.com/Shnatsel/libdiffuzz) 是一个插入式内存分配器，它将使用不同的值初始化每个内存分配。通过运行您的代码两次并比较结果，您可以确定未初始化内存的任何部分是否出现在结果中。更好的是，现在至少每夜都支持内存杀毒软件([跟踪问题](https://github.com/rust-lang/rust/issues/39699)列出了各种杀毒软件及其跨平台支持),并将检测任何未初始化的读取，即使它不会改变结果。

虽然从统计上看，模糊化器比普通的属性测试更有可能找到代码路径，但不能保证它们在任何时间后都能找到特定的代码路径。我个人在标记化函数中有一个 bug，结果是由我在互联网上的一个随机文档中发现的 unicode 宽空格触发的，在用数十亿个测试案例运行 cargo fuzz 一周之后，没有发现任何东西。尽管如此，Rust fuzz [trophy case](https://github.com/rust-fuzz/trophy-case) 展示了通过 fuzzing 发现的大量 bug。如果找到了，请补充。

rutenspitz 是一个过程宏，非常适合对有状态代码进行模型测试——例如数据结构。模型测试意味着您有一个“模型”，即一个简单但缓慢的版本，它模拟您想要确保的行为，并且您使用它来测试您的不安全实现。然后，它将生成一系列操作来测试等式关系是否成立。如果你遵循了我上面的建议，你应该已经有了一个安全的实现来测试。

[log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

## 调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。

Modernize how you debug your Rust apps — [start monitoring for free](https://lp.logrocket.com/blg/rust-signup).