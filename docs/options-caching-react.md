# React - LogRocket 博客中优化缓存的选项

> 原文：<https://blog.logrocket.com/options-caching-react/>

作为创建高性能 web 应用程序最流行的前端库之一，React 遵循基于组件的方法，其中每个组件都有自己的状态和逻辑。

React 面临的最大挑战是避免不必要的渲染，这可能会导致重大的性能问题，尤其是在较大的应用程序中。在本文中，我们将介绍通过不同的缓存方法来优化 React 应用程序性能的几种不同方法。

## 记忆反应钩

记忆化是 React 本身提供的一个特性。正如我们所知，React 每次重新渲染时都会创建新的引用。如果组件有大量的计算，即使输出没有改变，每次重新渲染时都会进行计算。

为了通过避免不必要的负载来保持 CPU 负载最小，React 提供了两个钩子来帮助内存化。钩子遵循一个过程，在这个过程中，结果被缓存在内存中，当我们得到相同的输入时，不需要重新计算就可以返回。在不同输入的情况下，缓存会失效。

### `useMemo()`

`useMemo()`是 React 为记忆化提供的一个钩子，它有助于保持提供给它的相同值的缓存值。它跟踪输入并返回先前执行的结果。

让我们看一个例子。假设我们必须用以下函数将两个巨大的数字相加:

```
const addTwoHugeNumbers=(a,b)=>{
return a+b
}

```

上面写的函数在 CPU 上很重，因此只应在`a`和`b`的值改变时计算。但是，默认情况下，它将在每次重新渲染时运行。

使用`useMemo()`，我们可以存储特定值的结果，这意味着函数将不进行计算，我们将直接获得之前计算的结果:

```
const memoizedValue = useMemo(() => addTwoHugeNumbers(a, b), [a, b])

```

该值存储在`memoizedValue`中。我们已经将依赖数组传递给了`useMemo`，它告诉它何时再次运行。在我们的例子中，当其中一个值改变时，它将运行。

### `UseCallback()`

有了`useCallback()`，我们也获得了记忆的能力，但它以不同的方式工作。`useCallback()`不记忆值，而是记忆提供给它的回调函数。我们来看一个小例子:

```
const increment = (() => {
  setCount(count + 1);
});

```

使用`useCallback()`，上面的函数看起来像下面的代码:

```
const increment = useCallback(() => {
  setCount(count + 1);
}, [count]);

```

`useCallback()`将记忆增量功能，仅在给定的依赖关系改变时运行。它不跟踪输入或函数返回的值。

## 惰性加载反应组件

React 中的延迟加载会在前面呈现必要的组件，而将不重要的组件延迟到后面加载。

特别是在较大的应用程序中，强烈建议使用这种方法来提高性能。在 React 中，我们内置了延迟加载组件的选项。

我们已经创建了一个名为`</Artists>`的组件，我们想让它延迟加载，我们可以这样做:

```
import { lazy } from 'react';

```

首先，我们从 react 导入 lazy 并如下使用它:

```
const Artists = React.lazy(() => import('./Artists'));

function App() {
  return (
    <div>
      <Artists />
    </div>
  );
}

```

### `useRef()`

我们知道，每当我们在组件中使用`useState()`时，当状态改变时，它会导致组件中的重新呈现。为了在不导致重新渲染的情况下跟踪状态，React 引入了`useRef()`钩子。

有些情况下,`useState()`可能不是您应用的正确解决方案。`useRef()`非常适合我们需要一个不会导致重新呈现的状态，并且对组件呈现的可见信息没有贡献的情况。例如，可以使用它来计算渲染次数:

```
function App() {
    const [foo, setFoo] = React.useState(false)
    const counter = React.useRef(0)
    console.log(counter.current++)
    return (
      <button onClick={() => setFoo(f => !f)} > Click </button>
     )
}

ReactDOM.render(<React.StrictMode><App /></React.StrictMode>, document.getElementById('mydiv'))

```

在上面的代码中，我们有一个简单的 toggler 来重新呈现组件。`counter`是一个保持其值的可变 ref。我们可以用`useState()`做同样的事情，但是它会导致每个切换出现两次渲染。

## 冗余缓存选择器

选择器只是用来从更大的数据池中选择数据的函数。在 React 中，选择器被广泛用于从 Redux 存储中获取值。选择器非常有用和强大，但是它们也有自己的缺点。

在 React Redux 中，我们使用了`useSelector()`钩子来从存储中获取状态。`useSelector()`的问题是每次组件渲染时它都会运行。`useSelector()`在某些情况下可能是理想的，但大多数时候，选择器返回的数据不会改变，这使得计算变得不必要。

让我们看一个例子:

```
import React, {useEffect,useState} from 'react'
import {useSelector, useDispatch} from 'react-redux'
import {getPosts} from './postActions'

export const List=()=>{
  Const [toggle, setToggle]=useState(false)
  const myPosts=useSelector(state=>state.posts)
  const dispatch=useDispatch()

  return(
    <div>
    {myPosts.map(post=><p>{posts}<p/>)}
    <button type="button" onClick={()=>{setToggle(!toggle)}} >Click Me!</button>
    <div/>
  )
}

```

在上面的代码中，我们更改了切换状态，每次我们这样做时，组件都会呈现出来。即使我们的 Redux 商店中的帖子没有变化，挂钩也会运行。

为了解决这个问题，我们将缓存选择器函数的结果。尽管没有内置的 React 解决方案，但我们有许多第三方库，允许我们创建缓存选择器。让我们使用 Reselect，这是著名的缓存选择器的解决方案。

### 重新选

Reselect 是一个用于创建记忆选择器的流行库。您可以使用以下命令将它安装到您的项目中:

```
yarn add reselect

```

我们可以如下使用重选:

```
import { createSelector } from 'reselect' 
import React, {useEffect,useState} from 'react'
import {useSelector, useDispatch} from 'react-redux'
import {getPosts} from './postActions'

export const List=()=>{
  Const [toggle, setToggle]=useState(false)
  const myPosts = createSelector(state=>state.posts)
  const dispatch=useDispatch()

  return(
  <div>
  {myPosts.map(post=><p>{posts}<p/>)}
  <button type="button" onClick={()=>{setToggle(!toggle)}} >Click Me!</button>
  <div/>
  )
}

```

在上面的代码中，我们从 Reselect 导入了`createSelector`，它接受一个选择器并返回它的一个记忆版本。有了记忆化版本，组件将不会计算选择器的值，即使在数千次重新渲染之后，除非`postReducer`的值改变。Reselect 的`createSelector`被证明是解决大型应用中[性能问题的优秀解决方案。](https://blog.logrocket.com/react-re-reselect-better-memoization-cache-management/)

## 用 React 查询优化 API 调用

React 以自己的方式处理异步操作，这对开发人员来说有时是个问题。异步操作的通常模式是在`useEffect`钩子中获取服务器数据，该钩子在每次渲染时运行，并且每次都获取新数据，即使服务器上没有新数据。

另一方面，React Query 缓存数据并在进行调用之前首先返回，但如果服务器返回的新数据与之前的数据相同，React Query 不会重新呈现组件。我们可以如下使用 React 查询:

```
import React from 'react'
import {useQuery} from 'react-query'
import axios from 'axios'

async function fetchPosts(){
    const {data} = await axios.get('https://jsonplaceholder.typicode.com/posts')    
    return data
}

function Posts(){
    const {data, error, isError, isLoading } = useQuery('posts', fetchPosts) 
    // first argument is a string to cache and track the query result
    if(isLoading){
        return <div>Loading...</div>
    }
    if(isError){
        return <div>Error! {error.message}</div>
    }

    return(
        <div className='container'>
        <h1>Posts</h1>
        {
            data.map((post, index) => {
                return <li key={index}>{post.title}</li>
            })
        }

        </div>
    )
}

export default Posts

```

## 反应碎片

如果您是 React 开发人员，您可能会遇到一个错误，要求用父 div 包装组件。如果组件中不需要额外的 div，那么添加它就没有意义。例如，如果 React 应用程序中有 1000 个组件，那么就会有 1000 个额外的 div，这对 DOM 来说是很大的负担。为了避免这种情况，React 为您提供了使用片段的选项:

```
const Message = () => {
  return (
    <React.Fragment>
      <p>Hello<p/>
      <p>I have message for you<p/>
    </React.Fragment>
  );
};

```

下面的代码片段与上面的代码完全相同，使用`<>`作为`React.Fragment`的快捷方式:

```
const Message = () => {
  return (
    <>
      <p>Hello<p/>
      <p>I have message for you<p/>
    </>
  );
};

```

无论使用哪种方法，都可以避免添加额外的`<div>`，从而减少 DOM 标记，提高渲染性能，并减少内存开销。

## 反应虚拟列表

经常，我们需要在浏览器上呈现大的列表；对于浏览器来说，这样做的工作量很大，因为它必须创建新的节点，并将它们都绘制在屏幕上。

为了使 React 中的过程高效，我们可以选择使用虚拟列表。虚拟列表只根据需要呈现少量项目，当用户动态滚动项目时简单地替换它们。

呈现比更改 DOM 更快，因此您可以使用虚拟列表快速呈现数千个列表项。React-virtualized 是一个优秀的库，拥有用于呈现虚拟列表的组件。

## 功能组件

React 从基类组件开始，但是，现在推荐使用功能组件，因为它们的轻量级性质。功能组件基本上是创建速度更快的功能，它们更容易缩小，从而减小了包的大小。

## 结论

在本教程中，我们讨论了在 React 应用程序中优化缓存管理的几种不同的解决方案，如记忆、缓存选择器、延迟加载、React 片段、虚拟列表和功能组件。这些方法中的每一种都可以通过减少不必要的组件渲染数量、减少开销和提高速度来改进您的应用程序。

正确的解决方案将取决于您个人项目的需求，但希望本文能帮助您了解可用的选项。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)