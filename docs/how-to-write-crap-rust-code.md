# 如何写垃圾生锈代码-日志火箭博客

> 原文：<https://blog.logrocket.com/how-to-write-crap-rust-code/>

我们这些乡巴佬喜欢我们的代码是垃圾。也就是说，是正确的、可读的和可执行的。确切的措辞各不相同；有些使用可互换的术语，如并发、清晰、严谨、可重用、高效、并行等。—你明白了。不管你怎么称呼它，这个原则对于构建快速、高效的 Rust 代码是至关重要的，它可以帮助提高你的工作效率。

在本教程中，我们将概述一系列步骤，以帮助您为下一个 Rust 项目获得垃圾代码。

## 为什么要写垃圾代码？

在过去的一年里，Rust 编译器已经大大加快了速度，分析过程也变得更加强大。然而开发人员仍然抱怨编译时间长，这是理所当然的。较长的编译时间会延长周转时间，从而影响生产率。

代码被阅读的次数比被编写的次数多，所以在可读性上投资是值得的。写的时候会稍微努力一点，但是以后会感谢自己的。此外，代码经常需要修改，所以以一种为将来的修改留有余地的方式编码是值得的。

最后，您总是希望您的代码安全可靠。当在 Rust 中工作时，这就有了另一层含义:你想要尽可能少的东西，并且这些东西都不应该是不合理的，否则程序不会比我们使用 C/C++的同类程序更好。

当然，您也希望代码在资源限制内运行得相当快。对许多人来说，将这些需求结合在一起是他们最初使用 Rust 的主要动机。

## 使其工作

当你写代码的时候，注意你的变量的命名并且[记录你为什么做每件事](https://blog.guillaume-gomez.fr/articles/2020-03-12+Guide+on+how+to+write+documentation+for+a+Rust+crate)。在早期阶段注释你的公共接口，最好是通过文档测试，这也是明智的，以确保你的接口实际上是可用的。

在至少有两个用例需要之前，不要把事情通用化。你的编译时间将是你的回报，你会发现它仍然很容易改变。

例如，我最近需要将一片`&[i32]` s 转换为一片`Vec<f64>`。

```
/// I could have done this, but didn't:
fn to_f64s<I: Into<f64>>(is: &[I]) -> Vec<f64> { .. }

// instead I did this:
fn to_f64s(is: &[i32]) -> Vec<f64> { .. }

```

当然，我可能需要在将来扩展它，但是现在，它完全清楚类型是什么，我没有为类型推断招致任何编译时间，我的 IDE 将毫无问题地为我插入正确的类型，并且扩展方法以与其他类型一起工作仍然是简单的，所以我没有丢失任何东西。

同样，避免在这个阶段引入并发性，除非没有它设计就无法工作。在大多数情况下，Rust 使这一点变得足够容易，如果不正确，在知道代码正确之前添加它会使调试变得更加困难。

这同样适用于`unsafe`——避免使用它，除非没有它就不可能实现某些功能。在某种程度上，不安全代码甚至比并发代码更糟糕；它可能在失败之前工作很长时间，或者它可能在大多数机器/操作系统/编译器版本组合上工作。尽管有像[米里](https://github.com/rust-lang/miri/)这样的工具，追踪未定义的行为还是非常困难的。

声明您的数据，以便于使用。好的数据设计会产生简单明了的代码。在这一阶段避免使用花哨的算法，除非你确信它们会将性能提高一个数量级，并且你不能在以后轻易替换它们(在后一种情况下，添加`// TODO`注释)。

这是有益的，因为:

*   简单明了的代码几乎没有给 bug 留下隐藏的空间
*   它可以帮助你建立一个合理的测试基线，前提是你没有做任何异常次优的事情(我曾经找到一个同事写的 *O(n⁴)* 集合联合)
*   这使得测试您的优化版本变得容易，因为您可以将两个版本的输出与您想要的任何输入进行比较

在这个阶段尽量避免不必要的分配，或者至少做一个`// TODO`记录，这样你就不会忘记以后去修复它。跟踪分配比跟踪花费的 CPU 周期更难，所以尽早监控是有意义的。是的，有一些很棒的工具可以帮助您找到内存消耗的地方，但是即使是这些工具也需要一些时间来设置、运行和解释结果。减少拨款通常会带来快速的成功。

```
// this `collect here is unnecessary unless `wolverine` has side effects that
// may not be reordered with following operations, for example thread starts
let intermediate = inputs
    .iter()
    .map(|i| wolverine(i))
    .collect::<Vec<_>>();
return intermediate
    .iter()
    .filter_map(|f| deadpool(f))
    .collect();

/// just reduce it to one run:
return inputs
    .iter()
    .map(|i| wolverine(i))
    .filter_map(|f| deadpool(f))
    .collect();

```

如果用 traits，这个阶段就要用[动态调度](https://llogiq.github.io/2020/03/14/ootb.html)。有一些开销，但不会太多，当分析显示有所不同时，您可以将它更改为具有单态化的静态分派。这将保持代码精简，缩短编译时间，并为最热的代码释放指令缓存。

```
/// Avoid this for now: This function will be monomorphized
fn monomorphic<I: std::io::Read>(input: &mut I) -> Config { .. }

/// Use a `&dyn` or `&mut dyn` reference instead
fn dynamic(input: &mut dyn std::io::Read) -> Config { .. }

```

如果你已经成功编译了，并且有一些周期空闲，运行 [Clippy](https://rust-lang.github.io/rust-clippy/) 并仔细阅读它的输出。它可能会显示一些误报，但大多数 lints 都处于良好状态，这些消息有时会带来不错的改进。

## 做正确的事

现在您已经有了可以工作的代码，是时候对它进行测试了。如果可以，为所有公共方法编写 doctests。你的朋友在这里吗？

由于我们没有添加任何不必要的抽象，这一点被简化了。不要为了让你的代码“可测试”而改变它如果需要的话，您可以拥有只使用`#[cfg(test)]`编译的测试助手方法，这样它们可以在测试之间共享。

更大、更复杂的使用测试可以放在`examples/`目录中。

现在也是写`README.md`的好时机，如果你还没有写的话。

用 [quickcheck](https://docs.rs/quickcheck) 或 [proptest](https://altsysrq.github.io/proptest-book/intro.html) 扩展你的测试工具箱。这些工具使您能够自动生成随机测试用例，并在发现错误时减少测试用例。

对于一个更直接、覆盖最大化的方法， [Rust Fuzz Book](https://rust-fuzz.github.io/book/) 展示了如何使用`[afl](https://github.com/rust-fuzz/afl.rs)`或`[cargo-fuzz](https://github.com/rust-fuzz/cargo-fuzz)`来找到失败的测试输入。这通常可以发现 quickcheck 或 proptest 未能发现的问题，因为它们只生成随机输入，而不考虑所采用的代码路径。

除了测试之外，您还可以经常使用类型系统在编译时捕捉可能的错误类。例如，如果你有一个只应该是`1`、`2`或`3`的`u8`，考虑用一个`enum`来代替。这种策略通常被称为“使非法状态不可表示”，Rust 强大的类型系统非常适合它。

举个极端的例子，我的`[compact-arena](https://docs.rs/compact-arena)` crate 使用类型和生存期来禁止在编译时误用索引。

最后，阅读一下您的代码。你能找到突出的东西吗？代码有什么好的？有哪些可以改进的地方？查看代码时，也要记住[性能陷阱](https://llogiq.github.io/2017/06/01/perf-pitfalls.html)。

我个人更喜欢团队合作。如果你也有这种特质，让它为人所知。给你的项目添加一个`CONTRIBUTING.md`，邀请其他人加入，并且[欢迎那些做](https://manishearth.github.io/blog/2016/01/03/making-your-open-source-project-newcomer-friendly/)的人。保留一份简单的、有指导的问题清单，并跟进它们。你甚至可以将它们发布到 Rust 本周的[邀请参与](https://users.rust-lang.org/t/twir-call-for-participation/4821)列表中。这通常需要一点耐心，但是一旦你吸引了忠诚和有能力的合作维护者来帮助你减少工作量，你就会得到回报。

## 动作快点

既然你的程序是精简的、经过良好测试的、可读的，那就试运行一下吧。如果它的速度足以满足您的需求，那么您就完成了。恭喜你！您可以跳过这一部分的其余部分。否则，请继续阅读。

在你开始优化之前，你的首要任务是了解什么需要优化。众所周知，人类不善于推理一个程序将把时间花在哪里。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

学习并使用系统提供的工具。对于采样剖析器来说， [inferno](https://docs.rs/inferno) 文档提供了非常好的指导来获得代码的火焰图(向 Jon Gjengset 致敬)。如果您的应用程序有任何类型的并发性，您可能也想尝试一下 [coz](https://github.com/plasma-umass/coz) 。

如果你能运行它， [DHAT](https://valgrind.org/docs/manual/dh-manual.html) 可以提供一个内存使用的可靠概览。过度使用内存的好处是，它通常是优化的最佳选择。糟糕的是你不太可能找到它们，因为你已经(希望)在早期摆脱了它们中的大部分。

一旦你理解了你的代码的热点，首先寻找算法上的改进(你的`TODO`可能会派上用场)。如果你以后改变整个事情，陷入低级的细节会适得其反。但是，请注意，您的程序很少表现出渐近复杂性(通俗地说，运行在非常大的输入上)，所以在选择算法时要注意这一点。

如果你已经用完了你的算法选项，还需要更快的速度，看看数据布局。你的`HashMap`是不是大部分时间都少于 50 个条目？使用一个`Vec<(key, value)>`来代替，特别是当你可以用`sort_by_key`和`binary_search_by_key`来查找的时候。如果你的`Vec`大多有一两个元素，也许可以试试`[SmallVec](https://docs.rs/smallvec)`(或者`[tinyvec](https://docs.rs/tinyvec)`，如果它给你同样的性能的话)。

在这个阶段，甚至数据的顺序也可能有所不同，所以如果您在您的概要文件中看到对结构字段的某些读取，请尝试在结构定义前添加`#[repr(C)]`并对字段进行重新排序，看看它是否能提高您的性能。

如果你特别聪明，你会注意到我们还没有谈到并发性。这是故意的。让程序并行运行通常是不必要的，而且结果可能并不令人满意，尤其是如果您引入了并发性，而又不清楚它在哪里有效。

[阿姆达尔定律](https://en.wikipedia.org/wiki/Amdahl%27s_law)指出，你的一部分代码的任何加速( *s **)*** 将占用总运行时间的一个百分比( *p)* ，这将通过*1–p*和 *p/s* 之和的倒数来使整个程序受益。因此，如果你将一个占运行时间 30%的程序加速两倍，你将把整个程序加速 15%。

因此，现在您有了可以在多个内核上运行的热代码。通常这将是循环，所以使用 [rayon](https://docs.rs/rayon/1.3.0/rayon/) 创建最外层的可并行循环。它并不是在所有情况下都是最佳的，但是如果并行计算真的成功了，它为一个非常简单的尝试提供了可接受的开销。

提醒一句:对您测试的工作负载保持警惕。创建一个能够正确度量对性能的影响的基准是一门微妙的艺术。这超出了本文的范围，但是我错误地将一个实际上是由混杂因素造成的影响归因于足够多的次数，这使得我对基准设计非常小心。这并不能保证我的下一个基准是正确的。在任何情况下，[标准](https://bheisler.github.io/criterion.rs/book/)基准测试可以帮助您遵循最佳实践。

## 知道什么时候停下来

为性能优化代码通常是一个有趣的游戏，但很容易迷失其中。请记住，每一次优化都伴随着复杂性的增加、可读性和可维护性的损失，以及漏洞攻击面的扩大，更不用说对您的时间造成的压力了。所以，设定一个明确的绩效目标，一旦达到就停下来。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。