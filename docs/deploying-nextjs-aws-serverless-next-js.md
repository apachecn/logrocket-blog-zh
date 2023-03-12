# 使用无服务器 Next.js 将 Next.js 部署到 AWS

> 原文：<https://blog.logrocket.com/deploying-nextjs-aws-serverless-next-js/>

# 使用无服务器 Next.js 将 Next.js 部署到 AWS

## 

2022 年 2 月 24 日 2 分钟阅读 783

无服务器架构是近几年来的一个发展趋势，它允许你将你的代码传递给拥有自己服务器的云提供商，你可以从这些服务器上运行你的代码。因此，您不必担心服务器如何工作、如何维护或如何扩展。相反，您可以专注于编写您的业务逻辑，并且只为代码运行的次数付费。

最著名的无服务器架构工具是 AWS Lambda，它是来自 AWS T1 的无服务器功能服务。然而，其他大型云提供商，如 [GCP](https://cloud.google.com/serverless/whitepaper) 和 [Azure](https://azure.microsoft.com/en-us/overview/serverless-computing/) 也有自己的产品。

在本文中，我们将学习如何在无服务器模式下将 Next.js 应用程序部署到 AWS Lambda，帮助我们最大化速度和可伸缩性。我们开始吧！

### 目录

## Next.js 无服务器模式

从 v8 开始，Next.js 就包含了[无服务器模式](https://nextjs.org/blog/next-8#serverless-nextjs)。通过 Next.js 中的无服务器模式，开发人员可以继续使用 Next.js 的开发人员友好的集成环境来构建应用程序，同时还可以通过在部署时将应用程序分解为较小的部分来利用无服务器架构。

无服务器模式包括几个内置配置，但是执行起来可能有些复杂。然而，部署到 Vercel 和 Netlify 非常简单。

许多初创公司和小公司在一个更大的云平台上享受他们的所有基础设施，实施粒度权限来控制对某些功能的访问，如预算。这种基础设施非常复杂，它的广泛使用解释了为什么云架构师现在如此受欢迎。在这种情况下，我们可以通过无服务器框架让我们的生活变得更加轻松。

## 无服务器框架和无服务器 Next.js 组件

[无服务器框架](https://www.serverless.com/)是一个工具，通过允许您将项目基础设施定义为`yml`，使部署到云平台变得更加容易，自动化供应。

[无服务器组件](https://github.com/serverless-components/)是无服务器框架的插件，具有构建典型项目结构的预定义步骤。[无服务器 Next.js](https://github.com/serverless-nextjs/serverless-next.js) 是一个无服务器组件，用于轻松地将 Next.js 应用部署到 AWS。在撰写本文时，Serverless Next.js 仅支持 AWS 的自托管，但最终它也将支持其他云提供商。

## 将 Next.js 应用程序部署到 AWS

### 先决条件

要完成本教程的剩余部分，您需要以下内容:

*   已安装 Node.js ≥v12.x
*   安装在您机器上的无服务器框架
*   能够访问 Lambda、SQS 和 Cloudfront 的 AWS `User`和`Role`

在 Next.js 项目的根目录下，在包含`package.json`文件的文件夹中，使用以下代码创建一个`serverless.yml`文件:

```
# serverless.yml

myNextApplication:
  component: "@sls-next/[email protected]" # use latest version, currently 3.6.0

```

然后，我们将运行下面的无服务器命令，它将读取无服务器`yml`，下载组件，并在您的项目中运行它。确保在命令中包含您的 AWS 凭证作为`.env`变量:

```
AWS_ACCESS_KEY_ID=accesskey AWS_SECRET_ACCESS_KEY=sshhh serverless

```

上面的命令将以无服务器模式将您的 Next.js 应用程序部署到 AWS。每个 API 端点和页面都将被配置为一个`AWS [[email protected]](/cdn-cgi/l/email-protection)`函数，为您提供无服务器架构的速度、成本和可伸缩性优势，而无需复杂的配置、API 网关和 AWS 控制台上的 Lambda 函数。

AWS Lambda 功能是通过 AWS Cloudfront CDN 部署的无服务器功能。分布在 CDN 上的无服务器功能比部署到特定 AWS 区域的常规 AWS Lambda 功能更高效。

您的 Next.js 应用程序中的所有静态路线都将在 CloudFront 上预先呈现和分发，以最大化速度。结果是一个动态应用程序可以以接近静态站点的速度运行，因为动态部分也是由 CDN 分发的，非常酷！

## 尚不支持的功能

在撰写本文时，无服务器 Next.js 组件尚不支持以下功能:

*   中间件
*   Bot 感知的 ISR 回退
*   avi 图像支持

请注意，虽然 ISR 是受支持的，但它要求您的 AWS 部署`User`和`Roles`能够访问 Lambda 和 SQS。

## 结论

在本文中，我们探索了使用无服务器框架将 Next.js 应用程序部署到 AWS Lambda。Next.js 提供了一个很好的开发环境，允许我们利用静态、服务器端和客户端代码。使用无服务器 Next.js，我们可以轻松地将应用程序部署到无服务器架构中，以最大化速度和可伸缩性。

我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。