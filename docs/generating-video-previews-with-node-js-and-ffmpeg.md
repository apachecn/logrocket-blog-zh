# 使用 Node.js 和 FFmpeg - LogRocket Blog 生成视频预览

> 原文：<https://blog.logrocket.com/generating-video-previews-with-node-js-and-ffmpeg/>

每一个以任何方式处理视频流的网站都有一种方法来显示视频的简短预览，而不是实际播放。例如，每当用户将鼠标悬停在视频缩略图上时，YouTube 就会播放 3 到 4 秒的视频摘录。创建预览的另一种流行方式是从视频中截取几帧，制作幻灯片。

我们将进一步研究如何实现这两种方法。

## 如何用 Node.js 操作视频

用 Node.js 操作视频本身会非常困难，所以我们将使用最流行的视频操作工具: [FFmpeg](https://ffmpeg.org/) 。在文档中，我们读到:

> FFmpeg 是领先的多媒体框架，能够解码、编码、转码、复用、解复用、流式传输、过滤和播放人类和机器创造的几乎任何东西。它支持最前沿的最晦涩的古老格式。不管它们是由某个标准委员会、社区还是公司设计的。它还具有高度的可移植性:FFmpeg 编译、运行并通过了我们在 Linux、Mac OS X、Microsoft Windows、BSDs、Solaris 等平台上的测试基础设施测试。在各种各样的构建环境、机器架构和配置下。

拥有如此令人印象深刻的简历，FFmpeg 是从程序内部进行视频操作的完美选择，能够在许多不同的环境中运行。

FFmpeg 可以通过 CLI 访问，但是框架可以通过 [node-fluent-ffmpeg](https://github.com/fluent-ffmpeg/node-fluent-ffmpeg) 库轻松控制。npm 上提供的库为我们生成 FFmpeg 命令并执行它们。它还实现了许多有用的特性，比如跟踪命令的进度和错误处理。

尽管命令会很快变得非常复杂，但是这个工具有非常好的[文档](https://ffmpeg.org/ffmpeg.html)。此外，在我们的例子中，不会有任何太花哨的事情发生。

如果你在 Mac 或 Linux 机器上，安装过程非常简单。对于 Windows，请参考[这里的](https://github.com/adaptlearning/adapt_authoring/wiki/Installing-FFmpeg)。fluent-ffmpeg 库依赖于在我们的`$PATH`上的`ffmpeg`可执行文件(所以它可以从 CLI 调用，比如:`ffmpeg ...`)或者通过环境变量提供可执行文件的路径。

示例性的`.env`文件:

```
FFMPEG_PATH="D:/ffmpeg/bin/ffmpeg.exe"
FFPROBE_PATH="D:/ffmpeg/bin/ffprobe.exe"
```

如果这两个路径在我们的`$PATH`中不可用，则必须设置它们。

## 创建预览

现在我们知道了在 Node.js 运行时中使用什么工具来进行视频操作，让我们以上面提到的格式创建预览。我将使用幼稚甘比诺的“[这是美国](https://www.youtube.com/watch?v=VYOjWnS4cMY)”视频进行测试。

### 视频片段

视频片段预览很容易创建；我们要做的就是在合适的时候把视频切片。为了使片段成为视频内容的有意义和有代表性的样本，我们最好从视频总长度的 25–75%左右的某个点获取它。为此，当然要先获取视频时长。

为了得到视频的时长，我们可以使用 FFmpeg 自带的 ffprobe。ffprobe 是一个工具，它可以让我们获取视频的元数据，以及其他信息。

让我们创建一个帮助函数来获取持续时间:

```
export const getVideoInfo = (inputPath: string) => {
  return new Promise((resolve, reject) => {
    return ffmpeg.ffprobe(inputPath, (error, videoInfo) => {
      if (error) {
        return reject(error);
      }

      const { duration, size } = videoInfo.format;

      return resolve({
        size,
        durationInSeconds: Math.floor(duration),
      });
    });
  });
};
```

`ffmpeg.ffprobe`方法使用视频元数据调用提供的回调。`videoInfo`是一个包含许多有用属性的对象，但是我们只对 `format`对象感兴趣，其中有`duration`属性。持续时间以秒为单位。

现在我们可以创建一个函数来创建预览。

在我们这样做之前，让我们分解一下用于创建片段的 FFmpeg 命令:

```
ffmpeg -ss 146 -i video.mp4 -y -an -t 4 fragment-preview.mp4
```

*   `-ss 146`:在视频的 146 秒标记处开始视频处理(146 在这里只是一个占位符，我们的代码会随机生成秒数)
*   `-i video.mp4`:输入文件路径
*   `-y`:生成输出时覆盖任何现有文件
*   `-an`:从生成的片段中删除音频
*   `-t 4`:片段的持续时间(以秒为单位)
*   `fragment-preview.mp4`:输出文件的路径

现在我们知道了这个命令的样子，让我们来看看将为我们生成它的节点代码。

```
const createFragmentPreview = async (
  inputPath,
  outputPath,
  fragmentDurationInSeconds = 4,
) => {
  return new Promise(async (resolve, reject) => {
    const { durationInSeconds: videoDurationInSeconds } = await getVideoInfo(
      inputPath,
    );

    const startTimeInSeconds = getStartTimeInSeconds(
      videoDurationInSeconds,
      fragmentDurationInSeconds,
    );

    return ffmpeg()
      .input(inputPath)
      .inputOptions([`-ss ${startTimeInSeconds}`])
      .outputOptions([`-t ${fragmentDurationInSeconds}`])
      .noAudio()
      .output(outputPath)
      .on('end', resolve)
      .on('error', reject)
      .run();
  });
};
```

首先，我们使用之前创建的`getVideoInfo`函数来获取视频的持续时间。然后我们使用`getStartTimeInSeconds`助手函数得到开始时间。

让我们考虑一下开始时间(参数`-ss`),因为可能很难得到正确的时间。开始时间必须在视频长度的 25–75%之间，因为这是最具代表性的片段所在的位置。

但是我们还要保证随机生成的开始时间加上片段的时长不大于视频的时长(`startTime` + `fragmentDuration` ≤ `videoDuration`)。如果是这样的话，由于没有足够的视频剩余，该片段将被缩短。

记住这些要求，让我们创建函数:

```
const getStartTimeInSeconds = (
  videoDurationInSeconds,
  fragmentDurationInSeconds,
) => {
  // by subtracting the fragment duration we can be sure that the resulting
  // start time + fragment duration will be less than the video duration
  const safeVideoDurationInSeconds =
    videoDurationInSeconds - fragmentDurationInSeconds;

  // if the fragment duration is longer than the video duration
  if (safeVideoDurationInSeconds <= 0) {
    return 0;
  }

  return getRandomIntegerInRange(
    0.25 * safeVideoDurationInSeconds,
    0.75 * safeVideoDurationInSeconds,
  );
};
```

首先，我们从视频持续时间中减去片段持续时间。通过这样做，我们可以确保得到的开始时间加上片段持续时间将小于视频持续时间。

如果相减的结果小于 0，则开始时间必须为 0，因为片段持续时间比实际视频长。例如，如果视频长 4 秒，而预期的片段长 6 秒，则该片段将是整个视频。

该函数使用辅助函数`getRandomIntegerInRange`返回视频长度的 25-75%范围内的随机秒数。

```
export const getRandomIntegerInRange = (min, max) => {
  const minInt = Math.ceil(min);
  const maxInt = Math.floor(max);

  return Math.floor(Math.random() * (maxInt - minInt + 1) + minInt);
};
```

它使用`Math.random()`来获得该范围内的一个伪随机整数。这里的帮助者很精彩地解释了。

现在，回到命令，剩下要做的就是用生成的值设置命令的参数并运行它。

```
return ffmpeg()
  .input(inputPath)
  .inputOptions([`-ss ${startTimeInSeconds}`])
  .outputOptions([`-t ${fragmentDurationInSeconds}`])
  .noAudio()
  .output(outputPath)
  .on('end', resolve)
  .on('error', reject)
  .run();
```

代码是不言自明的。我们利用`.noAudio()`方法来生成`-an`参数。我们还分别在`end`和`error`事件上附加了`resolve`和`reject`监听器。结果，我们有了一个很容易处理的函数，因为它包含在一个承诺中。

在现实世界的设置中，我们可能会接收一个流并从函数中输出一个流，但是这里我决定使用承诺来使代码更容易理解。

以下是在“[这是美国](https://www.youtube.com/watch?v=VYOjWnS4cMY)”视频上运行该函数的一些示例结果。这些视频被转换成 gif 格式，以便更容易嵌入。

![This Is America Video Preview 1](img/5bad6e91c9533de51459c7ac6c4a6865.png)

![This Is America Video Preview 2](img/394b38a0f791d53402ff6e0960db43d0.png)

![This Is America Video Preview 3](img/c36ca4e1fc589eaef2706e7d0a9c7fbb.png)

由于用户可能会在小视窗中查看预览，我们可以不使用不必要的高分辨率，从而节省文件大小。

## 帧间隔

第二种选择是让 x 帧均匀分布在整个视频中。例如，如果我们有一个 100 秒长的视频，我们想从中取出 5 帧用于预览，我们将每 20 秒取一帧。然后我们可以将它们合并到一个视频中(使用 ffmpeg ),或者将它们加载到网站上，用 JavaScript 操纵它们。

让我们分解命令:

```
ffmpeg -i video.mp4 -y -vf fps=1/24 thumb%04d.jpg
```

*   `-i video.mp4`:输入视频文件
*   `-y`:输出覆盖任何现有文件
*   `-vf fps=1/24`:每 24 秒取一帧的过滤器
*   `thumb%04d.jpg`:以下列方式生成文件的输出模式:`thumb0001.jpg`、`thumb0002.jpg`等。`%04d`部分指定应该有四个十进制数

这个命令也非常简单，让我们在 Node 中实现它。

```
export const createXFramesPreview = (
  inputPath,
  outputPattern,
  numberOfFrames,
) => {
  return new Promise(async (resolve, reject) => {
    const { durationInSeconds } = await getVideoInfo(inputPath);

    // 1/frameIntervalInSeconds = 1 frame each x seconds
    const frameIntervalInSeconds = Math.floor(
      durationInSeconds / numberOfFrames,
    );

    return ffmpeg()
      .input(inputPath)
      .outputOptions([`-vf fps=1/${frameIntervalInSeconds}`])
      .output(outputPattern)
      .on('end', resolve)
      .on('error', reject)
      .run();
  });
};
```

与前面的函数一样，我们必须首先知道视频的长度，以便计算何时提取每一帧。我们用之前定义的助手`getVideoInfo`得到它。

接下来，我们将视频的持续时间除以帧数(作为参数传递，`numberOfFrames`)。我们使用`Math.floor()`函数来确保该数字是一个整数，并且再次乘以帧数小于或等于视频的持续时间。

然后，我们用这些值生成命令并执行它。我们再次将`resolve`和`reject`函数分别附加到`end`和`error`事件，以将输出包装在承诺中。

以下是一些生成的图像(帧):

![](img/6a7de69695dbef59fd95b2be686a2864.png)

![](img/3622e3b8b0f2e4c8247bbebf805d42ad.png)

![](img/f2e6dfeaa22f428bf4a8649537d4daf7.png)

如上所述，我们现在可以在浏览器中加载图像，并使用 JavaScript 将它们制作成幻灯片或使用 FFmpeg 生成幻灯片。让我们为后一种方法创建一个命令作为练习:

```
ffmpeg -framerate 1/0.6 -i thumb%04d.jpg slideshow.mp4
```

*   `-framerate 1/0.6`:每帧应该看 0.6 秒
*   `-i thumb%04d.jpg`:幻灯片中包含的图像的模式
*   `slideshow.mp4`:输出视频文件名

这是从 10 个提取帧中生成的幻灯片视频。每 24 秒提取一帧。

![Final Slideshow Result](img/8bd798c291adc13795929f36577e0736.png)

这个预览向我们展示了视频内容的一个非常好的概述。

## 有趣的事实

为了准备嵌入到文章中的结果视频，我必须将它们转换成。gif 格式。有许多在线转换器和应用程序可以帮我做到这一点。但是写一篇关于使用 FFmpeg 的文章，在这种情况下甚至不尝试使用它，感觉很奇怪。果然，将视频转换成 gif 格式只需一个命令:

```
ffmpeg -i video.mp4 -filter_complex "[0:v] split [a][b];[a] palettegen [p];[b][p] paletteuse" converted-video.gif
```

下面是[博文](https://engineering.giphy.com/how-to-make-gifs-with-ffmpeg/)解释其背后的逻辑。

当然，由于复杂的过滤器，这个命令不太容易理解，但它在展示 FFmpeg 有多少用例以及熟悉这个工具有多大用处方面大有帮助。

我没有使用在线转换器，因为工具是免费的，转换需要一些时间，而是在服务器端进行，我执行了这个命令，几秒钟后 gif 就准备好了。

## 摘要

您不太可能需要自己创建视频预览，但希望现在您已经知道如何很好地使用 FFmpeg 及其基本命令语法，足以在任何潜在的项目中使用它。关于预览格式，我可能会选择视频片段，因为 YouTube 会让更多人熟悉它。

我们可能应该生成低质量的视频预览，以保持预览文件较小，因为它们必须加载到用户的浏览器上。预览通常显示在一个非常小的视窗中，所以低分辨率应该不是问题。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.