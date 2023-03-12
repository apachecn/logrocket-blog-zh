# 完全指南反应原生的网络日志火箭博客

> 原文：<https://blog.logrocket.com/complete-guide-react-native-web/>

***编者按:*** *本文于 2022 年 3 月 16 日更新，包含 React Native for Web* *最新发布的大多数* [*的信息，并说明了突破性的变化。*](https://github.com/necolas/react-native-web/releases/tag/0.17.0)

## 介绍

当开始一个新项目时，要做的最重要的决定之一是选择你的开发栈。你需要选择一个你的大多数用户都活跃的平台，但同时，这个平台不应该有太多的限制。

应用程序的未来发展可能需要您在一个完全不同的平台上接触用户。因此，您的初始堆栈应该足够灵活，以适应这种增长需求。

说到通用技术， [React Native for Web](https://blog.logrocket.com/sharing-code-react-native-web/) 使开发人员能够在 Web 上使用 React Native 组件和 API。这是移动优先框架之战中的一个游戏改变者，因为 React Native 是构建跨平台移动应用程序的最受欢迎的选择之一。

在本文中，我们将讨论围绕 React Native for Web 的讨论，并使用它建立一个演示项目！

## 内容

## React Native 可以用于 web 和移动吗？

是啊！通过 React Native for web，开发人员可以编写一个 React Native 应用程序，该应用程序可以在 Android 和 iOS 上本地运行，也可以在使用标准 Web 技术的 Web 浏览器上运行。

但是考虑到 React Native 的概念母体 React 是专门为 web 构建的，首先值得质疑的是为什么这种技术是必要的。

现实是，移动应用的基础与 web 应用的基础完全不同。例如，React 使用基本的 HTML5 元素，如`div`和`span`来构建布局，而 React Native 使用自定义 UI APIs，如`View`和`Text`。由于这种差异，很少有技术支持构建 web 和移动应用程序。

[React Native for Web](https://blog.logrocket.com/react-native-web-vs-flutter-web/) 就是试图弥合这一鸿沟。它旨在帮助使用单一 React 原生代码库创建真正的跨平台应用程序。但是它是如何做到的呢？让我们找出答案。

## 在浏览器上使用 React Native:React Native 如何用于 Web 工作？

如前所述，web 应用和移动应用有着非常不同的基础。两个平台上都有不同的 API 来完成相同的工作，比如呈现 UI。

任何旨在弥合这一差距的框架背后的思想都很简单:使用一个平台的 API 来构建另一个平台的 API 的等价物。React Native for Web 就是这么做的。

React Native for Web 提供了 React Native 组件的浏览器兼容等价物。例如，如果在 React Native mobile 中使用一个`<View>`来呈现视图，那么在 React Native for Web 中可以获得这个`<View>`的浏览器兼容版本，它有一个将这个视图编译成 web `<div>`的方法。

这提出了一个重要的观点:并不是所有的本地组件都是浏览器友好的。一些面向移动设备的组件利用了移动硬件 API，这些 API 不能通过 web 浏览器直接访问。

这意味着我们不能使用 React Native for Web 将应用程序完全移植到其 Web 版本中。但是我们仍然可以移植它的大部分，而且通常情况下，这就足够了。

样式是 React 和 React Native 的另一个不同之处。在 React 中，我们可以使用一长串样式解决方案，包括 CSS、 [Sass](https://blog.logrocket.com/sass-react-native-guide/) 、CSS-in-JS 等。另一方面，在 React Native 中，所有样式都是用 CSS-in-JS 编写的。

也许不出所料，React Native for Web 采用了 React Native 的 CSS-in-JS 方法，确保同一组样式在移动和 Web 上都可以很好地工作。

## 集成

你可以访问 [React 原生目录](https://reactnative.directory/?web=true)来查找提供 web 支持的 React 原生包。

以下流行的 web 框架也为集成提供了 API:

## React Native for Web 入门

现在，我们已经从理论上理解了 React Native 系列 API 背后的概念，让我们通过安装过程来了解如何设置我们的 web 友好的 React Native 应用程序。

我们可以通过两种方式为 web 应用程序设置 React Native:Expo 和 Create React App。

### 世博会

Expo 是一个通用 React 应用开发框架。它支持通过 [Expo for web](https://docs.expo.dev/workflow/web/) 开发 Android、iOS 和 web 应用程序。

Expo for web 提供了一系列现成的跨平台 API，包括 web 构建优化，并提供了一个通用 SDK，用于使用 React Native for web 在 Web 上运行原生移动 Expo 应用程序。

要使用 Expo for web 引导 React Native for Web 应用程序，请执行以下步骤:

```
// initialize project with npm
npx expo-cli init your-app-name
cd my-app

// install react-dom and react-native-web
npm install react-dom react-native-web
npx expo-cli start                                                       

```

### 创建 React 应用

创建 React 应用程序是引导纯 web React 应用程序的标准方式。虽然自带了对别名`react-native-web`到`react-native`的内置支持，但还是建议使用 Expo。

但是，我们可以通过以下步骤使用 CRA 引导 React Native for Web 应用程序:

```
// initialize create-react-app project
npx create-react-app your-app-name
cd my-app

// install react-dom and react-native-web
npm install react-native-web
npm start

```

## 为 web 准备 React 本机应用程序

让我们从引导您的 Expo 项目开始，如下所示:

```
// initilaize project
npx create-react-app <-- app-name -->

```

注意，通过使用`npx`，我们使用了最新版本的 CRA 包。这消除了我们在全球安装时在本地管理其版本的麻烦:

```
// change directory
cd <-- app-name -->

// install dependencies
npm i react-native-web

```

现在我们已经安装了主包，我们将使用 React native 的`[AppRegistry.registerComponent]([https://reactnative.dev/docs/appregistry](https://reactnative.dev/docs/appregistry))`注册我们的`App`根组件——注意，如果您使用的是`expo-cli`，这一步是不需要的。

`AppRegistry`是运行所有 React 原生应用的 JavaScript 入口点，您可以通过用以下代码替换`index.js`文件中的代码来注册`App`根组件:

```
import { AppRegistry } from "react-native";
import App from "./App";

AppRegistry.registerComponent("App", () => App);
AppRegistry.runApplication("App", {
  rootTag: document.getElementById("root")
});

```

接下来，让我们添加一些使用 React Native CLI 的样板代码。用以下代码替换`App`组件中的代码:

```
import React from 'react';
import { Button, StyleSheet, Text, View, Dimensions } from 'react-native';

function Link(props) {
  return <Text {...props} accessibilityRole="link" style={StyleSheet.compose(styles.link, props.style)} />;
}

function App() {
  return (
    <View style={styles.container}>
      <View style={styles.header}>
        <Text style={styles.title}>Welcome to React Native for Web</Text>
      </View>
      <Text style={styles.text}>
        This starter project enables you to build web application using React Native module. .
      </Text>
      <Text style={styles.text}>
        Built with <Link href="https://github.com/facebook/create-react-app">Create React App</Link> and{' '}
        <Link href="https://github.com/necolas/react-native-web">React Native for Web</Link>
      </Text>
      <Button onPress={() => {}} title="Example button" />
    </View>
  );
}
let ScreenHeight = Dimensions.get('window').height;
const styles = StyleSheet.create({
  container: {
    flex: 1,
    height: ScreenHeight,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF'
  },
  header: {
    padding: 20
  },
  title: {
    fontWeight: 'bold',
    fontSize: '1.5rem',
    marginVertical: '1em',
    textAlign: 'center'
  },
  text: {
    lineHeight: '1.5em',
    fontSize: '1.125rem',
    marginVertical: '1em',
    textAlign: 'center'
  },
  link: {
    color: '#1B95E0'
  },
  code: {
    fontFamily: 'monospace, monospace'
  }
});
export default App;

```

现在，我们可以通过运行以下命令来测试应用程序:

```
npm start

```

我们应该得到如下结果。

![Blank React Native for Web app](img/665b48940acc784e4c7957d315185199.png)

## webpack 和 Babel 的定制和增强

CRA 在幕后为我们做了很多，比如[包别名](https://necolas.github.io/react-native-web/docs/setup/#package-aliasing)和优化的 webpack 配置。

开箱即用，CRA 使用 webpack 的 [`resolve.alias`](https://webpack.js.org/configuration/resolve/#resolvealias) 为 Web 提供了别名 React Native 的内置支持。因此，当我们将 React Native 导入我们的应用程序时，它解析为 React Native for Web。

CRA 还提供了一个内部优化的 webpack 配置，但是对于有定制需求(比如使用 Babel 进行编译或构建增强)的精明开发者来说，你应该使用 Expo 引导你的应用程序。此外，确保将您的自定义 webpack 配置添加到您的`webpack.config.js`文件中，如下例所示:

```
 const webpack = require('webpack');

module.exports = {
  entry: {
    main: './index.web.js',
  },
  module: {
    loaders: [
      {
        test: /\.js?$/,
        exclude: /node_modules/,
        loader: 'babel',
        query: {
          presets: ['es2015', 'react'],
        },
      },
    ],
  },
  resolve: {
    alias: {
      'react-native$': 'react-native-web',
    },
  },
};

```

在上面的代码中，webpack 使用 [babel-loader](https://webpack.js.org/loaders/babel-loader/#root) 将 ES2015 和 JSX 代码转换成浏览器友好的 JavaScript。我们已经使用 webpack 的`resolve.alias`手动设置了包别名。`$`只是指定一个直接匹配。

请注意，为了使用上述配置，我们需要手动安装所需的依赖项，如下所示:

```
npm i webpack babel-loader babel-preset-react babel-preset-es2015
npm i webpack-dev-server --save-dev

```

或者，Babel 通过使用`[babel-plugin-module-resolver](https://www.npmjs.com/package/babel-plugin-module-resolver)`来支持模块别名:

```
{
  "plugins": [
    ["module-resolver", {
      "alias": {
        "^react-native$": "react-native-web"
      }
    }]
  ]
}

```

## 进一步建议

尽管 CRA 提供了一些开箱即用的功能，但通常建议您使用 Expo 引导您的应用程序，尤其是如果您正在构建一个多平台的应用程序。

对于构建时优化，建议您使用`babel-plugin-react-native-web`插件。您可以将它作为开发依赖项安装，如下所示:

```
npm install --save-dev babel-plugin-react-native-web

```

另外，可以使用平台模块添加特定于平台的代码:

```
import { Platform } from 'react-native';

const styles = StyleSheet.create({
  height: (Platform.OS === 'web') ? 200 : 100,
});

```

## 结论

回顾本教程，我们讨论了什么是 React Native for Web，它解决的问题，以及它是如何工作的。然后，我们学习了如何在从头开始创建我们自己的 React Native for Web 应用程序之前建立一个 React Native for Web 项目。

我们还学习了如何使用定制的 webpack 配置，以及如何使用 Babel 添加增强功能。

有趣的是，React Native for Web 被 Twitter、优步和美国职业足球大联盟用于生产 Web 应用程序。我希望您学到了足够的知识，可以在下一个多平台项目中尝试它。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)