# 使用 Node.js streams 

> 原文：<https://blog.logrocket.com/working-node-js-streams/>

## 介绍

流是大多数 Node.js 应用程序依赖的主要特性之一，尤其是在处理 HTTP 请求、读/写文件和进行套接字通信时。流是非常可预测的，因为在使用流时，我们总是可以预期数据、错误和结束事件。

本文将教节点开发人员如何使用流来有效地处理大量数据。当节点开发人员必须处理大型数据源时，这是他们面临的一个典型的现实挑战，一次处理所有这些数据可能是不可行的。

本文将涵盖以下主题:

## 流的类型

以下是 Node.js 中的四种主要类型的流:

*   可读流:可读流负责从源文件中读取数据
*   可写流:可写流负责将特定格式的数据写入文件
*   双工流:双工流是实现可读和可写流接口的流
*   转换流:转换流是一种双工流，它读取数据，转换数据，然后以指定的格式写入转换后的数据

## 何时使用 Node.js 流

当我们处理太大而无法读入内存并作为一个整体处理的文件时，流就派上了用场。

例如，如果您正在处理一个视频会议/流应用程序，该应用程序需要以较小的块传输数据，以支持大容量 web 流，同时避免网络延迟，那么 Node.js streams 是一个不错的选择。

## 配料过程

批处理是一种常见的数据优化模式，它包括以块的形式收集数据，将这些数据存储在内存中，并在所有数据都存储在内存中后将它们写入磁盘。

让我们来看看典型的配料过程:

```
const fs = require("fs");
const https = require("https");
const url = "some file url";
https.get(url, (res) => {
  const chunks = [];
  res
    .on("data", (data) => chunks.push(data))
    .on("end", () =>
      fs.writeFile("file.txt", Buffer.concat(chunks), (err) => {
        err ? console.error(err) : console.log("saved successfully!");
      })
    );
});

```

在这里，所有的数据都被放入一个数组中。当数据事件被触发时，一旦“结束”事件被触发，表明我们完成了接收数据，我们就使用`fs.writeFile`和`Buffer.concat`方法将数据写入文件。

批处理的主要缺点是内存分配不足，因为所有数据在写入磁盘之前都存储在内存中。

在收到数据时写入数据是处理大文件的一种更有效的方法。这就是流派上用场的地方。

## 在 Node.js 中编写流

Node.js `fs`模块公开了一些原生的[节点流 API](https://nodejs.org/api/stream.html) ，可以用来编写流。

我们将讨论可读、可写和转换流。如果你想了解更多，你可以阅读我们关于 Node.js 中的[双工流的博文。](https://blog.logrocket.com/creating-duplex-streams-nodejs/)

### 编写可写流

```
const fs = require("fs");
const fileStream = fs.createWriteStream('./file.txt')
for (let i = 0; i <= 20000; i++) {
  fileStream.write("Hello world welcome to Node.js\n"
  );
}

```

使用`createWriteStream()`方法创建可写流，该方法需要将文件的路径作为参数写入。
运行上面的代码片段会在你当前的目录下创建一个名为`file.txt`的文件，里面有 20，000 行`Hello world welcome to Node.js`。

### 组成可读流

```
const fs = require("fs");
const fileStream = fs.createReadStream("./file.txt");
fileStream
  .on("data", (data) => {
    console.log("Read data:", data.toString());
  })
  .on("end", () => { console.log("No more data."); });

```

这里，`data`事件处理程序将在每次读取一大块数据时执行，而`end`事件处理程序将在不再有数据时执行。
运行上面的代码片段将从`./file.txt`向控制台记录 20，000 行`Hello world welcome to Node.js`字符串。

### 合成转换流

转换流具有可读和可写的特性。它允许处理输入数据，然后以处理后的格式输出数据。

为了创建转换流，我们需要从 Node.js 流模块导入`Transform`类。`transform`流构造器接受包含数据处理/转换逻辑的函数:

```
const fs = require("fs");
const { Transform } = require("stream");
const fileStream= fs.createReadStream("./file.txt");
const transformedData= fs.createWriteStream("./transformedData.txt");

const uppercase = new Transform({
  transform(chunk, encoding, callback) {
    callback(null, chunk.toString().toUpperCase());
  },
});

fileStream.pipe(uppercase).pipe(transformedData);

```

这里，我们创建了一个新的`transform`流，其中包含一个需要三个参数的函数:第一个是数据的`chunk`，第二个是`encoding`(如果数据块是一个字符串，这很方便)，后面是一个用转换后的结果调用的`callback`。

运行上面的代码片段会将`./file.txt`中的所有文本转换为大写，然后将其写入`transformedData.txt`。
如果我们运行这个脚本并打开结果文件，我们会看到所有的文本都转换成了大写。

## 管道流

管道流是用于将多个流连接在一起的重要技术。当我们需要将复杂的处理分解成更小的任务并按顺序执行它们时，它就派上了用场。Node.js 为此提供了一个本地的`pipe`方法:

```
fileStream.pipe(uppercase).pipe(transformedData);

```

有关上述代码片段的更多详细信息，请参考撰写转换流下的代码片段。

## 处理 Node.js 流时出错

### 使用管道的错误处理

Node 10 引入了管道 API 来增强 Node.js 流的错误处理。`pipeline`方法接受任意数量的`streams`，后跟一个`callback`函数，该函数处理`pipeline`中的任何错误，并将在`pipeline`完成后执行:

```
pipeline(...streams, callback)

const fs = require("fs");
const { pipeline, Transform } = require("stream");

pipeline(
  streamA,
  streamB,
  streamC,
  (err) => {
    if (err) {
      console.error("An error occured in pipeline.", err);
    } else {
      console.log("Pipeline execcution successful");
    }
  }
);

```

使用`pipeline`时，应该按照需要执行的顺序依次传递一系列流。

### 使用管道处理错误

我们还可以使用管道来处理流错误，如下所示:

```
const fs = require("fs");
const fileStream= fs.createReadStream("./file.txt");
let b = otherStreamType()
let c = createWriteStream()
fileStream.on('error', function(e){handleError(e)})
.pipe(b)
.on('error', function(e){handleError(e)})
.pipe(c)
.on('error', function(e){handleError(e)});

```

如上面的代码片段所示，我们必须为每个创建的`pipe`创建一个`error`事件处理程序。这样，我们可以跟踪错误的上下文，这在调试时很有用。这种技术的缺点是冗长。

## 结论

在本文中，我们探索了 Node.js 流，何时使用它们，以及如何实现它们。

Node.js 流的知识是必不可少的，因为在处理大型数据集时，它们是一个很好的工具。查看 [Node.js API 文档](https://nodejs.org/api/stream.html)了解更多关于流的信息。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.