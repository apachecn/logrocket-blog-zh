# 比较跨平台框架的开发人员体验

> 原文：<https://blog.logrocket.com/comparing-developer-experience-for-cross-platform-frameworks/>

跨平台移动应用程序开发允许您仅使用一个技术堆栈为 iOS 和 Android 等多个平台构建移动应用程序。

这意味着，您可以一次编写代码，然后在多个平台上同时部署，而不是创建应用程序的多个版本，每个版本都使用针对每个平台的专用本地语言编写。

## 什么是开发者体验？

开发者体验(Developer Experience，缩写为 DX)描述了开发者在使用客户端库、SDK、框架、开源代码、工具、API、技术或任何服务时的体验。

DX 分享了一些来自用户体验(UX)设计的想法和哲学。

### 这有什么不同呢？

用一个终端命令部署一个 web 应用程序的机会表明了什么可以被认为是更好的开发人员体验，而不是遵循一个冗长的指令列表来实现相同的结果。

DX 是驱使开发者使用一个工具并坚持使用它的潜在因素之一。

这篇文章基于对开发者来说非常重要的四个因素比较了一些流行的跨平台移动框架的 DX:

*   设置
*   证明文件
*   热重装
*   排除故障

我们的比较将集中在 5 个跨平台框架上:

*   反应自然
*   摆动
*   Xamarin
*   离子的
*   原生脚本

## 入门指南

一个框架的建立是它的第一印象。入门容易是影响 DX first 关于框架工作流程的地方。

在这里，我们将比较不同移动框架的设置过程，忽略 Android Studio 和/或 XCode 设置，因为这基本上是所有这些框架的强制步骤。

**React Native** :使用 [Expo](https://expo.io/) ，React Native 极其容易上手。

安装 NodeJS 和 Expo 后，您只需键入一个命令，然后就可以开始了(无需安装 Android Studio 或 Xcode，假设您有一个安装了 Expo 应用程序的设备)。起始模板附带了一个漂亮而简单的界面来帮助指导开发人员。

您还可以使用 RN 命令行工具在没有 Expo 的情况下启动 React 本机应用程序。使用这种方法运行我们的 RN 应用程序并不需要太多的工作。CLI 工具可以帮助开发人员足够快速地获得他们的第一个屏幕应用程序。

只有当我们需要将 RN 集成到现有的本地应用程序或为[树外平台](https://reactnative.dev/docs/out-of-tree-platforms)构建时，开始使用 RN 才变得棘手。然而，这被认为是一种先进的做法，在这种比较中不应予以考虑。

React Native 的功课做得非常好，他们甚至发布了一个`react-native doctor`包来帮助验证你计算机上的设置。

安装 Flutter SDK 就像在你的电脑上安装一个软件包一样简单。他们的指南显示了易于遵循的四个简单步骤。即使这对你来说有点小问题，Flutter 也会帮你解决。

众所周知，他们非常关心 DX。他们将一个名为`flutter doctor`的工具集成到他们的 CLI 中，以帮助您了解在该框架的工具链中什么工作得好，什么工作得不好。

颤振工具也必须在您的代码编辑器中配置，以帮助提供更好的 DX。这说明了设置中的一些附加步骤。这些步骤是可选的，但它们有助于在互联网上获得最佳的颤振评论。

Flutter 的一切都让人感觉“开箱即用”只使用 Flutter SDK 附带的包，很容易构建一个更复杂的应用程序。

如果你是一个习惯于 Visual Studio IDE 的 C#开发人员，安装 Xamarin 肯定会像在公园散步一样。然而，如果你没有 Visual Studio，你可能会发现 VS2019 安装和 Xamarin 工具很乏味。

安装 Xamarin 后，您可以通过 Visual Studio 入门 GUI 启动应用程序。在这里，您只需选择移动应用程序，并从提供的模板中选择一个起始模板(主-详细信息、选项卡、外壳和空白)。)然后选择平台，单击 continue，等待它初始化。

**Ionic** :从 v1 开始，Ionic 就一直非常容易上手。现在我们到了 v5，这变得更加容易了。

它分 3 步进行:用 Node 安装 Ionic，运行并遵循`ionic start myApp <template-name>`的提示(他们为你提供了 3 个模板:空白、标签和侧菜单)，最后用`ionic serve`运行 app。

这里，默认的应用程序在你的浏览器中看起来像一个移动网站，但这几乎是应用程序构建后的样子。

同样重要的是注意到 Ionic 有 v5，一个[漂亮而直观的启动向导](https://ionicframework.com/start)，它帮助你只用一个命令行指令就能得到和上面一样的结果。

**NativeScript** :他们有两种入门方式:快速设置和完全设置。快速设置过程类似于 React Native with Expo——在物理设备上下载一个应用程序，然后 React Native 通过链接连接到开发终端，我们就可以开始开发我们的应用程序(无需安装 Android Studio 或 XCode)。

完整的设置也很容易，因为它们提供了一些链接，可以很好地与您的操作系统终端一起安装框架。此外，和 Flutter 一样，它们也有一个可以使用的诊断工具:`tns doctor`，它可以验证你计算机上的设置。

**注意** :
需要注意的是，大多数快速设置对工作流程都有一些限制。例如，对一些本机插件的访问受到限制，单元测试有时是不可能的(在 NativeScript 的情况下)，以及一些资源(闪屏、图标)是不可访问的。

## 证明文件

任何开发人员都可以承认，爱上一个工具有时是通过轻松地使用该工具的手册来实现的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

React Native :虽然 React Native 的文档最近有了很大的改进，但 React Native 的开发者很可能会从博客帖子、在线教程和课程等资源中接触到一些高级主题。

这似乎与从中级到高级的 React 本机主题要求比其核心 API 所建议的更多(状态管理、导航、动画等等)的事实有关。

**Flutter** : Flutter 的 docs 一定是五大框架中最爱的。他们用有组织的信息淹没它，以帮助任何人通过它们。从简单的命令到包含简短信息视频和小部件类的食谱，Flutter 的文档都非常好。我提到过小部件的类引用 API 可以很容易地从每个小部件的源代码中获得吗？

**Xamarin** : Xamarin 开发者说他们的文档“满了”，这是真的！在这五个文档中，Xamarin 有这样一个文档，当你需要了解框架的任何主题时，随时都可以找到它。

仅凭文档中的资料，你就可以从初级到高级。它的导航非常直观，包括简短的教程和关于主题和用例的开放问题的链接。

很明显，爱奥尼亚在他们的文档中做了一件惊人的工作。在每次重大更新时，整个网站都会为了更好的用户体验而改变。

大多数使用 Ionic 的开发人员发现搜索、查找和集成他们需要的组件很容易。

NativeScript :关于 NativeScript 有一点是，它比他们宣传的要好得多。NativeScript 的文档非常简单——它们需要更多的指南来向开发人员展示利用框架的能力是多么容易。

此外，当试图从初学者到老手时，我们觉得 NativeScript 的文档缺少一些清晰的例子来说明框架真正允许你做什么。大多数情况下，开发人员必须通过 GitHub 问题来理解一些基本概念。

## 热重装

这是移动跨平台框架中的一个热门话题，有时可以称为移动开发的一个省时部分。

**React Native** :在很长一段时间里，热重装是驱使开发者远离这种跨平台解决方案的弱点之一。然而，在 React Native 0.61 中，[他们宣布了快速刷新](https://reactnative.dev/blog/2019/09/18/version-0.61)，这是[不完全热重装](https://stackoverflow.com/a/59085605/5268119)，但也让 RN 开发者高兴。

快速刷新的了不起之处在于它完全支持现代的 React(函数组件和钩子)并优雅地处理错误。

颤振:颤振的热重装功能是一种乐趣。它非常好，flutter 提供了 [3 种不同的方式](https://flutter.dev/docs/development/tools/hot-reload)在工作时重新加载你的应用程序:热重新加载、热重启和完全重启。

有一些警告在他们的文档中得到了很好的解释和解释，但这些并没有阻止这个特性成为开发人员对 Flutter 的最佳卖点之一。

Xamarin :和其他 4 个框架一样，Xamarin 的热重载是开箱即用的。无需进一步配置，您就可以保存文件并触发刷新。Xamarin 的热重装可以同时在多个平台上工作，并使用一种叫做[弹性重装](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/xaml/hot-reload#resilient-reloading)的原理。

Xamarin 的热重新加载也有一些问题:它无法重新加载 C#代码，包括事件处理程序、自定义控件、页面代码隐藏和其他类。此外，您不能在热重新加载会话期间更改文件。要查看此更改，您需要重新构建并重新部署您的应用，以重新启用热重装

**Ionic**:[Ionic](https://ionicframework.com/docs/cli/livereload)中的 Live reload 可以从两个方面来看，因为 Ionic apps 可以用两种不同的方法来开发。人们可以使用浏览器编写应用程序并查看结果，也可以将手机连接到电脑上进行评估。然而，离子有一个完整的热重新加载功能，这将有助于您高效地开发您的应用程序，无论您选择的方法。

**NativeScript** :从 Native script 5.3 开始，[正式支持一个很棒的 HMR](https://www.nativescript.org/blog/nativescript-5.3-hot-module-replacement-officially-supported-and-more)——热模块替换特性，支持从 VSCode 调试、样式编辑、JavaScript 编辑甚至文件编辑。

当我们启动一个`run or debug on device`时运行的 [LiveSync 功能](https://docs.nativescript.org/sidekick/user-guide/run-app/livesync-changes)也可以像 Ionic 一样，对你的应用程序进行更改，并在所有连接的设备和仿真器上实时查看这些更改。

## 调试工具

开发人员有时会说“调试比一开始写代码难两倍。”既然这可以被认为是我们工作中最难的部分之一，为什么任何工具都要让它变得更难呢？

移动跨平台调试总是令人不安，因为这些工具不仅是混合的，而且可能并不总是本地兼容的。

在比较过程中，我们将结合框架中的调试工具和错误报告特性。

React Native :就像热重装一样，React Native 的调试一直很“粗略”，因为核心团队没有正式支持任何工具。

然而，在 React Native 0.62 中，他们[公布了 Flipper](https://reactnative.dev/blog/2020/03/26/version-0.62) ，这是自 3 月 26 日以来默认的 RN 调试器。Flipper 具有调试器所需的强大功能，特别是:

*   React 开发工具可用
*   网络、布局、数据库和偏好设置检查器
*   帮助重新加载应用程序的 Metro 操作
*   崩溃报告和设备日志。

**颤振** : [颤振开发工具](https://flutter.dev/docs/development/tools/devtools)是一个非常强大和完整的功能。它适用于几乎所有用例，从简单的控制台输出，到可视化小部件分析，再到网络和性能检查。

Flutter 可能是 5 个框架中调试能力最好的。

**Xamarin** : Xamarin 调试[安卓](https://docs.microsoft.com/en-us/xamarin/android/deploy-test/debugging/debug-on-device?tabs=windows)和 [iOS](https://docs.microsoft.com/en-us/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=windows) 应用的方式不同。Android 应用程序的调试和其他程序一样。NET 应用程序——在使用调试配置运行设备后，我们可以简单地添加断点并跟踪应用程序的执行。

Xamarin.iOS 使用 [Mono 软调试器](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)，生成的代码，Mono 运行时配合 IDE 提供调试体验。

**Ionic** :在他们的文档中，Ionic 提供了广泛的[调试工具](https://ionicframework.com/docs/troubleshooting/debugging)，因为框架创建了混合应用程序(web 视图中的 HTML、CSS 和 JavaScript ),使用 web 调试器可以轻松调试。

如果您希望查看更好的日志或更深入的调试，Cordova 提供了一个 VSCode 扩展，它将满足您几乎所有的需求。

**NativeScript** :通过命令`tns debug <platform>`，我们可以轻松地用 NativeScript 启动调试会话，并启动 Livesync。

然而，debug 的[输出会被打印到控制台中，并且您所做的所有更改都会与应用程序同步。然而，应该注意的是，](https://docs.nativescript.org/tooling/debugging/debugging) [VSCode 扩展](https://www.nativescript.org/nativescript-for-visual-studio-code)提供了更好的调试体验。

此外，这篇关于[调试 NativeScript 插件](https://www.nativescript.org/blog/a-guide-to-debugging-your-nativescript-plugins)的博客文章帮助开发人员更深入地使用 NativeScript 大纲构建和调试应用程序，它解释了开发人员如何调试他们使用的插件的原生源代码。

## 进一步比较

以上是吸引和留住开发人员使用他们的工具的主要因素。现在让我们看看那些让开发人员享受使用跨平台工具的时间的可选内容。

### 代码编辑器/IDE 集成

在上面的 5 个框架中，Flutter 听起来可能是最好的，因为它们为支持的不同编辑器提供了强大的插件和集成。

在 VSCode 上安装了 [Flutter 扩展后，它很快就开始感觉自己已经成为一个 IDE，拥有全面的调试支持、脚手架代码片段、漂亮的语法高亮显示和代码完成。](https://marketplace.visualstudio.com/items?itemName=Dart-Code.flutter)

虽然它没有 Flutter 的工具先进，但 React Native 也有微软的 React Native 工具，可以帮助快速轻松地运行和调试 React Native 应用程序。

Xamarin 很好，因为当你决定安装框架时，VS2019 为你提供了 Xamarin 开发所需的一切。这使得开发人员依赖于这个工具，因为他们可以从 IDE 中获得他们需要的一切。

有了 Ionic 和 NativeScript，你可以很好地使用大多数 web 开发编辑器，因为它们使用流行的前端工具，如 HTML、JSX、CSS、SASS、Angular、React 和 Vue，这些工具已经在现有编辑器上得到了很好的支持。

### 第三方模块

高级 React 原生应用严重依赖第三方模块，由于与其他模块或当前版本框架的兼容性，当更新缺失部分时，这可能很快成为一个难题。

然而，React 原生社区非常大，所以在 npm 上有很多开源包，它们对构建应用程序非常有帮助。

这个问题在 Flutter 和 Xamarin 中是一个小问题，因为他们已经准备好了我们构建跨平台应用程序所需的大部分内容，并且他们的更新过程由各自的 YAML 和 XML 文件很好地管理。

Flutter 有自己的[包管理器网站](https://pub.dev/)，你可以在那里搜索和查看 Flutter 包的使用示例。

Ionic 本身自带了构建 material 或 iOS UI 应用所需的大部分 UI 组件，他们尊重设计准则。然而，它强烈依赖 Cordova 的本机插件来访问本机功能，而这些功能可能不是最新的或缺乏文档。

NativeScript 和 Ionic 一样，捆绑了许多好看的 UI 组件。然而，NativeScript 的团队及其社区提供并开发了原生插件，并在专门的[市场](https://market.nativescript.org/plugins)上展示。

## 结论

所有这五个框架都有一个庞大的社区，并且已经被认为是主要的跨平台工具，因为它们在公司中被广泛使用。它们中的每一个都有独特的工作流，可以根据每个用例进行定制。

尽管如此，重要的是要了解每种方法的优点和局限性，并根据你正在处理的任务和团队进行选择。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)