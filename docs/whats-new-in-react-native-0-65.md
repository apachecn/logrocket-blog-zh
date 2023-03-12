# React Native v0.65 的新特性

> 原文：<https://blog.logrocket.com/whats-new-in-react-native-0-65/>

React Native v0.65 即将推出，这是一个成为 React Native 开发者的好时机！虽然这不是一个充满了许多闪亮的新组件的功能包版本，但性能优化和可访问性改进使它成为一个值得谈论的令人兴奋的更新。

因此，在你匆忙离开并更新你当前的项目之前，让我们看看为什么你应该这样做。

## 爱马仕 0.8.1

赫尔墨斯 0.8.1 是让[最新的 React 原生更新](https://reactnative.dev/blog/2021/08/17/version-065)值得关注的一大部分，所以让我们深入探讨一下。

2019 年发布的 [Hermes](https://blog.logrocket.com/getting-started-with-hermes-in-react-native/) 是一款 AOT(提前)JavaScript 引擎，旨在优化 Android 设备上的 React 原生性能。随着市场上各种高端和低端 Android 设备的出现，这是有意义的。

Hermes 优化了 Android 设备上的 React 原生性能，减少了:

*   交互时间(加载时间等。)
*   下载大小(较小的 APK 文件)
*   内存利用率(垃圾收集器策略)

## 冥府垃圾收集者

在冥界之神之前，赫尔墨斯有了 GenGC 垃圾收集器来提高操作系统的内存。它运行在与解释器共享的单线程上。但是现在我们有了一个新的上帝。

爱马仕的聪明人创造了一种叫做哈迪斯的新垃圾收集器。用该团队自己的话说，哈迪斯的目标是“将暂停时间比 GenGC 提高一个数量级。”

为了实现这一点，Hades 在后台线程中与运行 JavaScript 代码的解释器同时运行大部分垃圾收集工作。已经不是非此即彼了。

简单地说，哈迪斯垃圾收集器是多任务的。如果你的大脑运行在新的 Hermes 引擎上，你会在这个博客上用你的左眼阅读一篇文章，同时用你的右眼和双手在你最喜欢的 IDE 上写代码！

脸书本身在 CPU 密集型工作负载上的性能提高了 20%到 50 %,暂停时间缩短了 30 倍。

## Android 更新

### 国际化 API

大多数 Javascript 引擎捆绑了一个国际化框架，这会增加应用程序的规模。 [ECMAScript 国际化 API 规范](https://tc39.es/ecma402/) (ECMA-402，或`Intl`)现在内置于 Hermes for Android 中，默认启用。

虽然这带来了空间效率的巨大收益——与 JSC 的 6MiB 相比，每个 API 大小的开销高达 57-62K——但也有一些代价。Android SDKs 存在一些限制，目前不支持以下`Intl`对象:

你可以在赫尔墨斯官方文件中找到完整的限制列表。

## iOS 更新

### 对 Mac 催化剂的支持

[Mac Catalyst](https://developer.apple.com/mac-catalyst/) 允许您将现有的 iOS 应用程序带到 macOS，利用 Mac 的屏幕大小、键盘和更快的速度。此次更新恰逢 macOS Monterey 即将发布。如果你是一名 iPad 应用程序开发者，将你的 iPad 应用程序转换成 Mac 应用程序，这是一个好消息，因为你现在可以随身携带爱马仕的力量。

苹果新的桌面操作系统 macOS Monterey 不在本文讨论范围之内，所以如果这次更新真的让你热血沸腾，请前往[苹果开发者门户](https://developer.apple.com/mac-catalyst/)了解关于新 Mac Catalyst APIs 的更多细节，以及它们对 React 原生开发者的意义。

### 支持苹果 M1 MAC 电脑

自 2019 年以来，爱马仕走过了漫长的道路。最近最显著的变化之一是随着 [React Native 0.64](https://blog.logrocket.com/whats-new-in-react-native-0-64/) 的发布引入了对 iOS 的支持。这一更新使得 Android 和 iOS 可以使用相同的 JavaScript 引擎。

对于所有苹果芯片用户，iOS 上的 Hermes 现在支持苹果 M1 MAC 电脑(ARM64 模拟器)。对于所有 M1 车主来说，这是一个受欢迎的更新。作为一名过去六个月的 M1 用户，我可以证明处于技术前沿的兴奋——以及当构建失败时试图在我的终端上阅读天书的沮丧！

如果对 M1 的支持阻碍了你的尝试，那么现在可能是你开始 React Native 之旅的好时机。

## 可访问性的改进和变化

### ios

您现在可以为 iOS 设置高对比度的明暗值。要自己检查一下，去**设置** > **辅助功能** > **显示&文本大小** > **增加对比度**。

![Display and Text Sizes Menu](img/46e3e767cd9882cdf053dbd947ba4ac6.png)

为了在 React Native 中查看这个新的辅助功能，我们将编辑基本模板项目。

在您的终端中使用以下脚本创建一个新项目，或者按照下面的步骤操作:

```
npx react-native init newReactNativeUpdate --template react-native-template-typescript

```

接下来，将这段代码添加到项目的`App.tsx`中的导入语句之后:

```
const dynamicTextColor = DynamicColorIOS({
  dark: 'darkgray',
  light: 'gray',
  highContrastDark: Colors.white,
  highContrastLight: Colors.black,
});

```

在`Section`组件中，将文本样式中的颜色属性从:

`color: isDarkMode ? Colors.white : Colors.black,`

收件人:

`color: dynamicTextColor,`

在你的模拟器中，进入**设置** > **辅助功能** > **显示&文本大小** > **增加对比度**，然后回到你的应用。请注意灰色文本现在是黑色的。

现在改变设备主题为黑暗模式，并再次尝试最后一步。请注意文本是如何从深灰色变成白色的:

![Dark Mode](img/7e4da9fcbcb0bc03540f328618e8bd0a.png)

## 结论

最新的 React Native 更新带来了更好的性能和可访问性，但也值得考虑这一切对 React Native 的未来意味着什么。我们现在有一个用于 Android 和 iOS 的 JavaScript 引擎，它也可以用于装有 Mac Catalyst 的 macOS 应用程序。React Native 还与脸书的桌面和虚拟现实团队合作。正如我在文章开头所说的，这是一个 React 原生开发者的时代！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)