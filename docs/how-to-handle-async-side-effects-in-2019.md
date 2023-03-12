# 2019 年如何处理异步副作用

> 原文：<https://blog.logrocket.com/how-to-handle-async-side-effects-in-2019/>

处理异步操作一直是 React 生态系统中开发人员的主要关注点。

有很多不同的方法来处理异步操作，包括 Redux-Saga，但是在本文中，我们将关注我认为目前最好的方法:使用`react-async`。

我们还将对`react-async`库和 React 中处理异步副作用的其他现有方法进行比较。

### 什么是 React Async？

React Async 是一个基于承诺的工具，它允许您以声明的方式处理承诺和获取数据。

它使得处理异步 UI 状态变得容易，而不需要假设数据的形状或请求的类型。

React Async 由一个 React 组件和几个钩子组成。您可以将它与 fetch、Axios、GraphQL 和其他数据提取库一起使用。

React Async 依赖于使用声明性语法、JSX 和 native promises 来解析数据，使其更接近您在代码中需要它们的位置(例如，在组件级别)，这与 Redux 等其他系统不同，在 Redux 等其他系统中，解析数据是在应用程序的更高级别使用 actions 和 reducers 之类的东西来进行的。

### 对异步使用做出反应

为了像下面的例子一样使用 React Async，我们将从`react-async`导入`useAsync`

然后我们可以创建我们的异步函数，它接收一个信号作为参数。这个信号就是我们的 AbortController API，它为我们提供了一种方法，可以在需要的时候取消我们发出的 fetch 调用。

在我们的组件中，我们调用`useAsync`并传递我们的异步函数。

调用`useAsync`返回一个对象，我们可以将它分解成三个重要的值:数据、错误和 isPending。

这些值告诉我们异步函数的状态——是仍然挂起、出错还是成功。

我们可以使用这些值中的每一个来为用户呈现适当的 UI:

```
import { useAsync } from "react-async"
// You can use async/await or any function that returns a Promise
const asyncFn = async ({ signal }) => {
  const res = await fetch(`/api/users`, { signal })
  if (!res.ok) throw new Error(res.statusText)
  return res.json()
}
const MyComponent = () => {
  const { data, error, isPending } = useAsync({ promiseFn: asyncFn })
  if (isPending) return "Loading..."
  if (error) return `Something went wrong: ${error.message}`
  if (data)
    <ul>
      {data.users.map(user => <li>{user.name}</li>)}
    </ul>
)
return null
```

有一些使用 React-Async 的方法:

*   作为一个钩子
*   用`useFetch`
*   作为一个组件
*   作为一个工厂
*   使用助手组件
*   作为的静态属性

我将简要介绍前三种方法，只是为了让您对这些实现有个概念，但是您可以随意参考官方使用指南来深入了解每种方法。

### 将异步反应为挂钩

React-Async 提供了一个名为`useAsync`的钩子。在您的组件中，您可以像这样调用这个钩子:

```
import { useAsync } from "react-async";

const MyComponent = () => {
  const { data, error, isPending } = useAsync({ promiseFn: loadPlayer, playerId: 1 })
  //...
};
```

### 与`useFetch`异步反应

使用`useFetch`，您正在创建一个异步获取函数，该函数可以稍后在您的组件中运行:

```
import { useFetch } from "react-async"
const MyComponent = () => {
  const headers = { Accept: "application/json" }
  const { data, error, isPending, run } = useFetch("/api/example", { headers }, options)
  // You can call "handleClick" later
  function handleClick() {
    run()
  }
<button onClick={handleClick}>Load</button>
}
```

### 将异步作为组件反应

这就是 React Async 真正让 JSX 大放异彩的地方:

```
import Async from "react-async"
const MyComponent = () => (
  <Async promiseFn={load}>
    {
      ({ data, error, isPending }) => {
        if (isPending) return "Loading..."
        if (error) return `Something went wrong: ${error.message}`
        if (data)
          return (<div> { JSON.stringify(data, null, 2) }</div>)
        return null
      }
    }
  </Async>
)
```

您必须将一个函数作为子组件传递给`Async`组件。

正如您所看到的，这个函数将根据我们提供给`Async`的异步函数的状态来评估不同的节点值。

### 反应异步 vs `useEffect`

与 Async/Await 结合使用不如 React Async 方便，尤其是当您开始考虑竞争条件、处理清理和取消挂起的异步操作时。

React Async 以非常高效的方式为您处理所有这些事情。

让我们看一个用`useEffect`和 Async/Await 处理竞争条件的典型例子:

```
const [usersList, updateUsersList] = useState();
useEffect(() => {
  const runEffect = async () => {
    const data = await fetchUsersList(filter);
    updateUsersList(data);
  };
  runEffect();
}, [updateUsersList, filter]);
```

在上面的例子中，如果出于某种原因我们必须调用两次`useEffect`，并且对`fetchUsersList`的第二次调用在第一次调用之前解决，那么我们就会有一个过时的“更新”列表。

您可以通过添加一种方法来解决这个问题，即在您认为必要时阻止`updateUsersList`调用的发生，但是，这种方法可能无法很好地适应多个`await`表达式。

另一方面，当您使用 React Async 时，您不必担心取消未解决的请求或处理适当的竞争条件，因为 React 已经为您处理了这些:

```
import { useAsync } from "react-async"
// You can use async/await or any function that returns a Promise
const fetchUsersList = async ({ signal }) => {
  const res = await fetch(`/api/users`, { signal })
  if (!res.ok) throw new Error(res.statusText)
  return res.json()
}
const filteredUsers = (users) => {
  // Filter users ...
}
const MyComponent = () => {
  const { data, error, isPending } = useAsync({ promiseFn: fetchUsersList})
  if (isPending) return "Loading..."
  if (error) return `Something went wrong: ${error.message}`
  if (data)
  <ul>
    { filteredUsers(data.users).map(user => <li>{user.name}</li>) }
  </ul>
)
return null
```

在上面的代码片段中，任何时候我们调用`fetchUsersList`，我们都将重新呈现`MyComponent`组件，这意味着我们将总是拥有我们期望的状态。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

此外，React Async 在内部为我们做了一次清理，并使用`AbortController` API 取消了未解决的承诺(即`signal`变量被传递给`fetchUsersList`函数)，因此我们不必担心竞争条件和取消我们不再需要的未解决的承诺。

如果您的应用程序非常简单，并且添加一个 14kb 的库来处理异步操作没有意义，那么您可以满足于稍微高级一点的`useEffect`实现。

在我看来，React Async 已经是相当轻量级的了，除了经过充分测试之外，它还有很多好处。

因此，除非节省 14kb 的好处至关重要，否则您可能希望使用 React Async。

### React Async vs Redux-Saga

`Redux-Saga`是一个库，旨在使应用程序副作用(即异步的事情，如数据获取和不纯的事情，如访问浏览器缓存)更容易管理，更有效地执行，更容易测试，更好地处理故障:[redux-saga.js.org。](https://redux-saga.js.org/)

Redux-Saga 比 React Async 需要更多的步骤来开始。

那是因为它是一个 Redux 中间件，也就是说你要为它设置 Redux。

Redux 的想法是为应用程序的所有或主要部分提供一个集中的状态。这样，您可以通过调度`actions`来更新您的状态。例如:

```
const Counter = ({ value }) =>
  <div>
    <button onClick={() => store.dispatch({type: 'INCREMENT_ASYNC'})}>
      Increment after 1 second
    </button>
    <hr />
    <div>
      Clicked: {value} times
    </div>
  </div>
```

Redux-Saga 依靠“ES6 生成器”帮助您进行网络调用或执行其他异步副作用:

```
function* incrementAsync() {
  yield delay(1000)
  yield put({ type: 'INCREMENT' })
}
```

如你所见，你产生副作用的地方离你的组件很远。通过在组件内调度操作来触发更新。然后，更新的状态通过你的道具进来。

相当标准的东西，但是与 React Async 给你的东西非常不同，而且不够直观。

### 结论

*   使用 React Async，您不需要像使用 Redux 那样假设您的数据将会是什么样子。这就像你通常如何使用承诺。
*   使用 React Async，您可以将数据解析到更接近您需要的位置，从而更清楚地了解正在发生的事情。

你不需要理解一个相当复杂的结构，比如 reducers 和 actions——你可以利用你已经知道并在你的组件中使用的东西——JSX、承诺和钩子。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)