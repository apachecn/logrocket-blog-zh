# NestJS 在 AWS 上的无服务器应用

> 原文：<https://blog.logrocket.com/nest-js-serverless-application-aws-dynamodb/>

在设置、提供、维护和扩展底层基础设施时，构建和部署 web 应用程序可能会变得非常困难。然而，我们可以[使用无服务器框架来使事情变得更容易](https://blog.logrocket.com/deploying-nextjs-aws-serverless-next-js/#serverless-framework)。

在本教程中，我们将学习如何使用 DynamoDB 和无服务器框架在 NestJS 中构建一个无服务器博客应用程序。本教程的代码是 GitHub 上的[。我们开始吧！](https://github.com/icode247/nest-serverless)

## 什么是 NestJS？

NestJS 是一个 Node.js 框架，用于构建基于 TypeScript 的服务器端应用程序，这些应用程序是快速的、经过测试的、可伸缩的和松散链接的。NestJS 使用 Express 或 Fastify 等可靠的 HTTP 服务器框架，提供一个覆盖层来抽象它们，并直接向开发人员公开它们的 API。这使得开发人员能够利用底层平台可用的大量第三方模块。

NestJS 支持数据库管理系统，如 PostgreSQL、MySQL，以及本教程中的[yugabytdb](https://www.yugabyte.com/)。NestJS 还包括现成的依赖注入。

## 什么是 DynamoDB？

作为亚马逊 Web 服务组合的一部分，亚马逊提供了 [DynamoDB](https://aws.amazon.com/dynamodb/) ，这是一个完全托管的专有 NoSQL 数据库服务，支持键值和文档数据格式。DynamoDB 提供了一个可与 Dynamo 相媲美的数据架构，虽然 DynamoDB 有一个独特的底层实现，但它的名字却来自 Dynamo。

## 什么是无服务器框架？

无服务器框架是一个开源的 CLI，允许您以最低的复杂性和费用设计、部署、调试和保护无服务器应用程序，同时提供来自 AWS、Azure 和 Google 的基础架构资源。

无服务器框架提供了开箱即用的结构、自动化和最佳实践支持，允许您专注于开发由函数和事件组成的复杂的、事件驱动的无服务器系统。

## 入门指南

要开始构建和部署 AWS 无服务器功能，您需要[设置一个 AWS 帐户](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)。然后，通过运行以下命令来设置您的凭据:

```
aws configure

```

如果您希望将您的功能部署到 AWS，则需要上面的设置。接下来，使用以下命令全局安装无服务器框架:

```
npm install -g serverless

```

然后，安装 NestJS CLI:

```
npm i -g @nestjs/cli

```

使用下面的命令创建一个 NestJS 应用程序:

```
nest new nest-serverless && cd nest-serverless

```

## 安装依赖项

现在，让我们用下面的命令安装所需的依赖项:

```
npm install aws-lambda aws-serverless-express express aws-sdk --save

```

至此，我们已经完成了无服务器应用程序的初始设置。

## 配置无服务器框架

现在，让我们为无服务器应用程序添加一些实际的配置。用下面的代码片段在项目的根目录下创建一个`serverless.yml`文件:

```
  service: nest-serverless
plugins:
  - serverless-plugin-optimize
  - serverless-offline
  - serverless-dynamodb-local

functions:
 app: 
   handler: dist/main.handler
   events:
     - http:
         method: any
         path: /{any+}

provider: 
    name: aws
    runtime: nodejs14.x
    apiGateway:
      shouldStartNameWithService: true

    environment: 
      AWS_NODEJS_CONNECTION_REUSE_ENABLED: 1
      NODE_OPTIONS: --enable-source-maps --stack-trace-limit=1000
    iam: 
      role: 
        statements: 
            - Effect: 'Allow'
              Action: 
                - 'dynamodb:DescribeTable'
                - 'dynamodb:Query'
                - 'dynamodb:Scan'
                - 'dynamodb:GetItem'
                - 'dynamodb:PutItem'
                - 'dynamodb:UpdateItem'
                - 'dynamodb:DeleteItem'

              Resource: arn:aws:dynamodb:us-west-2:*:table/BlogsTable

custom: 
    esbuild: 
      bundle: true
      minify: false
      sourcemap: true
      exclude: aws-sdk
      target: node14
      define: 'require.resolve: undefined'
      platform: node
      concurrency: 10

    dynamodb: 
      start: 
        port: 5000
        inMemory: true
        migrate: true

      stages: dev

resources: 
    Resources: 
      TodosTable: 
        Type: AWS::DynamoDB::Table
        Properties:
          TableName: BlogsTable
          AttributeDefinitions: 
              - AttributeName: id
                AttributeType: S

          KeySchema: 
              - AttributeName: id
                KeyType: HASH

          ProvisionedThroughput: 
            ReadCapacityUnits: 1
            WriteCapacityUnits: 1  

```

在我们的`serveless.yml`文件中，我们指定了我们的项目在`FrameworkVersion`中运行的无服务器框架的版本。在我们的例子中，我们使用版本 3，这是编写本文时的最新版本。

我们在`Plugins`中指定需要哪些插件来替换或增强我们项目的功能。我们有两个插件， [serverless-esbuild](https://www.serverless.com/plugins/serverless-esbuild) 和 [Serverless Offline](https://www.serverless.com/plugins/serverless-offline) ，它们允许我们在本地执行代码。DynamoDB 可以通过[server less-dynamo db-local](https://www.serverless.com/plugins/serverless-dynamodb-local)进行本地操作。

在`Provider`中，我们为我们的项目配置云提供商。为了给我们的 Lambda 函数提供对 DynamoDB 资源表的读写访问，我们设置了各种云提供商特征，比如`name`、`runtime`、`apiGateway`和`iam`语句

在`Resources`中，我们使用`cloudFormation`资源模板填充 DynamoDB 数据库。在这一节中，我们声明了一些特定的特征，如`tableName`、`AttributeDefinitions`，其中我们指定了表的主键 ID，以及`ProvisionedThroughput`，其中我们描述了数据库在一秒钟内可以读写的单元数量。

我们在`Custom`中创建了自己的配置。对于我们的 DynamoDB 数据库，我们设置了`port 5000`。最后，在`Functions`中，我们配置我们的 Lambda 函数、路由和路由处理器。

为了使我们能够在本地计算机上运行我们的应用程序，我们需要安装以下插件:

*   在我们的计算机上本地连接到 DynamoDB
*   `serverless-offline`:本地启动应用程序
*   `serverless-plugin-optimize`:使我们能够在本地运行应用程序

运行下面的命令来安装上面的插件:

```
serverless plugin install -n serverless-plugin-optimize 
serverless plugin install -n serverless-dynamodb-local 
serverless plugin install -n serverless-offline

```

最后，让我们用下面的命令在本地安装 DynamoDB:

```
serverless dynamodb install

```

## 将我们的应用程序转换为 AWS Lambda

我们将把我们的 NestJS 应用程序转换成我们的`src/main.ts`文件中的 AWS Lambda 函数。首先，我们需要用下面的代码片段将我们的应用程序映射到 Expess 应用程序:

```
import { NestFactory } from '@nestjs/core';
import { ExpressAdapter } from '@nestjs/platform-express';
import { INestApplication } from '@nestjs/common';
import { AppModule } from './app.module';
import { Express } from 'express';
import { Server } from 'http';
import { Context } from 'aws-lambda';
import { createServer, proxy, Response } from 'aws-serverless-express';
import * as express from 'express';
let cachedServer: Server;
async function createExpressApp(
  expressApp: Express,
): Promise<INestApplication> {
  const app = await NestFactory.create(
    AppModule,
    new ExpressAdapter(expressApp),
  );
  return app;
}
...

```

然后，我们将把我们的 Express 应用程序转换成 AWS Lambda 函数，这样我们就可以将我们的应用程序作为 Lambda 函数运行:

```
async function bootstrap(): Promise<Server> {
  const expressApp = express();
  const app = await createExpressApp(expressApp);
  await app.init();
  return createServer(expressApp);
}
export async function handler(event: any, context: Context): Promise<Response> {
  if (!cachedServer) {
    const server = await bootstrap();
    cachedServer = server;
  }
  return proxy(cachedServer, event, context, 'PROMISE').promise;
}

```

此时，我们可以将 NestJS 应用程序作为 AWS Lambda 函数运行。现在，让我们创建应用程序服务，向 DynamoDB 数据库添加一些记录。

## 创建博客服务

让我们为我们的博客 API 创建 CRUD 操作。首先，打开`src/app.service.ts`文件，用下面代码片段中的以下导入替换代码:

```
import { Injectable, InternalServerErrorException } from '@nestjs/common';
import { v4 as uuid } from 'uuid';
import * as AWS from 'aws-sdk';
import Blog from './interface';

```

在上面的代码片段中，我们导入了 AWS SDK 来与 DynamoDB 交互，`uuid`来为我们的博客 ID 和博客接口生成随机字符串，我们将在后面创建博客接口。

为了知道将 DynamoDB 连接到哪个端点，我们将检查应用程序运行的环境:

```
...
const dynamoDB = process.env.IS_OFFLINE
  ? new AWS.DynamoDB.DocumentClient({
      region: "localhost",
      endpoint: process.env.DYNAMODB_ENDPOINT,
    })
  : new AWS.DynamoDB.DocumentClient();
 ...

```

我们将使用下面的代码片段使用`AWS.DynamoDB.DocumentClient method`来创建我们的 CRUD 操作:

```
@Injectable()
export class AppService {
  async getBlogs(): Promise<any> {
    try {
      return dynamoDB
        .scan({
          TableName: "BlogsTable",
        })
        .promise();
    } catch (e) {
      throw new InternalServerErrorException(e);
    }
  }

  async createBlog(blog: Blog): Promise<any> {
    const blogObj = {
      id: uuid(),
      ...blog,
    };
    try {
      return await dynamoDB
        .put({
          TableName: "BlogsTable",
          Item: blogObj,
        })
        .promise();
    } catch (e) {
      throw new InternalServerErrorException(e);
    }
  }

  async getBlog(id: string): Promise<any> {
    try {
      return await dynamoDB
        .get({
          TableName: process.env.USERS_TABLE_NAME,
          Key: { id },
        })
        .promise();
    } catch (e) {
      throw new InternalServerErrorException(e);
    }
  }

  async deleteBlog(id: string): Promise<any> {
    try {
      return await dynamoDB
        .delete({
          TableName: "BlogsTable",
          Key: {
            todosId: id,
          },
        })
        .promise();
    } catch (e) {
      throw new InternalServerErrorException(e);
    }
  }

```

现在，在`src`文件夹中创建一个`interface.ts`文件，并添加下面的代码片段:

```
export default interface Blog {
  title: string;
  coverImage: String;
  body: string;
  createdBy: string;
  dateCreated: string;
}

```

## 创建博客控制器

创建完博客服务后，让我们定义访问这些服务的端点。首先，打开`src/app.controller.ts`文件，用下面的代码片段替换当前代码:

```
import { Controller, Get, Post, Delete, Body, Param } from '@nestjs/common';
import { AppService } from './app.service';
import Blog from './interface';
```

然后用下面的代码片段定义博客路线:

```
@Controller('blogs')
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get()
  async getTodos(): Promise<Blog[]> {
    return await this.appService.getBlogs();
  }

  @Post()
  async createTodo(@Body() blog: Blog): Promise<Blog> {
    return await this.appService.createBlog(blog);
  }

  @Post(':id')
  async getTodo(@Param() id: string): Promise<Blog> {
    return await this.appService.getBlog(id);
  }

  @Delete(':id')
  async deleteTodo(@Param() id: string): Promise<any> {
    return await this.appService.deleteBlog(id);
  }
}

```

设置好博客路径后，让我们构建并运行应用程序。

## 测试应用程序

让我们用下面的命令运行我们的应用程序。因为我们的处理程序指向`dist`文件夹，所以我们需要在运行应用程序之前构建它:

```
npm run build && serverless offline start

```

如果一切顺利，您将在控制台上看到如下输出，如下所示:

![Test Nestjs Serverless Output](img/6aedc1c85e7594fe3b48ef084b316b7a.png)

## 部署应用程序

现在我们已经在本地测试了应用程序，让我们来部署它。您可以使用多种方法部署无服务器应用程序。

### 部署全部

这是部署无服务器应用程序的主要和最常用的方法。当您已经在`serverless.yml`中更新了您的函数、事件或资源配置，并且想要将该更改(或者一次多个更改)部署到 Amazon Web Services 时，使用这个方法。要使用此方法部署应用程序，请运行以下命令:

```
serverless deploy

```

部署完成后，您应该会在终端下方看到输出。

![Serverless Deploy All Output](img/58542c7f45ce76ce66d838b3d195de18.png)

### 直接云形成部署

这是一种部署无服务器功能的更快方法。据无服务器开发者称，这将是无服务器 4 中的默认部署方法。将以下配置添加到您的`serverless.yml`文件中:

```
provider: 
    ...
    deploymentMethod: direct

```

该方法的默认设置是`dev`阶段和`us-east-1`区域。您可以通过向命令传递以下标志来更改默认阶段和区域:

```
serverless deploy --stage beta --region eu-central-1

```

您应该会看到下面的输出。

![CloudFormation Deployment](img/a5d06a2b128c12acc35aac217647cec6.png)

### 部署功能

这是部署无服务器功能的最快方法。它只是在 AWS 上覆盖当前函数的 zip 文件，而不修改 AWS CloudFormation 堆栈:

```
serverless deploy function --function funtionToDeploy

```

## 结论

在本教程中，我们学习了如何使用 DynamoDB 在 AWS 上构建一个 NestJS 无服务器应用程序。首先，我们回顾了无服务器框架、NestJS 和 DynamoDB。然后，我们为演示创建了一个 Blog Lambda 函数。

要了解更多关于无服务器框架的信息，请务必查看官方文档。我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)