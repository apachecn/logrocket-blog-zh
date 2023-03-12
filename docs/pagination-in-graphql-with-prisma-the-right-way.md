# 用 Prisma 以正确的方式在 GraphQL 中分页

> 原文：<https://blog.logrocket.com/pagination-in-graphql-with-prisma-the-right-way/>

当处理有数百万条记录的数据库时，采用分页非常有用，因为它可以帮助我们检索数据的子集，否则如果不查询表上的每条记录，我们就无法访问这些数据。

从我们的数据库中检索这些信息会非常昂贵，并且会对性能产生巨大影响。在客户端进行渲染的成本也很高。

在撰写本文时，Prisma 在一个表上检索的默认记录数量是 1，000 条记录——也就是说，如果没有请求记录的子集。

当查询记录列表时，我们可以通过提供分页参数来获取该列表的某些部分(即页面)。

对于 Prisma 中的分页，我们可以使用 5 个主要参数:`first`、`last`、`after`、`before`和`skip`。

在这篇文章中，我们将探讨各种方法来组合这些参数，以有效地对数据进行分页。

我们还将使用这个[公共 API](https://graphiql.graphcms.com/simple/v1/swapi) 进行示例演示。

### 将 first 或 last 与 skip 一起使用

假设一个应用程序使用如下所示的按钮实现分页，页面大小为 10 项。

为了实现这一点，我们需要重写我们的`typedef`定义来接受参数，并在查询的解析器函数中实现它。

我们不会这样做，因为我们正在使用一个已经构建好的公共 API。查看文档可以发现，查询已经被定义为接受这些参数。

好吧，让我们湿一下脚。

*   如果我们访问公共 API，我们会看到 doc 部分。
*   单击文档部分，我们将看到查询、变异和订阅。
*   然后，我们将单击查询类型，查看所有可用的查询。

出于本文的目的，我们将使用`allStarships`查询。

```
     {
        allStarships(first: 10) {
          id
          name
          length
          manufacturer
        }
      }
```

当我们运行这段代码时，总共返回了 10 条记录。但是，这对于分页来说并不完整，因为每次都会返回相同的 10 条记录。

为了确保它正常工作，我们需要引入另一个名为`skip`的参数。

方法如下:

```
{
    allStarships(first: 10, skip: 10) {
      id
      name
      length
      manufacturer
    }
  }
```

这给了我们第二页，或接下来的 10 个记录。这可以通过发送按钮的页码乘以我们的页面大小来动态完成。

我们可以像这样得到第一、第二和第三页:

```
//first page
 // number to skip pageNumber = 1
number = 10 * (1 - 1); // 0
...
allStarships(first: 10, skip: 0) 
//second page 
//number = 10 * (2 - 1); // 10
allStarships(first: 10, skip: 10) 
// third page 
//number = 10 * (2 - 1); // 10
allStarships(first: 10, skip: 20)

```

我们也可以使用最后一个参数，但是我们将从最后输入的记录查询表(如果 mongoDB 是选择的数据库，则为集合),而不是从表的顶部查询上面的记录。

### 与 skip 一起使用 before 或 after

假设我们实现了页面导航，当您到达页面底部时，您可以使用最后一个`id`来获取更多的记录。

![load more posts](img/442647abcb7a0bfefd5b6e471b0c2047.png)

在我们的示例中，我们可以决定再获取 10 条记录。

下面是它在代码中的样子:

```
{
    allStarships(first: 10, skip: 10) {
      id
      name
      length
      manufacturer
    }
  }
```

这使我们在指定的`id`之后获得 10 条记录。我们也可以使用`last`和`before`来获取指定 id 之前的记录。

**注意:**我们不能将`first`与`before`结合使用，也不能将`last`与`after`结合使用。

如果我们在查询中这样做，那么`first`或`last`将被应用(在列表的末尾或开头，这取决于使用的是哪个),而`before`或`after`参数将被忽略。

### 结论

从数据库中查询记录时应用分页很重要，因为它有助于优化服务器的性能。这也有助于抑制客户端级别的昂贵渲染。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.