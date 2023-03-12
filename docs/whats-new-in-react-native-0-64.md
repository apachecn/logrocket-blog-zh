# React 原生 0.64 的新特性

> 原文：<https://blog.logrocket.com/whats-new-in-react-native-0-64/>

[Levi Vaguez Follow](https://blog.logrocket.com/author/levivaguez/) An enthusiastic writer and a reader who loves to explore new pastures. I began my journey as a Ruby on Rails developer but decided to pursue my love for writing even more.

# React 原生 0.64 的新特性

## 

2021 年 4 月 22 日 3 min 读 912

自 2020 年秋季 React 17 发布以来，开发者社区对 React Native 的下一个版本寄予了很高的期望。 [React Native 0.64](https://reactnative.dev/blog/2021/03/12/version-0.64) 在一片喧嚣和猜测声中于 2021 年 3 月发布。新版本带来了一些令人兴奋的新功能，这将使整个社区受益，特别是 iOS 开发者，他们热切期待 Hermes 在该平台上的发布。

在本指南中，我们将向您展示 React Native 0.64 的新增功能，包括:

## iOS 上的爱马仕

Hermes 是一个开源的 JavaScript 引擎，经过优化可以在多个平台上运行 React Native。爱马仕通过降低内存使用率、减少应用程序的下载大小、缩短应用程序变得可用的时间或交互时间(TTI)来提高应用程序的性能。

此前，爱马仕只能在安卓系统上选择加入。随着这次更新， [Hermes 也可以在 iOS](https://blog.logrocket.com/getting-started-with-hermes-in-react-native/) 上使用了。

随着 React 原生生态系统的增长，开发人员遇到了与大规模应用程序生产相关的挑战。举几个例子，巨大的包大小、过多的电话内存使用和增加的交互时间会导致糟糕的用户体验。

脸书的工程师设计了爱马仕作为这些问题的轻量级解决方案。然而，在 React Native 0.64 发布之前，iOS 开发者在保持相同质量水平方面面临着一场艰苦的战斗。

在 iOS 上包含 Hermes 有助于反应原生应用程序性能更好，使它们更轻量级，并减少 iOS 平台上的交互时间。这将激励 React 本地社区为爱马仕和整个平台的发展做出更大的贡献。

### 将现有项目切换到 Hermes

在将现有项目切换到 Hermes 之前，您必须确保升级项目以使用 React Native 0.64。要在 iOS 上启用 Hermes，在 podfile 中将`hermes_enabled`设置为`true`，然后运行`pod install`。

值得注意的是，iOS 对 Hermes 的支持仍处于萌芽状态。在未来的日子里，我们可以期待进一步的改进和支持。

要在 Android 上启用 Hermes，请将`enableHermes: true`添加到您的`android/app/build.gradle file`中。

## 用 Chrome 查看爱马仕痕迹

在过去的一年里，脸书一直在赞助[大联盟黑客](https://mlh.io/)奖学金，并支持对 React Native 的贡献，包括使用 Chrome DevTools 中的 Performance 选项卡来可视化 React Native 应用程序在使用 Hermes 时的执行。

尽管这个特性很有用，但它并不是全新的，因为你已经可以对运行在 Hermes 上的 React 原生应用进行配置了。但是通过运行这个新命令，您可以将一个 Hermes 跟踪配置文件转换为一个 Chrome 跟踪配置文件，并将其拉至本地机器:

```
react-native profile-hermes [destinationDir] <flag>

```

了解更多关于如何在官方文件中记录爱马仕取样档案的信息。

## 代理支持的 Hermes

React 原生 0.64 也为 Hermes 带来了代理支持。这种支持能够兼容社区的流行项目，如 [React Native Firebase](https://rnfirebase.io/) 和 [M](https://mobx.js.org/README.html) [ob](https://mobx.js.org/README.html) [X](https://mobx.js.org/README.html) 。如果您一直在使用这些包，现在您可以将您的项目迁移到 Hermes。

React 原生团队计划在即将到来的版本中让 Hermes 成为 Android 的默认 JavaScript 引擎。如果你在 React 原生项目中使用 Hermes 时遇到问题，你应该[报告它](https://github.com/facebook/hermes)。

## 默认情况下，内联要求已启用

[Inline requires](https://reactnative.dev/docs/ram-bundles-inline-requires#inline-requires) 是一个 Metro 配置选项，可以通过延迟 JavaScript 模块的执行直到它们被使用来帮助加速应用的启动时间。传统上，JavaScript 模块的执行从启动时开始。

这项功能作为一个选择加入的配置选项已经存在了几年，但 React Native 0.64 默认启用了该选项，以帮助您构建快速执行的移动应用程序，而无需任何额外的配置。

内联要求是一个巴别塔变换。它接受模块导入并将它们转换为内联的。

让我们看一个例子，在这个例子中，inline requires 将一个模块导入调用从文件的顶部转换到使用它的地方。

之前:

```
import { TestFunction } from 'test-module';

const TestComponent = (props) => {
  const result = TestFunction();

  return <Text>{result}</Text>;
};

```

之后:

```
const TestComponent = (props) => {
  const result = require('test-module').TestFunction();

  return <Text>{result}</Text>;
};

```

## 反应 17

React 17 不包括任何新功能或任何面向开发者的重大变化。[官方博客文章](https://reactjs.org/blog/2020/10/20/react-v17.html)指出，React 17 是一个“垫脚石”版本，旨在更安全地将一个版本 React 管理的树嵌入另一个版本管理的树中。这也使得将 React 嵌入到用其他框架构建的应用程序中变得更加容易。

此外，新的 JSX 变换消除了导入 React 以使用 JSX 的需要。这是一个选择加入的功能，React 团队指出，它计划继续支持经典的 JSX 变换。

## 依赖性变化

*   React Native 已经放弃了对 Android API 级别 16–20 的支持。随着脸书应用程序放弃对这些安卓版本的支持，React Native 也会跟进
*   现在需要 Xcode 12 和 [CocoaPods 1.10](https://github.com/CocoaPods/CocoaPods/releases)
*   对 Node.js 的最低支持从版本 10 提升到版本 12
*   脚蹼颠簸到 0.75.1

## 结论

由于全球 100 多名贡献者的努力，React Native 0.64 现已上线。这个新版本对整个 React Native 社区是有益的和支持的。有了社区的帮助来发现 bug，我们可以期待在即将发布的版本中有进一步的改进。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)