# 使用 Redux Toolkit 的 createasncthunk-log rocket 博客

> 原文：<https://blog.logrocket.com/using-redux-toolkits-createasyncthunk/>

虽然 Redux store 拥有强大的状态管理功能，但它不知道如何处理异步逻辑。Redux 避免处理异步逻辑，仅仅是因为它不知道您想对获取的数据做什么，更不用说它是否被获取了——你好，错误。🙂

此后，中间件被用于 Redux 应用程序中来执行异步任务，其中 [Redux Thunk](https://github.com/reduxjs/redux-thunk) 的中间件是最流行的包。中间件旨在使开发人员能够编写具有副作用的逻辑——这是指现有客户端应用程序之外的任何外部交互，如从 API 获取数据。

在 Redux Toolkit 中，默认包含 Redux Thunk，允许`createAsyncThunk`在将处理后的结果发送给 reducers 之前执行延迟的异步逻辑。

在本文中，您将学习如何使用`createAsyncThunk` API 在 Redux 应用程序中执行异步任务。

## 先决条件

你需要有一些关于 Redux 的知识来理解 Redux Toolkit。然而，你可以参考这篇文章来学习如何用 Redux Toolkit 创建 Redux 应用。

## 了解函数参数

```
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit'

const initialState = {
  entities: [],
  loading: false,
}

const getPosts = createAsyncThunk(
  //action type string
  'posts/getPosts',
  // callback function
  async (thunkAPI) => {
    const res = await fetch('https://jsonplaceholder.typicode.com/posts').then(
    (data) => data.json()
  )
  return res
})

export const postSlice = createSlice({
  name: 'posts',
  initialState,
  reducers: {},
  extraReducers: {},
})

export const postReducer = postSlice.reducer

```

上面的文件是 React 应用程序中的 Redux 切片。切片是一个函数，包含用于修改存储数据的 store 和 reducer 函数。`createSlice` API 被设定为编写 Redux 逻辑的规范。

在`createSlice`中，对存储的同步请求在`reducers`对象中处理，而`extraReducers`处理异步请求，这是我们的主要关注点。

用`createAsyncThunk`创建的异步请求接受三个参数:一个动作类型字符串、一个回调函数(称为`payloadCreator`)和一个选项对象。

将前面的代码块作为一个博客应用程序的 Redux 存储，让我们检查一下`getPosts`:

```
const getPosts = createAsyncThunk(
  'posts/getPosts',
  async (thunkAPI) => {
    const res = await fetch('https://jsonplaceholder.typicode.com/posts').then(
    (data) => data.json()
  )
  return res
})

```

`posts/getPosts`是这种情况下的动作类型字符串。每当从我们的应用程序中的一个组件调度这个函数时，`createAsyncThunk`使用这个字符串作为前缀来生成 promise 生命周期动作类型:

*   待定:`posts/getPosts/pending`
*   已履行:`posts/getPosts/fulfilled`
*   拒绝:`posts/getPosts/rejected`

在第一次调用时，`createAsyncThunk`分派了`posts/getPosts/pending`生命周期动作类型。然后`payloadCreator`执行返回结果或错误。

如果出现错误，`posts/getPosts/rejected`将被调度，`createAsyncThunk`将返回一个被拒绝的承诺，其中包含一个`Error`实例、一条简单的描述性消息，或者返回一个由`thunkAPI.rejectWithValue`函数返回的带有`RejectWithValue`参数的已解决承诺(稍后将详细介绍`thunkAPI`和错误处理)。

如果我们的数据获取成功，那么`posts/getPosts/fulfilled`动作类型将被分派。

`options`参数是一个包含`createAsyncThunk` API 不同配置的对象。查看可用选项的[列表。](https://redux-toolkit.js.org/api/createAsyncThunk#options)

然后可以在`extraReducers`中评估前面提到的三种生命周期活动类型，在这里我们对商店进行我们想要的更改。在这种情况下，让我们用一些数据填充`entities`,并在每个动作类型中适当地设置加载状态:

```
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit'

const initialState = {
  entities: [],
  loading: false,
}

const getPosts = createAsyncThunk(
  'posts/getPosts',
  async (thunkAPI) => {
    const res = await fetch('https://jsonplaceholder.typicode.com/posts').then(
    (data) => data.json()
  )
  return res
})

export const postSlice = createSlice({
  name: 'posts',
  initialState,
  reducers: {},
  extraReducers: {
    [getPosts.pending]: (state) => {
      state.loading = true
    },
    [getPosts.fulfilled]: (state, { payload }) => {
      state.loading = false
      state.entities = payload
    },
    [getPosts.rejected]: (state) => {
      state.loading = false
    },
  },
})

export const postReducer = postSlice.reducer

```

如果您是 Redux Toolkit 的新手，上面的状态逻辑可能对您来说有些陌生。Redux Toolkit 利用了 Immer 库，该库允许开发人员在 reducer 函数中编写可变逻辑。然后，Immer 将你的可变逻辑转换成不可变逻辑。

另外，请注意函数表达式。出于个人偏好，我使用了 map-object 符号来处理请求，主要是因为这种方法看起来更整洁。

处理请求的推荐方法是构建器回调表示法，因为这种方法具有更好的类型脚本支持(因此 IDE 自动完成功能甚至适用于 JavaScript 用户)。

*注意:随着应用程序的增长，您将继续向后端 API 发出更多的异步请求，并依次处理它们的生命周期动作类型。将所有这些逻辑整合到一个文件中会使文件更难阅读。我写了一篇文章，介绍了我在 Redux Toolkit 应用程序中分离逻辑的方法。*

## 组件中的调度操作

通过使用 react-redux 中的`useSelector`和`useDispatch`，我们可以分别从 redux 存储中读取状态和从组件中分派任何动作。

让我们设置一个组件，在它挂载时分派`getPosts`:

```
import { useEffect } from 'react'
import { useDispatch, useSelector } from 'react-redux'
import { getPosts } from '../redux/features/posts/postThunk'

export default function Home() {
  const dispatch = useDispatch()
  const { entities, loading } = useSelector((state) => state.posts)

  useEffect(() => {
    dispatch(getPosts())
  }, [])

  if (loading) return <p>Loading...</p>

  return (
    <div>
      <h2>Blog Posts</h2>
      {entities.map((post) => (
        <p key={post.id}>{post.title}</p>
      ))}
    </div>
  )
}

```

[Redux DevTools 扩展](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en)给出了关于任何生命周期动作类型的调度的实时信息。

![](img/aabefc87f2f12d1653d9e47797973be4.png)

需要注意的是，`payloadCreator`只接受两个参数，其中一个是可能在您的请求中使用的自定义参数，另一个是`thunkAPI`。`thunkAPI`是一个包含所有通常传递给 Redux Thunk 函数的参数的对象，比如`dispatch`和`getState`。看看所有的[可接受参数](https://redux-toolkit.js.org/api/createAsyncThunk#payloadcreator)。

如果您的请求需要多个参数，您可以在调度 reducer 函数时传入一个对象:

```
dispatch(getPosts({ category: 'politics', sortBy: 'name' })

```

## 处理`createAsyncThunk`中的错误

请记住，当您的`payloadCreator`返回一个被拒绝的承诺时，将调度`rejected`动作(用`action.payload`作为`undefined`)。大多数时候，我们希望显示定制的错误消息，而不是在`Error`对象中返回的消息。

通过使用`thunkAPI`，您可以将一个解决的承诺返回给 reducer，它已经将`action.payload`设置为您选择的自定义值。`thunkAPI`使用它的`rejectWithValue`属性来执行这个操作。

假设我们想在博客中添加一篇新文章。我们的`createAsyncThunk`函数看起来像这样:

```
const post = { title: 'lorem', body: 'ipsum' }

const addPost = createAsyncThunk(
  'posts/addPost',
  async (post, { rejectWithValue }) => {
    try {
      const response = await fetch(
        'https://jsonplaceholder.typicode.com/posts',
        {
          method: 'POST',
          body: JSON.stringify(post),
          header: {
            'Content-Type': 'application/json',
          },
        }
      )
      const data = await response.json()
      return data
    } catch (err) {
      // You can choose to use the message attached to err or write a custom error
      return rejectWithValue('Opps there seems to be an error')
    }
  }
)

```

然后评估`extraReducers`中的`posts/addPost/rejected`:

```
extraReducers: {
  [addPost.rejected]: (state, action) => {
    // returns 'Opps there seems to be an error'
    console.log(action.payload) 
  }
}
```

我们已经结束了，德夫斯。到目前为止，我们已经了解了`createAsyncThunk`的基本特性，并了解了它如何与切片函数中的减速器一起工作。API 还有一些更高级的主题，比如取消请求，您可以继续阅读。

## 结论

最后，我想提一下 Redux Toolkit 的 [RTK 查询数据获取 API](https://redux-toolkit.js.org/rtk-query/overview) 。

RTK Query 是专门为 Redux 应用程序构建的数据获取和缓存解决方案，它可以消除编写任何 thunks 或 reducers 来管理数据获取的需要。因此，如果您曾经尝试过 React Query 这样的库，在 Redux 中使用 RTK Query 进行异步逻辑将是明智的，因为语法非常相似。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)