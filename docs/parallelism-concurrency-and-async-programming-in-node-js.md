# Node.js 中的并行性、并发性和异步编程

> 原文：<https://blog.logrocket.com/parallelism-concurrency-and-async-programming-in-node-js/>

## 介绍

Node.js 是一个基于 Chrome V8 引擎的开源跨平台 JavaScript 运行时。运行时是程序生命周期的最后阶段，在此期间，当机器执行程序的代码时，程序正在运行。

为了使 Node 在运行时有效，它使用了事件驱动的非阻塞 I/O 模型，这使它变得轻量级和高效，非常适合跨分布式设备运行的数据密集型实时应用程序。在本文中，我们来看看机器执行代码的不同方式。

## 异步、并发和并行解释

Node.js 使用异步事件驱动的设计模式，这意味着在执行一个程序的同时采取多个动作。例如，如果程序中有两个相互跟随的端点，服务器将移动一个端点向第二个端点发出请求，而不等待第一个端点返回数据。

所有这一切都要感谢 Node 的 events 机制，它帮助服务器从先前的 API 调用中获得响应；简而言之，这就是异步编程模型。

另一个极端是同步编程，一次执行一个动作。这意味着当一个任务在等待响应时，你不能调用另一个任务，因为程序的所有其他部分都被阻止执行。

并发性意味着一个程序能够同时运行多个任务——这不要与并行性相混淆。在并发性中，可以同时执行具有不同程序目标的不同任务，而在并行性中，程序的不同部分执行一个任务。

并发性要求正确分配资源以高效工作，而并行性则要求程序的各个部分独立工作以完成相同的任务。并发性的一个例子是，您可以下载一个图像，同时能够在同一个图像上发布回复。

另一方面，并行性是通过使用[Web Workers API](https://blog.logrocket.com/optimized-media-loading-web-workers/)实现的。Web Workers 可能是 JavaScript 中实现“真正的”多处理的唯一方法。我们在这里说“真”是因为通过`setInterval()`、`setTimeOut()`、`XMLHttpRequest`、`async/await`和事件处理程序，我们可以模拟并行性。

## 回调地狱、承诺和处理 Node.js 中的异步执行

在同步 JavaScript 中，程序一次执行一个任务。这意味着所有其他流程都被搁置，导致流程之间耗费大量时间。在异步 JavaScript 中，一次会执行多个任务，这就产生了异步回调。

### 回调解释

异步回调是在调用函数时被指定为参数的函数，它将在后台开始执行代码。当后台代码完成运行时，它会调用回调函数来通知您任务已经完成。一个例子是 [`addEventListener()`](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener) :

```
btn.addEventListener('click', () => {
  alert('Button clicked!');

  let pElement = document.createElement('p');
  pElement.textContent = 'Hello dear Friend';
  document.body.appendChild(pElem);
});

```

第一个参数是要监听的事件的类型，第二个参数是在事件触发时调用的回调函数，即回调函数不会立即执行。稍后，它会被异步“回调”到包含它的函数体内的某个地方。

包含函数负责在时机到来时执行回调函数。像大多数函数一样，使用回调的函数需要一些时间来产生结果，而不是立即返回一些结果。

使用回调的异步 JavaScript 不是很直观，而且很难做好。这里有一个所谓回调地狱的经典例子:

```
fs.readdir(source, function (err, files) {
  if (err) {
    console.log('Error finding files: ' + err)
  } else {
    files.forEach(function (filename, fileIndex) {
      console.log(filename)
      gm(source + filename).size(function (err, values) {
        if (err) {
          console.log('Error identifying file size: ' + err)
        } else {
          console.log(filename + ' : ' + values)
          aspect = (values.width / values.height)
          widths.forEach(function (width, widthIndex) {
            height = Math.round(width / aspect)
            console.log('resizing ' + filename + 'to ' + height + 'x' + height)
            this.resize(width, height).write(dest + 'w' + width + '_' + filename, function(err) {
              if (err) console.log('Error writing file: ' + err)
            })
          }.bind(this))
        }
      })
    })
  }
})

```

发生这种情况的一个原因是，程序员可能倾向于以这样一种方式编写代码，即从上到下可视化地执行。

### 对救援的承诺

承诺有助于使类似上述混乱的情况更容易编写、解析和运行。承诺将在程序未来的某个时刻返回一个值。例如，出于安全和隐私方面的原因，每次您进行视频通话时，接收端的人员都需要授予视频访问权限，以便通话通过:

```
function phoneCallButton(evt) {
  setStatusMessage("Ringing...");
  navigator.mediaDevices.getUserMedia({video: true, audio: true})
    .then(chatStream => {
      selfViewElem.srcObject = chatStream;
      callStream.getTracks().forEach(track => myPeerConnection.addTrack(track, callStream));
      setStatusMessage("Connected to user");
    }).catch(err => {
      setStatusMessage("Failed to connect");
    });
}

```

当调用该函数时，将发送状态消息`"Ringing…"`。此后，`getUserMedia`将被调用，而不是等待用户，启用所选设备，并直接返回从所选源创建的流的 [`MediaStream`](https://developer.mozilla.org/en-US/docs/Web/API/MediaStream) 。`getUserMedia()`返回一个承诺，一旦承诺可用，就用`MediaStream`解决。

基本上只要 app 不知道已经连接上了，手机就会一直响。只有当它知道电话是否已经接通或者`catch`块是否已经被调用时，它才会停止。

接收一个带参数的函数，这是我们承诺的解析值。`.catch`返回我们承诺的拒绝值。`.then()`还提供了一种事件排队，确保没有回调地狱。

说到承诺，查看异步/等待函数也很重要。异步函数是用关键字`async`声明的。`async`和`await`关键字使得异步的、基于承诺的行为能够以更简洁的方式编写，避免了显式配置承诺链的需要。

```
app.post('/user/signin', async (request, reply) => {
        const data = request.body;
        const result = await signinUser(data);
        reply.status(200).send({
            status: 200,
            data: result,
        });
    });

```

在上面的登录端点中，为了得到结果，函数等待数据。异步函数可以包含零个或多个`await`表达式。Await 表达式通过一个异步函数暂停进程，然后只有当一个等待的基于承诺的异步操作完成或被拒绝时才恢复进程。

## 结论

在 Node.js 中，有多种方法可以并行处理异步操作。首先，也是最重要的，您可以使用 promises，如上所述。

你也可以利用第三方库，比如 [async](https://www.npmjs.com/package/async) [npm 包](https://www.npmjs.com/package/async)，这将确保你不会导致回调地狱。您可以使用以下命令安装它:

```
npm install async --save

```

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.