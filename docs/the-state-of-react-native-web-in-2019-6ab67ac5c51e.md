# React Native for Web-LogRocket Blog 的状态

> 原文：<https://blog.logrocket.com/the-state-of-react-native-web-in-2019-6ab67ac5c51e/>

[React Native for Web](https://www.npmjs.com/package/react-native-web) 是我最近偶然发现的最惊人的想法之一。对于 UI 开发人员来说，它让一个长期的梦想成为现实:能够用一个代码库创建一个既能在手机上运行又能在浏览器上运行的应用程序。

我们达到这一点的途径也非常有趣:

*   首先，React 的出现改变了我们创建 web 应用的方式
*   接下来，React Native 发布，吸取了 React 的所有优点，并提供给 iOS 和 Android 应用程序。React Native 最初是为了在移动平台上工作而创建的，它在很大程度上受本地应用程序开发方式的影响。尽管如此，它保留了一些让它感觉更接近 web 开发的关键元素:JavaScript、JSX 标记和 Flexbox
*   最后，React Native for Web 的创建是为了让这些 React Native 应用程序再次在浏览器中运行

## 将本机作为通用 UI 语言

上面最后一步的必要性一开始有点不清楚。我们已经有了 React，它最初是为了构建 web 应用程序而创建的。为什么我们要使用其他不符合明确目的的东西呢？

第一个原因是 React Native 使用 React 的一种子集来生成 UI。如果我们想要一个既能在手机上运行又能在网络上运行的代码，我们应该坚持使用限制性更强的代码；在这种情况下，它是 React Native。只要我们不使用需要一些原生功能的模块，React 原生应用应该可以通过 React Native for Web 开箱即用地在浏览器中工作。

第二个原因——也是真正让 React Native 在创建通用应用方面优于 React 的原因——是 React Native 是一种纯 UI 语言。它定义了一些定义 UI 原语的基本组件，这些组件被认为独立于运行它们的平台。我们可以在 React Native 中创建的所有组件都基于像`<View>`、`<Text>`或`<Image>`这样的原语，这些原语是对任何可视界面都有意义的基本元素，无论它在哪里运行。

另一方面，React 的原语只是 DOM 节点——像`<div>`、`<p>`或`<a>`这样的 HTML 标签，并不是纯粹的 UI。它们不是被创造出来定义一种视觉语言的；相反，它们是用来构建和理解超文本的。React 的原语具有界面之外的意义，这种意义在浏览器之外没有多大意义。

尽管如此，通过使用 HTML 标签将 React Native 原语翻译成 DOM 语言是可能的——这就是 React Native for Web 为我们所做的(以及更多)。

至此，我们已经理解了为什么使用 React Native 作为 web 和 Native 的通用语言是一个好主意。我相信这是正确的选择，但是 React Native for Web 已经存在了一段时间，它仍然在努力传播，尤其是在 Web 开发人员中。

## 一个平台两个生态系统

使用 React Native 创建 web 应用并不简单，这不仅仅是因为我们需要适应它的限制——这就像 Native 和 web 仍然是两个独立的世界。即使已经有可能使用一个代码库并在任何地方运行它，该代码也充满了可以在一个环境中运行而不能在另一个环境中运行的条件。

我们可以用来开发 React Native for Web 应用程序的库也是如此。

### React 本地库

一方面，我们有 React 本地库。我们应该能够将这些库毫无问题地插入到我们的 React Native for Web 项目中(除非它们运行本机代码或具有本机依赖性)。

不幸的是，对于我们的通用应用程序来说，性能曾经是 React 原生开发者的主要关注点——当渲染在原生端进行时，我们在移动端实现了最佳的 UI 性能。这样，我们可以在 JavaScript 线程中进行计算，而不会影响接口的响应。那些带有本机代码的库不能在 React Native for Web 项目中使用。

如果 React 原生库开发人员尽可能在他们的项目中附带一个替代原生功能的 JavaScript，那就太好了。如果本机代码在浏览器中没有多大意义，React 本机库应该提供一个在 web 环境中运行时不会中断的本机部分的模拟。

即使我们找到了一个与 web 兼容的 React 原生库，让它在我们的 web 应用程序中工作的过程也并不简单。为了构建我们的 web 应用程序，我们使用 webpack 作为捆绑器(稍后会有更多关于这个主题的内容)，它通常不会传输我们的`node_modules`文件夹中的文件。React 原生库不需要捆绑来为移动设备工作，所以[我们需要将异常](https://github.com/callstack/react-native-paper/issues/689)添加到我们的 webpack 的模块配置中，以便将它们捆绑在一起。

如果您是 React 本地库开发人员，请🙏，表现出对 React Native for Web 的热爱，并在您的库中发布一个 transpiled 版本。如今有一些工具可以让它变得非常简单，比如微捆绑或者 T2 毕丽。

### 反应库

另一方面，我们有 React 库，它通常被认为可以在浏览器中工作。他们利用 HTML 标签来构造 UI；因此，如果我们在我们的通用应用程序中使用它们，它们将破坏移动版本。

如果你是一个 React 库开发者，并且你认为你的库对移动环境有意义，你应该知道通过使用与 React Native 相同的原语通过 [react-primitives](https://github.com/lelandrichardson/react-primitives) 也可以使它在 React Native 中工作。

开始时，进入流程有点乏味，但是当你习惯了原语和 Flexbox，它甚至可以帮助你改进代码的组织。此外，您将学习 React Native，这在您的工具箱中非常方便。

## 使用 React Native 构建通用应用程序

创建通用应用程序时，我们必须忍受的另一个痛苦是管理多个捆绑器。在 React Native 中开发和构建应用的标准方法是使用 [Metro bundler](https://facebook.github.io/metro/) 。由脸书制造的 Metro 允许我们在本地构建和测试应用，几乎不需要配置。当您使用 [Expo](https://expo.io/) 创建一个 React 本地应用程序时，它就会发布。

当我们想要创建我们的应用程序的 web 版本时，React Native for Web 推荐使用 webpack 作为捆绑器。不要误解我——web pack 很棒，但是需要两个不同的构建系统和两种不同类型的配置让我很头疼。

我希望将来我们可以只用一台捆扎机。使用 Metro 来构建我们的 web 应用程序是可能的；我一直在尝试使用 Metro 捆绑我的 web 应用程序，这看起来完全可能。我可以创建一个 JavaScript 包，但是我的 web 应用程序并没有真正工作，因为一些我不想调查的 transpilation 问题。有人想试试吗？

## 融合移动和网络世界

我一直在谈论 React Native 和 [ReactDOM](https://www.npmjs.com/package/react-dom) 开发人员如何调整他们的库，使它们彼此友好，这应该只是第一步。

理想情况下，我们会将两个世界统一成一个通用的开发者社区。这不仅仅是使库在任何环境下都不会崩溃；更确切地说，它是在创造满足两个世界需求的工具，并为它们增加价值。

如前所述，React Native for Web 应用确实受到了移动应用最佳实践的影响。目前，当我们创建一个 React Native for Web 应用程序时，我们或多或少会让移动应用程序适应浏览器。但是，我们真的想要一个拥有全幅布局元素和可折叠抽屉、运行在大屏幕桌面浏览器中的手机应用吗？大概不会。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这就是为什么响应式布局革命在 2000 年代中期开始:让我们的设计适应不同的屏幕尺寸。这现在是任何 web 项目的一个基本特性。在移动应用中，响应式用户界面并不常见。但是，构建适应手机、平板电脑和桌面的应用程序而不需要编写两次代码不是很棒吗？

响应式布局只是 web 概念的一个例子，它可以为移动开发增加价值。另一个好方法是 URL。我们不需要在我们的移动应用程序中使用 URL，但是为我们的屏幕分配一个唯一的标识符并直接访问某个点的概念非常强大。这在移动应用程序中也不常见，这就是为什么在 React Native for Web 应用程序中处理 URL 相当困难，而这应该是非常基本的。

React Native for Web 尽了很大的努力将移动应用到 Web 开发中，但是在其他方面还没有什么进展。总的来说，我们应该开始纯粹从 UI 的角度来考虑问题，不管我们面对的是什么平台——我们还没有到那一步。

很难在 React Native 中定义悬停交互，因为当它被创建时，我们认为它们是不必要的。出于同样的原因，浏览器中的手势交互也不是小菜一碟。所有这些情况都应该被一种旨在成为定义 UIs 的通用方法的语言轻松涵盖。

## 欢迎来到新世界

创建通用应用程序是一个梦想成真，但就像大多数刚刚起步的事情一样，要让每个人都感到轻松，还有很多工作要做。

这是一个尚未探索的世界。作为一名库开发人员，我认为这是我们构建未来社区将使用的下一组工具和库的大好机会。你不想成为“下一个 Redux”的创造者吗？

我想特别呼吁世博团队为世博环境增加对 React Native for Web 的支持，因为我认为他们可以在过渡中发挥重要作用。最著名的移动开发平台推出现成的 web 包，这将是对通用应用程序的巨大推动。

让 Metro 为 web 服务将为 Expo(那里有一个巨大的社区)打开 web 开发的大门，但是教育 React 本地开发人员嘲笑他们在 web 上工作的本地依赖性将保证您的项目在您希望的时候退出。对每个人来说都是双赢的局面！

## 使用 React Native 创建一个通用的 app 值得吗？

肯定是的！尝试在所有平台上运行同一个项目当然仍然具有挑战性，并且还有许多空白需要填补。但是我们已经可以使用 React Native 作为一种语言来为我们的应用程序的不同版本创建 UI，即使它们针对不同的环境，也可以重用大部分相同的代码。

如果你想帮助解决上面的一些问题，你将推动通用应用程序开发的梦想，整个社区都会感谢你。

如果你只是想最大化你的代码可重用性，建立一个 [Lerna 项目](https://lerna.js.org/)，将你的应用程序的移动和网络版本作为单独的包，并开始使用 React Native 创建你的组件。您将重用整个 UI，但是您将能够单独处理特定于平台的问题——无需担心兼容性。

如果您对这种方法感兴趣，我强烈推荐您阅读来自 [Lucas McGartland](https://medium.com/u/2b3150c688d0) 的文章 [*用通用组件库*](https://hackernoon.com/building-cross-platform-applications-with-a-universal-component-library-e6292ca9a15) 构建跨平台应用程序。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)