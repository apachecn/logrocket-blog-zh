# iOS 开发中的 Flutter 与 Swift

> 原文：<https://blog.logrocket.com/flutter-vs-swift-for-ios-development/>

在本指南中，我们将比较用于构建 ui 的跨平台开源工具包 Flutter 和用于跨 iOS 和 macOS 构建应用的主要语言 Swift。我们将从功能、易用性和开发人员构建 iOS 应用程序的整体体验方面评估每种编程语言。我们还将提供一些指导，帮助您选择在给定的 iOS 应用程序开发项目中学习或使用哪种语言。

## Swift 与 Flutter:高水平概述

[Swift](https://swift.org/) 是苹果为 iOS、macOS、watchOS、tvOS 应用开发打造的开源通用编程语言。这是一种现代、快速、安全、交互式的语言，初学者很容易掌握。

例如，要向控制台输出(打印)一条消息，只需使用`print()`方法:

```
print("Hello World!")
// outputs the characters "Hello World!"
```

然后是 [Flutter](https://flutter.dev/) ，谷歌的开源 UI 工具包，用于从单一代码库构建跨平台的原生用户界面。Flutter 现在支持开发可以在网络和桌面上运行的应用程序[。](https://blog.logrocket.com/how-to-migrate-a-flutter-mobile-app-to-the-web/)

颤振框架是用 Dart 构建的。要在 Flutter 中构建应用程序，你必须对 [Dart 编程语言](https://blog.logrocket.com/introduction-to-using-dart-in-flutter/)有所了解。

Dart 是一种客户端优化语言，用于在任何平台上开发应用程序。它简洁、强类型、面向对象，并且支持可靠的空安全。

## 为 iOS 开发设置您的系统

在您开始构建一个 iOS 应用程序之前，初始设置包括添加基本工具，如系统和 ide，以便启动和运行。让我们看看如何使用 Flutter 和本地(Swift)工具为 iOS 开发设置您的系统。

要使用 Flutter 构建一个 iOS 应用，第一步是[下载安装包](https://flutter.dev/docs/get-started/install/macos)并将文件解压到所需位置。接下来，安装 [Xcode](https://blog.logrocket.com/xcode-for-react-native-developers-tutorial-and-best-practices/) 。然后你可以使用你选择的任何 IDE 来构建你的 Flutter iOS 应用。

要设置您的系统以使用 Swift 构建本地 iOS 应用程序，您只需要一台 macOS 和 [Xcode](https://developer.apple.com/xcode/) 。这使得在 Swift 中设置 iOS 开发环境比在 Flutter 中更容易一些。

## 用 Flutter 和 Swift 构建 iOS 应用程序

既然我们已经设置好了，是时候动手了，开始为一个同时使用 Flutter 和 Swift iOS 的新项目构建启动文件。

### 颤振演示应用程序

创建一个新的 Flutter iOS 应用程序，方法是在您想要放置项目的目录中的命令行上运行以下命令:

```
flutter create demo_flutter_app
// this creates a new called demo_flutter_app, containing the Flutter's starter app.
cd demo_flutter_app
// enter the demo_flutter_app directory
flutter run
// to launch the app in a simulator
```

对于我的演示，我使用的是 Android Studio IDE。您可以使用 Android Studio IDE 启动一个新的 iOS Flutter 项目，如下所示:

**文件** → **新建颤振项目**，然后选择**颤振应用**，输入项目名称，创建项目。

Flutter 会创建一个 Flutter starter app，如下图:

![Flutter Starter App](img/c5ec5e0eb4e14e890bdc2a893eb66c4f.png)

### 原生(Swift) iOS 演示应用

要使用 Xcode 启动一个新的 iOS 项目，点击**创建一个新的 Xcode 项目**，然后选择**应用**，然后输入项目名称。点击**下一步**，在你选择的目录下创建一个项目。

Xcode 将创建一个 iOS Starter 应用程序，在模拟器中运行时显示“Hello World ”,如下所示:

![iOS Starter App](img/e379136302b359595c1c22aee9a10383.png)

现在，我们已经用 Flutter 和 Swift 构建了基本的示例 iOS 应用程序，让我们比较一下学习曲线、开发时间、UI 创建过程以及与每个相关的整体开发人员体验。

## 学习曲线

与 Flutter 不同，一旦你在 Xcode 中创建了一个项目，Swift 的 iOS starter 应用程序就很轻便，很容易上手。这是因为您只需要熟悉 Swift 编程语言，而 Flutter 需要了解如何用 Dart 编写。

更重要的是，苹果将 Swift 设计成“[任何人的第一种编程语言](https://developer.apple.com/swift/#great)”你也可以利用 [SwiftUI](https://developer.apple.com/xcode/swiftui/) 来构建 iOS 应用。

Swift 的 iOS app 主要入口是`Content.swift`；对于 Flutter，它是 lib 目录下的`main.dart`。

Flutter 有[丰富的文档](https://flutter.dev/docs)，这是你开始用工具包构建 iOS 应用所需要的全部。 [Google Flutter codelabs](https://codelabs.developers.google.com/?cat=flutter) 是另一个伟大的资源，可以让你用 Flutter 弄脏你的手。

但是在你可以用 Flutter 构建任何类型的应用程序之前，你需要学习 Dart 编程语言。幸运的是，有一个不断增长和支持的开发者社区，他们一直在创造新的学习资源来帮助新的 Flutter 开发者 T2 熟悉 Dart T3。

与 Flutter 类似，苹果官方文档是开始学习使用 Swift 构建原生 iOS 应用程序的最佳地方。这些文档还包括使用 [SwiftUI](https://developer.apple.com/xcode/swiftui/) 的说明和指南，SwiftUI 是一个为任何苹果平台构建用户界面的声明性框架。

当谈到学习曲线和可接近性时，Swift 比 Flutter 有优势，因为如前所述，你不需要学习新的编程语言来构建原生 iOS 应用。也就是说，由于它们丰富的文档和社区支持，Flutter 和 Swift 都相对容易学习。

## 程序调试时间

在这一节中，我们将评估与 Swift 相比，使用 Flutter 开发一个相当标准的 iOS 应用程序所需的时间。我们将在分析中考虑调试和测试之类的事情。当然，交付一个 app 所需的时间也取决于项目的复杂程度、开发者的技术水平和工作风格等。

Dart 支持即时和提前编译，这就是 Flutter 支持有状态热重载的原因，即在应用程序仍在运行时，当代码发生变化时，ui 会即时更新。这总是导致高开发速度。

借助 Flutter 面向所有人(Android 和 iOS)的单一代码库，团队可以按时发布跨平台应用，降低在不同平台上开发应用的成本，并加快发布一个[最小可行产品(MVP)](https://blog.logrocket.com/product-management/12-agile-manifesto-principles-how-to-adopt-them/#3-deliver-working-software-frequently) 的时间。

尽管 Swift 是 iOS 应用程序开发的母语，但当应用程序发生变化时，您需要重新加载应用程序才能看到变化。

由于热重装，Flutter app 的重装速度更快，这使它在开发速度和交付 iOS 应用开发项目所需的时间方面比 Swift 更具优势。

## 用户界面(UI)创建

接下来，我们将研究使用 Flutter 和 Swift 为 iOS 应用程序创建用户界面的过程。我们将通过使用 Swift 和 Flutter 构建一个简单的应用程序来演示与每个应用程序相关的过程，从而结束这一比较。

### 用 Flutter 为 iOS 构建用户界面

Flutter 应用开发的核心是 [widgets](https://blog.logrocket.com/widgets-the-building-blocks-of-flutter-apps/) 。小部件是对用户界面一部分的描述。

Flutter 附带了可定制的小部件，比如`[Text() widget](https://flutter.dev/docs/development/ui/widgets/text)`，可以用来创建 ui。小部件也有属性，比如背景、宽度、高度等。有两种类型的小部件:有状态的和无状态的。

有了小部件，你不需要费心从头开始创建 ui。您可以简单地利用 Flutter 提供的可定制小部件。

下面是一个使用 Flutter 编写 Hello World 程序的例子:

```
import 'package:flutter/material.dart';

void main() {
runApp(MyApp());
}

class MyApp extends StatelessWidget {
@override
Widget build(BuildContext context) {
return MaterialApp(
title: 'Hello World Demo',
home: Scaffold(
appBar: AppBar(
title: Text('Hi there'),
),
body: Center(
child: Text('Hello World'),
),
),
),
}
}
```

### 使用 Swift 为 iOS 构建用户界面

通过 [SwiftUI](https://developer.apple.com/tutorials/swiftui) ，你可以为所有苹果平台创建 UI。SwiftUI 采用了易于阅读和自然编写的声明式语法方法。

下面是一个使用 SwiftUI 编写 Hello World 程序的示例:

```
import SwiftUI

struct ContentView: View{
var body: some View{
Text("Hello World!")
}
}
```

最终，使用 Flutter 和 Swift 都可以为 iOS 应用程序创建漂亮迷人的用户界面，并且都享有令人惊叹的社区支持。也就是说，与 Flutter 相比，Swift 以其代码简洁而著称。

## 将原生 iOS 应用移植到 Flutter

根据 Flutter 文档，可以将 Flutter 添加到现有的 iOS 应用程序中。在这一节中，我们将向您展示如何移植一个原生 iOS 应用程序到 Flutter。

要将 Flutter 添加到 iOS 应用程序中，首先要在项目的根目录中创建一个 Flutter 模块。

```
cd rootdir
flutter create --template module my_flutter
```

在终端中运行上述命令会导致 Flutter 自动运行`flutter pub get`，这将把 Flutter 依赖项添加到`my_flutter/pubspec.yaml`文件中。

![Flutter Module](img/de344bebf0544b11d09953dc75c21bd6.png)

注意，在上面的演示中，我将颤振模块命名为`my_flutter1`。

下面是通过[文档](https://flutter.dev/docs/development/add-to-app/ios/project-setup)的`my_flutter`模块目录的结构:

```
my_flutter/
├── .ios/
│   ├── Runner.xcworkspace
│   └── Flutter/podhelper.rb
├── lib/
│   └── main.dart
├── test/
└── pubspec.yaml
```

注意，`.ios/`目录是一个隐藏的子文件夹。

下一步是将 Flutter 模块嵌入到 iOS 应用中。[文档](https://flutter.dev/docs/development/add-to-app/ios/project-setup)概述了在 iOS 应用中嵌入 Flutter 的两种方式:

1.  使用 [CocoaPods](https://cocoapods.org/) 依赖管理器并安装 Flutter SDK
2.  为 Flutter 引擎创建框架，编译 Dart 代码，和 [Flutter 插件](https://flutter.dev/docs/development/packages-and-plugins/developing-packages)

推荐的方法是使用 CocoaPods 依赖项管理器，因此我们将相应地进行。

假设您已经[安装了 Flutter SDK](https://flutter.dev/docs/get-started/install/macos) ，向应用程序添加一个 Podfile。一个 [Podfile](https://guides.cocoapods.org/using/the-podfile.html) 是一个描述一个或多个 Xcode 项目的目标的依赖关系的规范。

要创建 Podfile，请打开终端，将 CD 放入 iOS 项目目录。然后，在项目目录下运行`$ pod init`。

使用编辑器(我推荐 VS 代码)打开 Podfile，然后粘贴下面的代码:

```
flutter_application_path = '../my_flutter'
load File.join(flutter_application_path, '.ios', 'Flutter', 'podhelper.rb')
```

在 Podfile 目标上，在`do`和`end`之间，添加以下内容以嵌入 Flutter:

```
  install_all_flutter_pods(flutter_application_path)
```

接下来，在终端上运行`$ pod install`。现在你的 iOS 应用内嵌了 Flutter。

现在，您可以使用“构建并运行播放”图标或 command + B 在 Xcode 中构建您的项目。但首先，退出 Xcode，再次启动它，并打开项目的`.xcworkspace`。

你的 iOS 现在已经移植到 Flutter 了，你可以开始构建了。

## 结论

在本指南中，我们比较了使用 Swift 编程语言构建原生 iOS 和使用 UI 工具包 Flutter 构建跨平台(特别是 iOS)应用的开发人员体验。

在我看来，选择用 Flutter 还是 Swift 来构建你的下一个 iOS 应用取决于你对 Dart 和 Swift 的偏好和舒适度。用 Swift 构建 iOS 应用的明显优势源于原生的 Swift 编程语言和苹果的 SwiftUI 框架。另一方面，使用 Flutter 构建 iOS 应用程序使您能够使用单一代码库构建 iOS 和 Android 应用程序。有关使用 Flutter 构建跨平台应用的更多信息，请查看“[Flutter 应用开发的利与弊](https://blog.logrocket.com/pros-and-cons-of-flutter-app-development/)”

最后，让我们总结一下我们在 iOS 开发中对 Flutter 和 Swift 进行比较的一些要点:

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

*   对于设置，如果您想使用 Swift 进行构建，您需要在 mac 上安装 Flutter SDK 和 Xcode。对于 Flutter，您需要安装 Flutter SDK
*   与可以使用 Xcode 创建的 iOS 入门应用程序相比，Flutter 的入门应用程序比较笨重
*   Flutter 和 Swift 都有很好的文档，可以帮助开发人员快速了解这两个平台。Flutter 的文档还解决了跨平台应用程序开发的问题
*   SwiftUI 建立在模型视图视图模型(MVVM)架构之上，而 Flutter 在其声明式架构中借鉴了 React 的一些经验
*   您可以将原生 Swift 应用移植到 Flutter，方法是在 iOS 应用的根目录下创建一个 Flutter 模块，或者使用 CocoaPods 将 Flutter 框架嵌入到应用中