# 在 Node 中编写一个工作的聊天服务器

> 原文：<https://blog.logrocket.com/working-chat-server-in-node/>

这大概是 Node.js 和(尤其是) [Socket.io](https://socket.io/) 发布以来被打得半死的话题。我看到的问题是，大多数文章往往停留在聊天服务器应该做什么的表面上，即使它们最终解决了最初的困境，但这是一个如此基本的用例，以至于将代码转化为生产就绪的聊天服务器相当于下图:

![](img/bce9a519ba349e345c5a932edc623164.png)

因此，在本文中，我想与您分享一个实际的聊天服务器，请注意，由于媒体的限制，它有点基础，但您将能够从第一天开始使用它。事实上，我已经在我的一个个人项目中使用了。

## 聊天服务器是做什么的？

但是首先，让我们快速回顾一下聊天服务器真正有用需要什么。抛开您的特殊需求不谈，聊天服务器应该能够做到以下几点:

*   从客户端应用程序接收消息
*   将收到的消息分发给其他有兴趣获取它们的客户端
*   广播常规通知，如用户登录和注销
*   能够在两个用户之间发送私人消息(即私人消息)

这就是我们的小聊天服务器能够做到的程度。

出于本文的目的，我将创建这个服务器作为后端服务，能够在没有定义前端的情况下工作，并且我还将使用 [jQuery](https://jquery.com/) 和普通 JavaScript 创建一个基本的 HTML 应用程序。

## 定义聊天服务器

现在我们知道了聊天服务器将要做什么，让我们来定义它的基本接口。不用说，整个事情将基于 [Socket.io](https://socket.io/) ，所以本教程假设你已经熟悉这个库。如果你不是，我强烈建议你在前进之前检查一下。

说完这些，让我们更详细地了解一下我们服务器的任务:

*   该服务器需要能够接收和分发邮件。这将成为我们将要使用的两个主要方法
*   除此之外，我还将添加一些代码来处理加入动作，以便通知同一房间中连接的其他客户端
*   信息将正常发送，私人信息将以@开头，后跟另一个用户名(例如*“@ Fernando 嘿，你好吗？”* )

我构建代码的方法是创建一个名为`ChatServer`的类，在这个类中，我们可以抽象出套接字的内部工作方式，就像这样:

```
// Setup basic express server
const config = require("config");
const ChatServer  = require("./lib/chat-server")

const port = process.env.PORT || config.get('app.port');
// Chatroom

let numUsers = 0;

const chatServer = new ChatServer({
    port
})

chatServer.start( socket => {
  console.log('Server listening at port %d', port);
    chatServer.onMessage( socket, (newmsg) => {
        if(newmsg.type = config.get("chat.message_types.generic")) {
            console.log("New message received: ", newmsg)
           chatServer.distributeMsg(socket, newmsg, _ => {
               console.log("Distribution sent")
           })
        }

        if(newmsg.type == config.get('chat.message_types.private')) {
            chatServer.sendMessage(socket, newmsg, _ => {
                console.log("PM sent")
            })
        }
    })

    chatServer.onJoin( socket, newUser => {
        console.log("New user joined: ", newUser.username)
        chatServer.distributeMsg(socket, newUser.username + ' has joined !', () => {
            console.log("Message sent")
        })
    }) 
})
```

注意我是如何启动服务器的，一旦它启动并运行，我就设置了两个不同的回调函数:

*   一个用于传入消息，它简单地接收消息，然后将其格式化成一个方便的 [JSON](https://blog.logrocket.com/the-quiet-revolution-how-json-displaced-xml-1e1f3e8552f7/) ，然后将其作为回调函数的属性返回
*   一个用于加入事件，当用户加入房间时，在所有其他人之间分发一条消息，让他们知道谁刚刚加入
*   标准消息被标记为“通用”类型，并且它们最终与接收到的消息的内容一起广播到整个房间(当然发送客户端除外)
*   私有消息(以@字符开头的消息)被标记为“私有”，并通过其独特的套接字连接直接发送给目标用户(我将在一分钟后向您展示如何发送)

现在让我向您展示来自聊天服务器的方法是如何实现的。

## 插座是如何工作的？

长话短说，套接字是两台计算机之间持久的双向连接，通常，一台充当客户机，另一台充当服务器(换句话说:服务提供者和消费者)。

套接字和另一种众所周知的客户端和服务器之间的通信方法(即 REST APIs)有两个主要区别(如果我们坚持我刚才给你的高层次定义的话):

1.  连接是持久的，这意味着一旦客户端和服务器连接，客户端发送的每条新消息都将被完全相同的服务器接收。REST APIs 不是这种情况，它需要无状态。一组负载平衡的 REST 服务器不需要(事实上，甚至不推荐)同一个服务器来回复来自同一个客户机的请求。
2.  通信可以由服务器启动，这也是使用 sockets 优于 REST(或者说 HTTP)的好处之一。当一段数据需要从服务器移动到客户机时，这简化了许多后勤工作，因为使用开放套接字，没有其他先决条件，数据只是从一端流向另一端。这也是使基于套接字的聊天服务器如此简单和直接的用例的特性之一，如果您想使用 REST 或类似的协议，您将需要大量额外的网络流量来触发各方之间的数据传输(比如让客户端应用程序进行主动轮询以从服务器请求待处理的消息)。

也就是说，下面的代码试图简化 Socket.io 处理和管理套接字连接所需的逻辑:

```
let express = require('express');
let config = require("config")
let app = express();
let socketIO = require("socket.io")
let http = require('http')

module.exports = class ChatServer {

    constructor(opts) {
        this.server = http.createServer(app);
        this.io = socketIO(this.server);
        this.opts = opts 
        this.userMaps = new Map()
    }

    start(cb) {
        this.server.listen(this.opts.port, () => {
            console.log("Up and running...")
            this.io.on('connection', socket => {
                cb(socket)
            })
        });
    }

    sendMessage(socket, msgObj, done) {
        // we tell the client to execute 'new message'
        let target = msgObj.target
        this.userMaps[target].emit(config.get("chat.events.NEWMSG"), msgObj)
        done()
    }

    onJoin(socket, cb) {
        socket.on(config.get('chat.events.JOINROOM'), (data) => {
            console.log("Requesting to join a room: ", data)

            socket.roomname = data.roomname
            socket.username = data.username

            this.userMaps.set(data.username, socket)

            socket.join(data.roomname, _ => {
                cb({
                    username: data.username, 
                    roomname: data.roomname
                })
            })
        })
    }

    distributeMsg(socket, msg, done) {
        socket.to(socket.roomname).emit(config.get('chat.events.NEWMSG'), msg);
        done()
    }

    onMessage(socket, cb) {
        socket.on(config.get('chat.events.NEWMSG'), (data) => {
            let room = socket.roomname
            if(!socket.roomname) {
                socket.emit(config.get('chat.events.NEWMSG'), )
                return cb({
                    error: true, 
                    msg: "You're not part of a room yet"
                })
            }

            let newMsg = {
                room: room,
                type: config.get("chat.message_types.generic"),
                username: socket.username,
                message: data
            }

            return cb(newMsg)
        });

        socket.on(config.get('chat.events.PRIVATEMSG'), (data) => {
            let room = socket.roomname

            let captureTarget = /(@[a-zA-Z0-9]+)(.+)/
            let matches = data.match(captureTarget)
            let targetUser = matches[1]
            console.log("New pm received, target: ", matches)

            let newMsg = {
                room: room,
                type: config.get("chat.message_types.private"),
                username: socket.username,
                message: matches[2].trim(),
                target: targetUser
            }
            return cb(newMsg)
        })
    }
}
```

## 初始化

`start`方法负责启动套接字服务器，使用 Express HTTP 服务器作为基础(这是库的要求)。在这里你没有什么可以做的了，这个初始化的结果就是调用你在代码中设置的回调函数。这里的要点是确保在服务器真正启动并运行之前(也就是回调被调用的时候)，您不能开始做任何事情。

在这个回调中，我们为`connection`事件设置了一个处理程序，它是每次新客户端连接时触发的事件。这个回调将接收实际的套接字实例，所以我们需要确保它的安全，因为它将是我们用来与客户端应用程序通信的对象。

正如您在第一个代码示例中注意到的，套接字实际上是作为所有需要它的方法的第一个参数传递的。这就是我如何确保不覆盖其他客户端创建的套接字的现有实例。

## 加入房间

套接字连接建立后，客户端应用程序需要手动加入聊天和其中的特定房间。这意味着客户机将用户名和房间名作为请求的一部分发送出去，服务器在一个 [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 对象中记录用户名-套接字对。一会儿我会给你们展示对这张地图的需求，但是现在，这是我们所要做的。

`socket`实例的`join`方法确保特定的插座被分配到正确的房间。通过这样做，我们可以限制广播消息的范围(那些需要发送给每个相关用户的消息)。幸运的是，这个方法和整个房间管理逻辑都是由 Socket.io 提供的，所以除了使用这些方法之外，我们实际上不需要做任何事情。

## 接收消息

这可能是模块中最复杂的方法，正如你可能已经看到的，它并不复杂。这个方法负责为收到的每个新消息设置一个处理程序。这可以解释为使用 [Express](https://blog.logrocket.com/forget-express-js-opt-for-these-alternatives-instead/) 的 REST API 的路由处理程序。

现在，如果我们深入抽象的兔子洞，你会注意到套接字并不真正理解“消息”，相反，它们只关心事件。对于本模块，我们只允许两个不同的事件名称“新消息”和“新 pm”作为消息接收或发送事件，因此服务器和客户端都需要确保它们使用相同的事件名称。这是必须发生的契约的一部分，就像客户端如何需要知道 API 端点以便使用它们一样，这应该在服务器的文档中指定。

现在，在接收到消息事件时，我们做类似的事情:2

*   对于一般消息，我们确保目标房间名称实际上是用户以前加入的房间名称。这只是一个防止发送邮件时出现问题的小检查。
*   对于私有消息，我们使用快速简单的正则表达式捕获它的两个部分:目标用户和实际消息。

完成后，我们创建一个 JSON 有效负载，并将其传递给提供的回调。所以基本上，这个方法的意思是接收消息，检查它，解析它并返回它。没有额外的逻辑与之相关联。

在这一步之后，无论需要什么样的逻辑，都将存在于您的自定义回调函数中，正如您在第一个示例中看到的那样，它负责根据类型将消息分发到正确的目的地(要么向同一聊天室中的每个人广播消息，要么向目标用户发送私人消息)。

## 传递私人信息

虽然非常简单，但是`sendMessage`方法使用了我最初提到的地图，所以我也想涵盖它。

我们可以将消息传递给特定的客户端应用程序(从而将其传递给实际用户)的方式是使用位于服务器和用户之间的套接字连接，这就是我们的`userMaps`属性发挥作用的地方。有了它，服务器可以根据目标用户名快速找到正确的连接，并使用这个连接通过`emit`方法发送消息。

## 向整个房间广播

这也是我们实际上不需要担心的事情，Socket.io 负责为我们做所有的繁重工作。为了跳过源客户端(基本上是向房间发送原始消息的客户端)向整个房间发送消息，需要调用房间的`emit`方法，使用特定客户端的套接字作为连接源。

除了源客户端之外，为房间中的每个人重复消息的逻辑完全在我们的控制之外(这正是我喜欢的方式！).

## 你完了！

没错，代码中没有其他相关内容，在这两个例子中，您已经获得了复制服务器并开始在代码中使用它所需的所有信息。

我将留给您一个非常简单的客户端，如果您以前没有做过，您可以使用它来测试您的进度:

```
const io = require('socket.io-client')

// Use https or wss in production.
let url = 'ws://localhost:8000/'

let usrname = process.argv[2] //grab the username from the command line
console.log("Username: ", usrname)

// Connect to a server.
let socket = io(url)

// Rooms messages handler (own messages are here too).
socket.on("new message", function (msg) {
  console.log("New message received")
  console.log(msg)
  console.log(arguments)
})

socket.on('connect', _ => {
  console.log("CONNECTED!")
})
socket.emit("new message", "Hey World!")

socket.emit("join room", {
  roomname: "testroom",
  username: usrname
})

socket.emit("new message", 'Hello there!')
```

这是一个非常简单的客户端，但是它包含了消息发送和房间加入事件。您可以快速编辑它，向不同的用户发送私人消息，或者添加输入收集代码，以实际创建一个工作的聊天客户端。

在这两种情况下，这个例子应该足以让您的聊天服务器快速启动！正如所料，有很多方法可以不断改进这一点，因为它的主要问题之一是没有持久性，如果服务终止，在重新启动时，所有连接信息都将丢失。用户信息和房间历史记录也是如此，您可以快速添加存储支持，以便永久保存该信息，然后在启动时恢复它。

请在下面的评论中告诉我，如果你在过去实现了这种基于套接字的聊天服务，以及你还用它做了什么，我很想知道！

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

否则，*下一场见！*

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.