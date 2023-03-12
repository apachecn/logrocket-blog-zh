# 让 GraphQL 服务器兼容 Relay 

> 原文：<https://blog.logrocket.com/making-a-graphql-server-compatible-with-relay/>

GraphQL 是一项非常强大的技术，它使我们能够构建更好的 API。GraphQL 是一种查询语言，它帮助我们避免对服务器的不必要的请求，减少过量提取和不足提取请求，并且只获取我们需要的数据。该技术的使用一直在增长，GraphQL 在不同语言中的采用越来越多，对于那些想要构建 GraphQL APIs 的人来说，未来真的很光明。

[Relay](https://relay.dev/en/) 是一个强大的 JavaScript 框架，用于声明性地使用 GraphQL 并构建数据驱动的 React 应用程序。它不是社区中使用最多的 GraphQL 客户端，原因有几个。其中之一是 Relay 比其他框架更加结构化和固执己见，文档不是很直观，社区本身也不是很大。虽然 Apollo 比 Relay 有一些优势，主要是在社区和文档方面，但 Relay 也有一些优势使它非常特别。

[Relay](https://relay.dev/en/) 建议在前端使用，以实现更结构化、模块化、经得起未来考验的应用，可以轻松扩展到数百万用户。为了使用 Relay，首先需要实现的是制作一个 Relay 兼容的 GraphQL 服务器。这就是我们现在要做的。

## GraphQL 服务器规范

在处理[缓存](https://relay.dev/docs/en/thinking-in-graphql#client-caching)和[数据获取](https://relay.dev/docs/en/thinking-in-graphql#fetching-data)时，Relay 以优雅的方式工作，这是它相对于其他 GraphQL 客户端的最大优势之一。

Relay 在其文档中有一个叫做 [GraphQL 服务器规范](https://relay.dev/docs/guides/graphql-server-specification/)的东西，这个指南展示了 Relay 为了正确工作而制定的关于 GraphQL 服务器的约定。

当创建一个将与 Relay 一起工作的新的 GraphQL 服务器时，我们需要确保它遵循以下原则:

## 一个`Node`物体

节点接口用于重新提取一个对象，正如[文档](https://graphql.org/learn/global-object-identification/#node-interface)所说:

> 服务器必须提供一个名为`Node`的接口。该接口必须包含一个名为`id`的字段，返回一个非空的`ID`。这个`id`应该是这个对象的一个全局唯一标识符，只要给定这个`id`，服务器应该能够重新获取这个对象。

对于 GraphQL 模式来说,`Node`接口非常重要，它提供了一种使用其`ID`请求对象的标准方式。

另一个需要实现的是`[Node](https://graphql.org/learn/global-object-identification/#node-root-field)` [根字段](https://graphql.org/learn/global-object-identification/#node-root-field)。该字段只接受一个非空的全局唯一的`ID`作为参数:

> 如果查询返回实现了`Node`的对象，那么当服务器在`Node`的`id`字段中返回的值作为`id`参数传递给`node`根字段时，这个根字段应该重新提取相同的对象。

## 如何通过连接分页

分页一直是 API 中的一个痛点，没有正确实现它的标准方法。Relay 通过使用 [GraphQL 游标连接规范](https://relay.dev/graphql/connections.htm)很好地处理了这个问题:

```
{
  podcasts {
    name
    episodes(first: 10) {
      totalCount
      edges {
        node {
          name
        }
        cursor
      }
      pageInfo {
        endCursor
        hasNextPage
      }
    }
  }
}
```

该规范提出了一种称为连接的模式，在查询中，连接提供了一种对结果进行切片和分页的标准方式。这些连接还为提供游标的响应提供了一种标准方式，一种告知客户端何时有更多结果的方式。

## 可预测的突变

突变应该使用`input`类型，以使它们可预测并以标准方式构建:

```
input AddPodcastInput {
  id: ID!
  name: String!
}

mutation AddPodcastMutation($input: AddPodcastInput!) {
  addPodcast(input: $input) {
    podcast {
      id
      name
    }
  }
}
```

使 GraphQL 服务器与 Relay 兼容将确保我们有一个结构良好、高性能和可伸缩的 GraphQL API，它可以很容易地扩展到数百万个。

## 入门指南

现在我们知道了 Relay 期望我们的 GraphQL 服务器提供的三个原则，让我们创建一个遵循 [GraphQL 服务器规范](https://relay.dev/docs/en/graphql-server-specification)的示例，并看看它在实践中是如何工作的。

我们要做的第一件事是创建一个新项目并安装一些依赖项:

```
yarn add @koa/cors @koa/router graphql graphql-relay koa koa-bodyparser koa-graphql koa-helmet koa-logger nodemon
```

现在，我们将添加一些开发依赖项:

```
yarn add --dev @types/graphql-relay @types/koa-bodyparser @types/koa-helmet @types/koa-logger @types/koa__cors @types/node ts-node typescript
```

安装完所有这些依赖项后，我们现在就可以创建 GraphQL 服务器了。我们将创建一个名为`src`的文件夹，下面是我们示例应用程序的文件:

```
-- src
  -- graphql.ts
  -- index.ts
  -- NodeInterface.ts
  -- schema.ts
  -- types.ts
  -- utils.ts
-- nodemon.json
-- tsconfig.json
```

让我们创建一个名为`tsconfig.json`的文件，以便在这个项目中使用 TypeScript。我们将把下面的代码放在这个文件中:

```
{
  "compilerOptions": {
    "lib": ["es2016", "esnext.asynciterable"],
    "target": "esnext",
    "module": "commonjs",
    "moduleResolution": "node",
    "sourceMap": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "esModuleInterop": true,
    "resolveJsonModule": true,
    "baseUrl": "."
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.spec.ts"]
}
```

在这个例子中，我们将使用 [nodemon](https://www.npmjs.com/package/nodemon) 来监视我们的`src`目录，并在每次检测到目录中有变化时重启我们的服务器。在`nodemon.json`文件中，放入以下代码:

```
{
  "watch": ["src"],
  "ext": "ts",
  "ignore": ["src/**/*.spec.ts", "src/types/**/*.d.ts"],
  "exec": "ts-node ./src/index.ts"
}
```

在我们的`index.ts`文件中，我们将放入以下代码:

```
import Koa from "koa";
import cors from "@koa/cors";
import Router from "@koa/router";
import bodyParser from "koa-bodyparser";
import logger from "koa-logger";
import helmet from "koa-helmet";
import graphqlHTTP from "koa-graphql";

import schema from "./schema";

const app = new Koa();
const router = new Router();

const graphqlServer = graphqlHTTP({ schema, graphiql: true });
router.all("/graphql", bodyParser(), graphqlServer);

app.listen(5000);
app.use(graphqlServer);
app.use(logger());
app.use(cors());
app.use(helmet());
app.use(router.routes()).use(router.allowedMethods());
```

目前，我们只是使用 Koa 创建了一个简单的 GraphQL API。我们从`schema.ts`文件中导入了 GraphQL 模式，现在我们将创建我们的服务器正常工作所需的类型。

在我们的`types.ts`中，我们将放置下面的代码:

```
export class IUser {
  id: string;
  firstName: string;
  lastName: string;
  constructor(data) {
    this.id = data.id;
    this.firstName = data.firstName;
    this.lastName = data.lastName;
  }
}
```

我们还将创建一个名为`utils.ts`的文件，并创建一个名为`users`的空数组:

```
export const users = [];
```

我们只是为我们的用户创建了类型。现在，在我们的`schema.ts`文件中，我们将创建我们的 GraphQL 模式，让我们将以下代码放入该文件中:

```
import { GraphQLSchema } from "graphql";
import { QueryType, MutationType } from "./graphql";

const schema = new GraphQLSchema({
  query: QueryType,
  mutation: MutationType,
});

export default schema;
```

在我们的`graphql.ts`文件中，我们将创建我们的查询。让我们创建一个名为`QueryType`的变量，并从我们的`NodeInterface.ts`文件中导入`NodeField`:

```
import {
  GraphQLObjectType,
  GraphQLInt,
  GraphQLString,
  GraphQLNonNull,
} from "graphql";

import { NodeField, NodesField } from "./NodeInterface";

export const QueryType = new GraphQLObjectType({
  name: "Query",
  description: "The root of all... queries",
  fields: () => ({
    node: NodeField,
    nodes: NodesField,
  }),
});
```

现在，我们有了 GraphQL 服务器的基础。让我们实现`Node`字段，看看它是如何工作的。现在让我们转到我们的`NodeInterface.ts`文件并键入一些代码。

我们将从`graphql-relay`、`nodeDefinitions`和`fromGlobalId`导入函数:

*   `nodeDefinitions`函数帮助我们将全局定义的 id 映射到实际的数据对象中。该函数的第一个参数接收`fromGlobalId`函数，第二个参数用于使用`fromGlobalId`函数读取对象的类型
*   `fromGlobalId`函数将使用其全局 ID 检索对象

我们的`NodeInterface.ts`文件将看起来像这样:

```
import { nodeDefinitions, fromGlobalId } from "graphql-relay";
import { UserType } from "./graphql";
import { users } from "./utils";
import { IUser } from "./types";

const { nodeField, nodesField, nodeInterface } = nodeDefinitions(
  async (globalId: string) => {
    const { id: userGlobalID, type } = fromGlobalId(globalId);
    if (type === "User")
      return await users.find(
        ({ id }: IUser) => (id as string) === userGlobalID
      );
    return null;
  },
  (obj) => {
    if (obj instanceof IUser) return UserType;
    return null;
  }
);

export const NodeInterface = nodeInterface;
export const NodeField = nodeField;
export const NodesField = nodesField;
```

现在让我们为 GraphQL 服务器创建用户类型。在我们的`graphql.ts`文件中，让我们导入一些东西并创建一个名为`UserType`的变量。我们整个`graphql.ts`现在应该是这个样子:

```
import { GraphQLObjectType, GraphQLString, GraphQLNonNull } from "graphql";

import { NodeField, NodesField } from "./NodeInterface";

export const UserType: GraphQLObjectType = new GraphQLObjectType({
  name: "User",
  description: "User",
  fields: () => ({
    firstName: {
      type: GraphQLNonNull(GraphQLString),
      resolve: ({ firstName }) => firstName,
    },
    lastName: {
      type: GraphQLNonNull(GraphQLString),
      resolve: ({ lastName }) => lastName,
    },
  }),
});

export const QueryType = new GraphQLObjectType({
  name: "Query",
  description: "The root of all... queries",
  fields: () => ({
    node: NodeField,
    nodes: NodesField
  }),
});
```

目前，我们没有使用 Relay 希望我们使用的全局 ID 原则。我们将从`graphql-relay`导入`globalIdField`，并在我们的`firstName`字段之前传递它，如下所示:

```
import { GraphQLObjectType, GraphQLString, GraphQLNonNull } from "graphql";
import { globalIdField } from "graphql-relay";

import { NodeField, NodesField } from "./NodeInterface";

export const UserType: GraphQLObjectType = new GraphQLObjectType({
  name: "User",
  description: "User",
  fields: () => ({
    id: globalIdField("User"),
    firstName: {
      type: GraphQLNonNull(GraphQLString),
      resolve: ({ firstName }) => firstName,
    },
    lastName: {
      type: GraphQLNonNull(GraphQLString),
      resolve: ({ lastName }) => lastName,
    },
  }),
});

export const QueryType = new GraphQLObjectType({
  name: "Query",
  description: "The root of all... queries",
  fields: () => ({
    node: NodeField,
    nodes: NodesField
  }),
});
```

现在，我们将在我们的`User`类型中创建一个名为`interfaces`的新属性，并传递我们的`NodeInterface`。首先，从我们的`NodeInterface.ts`文件中导入`NodeInterface`，现在我们将向我们的`UserType`添加一个新属性，它看起来像这样:

```
import { GraphQLObjectType, GraphQLString, GraphQLNonNull } from "graphql";
import { globalIdField } from "graphql-relay";

import { NodeField, NodesField, NodeInterface } from "./NodeInterface";

export const UserType: GraphQLObjectType = new GraphQLObjectType({
  name: "User",
  description: "User",
  fields: () => ({
    id: globalIdField("User"),
    firstName: {
      type: GraphQLNonNull(GraphQLString),
      resolve: ({ firstName }) => firstName,
    },
    lastName: {
      type: GraphQLNonNull(GraphQLString),
      resolve: ({ lastName }) => lastName,
    },
  }),
  interfaces: () => [NodeInterface],
});

export const QueryType = new GraphQLObjectType({
  name: "Query",
  description: "The root of all... queries",
  fields: () => ({
    node: NodeField,
    nodes: NodesField
  }),
});
```

现在让我们从`graphql-relay`导入`connectionDefinitions`，并创建一个名为`UserConnection`的新变量。我们将使用这个`UserConnection`变量为我们的用户创建一个连接类型。我们将在我们的`graphql.ts`内部创建`UserConnection`。

同样在我们的`graphql.ts`文件中，我们将创建一个新的查询来获取我们的用户`users`。我们将通过我们的`UserConnection`，作为一个论点，我们将重视来自`graphql-relay`的`connectionArgs`，并通过它。为了正确地对结果进行分页，我们将导入`connectionFromArray`并传递我们的用户数组和参数。

在所有这些变化之后，我们的`graphql.ts`看起来像这样:

```
import { GraphQLObjectType, GraphQLString, GraphQLNonNull } from "graphql";
import {
  globalIdField,
  connectionDefinitions,
  connectionFromArray,
  connectionArgs,
  mutationWithClientMutationId,
} from "graphql-relay";

import { NodeField, NodesField, NodeInterface } from "./NodeInterface";
import { IUser } from "./types";
import { users } from "./utils";

export const UserType: GraphQLObjectType = new GraphQLObjectType<IUser>({
  name: "User",
  description: "UserType",
  fields: () => ({
    id: globalIdField("User"),
    firstName: {
      type: GraphQLNonNull(GraphQLString),
      resolve: ({ firstName }) => firstName,
    },
    lastName: {
      type: GraphQLNonNull(GraphQLString),
      resolve: ({ lastName }) => lastName,
    },
  }),
  interfaces: () => [NodeInterface],
});

export const UserConnection = connectionDefinitions({
  name: "User",
  nodeType: UserType,
});

export const QueryType = new GraphQLObjectType({
  name: "Query",
  description: "QueryType",
  fields: () => ({
    node: NodeField,
    nodes: NodesField,
    users: {
      type: GraphQLNonNull(UserConnection.connectionType),
      args: {
        ...connectionArgs,
      },
      resolve: (_, args) => connectionFromArray(users, args),
    },
  }),
});
```

现在，在我们的服务器上要做的最后一件事是创建一个变异，我们将创建一个名为`UserCreate`的变异，在这里我们将创建一个新用户并将其添加到我们的阵列中。我们的`graphql. ts`文件现在看起来像这样:

```
import { GraphQLObjectType, GraphQLString, GraphQLNonNull } from "graphql";
import {
  globalIdField,
  connectionDefinitions,
  connectionFromArray,
  connectionArgs,
  mutationWithClientMutationId,
} from "graphql-relay";

import { NodeField, NodesField, NodeInterface } from "./NodeInterface";
import { IUser } from "./types";
import { users } from "./utils";

export const UserType: GraphQLObjectType = new GraphQLObjectType<IUser>({
  name: "User",
  description: "UserType",
  fields: () => ({
    id: globalIdField("User"),
    firstName: {
      type: GraphQLNonNull(GraphQLString),
      resolve: ({ firstName }) => firstName,
    },
    lastName: {
      type: GraphQLNonNull(GraphQLString),
      resolve: ({ lastName }) => lastName,
    },
  }),
  interfaces: () => [NodeInterface],
});

export const UserConnection = connectionDefinitions({
  name: "User",
  nodeType: UserType,
});

export const QueryType = new GraphQLObjectType({
  name: "Query",
  description: "QueryType",
  fields: () => ({
    node: NodeField,
    nodes: NodesField,
    users: {
      type: GraphQLNonNull(UserConnection.connectionType),
      args: {
        ...connectionArgs,
      },
      resolve: (_, args) => connectionFromArray(users, args),
    },
  }),
});

const UserCreate = mutationWithClientMutationId({
  name: "UserCreate",
  inputFields: {
    firstName: {
      type: new GraphQLNonNull(GraphQLString),
    },
    lastName: {
      type: new GraphQLNonNull(GraphQLString),
    },
  },
  mutateAndGetPayload: async ({ firstName, lastName }) => {
    const newUser = { firstName, lastName };
    users.push(newUser);
    return {
      message: "Success",
      error: null,
    };
  },
  outputFields: {
    message: {
      type: GraphQLString,
      resolve: ({ message }) => message,
    },
    error: {
      type: GraphQLString,
      resolve: ({ error }) => error,
    },
  },
});

export const MutationType = new GraphQLObjectType({
  name: "Mutation",
  description: "MutationType",
  fields: () => ({
    UserCreate,
  }),
});
```

[GraphQL 服务器规范](https://relay.dev/docs/en/graphql-server-specification)通过让您遵循严格的规则，确保您拥有一个非常模块化、可伸缩和健壮的 GraphQL API。为了在前端使用中继，应该在后端实现这些约定。

遵循这些规范可以使您的代码防弹并在将来易于扩展，您可以非常容易地实现诸如分页连接和身份验证之类的东西。你可以在这里找到这篇文章的代码。

## 结论

有时使用灵活的技术并不是最佳选择，它会导致一些不确定性，并且可能无法长期扩展。Relay 是一种非常固执和结构化的技术，它肯定会确保您以正确的方式使用 GraphQL 服务器，如果您遵循约定，就不会有任何问题。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.