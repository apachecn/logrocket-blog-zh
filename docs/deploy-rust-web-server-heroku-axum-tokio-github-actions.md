# 了解如何将 Rust web 服务器部署到 Heroku

> 原文：<https://blog.logrocket.com/deploy-rust-web-server-heroku-axum-tokio-github-actions/>

`axum`是来自[T](https://tokio.rs/)okio 项目的一个异步 web 框架。它被设计成 [hyper](https://hyper.rs/) 上的一个非常薄的层，并且与[T](https://docs.rs/tower/latest/tower/)ower 生态系统兼容，允许使用`tower-http`和`tower-web`提供的各种中间件。

在这篇文章中，我们将为您的项目介绍如何使用`axum`、Tokio 和 GitHub 动作将 Rust web 服务器部署到 Heroku。

*向前跳转:*

## 使用`axum`设置服务器

`axum`提供一个用户友好的界面，在服务器上安装路由并传递处理函数。

`axum`将处理对 TCP 套接字连接的侦听，并将 HTTP 请求多路复用到正确的处理器，并且正如我提到的，还允许使用由上述塔式生态系统提供的各种中间件。

```
use std::{net::SocketAddr, str::FromStr};

use axum::{
    http::StatusCode,
    response::IntoResponse,
    routing::get,
    Router,
    Server,
};

// running the top level future using tokio main
#[tokio::main]
async fn main() {
    // start the server
    run_server().await;
}
async fn run_server() {
    // Router is provided by Axum which allows mounting various routes and handlers.
    let app = Router::new()
        // `route` takes `/` and MethodRouter
        .route("/", 
        // get function create a MethodRouter for a `/` path from the `hello_world`
        get(hello_world))

    // create a socket address from the string address
    let addr = SocketAddr::from_str("0.0.0.0:8080").unwrap();
    // start the server on the address
    // Server is a re-export from the hyper::Server
    Server::bind(&addr)
    // start handling the request using this service
        .serve(app.into_make_service())
        // start polling the future
        .await
        .unwrap();
}

// basic handler that responds with a static string
// Handler function is an async function whose return type is anything that impl IntoResponse
async fn hello_world() -> impl IntoResponse {
    // returning a tuple with HTTP status and the body
    (StatusCode::OK, "hello world!")
}

```

这里，`Router`结构提供了一个`route`方法来添加新的路由和相应的处理程序。在上面的例子中，`get`被用来为`/`路由创建一个 get 处理程序。

`hello_world`是一个处理程序，它返回一个带有 HTTP 状态和主体的元组。这个元组实现了由`axum`提供的`IntoResponse`特征。

`Server`结构是`hyper::Server`的重新导出。随着`axum`试图成为 hyper 的一个非常薄的包装器，你可以期待它提供与`hyper`相当的性能。

## 处理发布请求

`post`函数用于在提供的路径上创建一个 POST route 与`get`函数一样，`post`也接受一个处理程序并返回`MethodRoute`。

```
let app = Router::new()
        // `route` takes `/` and MethodRouter
        .route("/", 
        // post function create a MethodRouter for a `/` path from the `hello_name`
        post(hello_name))

```

`axum`提供现成的 JSON 序列化和反序列化。`Json`类型实现了`FromRequest`和`IntoResponse`特征，允许您序列化响应和反序列化请求体。

```
// the input to our `hello_name` handler
// Deserialize trait is required for deserialising bytes to the struct
#[derive(Deserialize)]
struct Request {
    name: String,
}

// the output to our `hello_name` handler
// Serialize trait is required for serialising struct in bytes

#[derive(Serialize)]
struct Response{
    greet:String
}

```

`Request`结构实现了`serde_json`用来反序列化请求体的`Deserialize`特征，而`Response`结构实现了`Serialize`特征来序列化响应。

```
async fn hello_name(
    // this argument tells axum to parse the request body
    // as JSON into a `Request` type
    Json(payload): Json<Request>
) -> impl IntoResponse {
    // insert your application logic here
    let user = Response {
        greet:format!("hello {}",payload.name)
    };
    (StatusCode::CREATED, Json(user))
}

```

`Json`是由`axum`提供的一种类型，它在内部实现了`FromRequest`特征，并使用`serde`和`serde_json`机箱在对`Request`结构的请求中反序列化 JSON 主体。

与 GET 请求处理程序类似，POST 处理程序也可以返回一个带有响应状态代码和响应主体的元组。`Json`还实现了`IntoResponse`特征，允许它将`Response`结构转换成 JSON 响应。

Axum 提供了提取器作为在服务器上共享状态的抽象，并允许处理程序访问共享数据。

```
// creating common state
let app_state = Arc::new(Mutex::new(HashMap::<String,()>::new()));

    let app = Router::new()
        // `GET /` goes to `root`
        .route("/", get(root))
        // `POST /users` goes to `create_user`
        .route("/hello", post(hello_name))
        // Adding the state to the router.
        .layer(Extension(app_state));

```

`Extension`包装共享状态，负责与`axum`交互。在上面的例子中，共享状态被包装在`Arc`和`Mutex`中，以同步对内部状态的访问。

```
async fn hello_name(
    Json(payload): Json<Request>,
    // This will extract out the shared state
    Extension(db):Extension<Arc<Mutex<HashMap<String,()>>>>
) -> impl IntoResponse {
    let user = Response {
        greet:format!("hello {}",payload.name)
    };

   // we can use the shared state
    let mut s=db.lock().unwrap();
    s.insert(payload.name.clone(), ());
    (StatusCode::CREATED, Json(user))
}

```

`Extension`还实现了将由`axum`调用的`FromRequest`特征，以从请求中提取共享状态，并将其传递给处理函数。

## GitHub 操作

GitHub 动作可用于测试、构建和部署 Rust 应用程序。在本节中，我们将重点关注 Rust 应用程序的部署和测试。

```
# name of the workflow
name: Rust

# run workflow when the condition is met
on:
# run when code is pushed on the `main` branch
  push:
    branches: [ "main" ]
# run when a pull request to the `main` branch
  pull_request:
    branches: [ "main" ]

# env variables
env:
  CARGO_TERM_COLOR: always

# jobs
jobs:
# job name
  build:
  # os to run the job on support macOS and windows also
    runs-on: ubuntu-latest
# steps for job
    steps:
    # this will get the code and set the git
    - uses: actions/[email protected]
    # run the build
    - name: Build
    # using cargo to build
      run: cargo build --release

    # for deployment
    - name: make dir
    # create a directory
      run: mkdir app
    # put the app in it
    - name: copy
      run: mv ./target/release/axum-deom ./app/axum

    # heroku deployment
    - uses: akhileshns/[email protected]
      with:
      # key from repository secrets
        heroku_api_key: ${{secrets.HEROKU_API_KEY}}
        # name of the Heroku app
        heroku_app_name: "axum-demo-try2"
        # email from which the app is uploaded
        heroku_email: "[email protected]"

        # app directory
        appdir: "./app"

        # start command
        procfile: "web: ./axum"
        # buildpack is like environment used to run the app
        buildpack: "https://github.com/ph3nx/heroku-binary-buildpack.git"

```

GitHub 动作默认提供对 Rust 稳定版本的支持。默认情况下，GitHub Actions 会将 Cargo 和 rustc 安装在所有受支持的操作系统上——这是一个在将代码推送到主分支或创建主分支的 pull 请求时运行的操作。

```
on:
# run when code is pushed on the `main` branch
  push:
    branches: [ "main" ]
# run when a pull request to the `main` branch
  pull_request:
    branches: [ "main" ]

```

工作流将首先检查代码，然后运行 Cargo test 来对代码运行测试。然后，它将使用 cargo-build 构建代码。

Cargo release 将在目标文件夹中创建一个二进制文件，然后这个操作将这个二进制文件从目标文件夹复制到`./app`文件夹，以便在 Heroku 部署步骤中进一步使用，我们现在将继续这个步骤。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## Rust 的 Heroku 部署

Heroku 没有 Rust 的官方构建包，所以 Heroku 没有 Rust 应用的官方构建环境。

因此，我们将使用 GitHub Actions 来构建应用程序，并将其部署到 Heroku。

Heroku 要求每个应用都有一个 buildpack，所以[二进制 buildpack](https://github.com/ph3nx/heroku-binary-buildpack.git) 用于 Rust 应用。Rust 有社区构建包，由于 GitHub 动作已经被用于构建应用程序，直接使用 Heroku 上的二进制构建可以节省时间。

GitHub Actions 市场有一个非常有用的`akhileshns/heroku-deploy`使用 GitHub Actions 部署 Heroku 应用。结合`binary-buildpack`，成为部署代码的有力工具。

```
    - uses: akhileshns/[email protected]
      with:
      # key from repository secrets
        heroku_api_key: ${{secrets.HEROKU_API_KEY}}
        # name of the Heroku app
        heroku_app_name: "axum-demo-try2"
        # email from which the app is uploaded
        heroku_email: "[email protected]"

        # app directory
        appdir: "./app"

        # start command
        procfile: "web: ./axum"
        # buildpack is like environment used to run the app
        buildpack: "https://github.com/ph3nx/heroku-binary-buildpack.git"

```

要使用此操作，需要一个 Heroku API 密钥。可以使用 Heroku 控制台在您的[帐户设置中生成密钥。](https://dashboard.heroku.com/account)

此操作将为您创建并部署应用程序。它获取应用程序的目录并启动应用程序的命令，您还可以指定您想要使用的构建包。

在 Rust 应用程序可以部署到 Heroku 之前，需要进行一些代码更改。目前，该应用程序使用一个`8080`端口，但 Heroku 将提供一个不同的端口供该应用程序使用，因此 Rust 应用程序应读取环境变量`PORT`。

```
    // read the port from env or use the port default port(8080)
    let port = std::env::var("PORT").unwrap_or(String::from("8080"));
    // convert the port to a socket address
    let addr = SocketAddr::from_str(&format!("0.0.0.0:{}", port)).unwrap();
    // listen on the port
    Server::bind(&addr)
        .serve(app.into_make_service())
        .await
        .unwrap();

```

## 结论

是一个非常好的 web 服务器框架，支持更广泛的塔式 rs 生态系统。它允许构建可扩展和可组合的 web 服务，并通过在`hyper`上提供一个薄层来提供性能优势。

GitHub 动作对于 CI/CD 来说非常棒，并且允许执行各种自动化任务，例如在各种平台上构建和测试代码以及生成文档。GitHub 动作还支持缓存货物依赖关系，以加快动作速度。

Heroku 支持自动扩展连续部署，也支持托管资源，例如数据库和存储。GitHub Actions 和 Heroku 是独立于框架的，这意味着同一个动作可以测试和部署用 Rocket 或 Actix web 编写的 Web 服务器——所以可以随意试验任何适合你的东西！

当所有这些工具一起使用时，它们成为开发和托管 Rust web 服务器的杀手组合。我希望你喜欢这个教程，请在下面留下你的经历。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。