# 服务器发送的事件与 WebSockets - LogRocket 博客

> 原文：<https://blog.logrocket.com/server-sent-events-vs-websockets/>

HTTP 请求-响应模型是 web 的主要通信模型。在这个模型中，客户机(通常是浏览器)向服务器发送一个资源请求，服务器处理请求，然后相应地发回一个响应。为了进行这种通信，服务器和客户端都必须建立连接(例如，TCP 连接)，并且当请求-响应循环结束时，该连接将被关闭。

这个模型对于早期的 web 应用程序来说已经足够了，因为早期的网站只显示静态内容。但是随着 web 的发展，需要允许服务器向客户机发送数据，而不需要客户机首先请求它。这导致了推送技术。

推送技术是一种基于互联网的通信方式，在这种方式中，对给定事务的请求由服务器发起。例如，在社交媒体应用程序中的时间线更新中，服务器将数据推送到客户端，而无需客户端请求数据。

因为 HTTP 请求-响应模型不是为这些用例设计的，所以发明了不同的机制。这些机制是服务器发送事件(SSE)和 WebSockets。

在本文中，我们将了解服务器发送的事件和 WebSockets，包括它们的工作方式和各自的用例，这样您就可以在下一个项目中做出明智的决定。

## 服务器发送的事件

顾名思义，[服务器发送事件](https://blog.logrocket.com/using-fetch-event-source-server-sent-events-react/)是一种推送技术，允许客户端通过 HTTP 连接自动从服务器接收数据。在这种情况下，在服务器和客户端之间建立 HTTP 连接后，服务器可以发送自动更新。

SSE 通过普通的 HTTP 协议发送，浏览器客户端必须通过 JavaScript API `EventSource`注册到事件源。事件的源将在实例化过程中被传递给`EventSource`,它将处理到源的连接，因此客户端将自动获得发送的更新。

您可以将 SSEs 视为一个长期运行的 HTTP 请求，只要服务器需要，它就会向客户端发送数据。

![Representation of Server-Sent Events ](img/47f7487c7e49df8da499a78dbdf83947.png)

### 服务器发送事件中的数据流方向

如上图所示，SSE 是单向的，因此它们只允许从服务器向客户端发送数据。SSEs 被设计为比长轮询更高效，并且包括一些很棒的特性:

*   自动重新连接:如果客户端失去了与事件源的连接，在一定时间后会自动重试重新连接(时间长度可以定制)
*   事件 id:每个发送的事件都可以分配一个唯一的标识符
*   发送任意事件的能力

### 数据传输格式

SSEs 以文本编码的 UTF-8 传输数据。

## WebSockets

[WebSocket](https://blog.logrocket.com/websockets-tutorial-how-to-go-real-time-with-node-and-react-8e4693fbf843/) 是一种通信协议，通过单一 TCP 连接提供双向通信通道。与通过简单的 HTTP 传输的 SSEs 不同，WebSocket 有自己的协议，叫做 WebSocket 协议，你猜对了。

WebSocket 连接从普通的 HTTP 连接开始，通过 WebSocket 握手升级为 WebSocket 连接。握手以来自客户端的带有特殊`UPGRADE`头的 HTTP 请求开始，然后服务器接收请求，处理它，如果请求被接受，就切换到 WebSocket 协议。

当这个握手完成时，双向通信是可能的；客户端可以向服务器发送数据，服务器也可以向客户端发回数据。

以下是 WebSocket 协议握手请求的示例。

客户请求:

```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
Origin: http://example.com

```

服务器响应:

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
Sec-WebSocket-Protocol: chat

```

如前所述，如果握手成功，服务器将切换协议。协议中成功切换的状态代码是`101`。

![Depiction of websocket handshake](img/9e38542da6950b5a10a40b9d1c2f6537.png)

### WebSocket 中的数据传输格式

WebSocket 可以传输 UTF 8 编码文本和二进制格式的数据。以二进制传输数据可以提高数据传输和解释的速度，因为一个字节的所有值都可以在编码中使用。

如果数据是用文本编码的(在这种情况下是 UTF-8)，则只使用与 UTF-8 编码中的字符相对应的二进制值。二进制编码有效率的好处。

## SSE 和 WebSocket 的用例

你可能已经注意到 WebSocket 可以做 SSE 能做的事情甚至更多，那么为什么不仅仅使用 WebSockets 呢？

当不需要从客户机向服务器发送数据时，最好使用 SSE。例如，在状态更新和推送通知应用程序中，数据流仅从服务器流向客户端。这就是 SSE 的设计目的，所以 WebSocket 可能有些过头了。在工作中使用最好的工具总是明智的。

WebSocket 最适合在我们需要实时双向通信的时候使用，比如在聊天应用程序或多人游戏中。WebSocket 旨在处理这类用例。

## 结论

在本文中，我们讨论了 HTTP 请求-响应模型，以及它不足以将数据从服务器推送到客户机。

我们还了解到，服务器发送的事件是长时间运行的 HTTP 请求，通过这些请求，服务器可以随时向客户机发送数据。另一方面，WebSocket 是一个全新的协议，它使用 HTTP 进行连接，然后在建立双向通信之前进行 WebSocket 握手。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)