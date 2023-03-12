# 使用 WebSocket 和 Node.js 的 WebRTC 信令

> 原文：<https://blog.logrocket.com/webrtc-signaling-websocket-node-js/>

***编者按:*** *本文于 2022 年 5 月 12 日更新，包含了与 WebRTC 和 WebSocket 最新特性相关的信息。*

WebSocket 是一种支持客户端应用程序(例如，浏览器、本机平台等)之间实时通信的协议。)和一个 WebSocket 服务器。它利用一个单一的开放式 [TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) 连接(加密或未加密)来处理这两种介质之间的实时数据传输。

该规范定义了不同的 URI 方案:`ws` (WebSocket)用于未加密连接，而`wss` (WebSocket Secure)用于加密连接。因此，Websockets 协议支持双向客户端-服务器模型，在这种模型中，可以无缝传输视频/音频等媒体类型。

在本文中，我们将探索 WebSocket 协议，并回顾如何使用 Node.js 中的 [ws](https://github.com/websockets/ws) WebSocket 库来设置一个基本的 WebSocket 服务器。

首先，让我们探索 WebRTC，这是一种在所有现代浏览器客户端和原生 Android/iOS 平台上都可用的协议，用于实时双向通信。

向前跳:

## WebRTC 简介

[webRTC](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API) ，代表 Web 实时通信，是一种为 Web 提供一组双向和安全实时对等通信规则的协议。使用 WebRTC，web 应用程序或其他 WebRTC 代理可以利用简单的 web APIs 在对等点之间发送视频、音频和其他类型的媒体。

WebRTC 依靠一堆其他的[协议](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Protocols)来实现其创建
通信通道的目的，然后传输或交换数据和/或媒体类型。为了协调通信，WebRTC 客户端需要某种类型的“[信令服务器](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Signaling_and_video_calling)”来交换元数据信息。

提供[信号](https://github.com/muaz-khan/WebRTC-Experiment/blob/master/Signaling.md)的最常见方式之一是利用[插座。IO](https://socket.io/)–和基于 ws 的 Node.js 服务器，用于为 WebRTC 客户端实时共享会话描述、媒体信息和数据，使它们成为互补技术。

> **注意**:由于不同的应用程序可能更喜欢使用不同的信令协议或服务，因此 WebRTC 开放标准没有实现这一点。这有助于确保与生态系统中其他工具的最大兼容性。

## WebRTC 协议如何工作

WebRTC 代理知道如何创建与对等方的连接。信令触发这种初始尝试，最终使两个代理之间的呼叫成为可能。代理利用提供/应答模型:一个代理提供开始呼叫，另一个代理响应呼叫并检查关于所提供的媒体描述的兼容性。

在高层次上，WebRTC 协议分四个阶段工作。沟通以有序的方式进行，其中一个阶段必须在下一个阶段开始之前完成。这四个阶段包括:

### 第一阶段:发信号

这开始了识别两个打算通信和交换数据的 WebRTC 代理的过程。当对等体最终连接并可以通信时，信令使用另一种协议， [SDP](https://developer.mozilla.org/en-US/docs/Glossary/SDP) 。
会话描述协议(一种明文协议)对于交换密钥-值对中的媒体部分是有用的。有了它，我们就可以在两个或多个有意连接的对等体之间共享状态。

> **注**:共享状态可以提供在对等体之间建立连接所需的所有参数。

### 阶段 2:连接

发出信号后，WebRTC 代理需要实现双向的、对等的通信。尽管由于各种原因，如不同的 IP 版本、网络位置或协议，建立连接可能很困难，但与使用 HTTP 轮询的传统 web/服务器客户端通信相比，WebRTC 连接提供了更好的选择。使用 WebRTC，连接减少了带宽，降低了延迟，并且更安全。

> **注意** : WebRTC 也利用 [ICE](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Protocols#ICE) 协议(交互式连接建立)连接两个代理。ICE 协议试图找到两个 ICE 代理之间通信的最佳方式。更多细节可以在[这里](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Connectivity#ICE_candidates)找到。

### 阶段 3:保护

每个 WebRTC 连接都经过加密和身份验证。在幕后，它利用 [DTLS](https://en.wikipedia.org/wiki/Datagram_Transport_Layer_Security) 和 [SRTP](https://en.wikipedia.org/wiki/Secure_Real-time_Transport_Protocol) 协议来实现跨数据层的无缝和安全通信。类似于 TLS，DTLS 允许会话协商或握手，并允许对等体之间的安全数据交换。另一方面，SRTP 对于交换媒体信息来说很方便。

### 阶段 4:沟通

使用 WebRTC 协议，我们可以轻松地发送和接收无限量的音频和视频流。它依赖于两个预先存在的协议: [RTP](https://en.wikipedia.org/wiki/Real-time_Transport_Protocol) 和 [RTCP](https://en.wikipedia.org/wiki/RTP_Control_Protocol) 。RTP 协议携带媒体信息，允许实时传输视频流。RTCP 协议传递或同步关于呼叫的元数据。

为了获得无缝和成功的通信体验，在共享媒体信息之前，两个通信对等体必须共享双方同意的预定义编解码器。同样，作为标准实践，该协议独立于特定的编解码器，因为有许多[选项](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/WebRTC_codecs)。

> **注意**:对于大多数 WebRTC 应用程序，客户端之间没有直接的套接字连接(除非它们驻留在同一个本地网络上)。解决这类问题的常见方法是使用 TURN 服务器。该术语代表使用 NAT 周围中继的穿越，是一种用于中继网络流量的协议。 [NAT](https://developer.mozilla.org/en-US/docs/Glossary/NAT) 映射，借助 [STUN](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Protocols#STUN) 和 [TURN](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Protocols#TURN) 协议，让两个完全不同子网的对等体进行通信。

## WebRTC 的用例

WebRTC 对于在 web 和移动平台上构建实时应用程序非常有用。下面列出了一些最常见的使用案例:

*   视频和文本聊天
*   分析学
*   网络社交
*   屏幕共享技术
*   会议(音频/视频)
*   现场直播
*   文件传输
*   在线学习
*   多人在线游戏

## WebRTC JavaScript APIs

WebRTC 主要包括三个操作:从摄像机/麦克风获取用户媒体(音频和视频)；通过信道传送该媒体内容；最后，通过信道发送消息。

现在，让我们来看看每个流程的总结描述。

这个 API 让我们能够访问任何媒体数据的硬件来源。`getUserMedia()`方法在用户通过提示允许访问的情况下，激活系统上的摄像头和/或麦克风，并提供包含所需输入的视频轨道和/或音频轨道的`[[MediaStream]](https://developer.mozilla.org/en-US/docs/Web/API/MediaStream)`。

> **注意**:`Navigator.mediaDevices`只读属性返回一个`[[MediaDevices]](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices)`对象/接口，它提供对连接的媒体输入设备如照相机和麦克风的访问，以及屏幕共享。

格式如下所示:

```
const promise = navigator.mediaDevices.getUserMedia(constraints);

```

`constraints`参数是一个`MediaStreamConstraints`对象，有两个成员:`video`和`audio`，描述请求的媒体类型。它还控制媒体流的内容。
例如，我们可以设置一个约束，使用`minWidth`和`minHeight`功能打开摄像机:

```
  'video': {
    'width':  {'min': minWidth},
    'height': {'min': minHeight} 
  }

```

或者我们可以在麦克风上设置回声消除:

```
'audio': {'echoCancellation': true},

```

所以，本质上，我们一般可以声明一个`constraints`变量，就像这样:

```
const constraints = {
    'video': true,
    'audio': true
}

```

最后，让我们看一个例子，看看我们如何应用`getUserMedia()`来触发对用户浏览器的权限请求:

```
const openMediaDevices = async (constraints) => {
    return await navigator.mediaDevices.getUserMedia(constraints);
}

try {
    const stream = openMediaDevices({'video':true,'audio':true});
    console.log('Got MediaStream:', stream);
} catch(error) {
    console.error('Error accessing media devices.', error);
}

```

媒体流 API 中可用的其他方法包括:

*   `enumerateDevices()`
*   `getSupportedConstraints()`
*   `getDisplayedMedia()`
*   `RTCPeerConnection`

### `RTCPeerConnection`界面

`RTCPeerConnection`接口表示本地计算机和远程对等体之间的 WebRTC 连接。它提供了连接到远程对等点、维护和监控连接以及在不再需要时关闭连接的方法。

一旦向远程对等点发出`RTCPeerConnection`，就可以在它们之间传输音频和视频内容。在这一层，我们可以将从`getUserMedia()`方法接收的流连接到`RTCPeerConnection`。
`RTCPeerConnection`方法包括:

*   `addIceCandidate()`
*   `peerIdentity`
*   `signalingState`
*   `setLocalDescription()`
*   `setRemoteDescription()`

> **注意**:一个媒体流应该包括至少一个媒体轨道，当我们打算向远程对等点传输媒体时，必须将该媒体轨道添加到`RTCPeerConnection`中。

### `RTCDataChannel`界面

`[[RTCDataChannel]](https://developer.mozilla.org/en-US/docs/Web/API/RTCDataChannel)`接口代表一个网络通道，可用于任意数据的双向点对点传输。要创建一个数据通道并请求一个远程对等体加入，我们可以调用`[[RTCPeerConnection]](https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection)`的`[[createDataChannel()]](https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection/createDataChannel)`方法。下面显示了一个如何操作的示例:

```
const peerConnection = new RTCPeerConnection(configuration);
const dataChannel = peerConnection.createDataChannel();

```

方法包括:

*   `close()`:`RTCDataChannel.close()`方法关闭 RTCDataChannel 任何一方都可以调用此方法来启动通信通道的关闭
*   `send()`:`RTCDataChannel.send()`方法通过数据通道向远程对等体发送数据

基于公开的 API 集的 WebRTC 协议的几个开源实现可以在[这里](https://github.com/muaz-khan/WebRTC-Experiment)找到。它包含各种 WebRTC 实验的存储库。例如，`getDisplayMedia()`用法的现场演示可以在[这里](https://github.com/muaz-khan/WebRTC-Experiment/tree/master/getDisplayMedia)找到。

## 基于 Node.js WebSocket 的服务器示例

要创建 WebRTC 连接，客户端需要能够通过 WebSocket 信令传输消息，web socket 信令是两个端点之间的双向套接字连接。在 GitHub 上可以找到 Node.js 上的 [WebSocket 的完整演示实现，由 Muaz Khan 提供。为了更好地理解上下文，让我们探索一下`server.js`文件中的一些重要部分。](https://github.com/muaz-khan/WebRTC-Experiment/tree/master/websocket-over-nodejs)

首先，我们可以设置一个接受对象作为参数的 HTTP 服务器。该对象应该包含建立无缝连接所需的安全密钥。我们还需要指定一个回调函数，以便在收到连接请求以及返回给调用者的响应时运行:

```
// HTTPs server 
var app = require('https').createServer(options, function(request, response) {
  // accept server requests and handle subsequent responses here 
});

```

接下来，我们可以继续设置 WebSocket 服务器并监听何时有连接请求进入，如下所示:

```
// require websocket and setup server.
var WebSocketServer = require('websocket').server;

// wait for when a connection request comes in 
new WebSocketServer({
    httpServer: app, 
    autoAcceptConnections: false 
}).on('request', onRequest);

// listen on app port 
app.listen(process.env.PORT || 9449);

//handle exceptions and exit gracefully 
process.on('unhandledRejection', (reason, promise) => {
  process.exit(1);
});

```

从上面的片段中我们可以看到，当我们接收到 WebSocket 连接时，我们会在 app 端口上进行侦听。当我们这样做时(在`request`事件的触发下)，我们用`onRequest`回调来处理连接请求。

下面是`onRequest`方法的内容:

```
// callback function to run when we have a successful websocket connection request
function onRequest(socket) {

    // get origin of request 
    var origin = socket.origin + socket.resource;

    // accept socket origin 
    var websocket = socket.accept(null, origin);

    // websocket message event for when message is received
    websocket.on('message', function(message) {
        if(!message || !websocket) return;

        if (message.type === 'utf8') {
            try {
                // handle JSON serialization of messages 
                onMessage(JSON.parse(message.utf8Data), websocket);
            }
            // catch any errors 
            catch(e) {}
        }
    });

    // websocket event when the connection is closed 
    websocket.on('close', function() {
        try {
            // close websocket channels when the connection is closed for whatever reason
            truncateChannels(websocket);
        }
        catch(e) {}
    });
}

```

在上面的代码中，当消息以指定的格式出现时，我们通过`onMessage`回调函数处理它，该函数在`message`事件被触发时运行。

以下是回调方法的详细信息:

```
// callback to run when the message event is fired 
function onMessage(message, websocket) {
    if(!message || !websocket) return;

    try {
        if (message.checkPresence) {
            checkPresence(message, websocket);
        }
        else if (message.open) {
            onOpen(message, websocket);
        }
        else {
            sendMessage(message, websocket);
        }
    }
    catch(e) {}
}

```

> **注**:关于上面使用的其他方法的细节，如`sendMessage`和`checkPresence`，以及演示 WebSocket 服务器的完整实现可以在 [GitHub 上找到。](https://github.com/muaz-khan/WebRTC-Experiment/tree/master/websocket-over-nodejs)

此外，要开始使用 WebSocket 库，我们需要在 WebRTC 客户机中指定 Node.js 服务器的地址。完成后，我们可以进行浏览器间 WebRTC 音频/视频调用，其中的信令由 Node.js WebSocket 服务器处理。

最后，为了突出重点，下面是 WebSocket 连接中需要注意的三种基本方法:

*   `ws.onopen`:连接时发出
*   `ws.send`:触发发送事件到 WebSocket 服务器
*   `ws.onmessage`:接收消息时发出的事件

## 结论

正如我们所见，WebRTC API 包括媒体捕获、音频和视频流的编码和解码、传输和会话管理。尽管浏览器中的 WebRTC 实现仍在不断发展，因为对 WebRTC 特性的支持水平各不相同，但我们可以通过使用 Adapter.js 库来避免兼容性问题。

该库使用填充和聚合填充来解决各种支持环境中 WebRTC 实现之间的差异。我们可以用脚本属性将它添加到`index.html`文件中，就像这样:

```
<script src="https://webrtc.github.io/adapter/adapter-latest.js"></script>

```

我们可以在这个[链接](https://webrtc.github.io/samples/)上找到一些展示 WebRTC APIs 各个部分的小样本。它包含主要 WebRTC APIs 的实现细节和演示，包括`getUserMedia()`、`RTCPeerConnection()`和`RTCDataChannel()`。

最后，你可以在 GitHub 的 [websocket-over-nodejs](https://github.com/muaz-khan/WebRTC-Experiment/tree/master/websocket-over-nodejs) 和 [socketio-over-nodejs](https://github.com/muaz-khan/WebRTC-Experiment/tree/master/socketio-over-nodejs) 上找到更多关于 web 实验的细节。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.