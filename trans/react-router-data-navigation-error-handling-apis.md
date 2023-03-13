# 探索 React 路由器的数据、导航和错误处理 API

> 原文：<https://blog.logrocket.com/react-router-data-navigation-error-handling-apis/>

React Router 的 v6.4 是 2022 年早些时候的一个小版本，为`react-router-dom`包引入了一些突破性的变化。新添加的一组 API 和数据获取模式是从 [Remix](remix.run) 获得的。因为这两个库是由同一作者编写的， [React router v6.4](https://reactrouter.com/en/main/start/overview) 提供了直观的方法来处理数据获取，并对单页面应用程序中的错误管理进行更多的控制。要从 React 路由器 v5 迁移到 v6，请查看本[指南](https://blog.logrocket.com/migrating-react-router-v6-guide/)。

在本文中，您将看到 React Router v6.4 中引入的新 API 和模式，它们简化了基于 React 的应用程序的路由。

*向前跳转:*

## React 路由器 6.4 中的数据获取

让我们从一个非常简单的数据获取逻辑开始。您可能熟悉下面给出的模式。毕竟，这就是我们在 React 组件中从 API 获取数据的方式。

您只需创建一个调用 API 的`fetch`函数(在本例中为`getBlog`)并将它传递给`useEffect`钩子。每当来自`URL`的 ID 改变时，该钩子将重新获取，反映你正在写的博客文章。如果您需要复习使用带挂钩的 React 路由器的[知识，请查看本指南。](https://blog.logrocket.com/using-hooks-react-router/)

最后，您可以借助`useState`钩子手动处理加载和错误状态:

```
import { useState, useEffect } from 'react';
import { useParams } from 'react-router-dom';
import { getBlog } from './utils/api'

function BlogDetail(){
    const [isLoading, setIsLoading] = useState(false);
    const [isError, setError] = useState(null);
    const [blog, setBlog] = useState(null);

// fetching id from URL dynamically //
const params = useParams();
const id = params.id;

async function getBlogs(){
    setIsLoading(true)
    try {
        const blog = await getBlog(id);
        setBlog(blog)
    }
    catch(error) {
        setError(error.message) 
    }
    finally{
        setIsLoading(false)
    }
}

useEffect(() => {
  getBlogs()
}, [id])

return (
    <>
        {isLoading && <p>Loading...</p>}
        {isError && !isLoading && <p>Oops! An Error has Occurred, Try Again</p>}
        <BlogPost heading={blog.heading} detail={blog.detail} />
    <>
)
}

```

## 重构您的 React 组件

虽然这种模式受到鼓励并且运行良好，但它有一些缺点，React Router v6.4 旨在解决这些缺点。首先，数据获取对于单个请求来说需要大量代码。乍一看，处理所有这些错误、加载器等的状态值看起来相当标准。

然而，随着应用程序的增长，它会对代码库造成严重破坏。像这样的 React 应用程序的另一个大缺点是获取本身。`useEffect()`钩子只在组件被渲染后运行。所以，换句话说，只有在屏幕上打印出基本的 UI 之后，才能开始获取。在这段时间内，你只会看到装载机，这不是一个好的 UX。

React Router 鼓励移除所有这些代码块以获取组件来解决这个问题。取而代之的是，使用在 [Remix](remix.run) 和 React router v6.4 中引入的加载器模式。

您可以通过移除所有加载和错误状态以及`return`方法中的条件逻辑来开始重构:

```
function BlogDetail(){
    const [blog, setBlog] = useState(null);
    const params = useParams();
    const id = params.id;
return (
    ...
    )
}

export function BlogDetailLoader(id){
    return getBlog(id)
}

```

新的加载器和数据 API 现在将处理组件中的数据获取。注意上面重构组件中的`BlogDetailLoader`函数。现在可以使用它向`BlogDetail` React 组件公开由`getBlog`(即调用 API)接收的数据。

现在，为了让您的`BlogDetailLoader`函数工作，传递一个新的`loader` API，您已经在其中定义了所有的路线(可能在`App.js`文件中):

```
import { BlogDetailLoader, BlogDetail } from ''../BlogDetail";

<Route path="/blog" element={<GenericBlogLayout />}>
    <Router path=":id" element={<BlogDetail />}  
        loader={BlogDetailLoader} />
</Route>

```

通过使用 React Router v6.4 中的`loader` API，您的`BlogDetail`组件现在可以轻松访问由`BlogDetailLoader`返回的数据:

```
import { useDataLoader } from 'react-router-dom';

function BlogDetail(){

// data variable has now access to whatever the loader has returned (in this case getBlog) //
    const data = useDataLoader(); 
    const params = useParams();
    const id = params.id;
return (
    <>
        <BlogPost heading={data.heading} detail={data.detail} />
    <>
    )
}

export function BlogDetailLoader(id){
    return getBlog(id)
}

```

您可以通过将来自`params`的`ID`传递给 React Router 公开给`loader`函数来进一步改进这一点，如下所示:

```
export function BlogDetailLoader({params}){
    return getBlog(params.id)
}

```

现在，您已经完全重构了 React 组件，并通过使用新的 React Router v6.4 `useDataLoader` API 清理了所有不必要的`useEffect()`挂钩和显式`isLoading`状态。

这种模式消除了加载状态的使用，因为页面只需要在从 API 获取数据后加载。你可以把它想象成那些服务器端渲染的页面。这创建了一个很好的 UX，没有烦人的微调器和加载器等待数据加载。

但是等等！那`isError`状态呢？无论请求是否成功发出，我们仍然需要显式地处理 React 组件中的错误状态。

## 新导航 API

值得注意的是，这些新的 API 不是向后兼容的，并且需要在根级别定义路由的方式上稍作改变。

首先，React Router v6.4 建议将`BrowserRouter`替换为`RouterProvider`，这只是一个以`router`为道具的自闭组件:

```
import { RouterProvider, createBrowserRouter, } from 'react-router-dom';

const router = createBrowserRouter()

function App(){
    return(
        <RouterProvider router={router} />
        // move all routes defined here, and pass it             `createBrowserRouter`
    )
}

```

`createBrowserRouter()`可以简单地接受一个对象数组，你可以像这样传递你的路线和路径:

```
const router = createBrowserRouter([
    { element={ <Welcome /> }, path="/" },
    { element={ <BlogHome /> }, path="/blog" },
    { element={ <About /> }, path="/about" },
]);

// pass this `router` variable to the `RouterProvider` API //
<RouterProvider router={router} />

```

但是，您希望传递您为页面定义的复杂路线，包括动态路线(如带有`ID`的路线)，

在这种情况下，您可以将另一个函数`createRoutesFromElements`传递给`createBrowserRouter` API，如下所示:

```
const router = createBrowserRouter(createRoutesFromElements(
    // pass all the Routes as it was defined before
));

// later, passing the `router` to the provider as it was
<RouterProvider router={router} />

```

### 对`Outlet` API 的更改

虽然您已经设置了所有的路由并将它们迁移到了 v6.4，但是请记住，在 v6.4 中有一个重要的东西发生了变化，那就是`Outlet` API。通常，您可能已经使用了某种共享根布局组件，该组件通过使用 React 的`children`属性来使用所有子路由。

React Router v6.4 使得使用`Outlet` API 定义根布局变得更加容易。你可以在`Route`(而不是 6.4 之前的`Routes`)下包装你的整个路线，并通过一个`<SharedRootLayout />`组件:

```
const router = createBrowserRouter(createRoutesFromElements(
    <Route path="/" element={<SharedRootLayout />}>
        <Route path="/blog" element={<GenericBlogLayout />}>
        <Router path=":id" element={<BlogDetail />}  
            loader={BlogDetailLoader} />
        </Route>
    </Route>
));

```

在`SharedRootLayout`组件中，您可以在下面的代码中使用新的 outlet API:

```
import { Outlet } from 'react-router-dom';

function SharedRootLayout(){
    return(
        <>
            <Navbar />
            // replacing {children} with <Outlet />, this Outlet API has //now access to all the `children` Routes under the Root `<Route />` //
            <Outlet /> 
        </>
    )
}

```

因此，这些 API`createBrowserRouter`、`createRoutesFromElements`和`<Outlet />`完全改变了我们在 React Router v6.4 中定义路由的方式。这些改变使 React Router v6.4 在执行复杂路由时更加健壮。

## 处理错误状态

到目前为止，您已经将加载器状态、路由等重构到了最新的 v6.4，但是错误状态呢？如果网络请求失败，您仍然需要某种检查。在这种情况下，下面的代码不再相关，因为您已经脱离了`useEffect`挂钩和这些状态值的手动处理:

```
{isError && <p>Oops! an Error has Occured</p>}

```

幸运的是，React Router v6.4 提供了错误处理 API，使得处理这样的情况变得轻而易举。您需要在`Route`中添加一个`errorElement`属性，以便在出错时自动处理错误状态:

```
<Router path=":id" element={<BlogDetail />} loader={BlogDetailLoader} 
    errorElement={<p>Oops! Something Went Wrong</p>}
/>
// or even pass an Error page component to the `errorElement` prop
<Router path=":id" element={<BlogDetail />} loader={BlogDetailLoader} 
    errorElement={<404Page />}
/>

```

React Router v6.4 还提供了在根级别`Route`添加`errorElement`的灵活性，与`<SharedRootLayout />`组件相同。

如果您需要访问错误消息并将其呈现在 UI 页面上，您可以使用错误挂钩`useRouteError()`来获取实际原因或错误消息，并将其记录到屏幕上:

```
import { useRouteError } from 'react-router-dom';

function BlogDetail(){
    const error = useRouteError()
    return(
        //rest of the UI
        <div>Error : {error.status}, Sorry {error.message}</div>
    )
}

```

如您所见，它的模式类似于 6.4 版之前的 React 路由器，但是允许更多的灵活性和控制。

## 结论

在本文中，您看到 React Router v6.4 被证明是 React Router 团队最重要的版本之一。它引入了一堆直接来自 Remix 的新 API 和想法。虽然所有这些新的实践一开始可能看起来很陌生，但毫无疑问，它极大地改善了开发人员和最终用户的体验。React Router v6.4 提供的远不止这些。要了解更多信息，请查看他们的官方发布文档。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)