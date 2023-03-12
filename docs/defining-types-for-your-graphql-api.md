# 为您的 GraphQL API 定义类型

> 原文：<https://blog.logrocket.com/defining-types-for-your-graphql-api/>

GraphQL 是一种用于 API 的查询语言。它是脸书在 2015 年开源的[T3，从那以后作为 REST 的替代品获得了巨大的人气。像](https://github.com/graphql) [PayPal](https://medium.com/paypal-engineering/graphql-a-success-story-for-paypal-checkout-3482f724fb53) 、 [Shopify](https://help.shopify.com/en/api/graphql-admin-api) 、 [GitHub](https://developer.github.com/v4/) 这样的公司，以及许多其他领先的科技公司如今都在使用 GraphQL。创建 GraphQL 是为了在客户机和服务器之间有更好的通信。在这篇博文中，我们将了解 GraphQL 类型以及如何使用它们来构建 GraphQL 模式。

## 强类型架构

GraphQL 有一个[强类型](https://en.wikipedia.org/wiki/Strong_and_weak_typing)模式。这意味着模式充当客户机和服务器之间的契约。这是使用 GraphQL 的最大好处之一。

### 强类型架构的好处

*   **模式充当契约** —对于强类型模式，模式充当客户端和服务器之间的契约。有了这个明确定义的模式，客户机和服务器之间的交互变得更加容易。它减少了使用 API 时客户机/服务器交互中可能出现的任何错误通信和潜在延迟
*   **错误的早期检测** —在 GraphQL 中，您可以在开发阶段在 GraphQL 编辑器中发现大量错误。这是因为，由于模式的强类型特性，传递给 API 的任何不正确的数据都将被标记为错误。这在开发周期中节省了大量的时间和精力
*   **团队独立工作**——因为一切都在模式中明确定义，强类型模式也可以生成客户可以使用的文档，前端和后端团队可以独立工作

## GraphQL 标量类型

[标量类型](https://graphql.github.io/graphql-spec/June2018/#sec-Scalars)是 GraphQL 中的原始数据类型。以下是 GraphQL 支持的原始数据类型:

*   [Int](https://graphql.github.io/graphql-spec/June2018/#sec-Int)–一个有符号的 32 位整数
*   [Float](https://graphql.github.io/graphql-spec/June2018/#sec-Float)–有符号双精度浮点值
*   [字符串](https://graphql.github.io/graphql-spec/June2018/#sec-String)–UTF 8 字符序列
*   [布尔型](https://graphql.github.io/graphql-spec/June2018/#sec-Boolean)–代表真/假
*   [ID](https://graphql.github.io/graphql-spec/June2018/#sec-ID)–唯一标识符

让我们编写一个 GraphQL 对象，它有自己的特征，由项组成，每项都有自己的类型:

```
type BlogPost {
      id: ID
      title: String
      author: String
      numberOfLikes: Int
    }
```

在上面的例子中，我们创建了一个类型为 *BlogPost* 的复杂对象。 *BlogPost* 对象包含 id、文章标题、文章作者和收到的赞数。当您编写 GraphQL 模式时，您要做的第一件事就是定义类型。

由于 GraphQL 是强类型的，并且严重依赖于类型，所以在创建模式之前，就在一开始定义了这些类型。

让我们看看 GraphQL 中更多的类型来进一步理解这一点。

## Enum 类型(在 GraphQL 中)

GraphQL 支持*枚举*类型，它们的工作方式就像在任何其他编程语言中一样。枚举允许您验证任何参数都可以有一个定义的值。枚举也是 GraphQL 中的标量类型。

下面是 GraphQL 中枚举类型的一个示例:

```
enum BlogTopics {
  WEB,
  MOBILE,
  FRONTEND,
  BACKEND, 
  GENERAL
}
```

在这个例子中，我们定义了一个类型为 *BlogTopics* 的枚举。这个枚举可以有五个值。主题可以是 web、移动、前端、后端或常规。 *BlogTopics* 类型允许具有这五个值中的任何一个。

## GraphQL 中的列表类型

GraphQL 模式支持标量类型的扩展，以监听条目。这很简单，只需在类型周围添加方括号，使其成为该类型的列表。例如，如果您想要一个整数列表，您可以将其声明为 **int** ，这表明它是一个列表类型。

让我们给我们最初的 *BlogPost* 类型添加一个列表类型:

```
type BlogPost {
  id: ID
  title: String
  author: String
  numberOfLikes: Int
  comments: [String]
}
```

我们为博客文章评论添加了一个条目，它是一个字符串列表。这意味着它可以将多个评论保存为一个列表。GraphQL 模式中经常使用列表。

## 查询和变异类型

GraphQL 模式可以不仅仅包含标量类型。最常用的两种类型是[查询和变异类型](https://graphql.org/learn/schema/#the-query-and-mutation-types)。

每个 GraphQL 服务都有一个查询类型。这是 GraphQL 模式的入口点。GraphQL 中的查询表示客户端对 GraphQL API 的要求。

要了解更多关于 GraphQL 查询的信息，你可以阅读我写的博客文章,它详细描述了 GraphQL 中查询是如何构造的。

尽管每个 GraphQL 服务都有一个查询类型，但它可能包含也可能不包含变异类型。GraphQL 突变用于添加/更新/删除数据。客户端可以使用可用的突变来更新数据。你可以把它想象成 REST 世界中的 POST 请求。

下面是如何在 GraphQL 模式中定义查询和变异类型:

```
schema {
      query: Query
      mutation: Mutation
    }
```

在 GraphQL 中，查询和变异类型都像任何其他数据类型一样被处理。

让我们看一些例子来更好地理解这些。我将扩展我们之前的类型为 *BlogPost* 的例子，以包括一个查询和变异类型:

```
type Query {
  blog_info: [BlogPost]
}
```

在上面的例子中，我们定义了一个名为 *blog-info* 的查询。查询类型的定义就像 GraphQL 中的任何其他类型一样。至此，我们已经定义了一个新的查询 *blog-info* ，客户端可以用它来检索数据。它将返回一个类型为 *BlogPost* 的数据数组。

类似地，我们也可以定义突变类型:

```
type Mutation {
  add_blog_post: (title: String, author: String, numberOfLikes: int) : BlogPost
}
```

这里的 *add_blog_post* 变异可以用来向 *BlogPost* 添加新条目。它接受*标题、作者、*和 *numberOfLikes* 作为参数。

类似地，您可以为 GraphQL API 定义几个查询和变化。

## 不可为空的字段

GraphQL 类型的酷之处在于，您可以定义类型并决定它们是否可以为空。我调整了我们最初的 *BlogPost* 类型，加入了一个不可空的字段。注意下面有一个感叹号(！)旁边的 *ID* 。这表明 ID 不能为空。

```
type BlogPost {
  id: ID!
  title: String
  author: String
  numberOfLikes: Int
}
```

默认情况下，每个标量类型都可以设置为 null。通过添加感叹号，我们设置了一个规则，即字段不能为空。我喜欢这个特性，因为它在客户机和服务器之间定义了一个清晰的契约，消除了使用 API 时的任何混淆。

## 组合列表和非空

对于更复杂的模式定义，我们可以用多种方式组合列表和非空值。假设您有一个名为`[myList]`的列表类型。以下是将非空值与列表结合的方法。

*   如果您想要一个不能包含任何空项的列表，那么您可以将您的列表声明为`[myList!]`。这意味着列表中的项目不能为空
*   或者，如果您将列表声明为`[myList!]`，并在方括号外加上非空的感叹号，则意味着其他内容。这表示不接受 null，但允许列表为空
*   你也可以将你的列表声明为`[mylist!]!`。这是非 null 的一种更严格的形式。这意味着列表不能包含任何非空项，列表也不能为空

在下面的例子中，注释列表中不能有任何空项:

```
type BlogPost {
  id: ID
  title: String
  author: String
  numberOfLikes: Int
  comments: [String!]
}
```

## 结论

我希望您喜欢学习 GraphQL 类型。如果你有兴趣进一步了解 GraphQL，并对 GraphQL 有一个全面的了解，你可以在 GraphQL 上查看我的课程 [Pluralsight](https://app.pluralsight.com/library/courses/graphql-big-picture/table-of-contents) 。

其他资源:

如果您有任何意见，请在下面发表您的意见，并与您的团队和朋友分享这篇文章。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.