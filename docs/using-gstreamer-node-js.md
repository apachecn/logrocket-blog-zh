# 在 Node.js 中使用 GStreamer

> 原文：<https://blog.logrocket.com/using-gstreamer-node-js/>

## 介绍

GStreamer 是一个创建流媒体应用程序的框架。使用 GStreamer 框架，可以设计和构建能够处理任何类型的流数据流的低延迟应用程序，包括音频和视频功能。

GStreamer core 为插件、数据流和媒体类型处理提供了一个框架。它还提供了一个 API 来使用各种插件编写应用程序。

基于插件的框架提供了各种编解码器和其他功能，这些功能可以在管道中链接和安排，从而定义了数据流。但是，在撰写本文时，GStreamer 应用程序还没有正式的 Node.js 端口/绑定。

在本帖中，我们将通过以下几节讨论 GStreamer 的功能以及如何用 Node 设置它:

## 功能和使用案例

GStreamer 的一个主要用例是构建媒体播放器。作为开发人员，您可以使用大量强大的工具来创建媒体管道，而无需编写一行代码。

默认情况下，GStreamer 包含用于构建支持多种格式的媒体播放器的组件，包括 MP3、Ogg/Vorbis、MPEG-1/2、AVI、QuickTime、mod 等等。

然而，GStreamer 提供了比其他媒体播放器更多的功能。它的主要优点是可插入组件可以混合到任意管道中，因此可以编写视频或音频编辑应用程序。具体来说，GStreamer 提供了:

*   用于多媒体应用的 API
*   插件架构
*   流水线结构
*   用于媒体类型处理/协商的机制

## GStreamer 插件架构

通过插件，GStreamer 可以连接到其他多媒体框架，以重用现有的组件(如编解码器)和其他平台输入/输出机制。

GStreamer 的核心本质上是与媒体无关的。它只知道字节和块，只包含基本元素。所有的媒体处理功能都由内核外部的插件提供。这些告诉核心如何处理特定类型的媒体。

GStreamer 插件可以分为以下几组:

*   协议处理
*   来源:用于音频和视频(涉及协议插件)
*   格式:解析器、格式化器、复用器、解复用器、元数据、字幕
*   编解码器:编码器和解码器
*   滤镜:转换器、混音器、效果
*   接收器:用于音频和视频(涉及协议插件)

## GStreamer 安装

要安装 GStreamer，文档提供了各种方法，供您根据操作系统的选择进行选择。

对于 macOS X，我们需要安装 OS X 雪豹(10.6)或更高版本以及 XCode 3.2.6 或更高版本。不过推荐的系统版本是 macOS Sierra 带 XCode 8。我们可以从 GStreamer 下载页面安装运行时和开发安装程序，我们可以在这里找到。

对于其他操作系统/环境，包括 Windows、iOS、Android 和 Linux，我们可以查看文档中的[下载页面](https://gstreamer.freedesktop.org/documentation/installing/index.html?gi-language=javascript#)，其中包含所有受支持的生态系统和构建 GStreamer SDKs 的方式的列表。

我们可以导航到系统路径上的`/Library/Frameworks/GStreamer.framework/Commands`,看看可用的命令来使用 GStreamer。一些流行的命令包括`gst-launch-1.0`、`gst-inspect-1.0`、`gst-play-1.0`。

## 使用 Node.js 设置 GStreamer

安装完成后，我们可以继续使用 Node.js 运行时来使用 GStreamer 管道，并将结果输出到 web 浏览器。

让我们创建一个我们选择的文件夹，并使用 npm 或 Yarn 安装 Express.js，然后按照说明用一个`package.json`文件设置一个基本项目:

```
npm install express or yarn add express

```

然后继续创建一个`index.js`文件来保存我们使用 GStreamer 的流示例的 JavaScript 代码。参见下面的`index.js`文件:

```
const express = require('express')
const http = require('http')
const net = require('net');
const child = require('child_process');

const app = express();
app.use(express.static(__dirname + '/'));

const httpServer = http.createServer(app);
const port = 3000;  

//send the html page which holds the video tag
app.get('/', function (req, res) {
    res.send('index.html');
});

//stop the connection
app.post('/stop', function (req, res) {
    console.log('Connection closed using /stop endpoint.');
    if (gstMuxer != undefined) {
        gstMuxer.kill();    //kill the GStreamer Pipeline
    }
    gstMuxer = undefined;
    res.end();
});

//send the video stream
app.get('/stream', function (req, res) {
    res.writeHead(200, {
        'Content-Type': 'video/webm',
    });

    const tcpServer = net.createServer(function (socket) {
        socket.on('data', function (data) {
            res.write(data);
        });
        socket.on('close', function () {
            console.log('Socket closed.');
            res.end();
        });
    });

    tcpServer.maxConnections = 1;

    tcpServer.listen(function () {
        console.log("Connection started.");
        if (gstMuxer == undefined) {
            console.log("inside gstMuxer == undefined");
            const cmd = 'gst-launch-1.0';
            const args = getGstPipelineArguments(this);
            const gstMuxer = child.spawn(cmd, args);
            gstMuxer.stderr.on('data', onSpawnError);
            gstMuxer.on('exit', onSpawnExit);
        }
        else {
            console.log("New GST pipeline rejected because gstMuxer != undefined.");
        }
    });
});

httpServer.listen(port);
console.log(`Camera Streaming App listening at http://localhost:${port}`)

process.on('uncaughtException', function (err) {
    console.log(err);
});

//functions
function onSpawnError(data) {
    console.log(data.toString());
}

function onSpawnExit(code) {
    if (code != null) {
        console.log('GStreamer error, exit code ' + code);
    }
}

function getGstPipelineArguments(tcpServer) {
    const args =
        ['/Users/alexandernnakwue/Downloads/samplevideo.mp4', 'pattern=ball',
            '!', 'video/x-raw,width=320,height=240,framerate=100/1',
            '!', 'vpuenc_h264', 'bitrate=2000',
            '!', 'mp4mux', 'fragment-duration=10',
            '!', 'tcpclientsink', 'host=localhost',
            'port=' + tcpServer.address().port];
    return args;
}

```

正如我们在上面的文件中看到的，我们有三个端点:

1.  发送包含视频标签的 HTML 页面的端点
2.  发送视频流的端点
3.  结束连接的终结点

接下来，创建 HTML 页面(`index.html`)，它包含视频标签，如下所示。

```
<!DOCTYPE html>
<head>
    <title>GStreamer with NodeJS Demo</title>
    <meta name="viewport" content="width=device-width, initial-scale=0.9">
    <style>
        html,
        body {
            overflow: hidden;
        }
    </style> 
    <script>
        function buffer() {
            //Start playback as soon as possible to minimize latency at startup 
            const dStream = document.getElementById('vidStream');
            try {
                dStream.play();
            } catch (error) {
                console.log(error);
            }

        }
    </script>
</head>

<body onload="buffer();">
    <video id="vidStream" width="640" height="480" muted>
        <source src="/stream" type="video/mp4" />
        <source src="/stream" type="video/webm" />
        <source src="/stream" type="video/ogg" />
        <!-- fallback -->
        Your browser does not support the video element. </video> </body>
```

正如我在简介中提到的，Node.js 目前没有官方的端口或绑定，上面的代码改编自这篇[栈溢出帖子](https://stackoverflow.com/questions/63946535/how-to-use-gstreamers-webrtcbin-with-a-web-browser)。

我们可以继续使用`gst-launch-1.0`命令来启动流媒体应用程序和参数，包括流媒体的视频或音频源、TCP 端口和地址、设置等等。当页面加载时，我们用`play()`方法尽快播放视频流。

注意:这只适用于基于 Chromium 的浏览器。我将在下面详细解释

## Gstreamer 的一些限制

今天，GStreamer for Node.js 的当前实现是非标准化的，仍然缺乏。例如，当前的实现并不完全兼容浏览器，只能在基于 Chrome 的浏览器中工作，因为 Chrome 中加载资源所需的一些 HTTP 头不可用。此外，在某些系统架构上构建 GStreamer 仍然是一项困难的任务，因为它仍然包含许多 bug。

GStreamer 还不直接支持多种不同编程语言的端口。这意味着打算在 Node.js 应用程序中使用 GStreamer 的开发人员需要使用`node-addon-api`直接从 Node 调用`C`代码。然而，这种方法需要大量的工作，并且在使用 [node-gyp](https://github.com/nodejs/node-gyp) 构建时尤其容易出错。

## 结论

正如我们可能已经注意到的，目前对于 Node.js 的 GStreamer 绑定是有限的。

还有其他可用的绑定，如[node-gstreamer-surfacial](https://github.com/dturing/node-gstreamer-superficial)，但根据文档，它并不试图成为 GStreamer 的完整 JS 绑定，并且有望在某一天被`node-gir`所取代(或实现)。

其他可用的绑定或黑客只是不能像预期的那样工作，不是标准化的，就是容易出错。这确实是一个巨大的挑战，在不久的将来，需要构建 Node.js 绑定的标准化和行业范围的端口。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.