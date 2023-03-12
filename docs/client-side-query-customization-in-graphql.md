# GraphQL 中的客户端查询定制

> 原文：<https://blog.logrocket.com/client-side-query-customization-in-graphql/>

GraphQL 世界在查询、变异和订阅等操作的帮助下运行。任何使用过 GraphQL 的人都会知道，查询结构通常是在后端确定的，客户端被迫遵循它。对于 GraphQL 自动生成的文档，这不是什么大问题，尤其是当开发人员可以访问后端时。

但是在一些用例中，客户端可能不得不改变查询或参数的结构，本博客将解释其中的一个用例以及可能的解决方案。

## 贸易工具

在深入问题的细节和解决方案之前，对以下工具的基本了解将会有所帮助。

### Hasura

Hasura 是一个开源的 GraphQL 服务，它连接到用户数据库和微服务，并自动生成一个生产就绪的 GraphQL 后端。它支持 Postgres 数据库及其风格(时间刻度，Yugabyte)。它还允许用户将其现有的数据库/REST 端点转换成 GraphQL 数据库。

### `graphql-tag`

这是一个 JavaScript 模板文字标签，它将 GraphQL 查询字符串解析成标准的 GraphQL AST。这个工具允许我们将有效的查询字符串传递给后端，因为它可以处理并自动修复一些常见的查询字符串问题。在内部，这个工具使用 GraphQL 的解析器来生成 AST

## 背景

一个由三个前端和一个后端开发人员组成的团队在我们的业余时间开始从事一个新冠肺炎项目。后端人员只能在非常有限的时间内支持我们。这让我们不得不决定一个易于使用的后端，以便不依赖后端开发太多的支持。

### 哈苏拉来救援了

那时我们决定使用 Hasura 作为后端。使用 Hasura，只需创建模式和数据模型就足够了，它会自动创建带有模式的 GraphQL 后端。Hasura 的另一个漂亮的特性是，我们可以使用`where`条件直接从客户端查询数据，并使用`order_by`条件进行排序。这使得 Hasura 成为我们的首选解决方案。

有了 Hasura 的所有好处，我们已经能够作为一个前端开发团队来处理所有事情，并且不会给后端开发人员带来太多麻烦。这也有助于我们快速制作应用原型。

### 我们的技术堆栈

*   Next.js
*   阿波罗
*   样式组件
*   `graphql-tag`
*   Hasura
*   Postgres

## 问题是

当我们开始的时候，一切都很完美，这对我们来说很好。然而，由于我们必须决定如何在客户端查询数据，查询字符串的大小急剧增加，有许多嵌套的对象。这破坏了查询的可读性，甚至很难检查查询字符串的结构是否正确。

笨拙的查询示例如下所示:

```
const clumsyHasuraQuery = gql`
  query getProductById($id: Int!) {
    product(
      limit: 10
      offset: 10
      where: { id: { _eq: $id }, quantity: { _gte: 10 }, type_id: { _eq: 10, _gte: 22, _lte: 5, _in: [72,73,74] } }
      order_by: {category: asc, description: desc}
    ) {
      category
      id
    }
  }
`;
```

上面的查询展示了 Hasura 的一个特性，利用它我们可以基于`where`条件控制和获取准确的数据，并使用`order_by`子句对其进行排序。然而，这大大影响了我们的可读性。由于这是一个受欢迎的项目，我们偶尔会访问代码库，我们很难理解这些查询。

## 解决方案

我们决定找到一个解决方案来修复这个可读性问题。我们的第一个方法是挖掘现有的工具，找到一条出路。作为后端，Hasura 在这种情况下帮不上什么忙。如果我们想避免这种情况，我们就必须创建自定义参数并对模式进行更改。因此，我们决定寻找客户端的解决方案。

在这个过程中，我们在进入阿波罗客户端之前就开始深入研究`graphql-tag`。`graphql-tag`库实际上将查询字符串解析到 AST。所以我们决定在`graphql-tag`上写一个包装器，它获取以我们喜欢的格式编写的查询字符串，将其转换成 AST，并将其转换成 Hasura 在后端期望的结构。我们把它命名为`hql-tag`，因为它是 Hasura 特有的。

### 步骤 1:首选查询结构

我们决定为`where`和`order_by`子句提供一个简单的单个单词的参数，而不是使用嵌套对象作为查询的参数。我们想要的查询应该看起来更优雅，就像这样:

```
const elegantHasuraQuery = gql`
  query getProductById($id: Int!) {
   product(
      limit: 10
      offset: 10

      # `${arguementName}_${operatorSuffix}`
      id_eq: $id
      quantity_gte: 10
      type_id_eq: 10
      type_id_gte: 22
      type_id_lte: 5
      type_id_in: [72, 73, 74]
      category_ord: asc
      description_order: desc
    ) {
      category
      id
    }
  }
`;
```

### 第二步:创建`hql-tag`

既然我们已经知道了首选和所需后端查询的结构，那么是时候在`graphql-tag`上创建`hql-tag`包装器了。

这个包装器是一个 JavaScript 模板文字标签，它接收查询字符串并将其传递给`graphql-tag`。一旦`graphql-tag`返回 AST，`hql-tag`将修改它以适应后端的需要。

我们考虑直接操作查询字符串。但是不可靠。因此，我们决定操纵 AST。一个示例`hql-tag`实现:

```
const hql = (stringArray, ...expressions) => {
  // Generate AST from graphql-tag
  const gqlAst = gql(stringArray, ...expressions);
  // Return the AST if its not query
  if (gqlAst.definitions[0].operation !== "query") return gqlAst;
  // Traverse through AST and modify nodes
  processAST(gqlAst);
  // Return the AST
  return gqlAst;
};
```

### 步骤 3:修改 AST

我们现在有了 AST，是时候进行修改了。过程是这样的。

为所需的不同类型的节点创建 AST 节点模板:

```
const argumentTemplate = {
  kind: "Argument",
  name: {
    kind: "Name",
    value: "where",
  },
  value: {
    kind: "ObjectValue",
    fields: [],
  },
};
```

遍历节点并处理作为参数的节点:

```
processNodes(node, parent, key, index);
```

如果它是一个参数，检查它是否有任何预定义的操作符作为后缀。自定义参数的格式为`${argument_name}_${operator}`。

`where`参数应该有任何一个支持参数，用下划线`_`隔开。以下是`where`操作符:

```
[ "eq", "gte", "gt", "ilike", "in", "like", "lt", "lte", "neq", "nilike", "nin", "nlike", "similar", "nsimilar" ]
```

`order_by`参数应该有一个后缀`ord`或`order`。这些操作符是:

```
["ord","order"]
1\. ord - "short form"
2\. order - "full form"
```

如果一个参数节点有任何操作符，那么参数名和值将被提取并加载到预定义的模板中。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
- argumentTemplate.value.fields.push(...);
```

这构造了一个新的 AST 节点，它替换了 argument 节点，最终结果是一个服务器要求格式的 AST。

通过修改 AST，我们可以在客户端定制查询，并且我们能够实现一个可读且优雅的 GraphQL 查询。这个`hql-tag`库是独立的，可以与所有 GraphQL 客户端框架一起工作。

### 使用

可以在 [GitHub](https://github.com/product-ride/hql-tag/) 和 [npm](https://www.npmjs.com/package/hql-tag) 上找到`hql-tag`库。它的用法很简单。一个`graphql-tag`查询看起来像这样:

```
import gql from 'graphql-tag';

const clumsyHasuraQuery = gql`
  //  ... query
`;
```

使用该库所需的唯一更改是修改 import 语句:

```
import gql from 'hql-tag';

const elegantHasuraQuery = gql`
  //  ... query
`;
```

现在我们准备在查询中使用定制的参数。

## 要点和后续步骤

虽然这个库是为 Hasura 量身定制的，但是在处理大型代码库和多个团队时，通过修改 AST 来定制客户端查询的方法会很方便。

这为我们在 GraphQL 世界中编写优雅、可读和可重用的代码提供了无限的可能性。这也为客户端的组织级定制和编码标准增加了广阔的范围。

该库的下一步将是添加对 GraphiQL 的支持，构建一个 Babel 插件，一个 Apollo 插件，添加对所有类型的 AST 节点模板的支持，修改所有节点类型，等等。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.