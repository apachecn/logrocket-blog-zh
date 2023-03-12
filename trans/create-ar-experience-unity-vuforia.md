# 如何使用 Unity 和 Vuforia 创建 AR 体验- LogRocket 博客

> 原文：<https://blog.logrocket.com/create-ar-experience-unity-vuforia/>

在本文中，您将学习如何使用 Vuforia 和 [Unity](https://blog.logrocket.com/tag/unity/) 创建增强现实体验。您还将学习如何在 Android 智能手机上测试和运行您的应用。

这就是你要建造的东西👇

![AR Cube](img/6761a17ee06416fca009fd5c286b286c.png)

这个立方体可以用任何其他的 3D 物体代替，但是为了简单起见，我们在本教程中将只使用一个立方体。

下面是上述视频中发生的事情的详细分析:

1.  手机的摄像头检测图像
2.  该图像被识别为标记
3.  然后，手机在图像(或标记)上呈现一个对象

这种 AR 体验，即你的手机在图像上呈现 3D 内容，被称为基于标记的 AR 体验。这是我们今天的目标，我们将使用一个名为 Vuforia 的 SDK 来实现。

### Why Vuforia?

Vuforia 有许多功能，例如:

1.  图像跟踪
2.  目标跟踪
3.  地面跟踪
4.  多目标
5.  云识别
6.  虚拟按钮

还有更多。

我在本教程中使用 Vuforia 的原因之一是它设置起来既快又简单。您可以创建您的第一个 AR 体验，而无需编码的麻烦。

此外，如果你知道像 Android 这样的平台上 Vuforia 的配置步骤，你将能够在 iOS 上应用相同的步骤。这意味着你可以开发一次，然后将你的应用发布到多个平台。

与 ARCore 和 ARKit 相比，Vuforia 最大的优势在于它支持广泛的设备。除了智能手机和平板电脑，Vuforia 还支持微软 Hololens 和 Magic Leap 等增强现实耳机。你可以[在这里](https://library.vuforia.com/platform-support/recommended-devices)找到 Vuforia 推荐设备的完整列表。

## 先决条件

在我们进入 Unity 并开始项目之前，请确保您完成了以下任务:

1.  [注册成为 Vuforia 引擎开发者](https://library.vuforia.com/getting-started/register-vuforia-engine-developer)
2.  确保您安装了 Unity 2021.3 (LTS 版本)
3.  [检查您的设备是否支持增强现实](https://developers.google.com/ar/devices)

以下是我们将要介绍的内容:

*   项目设置
*   使用 Unity 设置 Vuforia
*   如何在 Vuforia 中创建图像目标？
*   在图像目标上呈现 3D 内容

## 项目设置

如果您还没有这样做，创建一个新的 Unity 3D 项目，并将其命名为“Hello Vuforia”

您可以通过在电脑上启动 Unity Hub 并点击“新建项目”来完成此操作。

![New Project Circled](img/569b90e8c7b6118afc360e8c5c9e077c.png)

点击“新建项目”后，弹出如下窗口。要确保我们正在创建 3D 项目，请执行以下步骤:

1.  从模板中选择“3D”
2.  将“项目名称”从“我的项目”更新为“Hello Vuforia”
3.  单击“创建项目”按钮

![3D, My Project, and Create Buttons](img/836868c7371e60ed72e37dc18f87bd59.png)

该项目将需要几分钟时间在 Unity 中打开。

保持项目开放，让我们一起努力集成 Vuforia。

## 使用 Unity 设置 Vuforia

在这一步中，您将把 Vuforia 包添加到 Unity 中的“Hello Vuforia”项目中。

首先，下载 [Vuforia 包](https://developer.vuforia.com/downloads/sdk)。请注意，除非你已经注册成为 Vuforia 开发者，否则你不能下载这个包。

![Add Vuforia Engine](img/0d50af6b9f1fa1ba9268c98b328efd33.png)

接下来，双击下载的 Unity 包，你会在 Unity 编辑器中自动看到这个弹出窗口。点击“导入”。

![Import Unity Package](img/68a25445ce5e378c197ede86c405e64a.png)

要验证 Vuforia 是否导入成功，请在 Unity 编辑器的“层级”面板中右键单击。如果在下拉列表中看到“Vuforia Engine”，则说明 Vuforia 已经导入成功。

![Ground Plane](img/3925d2c70560aaa4dec56800d6efe08a.png)

从场景中删除“主摄像机”。然后，右键单击“层次”面板> Vuforia 引擎>将 AR 摄像机添加到场景中。

![AR Camera](img/752041fafbfda57b03d6e892c9d4a0c2.png)

在“项目窗口”中，转到“资产”>“资源”,双击“VuforiaConfiguration ”,在检查器面板中查看其属性。我们将在下一步添加许可证密钥。

![App License Key](img/ffb101ede6c9b5b72818b425c9beecce.png)

要添加许可证密钥，请转到您的 [Vuforia 开发人员门户网站](https://developer.vuforia.com/vui/develop/licenses)许可证管理器，然后单击“获取基本”。

![License Manager Basic](img/8027f4fc863f83bf4b12e2651c8dee24.png)

基本计划(免费提供)包括图像目标、多目标、圆柱目标、真空标记和地平面等功能。没有 Vuforia 水印，您可以构建和发布无限数量的应用程序。

但是，如果您的应用程序使用 Vuforia 的高级功能，如模型目标和区域目标，Vuforia 水印将会出现。这意味着您可以使用这些功能开发您的应用程序用于个人研究目的，但不能在任何平台上发布应用程序。

如果你想在不显示 Vuforia 水印的情况下访问模型目标和区域目标，你可以购买高级计划。要更详细地了解 Vuforia，您可以[访问此链接](https://library.vuforia.com/faqs/pricing-and-licensing-options)。

保持许可证名称与您的项目名称相同；即 Hello Vuforia。您可以为您的许可证取任何名称。选中“条款和条件”框，然后单击“确认”。

![License Name](img/609718b5ef7916c22c6e0d6b25849983.png)

接下来，复制许可证密钥。

![Copy This](img/18f69cc1965ba6e95f7c198dc2c3bf71.png)

并将其粘贴到 Unity 中的“应用许可密钥”字段。

![Paste License Key](img/2649e466c75f1b08614a9a3469073094.png)

完美！您已经成功配置了 Vuforia 和您的 Unity 项目。

现在我们可以进入有趣的部分:图像识别。

## 如何在 Vuforia 中创建图像目标？

在这一步，我们将添加图像(或标记)，以便 Vuforia 可以检测和跟踪它们。这些被检测和跟踪的图像在 Vuforia 中被称为图像目标。图像目标的集合存储在数据库中。

图像目标和数据库都是从 Vuforia 开发人员门户创建的。

要开始使用图像目标，在 Vuforia 开发人员门户中，转到[目标管理器](https://developer.vuforia.com/vui/develop/databases)。然后点击“添加数据库”。

![Target Manager](img/174f0cfa9fd33197d8c939b7a3814a9e.png)

将数据库名称设置为“HelloVuforia”(无空格)，并选择设备类型。设备类型数据库是存在于您的应用程序中的数据库。如果您有一个大型数据库，那么选择云类型。最后，点击“创建”。

![Create Database](img/bb51dd8d7798c1f758d5e7cd18899a87.png)

数据库创建完成后，打开“HelloVuforia”并点击“添加目标”。

![Add Target](img/8eec272339d07c642bf21d254dd49035.png)

选择“图像”作为您的“类型”。您可以在“文件”字段中上传任何图像；我用的是 Pixabay 的[这个。该图像将成为您的图像目标，这意味着它将被您的应用程序检测和跟踪。](https://pixabay.com/illustrations/lens-camera-colorful-background-582605/)

![Type, File, Width, and Name Fields](img/7a9cd15a6aac26f75db5902b3ff0895c.png)

填写完上面的其他详细信息后，单击“添加”。您将看到数据库中列出的图像。对应每张图片，都有一个属性叫“评级”。尝试上传一张有更多星星的图片。星星的数量越多，就越容易发现和跟踪图像。

在下图中，您将看到两个图像目标。名为“石头 _ 图像”的目标只有三颗星，而“镜头 _ 图像”的目标却有五颗星。这两个图像都是可放大的，但是“lens_image”将提供更好的性能。

![Two Targets](img/d5d560e626b6c302df14ee5eb5ce57d4.png)

点击下载数据库> Unity 编辑器>下载。一旦它下载，你会得到一个名为“hello vu oria . unity package”的文件。双击此文件，在 Unity 中打开它。

![Download Database](img/e240b05597966ad05964a106351dc1e5.png)

您将看到下面的弹出窗口。点击“导入”。

![Import Package](img/5cf998f3d0af9015985fa17e170e5e37.png)

在 Unity 中导入数据库后，在项目面板中，转到“资产”>“资源”,双击“VuforiaConfiguration ”,在检查器面板中查看属性。在 Databases 部分，您会看到 HelloVuforia 被列出。这意味着您已经成功地将数据库导入到您的 Unity 项目中。

![Hello Vuforia](img/dc5440cfb4cefbc300fd90819876039d.png)

## 在图像目标上呈现 3D 内容

现在，您可以在图像目标的顶部显示一个立方体了。要做到这一点，请遵循本教程中的其余步骤。

在层次面板> Vuforia 引擎中右键单击，并在场景中添加一个图像目标。

![Image Target](img/2bffa094488ca49d4bf164c610891464.png)

选择“ImageTarget”在检查器面板中查看其属性。在图像目标行为(脚本)中，进行以下更改:

1.  将类型设置为来自数据库
2.  将数据库设置为 HelloVuforia
3.  将图像目标设置为 lens_image(或您上传到 HelloVuforia 数据库的任何其他图像)

![Type Selection](img/b3d4e6f810e88a01fe24977dc48da25d.png)

现在，在场景中添加一个立方体作为 ImageTarget 的子对象，如下所示👇：

![Cube Selection](img/81352ed0638281b859eff6b3b7d3e295.png)

将立方体缩小到 0.2(如果需要，也可以更小)。

![Scale](img/db07a6accd29d9a0695b0eb1c9732414.png)

然后，在 y 轴的正方向移动立方体一点点，让它看起来像是浮在图像上方。

![Move Cube](img/2fae8697160fdd046d27229f0a4d3e21.png)

就是这样！现在，您可以在您的 Android 设备上运行该应用程序了。

单击播放按钮，使用您的网络摄像头运行场景。

![Play Button](img/9a412a927db3ae3de43ca41c43f15c7e.png)

点击播放按钮后，将你的网络摄像头指向图像，立方体就会神奇地出现在图像上方。我已经把图像下载到我的 iPad 上了，但是你也可以把图像打印出来，然后把摄像头对准打印出来的图像。它给出了相同的结果。

![AR Cube](img/785f9e90676f45a9bde12d32142b0954.png)

## 结论

恭喜你！

你刚刚用 Unity 和 Vuforia 创造了一个增强现实体验。本教程是那些尝试创建第一次 AR 体验的人的基础教程。然而，立方体可以用任何其他 3D 模型替换。试试看！

如果你面临任何问题或需要一些帮助，请在 [Twitter](twitter.com/knightcube) 或 [LinkedIn](https://linkedin.com/in/knightcube) 上 ping 我，如果你正在寻找更多 Unity 教程和资源，请查看我的 [Gumroad 页面](https://knightcube.gumroad.com)或 [YouTube 频道](youtube.com/knightcubexr?sub_confirmation=1)。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)