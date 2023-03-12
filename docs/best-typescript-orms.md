# 最佳打字稿 ORMs 

> 原文：<https://blog.logrocket.com/best-typescript-orms/>

## 介绍

在构建或设计软件系统时，大多数设计决策都伴随着权衡。是否使用对象关系映射器(ORM)在许多开发人员中一直是一个有争议的话题，因为他们是否真的值得一直存在争论。

在某些情况下，虽然与原始查询相比，编写糟糕的 ORM 可能会导致性能瓶颈，但在其他一些情况下，当您需要快速方便地访问数据库方法时，ORM 可以派上用场。

ORM 还有助于缩短开发时间，这当然会提高开发人员的生产力。您的代码库的可维护性也可能会得到全面的改善，因为在数据库 API 方面有一个更容易与之交互的接口。

ORM 还是从头开始开发应用程序时的一个重要工具:开发人员可以在更短的时间内快速启动和迭代后端应用程序的初始工作功能。

在这篇文章中，我们将探讨在 TypeScript 环境中使用的几种流行的 ORM，并讨论以下内容:

## 如何实际定义 ORM

ORM 帮助我们处理应用程序中与数据层或模型相关的复杂性。使用 orm，开发人员可以通过直接在模型类的实例上调用特定的方法来轻松地执行数据库读写操作。在某种程度上，这是一种更方便的方法，更接近于开发人员在考虑数据操作时所习惯的思维模式。

使用 ORM，开发人员可以通过将数据库表中的行映射到类(或这些类的实例)来管理模型或数据库模式。处理与数据库交互所需的所有方法的数据库逻辑被抽象成对象或类，这种方式在大多数情况下是高度可重用的。因此，我们可以实施一种关注点分离，这非常有用。

但是在我们继续之前，让我们问自己这些非常相关的问题:在 TypeScript 中使用 ORM 可以吗？在访问我们的数据模型或查询我们的数据库时，什么需要 ORM 方法？

我们将回答这些问题，并涵盖使用 ORM 的所有优点和缺点。

## 我们应该在我们的类型脚本代码库中使用 ORM 吗？

ORM 不是银弹。它们并非对所有用例都是完美的。当需求出现时，为工作选择正确的折衷方案仍然是我们作为开发人员的职责。

根据我自己的经验，ORMs 不应该是每个数据库访问层问题的直接解决方案。一般来说，这取决于开发人员试图解决的具体问题、他们能够做出的权衡以及他们的数据层的范围。

虽然 ORM 可能因为涉及更高层次的抽象而使构建查询看起来更容易，但是它们通常更难移植，因此从长远来看是不可行的。此外，不能在所有用例中都依赖它们，因为不是所有的问题都可以通过受支持或可用的 ORM 查询或 API 来解决。

此外，对于设计不良的 ORM，通常会生成昂贵的 SQL 查询。这可能会大大降低应用程序的速度，并对整体性能造成巨大影响。

### 使用 ORM 的好处

另一方面，尽管有人反对使用 ORM，但它还是有自己的位置。使用 ORM 的最大理由之一是，它们提供的数据库抽象层通过帮助创建访问应用程序数据层的一致模式，使得切换数据库更加容易。这意味着我们可以以可预测的方式轻松操作应用程序的数据层。使用查询构建器(通过提供额外的内置方法来改进原始查询)，开发人员可以更容易、更快速地提高编写查询的速度，因此与编写原始数据库查询相比，可以大大提高工作效率。

随着应用程序变得越来越大，编写大型原始查询变得越来越复杂，查询本身也变得难以理解——更不用说基于开发人员存在多种编写相同原始查询的方法，这甚至会使扩展变得更加困难，尤其是对新团队成员而言。这就是奥姆来拯救我们的地方！

Prisma 文档强调了一个关于 ORM、查询构建器和原始查询的使用的有趣论点，即生产力水平和它允许开发人员的控制水平。使用原始查询，开发人员可以完全控制他们编写的查询的质量和复杂性。

然而，一个问题出现了。针对数据库模式运行这些查询有多安全？例如，这些查询是否足够安全，足以防止流行的 SQL 注入攻击？构建大型复杂查询(如多重连接)的维护负担有多大？这取决于实现该解决方案的程序员的经验和专业知识。

因此，使用原始查询，开发人员的工作效率较低，但对如何与数据层交互有更多的控制。他们还可以完全控制自己编写的查询的性能。

然而，使用 ORM，开发人员不需要关心复杂的 SQL 查询，也不需要修改查询结果来满足他们的需求。相反，应该注意细化实现特性所需的数据。

## 挑选最好的打字格式

为您的 TS 项目选择一个 ORM 可能具有挑战性，因为有许多可用的选项。它们的设计和抽象程度各不相同。一些查询构建器和 ORM 还提供了额外的特性，比如净化和类型安全，或者它们可以抽象出很多东西，让开发人员不用太担心。

如今，使用 ORM 在开发人员中更受欢迎，现在也有几个库可供选择。在为我们的应用程序选择一个 ORM 时，我们的目标是查看几个关键因素，并广泛地检查它们。我们将介绍它们的特性、它们的文档范围、它们的性能、它们的社区支持水平以及维护标准。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 最佳打字格式:Prisma

Prisma 是为 TypeScript 和 Node.js 应用程序自动生成的类型安全查询生成器。它是一个[开源](https://github.com/prisma/prisma)，下一代 ORM，允许开发人员轻松管理他们的数据库并与之交互。它有一个由许多开源贡献者和维护者组成的巨大的支持性[社区](https://www.prisma.io/community)。

### 特征

Prisma 是一种 SQL/关系型和面向 NoSQL 的 ORM 类型，目前支持 PostgreSQL、MYSQL、MicrosoftSQL server、SQLite、CockroachDB 和 MongoDB。

Prisma 还支持 JavaScript 生态系统中的大多数技术，包括 REST API 模式、GraphQL、gRPC 以及 JS 和 ts 环境中的大多数后端框架，如 Express。

说到功能，Prisma 给人的印象相当深刻。它提供:

*   Prisma 的客户
*   一种类型安全的 ORM，允许开发人员考虑如何处理数据(而不是如何编写原始查询)
*   这是一个迁移工具，在开发和生产过程中，随着模式的发展，它有助于运行数据库迁移
*   一个 GUI 来处理数据库中的数据，对开发也很有用

Prisma 是电池，因为它允许开发者用数据建模语言定义他们的应用模型。它还包含一个连接到您选择的数据库的简单方法，并定义了一个生成器。Prisma 文档中的 Prisma 模式文件示例如下所示:

```
datasource db { 
provider = "postgresql" 
  url      = env("DATABASE_URL") 
} 

generator client { 
  provider = "prisma-client-js" 
} 

model User { 
  id    Int     @id @default(autoincrement()) 
  email String  @unique 
  name  String? 
  posts Post[] 
} 

```

有了以上内容，我们可以配置三样东西，包括:

1.  指定数据库连接的数据源
2.  一个生成器，它表明我们打算生成一个 Prisma 客户端
3.  定义应用程序模型的数据模型

### Prisma 数据模型

要在 Prisma 中获得数据模型，有两个主要的工作流程。它们包括:

1.  使用 [Prisma Migrate](https://www.prisma.io/docs/concepts/components/prisma-migrate) 命令编写[数据模型](https://www.prisma.io/docs/concepts/components/prisma-schema/data-model)并将其映射到数据库
2.  通过[自省](https://www.prisma.io/docs/concepts/components/introspection)数据库生成数据模型

一旦定义了数据模型，就可以生成 [Prisma 客户端](https://www.prisma.io/docs/concepts/components/prisma-client/working-with-prismaclient/generating-prisma-client),它将公开已定义模型的 CRUD 操作和查询。有了 TypeScript，我们还可以[获得所有查询的全部类型安全好处](https://www.prisma.io/docs/concepts/components/prisma-client/advanced-type-safety/operating-against-partial-structures-of-model-types)。

要使用 npm 安装`@prisma/client`,我们可以运行以下命令:

```
npm install @prisma/client 

```

对数据模型进行更改后，您还需要手动重新生成 Prisma 客户端。我们可以通过运行以下命令来实现:

```
prisma generate

```

我们可以导入如下所示的客户端代码。

```
import { PrismaClient } from '@prisma/client'
const prisma = new PrismaClient() 

```

现在，我们可以通过生成的 Prisma 客户端 API 发送查询。下面是一个查询示例:

```
// Run inside async function 
const allUsers = await prisma.user.findMany() 

```

关于可用操作的更多详细信息可以在 [Prisma 客户端 API 参考](https://www.prisma.io/docs/concepts/components/prisma-client)中找到。Prisma [文档](https://www.prisma.io/docs/getting-started)也很成熟，有关于路线图、限制、API 参考、示例项目、常见问题等大量细节。

### 类型脚本配置

要将 Prisma 与 TypeScript 一起使用，请确保您使用的 TypeScript 版本≥ 3.3。`tsconfig.json`文件应该如下所示:

```
{
  "compilerOptions": {
    "sourceMap": true,
    "outDir": "dist",
    "target": "ES2018",
    "module": "commonjs",
    "strict": true,
    "lib": ["esnext"],
    "esModuleInterop": true
  },
  "exclude": ["dist", "prisma", "tests"]
}

```

最后，继续安装下面的`npm`包作为 Prisma 使用 Typescript 的开发依赖项:

```
npm install ts-node ts-node-dev typescript --save-dev

```

## 最好的打字格式:打字格式

[TypeORM](https://typeorm.io/) 是一个开源的 ORM，运行在 Node.js 和浏览器中。它支持 TypeScript 和所有最新的 JavaScript 应用程序。该项目的目标是为大型企业应用程序提供小型应用程序的附加功能。

根据文档，它支持[活动记录](https://typeorm.io/#/active-record-data-mapper/what-is-the-active-record-pattern)和[数据映射器](https://typeorm.io/#/active-record-data-mapper/what-is-the-data-mapper-pattern)模式，这与其他 JavaScript ORMs 不同。这种支持意味着开发人员可以编写高质量、松散耦合、可伸缩、可维护、生产就绪的应用程序。

### 特征

TypeORM 的文档中有一个[丰富的特性列表](https://typeorm.io/#undefined/features)。一些流行的工具包括 CLI、查询缓存、连接池和对钩子的支持。它还附带了一个带有广泛引用的[装饰 API](https://typeorm.io/decorator-reference)。

除了基本的 MongoDB 支持之外，TypeORM 还支持 MySQL、MariaDB、PostgreSQL、CockroachDB、SQLite、Microsoft SQL Server、SQL.js 和 Oracle。它还支持迁移、关系和索引；使用 TypeORM，我们可以直接从使用 Sequelize 中迁移出[。最后，它有一个巨大的支持性社区，有许多开源贡献者和维护者。](https://typeorm.io/sequelize-migration)

### 类型脚本配置

要在 TS 中使用 TypeORM，请确保您使用的 TypeScript 版本≥ 3.3，并且在`tsconfig.json`文件中启用了以下设置:

```
{
  "compilerOptions": {
    "sourceMap": true,
    "outDir": "dist",
    "target": "ES2018",
    "module": "commonjs",
    "strict": true,
    "lib": ["esnext"],
    "esModuleInterop": true,
    "emitDecoratorMetadata": true, 
    "experimentalDecorators": true, 
  },

```

然后，继续安装`npm`包:

```
npm install typeorm --save 

```

安装`reflect-metadata`垫片:

```
npm install reflect-metadata --save 

```

确保将其导入到应用程序的全局名称空间中的某个位置，例如在`app.ts`或数据库连接文件中。

您可能还需要安装节点类型，这可以通过运行以下命令来完成:

```
npm install @types/node --save-dev 

```

带有数据库连接配置的`ormconfig.json`文件如下所示。

```
{ 
   "type": "mysql", 
   "host": "localhost", 
   "port": 3306, 
   "username": "test", 
   "password": "test", 
   "database": "test", 
   "synchronize": true, 
   "logging": false, 
   "entities": [ 
      "src/entity/**/*.ts" 
   ], 
   "migrations": [ 
      "src/migration/**/*.ts" 
   ], 
   "subscribers": [ 
      "src/subscriber/**/*.ts" 
   ] 
} 

```

接下来，我们可以建立到数据库的连接。

```
import "reflect-metadata"; 
import { createConnection } from "typeorm"; 

createConnection({ 
    type: "mysql", 
    host: "localhost", 
    port: 3306, 
    username: "root", 
    password: "admin", 
    database: "test", 
    entities: [ 
        __dirname + "/entity/*.js" 
    ], 
    synchronize: true, 
    logging: false 
}).then(connection => { 
    // here you can start to work with your entities 
}).catch(error => console.log(error)); 

```

对于 TypeORM，我们需要用`@Entity` decorator 来修饰模型，这意味着将为模型创建一个等价的数据库表。我们可以使用 TypeORM 在任何地方处理实体，这允许我们查询数据库层。

### TypeORM 数据模型

TypeORM 模型看起来像这样:

```
@Entity() 
export class User { 
    @PrimaryGeneratedColumn() 
    id: number;  
    @Column() 
    firstName: string;  
    @Column() 
    lastName: string; 
    @Column() 
    age: number; 
} 

```

下面是一些示例域逻辑，看起来像这样:

```
const repository = connection.getRepository(User); 

const user = new User(); 
user.firstName = "Alex"; 
user.lastName = "Cage"; 
user.age = 26; 
await repository.save(user); 

const allUsers = await repository.find(); 
const firstUser = await repository.findOne(1); 
const name = await repository.findOne({ firstName: "Alex" }); 
await repository.remove(name); 

```

### Bonus: QueryBuilder

TypeORM 自带一个内置的 [QueryBuilder](https://typeorm.io/#/select-query-builder/what-is-querybuilder) ，这是它最强大的特性之一。这允许开发人员使用优雅而方便的语法构建 SQL 查询，执行它们，并接收自动转换的实体。

TypeORM 中 QueryBuilder 的一个简单示例:

```
const firstUser = await connection 
    .getRepository(User) 
    .createQueryBuilder("user") 
    .where("user.id = :id", { id: 1 }) 
    .getOne(); 

```

它构建了如下所示的 SQL 查询，该查询转化为一个很棒的原始 SQL 查询。

```
SELECT 
    user.id as userId, 
    user.firstName as userFirstName, 
    user.lastName as userLastName 
FROM users user 
WHERE user.id = 1 

```

## 最佳打字格式:MikroORM

MikroORM 是基于数据映射器、[工作单元](https://mikro-orm.io/docs/unit-of-work)和身份映射模式的 Node.js 的开源类型脚本 ORM。

### 特征

MikroORM 支持 SQL 和 NoSQL 数据库，包括 MongoDB、MySQL、MariaDB、PostgreSQL 和 SQLite 数据库。通过定制驱动程序，可以支持更多的数据库[。](https://mikro-orm.io/docs/unit-of-work#)

它还支持[一个查询构建器](https://mikro-orm.io/docs/query-builder#),当我们需要执行一个 SQL 查询而不涉及所有 ORM 的东西时。这样，我们既可以自己编写查询，也可以使用 query builder 助手为我们构建查询。

MikroORM 附带了[许多高级特性](https://mikro-orm.io/docs/query-builder#)，包括事件和钩子支持、模式生成器、迁移和传播。

### 安装和配置

要安装，我们只需要运行我们选择的每个提供商的驱动程序包，如下所示。

```
npm i -s @mikro-orm/core @mikro-orm/mongodb     # for mongo 
npm i -s @mikro-orm/core @mikro-orm/mysql       # for mysql/mariadb 
npm i -s @mikro-orm/core @mikro-orm/mariadb     # for mysql/mariadb 
npm i -s @mikro-orm/core @mikro-orm/postgresql  # for postgresql 
npm i -s @mikro-orm/core @mikro-orm/sqlite      # for sqlite 

```

### 类型脚本配置

接下来，我们需要像以前一样，在我们的`tsconfig.json`中启用对[装饰者](https://www.typescriptlang.org/docs/handbook/decorators.html)和`esModuleInterop`的支持。见下文:

```
{
  "compilerOptions": {
    "sourceMap": true,
    "outDir": "dist",
    "target": "ES2018",
    "module": "commonjs",
    "strict": true,
    "lib": ["esnext"],
    "esModuleInterop": true,
    "emitDecoratorMetadata": true, 
    "experimentalDecorators": true, 
}

```

MikroORM 还附带了几个在开发过程中非常有用的命令行工具，比如`SchemaGenerator`和`EntityGenerator`。

为了使用 CLI，我们需要在本地安装`@mikro-orm/cli`包。请注意，版本需要与`@mikro-orm/core`包一致。

```
$ yarn add @mikro-orm/cli 

```

## 最好的打字格式:序列

Sequelize 是一个众所周知的、基于 Promise 的 Node.js ORM，可以与 MySQL、MariaDB、SQLite、Microsoft SQL Server 和 PostgreSQL 一起使用。它有大量的特性，这意味着开发者喜欢它。

### 特征

Sequelize 附带了[可靠的文档](https://sequelize.org/v5/index.html)，支持许多优秀的特性，如数据库播种、迁移、模型验证、原始查询和事务支持。Sequelize 还提供了自己的类型脚本定义。

请注意，目前仅支持 TypeScript 版本≥ 4.1。不幸的是，Sequelize 的 TypeScript 支持没有遵循 SemVer。Sequelize 还严重依赖于手动类型声明的运行时属性赋值来处理模型，这是另一个缺点。

虽然使用 typescript 的设置与我们之前的其他 ORM 配置指南相似，但是要了解更多关于如何使用 Sequelize 设置 TypeScript 项目的信息，[请看一下我们之前关于这个主题的帖子](https://blog.logrocket.com/using-sequelize-with-typescript/)。

为了避免与不同节点版本的冲突，我们的演示中不包括节点的类型。如果你想试用的话，你必须自己手动安装`@types/node`。更多细节可以在这里找到[。](https://sequelize.org/master/manual/typescript.html)

### 安装和配置

Sequelize 可通过 npm 和 Yarn 获得。要安装，我们可以运行:

```
# using npm 
npm i sequelize 
npm i @sequelize/core 

# using yarn 
yarn add sequelize 
yarn add @sequelize/core

```

我们还必须为我们选择的数据库手动安装驱动程序。为了连接到数据库，我们必须创建一个 Sequelize 实例。我们可以将连接参数传递给 Sequelize 构造函数，或者传递一个连接 URI。这两个选项概述如下:

```
const { Sequelize } = require('@sequelize/core'); 

// Option 1: Passing a connection URI 
const sequelize = new Sequelize('sqlite::memory:') // Example for sqlite 
const sequelize = new Sequelize('postgres://user:[email protected]:5432/dbname') // Example for postgres 

// Option 2: Passing parameters separately (sqlite) 
const sequelize = new Sequelize({ 
  dialect: 'sqlite', 
  storage: 'path/to/database.sqlite' 
}); 

// Option 3: Passing parameters separately (other dialects) 
const sequelize = new Sequelize('database', 'username', 'password', { 
  host: 'localhost', 
  dialect: /* one of 'mysql' | 'mariadb' | 'postgres' | 'mssql' */ 
}); 

```

## 最好的打字格式是:Objection.js

Objection.js 是 Node.js 应用程序的 SQL 友好 ORM。它提供了 SQL 查询构建器的所有优点，以及一组功能强大的 API，也可用于处理关系数据库。

的确，Objection 可以说是一个关系查询构建器。异议构建在 SQL 查询构建器之上， [knex](http://knexjs.org/) 。由于这个原因，Knex 支持的所有数据库都同样受到 Objection.js 的支持，它们包括 SQLite3、PostgreSQL 和 MySQL。

异议可以照常通过 npm 或 Yarn 安装。因为它使用 Knex 作为数据库访问层，所以我们也需要安装它。

```
npm install objection knex 
yarn add objection knex 

```

根据我们计划使用的数据库方言，我们还需要安装以下软件之一:

```
npm install pg 
npm install sqlite3 
npm install mysql 
npm install mysql2 

```

Objection 支持基于文档的数据库、事务、钩子、验证，并拥有一个不断增长的插件生态系统。它还支持原始 SQL 查询。更多详情可在[文档指南](https://vincit.github.io/objection.js/guide/)中找到。

### 类型脚本配置

有了 Objection.js，我们可以用 TypeScript 扩展查询构建器，尽管它还不是完全受支持的特性。我们需要向自定义查询生成器添加一些额外的类型。为此，我们需要做的就是定义一个基本模型——只需一次。参见下面的文档。

```
import { Model } from 'objection';

class YourQueryBuilder<M extends Model, R = M[]> extends QueryBuilder<M, R> {
  // Make sure to change the name of the query builder classes. 
  ArrayQueryBuilderType!: MyQueryBuilder<M, M[]>;
  SingleQueryBuilderType!: MyQueryBuilder<M, M>;
  NumberQueryBuilderType!: MyQueryBuilder<M, number>;

  customMethod(something: number): this {
    //use function;
  }
}

class BaseModel extends Model {
  QueryBuilderType!: YourQueryBuilder<this>;
  static QueryBuilder = YourQueryBuilder;
}

```

在这之后，我们可以用定义的查询构建器从基本模型继承。在[文档](https://vincit.github.io/objection.js/recipes/custom-query-builder.html#extending-the-query-builder-in-typescript)中有更多详细信息。

## 结论

在 TypeScript 中，虽然我们有各种各样的 ORM 可供选择，但有些更容易通过更好的 API 接口层进行交互，而另一些则提供更好的性能和更高度优化的直观查询。

对于任何使用 ORM 的人来说，一个令人信服的理由是，总的来说，它们节省了大量的开发时间和执行维护任务的时间，这些任务通常是枯燥和重复的。此外，轻松切换数据库模式并在几分钟内无缝访问它们的能力——无需了解所有数据模型或数据库内部——确实是 ORM 的一大优势。

开发人员应该能够请求他们需要的数据，而不必担心编写查询，一个为他们做出正确决策的抽象可以提供强大的帮助。然而，在某些情况下，这可能意味着抽象强加了某些限制，使我们的查询变得缓慢而繁琐，这也是 ORM 出现许多问题的地方。

总之，ORM 对于具有简单数据访问模式的应用程序更有用，例如，没有复杂查询的应用程序。对于简单的 CRUD 应用程序和需要简单查询的应用程序，ORM 可能是一个方便的工具。另一方面，如果我们希望查询性能优先考虑速度，那么使用 ORM 在这方面没有好处。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.