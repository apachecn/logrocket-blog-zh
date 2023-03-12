# 用 Chrono-TZ - LogRocket 博客处理 Rust 中的时区

> 原文：<https://blog.logrocket.com/timezone-handling-in-rust-with-chrono-tz/>

当在现代 web 应用程序中处理日期和时间时——尤其是为来自世界各地的用户提供服务的应用程序——处理时区可能会成为一个问题。

根据不同的用例，时区可能会相当棘手。快速浏览一下 [IANA 时区数据库](https://www.iana.org/time-zones)及其发布历史，您会注意到另一个问题:时区可以改变。仅仅在过去的五年里，朝鲜、俄罗斯、海地、智利等地就发生了时区变化。其中一些变化包括增加或取消夏令时，其他的基于不同的因素。

如果您需要在应用程序中使用准确的时区，保持时区的最新是非常重要的。例如，假设您想在某个时间点向所有用户发送一封电子邮件或通知。根据国家通知的时间和数据库变化传播的速度，可能会有一段时间你的应用程序中的时区完全错误。

在本教程中，我们将向您展示如何使用 [Chrono](https://github.com/chronotope/chrono) 和 [Chrono-TZ](https://github.com/chronotope/chrono-tz) 库来处理 Rust 中的日期和时区。Chrono 是处理 Rust 中的日期和时间的首选机箱，而 Chrono-TZ 是处理时区的扩展。

我们将构建一个简单的 web 服务，使用户能够以 [RFC3339](https://www.ietf.org/rfc/rfc3339.txt) 格式(“1996-12-19T16:39:57+02:00”)添加日期，其中包括时区。然后，这些日期被保存在内存中，用户可以获取它们并将其转换为自己选择的时区。

在构建这个应用程序时，我们将演示如何在 Rust 中解析和格式化日期，以及如何解析和转换时区。

## 设置

接下来，您需要一个最新的 Rust 安装(1.39+)和一个发送 HTTP 请求的工具，比如 cURL。

首先，创建一个新的 Rust 项目。

```
cargo new rust-timezone-example
cd rust-timezone-example

```

接下来，编辑`Cargo.toml`文件并添加必要的依赖项。

```
[dependencies]
tokio = { version = "0.2", features = ["macros", "rt-threaded", "sync"] }
warp = "0.2"
chrono = { version = "0.4", features = ["serde"] }
chrono-tz = "0.5"
serde = {version = "1.0", features = ["derive"] }
serde_json = "1.0"

```

该服务将使用下面的 tokio 用 warp 编写。我们将使用`serde`来反序列化传入的请求负载。

## 数据存储

我们将使用共享数据向量形式的内存数据存储机制。

```
use chrono::prelude::*;
use tokio::sync::RwLock;
use std::sync::Arc;

type Dates = Arc<RwLock<Vec<DateTime<Utc>>>>;

```

类型描述了我们的数据存储。它前面有一个有点吓人的类型列表，但实际上没什么特别的。

让我们从里到外。我们将在`UTC`中保存日期。这样，我们就不必跟踪即将到来的日期的时区；我们可以简单地在它们之间转换。`DateTime`和`Utc`类型来自 Chrono 的前奏。

我们想要保存一个日期列表，所以这些日期被放入一个`Vec`中。因为我们将使用它作为异步 web 服务的数据存储，所以我们可能会从不同的线程中读取和写入这个数据存储，可能是在同一时间。出于这个原因，我们将在它周围放置一个来自 tokio 运行时的 async `RwLock`,这样任何想要访问它的人都必须获得一个锁。一个`RwLock`授予多个读者一个锁，但是一次只能有一个写锁处于活动状态。这保证了我们不会有任何数据竞争。

最后，我们将全部内容放在一个`Arc`——一个原子引用计数的智能指针——中，这样我们就可以以一种内存安全的方式跨线程共享它。

## 网络服务器

下一步是建立一个基本的 web 服务，并将其与这个临时的数据存储连接起来。先说`main`中的 warp 服务器。

```
use warp::{http::StatusCode, reply, Filter, Rejection, Reply};
use std::convert::Infallible;

#[tokio::main]
async fn main() {
    let dates: Dates = Arc::new(RwLock::new(Vec::new()));

    let create_route = warp::path("create")
        .and(warp::post())
        .and(with_dates(dates.clone()))
        .and(warp::body::json())
        .and_then(create_handler);
    let fetch_route = warp::path("fetch")
        .and(warp::get())
        .and(warp::path::param())
        .and(with_dates(dates.clone()))
        .and_then(fetch_handler);

    println!("Server started at localhost:8080");
    warp::serve(create_route.or(fetch_route))
        .run(([0, 0, 0, 0], 8080))
        .await;
}

fn with_dates(dates: Dates) -> impl Filter<Extract = (Dates,), Error = Infallible> + Clone {
    warp::any().map(move || dates.clone())
}

```

我们创建两条路线，一条是到`POST /create`的，一条是到`GET /fetch/$timezone`的。在这两种情况下，我们通过一个名为`with_dates`的扭曲过滤器将在`main`开始时初始化的`dates`数据存储传递给处理程序，该过滤器只是将一个原子引用复制到处理程序中。

`create`端点也接受类型为`DateTimeRequest`的有效负载。

```
use serde::Deserialize;

#[derive(Deserialize)]
struct DateTimeRequest {
    date_time: String,
}

```

最后，我们需要为这些路由定义两个占位符处理函数。

```
type Result<T> = std::result::Result<T, Rejection>;

async fn create_handler(dates: Dates, body: DateTimeRequest) -> Result<impl Reply> {
    Ok("")
}

async fn fetch_handler(time_zone: String, dates: Dates) -> Result<impl Reply> {
    Ok("")
}

```

这些还没有做任何有趣的事情，但这是我们接下来要解决的问题。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 创建日期

对于用于创建日期的端点，第一步是将传入的`body.date_time`字符串解析为有效的日期。然后，如果成功了，我们将把日期转换成 UTC，并把它推送到数据存储，返回一个成功消息。

让我们来看看这个翻译成 Rust 的代码。

```
async fn create_handler(dates: Dates, body: DateTimeRequest) -> Result<impl Reply> {
    let dt: DateTime<FixedOffset> = match DateTime::parse_from_rfc3339(&body.date_time) {
        Ok(v) => v,
        Err(e) => {
            return Ok(reply::with_status(
                format!("could not parse date: {}", e),
                StatusCode::BAD_REQUEST,
            ))
        }
    };

    dates.write().await.push(dt.with_timezone(&Utc));

    Ok(reply::with_status(
        format!("Added date with timezone: {} as UTC", dt.timezone()),
        StatusCode::OK,
    ))
}

```

我们使用 Chrono 的`DateTime::parse_from_rfc3339`函数来解析日期。这里出来的是一个`DateTime<FixedOffset`。偏移量描述了时区。在 Chrono 中，还有更多的方法来解析任意的用户定义格式的日期。

如果失败，我们将返回一个错误。否则，我们使用`dates.write().await`获得数据存储的写锁，并将转换为`UTC`的日期推送到日期向量。

最后，我们向用户返回一条成功消息，向他们显示从传入日期开始解析的时区。

## 获取日期

要获取所有保存的日期，用户可以使用`GET /fetch/$timezone`端点，其中`$timezone`可能是类似于`UTC`、`GMT`、`Africa/Algiers`或任意[多个时区](https://docs.rs/chrono-tz/0.5.3/chrono_tz/enum.Tz.html)的 Chrono-TZ 和 IANA 支持。

对于像`Europe/Vienna`这样的情况，当调用端点时，调用者需要对`/`到`%2F`进行 url 编码，我们必须再次对其进行 url 解码。对于这个基本示例，我们将做一个简单的替换来处理这种情况。

下一步是解析即将到来的时区，如果它是一个有效的时区，那么将所有保存的日期转换成这个时区后返回给用户。

让我们来看一个可能的解决方案:

```
async fn fetch_handler(time_zone: String, dates: Dates) -> Result<impl Reply> {
    let parsed_time_zone = time_zone.replace("%2F", "/");
    let tz: Tz = match parsed_time_zone.parse() {
        Ok(v) => v,
        Err(e) => {
            return Ok(reply::with_status(
                format!("could not parse timezone: {}", e),
                StatusCode::BAD_REQUEST,
            ))
        }
    };

    Ok(
        match serde_json::to_string(
            &dates
                .read()
                .await
                .iter()
                .map(|t: &DateTime<Utc>| t.with_timezone(&tz).to_rfc3339())
                .collect::<Vec<_>>(),
        ) {
            Ok(v) => reply::with_status(v, StatusCode::OK),
            Err(e) => {
                return Ok(reply::with_status(
                    format!("could not serialize json: {}", e),
                    StatusCode::INTERNAL_SERVER_ERROR,
                ))
            }
        },
    )
}

```

在我们的替换策略之后，我们使用`.parse()`函数将传入的字符串解析为`chrono_tz::Tz`。如果失败，我们将返回一个错误。

否则，下一步是使用`dates.read().await`获取数据存储上的读锁，然后迭代向量，使用`.with_timezone`助手将日期内的 UTC 日期映射到给定的时区。

最后，我们将日期转换为 RFC3339 日期字符串，以与输入保持一致。然后，生成的向量被序列化为 JSON 并返回给用户。

就是这样！让我们看看它是否如我们预期的那样工作。

首先，使用`cargo run`运行服务器，然后使用 cURL 创建一个日期。

```
curl -X POST http://localhost:8080/create -d '{"date_time": "1996-12-19T16:39:57+02:00"}' -H "content-type: application/json"
Added date with timezone: +02:00 as UTC

```

到目前为止，一切顺利。让我们先用 UTC 取它。

```
curl http://localhost:8080/fetch/UTC
["1996-12-19T14:39:57+01:00"]

```

然后在另一个时区:非洲/阿尔及尔，也就是 UTC+01:00。

```
curl http://localhost:8080/fetch/Africa%2FAlgiers
["1996-12-19T15:39:57+00:00"]

```

非常好！我们在 UTC+02:00 保存了一个日期，然后用少了两个小时的 UTC 和少了一个小时的 UTC+01:00`Africa/Algiers`获取。

您可以尝试不同时区的组合，从中获得乐趣。查看 GitHub 上的完整示例代码。

## 结论

时间和日期的处理，尤其是对于时区，是一件棘手的事情。幸运的是，Rust 的生态系统为我们提供了所有需要的工具。

Chrono 库是健壮的、完整的和广泛使用的，它们有很好的 API。诸如此类的板条箱使得处理时区的技术方面不成问题。如果不考虑奇怪的 API、不一致和 bug，处理与日期和时间相关的实际业务问题已经够困难的了。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。