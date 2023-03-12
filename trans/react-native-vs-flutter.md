# flutter vs . React Native-log rocket 博客

> 原文：<https://blog.logrocket.com/react-native-vs-flutter/>

***编者按**:本文最后一次更新于 2022 年 6 月 14 日，以反映在 Flutter 3 和 React Native v0.68 中所做的更改，以及比较 Flutter 和 React Native 的设备兼容性和文档可用性。*

目前最流行的两个跨平台移动应用开发框架是 Flutter 和 React Native。这些巨头分别由世界上最大的两家科技公司支持:谷歌和脸书。

在本指南中，我们将比较 Flutter 和 React Native，探究是什么让每个框架如此特别，以及为什么它们如此受欢迎。

以下是我们将要介绍的内容:

## 什么是颤振？

[Flutter 是 Google](https://flutter.dev/) 开发的跨平台 UI 框架。Flutter 于 2017 年 5 月首次发布，多年来人气稳步增长。

Flutter 的一个主要卖点是它[允许你使用单一代码库创建跨平台应用](https://blog.logrocket.com/comparing-developer-experience-for-cross-platform-frameworks/)。传统上，一个公司需要多种工具和开发人员来创建一个可以在 web、移动和桌面上使用的应用程序。

例如，您可能需要:

*   专门从事 web 开发的开发人员使用 React 构建网站
*   另一个开发人员使用 C#和 Java 来创建桌面版本
*   使用 Kotlin 和 Swift 构建 Android 和 iOS 应用的专业移动开发者

这种方法需要一个完整的开发团队，更不用说召开大量的会议来确保设计和品牌在所有平台上都是一致的。您还必须考虑对每个平台进行测试，并解决它们各自的错误和怪癖。

有了 Flutter，公司可以雇佣一名开发人员来创建跨平台的应用程序，只需管理一个代码库。这大大减少了启动和维护应用程序所需的时间和资源。

[扑动 3 引入了相当实质性的变化](https://docs.flutter.dev/whats-new)。虽然用于编写移动和网络应用程序的 API 没有多大变化，但现在您可以使用相同的代码库，使用旋动编译器创建 iPhone、Android、web、Windows、Mac 和 Linux 应用程序。

此外， [Flutter 3 为 Firebase](https://blog.logrocket.com/how-to-build-chat-application-flutter-firebase/) 提供了更好的支持，同时还有一个[休闲游戏工具包](https://docs.flutter.dev/resources/games-toolkit)用于创建带有 Flutter 的游戏。

## 什么是 React Native？

[React Native 是由脸书](https://reactnative.dev/)创建的跨平台框架。有了用 JavaScript 编写的代码库，React Native 使得创建跨平台应用程序变得相当简单，降低了 JavaScript 开发人员的入门门槛。

Web 开发已经存在很多年了，大多数 web 开发人员在其职业生涯的大部分时间里都在使用 JavaScript。移动开发仍然是相当新的，但生态系统在过去几年中已经成熟了不少。

如果你已经了解 JavaScript，那么使用 React Native 进行移动应用开发的学习曲线就不会那么陡峭。

如今，许多公司都在使用 React Native 进行应用开发。一个突出的例子是，微软最近使用 React Native 开发了新的 [Xbox 商店应用。像 Flutter 一样，React Native 使得使用一个代码库创建跨各种平台的应用成为可能。](https://www.xbox.com/en-US/microsoft-store)

在撰写本文时最近发布的 React Native v0.68 ，使用 Fabric Renderer 和 TurboModule 系统为[新 React Native 架构](https://reactnative.dev/architecture/overview)提供了选择加入支持。所有的图书馆还没有迁移到这个新的架构，这就是为什么它只是选择加入。

Fabric Renderer 带来了并发性、React 悬念和 React Native 的服务器端渲染，以及更快、更具互操作性的工具集。

## Flutter 和 React Native 之间有哪些关键区别？

Flutter 和 React Native 有很多相似之处，但它们在一些关键方面也有很大不同。

首先，Flutter 在其代码库中使用 Dart 编程语言，而 React Native 使用 JSX。

这两种语言都基于 C 风格的语法类型，并遵循面向对象的原则。有了这种共性，Flutter 和 React Native 在设计上基本相似，代码也非常相似。

### 证明文件

Flutter 和 React Native 都有优秀的文档指南。这些指南使程序员开始开发应用程序变得容易。

Flutter 的文档很容易阅读，因为它的格式、结构和细节都很好。相比之下， [React Native 的文档](https://reactnative.dev/docs/getting-started)就不简单了。

React Native 也严重依赖于社区开发的依赖库，所以它的文档组织得不是很好。

### 动态与静态编程

当涉及到核心编程语言时，Flutter 和 React Native 之间有着显著的区别。

JavaScript 本质上是动态的。这意味着您可以更改各种数据类型的值，这使得它非常通用。Dart 既是动态的，也是静态的，这使得它可以兼得两者之长。

静态类型语言通常被认为更安全，因为它强迫你声明和使用正确的数据类型。例如，你不能给一个字符串赋值而不返回一个错误。

如果您选择使用 TypeScript ,一个严格的 JavaScript 语法超集，那么您可以使用 [JavaScript 执行更多的类型安全和错误检查。](https://blog.logrocket.com/how-to-pass-typescript-function-parameter/)

### 项目规模

React 本地应用程序有一个 JavaScript 运行时环境。虽然他们通常有一个更大的构建尺寸，但这个尺寸可以在 [React Native 中通过启用赫尔墨斯](https://blog.logrocket.com/getting-started-with-hermes-in-react-native/)和[progguard](https://www.guardsquare.com/proguard)来减小。

另一方面，Flutter 应用程序往往具有更大的文件大小。不可避免的是，Flutter 应用程序的构建规模越大，占用的空间就越大。

### 布局

Flutter 使用小部件风格来构建用户界面，而 React Native 使用 JavaScript 和 JSX。

[Flutter widgets 是预制的](https://blog.logrocket.com/widgets-the-building-blocks-of-flutter-apps/)，所以你不需要创建你自己的定制 widgets，除非你想。因为这些小部件是由 Google 创建和测试的，所以您不需要担心兼容性问题。

如果你使用像 Swift 这样的编程语言进行移动应用开发，你通常看不到苹果用来创建 UI 组件的代码，比如按钮。相比之下，您可以查看 Flutter 小部件的代码，了解 Google 开发人员是如何创建它们的。

Flutter 和 React 都使用 CSS Flexbox 来构造布局。每个框架实现它的方式是不同的，但只要你了解 Flexbox，你应该不会有任何问题来构建你的应用程序的布局。

开发 Flutter 的团队还开发了谷歌 Chrome 浏览器的开发工具，这有助于快速过渡，因为调试工具非常相似。

## 为什么手机 app 开发这么受欢迎？

移动应用程序开发领域在过去几年中稳步发展。地球上几乎每个人都有手机，所以用户群非常庞大。如今，你几乎可以找到任何东西的应用。

如果你想创建一个移动应用程序，有很多方法可以选择。

你可以选择走原生路线，这将意味着使用 Swift 创建移动应用或使用 Kotlin 创建 Android 应用。这是苹果和谷歌分别使用的官方编程语言，所以你可以期待第一方的支持和频繁的更新。

或者，您可以选择跨平台的方式，使用 Flutter 或 React Native。通常，本地开发人员会使用 Xcode 和 Swift 来构建 iOS 应用程序，并为 Android 应用程序构建 Android Studio 和 Kotlin。这些工具适用于跨平台工作。

开发人员使用集成开发环境(IDE)也很常见，比如 Visual Studio 代码。

在大多数情况下，开发人员在使用 Flutter 和 React Native 构建应用程序时，倾向于使用 IDE、Android Studio 或 Xcode。我的偏好是使用 Visual Studio 代码开发 React 原生应用，使用 Android Studio 开发 Flutter 应用。

## 颤动 vs .反应原生:需求

在撰写本文时， [React Native 比 Flutter](https://www.statista.com/statistics/869224/worldwide-software-developer-working-hours/) 稍微流行一些，部分原因是 React Native 与流行的 web 框架 React 的关联。React Native 存在的时间也更长，所以它的用户群更大。

因此，目前对 React 原生开发者的需求高于对 Flutter 开发者的需求。然而，这并不意味着 Flutter 不受欢迎；事实上， [Flutter 随着时间的推移继续流行。](https://blog.logrocket.com/why-flutter-is-overtaking-react-native/)

Android Studio 有一个带代码助手的 [Flutter 插件，这使得编写和调试代码变得更加容易。在我写这篇文章的时候，](https://plugins.jetbrains.com/plugin/9212-flutter)[这个插件有大约 1200 万次下载](https://plugins.jetbrains.com/search?products=androidstudio&products=appcode&products=clion&products=dataspell&products=dbe&products=go&products=idea&products=idea_ce&products=idea_edu&products=mps&products=phpstorm&products=pycharm&products=pycharm_ce&products=pycharm_edu&products=rider&products=ruby&products=webstorm&search=flutter)，这显示了 Flutter 有多受欢迎。

## 颤振与反应原生:性能

很难决定是 Flutter 还是 React 原生应用的性能更好。不会每个 app 都一样；有许多因素需要考虑，如应用程序的类型、代码库、动画、过渡、应用程序大小、布局、数据传递、最终用户的手机等等。

Flutter 和 React Native 都是开源的，可以免费使用。两者都维护得很好——正如你所料，考虑到它们是由谷歌和脸书创建的。

你可以测试使用这两个框架创建的应用程序，既可以在你的 iOS 和 Android 电脑上使用内置的模拟器进行虚拟测试，也可以在你的手机上进行本地测试。

如果打算在 iOS 上开发，SDK 只在苹果电脑上有，那么 Windows 用户和 Linux 用户就要倒霉了。但是，你可以在任何平台上开发 Android 开发应用。

这两个框架都使用热重装，使得开发更加有效，因为您可以立即看到所做的更改。

关于颤振和反应原生是否是真正的原生，存在一些争论。要被认为是 100%的本土语言，它们需要用它们被设计的语言来编写；即 iOS 的 Swift 和 Android 的 Kotlin 或 Java。

在使用 React Native 和 Flutter 编写的应用程序时，您获得的体验在很大程度上是原生体验。然而，React Native 使用 JavaScript 桥与本机代码通信，桥会降低应用程序的性能。

相比之下，Flutter 使用的 Dart 代码可以直接编译成 C 语言，这是您所能期望的最接近原生语言的代码。Flutter 代码不需要任何桥来与本机代码通信。您可以放心地认为这将有助于提高性能。

为了解决性能问题，React Native 的新架构用 JavaScript 接口(JSI)取代了 JavaScript 桥。JSI 允许开发人员直接调用本机模块，而无需使用桥。

新的 React 本机架构还允许开发人员通过 TurboModules 延迟加载本机模块，这有助于缩短应用程序的启动时间。

在开发人员和社区的支持下，Flutter 和 React Native 都在继续改进它们的性能。但截至目前，Flutter 在性能上略胜一筹。

## Flutter vs. React Native:开发者生态系统

颤振开发者倾向于参考[官方文档](https://flutter.dev/)。但是，React Native 有几个选项；首先，[官方文件](https://reactnative.dev/)，或者不同的平台，最流行的是[博览会](https://expo.io/)。

Expo 提供了更多的功能和定制，包括一个集成的图标库，而官方的 React 原生文档则更加简单。

自从 JavaScript 在 1995 年出现以来，React 原生生态系统更加成熟，拥有更多用户。相比之下，Flutter 于 2017 年发布。

React Native 在社交媒体上有一个非常活跃的社区，但 Flutter 也不逊色。在本文撰写之时， [Flutter 在 GitHub 上拥有的明星](https://github.com/flutter/flutter)比 [React Native 多](https://github.com/facebook/react-native)。

社交媒体上的数字也非常相似。 [Flutter 的 Twitter 关注者](https://twitter.com/FlutterDev)比 [React Native](https://twitter.com/reactnative) 多，虽然数字接近，但 Reddit 上活跃的[Flutter 开发者比](https://www.reddit.com/r/FlutterDev/)[活跃的 React Native 开发者](https://www.reddit.com/r/reactnative/)多。

颤振和自然反应都已在流行的商业应用中使用。 [Flutter 被用来为百度、Groupon 和易贝等公司开发应用程序。与此同时，脸书、Instagram、Shopify 和 Discord 的应用程序都是用 React Native](https://flutter.dev/showcase)开发的。

## 颤振与反应本机:设备兼容性

Flutter 和 React Native 都支持采用 ARM 处理器的 Android 设备和 iPhone 4s 及更高版本的苹果设备。两者都支持 Android 和 iOS 模拟器来开发应用程序。

苹果设备对 Flutter 应用要求 iOS 9 及以上，对 React 原生应用要求 iOS 11 及以上。Android 设备对 Flutter 应用要求 API 等级 19 (Android 4.4)及以上，对 React 原生应用要求 API 等级 23 (Android 6.0)。

基于 Expo 的 React 原生应用支持的最低版本是 Android 5 或 iOS 10 及以上版本。

## 实际例子:使用 Flutter 和 React Native 开发应用程序

为了演示使用 Flutter 和 React Native 构建应用程序，我们将运行一个示例。

我们的示例应用程序名为 Social，有两个屏幕。iOS 和 Android 版本看起来一模一样。下图显示了它在 iPhone 上的外观:

![Flutter React Native App Iphone Homepage Showing Profile Button And Home Text With An Image Of A City At Street Level Displayed Below](img/43d71e8106ef64fafd2c5b7a3ad350fa.png)

![Flutter React Native App Iphone Social Profile With Image Of Smiling Brunette Girl In Red Long Sleeve Shirt. Name Sarah Taylor And Lorem Ipsum Bio Text Shown](img/6aed546c89b62c28f091ed5ac7a7b065.png)

### 颤振代码示例

与传统的编程方法相比，Flutter 基于小部件的架构非常独特。一旦你明白它是如何工作的，它就会成为你的第二天性。
![Flutter Widget Based Architecture Folder](img/627a7acfecab788d09fee9327b0b7977.png)

#### `main.dart`文件

`main.dart`文件是我们的应用程序的主要文件，它将为应用程序的不同视图确定不同的路径。我们用另一个名为`ProfileScreen`的路径在`HomeScreen`小部件中指定应用程序的初始视图，我们可以基于用户动作触发该路径:

```
import 'package:flutter/material.dart';
import 'package:social_app/screens/home_screen.dart';
import 'package:social_app/screens/profile_screen.dart';

void main() {
  runApp(Social());
}

class Social extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      initialRoute: HomeScreen.id,
      routes: {
        HomeScreen.id: (context) => HomeScreen(),
        ProfileScreen.id: (context) => ProfileScreen(),
      },
    );
  }
}

```

#### `home_screen.dart`小工具

`home_screen.dart`小部件表示我们的应用程序的初始视图，它还展示了应用程序栏和视图主体的样式和内容:

```
import 'package:flutter/material.dart';
import 'package:social_app/screens/profile_screen.dart';

class HomeScreen extends StatelessWidget {
  static String id = 'home_screen';

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Color(0xffb0c1957),
      appBar: AppBar(
        toolbarHeight: 40,
        title: Text(
          'Social',
          style: TextStyle(
              color: Colors.black, fontSize: 16, fontWeight: FontWeight.bold),
        ),
        backgroundColor: Colors.white,
      ),
      body: Column(
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        children: [
          Container(
            width: 400,
            margin: EdgeInsets.only(top: 20),
            child: RaisedButton(
              color: Color(0xff23397B),
              padding: EdgeInsets.all(20),
              onPressed: () {
                Navigator.push(context,
                    MaterialPageRoute(builder: (context) => ProfileScreen()));
              },
              child: Text(
                'PROFILE',
                style:
                    TextStyle(color: Colors.white, fontWeight: FontWeight.bold),
              ),
            ),
          ),
          SizedBox(
            height: 1,
          ),
          Container(
            child: Center(
              child: Text(
                'HOME',
                style: TextStyle(
                    fontSize: 40,
                    color: Colors.white,
                    fontWeight: FontWeight.bold),
              ),
            ),
          ),
          Container(
            child: Image(
              image: AssetImage('images/home-bg.jpg'),
            ),
          ),
        ],
      ),
    );
  }
}

```

#### `profile_screen.dart`文件

`profile_screen.dart`文件包含我们的第二个视图`ProfileScreen`，它在小部件主体的内容中显示使用该应用程序的用户的用户档案信息:

```
import 'package:flutter/material.dart';
import 'package:social_app/screens/home_screen.dart';

class ProfileScreen extends StatelessWidget {
  static String id = 'profile_screen';

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Color(0xffb0c1957),
      appBar: AppBar(
        toolbarHeight: 40,
        title: Text(
          'Social',
          style: TextStyle(
              color: Colors.black, fontSize: 16, fontWeight: FontWeight.bold),
        ),
        backgroundColor: Colors.white,
      ),
      body: Column(
        mainAxisAlignment: MainAxisAlignment.start,
        children: [
          Container(
            width: 400,
            margin: EdgeInsets.only(top: 20),
            child: RaisedButton(
              color: Color(0xff23397B),
              padding: EdgeInsets.all(20),
              onPressed: () {
                Navigator.push(context,
                    MaterialPageRoute(builder: (context) => HomeScreen()));
              },
              child: Text(
                'HOME',
                style:
                    TextStyle(color: Colors.white, fontWeight: FontWeight.bold),
              ),
            ),
          ),
          SizedBox(
            height: 50,
          ),
          Container(
            child: Center(
              child: Text(
                'PROFILE',
                style: TextStyle(
                    fontSize: 40,
                    color: Colors.white,
                    fontWeight: FontWeight.bold),
              ),
            ),
          ),
          SizedBox(
            height: 20,
          ),
          Container(
              child: CircleAvatar(
            radius: 130.0,
            backgroundImage: AssetImage('images/profile-image.jpg'),
          )),
          SizedBox(
            height: 20,
          ),
          Container(
            child: Text(
              'SARAH TAYLOR',
              style: TextStyle(
                  color: Colors.white,
                  fontSize: 20,
                  fontWeight: FontWeight.bold),
            ),
          ),
          SizedBox(
            height: 20,
          ),
          Container(
            color: Color(0xff000d4a),
            padding: EdgeInsets.all(18.0),
            child: Container(
              child: Text(
                "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla ut ultricies velit. Proin at nisi nisl. Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos. Etiam eu tincidunt dui. Quisque non ornare ex, facilisis congue enim. In neque nulla, posuere at gravida id, dapibus et libero.",
                style: TextStyle(
                  color: Colors.white,
                  fontSize: 17,
                ),
              ),
            ),
          )
        ],
      ),
    );
  }
}

```

### React 本机代码示例

如果您已经了解了 React for web，那么本节中的代码应该非常熟悉。设置 React 本地项目的架构与 React 中的几乎相同:

![React Native Example Project App Folder Structure](img/f165347f0b8545cbdcfa26cf3e8ccb46.png)

#### `App.js`组件

像任何 React 应用程序一样，`App.js`组件是我们的入口点，定义了我们的路线和视图、`HomeScreen`和`ProfileScreen`。就像在我们的 Flutter 应用程序中一样，`HomeScreen`是用户将看到的初始路线:

```
// Import statements for the components used in the app and the navigation
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
const Stack = createStackNavigator();
import HomeScreen from './src/screens/HomeScreen';
import ProfileScreen from './src/screens/ProfileScreen';
// The main component with all of the screen routing business logic
const App = () => {
    return (
        <NavigationContainer>
            <Stack.Navigator initialRouteName="HomeScreen" screenOptions={{ gestureEnabled: false }}>
                <Stack.Screen name="HomeScreen" component={HomeScreen} options={{ title: 'Social' }} />
                <Stack.Screen name="ProfileScreen" component={ProfileScreen} options={{ title: 'Social' }} />
            </Stack.Navigator>
        </NavigationContainer>
    );
};
export default App;

```

#### `Home.js`组件

`Home.js`组件描述了我们的`HomeScreen`视图。从本质上来说，`View`组件是 UI 的构建块，就像 HTML 中的 div 一样。在 React Native 中，使用`StyleSheet.create`函数在 CSS 中提供样式:

```
// Import statements for the components used in the app
import React from 'react';
import { View, Text, StyleSheet, TouchableOpacity, Image } from 'react-native';
import HomeBG from '../../assets/home-bg.jpg';
// The main component for the HomeScreen
const HomeScreen = ({ navigation }) => {
    return (
        <View style={styles.appStyle}>;
            <TouchableOpacity
                style={styles.btnContainer}
                title="Profile"
                onPress={() => navigation.navigate('ProfileScreen')}
            >
                <Text style={styles.btnText}>;Profile</Text>
            </TouchableOpacity>
            <Text style={styles.heading}>Home</Text>
            <View>
                <Image style={styles.homeBG} source={HomeBG} />
            </View>
        </View>
    );
};
// The component specific styles for the HomeScreen
const styles = StyleSheet.create({
    appStyle: {
        backgroundColor: 'rgb(12,25,87)',
        height: '100%',
    },
    btnContainer: {
        backgroundColor: '#23397B',
        padding: 20,
        justifyContent: 'center',
        alignItems: 'center',
        marginHorizontal: 20,
        marginVertical: 20,
    },
    btnText: {
        color: '#ffffff',
        fontWeight: 'bold',
        textTransform: 'uppercase',
    },
    heading: {
        fontSize: 40,
        color: '#ffffff',
        textAlign: 'center',
        margin: 20,
        fontWeight: 'bold',
        textTransform: 'uppercase',
    },
    homeBG: {
        height: '100%',
        width: '100%',
    },
});
export default HomeScreen;

```

#### `ProfileScreen.js`组件

`ProfileScreen.js`组件定义了`ProfileScreen`，显示用户信息:

```
// Import statements for the components used in the app
import React from 'react';
import { View, Text, StyleSheet, TouchableOpacity, Image } from 'react-native';
import ProfileImage from '../../assets/profile-image.jpg';
// The main component for the ProfileScreen
const ProfileScreen = ({ navigation }) => {
    return (
        <View style={styles.appStyle}>
            {/* TouchableOpacity is basically a more customizable button component */}
            <TouchableOpacity style={styles.btnContainer} title="Profile" onPress={() =&gt; navigation.navigate('HomeScreen')}&gt;
                <Text style={styles.btnText}>Home</Text>
            </TouchableOpacity>
            <Text style={styles.heading}>Profile</Text>
            <View style={styles.profileContainer}>
                <Image style={styles.profileImage} source={ProfileImage} />
            </View>
            <View>
                <Text style={styles.profileName}>Sarah Taylor</Text>
            </View>
            <View style={styles.profileBioContainer}>
                <Text style={styles.profileBio}>
                    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla ut ultricies velit. Proin at nisi nisl. Class
                    aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos. Etiam eu tincidunt dui.
                    Quisque non ornare ex, facilisis congue enim. In neque nulla, posuere at gravida id, dapibus et libero.
                </Text>
            </View>
        </View>
    );
};
// The component specific styles for the ProfileScreen
const styles = StyleSheet.create({
    appStyle: {
        backgroundColor: 'rgb(12,25,87)',
        height: '100%',
    },
    btnContainer: {
        backgroundColor: '#23397B',
        padding: 20,
        justifyContent: 'center',
        alignItems: 'center',
        marginHorizontal: 20,
        marginVertical: 20,
    },
    btnText: {
        color: '#ffffff',
        fontWeight: 'bold',
        textTransform: 'uppercase',
    },
    heading: {
        fontSize: 40,
        color: '#ffffff',
        textAlign: 'center',
        margin: 20,
        fontWeight: 'bold',
        textTransform: 'uppercase',
    },
    profileContainer: {
        alignItems: 'center',
    },
    profileImage: {
        borderRadius: 100,
        height: 250,
        width: 250,
    },
    profileName: {
        color: '#ffffff',
        fontSize: 20,
        textAlign: 'center',
        fontWeight: 'bold',
        margin: 20,
        textTransform: 'uppercase',
    },
    profileBioContainer: {
        backgroundColor: 'rgba(0,13,74,0.4234068627450981)',
    },
    profileBio: {
        marginHorizontal: 20,
        marginVertical: 20,
        color: '#ffffff',
        fontSize: 18,
    },
});
export default ProfileScreen;

```

## 应该使用 Flutter 还是 React Native？

这里没有明确的赢家；Flutter 和 React 各有利弊，正确的选择将取决于您的经验以及项目的目标和要求。

如果你已经了解 JavaScript，用 React Native 编写移动应用程序是显而易见的。然而，如果您正在寻找更好的性能、稳定性和生态系统之间更有凝聚力的环境，您应该考虑尝试一下 Flutter。

要了解 React Native 如何与另一个流行的跨平台移动框架 Xamarin 相抗衡，请查看 [Xamarin 与 React Native](https://blog.logrocket.com/xamarin-vs-react-native/) 。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)