# 使用 Node.js 为 IPFS 内容博客创建 HTTP 代理

> 原文：<https://blog.logrocket.com/using-node-js-http-proxy-ipfs-content/>

在本文中，我们将演示如何创建一个 Node.js 服务器来通过 clearnet 访问 IPFS 的内容。在我们解释如何做到这一点之前，让我们定义一些关键术语，并为这一点的重要性创造一些背景。

## 晴空是什么？

清算所本质上是公众可访问的互联网。它是指使用所有浏览器支持的标准协议通过公共网络消费的所有 web 内容，如 HTTP、 [WebSocket(用于实时通信)](https://blog.logrocket.com/websockets-tutorial-how-to-go-real-time-with-node-and-react-8e4693fbf843/)和 WebRTC(用于大多数音频和视频流平台)。

这些协议通常需要中央服务器和服务提供商来工作，这可能会成为看门人的媒介，特别是在限制言论自由和隐私的国家。

### 晴空万里

还有许多其他非标准网络协议，尽管它们可能不被所有浏览器支持。例子包括:

*   洋葱路由(Onion routing):一种支持网络匿名通信的技术，主要在 Tor 项目中实现
*   Gemini:一种通信协议，努力成为 Gopher 和 HTTP 的替代品。有许多可用的 Gemini 客户端
*   **星际文件系统(IPFS)** :共享数据的协议和对等网络。流行的基于 Chromium 的勇敢浏览器支持 IPFS

## IPFS 解释道

与 BitTorrent 类似，IPFS 是一种用于在分布式文件系统中存储和共享数据的协议。然而，关键的区别在于，IPFS 的目标是创建一个单一的全球网络。在 IPFS，托管的单个文件被分配一个内容 ID (CID ),用于访问它。

为了说明区别，在 HTTP 中，我们使用类似于`[https://www.google.com](https://www.google.com)`的 URI，然后将它发送到 DNS 服务器。服务器识别资源的 IP 地址并为资源提供服务。但是，如果该主机关闭或不可访问，该资源就……消失了。

在 IPFS，我们使用类似`ipfs://xjd9809bnuiue900sdfnuiwerwwer`的 CID，它是通过搜索网络中所有参与者托管的分布式哈希表(DHT)来识别的。不是识别一个单一的资源提供者，而是识别具有资源拷贝的所有主机，并且我们从所有主机下载部分资源。

和任何互联网协议一样，IPFS 的设计有优点也有缺点。

### IPFS 的散文

*   一旦内容被添加到 DHT 中，只要有一台主机连接到 IPFS 网络并带有该文件的副本，就可以在网络上访问该文件
*   随着时间的推移，文件的可访问性会增加，因为访问该文件的每个用户都会成为检索该文件的另一个主机
*   没有一个外部实体可以单独删除已经添加到网络中的文件

### IPFS 骗局

*   当你第一次向 IPFS 添加内容时，你是唯一的主机。因此，可及性从一开始就受到限制
*   您不能改变文件，因此如果您需要修复某个类型，您必须添加一个带有新 CID 的文件新副本，并提醒其他用户使用新 CID
*   目前并非所有浏览器都支持

## IPFS 入门

让我们安装 IPFS CLI，并添加一些内容到 IPFS。随着我们的进展，你可以参考 IPFS 的文档。

首先，安装 IPFS。对于 Linux 和 Mac，可以使用`brew install ipfs`用 Homebrew 安装。当它完成时，确保阅读输出中的重要信息；对于 Linux，它应该给出运行守护进程的确切路径。

安装完成后，运行`ipfs init`,再次确保阅读输出中的一些重要细节——您可能希望将它保存在安全的地方。输出应该共享一个 cat 命令来查看文档；运行命令，确认一切都已设置好。

用`ipfs init`启动守护进程。要向 IPFS 添加文件，只需使用命令`ipfs add FILENAME`，您将收到 CID。就这么简单！

### 消费 IPFS 内容

要使用 IPFS 内容，只需在命令行中运行`ipfs cat /ipfs/CONTENTID`来检索内容。

要通过浏览器浏览 IPFS 的内容，你需要使用 [Brave](https://brave.com/) ，它允许你浏览来自 IPFS 的内容。在浏览器中，使用这个模式`ipfs://CONTENTID`来访问内容。例如，这里有一个“你好，世界！”我发布到 IPFS 的文本文件:`ipfs://QmWATWQ7fVPP2EFGu71UkfnqhYXDYH566qy47CnJDgvs8u`。

## 通过 HTTP 传送 IPFS 内容

IPFS 很酷，但大多数日常互联网用户不想下载替代浏览器或使用 CLI 工具浏览网络资源。因此，也许你想通过创建一个在主流浏览器中获取内容的代理应用程序，向 clearnet 用户提供你的 IPFS 内容。让我们用 Node.js 来做这件事。

> **注意**，这个演示应用程序必须在运行 IPFS 服务器的机器上运行。这意味着您将希望使用虚拟专用服务器(VPS)进行部署，在这里您可以运行与 Node.js 应用程序并行的 IPFS 服务器。

### 设置应用程序

首先创建一个新文件夹——您可以将其命名为`ipfs-proxy`或类似的名称——并将目录更改到该文件夹中。通过运行`touch server.js`创建一个`server.js`文件，然后用`npm init -y`启动一个新的 Node.js 项目。最后，通过运行`npm install fastify ipfs-http-client`安装 Fastify(尽管[任何 web 框架都可以](https://blog.logrocket.com/comparing-top-node-js-frameworks-frontend-developers/))和 ipfs-http-client。

### 让节点服务器运行

将以下代码添加到您的`server.js`文件中:

```
// import fastify
const fastify = require("fastify");

// create the server object
const server = fastify({ logger: true });

// example route
server.get("/", async (request, reply) => {
  return { message: "The Server is Working" };
});

// start server function
const start = async () => {
  await server.listen(3000).catch((err) => {
    fastify.log.error(err);
    process.exit(1);
  });
  console.log("Listening on port 3000")
};

// turn server on
start();
```

运行文件`node server.js`，在浏览器中转至`localhost:3000`，并确保看到该消息。使用终端中的`Control+C`关闭服务器。

## [H3]使用 ipfs-http-客户端

我们需要将我们的`server.js`更新为下面的代码，但是首先让我们看一下我们在做什么。

首先，我们将 ipfs-http-client 库作为`ipfs`导入。然后我们创建一个连接到`localhost:5001`的客户端，这是我们的本地 IPFS 服务器应该运行的默认端口。

我们使用客户端的`get`方法来获取数据 CID。该函数返回一个包装在`AsyncGenerator`中的`Uint8Array`字节(有趣！).

我们使用一个`for await`循环遍历生成器，然后使用一个文本编码器将字节数组解码成一个字符串。在循环之后，结果文本中有几个空字符，所以我们使用`string.replace`删除它们。最后，我们将结果字符串作为 JSON 发送回去。

```
// import fastify
const fastify = require("fastify");
// import ipfs-http-client
const ipfs = require("ipfs-http-client")

// connect to local ipfs node
const client = ipfs.create()

// create the server object
const server = fastify({ logger: true });

// example route
server.get("/", async (request, reply) => {

  // fetch the content from ipfs
  const result = await client.get("QmWATWQ7fVPP2EFGu71UkfnqhYXDYH566qy47CnJDgvs8u")

  // create a string to append contents to
  let contents = ""

  // loop over incoming data
  for await(const item of result){
      // turn string buffer to string and append to contents
      contents += new TextDecoder().decode(item)
  }

  // remove null characters
  contents = contents.replace(/\0/g, "")

  // return results as a json
  return { message: contents };
});

// start server function
const start = async () => {
  await server.listen(3000).catch((err) => {
    fastify.log.error(err);
    process.exit(1);
  });
  console.log("Listening on port 3000")
};

// turn server on
start();
```

请注意，结果并不是世界上最友好的。结果字符串包括 CID 和时间戳；换句话说，除了我的“Hello，World！”文件。如果这是一个 HTML 文件，我可能需要做一些额外的清理工作来删除所有非 HTML 文件，但至少我们得到了内容。

因此，从理论上讲，我们可以在安装了 IPFS 的 VPS 上托管这个应用程序，clearnet/HTTP 上的用户可以看到我们的内容，而不必下载特殊的浏览器或命令行工具。

## 结论

IPFS 提出了一些非常有趣的想法，关于互联网如何变得更加分散和自由，但这确实是以牺牲一些便利为代价的。然而，通过巧妙使用 Node.js，我们可以开始为大众弥合这一差距。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.