# 使用历史 API 构建 JavaScript 路由器

> 原文：<https://blog.logrocket.com/building-a-javascript-router-using-history-api/>

在本文中，我们将构建一个客户端路由系统。客户端路由是一种路由类型，用户在应用程序中导航时，即使页面的 URL 发生变化，也不会重新加载整个页面，而是显示新的内容。

为了构建这个，我们需要一个简单的服务器来服务我们的`index.html`文件。准备好了吗？我们开始吧。

首先，设置一个新的 node.js 应用程序并创建项目结构:

```
npm init -y
npm install express morgan nodemon --save
touch server.js
mkdir public && cd public
touch index.html && touch main.js file
cd ..

```

`npm init`命令将为我们的应用程序创建一个`package.json`文件。我们将安装`Express`和`Morgan`，它们将用于运行我们的服务器和记录我们的路线。

我们还将创建一个`server.js`文件和一个公共目录，我们将在其中写入我们的视图。一旦我们对文件做了任何更改，Nodemon 将重启我们的应用程序。

## 设置服务器

让我们使用 Express 通过修改`server.js`文件来创建一个简单的服务器:

```
const express = require('express');
const morgan = require('morgan');
const app = express();

app.use(morgan('dev'));
app.use(express.static('public'))

app.get('*', (req, res) => {
    res.sendFile(__dirname + '/public/index.html')
})
app.listen(7000, () => console.log("App is listening on port 7000"))

```

现在我们可以通过运行`nodemon server.js`来启动我们的应用程序。让我们为 HTML 创建一个简单的样板文件:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>Javascript Routing</h1>
    <div id="app">
    </div>

    <script src="main.js"></script>
</body>
</html>

```

这里，我们将链接`main.js`文件，以便我们可以在任何时间点操作 DOM。

## 实施路由系统

让我们转到`main.js`文件，写下我们所有的路由器逻辑。我们所有的代码都将被包装在`window.onload`中，这样它们只在网页完全加载了所有内容后才执行脚本。

接下来，我们将创建一个路由器实例，它是一个带有两个参数的函数。第一个参数是路线的名称，第二个参数是一个数组，它包含了我们定义的所有路线。这个路由将有两个属性:路由的名称和路由的路径。

```
window.onload = () => {
// get root div for rendering
    let root = document.getElementById('app');

  //router instance
    let Router = function (name, routes) {
        return {
            name,
            routes
        }
    };

 //create the route instance
    let routerInstance = new Router('routerInstance', [{
            path: "/",
            name: "Root"
        },
        {
            path: '/about',
            name: "About"
        },
        {
            path: '/contact',
            name: "Contact"
        }
    ])

}

```

我们可以获得页面的当前路径，并显示基于该路径的模板。返回页面的当前路径，我们可以将这段代码用于我们的 DOM:

```
 let currentPath = window.location.pathname;
    if (currentPath === '/') {
        root.innerHTML = 'You are on Home page'
    } else {
        // check if route exist in the router instance 
        let route = routerInstance.routes.filter(r => r.path === currentPath)[0];
        if (route) {
            root.innerHTML = `You are on the ${route.name} path`
        } else {
            root.innerHTML = `This route is not defined`
        }
    }

```

我们将使用`currentPath`变量来检查 route 实例中是否定义了路由。如果路由存在，我们将呈现一个简单的 HTML 模板。如果没有，我们将在页面上显示`This route is not defined`。

请随意显示您选择的任何形式的错误。例如，如果路由不存在，您可以让它重定向回主页。

### 添加路由器链接

为了浏览页面，我们可以添加路由器链接。就像使用 [Angular](https://angular.io/) 一样，您可以传递一个`routerLink`，它将具有您想要导航到的路径的值。为了实现这一点，让我们添加一些链接到我们的`index.html`文件:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <nav>
        <button router-link="/">Home</button>
        <button router-link="/about">About</button>
        <button router-link="/contact">Contact</button>
        <button router-link="/unknown">Error</button>
    </nav>
    <h1>Javascript Routing</h1>
    <div id="app">
    </div>

    <script src="main.js"></script>
</body>
</html>

```

注意我们传入的`router-link`属性——这是我们将用于路由的属性。

我们将创建一个存储所有`router-link`的变量，并将其存储在一个数组中:

```
let definedRoutes = Array.from(document.querySelectorAll('[router-link]'));

```

在一个数组中存储了我们的路由器链接之后，我们可以遍历它们并添加一个 click 事件监听器来调用`navigate()`函数:

```
 //iterate over all defined routes
    definedRoutes.forEach(route => {
        route.addEventListener('click', navigate, false)
    })

```

### 定义导航功能

导航功能将使用 [Javascript 历史 API](https://developer.mozilla.org/en-US/docs/Web/API/History_API) 来导航。`history.pushState()`方法向浏览器的会话历史堆栈添加一个状态。

单击该按钮时，我们将接收该按钮的路由器链接属性，然后使用`history.pushState()`导航到该路径，然后更改呈现的 HTML 模板:

```
  // method to navigate
    let navigate = e => {
        let route = e.target.attributes[0].value;

        // redirect to the router instance
        let routeInfo = routerInstance.routes.filter(r => r.path === route)[0]
        if (!routeInfo) {
            window.history.pushState({}, '', 'error')
            root.innerHTML = `This route is not Defined`
        } else {
            window.history.pushState({}, '', routeInfo.path)
            root.innerHTML = `You are on the ${routeInfo.name} path`
        }
    }

```

如果一个导航链接有一个路由器链接没有在`routeInstance`中定义，它将把推送状态设置为`error`并在模板上呈现`This route is not Defined`。

接下来，您应该考虑将路线存储在一个单独的文件中，这使得代码更整洁，如果有任何错误也更容易调试。现在，创建一个`routes.js`文件，并将路由构造器和路由器实例提取到这个新文件中:

```
//router instance
let Router = function (name, routes) {
    return {
        name,
        routes
    }
};
let routerInstance = new Router('routerInstance', [{
        path: "/",
        name: "Root"
    },
    {
        path: '/about',
        name: "About"
    },
    {
        path: '/contact',
        name: "Contact"
    }
])

export default routerInstance

```

导出该文件可以让其他 JavaScript 文件访问它。我们可以将它导入我们的 main.js 文件:

```
import routerInstance from './routes.js'

```

这将抛出一个错误。要解决这个问题，请将 index.html 文件中的脚本标记修改为:

```
<script type="module" src="main.js"></script>

```

添加模块类型指定了哪些变量和函数可以在模块外部访问。

## 结论

理解如何用普通的 JavaScript 实现路由系统使开发人员更容易使用框架路由库，如 [Vue.js Router](https://router.vuejs.org/) 。我们这里的代码可以在单个页面应用程序中重用，这在没有框架的情况下是非常完美的。要获得源代码，请查看 [GitHub](https://github.com/Wisdom132/jsrouter) 。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.