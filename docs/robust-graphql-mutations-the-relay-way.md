# 健壮的 GraphQL 改变了中继方式

> 原文：<https://blog.logrocket.com/robust-graphql-mutations-the-relay-way/>

当您第一次学习 GraphQL 时，您可能会陷入一些与 API 突变设计相关的基本陷阱，这些陷阱可能会在以后反噬您。

幸运的是，如果您在设计 GraphQL 变体时遵循一些简单的原则，您将会处于良好的状态。

在本文中，我们将浏览脸书的 [Relay API spec for mutations](https://relay.dev/docs/en/graphql-server-specification#mutations) ,并浏览针对您的突变需要遵循的最佳实践(无论您是否在前端使用 Relay)。

### 为每个突变使用唯一的输出类型

起初，在 GraphQL 中对资源进行突变直接返回该资源似乎很直观。

然而，这导致了脆弱的突变，当您的需求改变时，它不能扩展以将元数据添加到响应或其他资源中。

相反，Relay 建议每个变异都有自己独特的输出类型，并带有后缀`Payload`。

例如，如果您有一个名为`CreateUser`的突变，它的输出类型将被称为`CreateUserPayload`。

让我们通过一个例子来更清楚地理解这一点。

假设我们正在构建一个简单的博客 API。

首先，您可能会尝试进行如下所示的`CreatePost`突变:

```
mutation CreatePost(title: String!, body: String!): Post
```

这看起来很简单:我们的变异只需要一个标题和主体，并直接返回一个`Post`对象。

当我们意识到我们还想添加当前的查看器对象来刷新查看器主屏幕上的文章列表时，问题就来了。

目前，没有办法向这个输出添加更多的字段，因为输出类型是`Post`，这可能在许多其他地方使用。

通过不为这个变异使用唯一的输出类型，变异的输出被锁定到`Post`中的任何字段。

让我们用一个继电器风格的输出类型来看看这个相同的变化。

现在，我们的突变看起来像这样:

```
mutation CreatePost(title: String!, body: String!): CreatePostPayload!

type CreatePostPayload {
  post: Post
}
```

使用中继样式的输出类型可以很容易地向输出中添加更多的字段。

如果我们想添加当前的查看器，我们可以这样添加:

```
mutation CreatePost(title: String!, body: String!): CreatePostPayload!

type CreatePostPayload {
  post: Post
  viewer: Viewer!
}
```

这将允许我们发出这样的突变:

```
mutation {
  createPost(title: "My New Post", body: "New Post Body") {
    post {
      id
      name
      body
    }
    viewer {
       dashboardPosts {
         id
         name
       }
    }
  }
}
```

同样，如果我们想在突变响应中添加更多字段，也没有问题——这种突变是经得起未来考验的！

### 对每个突变使用单一的、唯一的输入类型

虽然对于每个变异来说，拥有一个唯一的输出类型并不重要，但是对于每个变异来说，拥有一个名为`input`的输入参数也是一个最佳实践。

对于继电器，该参数用突变名称和后缀`Input`表示。

随着您的变化越来越大，并接受越来越多的参数，在开发过程中保持客户端 GraphQL 同步会很快变得困难。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

像这样将一个大的参数列表移动到一个输入对象中被称为[参数对象模式](http://wiki.c2.com/?ParameterObject)，可以帮助简化您的代码。

例如，对于上面的`CreatePost`变异，我们可以使用一个`CreatePostInput`类型重写输入，如下所示:

```
mutation CreatePost(input: CreatePostInput!): CreatePostPayload!

type CreatePostInput {
  title: String!
  body: String!
}
```

这让我们像这样使用我们的突变:

```
mutation createPost($input: CreatePostInput!) {
  createPost(input: $input) {
    post { ... }
  }
}
```

现在，无论我们在`createPost`突变中增加多少字段，上面的 GraphQL 仍然是正确的。

### 具有 clientMutationId 的幂等突变

中继变异规范中比较奇怪的部分之一是参数`clientMutationId`，它在变异的输入和输出类型中都出现了。

这个参数非常强大，因为它允许您的突变成为幂等的。

例如，想象一下在我们上面的例子中，一个客户端发送了一个`createPost`请求，但是在请求期间他们失去了一会儿互联网。客户端如何判断请求是否通过？

当它再次获得网络访问时，它应该重试吗？如果我们发送另一个请求，但是服务器确实收到了前一个请求，我们将会得到一个重复的 post。

幸运的是，我们可以用`clientMutationId`参数解决这个问题。

这要求客户端和服务器都知道这个参数。例如，它允许我们的服务器根据客户端发送的`clientMutationId`缓存每个客户端的响应。

因此，如果服务器看到两个具有相同`clientMutationId`的请求，它将意识到第二个请求是重复的，并再次返回缓存的响应，而不重新应用变异的内容。

这允许客户端获得它需要的响应内容，而不会意外地导致变异在服务器上运行多次。

通过使用这个参数，我们基本上使所有的突变都是幂等的。

中继规范还要求在变异的输出中将相同的`clientMutationId`发送回客户机。

这是为了帮助客户确保他们得到的响应是针对他们请求的突变，以防可能有多个类似的突变快速连续发送。

### 使用动词作为突变名称

虽然 Relay 没有严格执行，但规范建议使用动词来命名您的变体。

突变在您的 API 中执行一项操作，因此使用一个主动词来表示通过调用突变将会发生一些变化是有意义的。

例如，你应该用像`createPost`这样的动词而不是像`postCreation`这样的名词来命名一个突变。

### 标准化实施

坚持使用 Relay 规范的另一个好处是，作为 GraphQL 的通用规范，它很容易实现。

大多数常见的 GraphQL 库都支持 Relay，比如 Python 的 Graphene 或者 Node/JavaScript 的 graphql-js。

这些库使得实现上面描述的模式变得容易，所以您不需要担心记住为每个变异创建单独的命名输入和输出类型，或者添加可选的`clientMutationId`参数。

尽管如此，如果你想使用它，你仍然可以让你的代码知道`clientMutationId`。

### 结论

即使您在前端使用 Apollo 或其他 GraphQL 客户端，Relay 也是设计 GraphQL API 的一个很好的规范。

它得到了 GraphQL 和 Relay 的创造者脸书的官方支持。

它们是行业标准，Relay 强加于突变的设计意见将有助于确保您的突变是健壮的，并且随着 API 的增长是经得起未来考验的。

变异快乐！

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.