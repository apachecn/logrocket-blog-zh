# GraphQL 中的字段参数与指令

> 原文：<https://blog.logrocket.com/field-arguments-vs-directives-graphql/>

在 GraphQL 中修改字段输出的相同功能通常可以通过两种不同的方法实现:

1.  字段参数:`field(arg: value)`
2.  查询类型指令:`field @directive`

查询类型指令是应用于客户端查询的指令，而模式类型指令是在服务器端构建模式时通过 SDL 应用的。例如，通过传递带有枚举值`UPPERCASE`的字段参数`format`，可以将`title`字段的响应转换为大写，如下所示:

```
{
  posts {
    title(format: UPPERCASE)
  }
}

```

或者通过将指令`@upperCase`应用于该字段，如下所示:

```
{
  posts {
    title @upperCase
  }
}

```

在这两种情况下，来自 GraphQL 服务器的响应是相同的:

```
{
  "data": {
    "posts": [
      {
        "title": "HELLO WORLD!"
      },
      {
        "title": "FIELD ARGUMENTS VS DIRECTIVES IN GRAPHQL"
      }
    ]
  }
}

```

我们什么时候应该使用字段参数，什么时候应该使用查询端指令？这两种方法之间有什么区别吗，或者当一种选择比另一种更好时的任何情况？在这篇文章中，我们将找到答案。

## GraphQL 中的字段参数和指令有什么区别？

解析 GraphQL 中的字段涉及两种不同的操作:

1.  从被查询的实体获取所请求的数据
2.  对请求的数据应用功能(如格式化)

我们可以将这两种操作分别标记为“数据解析”和“应用功能”，或者简称为“数据”和“功能”。

字段参数和查询类型指令的主要区别在于，字段参数可以用于“数据”和“功能”，而指令只能用于“功能”。

让我们更详细地看看这意味着什么。

## 通过字段参数解析数据

解析字段时处理字段参数；因此，它们可以用来检索实际的数据，比如决定访问对象的什么属性。

例如，这个用于 Apollo GraphQL 的[解析器展示了如何使用参数`size`从对应于类型`Mission`的对象中获取一个或另一个属性:](https://www.apollographql.com/docs/tutorial/resolvers/#define-other-resolvers)

```
Mission: {
  // The default size is 'LARGE' if not provided
  missionPatch: (mission, { size } = { size: 'LARGE' }) => {
    return size === 'SMALL'
      ? mission.missionPatchSmall
      : mission.missionPatchLarge;
  },
},

```

字段参数还可以用来帮助决定必须查询数据库表中的哪一行或哪一列。

在[这个查询](https://newapi.getpop.org/graphiql/?query=%7B%0A%20%20post(id%3A%201)%20%7B%0A%20%20%20%20title%0A%20%20%7D%0A%7D)中，字段参数`id`用于查询`Post`类型的某个特定实体，解析器将把它翻译成 WordPress 的`wp_posts`数据库表中的某个特定行:

```
{
  post(id: 1) {
    title
  }
}

```

同一个表将文章的日期存储在两个不同的列中，`post_modified`和`post_modified_gmt`(出于向后兼容的原因)。在[这个查询](https://newapi.getpop.org/graphiql/?query=%7B%0A%20%20post(id%3A%201)%20%7B%0A%20%20%20%20title%0A%20%20%20%20date(gmt%3Atrue) %0A%20%20%7D%0A%7D)中，用`true`或`false`传递字段参数`gmt`转化为从一个或另一个列中获取值:

```
{
  post(id: 1) {
    title
    date(gmt: true)
  }
}

```

这些示例说明了在解析字段时，字段参数可以修改数据源。

但是，查询类型指令不能用于修改数据源，因为它们的逻辑是通过指令解析器提供的，这些解析器在字段解析器之后调用。因此，在应用该指令时，必须已经检索到该字段的值。

例如，检索单个实体的字段，如`post`、`user`或`product`，必须总是接收一个 ID，否则服务器不知道要检索哪个实体。所以这个查询永远不会工作:

```
{
  post @selectEntity(id: 1) {
    title
  }
}

```

在这种情况下，post 实体的`id`只能通过字段参数提供。因为它丢失了，服务器将因此[返回一个错误](https://newapi.getpop.org/graphiql/?query=%7B%0A%20%20post%20%40selectEntity(id%3A1)%20%7B%0A%20%20%20%20title%0A%20%20%7D%0A%7D):

```
{
  "errors": [
    {
      "message": "Argument 'id' cannot be empty",
      "extensions": {
        "type": "QueryRoot",
        "field": "post @selectEntity(id:1)"
      }
    }
  ]
}

```

总之，只有字段参数可以帮助检索解析字段的数据。

## 通过字段参数或指令应用功能

一旦我们检索了字段的数据，我们可能想要操作它的值。例如，我们可以:

*   格式化字符串，将其转换为大写或小写
*   格式化用字符串表示的日期，从默认的`YYYY-mm-dd`格式到`dd/mm/YYYY`
*   [屏蔽一串](https://blog.logrocket.com/implementing-react-input-mask-web-apps/)，用`***`替换邮件和电话号码
*   如果为`null`或为空，则提供默认值
*   [四舍五入到两位数](https://blog.logrocket.com/store-retrieve-precise-monetary-values-javascript-dinero-js/)

任何这些操作都是对已经检索到的数据的操作。因此，它们既可以在获取数据之后返回数据之前在字段解析器中编码，也可以在指令解析器中编码，后者将获取字段的值作为其输入。因此，任何这些操作都可以通过字段参数或查询类型的指令来实现。

例如，`Post.title`的字段解析器可以通过字段 arg `fallback`提供默认值:

```
// Resolvers
module.exports = {
  Post: {
    title: (post, { fallback } = { fallback: '' }) => {
      return post.title || fallback;
    }
  }
};

```

然后我们可以定制查询中的`fallback` arg 的值:

```
{
  posts {
    title(fallback: "(No title)")
  }
}

```

我们还可以创建一个 [`@default`指令](https://graphql-api.com/guides/directives/default/)，用一个指令解析器，如下所示:

```
/**
 * Replace all the empty results with the default value
 */
function resolveDirective(
  array $directiveArgs,
  array $objectIDFields,
  array $objectsByID,
  array &$responseByObjectIDAndField
): void {
  foreach ($objectIDFields as $id => $fields) {
    $object = $objectsByID[$id];
    $defaultValue = $directiveArgs['value'];
    foreach ($fields as $field) {
      if (empty($responseByObjectIDAndField\[$id\][$field])) {
        $responseByObjectIDAndField\[$id\][$field] = $defaultValue;
      }
    }
  }
}

```

这两种策略同样合适吗？让我们基于不同的兴趣领域来探讨这个问题。

## 使用字段参数与 GraphQL 规范、客户端和工具兼容

GraphQL 规范中没有明确定义指令允许操作的范围[，其内容如下:](https://spec.graphql.org/draft/#sec-Language.Directives)

> 指令提供了一种在 GraphQL 文档中描述可选运行时执行和类型验证行为的方式。
> 
> 在某些情况下，您需要提供选项来改变 GraphQL 的执行行为，以满足字段参数的要求，比如有条件地包含或跳过一个字段。指令通过向执行者描述附加信息来实现这一点。

这个定义同意使用查询类型的指令，比如`@include`和`@skip`——它们分别有条件地包含和跳过一个字段——以及`@stream`和`@defer` —它们为从服务器检索数据提供了不同的运行时执行。

然而，对于修改字段值的查询类型指令，这个定义并不明确，比如将输出值`"Hello world!"`转换为`"HELLO WORLD!"`。

由于这种模糊性，不同的 GraphQL 服务器、客户机和工具可能会在不同程度上考虑指令，从而在它们之间产生冲突。

例如，当使用[继电器](https://relay.dev/)时，我们必须注意到它[不考虑缓存字段值](https://discord.com/channels/625400653321076807/862834364718514196/882933871249862707)的指令。因此，如果使用用于 WordPress 的 [GraphQL API 作为我们的服务器，我们可能需要避免使用它的一些指令，比如](https://graphql-api.com) [`@upperCase`](https://graphql-api.com/guides/directives/uppercase-lowercase-titlecase/) 、 [`@lowerCase`](https://graphql-api.com/guides/directives/uppercase-lowercase-titlecase/) 和 [`@titleCase`](https://graphql-api.com/guides/directives/uppercase-lowercase-titlecase/) 。否则，当使用和不使用指令查询相同的字段时，Relay 可能会使其缓存产生错误的值。

例如，如果首先查询:

```
{
  post(id: 1) {
    title
  }
}

```

中继将查询并缓存 ID 为`1`的帖子的值`"Hello world!"`。如果我们运行这个查询:

```
{
  post(id: 1) {
    title @upperCase
  }
}

```

响应应该是`"HELLO WORLD!"`；然而，Relay 将返回`"Hello world!"`，这是为 ID 为`1`的帖子存储在缓存中的值，忽略应用于该字段的指令。

是否允许指令修改字段的输出值处于灰色区域，因为在 GraphQL 规范中既没有明确允许也没有明确禁止；然而，两种相反的情况都有指标。

一方面，GraphQL 规范似乎给了指令[改进和定制 GraphQL](https://spec.graphql.org/draft/#sec-Language.Directives) 的自由:

> 随着 GraphQL 未来版本采用新的可配置执行功能，它们可能会通过指令公开。GraphQL 服务和工具还可能提供此处描述之外的任何额外的自定义指令。

另一方面，该规范没有将指令考虑到 [`FieldsInSetCanMerge`验证](https://spec.graphql.org/draft/#sec-Field-Selection-Merging)或 [`CollectFields`算法](https://spec.graphql.org/draft/#CollectFields())中。正如[spec 贡献者 Benjie Gillam 指出的](https://discord.com/channels/625400653321076807/862834364718514196/893149862185562262)，下面的 GraphQL 查询是有效的，但不确定用户将获得什么响应:

```
{
  user(id: 1) {
    name
    name @upperCase
    name @lowercase
  }
}

```

根据来自 GraphQL 服务器的行为，字段`name`的响应可能是`"Leo"`、`"LEO"`或`"leo"`——这是一个问题，我们事先不知道它将返回什么。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

使用字段参数时不会出现同样的问题。当执行以下查询时:

```
{
  user(id: 1) {
    name
    name(format: UPPERCASE)
    name(format: LOWERCASE)
  }
}

```

规范指示 GraphQL 服务器返回一个错误，因此`name`的值将是`null`。然后，我们将被迫引入别名来执行查询:

可能正是由于这些原因，我们不鼓励在 GraphQL 生态系统中使用查询类型的指令。例如，GraphQL 工具[建议不要使用它们](https://www.graphql-tools.com/docs/schema-directives#what-about-query-directives)(尽管它仍然允许使用它们):

> 指令语法也可以出现在从客户端发送的 GraphQL 查询中。[…]然而，一般来说，模式作者应该考虑尽可能使用字段参数，而不是查询指令。

总而言之，如果我们想安全起见，我们必须使用字段参数。应该避免使用查询类型的指令，除非我们能够保证所使用的堆栈(GraphQL 服务器、客户端和工具)中没有冲突，或者当 API 仅供内部使用并且我们知道自己在做什么时。在这种情况下，有很好的理由使用查询类型的指令，我们将在下面看到。

## 指令更有利于模块化和代码重用

查询类型指令提供的许多操作不知道它们应用的实体和字段。例如，`@upperCase`可以处理任何字符串，无论是文章标题、用户名、地址还是其他任何内容。因此，该指令的代码只在一个地方实现一次:指令解析器。类似于[面向方面的编程](https://en.wikipedia.org/wiki/Aspect-oriented_programming)，它通过允许横切关注点的分离来增加模块化，指令被应用于字段而不影响字段的逻辑。相比之下，通过字段参数实现相同的功能需要跨不同的字段解析器执行相同的代码:

```
const formatString = (string, format) => {
  if (format === "UPPERCASE") {
    return string.toUpperCase();
  }
  if (format === "LOWERCASE") {
    return string.toLowerCase();
  }
  return string;
};

// Resolvers
module.exports = {
  Post: {
    title: (post, { format }) => {
      return formatString(post.title, format);
    },
    excerpt: (post, { format }) => {
      return formatString(post.excerpt, format);
    },
    content: (post, { format }) => {
      return formatString(post.content, format);
    },
  },
  User: {
    name: (user, { format }) => {
      return formatString(user.name, format);
    },
    description: (user, { format }) => {
      return formatString(user.description, format);
    },
  },
};

```

[中间件](https://www.graphql-modules.com/docs/advanced/middlewares/)可以改善该解决方案的架构:

```
async function middleware({ root, { format }, context, info }, next) {

  const string = await next();

  if (format === "UPPERCASE") {
    return string.toUpperCase();
  }
  if (format === "LOWERCASE") {
    return string.toLowerCase();
  }
  return string;
}

```

但是指令比中间件更加模块化和可重用，因为指令和字段可以完全相互解耦，所以它们只在运行时相互联系，而不是在模式构建时。

因此，可以肯定地说，如果目标是减少解析器中的代码量，那么查询类型的指令比字段参数更合适。

## 指令可能更适合与固执己见的 CMS 一起工作

当通过字段参数提供新功能时，我们需要修改模式并重新编译它。但是当使用一些堆栈时，这可能是不可能的。

例如，WordPress 允许它的用户通过插件注入功能，期望他们不用接触一行代码。

在这种情况下，通过插件提供的指令可以即插即用，因为指令是在运行时应用的。相反，向模式中的相关字段添加参数很可能需要修改代码，这可能会阻止插件在激活后立即工作。

总之，查询类型的指令可能更适合让 GraphQL 遵循它所运行的固执己见的 CMS 的哲学。

## 指令更适合模式设计

添加字段参数会给模式添加额外的信息，可能会使模式膨胀并变得不一致。

例如，需要将字段参数`format`添加到所有的`String`字段中，如果我们不小心的话，它可能在不同的字段中是不同的——它可能使用不同的名称、不同的值、不同的默认值，或者甚至将参数分成几个输入:

```
type Post {
  # Input value is "uppercase" or "lowercase"
  title(format: String): String
  content(format: String): String
  excerpt(format: String): String
}

type Category {
  # Input name is "case" instead of "format"
  # Input value is an enum StringCase with values UPPERCASE and LOWERCASE
  name(case: StringCase): String
}

type Tag {
  # Using a default value
  name(format: String = "lowercase"): String
}

type User {
  # Using multiple Boolean inputs
  description(useUppercase: Boolean, useLowercase: Boolean): String
}

```

指令允许我们尽可能保持模式精简:

```
directive @upperCase on FIELD
directive @lowerCase on FIELD

type Post {
  title: String
  content: String
  excerpt: String
}

type Category {
  name: String
}

type Tag {
  name: String
}

type User {
  description: String
}

```

总之，与使用字段参数相比，使用查询类型指令可以生成更优雅的 GraphQL 模式。

## 指令可能比字段参数更有效

在执行时，将在解析字段时访问字段参数，这发生在逐个字段和逐个对象的基础上。例如，当解析帖子列表中的字段`title`和`content`时，解析器将针对每个帖子和字段调用一次:

```
// Resolvers
module.exports = {
  Post: {
    title: (post, args) => {
      return post.title;
    },
    content: (post, args) => {
      return post.content;
    },
  }
};

```

假设我们想使用 Google Translate API 来翻译这些字符串，为此我们添加了参数`translateTo`:

```
const executeGoogleTranslate = (string, lang) => {
  // Execute against https://translation.googleapis.com
  return ...
};

module.exports = {
  Post: {
    title: (post, { translateTo }) => {
      return executeGoogleTranslate(post.title, translateTo);
    },
    content: (post, { translateTo }) => {
      return executeGoogleTranslate(post.content, translateTo);
    },
  }
};

```

我们最终可能会请求大量到外部 API 的连接，因为逻辑是按照字段和对象的组合自然执行的。这可能会降低解析查询的响应速度。

此外，彼此独立地执行调用将不会允许 Google Translate 关联它们的数据，因此翻译的质量将不如在单个 API 调用中一起提交所有数据的质量。

例如，如果文章内容与文章标题一起提交，文章标题`"Power"`可以被更好地翻译，这使得这个词明显指的是“电力”。

GraphQL 服务器可以选择提供关于指令的更好的体验，其中一个指令可以只被调用一次，传递所有要应用的字段和对象作为输入。这就是 WordPress 服务器 GraphQL API 指令的设计策略，它的`DirectiveResolver` 有[这个签名:](https://github.com/leoloso/PoP/blob/ca19db731eb958128ef017ccb94cae99cbd6445a/layers/Engine/packages/component-model/src/DirectiveResolvers/DirectiveResolverInterface.php#L101)

```
/**
 * All fields and objects are passed together to the executed directive
 */
function resolveDirective(
  array $directiveArgs,
  array $objectIDFields,
  array $objectsByID,
  array &$responseByObjectIDAndField
): void {
  // ...
}

```

通过一起接收所有数据， [`@translate`指令](https://graphql-api.com/guides/directives/translate/)可以执行对 Google Translate 的单个调用，传递所有对象的所有`title`和`content`字段，如[中的查询](https://newapi.getpop.org/graphiql/?query=%7B%0A%20%20posts(limit%3A6)%20%7B%0A%20%20%20%20title%20%40translate(from%3A%22en%22%2C%20to%3A%22fr%22)%0A%20%20%20%20excerpt%20%40translate(from%3A%22en%22%2C%20to%3A%22fr%22)%0A%20%20%7D%0A%7D):

```
{
  posts(limit: 6) {
    title @translate(from:"en", to:"fr")
    excerpt @translate(from:"en", to:"fr")
  }
}

```

总之，尽管不是所有的 GraphQL 服务器都利用了这种架构设计，但是指令可以提供一种更高效的方式来修改字段的值，比如在与外部 API 交互时。

## 结论

如果同样的功能可以通过字段参数或查询类型的指令来实现，我们为什么要选择使用一种方法而不是另一种呢？

查询类型的指令很有价值，它提供了代码的可重用性，并允许我们清理模式。不幸的是，它们目前处于 GraphQL 规范的灰色地带——没有得到完全支持，也没有被禁止。有了这样的模糊性， [GraphQL](https://blog.logrocket.com/tag/graphql/) 服务器、客户端和工具可能会以不同的方式处理指令，可能会在它们之间产生不兼容性。

因此，为了安全起见，建议使用字段参数或查询类型的指令——只要您知道自己在做什么。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.