# 5 个用于 JavaScript 和 Node.js 的 GraphQL 客户端

> 原文：<https://blog.logrocket.com/5-graphql-clients-for-javascript-and-node-js/>

GraphQL 是一种用于 API 的查询语言和运行时。它使客户端能够指定查询，并允许服务器根据强类型模式验证数据。

与 REST APIs 不同，GraphQL 对所有操作使用单个端点。基于 GraphQL 的服务器只能通过 GraphQL 查询进行通信。对于简单的项目，使用 REST 客户端发送 GraphQL 查询是可行的，比如 [Axios 或`fetch()`](https://blog.logrocket.com/axios-or-fetch-api/) 。然而，对于具有高级需求的更大、更复杂的项目，需要一个 GraphQL 客户端。

GraphQL 客户端通过提取小细节和实现附加功能(其中一些包括缓存、查询批处理和重复数据删除，以及 GraphQL 查询结果的静态类型)来简化通信。

在本指南中，我们将比较以下五个 GraphQL 客户端，并从功能、社区支持和大小方面对每个客户端进行评估。

我们开始吧！

## `graphql-request`

`[graphql-request](https://graphql.org/code/)`是一个非常小的 GraphQL 客户端。该库是同构的，这意味着它支持 Node.js 和浏览器。它有一流的打字支持，所以打字永远不会过时。该库仅重 5.2 kb(T2)。

### 活动

在撰写本文时， [3.7K stars](https://github.com/prisma-labs/graphql-request) 的 GitHub 库活动是健康的。在 npm 上，`graphql-request`是[在撰写本文时每周下载超过 130 万次](https://www.npmjs.com/package/graphql-request#whats-the-difference-between-graphql-request-apollo-and-relay)，所以你在网上寻找帮助应该不会有任何问题！

### 赞成的意见

`graphql-request`易于使用，几乎没有学习曲线:

```
import { GraphQLClient, gql } from 'graphql-request'
const query = gql`{
  hero {
    name  
  }
}`
const client = new GraphQLClient('<graphql-endpoint>')
const data = await client.request(query)

```

*   适用于服务器和客户端
*   支持类型脚本
*   非常轻便
*   强大的社区支持

### 骗局

*   不支持查询缓存和重复数据删除等高级功能
*   不支持 GraphQL 订阅

## 阿波罗客户端

[Apollo 客户端](https://www.apollographql.com/docs/react/)是一个高级的 GraphQL 客户端，仅用于前端。它包括缓存、查询批处理、查询重复数据删除和分页功能。它还可以用来代替 Redux 存储管理本地状态。基库重约 [33.9kB](https://bundlephobia.com/package/@apollo/client@3.3.6) 。

Apollo 客户端是用 TypeScript 编写的，所以 TypeScript 支持非常出色。它集成了流行的框架和库，如 React、Next.js、Angular 和 vue . js。Apollo Client 还有一个用于检查和查询的 [Chrome 扩展](https://chrome.google.com/webstore/detail/apollo-client-devtools/jdkknkkbebbapilgoeccciglkfbmbnfm?utm_source=chrome-ntp-icon)。

### 活动

阿波罗客户端每月至少更新一次。它在 npm 上每周下载[超过 100 万次](https://www.npmjs.com/package/apollo-client)，在撰写本文时在 GitHub 上拥有超过 [16K 颗星星和 2K 个分叉](https://github.com/apollographql/apollo-client)。

### 赞成的意见

*   强大而全面
*   满足大多数使用案例
*   支持类型脚本
*   流行 UI 库和框架的集成
*   iOS 和 Android 的集成
*   用于检查查询的 Chrome 扩展

除了在 npm 和 GitHub 上的活动，Apollo Client 还享有在线支持，其形式是专门的[社区论坛](https://community.apollographql.com)。它也有来自[阿波罗 GraphQL 公司](https://www.apollographql.com)的商业支持

### 骗局

*   查询批处理、持久化查询和缓存等高级功能的学习曲线很陡

## urql

urql 的目标是通过内置的缓存机制和合理的默认设置，既[易用又](https://blog.logrocket.com/why-i-finally-switched-to-urql-from-apollo-client/)可扩展。除了在服务器端使用 Node.js 的核心库之外，urql 还有 Preact、react 和 Svelte 的客户端。

urql 的 bundle 尺寸很小，[只有 7.1kB](https://formidable.com/open-source/urql/docs/comparison/) 重。它是少数几个内置离线支持的 GraphQL 库之一。

### 活动

在撰写本文时，urql 库在 npm 上每周[被下载 95K 次](https://www.npmjs.com/package/urql)，在 GitHub 上，urql 已经[收到超过 6K 颗星星](https://github.com/FormidableLabs/urql)。

### 赞成的意见

urql 是健壮的，但对于基本功能来说仍然很容易使用:

```
const QUERY = `
  query Test($id: ID!) {
    getUser(id: $id) {
      id
      name
    }
  }
`;
const result = client.readQuery(QUERY, { id: 'test' });

```

*   清晰易读的文档
*   支持类型脚本
*   许多流行的 UI 库和框架的集成
*   用于检查查询的浏览器扩展
*   内置脱机支持
*   小捆尺寸

### 骗局

*   不支持角度
*   不支持查询批处理
*   没有内置的分页支持
*   与其他 GraphQL 客户端相比，社区规模较小

## 继电器

Relay 是一个由脸书开发的高性能和可伸缩的 GraphQL 客户端。

正如您可能猜到的那样，Relay 是在考虑 React 的情况下构建的。因此，它采用基于组件的方法来获取数据。Relay 是非常固执己见的，并且严格用于 React 前端。中继的[包大小为 47.1 kB](https://bundlephobia.com/package/react-relay@11.0.2) 。

### 活动

在撰写本文时，Relay 在 npm 上每周至少被下载[94K 次](https://www.npmjs.com/package/react-relay)，并在 GitHub 上收到了超过 15K 颗星的[和超过 1.5K 个叉的](https://github.com/facebook/relay)。

### 赞成的意见

*   缓存、查询重复数据消除、分页和本地状态管理的高级功能
*   与 React 紧密集成，赋予它更高阶的组件和钩子
*   受到脸书和 Quora 等顶级公司的信任

### 骗局

*   只有支架起作用
*   非常固执己见，因为它僵化的传统
*   与其他客户相比，学习曲线更陡
*   文档很难解析
*   中继客户端只能与兼容[中继的](https://blog.logrocket.com/making-a-graphql-server-compatible-with-relay/) GraphQL 服务器通信

通过执行一个严格的惯例，接力减少了犯错的空间，这可能是积极的，也可能是消极的。

## `graphql-hooks`

`[graphql-hooks](https://www.nearform.com/blog/introducing-graphql-hooks/)`是 React 的一个非常小的 GraphQL 客户端。尽管很小，`graphql-hooks`却拥有一流的缓存、分页、认证、文件上传和服务器端渲染支持。它的包大小[仅重 3.6kB](https://bundlephobia.com/package/graphql-hooks@5.2.0) 。

客户端提供了`useQuery`、`useMutation`和`useSubscription` React 钩子，分别用于处理查询、突变和订阅。官方文档包括从阿波罗客户端迁移[的指南。](https://www.apollographql.com/docs/react/migrating/hooks-migration/)

### 活动

`graphql-hooks`在 npm 上每月下载 [6K 次，与 GitHub](https://www.npmjs.com/package/graphql-hooks) 上的 [1.5K 明星一起。](https://github.com/nearform/graphql-hooks)

### 赞成的意见

*   支持类型脚本
*   对缓存、分页和 SSR 等高级功能的支持是现成的
*   非常小的尺寸
*   API 的表面积很小

### 骗局

*   只有支架起作用
*   小社区

## 为您的项目选择正确的 GraphQL 客户端

下表总结了上面讨论的信息。

|  | CT2 团结
支持 | 学习
曲线 | 大小 | 打字稿打字 | 分页、查询缓存、
批处理和重复数据删除 | 同构支持 | 与 UI 库和框架的集成 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `graphql-request` | 伟大的 | 低 | 5.2 kB | 是 | 不 | 是 | 不 |
| 阿波罗客户端 | 伟大的 | 中等 | 33.9 kB | 是 | 是 | 仅限客户端 | 反应过来，下一个。js，Angular，Svelte，Ember，web components，
和 Vue |
| urql | 公平的 | 低 | 7.1 kB | 是 | 不支持批处理 | 是 | 反应，苗条，和 Vue |
| 继电器 | 公平的 | 高 | 47.1 kB | 不 | 是 | 仅限客户端 | 仅反应 |
| `graphql-hooks` | 低的 | 低 | 3.6 kB | 是 | 不支持批处理和重复数据删除 | 仅限客户端 | 仅反应 |

为你的项目选择最好的客户很大程度上取决于你个人的目标和需求。但是，您可以使用下面列出的准则作为起点:

*   `graphql-request`:需求简单的服务器端或前端项目
*   阿波罗客户端:高级前端项目
*   urql:运行服务器端的缓存等高级特性
*   Relay:项目是基于 React 的，需要高级特性，并且包括许多组件
*   项目是基于 React 的，不需要高级特性

## 结论

GraphQL 客户端使得与 GraphQL APIs 的交互比直接调用它们容易得多。

每个 GraphQL 客户端都有其优点和缺点。选择一个能够为您的用例提供最佳特性的客户端对于一个平稳的开发体验是至关重要的。选择最佳的客户端将取决于您独特的项目，并不总是有一个显而易见的答案。希望这篇文章中的信息能帮助你开始！

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.