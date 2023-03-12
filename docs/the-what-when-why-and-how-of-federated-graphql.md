# 联邦 GraphQL - LogRocket 博客的内容、时间、原因和方式

> 原文：<https://blog.logrocket.com/the-what-when-why-and-how-of-federated-graphql/>

## 介绍

随着 GraphQL [模式拼接](https://www.apollographql.com/docs/graphql-tools/schema-stitching/)的出现，现代[分布式图](https://www.apollographql.com/docs/apollo-server/federation/introduction/)的想法变得流行起来，现在被弃用，取而代之的是[阿波罗联盟](https://www.apollographql.com/docs/apollo-server/federation/introduction/)。

随着[f](https://en.wikipedia.org/wiki/Federated_architecture)融合架构的引入，构建新的统一单一图形 API 的模型(通过组合多个图形模式)变得更加容易。该架构提供了一个[网关层](https://www.apollographql.com/docs/apollo-server/api/apollo-gateway/)，将不同的联邦服务整合到一个统一的 API 端点中。

根据 Principled GraphQL 的说法，一个单独的图形 API 应该被拆分或分散，但在不同地点工作的团队之间可以互操作。主要的好处是，当我们的模式被分割到多个服务中时，并且当它们随着时间的推移而增长或扩展时，通过“网关”层的联合处理合并或组合这些模式的困难部分，而不需要我们进行太多复杂的逻辑或干预。

> 有了联邦，就有了关注点的分离，因为不同的团队可以同时独立地在图的指定部分上工作。

作为与 monoliths 相关的问题的结果，该架构引入了将我们的数据图实现(关注点分离)拆分到多个图形服务或 API 上的想法，这些服务或 API 可以在以后通过“网关”进行组装(协调)

对于上下文，使用与联邦相同的 GraphQL 语法，作为微服务解耦的大规模系统或应用程序有:

*   新工程师可以快速理解和实现，他们可以独立地同时处理图形模式的不同部分
*   更容易通过新功能或错误修复进行修改
*   更易于扩展和部署
*   更易于管理，因为不同服务之间的依赖性和耦合性更低
*   可增量采用的
*   在编程语言支持方面与实现无关

在这里，我们将批判性地检查并强调联邦的组件，在本文结束时，我们应该已经回答了下面概述的问题:

1.  联邦架构是一个新事物吗？
2.  它解决了哪些用其他架构模型难以(或不可能)实现的问题？

在回答这些问题的过程中，我们将了解到:

*   与单片设置/架构相关的问题
*   联合架构的优势。在这里，我们会明白为什么它与众不同
*   功能和用例、组件、设计，最后是示例实现的概要

## 先决条件

为了容易理解，建议对作为查询语言的 [G](https://graphql.org/) [raph](https://graphql.org/) [QL](https://graphql.org/) 有一个基本的了解。此外，理解[图形数据结构](https://en.wikipedia.org/wiki/Graph_(abstract_data_type))的基础知识以及如何用 GraphQL 构建 API 或服务也很有用。为了开始实现联邦架构，我们还应该对[模式拼接](https://www.apollographql.com/docs/apollo-server/federation/migrating-from-stitching/)有一个基本的了解，尽管这不是绝对必需的。

## 联邦的起源

在我们继续之前，让我们先来理解联合架构背后的原因或想法。根据[P](https://principledgraphql.com/integrity#2-federated-implementation)[principled](https://principledgraphql.com/integrity#2-federated-implementation)[G](https://principledgraphql.com/integrity#2-federated-implementation)[raph](https://principledgraphql.com/integrity#2-federated-implementation)[QL](https://principledgraphql.com/integrity#2-federated-implementation)，“虽然只有一个图，但是那个图的实现应该跨多个团队联合。”

简而言之，联邦架构提倡通过 API 公开单个数据图，用于查询由不同微服务组成的所有数据。本质上，我们不必知道数据来自哪个服务。

这样，我们可以用一种声明性的方式编写代码，而不用担心如何将不同的图形服务组合成一个统一的查询接口。为什么这很重要？因此，我们可以通过网关层以高效和优化的方式访问整个数据图，网关层连接和组合来自多个不同服务的数据源。

现在另一个问题出现了:同样的实现是否可以通过其他方式实现，如果可以，是什么让联邦更有性能或者有什么不同？我们将在接下来的部分回答这些问题。

## 巨石柱的问题

单一的图形服务器很难管理和维护。这是因为随着功能或特性集的增长，系统变得越来越紧密耦合。因此，微小的变化会变成巨大的挑战，单个故障点会导致整个系统瘫痪，因为它们相互之间非常依赖。

同样，根据 [G](https://principledgraphql.com/agility#4-abstract-demand-oriented-schema) [拉斐尔](https://principledgraphql.com/agility#4-abstract-demand-oriented-schema) [QL](https://principledgraphql.com/agility#4-abstract-demand-oriented-schema) [P](https://principledgraphql.com/agility#4-abstract-demand-oriented-schema) [原则](https://principledgraphql.com/agility#4-abstract-demand-oriented-schema)，在服务和那些服务的消费者之间应该有一个抽象层。对于 monoliths 来说，情况通常不是这样，最终会导致**可伸缩性**和**适应性**的问题，即使部署在坚实的基础设施上。由于整体的性质，模式与服务实现紧密耦合或绑定，这:

*   随着代码库的发展，降低了模式字段的可重用性
*   导致重构模式的困难
*   由于(通常)巨大的代码库，在维护、部署和扩展这些应用程序方面产生了越来越大的挑战

此外，因为在实现变更时，monoliths 可能会导致开发团队之间缓慢的反馈循环，所以可能会有一些摩擦。这将会降低分布式团队的速度。此外，由于单片往往涉及非常大的代码库，复杂的逻辑增加了应用程序的启动和响应时间。

对于 monoliths 来说，很明显，无论何时对系统进行升级或更改，都会有一段时间的停机时间，不管更改有多小……”

我们还应该注意到，将 monoliths 迁移到不同种类的技术、堆栈或架构最初会带来非常困难的挑战。这是因为整个应用程序需要在升级后进行彻底的测试和重新部署。这使得 monoliths 更加缺乏灵活性和容错性，更不用说难以维护了。

## 联邦的特征

对于基于微服务的架构，我们可以实现具有众多优势的联合。此外，模式是由特性和团队组织的，因此或多或少有一个严格的[关注点分离](https://www.apollographql.com/docs/apollo-server/federation/concerns/)。当我们在下一节讨论联邦的组件时，我们将会理解，尽管存在这种关注点分离，服务如何扩展其他服务中定义的类型。

此外，联邦的另一个亮点是在[增量](https://www.apollographql.com/docs/apollo-server/federation/introduction/#incremental-adoption) [采用](https://www.apollographql.com/docs/apollo-server/federation/introduction/#incremental-adoption)领域。你可以在一个代码库中逐步引入它，使得从一个架构模型转移到另一个架构模型变得容易。例如，为了强调联合宣传，我们可以通过将每个模块分割成一堆微服务，逐渐地将我们的代码库从单一的图形服务器中迁移出来。

> **注意**:其他联邦特性可以从 [Principled GraphQL](https://principledgraphql.com/) 中导出或推断。此外，联邦还提供了其他高级特性，这使得与遗留 API 的集成以及使用主键和复合键等构建复杂模式变得容易。更多细节可以在文档的[这一节中找到。](https://www.apollographql.com/docs/apollo-server/federation/advanced-features/)

## 联邦的组成部分

对于模式拼接，在网关层手动配置各个服务之间的通信或表示关系的过程。然而，在多个服务之间连接数据才是联邦真正的亮点。

连接数据的两个基本技术是[类型引用](https://www.apollographql.com/docs/apollo-server/federation/core-concepts/#referencing-external-types)和[类型扩展](https://www.apollographql.com/docs/apollo-server/federation/core-concepts/#extending-external-types)，它们也被称为指令。在这种情况下，每个单独的 GraphQL 服务器生成一个独立的模式，可以作为独立的组件运行。

> 联邦架构需要向我们的模式字段添加指令和解析器。

基本上，GraphQL 网关层只需要知道如何到达不同的 API 或图形服务，并且它能够自动创建单个图形 API 端点。这是如何工作的？基本上，每个服务都可以通过使用[实体和键](https://www.apollographql.com/docs/apollo-server/federation/core-concepts/#entities-and-keys)来公开它们的 API。这就是服务连接的方式，因为实体允许一个类型被另一个服务引用。

实体和键是联邦图的基础。这里，键基本上充当指令，帮助唯一地标识字段类型的实例。关于这一点的详细信息可以在文档的[部分找到。](https://www.apollographql.com/docs/apollo-server/federation/core-concepts/#entities-and-keys)

此外，因为组织的模式应该是分布式的责任，所以该架构引入了类型引用。在这个场景中，服务可以[从其他服务](https://www.apollographql.com/docs/apollo-server/federation/core-concepts/#referencing-external-types)扩展类型，基本上支持模式合成。

有关联邦的其他核心概念和组件的更多详细信息，请参考 Apollo 文档的本节。

## 设计和实施

实现联合图形架构包括:

1.  不同的图形服务器或模式
2.  统一这些不同的图形模式并将其组合成一个图形 API 端点的网关

想知道这是怎么回事吗？我们继续吧。

网关有一个[查询规划器](https://www.apollographql.com/docs/apollo-server/federation/core-concepts/#extending-external-types)。查询计划面板在 GraphQL 操场上是可见的，在这里我们可以看到网关是如何执行所有请求的。当客户机通过网关发送它们的请求时，网关将这些查询分成更小的块或子查询，遍历每个图模式。正如我们之前所讨论的，网关层已经理解了这些服务之间的相互关系。

基本上，查询规划器帮助规划和执行对所有图模式的查询，然后将它们组合成一个 API 端点供客户机查询。此外，还有一个[图形管理器](https://engine.apollographql.com/)，它有助于跟踪模式的完整历史和使用情况。

对于需要某种特殊工具和支持的高度分布式团队，联邦提供了一个[托管服务](https://engine.apollographql.com/)，它帮助解决一些大规模的特定问题——度量收集、跟踪、模式变更验证等。

> **注意**:一个实现的服务必须符合[联邦规范](https://engine.apollographql.com/)，该规范公开了发生在网关层的所有魔法。

进一步说，在文档中可以找到将现有模式转换成联邦服务的详细设置。此外，还有一个万物联盟的参考 API 文档，也可以在[这里](https://www.apollographql.com/docs/apollo-server/api/apollo-federation/)找到。

现在，让我们使用 GitHub 上的[演示应用程序，快速浏览一下联邦，看看所有部分是如何组合在一起的。](https://github.com/apollographql/federation-demo)

### 安装

像往常一样，我们可以用`npm init`命令启动一个新的节点项目，然后继续安装项目设置所需的所有依赖项。我们可以从安装 [@apollo/gateway](https://www.npmjs.com/package/@apollo/gateway) 、 [apollo-server](https://www.npmjs.com/package/apollo-server) 开始，当然还有最新版本的 [GraphQL](https://www.npmjs.com/package/graphql) 。为此，我们可以运行:

```
npm install @apollo/gateway apollo-server graphql --save
```

> **注**:关于设置的更多细节可以在 [`package.json`文件](https://github.com/apollographql/federation-demo/blob/master/package.json)中找到。还要注意，为了运行不同的服务，我们可以通过运行`npm install concurrently --save`来同时安装。

### 创建网关

在设置网关时，[我们可以看到](https://github.com/apollographql/federation-demo/blob/master/gateway.js)(如`gateway.js`文件中所定义的)我们正在导入 Apollo 服务器和网关。然后，我们实例化一个新的 Apollo 网关，以便将所有的服务组合成一个。之后，我们使用`gateway.load()`函数析构模式和执行器，稍后我们将使用它来启动我们的服务器。

### 类型引用和扩展

如果我们看一下[服务文件夹](https://github.com/apollographql/federation-demo/tree/master/services)，我们可以看到几个服务，它们稍后将被组合以形成单个图形 API 端点。

在 [`accounts service`文件夹](https://github.com/apollographql/federation-demo/tree/master/services/accounts)中，我们需要设置 API。为此，我们可以安装运行服务所需的所有依赖项:

```
npm install @apollo/federation apollo-server graphql --save
```

然后，在`index.js`文件中，我们可以看到[的整个设置](https://github.com/apollographql/federation-demo/blob/master/services/accounts/index.js)。我们会注意到，我们在这个服务中扩展了用户类型。`user`服务现在是一个基于`@key`指令的实体，可以在其他服务中扩展和[解析](https://www.apollographql.com/docs/apollo-server/api/apollo-federation/#__resolvereference)。

例如，如果我们导航到[评论服务](https://github.com/apollographql/federation-demo/blob/master/services/reviews/index.js)，我们将看到我们是如何使用之前在账户服务中指定的用户账户实体的。我们还可以注意到，从用户服务中指定的虚拟数据数组中，有`ids`，它是惟一的，可以在需要时用于引用其他服务中的这些字段。

此外，我们正在解构 Apollo 服务器和类型定义。为了构建一个联邦模式，我们可以看到我们正在导入`[buildFederatedSchema](https://www.apollographql.com/docs/apollo-server/api/apollo-federation/#buildfederatedschema)`，这对包装解析器很有用，然后是我们之前从 Apollo 服务器导入的类型定义。

## 结论

联邦架构的确是一个新事物，因为它以一种高度优化的方式简化了将来自不同微服务的多个模式集合在一起并将其合并到一个统一的图形 API 中的问题。

虽然这在基于整体的架构中也是可能的，但是有许多缺点；因此，作为构建现代高度分布式系统的首选，它很快变得不那么有吸引力了。

在本文中，我们已经了解了联合架构如何简化开发人员的生活，以及如何简化独立但同步工作的大型分布式团队的生活。随着这些改进和进步，可以肯定地说，生态系统将继续看到更多改进的功能。

此外，以前被认为难以实现的想法——或者更确切地说，实现——正在变得更简单、更容易实现，尽管是在一些抽象层之上加上了语法糖。

最后，我们可以在考虑微服务不是唯一解决方案的情况下做出关于联合的决定，因此，架构决策或选择总是需要某种程度的权衡。

请通过我的 [Twitter](https://twitter.com/alex_nnakwue) 与我分享您的观点。另外，请在下面的评论区问我任何问题——我很乐意回答。感谢阅读🙂

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.