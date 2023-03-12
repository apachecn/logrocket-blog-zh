# 使用 Agenda.js - LogRocket 博客在 Node.js 中进行作业调度

> 原文：<https://blog.logrocket.com/job-scheduling-node-js-agenda-js/>

通常在开发一个应用程序时，需要执行一个重复的任务或者提醒用户一个特定的事件。这可以从每月一次向用户发送服务的计费信息到执行数据库备份。它甚至可以简单到给用户发一封电子邮件来提醒他们新的优惠和促销。

一种简单的方法是使用 JavaScript 中的内置方法，即`setTimeout`和`setInterval`。然而，这不能水平扩展，因为没有办法跟踪已经完成或中止的作业，因此需要一个作业调度器。

在本教程中，我们将向您展示如何使用 Agenda.js 在 Node.js 中进行作业调度。

## 为什么选择 Agenda.js？

当[在 Node.js](https://blog.logrocket.com/comparing-best-node-js-schedulers/) 中选择一个调度程序时，有许多选项可以考虑，从已经建立的 [node-cron](https://blog.logrocket.com/task-scheduling-or-cron-jobs-in-node-using-node-cron/) 到更新更现代的解决方案，如 [Bull](https://github.com/OptimalBits/bull) 、 [Bee-Queue](https://github.com/bee-queue/bee-queue) 和 [Agenda](https://github.com/agenda/agenda) 。

是什么让 Agenda.js 与众不同？Agenda 使用 MongoDB 实现持久性，与大多数调度器使用的 Redis 选项相比，它提供了更少配置的优势。

在服务器崩溃或重启的情况下，Redis 选项不会像预期的那样运行，并且需要对数据库进行一些特殊的配置。

就其特性集而言，Agenda.js 既轻便又健壮。下面是来自[官方文档](https://github.com/agenda/agenda)的图表，显示 Agenda.js 附带了一些主流的现代调度程序。

![Agenda.js Feature Set Diagram](img/a42689d7923a3e22dfb62de8b4a7581a.png)

关于 Agenda.js 如何与其他著名的调度程序进行比较的详尽列表，请查看本指南。

## 设置 Agenda.js

以下示例显示了如何设置 Agenda.js:

```
npm install agenda

// then we can go ahead to require and use it 
const Agenda = require("agenda");
const agenda = new Agenda({ db: { address: "mongodb://127.0.0.1/agenda" } });

agenda.define("send monthly billing report", async (job) => {
  // some code that aggregates the database and send monthly billing report.
});

(async function () {
  // IIFE to give access to async/await
  await agenda.start();

  await agenda.every("1 month", "send monthly billing report");
})();

```

使用 Agenda.js 时，您可能会经常使用以下方法:

*   `. agenda.every`以指定的时间间隔重复一项任务，例如每月、每周、每天等。
*   `. agenda.schedule`安排任务在特定时间运行一次
*   `. agenda.now`安排任务立即运行

要获得参与该库的所有可能方式的完整列表，请查看这个官方[文档](https://www.npmjs.com/package/agenda)。

在本教程中，我将向您展示如何在 Express 应用程序中设置 Agenda.js。

在接下来的部分中，我将演示如何构建现有的 Express 代码库来使用 Agenda.js。

## 初始化 Agenda.js 的单一实例

首先，初始化 Agenda.js 并创建一个将在整个应用程序中使用的 singleton。

**:**

```
const Agenda = require(‘agenda’);
const env = process.env.NODE_ENV || "development";
const config = require(__dirname + "/../config/config.js")[env];
const { allDefinitions } = require("./definitions");

// establised a connection to our mongoDB database.
const agenda = new Agenda({
db: { 
    address: config.database, 
    collection: ‘agendaJobs’, 
    options: { useUnifiedTopology: true }, 
    },
    processEvery: "1 minute",
    maxConcurrency: 20,
});

// listen for the ready or error event.
agenda
 .on(‘ready’, () => console.log("Agenda started!"))
 .on(‘error’, () => console.log("Agenda connection error!"));

// define all agenda jobs
allDefinitions(agenda);

// logs all registered jobs 
console.log({ jobs: agenda._definitions });

module.exports = agenda;

```

## 定义

所有定义都通过闭包与 Agenda.js 的初始化实例一起提供。

**:**

```
const { mailDefinitions } =  require("./mails");
const { payoutDefinitions } = require("./payout");

const definitions = [mailDefinitions, payoutDefinitions];

 const allDefinitions = (agenda) => {
  definitions.forEach((definition) => definition(agenda));
};

module.exports = { allDefinitions }

```

然后，每个单独的定义是这样定义的。

### 邮件定义

下面，我们将所有的邮件定义及其相关的处理程序进行分组。

**:**

```
const { JobHandlers } =  require("../handlers");

 const mailDefinitions = (agenda) => {
   agenda.define("send-welcome-mail",JobHandlers.sendWelcomeEmail);

   agenda.define("reset-otp-mail",JobHandlers.sendOTPEmail);

   agenda.define(
      "billings-info",
      {
        priority: "high",
        concurrency: 20,
      },
      JobHandlers.monthlyBillingInformation
    );
};

module.exports = { mailDefinitions }

```

这里，`JobHandlers`是执行指定任务的函数。

`jobs/definitions/payout.js`文件也有类似的内容。

### `JobHandlers`

`JobHandlers`文件包含执行所需任务的实际函数定义。

**:**

```
//NB this imports are relative to where you have this funtions defined in your own projects
const PaymentService = require("../relative-to-your-project");
const mailService = require("../relative-to-your-project");

 const JobHandlers = {
  completePayout: async (job, done) => {
    const { data } = job.attrs;
    await PaymentService.completePayout(data);
    done();
  },
 sendWelcomeEmail: async (job, done) => {
      const { data } = job.attrs;
      await mailService.welcome(data);
      done();
    },

// .... more methods that perform diffrent tasks
};

module.exports = { JobHandlers }

```

既然我们已经有了大部分样板文件，我们可以在我们的控制器中使用 Agenda.js 的单例实例。

我们也可以用另一个模块来包装——我更喜欢后一种方法，因为它有助于让事情更有条理、更独立。

## 调度程序

**:**

```
import { agenda } from "./index";

const schedule = {
  completePayout: async (data) => {
    await agenda.schedule("in 1 minute", "complete-payout", data);
  },
  sendWelcomeMail: async (data) => {
    await agenda.now("send-welcome-mail", data);
  },

// .... more methods that shedule tasks at the different intervals.
}

module.exports = { schedule }

```

最后，我们可以在我们的控制器或路由中利用这一点，这取决于我们如何构建我们的应用程序。

## 控制器

**:**

```
//import the schdule file 
const { schedule } = require("../schedule"); 

app.post("/signup", (req, res, next) => {
  const user = new User(req.body);
  user.save((err) => {
    if (err) {
      return next(err);
    }
     //use the schedule module
     await schedule.sendWelcomeMail(user);

     return res.status(201).send({
      success: true,
      message: "user successfully created",
      token,
    });
  });
});

// use other schedule methods for other routes 

```

虽然上面的例子展示了在我们的应用程序中构造 Agenda.js 的一种中立方式，但它确实与[单一责任原则(SRP)](https://blog.logrocket.com/solid-principles-single-responsibility-in-javascript-frameworks/) 相冲突。

因为我们经常需要做的不仅仅是在用户注册时发送一封欢迎电子邮件，我们可能希望运行分析或一些第三方集成。一个简单的注册用户的控制器可能最终需要 1000 行代码。

为了防止这种情况，我们将使用一个事件发射器:

```
npm i eventemitter3

```

然后，在我们的 Express 应用程序中，我们将在整个应用程序中提供一个 singleton。

```
var EventEmitter = require('eventemitter3');

const myEmitter = new EventEmitter();

// where app is an instance of our express application.
//this should be done before all routes confuguration.

app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.set("myEmitter", myEmitter); // this makes my emitter available across all our controllers.

.....

module.exports = { myEmitter }

```

设置好之后，我们可以从上面编辑我们的注册路径，以发送一个我们可以响应的注册事件。

```
//import the schedule file 
const { schedule } = require("../schedule"); 

app.post("/signup", (req, res, next) => {
 const myEmitter = req.app.get("myEmitter");

  const user = new User(req.body);
  user.save((err) => {
    if (err) {
      return next(err);
    }

    //an emit event to handle registration.
     myEmitter.emit('register', user );

     return res.status(201).send({
      success: true,
      message: "user successfully created",
      token,
    });
  });
});

```

然后，我们可以通过在整个 Express 应用程序中导入相同的`myEmitter`集合实例来对我们的`register`事件做出反应。

```
const myEmitter = require("../relative-from-our-express-application");
const { schedule } = require("../schedule"); 

// then we can do perfom different actions like this
myEmitter.on('register', data => {
 await schedule.sendWelcomeMail(data);
 // run third party analytics 
 // send a sequence of mails
// ..do more stuff
})

```

## 结论

在本教程中，我们展示了 Agenda.js 如何帮助我们以结构化的方式处理 cron 作业。然而，有些用例超出了本文的范围。你可以参考 [Agenda.js](https://www.npmjs.com/package/agenda) 和 [eventemitter](https://nodejs.org/api/events.html) 库的文档来查看广泛的指南。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.