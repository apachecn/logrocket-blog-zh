# 带有 React 路由器挂钩的清洁组件

> 原文：<https://blog.logrocket.com/react-router-hooks-cleaner-components/>

***编者按*** *:本帖最后更新于 2022 年 3 月 7 日，以反映已经过时的路线渲染方法的信息。*

React Router 是一个轻量级的库，允许您管理和处理 React 应用程序的路由。当使用 React 和 React Native 构建单页面应用程序时，React Router 就派上了用场。

自从引入功能组件以来，React 已经进步了很多，比如通过钩子的引入。与普通的基于类的方法不同，钩子使得包能够很好地与功能组件一起工作，许多包正在朝这个方向迁移。

结果，我们看到 React 路由器挂钩。在本文中，我们将回顾三个 [React 挂钩，它们将使 React 路由器](https://blog.logrocket.com/using-hooks-with-react-router/)的工作更加容易。我们开始吧！

### 目录

## `useParams`钩子

`useParams`钩子将从应用程序 URL 返回一个设置为动态的键/值对对象。在复杂的应用程序中，有许多动态导航链接是很常见的。例如，您可能有一个`/post/:id` URL，它也启动一个到应用程序后端的获取过程。在这种情况下，最常见的 React 路由器模式是使用组件属性:

```
export default function App() {
  return (
    <Router>
      <div>
        <nav>
          <ul>
            <li>
              <Link to="/">Home</Link>
            </li>
            <li>
              <Link to="/post/hello-world">First Post</Link>
            </li>
          </ul>
        </nav>
        <Routes>
          <Route path="/post/:slug" element={<Post />} />
          <Route path="/">
            <Home />
          </Route>
        </Routes>
      </div>
    </Router>
  );
}

```

通过将`Post`组件传递给`/post/:numberRoute`组件，您可以从 React Router 传递给`Post`组件的`match`属性中提取`params`对象:

```
// Old way to fetch parameters
function Post({ match }) {
  let params = match.params;
  return (
    <div>
      In React Router v4, you get parameters from the props. Current parameter
      is <strong>{params.slug}</strong>
    </div>
  );
}

```

虽然上面的方法是可行的，但是如果你有一个包含许多动态路由的大型应用程序，这就相当麻烦了。你必须跟踪哪些`Route`组件需要组件道具，哪些不需要。此外，因为`match`对象是从`Route`传递到渲染组件的，所以您需要将动态路径传递给 DOM 树中更下面的组件。

在这种情况下，`useParams`钩子确实会发光。这是一个简洁的助手函数，它为您提供了当前路线的参数，因此您不必使用 component props 模式:

```
<Switch>
  <Route path="/post/:slug" element={<Post />} />
  <Route path="/users/:id/:hash" element={<Users />} />
  <Route path="/" element={<Home />} />
</Switch>

function Users() {
  let params = useParams();
  return (
    <div>
      In React Router v5, You can use hooks to get parameters.
      <br />
      Current id parameter is <strong>{params.id}</strong>
      <br />
      Current hash parameter is <strong>{params.hash}</strong>
    </div>
  );
}

```

如果`Users`的子组件需要访问参数，您也可以简单地在那里调用`useParams()`。

## `useLocation`钩子

在 React Router v4.x 中，就像获取参数一样，您必须使用 component props 模式来访问一个`location`对象:

```
<Route path="/post/:number" component={Post} />

function Post(props) {
  return (
    <div>
      In React Router v4, you get the location object from props. <br />
      Current pathname: <strong>{props.location.pathname}</strong>
    </div>
  );
}

```

但是，[用 React 路由器 v6.x，可以调用`useLocation`钩子](https://blog.logrocket.com/react-router-v6-future-reach-router/)从 React 路由器获取`location`对象:

```
<Route path="/users/:id/:password" element={<Users />} />

// new way to fetch location with hooks
function Users() {
  let location = useLocation();
  return (
    <div>
      In React Router v5, You can use hooks to get location object.
      <br />
      Current pathname: <strong>{location.pathname}</strong>
    </div>
  );
}

```

## `useNavigate`钩子

从上一节我们又少了一个使用组件道具的理由。但是你不是还需要使用组件或者渲染模式来获取`history`对象吗？

`history`对象是你需要使用组件或渲染道具模式的最后一个原因:

```
<Route path="/post/:slug" component={Post} />

// Old way to fetch history
function Post(props) {
  return (
    <div>
      In React Router v4, you get the history object from props. <br />
      <button type="button" onClick={() => props.history.goBack()}>
        Go back
      </button>
    </div>
  );
}

```

使用`useNavigate`钩子，您可以获得相同的历史对象，而不需要`Route`组件来传递它:

```
<Route path="/users/:id/:hash" element={<Users />}>

// new way to fetch history with hooks
function Users() {
  let navigate = useNavigate();
  return (
    <div>
      In React Router v5, You can use hooks to get navigate function to move around in the app.
      <br />
      <button type="button" onClick={() => navigate(-1)}>
        Go back
      </button>
    </div>
  );
}

```

React 路由器 v6 不提供`useHistory`钩子。相反，我们用`useNavigate`钩子导航。在上面的例子中，我们使用`navigate(-1)`返回到上一页。`-1`确定您想要返回的页数。例如，`-2`会倒退两页，等等。

## 用 React 路由器钩子重构你的组件

[React 路由器团队利用了钩子](https://blog.logrocket.com/migrating-react-router-v6-complete-guide/)的力量，实现了跨组件共享逻辑，而不需要从树的顶端向下传递。如果你想用这些新钩子重构你的组件，你可以从更新使用`match`、`location`或`history`对象的组件开始:

```
// before
function userComponent({ match, location, history }) {
  let { slug } = match.params
  // ...
}

// after
function userComponent() {
  let { slug } = useParams()
  let location = useLocation()
  // ...
}

```

之后，您可以更新您的`navigation`组件中可能有的奇怪的`<Route>`组件:

```
// before
<Switch>
  <Route path="/user/:id" component={userComponent} />
</Switch>

// after
<Switch>
  <Route path="/user/:id" element={<userComponent />}>
</Switch>

```

如何看待 React 路由器挂钩？如果你有任何问题，请在下面留下评论。编码快乐！

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