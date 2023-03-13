# 用 Node.js 中的 Kafka 构建一个持久的发布订阅

> 原文：<https://blog.logrocket.com/build-durable-pub-sub-with-kafka-node-js/>

在当今世界，处理数据已经成为任何面向用户的应用程序的核心要素之一。虽然它在开始时可能看起来无关紧要，但当您处理成千上万的每日活跃用户时，处理好它就不是那么微不足道了。

Kafka 帮助您构建持久、容错和可伸缩的数据管道。此外，它已经被 Twitter、LinkedIn 和网飞等应用程序所采用。

在本文中，我们将了解 Kafka pub-sub 是什么，它如何提供帮助，以及如何在 Node.js API 中开始使用它。

*向前跳转:*

## 什么是 pub-sub？

发布-订阅是一种分离连接两端并异步通信的方式。这种设置由发布者(pub)和订阅者(sub)组成，其中发布者广播事件，而不是以同步或阻塞的方式针对特定的订阅者。然后，订阅者使用来自发布者的事件。

有了这种机制，订阅者不再被绑定到给定的生产者，可以按照自己的步调消费和处理事件。它提高了可伸缩性，因为单个广播事件可以被多个订户消费。这是从单纯的 HTTP 请求向前迈出的一大步。

与此相反，HTTP 请求-响应循环让请求者等待响应，并有效地阻止他们做任何其他事情。这是在当今基于微服务的应用环境中转向基于事件的架构的一个重要原因。

## 卡夫卡简介

Kafka 提供了三种主要功能:

1.  发布-订阅机制
2.  存储数据(即使在消费完成后)的时间长短由您决定
3.  批处理或实时处理事件(特定于用例)

Kafka 确保任何发布的事件都可以被多个消费者消费，并且这些事件在消费后不会被删除或从存储中移除。它允许您多次使用同一事件，因此数据持久性是一流的。

现在我们知道了卡夫卡提供了什么，让我们看看它是如何工作的。

![Kafka fundamentals overview](img/3d5ace6bece3c0e50a680a677cad7672.png)

Figure 1: Kafka fundamentals overview

一个新的、自托管或管理的 Kafka 集群主要包含代理。Kafka broker 是运行 Kafka broker 进程的计算机或云实例。它管理分区的子集，并处理向这些分区写入或读取新事件的传入请求。写通常只发生在运行 leader 的实例上；其他实例通过复制完成。对于一个给定的主题及其分布在多个经纪人中的分区，如果一个主要经纪人去世，Kafka 会从追随者中无缝地选出一个新的领导者。

生产者和消费者使用 Kafka broker 来编写事件和阅读 Kafka 主题。主题被进一步划分为分区。为了跟踪处理(和提交)了多少个事件，Kafka 维护了一个偏移值，它只是一个反映处理的事件数量的递增数字。

当消费者死亡或变得无响应时(即，未能向 [`sessionTimeout`](https://kafka.js.org/docs/consuming#a-name-options-a-options) ms 集合内的代理发送心跳)，Kafka 将这些孤立分区重新分配给剩余的消费者。当一个新的消费者加入消费者组时，也会发生类似的负载平衡。这个重新分配和重新评估负载的过程称为重新平衡。当卡夫卡的消费者重新平衡时，他们从最新的未承诺补偿开始消费。

### 分区和消费者计数的实际限制

一个主题的分区只能由一个使用者组中的一个使用者使用。但是来自不同使用者组的多个使用者可以各自使用同一个分区。这在上面主题 1 的分区 3 的流程图中进行了描述。

## 设置我们的 Node.js 项目

在本教程中，我们将使用一个准系统 Express.js API。你可以在这里找到启动代码。我们将使用它作为基础，并将 Kafka 支持添加到 API 中。我们可以对数据做更多的事情，但是 API 有意保持简单，以便我们可以专注于卡夫卡。

如果您想跟进，请在您的机器上本地运行 API。以下是步骤:

1.  克隆存储库:
    `git clone [[email protected]](/cdn-cgi/l/email-protection):Rishabh570/kafka-nodejs-starter.git` 
2.  签出到起始分支:
    `git checkout starter`
3.  安装软件包:
    `npm install`
4.  运行服务器:
    `npm start`

现在 API 已经在您的机器上本地运行了，让我们安装 Kafka。

## 安装 Kafka

在我们开始生产和消费事件之前，我们需要将 Kafka 安装到我们的 API 中。我们将对 Node.js 使用 [Kafka.js 客户端库](https://kafka.js.org/)。

运行以下命令进行安装:

```
npm install kafkajs

```

接下来，安装 Kafka CLI 工具。它有助于管理任务和卡夫卡实验。只需前往[kafka.apache.org](https://kafka.apache.org/)下载卡夫卡。

我们现在正式准备好进入有趣的东西。让我们创建一个卡夫卡主题来开始产生事件。

## 创造一个卡夫卡主题

在卡夫卡中，你需要一个主题来发送事件。一个卡夫卡主题可以理解为一个文件夹；同样，该主题中的事件是该文件夹中的文件。在`channelA`发送的事件将与`channelB`中的事件保持隔离。卡夫卡主题允许多个通道之间的隔离。

让我们使用在上一步中下载的 Kafka CLI 创建一个主题:

```
bin/kafka-topics.sh --create --topic demoTopic --bootstrap-server localhost:9092

```

我们使用了`kafka-topics.sh`脚本来创建卡夫卡主题。运行上述命令时，请确保您位于下载 Kafka CLI 工具的文件夹中。

我们创建了一个名为`demoTopic`的主题。你可以给它起任何名字；我建议在创建主题时遵循命名约定。对于一个电子商务应用程序，Kafka 主题的命名法可以是这样的:

*   `macbook14_wishlisted_us_east_1_app`
*   `macbook14_wishlisted_us_east_2_app`
*   `macbook14_wishlisted_us_east_1_web`
*   `macbook14_wishlisted_us_east_2_web`

您可能已经注意到，它利用了`item`和`user`属性来分配主题名称。这不仅从你的肩上卸下了一个主要的责任，它还能立即告诉你这个话题会引发什么样的事件。当然，您可以根据您的具体项目使名称更加具体和细化。

## 建立 Kafka 经纪人

要发送事件，我们还需要一样东西:代理。

正如我们前面所学的，经纪人负责编写事件并从主题中读取事件。我们将确保它在我们的代码运行之前被设置好。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这是我们的`index.js`文件的样子:

```
​​const { Kafka } = require('kafkajs')

const kafka = new Kafka({
  clientId: 'kafka-nodejs-starter',
  brokers: ['kafka1:9092'],
});

```

在 API 使用路由来产生或消费事件之前，它连接到 Kafka 客户端并创建两个代理。就是这样。

现在让我们继续实际生产事件。

## 编写我们的第一个制片人

我们已经在 Node.js 应用程序上成功安装了 Kafka。是时候写我们的第一个卡夫卡制作人了，学习一下它是如何工作的。

```
const producer = kafka.producer()

// Connect to the producer
await producer.connect()

// Send an event to the demoTopic topic
await producer.send({
  topic: 'demoTopic,
  messages: [
    { value: 'Hello micro-services world!' },
  ],
});

// Disconnect the producer once we're done
await producer.disconnect();

```

我们用上面的代码处理三个步骤:

1.  连接到卡夫卡制作人
2.  发送一个关于我们创建的主题的测试事件
3.  一旦我们用完了，就断开生产者的连接

这是一个简单的例子。客户端库提供了一些配置设置，您可能希望根据需要进行调整。其中包括:

*   幂等的:如果设置，Kafka 生产者将确保事件只被写入主题一次。您可以选择禁用它，并在消费时处理等幂
*   重试:使用 Kafka.js 生成消息时，可以选择自定义重试机制；点击阅读更多关于重试机制如何工作的信息

## 配置您的 Kafka 消费者

我们已经写了我们的 Kafka producer，它已经准备好发送关于`demoTopic`主题的事件。让我们构建一个 Kafka 消费者，它将收听相同的主题并将其登录到控制台。

```
const consumer = kafka.consumer({ groupId: 'test-group' })

await consumer.connect()
await consumer.subscribe({ topic: 'demoTopic', fromBeginning: true })

await consumer.run({
  eachMessage: async ({ topic, partition, message }) => {
    console.log({
      value: message.value.toString(),
    })
  },
});

```

下面是上面代码片段中发生的情况:

1.  我们创造了一个消费群体
2.  我们联系消费者并订阅我们的`demoTopic`
3.  最后，我们处理消费的消息，并将它们记录到控制台

## Kafka 消费者定制选项

Kafka 消费者提供了许多选项和很大的灵活性，允许你决定如何消费数据。这里有一些你应该知道的设置:

### 自动提交

在 Kafka 中提交意味着在处理后将消息/事件保存到磁盘。正如我们所知，`offset`只是一个跟踪所处理的消息/事件的数字。

如果发生了重新平衡，提交通常可以确保我们不会浪费资源再次处理相同的事件。但这也增加了网络流量，降低了处理速度。

### 最大字节

这告诉 Kafka 在响应中累积的最大字节数。此设置可用于限制从 Kafka 获取的消息的大小，避免应用程序不堪重负。

### 从头开始

默认情况下，Kafka 从最新的偏移量开始消费。如果想从主题的开头开始消费，使用`fromBeginning`标志。

```
await consumer.subscribe({ topics: ['demoTopic], fromBeginning: true })

```

### 寻求

您也可以选择使用特定偏移量的事件。只需传递您想要开始消费的偏移量。

```
// This will now only resolve the previous offset, not commit it
consumer.seek({ topic: 'example', partition: 0, offset: "12384" })

```

客户端还为消费者提供了简洁的暂停和恢复功能。这允许您构建自己定制的[节流](https://blog.logrocket.com/how-and-when-to-debounce-or-throttle-in-react/)机制。

还有许多其他选项可供探索，您可以根据自己的需要进行配置。你可以在这里找到所有的消费者设置[。](https://kafka.js.org/docs/consuming#a-name-options-a-options)

## 为什么 Kafka pub-sub 比使用 HTTP 请求好？

HTTP 有其有效的用例，但很容易过度使用。存在 HTTP 不适合的场景。例如，您可以将事件注入到名为`order_invoices`的 Kafka 主题中，以便将它们发送给在购买过程中请求它们的客户。

与通过 HTTP 发送事件相比，这是一种更好的方法，因为:

1.  它允许你分离事件发送者和接收者。因为您不期望(或不需要)立即得到响应，所以 HTTP 请求没有帮助
2.  它是耐用的。因为 Kafka 事件不会在消费时被丢弃，所以如果通知发送服务突然失败，您可以重放这些事件
3.  它是可扩展的。您可以在多个地方使用 Kafka 事件，甚至在不同的服务或 API 中

## 消费后会怎样？

如果您有幂等性需求，我建议在您的服务中也有一个幂等性层。使用幂等层，您可以重放所有 Kafka 事件，以及两个给定时间戳之间的事件。

如何在给定的时间段内只过滤掉特定的内容？仔细看看，卡夫卡并没有(也不应该)解决这个问题。

这就是应用程序级等幂层发挥作用的地方。如果在特定时间戳之间重放所有事件，服务中的幂等机制会确保只处理最先出现的事件。

一个简单的幂等性解决方案可以是从请求中选择一个惟一的常量参数(比如我们的发票示例中的订单 ID ),并使用该参数创建一个 Redis 键。因此，对于初学者来说，类似下面的内容应该是有用的:

```
notification:invoices:<order_id>

```

这样，即使您重放事件，您的应用程序也不会因为发送发票通知而再次处理相同的订单。此外，如果希望这个 Redis 键只在一天内有效，可以相应地设置 TTL。

## 卡夫卡在现实世界中的应用

Kafka pub-sub 可用于多种场合:

1.  在大规模、分布式消息传递平台中，由于其持久性、复制性和容错性
2.  对于用户活动跟踪:Kafka 提供消息的批处理，可用于后处理和存储用户点击流和行为信息
3.  作为处理非时间敏感事件的流管道。例如，当商品有货时向客户发送通知，发送公告或促销活动通知
4.  在修订历史中，提供在特定时间范围内执行的历史操作
5.  存储在消费和处理一次后不想丢失的事件。这有助于重放或重新同步您的应用程序和某些操作

Twitter 使用 Kafka 作为其主要的发布-订阅渠道。LinkedIn 使用它每天处理超过 [7 万亿条消息](https://engineering.linkedin.com/blog/2019/apache-kafka-trillion-messages)，Kafka 事件流由于其高耐用性、线性可伸缩性和容错性，被用于所有点对点和跨[网飞](https://www.confluent.io/blog/how-kafka-is-used-by-netflix/)工作室的通信。

说 Kafka 是可伸缩服务最重要的基石之一，满足了数以百万计的请求，这不会错。

## 结论

我们已经了解了 Kafka pub-sub 是什么，它是如何工作的，以及在 Node.js 应用程序中利用它的一种方法。

现在，你拥有了开始你的卡夫卡之旅所需要的所有工具和知识。你可以从这个 [kafka-node-starter](https://github.com/Rishabh570/kafka-nodejs-starter) 库中获得完整的代码，以防你跟不上。只需克隆存储库并遵循本指南中显示的步骤。当你按照这个动手做的时候，你一定会有更好的理解。

如有任何疑问和反馈，请在评论中联系我们。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.