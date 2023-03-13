# 如何在 React Native 中构建动画幻灯片切换

> 原文：<https://blog.logrocket.com/build-animated-slide-toggle-react-native/>

在本指南中，我们将学习如何在 React Native 中构建动画幻灯片切换，并理解创建该功能的基本概念。

随着技术和人类的进步，我们的生活已经与我们对技术的使用交织在一起。许多应用程序努力将日常生活解释为应用程序，弥合两个世界之间的差距。现实世界和移动应用程序之间的一种关系是对象状态的解释。

通常，我们喜欢在应用程序中创建这些真实场景的外观和感觉。这意味着用大 UX 构建应用程序的一个组成部分是将应用程序流与任何时候执行的活动连接起来。在应用程序中创建平滑的连接可以创建有意义的 UX，并增加用户交互的便利性。因此，在现代移动应用程序中很好地表现一个动作是很重要的。

本教程将不遗余力，所以不要跳过任何部分！

*向前跳转:*

什么是动画幻灯片切换？

## 滑动开关代表一个物理开关，允许用户打开或关闭东西。大多数开关用于在首选项和设置中切换开/关，或者在智能手机上切换 Wi-Fi。滑动切换也最适合用于改变系统功能和偏好的状态。

因为这是一篇操作方法文章，所以在开始之前知道你需要什么样的环境是很重要的。如果您是生态系统的新手，从 [React 原生文档](https://reactnative.dev/docs/environment-setup)开始可能会有所帮助。

先决条件

### 要学习本教程，您需要以下内容:

因此，让我们直接进入指南的设置阶段。

React Native 入门

## 首先，打开命令提示符窗口，并从命令提示符导航到您希望保存应用程序的位置。然后，输入以下命令创建一个新的应用程序:

一旦创建了应用程序，导航到`rn-slide-toggle`文件夹，在这里您将看到在项目的根目录下创建的几个文件。在应用程序的根目录中，有一个包含启动代码的`App.js`文件。

```
npx create-expo-app rn-slide-toggle 

```

运行 iOS 设备的`yarn ios`和 Android 设备的`yarn android`命令将指示 React 本机编译器捆绑代码并显示用户界面:

![Starting the React Native App](img/dcafc5df2a36323b102077b49a361389.png)

好了，在我们继续编写特性代码之前，让我们为了清晰起见检查一些术语。

了解 React Native 中动画幻灯片切换的概念

## 帮助我们实现动画幻灯片切换的一个关键概念是翻译。平移意味着将元素/项目从一个位置移动到另一个位置。您可以使用 React Native 中的`transform` style 属性来翻译组件。`Transform`接受一个包含不同轴的键值对的对象数组，我们可以从这些轴控制元素的位置:

翻译元素时，不要忘记时间的重要性。不管你的动画的意图是什么，翻译必须适应你设置的视觉和风格模板。如果翻译太长以至于你失去了观众，简化总是更好。所以，要注意适当的时机！

> 让我们看一个使用 React Native 中的`Switch`组件创建动画幻灯片切换的例子。

在 React Native 中构建内置幻灯片切换

## 在这一节中，我们将研究一种使用 React Native 中内置的`switch`组件实现动画幻灯片切换的极简方法。

首先，从导入组件开始，如下所示:

`BuildInSlideToggle()`中的`isWifiEnabled`变量保存了用于跟踪切换状态的`Boolean`值，该值可以用`setIsWifiEnabled`函数更新。当用户点击滑动切换按钮时，调用`wifiToggleSwitch()`，并更新`isWifiEnabled`值。

```
// component state management
import React, { useState } from "react";
import { View, Switch, StyleSheet, Text } from "react-native";
import { StatusBar } from "expo-status-bar";

```

值得注意的是，在`return`语句中，我用`View`和`Text`组件渲染用户界面，然后传入`style`道具使其更加美观。

在`Switch`中，我们将把`wifiToggleSwitch()`传递给`onValueChange`道具，每当有动作时，这些道具就会更新并触发反应:

React Native 使用`StyleSheet` API 来样式化和布局 UI 中的内容。要在代码中使用适当的样式，访问分配给保存来自`StyleSheet`的`style`对象值的`styles`变量。

```
const BuiltInSlideToggle = () => {

  // define a isWifiEnabled state variable and set the default value to false
  const [isWifiEnabled, setIsWifiEnabled] = useState(false);

  // Handle changing the isWifiEnabled when button is clicked
  const wifiToggleSwitch = () =>
    setIsWifiEnabled((previousState) => !previousState);

  // Render a view with different style classes
  return (
    <View style={styles.container}>
      <View style={styles.header}>
        <Text style={styles.headerText}>Settings</Text>
      </View>
      <View style={styles.option}>
        <Text style={styles.optionText}>Wifi</Text>
        <View style={styles.subOptionContainer}>
          <Text>Current Status is {isWifiEnabled ? "On" : "Off"}</Text>
          <Switch
            trackColor={{ false: "#767577", true: "#81b0ff" }}
            thumbColor={isWifiEnabled ? "#f5dd4b" : "#f4f3f4"}
            ios_backgroundColor="#3e3e3e"
            onValueChange={
              wifiToggleSwitch
            } /** call the wifiToggleSwitch when onValueChange callback */
            value={isWifiEnabled}
          />
        </View>
      </View>
      <StatusBar style="dark" />
    </View>
  );
};

```

然后，将以下样式添加到渲染视图中:

现在，我们的应用程序应该是这样的:

```
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#E5E5E5",
  },
  header: {
    paddingTop: 50,
    justifyContent: "center",
    alignItems: "center",
    marginBottom: 16,
  },
  headerText: {
    fontSize: 28,
    fontWeight: "bold",
  },
  optionText: {
    fontSize: 20,
    color: "gray",
  },
  option: {
    justifyContent: "space-between",
    paddingHorizontal: 16,
    paddingVertical: 20,
    backgroundColor: "#FFFFFF",
    marginVertical: 10,
  },
  subOptionContainer: {
    alignItems: "center",
    flexDirection: "row",
    justifyContent: "space-between",
    paddingVertical: 16,
    backgroundColor: "#FFFFFF",
  },
});

```

在上面的演示应用程序中，Wi-Fi 和位置选项在设置屏幕中，而在源代码中，我们只有 Wi-Fi 选项实现。我建议您自己实现位置选项来练习实现交换机组件。

> 让我们看看[react-native-toggle-element](https://github.com/mymai91/react-native-toggle-element)包，探索更多定制滑动切换的方法。

使用 react-native-toggle-element 构建高级幻灯片切换

## react-native-toggle-element 是一个第三方库，它为在 React Native 中创建和定制动画幻灯片切换提供了额外的方法和属性。它有内置选项来定制拇指按钮，以便在两种状态之间转换时使用图标、显示文本或改变颜色。

在[开始使用 React Native](#getting-started-react-native) 部分创建的`rn-slide-toggle`项目中，让我们确保文件树遵循以下结构:

一旦你创建了项目文件来模仿我们上面的，在`package.json`文件中设置`main`值为这个`./index.js`。

```
…
├── assets
├── node_modules
├── redux-store
 │   ├── reducers.js
 │   └── store.js
├── src
 │   ├── BuiltInSlideToggle.js
 │   └── SlideToggle.js
 |── App.js
├── index.js
└── package.json

```

然后，安装这些软件包:

在这个例子中，我们将构建一个`slide toggle`功能来在应用程序的主题之间切换。应用程序使用 redux-tool-kit 在应用程序的整个状态中实现选择和效果的主题更新。查看[这篇关于如何用 Redux Toolkit 让 Redux 变得更智能的指南](https://blog.logrocket.com/smarter-redux-redux-toolkit)。

```
yarn add react-native-toggle-element react-redux @reduxjs/toolkit

```

在`reducers.js`和`store.js`文件中，用以下代码替换其中的代码:

另外，将以下代码粘贴到您的`store.js`中:

```
import { createSlice } from '@reduxjs/toolkit'

const initialState = {
  mode: 'dark',
}

export const themeSlice = createSlice({
  name: 'theme',
  initialState,
  reducers: {
    changeMode: (state, action) => {

      if (action.payload) {
        state.mode = 'light'
      } else {
        state.mode = 'dark'
      }
    },
  },
})
export const { changeMode } = themeSlice.actions 

export default themeSlice.reducer

```

在您的`App.js`文件中，将代码更新为:

```
import themeReducer from './reducers';
import { configureStore } from '@reduxjs/toolkit'

export const store = configureStore({
    reducer: {
      theme: themeReducer,
    },
  })

export default store;

```

接下来，在`src/SlideToggle.js`中，我将通过把每个步骤分成小块来为`slide toggle`元素添加功能。

```
import SlideToggle from "./src/SlideToggle";
import { Provider } from 'react-redux';
import store from './redux-store/store';

export default function App() {

  return (
    <Provider store={store}>

    {/* The SlideToggle component would be instantiated from here*/}
       <SlideToggle/> 

       </Provider>
  );
}

```

所以，马上，我将导入库来帮助构建特性。

在`src/SlideToggle.js`文件中，您可以将这段代码粘贴到那里:

在`src/SlideToggle.js`中，我创建了一个`function`组件来实现和渲染`slide toggle`。在里面，我声明并赋值给`toggleValue`和`mode`状态变量，通过`dispatch`和`theme`变量从 redux 状态调度当前主题的值。我们还将使用`useEffect`钩子来监听`theme`变量的变化，并重新呈现 UI。

```
import React, { useState, useEffect } from "react"; // React core library and hooks

// View component
import { StyleSheet, View, Text } from "react-native";

// the third party library for slide toggle
import Toggle from "react-native-toggle-element";

import { StatusBar } from "expo-status-bar";

// Icon component, installed during expo setup
import { Fontisto } from "@expo/vector-icons"; 

// Global state storage to effect changes access the application
import { useSelector, useDispatch } from "react-redux";
import { changeMode } from "../redux-store/reducers"; 

```

`onToggle()`被用作`Toggle`组件的触发器，以获得`slide toggle`元素当前状态的`Boolean`值。

将此代码直接粘贴到导入语句的下方:

考虑一下`return`语句中的`Toggle`组件，我在这里呈现 UI，它用于实现应用程序的滑动切换元素。对于`Toggle`元素，我可以将`onPress`、`value`、`thumbActiveComponent`、`thumbInActiveComponent`和`trackBar`属性传递给组件，以确定滑动切换元素的当前值，对用户的操作做出反应，并显示适当的 UI。

```
export default function SlideToggle() {
  // state variable within the component
  const [toggleValue, setToggleValue] = useState(false);

  const dispatch = useDispatch();
  const theme = useSelector((state) => state.theme);
  const [mode, setMode] = useState(theme.mode);

  const onToggle = (val) => {
    dispatch(changeMode(val)); // invoke the changeMode function and pass argument

    setToggleValue(val); // update the toggleValue state variable
  };

  useEffect(() => {
    setMode(theme.mode);
  }, [theme]);

  return (
    <>
      <View
        style={mode == "light" ? styles.container_light : styles.container_dark}
      >
        <Text style={mode == "light" ? styles.text_light : styles.text_dark}>
          We are on {theme.mode} mode!
        </Text>
        <Toggle
          value={toggleValue}
          onPress={(val) => onToggle(val)} // we access the val variable inside toggle component
          thumbActiveComponent={
            <Fontisto
              name="sun"
              color="black"
              width="40"
              height="40"
              fill={"#3BD2B5"}
            />
          }
          thumbInActiveComponent={
            <Fontisto
              name="night-clear"
              color="black"
              width="40"
              height="40"
              fill={"#03452C"}
            />
          }
          trackBar={{
            activeBackgroundColor: "#9ee3fb",
            inActiveBackgroundColor: "#3c4145",
            borderActiveColor: "#86c3d7",
            borderInActiveColor: "#1c1c1c",
            borderWidth: 5,
            width: 100,
          }}
        />
        <StatusBar style={mode === "light" ? "dark" : "light"} />
      </View>
    </>
  );
}

```

然后，在`src/SlideToggle.js`中，将样式选项粘贴在`SlideToggle()`之后:

现在，我们将有一个可以在应用程序的亮暗模式主题之间切换的工作应用程序:

```
const styles = StyleSheet.create({
  container_light: {
    flex: 1,
    backgroundColor: "#fff",
    alignItems: "center",
    justifyContent: "center",
  },

  container_dark: {
    flex: 1,
    backgroundColor: "#121212",
    alignItems: "center",
    justifyContent: "center",
  },

  text_light: {
    marginBottom: 20,
    color: "#000",
  },

  text_dark: {
    marginBottom: 20,
    color: "#fff",
  },
});

```

结论

## 在本文中，我们学习了幻灯片切换以及如何在 React 本机应用程序中构建动画幻灯片切换。我们还研究了翻译组件背后的核心概念，并探索了用于在 React Native 中创建滑动开关的现有库。

完整的代码可以在 [GitHub](https://github.com/paulayo93/rn-slide-toggle) 上找到。我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。编码快乐！

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)

Start proactively monitoring your React Native apps — [try LogRocket for free](https://lp.logrocket.com/blg/react-native-signup).