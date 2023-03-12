# 流行节点项目的 Deno 替代方案

> 原文：<https://blog.logrocket.com/deno-alternatives-to-popular-node-projects/>

Deno 是一个简单的现代安全运行时，用于使用 JavaScript 和 TypeScript 编写应用程序，它使用 Rust 内置的 V8 引擎。

这不是一篇解释 Deno 是什么的文章。但是，如果你需要了解更多关于 Deno 的信息，我可以提供帮助。查看这篇文章:[Deno 是什么，和 Node.js 有什么不同？](https://blog.logrocket.com/what-is-deno/)

如果你对未来的发展感兴趣，你也可以看看 Deno [库](https://github.com/denoland/deno)。

现在，Node 在过去几年中变得强大和流行。还出现了一些流行的节点项目，它们被称为依赖项。自从 Deno 诞生以来，关于如何在 Deno 中做我们在 Node 中做的同样的事情，一直有很多疑惑。

我将介绍 Node 中最受欢迎的几款，并向您展示 Deno 中的替代产品。

## Next.js/Aleph.js

[Aleph.js](https://alephjs.org/docs) 是 Deno 中的一个 React 框架。对，就是这样。正如 [Next.js](https://nextjs.org/) 处于节点。Next.js for Node 提供了使用 React 进行生产所需的所有特性。它允许您构建服务器端呈现、自动代码分割、静态导出选项、简单的生产构建以及静态 web 应用程序。

与 Next.js 不同，Aleph.js 不使用也不需要捆绑器，因为它使用 ESM(ECMAScript 模块)导入语法。每个导入的模块只编译一次并缓存:

```
import React from "https://esm.sh/[email protected]"
import Logo from "../components/logo.tsx"

export default function Home() {
    return (
      <div>
        <Logo />
        <h1>Hello World!</h1>
      </div>
    )
}

```

Aleph.js 提供的其他高级功能有异步导入、自定义`404`页面、自定义加载页面、`useDeno`钩子等等。

## Express.js/Opine

Express.js 因其开放、免费或自由的极简 web 框架而非常受欢迎。它的理念是为 HTTP 服务器提供一个小而快速的工具，使其成为 spa、网站、混合或公共 HTTP APIs 的绝佳选择。无论如何，你可以使用 Express.js 来构建你自己的框架。另一个很棒的地方是它健壮的路由系统，可以有效地响应客户的请求，并且很少选择 HTTP 助手。

与 Express.js 不同， [Opine](https://github.com/asos-craigmorten/opine/tree/0.25.0/) 专注于高性能、内容协商(当基于一种类型的请求返回响应时，这很棒)、健壮的路由和对 HTTP 代理中间件的支持(这在授权、负载平衡、日志记录等方面很有用)。指点江山前途光明！

```
import opine from "../../mod.ts";

const app = opine();

app.get("/", function (req, res) {
  res.send("Hello Deno!");
});

const server = app.listen();
const address = server.listener.addr as Deno.NetAddr;
console.log(`Server started on ${address.hostname}:${address.port}`);

```

我真的很喜欢 Opine 处理内容协商的方式，让您一目了然地处理每种格式:

```
import { opine } from "../../mod.ts";
import { users } from "./db.ts";
import type { Request, Response } from "../../src/types.ts";

const app = opine();

app.get("/", (req, res) => {
  res.format({
    html() {
      res.send(
        `<ul>${users.map((user) => `<li>${user.name}</li>`).join("")}</ul>`,
      );
    },

    text() {
      res.send(users.map((user) => ` - ${user.name}\n`).join(""));
    },

    json() {
      res.json(users);
    },
  });
});

```

## Passport.js/Onyx

Passport.js 只是帮助处理节点服务器中的认证。它作为一个中间件，非常灵活和模块化。它广泛用于任何基于 Express.js 的 web 应用程序。它支持一套认证策略([用户名和密码](http://www.passportjs.org/docs/username-password/)、[脸书](http://www.passportjs.org/docs/facebook/)、 [Twitter](http://www.passportjs.org/docs/twitter/) 等)。

与 Passport.js 不同， [Onyx](https://github.com/oslabs-beta/onyx/tree/v1.0.1/) 的主要目的是保持代码的整洁和系统化。它可以帮助您简化身份验证策略，而无需像在 Passport.js 中那样进行导入。

就像您将 Passport.js 作为中间件应用一样，Onyx 也是如此:

```
import { Router } from '../deps.ts';
import { onyx } from '../deps.ts';
import User from './models/userModels.ts';

const router = new Router();

// invoke onyx.authenticate with the name of the strategy, invoke the result with context
router.post('/login', async (ctx) => {
  await (await onyx.authenticate('local'))(ctx);

  if (await ctx.state.isAuthenticated()) {
    const { username } = await ctx.state.getUser();
    ctx.response.body = {
      success: true,
      username,
      isAuth: true,
    };
  } else {
    const message = ctx.state.onyx.errorMessage || 'login unsuccessful';
    ctx.response.body = {
      success: false,
      message,
      isAuth: false,
    };
  }
});

```

在服务器文件中设置非常简单。这与您在 Node:

```
import { Application, send, join, log } from '../deps.ts';
import { Session } from '../deps.ts';

// Import in onyx and setup
import { onyx } from '../deps.ts';
import './onyx-setup.ts';

// Server Middlewares
import router from './routes.ts';

// SSR
import { html, browserBundlePath, js } from './ssrConstants.tsx';

const port: number = Number(Deno.env.get('PORT')) || 4000;
const app: Application = new Application();

// session with Server Memory
// const session = new Session({ framework: 'oak' });

// session with Redis Database
const session = new Session({
  framework: 'oak',
  store: 'redis',
  hostname: '127.0.0.1',
  port: 6379,
});

// Initialize Session
await session.init();
app.use(session.use()(session));

// Initialize onyx after session
app.use(onyx.initialize());

```

## NodeRedis/DenoRedis

Redis 是一种流行的内存数据结构服务器，通常用于缓存、数据库和消息代理。它速度超快，吞吐量惊人。

在 Node 中， [node-redis](https://github.com/NodeRedis/node-redis) 是一个高性能的 redis 客户端，它是完全异步的。

[deno-redis](https://deno.land/x/redis@v0.14.0) 是 deno 的 redis 客户端的实现:

```
import { connect } from "https://deno.land/x/redis/mod.ts";
const redis = await connect({
  hostname: "127.0.0.1",
  port: 6379
});
const ok = await redis.set("hoge", "fuga");
const fuga = await redis.get("hoge");

```

## Nodemon/Denon

[Nodemon](https://github.com/remy/nodemon) 是一个命令行界面(CLI ),它监视文件系统中节点项目的任何变化，并自动重启进程/服务器。这有助于避免手动重启服务器来查看已经做出的更改。

在天龙，我们有[天龙](https://github.com/denosaurs/denon/tree/2.4.5/)。与 nodemon 没有区别。你要做的就是把`deno`换成`denon`这个词。

```
denon run app.ts

```

## WebSocket

Websocket API 开启了客户端和服务器之间的双向交互通信。基本上，在客户机和服务器之间有一个持久的连接，这样双方都可以在任何时候发送数据。 [ws](https://github.com/websockets/ws) 库是作为节点项目的客户机和服务器实现而构建的。

幸运的是，我们有[deno web socket](https://deno.land/x/websocket@v0.0.5)——一个和 [ws](https://github.com/websockets/ws) 一样的库，但是是 Deno 的。

服务器端的使用与任何节点项目都非常相似:

```
import { WebSocket, WebSocketServer } from "https://deno.land/x/[email protected]/mod.ts";

const wss = new WebSocketServer(8080);
wss.on("connection", function (ws: WebSocket) {
  ws.on("message", function (message: string) {
    console.log(message);
    ws.send(message)
  });
});

```

客户端也是如此:

```
import { WebSocket } from "https://deno.land/x/[[email protected]](/cdn-cgi/l/email-protection)/mod.ts";
const endpoint = "ws://127.0.0.1:8080";
const ws: WebSocket = new WebSocket(endpoint);
ws.on("open", function() {
  console.log("ws connected!");
});
ws.on("message", function (message: string) {
  console.log(message);
});
ws.send("something"); 
```

## 克-奥二氏分级量表

接下来我们有 [cors](https://github.com/expressjs/cors) ，一个作为中间件提供支持的节点包，它可以用来为 [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 提供各种选项。

在 Express.js 应用程序中启用所有 cors 请求的简单用法如下所示:

```
var express = require('express')
var cors = require('cors')
var app = express()

app.use(cors())

app.get('/products/:id', function (req, res, next) {
  res.json({msg: 'This is CORS-enabled for all origins!'})
})

app.listen(80, function () {
  console.log('CORS-enabled web server listening on port 80')
})

```

在 Deno，我们会在 Opine 做同样的事情。例如:

```
import { opine, serveStatic } from "https://deno.land/x/opine/mod.ts";
import { opineCors } from "../../mod.ts";

const app = opine();

app.use(opineCors()); // Enable CORS for All Routes

app
  .use(serveStatic(`${Deno.cwd()}/examples/opine/static`))
  .get("/products/:id", (_req, res) => {
    res.json({msg: 'This is CORS-enabled for all origins!'});
  })
  .listen(
    { port: 8000 },
    () => console.info("CORS-enabled web server listening on port 8000"),
  );

```

## Bcrypt/BCrypt

两者都是用于散列密码的类似库。由 Niel Provos 设计的 Bcrypt 是一个密码散列函数，主要用于密码。它的基本功能是从密码中生成一个散列并保存它。

虽然我们已经有了用于 Node 的 [Bcrypt](https://github.com/kelektiv/node.bcrypt.js#readme) ，但是我们现在有了用于 Deno 的 [BCrypt](https://deno.land/x/bcrypt@v0.2.4) 。Deno 中的用法仍然与 Node:

```
import * as bcrypt from "https://deno.land/x/bcrypt/mod.ts";

const hash = await bcrypt.hash("test");

// To check a password
const result = await bcrypt.compare("test", hash);

```

## 电子/网络视图

在 Node 中，您可以使用 [Electron](https://github.com/electron/electron) 创建跨平台的桌面应用程序。在 Deno，我们现在有了[网络视图](https://deno.land/x/webview@0.4.7)。它还用于为跨平台桌面应用程序创建 GUI:

```
import { WebView } from "https://deno.land/x/webview/mod.ts";

const html = `
  <html>
  <body>
    <h1>Hello from deno</h1>
  </body>
  </html>
`;

await new WebView({
  title: "Local webview_deno example",
  url: `data:text/html,${encodeURIComponent(html)}`,
  height: 600,
  resizable: true,
  debug: true,
  frameless: false,
}).run();

```

## MongoDB

MongoDB 是流行的、可伸缩的、灵活的。它是一个基于文档的数据库，在 JavaScript 生态系统中被广泛使用。

是的，现在有可能在 Deno 中使用 MongoDB:

```
import { MongoClient } from "https://deno.land/x/[email protected]/mod.ts";

const client = new MongoClient();
client.connectWithUri("mongodb://localhost:27017");

// Defining schema interface
interface UserSchema {
  _id: { $oid: string };
  username: string;
  password: string;
}

const db = client.database("test");
const users = db.collection<UserSchema>("users");

// insert
const insertId = await users.insertOne({
  username: "user1",
  password: "pass1",
});

// insertMany
const insertIds = await users.insertMany([
  {
    username: "user1",
    password: "pass1",
  },
  {
    username: "user2",
    password: "pass2",
  },
]);

// findOne
const user1 = await users.findOne({ _id: insertId });

// find
const all_users = await users.find({ username: { $ne: null } });

// find by ObjectId
const user1_id = await users.findOne({ _id: "idxxx" });

```

## 简单邮件传输协议

SMTP 是 Nodemailer 中发送邮件的主要传输方式。现在 Deno 中可用的名称为 [Deno SMTP](https://deno.land/x/smtp@v0.6.0) :

```
import { SmtpClient } from "https://deno.land/x/smtp/mod.ts";

const client = new SmtpClient();

await client.connect({
  hostname: "smtp.163.com",
  port: 25,
  username: "username",
  password: "password",
});

await client.send({
  from: "[email protected]",
  to: "[email protected]",
  subject: "Mail Title",
  content: "Mail Content，maybe HTML",
});

await client.close();

```

## NPM/Trex

Node 中的 npm 是流行的包管理工具，已经存在很长时间了，令人惊叹的是，我们在 Deno 中用 [Trex](https://deno.land/x/trex@v1.3.2) 替代了它。它有很多选项，很像 npm。不要忘记包是被缓存的，并且只生成一个文件。

有很多这样的 Deno 替代品，目前仍在开发中。我们还有:

*   [dino env](https://deno.land/x/dinoenv@v1.0.0)–管理环境变量
*   [deno db](https://github.com/eveningkid/denodb)–MySQL、MongoDB、SQLite 的 ORM
*   [CSV](https://github.com/hashrock/deno-fnparse/blob/master/parsers/csv.ts)–CSV 解析器
*   [deno-MySQL](https://github.com/denodrivers/mysql)–MySQL 驱动程序
*   [CAC](https://github.com/cacjs/cac)–构建命令行应用程序的框架。

## 结论

在本文中，我们讨论了开发过程中使用的流行节点项目的一些 Deno 替代方案。我们相信 Deno 仍在增长，如果你想进一步涉足 Deno 领域，了解当前可用的替代方案将是一件好事。

如果你觉得我跳过了一个流行的节点，你可以把它们作为回复。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.