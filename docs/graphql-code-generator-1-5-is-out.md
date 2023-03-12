# GraphQL 代码生成器 1.5 出来了！

> 原文：<https://blog.logrocket.com/graphql-code-generator-1-5-is-out/>

# GraphQL 代码生成器 1.5 出来了！

## 现在可以生成 Apollo 联邦解析器和完全类型化的 React-Apollo 3.0 钩子

2019 年 8 月 7 日 1 分钟阅读 549

我很高兴地宣布一个新版本的 GraphQL 代码生成器！

新版本有很多修正和新特性(你可以在这里查看完整的变更日志)！

GraphQL 代码生成器是由公会维护的[越来越多的开源库的一部分。如果你仔细研究这些图书馆，你会发现它们很少出现问题，而且维护得非常好。](https://github.com/the-guild-org/Stack)

这要归功于[公会](https://the-guild.dev/)的独特工作模式和我们的连接构建系统——在这个系统中，我们的一些用户可以将他们的构建管道连接到我们的库，我们确保他们保持更新和工作(如果你想加入，你可以[直接联系](https://the-guild.dev/))。

## 阿波罗联盟支持

GraphQL 代码生成器总是支持为后端解析器生成类型，这是最常用的特性之一。

最近我们收到[一份请求](https://github.com/dotansimha/graphql-code-generator/issues/2086)支持**阿波罗联合会**。我们开始研究如何做到这一点， [Kamil Kisiela](https://github.com/kamilkisiela/) 在短短几天内就开始在 GCG 版本 1.5 中实现对阿波罗联邦的支持！

现在，您的 Apollo 联邦服务可以在[类型脚本](https://blog.logrocket.com/writing-idiomatic-typescript/)和流中拥有强类型解析器，只需在您的 codegen [配置](https://graphql-code-generator.com/docs/getting-started/config-field)中启用一个标志:

```
federation: true
```

除了 TypeScript 和 JavaScript，我们还为更多的后端语言生成输出，比如 Java。如果您也想测试这些语言的生成器，请[联系](https://the-guild.dev/)。

## 反应-阿波罗 3.0 挂钩

几个月来，我们不仅支持生成[全类型 react-apollo HOC](https://graphql-code-generator.com/docs/plugins/typescript-react-apollo#withhoc-boolean-default-value-true) 和[查询组件](https://graphql-code-generator.com/docs/plugins/typescript-react-apollo#withcomponent-boolean-default-value-true)，还支持生成由 [react-apollo-hooks 库](https://github.com/trojanowski/react-apollo-hooks/)创建的全类型钩子。

因此，对我们来说，支持基于 react-apollo-hooks 库的新的 [react-apollo 3.0 hooks](https://blog.apollographql.com/apollo-client-now-with-react-hooks-676d116eeae2) 是一个简单的更新。在测试期间，我们也支持测试版的 react-apollo。

不要只生成 TypeScript 类型，而是生成完全类型化的、随时可以使用的钩子。为自己节省大量代码和潜在的错误(比如打字错误或使用错误的接口)，并确保团队中的每个人都具有强类型。

在 [react-apollo 部分](https://graphql-code-generator.com/docs/plugins/typescript-react-apollo)阅读我们[网站](https://graphql-code-generator.com/)上的相关文档，并尝试我们的[全栈 WhatsApp 克隆教程](https://www.tortilla.academy/Urigo/WhatsApp-Clone-Tutorial/master/next/step/9)！

## 使用阿波罗代码？

如果您现在使用的是 [Apollo codegen](https://www.apollographql.com/docs/angular/features/developer-tooling/#apollo-codegen) ，那么迁移到 GraphQL 代码生成器是很容易的。

我们的默认生成输出与 Apollo 的不同，但是因为很多人从 Apollo 迁移到我们的 codegen，我们已经创建了一些配置标志( [near-operation-file](https://graphql-code-generator.com/docs/presets/near-operation-file#docsNav) ，preResolveTypes)，使人们更容易逐渐迁移到最佳实践。

但是你为什么要从阿波罗代码中迁移出来呢？

*   GraphQL 代码生成器得到了更好的维护，尽管我们有了更多的特性，但问题却更少了
*   您可以为前端和后端生成类型
*   有许多语言和不同想法的社区生成器
*   您不仅可以生成类型，还可以生成全功能的 UI 组件
*   GraphQL 代码生成器有一个易于使用的插件系统。我们看到许多团队为他们的产品需求编写他们自己的定制逻辑

## 谢谢你

感谢社区中的每一个人，他们一直在推动我们，与我们合作，连接到我们的互联构建，提交问题、建议、pr 和纯粹的爱！

下一个版本再见！
行会

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.