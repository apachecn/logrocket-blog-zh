# 深入了解 React Native reactive-log rocket 博客

> 原文：<https://blog.logrocket.com/deep-dive-react-native-reanimated/>

React Native Reanimated 是一个强大、直观的库，允许您为 iOS 和 Android 应用程序创建流畅的动画和交互。虽然 React Native 有许多通用和高性能的[动画库，包括内置的`Animated` API，但我们将深入研究 Reanimated，以发现为什么在我看来它是更好的选择。](https://blog.logrocket.com/the-top-6-animation-libraries-in-react-native/)

我们开始吧！

## 复活的代码执行

Reanimated 的核心优势在于它能够提高 React 原生应用的性能和响应能力，使动画具有只有通过瞬时代码执行才能实现的平滑效果。

为了理解[reanized](https://github.com/software-mansion/react-native-reanimated)如何工作，我们首先需要理解 React Native 如何执行代码。

### 反应本机线程

React Native 有三个独立的线程，允许执行时间密集型的 JavaScript 代码，而不会影响 UI 的响应。UI 线程是本机线程。它在 iOS 上运行 Swift 或 Objective C，在 Android 上运行 Java 或 Kotlin。应用程序的 UI 只在 UI 线程上操作。

JavaScript 线程负责呈现逻辑，比如显示哪些视图以及以什么方式显示。它通过 JavaScript 引擎单独执行 JavaScript。最后，桥支持 UI 和 JavaScript 线程之间的异步通信。为了避免降低性能，它应该只用于传输少量数据。

这些交互从 JavaScript 线程开始，必须反映在主线程上，这可能会在处理事件驱动的交互时造成问题。

### 复活与`Animated` API

因为 UI 和 JavaScript 线程之间的通信是异步的，`Animated` API 会将更新延迟至少一帧，大约持续 16.67 毫秒。当 JavaScript 线程还在执行其他进程(如运行 React diffing 和处理网络请求)时，延迟会持续更长时间。

被称为丢帧，这些延迟会损害你的 UX，使动画显得笨拙。Reanimated 通过从 JavaScript 线程中移除动画和事件处理逻辑并直接在 UI 线程上运行来解决这个问题。

此外，Reanimated 还定义了工作小程序，即可以移动到单独的 JavaScript 虚拟机并在 UI 线程上同步执行的小块 JavaScript 代码。工作小程序使动画一被触发就运行，从而创建更令人满意的 UX。

## 共享价值观

共享值类似于常规 React 应用程序中的有状态数据，它存储动态数据，您可以在 Reanimated 中将这些数据动画化。然而，与有状态数据不同，共享值在 UI 线程和 JavaScript 线程之间共享数据。

当这些共享值中的数据被更新时，重新激活的库会注册该更改并执行动画，类似于状态被更新时 React 重新渲染组件的方式。

创建一个共享值看起来非常类似于用`useState`钩子创建一个状态；只需使用`useSharedValue`挂钩即可。

假设我们想要创建一个当按钮被按下时改变高度的盒子。我们将定义一个`boxHeight`变量作为共享值，这样我们可以在动画过程中在 UI 线程和 JavaScript 线程之间共享它:

```
const boxHeight = useSharedValue(150);

```

在上面的例子中，我们创建了一个名为`boxHeight`的共享值，并将其初始值设置为`150`。要在代码的后面访问`boxHeight`值，必须引用`boxHeight.value`，而不仅仅是`boxHeight`。

`useSharedValue`钩子返回一个对象，初始值保存到该对象的`.value`属性中。要更新共享值，这通常是在一些预先确定的用户输入之后完成的，只需更新函数中的`.value`属性。重新激活将记录更改:

```
function toggleHeight() {
  boxHeight.value === 450 ?
  boxHeight.value = 150 :
  boxHeight.value = 450;
}

```

现在，让我们来探索如何在 Reanimated 的 worklets 中使用这些共享值。

## 复活的工作台

Worklets 是简单的函数，允许我们在 UI 线程上同步执行 JavaScript 代码。通常，worklets 返回 React 组件的样式属性。工作小程序由它引用的共享值的任何变化触发。

要声明一个 worklet，我们可以在函数定义的开头使用`worklet`指令。在下面的代码块中，我们使用`worklet`指令声明了`boxAnimation`函数，表明该函数是一个工作小程序。我们正在使用共享值`boxHeight`返回更新的`height`属性:

```
const boxAnimation = () => {
  'worklet';
  return {
    height: withTiming(boxHeight.value, {duration: 750})
  };
};

```

更常见的是，我们可以使用`useAnimatedStyle`钩子来声明一个 worklet，这允许我们传入一个回调函数作为参数。现在，回调函数将被视为一个工作小程序:

```
const boxAnimation = useAnimatedStyle(() => {
  return {
    height: withTiming(boxHeight.value, {duration: 750})
  };
});

```

在声明工作流的两种方法中，我推荐使用`useAnimatedStyle`钩子。要使用`worklet`指令方法，我们必须添加一个调用`runOnUI`方法并将`boxAnimation`函数作为参数传递的辅助函数。另一方面，`useAnimatedStyle`钩子抽象出那个次要函数，自动运行传递给 UI 线程上的`useAnimatedStyle`钩子的回调。

在我们的两个例子中，每当`boxHeight.value`的值被更新时，worklet 将触发一个动画，显示盒子垂直扩展或收缩。

## 实用方法

### `withTiming`

在上面的例子中，我们使用了`withTiming`实用程序方法，它允许我们创建一个简单的动画，使从起点逐渐过渡到终点，从而使我们能够控制过渡的持续时间和加速度。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`withTiming`需要两个参数。第一个是必需的，是将要更新的共享值。在我们这里是`boxHeight`。

第二个可选参数是一个具有两个属性的对象。`duration`控制动画的时长，`easing`控制动画的加速和减速。`duration`的默认值为 300 毫秒，`easing`的默认值为`in-out quad easing`。

### `withSpring`

`withSpring`方法类似于`withTiming`，但是，它创建了一个不同的动画效果，其中元素在到达其结束位置之前通过其端点。

`withSpring`只有一个必选参数，即要更新的共享值。它还有下面列出的[六个可选参数](https://docs.swmansion.com/react-native-reanimated/docs/api/withSpring/)。然而，默认值将满足大多数用例:

*   `damping` : 10
*   `mass` : 1
*   `stiffness` : 100
*   `overshootClamping`:假
*   `restDisplacementThreshold` : 0.01
*   `restSpeedThreshold` : 2

## `useAnimatedStyle`挂钩

在我们前面的例子中，`useAnimatedStyle`钩子创建了一个将共享值`boxHeight`与一个在其样式属性中使用了`boxHeight.value`的组件链接起来的工作小程序。当赋予 React 组件属性时，我们必须使用我们可以制作动画的组件版本。

例如，我们应该使用一个`<Animated.View />`标签，而不是使用一个`<View />`标签。`<Animated.View />`的所有孩子都将服从应用于该父母的动画。

为组件设置样式时，请确保将样式作为数组传递。第一个元素是一个对象，包含您通常会使用的所有样式，包括`height`。第二个元素是前面定义的工作小程序。

现在，让我们结合我们已经介绍过的所有复活工具来创建一个简单的灰色框，当我们按下按钮时，它会扩展和收缩:

```
// import statements where we add the functionality to use hooks and methods from Reanimated
import React from 'react';
import { View, Button } from 'react-native';
import Animated, { useAnimatedStyle, useSharedValue, withTiming } from 'react-native-reanimated';
export default function ExpandingTextBox() {
  // creating shared value via useSharedValue
  const boxHeight = useSharedValue(150);
  // creating worklet via useAnimatedStyle, and incorporating the withTiming method
  const boxAnimation = useAnimatedStyle(() => {
    return {
      height: withTiming(boxHeight.value, {duration: 750})
    }
  });
  // function that toggles the value of boxHeight so it can expand and contract
  function toggleHeight() {
    boxHeight.value === 450 ?
    boxHeight.value = 150 :
    boxHeight.value = 450;
  };
  // styles to use on our grey box
  const styles = {
    box: {
      width: 400,
      height: 150,
      backgroundColor: 'grey',
      borderRadius: 15,
      margin: 100,
      padding: 20,
      display: 'flex'
    }
  };
  return (
    <View style={styles.app}>
      {/* Animated.View component, with the typical styles contained in styles.box,
and the worklet "boxHeight" that updates the height property alongside it */}
      <Animated.View style={[styles.box, boxAnimation]}>
        {/* button that fires off toggleHeight() function every time it's pressed */}
        <Button title='More' onPress={() => toggleHeight()} />
      </Animated.View>
    </View>
  )
};

```

上面代码的输出将类似于下面的动画:

![Reanimated Gif Dropdown](img/ea032f1f1e39b08d17ce321e29d4cb62.png)

让我们添加另一个共享值和 worklet 来控制文本不透明度。我们还将添加一些更多的样式细节。

现在，我们有了一个全功能的动画文本下拉菜单！

![Reanimated Gif Styling](img/1f94e8c71a96d3f47f0e34287a946f85.png)

## 结论

Reanimated 从 JavaScript 线程卸载事件驱动的交互，而是在 UI 线程上同步执行它们。有了 Reanimated，您再也不用担心丢帧或限制 JavaScript 线程的工作负载。

关于 Reanimated 的方法、钩子和动画的更多信息，我推荐查看 [Reanimated 文档](https://docs.swmansion.com/react-native-reanimated/)。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)