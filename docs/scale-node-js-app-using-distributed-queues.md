# 使用分布式队列扩展 Node.js 应用程序

> 原文：<https://blog.logrocket.com/scale-node-js-app-using-distributed-queues/>

***编者按:**本文于 2021 年 6 月更新，加入了 Bull(相对于 kue)的信息，这是一个 Node.js 库，在 Redis 之上实现了一个排队系统。*

在[之前的一篇文章](https://blog.logrocket.com/node-js-multithreading-what-are-worker-threads-and-why-do-they-matter-48ab102f8b10/)中，我谈到了如何在 Node.js 中运行后台任务/作业(特别是使用 *worker_threads* 模块)。但是，如果达到运行 Node.js 实例的机器的极限，会发生什么呢？然后，您需要转移到更大的机器(称为垂直扩展)或水平扩展。垂直缩放总是有限制的，所以在某些时候，你需要水平缩放。

## 使用 Node.js 分布式工作队列进行伸缩

那么，你如何做到这一点呢？例如，如果您的应用程序是一个需要立即发送响应的 web 服务器，那么您需要一个类似负载平衡器的东西。相比之下，如果您的应用程序需要完成工作，但不要求立即完成，那么您可以将工作分散到“worker”节点，并使用队列进行分配。

一些用例包括生成每日报告、每日为用户重新计算事物(例如，推荐)、处理用户上传的事物(例如，大型 CSV 文件、当用户迁移到服务时导入数据、当用户登录时导入数据)。

分布式队列类似于作业描述的存储，包含足够的信息来完成作业，或者足够的信息来计算完成作业所需的所有东西。例如:

```
const jobDataSendWelcomeEmail = {
  userId: 1234,
  userName: 'John Smith',
  email: '[email protected]'
}
```

通常，主应用程序(或更复杂系统的任何部分)将作业放入队列中。在不同机器上运行的其他应用程序连接到队列并接收这些作业。这些消费者可以用收到的信息来处理工作，或者至少他们可以找出并获得他们需要的所有信息。这种简单的架构具有重要的优势:

*   您的应用程序现在分为两个逻辑部分，可以分布在不同的机器上
*   您可以从一个工作人员扩展到多个工作人员，而无需接触任何代码，也不会中断主应用程序的执行。队列负责通过网络将作业发送给工人，并且在大多数实现中，负责将相同的作业发送给工人一次

***注意**:每个厂商对于队列(主题、通道)、作业(任务、消息)、工人(消费者)都有自己的行话。*

### 为什么应该使用 Node.js 分布式工作队列

您可能会想，您可以用现有的数据库自己实现这个体系结构，而不会增加系统的复杂性。您可以创建一个包含两列的“jobs”表，一个是*“id”*主键列，另一个是包含所有作业信息的*“数据”*列。主应用程序只向表中写入数据，每隔 X 秒，工作人员从表中读取数据，以查看下一个要执行的作业。为了防止其他工作线程读取该作业，您在事务中执行该操作，同时从表中删除该作业。

瞧啊。问题解决了，对吧？嗯，首先，你是每 X 秒查询等待一次。这并不理想，但在基本用例中是可以的。更重要的是，问题是，如果工人在处理作业时崩溃，会发生什么？当作业被从表中取出时，它已经被删除了，我们无法恢复它…这个问题(以及其他问题)已经被为这个问题实现的库和服务很好地解决了，您不必重新发明轮子。

### 使用队列服务的好处

队列系统的一个优点是它们如何处理错误场景。当您收到一个作业时，该作业不会从队列中删除，而是被“锁定”或对其他工作人员不可见，直到发生以下情况之一:工作人员在工作完成后将其删除，或者您可以配置超时。因此，如果一个工作线程崩溃，就会发生超时，作业会返回队列供其他工作线程使用。当一切正常时，一旦数据得到处理，工人就删除作业。

如果问题出在工人身上，那就太好了(机器关闭了，资源用完了，等等。)，但是如果问题出在处理作业的代码上，每次队列把它发送给一个 worker，worker 就崩溃了怎么办？

那我们就陷入了失败的无限循环中，对吗？不，分布式队列通常有一个配置选项来设置最大重试次数。如果达到最大重试次数，那么根据队列，您可以配置不同的东西。一个典型的调整是将这些作业移动到“失败队列”中进行人工检查，或者为只是通知错误的工人消耗它。

分布式队列实现不仅非常适合处理这些错误，而且它们使用不同的机制将作业尽快发送给工作人员。一些实现使用套接字，另一些使用 HTTP 长轮询，还有一些可能使用其他机制。这是一个实现细节，但我想强调的是，实现它并不简单，所以您最好使用现有的和经过实战检验的实现，而不是实现您自己的实现。

### 在职务数据中放入什么

很多时候，我发现自己不知道应该在工作数据中放入什么。答案取决于您的用例，但它总是归结为两个原则:

**不要放太多。**您可以放入作业数据的数据量是有限的。有关更多信息，请检查您正在使用的排队系统。通常，它足够大，我们不会达到极限，但有时我们会忍不住放得太多。例如，如果您需要处理一个大的 CSV 文件，您不能将它放入队列中。您需要首先将其上传到存储服务，然后使用文件的 URL 和您需要的附加信息(如上传文件的用户等)创建一个作业。

**不要放太少。**如果你有不可变的数据(如`createdAt`日期)或很少改变的数据(如用户名)，你可以放入你的工作数据。这项工作应该在几秒钟或几分钟内完成，所以通常情况下，放一些可能会改变的数据是可以的，比如用户名，但是如果它没有更新到秒，这并不重要。您可以将查询保存到数据库，或者完全删除任何查询。但是，如果有影响数据处理方式的信息，您应该在作业处理器中进行查询。

### 让您的队列作业小而快

如果你需要处理大量的数据，把它们分成小块。如果您必须处理一个大的 CSV 文件，首先，将它分成一定行数的块，并为每个块创建一个作业。这样做有几个好处:

*   数据将被更快地处理，因为它可以被并行处理
*   你能更好地利用你的资源。让一个工人做较小的工作，比让一个工人做繁重的加工工作，而其他人闲置或未被充分利用要好
*   与失败的大作业相比，重试失败的小作业也更快、更高效

如果您需要来自所有这些小块的聚合结果，您可以将所有中间结果放在数据库中，当它们都完成时，您可以在另一个队列中触发一个聚合结果的新作业。这本质上是一个 map/reduce。“映射”是将一个大任务划分为较小任务的步骤，然后*“缩减”*是汇总这些较小任务的结果的步骤。

如果你不能预先划分你的数据，你仍然应该在小的工作中做处理。例如，如果您需要使用一个使用游标对结果进行分页的外部 API，那么预先计算所有游标是不切实际的。您可以为每个作业处理一页结果，一旦作业被处理，您将光标移至下一页，并使用该光标创建一个新作业，因此下一个作业将处理下一页，依此类推。

### 工人队列中延迟的作业

分布式队列的另一个有趣的特性是，您通常可以延迟作业。通常这是有限制的，所以你不能推迟两年工作，但是在一些用例中这是有用的。一些例子包括:

*   您希望向注册的用户发送欢迎电子邮件，但不是立即发送，而是稍后发送。只需创建一个发送电子邮件的延迟作业
*   当处理一个任务时，你达到了一个 API 的速率限制。您可能会被告知速率限制何时结束，以便您可以将作业放回队列中，但会延迟该特定时间
*   一般来说，如果你想在未来的某个特定时间触发某件事，比如安排备份、通知、提醒等…

### 设置作业优先级

大多数队列实现不保证作业的执行顺序，所以不要依赖于此。但是，他们通常会实现某种方式来优先处理某些工作。这在很大程度上取决于实现，所以如果需要的话，看看你正在使用的系统的文档，看看你如何能实现它。

### 分布式工人排队系统的用例

让我们看一些例子。尽管所有的排队系统都有相似的特性，但是它们没有一个通用的 API，所以我们将看到一些不同的例子。

#### 公牛图书馆

[Bull](https://github.com/OptimalBits/bull) 是一个 Node.js 库，在 Redis 之上实现了一个排队系统。Redis 是一个可以持久化的内存数据库，很多时候已经用于应用程序中的会话存储。因此，选择这个库是显而易见的。此外，即使你还没有使用 Redis，也有一些云提供商可以让你轻松地启动一个托管 Redis 服务器(例如 Heroku 或 AWS)。最后，使用 Bull 的另一个好处是，您的堆栈是 100%开源的，因此您不会陷入任何供应商锁定。

如果你需要处理大量的工作，并且你仍然想要一个开源的解决方案，那么我会选择 [RabbitMQ](https://www.rabbitmq.com/) 。在本文的例子中，我没有选择它，因为 Redis 通常更容易设置，也更常见。然而 RabbitMQ 是专门为这些用例设计的，所以从设计上来说，它在技术上更胜一筹。

Bull 作业调度有两个主要部分:生产者和消费者。生产者创建作业并将它们添加到 Redis 队列中，而消费者从队列中挑选作业并处理它们。

让我们看看如何使用 Bull 来创建和消费工作。

**创建队列并在其上放置一个作业:**

```
const bull = require('bull')
const queue = new bull(“queue_name”)
const job = await queue.add({
  foo: 'bar'
});

```

**消耗队列中的作业:**

```
const bull = require('bull');
const queue = new bull('my-queue')
queue.process(async (job, done) => { 
/*  /* 
    Sometimes, you might need to report the jobs progress, you can easily use the     job.progress() function to track the progress 
     */
  let progress = 0;

  for(i = 0; i < 80; i++){
    await doSomething(job.data);
    progress += 10;
    job.progress(progress);
  }
  // call done when finished
  done();
});

```

#### 微软 Azure 使用其服务总线

微软 Azure 提供两种队列服务。这里有一个很好的对比。我选择使用服务总线，因为它保证一个作业最多被交付给一个工人。

让我们看看如何使用服务总线创建和消费作业。

#### 创建队列并在其上放置一个作业

有了 Microsoft Azure，我们可以用`createTopicIfNotExists`方法以编程方式创建队列。一旦创建完毕，我们就可以开始发送消息了:

```
const azure = require('azure')
const serviceBusService = azure.createServiceBusService()
const jobData = { hello: 'world' }
serviceBusService.createTopicIfNotExists('queue_name', err => {
  if (err) console.log(err)
  serviceBusService.sendTopicMessage('queue_name', jobData, err => {
    if (err) console.log(err)
  })
})
```

#### 消耗工作队列中的作业

像这样的一些实现是创建订阅所必需的。查看 [Azure 文档](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions)了解关于该主题的更多信息:

```
const azure = require('azure')
const serviceBusService = azure.createServiceBusService()
serviceBusService.createSubscription('queue_name', 'subscription_name', err => {
  if (err) return console.log(err)
  // If the `isPeekLock` option is not set to true, the message will be deleted when peeked
  serviceBusService.receiveSubscriptionMessage('queue_name', 'subscription_name', { isPeekLock: true }, (err, message) => {
    if (err) return console.log(err)
    // Do something with `message` and then delete it
    serviceBusService.deleteMessage(lockedMessage, err => {
      if (err) return console.log(err)
    })
  })
})
```

#### 亚马逊，使用其 SQS 服务

亚马逊分布式队列服务被称为简单队列服务(SQS)。它可以直接使用，但也可以配置其他 AWS 服务来完成有趣的工作流。例如，您可以配置 S3 存储桶，以便在存储新文件(对象)时自动将作业发送到 SQS 队列。例如，这有助于轻松处理文件(视频、图像、CSV)。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们看看如何以编程方式在队列上添加和使用作业。

**创建队列并在其上放置一个作业:**

```
const AWS = require('aws-sdk')
AWS.config.update({region: 'REGION'})
const sqs = new AWS.SQS({ apiVersion: '2012-11-05' })
const queueParams = {
  QueueName: 'queue_name',
  Attributes: {
    'DelaySeconds': '60',
    'MessageRetentionPeriod': '86400'
  }
}
sqs.createQueue(queueParams, (err, data) => {
  if (err) return console.log(err)
  console.log('queue url', data.QueueUrl)
  const jobParams = {
    MessageBody: JSON.stringify({ hello: 'world' }),
    QueueUrl: data.QueueUrl
  }
  sqs.sendMessage(jobParams, (err, data) => {
    if (err) return console.log(err)
  })
})

```

**消耗队列中的作业:**

```
const AWS = require('aws-sdk')
AWS.config.update({region: 'REGION'})
const sqs = new AWS.SQS({apiVersion: '2012-11-05'})
const queueURL = 'SQS_QUEUE_URL' // Obtained when the queue was created
const params = {
 AttributeNames: [
  'SentTimestamp'
 ],
 MaxNumberOfMessages: 1, // receive only one message at a time
 MessageAttributeNames: [
    "All"
 ],
 QueueUrl: queueURL,
 VisibilityTimeout: 20,
 WaitTimeSeconds: 0
}
sqs.receiveMessage(params, (err, data) => {
  if (err) return console.log(err)
  if (data.Messages) {
    // Do something when the messages and then delete them
    const message = data.Messages[0]

    const deleteParams = {
      QueueUrl: queueURL,
      ReceiptHandle: message.ReceiptHandle
    }
    sqs.deleteMessage(deleteParams, function(err, data) {
      if (err) return console.log(err)
    })
  }
})
```

查看 SQS 上的 [Node.js 文档了解更多信息。](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/sqs-examples-using-queues.html)

#### 谷歌云，使用其发布/订阅服务

像 Azure 一样，Google Cloud 也需要创建订阅([参见文档](https://cloud.google.com/pubsub/docs/quickstart-client-libraries#pubsub-client-libraries-nodejs)了解更多信息)。事实上，在向主题/队列发送消息之前，您需要首先创建订阅，否则它们将不可用。

文档建议从命令行创建主题和订阅:

`gcloud pubsub topics create queue_name`

和

`gcloud pubsub subscriptions create subscription_name --topic queue_name`

然而，您也可以通过编程方式[创建它们](https://cloud.google.com/pubsub/docs/admin)，但是现在让我们看看如何插入和消费作业，假设我们已经创建了队列(主题)和订阅。

**创建队列并在其上放置一个作业:**

```
const { PubSub } = require('@google-cloud/pubsub')
const pubsub = new PubSub()
const jobData = Buffer.from(JSON.stringify({ hello: 'world' }))
const messageId = await pubsub.topic('queue_name').publish(jobData)

```

#### 消耗队列中的作业

Google Cloud Pub/Sub 保证对于每个订阅，消息/作业至少被传送一次，但是消息可以被传送多次(与往常一样，[查看文档](https://cloud.google.com/pubsub/docs/subscriber#at-least-once-delivery)了解更多信息):

```
const { PubSub } = require('@google-cloud/pubsub')
const pubsub = new PubSub()
const subscription = pubsub.subscription('subscription_name')
subscription.on('message', message => {
  const { data } = message
  // Do something and then
  message.ack() // This deletes the message from the queue
})
```

### 结论

分布式队列是扩展应用程序的好方法，原因如下:

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.

*   它们允许您将应用程序划分成逻辑块，这些逻辑块可以单独且适度地伸缩
*   他们有可靠的机制来优雅地处理错误
*   它们还提供了其他有趣的特性，比如延迟作业和优先级
*   有许多具有类似功能的服务和开源库，您可以使用它们，而不用担心被供应商锁定