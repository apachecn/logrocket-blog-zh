# 使用 canvas-gif 编辑 Node.js 项目中的 gif

> 原文：<https://blog.logrocket.com/editing-gifs-node-js-project-canvas-gif/>

Canvas-gif 是在 Node.js 项目中访问和编辑 gif 框架的优秀工具，包括添加文本、进一步动画和编辑框架。Canvas-gif 利用了 Canvas 的所有超能力，允许你操作图像的每一部分，而不仅仅是给每个 gif 框架添加文本。

在本文中，我们将为您的下一个 Node.js 项目编写一个使用 canvas-gif 读取和编辑 GIF 的示例代码。在开始编辑 gif 之前，请确保您已经熟悉了使用 node-canvas 创建和保存图像的[。](https://blog.logrocket.com/creating-saving-images-node-canvas/)

*向前跳转:*

## 开始使用 canvas-gif

[canvas-gif](https://www.npmjs.com/package/canvas-gif) 库允许你将输入的 gif 作为[缓冲区](https://blog.logrocket.com/node-js-buffer-complete-guide/)来读取，并可以选择合并 GIF。然而，如果你打算合并 GIF，你需要安装 [ImageMagick](https://imagemagick.org/script/download.php) 。

首先，canvas-gif 将 gif 解码成一个`UInt8Array(s)`并为 GIF 返回的每一帧创建一个 canvas 实例。然后，它将`UInt8Array`转换成画布 [`ImageData`](https://developer.mozilla.org/en-US/docs/Web/API/ImageData) ，并将其放入 [`CanvasRenderingContext`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D) 。从那里，库将上下文和其他相关信息传递给函数来编辑框架。

然后，该库将处理上下文，并将新编辑的帧的上下文添加到编码器中。最后，canvas-gif 将返回编码器的数据作为缓冲区，我们将把它写入一个 gif 文件。

现在我们已经介绍了背景知识，让我们开始着手建立一个项目，探索一个实际的例子。

## 项目设置

你需要安装 [Node.js](https://nodejs.org/en/download/) 和 [npm](http://npmjs.com/package/npm) 来处理 canvas-gif。

为项目创建一个目录，初始化 Node.js 项目，并使用下面的命令安装`canvas-gif` npm 库:

```
mkdir edit-gif && cd edit-gif && npm init -y && npm install canvas-gif

```

下面是运行代码时会发生什么的示例:

![Code for Editing GIFs in Node.js Projects Using canvas-gif](img/be8c92369660939993fd363f1ab09aed.png)

设置和安装完成后，下载一个 GIF 文件并放在根目录下。对于本文，我们将使用这张 [GIF](https://c.tenor.com/1HnbW0C8O_0AAAAC/mr-rogers-nightmare.gif) :

![GIF to Start Editing Using Canvas-gif for a Node.js Project](img/4be995e6911c75bfce6ac5a58e283c78.png)

接下来，创建一个`app.js`文件，并向其中添加以下内容:

```
const canvasGif = require("canvas-gif");
const fs = require("fs");

canvasGif(path.join(__dirname, "mr-rogers-nightmare.gif"), callBack, options)
  .then((buffer) =>
    fs.writeFileSync(path.resolve(__dirname, "output1.gif"), buffer)
  )
  .catch((error) => {
    console.log(error);
  });

```

这里，`canvasGif`公开了`callBack`函数来接受 GIF 文件路径，并需要第三个可选的配置参数。不用担心；我们将很快研究如何处理这个问题。

## 使用 canvas-gif 的`callBack`函数

作为画布 API 的一部分，允许您修改框架的上下文。在这里，您可以渲染 2D 绘图，绘制形状，创建图像，文本，等等。我们将利用这个 API 来修改 GIF。

在下面输入函数定义:

```
const callBack = (context, width, height, totalFrames, currentFrame) => {
};

```

如您所见，`callBack`接受一个`context`作为第一个参数，后面是`width`、`height`、`totalFrames`和`currentFrame`，如上面的代码所示。

## 编辑和添加文本到我们的 Node.js GIF

为了给每个框架添加文本，我们将通过输入以下代码来修改上下文:

```
const callBack = (context, width, height, totalFrames, currentFrame) => {
// Edit the frame
  context.fillStyle = "#FFA500";
  context.font = '30px "Fira Code Retina"';
  context.fillText("Edited by Eze!", 40, 80);
}; 

```

当您将`callBack`传递给`canvasGif`时，它将操作 GIF 帧并添加`text`、`font`、`color`和字体系列。

现在，我们当前的代码将如下所示:

```
const canvasGif = require("canvas-gif");
const fs = require("fs");

const callBack = (context, width, height, totalFrames, currentFrame) => {
// Edit the frame
  context.fillStyle = "#FFA500";
  context.font = '30px "Fira Code Retina"';
  context.fillText("Edited by Eze!", 40, 80);
}; 
const options = {}

canvasGif(path.join(__dirname, "mr-rogers-nightmare.gif"), callBack, options)
  .then((buffer) =>
    fs.writeFileSync(path.resolve(__dirname, "output1.gif"), buffer)
  )
  .catch((error) => {
    console.log(error);
  });

```

然后，当我们运行它时，我们将得到这样的输出:

![Canvas-gif Edited GIF in the Node.js Project](img/4220c659ef234b525aa9b6e64ad77bc8.png)

### 使用 canvas-gif 中的配置

文字在 GIF 里，但是 GIF 渲染太快了吧？这是因为，默认情况下，canvas-gif 每秒渲染 60 帧(fps)。为了降低速度，让我们在选项参数中编辑 fps，并通过添加一个值为`20`的键`fps`将其降低到 20fps:

```
let options = {
  fps: 20
};

```

当我们重新运行脚本时，会得到以下结果:

![Rerun of the Edited GIF Using Canvas-gif in a Node.js Project](img/31f2a74af10a4c990ab2960ae84a0e3b.png)

这要慢得多，并且类似于原始的 GIF🙂。现在，您可以添加更多的配置，如循环 GIF 或设置停止点。

以下是配置模板示例:

```
let options = {
   coalesce: false,
   delay: 0, 
   repeat: 0, 
   algorithm: "neuquant",
   optimiser: true,
   fps: 20, 
   quality: 100, 
};

```

让我们来看看每种配置:

*   `delay`:以毫秒为单位设置每帧之间的延迟，默认为`0`
*   `repeat`:描述 GIF 应该重复多少次，默认为`0`，无限运行 GIF
*   `algorithm`:定义编码器应该使用哪种算法作为`neuquant`，但可以设置为`octree`。虽然`octree`可能会增加处理时间，但它可以减小文件大小
*   `optimiser`:告诉编码器是否使用内置优化器，默认为`false`
*   `fps`:描述每秒渲染的帧数，默认值为 60fps
*   `quality`:设置 GIF 的质量，从 1 到 100。数字越高，质量越好。默认情况下，它设置为`100`
*   `coalesce`:这允许你合并 GIF，默认为`false`

### 添加上下文绘图

到目前为止，我们已经编辑了我们的 GIF，并为每一帧添加了文本。但是，您可以做更多的事情，包括绘制形状、操作像素和合成。

例如，这是另一个呈现笑脸的上下文绘图:

```
const canvasGif = require("canvas-gif");
const fs = require("fs");

const callBack = (context, width, height, totalFrames, currentFrame) => {
// Edit the frame
  context.fillStyle = '#FFA500';
  context.strokeStyle = 'black';
  context.lineWidth = 5;
  context.beginPath();
  context.arc(80, 60, 50, 0, 2 * Math.PI);
  context.fill();
  context.stroke();
  context.closePath();
  //eyes
  context.fillStyle = 'white';
  context.beginPath();
  context.arc(67, 43.75, 7.5, 0, 2 * Math.PI);
  context.fill();
  context.stroke();
  context.closePath();
  context.beginPath();
  context.arc(92.5, 43.75, 7.5, 0, 2 * Math.PI);
  context.fill();
  context.stroke();
  context.closePath();
  //mouth
  context.strokeStyle = 'magenta';
  context.lineWidth = 5;
  context.beginPath();
  context.arc(80, 60, 37.5, 0, -1 * Math.PI);
  context.stroke();
  context.closePath();

}; 

let options = {
   coalesce: false,
   delay: 0, 
   repeat: 0, 
   algorithm: "neuquant",
   optimiser: true,
   fps: 20, 
   quality: 100, 
};

canvasGif(path.join(__dirname, "mr-rogers-nightmare.gif"), callBack, options)
  .then((buffer) =>
    fs.writeFileSync(path.resolve(__dirname, "output1.gif"), buffer)
  )
  .catch((error) => {
    console.log(error);
  });

```

这是最终产品:

![Example of Context Image Edited Canvas-gif in Node.js](img/0254741f06a2acd494eae234edbd2952.png)

## 画布的替代品-gif

虽然有 canvas-gif 的替代方案，比如 [jimp](https://www.npmjs.com/package/jimp) 、 [gifwrap](https://www.npmjs.com/package/gifwrap) 和 [text-on-gif](https://www.npmjs.com/package/text-on-gif) ，但是它们并没有直接解决 canvas-gif 的问题。

*   Jimp 是一个 JavaScript 库，允许您在代码中操作图像。使用 jimp，您可以调整大小、增加亮度、翻转、裁剪，甚至增加图像的不透明度
*   Gifwrap 是一个 Node.js jimp 可压缩库，用于处理 gif。使用 gifwrap，您可以创建 gif 并将其读入内部表示，以备操作
*   Text-on-gif 是另一个允许你编辑 gif 的库，但是它的重点是操纵 gif 来添加文本

### 使用 gifwrap 和跳转

为了实现类似于 canvas-gif 的功能，我们必须结合 jimp 和 gifwrap 的功能。下面是一个示例，说明如何使用这两个工具通过在 GIF 上放置文本来修改 GIF:

```
const { GifUtil, GifFrame, BitmapImage } = require("gifwrap");
const Jimp = require("jimp");
async function editGif() {
  var frames = [];
  let font = await Jimp.loadFont(Jimp.FONT_SANS_32_WHITE);
  let readGif = await GifUtil.read("mr-rogers-nightmare.gif");

  for (const frame of readGif.frames) {
    let jimpCopied = GifUtil.copyAsJimp(Jimp, frame);

    jimpCopied.print(font, 10, 0, "LogRocket");

    const modifiedFrame = new GifFrame(
      new BitmapImage(jimpCopied.bitmap, {
        disposalMethod: frame.disposalMethod,
        delayCentisecs: frame.delayCentisecs,
      })
    );

    frames.push(modifiedFrame);
  }

  GifUtil.quantizeDekker(frames);
  GifUtil.write("modified.gif", frames);
}
editGif()

```

在`let readGif = await GifUtil.read("mr-rogers-nightmare.gif");`中，我们用 gifwrap 将 GIF 读入内存，然后通过帧循环使用 jimp 来创建`jimpCopied.print(font, 10, 0, "LogRocket");`。在此基础上，我们根据特定框架中的文本和图像制作了一个新框架。

最后，我们将所有新的帧放入 frames 数组来创建一个新的编辑过的 GIF。

这是您运行代码时将得到的结果:

![Example of a GIF Created Using gifwrap and jimp](img/43da8f0364ae2e7c0f427a0d2aaa980b.png)

虽然这个过程是可行的，但是它有性能问题，而且速度太慢。Canvas-gif 修改一个 gif 大概需要三秒，但是用 gifwrap 和 jimp 操作同一个 GIF 需要 21 秒。

这种区别是因为 gifwrap 是基于现有的 GIF 生成一个新的 GIF，而 canvas-gif 是直接修改框架。正因为如此，`Canvas-gif`实现比`Gifwrap`和`Jimp`实现的执行效率更高。

### Node.js 中使用 text-on-gif

尽管 text-on-gif 很简单，但它主要是处理 gif，只添加文本。与利用 canvas 特性的 canvas-gif 不同，text-on-gif 不能处理图像的每一部分，只能向每个 gif 框架添加文本。

这里有一个来自[文档](https://github.com/sanidhya711/text-on-gif)的例子:

```
const TextOnGif = require("text-on-gif");
const generate = async ()=>{

  var gif = new TextOnGif({
    file_path: "mr-rogers-nightmare.gif"
  });

  gif.font_color = "orange";
  gif.font_size = "30px";

  var buffer = await gif.textOnGif({
    text: "Made with Love ",
    write_path: "mrroger.gif"
  });
}
generate()

```

上面的代码将生成这个 GIF:

![GIF Edited Using Text-on-gif](img/d2b12ccf72297bda69e6155d308060ab.png)

## 结论

在本文中，我们学习了如何使用 canvas-gif 编辑 gif 动画并添加形状、文本或图像。因为你可以用 canvas-gif 做的大部分事情取决于 canvas API，我鼓励你在 [MN](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API) 和 [Node-canvas](https://www.npmjs.com/package/canvas) 文档中学习更多关于 Canvas API 的知识。

黑客快乐！

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.