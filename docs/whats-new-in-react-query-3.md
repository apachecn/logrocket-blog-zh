# React Query 3 的新特性

> 原文：<https://blog.logrocket.com/whats-new-in-react-query-3/>

## 介绍

默认情况下，React 不支持路由、数据获取和复杂的状态管理。

因此，开发了一些第三方库和框架来满足这些需求。

诸如 [React-Router](https://reactrouter.com/) 和 [Reach Router](https://reach.tech/router/) 之类的库已经被开发用于路由，而诸如 [Redux](https://redux.js.org/) 、 [Mobx](https://mobx.js.org/README.html) 和[反冲](https://recoiljs.org/)之类的库和框架已经被开发用于复杂的状态管理。

[React Query](https://react-query.tanstack.com/overview) 是一个第三方库，它将自己描述为:

> React 缺少数据提取库；因为现成的 React 不提供从组件获取和更新数据的方法

然而，React Query 不仅仅是获取和更新数据。它是一个开箱即用的异步数据状态管理库，类似于 [Apollo Client](https://www.apollographql.com/docs/react/) ，但不同于 Apollo Client，它支持 REST 和 GraphQL。

简而言之，React Query 是一组定制挂钩，使得在 React 中获取、缓存和更新异步或服务器状态变得容易。

## 为什么使用 React Query？

我们在构建 React 应用程序时面临的挑战之一是确定一个有效的模式来(获取和更新)处理服务器状态。React 不会给我们任何现成的东西。

因此，开发人员通过在一个`useEffect`钩子中获取数据(服务器状态),然后将结果复制到一个基于组件的状态(客户端状态),创造了他们自己的方法。这种模式是可行的，但不是最佳的。

让我们通过考虑下面的代码来演示这种模式的缺点:

```
import "./styles.css";
import React, {useEffect, useState} from "react";
import axios from 'axios';
export default function App() {
  const [isLoading, setIsLoading] = useState(false);
  const [isError, setIsError] = useState(false);
  const [userData, setUserData] = useState(null);
  useEffect(() => {
    async function getUserData() {
      try {
        setIsLoading(true);
        const {data} = await axios.get(`https://jsonplaceholder.typicode.com/users/1`);
        setUserData(data);
        setIsLoading(false);
      } catch (error) {
        setIsLoading(false);
        setIsError(error);
      }
    }
    getUserData();
  }, []);
  return (
    <div>
      {isLoading && (<div> ...Loading </div>)}
      {isError && (<div>An error occured: {isError.message}</div>)}
      {userData && (<div>The username is : {userData.username}</div>)}
    </div>
  )
}

```

[玩代码](https://codesandbox.io/s/boring-satoshi-g5dgj?fontsize=14&hidenavigation=1&theme=dark)。

在上面的小例子中，我们从`[https://jsonplaceholder.typicode.com/users/1](https://jsonplaceholder.typicode.com/users/1)`端点获取用户数据。然后，我们根据 API 调用的状态(成功、加载或错误)呈现一个视图。这种方法有几个问题，例如:

1.  我们必须在组件树中的`App`组件或任何高层组件上进行这个调用。这是为了使用 [prop drilling](https://blog.logrocket.com/the-upsides-of-prop-drilling-in-react/) 将数据传递给需要它们的其他(嵌套)组件。React 中正确钻孔是一种反模式，应该尽可能避免。这里有一些帮助避免[支柱钻孔](https://blog.logrocket.com/mitigating-prop-drilling-with-react-and-typescript/)的策略
2.  我们必须在获取数据的每个组件中重复这种样板代码。上面的例子需要使用`useState`和`useEffect`钩子，我们使用了三种不同的客户端(本地)状态(isLoading、isError 和 userData)来确定 API 调用的状态。所有这些都必须在我们需要获取数据的每个组件中重写。尽管我们可以将这个样板代码的钩子逻辑抽象成一个[定制的可重用钩子](https://blog.logrocket.com/advanced-react-hooks-creating-custom-reusable-hooks/)，并在我们的应用中重用它，但它仍然不能解决我们所有的问题
3.  使用这种模式，开发人员经常会陷入将客户端状态和服务器状态混合在一起的陷阱。结果状态对象可能包含一些基于组件的(客户端)状态，例如侧边栏状态或带有服务器状态的主题颜色，例如获取的用户数据。最终的状态对象可能是这样的:

    ```
    const [state, setState] = {   showSideBar: false,   theme:       "dark",   currentUser: {},   users:       [],   posts:       [] };
    ```

为了避免这个麻烦，一些开发人员求助于像 Mobx 和 Redux 这样的全局状态管理库。虽然这可能行得通，但是它们给我们的应用程序增加了一层额外的复杂性，在某些情况下，这可能是多余的。

此外，尽管一些传统的状态管理库在管理客户端状态方面非常出色，但在处理服务器状态方面却不那么高效。

由于以下原因，服务器状态管理对此有独特的要求:

1.  它是远程持续存在的，不受我们的控制
2.  其他人可以访问和改变它
3.  它会变得陈旧(过时)
4.  它需要一个异步 API 来获取和更新

因此，为了有效地管理服务器状态，我们需要:

1.  将我们的服务器状态存储在内存缓存中
2.  知道状态是否改变的机制
3.  定期在后台更新状态
4.  为了尽快反映更新

这些都不是我们可以自己轻松编写的特性。幸运的是，React Query 就是为了解决这些问题而创建的。

现成的 React Query 为我们提供了一组钩子，用于获取、缓存和更新异步数据(服务器状态)。

在下一节中，我们将通过重构上面的样板代码来使用 React Query 来详细说明这一点。

## 入门指南

## 装置

```
# NPM
npm i react-query

#Yarn
yarn add react-query

```

要重构上面的样板代码以使用 React Query，请遵循以下步骤:

1.  使用`QueryClient`和`QueryClientProvider`像这样设置连接我们的应用程序到 React Query 的缓存的配置:

    ```
    import "./styles.css"; import React from "react"; import { QueryClient, QueryClientProvider } from "react-query"; import User from "./Components/User"; // Create a client const queryClient = new QueryClient(); export default function App() {   return (     // Provide the client to your App     <QueryClientProvider client={queryClient}>       <User />     </QueryClientProvider>   ); }
    ```

2.  使用 React Query 和 axios 之类的数据获取库从组件中获取数据:

    ```
    import React from "react"; import { useQuery } from "react-query"; import axios from "axios"; const User = () => {   const fetchUser = async () => {     const { data } = await axios.get(       `https://jsonplaceholder.typicode.com/users/1`     );     return data;   };   const {             isLoading,             isSuccess,             error,             isError,             data: userData          } = useQuery("user",fetchUser);   return (     <div>       {isLoading && <article>...Loading user </article>}       {isError && <article>{error.message}</article>}       {isSuccess && (         <article>           <p>Username: {userData.username}</p>         </article>       )}     </div>   ); }; export default User;
    ```

[玩代码](https://codesandbox.io/s/summer-haze-p94ch?fontsize=14&hidenavigation=1&theme=dark)。

从上面的例子中，我们可以看到，通过使用 React Query 中的`useQuery`钩子，我们已经从代码中移除了复杂的`useEffect`和`useState`逻辑。这是更干净，可维护，干燥。

此外，React Query 将服务器状态存储在我们上面配置的缓存中，并从那里为我们的组件提供服务，从而提高了性能。

React Query 通过定期在后台对端点进行 API 调用来保持服务器状态的更新。这是为了确保我们的组件总是获得最新的服务器状态。

React 查询库带来了更多的优势。我们上面的小例子给出了对 React Query 的高级介绍，我们仅仅触及了它的表面特性。

在下一节中，我们将重点关注 React Query 第 3 版中新增的令人惊叹的特性。

## 新功能

## 查询数据`Selectors`

有了这些特性，React Query 保留了 GraphQL 的一些优点。`useQuery`和`useInfiniteQuery`挂钩现在有了一个`select`选项。这使我们能够选择或转换查询结果的所需部分。

在上面的例子中，我们现在可以只选择查询结果中需要的部分，如下所示:

```
...
  const { 
    isLoading, 
    isSuccess, 
    error, 
    isError, 
    data: username 
  } = useQuery("user", fetchUser,{
       select: (user) => user.username
  });
...

```

然后我们会这样渲染`username`:

```
...
{isSuccess && (
  <article>
    <p>Username: {username}</p>
  </article>
)}
...

```

[见完整代码](https://codesandbox.io/s/tender-euclid-s5gdh?fontsize=14&hidenavigation=1&theme=dark)。

## `useQueries`钩子

`useQueries`钩子用于获取可变数量的查询，并返回一个包含所有查询结果的数组。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`useQueries`钩子接受一个参数，该参数是一个包含不同查询选项对象的数组，如下所示:

```
const results = useQueries([
 { queryKey: ['user', 1], queryFn: fetchUser },
 { queryKey: ['user', 2], queryFn: fetchUser },
 { queryKey: ['user', 3], queryFn: fetchUser },
 { queryKey: ['user', 4], queryFn: fetchUser },
])

```

## 重试/离线突变

React 查询突变从未有过`retry`，但是在 React 查询 3 中，您可以向`useMutation`钩子传递第二个参数来配置`retry`，如下所示:

```
const mutation = useMutation(addUser, {retry: 3});

```

因此，如果由于设备离线而导致变异失败，那么当设备重新连接时，变异会重试设定的次数(在上面的例子中是三次)。

但是，默认情况下，如果失败，React Query 不会重试变异。

## 持续突变

在 React 查询 3 中，可以使用水合物函数将突变保存到存储中。如果您希望暂停突变(因为设备处于离线状态),并在设备重新连接时恢复突变，这将非常有用。你可以在这里获得更多关于这个[的信息。](https://react-query.tanstack.com/guides/mutations#persist-mutations)

## `QueryObserver`

`QueryObserver`函数与`[new](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new)`操作符一起工作。它用于创建或观察如下查询:

```
const observer = new QueryObserver(queryClient, { queryKey: 'videos' })
const unsubscribe = observer.subscribe(result => {
 // do something here.
 unsubscribe()
})

```

`QueryObserver`也可以用来观察和切换查询。它有两个参数，一个是 T1，另一个是 T2。`QueryObserver`的选项与`[useQuery](https://react-query.tanstack.com/reference/useQuery#_top)`挂钩的选项相同。

## `InfiniteQueryObserver`

这类似于`QueryObserver`函数。它也适用于新的操作符，但是它的用例是不同的。

`InfiniteQueryObserver`钩子使我们能够观察无限的查询并在它们之间切换。它有两个参数，即`queryClient`和一个`option object`，如下所示:

```
const observer = new InfiniteQueryObserver(queryClient, {
 queryKey: 'videos',
 queryFn: fetchVideos,
 getNextPageParam: (lastPage, allPages) => lastPage.nextCursor,
 getPreviousPageParam: (firstPage, allPages) => firstPage.prevCursor,
}) 
const unsubscribe = observer.subscribe(result => {
  // do something
  unsubscribe()
})

```

`InfiniteObserverQuery`的选项与`[InfiniteQuery](https://react-query.tanstack.com/reference/useInfiniteQuery#_top)`挂钩的选项完全相同。

## `QueriesObserver`

`QueriesObserver`可用于创建或观察多个查询，如下所示:

```
const observer = new QueriesObserver(queryClient, [
 { queryKey: ['users', 1], queryFn: fetchUsers },
 { queryKey: ['users', 2], queryFn: fetchUsers },
]) 
const unsubscribe = observer.subscribe(result => {
 // do something
 unsubscribe()
})

```

它还与`new`操作符一起工作，并接受两个参数，即`queryClient`和一个选项`object`。

## 为特定查询设置默认选项

`QueryClient.setQueryDefaults()`方法使我们能够为特定查询设置默认选项，如下所示:

```
queryClient.setQueryDefaults('users', { queryFn: fetchUsers }
function GetUsers() {
 const { data } = useQuery('users')
   return data;
}

```

## 为特定突变设置默认选项

这种方法允许我们为特定的突变设置默认选项，如下所示:

```
queryClient.setMutationDefaults('addUser', { mutationFn: addUser })
function AddUser() {
  const { mutate } = useMutation('addUser')
    // do something...
}

```

## `useIsFetching`钩子

`useIsFetching`钩子是一个可选的钩子，它返回应用程序在后台获取的查询数。

它现在有一个过滤器，可以用来只返回与过滤器匹配的查询数。考虑下面的例子:

```
 ...
const isFetching = useIsFetching() // returns the how many queries are fetching
const isFetchingPosts = useIsFetching(['users']) // returns how many queries matching the users filter that are fetching.
...

```

## 最后的想法

React Query 是一个非常棒的库，它解决了使用 React 时管理异步数据的难题。它使得使用服务器状态变得轻而易举。

正如我们在本帖中看到的，React Query 3 为这个伟大的库添加了一些令人敬畏的特性。此外，React 查询核心现在从 React 中分离出来，它可以独立使用，也可以与其他框架一起使用。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)