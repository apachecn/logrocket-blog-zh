# 使用 web workers 实现安全、并发的 JavaScript 

> 原文：<https://blog.logrocket.com/using-webworkers-for-safe-concurrent-javascript-3f33da4eb0b2/>

Web workers 提供了一种在浏览器的单线程执行之外运行 JavaScript 代码的方法。单线程处理显示内容的请求以及通过键盘、鼠标点击和其他设备的用户交互，还处理对 AJAX 请求的响应。

事件处理和 AJAX 请求是异步的，可以被认为是在通用浏览器显示的代码路径之外运行一些代码的一种方式，但它们仍然在这个单线程中运行，并且确实必须相当快地完成。

否则，浏览器中的交互性会停止。

Web workers 允许 JavaScript 代码在一个单独的线程中运行，完全独立于浏览器线程及其通常的活动。

近年来，关于网络工作者到底有什么用处，有很多争论。如今，CPU 的速度非常快，几乎每个人的个人电脑都有几千兆字节的内存。类似地，移动设备的处理器速度和内存大小已经接近台式机。

曾经被认为是“计算密集型”的应用程序现在被认为没有那么糟糕。

## 你说这不是真空是什么意思？

但是很多时候，在决定如何高效地执行代码时，我们只考虑在开发环境中测试的一个应用程序的执行。在用户手中的现实生活系统中，许多事情可能同时执行。

因此，在隔离状态下运行的应用程序可能不需要使用工作线程，但却有必要使用它们来为广大用户提供最佳体验。

启动一个新的 worker 就像指定一个包含 JavaScript 代码的文件一样简单:

```
new Worker(‘worker-script.js’)
```

一旦创建了工作线程，它就在独立于主浏览器线程的单独线程中运行，执行脚本中提供给它的任何代码。浏览器相对于指定 JavaScript 文件的当前 HTML 页面的位置进行查找。

使用 JavaScript 代码中的两个互补特性在工作线程和主 JavaScript 线程之间传递数据:

*   发送端的`postMessage()`功能
*   接收端的消息事件处理程序

与其他事件处理程序一样，消息事件处理程序接收一个事件参数；这个事件有一个“数据”属性，它包含从另一端传来的任何数据。

这可以是双向通信:主线程中的代码可以调用`postMessage()`向工作线程发送消息，工作线程可以使用在工作线程环境中全局可用的`postMessage()`函数的实现将消息发送回主线程。

web worker 中的一个非常简单的流程如下所示:在页面的 HTML 中，向 worker 发送一条消息，页面等待响应:

```
var worker = new Worker("demo1-hello-world.js");

// Receive messages from postMessage() calls in the Worker
worker.onmessage = (evt) => {
    console.log("Message posted from webworker: " + evt.data);
}

// Pass data to the WebWorker
worker.postMessage({data: "123456789"});
```

工作代码等待一条消息:

```
// demo1-hello-world.js
postMessage('Worker running');
onmessage = (evt) => {
    postMessage("Worker received data: " + JSON.stringify(evt.data));
};
```

上面的代码将把它打印到控制台:

```
Message posted from webworker: Worker running
Message posted from webworker: Worker received data: {“data”:”123456789"}
```

## 工人被期望长寿，而不是停止和开始

在工作者的生命周期中，浏览器和工作者之间可以发送和接收多条消息。

web workers 的实现以两种方式确保了安全、无冲突的执行:

*   工作线程的独特、隔离的全局环境，独立于浏览器环境
*   在`postMessage()`调用中主线程和工作线程之间的数据传递交换

每个工作线程都有一个独立的全局环境，不同于浏览器页面的 JavaScript 环境。工作人员根本无权访问页面的 JavaScript 环境中的任何内容——无论是 DOM，还是`window`或`document`对象。

Workers 对某些东西有自己的版本，比如用于将消息记录到开发人员控制台的`console`对象，以及用于发出 AJAX 请求的`XMLHttpRequest`对象。但是除此之外，在 worker 中运行的 JavaScript 代码应该是自包含的；主窗口想要使用的来自工作线程的任何输出都必须通过`postMessage()`函数作为数据传回。

此外，任何通过`postMessage()`传递的数据在被传递之前都会被*复制*，所以改变主窗口线程中的数据不会导致工作线程中的数据改变。这为在主线程和工作线程之间传递的数据的冲突并发更改提供了内在的保护。

## 网络工作者的使用案例

web worker 的典型用例是任何在其执行过程中可能变得计算昂贵的任务，要么消耗大量 CPU 时间，要么花费不可预测的大量时钟时间来访问数据。

网络工作者的一些可能的使用案例:

*   预取和/或缓存数据以备后用
*   轮询和处理来自 web 服务的数据
*   大型数据集的处理和显示(想想基因组学)
*   游戏中与移动相关的计算
*   图像处理和过滤
*   处理文本数据(代码语法、拼写检查、字数统计)

CPU 时间是简单的用例，但是对资源的网络访问也非常重要。很多时候，互联网上的网络通信可以在几毫秒内执行，但有时网络资源变得不可用，直到网络恢复或请求超时(可能需要 1-2 分钟才能清除)才停止。

即使在开发环境中单独测试时，一些代码可能不需要很长时间就能运行，但当多个项目同时运行时，在用户环境中运行可能会成为一个问题。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

下面的例子演示了使用 web workers 的几种方法。

## 演示:游戏碰撞检测

(系好安全带。这是个很长的例子。)

现在，在网络浏览器中运行的基于 HTML5 的游戏随处可见。游戏的一个核心方面是计算游戏环境各部分之间的运动和交互。一些游戏有相对较少的活动部分，并且相当容易制作动画([超级马里奥模拟器](http://supermarioemulator.com/mario.php)克隆，有人吗？).但是让我们考虑一个计算量更大的情况。

这个例子包括大量的彩色球在一个矩形的边界上弹跳。目标是将球保持在游戏的边界内，并且检测球之间的碰撞并使它们相互弹开。

边界检测相对简单且执行速度快，但碰撞检测的计算要求更高，因为它大致随球数的平方增长——对于“n”个球，每个球必须与其他球进行比较，以查看它们的路径是否相交并需要被反弹(导致 n 乘以 n，或 n 的平方比较)。

因此，对于 50 个球，大约需要进行 2500 次检查；对于 100 个球，需要进行 10，000 次检查(实际上略少于这个数量的一半:如果你检查球 n 和球 m，你不需要稍后检查球 m 和球 n，但是仍然会涉及大量的计算)。

对于这个例子，边界和碰撞检测是在一个单独的工作线程中完成的，这个线程以浏览器动画的速度执行，每秒 60 次(每次调用`requestAnimationFrame()`)。定义了一个`World`对象，它保存了一个`Ball`对象的列表；每个`Ball`对象都知道它当前的位置和速度(以及半径和颜色，以允许它被绘制)。

在当前位置绘制球发生在主浏览器线程中(它可以访问画布及其绘制上下文)；更新球的位置发生在工作线程中。如果球碰到游戏边界或与另一个球碰撞，速度(特别是球的运动方向)会更新。

`World`对象在浏览器中的客户端代码和工作线程之间传递。即使对于几百个球来说，这也是一个相对较小的对象(100 乘以每个球大约 64 字节的数据= 6400 字节的数据)。所以这里的问题是计算量。

这个例子的完整代码可以在代码栏[这里](https://codepen.io/bwilln/pen/VXYRay)找到。有一个`Ball`类来表示被动画化的对象，还有一个`World`类实现制作动画的`move()`和`draw()`方法。

如果我们在不使用工人的情况下制作纯动画，主要代码将如下所示:

```
const canvas = $('#democanvas').get(0),
    canvasBounds = {'left': 0, 'right': canvas.width,
        'top': 0, 'bottom': canvas.height},
    ctx = canvas.getContext('2d');

const numberOfBalls = 150,
    ballRadius = 15,
    maxVelocity = 10;

// Create the World
const world = new World(canvasBounds), '#FFFF00', '#FF00FF', '#00FFFF'];

// Add Ball objects to the World
for(let i=0; i < numberOfBalls; i++) {
    world.addObject(new Ball(ballRadius, colors[i % colors.length])
            .setRandomLocation(canvasBounds)
            .setRandomVelocity(maxVelocity));
}
...
// The animation loop
function animationStep() {
    world.move();
    world.draw(ctx);
    requestAnimationFrame(animationStep);
}
animationStep();
```

代码使用`requestAnimationFrame()`在显示器的刷新周期内每秒运行`animationStep()`函数 60 次。动画步骤包括`move`，更新每个球的位置(可能还有方向)，然后`draw`，用新位置的球重新绘制画布。

为了在这个应用程序中使用一个工作线程，游戏动画循环的`move`部分(`World.move()`中的代码)将被移动到工作线程中。通过`postMessage()`调用，`World`对象将作为数据传递给工作线程，这样就可以在那里进行`move()`调用。`World`对象显然是要被传递的东西，因为它有`Ball`的显示列表和它们应该待在其中的矩形边界，并且每个球保留关于其位置和速度的所有信息。

由于使用了 worker，修改后的动画循环如下所示:

```
let worker = new Worker('collider-worker.js');

// Watch for the draw event
worker.addEventListener("message", (evt) => {
    if ( evt.data.message === "draw") {
        world = evt.data.world;
        world.draw(ctx);
        requestAnimationFrame(animationStep);
    }
});

// The animation loop
function animationStep() {
    worker.postMessage(world);  // world.move() in worker
}
animationStep();
```

工作线程本身看起来就像这样:

```
// collider-worker.js
importScripts("collider.js");

this.addEventListener("message", function(evt) {
    var world = evt.data;
    world.move();
    // Tell the main thread to update display
    this.postMessage({message: "draw", world: world});
});
```

这里的代码依赖工作线程从主代码接受`postMessage()`中的 W `orld`对象，然后将`world`传递回主代码，并更新位置和速度。

请记住，当`World`对象传入传出工作线程时，浏览器会制作一份副本——这里的假设是，制作`World`对象副本的时间明显少于`O(n**2)`碰撞计算的时间(这实际上是保存在`World`中的数据的相对较小的一部分)。

但是，运行基于新工作线程的代码会导致意外错误:

```
Uncaught TypeError: world.move is not a function
at collider-worker.js:10
```

原来在`postMessage()`调用中复制一个对象的过程将复制对象*上的数据属性，而不是对象*的原型。当复制并传递给 worker 时，`World`对象的方法被从原型中剥离。这是[“结构化克隆算法”](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm)的一部分，这是在主线程和 web worker 之间复制对象的标准方式，也称为[序列化](https://www.w3.org/TR/html5/infrastructure.html#safe-passing-of-structured-data)。

为了解决这个问题，我将向`World`类添加一个方法来创建它自己的新实例(它将具有包含这些方法的原型),并从消息中传递的数据中重新分配数据属性:

```
static restoreFromData(data) {
    // Restore from data that's been serialized to a worker thread
    let world = new World(data.bounds);
    world.displayList = data.displayList;
    return world;
}
```

尝试使用此修复运行动画会导致另一个类似的错误…在`World`的显示列表中的底层`Ball`对象也必须被恢复:

```
Uncaught TypeError: obj1.getRadius is not a function
at World.checkForCollisions (collider.js:60)
at World.move (collider.js:36)
```

必须增强`World`类的实现，以便从数据中恢复显示列表中的每个`Ball`，以及`World`类本身。

现在，在`World`类中:

```
static restoreFromData(data) {
    // Restore from data that's been serialized to a worker thread
    let world = new World(data.bounds);
    world.animationStep = data.animationStep;
    world.displayList = [];
    data.displayList.forEach((obj) => {
        // Restore each Ball object as well
        let ball = Ball.restoreFromData(obj);
        world.displayList.push(ball);
    });
    return world;
}
```

以及在`Ball`类中实现的类似的`restoreFromData()`方法:

```
static restoreFromData(data) {
    // Restore from data that's been serialized to a worker thread
    const ball = new Ball(data.radius, data.color);
    ball.position = data.position;
    ball.velocity = data.velocity;
    return ball;
}
```

这样，动画就能正确运行，计算工作线程中数百个球中每个球的`move`,并在浏览器中以每秒 60 次的速度显示它们的更新位置。

这个工作线程的例子是计算受限的，而不是内存受限的。如果记忆也是一个问题呢？

## 演示:阈值图像

最后一个例子，让我们看一个 CPU 和内存都很密集的应用程序:获取 HTML5 画布图像中的像素并转换它们，生成并显示另一个图像。

这个演示将使用 Ilmari Heikkinen 在 2012 年编写的[图像处理库](https://www.html5rocks.com/en/tutorials/canvas/imagefilters/)。它将获取一幅彩色图像，并将其转换为二进制黑白图像，以中间灰度值为阈值:灰度值小于该值的像素显示为黑色；大于该值显示为白色。

阈值代码遍历每个(rgb)值，使用一个公式将其转换为灰度值:

```
Filters.threshold = function(pixels, threshold) {
    var d = pixels.data;
    for (var i=0; i < d.length; i+=4) {
        var r = d[i];
        var g = d[i+1];
        var b = d[i+2];
        var v = (0.2126*r + 0.7152*g + 0.0722*b >= threshold) ? 255 : 0;
        d[i] = d[i+1] = d[i+2] = v
    }
    return pixels;
};
```

对于最初看起来像这样的图像:

![](img/23f46354a77ba1eea67913bb4061d7bf.png)

阈值算法会产生一个双色调的黑白图像，如下所示:

![](img/727e5ba68d1fd861927799fd1197bacf.png)

这个演示的代码笔可以在[这里](https://codepen.io/bwilln/pen/RMKwMX)找到。

即使对于小图像，数据以及所涉及的计算也可能很大。一个 640×480 的图像有 307，200 个像素，每个像素有四个字节的 RGBA 数据(“A”代表 alpha，或透明数据)，使图像数据的大小达到 1.2MB。计划是使用一个 web worker 迭代每个像素，并将它们转换为新的 RGB 值。图像的像素数据将从浏览器传递到工作线程，修改后的图像将被返回。每次在客户端和工作线程之间来回传递数据时，最好不要复制这些数据。

对`postMessage()`调用的扩展提供了一种方式来指定与消息一起传递的数据的一个或多个属性，该消息应该通过引用传递而不是复制。看起来是这样的:

```
<div style="margin: 50px 100px">
    <img id="original" src="images/flmansion.jpg" width="500" height="375">
    <canvas id="output" width="500" height="375" style="border: 1px solid;"></canvas>
</div>
...
<script type="text/javascript">
const image = document.getElementById('original');
...
// Use a temporary HTML5 canvas object to extract the image data
const tempCanvas = document.createElement('canvas'),
    tempCtx = tempCanvas.getContext('2d');
tempCanvas.width = image.width;
tempCanvas.height = image.height;
tempCtx.drawImage(image, 0, 0, image.width, image.height);
const imageDataObj = tempCtx.getImageData(0, 0, image.width, image.height);
...
worker.addEventListener('message', (evt) => {
    console.log("Received data back from worker");
    const results = evt.data;
    ctx.putImageData(results.newImageObj, 0, 0);
});
worker.postMessage(imageDataObj, [imageDataObj.data.buffer]);
</script>
```

任何实现可转移接口的对象都可以在这里指定。一个`ImageData`对象的`data.buffer`符合这个要求——它的类型是`Uint8ClampedArray`(一种用于存储 8 位图像数据的数组类型)。`ImageData`是 HTML5 canvas context 对象的`getImageData()`方法返回的内容。

一般来说，几种标准数据类型实现了可转移接口:`ArrayBuffer`、`MessagePort`和`ImageBitmap`。`ArrayBuffer`依次由多个特定的数组类型实现:`Int8Array`、`Uint8Array`、`Uint8ClampedArray`、`Int16Array`、`Uint16Array`、`Int32Array`、`Uint32Array`、`Float32Array`、`Float64Array`。

因此，如果现在数据是通过引用而不是通过值在线程之间传递的，那么数据可以在两个线程中同时被修改吗？这些标准防止了这一点:当数据被`postMessage()`传递时，发送端对数据的访问被禁用(规范中实际上使用了“阉割”一词)，使其不可用。通过`postMessage()`将数据再次传递回来，在工作线程端“中和”它，但在浏览器中可以访问它。这个“阉割”特性是在 JavaScript 引擎中实现的。

## 总结和最终想法

HTML5 web workers 提供了一种方法，可以将繁重的计算卸载到一个单独的执行线程中，而不会停止浏览器的主事件线程。

两个例子展示了网络工作者的一些特征:

*   通过`postMessage()`调用和消息事件监听器的基本双向消息传递
*   计算密集型示例:HTML5 动画中的边界和碰撞检测
*   最后一个例子:图像阈值处理(计算和数据密集型)，演示在`postMessage()`函数中通过引用传递大量数据

在此过程中，所演示的示例探讨了 web workers 的几个问题和实现细节:

*   在`postMessage()`中传递 JavaScript 对象时应用的序列化过程不会复制对象原型中的方法——必须设计一些代码来恢复这些方法
*   当从`getImageData()`方法传递像素数据数组时，像素数据对象的 buffer 属性必须传递给`postMessage()`调用(像`imageData.data.buffer`，而不是`imageData.data`)。实现可转移的是缓冲区

目前大多数主流浏览器都支持 Web workers。Chrome、Safari 和 Firefox 大约从 2009 年开始支持它们；MSEdge 支持它们，从 IE10 开始，Internet Explorer 也支持它们。

为了与浏览器兼容，对`if (typeof Worker !== "undefined")`的简单检查可以保护创建和使用 worker 的代码，同时在 worker 之外执行相同的代码(在超时或动画帧中)。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.