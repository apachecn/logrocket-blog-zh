# 将增强和虚拟现实集成到 Node.js 应用程序中

> 原文：<https://blog.logrocket.com/integrating-augmented-virtual-reality-node-js-app/>

虚拟现实(VR)是一种交互式技术，允许用户进入计算机生成的三维世界，并体验它，就好像他们真的在那里一样。使用先进的硬件和软件，虚拟现实技术创造了一个模拟现实世界的模拟环境，包括栩栩如生的景象、声音甚至感觉。

增强现实(AR)是一种用虚拟元素增强现实世界的技术，创造了一种无缝融合物理和数字世界的分层体验。与将用户完全沉浸在计算机生成的环境中的虚拟现实不同，AR 使用智能手机、平板电脑或专用眼镜等设备将数字信息叠加到用户对现实世界的看法上。这可以采取叠加在用户周围的图像、文本、视频或动画的形式，允许他们与数字内容实时交互。

在本教程中，您将学习如何使用 [A-Frame](https://aframe.io/) 和 [AR.js](https://ar-js-org.github.io/AR.js-Docs/) 库将虚拟现实和增强现实集成到 Node.js 应用程序中。

本教程结束时，您将拥有一个能够提供虚拟和增强现实体验的应用程序，如下例所示:

![An Example Of An App With Virtual And Augmented Reality](img/a82faa19525544086cf78b8b15d7fbc3.png)

*向前跳跃*:

## 先决条件

为了遵循这个教程，你需要[节点](https://nodejs.org/en/)和 [npm](https://www.npmjs.com/) 。

## 创建项目结构

在本节中，您将创建项目的目录结构。然后，您将为应用程序的服务器初始化 Node.js 项目。

创建一个名为`node-AR-VR`的新目录:

```
mkdir node-AR-VR

```

导航到您刚刚创建的目录:

```
cd node-AR-VR

```

接下来，创建一个名为`public`的目录:

```
mkdir public

```

`public`目录将用于存储静态资产，如图像、样式表和客户端 JavaScript 文件。

然后，导航到`public`目录:

```
cd public

```

使用以下命令创建存储图像的目录:

```
mkdir images

```

接下来，从 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3531358) 下载下面这张由[托马佐利维尔](https://pixabay.com/users/tomazolivier-9395471/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3531358)拍摄的 360 度图像，并以`beach.jpg`的名字保存在`images`目录下。您将使用此图像来创建虚拟现实体验:

![Image By Tomazolivier To Use For Virtual And Augmented Reality App](img/7a4ed4742e186cf6f7758763dcf025d3.png)

导航到项目的根目录:

```
cd ..

```

然后，使用默认设置创建一个新的节点项目:

```
npm init -y

```

安装此应用程序的服务器依赖项:

```
npm install express

```

上面的命令安装了`express`节点模块。`express`是一个流行的 JavaScript 库，用于使用 Node.js 构建 web 应用程序。它为创建路线、处理请求和呈现模板提供了一个简单易用的接口。它被设计成轻量级和灵活的，并且被广泛用作许多 web 应用程序框架的基础。此依赖项将用于创建应用程序的服务器。

## 创建应用程序的服务器

在本节中，您将创建应用程序的服务器。当`/`端点接收到 HTTP 请求时，该服务器将负责为您实现虚拟现实和增强现实功能的网页提供服务。

使用您喜欢的代码编辑器，在项目的根目录下创建一个名为`server.js`的文件，并向其中添加以下代码:

```
const express = require('express')
const app = express()
const port = 3000

```

在上面的代码块中，首先，您导入了`express`模块。

导入模块后，您创建了一个 Express 应用程序的实例，并将对象存储在一个名为`app`的变量中。`app`变量将用于配置和控制 web 服务器。

最后，您创建了一个名为`port`的常量变量，并将其赋值为`3000`。这是 web 服务器将侦听传入请求的端口号。

将以下代码添加到您的`server.js`文件的底部:

```
app.use(express.static('public'))

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})

```

上面代码中的第一行告诉 Express 应用程序提供存储在`public`目录中的静态文件。

第三行和剩余的行告诉 Express 应用程序开始在由`port`变量指定的端口上监听传入的请求。除了`port`，一个匿名函数也被作为参数传递给`app.listen()`方法。当服务器开始监听传入的请求时，该函数将向控制台记录一条消息。

回到您的终端，运行以下命令启动您的服务器:

```
node server.js

```

打开您喜欢的浏览器并导航到 [http://localhost:3000/](http://localhost:3000/) ，您应该会看到以下内容:

![An Error In The Express App](img/4de4b416de5e68aafdc0704409ea6245.png)

您会看到上面的错误，因为您还没有创建任何页面。

## 创建应用程序布局

在本节中，您将为您的应用程序创建主 HTML 文件，该文件将包含向客户端显示的布局。布局由一个带有两个选项卡的页面组成:“AR”和“VR”。“AR”选项卡是您将实现增强现实功能的地方，而“VR”选项卡是您将实现虚拟现实功能的地方。

回到您的代码编辑器，在`public`目录中，创建一个名为`index.html`的文件，并向其中添加以下代码:

```
<!DOCTYPE html>
<html>
<link href="https://cdn.jsdelivr.net/npm/[email protected]/dist/css/bootstrap.min.css" rel="stylesheet"
  integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
<body>
  <script src="https://cdn.jsdelivr.net/npm/[email protected]/dist/js/bootstrap.bundle.min.js"
    integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM"
    crossorigin="anonymous"></script>
</body>
</html>

```

在上面的代码块中，您向这个 HTML 文档添加了 Bootstrap。

在`body`部分内的`<script>`标记前添加以下代码:

```
<body>
  <div class="bg-dark">
    <div class="pt-4">
      <nav class="nav nav-pills justify-content-center">
        <a class="nav-item nav-link active" data-bs-target="#tab-1" data-bs-toggle="tab">VR</a>
        <a class="nav-item nav-link " data-bs-target="#tab-2" data-bs-toggle="tab">AR</a>
      </nav>
    </div>
    <div class="tab-content pt-4" style="height: 100vh">
      <div class="tab-pane active" id="tab-1">
        <iframe src="vr.html" style="height: 80vh; width: 100vw">
        </iframe>
      </div>
      <div class="tab-pane  " id="tab-2">
        <iframe src="ar.html" style="height: 80vh; width: 100vw">
        </iframe>
      </div>
    </div>
  </div>
</body>

```

这里，您使用 Bootstrap 创建了两个名为“AR”和“VR”的选项卡。这些选项卡将用于在单独的`iframe`元素中显示两个 HTML 文件的内容。这些 HTML 文件将被命名为`ar.html`和`vr.html`，并将分别包含 AR 和 VR 功能。

您的`index.html`文件应该如下所示:

```
<!DOCTYPE html>
<html>
<link href="https://cdn.jsdelivr.net/npm/[email protected]/dist/css/bootstrap.min.css" rel="stylesheet"
  integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">

<body>
  <div class="bg-dark">
    <div class="pt-4">
      <nav class="nav nav-pills justify-content-center">
        <a class="nav-item nav-link active" data-bs-target="#tab-1" data-bs-toggle="tab">VR</a>
        <a class="nav-item nav-link " data-bs-target="#tab-2" data-bs-toggle="tab">AR</a>
      </nav>
    </div>
    <div class="tab-content pt-4" style="height: 100vh">
      <div class="tab-pane active" id="tab-1">
        <iframe src="vr.html" style="height: 80vh; width: 100vw">
        </iframe>
      </div>
      <div class="tab-pane  " id="tab-2">
        <iframe src="ar.html" style="height: 80vh; width: 100vw">
        </iframe>
      </div>
    </div>
  </div>
  <script src="https://cdn.jsdelivr.net/npm/[email protected]/dist/js/bootstrap.bundle.min.js"
    integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM"
    crossorigin="anonymous"></script>

</body>

</html>

```

回到您的浏览器和您访问应用程序的选项卡，您应该看到以下内容:

![VR And AR Tabs](img/ccd0174e58aa1729bfa7773115ce3019.png)

## 将虚拟现实集成到应用中

在本节中，您将学习如何使用 [A-](https://aframe.io/docs/1.4.0/introduction/) [F](https://aframe.io/docs/1.4.0/introduction/) [框架](https://aframe.io/docs/1.4.0/introduction/)库将虚拟现实集成到`VR`选项卡中。

A-Frame 是一个为构建虚拟现实体验而设计的 web 框架，它构建在 HTML 之上，易于使用。A-Frame 不仅仅是一个简单的 3D 场景图或标记语言，而是建立在一个强大的实体组件结构上，允许轻松操作 three.js，提供了一种强大而灵活的方式来设计 VR 体验。

回到您的代码编辑器，在`public`目录中创建一个名为`vr.html`的文件，并向其中添加以下代码:

```
<!DOCTYPE html>
<html>
<head>
    <script src="https://aframe.io/releases/1.4.0/aframe.min.js"></script>
</head>
<body>
</body>
</html>

```

这里，您将 A 帧库脚本添加到了`head`部分。该脚本包含允许您将虚拟现实功能添加到该选项卡的代码。

现在，将以下代码添加到这个 HTML 文件的`body`部分:

```
<body>
    <a-scene>
    </a-scene>
</body>

```

在这段代码中，您添加了一个`[<a-scene>](https://aframe.io/docs/1.4.0/core/scene.html)`元素，这是一个表示场景的 A 帧元素。

A-Frame 中的场景是一个虚拟环境，作为所有[实体](https://aframe.io/docs/1.4.0/core/entity.html)的全局容器，如网格、形状、灯光和摄像机，它们可以进行交互和动画制作，以创建一个可信的沉浸式虚拟现实体验。

实体是充当占位符的对象，开发人员可以将组件附加到这些占位符上。这些组件为实体提供外观、行为和功能，以创建交互式虚拟现实体验。

现在，在`<a-scene>`元素中添加以下代码:

```
<body>
    <a-scene>
        <a-sky src="./images/beach.jpg" rotation="0 -130 0"></a-sky>
    </a-scene>
</body>

```

在`<a-scene>`内部，你添加了一个`<a-sky>`元素。`<a-sky>`是一个由 A-Frame 提供的元素，允许你给场景添加背景色或者 360 度的图像。这是通过创建一个内表面应用了选定颜色或纹理的大球来实现的。

在上面的代码中，选择的纹理是存储在`images`目录中的名为`beach.jpg`的 360 度图像。纹理由`src`属性指定，旋转由`rotation`属性指定。

完整的`vr.html`文件应该如下所示:

```
<!DOCTYPE html>
<html>
<head>
    <script src="https://aframe.io/releases/1.4.0/aframe.min.js"></script>
</head>
<body>
    <a-scene>
        <a-sky src="./images/beach.jpg" rotation="0 -130 0"></a-sky>
    </a-scene>
</body>
</html>

```

回到您的浏览器，刷新您访问该应用程序的选项卡，探索虚拟现实体验:

![Virtual Reality Experience](img/1ab3bc0707a365cd441e7887eb0796f6.png)

## 将增强现实集成到应用中

在本节中，您将学习如何使用 A-Frame 和 [AR.js](https://ar-js-org.github.io/AR.js-Docs/) 库将增强现实集成到`ar`选项卡中。

AR.js 是一个轻量级的库，允许在 web 上实现增强现实，提供图像跟踪、基于位置的 AR 和标记跟踪等功能。

图像跟踪包括使用相机检测 2D 图像，并在图像上或图像附近覆盖各种形式的内容，如 2D 图像、gif、3D 模型和 2D 视频。

基于位置的 AR 涉及根据用户的真实世界位置在用户的设备上显示增强现实内容。这种类型的 AR 用于创建与现实世界中特定地点相关联的体验。用户可以四处移动，最好是在户外，并使用他们的智能手机来查看与他们的物理位置相对应的 AR 内容。随着用户移动和旋转他们的手机，AR 内容将改变以反映他们的位置和方向，内容根据其与用户的距离而变大或变小。

标记跟踪包括用摄像机检测标记，并用各种形式的内容覆盖它。与图像跟踪类似，标记是稳定的，但在形状、颜色和大小方面有所限制。这个特性非常适合需要大量不同内容的标记的情况。

在本教程中，您将在`ar`选项卡中实现标记跟踪功能。

回到您的代码编辑器，在`public`目录中创建一个名为`ar.html`的文件，并向其中添加以下代码:

```
<!DOCTYPE html>
<html>
<head>
    <script src="https://aframe.io/releases/1.4.0/aframe.min.js"></script>
    <script src="https://raw.githack.com/AR-js-org/AR.js/master/aframe/build/aframe-ar.js"></script>
</head>
<body>
</body>
</html>

```

在这里，您将 A-Frame 和 AR.js 脚本添加到了`head`部分。这些脚本包含允许您将增强现实功能添加到该选项卡的代码。

现在，将以下代码添加到这个 HTML 文件的`body`部分:

```
<body>
    <a-scene embedded arjs>
    </a-scene>
</body>

```

在这段代码中，您添加了一个`<a-scene>`元素。`embedded`组件通过消除全屏 CSS 样式简化了将 A-Frame 集成到现有网页的过程，允许在布局中更平滑地集成。 *`arjs`* 指定场景应该使用 AR.js 库来显示增强现实内容。

在`<a-scene>`元素中添加以下代码:

```
<body>
    <a-scene embedded arjs>
        <a-marker preset="hiro">
            <a-box position="0 0.5 0" material="color: red;"></a-box>
        </a-marker>
        <a-entity camera></a-entity>
    </a-scene>
</body>

```

您向`<a-scene>`添加了以下元素:一个`a-marker`和一个`a-entity`。

`a-marker`用于定义摄像机将寻找和跟踪的 AR 标记。`preset`属性被设置为`hiro`，这是一个预定义的标记图像，包含在 AR.js 中。在`a-marker`中，有一个`a-box`元素定义了一个红色的盒子及其在场景中的位置。

带有`camera`组件的`a-entity`元素创建场景的摄像机。`camera`是用户感知场景的视角。

只有检测到[hiro.png](https://raw.githubusercontent.com/AR-js-org/AR.js/master/data/images/hiro.png)图像时，`a-box`才会在场景中显示:

![Hiro.Png Image](img/8a50add63437c7187785e11754feaac0.png)

请确保将此图像下载到您的移动设备，以测试增强现实功能。

完整的`ar.html`文件应该如下所示:

```
<!DOCTYPE html>
<html>
<head>
    <script src="https://aframe.io/releases/1.4.0/aframe.min.js"></script>
    <script src="https://raw.githack.com/AR-js-org/AR.js/master/aframe/build/aframe-ar.js"></script>
</head>
<body>
    <a-scene embedded arjs>
        <a-marker preset="hiro">
            <a-box position="0 0.5 0" material="color: red;"></a-box>
        </a-marker>
        <a-entity camera></a-entity>
    </a-scene>
</body>
</html>

```

回到您的浏览器，刷新您访问此应用程序的选项卡，允许此应用程序访问您的网络摄像头，然后单击“AR”选项卡。标签打开后，在手机上打开`hiro.png`图像，并显示给网络摄像头。当网络摄像头检测到图像时，您应该会看到以下内容:

![Augmented Reality Demo](img/2e3c0541ef63681a9259fbf23561d249.png)

## 结论

在本教程中，首先，您学习了什么是虚拟和增强现实。然后您学习了如何使用 A-Frame 库将虚拟现实集成到您的节点应用程序中。最后，您学习了如何使用 A-Frame 库和 AR.js 库将增强现实集成到您的应用程序中。

编码快乐！

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.