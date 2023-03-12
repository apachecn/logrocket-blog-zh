# Rust web 服务的端到端测试

> 原文：<https://blog.logrocket.com/end-to-end-testing-for-rust-web-services/>

在本教程中，我们将演示如何测试一个 [warp](https://github.com/seanmonstar/warp) web 应用程序，重点是集成测试，更具体地说，是实际评估整个系统的测试——也称为端到端测试。

首先，我们将构建一个小型的待办事项应用程序，它具有一个数据库和一个外部 HTTP 服务调用。这些是我们在编写测试时必须处理的事情。在这个过程中，我们还将大致了解如何测试扭曲过滤器和扭曲应用程序。

处理外部依赖(如数据库或缓存)的标准策略是简单地模仿它们——换句话说，用假的实现来替换它们。你可以通过使用 traits 或者 Rust 可用的众多模仿库中的一个来实现。

模拟是有用的，但是根据定义，它们不是真实的东西。例如，如果您的模拟实现有问题或不完整，您很可能会错过一些边缘情况。另一方面，使用真实的数据库和外部 web 服务进行自动化测试也会带来一些问题，比如提供基础设施、测试可重复性和速度。

最终，您决定编写哪些测试将取决于您的个人偏好和所审查的服务的性质。这就是为什么在你的工具箱里有几种技术是好的。

为了说明这些概念，我们将从创建一个简单的、自己编写的模拟解决方案开始，并逐步走向众所周知的“真实的东西”，用更复杂的赝品和真实的数据库替换模拟，使用 web 服务并向它发送 HTTP 请求。当然，我们的解决方案将是完全自动化和可重复的。

我们开始吧！

## 设置

接下来，您所需要的只是一个相当新的 Rust 安装(1.39+)。Docker ，或者其他运行 [Postgres](https://www.postgresql.org/) 数据库的工具也会有用。

首先，创建一个新的 Rust 项目。

```
cargo new rust-web-e2e-testing
cd rust-web-e2e-testing

```

编辑`Cargo.toml`文件并添加您需要的依赖项。

```
[dependencies]
tokio = { version = "0.2.21", features = ["macros", "rt-threaded", "sync", "time"] }
warp = "0.2.3"
mobc = "0.5.11"
mobc-postgres = { version = "0.5.0" }
hyper = "0.13"
hyper-tls = "0.4.1"
serde = {version = "1.0", features = ["derive"] }
serde_json = "1.0.53"
thiserror = "1.0"

[dev-dependencies]
wiremock = "0.2.2"
lazy_static = "=1.4.0"

```

由于我们将 warp 与 Tokio 一起使用，并使用数据库和外部 web 服务构建一个 JSON web 服务，因此这些依赖关系中的大部分都不足为奇。

`dev-dependencies`更有趣一点，尤其是 [wiremock](https://github.com/LukeMathWalker/wiremock-rs) ，这是一个编写良好的库，用于模拟外部 web 服务。这使得在应用程序执行真正的 HTTP 请求的同时定义预期的响应成为可能。

## 编写要测试的 web 服务

让我们从编写 web 服务开始。我们将快速浏览这一部分，但是我们在之前的一篇文章中已经介绍了基本概念。我们将从底层开始进行数据库抽象。

首先，定义`DBAccessor`特征，它将是我们在系统中传递来访问数据库的内容。

```
#[async_trait]
pub trait DBAccessor: Send + Sync + Clone + 'static {
    async fn fetch_todos(&self) -> Result<Vec<Todo>>;
    async fn create_todo(&self, name: String) -> Result<Todo>;
}

```

`async_trait`宏使我们能够在特征定义中使用`async`,这将在未来工作，但还没有在 Rust 中稳定下来。

接下来是具体的实现，`DBAccess`，它包括一些帮助器，用于从池中获取连接，并将数据库行反序列化到`Todo`。

```
#[derive(Deserialize)]
pub struct Todo {
    pub id: i32,
    pub name: String,
    pub checked: bool,
}

#[derive(Clone)]
pub struct DBAccess {
    pub db_pool: DBPool,
}

const INIT_SQL: &str = "./db.sql";

pub fn create_pool() -> std::result::Result<DBPool, mobc::Error<Error>> {
    let config = Config::from_str("postgres://[email protected]:7878/postgres")?;
    Ok(Pool::builder().build(PgConnectionManager::new(config, NoTls)))
}

impl DBAccess {
    pub fn new(db_pool: DBPool) -> Self {
        Self { db_pool }
    }

    pub async fn init_db(&self) -> Result<()> {
        let init_file = fs::read_to_string(INIT_SQL)?;
        let con = self.get_db_con().await?;
        con.batch_execute(init_file.as_str())
            .await
            .map_err(DBInitError)?;
        Ok(())
    }

    async fn get_db_con(&self) -> Result<DBCon> {
        self.db_pool.get().await.map_err(DBPoolError)
    }

    fn row_to_todo(&self, row: &Row) -> Todo {
        let id: i32 = row.get(0);
        let name: String = row.get(1);
        let checked: bool = row.get(2);
        Todo { id, name, checked }
    }
}

```

用于创建表格的`db.sql`文件也非常简单。

```
CREATE TABLE IF NOT EXISTS todo
(
    id SERIAL PRIMARY KEY NOT NULL,
    name TEXT,
    checked boolean DEFAULT false
);

```

最后，实现`DBAccessor`特征。

```
#[async_trait]
impl DBAccessor for DBAccess {
    async fn fetch_todos(&self) -> Result<Vec<Todo>> {
        let con = self.get_db_con().await?;
        let query = "SELECT id, name, checked FROM todo ORDER BY id ASC";
        let q = con.query(query, &[]).await;
        let rows = q.map_err(DBQueryError)?;

        Ok(rows.iter().map(|r| self.row_to_todo(&r)).collect())
    }

    async fn create_todo(&self, name: String) -> Result<Todo> {
        let con = self.get_db_con().await?;
        let query = "INSERT INTO todo (name) VALUES ($1) RETURNING *";
        let row = con.query_one(query, &[&name]).await.map_err(DBQueryError)?;
        Ok(self.row_to_todo(&row))
    }
}

```

`db`模块使我们能够连接和初始化数据库，并创建和获取 todos。

在这个应用程序中，我们想用来自这个猫事实应用程序的随机猫事实创建 todos，所以我们需要的下一个模块是一个 HTTP 抽象。

再次，从一个特质开始:

```
#[async_trait]
pub trait HttpClient: Send + Sync + Clone + 'static {
    async fn get_cat_fact(&self) -> Result<String>;
}

```

`HttpClient`是一个更简单的。具体的`Client`实现也是如此。

```
#[derive(Clone)]
pub struct Client {
    client: HyperClient<HttpsConnector<HttpConnector>>,
}

#[derive(Debug, Deserialize)]
pub struct CatFact {
    pub text: String,
}

impl Client {
    pub fn new() -> Self {
        let HTTPs = HttpsConnector::new();
        Self {
            client: HyperClient::builder().build::<_, Body>(https),
        }
    }

    fn get_url(&self) -> String {
        URI.to_owned()
    }
}

#[async_trait]
impl HttpClient for Client {
    async fn get_cat_fact(&self) -> Result<String> {
        let req = Request::builder()
            .method(Method::GET)
            .uri(&format!("{}{}", self.get_url(), "/facts/random"))
            .header("content-type", "application/json")
            .header("accept", "application/json")
            .body(Body::empty())?;
        let res = self.client.request(req).await?;
        if !res.status().is_success() {
            return Err(error::Error::GetCatFactError(res.status()));
        }
        let body_bytes = to_bytes(res.into_body()).await?;
        let json = from_slice::<CatFact>(&body_bytes)?;
        Ok(json.text)
    }
}

```

这个实现使用强大的 [hyper](http://hyper.rs/) HTTP 客户端来获得一个新的、有趣的`CatFact`，并且只返回实际的文本，忽略元数据。

有了这两个抽象，我们就可以上升一个层次，实现两个处理程序来获取和创建 todos。

`handler`模块有两个功能:

```
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

pub async fn list_todos_handler(db_access: impl DBAccessor) -> Result<impl Reply> {
    let todos = db_access
        .fetch_todos()
        .await
        .map_err(|e| reject::custom(e))?;
    Ok(json::<Vec<_>>(
        &todos.into_iter().map(|t| TodoResponse::of(t)).collect(),
    ))
}

pub async fn create_todo(
    http_client: impl HttpClient,
    db_access: impl DBAccessor,
) -> Result<impl Reply> {
    let cat_fact = http_client
        .get_cat_fact()
        .await
        .map_err(|e| reject::custom(e))?;
    Ok(json(&TodoResponse::of(
        db_access
            .create_todo(cat_fact)
            .await
            .map_err(|e| reject::custom(e))?,
    )))
}

```

在上面的代码片段中，定义了一个`TodoResponse`，它是来自数据库的 todos 的 JSON 表示。列表处理程序只是查询数据库，并将所有条目返回给调用者。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在`create_todo`处理程序中，我们使用数据库和 HTTP 抽象来获取一个新的 cat 事实，从中创建一个 todo，并返回新创建的 todo。

在这两种情况下，所有的错误都被正确地处理并发送到客户端。

说到错误，上面的代码中有一些自定义的错误类型，我们还没有检查过。接下来让我们看看`error`模块，它负责整个应用程序的错误处理。

```
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
    #[error("http client error: {0}")]
    HyperHttpError(#[from] hyper::http::Error),
    #[error("http client error: {0}")]
    HypeError(#[from] hyper::error::Error),
    #[error("http client error: {0}")]
    JSONError(#[from] serde_json::error::Error),
    #[error("http client error: {0}")]
    GetCatFactError(StatusCode),
}

#[derive(Serialize)]
struct ErrorResponse {
    message: String,
}

impl warp::reject::Reject for Error {}

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
                eprintln!("{}", e);
                code = StatusCode::BAD_REQUEST;
                message = "DB Error: Could not Execute request";
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

这只是一些如何处理 warp `Rejections`的样板文件，所以我们可以用自定义错误而不是字符串来保持代码的其余部分相对干净。

让我们通过查看`main`来完成 web 应用程序，在这里所有的内容都被放在一起。

首先，我们需要两个 warp 过滤器将数据库和 HTTP 抽象传递给处理程序。

```
fn with_db(
    db_access: impl db::DBAccessor,
) -> impl Filter<Extract = (impl db::DBAccessor,), Error = Infallible> + Clone {
    warp::any().map(move || db_access.clone())
}

fn with_http_client(
    http_client: impl HttpClient,
) -> impl Filter<Extract = (impl HttpClient,), Error = Infallible> + Clone {
    warp::any().map(move || http_client.clone())
}

```

然后我们可以为应用程序定义路由。

```
fn router(
    http_client: impl http::HttpClient,
    db_access: impl db::DBAccessor,
) -> impl Filter<Extract = impl Reply, Error = Infallible> + Clone {
    let todo = warp::path("todo");
    let todo_routes = todo
        .and(warp::get())
        .and(with_db(db_access.clone()))
        .and_then(handler::list_todos_handler)
        .or(todo
            .and(warp::post())
            .and(with_http_client(http_client.clone()))
            .and(with_db(db_access.clone()))
            .and_then(handler::create_todo));

    todo_routes.recover(error::handle_rejection)
}

```

我们把它们放在一个`run`函数中，这个函数由`main`调用。出于我们稍后将讨论的原因，这被放入它自己的函数中:

```
type Result<T> = std::result::Result<T, Rejection>;
type DBCon = Connection<PgConnectionManager<NoTls>>;
type DBPool = Pool<PgConnectionManager<NoTls>>;

#[tokio::main]
async fn main() {
    run().await;
}

async fn run() {
    let db_pool = db::create_pool().expect("database pool can be created");
    let db_access = db::DBAccess::new(db_pool);

    db_access
        .init_db()
        .await
        .expect("database can be initialized");
    let http_client = http::Client::new();

    println!("Server started at localhost:8080");
    warp::serve(router(http_client, db_access))
        .run(([0, 0, 0, 0], 8080))
        .await;
}

```

运行这个应用程序会在端口 8080 上启动一个 HTTP 服务器。我们可以用下面的命令创建并列出 todos。

```
curl http://localhost:8080/todo
curl -X POST http://localhost:8080/todo

```

这个例子的完整代码可以在 [GitHub](https://github.com/zupzup/rust-web-e2e-testing) 上找到。

有了这个相当长的设置，让我们开始测试！

## 带嘲笑的测试

在 Rust 中，通常将单元测试直接写在被测试的文件中，将集成测试写在`src`之外的`tests`文件夹中。

然而，由于集成测试方法是它自己的构建实体，并且意味着测试机箱的公共接口，这对于我们想要做的事情来说不是很有用。我们将采取更多的混合方法。

为了开始我们从模拟到真实的旅程，让我们在`src`中创建一个`test`模块，并将其添加到`main.rs`中。

```
#[cfg(test)]
mod tests;

```

这意味着该模块只为测试而加载，不会出现在我们的二进制文件中。

由于计划是从非常基本的模拟开始，让我们在`tests/mock.rs`中首先构建它们。

```
use crate::db::{DBAccessor, Todo};
use crate::HTTP::HttpClient;
...

#[derive(Clone)]
pub struct MockHttpClient {}
type Result<T> = std::result::Result<T, error::Error>;

#[async_trait]
impl HttpClient for MockHttpClient {
    async fn get_cat_fact(&self) -> Result<String> {
        Ok(String::from("cat fact"))
    }
}

#[derive(Clone)]
pub struct MockDBAccessor {}

#[async_trait]
impl DBAccessor for MockDBAccessor {
    async fn fetch_todos(&self) -> Result<Vec<Todo>> {
        Ok(vec![Todo {
            id: 1,
            name: String::from("first todo"),
            checked: true,
        }])
    }

    async fn create_todo(&self, name: String) -> Result<Todo> {
        Ok(Todo {
            id: 2,
            name: name,
            checked: false,
        })
    }
}

```

这里，我们简单地实现了应用程序中定义的数据库和 HTTP 抽象的两个硬编码版本。现在我们可以为我们的两个处理程序编写简单的测试。

```
#[tokio::test]
async fn test_list_todos_mock() {
    let r = router(MockHttpClient {}, MockDBAccessor {});
    let resp = request().path("/todo").reply(&r).await;
    assert_eq!(resp.status(), 200);
    assert_eq!(
        resp.body(),
        r#"[{"id":1,"name":"first todo","checked":true}]"#
    );
}

#[tokio::test]
async fn test_create_todo_mock() {
    let r = router(MockHttpClient {}, MockDBAccessor {});
    let resp = request()
        .path("/todo")
        .method("POST")
        .body("")
        .reply(&r)
        .await;
    assert_eq!(resp.status(), 200);
    assert_eq!(resp.body(), r#"{"id":2,"name":"cat fact","checked":false}"#);
}

```

这是怎么回事？首先要注意的是使用奇妙的`use warp::test`工具来测试扭曲过滤器和处理程序。这些工具使您能够针对 warp 测试工具创建 HTTP 请求。因为 warp `Filters`只是函数，而处理程序只是未来，所以你实际上可以插入整个路由器或其他定制过滤器来测试另一端会出现什么。这对于测试复杂的滤波器非常有用。

然而，在上面的例子中，我们对路由器使用了`reply`方法，用两个基本模拟实例化，这将给我们一个可以断言的实际 HTTP 响应。在这种情况下，我们应该确保响应与我们在基本模拟中返回的硬编码数据相匹配。

有了一个强大的模拟库，您可以用这种方法走得更远，动态地模拟各种响应，并验证一切如您所愿。

然而，我们也想看看我们的 HTTP 抽象是否按计划工作。下一步，我们将发送实际的 HTTP 请求。

## 混合方法

为了发送真正的 HTTP 请求(不依赖于正在运行的外部服务或垃圾邮件)，我们将使用 wiremock 库。

首先，围绕它构建一个薄薄的包装器，这样您就可以在整个应用程序和测试中轻松地使用它。

```
pub struct WiremockServer {
    pub server: Option<MockServer>,
}

impl WiremockServer {
    pub fn new() -> Self {
        Self { server: None }
    }

    pub async fn init(&mut self) {
        let mock_server = MockServer::start().await;
        Mock::given(method("GET"))
            .and(path("/facts/random"))
            .respond_with(
                ResponseTemplate::new(200).set_body_string(r#"{"text": "wiremock cat fact"}"#),
            )
            .mount(&mock_server)
            .await;
        self.server = Some(mock_server);
    }
}

lazy_static! {
    pub static ref MOCK_HTTP_SERVER: RwLock<WiremockServer> = RwLock::new(WiremockServer::new());
}

async fn setup_wiremock() {
    MOCK_HTTP_SERVER.write().unwrap().init().await;
}

```

这个简单的构造允许我们生成一个 wiremock 服务器，它在您的系统上搜索一个随机端口，按需启动并从任何地方访问它。

您还会注意到，我们为`/facts/given`端点注册了一个`Mock`。这意味着 wiremock 服务器上对该路径的所有请求都将返回所提供的响应。对于您想要替换的请求的所有部分，wiremock 可以使用定制匹配器做更多的事情。

在设置阶段，您可能想知道 HTTP 抽象中的`get_url`助手，在这种形式下，它似乎毫无用处。我们将在这一步中扩展它。

```
    fn get_url(&self) -> String {
        #[cfg(not(test))]
        return URI.to_owned();
        #[cfg(test)]
        return match crate::tests::MOCK_HTTP_SERVER.read().unwrap().server {
            Some(ref v) => v.uri(),
            None => URI.to_owned(),
        };
    }

```

这使我们能够在测试运行期间看到 wiremock 服务器是否正在运行。如果是，我们可以使用它的 URI 请求，否则就退回到标准的 URI。

这是必要的，因为我们事先不知道 wiremock 服务器将在哪个端口启动。还有其他方法可以做到这一点，但这是一个简单的方法来帮助您开始。

现在我们可以编写向 wiremock 发送实际 HTTP 请求的测试。

```
#[tokio::test]
async fn test_create_and_list_todo_hybrid() {
    setup_wiremock().await;
    let r = router(http::Client::new(), MockDBAccessor {});
    let resp = request()
        .path("/todo")
        .method("POST")
        .body("")
        .reply(&r)
        .await;
    assert_eq!(resp.status(), 200);
    assert_eq!(
        resp.body(),
        r#"{"id":2,"name":"wiremock cat fact","checked":false}"#
    );

    let resp = request().path("/todo").reply(&r).await;
    assert_eq!(resp.status(), 200);
    assert_eq!(
        resp.body(),
        r#"[{"id":1,"name":"first todo","checked":true}]"#
    );
}

```

如您所见，测试非常相似。主要的区别是我们创建了一个真正的 HTTP 客户机，它向 wiremock 发送真正的 HTTP 请求。这很有效——太好了！

但是我们仍然对数据库使用模拟，我们真的很想看看我们的查询是否如预期的那样工作。所以让我们解决这个问题。

## 无模拟测试

要在集成测试中使用真实的数据库，您实际上必须启动一个数据库服务器。

这可能实用，也可能不实用，取决于您使用的数据库引擎。使用 Postgres 数据库，您可以简单地在本地或 CI 上启动一个实例(一些 CI 提供者还提供 Postgres/Redis 和其他外部服务作为 CI 运行期间的运行服务)。

```
docker run -p 7878:5432 -d postgres:9.6.12

```

另一件要记住的事情是，您一次只能运行这些测试中的一个。否则，您将面临干扰其他测试的数据库状态的风险。为了确保这一点，您可以在运行测试时设置`--test-threads=1`。

```
cargo test --offline -- --color=always --test-threads=1 --nocapture

```

如果您有大量的测试，并且在每个测试之后都有数据库清理步骤，那么使用这种方法的测试将比模拟解决方案花费更长的时间。

下一步是创建一个助手函数来连接数据库并重置它，因为我们希望每个测试都有一个新的状态。

```
async fn init_db() -> impl db::DBAccessor {
    let db_pool = db::create_pool().expect("database pool can be created");
    let db_access = db::DBAccess::new(db_pool.clone());

    db_access
        .init_db()
        .await
        .expect("database can be initialized");

    let con = db_pool.get().await.unwrap();
    let query = format!("BEGIN;DELETE FROM todo;ALTER SEQUENCE todo_id_seq RESTART with 1;COMMIT;");
    let _ = con.batch_execute(query.as_str()).await;

    db_access
}

```

创建连接池和`DBAccess`实例，然后删除所有数据并重置`id`序列(这样每次都有一个新的开始)并返回要在测试中使用的实例。

有了这些，我们就可以编写一个使用真实数据库并发送真实 HTTP 请求的测试。

```
#[tokio::test]
async fn test_create_and_list_full() {
    setup_wiremock().await;
    let r = router(http::Client::new(), init_db().await);
    let resp = request()
        .path("/todo")
        .method("POST")
        .body("")
        .reply(&r)
        .await;
    assert_eq!(resp.status(), 200);
    assert_eq!(
        resp.body(),
        r#"{"id":1,"name":"wiremock cat fact","checked":false}"#
    );

    let resp = request().path("/todo").reply(&r).await;
    assert_eq!(resp.status(), 200);
    assert_eq!(
        resp.body(),
        r#"[{"id":1,"name":"wiremock cat fact","checked":false}]"#
    );
}

```

我们可以就此打住，但是我们想进行完整的端到端测试——这意味着我们不想使用 warp 测试工具，而是使用我们实际的 web 服务。

让我们用一些端到端的焰火来结束这个教程吧！

## 完整的端到端测试

还记得我说过我们稍后会看到为什么一切都在`run`函数中执行吗？现在是时候了。为了用所有真实的东西测试正在运行的 web 服务，我们需要一个小工具来运行服务器:

```
pub struct Server {
    pub started: AtomicBool,
}

impl Server {
    pub fn new() -> Server {
        Server {
            started: AtomicBool::new(false),
        }
    }

    pub async fn init_server(&mut self) {
        if !self.started.load(Ordering::Relaxed) {
            thread::spawn(move || {
                let rt = tokio::runtime::Runtime::new().expect("runtime starts");
                rt.spawn(run());
                loop {
                    thread::sleep(Duration::from_millis(100_000));
                }
            });
            delay_for(Duration::from_millis(100)).await;
            self.started.store(true, Ordering::Relaxed);
        }
    }
}

lazy_static! {
    pub static ref MOCK_HTTP_SERVER: RwLock<WiremockServer> = RwLock::new(WiremockServer::new());
    static ref SERVER: RwLock<Server> = RwLock::new(Server::new());
}

async fn init_real_server() {
    let _ = init_db().await;
    SERVER.write().unwrap().init_server().await;
}

```

基本上，这个想法是启动服务一次，然后在多次测试运行中重用该实例，因为服务本身是无状态的。

为此，我们将创建`Server`抽象，它有一个`init_server`方法。这将在第一次调用时启动 crate 的`run`方法。它还启动 warp web 服务并将`started`设置为 true，这样我们就不会意外地再次启动它。

为了实现这一点，服务器本身在一个新的 Tokio 运行时上的单独线程中启动，而线程永远休眠。服务器启动前可能会有短暂的延迟，这取决于服务在响应前必须进行的设置量。这只会发生一次。

对于测试，我们还将创建一个`init_real_server`助手，它清除数据库并确保服务器启动。

因为我们现在正在运行 web 服务，所以我们需要从外部发送 HTTP 请求，这需要一个 HTTP 客户端。幸运的是，我们有`hyper`。

```
fn http_client() -> HyperClient<HttpsConnector<HttpConnector>> {
    let https = HttpsConnector::new();
    HyperClient::builder().build::<_, Body>(https)
}

```

实际的端到端测试应该是这样的:

```
#[tokio::test]
async fn test_create_and_list_e2e() {
    setup_wiremock().await;
    init_real_server().await;
    let http_client = http_client();

    let req = Request::builder()
        .method(Method::POST)
        .uri("http://localhost:8080/todo")
        .body(Body::empty())
        .unwrap();
    let resp = http_client.request(req).await.unwrap();
    assert_eq!(resp.status(), 200);
    let body_bytes = to_bytes(resp.into_body()).await.unwrap();
    assert_eq!(
        body_bytes,
        r#"{"id":1,"name":"wiremock cat fact","checked":false}"#
    );

    let req = Request::builder()
        .method(Method::GET)
        .uri("http://localhost:8080/todo")
        .body(Body::empty())
        .unwrap();
    let resp = http_client.request(req).await.unwrap();
    assert_eq!(resp.status(), 200);
    let body_bytes = to_bytes(resp.into_body()).await.unwrap();
    assert_eq!(
        body_bytes,
        r#"[{"id":1,"name":"wiremock cat fact","checked":false}]"#
    );
}

#[tokio::test]
async fn test_list_e2e() {
    setup_wiremock().await;
    init_real_server().await;
    let http_client = http_client();

    let req = Request::builder()
        .method(Method::GET)
        .uri("http://localhost:8080/todo")
        .body(Body::empty())
        .unwrap();
    let resp = http_client.request(req).await.unwrap();
    assert_eq!(resp.status(), 200);
    let body_bytes = to_bytes(resp.into_body()).await.unwrap();
    assert_eq!(body_bytes, r#"[]"#);
}

```

这里我们设置了 wiremock，初始化了数据库和服务器，并向`[http://localhost:8080](http://localhost:8080)`发送了一个 HTTP 请求，这是我们的 web 服务运行的 URL。

第二个测试展示了这是如何与多个测试一起工作的。如您所见，数据库每次都被重置，服务器被重用。

多好的旅程啊！你可以在 [GitHub](https://github.com/zupzup/rust-web-e2e-testing) 上找到这个例子的完整代码。

## 结论

测试，尤其是在分布式 web 服务的环境中，是一个复杂的话题。关于如何测试，没有确定的答案，也没有放之四海而皆准的方法。通常，单元、集成和端到端测试的健康组合是一个很好的起点，但是在某些情况下，这些方法中的一种或多种是不切实际的。

出于这个原因，我认为熟悉多种测试技术并理解每种技术的优缺点和权衡是很重要的。

如果您已经完成了本教程，那么您现在应该知道一些使用外部依赖项的集成测试技术，从非常简单的模拟到自动测试完全集成的应用程序。

我们用很少的库来保持它的低技术含量，手动编写许多东西，这很有希望表明这些都不是魔术——或者，坦率地说，甚至不是特别复杂——在引擎盖下。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。