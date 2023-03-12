# 用 warp 在 Rust 中创建一个异步 CRUD web 服务

> 原文：<https://blog.logrocket.com/create-an-async-crud-web-service-in-rust-with-warp/>

在这个博客之前的一篇文章中，我们介绍了如何使用 T2 的 Actix 和 T4 的 Diesel 创建一个 Rust web 服务。这一次，我们将使用 [warp](https://github.com/seanmonstar/warp) web 框架和 [tokio-postgres](https://docs.rs/tokio-postgres/0.5.3/tokio_postgres/) 创建一个轻量级的、完全异步的 web 服务。

Warp 基于众所周知且久经考验的 [hyper](https://hyper.rs/) HTTP 库，它提供了一个健壮且非常快速的基础。warp 的另一个很酷的特性是它的过滤系统，这是一种编写 web 应用程序的功能性方法。

本质上，过滤器只是可以组合在一起的函数。在 warp 中，它们被用于从路由到中间件，再到将值传递给处理程序的所有事情。查看 warp 的[发布帖](https://seanmonstar.com/post/176530511587/warp)进行更深入的探究。在本教程中，您将看到一些过滤器的运行，我们将演示您如何自己编写一个。

## 设置

接下来，您需要的只是一个相当新的 Rust 安装(1.39+)和一个运行 Postgres 数据库的方法(例如 Docker)。

首先，创建您的测试项目。

```
cargo new warp-example
cd warp-example
```

接下来，编辑`Cargo.toml`文件并添加您需要的依赖项。

```
[dependencies]
tokio = { version = "0.2", features = ["macros"] }
warp = "0.2"
mobc-postgres = { version = "0.5", features = ["with-chrono-0_4"] }
mobc = "0.5"
serde = {version = "1.0", features = ["derive"] }
serde_derive = "1.0"
serde_json = "1.0"
thiserror = "1.0"
chrono = { version = "0.4", features = ["serde"] }
```

如果你想知道这一切意味着什么:

*   是我们的异步运行时，我们需要它来执行期货
*   是我们的网络框架
*   `mobc / mobc-postgres`代表我们数据库连接的异步连接池
*   `serde`用于序列化和反序列化对象(例如，到/从 JSON)
*   `thiserror`是一个我们将用于错误处理的实用程序库
*   `chrono`代表时间和日期实用程序

为了避免将所有内容都放入一个文件，让我们给`main.rs`添加一点结构。

```
mod data;
mod db;
mod error;
mod handler;
```

对于每个模块，我们还将创建一个文件(例如，`data.rs`)。

第一步，创建一个在端口 8000 上运行的 web 服务器，它有一个返回`200 OK`的`/health`端点。

在`main.rs`中，添加:

```
#[tokio::main]
async fn main() {
    let health_route = warp::path!("health")
        .map(|| StatusCode::OK);

    let routes = health_route
        .with(warp::cors().allow_any_origin());

    warp::serve(routes).run(([127, 0, 0, 1], 8000)).await;
}
```

在上面的代码片段中，我们定义了我们的`health_route`，它匹配`GET /health`并返回`200 OK`。然后，为了演示如何添加中间件，用`warp::cors`过滤器设置这个路由，它允许从任何来源调用服务。最后，使用`warp::serve`运行服务器。

通过使用`cargo run`和`cURL`启动应用程序来测试它是否工作。

```
curl http://localhost:8000/health
```

## 将 web 服务连接到数据库

到目前为止，一切顺利！下一步是设置 Postgres 数据库，并在`/health`处理程序中添加对工作数据库连接的检查。

要启动 Postgres 数据库，可以使用 Docker 或本地 Postgres 安装。使用 Docker，您可以简单地执行:

```
docker run -p 7878:5432 -d postgres:9.6.12
```

这个命令在端口 7878 上启动一个 Postgres DB，用户为`postgres`，数据库为`postgres`，没有密码。

现在您已经有了一个正在运行的数据库，下一步是从您的 warp 应用程序与这个数据库对话。为此，您可以使用 [mobc](https://github.com/importcjj/mobc) (一个异步连接池)来产生多个数据库连接，并在请求之间重用它们。

设置这个只需要几行代码。首先，在`main.rs`中定义一些便利类型。

```
use mobc::{Connection, Pool};
use mobc_postgres::{tokio_postgres, PgConnectionManager};
use tokio_postgres::NoTls;

type DBCon = Connection<PgConnectionManager<NoTls>>;
type DBPool = Pool<PgConnectionManager<NoTls>>;
```

接下来，在`db.rs`中创建连接池。

```
use crate::{DBCon, DBPool};
use mobc_postgres::{tokio_postgres, PgConnectionManager};
use tokio_postgres::{Config, Error, NoTls};
use std::fs;
use std::str::FromStr;
use std::time::Duration;

const DB_POOL_MAX_OPEN: u64 = 32;
const DB_POOL_MAX_IDLE: u64 = 8;
const DB_POOL_TIMEOUT_SECONDS: u64 = 15;

pub fn create_pool() -> std::result::Result<DBPool, mobc::Error<Error>> {
    let config = Config::from_str("postgres://[email protected]:7878/postgres")?;

    let manager = PgConnectionManager::new(config, NoTls);
    Ok(Pool::builder()
            .max_open(DB_POOL_MAX_OPEN)
            .max_idle(DB_POOL_MAX_IDLE)
            .get_timeout(Some(Duration::from_secs(DB_POOL_TIMEOUT_SECONDS)))
            .build(manager))
}
```

`create_pool`函数只是创建一个 Postgres 连接字符串，并为连接池定义一些参数，比如最小和最大打开连接数，以及连接超时。

下一步是简单地构建池并返回它。此时，实际上没有创建任何数据库连接，只有池。

既然我们已经在这里，让我们也创建一个在启动时初始化数据库的函数。

```
const INIT_SQL: &str = "./db.sql";

pub async fn get_db_con(db_pool: &DBPool) -> Result<DBCon> {
    db_pool.get().await.map_err(DBPoolError)
}

pub async fn init_db(db_pool: &DBPool) -> Result<()> {
    let init_file = fs::read_to_string(INIT_SQL)?;
    let con = get_db_con(db_pool).await?;
    con
            .batch_execute(init_file.as_str())
            .await
            .map_err(DBInitError)?;
    Ok(())
}
```

使用`get_db_con`实用程序，我们试图从池中获得一个新的数据库连接。现在不要担心错误，我们稍后将讨论错误处理。

为了在启动时从`db.sql`文件创建一个数据库表，调用了`init_db`函数。这会将文件读入一个字符串并执行查询。

init 查询如下所示:

```
CREATE TABLE IF NOT EXISTS todo
(
    id SERIAL PRIMARY KEY NOT NULL,
    name VARCHAR(255),
    created_at timestamp with time zone DEFAULT (now() at time zone 'utc'),
    checked boolean DEFAULT false
);
```

回到我们的主函数，我们现在可以调用数据库设置函数。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
let db_pool = db::create_pool().expect("database pool can be created");

db::init_db(&db_pool)
    .await
    .expect("database can be initialized");
```

如果任何一个数据库设置代码失败了，我们就会惊慌失措，因为继续下去毫无意义。

假设它没有失败，那么终于到了处理本节主要目标的时候了:向`/health`处理程序添加一个数据库检查。

为此，我们需要一种方法将`db_pool`传递给处理程序。这是编写我们第一个曲速`filter`的绝佳机会。

在`main.rs`中，添加以下`with_db`过滤器。

```
use std::convert::Infallible;
use warp::{Filter, Rejection};

fn with_db(db_pool: DBPool) -> impl Filter<Extract = (DBPool,), Error = Infallible> + Clone {
    warp::any().map(move || db_pool.clone())
}
```

这是一个简单的提取过滤器。上面的意思是，对于任何一个路由(`any()`)，你都想提取一个`DBPool`并传递下去。

如果你有兴趣学习更多关于过滤器的知识，那么[文档](https://docs.rs/warp/0.2.2/warp/trait.Filter.html)会很有帮助。

然后用`.and()`操作符将过滤器简单地添加到处理程序定义中:

```
let health_route = warp::path!("health")
  .and(with_db(db_pool.clone()))
  .and_then(handler::health_handler);
```

将健康处理程序移动到`handler.rs`文件，并添加数据库检查。

```
use crate::{db, DBPool};
use warp::{http::StatusCode, reject, Reply, Rejection};

pub async fn health_handler(db_pool: DBPool) -> std::result::Result<impl Reply, Rejection> {
    let db = db::get_db_con(&db_pool)
            .await
            .map_err(|e| reject::custom(e))?;

    db.execute("SELECT 1", &[])
            .await
            .map_err(|e| reject::custom(DBQueryError(e)))?;
    Ok(StatusCode::OK)
}
```

现在，处理程序接收到一个`DBPool`，您可以用它来获得一个连接并启动一个针对数据库的健全性检查查询。

如果检查过程中出现错误，使用`reject::custom`返回自定义错误。

接下来，正如承诺的那样，让我们看看 warp 的错误处理。

## 处理错误

在任何 web 应用程序中，干净的错误处理都是最重要也是最容易被忽略的事情之一。目标是向 API 消费者提供有用的错误，同时不泄露内部细节。

我们将使用 [thiserror](https://github.com/dtolnay/thiserror) 库来方便地创建自定义错误。

从`error.rs`开始，定义一个`Error`枚举，它有一个针对所有错误的变量。

```
use mobc_postgres::tokio_postgres;
use thiserror::Error;

#[derive(Error, Debug)]
pub enum Error {
    #[error("error getting connection from DB pool: {0}")]
    DBPoolError(mobc::Error<tokio_postgres::Error>),
    #[error("error executing DB query: {0}")]
    DBQueryError(#[from] tokio_postgres::Error),
    #[error("error creating table: {0}")]
    DBInitError(tokio_postgres::Error),
    #[error("error reading file: {0}")]
    ReadFileError(#[from] std::io::Error),
}
```

如果我们能够找到一种方法将这些和其他错误转换成有意义的 API 响应，我们可以简单地从处理程序返回一个自定义错误，调用者将自动获得正确的错误消息和状态代码。

为此，我们将使用 warp 的概念`rejections`。

首先，为容易出错的结果向`main.rs`添加一个便利类型。

```
type Result<T> = std::result::Result<T, warp::Rejection>;
```

接下来，通过实现`Reject`特征，确保 warp 将您的定制错误识别为拒绝。

```
impl warp::reject::Reject for Error {}
```

定义一个拒绝处理程序，它将拒绝转换成如下形式的好的错误响应。

```
#[derive(Serialize)]
struct ErrorResponse {
    message: String,
}
```

这样的拒绝处理程序可能如下所示:

```
pub async fn handle_rejection(err: Rejection) -> std::result::Result<impl Reply, Infallible> {
    let code;
    let message;

    if err.is_not_found() {
            code = StatusCode::NOT_FOUND;
            message = "Not Found";
    } else if let Some(_) = err.find::<warp::filters::body::BodyDeserializeError>() {
            code = StatusCode::BAD_REQUEST;
            message = "Invalid Body";
    } else if let Some(e) = err.find::<Error>() {
            match e {
                Error::DBQueryError(_) => {
                    code = StatusCode::BAD_REQUEST;
                    message = "Could not Execute request";
                }
                _ => {
                    eprintln!("unhandled application error: {:?}", err);
                    code = StatusCode::INTERNAL_SERVER_ERROR;
                    message = "Internal Server Error";
                }
            }
    } else if let Some(_) = err.find::<warp::reject::MethodNotAllowed>() {
            code = StatusCode::METHOD_NOT_ALLOWED;
            message = "Method Not Allowed";
    } else {
            eprintln!("unhandled error: {:?}", err);
            code = StatusCode::INTERNAL_SERVER_ERROR;
            message = "Internal Server Error";
    }

    let json = warp::reply::json(&ErrorResponse {
            message: message.into(),
    });

    Ok(warp::reply::with_status(json, code))
}
```

基本上，我们从处理程序那里得到一个`Rejection`。然后，根据错误的类型，我们为响应设置消息和状态代码。

如您所见，我们既可以处理一般错误，如`not found`，也可以处理特定问题，如解析请求的 JSON 主体时遇到的错误。

回退处理程序向用户返回一个一般性的 500 错误，并记录错误所在，这样您就可以在不泄漏内部信息的情况下进行必要的调查。

在路由定义中，只需使用`recover`过滤器添加这个错误处理程序:

```
let routes = health_route
    .with(warp::cors().allow_any_origin())
    .recover(error::handle_rejection);
```

完美！我们已经取得了很大的进步。剩下的就是为你的待办事项应用程序实现 CRUD 处理程序。

## 实现 CRUD API

现在，我们有了一个正在运行的 web 服务器，它连接到了一个数据库，并且有了一种优雅地处理错误的方法。我们的应用程序唯一缺少的是实际的应用程序逻辑。

我们将实现四个处理程序:

1.  `GET /todo/?search={searchString}`列出所有待办事项，通过可选的搜索字符串进行过滤
2.  `POST /todo/`创建待办事宜
3.  `PUT /todo/{id}`用给定的 ID 更新待办事项
4.  `DELETE /todo/{id}`删除给定 ID 的待办事项

第一步是创建 todos，因为没有它们，我们将无法方便地测试其他端点。

在`db.rs`中，增加一个将 todos 插入数据库的功能。

```
const TABLE: &str = "todo";

pub async fn create_todo(db_pool: &DBPool, body: TodoRequest) -> Result<Todo> {
    let con = get_db_con(db_pool).await?;
    let query = format!("INSERT INTO {} (name) VALUES ($1) RETURNING *", TABLE);
    let row = con
            .query_one(query.as_str(), &[&body.name])
            .await
            .map_err(DBQueryError)?;
    Ok(row_to_todo(&row))
}

fn row_to_todo(row: &Row) -> Todo {
    let id: i32 = row.get(0);
    let name: String = row.get(1);
    let created_at: DateTime<Utc> = row.get(2);
    let checked: bool = row.get(3);
    Todo {
            id,
            name,
            created_at,
            checked,
    }
}
```

这将从池中建立一个连接，发送一个插入查询，并将返回的行转换成一个`Todo`。

为此，您需要一些数据对象，它们在`data.rs`中定义。

```
use chrono::prelude::*;
use serde_derive::{Deserialize, Serialize};

#[derive(Deserialize)]
pub struct Todo {
    pub id: i32,
    pub name: String,
    pub created_at: DateTime<Utc>,
    pub checked: bool,
}

#[derive(Deserialize)]
pub struct TodoRequest {
    pub name: String,
}

#[derive(Deserialize)]
pub struct TodoUpdateRequest {
    pub name: String,
    pub checked: bool,
}

#[derive(Serialize)]
pub struct TodoResponse {
    pub id: i32,
    pub name: String,
    pub checked: bool,
}

impl TodoResponse {
    pub fn of(todo: Todo) -> TodoResponse {
            TodoResponse {
                id: todo.id,
                name: todo.name,
                checked: todo.checked,
            }
    }
}
```

`Todo`结构本质上是数据库表的镜像。`tokio-postgres`可以使用 chrono 的`DateTime<Utc>`来映射时间戳。其他结构是创建和更新 todo 所需的 JSON 请求，以及在 list、update 和 create 处理程序中发回的响应。

您现在可以在`handler.rs`中创建实际的创建处理程序。

```
pub async fn create_todo_handler(body: TodoRequest, db_pool: DBPool) -> Result<impl Reply> {
    Ok(json(&TodoResponse::of(
        db::create_todo(&db_pool, body)
            .await
            .map_err(|e| reject::custom(e))?,
    )))
}
```

在这种情况下，您将请求体解析为一个`TodoRequest`并将`db_pool`传递给处理程序。一旦进入，只需调用数据库函数，将其映射到一个`TodoResponse`，并使用 warp 的`reply::json`助手将其序列化到 JSON。

如果发生错误，用 warp 的`reject::custom`来处理它，这使您能够创建一个自定义错误类型的拒绝。

唯一缺少的是`main.rs`中的路由定义。

```
let todo = warp::path("todo");
let todo_routes = todo
        .and(warp::post())
        .and(warp::body::json())
        .and(with_db(db_pool.clone()))
        .and_then(handler::create_todo_handler));

let routes = health_route
    .or(todo_routes)
    .with(warp::cors().allow_any_origin())
    .recover(error::handle_rejection);
```

您将在 `/todo/`使用`warp::path`选择多条路线。然后，使用 warp 的过滤器，组成您的创建处理程序。

添加`post`方法，指定您期望一个 JSON 主体，并使用您的`with_db`过滤器来表示您需要数据库访问。最后，通过告诉路由使用哪个处理器来结束。

然后，所有这些都通过一个`or`操作符传递给路由。

用下面的命令测试它。

```
curl -X POST 'http://localhost:8000/todo/' -H 'Content-Type: application/json' -d '{"name": "Some Todo"}'

{"id":1,"name":"Some Todo","checked":false}
```

太好了！现在您已经知道了它是如何工作的，您可以同时处理其他三个处理程序了。同样，从添加数据库助手开始。

```
const SELECT_FIELDS: &str = "id, name, created_at, checked";

pub async fn fetch_todos(db_pool: &DBPool, search: Option<String>) -> Result<Vec<Todo>> {
    let con = get_db_con(db_pool).await?;
    let where_clause = match search {
            Some(_) => "WHERE name like $1",
            None => "",
    };
    let query = format!(
            "SELECT {} FROM {} {} ORDER BY created_at DESC",
            SELECT_FIELDS, TABLE, where_clause
    );
    let q = match search {
            Some(v) => con.query(query.as_str(), &[&v]).await,
            None => con.query(query.as_str(), &[]).await,
    };
    let rows = q.map_err(DBQueryError)?;

    Ok(rows.iter().map(|r| row_to_todo(&r)).collect())
}

pub async fn update_todo(db_pool: &DBPool, id: i32, body: TodoUpdateRequest) -> Result<Todo> {
    let con = get_db_con(db_pool).await?;
    let query = format!(
            "UPDATE {} SET name = $1, checked = $2 WHERE id = $3 RETURNING *",
            TABLE
    );
    let row = con
            .query_one(query.as_str(), &[&body.name, &body.checked, &id])
            .await
            .map_err(DBQueryError)?;
    Ok(row_to_todo(&row))
}

pub async fn delete_todo(db_pool: &DBPool, id: i32) -> Result<u64> {
    let con = get_db_con(db_pool).await?;
    let query = format!("DELETE FROM {} WHERE id = $1", TABLE);
    con.execute(query.as_str(), &[&id])
            .await
            .map_err(DBQueryError)
}
```

这些基本上与`create`的情况相同，除了`fetch_todos`，如果有搜索词，您将创建一个不同的查询。

接下来让我们看看处理程序。

```
#[derive(Deserialize)]
pub struct SearchQuery {
    search: Option<String>,
}

pub async fn list_todos_handler(query: SearchQuery, db_pool: DBPool) -> Result<impl Reply> {
    let todos = db::fetch_todos(&db_pool, query.search)
            .await
            .map_err(|e| reject::custom(e))?;
    Ok(json::<Vec<_>>(
            &todos.into_iter().map(|t| TodoResponse::of(t)).collect(),
    ))
}

pub async fn update_todo_handler(
    id: i32,
    body: TodoUpdateRequest,
    db_pool: DBPool,
) -> Result<impl Reply> {
    Ok(json(&TodoResponse::of(
        db::update_todo(&db_pool, id, body)
            .await
            .map_err(|e| reject::custom(e))?,
    )))
}

pub async fn delete_todo_handler(id: i32, db_pool: DBPool) -> Result<impl Reply> {
    db::delete_todo(&db_pool, id)
            .await
            .map_err(|e| reject::custom(e))?;
    Ok(StatusCode::OK)
}
```

还是那句话，你会看到一些熟悉的东西。每个处理程序调用数据库层，处理错误，如果一切顺利，为调用者创建一个返回值。

一个有趣的例外是`list_todos_handler`，前面提到的查询参数被传入，已经被解析为一个`SearchQuery`。

这就是你在 warp 中处理查询参数的方式。如果您有更多不同类型的参数，您可以简单地将它们添加到`SearchQuery`结构中，它们将被自动解析。

让我们把所有东西连接起来，做最后一次测试。

```
let todo_routes = todo
        .and(warp::get())
        .and(warp::query())
        .and(with_db(db_pool.clone()))
        .and_then(handler::list_todos_handler)
    .or(todo
            .and(warp::post())
            .and(warp::body::json())
            .and(with_db(db_pool.clone()))
            .and_then(handler::create_todo_handler))
    .or(todo
            .and(warp::put())
            .and(warp::path::param())
            .and(warp::body::json())
            .and(with_db(db_pool.clone()))
            .and_then(handler::update_todo_handler))
    .or(todo
            .and(warp::delete())
            .and(warp::path::param())
            .and(with_db(db_pool.clone()))
            .and_then(handler::delete_todo_handler));
```

这里有一些新的东西。要将查询参数传递给列表处理程序，您需要使用`warp::query()`。要获得用于更新和删除的`id`参数，请使用`warp::path::param()`。用`or`操作符组合不同的路线，你的 todo 路线就设置好了。

在 warp 中有许多不同的方法来创建和构造路线。它只是将功能组合在一起，因此过程非常灵活。更多示例，请查看[官方文档](https://docs.rs/warp/0.1.22/warp/filters/path/)。

现在让我们来测试整个事情。

首先，看看错误处理是否真的有效。

```
curl -v -X POST 'http://localhost:8000/todo/' -H 'Content-Type: application/json' -d '{"wrong": "Some Todo"}'

HTTP/1.1 400 Bad Request
{"message":"Invalid Body"}
```

接下来，添加另一个`Todo`，立即检查它，并尝试更新一个不存在的`Todo`。

```
curl -X POST 'http://localhost:8000/todo/' -H 'Content-Type: application/json' -d '{"name": "Done Todo"}'

{"id":2,"name":"Done Todo","checked":false}

curl -X PUT 'http://localhost:8000/todo/2' -H 'Content-Type: application/json' -d '{"name": "Done Todo", "checked": true}'

{"id":2,"name":"Done Todo","checked":true}

curl -X PUT 'http://localhost:8000/todo/2000' -H 'Content-Type: application/json' -d '{"name": "Done Todo", "checked": true}'

{"message":"Could not Execute request"}
```

到目前为止，一切顺利！现在列出它们，过滤列表，删除其中一个，然后重新列出。

```
curl -X GET 'http://localhost:8000/todo/' -H 'Content-Type: application/json'

[{"id":1,"name":"Some Todo","checked":false},{"id":2,"name":"Done Todo","checked":true}]

curl -X GET 'http://localhost:8000/todo/?search=Done%20Todo' -H 'Content-Type: application/json'

[{"id":2,"name":"Done Todo","checked":true}]

curl -v -X DELETE 'http://localhost:8000/todo/2' -H 'Content-Type: application/json'

HTTP/1.1 200 OK

curl -X GET 'http://localhost:8000/todo/' -H 'Content-Type: application/json'

[{"id":1,"name":"Some Todo","checked":false}]
```

完美！一切按预期运行。你可以在 [GitHub](https://github.com/zupzup/warp-postgres-example) 上找到这个例子的完整代码。

## 结论

多好的旅程啊！在本教程中，我们演示了如何使用 warp 和 tokio-postgres 创建一个完全异步的 web 应用程序。我们成功地用不到 300 行 Rust 代码实现了一个带有错误处理的基本数据库支持的 CRUD web 服务。不算太寒酸！

曲速看起来很有前途；它轻巧、现代、快速，我喜欢这种功能性的方法。这个框架还很年轻，还没有经受住时间的考验，但是到目前为止，它看起来很棒。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。