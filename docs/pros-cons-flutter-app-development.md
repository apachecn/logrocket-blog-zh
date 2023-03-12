# Flutter 应用程序开发的利与弊

> 原文：<https://blog.logrocket.com/pros-cons-flutter-app-development/>

***编者按**:这篇文章于 2022 年 9 月 19 日更新，包括了关于 Flutter 3.0 及其通过有组织的文档和更快的应用程序开发增加的可访问性的信息。*

如果你是一名经验丰富的移动应用开发者，你已经决定为 Android 或 iOS 设备开发应用。如果你是初学者，你可能还在考虑哪一个是你的首选平台。这些决定决定了你在成为移动或跨平台应用开发者的道路上应该学习哪些技术。

无论你的应用程序开发之旅将你带到哪里，Flutter 都是一个可靠的选择。在本指南中，我们将向您介绍 Flutter，概述使用 Flutter 的利与弊，并探索您可以用这个开源软件开发工具包构建什么。

我们将讨论以下内容:

## 什么是颤振？

[Flutter](https://flutter.dev/) 是 Google 开发的开源 UI 工具包，用于从单一代码库构建漂亮的原生用户界面 UI。换句话说，用 Flutter 构建的应用程序可以在 Android、iOS 和 web 上运行。

颤振框架是用 Dart 构建的。Dart 是一种现代的面向对象编程语言。以你对任何其他编程语言(如 JavaScript)的了解，这很容易掌握。

虽然最初是为开发跨平台应用而设计的，但现在，用 Flutter 3.0 版本创建桌面和网络应用已经成为可能。

谈论 Flutter 不能不提 [widgets](https://flutter.dev/docs/development/ui/widgets) 。小部件是任何 Flutter 应用程序的核心构件。

基本上，小部件是对用户界面一部分的描述。小部件类似于 React 中的组件。

您可以利用可定制的小部件在 Flutter 中构建您的 ui。小部件可以有背景、高度等属性。

在 Flutter 中有两种类型的小部件:

*   无状态小部件是不可变的，这意味着一旦创建了小部件，它们的属性，比如背景和高度，就不能改变
*   有状态窗口小部件是可变的，这意味着它们可以改变。这为用户互动创造了空间。您可以使用有状态小部件来保存状态和对用户交互的响应

## 用 Flutter 可以构建什么？

随着 Flutter 2.0 的发布，您现在可以构建以下内容:

1.  Android 和 iOS 的跨平台移动应用
2.  web 应用，包括[渐进式 Web 应用(PWAs)](https://blog.logrocket.com/why-you-should-turn-your-app-into-a-pwa/) ，单页应用(spa)，以及现有 Flutter 移动应用向 Web 的扩展
3.  搭载 Flutter 3.0 的桌面(Windows、Mac 和 Linux)应用

## 是什么让 Flutter 独一无二？

如果你曾经使用[跨平台框架](https://blog.logrocket.com/flutter-vs-react-native/)如 [React Native 或 Xamarin](https://blog.logrocket.com/xamarin-vs-react-native/) 构建过移动应用，你的代码将通过一个桥或开关，在设备上呈现之前，通过 JavaScript 将代码编译成 Webview。另一方面，Flutter 在本地呈现应用程序，而不是让平台来做。Flutter 只需要一个画布来在屏幕上呈现小部件，并访问诸如 tap 之类的事件和诸如设备上的摄像头之类的服务。

Flutter 通过 Dart 对即时和提前编译的支持，将代码编译成本机 ARM 代码，从而以设备的 ARM 为目标。这导致了更大的一致性，并使 Flutter 应用程序非常快，反应灵敏。

## 使用颤振的利与弊

现在我们来分解一下使用 Flutter 进行跨平台 app 开发的利弊。

### 赞成的意见

以下是 Flutter 在 React Native、Xamarin 和 NativeScript 等其他跨平台框架中迅速流行的一些原因。

#### 颤动一致性

可以说，Flutter 最大的吸引力在于“一次编写，随处运行”的概念。你不必担心在 Android、iOS 和网络上保持一致性。单个代码库处理所有这些，从而加快构建时间，节省资源，并简化代码的维护。

#### 有状态热重装

有状态热重装导致高开发速度。当代码发生变化时，Flutter 会提供即时的 UI 更新。多亏了实时编译器，这才成为可能。

在撰写本文时，就 GitHub 明星而言， [Flutter](https://github.com/flutter/flutter) 已经超过了 [React Native](https://github.com/facebook/react-native) 。这表明更多的开发者正在[拥抱 Flutter 而不是 React Native](https://blog.logrocket.com/why-flutter-is-overtaking-react-native/) 。

此外，2020 年关于栈溢出时[Flutter](https://insights.stackoverflow.com/trends?tags=react-native%2Cflutter)的提问数量超过了 React Native 上的提问数量。这意味着更多的开发者渴望了解更多关于 Flutter 的知识。

#### 开放源码

Flutter 是一个开源框架，这意味着您可以访问用于开发它的原始代码库。因此，你可以做出贡献，提出问题，帮助 Flutter 社区。软件开发中一些最大的进步都要归功于开源社区。

#### 更快的应用开发

有了 Flutter，跨平台应用变得更加容易。内置的小部件有助于更快的应用开发、测试和问题修复。Flutter 有助于快速原型化和开发 MVP。

#### 出色的文档

Flutter 以其优秀的文档而闻名，Flutter 文档组织有序，格式良好，易于理解，有助于开发人员构建 Flutter 应用。

Flutter 中的几乎所有东西——从布局、颜色、文本到按钮——本质上都是一个小部件，就像 React 中的组件一样。因此，Flutter 附带了大量可定制的小部件。

#### 你只需要知道达特

Dart 是一种客户端优化语言，可用于在任何平台上开发应用。Flutter 框架是在 Dart 中构建的，所以如果你想使用 Flutter 构建应用程序，你只需要知道 Dart。

Dart 令人惊叹的原因在于:它是一种简洁的、强类型的、面向对象的编程语言，提供了可靠的空安全，这意味着除非您同意，否则值不能为空。

Dart 语言支持实时(JIT)和提前(AOT)编译。JIT 有助于热重新加载——因为应用程序代码是在立即运行的同时编译的，所以会发生变化。AOT 编译处理编译代码到原生 ARM 机器码，使得 Flutter 速度极快。

#### 支持网络和桌面

Flutter 3.0 对 web 和桌面有稳定的支持。Flutter 开发人员现在不仅可以为手机开发，还可以用一个代码库为网络开发。对 web 的支持是一个巨大的胜利，因为它让您能够接触到更广泛的最终用户。随着桌面应用程序 3.0 版本的发布，Flutter 现在支持所有桌面平台，包括 Linux 和 macOS。

随着 Flutter 3.0 的发布，它现在为 Windows、macOS 和 Linux 的桌面版本提供了辅助功能，包括屏幕记录器、足够的对比度和更大的字体大小。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

#### 编译成本机 ARM 代码

与 React Native 和 Xamarin 等其他跨平台移动开发框架不同，Flutter 将其代码编译为原生 ARM 机器码，这有助于实现出色的性能。

随着 Flutter 3.0 的推出，macOS 桌面应用程序现在被创建为适用于采用英特尔和最新苹果硅处理器的 MAC 的通用二进制文件。

[Flutter DevTools](https://flutter.dev/docs/development/tools/devtools/overview)——包括[热重装](https://flutter.dev/docs/development/tools/hot-reload)、[代码格式化器](https://flutter.dev/docs/development/tools/formatting)、 [widget inspector](https://flutter.dev/docs/development/tools/devtools/inspector) 等。，—帮助您更高效地开发和调试。使用 widget 检查器，您可以在设备上的像素、widget 树和创建 widget 的代码行之间导航。

### 骗局

相比使用 Flutter 进行跨平台 app 开发的好处，并没有太多的坏处。然而，在决定哪个框架最适合您的项目时，值得考虑框架的相对不成熟性以及与这种新特性相关的限制。

下面是一些你应该考虑的潜在缺点。

#### 新兴框架

尽管它在跨平台开发人员社区中越来越流行，但 Flutter 仍然是一个新兴的框架。这意味着学习资源有限，插件和软件包数量相对较少。

Flutter 需要一些时间来提供新的功能，因为它们被添加到 iOS 和 Android 平台的原生 SDK 中。

#### 有限的插件/包

与 React Native 相比，Flutter 的插件和软件包数量有限。对于一个相对较新的框架来说，这是意料之中的；颤振的[首个稳定版本于 2018 年 12 月 4 日发布。](https://developers.googleblog.com/2018/12/flutter-10-googles-portable-ui-toolkit.html)

随着社区为 Flutter 构建更多的插件和包，这种限制的影响应该会逐渐减少。到目前为止，已经有超过 26K 个软件包可以用来帮助创建 Flutter 应用程序，而且这个数字还在持续增长。

#### 应用程序大小

Flutter 利用内置小部件而不是平台小部件，生成的应用程序通常更大。较大的应用程序下载时间较长，在智能手机上占用更多空间。

但是使用特定的库和包、资产压缩以及删除未使用的资源都有助于减小 Flutter 应用程序的大小。

与 JavaScript 相比，使用 Dart(用于构建 Flutter 应用程序的编程语言)的开发人员相对较少。这意味着，就像 Flutter 框架本身一样，旨在帮助您学习 Dart 的资源是有限的。

## 应该用 Flutter 吗？

鉴于其一个代码库适用于所有平台的方法，我相信 Flutter 将在所有支持平台的应用程序开发的进步中发挥重要作用。出于这个原因，如果你还没有，我推荐你学习如何用 Flutter 开发跨平台的应用。

了解如何使用 Flutter 使您能够身兼数职，通过创建一个编译成本机代码并将设备视为平台的单一代码库，同时充当移动和 web 开发人员。

更重要的是，Flutter 从 [React](https://reactjs.org/) 和 web 中获得了很多灵感，用 Flutter 构建的应用程序可以适应任何平台。这类似于[响应式/适应性网页设计](https://blog.logrocket.com/developing-responsive-layouts-with-react-hooks/)。

## 总结和关键要点

Flutter 团队[声明的任务](https://developers.googleblog.com/2021/03/announcing-flutter-2.html)是“从根本上改变开发者构建应用的方式，不是从你的目标平台开始，而是从你想要创造的体验开始。”

Flutter 已经从一个构建跨平台应用的工具包发展成为一个可移植的框架，用于构建各种平台，包括 Android、iOS、web 和桌面(Windows、Mac、Linux)。

Flutter 很快，因为它把你的代码编译成机器码。有了有状态热重装，开发人员的生产力提高了，现在支持 web 和桌面，Flutter 给了开发人员创造美好体验所需的杠杆。

最后，你不需要担心你的应用程序的用户界面将如何出现在 Android、iOS 或 web 上；Flutter 通过使用单一代码库帮助您保持一致性。创建 Flutter 是为了让开发者为所有设备构建漂亮的用户界面。

要了解更多信息，请查看官方文件。如果你已经有构建移动应用的经验，这里有一个关于[的速成班，给你现有的应用](https://flutter.dev/docs/development/add-to-app)添加 Flutter。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)