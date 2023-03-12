# Flutter Slider widgets:示例深度剖析

> 原文：<https://blog.logrocket.com/flutter-slider-widgets-deep-dive-with-examples/>

Slider 是基本的 Flutter 小部件之一，可以通过移动滑块从一系列值中进行选择。在 Flutter 中有几种不同类型的滑块小部件，在 Flutter 框架中最常用的有:

*   [滑块](https://api.flutter.dev/flutter/material/Slider-class.html)–一个材质设计组件，允许您从一系列值中选择一个值(有一个滑块)
*   [CupertinoSlider](https://api.flutter.dev/flutter/cupertino/CupertinoSlider-class.html)–类似于 Slider，但遵循 Cupertino 的设计风格
*   [range slider](https://api.flutter.dev/flutter/material/RangeSlider-class.html)–用于从数值范围中选择一个范围(使用两个滑块)

您可以观看此视频，快速了解各种滑块部件:

[https://www.youtube.com/embed/ufb4gIPDmEs](https://www.youtube.com/embed/ufb4gIPDmEs)

视频

在本文中，您将了解到:

让我们开始吧！

## 入门指南

我们将检查你可以应用到滑块的各种主题化选项，但是在最基本的形式中，它看起来像这样:

![The slider component in its most basic form](img/a620a384d77314ffcd81bb48041f646f.png)

实现这一点所需的代码如下:

```
Slider(
  min: 0.0,
  max: 100.0,
  value: _value,
  onChanged: (value) {
    setState(() {
      _value = value;
    });
  },
)

```

`_value`变量在 widget 类中初始化:

```
double _value = 20;

```

我在上面设置的属性是为了使用 Flutter 构建任何滑块而必须定义的最小属性，但是对于不同类型的滑块，您的属性可能会有所不同。让我们来看看这些属性:

*   `min`:用户向左拖动滑块可以选择的最小值(滑块越向左数值越小)
*   `max`:用户向右拖动滑块可以选择的最大值(滑块越向右数值越大)
*   `value`:滑块当前选择的值，用户可以通过拖动滑块来改变
*   `onChanged`:这是一个回调函数，当用户在音轨上向左或向右拖动滑块时调用，并返回滑块位置的当前值

在`onChanged`中，您必须使用`setState`将更新后的值赋给存储在类中的`_value`变量:

```
setState(() {
  _value = value;
});

```

这里，`setState`用于更新 UI，这样每次值更新时都会反映在 Slider 小部件上。请注意，`Slider`所在的[的父控件应该是`StatefulWidget`](https://blog.logrocket.com/difference-between-stateless-stateful-widgets-flutter/) ，否则将无法使用`setState`。

这款基本的 slider widget 采用了[材质设计](https://material.io/design)风格，适合[安卓设备](https://blog.logrocket.com/kotlin-vs-java-for-android-development/)，而 iOS 设备则倾向于遵循 Cupertino 风格。对于在 [iOS 平台](https://blog.logrocket.com/building-ios-apps-swiftui-tutorial/)上运行的应用，最好使用 [CupertinoSlider](https://api.flutter.dev/flutter/cupertino/CupertinoSlider-class.html) 。

只需用`CupertinoSlider`小部件替换`Slider`小部件，并保持属性不变，就可以实现 iOS 风格的滑块。

滑块将如下所示:

![The basic Cupertino slider](img/2045bb4357944feaf0292f9914de1e5b.png)

以下代码用于构建此滑块:

```
Container(
  width: double.maxFinite,
  child: CupertinoSlider(
    min: 0.0,
    max: 100.0,
    value: _value,
    onChanged: (value) {
      setState(() {
        _value = value;
      });
    },
  ),
)

```

默认情况下，Cupertino 滑块不会占据屏幕的整个宽度，所以如果你想将其扩展到屏幕的宽度，你必须用一个`Container`小部件将它包裹起来，并提供一个宽度`double.maxFinite`。

`Slider`和`CupertinoSlider`都只允许你从指定的范围中选择一个值，但是如果你想选择两个值，你可以使用`RangeSlider`小部件。

## RangeSlider 小工具

`RangeSlider`小部件遵循材质设计风格，它带有两个滑块，可以用来设置开始和结束值。在这个小部件中，没有`value`属性；相反，它带有一个`values`属性，其类型为 [RangeValues](https://api.flutter.dev/flutter/material/RangeValues-class.html) 。

一个基本的`RangeSlider`小部件如下所示:

![The basic RangeSlider widget](img/690837a715e980b164d204555832965b.png)

为此，您可以使用以下代码:

```
RangeSlider(
  min: 0.0,
  max: 100.0,
  values: RangeValues(_startValue, _endValue),
  onChanged: (values) {
    setState(() {
      _startValue = values.start;
      _endValue = values.end;
    });
  },
)

```

`RangeValues`取一对两个值作为输入:起始值(由`_startValue`提供)和结束值(由`_endValue`提供)。您可以在 widget 类中定义这两个变量，如下所示:

```
double _startValue = 20.0;
double _endValue = 90.0;

```

当您使用这些值运行应用程序时，滑块将根据这些值进行初始化。在范围滑块中，`onChanged`回调函数也返回`RangeValues`,您可以使用它来更新滑块的位置，如下所示:

```
setState(() {
  _startValue = values.start;
  _endValue = values.end;
});

```

## 自定义滑块颜色

我们上面讨论的所有三个 slider 小部件都带有一些属性，可以用来定制滑块的颜色。

基本的`Slider`小部件为您提供了设置颜色的三个属性:

*   `activeColor`:将颜色应用到滑块轨道的活动部分
*   `inactiveColor`:将颜色应用到滑块轨道的非活动部分
*   `thumbColor`:对滑块应用颜色

![The Slider widget's color properties](img/fa1486fb4a21da852104d691a6a4f571.png)

您可以使用以下代码实现这种滑块颜色组合:

```
Slider(
  min: 0.0,
  max: 100.0,
  activeColor: Colors.purple,
  inactiveColor: Colors.purple.shade100,
  thumbColor: Colors.pink,
  value: _value,
  onChanged: (value) {
    setState(() {
      _value = value;
    });
  },
)

```

同样，您可以更改这些属性并轻松自定义滑块颜色。以下是几个例子:

![Example custom sliders](img/ac0e53d4549803f82e8af1e7ffd41e8c.png)

如果您正在使用`CupertinoSlider`小部件，您可以自定义两个颜色属性:

以下是自定义 Cupertino 滑块的示例:

![Cupertino Slider with customized colors](img/e9f7ed2098a5f8148e7fe77274113b06.png)

您可以使用以下代码构建如上所示的定制 iOS 风格滑块:

```
Container(
  width: double.maxFinite,
  child: CupertinoSlider(
    min: 0.0,
    max: 100.0,
    value: _value,
    activeColor: CupertinoColors.activeGreen,
    thumbColor: CupertinoColors.systemPink,
    divisions: 10,
    onChanged: (value) {
      setState(() {
        _value = value;
      });
    },
  ),
)

```

`RangeSlider`小部件还允许你定制两个属性，尽管它们不同于 Cupertino 滑块所允许的属性:

*   `activeColor`
*   `inactiveColor`

以下是自定范围滑块的示例:

![A Range slider with customized colors](img/72dab4c71007c0ede4248d9826a05463.png)

可以使用下面的代码构建这个滑块:

```
RangeSlider(
  min: 0.0,
  max: 100.0,
  activeColor: widget.activeColor,
  inactiveColor: widget.inactiveColor,
  values: RangeValues(_startValue, _endValue),
  onChanged: (values) {
    setState(() {
      _startValue = values.start;
      _endValue = values.end;
    });
  },
)

```

在本文的后面，我们将讨论可以在滑块上应用的更复杂的定制和主题。

## 显示滑块刻度和标签

通常，slider 小部件返回十进制值，因为默认情况下它们是连续的。但是如果您只需要离散值(即没有任何小数位的整数)，您可以使用`divisions`属性。

`label`是一个常用于离散值的属性。当滑块处于活动状态时，它用于在滑块上方显示当前选定的离散值。

当设置了`divisions`和`label`属性时，基本的`Slider`小部件将如下所示:

![A basic Slider widget with the divisions and labels props applied](img/b98563f8b84e997afe4f8ab10e7f2276.png)

该滑块的代码如下:

```
Slider(
  min: 0.0,
  max: 100.0,
  value: _value,
  divisions: 10,
  label: '${_value.round()}',
  onChanged: (value) {
    setState(() {
      _value = value;
    });
  },
)

```

在`CupertinoSlider`小部件中，可以设置`divisions`属性，但是它不支持`label`。

`RangeSlider`小部件与`Slider`小部件具有相似的属性:`divisions`属性用于显示离散值，而`labels`属性可以使用，因为在两个滑块顶部有两个标签要显示。`labels`道具接受一个`RangeLabels`类型的变量。

应用分割和标签后，范围滑块如下所示:

![The basic RangeSlider with the divisions and labels props applied](img/162a9e1815a8614a5c78d498eb47ed0c.png)

该滑块的代码如下所示:

```
RangeSlider(
  min: 0.0,
  max: 100.0,
  divisions: 10,
  labels: RangeLabels(
    _startValue.round().toString(),
    _endValue.round().toString(),
  ),
  values: RangeValues(_startValue, _endValue),
  onChanged: (values) {
    setState(() {
      _startValue = values.start;
      _endValue = values.end;
    });
  },
)

```

## 显示滑块的状态

在某些情况下，您可能需要知道滑块的当前状态(是空闲、即将被拖动还是正在被拖动)，以便更新屏幕上显示的其他 UI 组件。不要担心——所有三种类型的滑块都有一些回调，可以帮助您确定滑块的当前状态。它们如下:

*   `onChanged`:当用户拖动滑块时被调用，其值更新
*   `onChangeStart`:当用户开始拖动滑块时被调用。该回调通常用于指示用户已经开始拖动，并可用于相应地更新任何 UI 组件
*   `onChangeEnd`:当用户停止拖动滑块时被调用。该回调用于指示用户已经停止拖动，并可用于相应地更新任何 UI 组件

在上面列出的三个回调中，只有`onChanged`应该用于更新`slider`值。

这里有一个简单的例子，使用这些回调来更新一个`Text`小部件:

![Slider with callbacks](img/4ad119e70a1ffa1de4f2e0ff0d4c118e.png)

用于构建它的代码如下:

```
Column(
  crossAxisAlignment: CrossAxisAlignment.start,
  children: [
    Slider(
      min: 0.0,
      max: 100.0,
      value: _value,
      divisions: 10,
      onChanged: (value) {
        setState(() {
          _value = value;
          _status = 'active (${_value.round()})';
          _statusColor = Colors.green;
        });
      },
      onChangeStart: (value) {
        setState(() {
          _status = 'start';
          _statusColor = Colors.lightGreen;
        });
      },
      onChangeEnd: (value) {
        setState(() {
          _status = 'end';
          _statusColor = Colors.red;
        });
      },
    ),
    Text(
      'Status: $_status',
      style: TextStyle(color: _statusColor),
    ),
  ],
)

```

一些变量在类内部初始化，如下所示:

```
double _value = 20;
String _status = 'idle';
Color _statusColor = Colors.amber;

```

这些变量值根据回调进行更新，而`Text`小部件由`setState`调用进行更新。

## 将主题应用到滑块

现在，我们正在进行一些更深入的定制，您可以将它们应用到滑块上。您可以通过用`SliderTheme`包装`Slider`小部件来解锁这些定制，这将允许您通过指定各种属性来定制滑块的每个方面。

让我们构建以下滑块:

![Our example slider with custom themes applied](img/10f605bd06d6e0127ba25901e2aeff53.png)

附带了大量的属性，但是我们用来构建上面的滑块的属性如下:

![The SliderTheme properties that can receive custom color styling](img/b60d490eb76c8f0544ef783f7cbea7fe.png)

*   `trackHeight`:指定整个轨道的高度，适用于轨道的非活动和活动部分
*   `trackShape`:指定轨道末端是否应该是圆形的，并应用于轨道的非活动和活动部分。使用`RoundedRectSliderTrackShape`给它漂亮的圆形边缘
*   `activeTrackColor`:指定应用于轨道活动部分的颜色；在上面的例子中，它是最左边的部分，从最小值开始直到滑块拇指位置
*   `inactiveTrackColor`:指定应用在轨道非活动部分的颜色；在上面的例子中，它是最右边的部分，从滑块位置开始直到最大值
*   `thumbShape`:指定要应用于滑块的形状。`RoundSliderThumbShape`给了它一个完全圆形的拇指，拇指的半径和它的按压高度也可以在这个道具里面设置
*   `thumbColor`:指定应用于滑块的颜色
*   `overlayColor`:指定当滑块处于按下状态时，在滑块旁边可见的覆盖层上应用的颜色；通常，这是一种接近拇指颜色但不透明度较低的颜色
*   `overlayShape`:指定要使用的覆盖的形状及其半径
*   `tickMarkShape`:指示轨道上的刻度，并指定用于指示滑块轨道上刻度线的形状。这些仅在滑块有刻度时可见
*   `activeTickMarkColor`:指定应用于轨道活动侧刻度线的颜色
*   `inactiveTickMarkColor`:指定应用于轨道非活动侧刻度线的颜色
*   `valueIndicatorShape`:指定数值指示器的形状，其内有标签(即数值文本)；当滑块处于按下状态时，这是可见的
*   `valueIndicatorColor`:指定数值指示器的颜色。通常，这里使用更接近滑块的颜色，但是您可以指定任何颜色
*   `valueIndicatorTextStyle`:指定用于显示数值指示器内滑块值的文本样式

应用该主题的完整代码如下:

```
SliderTheme(
  data: SliderTheme.of(context).copyWith(
    trackHeight: 10.0,
    trackShape: RoundedRectSliderTrackShape(),
    activeTrackColor: Colors.purple.shade800,
    inactiveTrackColor: Colors.purple.shade100,
    thumbShape: RoundSliderThumbShape(
      enabledThumbRadius: 14.0,
      pressedElevation: 8.0,
    ),
    thumbColor: Colors.pinkAccent,
    overlayColor: Colors.pink.withOpacity(0.2),
    overlayShape: RoundSliderOverlayShape(overlayRadius: 32.0),
    tickMarkShape: RoundSliderTickMarkShape(),
    activeTickMarkColor: Colors.pinkAccent,
    inactiveTickMarkColor: Colors.white,
    valueIndicatorShape: PaddleSliderValueIndicatorShape(),
    valueIndicatorColor: Colors.black,
    valueIndicatorTextStyle: TextStyle(
      color: Colors.white,
      fontSize: 20.0,
    ),
  ),
  child: Slider(
    min: 0.0,
    max: 100.0,
    value: _value,
    divisions: 10,
    label: '${_value.round()}',
    onChanged: (value) {
      setState(() {
        _value = value;
      });
    },
  ),
)

```

在`SliderTheme`中你可以使用更多的属性，尽管这种级别的定制对大多数用户来说已经足够了。但是你可以更进一步！

## 用`CustomPainter`设计自定义滑块

`SliderTheme`允许您仅从 Flutter 附带的现有设计中修改滑块组件形状。如果你想给你的滑块一个开箱即用的自定义外观， [`CustomPainter)`](https://api.flutter.dev/flutter/rendering/CustomPainter-class.html) 已经来救你了。

您可以为各种滑块组件创建自己的设计(如刻度标记、滑块、滑块轨道等。)并将它们作为形状分配给这些组件。

我们将为滑块创建一个自定义形状，并将其分配给我们的`Slider`小部件。它看起来会像这样:

![Our custom thumb shape on the Slider widget](img/cc9e7298bde65e507a6f91d7946fe21c.png)

为了创建这个多边形滑块，首先需要定义一个类来生成这个形状，它将从`SliderComponentShape`类扩展而来:

```
class PolygonSliderThumb extends SliderComponentShape {
  @override
  Size getPreferredSize(bool isEnabled, bool isDiscrete) {
    // Define size here
  }

  @override
  void paint(
    PaintingContext context,
    Offset center, {
    required Animation<double> activationAnimation,
    required Animation<double> enableAnimation,
    required bool isDiscrete,
    required TextPainter labelPainter,
    required RenderBox parentBox,
    required SliderThemeData sliderTheme,
    required TextDirection textDirection,
    required double value,
    required double textScaleFactor,
    required Size sizeWithOverflow,
  }) {
    // Define the slider thumb design here
  }
}

```

当从`SliderComponentShape`扩展类时，您必须定义两个方法覆盖:

1.  `getPreferredSize()`:这个方法应该返回滑块缩略图的大小
2.  `paint()`:该方法生成滑块拇指形状的设计

我们需要向`PolygonSliderThumb`类传递两个值，滑块的拇指半径值和当前选择的值:

```
class PolygonSliderThumb extends SliderComponentShape {
  final double thumbRadius;
  final double sliderValue;

  const PolygonSliderThumb({
    required this.thumbRadius,
    required this.sliderValue,
  });

  @override
  Size getPreferredSize(bool isEnabled, bool isDiscrete) {
    return Size.fromRadius(thumbRadius);
  }

  // ...
}

```

这里，我们使用了`thumbRadius`变量来定义滑块拇指形状的半径。

现在，让我们定义`paint()`方法内部的形状。它类似于我们在`CustomPainter`中使用的那个，具有相同的概念:

*   `canvas`:绘画区域，在这里你涂上颜料并创造出你想要的形状
*   `paint`:你用来画画的画笔

您可以从传递给`paint()`方法的`context`中访问`canvas`对象:

```
final Canvas canvas = context.canvas;

```

为多边形的边数、连接多边形的内圆和外圆的半径定义一些常数，最后根据这些常数计算角度:

```
int sides = 4;
double innerPolygonRadius = thumbRadius * 1.2;
double outerPolygonRadius = thumbRadius * 1.4;
double angle = (math.pi * 2) / sides;

```

绘画操作的顺序应该是这样的:

1.  外部路径
2.  内部路径
3.  值文本

第一个画在开头，第二个画在上面，接着是第三个。

要绘制外部路径，可以使用以下方法:

```
final outerPathColor = Paint()
  ..color = Colors.pink.shade800
  ..style = PaintingStyle.fill;

var outerPath = Path();

Offset startPoint2 = Offset(
  outerPolygonRadius * math.cos(0.0),
  outerPolygonRadius * math.sin(0.0),
);

outerPath.moveTo(
  startPoint2.dx + center.dx,
  startPoint2.dy + center.dy,
);

for (int i = 1; i <= sides; i++) {
  double x = outerPolygonRadius * math.cos(angle * i) + center.dx;
  double y = outerPolygonRadius * math.sin(angle * i) + center.dy;
  outerPath.lineTo(x, y);
}

outerPath.close();
canvas.drawPath(outerPath, outerPathColor);

```

内部路径可以画成这样:

```
final innerPathColor = Paint()
  ..color = sliderTheme.thumbColor ?? Colors.black
  ..style = PaintingStyle.fill;

var innerPath = Path();

Offset startPoint = Offset(
  innerPolygonRadius * math.cos(0.0),
  innerPolygonRadius * math.sin(0.0),
);

innerPath.moveTo(
  startPoint.dx + center.dx,
  startPoint.dy + center.dy,
);

for (int i = 1; i <= sides; i++) {
  double x = innerPolygonRadius * math.cos(angle * i) + center.dx;
  double y = innerPolygonRadius * math.sin(angle * i) + center.dy;
  innerPath.lineTo(x, y);
}

innerPath.close();
canvas.drawPath(innerPath, innerPathColor);

```

最后，值文本可以画成这样:

```
TextSpan span = new TextSpan(
  style: new TextStyle(
    fontSize: thumbRadius,
    fontWeight: FontWeight.w700,
    color: Colors.white,
  ),
  text: sliderValue.round().toString(),
);

TextPainter tp = new TextPainter(
  text: span,
  textAlign: TextAlign.center,
  textDirection: TextDirection.ltr,
);

tp.layout();

Offset textCenter = Offset(
  center.dx - (tp.width / 2),
  center.dy - (tp.height / 2),
);

tp.paint(canvas, textCenter);

```

您现在可以将这个自定义滑块形状应用到`SliderTheme`:

```
SliderTheme(
  data: SliderTheme.of(context).copyWith(
    thumbShape: PolygonSliderThumb(
      thumbRadius: 16.0,
      sliderValue: _value,
    ),
  ),
  child: Slider(...)
)

```

我们不会进入任何其他滑块组件的构建过程，但是您可以使用我们用于构建多边形滑块的这些概念来创建您自己的完全自定义的滑块。

## 一些其他惊人的滑块包

在 [pub.dev](https://pub.dev/) 上有很多有趣的包，它们提供了带有各种定制选项的预构建滑块。其中一些包括:

## 包扎

如果你能坚持到现在，你应该得到表扬！😉

本文涵盖了掌握 slider 小部件概念所需的所有知识。现在，是时候开始使用 Flutter 创建自己独特的滑块[了。](https://blog.logrocket.com/tag/flutter)

感谢您阅读文章！如果你对这篇文章或我的例子有任何建议或问题，请随时通过 [Twitter](https://twitter.com/sbis04) 或 [LinkedIn](https://www.linkedin.com/in/sbis04/) 与我联系。你可以在我的 [GitHub 库](https://github.com/sbis04/flutter_slider)上找到我们在本文中构建的所有滑块的代码示例。

## 用[log 火箭](https://lp.logrocket.com/blg/signup)突破传统错误报告的噪音

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)