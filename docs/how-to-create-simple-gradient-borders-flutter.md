# 如何在 Flutter 中创建简单的渐变边框

> 原文：<https://blog.logrocket.com/how-to-create-simple-gradient-borders-flutter/>

如今，组织努力在他们的移动应用程序中为最终用户提供可展示的用户界面——一种出色的视觉体验将用户吸引回来。

当谈到轻松创建漂亮的 UI 时，Flutter 受到了欢迎，因为它基本上是一个 UI 工具包，可以从单个代码库构建漂亮的本机编译的多平台应用程序。

视觉体验可以通过多种方式增强，包括微妙的亮点，如颜色、元素周围的亮点和边框装饰。边框——尤其是围绕 UI 组件的边框——可以为最终用户提供直观性以及层次和结构。

边框的形状和大小各不相同，从单色的普通边框到渐变的边框，这需要在 Flutter 中使用不同的方法。

在这篇文章中，你将学习如何在 [Flutter](https://blog.logrocket.com/complete-guide-flutter-architecture/) 中创建边框。不仅仅是一个典型的默认边框，而是更有趣的让小部件和容器脱颖而出的边框。除了基础知识，你还将学习如何绘制渐变边框。

让我们开始吧。

## 使用`BoxDecoration`了解基础知识

是一个不可变的类，它允许你通过指定不同的属性来绘制一个盒子。当构建诸如`Container`的元素时，它可以作为装饰属性被提供。顾名思义，用来装饰使用它的`Widget`。

支持的装饰包括颜色、渐变、背景图像、边框和阴影。

### 边框基础

您可以通过将`border`属性传递给`BoxDecoration`类来添加边框。有多种方法可以创建此实例，例如:

*   `Border()`构造器
*   `Border.all`工厂模式
*   `Border.merge`静态法

在`Container`上指定的边界绘制在所有东西的顶部，包括；`color`、`gradient`和`image`。

## 使用简单边框

让我们从在小部件周围绘制单色边框开始，如下所示:

```
// dart
Container(
  padding: const EdgeInsets.all(10),
  decoration: BoxDecoration(
    border: Border.all(color: Colors.green[400], width: 3)
  ),
  child: Text("I am inside a border.", style: TextStyle(fontSize: 24),),
);

```

在本例中，`Text`小部件由一个容器包装，并使用`BoxDecoration`保存诸如边框宽度和颜色等属性。或者，也可以使用`Border`构造函数代替`Border.all(...)`为每一边提供单独的边界属性。

### 圆形边框

您可以使用`BoxDecoration`中的`borderRadius`属性来更改边框半径。

```
// dart
Container(
    width: 100,
    height:100,
    decoration:BoxDecoration(
        color: Colors.blue,                             // Color of the container
        borderRadius: BorderRadius.circular(20.0),      // Radius of the border
        border: Border.all(
            width: 8.0,
            color: Colors.black,                        // Color of the border
        )
    )
)

```

### `TextField`的边框

具有特定边框属性(如`color`和`width`)的`UnderlineInputBorder`类的实例作为参数提供给`InputDecoration`类，以便在`TextField`周围绘制边框。

当`TextField`处于焦点时——比如当用户试图在字段中键入文本时，边框会变为活动状态。一旦`TextField`失去焦点，边框就会消失。

```
// dart
Container(
    child: TextField(
        decoration: InputDecoration(
            enabledBorder: UnderlineInputBorder(
                borderSide: BorderSide(
                    color: Colors.blue,
                    width: 2.0
                ),
            ),
            focusedBorder: InputBorder.none,
            disabledBorder: InputBorder.none,
            hintText: "Enter your name"
        ),
    )
)

```

### 描边边框

考虑文本周围的边框——它实际上是包围每个字母本身而不是外部`Container`的笔画。

```
// dart
Stack(
  children: [
    Text(
      'I am inside a border.',
      style: TextStyle(
        fontSize: 24,
        foreground: Paint()
          ..style = PaintingStyle.stroke
          ..strokeWidth = 3
          ..color = Colors.green[400],
      ),
    ),
    Text(
      'I am inside a border.',
      style: TextStyle(
        fontSize: 24,
        color: Colors.blue,
      ),
    ),
  ]
);

```

如果只使用堆栈中的第一个`Text` widget，只会增加一笔；您需要选择一个显示边框并填充两个部件的`Stack`部件。将两个字体大小不同的`Text`小部件堆叠在一起是行不通的，因为文本是从中间开始缩放的。

## 使用渐变边框

您已经学习了如何实现相对基本的边界。现在，让我们在`Widget`周围实现一个渐变边框，而不是简单的单色调边框。

用渐变装饰边框时，需要两个属性:

*   `Gradient`，包含颜色等必要信息。
*   `width`边框笔画

为此，您需要实现一个`CustomPainter`类的实例，如下面的代码所示:

```
// dart
class CustomGradient extends CustomPainter {
  CustomGradient({this.gradient, this.sWidth});

  final Gradient gradient;
  final double sWidth;
  final Paint p = Paint();

  @override
  void paint(Canvas canvas, Size size) {
    Rect innerRect = Rect.fromLTRB(sWidth, sWidth, size.width - sWidth, size.height - sWidth);
    Rect outerRect = Offset.zero & size;

    p.shader = gradient.createShader(outerRect);
    Path borderPath = _calculateBorderPath(outerRect, innerRect);
    canvas.drawPath(borderPath, p);
  }

  Path _calculateBorderPath(Rect outerRect, Rect innerRect) {
    Path outerRectPath = Path()..addRect(outerRect);
    Path innerRectPath = Path()..addRect(innerRect);
    return Path.combine(PathOperation.difference, outerRectPath, innerRectPath);
  }

  @override
  bool shouldRepaint(CustomPainter oldDelegate) => true;
}

```

这里，我们添加了两个矩形，而不是绘制边框的四个单独的边，如示例所示，其中一个与`Widget`的大小相同，而另一个矩形的笔画宽度从其原始大小中减去。`PathOperation.difference`计算两个矩形之间的差值，得到的差值作为较小矩形周围的笔画。

为了让渐变生效，我们需要给绘制对象`p`添加一个着色器。在上面的例子中，`createShader`是接受`outerRect`的对象作为参数的方法，使渐变到达边缘。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

现在，为了使用我们的自定义类`CustomGradient`，我们需要一个包含子部件的封装`Widget`，并在其周围绘制渐变。看看这个例子:

```
class CustomGradientContainer extends StatelessWidget {
  CustomGradientContainer({
    @required gradient,
    @required this.child,
    this.strokeWidth = 3,
  }) : this.painter = CustomGradient(
      gradient: gradient, sWidth: strokeWidth
  );

  final CustomGradient painter;
  final Widget child;
  final VoidCallback onPressed;
  final double strokeWidth;

  @override
  Widget build(BuildContext context) {
    return CustomPaint(
        painter: painter, 
        child: child
    );
  }
}

```

这将在文本周围绘制一个渐变。然而，这种边界将紧紧围绕文本本身，这不利于一个像样的用户界面。为了改进我们的 UI，我们需要插入一些进一步的定制，比如圆角和填充。

看看下面的例子，看看如何给`CustomGradient`类提供一个圆角边框:

```
// dart
class CustomGradient extends CustomPainter {

    ...

    final Gradient gradient;
    final double sWidth;
    final Paint p = Paint();   // Existing declarations

    final double bRadius;      // Declare border radius 

    ...

    @override
    void paint(Canvas canvas, Size size) {

        ...                     // Existing rectangle declarations

        RRect innerRoundedRect = RRect.fromRectAndRadius(innerRect, Radius.circular(bRadius));
        RRect outerRoundedRect = RRect.fromRectAndRadius(outerRect, Radius.circular(bRadius));

        ...                     // Existing PaintObject declarations

        Path borderPath = _calculateBorderPath(outerRoundedRect, innerRoundedRect);

    }

    Path _calculateBorderPath(RRect outerRect, RRect innerRect) {               // Update parameters
        Path outerRectPath = Path()..addRRect(outerRect);
        Path innerRectPath = Path()..addRRect(innerRect);
        return Path.combine(PathOperation.difference, outerRectPath, innerRectPath);
    }

    ...

}

```

一旦`CustomGradient`类被更新来指定边界半径属性，您可以修改`CustomGradientContainer`来绘制边界半径，如下所示:

```
// dart
class CustomGradientContainer extends StatelessWidget {
  CustomGradientContainer({
    @required gradient,
    @required this.child,
    @required this.onPressed,
    this.strokeWidth = 3,
    this.bRadius = 36,                                                    // Add border radius declaration                      
    this.padding = 12                                                     // Add padding declaration
    }) : this.painter = GradientPainter(
      gradient: gradient, strokeWidth: sWidth, borderRadius: bRadius      // Add border radius declaration
  );

  final GradientPainter painter;
  final Widget child;
  final VoidCallback onPressed;
  final double sWidth;
  final double bRadius;                                                     // Add border radius
  final double padding;                                                     // Add padding

  @override
  Widget build(BuildContext context) {
    return CustomPaint(
        painter: painter,
        child: Container(                                                   // Add container declaration
          padding: EdgeInsets.all(padding),                                 // Add padding declaration
          child: child
        )
    );
  }
}

```

下面是我们所做的:修改了`GradientBorderContainer`小部件的构造函数，以声明边界、半径和填充。然后我们声明了填充的值，用各自的填充将一个`Container`包在孩子`Widget`的周围。

## 结论

使用一个`CustomPainter`类在一个`Widget`周围创建一个边框为 Flutter 中的装饰提供了更多的灵活性。

除了基础知识，我们还看了如何在`Widget`周围创建一个变化的渐变边框。您可以更改渐变、填充、描边宽度以及包围边框的轮廓宽度。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)