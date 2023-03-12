# 使用 Node.js - LogRocket 博客处理和调度事件

> 原文：<https://blog.logrocket.com/handling-and-dispatching-events-with-node-js/>

***编者按:**本帖于 2021 年 4 月更新相关信息。*

## Node.js 是什么？

在其核心， [Node.js](https://blog.logrocket.com/web-analytics-with-node-js/) 是一个开源的运行时环境，为在服务器端运行 JavaScript 应用程序而构建。它提供了一个事件驱动、非阻塞(异步)I/O 和跨平台运行时环境，用于使用 JavaScript 构建高度可伸缩的服务器端应用程序。

这不会是 Node.js 的入门指南；要了解更多，你可以查看 [YouTube](https://www.youtube.com/results?search_query=nodejs) 上的[官方文档](https://nodejs.org/en/docs/)或视频教程。相反，我们将讨论如何在 Node.js 中处理和调度事件。

## Node.js 中的模块

Node.js 附带了几个模块——或者你也可以称之为库——它们可以包含在你的应用程序中，并被重用来帮助执行特定的任务，比如`event`、`os`和`path`模块等等。

Node.js 中的一些核心模块包括:

| **模块** | **描述** |
| --- | --- |
| `http` | 允许您创建 http 服务器并通过超文本传输协议(http)处理数据传输 |
| `url` | 解析和解析 URL 字符串 |
| `querystring` | 处理 URL 查询字符串 |
| `path` | 处理文件路径 |
| `fs` | 处理文件系统 |
| `os` | 提供有关操作系统的信息 |

## 基本服务器设置

### 要求:

*   [节点](https://nodejs.org/en/download/)(最新稳定版本)
*   JavaScript 和 Node.js 的基础知识

让我们用最少的配置设置我们的节点服务器，如下所示，并将文件保存为`index.js`。

```
// index.js
const http = require('http');

// declare server variables
const hostname = '127.0.0.1';
const port = 8080;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log(`Server is running at http://${hostname}:${port}/`);
});

```

确保保存文件并运行`node index.js`。输出应该是:

```
Server is running at http://127.0.0.1:8080
```

对我们服务器的每个请求都应该给出`Hello World`作为响应。

## `events`模块

`events`模块允许我们在 Node.js 中轻松创建和处理自定义事件。该模块包括`EventEmitter`类，用于引发和处理事件。

几乎整个 Node.js 核心 API 都是围绕这个模块构建的，它发出命名事件，这些事件导致函数对象(也称为侦听器)被调用。在文章的最后，我们应该已经构建了一个非常简单的模块，它实现了事件模块。

### `events`模块的一些常见属性和方法

| **事件发射器方法** | **描述** |
| --- | --- |
| `addListener(event, listener)` | 将侦听器添加到指定事件的侦听器数组的末尾。不检查是否已经添加了侦听器。 |
| `on(event, listener)` | 它也可以作为 emitter.addListener()的别名来调用 |
| `once(event, listener)` | 为事件添加一次性侦听器。该侦听器仅在下一次触发事件时被调用，之后将被移除。 |
| `emit(event, [arg1], [arg2], [...])` | 用提供的参数引发指定的事件。 |
| `removeListener(event, listener)` | 从指定事件的侦听器数组中移除一个侦听器。注意:更改侦听器后面的侦听器数组中的数组索引。 |
| `removeAllListeners([event])` | 移除所有侦听器，或指定事件的侦听器。 |

将事件添加到 Node.js 应用程序很容易——您只需要使用 [`require`模块](https://nodejs.org/api/events.html) 或 [`import`语句](https://nodejs.org/api/esm.html)来添加事件，这取决于您的设置。在我们的例子中，我们使用如下所示的 require 语句:

```
// index.js

const http = require('http');
const events = require('events'); //— here

// declare server variables
const hostname = '127.0.0.1';
const port = 8080;

//create an object of EventEmitter class from events module
const myEmitter = new events.EventEmitter();

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

让我们通过监听一个自定义事件并同时调度该事件来玩一玩:

```
//index.js

const http = require('http');
const events = require('events');

// declare server variables
const hostname = '127.0.0.1';
const port = 8080;

//create an object of EventEmitter class from events module
const myEmitter = new events.EventEmitter();

 //Subscribe for ping event
 myEmitter.on('ping', function (data) {
    console.log('First event: ' + data);
 });

 // Raising ping event
 myEmitter.emit('ping', 'My first Node.js event has been triggered.');

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

要使更改生效，必须重新启动服务器。完成后，在浏览器中刷新页面，您应该会看到控制台中记录了一条消息:

```
First event: My first Node.js event has been triggered.
```

如上例所示，除了用`.emit`函数触发事件之外，我们还可以传入其他对事件处理程序有用的参数，让 订阅 事件。

在我们的例子中，我们传递了一个字符串，“ 我的第一个 Node.js 事件已经被触发。 “这可能是一些我们想要操作的数据，或者任何对事件处理程序有用的东西。

## 在 Node.js 中只处理一次事件

正如我们上面所做的，当使用`emitter.on()`方法注册一个侦听器时，每次发出指定的事件时，都会调用该侦听器。根据应用程序的需求，有些事件在整个应用程序生命周期中应该只处理一次，可以用`once()`方法来实现。

让我们将它添加到代码中:

```
//index.js

const http = require('http');
const events = require('events');

// declare server variables
const hostname = '127.0.0.1';
const port = 8080;

//create an object of EventEmitter class from events module
const myEmitter = new events.EventEmitter();

//Subscribe for ping event
 myEmitter.on('ping', function (data) {
    console.log('First event: ' + data);
 });

 // Raising ping event
 myEmitter.emit('ping', 'My first Node.js event has been triggered.');

let triggered = 0;
myEmitter.once('event', () => {
  console.log(++triggered);
});
myEmitter.emit('event');
// Prints: 1
myEmitter.emit('event');
// Ignored

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

通过使用`once()`方法而不是`on()`，事件不会发生多次，只会在第一次发生时触发。如果它在程序的生命周期中被第二次触发，它将被忽略。

## Node.js 中的错误事件

开发过程中的错误是不可避免的，但可以在 Node.js 中正确处理。除了 try-catch 块，Node 还可以侦听错误事件，并在错误发生时执行若干操作。如果一个`EventEmitter`没有至少一个为`error`事件注册的侦听器，并且发出了一个`error`事件，那么就会抛出错误，打印一个堆栈跟踪，并且 Node.js 进程退出。

```
//index.js

const http = require('http');
const events = require('events');

// declare server variables
const hostname = '127.0.0.1';
const port = 8080;

//create an object of EventEmitter class from events module
const myEmitter = new events.EventEmitter();

//Subscribe for ping event
 myEmitter.on('ping', function (data) {   
 console.log('First subscriber: ' + data);
 });

 // Raising ping event
myEmitter.emit('ping', 'This is my first Node.js event emitter example.');

let triggered = 0;
myEmitter.once('event', () => {
  console.log(++triggered);
});
myEmitter.emit('event');
// Prints: 1
myEmitter.emit('event');
// Ignored

myEmitter.on('error', (err) => {
  console.error('whoops! there was an error bro!' + err);
 });
myEmitter.emit('error', new Error('whoops!'));
 // Prints: whoops! there was an error to the console

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

为了防止 Node.js 进程崩溃，建议总是向节点应用程序添加`error`事件监听器

## 结论

我们已经学习了如何在 Node.js 中创建、调度和管理事件，但是我们只是触及了表面。关于在 Node.js 中处理事件，你还可以学到很多东西。每当你需要在 Node.js 中复习和处理复杂的事件驱动设计时，你都可以使用官方文档作为参考。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.