# Wouter 简介:React 路由器替代方案

> 原文：<https://blog.logrocket.com/an-introduction-to-wouter-a-react-router-alternative/>

JavaScript 生态系统总是处于不断变化的状态。尽管发生了这些变化，JavaScript 社区还是努力解决出现的所有问题。如果您以前使用过 React，您会注意到首选路由解决方案是 React 路由器库。

它经常被错误地描述为来自脸书的官方路由解决方案。然而实际上，脸书大学的大多数项目甚至没有利用它。

大多数 web 应用程序项目和用例可以用一个小而简单的路由库做得很好。

一个这样的路由解决方案是 Wouter。在本指南中，您将对 Wouter 进行概述，并了解如何用它来代替 React 路由器。

## 客户端路由是如何工作的？

客户端路由(CSR)类似于服务器端路由(SSR)，只是 CSR 是由浏览器处理的。在典型的 web 应用程序中，一些页面或路径映射到不同的 URL。每个页面都有一些逻辑和一个模板，一旦调用了路由，就会呈现这个模板。

CSR 完全由浏览器的 JavaScript 引擎处理。客户端框架预取并加载 JavaScript 包。这使得应用程序能够在包中存储的页面之间切换，而不会丢失状态或应用程序数据。

前端路由或 CSR 的工作方式是将 URL 与 JavaScript 文件中的登录信息进行匹配，然后使用 API 从服务器获取必要的数据。

要了解有关客户端路由的更多信息，您可以参考本指南的，其中详细解释了 CSR。

## 伤口特征

下面你会发现 Wouter 比 React 路由器提供的所有特性或优势。

*   它没有依赖性——与 React 路由器的 17KB 相比，只有 1308 字节的 gzip。
*   除了 react，Wouter 还支持 Preact，这是 React 的一个微小的替代方案，使用相同的 API。
*   组件在 Wouter 中是完全可选的。
*   Wouter 是一个基于 Hook 的 API，所以你对路由有更多的控制权。

## Wouter 入门

看看这个使用 Wouter 设置路由的基本代码。你所需要做的就是从`wouter`进口`Link`和`Route`组件，然后你就可以开始了。与`react-router`所需的所有样板文件相比，这个设置非常简单。

```
import { Link, Route } from "wouter";

const App = () => (
  <div>
    <Link href="/users/John">
      <a className="link">My Account</a>
    </Link>

    <Route path="/inbox" component={InboxPage} />
    <Route path="/settings">Settings</Route>
    <Route path="/users/:name">
      {(params) => <div>Hello, {params.name}!</div>}
    </Route>
  </div>
);

export default App;
```

## 外部组件

### `<Route />`

`<Route />`组件表示基于特定模式有条件呈现的应用程序的一部分。模式是一个字符串，可以包含特殊字符来匹配动态段。

Wouter 提供了不同的方式来声明路由。

```
import { Route } from "wouter";

// basic usage
<Route path="/home"><Home /></Route>

// render-prop style usage
<Route path="/profile/:id">
  {params => <ProfilePage id={params.id} />}
</Route>

// prop based usage
<Route path="/orders/:status" component={Orders} />
```

### `<Link />`

`<Link />`组件呈现一个锚`<a />`元素，单击该元素可以导航到在`href`属性中指定的路线。还可以通过提供一个自定义组件作为其子组件来对其进行自定义。

```
import { Link } from "wouter";

<Link href="/users/all" className="active">
  List Users
</Link>;

<Link href="/users/all">
  <CustomLink> List Users </CustomLink>
</Link>;
```

### `<Switch />`

在某些情况下，您可能需要独占路由。也就是说，任何时候都只需要在页面上呈现一条路线，即使这些路线具有彼此重叠的模式。

您可以使用`<Switch />`组件实现独占路由。它只呈现第一个匹配的路线。

```
import { Route, Switch } from "wouter";

<Switch>
  <Route path="/users/all" component={AllUsers} />
  <Route path="/users/:userId" component={User} />
</Switch>;
```

### `Redirect`

`<Redirect />`组件用于将用户重定向到`to`属性中提供的另一条路线。它在内部使用`useLocation`钩子来触发`useEffect`回调块内部的导航。

```
import { Redirect } from "wouter";

const User = ({ userId }) => {
  if (!userId) return <Redirect to="/login" />;

  return <div> Current User is {userId} </div>;
};
```

为了以编程方式导航用户，您可以使用`useLocation`钩子直接访问`setLocation`方法。

```
import { useLocation } from "wouter";

const App = () => {
  const [location, setLocation] = useLocation();

  const navigate = () => {
    setLocation("/login");
  };

  return (
    <div>
      <button onClick={() => navigate()}> Logout </button>
    </div>
  );
};
```

## 如何激活当前链接

例如，在导航菜单中，Wouter 开箱即用不支持确定链接当前是否处于活动状态。然而，通过创建一个包装器组件并使用`useRoute`钩子检测当前路径是否有效，可以很容易地实现这个功能。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
import { useRoute, Link } from "wouter";

const CustomLink = (props) => {
  const [isActive] = useRoute(props.href);

  return (
    <Link {...props}>
      <a className={isActive ? "active" : ""}>{props.children}</a>
    </Link>
  );
};
```

## 使用 Wouter 进行服务器端渲染(SSR)

要在服务器端上下文中使用 Wouter，您需要指示路由器当前位置需要来自 HTTP 请求，而不是浏览器历史。

您可以使用`staticLocationHook`而不是默认的`useLocation`钩子来告诉路由器处理传入的 HTTP 请求。

```
import { renderToString } from "react-dom/server";
import { Router } from "wouter";

import staticLocationHook from "wouter/static-location";

import App from "./App";

const handleRequest = (req, res) => {
  const prerendered = renderToString(
    <Router hook={staticLocationHook(req.path)}>
      <App />
    </Router>
  );

  // return with prerendered html
};
```

如果您需要检测重定向，您还可以向`staticLocationHook`提供 record 选项。`location.history`属性是一个匹配用户历史的数组，它将在加载页面后被捕获。

```
import { renderToString } from "react-dom/server";
import { Router } from "wouter";
import staticLocationHook from "wouter/static-location";

import App from "./App";

const handleRequest = (req, res) => {
  const location = staticLocationHook(req.path, { record: true });
  const prerendered = renderToString(
    <Router hook={location}>
      <App />
    </Router>
  );

  const finalPage = location.history.slice(-1)[0];
  if (finalPage !== req.path) {
    // perform redirect here
  }
};
```

### 进一步最小化包的大小

如果你是一个捆绑包大小的极简主义者，并希望你的应用程序的捆绑包大小尽可能小，你可以使用`useLocation`钩子来处理所有的路线。

`useLocation` Hook 是一个底层导航 API，为核心 Wouter 组件和路由提供动力。它本质上是本机浏览器`location`对象的包装器。

```
import { useLocation } from "wouter";

const CurrentLocation = () => {
  const [location, setLocation] = useLocation();

  return (
    <div>
      {`The active page is: ${location}`}
      <button onClick={() => setLocation("/users")}>
        Click to update the route
      </buttom>
    </div>
  );
};
```

还可以从一个单独的文件访问`useLocation`挂钩，如果您只打算使用`useLocation`挂钩并手动匹配路线，这将进一步减小包的大小。

```
import useLocation from "wouter/use-location";

const UsersRoute = () => {
  const [location] = useLocation();

  if (location !== "/users") return null;

  // render the route
};
```

### 过时的平台支持

Wouter 库使用最新的 EcmaScript (ES) 6 特性，如析构赋值和 const/let 变量声明，并且不附带 ES5 transpiled 源代码。如果你想支持像 Internet Explorer 11 和更低版本的传统浏览器，确保你在`node_modules`文件夹上运行 Babel。

## 与 React 路由器相比的优缺点

下面列出了 Wouter 与 React 路由器库相比的一些优点和缺点。

### 优点:

*   Wouter 很小，不依赖于其他库
*   Wouter 对于附带项目、黑客马拉松项目和大约有十条路线的小应用程序来说非常优秀
*   Wouter 对于那些想通过阅读源代码来了解更多关于 React Hooks 的开发人员来说是一个很好的启发

### 缺点:

*   Wouter 只实现了一组有限的功能(重定向、开关、对基于散列的历史的支持、扩展模式语法(如`/app/:foo(\d+)`)尚未开发)
*   对于复杂的企业级应用程序来说，这不是一个好的选择
*   Wouter 后面的社区与 React 路由器相比要小得多

## 结论

Wouter 无疑不是 React 路由器的替代品，也不建议在您的项目中切换到 Wouter。从 React 路由器到 Wouter 的迁移在大多数用例中是不可行的，因为 Wouter 只是模仿 React 路由器 API 的特性，并没有完全实现这些特性。

但 Wouter 背后的主要思想是，大多数开发者倾向于选择 React 路由器，然后只使用 30%的功能或特性。对于只需要简单路由的基本项目，React 路由器可能是多余的。

Wouter 也可以作为一个参考，说明如何使用 React Hooks 来创建可以开源的优秀库。

这就是本指南的全部内容。直到下一次，继续探索更多的路由解决方案和库。

### 参考

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

## 调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).