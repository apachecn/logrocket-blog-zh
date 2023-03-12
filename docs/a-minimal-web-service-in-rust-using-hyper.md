# 使用 hyper - LogRocket 博客在 Rust 中编写一个简单的 web 服务

> 原文：<https://blog.logrocket.com/a-minimal-web-service-in-rust-using-hyper/>

根据我的经验，在构建 web 服务时，越简单越好。人们很容易采用一个功能齐全的重量级 web 框架，把它放入其中，并使用其约定胜于配置的方法来“完成工作”。包括我自己在内，许多开发人员已经成功地使用了这种方法。

然而，过了一段时间，我注意到隐藏的复杂性——非常大——有一些缺点。这些缺点包括性能下降、在庞大的可传递依赖关系网络中调试问题造成的时间损失，以及根本不知道发生了什么。同样，从头开始编写任何东西也不是最佳选择。这非常耗时，每一步都有可能引入错误。

我并不是说广泛使用的开源 web 框架是没有错误的——远非如此。但是，至少，有更多的人关注他们，更多的利益相关者积极地发现和修复错误。

在我看来，有一个快乐的媒介。目标是保持较低的复杂性，同时保留将所有东西都放在一个依赖项中所带来的大部分便利和开发速度。

这个最佳点对不同的人来说可能是不同的，因为根据你的经验，你喜欢自己写什么或者使用微依赖会有所不同。但是一般的方法——采用多个小的(额)库并从中构建一个最小的系统——在过去对我来说很有效。一个优点是依赖关系足够小，以至于你实际上可以在有限的时间内，到它们的存储库中去阅读、理解，并且如果必要的话，修复它们。此外，您将更好地了解代码库中有什么(例如，什么可能出错)，什么没有，这对于大型框架来说是很困难的。这使你能够根据你正在解决的问题来定制系统。

您可以根据自己构建系统的需要来定义基本的 API，而不必与框架斗争来完成任务。这需要一定程度的经验来做正确的事情，但是如果你愿意花时间与框架搏斗，这肯定是值得的。

在本教程中，我们将向您展示如何在不使用 web 框架的情况下构建 Rust web 服务。

不过，我们不会从头开始构建一切。对于我们的 HTTP 服务器，我们将使用 [hyper](https://hyper.rs/) ，它在底层使用 [tokio](https://tokio.rs/) 运行时。这两个库都不是最轻量级或最少的选项，但都被广泛使用，无论使用什么库，这里描述的概念都适用。

我们将构建一个基本的 web 服务器，带有一个灵活的路由 API 和一些示例处理程序来展示它。我们的成品决不会按原样准备好投入生产，但是在本教程结束时，您应该对如何扩展它有一个清晰的想法。

## 设置

接下来，您需要一个最新的 Rust 安装(1.39+)和一个发送 HTTP 请求的工具，比如 cURL。

首先，创建一个新的 Rust 项目。

```
cargo new rust-minimal-web-example
cd rust-minimal-web-example

```

接下来，编辑`Cargo.toml`文件并添加以下依赖项。

```
[dependencies]
futures = { version = "0.3.6", default-features = false, features = ["async-await"] }
hyper = "0.13"
tokio = { version = "0.2", features = ["macros", "rt-threaded"] }
serde = {version = "1.0", features = ["derive"] }
serde_json = "1.0"
route-recognizer = "0.2"
bytes = "0.5"
async-trait = "0.1"

```

对于一个“最小”的 web 应用程序来说，这是相当多的依赖项。那是怎么回事？

因为我们没有使用一个完整的 web 框架，而是试图构建我们自己的由几个微库组成的系统，所以整体复杂性仍然较低，即使直接依赖的数量增加了。

我们担心的不一定是直接依赖的数量，而是传递依赖的数量以及将它们粘合在一起的代码量。

因为我们使用`hyper`作为异步 HTTP 服务器，所以我们也需要一个异步运行时。在这个例子中，我们使用了`tokio`，但是我们也可以使用一个轻量级的解决方案，比如 [smol](https://github.com/stjepang/smol) 。

`serde`和`serde_json`依赖项对于处理传入的 JSON 是必要的。如果我们想把所有东西都最小化的话，我们也可以不使用 [nanoserde](https://github.com/not-fl3/nanoserde) 。`route-recognizer`机箱是一个非常小的轻量级路由器，它也可以处理带有`/product/:product_id`等参数的路径。

对于剩余的库，即`bytes`、`async-trait`和`futures`，我们需要它们来构建我们的路由器。它们很可能是我们已经在使用的任何东西的传递依赖，所以它们不会增加任何重量，也不会特别重。在`futures`的情况下，我们也可以使用[期货-建兴](https://github.com/stjepang/futures-lite)箱。

## 处理器 API

让我们自下而上地构建它，首先看看我们希望用于处理程序的 API。然后，我们将实现一个路由器，并在最后，把一切放在一起。

在本例中，我们将创建三个处理程序:

*   `GET /test`，一个基本的处理程序，它返回一个字符串来表明它工作正常
*   `POST /send`，一个需要 JSON 有效负载的处理程序，如果它无效，将返回一个错误
*   一个简单的处理程序，展示了我们如何处理路径参数

让我们在`handler.rs`中实现它们，看看我们想要为它们创建的 API。

```
use crate::{Context, Response};
use hyper::StatusCode;
use serde::Deserialize;

pub async fn test_handler(ctx: Context) -> String {
    format!("test called, state_thing was: {}", ctx.state.state_thing)
}

#[derive(Deserialize)]
struct SendRequest {
    name: String,
    active: bool,
}

pub async fn send_handler(mut ctx: Context) -> Response {
    let body: SendRequest = match ctx.body_json().await {
        Ok(v) => v,
        Err(e) => {
            return hyper::Response::builder()
                .status(StatusCode::BAD_REQUEST)
                .body(format!("could not parse JSON: {}", e).into())
                .unwrap()
        }
    };

    Response::new(
        format!(
            "send called with name: {} and active: {}",
            body.name, body.active
        )
        .into(),
    )
}

pub async fn param_handler(ctx: Context) -> String {
    let param = match ctx.params.find("some_param") {
        Some(v) => v,
        None => "empty",
    };
    format!("param called, param was: {}", param)
}

```

`test_handler`非常简单，但是我们已经看到了一个重要的概念:T1。使用请求状态(如主体、查询参数、标题等)。)在一个处理程序中，我们需要某种方法把它放进去。

此外，根据我们希望如何构建系统，我们可能希望让某些共享系统(HTTP 客户端或数据库存储库)对处理程序可用。在这个例子中，我们将使用一个`Context`对象来封装这些东西。在这种情况下，我们返回`ctx.state.state_thing`变量的内容，这是某个实际应用程序状态的虚拟版本。我们稍后会看到它是如何实现的，但是它包含了处理程序完成工作可能需要的所有信息。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

对于`send_handler`，我们已经看到`Context`在运行。我们期望在这个处理程序中有一个`SendRequest` JSON 有效负载，所以我们使用`ctx.body_json()`方法将请求体解析为一个`SendRequest`。如果出错，我们只需返回一个 400 错误。

`test_handler`和`send_handler`之间一个有趣的区别是返回类型。我们希望能够返回不同类型的值。在最基本的情况下，一个`String`，或者一个`&'static str`就可以了。在其他情况下——就像在我们的例子中——我们想要返回一个`Result<>`或者一个原始的`Response`。

第三个处理程序是`param_handler`，展示了我们如何使用`Context`来获取路径参数，这些参数在 route 中定义。所有的处理程序都是`async`函数，但是我们也可以手工使用`impl Future`创建一个处理程序。

在`main.rs`中，我们可以为`Context`和一些助手类型添加定义。

```
use route_recognizer::Params;

type Response = hyper::Response<hyper::Body>;
type Error = Box<dyn std::error::Error + Send + Sync + 'static>;

#[derive(Clone, Debug)]
pub struct AppState {
    pub state_thing: String,
}

#[derive(Debug)]
pub struct Context {
    pub state: AppState,
    pub req: Request<Body>,
    pub params: Params,
    body_bytes: Option<Bytes>,
}

impl Context {
    pub fn new(state: AppState, req: Request<Body>, params: Params) -> Context {
        Context {
            state,
            req,
            params,
            body_bytes: None,
        }
    }

    pub async fn body_json<T: serde::de::DeserializeOwned>(&mut self) -> Result<T, Error> {
        let body_bytes = match self.body_bytes {
            Some(ref v) => v,
            _ => {
                let body = to_bytes(self.req.body_mut()).await?;
                self.body_bytes = Some(body);
                self.body_bytes.as_ref().expect("body_bytes was set above")
            }
        };
        Ok(serde_json::from_slice(&body_bytes)?)
    }
}

```

在这里，我们定义了`Response`以避免一些类型化，定义了`Error`，这是一种通用的错误类型。实际上，我们可能希望使用自定义的错误类型在整个应用程序中传播错误。

接下来，我们定义我们的`AppState`结构。在这个例子中，它只是保存了前面提到的虚拟应用程序状态。例如，这个`AppState`还可以保存对缓存、数据库存储库或您希望处理程序访问的其他应用程序范围的内容的共享引用。

`Context`本身只是一个包含`AppState`、传入的`Request`、路径参数(如果有的话)和`body_bytes`的结构。它公开了一个名为`body_json`的函数，该函数设置`body_bytes`并试图将其解析为给定的类型。

我们在这里记忆`body_bytes`,因为在请求的生命周期中，我们可能想要多次访问主体。这样，我们只需要读取和存储它一次(例如，在一个中间件中)。

## 按指定路线发送

现在我们知道了我们的处理程序 API 应该是什么样子，我们需要构建一个路由机制来适应它。

这里显示的方法是 [tide](https://github.com/http-rs/tide) 框架用于路由的简化版本(可能更糟)。最好有一个简单的 API 来定义路由:

```
let mut router: Router = Router::new();
router.get("/test", Box::new(handler::test_handler));
router.post("/send", Box::new(handler::send_handler));
router.get("/params/:some_param", Box::new(handler::param_handler));

```

我们创建一个`router`,并为不同的 HTTP 方法使用助手方法来添加处理程序。在这个例子中，我们将处理函数包装在一个`Box`中，这是一个用于堆分配的指针类型。

我们可以通过创建另一个特征来避免这种情况，该特征对调用者隐藏了这一点复杂性，但是这会使整个示例更加复杂，所以我省略了它。

接下来，我们来看看`router.rs`中的路由器实现。我们将从路由器的定义和一些依赖关系开始。

```
use crate::{Context, Response};
use async_trait::async_trait;
use futures::future::Future;
use hyper::{Method, StatusCode};
use route_recognizer::{Match, Params, Router as InternalRouter};
use std::collections::HashMap;

pub struct Router {
    method_map: HashMap<Method, InternalRouter<Box<dyn Handler>>>,
}

```

路由器只是一个结构体，它在内部保存一个`method_map`。这只是一个`HashMap`，它通过注册的路由的 HTTP 方法来分隔它们。实际上，我们可能会为此使用一个更有效的`HashMap`实现，比如 [fnv](https://github.com/servo/rust-fnv) ，但是这对于我们的例子来说很好。

该映射的条目属于类型`InternalRouter`，这是使用`route_recognizer`创建的路由器，每个路由器保存类型`Box<dyn Handler>`的值，我们接下来将会看到。

```
#[async_trait]
pub trait Handler: Send + Sync + 'static {
    async fn invoke(&self, context: Context) -> Response;
}

#[async_trait]
impl<F: Send + Sync + 'static, Fut> Handler for F
where
    F: Fn(Context) -> Fut,
    Fut: Future + Send + 'static,
    Fut::Output: IntoResponse,
{
    async fn invoke(&self, context: Context) -> Response {
        (self)(context).await.into_response()
    }
}

```

这有点复杂。为了将对我们的处理函数的引用放在路由器内部，这样我们就可以将请求`Context`传递给它，我们定义了`Handler`特征。

然后，我们为`F`实现这个`Handler`特性，其中`F`是一个接受`Context`并返回`Future`的函数(就像我们的处理程序应该做的那样)。

此外，我们定义这些期货有一个实现`IntoResponse`作为返回类型的类型。

特征看起来是这样的:

```
pub trait IntoResponse: Send + Sized {
    fn into_response(self) -> Response;
}

impl IntoResponse for Response {
    fn into_response(self) -> Response {
        self
    }
}

impl IntoResponse for &'static str {
    fn into_response(self) -> Response {
        Response::new(self.into())
    }
}

impl IntoResponse for String {
    fn into_response(self) -> Response {
        Response::new(self.into())
    }
}

```

这个简单的特征就是我们能够从我们的处理程序返回`String`和`'static str`和`Response`的原因。我们可以在这里添加任意的其他类型，比如`Result<T,E>`，它会以一种很好的方式自动处理并返回错误。

但是回到`Handler`特征。该特征只包含一个函数`invoke`，它是向我们的处理程序传递一个`Context`的间接方式。`invoke`函数简单地用给定的上下文调用 self(处理函数)，等待未来，并将结果转化为响应。

有了这个，我们可以添加任何异步函数，它接受一个`Context`并返回一个`IntoResponse`给我们的路由器。很好。

接下来我们来看看`Router`的实现。

```
pub struct RouterMatch<'a> {
    pub handler: &'a dyn Handler,
    pub params: Params,
}

impl Router {
    pub fn new() -> Router {
        Router {
            method_map: HashMap::default(),
        }
    }

    pub fn get(&mut self, path: &str, handler: Box<dyn Handler>) {
        self.method_map
            .entry(Method::GET)
            .or_insert_with(InternalRouter::new)
            .add(path, handler)
    }

    pub fn post(&mut self, path: &str, handler: Box<dyn Handler>) {
        self.method_map
            .entry(Method::POST)
            .or_insert_with(InternalRouter::new)
            .add(path, handler)
    }

    pub fn route(&self, path: &str, method: &Method) -> RouterMatch<'_> {
        if let Some(Match { handler, params }) = self
            .method_map
            .get(method)
            .and_then(|r| r.recognize(path).ok())
        {
            RouterMatch {
                handler: &**handler,
                params,
            }
        } else {
            RouterMatch {
                handler: &not_found_handler,
                params: Params::new(),
            }
        }
    }
}

async fn not_found_handler(_cx: Context) -> Response {
    hyper::Response::builder()
        .status(StatusCode::NOT_FOUND)
        .body("NOT FOUND".into())
        .unwrap()
}

```

`new`函数创建一个带有空`method_map`的`Router`。然后，我们为`get`和`post`添加助手。为简洁起见，`put`、`delete`等的帮助者。在这里被省略了，但是它们只是更多的相同。

在这些助手中，我们查看`method_map`是否已经有了给定`Method`的条目，如果没有，就在其中创建一个新的路由器。无论如何，具有`path`和`handler`功能的条目被添加到内部的`route_recognizer`路由器中。

这就是我们添加路由的方式，但是我们如何进行实际的路由呢？这就是`route`函数的用武之地。它被传入的`path`和请求`Method`调用，并返回一个`RouterMatch`。`RouterMatch`结构保存对返回的处理函数的引用和由`route_recognizer`计算的路径参数，如果有的话。

基本上，`method_map`被要求使用给定的 HTTP 方法获得一个条目，底层内部路由器被要求使用给定的路径获得一个路由。内部路由器返回一个`Match`。`Match`包括处理程序和路径参数，它们被返回并包装在一个`RouterMatch`中。`&**handler`语法可能看起来有点奇怪，但是我们在这里需要它；因为我们得到了一个`&Box<dyn Handler>`并想将其转换成一个`&'a dyn Handler`，我们需要解引用`&`和`Box`，并使用`&`再次引用结果。如果什么都没有找到，我们简单地返回一个`404 NOT FOUND`。

## 把所有的放在一起

路由器已经就位，`Context`也在那里，我们的处理程序已经准备就绪。剩下唯一要做的就是把所有的东西连在一起，并希望它能工作。

首先，`main.rs`中的`main`功能:

```
use bytes::Bytes;
use hyper::{
    body::to_bytes,
    service::{make_service_fn, service_fn},
    Body, Request, Server,
};
use route_recognizer::Params;
use router::Router;
use std::sync::Arc;

mod handler;
mod router;

#[tokio::main]
async fn main() {
    let some_state = "state".to_string();

    let mut router: Router = Router::new();
    router.get("/test", Box::new(handler::test_handler));
    router.post("/send", Box::new(handler::send_handler));
    router.get("/params/:some_param", Box::new(handler::param_handler));

    let shared_router = Arc::new(router);
    let new_service = make_service_fn(move |_| {
        let app_state = AppState {
            state_thing: some_state.clone(),
        };

        let router_capture = shared_router.clone();
        async {
            Ok::<_, Error>(service_fn(move |req| {
                route(router_capture.clone(), req, app_state.clone())
            }))
        }
    });

    let addr = "0.0.0.0:8080".parse().expect("address creation works");
    let server = Server::bind(&addr).serve(new_service);
    println!("Listening on http://{}", addr);
    let _ = server.await;
}

```

我们创建一个`Router`并添加一些路线。然后，我们将`router`放入一个`Arc`——一个可以跨线程共享的智能指针——并使用由`hyper`提供的`make_service_fn`函数来定义传入请求应该发生什么。

在服务闭包内部，我们创建了`AppState`，在其中克隆了预定义的伪字符串。我们还需要在这个闭包内克隆我们的共享路由器，然后将它传递给`async`块。这是必要的，因为`async`块的内部可以而且将会在以后的时间里在许多不同的线程上执行，所以我们需要确保我们赋予它的所有东西都有足够长的寿命。

在`async`块中，我们使用`service_fn`来定义每个传入请求应该发生什么。我们提供了一个闭包，将我们的路由器和状态移动到其中，并调用`route()`函数，我们将进一步研究这个函数，将路由器、传入请求和应用程序状态传递给它。

之后，只剩下一些`hyper`样板文件，它告诉服务器从哪个端口开始，并实际启动它。

最后要看的是`route()`函数。

```
async fn route(
    router: Arc<Router>,
    req: Request<hyper::Body>,
    app_state: AppState,
) -> Result<Response, Error> {
    let found_handler = router.route(req.uri().path(), req.method());
    let resp = found_handler
        .handler
        .invoke(Context::new(app_state, req, found_handler.params))
        .await;
    Ok(resp)
}

```

这是我们从传入请求到处理函数的地方。我们使用路由器的`route`函数和请求路径和方法到达`RouterMatch`。然后，我们在返回的处理程序上调用`.invoke()`，给它一个新的`Context`对象，包含应用程序状态、请求和来自`RouterMatch`的`params`。我们等待处理程序的未来并返回响应。

这是一个简单的路由功能，但是您可以想象添加一个`CORS`中间件，或者任何种类的中间件，这里是
——比如日志。您还可以在这里进行授权处理，检查传入的`Authorization`头并确保调用者可以访问所请求的资源。

就是这样！让我们通过执行`cargo run`并使用`curl`向它发送请求来看看它是否工作。

```
curl http://localhost:8080/test
test called, state_thing was: state

curl http://localhost:8080/params/1234
param called, param was: 1234

curl -X POST http://localhost:8080/send -d '{"name": "chip", "active": true }'
send called with name: chip and active: true

curl -X POST http://localhost:8080/send -d '{"name": fsdfds, "active": true }'
HTTP/1.1 400 Bad Request
could not parse JSON: expected ident at line 1 column 11

```

非常好！你可以在 [GitHub](https://github.com/zupzup/rust-minimal-web-service-hyper) 找到完整的示例代码。

## 结论

在我看来，简单是构建 web 服务和软件的核心价值。代码库越大，依赖的软件包越多，就越难处理系统固有的和附带的复杂性。这可能会导致性能下降和您从未见过和不理解的细微错误。在许多情况下，它可以引导您尝试使用针对一般用途而优化的工具来做一些特定的事情。

找到一个最佳点需要实验和勇气。这完全取决于你自己(或你的团队)愿意接受的复杂程度，以及你试图实现的项目。

你不需要自己构建所有的东西。事实上，对于某些部分——尤其是像加密库这样的安全关键的东西——您应该总是使用经过实战检验的解决方案。

使用小型、轻量级的库和一些自己编写的代码来组成一个可以被认为是最小的(或接近最小的)系统，可以帮助提高性能、可维护性和代码质量。我强烈推荐这种方法，并希望看到更多的开发人员采用它。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。