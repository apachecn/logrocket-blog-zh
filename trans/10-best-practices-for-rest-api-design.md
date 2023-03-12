# REST API 设计的 10 个最佳实践

> 原文：<https://blog.logrocket.com/10-best-practices-for-rest-api-design/>

[REST API](https://restfulapi.net) [，](https://restfulapi.net)具象状态转移的首字母缩写，是分布式超媒体系统的一种架构风格。这是一种按照某种协议设计 API 的灵活方法。

REST API 使客户端能够通过传输主要存储在数据库中的数据状态来与服务器通信。因为客户机和服务器是独立工作的，所以我们需要一些接口来促进它们之间的通信。客户端通过 API 向服务器发送请求，服务器以标准化的格式返回响应，如 [JSON](https://www.json.org/) 或 [XML](https://developer.mozilla.org/en-US/docs/Web/XML/XML_introduction) 。

REST APIs 在促进服务器中的通信方面起着至关重要的作用，因此对于开发人员来说，深入了解如何使用它们是至关重要的。容易出错的 API 会给客户端带来巨大的功能问题，并使软件整体吸引力下降。

在本文中，我们将深入探讨设计 REST APIs 的最佳实践，以确保尽可能好的性能。

## 优化 REST API 的最佳实践

### 1.使用 JSON 发送和接收数据

一个设计良好的 REST API 应该总是接受 JSON 格式的数据。

JSON 是一种轻量级数据交换格式，已经成为许多开发人员的标准。它可以在许多技术中使用，并且由于它的轻量级特性，使服务器端的编码和解码变得快速和容易。此外，JSON 可读性强，易于解释。

JSON 的替代方案 XML 并不被很多框架所支持。此外，与 JSON 相比，XML 数据操作可能很麻烦，因为它冗长且难以编写。

为了确保 REST API 使用 JSON 格式，总是将响应头中的 [`Content-Type`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type) 设置为`application/JSON`。大多数后端框架都有内置函数来自动将数据解析为 JSON 格式。

### 2.用名词代替动词

REST APIs 的命名约定很重要，可以避免很多混乱。

我们不应该在我们的 API 端点中使用像`DELETE`、`PUT`或`GET`这样的动词，因为它们类似于标准的 HTTP 请求方法。此外，用于 API 的名词已经完美地描述了被操作的实体。

然而，当我们要引用动词时，我们大多会引用 HTTP 方法，如`GET`、`POST`、`PUT`、`DELETE`。这些类似于发生在数据库层的 CRUD 操作，我们不想将它直接集成到 API 命名中。

假设我们必须检索用户列表。我们将该 API 命名如下:

```
const express = require('express');
const bodyParser = require('body-parser');

const app = express();

app.use(bodyParser.json());

app.get('/user’, (req, res) => {
  const user’= [];
  res.json(user);
});

//Avoid this.
aap.get(‘getUser’, req,res)=>{
  const user’= [];
  res.json(user);
});

```

### 3.集合使用复数

从数据库中检索数据通常需要批量进行，而不是从单个对象中检索，因为大多数操作都是基于列表的。因此，我们应该在 API 中对端点使用复数。这使得我们的 API 和数据库之间保持简单和一致。

例如，如果您正在设计一个 API 来检索数据库中的所有用户，那么您的端点应该如下所示:

```
// (List of users)
https://api.abc.com/users

```

不正确的端点如下所示:

```
https://api.abc.com/user

```

### 4.不要忽略错误处理

每个应用程序都容易出错，这就是错误处理如此重要的原因。一个好的 API 应该总是返回正确的 HTTP 错误代码，正确地解释发生的特定错误的性质。

假设我们想要为一个错误的请求返回一个错误。下面的代码示例是用用户的电子邮件地址注册用户:

```
const express = require('express');
const bodyParser = require('body-parser');

const app = express();

// existing users
const users = [
  { email: '[email protected]' }
]

app.use(bodyParser.json());

app.post('/users', (req, res) => {
  const { email } = req.body;
  const user= users.find(u => u.email === email);
  if (user) {
    return res.status(400).json({ error: 'User already exists' })
  }
  res.json(req.body);
});
app.listen(3000, () => console.log('server started'));

```

我们增加了一个功能，在输入的电子邮件已经被使用的情况下返回一个错误。错误 400 用于错误的请求，通知客户端输入不同的电子邮件地址。详细说明问题的错误消息使得调试更加容易，这也是 REST APIs 广受欢迎的另一个原因。

### 5.过滤数据

任何有经验的开发人员都知道，数据库可能会变得非常大，当它们变得非常大时，就很难管理了。当请求到来时，我们必须只检索我们需要的数据，而不是返回数据库中的所有数据。

为此，我们必须使用过滤器。这只会返回完成请求所需的数据，从而提高性能并在客户端节省大量带宽。随着数据库规模的增长，过滤器变得更加重要。

### 6.保持严密的安全

数据库安全应该是每个 API 开发者最关心的问题之一；一个安全漏洞会给公司带来数百万美元的损失。

由于数据有时包含敏感信息，如信用卡信息，我们必须保持服务器和客户端之间的通信完全保密。 [SSL/TLS](http://%E2%80%8B%E2%80%8Bhttps://blog.logrocket.com/rust-cryptography-libraries-a-comprehensive-list/#TLS) 安全性是确保每个请求和响应都在通道上加密的一种常见且经济实惠的方法。

此外，用户不应该能够访问超过需要的数据。例如，用户 A 访问用户 B 的数据会造成巨大的隐私和安全威胁。解决这个问题的一个方法是为管理员提供他们自己的特权，并分别为用户分配角色。

### 7.自动缓存

重复请求和响应相同的数据会消耗资源，并且是有缺陷的设计的标志。要解决这个问题，请将从 API 获取的数据存储在服务器上，并从那里提供服务。

然而，可能出现的一个问题是数据可能会过时。为此，有几个行业标准的缓存解决方案可以在每次更改后缓存数据，如 [Redis](https://redis.io/) 和[亚马逊 ElasticCache](https://aws.amazon.com/elasticache/) 。

### 8.分配正确的 API 版本

如果您计划对您的 API 进行更改，请始终确保分配正确的版本，以便客户端不会中断。您应该为客户端提供选项，要么继续使用以前的版本，要么尝试新版本。

目标是通过保持更新对客户端是可选的来提供最好的用户体验。通常的做法是在端点前添加一个版本，如下所示:

```
https://api.abc.com/v1/users
https://api.abc.com/v2/users

```

### 9.使用嵌套来显示关系

将相关的端点放在一起创建一个层次结构称为 API 嵌套。例如，如果用户有任何活动订单，那么在`/users/:id`之后嵌套`/order`是管理 API 的好方法:

```
https://api.abc.com/users (list of users)
https://api.abc.com/users/321 (specific user by using filters)
https://api.abc.com/users/321/order (list of the order of the specific user)

```

建议使用较少的嵌套层次，以防止应用程序过于复杂；您可以使用筛选来减少嵌套层数。两级嵌套通常使 API 更简单并完成工作。

### 10.提供 API 文档

提供完整的文档对于任何 API 都是至关重要的。没有清晰的文档，客户就不可能正确使用 API。我们必须确保 API 文档使用 simples 语言，并随着新版本不断更新。

固体 API 文件应包括以下特征:

*   简单的工作和语言
*   每个端点的请求解释、示例和示例响应
*   用不同的编程语言实现 API(如果适用)
*   可能的错误消息已登记

## 结论

随着互联网流量的增加，每天获取的数据越来越多。一个好的 API 是任何系统保持平稳运行的支柱。如果我们在设计 API 时遵循上述实践，结果将是高度功能化和高性能的应用程序。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)