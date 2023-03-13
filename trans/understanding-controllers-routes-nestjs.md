# 理解 NestJS 中的控制器和路由

> 原文：<https://blog.logrocket.com/understanding-controllers-routes-nestjs/>

NestJS 是用 TypeScript [构建的 Node.js web 框架，用于构建可伸缩的服务器端应用](https://blog.logrocket.com/build-server-side-application-ts-ed-mysql/)。应用程序的服务器端没有 UI 元素，它执行重要的逻辑，比如处理请求、发送响应、在数据库中存储数据等等。

控制器和路由在服务器端应用中扮演着重要的角色。在本文中，我们将看看 NestJS 中的路由和控制器。我们将涵盖:

接下来，你需要安装 [Node.js](https://nodejs.org/) ，一个 [JavaScript 包管理器](https://blog.logrocket.com/javascript-package-managers-compared/)——我用的是[Yarn](https://yarnpkg.com/)——和一个你选择的 IDE 或文本编辑器，比如 Sublime Text 或 [Visual Studio 代码](https://code.visualstudio.com/)。确保您使用的是 Node v12 或更新版本。

## 服务器端(HTTP)请求的简要回顾

服务器端或 HTTP 请求是指用户请求应用程序执行与资源相关的操作。这些用户请求是通过客户端(如浏览器或应用程序)发出的。

当我们要求我们的应用程序执行某些动作时，这些 HTTP 请求是按动词分类的。以下是一些常用的动词以及它们所涉及的请求:

*   GET:用于检索资源或资源列表的请求
    *   列出应用程序用户的请求可能类似于:`GET /users`
    *   提供用户 A 的详细资料的请求可能看起来像:`GET /users/A`
*   POST:用于创建资源条目的请求
    *   创建新用户的请求可能看起来像:`*POST /users*`
*   PUT:用于更新现有资源的请求
    *   更新用户 A 的电子邮件和电话号码的请求可能类似于:`PUT /users/A`
*   删除:用于删除现有资源的请求
    *   删除用户 A 的请求可能类似于:`DELETE /users/A`

您可能已经注意到，在上面的例子中，我们用复数`users`来指代我们的资源。命名资源没有对错之分，但这是一个表示所有资源的流行约定。对于一个特定的资源，您可以在复数资源名称后面加上一个唯一的标识符——例如，`users/A`或`users/1`。

这些是最常用的 HTTP 动词，但还有一些其他动词，如 PATCH、OPTIONS 等等。您可以在 MDN 文档中阅读所有的 [HTTP 动词和方法。](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)

## 定义 NestJS 路由和控制器

现在我们已经回顾了 HTTP 请求，让我们来谈谈在 Nest 中路由和控制器是如何使用的。

路由是 HTTP 方法、路径和应用程序中定义的处理它的函数的组合。它们帮助确定哪些控制器接收某些请求。

控制器是用处理一个或多个请求的方法定义的类。控制器为路由提供处理程序功能。

注意，在 NestJS 中，路由是在控制器中使用 decorators 与处理程序方法一起定义的。

了解更多关于使用嵌套中的[控制器和路由处理请求和响应的信息。](https://docs.nestjs.com/controllers)

## 创建我们的 NestJS 项目

我们将使用 NestJS CLI 创建我们的项目。让我们现在安装它:

```
$ npm i -g @nestjs/cli

```

安装完成后，我们可以运行 Nest 命令来创建一个新项目:

```
$ nest new fruit-tree

```

在这种情况下，`fruit-tree`是我们的项目名称。这是一个简单的项目，它返回`Tree`元数据，并说明了 NestJS 中的路由是如何工作的。

运行 Nest 命令后，按照给出的提示进行操作。Nest 会问你想用什么作为你的包管理器。我选择了纱线。就这样，我们的项目准备好了！

Nest 在我们项目的`src/`目录中创建一些核心文件。让我们看一下这些文件:

*   `app.controller.ts`是具有单一路线定义的基本控制器类
*   `app.controller.spec.ts`是控制器类`app.controller.ts`的单元测试文件
*   这是我们应用程序的根模块。我们创建的所有其他模块都将在这里注册
*   是我们的控制器用来执行一些逻辑的基本服务
*   `main.ts`是我们应用程序的入口点

`main.ts`文件是创建 Nest 应用程序实例并注册到一个端口以便从我们的客户端应用程序访问的地方。

## 创建我们自己的 NestJS 路线和控制器

让我们开始在我们的 Nest 项目中创建我们自己的资源、路线和控制器。

### 设置数据库和 ORM

让我们建立一个 MySQL 数据库和 ORM 来检索和更新数据。首先，我们将通过运行以下命令来安装`@nestjs/typeform`、`typeform`和`mysql2`:

```
$ yarn add @nestjs/typeorm typeorm mysql2

```

现在我们需要的库已经安装好了，让我们在核心模块`AppModule`中导入并注册`TypeOrmModule`:

```
// app.module.ts

import { TypeOrmModule } from '@nestjs/typeorm';
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'mysql',
      host: 'localhost',
      port: 3306,
      username: 'root',
      password: 'xxxx',
      database: 'fruit-tree',
      entities: [],
      synchronize: true,
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}

```

一旦`TypeOrmModule`在`AppModule`中注册，我们就不需要在我们创建的任何其他模块中注册它。注意，你可能想设置一个像 [Dotenv](https://github.com/motdotla/dotenv) 这样的配置，这样你就不会提交你的 MySQL 凭证。

在我们的核心模块中注册了`TypeOrmModule`之后，我们可以在项目的其他模块中注入来自 TypeOrm 的`DataSource`和`EntityManager`对象。

### 创建我们的`Tree`实体和`TreeService`

TypeORM 使用[存储库设计模式](https://blog.logrocket.com/implementing-repository-pattern-flutter/)，这是一种设计范式，允许您抽象与数据层的通信，将业务逻辑与数据对象和用于管理它们的数据库类型分开。

首先，我们将为我们的树模块创建一个`tree`目录。该目录将存放树实体，并最终存放树请求的路由、控制器和服务:

```
$ cd src/
$ mkdir tree
$ cd tree/
$ touch tree.entity.ts

```

在我们的`tree.entity.ts`中，我们将指定一些简单的列和属性:

```
// tree.entity.ts

import { Entity, Column, PrimaryGeneratedColumn } from 'typeorm';

@Entity()
export class Tree {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;

  @Column()
  age: number;

  @Column()
  isEndangered: boolean;
}

```

让我们为在我们的`Tree`实体上运行操作创建我们的`TreeService`:

```
$ touch tree.service.ts

```

我们将在我们的`TreeService`中注入我们的`treeRepository`,以对实体执行操作:

```
// tree.service.ts

import { Injectable } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { Tree } from './tree.entity';

@Injectable()
export class TreeService {
  constructor(
    @InjectRepository(Tree)
    private treeRepository: Repository<Tree>,
  ) {}

  findAll(): Promise<Tree[]> {
    return this.treeRepository.find();
  }

  findOne(id: number): Promise<Tree> {
    return this.treeRepository.findOneBy({ id });
  }

  async deleteById(id: number): Promise<void> {
    await this.treeRepository.delete(id);
  }
}

```

现在我们将创建我们的`TreeModule`并注册我们的`Tree`实体和`TreeService`:

```
$ touch tree.module.ts

// tree.module.ts

import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';

import { Tree } from './tree.entity';
import { TreeService } from './tree.service';

@Module({
  imports: [TypeOrmModule.forFeature([Tree])],
  providers: [TreeService],
})
export class TreeModule {}

```

我们还将在`app.module.ts`中的核心模块中注册`TreeModule`:

```
// app.module.ts

...
import { TreeModule } from './tree/tree.module';

@Module({
  imports: [
    ...
    TreeModule,
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}

```

### 定义我们的路线和控制器

到目前为止，在我们的项目中，我们已经建立了一个 MySQL 数据库、处理 DB 操作的 TypeORM、一个定义了数据实体属性的`TreeEntity`类，以及一个我们可以读取或写入数据实体的服务。

现在我们将创建一些嵌套路由，以及处理这些路由的控制器方法！请记住:路由是一种媒介，用户可以通过它请求应用程序执行某些操作，而控制器是一个类，我们通过它来处理这些路由请求。

让我们定义一个获取数据库中所有树的路径。首先，我们将创建控制器文件:

```
$ cd src/trees/
$ touch tree.controller.ts

```

在我们的`tree.controller.ts`文件中，我们将从`@nestjs/common`导入所需的装饰器。然后，我们将使用预期的 HTTP 动词 decorator 以及处理该路由的控制器方法来定义我们的路由:

```
// tree.controller.ts

import { Controller, Get } from '@nestjs/common';

import { Tree } from './tree.entity';
import { TreeService } from './tree.service';

@Controller('trees')
export class TreeController {
  constructor(private readonly treeService: TreeService) {}

  @Get()
  getTrees(): Promise<Tree[]> {
    return this.treeService.findAll();
  }
}

```

注意在 `[@Controller](https://blog.logrocket.com/node-back-end-next-level-nestjs/)` [装饰器](https://blog.logrocket.com/node-back-end-next-level-nestjs/)中的[，我们为将在该文件中定义的所有路径设置了路径前缀。例如，我们在上面的文件中定义的路线是`GET /trees`。](https://blog.logrocket.com/node-back-end-next-level-nestjs/)

我们的控制器方法调用`treeService`的`findAll()`方法来返回我们的树实体列表。

不要忘记在模块中注册控制器:

```
// tree.module.ts

import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';

import { TreeController } from './tree.controller';
import { Tree } from './tree.entity';
import { TreeService } from './tree.service';

@Module({
  imports: [TypeOrmModule.forFeature([Tree])],
  providers: [TreeService],
  controllers: [TreeController],
})
export class TreeModule {}

```

### 在路线中传递参数

我们在上面定义了一个非常简单的路径来获取和返回资源列表。如果我们想要在一个特定的资源项目上定义一个路由—例如，使用它的惟一标识符来检索一个项目，该怎么办？我们来看看吧！

在我们的控制器中——在本例中是`tree.controller.ts`——我们将从`@nestjs/common`导入一个名为`Param`的附加装饰器，并在我们的控制器中使用它，如下所示:

```
// tree.controller.ts

import { Controller, Get, Param, Req, Res } from '@nestjs/common';
import { Tree } from './tree.entity';
import { TreeService } from './tree.service';

@Controller('trees')
export class TreeController {
  constructor(private readonly treeService: TreeService) {}

  @Get()
  getTrees(): Promise<Tree[]> {
    return this.treeService.findAll();
  }

  @Get('/:id')
  getSingleTree(@Param() params: { id: number }): Promise<Tree> {
    return this.treeService.findOne(params.id);
  }
}

```

在上面的代码片段中，我们用装饰器`@Get('/:id')`定义了一条路由，以表示参数`id​`将被传入。为了访问我们的处理程序控制器方法中的参数`id​`，我们将`@Param()​` decorator 作为我们方法的参数传递。

为了实际使用`@Param()`的内容，我们给它分配了一个变量名`params`。另外，因为它是 TypeScript，我们将`params​`的内容定义为一个接口`{ id: number }`。

现在在方法中，我们可以使用`params.id`引用 ID。我们定义的路线将与`GET /trees/:id`匹配。

注意，在上面的例子中，我们传入并引用了整个`param`对象。另一种选择是只将我们需要的`id`参数传递给控制器方法。我们可以通过使用`@Param('id') id: string`并在我们的控制器方法中只引用`id`来做到这一点。

### 传递带有有效负载正文的请求

我们定义了没有参数的简单路线；我们已经用参数定义了路线。但是，我们如何处理接受有效负载的路由呢——例如，用该有效负载创建一个实体？让我们来看看。

对于这个例子，我们将定义一条`POST`路线。第一步是将`Post`添加到我们从`@nestjs/common`的进口清单中。我们还将从`@nestjs/common`中导入`Body`，通过它我们将把有效载荷主体传递给路由的控制器方法。

首先，我们将做一个小的迂回来保持事物的整洁，并定义一个 DTO 类。这个类将定义我们期望在有效负载体中包含的属性:

```
$ cd src/trees
$ mkdir dto
$ cd dto/
$ touch create-tree.dto.ts

```

在我们的 DTO 文件中，我们需要添加以下内容:

```
// create-tree.dto.ts

export class CreateTreeDTO {
  _id?: number;

  name: string;

  age: number;

  isEndangered?: boolean;
}

```

在定义 DTO 时，我决定将`isEndangered`设为可选字段，因此我们将更新实体，以便在设置`none`时为该属性设置默认值:

```
// tree.entity.ts

 ...
 @Column({ default: false })
  isEndangered: boolean;
}

```

现在，我们可以在路由定义中使用这个 DTO 类:

```
// tree.controller.ts

import { Controller, Get, Param, Post, Body } from '@nestjs/common';

import { Tree } from './tree.entity';
import { TreeService } from './tree.service';
import { CreateTreeDTO } from './dto/create-tree.dto';

@Controller('trees')
export class TreeController {
  constructor(private readonly treeService: TreeService) {}

  ...

  @Post()
  createTree(@Body() body: CreateTreeDTO): Promise<Tree> {
    return this.treeService.create(body);
  }
}

```

### 在路由中传递查询参数

接下来，让我们看看如何将查询发送到路由中。我们将扩展所有端点的列表以接受查询，我们可以使用这些查询来过滤列表结果。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

首先，我们将从`@nestjs/common`导入`Query`。然后我们将它传递给我们的控制器方法，就像这样:

```
// tree.controller.ts

import { Controller, Get, Param, Post, Body, Query } from '@nestjs/common';

import { Tree } from './tree.entity';
import { TreeService } from './tree.service';
import { CreateTreeDTO } from './dto/create-tree.dto';

@Controller('trees')
export class TreeController {
  constructor(private readonly treeService: TreeService) {}

  @Get()
  getTrees(@Query() query: { isEndangered?: boolean }): Promise<Tree[]> {
    return this.treeService.findAll({ isEndangered: query.isEndangered });
  }
  ...

```

既然我们已经传入了查询选项`isEndangered`——正如我们所定义的，它被传递给了我们的控制器方法和服务方法——我们将扩展`TreeService`和`TreeEntity`来通过查询选项接受和过滤结果:

```
// tree.service.ts

import { Injectable } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';

import { CreateTreeDTO } from './dto/create-tree.dto';
import { TreeFilterOptions } from './interfaces/filters';
import { Tree } from './tree.entity';

@Injectable()
export class TreeService {
  constructor(
    @InjectRepository(Tree)
    private treeRepository: Repository<Tree>,
  ) {}

  findAll(filters: TreeFilterOptions): Promise<Tree[]> {
    return this.treeRepository.find({
      where: {
        isEndangered: filters.isEndangered,
      },
    });
  }

```

您会注意到，我们为过滤器选项`TreeFilterOptions`定义了一个接口，该接口匹配我们的路由查询对象，并且是将从控制器发送的内容。下面是界面:

```
// interfaces/filters.ts

export interface TreeFilterOptions {
  isEndangered?: boolean;
}

```

### 验证路线参数

在 NestJS 中定义路由的一个重要部分是确保通过参数、查询或有效载荷主体发送的数据与我们的应用程序所期望的相匹配。

首先，我们将通过运行以下命令来导入包`class-validator`和`class-transformer`:

```
$ yarn add class-validator class-transformer

```

现在让我们为我们的`POST /trees`请求扩展我们的`CreateTreeDTO`类，以使用`class-validator`中可用的验证器装饰器:

```
// dto/tree.dto.ts

import { IsNotEmpty, IsNumber } from 'class-validator';

export class CreateTreeDTO {
  _id?: number;

  @IsNotEmpty()
  name: string;

  @IsNumber()
  age: number;

  isEndangered?: boolean;
}

```

在上面的例子中，`IsNotEmpty` decorator 确保请求不接受空字符串作为有效输入，即使它在技术上是长度为`0`的字符串。如果请求使用空字符串值，则响应将匹配:

```
{
  "statusCode": 400,
  "error": "Bad Request",
  "message": ["name must not be empty"]
}

```

注意，NestJS 还允许我们使用管道来[验证路由参数。](https://docs.nestjs.com/pipes)

## 结论

在本文中，我们研究了如何在 NestJS 中定义路由和控制器。我们回顾了 HTTP 请求的例子，并探索了如何使用示例 API 应用程序在路由请求中传递有效负载体、查询和参数等选项。

你可以在我的 Github repo 中找到本文使用的[完整代码。](https://github.com/ibywaks/fruit-tree)

我希望这篇文章对你有用。请在评论区分享你的想法或问题！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)