# 在 Node.js 中用 sharp 处理图像

> 原文：<https://blog.logrocket.com/processing-images-sharp-node-js/>

图像是大多数处理用户生成内容的应用程序的重要组成部分。但是，过大或未经优化的图像文件会对性能和用户体验产生负面影响。一个强大的图像处理解决方案对于 UGC 管理是非常宝贵的。

sharp 是 Node.js 的高性能图像处理模块。该模块通过提供读取、增强和保存图像文件的简单解决方案来协助 UGC 管理。与大多数其他 Node.js 模块(如 ImageMagick、Jimp 或 Squoosh)相比，sharp 压缩图像的速度更快，并且产生高质量的结果。

sharp 将较大的常见图像格式转换为较小的网络友好图像。夏普可以读取 JPEG、PNG、WebP、AVIF、TIFF、GIF 和 SVG 图像格式。该模块可以生成 JPEG、PNG、WebP、AVIF 和 TIFF 格式的图像以及未压缩的原始像素数据。

在本教程中，我们将使用 sharp Node.js 库来分析和修改图像。本教程的唯一先决条件是用 Node.js 和 npm 设置一个系统。

我们将讨论以下内容:

## 建立鲜明的形象工程

要建立一个清晰的图像项目，首先在编辑器的终端中创建一个新目录:

```
mkdir sharp_project

```

接下来，迁移到新目录:

```
cd sharp_project

```

现在，初始化 npm:

```
npm init -y

```

接下来，安装夏普:

```
npm install sharp

```

现在，使用您最喜欢的 IDE 打开目录。在本教程中，我们将使用 VS 代码。

```
code .

```

我们将使用以下两幅图像:

![Sharp Image Project Sample Image](img/f848da177449bf601c1b6c25570cc09f.png)

![Sharp Image Project Sample Background Image](img/29782795304582f9b088e28adb5441e3.png)

## 将图像转换为灰度

要将图像转换为灰度，请在编辑器的终端中创建一个新文件:

```
touch _grayscale.js

```

接下来，复制以下代码:

```
const sharp = require('sharp')

const convertTograyscale = () => {
  sharp('./images/robo.jpg')
  .grayscale() // or .greyscale()
  .toFile(__dirname + '/processed_images/grayscale_robo.jpg')
}

convertTograyscale()

```

在这个例子中，我们从创建一个`sharp`模块的实例开始。我们使用`require()`函数范围内的`sharp()`实例来读取图像的路径。

为了执行处理任务，我们定义了`convertTograyscale()`函数。然后，我们将`sharp`模块的`grayscale()`方法链接到`sharp`实例来改变图像的外观。我们使用`toFile()`方法将修改后的图像保存在`processed_images`文件夹中。

现在，我们在终端上运行文件代码:

```
node _grayscale.js

```

新创建的灰度图像`grayscale_robo.jpg`可以在`processed_image`文件夹中找到:

![Image Converted To Grayscale With Sharp](img/d42af4b43c8bbae3c128cee30f336a63.png)

## 给图像着色

要给图像上色，首先创建一个新文件:

```
touch _tint.js

```

现在，复制以下代码:

```
const sharp = require('sharp')

const tintImage = () => {
  sharp ('./images/robo.jpg')
  .tint({r: 255, g: 0, b: 0})
  .toFile(__dirname + '/processed_images/tint_robo.jpg')
}

tintImage()

```

我们使用`sharp`模块的`tint()`方法来改变图像的颜色。使用这种方法，我们可以指定红色、绿色和蓝色色度值的强度。此方法的范围是 0 到 255，色度值越高，色调越饱和。对于这个例子，我们使用红色参数的最大值 255:`r`。

保存文件并运行以下命令:

```
node _tint.js

```

这是新创建的彩色图像，`tint_robo.jpg`:

![Image Tinted With Sharp](img/839fa2790a01f45be1cdfeb7377ad4c1.png)

要提取图像元数据，首先创建一个新文件:

```
touch _metadata.js

```

接下来，复制以下代码:

```
const sharp = require('sharp')

const imageMetadata = () => {
  const metadata = sharp('./images/robo.jpg').metadata()

  console.log(metadata)
}

imageMetadata()

```

我们使用`metadata()`函数提取图像元数据。我们保存`metadata`变量，并使用`console.log()`登录到终端。

现在，我们运行以下命令:

```
node _metadata.js

```

这会产生以下输出:

```
{
  format: 'jpeg',
  width: 1920,
  height: 1080,
  space: 'srgb',
  channels: 3,
  depth: 'uchar',
  density: 72,
  chromaSubsampling: '4:2:0',
  isProgressive: false,
  hasProfile: false,
  hasAlpha: false,
  orientation: 1,
  exif: <Buffer 45 78 69 66 00 00 4d 4d 00 2a 00 00 00 08 00 05 01 12 00 03 00 00 00 01 00 01 00 00 01 1a 00 05 00 00 00 01 00 00 00 4a 01 1b 00 05 00 00 00 01 00 00 ... 88 more bytes>,
  iptc: <Buffer 50 68 6f 74 6f 73 68 6f 70 20 33 2e 30 00 38 42 49 4d 04 04 00 00 00 00 00 00 38 42 49 4d 04 25 00 00 00 00 00 10 d4 1d 8c d9 8f 00 b2 04 e9 80 09 98 ... 4 more bytes>
}

```

## 旋转图像

要旋转图像，首先创建一个新文件:

```
touch _rotate.js

```

现在，添加以下代码:

```
const sharp = require('sharp');

const rotateImage = () => {
  sharp('./images/robo.jpg')
  .rotate(250)
  .toFile(__dirname + '/processed_images/rotate_robo.jpg')
}

rotateImage()

```

在本例中，`rotateImage()`函数读取图像并将其旋转 250 度后返回。我们在函数范围内使用了`rotate()`方法，它链接到了`sharp`模块。`rotate()`方法将旋转角度作为输入，并保存为新图像:`rotate_robo.jpg`。

现在，运行文件代码:

```
node _rotate.js

```

这是旋转后的图像:

![Image Rotated With Sharp](img/6b144eeb7c1b20b718e2442624d63e4a.png)

## 调整图像大小

要调整图像大小，请先创建一个新文件:

```
touch _resize.js

```

接下来，复制以下代码:

```
const sharp = require('sharp')

const resizeImage = () => {
  const resize = sharp('./images/robo.jpg')
  .resize(350, 260)
  .toFile(__dirname + '/processed_images/resize_robo.jpg')

  console.log(resize)
}

resizeImage()

```

为了调整图像的大小，我们首先将`resize()`函数链接到`sharp`实例。然后，我们将它保存在`processed_images`文件夹中。这一过程改变了整体尺寸，而不会裁剪或扭曲图像。

现在，我们运行以下命令:

```
node _resize.js

```

这会产生以下输出:

```
{
  format: 'jpeg',
  width: 350,
  height: 260,
  channels: 3,
  premultiplied: false,
  size: 12042
}

```

## 格式化图像

```
const formatImage = () => {
  sharp('./images/robo.jpg')
  .toFormat('png', {palette: true})
  .toFile(__dirname + '/processed_images/format_robo.png')
}

formatImage()

```

为了将`sharp`实例的文件格式从 JPEG 更改为 PNG，我们使用了`format()`方法。我们也给了`format()`方法一个压缩图像的选项。

每个`formatImage()`接受一个具有不同属性的独立对象。例如，`palette`属性只对 PNG 图像有效。只有 JPEG 图片可以使用`mozjpeg`属性，只有 WebP 图片可以使用`lossless`属性。`toFile()`方法用于将压缩图像保存在`processed_images`文件夹中。

## 裁剪图像

要裁剪图像，首先创建一个新文件:

```
touch _crop.js

```

接下来，复制以下代码:

```
const sharp = require('sharp')

const cropImage = () => {
  sharp('./images/robo.jpg')
  .extract({left: 740, width: 500, height: 300, top: 340})
  .toFile(__dirname + '/processed_images/crop_robo.png')
}

cropImage()

```

为了裁剪图像，我们首先将`extract()`函数链接到`sharp`实例。然后，我们将它保存在`processed_images`文件夹中。通过这个过程，我们可以指定:图像左侧要裁剪的水平空间(`left`)、所需图像`width`、所需图像`height`，以及图像上方要裁剪的垂直空间(`top`)。

在本例中，我们生成了一个 500 像素宽、300 像素高的裁剪框，其位置距离图像的左边界为 740 像素，距离图像的上边界为 340 像素。使用`extract`方法，图像中适合盒子的任何部分都将被保留。盒子外的任何东西都将被删除。

现在，复制以下代码:

```
node _crop.js

```

这是复制的图像:

![Image Cropped With Sharp](img/3f830c4a5dd130f41c35eb0ac7747c99.png)

## 创建合成图像

要合并两个或多个图像，请先创建一个新文件:

```
touch _composite.js

```

接下来，复制以下代码:

```
const sharp = require('sharp')

const compositeImage = () => {
  sharp ('./images/fall.jpg')
  .composite([{input: './images/robo.jpg', left: 900, top: 750}])
  .toFile(__dirname + '/processed_images/composite_robo.jpg')
}

compositeImage()

```

为了创建一个复合图像，我们首先将`composite()`函数链接到`sharp`实例。我们还向`sharp`模块添加了一个新的图像文件`fall.jpg`。在这个例子中，`fall.jpg`是经过处理的图像(或背景图像)。

sharp 模块的`composite()`方法接受一个数组作为输入。要合成的图像`robo.jpg`由数组中的单个对象读取。值得注意的是，合成图像的尺寸必须小于处理后的图像。我们可以指定以下对象属性:

*   `input`:将放置在已处理图像上的图像
*   `top`:合成在已处理图像上的图像的垂直位置
*   `left`:合成在已处理图像上的图像的水平位置

保存生成的图像，并运行以下命令:

```
node _composite.js

```

这是合成图像:

![Composite Image Created With Sharp](img/0c14fb2ff1f68b5702db338790cd86f9.png)

## 模糊图像

要模糊图像，首先创建一个新文件:

```
touch _blur.js

```

接下来，添加以下代码:

```
const sharp = require('sharp')

const blurImage = () => {
  sharp('./images/robo.jpg')
  .blur(9)
  .toFile(__dirname + '/processed_images/blur_robo.jpg')
}

blurImage()

```

在我们的例子中，我们使用`sharp`模块的`blur()`方法对图像应用高斯模糊。这种技术使用高斯函数来降低图像边缘像素的权重，从而减少图像细节和图像噪声。对于这个例子，高斯`.blur()`西格玛是 9。

保存文件，并运行以下命令:

```
node _blur.js

```

这是模糊的图像:

![Image Blurred With Sharp](img/df851a21ec8a218412b7ae0300713b0e.png)

## 锐化图像

要锐化图像，首先创建一个新文件:

```
touch _sharpen.js

```

接下来，添加以下代码:

```
const sharp = require('sharp')

const sharpenImage = () => {
  sharp ('./images/robo.jpg')
  .sharpen(13)
  .toFile(__dirname + '/processed_images/sharpen_robo.jpg')
}

sharpenImage()

```

为了锐化图像，我们使用`sharpen()`功能。如果没有指定参数，这种技术将产生原始图像的快速、适度锐化的版本。但是，如果指定了参数，此功能将执行更慢、更精确的锐化。对于这个例子，`sharpen()`西格玛是 13。

现在，复制以下代码:

```
node _sharpen.js

```

这是锐化后的图像:

![Image Sharpened With Sharp](img/5192d30a505e64b28aa62261a6bbad34.png)

## 翻转图像

要翻转(或水平反转)图像，首先创建一个新文件:

```
touch _flip.js

```

接下来，复制以下代码:

```
const sharp = require('sharp')
const flipImage = async () => {
  await sharp('./images/robo.jpg')
  .flip()
  .toFile(__dirname + '/processed_images/flip_robo.jpg');
}
flipImage()

```

我们将`flip()`方法链接到`sharp`实例，以在 x 轴上翻转图像。

现在，运行以下程序:

```
node _flip.js

```

这是翻转的图像:

![Image Flipped With Sharp](img/ee73d986000eaa794252d2dae81e8c1a.png)

## 翻转图像

要翻转(或垂直反转)图像，首先创建一个新文件:

```
touch _flop.js

```

接下来，添加以下代码:

```
const sharp = require('sharp')
const flopImage = async () => {
  await sharp('./images/robo.jpg')
  .flop()
  .toFile(__dirname + '/processed_images/flop_robo.jpg');
}
flopImage()

```

我们将`flop()`方法链接到`sharp`实例，以在 y 轴上`flop`图像。

现在，运行以下命令:

```
node _flop.js

```

下面是这张失败的图片:

![Image Flopped With Sharp](img/a021c1e0363072d182dde51cdabdadc8.png)

## 向图像添加文本

夏普目前还没有向图像添加文本的方法。然而，作为一种变通方法，我们可以使用 SVG 绘制文本，然后使用`composite()`方法将文本图像添加到原始图像中。

首先创建一个新文件:

```
touch _text.js

```

接下来，复制以下代码:

```
const sharp = require('sharp');

const addText = () => {
  const width = 900;
  const height = 500;
  const text = "E.T, go home";

  const svgText = `
  <svg width="${width}" height="${height}">
    <style>
      .title { fill: red; font-size: 85px}
    </style>
    <text x="45%" y="40%" text-anchor="middle" class="title">${text}</text>
  </svg>`

  const svgBuffer = Buffer.from(svgText);

  sharp ('./images/robo.jpg')
  .composite([{input: svgBuffer, left: 1150, top: 90}])
  .toFile(__dirname + '/processed_images/text_robo.jpg')
}

addText()

```

对于我们的解决方法，我们使用一个带有四个变量的`addText()`函数:期望的文本区域`width`、期望的文本区域`height`、将使用 SVG 绘制的`text`字符串(在本例中为`E.T, go home`)以及一个保存文本的透明容器`svgText`。

SVG 元素有两个子元素:`style`和`text`。`style`元素使用 CSS 来改变`text`的外观。`fill`和`font-size`属性分别修改文本的颜色和大小。

`text`元素有四个属性:水平位置(`x`)、垂直位置(`y`)、对齐(`text-anchor`)和`class`。最后一个属性`class`，指定 CSS 样式应该如何应用到`text`元素。`${text}`从变量`text`中插入字符串`E.T, go home`。

接下来，我们使用`Buffer.from()`函数从`svgText`变量创建一个缓冲对象，然后将它存储在`svgBuffer`变量中。

为了给图像添加文本，我们将`composite()`函数链接到`sharp`实例，并使用`svgText`变量作为输入。在本例中，`robo.jpg`是处理后的图像(或背景图像)。

现在，运行以下命令:

```
node _text.js

```

这是带有文本的图像:

![Image With Text Added With Sharp](img/6d8b9a3cad69f879579e0d83d645ef90.png)

## 将图像存储在数据库中

使用 sharp 库时，您可能希望将处理过的图像存储在数据库中。该操作可以两种方式进行:

1.  使用 JavaScript 承诺
2.  使用异步/等待语法

在我们开始讨论存储图像的两种方法之前，您需要创建一个名为`touch_database.js`的新文件。

### 使用 JavaScript 承诺

承诺是表示异步操作的成功或失败及其生成值的对象。夏普回复了一个承诺，只要我们处理完图像，就可以用它来执行操作。

```
const sharp = require('sharp')

const usingPromise = () => {
  sharp('./images/robo.jpg')
  .extract({left: 740, width: 500, height: 300, top: 340})
  .then(() => {

    // Write code to store the image to the database

  })
  .catch((err) => console.warn(err));
}

usingPromise()

```

承诺提供了两种处理其结果的技巧。成功的结果由`.then()`方法处理。在这个例子中，在图像被成功处理后，`.then()`方法执行代码将图像保存到数据库。如果图像处理失败，失败结果由`.catch()`处理。

### 使用异步/等待语法

关键字`async`简化了异步的、基于承诺的代码的工作，这使得代码看起来和行为像同步代码。

```
const usingAsync = async() => {
  try{
    const image = await sharp('./images/robo.jpg')
    .extract({left: 740, width: 500, height: 300, top: 340})
    .toFile(__dirname + '/processed_images/crop_robo.png')

    // Write code to store image to the database

    return image

  }catch(e){
     // handles error if any
  }
}
usingAsync()

```

在异步函数内部，`try` … `catch`块用于处理错误。`await`关键字用在调用返回承诺的函数之前。

## 结论

在本教程中，我们回顾了如何使用 sharp 库处理 Node.js 中的图像。我们使用了`grayscale()`、`tint()`、`rotate()`、`resize()`、`crop()`、`blur()`、`sharpen()`、`flip()`和`flop()`方法来改变图像的外观、样式和形状。我们使用`metadata()`方法提取图像元数据。我们使用`composite()`方法合并了两幅图像。我们还使用了`composite()`方法，通过 SVG 变通方法向图像添加文本。最后，我们使用了`format()`方法来改变图像类型和压缩图像。

为了进一步提高 Node.js 应用程序的效率和性能，这里有一些额外的建议。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.