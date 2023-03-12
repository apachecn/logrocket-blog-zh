# Rust 的编码和解码包

> 原文：<https://blog.logrocket.com/encoding-and-decoding-packages-for-rust/>

编码是将数据从一种形式转换成另一种形式的过程。解码的意思完全一样。尽管编码通常被定义为一个过程，但它也指一种特定形式的数据(字符编码或媒体编码)。

字符编码/解码在编程中尤其重要，因为计算机只能识别二进制数据。这就是我们翻译一系列字符(字母、数字、符号、标点符号等)的方式。)转换成一种专门的格式，以帮助我们说计算机的语言，并理解它的反馈。

在本指南中，我们将演示如何在 Rust 中编码和解码您的数据。

## Rust 中的编码和解码

如果你认为编码和解码听起来很无聊，你并不孤单。有许多边缘情况，过程可能相当复杂。

幸运的是，在 Rust 中，就像在许多其他编程语言中一样，编码和解码是由模块处理的，这些模块已经针对大多数边缘情况进行了彻底的测试。高效的编码和解码库对于像 Rust 这样接近机器的语言来说尤为关键。

Rust 中的编码相对简单。尽管它没有包含在核心的 Rust 包中，但是社区开发的一些解决方案可以很好地处理这项工作。这些工具使您能够通过函数发送要编码或解码的字符串，并接收所追求的结果(编码或解码的字符串)。

### `base64`铁锈库

[base64](https://crates.io/crates/base64) 设计用于尽可能快和精确地编码和解码 base64。顾名思义，它只适用于 base64。它实际上有两个转换函数——`encode()`和`decode()`——以及配置函数来帮助你塑造它解码和编码的方式。

```
extern crate base64;
use base64::{encode};
fn main() {
    let a = "hello world";
    println!("{}", encode(a)); // -> aGVsbG8gd29ybGQ=
}

```

信不信由你，在你的电脑上处理二进制文件时，base64 实际上是头等必需品。Base64 通常用于对二进制数据(图像、声音文件等)进行编码，我们在网上分享的所有内容都使用这种编码，从电子邮件附件到在数据库中保存文件。

如果想更深入地了解，可以去找 base64 大师。

### `rust-encoding`

`[rust-encoding](https://crates.io/crates/encoding)`支持大量的字符编码(所有那些被 WHATWG 标准支持的)。非常独特的是，它通过用指定的值替换处理字符串时收到的错误来处理错误。当检测到错误时，可以使用严格模式完全停止执行=。

编码的`encode`和`decode`方法将一个`String`转换为`Vec<u8>`，反之亦然。由于支持许多编码类型，该库提供了两种方式来获得您的编码:

1.  1.  您将用于编码过程的编码附加到其中。所有未使用的编码类型都将从二进制文件中丢弃
    2.  `encoding::label`，它根据给定的标签捕获编码，并返回静态编码类型，得到一个更大的二进制文件

```
use encoding::{Encoding, EncoderTrap};
use encoding::all::ISO_8859_1; // use with all
use encoding::label::encoding_from_whatwg_label; // use with label

assert_eq!(ISO_8859_1.encode("caf\u{e9}", EncoderTrap::Strict),
Ok(vec![99,97,102,233]));
let euckr = encoding_from_whatwg_label("euc-kr").unwrap();
assert_eq!(euckr.name(), "windows-949");
```

`rust-encoding`是下载量最高的库之一(3.5k/周)，尽管它已经四年没有更新了。可以肯定地说，它极其稳定和强健。

### `data-encoding`

[`data-encoding`](https://crates.io/crates/data-encoding) 处理 15 种不同的编码类型，允许用户定义自己的自定义边缘情况。用数据编码来编码和解码你的字符非常具体和简单，假设你知道你想编码成什么类型或者从什么类型解码。

```
BASE64.encode(&input_to_encode)
HEXLOWER.decode(&input_to_decode)

```

该库允许您为 2 到 64 的基数定义自己的小端 ASCII 基数转换编码，这对于本机编码类型是无法实现的，因为它们是特殊的用例。

是一个小型的现代化图书馆。它非常受欢迎并且维护良好，如果您必须使用它们支持的编码类型，这是一个很好的选择。

### `integer-encoding`

我们认为整数是字符，因此它们也需要编码和解码。 [`integer-encoding`](https://lib.rs/crates/integer-encoding) 支持两种整数类型:`[FixedInt](https://docs.rs/integer-encoding/2.0.0/integer_encoding/trait.FixedInt.html)`和`[VarInt](https://docs.rs/integer-encoding/2.0.0/integer_encoding/trait.VarInt.html)`。它还提供了有效的读写类型来简化整数的处理。

这个库有一个特殊的用例，在使用 Google 的[协议缓冲区](https://developers.google.com/protocol-buffers/docs/encoding#top_of_page)的开发人员中众所周知，它说明了对非通用类型进行编码/解码的需要。

## 网络的 Rust 编码和解码

编码和解码的一个最实用和最常见的用例是在 web 上，在 web 上，我们使用字符串、表单、数组、JSON 等创建了两个不同的实体(后端和前端)。它可能看起来天衣无缝，但幕后有很多工作要做。

让我们放大一些有助于实现这种交流的库。

### `urlencoded`

人们经常想知道为什么浏览器和服务器能够从 URL 中读取参数和表单数据，以及其他奇怪的字符，如空格、问号等。尽管这是 web 的[标准，但是每种语言都应该能够阅读和理解它，以便能够与用户的浏览器进行交流。](https://url.spec.whatwg.org/)

在 Rust 中， [`urlencoded`](https://crates.io/crates/urlencoded) 在 [Iron](https://github.com/iron/iron) web 框架中扮演中间件的角色。它的职责是将 URL 查询字符串解析成`hashmaps`，这在 Rust 中更容易阅读。得到的值保存在一个`Vec`中，以确保在密钥被复制的情况下不会丢失任何信息。因此，查询`a=b&a=c`将导致从`a`到`[b, c]`的映射。

`urlencoded`也能够解析 MIME 类型的 post 主体:`application/x-www-form-urlencoded`。在前端处理表单时，这很方便。

在 url 编码和解码方面,`urlencoded`是一个完整的库。在不到四个依赖项的情况下工作，它被认为是稳定的，可以投入生产了。

### `multer-rs`

深受 [`multipart`](https://crates.io/crates/multipart) 和[`multipart-async`](https://github.com/abonander/multipart-async)[`multer-rs`](https://github.com/rousan/multer-rs)的启发，以异步解析 Rust 中`multipart/form-data`内容类型的能力著称。以前的库不能处理任何异步服务器，这是至关重要的，因为 Rust 的生态系统正在走向异步。

`multer-rs`接受`Bytes`的一个`Stream`作为源，并生成一个 iterable，包含从`multipart/form-data`获得的所有需要的字段，包括来自文件的数据，然后可以将这些数据写入一个定制文件。

由于对异步工作的多部分解码器的需求，它越来越受欢迎。之前流行的都达不到。虽然它的维护者仍在研究新的问题，但 multer-rs 可以被认为是稳定的。

### `percent-encoding`

[`percent-encoding`](https://lib.rs/crates/percent-encoding) 是 url-encoder 的绝佳替代品。它们有许多相似的特征，但是百分比编码是不同的，因为:

1.  1.  它不依赖于铁网框架
    2.  它是一个完整的 URL 解析器，可以分解 URL 的结构，只得到特定的部分，而不是查询参数
    3.  它解析和序列化 HTML 表单中使用的`[application/x-www-form-urlencoded](https://url.spec.whatwg.org/#application/x-www-form-urlencoded)`语法

```
// code from url docs
use url::{Url, Host, Position};
let issue*list*url = Url::parse(
"https://github.com/rust-lang/rust/issues?labels=E-easy&state=open"
)?

;assert!(issue_list_url.scheme() == "https"); // takes the scheme
assert!(issue_list_url.hoststr() == Some("github.com")); //get the url's host
assert!(issue_list_url.port() == None); // get the port if there's one
assert!(issue_list_url.path() == "/rust-lang/rust/issues"); // the whole path
assert!(issue_list_url.query() == Some("labels=E-easy&state=open")); // get only the query
assert!(&issue_list_url[Position::BeforePath..] == "/rust-lang/rust/issues?labels=E-easy&state=open");
```

这个库优雅地处理边缘情况，就像拥有相对 URL 一样。它使您能够加入 URL 并创建新的 URL。

```
use url::Url;

let this_document = Url::parse("http://servo.github.io/rust-url/url/index.html")?;
let css_url = this_document.join("../main.css")?;
assert_eq!(css_url.as_str(), "http://servo.github.io/rust-url/main.css");

```

当比较开发人员的体验时，`percent-encoding`比`url-encoded`更有优势，尤其是如果你没有使用 Iron 框架的话。此外，虽然它仍然使用 2015 年的老锈，`percent-encoding`似乎更强大。

### `base64-url`

[`base64-url`](https://lib.rs/crates/base64-url) 是——你猜对了——另一个 base64 编码器，现在在 URL 中。虽然听起来不太可能，但当你需要缩短 URL 或包含一些二进制数据时，这是一个[便利的功能](https://en.wikipedia.org/wiki/Base64#URL_applications)。

### `blob-uuid`

有时你只是简单地编码来缩短字符串的长度。 [`blob-uuid`](https://lib.rs/crates/blob-uuid) 用 36 个字符的 UUIDs 帮你做到这一点，编码后变成 22 个。如果您想在 URL 中共享 UUID，它还可以帮助您隐藏它。

```
let uuid = Uuid::parse_str("557c8018-5e21-4b74-8bb0-9040e2e8ead1").unwrap();
assert_eq!("VXyAGF4hS3SLsJBA4ujq0Q", blob_uuid::to_blob(uuid));

```

## 操场

我在我的 [Repl.it](https://repl.it/@DylanJason/ThankfulChocolateBug#main.rs) 上为这些图书馆准备了一个操场。确保使用`Cargo.toml`添加您想要的库，并在终端上点击`cargo run`。

## Rust 中的序列化

序列化意味着将数据(数组、对象和类似的结构)转换成单个字符串，以便于存储或传输。序列化是一个非常具体的主题，被视为编码的一个子集。然而，为了简单起见，本文不包括序列化库。

Rust 的生态系统提供了一些[优秀的序列化库](https://blog.logrocket.com/rust-serialization-whats-ready-for-production-today/)，包括[`serde_json`](https://blog.logrocket.com/json-and-rust-why-serde_json-is-the-top-choice/)[`toml`](https://lib.rs/crates/toml)[`bincode`](https://lib.rs/crates/bincode)。这些在幕后也经历了某种形式的编码和解码。

## 结论

还有比我们上面描述的更多的 Rust 编码库。我们强调了一些最常见的。在现实生活中，您最有可能根据需要处理的格式选择编码库。

编码和解码不限于字符编码。Rust 也有各种各样的[媒体编码库。](https://lib.rs/multimedia/encoding)

上面列出的所有库都是为生产使用而建立的、流行的和稳定的。这仅仅是因为编码和解码在任何现代语言中都是一个强制性的特征。列表是多样化的，每个库以不同的方式处理特定的用例。这使得它们很难相互比较。

Rust 有很强的编码和解码库基础。然而，我相信如果 Rust 内核中至少装载了最流行的编码类型(UTF-8 和 base64 ),并且设计得像 Python 中的[模块](https://docs.python.org/3/library/base64.html)一样具有延展性，Rust 会感觉更加成熟。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。