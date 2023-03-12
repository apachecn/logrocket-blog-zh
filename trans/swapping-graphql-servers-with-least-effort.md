# 用最少的努力交换 GraphQL 服务器

> 原文：<https://blog.logrocket.com/swapping-graphql-servers-with-least-effort/>

“针对接口而不是实现进行编码”是指不直接调用功能，而是通过一个契约来调用，该契约列举了所需的输入和预期的输出，并隐藏了实现是如何完成的。这种策略有助于将应用程序从特定的实现、提供者或堆栈中分离出来，并使您能够在它们之间进行交换，而不必更改应用程序代码。

我们可以用 GraphQL 通过使用源代码插件来执行这个策略，就像 Gatsby 举例说明的[。源插件检索特定 GraphQL 服务器所需的数据，允许我们在安装不同的源插件后，用另一个 GraphQL 服务器交换向应用程序提供数据的 graph QL 服务器。](https://www.gatsbyjs.com/docs/creating-a-source-plugin)

![The Gatsby plugin directory](img/5052e4797ec8ef77d5228514e361117b.png)

Gatsby offers over 500 plugins in its directory

源代码插件方法产生了一个可靠的、可扩展的架构，但是它非常耗时，因为我们需要了解 GraphQL 服务器的所有细节。它适用于通用应用程序，如 Gatsby，但对于我们自己从头构建的定制应用程序来说，可能有些矫枉过正，我们可能希望这些应用程序可以重用于需求略有不同的不同客户。

对于这种情况，有一个更简单的策略:我们可以使用 GraphQL 查询作为应用程序和服务器之间的中介，只对 GraphQL 查询执行任何需要的修改，保持业务逻辑不变。

在本文中，我们将探索这种方法。

## 使用 GraphQL 查询作为接口

GraphQL 查询充当客户机和服务器之间的接口。当执行一个查询时，GraphQL 服务器将处理它并将所需的数据返回给客户机。但是数据从哪里来呢？是如何获得的？客户端[不知道，也不关心](https://blog.logrocket.com/client-side-query-customization-in-graphql/)。

![GraphQL query as interface](img/709703dbcdea55b9eae4657aa6eb1006.png)

The GraphQL query acts as an interface between client and server

对查询的响应将具有与查询相同的形状。对于[这个 GraphQL 查询](https://newapi.getpop.org/graphiql/?query=%7B%0A%20%20post(id%3A1)%20%7B%0A%20%20%20%20id%0A%20%20%20%20title%0A%20%20%7D%0A%7D):

```
{
  post(id: 1) {
    id
    title
  }
}

```

…答案将是:

```
{
  "data": {
    "post": {
      "id": 1,
      "title": "Hello world!"
    }
  }
}

```

给定具有不同参数的相同查询，返回的数据将是不同的，但是形状将是恒定的。这意味着只要查询没有改变，应用程序就不需要改变它用来读取和处理数据的逻辑。

然后，GraphQL 查询可以承担源插件的角色，充当应用程序和 GraphQL 服务器之间的中介。只要查询没有改变，哪个 GraphQL 服务器执行查询就无关紧要，我们可以无缝地将一个 GraphQL 服务器与另一个交换。

## 查询依赖于 GraphQL 模式

现在，最后一段有点太乐观了，因为 GraphQL 查询可能需要根据 GraphQL 服务器而改变。更准确地说，查询是基于 GraphQL 模式的，如果[不同的服务器公开不同的模式](https://blog.logrocket.com/anti-patterns-graphql-schema-design/)，那么查询也会不同。

考虑 WordPress 的两个 GraphQL 服务器是如何公开 CMS 的数据模型的。此图显示了由 [WPGraphQL](https://www.wpgraphql.com/) 创建的 GraphQL 模式:

![The schema exposed by WPGraphQL ](img/2b15bd0f9ffbf7da7e010cfcb725798a.png)

The schema exposed by WPGraphQL

另一张图片显示了由 WordPress 的 [GraphQL API 提供的 GraphQL 模式:](https://graphql-api.com)

![The schema exposed by the GraphQL API for WordPress](img/33f2933e486393eb24f7b60a6f4bb6d8.png)

The schema exposed by the GraphQL API for WordPress

在这两个模式中，相同的字段具有不同的名称，并且公开了不同的类型，部分原因是 WPGraphQL 使用了 [GraphQL 游标连接规范](https://relay.dev/graphql/connections.htm)，但是 WP 的 GraphQL API 不使用。因此，在 WPGraphQL 中有效的查询很可能在 WP 的 GraphQL API 中无效，反之亦然。

例如，基于 WPGraphQL 的 [Next.js WordPress starter](https://github.com/colbyfayock/next-wordpress-starter) 包含[这个查询](https://github.com/colbyfayock/next-wordpress-starter/blob/fa87808/src/data/categories.js#L3-L17):

```
{
  categories(first: 10000) {
    edges {
      node {
        categoryId
        description
        id
        name
        slug
      }
    }
  }
}

```

适用于 WP 的 GraphQL API 的等价查询如下:

```
{
  postCategories(limit: 10000) {
    id
    description
    globalID
    name
    slug
  }
}

```

我们可以理解这两个查询之间的差异:

![A graph displaying the differences between the two queries.](img/6d5783f497b2758f47174870cbf38b2b.png)

将 Next.js WordPress starter 中的 WPGraphQL 查询替换为 WP 的 GraphQL API 中的对等查询，这本身是行不通的。这是因为逻辑仍然会根据原始查询的形状和字段从响应中访问数据。运行启动器不出所料地失败了:

![An error is produced when running the starter](img/37d60de8cedb61aef158de744bf2fc28.png)

An error is produced when running the starter

一个可能的解决方案是替换用于检索数据的逻辑。例如，[下面的逻辑](https://github.com/colbyfayock/next-wordpress-starter/blob/274f0917360898c2a1cb67538c3bfb528523afea/src/lib/categories.js#L24):

```
const categories = data?.data.categories.edges.map(({ node = {} }) => node);

```

…可以这样替换:

```
const categories = data?.data.postCategories;

```

但这正是我们想要避免的。我们希望将更改保持在最低限度，只修改 GraphQL 查询“接口”，并保持业务逻辑不变。

幸运的是，通过按照以下步骤修改 GraphQL 查询，可以弥合这些差异:

1.  [保持 GraphQL 查询与应用程序分离](#keeping-graphql-queries-detached-application)
2.  [通过别名修改字段名](#adapting-field-names-via-aliases)
3.  [通过`self`字段](#adapting-shape-response-via-self-field)调整响应的形状

让我们看看如何通过这三个步骤让 Next.js WordPress starter 与 WP 的 GraphQL API 一起工作。

## 1.保持 GraphQL 查询与应用程序分离

从应用程序逻辑中分离 GraphQL 查询包括:

*   将每个 GraphQL 查询(或一组查询)存储在单独的文件中，并将它们全部存储在特定的文件夹中
*   导出查询并将它们导入应用程序

Next.js WordPress starter 已经有了这样的布局。所有的查询都放在`src/data`下的单独文件中(有一些例外)，并且[它们被导出](https://github.com/colbyfayock/next-wordpress-starter/blob/23599b4fba3d54c6d8df06c0609d05df57eef1b9/src/data/categories.js#L3-L17):

```
export const QUERY_ALL_CATEGORIES = gql`
  {
    categories(first: 10000) {
      edges {
        node {
          databaseId
          description
          id
          name
          slug
        }
      }
    }
  }
`;

```

然后，应用程序可以[导入并使用 GraphQL 查询](https://github.com/colbyfayock/next-wordpress-starter/blob/274f0917360898c2a1cb67538c3bfb528523afea/src/lib/categories.js):

```
import { QUERY_ALL_CATEGORIES } from 'data/categories';

export async function getAllCategories() {
  const apolloClient = getApolloClient();

  const data = await apolloClient.query({
    query: QUERY_ALL_CATEGORIES,
  });

  const categories = data?.data.categories.edges.map(({ node = {} }) => node);

  return {
    categories,
  };
}

```

由于这种设置，所有的修改只能在`src/data`下的文件上进行。

## 2.通过别名修改字段名

一个[字段别名](https://graphql.org/learn/queries/#aliases)可以用来在对 WPGraphQL 模式的响应中重命名一个字段。

这样，字段`postCategories`、`id`和`globalID`可以使用应用程序期望的名称分别检索到:`categories`、`categoryId`和`id`:

```
{
  categories: postCategories(limit: 10000) {
    categoryId: id
    description
    id: globalID
    name
    slug
  }
}

```

请注意 WPGraphQL 中的字段`categories`有参数`first`，而它对应的字段`postCategories`使用参数`limit`。但是，因为字段参数没有反映在响应中的字段名称中，所以我们不需要担心它们。

## 3.通过`self`字段调整响应的形状

最后一个挑战有点棘手:我们需要修改响应的形状，为来自[光标连接规范](https://relay.dev/graphql/connections.htm)的`edges`和`node`添加额外的级别。

为了实现这一点，我们将为 GraphQL 模式中的所有类型引入一个`self`字段，该字段将回显它所应用的对象:

```
type QueryRoot {
  self: QueryRoot!
}

type Post {
  self: Post!
}

type User {
  self: User!
}

```

`self`字段允许您在不离开被查询对象的情况下向查询追加额外的级别。运行[本查询](https://newapi.getpop.org/graphiql/?query=%7B%0A%20%20__typename%0A%20%20self%20%7B%0A%20%20%20%20__typename%0A%20%20%7D%0A%20%20%0A%20%20post(id%3A%201)%20%7B%0A%20%20%20%20self%20%7B%0A%20%20%20%20%20%20id%0A%20%20%20%20%20%20__typename%0A%20%20%20%20%7D%0A%20%20%7D%0A%20%20%0A%20%20user(id%3A%201)%20%7B%0A%20%20%20%20self%20%7B%0A%20%20%20%20%20%20id%0A%20%20%20%20%20%20__typename%0A%20%20%20%20%7D%0A%20%20%7D%0A%7D):

```
{
  __typename
  self {
    __typename
  }

  post(id: 1) {
    self {
      id
      __typename
    }
  }

  user(id: 1) {
    self {
      id
      __typename
    }
  }
}

```

…产生这样的反应:

```
{
  "data": {
    "__typename": "QueryRoot",
    "self": {
      "__typename": "QueryRoot"
    },
    "post": {
      "self": {
        "id": 1,
        "__typename": "Post"
      }
    },
    "user": {
      "self": {
        "id": 1,
        "__typename": "User"
      }
    }
  }
}

```

现在，我们可以使用`self`来人为添加`node`和`edges`级别:

```
{
  categories: self {
    edges: postCategories(limit: 10000) {
      node: self {
        categoryId: id
        description
        id: globalID
        name
        slug
      }
    }
  }
}

```

`edges`和`self`的 GraphQL 模式中的对象类型明显不同。但这对应用程序来说无关紧要，因为它不与 GraphQL 服务器中建模的实际对象进行交互。

相反，它以 JSON 对象的形式接收数据，来自`PostConnection`或`Post`对象的字段数据将是相同的。

请注意，`categories`字段是通过`self`解析的，而`edges`是通过`postCategories`解析的——而不是相反。这是为了确保返回元素的基数与 WPGraphQL 模式定义的匹配:

```
type RootQuery {
  categories: RootQueryToCategoryConnection
}

type RootQueryToCategoryConnection {
  edges: [RootQueryToCategoryConnectionEdge]
}

type RootQueryToCategoryConnectionEdge {
  node: Category
}

```

如果修改后的 GraphQL 查询是反过来的(即查询`categories: postCategories`和`edges: self`，那么访问数据将会失败，因为`data.categories`将会是一个数组，所以`data.categories.edges`在执行时将会抛出一个错误:

```
const categories = data?.data.categories.edges.map(({ node = {} }) => node);

```

## 修改后的查询

这是最后一个适合 WP 的 GraphQL API 的查询:

```
{
  categories: self {
    edges: postCategories(limit: 10000) {
      node: self {
        databaseId: id
        description
        id
        name
        slug
      }
    }
  }
}

```

在对`src/data` 中的所有[graph QL 查询应用了相同的策略之后，针对 WP 端点的 GraphQL API 运行 starter 可以很好地工作:](https://github.com/leoloso/next-wordpress-starter/tree/Add-plugin-GraphQL-API-for-WP-as-the-GraphQL-source/src/data)

![Successfully running the starter with a different GraphQL server](img/601ec1093fa347bcca0ae453a79e76db.png)

Successfully running the starter with a different GraphQL server

## 结论

在本文中，我们探索了依赖于特定 GraphQL 服务器的应用程序如何以最少的工作量适应不同的 GraphQL 服务器。

解决方案包括让 GraphQL 查询成为应用程序和服务器之间的中介，并且只对查询执行所有需要的更改，以避免修改业务逻辑。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.