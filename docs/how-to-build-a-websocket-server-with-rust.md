# 如何用 Rust 构建 WebSocket 服务器

> 原文：<https://blog.logrocket.com/how-to-build-a-websocket-server-with-rust/>

在[之前的一篇文章](https://blog.logrocket.com/create-an-async-crud-web-service-in-rust-with-warp/)中，我们介绍了如何使用 [warp](https://github.com/seanmonstar/warp) web 框架创建异步 CRUD web 服务。warp 的另一个很酷的地方是它支持 WebSockets。

在本教程中，我们将演示如何构建一个基本的消息中继服务，客户端可以通过 WebSockets 连接到这个服务。

在客户端注册了他们的`user_id`之后，他们会获得一个唯一的连接 ID，可以用来通过 WebSockets 进行连接。然后，他们能够接收实时消息，这些消息会发布在服务上。

客户也可以通过 WebSocket 连接交流他们感兴趣的话题。例如，如果他们订阅了主题“猫”和“狗”，他们只会得到带有这些主题的已发布消息。发布的消息可以直接发送给特定的`user_id`或广播给所有用户。

## 设置

接下来，您所需要的只是一个相当新的 Rust 安装(1.39+)。测试 WebSockets 连接的工具，如[websocket.org](https://www.websocket.org/echo.html)或 [websocat](https://github.com/vi/websocat) ，以及发送 HTTP 请求的工具，如 [curl](https://curl.haxx.se/) 或 [Postman](https://www.postman.com/) ，也会很有用。

首先，创建一个新的 Rust 项目。

```
cargo new warp-ws-example
cd warp-ws-example

```

接下来，编辑`Cargo.toml`文件并添加您需要的依赖项。

```
[dependencies]
tokio = { version = "0.2", features = ["macros", "sync"] }
warp = "0.2"
serde = {version = "1.0", features = ["derive"] }
serde_json = "1.0"
futures = { version = "0.3", default-features = false }
uuid = { version = "0.4", features = ["serde", "v4"] }

```

这里没有什么特别令人惊讶的。我们需要 warp 和 Tokio 来运行 web 服务器，需要 Serde 来序列化和反序列化 JSON。`uuid`库将用于创建连接 ID，而`futures`库将在处理 WebSocket 的异步数据流时很有用。

## 数据结构

在我们开始之前，让我们看看一些数据结构，我们将使用这些数据结构来获得更多的上下文。

首先，`Client`是这个应用程序的核心。

```
pub struct Client {
  pub user_id: usize,
  pub topics: Vec<String>,
  pub sender: Option<mpsc::UnboundedSender<std::result::Result<Message, warp::Error>>>,
}

```

在这种情况下，客户端和用户是有区别的。一个用户可以有几个客户端——例如，假设同一个用户使用移动应用程序和 web 应用程序连接到 API。

每个客户都有一个`user_id`，一个他们感兴趣的`topics`列表，和一个`sender`。此发送方是 MPSC(多生产者、单消费者)通道的发送方。稍后我们将了解发送者，但是现在可以说，它用于通过 WebSockets 向这个连接的客户端发送消息。

接下来的数据结构在 REST API 中用于注册用户和广播事件。

```
pub struct RegisterRequest {
  user_id: usize,
}

pub struct RegisterResponse {
  url: String,
}

pub struct Event {
  topic: String,
  user_id: Option<usize>,
  message: String,
}

```

这些都不是特别有意思。如上所述，事件被标记了一个特定的主题，可以直接发给用户，在这种情况下，`user_id`将被显式设置。

最后但同样重要的是，我们需要一种方式让客户交流他们感兴趣的话题。如果他们不明确设置主题，他们将默认为*猫*——因为谁不喜欢这些？

```
pub struct TopicsRequest {
  topics: Vec<String>,
}

```

## 启动并运行服务器

现在你已经有了一个我们将要构建的心智模型，让我们开始用我们需要的所有路线来构建一个 warp web 服务器。

```
mod handler;
mod ws;

type Result<T> = std::result::Result<T, Rejection>;
type Clients = Arc<Mutex<HashMap<String, Client>>>;

#[tokio::main]
async fn main() {
  let clients: Clients = Arc::new(Mutex::new(HashMap::new()));

  let health_route = warp::path!("health").and_then(handler::health_handler);

  let register = warp::path("register");
  let register_routes = register
    .and(warp::post())
    .and(warp::body::json())
    .and(with_clients(clients.clone()))
    .and_then(handler::register_handler)
    .or(register
      .and(warp::delete())
      .and(warp::path::param())
      .and(with_clients(clients.clone()))
      .and_then(handler::unregister_handler));

  let publish = warp::path!("publish")
    .and(warp::body::json())
    .and(with_clients(clients.clone()))
    .and_then(handler::publish_handler);

  let ws_route = warp::path("ws")
    .and(warp::ws())
    .and(warp::path::param())
    .and(with_clients(clients.clone()))
    .and_then(handler::ws_handler);

  let routes = health_route
    .or(register_routes)
    .or(ws_route)
    .or(publish)
    .with(warp::cors().allow_any_origin());

  warp::serve(routes).run(([127, 0, 0, 1], 8000)).await;
}

fn with_clients(clients: Clients) -> impl Filter<Extract = (Clients,), Error = Infallible> + Clone {
  warp::any().map(move || clients.clone())
}

```

这相当多的代码。让我们一步一步来。

如上所述，我们希望客户端通过 WebSockets 连接到我们的服务。为了适应这种情况，我们需要一种方法来跟踪服务中的这些客户端。

我们可以用很多方法解决这个问题，但是在这个简单的例子中，我们将简单地把它们保存在内存中——更具体地说，在一个`HashMap`内。然而，因为客户端的这个集合需要被整个系统中的几个参与者访问和改变(例如，注册新客户端、发送消息、更新主题等。)，我们需要确保它可以在线程之间安全地传递，并且可以毫无问题地并发修改。

这就是为什么`Clients`类型是我们上面定义的第一个东西——一个`Arc<Mutex<HashMap<String, Client>>>`。这种类型可能看起来很可怕，但本质上，我们只是希望在互斥体后面有到客户端的连接 id 映射，这样在任何给定的时间它只能被代码的一部分访问。为了安全地将它传递给其他线程，我们将它包装成一个`Arc`，这是一个原子智能指针类型，以线程安全的方式提供共享所有权。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

到目前为止，一切顺利。在主函数中，这个`Clients`数据结构被初始化，随后是一些路线定义:

*   GET /health —指示服务是否已启动
*   POST /register —在应用程序中注册客户端
*   删除/注册/{客户端 id} —注销具有 a ID 的客户端
*   发布/发布—向客户端广播事件
*   GET/ws—web socket 端点

创建`with_clients`过滤器是为了将客户端传递到这些路由中。这会克隆一个指向客户端的指针，并将其传递到使用它们的路由中。

除此之外，所有的处理器(除了 WebSockets 之外)都是非常基本的。对于`/ws`路由，使用了`warp::ws()`过滤器，这使得在处理程序中将连接升级为 WebSocket 连接成为可能。

在`main`结束时，路由被合并到一个支持 CORS 的路由器中，服务器在端口 8000 上启动。

## 客户注册

现在服务器已经设置好了，让我们看看上面定义的路由的处理程序，从客户机注册开始。

为了让代码更好阅读，让我们将处理程序放在一个不同的文件中，名为`handler.rs`。

让我们从注册一个新的客户机开始，带有`user_id`的 JSON 主体被发送给服务。

```
pub async fn register_handler(body: RegisterRequest, clients: Clients) -> Result<impl Reply> {
  let user_id = body.user_id;
  let uuid = Uuid::new_v4().simple().to_string();

  register_client(uuid.clone(), user_id, clients).await;
  Ok(json(&RegisterResponse {
    url: format!("ws://127.0.0.1:8000/ws/{}", uuid),
  }))
}

async fn register_client(id: String, user_id: usize, clients: Clients) {
  clients.lock().await.insert(
    id,
    Client {
      user_id,
      topics: vec![String::from("cats")],
      sender: None,
    },
  );
}

```

注册新客户端的过程很简单。

首先，创建一个新的`uuid`。这个 ID 用于创建一个新的`Client`,带有一个空的发送者、用户 ID 和默认主题。这些只是被添加到客户端数据结构中，向用户返回一个带有`uuid`的 WebSocket URL。用户可以使用此 URL 通过 WebSockets 连接客户端。

要将新创建的客户机添加到共享客户机结构中，我们需要`lock()`互斥体。因为我们在这种情况下使用了 Tokio 的异步互斥，所以这是一个`future`,因此应该等待。

获得锁后，只需将新客户端`insert()`发送到底层`HashMap`。

一旦锁超出范围，它就会被丢弃，其他人可以再次访问该数据结构。

太好了！我们可以这样称呼`register`端点:

```
curl -X POST 'http://localhost:8000/register' -H 'Content-Type: application/json' -d '{ "user_id": 1 }'
{"url":"ws://127.0.0.1:8000/ws/625ac78b88e047a1bc7b3f8459702078"}

```

注销客户端甚至更容易。

```
pub async fn unregister_handler(id: String, clients: Clients) -> Result<impl Reply> {
  clients.lock().await.remove(&id);
  Ok(StatusCode::OK)
}

```

具有给定 ID(上面生成的`uuid`)的客户机被简单地从`Clients`数据结构中删除。

您可能会问自己，“如果您已经使用这个 ID 通过 WebSockets 连接了，会发生什么？”它们只是断开连接，一切都在服务端关闭和清理。

这是可行的，因为移除`Client`会使它超出范围，这意味着它会被丢弃。这反过来会丢弃客户端内的通道发送端，从而关闭通道并触发错误。我们稍后会看到，这是一个信号，我们可以用它来关闭我们这边的连接。

调用取消注册的工作方式如下:

```
curl -X DELETE 'http://localhost:8000/register/e2fa90682255472b9221709566dbceba'

```

## 通过 WebSocket 连接

既然客户端可以注册和注销，那么是时候让它们连接到我们的实时 WebSocket 端点了。

让我们从训导员开始。

```
pub async fn ws_handler(ws: warp::ws::Ws, id: String, clients: Clients) -> Result<impl Reply> {
  let client = clients.lock().await.get(&id).cloned();
  match client {
    Some(c) => Ok(ws.on_upgrade(move |socket| ws::client_connection(socket, id, clients, c))),
    None => Err(warp::reject::not_found()),
  }
}

```

首先，根据`Clients`数据结构检查给定的客户机 ID。如果不存在这样的客户端，将返回 404 错误。如果找到一个客户端，`ws.on_upgrade()`用于将连接升级到 WebSocket 连接，在这里调用`ws::client_connection`函数。

```
pub async fn client_connection(ws: WebSocket, id: String, clients: Clients, mut client: Client) {
  let (client_ws_sender, mut client_ws_rcv) = ws.split();
  let (client_sender, client_rcv) = mpsc::unbounded_channel();

  tokio::task::spawn(client_rcv.forward(client_ws_sender).map(|result| {
    if let Err(e) = result {
      eprintln!("error sending websocket msg: {}", e);
    }
  }));

```

这是 WebSockets 逻辑的核心部分，所以让我们慢慢来。该函数通过`warp::ws filter`获得一个传递给它的`warp::ws::WebSocket`。你可以粗略地认为这是升级的 WebSocket 连接和一个异步的`Stream`和`Sink`。

`futures::StreamExt`的`split()`函数将它分成一个流和一个接收器，可以认为是发送者和接收者。

接下来，创建一个无界的 MPSC 通道向客户端发送消息。此外，如果您还记得`Client`对象上的`sender`，`client_sender`正是通道的发送者部分。

下一步是生成一个 Tokio 任务，其中使用`futures::StreamExt::forward()`将发送到客户端接收者部分(`client_rcv`)的消息传播到 WebSocket 流的发送者部分(`client_ws_sender`)。如果消息发送失败，我们记录错误。在另一个不同的场景中，根据错误情况，在此时关闭连接也是可行的。

下一步是用新创建的发送者更新客户机。

```
client.sender = Some(client_sender);
clients.lock().await.insert(id.clone(), client);

println!("{} connected", id);

```

传入的客户端获得发送通道的发送方部分，并且数据结构被更新。从这一点开始，如果有人将任何东西发送到通道的发送端，它将通过 WebSocket 转发到客户端，因此我们将客户端记录为已连接。

现在我们可以向连接的客户端发送数据，但是我们也希望能够在 WebSocket 上接收数据。首先，客户端应该能够向我们发送 pings 来检查连接是否正常，但我们也希望客户端能够通过 WebSocket 更改他们的首选主题。

```
while let Some(result) = client_ws_rcv.next().await {
  let msg = match result {
    Ok(msg) => msg,
    Err(e) => {
      eprintln!("error receiving ws message for id: {}): {}", id.clone(), e);
      break;
      }
  };
  client_msg(&id, msg, &clients).await;
}

clients.lock().await.remove(&id);
  println!("{} disconnected", id);
}

```

要从 WebSocket 接收消息，我们可以使用 WebSocket 的接收端(`Stream`)。与任何其他异步流一样，我们可以简单地在循环中使用`.next().await`来等待值。

当接收到一条消息时，这条消息被转发给`client_msg`函数，我们接下来将会看到这个函数。但是，可能更有趣的是，如果发生了错误，错误会被记录下来，我们会跳出循环，导致函数结束。

到达这里的唯一方法是如果有一个错误。在这种情况下，我们希望关闭连接，并从共享数据结构中删除客户机。

如果一个客户端没有注册一个正在运行的连接，我们也将在这里结束。唯一的区别是客户端已经被删除，从而触发连接错误。

为了完成 WebSockets 部分，让我们看一下`client_msg`函数，它处理来自客户端的传入消息。

```
async fn client_msg(id: &str, msg: Message, clients: &Clients) {
  println!("received message from {}: {:?}", id, msg);
  let message = match msg.to_str() {
    Ok(v) => v,
    Err(_) => return,
  };

  if message == "ping" || message == "ping\n" {
    return;
  }

  let topics_req: TopicsRequest = match from_str(&message) {
    Ok(v) => v,
    Err(e) => {
      eprintln!("error while parsing message to topics request: {}", e);
      return;
    }
  };

  let mut locked = clients.lock().await;
  match locked.get_mut(id) {
    Some(v) => {
      v.topics = topics_req.topics;
    }
    None => return,
  };
}

```

首先，传入的消息被记录下来，让我们能够看到将要测试的内容。接下来，消息被转换成字符串。如果不可能，我们放弃，因为我们只对字符串消息感兴趣。

如前所述，客户端应该能够向我们发送 ping，所以如果消息是“ping”，我们只需返回。我们也可以发送回一个“pong”，或者在这种情况下我们想要使用的任何东西。

我们感兴趣的另一种消息是`TopicsRequests`，当客户想要改变他们喜欢的话题时，就会发送这种消息。要做到这一点，客户端必须向 WebSocket 发送一些 JSON，然后将它解析成一个主题列表。然后在客户端的数据结构中更新这个列表。从那时起，客户端将只根据他们的新主题首选项接收消息。

不错！WebSockets 现在可用于服务中的客户端。为了测试这一点，要么使用像 websocket.org 这样的网站，要么使用像 websocat 这样的工具。

```
websocat -t ws://127.0.0.1:8000/ws/2fd7f6e2e3294057b714c6c1c5aa827d
{ "topics": ["cats", "dogs"] }

```

## 向客户端转发消息

快好了！我们仍然缺少的唯一一块拼图是向连接的客户端广播消息的能力。这是使用`/publish`端点完成的。

```
pub async fn publish_handler(body: Event, clients: Clients) -> Result<impl Reply> {
  clients
    .lock()
    .await
    .iter_mut()
    .filter(|(_, client)| match body.user_id {
      Some(v) => client.user_id == v,
      None => true,
    })
    .filter(|(_, client)| client.topics.contains(&body.topic))
    .for_each(|(_, client)| {
      if let Some(sender) = &client.sender {
        let _ = sender.send(Ok(Message::text(body.message.clone())));
      }
    });

  Ok(StatusCode::OK)
}

```

当任何人想要向客户端广播消息时，如果设置了`user_id`，我们必须迭代客户端的数据结构，过滤掉所有不是指定用户的客户端。

我们只对订阅消息主题的客户感兴趣。我们使用每个客户端的`sender`沿着管道传输消息。

发布端点可以这样调用:

```
curl -X POST 'http://localhost:8000/publish' \
-H 'Content-Type: application/json' \
-d '{"user_id": 1, "topic": "cats", "message": "are awesome"}'

```

该消息将被发送给具有`1`的`user_id`的连接客户端，订阅主题`cats`。

完美！我们着手构建的一切都已完成，并且运行良好。

你可以在 [GitHub](https://github.com/zupzup/warp-websockets-example) 上找到这个例子的完整代码。

## 结论

WebSockets 非常棒，无论是对于交互式的实时 web 体验，还是与 REST APIs 相结合来更新 UI，而不需要客户端轮询更改。

warp 使 WebSockets 易于使用，但需要注意的是，根据不同的用例，需要一些 Rust 中异步流和并发的背景知识。但是由于这些在 Rust web 开发领域都是非常有用的技能，这看起来很合理。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。