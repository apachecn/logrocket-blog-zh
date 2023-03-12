# 如何在 React - LogRocket 博客中实现 Keycloak 认证

> 原文：<https://blog.logrocket.com/implement-keycloak-authentication-react/>

## 介绍

在构建现代应用程序时，提供安全的用户身份验证和管理有时是一项艰巨的任务。这就是为什么大多数开发人员更喜欢将问题交给第三方认证服务，如 Okta、Auth0 或 Keycloak。

在本教程中，您将学习如何使用 Keycloak 保护您的 React 应用程序。Keycloak 是一款开源身份和访问管理工具，用于为现代应用和服务添加身份认证。

使用 Keycloak，您可以轻松地设置应用程序的登录/注销、受保护的路由、身份管理等，而无需您做太多工作。本教程将向您展示如何在 React 应用程序中使用受保护的路由实现简单的登录/注销功能。

尽情享受吧！

## 入门指南

要轻松完成教程，请确保您具备以下条件:

*   Node.js 和 npm 在您的本地机器上工作(我使用最新的 Node.js 稳定版本 v16.13.0 创建了本教程)
*   JavaScript 的基础知识
*   安装在本地机器上的 Docker

### 安装 Keycloak 服务器

有很多方法可以在你的本地机器上设置一个 Keycloak 服务器。然而，在本教程中，我们将使用 Keycloak Docker 映像，因为它实现起来更容易、更快。

首先，打开您的终端并运行以下命令来确认您的 Docker 安装。

```
$ docker version 

Client:
 Cloud integration: v1.0.20
 Version:           20.10.10
 API version:       1.41
 Go version:        go1.16.9
 Git commit:        b485636
 Built:             Mon Oct 25 07:43:15 2021
 OS/Arch:           darwin/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.10
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.16.9
  Git commit:       e2f740d
  Built:            Mon Oct 25 07:41:30 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.11
  GitCommit:        5b46e404f6b9f661a205e28d59c982d3634148f8
 runc:
  Version:          1.0.2
  GitCommit:        v1.0.2-0-g52b36a2
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

如果您没有在本地安装 Docker，您可以按照这个[完整的 Docker 安装指南](https://blog.logrocket.com/node-js-docker-improve-dx/)在您的 Windows、macOS 或 Linux 机器上安装 Docker。

确认 Docker 安装后，下一步将是设置一个 Keycloak 服务器。要在本地计算机上启动 Keycloak 服务器，请在终端中运行以下命令:

```
$ docker run -p 8080:8080 -e KEYCLOAK_USER=<username> -e KEYCLOAK_PASSWORD=<password> quay.io/keycloak/keycloak:15.0.2
```

将`<username>`更改为您首选的 Keycloak 管理员用户名，将`<password>`更改为您首选的管理员密码。这两个输入是登录服务器所必需的。

成功安装后，管理服务器将在`[http://localhost:8080/auth/admin](http://localhost:8080/auth/admin)`打开。使用您的用户名和密码登录。您应该可以访问一个类似于下面这样的服务器:
![The Keycloak admin server you should gain access to upon successful login](img/879ce4e42cc31651ba75f5c588a9cc32.png)

### 设置钥匙锁领域

Keycloak 设置的下一步是创建一个领域。[根据键盘锁](https://www.keycloak.org/docs/latest/server_admin/#:~:text=that%20group%20defines.-,realms,can%20only%20manage%20and%20authenticate%20the%20users%20that%20they%20control.,-clients):

> 一个领域管理一组用户、凭据、角色和组。用户属于并登录到一个领域。领域相互隔离，只能管理和验证它们控制的用户。

默认情况下，Keycloak 附带一个名为`master`的领域。这是专门用于管理 Keycloak 的，不应该被篡改。

要创建新领域，请执行以下步骤:

1.  登录您的 Keycloak 仪表盘
2.  将鼠标悬停在侧边栏上的**主**下拉菜单上，会出现一个带有**添加领域**的菜单
    ![The Add Realm button in the Master dropdown](img/b8f69484b42b670dfdf2020c8fe0152b.png)
3.  点击**添加领域**，输入您的领域名称，点击**创建**创建新领域
    ![Create your new realm](img/2bcc8da8ca67fae1568a8ab304beea95.png)

### 设置键盘锁用户

创建领域后，下一步是向其中添加用户。在管理控制台中执行以下步骤:

1.  点击侧边菜单上的**用户**，在出现的新窗口
    ![Add a new user in Keycloak](img/8fdae20cb0be0b988547f33a0c5143e7.png)中选择**添加用户**
2.  填写所需的详细信息，在上将**电子邮件验证**设置为**，点击**保存**登记变更
    ![Add the user's details](img/b8e48da79dd3b851ef180f8199bf86d4.png)**
3.  在出现的新窗口中点击**凭证**，输入并确认用户密码。
4.  将**临时**切换到**关闭**，点击**设置密码**注册新密码。
    ![Register the new user's password](img/eed2a49fd97bd8e708c90c864640c8cb.png)

让我们测试一下我们的新用户是否在工作。退出管理控制台并访问`[http://localhost:8080/auth/realms/<realm-name>/account/](http://localhost:8080/auth/realms/<realm-name>/account/)` —您应该将`<realm-name>`更改为创建的用户的领域名。

你应该会看到这样一个页面:
![Sign in to your Keycloak account](img/e86bf6ae215093f5f28d6cdb26c3033f.png)

点击**登录**，输入新用户的用户名和密码。成功登录后，您应该可以访问类似于以下内容的页面:
![Keycloak account management page upon successful registration](img/1e237045c7baab74c0c1f40a13be0dda.png)

## 将 React 应用程序添加到 Keycloak

Keycloak 服务器设置的最后一步是将 React 前端应用程序注册为客户端。为此，我们希望登录到管理控制台，在侧边栏菜单中导航到**客户端**页面，然后单击**创建**。
![The Clients page in Keycloak](img/45323260412f040b55aa938cce162725.png)

添加客户端名称，并将**根 URL** 设置为`[http://localhost:3000/](http://localhost:3000/)`。
![Add the client's name and set the root URL](img/aeefbbc90562c4c28dc4764d3340a65c.png)

这样，我们的 Keycloak 服务器就完全设置好了！我们现在可以移到前端，完成我们的集成。

## 设置我们的 React 前端

首先，创建一个新的 React 应用。

```
npx create-react-app <app-name>
cd <app-name>
npm start
```

将`<app-name>`更改为您喜欢的应用程序的名称。我们将使用应用程序的造型顺风 CSS 如果你需要的话，你可以按照这些说明来用 Create React App[安装和设置 Tailwind CSS](https://blog.logrocket.com/tailwind-css-configure-create-react-app/) 。

最后，为了完成设置，安装`keycloak-js`、`@react-keycloack/web`和`react-router-dom`。

```
npm install --save keycloak-js @react-keycloak/web react-router-dom
```

Keycloak 和`@react-keycloak/web`将用于 React-Keycloak 实现，而 React Router DOM 将用于创建我们的页面。请注意，本教程是使用 [React 路由器 v6](https://blog.logrocket.com/react-router-v6/) 创建的。

接下来，将两个名为`components`和`pages`的文件夹添加到您的`src`文件夹中。在`pages`文件夹中添加一个`Homepage.js`文件和一个`Securedpage.js`文件，在`components`文件夹中添加一个`Nav.js`文件。

将以下代码添加到`components/Nav.js`文件中:

```
import React from "react";

const Nav = () => {
 return (
   <div>
     <div className="top-0 w-full flex flex-wrap">
       <section className="x-auto">
         <nav className="flex justify-between bg-gray-200 text-blue-800 w-screen">
           <div className="px-5 xl:px-12 py-6 flex w-full items-center">
             <h1 className="text-3xl font-bold font-heading">
               Keycloak React AUTH.
             </h1>
             <ul className="hidden md:flex px-4 mx-auto font-semibold font-heading space-x-12">
               <li>
                 <a className="hover:text-blue-800" href="/">
                   Home
                 </a>
               </li>
               <li>
                 <a className="hover:text-blue-800" href="/secured">
                   Secured Page
                 </a>
               </li>
             </ul>
             <div className="hidden xl:flex items-center space-x-5">
               <div className="hover:text-gray-200">
                 <h1>Login</h1>
               </div>
             </div>
           </div>
         </nav>
       </section>
     </div>
   </div>
 );
};

export default Nav;
```

并将以下内容添加到`pages/Homepage.js`文件中。

```
import React from 'react';

const Home = () => {

 return (
   <div>
     <h1 className="text-green-800 text-4xl">Welcome to the Homepage</h1>
   </div>
 );
};

export default Home;
```

将以下内容添加到`pages/Securedpage.js`文件中。

```
import React from 'react';

const Secured = () => {

 return (
   <div>
     <h1 className="text-black text-4xl">Welcome to the Protected Page.</h1>
   </div>
 );
};

export default Secured;
```

用以下代码更新您的`App.js`代码。

```
import React from "react";
import { BrowserRouter, Route, Routes } from "react-router-dom";
import Nav from "./components/Nav";
import WelcomePage from "./pages/Homepage";
import SecuredPage from "./pages/Securedpage";

function App() {
 return (
   <div>
     <Nav />
     <BrowserRouter>
       <Routes>
         <Route exact path="/" element={<WelcomePage />} />
         <Route path="/secured" element={<SecuredPage />} />
       </Routes>
     </BrowserRouter>
   </div>
 );
}

export default App;
```

前端文件结构应该类似于这样:
![Our frontend's file structure](img/346f72878295ea87fe803364852ccd2c.png)

现在，启动 React 服务器。您应该会被定向到一个类似于以下内容的页面:
![Keycloak secure page after our React server is started](img/5bd693182c821bdb0e200ecccee8c871.png)

## 在 React 中设置键盘锁

React 前端完全实现后，下一步是在 React 项目中配置 Keycloak。

在您的`src`文件夹中创建一个名为`Keycloak.js`的文件，并向其中添加以下代码。

```
import Keycloak from "keycloak-js";
const keycloak = new Keycloak({
 url: "http://localhost:8080/auth",
 realm: "Keycloak-react-auth",
 clientId: "React-auth",
});

export default keycloak;
```

`url`是 Keycloak 服务器的 URL，`realm`是您创建的域名，`clientId`是创建的客户端的名称。将您的`App.js`代码更新为:

```
import React from "react";
import { ReactKeycloakProvider } from "@react-keycloak/web";
import keycloak from "./Keycloak"
import { BrowserRouter, Route, Routes } from "react-router-dom";
import Nav from "./components/Nav";
import WelcomePage from "./pages/Homepage";
import SecuredPage from "./pages/Securedpage";

function App() {
 return (
   <div>
     <ReactKeycloakProvider authClient={keycloak}>
       <Nav />
       <BrowserRouter>
         <Routes>
           <Route exact path="/" element={<WelcomePage />} />
           <Route path="/secured" element={<SecuredPage />} />
         </Routes>
       </BrowserRouter>
       </ReactKeycloakProvider>
   </div>
 );
}

export default App;
```

这段代码导入了`<ReactKeycloakProvider />`，并用提供者包装了整个应用程序。我们还添加了我们的`keycloak.js`文件作为道具。

## 设置反应`ProtectedRoute`

在这一步中，我们将创建一个只有经过身份验证的用户才能访问的受保护路由。创建一个`helpers`文件夹，并向其中添加一个`PrivateRoute.js`文件。将以下代码添加到`helpers/PrivateRoute.js`文件中。

```
import { useKeycloak } from "@react-keycloak/web";

const PrivateRoute = ({ children }) => {
 const { keycloak } = useKeycloak();

 const isLoggedIn = keycloak.authenticated;

 return isLoggedIn ? children : null;
};

export default PrivateRoute;

```

这段代码检查试图访问受保护路由的用户是否经过身份验证，如果用户经过身份验证，则显示受保护路由，如果用户未经身份验证，则不显示任何内容。

Keycloak 的 JavaScript 适配器提供了对一些附加属性的访问，以保护您的应用程序，比如`authenticated`属性，我们将使用它来检查用户是否经过身份验证。您可以[在 Keycloak 文档中查看其他可用属性](https://www.keycloak.org/docs/latest/securing_apps/index.html#properties)。

更新您的`App.js`文件中的路线。我们用我们创建的受保护路由包装了我们的`SecuredPage`路由；这将确保`SecuredPage`的内容只能被认证的个人访问。

```
import React from "react";
import { ReactKeycloakProvider } from "@react-keycloak/web";
import keycloak from "./Keycloak";
import { BrowserRouter, Route, Routes } from "react-router-dom";
import Nav from "./components/Nav";
import WelcomePage from "./pages/Homepage";
import SecuredPage from "./pages/Securedpage";
import PrivateRoute from "./helpers/PrivateRoute";

function App() {
 return (
   <div>
     <ReactKeycloakProvider authClient={keycloak}>
       <Nav />
       <BrowserRouter>
         <Routes>
           <Route exact path="/" element={<WelcomePage />} />
           <Route
             path="/secured"
             element={
               <PrivateRoute>
                 <SecuredPage />
               </PrivateRoute>
             }
           />
         </Routes>
       </BrowserRouter>
     </ReactKeycloakProvider>
   </div>
 );
}

export default App;

```

现在，我们需要给[导航栏](https://blog.logrocket.com/creating-navbar-react/)添加一个登录/注销按钮。将`components/Nav.js`文件中的代码片段更新如下:

```
import React from "react";
import { useKeycloak } from "@react-keycloak/web";

const Nav = () => {
 const { keycloak, initialized } = useKeycloak();

 return (
   <div>
     <div className="top-0 w-full flex flex-wrap">
       <section className="x-auto">
         <nav className="flex justify-between bg-gray-200 text-blue-800 w-screen">
           <div className="px-5 xl:px-12 py-6 flex w-full items-center">
             <h1 className="text-3xl font-bold font-heading">
               Keycloak React AUTH.
             </h1>
             <ul className="hidden md:flex px-4 mx-auto font-semibold font-heading space-x-12">
               <li>
                 <a className="hover:text-blue-800" href="/">
                   Home
                 </a>
               </li>
               <li>
                 <a className="hover:text-blue-800" href="/secured">
                   Secured Page
                 </a>
               </li>
             </ul>
             <div className="hidden xl:flex items-center space-x-5">
               <div className="hover:text-gray-200">
                 {!keycloak.authenticated && (
                   <button
                     type="button"
                     className="text-blue-800"
                     onClick={() => keycloak.login()}
                   >
                     Login
                   </button>
                 )}

                 {!!keycloak.authenticated && (
                   <button
                     type="button"
                     className="text-blue-800"
                     onClick={() => keycloak.logout()}
                   >
                     Logout ({keycloak.tokenParsed.preferred_username})
                   </button>
                 )}
               </div>
             </div>
           </div>
         </nav>
       </section>
     </div>
   </div>
 );
};

export default Nav;

```

我们现在增加了一个新的登录/注销按钮。该代码检查用户是否通过身份验证，当用户通过身份验证时显示**登录**按钮，当用户未通过身份验证时显示**注销**按钮。

当点击**登录**按钮时，它调用 Keycloak 的`[Login method](https://www.keycloak.org/docs/latest/securing_apps/index.html#login-options)`对用户进行认证。当点击**注销**按钮时，它调用`[Logout method](https://www.keycloak.org/docs/latest/securing_apps/index.html#logout-options)`将用户注销。

当你访问我们创建的演示 React 网站时，导航栏上现在应该有一个**登录**按钮。此外，如果您尝试访问受保护的页面，它应该不会显示任何内容。


当你点击**登录**按钮时，你应该被重定向到一个 Keycloak 登录页面。


输入创建的用户详细信息进行登录，您将被自动重定向到该网站。成功登录后，**登录**按钮应该变成**注销**按钮，并且安全页面应该可以访问。
![The logged-in view of the secured page](img/6f288a861ce1bf3ea5fe5bf68714b6ed.png)

## 结论

在本教程中，我们使用 Keycloak 在 React 应用程序中实现了一个简单的受保护路由的登录/注销功能。我们用 Keycloak 的`authenticated`属性实现了私有路由，用 Keycloak 的`Login`和`Logout`方法实现了登录/注销功能。

您可以根据自己的需要轻松创建更多的自定义实现；如果你想了解更多，Keycloak 的 [JavaScript 适配器文档](https://www.keycloak.org/docs/latest/securing_apps/#_javascript_adapter)是一个很好的资源。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)