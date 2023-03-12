# 迁移到 React 路由器 v6:完整指南

> 原文：<https://blog.logrocket.com/migrating-react-router-v6-guide/>

***编者按**:这份 React 路由器迁移指南最后一次更新是在 2022 年 10 月 25 日，包括了关于`useNavigate`与`useHistory`的信息，对`NavLink`的更改，以及更多关于 React 路由器 v6 的信息。*

当我们维护 React 应用程序时，我们需要通过改变现有的代码库来升级依赖库。例如，将 React Rounder v5 依赖项升级到 v6 需要在现有代码库中逐步进行更改。从 React Router v5 过渡到 v6 可能具有挑战性，因为一些变化改变了 React Router v5 的工作方式。此外还引入了几个新功能，所以即使过渡略嫌烦，也建议升级到 v6。

要从 React Router v5 升级到 v6，您要么需要创建一个新项目，要么使用 npm 升级现有项目。React 路由器 v6 也大量使用 [React 挂钩](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/)，要求 React v16.8 以上。在本文中，我们将研究 React 路由器 v5 的问题、变化、如何升级到 v6，以及此次升级带来的好处。要跟上，你应该熟悉[反应路由器](https://blog.logrocket.com/react-router-v6/)。

*向前跳转:*

## React 路由器 v5 的问题

React 路由器 v5 接近完美，但仍有一些缺陷。v5 库在路由算法和路由配置默认值(JSX 元件属性默认值)方面存在一些问题。此外，v5 附带了一些不一致的、有些复杂的 API，影响了开发人员的工作效率。此外，与它作为 React 库提供的相比，v5 的包大小更重。这里详细解释了 React Router v5 中每个开发人员面临的主要问题:

### 路径匹配问题

`Routes`查询具有最相似命名而不是精确命名的路由路径。例如，如果浏览器路由是`/dashboard/test`或`/dashboard/testagain`等，仍会调用路由名称`/dashboard`。，这是 app 开发者所没有预料到的。开发者必须明确指定道具`exact`来严格查询路线名称。

此外，定义具有相同路径和可选参数的路由需要特殊的安排。例如，当定义路线`/games`和另一条同名路线时，需要该参数。因此，开发人员需要安排定义，所以首先是带参数的路线。否则，由于 v5 的路由算法中的问题，路由将不会按预期工作。

请看下面的例子，它展示了 v5 的路由算法中的排序问题:

正确的做法是:在这里，`/games`将呈现`Games`组件，而不是`SelectedGame`，因为它没有参数`id`。而`/games/:id`将只渲染`SelectedGame`组件:

```
<Router>
  <Route path="/games/:id" component={SelectedGame} />
  <Route path="/games" component={Games} />
</Router>

```

不正确的方式:在这里，`/games`或`/games/:id`将呈现`Games`组件:

```
<Router>
  <Route path="/games" component={Games} />
  <Route path="/games/:id" component={SelectedGame} />
</Router>

```

上面的代码片段说明了根据 React Router v5 对通过路径或参数相关的路由进行排序的正确和错误方式。第一个例子是最合适的顺序，而第二个例子只允许路线`/games`在任何带有参数的情况下进行渲染。

根据 v5 的观点，第二种方法是不正确的，开发人员需要使用`exact` prop 或重新排序(如第一个代码片段中所示)来修复它。但是，v5 路由排序算法可以智能地识别最合适的路由，而无需显式排序或使用`exact`。

为了开发嵌套路由，开发人员不得不用`useRouteMatch`钩子、`Route`和`Switch`组件编写更多的代码。对于嵌套路由，开发人员必须找到整个路由，因为在 v5:

```
// ----
<Switch>
  <Route path="/dashboard" component={Dashboard} />
</Switch>;
// ----

function Dashboard() {
  const { path } = useRouteMatch();
  return (
    <div>
      <h1>Dashboard</h1>
      <Switch>
        <Route path={`${path}/charts`} component={Charts} />
      </Switch>
    </div>
  );
} 
```

### API 一致性和简单性方面的问题

当一个特定的库提供不一致且有些复杂的 API 时，应用程序代码库变得更难维护，更容易出错。因此，软件库开发人员总是试图为应用程序开发人员提供一致而简单的 API，以便更好地管理他们的代码库。但是，库开发人员无法在他们的第一个版本中为应用程序开发人员提供最佳的 API 设计。API 的改进通常伴随着开源开发者社区的反馈、错误报告和特性请求。

React 路由器 v5 有一些不一致的 API 和不熟悉的命名。此外，应用程序开发人员不得不编写比他们预期更多的[样板代码](https://en.wikipedia.org/wiki/Boilerplate_code)，以配置和集成 React Router v5 到他们的应用程序中。例如，对于编程导航，v5 提供了`useHistory`钩子，但几乎所有的 React 路由器新手都期望`useNavigate`。

另外，`Route`组件提供了两个不同的道具来链接组件:`component`和`render`，但是也可以像[悬念 API](https://blog.logrocket.com/react-suspense-data-fetching/#what-is-react-suspense) 一样提供一个`element`道具。此外，为了用 v5 中的 [JavaScript 对象](https://blog.logrocket.com/tag/vanilla-javascript/)定义您的路线，您必须使用单独的 [`react-router-config`](https://www.npmjs.com/package/react-router-config) 包。

### 过度的包和代码大小

正如我们已经知道的，当我们使用重度依赖时，我们的应用程序也会变得很重。类似地，如果我们使用一个需要编写冗长 API 调用的库，我们的应用程序包也会包含过多的代码，这会导致包很大。

React 路由器 v5 最新稳定版( [v5.3.4](https://bundlephobia.com/package/react-router-dom@5.3.4) )的 bundle 大小大约是 [React](https://bundlephobia.com/package/react@18.2.0) 的四倍！在考虑了 React Router v5 的问题之后，我们现在将讨论如何迁移以及什么改变了 React Router v6 的不同之处。

## 迁移到 React 路由器 v6

下面几节将教你如何在已经安装了 React Router 的项目中升级到 React Router v6。

### 在已经安装 React 路由器的项目中升级 React 路由器

要升级 React 路由器版本，请打开终端并导航到您希望升级的项目目录。

若要查看项目依赖项的列表，请使用以下命令:

```
npm ls
# --- or ---
yarn list
```

您应该会看到这样一个列表:

![List Indicating Which Version of React Router is Currently Installed](img/a07ae1e3ee73ba9bb52ecf67fd4e3c5b.png)

虽然生成的列表可能与上面的不完全相同，但是如果您安装了 React 路由器，您应该会看到一个条目及其安装的版本。

接下来，运行以下命令启动升级:

```
npm install [email protected]
# --- or ---
yarn install [email protected]
```

如果您在没有连接到互联网的情况下执行此命令，它将会失败，因为在安装过程中必须下载一些文件。如果一切正常，您应该会看到类似的内容；在我们的例子中，版本是 v6.0.2:

![Update Confirmation Page From npm ](img/9f3bb3c1dab4e1ac2fb18ab59cee836d.png)

如果一切顺利，我们应该注意到，当我们再次运行`npm ls`或`yarn list`命令时，React 路由器已经更新:

![List Showing React Router Updated to v6](img/58650836a4758afdbfb7de5b93e8be4e.png)

### 从头开始安装 React 路由器

首先，在没有安装 React 路由器的项目目录中打开一个终端。要安装特定版本的 React 路由器，请运行以下命令:

```
npm install [email protected][VERSION_TO_BE_INSTALLED]

```

将`[VERSION_TO_BE_INSTALLED]`替换为想要安装的版本，例如`6.0.2`。

或者，运行以下代码来安装最新版本:

```
npm install react-router-dom
# --- or ---
yarn install react-router-dom
```

这种安装也需要使用互联网。如果安装顺利，您应该会看到类似下面的内容:

![Npm Confirmation of React Router Install](img/fc5042aaedbedbd213756b4e00009610.png)

## React 路由器 v6 有什么变化？

重要的是要理解发生了什么变化，这样我们就能明白为什么升级到 React Router v6 是有帮助的。请注意，在某些情况下，开发人员会降级程序以增加功能或避免问题。

我们将讨论 React Router v5 中的变化，当选择在项目中实现哪个版本时应该考虑这些变化。

### 设置路线

在 React Router v5 中，我们有三种不同的技术来生成路由，这导致了混乱。第一个技巧是传递`component`和`path`作为`Route`组件的道具:

```
<Route path="/games" component={Games} />

```

这样做很好，但是，我们不能将道具传递给渲染的组件。第二个是将组件作为`Route`组件的子组件传入:

```
<Route path="/games">
  <Games count=”10” category=”Action” />
</Route>

```

我们可以使用这种方法将自定义道具传递给我们想要呈现的组件。第三个也是最后一个技巧是使用`render` prop，其中一个函数返回要渲染的组件:

```
<Route path="/games" render={(props) => <Games {…props} />} />

```

这也是可行的，让我们给我们正在渲染的组件提供支持。但是，它是不明确的，容易不准确。

在 React Router v6 中，路由已经被简化到我们不再需要利用`Switch`来查询它们。相反，我们使用一个名为`Routes`的“必需”组件，它只按名称搜索路由。使用通配符可以使用`*`字符进行查询。

然后，我们将要呈现的组件作为`element`道具提供给`Route`组件。我们还可以为我们希望呈现的每条路线中的组件提供定制道具。

下面的代码片段演示了如何在 v6 中定义路由:

```
<Routes>
  <Route path="/games" element={<Games />} />
  <Route path="/movies" element={<Movies genre="Action" age="13" />} />
</Routes> 

```

还可以使用`useRoutes`在 app 中查询路线。为此，我们必须修改`index.js`内容，将`App`包装器从`React.StrictMode`改为`BrowserRouter`，如下所示:

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import { BrowserRouter } from 'react-router-dom'

ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  document.getElementById('root')
);

```

然后，在`App.js`文件中，我们通过执行以下操作来定义路线:

```
import { Outlet, useRoutes } from 'react-router-dom';
const App = () => {
  const routes = useRoutes([
    {
      path: '/',
      element: <div>Hello Index</div>
    },
    {
      path: 'games',
      element: <Games />,
      children: [
      {
        path: '',
        element: <div>Games Index</div>
      },
      {
        path: ':id',
        element: <div>Game Details</div>
      }]
    }
  ]);
  return routes;
}

const Games = () => {
  return (
    <div className="Games">
      <div>This is the Games page</div>
      <Outlet />
    </div>
  );
}

export default App;

```

这里我们导入了`Outlet`和`useRoutes`。`useRoutes`允许我们将路线定义为一个对象数组，在该数组中我们可以指定一个`path`、浏览路径时要呈现的`element`以及子路径。`Outlet`帮助我们渲染与当前路径匹配的子路径。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## `useNavigate`对`useHistory`

在 React Router v5 中，我们使用`useHistory()`以编程方式处理导航。这种技术存在一些问题，比如命名混乱和有两种导航方法，`history.push`和`history.replace`。

为了在 v5 中实现导航，我们通常做以下事情:

```
import { useHistory } from 'react-route-dom';

const App = () => {
  const history = useHistory();
  const handleClick = () => {
    history.push('/home');
  }
  return (
    <div>
      <button onClick={handleClick}>Go Home</button>
    </div>
  ) 
}
export default App;

```

如果我们需要替换当前历史`frame.replace('/home')`方法调用，我们使用历史。

在 v6 中，我们用`useNavigate`代替`useHistory`:

```
import { useNavigate } from "react-router-dom";

const App = () => {
  const navigate = useNavigate();
  const handleClick = () => {
    navigate("/home");
  }
  return (
    <div>
      <button onClick={handleClick}>Go Home</button>
    </div>
  );
}
export default App

```

代替`history.replace`，我们可以使用选项对象调用 navigate 方法，如下所示:

```
const handleClick = () => {
  navigate('/home', { replace: true });
}

```

那么，v5 中遗留历史 API 中的`go`、`goBack`和`goForward`方法发生了什么变化呢？在 v6 中，我们可以用数字调用相同的导航函数。例如，看看下面演示`go back`、`forward`和`2nd history`框架导航的代码片段:

```
navigate(-1)   // v5's history.go(-1) or history.goBack()
navigate(1)    // v5's history.go(1) or history.goForward()
navigate(2)    // v5's history.go(2)

```

同样，v6 用声明性版本的`useNavigate`、`Navigate`替换了`Redirect`:

```
import { Navigate } from 'react-router-dom';

function App() {
  return <Navigate to='/home' replace state={state} />;
}
```

此外，navigate API 现在已经准备就绪。您可以从 GitHub 上的这个源文件[中查看一个基于暂记的路由器实现示例。](https://github.com/remix-run/react-router/blob/4d915e3305df5b01f51abdeb1c01bf442453522e/examples/data-router/src/routes.tsx#L289)

## 了解`NavLink`中的变化

v6 版本还改变了`NavLink`组件接口，帮助您创建具有动态样式的面包屑、选项卡和导航菜单。这是大多数基于 React 路由器的应用程序中经常使用的组件，因此您可能需要为与`NavLink`相关的重写分配更多时间。

### 在`NavLink`中指定精确的路线路径

在 v5 中，我们在实施精确的路径或路线时使用 prop。为了实现这一点，我们执行以下操作:

```
<NavLink to="/dashboard" exact>Go Home</NavLink>

```

在 v6 中，我们使用`end`属性来确保精确的路线:

```
<NavLink to="/dashboard" end>Go Home</NavLink>
```

React 路由器团队重命名了这个道具，以使这个库与 React 生态系统的实践保持一致。

### 激活样式`NavLink`

在 React Router v5 中，我们使用`activeClassName`或`activeStyle`道具来设计当前活动的`NavLink`的样式。

例如:

```
<NavLink 
  to="/home" 
  style={{color: 'black'}} 
  activeStyle={{color: 'blue'}} 
  className="nav_link" 
  activeClassName="active" >
  Go Home
</NavLink>
```

在 v6 中，我们必须使用一个带有[析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#object_destructuring)参数`{isActive}`的函数来调节用于活动`NavLink`的`style`或`className`。

例如:

```
<NavLink 
  to="/home" 
  style={({isActive}) => ({color: isActive ? 'blue' : 'black'})} 
  className={({isActive}) => `ln-${isActive ? ' active' : ''}`} >
  Go Home
</NavLink>
```

这些变化也解决了 v5 遗留 API 中的一致性和简单性问题。但是，如果您的 React 应用程序大量使用了上述不赞成使用的特性，您可以通过为 v6 的`NavLink`创建一个临时包装器来使您的迁移过程更快更顺利，如下所示:

```
import * as React from 'react';
import { NavLink as BaseNavLink } from 'react-router-dom';

const NavLink = React.forwardRef(
  ({ activeClassName, activeStyle, ...props }, ref) => {
    return (
      <BaseNavLink
        ref={ref}
        {...props}
        className={({ isActive }) =>
          [
            props.className,
            isActive ? activeClassName : null,
          ]
            .filter(Boolean)
            .join(" ")
        }
        style={({ isActive }) => ({
          ...props.style,
          ...(isActive ? activeStyle : null),
        })}
      />
    );
  }
);

```

上面的代码将 v6 的`NavLink`转换成类似 v5 的`NavLink`，并让你通过保持工作 v5 风格的`NavLinks`来部署 v6 的应用程序，这样你就可以不急着重写了。

### 用`useMatch`代替`useRouteMatch`

在 v5 中，`useRouteMatch`用于创建与特定路线匹配的相对子路线路径。我们可以使用这个钩子，不管有没有模式参数，如下所示:

```
// Receive the matched path and url of the current <Route/>
const { path, url } = useRouteMatch();

// Receive the matched route details based on the pattern argument
const match = useRouteMatch('/users/:id');
```

在 v6 中，我们对此使用`useMatch`。使用`useMatch`钩子需要一个模式参数，并且不接受模式作为数组。因此，以下代码片段对 v6 有效:

```
const match = useRouteMatch('/users/:id');
```

然而，如果你不把模式参数传递给钩子，v6 会抛出一个`error`。上述`match`常量将接收[关于路线匹配](https://github.com/remix-run/react-router/blob/main/docs/utils/match-path.md)的详细信息。

### 嵌套的相对路线支持

在 v5 中，创建基于 JSX 的嵌套路由会生成更多的样板代码段，因为我们必须显式地构造完整的 URL。考虑以下示例代码片段:

```
// ----
<Switch>
  <Route path="/dashboard" component={Dashboard} />
</Switch>;
// ----

function Dashboard() {
  const { path } = useRouteMatch();
  return (
    <div>
      <h1>Dashboard</h1>
      <Switch>
        <Route path={`${path}`} component={Summary} />
        <Route path={`${path}/stats`} component={Stats} />
        <Route path={`${path}/charts`} component={Charts} />
      </Switch>
    </div>
  );
}

```

这里我们需要使用`useRouteMatch`钩子为每个`path`道具手动构建完整路径。因此，当您开发复杂的应用程序时，您可以在任何地方看到这种样板代码。

React Router v6 使得基于 JSX 的嵌套路由定义变得非常简单。看看下面的 v6 对上面的 v5 样本代码片段的改写:

```
// ---
<Routes>
  <Route path="/dashboard" element={<Dashboard/>}>
    <Route path="" element={<Summary/>}/>
    <Route path="stats" element={<Stats/>}/>
    <Route path="charts" element={<Charts/>}/>
  </Route>
</Routes>
// ---

function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Outlet/>
    </div>
  );
}

```

这里，v6 允许开发人员以嵌套的方式使用`Route`组件来定义嵌套的路由。注意呈现嵌套组件的`Outlet`组件:`Summary`、`Stats`和`Charts`。这里我们不需要像 v5 那样在所有嵌套的路由前添加`/dashboard/`，因为 v6 支持关系路径定义。

## React 路由器 v6 去掉了什么？

在 React Router v6 中，一些功能不再受支持，因为它们不明确或有错误。到目前为止，发现有两个特征被删除:

### `Prompt`、`usePrompt`和`useBlocker`

*   `usePrompt` (JSX `Prompt`):当页面处于不需要用户离开的状态时，用于确认用户是否要退出页面
*   `useBlocker`:类似于`usePrompt`，用于阻止用户离开特定页面

这两个功能相似，并且具有相同的问题。在用户试图在页面外导航并且导航被`usePrompt`或`useBlocker`限制的情况下，即使导航被阻止，URL 路径也会改变。

尽管有关于`useBlocker`和`usePrompt`的问题，创造者们仍然在考虑在发布稳定版本后将它们添加回 v6。[点击此处了解更多详情](https://github.com/remix-run/react-router/issues/8139#issuecomment-954427837)。与此同时，开发者社区为需要 React Router v6 中这些移除功能的应用开发者创建了类似 [`react-router-prompt`](https://www.npmjs.com/package/react-router-prompt) 的库。

### 嵌套路由器支持

使用一个`BrowserRouter`组件就足以开发高级的 React 应用导航。一些使用 React Router v5 的应用程序开发人员练习在他们的应用程序中使用嵌套路由器来隔离复杂的组件。例如，大多数开发人员在`BrowserRouter`实例中使用 v5 的`MemoryRouter`来隔离模态和选项卡组。不幸的是，v6 放弃了对嵌套路由器的支持，并促使应用程序开发人员只使用一种首选的路由器实现。

大多数构建了嵌套路由器的应用程序开发人员现在都要求将 v6 中不赞成使用的特性升级到 v6。一些开发人员担心 v6 中的这一突破性变化，甚至[考虑将](https://github.com/shapeshift/web/pull/968#issuecomment-1033431536)迁移到另一个 React 路由模块。

根据[这个讨论](https://github.com/remix-run/react-router/issues/7375)，在当前的开发时间表中，维护者似乎没有关注嵌套路由器支持。但是，开发者社区可能很快会支持维护者来关注这个被否决的特性！或者，维护者将为这个问题提供一个生产友好的解决方案。

开发人员社区找到了下面的解决方法(相当于黑客)来启用 v6 中的嵌套路由器，但是由于意外的行为，它对生产环境并不友好:

```
<UNSAFE_LocationContext.Provider value={ null }>
  <MemoryRouter>
    { /* some routes not related to the outer router */ }
  </MemoryRouter>
</UNSAFE_LocationContext.Provider>  

```

在 GitHub 上订阅问题 [#7375](https://github.com/remix-run/react-router/issues/7375) ，获取与 v6 嵌套路由器相关的更新。

## React 路由器 v6 为什么在你的 app 里不行？

完成迁移过程后，您可能会注意到意外行为、错误消息和警告消息。由于 v6 中的重大变化，您可能需要解决一个 bug。我们无法将有路由问题的应用交付生产。因此，用字符串测试计划来检测路由问题更好。

由于以下问题，您的 React 路由器集成在升级到 v6 后将无法正常工作。

### `history.push`方法将无法正常工作

您可能已经将`history`包更新到了最新版本，而没有将 React Router 更新到版本 6。v5 `history`包兼容 React 路由器 v6，不兼容 React 路由器 v5。要解决这个问题，首先，更新 React 路由器，然后它会自动下载一个兼容的`history`版本。如果还需要运行 React 路由器 v5，使用`history` v4(即 v4.10.1)。见[本期](https://github.com/remix-run/history/issues/804)。

### 在`react-router-dom`中找不到导出`x`

您正在导入在 v6 中已删除或重命名的挂钩或组件。比如`useRouteMatch`在 v6 就不行。要解决这个问题，请确保不要导入不推荐的 v5 挂钩或组件。

### 嵌套布线不会渲染组件

确保在嵌入嵌套路线组件的父组件中添加`<Outlet/>`。检查前面的`Dashboard`组件作为参考实现。

### `x`不是`<Route>`组件

在 v6 中，`Routes`只能包含`Route`或`React.Fragment`子级，所以要确保将其他 HTML 元素和组件从`Routes`组件移到合适的位置。

### `meta.relativePath.startsWith`不是函数

您正在使用`Route`的`path`道具中的对象。在 v6 中，不能使用 Regex 对象或数组作为路径。确保只使用字符串作为路径。

这里我们讨论了一些经常出现的问题。但是，您可能会收到一条在互联网上找不到的独特错误消息。在这种情况下，彻底检查您的代码库是否使用了不推荐的 API，然后在 [React Router 中打开一个新的讨论线程。](https://github.com/remix-run/react-router/discussions)

## React 路由器 v6 优于 v5 的优势

如果一个版本没有比另一个版本提供任何优势，那么迁移就没有意义。在本节中，我们将讨论从 React 路由器 v5 升级到 v6 的好处。

对于开发人员来说，最重要的考虑之一是应用程序的可移植性。与以前的版本相比，React 路由器 v6 的尺寸已经大大减小。v5 和 v6 之间的差异大约是 60%。

这里有一个来自 [Bundlephobia](https://bundlephobia.com/) 的对比:

![Bundlepedia Example of React Router v5](img/63a01b275f9d6892f068da3acbf4c06c.png)

![Bundlepedia Example of React Router v6](img/1188060e908f94c6a83a204355dadde6.png)

由于尺寸减小，编译和部署应用程序将更容易和更快。此外，我们讨论的 React Router v6 中的大部分修改更有益，问题更少。

如前所述，React Router v6 允许路由在构造路由时接受组件作为元素，并将自定义属性传递给组件。我们也可以嵌套路由并使用相对链接。这有助于我们防止或减少各种组件文件中新路线的定义，并使将全局数据传递给组件和道具变得容易。

当重定向而不是使用`useHistory`时，使用`useNavigate`的新方法被设计成提供更好的导航 API。它允许我们通过`navigate()`轻松设置新路线，而不是使用多种方法:`history.push()`、`history.replace()`和`history.goBack()`。此外，新的 v6 API 是如此的简洁和开发者友好。它让你减少样板代码和复杂的路线定义，使你的应用程序代码库干净。

最后，导航链接`NavLink`允许我们调节活动链接，这使得我们的代码更加整洁和简单，而不是为活动和非活动状态传递两个不同的道具。通过这样做，我们可以很容易地使用一个三进制操作符来决定当一个链接活动或不活动时，哪种样式会受到影响。

## 结论

读完这篇文章后，我希望您能够升级您的 React 路由器版本并重构您的代码库，以便与 React Router v6 无缝协作。根据以上几点更新您的代码有助于您迁移任何基于 React 路由器的应用程序。但是，如果你的应用程序路由如此复杂，并且大量使用不推荐的 v5 特性，你可以考虑使用来自维护者的官方增量迁移指南。

不仅 React 路由器 v5 应该升级到 v6，Reach 路由器也应该切换到 v6。

定期升级 React Router v6 并查看[文档](https://reactrouter.com/en/main)中的任何更改，以保持最新版本。

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