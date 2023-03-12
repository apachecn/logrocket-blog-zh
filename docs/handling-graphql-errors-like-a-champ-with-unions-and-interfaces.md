# 用联合和接口像冠军一样处理 GraphQL 错误

> 原文：<https://blog.logrocket.com/handling-graphql-errors-like-a-champ-with-unions-and-interfaces/>

毫无疑问，GraphQL 最好的特性之一是它令人敬畏的类型系统。

结合 GraphQL 代码生成器和 TypeScript 或 Flow 等类型化 Javascript 子集等工具，您可以在几秒钟内生成完全类型化的数据获取代码。

我无法回忆起在没有 GraphQL 生态系统的情况下设计和构建 API 的时代。

当我开始使用 GraphQL 时，我在改变我在休息时思考所形成的思维模式时遇到了一些问题。

我特别不满意的一点是错误处理。在传统的 HTTP 中，您有不同的状态代码来表示不同类型的错误(或成功)。

当 GraphQL 越来越流行时，我记得某个终端制作了一个 meme，显示一个 Apollo 服务器记录了一个错误对象，状态代码为 200，标题为`ok`。我想知道为什么 GraphQL 打破了这些广泛使用的标准。

今天，我知道 GraphQL 给了我们以更好、更明确的方式处理错误的能力。

## 处理 GraphQL 中的错误

在我们看一下我今天如何设计我的 API 之前，我想展示一下直到最近我是如何处理错误的。

我将在整篇文章中使用`react-apollo`和`apollo-server`代码示例。但是，这些概念应该适用于任何其他客户端和服务器框架。

让我们先来看看下面的 JSON 对象:

```
{
  "errors": [
    {
      "message": "Name for character with ID 1002 could not be fetched.",
      "locations": [ { "line": 6, "column": 7 } ],
      "path": [ "hero", "heroFriends", 1, "name" ]
    }
  ],
  "data": {
    "hero": {
      "name": "R2-D2",
      "heroFriends": [
        {
          "id": "1000",
          "name": "Luke Skywalker"
        },
        {
          "id": "1002",
          "name": null
        },
        {
          "id": "1003",
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

这看起来熟悉吗？

这段精确的代码是从 [GraphQL 规格错误部分](https://graphql.github.io/graphql-spec/draft/#example-90475)复制的。如果您已经将 GraphQL API 集成到您的应用程序中，您可能会对这种响应格式很熟悉。

根据设计，GraphQL 具有声明字段可空的能力。尽管这些数据是可选的，但它也允许我们在解析器抛出错误时发送部分结果。

这是 GraphQL 区别于严格 REST 的一点。

如果解析器抛出错误——在本例中，id 为 1002 的主角的名称解析器——一个包含关键错误的新数组将被追加到响应 JSON 对象中。

该数组包含一个错误对象，该对象具有错误的原始消息、路径和查询位置。

解析器的代码如下所示:

```
const resolvers = {
  Hero: {
    name: (parent, args, context) => {
      throw new Error(
        "Name for character with ID 1002 could not be fetched."
      );
    },
  },
};
```

我曾经认为这很酷。

然后我意识到我需要更详细的信息——比如状态代码或错误代码。我如何区分“用户不存在”和“用户阻止了你”的错误？

社区了解到了这一点，并将扩展的概念添加到了 GraphQL 规范中。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

扩展只不过是一个可以添加到错误对象(或响应对象)中的附加对象。

```
{
  "errors": [
    {
      "message": "Name for character with ID 1002 could not be fetched.",
      "locations": [ { "line": 6, "column": 7 } ],
      "path": [ "hero", "heroFriends", 1, "name" ],
      "extensions": {
        "code": "CAN_NOT_FETCH_BY_ID",
        "timestamp": "Fri Feb 9 14:33:09 UTC 2018"
      }
    }
  ]
}
```

使用`extensions`，我们可以向我们的错误对象添加一个`code`属性，然后客户端可以使用它(例如一个`switch`或`if`语句)。

这比解析错误消息来解释错误要方便得多。

像 Apollo Server 这样的框架提供了可以用错误消息和代码初始化的错误类:

```
import {
  ApolloError,
}  from "apollo-server";

const resolvers = {
  Hero: {
    name: (parent, args, context) => {
      throw new ApolloError(
        "Name for character with ID 1002 could not be fetched.",
        "CAN_NOT_FETCH_BY_ID",
      );
    },
  },
};
```

当然，我也很快开始采用这种错误处理方式，但是我很快意识到有一些缺点会降低我的工作效率:

### 错误没有被放在它们发生的地方

当然，您有一个描述错误发生位置的路径数组(例如[ `hero`、`heroFriends`、1、`name`)。您可以在客户机中构建一些自定义函数，将错误映射到查询路径。

我个人认为每个错误都应该在应用程序的 UI 中处理。

默认情况下将错误放在不同的地方并不能真正鼓励开发人员优雅地处理错误。

此外，像 relay modern [这样的框架鼓励你只将片段注入你的组件](https://relay.dev/docs/en/fragment-container)。

为了进行正确的错误处理，您需要应用自定义逻辑将正确的错误注入到正确的组件中。

听起来像是我个人想要避免的额外工作。

### 使用这些错误剥夺了 GraphQL 的一个主要好处:类型安全

如前所述，GraphQL API 的主要好处之一是类型安全。

默认情况下，模式是自省的，并公开所有可用类型和字段的完整注册。

不幸的是，错误代码不遵循任何模式(至少不符合 GraphQL 规范)。

如果在解析器中键入错误消息或扩展代码，将不会引发类型错误。

GraphQL 引擎不关心消息的结构。

此外，错误代码只是一个可选的扩展。我目前不知道有什么工具可以生成类型安全的错误代码，也看不到字段(或解析器)可能抛出的所有可用错误代码的概述。

### 当使用 errors 数组时，我们又回到了猜测类型的老路上。

后端和前端开发人员现在又多了一个棘手的问题(这是他们最初试图通过转向 GraphQL 来避免的)。)

不要误解我——即使你有一个完全类型化的 GraphQL API，仍然应该有一些文档。

由 GraphQL 或 GraphQL Playground 等工具生成的 API 浏览器应该更容易发现和理解 GraphQL API 提供的内容，但它不应该用使用示例来代替文档。

## 我们可以用现有的 GraphQL 原语做得更好

最近，有很多关于使用联合类型处理错误的讨论。联合类型表示字段可以返回的对象列表。

```
type User {
  id: ID!
  login: String!
}

type UserNotFoundError {
  message: String!
}

union UserResult = User | UserNotFoundError

type Query {
  user(id: ID!): UserResult!
}
```

在下面的模式中，字段`user`可以返回一个`User`或`UserNotFoundError`。我们只是返回一个不同的类型，而不是在解析器中抛出一个错误。

您将发送到服务器的查询如下所示:

```
query user($id: ID!) {
  user(id: $id) {
    ... on UserNotFoundError {
      message
    }
    ... on User {
      id
      login
    }
  }
}
```

相应地，`apollo-server`解析器可能看起来如下所示:

```
const resolvers = {
  Query: {
    user: async (parent, args, context) => {
      const userRecord = await context.db.findUserById(args.id);
      if (userRecord) {
        return {
          __typename: "User",
          ...userRecord,
        };
      }
      return {
        __typename: "UserNotFound",
        message: `The user with the id ${args.id} does not exist.`,
      };
    },
  },
};
```

当使用联合时，您必须返回一个`__typename`,这样 apollo-server 就知道结果具有哪种类型，以及必须使用哪个解析器映射来解析已解析类型的更多字段值。

这允许我们像普通的 GraphQL 类型一样对错误建模。这样，我们重新获得了类型安全的力量:我们可以拥有更复杂的类型，而不是处理消息和错误代码。

下面是一个返回`UserRegisterInvalidInputError`错误类型的登录变异的例子。

尽管有一般的错误消息，该类型还为单个输入字段提供字段。

```
type User {
  id: ID!
  login: String!
}

type UserRegisterResultSuccess {
  user: User!
}

type UserRegisterInvalidInputError {
  message: String!
  loginErrorMessage: String
  emailErrorMessage: String
  passwordErrorMessage: String
}

input UserRegisterInput {
  login: String!
  email: String!
  password: String!
}

union UserRegisterResult = UserRegisterResultSuccess | UserRegisterInvalidInputError

type Mutation {
  userRegister(input: UserRegisterInput!): UserRegisterResult!
}
```

您甚至可以更进一步，添加返回新的、更复杂的`object types`的字段。

客户端实现可能类似于以下内容:

```
import React, { useState } from "react";
import { useUserRegisterMutation } from "./generated-types"
import idx from "idx";
import { useFormState } from 'react-use-form-state';

const RegistrationForm: React.FC<{}> = () => {
  const [userRegister, { loading, data }] = useUserRegisterMutation();
  const loginState = useFormState("login");
  const emailState = useFormState("email");
  const passwordState = useFormState("password");

  useEffect(() => {
    if (idx(data, d => d.userRegister.__typename) === "UserRegisterResultSuccess") {
      alert("registration success!");
    }
  }, [data]);

  return (
    <form
      onSubmit={(ev) => {
        ev.preventDefault();
        userRegister();
      }}
    >
      <InputField
        {...loginState}
        error={idx(data, d => d.userRegister.loginErrorMessage)}
      />
      <InputField
        {...emailState}
        error={idx(data, d => d.userRegister.emailErrorMessage)}
      />
      <InputField
        {...passwordState}
        error={idx(data, d => d.userRegister.passwordErrorMessage)}
      />
      <SubmitButton />
      {idx(data, d => d.userRegister.message) || null}
      {loading ? <LoadingSpinner /> : null}
    </form>
  )
}
```

## GraphQL 使您能够根据您的 UI 来塑造您的数据树

这就是为什么你也应该根据用户界面来设计你的错误类型。

如果您有不同类型的错误，您可以为每个错误创建一个类型，并将它们添加到您的联合列表中:

```
type User {
  id: ID!
  login: String!
}

type UserRegisterResultSuccess {
  user: User!
}

type UserRegisterInvalidInputError {
  message: String!
  loginErrorMessage: String
  emailErrorMessage: String
  passwordErrorMessage: String
}

type CountryBlockedError {
  message: String!
}

type UserRegisterInput {
  login: String!
  email: String!
  password: String!
}

union UserRegisterResult =
  UserRegisterResultSuccess
  | UserRegisterInvalidInputError
  | CountryBlockedError

type Mutation {
  userRegister(input: UserRegisterInput!): UserRegisterResult!
}
```

这使得每种错误类型都有其独特的属性。

让我们跳过这个需求的前端部分:

> 你对你的 API 有了一个新的要求:来自 X 国的人不应该再被允许注册，因为你的公司所在的国家有一些奇怪的制裁。

看起来很简单，只需在后端添加一些新类型，对吗？

不幸的是，没有。前端开发人员现在也必须更新他的查询，因为一种新类型的错误，没有被任何选择集覆盖，现在被返回。

这意味着以下查询:

```
mutation userRegister($input: UserRegisterInput!) {
  userRegister(input: $input) {
    __typename
    ... on UserRegisterResultSuccess {
      user {
        id
        login
      }
    }
    ... on UserRegisterInvalidInputError {
      message
      loginErrorMessage
      emailErrorMessage
      passwordErrorMessage
    }
  }
}
```

需要更新为:

```
mutation userRegister($input: UserRegisterInput!) {
  userRegister(input: $input) {
    __typename
    ... on UserRegisterResultSuccess {
      user {
        id
        login
      }
    }
    ... on UserRegisterInvalidInputError {
      message
      loginErrorMessage
      emailErrorMessage
      passwordErrorMessage
    }
    ... on CountryBlockedError {
      message
    }
  }
}
```

否则，客户端将不会收到可显示的`CountryBlockedError`的错误消息。

每当我们添加新的错误类型时，强迫客户端应用程序的开发人员调整他们的 GraphQL 文档似乎不是一个聪明的解决方案。

让我们仔细看看我们的错误对象:

```
type UserRegisterInvalidInputError {
  message: String!
  loginErrorMessage: String
  emailErrorMessage: String
  passwordErrorMessage: String
}

type CountryBlockedError {
  message: String!
}
```

它们都有一个共同的属性:`message`

此外，我们可以假设，将来可能添加到联合中的每个错误也将有一个`message`属性。

幸运的是，GraphQL 为我们提供了`interfaces`，允许我们描述这样的抽象。

```
interface Error {
  message: String!
}
```

接口描述可以由不同类型实现/共享的字段:

```
interface Node {
  id: ID!
}

type User implements Node {
  id: ID!
  login: String!
}

type Post implements Node {
  id: ID!
  title: String!
  body: String!
}

type Query {
  entity(id: ID!): Node
}
```

对于查询，接口的强大之处在于能够通过接口而不是类型来声明数据选择。

这意味着我们以前的模式可以转换成以下形式:

```
type User {
  id: ID!
  login: String!
}

interface Error {
  message: String!
}

type UserRegisterResultSuccess {
  user: User!
}

type UserRegisterInvalidInputError implements Error {
  message: String!
  loginErrorMessage: String
  emailErrorMessage: String
  passwordErrorMessage: String
}

type CountryBlockedError implements Error {
  message: String!
}

type UserRegisterInput {
  login: String!
  email: String!
  password: String!
}

union UserRegisterResult =
  UserRegisterResultSuccess
  | UserRegisterInvalidInputError
  | CountryBlockedError

type Mutation {
  userRegister(input: UserRegisterInput!): UserRegisterResult!
}
```

这两种错误类型现在都实现了错误接口。

我们现在可以调整我们的查询，如下所示:

```
mutation userRegister($input: UserRegisterInput!) {
  userRegister(input: $input) {
    __typename
    ... on UserRegisterResultSuccess {
      user {
        id
        login
      }
    }
    ... on Error {
      message
    }
    ... on UserRegisterInvalidInputError {
      loginErrorMessage
      emailErrorMessage
      passwordErrorMessage
    }
  }
}
```

甚至不再需要声明`CountryBlockedError`选择集。它自动被`Error`选择集覆盖。

此外，如果任何实现`Error`接口的新类型被添加到`UserRegisterResult`联合中，错误消息将自动包含在结果中。

当然，您仍然需要在客户端添加一些逻辑来处理您的错误状态，但是您可以在需要更多工作的错误(如`UserRegisterInvalidInputError`)和所有其他只显示某种对话框的错误(如`CountryBlockedError`)之间切换，而不是显式地处理每一个错误。

例如，如果您遵循以单词`Error`结束所有错误类型的惯例，您可以构建一个处理多种错误类型的抽象。

```
import React, { useState } from "react";
import { useUserRegisterMutation } from "./generated-types"
import idx from "idx";
import { useAlert } from "./alert";

const RegistrationForm: React.FC<{}> = () => {
  const [userRegister, { loading, data }] = useUserRegisterMutation();
  const loginState = useFormState("login");
  const emailState = useFormState("email");
  const passwordState = useFormState("password");
  const showAlert = useAlert();

  useEffect(() => {
    const typename = idx(data, d => d.userRegister.__typename)
    if (typename === "UserRegisterResultSuccess") {
      alert("registration success!");
    } else if (typename.endsWith("Error")) {
      showAlert(data.userRegister.message);
    }
  }, [data]);

  return (
    <form
      onSubmit={(ev) => {
        ev.preventDefault();
        userRegister();
      }}
    >
      <InputField
        {...loginState}
        error={idx(data, d => d.userRegister.loginErrorMessage)}
      />
      <InputField
        {...emailState}
        error={idx(data, d => d.userRegister.emailErrorMessage)}
      />
      <InputField
        {...passwordState}
        error={idx(data, d => d.userRegister.passwordErrorMessage)}
      />
      <SubmitButton />
      {loading ? <LoadingSpinner /> : null}
    </form>
  )
}
```

稍后，当您的团队决定一个新的错误应该以不同于其他错误的方式处理时，您可以通过在`useEffect`中添加一个新的 else/if 语句来调整代码。

## 结论

像 GraphQL 代码生成器(或 apollo-cli)这样的代码生成工具可以解析您的 GraphQL 模式，并为您的 GraphQL 服务器生成类型脚本定义文件。

在过去的两个月里， [@dotansimha](https://github.com/dotansimha) 和 [I](https://github.com/n1ru4l) 用 GraphQL 代码生成器为联合和接口生成了正确的类型。使用这样的工具可以显著改善您的工作流程。

GraphQL Codegen 最好的一点是，它可以与您正在使用的几乎所有 GraphQL 客户端一起使用，无论是 react-apollo、urql，甚至是 Java。

我强烈建议您查看 GraphQL Codegen ,因为它可以进一步改进您的工作流程。

你已经采用 GraphQL 了吗？你已经在使用抽象的联合和接口了吗？你利用类型生成吗？

大家在评论里讨论吧。

还有，在这些平台上随时关注我。我写的是 JavaScript、Node、React、GraphQL 和 DevOps。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.