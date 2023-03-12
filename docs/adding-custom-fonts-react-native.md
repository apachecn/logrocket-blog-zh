# 如何在 React Native - LogRocket 博客中添加自定义字体

> 原文：<https://blog.logrocket.com/adding-custom-fonts-react-native/>

字体是出色用户体验的基石，自定义字体可以为应用程序提供独特的身份，帮助您的应用程序在竞争激烈的市场中脱颖而出。

在本指南中，我们将探索在 React 原生应用程序中添加自定义字体的方法。

为了跟上进度，您应该熟悉 React Native 或 expo SDK 的[基础知识，包括](https://blog.logrocket.com/how-to-build-ios-apps-using-react-native/) [JSX](https://blog.logrocket.com/diving-into-the-new-jsx-transform/) 、[c](https://blog.logrocket.com/a-complete-guide-to-default-props-in-react-984ea8e6972d/)组件(类和函数)，以及[样式](https://blog.logrocket.com/react-native-styling-tutorial-with-examples/)。

您可以简单地复制并粘贴本指南中的代码块，但是我建议您通读整个教程以获得完整的理解。

本指南假设您已经完成了应用程序的基本设置。

## 以原生方式添加字体(0.60+)

根据 React Native 的最新更新(0.60 之后的任何版本)，向应用程序添加自定义字体变得非常直接和有效。

我们所要做的就是创建一个 fonts 目录，添加我们想要使用的自定义字体，创建一个配置文件，添加路径，并将它们链接到项目。

### 获取应用程序所需的字体

这是一个相当简单的过程，因为我们在互联网上有各种各样的字体来源。Google Fonts 是开源和免费字体流行资源的一个很好的例子。您可以在中查看和下载您喜欢的任何字体。ttf 格式。

下载你想要的字体后，在你的项目中的`assets`下创建一个字体目录。路径应该是这样的:

```
PROJECT-DIRECTORY/assets/fonts

```

将下载的字体文件移动到这个字体目录。

### 将配置添加到项目中

如果尚未创建，请在名为`react-native.config.js`的项目根目录下创建一个配置文件。继续在`module.exports`中添加以下代码:

```
module.exports = {
    project: {
        ios:{},
        android:{}
    },
    assets:['./assets/fonts/'],
}

```

### 将资产链接到项目

完成上述步骤后，我们可以简单地运行一个命令来链接我们刚刚添加的字体。

```
$ npx react-native link

```

…或者:

```
$ yarn react-native link

```

**反应原生 0.69 更新**

在 0.69 版本的 React 中已经删除了原生链接命令。react-native-asset应该用来自动链接字体资产。只需运行以下命令:

```
$ npx react-native-asset

```

差不多就是这样！在这个命令之后，我们可以在`android/app/src/main/assets/fonts`目录和`Info.plist`中看到我们添加的字体(分别针对 Android 和 iOS)。

此后，您可以在`fontFamily`样式下使用自定义字体的名称:

```
fontFamily: 'Yatra-One'

```

## 以世博会的方式添加字体

Expo 是一个基于 React Native 构建的优秀框架，几乎包含了构建一个全功能应用所需的所有模块。

`expo-font`是向 React 原生应用添加字体的另一种方式。与前一种方法相比，这更类似于每次应用程序加载时初始化字体。

第一步是相同的—要获取字体，在 assets 下创建“fonts”目录并将字体移动到那里。

Expo 应用程序中有两种初始化字体的方法；对根处的功能组件使用钩子，或者对类组件使用异步函数。

(注意:在字体加载之前，不要加载带有自定义字体的内容)

### 使用钩子初始化字体

挂钩用于初始化字体，如下面的代码片段所示:

```
import * as React from 'react';
import { View, Text, StyleSheet } from 'react-native';
import { useFonts } from 'expo-font';

export default function App() {
  const [loaded] = useFonts({
    Montserrat: require('./assets/fonts/Yatra-One.ttf'),
  });

  if (!loaded) {
    return null;
  }

  return (
    <View style={{flex:1}>
      <Text style={{ fontFamily: 'Yatra-One', fontSize: 27 }}>Yatra-One</Text>
    </View>
  );
}

```

### 使用`Async`功能初始化字体

`Fonts.loadAsync`用于初始化字体，如下面的代码片段所示:

```
import * as React from 'react';
import { Text, View, StyleSheet } from 'react-native';
import * as Font from 'expo-font';

export default class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
        fontsLoaded: false
    }
  }

  async loadFonts() {
    await Font.loadAsync({
      'Yatra-One': require('./assets/fonts/Yatra-One'),
    });
    this.setState({ fontsLoaded: true });
  }

  componentDidMount() {
    this.loadFonts();
  }

  render() {
    if (this.state.fontsLoaded) {
      return (
        <View style={styles.container}>
          <Text style={{ fontSize: 21 }}>Default Font</Text>
          <Text style={{ fontFamily: 'Yatra-One', fontSize: 27 }}>Yatra-One</Text>
        </View>
      );
    } else {
      return null;
    }
  }
}

```

## 结论

React 原生配置和 expo-font 都是向 React 原生应用添加字体的好方法。

在本教程中，我们讨论了如何使用 config-link 方法和 expo-font 方法添加字体。两者都有各自的好处，所以你可以根据自己的喜好选择一个最适合你的。

你可以在查看`expo-font`的官方文档以获得更多详细参考[。](https://docs.expo.dev/versions/latest/sdk/font/)

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)