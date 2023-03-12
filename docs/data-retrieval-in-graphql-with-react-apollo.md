# 用 react-apollo - LogRocket Blog 在 GraphQL 中进行数据检索

> 原文：<https://blog.logrocket.com/data-retrieval-in-graphql-with-react-apollo/>

在本文中，我们将探索如何在 Apollo 客户端中使用 GraphQL 查询语言来高效无缝地从服务器获取数据。阅读结束时，你将学会如何使用和安装 GraphQL 和 Apollo 客户端，以及如何在`react-apollo`中执行`query`和`mutation`操作的两种不同方法。

## GraphQL

GraphQL 是一种查询语言，它使用一个定义好的系统来描述用户应该如何从 API 请求数据。GraphQL 要求用户指定需要传输的确切数据——通常来自服务器——然后这些数据将被加载到客户端。

在我们了解如何使用 Apollo 执行特定的查询操作之前，我们将回顾一些可以在 GraphQL 查询库中执行的基本操作。

### 1.询问

一个 [GraphQL 查询操作](https://blog.logrocket.com/client-side-query-customization-in-graphql/)与从服务器端获取基本数据有关。

```
  {
    query getItems {
      Items {
        id
        chair
        stool
       }
     }
  }
```

上面是一个名为`getItems`的 GraphQL 查询，它获取了`id`、`chair`和`stool`。

### 2.变化

添加`mutation`关键字在 GraphQL 应用程序中执行基本的 [CRUD(创建、读取、更新和删除)操作](https://blog.logrocket.com/crud-with-node-graphql-react/)。

```
{
    mutation addUser(name: String!, email: String!){
      addUser(name: $name, email: $email){
        id
        name
        email
        created_at
      }
    }
  }
```

当指定了用户名和电子邮件时，上面的`addUser`变异返回用户的`name`、`id`、`email`和`create_at`字段。

### 3.签署

这是一个基于事件的操作，当执行特定事件时，数据从服务器传输到客户端。要在 GraphQL 中创建订阅，我们需要将订阅类型添加到我们的模式中:

```
const typeDefs = gql`
  type Subscription {
    bookAdded: Book
  }`

```

下一步是创建一个`resolver`。`Pubsub`在这种情况下从一个突变内部被通知一个事件。

```
const BOOK_ADDED = 'BOOK_ADDED';

const resolvers = {
  Subscription: {
    bookAdded: {
      subscribe: () => pubsub.asyncIterator([BOOK_ADDED]),
    },
  },

  Mutation: {
    addBook(root, args, context) {
      pubsub.publish(BOOK_ADDED, { bookAdded: args });
      return postController.addBook(args);
    },
  },
};

```

订阅由变异内部的一个`publish`调用触发。

## 阿波罗

[Apollo](https://github.com/ApolloAuto/apollo) 在 GraphQL 应用程序中充当本地和远程数据存储的状态管理库。借助 Apollo 可以执行的一些基本操作包括:获取、本地状态存储和缓存。Apollo 客户端还有一个与 React 的内置集成，这使得开发变得更加容易。

Apollo 客户端的一些基本功能包括:

### 声明性数据提取

Apollo client 允许用户从应用程序编程接口指定所需的确切数据。React 使用一个`useQuery`钩子，使我们能够在 GraphQL 应用程序中执行数据检索和数据获取操作。下面是官方文档中的一个示例:

```
function Feed() {
    const { loading, error, data } = useQuery(GET_DOGS);
    if (error) return <Error />;
    if (loading || !data) return <Fetching />;

    return <DogList dogs={data.dogs} />;
  }

```

在`useQuery`钩子的帮助下，我们能够从 GraphQL 服务器获取数据，这将导致 UI 更新以反映任何更改。

### 贮藏

除了可以使用 GraphQL 执行的主要获取操作之外，智能数据缓存操作也可以在几乎不需要配置的情况下执行。

下面是用于数据缓存的 Apollo 客户端配置:

```
 import { ApolloClient, InMemoryCache } from '@apollo/client';

const client = new ApolloClient({
  cache: new InMemoryCache()
});

```

阿波罗客户端的其他优势包括:

1.  支持现代[反应钩子](https://blog.logrocket.com/react-reference-guide-hooks-api/)
2.  和睦相处
3.  强大的社区支持
4.  页码

## GraphQL 和 Apollo 客户端的安装和使用

如果您尚未设置 React 应用程序，请运行以下命令:

```
npx create-react-app 
```

要在 React 应用程序中使用 GraphQL 和 Apollo，您需要安装以下依赖项:

```
npm install apollo-boost graphql react-apollo 
```

您的带有 GraphQL 的 React 应用程序现在已经设置好，可以使用了！

要创建连接，首先创建一个`index.js`文件并添加以下代码片段:

```
import ApolloClient from "apollo-boost";
import { ApolloProvider } from 'react-apollo';

const client = new ApolloClient({
    uri: //graghql server url should be here
  }); 
```

接下来，用 ApolloProvider 包装您的应用程序组件，将客户端作为道具传递:

```
ReactDOM.render(
    <ApolloProvider client={client}>
      <App />
    </ApolloProvider>,
    document.getElementById("root")
  ); 
```

在开始，我们讨论了可以用 GraphQL 执行的基本操作，包括`query`和`mutation`。现在让我们来看看如何使用`useQuery`钩子方法和渲染道具方法在阿波罗上执行这些操作。

### 问题

GraphQL 查询涉及数据的读取和提取。在使用 React Apollo 执行该操作时，我们可以采用两种方法:挂钩方法和渲染道具方法。

#### `useQuery`挂钩方法

首先，让我们创建一个 GraphQL 查询，并将其命名为`getItems`，如下所示:

```
import React from "react";
import { useQuery } from "react-apollo";
import { gql } from "apollo-boost";

const GET_ITEMS = gql`
  query GetItems {
    items {
      id
          oneitem
    }
  }
`;

```

接下来，创建一个功能组件并将 GraphQL 查询传递给`useQuery`钩子，如下所示:

```
function DisplayItems() {
  const { loading, error, data } = useQuery(GET_ITEMS);

  if (loading) return 'Loading...';
  if (error) return `Error! ${error.message}`;

  return (
    <select name="items">
      {data.items.map(item => (
        <option key={item.id} value={item.oneitem}>
          {item.oneitem}
        </option>
      ))}
    </select>
  );
}

```

当获取新数据或发生错误时，应用程序将更新以反映新的更改。

#### **渲染道具进场**

对于渲染道具方法，第一步保持不变，除了从我们之前安装的`react-apollo`中额外导入查询:

```
import React from "react";
import { Query } from "react-apollo";
import { gql } from "apollo-boost";

const GET_ITEMS = gql`
  query GetItems {
    items {
      id
          oneitem
    }
  }
`;

```

接下来，我们将通过将创建的 GraphQL 查询传递给`query`来使用它，如下所示:

```
function DisplayItems() {

  if (loading) return 'Loading...';
  if (error) return `Error! ${error.message}`;

  return (
    <Query query={GET_ITEMS}>
    <select name="items">
      {data.items.map(item => (
        <option key={item.id} value={item.oneitem}>
          {item.oneitem}
        </option>
      ))}
    </select>
    </Query>
  );
}

```

就像钩子方法一样，应用程序要么更新以反映新数据，要么返回一个错误。

### 变化

变异包括执行创建、更新和删除操作。与查询一样，在处理变异时，我们可以利用 hooks 方法和 render props 方法。

#### 钩子接近

首先让我们导入依赖项，包括从`react-apollo`导入的`useMutation`。

```
import React, { useState } from 'react';
import { useMutation } from 'react-apollo';
import { gql } from 'apollo-boost';

const ADD_TODO = gql`
  mutation AddTodo($type: String!) {
    addTodo(type: $type) {
      id
      type
    }
  }
`;

```

下一步是创建一个名为`AddToDo`的功能组件，并将我们的`ADD_TODO`变异传递给`useMutation`钩子:

```
function AddTodo() {
  let input;
  const [addTodo, { data }] = useMutation(ADD_TODO);

  return (
    <div>
      <form
        onSubmit={e => {
          e.preventDefault();
          addTodo({ variables: { type: input.value } });
          input.value = '';
        }}
      >
        <input
          ref={node => {
            input = node;
          }}
        />
        <button type="submit">Add Todo</button>
      </form>
    </div>
  );
}

```

#### 渲染道具方法

与 hooks 方法类似，我们需要从`react-apollo`导入一个新的`mutation`来导入必要的依赖项:

```
import React, { useState } from 'react';
import { useMutation } from 'react-apollo';
import { gql } from 'apollo-boost';

const ADD_TODO = gql`
  mutation AddTodo($type: String!) {
    addTodo(type: $type) {
      id
      type
    }
  }
`;

```

要完成，请按如下方式传递创建的变异:

```
function AddTodo() {
  let input;

  return (
    <Mutation mutation={ADD_TODO}>
    <div>
      <form
        onSubmit={e => {
          e.preventDefault();
          addTodo({ variables: { type: input.value } });
          input.value = '';
        }}
      >
        <input
          ref={node => {
            input = node;
          }}
        />
        <button type="submit">Add Todo</button>
      </form>
    </div>
    </Mutation>
  );
}

```

## 结论

在处理数据提取时，GraphQL 是一个很好的选择，因为它解决了 RESTful APIs 带来的过度提取和提取不足的问题。Apollo Client 使 GraphQL 与服务器的连接变得简单、无缝和高效。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。