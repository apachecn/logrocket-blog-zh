# 使用 Claudia.js - LogRocket 博客将无服务器 API 部署到 AWS

> 原文：<https://blog.logrocket.com/deploying-a-serverless-api-to-aws-with-claudia-js/>

AWS Lambda 是一种无服务器技术，允许您在不提供或管理服务器的情况下运行代码。它支持大多数流行的编程语言，包括 Node.js。

为什么选择无服务器？部署一个无服务器 API 的好处包括自动伸缩、零服务器管理、只为你使用的东西付费的能力、提高的速度等等。在 AWS Lambda 上运行 Express.js API 是利用所有这些优势的一个好方法。

然而，Lambda 并不直接支持用 Node.js 等编程语言构建的 Express.js 等框架，这就是 Claudia.js 等适配器的用武之地。

Claudia.js 位于 AWS Lambda 和您的 Express.js 应用程序之间，将来自各种 AWS 源(如 API 网关或应用程序负载平衡器)的 Lambda 调用事件转换为您的 Express 应用程序可以侦听的 HTTP 事件。

在本教程中，我们将演示如何使用 Claudia.js 将无服务器 API 部署到 AWS。我们将向您展示如何构建 Express.js 应用程序并将其部署到 AWS Lambda。

以下是我们将要介绍的内容:

本教程假设您熟悉 Node.js、Express.js 和 Lambda。Lambda 希望你的代码以某种方式构建。所以如果你想用它来托管类似 Express 应用的东西，你需要使用一个像 Claudia.js 这样的适配器。

## Claudia.js 是什么？

Claudia.js 是一个工具，旨在简化 Node.js 项目到 AWS Lambda 和 API Gateway 的部署。它自动执行部署任务，因此您无需担心引入错误，并按照您期望的开箱即用方式配置一切。

根据[官方网站](https://claudiajs.com/)的消息，克劳迪娅的主要目标是帮助 JavaScript 开发者轻松开始使用 Lambda 微服务，这样他们就可以“专注于解决重要的业务问题，而不是处理 AWS 部署工作流。”

## Claudia.js 入门

在本节中，我们将指导您设置一个示例 Node.js 和 Express.js 应用程序，以展示 Claudia.js 的实际应用。

### 安装 Claudia.js

[克劳迪娅](https://www.npmjs.com/package/claudia)T2。js 在 npm 上有。使用 Claudia.js 的最简单方法是将其作为全局实用程序安装:

```
npm install -g claudia

```

这将使 Claudia.js 立即可用于您的所有项目。通过运行以下命令，验证 Claudia.js 是否安装正确:

```
claudia --version

```

如果安装按预期运行，您将看到类似于下面的版本号:

```
5.13.0

```

### 设置 AWS 帐户

如果您还没有 AWS 帐户，您需要创建一个。亚马逊有一个免费层计划，将涵盖在 Lambda 中创建和测试 Express.js 应用程序。按照[日](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)日[日](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)日[指南](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)来设置您的 AWS 账户

### 配置 AWS 访问凭据

现在您已经有了一个 AWS 帐户，您需要创建一个新用户，并授予该用户对您的 AWS 帐户的编程访问权限。遵循下面概述的步骤:

1.  登录 [AWS 管理控制台](https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Fconsole.aws.amazon.com%2Fiam%2F%3Fstate%3DhashArgs%2523%26isauthcode%3Dtrue&client_id=arn%3Aaws%3Aiam%3A%3A015428540659%3Auser%2Fiam&forceMobileApp=0&code_challenge=txsI3FyTuRhTZ0gU3GQpN14wCLDUQQ-uffXnzpIiaNU&code_challenge_method=SHA-256)并打开 IAM 控制台
2.  在导航窗格中，选择**用户**
3.  创建一个新用户，选择**编程访问**选项，并遵循提示的其余部分
4.  在最后一个提示中，您将看到新的用户凭证。要查看新的访问密钥对，选择**显示**。此对话框关闭后，您将无法再访问密钥。您的凭据将如下所示:
    *   访问密钥 ID: `AKIAIOSFODNN7EXAMPLE`
    *   秘密访问密钥:`wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY`
5.  下载 CSV 格式的凭据，并将文件存储在安全的位置
6.  点击**用户**选项卡，选择新添加的用户
7.  在**权限**选项卡上，添加以下权限:
    *   `[AWSLambdaFullAccess](https://console.aws.amazon.com/iam/home?region=us-east-1#policies/arn:aws:iam::aws:policy/AWSLambdaFullAccess)`是所有 Claudia.js 部署所必需的
    *   如果您希望 Claudia.js 自动为您的 Lambda 函数创建执行角色，则 [`IAMFullAccess`](https://console.aws.amazon.com/iam/home?region=us-east-1#policies/arn:aws:iam::aws:policy/IAMFullAccess) 是必需的(建议初学者使用)

现在，安装 [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) 并使用它来配置本地概要文件。Claudia 将使用此配置文件将你的应用部署到 Lambda:

```
$ aws configure
AWS Access Key ID [None]: Your Access Key ID
AWS Secret Access Key [None]: Your screet key
Default region name [None]: us-east-1
Default output format [None]: json

```

## 生成 Express.js 应用程序

使用 npm 安装 Express.js 应用程序生成器:

```
npm install express-generator -g

```

快速生成器是一个 CLI，允许您在终端中运行`express`命令:

```
express claudia-demo

```

这将创建一个名为`claudia-demo`的新 Express 项目，然后将它放在`claudia-demo`目录中:

```
cd claudia-demo

```

现在安装所有项目依赖项:

```
npm install

```

您的应用程序不需要侦听 TCP 端口，因为 API Gateway 会处理 web 请求。您可以删除 Express generator 创建的`.bin`文件夹。

## 为部署配置 Express.js 应用程序

Express.js 应用程序将由 Lambda 函数托管，因此它需要一个处理程序来在应用程序和 API 网关之间发送数据。这就是 Claudia.js 的用武之地。Claudia 使用`aws-serverless-express`生成一个 helper 函数来连接 API 网关请求和 Express.js 请求和响应。

在您的项目目录中执行下面的命令。如果您重命名了 Express.js 应用程序模块，请将`app`替换为主 Express.js 应用程序模块的名称:

```
$ claudia generate-serverless-express-proxy --express-module app

```

这将把`aws-serverless-express`添加到您的项目依赖项中，并创建包含 Lambda 函数的文件。默认情况下，该文件将被命名为`lambda.js`。

## 部署到 AWS Lambda

您的应用程序现已准备好上线:

```
claudia create --handler lambda.handler --deploy-proxy-api --region us-east-1

```

恭喜你！您的 Express.js 应用程序运行在 Lambda 上。

## 结论

我们已经看到，我们可以在 AWS Lambda 上运行 Express.js 应用程序，但每个 Express 应用程序都应该在 Lambda 上运行吗？

Express.js 假设您的应用程序运行在传统的服务器上，而不是无服务器的。在传统的 Express.js 服务器应用程序中可以轻松工作的某些操作可能无法在无服务器的情况下工作。

例如，文件和图像上传在 Lambda 上的 Express.js 应用程序中无法工作，因为该应用程序无法访问持久文件系统。此外，WebSocket 通信在 Lambda 上无法工作，因为当没有请求时，您的服务器不存在。

像任何工具一样，在 AWS Lambda 上运行 Express.js 应用程序的可行性取决于您的用例以及项目的独特需求。我希望这篇教程能给你一些启发，帮助你决定什么时候使用这种方法。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)