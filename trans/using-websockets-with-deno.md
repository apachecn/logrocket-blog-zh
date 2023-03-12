# 通过 Deno - LogRocket 博客使用 WebSockets

> 原文：<https://blog.logrocket.com/using-websockets-with-deno/>

WebSockets 是一种通信协议，它允许作为 HTTP 替代方案的双向、持久通信。假设您想获得某个服务器状态的更新。对于 HTTP，您需要频繁地轮询数据。

但是 HTTP 轮询有一些限制:

1.  订阅客户端不会实时看到对数据的更改
2.  服务器只对客户端的初始请求做出响应——换句话说，它是单向的
3.  即使没有新数据，服务器资源也会被处理请求占用

有了 WebSockets，客户机不需要向服务器请求新数据，web sockets 服务器只是将新数据直接推送到客户机。

### 什么时候应该使用 WebSockets？

WebSockets 应该在您的应用程序中需要实时功能时使用，例如，一个聊天应用程序或一个观察股票市场的机器人。WebSockets 最适用于需要对频繁变化的数据做出快速反应的情况。如果您的应用程序数据不经常改变，最好实现简单的轮询逻辑。

### WebSockets 协议是如何工作的？

WebSockets 使用 HTTP 作为启动服务器连接的机制。然后，此连接将升级为 WebSocket 连接。

客户端只能通过`ws://`或`wss://`的 URI 方案访问 WebSocket 服务器。要启动 WebSockets 连接，您必须首先实现一个 WebSocket 客户端并拥有一个支持 WebSockets 的服务器。这就是 Deno 的用武之地。

## 用 Deno 实现 WebSocket 服务器和客户端

以下是我们将在本教程中涵盖的内容:

1.  [在 Deno 创建 WebSocket 服务器](#creating-websocket-server)
2.  [在 Deno 中创建 WebSocket 客户端](#creating-websocket-client)
3.  [在服务器和客户端之间发送消息](#sending-messages)
4.  [从服务器向多个客户端广播消息](#broadcasting-messages)

### 1.在 Deno 中创建 WebSocket 服务器

首先，我们需要在本地机器上安装 Deno。完成后，创建一个新目录来存放我们的服务器和客户机代码:

```
mkdir websocket-tutorial

```

创建一个名为`server.ts`的新文件。我们将在这里创建一个简单的 HTTP 服务器。

```
touch ./server.ts

```

接下来，在文件的顶部，导入`serve`函数:

```
import { serve } from "https://deno.land/[email protected]/http/server.ts";

```

现在让我们在端口 80 上创建 HTTP 服务器，它将用于引导、接受和发送 WebSocket 连接:

```
for await (const req of serve({port:80})){
req.response({body:"Hello world"})
}

```

`serve()`返回一个[异步可迭代的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for-await...of)，它由对我们服务器的 HTTP 请求组成。`for` `await`语法用于遍历和响应每个请求。

使用以下命令运行代码:

```
deno run --allow-net server.ts

```

`--allow-net`参数赋予 Deno 进行网络调用的权限。在你的浏览器中输入`localhost:80`，你应该会在页面上看到一条“Hello world”的消息。

接下来，我们需要将常规的 HTTP 请求升级为 WebSocket 连接。从 Deno 的 WebSocket 模块导入`acceptWebSocket`函数:

```
import {acceptWebSocket} from "https://deno.land/[email protected]/ws/mod.ts"

```

负责将请求升级到 WebSocket 连接。我们将为它提供必要的参数:

```
for await (const req of serve({ port: 80 })) {
    const { conn, r: bufReader, w: bufWriter, headers } = req;
         acceptWebSocket({
            conn,
            bufReader,
            bufWriter,
            headers,
        }).then(handleWs)
}

```

我们还没有创建`handleWs`函数，所以让我们在继续下一步之前先创建它:

```
async function handleWs(sock:WebSocket){
console.log("socket connected")
for await (const event of sock){
if(typeof event === "string"{
console.log(ev)
}
}
}

```

`handleWs`函数将 WebSocket 对象作为参数。该对象是一个异步 iterable，由 WebSocket 连接的客户端发出的事件组成。如果事件是字符串，则事件是来自 WebSocket 客户端的消息有效负载。

还有其他类型的事件，我们将在后面介绍。WebSockets 只能以`ArrayBuffer`或字符串的形式传输消息，所以您需要使用`JSON.stringify`来传输 JSON 消息。

完成后，让我们创建 WebSocket 客户端。

## 2.在 Deno 中创建 WebSocket 客户端

在项目文件夹中创建一个名为`client.ts`的新文件:

```
touch ./client.ts

```

我们将定义一个名为`createWebSocket`的函数，它将包含初始化 WebSocket 以及发送和接收 WebSocket 消息的代码。

```
function createWebSocket() {
    const websocket = new WebSocket("ws://localhost:80")
    websocket.onopen = () => {
        setInterval(() => {
            websocket.send(`Client says hello`)
        }, 2000)
    }

}

```

Websocket URLs 以`wss://`开头表示安全连接，以`ws://`开头表示不安全连接。

当新创建一个 WebSocket 对象时，它的连接并没有立即准备好。利用 WebSockets 的事件驱动特性。我们可以给`WebSocket.onopen`事件监听器附加一个函数。一旦 WebSocket 连接打开，就调用这个函数。

在`onopen`事件监听器函数中，我们使用 [`setInterval`](https://www.w3schools.com/jsref/met_win_setinterval.asp) 函数每两秒发送一条消息。

让我们通过启动 WebSocket 服务器来测试我们的代码:

```
deno run --allow-net server.ts

```

而客户端:

```
deno run --allow-net client.ts

```

我们应该每两秒钟就能在服务器控制台上看到“Client says hello”。

### 3.在服务器和客户端之间发送消息

我们已经看到了如何从客户机向服务器发送消息。但是正如我们上面提到的，WebSockets 允许双向消息传递。现在让我们从服务器向客户机发送消息。

更新`server.ts`中的`handleWs`功能:

```
async function handleWs(sock: WebSocket) {
    if (!sock.isClosed) {
        sock.send("Hi from server")
    } //add this

    for await (const ev of sock) {
        if (typeof ev === "string") {
            console.log(ev);
        }
    }
}

```

注意，这里有一个检查来检测套接字是否已经使用`sock.isClosed`属性关闭。

更新`client.ts`中的`createWebSocket`函数，以接收来自服务器的消息:

```
function createWebSocket() {
    const websocket = new WebSocket("ws://localhost:80")
    websocket.onopen = () => {
        setInterval(() => {
            websocket.send(`Client says hello`)
        }, 2000)
    }
    websocket.onmessage = (message) => {
        console.log(message.data)
    }
}

```

运行更新后的代码应该会在客户端控制台上显示“Hi from server”。

`onmessage`事件监听器函数用于订阅从 WebSocket 服务器发送的消息。它的有效负载在数据属性内。

### 4.从服务器向多个客户端广播消息

我们已经看到了如何从服务器向单个客户端发送消息，但是这很少有用；例如，在聊天应用程序中，您需要立即向多个客户端广播一条消息。您可能还需要区分不同的 WebSocket 连接。

在我们的`server.ts`文件的顶部，我们将创建一个`Map`对象，并给它分配变量`sockets`:

```
const sockets = new Map<string, WebSocket>()

```

`Map`是一个具有包装键值对的方法的对象。在这种情况下，我们将一个字符串关联到一个 WebSockets 对象。我们将所有 WebSockets 连接存储在`sockets`变量中。

现在更新`handleWs`功能:

```
async function handleWs(sock: WebSocket) {
    console.log('connected')
    const uid = v4.generate()
    sockets.set(uid, sock)

    for await (const ev of sock) {
        if (isWebSocketCloseEvent(ev)) {
            sockets.delete(uid)
            return
        }
        if (typeof ev === "string") {
            console.log(ev)
            broadcastMessage(ev,uid)
        }
    }
}

```

从 [uuid](https://deno.land/std@0.90.0/uuid) 库 **:** 中导入`v4.generate`函数

```
import { v4 } from 'https://deno.land/std/uuid/mod.ts';

```

`v4.generate`函数为每个 WebSocket 连接生成一个随机 ID。每个 ID 用于在发送消息时标识 WebSocket 连接。我们将把这个连接添加到`sockets`变量中。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

请注意，一旦 close 事件发生，我们将删除 WebSocket 连接:

```
if (isWebSocketCloseEvent(ev)) {
 sockets.delete(uid)
 return
}

```

接下来，我们将创建一个名为`broadcastMessage`的函数，它将一条消息作为参数传输到存储在`sockets`变量中的所有 WebSockets:

```
function broadcastMessage(message: string, uid: string) {
    sockets.forEach((socket) => {
        if (!socket.isClosed && uid !== id)
            socket.send(message)
    })
}

```

注意，我们使用第二个参数`uid`来标识发送消息的客户端。注意检查`uid !==id`，它确保我们不会向它的源套接字广播消息。

现在让我们更新我们的`client.ts`文件来模拟多个客户端连接和发送消息。更新`createWebsocket`函数以获取一个`id`参数:

```
function createWebSocket(id: number) {
    const websocket = new WebSocket("ws://localhost:80")
    websocket.onopen = () => {
        setInterval(() => {
            websocket.send(`Client ${id} says hello`)
        }, 2000 * id)
    }
    websocket.onmessage = (message) => {
        console.log(`Client ${id}: ${message.data}`)
    }
}

```

注意`setInterval`参数值`2000 * id`。因为我们将数字作为 ID 传递，所以 ID`1`将等待 2000 毫秒(即 2 秒)来发送消息，而 ID`4`将等待 8000 毫秒(即 8 秒)。

我们使用一个`for`循环创建多个 WebSocket 连接:

```
for (let x = 1; x < 10; x++) {
    createWebSocket(x)
}

```

当我们同时运行`server.ts`和`client.ts`时，我们应该会看到这样的消息:

```
Client 3: Client 8 says hello 
Client 4: Client 8 says hello 
Client 6: Client 8 says hello 

```

## 结论

WebSockets 协议提供了一种无需轮询的双向通信方式。WebSockets 应该用于实时应用程序，如股票市场可视化和需要即时反馈的消息应用程序。

对数据不经常改变的应用程序使用 WebSockets 可能有些过分，因此，简单的 HTTP 轮询机制可能更好。这篇教程的完整代码可以在这里找到。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)