# 如何选择合适的 Rust HTTP 客户端

> 原文：<https://blog.logrocket.com/best-rust-http-client/>

***编者按**:这篇 Rust 文章最后一次更新是在 2022 年 12 月 21 日，目的是更新代码，并包括不太知名但很有用的 HTTP 客户端，如 Actix Web Client、rustify 和 tokio-curl。查看这篇文章，获得用 reqwest 库发出 [HTTP 请求的教程。](https://blog.logrocket.com/making-http-requests-rust-reqwest/)*

HTTP 客户端的重要性怎么强调都不为过，这就是为什么在为手头的任务选择合适的 HTTP 客户端时，您应该做足功课。根据您的项目所基于的技术栈或者您必须进行的 HTTP 调用的数量，某些库将非常适合您，而其他库可能会比它们的价值更麻烦。

在本指南中，我们将介绍六种 Rust HTTP 客户端的主要功能:

1.  [卷锈](#curl-rust)
2.  [超级](#hyper)
3.  [请求西部](#reqwest)
4.  [伊萨克](#isahc)
5.  [冲浪](#surf)
6.  [ureq](#ureq)

我们还将演示如何对每个库发出 GET 和 POST 请求。我们开始吧！

## 1.卷曲锈病

curl-rust 为 rust 提供了 [libcurl](https://curl.haxx.se/libcurl/) 绑定，这意味着它包括了一个基于 C 的 HTTP 库接口。换句话说，您可以将 libcurl 的功能与 Rust 提供的额外安全层结合使用。

如果你想完全停留在 Rust 领域，避免基于 C 的 curl 可能产生的问题，你可能想选择一个完全依赖 Rust 的库。重要的是，curl-rust 不提供对异步请求的任何支持，这意味着它比任何异步请求都要慢，而且占用更多资源。

我的建议是，只有在你确实需要使用 curl 的情况下才使用 curl-rust——例如，如果你正在做一个依赖于不同技术栈并且已经集成了 curl 的项目。curl-rust 附带了一些您并不总是需要的功能，比如支持 Telnet、SMTP、FTP、IMAP 或 LDAP 等传输协议。有更多的轻量级解决方案，为今天的 web 需求提供更多定制的功能。

这里有一个使用 curl-rust 的 POST 请求的例子。请注意更复杂的语法:

```
use std::io::Read;
use curl::easy::Easy;

fn main() {
    let mut data = "this is the body".as_bytes();

    let mut easy = Easy::new();
    easy.url("http://www.example.com/upload").unwrap();
    easy.post(true).unwrap();
    easy.post_field_size(data.len() as u64).unwrap();

    let mut transfer = easy.transfer();
    transfer.read_function(|buf| {
        Ok(data.read(buf).unwrap_or(0))
    }).unwrap();
    transfer.perform().unwrap();
}

```

curl-rust 的人气统计如下:

*   GitHub stars: 880
*   在 [crates.io](https://crates.io/crates/curl) 上的空前下载量:9818464
*   最近下载(过去 90 天):1，191，182

## 2.高度紧张的

当高级库没有提供您正在寻找的所有功能时，查看一下 [hyper](https://github.com/hyperium/hyper) 库可能是个好主意。它是相对低级的，所以当你需要更高级的功能时它是完美的。

我们将在本文中讨论的几个库都基于 hyper。它的设计是异步的，提供了客户端和服务器端的 API。这是一个可靠、可靠的库，在生产中广泛使用。

如果你不需要 hyper 提供的任何高级功能，也许值得看看基于 hyper 的更高级的库。hyper 旨在成为其他库和应用程序的构建模块。如果你想要一个更方便的 HTTP 客户端，hyper 推荐看看 [reqwest](https://github.com/seanmonstar/reqwest) ,因为它是建立在 hyper 之上的(稍后会详细介绍)。

此外，hyper 拥有一个相当活跃的贡献者社区，甚至还运营着一个 [Discord 服务器](https://discord.gg/kkwpueZ)。[官方网站](https://hyper.rs/)提供了几个指南来帮助你快速开始运行 HTTP 客户端或服务器。更多细节可以在 API 参考中找到—例如， [`Body`](https://docs.rs/hyper/0.13.7/hyper/body/index.html) [对象](https://docs.rs/hyper/0.13.7/hyper/body/index.html)是如何实现的。

下面是如何使用 reqwest 发出 GET 请求。首先，创建一个新的客户机对象，然后向它传递一个从中检索数据的 URL。接下来，使用 Rust 支持的新的`await`语法来等待响应:

```
use hyper::Client;

let client = Client::new();

// Parse an `http::Uri`...
let uri = "http://httpbin.org/ip".parse()?;

// Await the response...
let resp = client.get(uri).await?;

println!("Response: {}", resp.status());

```

以下是 hyper 的优势概述:

*   HTTP/1 和 [HTTP/2](https://developers.google.com/web/fundamentals/performance/http2) 支持
*   异步设计
*   通过并发支持实现领先的性能
*   经过良好测试的 HTTP 客户端
*   广泛的生产用途
*   客户端和服务器端 API 支持

如果你在家记分:

*   GitHub 星级:10905
*   在 [crates.io](https://crates.io/crates/hyper) 上的空前下载量:67138349
*   最近下载(过去 90 天):8，981，728 次

## 3.reqwest

如果您正在寻找一个更高级的 HTTP 客户端， [reqwest](https://github.com/seanmonstar/reqwest) 可能会满足您的需求。该库提供了一个基于超级库的 HTTP 客户端。

默认情况下，reqwest 包含一个能够发出异步请求的客户端。但是，如果您只需要进行少量的 HTTP 调用，并且不想增加使用异步函数的复杂性，那么也可以对它进行配置，以便使用“阻塞”客户端。这是一个极好的特性。

HTTP 代理、可定制的重定向策略和对 cookies 的支持等特性使 reqwest 在便利性方面成为首选。这个 cookie 存储对于身份验证非常有用。

这个来自[官方文档](https://docs.rs/reqwest/0.10.7/reqwest/)的例子展示了如何进行 GET 和 POST 请求。如您所见，语法非常简单:

```
// 1\. GET
let body = reqwest::get("https://www.rust-lang.org")
    .await?
    .text()
    .await?;

println!("body = {:?}", body);

// 2\. POST
let client = reqwest::Client::new();
let res = client.post("http://httpbin.org/post")
    .body("the exact body that is sent")
    .send()
    .await?;

```

总而言之，reqwest 提供了以下突出特点:

*   异步和阻塞客户端
*   支持普通体、JSON、 [urlencoded](https://www.urlencoder.io/learn/) 和多部分数据
*   可定制的重定向策略
*   HTTP 代理
*   使用系统本机 TLS
*   饼干

至于它的受欢迎程度:

*   GitHub 星级:6693
*   在 [crates.io](https://crates.io/crates/reqwest) 上的空前下载量:42958476
*   最近下载(过去 90 天):6，447，770 次

## 4.Isahc

与 reqwest 非常相似， [Isahc](https://github.com/sagebind/isahc/) 提供了一个包含异步和同步方法的高级 HTTP 客户端。然而，与 reqwest 不同，Isahc 在引擎盖下使用 curl。如果您想要一个需要保持与 curl 兼容的客户机，这可能会很有用。它还提供了使用一个稳定的、积极开发的、流行的库的所有好处。

下面是一个简单的同步 GET 请求的例子。特别是，`status`、`headers`和`text`函数使得从响应对象中检索常用信息变得非常容易:

```
use isahc::prelude::*;

fn main() -> Result<(), isahc::Error> {
    // Send a GET request and wait for the response headers.
    // Must be `mut` so we can read the response body.
    let mut response = isahc::get("http://example.org")?;

    // Print some basic info about the response to standard output.
    println!("Status: {}", response.status());
    println!("Headers: {:#?}", response.headers());

    // Read the response body as text into a string and print it.
    print!("{}", response.text()?);

    Ok(())
}

```

让我们来看看 Isahc 最显著的特点:

*   完全支持 HTTP/1.1 和 HTTP/2
*   可配置的请求超时
*   完全异步的内核，对请求和响应体进行异步和增量读写
*   支持异步/等待的同步和异步 API
*   会话和 cookie 持久性

以下数据显示了 Isahc 的相对受欢迎程度:

*   GitHub stars: 595
*   在 [crates.io](https://crates.io/crates/isahc) 上的空前下载量:2，514，510
*   最近下载(过去 90 天):438，357

## 5.冲浪

Surf 是一个完全模块化的客户端，采用异步设计。它可以通过强大的中间件系统进行扩展。默认的本地客户端使用 curl，但是您也可以使用 [curl-client](https://github.com/php-http/curl-client) 。

curl-client 通过 Isahc 使用 curl 作为 HTTP 服务器。如果您不想使用基于 curl 的客户端，您可以选择使用 hyper-client，它使用 hyper 作为 HTTP 服务器。

下面是一个简单的中间件示例，它打印了每个请求的详细信息。如您所见，它使用了经典的`req`、`client`和`next`参数，您可以在许多其他 HTTP 客户端中找到这些参数。`next`参数允许您将请求传递给另一个中间件模块或请求一个处理程序:

```
use futures::future::BoxFuture;
use surf::middleware::{Next, Middleware, Request, Response, HttpClient};
use std::time;

/// Log each request's duration
#[derive(Debug)]
pub struct Logger;

impl<C: HttpClient> Middleware<C> for Logger {
    fn handle<'a>(
        &'a self,
        req: Request,
        client: C,
        next: Next<'a, C>,
    ) -> BoxFuture<'a, Result<Response, surf::Exception>> {
        Box::pin(async move {
            println!("sending request to {}", req.uri());
            let now = time::Instant::now();
            let res = next.run(req, client).await?;
            println!("request completed ({:?})", now.elapsed());
            Ok(res)
        })
    }
}

```

下面是一个使用 Surf 的简单 GET 请求示例:

```
#[tokio::main]
async fn main() -> Result<(), surf::Error> {
    let mut res = surf::get("https://httpbin.org/get").await?;
    println!("{}", res.body_string().await?);
    Ok(())
}

```

要运行该程序，您需要以下依赖项:

```
surf = "2.3"
tokio = { version = "1", features = ["full"] }

```

分区域资源中心的显著特点包括:

*   可通过强大的中间件系统进行扩展
*   通过客户端界面重用连接
*   完全流式请求和响应
*   默认启用 TLS/SSL
*   默认情况下启用 HTTP/2

至于它的受欢迎程度:

*   GitHub 星级:1332
*   在 [crates.io](https://crates.io/crates/surf) 上的空前下载量:1406370
*   最近下载(过去 90 天):222，065

## 6.ureq

ureq 是一个最小请求库，如果你对最小依赖树感兴趣的话，它会很有用。它是为了方便而设计的，并提供了一个阻塞 API 来简化事情。它没有使用任何不安全的 Rust，这对于想要坚持使用[安全 Rust](https://doc.rust-lang.org/nomicon/meet-safe-and-unsafe.html#:~:text=Rust%20can%20be%20thought%20of,do%20some%20really%20unsafe%20things.) 的开发者来说很好。

如果您重视编译时间，ureq 是一个特别好的选择。它不使用 curl 服务器，也不提供异步功能。这些都是有意省略的，以尽可能保持库的最小化和轻量级。因此，在撰写本文时，还没有迁移到异步功能的计划。

下面是一个使用 ureq 的 GET 请求的小例子。请注意设置标题和发出请求的简单性:

```
fn main() -> Result<(), ureq::Error> {
    let body: String = ureq::get("http://example.com")
        .set("Example-Header", "header value")
        .call()?
        .into_string()?;
    Ok(())
}

```

重申一下，ureq 的主要特点包括:

*   最小依赖树
*   简单的 API 设计
*   仅阻止 API
*   不使用不安全的铁锈

最后，我们来看一下 ureq 的人气:

*   GitHub 星级:1153
*   在 [crates.io](https://crates.io/crates/ureq) 上的空前下载量:5579564
*   最近下载次数(过去 90 天):1，071，415 次

## 荣誉奖

如果您正在寻找在 Rust 中构建 HTTP 客户端的流行库之外的选项，那么您很幸运！以下是其他不太知名的图书馆，您在探索选择时可能会考虑:

### Actix Web 客户端

Actix Web Client 是一个在 Rust 中构建 HTTP 客户端的流行库。Actix Web Client 以其[速度和效率](https://crates.io/crates/awc)而闻名，是在 Rust 中构建 Web 应用程序的强大而实用的选择。下面是一个运行中的库示例:

```
// create client
let mut client = awc::Client::default();

// construct request
let req = client.get("http://www.rust-lang.org")
    .insert_header(("User-Agent", "awc/3.0"));

// send request and await response
let res = req.send().await?;
println!("Response: {:?}", res);

```

### 证明合法

rustify 是一个轻量级的灵活的库，用于在 Rust 中构建 HTTP 客户端。它有助于简化搭建 HTTP APIs 的过程，并为构建和使用 HTTP 端点提供了一系列特性。

Rustify 支持异步和同步客户端，并允许使用所提供的客户端特征进行定制客户端实现。Rustify 还支持序列化请求、反序列化响应以及处理原始请求和响应数据。它包括各种处理请求的助手，比如中间件支持和响应包装。

下面是一个用 Rustify 创建的简单客户端:

```
use rustify::{Client, Endpoint};
use rustify_derive::Endpoint;

// Defines an API endpoint at /test/path that takes no inputs and returns an
// empty response.
#[derive(Endpoint)]
#[endpoint(path = "test/path")]
struct Test {}

let endpoint = Test {};
let client = Client::default("http://api.com"); // Configures base address of http://api.com
let result = endpoint.exec(&client).await; // Sends GET request to http://api.com/test/path

assert!(result.is_ok());

```

### 托基奥卷曲

如果您对在 Rust 中构建异步 HTTP 客户端感兴趣，那么 [tokio-curl](https://github.com/alexcrichton/tokio-curl) 可能值得考虑。这个库为 libcurl HTTP 库提供了一个基于未来的接口，libcurl HTTP 库是一个用于发送 HTTP 请求的广泛使用且备受推崇的 C 库。

通过构建在 Rust 中的 libcurl 之上，tokio-curl 允许您利用这个库的功能和稳定性，同时利用 Rust 的 tokio 库提供的异步编程模型。

无论您是需要发送简单的 HTTP 请求，还是需要发送带有自定义头和认证的更复杂的请求，tokio-curl 都提供了一系列的特性和选项来帮助您完成任务。如果您正在寻找一种可靠而高效的方式在 Rust 中构建异步 HTTP 客户端，tokio-curl 绝对值得一试。请参见下面的库操作:

```
extern crate curl;
extern crate futures;
extern crate tokio_core;
extern crate tokio_curl;

use std::io::{self, Write};

use curl::easy::Easy;
use futures::Future;
use tokio_core::reactor::Core;
use tokio_curl::Session;

fn main() {
    // Create an event loop that we'll run on, as well as an HTTP `Session`
    // which we'll be routing all requests through.
    let mut lp = Core::new().unwrap();
    let session = Session::new(lp.handle());

    // Prepare the HTTP request to be sent.
    let mut req = Easy::new();
    req.get(true).unwrap();
    req.url("https://www.rust-lang.org").unwrap();
    req.write_function(|data| {
        io::stdout().write_all(data).unwrap();
        Ok(data.len())
    }).unwrap();

    // Once we've got our session, issue an HTTP request to download the
    // rust-lang home page
    let request = session.perform(req);

    // Execute the request, and print the response code as well as the error
    // that happened (if any).
    let mut req = lp.run(request).unwrap();
    println!("{:?}", req.response_code());
}

```

## 选择最好的 Rust HTTP 客户端

如果你想要 Rust 中的低级 HTTP 库，我推荐用 hyper。它已经可以生产了，并且完全是用 Rust 编写的，所以你不必太担心安全问题。此外，它是唯一提到生产就绪的库。

对于更高级的 HTTP 库，我会选择 reqwest。该库建立在 hyper 之上，因此它提供了许多与 hyper 相同的优点，并且具有更方便的启动语法。

在本指南中，我们谈到了在许多其他情况下有用的其他库。如果你必须使用 curl，你应该探索 curl-rust，Isahc，甚至 Surf。如果你正在寻找一个轻量级的库，它不会增加很多编译时间，ureq 是一个不错的选择。但是，请注意，ureq 只支持同步请求。

希望这种比较能够帮助您为下一个 Rust 项目选择最佳的 HTTP 客户端。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。