# Rust cryptography 库:一个全面的列表- LogRocket 博客

> 原文：<https://blog.logrocket.com/rust-cryptography-libraries-a-comprehensive-list/>

密码术是将信息转换成安全格式的过程，主要通过加密。加密确保通过网络传输的数据是安全可靠的。

虽然 Rust 有一个随机数生成器，但现在使用的主要套件是 OpenSSL。正如您将在本指南中看到的，Rust 生态系统提供了大量功能丰富的加密库，帮助您在应用程序之间安全传输数据。

在本指南中，我们将回顾 Rust 中的加密技术，并比较一些开源加密库，包括:

## Rust 的加密套件

使用加密套件，您可以保护使用 TLS 或 SSL 的网络连接。在这一节中，我们将看看一些开源 Rust 套件，评估每个套件的稳定性和生产就绪性。

### `openssl`

`[openssl](https://crates.io/crates/openssl)`为流行的 OpenSSL 加密库提供了一个安全的接口。这个库发布了 123 个版本，最新的稳定版本是 0.10.30。`openssl`在 Apache 2.0 下获得许可。

要使用`openssl`，请将下面的代码添加到您的`cargo.toml`文件中。

```
openssl = "0.10.30"

```

### `orion`

`[orion](https://docs.rs/orion/0.15.5/orion/)`旨在尽量减少不安全代码的使用。该库支持 Rust 1.41 或更高版本以及以下加密方法。

*   AEAD: (X)ChaCha20Poly1305
*   流密码:(X)ChaCha20
*   KDF: HKDF-HMAC-SHA512，PBKDF2-HMAC-SHA512，Argon2i
*   麦克:MAC 沙 512，波利 1305
*   哈希:BLAKE2b，SHA512

这个库在 MIT 下授权，目前有 [68 个版本发布](https://crates.io/crates/orion)，版本号是 0.15.4。要使用该库的当前版本，请将以下代码添加到您的`cargo.toml`文件中:

```
orion = "0.15.4"

```

默认情况下，orion 使用 STD。要在`no_std`上下文中使用 orion，您需要通过编写下面的代码来指定依赖关系。

```
orion = { version = "*", default-features = false }
# Replace * with the most recent version

```

Argon2i 将无法与`no_std`一起使用。要允许 Argon2i，您必须启用`alloc`功能。

```
[dependencies.orion]
version = "*" # Replace * with the most recent version
default-features = false
features = ["alloc"]

```

### `libsodium-sys`

lib na-sys 是一个开源的 Rust 绑定到[钠库](https://github.com/jedisct1/libsodium)。这个图书馆已经出版了 [24 个版本](https://crates.io/crates/libsodium-sys)。它的最新稳定版本是 0.2.6。该库获得了 Apache-2.0/MIT 的许可。

可用于 libna-sys 的绑定函数如下:

*   `crypto::box_`和`crypto::sign`用于公钥加密
*   `crypto::sealedbox`对于密封箱
*   密钥加密的`crypto::secretbox`、`crypto::stream`、`crypto::auth`和`crypto::onetimeauth`
*   低级功能的`crypto::hash`、`crypto::verify`和`crypto::shorthash`

### `gpgme`

这是一个针对 Rust 的 [GnuPG Made Easy (GPGME)](https://www.gnupg.org/software/gpgme/index.html) 库。直接从应用程序中使用 GnuPG 可能会很复杂；GPGME 使得访问 GnuPG (GNU 隐私卫士)更加容易。

该库最稳定的版本 0.9.2 不支持捆绑`gpgme`库源代码来尝试通过 buildscript 进行构建。

要使用`gpgme`，请将以下内容添加到您的`cargo.toml`文件中。

```
gpgme = "0.9.2"

```

这个库被授予了`LGPL-2.1`的许可，并且已经发布了 [14 个版本](https://crates.io/crates/gpgme/0.8.0)。

### `ring`

这个开源库允许我们使用 Rust 和 BoringSSL 的加密原语构建安全、快速、小型的加密应用程序。已经出版了 94 个版本。它的最新稳定版本是`[ring](https://crates.io/crates/ring)` [0.16.15](https://crates.io/crates/ring) 。

戒指有以下特点。

*   `alloc`(默认)，启用需要使用堆的特性，特别是 RSA
*   `dev_urandom_fallback`(默认)，当`getrandom()`系统调用在运行时不被支持时，默认情况下导致`ring::rand::SystemRandom`回退到`/dev/urandom`。`dev_urandom_fallback`目前仅受 Linux 操作系统支持
*   `std`，启用使用`libstd`的功能，尤其是`std::error::Error`功能

要使用这个库的最新版本，请将下面的代码添加到您的`cargo.toml`文件中。

```
ring = "0.16.15"

```

## Rust 的随机数发生器(RNG)

考虑到计算机/编程算法固有的可预测性(例如，您可以通过输入来确定输出)，您是否曾经想过信息是如何安全的？随机数发生器接受不确定的输入(如相位噪声或时钟信号)，并生成不可预测的数字作为输出。

下面是一些最好的和最适合生产的 Rust 随机数生成器。

### `rand`

`[rand](https://docs.rs/rand/0.7.3/rand/)`是一个用于随机数生成的 Rust 库。随机数被生成并转换成有用的类型、分布和一些与随机性相关的算法。

`rand`0.7 版本需要【1.32 或更高版本。同时，`rand` 0.5 要求`rustc` 1.22 或更高版本，0.4 和 0.3 版本要求【1.15 或更高版本(因为大约。2017 年 6 月)。

有些`rand`代码可能适用于较老的 Rust 版本，但不建议使用它。`rand`已经发布了 62 个版本，有一个版本是被强拉(未发布)的版本，0.7.1。

要使用当前版本的`rand`，请将以下内容添加到您的`cargo.toml`文件中。

```
[dependencies]
rand = "0.7"

```

### `uuid`

`[uuid](https://docs.rs/uuid/0.8.1/uuid/)`创建并解析通用唯一标识符(UUIDs)，它是以 16 个八位字节存储的唯一的 128 位数字。使用 UUIDs，唯一标识符可以被分配给实体，而不需要中央分配机构。

这个库已经发布了 [45 个版本](https://crates.io/crates/uuid)，最新版本是 0.8.1。

要使用`uuid`，请将以下内容添加到您的`cargo.toml`文件中。

```
[dependencies]
uuid = "0.8"

```

## 基于密码的 Rust 加密

密码是一种加密形式，可以帮助我们创建强密钥。使用基于密码的加密，用户可以根据他们提供的密码创建强密钥。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### `bcrypt`

是一个开源库，可以让你在 Rust 上轻松散列和验证密码。这个图书馆有 [21 个版本](https://crates.io/crates/bcrypt)；最新是版本`0.8.2`。

要使用`bcrypt`，请将以下内容添加到您的`cargo.toml`文件中。

```
bcrypt = "0.8.2"

```

此版本`bcrypt`支持 1.36.0 版本作为 Rust 的最低版本。

### `djangohashers`

`[djangohashers](https://docs.rs/djangohashers/1.3.1/djangohashers/)`是 Django 项目中使用的密码原语的 Rust 端口。尽管 Django 有由一些处理密码的方法组成的`django.contrib.auth.models.User`类，如`set_password()`和`check_password()`，`djangohashers`执行这些方法背后的原始函数。你可以在任何 Rust 项目中使用这个库的密码哈希算法。

`djangohashers`已经 [23 个版本](https://crates.io/crates/djangohashers)出版了。最新的稳定版本是 1.3.1。

要安装`djangohashers`，请将依赖项添加到您的`cargo.toml`文件中。

```
[dependencies]
djangohashers = "^1.3"

```

### `pwhash`

这个 Rust 开源库由一组密码哈希和验证例程组成。

使用`[pwhash](https://docs.rs/pwhash/0.3.1/pwhash/)`，您可以使用`verify()`函数根据散列来验证密码。您还可以分别使用`hash()`和`hash_with()`函数，使用默认的特定算法参数或定制的参数来散列密码。

这个库在编写时已经发布了[五个版本](https://crates.io/crates/pwhash)，最稳定的版本是 0.3.1。

## Rust 的 TLS 库

借助传输层安全性(TLS ),通过互联网发送的数据将被加密，以保护私有和敏感信息免受不良分子的侵害。

以下库为基于 Rust 的应用程序提供了 TLS 协议。

### `rustls`

发音为“rustless”，是一个为 Rust 实现 TLS 的现代库。它使用`[ring](https://github.com/briansmith/ring)`库进行加密`[libwebpki](https://github.com/briansmith/webpki)`进行证书验证。

`rustls`无需配置即可确保高水平的加密安全性。它有 [29 个出版版本](https://crates.io/crates/rustls)；最稳定的是 0.18.1 版本。

要使用当前版本的`rustls`，请将以下内容添加到您的`cargo.toml`文件中。

```
rustls = "0.18.1"

```

### `tokio-openssl`

`[tokio-openssl](https://docs.rs/tokio-openssl/0.5.0/tokio_openssl/)`是 Tokio 的 SSL 流的实现，这是 Rust 的异步运行时，由 OpenSSL 支持。

`[tokio-openssl](https://crates.io/crates/tokio-openssl)`在 MIT/Apache-2.0 下授权，最新的稳定版本是 0.4.0。

要使用这个版本，请将以下内容添加到您的`cargo.toml`文件中。

```
tokio-openssl = "0.4.0"

```

### `tokio-rustls`

`[tokio-rustls](https://docs.rs/tokio-rustls/0.20.0/tokio_rustls/)`是使用`[rustls](https://crates.io/crates/tokio-rustls)`库的 Tokio 的异步 TLS/SSL 流。这个库目前有 56 个版本，当前的稳定版本是 0.14.1。`tokio-rustls`从 [Tokio-TLS](https://github.com/tokio-rs/tokio) 项目开始。

### `webpki`

使用`webpki`，您可以验证 Web PKI (TLS/SSL)证书。这个库使用`[ring](https://github.com/briansmith/ring)`进行签名验证。

对于`webpki`，效率和安全性没有争议，因为它使用 [Rust 的借用检查器](https://blog.logrocket.com/introducing-the-rust-borrow-checker/)来验证其零拷贝解析策略是安全和高效的。`webpki`对它使用的堆栈内存量有严格的限制，所以它从不在堆上分配内存。

`webpki`已经发布了 [40 个版本](https://crates.io/crates/webpki)，最新版本是 0.21.3。该库未经许可，也不提供任何担保。

在密码学中，工具的重要性怎么强调都不为过。最好的方法使对等体之间的信息保护几乎天衣无缝。

让我们放大一些最流行的 Rust 加密工具，包括从令牌管理、文件管理到 cookie 管理的服务。

### `tempfile`

假设您想要创建一个文件，但不关心它的名称或位置，因为它是一个临时文件，您希望紧急处理并在以后删除。完成后，`[tempfile](https://docs.rs/tempfile/3.1.0/tempfile/)`库会删除该文件。

该工具使您能够使用`tempfile()`和`tempdir()`功能删除临时文件和目录。

`tempfile`在 MIT/Apache 2.0 下授权，有 [35 个版本](https://crates.io/crates/tempfile)；最新的是 3.1.0。

### `cookie`

简单来说，`[cookie](https://docs.rs/cookie/0.14.3/cookie/)`增强了 HTTP cookie 解析和 cookie jar 管理。这个库是在 MIT 或 Apache 2.0 下许可的，有 62 个版本(最新的版本是 0.14.2)。`cookie`已经被[下载了近 600 万次](https://crates.io/search?q=cookie)。

### `frank_jwt`

听说过 JSON Web Token (JWT)吗？您可以通过解码、验证和生成称为 JWT 的编码凭据，在双方之间安全地传递声明。`[frank_jwt](https://docs.rs/crate/frank_jwt/2.1.0)`是 Rust 中 [JSON Web 令牌的实现。](https://blog.logrocket.com/jwt-authentication-in-rust/)

这个库支持加密算法，在 Apache 2.0 下获得许可，在撰写本文时有 [14 个版本](https://crates.io/crates/frank_jwt)。

## 锈迹斑斑

密码学中的哈希意味着将数据转换成人类无法破译的独特字符串。下面是一个简单的数据及其等效散列字符串的示例:

```
Data: Hello
Hash: f7ff9e8b7bb2e09b70935a5d785e0cc5d9d0abf0

```

让我们放大一些在 Rust 中提供散列的库。

注意:在“Rust 的基于密码的加密”一节中提到的许多库也可以用于 Rust 中的散列。

### `rust-fnv`

FNV (Fowler/Noll/Vo)是一个定制的哈希实现，速度快，分散性好。虽然默认的 hasher 实现`SipHash`在很多情况下很好，但它通常比其他使用短键的算法要慢。

`rust-fnv`使用短键的算法执行速度更快。`rust-fnv`的缺点是它在较大的输入上表现不佳，并且不提供对碰撞攻击的保护。

这个库是在 Apache 2.0/MIT 下许可的，它有[八个发布版本](https://crates.io/crates/fnv)。

### `twox-hash`

这个库是 XXHash 算法的 Rust 实现，这是一种在 RAM 速度限制下运行的快速哈希算法。

要使用这个`[twox-hash](https://docs.rs/twox-hash/1.6.0/twox_hash/)`，将以下内容添加到您的`cargo.toml`文件中。

```
twox-hash = "1.5.0"

```

`twox-hash`有 [13 个版本](https://crates.io/crates/twox-hash)有两个版本被撤回(未发布)。在我写作的时候，`twox-hash`最稳定的版本是`1.5.0`。

### `blake2-rfc`

如果你想要一个比 MD5、SHA-1、SHA-2 更快的散列函数，并且像最新版本的 SHA-3 一样安全，那么只需看看`[blake2-rfc](https://docs.rs/blake2-rfc/0.2.18/blake2_rfc/)`。

`blake2-rfc`是 BLAKE2 的纯 Rust 实现，基于 RFC 7693。它有 [20 个版本](https://crates.io/crates/blake2-rfc)，最新的是 0.2.18。

## Rust 的密码算法

算法是最广泛使用的隐私保护方法之一。这些通常是为数据加密、认证和数字签名而设计的。

### `bulletproofs`

本库使用 Ristretto 实现了 Bulletproofs。Ristretto 是一种压缩曲线(椭圆)的技术；它将曲线的余因子除以 4 或 8 来实现素数阶群。

`[bulletproofs](https://github.com/dalek-cryptography/bulletproofs)`在写作时已经有[八个版本](https://crates.io/crates/bulletproofs)出版，版本`1.0.3`被撤。

### `curve25519-dalek`

这个开源库是 Ristretto 和 Curve25519 上操作的 Rust 实现。

`[curve25519-dalek](https://docs.rs/curve25519-dalek/0.18.0/curve25519_dalek/)`旨在提供干净安全的中级 API，执行基于 ECC 的加密协议，如零知识证明系统、密钥协议、签名和匿名证书。

这个库是在 BSD 3 条款下许可的，在撰写本文时已经有 [64 个版本在](https://crates.io/crates/curve25519-dalek)发布。版本`2.0.0`被猛拉。

要在您的应用程序中使用这个库的最新发布版本，请将下面的代码添加到您的`cargo.toml`文件中。

```
curve25519-dalek = "3.0.0"

```

### `ed25519-dalek`

这个库是一个快速有效的 Rust 实现，用于在 Rust 中生成、签名和验证 ed25519 密钥。

有了`[ed25519-dalek](https://docs.rs/ed25519-dalek/1.0.1/ed25519_dalek/)`，在 Rust 中，ed25519 密钥生成、签名和验证变得更加容易和快速。

这个库是在 BSD 3-条款下授权的，已经发布了超过 [28 个版本](https://crates.io/crates/curve25519-dalek)。

### `merlin`

`[merlin](https://docs.rs/merlin/2.0.0/merlin/)`提供基于抄本的 RNG 作为针对坏熵攻击的深度防御。`merlin`是零知识证明的抄本构造。

如果您在应用程序中使用`merlin`，您可以执行非交互式协议，就像它们是交互式的一样。这是因为`merlin`实现了菲亚特-沙米尔转换的自动化。

### `rust-secp256k1`

`rust-secp256k1`是一个围绕`libsecp256k1`的包装器，一个可以使用 SECG 曲线`[secp256k1](https://docs.rs/secp256k1/0.19.0/secp256k1/)`产生 ECDSA 签名的库。

你会喜欢这个图书馆的效率；它没有为独立实现中的效率和使用进行分配(除了在单元测试中)。

这个库是 CC0-1.0 许可的，已经发布了 63 个版本。

### `subtle`

这是一个用于常数时间加密实现的开源库。

`[subtle](https://docs.rs/subtle/2.3.0/subtle/)`支持最低 Rust 版本 1.41。已经有 [24 个版本](https://crates.io/crates/subtle)发布，最新的是 2 . 3 . 0；2.2.0 版本被强拉了。

### `x25519-dalek`

这是 x25519 椭圆曲线 Diffie-Hellman 密钥交换的纯 Rust 实现，曲线操作由 curve25519-dalek 提供。

`[x25519-dalek](https://crates.io/crates/x25519-dalek)`已出版 17 个版本；1.1.0 是最新的。

要在您的应用程序中使用`x25519-dalek`，请将以下内容添加到您的`cargo.toml`文件中。

```
x25519-dalek = "1.1.0"

```

### `zkp`

本库按照 Schnorr 风格实现了零知识证明。这意味着`zkp`可以验证一个声明的有效性，而无需发布除声明有效性之外的任何信息(敏感信息)。

`[zkp](https://docs.rs/zkp/0.6.0/zkp/)`提供了两个级别的 API:基于`define_proof`宏的高级声明式 API 和受 Bellman 启发的低级命令式 API，后者为 Schnorr 风格的语句提供了约束系统。

`zkp`根据 CC0-1.0 获得许可。它有 [12 个版本](https://crates.io/crates/zkp)，其中四个已经被撤回。

## 结论

无论你是用 Rust 还是其他编程语言来写，当你在互联网上传输信息时，你都应该使用加密技术。正如您可以从各种流行的、生产就绪的加密套件、随机数生成器、密码解决方案、TLS、工具和可用算法中看出的那样，Rust 中的加密技术相当强大。

我们已经看到了包含不同加密方法的不同章节，以及在基于 Rust 的应用程序中帮助安全解析数据的库。

如果你想了解更多关于 Rust 的密码学知识，请查看 [Rust Cryptography](https://www.arewewebyet.org/topics/crypto/) 。

感谢您的宝贵时间！如果您对本文有任何问题或建议，请通过 twitter 联系我。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。