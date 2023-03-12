# 在 Golang 中实现 GraphQL 的 3 个技巧

> 原文：<https://blog.logrocket.com/3-tips-for-implementing-graphql-in-golang/>

## GraphQL 是什么？

后端技术专家通常会说 GraphQL 是继 REST APIs 之后的“闪亮的新成员”。最初由脸书开发，作为一个强大的内部使用的数据获取 API，今天它每天支持数十亿次 API 调用。

官方文档将 GraphQL 定义为 API 的查询语言，它提供了 API 中数据的 360 度视图。但是来自自由代码阵营的定义说:

GraphQL 是一种描述如何请求数据的语法，通常用于将数据从服务器加载到客户端。它让客户在请求中准确指定需要什么数据，从而更容易从多个来源中选择数据。”

### 先决条件

本教程将重点介绍用 Golang 实现 GraphQL 时需要注意的一些要点。为了跟进，强烈建议您具备以下工作知识:

### 让我们复习一下 GraphQL

在任何讨论 GraphQL 的场景中，有些关键字会比其他关键字使用得更频繁。因此，有必要提醒我们自己其中的一些话:

**GraphQL 类型**:这些是 GraphQL 模式定义的最基本组成部分。它们是 JSON 字段，表示可以从服务中获取的对象种类。查看这个[教程](https://blog.logrocket.com/defining-types-for-your-graphql-api/)来详细了解 GraphQL 类型。

作为一个例子，这里有一个作者的样本类型定义，它有三个字段:`name`、`id`和`book`:

```
type author {
  name: String! //the `!` sign shows it’s required
  id: ID!
  book: String!
}
```

**GraphQL 查询** : GraphQL 查询是从 GraphQL 服务器请求或获取数据的一种方式。查询的结构将决定数据是取多还是取少。这个[教程](https://blog.logrocket.com/graphql-queries-in-simple-terms/)详细解释了查询。

下面是一个关于作者姓名的示例查询，遵循我们上面的类型定义:

```
query {                     
  authors {                 
    name      
  }
}
```

**GraphQL 模式**:模式就像地图。它们通过描述数据在服务器中的关系来帮助我们理解数据。这有助于客户机在试图访问数据时知道如何组织它们的请求。它们是用 SDLs 编写的，即模式定义语言。

**解析器**:解析器在服务器中寻找请求的数据。它们是特定于字段的函数。这是 GraphQL 用于为每个查询请求返回数据的唯一机制。

**GraphQL 突变**:突变用于创建和修改 GraphQL 中的对象，类似于 REST APIs 中的 PUT 和 POST。它们遵循与查询类似的语法，除了它们需要以关键字`mutation`开始。

**GraphQL 订阅**:这是一个允许服务器为其配置的每个特定事件向其客户端发送数据的特性。为此，服务器与订阅它的客户机保持稳定的连接

## 为什么用 Golang 的 GraphQL？

Go 编程语言，也称为 Golang，是一种通用编程语言，最初由 Google 设计。Golang 因其简单性、并发性和快速性能以及许多其他漂亮的特性而名列前茅。因此，它仍然是实现 GraphQL 时的首选之一。

随着许多人开始意识到这对组合(GraphQL 和 Golang)的潜力，多年来软件工程师开始构建库来无缝集成 GraphQL 和 Golang。

下面是一些使实现变得流畅的库:

*   [Graph-gophers/graphql-go](https://github.com/graph-gophers/graphql-go) :这提供了解析 graphql 模式语言的支持，同时在编译时给出解析器信息，而不必等到运行时才发现模式和解析器的问题。
*   GraphQL-go/graphql :它像其他库一样支持查询、突变和订阅。它还模拟了 [GraphQL-js](https://github.com/graphql/graphql-js) 库的正式实现
*   Gqlgen :这优先考虑类型安全，并且基于模式优先的方法。

在本教程中，我们不会关注这些库的实现。然而，这些库中的每一个都有一个`examples`文件夹，您可以导航到这个文件夹，以便直接看到这些库是如何实现的。

### 灯泡时刻来了

让我们探讨一下 GraphQL 与 Golang 集成时可能遇到的一些场景，以及解决方案是什么。

### 技巧#1:模式同质性

`graphql-go/graphql`是 Golang 中 GraphQL 的全功能实现，支持查询、突变和订阅，以及许多其他功能。

虽然这个库利用其他库来帮助您构建生产就绪的 GraphQL 服务器，但它是 GraphQL.js 的官方参考实现。换句话说，它是 GraphQL.js 库的 Golang 版本。

其与 GraphQL.js 几乎相同的 API 的优势使得阅读另一个为 Node.js/JavaScript,编写的 GraphQL 模式变得如此容易，并且您仍然能够为 Golang 重写它。让我们来看看:

**用`graphql-go`** 为 Golang 定义 GraphQL 模式

我们导入`graphql-go`包，并用一个`hello`字段定义名为`BaseQuery`的 GraphQL 对象类型来创建模式。

```
package main

import (
   "log"
   "github.com/graphql-go/graphql"
)

var queryType = graphql.NewObject(graphql.ObjectConfig{
   Name: "BaseQuery",
   Fields: graphql.Fields{
       "hello": &graphql.Field{
           Type: graphql.String,
           Resolve: func(p types.ResolveParams) interface{} {
               return "World!"
           },
       },
   },
})

var Schema, err = graphql.NewSchema(graphql.SchemaConfig{
   Query: queryType,
})
if err != nil {
   log.Fatalf("failed to create new schema, error: %v", err)
}
```

在模式创建之后创建 GraphQL 服务器遵循官方`graphql-go`文档[这里](https://github.com/graphql-go/graphql)中强调的正常步骤。

**用 GraphQL.js 为 JavaScript 定义 Graphql 模式**

从下面的代码片段中，我们需要 GraphQL 包，并用一个`hello`字段定义名为`BaseQuery`的 GraphQL 对象类型。这与上面模式中强调的过程相似。

```
var graphql = require('graphql');

var queryType = new graphql.GraphQLObjectType({
   name: 'BaseQuery',
   fields: {
       hello: {
           type: graphql.GraphQLString,
           resolve: function () {
               return 'World!';
           }
       }
   }
});

var schema = new graphql.GraphQLSchema({
   query: queryType
});
```

仔细观察上面的两种表示，我们可以得出结论，无论您熟悉哪种编程语言，使用`graphql-go`库在 Golang 中实现 GraphQL 都是轻而易举的事情。

这是因为`graphql-go`库非常接近 GraphQL.js 的官方参考实现，这是我们选择的事实上的库。因此，它为 GraphQL.js 库提供了几乎相同的 API。

### 技巧 2:解决 n+1 问题

与每个 API 端点使用一个解析器的 REST APIs 不同，GraphQL 为每个字段执行一个解析器。这意味着每个请求可能有太多额外的解析器，尤其是对于嵌套数据。这就是 n+1 问题。

下面是一个示例查询来进一步支持这一点:

```
query {                     
 authors {            # fetches authors - query 1
   name      
   book {             # fetches books for each author (N queries for N authors)
     title
   }
 }
}                     # N+1 round trips
```

以下是查询的响应:

```
{
 "writers": [{
   "name": "Tom Wolfe",
   "book": {
     "title": "The Bonfire of the Vanities"
   }
 }, {
   "name": "ALice Walker",
   "book": {
     "title": "The Color Purple"
   }    
 }]
}
```

上面的例子可能没有显示这种情况升级为问题的速度有多快。但是假设我们需要获取 100 个作者的数据，解析器要进行 100+1 次旅行来获取响应。同时，这可以在两次旅行中完成；即请求和响应。

解决这个问题的方法是批处理。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

**批处理**是数据加载器的主要特性。[数据加载器](https://github.com/graphql/dataloader)最初是由脸书在 2010 年为 GraphQL.js 开发的，用作应用程序数据提取层的一部分。它通过批处理和缓存在远程数据源上提供了一个简化且一致的 API。然而，已经为`graphql-go`开发了其他几个数据加载器库，例如:

*   graph-gophers/dataloader :这是官方 facebook [dataloader](https://github.com/graphql/dataloader) (用 JavaScript 编写)在 Golang 中的复制实现
*   来自 [gqlgen](https://github.com/99designs/gqlgen) 作者的 [Dataloaden](https://github.com/vektah/dataloaden) :这在 Golang 中生成类型安全的数据加载器，与`gglgen`库一起使用，用于区分类型安全的优先级。它也受到官方数据加载器库的启发

和许多其他人。

数据加载器(不考虑库)通过允许解析器请求数据并返回对数据的承诺，而不是立即返回所请求的确切字段，来解决 n+1 问题。然后，对来自请求的所有响应进行批处理，并立即做出响应，因此有两个行程——请求和响应。

然而，让我们更详细地看看`graphql-gophers/dataloader`库。

这里有一个示例实现，演示了如何在没有缓存的情况下使用这个库。更多的例子，请点击库[的例子目录这里](https://github.com/graph-gophers/dataloader/tree/master/example)。

```
package no_cache_test

import (
 "context"
 "fmt"

 dataloader "github.com/graph-gophers/dataloader/v6"
)

func ExampleNoCache() {
 // go-cache will automaticlly cleanup expired items on given diration
 cache := &dataloader.NoCache{}
 loader := dataloader.NewBatchedLoader(batchFunc, dataloader.WithCache(cache))

 result, err := loader.Load(context.TODO(), dataloader.StringKey("some key"))()
 if err != nil {
   // handle error
 }

 fmt.Printf("identity: %s", result)
 // Output: identity: some key
}

func batchFunc(_ context.Context, keys dataloader.Keys) []*dataloader.Result {
 var results []*dataloader.Result
 // do some pretend work to resolve keys
 for _, key := range keys {
   results = append(results, &dataloader.Result{Data: key.String()})
 }
 return results
}
```

### 技巧#3:客户机-服务器数据模式不匹配

通常，从头开始编写一个 GraphQL 后端需要复制大量相似的代码(模式)，例如不同但相似的数据库和 API 端点模式。

这是因为不同的模式影响了文档的存储方式。

例如:对来自`database`的作者的查询将返回一个`authorID`属性，但是对直接来自`server`的作者的相同查询将返回`author`属性。这使得很难在客户机和服务器之间共享代码来简化代码库。

```
const fetchBookTitleClient = author => {
   return author.book.title
}

const fetchBookTitleServer = author => {
   const authorDeets = Books.findOne(author.bookId)
   return authorDeets.title
}
```

**解决这个问题的方法是服务器-服务器查询**

回想起来，这是通过将 GraphQL 模式添加到 GraphQL 函数中实现的，如下所示:

```
graphql(
 schema: GraphQLSchema,
 requestString: string,
 rootValue?: ?any,
 contextValue?: ?any,
 variableValues?: ?{[key: string]: any},
 operationName?: ?string
): Promise<GraphQLResult>
```

但是在实现中，在我的代码中，我是这样运行服务器到服务器的查询的:

```
const result = await graphql(executableSchema, query, {}, context, variables);
```

这允许在不牺牲性能的情况下极大地简化代码。

## 结论

正如我们在本教程中所看到的，GraphQL 不仅仅是一种 API 查询语言。它有可能在数据和 API 查询方面彻底改变游戏。

有了这样的潜力，GraphQL 的全部优势必须由像 Golang 这样的编程语言来利用。

Go 编程语言由于其并发性、简单性和快速的性能而轻而易举地名列榜首。

我希望你喜欢阅读这篇关于在 Golang 中实现 GraphQL 时遇到的问题的文章。请随意留下任何问题或反馈。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.