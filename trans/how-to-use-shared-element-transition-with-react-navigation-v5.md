# 如何在 React Navigation v5 中使用共享元素转换

> 原文：<https://blog.logrocket.com/how-to-use-shared-element-transition-with-react-navigation-v5/>

移动应用的过渡提供了设计的连续性。这种连续性是通过在应用程序中导航时将一个视图中的通用元素连接到下一个视图来提供的。

## 什么是共享元素转换？

不同视图或活动之间的转换包括进入和退出转换，它们相互独立地激活整个视图层次结构。有时，两种不同的连续观点有共同的要素。提供了一种将这些公共元素从一个视图转换到另一个视图的方法，强调了转换之间的连续性。这些过渡的本质是让最终用户关注内容，并提供无缝体验。共享元素过渡决定了两个不同的视图如何共享一个或多个元素来保持焦点和体验。

## 先决条件

开始之前，请确保在本地环境中安装了以下软件:

*   [Node.js](https://nodejs.org/en/) 版本> = 12.x.x 已安装
*   访问一个包装管理器，如 npm 或 yarn 或 npx
*   安装 expo-cli ，或使用 npx

请注意，为了演示，我将使用 iOS 模拟器。如果你更喜欢使用 Android 设备或模拟器，这篇文章中分享的代码片段也可以运行。

## 安装共享元素转换库

首先，让我们使用`expo-cli`创建一个新的 React 本地项目。在终端窗口中，执行下面的命令，然后在新创建的项目目录中导航。导航后，安装创建共享元素转换所需的库。让我们使用堆栈导航模式从一个屏幕到另一个屏幕使用`react-navigation`。

要安装 React 导航库，请查看官方文档中的[以下说明](https://reactnavigation.org/docs/getting-started)。这些依赖关系随时间而变化:

```
npx expo init shared-element-transitions
cd shared-element-transitions
yarn add @react-navigation/native react-native-animatable
expo install react-native-gesture-handler react-native-reanimated react-native-screens react-native-safe-area-context @react-native-community/masked-view
yarn add react-native-shared-element [email protected]
```

安装完这些库之后，让我们来看看如何运行 Expo 应用程序。从终端运行`yarn start`命令来触发 Expo 应用程序的构建。然后根据模拟器或设备，请从终端提示中选择正确的选项。例如，要在 iOS 模拟器上以初始状态运行此应用程序，请按下`i`。

下面是 iOS 模拟器上的输出显示方式:

![output in iOS simulator](img/74c50f24d9409d11ea85d6cbb476aa9e.png)

该输出验证 Expo 应用程序已经启动并正在运行。

## 创建主屏幕

在这个示例应用程序中，过渡将在主屏幕和详细信息屏幕之间进行。主屏幕将是一个图像和一些数据的滚动列表。我将使用一组模拟数据数组。你可以自由地使用任何你想尝试的数据。不用担心数据集，您可以使用模拟数据。创建一个名为`config/`的新目录，并在其中创建一个名为`data.js`的新文件，包含以下数组和对象:

```
export const data = [
  {
    id: '1',
    title: 'Manarola, Italy',
    description: 'The Cliffs of Cinque Terre',
    image_url:
      'https://images.unsplash.com/photo-1516483638261-f4dbaf036963?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=633&q=80',
    iconName: 'location-pin'
  },
  {
    id: '2',
    title: 'Venezia, Italy',
    description: 'Rialto Bridge, Venezia, Italy',
    image_url:
      'https://images.unsplash.com/photo-1523906834658-6e24ef2386f9?ixlib=rb-1.2.1&ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&auto=format&fit=crop&w=630&q=80',
    iconName: 'location-pin'
  },
  {
    id: '3',
    title: 'Prague, Czechia',
    description: 'Tram in Prague',
    image_url:
      'https://images.unsplash.com/photo-1513805959324-96eb66ca8713?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=634&q=80',
    iconName: 'location-pin'
  }
];
```

之后，创建一个名为`screens/`的新目录，两个应用程序屏幕将存放在那里。在其中创建一个名为`HomeScreen.js`的文件，并导入以下语句:

```
import React from 'react';
import {
  ScrollView,
  Text,
  View,
  TouchableOpacity,
  Image,
  Dimensions
} from 'react-native';
import { StatusBar } from 'expo-status-bar';
import { SimpleLineIcons } from '@expo/vector-icons';
import { data } from '../config/data';
```

使用 React Native 的`Dimensions` API，让我们定义图像组件的初始宽度和高度。在下面的代码片段中，我使用屏幕的`width`来计算宽度和高度:

```
const { width } = Dimensions.get('screen');
const ITEM_WIDTH = width * 0.9;
const ITEM_HEIGHT = ITEM_WIDTH * 0.9;
```

`HomeScree`组件将是一个功能性的 React 组件，它接受一个名为`navigation`的属性。`navigation`道具将允许从主屏幕导航到`DetailScreen`。在任何 React 本机应用程序中，React 导航库提供了一个上下文，进一步自动提供对作为道具的`navigation`对象的访问。该属性包含各种分派导航动作的函数:

```
export default function HomeScreen({ navigation }) {
  return (
    <View style={{ flex: 1, backgroundColor: '#0f0f0f' }}>
      <StatusBar hidden />
      {/* Header */}
      <View style={{ marginTop: 50, marginBottom: 20, paddingHorizontal: 20 }}>
        <Text style={{ color: '#888', textTransform: 'uppercase' }}>
          Saturday 9 January
        </Text>
        <Text style={{ color: '#fff', fontSize: 32, fontWeight: '600' }}>
          Today
        </Text>
      </View>
  )
}
```

该功能组件将呈现标题，说明要显示的一些虚拟信息，并在其下方显示一个用于滚动图像列表的`ScrollView`。每个图像显示一个图标和一些关于图像内容的信息。当主页和详细信息屏幕之间发生转换时，该图像和其上的文本将发挥巨大的作用。在`ScrollView`组件内部，让我们使用 JavaScript 的`map()`方法来呈现模拟数据。如果您从某处托管的 REST API 注入数据，并且您不确定特定数据集中的项目数量，请使用 React Native 的`FlatList`组件，而不是`ScrollView`:

```
return (
  {/* Scrollable content */}
<View style={{ flex: 1, paddingBottom: 20 }}>
  <ScrollView
    indicatorStyle='white'
    contentContainerStyle={{ alignItems: 'center' }}
  >
    {data.map(item => (
      <View key={item.id}>
        <TouchableOpacity
          activeOpacity={0.8}
          style={{ marginBottom: 14 }}
          onPress={() => navigation.navigate('DetailScreen', { item })}
        >
          <Image
            style={{
              borderRadius: 14,
              width: ITEM_WIDTH,
              height: ITEM_HEIGHT
            }}
            source={{ uri: item.image_url }}
            resizeMode='cover'
          />
          <View
            style={{
              position: 'absolute',
              bottom: 20,
              left: 10
            }}
          >
            <View style={{ flexDirection: 'row' }}>
              <SimpleLineIcons size={40} color='white' name={item.iconName} />
              <View style={{ flexDirection: 'column', paddingLeft: 6 }}>
                <Text
                  style={{
                    color: 'white',
                    fontSize: 24,
                    fontWeight: 'bold',
                    lineHeight: 28
                  }}
                >
                  {item.title}
                </Text>
                <Text
                  style={{
                    color: 'white',
                    fontSize: 16,
                    fontWeight: 'bold',
                    lineHeight: 18
                  }}
                >
                  {item.description}
                </Text>
              </View>
            </View>
          </View>
        </TouchableOpacity>
      </View>
    ))}
  </ScrollView>
</View>);
```

## 创建详细信息屏幕

`DetailScreen`组件将呈现主屏幕上滚动列表中每张图片的细节。在该屏幕上，显示的图像带有位于屏幕顶部的后退导航按钮。它以一个`item`对象的形式接收数据，该对象使用来自 React 导航库的`route.params`进行析构。在图像下方，将显示将与主屏幕共享的标题和一些虚拟文本。

在`screens/`目录中创建一个名为`DetailScreen.js`的新文件，并添加以下代码片段:

```
import React, { useRef } from 'react';
import {
  StyleSheet,
  Text,
  View,
  ScrollView,
  Image,
  Dimensions
} from 'react-native';
import { SimpleLineIcons, MaterialCommunityIcons } from '@expo/vector-icons';
const { height } = Dimensions.get('window');
const ITEM_HEIGHT = height * 0.5;
const DetailScreen = ({ navigation, route }) => {
  const { item } = route.params;
  return (
    <View style={{ flex: 1, backgroundColor: '#0f0f0f' }}>
      <Image
        source={{ uri: item.image_url }}
        style={{
          width: '100%',
          height: ITEM_HEIGHT,
          borderBottomLeftRadius: 20,
          borderBottomRightRadius: 20
        }}
        resizeMode='cover'
      />
      <MaterialCommunityIcons
        name='close'
        size={28}
        color='#fff'
        style={{
          position: 'absolute',
          top: 40,
          right: 20,
          zIndex: 2
        }}
        onPress={() => {
          navigation.goBack();
        }}
      />
      <View
        style={{ flexDirection: 'row', marginTop: 10, paddingHorizontal: 20 }}
      >
        <SimpleLineIcons size={40} color='white' name={item.iconName} />
        <View style={{ flexDirection: 'column', paddingLeft: 6 }}>
          <Text
            style={{
              color: 'white',
              fontSize: 24,
              fontWeight: 'bold',
              lineHeight: 28
            }}
          >
            {item.title}
          </Text>
          <Text
            style={{
              color: 'white',
              fontSize: 16,
              fontWeight: 'bold',
              lineHeight: 18
            }}
          >
            {item.description}
          </Text>
        </View>
      </View>
      <ScrollView
        indicatorStyle='white'
        style={{
          paddingHorizontal: 20,
          backgroundColor: '#0f0f0f'
        }}
        contentContainerStyle={{ paddingVertical: 20 }}
      >
        <Text
          style={{
            fontSize: 18,
            color: '#fff',
            lineHeight: 24,
            marginBottom: 4
          }}
        >
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do
          eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad
          minim veniam, quis nostrud exercitation ullamco laboris nisi ut
          aliquip ex ea commodo consequat. Duis aute irure dolor in
          reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla
          pariatur. Excepteur sint occaecat cupidatat non proident, sunt in
          culpa qui officia deserunt mollit anim id est laborum.
        </Text>
        <Text
          style={{
            fontSize: 18,
            color: '#fff',
            lineHeight: 24,
            marginBottom: 4
          }}
        >
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do
          eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad
          minim veniam, quis nostrud exercitation ullamco laboris nisi ut
          aliquip ex ea commodo consequat. Duis aute irure dolor in
          reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla
          pariatur. Excepteur sint occaecat cupidatat non proident, sunt in
          culpa qui officia deserunt mollit anim id est laborum.
        </Text>
      </ScrollView>
    </View>
  );
};
export default DetailScreen;
```

## 向应用程序添加导航

为了从主屏幕导航到细节屏幕，应用程序需要有一个导航流。这将由来自`react-navigation-shared-element`模块的`createSharedElementStackNavigator`方法提供。它包含了`react-native-shared-element`的 React 导航库。这个方法允许我们创建一个 stack-navigator，这是在两个单独的屏幕之间共享元素的初始过程。它用共享元素包装每条路由，并检测路由变化以触发转换。使用这种方法定义导航流的过程类似于 React Navigation 的堆栈导航模块。

创建一个名为`navigation/`的新目录，并在其中创建一个名为`RootNavigator.js`的新文件。导入以下语句并创建一个名为`createSharedElementStackNavigator`方法的`Stack`的实例。然后定义根导航器:

```
import * as React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createSharedElementStackNavigator } from 'react-navigation-shared-element';
import HomeScreen from '../screens/HomeScreen';
import DetailScreen from '../screens/DetailScreen';
const Stack = createSharedElementStackNavigator();
export default function RootNavigator() {
  return (
    <NavigationContainer>
      <Stack.Navigator headerMode='none' initialRouteName='HomeScreen'>
        <Stack.Screen name='HomeScreen' component={HomeScreen} />
        <Stack.Screen name='DetailScreen' component={DetailScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

要查看它的运行情况，请修改`App.js`文件，如下所示:

```
import React from 'react';
import RootNavigator from './navigation/RootNavigator';
export default function App() {
  return <RootNavigator />;
}
```

以下是在 iOS 模拟器中完成此步骤后的结果:

![adding navigation to react](img/6c07453c3b69c79ff3f41967f560d5a0.png)

## sharedelemont 映射

图像组件将负责支持主屏幕和细节屏幕之间的无缝来回转换。这个过渡应该从滚动网格到细节屏幕，然后回到相关图像。为了实现这一点，用`<SharedElement>`包装`Image`组件，并在`HomeScreen`中为其提供唯一的`id`。

另外，确保从`react-navigation-shared-element`模块导入`<SharedElement>`组件:

```
import { SharedElement } from 'react-navigation-shared-element';
// Wrap the image component as
return (
  // ...
  <SharedElement id={`item.${item.id}.image_url`}>
    <Image
      style={{
        borderRadius: 14,
        width: ITEM_WIDTH,
        height: ITEM_HEIGHT
      }}
      source={{ uri: item.image_url }}
      resizeMode='cover'
    />
  </SharedElement>
);
```

`<SharedElement>`组件接受一个名为`id`的属性，它是两个屏幕之间的共享 ID。它所围绕的子组件是发生转换的实际组件。

为了启用共享元素转换，在`DetailScreen`中必须遵循上述过程:

```
import { SharedElement } from 'react-navigation-shared-element';
// Wrap the image component as
return (
  // ...
  <SharedElement id={`item.${item.id}.image_url`}>
    <Image
      source={{ uri: item.image_url }}
      style={{
        width: '100%',
        height: ITEM_HEIGHT,
        borderBottomLeftRadius: 20,
        borderBottomRightRadius: 20
      }}
      resizeMode='cover'
    />
  </SharedElement>
);
```

为了制作主页和细节屏幕之间的动画，在`DetailScreen`组件中定义一个`sharedElements`配置。这将映射两个屏幕之间的`Image`组件的转换。

在`DetailScreen.js`中的`export`语句前添加代码片段:

```
DetailScreen.sharedElements = route => {
  const { item } = route.params;
  return [
    {
      id: `item.${item.id}.image_url`,
      animation: 'move',
      resize: 'clip'
    }
  ];
};
```

上面的 config 对象根据这两个屏幕之间共享的惟一 ID 触发屏幕之间共享元素的过渡效果。这是通过定义一个名为`id`的属性来实现的。

属性`animation`决定了在两个屏幕之间导航时动画将如何发生。例如，在上面的代码片段中，`animation`有一个名为`move`的值。它也是该属性的默认值。还有其他值可用，如`fade`、`fade-in`和`fade-out`。属性`resize`是决定元素的形状和大小是否应该被修改的行为。例如，在上面的代码片段中，值`clip`添加了一个类似于文本显示效果的过渡效果。

这是此步骤后的输出:

![navigating between screens gifts](img/4de967644078de365456b3ebd15885f5.png)

在上面的例子中，请注意，当过渡发生时，屏幕在中间从左向右滑动。要修改此行为以应用共享元素的过渡效果，让我们向`DetailScreen`添加一个`options`配置对象。在根导航器文件中，添加以下配置:

```
const options = {
  headerBackTitleVisible: false,
  cardStyleInterpolator: ({ current: { progress } }) => {
    return {
      cardStyle: {
        opacity: progress
      }
    };
  }
};
// Then add it to the DetailScreen
return (
  <Stack.Screen
    name='DetailScreen'
    component={DetailScreen}
    options={() => options}
  />
);
```

`cardStyleInterpolator`函数为卡片的不同部分指定插值样式。它允许我们在两个屏幕之间导航时自定义过渡。它接收一个名为`current.progress`的属性值，表示当前屏幕的动画节点进度值。将该值应用到属性`opacity`会将动画节点更改为共享元素配置对象中定义的动画值。它的`cardStyle`属性将样式应用到表示卡片的视图上。

## 更新共享元素映射

在前面的演示中，您可以看到图像组件上的过渡是无缝的，但其他共享组件(如两个屏幕之间的定位销图标、标题和项目描述)则不是。

为了解决这个问题，让我们使用`<SharedElement>`组件来映射它们。首先，在主屏幕中，修改以下组件:

```
return (
  // Icon
  <SharedElement id={`item.${item.id}.iconName`}>
    <SimpleLineIcons size={40} color='white' name={item.iconName} />
  </SharedElement>
  //Title
  <SharedElement id={`item.${item.id}.title`}>
  <Text
    style={{
      color: 'white',
      fontSize: 24,
      fontWeight: 'bold',
      lineHeight: 28
    }}
  >
    {item.title}
  </Text>
</SharedElement>
  // Description
  <SharedElement id={`item.${item.id}.description`}>
  <Text
    style={{
      color: 'white',
      fontSize: 16,
      fontWeight: 'bold',
      lineHeight: 18
    }}
  >
    {item.description}
  </Text>
</SharedElement>
);
```

类似地，修改`DetailScreen.js`文件中的以下元素:

```
// Icon
<SharedElement id={`item.${item.id}.iconName`}>
  <SimpleLineIcons size={40} color='white' name={item.iconName} />
</SharedElement>
// Title
<SharedElement id={`item.${item.id}.title`}>
  <Text
    style={{
      color: 'white',
      fontSize: 24,
      fontWeight: 'bold',
      lineHeight: 28
    }}
  >
    {item.title}
  </Text>
</SharedElement>
// Description
<SharedElement id={`item.${item.id}.description`}>
  <Text
    style={{
      color: 'white',
      fontSize: 16,
      fontWeight: 'bold',
      lineHeight: 18
    }}
  >
    {item.description}
  </Text>
</SharedElement>
```

然后添加配置:

```
DetailScreen.sharedElements = route => {
  const { item } = route.params;
  return [
    {
      id: `item.${item.id}.image_url`,
      animation: 'move',
      resize: 'clip'
    },
    {
      id: `item.${item.id}.title`,
      animation: 'fade',
      resize: 'clip'
    },
    {
      id: `item.${item.id}.description`,
      animation: 'fade',
      resize: 'clip'
    },
    {
      id: `item.${item.id}.iconName`,
      animation: 'move',
      resize: 'clip'
    }
  ];
};
```

这是此步骤后的输出:

![shared elements mapping ](img/4e0ab4a3b6b31e8662dd361064fb1c68.png)

## 延迟装载

[共享元素转换](https://github.com/IjzerenHein/react-native-shared-element)是支持平滑的最终用户体验的好方法，但是当处理需要在转换发生之前或之后加载的元素时，它会变得棘手。例如，在前面的演示中，后退按钮在过渡发生之前呈现。为了控制它的行为，让我们使用 [React Native Animatable](https://github.com/oblador/react-native-animatable) 库来制作它的动画。

将其导入到`DetailScreen.js`文件中:

```
import * as Animatable from 'react-native-animatable';
```

关闭按钮图标将被包裹在`<Animatable.View>`中。这个组件有一个名为`delay`的道具，可以延迟动画。使用一个叫做`duration`的道具，你可以控制动画运行的时间。这两个属性的值是以毫秒为单位提供的。使用一个`ref`值，将`fadeOut`动画应用到图标上。这种动画方法是异步的，因此，在动画成功运行后，您可以使用 promise 导航回主屏幕。传递给此动画方法的参数以毫秒为单位:

```
const DetailScreen = ({ navigation, route }) => {
  const buttonRef = React.useRef();
  return (
    <Animatable.View
      ref={buttonRef}
      animation='fadeIn'
      duration={600}
      delay={300}
      style={[StyleSheet.absoluteFillObject]}
    >
      <MaterialCommunityIcons
        name='close'
        size={28}
        color='#fff'
        style={{
          position: 'absolute',
          top: 40,
          right: 20,
          zIndex: 2
        }}
        onPress={() => {
          buttonRef.current.fadeOut(100).then(() => {
            navigation.goBack();
          });
        }}
      />
    </Animatable.View>
  );
};
```

下面是最终输出:
![react navigation](img/cd633c4d8289cfde987c58b0cf5943f8.png)

## 结论

我希望你在阅读本教程时感到愉快。使用 React Navigation 共享元素模块在 React Native 的屏幕之间共享元素使开发过程和最终用户体验都变得流畅。我建议你查看这里的官方文档了解更多信息。源代码可以在这个 [GitHub repo](https://github.com/amandeepmittal/react-native-examples/tree/master/shared-element-transitions) 获得。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)