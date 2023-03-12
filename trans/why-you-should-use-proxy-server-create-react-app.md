# 为什么应该在 Create React App - LogRocket Blog 中使用代理服务器

> 原文：<https://blog.logrocket.com/why-you-should-use-proxy-server-create-react-app/>

## 介绍

在许多情况下，开发人员在使用 Create React App (CRA)构建应用程序时，需要从后端使用 API。因为大多数客户端应用程序与后端应用程序托管在不同的服务器上，所以跨两个服务器发送请求会带来复杂性。CRA 支持通过代理来减轻或完全消除这些问题。

在本文中，我们将讨论什么是代理服务器，为什么应该在开发中使用代理服务器，以及在 React 应用程序中代理请求的用例。然后，我们将实现一个在 React 中设置代理服务器的真实例子。

### 内容

## 什么是代理服务器？

代理服务器是一个系统或应用程序，当它请求资源时，充当客户端应用程序和服务器之间的管道。

![Diagram of a proxy server](img/acc24bb3c6ce24860e4003fd15e57224.png)

上图说明了代理服务器是如何工作的。当您向服务器发出请求时，它会通过代理进行路由，代理接收来自服务器的响应并将信息传输回客户端。

## 为什么在开发中应该使用代理服务器

您可能会问，在将请求发送到服务器之前，为什么有必要通过代理路由这些请求。下列情况需要使用代理服务器:

需要保护您的客户端免受跨站点攻击？代理服务器允许在将服务器响应传递给客户端之前对其进行验证，从而增加了额外的保护级别。它们可以用作网络过滤器和防火墙。

为了避免崩溃，必须节省带宽，并且必须控制服务器流量。代理服务器可以缓存请求和服务器响应，这样就不会将所有查询同时传输到服务器。这通常是在处理静态信息和大型文件时完成的。

当客户端数据必须受到保护或者必须匿名发出请求时，代理服务器非常有用。代理服务器不向服务器提供信息，如客户端的位置或 IP 地址。因为请求不是在已知网络上发送的，这有助于客户通过防止黑客截取重要信息来保护他们的数据隐私。

此外，代理服务器可以指定允许哪些请求，不允许哪些请求。最后，代理服务器可以用来记录请求以及在需要它的组织内发出请求的客户机。

## React 应用程序中代理请求的用例

在理解了为什么需要代理服务器之后，理解可以实现代理服务器的真实场景是至关重要的。下面列出了 React 应用程序中代理请求的一些例子。

首先是请求开发中的 HTTPS API(`[https://localhost](https://localhost)`)。浏览器有跨来源限制，阻止来自运行在本地主机上的 HTTP 站点的 HTTPS API 请求。代理服务器可以帮助将请求重定向到 API，而不必通过浏览器的默认请求选项，这有助于规避一些跨源限制。

第二是从跨来源的 API 获取数据，这些 API 不支持 web 浏览器中的 CORs。出于安全考虑，某些 API(不管超文本传输协议如何)不支持 CORs，并且浏览器强制实施同源策略来保护用户免受跨站点脚本攻击(XSS)。代理服务器可用于进行 API 调用，然后将结果传输到 web 浏览器。

最后，从服务器获取媒体资源。代理服务器可用于缓存来自服务器的媒体文件，无需在每次需要时向服务器发送对这些文件的请求。

## 如何在 React 中设置代理服务器

在使用 CRA 开发的 React 应用程序中，有两种设置代理服务器的常见方法。我们有默认的 CRA 代理选项以及 npm 包[http-代理-中间件](https://www.npmjs.com/package/http-proxy-middleware)。

> 请注意，这些方法只在开发中有效。我建议遵循以下指南在生产中实现代理:

首先，我们将使用 CRA 设置应用程序，这样我们就可以设置代理服务器。

### 创建 React 应用程序设置

确保您的计算机上安装了节点以使用 CRA。然后，在新目录中，启动终端并运行以下命令:

```
npx create-react-app proxyclient && cd proxyclient

```

上面的命令将创建一个名为`proxyclient`的新目录，并将终端目录设置为`/proxyclient`。文件夹中应该有以下文件夹和文件:

![Proxyclient folder structure](img/b2b064a31b12cf544220860109419aaf.png)

`package.json`文件也应该有类似的结构:

```
{
  "name": "proxyclient",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@testing-library/jest-dom": "^5.16.2",
    "@testing-library/react": "^12.1.3",
    "@testing-library/user-event": "^13.5.0",
    "react": "^17.0.2",
    "react-dom": "^17.0.2",
    "react-scripts": "5.0.0",
    "web-vitals": "^2.1.4"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": [
      "react-app",
      "react-app/jest"
    ]
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  }
}

```

编辑`src`文件夹中的`App.js`文件，以包含以下代码:

```
import React, { useEffect } from 'react'
const App = () => {
  useEffect(() => {
    fetch('https://google.com/search?q=javascript')
      .then(resp => {
        console.log(resp);
        console.log('======success=======');
      })
      .catch(err => {
        console.log('======failure=======');
        console.log(err);
      });
  }, [])
  return (
    <div>App</div>
  )
}
export default App

```

上面，`fetch`函数用于向跨源 URL `[https://google.com/search?q=javascript](https://google.com/search?q=javascript)`发出请求。

然后应该启动应用服务器，以便我们可以查看响应。在项目目录的终端中运行以下命令来启动应用服务器:

```
npm start

```

默认情况下，上述命令将开始监听`localhost`或`127.0.0.1`上的端口 3000。如果您打开浏览器并键入“localhost:3000”，您应该会在控制台中看到类似这样的内容:

![error message](img/04406ee67426c6ac59bb801dcc567941.png)

### 使用 CRA 默认代理

为了避免这个问题，我们将通过在`package.json`文件中包含一个`proxy`字段来定义 CRA 的默认代理，如下所示:

```
{
  ...,
  "proxy": "https://www.google.com",
  ...
}

```

然后，在`App.js`文件中，像这样更改获取请求:

```
fetch('/search?q=proxy')

```

上面，我们删除了源，并把请求 URL 构造成一个同源请求。请求应该成功，如浏览器控制台所示。它应该输出类似这样的内容:

![success message](img/90a7d9cadfb753b808c3975a60a93aa2.png)

注意响应中的 URL 作为同源传到浏览器，结果`[http://localhost:3000/search?q=proxy](http://localhost:3000/search?q=proxy)`，CORs 错误没了。
浏览器接收请求并将其传输到代理服务器，然后代理服务器将请求重定向到`package.json`文件中指定的实际 API 服务器。

我们还可以通过指定应该用来定位特定域的路由来向几个服务器发出请求。为了实现这一点，我们将像这样构造`package.json`文件中的`proxy`字段:

```
{
  ...,
  "proxy": {
    "/search" : {
      "target" : "https://www.google.com",
      "changeOrigin" : true
    },
    "/jobs" : {
      "target" : "https://stackoverflow.com",
      "changeOrigin" : true
    }
  }
  ...
}

```

上面，我们定义了两个`paths` : `/search`和`/jobs`，它们针对两个独立的域。

当我们在`/jobs`执行获取请求时，浏览器以`[http://localhost:3000/jobs](http://localhost:3000/jobs)`的形式发送请求，但是代理服务器以`[https://stackoverflow.com/jobs](https://stackoverflow.com/jobs)`的形式转发请求。

## 在 Create React 应用程序中使用手动创建的代理

如果你不想使用默认的 CRA 代理，你可以使用`http-proxy-middleware` npm 包。只需注意，该功能在`[[email protected]](/cdn-cgi/l/email-protection)`或更高版本中可用。

此包必须作为项目依赖项安装，然后才能使用。为此，打开终端并在项目目录中执行以下命令:

```
npm i http-proxy-middleware --save

```

确保在`package.json`文件中没有定义代理，然后在`src`目录中创建一个名为`setupProxy.js`的新文件。将以下代码片段添加到`setupProxy.js`文件中:

```
const { createProxyMiddleware } = require('http-proxy-middleware');
const proxy = {
    target: 'https://www.google.com',
    changeOrigin: true
}
module.exports = function(app) {
  app.use(
    '/search',
    createProxyMiddleware(proxy)
  );
};

```

上面的代码片段将一个函数导出到应用程序，以便开发服务器一启动，代理中间件就注册到应用程序，而不需要将其导入到任何文件中。一旦应用程序启动，它将自动启动。

我们使用之前安装的`http-proxy-middleware`中的`createProxyMiddleware`为`/search`注册一个代理目标。

在有几个代理目标的情况下，我们简单地如下调用`app.use`函数:

```
const { createProxyMiddleware } = require('http-proxy-middleware');
const proxy = {
    target: 'https://www.google.com',
    changeOrigin: true
}
const proxy2 = {
    target: 'https://www.stackoverflow.com',
    changeOrigin: true,
}
module.exports = function(app) {
  app.use(
    '/search',
    createProxyMiddleware(proxy)
  );

  app.use(
    '/jobs',
    createProxyMiddleware(proxy2)
  );
};

```

上面，我们在创建了另一个名为`proxy2`的对象之后调用了`app.use`方法，该对象包含了另一个目标域的代理参数，以便构造一个代理中间件。

# 结论

当您在客户端应用程序中使用代理服务器时，您可能会期望更好的安全性、机密请求、受管制的请求以及其他好处。现在你知道了为什么以及如何在 CRA 中使用 dev 代理服务器，我鼓励你在下一个项目中使用它。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

请在下面的评论区提出问题或建议。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)