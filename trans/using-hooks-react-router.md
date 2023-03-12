# 在 React 路由器上使用钩子

> 原文：<https://blog.logrocket.com/using-hooks-react-router/>

***编者按**:本文最后一次更新于 2022 年 11 月 16 日[包括 React 路由器 v6](https://blog.logrocket.com/react-router-v6-future-reach-router/) 中所做的更改，包括删除了已弃用的`useHistory`钩子。*

React 挂钩是随着 React v16.8.0 的发布而引入的，令人兴奋不已。与类组件相比，使用钩子，开发人员可以用更少的样板代码编写更干净的组件。许多流行的 React 包都增加了对钩子的支持，这样开发者就可以在功能组件中利用他们的 API。

React 路由器是 React 应用程序的首选路由解决方案，在其 5.0 版本中增加了钩子支持。这些 [React 路由器钩子](https://reactrouter.com/web/api/Hooks)为开发者提供了处理路由器状态的新方法。在本教程中，你将[学习如何在 React Router](https://blog.logrocket.com/how-react-hooks-can-replace-react-router/) 中使用钩子，并最小化组件中的代码行数。我们开始吧！

## 钩子如何与 React 路由器一起工作？

为了演示钩子如何工作，我们将创建一个 React 项目并设置页面。要创建新的 React 应用程序，请运行以下命令:

```
npx create-react-app router-hooks-demo

```

`router-hooks-demo`是 app 的名字，不过你想怎么命名都行。接下来，添加`[react-router-dom](https://www.npmjs.com/package/react-router-dom)`包:

```
npm i react-router-dom --save

```

从`react-router-dom`包中导入`BrowserRouter`、`Link`、`Route`和`Routes`组件。我们将使用这些组件在 React 应用程序中配置客户端路由:

```
import { BrowserRouter, Link, Route, Routes } from "react-router-dom";

```

对于这个演示，您将只有两个路线或页面，即`Home`页面和`User`页面。我们将主要致力于`User`页面:

```
const User = () => {
  return <div>This is the user page</div>;
};

const Home = () => {
  return <div>This is the home page</div>;
};

```

在`App`组件中，创建一个导航栏，并使用 React Router 的`Link`组件向`Home`和`User`页面添加超链接。在网页上，`Link`组件被渲染成一个`<a>`标签:

```
<nav>
  <div>
    <Link to="/">Home</Link>
  </div>
  <div>
    <Link to="/user/:id">User</Link>
  </div>
</nav>

```

接下来，添加`Routes`和`Route`组件，然后将所有内容包装在`BrowserRouter`组件中:

```
<BrowserRouter>
  <nav>
    <div>
      <Link to="/">Home</Link>
    </div>
    <div>
      <Link to="/user/:id">User</Link>
    </div>
  </nav>
  <Switch>
    <Route path="/" element={<Home />} />
    <Route path="/user/:id" element={<User />} />
  </Switch>
</BrowserRouter>

```

`[BrowserRouter](https://v5.reactrouter.com/web/api/BrowserRouter)`组件启用客户端路由特性，并使用[浏览器`history` API](https://v5.reactrouter.com/web/api/history) 处理路由逻辑。当路由路径与活动 URL 匹配时,`Route`组件呈现页面 UI。`Routes`组件的工作是理解其子`Route`元素，并在用户导航到不同路线时决定呈现哪个元素。

最终的`App.js`文件应该如下所示:

```
import React from "react";
import { BrowserRouter, Link, Route, Routes } from "react-router-dom";

const User = () => {
  return <div>This is the user page</div>;
};

const Home = () => {
  return <div>This is the home page</div>;
};

export default function App() {
  return (
    <div className="App">
      <BrowserRouter>
        <nav>
          <div>
            <Link to="/">Home</Link>
          </div>
          <div>
            <Link to="/user/:id">User</Link>
          </div>
        </nav>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/user/:id" element={<User />} />
        </Routes>
      </BrowserRouter>
    </div>
  );
}

```

## React 路由器钩子为什么有用？

假设您需要访问一个`page`组件中 URL 的当前路径名。在挂钩之前，你必须将`page`组件传递给`Route`组件的`component`道具。然后，`Route`组件将注入[路由属性](https://reactrouter.com/web/api/Route/route-props)、`match`、`location`和`history`。

虽然这种方法可行，但是组件变得难以阅读，并且很难理解在维护项目时是如何注入道具的。React 路由器作者添加了钩子支持，这样`page`组件就可以访问`history`、`location`和`match`对象，而不必将`page`组件作为`Route`组件中的`component`道具传递:

```
// Route with component prop
<Route path="/user/:id" component={User} />;

// User component
const User = (props) => {
  const location = props.location;

  console.log(location.pathname);

  return <div>This is the user page</div>;
};

```

## React 路由器挂钩

既然您已经理解了我们为什么要为路由添加钩子，那么让我们来看看钩子是如何工作的。

### `useParams`

`useParams`钩子返回一个对象，该对象包含 URL 中存在的任何参数的键值对。例如，假设您有一个`User`页面组件，它接受一个`id`作为 URL 中的参数。您可以使用`useParams`挂钩访问参数:

```
import { useParams } from "react-router-dom";

<Route path="/user/:id" element={<User />} />

const User = () => {
  const params = useParams();

  console.log(params);

  return (
    // ...
  )
}

```

如果您要将`234`作为用户 URL 中的 ID 传递给`/user/234`，那么`useParams()`钩子将返回以下对象:

```
{
  id: 234,
}

```

您可以在页面上显示`id`参数，或者使用它从服务器获取用户信息:

```
import { useParams } from "react-router-dom";

// /user/:id
const User = () => {
  const params = useParams();

  return (
    <div>
      <p>This is the user page</p>
      <p>current user Id - {params.id}</p>
    </div>
  );
};

```

### `useNavigate`

在 React 路由器 v6 中，`useHistory`钩子已经停产，[提供了`useNavigate`](https://reactrouter.com/en/main/upgrading/v5#use-usenavigate-instead-of-usehistory) 作为它的替代品。从`useHistory`切换到`useNavigate`的主要原因是为了[提供与 React suspension](https://blog.logrocket.com/react-suspense-data-fetching/)更好的兼容性。

`useNavigate`钩子返回一个函数，让您处理路线更改并以编程方式导航:

```
import { useNavigate, useParams } from "react-router-dom";

const User = () => {
  let navigate = useNavigate();
  const params = useParams();

  const handleBack = () => {
    navigate(-1);
  };

  const handleNavigation = () => {
    navigate("/user/5");
  };

  return (
    <div>
      <div>This is the user page</div>
      <div>current user Id - {params.id}</div>
      <div>
        <button onClick={handleBack}>Go Back</button>
      </div>
      <div>
        <button onClick={handleNavigation}>Go To Different User</button>
      </div>
    </div>
  );
};

```

在上面的代码块中，我们向`User`页面组件添加了两个按钮。一个按钮将用户带到上一页，另一个按钮将用户导航到不同的页面。`navigate(-1)`方法相当于点击后退按钮。它将用户导航到上一页，并返回历史堆栈中的一个条目。

### `useLocation`

`useLocation`钩子允许您访问代表活动 URL 的`location`对象。每当用户导航到一个新的 URL 时，`location`对象的值就会改变。当您必须在 URL 改变时触发任何事件时,`useLocation`钩子会很方便。

考虑到您必须跟踪用户个人资料页面上的视图。您可以使用 React 附带的`useEffect`钩子来检测`location`对象中的变化:

```
import { useNavigate, useParams, useLocation } from "react-router-dom";

const User = () => {
  const navigate = useNavigate();
  const params = useParams();
  const location = useLocation();

  useEffect(() => {
    console.log(location.pathname);
    // Send request to your server to increment page view count
  }, [location]);

  const handleBack = () => {
    navigate(-1);
  };

  const handleNavigation = () => {
    navigate("/user/5");
  };

  return (
    <div>
      <div>This is the user page</div>
      <div>current user Id - {params.id}</div>
      <div>
        <button onClick={handleBack}>Go Back</button>
      </div>
      <div>
        <button onClick={handleNavigation}>Go To Different User</button>
      </div>
    </div>
  );
};

```

### `useMatch`

React Router v6 已经停止使用`useRouteMatch`挂钩，[提供了`useMatch`挂钩](https://reactrouter.com/en/main/upgrading/v5#replace-useroutematch-with-usematch)作为其替代品。`useMatch`返回与当前路线相关的路线匹配数据。它将活动 URL 与给定的路径进行匹配，类似于`Route`组件的工作方式。

`useMatch`钩子解决什么问题？假设您有一条显示多篇博文的`/blog`路线和一条显示单篇博文详细信息的`blog/:id`路线:

```
const App = () => {
  [const posts, setPosts] = useStatte([....])

  return (  
    <Routes>
      <Route path="/blog/:id" element={<Post post={post} />} />        
      <Route path="/blog" element={<Posts posts={posts} />} />   
      <Route path="/" element={<Home />} />      
    </Routes>   
  )
}  

```

以前，您必须将所有的帖子传递给`Post`组件，并使用`useParams`钩子过滤掉特定的帖子:

```
const Post = ({ posts }) => { 
  const id = useParams().id
  const post = posts.find(post => post.id === Number(id))

  return (
    <div>
      <span>{post.user}</span>
      <p>{post.content}</p>
    </div>
  )
}

```

虽然这种方法可行，但是我们可以利用`useMatch`钩子来创建一个更干净的实现。`Post`组件只接收它应该显示的数据，而不是文章的数组。

使用`app`组件中的`useMatch`挂钩:

```
import { useMatch } from "react-router-dom"

const App = () => {
  [const posts, setPosts] = useStatte([....])

  const match = useMatch('/blog/:id')  
  const post = match
  ? posts.find(post => post.id === Number(match.params.id))    
  : null

  return (  
    <Routes>
      <Route path="/blog/:id" element={<Post post={post} />} />        
      <Route path="/blog" element={<Posts posts={posts} />} />   
      <Route path="/" element={<Home />} />      
    </Routes>   
  )
}  

```

通过这种实现，每当 URL 改变时，执行下面的命令来检查当前路由是否与期望的 URL 匹配:

```
const match = useMatch('/posts/:id')

```

如果 URL 匹配，`match`变量将返回一个包含匹配 URL 数据的对象。然后，您可以从该对象访问`id`参数，用它过滤出正确的帖子数据，并将其传递给`Post`组件:

```
const Post = ({ post }) => { 
  return (
    <div>
      <span>{post.user}</span>
      <p>{post.content}</p>
    </div>
  )
}

```

### `useRoutes`

React 路由器提供了两种在 React 应用程序中定义路由的方法。第一个也是最流行的是使用`<Routes>`和`<Route>`组件。第二种方法包括使用 [`useRoutes`钩子](https://reactrouter.com/en/main/hooks/use-routes)和普通的 JavaScript 对象来声明您的路线。

要查看`useRoutes`挂钩是如何工作的，首先创建一个`routes.js`文件，您将在其中创建路线:

```
import { useRoutes } from "react-router-dom";
import Home from "./pages/Home";
import Post from "./pages/Post";
import Posts from "./pages/Posts";
import User from "./pages/User";

export default function Router() {
  let element = useRoutes([
    { path: "/home", element: <Home /> },
    { path: "/posts", element: <Post /> },
    { path: "/post:id", element: <Posts /> },
    { path: "user", element: <User /> },
  ]);

  return element;
}

```

然后，将路线导入到您的`App.js`文件中:

```
import { BrowserRouter } from "react-router-dom";
import Router from "./routes";

export default function App() {
  return (
    <BrowserRouter>
      <div className="App">
        <h1>useRoutes Example</h1>
        <Router />
      </div>
    </BrowserRouter>
  );
}

```

使用一种方法定义路由优于另一种方法没有任何好处。这可以归结为你和你的团队的偏好和模式。

## 结论

钩子是 React 生态系统的一个很好的补充。在本文中，我们回顾了一些用钩子处理路由的代码，使用了`useParams`、`useNavigate`、`useLocation`、`useMatch`和`useRoutes`钩子。现在，您已经准备好充分利用他们所提供的一切。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)