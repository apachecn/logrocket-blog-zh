# 使用 React 本机调试器调试您的应用程序

> 原文：<https://blog.logrocket.com/debugging-your-app-with-react-native-debugger/>

[Akshay Rana Follow](https://blog.logrocket.com/author/akshayrana/) I'm a self-taught, full-time programmer. I have experience working with various technologies including React, React Native, WordPress, Node.js, Express.js, Raspberry Pi, Python, REST APIs, and GraphQL. I love to explore new technologies and update my skills.

# 使用 React 本机调试器调试您的应用程序

## 

2021 年 7 月 16 日 2 分钟读完 731

在本教程中，我们将向您展示如何使用 React 本机调试器来调试 React 本机应用程序。

您可能想知道，为什么不使用默认的 React 本机调试工具呢？React 本机调试器的独特之处是什么？请继续阅读，了解这些问题的答案，并了解 React 本机调试器如何工作。

## 安装 React 本机调试器

要开始调试，我们需要安装 [React 原生调试器](https://github.com/jhen0409/react-native-debugger)。可以从 [GitHub](https://github.com/jhen0409/react-native-debugger/releases) 安装 React 原生调试器。或者，如果您使用的是 macOS，您可以在终端中运行以下命令:

```
brew update && brew install --cask react-native-debugger

```

下载并安装完这个包之后，就可以开始使用 React 本机调试器了。

让我们启动调试器工具。您可以看到 React 本机调试器处于等待状态，调试器工具正在监听端口 8081:

![Debugger Tool](img/ef18c1596d4b8767bc4b354f5adaed08.png)

## 将 React 本机应用程序连接到 React 本机调试器

要将您的应用程序与 React 本机调试器连接，您需要运行您的应用程序并启动调试模式。

要启动调试模式，摇动您的移动设备或按下`Command + Shift + Z`或`Ctrl + M`并选择调试选项。

现在，您将看到您的应用程序与 React 本机调试器工具相连接:

![React Native Debugger Tool](img/3c9d1733ed0d6518d03a2fae49796ef5.png)

我们已经准备好开始使用 React 本机调试器。

## React 本机调试器的功能

React Native Debugger 的与众不同之处在于，它将广泛的功能整合到一个独立的应用程序中。一些最酷的功能包括:

让我们更详细地放大每个功能。

## UI 检查器

如果您是 web 开发人员，您应该熟悉元素检查器。在 React Native Debugger 中，UI 检查器以同样的方式工作:您可以看到您在应用程序中使用的所有标签，并检查样式。更神奇的是，你可以从检查器中测试你的 UI 并改变样式。

![Debugging UI React Native](img/45b7cba0465aa705d6b143e1413db057.png)

这使得您的 UI 测试更快，并相应地加快了构建 UI 的过程。

## 在 React Native 中调试 Redux

无论您是在 React 还是 React 本机应用程序上工作，您最终都需要一个状态管理库。Redux 是目前领先的状态管理库之一。

也就是说，如果不能调试状态，使用 Redux 可能会很痛苦，尤其是在 React Native 中。React Native Debugger 使您能够轻松调试基于 Redux 的应用程序。你甚至可以调试实时状态。

![Debugging Redux App](img/ba340bde5381969aa66f395d1f52f61e.png)

您可以使用 Redux 时间旅行来调试一段时间内的状态。这项技术在处理大型项目时尤其有用。

要使用 Redux dev 工具，您需要在应用程序中激活 Redux。为此，您需要将以下代码添加到您的`App.js`中。或者，你可以将它添加到 Redux 的主函数中。

```
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__;
const store = createStore(reducers, /* preloadedState, */ composeEnhancers(middlewares));

```

## 网络检查员

如今，应用程序从云端或服务器获取数据非常普遍。为了执行这样的任务，我们需要连接到互联网或网络。

您可能会遇到监控请求的需要。例如，当进行 API 调用时，您可能需要检查是否发送了正确的 API 参数。或者您可能希望验证服务器的响应。

为了调试请求，我们可以使用 React 本机调试器中的网络检查器功能。点击**网络**标签，如下图所示:

![Network tab React Native Debugger](img/4f0c84e66aaee58852c9ac5c0c5b3edd.png)

打开网络检查器后，如果您发出网络请求，您可以在此标签中看到所有请求及其响应。

## `AsyncStorage`React Native 中的管理

如果您想在控制台中打印或记录您的`AsyncStorage`,您可以使用以下命令轻松完成:

```
console.log(showAsyncStorageContentInDev())

```

这将在 React 本机调试器的控制台中打印或记录`AsyncStorage`数据。

![Asyncstoage](img/7c03d94c23170d8ad9e6bd99c5bf278e.png)

## 在 React Native 中使用断点

当您需要在某个时间停止代码执行时，断点非常有用。

![Breakpoints](img/0280b66b66000672461a4589f56343d6.png)

您还可以检查代码执行流并查看变量的值。简而言之，断点可以帮助你理解应用程序的行为，并在几秒钟内发现错误。

## 结论

在本教程中，我们介绍了如何使用 React 本机调试器来调试 React 本机应用程序。我们还回顾了 React Native Debugger 的一些最重要的特性，这些特性旨在帮助加速和简化开发过程。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)