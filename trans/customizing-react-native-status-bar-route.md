# 基于路线定制您的 React 本机状态栏

> 原文：<https://blog.logrocket.com/customizing-react-native-status-bar-route/>

移动设备上的状态栏是指用户可以观察当前时间、手机运营商信息、wifi 状态、电池电量、各种应用程序通知的图标等的顶部区域。

该区域可能看起来类似于下面的 iPhone 13 Pro 示例:

![Screenshot Of Example Status Bar Area Shown On Iphone 13 Pro With Time Displayed At Top Left, Notch In Center, And Data, Wifi, And Battery Symbols Shown In Order At Top Right](img/1aa0bf4bfd1717e5baa8fdab3b659652.png)

在开发 React 本机应用程序时，您可能希望更改该区域的外观，以与屏幕上的当前视图完美匹配。

你还需要考虑到一些设备有一个凹口——就像上面的例子——而一些设备没有。对于那些有缺口的应用程序，它们可以将内容扩展到屏幕顶部。在这种情况下，应用程序内容会与状态栏重叠。

在本文中，我们将详细介绍这些方面，并了解如何在 React Native 中掌握状态栏。我们将涵盖:

## 使用 React Native 中的`StatusBar`来控制状态栏

首先，我们如何控制 React 本地应用程序中的状态栏？再简单不过了:React 原生框架为我们提供了`StatusBar`组件来实现这个目的。

`StatusBar`组件使开发人员能够根据用户在应用程序中的位置或当前需求来处理设备状态栏的外观。

例如，如果用户正在阅读一些东西，而[需要保持专注](https://blog.logrocket.com/how-to-autofocus-using-react-hooks/)，我们可能希望完全隐藏状态栏，并限制他们在特定屏幕上的注意力分散。

在 React 本地应用程序中，还有第二个控制状态栏的选项。您可以使用命令式 API 并以编程方式更改状态栏的外观，而不是使用 React Native `StatusBar`组件。

在接下来的几节中，我们将看看每一种方法，了解何时使用每一种方法，以及如何正确地使用它们。

## 当设备有缺口时管理 React 本机状态栏

当设备有缺口时，我们该怎么办？

我们知道，我们的应用程序的内容可以与带有凹槽的设备上的状态栏重叠。在这种情况下，我们必须确保考虑到状态栏的高度，并避免在那里放置任何类型的文本或按钮。否则，我们可能会像这样结束:

![Demonstration Of Content Overlapping With Status Bar On Android Device With Blue Button Reading "Show/Hide Custom Status Bar" Obscured By Status Bar Info And Text Below Reading "Oh No, Our Button Is Overlapping With The Status Bar"](img/f445d00a26889c9ad37026ea53f9f1aa.png)

为了防止状态栏的这个问题， [React Native 提供了一个名为](https://blog.logrocket.com/improve-mobile-ui-react-native-safe-area-context/) `[SafeAreaView](https://blog.logrocket.com/improve-mobile-ui-react-native-safe-area-context/)`的组件。

组件`SafeAreaView`应该被用作你的屏幕的根组件，而不是`View`。它会自动在设备屏幕的安全范围内呈现内容。但是，它只能在至少运行 iOS 11 的 iOS 设备上运行。

因此，下面的代码片段在 iOS 和 Android 上产生的结果会有所不同:

```
  <SafeAreaView style={{ flex: 1 }}>
     <View
       style={{
         height: 48,
         backgroundColor: 'purple',
         alignContent: 'center',
         justifyContent: 'center',
         paddingHorizontal: 16,
       }}>
       <Text style={{ color: 'white', fontWeight: 'bold' }}>NOW TRENDING</Text>
     </View>
     {renderContent()}
  </SafeAreaView>        

```

下图显示了每个设备如何显示上述代码的结果。左边是 Android 的结果，而右边的截图是在 iOS 设备上。您可以看到,`SafeAreaView`组件考虑了 iOS 设备上的凹槽:

![Demonstration Comparison How Using The Safeareaview Component Would Appear Differently On Android (Left, Status Bar Overlapping With App Contents) Vs Iphone (Right, Correct Spacing Between Status Bar And App Contents)](img/ea97bd4a23840a03556b43ef45beb56d.png)

因此，我们知道 iOS 设备会自动使用`SafeAreaView`组件对顶部应用必要的填充。然而，对于 Android 设备，我们需要手动处理这个问题。

为了防止我们的内容与 Android 设备上的凹槽重叠，我们应该修改我们的代码片段，并向`SafeAreaView`添加一个`paddingTop`。我们将这样做:

```
import { Platform, NativeModules } from 'react-native';
const { StatusBarManager } = NativeModules;

<SafeAreaView style={{ 
  flex: 1, 
  paddingTop: Platform.OS === 'android' ? StatusBarManager.HEIGHT : 0,
 }}>
 ...

```

不要忘记在顶部添加导入，就像添加代码片段一样。

如上所示修改我们的代码将产生以下结果:

![Results Of Adding Top Padding To Android Device So App Content Does Not Overlap With Status Bar. Status Bar Information Displayed In Black Text Over White Background Above Purple Bar Leading Into App Content](img/344b3b605b2b958cc587df61584e1cfe.png)

如您所见，我们在屏幕的根容器中添加了一个顶部填充，其高度等于状态栏的高度。

一般来说，我们也想拉伸状态栏下面的顶部颜色。要做到这一点，我们需要将填充移动到组件的顶部——在我们的例子中，是紫色的标题头。请看下面:

```
 <SafeAreaView style={{ flex: 1 }}>
     <View
       style={{
         backgroundColor: 'purple',
         alignContent: 'center',
         justifyContent: 'center',
         paddingHorizontal: 16, 
      // we are adding the following lines and we get rid of the height prop
      paddingTop: Platform.OS === 'android' ? StatusBarManager.HEIGHT : 0,
      paddingBottom: 16
       }}>
       <Text style={{ color: 'white', fontWeight: 'bold' }}>NOW TRENDING</Text>
     </View>
     {renderContent()}
  </SafeAreaView>    

```

上述代码片段将在 Android 设备上导致以下结果:

![Android Device With Top Padding Added And Color Extending From App Content To Status Bar For Better Visual Flow](img/5dfce68cea381f03a7458e84a24beeaf.png)

我们很好地处理了 Android 设备状态栏的高度，没有对 iOS 造成不必要的影响。

然而，我们的应用程序看起来还是有点问题。由于紫色的背景，状态栏上的文字和图标有点难以辨认。在这种情况下，我们可能希望将它们的颜色更改为白色或基于较暗背景色的另一种较亮的颜色。

## 如何动态个性化 React Native 中的状态栏

假设你正在开发一个包含各种品牌内容的应用程序，它将利用各种品牌颜色。

在这种情况下，检查应用程序当前所在的路线是有意义的。然后，您可以根据路线个性化状态栏的外观，以增强应用程序的 UI 和 UX。

你为什么想这么做？让我们看看下面的例子。

为了简单起见，我们将只有 2 个屏幕:`Home`和`Products`。我们正在实现一个检查当前路由的方法。

在我们的例子中，我们只检查两个值。然而，在现实世界中，您可能有多个包含路径的数组。每个数组都有不同的风格。然后，这些不同的样式将基于路线被应用为当前屏幕的背景。

让我们看一些代码。

首先，我们需要添加所需的导入来实现我们的导航，并为我们的示例获取路线。它们是从[库](https://blog.logrocket.com/navigating-react-native-apps-using-react-navigation/) `[react-navigation](https://blog.logrocket.com/navigating-react-native-apps-using-react-navigation/)` [库](https://blog.logrocket.com/navigating-react-native-apps-using-react-navigation/)中添加的:

```
import {
  NavigationContainer,
  useNavigationContainerRef,
} from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import { View, StatusBar, Button, Text } from 'react-native';
import { useRoute } from '@react-navigation/native';
import { NativeModules } from 'react-native';
const { StatusBarManager } = NativeModules;

```

注意，对于这个例子，我们使用的是 React Navigation v6。

我们将像这样使用`Stack`导航器:

```
const Stack = createStackNavigator();

```

现在，事情变得有点有趣了。我们正在为每个屏幕实现一个根容器。该组件将包装我们的每个屏幕，从而避免代码重复:

```
const ScreenWrapper = ({ children }) => {
  // we obtain the object that contains info about the current route
  const route = useRoute();

  // for simplicity we will only modify the background color
const getBackgroundColorBasedOnRoute = () => {
    return route.name === 'Products' ? 'darkolivegreen' : 'orange';
  };

  // we are applying the background color to the component itself
return (
    <View
      style={{
        paddingTop: StatusBarManager.HEIGHT,
        backgroundColor: getBackgroundColorBasedOnRoute(),
        flex: 1,
      }}>
      <StatusBar
        barStyle={route.name === 'Products' ? 'light-content' : 'dark-content'}
      />
      {children}
    </View>
  );

};

```

使用上面的`ScreenWrapper`组件，我们现在将实现两个基本屏幕:

```
const ProductsScreen = () => {
  return (
    <ScreenWrapper>
      <Text style={{ color: 'white', marginTop: 50, alignSelf: 'center' }}>
        Products screen
      </Text>
    </ScreenWrapper>
  );
};

const HomeScreen = ({ navigation }) => {
  return (
    <ScreenWrapper>
      <View style={{ marginTop: 50, width: 200, alignSelf: 'center' }}>
        <Button
          title="Go to products"
          onPress={() => navigation.push('Products')}
        />
      </View>
      <ScreenWrapper>
        {/*
      Component content goes here...
       */}
      </ScreenWrapper>
    </ScreenWrapper>
  );
};

```

在上面的代码中，您可能已经注意到了屏幕可以接收的`navigation`属性。该属性由 React 导航库自动传递给那些在`Screen`组件中使用的组件。当我们实现堆栈导航器时，您将在下面的操作中看到这一点。

此外，导航器的默认标题并没有为我们的示例增加价值。为了隐藏它，我们提供了`headerShown`选项并将其设置为`false`:

```
export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator
        screenOptions={{
          headerShown: false,
        }}>
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen name="Products" component={ProductsScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

```

在上面的最后一段代码中，我们将所有的东西连接在一起。我们使用一个堆栈导航器，允许我们在`HomeScreen`和`ProductsScreen`之间切换。这就是我们默认通过`navigation`道具的地方。

这段代码导致我们的 React 本地应用程序在 Android 屏幕上出现以下主屏幕:

![Home Screen Of React Native App On Android Device With Status Bar Information Displayed In Black Over Orange Background And Above Blue Button Reading "Go To Products"](img/998f8107b1600d4b3adc4fee0dbe8f10.png)

按下按钮将我们带到产品屏幕，看起来像这样:

![Products Screen Shown After Clicking Button Displayed In Previous Image With Status Bar Information In White Over Dark Green Background](img/2e407c12bd602ee9678662e591608375.png)

正如你所注意到的，我们正在根据路线定制状态栏的外观。更具体地说，当背景颜色较浅时，我们将状态栏文本设置为黑色，当处理较暗的背景时，我们将它设置为白色。

假设您想要进一步定制 React 本机应用程序的状态栏。您还可以向`StatusBar`提供`backgroundColor`属性来设置自定义背景颜色，这将独立于组件的背景。

## 对 React Native 使用命令式 API 而不是`StatusBar`

如果您不想使用`StatusBar`组件，我们还有另一个选择。命令式 API 允许我们将各种样式推送到状态栏的堆栈中，从而修改它的外观。

状态栏上有各种我们可以更改的道具，都可以在官方文档中[找到。现在我们来看一些代码，这样我们就知道这个 API 到底是如何被使用的。](https://reactnative.dev/docs/statusbar)

让我们回到前面的例子。我们可以很容易地添加一个`useEffect`到`ScreenWrapper`组件中，在那里我们可以根据当前的路径将一组不同的道具放入状态栏的堆栈中:

```
useEffect(() => {
    switch (route.name) {
      case 'Products':
        StatusBar.pushStackEntry({ barStyle: 'light-content', hidden: false });
        break;
      case 'Home':
        StatusBar.pushStackEntry({ barStyle: 'dark-content', hidden: false });
        break;
      default:
        StatusBar.pushStackEntry({ barStyle: 'light-content', hidden: true });
    }
  }, [route]);

```

请注意，我们还向我们的`StackNavigator`添加了第三个屏幕，名为`NotificationsScreen`。将这一行添加到堆栈导航器中:

```
<Stack.Screen name="Notifications" component={NotificationsScreen} />

```

您还需要实现`NotificationsScreen`组件:

```
const NotificationsScreen = () => {
  return (
    <ScreenWrapper>
      <Text style={{ color: 'white', marginTop: 50, alignSelf: 'center' }}>
        Notifications screen
      </Text>
    </ScreenWrapper>
  );
};

```

现在让我们来看看`useEffect`中的开关。我们到底在那里做什么？

根据当前的路线，我们正在给状态栏堆栈的外观添加不同的样式。当我们到达通知屏幕时，我们将完全隐藏该栏。同时，当在其他两个屏幕之间切换时，我们会根据暗和亮的内容来改变主题。

您也可以使用`popStackEntry`弹出最近添加的条目。这将删除最后添加到状态栏堆栈中的样式。

另外值得注意的是，除了使用`pushStackEntry`和传递一个包含状态栏样式配置的对象之外，还可以使用一组会产生相同效果的方法。

例如，你可以使用`setBackgroundColor`和`setHidden`，而不是使用包含`backgroundColor`和`hidden`的对象调用`pushStackEntry()`。

其他值得一提的道具:

*   `translucent`:布尔型；仅限 Android
    *   如果您需要半透明的状态栏，请使用
    *   该应用程序将被绘制在下面
*   `barStyle` : `StatusBarStyle`枚举
    *   可能的值:`default`、`light-content`、`dark-content`
    *   用于根据应用程序内容或当前路线来更改状态栏文本的颜色，就像我们在上面所做的那样
*   `animated` : boolean
    *   已确定过渡是否有动画效果
    *   与`backgroundColor`、`hidden`和`barStyle`一起使用

您可以在关于状态栏的 [React 原生文档中找到完整的列表和附加信息。](https://reactnative.dev/docs/statusbar)

决定是使用`StatusBar`组件还是命令式 API 取决于您，这取决于您的需求、用例以及个人偏好。

例如，在某些情况下，使用命令式 API 会更简洁。如果您想避免在多个地方复制`StatusBar`组件，那么您可以安全地使用命令式 API 方法并从同一个地方控制状态栏。

否则，如果你的状态栏在整个应用程序中有一个非常一致的外观，你可以简单地在顶层使用`StatusBar`组件，它将完美地工作。

您应该与您的设计者讨论，以确定您将在多大程度上动态自定义状态栏。这些信息可以帮助您选择正确的方法。

然而，请记住一个警告:您不应该混合这些方法。混合它们将导致用您在`StatusBar`组件中指定的内容覆盖您通过命令式 API 设置的任何内容。

假设您为自己的屏幕设置了一个`StatusBar`组件。然而，在同一个屏幕上，在一个事件(如按下按钮)之后，您必须更改状态栏。重新渲染后，状态栏将重置为初始的`StatusBar`组件样式，覆盖您的命令式 API 设置。

## 结论

在本文中，我们深入研究了 React Native 的`StatusBar`组件以及如何使用它。我们还研究了如何配置一个 stack navigator 来利用当前的 route 来定制状态栏的外观。

此外，我们查看了一个使用命令式 API 作为使用`StatusBar`组件的替代方法的例子。

谢谢你坚持到最后。我希望阅读这篇文章对你有用。不要犹豫，在评论中开始对话——反馈总是受欢迎的！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)