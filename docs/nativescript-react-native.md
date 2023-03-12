# NativeScript 与 React Native 

> 原文：<https://blog.logrocket.com/nativescript-react-native/>

对于 JavaScript 开发人员来说，使用 JavaScript 构建跨平台的移动应用程序已经改变了游戏规则。开发人员不仅可以构建在网络上运行的应用，还可以为 Android 和 iOS 构建使用 JavaScript 运行的原生应用。

像 React Native 和 NativeScript 这样的框架使得用 JavaScript 构建优雅、高效、生产就绪和可伸缩的移动应用变得容易。但是，哪个对开发者更好呢？

在我们比较这两种技术之前，我们必须了解这两种技术独特解决的问题以及它们的前景。

## 什么是 NativeScript 和 React Native？

[NativeScript 的愿景](https://nativescript.org/)是“通过直接使用平台 API 解放你的开发，而不离开你对 JavaScript 的[热爱]。”在框架无关的团队中使用 JavaScript 构建跨平台移动应用程序时，可以将 NativeScript 视为首选工具。

因此，无论您选择的 JavaScript 框架是 Vue、React、Svelte、Angular 还是普通的 JavaScript，NativeScript 都能满足您的需求！

另一方面，React Native 是由脸书创建的 JavaScript 库，它使用 React 构建原生移动应用程序，这意味着我们无法使用 Vue、Angular 或任何其他 JavaScript 框架来构建 React Native 移动应用程序。

这并不意味着 App 或 Play store 上的所有 React 原生应用都是用纯 JavaScript 构建的，因为有些情况下，开发人员必须用 Swift/Objective-C 或 Java/Kotlin 编写原生代码，才能访问默认情况下 JavaScript 不可用的原生 API。

记住这一点，让我们来看看 React Native 与 NativeScript 相比如何，到本文结束时，你应该能够在用 JavaScript 构建移动应用程序时选择最适合你或你的团队的框架。

## 社区支持

在选择合适的框架时，社区支持是要考虑的最重要的因素之一。这似乎令人惊讶，但 NativeScript 比 React Native 存在的时间更长。 [NativeScript 最初发布于 2014 年](https://www.telerik.com/blogs/announcing-nativescript---cross-platform-framework-for-building-native-mobile-applications)，而 [React Native 最初发布于 2015 年 3 月 26 日](https://engineering.fb.com/2015/03/26/android/react-native-bringing-modern-web-techniques-to-mobile/)。

虽然一年的差异似乎是一个优势，但 React Native 的增长和采用率远高于 NativeScript。截至本发布日， [React Native 记录在 npm](https://www.npmjs.com/package/react-native) 上的周下载量约为 53.3 万次，而 [NativeScript 记录的周下载量不到 7 万次](https://www.npmjs.com/package/@nativescript/core)。

在贡献者方面， [React Native 在 GitHub](https://github.com/facebook/react-native/graphs/contributors) 上以超过 2200 名贡献者轻松胜出，而 [NativeScript 有 208 名贡献者](https://github.com/NativeScript/NativeScript/graphs/contributors)。并且， [NativeScript 被超过 3.5k 的开发者使用](https://github.com/NativeScript/NativeScript/network/dependents?package_id=UGFja2FnZS02NDc2NDYwNDc%3D)，而 [React Native 被 GitHub 上不到 720k 的开发者使用](https://github.com/facebook/react-native/network/dependents?package_id=UGFja2FnZS00OTM3MDY0OTA%3D)。

这种广泛的利润展示了开发人员社区对 React Native 的广泛采用，并影响了 StackOverflow 上第三方库、错误修复和答案的数量。

社区支持也很重要，因为它决定了可用于了解每个框架的资源数量，如博客帖子、付费和免费课程、电子书和 YouTube 视频。

谈到社区支持，React Native 认为这是最受欢迎的。

## 入门指南

### 设置 React 本地项目

对于刚接触移动开发的 web 开发人员来说，开始并设置一个 React Native 项目可能是一个挑战。然而，由于有了 [Expo，开始使用 React Native 变得轻而易举](https://blog.logrocket.com/getting-started-with-react-native-and-expo-sdk/)。

虽然 [Expo 确实有一些限制](https://docs.expo.dev/introduction/why-not-expo/#limitations-of-the-managed-workflow)，但是这些限制不会影响大多数使用 Expo 构建的应用程序。

开发者还可以使用由 Expo 团队开发的移动应用程序 [Expo Go，通过扫描二维码在物理设备上安装应用程序，在物理 Android 和 iOS 设备上运行 React Native。](https://expo.dev/client)

对于来自 React 背景的开发人员来说，开始使用 React Native 更容易。但是，如果您选择的主要 JavaScript 是 Angular，那么学习曲线可能会很陡。

### 设置 NativeScript 项目

在引导 NativeScript 应用程序时，使用[官方模板开始使用 NativeScript 相当容易。您可以通过在终端中运行以下命令，使用已经设置好的侧抽屉](https://github.com/NativeScript/nativescript-app-templates)来[初始化一个 NativeScript 应用程序:](https://docs.nativescript.org/introduction.html#drawer)

```
create myNsApp --template @nativescript/template-drawer-navigation

```

NativeScript 的伟大之处在于它是框架不可知的，这意味着您可以使用 Angular、Vue、Svelte、React 甚至 Vanilla JavaScript 等 JavaScript 框架使用 NativeScript 构建移动应用程序。

然而，NativeScript 比任何其他框架都更适合与 Angular 同步，因为 NativeScript 团队与 Google 合作，确保两种工具在构建移动应用程序时无缝协作。

通过使用 [NativeScript Playground 应用](https://play.google.com/store/apps/details?id=org.nativescript.play)，你可以在物理 Android 和 iOS 设备上运行你的应用，就像 Expo Go 一样。

总的来说，Expo Go 应用程序比 NativeScript playground 提供了更好的开发人员体验，因此这对于寻求更简单设置体验的开发人员来说是一个优势。

## 热重装

热重新加载功能在移动应用程序开发中非常重要，因为它极大地提高了开发人员的工作效率。没有一个开发人员愿意在开发阶段等待超过一分钟来查看他们的移动应用程序中的每一个小变化。

在这一节中，我们将看到 Native 和 NativeScript 如何处理热重载。

### 反应本机快速刷新

在 React Native 的【0.61 版本中，团队公布了一个名为快速刷新的功能。快速刷新统一了之前 React 原生版本中已经存在的实时重装功能和热重装功能。

启用快速刷新后，您可以对您的应用进行更改，并几乎即时看到更改。默认情况下，该功能在功能组件中保留本地状态，但在类组件中不保留。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

您还可以使用 React 本机组件中的`// @refresh reset`指令手动调用快速刷新，强制组件重新挂载。

### NativeScript 热模块更换

[NativeScript 的热模块替换(HMR)特性在 NativeScript 5.3](https://nativescript.org/blog/nativescript-5.3-hot-module-replacement-officially-supported-and-more/) 中发布。它支持 Visual Studio 代码调试，允许开发人员甚至在 HMR 功能仍在运行时使用调试器。

它还完全支持 SASS 文件，这意味着当你在应用程序中的`.scss`或`.sass`文件中进行更改时，它们会在开发过程中反映在你的应用程序中；然后，HMR 可以在 NativeScript 应用程序目录中添加或删除文件。

也支持在`.vue`文件中修改脚本，但是在 Vue 中使用 TypeScript 时有一个[限制，并且在开箱即用的 NativeScript Angular 项目中有完全的 HMR 支持，不需要额外的配置！](https://github.com/nativescript-vue/nativescript-vue/issues/518)

HMR 和快速刷新的性能相似，并且都智能地构建了一些功能，如在 NativeScript 中调试 Visual Studio 代码和在 React Native 中调试[错误恢复能力。](https://reactnative.dev/docs/fast-refresh#error-resilience)

## 本机 API

### 反应本机 API

React Native 附带了一些基本的开箱即用模块，但在某些情况下，您可能需要 React Native 还没有相应模块的本机功能。在这种情况下，您必须编写本机模块来与本机 API 通信。

实现这个[需要特殊的桥梁](https://reactnative.dev/docs/communication-ios#calling-native-functions-from-react-native-native-modules)，这需要一些本地开发方面的专业知识。然而，以 React Native 生态系统的规模，你很可能会在 GitHub 或 npm 上找到一个现有的原生模块。

### NativeScript APIs

在 NativeScript 中访问[原生平台 API 是 NativeScript](https://docs.nativescript.org/native-api-access.html) 最重要的特性。开发者可以从 JavaScript 调用平台 API，而不是用 Objective-C 或 Kotlin 编写原生代码。

这就是 NativeScript 优于 React Native 的地方，因为 JavaScript 开发人员不需要特定平台语言的专业知识就可以使用本机 API。

## 反应本机和本机脚本性能

NativeScript 应用程序通常较慢，因为该框架采用单线程方法来访问本机设备 API。

与 NativeScript 不同，React Native apps 使用多线程模型，JavaScript 和应用程序的接口在不同的线程上运行。因此，当构建一个性能是关键因素的移动应用程序时，React Native 是您的最佳选择。

React Native 还提供了更快的 UI 渲染，因为它使用虚拟 DOM 来更新应用程序 UI，类似于 React。

## Native 编写和反应本机文档

NativeScript 的文档很容易理解，其中有一些非常棒的插图，介绍了如何实现诸如[动画](https://docs.nativescript.org/interaction.html#animations)、[手势](https://docs.nativescript.org/interaction.html#gestures)、 [HTTP 请求](https://docs.nativescript.org/Http.html)和[代码共享](https://docs.nativescript.org/code-sharing/index.html)之类的东西。

并且，对于带有[svelet](https://svelte-native.technology/docs)和 [Vue](https://nativescript-vue.org/) 的 NativeScript 的具体用例，有单独的文档。但大多数时候，开发人员必须了解官方文档之外的 NativeScript 生态系统中的事情是如何工作的。

React Native 拥有比 NativeScript 更成熟的生态系统，随之而来的是对[文档](https://reactnative.dev/)的期望，因为跨平台移动应用开发的初学者和专家都可以访问它。

然而，虽然文档是平易近人的，但它缺乏细节，这可能会使初学者对一些概念不清楚。

[使用 Expo 构建 React 原生应用并使用 Expo 文档](https://expo.io/)简化了初学者使用 React 原生文档构建功能性 React 原生应用的过程。

两者都没有最好的文档，使用这两种框架的开发人员通常必须超越官方文档来学习框架的所有方面。

## 使用 NativeScript 和 React Native 的公司

React Native 目前被顶级品牌和公司用于生产，如[脸书](https://code.facebook.com/posts/895897210527114/dive-into-react-native-performance/)、[彭博](https://www.techatbloomberg.com/blog/bloomberg-used-react-native-develop-new-consumer-app/)、[比特币基地](https://blog.coinbase.com/optimizing-react-native-7e7bf7ac3a34)、[不和谐](https://blog.discord.com/how-discord-achieves-native-ios-performance-with-react-native-390c84dcd502)、[特斯拉等](https://reactnative.dev/showcase)。这证明了 React Native 已经变得多么的大，多么的受欢迎。

[脸书市场也是用 React Native](https://newsroom.fb.com/news/2016/10/introducing-marketplace-buy-and-sell-with-your-local-community/) 构建的，证明如果你有一个用本地语言编写的现有移动应用，你可以逐步采用 React Native 来构建你的移动应用的不同部分。

尽管 React Native 在这里备受关注，但这并不意味着 NativeScript 不能解决 React Native 为使用它的公司带来的业务问题。看看这里用 NativeScript 构建的[令人惊叹的应用](https://github.com/jbristowe/awesome-nativescript)就知道它可以构建生产就绪的移动应用。

## 结论

作为 web 开发人员，使用 JavaScript 构建移动应用程序是一种非常棒的体验，因为这种语言可以在 web 浏览器上运行，为 Android 和 iOS 构建原生应用程序。在所有用于构建跨平台应用的 JavaScript 框架中，React Native 和 NativeScript 居于首位。

在本帖中，我们讨论了每个框架的独特之处以及它们的发展历程。选择哪种框架最终取决于移动应用的需求、性能成本、首选 JavaScript 框架、移动应用开发的先验知识以及所需的本机 API。

从以上角度来看，当您为下一个项目选择跨平台移动应用程序框架时，您应该能够做出明智的决策。

请随意发表评论，让我知道你对这篇文章的看法。你也可以在 [Twitter](https://twitter.com/ejirocodes) 和 [LinkedIn](https://www.linkedin.com/in/ejiro-asiuwhu/) 上找到我。感谢您的阅读！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)