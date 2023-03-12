# Deta vs. Heroku:寻找更好的云提供商

> 原文：<https://blog.logrocket.com/deta-vs-heroku-finding-better-cloud-provider/>

选择云服务提供商时，速度、可伸缩性、成本和安全性都应该考虑，因为这些变量对您的应用程序的成功有着巨大的影响。因此，你必须尽最大努力来决定哪一个最适合你。

在本帖中，我们将比较最受欢迎的云服务提供商之一 Heroku 和相对较新且完全免费的替代产品 Deta。我们将探索这两个平台提供的服务和功能、它们的定价模式以及它们的开发人员体验，即在这些平台上托管应用程序的体验。

## 什么是 Heroku？

Heroku 是一个云平台即服务(PaaS)，最初由詹姆斯·林登鲍姆、亚当·威金斯和猎户座·亨利在 2017 年创立。该平台最初开发时仅支持使用 Ruby 编程语言创建的项目，但自 2010 年被 Salesforce 收购以来，该平台增加了对多种编程语言的支持，包括 Java、Node.js、Scala、Clojure、Python、PHP 和 Go。

![Heroku Cloud Platform](img/034a029af4de6f24a2d49d694d27301f.png)

### Heroku 特征

Heroku 提供了一系列工具和服务来轻松扩展和部署您的应用程序。其中包括:

#### 戴森

几乎每个云服务提供商都对他们的云容器有一个唯一的名称；Heroku 的是 Dyno。

Dynos 是任何 Heroku 应用程序的基本组件。它们是独立的虚拟 Linux 容器，提供运行应用程序所需的所有 CPU、内存和操作系统资源。

Heroku 还消除了为应用程序配置硬件资源和虚拟机的压力。无需担心硬件管理，您只需将您的应用发布到 Heroku，该平台会将您的所有应用代码和依赖项打包到 dynos。

#### Add-ons

Heroku 还允许您使用各种附加组件来扩展您的项目，这些附加组件是 Heroku 和第三方开发人员提供的额外云服务，用于为您的应用程序添加更多功能，如缓存、数据存储(包括对 Postgres、MongoDB 和 MySQL 等的支持)、事件调度、日志记录等。

#### 运行经验

Heroku 提供的另一个有趣的功能是操作体验平台，它允许您专注于维护应用程序的可靠性，同时提供出色的用户体验。

该平台有助于监控您的应用程序的健康状况、验证其响应能力，并在请求失败时生成主动警报，甚至在您的用户看到它们之前。

### ![Heroku Operational Experience Platform](img/e70e4e88dcd6bb16079b2febfa7c846e.png)

### 定价

Heroku 提供了一个慷慨的免费 dyno 计划，其中包括一个自定义域和一个免费的 512MB 内存供应。但是，您的应用程序将在 30 分钟不活动后休眠。这意味着，如果没有向您的应用程序发送请求，或者没有每 30 分钟访问一次它的 URL，应用程序将休眠，下一个请求将需要 10 秒钟才能加载。

根据你项目的要求，其他的计划从每月 7 美元到超过 500 美元不等。更多信息，请查看他们的[定价页面](https://www.heroku.com/pricing)。

### 开发者体验

在 Heroku 上托管应用程序的过程相当简单。一种选择是用 Heroku 和项目开发平台(如 GitHub 或 GitLab)建立一个 CI/CD 管道；另一种选择是使用 Heroku 命令行界面，这是一种更快的方法来创建新项目，以及直接从命令行管理之前的所有项目。

使用 Heroku CLI，您还可以简单地搜索市场上可用的任何附加组件并将其添加到您的项目中，甚至可以在部署项目供公众访问之前在本地测试项目。

## 什么是 Deta？

deta——其主页上提到的“实干家和梦想家的云”——是一家相对较新的完全免费的云服务提供商。它提供了一个开发人员友好的界面，允许您在几秒钟内将程序部署到云中。虽然该平台目前仅支持托管 Node.js 和 Python 项目，但未来对其他编程语言的支持将不会令人惊讶。

![Deta Cloud Platform](img/1995b0424303484752cf6cebaa1392d3.png)

### 特征

#### Deta Micros

Deta 托管的应用，像 Heroku 的 Dyno，叫做 micros 这是一个沙盒 Linux 虚拟机，每个进程执行的内存超过 128MB，还有一个只读文件系统，存储空间高达 512MB。

不像 Heroku 只允许你运行两个免费的 dynos，你可以在 Deta 上创建无限数量的 micros。但是，应用程序源代码及其依赖项的大小不能超过 250MB。

Micros 目前只支持 Node.js 和 Python。同样值得注意的是，两种语言的旧版本都不被支持；这种情况将来可能会改变，但目前支持以下版本:

*   Nodejs: 12.x，14.x
*   Python: 3.7，3.8，3.9

#### Deta 基地

Deta 并不支持 Postgres 或 MySQL 等关系数据库，因此该团队开发了他们自己的安全 NoSQL 数据库，专为他们平台上的应用程序设计。

Deta Base 是一个快速、可扩展的 NoSQL 数据库，优先考虑最终用户的便利性。它有一个用户界面和一个允许你管理和查询数据库记录的 SDK。该数据库以 Javascript、Python 和 Go SDK 的形式提供，语法相当简单。以下是如何在数据库中插入和删除记录的示例:

```
const { Deta } = require('deta');

// Project key retrieved from deta dashboard
const deta = Deta('your-project-key'); 

// Connect to or create a new 'users' database.
const users = deta.Base('users'); 

// Add new user to the database.
users.put({name: "Elijah", state: 'Lagos'})

// delete user with key 'one'
const res = await db.delete("one")
```

#### 数据驱动

Deta Drive 是一种安全且可扩展的文件存储解决方案，是该平台提供的另一项服务。它与 Deta Base 非常相似，只是 Deta Drive 侧重于文件/资产管理，如图像、视频和其他类型的媒体，而 Deta Base 侧重于数据管理。所有帐户都被限制在 10GB 的存储空间，考虑到它是免费的，这是一个相当慷慨的提议，并且存储在驱动器上的所有文件都可以直接从您的仪表板或通过他们的 SDK 以编程方式进行管理。

### 定价

与 Heroku 不同，Deta 免费提供其所有服务，您不需要提供任何信用卡详细信息或其他敏感的支付信息即可开始使用。

### 开发者体验

像 Heroku 一样，Deta 也有集成的 CLI 来管理所有现有项目或创建新项目。与 Heroku 不同，Deta 目前不支持 CI/CD，这意味着您不能将 GitHub 或 GitLab 项目同步到 Deta 来反映新的更改。

## 最后的想法

在整篇文章中，我们将最受欢迎的云服务提供商之一 Heroku 与完全免费的新选择 Deta 进行了比较。两个平台都提供出色的服务。

然而，就目前而言，Deta 的启动和运行速度更快，而 Heroku 应该考虑用于企业应用程序。此外，由于 Deta 仍然相对年轻，我们应该期待未来会有很多有趣的更新。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)