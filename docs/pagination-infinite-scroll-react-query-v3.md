# React Query v3 的分页和无限滚动

> 原文：<https://blog.logrocket.com/pagination-infinite-scroll-react-query-v3/>

当大型数据集处理不当时，开发人员和最终用户都会感受到负面影响。前端开发人员可以用来有效呈现大型数据集的两种流行的 UI 模式是分页和无限滚动。这些模式通过一次只呈现或获取小块数据来提高应用程序的性能，通过允许用户轻松浏览数据来大大提高 UX。

在本教程中，我们将学习如何使用 React Query 实现分页和无限滚动。我们将使用[随机用户 API](https://randomuser.me/documentation) ，它允许您在一个请求中或者在带有分页的小块中获取多达 5000 个随机用户。本文假设您对 React 有基本的了解。下面的 gif 是我们将要构建的一个演示:

![React Query Random User API](img/244ce898b8803cb5e1a787b3e3c3d547.png)

我们开始吧！

## 反应查询

[React Query 使得在 React 应用中获取、缓存、同步和更新服务器状态](https://blog.logrocket.com/whats-new-in-react-query-3/)变得容易。React Query 提供了一些功能，如数据缓存、对同一数据的多个请求进行重复数据删除、在后台更新状态数据、性能优化，如分页和延迟加载数据、记忆查询结果、预取数据、突变等，这些功能允许无缝管理服务器端状态。

所有这些功能都是用几行代码实现的，React Query 会在后台为您处理其余的工作。

## 设置项目

我们首先初始化一个新的 React 应用程序并安装 React Query，如下所示:

```
npx create-react-app app-name
npm install react-query

```

用`npm start`启动服务器，让我们开始吧！

### 设置反应查询

为了初始化 React Query 的新实例，我们将从 React Query 导入`QueryClient`和`QueryClientProvider`。然后，我们用`QueryClientProvider`包装这个应用，如下所示:

```
//App.js

import {
  QueryClient,
  QueryClientProvider,
} from 'react-query'

const queryClient = new QueryClient()

ReactDOM.render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      <App />
    </QueryClientProvider>
  </React.StrictMode>,
  document.getElementById('root')
);

```

`useQuery`钩子用于从 API 获取数据。查询是对具有唯一键的异步数据源的声明性依赖。为了实现分页，我们理想情况下需要增加或减少查询的`pageIndex`或光标。将`keepPreviousData`设置为`true`会给我们带来以下好处:

*   即使查询关键字已更改，上次成功提取的数据仍将可用
*   一旦新数据到达，以前的数据将与新数据交换
*   `isPreviousData`检查查询当前提供了哪些数据

在 React Query 以前的版本中，分页是用 [`usePaginatedQuery()`](https://react-query.tanstack.com/guides/migrating-to-react-query-3) 实现的，在编写本文时已经弃用。让我们在`src`文件夹中创建一个新组件，并将其命名为`Pagination.js`:

```
// Pagination.js

import React from 'react'

function Pagination() {
  return (
    <div>Pagination View</div>
  )
}

export default Pagination;

```

接下来，我们将编写一个获取数据并将其传递给`useQuery`钩子的函数:

```
// Pagination.js

const [page, setPage] = useState(1);

const fetchPlanets = async (page) => {
  const res = await fetch(`https://randomuser.me/api/page=${page}&results=10&seed=03de891ee8139363`);
  return res.json();
}

const {
    isLoading,
    isError,
    error,
    data,
    isFetching,
    isPreviousData
  } = useQuery(['users', page], () => fetchPlanets(page), { keepPreviousData: true });

```

注意我们是如何传入一个页码和`results=10`的，每页只获取 10 个结果。

`useQuery`钩子返回数据和重要的状态，可以用来随时跟踪请求。在任何给定时刻，查询只能处于其中一种状态。

*   `isLoading or status === 'loading'`:查询没有数据，当前正在提取
*   `isError or status === 'error'`:查询遇到错误
*   `isSuccess or status === 'success'`:查询成功，有数据

我们还有`isPreviousData`，它之所以可用是因为我们将`keepPreviousData`设置为`true`。使用这些信息，我们可以在 JSX 中显示结果:

```
// Pagination.js

if (isLoading) {
    return <h2>Loading...</h2>
  }

  if (isError) {
    return <h2>{error.message}</h2>
  }

return (
 <div>

      <h2>Paginated View</h2>

      {data && (
        <div className="card">
          {data?.results?.map(user => <Users key={user.id} user={user} />)}
        </div>
      )}

      <div>{isFetching ? 'Fetching...' : null}</div>
    </div>
)

```

为了显示获取的数据，我们将创建一个名为`Users`的可重用无状态组件:

```
//Users.js

import React from 'react';

const Users = ({ user }) => {
  return (
    <div className='card-detail'>
      &lt;img src={user.picture.large} />
      <h3>{user.name.first}{user.name.last}</h3>
    </div>
  );
}

export default Users;

```

接下来，在`Pagination.js`文件中，我们将为用户实现在不同页面之间导航的导航:

```
  // Pagination.js

   <div className='nav btn-container'>
        <button
          onClick={() => setPage(prevState => Math.max(prevState - 1, 0))}
          disabled={page === 1}
        >Prev Page</button>

        <button
          onClick={() => setPage(prevState => prevState + 1)}
        >Next Page</button>
      </div>

```

在下面的代码中，我们根据用户点击的按钮增加或减少传递给 API 的页码:

```
// Pagination.js

import React, { useState } from 'react';
import { useQuery } from 'react-query';
import User from './User';

const fetchUsers = async (page) => {
  const res = await fetch(`https://randomuser.me/api/?page=${page}&results=10&seed=03de891ee8139363`);
  return res.json();
}

const Pagination = () => {
  const [page, setPage] = useState(1);

  const {
    isLoading,
    isError,
    error,
    data,
    isFetching,
  } = useQuery(['users', page], () => fetchUsers(page), { keepPreviousData: true });

  if (isLoading) {
    return <h2>Loading...</h2>
  }

  if (isError) {
    return <h2>{error.message}</h2>
  }

  return (
    <div>

      <h2>Paginated View</h2>

      {data && (
        <div className="card">
          {data?.results?.map(user => <User key={user.id} user={user} />)}
        </div>
      )}
      <div className='nav btn-container'>
        <button
          onClick={() => setPage(prevState => Math.max(prevState - 1, 0))}
          disabled={page === 1}
        >Prev Page</button>

        <button
          onClick={() => setPage(prevState => prevState + 1)}
        >Next Page</button>
      </div>
      <div>{isFetching ? 'Fetching...' : null}</div>
    </div>
  );
}

export default Pagination;

```

代替`useQuery`钩子，我们将使用`useInfiniteQuery`钩子将更多的数据加载到现有的数据集上。

关于`useInfiniteQuery`，有几点需要注意:

*   `data`现在是一个包含无限查询数据的对象
*   `data.pages`是包含提取页面的数组
*   `data.pageParams`是包含用于获取页面的页面参数的数组
*   `fetchNextPage`和`fetchPreviousPage`功能现已可用
*   `getNextPageParam`和`getPreviousPageParam`选项均可用于确定是否有更多数据要加载，以及是否有更多信息要获取
*   一个`hasNextPage`，如果`getNextPageParam`返回一个非未定义的值，则为`true`
*   一个`hasPreviousPage`，如果`getPreviousPageParam`返回一个非未定义的值，则为`true`
*   `isFetchingNextPage`和`isFetchingPreviousPage`布尔值区分后台刷新状态和加载更多状态

> 注意:`getNextPageParam`和`getPreviousPageParam`提供的信息是查询函数中的一个附加参数，在调用`fetchNextPage`或`fetchPreviousPage`函数时可以随意覆盖。

让我们在`src`文件夹中创建另一个名为`InfiniteScroll.js`的组件。我们将编写获取数据的函数，并将其传递给`useInfiniteQuery`钩子，如下所示:

```
//InfiniteScroll.js

const fetchUsers = async ({ pageParam = 1 }) => {
    const res = await fetch(`https://randomuser.me/api/?page=${pageParam}&results=10`);
    return res.json();
}

    const {
        isLoading,
        isError,
        error,
        data,
        fetchNextPage,
        isFetching,
        isFetchingNextPage
    } = useInfiniteQuery(['colors'], fetchUsers, {
        getNextPageParam: (lastPage, pages) => {
            return lastPage.info.page + 1
        }
    })

```

使用上面的代码，我们可以很容易地在 UI 上实现一个 load more 按钮，方法是等待获取第一批数据，在`getNextPageParam`中返回下一个查询的信息，然后调用`fetchNextPage`获取下一批数据。

让我们呈现检索到的数据并实现一个 load more 按钮:

```
// InfiniteScroll.js
if (isLoading) {
        return <h2>Loading...</h2>
    }

    if (isError) {
        return <h2>{error.message}</h2>
    }

    return (
        <>
            <h2>Infinite Scroll View</h2>
            <div className="card">
                {data.pages.map(page =>
                    page.results.map(user => <User key={user.id} user={user} />)
                )}
            </div>
            <div className='btn-container'>
                <button onClick={fetchNextPage}>Load More</button>
            </div>
            <div>{isFetching && !isFetchingNextPage ? 'Fetching...' : null}</div>
        </>
    )

```

为了显示数据，我们重用了`Users`组件。

注意当点击 **load more** 按钮时，我们是如何调用`fetchNextPage`的。`getNextPageParam`中返回的值被自动传递给端点，以便获取另一组数据:

```
// InfiniteScroll.js

import { useInfiniteQuery } from 'react-query'
import User from './User';

const fetchUsers = async ({ pageParam = 1 }) => {
    const res = await fetch(`https://randomuser.me/api/?page=${pageParam}&results=10`);
    return res.json();
}

const InfiniteScroll = () => {

    const {
        isLoading,
        isError,
        error,
        data,
        fetchNextPage,
        isFetching,
        isFetchingNextPage
    } = useInfiniteQuery(['colors'], fetchUsers, {
        getNextPageParam: (lastPage, pages) => {
            return lastPage.info.page + 1
        }
    })

    if (isLoading) {
        return <h2>Loading...</h2>
    }

    if (isError) {
        return <h2>{error.message}</h2>
    }

    return (
        <>
            <h2>Infinite Scroll View</h2>
            <div className="card">
                {data.pages.map(page =>
                    page.results.map(user => <User key={user.id} user={user} />)
                )}
            </div>
            <div className='btn-container'>
                <button onClick={fetchNextPage}>Load More</button>
            </div>
            <div>{isFetching && !isFetchingNextPage ? 'Fetching...' : null}</div>
        </>
    )
}

export default InfiniteScroll;

```

让我们导入`App.js`中的组件，并适当地渲染它们:

```
// App.js

import './App.css';
import Pagination from './Pagination';
import InfiniteScroll from './InfiniteScroll';
import { useState } from 'react';

function App() {
  const [view, setView] = useState('pagination')

  return (
    <div >
      <h1>Welcome to Random Users</h1>

      <nav className='nav'>
        <button onClick={() => setView('pagination')}>Pagination</button>
        <button onClick={() => setView('infiniteScroll')}>Infinite Scroll</button>
      </nav>

      {view === 'pagination' ? <Pagination /> : <InfiniteScroll />}
    </div>
  );
}

export default App;

```

最后，我们添加 CSS:

```
body {
  margin: 0;
  font-family: sans-serif;
  background: #222;
  color: #ddd;
  text-align: center;
}

.card{
  display: flex;
  justify-content: space-between;
  text-align: center;
  flex-wrap: wrap;
  flex: 1;
}

.card-detail{
  box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
  width: 15rem;
  height: 15rem;
  margin: 1rem;

}

.card-detail h3{

  color: #ffff57;
}

.btn-container{
  text-align: center;
  margin-bottom: 5rem;
  margin-top: 2rem;
}

.nav{
  text-align: center;
}

.nav button{
  margin-right: 2rem;
}

button{
  padding: 0.5rem;
  background-color: aqua;
  border: none;
  border-radius: 10px;
  cursor: pointer;
}

```

## 结论

在本文中，我们学习了如何使用 React Query 实现分页和无限滚动，React Query 是一个非常流行的状态管理 React 库。React Query 经常被描述为 React 生态系统中的缺失部分。在本文中，我们已经看到了如何通过调用一个钩子并传入一个函数来完全管理整个请求-响应周期，而不会产生歧义。

我希望你喜欢这篇文章！如果你有任何问题，一定要留下评论。编码快乐！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)