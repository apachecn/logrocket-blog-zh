# 2021 年顶级 React 原生样板- LogRocket 博客

> 原文：<https://blog.logrocket.com/top-react-native-boilerplates-for-2021/>

React Native 是脸书开发的一个框架，用于使用 React 构建 Android 和 iOS 应用程序。如果您熟悉 React Native，您会知道从头开始一个项目可能是一个非常乏味、重复且耗时的过程。因此，在本文中，我们将看看一些最流行的 React 原生样板文件，它们将帮助您更快地开始一个项目。

我相信这些是当今可用的最好的样板文件，并且都是由出色的开发人员开发的。为了帮助您在选择本文中的样板文件时节省时间，我还将讨论它们的一些特性，以便您可以发现哪一个最适合您的项目。

用于分析本文中即将展示的样板文件的标准是它们的 UI 设计、导航类型和测试能力。

我没有对这些项目进行排名，因为我相信它们中的每一个都是有用的工具。但是，我用他们在 Github 上的星数来决定他们出现的顺序。

## 1.被无限的红色点燃

[Ignite](https://github.com/infinitered/ignite)by[Infinite Red](https://github.com/infinitered)是一个前沿的 React 原生项目样板，包括 CLI、组件/模型生成器等等。

Ignite 拥有 12.2K stars，是 Expo 和 bare React Native 最受欢迎的 React Native 应用程序样板，它支持 Expo 开箱即用。这个[TypeScript](https://github.com/microsoft/TypeScript)ready 项目使用 [MobX](https://github.com/mobxjs/mobx) 进行状态管理， [React navigation](https://reactnavigation.org/) 进行路由和导航， [Apisauce](https://github.com/infinitered/apisauce) 与 REST 服务器对话，以及 [Jest 框架](https://jestjs.io/)进行测试。

我喜欢这个样板文件的原因是，它是 Flipper-ready、Reactotron-ready，并且支持 Expo 开箱即用。除此之外，这个样板文件使用 MobX 而不是 Redux 进行状态管理，众所周知，与 Redux 相比，后者更容易理解。它还带有与 MST 集成的 [AsyncStorage](https://github.com/react-native-async-storage/async-storage) 用于恢复状态。

Ignite 有一个名为 [ignite-cli](https://www.npmjs.com/package/ignite-cli) 的内置命令行界面。建议您在运行时使用 Node.js 附带的`npx`来访问当前版本，而不是全局安装和管理特定版本的 CLI。

满足此项目页面上列出的所有先决条件后，您应该能够使用以下命令创建一个新项目:

```
# for vanilla React Native
npx ignite-cli new PizzaApp

# or for Expo-powered:
npx ignite-cli new PizzaApp --expo

```

## 2.由 mcnamee 制作的 React Native 初学者工具包

[React Native Starter](https://github.com/mcnamee/react-native-starter-kit) 作者[麦克纳米](https://github.com/mcnamee)是一款 React Native 样板应用，可以让你非常非常快速地启动并运行。GitHub 上有 3K 明星。

该项目使用 [Redux](https://redux.js.org/) 进行状态管理， [React Native Router](https://github.com/aksonov/react-native-router-flux) 进行路由和导航， [NativeBase](https://github.com/GeekyAnts/NativeBase) 进行 UI 设计， [React Native Vector Icons](https://github.com/oblador/react-native-vector-icons) 进行图标显示， [Axios](https://github.com/axios/axios) 用于与 REST 服务器对话，Jest 框架用于测试。

这个样板文件的作者确保它附带了电池。这个样板文件不仅带有 NativeBase，这是一个为 React Native 提供基本跨平台 UI 组件的库，还带有 [Redux persist](https://github.com/rt2zz/redux-persist) ，这是一个允许我们在浏览器的本地存储中保存 Redux 存储的库。

您可以使用这个样板文件创建一个新项目，只需使用以下命令克隆这个项目:

```
git clone https://github.com/mcnamee/react-native-starter-kit.git your_app_name

```

## 3.用打码机反应本机样板文件

[React Native 样板](https://thecodingmachine.github.io/react-native-boilerplate/)作者[the code machine](https://github.com/thecodingmachine)是一个 React Native 项目模板，用于通过 UI、状态管理和业务逻辑之间的关注点分离来构建可靠的跨平台移动应用。它在 Github 上有 2k 颗星。

这个样板文件使用 Redux 进行状态管理，使用 React Navigation 进行路由和导航，使用 Axios 与 REST 服务器进行对话，使用 Jest 框架进行测试。

这个样板文件易于安装，并且自带了一个名为 [i18next](https://github.com/i18next/i18next) 的国际化库。

在使用这个项目之前，请转到[React Native environment setup](https://reactnative.dev/docs/environment-setup)，然后选择 React Native CLI Quickstart 选项卡。遵循给定开发操作系统和目标操作系统的说明。

满足此项目页面上列出的所有先决条件后，您应该能够使用以下命令创建一个新项目:

```
npx react-native init MyApp --template @thecodingmachine/react-native-boilerplate

```

[React Native Template TypeScript](https://github.com/react-native-community/react-native-template-typescript)是由 [React Native 社区](https://github.com/react-native-community)推出的 1.2K 星级项目。这是一个简洁的 React 本机模板，可以快速入门使用 TypeScript。

这个 TypeScript 就绪的项目旨在保持适度，因此它没有附带任何用于状态管理、导航、UI 设计或与 REST 服务器对话的库。然而，它确实附带了 Jest 测试框架。

这个样板文件非常适合那些喜欢用最少的资源启动 react 原生项目的人。

满足此项目页面上列出的所有先决条件后，您应该能够使用以下命令创建一个新项目:

```
npx react-native init MyApp --template react-native-template-typescript

```

## 荣誉奖

本节中的样板文件是一些无法进入前四名的项目，但仍然是很好的工具。

### 通过反应重新开始-无处不在

[由](https://github.com/react-everywhere/re-start)[重新启动](https://github.com/react-everywhere)react-everywhere是一个 React 原生模板，旨在通过单一代码库实现多种平台，包括 web、移动和桌面。

对于 1.3K stars，该样板文件使用 Redux 进行状态管理，使用 [React Router](https://github.com/ReactTraining/react-router) 进行路由和导航。不幸的是，这个样板文件没有附带用于 UI 设计、与 REST 服务器对话或测试的库。

这个样板文件的伟大之处在于，它允许您使用 React 本机 API 和单个代码库针对多个平台(Android、iOS、Web、Windows 和 electronic)。

满足此项目页面上列出的所有先决条件后，您应该能够使用以下内容创建一个新项目。

使用`react-native-cli`创建一个新的 react-native 项目，并将`re-base`指定为模板:

```
react-native init <Your Project Name> --template re-base

```

React Native 不支持模板继承。如果您想使用类似于`re-dux`或`re-route`的派生模板，请将它们直接安装在新创建的项目之上。您可能会得到一个警告，提示项目已经存在，只需说“是”就可以覆盖所需的文件:

```
react-native init <Your Project Name> --template re-dux
react-native init <Your Project Name> --template re-route
react-native init <Your Project Name> --template re-start

```

`re-start`模板依赖于`re-route`和`re-dux`，一定要按顺序先安装它们。

由于 react-native-template 不支持向`package.json`添加自定义脚本，所以执行`./finishInstall.js`来完成项目配置。

### 极客们的反应

由 [GeekyAnts](https://github.com/GeekyAnts) 开发的项目 ReactNativeSeed ，为你提供了大量 React 原生样板文件供你选择。

这个项目的网站允许你选择你希望你的样板文件具有的特性。

您可以选择 MobX 或 Redux 进行状态管理，React Navigation 进行导航和路由，TypeScript 或 [Flow](https://github.com/facebook/flow) 进行静态类型检查，[创建 React Native App (CRNA)](https://github.com/expo/create-react-native-app) 或 plain React Native 进行堆栈。

在提供的一些样板文件中，你还会发现用于 UI 设计的 [NativeBase](https://github.com/GeekyAnts/NativeBase) ，React Native Vector 图标，以及用于测试的 Jest 框架。

我喜欢这个条目的原因是，它能够在下载样板文件之前选择您希望您的应用程序拥有的组件。

在您使用这个样板文件生成器之前，请注意，不幸的是，一些包含您根据组件选择下载的样板文件的存储库有点过时。

### 由 victorkvarghese 反应本地样板文件

[Reactive Native Boilerplate](https://github.com/victorkvarghese/react-native-boilerplate)作者 [victorkvarghese](https://github.com/victorkvarghese) 是一个基于类型的架构，使用 React、Redux、Sagas 和带授权流的钩子来开发 React Native 应用。它在 GitHub 上有 375 颗星。

这个 TypeScript ready 项目使用 Redux 进行状态管理，React Navigation 进行路由和导航， [React Native Paper](https://callstack.github.io/react-native-paper/) 进行 UI 设计，React Native Vector 图标，Axios 用于与 REST 服务器对话，Jest 框架用于测试。

这个样板文件的一些最大卖点是路由和导航带有内置的身份验证流，并且它使用 React Native Paper 进行 UI 设计。React Native Paper 是 React Native 的可定制和生产就绪组件的集合，遵循 Google 的材料设计指南。

满足此项目页面上列出的所有先决条件后，您应该能够使用以下内容创建一个新项目。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

首先，克隆样本库:

```
git clone https://github.com/victorkvarghese/react-native-boilerplate.git <your project name>

```

转到项目的根目录:

```
cd <your project name>

```

移除。git 文件夹:

```
rm -rf .git

```

使用`react-native-rename`更新项目名称，您应该可以开始了:

```
npx react-native-rename <newName>

```

### React 本机模板 AmitM30 的类型脚本

[React Native Template–TypeScript](https://github.com/AmitM30/react-native-typescript-boilerplate)by[amitm 30](https://github.com/AmitM30)是一个样板文件，允许您使用 React Native 和 TypeScript 快速构建健壮的应用程序，并且已经设置了最常用的工具。

这个样板文件有 166 颗星，使用 Redux 进行状态管理， [React Native Navigation](https://github.com/wix/react-native-navigation) 进行路由和导航，React Native Paper 用于 UI 设计，React Native Vector 图标，Axios 用于与 REST 服务器对话，Jest 框架用于测试。

这个样板文件与前面提到的样板文件的区别在于，它使用 React 本机导航进行路由和导航。React Native Navigation 在 iOS 和 Android 上都提供了 100%的原生平台导航，与 React Navigation 库相比，它使您的应用程序的性能更高，React Navigation 库在本文中显示的其他一些样板文件中使用。

您可以使用这个样板文件创建一个新项目，只需使用以下命令克隆这个项目:

```
git clone https://github.com/AmitM30/react-native-typescript-boilerplate.git your_app_name

```

## 结论

在本文中，我们讨论了一些 React 原生样板，它们使我们的应用程序开发变得更加容易。

如果您忘记了这些样板文件的优势，请查看下表来刷新您的记忆:

| 功能/产品 | 被无限红色点燃【12.2k⭐】 | 由麦克纳米(3K⭐)公司生产的 react native starter kit | 通过打码机(2K⭐)反应本地样板文件 | React 本地模板由 React 本地社区(1.2K⭐)编写 |
| --- | --- | --- | --- | --- |
| 状态管理 | MobX | Redux | Redux | ❌ |
| 导航 | 反应导航 | 反应本地路由器 | 反应导航 | ❌ |
| UI 组件 | ❌ | NativeBase，反应原生矢量图标 | ❌ | ❌ |
| HTTP客户端 | 蜜蜂酱 | Axios | Axios | ❌ |
| 打字稿 | ✔️ | ❌ | ❌ | ✔️ |

不管您选择哪种样板文件，希望在您开始使用其中一种后，您的应用程序开发过程会比以前更顺利。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)