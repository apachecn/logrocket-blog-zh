# React 导航与 React 原生导航:哪个适合你？

> 原文：<https://blog.logrocket.com/react-navigation-vs-react-native-navigation/>

***编者按:**本文于 2021 年 9 月更新，旨在解决 React 导航和 React 原生导航中关于 API、状态管理和 app 架构的问题。这篇文章包含了作者的个人观点，这些观点是建立在使用 React 本地应用的多个导航库的经验之上的。*

当使用 React Native 开发移动应用程序时，最重要的步骤之一是为您的项目选择完美的导航库。导航是应用程序的支柱，对用户体验有很大的影响。

没有任何单一的解决方案可以满足所有需求。有几个导航库可供我们选择。在这里，我们将比较 React Navigation 和其著名的替代产品 React Native Navigation (RNN ),权衡为您的应用程序选择导航库的关键要素。

## 什么是 React 导航？

React Navigation 是使用最广泛、开发最活跃的库之一。也是 React 原生团队推荐的[方案之一。这是脸书推动最多的社区解决方案。](https://facebook.github.io/react-native/docs/navigation)

让我们分析一下 React 导航盛行的场景，以及一些不盛行的场景。在后一种情况下，我们将尝试分析你需要多少复杂性来实现你想要的特性，或者在这些情况下 RNN 会有多好。

## React 导航与 React 本机导航的性能比较

TL；DR: React 导航使用库的组合来提供接近本地的体验，而 RNN 为每个屏幕使用本地片段。

为应用选择导航库时，性能是首要参数。每秒显示的帧数决定了界面的流畅度。大多数高性能应用程序总是保持 60fps，尤其是在交互和过渡期间。

该基准留给我们大约 16.67 毫秒来完成生成下一帧所需的所有工作。如果我们错过了这个窗口，我们将会丢失一帧，这会使我们的用户界面看起来没有反应和笨拙。

在我们的 React 原生应用中，大多数业务逻辑驻留在 JS 线程上，包括 API 调用、状态管理和触摸事件的处理。React 导航与应用程序的其他部分完全在同一个 JavaScript 线程上工作。

当您推送一个新的路由时，JS 线程需要在屏幕上呈现组件，以便将正确的命令发送到本机端来创建后备视图。这可能会导致具有大量屏幕和复杂界面的应用程序出现性能瓶颈。

React Navigation 依靠[引擎罩下的 react-native-screens](https://github.com/software-mansion/react-native-screens) 为其所有的导航器提供接近本地的体验。他们还拥有[原生堆栈导航器](https://reactnavigation.org/docs/native-stack-navigator/)，该导航器在 iOS 上使用原生 API`UINavigationController`，在 Android 上使用原生 API`Fragment`，因此导航具有与基于这些 API 原生构建的应用相同的性能特征。

与[基于 JS 的堆栈导航器](https://reactnavigation.org/docs/stack-navigator/)相比，这是以更少的定制为代价的。所有的动画都是使用内置导航器中的原生驱动完成的。除此之外，React 导航使用[React-native-realized v2](https://github.com/software-mansion/react-native-reanimated)来提高手势释放的性能。

对于手势处理，[react-native-gesture-handler 将手势从 JS 线程卸载到主线程](https://blog.logrocket.com/react-native-gesture-handler-swipe-long-press-and-more/)。Tab navigator 使用 [react-native-tab-view](https://github.com/satya164/react-native-tab-view) 引擎盖下；它利用[反应本地页面视图](https://github.com/callstack/react-native-pager-view)而不是复活和手势处理器。

这将提供本机 UX 并提高性能。这种库的组合有助于我们利用原生线程的所有优势，同时保持我们的导航栈更加以 JS 为中心。

与 React 导航相比，RNN 在默认情况下会将你的所有屏幕都视为单独的 React 应用，这使得你的应用本质上更具性能。然而，这也带来了一些复杂性——尤其是在与像 Redux 和 react-intl 这样的库集成时，这些库要求您包装您的应用程序。我们将在稍后的集成比较中讨论这一点。但对于大规模应用程序，性能提升可以取代这一点。

## React 导航中的 API 与 React 本机导航

TL；DR: React Navigation 有一个带有内置钩子的声明式 API，而 React Native Navigation 有带有钩子社区库的命令式 API。

React 改变了我们对界面开发的看法。今天的开发人员寻找更多的声明式 API，而不是命令式 API。React Hooks 在这个概念的基础上又加了一点冰。在 React 导航和 React 本机导航之间，前者提供的 API 更接近 React 组件 API，更具声明性，并且更容易被 React 开发人员采用。虽然 RNN 依赖于命令式 API，但这可能是他们的架构所必需的。至于钩子， [RNN 有一个社区库](https://github.com/underscopeio/react-native-navigation-hooks)，而 React Navigation 有内置钩子。

## 状态管理和应用架构

TL；DR:使用 JS 状态管理库时，React 导航更加简单。RNN 可能需要一些开箱即用的开发来集成这些库，[好的文档](https://wix.github.io/react-native-navigation/docs/third-party-react-context)弥补了这一点。

在使用 RN 应用程序时，我们通常使用不同的解决方案来管理应用程序的状态。我们可以使用 Redux、MobX、React Query、React Context、XState 或任何其他库。因为它以 JS 为中心，React Navigation 不需要太多努力就可以将这些解决方案集成到应用程序中。然而，RNN 为每个屏幕创建了新的片段，[需要一些变通方法来保持 JS 上下文在屏幕间的完整性](https://wix.github.io/react-native-navigation/docs/third-party-react-context)。这种变通方法对于来自 web 背景的开发人员来说可能有点痛苦，但是可以为大规模应用程序提供惊人的能力。

## 在 React 导航和 React 本地导航中使用多个路由器

TL；DR: React Navigation 基于组件的 API 允许 React 风格的路由器隔离，而 RNN 使用`setRoot`在路由器之间移动。

在需要登录的应用程序中，开发人员通常喜欢不同的导航器来分别处理非登录和登录过程。使用 React Navigation，这很容易实现，因为它是一个基于 JS 的导航器，并且提供了令人敬畏的 API。使用多台路由器时，[请查看 React Navigation](https://reactnavigation.org/docs/en/common-mistakes.html#explicitly-rendering-more-than-one-navigator) 提供的指南，以避免任何麻烦。

相反，React Native Navigation 允许您从应用程序中的任何位置设置导航的根，模拟您可以启动新活动或片段并清除现有堆栈的本机 API。

## 与其他第三方库集成

TL；DR: React 导航不太触及 RN 的核心架构，集成起来更简单。对于 RNN 来说，将 RNN 与第三方库集成在一起可能有点困难，但是他们完整的文档涵盖了如何做。

作为一个基于 JavaScript 的导航器，React Navigation 可以与任何第三方库顺利集成，而 RNN 可能会受到与原生平台紧密耦合或需要包装在整个应用程序周围的库的影响。例如，RNN 为集成像 React Native 脸书 SDK 这样的包提供了单独的指南。对于更多依赖原生库的应用程序来说，这可能是一个挑战，也可能是一个福音。

## 与现有应用的集成

TL；DR: React 导航是棕色地带应用的英雄。RNN 有一些这样做的技巧，但是图书馆不支持。

React Native 的主要优势之一是它能够与您现有的任何本机应用程序集成。在这种情况下，与现有的原生解决方案相比，任何基于 JS 的导航器都工作得很好。这使得 [React Navigation 比 RNN](https://github.com/wix/react-native-navigation/issues/1234) 更有优势，尤其是如果你希望用 React Native 为你现有应用的几个模块提供动力，就像脸书和许多其他棕色地带应用一样。

## 两个库中的启动工作

TL；DR:两个图书馆都有完整的文档，所以这里没有太大的区别。

如果你和你团队中的其他开发者没有本地应用开发的背景，你可能想要一个没有陡峭集成指南的解决方案。React 导航只有在集成过程中稍有优势。

尽管有些开发人员有时发现很难开始，Wix 的这个[官方指南可以帮助你开始工作。最近，RNN 引入了一个基于](https://wix.github.io/react-native-navigation/docs/installing) [npx 的命令，将 RNN 安装在一个全新的 React 原生应用](https://wix.github.io/react-native-navigation/docs/installing/#installing-with-npx-rnn-link)中。Wix 还有一个[有用的不和谐聊天](https://discordapp.com/channels/242515947020222464/339371162855276544)，可能是你遇到麻烦时的首选。

## React 导航中的深度链接与 React 本机导航

TL；DR:两个库都支持深度链接。

在社交媒体时代，大多数应用程序都有一种简单的方式，允许用户在他们的社交渠道上分享信息和活动。这要求我们在应用程序中启用深度链接。

假设您的应用程序显示博客，您希望用户在进入您的应用程序时直接登陆帖子屏幕。React Navigation 和 RNN 目前都没有提供一种简单的方法来做到这一点。这两种方法在静态定义路线的情况下效果最佳。尽管您可以实现一些变通方法来实现这个特性，但这是以增加代码库的复杂性为代价的。

## 开发团队和后盾

TL；DR:两个库都是大规模团队和 app 开发和使用的。

当选择任何库时，可以显著影响应用程序的主要因素之一是维护该库的开发人员和贡献者的数量。React Navigation 背后的团队由 React 本地核心和 expo 的积极贡献者组成。他们还在开发几个 React 原生应用，让他们对未来可能出现的问题有更广阔的视角。

今天，大量使用 React Native 构建的应用程序，包括 CNN、彭博和城市词典，都在使用 React 导航。这里有一个 [Twitter 线程](https://twitter.com/notbrent/status/1116717557107417090)列出了额外的应用程序。RNN 得到了维克斯的支持。他们在生产中将其用于自己的应用程序，并专注于它的持续进步和发展。

## 简而言之，反应导航与反应本地导航

选择导航库是应用开发的关键一步。任何未经深思熟虑的决定都可能会损害应用程序的可伸缩性，或者在应用程序变大时产生其他问题。

依我看，React 导航是目前最好的导航解决方案之一。它是一个被广泛采用的库，非常适合大多数用例。尽管存在某些已知的限制，但这是一个首选的解决方案。请记住，由于像 react-native-screens 和 react-native-gesture-handler 这样的库，它现在的性能得到了提升。

为了更深入的分析，你可以观看 Brent Vatne 的这个[精彩演讲，他是 React Navigation 的主要贡献者。Brent 非常全面地比较了这两种导航解决方案。](https://www.youtube.com/watch?v=GBhdooVxX6Q)

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