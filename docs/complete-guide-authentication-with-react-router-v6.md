# React 路由器 v6 认证完整指南

> 原文：<https://blog.logrocket.com/complete-guide-authentication-with-react-router-v6/>

[React Router v6](https://reactrouter.com/docs/en/v6/getting-started/overview) 是 React 应用程序的一个流行且强大的路由库。它提供了一种声明性的、基于组件的路由方法，并处理 URL 参数、重定向和加载数据等常见任务。

React Router 提供了一个最直观的 API，支持延迟加载和 SEO 友好的服务器端渲染。React 路由器的最新版本引入了许多新概念，如`<Outlet />`和布局路由，但文档仍然很少。

本教程将演示如何使用 React Router v6 创建受保护的路由并添加身份验证。

所以，启动你最喜欢的文本编辑器，让我们开始吧！

*向前跳转:*

## 入门指南

打开终端，通过运行以下命令创建一个新的 React 项目:

```
> npx create-react-app ReactRouterAuthDemo
> cd ReactRouterAuthDemo

```

接下来，在 React 应用程序中安装 React 路由器作为依赖项:

```
> npm install react-router-dom

```

安装 React 路由器依赖项后，我们需要编辑`src/index.js`文件。

从`react-router-dom`导入`BrowserRouter`，然后用`<BrowserRouter />`包裹`<App />`组件，像这样:

```
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import { BrowserRouter } from "react-router-dom";

import App from "./App";

const rootElement = document.getElementById("root");
const root = createRoot(rootElement);

root.render(
  <StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </StrictMode>
);

```

现在，我们已经设置好在我们的应用程序中的任何地方使用 React 路由器组件和挂钩。

让我们用一些路径替换来自`App.js`文件的样板代码。

## 基本路由

React Router 提供了`<Routes />`和`<Route />`组件，使我们能够基于组件的当前位置来呈现组件:

```
import { Routes, Route } from "react-router-dom";
import { LoginPage } from "./pages/Login";
import { HomePage } from "./pages/Home";
import "./styles.css";

export default function App() {
  return (
    <Routes>
      <Route path="/" element={<HomePage />} />
      <Route path="/login" element={<LoginPage />} />
    </Routes>
  );
}

```

### 用`<Route >`进行基本路由

`<Route />`提供 app 上的路径和不同 React 组件之间的映射。例如，当有人导航到`/login`时，为了呈现`LoginPage`组件，我们只需要提供`<Route />`，就像这样:

```
<Route path="/login" element={<LoginPage />} />

```

组件可以被认为是一个 if 语句；只有当 URL 位置与指定的路径匹配时，它才会使用它的元素对 URL 位置进行操作。

### 用`<Routes />`进行基本路由

`<Routes />`组件是 React 路由器 v5 中`<Switch />`组件的替代组件。

要使用`<Routes />`，我们将首先在 pages 目录中创建包含以下内容的`Login.jsx`和`Home.jsx`文件:

```
// Login.jsx
export const LoginPage = () => (
  <div>
    <h1>This is the Login Page</h1>
  </div>
);

// Home.jsx
export const HomePage = () => (
  <div>
    <h1>This is the Home Page</h1>
  </div>
);

```

接下来，我们将运行此命令来启动应用程序:

```
> npm run start

```

在浏览器上，我们默认会看到`HomePage`组件。如果我们走`/login`路线，我们会看到`LoginPage`组件呈现在屏幕上。

或者，我们可以使用一个普通的 JavaScript 对象，在我们的应用程序中使用`useRoutes`钩子来表示路线。这是一种定义路线的功能方法，其工作方式与组合`<Routes />`和`<Route />`组件的方式相同:

```
import { useRoutes } from "react-router-dom";
// ...

export default function App() {
  const routes = useRoutes([
    {
      path: "/",
      element: <HomePage />
    },
    {
      path: "/login",
      element: <LoginPage />
    }
  ]);
  return routes;
}

```

现在基本的设置已经完成，让我们看看如何创建受保护的路由，以便未经身份验证的用户不能访问我们应用程序中的某些内容。

## 创建受保护的路由

在创建受保护的路由(也称为私有路由)之前，让我们创建一个定制钩子，它将使用[上下文 API](https://reactjs.org/docs/context.html) 和`useContext`钩子来处理经过身份验证的用户的状态:

```
import { createContext, useContext, useMemo } from "react";
import { useNavigate } from "react-router-dom";
import { useLocalStorage } from "./useLocalStorage";
const AuthContext = createContext();

export const AuthProvider = ({ children }) => {
  const [user, setUser] = useLocalStorage("user", null);
  const navigate = useNavigate();

  // call this function when you want to authenticate the user
  const login = async (data) => {
    setUser(data);
    navigate("/profile");
  };

  // call this function to sign out logged in user
  const logout = () => {
    setUser(null);
    navigate("/", { replace: true });
  };

  const value = useMemo(
    () => ({
      user,
      login,
      logout
    }),
    [user]
  );
  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
};

export const useAuth = () => {
  return useContext(AuthContext);
};

```

通过`useAuth`钩子，我们公开了用户的状态以及用户登录和注销的一些方法。当用户注销时，我们使用 React Router 的`useNavigate`钩子将他们重定向到主页。

为了在页面刷新时保持用户的状态，我们将使用`useLocalStorage`钩子来同步浏览器本地存储中的状态值:

```
import { useState } from "react";

export const useLocalStorage = (keyName, defaultValue) => {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const value = window.localStorage.getItem(keyName);
      if (value) {
        return JSON.parse(value);
      } else {
        window.localStorage.setItem(keyName, JSON.stringify(defaultValue));
        return defaultValue;
      }
    } catch (err) {
      return defaultValue;
    }
  });
  const setValue = (newValue) => {
    try {
      window.localStorage.setItem(keyName, JSON.stringify(newValue));
    } catch (err) {}
    setStoredValue(newValue);
  };
  return [storedValue, setValue];
};

```

`<ProtectedRoute />`组件将简单地检查来自`useAuth`钩子的当前用户状态，然后如果用户未被认证，则重定向到主屏幕:

```
import { Navigate } from "react-router-dom";
import { useAuth } from "../hooks/useAuth";

export const ProtectedRoute = ({ children }) => {
  const { user } = useAuth();
  if (!user) {
    // user is not authenticated
    return <Navigate to="/" />;
  }
  return children;
};

```

为了重定向用户，我们使用了`<Navigate />`组件。当父组件渲染时，`<Navigate />`组件改变当前位置。在内部，它使用`useNavigate`钩子。

在`App.js`文件中，我们可以用`<ProtectedRoute />`组件包装页面组件。在下面的例子中，我们用`<ProtectedRoute />`包装了`<SettingsPage />`和`<ProfilePage />`组件。现在，当未经验证的用户试图访问`/profile`或`/settings`路径时，他们将被重定向到主页:

* * *

```
import { Routes, Route } from "react-router-dom";
import { LoginPage } from "./pages/Login";
import { HomePage } from "./pages/Home";
import { SignUpPage } from "./pages/SignUp";
import { ProfilePage } from "./pages/Profile";
import { SettingsPage } from "./pages/Settings";
import { ProtectedRoute } from "./components/ProtectedRoute";

export default function App() {
  return (
    <Routes>
      <Route path="/" element={<HomePage />} />
      <Route path="/login" element={<LoginPage />} />
      <Route path="/register" element={<SignUpPage />} />
      <Route
        path="/profile"
        element={
          <ProtectedRoute>
            <ProfilePage />
          </ProtectedRoute>
        }
      />
      <Route
        path="/settings"
        element={
          <ProtectedRoute>
            <SettingsPage />
          </ProtectedRoute>
        }
      />
    </Routes>
  );
}

```

如果受保护的路由数量有限，上面的方法就很好，但是如果有多条这样的路由，我们就必须包装每一条，这是很繁琐的。

相反，我们可以使用 React Router v6 嵌套路由特性将所有受保护的路由包装在一个布局中。

## 使用嵌套路线和`<Outlet />`

React Router v6 中最强大的功能之一是嵌套路由。此功能允许我们拥有包含其他子路由的路由。我们的大部分布局都与 URL 上的段相关联，React Router 完全支持这一点。

例如，我们可以向`<HomePage />`和`<LoginPage />`路线添加父`<Route />`组件，如下所示:

```
import { ProtectedLayout } from "./components/ProtectedLayout";
import { HomeLayout } from "./components/HomeLayout";
// ...

export default function App() {
  return (
    <Routes>
      <Route element={<HomeLayout />}>
        <Route path="/" element={<HomePage />} />
        <Route path="/login" element={<LoginPage />} />
      </Route>

      <Route path="/dashboard" element={<ProtectedLayout />}>
        <Route path="profile" element={<ProfilePage />} />
        <Route path="settings" element={<SettingsPage />} />
      </Route>
    </Routes>
  );
}

```

父组件`<Route />`也可以有一个路径，并负责在屏幕上呈现子组件`<Route />`。

当用户导航到`/dashboard/profile`时，路由器将呈现`<ProfilePage />`。为了实现这一点，父 route 元素必须有一个`<Outlet />`组件来呈现子元素。`Outlet`组件使嵌套的 UI 在呈现子路由时可见。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

父路由元素还可以有附加的公共业务逻辑和用户界面。例如，在`<ProtectedLayout />`组件中，我们包含了私有路线逻辑和一个公共导航栏，当子路线被渲染时，该导航栏将是可见的:

```
import { Navigate, Outlet } from "react-router-dom";
import { useAuth } from "../hooks/useAuth";

export const ProtectedLayout = () => {
  const { user } = useAuth();

  if (!user) {
    return <Navigate to="/" />;
  }

  return (
    <div>
      <nav>
        <Link to="/settings">Settings</Link>
        <Link to="/profile">Profile</Link>
      </nav>
      <Outlet />
    </div>
  )
};

```

除了`<Outlet />`组件，我们还可以选择使用`useOutlet`钩子来实现同样的目的:

```
import { Link, Navigate, useOutlet } from "react-router-dom";
// ...

export const ProtectedLayout = () => {
  const { user } = useAuth();
  const outlet = useOutlet();

  if (!user) {
    return <Navigate to="/" />;
  }

  return (
    <div>
      <nav>
        <Link to="/settings">Settings</Link>
        <Link to="/profile">Profile</Link>
      </nav>
      {outlet}
    </div>
  );
};

```

类似于受保护的路由，我们不希望经过身份验证的用户访问`/login`路径。让我们在`<HomeLayout />`组件中处理它:

```
import { Navigate, Outlet } from "react-router-dom";
import { useAuth } from "../hooks/useAuth";

export const HomeLayout = () => {
  const { user } = useAuth();

  if (user) {
    return <Navigate to="/dashboard/profile" />;
  }

  return (
    <div>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/login">Login</Link>
      </nav>
      <Outlet />
    </div>
  )
};

```

你可以在这个 [CodeSandbox](https://codesandbox.io/s/react-router-v6-auth-demo-4jzltb) 中查看完整的代码和演示。

## 使用 React 路由器 6.4 版数据库 API

在 v6.4 中， [React 路由器包引入了](https://reactrouter.com/en/main/start/overview)新的路由器和数据 API。今后，所有 web 应用程序都应该使用`createBrowserRouter()`函数来启用数据 API 访问。将现有应用程序更新到新 API 的最快方法是用`createRoutesFromElements()`函数包装路由组件:

```
export const router = createBrowserRouter(
  createRoutesFromElements(
    <>
      <Route element={<HomeLayout />}>
        <Route path="/" element={<HomePage />} />
        <Route path="/login" element={<LoginPage />} />
      </Route>

      <Route path="/dashboard" element={<ProtectedLayout />}>
        <Route path="profile" element={<ProfilePage />} />
        <Route path="settings" element={<SettingsPage />} />
      </Route>
    </>
  )
);

```

在`index.js`文件中，使用`<RouterProvider />`组件，而不是`<BrowserRouter />`组件，并从`App.js`文件中传递导出的`router`对象。还要注意，没有`BrowserRouter``AuthProvider`将无法工作，因为它使用`useNavigate()`功能:

```
import { router } from "./App";
...
root.render(
  <StrictMode>
    <ThemeProvider theme={theme}>
      <RouterProvider router={router} />
    </ThemeProvider>
  </StrictMode>
);

```

为了在路由器上下文中使用`AuthProvider`，我们需要创建一个`<AuthLayout />`组件，用`AuthProvider`包装`outlet`元素。这将使所有子路由都能够访问授权上下文:

```
import { useLoaderData, useOutlet } from "react-router-dom";
import { AuthProvider } from "../hooks/useAuth";

export const AuthLayout = () => {
  const outlet = useOutlet();

  return (
    <AuthProvider>{outlet}</AuthProvider>
  );
};

```

现在，我们可以使用`AuthLayout`组件作为根级路由，如下所示:

```
export const router = createBrowserRouter(
  createRoutesFromElements(
    <Route
      element={<AuthLayout />}
    >
      <Route element={<HomeLayout />}>
        ...
      </Route>

      <Route path="/dashboard" element={<ProtectedLayout />}>
        ...
      </Route>
    </Route>
  )
);

```

此时，应用程序已经准备好访问数据 API。

通过 React Router 的数据 API，我们可以抽象出数据是如何获取的。通常，我们会使用`useEffect`钩子在组件内部加载数据。相反，我们可以使用路由器的`loader()`函数在呈现 route 元素之前获取数据。

考虑一个用例，当应用程序加载时，我们需要获取登录用户的数据。根据用户是否经过身份验证，我们可以将他们重定向到主页或仪表板。

为了模拟数据获取，我们可以将`Promise`与`setTimeout()`方法结合使用，并从`localStorage`获取用户:

```
const getUserData = () =>
  new Promise((resolve) =>
    setTimeout(() => {
      const user = window.localStorage.getItem("user");
      resolve(user);
    }, 3000)
  );

```

使用`Route`组件上的`loader`道具，我们可以借助`defer()`效用函数将`getUserData()`承诺传递给`AuthLayout`组件。`defer()`函数允许我们在呈现`Route`组件之前传递承诺而不是解析的值:

```
import {
  Route,
  createBrowserRouter,
  createRoutesFromElements,
  defer
} from "react-router-dom";

import { AuthLayout } from "./components/AuthLayout";
...
// ideally this would be an API call to server to get logged in user data
const getUserData = () =>
  new Promise((resolve) =>
    setTimeout(() => {
      const user = window.localStorage.getItem("user");
      resolve(user);
    }, 3000)
  );

export const router = createBrowserRouter(
  createRoutesFromElements(
    <Route
      element={<AuthLayout />}
      loader={() => defer({ userPromise: getUserData() })}
    >
      <Route element={<HomeLayout />}>
        ...
      </Route>

      <Route path="/dashboard" element={<ProtectedLayout />}>
        ...
      </Route>
    </Route>
  )
);

```

在`AuthLayout`组件中，您可以使用`useLoaderData`钩子访问`userPromise`。

`Await`组件可以通过内置的错误处理机制呈现延迟值。组件`Await`应该被[包装在 React 悬念](https://blog.logrocket.com/react-suspense-data-fetching/)中，以启用回退 UI。在这种情况下，我们渲染一个线性进度条，直到`userPromise`被解决。

如果承诺被拒绝，我们可以向`errorElement` prop 传递一个组件来呈现一个错误的 UI 状态。

最后，我们可以将用户数据作为初始值传递给`AuthProvider`:

```
import { Suspense } from "react";
import { useLoaderData, useOutlet, Await } from "react-router-dom";
import LinearProgress from "@mui/material/LinearProgress";
import Alert from "@mui/material/Alert";
import { AuthProvider } from "../hooks/useAuth";

export const AuthLayout = () => {
  const outlet = useOutlet();

  const { userPromise } = useLoaderData();

  return (
    <Suspense fallback={<LinearProgress />}>
      <Await
        resolve={userPromise}
        errorElement={<Alert severity="error">Something went wrong!</Alert>}
        children={(user) => (
          <AuthProvider userData={user}>{outlet}</AuthProvider>
        )}
      />
    </Suspense>
  );
};

```

要验证错误情况，您可以拒绝如下所示的`Promise`:

```
// for error
const getUserData = () =>
  new Promise((resolve, reject) =>
    setTimeout(() => {
      reject("Error");
    }, 3000)
  );

```

您可以在这个 [CodeSandbox](https://codesandbox.io/s/react-router-v6-auth-demo-updated-t28l48) 中查看与数据库集成的完整代码。

## 结论

值得花一些时间来更好地理解 React Router v6 是如何工作的，特别是对于用户认证的常见用例。

[React 路由器 v6 是对之前版本的巨大改进](https://blog.logrocket.com/react-router-v6-future-reach-router/)。它快速、稳定、可靠。除了更容易使用之外，它还有许多新功能，如`<Outlet />`和一个改进的`<Route />`组件，这大大简化了 React 应用程序中的路由。

有了 v6.4 中可用的新路由器和数据 API，您可以轻松处理乐观 UI、挂起和错误状态。您可以在组件外部抽象和加载数据，同时显示回退用户界面，直到数据准备就绪。

我希望本指南对您有所帮助，并且现在对如何使用 React Router v6 处理用户认证有了更好的理解。

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