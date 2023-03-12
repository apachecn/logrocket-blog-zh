# 2022 年 Node.js 的顶级 WebSocket 库

> 原文：<https://blog.logrocket.com/top-websocket-libraries-nodejs-2022/>

WebSockets 是一种计算机通信协议，它通过单个连接在双方之间提供数据和信息。WebSocket [s](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) 是为你的应用添加实时功能的好方法，彻底改变了我们所知的 web 开发空间。在本文中，我们将讨论你在 2022 年应该考虑的八个 Node.js WebSocket 库:

要开始使用这些库，您需要在本地机器上安装 npm 和 Node.js。在开始之前，让我们先了解一下 WebSockets 的一些重要背景。

## WebSockets 是如何工作的？

传统上，客户端和服务器按照请求/响应循环进行通信。然而， [WebSockets 允许您以非标准的方式在客户端和服务器](https://blog.logrocket.com/using-websockets-go/)之间进行通信。

WebSockets 在服务器和客户端之间提供了一个开放的连接，允许在服务器和客户端之间进行简单、即时的双向通信，而无需轮询服务器。WebSockets 对于构建实时应用程序特别有用，例如实时消息、实时流、推送通知、跟踪、金融更新和体育更新等等。

现在我们知道了 WebSockets 是如何工作的，让我们来看看几个 Node.js 库，您可以用它们来简化 WebSockets 的开发。

## 糖吗

SockJS 是一个 JavaScript 库，它以类似于本地 WebSockets API 的方式提供客户端和服务器之间的通信。 [SockJS](https://github.com/sockjs) 以 [sockjs-node](https://github.com/sockjs/sockjs-node) 和 [SockJS-client](https://github.com/sockjs/sockjs-client) 的形式与服务器对等体一起工作，SockJS-client 是一个 JavaScript 客户端库。

在撰写本文时，SockJS 在 GitHub 上拥有超过 7k 颗星，在 npm 上每周有近 700 万次下载，可以说是 Node.js 最好的 WebSocket 库之一。

## 《华盛顿明星报》

凭借 GitHub 上超过 [17.5k 的星级和 npm](https://github.com/websockets/ws) 上每周约 3500 万次的[下载，ws 是最受欢迎的 Node.js WebSocket 库之一。](https://www.npmjs.com/package/ws)

ws 是一个快速、易用、文档完善、经过全面测试的 WebSocket 客户端和服务器实现，它支持几乎所有的浏览器。因此，ws 是许多开发人员最喜欢的首选库，这意味着它的社区是活跃和健壮的。

安装 ws 非常简单，可以用以下命令之一完成:

```
npm install ws
//
npm i ws

```

要开始使用 ws WebSocket 库，[查看文档](https://github.com/websockets/ws#api-docs)。

```
npm install --save-optional bufferutil
```

上面的命令允许您有效地执行操作，比如屏蔽和取消屏蔽 WebSocket 帧的数据有效负载。

```
npm install --save-optional utf-8-validate
```

要有效地检查邮件是否包含有效的 UTF-8，请运行上面的命令。

## Socket.IO

[创建于 2010 年的插座。IO](https://socket.io/) 是一个流行的 WebSocket 库，被 Trello 和微软这样的大公司使用。插座。IO 是可靠的，提供负载平衡、支持二进制流、处理代理等特性。

插座。然而，IO 的主要用途是促进浏览器和服务器之间的实时、双向和基于事件的通信。像 ws，Socket。IO 有 Node.js 服务器和 [JavaScript 客户端库](https://github.com/socketio/socket.io-client)。

另外，插座。IO 包括支持在代理或负载平衡器存在的情况下建立的连接以及通过网络广播等功能。WebSocket 不支持这两种活动。

GitHub 上有近 55000 位明星， [npm weekly](https://www.npmjs.com/package/socket.io) 上有约 300 万次下载，Socket。2022 年，木卫一是一个值得关注的伟大图书馆。[文档](https://socket.io/docs/v4/)非常简单，这意味着即使是没有经验的开发人员也应该能够很快上手。

## Faye WebSocket

从 Faye 项目中提取的 [Faye WebSocket](https://github.com/faye/faye-websocket-node) 是一个多用途的 WebSocket，它使得在 Node.js 应用程序中命令预先存在的 WebSocket 连接变得容易。

Faye WebSocket 只提供标准的 [WebSocket API](https://html.spec.whatwg.org/multipage/web-sockets.html#network) 和在 Node 中构建 WebSocket 客户端和服务器所必需的类。它不提供服务器本身，也不提供任何其他抽象。

要安装 Faye WebSocket，运行`npm install faye-WebSocket`。Faye 项目的 [README](https://github.com/faye/faye-websocket-node#readme) 部分提供了入门的必要文档。

## 套接字集群

[SocketCluster](https://socketcluster.io/) 是一个高度可扩展的框架和实时 HTTP 服务器引擎，使您能够一次使用所有可用的 CPU 内核构建多进程、实时服务器。使用 SocketCluster，后端可以避免将 Node.js 服务器作为单线程运行，从而提高工作效率并保持弹性。

SocketCluster 框架通过发布/订阅通道支持客户端-服务器和组通信。它使用局域网(LAN)来连接集群的组件。SocketCluster 经过优化，可以轻松地从故障或断开连接中恢复，并且不会丢失数据。

在撰写本文时，SocketCluster 在 GitHub 上有近 6000 颗星，在 T2 NPM 周刊上有 7000 次下载，

## 吊坠

Sockette 是一个 WebSocket 包装器，它允许你重用实例，而不必多次声明所有的事件监听器。Sockette 是一个非常轻量级的库，根据 GitHub 页面，只有大约 367 字节。如果连接丢失，Sockette 会自动重新连接。

考虑到 Sockette 是一个小工具，最好在初始化时声明所有的事件侦听器。要安装 Sockette 并开始使用，请运行以下命令:

```
npm install -- save sockette

```

在撰写本文时，Sockette 在 GitHub 上有大约 2k 颗星，在 npm 上有 [9k 周下载量](https://www.npmjs.com/package/sockette)。[文档](https://www.npmjs.com/package/sockette)是一个可以依靠的很好的资源。

## 羽毛

[羽毛框架](https://feathersjs.com/)使用插座。IO 模块，以便您实时接收和提供更新。它还通过套接字、灵活的插件、RESTful API 和资源让您控制数据。

[Feathers 提供了自动化和控制的终极结合](https://blog.logrocket.com/lightning-fast-apis-feathersjs/)。有了 Feathers，您可以完全控制 API 的行为，同时还能从提供的工具中获益。

在写这篇文章的时候，这个项目在 GitHub 上有将近 14k 的星星，在 npm 上有 26k 的每周下载量。Feathers 还包括一个优秀的[文档](https://docs.feathersjs.com/)，并且以轻量级和易于与不同数据库集成而闻名。

要安装 Feathers.js 库，请运行以下命令:

```
npm install -g @feathersjs/cli

```

## 结论

在本文中，我们回顾了在 Node.js 中使用 WebSocket 的八个库，涵盖了每个库的特性、流行程度和文档。我们还介绍了 WebSockets 如何在客户机和服务器之间提供实时通信的一些背景知识。在 2022 年构建 Node.js 应用程序时，这些库值得关注。我希望你喜欢这篇文章！

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.