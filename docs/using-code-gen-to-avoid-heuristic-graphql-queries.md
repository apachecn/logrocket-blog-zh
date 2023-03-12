# 使用 code-gen 避免启发式 GraphQL 查询

> 原文：<https://blog.logrocket.com/using-code-gen-to-avoid-heuristic-graphql-queries/>

我正在构建一个 Nuxt.js 和 GraphQL 应用程序，这时我遇到了一个从未见过的错误:

`You are using the simple (heuristic) fragment matcher, but your queries contain union or interface types. Apollo Client will not be able to accurately map fragments. To make this error go away, use the `IntrospectionFragmentMatcher``

我知道其他 GraphQL 类型，比如[联合](https://graphql.org/learn/schema/#union-types)和[接口](https://graphql.org/learn/schema/#interfaces)，但是除了阅读它们之外，这些都是非常新的。更不用说`IntrospectionFragmentMatcher`这个词有多吓人了。

就像每个开发人员一样，我谷歌了一下我的错误，马上就知道这将是漫长的一天。

我知道你在想什么:“丹尼尔..你是怎么来的？”

嗯，我使用的是一个非常酷的自托管无头 CMS，名为 [Strapi](https://strapi.io/) (具体来说是 GraphQL API)，它有一个非常漂亮的功能，名为[动态区域](https://strapi.io/blog/release-beta-18-dynamic-zones)。

这个特性让您可以动态地将内容放在应用程序的前端，而不必事先过多地担心内容结构。

仍然被这个错误消息困扰着，我检查了我的模式并意识到我用 Strapi 创建的动态区域是一个联合类型。现在事情开始变得有意义了，因为我使用的是 Apollo Client，所以我搜索了他们的文档来寻找我的问题的解决方案。

他们不断地提到某个 code-gen，但是他们给我的解决方案都很模糊，几乎不起作用。

在本文中，我将解释我是如何克服这个错误消息并使我的查询准确地与我的模式中的联合一起工作的，因此您也可以这样做。

对于上下文，Strapi 动态区域允许您从自己创建的组件中进行挑选，并随时将这些组件添加到页面中。对于 GraphQL，每个动态区域都是一个联合类型，包含由您所称的组件定义的组件。

在我的例子中，我有一个名为`pageZone`的动态区域，它返回两个属于自己类型的组件。

希望这能让一切变得更清楚。

## GraphQL 和联合类型

GraphQL 是一种用于 API 的查询语言，对开发者有很多好处。这是一个类型系统，对许多资源的单个请求，它返回您请求的确切数据。

GraphQL 有一种类似于查询语言的模式语言。这使我们能够以一种与语言无关的方式谈论 GraphQL 模式——这意味着无论我们使用什么编程语言，模式语言都允许我们通过 GraphQL API 进行通信和访问数据。

让我们提炼一下:GraphQL 有一个模式，它规定了服务器可以分别执行和返回哪些操作和数据。您的模式具有描述从服务器获取的数据的类型，以及每当客户端连接到它时可用的功能。

其中一种类型是联合——它们是一种抽象类型，表示一个字段可以返回多个对象类型，但本身并不定义特定的字段。

这对我和我的实现意味着什么？当我创建我的动态区域`pageZone`时，其中的每个组件都充当一个字段。我在 I . t .中放置了三个组件——图片、报价和视频，这意味着在我的`pageZone`对象中有三种类型。

```
union pageZone = ComponentImage | ComponentQuote | ComponentVideo
```

每当我们在模式中返回`pageZone`类型时，我们可能会得到一张图片、一段引文或一段视频。这改变了我们开始进行查询的方式，我会不假思索地假设访问我的动态区域中的数据的查询如下所示:

```
...
{
  pageZone {
     ComponentVideo {
          url
        }
     ComponentImage {
          image {
            url
            alt
          }
        }
     ComponentQuote {
          text
        }
  }
}
```

虽然我很希望能够像这样查询联合类型，但是上面显示的方法根本无法工作，因为它在 GraphQL 规范中不受支持。

联合类型的模糊性意味着我们必须指定我们想要的对象类型以及查询中包含的字段。对于我们的场景，正确的查询应该是这样的:

```
...
{
  pageZone {
    ... on ComponentImage {
      image {
        url
        alt
      }
    }
    ... on ComponentQuote {
      text
    }
    ... on ComponentVideo {
      url
    }
  }
}
```

这使得结果取决于我们提出请求时可用的资源。如果我们的`pageZone`中有图像，它会返回给我们。联合对于从单个字段返回不连续的数据类型很有用。

> 为了简洁起见，这是对 GraphQL 类型的一个非常简短的描述。如果您需要更详细的解释和例子，您应该查看关于[模式和类型](https://graphql.org/learn/schema/)的官方 GraphQL 资源。

现在，在设置了 Apollo 客户机之后，出现了一个问题，因为联合类型可以包含所有类型。因此，Apollo 不能确定您所请求的类型是否存在，所以它可能会抛出一个错误。

就像我们之前提到的，我们的动态区域`pageZone`是一个联合类型。Apollo 客户端自带的默认[启发式片段匹配器](https://www.apollographql.com/docs/react/data/fragments/#fragments-on-unions-and-interfaces)在使用带有联合的片段时无法准确工作，正因为如此，我们需要使用`IntrospectionFragmentMatcher`。这让 Apollo 客户机提前知道您的数据是什么样的，以及它有哪些类型。

`IntrospectionFragmentMatcher`查看您的模式并输出所有可能的类型，以便当您发送包含联合类型的查询时，Apollo client 可以放心地返回数据。

> 你可以在[阿波罗碎片](https://www.apollographql.com/docs/react/data/fragments/#fragments-on-unions-and-interfaces)文档中读到更多

为了实现`IntrospectionFragmentMatcher`，我们将使用一个由[行会](https://the-guild.dev)的人构建的工具，名为 [GraphQL 代码生成器](https://graphql-code-generator.com/)。

## 设置 GraphQL 代码生成器

我们实现了`IntrospectionFragmentMatcher`来避免启发式查询。在我们开始之前，我们需要安装它。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在项目目录中，打开终端:

*   运行`yarn add -D @graphql-codegen/cli`–这将安装 GraphQL 代码生成器
*   运行`yarn add -D @graphql-codegen/fragment-matcher`–这将安装片段匹配器插件

安装后，我们可以继续我们的设置

*   创建一个名为`codegen.yml`的文件并粘贴到其中:

```
schema: "<Your GraphQL Endpoint URI>" //eg. http://localhost:1337/graphql
generates:
  ./fragmentTypes.json:
    plugins:
      - "fragment-matcher"
```

> 记得用你的实际端点替换你的 GraphQL 端点 URI 的**。**

 **这个 YAML 文件充当代码生成器的配置存储。您为生成的自省文件定义了模式端点、输出目录和名称。我们的名为`fragmentTypes.json`，可以在项目根目录中找到。

我们的自省文件包含了联合和接口的名称，这样您的 Apollo 客户机就可以在您的模式中准确地使用联合和它们的嵌套类型。

接下来，我们通过粘贴以下内容将代码生成脚本添加到我们的`package.json`中:

```
{
"scripts": {
"generate": "graphql-codegen"
}
}
```

这允许我们在需要时使用`yarn generate`并获得自省结果。

理想情况下，我们希望在构建或运行应用程序时设置脚本来生成自省文件。这样，如果我们向联合类型添加更多的类型，我们可以避免错误，因为我们的内省将总是最新的。

现在您可以继续运行`yarn generate`来创建您的自省文件。

此时，自省文件已经创建，但是我们仍然不能进行准确的查询，因为我们还没有设置 Apollo 客户机。我们需要更改它的配置，以便它检查联合类型的自省文件，然后将它们存储在其缓存中。

我的 Apollo 客户端配置在`./graphql/config.js`中，不同的项目会有所不同。将这段代码粘贴到您的配置文件中:

```
import { InMemoryCache, IntrospectionFragmentMatcher } from 'apollo-cache-inmemory'
import introspectionResult from '~/fragmentTypes.json';

const fragmentMatcher = new IntrospectionFragmentMatcher({
    introspectionQueryResultData: introspectionResult,
  });

export default ({req, app}) => {
    return {
      httpEndpoint: process.env.BACKEND_URL || "<Your GraphQL Endpoint URI>",
      cache: new InMemoryCache({ fragmentMatcher })
    }
}
```

除了通常的端点定义和客户端实例化之外，这段代码还使得 Apollo 客户端读取我们在`./fragmentTypes.json`(来自我们的 GraphQL code-gen)中的自省结果，并将其存储在 Apollo 缓存中。

仅此而已。告别启发式查询！

## 在 Nuxt 前端呈现我们的查询结果

进行准确的查询是一回事。渲染它们的结果是另一回事。

以一个 [Nuxt.js](https://nuxtjs.org/) 应用程序为例。下面的代码说明了如何实现这一点:

```
<template>
  <div class="main">
    <div v-for="contents in content" :key="contents.id">
      <div v-for="zone in contents.pageZone" :key="zone.id" >

        <!-- Display all Image here -->
        <div v-if="zone.__typename === 'ComponentImage'">
          <img :src="zone.image.url" alt="zone.image.alt" />
        </div>

        <!-- Displays all Quotes here -->
        <div v-if="zone.__typename === 'ComponentQuote'">
          <p>{{ zone.text }}</p>
        </div>

        <!-- Displays all Videos here -->
        <div v-if="zone.__typename === 'ComponentVideo'">
          <a :src="zone.url"> Video Link </a>
        </div>

      </div>
    </div>
  </div>
</template>
```

我们将查询结果存储在一个名为`content`的变量中。我们使用`v-for`指令来遍历其中的元素。

动态区域(我们的 Union 类型)数据存储在`contents.pageZone`中，您使用`v-for`来遍历它。您可能已经听腻了，动态区域中的每个组件都有自己的类型。

在上面的代码中，我们通过比较 union 中的`__typename`和 Image 组件的类型，在组件中创建一个区域来显示所有的图像。

> 您可以通过在我们的 GraphQL 游戏中探索我们的模式来获得这一点。

如果匹配，我们可以使用`zone.image.url`显示图像

类似地，对于动态区域中的其他组件，您可以使用相同的方法来创建显示与其类型相匹配的数据的区域。您还可以对添加到动态区域的任何后续组件执行此操作，以便在前端进行渲染。

### 理解在任何前端实现它的逻辑

任何其他前端的配置逻辑都与 Nuxt 中的非常相似。我们做了我们做过的一切。然而，当我们使用不同的前端时，我们的 Apollo 配置可能在不同的目录中。

显示这些数据背后的核心逻辑是:

*   循环查询结果
*   循环通过联合类型
*   通过有条件地显示组件来创建“区域”。这是通过比较查询结果中的类型和我们期望收到的类型来实现的。

很漂亮吧？

## 结论

GraphQL 是一个非常酷的工具，可以让你做很多事情。我们研究了联合类型，并用 Apollo 客户机解决了启发式查询的问题。我们还设置了一个 GraphQL 代码生成器，并将其集成到我们的项目存储库中。希望这将有助于你欣赏工会类型和他们带来的灵活性。

你曾经在项目中使用过联合类型吗？我很想知道！在我的 [Twitter](https://twitter.com/malgamves) 上联系我，分享你的项目、喜欢的类型，或者只是打个招呼。

下次见！

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.**