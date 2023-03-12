# 你应该保持对当地人的信任吗？

> 原文：<https://blog.logrocket.com/should-you-keep-faith-react-native/>

> 编者按:这篇文章于 2021 年 11 月 30 日更新，以反映 React Native 的更新。

2015 年初，脸书首次推出 React Native，这是一个使用 React 库构建原生应用的框架。自首次发布以来，React Native 已经成为许多开发人员最喜欢的可靠框架，被 Pinterest、优步、Spotify 和微软等科技巨头使用。另一方面，React Native 一直受到一些质疑，例如，当 Airbnb 在 2018 年取消 React Native 的开发工作时，引发了 React Native 是否实际上已经死亡的问题。

在本文中，我们将回顾一下 React Native 的一些特性，这些特性平息了这个谣言。我们将回顾开发人员应该学习 React Native 的许多原因，包括它最近的一些新增功能和未来的计划。我们开始吧！

目录:

## 为什么要选择 React Native？

让我们来看看开发人员选择 React Native 来构建他们的下一个移动应用程序或在现有移动应用程序中添加新功能的主要原因。

### React 的跨平台组件

如今，快节奏的发展并没有为科技公司提供实质性的优势，相反，它对公司的生存至关重要。巨大的竞争要求公司以更快的速度和更大的稳定性前进。

让我们想象一下，一家公司正在管理三个不同的前端团队，Android、iOS 和 web，致力于一个产品。这种方法不仅从金钱的角度来看成本很高，还会阻碍产品的发展。React Native 允许团队在 Android 和 iOS 上共享高达 95%的代码，这在敏捷开发时代是一个巨大的优势。

也有可能与 web 团队共享 React 原生代码库，我们将在下一节回顾这一点。

### React 的声明式用户界面

毫无疑问，声明式 UI 和基于组件的架构彻底改变了前端开发。这些特性允许前端开发人员构建更具可预测性和更少 bug 的大规模应用程序。

从一开始，React 的座右铭就是“学习一次，在任何地方写作”我们可以在开发移动应用程序时利用这些概念，使 React Native 成为 React 整体进步和发展的重要元素。

1.  移动应用程序中更可预测的用户界面，错误更少
2.  如果开发人员已经熟悉 React，他们可以为移动应用程序做出贡献
3.  如果考虑到构建，[代码可以跨 React 渲染器](https://buttercms.com/blog/react-best-practices-maintaining-large-scale-projects)重用，包括 web、移动、桌面、Unity 和 VR

### React 的可靠性:大规模测试

React 和 React Native 都是开源的，这意味着它们首先会在脸书的生产应用程序中进行测试，这使得其他公司可以更容易地将其应用程序升级到新版本。

尽管对任何框架或构建工具的更新对于大规模的应用程序来说都不容易实现，但如果这种升级已经在脸书的规模上进行了测试，团队应该对在自己的应用程序中实现更改充满信心。

### 被主要科技巨头采用

除了在脸书被广泛使用之外，React Native 还被几家科技巨头采用。这个列表包括微软，它使用 React Native 作为其各种产品，包括 Greenfield 和 Brownfield。电子商务巨头沃尔玛和 Shopify 正在依赖 React Native 开发他们的移动应用程序。请务必在今天的产品中查看更多使用 React Native 的[应用。](https://reactnative.dev/showcase)

### 通用:将应用部署到任何平台

官方 React Native 项目主要针对 Android 和 iOS 移动平台。然而，React 渲染器架构是灵活且可扩展的。例如，微软分叉了主要的 React 原生项目，并将其扩展用于 [Windows 和 macOS](https://microsoft.github.io/react-native-windows/) 桌面平台。此外，许多社区派生扩展了 React Native for Linux。因此，您可以将 React 本地应用轻松移植到任何桌面平台。

正如我们所知，颤振是一个普遍的框架。由于这些社区分支，我们可以说 React Native 也是一个通用框架，这使得 React Native 与 Flutter 相比更具竞争力。

## 对本地人的进度做出反应

让我们看看 React Native 的一些新特性，它们对 React Native 项目的性能、调试和管理产生了真正的影响。

### 更好的 JavaScript 运行时:Hermes

2019 年年中，脸书发布了针对 React Native 优化的 JavaScript 引擎 [Hermes。赫尔墨斯带着](https://github.com/facebook/hermes) [v0.64](https://github.com/facebook/react-native/releases/tag/v0.64.0) 版本来到 iOS。在撰写本文时，React 原生团队正计划将 Hermes 作为默认的 JavaScript 引擎，因为它的性能有所提高。

Hermes 不仅帮助优化了 React 原生应用的性能，还提高了应用的可用性，减小了应用包的大小，并优化了电池的使用。

移动用户社区仍然使用低端和中端设备，Hermes 通过提供更小的应用捆绑包和整体接近原生的性能来支持这些设备。Hermes 提供了超前(AOT)编译，取代了以前的即时编译(JIT)，提高了应用程序的交互时间(TTI)因素。

对于 React Native 的性能，尤其是在低端 Android 设备上的性能，一直有很多批评。作为一个基于 JavaScript 的框架，React Native 非常依赖 JavaScript 引擎。在早期版本中，React Native 在 Android 上使用 WebKit JSC，经过分析，发现这是启动性能的主要因素。

Hermes 用 AOT 编译将 JavaScript 预编译成高效的字节码，而不是用 JIT 编译来编译 JavaScript。反过来，使用 Hermes，React 本机应用程序的启动时间更快，这也最小化了 APK 大小并优化了运行时。

### 快速刷新和翻转带来更好的调试体验

虽然 React 以其出色的开发人员体验而闻名，但调试一直是 React 原生开发人员的一个痛点。尽管 React Native 最初包含了热模块替换和热重装，但在很多情况下，它都崩溃了。作为一个解决方案，Dan Abramov 为 React Native 开发了[快速刷新](https://reactnative.dev/blog/2019/09/18/version-0.61)，这在很大程度上减少了这个痛点。

Flipper 是我个人最喜欢的功能之一，它改变了我们调试本地应用的方式。Flipper 在快速刷新后立即发布了一个版本，即 v0.62，该版本还支持 React DevTools v4.x，这意味着我们也可以通过 React Native 利用它的强大功能。

### 更好的管理:更轻、更易管理

React 原生核心团队启动了一个项目，旨在移除所有非必要元素，并将它们放在一个单独的 repo 中，例如 WebView 等 UI 组件和推送通知等原生模块。这些回购大多在 [react-native-community](https://github.com/react-native-community) 下。

移除这些组件和模块将允许核心团队更快地移动，使 React Native core 更轻便、更易于管理。

分析应用性能的一个重要方面是它对手势、滚动和动画的响应。React Native 社区为处理这一点引入了很多强大的模块，包括 [react-native-reanimated](https://github.com/software-mansion/react-native-reanimated) 、 [react-native-screens](https://github.com/software-mansion/react-native-screens) 、[React-Native-gesture-handler](https://github.com/software-mansion/react-native-gesture-handler)和 [RecyclerListView](https://github.com/Flipkart/recyclerlistview) 。此外，React Navigation 和 React Native Navigation 正在努力改善开发人员的体验，并使应用程序中的导航更加流畅。

## React Native 的下一步是什么？

在我们对 React Native 未来的评估中，最重要的因素是脸书对图书馆未来的计划。2020 年，脸书为 React Native 规划了一个全新的架构，确定了它在未来几年的外观。

React Native 团队正在一点一点地构建这个架构，其中一些已经在 React Native 的当前版本中可用，包括 Hermes。此外，还有一些已经在脸书内部测试过了，很快就可以在社区中使用了。React 本地核心团队将整个改造分为三个阶段:

1.  本机模块
2.  新渲染器
3.  更好的初始化

我们将在下面简要讨论这些阶段。

### 原生模块:2020 年初

从一开始，React Native 就在应用启动时初始化所有本机模块。这可能包括本机模块，对于某些用户来说，这些模块有时可能不是必需的。例如，react-native-device-info 模块可能只对一个很少使用的特性是必要的，但是我们无论如何都必须在开始时初始化这个模块，因此增加了我们的启动时间。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

Turbo 模块允许我们在使用本机模块时加载它们。这将减少启动时的初始化开销，从而提高启动性能。它还跨 JavaScript 和本机代码增加了更好的类型安全。这已经在脸书的生产应用中测试过了。

### 新渲染器:2020 年中期

我们听说这被称为 Fabric，是 React Native 的 UI 层的 C++重写。由于新的渲染器是线程安全的，它不需要在专用线程中完成测量和布局效果。这将修复很多严重依赖 measure API 的组件的 bug，包括`ScrollView`和`TextInput`。

新的渲染器还提供了改进的类型安全。此外，所有这些 UI 组件都是延迟初始化的，就像本机模块一样，从而加快了启动时间。脸书的应用程序也在测试中。

### 初始化:2020 年末

今天，React Native 基于一个桥:JavaScript 和本机代码之间的所有通信都是通过桥完成的，所有这些都是异步的、批处理的和序列化的。这意味着所有这些通信都是通过回调或承诺来完成的，并且在序列化之后在它们之间传递值。这使得它是线程安全和可靠的，但是增加了很多开销。

新的建筑不依赖于桥梁；相反，它将使用新的 JavaScript 接口(JSI)。JSI 是一个独立于引擎的 API，允许 JavaScript 与 C++交互。它创建 JavaScript 运行时，加载 JavaScript 包，并允许我们从 JavaScript 调用 C++方法。

它还可以从 C++访问和操作 JavaScript 内存。通过直接使用 JSI 而不依赖网桥，我们可以消除与网桥相关的所有开销。展望未来，所有本机模块和视图管理器都将依赖 JSI，从而使它们变得非常快。

为执行 JavaScript 代码准备运行时的大部分初始化都将在 C++中完成，因此 JavaScript 初始化也将更具性能和优化。

毫无疑问，这种新的架构将使 RN 在性能和行为方面更加接近本地框架。更重要的是，这将使 RN 应用对最终用户更好。这也将使 RN 受益于 React 的精彩特性，比如并发模式。

## 新架构状态和版本

在撰写本文时，新的架构更新已经在脸书的大部分生产应用中进行了测试。脸书的应用程序现在已经完全迁移到新的 React 本地架构上。正如我们所知，没有深入的测试，一个工程团队就不能发布一个大的特性，架构的改变，或者重构的代码。此外，由于正在进行的疫情，React Native 的新架构实现已被推迟。

幸运的是，React 原生团队正计划不久后发布新的架构变更。他们正与 Expo 和 Callstack 密切合作，验证集成新架构更新的可行性。此外， [React 本地团队正在准备针对新架构的文档](https://www.youtube.com/watch?v=M-Fl6_vKcWw&t=2214s)和教程。

虽然新的架构还没有在 v0.67 候选版本中提到，但是我们很快就能看到它了。然而，新的架构已经发布到 GitHub，你可以用 [RNTester](https://github.com/facebook/react-native/tree/main/packages/rn-tester) 应用测试一个支持 Fabric 的体验。

## 结论

我在 Greenfield 和 Brownfield 应用程序中使用 React Native 的体验非常棒。看看 React Native 中包含的令人敬畏的特性，现在是成为 React Native 开发人员的最佳时机。

我很乐意讨论你对澳洲人未来的看法。欢迎在下面的评论中分享。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)