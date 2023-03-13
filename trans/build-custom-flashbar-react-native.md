# 在 React Native 中构建自定义 flashbar

> 原文：<https://blog.logrocket.com/build-custom-flashbar-react-native/>

移动应用程序开发人员使用不同的策略来通知用户前台和后台应用程序事件。对于前台应用程序事件提醒，开发人员通常使用 flashbars、传统的提醒框、toast 消息、应用程序内通知系统或这些的组合。依赖于平台的本地推送通知通常用于通知用户后台应用事件。

flashbar 是一个现代的动画 GUI 元素，它呈现了关于应用程序事件的轻量级细节。Flashbars 通常出现在屏幕的一个角落(在大多数情况下，它们出现在屏幕的顶部或底部)，并在预定义的超时后消失。从 UI/UX 的角度来看，flashbars 适用于大多数需要通知用户应用程序事件状态的场景。例如，您可以在向数据库中插入新记录后显示特定的操作状态。

React 本地开发者可以使用流行的 [`react-native-flash-message`库](https://github.com/lucasferreira/react-native-flash-message)来显示 flashbars。在本文中，我将解释如何根据实际需求在 React Native 中构建和定制 flashbar 组件。

*向前跳转:*

## `react-native-flash-message`的突出特点

`react-native-flash-message`库允许你添加[素材 Snackbar](https://material.io/components/snackbars) 一样的低优先级警告组件，并允许你根据设备方向和状态栏高度创建闪灯条，因此它在有凹口的设备上工作良好(例如 iPhone X)。

我在下面强调了一些附加功能:

### 跨平台支持

这个库提供了一个基于 JavaScript 的解决方案。换句话说，它使用内置的 React 本地原始组件，如`View`、`Text`和`Image`，而不使用定制的本地模块。因此，你可以期待 Android 和 iOS 平台上一致的 flashbar 外观和感觉。此外，这个库提供了一种控制操作系统级 UI 元素的方法，比如状态栏。

### 可定制性和可扩展性

这个库提供了各种自解释的道具来定制 flashbar 的颜色、图标、样式、内容和过渡动画。因此，您可以轻松创建一个自定义的 flashbar，以创纪录的速度匹配您的应用程序主题。

这个包也足够灵活，允许你扩展它来满足任何高级需求，比如创建你自己的 flashbar 组件。

### 遵守 UI/UX 原则

你不需要自己设置很多道具来为你的 app 创建一个 flashbar。react-native-flash-message 库允许您通过设置 UX 友好的默认值来创建带有消息字符串的 flashbar 消息。例如，默认情况下，这个库在屏幕顶部呈现一个灰色的 flashbar，并在大约两秒钟后隐藏它。

## 构建一个 React 本机 flashbar

我们将创建一个新的 React 本地应用程序，并添加自定义的 flashbars 来了解`react-native-flash-message`库的特性。如果您需要将 flashbars 添加到现有的 React 本机应用程序中，也可以使用即将推出的代码示例，而无需创建新项目。

### 项目设置和配置

如果您计划为本教程创建一个新的 React Native 项目，请使用官方 React Native CLI 创建一个项目:

```
npx react-native init CustomFlashbars
cd CustomFlashbars

```

一旦上述命令完成，运行新创建的项目以确保一切正常:

```
npx react-native run-android
# --- or ---
npx react-native run-ios

```

接下来，按照以下步骤安装`react-native-flash-message`:

```
npm install react-native-flash-message
# --- or ---
yarn add react-native-flash-message

```

现在，安装成功后，我们可以开始创建 flashbars 了。先来展示一个基本的 flashbar 吧！

### 创建基本的 flashbar

将以下代码添加到您的`App.js`中，以显示一个基本的 flashbar:

```
import React from 'react';
import {
  SafeAreaView,
  StyleSheet,
  View,
  Button,
} from 'react-native';
import FlashMessage, { showMessage } from 'react-native-flash-message';

function App() {
  function handlePress() {
    showMessage({
      message: 'Hello flashbars!'
    });
  }
  return (
    <SafeAreaView style={styles.container}>
      <Button
        title="Show alert"
        onPress={handlePress}/>
      <FlashMessage/>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    padding: 12
  }
});

export default App;

```

这里，我们通过只发送`message`选项来显示默认配置的 flashbar。如上面的代码所示，在使用`showMessage`函数之前，我们需要将`FlashMessage`组件作为全局组件添加到`App`组件中。

一旦添加了全局`FlashMessage`组件，就可以从任何地方调用`showMessage`函数来显示 flashbar 消息。

运行上面的代码，您应该会看到一个背景为灰色的 flashbar，如下面的预览所示:

![Basic gray flashbar.](img/dd103962d1ee3464b6ce99393416d3ee.png)

默认情况下， [1850 毫秒后](https://github.com/lucasferreira/react-native-flash-message/blob/302b0642f1a8dcdd5911a4815bd9ecf67b67359e/src/FlashMessage.js#L326)flash bar 将消失，但是您可以通过消息对象中的`duration`选项自定义该时间段。例如，以下代码在五秒钟后隐藏 flashbar 组件:

```
function handlePress() {
  showMessage({
    message: 'Hello flashbars!',
    duration: 5000
  });
}

```

您也可以在全局`FlashMessage`组件中使用这些消息对象选项:

```
<FlashMessage duration={5000}/>

```

我们通常在 UX 友好的框中显示警告标题和描述，以创建一致的、最小的和灵活的用户界面。我们可以对 flashbar 消息做同样的事情。

以下代码片段显示了 flashbar 中的 flashbar 标题和描述，并呈现了多行 flashbar 消息:

```
function handlePress() {
  showMessage({
    message: 'react-native-flash-message',
    description: 'React Native flashbar and top notification alert utility' +
                  '\n' +
                  'Works on Android & iOS',
    duration: 2500
  });
}

```

通常，您可以使用换行符(`\n`)为 flashbar 消息描述创建多行文本。一旦你用上面的代码片段运行你的应用程序，你会看到一个多行 flashbar，如下所示:

![A multi-line flashbar](img/965431522f782812d4fb239f2d558325.png)

如果您的应用程序使用类似于`[react-navigation](https://blog.logrocket.com/navigating-react-native-apps-using-react-navigation/)`的库呈现多个应用程序屏幕，上面的`FlashMessage`组件位置保持不变。检查[这个源文件段](https://github.com/lucasferreira/react-native-flash-message/blob/302b0642f1a8dcdd5911a4815bd9ecf67b67359e/examples/FlashMessagePlayground/App.js#L31)作为参考实现。

如果您的应用程序只有一个应用程序屏幕，库文档建议避免使用 React `ref`对象进行全局设置，如下所示:

```
import React, { useRef } from 'react';
import {
  SafeAreaView,
  StyleSheet,
  View,
  Button,
} from 'react-native';
import FlashMessage from 'react-native-flash-message';

function App() {
  const flashMessage = useRef();
  function handlePress() {
    flashMessage.current.showMessage({
      message: 'Hello flashbars!'
    });
  }
  return (
    <SafeAreaView style={styles.container}>
      <Button
        title="Show alert"
        onPress={handlePress}/>
      <FlashMessage ref={flashMessage}/>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    padding: 12
  }
});

export default App;

```

上面的代码通过一个`ref`对象调用了`showMessage`函数，而没有从库中导入全局函数。在本教程中，我们将使用`ref`基于对象的方法，因为我们的示例应用程序只有一个屏幕。但是，如果你的应用程序有多个屏幕，你应该使用上面的第一个代码片段。

## 更改 flashbar 类型

之前，我们看到了一条灰色的 flashbar 消息，因为我们使用了默认配置。这个 flashbar 库为您提供了五种预定义的 flashbar 类型，因此您可以根据 flashbar 消息的严重性选择一种。看看以下受支持的消息类型:

```
function Spacer() {
  return (
    <View style={styles.spacer}></View>
  );
}

function App() {
  const flashMessage = useRef();
  function handlePress(type) {
    flashMessage.current.showMessage({
      message: 'Type: ' + type,
      type
    });
  }
  return (
    <SafeAreaView style={styles.container}>
      <Button
        style={styles}
        title="Default"
        onPress={() => handlePress('default')}
        color="#696969"/>
      <Spacer/>
      <Button
        title="Success"
        onPress={() => handlePress('success')}
        color="#5cb85c"/>
      <Spacer/>
      <Button
        title="Info"
        onPress={() => handlePress('info')}
        color="#5bc0de"/>
      <Spacer/>
      <Button
        title="Warning"
        onPress={() => handlePress('warning')}
        color="#f0ad4e"/>
      <Spacer/>
      <Button
        title="Danger"
        onPress={() => handlePress('danger')}
        color="#d9534f"/>
      <FlashMessage ref={flashMessage}/>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    padding: 12
  },
  spacer: {
    paddingBottom: 8
  }
});

```

这里，我们创建了五个按钮来显示所有支持的预定义 flashbar 类型。`handlePress`函数动态设置消息类型。将上述代码添加到您的`App.js`中，然后运行它。您将看到以下结果:

![Pre-defined flashbar types](img/797423bd0be4e0271744568e5df30c39.png)

## 使用自定义颜色

`type`选项根据预定义的类型设置背景颜色。例如，默认消息类型将背景渲染为灰色。这些预定义的配色方案是 UX 友好的，并解决了几乎所有的设计要求，但这个库也允许您为文本和背景使用自定义颜色。

例如，以下代码片段使用自定义颜色呈现 flashbar:

```
function App() {
  const flashMessage = useRef();
  function handlePress() {
    flashMessage.current.showMessage({
      message: 'Hello flashbars!',
      color: '#332222',
      backgroundColor: '#e09107'
    });
  }
  return (
    <SafeAreaView style={styles.container}>
      <Button
        title="Show alert"
        onPress={handlePress}
        color="#e09107"/>
      <FlashMessage ref={flashMessage}/>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    padding: 12
  }
});

```

上面的代码片段分别使用`color`和`backgroundColor`选项应用自定义文本和背景颜色。运行该应用程序时，您将看到以下结果:

![Background color of flashbar.](img/d324d1dc7d5556376aea0f1a11ba274d.png)

## 将自定义样式应用到您的 flashbars

这个库根据 flashbar 消息类型自动应用合适的颜色，同时遵循 UI/UX 原则。它还根据给定的道具使用了一些文本样式，比如如果你用`message`和`description`选项创建了多行 flashbar，就增加消息标题的字体粗细。

但是在某些场景中，我们需要做进一步的定制来完成我们的设计需求。该库允许您通过消息对象更改消息容器、标题和描述样式定义。

### `style`:更改消息容器样式

您可以更改容器样式以使用 flashbar 框的自定义样式，如以下代码片段所示:

```
function App() {
  const flashMessage = useRef();
  function handlePress() {
    flashMessage.current.showMessage({
      message: 'Hello flashbars!',
      type: 'success',
      style: styles.flashMessage
    });
  }
  return (
    <SafeAreaView style={styles.container}>
      <Button
        title="Show alert"
        onPress={handlePress}/>
      <FlashMessage ref={flashMessage}/>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    padding: 12,
  },
  flashMessage: {
    borderRadius: 12,
    opacity: 0.8,
    borderWidth: 2,
    borderColor: '#222',
    margin: 12
  }
});

```

上面的代码扩展了内置的成功消息样式，并创建了一个带有圆形边缘的自定义 flashbar。看下面的预告:

![Flashbar with round edges.](img/3b5d02970963a056edf1e37ef05112b1.png)

注意:您也可以将`floating`消息对象选项设置为`true`来使 flashbar 边缘变圆。上述`borderRadius`道具用于演示目的。

### `titleStyle` **和** `textStyle` **:** 改变文本样式

您可以使用`titleStyle`和`textStyle`选项来传递 flashbar 消息标题和描述的自定义样式。例如，以下代码片段对标题文本使用粗体字体样式，对描述文本使用小斜体字体样式:

```
function App() {
  const flashMessage = useRef();
  function handlePress() {
    flashMessage.current.showMessage({
      message: 'Hello flashbars!',
      description: 'React Native flashbar and top notification alert utility',
      type: 'success',
      titleStyle: styles.flasdescriptionhTitle,
      textStyle: styles.flashText
    });
  }
  return (
    <SafeAreaView style={styles.container}>
      <Button
        title="Show alert"
        onPress={handlePress}/>
      <FlashMessage ref={flashMessage}/>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    padding: 16,
  },
  flashTitle: {
    fontWeight: 'bold',
    fontSize: 18
  },
  flashText: {
    fontStyle: 'italic',
    fontSize: 15
  }
});

```

运行上面的代码。您将看到自定文本样式，如以下预览所示:

![Custom text styles. ](img/89f7d4e8eaa59ba55aead4924a433c9c.png)

### 使用内置和自定义图标

大多数 web 开发人员经常使用带有警告信息的图标来提高用户友好性。例如，如果您为错误弹出窗口使用合适的图标和颜色，用户无需阅读整个文本就能知道特定弹出窗口的严重性。类似地，我们可以在 React Native flashbar 消息中使用图标来获得更好的用户友好性。

默认情况下，该库不显示任何带有 flashbar 的图标，但是您可以根据 flash bar 类型自动使用一个图标，如下所示:

```
function Spacer() {
  return (
    <View style={styles.spacer}></View>
  );
}

function App() {
  const flashMessage = useRef();
  function handlePress(type) {
    flashMessage.current.showMessage({
      message: 'Type: ' + type,
      type,
      icon: 'auto'
    });
  }
  return (
    <SafeAreaView style={styles.container}>
      <Button
        title="Success"
        onPress={() => handlePress('success')}
        color="#5cb85c"/>
      <Spacer/>
      <Button
        title="Info"
        onPress={() => handlePress('info')}
        color="#5bc0de"/>
      <Spacer/>
      <Button
        title="Warning"
        onPress={() => handlePress('warning')}
        color="#f0ad4e"/>
      <Spacer/>
      <Button
        title="Danger"
        onPress={() => handlePress('danger')}
        color="#d9534f"/>
      <FlashMessage ref={flashMessage}/>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    padding: 12
  },
  spacer: {
    paddingBottom: 8
  }
});

```

上述`icon: 'auto'`配置要求库根据消息类型使用合适的图标。运行上述代码后，您将看到如下 flashbar 消息图标:

![Flashbar message icons.](img/17a38dd9f1a03a4863434e92fe62723d.png)

如果您想对内置图标选择进行更多控制，您可以手动设置`icon`选项的`info`、`success`、`warning`和`danger`值。

这个库非常灵活，它可以让你将任何 React 组件设置为 flashbar 图标。让我们用 [`react-native-vector-icons`库添加一个矢量图标。](https://github.com/oblador/react-native-vector-icons)

注意:下面的代码需要安装`react-native-vector-icons`库。按照 GitHub 上库的[自述文件中提到的步骤安装。](https://github.com/oblador/react-native-vector-icons#installation)

将以下代码添加到您的`App.js`文件中:

```
import Icon from 'react-native-vector-icons/FontAwesome';

function App() {
  const flashMessage = useRef();
  function handlePress() {
    flashMessage.current.showMessage({
      message: 'Hello flashbars!',
      description: 'React Native flashbar and top notification alert utility',
      type: 'warning',
      icon: () => <Icon
                    name="warning"
                    size={30}
                    color="#aa2020"
                    style={{paddingRight: 20, paddingTop: 14}}
                  />
    });
  }
  return (
    <SafeAreaView style={styles.container}>
      <Button
        title="Show alert"
        onPress={handlePress}
        color="#e09107"/>
      <FlashMessage ref={flashMessage}/>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    padding: 12
  }
});

```

在这里，我们使用字体牛逼图标包中的警告标志矢量图标。上面的代码将在 flashbar 中显示一个自定义图标，如下面的预览所示:

![Warning sign vector.](img/63c4cdc81992c117704aed6ea9d1da9f.png)

尝试使用带有`Image`组件实例的 PNG 图标——您可以在`icon`回调中使用任何 JSX 元素。

## 更改 flashbar 的位置

由于我们使用了默认位置，所有以前的 flashbars 都出现在屏幕的顶部。我们可以用`position`选项改变 flashbar 的位置。`position`选项接受`top`、`right`、`bottom`和`left`字符串值。

将以下选项添加到之前的自定义图标设置的`showMessage`功能中:

```
position: 'bottom'

```

一旦你打开 flashbar，你会注意到它在屏幕的底部边缘，和 [Android Toast](https://developer.android.com/guide/topics/ui/notifiers/toasts) 或者默认的 [Snackbar](https://developer.android.com/reference/com/google/android/material/snackbar/Snackbar) 设置一样。

## 附加事件处理程序

Flashbars 通常用于显示各种提醒和应用内通知。提醒通常是短消息，不提供进一步的交互性，但如果您使用 flashbar 来显示应用程序内通知，您可能需要在用户按下 flash bar 消息时将用户指引到另一个应用程序屏幕。或者，当用户长按它时，您可能需要显示关于特定的基于 flashbar 的错误消息的详细解释。

在这些场景中，我们需要将事件处理程序附加到 flashbar。查看以下代码，了解所有受支持的事件回调:

```
function App() {
  const flashMessage = useRef();
  function handlePress() {
    flashMessage.current.showMessage({
      message: 'Hello flashbars!',
      description: 'React Native flashbar and top notification alert utility',
      type: 'info',
      position: 'bottom',
      hideOnPress: false,
      duration: 5000,
      onPress: () => console.log('event: onPress'),
      onLongPress: () => console.log('event: onLongPress'),
      onShow: () => console.log('event: onShow'),
      onHide: () => console.log('event: onHide'),
    });
  }
  return (
    <SafeAreaView style={styles.container}>
      <Button
        title="Show alert"
        onPress={handlePress}/>
      <FlashMessage ref={flashMessage}/>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    padding: 12
  }
});

```

我们将`hideOnPress`属性设置为`false`,并使用长持续时间来试验受支持的事件处理程序。事件处理程序在以下 flashbar 和用户操作上执行:

*   `onPress`:当用户按下 flashbar 时
*   `onLongPress`:当用户长按 flashbar 时
*   `onShow`:当 flashbar 出现时
*   `onHide`:当 flashbar 消失时

运行上面的代码。轻按或长按 flashbar。您将看到每个事件处理程序如下执行:

![Event handler ](img/c9e5ca79a6a7c380d0a37d23981d7e95.png)

## 自定义内置动画和创建自定义动画

默认情况下，当您在屏幕顶部或底部显示 flashbar 时，`react-native-flash-message`库会播放滑入/滑出过渡动画。否则，它将播放淡入/淡出过渡动画。如果您不喜欢过渡动画，可以按如下方式关闭过渡动画:

```
function handlePress() {
  flashMessage.current.showMessage({
    message: 'Hello flashbars!',
    animated: false,
    // -------
  });
}

```

通过使用`animationDuration`选项改变过渡动画的速度，您可以进一步定制内置动画:

```
function handlePress() {
  flashMessage.current.showMessage({
    message: 'Hello flashbars!',
    animationDuration: 650,
    // -------
  });
}

```

此包允许您使用自定义动画覆盖默认过渡动画，如下所示:

```
function handlePress() {
  flashMessage.current.showMessage({
    message: 'Hello flashbars!',
    animationDuration: 650,
    transitionConfig: (animValue, position) => {
      const opacity = animValue.interpolate({
        inputRange: [0, 1],
        outputRange: [0, 1]
      });
      const scaleY = animValue.interpolate({
        inputRange: [0, 1],
        outputRange: [0, 1]
      });
      return {
        opacity,
        transform: [{ scaleY }]
      };
    }
  });
}

```

该库使用`transitionConfig`回调函数为 flashbar 容器创建动画样式。回调函数发送两个参数:

1.  `[Animated.Value](https://reactnative.dev/docs/animatedvalue)`参考
    1.  闪光灯的位置

回调函数期望一个动画样式对象作为返回值。当 flashbar 出现时`Animated.Value`从`0`到`1`，当 flash bar 消失时`1`到`0`。因此，可以通过`[interpolate](https://reactnative.dev/docs/animatedvalue#interpolate)`方法在`transitionConfig`中使用任何支持的动画风格道具来制作 flashbar 的动画。

例如，上面的代码片段使用了`opacity`和`scaleY`。运行上述代码后，您将看到一个自定义的过渡动画，如下面的预览所示:

![Custom transition animation.](img/492650c5133a2ab6557c8b1e18ba90f6.png)

尝试使用 React Native [动画为示例 flashbar 制作更多自定义动画。值 API](https://reactnative.dev/docs/animatedvalue) 。

## 将其他组件嵌入 flashbars

Flashbars 通常显示带有合适图标的标题和描述。但是，在某些场景中，我们需要在 flashbars 中嵌入其他组件来提高 UX 和实现设计需求。例如，您可以添加一个按钮供用户点击，而不是让用户点击 flashbar 来执行特定操作。

请看下面的示例代码片段:

```
function handlePress() {
  flashMessage.current.showMessage({
    message: 'Hello flashbars!',
    description: 'React Native flashbar and top notification alert utility',
    animationDuration: 550,
    position: 'bottom',
    renderAfterContent: () => (
      <View style={{alignItems: 'flex-end', paddingTop: 12}}>
        <View style={{width: '30%'}}>
          <Button title="Details" onPress={() => {}}/>
        </View>
      </View>
    )
  });
}

```

上面的代码片段将在 flashbar 中嵌入一个按钮元素。看下面的预告:

![Button element in flashbar](img/891accc115132dd2c0dd3c849ea53a57.png)

同样，您可以使用`renderBeforeContent`在 flashbar 内容之前嵌入一个定制组件。出于演示目的，上面的代码片段使用了带有内联样式的`renderAfterContent`的内联函数，但请确保将组件分成函数，并在生产应用程序的样式表中正确定义组件样式。

## 高级 flashbar 配置和功能

我们已经讨论了一些库特性，您可以根据自己的设计需求来创建任何自定义的 flashbar。您知道如何添加带有自定义颜色、样式、动画和嵌入组件的 flashbars。但是`react-native-flash-message`库为高级用例提供了更多的选项和特性。

### **使用 FlashMessageManager API 进行全局访问**

`FlashMessageManager`实用程序类允许您管理 flashbar 实例。例如，以下代码片段禁用所有即将出现的 flashbars:

```
import { FlashMessageManager } from 'react-native-flash-message';
FlashMessageManager.setDisabled(true);

```

以下代码片段将当前 flashbar 引用存储在`ref`中:

```
const ref = FlashMessageManager.getCurrent();

```

然后，您可以通过编程隐藏它:

```
ref.hideMessage();

```

### **用** `MessageComponent` **道具**定义自己的 flashbar 组件

内置的 flashbar 组件可以让你通过道具进行定制。如果您不能使用现有的内置选项来满足您的设计需求，您可以通过`MessageComponent` prop 实现您自己的 flashbar 组件并将其与库链接。

看下面的`FlashMessage`用法:

```
<SafeAreaView style={styles.container}>
  <Button
    title="Show alert"
    onPress={handlePress}/>
  <FlashMessage ref={flashMessage} MessageComponent=
    {({message}) => (
      <View style={{padding: 20}}>
        <Text style={{fontWeight: 'bold'}}>{message.message}</Text>
        <Text>{message.description}</Text>
      </View>
    )}
  />
</SafeAreaView>

```

上面的代码呈现了一个只支持标题和描述的最小 flashbar:

![Title and description in the flashbar.](img/d91e6743bf4f979e1a7959fe8efe440c.png)

同样，您可以根据自己的需求制作自己的 flashbar 组件。我们只覆盖了 flashbar 组件——动画和全局实用函数将按预期工作，所以您不需要自己重新实现它们。链接一个自定义的 flashbar 组件是你唯一需要做的事情。

从官方库文档中查看所有支持的消息对象选项和组件属性。库维护者可能会根据 GitHub 上的[特性请求添加额外的特性。](https://github.com/lucasferreira/react-native-flash-message/labels/enhancement)

## `react-native-flash-message`替代品

每个流行的 React 本地库通常都有竞争对手。`react-native-flash-message`是一个灵活的全功能库，你可以在任何 React 本地应用中使用——定制基于`react-native-flash-message`的 flashbars 是如此简单。

但是，以下`react-native-flash-message`备选方案也值得一试，因为它们具有竞争优势:

## 结论

在本教程中，我们学习了如何用流行的`react-native-flash-message`库创建定制的 flashbars。一些开发人员努力使他们的应用程序在所有支持的操作系统上看起来一致，具有平台无关的 GUI 元素。同时，一些开发人员倾向于使用特定于平台的 GUI 元素。`react-native-flash-message`库是一个纯 JavaScript 包，可以让你在所有支持的平台上创建一致的 flashbars。

我们还简要地看了类似于 [`react-native-simple-toast`库](https://www.npmjs.com/package/react-native-simple-toast)的替代库，它呈现特定于平台的、本地的、类似 flashbar 的 toast 消息。但是，与`react-native-flash-message`相比，`react-native-simple-toast`不支持嵌入 JSX 组件，因为它是一个简单的原生 toast 库，而不是一个全功能的 flashbar 库。

`react-native-flash-message`库支持嵌入 JSX 组件，并为定制提供了广泛的选项/道具，它提供了比本文中提到的其他选择更灵活的特性。

因此，总的来说，`react-native-flash-message`库是一个很好的库，可以向 React 本地应用添加现代的、可定制的 flashbars。从 UI/UX 的角度来看，flashbar 概念比传统的 toast 概念显示了更多的内容，所以不要犹豫在 flash bar 中嵌入按钮或链接。

Flashbars 通常显示低优先级的应用程序事件，因此，对于关键的应用程序事件，传统的警告框(模态对话框)适用于当前的应用程序屏幕。使用 flashbars 向用户通知低优先级的应用程序事件，而不会阻止用户与应用程序屏幕的交互。查看[材料 Snackbar](https://m2.material.io/components/snackbars) 规格，了解有关 flashbar 概念的更多信息。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)