# GraphQL 片段解释

> 原文：<https://blog.logrocket.com/graphql-fragments-explained/>

# GraphQL 片段解释

## 2019 年 10 月 10 日 2 分钟阅读 683

什么是 GraphQL 片段？

## 在这篇文章中，我们将了解 GraphQL 中的**片段**是什么。一个 [GraphQL 片段](https://graphql.org/learn/queries/#fragments)是查询的一个可重用部分。在 GraphQL 中，您可能会遇到需要在不同的查询中查询相同字段的情况。如果您注意到您的查询在多个区域中有许多重复的字段，您可以将它们合并到一个称为片段的可重用单元中。

GraphQL 片段允许您构建多个字段，并将它们包含在多个查询中。你可以把它看作编程语言中的函数，是可重用的单元。

GraphQL 片段是一个 GraphQL 查询的可重用单元，它创建了一个共享的查询逻辑。

GraphQL 片段的组成部分

## 让我们用下面的示例结构来看看 GraphQL 片段的不同部分:

片段由三个独特的部分组成:

```
fragment Name on TypeName {
  field1
  field2
  field3
}
```

**Name** :这是片段的唯一名称(每个片段可以有自己的名称)

*   **TypeName** :片段将要使用的对象的类型。这表明这个片段是在 GraphQL 模式的哪个嵌套对象上创建的
*   **主体**:最后一部分是片段的主体。片段的主体定义了将被查询的字段
*   使用 GraphQL 片段的好处

## 那么，为什么 GrapQL 查询中的片段很酷呢？

**可重用性**——使用片段，您可以将您的查询组织成可重用的单元

*   **缓存**–[graph QL 客户端](https://www.apollographql.com/docs/react/caching/cache-interaction/)利用片段来提供缓存选项
*   创建 GraphQL 片段

## 让我们通过一些例子来学习如何创建 GraphQL 片段。对于这篇博文中的例子，我使用的是 [GitHub 的公共 API](https://developer.github.com/v4/) 并针对它编写查询。您可以登录您的 GitHub 帐户，并从 [GitHub GraphQL Explorer](https://developer.github.com/v4/explorer/) 执行查询。

注意，我们多次查询**所有者**字段中的相同字段。这是创建片段的好地方:

我们可以重写我们的查询来使用一个片段。片段是用关键字 fragment 创建的。

```
{
  googleRepo: repository (owner:"google", name:"WebFundamentals") {
    name
    owner {
      id,
      avatarUrl
      resourcePath
      url
    }
  }
  facebookRepo: repository (owner:"facebook", name:"react") {
    name
    owner {
      id,
      avatarUrl
      resourcePath
      url
    }
  }
}
```

我们可以创建一个名为`ownerInfo`的片段。在创建片段时，我们必须让 GraphQL 知道它是在哪个字段上创建的。在我们的例子中，我们在`RepositoryOwner`字段上创建片段。在我们的片段定义中，我们可以包含我们在`RepositoryOwner`对象上查询的所有字段。我们将`id`、`avatarUrl`、`resourcePath`和`url`作为字段添加到我们的片段中。

使用 GraphQL 片段

```
// fragment ownerInfo for RepositoryOwner fields
fragment ownerInfo on RepositoryOwner {
  id
  avatarUrl
  resourcePath
  url
}
```

## 然后，您可以在查询中使用我们在前面的示例中创建的片段，方法是使用…运算符并提供片段的名称，如下所示:

下面显示的代码片段是使用片段后的 JSON 响应。注意，使用片段不会对返回的响应有任何改变。片段只是让你的查询简洁、易读和可重用。它对返回的查询响应没有影响。

```
// GraphQL Query with fragments

{
  googleRepo: repository(owner: "google", name: "WebFundamentals") {
    name
    owner {
      ...ownerInfo //fragment
    }
  }
  facebookRepo: repository(owner: "facebook", name: "react") {
    name
    owner {
     ...ownerInfo //fragment
    }
  }
}
```

结论

```
// GraphQL JSON Response

{
  "data": {
    "googleRepo": {
      "name": "WebFundamentals",
      "owner": {
        "id": "MDEyOk9yZ2FuaXphdGlvbjEzNDIwMDQ=",
        "avatarUrl": "https://avatars1.githubusercontent.com/u/1342004?v=4",
        "resourcePath": "/google",
        "url": "https://github.com/google"
      }
    },
    "facebookRepo": {
      "name": "react",
      "owner": {
        "id": "MDEyOk9yZ2FuaXphdGlvbjY5NjMx",
        "avatarUrl": "https://avatars3.githubusercontent.com/u/69631?v=4",
        "resourcePath": "/facebook",
        "url": "https://github.com/facebook"
      }
    }
  }
}
```

## 我希望您喜欢编写一些 GraphQL 查询。如果你有兴趣进一步了解 GraphQL，并对 GraphQL 有一个全面的了解，你可以在 Pluralsight 上查看我的课程 [GraphQL: The Big Picture](https://app.pluralsight.com/library/courses/graphql-big-picture/table-of-contents) 。

资源:
[https://graphql.org/](https://graphql.org/)
[https://graphql.org/learn/](https://graphql.org/learn/)
[https://www.graphql.com/](https://www.graphql.com/)

如果您有任何意见，请在下面发表您的意见，并与您的团队和朋友分享这篇文章。

监控生产中失败和缓慢的 GraphQL 请求

## 虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

.

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.