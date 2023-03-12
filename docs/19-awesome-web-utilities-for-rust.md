# Rust - LogRocket 博客的 19 个令人敬畏的 web 实用程序

> 原文：<https://blog.logrocket.com/19-awesome-web-utilities-for-rust/>

Rust 是一种主要关注性能和安全的编程语言。它在语法上与 C++相似，非常流行。事实上， [Stack Overflow 的年度开发者调查](https://insights.stackoverflow.com/survey/2020?utm_source=twitter&utm_medium=social&utm_campaign=dev-survey-2020#technology-most-loved-dreaded-and-wanted-languages-loved)已经连续五年将 Rust 评为开发者最喜爱的语言。

我最喜欢 Rust 的是它的静态类型，它允许使用`null`的概念。与其他静态类型编程语言不同，Rust 使用可选类型对`null`概念进行编码，这意味着编译器要求您处理非 case 错误，以防止`TypeError: Cannot read property 'foo' of null`运行时错误。您得到的不是上面的运行时错误，而是一个很容易解决的编译时错误。

使用 Rust，您还可以将数据存储在堆栈或堆上。这有助于您确定编译时不再需要哪些数据。

有各种各样的开源 web 实用程序可以用来增强 Rust 的功能。例如，尽管 Rust 的标准库不包含任何正则表达式解析器/匹配器，但`regex`实用程序提供了一个正则表达式解析器。使用`regex`，您可以搜索、拆分或替换文本。

看看下面的表达式，它可以让您快速搜索美国号码。

```
let re = Regex::new("[0-9]{3}-[0-9]{3}-[0-9]{4}").unwrap();
let mat = re.find("phone: 111-222-3333").unwrap();
assert_eq!((mat.start(), mat.end()), (7, 19));

```

这只是一个例子，说明 web 实用程序如何帮助您将 Rust 应用程序提升到一个新的水平。在本指南中，我们将向您介绍 19 个最流行和最有用的 Rust web 实用程序:

1.  [T2`cookie`](#cookie)
2.  [T2`clap`](#clap)
3.  [T2`heim`](#heim)
4.  [T2`tempfile`](#tempfile)
5.  [T2`urlencoded`](#urlencoded)
6.  [T2`bodyparser`](#bodyparser)
7.  [T2`queryst`](#queryst)
8.  [T2`scgi`](#scgi)
9.  [T2`sass-rs`](#sass-rs)
10.  [T2`ratelimit_meter`](#ratelimit_meter)
11.  [T2`regex`](#regex)
12.  [T2`chrono`](#chrono)
13.  [T2`time`](#time)
14.  [T2`backtrace`](#backtrace)
15.  [T2`docopt`](#docopt)
16.  [T2`structopt`](#structopt)
17.  [T2`url`](#url)
18.  [T2`robotparser`](#robotparser)
19.  [T2`multipart`](#multipart)

## 1.`cookie`

该实用程序增强了 HTTP cookie 解析和 cookie jar 管理。这个库是在 MIT 和 Apache 2.0 下授权的。据 [crates.io](https://crates.io/crates/cookie) 称，它有 62 个版本，稳定的最新版本是 0.14.2。在撰写本文时，`cookie`已经被下载了大约 580 万次。

### **特性**

默认情况下，`cookie`的以下功能被禁用。

*   `percent-encode`允许对 cookies 中的名称和值进行百分比编码。`cookie:: parse_encoded()`方法允许在解析时对 cookie 的名称和值进行百分比解码。`cookie:: encoded`方法返回一个包装 cookie 的包装器，并用它的`Display`实现对 cookie 进行百分比编码
*   `signed`允许使用`CookieJar::signed()`方法对 cookies 进行签名
*   `private` —当您使用`CookieJar::private()`方法启用了 私有 功能时，cookies 会在添加和检索时被加密、解密和验证
*   按键扩展自动启用`signed`和`private`功能，以及`Key::derive_from()`方法。使用`Key::derive_from()`方法，您可以得到一个适合在`single`或`private`罐子中使用的密钥结构，其长度比完整的密钥要短
*   `secure`是一个元特性，可以在使能`signed`、`private`和`key-expansion`之间切换。通过将以下代码添加到您的`Cargo.toml`文件中来启用它:

```
[dependencies.cookie]
features = ["secure", "percent-encode"]

```

## 2.`clap`

在用 Rust 编写控制台应用程序时， [`clap`](https://docs.rs/clap/2.33.3/clap/) 箱是解析和验证命令行参数和子命令的最佳工具。这个库获得了 MIT 的许可，发布了 211 个版本，其中最稳定的是 2 . 33 . 3(3 . 0 . 0 版本仍处于测试阶段)。在撰写本文时，它已经拥有超过[1500 万的下载量](https://crates.io/crates/clap)。

或者，您可以使用 YAML 文件来构建您的 CLI，以保持 Rust 源代码的整洁。

### 特征

默认情况下，会启用以下内置功能。

*   当用户输入错别字时，建议可以帮助他们，例如，`Did you mean '--myoption'?`
*   彩色—错误消息彩色时更明显。此功能打开彩色错误消息，并且仅在非 Windows 操作系统上起作用
*   换行帮助—使用此功能，终端中的帮助会换行，而不是 120 个字符

要禁用上述特性，请将以下代码添加到您的`cargo.toml`文件中。

```
[dependencies.clap]
version = "~2.27.0"
default-features = false

```

您也可以通过将下面的代码粘贴到您的`cargo.toml`文件中，只启用您想要使用的功能。

```
[dependencies.clap]
version = "~2.27.0"
default-features = false
# Cherry-pick the features you'd like to use
features = [ "suggestions", "color" ]

```

以下是选择加入的功能；它们不是内置的。

*   YAML 使您能够从一个`.yaml`文件构建您的 CLI
*   `unstable`使您能够使用不稳定的 clap 功能

## 3.`heim`

[`heim`](https://docs.rs/heim/0.0.11/heim/) 是一个跨平台的库，可用于系统信息抓取。它使用异步优先，并且是跨平台和模块化的。

图书馆的 GitHub 页面包括了一个有用的`heim`和其他箱子的比较。

`heim`[是否得到了麻省理工学院和阿帕奇 2.0](https://crates.io/crates/heim) 的许可。15 个版本中最稳定的是 0.0.10，0.10 版本处于 beta 阶段。

## 4.`tempfile`

[`tempfile`](https://docs.rs/tempfile/3.1.0/tempfile/) 机箱包括`tempfile()`和`tempdir()`功能，可以创建临时文件和目录。比方说，你想创建一个文件，但不关心它的名字，因为它是一个临时文件，你可以删除它。`tempfile`库将使这个过程变得更容易，因为一旦你完成，它将删除文件。

`tempfile()`函数依靠操作系统在你关闭最后一个句柄后立即删除临时文件，而`tempdir()`函数依靠 Rust 析构函数进行清理。如果析构函数不运行，`tempdir()`函数可能会失败，而`tempfile()`函数几乎不会失败，因为它依赖于计算机的操作系统。

这个库是在 MIT 和 Apache 2.0 下授权的，已经发布了 [35 个版本](https://crates.io/crates/tempfile)，其最新的稳定版本是 3.1.0。

## 5.`urlencoded`

是一个用于 Iron web 框架的中间件，帮助你从 GET 和 POST 请求中解码 URL 数据。它将一个 URL 查询字符串解析成一个`HashMaps`,后者将键的字符串表示映射到字符串值的 vec 上。值存储在 vec 中，以确保如果一个键出现多次，不会丢失任何信息。查询字符串`d=b&d=c`导致从`d`到`b`，`c`的映射。它解析 web 表单数据的 POST 请求主体(MIME 类型:application/x-www-form-urlencoded)。

这个库是在 MIT 下许可的，发布了九个版本(最稳定的版本:0.6.0)，并且需要以下依赖项。

*   `bodyparser` ^0.8
*   铁> =0.5，< 0.7
*   `plugin` ^0.2.6
*   `url` ^1.6

## 6.`bodyparser`

Iron 的核心中间件的一部分， [`bodyparser`](https://docs.rs/bodyparser/0.8.0/bodyparser/) 顾名思义，是一个面向 Iron 的解析中间件。它包含以下功能:

*   `raw` —在 Raw 中，`bodyparser`对字符串执行正文解析
*   `json`—`bodyparser`框架可以将主体解析成 JSON
*   使用 [Serde](https://blog.logrocket.com/json-and-rust-why-serde_json-is-the-top-choice/) 将主体解析成一个结构

这个库得到了麻省理工学院的许可，有 15 个版本。其当前最稳定的版本是`0.8.0`，它需要以下依赖项:

*   铁> =0.5，< 0.7
*   执着的^0.4
*   `plugin` ^0.2
*   塞尔德·^1.0
*   `serde_json` ^1.0
*   `serde_derive` ^1.0

## 7.`queryst`

[`queryst`](https://docs.rs/queryst/1.0.1/queryst/) 是一个 Rust 字符串解析库，使您能够将查询字符串解析为它们对应的 JSON 值。

通过将以下内容添加到您的`cargo.toml`文件来启用`queryst`库。

```
[dependencies]
queryst = "1"

```

使用`queryst`，字符串`foo[bar]=baz`可以被解析为:

```
{
  "foo": {
    "bar": "baz"
   }
}

```

URL 编码的字符串也有效，可以解析为:

```
parse('a%5Bb%5D=c');
// { "a": { "b": "c" } }

```

这个库得到了麻省理工学院的许可，已经发布了 [21 个版本](https://crates.io/crates/queryst)。它的最新版本`2.0.2`需要以下依赖项。

*   塞尔德·^1
*   `serde_json` ^1
*   `url` ^1
*   `lazy_static` ^1 (optional)
*   `regex` ^0.2 (optional)

## 8.`scgi`

简单通用网关接口(SCGI)是应用程序与 HTTP 服务器接口的协议。 [`scgi`](https://artemgr.github.io/rust-scgi/scgi/index.html) 是一种用于防锈的 SCGI 连接器。

该库得到了麻省理工学院和阿帕奇 2.0(T2)的许可，已经发布了 16 个版本。最新版本是 0.3.4，它需要`bufstream` ^0.1.1 作为依赖项。

## 9.`sass-rs`

Sass 库的高级绑定是对`libsass`的包装。

[`sass-rs`](https://crates.io/crates/sass-rs) 在 MIT 下获得许可，已经发布了 22 个。稳定的、最新的库是`0.2.2`，它需要以下依赖项。

## 10.`ratelimit_meter`

[`ratelimit_meter`](https://docs.rs/ratelimit_meter/5.0.0/ratelimit_meter/) 是一个开源库，它实现了漏桶和漏桶的一个变体，即用于速率限制和调度的通用信元速率算法(GCRA)。与令牌桶算法不同，漏桶算法假设所有的工作单元具有相同的“权重”因此，它允许一些优化，这导致更简洁和快速的代码。

这个库得到了麻省理工学院的许可，并且已经发布了 1 [7 个版本](https://crates.io/crates/ratelimit_meter/1.0.0)。`ratelimit_meter`的当前版本是 5.0.0，需要以下依赖项。

*   `nonzero_ext` ^0.1.5
*   `evmap` ^6.0.0 optional
*   `parking_lot` ^0.9.0 optional
*   `spin` ^0.5.0 optional
*   `criterion` ^0.2.11
*   `libc` ^0.2.41

## 11.`regex`

[`regex`](https://docs.rs/regex/1.4.2/regex/) 是一个帮助你解析、编译、执行正则表达式的 Rust 库。使用`regex`，您可以匹配 YYYY-MM-DD 格式的日期，以打印年、月、时和日，甚至可以使用迭代器迭代文本。

要使用`regex`，请将以下内容添加到您的`cargo.toml`文件中。

```
[dependencies]
regex = "1"

```

如果您使用的是 Rust 2015，您需要将它添加到您的板条箱工具中:

```
extern crate regex;

```

这个库是在 MIT 和 Apache 2.0 下授权的。它有 [120 个版本](https://crates.io/crates/regex)，最新的是 1.3.9。此版本需要下列依赖项。

*   `regex-syntax` ^0.6.18
*   `aho-corasick` ^0.7.6 optional
*   `memchr` ^2.2.1 optional
*   `thread_local` ^1 optional
*   `lazy_static` ^1
*   `quickcheck` ^0.8
*   `rand` ^0.6.5

## 12.`chrono`

[`chrono`](https://blog.logrocket.com/timezone-handling-in-rust-with-chrono-tz/) 是日期和时间的锈库。通过`chrono`，您可以使用`time`框中的`time::Duration`类型来表示时间跨度的大小。您还可以使用`DateTime`类型来表示时区中的日期和时间。`DateTime`类型必须从`TimeZone`对象中构造。这将定义本地日期如何与 UTC 日期相互转换。

这个库是在 MIT 或 Apache-2.0 下授权的，有 [66 个发布版本](https://crates.io/crates/chrono)。最新最稳定的版本是 0.4.18。

### 默认功能

*   `alloc`启用依赖于分配的特性(主要是字符串格式)
*   `std`是`alloc`的超集，增加了与标准库类型和特征的互操作
*   `clock`允许读取系统时间，不管`std::time::SystemTime`是否存在(取决于是否有`libc`

### 可选功能

*   `wasmbind`与`wasm-bind` gen 及其`js-sys`项目无缝集成
*   `serde`允许[通过 Serde 进行 Rust 序列化和反序列化](https://blog.logrocket.com/rust-serialization-whats-ready-for-production-today/)
*   `unstable-locales`启用本地化并添加带有`_localized`后缀的各种方法

## 13.`time`

[`time`](https://docs.rs/time/0.2.22/time/) 库是一个开源的日期和时间库，它与标准库完全互操作，并且大部分与`#![no_std]`兼容。这个库有 [70 个版本](https://crates.io/crates/time)，其最新、最稳定的版本是 0.2.22。

### 默认情况下启用的功能

*   `std`启用依赖于标准库的各种其他功能
*   `deprecated`允许支持从`time` 0.1 开始的某些功能

### 默认情况下禁用的功能

*   启用对所有类型的 Serde 支持
*   `rand`支持所有类型的 rand
*   `panicking-API`允许您在需要时使用`unwrap()`方法

## 14.`backtrace`

构建 [`backtrace`](https://doc.rust-lang.org/std/backtrace/index.html) 库是为了通过允许以编程方式获取回溯来支持`RUST_BACKTRACE=1`标准库。

要使用`backtrace`，请将以下内容添加到您的`cargo.toml`文件中

```
[dependencies]
backtrace = "0.3"

```

本库有 [64 版本发布](https://crates.io/crates/backtrace)。最新的稳定版本是 0.3.50，它需要以下依赖项。

*   `cfg`——如果^0.1.10
*   `libc` ^0.2.45
*   `rustc-demangle` ^0.1.4
*   `addr2line` ^0.13.0 optional
*   `backtrace-sys` ^0.1.35 optional
*   `compiler_builtins` ^0.1.2 optional
*   `cpp_demangle` ^0.3.0 optional
*   `miniz_oxide` ^0.4.0 optional
*   `object` ^0.20.0 optional
*   `rustc-serialize` ^0.3 optional
*   `rustc-std-workspace-core` ^1.0.0 optional
*   `serde` ^1.0 optional
*   `winapi` ^0.3.3 optional
*   `libloading` ^0.6

## 15.`docopt`

[`docopt`](https://docs.rs/docopt/1.1.0/docopt/) 是一个命令行参数解析器。与其他解析器不同，这个解析器可以从用法字符串中派生出来。

要使用`docopt`，请将以下内容添加到您的`cargo.toml`文件中。

```
[dependencies]
docopt = "1"
serde = { version = "1", features = ["derive"] }

```

`docopt`有 [86 个版本](https://crates.io/crates/docopt)，最新的是 1.1.0。此版本需要以下依赖项:

*   `lazy_static` ^1.3
*   `regex` ^1.1.5
*   `serde` ^1.0
*   `strsim` ^0.9

## 16.`structopt`

[`structopt`](https://docs.rs/structopt/0.3.20/structopt/) 将`clap`库与自定义驱动结合起来，通过定义一个结构来解析命令行参数。

要使用这个库，请将`structopt`添加到您的`cargo.toml`文件中:

```
[dependencies]
structopt = "0.3"

```

然后，在您的 rust 文件中:

```
use std::path::PathBuf;
use structopt::StructOpt;

/// A basic example
#[derive(StructOpt, Debug)]
#[structopt(name = "basic")]
struct Opt {
    /**this will return true if used in the command line. Note doc comment will
     be used for the help message of the flag. The name of the
     document by default, will be based on the name of the field.**/
    /// Activate debug mode
    #[structopt(short, long)]
    debug: bool,

    /// Verbose mode (-v, -vv, -vvv, etc.)
    #[structopt(short, long, parse(from_occurrences))]
    verbose: u8,

    /// Set speed
    #[structopt(short, long, default_value = "42")]
    speed: f64,

    /// Output file
    #[structopt(short, long, parse(from_os_str))]
    output: PathBuf,

    // the long option will be translated to kebab case by default,
    // i.e. `--nb-cars`.
    /// Number of cars
    #[structopt(short = "c", long)]
    nb_cars: Option<i32>,

    /// admin_level to consider
    #[structopt(short, long)]
    level: Vec<String>,

    /// Files to process
    #[structopt(name = "FILE", parse(from_os_str))]
    files: Vec<PathBuf>,
}

fn main() {
    let opt = Opt::from_args();
    println!("{:#?}", opt);
}

```

`structopt`已经发布了 [52 个版本](https://crates.io/crates/structopt/0.1.3)，最新版本是 0.3.18。

## 17.`url`

`[url](https://docs.rs/url/2.1.1/url/)`是 Rust 编程语言的 URL 标准的实现。URL 标准定义了 URL、域、IP 地址、application/x-www-form-urlencoded 格式以及它们的 API。

这个图书馆有 [73 个版本出版](https://crates.io/crates/url)。它最稳定的版本是`2.1.1`。

## **18。`robotparser`**

[`robotparser`](https://docs.rs/robotparser/0.8.2/robotparser/) 是一个用于 Rust 的`robots.txt`解析器。`robost.txt`文件告诉搜索引擎爬虫他们能或不能抓取哪些页面或文件。这是为了避免你的网站被过多的请求。

这个库得到了麻省理工学院的许可，已经出版了 [18 个版本](https://crates.io/crates/robotparser)。最新的稳定版本是`0.10.2`，需要以下依赖项。

*   `url` ^1
*   `reqwest` ^0.9 optional

## 19.`multipart`

[`multipart`](https://docs.rs/multipart/0.17.0/multipart/) 是 HTTP 文件上传的客户端和服务器端概念(内容类型为 multipart/form-data 的 POST 请求)。这个库支持各种同步 API HTTP crates，包括`hyper`和`tiny_http`。

`multipart`在麻省理工学院和阿帕奇 2.0 下获得许可，并且已经发布了 [18 个版本](https://crates.io/keywords/multipart)。最新的稳定版本是`0.17.0`，需要以下依赖项。

*   `log` ^0.4
*   `mime` ^0.3.14
*   `mime_guess` ^2.0.1
*   `rand` ^0.6
*   `tempfile` ^3
*   `buf_redux` ^0.8 optional
*   `clippy` > =0.0, < 0.1 optional
*   `httparse` ^1.2 optional
*   `hyper` > =0.9, < 0.11 optional
*   `iron` > =0.4, < 0.7 optional
*   `lazy_static` ^1.2.0 optional
*   `nickel` > =0.10.1 optional
*   `quick-error` ^1.2 optional
*   `rocket` ^0.4 optional
*   `safemem` ^0.3 optional
*   `tiny_http` ^0.6 optional
*   `twoway` ^0.1 optional
*   `env_logger` ^0.5

## 结论

Rust 的 web 实用程序太多了！希望本指南能为您提供选择合适的解决方案组合所需的背景，让您的 Rust 应用更上一层楼。

如果你想解析 Rust 中的命令行参数，`clap`、`docopt`和`structopt`都是不错的选择。当涉及到实现日期和时间相关的特性时，使用`chrono`和`time`是不会错的。对于创建临时文件，可以使用`tempfile`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

Rust 生态系统为解析 cookes ( `cookie`)、string(`queryst`)和 regular expression(`regex`)提供了优秀的工具。为了允许 Rust 在应用程序中读取传入请求的主体，您可能希望尝试使用`bodyparser`来解析请求的主体部分。

一如既往，最佳解决方案将取决于与您的 Rust 项目相关的独特目标、需求和挑战。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。