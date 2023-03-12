# Node.js 和 MQTT 入门

> 原文：<https://blog.logrocket.com/getting-started-with-node-js-mqtt/>

## 介绍

正如我们可能已经知道的，Node.js 是一个异步和事件驱动的 JavaScript 运行时和引擎，支持当今存在的许多服务器端、网络应用程序。在本文中，我们将探讨 Node.js 与 MQTT 的相互作用，MQTT 是物联网(IoT)世界的发布/订阅(pub/sub)协议和标准。

在本文中，我们计划只讨论重要的公共 MQTT APIs 和函数，并探索 Node.js 中一个简单的发布者和订阅者脚本。

## MQTT 是什么？

1999 年，IBM 的安迪·斯坦福-克拉克和阿尔伦·尼珀设计了 MQTT 协议的最初版本。当时的目标是建立一个能够支持低带宽、轻量级、消耗最少资源的协议，因为通过卫星链路连接的设备非常昂贵。

该规范有两个版本:MQTT 3.1.1 和 MQTT 5.0.0。现在大多数商业 MQTT 经纪人都支持 MQTT 5，但许多物联网托管的云服务只支持 [MQTT 3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html) ，这曾经是最流行和最广泛支持的版本。

强烈建议新的物联网部署使用[版本 5.0.0](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_MQTT_Control_Packet) (于 2018 年批准)，因为其[新功能](https://www.hivemq.com/blog/mqtt5-essentials-part3-upgrade-to-mqtt5-now/)更关注稳健的系统和云原生可扩展性。你可以[在 MQTT 的 GitHub 页面上阅读更多两个版本的亮点和细节差异](https://github.com/mqtt/mqtt.org/wiki/Differences-between-3.1.1-and-5.0)。

### 今天的用法

MQTT 是一种轻量级客户端-服务器协议，实现了发布/订阅消息传输模型，并已用于连接关键物联网应用中的远程对等点/设备、机器对机器(M2M)通信以及许多其他需要平滑接口以有限带宽和卓越性能进行消息传输的领域。这得益于其简单的设计、易用性和开放的[规范](https://mqtt.org/mqtt-specification/)。

MQTT 自 2014 年以来一直由 [OASIS](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901003) 技术指导委员会管理。该委员会监督标准的维护、更新和持续，包括组织用例文档和保护其知识产权。

它依赖于 [TCP/IP](https://en.wikipedia.org/wiki/Internet_protocol_suite) ，这是一个可靠的面向连接的低层网络堆栈，具有独立于数据有效负载结构的传输层，支持有序和正确的双向主机到主机网络通信模型。

发布/订阅模式还允许消息同时从一个应用程序分发到许多不同的应用程序。然而，在这种情况下，发布者和订阅者通常是独立的应用程序(解耦的)，只通过代理或服务器连接。例如，[流行的发布/订阅消息平台 RabbitMQ](https://blog.logrocket.com/kafka-vs-rabbitmq-comparing-node-js-message-brokers/) ，在幕后使用 MQTT。

## MQTT 用例

MQTT 已经发现了许多用例，我们将在下面探讨。

1.  启用消息广播:一个客户端可以向多个其他客户端应用程序发布消息或有效负载，当然，这些客户端应用程序需要事先通过代理上的主题订阅这些消息
2.  提供轻量级和最少的消息头/资源:这允许在消息传输中使用最少的带宽，因此 MQTT 可以有效地扩展以服务数百万的物联网设备
3.  可靠的消息传输或交付是其核心:大多数物联网设备都依赖于此，但在更高的层面上，MQTT 定义了保证消息传输的服务层
4.  构建高度安全的消息传输应用程序:MQTT 在这里大放异彩，因为可以使用 TLS 和其他现代认证机制(如 [OAUTH](https://blog.logrocket.com/implementing-oauth-2-0-in-node-js/) )对消息负载进行加密和保护
5.  确保客户端应用程序有一个持久的连接:在网络连接不可靠的地方，这对于临时消息存储很方便
    1.  与之相关的是，MQTT 有助于减少不良蜂窝网络上的设备重新连接所需的时间
6.  寻找不同行业和领域的应用，包括汽车、石油和天然气、制造、物流、运输和智能家居设备行业

您可以在 MQTT 的文档的[用例页面上找到关于这些的更多细节。最流行的开源家庭自动化项目之一，](https://mqtt.org/use-cases/)[家庭助手](https://github.com/home-assistant/core)，是基于 MQTT 协议的。

## MQTT 的发布/订阅架构

MQTT 协议由代理和一个或多个客户端应用程序组成，代理充当中央服务器，将消息从发布者客户端传送到订阅者客户端，客户端应用程序可以是消息或数据的订阅者或发布者。

经纪人可以说就像邮递员，确保消息被送到各自的收件人手中。它们应该被设计成高度可伸缩和安全的，因为它们是 MQTT 客户机的核心争论点。

在高层次上，代理充当网关，将发布的消息从发布者应用程序路由到适当的订阅者。通常，代理可以部署在多个集群或实例上，并位于负载平衡器之后，以获得更好的容错能力。MQTT 客户机将消息发布到一个中央代理(通常是一个主题)，其他客户机可以在代理上订阅相同的主题来接收这些消息。

![The MQTT pub/sub architecture model](img/e6b853bb9c4b7c9f100e14dd8bad30d4.png)

The MQTT pub/sub architecture model

因此，一个客户端向主题发布消息，而其他客户端订阅该主题，以表明他们对接收有关的消息感兴趣。代理有一种过滤机制，用于控制应该向哪个订户发送消息。这意味着代理检查或过滤特定主题或主题组的订阅者(或订阅者列表),并向他们发送消息。

总之，代理读取、确认和处理从发布者客户端或应用程序发送的消息(这需要确定主题的订阅者并将所有适当的消息发送给他们)。

**注意** : MQTT 依赖于一种过滤消息的方式，通过这种方式，代理将消息发送给对获取消息内容感兴趣的订阅者。消息通常包含一个主题，代理使用该主题来确定如何将特定消息路由到适当的订阅客户端。

基于主题的过滤需要客户端(发布者和订阅者)如何通过主题与代理交互，主题是每个消息负载的一部分。

到目前为止，我们已经使用了一些对一些读者来说可能是新的技术术语。在下一节中，我们将探讨其中的一些术语及其含义。

## 解释了一些 MQTT 技术概念

*   **桥**——两个 MQTT 代理之间的连接
*   **MQTT 客户端**—通过 MQTT 客户端库编写的设备或应用程序，通过安全网络连接到 MQTT 代理；MQTT 客户机可以是发布者，也可以是订阅者应用程序/客户机
*   **Message**–简单地发布消息，可以是缓冲区、字符串或 JSON 对象
*   **主题**–一个字符串标识符，代理使用它来过滤适当的消息并将其发送到连接的客户端。主题名称通常以这种带有分隔符的分层方式构成，称为主题级别
    *   请注意，消息必须包含一个主题，代理可以使用该主题将有效负载适当地路由到感兴趣的客户端
    *   示例主题名称:`mytopic/homeautomation/closedoor`
*   **发布者**–发布者客户端将数据或消息分发到服务器/代理上的主题，以供其他可能对获取这些消息感兴趣的订阅者客户端使用
*   **物联网(IoT)**–一个由嵌入式系统、自动化设备、无线网络和控制机制组成的互联设备世界
*   **解耦**–在这种情况下，解耦意味着发布者/订阅者只需要知道代理的主机名/IP 和端口，这与传统的客户端-服务器架构不同，在传统的客户端-服务器架构中，客户端和服务器通过端点/API 直接通信，通常采用 URI 格式

## 在应用程序级别使用 MQTT

现在，让我们看看 MQTT 在应用程序级别上是如何工作的。我们将使用 MQTT 的 Node.js 客户端库， [mqtt.js](https://www.npmjs.com/package/mqtt) 。

MQTT.js 是针对 [MQTT](http://mqtt.org/) 协议的开源 JavaScript 库，适用于 Node.js 和浏览器。通常，这个库可以用于在 MQTT 代理上发布消息和订阅主题。

**关于 MQTT.js 库的注意事项**

*   支持 ES 模块和 Common.js 样式的文件导入
*   它有一个基于承诺的 API 接口，因为 MQTT 本身是异步工作的
*   MQTT.js 默认为老的 MQTT v3.1.1，支持老的代理，但是目前最新的版本是 5.0
*   MQTT 客户机带有一个内置的错误处理程序，当程序员无法处理代码中的错误时，这个程序就派上了用场

请注意，还有针对各种编程语言和主要操作系统(Linux、Windows 和 macOS)的客户端库。

## 连接/断开与 MQTT 代理/服务器的连接

客户端连接总是由代理/服务器处理，因为 MQTT 订阅者和发布者是独立的、解耦的应用程序。正如我们前面提到的，发布者和订阅者都是 MQTT 客户机，因此需要连接到同一个代理/服务器。

客户端从不直接相互连接；连接通常在一个客户端和代理之间通过 TCP/IP 进行。其他 MQTT 实现或变体也通过 UDP 进行连接( [MQTT < -SN](https://en.wikipedia.org/wiki/MQTT#Overview) )。
经纪人负责:

*   接收所有消息
*   通过确定哪个订阅客户端订阅了每个消息来过滤适当的消息
*   保持客户端之间的连接/会话
*   对客户端进行身份验证和授权
*   将消息发送到正确的客户端

代理应该易于扩展并集成到不同的后端系统中。它们可以很容错，因为它们是发布者/订阅者通信的最关键点。

第一次连接到代理时，客户机发送一条`CONNECT`消息。一旦启动，代理返回一个`CONNACK`消息和一个状态代码。另一个需要注意的重要事情是，代理总是保持连接活动或打开，除非客户端发送断开事件或他们的互联网连接中断。

现在有一些流行的、免费托管的 MQTT 经纪人。Eclipse 的 [Mosquitto](https://mosquitto.org/) 就是其中之一，它运行在所有主流操作系统上。

还有其他基于云或托管的商业经纪人，如 [HIVEMQ](http://www.hivemq.com/) 。如果我们不打算安装和管理自己的代理，它们通常会派上用场。你可以[在 MQTT 网站上找到一个好的 MQTT 经纪人](http://www.mqtt-dashboard.com/)进行快速测试。

## 安装我们的客户端库

要安装 MQTT.js，请运行以下命令:

```
npm install mqtt --save  

```

注意，MQTT.js 捆绑了一个命令来与代理交互。为了让 MQTT 协议接口在您的系统路径上可用，我们可以全局安装它:

```
npm install mqtt -g 

```

在安装结束时，我们的`package.json`文件应该如下所示:

```
//package.json
{
"name": "mqtt-demo", 
  "version": "1.0.0", 
  "description": "A node.js and MQTT demo", 
  "main": "index.js", 
  "scripts": { 
    "start-publisher": "nodemon publisher.js", 
    "start-consumer": "nodemon subscriber.js" 
  }, 
  "keywords": [ 
    "Node.js", 
    "MQTT", 
    "Pub/Sub", 
    "IoT", 
    "message", 
    "transport" 
  ], 
  "author": "Alexander Nnakwue", 
  "license": "MIT", 
  "dependencies": { 
    "dotenv": "^10.0.0", 
    "mqtt": "^4.3.2" 
  }, 
  "devDependencies": { 
    "nodemon": "^2.0.15" 
  } 
} 

```

要测试程序，可以在一个终端窗口上运行发布程序，在另一个终端窗口上运行订阅程序。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 创建 MQTT 发布客户机

现在，让我们创建一个发布消息的 MQTT 客户机。为此，我们可以导入 MQTT.js 库并使用 connect 方法。

```
const mqtt = require('mqtt') 
require('dotenv').config() 

const clientId = 'mqttjs_' + Math.random().toString(8).substr(2, 4) 
const client  = mqtt.connect(process.env.BROKER_URL, {clientId: clientId}); 

```

注意，我们已经将`BROKER_URL`添加到我们的环境文件中。正如我们所见，`connect`方法接受一个给定的 URL(代理服务器 URL)和一个可选的服务器选项对象。接受的协议可以是`MQTT`、`ws`、`wss`、`tcp`、`tls`等等。connect 方法返回一个连接的客户端。

为了在 MQTT 客户机连接断开时尝试重新连接，我们可以将`reconnectPeriod`选项(两次重新连接之间的时间间隔)设置为大于零。默认值是`1`秒，这意味着在断开连接后，它几乎会立即尝试重新打开连接。

```
const client = mqtt.connect(process.env.BROKER_URL, {clientId: clientId, clean: false, reconnectPeriod: 1}); 

```

如果我们将`reconnectPeriod`客户端选项的值设置为`0`，当连接断开时，重新连接将被禁用和终止。

当重新订阅选项设置为默认值(`true`)时，当连接断开时，客户端可以自动重新连接并重新订阅之前订阅的主题。尤其是对于自托管代理，我们可能希望使用用户名和密码进行身份验证。服务器选项对象的更多细节可以在 MQTT GitHub 上找到[。](https://github.com/mqttjs/MQTT.js#mqttclientstreambuilder-options)

## 发布数据和消息

一旦连接到代理，MQTT 客户机几乎可以立即发送消息。发布事件接受一个消息负载和一个主题名，代理可以用它来标识订阅方。此外，还有一个选项回调，用于检查错误或消息包何时被传输。

发布的消息类型或发送的数据包具有以下属性:

*   `topicName`
*   `dupFlag`
*   `qos`
*   `payload`
*   `packetId`或`messageId`
*   `retainFlag`

命令如下所示。

```
{ 
  cmd: 'publish', 
  topic: 'test/connection', 
  payload: '{"1":"Hello world","2":"Welcome to the test connection"}', 
  qos: 1, 
  retain: true, 
  messageId: 12041, 
  dup: false 
} MQTT publish packet 

```

当客户端向代理发送消息时，代理根据开发人员设置的一些标准处理消息。这应该包括 QoS 级别，它决定了消息到达目标接收者时的保证类型，并确保消息传递保证。

处理阶段通常需要阅读、确认和识别已订阅主题的客户。最后一步是向订阅的客户端发送消息。

下面是`publisher.js`文件的完整代码，包括一些公共方法/API:

```
//publisher.js 
const mqtt = require('mqtt') 
require('dotenv').config() 

//the client id is used by the MQTT broker to keep track of clients and and their // state
const clientId = 'mqttjs_' + Math.random().toString(8).substr(2, 4) 
const client  = mqtt.connect(process.env.BROKER_URL, {clientId: clientId, clean: false}); 

// console.log(process.env.BROKER_URL, 'client', clientId) 

const topicName = 'test/connection' 

client.on("connect",function(connack){   
   console.log("client connected", connack); 
// on client connection publish messages to the topic on the server/broker  
  const payload = {1: "Hello world", 2: "Welcome to the test connection"} 
  client.publish(topicName, JSON.stringify(payload), {qos: 1, retain: true}, (PacketCallback, err) =&gt; { 

      if(err) { 
          console.log(err, 'MQTT publish packet') 
      } 
  }) 

  //assuming messages comes in every 3 seconds to our server and we need to publish or process these messages 
  setInterval(() =&gt; console.log("Message published"), 3000); 
}) 

client.on("error", function(err) { 
    console.log("Error: " + err) 
    if(err.code == "ENOTFOUND") { 
        console.log("Network error, make sure you have an active internet connection") 
    } 
}) 

client.on("close", function() { 
    console.log("Connection closed by client") 
}) 

client.on("reconnect", function() { 
    console.log("Client trying a reconnection") 
}) 

client.on("offline", function() { 
    console.log("Client is currently offline") 
})  

```

接下来，我们可以实现订阅客户端，它使用主题上的消息。

## 订阅消息

为了接收关于我们感兴趣的主题的消息，客户端调用代理的`subscribe`事件。订阅的消息通常包含消息包负载，如下所示。

```
//stdout
Packet { 
  cmd: 'publish', 
  retain: true, 
  qos: 0, 
  dup: false, 
  length: 73, 
  topic: 'test/connection', 
  payload: &lt;Buffer 7b 22 31 22 3a 22 48 65 6c 6c 6f 20 77 6f 72 6c 64 22 2c 22 32 22 3a 22 57 65 6c 63 6f 6d 65 20 74 6f 20 74 68 65 20 74 65 73 74 20 63 6f 6e 6e 65 63 ... 6 more bytes&gt; 
} {"1":"Hello world","2":"Welcome to the test connection"}``` 

//
[ { topic: 'test/connection', qos: 0 } ] granted 

```

我们也可以将主题存储为通配符，而不是将主题名称存储为常规的分隔字符串，这样订阅者就可以轻松地订阅主题模式，而不是一次只订阅一个主题。发布者和订阅者客户机需要事先知道模式的主题名。

请注意，订阅客户端需要事先知道它们将接收的数据的结构，这样它们才能适当地处理数据。发布者以特定的格式向代理中的特定主题发送消息，该消息的预定订阅者需要知道该数据是如何构造的，以便他们能够在不破坏应用程序的情况下正确地处理它。

下面的文件显示了订阅者客户端的完整代码。

```
// subscriber.js 
const mqtt = require('mqtt') 
const client = mqtt.connect("mqtt://test.mosquitto.org") 
const topicName = 'test/connection' 

// connect to same client and subscribe to same topic name  
client.on('connect', () =&gt; { 
    // can also accept objects in the form {'topic': qos} 
  client.subscribe(topicName, (err, granted) =&gt; { 
      if(err) { 
          console.log(err, 'err'); 
      } 
      console.log(granted, 'granted') 
  }) 
}) 

// on receive message event, log the message to the console 
client.on('message', (topic, message, packet) =&gt; { 
  console.log(packet, packet.payload.toString()); 
  if(topic === topicName) { 
   console.log(JSON.parse(message)); 
  } 
}) 
client.on("packetsend", (packet) =&gt; { 
    console.log(packet, 'packet2'); 
}) 

```

## 附加 MQTT 特性

MQTT 的一些特性描述如下。

### 保留的邮件

保留消息是保留标志/选项设置为`true`的 MQTT 消息。默认情况下，当代理/服务器收到没有订阅者的主题的消息时，这些消息将被丢弃。但是，MQTT 有一种通过设置一个标志来保留这些消息的机制，这个标志告诉发布者保留消息。请注意，代理只为每个主题存储一条保留的消息。

### 广泛的身份验证和数据安全支持

MQTT 支持各种身份验证方法和数据安全机制，包括 TLS 和 [OAuth](https://blog.logrocket.com/implementing-oauth-2-0-in-node-js/) ，它们通常在 MQTT 代理上配置。因此，这意味着实现这些服务器的客户机需要遵守所定义的机制。

默认情况下，MQTT 支持一种重连接机制，这种机制在低连接性区域建立持久连接。这对于存储消息很重要，但是与传统的排队系统不同，代理并不专门存储消息。MQTT 通过确保客户机会话是持久的并且 QoS 级别高于`0`来存储消息。

### 服务质量

为了处理发布/订阅系统中的典型挑战，MQTT 实现了三个服务质量(QoS)级别。这三个级别包括 0、1 和 2。它们决定了消息到达预期接收者(客户或经纪人等)的保证类型。

为了支持可靠的消息传递，该协议支持三种不同类型的服务质量消息:

*   0–最多一次
*   1–至少一次
*   2–恰好一次

为了存储消息，客户端必须订阅服务质量高于`0`的主题。

### 数据不可知

MQTT 是数据不可知的，客户机的用例决定了有效负载的结构。因此，可以发送任何类型的消息，包括图像、编码文本、加密数据等等。

**注意**:默认未加密的 MQTT 端口是`1883`。加密的 TCP/IP 端口`8883`也支持在 SSL 上使用 MQTT。

## 结论

MQTT 提供了双向发布/订阅消息模型，适用于网络带宽有限的区域。服务独立于我们的主要应用程序，因为它们是解耦的，代理或服务器可以单独扩展。

这里是 GitHub 上利用 MQTT 协议的开源项目列表。我们的演示应用程序的细节可以在 [GitHub](https://github.com/firebase007/node-mqtt-pub-sub-demo) 上找到，你也可以在 [GitHub](https://github.com/mqtt) 上找到 MQTT 标准的细节。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.