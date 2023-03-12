# Relay v11 中的新内容

> 原文：<https://blog.logrocket.com/whats-new-in-relay-v11/>

GraphQL 是当今许多公司都在使用的技术(例如脸书)。它正在成为构建可靠的、可伸缩的、高性能的 API 的重要替代方法。

脸书[几个月前发布了一个新版本](https://engineering.fb.com/2020/05/08/web/facebook-redesign/)，并在他们的应用程序中大量使用 React、GraphQL 和最新版本的 [Relay](https://relay.dev/) 。脸书在生产中使用 Relay 已经有几年了，它正在帮助他们拥有一个更具可伸缩性、稳定性和可维护性的应用程序。

在本帖中，我们将介绍新版本的[继电器](https://relay.dev/)。我们将探索最新版本是如何工作的，以及我们如何创建更可靠和可伸缩的 React 和 GraphQL 应用程序。

## 继电器挂钩

[Relay](https://relay.dev/) 是一个强大的 JavaScript 框架，用于在 React 应用程序中使用 GraphQL。Relay 不同于其他 GraphQL 客户端，因为它更加结构化和固执己见。

[Relay](https://relay.dev/) 有助于构建更具可伸缩性、结构化和高性能的 React 和 GraphQL 应用。依靠 GraphQL 的最佳实践，如[片段](https://graphql.org/learn/queries/#fragments)、[连接](https://graphql.org/learn/pagination/#complete-connection-model)、[全局对象识别](https://graphql.org/learn/global-object-identification/)等，它使得在 GraphQL 中获取数据变得容易。

Relay 的最新版本于 2021 年 3 月 9 日发布，它有一个对开发者更友好的 API。新版本支持[反应悬念](https://reactjs.org/docs/concurrent-mode-suspense.html)，虽然这是一个实验性的功能。

Relay Hooks 是一组新的 API，用于在 React 应用程序中使用 React Hooks 获取和管理 GraphQL 数据。

从最新版本的 Relay 开始，让我们首先使用 [create-react-app](https://create-react-app.dev/) 创建一个新的 React 应用程序，并设置 Relay:

```
npx create-react-app graphql-relay-example --template typescript

```

创建 React 应用程序后，我们需要安装几个包来开始使用 Relay:

```
yarn add react-relay relay-runtime isomorphic-fetch

yarn add --dev @types/react-relay @types/relay-runtime graphql relay-compiler relay-compiler-language-typescript

```

现在，我们创建一个名为`environment.tsx`的文件，这是我们将要创建中继环境的地方。使用 Relay 的最佳方式是使用与 Relay 兼容的 GraphQL API，它将遵循最佳实践，并使实现诸如分页之类的事情更加容易。

在我们的`environment.tsx`文件中，放入以下代码:

```
import {
  Environment,
  Network,
  RecordSource,
  Store,
  RequestParameters,
  Variables
} from "relay-runtime";
import fetch from "isomorphic-fetch";

function fetchQuery(operation: RequestParameters, variables: Variables) {
  return fetch('https://podhouse-server.herokuapp.com/graphql', {
    method: "POST",
    headers: {
      Accept: "application/json",
      "Content-type": "application/json",
    },
    body: JSON.stringify({
      query: operation.text,
      variables,
    }),
    }).then((response: any) => {
      return response.json()
    })
}

const network = Network.create(fetchQuery);

const env = new Environment({
  network,
  store: new Store(new RecordSource(), {
    gcReleaseBufferSize: 10,
  }),
});

export default env;

```

在我们的`index.tsx`中，我们导入`RelayEnvironmentProvider`并传递我们的`environment`给它:

```
import React from "react";
import ReactDOM from "react-dom";
import { RelayEnvironmentProvider } from "react-relay/hooks";
import environment from "./environment";

ReactDOM.render(
  <RelayEnvironmentProvider environment={environment}>
    <React.StrictMode>
      <App />
    </React.StrictMode>
  </RelayEnvironmentProvider>,
  document.getElementById('root')
); 
```

现在，我们已经为我们的项目设置了 Relay，我们可以开始使用最新的版本，看看它是如何工作的。

## 用`useLazyLoadQuery`抓取

用 Relay 获取数据最简单的方法是使用`useLazyLoadQuery`。这个钩子将在渲染过程中获取数据，使其成为获取数据的最有效方式，但也是最简单的方式:

```
import { useLazyLoadQuery } from "react-relay/hooks";
useLazyLoadQuery(query, variables, options);

```

下面是`useLazyLoadQuery`的工作原理:

*   `query` —您需要它来传递您的 GraphQL 查询模板文本
*   `variables` —包含用于获取查询的值的对象。例如，当您想要在应用程序内部对用户进行身份验证时
*   `options` —是一个你定义了一些属性的对象。`fetchPolicy`用于决定数据是否需要缓存。`fetchKey`用于强制重新评估查询
*   `networkCacheConfig`是一个你可以定义到你的缓存配置选项的对象

`useLazyLoadQuery`应该总是在`RelayEnvironmentProvider`中使用。当网络请求正在进行时，`useLazyLoadQuery`可能会暂停您的数据，这取决于您选择的`fetchPolicy`。根据所选的`fetchPolicy`,您应该在应用中使用[反应暂停](https://reactjs.org/docs/concurrent-mode-suspense.html)作为[加载状态](https://relay.dev/docs/guided-tour/rendering/loading-states/)。

查看`useLazyLoadQuery`挂钩的示例:

```
import React from "react";
import graphql from "babel-plugin-relay/macro";
import { useLazyLoadQuery } from "react-relay/hooks";

const query = graphql`
  query SettingsQuery {
    currentUser {
      id
      _id
      email
    }
  }
`;

const Component = () => {
  const data = useLazyLoadQuery(
    query,
    {},
    {
      fetchPolicy: "store-and-network",
    }
  );

  return (
    <div>
      <h1>{data.currentUser.email}</h1>
    </div>
  );
};

export default Component;

```

## 用`usePreloadedQuery`抓取

`usePreloadedQuery`是最推荐的用 Relay 获取数据的钩子。它实现了[即取即渲染](https://reactjs.org/docs/concurrent-mode-suspense.html#approach-3-render-as-you-fetch-using-suspense)模式，这种模式允许我们加载我们需要的数据并并行渲染我们的组件。

`usePreloadedQuery`可能有点混乱，所以让我们弄清楚这个钩子是如何工作的:

*   `usePreloadedQuery`使用`useQueryLoader`，这是新版本继电器上的另一个挂钩
*   `useQueryLoader`是一个用于安全加载查询的钩子。它将保存一个查询引用，并在组件被释放时释放它
*   `useQueryLoader`设计为与`usePreloadedQuery`一起使用
*   `useQueryLoader`返回一个`queryReference`、一个`loadQuery`回调和一个`disposeQuery`回调
*   我们需要首先使用来自`useQueryLoader`的`loadQuery`回调，它将在 React 状态下存储一个查询引用
*   之后，我们将`queryReference`传递给我们的`usePreloadedQuery`，这将允许我们更早地获取数据，同时不会阻塞组件上的呈现

`usePreloadedQuery`是使用中继获取数据的最强大和推荐的方式。

查看`usePreloadedQuery`钩的使用示例:

```
import React, { useEffect } from "react";
import graphql from "babel-plugin-relay/macro";
import { useQueryLoader, usePreloadedQuery } from "react-relay/hooks";

const query = graphql`
  query UserQuery($_id: ID!) {
    user(_id: $_id) {
      id
      _id
      name
    }
  }
`;

const Component = () => {
  const [queryReference, loadQuery, disposeQuery] = useQueryLoader(query);

  useEffect(() => {
    loadQuery({ _id: _id }, { fetchPolicy: "store-or-network" });
    return () => {
      disposeQuery();
    };
  }, [loadQuery, disposeQuery, _id]);

  return (
    <React.Suspense fallback="Loading user...">
    {queryReference != null ? <UserComponent queryReference={queryReference} /> : null
    }
    </React.Suspense>
  );
};

const UserComponent = ({ queryReference }) => {
  const data = usePreloadedQuery(query, queryReference);
  return <h1>{data.user?.name}</h1>;
}

export default Component;

```

## `usePaginationFragment`

使用 Relay，让你的 GraphQL API [与 Relay](https://blog.logrocket.com/making-a-graphql-server-compatible-with-relay/) 兼容，并遵循 [GraphQL 规范](https://spec.graphql.org/June2018/)的一个好处是，它使得实现一些特性比如分页变得非常容易。

`usePaginationFragment`是一个钩子，可用于呈现一个片段并在其上分页:

```
import { usePaginationFragment } from "react-relay/hooks";
usePaginationFragment(query, variables, options);

```

下面是`usePaginationFragment`的工作原理:

*   `fragment`–graph QL 片段模板文字。GraphQL 片段必须有一个`@connection`和`@refetchable`指令，否则，它将抛出一个错误
*   `fragmentReference`–一个片段引用，Relay 使用它从存储中读取片段的数据

我们可以将`usePaginationFragment`和`usePreloadedQuery`一起使用。查看一个使用`usePreloadedQuery`钩子的例子。首先，我们创建查询和片段:

```
const query = graphql`
  query ProductsQuery($name: String!) {
    ...SearchProducts_products @arguments(name: $name)
  }
`;

const fragment = graphql`
fragment SearchProducts_products on Query
@argumentDefinitions(
name: { type: "String" }
after: { type: "String" }
first: { type: "Int", defaultValue: 30 }
before: { type: "String" }
last: { type: "Int" }
)
@refetchable(queryName: "SearchProductsPaginationQuery") {
products(
name: $name
after: $after
first: $first
before: $before
last: $last
) @connection(key: "SearchProducts_products", filters: ["name"]) {
edges {
node {
_id
name
image
}
}
}
}
`; 

```

现在，在我们的组件中，我们将查询传递给`useQueryLoader`钩子，在子组件中我们使用`usePreloadedQuery`:

```
import React, { useEffect } from "react";
import graphql from "babel-plugin-relay/macro";
import { useQueryLoader, usePreloadedQuery } from "react-relay/hooks";

const Component = () => {
  const [queryReference, loadQuery, disposeQuery] = useQueryLoader(query);

  useEffect(() => {
    loadQuery({ name: name }, { fetchPolicy: "store-or-network" });
    return () => {
      disposeQuery();
    };
  }, [loadQuery, disposeQuery, name]);

  return (
  <React.Suspense fallback="Loading products...">
    {queryReference != null ? (<ProductComponent queryReference={queryReference} />) : null}
  </React.Suspense>
  );
};

const ProductComponent = ({ queryReference }) => {
  const query = usePreloadedQuery(query, queryReference);
  const { data } = usePaginationFragment(fragment, query);

  return (
    <div>
      {data.friends?.edges.map(({ node }) => (
      <div>{node.name}</div>
      ))}
    </div>
  );
};

export default Component;

```

## `useMutation`

突变是 GraphQL 非常重要的一部分。它允许我们创建、更新和删除数据。

`useMutation`是用中继执行变异的新钩子。这是一个非常简单明了的挂钩，只有两个参数:

*   `mutation`—graph QL 变异模板文字
*   `commitMutationFn` —将被调用的函数。这个函数是可选的，大多数时候你不需要它

`useMutation`只返回两个值:

*   `commitMutation` —将执行变异的函数
*   `isInFlight` —检查突变是否仍在进行中的值。你可以想用多少次就用多少次`commitMutation`，所以通常你可以在飞行中一次使用多个变异

查看`usePreloadedQuery`挂钩的示例:

```
import React from "react";
import graphql from "babel-plugin-relay/macro";
import { useMutation } from "react-relay/hooks";

const mutation = graphql`
  mutation SignInWithEmail($input: SignInWithEmailInput!) {
    SignInWithEmail(input: $input) {
      token
      success
      error
    }
  }
`;

const Component = () => {
  const [commitMutation, isInFlight] = useMutation(mutation);

  const onSubmit = () => {
    commitMutation({
      variables: {
        input: {
          email: email,
          password: password,
        },
      },
      onCompleted: ({ SignInWithEmail }) => {
        if (SignInWithEmail?.error) {
          return SignInWithEmail?.error;
        }
        updateToken(SignInWithEmail?.token);
      },
    });
  };

  return (
    <form onSubmit={onSubmit}>
      <input type="text" value={email} />
      <input type="password" value={password} />
      <button type="submit">Submit</button>
    </form>
  );
};

export default Component;

```

## 结论

Relay 的最新版本带来了一组新的 API，它们将帮助我们构建更多可伸缩的 React 和 GraphQL 应用程序。React 钩子的使用可以帮助我们构建更加模块化和高性能的应用程序，使我们的代码更容易理解，并且没有意想不到的副作用。

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