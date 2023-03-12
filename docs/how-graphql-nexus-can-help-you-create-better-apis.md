# GraphQL Nexus 如何帮助您创建更好的 API

> 原文：<https://blog.logrocket.com/how-graphql-nexus-can-help-you-create-better-apis/>

在处理 API 时，GraphQL 真的正在成为一种标准。它帮助许多公司和开发人员发展壮大，并创建更好的 API，从而为他们的客户创建更好的应用程序。很多公司，包括 GitHub、Shopify、Airbnb、PayPal、[都采用了 GraphQL](https://graphql.org/users/) 。

在开始创建一个新的 API 时，有许多要点和概念使 GraphQL 成为一个容易的选择。这个想法就是只要求你想要的，然后得到你想要的。REST API 并不总是这样。基本上，在 REST APIs 中，当我们想要获取数据时，我们接收的是整个对象，而不是我们想要的数据的特定部分。假设我们只想获得一个特定用户的名字，为了做到这一点，我们通常会点击一个端点来获得用户的数据。问题是，如果我们不仅仅是为了检索用户名而创建一个特定的端点，我们将会收到整个用户对象，包含比我们想要的更多的数据。

构建一个可伸缩的 GraphQL API 并不容易，需要花费大量的时间、精力和研究。我们需要确切地知道将要使用哪种方法，哪种技术，以及如何正确地设计模式。

本质上，GraphQL 有两种主要方法可以用来创建我们的 API，即代码优先方法和 SDL 优先方法(也称为模式优先方法)。

在本文中，我们将了解如何使用 GraphQL Nexus(一种代码优先和强类型的 GraphQL 模式构造)来创建更好的、类型安全的和声明性的 GraphQL APIs。

为了更好地理解 GraphQL Nexus，我们需要了解一下这两种方法是如何工作的。

## 代码优先与 SDL 优先

几年前 GraphQL 发布时，脸书发布了一个 GraphQL APIs 的参考实现，名为 [graphql-js](https://github.com/graphql/graphql-js) 。

[graphql-js](https://github.com/graphql/graphql-js) 参考实现使用代码优先的方法，graphql 模式是通过使用普通的 JavaScript 对象创建的:

```
import { GraphQLSchema, GraphQLObjectType, GraphQLString } from 'graphql';

const schema = new GraphQLSchema({
  query: new GraphQLObjectType({
    name: 'QueryType',
    fields: {
      helloWorld: {
        type: GraphQLString,
        resolve: () => 'Hello world!',
      },
    },
  }),
})
```

使用代码优先的方法，我们在代码中定义模式。在大型代码库中，代码优先的方法对于组织和快速移动 GraphQL 模式非常有用。

虽然代码优先的方法是构建 GraphQL API 的一个很好的方法，但是许多开发人员认为代码优先的方法写起来很冗长。对于一些开发人员来说，在实现模式时，使用代码创建 GraphQL 模式的想法在视觉上并不容易理解。

SDL 优先(也称为模式优先)方法非常适合这种情况。在 SDL 优先的方法中，我们使用 [SDL(模式定义语言)](https://www.prisma.io/blog/graphql-sdl-schema-definition-language-6755bcb9ce51)定义 GraphQL 模式:

```
type Query {
  helloWorld: String!
}
```

SDL 是一种定义良好的构建模式的方式，它是官方 GraphQL 规范的一部分。模式优先的方法使得我们的 GraphQL 模式更容易理解和可视化，使得创建模式的过程更加直观。

缺点之一是我们需要从 GraphQL 模式中单独实现解析器。SDL 优先方法的另一个缺点是，有时在模式中使用指令毫无意义。

既然我们知道了代码优先和 SDL 优先之间的区别，我们可以了解更多关于 GraphQL Nexus 的信息。

## GraphQL 关系

由 [Prisma](https://www.prisma.io/about) 、[创建的 GraphQL Nexus](https://github.com/graphql-nexus/schema) 是一个声明性的、代码优先的、强类型的 GraphQL 模式构造。

[GraphQL Nexus](https://github.com/graphql-nexus/schema) 是 [Nexus 框架](https://nexusjs.org/)的一部分，但是可以单独使用。要开始使用它，我们所要做的就是安装两个依赖项:

```
yarn add @nexus/schema graphql
```

这里我们需要`graphql`依赖，因为 [GraphQL Nexus](https://nexus.js.org/) 需要它作为对等依赖。

GraphQL Nexus 的设计结合了 SDL 优先方法的简单性和代码优先方法的长期性和组织性。它是建立在`graphql-js`之上的，所以如果你以前用过它，你可能会感觉有些熟悉。

正如[文档](https://nexus.js.org/docs/getting-started)所说:

> GraphQL Nexus 的设计考虑了 TypeScript/JavaScript IntelliSense，并结合了 type script 泛型、条件类型和类型合并，以提供开箱即用的完整自动生成类型覆盖。

## (计划或理论的)纲要

安装完 GraphQL Nexus 后，要创建您的第一个模式，您需要做的就是从 GraphQL Nexus 导入`makeSchema`:

```
import { makeSchema } from '@nexus/schema';
```

`makeSchema`函数用于定义 GraphQL 模式。您可以组合使用 GraphQL Nexus 定义的 GraphQL 类型，甚至可以传递您手动定义为类型的类型，如`Scalar`、`ObjectType`、`Enum`等。

使用`makeSchema`函数，非常类似于 [graphql-js](https://github.com/graphql/graphql-js) 的实现。为您的模式创建一个对象，在该对象中，您可以传递两个属性，`types`和`outputs`:

```
const schema = makeSchema({
  types: [Query],
  outputs: {
    schema: __dirname + '/generated/schema.graphql',
    typegen: __dirname + '/generated/typings.ts',
  },
});
```

`types`属性是您可以传递 GraphQL 类型的地方。在`outputs`属性中，可以传递两个名为`schema`和`typegen`的属性。GraphQL Nexus 将为您生成一个包含整个 GraphQL 模式的名为`schema.graphql`的文件，以及一个包含我们所有类型的名为`typing.ts`的文件。

当然，您可以在`makeSchema`函数中传递两个以上的属性。GraphQL Nexus 有一个名为`plugins`的属性，其中可以有一组插件来扩展 GraphQL 的功能:

```
const schema = makeSchema({
  types: [Query, Mutation],
  outputs: {
    schema: __dirname + '/generated/schema.graphql',
    typegen: __dirname + '/generated/typings.ts',
  },
  plugins: {
    fieldAuthorizePlugin({
      ...
    }),
    nullabilityGuard({
      ...
    })
  }
});
```

## 问题

要使用 GraphQL Nexus 创建查询，首先需要导入`QueryType`:

```
import { queryType, makeSchema } from '@nexus/schema';
```

导入之后，您可以创建一个名为`Query`的对象，所有的查询都将存放在这个对象中。您需要使用一个名为`definition`的函数，在这个函数中，您可以使用`t`参数来定义查询的响应类型:

```
const Query = queryType({
  definition(t) {
    ...
  },
});
```

例如，假设我们想要创建一个名为`user`的查询。如果我们想返回一个`string`作为该查询的预期响应类型，我们可以这样做:

```
const Query = queryType({
  definition(t) {
    t.string('user', {
      resolve: (root, args, context) => 'Hello!',
    });
  },
});
```

注意，我们使用了`t.string`来指定查询响应的类型。您可以使用多种类型来指定响应类型，如`boolean`、`field`、`float`、`id`、`int`等。

## 突变

使用 GraphQL Nexus 创建变异与查询非常相似，首先，您需要导入`MutationType`:

```
const Mutation = mutationType({
  definition(t) {
    t.string('createUser', {
      resolve: (root, args, context) => 'Hello!',
    });
  },
});
```

GraphQL Nexus 的一个优点是，您可以定义可用于任何对象或接口类型的参数:

```
import { stringArg } from '@nexus/schema';
const userArgs = {
  name: stringArg({
    required: true,
    description: 'The name of the user to be created'
  })
};
```

您可以使用许多不同的参数函数，如`arg`、`intArg`、`stringArg`、`floatArg`、`idArg`和`booleanArg`。

## 类型

GraphQL API 最重要的部分是对象类型。要使用 GraphQL Nexus 创建一个，您需要做的就是导入`objectType`。

```
import { objectType } from '@nexus/schema';
```

创建对象类型与创建查询或变异非常相似。您可以传递一个名为`name`的属性来定义类型的名称，并在`definition`函数中定义类型的属性:

```
import { objectType } from '@nexus/schema';
const User = objectType({
  name: 'User',
  definition(t) {
    t.int('id');
    t.string('title');
    t.int('age');
  }
});
```

您可以非常容易地创建其他类型，如`Scalar`、`ObjectType`、`Enum`，GraphQL Nexus 很好地支持这些类型:

```
import { scalarType } from '@nexus/schema';
const DateScalar = scalarType({
  name: 'DateScalar',
  description: 'Date custom scalar',
  ...
});
```

另一个有用的类型是`inputObjectType`。您可以创建一个可以定义并作为输入值传递的输入对象类型，当您有一个复杂的输入值时，这对于突变和查询非常有用:

```
import { inputObjectType } from '@nexus/schema';
const UserInputType = inputObjectType({
  name: 'UserInputType',
  description: 'User input type',
  definition(t) {
    t.string('id', { required: true });     
    t.string('name');
    t.int('age');
  }
});

```

## 插件

GraphQL Nexus 的另一个非常好的特性是插件。GraphQL Nexus 有一个插件 API，允许您在构建 GraphQL 模式时定义抽象。

GraphQL Nexus 也有很多开箱即用的插件，比如:

*   [查询复杂度插件](https://nexus.js.org/docs/plugin-queryComplexity) —测量 GraphQL 模式中的查询复杂度不是很多开发人员都会做的事情，但有时这是必要的。单个查询可能会为您的服务器产生巨大的工作负载。您可以使用这个插件来限制和跟踪您的 GraphQL 操作，并定义字段级复杂度值

GraphQL Nexus 还有一些非常有用的插件，例如:

在某些情况下，代码优先的方法比 SDL 优先的方法更好。有了 GraphQL Nexus，我们可以从这种方法中获益，并创建一个更健壮、定义更好的 GraphQL 模式。GraphQL Nexus 创建于`graphql-js`实现之上，是创建真正优秀、健壮、可维护和简洁的 GraphQL API 的最佳选择之一。

## 结论

在本文中，我们首先了解了 GraphQL 中的两种主要方法，代码优先和 SDL 优先。我们还看了 GraphQL Nexus，如何创建您的第一个模式，以及定义您的查询和变化。GraphQL 附带了很多特性，比如插件，我们可以用它们来定义自己的抽象，并帮助我们改进我们的 GraphQL API。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.