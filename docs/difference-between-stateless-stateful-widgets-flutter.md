# Flutter - LogRocket 博客中无状态和有状态窗口小部件的区别

> 原文：<https://blog.logrocket.com/difference-between-stateless-stateful-widgets-flutter/>

[Favour Vivian Woka Follow](https://blog.logrocket.com/author/favourvivianwoka/) I'm a frontend developer based in Port Harcourt, Nigeria. I am passionate about building applications that run on the web. Technical writing is another interest of mine.

# Flutter 中无状态和有状态窗口小部件的区别

## 

2021 年 7 月 1 日 2 分钟阅读 747

## 介绍

要在 Flutter 中构建任何应用程序，我们必须创建一个 widget 类，它是 Flutter 应用程序的构建块。Flutter 使用 widgets 创建现代移动应用。

Flutter 中的小部件分为两种类型:无状态小部件和有状态小部件。记住这一点，我们将看看 Flutter 中什么是无状态和有状态小部件，并解释它们的区别。

让我们从这个问题开始:一个 widget 在 Flutter 中是什么状态？

状态是指在构建时同步读取小部件类的信息，也就是说，当小部件显示在屏幕上时，如果信息在其生命周期内发生变化，它可能会发生变化。

Flutter 有几个内置的小部件，它们都分为有状态和无状态小部件。

## 无状态小部件

无状态小部件不能在 Flutter 应用程序运行时改变它的状态。这意味着当应用程序运行时，无状态小部件不能被重绘。因此，外观和属性在小部件的整个生命周期中保持不变。

当我们描述的 UI 部分不依赖于任何其他小部件时，无状态小部件会很有用。无状态小部件的例子有文本、图标、图标按钮和凸起按钮。

这是一个无状态小部件的例子:

```
class StatelessScreen extends StatelessWidget {
 @override
 Widget build(BuildContext context) {
   return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('LogRockets'),
          backgroundColor: Colors.blueGrey[600],
        ),
        backgroundColor: Colors.white,
        body: Container(),
      ),
    );
  }
}

```

我们可以看到，在代码中，无状态小部件的名称是`StatelessScreen`，它覆盖了`build`方法。`build`方法将`BuildContext`作为参数，并返回一个小部件。

当我们创建一个不需要一次又一次重画小部件的应用程序时，我们使用无状态小部件。例如，当我们在[创建一个`AppBar`](https://blog.logrocket.com/flutter-appbar-tutorial/) 时，一个无状态的小部件可以是不需要改变的脚手架或图标。

无状态小部件类只在初始化时调用一次。即使有外力作用，也不会更新。

每当初始化`StatelessScreen`小部件时，就会调用`build`方法。之后，小部件将被打印在屏幕上。

但是如果我们想在有动作时更新它，我们必须制作一个有状态的小部件。

## 有状态小部件

当 UI 的某个部分必须在运行时动态改变时，使用有状态小部件。当应用程序运行时，有状态窗口小部件可以多次重绘自己。

当我们描述的 UI 部分动态变化时，有状态窗口小部件非常有用。如果我们创建一个按钮小部件，它在用户每次点击按钮时自我更新，这就是一个有状态小部件。

这就是使用有状态小部件的方法:

```
class StatefulScreen extends StatefulWidget {
  @override
  _StatefulScreenState createState() => _StatefulScreenState();
}
class _StatefulScreenState extends State<StatefulScreen> {
  String title = 'Original title';
  @override
  Widget build(BuildContext context) {
    return Column(children: [
      Text(title),
      RaisedButton(
          child: Text('Click'),
          onPressed: () {
            setState(() {
              title = 'Changed title';
            });
          })
    ]);
  }
}

```

我们创建了一个文本字段和一个按钮小部件。

一旦我们调用这个小部件并按下按钮，我们就让文本字段的值自动改变。

在这种类型的应用中，我们可以通过实现`setState()`来实现。`setState()`是在有状态小部件类中调用的方法。该方法在每次被调用时改变有状态小部件的值。

### 无状态和有状态的区别

为了重述我们在上面的例子中所阐述的内容，下表描述了无状态和有状态小部件之间的区别:

| 无状态小部件 | 有状态小部件 |
| --- | --- |
| 仅在初始化时更新 | 动态变化 |
| 文本、图标和`RaisedButtons` | 复选框、单选按钮和滑块 |
| 没有一个`setState()`。它将被渲染一次，不会自我更新 | 有一个内部的`setState()`，如果输入数据改变可以重新渲染 |
| 静态小部件 | 动态小部件 |
| 除非发生外部事件，否则无法在运行时更新 | 可以在运行时基于用户操作或数据更改进行更新 |

注意，对于要创建的两个小部件，它们都需要`BuildContext`作为返回小部件的参数。

## 结论

我们已经讨论了有状态和无状态小部件之间的区别，以帮助您构建更好的 Flutter 应用程序。从示例中，我们了解了无状态和有状态小部件的功能，以及如何知道您的用例需要哪个类。

现在，你可以用小部件为不同的用例创建一个更好的 UI。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)