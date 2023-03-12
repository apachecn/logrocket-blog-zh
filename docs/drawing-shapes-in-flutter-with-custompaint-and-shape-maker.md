# 使用 CustomPaint 和 Shape Maker 在 Flutter 中绘制形状

> 原文：<https://blog.logrocket.com/drawing-shapes-in-flutter-with-custompaint-and-shape-maker/>

作为一名移动开发人员，您可能会发现自己正在开发一个需要高度定制的用户交互的应用程序。你的设计团队可能会想出一些古怪的、前所未见的 UI/UX，而你的工作就是找出如何执行它。或者说，你正在做一个宠物项目，你想制作引人注目的视觉效果来吸引你的用户。想象一下，如果您不能访问这样做所需的资产，这将是多么令人沮丧，也许是因为文件的格式不正确，它们添加到您的项目中太昂贵了，等等。

不要慌！颤振已经覆盖了你。

Flutter 的 [CustomPaint](https://api.flutter.dev/flutter/widgets/CustomPaint-class.html) widget 可以让你将你的想象力倾注到数字画布上。用这个低级的绘画 API，你几乎可以在屏幕上画任何东西。这类似于在纸上画画，但是你有一个 API 和一个[画布](https://api.flutter.dev/flutter/dart-ui/Canvas-class.html)在上面画画，而不是铅笔和纸。

在本教程中，我们将向您介绍 CustomPaint，如何使用它，使用它时可能会面临什么问题，以及最终使用 [Flutter Shape Maker 工具的必要性。](https://fluttershapemaker.com/)

以下是我们将要介绍的内容:

## 如何使用自定义画图

CustomPaint 是 Flutter 中的一个小部件，它可以在绘制阶段生成一个画布。画布的坐标系与`CustomPaint`对象的坐标系相匹配。

首先，CustomPaint 要求它的`painter`在画布上绘画。在绘制其子窗口后，小部件要求`[foregroundPainter](https://api.flutter.dev/flutter/widgets/CustomPaint/foregroundPainter.html)`属性进行绘制。画师被限制在一个矩形区域内，该区域从原点开始，包含一个指定大小的区域。如果他们冒险超出这个分配的空间，可能没有足够的内存来光栅化绘画命令。

下面的视频很好地解释了 Flutter 的 CustomPaint 小部件是如何工作的:

 [https://www.youtube.com/embed/kp14Y4uHpHs?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/kp14Y4uHpHs?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

要查看 Flutter CustomPaint 的运行情况，让我们试着画一条简单的线:

![Flutter CustomPaint Trial Simple Line Drawing](img/7a730e1d47cedd5415e94a51ec73d788.png)

首先，在代码中引入 CustomPaint 小部件。绘制简单线条的基本代码如下所示:

```
// Step 1
class MyBookings extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Custom paint Demo'),
      ),
      body: Container(
        child: Center(
          child: CustomPaint(
            size: Size(300, 200),
            painter: LinePainter(),
          ),
        ),
      ),
    );
  }
}

```

CustomPaint 至少需要两个属性:

1.  `[painter](https://api.flutter.dev/flutter/widgets/CustomPaint/painter.html)`在孩子们面前画画
2.  [`size`](https://api.flutter.dev/flutter/widgets/CustomPaint/size.html) ，指定画线的画布

如果你想在任何部件上画线，比如`container`、`stack`、`row`、`column`等。，将`size`替换为`child`，将`painter`替换为`foregroundPainter`:

```
CustomPaint(
  child: Container(
    width: 300,
    height: 200,
    color: Colors.amberAccent,
  ),
  foregroundPainter: LinePainter(),
)

```

无论给出什么，这条线都会被画在它上面。`foregroundPainter`在子对象上面画一条线。

结果看起来像这样:

![CustomPaint Foregroundpainter Line Over Child Result](img/73ade88f1224caee87b5ba451c978680.png)

现在让我们创建一个扩展 CustomPainter 的类。这个班负责画真实的画。它有一个 paint 和 canvas 类，允许你绘制不同的形状，如线条、圆形、矩形、自定义路径等。

```
class LinePainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    var paint = Paint()
      ..color = Colors.teal
      ..strokeWidth = 15;

    Offset start = Offset(0, size.height / 2);
    Offset end = Offset(size.width, size.height / 2);

    canvas.drawLine(start, end, paint);
  }

  @override
  bool shouldRepaint(CustomPainter oldDelegate) {
    return false;
  }
}

```

`Paint`类用于配置绘画属性，例如钢笔的颜色和大小。除此之外，它还提供了许多其他有趣的东西供你玩耍。`paint`类通常是在绘画之前配置的，就像你决定用哪种颜色的笔在纸上绘画一样。

[`canvas`类](https://api.flutter.dev/flutter/dart-ui/Canvas-class.html)提供了各种各样的方法来真正开始绘画。

使用 CustomPaint 在 Flutter 中绘制圆形也很容易。Canvas 提供了一个`drawCircle`方法，顾名思义就是画一个圆。圆以第一个参数给出的点为中心；半径在第二个参数中给出。

用下面的代码替换上例中的`LinePainter`类，在屏幕上看到一个圆:

```
class CirclePainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    var paint = Paint()
      ..color = Colors.teal
      ..strokeWidth = 15;

    Offset center = Offset(size.width / 2, size.height / 2);

    canvas.drawCircle(center, 100, paint);
  }

  @override
  bool shouldRepaint(CustomPainter oldDelegate) {
    return false;
  }
}

```

![Custompaint Flutter CirclePainter Display](img/c2306b962472708dc6b4e3f1deac1e6a.png)

## 在颤振中绘制具有挑战性的形状

画简单的形状，如线条和圆形，很容易。但是假设你想画一个弯曲的形状，像这样:

![Flutter Curved Shape Drawing Display](img/f78a615989fdceca1807bf4d2107b98e.png)

Canvas 提供了一种使用`drawPath`方法绘制路径的方法。这里，`Path`类在移动指针时非常有用。使用`path.quadraticBezierTo(x1,y1,x2,y2)`方法绘制曲线，以`x1,y2`为控制点绘制曲线到`x2,y2`:

```
class MyBookings extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Custom paint Demo'),
      ),
      body: Container(
        child: Center(
          child: CustomPaint(
            size: Size(400,400), 
            painter: CurvedPainter(),
          ),
        ),
      ),
    );
  }
}
class CurvedPainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    var paint = Paint()
      ..color = Colors.teal
      ..strokeWidth = 15;

    var path = Path();

    path.moveTo(0, size.height * 0.7);
    path.quadraticBezierTo(size.width * 0.25, size.height * 0.7,
        size.width * 0.5, size.height * 0.8);
    path.quadraticBezierTo(size.width * 0.75, size.height * 0.9,
        size.width * 1.0, size.height * 0.8);
    path.lineTo(size.width, size.height);
    path.lineTo(0, size.height);

    canvas.drawPath(path, paint);
  }

  @override
  bool shouldRepaint(CustomPainter oldDelegate) {
    return true;
  }
}

```

这里真正的挑战是在开发过程中识别控制点。如果你正在开发一个有很多曲线的 UI，确定一个控制点的最佳位置可能是棘手和耗时的。

虽然 Flutter 提供了一个热重载特性，允许你在开发时快速迭代设计，但有时你需要更多来把控制点放在最好的地方。简而言之，您需要一些东西，允许您用 UI 控件而不是代码来管理曲线。

## 颤振成形机

由 [Paras Jain](https://twitter.com/theretroportal) 开发的 Flutter Shape Maker 是目前唯一一个可以让你用 UI 控件管理曲线的工具集。

要配置 Flutter Shape Maker 来绘制形状，就像在现实世界中一样，首先选择您的画布，然后开始在其上绘制。

### 形状大小

这里我们有一个选项来选择我们的画布大小。确保您配置的大小与最终形状的长宽比最匹配。例如，如果您想要的形状是矩形，那么您必须将大小设置为类似于`800(W) * 400(H)`的值。

![Select Canvas Size Flutter Shape Maker Tutorial](img/00e85442bce6bbb128f2c5a30d895497.png)

默认情况下，响应代码选项将打开。让响应代码在所有设备上保持形状比例一致是一个很好的做法。

### 钢笔工具

钢笔工具允许您在画布上绘制点和创建所需的形状。对齐网格点有助于创建精确的绘图。

![Pen Tool Plot Points Flutter Shapemaker](img/139e9a57958235df2b65f61401219248.png)

### 层

一层只是各种形状的堆叠。这对于创建由多个形状组成的复杂场景非常有帮助。您可以添加、删除、隐藏和显示任何层，以便一次管理一个形状:

![Layer Stacking Example Flutter Shape Maker](img/7da0e22c982e87f5ceae46e460bd83ed.png)

### 移动

这使您能够自由移动整个形状，并对形状的位置进行一些最终调整。

![Move Tool Adjustment Example Flutter Shape Maker](img/291ae964ef00b8cc222a158adbcd44ca.png)

### 获取代码

获取代码是 Flutter Shape Maker 的独特卖点。自定义油漆代码现在只需点击一下。单击它，您将获得代码的响应版本，可以直接包含在您的代码中:

![Get Code Custom Paint Code Output Example](img/cbe3f0e54f539056cbf7c60e80a4adbb.png)

### 创建曲线

有了 Flutter Shape Maker，创建曲线简直易如反掌。只需选择任意点，然后单击 H 以启用控制手柄来创建曲线:

![Create Curve Flutter Shape Maker Example](img/f5e2bacc6436d3523db1d4c212e4cbe3.png)

如您所见，创建和修改曲线的简易性使这里变得完全不同。

## 在 Flutter 中构建复杂的 UI

要查看实际运行中的 Flutter Shape Maker，让我们快速构建一个相当复杂的票证 UI。最终结果将如下所示:

![Complex Ticket UI Finished Result](img/ab46a364a6415a0154687309624c835f.png)

首先，创建底层:

![Flutter Shape Maker Ticket Bottom Layer Visual](img/2de2fb3321387b591fff5370a3f4e841.png)

然后，创建上层:

![Flutter Shape Maker Upper Layer Visual](img/e0dd1215fb95d240c817f551b8f004cc.png)

通过使用图层对右侧重复相同的过程，并仔细管理带有控制手柄的曲线，您可以在很短的时间内实现复杂外观的 UI。

可以查看此票 UI 的[完整源代码](https://dartpad.dartlang.org/acf104ef2f460aae265b86613ddf34ae)。

## 结论

在本教程中，我们向您介绍了 Flutter 的 CustomPaint 小部件，并演示了如何使用 Flutter Shape Maker 创建和管理复杂的形状。然后，我们在一个示例 Flutter 应用程序中向您展示了如何使用这些工具创建相对复杂的 UI。

有了工具箱中的这些技能，你可以用 Flutter 创建的形状就只受你的想象力的限制了。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)