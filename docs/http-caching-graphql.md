# GraphQL 中的 HTTP 缓存

> 原文：<https://blog.logrocket.com/http-caching-graphql/>

GraphQL 和缓存:这两个词放在一起不太合适。

原因是 GraphQL 通过对单个端点执行所有查询并通过请求体传递参数来通过`POST`进行操作。单个端点的 URL 将产生不同的响应，这意味着它不能被缓存——至少不能使用 URL 作为标识符。

“但是等一下，”你说。" GraphQL 肯定有缓存，对吗？"

是的，在客户端通过 [Apollo Client](https://github.com/apollographql/apollo-client) 和类似的库来完成，这些库独立地缓存返回的对象，通过它们唯一的全局 ID 来标识它们。

但这是黑客行为。这种解决方案之所以存在，只是因为 GraphQL 无法处理服务器中的缓存，为此我们通常使用 URL 作为标识符，并一起缓存响应中所有实体的数据。

客户端中的缓存有几个缺点:

*   应用程序获得了更多的 JavaScript 来运行在客户端。通过低端手机访问网站会影响性能
*   应用程序变得越来越复杂，有了更多可移动的部分，因为现在我们还需要担心缓存层的实现
*   不是每个人都懂 JavaScript(例如，网站可能是用 PHP 编写的)，但是现在处理 JS 也变成了一种责任

那么解决办法是什么呢？

简单来说，就是使用标准。在这种情况下，标准是 [HTTP 缓存](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)。

“是的，但这就是问题的关键——我们不能使用 HTTP 缓存！或者我们在说什么？”

没错。但是知道我们想要使用 HTTP 缓存，我们就可以从不同的角度来处理这个问题。而不是问“我们如何缓存 GraphQL？”我们可以问，“为了使用 HTTP 缓存，我们应该如何使用 GraphQL？”

在这篇文章中，我们将回答这个问题。

## 通过`GET`访问 GraphQL

使用 HTTP 缓存意味着我们将使用 URL 作为标识符来缓存 GraphQL 响应。这有两层含义:

1.  我们必须通过`GET`访问 GraphQL 的单个端点
2.  我们必须将查询和变量作为 URL 参数传递

然后，如果单个端点是`/graphql`，就可以针对 URL `/graphql?query=...&variables=...`执行`GET`操作。

这适用于从服务器检索数据(通过`query`操作)。对于变异数据(通过`mutation`操作)，我们仍然必须使用`POST`。这里没有问题，因为突变总是在新的时候执行；我们不能缓存变异的结果，所以我们无论如何也不会使用 HTTP 缓存。

这种方法是可行的(甚至在官方网站中也有[的建议)，但是我们必须记住一些注意事项。](https://graphql.org/learn/serving-over-http/#get-request)

## 通过 URL 参数编码 GraphQL 查询

一个 GraphQL 查询通常会跨越多行。例如:

```
{
  posts {
    id
    title
  }
}
```

但是，我们不能在 URL param 中直接输入这种多行字符串。

解决方法是对其进行编码。例如，GraphiQL 客户端将对上面的查询进行编码，如下所示:

```
%7B%0A%20%20posts%20%7B%0A%20%20%20%20id%0A%20%20%20%20title%0A%20%20%7D%0A%7D
```

好吧，这个有用。但是看起来不太好吧？谁能理解这个查询？

GraphQL 的优点之一是它的查询非常容易理解。通过一些练习，一旦我们看到查询，我们就能立即理解它。但是一旦它被编码，所有的都消失了，只有机器能理解它；人类不在考虑范围内。

另一个解决方案是用空格替换查询中的所有换行符，这很有效，因为[换行符不会给查询](https://spec.graphql.org/draft/#sec-Line-Terminators)增加语义。那么，[上面的查询](https://newapi.getpop.org/graphiql/?query=%7B%20posts%20%7B%20id%20title%20%7D%20%7D)可以表示为:

```
?query={ posts { id title } }
```

这对于简单的查询非常有效。但是，如果您有一个非常长的查询，打开和关闭许多花括号，并添加字段参数和指令，那么它会变得越来越难以理解。

例如，[这个查询](https://newapi.getpop.org/graphiql/?query=%7B%0A%20%20posts(limit%3A5)%20%7B%0A%20%20%20%20id%0A%20%20%20%20title%20%40titleCase%0A%20%20%20%20excerpt%20%40default(%0A%20%20%20%20%20%20value%3A%22No%20title%22%2C%0A%20%20%20%20%20%20condition%3AIS_EMPTY%0A%20%20%20%20)%0A%20%20%20%20author%20%7B%0A%20%20%20%20%20%20name%0A%20%20%20%20%7D%0A%20%20%20%20tags%20%7B%0A%20%20%20%20%20%20id%0A%20%20%20%20%20%20name%0A%20%20%20%20%7D%0A%20%20%20%20comments(%0A%20%20%20%20%20%20limit%3A3%2C%0A%20%20%20%20%20%20order%3A%22date%7CDESC%22%0A%20%20%20%20)%20%7B%0A%20%20%20%20%20%20id%0A%20%20%20%20%20%20date(format%3A%22d%2Fm%2FY%22)%0A%20%20%20%20%20%20author%20%7B%0A%20%20%20%20%20%20%20%20name%0A%20%20%20%20%20%20%7D%0A%20%20%20%20%20%20content%0A%20%20%20%20%7D%0A%20%20%7D%0A%7D%0A):

```
{
  posts(limit:5) {
    id
    title @titleCase
    excerpt @default(
      value:"No title",
      condition:IS_EMPTY
    )
    author {
      name
    }
    tags {
      id
      name
    }
    comments(
      limit:3,
      order:"date|DESC"
    ) {
      id
      date(format:"d/m/Y")
      author {
        name
      }
      content
    }
  }
}
```

会变成这样一行查询:

```
{ posts(limit:5) { id title @titleCase excerpt @default(value:"No title", condition:IS_EMPTY) author { name } tags { id name } comments(limit:3, order:"date|DESC") { id date(format:"d/m/Y") author { name } content } } }
```

再一次，[它工作了](https://newapi.getpop.org/graphiql/?query=%7B%20posts(limit%3A5)%20%7B%20id%20title%20%40titleCase%20excerpt%20%40default(value%3A%22No%20title%22%2C%20condition%3AIS_EMPTY)%20author%20%7B%20name%20%7D%20tags%20%7B%20id%20name%20%7D%20comments(limit%3A3%2C%20order%3A%22date%7CDESC%22)%20%7B%20id%20date(format%3A%22d%2Fm%2FY%22)%20author%20%7B%20name%20%7D%20content%20%7D%20%7D%20%7D%20)，但是我们不知道我们正在执行的是什么。如果查询也包含片段，那就完全忘记它——我们无法理解它。

那么，我们能做些什么呢？

## HTTP 上的 GraphQL

首先，好消息是:来自 GraphQL 社区的利益相关者已经发现了这个问题，并且已经开始着手制定 HTTP 上的 GraphQL 规范，这将使每个人(GraphQL 服务器、客户机、库等)都标准化。)将通过 URL 参数传递它们的 GraphQL 查询。

其次，不太好的消息是:这项工作的进展似乎很慢，而且到目前为止的规范还不够全面，无法使用。因此，我们要么等待一段不确定的时间，要么寻找另一种解决方案。

### 持久化查询来拯救

如果在 URL 中传递查询不令人满意，我们还有什么其他选择？为了不在 URL 中传递查询！

这种方法被称为“持久查询”我们将查询存储在服务器中，并使用一个标识符(比如一个数字 ID 或一个通过将查询作为输入应用散列算法而产生的唯一字符串)来检索它。最后，我们将这个标识符作为 URL 参数传递，而不是作为查询。

例如，可以用 ID `2908`(或者一个散列，比如`"50ac3e81"`)来标识查询，然后我们对 URL `/graphql?id=2908`执行`GET`操作。然后，GraphQL 服务器将检索对应于这个 ID 的查询，执行它，并返回结果。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

使用持久化查询，实现 HTTP 缓存就不成问题了。

问题解决了！如果您想在您的 GraphQL 服务器中使用 HTTP 缓存，请找到一个支持持久化查询的 GraphQL 服务器，无论是本机查询还是通过某个库查询。

### 计算`max-age`值

迎接下一个挑战！

HTTP 缓存的工作方式是在响应中发送`Cache-Control`头，用一个`max-age`值表示响应必须被缓存的时间，用`no-store`表示不缓存。

考虑到不同的字段可以有不同的`max-age`值，GraphQL 服务器将如何计算查询的`max-age`值？

答案是获取查询中请求的所有字段的`max-age`值，并找出最低的值。这将是响应的`max-age`。

例如，假设我们有一个类型为`User`的实体。根据分配给该实体的行为，我们可以分配相应的字段应该被缓存多长时间:

🛠它的 ID 永远不会改变我们给字段`id`一个 1 年的`max-age`

🛠它的 URL 会随机更新(如果有的话)我们给字段`url`一个 1 天的`max-age`

🛠这个人的名字可能会不时改变(例如，添加一个状态，或者说“Milton(戴面具)”)我们给 field `name`一个 1 小时的`max-age`

🛠用户在网站上的因果关系可以随时改变(例如，在有人投票支持他们的评论之后)我们给 field`karma`1 分钟的时间

🛠如果查询来自登录用户的数据，那么响应根本不能被缓存(与我们获取的字段无关)`max-age`必须是`no-store`

因此，对以下 GraphQL 查询的响应将具有以下`max-age`值(在本例中，我们忽略了字段`Root.users`的`max-age`，但在实践中，它也会被考虑在内):

| 询问 | `max-age`值 |
| --- | --- |
| 

```
{   users {     id   } }
```

 | 1 年 |
| 

```
{   users {     id     url   } }
```

 | 1 天 |
| 

```
{   users {     id     url     name   } }
```

 | 1 小时 |
| 

```
{   users {     id     url     name     karma   } }
```

 | 1 分钟 |
| 

```
{   me {     id     url     name     karma   } }
```

 | `no-store`(不缓存) |

#### 添加计算`max-age`值的指令

GraphQL 服务器如何计算响应的`max-age`值？因为这个值将依赖于查询中的所有字段，所以有一个显而易见的候选方法:指令。

一个[模式类型的指令](https://blog.logrocket.com/graphql-directives-are-underrated/#directivesnotequal)可以分配给一个字段，我们可以通过指令参数定制它的配置。

因此，我们可以创建一个带有类型为`Int`(测量秒)的参数`maxAge`的指令`@cacheControl`。用值`0`指定`maxAge`相当于`no-store`。如果未提供(该参数已被定义为非强制性的)，则使用预定义的默认值`max-age`。

我们现在可以配置我们的模式来满足前面为所有字段定义的`max-age`。使用模式定义语言(SDL)，它将看起来像这样:

```
directive @cacheControl(maxAge: Int) on FIELD_DEFINITION

type User {
  id: ID @cacheControl(maxAge: 31557600)
  url: URL @cacheControl(maxAge: 86400)
  name: String @cacheControl(maxAge: 3600)
  karma: Int @cacheControl(maxAge: 60)
}

type Root {
  me: User @cacheControl(maxAge: 0)
}
```

#### 编码`@cacheControl`指令

我将展示我为 WordPress 的服务器 [GraphQL API 实现的`@cacheControl`指令，它是用 PHP 编写的。(这个服务器既有本地的](https://graphql-api.com)[持久化查询](https://graphql-api.com/features/#heading-persisted-queries)又有 [HTTP 缓存](https://graphql-api.com/features/#heading-http-caching)。)

指令的[解析非常简单:它只是从指令参数中获取`maxAge`值，并将其注入到名为`CacheControlEngine`的服务中:](https://github.com/leoloso/PoP/blob/f805ccafd6240a3467582ff73286b02c0722c25b/layers/Engine/packages/cache-control/src/DirectiveResolvers/AbstractCacheControlDirectiveResolver.php#L113)

```
public function resolveDirective(): void
{
  $maxAge = $this->directiveArgsForSchema['maxAge'];
  if (!is_null($maxAge)) {
    $this->cacheControlEngine->addMaxAge($maxAge);
  }
}
```

每当注入一个新的`max-age`值时，`CacheControlEngine`服务将[计算较低的值](https://github.com/leoloso/PoP/blob/710a57a7570d452f5745ec2242f245f501b80c9d/layers/Engine/packages/cache-control/src/Managers/CacheControlEngine.php#L17)并将其存储在其状态中:

```
class CacheControlEngine
{
  protected ?int $minimumMaxAge = null;

  public function addMaxAge(int $maxAge): void
  {
    if (is_null($this->minimumMaxAge) || $maxAge < $this->minimumMaxAge) {
      $this->minimumMaxAge = $maxAge;
    }
  }
}
```

该服务然后可以[生成`Cache-control`报头](https://github.com/leoloso/PoP/blob/710a57a7570d452f5745ec2242f245f501b80c9d/layers/Engine/packages/cache-control/src/Managers/CacheControlEngine.php#L42)，带有响应的`max-age`值:

```
class CacheControlEngine
{
  public function getCacheControlHeader(): ?string
  {
    if (!is_null($this->minimumMaxAge)) {
      // Minimum max-age = 0 => `no-store`
      if ($this->minimumMaxAge === 0) {
        return 'Cache-Control: no-store';
      }
      return sprintf(
        'Cache-Control: max-age=%s',
        $this->minimumMaxAge
      );
    }
    return null;
  }
}
```

最后，GraphQL 服务器将从服务中获取`Cache-Control`头，并将其添加到响应中。

## 结论

在关于 GraphQL 是否比 REST 更好(反之亦然)的无休止的争论中，REST 总是有一张王牌:服务器端缓存。

但是我们也可以让 GraphQL 支持 HTTP 缓存。所需要做的就是将查询存储在服务器中，然后通过`GET`访问这个“持久化查询”,将查询的 ID 作为 URL 参数提供。这是一种非常合理、非常值得的权衡。

GraphQL 和缓存:两个非常相配的词。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.