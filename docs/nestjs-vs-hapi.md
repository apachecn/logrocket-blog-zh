# NestJS vs. Hapi - LogRocket Blog

> 原文：<https://blog.logrocket.com/nestjs-vs-hapi/>

NestJS 和哈比神都是 Node.js 框架，这就是为什么许多开发人员质疑在他们的项目中使用哪一个。这两个框架有很多明显的不同，其中最重要的是 NestJS 对 TypeScript 的内置支持。

此外，NestJS 结合了面向对象编程、函数式编程和函数式反应式编程的元素。哈比神和 NestJS 还有其他几个关键的区别，我们将在本教程中讨论。我们开始吧！

## NestJS 概述

NestJS 是一个用于使用 Node.js 创建可伸缩、高效的服务器端应用程序的框架。它使用支持 TypeScript 的渐进式 JavaScript，并结合了面向对象编程、函数式编程和函数式反应编程的特性。

NestJS 使用强大的 HTTP 服务器框架，如 Express 和 Fastify，它们提供了对主要 Node.js 框架的一定程度的抽象。但是，它们也直接向开发人员公开它们的 API，允许开发人员使用底层平台可用的大量第三方模块。

## NestJS 用例

*   单页面应用程序:NestJS 提供了 [serve-static](https://www.npmjs.com/package/@nestjs/serve-static) 包，它允许你尽可能快地构建单页面应用程序
*   可重用的应用程序:由于 NestJS 支持和实现面向对象的编程，所以构建可重用的 web 应用程序更容易
*   中间件应用程序:`@nestjs/common`包允许您在 web 应用程序中实现中间件
*   RESTful API:NestJS 在很大程度上基于 Angular，因此使用类似 Angular 的模块、服务、控制器、管道和装饰器，这使得它非常适合开发可伸缩、可测试、松散耦合且易于维护的 RESTful API
*   使用浏览器服务和访问静态文件:serve-static 包支持模板引擎，如手柄和 [EJS，它们允许你轻松地提供 HTML 模板](https://blog.logrocket.com/how-to-use-ejs-template-node-js-application/)
*   企业 web 应用程序:NestJS 应用程序可伸缩，是构建大型企业级应用程序的理想选择
*   电子商务 web 应用程序:轻松地将 NestJS 与 React、Angular 和 Vue 等前端框架结合起来，构建电子商务网站

## 谁使用 NestJS？

在撰写本文时，[据报道，超过 285 家公司在其技术栈中使用 NestJS](https://stackshare.io/nestjs) ，包括 Postclick、Quero、Kevin、Ubuddy、Our Stack、Frontend 等等。

## 什么是哈比神？

哈比神，简称 HTTP API，是一个用于开发可扩展网络应用的开源框架。哈比神最基本的用例之一是构建 REST APIs。您可以使用哈比神构建 API 服务器、网站和 HTTP 代理应用程序，它允许开发人员花费更少的时间建立基础架构，而将更多的时间用于编写可重用的应用程序逻辑。

## 哈比神用例

*   网络应用程序:哈比神是尽快构建网络应用程序的理想选择，无需花费时间构建基础设施
*   单页面应用程序的服务器组件:使用哈比神和前端框架，如 Backbone、React 和 Angular，创建令人印象深刻的单页面应用程序
*   基于 HTTP 的软件应用程序:哈比神有一个 [h2o2 插件](https://github.com/hapijs/h2o2)，它提供了一个处理程序类型，用于将 HTTP 请求代理到其他端点
*   电子商务 web 应用程序:哈比神可以与 React、Angular 或 Vue 等前端框架结合起来构建电子商务网站
*   用于 web 应用程序的 JSON API:哈比神是构建 JSON API 的经典选择，可以轻松实现路由、输入和输出验证、身份验证和缓存
*   静态或数据驱动的网站:哈比神集成了几种 HTML 模板语言，包括 Handlebars 和 Jade，使得从动态数据生成 HTML 文档变得简单。哈比神还简化了应用程序的部署，这些应用程序使用基于 cookie 的身份验证和会话来保持应用程序在页面请求之间的状态一致

## 谁使用哈比神？

在撰写本文时，据报道约有 78 家公司在他们的技术栈中使用哈比神，包括 TechStack、Commercetools、Brainhub、Brainhub、Beam 和 Platform。

## 巢状美国。哈皮

现在，让我们比较一下内斯特和哈比神的特点，以便更好地了解他们的区别、弱点和优势。

### 表演

性能是任何好框架的关键特性之一，就性能而言，NestJS 在开发人员中很受欢迎。由于 NestJS 与 Node.js 以及 Express 和 Fastify 等模块相集成，它可以在节省时间的同时将开发人员的生产力和应用程序性能提高四倍。

NestJS [代码检查基准](https://github.com/nestjs/nest/runs/482105333)显示，nestjs-fastify、NestJS 和 FastifyAdapter，“Hello，World！”应用程序每秒处理大约 30，001 个请求。另一方面，哈比神“你好，世界！”application 每秒处理 29998 个请求，Express 每秒处理 38510 个请求，比 nest-fastify 快。

### 流行

NestJS 和哈比神都是最流行的 Node.js 框架，但是其中一个肯定比另一个更流行。在撰写本文时，NestJS 在 [GitHub](https://github.com/nestjs/nest) 上有超过 48，500 颗星，在 [npm](https://www.npmjs.com/package/@nestjs/core) 上有超过 1，400，000 的周下载量，以及 59 个包依赖者。

另一方面，哈比神有超过 [13，900 GitHub 明星](https://github.com/hapijs/hapi)，在 [npm](https://www.npmjs.com/package/@hapi/hapi) 上有 563，000 的周下载量，以及 547 个软件包依赖者。

### 应用架构

应用程序架构描述了用于设计和构建应用程序的模式和技术。

NestJS 提供了一个现成的应用程序架构，称为控制器-提供者-模块，使开发人员和团队能够构建易于测试和维护的应用程序。

另一方面，哈比神支持 MVC、[模型视图控制器、](https://blog.logrocket.com/dont-underestimate-the-model-in-mvc/)架构，这是基于配置的。这种类型的架构模式有助于 web 应用程序的可伸缩性。

哈比神提供了一个控制器来管理数据模型或业务逻辑与视图组件或表示层之间的连接。

### 可量测性

可伸缩性是系统通过向系统添加资源来处理不断增长的工作量的属性。NestJS 使用可扩展的 HTTP 框架，如 Express，它源自 Node.js 的非阻塞特性。这一点，加上其开箱即用的应用程序架构，允许开发人员创建高度可扩展的应用程序。

开发哈比神时考虑了可伸缩性，因为它是为企业应用程序开发的。当哈比神应用程序被扩展时，哈比神服务器将业务逻辑分解成微服务，便于服务器管理。

### 数据库支持

NestJS 与数据库无关，它允许您轻松地与任何 SQL 数据库集成，包括 MySQL、Oracle、SQL Server、Postgres 或任何 NoSQL 数据库，如 MongoDB、DynamoDB 等。，只需在应用程序中安装数据库驱动程序。

哈比神还支持不同的数据库管理系统，如 MariaDB、MongoDB、SQLite、PostgreSQL 和 NoSQL。它通过 API 和数据缓存实现数据库连接，以减少负载。

### 嵌套路由

默认情况下，NestJS 支持嵌套路由。通过连接在`@Controller`装饰器中为控制器声明的前缀，可以很容易地对一组相关的路由进行分组。然后，可以使用 NestJS 方法装饰器(`@Get`、`@Post`、`@Put`、`@Delete`等)。)来定义每个端点的路径信息。

默认情况下，哈比神不支持嵌套路由。但是，您可以稍微调整一下您的项目，将一组相关的路线分组，并减少重复的代码。

## 创建项目

我们来看一个简单的“你好，世界！”两种框架中的服务器。NestJS 和哈比神使用命令行界面创建应用程序的方法相似。

### NestJS 项目

要创建 NestJS 服务器，您需要使用以下命令安装 NestJS CLI:

```
npm install @nest/cli

```

然后，用下面的命令创建一个 NestJS 应用程序:

```
nest new project-name

```

上面的代码会生成一个`project-name`目录和节点模块。它将安装一些其他样板文件，并创建一个包含几个核心文件的`src/`目录，如下所示:

*   `app.controller.spec.ts`
*   `app.controller.ts`
*   `app.module.ts`
*   `app.service.ts`
*   `main.ts`

最后，使用下面的命令运行服务器:

```
npm start:dev

```

### 哈比神项目

从命令行界面创建哈比神应用程序类似于创建 NestJS 项目。首先，您需要使用以下命令获得哈比神 CLI:

```
npm install -g hapi-cli

```

然后，运行下面的命令创建一个新项目:

```
hapi my_app

```

现在，将目录更改为项目文件夹，并安装所需的依赖项:

```
cd my_app && npm install

```

## 应该选择哪个框架？

每个框架都有其优点和缺点。这完全取决于您想要什么以及您打算构建的应用程序的类型。尽管 NestJS 比哈比神有很多优势，但这并不意味着哈比神对于某些用例来说是不理想的。我不会建议你选择哈比神而不是内斯特，反之亦然。如果你有一个应用程序需要你使用哈比神，那就去做吧。这同样适用于 NestJS。

## 结论

在本文中，我们比较了两个主要的 Node.js 框架，哈比神和 NestJS，基于它们的用例、性能、流行程度、架构、可伸缩性、嵌套路由支持和数据库支持。我们还学习了如何用这些框架创建一个基本项目。请随意选择最适合您需求的框架。我希望你喜欢这篇文章！编码快乐！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)