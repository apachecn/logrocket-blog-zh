# 使用 Socket.io - LogRocket 博客进行实时数据传输

> 原文：<https://blog.logrocket.com/real-time-data-transfer-with-socket-io/>

## 介绍

无论您是在构建聊天应用程序、视频共享应用程序，还是视频会议应用程序， [Socket.io](https://socket.io/) 都可以在您的客户端之间提供连接，从而在它们之间实现闪电般的数据传输。

它由客户端和服务器端组成，服务器端处理不同的连接和信息分散。要知道 Socket.io 不是 WebSocket，而是建立在其他实时协议之上的自定义实时传输协议实现。

本文涵盖了 Socket.io 的基础知识，从建立连接到将客户端连接到服务器端。

## 什么是 WebSocket？

WebSocket 是一种 web 通信协议，一旦在客户端和服务器端之间创建了通道，就可以将数据从客户端传输到服务器端(以及从服务器端传输到客户端)。这种连接一直持续到通道终止。与 HTTPS 协议不同，WebSocket 允许双向数据传输。

WebSockets 可以用来构建任何东西，从实时交易应用程序到聊天应用程序，或者任何需要高效、实时数据传输的东西。

## 什么是 sockets 和 Socket.io？

套接字是网络中给定点之间实现的连接。套接字中的连接一旦创建就持续存在，直到被终止。套接字连接可以是服务器到客户端、客户端到服务器或者两个客户端或服务器之间的连接。

[Socket.io](https://socket.io/) 是一个 JavaScript 库，工作方式类似于 WebSockets。数据传输是通过一个允许实时数据交换的开放连接来完成的。每个连接也称为套接字，由两部分组成:服务器端和客户端。Socket.io 利用 [Engine.io](https://github.com/socketio/engine.io) 实现服务器端与客户端 [Engine.io-client](https://github.com/socketio/engine.io-client) 的连接。

Socket.io 中的数据传输主要是以 JSON (JavaScript 对象表示法)的形式完成的，所以本文中我们将使用 JSON 格式。

## 安装和设置

因为 Socket.io 需要服务器端和客户端来实现连接，所以我们将从服务器端开始安装服务器和客户端，然后再安装客户端。

### 服务器端

确保您的系统中已经安装了 [Node.js](https://nodejs.org/en/) 和[节点包管理器](https://www.npmjs.com/) (npm)。如果没有，请访问 Node.js 网站了解更多关于安装的信息。

在命令行中，运行以下命令:

```
npm install express Socket.io 

```

这将安装必要的依赖项。

下一步是设置我们的服务器。继续要求 Socket.io、Express 和 HTTP 模块，如 index.js 文件中所示。我们将使用 HTTP 模块而不是 Express 来创建我们的服务器，以便轻松地将我们的服务器传递给 Socket.io:

```
const express = require("express");
const http = require("http");
const socketio = require("socket.io");

const app = express();

const PORT = process.env.PORT || 5000;

const server = http.createServer(app);

io = socketio(server);

server.listen(PORT, () => {
  console.log(`server running at port ${PORT}`);
});

```

如上所示，我们成功地创建了我们的服务器。我们可以通过运行以下命令来启动我们的服务器:

```
node index.js

```

我们的服务器应该在端口 5000 上启动并运行。注意，我们的服务器被传递给了 Socket.io。

### 客户端

对于客户端设置，让我们将 Socket.io 脚本导入我们的 index.html 文件:

```
<script src="/socket.io/socket.io.js"></script>

```

继续将您的客户端连接到服务器端，如下所示:

```
=let ENDPOINT = "http://localhost:5000"
let socket = io.connect(ENDPOINT);
=
```

或者，如果您正在使用 React，可以为您的客户端安装`socket.io-client`:

```
npm install socket.io-client

```

然后，您可以继续导入并创建连接，如下所示:

```
import React from "react";
import io from "socket.io-client";

let ENDPOINT = "http://localhost:5000"
let socket = io(ENDPOINT);

```

## 插座连接和断开

我们已经成功地设置了我们的服务器端和客户端。现在，一旦客户端连接上了，服务器端就会通知我们这个连接。Socket.io 帮助我们处理这种连接:

```
io.on("connection", (socket) => {
  // your code snippet 
})

```

这个事件配备了一个回调函数，在连接发生时被触发。

对于更复杂的应用程序，您可能希望为不同的用户组创建特定的房间。使用 Socket.io 可以轻松实现该功能。一旦实现连接，您可以使用 join 方法连接特定的房间:

```
io.on("connection", (socket) => {
  let roomID = 2436
  socket.join(roomID)

})

```

一旦发生断开连接，就会触发断开连接事件:

```
io.on("connection", (socket) => {
  socket.on("disconnect", () => {
        });
})

```

## 事件发射和广播

Socket.io 允许用户发出事件，并在另一端监听它们。服务器端和客户端都可能发生事件发射。例如，在连接期间，可以在客户端发出“加入”事件:

```
 socket.emit("join", { name: “peter paul”}, (err) => {
      if (err) {
        alert(err);
      }
    });

```

请注意，该事件是与 JSON 形式的有效负载一起发出的，其中包含当前加入连接的用户名。

继续在服务器端处理该事件:

```
socket.on("join", ({ name }, callback) => {
    if (error) {
      callback(error);
    } else {
      let roomID = 2436
      socket.join(roomID)
      console.log(name)
      });

```

您还可以选择在事件处理期间向特定房间中的每个人广播信息:

```
socket.on("join", ({ name }, callback) => {
    if (error) {
      callback(error);
    }
    else {
       let roomID = 2436
       socket.join(roomID)
       socket.broadcast.to(roomID).emit("adminMessage", {
             name: "admin",
             content: `${name} has joined`,
         });
      }
  });

```

此时，我们所有的服务器端代码应该是这样的:

```
const express = require("express");
const http = require("http");
const socketio = require("socket.io");

const app = express();

const PORT = process.env.PORT || 5000;

const server = http.createServer(app);

io = socketio(server);

io.on("connection", (socket) => {
   socket.on("join", ({ name }, callback) => {

    if (error) {
      callback(error);
    } else {
      let roomID = 2436
      socket.join(roomID)
      console.log(name)
       socket.broadcast.to(roomID).emit("adminMessage", {
             name: "admin",
             content: `${name} has joined`,
         });
      });

    socket.on("disconnect", () => {
         });
})

server.listen(PORT, () => {
  console.log(`server running at port ${PORT}`);
});

```

客户端的结构应该如下:

```
let ENDPOINT = "http://localhost:5000"
let socket = io.connect(ENDPOINT);

 socket.emit("join", { name: “peter paul”}, (err) => {
      if (err) {
        alert(err);
      }
    });

```

## 调试和日志记录

Socket.io 附带了一个非常强大的调试工具，叫做 [Debug](https://github.com/visionmedia/Debug) 。在引入 Debug 之前，Socket.io 求助于将所有事情记录到控制台，这让大多数用户觉得很烦。后来，他们采取了新的措施，默认不记录任何东西。

使用浏览器时，您可以通过提供调试环境变量或`localStorage.debug`属性来选择查看这些消息。

对于控制台，如下所示:

```
DEBUG=* node yourfile.js

```

`*`帮助您查看哪些信息是可用的。

对于浏览器，它是这样完成的:

```
localStorage.debug = '*';

```

## 结论

Socket.io 非常简单，已经成为开发人员构建需要实时数据传输的应用程序的首选。随着几乎所有现代浏览器都支持 WebSocket，Socket.io 有望变得更大。

现在你已经掌握了 Socket.io 的基础知识，我建议你使用与上面相同的概念做一个不同的项目，以提高你的技能，并对这个主题有更清晰的理解。

## [LogRocket](https://logrocket.com/signup/) :全面了解您的网络应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://logrocket.com/signup/)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。