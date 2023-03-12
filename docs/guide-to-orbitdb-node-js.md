# Node.js 中的 OrbitDB 使用指南

> 原文：<https://blog.logrocket.com/guide-to-orbitdb-node-js/>

## 介绍

当谈到分布式网络的数据库时， [OrbitDB](https://orbitdb.org/about/) 是一个众所周知的名字。OrbitDB 不是一个常规数据库，所有条目都有一个中央存储库；相反，它依赖于构建 P2P 分散应用程序(DApps)的协议，其中每个连接的对等点都有一个特定的数据库实例或副本。

典型的 DApps 依赖于分布式计算模型，其中系统组件在 P2P 基础上运行。P2P 网络提供了一个实例，在该实例中，文件可以在位于同一网络上的其他对等体之间被复制或同步。

在这种网络体系结构中有一种直接通信，与集中式 web 的客户机-服务器模型相反，在这种模型中，服务器充当其他系统连接和通信的中间层。

在实际的 P2P 模型中，存在与全球网络中的其他对等体的连接和交互，尽管对等体通常在本地单独开始，并且不与网络连接。

现在，让我们以 OrbitDB 为例，回顾一下分布式数据库的一些背景术语。

## “分布式数据库”和其他背景术语

就像分布式系统一样，分布式数据库可以很容易地复制和复制。数据通常存储在不同位置或地区的多个独立系统中，甚至存储在一个数据中心中。让我们在分布式数据库的背景下看看 OrbitDB。

首先，它利用了 [libp2p](https://github.com/libp2p/libp2p) ，这是一种网络协议。这是一个发布-订阅(pub/sub)协议，用于从多个对等方轻松同步数据库更新。

其次，为了数据的同质性和一致性，OrbitDB 实例必须在对等点之间复制。这是以这样一种方式发生的，一个对等体只需要订阅一个数据库地址，它就会自动开始复制它。

此外，OrbitDB 使用无冲突复制数据类型(CRDTs)。这是一种数据结构，其网络副本可以在副本不协调的情况下同时且独立地更新。这意味着对等体可以离开网格，当它们返回时，它们的状态将与其他对等体/节点同步，并且整个系统中的所有不一致将被解决。

最后，对于像 orbitDB 这样的分布式数据库，可以为一组可以写入数据库的对等点指定对数据的访问。请注意，默认情况下，如果数据库的创建者没有指定，则只有创建者拥有写访问权限。

### IPFS 吗

OrbitDB 利用了[星际文件系统(IPFS)](https://docs.ipfs.io/concepts/what-is-ipfs/#decentralization) 。IPFS 是一种用于在分布式文件系统中存储和共享数据的协议，根据其文档，它为分布式 web 提供了动力。

它使用内容寻址给每个资源一个唯一的标识符。这意味着当我们向 IPFS 添加内容时，会给它一个地址，通常包含一个哈希。

这将创建一组数据，这些数据将被存储并随后由连接的对等方访问。因此，对于数据检索，如果有数据，多个对等点可以同时响应，从而提高高延迟网络的性能。数据也可以是可验证的。

### DApps

分散系统背后的驱动力是创建一个更快、更安全、更可靠的网络。对于分散式应用程序(DApps ),试图连接到主机系统的第三方应用程序必须请求在本地运行的权限。

以 OrbitDB 为例，有一个访问控制层，当创建一个 DB 时，对等点可以在这个层定义一组公钥。这允许对等点拥有一个 DB 实例，多个其他对等点可以一次更新该实例。

### 对等网络

使用 ObitDB，网络中的每个对等点或节点都托管一个数据库实例。libp2p 发布/订阅协议允许 OrbitDB 轻松地同步来自多个对等方的数据库更新。

这允许在多个对等实例上主动备份用户的数据库，而不需要用户显式地连接到其中的任何一个。然而，进行连接的对等体需要具有它们想要在网络中与之连接的对等体的公共地址。

因此，OrbitDB 允许创建一个数据库网络，任何人都可以加入，只要他们有正确的访问权限来帮助保持数据可用，从而使整个网络链更加健壮和可持续。

## OrbitDB 入门

为了容易地跟随本教程，建议在我们的开发机器上安装 Node.js 和 npm。否则，我们就可以走了。请注意，我们将继续讨论其他必要的依赖项。

OrbitDB 包含了我们上面定义的所有技术。它是一种无服务器、分布式、P2P 类型的数据库，依赖于 [IPFS](https://ipfs.io/#how) 发布/订阅来存储数据和在连接的对等点之间同步。这允许对等点订阅给定主题的新消息或发布特定主题的消息。

网络中的对等点或节点只存储它们需要的数据和下一个节点的一些额外元数据。因此，用户或对等体可以持有网络中全部数据的一部分，并因此可以通过它们各自的地址来提供文件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 安装和设置

因为 OrbitDB 依赖于 IPFS，所以我们需要安装它。关于安装 IPFS 的各种方法的详细信息，请看文档中的[部分](https://docs.ipfs.io/install/)。然而，因为我们专注于 Node.js，并且我们打算以编程方式使用它，所以我们可以运行`npm install ipfs`来将其作为 npm 包安装。

或者，我们可以运行以下命令:

```
npm install orbit-db ipfs
```

创建一个轨道实例就像调用`createInstance()`方法一样简单。在这个方法签名中，我们被允许传递已经设置好的 IPFS 实例作为参数。这个实例带有可选的设置，以防我们需要专门使它可配置。

要创建 OrbitDB 实例，我们可以执行以下操作:

```
const IPFS = require('ipfs')
const OrbitDB = require('orbit-db')

// optional settings for the ipfs instance
const ipfsOptions = {
  EXPERIMENTAL: {
    pubsub: true
  }
}

 // Create IPFS instance with optional config
const ipfs = await IPFS.create(ipfsOptions)

 // Create OrbitDB instance
const orbitDB = await OrbitDB.createInstance(ipfs)

//create KV database
const db = await orbitdb.keyvalue('test-db')
```

正如我们在上面看到的，安装后我们已经导入了`ipfs`和`orbit-db`。然后，我们创建了一个新的 IPFS 实例，为特殊配置传递了一个可选的`ipfsOptions`参数。之后，我们继续创建一个 OrbitDB 实例，它允许我们与各种支持的数据库结构交互，例如`KeyValue`。

要获得上面代码块的完整、可工作的实现，请看一下附带的[GitHub repo](https://github.com/firebase007/orbitDB-demo/blob/master/peer1.js)。

传递给新实例化的 IPFS 实例的默认选项设置对象可以包含以下内容:

*   `EXPERIMENTAL: { pubsub: true }`–这启用了 IPFS 发布/订阅，这基本上是一种节点间通信的方法，如前所述
*   `config: { Bootstrap: [], Addresses: { Swarm: [] }}`–这将清空我们的引导节点列表(实例化时加载的节点)和集群节点列表(可以随时连接和断开的节点)
*   `repo: './ipfs'`–指定回购的路径，通常仅在 Node.js 中。在浏览器中，这不是真正必要的。注意，默认设置是我们机器主目录中一个名为`.jsipfs`的文件夹

此外，`createInstance`方法的签名如下所示:

```
createInstance(ipfs, [options])
```

对进一步配置有用的可选`options`设置是一个可能包含以下任何属性的对象:

*   `directory`–用于数据库文件的路径。默认情况下，它使用`'./orbitdb'`
*   `peerId`–默认情况下，它使用 IPFS 对等 ID 的 Base58 字符串
*   `identity`–默认情况下，它创建一个`[Identity](https://github.com/orbitdb/orbit-db-identity-provider/blob/master/src/identity.js)`的实例
*   `offline`–在离线模式下启动 OrbitDB 实例。请注意，在脱机模式下启动实例时，不会复制数据库

> **注意**:如果 OrbitDB 实例是在离线模式下启动的，并且您想要开始复制数据库，则需要重新创建 OrbitDB 实例。

例如，为了与一个`Key-Value`数据库交互，我们可以像这样使用新创建的 OrbitDB 实例:

```
const db = orbitDB.keyvalue('test-db')
```

完成这一步后，我们可以访问数据库[地址](https://github.com/orbitdb/orbit-db-store#address)，它作为一个标识符，也作为其他数据库对等体或客户端复制的指针。要访问该地址，我们可以执行以下操作:

```
console.log(db.address.toString()) // convert the database address object to a string with the toString() method.
```

当这被记录到控制台时，我们会得到类似下面的输出:

```
/orbitdb/zdpuB1ccfqAVXPhf4zBBCohvvbDWV1k6S6thTujzy2CHQBPAx/test-db
```

数据库地址包含三个部分，即:

*   由`/orbitdb`表示的协议
*   中间的 IPFS 散列，通常是一个包含数据库信息组合的对象，称为清单，由散列`zdpuB1ccfqAVXPhf4zBBCohvvbDWV1k6S6thTujzy2CHQBPAx`表示
*   数据库名称，在上例中是`test-db`

还要注意，OrbitDB 提供了一个 API 方法`isValidAddress`，来验证 DB 地址的有效性。请注意，它返回一个布尔值。下面让我们看看如何做到这一点:

```
OrbitDB.isValidAddress('/orbitdb/Qmd8TmZrWASypEp4Er9tgWP4kCNQnW4ncSnvjvyHQ3EVSU/test-db')
// true
```

## 创建数据库支持的数据模型

OrbitDB 有不同类型的数据库。它通过将不同的 API 分成存储来组织其功能，每个存储满足不同的目的。

每个商店都有自己特定的 API 方法来创建、删除、检索和更新数据。要获得受支持商店的列表，我们可以执行以下操作

```
OrbitDB.databaseTypes // Returns supported database types  as an Array of Strings
// [ 'counter', 'eventlog', 'feed', 'docstore', 'keyvalue']
```

此外，我们可以通过调用如下所示的`isValidType`方法来检查所提供的`String`是否是受支持的数据库类型，该方法返回一个布尔值。

```
OrbitDB.isValidType('docstore')
// true
```

下面讨论了预定义商店的列表:

*   `log`–不可变的只写数据库，主要用于消息队列系统或事务列表
*   `feed`–可变日志，可以添加和删除条目。主要用于购物车、博客帖子、评论或 Twitter 风格的时间线
*   `doc`–存储 JSON 文档的文档数据库，可以通过指定的键进行索引。对于构建搜索索引非常有用
*   `keyvalue`–支持 JSON 可序列化数据的简单键值数据库
*   `counter`–用于计数事件或通常有序数据的纯增量整数计数器。

> **注意**:添加自定义数据库类型，我们要做的就是调用 OrbitDB 公开的`addDatabaseType` API 方法，如下图:
> 
> ```
> const CustomStore = require('./CustomStore')
> OrbitDB.addDatabaseType(CustomStore.type, CustomStore)
> ```

此外，值得注意的是，除了特定的方法之外，每个数据库存储都有以下可用的方法:

*   `store.load()`–将本地保存的数据库状态加载到内存中
*   `store.close()`–这将关闭数据库
*   `store.drop()`–这将在本地删除数据库。但是，这不会删除已经连接的对等方的任何数据
*   `store.type`–以`String`的形式返回数据库的类型

更多细节和信息可在[文档](https://github.com/orbitdb/orbit-db/blob/master/API.md#store-api)中找到。现在，让我们看看如何通过新创建的 OrbitDB 实例与不同的数据库进行交互:

```
# for a log database type
const db = await orbitdb.eventlog()

# for a feed database type
const db = await orbitdb.feed()

# for a key keyvalue database type
const db = await orbitdb.keyvalue()

# for a docs database type
const db = await orbitdb.docs()

# for a counter database type
const counter = await orbitdb.counter()
```

> **注意**:我们可以根据自己的具体用例来决定编写自己的商店。

## 在野外使用 OrbitDB:演示应用程序

### 数据库对等方之间的通信

在这一节中，我们将了解如何识别一个连接的对等体并获得对数据库的必要访问。首先，让我们构建我们的 IPFS 节点，如`index.js`文件所示:

```
// optional settings for the ipfs instance
const ipfsOptions = {
    EXPERIMENTAL: {
      pubsub: true
    },
  }

const ipfs = await IPFS.create(ipfsOptions)
```

`create()`方法接受一个可选的`ipfsOptions`，它是一个具有不同属性的对象，我们可以将其作为参数传递。这里，我们传递了一个`EXPERIMENTAL`属性，这个对象允许我们启用或者添加`pubsub`到我们刚刚创建的`ipfs`实例中。

接下来，我们可以继续为我们的数据库创建身份。数据库中的每个条目都由创建者签名。为此，我们可以使用`createIdentity`方法，就像这样:

```
const identity = await Identities.createIdentity(options)
```

在我们这样做之前，我们应该确保导入`orbit-db-identity-provider`。当我们将身份登录到控制台时，上面命令的输出如下所示:

```
console.log(identity.toJSON()

//output 
{
  id: '034b8a8931164238b1a8c598fcf0d73245780174bf0cb100d93cb3098ba4b19ff2',
  publicKey: '04ad4d2a7812cac1f0e6331edf22cec1a74b9694de6ad222b7cead06f79ec44a95e14b002ee7a0f6f03921fcf2ff646724175d1d31de4876c99dcc582cde835b4c',
  signatures: {
    id: '304402203a7fa472dc584f02aabb27111eab48bc50b0c2137876cd08db89842870aa5abe022069a05962ab9d3d28ff5d7587503852c210e3de65e7fe4bfa0a25ba96a5f078f3',
    publicKey: '3044022049a5885d613a7dd70cd21bad46e159645202911e2d2c16e1be7681ec6b84a272022024575ef612119fbb8e374862d8178b4c0a44f3655400626de4b6ea89e12fb488'
  },
  type: 'orbitdb'
}
```

> **注意**:该对象包含证明拥有某个外部标识符和 OrbitDB 公钥的签名。这是为了证明 OrbitDB 中外部标识符的所有权。

在上面的示例中，`id`属性返回外部身份的 ID。`publicKey`返回用于签署 OrbitDB 条目的签名密钥，而`signatures`返回包含两个签名的对象，如图所示。

为了获取我们创建的 OrbitDB 实例的公钥，我们可以运行以下命令:

```
console.log(identity.publicKey)
//output
04ad4d2a7812cac1f0e6331edf22cec1a74b9694de6ad222b7cead06f79ec44a95e14b002ee7a0f6f03921fcf2ff646724175d1d31de4876c99dcc582cde835b4c
```

下一步是用我们之前创建的身份创建一个 OrbitDB 实例。为此，我们可以使用`createInstance`方法，该方法接受已经创建的`ipfs`实例和一个可选的设置对象:

```
const orbitdb = await OrbitDB.createInstance(ipfs,  { identity: identity })
```

下一步是用 OrbitDB 从受支持的数据库存储中实际创建一个数据库。然而，在我们这样做之前，我们可以为谁对我们的数据库具有写访问权限设置访问控制选项。

为此，我们可以继续定义一组对等点，这些对等点可以写入我们的数据库，或者允许任何人通过通配符写入数据库。

> **注意**:默认情况下，只有数据库的创建者有写权限。

```
const optionsToWrite = {
    // Give write access to the creator of the database
    accessController: {
      type: 'orbitdb', //OrbitDBAccessController
      write: [orbitdb.identity.id, '04ad4d2a7812cac1f0e6331edf22cec1a74b9694de6ad222b7cead06f79ec44a95e14b002ee7a0f6f03921fcf2ff646724175d1d31de4876c99dcc582cde835b4c'],
    }
```

从上面我们可以看到，我们已经使用`accessController` options 属性授予自己对数据库的写访问权。请注意，要允许任何人写入数据库，我们可以执行以下操作:

```
write: ['*'] //enable write access to the public
```

> **注意**:要给另一个数据库或对等体写访问权，我们可以在调用`identity.publicKey`时使用它们的公钥。

现在，以创建一个`doc`数据库为例，我们可以做如下操作:

```
const db = await orbitdb.docs('test-db', optionsToWrite)
```

`optionsToWrite`是我们对数据库的访问控制权。

接下来，让我们向数据库添加一个项目:

```
await db.put({ _id: 'test', name: 'test-doc-db', category: 'distributed' })
```

要获取我们的数据库地址，我们可以运行以下命令:

```
const address = db.address.toString()

//output
orbitdb/zdpuB1ccfqAVXPhf4zBBCohvvbDWV1k6S6thTujzy2CHQBPAx/test-db
```

此外，我们可以在数据库创建后授予对它的访问权限。要明确做到这一点，请通过运行以下命令授予对数据库的写访问权限:

```
await db.access.grant('write', '04ad4d2a7812cac1f0e6331edf22cec1a74b9694de6ad222b7cead06f79ec44a95e14b002ee7a0f6f03921fcf2ff646724175d1d31de4876c99dcc582cde835b4c') // grant access to database2
//where the hash is the `identity2.publicKey`
```

因为 OrbitDB 将数据库的状态自动保存到磁盘，所以您可以在使用数据库之前在本地加载它。因此，在打开数据库时，我们可以选择在使用数据库之前在本地加载持久化的数据。

为此，我们可以使用`load()`方法:

```
await db2.load()
```

此外，我们可以从数据库中获取一个值或条目。为此，我们可以调用适当的函数，这些函数对于不同的数据库类型是不同的:

```
const value2 = db2.get('') // this gets all the entries in the database store

console.log(value2)
//output
[
  { _id: 'test', name: 'test-doc-db', category: 'distributed' },
  { _id: 'test2', name: 'test-doc-db2', category: 'nil' }
]
```

OrbitDB 中的所有数据库模型都是在`ipfs-log`之上实现的，这是一个不可变的、基于操作的 CRDTs。它是一个只附加的日志，可以用来在 P2P 应用程序中建模一个可变的共享状态。

> **注意**:日志中的每个条目都保存在 IPFS 中，每个条目都指向以前条目的散列，从而形成一个图。

使用`ipfs-log`的详细片段可以在文档的[示例部分](https://github.com/orbitdb/ipfs-log#quick-start)中找到。

## CRUD 操作

OrbitDB 自带一个干净易用的 API 接口。它有`get`、`put`、`add`、`set`等功能/方法。关于不同公开 API 的细节可以在文档的[这一节](https://github.com/orbitdb/orbit-db/blob/master/API.md#orbitdbkeyvaluenameaddress)中找到。

让我们看看下面支持的数据库公开的 API 方法的一些例子:

#### `keyvalue`

在`keyvalue`数据库类型中，公开的 API 方法包括:`put`、`set`、`get`。例如`put`的签名是`put(key, value)`，它接受一个密钥或数据库名称以及我们想要更新的值。更多详情可在单据的[本节](https://github.com/orbitdb/orbit-db/blob/master/API.md#orbitdbkeyvaluenameaddress)中找到。

#### `log`

在`log` DB 类型中，我们有`add`、`get`和`iterator`方法，在这里将更详细地解释[。](https://github.com/orbitdb/orbit-db/blob/master/API.md#orbitdblognameaddress)

#### `feed`

在`feed`数据库类型中，我们有`add`、`get`、`remove`和`iterator`。关于这些的更多细节可以在文档中找到[这里](https://github.com/orbitdb/orbit-db/blob/master/API.md#orbitdbfeednameaddress)。

#### `doc`

在`doc`数据库类型中，公开的 API 方法有`put`、`get`、`query`和`del`。更详细的使用案例可以在文档中的[这里](https://github.com/orbitdb/orbit-db/blob/master/API.md#orbitdbdocsnameaddress-options)找到。

#### `counter`

在`counter`数据库类型中，公开的 API 方法包括`value`和`inc`。更详细的信息可以在[这里](https://github.com/orbitdb/orbit-db/blob/master/API.md#orbitdbcounternameaddress)的文档中找到。

## 结论

为什么 OrbitDB 是 DApps 和区块链应用的绝佳选择？因为它使用了一种特殊的数据结构，称为无冲突复制数据类型(CRDT ),以保持最终的一致性。这意味着操作可以在不同的时间发生而不需要协调，假设它们最终会同步。

每次我们与 OrbitDB 数据库进行交互时，我们都在与一个快照进行交互。这就是分布式数据库的设计方式。他们在网上和网下都工作。但是，它确实要求至少有一个节点或对等点愿意持久存储数据库，以便在断开连接时数据不会丢失。

数据通过内容地址相互链接，这与集中式 web 的基于位置的寻址相反，在集中式 web 中，应用程序代码在集中式服务器上运行。

然而，应该注意的是，OrbitDB 和底层的 IPFS 层目前是 alpha 阶段的软件。它们既可以在 Node.js 应用程序中运行，也可以在浏览器中运行，但是目前不支持 Windows 操作系统。

关于 OrbitDB 功能的更多信息可以在 Github 上的[项目库中找到。用于本教程的脚本也可以在 GitHub 的](https://github.com/orbitdb/orbit-db/blob/master/API.md#creating-an-orbitdb-instance)[这里](https://github.com/firebase007/orbitDB-demo)找到。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.