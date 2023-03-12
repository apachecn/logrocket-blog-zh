# GraphQL 与 REST APIs:为什么你不应该使用 GraphQL 

> 原文：<https://blog.logrocket.com/graphql-vs-rest-api-why-you-shouldnt-use-graphql/>

***编者按**:这篇文章最后一次更新于 2022 年 8 月 24 日，以验证代码的准确性。*

如果您想以声明的方式工作，GraphQL 是很棒的，它允许您只选择您需要的信息或操作。然而，根据您的用例、性能需求和对不必要复杂性的容忍度，GraphQL 可能不适合您的项目。

在本文中，我们将回顾为什么您应该考虑使用 REST 架构而不是 GraphQL 的一些原因。我们将讨论使用 GraphQL 的缺点，包括性能问题、GraphQL 模式问题和复杂查询。我们还将概述 GraphQL 的一些常见用例，并提供一些提示来帮助您[决定何时使用 GraphQL 与 REST](https://blog.logrocket.com/graphql-vs-grpc-vs-rest-choosing-right-api/) 。我们开始吧！

## GraphQL 与 REST:差异

GraphQL 和 REST 最大的区别是数据发送到客户端的方式。在 REST 架构中，客户端发出 HTTP 请求，数据作为 HTTP 响应发送，而在 GraphQL 中，客户端通过查询请求数据。

同样值得注意的是，在 REST 中，请求对象的结构是在服务器上定义的。在 [GraphL 中，您在客户端](https://blog.logrocket.com/5-graphql-clients-for-javascript-and-node-js/)上定义对象。

## 什么时候应该使用 GraphQL？

GraphQL 是构建和使用 API 的独特问题的优秀解决方案。当按设计使用时，它可以成为下述用例的理想工具。

### 数据提取控制

GraphQL 被设计成允许客户机只请求它需要的数据。虽然服务器可能能够为单个请求向客户机传送更多的数据，但它只会发送客户机请求的数据。如果您希望客户机控制它需要的数据类型和数量，GraphQL 将是您的项目的理想选择。

### 使用多个数据源

GraphQL 简化了从多个来源或 API 聚集数据，然后在一个 API 调用中将数据解析到客户端的任务。另一方面，像 REST 这样的 API 技术需要多个 HTTP 调用来访问来自多个数据源的数据。

### 缓解带宽问题

对于手机、智能手表和物联网设备等无法处理大量数据的小型设备来说，带宽是一个问题。使用 GraphQL 有助于最小化这个问题。因为 GraphQL 允许客户端指定它需要什么数据，所以服务器不会发送多余的数据，这可能会在带宽有限的情况下降低应用程序的性能。

### 快速原型

GraphQL 公开了允许您访问多个资源的单个端点。此外，资源不会根据您在应用程序中拥有的视图而公开。因此，如果您的 UI 发生变化，例如，需要更多或更少的数据，它不会对服务器产生影响，也不需要对服务器进行更改。

## 什么时候应该使用 REST API？

既然我们已经概述了一些需要使用 GraphQL 的用例，那么让我们来讨论一些为什么您可能想要考虑使用 REST 的原因，回顾一些提示来帮助您确定何时使用每一个。

### GraphQL 性能问题

作为 API 的一种查询语言，GraphQL 赋予了客户执行查询的能力，以获得他们所需要的信息。但是，如果客户端发送一个查询，要求提供许多字段和资源，例如，某个作者对所有书籍发表评论的用户的信息，该怎么办呢:

```
author(id: '1234') {
  id
  name
  books {
    id
    title
    reviews {
      text
      date
      user {
        id
        name
      }
    }
  }
}

```

使用 GraphQL，用户不能简单地运行他们想要的任何查询。必须仔细设计 GraphQL API 这不仅仅是将它放在 REST API 或数据库之上。

对于复杂的查询，REST API 可能更容易设计，因为您可以为特定的需求建立多个端点，并且可以对特定的查询进行微调，以高效的方式检索数据。

这可能有点争议，因为多个网络调用仍然会花费很多时间。但是，如果不小心的话，一些大的查询可能会让您的服务器严重停机。从这个意义上说，GraphQL 最大的优势也可能是它最大的弱点。

在 GraphQL API 中，像 [Dataloader](https://github.com/facebook/dataloader) 这样的工具允许你批处理和缓存数据库调用。但是，在某些情况下，即使这样也不够，唯一的解决方案是通过计算最大执行成本或查询深度来阻塞查询。这些解决方案都取决于您使用的库。

### REST 可以做 GraphQL 做的很多事情

重要的是要记住，GraphQL 是 REST 开发 API 的替代品，而不是替代品。

使用 GraphQL 的主要好处是能够发送只指定您需要的信息的查询，并准确地接收这些信息。然而，您可以使用 REST 达到同样的效果，方法是在 URL 中传递您想要使用的字段的名称，然后自己实现解析并返回逻辑:

```
GET /books/1492030716?fields=title,pageCount

```

幸运的是，这很容易实现，这要归功于许多语言中可用的 JSON API 库。如果您希望在 REST 中使用模式和强类型，您可以使用 [JSON 模式](https://json-schema.org/)；有许多[库实现并支持 JSON 模式](https://json-schema.org/implementations.html)。

如果您想在 REST APIs 中使用查询语言，像 [OData](https://www.odata.org/) 这样的解决方案是一个很好的选择。OData 是开放数据协议的缩写，最初由微软于 2007 年开发。OData 是一个开放的协议，使您能够以简单、标准的方式创建和使用可查询和可互操作的 RESTful APIs。OData 为您提供了一组丰富的查询功能，并因其开源方法和卓越的可伸缩性而迅速赢得市场。

有许多有效的替代方法，特别是对于使用 GraphQL 可能有些过头的小型应用程序和项目。出于同样的原因，您会遇到实现这些库很复杂而使用 GraphQL 更容易的情况，graph QL 本身支持所有这些特性。然而，GraphQL 也可以以自己的方式使事情变得更复杂，这一点我们将在接下来讨论。

### GraphQL 使一些任务变得更加复杂

不建议在简单的应用程序中使用 GraphQL。例如，在一个每次都以相同的方式使用一些字段的应用程序中，使用 GraphQL 会增加更多的复杂性，因为像类型、查询、赋值函数、解析器和更高阶的组件，

从维护的角度来看，这尤其有害。但是，即使使用 GraphQL 是合理的，也可能存在一些复杂性。两个例子包括错误处理和文件上传。

在 REST 中，检查响应状态是了解请求是否成功执行、是否存在服务器错误或者是否没有找到资源的唯一方法。但是，当 GraphQL 中出现错误时，您会得到类似如下的结果:

```
{
  "data": null,
  "errors": [
    {
      "message": "Validation error...",
      "locations": [
        {
          "line": 5,
          "column": 6
        }
      ]
    }
  ]
}

```

您必须解析这条消息才能知道是否有错误，不同的错误可能会有稍微不同的格式或一些自定义字段。

一些库，比如 [Apollo client，帮助处理错误](https://blog.logrocket.com/graphql-local-state-management-apollo/)，但是这不像在 REST API 中那么容易。

文件上传不是 GraphQL 规范的一部分，所以实现工作留给了您。一些选项包括使用:

第三种选择可能是最好的。然而，这意味着添加另一个依赖项来管理您的项目，并且它可能不适用于所有编程语言。

### GraphQL 与。REST: Web cache

我想强调 web 部分，即网络级别的缓存，因为您肯定可以使用 Apollo 客户端的[内存缓存实现](https://www.apollographql.com/docs/react/advanced/caching.html)在数据库级别或客户端级别实现缓存。

例如，假设您有一个在 HTTP 级别实现的缓存，它带有一个存储请求内容的反向代理。这可以减少服务器的流量，或者将频繁访问的信息保存在靠近客户端的位置，如内容交付网络。

由于 REST API 提供了许多端点，您可以轻松地配置 web 缓存来匹配某些 URL 模式、HTTP 方法或特定资源。

在 GraphQL 中，只有一个端点，通常是 HTTP POST 端点，所有的查询都发送到这个端点。因为每个查询都可能不同，所以使用这种类型的缓存比较困难。为了减少 web 服务器的流量，您可以使用带有 [PersistGraphQL](https://www.npmjs.com/package/persistgraphql) 的[持久化查询](https://blog.apollographql.com/persisted-graphql-queries-with-apollo-client-119fd7e6bba5)。请记住，在撰写本文时，这个工具不再被维护，但是，它仍然被广泛使用，并将为本教程的目的而工作。

PersistGraphQL 为 GraphQL 查询分配标识符，生成一个映射查询和标识符的 JSON 文件。有了这个映射，客户机只需将标识符和查询的参数发送给服务器，这样它就可以查找了。然而，这增加了另一层复杂性，并且它只是部分地解决了问题。

### GraphQL 架构

通过允许定义模式，GraphQL 给了你很多好处，比如自动验证和 T2 自省。根据您选择的编写或生成模式的方式，当您有一个不断变化的数据模型时，静态模式可能会成为一个问题，因为客户端得到的响应取决于模式定义和它们进行的查询。

例如，您不能拥有比模式或查询中指定的深度更深的深度，您可以在运行时修改的模式，或[动态类型定义](https://github.com/absinthe-graphql/absinthe/issues/439)。在 GraphQL 中，您可以通过使用代码优先的方法以编程方式构建模式来解决这个问题。设计过程从编写解析器开始，GraphQL 模式的 SDL 版本是以编程方式生成的。

与 GraphQL 相比，REST 中的错误处理更容易。RESTful APIs 在资源方面遵循 HTTP 规范，并为各种 API 请求状态返回各种 HTTP 状态。另一方面，GraphQL 为每个 API 请求返回`200 Ok`状态，包括错误。这使得管理错误变得困难，并且难以与监控工具集成。

## 结论

GraphQL 是一个强大的工具，有很多理由让你选择 GraphQL 而不是 REST。但是，如果您对缓慢的性能和复杂性的容忍度较低，那么您可能希望避开它，考虑使用 REST 架构。

在本指南中，我们讨论了一些使用 GraphQL 可能导致性能问题以及与模式和复杂查询相关的问题的场景。我们还提供了一些技巧来帮助您确定什么时候使用 GraphQL，什么时候使用 REST 架构。

我在这里提出的观点可能并不总是适用，但值得考虑一下，看看是否可以解决。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.