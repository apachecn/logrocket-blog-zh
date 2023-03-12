# 比较最佳 Node.js 调度程序

> 原文：<https://blog.logrocket.com/comparing-best-node-js-schedulers/>

## 介绍

作业调度是指在预定时间或某些事件发生时触发作业、任务或功能的过程。大多数作业调度器都是基于 cron 的，cron 是类 Unix 系统中基于时间的作业调度器。

Node.js 运行时环境有几个作业调度程序。其中一些调度程序同时在浏览器和节点中运行，而其他调度程序仅在节点中运行。这些调度器各有其独特的特性，因此为您的用例选择最佳的调度器可能是一个令人生畏且耗时的过程。

在本文中，我将带您了解 Node.js 最流行的作业调度程序，突出它们的主要特性、差异和相似之处。

除了每个包提供的关键特性和功能之外，我还将重点介绍有用的指标和信息，比如包大小、GitHub 星级、发布数量、npm 下载统计、包许可、依赖性和维护等等。

## 关于度量的一个注记

一些度量标准，比如 npm 下载计数，可以用作指向要研究的包的初始指针，但是不应该用来决定哪个包比另一个好。根据 npm 的说法，下载统计数据是被设计成幼稚的；报告的下载数量远远超过实际的软件包安装数量。

同样，GitHub 明星与社交媒体上的赞没有任何区别。它们可以作为一个包受欢迎程度的代理指标，但是它们不能告诉你太多关于项目的质量、稳定性或成熟度的信息。

另一方面，诸如发布计数、捆绑包大小、测试覆盖率、依赖性、提交历史和包的年龄等度量标准，当放在一起看时，可以给你一些关于包的信心。然后，如果您愿意，您可以卷起袖子，深入实际的代码库，在进行尽职调查时确定代码质量。

## 比较 Node.js 调度程序

在这一节中，我们将研究节点运行时环境中最常见的作业调度程序。这些包括议程、节点时间表、节点克隆、Bree、克隆和 Bull。

### 议程

议程是一个麻省理工学院许可的节点调度程序。它在八年前首次发布，目前的版本是 4.2.1。它在 GitHub 上有 7.8k 星，在 npm 上有 62，214 周下载量。

根据 GitHub 提交图，它得到了积极的维护。考虑到发布的数量，Agenda 是一个非常成熟的包。

Agenda 提供了使用 cron 和更易于阅读的语法灵活调度作业的功能。您需要有一个工作的 MongoDB 数据库来使用 Agenda 作为作业调度器。

如果您对在您的系统中下载和安装 MongoDB 不感兴趣，您可以通过[创建 Docker 容器](https://blog.logrocket.com/node-js-docker-improve-dx/)或使用 MongoDB 的云数据库 Atlas 来体验一下这个调度程序。如果您对使用 MongoDB 不感兴趣，那么这个包可能并不适合您。

在下面的代码片段中，我定义了一个每分钟都记录到控制台的简单作业:

```
const Agenda = require("agenda");
require("dotenv").config();

const databaseUri = `mongodb+srv://mawa:${process.env.PASSWORD}@test.nq88g.mongodb.net/jobs?retryWrites=true&w=majority`;

const agenda = new Agenda({
  db: { address: databaseUri, collection: "test-jobs" },
});

agenda.define("log", async (job) => {
  const { name } = job.attrs.data;
  console.log(`Hello ${name}! ${new Date().toLocaleTimeString()}`);
});

(async function () {
  await agenda.start();
  await agenda.every("one minute", "log", { name: "John Doe" });
})();

```

议程是一个功能丰富的调度程序。它提供了基于承诺的 API、具有可配置优先级和并发性的作业调度、作业结果的持久性等等，你可以在他们的[文档](https://github.com/agenda/agenda#readme)中读到这些。

尽管 Agenda 提供了丰富的特性，但它也有多个依赖项，如下图所示。

![Agenda dependencies graph](img/71d2a5fdbefc260692129c314e3fbd6b.png)

Agenda 的发布包大小为 256KB，安装大小为 18.8MB

![Graph of Agenda's publish and install size](img/e55ed12fa0dc22e198d251164eb6cfd4.png)

### 节点时间表

这是另一个开源的、MIT 许可的包，用于在 Node 中调度作业。考虑到 npm 上的下载数量，它是第二受欢迎的节点调度程序。到目前的版本 2.0.0，已经发布了 43 个版本，这些年来，Node-schedule 有足够的时间来适应和改进。

Node-schedule 主要用于基于时间的调度，而不是基于间隔的调度，不过您也可以灵活地使用它。使用节点计划，您可以通过可选的重复配置轻松计划作业在特定日期运行。

Node-schedule 为您提供了使用 cron 风格的调度和基于日期的调度来调度作业的灵活性。使用 cron 风格的调度，您可以传递一个 cron 表达式来指定何时触发作业，以及何时使用 cron-parser 解析表达式:

```
const nodeSchedule = require('node-schedule');
const job = nodeSchedule.scheduleJob('* * * * *', function(){
    console.log('Job has been triggered at: ', new Date.toLocaleTimeString());
})

```

另一方面，使用基于日期的调度，您可以传递一个实际的 JavaScript date 对象来指定执行作业的确切日期。它还提供了一个选项来绑定当前数据以供将来使用，如下面的代码片段所示:

```
const nodeSchedule = require('node-schedule');
const birthDay = new Date(2021, 11, 21, 5, 30, 0); // Bae's birthday
let birthDayGift = 🎁;
const job = nodeSchedule.scheduleJob(birthDay, function(gift){
    console.log('Happy Birthday Babe ', new Date.toLocaleDateString());
    console.log(gift);
}.bind(null, birthDayGift));
birthDayGift = null;

```

与大多数节点调度程序一样，节点调度的一个限制是只有在脚本运行时才会触发作业。如果您希望调度一个即使在脚本不运行时也能持续的作业，请考虑使用 Cron。同样，如果您希望在重新启动之间持久化作业，这也不是一个好的选择。

Node-schedule 有三个一级依赖关系。您可以在下面的依赖关系图中看到节点计划的依赖关系。

![Node-schedule dependencies chart](img/a6af9f61610382b3451fb943b0f35860.png)

在运行完 Packagephobia 之后，Node-schedule 的发布大小为 33.2KB，安装大小为 3.15MB

![Node-schedule publish and bundle size graph](img/5c2a2c19f7414d77a35671efef6ba597.png)

### 节点克隆

Node-cron 是一个开源的、ISC 许可的包。Node-cron 于 6 年前首次发布，是另一个成熟、稳定、久经考验的节点调度程序。

它使用 GNU crontab 表达式进行作业调度。您可以安排 Node-cron 在特定时间或间隔时间触发作业:

```
const nodeCron = require('node-cron');
nodeCron.schedule('* * * * * *', () => {
  // This job will run every second
  console.log(new Date().toLocaleTimeString());
})

```

除了调度作业的`schedule`方法，Node-cron 还公开了验证 cron 表达式、启动、停止或销毁调度作业的方法。

虽然 Node-cron 可以满足您在 Node 或服务器端调度简单作业的需求，但如果您需要一个在浏览器中运行或支持工作线程的调度程序，它就没有多大用处了。

Node-cron 只有一个一级依赖关系。

![Node-cron dependency chart](img/7065750b11ae76124ea62a34e4d70699.png)

根据 Packagephobia，Node-cron 发布和安装大小分别为 65.6KB 和 5.26MB。

![Node-cron publish and bundle size chart](img/18f7d6245721a0ff82d2ba9d173c11d2.png)

### 清汤

Bree 是另一个 MIT 许可的 JavaScript 作业调度器。它既可以在节点中运行，也可以在浏览器中运行。

与上面提到的其他调度程序相比，这个包非常年轻。它大约在九个月前出版，从那时起已经出版了大约 72 个版本。目前的版本是 6.3.0，在 npm 上每周有大约 9109 次下载。

然而，尽管 Bree 是最年轻的调度程序，但它提供了比大多数其他调度程序更多的功能，并且是唯一一个同时在浏览器和节点中运行的调度程序。根据文档，Bree 使用 Node 中的 worker 线程和浏览器中的 [web workers 来生成沙盒进程。](https://blog.logrocket.com/use-cases-for-node-workers/)

它支持并发、节流、长时间运行的作业等等。查看[文档](https://jobscheduler.net/#/)以获得完整的特性列表。

下面的代码片段展示了如何使用 Bree 来安排一个简单的作业。`scrape-data.js`、`backup-database.js`和`send-email.js`是在项目目录根目录下的作业文件夹中声明的函数:

```
const Bree = require('bree');
const jobs = [
    { name: 'scrape-data', interval: 'every 1 minute'}, 
    { name: 'backup-database', timeout: 'at 12:00 am'},
    { name: 'send-email', timeout: '1m', interval: '5m'}
];
const bree = new Bree({
    jobs
});
bree.start();

```

这个包有 13 个一级依赖关系，如下面的依赖关系树所示。

![Bree dependency chart](img/6368e596f2461d8bdb3a11e68ebcfd63.png)

通过 Packagephobia 运行后，发布大小为 1.38MB，安装大小为 5.64MB。

![Bree publish and install size chart](img/9872b33a002efb27624e705384772935.png)

类似地，如果您正在考虑在客户端运行 Bree，那么在通过 Bundlephobia 运行包之后，它的缩小和压缩包大小是 64.3KB:

![Bree bundle size chart](img/991e8939a66aefdf09a9c48d768d1627.png)

### 时间单位

这个 MIT 许可的节点调度器不应该与 Node-cron 混淆——小心！在 GitHub 上，Cron 和 Node-cron 都有相同的存储库名称`node-cron`。通过在 GitHub 上搜索，你可能会认为一个是另一个的分支。

这个包在十年前第一次发布，从那以后发布了 37 个版本。它目前的版本是 1.8.2，在 npm 上每周有 909，120 次下载，是这里突出显示的节点调度程序中最高的下载次数。

您可以使用 crontab 语法`'* * * * * *'`来安排触发作业的时间。该表达式通过添加秒选项扩展了 Unix 模式。如果您没有通过秒选项，它默认为零。

它还提供了传递日期对象而不是 cron 表达式的灵活性。就功能而言，Cron 与 Node-cron 非常相似，如下所示:

```
const { CronJob } = require('cron');
const job = new CronJob('* * * * * *', function() {
  console.log('This job is triggered each second!');
});
job.start();

```

Cron 在 npm 上有一个一级依赖项和 1，516 个依赖项。Cron 的依赖项与 Node-cron 的依赖项完全相同，如下面的依赖项树所示。

![Cron dependency tree](img/caf926d64b62ab08c674d192d2657bad.png)

它的发布大小为 69.3KB，安装大小为 5.25MB。您会注意到，发布大小仅比 Node-cron 高 3.7KB。

![Cron publish and install size chart](img/63b634cf03b7e589b86497bdf0d45b8d.png)

### 公牛

Bull 是一个基于 Redis 的队列系统，用于需要运行 Redis 服务器的节点。如果您不想使用 Redis，您将不得不满足于其他调度程序。如果你对在你的系统中安装它不感兴趣，有 Redis 主机服务可以很好地与 Bull 一起工作。

Bull 自诩为节点运行时环境中最快、最可靠的基于 Redis 的队列系统。这个麻省理工学院许可的软件包在八年前发布，每周有 216，275 次下载。目前的版本是 3.29.1，说明它的成熟和稳定。

Bull 提供了基于 cron 语法的作业调度、作业速率限制、并发性、每个队列运行多个作业、重试和作业优先级等功能。如需完整的功能列表，请查看[文档](https://github.com/OptimalBits/bull#readme)。

下面的小例子将记录“Hello World！”五秒钟后使用默认 Redis 配置发送到控制台:

```
const Queue = require('bull');
const helloWorldQueue = new Queue('hello-world-queue');
const data = {
    someData: 'Hello World!'
};
const options = {
   delay: 5000,
   attempts: 3
};
helloWorldQueue.add(data, options);
helloWorldQueue.process((job) => {
   console.log(job.data.someData);
});

```

这个包有十个一级依赖项。您可以在下面的依赖关系图中检查后续级别的依赖关系。

![Bull dependecy tree](img/4eda454594f48e0a228067fe6c5fb08a.png)

该软件包发布大小为 170KB，安装大小为 9.14MB

![Bull publish and install size chart](img/8dd1fbb7b14dc00a36114ace42a6d377.png)

## 节点中的作业调度程序概述

下表总结了前面小节中强调的一些指标和信息:

| 议程 | 节点克隆 | 节点时间表 | Bree，你知道吗 | 克罗恩 | 公牛！公牛 | **安装管束尺寸** |
| --- | --- | --- | --- | --- | --- | --- |
| 18.8MB | 5.26MB | 3.15MB | 5.64MB | 5.25MB | 9.14 兆字节 | **GitHub 明星** |
| 7.8k | 2k | 8k | 1.2k | 7.1k | 11.2k | **测试覆盖率** |
| 80% | 100% | 95% | 100% | 81.3% | 94% | **版本发布** |
| 85 | 19 | 43 | 72 | 37 | 136 | **发布捆绑包大小** |
| 256KB | 65.6KB | 33.2KB | 1.38 兆字节 | 69.3KB | 170KB | **缩小+压缩包尺寸** |
| 336.8KB | 103.3KB | 31.1 千字节 | 64.3KB | 94.4KB | 193.4KB | **每周下载量** |
| 62,214 | 326,261 | 697,002 | 8,527 | 909,120 | 310,809 | **文档** |
| 好的 | 好的 | 好的 | 好的 | 好的 | 好的 | **执照** |
| 麻省理工学院(Massachu-setts Institute of Technology) | (美)工业安全委员会(Industrial Security Commission) | 用它 | 用它 | 用它 | 用它 | 我们上面看到的所有包都是免费的、开源的，并且有很好的文档记录。友好的许可(麻省理工学院或 ISC)给了你对代码库做任何事情的自由。 |

Node-cron、Node-schedule 和 cron 都相当成熟和稳定。对于简单的作业调度，您可能希望使用其中的一个。

另一方面，尽管 Agenda 和 Bull 也是成熟的调度程序，但它们分别需要 MongoDB 和 Redis。

最后，以上所有调度中最年轻的是 Bree。尽管 Bree 很老，但它功能丰富。它在节点和浏览器中运行。

结论

## 在节点包生态系统中有几个作业调度程序可供选择。大多数包都具有满足您的基本作业调度需求的功能，您对调度程序的选择很大程度上取决于您的用例。

如果您打算在服务器端进行简单的作业调度，您可能希望探索 Cron、Node-cron 和 Node-schedule，因为它们简单、成熟、流行、稳定，并且具有友好的许可。对于简单的工作，我建议使用这三个中的一个。

Agenda 和 Bull 也提供了简单的作业调度，但是增加了额外的特性和对数据库持久性的支持。除非您对使用它们提供的附加特性感兴趣，否则您不会希望在您的应用程序中包含所有这些包和依赖项。

另一方面，如果您正在寻找一个具有扩展功能的调度程序，例如支持工作线程或能够在浏览器和节点中运行(除了简单的作业调度之外)，那么 Bree 可能是您的最佳选择。

我错过了什么吗？请在下面的评论区告诉我。

200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

## 部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 检测您的应用程序以记录基线性能计时，例如页面加载时间、第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

.

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.