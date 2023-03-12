# 通过 API routes 在 Next.js 中构建 GraphQL 服务器

> 原文：<https://blog.logrocket.com/building-a-graphql-server-in-next-js/>

***编者按:*** *这篇文章于 2021 年 5 月 14 日更新，以反映更新的信息和新代码，使用 TypeScript 添加类型安全，使用 DataLoader 避免 N+1，对数据库层使用 Prisma 代替 Knex，并采用代码优先的 GraphQL 模式方法。*

很多人只把 [Next.js](https://nextjs.org/) 看作一个前端 React 框架，提供服务器端渲染、内置路由和大量性能特性。所有这些都是真的，但是 Next.js 还通过其 API routes 支持无服务器功能，这是一种为 React 前端代码提供后端的简单方法，所有这些都在同一个应用程序中。

在这篇文章中，我们将学习如何使用 API routes 在 Next.js 中设置 GraphQL API。我们将使用完全类型化的设置: [TypeScript](https://www.typescriptlang.org/) ，使用 [Prisma](https://www.prisma.io/) 从 Postgres 加载数据，并使用带有 Nexus 的[代码优先](https://blog.graphqleditor.com/graphql-schemafirst-codefirst) GraphQL 模式。

我们还将了解如何使用 DataLoader 包和模式来提高性能，避免代价高昂的 N+1 查询。完整的源代码可以在这里找到。

## 构建 GraphQL 服务器

在本文结束时，我们希望能够执行以下 GraphQL 专辑和艺术家查询，从我们的 Postgres 数据库中高效地加载(仅使用两个 SQL 查询):

```
{
  albums(first: 5) {
    id
    name
    year
    artist {
      id
      name
    }
  }
}
```

产生的输出可能类似于:

```
{
  "data": {
    "albums": [
      {
        "id": 3,
        "name": "Rotation & Frequency",
        "year": "2020",
        "artist": {
          "id": 2,
          "name": "Slick Shoes"
        }
      },
      {
        "id": 5,
        "name": "I Am",
        "year": "2018",
        "artist": {
          "id": 3,
          "name": "Sleeping Giant"
        }
      }
      // etc...
    ]
  }
}
```

## 设置 Next.js

设置 Next.js 最简单的方法是运行命令`yarn create next-app`。一旦你将 Next.js 中的一个文件更改为 TypeScript 扩展名(`.ts`或`.tsx`)并运行`yarn dev`，它会要求你安装一些额外的包，并自动创建一个`tsconfig.json`文件。

您可以随意避开这一步，但是我们将稍微修改一下`tsconfig.json`文件，让 TypeScript 执行更严格的规则:

```
// tsconfig.json
{
  "compilerOptions": {
    "target": "esnext",
    "module": "esnext",
    "lib": ["dom", "esnext"],
    "incremental": true,
    "allowJs": false,
    "checkJs": false,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "jsx": "preserve",
    "allowSyntheticDefaultImports": true,

    /* Module Resolution */
    "esModuleInterop": true,
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,

    /* Type Checking */
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["**/*.ts", "**/*.tsx"],
  "exclude": ["node_modules", ".next"]
}
```

关于本文中使用的包的完整列表，请参考 [package.json 文件](https://github.com/leighhalliday/next-prisma-nexus-graphql/blob/main/package.json)。

## 配置 Prisma

Prisma 将是我们用来从 Postgres 数据库加载数据的工具。首先，运行命令`npx prisma init`，这将生成一个包含`schema.prisma`文件的`prisma`文件夹。

注意，它引用了一个`DATABASE_URL`环境变量。确保这可以通过一个`.env`文件获得(但是不要提交它！).我们将修改模式文件以包含两个模型:Album 和 Artist。

```
// prisma/schema.prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model Album {
  id       Int    @id @default(autoincrement())
  name     String
  year     String
  artist   Artist @relation(fields: [artistId], references: [id])
  artistId Int
}

model Artist {
  id     Int     @id @default(autoincrement())
  name   String
  url    String
  albums Album[]
}
```

为了在数据库中创建`Album`和`Artist`表，我们需要告诉 Prisma 创建并运行数据库迁移:`npx prisma migrate dev --name init`。

## 使用 Prism 加载种子数据

在本地开发时，最好有一些一致的种子数据。Prisma 支持数据库播种，但是首先让我们更新我们的`package.json`文件中的`scripts`部分，使其具有一个执行 Prisma seed 命令所需的`ts-node`脚本。

```
// package.json
{
  "scripts": {
    "ts-node": "ts-node --compiler-options '{\"module\":\"CommonJS\"}'"
  }
}
```

接下来，我们将在`prisma`文件夹中创建一个`seed.ts`文件。它的目的是生成开发时要在本地使用的任何数据。请随意把你最喜欢的乐队放在这里！

```
// prisma/seed.ts
import { PrismaClient } from "@prisma/client";
const prisma = new PrismaClient();

async function main() {
  const comeback = await prisma.artist.create({
    data: {
      name: "Comeback Kid",
      url: "https://comeback-kid.com/",
      albums: {
        create: [
          { name: "Turn It Around", year: "2003" },
          { name: "Wake the Dead", year: "2005" },
        ],
      },
    },
  });
  console.log({ comeback });
}

main()
  .catch((e) => {
    console.error(e);
    process.exit(1);
  })
  .finally(async () => {
    await prisma.$disconnect();
  });
```

准备好之后，运行`npx prisma db seed --preview-feature`来执行种子文件并填充我们的本地数据库。

## 在 GraphQL 中添加 API 路由

设置好 Next.js 后，我们将向我们的应用程序添加一个 API(服务器)路由。这就像在名为`graphql.ts`的`pages/api`文件夹中创建一个文件一样简单。现在，它的内容将是:

```
// pages/api/graphql.ts
export default (_req, res) => res.end("GraphQL!");
```

搞定了。GraphQL 不是很容易吗？开个玩笑…上面的代码将简单地用文本“GraphQL！”来响应但是通过这种设置，我们可以用任何我们想要的 JSON 来响应，读取查询参数、头等。来自`req`(请求)对象。

实际 GraphQL 处理的时间。为此，我们将使用`ApolloServer`(来自`apollo-server-micro`包——这个在 Next.js 中非常好用)。你会注意到我们从两个文件中导入了`schema`和`context`。这些现在还不存在，但是我们以后会花大部分时间在这里。

```
// pages/api/graphql.ts
import { ApolloServer } from "apollo-server-micro";
import { schema } from "../../src/schema";
import { context } from "../../src/context";

const server = new ApolloServer({ schema, context });
const handler = server.createHandler({ path: "/api/graphql" });

export default handler;
```

## GraphQL 上下文

GraphQL 中的每个解析器都会接收到一个叫做上下文的东西。这是一个放置全球事物的地方，例如:

*   认证用户
*   数据库连接(Prisma)
*   数据加载器(避免 N+1 次查询)

在这个例子中我们没有经过身份验证的用户，但是我们有另外两个。我们将从导出一个返回 Prisma 客户端实例的`context`函数开始。

```
// src/context.ts
import { PrismaClient } from "@prisma/client";
import { Artist } from ".prisma/client";

const prisma = new PrismaClient({ log: ["query"] });

export interface Context {
  prisma: PrismaClient;
}

export function context(): Context {
  return { prisma };
}
```

## 设置 GraphQL 模式

有了上下文，就该生成 GraphQL 模式了。您经常会发现手写的`schema.graphql`文件，以及相应的解析器来加载从每个字段返回的数据。

Nexus 就不是这样了。Nexus 采用代码优先的方法，其中`schema.graphql`文件是从 GraphQL 类型定义中生成的工件。这意味着您不必让它们保持同步！

我们将创建三种 GraphQL 类型:查询、艺术家和相册。本次`makeSchema`拜访的关键部分是:

*   `outputs`:您希望将`schema.graphql`和 TypeScript 类型输出到哪里？这些是我上面提到的人工制品
*   `sourceTypes`:如果您已经有了与您的 GraphQL 类型一致的 TypeScript 类型，您可以避免重新创建它们！我们将把它指向从我们的 Prisma 客户端创建的类型
*   这允许我们告诉 Nexus 在哪里寻找我们上下文的类型脚本类型
*   默认情况下，我们将把我们的每个 GraphQL 字段定义为非空…这可以被覆盖，但我觉得默认情况下从严格的代码开始会更好

```
// src/schema.ts
import { makeSchema, objectType, queryType } from "nexus";
import { join } from "path";

export const schema = makeSchema({
  types: [Query, Artist, Album],
  shouldGenerateArtifacts: process.env.NODE_ENV === "development",
  outputs: {
    schema: join(process.cwd(), "schema.graphql"),
    typegen: join(process.cwd(), "nexus.ts"),
  },
  sourceTypes: {
    modules: [{ module: ".prisma/client", alias: "prisma" }],
    debug: process.env.NODE_ENV === "development",
  },
  contextType: {
    module: join(process.cwd(), "src", "context.ts"),
    export: "Context",
  },
  nonNullDefaults: {
    input: true,
    output: true,
  },
});
```

## GraphQL API 中的顶级查询类型

`Query`类型(以及`Mutation`和`Subscription`，我们不会在这里讨论)是 GraphQL API 的顶级字段。它们是 API 的入口点。在我们的`Query`类型中，我们将定义一个字段:`albums`，它将使用我们上下文中的 Prisma 客户端来加载相册。

```
// src/schema.ts
const Query = queryType({
  definition(t) {
    t.list.field("albums", {
      type: "Album",
      args: {
        first: "Int",
      },
      resolve(_root, args, ctx) {
        return ctx.prisma.album.findMany({ take: args.first });
      },
    });
  },
});
```

## `Album`和`Artist`类型

使用 Nexus 的`objectType`函数创建`Artist`和`Album`的类型。大多数字段可以不使用显式解析器，因为它们只是访问属性，没有任何操作。

虽然,`Album`类型能够加载一个`Artist`,我们将创建一个自定义解析器来从数据库中加载艺术家。

```
// src/schema.ts
const Artist = objectType({
  name: "Artist",
  definition(t) {
    t.int("id");
    t.string("name");
    t.string("url");
  },
});

const Album = objectType({
  name: "Album",
  definition(t) {
    t.int("id");
    t.string("name");
    t.string("year");
    t.field("artist", {
      type: "Artist",
      async resolve(album, _args, ctx) {
        const artist = await ctx.prisma.artist.findFirst({
          where: { id: album.artistId },
        });
        // The ! tells TypeScript to trust us, it won't be null
        return artist!;
      },
    });
  },
});
```

## 使用数据加载器避免 N+1 查询

上述解决方案存在一个隐藏的问题。具体来说，加载每张专辑的艺术家。针对每个专辑运行一个 SQL 查询，这意味着如果您有 50 个专辑要显示，您将不得不执行 50 个额外的 SQL 查询来加载每个专辑的艺术家。Marc-André Giroux 在这个问题上有一篇[的好文章](https://xuorig.medium.com/the-graphql-dataloader-pattern-visualized-3064a00f319f)，我们现在就来看看如何解决这个问题！

第一步是定义一个加载器函数。加载器的目的是汇集 id(在我们的例子中是艺术家的 id ),并一次在一个批处理中加载所有 id，而不是单独加载每个 id。你在偷懒加载数据。

```
// src/context.ts
const createArtistLoader = () =>
  new DataLoader<number, Artist | null>(async (ids) => {
    // Load all of the artists for the given `ids`
    const artists = await prisma.artist.findMany({
      where: { id: { in: [...ids] } },
    });

    // To make for more efficient lookup, convert to a Map of id => Artist records
    const artistMap = artists.reduce(
      (acc, artist) => acc.set(artist.id, artist),
      new Map<number, Artist>()
    );

    // Map the ids back to the loaded Artist record
    return ids.map((id) => artistMap.get(id) ?? null);
  });
```

我们需要将这个数据加载器实例添加到我们的上下文中，并进行以下更改:

```
// src/context.ts
export interface Context {
  prisma: PrismaClient;
  artistLoader: ReturnType;
}

export function createContext(): Context {
  return { prisma, artistLoader: createArtistLoader() };
}
```

这允许我们更新我们的`Album`类型，以利用`artist`解析器内部的数据加载器:

```
const Album = objectType({
  name: "Album",
  definition(t) {
    t.int("id");
    t.string("name");
    t.string("year");
    t.field("artist", {
      type: "Artist",
      async resolve(album, _args, ctx) {
        const artist = await ctx.artistLoader.load(album.artistId);
        return artist!;
      },
    });
  },
});
```

最终的结果是对数据库的一个简单查询就可以一次加载所有的艺术家… N+1 问题解决了！我们现在应该看到总共两个查询:一个加载专辑，另一个加载所有艺术家。

## 结论

在本文中，我们能够在 Next.js 中创建一个类型化的、代码优先的 GraphQL 服务器，使用 Prisma 从 Postgres 加载数据，并使用 DataLoader 解决 N+1 性能问题。对于一天的工作来说还不错！

下一步可能包括在我们的应用程序中添加突变和认证，使用户能够以正确的权限创建和修改数据。如你所见，Next.js 不再仅仅用于前端。它拥有对无服务器端点的一流支持，是放置 GraphQL API 的最佳位置。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.