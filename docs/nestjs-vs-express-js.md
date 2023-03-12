# NestJS vs. Express.js 

> 原文：<https://blog.logrocket.com/nestjs-vs-express-js/>

当我们想要构建一个带有框架的大型应用时，通常首先想到的就是 Express.js。在过去的几年中，Express 在开发人员中广受欢迎。NestJS 是另一个流行的框架，也可以很好地完成这项工作。

由于 Nest 和 Express 都被用作 Node.js 应用程序的框架，对于开始一个新的 Node 项目的开发人员来说，哪个选项更好一直是一个激烈的争论。在本文中，我们将比较 NestJS 和 Express.js，以帮助开发人员做出明智的决定。

我们将涵盖的内容:

## 什么是 NestJS？

NestJS 是一个用于构建服务器端应用程序的 Node.js 框架。它基于 TypeScript 和 JavaScript。

这个框架的灵感来自 Angular，所以你在 Angular 中发现的大部分东西也可以在 Nest 中找到，包括提供者、中间件、组件和服务。可以肯定地说，对于任何类型的项目，Angular 开发人员都可以轻松地学习 Nest。

默认情况下，Nest 使用 Express HTTP 框架。然而，Nest 是平台不可知的，这意味着它可以与任何节点 HTTP 框架一起工作。比如可以可选的将[配置成使用 Fastify](https://www.npmjs.com/package/@nestjs/platform-fastify) ，可以改善节点框架。

关于 NestJS 的一个很酷的事情是，在 Express 中支持的任何东西(即 Express 函数)在 Nest 中也是支持的。

### NestJS 核心组件

让我们回顾一下 NestJS 的一些核心组件。

模块是一个已经用`@Module()` decorator 注释过的类。Nest 使用由`@Module()` decorator 提供的元数据来组织应用程序结构。

每个 NestJS 应用程序至少包含一个模块，称为根模块。Nest 使用根模块作为起点来解析应用程序的结构和关系。

动态模块是嵌套模块系统的一个强大特性。这个特性允许您轻松地创建可定制的模块，这些模块可以动态地注册和配置提供程序。

提供者非常重要，因为 Nest 非常依赖它们来创建不同对象之间的关系。提供者可以作为依赖项注入。

像服务、存储库和助手这样的类可以被视为提供者；只需添加 Nest 中的`@Injectable()`装饰器就可以解决问题，这使得依赖关系管理变得极其简单。

当接收到 HTTP 请求时，路由机制将其路由到 NestJS 中正确的控制器。控制器处理传入的请求并响应应用程序的客户端。

### 如何安装 NestJS

Nest 包含了一个优秀的 CLI，使得搭建 Nest 应用程序变得简单。
在终端或命令提示符下，键入以下内容:

```
npm i -g @nestjs/cli

```

让我们使用`cd`命令切换到我们想要构建应用程序的目录。运行以下命令:

```
nest new nest-blog-api
cd nest-blog-api
npm run start:dev

```

在任何浏览器中进入 [http://localhost:3000](http://localhost:3000) 。您应该会看到一条“Hello World”消息。

### NestJS 的特点

让我们探索一下使 NestJS 与众不同的一些显著特征。

使用 Node 和 Express 非常适合构建一个小的、轻量级的应用程序，其中的代码非常好并且易于阅读。然而，随着事情开始变得复杂，并且您向应用程序添加了更多的特性，您的代码将开始变得有点混乱并且难以管理。

这就是 NestJS 的用武之地。Nest 可以将您的应用程序组织成自包含的模块，每个模块都有自己的职责。一个模块可以包含相关的控制器和服务，并保持它们与应用程序的其余部分完全隔离。

[Nest 还支持依赖注入](https://blog.logrocket.com/top-five-typescript-dependency-injection-containers/)。使用依赖注入意味着您不需要在代码中对组件、服务和中间件之类的东西有硬依赖。

异常过滤器是另一个很酷的嵌套特性。所有应用程序都会不时遇到异常。如何处理异常真的很重要，而且 Nest 很方便地为您解决了所有这些问题。

Nest 包括一个异常层，负责处理应用程序中所有未处理的异常。当您的应用程序代码没有处理异常时，该层会捕获它，并自动发送适当的用户友好的响应。

您还可以通过 Nest 轻松获得 MongoDB 支持。很多用 Node 构建的 web 应用使用[MEAN stack，由 MongoDB、Express、Angular 和 Node](https://www.mongodb.com/mean-stack) 组成。访问 Mongo 数据库最流行的库之一是 Mongoose。

您可以使用[NestJS mongose 包](https://www.npmjs.com/package/@nestjs/mongoose)轻松连接到 MongoDB 数据库并使用它来构建可伸缩的应用程序。

最后，正如我们已经提到的，NestJS 默认使用 Express 框架作为请求处理管道。这意味着，如果您已经熟悉了 Express 处理，您将能够在 Nest 中使用您的 Express 中间件。

## 什么是 Express.js？

Express 是一个 Node.js web 应用程序框架，为构建 web 和移动应用程序提供了广泛的功能。它是建立在节点之上的一层，帮助管理服务器和路由。

您可以使用 Express with Node 创建单页、多页或混合 web 应用程序。它支持用于设计 web 应用程序的 [MVC 架构模式:模型、视图和控制器。](https://blog.logrocket.com/building-structuring-node-js-mvc-application/)

### 如何安装 Express.js

要在我们的项目中使用 Express，我们首先要安装它:

```
npm install express

```

然后我们想在我们的项目中导入 Express:

```
import express from 'express';

```

接下来，我们需要初始化我们的应用程序，给它一个监听端口，并响应该路径上的请求:

```
const app = express();

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(3000, () =>
  console.log('Example app listening on port 3000!'),
);

```

### Express.js 的特点

让我们回顾一下 Express 的一些功能。

中间件是可以访问数据库、客户端请求和其他中间件的程序组件。它主要负责各种 Express.js 函数的系统化组织。

谈到路由，Express 包括一个复杂的路由机制，使用 URL 来保存网页的状态。

最后，Express 包括模板引擎，使开发人员能够通过在服务器端创建 HTML 模板来为网页创建动态内容。

## NestJS vs Express.js

在本节中，我们将直接比较 Nest 和 Express 的各个方面，包括每个方面的示例用例。

### NestJS vs. Express.js:固执己见和不固执己见

Nest 是一个观点很强的框架。它遵循“约定优于配置”的[设计范例，](https://en.wikipedia.org/wiki/Convention_over_configuration)允许开发人员以特定的方式使用标准工具和代码，从而减少对显式配置的需求。

另外，NestJS 是[基于角度的，所以 TypeScript 是它的主要编程语言](https://blog.logrocket.com/angular-11-what-to-expect/)，虽然你也可以使用 JavaScript。TypeScript 的使用确保了应用程序的可靠性和无缺陷性。

Express.js 是一个没有强烈观点的框架——换句话说，没有主见。这意味着它没有一套预定义的规则可以遵循。开发人员经常利用这个机会试验不同的场景，并根据需要编写代码。

### NestJS vs. Express.js:人气

说到哪种框架最受欢迎，Express.js 独占鳌头。Github 上有超过 [57，000 颗星星，Express 是最受欢迎的框架。它标榜自己是一个快速、无主见、极简主义的框架。](https://github.com/expressjs/express)

Nest.js 紧随其后。它在 Github 上拥有超过[47000 颗星，在 Github stars](https://github.com/nestjs/nest) 的 [top Node.js 框架中排名第二。](https://github.com/VanoDevium/node-framework-stars)

### NestJS vs. Express.js:性能

Express 可以使用异步编程执行多个彼此独立的操作。默认情况下，Nest 使用 Express 框架。

然而，Nest 还提供了一种替代方法，可以将底层框架从 Express 更改为 Fastify，从而显著提高性能。

### NestJS vs. Express.js:架构

Nest 使用控制器、提供者和模块提供了一个现成的应用程序架构。这使得开发人员和团队能够创建易于测试和维护的应用程序。

[Express 不需要特定的结构](https://blog.logrocket.com/organizing-express-js-project-structure-better-productivity/)，可以为小型或者一个人的开发团队提供灵活性。然而，随着团队规模或应用程序复杂性的增长，这可能会成为一个问题，特别是当开发人员在应用程序的不同方面工作时。

### NestJS 与 Express.js:单元测试

Nest CLI 附带了一个基于 Jest 的默认测试环境。创建服务、拦截器或控制器时，CLI 会创建一个规范文件。该文件包括自动生成的测试平台代码，消除了开发人员为单元测试编写额外代码的需要。

在 Express 中，单元测试需要构建不同的代码，这需要时间，并且可能会降低应用程序的生产率。

### NestJS 与 Express.js:用例

让我们来看看 Nest vs. Express 的一些用例。

使用 NestJS 构建的一些例子包括企业级 web 应用程序和电子商务应用程序。

NestJS 应用程序伸缩性很好，非常适合大型企业级应用程序。阿迪达斯、罗氏、Trilon 等领先公司使用 Nest T1 并不奇怪。

我们还可以通过将 NestJS 与 React、Angular 或 Vue 等前端框架结合起来，轻松地[构建电子商务网站。](https://blog.logrocket.com/how-build-ecommerce-app-nestjs/)

使用 Express.js 可以构建的其他示例包括金融科技应用程序和流媒体应用程序。

用于支持或促进银行和金融服务的计算机软件或其他技术被称为 fintech。越来越多的金融机构正在创建 fintech 应用程序，Express 是创建高度可扩展的金融应用程序的首选框架。

实时流服务是复杂的，具有多级数据流。要创建这样一个应用，你需要一个健壮的框架，比如 Express.js，它可以有效地处理异步数据流。

## 结论

根据一些开发人员的说法，如果您已经在使用 NestJS，那么您已经走在了游戏的前面。这个框架在早期给你一个显著的优势，并且通过适当地构建你的应用程序，帮助你[把你的节点后端提升到下一个层次](https://blog.logrocket.com/node-back-end-next-level-nestjs/)。

然而，Express.js 是使用 JavaScript 的最好和最受欢迎的后端开发框架之一，并且很可能在一段时间内保持这种状态。你更喜欢哪个选项？在评论区分享你的想法吧！

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.