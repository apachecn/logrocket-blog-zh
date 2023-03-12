# 颤振建筑完全指南

> 原文：<https://blog.logrocket.com/complete-guide-flutter-architecture/>

Flutter 是 Google 的一个 UI 工具包，允许用户为 web、桌面和移动设备构建本地编译的应用程序。它也是从一个单一的代码库嵌入的，这意味着尽管 Flutter 包含不同的平台，但它仍然源于一个单一的代码库。

而且，因为它是一个 UI 工具包，它使用 UI 部件给用户创建 UI 的能力，而不需要在构建应用程序时以特定的方式创建它们；Flutter 以代码形式构建 ui。

在本文中，我们将看看 Flutter 架构及其组成。学习 Flutter 架构的好处之一是，它有助于构建应用程序，如更新状态、构建小部件或屏幕，或者使应用程序易于维护。

了解 Flutter 架构让我们熟悉这些构建架构的核心概念，并有助于快速编译和代码修改过程。

## Flutter 架构中的层

就像任何其他本地应用程序一样，Flutter 打包了三层:

*   嵌入器(最低层)
*   发动机
*   框架(最高层)

![Flutter Layers](img/bfd3a15474147132dd99d663e795b256.png)

[Flutter layers](https://docs.flutter.dev/resources/architectural-overview).

我们将在接下来的小节中深入讨论每一个问题。

### 嵌入层

入口点由特定于平台的嵌入程序提供，它与底层操作系统协作来访问诸如可访问性、呈现表面和输入等服务。

嵌入器是用特定于平台的语言编写的，比如 Android 用 Java 和 C++，iOS 和 macOS 用 Objective-C/Objective-C++，Windows 和 Linux 用 C++。

Flutter 代码可以作为一个模块嵌入到现有的应用程序中，或者使用嵌入器作为整个应用程序的内容。

### 引擎层

引擎层是用 C/C++编写的，它负责输入、输出、网络请求，并在需要绘制帧时处理渲染的困难转换。

Flutter 使用`Skia`作为它的渲染引擎，并通过 `[dart : ui](https://github.com/flutter/engine/tree/master/lib/ui%22)`显示给 Flutter 框架，它将主要的 C++代码包装在 Dart 类中。

### 框架层

框架层是大多数开发者可以和 Flutter 交互的部分。Flutter 框架提供了一个用 Dart 编写的反应式现代框架。

在框架层中，它包括以下内容:

*   翻译
*   小工具
*   材料和 cupertino

它还有基础类和构建块服务，如动画、绘图和手势，这些都是编写 Flutter 应用程序所必需的。

我们将在接下来的小节中更深入地研究所有这些方面。

首先要注意的是，在 Flutter 中，[一切都是 widget](https://blog.logrocket.com/widgets-the-building-blocks-of-flutter-apps/) 。小部件只是一个放在代码中的指令，它们是 Flutter 应用程序 UI 的基本构件。小部件指示它们当前的配置和状态应该如何出现在它们的显示屏上。

当小部件的状态发生变化时，它会重新构建自己的描述，框架会将它与之前的描述进行比较，以查看底层呈现树在从一种状态过渡到下一种状态时发生了什么变化。

小部件可以是按钮、图像、图标或布局的形式，将小部件放在一起会创建一个小部件树。

小部件树是一个永无止境的父部件和子部件链，它在屏幕上创建可视布局。

布局是通过在父子层次结构中将小部件嵌套在一起而创建的。父子窗口小部件树一开始可能看起来很吓人，但是通过练习，您将能够掌握它。

例如，假设有一个应用程序，其登录用户界面如下图所示:

![Example Of Login Page](img/eff53754b608affdbcf793afd3c72d53.png)

该登录屏幕的小部件树如下图所示:

![Widget Tree For Login Screen](img/fe806479ce510f92af09c888a5330ab5.png)

`root`小部件是主小部件。我们也可以称它为树中所有其他小部件的父部件。然后我们有了`material`小部件，这是我们应用程序的主题，接着是`scaffold`，这是我们应用程序的主体。

这里，子部件是以`container`部件的形式出现的，而`column`部件是子部件。`column`小部件的子部件包括`image`、`textfield`、`raisedbutton`、`container`和`column`。

下面是如何在我们的代码中从`scaffold`开始构造它:

```
scaffold(
   body: Container(
    child: Column(
       children: [
              Image(),
              TextField(),
              TextField(),
              Container(
                  child: Column(
                   children: [
                     RaisedButton(),
                     RaisedButton(),
                  ]
                    ),
                  ),

               RaisedButton()
            ]
          ),
        ),
);

```

在 Flutter 中，小部件可以分为不同的类别，如布局小部件、特定于平台的小部件、独立于平台的小部件和状态维护。

将小部件放在一起的过程称为合成。这意味着我们可以组合一堆简单的界面小部件，每个小部件处理一个特定的任务，产生强大的效果。

### 手势

得益于一个名为 `[GestureDetector](https://github.com/flutter/engine/tree/master/lib/ui)`的[独特小部件，与 Flutter 小部件的交互成为可能。`GestureDetector`是一个不可见的小部件，可以记录用户与其子部件的事件，如点击和拖动。](https://blog.logrocket.com/handling-gestures-flutter-gesturedetector/)

使用`GestureDetector`小部件，Flutter 为所有类型的手势提供了出色的支持，我们可以在现有的小部件中包含一个交互功能。让我们创建一个简单的应用程序，看看它是如何使用`ontap()`事件工作的。

```
         import 'package:flutter/material.dart';  

void main() => runApp(MyApp());  

class MyApp extends StatelessWidget {  
  // This widget is the root of your application.  
  @override  
  Widget build(BuildContext context) {  
    return MaterialApp(  
      title: 'Flutter Demo Application', theme: ThemeData(  
      primarySwatch: Colors.blue,),  
      home: MyHomePage(),  
    );  
  }  
}  
class MyHomePage extends StatefulWidget {  
  @override  
  MyHomePageState createState() => new MyHomePageState();  
}  

class MyHomePageState extends State<MyHomePage> {  
  @override  
  Widget build(BuildContext context) {  
    return new Scaffold(  
      appBar: new AppBar(  
        title: new Text('gesture'),  
        centerTitle: true,  
      ),  
      body: new Center(child: GestureDetector(  
          onTap: () {  
            print('Box Clicked');  
          },  
          child: Container(  
            height: 60.0,  
            width: 120.0,  
            padding: EdgeInsets.all(10.0),  
            decoration: BoxDecoration(  
              color: Colors.blueGrey,  
              borderRadius: BorderRadius.circular(15.0),  
            ),  
            child: Center(child: Text('Click Me')),  
          )  
      )),  
    );  
  }  
}  

```

这将输出以下屏幕:点击按钮时，输出将显示在控制台上:

![Output Displayed On Console](img/3c8cea669414c9562af69dfa96a7c9ed.png)

状态是应用程序在任何给定时刻的行为。可以把它看作是小部件首次创建时的信息，以及它如何定义该小部件的属性。但是在小部件的生命周期中，这些信息可能会改变。

为了在 Flutter 中构建 UI，我们使用了两种类型的小部件:

*   无状态小部件
*   有状态小部件

无状态小部件是静态的，这意味着一旦它们初始化，就不会改变。无状态小部件不保留任何实时信息，没有状态要管理，也没有与应用程序的直接交互。`Icons`、`IconButton`和`Text`是无状态窗口小部件的例子。

另外，请注意，无状态小部件会覆盖`build()`并返回一个小部件。当 UI 依赖于对象本身的信息时，我们使用无状态小部件:

```
import 'package:flutter/material.dart';

void main() => runApp(OurApp());

class OurApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container();
  }
}

```

在这里，这个无状态小部件的名称是`OurApp`。一个构建函数被覆盖，并将 BuildContext 作为参数，返回一个小部件。这就是为什么我们可以看到 build 方法的返回类型是一个 widget。

这是你可以设计屏幕 UI 的地方，是无状态的。

有状态小部件是动态的，这意味着它们可以根据情况而变化。这些能够存储实时数据，我们可以使用这些信息来更新用户界面。`TextField`、`Slider`和`Form`都是有状态小部件的例子。

`createState`方法被一个有状态的小部件覆盖，它返回一个状态。当 UI 可以动态改变时，我们使用有状态的小部件。

下面是有状态小部件的结构:

```
import 'package:flutter/material.dart';

void main() => runApp(OurApp());

class OurApp extends StatefulWidget {
  @override
  _OurAppState createState() => _OurAppState();
}

class _OurAppState extends State<OurApp> {
  @override
  Widget build(BuildContext context) {
    return Container();
  }
}

```

小部件的名称仍然是`OurApp`，但是它现在覆盖了`createState`方法而不是`build`方法，后者返回了`_OurAppState`类的一个实例。这个类从`State<>`扩展而来，它将`OurApp`作为模板输入。

`_OurAppState`方法现在覆盖了`build`函数并返回一个小部件。这是我们可以定义应用程序 UI 的地方，它是有状态的。因为它是一个有状态的小部件，所以您可以调用`build`方法任意次，这将在屏幕上重新绘制小部件。

当小部件的状态改变时，状态对象调用`setState()`，告诉框架重新绘制小部件。

## 状态管理

[管理应用程序的状态](https://blog.logrocket.com/flutter-state-management-methods/)是最重要和必要的过程之一，因为它帮助开发人员集中各种 UI 控件的所有状态，以管理整个应用程序中的数据流。

我们还可以看到状态是如何在系统中管理和传递的，以及状态管理如何控制一个或多个 UI 控件的状态，如文本字段、单选按钮、复选框、下拉列表、切换和表单。

颤振有两种状态:

*   短暂/局部状态
*   应用状态

![Two Kinds Of State In Flutter](img/8eb941d0de7bedd994667c15ef400c5b.png)

[States in Flutter](https://docs.flutter.dev/development/data-and-backend/state-mgmt/ephemeral-vs-app).

### 短暂的状态

短暂状态表示单个小部件的本地状态。它可以是`PageView`中的当前页面、当前动画进度或 UI 的任何其他当前本地状态。

没有必要对这种状态应用任何状态管理策略，因为它是可管理的，并且不会过于复杂。它真的很简单，很基本；然而，正因为如此，它对于大型程序并不理想，并且使状态维护变得复杂。

### 应用状态

应用程序状态是由应用程序的多个部分共享的状态，在用户会话期间使用。应用状态包括用户的登录信息、社交网络或电子商务应用中的通知以及用户偏好等信息。

您必须选择一种状态管理方法来处理应用程序的状态。虽然实现应用程序状态的方法有很多，但是应用程序的复杂性和本质对选择有很大的影响。

一种方法是使用`inheritedWidget`。当一个应用变得更大，窗口小部件树变得更复杂时，`inheritedWidget`可以帮上忙。

`InheritedWidget`是一个基础类，允许扩展它的类有效地沿着树向下传播信息。本质上，它通过向注册的构建上下文通知任何更改来运行。其他替代方法包括:

## Flutter 的渲染过程

Flutter 的渲染过程基本上就是把小部件变成像素。这是 Flutter 多重渲染管道的工作。Flutter 处理输入，运行动画，构建部件树，布局渲染对象，绘制渲染对象，并将所有内容编译到一个图像中。

每当部件树的一部分接收到用户输入、动画或任何其他更改时，Flutter 就会进入构建阶段。在这个阶段，框架通过调用必要的构建方法来重建小部件树。

`build()`阶段更新元素树，元素树更新渲染对象树。渲染对象负责布局和绘画。在布局阶段，Flutter 遍历向下传递约束的渲染对象树。

一旦布局阶段完成，就进入绘制阶段。这是 Flutter 在所有渲染对象上调用`paint`方法的地方:

```
@overide
void paint(PaintingContext context, Offset offset) {
final paint =ui.paint()..color = const Color(0xff0000ff);
final rect = Rect.fromLTWH(0, 0, size.width, size.height);
context.canvas.drawReact(rect, paint);

}

```

使用`drawRect`和`drawLine`等方法在画布下绘制渲染对象。绘画完成后，进入合成阶段。这意味着将图像组合成一张图片。

然后，引擎接收包含绘图指令的布局树，并将它们转换为像素。这是一个叫做`Rasterization`的过程。

![Layout Tree Converted To Pixels Through Rasterization](img/4253bc23bc069916f6ccbe7505de721e.png)

[Rendering through layout, paint, and composition](https://docs.flutter.dev/resources/architectural-overview).

## Flutter 如何与其他代码集成？

无论您是将本机控件嵌入到 Flutter 项目中，还是将 Flutter 嵌入到现有的应用程序中，Flutter 都提供了广泛的互操作性技术或协议。

通过平台通道，Flutter 允许您将自定义代码集成到移动和桌面应用程序中，并为您的主机应用程序的平台特定代码和 Dart 代码之间的通信提供了一种简单的技术。

通过建立一个公共通道，可以在用 Swift、Kotlin 和 Dart 等语言构建的平台组件之间发送和接收消息。数据从 Dart 类型序列化为标准格式，然后反序列化为等效的 Kotlin 或 Swift 表示。

## 使用颤振的好处

### 更快的编码

通常，iOS 和 Android 开发人员必须创建代码，等待代码编译并加载到设备上，然后才能看到变化。然而，由于 Flutter 的热重装，他们可以立即或毫不延迟地测试影响。

这意味着您的开发团队可以快速调整并看到结果。这正是为什么 Flutter 应用程序开发比其他人花费更少时间的原因。

### Flutter 的原生渲染引擎

本地渲染引擎允许您创建在所有平台上都相同的 ui。通常，呈现过程会在平台小部件的属性显示层和框架的小部件数据同步方面产生进一步的挑战。

而且，Flutter 正在尽一切努力促进这一进程。因为它使用 Skia，所以它不需要特定的 UI 组件来呈现，只需要一个画布来绘画。使用 Flutter 是多么大的优势啊！

### 一个适用于所有平台的代码库

由于 Flutter 的代码可重用性，您可以开发一个单一的代码库，不仅可以用于移动 Android 和 iOS，还可以用于 web、桌面和其他应用程序。

同样值得注意的是，Flutter 刚刚[发布了一个关于 Windows 支持](https://www.youtube.com/watch?v=g-0B_Vfc9qM)的重大更新，大大减少了开发时间，消除了成本，并允许您更快地部署您的产品。

### 出色的表现

Flutter 应用程序的性能与原生应用程序不相上下，[正在超越其他跨平台技术](https://surf.dev/flutter-vs-native/#:~:text=the%20figure%20below.-,Flutter%20vs%20native%20app%3A%20Speed%20of%20development,reduces%20development%20time%20and%20cost.&text=According%20to%20Surf%20experts%2C%20Flutter,creation%20of%20two%20native%20apps)。

这主要是因为 Flutter 是唯一一个不依赖于桥梁(JavaScript 或 web view)在应用程序和平台之间进行通信的移动 SDK。因此，您可以获得一个响应速度更快、动画更吸引人、性能问题更少的应用程序。

### 缩短上市时间

由于 Flutter 提供的所有功能使开发变得更加容易，您可以更快地发布您的应用程序，同时将新功能和改进发送到 iOS 和 Android。

作为一个开源平台，Flutter 可以免费使用，并且有一个不断增长的社区来贡献其优秀的文档，并帮助开发人员解决他们可能遇到的问题。

还有许多 YouTube 视频可供有兴趣学习 Flutter 或在 Google 的移动 UI 框架中提高技能的人访问。

## 结论

我们的应用程序的结构和我们的数据的组织方式可以在一个伟大的和普通的用户体验之间产生差异。在 Flutter 中构建移动应用程序时，理解所有的核心概念非常有帮助。你能分享一下这是否有帮助吗？

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)