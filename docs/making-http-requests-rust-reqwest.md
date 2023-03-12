# 在 Rust 中使用 Reqwest 发出 HTTP 请求

> 原文：<https://blog.logrocket.com/making-http-requests-rust-reqwest/>

由于强大的开源社区，Rust 似乎变得越来越强大。Rust 已经成为全栈应用的一个可行选项( [Rocket](https://rocket.rs/) 就是一个有趣的例子)。

但是随着无限的应用程序而来的是对无限的数据连接的需求。那么，我们如何从某个地方请求数据呢？

在我们的机器上保存数据转储和 CLI 输入并不有趣。让我们了解 Rust 的 [Reqwest](https://github.com/seanmonstar/reqwest) 库如何:

*   发出基本的 GET 和 POST 请求
*   使用标题验证和请求内容类型
*   将 JSON 序列化为可用的、类型安全的结构
*   帮助我们构建一个基本的 Spotify 搜索客户端

## 什么是铁锈？

如果您是 Rust 新手，那么对于任何编程背景来说，它都是一种非常受欢迎的语言。我认为它是 c 的现代等价物。但是为了使严格的类型和内存管理更易于管理，它增加了人类可读的编译时错误的细节和函数式编程特性，如[“匹配”表达式](https://doc.rust-lang.org/rust-by-example/flow_control/match.html)。

更重要的是，Rust 提供了一个欣欣向荣的贡献者社区和大量优秀的 T2 教程。

## Reqwest 是什么？

Reqwest 库是为使用 [HTTP 协议](https://developer.mozilla.org/en-US/docs/Web/HTTP)获取资源而构建的。它提供了一个简化的 API，可以向一个给定的 URL 发出`get`和`post`请求，还提供了一个全功能的`Client`模块，可以应用头部、cookies、重定向策略等。

Reqwest 遵循 Rust 的异步协议，使用“[期货](https://rust-lang.github.io/async-book/01_getting_started/01_chapter.html)”如果你不熟悉 Rust 的异步编程故事，它有两个特性:

*   **标志表示我们的异步代码块**(`await`关键字)。如果您在 JavaScript 中使用过`await`关键字，它在 Rust 中的工作方式类似
*   识别这些标志并执行我们的异步程序的运行时。Rust 采用了`async`关键字来标记我们应该等待响应的函数和块范围。然而，Reqwest 也依赖于 [Tokio](https://github.com/tokio-rs/tokio) 运行时来有效地排队异步事件

要了解更多，还可以查看 [Rust 古雅的异步编程书籍](https://rust-lang.github.io/async-book/01_getting_started/01_chapter.html)。

## 使用 Reqwest 构建应用程序

> **免责声明**:该演示使用了 2021 年 10 月记录的 Spotify API。从那时起，他们的 API +认证协议可能已经改变了！但是不要担心，从这个演示中学到的知识仍然适用。

对于我们的项目，我们将使用 [Spotify API](https://developer.spotify.com/documentation/web-api/) 来构建一个简单的搜索客户端，以快速获取歌曲链接并与朋友分享。为了简单起见，我们跳过了官方的认证流程，所以如果你想跟着做，[点击这里](https://developer.spotify.com/console/get-search-item/)来获取一个你可以使用的示例 API 令牌。如果您需要，可以免费创建帐户。

## 创建我们的首个`get` Reqwest

让我们进入一些基本的`get`和`post`请求。首先，用 [Cargo](https://doc.rust-lang.org/cargo/getting-started/first-steps.html) 创建一个新项目，并向`cargo.toml`添加一些依赖项:

```
[dependencies]
reqwest = { version = "0.11", features = ["json"] } # reqwest with JSON parsing support
futures = "0.3" # for our async / await blocks
tokio = { version = "1.12.0", features = ["full"] } # for our async runtime

```

现在，让我们给 Spotify 的一个 API 端点打个电话:

```
use reqwest;

// tokio let's us use "async" on our main function
#[tokio::main]
async fn main() {
    // chaining .await will yield our query result
    let result = reqwest::get("https://api.spotify.com/v1/search").await
}

```

相当简单！让我们看看印刷品给了我们什么:

```
println!("{:?}", result)

// 👉 Ok(Response { url: Url { scheme: "https", cannot_be_a_base: false, username: "", password: None, host: Some(Domain("api.spotify.com")), port: None, path: "/v1/search", query: None, fragment: None }, status: 401, headers: {"www-authenticate": "Bearer realm=\"spotify\"", "access-control-allow-origin": "*", "access-control-allow-headers": "Accept, App-Platform, Authorization...

```

嗯，我们收到的数据比预期的多。怎么会这样这是因为任何请求的初始响应都将是服务器的直接`Response`对象。

如果您曾经在浏览器中使用过检查器工具来观察网络活动，您可能以前见过这些栏中的一些。`Status`是一个用于匹配错误条件的有用字段，例如，这将我们带到下一节。

### 匹配于`StatusCode`

您可能会希望根据响应分支到不同的行为。我们可以匹配请求的`StatusCode`:

```
match response.status() {
    reqwest::StatusCode::OK => {
        println!("Success! {:?}");
    },
    reqwest::StatusCode::UNAUTHORIZED => {
        println!("Need to grab a new token");
    },
    _ => {
        panic!("Uh oh! Something unexpected happened.");
    },
};

```

一旦我们稍后开始反序列化有效响应，这将被证明特别有用，因为与有效输出相比，错误通常具有不同的响应体。

### 解析响应正文

如果我们想要实际的响应体，我们需要链接第二个函数来指定我们正在解析的内容。让我们先尝试一下`.text()`字段，并将其作为明文处理:

```
#[tokio::main]
async fn main() {
    let response = reqwest::get("https://api.spotify.com/v1/search")
        .await
        // each response is wrapped in a `Result` type
        // we'll unwrap here for simplicity
        .unwrap()
        .text()
        .await;
    println!("{:?}", response);
}
// 👉 Ok("{\n  \"error\": {\n    \"status\": 401,\n    \"message\": \"No token provided\"\n  }\n}")

```

这里没什么可看的！因为我们没有提供身份验证令牌，所以我们的搜索查询遇到了 401，这应该属于前面的`match`中的`reqwest::StatusCode::UNAUTHORIZED`情况。

那么我们如何获得一个`200`状态响应呢？我们来谈谈头球。

## 将内容类型指定为标题

让我们为我们的查询添加几个标题:

*   `content_type`和`accept`来确认我们想要一个 JSON 形式的响应
*   `authorization`通过我们的 Spotify 帐户令牌

我们不能将这些头直接传递给`reqwest::get`。相反，我们需要使用`client`模块将我们的头链接在一起。让我们重构之前的查询，首先使用`client`:

```
#[tokio::main]
async fn main() {
    let client = reqwest::Client::new();
    let response = client
        .get("https://api.spotify.com/v1/search")
        // confirm the request using send()
        .send()
        .await
        // the rest is the same!
        .unwrap()
        .text()
        .await;
    println!("{:?}", response);
}

```

现在我们有了一个`client`，我们可以添加我们的头部配置，就像这样:

```
let response = client
    .get("https://api.spotify.com/v1/search")
    .header(AUTHORIZATION, "Bearer [AUTH_TOKEN]")
    .header(CONTENT_TYPE, "application/json")
    .header(ACCEPT, "application/json")
    .send()
    ...

```

`[AUTH_TOKEN]`是你账户的 OAuth 令牌([这里抓一个](https://developer.spotify.com/console/get-search-item/?q=Muse&type=track&market=US&limit=5&offset=5&include_external=))。如果我们现在记录我们的响应，我们应该看到…一个不同的错误状态！

我们应该得到一个错误 400 错误请求，而不是 401 授权错误。这是因为我们还没有指定要搜索的内容，所以让我们这样做:

```
let url = format!(
    "https://api.spotify.com/v1/search?q={query}&type=track,artist",
    // go check out her latest album. It's 🔥
    query = "Little Simz"
);
// the rest is the same as before!
let client = reqwest::Client::new();
let response = client
    .get(url)
    .header(AUTHORIZATION, "Bearer [AUTH_TOKEN]")
    .header(CONTENT_TYPE, "application/json")
    .header(ACCEPT, "application/json")
    .send()
    .await
    .unwrap();
println!("Success! {:?}", response)
// 👉 Ok("{\n  \"artists\" : {\n    \"href\" : \"https://api.spotify.com/v1/search?query=Lil+Simz&type=artist&offset=0&limit=20\",\n    \"items\" : [ {\n      \"external_urls\"...

```

## 反序列化到 JSON

现在我们已经收到了大量的搜索结果。我们如何把它变成一个有用的数据结构呢？

好吧，让我们使用嵌套的`structs`来建模我们想要接收的数据。根据您的使用情况，结构会有很大的不同，但我们需要 Spotify 搜索结果的模型如下:

```
use serde::{Deserialize, Serialize};

#[derive(Serialize, Deserialize, Debug)]
struct ExternalUrls {
    spotify: String,
}
#[derive(Serialize, Deserialize, Debug)]
struct Artist {
    name: String,
    external_urls: ExternalUrls,
}
#[derive(Serialize, Deserialize, Debug)]
struct Album {
    name: String,
    artists: Vec<Artist>,
    external_urls: ExternalUrls,
}
#[derive(Serialize, Deserialize, Debug)]
struct Track {
    name: String,
    href: String,
    popularity: u32,
    album: Album,
    external_urls: ExternalUrls,
}
#[derive(Serialize, Deserialize, Debug)]
struct Items<T> {
    items: Vec<T>,
}
#[derive(Serialize, Deserialize, Debug)]
struct APIResponse {
    tracks: Items<Track>,
}

```

注意:在撰写本文时，Rust 不支持嵌套结构，所以我们将把每一级分解成单独命名的可序列化结构。

您还会注意到`Serialize`派生，它允许 Reqwest 通过 [Serde](https://serde.rs/) 将原始 API 响应转换为 Rust 友好类型。确保将 Serde 添加为项目依赖项，如下所示:

```
[dependencies]
...
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"

```

现在我们准备解析我们的响应。我们应该只在 API 以 200 状态响应时尝试解析。否则，我们将遇到解析错误消息到我们的`APIResponse`结构的问题。

让我们根据状态代码恢复前面的`match`语法:

```
let response = client
    .get(url)
    ...
match response.status() {
    reqwest::StatusCode::OK => {
        // on success, parse our JSON to an APIResponse
        match response.json::<APIResponse>().await {
            Ok(parsed) => println!("Success! {:?}", parsed),
            Err(_) => println!("Hm, the response didn't match the shape we expected."),
        };
    }
    reqwest::StatusCode::UNAUTHORIZED => {
        println!("Need to grab a new token");
    }
    other => {
        panic!("Uh oh! Something unexpected happened: {:?}", other);
    }
};

```

幸运的话，我们应该得到一个可以使用的 API 输出。我们还通过缩小结构上的键来过滤掉 API 响应中我们不感兴趣的键。

如果您习惯于 JavaScript 和 TypeScript，其中可能会出现意外的对象键，那么这应该是一个受欢迎的附加功能！

## 创建 CLI 客户端

让我们将这些数据转化为人类可读的东西。

我们会在我们的足迹上运行一个基本的地图:

```
fn print_tracks(tracks: Vec<&Track>) {
    for track in tracks {
        println!("🔥 {}", track.name);
        println!("💿 {}", track.album.name);
        println!(
            "🕺 {}",
            track
                .album
                .artists
                .iter()
                .map(|artist| artist.name.to_string())
                .collect::<String>()
        );
        println!("🌎 {}", track.external_urls.spotify);
        println!("---------")
    }
}

```

然后在我们的`match`中调用这个打印函数:

```
match response.status() {
    reqwest::StatusCode::OK => {
        match response.json::<APIResponse>().await {
            Ok(parsed) => print_tracks(parsed.tracks.items.iter().collect()),
            Err(_) => println!("Hm, the response didn't match the shape we expected."),
        };
    }
...

```

我们还应该接受您希望作为 CLI 参数的任何查询:

```
let args: Vec<String> = env::args().collect();
let search_query = &args[1];
let url = format!(
    "https://api.spotify.com/v1/search?q={query}&type=track,artist",
    query = search_query
);
...

```

现在，我们可以从我们的终端运行这样的搜索输出:

```
cargo run "Little Simz"
     Running `target/debug/spotify-search`
🔥 Venom
💿 Venom
🕺 Little Simz
🌎 https://open.spotify.com/track/4WaaWczlVb1UJ24LILsR4C
---------
🔥 Fear No Man
💿 Sometimes I Might Be Introvert
🕺 Little Simz
🌎 https://open.spotify.com/track/6bLkNijhsnr1MWYrO6XnRz
---------
🔥 Venom
💿 GREY Area
🕺 Little Simz
🌎 https://open.spotify.com/track/3A0ITFj6kbb9CggwtPe55f
---------
...

```

就是这样！

## 结论

希望本教程展示了 Reqwest 库是多么强大和简单。如果您需要一些扩展阅读，Reqwest 的文档提供了一些有价值的深入研究的示例，包括:

如果你想探索成品，你可以在 GitHub 上找到[这个 Spotify 搜索！](https://github.com/Holben888/spotify-search)

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。