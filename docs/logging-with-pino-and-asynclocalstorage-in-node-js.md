# Node.js 中使用 Pino 和 AsyncLocalStorage 进行日志记录

> 原文：<https://blog.logrocket.com/logging-with-pino-and-asynclocalstorage-in-node-js/>

花上几个小时，甚至几天，试图修复一个不明显的错误是令人沮丧和徒劳的。最终，你会盯着屏幕等待灵光乍现。

但是，如果不是等待解决方案神奇地来到你面前，而是你有系统地跟踪你正在处理的任何边缘情况错误的超级能力，会怎么样？

你可以——通过记录。正确使用时，日志记录可以让您对应用程序有必要的了解，这样您就可以确切地知道发生了什么。正确的日志记录可能是糟糕的调试语句转储和强大的调试工具之间的区别，后者可以帮助您更容易地找到错误并更快地修复它们。

## 什么是日志库，为什么要使用它？

在日志记录中，重要的是要有一个既容易被人阅读又能被机器解析的输出。对于我们开发人员来说，重要的是当我们查看和检查日志时，我们能够理解它。机器需要能够解析日志，以便我们可以运行高级查询和执行奇特的聚合。

JSON 是一种符合这两个标准的格式，这就是为什么日志库会将输出解析为有效的 JSON，并确保日志的格式总是正确的。

在爱好项目中使用`console.log`可能会有所收获。然而，在[生产级 Node.js 应用](https://maximorlov.com/start-node-js-in-production/)中，能够区分不同的日志级别通常是有用的。

进入日志库，它也允许您在不同的级别打开和关闭日志。在生产环境中，您通常希望打开错误和警告，但是在登台环境中，调试/更详细的日志也是有用的，否则会在生产中增加太多噪音。

## 皮诺是什么？

[Pino](https://github.com/pinojs/pino) 是 Node.js 生态系统中一个流行的日志库。它的速度[快](https://github.com/pinojs/pino/blob/master/docs/benchmarks.md)，开销最小。

## 使用 Pino 的先决条件

*   [Node.js](https://nodejs.org/en/) 版本> =12.17 或者> =13.10
*   [NPM](https://www.npmjs.com/) (通常包含在 Node.js 中)
*   您希望添加日志记录的 [Express](https://expressjs.com/) web 服务器

## 在 Node.js 应用程序中使用 Pino

要在 Node.js 项目中安装 Pino，请运行:

```
npm install pino
```

创建一个名为`logger.js`的文件。在这个文件中，我们将导入 Pino，对其进行配置，然后导出一个在整个项目中使用的日志记录实例:

```
// logger.js
const pino = require('pino');

// Create a logging instance
const logger = pino({
level: process.env.NODE_ENV === 'production' ? 'info' : 'debug',
});

module.exports.logger = logger;
```

使用 Pino 相对简单。您可以在不同的日志级别记录消息(调试、信息、警告、错误等)。)使用类似命名的方法。您还可以为附加上下文传入对象和/或错误:

```
const { logger } = require('./logger.js');

// Log a simple message at the "info" level
logger.info('Application started!');

// Outputs:
// {"level":30,"time":1608568334356,"pid":67017,"hostname":"Maxims-MacBook-Pro.local","msg":"Application started!"}

// Log an object in addition to a message to supply context
const user = { firstName: 'Maxim', lastName: 'Orlov' };
logger.info(user, 'User successfully authenticated');

// Outputs:
// {"level":30,"time":1608568334356,"pid":67017,"hostname":"Maxims-MacBook-Pro.local","firstName":"Maxim","lastName":"Orlov","msg":"User successfully authenticated"}

// Log an error at the "error" level
const error = new Error('Database crashed!');
logger.error(error, 'Failed to fetch user');

// Outputs:
// {"level":50,"time":1608568334356,"pid":67017,"hostname":"Maxims-MacBook-Pro.local","stack":"Error: Database crashed!\n    at Object.<anonymous> (/Users/maxim/Code/playground/test.js:19:15)\n    at Module._compile (node:internal/modules/cjs/loader:1102:14)\n    at Object.Module._extensions..js (node:internal/modules/cjs/loader:1131:10)\n    at Module.load (node:internal/modules/cjs/loader:967:32)\n    at Function.Module._load (node:internal/modules/cjs/loader:807:14)\n    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:76:12)\n    at node:internal/main/run_main_module:17:47","type":"Error","msg":"Failed to fetch user"}

```

要将 Pino 与现有的 Node.js 应用程序集成，只需导入 logger 实例，并在整个项目中使用它。如果您的项目正在使用`console.log`，您可以在项目范围内使用`logger.info`进行查找和替换。只需确保在文件的顶部导入 logger 实例。

此外，您可以花一些时间将日志分类到不同的日志级别。这将允许您区分不同严重性级别的日志消息，并且您可以针对特定环境使低于特定日志级别的日志静默，例如生产中的调试日志。

作为最低要求，我在项目中使用“信息”和“错误”日志级别，这样我可以很容易地将错误日志与正常操作日志区分开来。

下面是一个运行 Express web 服务器的 Node.js 应用程序示例，该服务器具有一个获取用户的端点:

```
// server.js
const express = require('express');
const { logger } = require('./logger.js');
const db = require('./db.js');

const PORT = process.env.PORT || 3000;

const app = express();

app.get('/users/:id', async (req, res) => {
  let userId = req.params.id;

  if (isNaN(userId)) {
    logger.warn({ userId }, 'Invalid user ID');
    return res.status(400).send('Invalid user ID');
  } else {
    userId = Number(userId);
  }

  try {
    logger.info({ userId }, 'Fetching user from DB');
    const user = await db.getUser({ userId });

    if (!user) {
      logger.warn({ userId }, 'User not found');
      return res.status(404).send('User not found');
    }

    logger.debug({ user }, 'User found, sending to client');
    return res.status(200).json(user);
  } catch (error) {
    logger.error(error, 'Failed to fetch user from DB');
    return res.status(500).send('An error occurred while fetching user');
  }
});

app.listen(PORT, () => {
  logger.info(`Server listening at http://localhost:${PORT}`);
});

```

请注意本例中使用的不同日志级别(调试、信息、警告和错误)。当发生既不是正常操作也不是错误的事情时，我倾向于以“警告”的严重程度记录日志，所以介于两者之间。

Pino 中默认禁用调试日志，因此我倾向于将它们用于会在生产中增加太多噪音但在调试期间可能对登台或本地开发有用的日志。

## 什么是 AsyncLocalStorage，它是如何工作的？

Node.js 是单线程语言，因此它使用事件循环来处理同步异步任务。虽然这使得 Node.js 在处理 web 请求时非常快，但缺点是函数堆栈和上下文在这个过程中丢失了。

[AsyncLocalStorage 类](https://nodejs.org/api/async_hooks.html#async_hooks_class_asynclocalstorage)是`async_hooks`模块的一部分。这是一个相对较新的 Node.js API，允许您在回调函数和异步操作中存储数据。

要使用它，您需要创建一个新的类实例，并通过传递两个参数来调用`run`方法:存储和回调函数。

存储可以是任何东西，从简单的整数或字符串到复杂的对象或映射。作为第二个参数传入的回调函数将在存储的上下文中执行。要访问存储，调用实例上的`getStore`方法。

让我们看看它是如何工作的:

```
const { AsyncLocalStorage } = require('async_hooks');

// Create a new context
const context = new AsyncLocalStorage();

function doSomethingAsync() {
  // Use setImmediate to mimic async execution
  setImmediate(() => {
    const store = context.getStore();
    store.get('name'); // Maxim
  });
}

function main() {
  const store = new Map();
  store.set('name', 'Maxim');

  // Run the callback function (second argument) inside a context with `store` as data
  context.run(store, () => {
    doSomethingAsync();
  });

  // This is outside of the context
    context.getStore(); // undefined
}

main();

```

请记住，该存储只能从回调函数及其任何子函数中访问。在外面的任何地方，`getStore`方法将返回`undefined`。

这个例子只使用了一个嵌套层次，而且两个函数都在同一个文件中，所以好处可能不是很明显。然而，在一个较大的项目中，函数堆栈有几层之深，并且分布在不同的文件中，您可以想象从任何地方访问线程本地存储的有用性。

## 将日志与特定请求相关联

在堆栈级别存储和检索数据有几个用例。一个用例是将日志与 web 请求相关联。

当面对生产应用程序中的错误日志时，能够看到属于同一个请求/响应周期的所有其他日志是非常有帮助的。这使您能够在请求通过应用程序的过程中对其进行跟踪，因此您可以收集导致特定 bug 的条件和变量。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

为了做到这一点，我们需要为每个请求分配某种唯一的标识符。一个[通用唯一 ID (UUID)](https://en.wikipedia.org/wiki/Universally_unique_identifier) 就是我们要找的，而`[uuid](https://www.npmjs.com/package/uuid)` [库](https://www.npmjs.com/package/uuid)正好给了我们这个。更具体地说，我们将为每个请求生成版本 4 的 UUIDs。

让我们扩展上面的示例 Node.js 应用程序。首先，我们将创建一个导出 AsyncLocalStorage 实例的模块:

```
// async-context.js
const { AsyncLocalStorage } = require('async_hooks');

const context = new AsyncLocalStorage();

module.exports = context;

```

接下来，我们将使用一个 Express 中间件函数来扩展`logger.js`文件，该函数创建一个具有唯一请求 ID 的子记录器，并将其添加到上下文存储中:

```
// logger.js
const pino = require('pino');
const uuid = require('uuid');
const context = require('./async-context.js');

// Create a logging instance
const logger = pino({
  level: process.env.NODE_ENV === 'production' ? 'info' : 'debug',
});

// Proxify logger instance to use child logger from context if it exists
module.exports.logger = new Proxy(logger, {
  get(target, property, receiver) {
    target = context.getStore()?.get('logger') || target;
    return Reflect.get(target, property, receiver);
  },
});

// Generate a unique ID for each incoming request and store a child logger in context
// to always log the request ID
module.exports.contextMiddleware = (req, res, next) => {
  const child = logger.child({ requestId: uuid.v4() });
  const store = new Map();
  store.set('logger', child);

  return context.run(store, next);
};

```

> 子记录器是向记录器添加状态的一种方式。使用子记录器时，您创建的子记录器的属性会输出到每个日志行。子记录器是向日志添加请求 ID 的一个很好的用例。

此外，我们还使用一个[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)来修改日志实例，以便使用子日志记录器实例(如果存在的话)进行日志记录。

最后，让我们使用`server.js`中的中间件功能:

```
// server.js
const express = require('express');
const { logger, contextMiddleware } = require('./logger.js');
const db = require('./db.js');

const PORT = process.env.PORT || 3000;

const app = express();

// Attach a unique request ID to every log line
app.use(contextMiddleware);

app.get('/users/:id', async (req, res) => {
  let userId = req.params.id;

  if (isNaN(userId)) {
    logger.warn({ userId }, 'Invalid user ID');
    return res.status(400).send('Invalid user ID');
  } else {
    userId = Number(userId);
  }

  try {
    logger.info({ userId }, 'Fetching user from DB');
    const user = await db.getUser({ userId });

    if (!user) {
      logger.warn({ userId }, 'User not found');
      return res.status(404).send('User not found');
    }

    logger.debug({ user }, 'User found, sending to client');
    return res.status(200).json(user);
  } catch (error) {
    logger.error(error, 'Failed to fetch user from DB');
    return res.status(500).send('An error occurred while fetching user');
  }
});

app.listen(PORT, () => {
  logger.info(`Server listening at http://localhost:${PORT}`);
});

```

就是这样！现在，我们为每个日志行附加了一个唯一的请求 ID。

```
{...,"requestId":"da672623-818b-4b18-89ca-7eb073accbfe","userId":1,"msg":"Fetching user from DB"}
{...,"requestId":"da672623-818b-4b18-89ca-7eb073accbfe","user":{...},"msg":"User found, sending to client"}
{...,"requestId":"01107c17-d3c8-4e20-b1ed-165e279a9f75","userId":2,"msg":"Fetching user from DB"}
{...,"requestId":"01107c17-d3c8-4e20-b1ed-165e279a9f75","user":{...},"msg":"User found, sending to client"}

```

## 结论

你可以在 Github 的这个资源库中找到一个完整的工作示例[。](https://github.com/Maximization/contextual-logging-nodejs)

除了请求 ID 之外，您可能会发现在每个日志中包含其他数据也很有用，比如用户 ID 和/或电子邮件。这样做的一个好地方是在请求被认证之后的认证中间件中。

现在，您已经做好了一切准备，可以跟踪您将遇到的每一个模糊错误。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.