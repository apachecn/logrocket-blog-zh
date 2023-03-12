# React 原生手势处理程序:滑动、长按和更多日志

> 原文：<https://blog.logrocket.com/react-native-gesture-handler-swipe-long-press-and-more/>

现在大多数应用程序都允许用户通过滑动、双击、挤压、长按等方式与关键组件进行交互。当在您的移动应用程序中正确实施时，手势可以为您的用户提供迷人、自然和直观的体验。事实上，你可以用手势代替大多数可见的控件，比如按钮和图标点击。

有几个包可以用来在 React 本机应用程序中实现手势。最受欢迎和推荐的库是`[react-native-gesture-handler](https://www.npmjs.com/package/react-native-gesture-handler)`。该库公开了特定于平台(即 Android 和 iOS)的本机触摸和手势，以进行本机反应。

虽然 React Native 中的手势可以使用内置的[手势响应系统](https://reactnative.dev/docs/gesture-responder-system)来处理，但这种实现有一些限制，因为它运行在 JavaScript 线程上。因此，每次执行手势事件时，它都会通过 React 本机桥将数据发送到接口，这可能会导致性能下降。

[React Native 手势处理程序](https://docs.swmansion.com/react-native-gesture-handler/)使您能够在 React Native 中实现高性能手势，因为它运行在本机线程中，并遵循特定于平台的行为，这反过来会带来更好的性能。

React 原生手势处理程序库附带了许多有用的手势，包括:

*   `PanGestureHandler`
*   `TapGestureHandler`
*   `LongPressGestureHandler`
*   `PinchGestureHandler`

除了上述手势，我们还将演示如何实现拉至刷新、可滑动和双击手势。

## 初始化新的世博会应用程序

我们将从初始化一个新的 Expo 应用程序开始。如果您的计算机上还没有安装 Expo，请运行以下命令:

```
# installing expo CLI globally
npm install --global expo-cli

```

要创建新的 Expo 应用程序，请在您的终端上运行以下命令

```
# Create a project named react-native-gestures

expo init react-native-gestures

```

导航到您的项目目录，运行以下命令启动您的应用程序:

```
expo start

```

按`i`在 [iOS 模拟器](https://docs.expo.io/workflow/ios-simulator/)中打开，或者按`a`在 [Android 模拟器或连接设备](https://docs.expo.io/workflow/android-studio-emulator/)中打开。确保您的模拟器或仿真器已经设置好了。

现在让我们开始在 React 本地应用中实现和管理手势。

## 平移手势

为了使用`[react-native-gesture-handler](https://www.npmjs.com/package/react-native-gesture-handler)`库在 React Native 中实现平移手势，我们将使用`PanGestureHandler`。`PanGestureHandler`是一个连续的手势处理器，当用户平移(拖动)一个元素时，它生成手势事件流。

要开始使用`PanGestureHandler`，我们必须从我们之前安装的`react-native-gesture-handler`库导入它:

```
import { PanGestureHandler } from 'react-native-gesture-handler';

```

接下来，我们需要用之前导入的`PanGestureHandler`组件包装我们想要应用平移手势的元素:

```
// in your return block
<PanGestureHandler>
  <View style={styles.square}/>
</PanGestureHandler>

```

为了让事情四处移动，我们需要使用名为`onGestureEvent`的基本手势处理程序道具，并向其传递一个回调函数。

让我们创建一个函数，稍后我们将把它传递给`onGestureEvent`:

```
// dont't forget to import Animated from react native
onPanGestureEvent = Animated.event(
    [
      {
        nativeEvent: {
          translationX: this.translateX,
          translationY: this.translateY,
        },
      },
    ],
    { useNativeDriver: true }
  );

```

注意，我们在这里使用了一个类组件。

让我们将刚刚创建的函数传递给`PanGestureHandler`组件中的`onGestureEvent` prop。

```
<PanGestureHandler onGestureEvent={this.onPanGestureEvent}> </PanGestureHandler>

```

因为我们想让我们的`View`组件具有动画效果，所以让我们用`Animated.View`替换它，并在我们的`transform`数组中添加`translateX`和`translateY`属性来指定平移手势沿 X 和 Y 轴的平移。

```
  <Animated.View
            style={[
              styles.square,
              {
                transform: [
                  {
                    translateX: this.translateX,
                  },
                  {
                    translateY: this.translateY,
                  },
                ],
              },
            ]}
          />

```

![Pan Gesture](img/24c2649390a930504618cb1aed3953a9.png)

Pan Gesture in React Native.

以下是使用 React 本机手势处理程序创建平移手势的完整代码:

```
import React, { Component } from 'react';
import { StatusBar } from 'expo-status-bar';
import { PanGestureHandler } from 'react-native-gesture-handler';
import { Animated, StyleSheet, Text } from 'react-native';
export default class PanGesture extends Component {
  translateX = new Animated.Value(0);
  translateY = new Animated.Value(0);
  onPanGestureEvent = Animated.event(
    [
      {
        nativeEvent: {
          translationX: this.translateX,
          translationY: this.translateY,
        },
      },
    ],
    { useNativeDriver: true }
  );
  render() {
    return (
      <>
        <Text>Pan Gesture Handler</Text>
        <PanGestureHandler onGestureEvent={this.onPanGestureEvent}>
          <Animated.View
            style={[
              styles.square,
              {
                transform: [
                  {
                    translateX: this.translateX,
                  },
                  {
                    translateY: this.translateY,
                  },
                ],
              },
            ]}
          />
        </PanGestureHandler>
        <StatusBar style="auto" />
      </>
    );
  }
}
const styles = StyleSheet.create({
  square: {
    width: 150,
    height: 150,
    backgroundColor: '#28b5b5',
    marginTop: 22,
  },
});

```

## `TapGestureHandler`

使用`TapGestureHandler`，我们可以实现单击和双击手势。让我们从单击手势开始，然后转到双击手势。

### 单击手势

![Single Tap Gesture](img/7bc3f864c7d13f2ae6b1a48429a81420.png)

Single-tap gesture with `TapGestureHandler` using React Native Gesture Handler.

我们将使用`TapGestureHandler`组件实现单击手势。注意我们添加了`onHandlerStateChange`道具并传递了一个函数:

```
import { View, StyleSheet, Text } from 'react-native';
import { TapGestureHandler, State } from 'react-native-gesture-handler';

export default function TapGesture() {

  const onSingleTapEvent = (event) => {
    if (event.nativeEvent.state === State.ACTIVE) {
      alert('Hey single tap!');
    }
  };

  return (
    <>
      <Text>Double and Single Tap Gesture Handler</Text>
      <TapGestureHandler
        onHandlerStateChange={onSingleTapEvent}
        waitFor={doubleTapRef}
      >
        <View style={styles.square} />
      </TapGestureHandler>
    </>
  );
}

```

### 双击

![Double-tap Gesture](img/564da1a1b2f3cdf790887314feb51f28.png)

Double-tap gesture with `TapGestureHandler` using React Native Gesture Handler.

单击和双击手势实现的主要区别是`numberOfTaps`道具，它被传递给`TapGestureHandler`组件。注意，我们将`2`传递给`numberOfTaps`来指定我们想要触发`onHandlerStateChange`事件回调:

```
import React, { useRef, useState } from 'react';
import { View, StyleSheet, Text } from 'react-native';
import { TapGestureHandler, State } from 'react-native-gesture-handler';

export default function TapGesture() {

  const [likeColour, setLikeColour] = useState('#28b5b5');
  const doubleTapRef = useRef(null);

  const onDoubleTapEvent = (event) => {
    if (event.nativeEvent.state === State.ACTIVE) {
      likeColour === '#28b5b5'
        ? setLikeColour('red')
        : setLikeColour('#28b5b5');
    }
  };
  const styles = StyleSheet.create({
    square: {
      width: 150,
      height: 150,
      backgroundColor: likeColour,
      marginTop: 22,
      marginBottom: 22,
    },
  });
  return (
    <>
      <Text>Double and Single Tap Gesture Handler</Text>
        <TapGestureHandler
          ref={doubleTapRef}
          onHandlerStateChange={onDoubleTapEvent}
          numberOfTaps={2}
        >
          <View style={styles.square} />
      </TapGestureHandler>
    </>
  );
}

```

注意，我们在这里使用了一个功能组件，因为我们使用了`useRef`钩子。

### 在一个组件中实现单击和双击

![Double- and Single-tap Gestures](img/b8985d48594d36830bbf5612d1893bbc.png)

Double and single tap gesture with `TapGestureHandler` using React Native Gesture Handler.

使用`TapGestureHandler`，我们可以实现一个手势，其中一个事件在单击时调用，另一个事件在双击时调用。LinkedIn 移动应用程序就是一个很好的例子，点击一下就可以打开帖子，双击就可以为帖子点赞。

```
import React, { useRef, useState } from 'react';
import { View, StyleSheet, Text } from 'react-native';
import { TapGestureHandler, State } from 'react-native-gesture-handler';

export default function TapGesture() {

  const [likeColour, setLikeColour] = useState('#28b5b5');
  const doubleTapRef = useRef(null);

  const onSingleTapEvent = (event) => {
    if (event.nativeEvent.state === State.ACTIVE) {
      alert('Hey single');
    }
  };

  const onDoubleTapEvent = (event) => {
    if (event.nativeEvent.state === State.ACTIVE) {
      likeColour === '#28b5b5'
        ? setLikeColour('red')
        : setLikeColour('#28b5b5');
    }
  };

  const styles = StyleSheet.create({
    square: {
      width: 150,
      height: 150,
      backgroundColor: likeColour,
      marginTop: 22,
      marginBottom: 22,
    },
  });

  return (
    <>
      <Text>Double and Single Tap Gesture Handler</Text>
      <TapGestureHandler
        onHandlerStateChange={onSingleTapEvent}
        waitFor={doubleTapRef}
      >
        <TapGestureHandler
          ref={doubleTapRef}
          onHandlerStateChange={onDoubleTapEvent}
          numberOfTaps={2}
        >
          <View style={styles.square} />
        </TapGestureHandler>
      </TapGestureHandler>
    </>
  );
}

```

为了实现这一点，我们需要将`waitFor={doubleTapRef}`添加到单击`TapGestureHandler`组件中。当`doubleTapRef`为真时，on `onSingleTapEvent`不会被调用。

## 可旋转的手势

![Swipeable Gestures](img/37879a85d0880dc66c11691eb0d9430f.png)

Swipeable gestures with `Swipeable` component using React Native Gesture Handle.

为了演示如何实现可滑动手势，让我们创建一个项目列表，用户可以在其中向右或向左滑动，并调用某些事件或方法。

让我们创建一个平面列表组件，并将我们的数据传递给`data` props:

```
<FlatList
          data={todoList}
          keyExtractor={(item) => item.id}
          renderItem={({ item }) => <ListItem {...item} />}
          ItemSeparatorComponent={() => <Separator />}
        />

```

请注意，`renderItem` props 正在返回一个`ListItem`组件。这代表了我们的待办事项列表，如上面的演示所示。

现在让我们创建我们的`ListItem`组件，并使其可切换。

首先，从`react-native-gesture-handler`包中导入`Swipeable`组件:

```
import Swipeable from 'react-native-gesture-handler/Swipeable';

```

接下来，用我们之前导入的`Swipeable`组件包装`View`和`Text`组件。有了这个，我们的组件就可以切换了。但是在真实的场景中，您可能希望在用户向左或向右滑动时调用某种事件或函数。

要渲染左边的容器，使用`renderLeftActions`道具并传入一个组件。要渲染右边的容器，使用`renderRightActions`道具。

向左滑动时，我们有`delete`容器，向右滑动时，我们有`bookmark`容器。`renderLeftActions`和`renderRightActions`道具让这成为可能。

`onSwipeableRightOpen` props 接受一个方法，当从右到左的可滑动手势完成时，即当右动作面板打开时，调用该方法。`onSwipeableLeftOpen` props 接受一个方法，该方法在左侧操作面板打开时被调用。

```
const ListItem = ({ text }) => (
  <Swipeable
    renderLeftActions={LeftSwipeActions}
    renderRightActions={rightSwipeActions}
    onSwipeableRightOpen={swipeFromRightOpen}
    onSwipeableLeftOpen={swipeFromLeftOpen}
  >
    <View
      style={{
        paddingHorizontal: 30,
        paddingVertical: 20,
        backgroundColor: 'white',
      }}
    >
      <Text style={{ fontSize: 24 }} style={{ fontSize: 20 }}>
        {text}
      </Text>
    </View>
  </Swipeable>
);

```

以下是可滑动手势的完整代码:

```
import React from 'react';
import {
  SafeAreaView,
  StyleSheet,
  View,
  Text,
  StatusBar,
  FlatList,
} from 'react-native';
import Swipeable from 'react-native-gesture-handler/Swipeable';
const todoList = [
  { id: '1', text: 'Learn JavaScript' },
  { id: '2', text: 'Learn React' },
  { id: '3', text: 'Learn TypeScript' },
];
const Separator = () => <View style={styles.itemSeparator} />;
const LeftSwipeActions = () => {
  return (
    <View
      style={{ flex: 1, backgroundColor: '#ccffbd', justifyContent: 'center' }}
    >
      <Text
        style={{
          color: '#40394a',
          paddingHorizontal: 10,
          fontWeight: '600',
          paddingHorizontal: 30,
          paddingVertical: 20,
        }}
      >
        Bookmark
      </Text>
    </View>
  );
};
const rightSwipeActions = () => {
  return (
    <View
      style={{
        backgroundColor: '#ff8303',
        justifyContent: 'center',
        alignItems: 'flex-end',
      }}
    >
      <Text
        style={{
          color: '#1b1a17',
          paddingHorizontal: 10,
          fontWeight: '600',
          paddingHorizontal: 30,
          paddingVertical: 20,
        }}
      >
        Delete
      </Text>
    </View>
  );
};
const swipeFromLeftOpen = () => {
  alert('Swipe from left');
};
const swipeFromRightOpen = () => {
  alert('Swipe from right');
};
const ListItem = ({ text }) => (
  <Swipeable
    renderLeftActions={LeftSwipeActions}
    renderRightActions={rightSwipeActions}
    onSwipeableRightOpen={swipeFromRightOpen}
    onSwipeableLeftOpen={swipeFromLeftOpen}
  >
    <View
      style={{
        paddingHorizontal: 30,
        paddingVertical: 20,
        backgroundColor: 'white',
      }}
    >
      <Text style={{ fontSize: 24 }} style={{ fontSize: 20 }}>
        {text}
      </Text>
    </View>
  </Swipeable>
);
const SwipeGesture = () => {
  return (
    <>
      <StatusBar />
      <SafeAreaView style={styles.container}>
        <Text style={{ textAlign: 'center', marginVertical: 20 }}>
          Swipe right or left
        </Text>
        <FlatList
          data={todoList}
          keyExtractor={(item) => item.id}
          renderItem={({ item }) => <ListItem {...item} />}
          ItemSeparatorComponent={() => <Separator />}
        />
      </SafeAreaView>
    </>
  );
};
const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  itemSeparator: {
    flex: 1,
    height: 1,
    backgroundColor: '#444',
  },
});
export default SwipeGesture;

```

## 按住/长按手势

![Hold Gesture](img/e80d1ea35ef8bb98847d52c163f8e265.png)

Long press / hold gestures with `LongPressGestureHandler` using React Native Gesture Handle.

使用 React 本机手势处理程序，实现长按/保持手势非常简单。

基本上，我们需要用从`react-native-gesture-handler`导入的`LongPressGestureHandler`包装我们想要实现手势的 React 原生组件，然后添加`onHandlerStateChange`道具，当用户按住组件一段给定的时间时，就会触发一个方法。为了指定长按的持续时间，我们传入了`minDurationMs`，它接受一个以毫秒表示的数字。

下面是上面演示的代码:

```
import React from 'react';
import { View, StyleSheet } from 'react-native';
import { LongPressGestureHandler, State } from 'react-native-gesture-handler';

export default function LongPressGesture() {
  const onLongPress = (event) => {
    if (event.nativeEvent.state === State.ACTIVE) {
      alert("I've been pressed for 800 milliseconds");
    }
  };

  return (
    <LongPressGestureHandler
      onHandlerStateChange={onLongPress}
      minDurationMs={800}
    >
      <View style={styles.box} />
    </LongPressGestureHandler>
  );
}
const styles = StyleSheet.create({
  box: {
    width: 150,
    height: 150,
    backgroundColor: '#28b5b5',
    marginTop: 22,
    marginBottom: 22,
  },
});

```

## 双指缩放

顾名思义，这是一个连续的手势，可以跟踪两个手指之间的距离，并使用这些信息来缩放您的内容。

```
render() {
    return (
      <PinchGestureHandler
        onGestureEvent={this.onPinchGestureEvent}
        onHandlerStateChange={this.onPinchHandlerStateChange}
      >
        <Animated.View
          style={[
            styles.pinchableImage,
            {
              transform: [{ perspective: 1 }, { scale: this.scale }],
            },
          ]}
        ></Animated.View>
      </PinchGestureHandler>
    );
  }

```

![Pinch-to-zoom Gesture](img/5da246b303bff7589ede9cf0a329f332.png)

Pinch to zoom gestures with `PinchGestureHandler` using React Native Gesture Handle.

为了完成这项工作，我们需要将`onGestureEvent`道具添加到我们的`PinchGestureHandler`中，然后在我们的`transform`数组中设置`{ scale: this.scale }`对象:

```
import React, { Component } from 'react';
import { View, Image, StyleSheet, Animated } from 'react-native';
import { PinchGestureHandler, State } from 'react-native-gesture-handler';
export default class PinchToZoom extends Component {
  baseScale = new Animated.Value(1);
  pinchScale = new Animated.Value(1);
  scale = Animated.multiply(this.baseScale, this.pinchScale);
  lastScale = 1;
  onPinchGestureEvent = Animated.event(
    [{ nativeEvent: { scale: this.pinchScale } }],
    { useNativeDriver: true }
  );
  onPinchHandlerStateChange = (event) => {
    if (event.nativeEvent.oldState === State.ACTIVE) {
      this.lastScale *= event.nativeEvent.scale;
      this.baseScale.setValue(this.lastScale);
      this.pinchScale.setValue(1);
    }
  };
  render() {
    return (
      <PinchGestureHandler
        onGestureEvent={this.onPinchGestureEvent}
        onHandlerStateChange={this.onPinchHandlerStateChange}
      >
        <Animated.View
          style={[
            styles.pinchableImage,
            {
              transform: [{ perspective: 1 }, { scale: this.scale }],
            },
          ]}
        ></Animated.View>
      </PinchGestureHandler>
    );
  }
}
const styles = StyleSheet.create({
  pinchableImage: {
    width: 250,
    height: 250,
    backgroundColor: '#28b5b5',
    marginTop: 22,
    marginBottom: 22,
  },
});

```

## 拉至刷新

要在 React Native 中实现拉刷新，不需要外部库。只需添加`onRefresh`属性，它接受`FlatList`组件中的一个函数，并将`refreshing`属性设置为布尔值。

```
// at the top of your component
  const [refreshing, setrefreshing] = useState(false);

<FlatList
        data={data}
        renderItem={renderItem}
        keyExtractor={(item) => item.id}
        refreshing={refreshing}
        onRefresh={onRefresh}
      />

```

默认情况下，我们将`refreshing`设置为`false`，然后，当调用`onRefresh`方法时，我们使用`setrefreshing`将`refreshing`状态设置为`true`:

![Pull-to-refresh Gesture](img/5eab89cddc87a13c315c98b07a31a34c.png)

Pull-to-refresh gesture demo in React Native.

下面是上面演示的完整代码:

```
import React, { useState } from 'react';
import {
  SafeAreaView,
  View,
  FlatList,
  StyleSheet,
  Text,
  StatusBar,
} from 'react-native';
const DATA = [
  {
    id: '1',
    title: 'Take coffee',
  },
  {
    id: '2',
    title: 'Write some code',
  },
  {
    id: '3',
    title: 'Take the test',
  },
  {
    id: '4',
    title: 'Excercise',
  },
];
const Item = ({ title }) => (
  <View style={styles.item}>
    <Text style={styles.title}>{title}</Text>
  </View>
);
export default function PullToRefresh() {
  const [refreshing, setrefreshing] = useState(false);
  const [data, setdata] = useState(DATA);
  const onRefresh = () => {
    setrefreshing(true);
    setTimeout(() => {
      setdata((data) => [
        ...data,
        {
          id: '57878',
          title: 'Take a walk in the park',
        },
      ]);
      setrefreshing(false);
    }, 2000);
  };
  const renderItem = ({ item }) => <Item title={item.title} />;
  return (
    <SafeAreaView style={styles.container}>
      <FlatList
        data={data}
        renderItem={renderItem}
        keyExtractor={(item) => item.id}
        refreshing={refreshing}
        onRefresh={onRefresh}
      />
    </SafeAreaView>
  );
}
const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: StatusBar.currentHeight || 0,
  },
  item: {
    backgroundColor: '#fad586',
    padding: 20,
    marginVertical: 8,
    marginHorizontal: 16,
  },
  title: {
    fontSize: 20,
  },
});

```

## 结论

在 React Native 中实现手势可以帮助改善用户体验，让用户感觉你的应用很自然。

在本教程中，我们介绍了 React 原生应用中手势的实现和管理，包括可滑动、平移、双击和单击、缩放等。

这个演示的所有代码都可以在 [GitHub](https://github.com/ejirocodes/react-native-gesture-management) 上获得。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)