# 用 JavaScript 和 WordPress - LogRocket 博客编写 GraphQL 服务器

> 原文：<https://blog.logrocket.com/coding-a-graphql-server-in-javascript-vs-wordpress/>

GraphQL 是一种规范，而不是特定语言的实现。因此，许多语言都有 GraphQL 服务器，包括 JavaScript、PHP、Rust、Python、Go 等。

`[graphql-js](https://github.com/graphql/graphql-js/)`，第一个 GraphQL 服务器，用 JavaScript 编码。它也是 GraphQL 规范的参考实现，因此其他 GraphQL 服务器可能会遵循`graphql-js`为自己的解决方案设置的架构决策。

然而，不同的语言有不同的特征，为基于 JavaScript 的服务器移植解决方案可能不会为基于其他语言的服务器产生最佳结果，或者对应用程序如何工作有不同的理解。相反，有偏离这些指导方针的空间可能会提供更好的结果。

在这篇文章中，我将探讨在三种情况下为 WordPress 构建一个 GraphQL 解决方案是如何背离标准 JavaScript 准则的:

1.  扩展查询解析
2.  命名空间模式(或不是)
3.  使用嵌套突变(或不使用)

我将使用我自己的插件 [GraphQL API for WordPress](https://github.com/GraphQLAPI/graphql-api-for-wp) 来演示 WordPress 的方法。

为了进行比较，我将利用 JavaScript 和 PHP 之间的不同特征，驱动它们的社区的基本原理，以及使用这些技术时所采用的公认概念和标准方法。

我还将探索基于 PHP 的 GraphQL 服务器是否能够提供定制特性，这些特性不能成为 GraphQL 规范的一部分，因为`graphql-js`不支持它们。

我们开始吧。

## 1.通过指令使查询解析可扩展

WordPress 应用程序通过插件是高度可扩展的。在 GraphQL 的上下文中，我们可能希望插件能够修改:

*   架构—向现有类型添加自定义类型、字段和指令
*   查询解析-覆盖字段的值

在本节中，我们将探索第二个元素:GraphQL 服务如何支持修改查询结果？

### WordPress 解决方案

WordPress 使用[钩子](https://developer.wordpress.org/plugins/hooks/)来修改行为。钩子是一些简单的代码，它们可以在被触发时覆盖一个值或者执行一个动作。

在[这个例子](https://github.com/GraphQLAPI/graphql-api-for-wp/blob/a92efc2e1e7d03d551196a17d2c717e5f1a74962/src/BlockCategories/AbstractBlockCategory.php#L13)中，函数`getBlockCategories`对过滤器`block_categories`做出反应，修改在 WordPress 编辑器中启用的块类别:

```
class AbstractBlockCategory
{
  function initialize()
  {
    \add_filter(
      'block_categories',
      [$this, 'getBlockCategories']
    );
  }

  public function getBlockCategories(array $categories): array
  {
    return [
      ...$categories,
      [
        'slug' => 'graphql-api-access-control',
        'title' => __('Access Control for GraphQL', 'graphql-api'),
      ],
    ];
  }
}

```

钩子简单、功能多样、功能强大；它们可能会被滥用，但是如果实现得好，它们会使应用程序以开发人员事先没有计划的方式高度可扩展。

### 指令作为钩子

受这个 WordPress 解决方案的影响，我为 GraphQL 搜索了一个类似的解决方案，并得出结论:[指令](https://graphql.org/learn/queries/#directives)可以被认为是它们的等价物。

指令就像一个 WordPress 钩子:它是一个修改字段值的函数，因此增加了一些其他的功能。作为它的对应物，它简单、通用，并且[强大](https://blog.logrocket.com/graphql-directives-are-underrated/#whyaredirectivesimportant)。

例如，假设我们用这个查询检索一个文章标题列表:

```
query {
  posts {
    title
  }
}

```

这些结果是英文的。我们如何把它们翻译成法语？应用于字段`title`的指令`@translate`获取该字段的值作为输入，调用 Google Translate API 来翻译`title`并返回该输出，如[中的查询](https://newapi.getpop.org/graphiql/?query=query%20%7B%0A%20%20posts%20%7B%0A%20%20%20%20title%20%40translate(from%3A%22en%22%2Cto%3A%22fr%22)%0A%20%20%7D%0A%7D):

```
query {
  posts {
    title @translate(from:"en", to:"fr")
  }
}

```

我发现扩展性的用例非常清楚:给定字段`title`的值，通过指令以任何想要的方式修改它。在这种情况下，它的修改是通过`[@translate](https://github.com/getpop/translate-directive/blob/78461f9bae6912d286e766c4886cf376e1ccb2ba/src/DirectiveResolvers/AbstractTranslateDirectiveResolver.php#L1)`翻译成法语，但也可以通过`[@upperCase](https://github.com/PoPSchema/convert-case-directives/blob/252b981e14a5b31898df10615efe4f5ca5d1be5e/src/DirectiveResolvers/UpperCaseStringDirectiveResolver.php)`和`[@lowerCase](https://github.com/PoPSchema/convert-case-directives/blob/252b981e14a5b31898df10615efe4f5ca5d1be5e/src/DirectiveResolvers/LowerCaseStringDirectiveResolver.php#L1)`转换成大写/小写，或者其他任何形式。

但是 JS 社区和我有共同的信念吗？

### js 中的指令

我相信我以前的解决方案在 GraphQL 社区(主要是与 JS 一起工作)中没有被广泛接受。

当描述这个解决方案时，我会被指责查询指令有多糟糕，就像 Reddit 上的这个评论一样:

> 在查询端出现大量指令**对我来说就像“代码味道”**

那条评论最后导致了一场争吵，我对此并不感到自豪😔。令人难以置信的是，即使是指令也能成为效忠、敌对和争夺的动机！🤣

### 使用字段参数代替指令

我得到的另一个解决方案是在字段中使用参数，就像这样:

```
query {
  posts {
    title(translateFrom: "en", translateTo: "fr")
  }
}

```

我在其他地方也遇到过这种被提倡的解决方案——例如，这里的。

### 用字段参数代替指令的问题

对我来说，使用字段参数是一个糟糕的解决方案；它将翻译的逻辑与解析字段的逻辑混合在一起，所有这些都在字段解析器中完成。它可能会工作，但不是特别优雅。

此外，如果我们也需要翻译摘录，会发生什么？使用指令非常简单，因为它们可以很容易地应用于任何字段，无需任何更改，如下面的查询所示:

```
query {
  posts {
    title @translate(from:"en", to:"fr")
    excerpt @translate(from:"en", to:"fr")
  }
}

```

但是，作为字段参数，翻译逻辑还需要“污染”字段`excerpt`的分辨率:

```
query {
  posts {
    title(translateFrom: "en", translateTo: "fr")
    excerpt(translateFrom: "en", translateTo: "fr")
  }
}

```

### 分析 WordPress 所期望的可扩展性

使用不同技术的不同开发人员会有不同的设计解决方案的方式，只要他们工作，就没有问题——没有对错。

然而，考虑到当前的特定主题(解析 GraphQL 查询的可扩展性)，使用字段参数替换指令是否总是可行？还是只有当所有信息都是预先已知的时候，比如为自己的公司或特定客户编写 GraphQL API 时？

如果 WordPress 期望让服务总是可扩展的，即使我们不知道哪个插件会扩展它，或者用户有什么定制需求，这样做可行吗？

考虑到 WordPress 的极端用户友好性，也就是说功能可以通过用户界面来实现，GraphQL 模式可以在不接触一行代码的情况下被扩展吗？

不，我认为不会:使用字段参数，开发人员将总是需要知道必须在 GraphQL 模式上完成什么集成，并且必须通过代码来执行。因此，这种方法不适合 WordPress 做事的方式。

然而，使用指令，所有的逻辑都被干净优雅地解耦，查询本身可以实现可扩展性，甚至可以通过配置和用户界面集成到模式[(在这种情况下，我们甚至可以避免将查询指令暴露给客户端)。](https://blog.logrocket.com/adding-directives-schema-code-first-graphql-servers/)

### 不同的方法来自哪里？

我不认为这是 JavaScript 和 PHP/WordPress 技术的问题，而是它们的基本哲学和工作方法的问题。虽然 JS 对开发者更友好，但是 WordPress 更愿意优先考虑最终用户的需求，而不是开发者的便利。

(顺便提一下，最近类似的讨论一直在激烈进行，是关于 [WordPress 和 Jamstack](https://wptavern.com/matt-mullenweg-and-jamstack-community-square-off-making-long-term-bets-on-the-predominant-architecture-for-the-web) 之间的所谓竞争，对此[我有自己的看法](https://leoloso.com/posts/jamstack-failing-at-comments/)。)

就我自己而言，当我推广基于指令的解决方案时，我来自于“通过钩子使它可扩展”的理念，这种理念很好地服务于 WordPress，也可以同样很好地服务于 GraphQL。

这一理念已经深深嵌入到插件 GraphQL API for WordPress 中:它使用一个[指令管道](https://blog.logrocket.com/treating-graphql-directives-as-middleware/)来解决查询，指令被视为一等公民。其中，他们可以对用户进行身份验证，删除字段的输出，以及停止执行管道中即将到来的阶段。

## 2.通过命名空间避免插件之间的冲突

在 [WordPress 目录](https://wordpress.org/plugins/)上发布插件的开发者事先不知道谁会使用他们的插件，也不知道网站会有什么配置/环境，包括可能会安装什么其他插件。因此，插件必须为冲突做好准备，并试图预先防止冲突。

WordPress 插件如何避免冲突？

### PHP 中的命名空间

再来说说钩子。如果我的插件需要添加一个过滤器来修改用户名，这个过滤器不能简单地叫做`"userName"`,因为这个名字太普通了，我担心另一个插件会使用相同的名字。相反，我必须给它加上一个唯一的名字，比如`"GraphQLAPIForWP:userName"`。

命名空间也有同样的作用——让一些代码变得独特。

名称空间在 PHP 社区中被广泛使用，遵循 PHP 标准建议`[PSR-4](https://www.php-fig.org/psr/psr-4/)`来启用 [Composer 自动加载](https://getcomposer.org/doc/01-basic-usage.md#autoloading)。PHP 包必须包含供应商的名称，如`"vendor-name/package-name"`，并且相应的名称空间出现在 PHP 代码中:

```
<?php
namespace VendorName\PackageName;

```

命名空间对 GraphQL 服务也有意义吗？

### GraphQL 中的命名空间

命名空间在 GraphQL 的上下文中也是有意义的，可以避免模式上发生以下潜在的冲突:

*   同名的两种类型的
*   具有两个相同类型的同名字段
*   有两个同名的指令

GraphQL 规范实际上已经请求了[命名空间:](https://github.com/graphql/graphql-spec/issues/163)

> 目前，所有 GraphQL 类型共享一个全局名称空间。对于变异/订阅类型中的所有字段也是如此。对于较大的项目来说，这可能是一个问题，因为在 GraphQL 模式中可能包含几个松散耦合的部分。

这个问题是四年多前提出的，现在仍然悬而未决。Lee Byron(在脸书工作时 GraphQL 的创建者之一)表示反对这个特性。在这篇评论中，他解释了脸书是如何管理其 GraphQL 模式中的数千种类型而不产生冲突的:

> 我们通过两种方式避免命名冲突:
> 
> 1.  **集成测试**。我们不允许任何提交合并到我们的存储库中，这将导致一个破碎的 GraphQL 模式。[…]
> 2.  **常见命名模式**。我们有命名事物的共同模式，这自然避免了冲突问题。[…]

但我不得不怀疑，适用于脸书的东西也一定适用于 WordPress 吗？

### 脸书语境与 WordPress 语境

请注意，在这一部分，WordPress 不是和 JavaScript，而是和脸书进行比较。然而，由于脸书是推动 GraphQL 向前发展的最大利益相关者之一，深刻影响了`graphql-js`的设计决策，我认为这种比较是有效的。

李·拜伦声称，如果脸书不需要命名空间来管理其数以千计的类型，那么我们都可以没有它。

然而，这里有一个重要的因素:脸书控制着它的 GraphQL 模式的所有输入！在这种情况下，它可以遵循一个命名实体的过程，确保不会出现冲突。

但这不是 WordPress 的工作方式；WordPress 网站严重依赖第三方插件，它并不控制这些插件是如何产生的。

例如，如果一个站点使用 WooCommerce 和 Easy Digital Downloads 插件，并且它们都有一个名为`Product`的 GraphQL 模式类型，那么就会产生冲突。网站所有者的唯一资源是联系其中一家公司，请他们修改代码。这不是预防而是矫正，不靠谱。

### 真的会发生冲突吗？

到目前为止我一直在说理论，让我们在实践中考虑一下这个话题:真的会发生冲突吗？

WordPress 插件必须认真考虑防止命名冲突，因为可用插件的数量太多了(仅在[目录](https://wordpress.org/plugins/)中就有超过 58，000 个)。一些重叠发生的可能性是不可忽略的。

但是 GraphQL 在 WordPress 内部有多受欢迎呢？

不幸的是，它的使用还不是很广泛。与 REST(通过 [WP REST API](https://developer.wordpress.org/rest-api/) 内置于 WordPress 核心)不同，GraphQL 必须从第三方插件安装。因此，除了代理公司为他们的客户创建主题和插件，或者在编写个人网站时，开发者不能假设 WordPress 网站上会有 GraphQL 服务。

目前，WordPress 有两个 GraphQL 解决方案:

我们关心扩展 GraphQL 模式的插件。有多少？

在 WordPress 插件目录上搜索只会产生少量的结果，没有一个结果超过 200 次激活。

[在 GitHub](https://github.com/search?o=desc&p=1&q=wpgraphql&s=stars&type=Repositories) 上搜索会产生以下 10 星或以上的 WPGraphQL 扩展:

(还没有针对 WordPress 的 GraphQL API 的扩展。)

那么，如果 GraphQL 在 WordPress 中还不那么流行，我们还需要命名空间吗？

### 然而命名空间

我认为我们确实需要命名空间，即使它现在不能解决任何问题。这是因为:

*   它提供了一个内置的解决方案来避免冲突，就像对未来的一个承诺，这样插件开发者就可以放心，他们的代码会一直工作
*   它使得 GraphQL 模式更加优雅

最后一项有如下逻辑:如果 WooCommerce 希望确保永远不会发生冲突，那么它不能为它们的类型使用通用名称，如`Product`、`Download`或`Payment`。相反，它需要在类型名本身上“命名”它们:`WCProduct`、`WCDownload`和`WCPayment`。或者，为了绝对确信它会一直工作，它可以把它们叫做`WooCommerceProduct`、`WooCommerceDownload`和`WooCommercePayment`。

这个模式变得更加冗长，不够优雅。如果可能的话，这是应该避免的。

### 支持自动命名空间，作为一个选择

假设我们可能并不真的需要命名空间，那么 GraphQL 服务器的最佳策略是将它作为一个可选特性提供，当启用时，它将自动命名模式中的所有类型。

这个策略在 GraphQL API for WordPress 插件中是可行的，因为:

*   它使用了[代码优先的方法](https://blog.logrocket.com/code-first-vs-schema-first-development-graphql/)(而不是 SDL 优先的方法),因此模式是作为代码的工件产生的，并且可以方便地重新生成
*   它通过类型的 PHP 类而不是类型名来引用类型，因此可以方便地覆盖类型名

例如，在[这段代码](https://github.com/getpop/comments/blob/e0ba8aeb1c8ce3f0091149fadcfeb1454d2b232e/src/FieldResolvers/CustomPostFieldResolver.php#L103)中，字段`Post.comments`通过引用其对应的 PHP 类`CommentTypeResolver`声明属于类型`Comment`:

```
class CustomPostFieldResolver extends AbstractQueryableFieldResolver
{
  // ...

  public function resolveFieldTypeResolverClass(
    TypeResolverInterface $typeResolver,
    string $fieldName,
    array $fieldArgs = []
  ): ?string {
    switch ($fieldName) {
      case 'comments':
        return CommentTypeResolver::class;
    }

    return null;
  }
}

```

作为进一步的增强，插件不需要用户输入名称空间类型。相反，所有 PHP 包都使用现有的`PSR-4`命名，将它们在模式中的类型从`"VendorName\PackageName"`转换为`"VendorName_PackageName"`(类型名的语法中目前不允许使用字符`"\"`或`"/"`，所以使用`"_"`。

## 3.偏离标准:嵌套突变

[突变](https://graphql.org/learn/queries/#mutations)是可以改变 GraphQL 服务器中数据的操作，比如创建帖子、更新用户名、给帖子添加评论等等。

在 GraphQL 中，突变只暴露在`RootMutation`类型下，就像这样:

```
type RootMutation {
  createPost(id: ID!, title: String!, content: String): Post!
  updateUserName(userID: ID!, newName: String!): User!
  addCommentToPost(postID: ID!, comment: String!, userID: ID): Comment!
}

```

使用这个模式，修改用户名是这样实现的:

```
mutation {
  updateUserName(userID: 37, newName: "Peter") {
    name
  }
}
```

正如 GraphQL 规范中的[所解释的，在变异根对象类型中暴露变异只是为了强制它们被顺序执行:](https://spec.graphql.org/draft/#sec-Mutation)

> 预计变异操作中的顶级字段会对底层数据系统产生副作用。所提供的突变的连续执行确保了在这些副作用期间对抗竞争条件。

术语“串行执行”与“并行执行”相对，后者是解析字段的推荐行为。

例如，在下面的查询中，GraphQL 服务器首先解析哪个字段(是`name`还是`email`)并不重要，这些可以并行解析:

```
query {
  user(id: 37) {
    name
    email
  }
}

```

不过，突变会改变数据，所以解析字段的顺序很重要；因此，它们必须串行执行，否则，它们可能会产生竞争条件。

例如，下面的两个查询将产生不同的结果:

```
# Query 1: after execution, user name will be "John"
mutation {
  updateUserName(userID: 37, newName: "Peter") {
    name
  }
  updateUserName(userID: 37, newName: "John") {
    name
  }
}

# Query 2: after execution, user name will be "Peter"
mutation {
  updateUserName(userID: 37, newName: "John") {
    name
  }
  updateUserName(userID: 37, newName: "Peter") {
    name
  }
}

```

仅通过`RootMutation`暴露突变的结果是这种类型变得非常臃肿，包含除了必须串行执行之外没有任何共同之处的字段(这是一个技术问题，而不是接口设计决策)。

### 嵌套突变的情况

从上面的突变来看，只有`createPost`真正属于`RootMutation`类型，因为它凭空创造了一个新元素。然而，变异`updateUserName`和`addCommentToPost`可以对另一种类型的现有实体应用等价的操作:

```
type User {
  updateName(newName: String!): User!
}

type Post {
  addComment(comment: String!, userID: ID): Comment!
}

```

使用此模式，可以像这样修改用户名:

```
mutation {
  user(ID: 37) {
    updateName(newName: "Peter") {
      name
    }
  }
}

```

这个特性被称为嵌套突变——将一个突变应用于另一个操作的结果，无论是查询还是突变。

请注意使用嵌套突变如何使 GraphQL 模式更加优雅:

*   虽然操作`RootMutation.updateUserName`必须接收用户的`ID`，但是它的等价操作`User.updateName`不能，因为它已经在用户实体上执行了
*   字段名从`updateUserName`缩短为`updateName`

此外，GraphQL 服务变得更简单、更容易理解，因为我们可以在图中的实体间导航，以与查询数据相同的方式修改它们的数据。

嵌套突变可以有多个层次。例如，我们可以在新创建的帖子上添加评论，所有这些都在一个查询中完成:

```
mutation {
  createPost(ID: 37, title: "Hello world!", content: "Just another post") {
    id
    addComment(comment: "Lovely post") {
      id
    }
  }
}

```

由此，嵌套变异还可以通过减少往返延迟来提高性能，从执行多个查询来变异几个元素到执行单个查询。

差不多四年前，GraphQL 规范已经请求了嵌套突变[,但是这个问题仍然悬而未决(原因我们将在下面探讨)。](https://github.com/graphql/graphql-spec/issues/252)

### 为什么嵌套突变不是规范的一部分

GraphQL 规范适用于任何语言的 GraphQL 服务器的所有实现。然而，它的驱动力是通过参考实现`graphql-js`的 JavaScript。

换句话说，任何不能被`graphql-js`支持的特性都不会成为规范的一部分。

由于 JavaScript 支持[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)，字段的并行解析是可行的，并行性成为最初设计`graphql-js`时的基本原则之一，这在[数据加载器](https://github.com/graphql/dataloader)(数据提取层)中有所体现，其批处理函数返回 JavaScript 承诺。

并行执行对于性能的优势太多了，嵌套变异无法与并行协同工作。已经决定不值得用并行执行来换取嵌套变异。因此，嵌套突变不符合规范。

但是对于其他不支持承诺的语言会发生什么呢？

### 为什么嵌套突变对 PHP 有意义

PHP 天生不支持承诺(除非使用外部库，比如 [ReactPHP](https://reactphp.org/) )。因此，支持嵌套突变不会产生移除并行执行的负面结果，因为这个特性本来就不存在。

这意味着一个基于 PHP 的 GraphQL 服务器应该完全能够支持嵌套突变，提供它的所有好处，并且没有它的任何后果。

这是一个机会。为什么不应该拿？

### 嵌套突变和性能

对于 WordPress 插件的 GraphQL API，字段总是被顺序解析，并且它们被解析的顺序是确定的(正是这种特性使得支持 [`@export`指令](https://graphql-by-pop.com/docs/operational/export.html)成为可能)。

这个特性不影响查询解析性能，因为服务器[在解析查询](https://graphql-by-pop.com/docs/architecture/dataloading-engine.html#analyzing-the-time-complexity-of-the-solution)时使用队列(而不是图),根据模式上的类型数量交付复杂时间`O(n)`。用线性时间解决一个 GraphQL 查询已经是最好的了。

在我写这篇文章的时候，这个插件的嵌套突变的实现是一个正在进行中的工作。因为 GraphQL 规范不支持嵌套突变，所以这个特性将作为选择加入，并声明它是非标准功能。

## 结论

在本文中，我们探讨了不同的因素如何影响 WordPress 的 GraphQL 服务器与 JavaScript 的同等服务器的某些元素的实现:

1.  使用 WordPress/JS 时使用的不同方法可能会影响提供可扩展查询解析系统的架构。
2.  关于如何以及何时修改 GraphQL 模式以及由谁来修改的不同观点可能会影响是否有必要在模式中引入命名空间。
3.  JavaScript 和 PHP 之间的不同特性可能证明支持嵌套突变是正确的。

没有好的或坏的解决方案，只有更适合不同环境的解决方案。我已经提出并演示了 WordPress 的解决方案，考虑到它的哲学、观点、PHP 的能力和其他，我认为它比基于 JavaScript 的替代方案更好。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.