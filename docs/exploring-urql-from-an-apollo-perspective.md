# 从阿波罗视角探索 urql

> 原文：<https://blog.logrocket.com/exploring-urql-from-an-apollo-perspective/>

我已经与 GraphQL 合作了一年多，主要是与北欧的视频点播(SVOD)服务 C More 合作，客户数据由 GraphQL 提供。我们一直在 web 客户端上使用 react-apollo，最近看到 GraphQL 库 urql 弹出来激起了我的兴趣，特别是它与 apollo 相比如何。

urql 代表 Universal React Query Language，每周有约 2500 次下载，截至 2019 年 7 月，apollo 的下载量约为 50 万次。虽然它还没有太多的用途，但这个项目有一些诱人的方面。稍后会有更多的内容——首先，我想花点时间思考一下为什么图书馆可能是一个好主意。

## 什么是 GraphQL，为什么我们需要一个库？

GraphQL 是一种查询语言，在这种语言中，客户机向服务器请求它所需要的东西——不多也不少。您可以把它看作是发送一个包含 JSON 对象的所有键的字符串，服务器应该为您填充这些键。查询可能是这样的:

```
query {
  series(id: 3446) {
    title
    year
    suggestedEpisode {
      title
      episodeNumber
    }
  }
}

```

它会返回:

```
{
  "data": {
    "series": {
      "title": "Game of Thrones",
      "year": 2019,
      "suggestedEpisode": {
        "title": "Winterfell",
        "episodeNumber": 1
      }
    }
  }
}

```

GraphQL 有三种不同的操作类型:查询、变异和订阅。查询用于请求数据，突变用于改变数据，订阅用于实时数据。由于我在订阅方面的经验有限，所以我不会对 urql 如何处理它做出判断，而是专注于更常见的操作:查询和变异。

GraphQL 查询和变异通常在 HTTP 上使用，并经常通过`POST`请求发送(通常也支持`GET`)。GraphQL 查询通常与任何变量一起在请求正文中发送。

那么为什么你需要一个库来做这件事呢？要做简单的事情，你不需要——你可以做简单的`fetch`呼叫，它会工作得很好。如果我从一个[节点](https://blog.logrocket.com/design-patterns-in-node-js-2/)服务器或者从一个有一些(很少被调用)GraphQL 请求的简单模块调用它，这是我使用 GraphQL 的首选方式。我觉得它作为真正简单用例的替代方案经常被忽视。

那么图书馆给了我们什么呢？GraphQL 对所有数据使用类型系统，这为一些客户端缓存提供了可能性。缓存，以及一些关于数据获取的实用工具，是一个库可以节省你很多时间的地方。因此，让我们来看看 urql 如何为我们节省时间。

## 使用 urql

正如我前面提到的，urql 是 React 的一个轻量级、可扩展的 GraphQL 客户端。它存在的全部原因是使客户端的 GraphQL 尽可能简单，正如在[初始提交](https://github.com/FormidableLabs/urql/commit/b97d64ab7ffabe5be7439135282553151c3585ea#diff-04c6e90faac2675aa89e2176d2eec7d8)中看到的。这立即反映在安装中；你只要输入`npm install urql graphql`。

然后你做一些小的设置:

```
import { Provider, createClient } from "urql"

const client = createClient({
  url: "http://localhost:1234/graphql",
  *// you can also add more customizations here,
  // such as setting up an authorization header.

  // Advanced customizations are called "Exchanges",
  // and also go here if you need more advanced stuff.*
})

ReactDOM.render(
  <Provider value={client}>
    <YourApp />
  </Provider>,
  document.body
)

```

现在您已经准备好在您的组件中使用 urql 了！

### 提出疑问

urql 支持组件 API 和钩子 API。组件 API 基于渲染道具，由一个`<Query>`、一个`<Mutation>`和一个`<Subscription>`组件组成。最基本的情况是这样的:

```
function RenderPropVersion() {
  return (
    <Query
      query={`{
        # 4711 would normally be passed as a variable
        # (in all the following examples too, not just this one)
        movie(id: 4711) {
          title
          isInFavoriteList
          year
        }   
      }`}
    >
      {({ fetching, data }) =>
        fetching ? (
          <div className="loader">Loading..</div>
        ) : (
          <div className="json">
            {JSON.stringify(
              data,
              null,
              2
            ) /* The (_, null, 2) makes JSON.stringify pretty. */}
          </div>
        )
      }
    </Query>
  )
}

```

钩子 API 由`useQuery`、`useMutation`和`useSubscription`钩子组成，我们上面的组件看起来和钩子一样:

```
function HookVersion() {
  const [result] = useQuery({
    query: `{
      movie(id: 4711) {
        title
        isInFavoriteList
        year
      } 
    }`,
  })
  const { fetching, data } = result
  return fetching ? (
    <div className="loader">Loading..</div>
  ) : (
    <div className="json">{JSON.stringify(data, null, 2)}</div>
  )
}

```

注意钩子版本是如何少了一个缩进层次的。作为曾经用 Apollo 编写过三层组件的人，我只想说我喜欢这样。在接下来的突变部分，您会很高兴 Hooks API 的存在。

当 React 呈现一个 urql `<Query>`或`useQuery`时，urql 会查看查询和任何变量，并检查是否缓存了该查询的结果。在这种情况下，结果会立即呈现。否则，它发送一个请求来填充缓存(这个行为可以用`requestPolicy`属性/参数来修改)。

### urql 缓存

因为从 GraphQL 库获得的主要好处是缓存，所以我认为对您选择的库如何为您处理缓存有一个合适的心理模型是很重要的。

在 urql 中，查询的结果被精确的查询缓存(甚至字段的顺序也很重要！)以及映射到该查询结果的任何变量。没有奇迹发生——从输入到输出是一个`Map`。

当数据因突变而改变时，缓存失效。当 urql 从 GraphQL 服务器获得变异响应时，urql 会查看响应中存在哪些类型。任何包含这些类型的缓存数据都将失效，任何缓存失效的当前呈现的查询都将重新提取。

没有对缓存的手动访问。缓存是在后台完成的，这一切都是为了方便用户。

### 变异数据

如果您熟悉查询数据，从 API 的角度来看，使用 urql 改变数据是非常简单的。上面的 Hooks 版本添加了两个变体，看起来像这样:

```
function HookVersion() {
  const [result] = useQuery({
    query: `{
      movie(id: 4711) {
        title
        isInFavoriteList
        year
      } 
    }`,
  })

  *// Mutations added here! (imagine if this was render props 😰)*
  const [addFavoriteResult, addFavorite] = useMutation(`mutation {
    addMovieToFavoriteList(id: 4711) {
      title
    }
  }`)
  const [removeFavoriteResult, removeFavorite] = useMutation(`mutation {
    removeMovieFromFavoriteList(id: 4711) {
      title
    }
  }`)

  const { fetching, data } = result

  *// <button> added in render*
  return fetching ? (
    <div className="loader">Loading..</div>
  ) : (
    <>
      <button
        onClick={() => {
          if (data.movie.isInFavoriteList) {
            removeFavorite()
          } else {
            addFavorite()
          }
        }}
      >
        {data.movie.isInFavoriteList ? "Remove favorite" : "Add favorite"}
      </button>
      <div className="json">{JSON.stringify(data, null, 2)}</div>
    </>
  )
}

```

请记住，缓存的失效是基于变异响应中包含的类型。作为一名 urql 用户，这意味着您必须考虑 GraphQL 服务器返回的内容。

想象一下，如果`removeMovieFromFavoriteList`突变返回所有标记为喜爱的电影的完整列表。这似乎不是太不合逻辑，因为你*是*有效地改变了最喜欢的电影列表。然而，这被证明是一个坏主意。

这是一个坏主意的原因是它会导致一个错误！下面的场景说明了可能发生的错误:用户删除了收藏列表中的最后一项，因此用户不再有任何标记为收藏的电影。变异响应(收藏夹列表)将是一个空数组。

空数组不包含任何类型。这意味着 urql 不会使正确的查询缓存失效，数据也不会与服务器同步。

也就是说，无论使用什么库，返回 GraphQL 查询中实际发生变化的内容总是一个好主意。从上面的例子中，Apollo 也会得到一个陈旧的缓存。

在这种情况下，更好的反应是我们标记为最喜欢的电影。这样，响应将总是包含类型，urql 可以使正确的缓存无效。

## urql 和 Apollo 的区别

阿波罗可能是当今最著名和最受欢迎的 GraphQL 库，也是我所知最多的库。因此，继续进行简单的比较似乎是合乎逻辑的。

### 哲学

urql 只有一个包，相比之下，react-apollo 需要五个以上的包(然而，你可以使用 [apollo-boost](https://github.com/apollographql/apollo-client/tree/master/packages/apollo-boost) ，它给你一个与 urql 相似的设置体验)。

库的文件大小也不同:apollo-boost + react-apollo 的文件大小为 91kB + 35kB，而 urql 的文件大小为 21.5kB(缩小，通过 [BundlePhobia](https://bundlephobia.com/) 检查)。这些不同的事实反映了他们的指导思想和目标。

urql 是轻量级和可扩展的，信任开源社区来解决利基问题，如[持久化查询](https://github.com/FormidableLabs/urql/issues/196)，这是一种请求大小优化，其中 GraphQL 查询存储在服务器上，并且只发送一个哈希。Apollo 是一家公司，感觉他们希望自己有一个解决每个问题的方案。

两者都是有效的哲学，但是当你选择你的图书馆的时候考虑一下是有价值的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 应用程序接口

在评估 API 时，它们看起来非常相似。创建一个连接到您的端点的客户机，将它挂接到一个`<Provider>`，并在您的组件中使用查询、变异和订阅。

两个库都公开了`<Query>`、`<Mutation>`和`<Subscription>`渲染道具组件来与您的 API 一起工作。urql 还支持`useQuery`、`useMutation`和`useSubscription`挂钩。Apollo 也创建了一个 Hooks API，但是还没有文档化。

目前，React 悬念还没有发布，但我们可以确定两个库都将支持它。API 是否不同，或者仅仅是移除了`fetching`状态，还有待观察。

Apollo 有很多 urql 没有的 API。例如，Apollo 允许您直接访问缓存。如果您使用的 GraphQL 模式不返回缓存失效所需的类型，这将非常有用。

您可以在 urql 中通过(ab)使用`requestPolicy`参数/prop 来解决此类问题，但我认为在 Apollo 中使用此类模式更好。

### 贮藏

缓存可能是 Apollo 和 urql 最大的不同之处。Apollo 对其缓存进行了规范化，这意味着从 GraphQL 返回的每一项都按照其`id`和类型进行缓存。这个组合是一个不错的启发，因为你不能只通过`id`缓存(一个`User`和`Movie`可能有相同的`id`)。Apollo 还在查询级别进行缓存——如果您对缓存的外观感兴趣，我建议您下载 Apollo devtools，在那里您可以检查缓存。

规范化缓存意味着，如果两个不同的查询在同一个页面上有相同的条目，改变其中一个就会改变另一个；它们都是从规范化缓存中呈现的。

然而，Apollo 的缓存有一个缺点。想象一下，我们正在显示一个标记为喜爱的电影列表，以及另一个电影列表(新发布的或类似的)，其中每部电影都有一个**标记为喜爱的**按钮，其当前的喜爱状态(是的，现在是一个词)在每部电影上可见。

如果我们点击那个按钮，电影改变了它的受欢迎状态，GraphQL 服务器将返回更新的`Movie`和更新的`isInFavoriteList`字段。这将更新`Movie`的偏好状态，但该电影不会出现在您最喜欢的电影列表中，因为更新的列表不是响应的一部分。

使用 urql 的缓存策略就不会出现这个问题。正如我前面说过的，urql 的缓存方法更简单:它在查询级别缓存，而不是每个单独的项。为了确保缓存在突变后不会过时，它只是清除所有查询的缓存，这些查询返回一些与突变返回的类型相同的项。

urql 缓存方法可能适用于某些站点，但不适用于其他站点。比如 C More(北欧的流媒体服务，还记得吗？)要清除所有`Movie`项的缓存，如果你在单个`Movie`上使用了突变`addMovieToFavoriteList`或`markAsSeen`，基本上会清空整个缓存。

## 结论

说实话，看到 urql 和 Apollo 如此相似，我很惊讶。urql 更简单，但是缺少一些现成的特性，比如持久化查询。urql 在从缓存中删除内容方面也相当自由，所以如果您有一个突变多、数据类型少的应用程序，urql 缓存策略可能并不适合您。

然而，由于 Apollo 和 urql APIs 如此相似，从 urql 更改为 Apollo 应该不会非常复杂。如果当您遇到需要规范化缓存或持久化查询等特性的问题时，您可以进行重写，而不会花费太多成本。

因此，如果您正在寻找一个简单的库来帮助您使用 GraphQL，我绝对推荐您尝试一下 urql。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)