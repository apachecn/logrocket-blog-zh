# React Native vs. Swift for iOS 开发- LogRocket 博客

> 原文：<https://blog.logrocket.com/react-native-vs-swift-ios-development/>

***编者按**:本文最后更新于 2023 年 2 月 7 日。查看这篇文章，了解更多关于使用 [React Native for iOS 应用](https://blog.logrocket.com/how-to-build-ios-apps-using-react-native/)的信息。*

如果你想开发一个 iOS 应用，你已经有相当多的语言和框架可以使用。React Native 和 Swift 就是这样两个工具。

React Native 是一个来自脸书的 JavaScript 库，用于创建兼容 iOS 和 Android 的跨平台应用程序。它允许您使用熟悉的语法来利用本机功能。Swift 是苹果开发的一种逐渐超越 Objective-C 的编程语言，Swift 专门用于构建 iOS 应用。

在为 iOS 开发选择这两者之前，首先要问自己的问题是，你打算建立一个跨平台的应用程序还是一个 iOS 原生的应用程序。当然，在做出这个选择之前，还有其他因素需要考虑。

在本文中，我们将根据从性能到易用性的许多因素来评估这两种技术。

我们将涵盖:

## React Native 如何构建 iOS 应用

React Native 的工作方式与 React 类似，只是 React Native 不使用虚拟 DOM 来操作 DOM。在幕后，React Native 解释 JavaScript 代码，并使用 API 与本机组件进行通信。所有数据都在桥中序列化，这不允许用户交互。

众所周知，React Native 有两个独立的部分:JavaScript 代码和本机代码(iOS 和 Android)。实际上连接这两者的是一座桥梁，尽管是不同的语言，但它允许双方进行交流。从 JavaScript 发送到本机代码的所有数据都作为序列化的 JSON 对象传输。数据可以是你的 UI 元素、函数等等。

最后，我们的本机 iOS 模块，如`FileReader`、`DeviceInfo`和其他模块与 iOS SDK 通信，实现它们相应的本机功能。

## Swift 如何构建 iOS 应用

开发 Swift 是为了取代 Objective-C 构建苹果软件。它包含在 Xcode 中，Xcode 是苹果用于编写和开发应用程序的 IDE。Swift 使用的是 [LLVM](https://en.wikipedia.org/wiki/LLVM) 编译器，Xcode 中仍然包含该编译器。

## 对 iOS 开发的本地利弊做出反应

一门语言再伟大，再可靠，也难免会有一些不好的地方。仅仅因为一家公司选择 Swift 或其他任何东西而不是 React Native，并不意味着它是移动开发的坏工具；相反，它不适合他们的业务或产品用例。一般来说，React Native 提供了许多优点。

### 赞成的意见

*   非移动开发人员的低学习曲线:Web 开发人员可以使用 React Native 直接进入移动开发，因为毕竟它只是 JavaScript 代码。即使你仍然需要学习一些本土概念，学习的时间也更短
*   **热重装**:从代码中修改 UI 不需要你重新构建应用程序。这也有助于更快的开发和错误修复
*   **基于组件的**:可以写代码，重用。像按钮和卡片这样的组件可以在你的代码库中的任何地方使用，甚至可以在其他项目中使用——不需要重写相同的东西
*   **成本效益**:这可能不是每个市场都合理的，但一般来说，构建一个 iOS 应用程序的成本是省时和低成本的
*   **大社区** : React Native 有一个大而活跃的社区，更容易找到开发的帮助和资源。社区通常可以提供关于堆栈溢出的常见问题的答案和解决方案。你可以在 GitHub 上查看 [React 原生社区，它有支持 React 原生生态系统的存储库。在内容方面，你可以在多个平台上找到 React 原生标记的内容，包括](https://github.com/react-native-community) [r/reactnative/](https://www.reddit.com/r/reactnative/) subreddit，以及 [LogRocket blogsite](https://blog.logrocket.com/tag/react-native/) 。

### 骗局

*   更新版本中不可预见的变化
*   大型项目的可伸缩性问题。严重依赖设备硬件的复杂软件如果不是用本机代码构建的，往往会遇到性能和可伸缩性问题。请记住，桥使用本机来连接 JS 代码，而本机代码仍然使用额外的层，这可能会影响性能

## 快速反应的 UX 本地人

Swift 和 React Native 都用于移动应用程序开发，但它们在用户界面(UI)和用户体验(UX)方面有不同的方法。

作为一种原生的 iOS 开发语言，Swift 为苹果产品的用户提供了一个熟悉的、可预测的 UI 和 UX。使用 Swift 构建的应用程序可以访问苹果的所有内置 UI 元素，允许与其他原生应用程序保持一致的外观和感觉。然而，为特定平台设计会限制应用的覆盖范围和受众。

另一方面，React Native 允许开发人员构建可以在 iOS 和 Android 上运行的跨平台应用程序。它使用 JavaScript 和 React 来构建原生应用，允许开发人员构建外观和感觉都像原生元素的自定义 UI 组件。

React Native 也有一个大型的预建 UI 组件库，使开发人员可以更容易地快速创建一个精美的、具有本机感觉的应用程序。然而，React Native 的 UI 有时会感觉与完全本机的应用程序略有不同，因为它具有跨平台的特性。

## iOS 开发的利弊

Swift 设计用于所有苹果操作系统，即 iOS、iPadOS、macOS、tvOS 和其他一些操作系统。Swift 的主要优势在于它的名字——它很快。但是，和往常一样，利与弊并存。

### 赞成的意见

*   **类型安全** : Swift 提供了一个安全而强大的打字系统，具有良好的错误处理风格。这在最大程度上防止或减少了错误
*   **可扩展和可维护** : Swift 允许您专注于应用程序的核心基础设施。更简单的语法使得代码更容易阅读和维护
*   **性能** : Swift 的创建是为了超越 Objective-C，成为开发移动应用的手段。使用 Swift 构建的应用程序比使用 Objective-C 构建的类似应用程序快[2.6 倍](https://www.apple.com/swift/)
*   **由苹果公司支持** : Swift 是由苹果公司构建和维护的，所以你可以放心地假设你的应用程序是面向未来的。它也是开源的

### 骗局

*   缺乏对以前版本的支持
*   寻找专门的 Swift 开发人员的相对昂贵/竞争激烈的就业市场
*   你必须使用 Xcode，并且只能在 Mac 和其他苹果设备上工作

## 哪个性能更好？

Swift 的真正本地开发在性能上胜出——不是因为 Swift 本身，而是因为它允许直接访问本地设备硬件和 API。

这并不意味着 React Native 不是 performant 默认情况下是这样的。也就是说，在使用 React Native 进行 iOS 开发时，您需要采取某些措施来确保您的应用程序快速可靠:

### 使用备用图像缓存组件

考虑使用外部 [react-native-fast-image](https://github.com/DylanVann/react-native-fast-image) 渲染图像。React Native 确实提供了一个内置的`Image`组件，但它有时会导致性能低下，尤其是在闪烁或处理屏幕上的许多图像时。

当使用`FastImage`而不是内置的`Image`组件时，您可以考虑的一些潜在好处是:

*   更快的图像加载和渲染:这个库特别针对性能进行了优化，在某些情况下可以比内置的`Image`组件更快地加载和显示图像
*   提高内存使用率:显示图像时可以使用更少的内存，这有助于防止内存不足的错误，并提高应用程序的整体性能。

事实上，`FastImage`(外部库)是用于原生 iOS 开发的流行库 [SDWebImage](https://github.com/rs/SDWebImage) 的包装器。

### 使用爱马仕

爱马仕已经为 APK 的安卓系统提供了一段时间，它帮助减少了应用程序的下载量、内存使用量等等。它还减少了下载大小，缩短了应用程序变得可用的时间，并减少了内存使用。对于 iOS 支持，Hermes 仍处于早期阶段。

为了在您的应用程序中使用 Hermes，在您的 Podfile 中将`hermes_enabled`设置为`true`并运行`pod install`:

```
use_react_native!(
   :path => config[:reactNativePath],
   # to enable hermes on iOS, change `false` to `true` and then install pods
   :hermes_enabled => true
)

```

### 拥抱记忆

这是一种特定类型的缓存，它返回已经被缓存的函数的相同值。仅此一项就可以加速程序并提高性能。

我不得不解释更多关于 React Native 的性能优化，因为 Swift 显然是这里的赢家——它是原生的，除此之外没什么可说的了。但这并不意味着 React Native 对于某些用例来说不够强大。

## 哪个开发生命周期更快？

React Native 在满足在 iOS 和 Android 上发布应用程序的标准方面表现出色，因为它使您能够构建一个在两个平台上无缝工作的应用程序。此外，React 本地组件大部分来自 web (React ),可以复制，从而加快开发过程。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

此外，React Native 提供了热重装和实时重装功能，这意味着您不必多次重新构建您的应用程序。仅这一点就能促进快速的开发生命周期。

但是，你需要明白，开发生命周期取决于各种因素，比如应用的复杂程度，开发人员的技能和经验，以及可用的资源。Swift(iOS 版)和 React Native 都通过简化的流程为您提供快速的开发周期。

## 何时使用 Swift，何时使用 React Native

React Native 和 iOS 是两个强大的工具，在各自的领域表现出色。选择使用哪一个现在取决于您，最终取决于您想要构建的项目类型。

这就是我的意思。Swift 将更好地服务于:

*   具有高内存管理需求和复杂用户界面的资源密集型应用
*   未来肯定会扩展或连接到其他苹果产品的应用——换句话说，主要面向 iOS 和 macOS 用户的应用
*   需要长期升级或维护的应用程序

App Store 上的许多应用程序都使用 Swift，因为它是由苹果公司本身支持的。所有的苹果应用程序，Airbnb、LinkedIn、Slack 和 Firefox，都是用 Swift 语言编写的。

React Native 更适用于:

*   需要少数开发人员在更短或有限的时间内开发的应用程序
*   需要跨平台功能的应用

为了更好地理解，下面列出了一些公司使用 React Native 的经历:

此外，你可以找到来自脸书、微软、Wix 等公司的顶级 React 原生应用的完整展示。

以下是 Swift 与 React Native 的一些关键特性的对比表:

| **功能** | 雨燕 | **反应原生** |
| --- | --- | --- |
| iOS 的开发语言/框架 | Swift/UIKit/SwiftUI | JavaScript 和 React |
| 支持的平台 | 苹果平台(iOS、macOS、tvOS、watchOS、iPadOS) | iOS 和 Android |
| UI/UX | 苹果产品的本地和熟悉的外观和感觉 | 带有定制组件的跨平台 UI，具有类似本机的感觉 |
| 程序调试时间 | 对于有经验的 Swift 开发人员来说可能更快 | 开发可能需要更长时间，但从长远来看，跨平台支持可以节省时间 |
| 社区和支持 | 庞大、活跃的社区和来自苹果公司的长期支持 | 拥有各种可用资源的大型活跃社区 |
| 表演 | 良好的性能，尤其是本机应用 | 性能可能稍慢，但优化技术是可用的 |
| 代码可重用性 | 仅限于苹果的平台 | 由于跨平台支持，代码可重用性更高 |
| 学习曲线 | 对于不熟悉原生 iOS 开发的人来说，学习曲线很陡 | 对于熟悉 JavaScript 和 web 开发的人来说，学习曲线比较平缓 |

## 结论

总之，Swift 将默认生产一个更高性能的 iOS 应用程序，但知道 RN 提供强大的性能和开箱即用的良好 DX 仍然很好，特别是现在 Hermes 已经帮助减少了下载大小和内存使用。归根结底，为您的应用需求选择合适的工具绝对是您能做的最好的事情。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)