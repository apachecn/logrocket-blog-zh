# 对比 Rust 供应链安全工具- LogRocket 博客

> 原文：<https://blog.logrocket.com/comparing-rust-supply-chain-safety-tools/>

Rust 使得向你的项目添加依赖项变得很容易:编辑你的`Cargo.toml`(或者使用`cargo add`从命令行为你改变它)，然后你就可以使用这个箱子了。但是你知道你给你的项目增加了什么吗？你确定你能信任那些代码行吗？

当然，有些开发者不会费心去了解。毕竟，如果一个板条箱被广泛使用，它做一些可疑的事情的可能性有多大？事实证明，这方面有很多先例。

在 JavaScript 世界中，流行的 npm 库可能会从最初的维护者那里被劫持，并可能出于犯罪目的而被颠覆。如今，研究人员数出了数以千计的受损或恶意软件包。

即使没有恶意，库也可能包含弱点——最近 Log4Shell 的崩溃让许多 Java 系统维护者彻夜难眠。

但是作为开发者我们能做什么呢？把我们的集体头埋在沙子里会导致灾难。对于除了最安全敏感的项目之外的所有项目来说，用你自己的眼睛检查你的整个依赖关系是太昂贵了。

在本帖中，我们将会看到一些工具，这些工具能够以最小的努力为我们提供少量的安全保障，将我们的注意力集中在可能需要的地方，甚至扩大我们的审查工作，以改善我们社区的供应链安全。

*向前跳转:*

## 使用货物-检查防锈安全性

[cargo-audit](https://docs.rs/cargo-audit/latest/cargo_audit/) 由 Rust Secure Code 工作组构建，是其 [RustSec 咨询数据库](https://rustsec.org/)的标准最终用户界面。可以用`cargo install cargo-audit`安装。

一旦安装完毕，您可以通过调用`cargo audit`从您的货物项目的基础启动它来运行它。它会查看您的`Cargo.toml`并扫描 RustSec 数据库中您的所有依赖项，打印它找到的所有咨询。

如果你关心你和你的用户的安全(你应该！)，这是您可以花费的绝对最小的努力量，以确保至少没有已知的问题潜伏在您的依赖关系树中。

例如，我在诱变剂箱上运行`cargo audit`:

```
$ cargo audit
    Fetching advisory database from `https://github.com/RustSec/advisory-db.git`
      Loaded 394 security advisories (from /home/andre/.cargo/advisory-db)
    Updating crates.io index
    Scanning Cargo.lock for vulnerabilities (60 crate dependencies)
Crate:         failure
Version:       0.1.8
Warning:       unmaintained
Title:         failure is officially deprecated/unmaintained
Date:          2020-05-02
ID:            RUSTSEC-2020-0036
URL:           https://rustsec.org/advisories/RUSTSEC-2020-0036
Dependency tree:
failure 0.1.8
├── mutagen-core 0.2.0
│   ├── mutagen-transform 0.2.0
│   │   └── mutagen 0.2.0
│   │       ├── mutagen-selftest 0.2.0
│   │       ├── feature-gated 0.2.0
│   │       ├── example-with-integration-tests-v2 0.2.0
│   │       ├── example-with-integration-tests-v1 0.2.0
│   │       └── example-simple 0.2.0
│   ├── mutagen-selftest 0.2.0
│   ├── mutagen 0.2.0
│   └── cargo-mutagen 0.2.1
└── cargo-mutagen 0.2.1

```

我真的应该转到无论如何或类似的东西。有人能推一个公关吗？☺️

## 使用 cargo-deny 检查来源和依赖项许可证

由 Embark 构建的, [cargo-deny](https://github.com/EmbarkStudios/cargo-deny) 比 cargo-audit 更进一步，检查依赖项的来源和许可证。如果你打算发布你的板条箱，并打算让人们使用它，就使用它——没有什么比不兼容的许可证更能让一个乡下人的日子变得糟糕的了！

另一方面，cargo-deny 需要一些关于您的板条箱的信息，因此，要使用它，您需要配置它。幸运的是，您不需要手动编写任何东西——一个简单的`cargo deny init`就能帮您做到。

运行之后，您会发现一个名为`deny.toml`的新文件，您可以编辑它来配置和定制您的检查。你可以在 [GitHub repo](https://github.com/EmbarkStudios/cargo-deny/blob/main/deny.template.toml) 中[了解更多关于此事的](https://github.com/EmbarkStudios/cargo-deny/blob/main/deny.template.toml)。

(有趣的是，cargo-deny 显示我在某个时候丢失了所有`Cargo.toml`文件的所有许可信息。)

正确配置后，您将获得一行令人放心的结果(取决于您的配置，因为输出中可能会遗漏一些检查):

```
advisories ok, bans ok, licenses ok, sources ok 

```

然而，这假设您的依赖项是最新的，否则，您可能会得到一堆冗长的警告。例如，在我的`compact_arena`机箱中，我使用了一个旧版本的 [compiletest](https://github.com/Manishearth/compiletest-rs) ，它在寻找维护者时有一些依赖项，所以我收到了一些警告，比如:

```
warning[A003]: term is looking for a new maintainer
   ┌─ /home/andre/projects/compact_arena/Cargo.lock:33:1
   │
33 │ term 0.6.1 registry+https://github.com/rust-lang/crates.io-index
   │ ---------------------------------------------------------------- unmaintained advisory detected
   │
   = ID: RUSTSEC-2018-0015
   = Advisory: https://rustsec.org/advisories/RUSTSEC-2018-0015
   = The author of the `term` crate does not have time to maintain it and is looking
     for a new maintainer.

     Some maintained alternatives you can switch to instead, depending
     on your needs:

     - \[`crossterm`\](https://github.com/crossterm-rs/crossterm)
     - \[`termcolor`\](https://crates.io/crates/termcolor)
     - \[`yansi`\](https://crates.io/crates/yansi)
   = Announcement: https://github.com/Stebalien/term/issues/93
   = Solution: No safe upgrade is available!
   = term v0.6.1
     └── tester v0.7.0
         └── compiletest_rs v0.5.0
             └── (dev) compact_arena v0.4.1

```

得到几页这样的警告可能会令人沮丧，但是有一些工具可以处理这种情况。

## 使用`cargo outdated`和`cargo duplicates`

更新依赖关系的规范 cargo 命令是`cargo update`。不过，这些自动应用的更新不会改变主要版本，因为它们可能不兼容。

因此，您可以调用`cargo outdated`(在用`cargo install cargo-outdated`安装之后)来找出哪些依赖项可以进一步更新。

例如，在最新版本的`flame`上，我得到:

```
$ cargo outdated
Name                       Project  Compat   Latest   Kind    Platform
----                       -------  ------   ------   ----    --------
proc-macro2->unicode-xid   0.2.1    0.2.2    0.2.2    Normal  ---
quote->proc-macro2         1.0.24   1.0.36   1.0.36   Normal  ---
serde                      1.0.118  1.0.136  1.0.136  Normal  ---
serde_derive               1.0.118  1.0.136  1.0.136  Normal  ---
serde_derive->proc-macro2  1.0.24   1.0.36   1.0.36   Normal  ---
serde_derive->quote        1.0.7    1.0.15   1.0.15   Normal  ---
serde_derive->syn          1.0.54   1.0.86   1.0.86   Normal  ---
serde_json                 1.0.60   1.0.79   1.0.79   Normal  ---
serde_json->itoa           0.4.6    1.0.1    1.0.1    Normal  ---
serde_json->ryu            1.0.5    1.0.9    1.0.9    Normal  ---
serde_json->serde          1.0.118  1.0.136  1.0.136  Normal  ---
syn->proc-macro2           1.0.24   1.0.36   1.0.36   Normal  ---
syn->quote                 1.0.7    1.0.15   1.0.15   Normal  ---
syn->unicode-xid           0.2.1    0.2.2    0.2.2    Normal  ---
thread-id->libc            0.2.81   0.2.119  0.2.119  Normal  cfg(unix)

```

最新版本可能不兼容，因此我们可能需要修复更新中出现的问题。没有唯一正确的方法来决定何时更新，因此您应该始终考虑更新的成本与保持当前版本的成本的比较。

但是，这不会解决我们的重复依赖问题。这个问题的原因是，如果可能的话，cargo 经常会尝试使用最新的兼容版本。

然而，随着依赖关系树的增长，某个依赖关系出现多次的概率也在增长——尽管版本不兼容。Cargo 的解决方案是简单地构建两个版本，并使它们具有不同的名称空间。

如果您使用来自`foo-0.3.2`的类型`Foo`，并且有一个依赖项使用来自`foo-0.1.5`的`Foo`，那么这些`Foo`将不兼容。更糟糕的是，编译您的项目将会引入两个版本并分别编译它们。

通过`cargo install cargo-duplicates`安装`cargo duplicates`将允许你调用`cargo duplicates`,至少找到依赖树中要升级或降级的部分。比如在当前版本的[字节数](https://docs.rs/bytecount/latest/bytecount/)上，我发现了两个版本的`cfg-if`:

```
Package  Versions
-------  --------
cfg-if   1.0.0  0.1.10

cfg-if 1.0.0:
- Because of bytecount 0.6.2 => criterion 0.3.3 => rayon 1.5.0 => rayon-core 1.9.0 => crossbeam-channel 0.5.0 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => rayon 1.5.0 => crossbeam-deque 0.8.0 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => rayon 1.5.0 => rayon-core 1.9.0 => crossbeam-deque 0.8.0 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => rayon 1.5.0 => crossbeam-deque 0.8.0 => crossbeam-epoch 0.9.0 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => rayon 1.5.0 => rayon-core 1.9.0 => crossbeam-deque 0.8.0 => crossbeam-epoch 0.9.0 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => rayon 1.5.0 => rayon-core 1.9.0 => crossbeam-channel 0.5.0 => crossbeam-utils 0.8.0 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => rayon 1.5.0 => crossbeam-deque 0.8.0 => crossbeam-utils 0.8.0 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => rayon 1.5.0 => rayon-core 1.9.0 => crossbeam-deque 0.8.0 => crossbeam-utils 0.8.0 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => rayon 1.5.0 => crossbeam-deque 0.8.0 => crossbeam-epoch 0.9.0 => crossbeam-utils 0.8.0 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => rayon 1.5.0 => rayon-core 1.9.0 => crossbeam-deque 0.8.0 => crossbeam-epoch 0.9.0 => crossbeam-utils 0.8.0 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => rayon 1.5.0 => rayon-core 1.9.0 => crossbeam-utils 0.8.0 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => rand 0.8.4 => rand_core 0.6.3 => getrandom 0.2.3 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => quickcheck 1.0.3 => rand 0.8.4 => rand_core 0.6.3 => getrandom 0.2.3 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => rand 0.8.4 => rand_chacha 0.3.1 => rand_core 0.6.3 => getrandom 0.2.3 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => quickcheck 1.0.3 => rand 0.8.4 => rand_chacha 0.3.1 => rand_core 0.6.3 => getrandom 0.2.3 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => rand 0.8.4 => rand_hc 0.3.1 => rand_core 0.6.3 => getrandom 0.2.3 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => quickcheck 1.0.3 => rand 0.8.4 => rand_hc 0.3.1 => rand_core 0.6.3 => getrandom 0.2.3 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => plotters 0.2.15 => js-sys 0.3.46 => wasm-bindgen 0.2.69 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => plotters 0.2.15 => web-sys 0.3.46 => js-sys 0.3.46 => wasm-bindgen 0.2.69 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => plotters 0.2.15 => wasm-bindgen 0.2.69 => cfg-if 1.0.0
- Because of bytecount 0.6.2 => criterion 0.3.3 => plotters 0.2.15 => web-sys 0.3.46 => wasm-bindgen 0.2.69 => cfg-if 1.0.0
cfg-if 0.1.10:
- Because of bytecount 0.6.2 => quickcheck 1.0.3 => env_logger 0.8.4 => log 0.4.11 => cfg-if 0.1.10
- Because of bytecount 0.6.2 => quickcheck 1.0.3 => log 0.4.11 => cfg-if 0.1.10
- Because of bytecount 0.6.2 => criterion 0.3.3 => plotters 0.2.15 => js-sys 0.3.46 => wasm-bindgen 0.2.69 => wasm-bindgen-macro 0.2.69 => wasm-bindgen-macro-support 0.2.69 => wasm-bindgen-backend 0.2.69 => log 0.4.11 => cfg-if 0.1.10
- Because of bytecount 0.6.2 => criterion 0.3.3 => plotters 0.2.15 => web-sys 0.3.46 => js-sys 0.3.46 => wasm-bindgen 0.2.69 => wasm-bindgen-macro 0.2.69 => wasm-bindgen-macro-support 0.2.69 => wasm-bindgen-backend 0.2.69 => log 0.4.11 => cfg-if 0.1.10
- Because of bytecount 0.6.2 => criterion 0.3.3 => plotters 0.2.15 => wasm-bindgen 0.2.69 => wasm-bindgen-macro 0.2.69 => wasm-bindgen-macro-support 0.2.69 => wasm-bindgen-backend 0.2.69 => log 0.4.11 => cfg-if 0.1.10
- Because of bytecount 0.6.2 => criterion 0.3.3 => plotters 0.2.15 => web-sys 0.3.46 => wasm-bindgen 0.2.69 => wasm-bindgen-macro 0.2.69 => wasm-bindgen-macro-support 0.2.69 => wasm-bindgen-backend 0.2.69 => log 0.4.11 => cfg-if 0.1.10
- Because of bytecount 0.6.2 => packed_simd_2 0.3.4 => cfg-if 0.1.10

```

应该有人来解决这个问题，让我们的构建稍微快一点🙂。

## Rust 中使用 cargo-geiger 和 cargo-osha

Rust 以安全为荣。它对内存安全的保证是无与伦比的。但是，它只能通过拒绝有效的程序来做到这一点。

为了能够作为一种系统语言运行，它必须包含一个陷阱门，以避免在程序员可以确保代码安全但无法说服编译器的情况下受到这些限制。这就是`unsafe`的用武之地(我之前已经用[写过关于这个话题的](https://blog.logrocket.com/unsafe-rust-how-and-when-not-to-use-it/))。

当一个 Rust 程序不符合我们所要求的内存安全时，必须被审计的代码行位于包含不安全代码的模块中。然而，并不是所有这些代码都需要是你自己的。您可能已经在整个依赖关系树中引入了不安全的代码，而自己却不知道。

[cargo-geiger](https://github.com/rust-secure-code/cargo-geiger)——与`cargo install cargo-geiger`一起安装——会给你一个你使用的所有包含不安全代码的软件包的概览。例如，在`flame`中，我得到如下结果:

```
Metric output format: x/y
    x = unsafe code used by the build
    y = total unsafe code found in the crate

Symbols:
    :) = No `unsafe` usage found, declares #![forbid(unsafe_code)]
    ?  = No `unsafe` usage found, missing #![forbid(unsafe_code)]
    !  = `unsafe` usage found

Functions  Expressions  Impls  Traits  Methods  Dependency

0/0        0/0          0/0    0/0     0/0      ?  flame 0.2.1-pre
0/0        7/7          1/1    0/0     0/0      !  ├── lazy_static 1.4.0
0/0        0/0          0/0    0/0     0/0      :) ├── serde 1.0.118
0/0        0/0          0/0    0/0     0/0      ?  │   └── serde_derive 1.0.118
0/0        0/0          0/0    0/0     0/0      ?  │       ├── proc-macro2 1.0.24
0/0        0/0          0/0    0/0     0/0      :) │       │   └── unicode-xid 0.2.1
0/0        0/0          0/0    0/0     0/0      :) │       ├── quote 1.0.7
0/0        0/0          0/0    0/0     0/0      ?  │       │   └── proc-macro2 1.0.24
0/0        45/45        3/3    0/0     2/2      !  │       └── syn 1.0.54
0/0        0/0          0/0    0/0     0/0      ?  │           ├── proc-macro2 1.0.24
0/0        0/0          0/0    0/0     0/0      :) │           ├── quote 1.0.7
0/0        0/0          0/0    0/0     0/0      :) │           └── unicode-xid 0.2.1
0/0        0/0          0/0    0/0     0/0      ?  ├── serde_derive 1.0.118
0/0        3/5          0/0    0/0     0/0      !  ├── serde_json 1.0.60
0/0        1/1          0/0    0/0     0/0      !  │   ├── itoa 0.4.6
8/12       674/921      0/0    0/0     2/2      !  │   ├── ryu 1.0.5
0/0        0/0          0/0    0/0     0/0      :) │   └── serde 1.0.118
0/0        4/4          0/0    0/0     0/0      !  └── thread-id 4.0.0
0/5        12/259       0/0    0/0     2/22     !      └── libc 0.2.81

8/17       746/1242     4/4    0/0     6/26

```

## 在铁锈中使用 crev

这一切都很好，但在某些时候，有人将不得不卷起袖子，执行适当的代码审查。与我们信任的其他人分享审查代码的负担，并通过加密签名来保证安全，这样我们就可以相信审查者就是他们所声称的那个人，这不是很好吗？

只需看看 [cargo-crev](https://github.com/crev-dev/cargo-crev) ，它允许我们创建数字指纹并直接开始输入评论。下面是设置它的方法:

```
$ cargo install cargo-crev
$ # Note that hereby we trust the author of crev:
$ cargo crev trust --level high https://github.com/dpc/crev-proofs
$ cargo crev repo fetch all
$ cargo crev id set-url <your proof repo>
$ # as this exhorts you to set your password
$ cargo crev id passwd

```

关于`<your proof repo>`:你需要叉[https://github.com/crev-dev/crev-proofs](https://github.com/crev-dev/crev-proofs)来设置这个。如果您有 GitHub 帐户，您可以继续 fork，否则，您可以克隆 repo 并将其推送到您使用的任何 git 服务中。

现在我们准备复习:

```
$ cargo crev open <crate>
$ cargo crev review <crate>

```

我不知道为什么这不是一个命令，但是第一个命令将做好准备，第二个命令将打开您的`$EDITOR`,其中包含一些字段。我刚刚提交了对我的`compact_arena`板条箱的审查。

```
kind: package review
version: -1
date: "2022-04-03T15:53:31.338910703+02:00"
from:
  id-type: crev
  id: FJuAGAaomkJL8JJojZdS_jVpqHIW__7QSXcWSnjAB6U
package:
  source: "https://crates.io"f
  name: compact_arena
  version: 0.4.1
  revision: b66f8a2f000cd91ce18f67f4c9f9974fe6e19ded
  digest: 6_pE4YUU_SbeJmBUty7B8buurO68FNBNvR3j6iAn9Kk
review:
  thoroughness: medium
  understanding: high
  rating: positive
alternatives:
  - source: "https://crates.io"
    name: generational-arena
comment: |-
  The idea behind this crate is that one can use small index types and use the
  lifetime tokens to ensure the validity of the indices, so that the actual
  indexing can be unchecked.

  Earlier versions had problems getting the compiler to check the
  lifetimes for all types, including those that did not implement Drop. For
  those, we implemented a Guard type that ensures the arena lifetime is
  actually observed.

  Basically the lifetime tokens bind the indices returned by *Arena::add to the
  arena. Neither through threads nor recursion can one mix up arenas, and as
  Arenas don't allow deleting items, the index lifetime can only live as long
  as the arena it refers to.

  I have done my due diligence and filed for a CVE as well as yanking all
  previous unsound versions.

```

希望这能澄清事实——这个板条箱可以安全使用！😇

如果你想选择相信我的评论，你可以在你的终端上输入`cargo crev trust --level high [https://github.com/llogiq/crev-proofs](https://github.com/llogiq/crev-proofs)`来完成。也许将来我会抽时间查看更多的板条箱。

## 就这些了，伙计们！

无论您是想了解您的依赖树，寻找不安全的代码，减少您的构建时间(和隐含的攻击面)，还是帮助改进我们所有人的代码审查状态，您现在都有工具来完成这些工作。前进，保持安全！👋

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。