# 忘记 Express.js 吧——选择这些替代品——LogRocket Blog

> 原文：<https://blog.logrocket.com/forget-express-js-opt-for-these-alternatives-instead/>

***编者按:**这篇文章于 2023 年 3 月 10 日更新，包含了关于其他 Node.js 框架的信息，这些框架可能值得考虑作为 Express.js 的替代方案*

在构建 HTTP 服务器时，Node.js 提供了一些强大的原语。默认情况下，每当服务器收到 HTTP 请求时，就会运行一个函数。众所周知，解析包含 JSON 主体的传入 POST 请求的服务器示例看起来有点像这样:

```
const http = require('http');

const server = http.createServer((req, res) => {
  // This function is called once the headers have been received
  res.setHeader('Content-Type', 'application/json');

  if (req.method !== 'POST' || req.url !== '/user') {
    res.statusCode = 405;
    res.end('{"error":"METHOD_NOT_ALLOWED"}');
    return;
  }

  let body = '';

  req.on('data', (data) => {
    // This function is called as chunks of body are received
    body += data;
  });

  req.on('end', () => {
    // This function is called once the body has been fully received
    let parsed;

    try {
      parsed = JSON.parse(body);
    } catch (e) {
      res.statusCode = 400;
      res.end('{"error":"CANNOT_PARSE"}');
    }

    res.end(JSON.stringify({
      error: false,
      username: parsed.username
    }));
  });
});

server.listen(3000, () => {
  console.log('Server running at http://localhost:3000/');
});

```

默认情况下，Node.js 允许我们在收到任何请求时运行一个函数。没有基于路径的内置路由器。Node.js 执行一些基本的解析——例如，解析传入的 HTTP 消息并提取不同的组件，如路径、头对、编码(Gzip 和 SSL)等。

然而，对更高级功能的需求意味着我们通常不得不使用 web 框架。例如，如果收到一个`multipart/form-data`或`application/x-www-form-urlencoded`请求，我们需要使用一个模块来为我们解码内容。如果我们想简单地基于模式匹配和 HTTP 方法路由请求，我们将需要一个模块——或者，通常，一个完整的 web 框架——来为我们处理这个问题。

这就是像 Express.js 这样的工具发挥作用的地方。

在本文中，我们将研究 Express.js 以及其他几个 Node.js 框架，您可能希望在下一个项目中考虑这些框架。

*向前跳转:*

## 了解 Express.js

Express.js 很早就成为使用 Node.js 构建 web 应用程序的首选框架。它为路由 HTTP 请求提供了良好的语法，并为构建中间件提供了标准化的接口。此外，它使用了核心 Node.js APIs 和大多数 npm 生态系统所采用的熟悉的回调模式。

Express.js 变得如此流行，以至于它几乎无处不在地与 Node.js 联系在一起——就像当我们读到 Ruby 语言时，我们已经想到了框架 Rails。事实上，Express.js 和 Node.js 都是流行的 MEAN 和 MERN 堆栈首字母缩略词的成员。

让我们来看看当我们把 Express.js 引入到图片中时，我们前面的例子可能是什么样子的:

```
const express = require('express');
const app = express();

app.post('/user', (req, res) => {
  // This function is called once the headers have been received

  let body = '';

  req.on('data', (data) => {
    // This function is called as chunks of body are received
    body += data;
  });

  req.on('end', () => {
    // This function is called once the body has been fully received
    let parsed;

    try {
      parsed = JSON.parse(body);
    } catch (e) {
      res.statusCode = 400;
      res.json({
        error: 'CANNOT_PARSE'
      });
    }

    res.json({
      error: false,
      username: parsed.username
    });
  });

});

app.listen(3000, () => {
  console.log('Server running at http://localhost:3000/');
});

```

在这个例子中，我们看到情况有所好转。我们可以通过使用`app.post('/user')`来具体说明我们想要匹配的方法和路径。这比在处理程序中编写一个大的分支语句要简单得多。

我们也被赋予了一些其他的细节。考虑一下`res.json({})`方法:这不仅将一个对象序列化为它的 JSON 等价物，而且还为我们设置了适当的`Content-Type`头！

然而，Express.js 仍然给了我们与使用内置的`http`模块时相同的范例；例如，我们仍然在调用`req`和`res`对象上的方法。

## 考虑一个 HTTP 服务器的理想例子

让我们后退一步，看看 HTTP 服务器的理想示例是什么样的。路由是可取的，Express.js 具有强大的路由语法(例如，它支持动态路由模式)。然而，在控制器函数中运行的代码是我们真正想要清理的地方。

在上面的例子中，我们用异步代码做了很多工作。请求对象是一个事件发射器，它发出我们关心的两个事件，即`data`和`end`。但是，实际上，我们通常只是希望能够将 HTTP 请求转换成 JSON 对象，以便于从中提取值。

此外，我们还得到一个请求(`req`)和一个响应(`res`)对象。`req`对象是有意义的——它包含了关于我们正在接收的请求的信息。但是`res`真的有那么多意义吗？我们只想提供一个来自控制器函数的结果作为回复。

对于同步函数，从函数调用中接收结果很简单:只需返回值。如果我们利用`async`函数，我们可以做同样的事情。通过返回对一个`async`函数的调用，控制器函数可以解析一个值，该值最终表示我们希望消费者接收的响应。

让我们来看一个例子:

```
const server = someCoolFramework();

server.post('/user', async (req) => {
  let parsed;

  try {
    parsed = await req.requestBodyJson();
  } catch (e) {
    return [400, {
      error: 'CANNOT_PARSE'
    }];
  }

  return {
    error: false,
    username: parsed.username
  };
});

server.listen(3000, () => {
  console.log('Server running at http://localhost:3000/');
});

```

在我们这个理想化的例子中有一些概念。首先，我们维护 Express.js 使用的现有路由器语法，因为它相当可靠。其次，我们的`req`对象提供了一个助手，用于将传入的请求转换成 JSON。

第三个特性是，我们能够通过简单地返回结果来提供响应的表示。由于 JavaScript 不支持元组，我们实际上是通过使用数组来重新创建一个元组。因此，在这个虚构的例子中，返回的字符串可以作为主体直接发送给客户机，返回的数组可以用于表示状态代码和主体(可能还有第三个参数用于元数据，如头)，返回的对象可以转换成它的 JSON 表示。

## 改编 Express.js

现在，使用一组中间件，用 Express.js 实际上可以重现一些这种行为。

`express-async-handler` npm 模块提供了一个包装函数，它可以插入并允许`async`控制器函数与 Express.js `app.use` API 很好地交互。不幸的是，这需要开发人员手动包装每个控制器功能:

```
const asyncHandler = require('express-async-handler')

app.post('/user', asyncHandler(async (req, res, next) => {
  const bar = await foo.findAll();
  res.send(bar);
}))

```

响应元组展开也可以由中间件来处理。这种中间件需要在控制器代码运行后运行，并用 Express.js 期望的表示替换数组。

承诺请求正文流解析的能力也可以以通用方式构建:

```
app.use((req, res, next) => {
  req.bodyToJson = requestBodyJson(req);
  next();
});

function requestBodyJson(req) {
  return new Promise((resolve, reject) => {
    let body = '';

    req.on('data', (data) => {
      // This function is called as chunks of body are received
      body += data;
    });

    req.on('end', () => {
      // This function is called once the body has been fully received
      let parsed;

      try {
        parsed = JSON.parse(body);
      } catch (e) {
        reject(e);
        return;
      }

      resolve(parsed);
    });
  });
}

```

有了上面的代码，我们就可以等待使用 Express.js 进行解析了(实际上还有其他任何给定 HTTP `Request`对象实例的情况):

```
// When using the Express.js middleware:
const parsed = await req.bodyToJson();

// Using the function generically:
const parsed = await requestBodyJson(req);

```

## 使用替代 Node.js 框架

的确，我们可以使用 Express.js 来复制这些期望的模式，但是也有一些框架是在支持 promises 和 async/await 范式的基础上构建的。让我们看看使用不同的 web 服务器框架编写的示例控制器是什么样子的。

### Fastify

顾名思义，Fastify 是一个非常快速的 Node.js web 框架。尽管它的主要目标是速度，但它实际上很好地实现了我们理想的控制器语法。

这个例子非常简洁，几乎让人感觉像是作弊:

```
const fastify = require('fastify');
const app = fastify();

app.post('/user', async (req, reply) => {
  return {
    error: false,
    username: req.body.username
  };
});

app.listen(3000).then(() => {
  console.log('Server running at http://localhost:3000/');
});

```

Fastify 不仅支持将`async`函数用作控制器代码，而且如果`Content-Type`头表明主体是 JSON，它还会自动将传入的请求解析为 JSON。这就是示例代码如此之小的原因。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这也意味着当解析失败时，我们可以依靠 Fastify 来响应一个正常的错误。例如，当客户端向 Fastify 发送无效的 JSON 时，响应将类似如下:

```
{
  "statusCode": 400,
  "error": "Bad Request",
  "message": "Unexpected string in JSON at position 19"
}

```

#### 快速启动

```
npm install fastify

```

### 寇阿相思树

[Koa](https://koajs.com/) 是 Express.js 的精神继承者，由一些最初的 Express.js 作者编写。它确实支持户外功能，但它没有自带路由器。我们可以利用`koa-router`来提供路由。

下面是我们的示例控制器在 Koa 中的样子:

```
const Koa = require('koa');
const Router = require('koa-router');
const app = new Koa();
const router = new Router();

router.post('/user', async (ctx) => {
  try {
    const parsed = await requestBodyJson(ctx.req);
    ctx.body = {
      error: false,
      username: parsed.username
    };
  } catch (e) {
    ctx.status = 400;
    ctx.body = {
      error: 'CANNOT_PARSE'
    };
  }
});

app.use(router.routes());
app.listen(3000);

```

这个 Koa 例子不像 Fastify 版本那样简洁。它不执行自动 JSON 解析，但是我们能够重用我们之前创建的`requestBodyJson()`方法。它也不使用从我们的控制器返回/解析的值，而是通过使用附加到`ctx`参数的数据来工作。

#### 快速启动

```
 npm i koa

```

### 嵌套. js

Nest.js 为 TypeScript 提供了内置的支持，这保证了类型安全，并为创建可测试、可伸缩、模块化和可维护的应用程序提供了一个架构。与 Fastify 和 Express.js 等其他流行框架一样，Nest.js 提供了广泛的功能，包括依赖注入、中间件、路由和异步编程。

下面是我们的示例控制器在 Nest.js 中的样子:

```
import { Controller, Post, Body } from '@nestjs/common';

@Controller()
export class UserController {
  @Post('/user')
  async createUser(@Body() body: { username: string }): Promise<{ error: boolean, username: string }> {
    return {
      error: false,
      username: body.username,
    };
  }
}

```

Nest 还自动将传入的请求解析成 JSON，并支持控制器代码的`async`函数。然而，它不像 Fastify 那样简洁，这是由于它的面向对象、模块、依赖注入和装饰架构模式，这不容易掌握，尤其是对于开发新手来说。

#### 快速启动

```
npm i @nestjs/core

```

### 哈比神

哈比神最初是为了处理沃尔玛的黑色星期五销售而构建的，因此它是开发人员构建可伸缩且健壮的应用程序的最佳选择之一。它非常适合我们选择控制器语法，因为它提供了许多内置特性，比如路由和异步编程。

下面是我们的示例控制器在使用哈比神时的样子:

```
const Hapi = require('@hapi/hapi');

const init = async () => {
  const server = Hapi.server({
    port: 3000,
    host: 'localhost'
  });

  server.route({
    method: 'POST',
    path: '/user',
    handler: async (request, h) => {
      return {
        error: false,
        username: request.payload.username
      };
    }
  });

  await server.start();
  console.log('Server running at:', server.info.uri);
};

process.on('unhandledRejection', (err) => {
  console.error(err);
  process.exit(1);
});

init();

```

哈比神使用上例中的`request.payload`对象提取传入的请求体，并根据请求的`content-type`头自动解析它。

默认情况下，哈比神不仅解析传入的请求，还提供了一系列功能来帮助开发人员处理请求数据。例如，哈比神的验证系统允许开发人员为传入的请求数据定义输入验证规则，确保数据在被应用程序处理之前是有效和安全的。

#### 快速启动

```
npm install @hapi/hapi

```

### 德比耶斯

DerbyJS 采用了一种不同的方法来创建服务器端应用。它是一个模型-视图-控制器框架，可用于编写实时后端或前端应用程序。该框架为模板、路由、异步编程和后端框架中常见的其他特性提供了内置支持。

下面是我们的示例控制器使用 DerbyJS 时的样子:

```
const app = require('derby').createApp();
const http = require('http');

app.post('/user', async function(page, model, params, next) {
  try {
    const username = params.body.username;
    await new Promise(resolve => setTimeout(resolve, 1000));
    next(null, {error: false, username: username});
  } catch (err) {
    next(err);
  }
});

const server = http.createServer(app.run());

server.listen(3000, function() {
  console.log('Server running at http://localhost:3000/');
});

```

尽管 DerbyJS 采用了非常规的架构，但它提供了对请求体的自动解析，不需要像其他 Node.js 框架那样使用中间件或库来手动解析请求体。

#### 快速启动

```
git clone https://github.com/derbyjs/derby-examples.git

```

## 外卖食品

当 Node.js 还处于起步阶段时，Express.js 成为构建 web 应用程序的明显选择。Express.js 的目标是成为一个遵循回调范例的方便的 web 服务器。它实现了这个目标，产品现在基本上已经完成了。

然而，随着 JavaScript 生态系统的成熟，我们获得了新的语言工具和语法。从那以后，出现了几十个甚至几百个框架，其中许多都采用了这些新的语言特性。

如果你发现自己在从事一个用 Node.js 编写的新项目，它充当 web 服务器，我鼓励你考虑新的竞争者，比如 Koa、Nest.js、哈比神、DerbyJS 和 Fastify，而不是默认使用熟悉的 Express.js

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.