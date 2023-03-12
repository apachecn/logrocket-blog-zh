# 如何在 Flutter 中显示 SnackBar:示例教程

> 原文：<https://blog.logrocket.com/how-to-display-and-customize-a-snackbar-in-flutter/>

在本教程中，我们将向您介绍 Flutter SnackBar 类，这是在 Flutter 中实现材质设计的众多小部件之一。我们将回顾该小部件的一些最重要的特性，概述在 Flutter 应用程序中定制和显示 SnackBar 的步骤，并回顾一些真实世界的示例来展示 SnackBar 在实践中是如何工作的。

为了跟上进度，您应该对 [Flutter 应用程序开发](https://blog.logrocket.com/pros-and-cons-of-flutter-app-development/)有一个基本的了解，并且有一些使用 SDK 构建跨平台应用程序的经验。

事不宜迟，我们开始吧！

## 什么是 SnackBar 类？

SnackBar 是一个 Flutter 小工具，可以让你在应用程序中临时显示一条弹出消息。它通常出现在应用程序屏幕的底部。

例如，您可以使用 SnackBar 小部件让用户知道所选商品何时被添加到购物车或被删除，或者指示表单已发送或图像已成功上传。

## 在 Flutter 应用程序中实现 SnackBar 的注意事项

实施 SnackBar 时，您应该考虑以下特性和/或功能:

### 频率

这需要 SnackBar 在应用程序中显示多长时间。永远记住，零食条不应该分散终端用户对应用程序主要目标的注意力。

这就是为什么 SnackBar 通常放在应用程序屏幕底部的一个原因。SnackBar 在 Flutter 应用程序中显示的推荐持续时间是 4 到 10 秒，不会更长。

### 行动

尽管如前所述，在没有用户交互的情况下，SnackBar 可能会在一段时间后从应用程序的屏幕上消失，但实现某种交互式元素来伴随您的消息是一个很好的做法。

例如，您可以创建一个带有“解除”或“重试”等标签的动作按钮，并将其附加到 SnackBar 小部件。

### 信息量

在 Flutter 应用程序中，SnackBar 小部件最常见也是最重要的用例之一是传达关于应用程序进程的重要信息。举个简单的例子，当用户单击按钮提交表单、上传文件等时，SnackBar 可能会显示类似“Successful”的消息。

## 构建、显示和自定义 SnackBar

要开始构建、显示和设计 SnackBar，首先完成以下步骤:

1.  启动 Android Studio 或您选择的其他 IDE
2.  开始一个新的颤振项目
3.  选择 **Flutter 应用**并将项目命名为“snackbardemo”

注意:如果您在尝试编译和运行代码时遇到“空安全特性”错误，您可以通过将 Flutter 升级到[版本 2.12 或更高版本](https://flutter.dev/docs/development/tools/sdk/releases)来修复它。

要升级到 Flutter 的最新版本，请单击 Android Studio 中的 terminal 选项卡并运行以下命令:

```
flutter upgrade

```

接下来，清除除了`void main() => runApp(MyApp());`之外的代码，这是 Flutter 在创建新项目时生成的代码。我们这样做是因为我们想从头开始。

现在，您的编辑器中应该有这样的内容:

```
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

```

现在，将`MyApp()`改为`const SnackBarDisplay()`:

```
void main() => runApp(SnackBarDisplay());

```

`void main()`是灾难援助反应队项目的主要切入点。Flutter 使用`runApp()`函数或方法作为 app 的根小部件。

这里，我们正在创建一个小部件，我们将其命名为`SnackBarDisplay`。这将是我们的应用程序的根。

在`void man()`后输入下面的代码。`SnackBarDisplay`扩展了`StatelessWidget`，使`SnackBarDisplay`成为一个小部件。

```
class SnackBarDisplay extends StatelessWidget {
  const SnackBarDisplay({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Displaying a SnackBar',
      home: Scaffold(
        backgroundColor: (Colors.grey),
        appBar: AppBar(
          title: const Text('Displaying a SnackBar'),
          backgroundColor: Colors.black12,
        ),
        body: const SnackBarPage(),
      ),
    );
  }
}

```

我们正在利用[材料设计](https://material.io/design)库来创建应用程序的结构。Material 是谷歌拥有的一个开源设计系统，用于定制 Android、Flutter、iOS 和 web 应用。如前所述， [Flutter 拥有种类繁多的材质 widgets](https://blog.logrocket.com/new-material-buttons-in-flutter/) 。

`Scaffold`小部件提供了默认的应用程序栏、一个标题和一个 body 属性，其中包含了主屏幕`home: Scaffold()`的小部件树。显示 SnackBar 需要使用`Scaffold`小部件。

`backgroudColor: (Colors.grey)`用于改变 Flutter app 的默认背景。

接下来，使用下面的代码创建 SnackBar 部分。在这里，当用户单击按钮时，将显示 SnackBar:

```
class SnackBarPage extends StatelessWidget {
  const SnackBarPage({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Center(
      child: ElevatedButton(
        onPressed: () {
          final snackBar = SnackBar(
            content: const Text('Hi, I am a SnackBar!'),
            backgroundColor: (Colors.black12),
            action: SnackBarAction(
              label: 'dismiss',
              onPressed: () {
              },
            ),
          );
          ScaffoldMessenger.of(context).showSnackBar(snackBar);
        },
        child: const Text(
          'Click to Display a SnackBar',
        ),
      ),
    );
  }
}

```

在上面的代码中，我们有 build 小部件— `Widget build(BuildContext context)` —这里没有任何价值的是`BuildContext`参数，它指定小部件在哪里构建。

在这个例子中，我们正在构建一个自定义的小部件按钮`ElevatedButton`，它将位于应用程序屏幕的中央。`ElevatedButton`是放置在`return Center`上的子部件。

然后，我们使用`ScaffoldMessenger`类来显示 SnackBar。

最后，运行程序。您应该会看到类似这样的内容:

![Click to Display SnackBar](img/7a5c02b7d68dd1a13ee7a7e6c9131d2f.png)

单击该按钮将显示 SnackBar，并显示以下消息:“嗨，我是 SnackBar！”

![SnackBar Message on Bottom of Screen](img/ca6265f80af633c6a73c46a5f8fb98fe.png)

## 结论

我希望你觉得这篇教程很有见地。Flutter 使得跨移动平台和网络构建应用变得容易。Flutter 的核心是 [widgets](https://blog.logrocket.com/widgets-the-building-blocks-of-flutter-apps/) 。SnackBar 小部件可以轻松地与您的用户分享重要的信息，帮助他们充分利用您的 Flutter 应用程序。

你可以通过浏览我们收集的 [Flutter 文章和教程](https://blog.logrocket.com/tag/flutter/)来了解更多关于 Flutter 的知识。

编码快乐！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)