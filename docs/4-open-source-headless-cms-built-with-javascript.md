# 4 用 JavaScript 构建的开源无头 CMS-log rocket 博客

> 原文：<https://blog.logrocket.com/4-open-source-headless-cms-built-with-javascript/>

根据[维基百科](https://en.wikipedia.org/wiki/Content_management_system)的说法，内容管理系统(CMS)是一个可以用来管理数字内容的创建和修改的应用程序。

CMS 通常由两个主要部分组成:后端，负责创建和管理内容；前端，用于向查看者显示内容。CMS 的一些基本功能包括内容编辑器、用户管理功能和直观的仪表板。

JAMSTACK 的日益流行刺激了无头 CMS 的采用。它们让开发者可以自由选择如何提供内容。

无头 CMS 基本上是一个没有前端层的 CMS。在某些圈子里也被称为 JAMSTACK CMS。前端与 CMS 是分离的，这给了开发人员以任何他们想要的方式构建 CMS 视图层的灵活性。

在本文中，我们将关注面向 JavaScript 开发人员的流行的开源无头 CMS，以及它们的独特之处。

## 《人鬼情未了》

拉开榜单序幕的是 Ghost，这是一款拥有 34.5K GitHub 明星的热门 CMS。

Ghost 是一个 API 驱动的、用 Node.js 构建的无头 CMS，它是一个完全解耦的无头 CMS，这意味着它既可以以无头方式使用，也可以通过模板使用。Ghost 使用一组 REST API 端点来提供其内容，这些端点分为三个主要类别:

*   内容 API
*   管理
*   Webhooks

它还附带了一套现成的工具，包括:

*   Ghost CLI :管理 Ghost 安装和配置的工具
*   **迁移**:帮助你从其他流行博客平台迁移内容的工具
*   JavaScript SDK:一组 JavaScript 包，帮助你用 Ghost API 完成一些常见的任务

要快速开始使用 Ghost，您必须通过`npm`或`yarn`安装 CLI:

```
yarn global add [email protected]
```

成功安装 Ghost CLI 后，您可以运行以下命令，继续在您选择的目录中安装 Ghost:

```
ghost install local
```

## 斯特拉皮

Strapi 是一个开源的 headless CMS，也基于 Node.js，由 100 多个贡献者维护。这是一个完全可定制的 CMS。

它的一些功能包括:

*   **多数据库支持**:支持 SQLite、MongoDB、MySQL、Postgres
*   Webhooks :通知其他应用程序在您的 Strapi 应用程序中发生了一个事件
*   **自动生成的文档**:一键式集成编写和维护文档
*   **认证和权限**:通过允许(或不允许)用户按角色访问您的 API 来保护您的终端
*   **100% JavaScript** :完全由 JavaScript 构建

Strapi 中的数据可以通过 REST API 或 GraphQL 使用。它还为 Heroku、Digital Ocean 和 Platform.sh 等托管平台提供了一键部署选项。

Strapi 的功能可以通过集成 Cloudinary、Mailgun、Algolia、Redis、Sentry 等工具来扩展。

要开始使用 Strapi，请在您的终端中运行下面的代码:

```
yarn create strapi-app my-project --quickstart
```

## Netlify CMS

Netlify CMS 是一个单页 React 应用程序，它为用户提供了一种在任何使用静态站点生成器构建的站点上处理内容的方法。它是一个基于 Git 的 CMS，这意味着数据驻留在 Git 存储库中的文件中，而不是大多数 API 驱动的 CMS，后者从数据库中存储和获取数据。

Netlify CMS 的一些功能包括:

*   **对所有内容的完整版本控制**:您可以完全控制文件的存放位置
*   **快速 web UI** :支持富文本编辑、实时预览和拖放式媒体上传
*   **平台无关**:可用于任何静态站点生成器和前端框架
*   **简单安装**:将两个文件添加到您的网站，并通过将这些文件包含在您的构建过程中或链接到我们的内容交付网络(CDN)来连接后端
*   **现代认证**:使用 GitHub、GitLab 或 Bitbucket 和 JSON web 令牌
*   **灵活的内容类型**:使用自定义字段指定无限数量的内容类型
*   **完全可扩展的**:创建定制风格的预览、UI 部件和编辑器插件
*   **一键式部署** t 到网络

你可以通过将 Netlify 添加到一个现有的站点(T1)或者使用一个 T2 模板(T3)来开始使用它。Netlify CMS 为流行的静态站点生成器提供了一个入门模板。

## 基石 5

Keystone 5 是 Keystone CMS 的最新版本。以前版本的整体架构被彻底改造为 API 驱动的方法。

Keystone 5 是一个用 Node.js 构建的无头 CMS，它是前端不可知的，可以被流行的前端框架、静态站点生成器和移动应用使用。

它也是完全可定制的，允许你选择你需要的特性，比如数据库的类型。管理 UI 非常直观，并根据定义的模式进行更改。

Keystone 5 的一些关键特性包括:

*   **GraphQL API:** Keystone 为您的所有列表提供了强大的 GraphQL API 和 CRUD 操作以及强大的过滤选项
*   **可扩展的管理用户界面**:管理应用程序是一个全功能的管理用户界面，可帮助您管理数据
*   数据库适配器:Keystone 允许您为 MongoDB 和 Postgres 选择不同的存储方法
*   **访问控制**:控制谁可以访问你的 GraphQL API
*   **第三方认证** : Keystone 支持流行的第三方认证，如谷歌、脸书、GitHub 等。，以及由 [PassportJs](http://www.passportjs.org/) 支持的许多其他认证方法

要快速开始使用 Keystone 5，请确保您拥有两个支持数据库之一:MongoDB 和 PostgreSQL。

然后，在您的终端中运行下面的代码

```
yarn create keystone-app my-project
```

按照屏幕提示设置 Keystone。成功安装后，转到应用程序的根目录并运行以下代码:

```
yarn dev
```

上面的命令启动 Keystone 开发服务器。

## 结论

开源的 Headless CMS 作为管理内容的一种有效的替代方式正变得越来越流行。它允许您控制内容的存储方式，以及您希望内容呈现的方式。本文中提到的每个 CMS 都有一套独特的特性。由您根据您的用例来决定什么最适合您的应用程序。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.