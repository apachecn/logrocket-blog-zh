# 在 Rust web 服务中使用 MongoDB

> 原文：<https://blog.logrocket.com/using-mongodb-in-a-rust-web-service/>

在之前的一篇文章中，我们介绍了如何用 Postgres 在 Rust 中创建一个 web API。这一次，我们将看看如何用 [MongoDB](https://www.mongodb.com/) 创建一个类似的应用程序，这是一个非常流行和广泛使用的文档数据库。

这个应用程序的底层基础设施将与前面提到的文章中的基础设施相同，以展示在 Rust web 服务中从关系数据库切换到对象数据库时的相似之处和不同之处。

幸运的是，MongoDB 可以与官方的 MongoDB Rust 库同步或异步使用，这意味着这个示例也将是完全异步的。我们将通过为书籍构建一个 CRUD API 来演示这个概念。

## 设置

接下来，您所需要的只是一个相当新的 Rust 安装(1.39+)。启动本地 MongoDB 实例的方法，如 [Docker](https://www.docker.com/) ，以及发送 HTTP 请求的工具，如 [curl](https://curl.haxx.se/) 或 [Postman](https://www.postman.com/) ，也会很有用。

首先，创建一个新的 Rust 项目。

```
cargo new rust-web-mongodb-example
cd rust-web-mongodb-example

```

接下来，编辑`Cargo.toml`文件并添加您需要的依赖项。

```
[dependencies]
tokio = { version = "0.2", features = ["macros", "rt-threaded"] }
warp = "0.2"
serde = {version = "1.0", features = ["derive"] }
thiserror = "1.0"
chrono = { version = "0.4", features = ["serde"] }
futures = { version = "0.3.4", default-features = false, features = ["async-await"]}
mongodb = "1.0.0"

```

对于这个例子，我们将使用官方的 [Rust MongoDB 驱动](https://github.com/mongodb/mongo-rust-driver)。web 应用基于 [warp](https://github.com/seanmonstar/warp) ，我们会添加几个助手库，比如处理序列化的`serde`，处理时间的`chrono`，处理错误的`thiserror`。

## 结构

为了构建这个小的示例应用程序，我们将创建三个模块:

1.  `db` —数据库访问逻辑
2.  `error` —错误和 HTTP 错误响应处理
3.  `handler` — CRUD HTTP 处理程序

让我们从数据库访问开始，一步步向上。我们将在最后把所有东西连接在一起。

## 数据库访问

首先，让我们为数据库访问创建一个小的抽象，我们可以在应用程序中传递它。

```
use mongodb::bson::{doc, document::Document, oid::ObjectId, Bson};
use mongodb::{options::ClientOptions, Client, Collection};

const DB_NAME: &str = "booky";
const COLL: &str = "books";

const ID: &str = "_id";
const NAME: &str = "name";
const AUTHOR: &str = "author";
const NUM_PAGES: &str = "num_pages";
const ADDED_AT: &str = "added_at";
const TAGS: &str = "tags";

#[derive(Clone, Debug)]
pub struct DB {
    pub client: Client,
}

impl DB {
...
}

```

接下来，我们需要建立一种初始化 MongoDB 客户机的方法。

```
impl DB {
    pub async fn init() -> Result<Self> {
        let mut client_options = ClientOptions::parse("mongodb://127.0.0.1:27017").await?;
        client_options.app_name = Some("booky".to_string());
        Ok(Self {
            client: Client::with_options(client_options)?,
        })
    }
}

```

在 Rust 中创建 MongoDB 客户端非常简单。只需解析一个连接字符串来创建`ClientOptions`，设置一个应用程序名，并用选项初始化客户机。

让我们来看看如何进行一个简单的查询(在数据库中查找所有书籍)。

```
    pub async fn fetch_books(&self) -> Result<Vec<Book>> {
        let mut cursor = self
            .get_collection()
            .find(None, None)
            .await
            .map_err(MongoQueryError)?;

        let mut result: Vec<Book> = Vec::new();
        while let Some(doc) = cursor.next().await {
            result.push(self.doc_to_book(&doc?)?);
        }
        Ok(result)
    }

    fn get_collection(&self) -> Collection {
        self.client.database(DB_NAME).collection(COLL)
    }

```

第一步是使用`get_collection()`助手选择一个`collection`。该操作实际上并不与数据库对话；它只是设置了我们想要操作的集合上下文。

接下来，对这个集合执行`find()`命令。在这种情况下，我们将传递 no `filter`和 no other `options`，因此两个参数都被设置为`None`。我们只是想要所有的东西。

等待`find`的结果给了我们一个`Cursor`，这是一个`Stream`。这个流可以被迭代。一旦用尽，如果还有更多数据，光标将自动获取下一个数据块，直到没有更多数据。

从这些文件到实际的——

```
#[derive(Serialize, Deserialize, Debug)]
pub struct Book {
    pub id: String,
    pub name: String,
    pub author: String,
    pub num_pages: usize,
    pub added_at: DateTime<Utc>,
    pub tags: Vec<String>,
}

```

—创建`doc_to_book`助手。

```
    fn doc_to_book(&self, doc: &Document) -> Result<Book> {
        let id = doc.get_object_id(ID)?;
        let name = doc.get_str(NAME)?;
        let author = doc.get_str(AUTHOR)?;
        let num_pages = doc.get_i32(NUM_PAGES)?;
        let added_at = doc.get_datetime(ADDED_AT)?;
        let tags = doc.get_array(TAGS)?;

        let book = Book {
            id: id.to_hex(),
            name: name.to_owned(),
            author: author.to_owned(),
            num_pages: num_pages as usize,
            added_at: *added_at,
            tags: tags
                .iter()
                .filter_map(|entry| match entry {
                    Bson::String(v) => Some(v.to_owned()),
                    _ => None,
                })
                .collect(),
        };
        Ok(book)
    }

```

这个助手试图根据我们希望文档表示的数据模型来解析返回文档的字段。当然，这些转换中的任何一个都可能失败。

这里有趣的是，我们可以在文档上使用不同的`get_$type(key)`函数将数据转换成我们需要的类型。比如官方的 MongoDB 库支持`chrono::DateTime<Utc>`用于日期，非常方便。所以我们可以简单地使用`doc.get_datetime(key)`将 MongoDB 中的日期/时间转换为`chrono`类型。

另一个有用的助手是`.get_array(key)`，顾名思义，它试图将给定键的值转换成一个`Vec`。然而，在这种情况下，我们需要确保值实际上是`Strings`，因为在 MongoDB 中，它们可以是不同的类型。

## CRUD 处理程序

处理程序相当简单；他们所做的就是接收一些输入并调用数据库抽象。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
#[derive(Serialize, Deserialize, Debug)]
pub struct BookRequest {
    pub name: String,
    pub author: String,
    pub num_pages: usize,
    pub tags: Vec<String>,
}

pub async fn books_list_handler(db: DB) -> WebResult<impl Reply> {
    let books = db.fetch_books().await.map_err(|e| reject::custom(e))?;
    Ok(json(&books))
}

pub async fn create_book_handler(body: BookRequest, db: DB) -> WebResult<impl Reply> {
    db.create_book(&body).await.map_err(|e| reject::custom(e))?;
    Ok(StatusCode::CREATED)
}

pub async fn edit_book_handler(id: String, body: BookRequest, db: DB) -> WebResult<impl Reply> {
    db.edit_book(&id, &body)
        .await
        .map_err(|e| reject::custom(e))?;
    Ok(StatusCode::OK)
}

pub async fn delete_book_handler(id: String, db: DB) -> WebResult<impl Reply> {
    db.delete_book(&id).await.map_err(|e| reject::custom(e))?;
    Ok(StatusCode::OK)
}

```

`BookRequest`类型是用于创建和编辑书籍的数据对象。每个处理程序都有一个通过`warp`过滤器传递给它的`DB`(下一节将详细介绍)。

除此之外，处理程序简单地调用数据库抽象中各自的功能，并以`HTTP 200 OK`的形式返回成功消息，或者在`books_list_handler`的情况下，返回现有书籍的列表。

在现实世界的应用程序中，我们不会在这里泄漏数据库对象；我们会将它转换成某种`BookResponse`对象。但是这种最简单的方法应该足以满足本教程的目的。

由于我们是自下而上地工作，下一个也是最后一个阶段是`main`，在这里我们将把所有东西放在一起，用 MongoDB 创建一个可工作的 Rust web 应用程序。

## 把所有的放在一起

在`main`中，我们将首先定义在`handler`和`db`中使用的共享`Result`类型。

```
type Result<T> = std::result::Result<T, error::Error>;
type WebResult<T> = std::result::Result<T, Rejection>;

```

这些只是为了区分内部结果和我们发送给客户的结果(`WebResult`)。

`error`模块，我们不会在这里深入讨论，只包含一个自定义的`Error`枚举，它实现了 warp 的`Reject`特征，因此我们可以处理内部错误并将自定义错误返回给客户端。

回到`main`，下一步是初始化数据库抽象并定义一些路由。

```
#[tokio::main]
async fn main() -> Result<()> {
    let db = DB::init().await?;

    let book = warp::path("book");

    let book_routes = book
        .and(warp::post())
        .and(warp::body::json())
        .and(with_db(db.clone()))
        .and_then(handler::create_book_handler)
        .or(book
            .and(warp::put())
            .and(warp::path::param())
            .and(warp::body::json())
            .and(with_db(db.clone()))
            .and_then(handler::edit_book_handler))
        .or(book
            .and(warp::delete())
            .and(warp::path::param())
            .and(with_db(db.clone()))
            .and_then(handler::delete_book_handler))
        .or(book
            .and(warp::get())
            .and(with_db(db.clone()))
            .and_then(handler::books_list_handler));

    let routes = book_routes.recover(error::handle_rejection);

    println!("Started on port 8080");
    warp::serve(routes).run(([0, 0, 0, 0], 8080)).await;
    Ok(())
}

```

这一部分完全符合你的预期。首先，初始化数据库包装器。然后，用各自的 HTTP 方法、参数和主体定义来定义 CRUD 路由。

`with_db`过滤器将 MongoDB 数据库包装器传递给处理程序。

```
fn with_db(db: DB) -> impl Filter<Extract = (DB,), Error = Infallible> + Clone {
    warp::any().map(move || db.clone())
}

```

在`main`结束时，实际的网络服务器被启动。

现在是考验的时候了！

使用 Docker 启动一个本地 MongoDB 实例。

```
docker run -d -p 27017:27017 -v `pwd`/data/db:/data/db --name bookydb mongo

```

这将在端口`27017`上启动 MongoDB，其数据目录设置为命令执行目录中的`data/db`文件夹。

创作一本书。

```
curl -X POST http://localhost:8080/book -d '{"name": "Good Book", "author": "gGeat Author", "num_pages": 500, "tags": ["fun", "exciting"]}' -H "content-type: application/json"

```

通过获取所有书籍来检查以确保其工作正常。

```
curl http://localhost:8080/book

[{"id":"5f19d95800065210009332ef","name":"Good Book","author":"gGeat Author","num_pages":500,"added_at":"2020-07-23T18:39:20.062Z","tags":["fun","exciting"]}]

```

现在我们来编辑这本书。

```
curl -X PUT http://localhost:8080/book/5f19d95800065210009332ef -d '{"name": "Great Book", "author": "Another Great Author", "num_pages": 320, "tags": ["boring", "long"]}' -H "content-type: application/json"

```

要检查它是否工作:

```
curl http://localhost:8080/book

[{"id":"5f19d95800065210009332ef","name":"Great Book","author":"Another Great Author","num_pages":320,"added_at":"2020-07-23T18:39:54.045Z","tags":["boring","long"]}]

```

最后，测试以确保书籍被成功删除。

```
curl -X DELETE http://localhost:8080/book/5f19d95800065210009332ef
curl http://localhost:8080/book

[]

```

太棒了。它完全按计划运行。

这个例子的完整代码可以在 [GitHub](https://github.com/zupzup/rust-web-mongodb-example) 上找到。

## 结论

这个用例是快速成熟的 Rust web 生态系统的又一个光辉例子。MongoDB 驱动程序看起来已经非常强大了，提供了对`tokio`和`async_std`运行时的支持，并提供了完整的同步和异步 API。

板条箱最近击中`1.0.0`并且被很好地记录。此外，API 是直观的，并且是主动维护的。总而言之，MongoDB 团队做得非常好。我期待着看到第一个 Rust 和 MongoDB 应用程序投入生产。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。