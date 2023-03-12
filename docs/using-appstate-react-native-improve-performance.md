# 在 React Native 中使用 AppState 提高性能

> 原文：<https://blog.logrocket.com/using-appstate-react-native-improve-performance/>

出于各种原因，了解应用程序的当前状态是至关重要的，最明显的是内存管理。持续更新应用程序的状态会消耗大量的能量，有时最好在用户没有与应用程序交互时暂停更新。

这就是 React 本机 AppState API 的用武之地。AppState 会告诉您应用程序何时处于非活动状态或处于后台，以便您可以停止不必要的进程，节省内存，并提高 React 本机应用程序的性能。

在本教程中，我们将向您介绍 AppState，并通过一个简单的例子演示它是如何工作的。

## React Native 中的 AppState 是什么？

在 React Native 中， [AppState](https://reactnative.dev/docs/appstate) 表示应用的当前状态——即应用是在前台还是后台。

AppState 对于收集应用程序使用情况的数据非常有用，例如，在将应用程序置于后台或关闭应用程序之前，用户在应用程序上花费的时间。分析这些数据有助于您了解用户与您的应用程序交互的方式，以便您可以在必要时做出更改来提高参与度。

有无数的 SDK 旨在帮助您生成这种类型的洞察力，但是 AppState 使您能够自己监控状态变化，而不依赖于任何第三方工具。

## AppState 是做什么用的？

如上所述，AppState 最常用于内存管理和用户状态管理。让我们更深入地研究一下，看看它是如何在这些重要任务中发挥作用的。

### 内存管理

当用户没有与应用程序交互时，AppState 可以帮助您避免不必要的状态更改。

创建一个根据应用程序状态变化的`isMounted`属性是一个很好的实践。如果我们考虑类组件，一旦`componentDidMount`被触发，则`isMounted`被设置为`true`，而当`componentWillUnmount`被触发时，则`false`。

你可以在所有组件中使用`this`的`isMounted`属性，只在组件被挂载时调用`this.setState`。

```
this.isMounted && this.setState(...)

```

您可以使用 AppState 的功能来相应地限制状态更新，例如，当应用程序处于后台或不活跃(在 iOS 中)时暂停状态更新，并在用户返回应用程序时继续。

### 用户状态管理

就分析而言，AppState 使您能够更新用户交互的数据库，例如，当用户返回应用程序或将其置于后台时，这些数据会告诉您用户如何与您的应用程序交互。

AppState 还可以帮助你判断用户是在线还是离线，这对于聊天应用来说尤为重要。你可能在 WhatsApp、Telegram 和其他提供聊天功能的应用程序中看到过“在线”和“最后一次出现在…”。

您可以根据应用程序状态的变化轻松更新用户状态，例如，当用户与应用程序交互时，当应用程序当前处于活动状态时，或当应用程序处于前台时，在线；当用户将应用程序置于后台或将其关闭时，离线。

## 如何在 React Native 中使用 AppState

为了查看 AppState 的运行情况，我们将创建一个 React 本地聊天应用程序来显示用户的在线状态。我们将使用 AppState 来指示用户何时在线，何时应用程序打开或处于前台，以及何时应用程序处于后台或关闭。

AppState 是`react-native`库的一部分，可以使用以下代码轻松导入:

```
import {AppState} from 'react-native';

```

现在，您可以在应用程序中使用 AppState 及其侦听器了。

AppState 最基本的用例是使用`currentState`属性获取应用程序的状态:

```
AppState.currentState

```

从上面的属性我们可以得到两种状态:`active`和`background`。

*   `active`表示应用程序当前正在运行，并处于前台
*   `background`表示应用程序正在运行，但目前处于后台，即用户正在运行另一个应用程序或查看其主屏幕

Android 和 iOS 上都给出了上述状态，但 iOS 支持一个名为`inactive`的附加 AppState，当用户在两个应用之间转换、打开通知中心或收到来电时，就会出现这种状态。

## 侦听 AppState 中的更改

AppState 带有状态变化的监听器。Android 和 iOS 都支持`change`监听器。

要添加新的监听程序，请输入以下内容:

```
AppState.addEventListener

```

然后添加`change`事件监听器，根据更改更新应用程序:

```
const appStateListener = AppState.addEventListener(
  'change',
  nextAppState => {
    console.log('Next AppState is: ', nextAppState);
    setAppState(nextAppState);
  },
);

```

为了提高性能，清理侦听器总是一个好的做法。要清理 AppState 监听器，只需使用`remove`方法:

```
appStateListener.remove()

```

下面是我们示例的完整代码:

```
import React, {useEffect, useState} from 'react';
import {View, StyleSheet, Text, AppState} from 'react-native';

const App = () => {
  const [aState, setAppState] = useState(AppState.currentState);
  useEffect(() => {
    const appStateListener = AppState.addEventListener(
      'change',
      nextAppState => {
        console.log('Next AppState is: ', nextAppState);
        setAppState(nextAppState);
      },
    );
    return () => {
      appStateListener?.remove();
    };
  }, []);
  return (
    <View style={styles.container}>
      <Text style={styles.txt}>
        Current App State is: <Text style={styles.aState}>{aState}</Text>
      </Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
    backgroundColor: '#000',
  },
  txt: {
    color: '#d9d9d9',
    fontSize: 18,
  },
  aState: {
    color: '#fff',
  },
});
export default App;

```

还有两个特定于 Android 的监听器可供您使用:

*   `focus`当用户与应用程序交互时
*   `blur`当用户拉下通知中心时

下面是最终结果:

## 结论

现在，您已经对 AppState 工具以及如何在 React 本机应用程序中使用它有了基本的了解。您可以使用它来更改应用程序中的用户状态(从在线到离线，反之亦然)，收集应用程序使用情况的分析，以及播放或暂停应用程序中的 AV 内容，具体取决于您正在处理的项目类型。

感谢阅读！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)