# libp2p 教程:在 Rust - LogRocket 博客中构建一个点对点应用程序

> 原文：<https://blog.logrocket.com/libp2p-tutorial-build-a-peer-to-peer-app-in-rust/>

在过去的几年里，很大程度上由于围绕区块链和加密货币的大肆宣传，分散式应用程序获得了相当大的发展势头。对去中心化的兴趣高涨背后的另一个因素是，人们越来越意识到将大部分网络掌握在一小撮公司手中在数据隐私和垄断方面的弊端。

无论如何，即使抛开所有的加密和区块链技术，最近在去中心化软件领域已经有了一些非常有趣的发展。

著名的例子包括[IPFS](https://ipfs.io/)；全新的分布式编码平台[Radicle](https://radicle.xyz/)；去中心化的社交网络[小道消息](https://scuttlebutt.nz/)；以及 [Fediverse](https://fediverse.party/) 内更多的应用，比如[乳齿象](https://joinmastodon.org/)。

在本教程中，我们将向您展示如何使用 Rust 和神奇的 [`libp2p`](https://github.com/libp2p/rust-libp2p) 库构建一个非常简单的点对点应用程序，该库存在于各种语言的不同成熟阶段。

我们将构建一个烹饪食谱应用程序，它具有简单的命令行界面，使我们能够:

*   创建食谱
*   发布食谱
*   列出当地食谱
*   列出我们在网络上发现的其他对等点
*   列出给定同行的已发布食谱
*   列出我们知道的所有同行的所有食谱

我们将在大约 300 行 Rust 中完成这一切。我们开始吧！

## 安装铁锈

接下来，你只需要一个最新的 Rust 安装(1.47+)。

首先，创建一个新的 Rust 项目:

```
cargo new rust-p2p-example
cd rust-p2p-example

```

接下来，编辑`Cargo.toml`文件并添加您需要的依赖项:

```
[dependencies]
libp2p = { version = "0.31", features = ["tcp-tokio", "mdns-tokio"] }
tokio = { version = "0.3", features = ["io-util", "io-std", "stream", "macros", "rt", "rt-multi-thread", "fs", "time", "sync"] }
serde = {version = "=1.0", features = ["derive"] }
serde_json = "1.0"
once_cell = "1.5"
log = "0.4"
pretty_env_logger = "0.4"

```

如上所述，我们将使用 [`libp2p`](https://github.com/libp2p/rust-libp2p) 用于对等网络部分。更具体地说，我们将把它与 Tokio 异步运行时配合使用。我们将使用 [Serde 进行 JSON 序列化和反序列化](https://blog.logrocket.com/json-and-rust-why-serde_json-is-the-top-choice/)，并使用几个助手库来记录和初始化状态。

## 什么是`libp2p`？

[`libp2p`](https://libp2p.io/) 是一组用于构建对等应用程序的协议，它专注于模块化。

有几种语言的库实现，比如 JavaScript、Go 和 Rust。这些库都实现了相同的`libp2p`规范，所以用 Go 构建的`libp2p`客户端可以与用 JavaScript 编写的另一个客户端无缝交互，只要它们在所选的协议栈方面兼容。这些协议涵盖的范围很广，从基本的网络传输协议到安全层协议和多路复用。

在这篇文章中，我们不会深入讨论`libp2p`的细节，但是如果你有兴趣更深入地研究，那么[官方`libp2p`文档](https://docs.libp2p.io/concepts/)为我们将会遇到的各种概念提供了一个非常好的概述。

## `libp2p`如何工作

为了看到`libp2p`的实际效果，让我们启动我们的食谱应用程序。我们将从定义一些我们需要的常量和类型开始:

```
const STORAGE_FILE_PATH: &str = "./recipes.json";

type Result<T> = std::result::Result<T, Box<dyn std::error::Error + Send + Sync + 'static>>;

static KEYS: Lazy<identity::Keypair> = Lazy::new(|| identity::Keypair::generate_ed25519());
static PEER_ID: Lazy<PeerId> = Lazy::new(|| PeerId::from(KEYS.public()));
static TOPIC: Lazy<Topic> = Lazy::new(|| Topic::new("recipes"));

```

我们将把本地食谱存储在一个名为`recipes.json`的简单 JSON 文件中，应用程序希望这个文件与可执行文件在同一个文件夹中。我们还为`Result`定义了一个助手类型，它允许我们传播任意错误。

然后，我们使用`once_cell::Lazy`，来惰性地初始化一些东西。首先，我们使用它来生成一个密钥对和一个从公钥派生的所谓的`PeerId`。我们还创建了一个`Topic`，这是`libp2p`中的另一个关键概念。

这一切意味着什么？简而言之，`PeerId`只是整个对等网络中特定对等体的唯一标识符。我们从密钥对中导出它，以确保它的唯一性。此外，密钥对使我们能够安全地与网络的其他部分进行通信，确保没有人能够冒充我们。

另一方面，`Topic`是来自 Floodsub 的概念，它是`libp2p`的[发布/订阅接口](https://github.com/libp2p/specs/tree/master/pubsub)的实现。一个`Topic`是我们可以`subscribe`向其发送消息的东西——例如，只监听发布/订阅网络上的一部分流量。

我们还需要一些食谱的类型:

```
type Recipes = Vec<Recipe>;

#[derive(Debug, Serialize, Deserialize)]
struct Recipe {
    id: usize,
    name: String,
    ingredients: String,
    instructions: String,
    public: bool,
}

```

我们计划传播的一些信息类型:

```
#[derive(Debug, Serialize, Deserialize)]
enum ListMode {
    ALL,
    One(String),
}

#[derive(Debug, Serialize, Deserialize)]
struct ListRequest {
    mode: ListMode,
}

#[derive(Debug, Serialize, Deserialize)]
struct ListResponse {
    mode: ListMode,
    data: Recipes,
    receiver: String,
}

enum EventType {
    Response(ListResponse),
    Input(String),
}

```

食谱相当简单。它有一个 ID、一个名字、一些成分和执行它的指令。此外，我们添加了一个`public`标志，这样我们就可以区分哪些食谱我们想要分享，哪些想要留给自己。

正如在开始时提到的，有两种方法可以从其他对等体获取列表:从所有对等体或从一个对等体获取列表，这由`ListMode` enum 表示。

`ListRequest`和`ListResponse`类型只是这个类型和使用它们发送的日期的包装器。

enum 区分来自另一个对等体的响应和来自我们自己的输入。我们稍后会看到为什么这种差异是相关的。

### 创建`libp2p`客户端

让我们开始编写`main`函数，在对等网络中建立一个对等点。

```
#[tokio::main]
async fn main() {
    pretty_env_logger::init();

    info!("Peer Id: {}", PEER_ID.clone());
    let (response_sender, mut response_rcv) = mpsc::unbounded_channel();

    let auth_keys = Keypair::<X25519Spec>::new()
        .into_authentic(&KEYS)
        .expect("can create auth keys");

```

我们初始化日志记录并创建一个异步`channel`来在应用程序的不同部分之间进行通信。稍后我们将使用这个通道将来自`libp2p`网络堆栈的响应发送回我们的应用程序来处理它们。

此外，我们为[噪声](https://noiseprotocol.org/)加密协议创建一些认证密钥，我们将使用它们来保护网络中的流量。为此，我们创建一个新的密钥对，并使用`into_authentic`函数用我们的身份密钥对其进行签名。

下一步很重要，涉及到`libp2p`的一些核心概念:创建一个所谓的[运输](http://let%20transp%20=%20TokioTcpConfig::new()%20.upgrade(upgrade::Version::V1)。认证(NoiseConfig::xx(auth *keys)。into* authenticated()) // XX 握手模式，IX 也存在，IK–目前只有 XX 提供与其他 libp2p impls 的 interop。multiplex(mplex::MplexConfig::new())。盒装()；).

```
    let transp = TokioTcpConfig::new()
        .upgrade(upgrade::Version::V1)
        .authenticate(NoiseConfig::xx(auth_keys).into_authenticated())
        .multiplex(mplex::MplexConfig::new())
        .boxed();

```

传输是一组网络协议，支持对等体之间面向连接的通信。还可以在一个应用程序中使用多种传输方式，例如，TCP/IP 和 Websockets，或者针对不同的用例同时使用 UDP。

在这个例子中，我们将使用 TCP 作为使用 Tokio 的异步 TCP 的基础。一旦建立了 TCP 连接，我们将`upgrade`它来使用`Noise`进行安全通信。一个基于 web 的例子是在 HTTP 之上使用 TLS 来创建安全连接。

我们使用`NoiseConfig:xx`握手模式，这是三个选项之一，因为它是唯一一个保证与其他`libp2p`应用程序互操作的模式。

关于`libp2p`的好处是，我们可以编写一个 Rust 客户端，另一个可以编写一个 JavaScript 客户端，只要协议在两个版本的库中实现，它们仍然可以很容易地通信。

最后，我们还[复用](https://docs.libp2p.io/concepts/stream-multiplexing/)传输，这使我们能够在同一个传输上复用多个子流或连接。

唷，那是相当多的理论！但所有这些都可以在 [`libp2p`文档](https://docs.libp2p.io/)中找到。这只是创建对等传输的许多方法中的一种。

下一个概念是一个`NetworkBehaviour`。这是`libp2p`中的一部分，它实际上定义了网络和所有对等体的逻辑——例如，如何处理传入的事件以及发送哪些事件。

```
    let mut behaviour = RecipeBehaviour {
        floodsub: Floodsub::new(PEER_ID.clone()),
        mdns: TokioMdns::new().expect("can create mdns"),
        response_sender,
    };

    behaviour.floodsub.subscribe(TOPIC.clone());

```

在这种情况下，如上所述，我们将使用`FloodSub`协议来处理事件。我们还将使用 [`mDNS`](https://tools.ietf.org/html/rfc6762) ，这是一种用于发现本地网络上其他对等点的协议。我们还将把通道的`sender`部分放在这里，这样我们可以用它将事件传播回应用程序的主要部分。

我们之前创建的`FloodSub`主题现在正被我们的行为订阅，这意味着我们将接收并发送关于该主题的事件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们几乎完成了`libp2p`的设置。我们需要的最后一个概念是`Swarm`。

```
    let mut swarm = SwarmBuilder::new(transp, behaviour, PEER_ID.clone())
        .executor(Box::new(|fut| {
            tokio::spawn(fut);
        }))
        .build();

```

一个 [`Swarm`](https://docs.rs/libp2p/latest/libp2p/index.html#swarm) 管理使用传输创建的连接，并执行我们创建的网络行为，触发和接收事件，并为我们提供从外部访问它们的方法。

我们用我们的传输、行为和对等 ID 创建了`Swarm`。`executor`部分只是告诉`Swarm`使用`Tokio`运行时在内部运行，但是我们也可以在这里使用其他异步运行时。

剩下唯一要做的事情就是开始我们的`Swarm`:

```
    Swarm::listen_on(
        &mut swarm,
        "/ip4/0.0.0.0/tcp/0"
            .parse()
            .expect("can get a local socket"),
    )
    .expect("swarm can be started");

```

例如，类似于启动一个 TCP 服务器，我们简单地用一个本地 IP 调用`listen_on`，让操作系统为我们决定端口。这将启动带有所有设置的`Swarm`,但是我们实际上还没有定义任何逻辑。

让我们从处理用户输入开始。

## 处理`libp2p`中的输入

对于用户输入，我们将简单地依赖传统的标准输入。因此，在打`Swarm::listen_on`电话之前，我们将补充:

```
    let mut stdin = tokio::io::BufReader::new(tokio::io::stdin()).lines();

```

这在 STDIN 上定义了一个异步读取器，它逐行读取流。所以如果我们按下回车键，将会有新的消息传入。

下一部分是创建我们的事件循环，它将监听来自 STDIN、`Swarm`和上面定义的响应通道的事件。

```
    loop {
        let evt = {
            tokio::select! {
                line = stdin.next_line() => Some(EventType::Input(line.expect("can get line").expect("can read line from stdin"))),
                event = swarm.next() => {
                    info!("Unhandled Swarm Event: {:?}", event);
                    None
                },
                response = response_rcv.recv() => Some(EventType::Response(response.expect("response exists"))),
            }
        };
        ...
    }
}

```

我们使用 Tokio 的`select`宏来等待几个异步进程，处理第一个完成的进程。我们对`Swarm`事件不做任何事情；这些都是在我们的`RecipeBehaviour`中处理的，稍后我们会看到，但是我们仍然需要调用`swarm.next()`来驱动`Swarm`前进。

让我们添加一些事件处理逻辑来代替`…`:

```
        if let Some(event) = evt {
            match event {
                EventType::Response(resp) => {
                   ...
                }
                EventType::Input(line) => match line.as_str() {
                    "ls p" => handle_list_peers(&mut swarm).await,
                    cmd if cmd.starts_with("ls r") => handle_list_recipes(cmd, &mut swarm).await,
                    cmd if cmd.starts_with("create r") => handle_create_recipe(cmd).await,
                    cmd if cmd.starts_with("publish r") => handle_publish_recipe(cmd).await,
                    _ => error!("unknown command"),
                },
            }
        }

```

如果有一个事件，我们匹配它，看看它是一个`Response`还是一个`Input`事件。让我们暂时只看一下`Input`事件。

有几个选择。我们支持以下命令:

*   `ls p`列出所有已知的对等点
*   `ls r`列出当地食谱
*   `ls r {peerId}`列出某个同行发布的食谱
*   列出所有已知同行发布的食谱
*   `publish r {recipeId}`发布给定的配方
*   `create r {recipeName}|{recipeIngredients}|{recipeInstructions`用给定数据和递增 ID 创建新配方

在这种情况下，列出来自同行的所有菜谱意味着向我们的同行发送一个菜谱请求，等待他们的响应，并显示结果。在对等网络中，这可能需要一段时间，因为一些对等点可能在地球的另一边，我们不知道是否所有对等点都会响应我们。例如，这与向 HTTP 服务器发送请求有很大不同。

我们先来看看列出同行的逻辑:

```
async fn handle_list_peers(swarm: &mut Swarm<RecipeBehaviour>) {
    info!("Discovered Peers:");
    let nodes = swarm.mdns.discovered_nodes();
    let mut unique_peers = HashSet::new();
    for peer in nodes {
        unique_peers.insert(peer);
    }
    unique_peers.iter().for_each(|p| info!("{}", p));
}

```

在这种情况下，我们可以使用`mDNS`给出所有发现的节点，迭代并显示它们。简单。

在处理列表命令之前，接下来让我们浏览一下创建和发布食谱:

```
async fn handle_create_recipe(cmd: &str) {
    if let Some(rest) = cmd.strip_prefix("create r") {
        let elements: Vec<&str> = rest.split("|").collect();
        if elements.len() < 3 {
            info!("too few arguments - Format: name|ingredients|instructions");
        } else {
            let name = elements.get(0).expect("name is there");
            let ingredients = elements.get(1).expect("ingredients is there");
            let instructions = elements.get(2).expect("instructions is there");
            if let Err(e) = create_new_recipe(name, ingredients, instructions).await {
                error!("error creating recipe: {}", e);
            };
        }
    }
}

async fn handle_publish_recipe(cmd: &str) {
    if let Some(rest) = cmd.strip_prefix("publish r") {
        match rest.trim().parse::<usize>() {
            Ok(id) => {
                if let Err(e) = publish_recipe(id).await {
                    info!("error publishing recipe with id {}, {}", id, e)
                } else {
                    info!("Published Recipe with id: {}", id);
                }
            }
            Err(e) => error!("invalid id: {}, {}", rest.trim(), e),
        };
    }
}

```

在这两种情况下，我们都需要解析字符串以获得由`|`分隔的数据，或者在`publish`的情况下获得给定的配方 ID，如果给定的输入无效，则记录一个错误。

在`create`的情况下，我们用给定的数据调用`create_new_recipe`辅助函数。让我们来看看与简单的本地 JSON 食谱存储交互所需的所有帮助函数:

```
async fn create_new_recipe(name: &str, ingredients: &str, instructions: &str) -> Result<()> {
    let mut local_recipes = read_local_recipes().await?;
    let new_id = match local_recipes.iter().max_by_key(|r| r.id) {
        Some(v) => v.id + 1,
        None => 0,
    };
    local_recipes.push(Recipe {
        id: new_id,
        name: name.to_owned(),
        ingredients: ingredients.to_owned(),
        instructions: instructions.to_owned(),
        public: false,
    });
    write_local_recipes(&local_recipes).await?;

    info!("Created recipe:");
    info!("Name: {}", name);
    info!("Ingredients: {}", ingredients);
    info!("Instructions:: {}", instructions);

    Ok(())
}

async fn publish_recipe(id: usize) -> Result<()> {
    let mut local_recipes = read_local_recipes().await?;
    local_recipes
        .iter_mut()
        .filter(|r| r.id == id)
        .for_each(|r| r.public = true);
    write_local_recipes(&local_recipes).await?;
    Ok(())
}

async fn read_local_recipes() -> Result<Recipes> {
    let content = fs::read(STORAGE_FILE_PATH).await?;
    let result = serde_json::from_slice(&content)?;
    Ok(result)
}

async fn write_local_recipes(recipes: &Recipes) -> Result<()> {
    let json = serde_json::to_string(&recipes)?;
    fs::write(STORAGE_FILE_PATH, &json).await?;
    Ok(())
}

```

最基本的构建模块是`read_local_recipes`和`write_local_recipes`，它们只是从存储文件中读取和反序列化或者序列化配方，或者将配方写入存储文件。

`publish_recipe`助手从文件中获取所有的配方，寻找具有给定 ID 的配方，并将其`public`标志设置为 true。

当创建一个配方时，我们也从文件中获取所有配方，在末尾添加一个新配方，并写回整个数据，覆盖文件。这不是超高效的，但是很简单，很管用。

## 用`libp2p`发送信息

接下来让我们看看`list`命令，并探索如何向其他对等体发送消息。

在`list`命令中，有三种可能的情况:

```
async fn handle_list_recipes(cmd: &str, swarm: &mut Swarm<RecipeBehaviour>) {
    let rest = cmd.strip_prefix("ls r ");
    match rest {
        Some("all") => {
            let req = ListRequest {
                mode: ListMode::ALL,
            };
            let json = serde_json::to_string(&req).expect("can jsonify request");
            swarm.floodsub.publish(TOPIC.clone(), json.as_bytes());
        }
        Some(recipes_peer_id) => {
            let req = ListRequest {
                mode: ListMode::One(recipes_peer_id.to_owned()),
            };
            let json = serde_json::to_string(&req).expect("can jsonify request");
            swarm.floodsub.publish(TOPIC.clone(), json.as_bytes());
        }
        None => {
            match read_local_recipes().await {
                Ok(v) => {
                    info!("Local Recipes ({})", v.len());
                    v.iter().for_each(|r| info!("{:?}", r));
                }
                Err(e) => error!("error fetching local recipes: {}", e),
            };
        }
    };
}

```

我们解析传入的命令，剥离`ls r`部分，并检查剩下的部分。如果命令中没有其他内容，我们可以简单地获取本地食谱，并使用上一节中定义的帮助器打印它们。

如果我们遇到了`all`关键字，我们用`ListMode::ALL`集创建一个`ListRequest`，将其序列化为 JSON，并使用我们的`Swarm`中的`FloodSub`实例，将其发布到前面提到的`Topic`。

如果我们在命令中遇到对等 ID，也会发生同样的事情，在这种情况下，我们只需发送带有对等 ID 的`ListMode::One`模式。我们可以检查它是否是有效的对等 id，或者它是否是我们已经发现的对等 ID，但是让我们保持简单:如果没有人听它，什么也不会发生。

这就是我们向网络发送消息所需要做的一切。现在的问题是，这些信息会发生什么？它们在哪里处理？

在对等应用程序的情况下，记住我们都是事件的`Sender`和`Receiver`，所以我们需要在实现中处理传出和传入事件。

## 用`libp2p`响应消息

这终于是我们的`RecipeBehaviour`发挥作用的部分了。我们来定义一下:

```
#[derive(NetworkBehaviour)]
struct RecipeBehaviour {
    floodsub: Floodsub,
    mdns: TokioMdns,
    #[behaviour(ignore)]
    response_sender: mpsc::UnboundedSender<ListResponse>,
}

```

行为本身只是一个 struct，但是我们使用了`libp2p`的`NetworkBehaviour` derive 宏，所以我们不必自己手动实现所有的 trait 函数。

这个派生宏为结构的所有成员实现了 [`NetworkBehaviour`](https://docs.rs/libp2p/latest/libp2p/swarm/trait.NetworkBehaviour.html) 特征的功能，这些成员没有用`behaviour(ignore)`标注。我们的通道在这里被忽略，因为它与我们的行为没有直接关系。

剩下的就是为`FloodsubEvent`和`MdnsEvent`实现`inject_event`功能。

先说`mDNS`:

```
impl NetworkBehaviourEventProcess<MdnsEvent> for RecipeBehaviour {
    fn inject_event(&mut self, event: MdnsEvent) {
        match event {
            MdnsEvent::Discovered(discovered_list) => {
                for (peer, _addr) in discovered_list {
                    self.floodsub.add_node_to_partial_view(peer);
                }
            }
            MdnsEvent::Expired(expired_list) => {
                for (peer, _addr) in expired_list {
                    if !self.mdns.has_node(&peer) {
                        self.floodsub.remove_node_from_partial_view(&peer);
                    }
                }
            }
        }
    }
}

```

当这个处理程序的事件到来时，调用`inject_event`函数。在`mDNS`端，只有两个事件，`Discovered`和`Expired`，当我们在网络上看到一个新的对等体或者当一个现有的对等体离开时，就会触发这两个事件。在这两种情况下，我们要么将它添加到我们的`FloodSub`“局部视图”中，要么将它从其中删除，局部视图是我们的消息要传播到的节点列表。

发布/订阅事件的`inject_event`稍微复杂一些。我们需要对到来的`ListRequest`和`ListResponse`有效载荷做出反应。如果我们发送一个`ListRequest`，收到请求的对等体将获取其本地发布的食谱，然后需要一种方法将它们发送回来。

将它们发送回请求方的唯一方法是在网络上发布它们。因为发布/订阅确实是我们拥有的唯一机制，所以我们需要对传入的请求和传入的响应做出反应。

让我们看看这是如何工作的:

```
impl NetworkBehaviourEventProcess<FloodsubEvent> for RecipeBehaviour {
    fn inject_event(&mut self, event: FloodsubEvent) {
        match event {
            FloodsubEvent::Message(msg) => {
                if let Ok(resp) = serde_json::from_slice::<ListResponse>(&msg.data) {
                    if resp.receiver == PEER_ID.to_string() {
                        info!("Response from {}:", msg.source);
                        resp.data.iter().for_each(|r| info!("{:?}", r));
                    }
                } else if let Ok(req) = serde_json::from_slice::<ListRequest>(&msg.data) {
                    match req.mode {
                        ListMode::ALL => {
                            info!("Received ALL req: {:?} from {:?}", req, msg.source);
                            respond_with_public_recipes(
                                self.response_sender.clone(),
                                msg.source.to_string(),
                            );
                        }
                        ListMode::One(ref peer_id) => {
                            if peer_id == &PEER_ID.to_string() {
                                info!("Received req: {:?} from {:?}", req, msg.source);
                                respond_with_public_recipes(
                                    self.response_sender.clone(),
                                    msg.source.to_string(),
                                );
                            }
                        }
                    }
                }
            }
            _ => (),
        }
    }
}

```

我们匹配传入的消息，尝试将其反序列化为请求或响应。对于响应，我们只需打印带有调用者对等 ID 的响应，这是使用`msg.source`得到的。当我们收到一个传入的请求时，我们需要区分`ALL`和`One`案例。

在`One`的情况下，我们检查给定的对等体 ID 是否与我们的相同——即请求实际上是针对我们的。如果是，我们返回我们发布的食谱，这也是我们对`ALL`的响应。

在这两种情况下，我们都调用`respond_with_public_recipes`助手:

```
fn respond_with_public_recipes(sender: mpsc::UnboundedSender<ListResponse>, receiver: String) {
    tokio::spawn(async move {
        match read_local_recipes().await {
            Ok(recipes) => {
                let resp = ListResponse {
                    mode: ListMode::ALL,
                    receiver,
                    data: recipes.into_iter().filter(|r| r.public).collect(),
                };
                if let Err(e) = sender.send(resp) {
                    error!("error sending response via channel, {}", e);
                }
            }
            Err(e) => error!("error fetching local recipes to answer ALL request, {}", e),
        }
    });
}

```

在这个帮助器方法中，我们使用 Tokio 的 spawn 异步执行 future，它读取所有本地食谱，从数据中创建一个`ListResponse`，并通过`channel_sender`将该数据发送到我们的事件循环，在这里我们像这样处理它:

```
                EventType::Response(resp) => {
                    let json = serde_json::to_string(&resp).expect("can jsonify response");
                    swarm.floodsub.publish(TOPIC.clone(), json.as_bytes());
                }

```

如果我们注意到一个“内部”发送过来的`Response`事件，我们将它序列化到 JSON 并发送到网络上。

## 使用`libp2p`进行测试

实现到此为止。现在我们来测试一下。

为了检查我们的实现是否工作，让我们使用以下命令在几个终端中启动应用程序:

```
RUST_LOG=info cargo run

```

请记住，应用程序希望在您启动它的目录中有一个名为`recipes.json`的文件。

当应用程序启动时，我们得到以下日志，打印我们的对等 ID:

```
INFO  rust_peer_to_peer_example > Peer Id: 12D3KooWDc1FDabQzpntvZRWeDZUL351gJRy3F4E8VN5Gx2pBCU2

```

现在我们需要按回车键来启动事件循环。

进入`ls p`后，我们会得到一个发现的对等点列表:

```
ls p
 INFO  rust_peer_to_peer_example > Discovered Peers:
 INFO  rust_peer_to_peer_example > 12D3KooWCK6X7mFk9HeWw69WF1ueWa3XmphZ2Mu7ZHvEECj5rrhG
 INFO  rust_peer_to_peer_example > 12D3KooWLGN85pv5XTDALGX5M6tRgQtUGMWXWasWQD6oJjMcEENA

```

通过`ls r`，我们得到了当地的食谱:

```
ls r
 INFO  rust_peer_to_peer_example > Local Recipes (3)
 INFO  rust_peer_to_peer_example > Recipe { id: 0, name: " Coffee", ingredients: "Coffee", instructions: "Make Coffee", public: true }
 INFO  rust_peer_to_peer_example > Recipe { id: 1, name: " Tea", ingredients: "Tea, Water", instructions: "Boil Water, add tea", public: false }
 INFO  rust_peer_to_peer_example > Recipe { id: 2, name: " Carrot Cake", ingredients: "Carrots, Cake", instructions: "Make Carrot Cake", public: true }

```

调用`ls r all`触发向其他对等体发送请求，并返回它们的配方:

```
ls r all
 INFO  rust_peer_to_peer_example > Response from 12D3KooWCK6X7mFk9HeWw69WF1ueWa3XmphZ2Mu7ZHvEECj5rrhG:
 INFO  rust_peer_to_peer_example > Recipe { id: 0, name: " Coffee", ingredients: "Coffee", instructions: "Make Coffee", public: true }
 INFO  rust_peer_to_peer_example > Recipe { id: 2, name: " Carrot Cake", ingredients: "Carrots, Cake", instructions: "Make Carrot Cake", public: true }

```

如果我们使用带有对等 ID 的`ls r`,也会发生同样的情况:

```
ls r 12D3KooWCK6X7mFk9HeWw69WF1ueWa3XmphZ2Mu7ZHvEECj5rrhG
 INFO  rust_peer_to_peer_example > Response from 12D3KooWCK6X7mFk9HeWw69WF1ueWa3XmphZ2Mu7ZHvEECj5rrhG:
 INFO  rust_peer_to_peer_example > Recipe { id: 0, name: " Coffee", ingredients: "Coffee", instructions: "Make Coffee", public: true }
 INFO  rust_peer_to_peer_example > Recipe { id: 2, name: " Carrot Cake", ingredients: "Carrots, Cake", instructions: "Make Carrot Cake", public: true }

```

有用！您也可以对同一网络中的大量客户端进行尝试。

你可以在 [GitHub](https://github.com/zupzup/rust-peer-to-peer-example) 找到完整的示例代码。

## 结论

在这篇文章中，我们介绍了如何使用 Rust 和`libp2p`构建一个小型的、分散的网络应用程序。

如果您有 web 背景，许多网络概念会有些熟悉，但是构建对等应用程序仍然需要一种完全不同的设计和构建方法。

`libp2p`库相当成熟，而且由于 Rust 在加密领域的流行，有一个新兴的丰富的库生态系统来构建强大的去中心化应用。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。