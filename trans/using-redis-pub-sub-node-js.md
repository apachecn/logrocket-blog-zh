# 通过 Node.js - LogRocket Blog 使用 Redis 发布/订阅

> 原文：<https://blog.logrocket.com/using-redis-pub-sub-node-js/>

如今，在构建应用程序时，开发人员经常会遇到将系统解耦并分离成组件的需求。但是，由于应用程序内置于不同的组件中，每个组件都无法与其他组件通信或理解其他组件的工作方式。

它们之间唯一能够提供这种通信和信息的通信是使用发布/订阅(pub/sub)模式。

在本文中，我们将研究发布/订阅模式，如何在 Node.js 应用程序中设置它，如何实现该模式，并了解使用 Redis 的替代方案。这包括:

在我们这样做之前，在深入了解在 Node.js 中使用该模式的本质之前，充分理解该模式是很重要的。

*   Node.js(节点. js)
*   再说一遍
*   redis client for node . js 节点的用户端

## 了解发布/订阅模式

发布/订阅是一种消息传递模式，其中不同的组件相互发布和订阅，以相互通知和发送数据。

它还允许组件的解耦，每个组件都依赖于一个消息代理。基本上，发布者发布消息，订阅者订阅发布者以从消息代理接收消息。

使用这种模式提供了简单的开发，因此开发人员可以工作并关注他们的组件或服务，而不需要理解其他组件的接口。

这种模式还提供了可伸缩性，因此组件是松散耦合的，很容易变得健壮和安全。

## 使用 Redis

Redis 是一个内存系统，可以以特定的格式存储所有类型的数据，它的主要目的是缓存数据，通常选择它而不是替代系统，[尤其是](https://blog.logrocket.com/11-database-drivers-and-orms-for-rust-that-are-ready-for-production/#memcachehttpsdocsrsmemcache0140memcache) `[memcached](https://blog.logrocket.com/11-database-drivers-and-orms-for-rust-that-are-ready-for-production/#memcachehttpsdocsrsmemcache0140memcache)`，因为它能够以健壮的方式存储任何类型的数据。

还需要注意的是，Redis 使用一种数据结构来保存数据，并且没有查询语言或者没有灵活性来执行一些特别的查询。

### 将 Redis 用于发布/订阅

当使用发布/订阅时，我确实倾向于使用 Redis，因为它很简单；不需要什么硬科学，但前提是应用程序简单且不复杂。

另外，请注意 Redis 是一个内存系统，只有在分配给它的 RAM 有能力的情况下，才能处理数据。这意味着 Redis 适用于实时消息传递和快速无延迟发送数据的系统。

缓存的其他用例包括。

## 为发布/订阅设置 Redis

在开始之前，我们知道 Redis 实现了发布/订阅消息传递模式，这意味着发布者向订阅者发送消息，订阅者收到消息。请注意，一个以上的组件可以订阅一个发布者。

让我们看看它在 Redis-CLI 上是如何工作的:

```
Vectormikes-MacBook-Pro:Projects macbookpro$ redis-cli
127.0.0.1:6379> SUBSCRIBE article
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "article"
3) (integer) 1

```

上面的终端显示用户订阅了一个名为`article`的频道。

现在让我们打开一个新的终端窗口:

```
Vectormikes-MacBook-Pro:Projects macbookpro$ redis-cli
127.0.0.1:6379> PUBLISH article Pub/Sub
(integer) 1
127.0.0.1:6379> 

```

然后，我们可以将消息`Pub/Sub`发布到同一个通道`article`:

```
Vectormikes-MacBook-Pro:Projects macbookpro$ redis-cli
127.0.0.1:6379> SUBSCRIBE article
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "article"
3) (integer) 1
1) "message"
2) "article"
3) "Pub/Sub"

```

同一频道的所有用户都收到相同的消息。

请注意，在开始之前，Redis 服务器必须安装在您的计算机上。

接下来，创建一个包含工作目录的文件夹，并将其命名为`redisNode`:

```
$ mkdir redisNode && cd redisNode

```

理想情况下，我们希望创建两个不同的文件作为发布者和订阅者。但是，让我们首先创建两个文件夹，它们将是具有各自服务器的独立组件:

```
$ mkdir publish subscribe

```

接下来，初始化发布组件的项目:

```
$ cd publish && npm init -y

```

然后，为发布组件初始化项目:

```
$ cd subscribe && npm init -y

```

现在，我们的服务器有两个独立的组件，一个作为发布者，另一个作为订阅者。

## 用 Redis 在 Node.js 中实现发布

我们这里的重点是实现将消息发布到特定通道的组件。

要在 Node.js 中使用 [Redis，我们需要安装`redis`包。它充当我们机器上 Redis 实例的客户机:](https://github.com/NodeRedis/node-redis)

```
$ npm install redis

```

现在我们可以在发布目录中创建一个包含我们代码的`server.js`文件:

```
const redis = require('redis');
const publisher = redis.createClient();

(async () => {

  const article = {
    id: '123456',
    name: 'Using Redis Pub/Sub with Node.js',
    blog: 'Logrocket Blog',
  };

  await publisher.connect();

  await publisher.publish('article', JSON.stringify(article));
})();

```

在上面的代码中，我们导入 Redis 并调用`createClient()`方法。现在，我们想发布一个包含`id`、`name`和`blog`的`article`对象。

有了这个对象，我们可以将它作为消息发送到`article`通道:

```
$ publisher.publish('article', JSON.stringify(article));

```

## 用 Redis 在 Node.js 中实现 subscribe

既然我们已经有了一个准备发布消息的组件，那么让我们实现一个将接收这些消息的`subscribe`组件。理想情况下，我们可以创建或拥有多个组件。

首先，确保安装了 Redis:

```
$ npm install express redis

```

接下来，在您的`subscribe`目录中创建一个`server.js`文件来包含实现:

```
const redis = require('redis');

(async () => {

  const client = redis.createClient();

  const subscriber = client.duplicate();

  await subscriber.connect();

  await subscriber.subscribe('article', (message) => {
    console.log(message); // 'message'
  });

})();

```

要订阅，请打开您的终端并运行订阅文件`server.js`，并在发布消息后等待消息:

```
Vectormikes-MacBook-Pro:subscribe macbookpro$ node server.js
{"id":"123456","name":"Using Redis Pub/Sub with Node.js","blog":"Logrocket Blog"}

```

要发布，请打开另一个终端并运行发布文件。

相反，如果您希望在同一个服务器上实现发布/订阅，而不是在不同的服务器上，那么需要注意的是，您不能在同一个 Redis 客户端上实现，因为一个 Redis 客户端不能处理发布/订阅；必须有两个可用。

因此，您必须添加两个 Redis 客户端来提供订阅和发布功能:

```
const subscriber = redis.createClient({ ... })
const publisher = redis.createClient({ ... })

```

另外，不要忘记在异步函数中连接 Redis 客户机:

```
await client.connect()

```

## 使用 Redis 的替代方法

除了使用 Redis 之外，我们确实有 Redis 的替代方案来实现发布/订阅模式，尤其是在 Node.js 应用程序中。以下是 Redis 的常用替代方案列表。

### 阿帕奇卡夫卡

Apache Kafka 比 Redis 快得多，[比 Redis](https://engineering.linkedin.com/kafka/benchmarking-apache-kafka-2-million-writes-second-three-cheap-machines) 能处理更多的数据。Kafka 也倾向于长时间保存数据。

### 兔子 q

RabbitMQ 是一个比 Redis 更高级的专用消息代理，因为它被设计为发布/订阅模式的消息代理，提供更多的配置和场景。Redis 只是一个内存存储，是一个理想的数据库。

### 谷歌云发布/订阅

与 Redis 相比，Google Cloud Pub/Sub 更具[可扩展性，如果不是高度可扩展性的话](https://cloud.google.com/pubsub/architecture#scalability)。但是，它[比 Redis](https://firebase.google.com/docs/firestore/data-model) 更难配置。

### Firebase 云消息传递

说到 [Firebase 云消息](https://blog.logrocket.com/using-firebase-cloud-messaging-as-a-pub-sub-service/)，选择权在你，但是 Firebase 提供了一个文档模型，而不是 Redis 中的内存。

## 结论

在这个简短的教程中，我们研究了如何快速设置 Node.js 应用程序，以使用 Redis 启动发布/订阅模式。这种方法适合微服务或松散耦合的架构。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.