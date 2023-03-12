# 电子锻造与电子反应样板日志

> 原文：<https://blog.logrocket.com/electron-forge-vs-electron-react-boilerplate/>

不知何故，网络技术征服了桌面。无数当今最流行的应用程序都使用 JavaScript 和 HTML。网络技术要么嵌入更大的本地应用程序，如微软飞行模拟器的 JavaScript 飞机仪器，要么建立在电子之上，如 Visual Studio Code，Twitch，Slack，Discord 或 Facebook Messenger。

在本文中，我们将看看使用 React 和 [Electron](https://www.electronjs.org/) 的桌面应用程序的样板文件。

## 什么是电子？

简而言之，Electron 将 Node.js 与 Chrome 的渲染引擎相结合，使开发者能够用 JavaScript 和 HTML 构建桌面应用。

它的秘密力量是用本地代码扩展 web 技术功能的能力；无论您的 web 应用程序在哪里遇到 web 浏览器的限制和约束，Electron 都允许您使用 Node.js，甚至允许开发人员直接编写 C、C++、Objective-C 或 Rust。

[React](https://reactjs.org/) 拥有被证明是应用程序的可靠选择的良好记录。然而，现在是 2021 年——大多数网络应用程序并不是按照它们执行的方式编写的。

Transpilers、compilers 和 bundlers 几乎是意料之中的，所以每当你开始编写一个新的电子应用程序时，你被要求以一种能很好地与 electronic 一起工作的方式来安排你的许多 web 工具。

当然，你可以使用 [TypeScript](https://www.typescriptlang.org/) 、 [webpack](https://webpack.js.org/) 以及任何你喜欢的工具，直到它们全部点击，但是使用样板文件可能是开始工作于你的应用程序的更简单的方法。

## 电子和反应样板:需求和期望

我们理想的样板文件需要处理两大任务。首先，它必须做我们在“web 应用程序”样板文件中寻找的所有事情。例如，您可能希望用 TypeScript 编写，使用 Less 或 Sass，包括所有类型的静态资产，并将您的许多 JSX 文件转换成 web 浏览器可以使用的东西。您可能希望使用大量的 JavaScript 依赖项，并将它们包含在您的应用程序包中。

然后，我们需要注意事物的电子方面。我们需要我们的应用程序有一个主进程入口点——和一个不同的呈现器进程入口点。

> 如果你对这两种流程类型之间的区别还不熟悉，请查看一下 [Electron 的快速入门指南](https://www.electronjs.org/docs/tutorial/quick-start#create-the-main-script-file)，因为理解 Electron 如何处理你的应用程序将在以后变得很重要，并使处理任何给定的样板文件变得容易得多。

换句话说，我们的电子样板将包含多个入口点，不像传统的 web 应用程序。然后，我们需要样板文件来执行构建适当的桌面应用程序所需的许多编译操作。

我们需要为我们所有的目标操作系统编译二进制文件，可能的话[对这些二进制文件进行代码签名](https://www.electronjs.org/docs/tutorial/code-signing)以便 macOS 和 Windows 不会显示讨厌的警告，并创建安装程序。哦，我们希望我们的应用程序能够自动更新。

最后，我们不希望样板文件将我们束缚在任何不好的实践中。这与事物的电子方面特别相关。桌面应用是强大的软件，可以访问整个机器，比纯 web 应用更需要考虑安全性。

## 使用电子反应样板作为样板

从原始数据来看，[电子反应样板](https://electron-react-boilerplate.js.org/)赢得了人心。GitHub 上有超过 16，000 颗星，这是目前最受欢迎的样板选择。它结合了 [TypeScript](https://www.typescriptlang.org/) 、 [Babel](https://babeljs.io/) 、 [webpack](https://webpack.js.org/) 和 [eslint](https://eslint.org/) ，利用 [electron-builder](https://electron.build) 处理电子的编译和打包操作。

仔细检查一下样板文件，我们就能明白为什么要选择它。webpack 已经被配置为处理“主”和“呈现器”流程入口点，了解哪里需要哪些依赖项，并创建包。

截至本文撰写之时，依赖项大部分都是最新的。预先配置的代码负责调试、日志记录和自动更新。打包程序 CLI，electronic-builder，已经设置好为所有操作系统生成二进制文件和安装程序。

如果您只是想开始构建您的应用程序，而不担心内部是如何工作的，这个样板文件将让您在几分钟内编写代码，而不要求您考虑构建过程。

```
# Clone the boilerplate:
git clone --depth=1 \
 https://github.com/electron-react-boilerplate/electron-react-boilerplate \
 your-project-name

cd your-project-name

# Install dependencies with yarn:
yarn
```

所有这些预先配置的便利都是有代价的，无论是对你，开发者，还是对你的应用程序的用户。

让我们从用户成本开始:一些依赖是有问题的。鉴于 Electron 的捆绑 Node.js 和 Chrome 版本可以很好地执行非聚合填充代码，并且通常速度更快，因此现代 JavaScript 特性的聚合填充很慢而且没有必要。

一开始，样板文件就包含了大约 1 兆字节的 JavaScript 代码。实际需要多少很大程度上取决于您的个人需求，因此有可能有些代码只是在根本不需要的情况下消耗资源。

至于开发者的成本，看看电子构建者吧，[宣称自己](https://www.electron.build/)是“打包和构建一个现成的电子应用程序的完整解决方案”。

那是准确的。该工具将负责编译跨平台应用程序的几乎所有方面，对开发人员隐藏所有潜在的复杂性。因此，这是一个有点一刀切的解决方案，为开发人员做出许多决定。

为了抽象掉这些复杂性，electronic-builder 背后的团队已经派生并修改了许多由 Electron maintainers 提供的原始工具和软件包。

当你只是尝试原型时，这种一刀切的解决方案可以让你专注于编写应用程序的创造性方面。然而，一旦您的应用程序增长了一点，您迟早会遇到需要构建过程的高级定制的场景。

抽象开了，你就很难做出那些改变。由于许多 electronic-builder 的内部软件包和依赖项与官方的电子软件包和依赖项不同，您可能会在以后获得安全更新和 bug 修复，但永远不会，或者会遇到一组完全不同的 bug。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

当 electronic 团队最近开始为 Apple 的新“Apple Silicon”处理器做准备的主要项目时，它为自己维护的软件包做了这项工作。

出于这个原因，你会发现大多数大型电子应用程序放弃了电子构建器的便利性，以获得对构建过程的更多控制。

现在，您是想走捷径还是想投资一个更坚实的构建基础，取决于您自己、您的项目需求以及您对电子应用程序如何打包的好奇心。如果你想走捷径，你可以停止阅读这里，使用电子反应样板，并建立下一个大东西！

## 使用电子锻造作为样板

如果您已经阅读了本文，那么您会对一个样板文件感兴趣，它将允许您使用官方的电子工具和软件包，并允许您控制构建过程。为了构建、打包和自动更新应用程序，Electron maintainers 提供了几十个单独的小软件包，它们都负责这个过程中的一个重要步骤。

例如，电子安装程序为 Windows 创建安装程序，而电子公证程序负责公证一个构建的。苹果看门人服务的应用捆绑。Slack 的构建过程使用了大约 10 个这样的包，并与几十个定制的构建文件相协调。

样板文件的整个想法是，你不想知道所有这些依赖关系，也不想考虑“几十个定制构建文件”。你想专注于你的应用。

在未来的可配置性和今天的便利性之间的一个很好的折衷是 electronic-forge，这是一套编排了许多官方电子打包工具的 CLI 工具。

```
yarn create electron-app my-new-app --template=typescript-webpack
```

由 electron-forge 提供的“typescript-webpack”模板包含 typescript、webpack、eslint 和最新版本的 electron 及其各种打包工具。

很像 electron-react-boilerplate，它允许您立即开始处理您的应用程序，已经为流程类型和绑定 JavaScript 模块的设置配置了 webpack。

值得注意的是缺少了反应。幸运的是，将 React 添加到我们的项目中只需要不到两分钟，这一时间投资绝对值得使用电子锻造的好处。从添加依赖项开始:

```
yarn add react react-dom
yarn add @types/react @types/react-dom --dev
```

然后，在“tsconfig.json”中，通过向您的`compilerOptions`添加一个`"jsx": true`来添加对 JSX 文件格式的支持。就是这样！您现在已经有了一个包含 React、TypeScript、ESLint、webpack 的工作样板，并准备好征服世界了。

## 电子反应样板与电子锻造

你可能会想，“告诉我用哪一个就行了。”嗯，我管理着两个维护 electronic 的团队中的一个，已经在 electronic 上工作了多年，并且开发了许多电子应用程序( [Slack](https://slack.com/) 、 [windows95](https://github.com/felixrieseberg/windows95) 、 [macintosh.js](https://github.com/felixrieseberg/macintosh.js/) )。

如果你想要一个推荐，我会告诉你去电子锻造。是的，与最流行的选项相比，它会要求你了解更多关于电子应用程序的内部工作原理，但我个人保证，对电子应用程序工作原理的额外理解将允许你编写一个性能更好、更安全、更稳定的应用程序。如果你有任何问题，请加入我们[新开的 Discord 服务器](https://discord.com/invite/electron)上的电子开发者社区。

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