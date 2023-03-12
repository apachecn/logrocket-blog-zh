# 在 Next.js 中实现 WebSocket 通信

> 原文：<https://blog.logrocket.com/implementing-websocket-communication-next-js/>

网络上的实时通信使用 [WebSocket](https://blog.logrocket.com/websockets-tutorial-how-to-go-real-time-with-node-and-react-8e4693fbf843/) API，这是一个通过单一 TCP 连接的全双工通信通道。这使得多个客户端连接到一个服务器并共享实时数据成为可能，从而实现各种体验，如多人游戏、交互式文档或聊天室。

## Socket.io 是什么？

Node.js 最著名的 WebSocket 包装器是 [Socket.io](https://socket.io/) 。它是一个包，包含一个 Node.js 服务器和一个在浏览器上使用的客户端库。在本文中，我将使用 Socket.io 来展示服务器和 Next.js 应用程序之间的 WebSocket 连接。

需要注意的是，Vercel 上的[无服务器功能](https://vercel.com/docs/concepts/functions/introduction)不支持 WebSockets。尽管 Vercel 并不是部署 Next.js 应用程序的唯一选择，但这一限制可能会成为一些人的绊脚石。

Vercel 建议使用[第三方解决方案](https://vercel.com/docs/concepts/solutions/realtime)进行部署到其平台的实时通信。他们还提供了一个[指南](https://vercel.com/guides/deploying-pusher-channels-with-vercel)，用于集成这些第三方解决方案中的一个，称为 Pusher。

我们将从使用 [Create Next App](https://nextjs.org/docs/api-reference/create-next-app) 包创建 Next.js 应用程序开始。

运行`npx create-next-app websocket-example`，其中`websocket-example`是项目的名称。这将生成一个样板应用程序。

## 创建服务器端

当您导航到项目文件夹时，您将看到`pages/api`文件夹。这是我们创建客户端将使用的端点的地方。任何在`pages/api`下的文件都被视为映射到`/api/*`的端点。

在`pages/api`下创建一个`socket.js`文件。该文件将通过确保只有一个 WebSocket 实例来处理 WebSocket 连接，我们将通过向客户端上的`/api/socket`发出请求来访问该实例。

默认情况下，Next.js 有自己的服务器，除非需要定制服务器，否则不需要任何配置。对于这个基本的 WebSocket 示例，我们不需要配置定制服务器。

在`socket.js`文件中，我们需要检查 HTTP 服务器中是否已经打开了一个套接字连接。如果没有，我们插 Socket.io。

正如您可能已经知道的那样，为了使 API 路由工作，Next.js 需要一个默认导出的函数，该函数具有分别对应于`http.IncomingMessage`和`http.ServerResponse`的`req`和`res`参数。

下面是一个 API 路由的例子。

```
export default function handler(req, res) {}

```

现在，我们需要使用这种模式，在还没有套接字连接的地方初始化套接字连接。为了将 HTTP 服务器插入 Socket.io，我们使用了来自`Socket.IO`包的`Server`类。

在默认导出的函数中，我们对`res.socket.server.io`进行检查，默认情况下它不存在，并显示套接字连接不在适当的位置。如果`res.socket.server`上的这个`io`对象不存在，那么我们通过实例化一个`Server`来启动一个新的套接字连接，它将`res.socket.server`作为输入。

接下来，我们将返回的对象设置为`res.socket.server.io`，这样当实例化后有新的请求时，`res.socket.server.io`就不会是`undefined`。

在`if`语句之外，我们简单地结束请求，以防止它导致一个停滞的请求。

```
import { Server } from 'Socket.IO'

const SocketHandler = (req, res) => {
  if (res.socket.server.io) {
    console.log('Socket is already running')
  } else {
    console.log('Socket is initializing')
    const io = new Server(res.socket.server)
    res.socket.server.io = io
  }
  res.end()
}

export default SocketHandler

```

## 在客户端使用端点

到目前为止，我们已经创建了一个端点来初始化 WebSocket 连接。现在我们需要在客户端使用这个端点。

在`/pages`目录下，您会发现`index.js`文件，这是主目录的路径。在这个文件中，我们将实现一个带有输入字段的组件。然后，我们将把输入字段中的更改发送到服务器，并广播回所有其他连接的客户端。这是一个显示实时通信的基本示例。

我们需要在组件中做的第一件事是建立套接字连接。为此，我们将使用具有空数组依赖的`useEffect`钩子，它基本上相当于`componentDidMount`生命周期钩子。

`SocketInitializer`是一个异步函数，因为我们需要调用`/api/socket`端点来打开套接字连接。

一旦这个调用被解析，我们就初始化`io()`对象，并将其设置为函数范围之外的一个变量，这样它在组件的整个生命周期中都保持不变。

然后，我们使用`socket.on()`来监听`connect`发射并记录一条消息，以确保客户端已连接。这是一个保留事件，不应手动发出。你可以在这里找到[的预约活动列表。](https://socket.io/docs/v3/emit-cheatsheet/#reserved-events)

现在，我们需要在具有`[]`依赖关系的`useEffect`钩子中调用这个函数。我们将`socketInitializer`包装在另一个函数中的原因是因为效果回调应该是同步的，以防止竞争条件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
import { useEffect } from 'react'
import io from 'Socket.IO-client'
let socket

const Home = () => {
  useEffect(() => socketInitializer(), [])

  const socketInitializer = async () => {
    await fetch('/api/socket')
    socket = io()

    socket.on('connect', () => {
      console.log('connected')
    })
  }

  return null
}

export default Home;

```

如果您现在访问运行开发服务器的本地主机上的端口，您将看到控制台中记录的“Connected”消息。

如果您检查启动开发服务器的终端屏幕，您还会看到在第一个客户机连接后记录的“套接字正在初始化”。

如果您通过在另一个浏览器选项卡或屏幕上访问开发服务器来连接另一个客户机，您将看到打印出的“Socket is always running”消息。

## 用 Socket.io 发出消息

现在我们已经有了套接字连接，我们可以在客户机和服务器之间发送消息了。

首先，我们将创建一个受控的输入字段。我们需要的只是输入字段的`onChange`事件的状态和处理函数。

```
const [input, setInput] = useState('')

const onChangeHandler = (e) => {
  setInput(e.target.value)
  socket.emit('input-change', e.target.value)
}

return (
  <input
    placeholder="Type something"
    value={input}
    onChange={onChangeHandler}
  />
)

```

在`onChangeHandler`内部，我们将`input`状态设置为一个新值，然后将这个变化作为一个事件发送给服务器。`socket.emit()`的第一个参数是唯一的事件名称，即`input-change`，第二个参数是消息。在我们的例子中，这是输入字段的值。

接下来，我们需要通过监听特定的`input-change`事件来处理服务器中的这个事件。

通过注册一个`connection`监听器，我们可以确保建立了套接字连接。之后，在回调函数中，我们通过`socket.on()`函数订阅了`input-change`事件，该函数将事件名称和回调函数作为参数。

```
io.on('connection', socket => {
  socket.on('input-change', msg => {
    socket.broadcast.emit('update-input', msg)
  })
})

```

然后，在回调函数中，我们使用`socket.broadcast.emit()`，它向除了发出`input-change`事件的客户端之外的所有客户端发出一条消息。所以，只有其他客户端接收到`msg`，它是由其中一个客户端发送的文本输入值。

`socket.js`文件的最终版本如下。

```
>import { Server } from 'Socket.IO'

const SocketHandler = (req, res) => {
  if (res.socket.server.io) {
    console.log('Socket is already running')
  } else {
    console.log('Socket is initializing')
    const io = new Server(res.socket.server)
    res.socket.server.io = io

    io.on('connection', socket => {
      socket.on('input-change', msg => {
        socket.broadcast.emit('update-input', msg)
      })
    })
  }
  res.end()
}

export default SocketHandler

```

现在，回到客户。这一次，我们需要处理广播的事件，它是`update-input`。我们需要做的就是用`socket.on()`订阅这个事件，并在回调函数中调用`setInput`函数，用服务器发送的值更新输入值。

```
import { useEffect, useState } from 'react'
import io from 'Socket.IO-client'
let socket;

const Home = () => {
  const [input, setInput] = useState('')

  useEffect(() => socketInitializer(), [])

  const socketInitializer = async () => {
    await fetch('/api/socket');
    socket = io()

    socket.on('connect', () => {
      console.log('connected')
    })

    socket.on('update-input', msg => {
      setInput(msg)
    })
  }

  const onChangeHandler = (e) => {
    setInput(e.target.value)
    socket.emit('input-change', e.target.value)
  }

  return (
    <input
      placeholder="Type something"
      value={input}
      onChange={onChangeHandler}
    />
  )
}

export default Home;

```

下面是两个客户端连接到套接字并根据发送到服务器的数据进行实时更新的演示。

![Socket Updating in Real-Time](img/5740c3afa7c6f875a1b4bd6c349c28cf.png)

## 结论

在本文中，我们学习了如何将 Socket.io 集成到 Next.js 中，以便启动 WebSocket 连接，在客户机和服务器之间实时地来回共享信息。

我们还在 Next.js 后端创建了一个端点，然后我们在客户端向它发出一个请求来初始化套接字连接。在此之后，服务器和客户机之间的其余通信都是通过套接字连接来处理的。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。