# 使用 TypeScript 和 Node - LogRocket Blog 探索存储库模式

> 原文：<https://blog.logrocket.com/exploring-repository-pattern-typescript-node/>

存储库模式可以定义为数据存储的抽象，允许将数据访问逻辑从业务逻辑中分离出来。存储库模式有很多好处:

*   它实施了[依赖性反转原则](https://en.wikipedia.org/wiki/Dependency_inversion_principle)
*   因为业务逻辑和数据访问逻辑是松散耦合的，所以它们可以分开测试
*   它有助于保持代码的结构化和组织化
*   它减少了代码的重复，增强了代码的可维护性

像大多数设计模式一样，存储库模式是语言不可知的。考虑到这一点，我将展示如何用 TypeScript 和 Node.js 实现存储库。

## 入门指南

### 创建新的 Nest 应用程序

就像我前面说的，我们将使用嵌套框架。所以，让我们从创建一个新的嵌套应用程序开始。

首先，如果您还没有安装 Nest CLI，请安装它:

```
npm install -g @nestjs/cli

```

安装完成后，我们可以使用 CLI 创建一个新的嵌套应用程序:

```
nest new nest-repository-pattern

```

为了演示存储库模式，我们将使用`post`的概念。让我们为它创建模块和控制器:

```
nest generate module post

nest generate controller post --no-spec

```

这些命令会在`src`目录下的`post`目录下分别生成一个`post.module.ts`文件和一个`post.controller.ts`文件。

### 数据库设置

接下来，让我们为新创建的 Nest 应用程序设置数据库。我将使用 PostgreSQL，但是您可以使用 Knex 支持的任何数据库。为了与我们的数据库进行交互，我们将使用 [Objection.js](http://vincit.github.io/objection.js/) ，这是构建在 [Knex](https://blog.logrocket.com/querying-databases-with-knex-js/) 之上的 Node.js 的 ORM。在本教程中，我们将使用[嵌套异议](https://github.com/willsoto/nestjs-objection)，一个用于异议的嵌套模块。

因此，让我们安装所有必要的依赖项:

```
npm install knex objection @willsoto/nestjs-objection pg

```

安装完成后，我们可以在`src/app.module.ts`的`imports`数组中注册 Nest Objection 模块，并传递我们的数据库详细信息:

```
// src/app.module.ts

import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { ObjectionModule } from '@willsoto/nestjs-objection';

@Module({
  imports: [
    ObjectionModule.register({
      config: {
        client: 'pg',
        useNullAsDefault: true,
        connection: {
          host: '127.0.0.1',
          port: 5432,
          user: 'postgres',
          password: '',
          database: 'nest-repository-pattern',
        },
      },
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}

```

### 模型和迁移

为了演示存储库模式，我们将使用 post 的概念。所以让我们创建一个`Post`模型和相应的迁移来创建一个`posts`表。

让我们从模型开始。在`post`目录下，创建一个新的`post.model.ts`文件，并在其中粘贴以下代码:

```
// src/post/post.model.ts

import { Model } from 'objection';

export default class Post extends Model {
  static tableName = 'posts';

  id: number;
  title: string;
  content: string;
}

```

`Post`模型从异议扩展了基础模型。然后我们定义这个模型将使用的表的名称。最后，我们定义表将拥有的列及其类型。

接下来，让我们通过更新模块，用`PostModule`注册模型，如下所示:

```
// src/post/post.module.ts

import { Module } from '@nestjs/common';
import { ObjectionModule } from '@willsoto/nestjs-objection';
import { PostController } from './post.controller';
import Post from './post.model';

@Module({
  imports: [ObjectionModule.forFeature([Post])],
  controllers: [PostController],
})
export class PostModule {}

```

让我们为`posts`表创建迁移。在我们创建迁移之前，我们的项目需要有一个`knexfile`，我们可以通过运行下面的命令来创建文件:

```
npx knex init -x ts

```

默认情况下，`init`命令会创建一个`knexfile.js`，但是因为我们使用的是 TypeScript，传递`-x ts`会指示`init`命令创建一个`knexfile.ts`。该文件将在项目的根目录下创建。然后，我们用以下内容替换它的内容:

```
// knexfile.ts

import type { Knex } from 'knex';

const config: { [key: string]: Knex.Config } = {
  development: {
    client: 'pg',
    connection: {
      host: '127.0.0.1',
      port: 5432,
      user: 'postgres',
      password: '',
      database: 'nest-repository-pattern',
    },
    migrations: {
      directory: './src/database/migrations',
    },
  },
};

module.exports = config;

```

理想情况下，您可能希望针对不同的环境(开发、试运行、生产等)拥有不同的配置。)，但是出于本教程的目的，我只添加了开发环境的配置。除了数据库配置细节，我们还指定了迁移将驻留的目录。

现在，我们可以为`posts`表创建迁移:

```
npx knex migrate:make create_posts_table

```

如在`knexfile.js`中所指定的，迁移将在`src/database/migrations`中创建。打开它并更新它，如下所示:

```
// src/database/migrations/TIMESTAMP_create_posts_table.ts

import { Knex } from 'knex';

export async function up(knex: Knex): Promise<void> {
  return knex.schema.createTable('posts', function (table) {
    table.increments('id');
    table.string('title').notNullable();
    table.text('content').notNullable();
  });
}

export async function down(knex: Knex): Promise<void> {
  return knex.schema.dropTable('posts');
}

```

在`up`函数中，我们在数据库中创建一个`posts`表，它有三列:`id`、`title`和`content`。当我们运行迁移时，将执行`up`功能。然后在`down`函数中，我们简单地删除可能已经创建的`posts`表。当我们回滚迁移时，将执行`down`功能。

最后，让我们运行迁移:

```
npx knex migrate:up

```

## 实现存储库模式

现在，让我们进入本教程的核心部分。存储库模式利用了契约(接口)和具体实现的概念。基本上，我们定义了我们希望具体实现(类)遵守的契约/接口。

### 创建存储库合同

说到这里，我们来创建后契约/接口。在`src`中，创建一个新的`repositories`目录。这是我们存放所有仓库的地方。在新创建的目录中，创建一个包含以下内容的`PostRepositoryInterface.ts`文件:

```
// src/repositories/PostRepositoryInterface.ts

import Post from '../post/post.model';

export default interface PostRepositoryInterface {
  all(): Promise<Post[]>;
  find(id: number): Promise<Post>;
  create(data: object): Promise<Post>;
}

```

这是我们希望我们所有岗位具体实施都要遵守的契约。为了简单明了，我只添加了三个方法。

### 创建具体的实现

接下来，让我们创建具体的实现。因为我们的应用程序当前使用 Knex 与数据库交互，所以这将是 Knex 实现。仍然在`repositories`目录中，用以下内容创建一个新的`KnexPostRepository.ts`文件:

```
// src/repositories/KnexPostRepository.ts

import { Inject } from '@nestjs/common';
import Post from 'src/post/post.model';
import PostRepositoryInterface from './PostRepositoryInterface';

export default class KnexPostRepository implements PostRepositoryInterface {
  constructor(@Inject(Post) private readonly postModel: typeof Post) {}

  async all(): Promise<Post[]> {
    return this.postModel.query();
  }

  async find(id: number): Promise<Post> {
    return this.postModel.query().where('id', id).first();
  }

  async create(data: object): Promise<Post> {
    return this.postModel.query().insert(data);
  }
}

```

`KnexPostRepository`类实现了我们之前创建的`PostRepositoryInterface`，因此它必须遵守契约的条款；也就是说，实现接口中定义的那些方法。在类构造函数中，我们将`Post`模型注入到类中。既然我们现在可以访问`Post`模型，我们可以使用它在各自的方法中执行必要的操作。

### 使用存储库

现在，要使用我们刚刚创建的`KnexPostRepository`,我们需要首先向嵌套 IoC 容器注册。我们可以通过将它添加到`PostModule`的`providers`数组中来实现:

```
// src/post/post.module.ts

...
import KnexPostRepository from 'src/repositories/KnexPostRepository';

@Module({
  ...
  providers: [
    { provide: 'PostRepository', useClass: KnexPostRepository },
  ],
  ...
})

export class PostModule {}

```

在`providers`数组中，我们说，“嘿，Nest，我们想要将`PostRepository`令牌解析为`KnexPostRepository`类。”这样，每当我们注入`PostRepository`(稍后会有更多介绍)，我们都会得到一个`KnexPostRepository`的实例。

现在，让我们实际利用存储库。更新`PostController`:

```
// src/post/post.controller.ts

import { Controller, Get, Inject } from '@nestjs/common';
import PostRepositoryInterface from 'src/repositories/PostRepositoryInterface';
import Post from './post.model';

@Controller('post')
export class PostController {
  constructor(
    @Inject('PostRepository')
    private readonly postRepository: PostRepositoryInterface,
  ) {}

  @Get()
  async findAll() {
    return this.postRepository.all();
  }
}

```

神奇的事情发生在构造函数内部。还记得上面的`PostRepository`令牌吗？我们通过它的构造函数将它注入到控制器中，结果,`postRepository`属性将是`KnexPostRepository`的一个实例，如上所述。然后我们可以方便地使用存储库中定义的任何方法。

这就是如何使用存储库。如果在项目过程中我们决定将数据访问层切换到类似于 [Prisma](https://www.prisma.io/) 的东西，我们只需要创建一个实现`PostRepositoryInterface`的`PrismaPostRepository`类:

```
// src/repositories/PrismaPostRepository.ts

import PostRepositoryInterface from './PostRepositoryInterface';

export default class PrismaPostRepository implements PostRepositoryInterface {  
  async all(): Promise<Post[]> {
    // Prisma logic
  }

  async find(id: number): Promise<Post> {
    // Prisma logic
  }

  async create(data: object): Promise<Post> {
    // Prisma logic
  }
}

```

然后简单地在嵌套 IoC 容器中注册:

```
// src/post/post.module.ts

...
import KnexPostRepository from 'src/repositories/KnexPostRepository';

@Module({
  ...
  providers: [
    // { provide: 'PostRepository', useClass: KnexPostRepository },
    { provide: 'PostRepository', useClass: PrismaPostRepository },
  ],
  ...
})

export class PostModule {}

```

控制器代码将基本保持不变。

## 重构存储库契约以使用泛型

目前，我们已经成功地实现了存储库模式，我们可以简单地到此为止了。不过，您会注意到:`PostRepositoryInterface`与`Post`模型紧密耦合，这本身并不是问题，但是假设我们想在应用程序中添加评论功能。我们可能倾向于创建一个与`PostRepositoryInterface`具有相同方法和结构的`CommentRepositoryInterface`。然后，我们将创建一个实现`CommentRepositoryInterface`的`KnexCommentRepository`。

您可以立即看到代码重复的模式，因为`PostRepositoryInterface`和`CommentRepositoryInterface`基本上是相同的，只是型号不同。因此，我们将重构接口，以便它可以在任何模型中重用。

我们需要一种方法将模型传递给接口。幸运的是，我们可以使用 TypeScript [泛型](https://www.typescriptlang.org/docs/handbook/2/generics.html)轻松实现这一点。

我们将把`PostRepositoryInterface`重命名为`RepositoryInterface`，并更新代码:

```
// src/repositories/RepositoryInterface.ts

export default interface RepositoryInterface<T> {
  all(): Promise<T[]>;
  find(id: number): Promise<T>;
  create(data: object): Promise<T>;
}

```

这里，`T`将是具体实现的模型。任何想要使用这个接口的类都必须将模型传递给它，以完全遵守契约。

现在，我们可以通过将`Post`模型传递给`RepositoryInterface`来对`KnexPostRepository`做一点小小的调整:

```
// src/repositories/KnexPostRepository.ts

import RepositoryInterface from './RepositoryInterface';

export default class KnexPostRepository implements RepositoryInterface<Post> {
  // rest of the code remain the same
}

```

然后，`KnexCommentRepository`可以这样看:

```
// src/repositories/KnexCommentRepository.ts

import RepositoryInterface from './RepositoryInterface';

export default class KnexCommentRepository implements RepositoryInterface<Comment> {
  // methods implementation for commenting functionality
}

```

## 结论

在本教程中，我们学习了存储库模式、它的一些优点，以及如何用 TypeScript 和 Node.js 实现存储库模式。此外，我们还了解了如何使用 TypeScript 泛型减少代码重复。

您可以从这个 [GitHub 库](https://github.com/ammezie/nest-repository-pattern)获得我们演示的完整源代码。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.