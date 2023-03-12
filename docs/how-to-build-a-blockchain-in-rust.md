# 如何在 Rust - LogRocket 博客中建立一个区块链

> 原文：<https://blog.logrocket.com/how-to-build-a-blockchain-in-rust/>

当我们想到 P2P 技术和它现在的用途时，不可能不立即想到区块链技术。在过去的十年里，很少有话题像区块链科技和加密货币这样被大肆宣传或引起如此大的争议。

虽然对区块链技术的广泛兴趣有很大不同——这自然是由于一些更广为人知和使用的加密货币背后的货币潜力——但有一点是明确的:它仍然相关，而且似乎不会有任何发展。

在之前的一篇文章中，我们介绍了如何在 Rust 中构建一个非常基本的、有效的(尽管效率相当低)点对点应用程序。在本教程中，我们将演示如何用 500 行 Rust 代码构建一个具有基本挖掘方案、共识和对等网络的区块链应用程序。

我们将详细介绍以下内容:

## 为什么区块链令人兴奋

虽然我个人对加密货币或一般的金融赌博并不特别感兴趣，但我发现分散我们现有基础设施的一部分的想法非常有吸引力。区块链有许多伟大的项目，旨在解决气候变化、社会不平等、隐私和政府透明度等社会问题。

技术背后的潜力建立在安全、完全透明、去中心化的账本理念之上，让参与者无需首先建立信任就能进行互动，这种潜力就像它看起来那样改变了游戏规则。看到前面提到的雄心勃勃的想法中的哪一个能够起步、获得牵引力并成功前进将是令人兴奋的。

简而言之，区块链技术令人兴奋，不仅因为它改变世界的潜力，而且从技术角度来看也是如此。从点对点网络上的密码术到奇特的一致算法，这个领域有相当多令人着迷的主题可以深入研究。

## 用 Rust 编写区块链应用程序

在本指南中，我们将使用 Rust 从头开始构建一个非常简单的区块链应用程序。我们的应用程序不会特别高效、安全或健壮，但它将帮助您理解如何以简单的方式实现广为人知的区块链系统背后的一些基本概念，解释它们背后的一些想法。

我们不会在每一个概念上深究每一个细节，实现会有一些严重的缺点。你不会想把这个项目用于生产用例范围内的任何东西，但是目标是构建一些你可以摆弄的东西，应用到你自己的想法中，并且从总体上来说，研究如何更熟悉 Rust 和区块链理工大学。

重点将放在技术部分，即如何实现一些概念以及它们如何一起发挥作用。我们不会解释什么是区块链，也不会触及挖掘、共识等超出本教程所需的内容。我们主要关心的是如何将这些想法以简化的形式转化成 Rust 代码。

还有，我们不会建立加密货币或者类似的系统。我们的设计要简单得多:网络中的每个节点都可以通过在本地挖掘一个有效的块，然后广播该块，将数据(字符串)添加到分散的分类帐(区块链)中。

只要它是一个有效的块(我们将在后面看到这意味着什么)，每个节点都会将该块添加到它的链中，我们的数据就成为一个分散的、防篡改的、不可破坏的(除了在我们的例子中所有的节点都关闭了)网络的一部分！

这显然是一个非常简化且有些做作的设计，在扩展时会很快遇到效率和健壮性问题。但是既然我们做这个练习只是为了学习，那完全没问题。如果你坚持到最后，并有一些动力，你可以向任何你想要的方向延伸，也许会从我们微不足道的起点建立下一个伟大的东西——你永远不知道！

## 设置我们的 Rust 应用

接下来，你所需要的就是一个[最近的生锈装置](https://www.rust-lang.org/tools/install)。

首先，创建一个新的 Rust 项目:

```
cargo new rust-blockchain-example
cd rust-blockchain-example
```

接下来，编辑`Cargo.toml`文件并添加您需要的依赖项。

```
[dependencies]
chrono = "0.4"
sha2 = "0.9.8"
serde = {version = "1.0", features = ["derive"] }
serde_json = "1.0"
libp2p = { version = "0.39", features = ["tcp-tokio", "mdns"] }
tokio = { version = "1.0", features = ["io-util", "io-std", "macros", "rt", "rt-multi-thread", "sync", "time"] }
hex = "0.4"
once_cell = "1.5"
log = "0.4"
pretty_env_logger = "0.4"
```

我们使用 [libp2p](https://github.com/libp2p/rust-libp2p) 作为我们的对等网络层，使用 [Tokio](https://github.com/tokio-rs/tokio) 作为我们的底层运行时。

我们将使用`sha2`库进行 sha256 散列，并使用`hex`箱将二进制散列转换成可读和可转移的十六进制。

除此之外，实际上只有用于 JSON 的`serde`、用于日志记录的`log`和`pretty_env_logger`、用于静态初始化的`once_cell`和用于时间戳的`chrono`等实用程序。

设置就绪后，让我们先从实现区块链基础开始，然后，再将所有这些放入 P2P 网络环境中。

## 区块链基础知识

让我们首先定义实际区块链的数据结构:

```
pub struct App {
    pub blocks: Vec,
}

#[derive(Serialize, Deserialize, Debug, Clone)]
pub struct Block {
    pub id: u64,
    pub hash: String,
    pub previous_hash: String,
    pub timestamp: i64,
    pub data: String,
    pub nonce: u64,
}
```

就这样——真的，后面没什么。我们的`App`结构本质上保存了我们的应用程序状态。在这个例子中，我们不会持久化区块链，所以一旦我们停止应用程序，它就会消失。

这个状态就是简单的一个`Blocks`列表。我们将在这个列表的末尾添加新的块，这实际上将是我们的区块链数据结构。

实际的逻辑将使这个块列表成为一个块链，其中每个块引用前一个块的散列，这将在我们的应用程序逻辑中实现。构建一个已经支持我们需要的开箱即用的验证的数据结构是可能的，但是这种方法似乎更简单，并且我们在这里明确地以简单为目标。

在我们的例子中，一个`Block`将由一个`id`组成，它是一个从 0 开始向上计数的索引。然后，一个 sha256 散列(我们将在后面讨论它的计算)，前一个块的散列，一个时间戳，块中包含的数据和一个 nonce，我们在讨论块的`mining`时也会涉及到。

在我们开始挖掘之前，让我们首先实现一些验证功能，我们需要这些功能来保持我们的状态一致，并且需要一些非常基本的共识，这样每个客户端都知道哪个区块链是正确的，以防有多个冲突的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们从实现我们的`App`结构开始:

```
impl App {
    fn new() -> Self {
        Self { blocks: vec![] }
    }

    fn genesis(&mut self) {
        let genesis_block = Block {
            id: 0,
            timestamp: Utc::now().timestamp(),
            previous_hash: String::from("genesis"),
            data: String::from("genesis!"),
            nonce: 2836,
            hash: "0000f816a87f806bb0073dcf026a64fb40c946b5abee2573702828694d5b4c43".to_string(),
        };
        self.blocks.push(genesis_block);
    }
...
}
```

我们用一个空链初始化我们的应用程序。稍后，我们将实现一些逻辑。我们在启动时向其他节点要他们的链，如果链比我们的长，就用他们的。这是我们简单化的共识标准。

`genesis`方法在我们的区块链中创建了第一个硬编码块。这是一个“特殊”的块，因为它并不真正遵守与其他块相同的规则。例如，它没有有效的`previous_hash`，因为在它之前根本没有块。

我们需要这个来“引导”我们的节点——或者，实际上，整个网络作为第一个节点启动。这个链条必须从某个地方开始，就是这里。

## 街区，街区，街区

接下来，让我们添加一些功能，使我们能够向链中添加新的数据块。

```
impl App {
...
    fn try_add_block(&mut self, block: Block) {
        let latest_block = self.blocks.last().expect("there is at least one block");
        if self.is_block_valid(&block, latest_block) {
            self.blocks.push(block);
        } else {
            error!("could not add block - invalid");
        }
    }
...
}
```

这里，我们获取链中的最后一个块——我们的`previous block`——然后验证我们想要添加的块实际上是否有效。如果没有，我们只是记录一个错误。

在我们的简单应用程序中，我们不会实现任何真正的错误处理。正如您将在后面看到的，如果我们遇到节点间竞争条件的问题，并且有一个无效的状态，我们的节点基本上就坏了。

我将提到这些问题的一些可能的解决方案，但我们不会在这里实现它们；即使不用担心这些恼人的现实世界的问题，我们也有相当多的内容要讨论。

接下来让我们看看`is_block_valid`，我们逻辑的核心部分。

```
const DIFFICULTY_PREFIX: &str = "00";

fn hash_to_binary_representation(hash: &[u8]) -> String {
    let mut res: String = String::default();
    for c in hash {
        res.push_str(&format!("{:b}", c));
    }
    res
}

impl App {
...
    fn is_block_valid(&self, block: &Block, previous_block: &Block) -> bool {
        if block.previous_hash != previous_block.hash {
            warn!("block with id: {} has wrong previous hash", block.id);
            return false;
        } else if !hash_to_binary_representation(
            &hex::decode(&block.hash).expect("can decode from hex"),
        )
        .starts_with(DIFFICULTY_PREFIX)
        {
            warn!("block with id: {} has invalid difficulty", block.id);
            return false;
        } else if block.id != previous_block.id + 1 {
            warn!(
                "block with id: {} is not the next block after the latest: {}",
                block.id, previous_block.id
            );
            return false;
        } else if hex::encode(calculate_hash(
            block.id,
            block.timestamp,
            &block.previous_hash,
            &block.data,
            block.nonce,
        )) != block.hash
        {
            warn!("block with id: {} has invalid hash", block.id);
            return false;
        }
        true
    }
...
}
```

我们首先定义一个常数`DIFFICULTY_PREFIX`。这是我们非常简单的采矿方案的基础。本质上，当挖掘一个块时，挖掘的人必须散列该块的数据(在我们的例子中是用 SHA256)并找到一个散列，这个散列在二进制中以`00`(两个零)开始。这也说明了我们在网络上的“困难”。

可以想象，如果我们需要三个、四个、五个甚至二十个前导零，那么找到合适的散列值的时间会增加很多。在“真正的”区块链系统中，这种困难是网络属性，它是基于共识算法和基于网络的散列能力在节点之间达成一致的，因此网络可以保证在一定时间内产生新的块。

我们不在这里讨论这个。为了简单起见，我们只将其硬编码为两个前导零。这个在普通硬件上计算不需要太长时间，所以测试的时候不需要担心等待时间太长。

接下来，我们有一个助手函数，它只是一个给定字节数组的二进制表示，形式为`String`。这用于方便地检查一个散列是否符合我们的`DIFFICULTY_PREFIX`条件。显然，有更好、更快的方法可以做到这一点，但这很简单，并且适用于我们的情况。

现在来看验证一个`Block`的逻辑。这很重要，因为它确保我们的区块链坚持它的链属性，很难篡改。改变某些东西的难度随着每一个块的增加而增加，因为你必须重新计算(例如，重新挖掘)链的剩余部分来再次获得有效的链。在真实的区块链系统中，这将是足够昂贵的，足以抑制你的积极性)

你应该遵循一些经验法则:

1.  `previous_hash`需要实际匹配链中最后一个块的散列
2.  `hash`需要以我们的`DIFFICULTY_PREFIX`开始(即两个零)，这表明它被正确地挖掘
3.  `id`需要是增加 1 的最新 ID
4.  散列需要确实是正确的；散列块的数据需要给我们块散列(否则，您还不如创建一个从`001`开始的随机散列)

如果我们把它想成一个分布式系统，你可能会注意到这里可能会遇到麻烦。如果两个节点基于块 ID `5`同时挖掘一个块会怎么样？他们都将创建块 ID `6`，前一个块指向块 ID `5`。

所以我们会被送到两个街区。我们将验证它们并添加第一个进来的，但是第二个将在验证期间被丢弃，因为我们已经有一个 ID 为`6`的块。

这是像这样的系统中的固有问题，也是节点之间需要一致算法来决定同意和使用哪些块(即，哪个链)的原因。

最理想的情况是，如果你开采的区块没有被添加到商定的链中，你将不得不再次开采它，并希望下次会更好。在我们这个简单的例子中，这个重试机制不会被实现；如果发生这种竞争，该节点基本上就出局了。

当然，在区块链领域有更复杂的方法来解决这个问题。例如，如果我们将数据作为“事务”发送到其他节点，而节点将使用一组事务来挖掘数据块，这将在某种程度上得到缓解。但是每个人都会一直挖矿，最快的人会赢。所以，如你所见，这将产生额外的，但不太严重的问题，我们必须解决。

无论如何，我们的简单方法将适用于我们的本地测试网络。

## 用哪个链条？

现在我们可以验证一个块，让我们实现验证整个链的逻辑:

```
impl App {
...
    fn is_chain_valid(&self, chain: &[Block]) -> bool {
        for i in 0..chain.len() {
            if i == 0 {
                continue;
            }
            let first = chain.get(i - 1).expect("has to exist");
            let second = chain.get(i).expect("has to exist");
            if !self.is_block_valid(second, first) {
                return false;
            }
        }
        true
    }
...
}
```

忽略 genesis 块，我们基本上只是遍历所有的块并验证它们。如果一个模块没有通过验证，我们就让整个链都失败了。

在`App`中还有一个方法可以帮助我们选择使用哪个链:

```
impl App {
...
    // We always choose the longest valid chain
    fn choose_chain(&mut self, local: Vec, remote: Vec) -> Vec {
        let is_local_valid = self.is_chain_valid(&local);
        let is_remote_valid = self.is_chain_valid(&remote);

        if is_local_valid && is_remote_valid {
            if local.len() >= remote.len() {
                local
            } else {
                remote
            }
        } else if is_remote_valid && !is_local_valid {
            remote
        } else if !is_remote_valid && is_local_valid {
            local
        } else {
            panic!("local and remote chains are both invalid");
        }
    }
}
```

如果我们要求另一个节点的链来确定它是否比我们本地的“更好”(根据我们的共识算法)，就会发生这种情况。

我们的标准只是链条的长度。在实际系统中，通常会有更多的因素，如考虑的难度和许多其他可能性。为了这个练习的目的，如果一个(有效的)链比另一个长，那么我们取那个。

我们验证本地和远程链，并选择较长的一个。在启动时，当我们向其他节点请求它们的链和。由于我们的程序只包含一个 genesis 块，我们将立即开始处理“商定”链。

## 采矿

为了完成我们的区块链相关逻辑，让我们实现我们的基本挖掘方案。

```
impl Block {
    pub fn new(id: u64, previous_hash: String, data: String) -> Self {
        let now = Utc::now();
        let (nonce, hash) = mine_block(id, now.timestamp(), &previous_hash, &data);
        Self {
            id,
            hash,
            timestamp: now.timestamp(),
            previous_hash,
            data,
            nonce,
        }
    }
}
```

当一个新的块被创建时，我们调用`mine_block`，它将返回一个`nonce`和一个`hash`。然后，我们可以用它的时间戳、给定的数据、ID、以前的散列以及新的散列和 nonce 来创建块。

除了`nonce`，我们讨论了上面所有的字段。为了解释这是什么，让我们看看`mine_block`函数:

```
fn mine_block(id: u64, timestamp: i64, previous_hash: &str, data: &str) -> (u64, String) {
    info!("mining block...");
    let mut nonce = 0;

    loop {
        if nonce % 100000 == 0 {
            info!("nonce: {}", nonce);
        }
        let hash = calculate_hash(id, timestamp, previous_hash, data, nonce);
        let binary_hash = hash_to_binary_representation(&hash);
        if binary_hash.starts_with(DIFFICULTY_PREFIX) {
            info!(
                "mined! nonce: {}, hash: {}, binary hash: {}",
                nonce,
                hex::encode(&hash),
                binary_hash
            );
            return (nonce, hex::encode(hash));
        }
        nonce += 1;
    }
}
```

在宣布我们将要开采一个区块之后，我们将`nonce`设置为 0。

然后，我们开始一个无限循环，在每一步中递增`nonce`。在循环内部，除了记录每 100000 次的迭代以获得一个粗略的进度指标，我们还使用`calculate_hash`计算块数据的散列，我们接下来将检查它。

然后，我们使用我们的`hash_to_binary_representation`助手，检查计算出的散列值是否符合我们从两个零开始的难度标准。

如果是这样，我们记录它并返回`nonce`、递增的整数、它发生的位置和(十六进制编码的)散列。否则，我们递增`nonce`并重新开始。

本质上，我们拼命地寻找一段数据——在本例中，是`nonce`和一个数字，它和使用 SHA256 散列的块数据一起，将为我们提供一个以两个零开始的散列。

我们需要将这个`nonce`记录在我们的块中，以便其他节点可以验证我们的散列，因为`nonce`是与块数据一起散列的。例如，如果计算一个合适的散列需要 52，342 次迭代(从两个零开始)，那么`nonce`将是`52341`(少 1，因为它从 0 开始)。

让我们看看实际创建 SHA256 散列的实用程序。

```
fn calculate_hash(id: u64, timestamp: i64, previous_hash: &str, data: &str, nonce: u64) -> Vec<u8> {
    let data = serde_json::json!({
        "id": id,
        "previous_hash": previous_hash,
        "data": data,
        "timestamp": timestamp,
        "nonce": nonce
    });
    let mut hasher = Sha256::new();
    hasher.update(data.to_string().as_bytes());
    hasher.finalize().as_slice().to_owned()
}
```

这个相当简单。我们使用当前 nonce 创建块数据的 JSON 表示，并通过`sha2`的 SHA256 哈希函数，返回一个`Vec<u8>`。

这基本上就是我们实现的所有区块链逻辑。我们有一个区块链数据结构:一个块列表。我们有块，它指向前一个块。这些被要求有一个递增的 ID 号和一个符合我们的挖掘规则的散列。

如果我们请求从其他节点获取新数据块，我们会验证它们，如果它们是正确的，就将它们添加到链中。如果我们从另一个节点获得完整的区块链，我们也会验证它，如果它比我们的更长(即，其中有更多的块)，我们会用它替换我们自己的链。

正如您所想象的，由于每个节点都实现了这种精确的逻辑，所以块和约定的链可以快速地在网络中传播，并且网络收敛到相同的状态(就像我们的简单例子中前面提到的错误处理限制一样)。

## 点对点基础知识

接下来，我们将实现基于 P2P 的网络栈。

首先创建一个`p2p.rs`文件，它将保存我们将在应用程序中使用的大部分对等逻辑。

在这里，我们再次定义了一些我们需要的基本数据结构和常量:

```
pub static KEYS: Lazy = Lazy::new(identity::Keypair::generate_ed25519);
pub static PEER_ID: Lazy = Lazy::new(|| PeerId::from(KEYS.public()));
pub static CHAIN_TOPIC: Lazy = Lazy::new(|| Topic::new("chains"));
pub static BLOCK_TOPIC: Lazy = Lazy::new(|| Topic::new("blocks"));

#[derive(Debug, Serialize, Deserialize)]
pub struct ChainResponse {
    pub blocks: Vec,
    pub receiver: String,
}

#[derive(Debug, Serialize, Deserialize)]
pub struct LocalChainRequest {
    pub from_peer_id: String,
}

pub enum EventType {
    LocalChainResponse(ChainResponse),
    Input(String),
    Init,
}

#[derive(NetworkBehaviour)]
pub struct AppBehaviour {
    pub floodsub: Floodsub,
    pub mdns: Mdns,
    #[behaviour(ignore)]
    pub response_sender: mpsc::UnboundedSender,
    #[behaviour(ignore)]
    pub init_sender: mpsc::UnboundedSender,
    #[behaviour(ignore)]
    pub app: App,
}
```

从顶部开始，我们定义一个密钥对和一个派生的对等 ID。这些只是 libp2p 在网络上识别客户端的内在特性。

然后，我们定义两个所谓的`topics` : `chains`和`blocks`。我们将使用`FloodSub`协议，一个简单的发布/订阅协议，用于节点之间的通信。

这有一个优点，即设置和使用非常简单，但缺点是，我们需要广播每一条信息。因此，即使我们只想响应一个客户端的“对我们链的请求”，该客户端也会将此请求发送到网络上与它们连接的所有节点，我们也会将我们的响应发送到所有节点。

这从正确性上来说没问题，但从效率上来说，显然是骇人听闻的。这可以通过简单的点对点请求/响应模型来处理，这是 libp2p 支持的，但是这只会给这个已经很复杂的例子增加更多的复杂性。如果你有兴趣，可以查看一下 [libp2p](https://docs.rs/libp2p/0.41.0/libp2p/request_response/index.html) [docs](https://docs.rs/libp2p/0.41.0/libp2p/request_response/index.html) 。

我们也可以使用更有效的`GossipSub`来代替`FloodSub`。但是，同样，它的设置并不方便，我们在这一点上对性能并不特别感兴趣。界面非常相似。同样，如果你有兴趣玩这个，看看[官方文件](https://docs.rs/libp2p/0.41.0/libp2p/gossipsub/index.html)。

反正话题基本都是订阅的“频道”。我们可以订阅“链”,并使用它们将我们的本地区块链发送到其他节点，并接收他们的。“块”也是如此，我们将使用它来广播和接收新的块。

接下来，我们有一个持有一个块列表和一个接收器的概念。这是一个结构，如果有人向我们发送他们的本地区块链，并用它向他们发送我们的本地链，我们就会期待这个结构。

触发这种互动的是`LocalChainRequest`。如果我们发送一个带有系统中另一个节点的`peer_id`的`LocalChainRequest`，这将触发他们把他们的链发送给我们，我们将在后面看到。

为了处理传入的消息、惰性初始化和客户端用户的键盘输入，我们定义了`EventType` enum，它将帮助我们在应用程序中发送事件，以保持我们的应用程序状态与传入和传出的网络流量同步。

最后，P2P 功能的核心是我们的`AppBehaviour`，它实现了`NetworkBehaviour`，这是 libp2p 实现分散网络堆栈的概念。

我们不会在这里深入细节，但是我的[综合 libp2p 教程](https://blog.logrocket.com/libp2p-tutorial-build-a-peer-to-peer-app-in-rust/)会更详细地介绍这一点。

`AppBehaviour`保存了用于发布/订阅通信的 FloodSub 实例和 Mdns 实例，这将使我们能够自动找到本地网络上的其他节点(但不是在本地网络之外)。

我们还将我们的区块链`App`添加到这个行为中，以及为应用程序各部分之间的初始化和请求/响应通信发送事件的通道。稍后我们将看到这一点。

初始化`AppBehaviour`也相当简单:

```
impl AppBehaviour {
    pub async fn new(
        app: App,
        response_sender: mpsc::UnboundedSender,
        init_sender: mpsc::UnboundedSender,
    ) -> Self {
        let mut behaviour = Self {
            app,
            floodsub: Floodsub::new(*PEER_ID),
            mdns: Mdns::new(Default::default())
                .await
                .expect("can create mdns"),
            response_sender,
            init_sender,
        };
        behaviour.floodsub.subscribe(CHAIN_TOPIC.clone());
        behaviour.floodsub.subscribe(BLOCK_TOPIC.clone());

        behaviour
    }
}
```

## 处理传入消息

首先，我们为来自其他节点的数据实现处理程序。

我们将从 Mdns 事件开始，因为它们基本上是样板文件:

```
impl NetworkBehaviourEventProcess<MdnsEvent> for AppBehaviour {
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

如果发现了一个新节点，我们会将其添加到 FloodSub 节点列表中，这样我们就可以进行通信了。一旦过期，我们会再次删除它。

更有趣的是我们的 FloodSub 通信协议的`NetworkBehaviour`的实现。

```
// incoming event handler
impl NetworkBehaviourEventProcess for AppBehaviour {
    fn inject_event(&mut self, event: FloodsubEvent) {
        if let FloodsubEvent::Message(msg) = event {
            if let Ok(resp) = serde_json::from_slice::(&msg.data) {
                if resp.receiver == PEER_ID.to_string() {
                    info!("Response from {}:", msg.source);
                    resp.blocks.iter().for_each(|r| info!("{:?}", r));

                    self.app.blocks = self.app.choose_chain(self.app.blocks.clone(), resp.blocks);
                }
            } else if let Ok(resp) = serde_json::from_slice::(&msg.data) {
                info!("sending local chain to {}", msg.source.to_string());
                let peer_id = resp.from_peer_id;
                if PEER_ID.to_string() == peer_id {
                    if let Err(e) = self.response_sender.send(ChainResponse {
                        blocks: self.app.blocks.clone(),
                        receiver: msg.source.to_string(),
                    }) {
                        error!("error sending response via channel, {}", e);
                    }
                }
            } else if let Ok(block) = serde_json::from_slice::(&msg.data) {
                info!("received new block from {}", msg.source.to_string());
                self.app.try_add_block(block);
            }
        }
    }
}
```

对于传入的事件，即`FloodsubEvent::Message`，我们检查有效载荷是否符合我们预期的数据结构。

如果是一个`ChainResponse`，这意味着我们被另一个节点发送了一个本地区块链。

我们检查我们是否实际上是所述数据的接收者，如果是，记录传入的区块链并尝试执行我们的共识。如果它有效并且比我们的链长，我们就用它替换我们的链。否则，我们保留自己的链。

如果传入的数据是一个`LocalChainRequest`，我们检查我们是否是他们想要的链，检查`from_peer_id`。如果是这样，我们只需向他们发送本地区块链的 JSON 版本。实际的发送部分在代码的另一部分，但是现在，我们只是通过我们的事件通道发送响应。

最后，如果是一个`Block`进入，这意味着有人开采了一个区块，并希望我们将它添加到我们的本地链中。我们检查该块是否有效，如果有效，就添加它。

## 把所有的放在一起

太好了！现在，让我们将所有这些连接在一起，并添加一些命令供用户与应用程序交互。

回到`main.rs`，是时候实际实现`main`函数了。

我们从设置开始:

```
#[tokio::main]
async fn main() {
    pretty_env_logger::init();

    info!("Peer Id: {}", p2p::PEER_ID.clone());
    let (response_sender, mut response_rcv) = mpsc::unbounded_channel();
    let (init_sender, mut init_rcv) = mpsc::unbounded_channel();

    let auth_keys = Keypair::::new()
        .into_authentic(&p2p::KEYS)
        .expect("can create auth keys");

    let transp = TokioTcpConfig::new()
        .upgrade(upgrade::Version::V1)
        .authenticate(NoiseConfig::xx(auth_keys).into_authenticated())
        .multiplex(mplex::MplexConfig::new())
        .boxed();

    let behaviour = p2p::AppBehaviour::new(App::new(), response_sender, init_sender.clone()).await;

    let mut swarm = SwarmBuilder::new(transp, behaviour, *p2p::PEER_ID)
        .executor(Box::new(|fut| {
            spawn(fut);
        }))
        .build();

    let mut stdin = BufReader::new(stdin()).lines();

    Swarm::listen_on(
        &mut swarm,
        "/ip4/0.0.0.0/tcp/0"
            .parse()
            .expect("can get a local socket"),
    )
    .expect("swarm can be started");

    spawn(async move {
        sleep(Duration::from_secs(1)).await;
        info!("sending init event");
        init_sender.send(true).expect("can send init event");
    });
```

这是一大堆代码，但它基本上只是设置了我们已经讨论过的东西。我们初始化日志记录以及用于初始化和响应的两个事件通道。

然后，我们初始化我们的密钥对，libp2p 传输、行为和 libp2p 群，它是运行我们的网络堆栈的实体。

我们还在`stdin`上初始化了一个缓冲读取器，这样我们就可以读取来自用户的输入命令并启动我们的 Swarm。

最后，我们生成一个异步协程，它等待一秒钟，然后在 init 通道上发送一个初始化触发器。

这是我们将在启动一个节点后使用的信号，等待一段时间，直到该节点启动并连接。然后，我们向另一个节点询问他们的当前区块链，以让我们跟上速度。

`main`的其余部分是有趣的部分——我们处理来自用户的键盘事件、输入数据和输出数据的部分。

```
    loop {
        let evt = {
            select! {
                line = stdin.next_line() => Some(p2p::EventType::Input(line.expect("can get line").expect("can read line from stdin"))),
                response = response_rcv.recv() => {
                    Some(p2p::EventType::LocalChainResponse(response.expect("response exists")))
                },
                _init = init_rcv.recv() => {
                    Some(p2p::EventType::Init)
                }
                event = swarm.select_next_some() => {
                    info!("Unhandled Swarm Event: {:?}", event);
                    None
                },
            }
        };

        if let Some(event) = evt {
            match event {
                p2p::EventType::Init => {
                    let peers = p2p::get_list_peers(&swarm);
                    swarm.behaviour_mut().app.genesis();

                    info!("connected nodes: {}", peers.len());
                    if !peers.is_empty() {
                        let req = p2p::LocalChainRequest {
                            from_peer_id: peers
                                .iter()
                                .last()
                                .expect("at least one peer")
                                .to_string(),
                        };

                        let json = serde_json::to_string(&req).expect("can jsonify request");
                        swarm
                            .behaviour_mut()
                            .floodsub
                            .publish(p2p::CHAIN_TOPIC.clone(), json.as_bytes());
                    }
                }
                p2p::EventType::LocalChainResponse(resp) => {
                    let json = serde_json::to_string(&resp).expect("can jsonify response");
                    swarm
                        .behaviour_mut()
                        .floodsub
                        .publish(p2p::CHAIN_TOPIC.clone(), json.as_bytes());
                }
                p2p::EventType::Input(line) => match line.as_str() {
                    "ls p" => p2p::handle_print_peers(&swarm),
                    cmd if cmd.starts_with("ls c") => p2p::handle_print_chain(&swarm),
                    cmd if cmd.starts_with("create b") => p2p::handle_create_block(cmd, &mut swarm),
                    _ => error!("unknown command"),
                },
            }
        }
    }
```

我们开始一个无限循环，并使用 Tokio 的`select!`宏来竞争多个异步函数。

这意味着无论哪一个先完成，我们都会先处理，然后重新开始。

第一个事件发射器是我们的缓冲读取器，它将为我们提供来自用户的输入行。如果我们得到一个，我们用下面的代码行创建一个`EventType::Input`。

然后，我们监听响应通道和初始化通道，分别创建它们的事件。如果事件发生在群本身，这意味着它们既不是由我们的 Mdns 行为处理的，也不是由我们的 FloodSub 行为处理的，我们只是记录它们。它们大部分是噪音，比如我们的例子中的连接/断开，但是对调试有帮助。

创建了相应的事件(或者没有创建事件)，我们开始处理它们。

对于我们的`Init`事件，我们在应用程序上调用`genesis()`，创建我们的 genesis 块。如果我们连接到节点，我们触发一个`LocalChainRequest`到列表中的最后一个。

显然，在这里询问多个节点，也许是多次，并选择我们得到的最佳(即最长)响应链是有意义的。但是为了简单起见，我们只问一个，接受他们发给我们的任何东西。

然后，如果我们得到一个`LocalChainResponse`事件，这意味着在响应通道上发送了一些东西。如果您还记得上面的内容，当我们将本地区块链发送回请求节点时，FloodSub 行为中就发生了这种情况。在这里，我们实际上将传入的 JSON 发送到正确的 FloodSub 主题，因此它被广播到网络。

最后，对于用户输入，我们有三个命令:

*   `ls p`列出所有同行
*   `ls c`打印当地的区块链
*   `create b $data`创建一个以`$data`为字符串内容的新块

每个命令都调用这些辅助函数之一:

```
pub fn get_list_peers(swarm: &Swarm) -> Vec {
    info!("Discovered Peers:");
    let nodes = swarm.behaviour().mdns.discovered_nodes();
    let mut unique_peers = HashSet::new();
    for peer in nodes {
        unique_peers.insert(peer);
    }
    unique_peers.iter().map(|p| p.to_string()).collect()
}

pub fn handle_print_peers(swarm: &Swarm) {
    let peers = get_list_peers(swarm);
    peers.iter().for_each(|p| info!("{}", p));
}

pub fn handle_print_chain(swarm: &Swarm) {
    info!("Local Blockchain:");
    let pretty_json =
        serde_json::to_string_pretty(&swarm.behaviour().app.blocks).expect("can jsonify blocks");
    info!("{}", pretty_json);
}

pub fn handle_create_block(cmd: &str, swarm: &mut Swarm) {
    if let Some(data) = cmd.strip_prefix("create b") {
        let behaviour = swarm.behaviour_mut();
        let latest_block = behaviour
            .app
            .blocks
            .last()
            .expect("there is at least one block");
        let block = Block::new(
            latest_block.id + 1,
            latest_block.hash.clone(),
            data.to_owned(),
        );
        let json = serde_json::to_string(&block).expect("can jsonify request");
        behaviour.app.blocks.push(block);
        info!("broadcasting new block");
        behaviour
            .floodsub
            .publish(BLOCK_TOPIC.clone(), json.as_bytes());
    }
}
```

列出客户和打印区块链相当简单。创建块更有趣。

在这种情况下，我们使用`Block::new`来创建(和挖掘)一个新的块。一旦发生这种情况，我们将其转化并广播到网络，这样其他人就可以将它添加到他们的链中。

我们应该在这里放一些逻辑来重新尝试。例如，我们可以将它添加到一个队列中，并查看一段时间后，我们的块是否传播到广泛认可的区块链，如果没有，则获取认可链的新副本，并再次挖掘它以将其放在那里。如上所述，这种设计当然不会扩展到许多节点一直挖掘它们的块，但是对于本教程的目的来说这是可以的。

让我们启动它，看看它是否有效！

## 测试我们的锈区块链

我们可以使用`RUST_LOG=info cargo run`来启动应用程序。最好在不同的终端窗口中实际启动它的多个实例。

例如，我们可以启动两个节点:

```
INFO  rust_blockchain_example > Peer Id: 12D3KooWJWbGzpdakrDroXuCKPRBqmDW8wYc1U3WzWEydVr2qZNv
```

并且:

```
INFO  rust_blockchain_example > Peer Id: 12D3KooWSXGZJJEnh3tndGEVm6ACQ5pdaPKL34ktmCsUqkqSVTWX
```

在第二个应用程序中使用`ls p`向我们展示了与第一个应用程序的连接:

```
INFO  rust_blockchain_example::p2p > Discovered Peers:
 INFO  rust_blockchain_example::p2p > 12D3KooWJWbGzpdakrDroXuCKPRBqmDW8wYc1U3WzWEydVr2qZNv
```

然后，我们可以使用`ls c`来打印创世纪块:

```
INFO  rust_blockchain_example::p2p > Local Blockchain:
 INFO  rust_blockchain_example::p2p > [
  {
    "id": 0,
    "hash": "0000f816a87f806bb0073dcf026a64fb40c946b5abee2573702828694d5b4c43",
    "previous_hash": "genesis",
    "timestamp": 1636664658,
    "data": "genesis!",
    "nonce": 2836
  }
]
```

到目前为止，一切顺利。让我们创建一个块:

```
create b hello
 INFO  rust_blockchain_example      > mining block...
 INFO  rust_blockchain_example      > nonce: 0
 INFO  rust_blockchain_example      > mined! nonce: 62235, hash: 00008cf68da9f978aa080b7aad93fb4285e3c0dbd85fc21bc7e83e623f9fa922, binary hash: 0010001100111101101000110110101001111110011111000101010101000101111110101010110110010011111110111000010100001011110001111000000110110111101100010111111100001011011110001111110100011111011000101111111001111110101001100010
 INFO  rust_blockchain_example::p2p > broadcasting new block
```

在第一个节点上，我们看到:

```
INFO  rust_blockchain_example::p2p > received new block from 12D3KooWSXGZJJEnh3tndGEVm6ACQ5pdaPKL34ktmCsUqkqSVTWX
```

并呼叫`ls c`:

```
INFO  rust_blockchain_example::p2p > Local Blockchain:
 INFO  rust_blockchain_example::p2p > [
  {
    "id": 0,
    "hash": "0000f816a87f806bb0073dcf026a64fb40c946b5abee2573702828694d5b4c43",
    "previous_hash": "genesis",
    "timestamp": 1636664655,
    "data": "genesis!",
    "nonce": 2836
  },
  {
    "id": 1,
    "hash": "00008cf68da9f978aa080b7aad93fb4285e3c0dbd85fc21bc7e83e623f9fa922",
    "previous_hash": "0000f816a87f806bb0073dcf026a64fb40c946b5abee2573702828694d5b4c43",
    "timestamp": 1636664772,
    "data": " hello",
    "nonce": 62235
  }
]
```

该块已添加！

让我们开始第三个节点。它应该会自动获得这个更新的链，因为它比自己的链长(只有 genesis 块)。

```
INFO  rust_blockchain_example > Peer Id: 12D3KooWSDyn83pJD4eEg9dvYffceAEcbUkioQvSPY7aCi7J598q

 INFO  rust_blockchain_example > sending init event
 INFO  rust_blockchain_example::p2p > Discovered Peers:
 INFO  rust_blockchain_example      > connected nodes: 2
 INFO  rust_blockchain_example::p2p > Response from 12D3KooWSXGZJJEnh3tndGEVm6ACQ5pdaPKL34ktmCsUqkqSVTWX:
 INFO  rust_blockchain_example::p2p > Block { id: 0, hash: "0000f816a87f806bb0073dcf026a64fb40c946b5abee2573702828694d5b4c43", previous_hash: "genesis", timestamp: 1636664658, data: "genesis!", nonce: 2836 }
 INFO  rust_blockchain_example::p2p > Block { id: 1, hash: "00008cf68da9f978aa080b7aad93fb4285e3c0dbd85fc21bc7e83e623f9fa922", previous_hash: "0000f816a87f806bb0073dcf026a64fb40c946b5abee2573702828694d5b4c43", timestamp: 1636664772, data: " hello", nonce: 62235 }
```

在发送了`init`事件后，我们请求了第二个节点的链并得到了它。

在这里调用`ls c`向我们展示了相同的链:

```
INFO  rust_blockchain_example::p2p > Local Blockchain:
 INFO  rust_blockchain_example::p2p > [
  {
    "id": 0,
    "hash": "0000f816a87f806bb0073dcf026a64fb40c946b5abee2573702828694d5b4c43",
    "previous_hash": "genesis",
    "timestamp": 1636664658,
    "data": "genesis!",
    "nonce": 2836
  },
  {
    "id": 1,
    "hash": "00008cf68da9f978aa080b7aad93fb4285e3c0dbd85fc21bc7e83e623f9fa922",
    "previous_hash": "0000f816a87f806bb0073dcf026a64fb40c946b5abee2573702828694d5b4c43",
    "timestamp": 1636664772,
    "data": " hello",
    "nonce": 62235
  }
]
```

创建块也是可行的:

```
create b alsoworks
 INFO  rust_blockchain_example      > mining block...
 INFO  rust_blockchain_example      > nonce: 0
 INFO  rust_blockchain_example      > mined! nonce: 34855, hash: 0000e0bddf4e603da675b92b88e86e25692eaaa8ad20db6ecab5940bdee1fdfd, binary hash: 001110000010111101110111111001110110000011110110100110111010110111001101011100010001110100011011101001011101001101110101010101010100010101101100000110110111101110110010101011010110010100101111011110111000011111110111111101
 INFO  rust_blockchain_example::p2p > broadcasting new block
```

节点 1:

```
 INFO  rust_blockchain_example::p2p > received new block from 12D3KooWSDyn83pJD4eEg9dvYffceAEcbUkioQvSPY7aCi7J598q

ls c
 INFO  rust_blockchain_example::p2p > Local Blockchain:
 INFO  rust_blockchain_example::p2p > [
  {
    "id": 0,
    "hash": "0000f816a87f806bb0073dcf026a64fb40c946b5abee2573702828694d5b4c43",
    "previous_hash": "genesis",
    "timestamp": 1636664658,
    "data": "genesis!",
    "nonce": 2836
  },
  {
    "id": 1,
    "hash": "00008cf68da9f978aa080b7aad93fb4285e3c0dbd85fc21bc7e83e623f9fa922",
    "previous_hash": "0000f816a87f806bb0073dcf026a64fb40c946b5abee2573702828694d5b4c43",
    "timestamp": 1636664772,
    "data": " hello",
    "nonce": 62235
  },
  {
    "id": 2,
    "hash": "0000e0bddf4e603da675b92b88e86e25692eaaa8ad20db6ecab5940bdee1fdfd",
    "previous_hash": "00008cf68da9f978aa080b7aad93fb4285e3c0dbd85fc21bc7e83e623f9fa922",
    "timestamp": 1636664920,
    "data": " alsoworks",
    "nonce": 34855
  }
]

```

节点 2:

```
 INFO  rust_blockchain_example::p2p > received new block from 12D3KooWSDyn83pJD4eEg9dvYffceAEcbUkioQvSPY7aCi7J598q
ls c
 INFO  rust_blockchain_example::p2p > Local Blockchain:
 INFO  rust_blockchain_example::p2p > [
  {
    "id": 0,
    "hash": "0000f816a87f806bb0073dcf026a64fb40c946b5abee2573702828694d5b4c43",
    "previous_hash": "genesis",
    "timestamp": 1636664655,
    "data": "genesis!",
    "nonce": 2836
  },
  {
    "id": 1,
    "hash": "00008cf68da9f978aa080b7aad93fb4285e3c0dbd85fc21bc7e83e623f9fa922",
    "previous_hash": "0000f816a87f806bb0073dcf026a64fb40c946b5abee2573702828694d5b4c43",
    "timestamp": 1636664772,
    "data": " hello",
    "nonce": 62235
  },
  {
    "id": 2,
    "hash": "0000e0bddf4e603da675b92b88e86e25692eaaa8ad20db6ecab5940bdee1fdfd",
    "previous_hash": "00008cf68da9f978aa080b7aad93fb4285e3c0dbd85fc21bc7e83e623f9fa922",
    "timestamp": 1636664920,
    "data": " alsoworks",
    "nonce": 34855
  }
]
```

太棒了——它起作用了！

你可以尝试创造竞赛条件(例如，将难度增加到三个零，并在多个节点启动多个区块。你会立即注意到这个设计的一些缺陷，但基本的工作。我们有一个点对点的区块链应用程序，一个真正的分散式账本，具有基本的健壮性，完全是在 Rust 中从零开始构建的。厉害！

你可以在 [GitHub](https://github.com/zupzup/rust-blockchain-example) 找到完整的示例代码。

## 结论

在本教程中，我们在 Rust 中构建了一个简单的、非常有限的、但是可以工作的区块链应用程序。我们的区块链应用程序在短短 500 行 Rust 中有一个非常基本的挖掘方案、共识和点对点网络。

这种简单性大部分要归功于奇妙的 libp2p 库，它完成了网络方面的所有繁重工作。很明显，正如软件工程教程中的情况一样，对于一个生产级的区块链应用程序，有很多很多事情需要考虑和解决。

然而，这个练习为这个主题搭建了舞台，解释了一些基础知识，并在 Rust 中展示了它们，这样我们就可以继续这个旅程，看看我们将如何着手构建一个区块链应用程序，该应用程序实际上可以使用一个框架，如 [Substrate](https://github.com/paritytech/substrate) 。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。