# 优化 React 本机性能

> 原文：<https://blog.logrocket.com/optimize-react-native-performance/>

***编者按:*** *这篇 React Native 的文章最后一次更新是在 2022 年 10 月 25 日，内容包括 react-native-fast-image 库的信息以及 react-navigation 和 react-native-navigation 库的代码示例。*

出色的用户体验应该是任何应用程序开发的核心目标。虽然 [React Native](https://reactnative.dev/) 试图提供开发高性能应用程序所需的一切，但有时您必须手动优化您的应用程序。要做到这一点，开发人员需要从项目一开始就有一个性能优化的心态。

大多数现代移动设备在一秒钟内显示 60 帧——这意味着你有 16.67 毫秒来显示一帧,以便应用程序保持高性能。如果做不到这一点，将会导致性能下降，UI 甚至可能看起来没有响应。

在本文中，我们将回顾如何使用以下步骤来优化 React 本机应用程序的性能:

## 如何反应本土作品？

React 本地应用程序由两部分组成:JavaScript 代码和本地代码。这两者是完全不同的，通常不能单独相互通信。

它们只能通过 React 本机桥(有时称为影子树)来实现。桥梁位于 JavaScript 代码和本机代码之间，将序列化的 JSON 对象从 JavaScript 线程发送到本机线程，反之亦然。

虽然[脸书正在致力于改进 React 原生架构](https://engineering.fb.com/2019/07/12/android/hermes/)(也是[你不应该放弃 React 原生的一个原因](https://blog.logrocket.com/why-keep-faith-react-native/))这将使 JavaScript 代码能够通过 JavaScript 接口(JSI)直接与原生代码对话。在处理复杂流程时，当前架构中很容易出现性能问题。这就是为什么在将 React 原生应用程序投入生产之前对其进行优化非常重要。

让我们通过几个代码示例来看看提高 React 本机应用程序性能的一些最佳实践。

## 使用`FlatList`或`SectionList`在 React Native 中呈现大型列表

如果你有一个很大的列表，一次渲染所有的条目会导致性能问题，但是用`FlatList`延迟加载可以提高性能。

`FlatList`组件仅呈现将在屏幕上显示的项目，并在它们不再显示时移除它们。这节省了大量内存，使应用程序速度更快:

```
import React from 'react'
import {FlatList} from 'react-native'

const data = [
  {
    id: 1,
    text: 'First'
  },
  {
    id: 2,
    text: 'Second'
  },
  ...
]

const App = () =>{
    const renderItem = ({item}) =>(
        <View>
          <Text>{item.text}</Text>
        </View>
    )
    return (
        <FlatList
          data={data}
          renderItem={renderItem}
          keyExtractor={item => item.id}
        />
    )
}

```

`FlatList`和`SectionList`的作用相似。两者都可以提高你的应用程序的性能。但是，`SectionList`更适合渲染截面。如果您需要更大的灵活性，也可以使用`VirtualizedList`。

也可以用`ListView`来呈现列表，虽然这可以用于小列表，但不推荐用于大列表。虽然可以用`map`来呈现列表，但是不建议在 React Native 中这样做。

## 删除所有控制台语句

控制台语句对于调试 JavaScript 代码是必要的，但它们仅用于开发目的。如果在绑定之前没有删除这些语句，可能会导致 React 本机应用程序出现严重的性能问题。

虽然你可以安装插件如`babel-plugin-transform-remove-console`来从产品中删除这些语句，但如果你不想给你的应用程序增加额外的依赖，最好手动删除它们。

## 昂贵的计算

React 在 React v16.6 中引入了`memo` HOC(高阶组件)以防止不必要的重新渲染，并在 React v16.8 中引入了`useMemo` hook 以优化昂贵的计算。

然而，也可以使用`useCallback`钩子来实现。`useMemo`和`useCallback`的主要区别在于`useMemo`返回一个记忆化的值，而`useCallback`返回一个记忆化的回调。

让我们来看看每一个。

### `React.memo`高阶组件(HOC)

`React.memo`被引入到功能组件中，以达到与`React PureComponents`在类组件中服务相同的目的。`memo`防止组件不必要的重新渲染，有助于优化应用程序。

然而，像其他优化技术一样，`memo`应该只在必要时使用。在某些情况下，不必要的重新渲染不会对性能产生太大影响。

这里有一个例子来说明`memo`:

```
import React from 'react'
import {View, Text, TouchableOpacity} from 'react-native'

const Element = ({children, value, setValue}) =>{
  const handleOperation = () => setValue(value * 2)
  return (
        <View>
            <Text>{value}</Text>
            <TouchableOpacity onPress={handleOperation}>
               {children}
            </TouchableOpacity>
        </View>
    )
  }

export default Element
import React, {useState} from 'react'
import {View} from 'react-native'
import Element from './Element'

const App = () =>{
  const [firstNum, setFirstNum] = useState(5)
  const [secondNum, setSecondNum] = useState(5)
  return(
    <View>
      <Element setValue={setFirstNum} value={firstNum} > Add First </Element>
      <Element setValue={setSecondNum} value={secondNum} > Add Second </Element>
    </View>
  ) 
}

```

上面代码的问题是，当任何一个按钮被按下时，两个按钮都会重新呈现，即使只有被按下按钮的状态会改变。

这可以通过用`React.memo` HOC 包裹`Element`组件来解决。下面是如何做到这一点:

```
import React, {memo} from 'react'
import {View, Text, TouchableOpacity} from 'react-native'

const Element = ({children, value, setValue}) =>{
  const handleOperation = () => setValue(value * 2)
  return (
        <View>
            <Text>{value}</Text>
            <TouchableOpacity onPress={handleOperation}>
               {children}
            </TouchableOpacity>
        </View>
    )
  }

export default memo(Element)

```

这将修复重新渲染问题。但是，只有当重新渲染导致性能问题时，才应该使用它。

### `useMemo`钩子

`useMemo`返回函数的记忆值。然而，它应该只在执行昂贵的计算时使用。

例如，假设我们想通过评级过滤来自 API 的一些数据。我们可以将计算记忆为仅在值改变时重新计算结果:

```
const data = [
  {id: 1, state: 'Texas', rating: 4.5},
  {id: 2, state: 'Hawaii', rating: 3},
  {id: 3, state: 'Illinois', rating: 4},
  {id: 4, state: 'Texas', rating: 5},
  {id: 5, state: 'Ohio', rating: 4.5},
  {id: 6, state: 'Louisiana', rating: 3},
  {id: 7, state: 'Texas', rating: 2},
  ...
  {id: 1000, state: 'Illinois', rating: 4.5},
]

```

如果我们希望根据评级过滤数据(没有记忆)，我们可能会用尽大量内存。

因此，我们不希望在其他组件重新渲染时不必要地重新计算这些值。我们希望仅在从属评级发生变化时重新呈现或重新计算。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们看看如何通过`useMemo`实现这一点:

```
import React, {useMemo} from 'react'
import {FlatList} from 'react-native'
import data from './data'

const App = () =>{
    const rateCompare = 3;

    const computedValue = useMemo(() => {
        //supposed computationally intensive calculation
        const result = data.filter((d) => d.rating > rateCompare);
        return result;
    }, [rateCompare]);

      const renderItem = ({ item }) => (
          <View>
            <Text>{item.state}</Text>
          </View>
      );
    return (
        <FlatList
          data={computedValue}
          renderItem={renderItem}
          keyExtractor={item => item.id}
        />
    )
}

```

我们假设我们有来自 API 的大量数据，并且需要执行计算密集型的计算。虽然我们使用了简单的过滤操作，但我们可以在这里进行一系列计算。这是`useMemo`钩子的一个很好的用例。

通过使用`useMemo`，我们可以缓存(memoize)依赖数组中指定值的结果。例如，如果第一次运行时`rateCompare`常量为`3`，那么无论何时`rateCompare`的值为`3`，该函数都不会重新计算，即使整个组件重新渲染。它只会在值改变时重新计算。

### `useCallback`钩子

`useCallback`钩子类似于`useMemo`，但是它返回一个记忆化的回调:

```
import React, {useState, useEffect, useCallback} from 'react'
import {FlatList} from 'react-native'
import data from './data'

const App = () =>{
    const [values, setValues] = useState([]);
    const rateCompare = 3;

    const valuesCallback = useCallback(() => {
        //supposed computationally intensive calculation
        const result = data.filter((d) => d.rating > rateCompare);
        setValues(result);
    }, [rateCompare, setValues]);

    useEffect(() => {
        valuesCallback();
    }, [valuesCallback]);

    const renderItem = ({ item }) => (
        <View>
          <Text>{item.state}</Text>
        </View>
    );
    return (
        <FlatList
          data={values}
          renderItem={renderItem}
          keyExtractor={item => item.id}
        />
    )
}

```

这与`useMemo`示例做了同样的事情。但是，因为`useCallback`返回一个函数，我们需要调用那个函数来获取值。这里，我们在一个`useEffect`钩子中调用了函数，然后在一个`FlatList`组件中呈现了值。

我们同样可以在`Button`或`TouchableOpacity`组件中调用该函数。这样，只要按下按钮，计算就会运行:

```
Button onPress={valuesCallback} title="Example button" />

```

虽然`React.memo`可以优化整个组件，但是`useMemo`和`useCallback`可以优化计算或过程。然而，只有在必要的时候才应该使用其中的每一个，否则，它们甚至会加剧性能问题。

建议首先编写没有记忆的计算或组件，只有在导致性能问题时才进行优化(记忆)。

## 调整图像尺寸

在 React 本机应用程序中，映像可能是导致性能问题的重要原因。它们也会给网络应用带来问题，但是浏览器有下载甚至缩放图像的能力。在某些情况下，它甚至可以缓存它们。

但这与移动应用程序不同。React Native 附带了一个`Image`组件，可以很好地处理单个图像，但它在处理许多大尺寸图像时性能很差。

解决这个问题的最好方法是加载你所需要的图片大小。换句话说，在将图片加载到你的应用程序之前，你应该调整图片的大小和比例。

## 本地缓存图像

缓存是 React 原生应用中映像问题的另一种解决方案。它在第一次加载图像时将图像保存在本地，并在后续请求中使用本地缓存。这可以显著提高应用程序的性能。但是只有 iOS 支持使用图像组件[进行缓存，而 Android](https://reactnative.dev/docs/image#imagesource) 不支持。

以下是缓存图像的方法:

```
<Image
  source={{
    uri: 'https://unsplash.it/200/200?image=8'
    cache: 'only-if-cached'
  }}
  style={{ ... }}
/>

```

然而，这种缓存方法并不是最佳的，因为它几乎不能解决问题。当以这种方式缓存图像时，可能会出现一些问题，如闪烁、缓存未命中、应用程序性能差以及从缓存加载的性能差。使用`react-native-fast-image`可以解决这个问题。

除了缓存图像，`[FastImage](https://github.com/DylanVann/react-native-fast-image)`还增加了授权头和其他几个特性:

```
import FastImage from 'react-native-fast-image'

const App = () => (
  <FastImage
        style={{ ... }}
        source={{
            uri: 'https://unsplash.it/200/200?image=8',
            priority: FastImage.priority.normal,
            cache: FastImage.cacheControl.cacheOnly
        }}
    />
)

```

让我们仔细看看`FastImage`组件的属性。顾名思义，`priority`属性定义了图像加载的优先级。对于需要首先加载的图像，您可以将优先级设置为`FastImage.priority.high`。

然而，在优化 React 原生应用的背景下，我们最感兴趣的是`cache`属性。`cache`属性允许我们以三种不同的方式缓存图像:

1.  `FastImage.cacheControl.immutable`:这是默认值。只有当 URI 发生变化时，图像才会更新
2.  `FastImage.cacheControl.web`:来自`source.headers` prop 的配置可以像普通的 web 浏览器一样用于正常的缓存过程
3.  这是限制性最强的选项。只会显示缓存中的图像，不会发出网络请求

## 在 React Native 中使用快速加载图像格式

除了图像大小，图像格式也可能影响应用程序的性能。来自网络背景的开发者更喜欢`JPEG`和`JPG`格式，因为它们允许压缩。然而，对于移动平台来说，情况并非如此。

另一方面，你可以减少组成`PNG`图像的每行像素中独特颜色的数量。这可能会显著减小图像大小。对于移动平台来说，`PNG`格式比`JPG`格式更好，这就足够了。

谷歌在 2010 年推出的`WebP`格式是三种格式中性能最好的。[它支持无损和有损压缩模式](https://developer.android.com/topic/performance/network-xfer)，可以将图像尺寸缩小高达 25-34%。请记住，并非所有移动设备都支持这种格式。受 Android 4.2.1 及更高版本设备和 iOS 14 支持。

## 用`InteractionManager`和`LayoutAnimation`安排动画

如果处理不当，动画会影响 React 本机应用程序的性能。`InteractionManager`的`runAfterInteractions`方法可用于在动画或交互完成后安排长时间运行的同步操作。这可以通过确保动画平稳运行来提高 React 本机应用程序的性能:

```
InteractionManager.runAfterInteractions(() => {
   ...
});

```

如果你关心用户体验，使用`LayoutAnimation`可能更好。这将在下一个布局中运行动画:

```
import React, { useState } from "react";
import { LayoutAnimation } from "react-native";

if (Platform.OS === 'android' && UIManager.setLayoutAnimationEnabledExperimental) {
    UIManager.setLayoutAnimationEnabledExperimental(true);
}

const App = () =>{
  const [animate, setAnimate] = useState(false)

  const handleClick = () =>{
    LayoutAnimation.configureNext(LayoutAnimation.Presets.spring)
    setAnimate(!animate)
  }
  return (
    Button onPress={handleClick} title="Animate" />
  )
}

```

必须设置标志`UIManager.setLayoutAnimationEnabledExperimental(true);`以确保它在 Android 上工作。

## 将本地驱动程序与动画 API 一起使用

在 JavaScript 线程上运行动画不是一个好主意。JS 线程很容易被阻塞，这可能会使动画运行缓慢或根本不运行。

因为[动画 API 是可序列化的](https://reactnative.dev/blog/2017/02/14/using-native-driver-for-animated)，所以有可能在动画开始之前将动画的细节推送到 native。因此，本机代码将在 UI 线程上执行动画。这将确保动画流畅运行，即使 JavaScript 线程被阻塞。

下面是如何用动画 API 设置`useNativeDriver`:

```
import React, {useRef} from 'react'
import {Animated} from 'react-native'

const App = () =>{

  const opacity = useRef(new Animated.value(0)).current

  const showVal = () =>{
    Animated.timing(opacity, {
      toValue: 1,
      duration: 500,
      useNativeDriver: true,
    }).start();
  } 
  ...

  return (
    <View>
        <Animated.View>
          <Animated.Text>Text to show</Animated.Text>
        </Animated.View>
        <Button title="Show" onPress={showVal} />
    </View>
  )
}

```

这里，我们声明了一个变量`opacity`来保存不透明度值。我们将初始值设为`0`，然后使用`Animated.timing`来触发按钮点击时的动画。在`Animated.timing`中`useNativeDriver`被设置为`true`，以将动画的细节发送到本地。

## 删除不必要的库和功能

React 或 React 本机应用程序中的每个库都会在应用程序上留下一些足迹。这就是为什么你应该只在你的应用中添加你需要的库和特性，并删除不相关的依赖和库。

动画、导航、标签和其他功能都会增加屏幕加载时间，因此它们在屏幕上出现得越多，性能就越差。

## 使用爱马仕

[Hermes](https://reactnative.dev/docs/hermes) 是脸书在 2019 年开发的 JavaScript 引擎。它是提高应用程序性能、减少内存使用、减小应用程序大小和缩短应用程序启动时间的必备功能之一。

Hermes 目前在 React Native 中默认是不启用的，但是你可以在你的 app 中轻松启用。

在 React Native v0.64 发布之前，Hermes 只适用于 Android 平台，但现在它也适用于 iOS。

如果您使用的是 React Native 的早期版本，您需要在启用 Hermes 之前首先将其升级到可接受的版本。

要在 Android 上启用 Hermes，请编辑您的`android/app/build.gradle`文件并添加以下规则:

```
  project.ext.react = [
      entryFile: "index.js",
      enableHermes: true
  ]

```

你也可以这样做:

```
def enableHermes = project.ext.react.get("enableHermes", true);

```

如果您正在使用 ProGuard，打开您的`proguard-rules.pro`并添加以下规则:

```
-keep class com.facebook.hermes.unicode.** { *; }
-keep class com.facebook.jni.** { *; }

```

然后清理并重新构建你的应用程序(如果你已经构建了的话)。为此，首先运行`cd android`，然后运行`./gradlew clean`。

如果你正在升级你的 React 原生版本，确保用最新的 [Gradle 版本](https://gradle.org/releases/)相应地编辑`build.gradle`文件。

有趣的是，Hermes for iOS 最近在 2021 年 3 月 12 日发布了新的 React 原生版本 0.64。要在 iOS 上启用 Hermes，将`hermes_enabled`设置为`true`，并在`Podfile`中运行`pod install`:

```
use_react_native!(
   :path => config[:reactNativePath],
   :hermes_enabled => true
)

```

## 将`Reselect`与 Redux 一起使用

像`useMemo`钩子一样，`Reselect`可以用来创建记忆选择器来优化昂贵的计算。然而，与`useMemo`不同的是，这必须与`Redux`一起使用。

假设我们有一个来自 Redux store 的`getPosts`状态，我们可以使用`Reselect`从帖子中选择具有最高赞和用户的帖子。

`Reselect`带有`createSelector`函数，可用于创建记忆选择器，如下所示:

```
import {createSelector} from "reselect"
import _ from "lodash"

//non-memoized selector
export const getAllPosts = (state) => state.allPosts

//memoized selector to get posts with top likes
export const getMostLiked = (likes) => createSelector(
   getAllPost,
    item => item && _.filter(item, (post) => post.likes >= likes)
)

//memoized selector to get users with the top likes
export const getTopUsersByLikedPosts = (likes) => createSelector(
   getMostLiked(likes),
    item => item && _(item).map((post)=> post.user).uniqBy(post => post.id).value()       
)

```

首先，我们从 redux 状态`allPosts`创建了一个非优化选择器`getAllPosts`。然后我们在`getAllPosts`上创建了一个优化的选择器`getMostLiked`，然后根据他们的喜好过滤这些项目。这样就记住了`getMostLiked`,只有当帖子数据改变时，才会重新计算值。

此外，我们从`getMostLiked`创建了一个记忆选择器`getTopUsersByLikedPosts`，并从帖子中选择用户。这确保了`getTopUsersByLikedPosts`仅在帖子数据的最喜欢者改变时才改变。

这只是一个例子。我们可以在这里执行任何昂贵的计算，并用`Reselect`来记忆它。

`Reselect`只应在必要时使用；过度使用它甚至会加剧 React 本机应用程序的性能问题。

## 在 React Native 中监控内存使用情况

一些进程和功能可能会超出您的预期，耗尽不寻常的内存量。除非您想要这些进程，否则关闭或优化它们可能是提高 React 本机性能的关键。

你可以监控 Android studio 和 [Xcode](https://developer.apple.com/documentation/xcode/improving_your_app_s_performance) 上的内存使用情况，以找到可能影响你的应用程序性能的漏洞。

这可以通过 Android Studio 上的内存分析器来完成。您可以从**视图>工具窗口>评测器**中打开内存评测器，或者从工具栏中找到**评测器**图标。

您还可以从应用程序的开发者菜单中监控应用程序的性能。要打开开发者菜单，使用快捷键 **Command + M** (Mac)或 **Control + M** (Windows 和 Linux)。在开发者菜单中，切换`Show Perf Monitor`来监控应用程序中每个组件的性能。

Xcode 还允许开发人员在他们的应用程序中发现内存搅动或泄漏。你可以从**产品>简介**中找到这一点。或者，您可以使用快捷方式**命令+ i** 来打开泄漏分析器。在模拟器中重启应用程序，开始识别内存泄漏。

如上所述，内存泄漏可能由多种因素引起。有时，缓存的图像会堵塞内存并导致问题。在某些情况下，您可能只需要将`ListView`改为`FlatList`就可以解决问题。在任何情况下，分析器都会向您显示问题的实际原因。

## React Native 中的导航

React Native 中处理导航最流行的库是`[react-navigation](https://github.com/react-navigation/react-navigation.github.io)`包。它是一个普通的 JavaScript 库，也是 React Native 在其文档中推广的。关于如何安装软件包的提示可以在[这里](https://reactnative.dev/docs/navigation)和[这里](https://reactnavigation.org/docs/getting-started)找到。

让我们来看看`react-navigation`的一个简单实现:

```
import { NavigationContainer } from "@react-navigation/native";
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import Home from "./screens/Home";
import Profile from "./screens/Profile";
const Stack = createNativeStackNavigator();
export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen name="Home" component={Home} />
        <Stack.Screen name="Profile" component={Profile} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

```

在`App.js`中，我们将组件包装在`NavigationContainer`中。其次，我们创建了一个堆栈导航器，这是移动应用中最常见的导航方式。默认路径在第 9 行中定义，在我们的例子中，它是主屏幕:

```
import { View, Text, StyleSheet, Button } from "react-native";
import React from "react";
export default function Home({ navigation }) {
  return (
    <View style={styles.container}>
      <Text>Home Screen</Text>
      <Button
        title="Go to Profiule Page"
        onPress={() =>
          navigation.navigate("Profile", {
            name: "John Doe",
            age: 25,
            email: "[email protected]",
            phone: "123-456-7890",
          })
        }
        style={styles.button}
      />
    </View>
  );
}

```

在`Home.js`文件中，我们添加了一个按钮，这将允许我们导航到 profile 屏幕。请注意，在第 10 行，我们可以轻松地在屏幕之间传递数据:

```
import { View, Text, StyleSheet, Button } from "react-native";
import React from "react";
export default function Profile({ route, navigation }) {
  const { name, age, email, phone } = route.params;
  return (
    <View style={styles.container}>
      <Text>Profile Screen</Text>
      <Button
        title="Go Back"
        onPress={() => navigation.goBack()}
        style={styles.button}
      />
      <View>
        <Text>Name: {name}</Text>
        <Text>Age: {age}</Text>
        <Text>Email: {email}</Text>
        <Text>Phone: {phone}</Text>
      </View>
    </View>
  );
}

```

在`Profile.js`中，我们呈现传递给它的信息。在第 10 行，我们看到了在屏幕间导航的另一种可能性。在`Home.js`中，我们明确地将个人资料屏幕设置为目的地，但这里我们只说`navigation.goBack()`。

虽然开始使用这个库非常简单，但是注意不同的导航库来提高应用程序的性能是明智的。尤其是在 Android 设备上，当你的应用变得更复杂时，性能会下降。此外，这个导航选项的开销相当高。`@react-navigation/native`、`@react-navigation/native-stack`、`react-native-safe-area-context`、`react-native-screens`将在您项目的`package.json`中结束。

对于一个更高性能和轻量级的选择，您可以尝试一下 [`react-native-navigation`](https://wix.github.io/react-native-navigation/docs/before-you-start/) 库，它与本地组件相关联。尽管与第一个导航库相比，安装和文档通常不那么直观，但它仍然是值得的。查看[本页](https://wix.github.io/react-native-navigation/docs/installing)了解安装部分。下面是如何在两个屏幕间导航的简单演示代码:

```
import React from 'react';
import { View, Text, Button, StyleSheet } from 'react-native';
import { Navigation } from 'react-native-navigation';

const HomeScreen = (props) => {
  return (
    <View style={styles.root}>
      <Text>Home</Text>
      <Button
        title='Go to profile'
        onPress={() => Navigation.push(props.componentId, {
          component: {
            name: 'Profile',
            options: {
              topBar: {
                title: {
                  text: 'Profile'
                }
              }
            }
          }
        })}/>
    </View>
  );
};

const ProfileScreen = () => {
  return (
    <View style={styles.root}>
      <Text>Profile Screen</Text>
    </View>
  );
}

Navigation.registerComponent('Home', () => HomeScreen);
Navigation.registerComponent('Profile', () => ProfileScreen);

Navigation.events().registerAppLaunchedListener(async () => {
  Navigation.setRoot({
    root: {
      stack: {
        children: [
          {
            component: {
              name: 'Home'
            }
          }
        ]
      }
    }
  });
});

const styles = StyleSheet.create({
  root: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  }
});

```

在 React Native 中，还有其他几个库用于处理导航，但这两个是最受欢迎的选项。

## 结论

性能是每个 React 本机应用程序的关键因素，但也是一个复杂的话题。

有几个因素会影响性能，从控制台语句和动画到大尺寸图像和繁重的计算。确定这些内存泄漏和低性能的来源并修复它们是非常重要的。

有趣的是，Android Studio 和 Xcode 都为我们提供了一种监控内存使用的方法。如前所述，我们还有大量的工具和方法来优化 React 本机应用程序的性能。在您的下一个 React 原生项目中尝试这些优化技术。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)