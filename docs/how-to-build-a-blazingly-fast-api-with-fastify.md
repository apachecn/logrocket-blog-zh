# 如何用 Fastify 构建一个速度惊人的 API

> 原文：<https://blog.logrocket.com/how-to-build-a-blazingly-fast-api-with-fastify/>

Fastify 是一个后端 web 开发框架，具有强大的插件架构和最小的开销。它主要受[哈比神](https://hapi.dev/)和 [Express](https://expressjs.com/) 的启发，是运行在 Node.js 上最快的 web 框架之一

Fastify v3.0 是最新版本，已于 2020 年 7 月初上市。版本 3 具有一些特殊的新功能，例如:

*   在伪造中运行快速应用程序
*   日志序列化的变化
*   模式替换的变化
*   更好的类型脚本支持

## Fastify 功能概述

现在，我们已经介绍了 Fastify 的最新版本更新功能，让我们回顾一下 Fastify 最重要的功能列表:

##### 高性能

根据代码的复杂程度，对于不太复杂的业务逻辑，它每秒可以处理大约 30，000 个请求

##### 可扩展性

钩子、插件和装饰器使它完全可扩展

##### 基于方案

在内部，Fastify 将一个 JSON 模式编译成一个高性能函数，可用于路由验证和输出序列化

##### 记录

Pino 是一款高性价比的测井仪，用于加快测井速度

##### 开发人员友好的环境

这个框架很有表现力，也很容易上手。此外，它允许开发人员在不牺牲性能或安全性的情况下，快速将小项目扩展到更大的高性能应用程序

##### 打字稿就绪

TypeScript 类型声明文件维护对 TypeScript 社区的支持

## 你应该知道的 5 个重要的 Fastify 插件

除了 Fastify 提供的大量功能，它还有一个强大的插件架构。任何开发人员都可以构建与 Fastify 协同工作的插件，以创建用于引导 API 项目的快速构建块。

由外部开发人员开发的插件属于“社区插件”的范畴，而 Fastify 团队也维护着一些他们自己的插件，他们称之为“核心插件”。然而，重要的是要知道任何社区插件都必须遵循 Fastify 的最佳实践。

使用核心插件的好处是 Fastify 团队会主动维护那些插件，但要记住社区插件可能不会维护。

以下是一些需要注意的重要 Fastify 插件:

*   fastify-auth :由 fastify 团队开发的一个认证插件，允许你快速地将认证逻辑注入到你的 API 路径中
*   fastify-cors :跨源请求对任何应用程序都很重要，fastify-cors 可以帮助你管理这一点，而不需要单独安装一个 [CORS 包](https://www.npmjs.com/package/cors)
*   fastify-jwt :这个插件会用标准的 JSON web 令牌来装饰你的应用程序。Fastify-jwt 在内部使用 jsonwebtoken 包
*   [fastify-nextjs](https://github.com/fastify/fastify-nextjs):[Next](https://github.com/vercel/next.js)是一个在服务器端构建预渲染网站的 React 框架。这个插件帮助你用 Fastify 做同样的事情
*   [fastify-redis](https://github.com/fastify/fastify-redis) :这使您的 fastify 应用程序能够在整个服务器上共享同一个 [Redis](https://redis.io/) 连接

这还不是一个完整的列表。Fastify 有[多种插件](https://www.fastify.io/ecosystem/)可供选择。

## Fastify vs Koa vs Express

当然，每个框架都有其优点和缺点——但是每个框架也有其应用。比较多样的框架并不容易。然而，当你选择一个框架时，我试图选择相关的评估标准。

### 速度比较

[下面是](https://www.fastify.io/benchmarks/)stack share . io 的速度对比概述。

[Express](https://expressjs.com/) : Express 每秒处理的请求量最少。基准测试证明，Express 每秒可以处理 15978 个请求
[Koa](https://koajs.com/) : Koa 是比 Express 更好的选择。它还是一个轻量级框架，每秒处理 54，848 个请求
[Fastify](https://www.fastify.io/) : Fastify 以每秒 78，956 个请求获得了最好的基准测试结果

### 插件生态系统

正如上一节所讨论的，Fastify 是这三个框架中唯一一个有大量插件的 web 框架，它有很大的不同。这对开发人员来说是一大优势，因为他们不必依赖多个框架或包来构建他们的应用程序。Fastify 成为一站式解决方案。

### 类型脚本支持

同样，Fastify 是唯一一个开箱即用的支持 TypeScript 的框架。根据您的 Node.js 版本，您可能需要安装`@types/node`。

## 使用 Fastify 3.0 创建您的第一台服务器

现在，激动人心的部分来了！本教程将指导您使用 Fastify 构建第一台服务器，包括以下几个方面:

1.  装置
2.  运行您的第一台服务器
3.  向您的 API 添加路线
4.  使用 Fastify 插件
5.  添加数据验证功能

准备好了吗？我们开始吧。

## 1.安装和要求

首先，启动一个新的 npm 项目，使用:

```
npm init -y
```

接下来，让我们将 Fastify 依赖项添加到项目中。

使用 npm:

```
npm i fastify --save
```

使用纱线:

```
yarn add fastify
```

确保您的系统上安装了最新版本的 Node.js。可以使用 [nvm](https://github.com/nvm-sh/nvm#installing-and-updating) (节点版本管理器)在不同 Node.js 版本之间快速切换。你还需要一个工具来发送请求，比如[卷毛](https://curl.haxx.se/)或者[邮递员](https://www.postman.com/)。

## 2.创建 server.js

接下来，让我们在项目的根目录下创建一个名为`server.js`的新文件。将以下代码添加到您的`server.js`文件中:

```
const fastify = require('fastify')({ logger: true })

//Add routes here, discussed in further steps

//@Server
fastify.listen(5000, (err) => {
  if (err) {
    console.log(err)
    process.exit(1)
  } else {
    console.log(`Server running, navigate to  https://localhost:5000`)
  }
})
```

如您所见，listen 函数在端口`5000`上启动服务器。它还接受一个接受一个参数的回调，该参数可以包含一个错误对象。这是运行 Fastify API 的最基本的服务器设置。

如果您想尝试这个基本设置，您可以使用`node`命令来运行`server.js`文件，如下所示:

```
node server.js 
```

这将在地址 [http://localhost:5000](http://localhost:5000) 上启动您的服务器。如果您尝试导航到该地址，您会看到一条错误消息，指出该路由不存在，因为我们尚未定义任何路由。现在我们需要添加一些简单的 [CRUD 路线](https://blog.logrocket.com/crud-with-node-graphql-react/)。

## 3.添加 CRUD 路线

让我们在应用程序中添加一些基本的 CRUD 路径。首先，让我们添加一个 GET 路线。

### 3.1 获取路线

假设我们有一个类型为数组的堆栈对象。首先，我们想添加一个 GET route 来检索这个数组的内容。为此，我们可以使用 Fastify 对象来定义一条`get`路线。第一个参数接受我们希望将路由附加到的路径，第二个参数接受一个回调，该回调向您的客户端发回一个回复。

```
const stack = []

//@Routes
fastify.get('/getStack', (request, reply) => {
  reply.send(stack)
})
```

### 3.2 邮寄路线

接下来，让我们尝试使用 POST route 向堆栈数组添加项。这样，我们就可以根据我们的请求发送数据。这里我们期望用户发送一个 JSON 对象，带有一个名为`item`的参数。我们将这个`item`压入堆栈数组。现在我们可以使用回调函数`request`的第一个参数，它包含与 POST 请求一起发送的数据。

```
fastify.post('/addItem', (request, reply) => {
    const item = request.body.item
    stack.push(item)
    reply.send(stack)
})
```

同样的原则也适用于其他路由方法，如 PUT、DELETE、HEAD、PATCH 和 OPTIONS。关于[路线选项](https://www.fastify.io/docs/latest/Routes/#routes-option)的更多信息可以在 Fastify 文档中找到。

### 3.3 最终路由代码

您的最终路由代码应该如下所示:

```
const fastify = require('fastify')({ logger: true })

const stack = []

//@Routes
fastify.get('/getStack', (request, reply) => {
  reply.send(stack)
})

fastify.post('/addItem', (request, reply) => {
    const item = request.body.item
    stack.push(item)
    reply.send(stack)
})

//@Server
fastify.listen(5000, (err) => {
  if (err) {
    console.log(err)
    process.exit(1)
  } else {
    console.log(`Server running, navigate to  https://localhost:5000`)
  }
})
```

现在让我们试试我们创建的代码。首先用`node server.js`启动服务器。接下来，访问下面的路由[http://localhost:5000/get stack](http://localhost:5000/getStack)，它应该会返回一个空的数组对象。

让我们使用 cURL 向堆栈中添加一个条目。我想在堆栈中添加一个苹果。因此，我发送一个带有键`item`和值`apple`的 JSON 对象。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
curl --header "Content-Type: application/json" --request POST --data '{"item": "apple"}' http://localhost:5000/addItem 
```

如果您再次访问[http://localhost:5000/get stack](http://localhost:5000/getStack)，您会注意到堆栈数组中填充了`apple`项。

一切都好吗？让我们添加一个插件！

## 4.向您的 Fastify API 添加插件

为了演示添加和使用 fastify 插件是多么容易，让我们安装 [fastify-routes](https://github.com/fastify/fastify-routes) ，这使我们能够使用 Fastify 实例检索所有注册路线的地图。

首先，从 CLI 安装 Fastify-routes 依赖项:

```
npm i fastify-routes 
```

安装插件后，在注册任何路由之前，通过包含插件来注册插件。

下面是包含`fastify-routes`插件的`server.js`文件的一个片段。我还添加了一个`console.log`语句，展示了如何使用插件返回所有注册的路线。

```
const fastify = require('fastify')({ logger: true })
fastify.register(require('fastify-routes')) // Add and register plugin

const stack = []

//@Routes
fastify.get('/getStack', (request, reply) => {
    reply.send(stack)
})

fastify.post('/addItem', (request, reply) => {
    const item = request.body.item
    stack.push(item)
    reply.send(stack)
})

//@Server
fastify.listen(5000, (err) => {
    console.log(fastify.routes) // Log all registered routes
    if (err) {
        console.log(err)
        process.exit(1)
    } else {
        console.log(`Server running, navigate to  https://localhost:5000`)
    }
})
```

现在，当您使用`node server.js`启动服务器时，您的 CLI 将打印所有注册的路由。

这个插件演示了在你的服务器上添加插件是多么容易。你不需要初始化它们。构成您的服务器的`fastify`对象也作为所有注册插件的父对象，您可以从这个`fastify`对象直接调用它们。

最后，让我们为我们的服务器添加一些基本的数据验证。

## 5.添加数据验证

作为本教程的最后一部分，让我们为您的路径添加数据验证。具体来说，我们希望为之前创建的 POST 路由添加验证。让我们验证一下 body 对象是否包含了`item`属性，数据类型是否应该与`string`类型相匹配。

幸运的是，Fastify 允许我们为路由定义验证模式。下面是一个验证`item`属性是否存在并包含一个`string`的例子。此外，我们告诉 Fastify 服务器，我们不允许使用`additionalProperties: false`设置在 body 对象上添加任何附加属性。

您还可以定义一个响应属性来描述请求成功时的响应。

下面是添加数据验证选项后的完整代码。不要忘记添加`itemValidation`作为 POST 路线的第二个参数。

```
const fastify = require('fastify')({ logger: true })
fastify.register(require('fastify-routes'))

const itemValidation = { // Define validation
    schema: {
        body: {
            type: 'object',
            additionalProperties: false,
            required: [
                'item'
            ],
            properties: {
                item: { type: 'string' }
            }
        },
        response: {
            201: {
                type: 'object',
                properties: {
                    item: { type: 'string' }
                }
            }
        }
    }
}

const stack = []

//@Routes
fastify.get('/getStack', (request, reply) => {
    reply.send(stack)
})

fastify.post('/addItem', itemValidation, (request, reply) => { // Add validation options to POST route
    const item = request.body.item
    stack.push(item)
    reply.send(stack)
})

//@Server
fastify.listen(5000, (err) => {
    console.log(fastify.routes)
    if (err) {
        console.log(err)
        process.exit(1)
    } else {
        console.log(`Server running, navigate to  https://localhost:5000`)
    }
})
```

让我们通过向我们的服务器发送相同的请求，添加`apple`项，再次尝试代码。这个请求应该被成功执行。

```
curl --header "Content-Type: application/json" --request POST --data '{"item": "apple"}' http://localhost:5000/addItem 
```

接下来，让我们尝试发送一个包含空对象的项目，这样我们就可以测试服务器是否拒绝请求。将以下请求发送到您的服务器，以验证数据验证实施。

```
curl --header "Content-Type: application/json" --request POST --data '{"item": {}}' http://localhost:5000/addItem 
```

服务器应该向您发送以下错误消息:

```
{"statusCode":400,"error":"Bad Request","message":"body.item should be string"} 
```

一切都好吗？恭喜你！您已经成功完成了您的第一个 Fastify API 服务器。

## 结论

我希望您喜欢使用 Fastify 构建一个简单的 CRUD API，同时实现数据验证和添加插件。

还有更多的插件，所以一定要看看 Fastify 插件生态系统看看有什么可用的。插件有助于快速引导你的 API，消除从头构建 API 的需要。

请随意查看 Fastify 提供的以下概念:

就这样，伙计们！完整的代码可以在我的 GitHub 库上找到。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)