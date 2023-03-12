# GraphQL 中代码优先与模式优先的开发

> 原文：<https://blog.logrocket.com/code-first-vs-schema-first-development-graphql/>

> 本文是正在进行的关于概念化、设计和实现 GraphQL 服务器的系列文章的一部分。该系列的前几篇文章是:

* * *

[GraphQL](https://graphql.org) 模式通过导出可以针对服务执行的一组类型、字段和变异来定义 GraphQL 服务的契约。在创建 GraphQL 服务时，我们可以决定让模式成为事实的来源，并让我们所有的实现代码与其定义相匹配，或者让我们的代码成为事实的来源，让模式成为从代码生成的工件。

在这两种情况下，我们都将拥有一个全功能的 GraphQL 服务，但是根据我们使用的方法，我们可能能够实现更多或更少的功能，或多或少容易一些。这两种方法分别称为模式优先和代码优先。

在本文中，我们将探索这两种创建 GraphQL 服务的方法，回顾每种方法的优缺点，最后，表明立场并决定哪种方法可能是更好的选择。开始吧！

## 什么是模式优先？

Schema-first 表示我们首先为 GraphQL 服务定义模式，然后通过匹配模式中的定义来实现代码。为了对模式进行编码，我们使用了[模式定义语言(SDL)](https://www.howtographql.com/basics/2-core-concepts/) ，这是一种为表示 GraphQL 数据模型而创建的语法。正因为如此，这种方法也可以称为 SDL 优先。

SDL 为 GraphQL 的广泛流行做出了贡献。即使 GraphQL 的第一次实现和实用程序是在 JavaScript 中(2015 年 [graphql-js](https://github.com/graphql/graphql-js) ，2016 年 [graphql-tools](https://github.com/apollographql/graphql-tools) )，SDL 也被设计成语言无关的。不同语言的 GraphQL 服务器的实现——包括那些用于 [Node.js](https://github.com/prisma/graphql-yoga) 、 [Ruby](http://graphql-ruby.org/) 、 [Python](http://graphene-python.org/) 、 [Scala](https://sangria-graphql.org/) 、 [PHP](https://graphql-by-pop.com) 和其他语言的实现——可以读写 SDL。

让我们看看 SDL 是什么样子的(我不会去详细介绍，因为有很多关于它的教程，像[这个](https://blog.logrocket.com/defining-types-for-your-graphql-api/))。在 SDL 定义一个具有属性`id`和`title`的类型`Film`，如下所示:

```
type Film {
  id: Int
  title: String!
}
```

代码几乎是自描述的:title 是 string 类型的属性(用 title case: `String`编写)，它必须总是有一个值，通过字符`!`表示。

由于 GraphQL 完全是关于在图中查询关系的(这就是 GraphQL 这个名字的来源:图查询语言)，我们可以跨不同类型定义关系，就像这样:

```
type Film {
  id: Int
  title: String!
  director: Person!
  actors(limit: Int = 10): [Person]
}

type Person {
  id: Int
  name: String!
}
```

由于一部电影可以有很多男演员和女演员，所以属性`actors`通过字符`[]`被定义为一个列表类型，我们可以通过自定义参数`limit`来限制在这个列表中返回多少个元素，默认值为 10。

在这个简单的例子中，我们已经可以体会到 SDL 的最大优势:它非常简单明了，因此，对于技术人员和非技术人员来说都非常容易理解。因此，SDL 可以作为一种交流工具，团队成员可以通过它协作创建组织的数据模型。

同样，我们已经可以注意到 SDL 带来的弊端。首先，SDL 不包括解析器，即计算字段值的实际代码。因此，SDL 不可能独自成为真理的唯一来源，因为它并不完整。最多，SDL 只是在模式定义和解析之间带来了一个清晰的分离，如下面的代码(使用了 [graphql-tools](https://github.com/apollographql/graphql-tools) ):

```
const { makeExecutableSchema } = require('graphql-tools')

// Schema definition
const typeDefs = `
type Film {
  id: Int
  title: String!
  director: Person!
  actors(limit: Int = 10): [Person]
}

type Person {
  id: Int
  name: String!
}
`

// Schema resolution
const resolvers = {
  Film: {
    director: (film, args) => fetchUserById(film.directorID),
    actors: (film, args) => findUsers(film.actorIDs, {limit: args.limit}),
  }
}

const schema = makeExecutableSchema({
  typeDefs,
  resolvers,
})
```

第二个缺点是解析器代码必须与 SDL 中的定义完全匹配(例如，类型为`String!`的属性必须是`String!`，而不是`String`或`Int!`)。因此，它不是干的(不要重复自己)；代码库中会有重复的信息，这些信息必须保持同步。

## 什么是代码优先？

在代码优先的方法中，我们首先对解析器进行编码，然后，从作为单一事实来源的代码开始，我们将模式作为工件生成。因此，我们仍然有一个模式，但是它不是手动创建的，而是通过运行脚本创建的。这种方法也可以称为解析器优先。

仅仅通过查看代码来理解模式不像查看 SDL 定义那样容易。因此，一些 GraphQL 服务器实现试图尽可能地镜像它们的代码，即 SDL。

例如，在[Nexus](https://nexus.js.org/)(JavaScript/TypeScript 中代码优先的 GraphQL 服务器)中，定义字段的方法名已经表达了响应的类型(在下面的代码中:`t.int`、`t.string`、`t.field`表示与对象类型的关系，`t.list.field`表示与对象类型的关系列表)。实现与上面相同的模式如下所示:

```
const Film = objectType({
  name: "Film",
  definition(t) {
    t.int("id", { description: "Id of the film" });
    t.string("title", { description: "Title of the film" });
    t.field("director", {
      type: Person,
      resolve(root, args, ctx) {
        return ctx.getFilm(root.id).director();
      },
    });
    t.list.field("actors", {
      type: Person,
      nullable: true,
      args: {
        limit: intArg({
            required: false,
            default: 10,
            description: "Limit the number of actors/actresses"
        }),
      },
      resolve(root, args, ctx) {
        return ctx.getFilm(root.id).actors(args.limit);
      },
    });
  }
});

const Person = objectType({
  name: "Person",
  definition(t) {
    t.int("id");
    t.string("name");
  }
});
```

要被解析为对象中同名属性的字段(例如来自类型`Film`的字段`id`和`title`，它们被解析为来自电影对象的属性`id`和`title`)可以省略它们的解析函数。如果数据模型主要由这样的简单属性组成，没有任何自定义逻辑需要解析，那么代码就很像 SDL，就像上面代码中类型`Person`的情况一样。

Nexus 强调声明性和可读性。因此，尽管不如阅读 SDL 那么容易理解，但它仍然足够好，可以用于交流模式的意图以及如何解决它。

其他 GraphQL 服务器也在它们的定义中模拟 SDL，但是它们的结果可能不太理想。例如， [graphql-php](https://webonyx.github.io/graphql-php/) 像这样实现上面的模式:

```
$film = new ObjectType([
    'name' => 'Film',
    'fields' => [
        'id' => Type::int(),
        'title' => [
            'type' => Type::string(),
            'description' => 'Film's title'
        ],
        'director' => [
            'type' => Type::nonNull($personType),
            'description' => 'Film director',
            'resolve' => function(Film $film) {
                return DataSource::findPerson($film->directorID);
            }
        ],
        'actors' => [
            'type' => Type::listOf($personType),
            'description' => 'List of actors/actresses in the film',
            'args' => [
                'limit' => [
                    'type' => Type::int(),
                    'description' => 'Limit the number of actors returned',
                    'defaultValue' => 10
                ]
            ],
            'resolve' => function(Film $film, $args) {
                return DataSource::findActors($film->id, $args['limit']);
            }
        ]
    ]
]);

$personType = new ObjectType([
    'name' => 'Person',
    'description' => 'A person',
    'fields' => [
        'id' => Type::int(),
        'name' => [
            'type' => Type::string(),
            'description' => 'Person's name'
        ],
    ]
]);

```

在这种情况下，定义更加不和谐，几个缩进切断了易读性，不同的语法颜色也碍事。因此，尽管该模式仍然是可理解的，但并不是对每个人都是如此，并且它作为跨团队交流工具的效用降低了。

从这些例子中，我们可以体会到代码优先模式的最大优点和缺点:它可以有效地成为数据模型的唯一来源，因为它包含了模式定义和解决它们的代码，但代价是更难理解。

请注意，我们仍然可以使用用 SDL 编写的模式来与我们的同事交流数据模型:通过运行一些脚本，模式可以作为工件从代码中生成。此外，生成的模式可以被一致地格式化以帮助其易读性，例如按字母顺序排序类型。

需要时，可以手动生成模式。它也可以作为我们持续集成过程的一部分来自动化——例如，每当我们的代码库被标记时触发它——并将新创建的文件提交到一个特殊的存储库中。这是 GitHub 为其[公共 GraphQL 模式](https://github.com/octokit/graphql-schema)所采用的策略。

## 选购模式优先或代码优先的 GraphQL 服务器

我们可以选择这两种方法中的哪一种来创建我们的 GraphQL 服务。然而，可能必须在一开始就做出决定，因为它可能会影响我们使用哪个 GraphQL 服务器实现；大多数实现都会坚持使用其中一个，很少两个都用。

此外，这个决定也可能影响使用哪种语言。一些语言提供了这两种选择，比如 JavaScript 或 TypeScript 通过 [Apollo server](https://www.apollographql.com/docs/apollo-server/getting-started/#step-3-define-your-graphql-schema) (模式优先)和 [Nexus](https://nexus.js.org/) (代码优先)，Python 通过 [Ariadne](https://ariadnegraphql.org/) (模式优先)和 [Graphene](https://graphene-python.org/) (代码优先)，以及。NET 通过 [GraphQL 进行。NET](https://graphql-dotnet.github.io/docs/getting-started/introduction/) 提供了两种选择。

然而，其他一些语言只有一种方法，比如 Rust，它只有一个 GraphQL 服务器: [Juniper](https://github.com/graphql-rust/juniper) ，它只支持代码优先的方法。

最后，它还可能影响我们可以使用某些语言的哪个框架。例如，对于 PHP， [Laravel](https://laravel.com) 可以通过 [Lighthouse](https://lighthouse-php.com/) (模式优先)和 [Laravel GraphQL](https://github.com/rebing/graphql-laravel) (代码优先)从两个选项中进行选择，但是 [WordPress](https://wordpress.org) 只有提供代码优先方法的解决方案，例如 [WPGraphQL](https://www.wpgraphql.com/) 和[PoP graph QL](https://graphql-by-pop.com)。

所有语言的所有不同 GraphQL 服务器的链接可以在[这里](https://devhub.io/repos/chentsulin-awesome-graphql)找到。

## 不同实现者提倡的模式优先与代码优先

到目前为止，我们已经回顾了这两种方法在易读性(模式优先更好)和成为没有重复代码的单一事实来源的能力(代码优先更好)方面的比较。然而，这些并不是唯一需要比较的特征。

接下来，让我们看看不同的实现者如何证明在他们自己的 GraphQL 服务器实现中使用这两种方法的合理性。

### 为什么模式优先更好

在“[模式优先 GraphQL:少走的路](https://blog.mirumee.com/schema-first-graphql-the-road-less-travelled-cf0e50d5ccff)”中，Mirumee 的 Jakub Draganek 推广了 GraphQL 服务器 [Ariadne](https://ariadnegraphql.org/) 使用的模式优先方法。

Jakub 提到让模式充当客户端和服务器端之间的公共契约的优点包括:

*   这类似于进行测试驱动开发(TDD ),因为开发人员必须在开始编码解决方案之前考虑不同的用例以及用户的便利性，并且最终结果可能会带来更加模块化和可维护的设计。
*   它遵循依赖倒置原则(DIP ),这使得解决方案更加抽象，并且较少依赖于依赖。
*   它使客户端和服务器端开发人员能够同时工作，因为客户端开发人员不需要等待后端的工作先完成。该模式使得为 API 提供模拟数据成为可能，因此可以独立测试客户端，而无需准备好服务器端的 GraphQL 服务。

### 为什么代码优先更好

在“[‘模式优先’GraphQL 服务器开发的问题](https://www.prisma.io/blog/the-problems-of-schema-first-graphql-development-x1mn4cb0tyl3)”(以及他在 GraphQL Conf 2019 上的[演讲)中，Prisma 的 Nikolas Burk 提出了 graph QL 服务器](https://youtu.be/OloBAdNCnyQ) [Nexus](https://nexus.js.org/) 提供的代码优先方法。

按照 Nikolas 的说法，代码优先更好，因为模式优先不支持代码优先不支持的异常特性。与此同时，它使用起来更省力，因为与模式优先的方法相比，它不依赖于过多的工具。模式优先迫使开发人员使用大量的额外工具，这阻碍了他们的体验。

下面列出了一些挑战，模式优先需要一些工具来解决，而代码优先则不需要:

*   **模式定义和解析器之间的不一致**:确保模式定义始终与解析器同步。
*   **GraphQL 模式的模块化**:将 graph QL 类型定义组织成几个文件。
*   模式定义中的冗余(代码重用):重用 SDL 定义可能涉及大量样板文件和重复代码。
*   **IDE 支持和开发人员体验**:利用编辑器工作流中的 GraphQL 类型，从 SDL 代码的自动补全和构建时错误检查等功能中获益。
*   **组合 GraphQL 模式**:将许多现有的(和分布式的)模式组合成一个模式。

我想补充一点，模式优先中存在的问题在代码优先中不需要任何努力就可以解决，例如 GraphQL 规范中的这个问题，它涉及到在模式中本地化描述，以便让那些说不同语言的人可以使用它。

## 根据我自己的经验，模式优先与代码优先

毫无疑问，我完全支持代码优先的方法。让我解释一下我的立场。

我用 PHP 构建了一个 GraphQL 服务器， [GraphQL by PoP](https://graphql-by-pop.com) ，它只支持代码优先的方法。缺少 schema-first 是因为 PHP 中没有合适的 SDL 解析器，而我自己实现一个解析器将花费大量的精力，我宁愿花在其他任务上。

然而，即使我可以，在我的 GraphQL 服务器中添加模式优先支持可能也没有多大意义，因为模式不是静态的，而是动态的:它可以根据需要变化，通过代码进行管理。动态模式提供了下面描述的所有好处，我非常怀疑通过模式优先的方法能否提供这些好处。

模式的[真值来源可以是 GraphQL 所需真值来源的超集(通过](https://newapi.getpop.org/api/graphql/?query=fullSchema)[GraphQL](https://newapi.getpop.org/graphiql/)和 [Voyager](https://newapi.getpop.org/graphql-interactive/) 可以看到等价的 graph QL 模式)。额外的属性(比如全局字段、全局连接、全局指令和持久片段)已经可以在我们的 API 中使用，而不必等待它们被添加到 GraphQL 规范中(如果有的话)。

因为真理的来源与模式无关，所以我们也可以为任何其他系统生成任何模式；GraphQL 只是目标之一。例如，它可以从相同的事实来源为 REST 服务生成一个 JSON 模式。

API 可以同时是公共的/私有的，这取决于用户是否登录以及登录的用户角色，或者根据一些其他属性提供更多或更少的字段，例如用户是否为专业会员付费。

类型事先不知道它们将解析什么字段。相反，字段解析器使用[发布-订阅模式](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)将自己附加到类型解析器，并且字段解析器可以覆盖其他字段解析器。这个特性使得 API 具有很强的可扩展性，允许我们为自己的 API 编写一个通用代码，并在应用程序级别为特定的客户或项目定制它。

一个字段可以由不止一个字段解析器处理，而是由许多字段解析器处理:链中的每个字段解析器都可以在运行时决定是基于某个属性处理字段，还是沿着链传递字段。

例如，只有当字段参数`"source: testing"`被传递时，才可以使用一个特殊的字段解析器，使得它能够在正式发布之前在生产中的几个站点进行测试。同样的策略也使您能够为特定的客户端或环境提供快速的错误修复，而不会在其他地方冒意外副作用的风险。

上面描述的发布-订阅模式和字段解析器链接特性的组合也使得服务器的实现本身是分散的/联合的。事实上，不需要实现联合，因为联合或非联合 API 的编码方式是一样的，也不需要不同的团队为管理数据模型建立特殊的约定，就像 [Apollo Federation](https://www.apollographql.com/docs/apollo-server/federation/introduction/) 那样。

类型和接口可以[自动命名空间](https://leoloso.com/posts/added-namespaces-to-graphql-by-pop/)以避免来自第三方的冲突。

我将在本系列的后续文章中描述这些策略及其实现。那么，我希望您会相信代码优先方法的优越性。

## 结论

在本文中，我们分析并比较了创建 GraphQL 服务的两种方法:模式优先和代码优先。尽管它们都有积极和消极的特点，基于我自己的经验，我可以说代码优先是更好的选择，因为它允许我们实现否则不可能实现的特性。

我不是一个人有这种感觉。在这条推文中，Prisma 的 Johannes Schickling 预测，从 2020 年开始，代码优先的方法将会比模式优先更受欢迎。

Prisma 有一些经验[创建工具](https://github.com/prisma-labs/)来改善模式优先的固有限制，它创建了 [Nexus](https://nexus.js.org/) 以便通过代码优先的方法通过架构设计直接避免这些问题。因此，我相信约翰内斯肯定知道他在说什么，我会留意他的预测。

在任何情况下，使用模式优先还是代码优先仍然是一个必须基于每个项目具体情况的决定。如果您的团队已经知道如何处理它，或者您的模式永远不会从一个有限的大小开始增长，或者您只需要今天快速启动一些东西，而不需要担心未来，那么模式优先是理想的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

然而，如果您需要扩展模式、联合模式、让自治团队处理模式，或者为特定的客户或项目定制模式，那么模式优先可能会产生一些棘手的问题。如果您认为您的模式最终会变得复杂或变大，那么代码优先是一条可行之路。

## 在生产环境中监控失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.