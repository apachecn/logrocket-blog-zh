# 为什么 Flutter 正在超越 React

> 原文：<https://blog.logrocket.com/why-flutter-is-overtaking-react-native/>

## 介绍

作为开发人员，很难为多种屏幕尺寸或不同的操作系统构建应用程序，但跨平台语言彻底改变了软件开发。

有许多跨平台语言使得软件开发变得非常容易，但是 Flutter 正在获得对现有跨平台技术的认可，如 React Native、NativeScript、Xamarin 等。

Flutter 构建的应用程序使用 [Dart](https://dart.dev/) ，它适用于客户端和服务器端开发。Dart 是 Google 开发的一种开源的面向对象的编程语言。

Flutter 是一个用于构建应用程序的完整 SDK，而不仅仅是一个框架。这意味着它包含了构建用户界面(UI)所需的一切，包括材料设计和 Cupertino 小部件。这使得开发人员可以轻松地在 iOS 和 Android 上呈现 UI。

## 什么是跨平台开发？

跨平台开发是为不同平台或软件环境设计软件产品或服务的过程。

跨平台开发使开发人员能够一次构建应用程序，并在每个平台或软件环境上运行它们。

## 统计数字

根据 2020 年 1 月至 2021 年 1 月的谷歌趋势搜索结果，比较 Flutter 和 React Native 在谷歌上的搜索结果显示，Flutter 是搜索次数最多的术语。

![Flutter Search Trends](img/6e565db414b0618ac14295e1533acf3d.png)

[Source Google Trends](https://trends.google.com/trends/explore?q=flutter,react)

根据显示 2020 年 1 月至 2021 年 1 月搜索结果的谷歌趋势，比较 Flutter、React Native、Xamarin 和 NativeScript 在谷歌上的搜索结果，显示 Flutter 是搜索次数最多的术语。

![Flutter Search Trends Showing Popularity](img/9b782b7fe2a7a16fd563b02ea7f449a7.png)

[Source Google Trends](https://trends.google.com/trends/explore?q=flutter,react%20native,Xamarin,NativeScript)

## 使用 Flutter 与 React Native 的优势

### 用户界面

Flutter 为其 widget 使用了[材质设计](https://material.io/)和[库比蒂诺](https://flutter.dev/docs/development/ui/widgets/cupertino)，这提供了一个富有表现力和灵活性的 UI，使开发人员能够通过快速原型制作轻松地在 iOS 和 Android 平台上渲染 UI。

### 表演

对于 Android 和 iOS 来说，由于 Flutter 被编译成了原生的 [ARM 代码](https://en.wikipedia.org/wiki/ARM_architecture)，性能问题得到了进一步的缓解。Flutter 允许程序员使用单一的代码库，这统一了团队，降低了风险，并加快了上市时间。同时具有本地外观和效率的优点。

### 跨平台

Flutter 为 web 和移动开发使用单一的代码库。

### 消防基地支持

当谈到使用 Google Firebase 进行云服务时，Flutter 在 [Firebase 安装](https://flutter.dev/docs/development/data-and-backend/firebase)上有很好的支持，这使得开发人员可以轻松地将 Flutter 应用程序集成到 Firebase 上。

### 社区支持

React Native 有很大的社区支持，但 Flutter 社区很有希望， [Flutter](https://github.com/flutter/flutter) 是一个由谷歌支持的开源工具，拥有 105k GitHub stars 和 14.6k GitHub forks，并且在 [Discord](https://discord.com/invite/N7Yshp4) 、 [Meetup](https://www.meetup.com/find/?allMeetups=false&keywords=flutter&radius=Infinity&userFreeform=Santa+Clara%2C+CA&mcId=z95054&mcName=Santa+Clara%2C+CA&sort=recommended&eventFilter=mysugg) 、 [Gitter](https://gitter.im/flutter/flutter) 、 [Medium](https://medium.com/flutter) 和[堆栈溢出](https://stackoverflow.com/tags/flutter)上与开发人员有良好的社区互动。

### 热重装

通过有状态热重新加载，Flutter 提供了快速开发周期，能够重新加载实时运行的应用程序的代码，而无需重新启动或丢失应用程序状态。如果程序员在构建 Flutter 应用程序和重新编译时更改了一些代码，他们不必返回或手动重新创建状态来查看更改了什么。

## 颤振是如何工作的

Flutter 使用单个代码库构建了一个具有漂亮 UI 的本机应用程序，而没有牺牲速度、质量或性能。

跨平台开发中的方法仅使用单一代码库，但它涉及应用程序创建 HTML 并在平台上的 web 视图中显示它的 web 视图，或者构建从 JavaScript 等通用语言的代码到按钮和菜单等本机 UI 组件的桥梁。

与此相反，Flutter 呈现应用程序本身，而不是让平台这样做，消除了应用程序和平台之间所需的任何桥梁或上下文。该平台只需要一个画布来呈现小部件，这样它们就可以出现在设备屏幕上，并访问手指点击等事件以及相机和地理定位等服务。这使得该应用程序速度超快，响应速度快，可以在任何平台上运行。

![Flutter System Overview](img/190a2b2503453bacf111a73f69775ef2.png)

[Source morioh.com](https://morioh.com/p/b576a71cb674)

Flutter 由一个用 Dart 构建的框架和一个主要用 C++设计的渲染引擎组成，因为顶部区域的一切都是 Dart，所以你会得到一个完整的堆栈跟踪，以便更好地调试。在 Flutter 中，你在屏幕上看到的所有东西都是一个小部件。小部件可以描述为按钮或菜单等结构元素，字体或配色方案等风格元素，甚至是填充等布局。

DART 既提供即时(JIT)编译，也提供提前编译，这使得开发体验快如闪电，它有一个名为 **Hot Reload** 的特性，允许您在不到一秒钟的时间内看到代码的变化。

## 开始使用 Flutter

### 安装 SDK

要开始 Flutter 开发，你需要从官方的 Flutter 网站[这里](https://flutter.dev/docs/get-started/install)下载 SDK，并选择你正在安装的操作系统版本。

### 设置代码编辑器

接下来，根据你的喜好设置你的代码编辑器，对于本教程我将使用 visual studio 代码，如果你没有它，你可以在这里下载。设置好代码编辑器后，进入**扩展**选项卡，在扩展搜索部分，输入“Flutter”，从列表中选择 Flutter，然后点击 Install。

### 创建测试应用程序

转到**视图**选项卡并选择**命令面板。**

![Command Palette in View Tab](img/ec3a8758944f007aa8887d2536e88fab.png)

键入“颤振”并选择**颤振:新项目。**

![Flutter New Project Dropdown](img/183476f84468aed42cc43d6c4319c990.png)

接下来，输入一个项目名，如“my_first_flutter_app ”,然后为您的项目创建或选择一个父目录文件夹。

![Enter Project Name and Select Folder](img/e5645498ba62fc7cf1134873c337cf84.png)

最后，等待您的项目创建完成，您将看到一个`libs`文件夹，在其中，您将看到`main.dart`文件。`libs`文件夹是您使用 Dart 编程语言编写大部分代码的地方。

![Main.dart File](img/a1c33a59a65554f0f9ecf2e8844f51ae.png)

### 测试应用程序

要测试它，请选择一个仿真器设备来运行您的应用程序。我使用 Android 模拟器运行应用程序:

![Android Emulator](img/7fceac328d4285b9514b41a2594e7cab.png)

[Source Flutter Github page](https://github.com/flutter/flutter)

要使用**热重新加载，**更改代码，然后点击热重新加载键或保存更改，以在您的仿真器或设备中查看更改。

![Changing Hot Reload](img/d6dbf64b08aefb2805e26bf509ba58a4.png)

### 使用 NoCode 构建 Flutter UI

使用 [Flutter studio](https://flutterstudio.app/) ，一个 Flutter UI(用户界面)NoCode 平台，你可以通过简单的拖放元素来构建用户界面。

![Flutter Studio Interface](img/a9adbbd2e283928acff1adae9019bce0.png)

[Screenshot from FlutterStudio](https://flutterstudio.app/)

从 **Material** 选项卡中，将 FloatingActionButton 和 BottomNavigationBar 拖放到移动屏幕上，然后将 BottomNavigationIcon 三次拖放到 BottomNavigationBar 中并编辑图标。

![Drag-and-dropping Elements](img/617bc4710fda7ec7efafa51b5cb08da6.png)

[Screenshot from FlutterStudio](https://flutterstudio.app/)

分别从**源代码**和 **pubspec** 页签中复制源代码和 pubspec 代码，将源代码粘贴到`main.dart`文件中，将 pubspec 代码粘贴到`pubspec.yaml`文件中。

![Pasting into pubspec.yaml File](img/809bf907a48f34651652a372b23082e4.png)

[Screenshot from FlutterStudio](https://flutterstudio.app/)

粘贴代码后，**热重装**，你会看到 app 的新鲜变化。

![Changes After Refresh](img/3f9c7135f6b5c997ac3aad389ed0f745.png)

GitHub 源代码的链接可以在[这里](https://github.com/Jethro-magaji/my_first_flutter_app)找到。

## 颤振用例

如今，许多公司都在使用 Flutter，包括房地产经纪人、腾讯、纽约时报、谷歌助手、Pickio 和 pick io。

### 集成

*   Firebase: Firebase 是一种云服务，旨在支持实时、协作的应用程序
*   Android SDK: 一个 SDK，为您提供构建、测试和调试 Android 应用所需的 API 库和开发工具
*   Dart:一种新的 web 编程语言，有库、虚拟机和工具
*   [插座。IO](https://socket.io/) :实时应用框架(节点。JS 服务器)
*   Google Admob :货币化和推广你的应用
*   [pub . dev](https://pub.dev/):Dart 编程语言的包管理器

## 结论

在这篇文章中，我们了解了为什么 Flutter 会超越 React。我们还研究了 Flutter 跨平台开发是如何工作的，以及使用 Flutter studio(一个在线 NoCode 平台)开始构建 Flutter 应用程序有多容易。

### 参考

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)

Start proactively monitoring your React Native apps — [try LogRocket for free](https://lp.logrocket.com/blg/react-native-signup).