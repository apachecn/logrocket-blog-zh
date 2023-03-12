# tinyhttp 与 Express.js:比较 Node.js 框架

> 原文：<https://blog.logrocket.com/tinyhttp-vs-express-js-comparing-node-js-frameworks/>

在本指南中，我们将比较用 Node.js、Express.js 构建 web 应用程序最常用的框架之一和它最流行的替代方案之一 tinyhttp。

以下是我们将要介绍的内容:

## tinyhttp 是什么？

tinyhttp 是一个类似于 Express.js 的现代 web 框架，是用 TypeScript 编写的。与 Express.js 相比，它使用了非常少的依赖项，这使得它非常快。

tinyhttp 提供路由、req/res 扩展等等，所有这些都只依赖于六个模块——其中四个是 tinyhttp 自己的。

为 Express.js 创建的所有中间件都可以与 tinyhttp 顺利协作。查看这个 [tinyhttp + Express/GraphQL](https://github.com/talentlessguy/tinyhttp/tree/master/examples/graphql) 集成，了解如何将 Express.js 中间件与 tinyhttp 结合使用的示例。

tinyhttp 还支持路由的 async/await，这在 Express.js 中不是这样。tinyhttp 中有正确的类型和一个函数检查器，它解析异步函数。换句话说，在 tinyhttp 中，同步和异步都可以很好地工作。看看这些[异步](https://github.com/talentlessguy/tinyhttp/tree/master/examples/async)和 [MongoDB](https://github.com/talentlessguy/tinyhttp/tree/master/examples/mongodb) 的例子，看看如何制作异步处理程序。

根据[官方文档](https://github.com/talentlessguy/tinyhttp#tinyhttp)，这里列出了 tinyhttp 最重要的特性:

*   比 Express 快 2.5 倍
*   完整的 Express 中间件支持
*   异步中间件支持
*   原生 ESM 和 CommonJS 支持
*   没有遗留依赖，只有 JavaScript 本身
*   现成的类型
*   没有内置中间件支持

## 什么是 Express.js？

Express.js 是一个灵活的 Node.js 框架，为 web 和移动应用程序提供了一组健壮的特性。创建健壮的 API 非常容易，因为 Express.js 附带了许多中间件和其他内置支持。

Express.js 没有自带数据库；这留给了第三方模块，它允许您与几乎任何数据库进行交互。Express.js 支持无数的[模板引擎](https://expressjs.com/en/guide/using-template-engines.html)，它们与`(path, locals, callback)`签名相匹配。

这个框架是以这样一种方式构建的，它作为一个最小的、灵活的 Node.js web 应用程序框架，为构建单页面、多页面和混合 web 应用程序提供了一组健壮的特性。

Express.js 的一些特性:

*   开源社区支持
*   快速应用开发
*   简单易学
*   模板引擎支持
*   输入输出处理
*   内置中间件支持

## tinyhttp 与 Express.js:基本比较

对于 tinyhttp 和 Express v4 的高级比较，我们来看看支持的最低 Node.js 版本、ECMAScript 版本、测试覆盖率等。：

| **标准** | **tinyhttp** | **快递 v4** |
| --- | --- | --- |
| 支持的最低 Node.js 版本 | 12.4.0 | 0.10.0 |
| 支持的最低 ECMAScript 版本 | ES2019 | ES5(？) |
| `req` / `res`扩展 | ✔️ | ✔️ |
| 测试覆盖率 | 92% | 100% |
| 编译为本机 ESM | ✔️ | ✖️ |
| 类型脚本支持 | ✔️ | ✖️ |
| 封装尺寸(仅核心) | 35.2 kB | 208 kB |
| 内置中间件 | ✖️ | ✔️ |

上表中的一些注释:

*   tinyhttp 工作所需的最低 Node.js 版本是 12.4.0，而对于 Express v4，我们甚至可以从 Node.js 0.10.0 开始工作
*   tinyhttp 支持的最低版本是 ES2019 (ES10)，而在 Express v4 中，我们需要 ES5
*   `req`是一个对象，包含有关引发事件的 HTTP 请求的信息。为了响应`req`，您使用`res`发回想要的 HTTP 响应。tinyhttp 和 express v4 都支持`req` / `res`扩展
*   tinyhttp 和 Express.js v4 的代码库测试覆盖率分别为 92%和 100%
*   ES6(或 ES2015)规范中引入的 ESM (EcmaScript 模块)规范描述了如何在 JavaScript 中导入和导出模块。我们可以在 tinyhttp 中使用 ESM，但是我们需要外部支持，比如针对 Express.js 的 [Babel](https://blog.logrocket.com/why-you-dont-need-babel/)
*   与 Express.js 不同，tinyhttp 附带了 TypeScript 支持
*   tinyhttp 的核心包大小是 35.2kB，而 Express.js 是 208kB
*   tinyhttp 没有内置中间件。Express.js 确实有[内置中间件](http://expressjs.com/en/guide/using-middleware.html#middleware.built-in)

## 性能基准

了解了基础知识之后，让我们来看看使用 [fastify 基准测试工具](https://github.com/talentlessguy/benchmarks)的性能报告。这里我们使用 req/s、transfer/s 和 latency 等参数来衡量两个框架的性能。

下面是关于该基准测试所使用的硬件、系统和条件的一些说明。

*   **硬件**:
    *   小米 Pro 2019 版(笔记本电脑)
    *   CPU: Intel Core i7-8550U
    *   内存:16GB
*   **系统:**
    *   内核:5.7.19-2
    *   节点:15.5
*   **条件**:
    *   100 个连接
    *   10 条管道
    *   40 秒持续时间

| **框架** | **请求/秒** | **传输/秒** | **等待时间** |
| --- | --- | --- | --- |
| @tinyhttp/app(不带 exts) | 24575 | 3.81 兆字节 | 3.37 毫秒 |
| @tinyhttp/app (esm) | 22820 | 3.54 兆字节 | 4.04 毫秒 |
| @tinyhttp/app (cjs) | 22637 | 3.51 兆字节 | 4.08 毫秒 |
| [【电子邮件保护】](/cdn-cgi/l/email-protection) | 12986 | 2 MB | 7.11 毫秒 |

`req/s`是每秒的请求数。`Latency`指用户动作和 web 应用程序对该动作的响应之间的延迟，也称为总往返时间。

从上表中，我们可以看到 tinyhttp 能够以比 Express.js v4 更低的延迟每秒进行更多的传输

**注意**:基准测试并不完全准确，可能在每次运行和每台机器上都有所不同。关键是比较比例而不是绝对值。

总结我们的性能基准，tinyhttp(没有额外的`req` / `res`扩展)比 Express.js 快大约 1.9 倍。

## tinyhttp 和 Express.js 的应用:一个简单的例子

现在是时候构建一个简单的示例应用程序了，这样我们就可以看到 tinyhttp 和 Express.js 并排运行。

您可以使用任何软件包管理器来安装 tinyhttp 和 Express。我用 [npm](https://www.npmjs.com/) 来演示。

要安装 tinyhttp:

```
npm i @tinyhttp/app

```

要安装 Express.js:

```
npm install express

```

### “你好，世界”

Express.js 和 tinyhttp app 的结构非常相似。如果你知道 Express.js，那么你也知道 tinyhttp。

tinyhttp:

```
import { App } from '@tinyhttp/app'
const app = new App()
const PORT = 3000

app
  .get('/', (_, res) => void res.send('<h1>Hello World</h1>'))
  .listen(PORT, () => console.log(`Started on http://localhost:${PORT}!`))

```

快递. js:

```
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Hello world');
});

app.listen(3000, () => console.log('listening on port 3000'));

```

你可能已经注意到，我们使用的是 ESM 导入，而不是`require`。tinyhttp 被设计成与[本地 ESM](https://nodejs.org/api/esm.html) 一起使用。与 Express 不同，tinyhttp 编译成 ESM 和 CommonJS 模块系统。同时，不知何故，还是比 Express.js 小很多。

你可以在 Node.js 中使用`import` / `export`语法。要设置节点 ESM 包，将`"type": "module"`放在`package.json`文件中，如下所示:

```
{
  "type": "module"
}

```

另一个选择是使用一个`.mjs`扩展。这样，您就不需要将那个`"type"`字段放在`package.json`中。更多信息，请查看 [ECMAScript Modules Node.js 文档](https://nodejs.org/api/esm.html)。

因为最流行的 Express.js 中间件也使用过时的模块，tinyhttp 提供了一组它对流行软件的重写/重制，比如`logger`、`session`等等。

### 按指定路线发送

现在让我们来看看如何在 tinyhttp 和 Express.js 中处理一些基本的路由。Express.js 在它们的`req`和`res`对象中包含了许多助手函数。tinyhttp 使用`res.send`、`res.download`、`res.redirect`等方法完全实现了 Express.js APIs。

tinyhttp:

```
import { App } from '@tinyhttp/app'
import { once } from 'events'

const app = new App()
const PORT = 3000

app.get('/', (req, res) => {
    res.send('Sent a GET!')
})

app.post('/', async (req, res) => {
    // Nothing complex here, we just listen to 'data' event and return the data as a promise to a `data` variable
    const data = await once(req, 'data').then(d => d.toString())

    // And then we send it
    res.end(`Sent some data: ${data}`)
})

app.listen(PORT, () => console.log(`Started on http://localhost:${PORT}!`))

```

快递. js

```
var express = require('express');
var app = express();

app.get('/', function(req, res){
   res.send("Send a GET!");
});

app.post('/', function(req, res){
   res.send("hello'!\n");
});

app.listen(3000);

```

## 结论

tinyhttp 又快又轻，你今天就可以开始用它做后端应用。 [tinyhttp 库](https://github.com/talentlessguy/tinyhttp)包含了大量的[实例](https://github.com/talentlessguy/tinyhttp/tree/master/examples)，包括 [MongoDB](https://github.com/talentlessguy/tinyhttp/tree/master/examples/mongodb) 和 [GraphQL](https://github.com/talentlessguy/tinyhttp/tree/master/examples/graphql) 集成。当你需要快速入门最少的代码时，我推荐使用 tinyhttp。

也就是说，Express.js 仍然很受欢迎，并将继续在主要项目和行业中使用，因为它有广泛的开源社区支持和简单的学习曲线。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.