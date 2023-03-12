# 如何在 React Native - LogRocket 博客中实现 WebSockets

> 原文：<https://blog.logrocket.com/how-to-implement-websockets-in-react-native/>

web 最初是基于请求和响应的原则实现的:客户端发送请求，服务器用适当的响应进行回复。当不需要根据客户端请求进行数据操作时，通常会提供一个静态页面。

当[动态页面](https://www.pingdom.com/blog/a-history-of-the-dynamic-web/)出现时，我们被引入了 GET、POST、PUT 和 DELETE 请求的概念。现在，客户机可以通过将自己的需求作为参数发送给服务器来请求定制数据。然后，服务器处理请求并返回动态响应。

尽管如此，基本原理还是基于请求和响应:客户端请求，服务器响应，然后连接关闭。

还有一种不像请求-响应模式那样工作的协议，叫做 WebSockets。在本教程中，我们将向您介绍 WebSockets 技术及其一些常见的用例。我们将通过设计一个消息广播应用程序，带您了解如何在 React Native 中实现 WebSockets。

## 什么是 WebSockets？

WebSockets 是一种提供[全双工](https://www.comms-express.com/infozone/article/half-full-duplex/)通信的协议，这意味着客户端和服务器通过单一 TCP 连接保持连接。与请求-响应通信不同，连接不会在这里关闭。

由于它是全双工的，服务器也可以在没有请求的情况下向客户端发送数据。这有助于节省带宽和响应时间。

WebSockets 协议以`ws://`开始。为了处理超文本，我们使用`http://`或`https://`。

## 为什么使用 WebSockets？

WebSockets 有一系列的应用，但是我们主要在数据需要被服务器广播或推送的情况下使用它们。

例如，一个聊天应用程序服务器需要在发送者发送消息后立即将消息发送给接收者。服务器不能等待客户端请求新消息，所以它使用全双工通信将它们推送到客户端。同样的，新闻，交易矩阵，甚至社交媒体的帖子都是这样推送的。

在 WebSockets 出现之前，这种结果是可以实现的，但是它们是不可靠和低效的。

例如，许多公司使用长轮询，即浏览器发送请求，但服务器不响应。连接将保持活动状态，直到连接超时。同时，如果服务器有任何消息要发送给客户端，它会做出响应并关闭连接。一旦连接由于超时或服务器响应而关闭，客户端就会再次发送请求。这个过程一直持续下去。

另一种方法是在几秒钟的时间间隔内发送一个 AJAX 请求——比如说三秒钟——然后得到一个服务器响应，它要么是一条有效消息，要么是一个空对象。因此，一条新消息最多只能延迟三秒钟。这种方法的缺点是大量 API 调用被浪费了，因为服务器没有任何消息可以响应。

## 如何在 React Native 中使用 WebSockets

在 WebSockets 的生命周期中，有四个主要的功能被执行。当应用程序建立连接、接收消息、捕捉错误和断开连接时，会调用它们。

让我们放大这些功能。

### 创建 WebSockets 连接

第一步是建立与服务器的连接。WebSockets 按照自己的协议工作。

在 React Native 中，我们可以使用以下代码创建一个连接:

```
var ws = new WebSocket('ws://host.com/path');

```

这里的链接对应于运行在后端上的[套接字服务。](https://blog.logrocket.com/websockets-tutorial-how-to-go-real-time-with-node-and-react-8e4693fbf843/,)

如果服务器接受连接，则通过在 WebSocket 对象上调用`onopen`函数通知客户端:

```
ws.onopen = () => {
  // connection opened
  ws.send('something');  // send a message
};

```

这类似于构造函数。您可以在此功能中分配资源。此外，如果您需要向服务器发送一些一次性信息，如用户标识号，您可以在这里这样做。

### 接收消息

在请求-响应策略中，客户端在发送的请求中寻找响应。这意味着客户机知道何时从服务器获取数据，因此它随时准备处理数据。但是在 WebSockets 这样的全双工通信中，服务器可以随时发送数据，无需客户端同意。

为了处理这种情况，当服务器发送消息时，必须调用一个函数。

在 React Native 中，我们为此提供了一个`onmessage`函数:

```
ws.onmessage = (e) => {
  // a message was received
  console.log(e.data);
};

```

### 处理错误

当由于互联网连接不良或内部服务器错误而出现错误时，会调用`onerror`函数:

```
ws.onerror = (e) => {
  // an error occurred
  console.log(e.message);
};

```

### 连接关闭

当服务器或客户端关闭连接时，就会调用`onclose`函数。这就像一个析构函数，你可以释放分配的资源。

```
ws.onclose = (e) => {
  // connection closed
  console.log(e.code, e.reason);
};

```

现在我们已经看到了 React Native 中 WebSockets 的完整生命周期，从建立连接到关闭连接。我们可以在单个块中编写所有这些函数，如下所示:

```
var ws = new WebSocket('ws://host.com/path');

ws.onopen = () => {
  // connection opened
  ws.send('something'); // send a message
};

ws.onmessage = (e) => {
  // a message was received
  console.log(e.data);
};

ws.onerror = (e) => {
  // an error occurred
  console.log(e.message);
};

ws.onclose = (e) => {
  // connection closed
  console.log(e.code, e.reason);
};

```

## WebSockets 示例:消息广播应用程序

为了展示 WebSockets 的作用，让我们用 Rect Native 创建一个简单的消息广播应用程序。在我们的演示应用程序中，从一个应用程序发送的消息将被广播到所有连接的应用程序。

我们将[在 Node.js](https://blog.logrocket.com/websockets-tutorial-how-to-go-real-time-with-node-and-react-8e4693fbf843/) 中开发服务器脚本。以下是 WebSockets 服务器代码:

```
const express = require("express");
const app = express();
const http = require("http");
const WebSocket = require("ws");
const server = http.createServer(app);
const wss = new WebSocket.Server({ server });
wss.on("connection", function connection(ws) {
  ws.on("message", function incoming(message, isBinary) {
    console.log(message.toString(), isBinary);
    wss.clients.forEach(function each(client) {
      if (client.readyState === WebSocket.OPEN) {
        client.send(message.toString());
      }
    });
  });
});
app.get("/", (req, res) => {
  res.send("Hello World!");
});
server.listen(8080, () => {
  console.log("Listening to port 8080");
});

```

您可以在 npm 仓库中为 [ws 包](https://www.npmjs.com/package/ws)找到这段代码。

请记住，您需要保持服务器活动。否则，客户端将无法连接。

客户代码

### 现在我们的服务器已经成功运行，是时候创建我们的 React 本地应用程序了。

在顶部，我们将创建一个水平栏来显示连接或断开通知以及错误。在底部，我们将放置一个输入字段和一个提交按钮来通过 WebSockets 发送消息。中间区域的其余部分将用于显示从服务器接收的消息列表。

更多来自 LogRocket 的精彩文章:

* * *

### 这是一个广播应用程序，因此任何设备发送的任何消息都将被广播给所有人。

* * *

让我们来看看代码:

简而言之，在 React 原生应用中实现 WebSockets 的过程如下:

```
import * as React from 'react';
import { Text, View, StyleSheet, TextInput, Button, ScrollView } from 'react-native';

export default function App() {
  const [serverState, setServerState] = React.useState('Loading...');
  const [messageText, setMessageText] = React.useState('');
  const [disableButton, setDisableButton] = React.useState(true);
  const [inputFieldEmpty, setInputFieldEmpty] = React.useState(true);
  const [serverMessages, setServerMessages] = React.useState([]);
  var ws = React.useRef(new WebSocket('ws://w567l.sse.codesandbox.io/')).current;

  React.useEffect(() => {
    const serverMessagesList = [];
    ws.onopen = () => {
      setServerState('Connected to the server')
      setDisableButton(false);
    };
    ws.onclose = (e) => {
      setServerState('Disconnected. Check internet or server.')
      setDisableButton(true);
    };
    ws.onerror = (e) => {
      setServerState(e.message);
    };
    ws.onmessage = (e) => {
      serverMessagesList.push(e.data);
      setServerMessages([...serverMessagesList])
    };
  }, [])
  const submitMessage = () => {
    ws.send(messageText);
    setMessageText('')
    setInputFieldEmpty(true)
  }
  return (
    &lt;View style={styles.container}&gt;
      &lt;View style={{
        height: 30,
        backgroundColor: '#eeceff',
        padding: 5
      }}&gt;
        &lt;Text&gt;{serverState}&lt;/Text&gt;
      &lt;/View&gt;
      &lt;View style={{
        backgroundColor: '#ffeece',
        padding: 5,
        flexGrow: 1
      }}&gt;
        &lt;ScrollView&gt;
          {
            serverMessages.map((item, ind) => {
              return (
                &lt;Text key={ind}&gt;{item}&lt;/Text&gt;
              )
            })
          }
        &lt;/ScrollView&gt;
      &lt;/View&gt;
      &lt;View style={{
        flexDirection: 'row',
      }}&gt;
        &lt;TextInput style={{
            borderWidth: 1,
            borderColor: 'black',
            flexGrow: 1,
            padding: 5,
          }} 
          placeholder={'Add Message'} 
          onChangeText={text => {
            setMessageText(text)
            setInputFieldEmpty(text.length &gt; 0 ? false : true)  
          }}
          value={messageText}
         /&gt;
        &lt;Button
         onPress={submitMessage}
         title={'Submit'} 
         disabled={disableButton || inputFieldEmpty}
        /&gt;
      &lt;/View&gt;

    &lt;/View&gt;
  );
}
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#ecf0f1',
    paddingTop: 30,
    padding: 8,
  },

});

```

React 本地应用程序创建一个新 WebSockets 连接，并将其存储在一个引用变量中，`ws`

1.  应用程序的顶部栏显示了`serverState`变量的值，中间部分显示了存储在`serverMessages`数组中的文本消息，底部栏有一个输入字段，用于存储在`messageText`状态变量中键入的消息
2.  只有当应用程序成功连接到 WebSockets 并且输入框中有一些文本时，提交按钮才会变为活动状态。我们使用`disableButton`和`inputFieldEmpty`变量来控制它。当`disableButton`和`inputFieldEmpty`都是`false`时，提交按钮将被激活
3.  在`useEffect()`钩子中，我们定义了所有的网络套接字函数
4.  当 WebSockets 连接被服务器打开时，调用`onopen`函数。这会将`serverState`变量的值更改为`connected to the server`，将`disableButton`更改为`false`，因此应用程序的顶部栏会显示已连接的消息
5.  当连接关闭时，调用`onclose`函数。这使得`serverState`变为`disconnected message`，而`disableButton`变为`true`。此时，提交按钮不再有效，即使您在输入框中键入消息，因为我们不能向服务器发送消息
6.  如果有错误，调用`onerror`函数，将`serverState`更改为特定的错误消息
7.  当服务器广播消息时，调用`onmessage`函数。这将把接收到的消息追加到`serverMessages`数组中
8.  我们创建了一个`submitMessage`消息来将消息发送给服务器，服务器将消息广播给所有设备
9.  在这里，我嵌入了这个应用程序的两个实例:一个用于 Android，另一个用于 iOS。您可以测试从一台设备发送的信息是否会在两台设备上显示。验证我们在上面嵌入的 Node.js 服务器运行良好，并且没有处于休眠模式。

如果遇到以上设备的问题，可以在这里运行代码[。](https://snack.expo.dev/@akamit/websockets)

结论

## 在本教程中，我们向您展示了在 React 本地应用程序中创建 WebSockets 是多么容易。该代码将在 Android 和 iOS 平台上运行。

我们为这个特定的演示创建了一个简单的广播应用程序，但是它还有很大的扩展空间。例如，您可以设置 id 来区分客户端，并将客户端发送的消息对齐到右侧，将所有其他消息对齐到左侧。这将产生一个完美的聊天应用程序一样的外观和感觉。

您还可以创建一个表单，在开始聊天之前获取详细信息(如用户名),并在收到的消息旁边显示信息。

让我们在评论中知道你在 React Native 中使用 WebSockets 构建了哪些东西。

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)

Start proactively monitoring your React Native apps — [try LogRocket for free](https://lp.logrocket.com/blg/react-native-signup).