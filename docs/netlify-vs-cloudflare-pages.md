# Netlify vs. Cloudflare 页面- LogRocket 博客

> 原文：<https://blog.logrocket.com/netlify-vs-cloudflare-pages/>

无服务器技术帮助开发者通过云提供商部署和管理应用。这可以最小化成本，因为开发人员不会为他们不使用的东西付费，他们也不需要维护服务器。

Netlify 是一个强大的无服务器平台，具有直观的基于 Git 的工作流、自动化部署和可共享的预览，自 2014 年以来一直是无服务器平台的主要参与者。

然而，随着 Cloudflare 在 2021 年 4 月推出 Cloudflare Pages，这是一个供前端开发人员协作和部署网站的 Jamstack 平台，Cloudflare Pages 已经越来越多地被社区采用。

而且，最近在 2021 年 11 月，Cloudflare Pages 宣布它现在是一个全栈平台，与 Netlify 形成直接竞争，因为开发人员现在可以一起创建后端功能和前端代码。

通过这次竞赛，在本文中，我们将讨论两者在开发人员经验、性能和构建时间方面的比较，以帮助您在部署自己的 Jamstack 网站时做出明智的决策。

我们将比较:

## 第三方注册服务

第三方注册服务是用户名/密码认证的替代方案，在衡量入职体验的成功与否时至关重要。当比较 Netlify 和 Cloudflare 时，Netlify 比 Cloudflare 更容易注册。

Netlify 允许用户通过 GitHub、GitLab、Bitbucket 和 email 等第三方注册或登录，而 Cloudflare Pages 只允许电子邮件注册和登录。

因为大多数开发人员已经在他们的浏览器上登录到他们的版本控制平台，注册或登录 Netlify 更方便，因为它使用那些版本控制平台的凭证。然后，开发人员可以轻松地从这些平台部署代码。

虽然 Cloudflare Pages 提供了与 GitHub 和 GitLab 的集成，但通过电子邮件登录的过程会使使用 Cloudflare Pages 变得繁琐。

## 在 Netlify 和 Cloudflare 中使用函数

在为动态特性添加功能时，Netlify 和 Cloudflare 页面都遵循相同的规则，例如要求我们将所有内容创建一个`./functions`文件夹，包括动态特性。

现在让我们看看如何在 Cloudflare Pages 和 Netlify 中路由和编写无服务器函数。

### Cloudflare 页面中的功能路由

在 Cloudflare 页面中，使用`./functions`目录会根据目录中的文件生成一个路由表。您可以使用 JavaScript ( `*.js`)或 TypeScript ( `*.ts`)来编写您的函数。

例如，假设目录结构如下:

```
├── ...
├── functions
|   └── api
│       ├── [[path]].js
│       ├── [username]
│       │   └── profile.js
│       ├── time.js
│       └── todos
│           ├── [[path]].js
│           ├── [id].js
│           └── index.js
└── ...

```

下面的路由将基于文件结构生成，将 URL 模式映射到被调用的`./functions`文件:

```
/api/time => ./functions/api/time.ts
/api/todos => ./functions/api/todos/index.ts
/api/todos/* => ./functions/api/todos/[id].ts
/api/todos/*/** => ./functions/api/todos/[[path]].ts
/*/profile => ./functions/api/[username]/profile.ts
/** => ./functions/api/[[path]].ts

```

### 在 Cloudflare 中编写函数

在您的 Cloudflare Pages 应用程序中编写请求处理程序时，每个`./functions`文件必须`export`一个函数来处理传入的请求。然后，每个函数接收一个单一的`context`对象，其中包含请求的所有信息:

```
//js
export async function onRequest(context) {
  // Contents of context object
  const {
    request, // same as existing Worker API
    env, // same as existing Worker API
    params, // if filename includes [id] or [[path]]
    waitUntil, // same as ctx.waitUntil in existing Worker API
    next, // used for middleware or to fetch assets
    data, // arbitrary space for passing data between middlewares
  } = context;

  return new Response("How you dey?");
}

```

在上面的代码示例中，我们导出了一个`onRequest`函数。这是一个通用名称，因为它一般处理所有 HTTP 请求。

但是，为了响应特定的 HTTP 请求方法，您可以使用方法名作为导出函数的后缀。例如，应该只接收`GET`请求的处理程序应该被命名为`onRequestGet`。以下是 Cloudflare Pages 支持的其他处理程序:

1.  `onRequestPost`
2.  `onRequestPut`
3.  `onRequestPatch`
4.  `onRequestDelete`
5.  `onRequestHead`
6.  `onRequestOptions`

这些是您为编写第一个函数而导出的请求。例如，当您向`/functions/greetings.js`文件中的`/hello-world`发出 post 请求时，您可以编写一个输出“Hello World”的函数:

```
//functions/hello-world.js
// Reacts to POST /hello-world
export async function onRequestPost(request) {
  // ...
  return new Response(`Hello world`);
}

```

### 网络中的功能路由

默认情况下，Netlify 中的所有函数都是通过以下方式部署的:

*   us-east-1 AWS Lambda 区域(这也是它们仍被称为 Netlify Lambda 函数的原因)
*   1024 兆内存
*   同步功能的 10 秒执行限制
*   [后台功能](https://blog.logrocket.com/how-to-send-emails-react-app-netlify-functions/#background-netlify-functions)的 15 分钟执行限制

在 Netlify 中创建函数，首先要创建一个`./functions`文件夹；请注意，您可以将该文件夹命名为任何名称。

然后我们需要在根目录下创建一个`netlify.toml`文件。这告诉 Netlify 在部署函数时应该在哪里查找。由于我们决定将我们的函数放在一个`functions`文件夹中，这个文件应该如下所示:

```
//netlify.toml file
[build]
  functions = "functions"

```

### 网络生活中的写作功能

现在让我们假设我们有一个名为`hello.js`的函数，这将使我们的函数在`.netlify/functions/hello`可用:

```
// js
exports.handler = aysnc function(event, context) {
  return {
    statusCode : 200,
    body: JSON.stringify ({message: "How far, Howdy?"})
  };
}

```

但是，假设我们有一个在`[http://localhost:8080](http://localhost:8080)`运行的[R](https://blog.logrocket.com/3-ways-to-deploy-react-apps-to-netlify/)eact 应用程序。我们可以在`[http://localhost:8080/.netlify/functions/hello](http://localhost:8080/.netlify/functions/hello)`或`[http://localhost:8080/functions/hello](http://localhost:8080/functions/hello)`访问上述功能。

### 无服务器功能请求使用

Netlify 每月允许每个站点 125，000 个无服务器功能请求；另一方面，Cloudflare Pages 有一种不同的方法。在撰写本文时，在开放测试期间，它每天允许 100K 个调用请求。这听起来很棒，但请记住，如果您在 Cloudflare 页面上部署了 10 个网站，这 10 个网站将共享 100K 个请求。

由于该功能仍处于 Cloudflare 页面的测试阶段，因此在评估哪个平台占上风之前，我们需要看看这些限制在正式发布时会如何变化。

## Netlify 与 Cloudflare 页面 CLIs

Cloudflare Pages 和 Netlify 都有出色的内置 CLI 工具，可以帮助我们在本地使用这两个平台，在推向生产之前，开发和测试开发阶段的所有内容。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

全栈 Cloudflare Pages 带来了 [W](https://developers.cloudflare.com/workers/cli-wrangler) [rangler](https://developers.cloudflare.com/workers/cli-wrangler) ，易于安装，具体通过 npm 和 cURL。而 Netlify 则有 [Netlify CLI](https://docs.netlify.com/cli/get-started/) ，也可以和 npm 一起安装。

### CLI 中的身份验证过程

Cloudflare Pages 和 Netlify 的 CLI 都采用相同的身份验证方法来存储机器的访问令牌以供将来使用。

但是，在 Cloudflare 的系统中，一个用户可以拥有多个帐户和区域。因此，您的用户通过单个 Cloudflare 令牌在您的计算机上进行全局配置。

您的帐户和区域将根据项目进行配置，但将使用您的 Cloudflare 令牌对所有 API 调用进行身份验证。存储帐户和区域信息的配置文件创建在计算机主目录的`.wrangler`目录中。

Netlify 的 CLI 使用访问令牌向 Netlify 进行身份验证。您可以使用命令行或在 Netlify UI 中获取此令牌。

![Authenticating and Accessing Netlify Token In The Netlify CLI](img/bf1d60da7cc918eebc89a9a42e567272.png)

要使用命令行进行身份验证并获取访问令牌，请从任意目录输入以下命令:

```
netlify login

```

这将打开一个浏览器窗口，要求您使用 Netlify 登录并授予对 Netlify CLI 的访问权限。

授权后，Netlify CLI 会将您的访问令牌存储在一个`[config.json](https://docs.netlify.com/cli/get-started/#config-json-location)`全局配置文件中。然后，Netlify CLI 会将该文件中的令牌自动用于以后的所有命令。

## 带宽、设置和支持的语言

带宽是指可以在站点、用户和服务器之间传输的数据容量。虽然 Netlify 在其免费层选项上设置了 100GB 的每月带宽限制，但 Cloudflare Pages 没有；其实是无限的。

然而，每月 100GB 是相当大的，100GB 的数据可以在部署在 Netflify 服务器上的网站的用户之间传输。

至于设置，两个平台都很简单，很容易设置项目。它们都免费支持安全套接字层(SSL)证书，为通过互联网发送的敏感数据提供加密。

对于语言支持，全栈 Cloudflare Pages 支持 JavaScript 和 Typescript，而 Netlify 支持 Golang、JavaScript/Node.js 和 TypeScript。

## 构建时间

构建时间是在部署项目后完全构建项目所需的时间。这可能是在您将更改推送到同步到 Cloudflare Pages 或 Netlify 的 GitHub 或 Gitlab 存储库之后。

对于两个平台的免费层选项，Cloudflare 更好。Netlify 以分钟(300 分钟/月)来衡量构建，而 Cloudflare Pages 以一个月内的构建次数来衡量，无论构建需要多长时间，都是 500 次/月。

## 结论

虽然 Netlify 自 2014 年以来一直是无服务器平台的标准，但全栈 Cloudflare Pages 似乎在 Jamstack 网站部署中很有前途[。](https://blog.logrocket.com/jamstack-in-2021-past-present-and-future/)

由于两个平台都提供了在它们的服务器上运行后端代码的方法，这是一个梦想成真，特别是对于那些不太大的项目。

然而，开发人员可能希望选择 Netlify 而不是 Cloudflare 页面，因为他们可以可靠地预计免费层上每个站点每月有 125K 无服务器功能请求(125K)。我们需要看看 Cloudflare Pages 在测试版发布后会实现什么样的限制。

另一方面，当您考虑带宽时，Cloudflare Pages 提供无限制的 1GB，而 Netlify 每月提供 100GB。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)