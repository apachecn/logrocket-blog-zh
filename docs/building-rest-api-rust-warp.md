# 用 warp - LogRocket 博客在 Rust 中构建 REST API

> 原文：<https://blog.logrocket.com/building-rest-api-rust-warp/>

***编者按:**这个用 warp 在 Rust 中构建 REST API 的指南最后一次更新是在 2023 年 2 月 8 日，以反映 Rust 框架的最新变化。这次更新还包括了关于锈中翘曲的好处的新章节。*

Rust 是很多人最喜欢的编程语言，但是仍然很难找到一个关于它的项目，甚至很难掌握它。开始学习任何语言的一个好方法是构建一些你每天都会用到的东西。如果你的公司运营微服务，那就更容易了。Rust 非常适合取代这样的服务，你可以在几天内重写它。

从 Rust 开始，你需要[学习基础知识](https://blog.logrocket.com/getting-up-to-speed-with-rust/)。一旦熟悉了语法和基本概念，就可以开始考虑[异步 Rust](https://blog.logrocket.com/a-practical-guide-to-async-in-rust/) 了。大多数现代语言都有一个内置的运行时来处理异步任务，比如发送请求或在后台等待应答。

在 Rust 中，你必须选择一个适合你的异步运行时。库通常有自己的运行时。如果您从事一个较大的项目，您可能希望避免添加多个运行时，因为通过选择一个单一的、一致的运行时，您可以简化您的应用程序架构，并降低应用程序的复杂性和兼容性问题的风险。

Tokio 是生产中使用最多的、经过验证的可以处理异步任务的运行时，所以你未来的雇主很可能已经在使用它了。因此，您的选择有些有限，因为您可能需要选择一个已经内置了 Tokio 的库来创建您的 API。对于本教程，我们将使用[扭曲](https://github.com/seanmonstar/warp)。根据您以前的编程经验，可能需要几天时间才能理解。但是一旦你理解了 warp，它可以成为构建 API 的一个非常好的工具。

*让我们开始吧:*

## 什么是翘曲？

Warp 是一个最小且高效的 web 框架，用于在 Rust 中构建基于 HTTP 的 web 服务。它为构建 HTTP 服务器提供了一个高级 API，关注安全性、性能和稳定性。Warp 还包括一些内置特性，比如支持 HTTP/1 和 HTTP/2、TLS 加密、异步编程以及用于日志记录、速率限制和路由任务的通用中间件。它的许多功能都是从 [Hyper](https://hyper.rs/) 借来的，因为 warp 更像是 Hyper 的超集。

## 设置您的项目

您需要安装以下库来跟随本教程。

*   创建 API 的 warp
*   Tokio 运行异步服务器
*   Serde 帮助序列化传入的 JSON
*   为您的本地存储创建一个`ReadWriteLock`

首先，用 cargo 创建一个新项目:

```
cargo new neat-api --bin

```

我们已经在`Cargo.toml`中包含了 warp，所以我们可以在我们的代码库中使用它:

```
 [dependencies]
warp = "0.2"
parking_lot = "0.10.0"
serde = { version = "1.0", features = ["derive"] }
tokio = { version = "0.2", features = ["macros"] } 
```

对于第一个测试，创建一个简单的“Hello，World！”进入`main.rs`，如下图:
<

```
use warp::Filter;

#[tokio::main]
async fn main() {
    // GET /hello/warp => 200 OK with body "Hello, warp!"
    let hello = warp::path!("hello" / String)
        .map(|name| format!("Hello, {}!", name));

    warp::serve(hello)
        .run(([127, 0, 0, 1], 3030))
        .await;
}

```

是一种解析请求并根据我们创建的路由进行匹配的方法。因此，当你通过`cargo run`启动服务器并将浏览器指向`localhost:3030/hello/WHATEVER`时，warp 通过其过滤器发送这个请求，并执行第一个被触发的请求。

在`let hello = …`中，我们创建了一个新的路径，本质上就是说每个带有路径`/hello`和一个字符串的请求都由这个方法处理。所以，我们返回`Hello, WHATEVER`。
如果我们将浏览器指向`localhost:3030/hello/new/WHATEVER`，我们将得到 404，因为我们没有针对`/hello/new + String`的过滤器。

## 构建 REST API

让我们构建一个真实的 API 来演示这些概念。一个好的模型是用于购物清单的 API。我们希望能够向列表中添加项目，更新数量，删除项目，并查看整个列表。因此，我们需要四个不同的路由，分别使用 [HTTP 方法](https://blog.logrocket.com/making-http-requests-rust-reqwest/) `GET`、`DELETE`、`PUT`和`POST`。有这么多不同的路径，为每一个创建方法而不是在`main.rs`中处理它们是明智的吗？

### 创建本地存储

除了路由，我们还需要在文件或局部变量中存储一个状态。在[异步环境](https://blog.logrocket.com/pinning-rust-async-data-types-memory-safety/)中，我们必须确保一次只有一个方法可以访问存储，这样线程之间就不会有不一致。

在 Rust 中，我们有`Arc`，所以编译器知道何时丢弃一个值和一个读写锁(`RwLock`)。这样，不同线程上的两个方法就不会写入同一个内存。
您的商店实现应该是这样的:
T4

```
use parking_lot::RwLock;
use std::collections::HashMap;
use std::sync::Arc;

type Items = HashMap<String, i32>;

#[derive(Debug, Deserialize, Serialize, Clone)]
struct Item {
    name: String,
    quantity: i32,
}

#[derive(Clone)]
struct Store {
  grocery_list: Arc<RwLock<Items>>
}

impl Store {
    fn new() -> Self {
        Store {
            grocery_list: Arc::new(RwLock::new(HashMap::new())),
        }
    }
}

```

### 将一个项目添加到列表中

现在，我们可以添加我们的第一条路线。要向列表中添加项目，需要向路径发出 HTTP `POST`请求。我们的方法必须返回一个正确的 HTTP 代码，以便调用者知道他们的调用是否成功。Warp 通过自己的`http`库提供基本类型，我们也需要包含这些类型。像这样添加:


```
use warp::{http, Filter};

```

用于`POST`请求的方法如下:
<

```
async fn add_grocery_list_item(
    item: Item,
    store: Store
    ) -> Result<impl warp::Reply, warp::Rejection> {
        let r = store.grocery_list.read();
        Ok(warp::reply::json(&*r))
}

```

warp 框架提供了选择`reply with status`的选项，所以我们可以添加文本和一个通用的 HTTP 状态，这样调用者就知道请求是否成功，或者他们是否必须再试一次。现在，添加一个新的路由，并调用刚刚为它创建的方法。因为您可以期待一个 JSON，所以您应该创建一个小的`json_body`助手函数来从 HTTP 请求的`body`中提取出`Item`。

此外，我们需要通过克隆它并创建一个`warp filter`来将存储传递给每个方法，在`warp path`创建期间，我们在`.and()`中调用这个方法:
<

```
fn json_body() -> impl Filter<Extract = (Item,), Error = warp::Rejection> + Clone {
    // When accepting a body, we want a JSON body
    // (and to reject huge payloads)...
    warp::body::content_length_limit(1024 * 16).and(warp::body::json())
}

#[tokio::main]
async fn main() {
    let store = Store::new();
    let store_filter = warp::any().map(move || store.clone());

    let add_items = warp::post()
        .and(warp::path("v1"))
        .and(warp::path("groceries"))
        .and(warp::path::end())
        .and(json_body())
        .and(store_filter.clone())
        .and_then(add_grocery_list_item);

    warp::serve(add_items)
        .run(([127, 0, 0, 1], 3030))
        .await;
}

```

你可以通过`curl`或者像[邮差](https://blog.logrocket.com/how-automate-api-tests-postman/)这样的应用程序来测试`POST`调用，它现在是一个独立的 HTTP 请求应用程序。通过`cargo run`启动服务器，打开另一个终端窗口或标签，执行下面的`curl` :
<

```
curl --location --request POST 'localhost:3030/v1/groceries' \
--header 'Content-Type: application/json' \
--header 'Content-Type: text/plain' \
--data-raw '{
        "name": "apple",
        "quantity": 3
}'

```

您应该得到方法中定义的文本响应和 HTTP 代码。

### 购物清单

现在，我们可以将商品列表发布到我们的杂货列表中，但是我们仍然无法检索它们。我们需要为`GET`请求创建另一个路由。我们的主要功能是添加这条新路线。对于这条新路线，我们不需要解析任何 JSON。代码如下:

```
#[tokio::main]
async fn main() {
    let store = Store::new();
    let store_filter = warp::any().map(move || store.clone());

    let add_items = warp::post()
        .and(warp::path("v1"))
        .and(warp::path("groceries"))
        .and(warp::path::end())
        .and(json_body())
        .and(store_filter.clone())
        .and_then(add_grocery_list_item);

    let get_items = warp::get()
        .and(warp::path("v1"))
        .and(warp::path("groceries"))
        .and(warp::path::end())
        .and(store_filter.clone())
        .and_then(get_grocery_list);

    let routes = add_items.or(get_items);

    warp::serve(routes)
        .run(([127, 0, 0, 1], 3030))
        .await;
}

```

当您检查您的`Arc`背后的数据结构时，您将体验到异步 Rust 的味道。您需要使用`.read()`方法来访问和取消引用数据。该函数如下所示:

```
async fn get_grocery_list(
    store: Store
    ) -> Result<impl warp::Reply, warp::Rejection> {
         let result = store.grocery_list.read();
        Ok(warp::reply::json(&*result))
}

```

然后，为`store.grocery_list.read()`创建一个变量，我们称之为`result`。注意我们正在返回`&*result;`这是新的，对吗？是的，`&*result`将`[RwLockReadGuard](https://doc.rust-lang.org/std/sync/struct.RwLockReadGuard.html)`对象`result`解引用到一个`&HashMap`，然后作为对已经返回的`warp::reply::json`函数的引用传递。

### `UPDATE`和`DELETE`

最后两个缺失的方法是`UPDATE`和`DELETE`。对于`DELETE`，你几乎可以复制你的`add_grocery_list_item`，而不是`.insert()`，`[.remove()](https://doc.rust-lang.org/std/collections/struct.HashMap.html#method.remove)`一个词条。

一个特例是`UPDATE`。这里 Rust `HashMap`实现也使用了`.insert()`，但是如果键不存在，它会更新值而不是创建一个新条目。因此，只需重命名该方法，并为`POST`和`PUT`调用它。

对于`DELETE`方法，您只需要传递项目的名称，因此创建一个新的 struct 并为新类型添加另一个`parse_json()`方法。重命名第一个解析方法，并添加另一个方法。

您可以简单地将您的`add_grocery_list_item`方法重命名为`update_grocery_list`，并为一个`warp::post()`和`warp::put()`调用它。您的完整代码应该如下所示:

```
use warp::{http, Filter};
use parking_lot::RwLock;
use std::collections::HashMap;
use std::sync::Arc;
use serde::{Serialize, Deserialize};

type Items = HashMap<String, i32>;

#[derive(Debug, Deserialize, Serialize, Clone)]
struct Id {
    name: String,
}

#[derive(Debug, Deserialize, Serialize, Clone)]
struct Item {
    name: String,
    quantity: i32,
}

#[derive(Clone)]
struct Store {
  grocery_list: Arc<RwLock<Items>>
}

impl Store {
    fn new() -> Self {
        Store {
            grocery_list: Arc::new(RwLock::new(HashMap::new())),
        }
    }
}

async fn update_grocery_list(
    item: Item,
    store: Store
    ) -> Result<impl warp::Reply, warp::Rejection> {
        store.grocery_list.write().insert(item.name, item.quantity);

        Ok(warp::reply::with_status(
            "Added items to the grocery list",
            http::StatusCode::CREATED,
        ))
}

async fn delete_grocery_list_item(
    id: Id,
    store: Store
    ) -> Result<impl warp::Reply, warp::Rejection> {
        store.grocery_list.write().remove(&id.name);

        Ok(warp::reply::with_status(
            "Removed item from grocery list",
            http::StatusCode::OK,
        ))
}

async fn get_grocery_list(
    store: Store
    ) -> Result<impl warp::Reply, warp::Rejection> {
        let r = store.grocery_list.read();
        Ok(warp::reply::json(&*r))
}

fn delete_json() -> impl Filter<Extract = (Id,), Error = warp::Rejection> + Clone {
    // When accepting a body, we want a JSON body
    // (and to reject huge payloads)...
    warp::body::content_length_limit(1024 * 16).and(warp::body::json())
}

fn post_json() -> impl Filter<Extract = (Item,), Error = warp::Rejection> + Clone {
    // When accepting a body, we want a JSON body
    // (and to reject huge payloads)...
    warp::body::content_length_limit(1024 * 16).and(warp::body::json())
}

#[tokio::main]
async fn main() {
    let store = Store::new();
    let store_filter = warp::any().map(move || store.clone());

    let add_items = warp::post()
        .and(warp::path("v1"))
        .and(warp::path("groceries"))
        .and(warp::path::end())
        .and(post_json())
        .and(store_filter.clone())
        .and_then(update_grocery_list);

    let get_items = warp::get()
        .and(warp::path("v1"))
        .and(warp::path("groceries"))
        .and(warp::path::end())
        .and(store_filter.clone())
        .and_then(get_grocery_list);

    let delete_item = warp::delete()
        .and(warp::path("v1"))
        .and(warp::path("groceries"))
        .and(warp::path::end())
        .and(delete_json())
        .and(store_filter.clone())
        .and_then(delete_grocery_list_item);

    let update_item = warp::put()
        .and(warp::path("v1"))
        .and(warp::path("groceries"))
        .and(warp::path::end())
        .and(post_json())
        .and(store_filter.clone())
        .and_then(update_grocery_list);

    let routes = add_items.or(get_items).or(delete_item).or(update_item);

    warp::serve(routes)
        .run(([127, 0, 0, 1], 3030))
        .await;
}

```

## 了解测试卷曲

更新代码后，通过`cargo run`重启服务器，并使用这些 curls 来发布、更新、获取和删除项目。

### `POST`

```
curl --location --request POST 'localhost:3030/v1/groceries' \
--header 'Content-Type: application/json' \
--header 'Content-Type: text/plain' \
--data-raw '{
        "name": "apple",
        "quantity": 3
}'

```

### `UPDATE`

```
curl --location --request PUT 'localhost:3030/v1/groceries' \
--header 'Content-Type: application/json' \
--header 'Content-Type: text/plain' \
--data-raw '{
        "name": "apple",
        "quantity": 5
}'

```

### `GET`

```
curl --location --request GET 'localhost:3030/v1/groceries' \
--header 'Content-Type: application/json' \
--header 'Content-Type: text/plain'

```

### `DELETE`

```
curl --location --request DELETE 'localhost:3030/v1/groceries' \
--header 'Content-Type: application/json' \
--header 'Content-Type: text/plain' \
--data-raw '{
        "name": "apple"
}'

```

总结一下我们刚刚讲述的步骤:

*   为每个项目创建一个 ID，以便您可以通过`/v1/groceries/{id}`进行更新和删除
*   添加 404 路线
*   为格式错误的 JSON 添加错误处理
*   调整每条路线的返回消息
*   为每条有弯曲的路线添加测试

## 为什么在 Rust 中使用 warp

在 Rust 中构建 API 时，您有几个库选项。但是，项目的具体要求将有助于指导您的选择。如果你决定使用 warp，这里有一些在 Rust 项目中使用它的好处。

1.  性能:warp 被设计为快速高效，侧重于异步处理和性能优化特性，比如自动 HTTP 保持活动和连接池
2.  安全性:warp 非常重视安全性，内置 TLS 加密支持等功能可确保您的数据在网络上安全传输
3.  简单性:warp 提供了一个易于使用的高级 API，但仍然是强大的和可定制的。这使得开始构建 HTTP 服务器变得简单，并且可以根据需要用额外的功能轻松地扩展您的应用程序
4.  健壮性:warp 被设计得稳定可靠，重点是错误处理和报告
5.  可伸缩性:warp 被设计成可伸缩的，支持 HTTP/1 和 HTTP/2 以及高效的资源利用，这使得它成为构建高性能和可伸缩的 web 应用程序的绝佳选择

## 最后的想法

Warp 是一个用 Rust 构建 web APIs 的有趣工具。尽管代码远非完美，样本代码还是给了我们 warp 的冰山一角。我们可以延长它，我希望你这样做。我很乐意看到你基于你已经建立的反馈。

您可以添加一些内容:

*   错误处理
*   单元测试
*   统一的 JSON 响应

您可以看到用 Rust 和 warp 创建您的第一个 REST API 是多么简单，Rust type 系统是如何明确您正在处理什么数据以及您可以使用什么方法的。现在就看你自己磨练技巧，优化代码了。你可以在 [GitHub](https://github.com/gruberb/warp-examaple-api) 找到完整的源代码。请随意克隆、试验和改进它。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。