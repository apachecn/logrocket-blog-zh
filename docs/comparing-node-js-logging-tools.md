# 比较 Node.js 日志工具

> 原文：<https://blog.logrocket.com/comparing-node-js-logging-tools/>

日志是软件工程中的一个重要概念。通过写出应用程序中发生的事件的审计，日志为开发人员提供了理解应用程序代码实际做什么的洞察力。

在本文中，我们将比较 Node.js 的不同日志工具，包括:

但是在我们进行比较之前，让我们深入了解一下日志记录的元素。

## 我们为什么要记录？

在任何应用程序中使用日志时，了解日志记录级别非常重要。日志记录级别是一种根据紧急程度和显示方式对日志文件中的条目进行分类的方式。

每个定义的日志消息都有一个相关联的日志级别，该日志级别为消息的重要性和紧急程度提供了粗略的指导。Nodejs 中最常见的日志记录级别有:

*   `ERROR`
*   `INFO`
*   `DEBUG`
*   `WARN`
*   `FATAL`

每个日志级别都有自己的用例以及如何应用它们。

### `ERROR`日志级别

`ERROR`级表示必须处理的严重问题。它指定可能仍然允许应用程序继续运行的错误事件。

### `INFO`日志级别

此日志级别跟踪已发生的事件。假设系统的其余部分继续正常运行，通常可以忽略这些警报。

它基本上指示了信息性消息，这些消息在粒度级别上突出了应用程序的进度。

### `DEBUG`日志级别

`DEBUG`日志级别包含的信息只在调试阶段有用，在生产过程中可能没有什么价值。它们基本上是在调试应用程序时最有用的信息事件。

### `WARN`日志级别

`WARN`日志级别比错误条件稍微不那么严重，因为它们指示潜在的有害情况。该消息表明应用程序中发生了意外事件，可能会中断或延迟其他进程。

### `FATAL`日志级别

此日志级别表示可能导致应用程序中止的非常严重的错误事件。

## Node.js 日志库

现在我们已经了解了不同的日志级别，我们可以深入了解 Node.js 中不同的日志工具，以及如何在我们的应用程序中使用它们。

### 班扬

[班扬是](https://www.npmjs.com/package/bunyan)节点[非常流行的测井工具。](https://www.npmjs.com/package/bunyan) [js](https://www.npmjs.com/package/bunyan) 。这是一个用于 Node.js 服务的简单快速的 JSON 日志库，提供了一个漂亮的日志 CLI 视图，用不同的颜色描述不同的日志级别。

![Bunyan Interface Showing Different Coloring For Code](img/7243b849170949a82ff0b716f7a23443.png)

#### 安装 Bunyan

要安装 Bunyan，请将以下内容添加到您的终端:

```
npm i bunyan

```

#### 使用班扬语

要使用 Bunyan 开始日志记录，创建一个`test.js`文件并添加以下代码用于测试目的:

```
const bunyan = require('bunyan');

```

在需要这个包之后，我们必须使用`createLogger`方法定义一个日志记录器的实例:

```
var log = bunyan.createLogger({
  name: '<name of application',
  stream: process.stdout
});

```

然后我们可以使用 Bunyan 来记录数据:

```
log.info('hi');

```

如果我们运行我们的`test.js`文件，我们会在控制台中得到这个输出:

```
{"name":"myapp","hostname":"banana.local","pid":40161,"level":30,"msg":"hi","time":"2013-01-04T18:46:23.851Z","v":0}

```

这里很明显，Bunyan 主张日志应该是 JSON 格式的，并且每个日志都带有日志创建的日期。Node.js 中的一个常见做法是将这些日志实体存储在一个文件中以供参考。

#### 班扬的好处

除了 Node.js 之外，Bunyan 还支持多种运行时，如 [Webpack](https://blog.logrocket.com/changes-coming-to-webpack-in-2021/) ，Browserify 和 NW.js

Bunyan 还有序列化的概念，即函数从 JavaScript 对象产生一个 JSON-able 对象。这里，一个特定的 logger 实例可以有一个`serializer`，它将日志记录字段名称映射到一个序列化函数。

使用子日志记录，开发人员可以为应用程序的子组件专门设计一个日志记录器，比如创建一个新的日志记录器，在其日志记录中包含附加的绑定字段。

### 温斯顿

Winston 是一个顶级的 Node.js 日志库[，由于其庞大的社区和功能，在撰写本文时，GitHub](https://github.com/winstonjs/winston) 上有超过 17k 颗星。

Winston 分离了日志记录的各个部分，通过大量的配置使其具有可扩展性和灵活性，从而实现无缝开发。

#### 安装 Winston

要安装 Bunyan，请将以下内容添加到您的终端:

```
npm i winston

```

#### 利用温斯顿

安装库之后，我们必须在我们的根文件中需要该库，并创建它的一个实例:

```
const winston = require('winston');
const logger = winston.createLogger({})

```

`createLogger`方法可以保存很多配置，比如日志级别、格式和元描述:

```
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  defaultMeta: { service: '<service>' },
});

```

此外，我们可以通过将文件包含在`transport`数组中来指定一个文件，所有日志都将写入该文件:

```
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  defaultMeta: { service: 'user-service' },
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' }),
  ],
});

```

在这里，我们设置所有具有错误日志级别的日志必须写入`error.log`文件，而所有其他日志必须写入`combined.log`文件。

我们还可以选择在控制台中仅显示我们的日志，方法是在`transport`数组中使用以下配置:

```
new winston.transports.Console()

```

要获得 Winston 提供的所有方法，[您可以参考文档](https://www.npmjs.com/package/winston)。

#### 温斯顿福利

Winston 能够将日志发送到其他云日志服务，如 [logz.io](http://logz.io) 和 [AWS Cloudwatch](https://www.npmjs.com/package/winston-cloudwatch) ，而不是将日志存储在静态文件中或记录在控制台上。

因为每个日志都有时间戳和日志级别，所以可以根据任何事件轻松跟踪日志。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 皮诺

Pino 标榜自己是一个“开销非常低”的 Node.js 日志记录器，因为它使用最少的资源进行日志记录。使用 Pino，记录的消息会随着时间的推移而增加，从而对应用程序产生节流效应，例如减少每秒请求数。

节流是一种方法，无论用户触发事件多少次，附属函数在给定的时间间隔内只执行一次。

#### 安装 Pino

要安装 Pino，请将以下内容添加到您的终端:

```
npm i pino

```

#### 使用 Pino

使用这个库非常简单明了。您所要做的就是请求库并初始化它:

```
const logger = require('pino')()

logger.info('hello world')

```

运行此脚本会在控制台上产生以下内容:

```
{"level":30,"time":1531171074631,"msg":"hello world","pid":657,"hostname":"Davids-MBP-3.fritz.box"}

```

控制台上记录的数据包括日志级别、记录数据的时间、日志的实际消息、日志的`id`和主机。

#### 在 Express.js 中使用 Pino

您也可以通过安装此软件包在 Express.js 应用程序中使用 Pino:

```
npm install pino-http

```

安装这个库之后，您可以在您的 Express.js 应用程序中使用它，如下所示:

```
const express = require('express')
const pino = require('pino-http')

const app = express();
const pinoInstance = pino()

app.use(pinoInstance);

app.post('/do-stuff', (req,res) => {
  req.log.info('Something done');
  res.send('Say hello to Pino')
})

app.listen(5500)

```

#### 皮诺的好处

Pino 有一个模块提供了一个名为`pino-pretty`的基本 NDJSON 格式化程序。

换行符分隔的 JSON(或 NDJSON)是一种存储或流式传输结构化数据的方便格式，一次可以处理一条记录。考虑到日志级别和时间戳等因素，所有记录的数据都会应用额外的格式。

您可以将`pino-pretty`安装在您的项目上，或者全局安装在您的本地机器上。

要安装`pino-pretty`，请运行以下命令:

```
npm install -g pino-pretty

```

安装后，您可以使用以下命令运行应用程序:

```
node app.js | pino-pretty

```

### 摩根

Morgan 是一个 Node.js 库，用于记录 HTTP 请求。它通常作为中间件添加，这样它可以跟踪所有的请求。与其他日志记录工具不同，它的主要功能是记录 HTTP 请求。

#### 安装摩根

要安装 Morgan，请将以下内容添加到您的终端:

```
npm i morgan

```

#### 利用摩根

安装后，您必须需要该库，然后将其作为 Express.js 中间件添加:

```
var morgan = require('morgan')

app.use(morgan('dev'))

```

传递的`dev`是摩根格式。Morgan 实现了五种日志格式:

1.  `combined`，它使用标准的 Apache 组合日志输出
2.  `common`，它使用标准的 Apache 组合日志输出
3.  `dev`，使用由响应状态着色的简明输出，供开发使用
4.  `short`，默认包含响应时间并缩短日志
5.  `tiny`，使用最小输出

然后，您可以使用如下所示的格式:

```
app.use(morgan('combined'))
app.use(morgan('common'))
app.use(morgan('dev'))
app.use(morgan('short'))
app.use(morgan('tiny'))

```

#### 摩根福利

无需编写额外的配置代码，Morgan 使您能够根据您的需求选择任何预定义的格式，从而节省您的时间。

### npmlog

这是 npm 使用的官方记录器实用程序。就像其他 Node.js 日志库一样，它支持自定义级别、彩色输出，并允许您为不同的日志级别设置不同的颜色。

#### 正在安装 npmlog

要安装 Npmlog，请将以下内容添加到您的终端:

```
npm i npmlog

```

#### 使用 npmlog

要开始使用这个库，创建一个测试文件，然后需要这个包:

```
const log = require('npmlog');
log.info('Wisdom Ekpot', 'Hello from logrocket', {'message': 'test'})

```

`log.info`在控制台或文件上记录数据，它最多可以接受三个参数:

*   第一个参数:日志的前缀
*   第二个参数:实际的日志消息
*   第三个参数:日志的附加数据

#### npmlog 优势

就像其他日志记录工具一样，npmlog 附带了许多简化开发的方法，比如设置日志标题、标题样式和定义日志级别。

## Node.js 日志库的统计信息

下面是我们在本文中介绍的 Node.js 日志库的快速比较；在发表本文时，所有数据都是准确的。

#### 图书馆

| 每周下载次数 | Github stars | Github forks | 班扬 |
| --- | --- | --- | --- |
| 1,568,274 | 6.7k | 522 | 温斯顿 |
| 6,364,282 | 17.8k | 1.6k | 皮诺 |
| 1,836,807 | 7.8k | 530 | 摩根 |
| 2 873 389 | 六点七 k | 485 | npmlog 公司 |
| 13,220,573 | 358 | 55 | 结论 |

## 浏览所有这些 Node.js 日志记录工具，它表明在我们的任何 Node.js 项目中实现日志记录都非常简单和直接。通常建议使用适合您的应用程序目的并显示实际所需数据的库。

200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

## 部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

.

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.