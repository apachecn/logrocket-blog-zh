# 构建一个颤动的桌面应用程序:示例教程

> 原文：<https://blog.logrocket.com/building-flutter-desktop-app-tutorial-examples/>

***编者按**:这篇文章于 2022 年 8 月 15 日更新，以反映 Flutter 的最新版本，包括 2022 年 2 月宣布的 Windows update 的新 Flutter，以及如何在 Windows 或 macOS 上设置 Flutter 桌面应用程序。*

开发人员可以选择编写几种不同类型的应用程序:控制台应用程序、移动应用程序、web 应用程序和桌面应用程序。

桌面应用程序运行在具有各种 UI 元素的系统窗口中。如果你打算开发一个桌面应用程序，有多种不同的框架、UI 工具包和编程语言可供选择。

桌面应用程序开发 API 有两种类型:特定于平台的和跨平台的。特定于平台的 API 只支持一个目标操作系统。例如，Cocoa API 提供了为 macOS 平台开发桌面应用程序的接口。

另一方面，跨平台 API 允许开发人员用一个通用 API 为多个操作系统开发桌面应用程序。

如今，由于各种原因，跨平台开发框架很受欢迎。

例如，开发人员可以为多个平台维护单个代码库，因为跨平台框架的 API 为特定于平台的 API 提供了良好的抽象。

此外，跨平台开发框架支持快速特性交付。换句话说，不需要通过编写特定于平台的代码来为不同的操作系统实现相同的功能，因此可以高效地交付新功能。

在本文中，我将解释如何用 Flutter 创建、构建和发布原生的跨平台桌面应用程序。我们还将讨论 Flutter 受欢迎的原因。

我们将涵盖:

## 什么是颤振？

Flutter 是一个跨平台的应用程序开发框架，我们可以用它来为多种平台构建本地编译的应用程序:移动、桌面和 web。

已知支持的输出目标是 Android、iOS、HTML、HTML Canvas、Linux、Windows、macOS 和 Fushia。 [Flutter 使用 Dart 作为应用开发语言](https://blog.logrocket.com/dart-flutter-data-structures-comprehensive-guide/)。

Flutter 目前更侧重于移动应用程序开发，但也支持基于桌面的目标。

这个框架自带小部件工具包。当一个特定的 Flutter 应用程序启动时，Flutter 将通过 [Skia 图形库呈现该应用程序的 UI 控件。](https://skia.org/)

另外，截至 2022 年 2 月，Flutter 现已稳定用于 Windows！这意味着您可以开始使用 Flutter 创建生产应用程序。如果你已经有一个运行在 Android 或 iOS 上的 Flutter 应用程序，并且你正在考虑将这个应用程序带到 Windows 上，现在将是一个很好的时机。

## 为什么 Flutter 这么受欢迎？

在跨平台框架开发领域，最关键的问题是如何为所有平台放置抽象层。换句话说，如何为开发人员提供一个通用的 API，而不会给应用程序带来性能问题。

[电子等桌面应用开发框架](https://blog.logrocket.com/advanced-electron-js-architecture/)推出了用于渲染的网页浏览器。Apache Cordova 和 Ionic 移动应用程序开发框架以同样的方式解决了这个问题。

尽管这些框架为开发人员提供了简单的跨平台 API，但由于基于 web 浏览器的渲染，仍然存在严重的性能问题。

Flutter 通过 Dart 库提供了简单的跨平台 API，同时还保持了卓越的性能。

## 用 Flutter 开发桌面应用程序

在本教程中，我将展示如何用 Flutter 制作一个简单的跨平台桌面应用程序。

因为 Flutter 现在被认为对 Windows 和 Linux 是稳定的，我们将考虑如何在这些操作系统上开始，然后创建一个可以在这三种环境中工作的桌面应用程序。

### 在 Windows 上建立 Flutter 开发环境

在 Windows 上设置 Flutter 是一个非常简单的过程。你只需要[下载 Flutter app 包](https://docs.flutter.dev/get-started/install/windows)，在某个地方解压，然后添加到你系统的`PATH`变量中。为此:

*   打开开始菜单，输入“env”
*   从列表中选择“编辑系统环境变量”
*   单击“环境变量…”按钮
*   在“用于`username`的用户变量”部分，双击“路径”条目
*   将提取 Flutter 的路径添加到末尾带有一个`bin`后缀的地方
    *   如果你提取了颤振到`c:\flutter`，你应该添加`c:\flutter\bin`到你的路径

在这之后，您应该能够打开一个命令提示符并运行`flutter doctor`来检查您的配置是否全部完成。

### 在 Ubuntu 上建立颤振开发环境

在 Ubuntu 上设置 Flutter 是小菜一碟，因为这一切都是为了输入一些终端命令。

要安装 Flutter SDK，请使用以下命令:

```
$ sudo snap install flutter --classic

```

### 验证颤振环境

如果启用了桌面模式，`flutter devices`命令应该会列出您当前的操作系统。这张截图是在 Ubuntu 上，但是在 Windows 上你应该会看到类似的结果。

![Screenshot Of Flutter Devices Command On Ubuntu Showing Connected Device Info](img/d41209951aeb67c0dc211dd3efb026e2.png)

### 创建新的 Flutter 应用程序

像任何其他典型的 CLI 一样，我们可以使用`create`命令创建一个新的应用程序，如下所示:

```
$ flutter create desktop-app

```

上面的命令将为我们搭建一个简单的启动项目。starter 项目包含最少的代码和注释来帮助我们理解 Flutter 是如何工作的。

`lib/main.dart`文件包含应用程序的主要源代码。让我们修改如下所示的主要源代码，以创建一个简单的“Hello World”应用程序:

```
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'TextPad',
      theme: ThemeData(
        primarySwatch: Colors.green,
      ),
      home: Scaffold(
        body: Center(
          child: Text('Hello World')
        ),
      ),
    );
  }
}

```

你可以在 Github 上查看这个项目。

编辑主源文件后，输入以下命令运行您的应用程序:

```
$ flutter run -d <platform>
// <platform> = linux, windows, macos

```

上述命令将在原生窗口中打开 Flutter“Hello World”应用程序。本机窗口将根据当前操作系统和系统主题配置进行样式设置。

![Screenshot Of Flutter Hello World Desktop App On Debug Mode Displaying Hello World Text And Red Banner In Corner Labeled Debug](img/2cd3195cbe73e094be38037cf3afcf4a.png)

### 调试 Flutter 应用

Flutter 开发环境支持热重载特性。因此，我们可以在您输入`flutter run`命令的控制台中按下`r`键来触发热重装。当应用程序在调试模式下运行时，尝试更改文本小部件的内容。

### 实现跨平台桌面应用程序

让我们用 Flutter 实现一个小型的跨平台桌面应用程序。

我们将制作一个名为“TextPad”的小型文本编辑器应用程序，并学习如何为 Windows 构建它。TextPad 有一个大的多行文本字段，我们可以在其中键入笔记。它还有一个动作按钮，可以将当前便笺保存到文件中。

下面是 TextPad 的截图。

![Screenshot Of Sample Textpad App With Text Field Displaying Prompt To Play With Notes](img/de13a6b384365d5be9b0e8a7d7d88c2b.png)

如上所述，右上角的 save 按钮会将当前笔记写入一个文本文件。保存文件后，TextPad 将显示任务完成的通知消息。

![Screenshot Of Textpad App In Use With Cursor Hovering Over Save Icon And Tooltip Stating Export To Textpadnote.txt At Top Right, Text Written In Text Field, And Notification Message Confirming Saved File At Bottom](img/22dc5f218a186c7c35c864bfc7af23b5.png)

与前面的“Hello World”应用程序类似，将以下源代码添加到主应用程序源文件中:

```
import 'dart:io';
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {

    final textController = new TextEditingController();
    final globalKey = GlobalKey<ScaffoldState>();
    final String fileName = 'textPadNote.txt';

     Future<void> _exportToFile(BuildContext context) async {
       final File file = File('${Directory.current.absolute.path}/${fileName}');
       final snackBar = SnackBar(content: Text('Saved to: ${file.path}'));

       await file.writeAsString(textController.text);
       globalKey.currentState.showSnackBar(snackBar);
    }
  @override
  Widget build(BuildContext context) {

    return MaterialApp(
      title: 'TextPad',
      theme: ThemeData(
                brightness: Brightness.dark,
           ),
      home: Scaffold(
        key: globalKey,
        appBar: AppBar(
          title: Text('TextPad'),
          actions: <Widget>[
              IconButton(
                    icon: const Icon(Icons.save),
                    tooltip: 'Export to ${fileName}',
                    onPressed: () {_exportToFile(context);}
                  )]
        ),
        body: Center(
          child: TextField(
                controller: textController,
                maxLines: null,
                keyboardType: TextInputType.multiline,
                expands: true,
                  decoration: InputDecoration(
    hintText: 'Play with your notes here...',
    contentPadding: EdgeInsets.all(12.0)
  ),
          ),
        ),
      ),
    );
  }
}

```

### 使用 Flutter 在 Windows 上运行 TextPad

因为 Flutter 是一个跨平台的解决方案，所以我们可以在 Windows 计算机上运行这段代码。如果我们将包含代码的文件夹复制到 Windows 计算机上，然后运行`flutter run windows`，结果将会是这样:

![Screenshot Of Textpad App After Flutter Code Has Been Copied To A Windows Computer And Run Using Command Flutter Run Windows](img/399825c142452b64e5d3cba1fa942eb1.png)

因为我们在 Windows 上运行，所以让我们引入一些 Windows 功能，比如 Aero 主题。首先，我们需要引入`flutter_acrylic`包。我们可以通过打开项目所在的命令窗口，然后运行以下命令来实现这一点:

```
flutter pub add flutter_acrylic

```

接下来，我们需要用下面的代码替换 Flutter 应用程序中的`main`函数，这将初始化应用程序中的`flutter_acrylic`包。

```
Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  if (Platform.isWindows) { // only apply Aero theming on Windows.
    await Window.initialize();
    await Window.setEffect(
      effect: WindowEffect.aero,
      color: Colors.black.withOpacity(0.6),
    );
  }

  runApp(MyApp());
}

```

最后，我们需要将我们的`MaterialApp`和`Scaffold`的`color`属性设置为`Colors.transparent`。这是为了让我们可以“看穿”我们的应用程序的透明背景层。

这样做的结果是，我们的应用程序有一个非常好看的背景，它稍微模糊了应用程序后面的窗口，这导致了一种原生的感觉。

![Result Of Adding Aero Design Theme To Flutter Windows App To Lightly Blur Windows Behind Application For A Native Feel](img/6e08545e96143f1c13ff7e34acd57926.png)

在 Github 上查看这个项目。

### 用可移植的二进制文件构建你的 Flutter 桌面应用程序

在将您的应用程序发布到互联网之前，您可以构建 Flutter 应用程序来获得可移植的二进制文件。这些二进制文件也可以与用户或测试人员共享。此外，您可以用自己喜欢的安装工具制作可安装的二进制文件。

当我们调试应用程序时，将创建可调试的二进制文件。但是，可调试的二进制文件并没有针对发行版进行优化。在终端上执行以下命令以生成优化的二进制文件:

```
$ flutter build <platform>
// <platform> = linux, windows, macos 

```

### 如何发布您的应用程序

有不同的方法来发布你的 Flutter 应用程序，但是部署方法取决于操作系统的类型。

例如，如果您打算为 Linux 用户发布您的应用程序，您可以将您的 Flutter 应用程序部署到 [Snapcraft 软件商店](https://snapcraft.io/)。

如果您需要为 Windows 用户部署应用程序，可以借助第三方工具制作 Windows Installer 包(MSI)或新的 Windows 应用程序包(MSIX)。关于如何实现这一点的更多细节可以在 pub.dev 上看到。

现在，用于 Windows、Linux 和 macOS 的 Flutter 被认为是稳定的，因此您可以开始以这种方式制作生产应用程序。仍然有一点点的功能差异，但随着 Flutter 新功能的发布，这种差异正在缩小。

## 结论

现在，电子在桌面应用程序开发中非常流行，因为它允许开发人员用 web 技术制作桌面应用程序。

然而，[由于高物理资源使用率，电子应用程序经常成为臃肿的软件](https://blog.logrocket.com/why-use-electron-alternative/)。Flutter 的性能比 Electron 好，因为它不在 web 浏览器上执行应用程序的 GUI 逻辑。

[Flutter 确实有一些开发者的痛点](https://blog.logrocket.com/pros-and-cons-of-flutter-app-development/)。它带有一种不熟悉的编程语言和一个全新的小部件工具包。尽管如此，桌面平台的发布二进制文件并不是轻量级的。

与 React Native 、Ionic 和 Electron 等其他框架相比，Flutter 继续以其令人印象深刻的整体性能和特性[给人留下深刻印象。你更喜欢哪种跨平台的移动 app 开发框架？](https://blog.logrocket.com/react-native-vs-flutter/)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)