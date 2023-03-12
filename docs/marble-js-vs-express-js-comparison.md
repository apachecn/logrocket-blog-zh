# Marble.js 与 Express.js:比较 Node.js web 框架

> 原文：<https://blog.logrocket.com/marble-js-vs-express-js-comparison/>

在本文中，我们将比较 Express.js 和 Marble.js，express . js 是使用 Node.js 构建 web 应用程序最常用的服务器端框架之一，marble . js 是另一个更新的反应式服务器端 Node.js 框架。

## 先决条件

如果你不熟悉[功能反应式编程(FRP)](https://en.wikipedia.org/wiki/Functional_reactive_programming) ，你可能想在阅读之前暂停一下。我推荐[康纳尔·埃利奥特的作品](https://github.com/conal/essence-and-origins-of-frp)，其中包含了更多精确的信息。

FRP 的基本要点是，它是描述动态(也称为时变)信息和顺序执行的连续副作用的通用范例。Conal Elliott 用这句话抓住了 FRP 的含义:

> *FRP 是并发的，不会与困扰强制并发的理论和实用的老鼠仓发生冲突*。

Cycle.js 框架的作者和 RxJS 的核心贡献者 Andre Staltz 也给出了这个精彩的解释:

> 反应式编程提高了代码的抽象层次，因此人们可以专注于定义业务逻辑的事件的相互依赖性，而不必经常摆弄大量的实现细节。

这允许显式执行我们的程序。一旦在一个新线程上开始执行一个进程，它应该能够被控制。

现在，让我们继续我们的比较。

## Marble.js 与 Express.js:基本比较

### 什么是 Marble.js？

[Marble.js](https://github.com/marblejs/marble) 是一个功能性的 reactive Node.js 框架，用于构建服务器端应用。它基于 TypeScript 和 RxJS，依赖于反应范式，在节点平台之上有功能糖。

Marble.js 的主要构件是[一个效果](https://docs.marblejs.com/http/effects)，或者一个返回事件流的函数。

由于 Marble.js 基于事件的特性，当一个服务执行其他服务可能感兴趣的动作时，该服务会产生一个事件，该事件是所执行动作的记录。其他服务使用这些事件来执行事件所需的操作。

Effect 适用于实现分布式事件处理系统，因此，除了在基本 HTTP 协议上操作之外，Marble.js 还可用于通用消息驱动架构(MDA)解决方案，如 WebSockets 或微服务。

### 什么是 Express.js？

Express.js 是一个快速、灵活、极简的 Node.js web 开发框架，可以设计单页面、多页面和混合的应用程序，具有一系列健壮的特性。

Express 提供了许多中间件和其他内置的支持来简化 API 的创建，并支持第三方模块与数据库接口。这些优点使您可以在 Express 中使用几乎任何形式的数据库。

对于 Marble v3 和 Express v4 的高级比较，我们来看看下表。

| 特征和标准 | Marble.js v3 | 快速 v4 |
| 支持的最低 Node.js 版本 | ≥v8.0 | ≥v0.10.0 |
| 类型脚本支持 | ✅ | ❌ |
| 内置中间件 | ✅ | ✅ |
| 核心包装尺寸 | 102 kB | 208 kB |
| HTTP helpers | ✅ | ✅ |
| Modular design | ✅ | ✅ |
| I/O 操作 | 981 号房 | 981 号房 |
| 支持[【电子邮件保护】](/cdn-cgi/l/email-protection) | ✅ | ❌ |
| Async readers | ✅ | ✅ |
| Request body parsing | ✅ | ✅ |
| 日志记录–用于服务器/服务日志记录的可插拔的现成解决方案。 | ✅ | ✅ |
| RxJS support | ✅ | ✅ |

## 创建我们的 Marble.js 和 Express.js 目录

我们要创造一个“你好，世界！”使用 Marble.js 和 Express.js 编写程序，概述如何在 Marble.js 和 Express.js 中处理路由，并在创建的 web 应用程序上运行基准测试工具，以查看这两个框架中哪一个更快。

正如我们上面提到的，Express.js 要求 Node ≥v0.10.0，而 Marble.js 要求 Node ≥v8.0。

在您想要的项目目录中。创建将保存您的大理石应用程序的文件夹。

```
$ mkdir MarbleJsApp
```

在你喜欢的代码编辑器中打开你的`MarbleJsApp`文件夹；我将使用 VS 代码。

```
$ code
```

打开后，打开终端窗口并运行命令:

```
$ yarn init -y
```

安装`@marblejs/http`模块:

```
$ yarn add @marblejs/core @marblejs/http fp-ts rxjs

Every @marblejs/* package requires @marblejs/core + fp-ts + rxjs to be installed first.
```

在另一个项目目录中，创建保存 Express 应用程序的文件夹。

```
$ mkdir ExpressJsApp
```

打开`ExpressJsApp`文件夹。

```
$ code
```

在终端窗口中，运行命令创建`package.json`文件。

```
$ yarn init -y
```

现在，打开你的`ExpressJsApp`文件夹和终端窗口，安装 Express。

```
$ yarn add express
```

## 创建 Marble.js 服务器

从版本 4.0 开始，`@marblejs/http`包包含了所有与 HTTP 相关的 API，我们需要安装这个包，以便 Marble 执行创建服务器端应用程序所需的两个基本配置。两个基本配置是 HTTP 侦听器定义和 HTTP 服务器配置。

我们将在项目的根目录下创建三个基本文件:

*   `index.ts`
*   `http.listener.ts`
*   `api.effects.ts`

打开终端窗口，输入以下命令创建文件:

```
$ touch index.ts http.listener.ts api.effects.ts
```

在我们的`index.ts`文件中，我们首先用来自`[@marblejs/http module](https://docs.marblejs.com/other/api-reference/marblejs-http)`的`createServer`方法创建一个 Marble 应用程序实例；`createServer`方法是 Node.js 服务器创建者的包装器。

```
import { createServer } from '@marblejs/http';
import { IO } from 'fp-ts/lib/IO';
import { listener } from './http.listener';

const server = createServer({
  port: 1337,
  hostname: '127.0.0.1',
  listener,
});

const main: IO<void> = async () =>
  await (await server)();

main();

```

为了测试运行新创建的服务器，我们安装了 TypeScript 编译器和`ts-node`:

```
$ yarn add typescript ts-node
```

将以下脚本添加到我们的`package.json`文件中:

```
 "scripts": {
    "start": "ts-node index.ts"
  },

```

启动服务器:

```
$ yarn start
```

服务器应该正在运行。您可以通过访问`[http://localhost:1337](http://localhost:1337)`使用浏览器进行检查。

## 创建 Express.js 服务器

我们在我们的`ExpressJSApp`文件夹中创建一个`index.js`文件，并创建一个监听`port 1338`连接的服务器。

```
const express = require("express");
const app = express();
const port = 1338;

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`);
});

```

使用以下命令运行 Express 应用程序:

```
$ node app.js
```

我们已经能够只用几行代码创建我们的服务器——先睹为快。

我们可以打开浏览器选项卡并粘贴 URL `[http://localhost:1338](http://localhost:1338)`。应用程序应该回应“你好，世界！”

## Marble.js vs. Express.js:路由

让我们看看如何在 Express.js 和 Marble.js 中处理路由。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

Express.js 在它们的`req` 和`res` 对象中包含了很多 helper 函数，这些函数自带了`res.send`、`res.download`、`res.redirect`等方法。

```
const express = require("express");
const app = express();
const port = 1338;
app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`);
});

```

相比之下，在 Marble 中定义路线是通过定义 API 效果来完成的。提醒一下，一个效果仅仅是一个响应输入请求的事件流。在我们的例子中，它将返回我们的“你好，世界！”消息。

为了在 Marble 中构建我们的路线，我们打开`http.listener.ts` 文件来定义我们的`[httpListener](https://docs.marblejs.com/other/api-reference/marblejs-http/core-httplistener)` 程序。这是每个 Marble 应用程序的基本起点，因为它包括所有全局中间件和 API 效果的定义。

然后，我们将在应用程序中安装两个更重要的模块:一个用于日志记录，一个用于解析。

使用以下命令安装中间件包:

```
$  yarn add @marblejs/middleware-logger @marblejs/middleware-body
```

并且这样定义`httpListener`:

```
import { httpListener } from '@marblejs/http';
import { logger$ } from '@marblejs/middleware-logger';
import { bodyParser$ } from '@marblejs/middleware-body';
import { api$ } from './api.effects';

const middlewares = [
  logger$(),
  bodyParser$(),
];

const effects = [
  api$,
];

export const listener = httpListener({
  middlewares,
  effects,
});

```

使用 Effects 接口，我们可以定义 API 端点或事件处理程序。我们将创建一个 API 效果，返回“Hello，world！”给用户。

打开`api.effects.ts`文件。在这里，我们将创建第一个端点。为了路由我们的第一个`httpEffect`，我们必须定义传入请求应该匹配的路径和方法。

这里，我们将`matchPath` 属性指定为 **`("/")`** 。我们对`matchType` 属性的方法是一个`GET`方法。

### 在`api.effects.ts`中定义我们的第一个端点

```
import { r } from '@marblejs/http';
import { mapTo } from 'rxjs/operators';

export const api$ = r.pipe(
  r.matchPath('/'),
  r.matchType('GET'),
  r.useEffect(req$ => req$.pipe(
     mapTo({ body: 'Hello, world!' }),
  )));

```

如果我们回到`[http://localhost:1337](http://localhost:1337)`，应该会看到我们的“你好，世界！”正在返回消息。

## 比较性能基准

**硬件:**

*   笔记本电脑:联想 Thinkpad W540
*   CPU: Intel ® Core(™) i7-4800MQ CPU @ 2.70GHz

**条件:**

*   125 个连接
*   5000000 个请求

### 基准测试 Marble.js HTTP 性能

为了进行测试，我将使用 [Go Bombardier 包](https://github.com/codesenberg/bombardier)，该包使用以下命令运行 5000000 个请求和 125 个并发连接:

运行以下命令，指定 Marble.js 项目的 URL。

```
$ bombardier -c 125 -n 5000000 http://127.0.0.1:1337/
```

下面是我们在 Marble.js 应用程序上运行测试的结果。

```
Bombarding http://127.0.0.1:1337/ with 5000000 request(s) using 125 connection(s)
 5000000 / 5000000 [=====================================================================================] 100.00% 2355/s 35m22s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec      2368.27     790.92    6658.29
  Latency       53.07ms    12.42ms   601.59ms
  HTTP codes:
    1xx - 0, 2xx - 5000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:   604.92KB/s
```

### 基准测试 Express.js HTTP 性能

运行以下命令，指定 Express.js 项目的 URL。

```
$ bombardier -c 125 -n 5000000 http://127.0.0.1:1338/
```

下面是我们在 Express.js 应用程序上运行测试的结果。

```
Bombarding http://127.0.0.1:1338/ with 5000000 request(s) using 125 connection(s)
 5000000 / 5000000 [================] 100.00% 5738/s 14m31s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec      5745.13    1164.50   34982.51
  Latency       21.78ms     4.02ms   411.92ms
  HTTP codes:
    1xx - 0, 2xx - 5000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:     1.65MB/s
```

基准测试的目的是关于性能和比较两个框架的性能。考虑到这一点，最重要的指标是每秒的请求数。

当考虑这种度量时，我们希望尽可能高的值，因为应用程序每秒可以处理的请求越多，应用程序就越快。

以下是我们基准测试的结果:

| 结构 | 大理石. js | 快递. js |
| 每秒请求数 | 2368.27 | 5745.13 |
| 等待时间(毫秒) | 53.07 | 21.78 |
| 处理 5000000 个请求的总时间 | 35 分钟 | 14 分钟 |

按照这个标准，Express.js 的延迟更高。查看我们的基准测试结果，Express.js 应用程序的性能比 Marble.js 应用程序好得多。

## 结论

Express.js 非常适合创建简单的应用程序，正如我们在创建“Hello，world！”程序，构建出简单的路由，并易于学习。

在我们的基准测试中，Express 比 Marble.js 更有能力处理更多的请求。

Marble.js 提供了对 TypeScript 的支持，极大地支持了 RxJS 提供的更高级的功能，并且通过其专用的`@marblejs/messaging`模块，Marble.js 为构建基于事件的微服务提供了一个健壮的统一架构。

Express.js 非常适合构建 RESTful APIs，但是在构建事件驱动架构的应用程序时，Marble.js 更胜一筹。

Express.js 仍然很受欢迎，并将继续在重大项目和行业中使用，但 Marble.js 在函数式反应式编程的支持者中相当受欢迎。有希望的是，我们将继续看到 Marble 在开发人员中的使用增加，这些开发人员正在寻求将其异步特性融入到依赖服务器来处理繁重工作和复杂计算而不冻结主线程的应用程序中。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.