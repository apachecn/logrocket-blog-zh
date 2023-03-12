# 用 Node.js - LogRocket Blog 构建视频流服务器

> 原文：<https://blog.logrocket.com/build-video-streaming-server-node/>

在构建 web 应用程序时，开发人员经常要处理不同类型的媒体，其中一些可能很复杂。在本文中，我们将使用 Node.js 创建自己的视频流服务器。

如果您一步一步地遵循本教程，您将能够使用 Node.js 构建一个视频流服务器，并将其集成到您自己的项目中。要了解这篇文章，您可以查看 GitHub repo 。

![Video Streaming Server Node](img/4003f4173ac07bbe6c2e88d2a978d200.png)

## 项目概述

在我们开始为我们的项目编码之前，让我们回顾一下我们的应用程序将如何工作。在上图中，浏览器在左边，服务器在右边。在您的站点上，您将拥有一个 HTML5 `video`元素，其源指向`/video`端点。

首先，`video`元素向服务器发出请求，然后头提供视频中所需的字节范围。例如，在视频开始时，请求的范围将从第 0 个字节开始，因此是`0-`。服务器将用一个`206` HTTP 状态来响应，表明它正在返回部分内容和适当的头响应，包括范围和内容长度。

响应头向`video`元素表明视频是不完整的。因此，`video`元素将播放它目前已经下载的内容。当这种情况发生时，`video`元素将继续发出请求，循环将继续，直到没有字节了。

### 应用利弊

现在我们已经了解了我们的应用程序将如何工作，让我们来考虑一下遵循这种方法的利弊。

正如您从应用程序概述中可能已经猜到的，我们的流服务器实现起来相当简单。本质上，我们正在创建一个文件系统并将其返回给客户端。我们的服务器将允许我们选择整个视频的时间框架，并决定发送回多大的有效载荷。对于我的，我选择了 1MB，但你可以自由地摆弄它。

然而，由于我们的应用程序的简单性，服务器和视频播放器不能像我们希望的那样很好地协同工作。本质上，视频播放器只会请求你正在播放的那部分视频，而不会考虑你已经请求了什么。很可能你会一次又一次地请求一些相同的资源。

## 入门指南

首先，我们将设置一个新文件夹并初始化 npm:

```
npm init

```

现在，安装 Express 和 nodemon:

```
npm install --save express nodemon

```

假设您的`video`元素是一个空文件夹，您需要生成一个 HTML 文件，如下所示:

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Video Streaming With Node</title>
        <style>
            body {
                margin: 5% auto;
                max-width: 100%;
                background-color: rgb(14, 14, 14);
                padding-top: 10%;
                padding-left: 35%;
            }
        </style>
    </head>
    <body>
        <video id="videoPlayer" width="50%" controls muted="muted" autoplay>
            <source src="/video" type="video/mp4" />
        </video>
    </body>
</html>

```

## 编写`/video`端点

接下来，我们将编写`/video`端点。最终，当您测试上面的 HTML 代码时，您应该在屏幕上看到一个`media`元素。

要做到这一点，我们首先需要创建一个新的 JavaScript 文件来存放我们所有的函数。在这个新文件中，我们将导入 Express 和代表文件系统的`fs`。`fs`将创建一个文件流，然后在`/video`端点将其返回给客户端。运行下面的代码:

```
const express = require("express");
const app = express();
const fs = require("fs");

app.get("/", function (req, res) {
    res.sendFile(__dirname + "/index.html");
});

 // more code will go in here just befor the listening function

app.listen(8000, function () {
    console.log("Listening on port 8000!");
});

```

现在，我们将为`/video`端点创建一个函数。您需要确保有一个范围标题。否则，你将无法告诉客户端你想发回视频的哪一部分。`if`语句对此进行处理，返回一个`400 Error`，提醒客户端它需要一个范围头:

```
app.get("/video", function (req, res) {
    const range = req.headers.range;
    if (!range) {
        res.status(400).send("Requires Range header");
    }
});

```

我们还需要提供视频的路径和大小。只要你的视频和 JavaScript 文件在同一个目录下，就没必要加一堆斜杠。但是，如果视频与 JavaScript 文件不在同一个目录中，您需要提供相对路径，如下例所示:

```
const videoPath = "Chris-Do.mp4";
const videoSize = fs.statSync("Chris-Do.mp4").size;

```

现在，新文件应该类似于下面的代码块:

```
const express = require("express");
const app = express();
const fs = require("fs");

app.get("/", function (req, res) {
    res.sendFile(__dirname + "/index.html");
});
app.get("/video", function (req, res) {
    const range = req.headers.range;
    if (!range) {
        res.status(400).send("Requires Range header");
    }
    const videoPath = "Chris-Do.mp4";
    const videoSize = fs.statSync("Chris-Do.mp4").size;
});

app.listen(8000, function () {
    console.log("Listening on port 8000!");
});

```

## 解析范围

接下来，我们将解析这个范围，如上面代码块的第`10`行所示。我一次给它 1MB，这就是所谓的块大小:

```
const CHUNK_SIZE = 10 ** 6; // 1MB
const start = Number(range.replace(/\D/g, ""));

```

现在，我们将解析范围头中的起始字节。由于它是一个字符串，您需要使用下面的代码行将其转换为一个数字:

```
const start = Number(range.replace(/\D/g, ""));

```

注意，我从最后一个字节的`videoSize`中减去 1，因为这是最后一个字节。如果一个视频有 100 个字节，那么第 99 个字节就是最后一个，因为在计算机科学中我们是从零开始计数的。

现在，你需要计算你将发送回来的结束字节。首先，将 1MB 的块大小添加到起始块中。随着服务器继续向起始块发回 1MB 的数据，最终，发送的字节总数可能会超过视频本身的大小。

在这种情况下，您需要返回视频大小。您可以使用`Math.min`函数来实现，该函数取给定的两个参数中的最小值，总结如下:

```
const end = Math.min(start + CHUNK_SIZE, videoSize - 1);

```

## 创建响应标题

现在，我们需要创建将要返回的响应头。首先用`end-start + 1`计算内容长度。

然后，我们将创建`headers`对象。在内容范围内，您需要使用起始字节、结束字节和视频大小，如下所示:

```
const headers = {
    "Content-Range": `bytes ${start}-${end}/${videoSize}`,
    ... // this ... just indicates that there is more code here. 
        // it is not part of code.
}

```

有了上面的代码，视频播放器就可以根据视频本身的大小知道它走了多远。之后，我们将指定我们将发回的数据类型。添加内容长度和视频类型。您的`headers`对象应该看起来像下面的代码:

```
const headers = {
    "Content-Range": `bytes ${start}-${end}/${videoSize}`,
    "Accept-Ranges": "bytes",
    "Content-Length": contentLength,
    "Content-Type": "video/mp4",
};

```

现在，我们需要为请求写一个响应。我使用`206`作为状态，表示我正在发送部分内容。这样，您还应该按如下方式设置标题:

```
// HTTP Status 206 for Partial Content
res.writeHead(206, headers);

```

我们需要使用文件系统库来创建`readstream`，使用视频路径作为参数，使用`start`和`end`作为`options`对象中的选项:

```
const videoStream = fs.createReadStream(videoPath, { start, end });

```

自己不做任何事情。我们需要将它传输到函数开始时的响应中:

```
videoStream.pipe(res);

```

如果您一直按部就班地操作，您的文件应该类似于下面的代码:

```
const express = require("express");
const app = express();
const fs = require("fs");

app.get("/", function (req, res) {
    res.sendFile(__dirname + "/index.html");
});

app.get("/video", function (req, res) {
    const range = req.headers.range;
    if (!range) {
        res.status(400).send("Requires Range header");
    }
    const videoPath = "Chris-Do.mp4";
    const videoSize = fs.statSync("Chris-Do.mp4").size;
    const CHUNK_SIZE = 10 ** 6;
    const start = Number(range.replace(/\D/g, ""));
    const end = Math.min(start + CHUNK_SIZE, videoSize - 1);
    const contentLength = end - start + 1;
    const headers = {
        "Content-Range": `bytes ${start}-${end}/${videoSize}`,
        "Accept-Ranges": "bytes",
        "Content-Length": contentLength,
        "Content-Type": "video/mp4",
    };
    res.writeHead(206, headers);
    const videoStream = fs.createReadStream(videoPath, { start, end });
    videoStream.pipe(res);
});

app.listen(8000, function () {
    console.log("Listening on port 8000!");
});

```

在结束之前，您只需要将`"start": "nodemon index.js"`添加到您的`package.json`文件中:

```
"scripts": {
      "start": "nodemon index.js" //this is the main line you need to add
},

//note that the index.js is just the name of my file. yours might be named differently

```

要查看最终输出，只需运行`npm start`。

## 结论

在本教程中，我们学习了使用 Node.js 构建我们自己的视频流服务器。首先，我们深入介绍了项目架构，然后我们阐述了遵循简单方法的利弊。然后，我们通过创建`/video`端点、解析范围和创建响应头来构建我们的应用程序。

通过遵循本教程中的步骤，您可以构建自己的 Node.js 视频流服务器，并将其集成到自己的应用程序中。我希望你喜欢这篇文章！

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.