# 选择最好的 Next.js 托管平台

> 原文：<https://blog.logrocket.com/choosing-best-nextjs-hosting-platform/>

### 目录

1.  [使用 Vercel 托管 Next.js 应用](#hosting-nextjs-apps-with-vercel)
2.  [使用 Netlify 托管您的应用](#use-netlify-to-host-your-app)
3.  [Heroku 作为 Next.js 托管平台](#heroku-as-a-nextjs-hosting-platform)
4.  [韦尔塞尔 vs .奈特利菲 vs .赫罗库:哪一个最适合你？](#vercel-vs-netlify-vs-heroku-which-one-is-best)

有这么多的选择，选择一个托管解决方案可能会觉得有点势不可挡。开发人员问自己的一些最常见的问题是:

*   有免费计划吗？如果有，我能用它做什么？
*   这项技术受支持吗？
*   包括 SSL 吗？
*   静态 vs 服务器渲染？
*   无服务器功能呢？

这通常会导致花费数小时来分析文档，并且非常耗时。

因此，本文旨在帮助您进行搜索。不同的项目有不同的需求，但在本指南中，我们将介绍三种可能的 Next.js 托管解决方案，包括 Vercel、Netlify 和 Heroku。

## 使用 Vercel 托管 Next.js 应用

如果不提到 Vercel，就很难提到 Next.js 项目可用的托管解决方案。

由 Next.js 的创始人创建的 Vercel 正是基于这一技术而构建的。因此，对于 Next.js 开发人员来说，这是一个很有吸引力的选择。他们最大的客户包括 Meta、优步和 Auth0。为了发展他们的产品并提供有趣的功能，Vercel 在 2020 年 12 月筹集了 4000 万美元。

### Vercel 的特点

在这些新特性中，最明显的是对[增量静态再生](https://vercel.com/docs/concepts/next.js/incremental-static-regeneration)的内置支持。这加快了构建速度，因为它允许开发人员更新一些页面，而无需重新构建整个站点。

Vercel 还提供对[无服务器函数](https://vercel.com/docs/concepts/functions/serverless-functions)的支持，因为它是在考虑 Next.js 的情况下创建的，所以它会自动获取`pages/api`中的所有 API 路径并创建这些函数。[边缘功能](https://vercel.com/docs/concepts/functions/edge-functions)支持也是可用的，尽管它仍处于测试阶段。

说到部署，部署 Next.js 项目非常容易。无论你使用 GitHub、 [GitLab](https://about.gitlab.com/) 还是 [Bitbucket](https://bitbucket.org/) ，你都可以快速连接你的账户，导入任何存储库。导入后，您可以设置自动部署。这意味着每次代码被推送到一个分支时，项目将自动重新构建并重新部署自己。

部署之后，Vercel 为前端开发人员提供了一些简洁的特性。第一个是[预览模式](https://vercel.com/docs/concepts/next.js/preview-mode)。此功能允许前端团队在一个唯一的 URL 上实时推送代码和预览这些部署。

一旦获得批准，这些部署就可以投入生产。这对于经常需要依靠截图或者织机来回顾和预览变更的团队来说非常有帮助。

前端开发者可能感兴趣的第二个特性是[分析](https://vercel.com/docs/concepts/next.js/analytics)。类似于[灯塔实验室的报告](https://blog.logrocket.com/google-web-vitals-single-page-apps/)，这给你的网站一个基于某些指标的满分 100 分，比如第一次内容丰富的绘画的时间。

它将跟踪这些指标，并为您显示一个仪表板来监控它们。然而，有一个限制。使用自由计划时，只能在一个项目上启用分析。

在免费计划中，您还可以获得 SSL 和设置自定义域名的能力。

当然，Vercel 也有一些缺点。与更大的主机解决方案不同，Vercel 不提供任何数据库插件。因此，需要第二个解决方案来托管您的数据。

最后，免费计划中没有商业网站。因此，如果你正在运行一个电子商务，你会自动需要一个付费计划。

**TL；博士**:

### 使用 Vercel 的优势

*   由 Next.js 的创建者构建
*   增量静态再生内置支持
*   无服务器和 Edge(测试版)功能支持
*   易于部署
*   使用 Github/GitLab/Bitbucket 轻松配置
*   自动部署是可用的
*   每个构件的预览模式
*   可用分析(自由计划中只有一个项目)
*   免费计划中可用的 SSL
*   免费计划中提供的自定义域名设置

### 使用 Vercel 的缺点

*   没有数据库加载项
*   支付商业用地的义务

感兴趣吗？下面是部署过程:[https://vercel.com/docs/concepts/next.js/overview](https://vercel.com/docs/concepts/next.js/overview)

## 使用 Netlify 托管您的应用程序

部署 Next.js 项目的另一个有趣的选项是 Netlify。像 Vercel 一样，这个主机提供商提供对[增量静态再生](https://docs.netlify.com/integrations/frameworks/next-js/#key-features)的支持，通过按需重建页面来加快构建速度。

无服务器功能也是可用的。他们使用 JavaScript、 [TypeScript](https://blog.logrocket.com/building-serverless-app-typescript/) 或 Go。不像 Vercel，它从你的`pages/api`文件夹中自动提取你的函数，你需要把它们放在一个`functions`文件夹中。然而，设置应该很简单，并且[有文档可以帮助你](https://docs.netlify.com/functions/overview/)。

在 Netlify 上部署非常容易。通过连接您的 GitHub/Bitbucket/GitLab 帐户，您可以轻松地导入任何存储库并部署您的站点。部署完成后，您可以设置自动部署，这样每个推送到分支的代码都将重新构建您的项目。

Netlify 优于 Vercel 的地方是他们提供的附加组件列表。其中， [Netlify Forms](https://www.netlify.com/products/forms/) 允许开发者无需额外代码即可管理表单和提交。它甚至可以与 MailChimp、Zendesk 等第三方应用程序集成。

另一个有趣的功能是[分割测试](https://docs.netlify.com/site-deploys/split-testing/)，尽管它仍处于测试阶段。有了这个，开发者可以用不同的部署来测试他们的产品。

最后，与 Vercel 不同的是，免费计划允许商业项目，这对电子商务开发者来说非常好。

网络生活有一些缺点。首先，分析是一项付费功能，每个网站每月收费 9 美元。第二是不提供数据库附件。要托管您的数据，您将需要另一个解决方案。

**TL；博士**:

### 使用网络生活的优势

*   增量静态再生内置支持
*   实时预览模式
*   无服务器功能
*   易于部署
*   使用 GitHub 轻松配置
*   自动部署是可用的
*   许多插件和集成
*   自由计划允许商业项目

### 不足之处

*   分析是一项付费功能
*   没有数据库加载项

感兴趣吗？下面是部署过程:[https://www.netlify.com/with/nextjs/](https://www.netlify.com/with/nextjs/)

## Heroku 作为 Next.js 托管平台

开发人员或创业公司中流行的托管解决方案是 Heroku。虽然它不是为 Next.js 构建的，但是有一个[构建包](https://elements.heroku.com/buildpacks/mars/heroku-nextjs)，因此您可以轻松地部署您的项目。

在 Heroku 上部署您的项目相当容易。通过 Heroku CLI，您可以用几个命令部署一个项目。此外，像许多托管解决方案一样，您可以配置您的 GitHub/Bitbucket/GitLab 帐户并设置自动部署。

对于 Next.js 项目，您将需要一个额外的配置步骤来绑定您的端口，但是除此之外，在 Heroku 上的部署相当简单。

部署之后，有许多附加组件可供开发人员使用。在这些插件中，你可以找到 Postgres 和 Redis。对于寻找一站式商店的人来说，Heroku 是一个有吸引力的选择。

然而，Heroku 也有一些缺点。首先，尽管有它的构建包，Heroku 将把你的项目作为 Node.js 应用程序运行。因此，您将失去 Next.js 的一些最有趣的特性，比如增量静态再生。分析被指标和测量的吞吐量、响应时间和内存使用(仅针对付费计划)所取代。

在免费计划中，Heroku 提供设置自定义域名的功能。另一方面，不包括 SSL。免费计划中的 Web 应用程序也会在一段时间不活动后进入休眠状态。

**TL；博士**:

### Heroku 的优势

*   易于部署到 Heroku
*   使用 GitHub 轻松配置
*   自动部署是可用的
*   Postgres 和 Redis 等几个附加软件
*   免费计划中可用的海关域设置

### 不足之处

*   无增量静态再生支持
*   Heroku web dynos 在一段时间不使用免费计划后会进入睡眠状态
*   SSL 仅适用于付费计划
*   绑定端口需要额外的配置
*   没有网络分析，只有指标(付费计划)
*   不支持无服务器功能

下面是[部署流程](https://elements.heroku.com/buildpacks/mars/heroku-nextjs)。

## 韦尔塞尔 vs .奈特利菲 vs .赫罗库:哪一个最适合你？

为了便于参考，这里有一个比较 Vercel、Netlify 和 Heroku 特性的快速表格。

| **特性** | 韦尔塞尔 | **Netlify** | **Heroku** |
| 增量静态再生(ISR) | 是 | 是 | 不 |
| 无服务器功能 | 是 | 是 | 不 |
| 易于部署 | 是 | 是 | 是 |
| 使用 Github/GitLab/Bitbucket 进行配置 | 是 | 是 | 是 |
| 自动部署 | 是 | 是 | 是 |
| 预览模式 | 是 | 是 | 不 |
| 分析学 | 是 | 每个站点/月 9 美元 | 度量而不是分析 |
| 自定义域 | 是 | 是 | 是 |
| 自由域上的 SSL | 是 | 是 | 不 |
| 免费计划上的商业网站 | 不 | 是 | 不 |
| 附加组件 | 没有人 |  | 研究生院

*   再说一遍
*   阿帕奇卡夫卡
*   结论

 |

## 在本文中，您发现了三种针对 Next.js 的托管解决方案:Vercel、Netlify 和 Heroku。对于它们中的每一个，本指南比较了对无服务器功能的支持、增量静态重新生成和部署过程的简易性。

如果您想要更多选择，还有其他未讨论过的解决方案，但值得一试，例如:

最后，没有神奇的解决方法。你决定使用的托管平台很大程度上取决于你的项目和它的需求。感谢阅读。

[LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

## 调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。

Modernize how you debug your Next.js apps — [start monitoring for free](https://lp.logrocket.com/blg/nextjs-signup).