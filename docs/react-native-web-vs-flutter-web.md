# React 原生 Web vs. Flutter web 

> 原文：<https://blog.logrocket.com/react-native-web-vs-flutter-web/>

***编者按:*** *这篇文章于 2021 年 7 月更新，提供了关于 Flutter 2 的 web 支持的信息，以及 React Native 和 Flutter 的最新比较。你可以[在这里](https://blog.logrocket.com/whats-new-in-flutter-2-0/)了解更多关于 Flutter 2.0 的内容。*

作为开发人员，我们总是在寻找能够让我们节省时间和精力的工具。我们希望能够编写代码并在任何地方运行。有了 React Native 和 Flutter，这个梦想正在慢慢变成现实。因为现在，我们不仅可以在 Android 和 iOS 上运行应用程序，还可以在 web 甚至桌面上运行它们。

在本文中，我们将比较每个平台。然后，我们将放大他们的网络功能，看看他们是否足够好，可以用于生产。最后，我们将看看每个平台的优缺点。读完这篇文章后，你将能够决定在你的下一个项目中使用哪一个。

## React Native vs. Flutter:比较底层平台

为了有效地判断 React Native Web 和 Flutter for web，我们首先比较底层平台是很重要的。我们不仅要考虑网络功能，还要考虑整体平台体验。

我们将使用以下标准作为比较点:

### 初学者友好度

一个初学者从零平台知识到一个全功能的应用程序需要多长时间？

在 React Native 中，这是一个多步骤的过程:

1.  安装 Node.js
2.  安装 Git
3.  安装 Watchman(MAC OS 用户)
4.  安装 [Expo](https://expo.io/) CLI
5.  生成新项目
6.  在您的移动设备上安装 Expo 客户端
7.  运行项目

在浏览器上运行它还需要一个额外的步骤，但这可以忽略不计，因为它只是一个安装 web 依赖项的命令。

上面提到的步骤来自快速入门指南。如果您走 CLI 路线，您将需要在设置您的机器时投入更多的时间，尽管初学者很少需要这样做，因为大多数本机 API 已经在 Expo 中实现了。

另一方面，Flutter 的开发设置可以总结为以下步骤:

1.  下载 Flutter SDK
2.  安装必备的开发工具(例如 curl、zip、git)
3.  将 Flutter SDK 添加到您的系统路径中
4.  安装 Android Studio 和 Xcode
5.  配置 XCode 命令行工具(用于 MacOS)
6.  生成新项目
7.  运行项目

对于网络设置，您需要完成[几个额外的步骤](https://flutter.dev/docs/get-started/web)。

这看起来很简单，但是对于一个完全没有命令行经验的初学者来说，也不知道如何安装不同的工具，这可能会很困难。

社区是选择平台时要考虑的另一个重要因素。这个社区是库和插件、教程、论坛/堆栈溢出问题和 GitHub 问题的顶峰。

React Native 领先了两年多，但这并不一定意味着它更受欢迎。 [Flutter 在 GitHub](https://github.com/flutter/flutter) 上有 125000 颗星星，而 [React Native 大约有 97000 颗](https://github.com/facebook/react-native)。看起来它们得到了相似的使用:两者都有大约 10 万个关于堆栈溢出的问题。

在实际使用方面，根据 2021 年的[堆栈溢出开发者调查，大约 15%的受访者使用 React Native，而大约 14%的人使用 Flutter。](https://insights.stackoverflow.com/survey/2021)

然而，React Native 对于开始移动开发的 web 开发人员来说是更受欢迎的选择，因为它使用 JavaScript 和 CSS 的子集来设计应用程序。

### 插件支持

Web 开发人员是一群被宠坏的人。几乎所有我们在构建现代 web 应用程序时需要的功能都已经有了相应的实现库。我们所要做的就是学习它的 API，然后我们就可以开始了。

Flutter 有一个相当不错的[插件系统](https://pub.dev/flutter/packages)，实现了移动应用中常用的大部分功能(例如，社交登录、谷歌地图)。

React Native 赢得了这一点，因为我们可以使用几乎任何 npm 模块(除了那些具有本机依赖性的节点模块)。

### 开发人员生产力

决定使用哪种工具的另一个重要因素是开发人员的生产力。这可能意味着很多事情，因此我们将把它分解为以下内容:

1.  程序设计语言
2.  证明文件
3.  代码结构
4.  开发者工具
5.  UI 组件
6.  本机 API 访问

#### 程序设计语言

要构建 React 原生应用，你需要知道如何编写 JavaScript 代码。在 Flutter 中，你需要知道 [Dart](https://dart.dev/guides) 。

大多数来探索使用单一代码库构建跨平台应用程序的解决方案的人都是 web 开发人员。我们希望重新利用我们现有的网络生态系统知识来构建移动和桌面应用。这意味着 React Native 对我们来说是一个更可行的选择，因为我们实际上不必学习任何其他东西来使用 React Native 构建应用程序。

#### 证明文件

能够在相对较短的时间内找到您正在寻找的东西并立即掌握信息是开发人员生产力的另一个决定因素。

Flutter 的文档比 React Native 更有条理，也更详细。但是对于初学者来说，大量的链接会让你不知所措。我喜欢 React Native 文档的简单性。

也就是说，我们不会永远是初学者。一旦我们获得了更多的经验，并习惯了在哪里可以找到东西，那么事情就变得容易了。这是 Flutter 的文档超越本地反应的地方。此外，React Native 依赖于许多社区编写的库，因此它的文档非常零散。与此同时，Flutter 将所有东西都放在了一个地方。

#### 代码结构

作为一名新的开发人员，了解现有项目的代码组织方式有多容易？

在 React Native 中，您几乎可以拥有任何想要的结构。通常，这取决于项目的规模以及您的团队所采用的各种工具和库。例如，使用 Redux 将迫使你进入一种特定的结构，因为你将有 reducers、actions 等。

颤振的情况也是如此；您可以按照自己的意愿组织代码。但是由于导航和状态管理已经融入到框架中，代码组织也会变得更加固执己见。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

#### 开发者工具

如果一个平台不支持热重装，那么热重装就是其中之一。能够修改您的代码并在保存时即时看到更改是一个很大的生产力助推器(特别是在双显示器设置上)。

React Native 和 Flutter 都支持这个特性，不过如果你和 Expo 比较的话，它在 Flutter 上要快一些。与 React Native 的 CLI 路径相比，只有一点点不同。

另一个需要考虑的重要事情是调试体验。在 React Native 中，错误消息可能很难理解。而在 Flutter 中，由于 Dart 是一种静态类型语言，它显示的错误消息往往更有意义。

#### UI 组件

React 原生组件转换为原生 UI 组件，因此它具有与原生 app 相同的外观和感觉。

另一方面，Flutter 使用渲染引擎绘制 UI 元素。它负责创建每个小部件的所有基本功能。这意味着只要有相应平台的更新，Flutter 的开发就会不断努力以匹配原生 UI 行为。

React Native 只提供了几个基本组件。尽管有各种 UI 套件可用，但它仍然无法击败内置在 Flutter 中的 widget 集合。Flutter 唯一的缺点是，与 T2 的 iOS 系统相比，它为安卓系统提供了更多的插件。

#### 本机 API 访问

React Native 和 Flutter 都支持常用功能的本机 API，如图像拾取器和地理定位。如果你需要一个还没有的原生功能，你可以随时编写[定制的特定平台代码](https://flutter.dev/docs/development/platform-integration/platform-channels)或者[创建你自己的原生模块](https://reactnative.dev/docs/native-modules-setup)。

### 应用性能

应用程序的性能很难判断，因为它完全取决于你要构建什么类型的应用程序。如果你的应用程序不需要复杂的布局、过渡，也不需要在本机端来回传递数据，那么你可以选择任何一个平台。

然而，当谈到纯代码性能时，Flutter 是明显的赢家，因为您编写的代码被直接编译成本机代码。与 React Native 不同，Flutter 没有在 JavaScript 端和本机端之间传递数据的“JavaScript 桥”概念。React Native 的“桥”本身可能很快就会被 JSI 取代。

### 网络支持

React Native 和 Flutter 都有 web 支持，前者更多的是由社区领导的工作，而后者是由 Flutter 团队领导的。

对 React Native 的 Web 支持比在 Flutter 中更成熟。Web 支持可以添加到现有的 [React Native 项目](https://docs.expo.dev/guides/running-in-the-browser/#adding-web-support-to-expo-projects)和 [Flutter 项目](https://flutter.dev/docs/get-started/web#add-web-support-to-an-existing-app)中，尽管它们实际上没有可比性。React Native Web 编译成实际的 HTML 元素，而 Flutter 使用画布。

在本文的后面，我们将更深入地探讨每个平台的 web 支持。

### 测试和部署

平台是否支持自动化测试和部署(供应、持续集成和部署)？这对于较小的项目是可选的，但对于较大的项目是至关重要的。它帮助我们防止应用程序中的错误，而不是陷入手动部署过程。

在撰写本文时，React Native 还没有内置测试和部署工具。就像其他事情一样，它依赖于社区构建的工具。为了测试 React Native，我们有 React Native 测试库、Jest 和 Detox。

说到供应和部署，有 [fastlane](https://fastlane.tools/) 、 [App Center](https://appcenter.ms/) 、 [codemagic](https://codemagic.io/start/) 、 [Bitrise](https://www.bitrise.io/) 和 [Expo 的 EAS build](https://docs.expo.dev/build/introduction/) 。

另一方面，Flutter 有[内置的测试支持](https://flutter.dev/docs/testing)(单元、小部件和集成)。对于部署，它可以与 fastlane、Bitrise、 [Cirrus CI](https://cirrus-ci.org/) 、 [Appcircle](https://appcircle.io) 以及 codemagic 一起使用。

## 颤振腹板概述

Flutter web 是 Flutter 的一个实现，它允许您将用于构建 Flutter 应用程序的 Dart 代码编译成 HTML、CSS 和 JavaScript 代码。

Flutter web 通过使用 web 渲染器来工作。简单地说，它们用于在 web 上渲染一个 Flutter 应用程序。Flutter 使用两个渲染器:HTML 渲染器和 CanvasKit 渲染器。前者使用 HTML、CSS 和 Canvas API 来生成页面。后者基本上是使用 WebAssembly 和 WebGL 的 web Skia。

HTML 渲染器的主要优点是其紧凑的尺寸，尽管其缺点是其性能。

另一方面，CanvasKit 提供了很好的性能，但是启动时间很短。这主要是因为与 HTML 渲染器相比，下载量增加了 2Mb。

用户可以访问适用于移动设备的小部件。但是开发者必须考虑如何让他们的应用程序响应迅速。因此，他们必须使用 LayoutBuilder 或 MediaQuery 等小部件和类，以便他们的应用程序在用户使用的任何平台上都看起来很棒。

说到插件，并不是所有的插件都有 web 实现(例如 ARCore，文件系统访问)。在 pub.dev 中，您可以过滤结果，只显示对 web 可用的[插件。](https://pub.dev/flutter/packages?platform=web)

### 为什么说 Flutter web 好？

*   它很稳定，可以投入生产
*   该特性集是完整的，使开发人员能够构建丰富的交互式 web 体验
*   访问可用于 Flutter for mobile 的相同小部件
*   完全访问运行在 web 上的所有现有的 [Dart 库](https://pub.dev/dart/packages?runtime=web)
*   集成 ide 和文本编辑器(IntelliJ，VS 代码)

### 为什么 Flutter web 不好？

*   如果使用 CanvasKit 作为渲染器，下载量会很大
*   并非所有的 JavaScript 库都受支持。它只支持 [Dart 的 JS-interop 包](https://dart.dev/web/libraries)
*   不支持搜索引擎优化
*   目前更适合渐进式网络应用和游戏，而不是典型的网站

## React Native for Web 概述

[React Native for Web](https://github.com/necolas/react-native-web) 是尼古拉斯·加拉格尔的一个开源项目。React Native for Web 使得开发者在 Web 上使用 React Native 组件和 API 成为可能。这样做的明显优势是，它允许在多个平台之间共享代码，因此您只需编写大部分单一代码，就可以在任何地方运行它。

当你听到 React Native for Web 的时候，你想到的第一个问题可能是为什么不直接使用 React？答案是代码共享。如果你使用 React，你不能真正利用 React Native 提供的工具。您将会对相同的功能编写两次代码。

请注意，并不是所有的本地组件和 API 都可以在 web 上使用。这是因为它们并不都有相同的 web API。React Native for Web 所做的主要工作是为每个 React Native 组件和 API 绘制 Web 等效物。因此，`<View>`组件变成了`<div>`,`<Image>`组件变成了`<img>`，以此类推。

第二个可能想到的问题是 React Native for Web 和 Expo Web 有什么区别？我怎么知道用哪一个？嗯，就像 React 本地项目的标准 CLI 路线一样，也有快速启动路线，即 [Expo](https://expo.io/) 。

当你已经在用 Expo 构建你的项目时，Expo Web 只是一个很好的附加物。因此，如果您已经有了一个现有的项目，主要的决定点是您是以标准 CLI 方式还是 Expo 方式构建您的项目。然而，如果你的项目是新的，它将完全取决于你的项目是什么。如果您认为您将需要大量具有本机依赖性的第三方库，那么您最好坚持使用标准的 React 本机 CLI。否则，真的没有理由不使用博览会，因为它只是让事情对你来说容易得多。

计划使用 React Native for Web 的开发人员必须担心以下问题:

1.  就像 Android 和 iOS 之间如何存在平台差异一样，web 也是如此。这意味着您现有的 React 本机代码不会通过使用 React Native for web 立即在 Web 上运行。您仍然需要进行大量的平台检测和代码修改。对于像文件系统这样的东西来说尤其如此。
2.  并非 React Native 中可用的所有 API 在浏览器中都可用。这意味着，如果你有一个现有的 React 原生应用程序，你要么必须自己重新实现该功能，要么在 web 上根本不支持它。这个 API 问题的一个例子是[图像编辑器](https://github.com/react-native-community/react-native-image-editor/issues/47)。
3.  并不是所有内置于 React Native 或 Expo 的组件都可以在 web 上工作。这方面的一个例子是模态组件。有时会有另一个开发人员发布他们自己的可以在网络上运行的组件版本，但是如果没有的话，那么你必须构建自己的组件。图书馆也是如此；作者将不得不为 web 编写他们自己的实现。

### 为什么说 React Native 对 Web 有好处？

*   就像 React Native 一样，React Native for Web 包含允许开发人员[构建可访问应用的工具。](https://necolas.github.io/react-native-web/docs/accessibility/)这是通过`accessibilityLabel`和`accessibilityRole`等辅助工具提供的
*   允许通过[平台检测](https://necolas.github.io/react-native-web/docs/platform/)执行特定于 web 的代码。我们不能期望所有的 API 都能工作，因此检测平台非常重要，这样您就只能执行适用于当前平台的代码
*   [支持服务器端渲染](https://necolas.github.io/react-native-web/docs/rendering/)。您可以使用`AppRegistry`来硬编码用于呈现应用程序的 HTML 文档字符串
*   Expo Web 充当 React Native Web 的包装器，因此您可以启动一个可以在 Android、iOS、Web 甚至桌面(通过电子)上运行的 Expo 项目
*   世博网支持[渐进式网络应用](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps)

### 为什么 React Native for Web 不好？

*   在撰写本文时， [React Native for Web 仍未投入生产](https://github.com/expo/expo/issues/6782)
*   带有本机依赖项的 React 本机包不能在 web 上运行。如果你依赖于具有本地依赖性的第三方包(Swift/Objective-C 或 Java/Kotlin 代码)，那么这些包在 web 上是不能用的
*   没有本机依赖关系的 React 本机包也不能 100%工作。如果涉及到动画、过渡、手势、键盘和列表，尤其如此

## 正反两面摇摆

最后，让我们来看看是什么让每个平台变得伟大和不那么伟大。首先是颤振。请注意，我们不会讨论两个平台之间相似的优点和缺点。相反，在选择最适合您的平台时，这些要点可能会让您“赞成”或“反对”:

### 为什么 Flutter 好？

*   性能非常接近本机，因为您的代码编译成本机代码
*   材料设计和 Cupertino 的许多内置部件
*   内置测试支持
*   详细且组织良好的文档
*   错误消息更有意义，因为 Dart 是强类型的

### 为什么 Flutter 不好？

*   略微陡峭的学习曲线

## 对本地的利弊做出反应

现在让我们回顾一下 React Native 的优缺点:

### React 为什么是 Native good？

*   初学者友好的
*   通过 npm 模块访问 JavaScript 生态系统

### 为什么 React Native 不好？

*   与标准的 React 本地项目相比，Expo 上的热重装速度较慢
*   文档很少，也不是很详细
*   有限的本机 UI 组件
*   它没有官方的测试工具

## Flutter web 和 React Native for Web 哪个好？

就像每个关于什么是你下一个项目的最佳平台的争论一样，没有正确或错误的答案。它取决于各种因素，这些因素主要归结为您的项目的性能需求和您的团队的相关经验。你的项目需要流畅的用户界面交互和动画吗？你的团队有使用 JavaScript 的经验吗？你愿意探索和使用核心平台本身不提供的第三方解决方案吗？

说到 web 功能，我想说 React Native for Web 是生产就绪的，这取决于您的需求。它更忠实于让网络工作的技术，因为它只是简单地编译了老式的 HTML、CSS 和 JavaScript。

另一方面，Flutter web 也可以根据您的需求进行生产。它的主要问题是性能。他们已经使用 CanvasKit 解决了这个问题，但是用户将不得不等待更长时间来下载必要的脚本。

总的来说，对于开发者来说，这是一个激动人心的时刻，因为我们现在可以接触到比以往更多的平台。所有这些都离不开我们熟悉和喜爱的工具带来的舒适。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)