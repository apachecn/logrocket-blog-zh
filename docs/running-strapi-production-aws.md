# 使用 AWS 在生产环境中运行 Strapi

> 原文：<https://blog.logrocket.com/running-strapi-production-aws/>

## 介绍

Strapi 是最受欢迎的开源无头 CMSes 之一，拥有超过 39k Github stars。目前，您必须在您的基础设施上托管 Strapi，因为云版本还不可用。好消息是有很多选择。在本文中，我将讨论在生产环境中的 AWS 上运行 Strapi。

Strapi 文档已经有了关于如何在 AWS 上托管它的深入教程。但是，这不是生产级的，所以我将在这里介绍缺失的部分。

首先，让我们看看在生产中运行 Strapi 需要什么:

1.  计算能力——你需要运行我们应用的东西，一个*服务器*；
2.  数据库— Strapi 将内容保存到 SQL 或 NoSQL 数据库中；

3.  存储——你必须把媒体文件存储在某个地方；

4.  监控—收集和查看应用程序日志在生产环境中至关重要；

5.  秘密管理—您需要处理访问密钥、密码等。；

6.  可伸缩性——系统应该易于伸缩

## 1.计算容量

### EC2

显而易见的解决方案是在一个 **AWS EC2** 实例上运行应用程序。 **t2.small** 是可以运行 Strapi 的最小实例类型。

当您启动 EC2 实例时，您需要选择一个 AMI。AMI 是包含启动实例所需的操作系统和软件的模板。我建议使用亚马逊 Linux 2，因为它很容易与其他 AWS 服务集成。

另一个需要设置的重要事项是安全组，它充当防火墙，允许您控制入站和出站流量。

*注意，不允许任何地方的宋承宪！应该只允许从您的内部安全网络进行 SSH 访问。*

一旦实例运行，您应该能够通过 ssh 访问它。Strapi 是一个 Node.js 应用程序，您需要确保安装了 Node。不幸的是，Amazon Linux 2 附带了一个不被 Strapi 支持的旧版本的 Node。我推荐使用 [NVM](https://github.com/nvm-sh/nvm) 来管理 Node 的不同版本。

使用下面的代码片段，您可以安装 Node 的最新版本:

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
. ~/.nvm/nvm.sh
nvm install node
```

现在你已经有了 NPM，你还应该安装 [PM2](https://pm2.keymetrics.io/) ，一个进程管理器，它将帮助你管理你的应用程序。

如果您决定使用 EC2 实例，您还应该查看一下[保留实例](https://aws.amazon.com/ec2/pricing/reserved-instances/)，因为它们可以显著降低成本。

### 容器

另一个流行的解决方案是使用容器运行 Strapi。AWS 有一堆管理和运行容器的服务。它们可以分为三类:

*   注册表:AWS ECR(弹性容器注册表)——这有助于您管理和存储容器图像；
*   编排:AWS ECS(弹性容器服务)，AWS EKS(弹性 Kubernetes 服务)——这些服务允许您配置容器应该如何运行；
*   计算:AWS Fargate、AWS EC2、AWS App Runner——底层计算层；

这是 Strapi 的官方 Docker 图片。

说到流程编排，选择 AWS ECS 还是 AWS EKS 完全取决于您。如果你已经在使用 Kubernetes，或者你喜欢他们提供的灵活性，EKS 是适合你的工具。另一方面，ECS 是运行容器的 AWS 自以为是的解决方案，因此更容易开始，并且可以更好地与其他 AWS 服务集成。

接下来，您需要决定在哪里运行容器。同样，这也是一个灵活性的问题。Fargate 是一个无服务器容器计算引擎。它很有用，因为在使用 EC2 时，您不必按照要求去关心底层环境。

我会使用 ECS 和 Fargate，因为它们提供了简单性。

## 2.数据库ˌ资料库

默认情况下，Strapi 连接到 SQLite 数据库。由于这不适合生产，您需要在 PostgreSQL、MySQL、MariaDB 和 MongoDB 之间进行选择。

*注意， [Strapi V4](https://strapi.io/blog/mongo-db-support-in-strapi-past-present-and-future) 不再支持 MongoDB。*

如果您愿意，可以在 EC2 机器上部署数据库服务器，但是我强烈推荐使用 AWS RDS，因为它消除了配置、伸缩和备份数据库的复杂性。

在 RDS 上创建数据库很简单，但是请记住，您应该只允许来自 Strapi 应用程序的流量连接到数据库。所以**公共访问**选项应该设置为**否**。您需要配置一个安全组规则，以允许来自 EC2 安全组的入站流量。

Strapi 将数据库配置文件保存在`config/database.js`下。如果您需要在开发和生产中使用不同的配置，可以在以下环境中为每个环境创建数据库配置:

```
config/env/{env_name}/database.js
```

## 3.储存；储备

将资产存储到关系数据库中虽然可能，但不是一个好主意。为了管理媒体文件，Strapi 使用上传提供者。默认的提供者是将文件保存到本地目录下的 **`./public/uploads`** 文件夹中。有很多[供应商可以提供](https://www.npmjs.com/search?q=strapi-provider-upload-&ranking=popularity)。

AWS S3 是一个很好的存储文件的服务，有一个[提供者](https://www.npmjs.com/package/strapi-provider-upload-aws-s3)为此而生。您可以从 npm 安装它:

```
npm install strapi-provider-upload-aws-s3
```

我不喜欢这个插件的一点是，它增加了一个 ACL 规则，使得 S3 桶可以公开访问。对于许多用户来说，这是不可接受的，所以我做了一个分叉，删除了那个 ACL 规则。你可以在这里找到我的供应商版本。

现在，您需要允许您的应用程序写入 S3 存储桶。为此，我建议对 EC2 使用 [IAM 角色。](https://aws.amazon.com/premiumsupport/knowledge-center/ec2-instance-access-s3-bucket/)

## 4.监视

在生产环境中，监控应用程序和 AWS 资源是至关重要的。这可以通过使用 [AWS CloudWatch](https://aws.amazon.com/cloudwatch/) 来实现。

如果您在 EC2 机器或 Docker 容器上运行 Strapi，您将需要[安装 CloudWatch 代理](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/download-cloudwatch-agent-commandline.html)。代理将指标和日志发送到 CloudWatch。在 Amazon Linux 2 上，它是作为一个包提供的:

```
sudo yum install amazon-cloudwatch-agent
```

默认情况下，代理会向 CloudWatch 发送一系列指标。我不会在这里介绍如何配置代理，但以下链接应该会有所帮助:

## 5.秘密管理

将密码和配置保存在一个集中的地方，并从您的应用程序中安全地引用它们可能会很棘手。正如您可能期望的那样，AWS 为此提供了一个服务:[系统管理器参数存储库](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)。参数存储是一个键值存储，您可以在其中保存密码、数据库字符串、访问键等。无论是纯文本还是加密文本。它集成了 EC2 和 ECS。

让我们考虑一下数据库配置文件:

```
  // ./config/database.js
    module.exports = ({ env }) => ({
      defaultConnection: "default",
      connections: {
        default: {
          connector: "bookshelf",
          settings: {
            client: "postgres",
            host: env("DATABASE_HOST", "localhost"),
            port: env.int("DATABASE_PORT", 5432),
            database: env("DATABASE_NAME", "strapi"),
            username: env("DATABASE_USERNAME", "postgres"),
            password: env("DATABASE_PASSWORD", "secret"),
            schema: env("DATABASE_SCHEMA", "public"),
          },
          options: {},
        },
      },
    });
```

您可以在参数存储中保留每个参数，如 **DATABASE_HOST、DATABASE_PORT、DATABASE_NAME、DATABASE_USERNAME、DATABASE_PASSWORD、DATABASE_SCHEMA** 。使用 AWS CLI，您可以从参数存储中提取参数，如下所示:

```
aws ssm get-parameters \
    --names "DATABASE_HOST" "DATABASE_PORT" "DATABASE_NAME" "DATABASE_USERNAME" "DATABASE_PASSWORD" "DATABASE_SCHEMA"
```

回应应该是这样的:

```
{
    "Parameters": [
        {
            "Name": "DATABASE_HOST",
            "Type": "String",
            "Value": "localhost",
            "Version": 1,
            "LastModifiedDate": 1582154764.222,
            "ARN": "arn:aws:ssm:us-east-1:111222333444:parameter/DATABASE_HOST"
            "DataType": "text"
        },
        {
            "Name": "DATABASE_PORT",
            "Type": "String",
            "Value": "5432",
            "Version": 3,
            "LastModifiedDate": 1582156117.545,
            "ARN": "arn:aws:ssm:us-east-2:111222333444:parameter/DATABASE_PORT"
            "DataType": "text"
        },
.....
    ]
}
```

当我启动实例时，我通常会运行一个小脚本来查询参数存储，解析 JSON 响应，并将参数作为环境变量导出到我的`.env.production`文件中。

## 6.可量测性

通常，每个 AWS 服务都能够在需要时扩展。这个基础设施唯一缺少的是应用程序前面的 ALB(应用程序负载平衡器)。ALB 有益的原因有很多:

*   将应用程序放在网络边缘并不是好的做法；
*   需要时，您可以随时添加多个实例/容器；
*   斯特拉皮不支持 HTTPS；
*   [蓝/绿部署所必需的](https://docs.aws.amazon.com/whitepapers/latest/overview-deployment-options/bluegreen-deployments.html)

此外，在 EC2 上运行应用程序时，应该考虑使用 EC2 自动缩放组。自动缩放组由一个或多个 EC2 实例组成。您可以指定最小、最大和所需的实例数。

假设您将最小值设置为 1，将期望值设置为 1，将最大值设置为 3。首先，自动缩放组将启动 1 个实例，因为这是所需的数量。然后，在出现峰值的情况下，它将启动更多实例，但不会超过 3 个。当需求下降时，它会终止不必要的实例，但会保留至少一个实例。

## 结论

在 AWS 上部署 [Strapi](https://blog.logrocket.com/how-to-create-an-api-with-strapi/) 时，需要做出很多决定。这可能有点让人不知所措，尤其是如果你是 AWS 的新手。我希望这篇文章可以作为一个起点，并为您提供一个在 AWS 上运行 Strapi 的高层次概述。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)