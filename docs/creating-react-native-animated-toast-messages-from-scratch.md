# 从头开始创建 React 本地动画吐司消息

> 原文：<https://blog.logrocket.com/creating-react-native-animated-toast-messages-from-scratch/>

如果你以前从未听说过 toast 消息，请查看下面的视频，它显示了我们将在本教程中一起构建的项目的简短演示。

简而言之，祝酒词是一种通知，它会在显示器的顶部显示一小段时间，之后，它会再次消失。

[https://www.youtube.com/embed/v4xsE8yiL38](https://www.youtube.com/embed/v4xsE8yiL38)

视频

在本教程中，我们将使用 React Native 动画库在 React Native 中从头开始创建这样一个 toast 消息！React Native 是一个基于 JavaScript 的框架，它使得构建跨平台(iOS 和 Android)的移动应用程序变得非常容易。请随意查看 [React 原生文档](https://reactnative.dev)以获取更多信息。

## 先决条件

我使用了 [Expo 框架](https://docs.expo.dev)来开发这个小项目。如果您也想使用 Expo，但还没有安装，您可以添加 Expo 客户端，如下所示:

`$ npm install --global expo-cli`

如果您不想使用 Expo，下面的代码通常也适用于您。这里唯一与世博会相关的是**世博会/矢量图标，**只有在使用世博会时才可用。但这没问题，一个很好的选择是[反应原生矢量图标](https://github.com/oblador/react-native-vector-icons)！

除此之外，JavaScript 和 React Native 的基础知识将帮助您轻松地理解本教程。如果你想更深入地了解，你可以在我的 GitHub 上找到整个项目。

## 入门指南

事不宜迟，让我们深入实际代码！首先，转到您想要存储项目的目录。在这个目录中，运行`expo init my-project`来初始化 Expo 项目。您可以将“我的项目”替换为您喜欢的任何名称。

然后，使用`cd my-project`转到新创建的目录，并运行`expo start`来启动开发服务器。Expo 让你决定想用什么样的设备工作；我在上面的演示和视频中使用的设备是一台 iPhone 12 Pro Max。

以下是终端命令的简要概述:

```
# cd into the directory where to store your project
 $ cd dir

# initialize the expo project
$ expo init my-project

# navigate inside the newly created project
$ cd my-project

# run the development server
$ expo start

```

### 关于项目目录的说明

我通常会在最初生成的项目目录中添加一些自定义目录。在这个特殊的例子中，我们只需要`screens`文件夹，在那里我们将存储我们的`Home.js`文件。这是我们需要添加到初始结构中的所有内容，因为我们只有一个屏幕。祝酒词将被编码在屏幕上，所以我们甚至不需要一个`component`目录。当然，您也可以将代码从`Home.js`外包到它自己的组件文件中。

## React 本地动画库

正如我在这篇文章的介绍中提到的，我们将使用一个名为 [React Native Animated](https://reactnative.dev/docs/animated#configuring-animations) 的库，它将帮助我们轻松地执行平滑的动画。

整个项目背后的总体思路遵循以下步骤:

1.  最初，在视窗内看不到 toast 消息
2.  一旦其中一个按钮被点击，一个功能将被触发，这将确保祝酒词消息将被顺利地移动到屏幕的顶部
3.  最后，在我们定义的时间窗口之后，toast 消息将再次消失

这整个过程可以用 React Native 动画来实现。下面，您可以找到我们将编写的实现此行为的代码的第一部分:

```
// Home.js
import React, { useRef, useState } from "react";
import {
  StyleSheet,
  Text,
  View,
  Animated,
  Button,
  TouchableOpacity,
  Dimensions,
} from "react-native";
import { AntDesign, Entypo } from "@expo/vector-icons";

const Home = () => {
  const windowHeight = Dimensions.get("window").height;
  const popAnim = useRef(new Animated.Value(windowHeight * -1)).current;

  const popIn = () => {
    Animated.timing(popAnim, {
      toValue: windowHeight * 0.35 * -1,
      duration: 300,
      useNativeDriver: true,
    }).start(popOut());
  };

  const popOut = () => {
    setTimeout(() => {
      Animated.timing(popAnim, {
        toValue: windowHeight * -1,
        duration: 300,
        useNativeDriver: true,
      }).start();
    }, 2000);
  };

  const instantPopOut = () => {
    Animated.timing(popAnim, {
      toValue: windowHeight * -1,
      duration: 150,
      useNativeDriver: true,
    }).start();
  };

  );
};
export default Home;

```

在**第 15 行**中，我们使用 React Native 的[维度库](https://reactnative.dev/docs/dimensions)获得设备的实际显示高度。这将确保我们的 toast 消息的位置将根据实际显示大小动态呈现。

在**第 16 行**中，我们用以下内容定义我们的祝酒词 *的初始位置`popAnim`:*

```
const popAnim = useRef(new Animated.Value(windowHeight * -1)).current;

```

我们使用`useRef`钩子是因为 React Native Animated 告诉我们不要直接修改`Animated.value`，而是使用`useRef`钩子来创建一个可变对象。

你还会注意到我们正在用当前显示高度的负值初始化`Animated.value`。这样做的原因是，最初，我们不想看到祝酒辞。使用`(windowHeight * -1)`，toast 消息将暂时显示在实际视窗上方。

在下面的函数中，我们将定义在某些情况下`popAnim`应该改变的值和速度。在**第 18 行**中，我们声明了`popIn`函数。顾名思义，这个函数处理 toast 消息的显示方式。

```
const popIn = () => {
    Animated.timing(popAnim, {
      toValue: windowHeight * 0.35 * -1,
      duration: 300,
      useNativeDriver: true,
    }).start(popOut());
  };

```

上面，在**的第 2 行**中，我们调用了`timing()`方法，它最终只是沿着一条定时缓动曲线来动画显示我们的`popAnim`值。使用`toValue`，你可以告诉动画在哪个值停止动画，使用`duration`，你可以决定动画的持续时间。

在我们的例子中，我们希望 toast 消息在 300 毫秒内显示在屏幕顶部。我们还想将`useNativeDriver`设置为`true`，以便使用本地代码来执行动画。在我们的小例子中，这个选项对我们的动画没有任何影响。

最后，在**的第 6 行**，我们在`timing`方法上调用`start`方法，以便真正开始动画。在`start`方法中，一旦动画完成，就可以调用一个回调函数。在这种情况下，我们要调用`popOut`函数！

```
const popOut = () => {
    setTimeout(() => {
      Animated.timing(popAnim, {
        toValue: windowHeight * -1,
        duration: 300,
        useNativeDriver: true,
      }).start();
    }, 2000);
  };

```

这看起来与`popIn`函数非常相似，除了三点:

*   我们再次将`toValue`属性更改为`windowHeight * -1`，这将再次将 toast 消息移出我们的视口
*   我们用一个`setTimeOut`函数将整个事情包装起来，这样 toast 消息不会立即消失，而是在两秒钟内可见
*   最后，我们没有为这个`start`方法提供回调函数

在 toast 消息中，我们将有一个关闭按钮，它也需要一个动画功能。因为我是从零开始构建这个，所以我把它叫做`instantPopOut`。

```
const instantPopOut = () => {
    Animated.timing(popAnim, {
      toValue: windowHeight * -1,
      duration: 150,
      useNativeDriver: true,
    }).start();
  };

```

它几乎与`popOut`函数中的代码相同，只是我们不需要`setTimeOut`函数，因为一旦点击关闭按钮，我们希望 toast 消息立即消失。

而这一段其实是这个项目最难的部分。剩下的就相对简单了——现在，我们只需要为两个按钮和 toast 消息编写实际的 UI。

## 编写 UI 代码

首先，我们将对按钮进行编码，这将触发 toast 消息的出现。该部分将包含两个按钮:

*   一个用于显示成功消息
*   一个用于显示失败消息

请看下面的截图。

```
<Button
    title="Success Message"
    onPress={() => {
      setStatus("success");
      popIn();
    }}
    style={{ marginTop: 30 }}
></Button>

<Button
    title="Fail Message"
    onPress={() => {
      setStatus("fail");
      popIn();
    }}
      style={{ marginTop: 30 }}
></Button>

```

如果您看一下上面的代码片段，您会注意到按钮看起来非常相似。只有第 4 行**和第 13 行**中的标题和状态不同。当您点击标题为“成功消息”的第一个按钮时，状态将被设置为**成功**；否则，将设置为**失败**。

![The toast message display when you press the Success button](img/8ba42ca873d2df0d4960b2e42b358439.png)

![The toast message display when you press the Fail button](img/34261c5c5770cce07c58c7d6c623e050.png)

我们将在编写 toast 消息时回到这个状态。在我们开始之前，我们需要在两种情况下调用`popIn()`函数。

## 撰写祝酒词

现在，让我们转到 UI 中有趣的部分:实际的祝酒词！

```
<Animated.View
    style={[
      styles.toastContainer,
      {
        transform: [{ translateY: popAnim }],
      },
    ]}
  >
    <View style={styles.toastRow}>
      <AntDesign
        name={status === "success" ? "checkcircleo" : "closecircleo"}
        size={24}
        color={status === "success" ? successColor : failColor}
      />

      <View style={styles.toastText}>
        <Text style={{ fontWeight: "bold", fontSize: 15 }}>
          {status === "success" ? successHeader : failHeader}
        </Text>
        <Text style={{ fontSize: 12 }}>
          {status === "success" ? successMessage : failMessage}
        </Text>
      </View>

      <TouchableOpacity onPress={instantPopOut}>
        <Entypo name="cross" size={24} color="black" />
      </TouchableOpacity>

    </View>
</Animated.View>

```

在上面的代码片段中，您可以看到整个 toast 消息被包装在一个`Animated.View`中。试着把它想象成一个普通的视图元素，除了你可以在它上面运行动画。

在**的第 2-7 行**，你可以看到我给这个`Animated.View`分配了一个样式数组。第一个是`styles.toastContainer`，是一个普通的 React 原生样式表引用。

然而，第二个问题是最有趣的部分。[转换](https://reactnative.dev/docs/transforms)接受大量的转换对象，如`rotate`、`scale`或`translate`(如本例所示)。

更具体地说，我们想要沿着 y 轴变换这个`Animated.View`，并使用`popAnim`作为这个变换中的值。记住这是`Animated.Value`，它会根据`popIn`和`popOut`函数中的规则变化。

在第 9-29 行中，我们定义了实际的 toast 消息。这个 toast 消息的第一个组件是一个 AntDesign 图标，它将根据我们的 state `success`的值有条件地呈现。

如果状态设置为`success`，下面的行将实现这一点，并且将呈现一个复选图标:

```
name={status === "success" ? "checkcircleo" : "closecircleo"}
```

否则，会呈现一个十字图标。同样的逻辑也适用于图标的颜色(**第 13 行**)、toast 消息的标题文本(**第 18 行**)以及该容器内的实际消息(**第 21 行**)。我们在这几行中引用的常量可以在下面的代码片段中找到。

```
const successColor = "#6dcf81";
const successHeader = "Success!";
const successMessage = "You pressed the success button";

const failColor = "#bf6060";
const failHeader = "Failed!";
const failMessage = "You pressed the fail button";

```

这里唯一的部分，总是以同样的方式呈现，是关闭图标。通过点击该图标，代码`onPress={instantPopOut}`将确保`instantPop`功能被触发。

就是这样！所有代码片段可以总结如下:

```
// Home.js
import React, { useRef, useState } from "react";
import {
  StyleSheet,
  Text,
  View,
  Animated,
  Button,
  TouchableOpacity,
  Dimensions,
} from "react-native";
import { AntDesign, Entypo } from "@expo/vector-icons";

const Home = () => {
  const windowHeight = Dimensions.get("window").height;
  const [status, setStatus] = useState(null);
  const popAnim = useRef(new Animated.Value(windowHeight * -1)).current;
  const successColor = "#6dcf81";
  const successHeader = "Success!";
  const successMessage = "You pressed the success button";
  const failColor = "#bf6060";
  const failHeader = "Failed!";
  const failMessage = "You pressed the fail button";

  const popIn = () => {
    Animated.timing(popAnim, {
      toValue: windowHeight * 0.35 * -1,
      duration: 300,
      useNativeDriver: true,
    }).start(popOut());
  };

  const popOut = () => {
    setTimeout(() => {
      Animated.timing(popAnim, {
        toValue: windowHeight * -1,
        duration: 300,
        useNativeDriver: true,
      }).start();
    }, 10000);
  };

  const instantPopOut = () => {
    Animated.timing(popAnim, {
      toValue: windowHeight * -1,
      duration: 150,
      useNativeDriver: true,
    }).start();
  };

  return (
    <View>
      <Animated.View
        style={[
          styles.toastContainer,
          {
            transform: [{ translateY: popAnim }],
          },
        ]}
      >
        <View style={styles.toastRow}>
          <AntDesign
            name={status === "success" ? "checkcircleo" : "closecircleo"}
            size={24}
            color={status === "success" ? successColor : failColor}
          />
          <View style={styles.toastText}>
            <Text style={{ fontWeight: "bold", fontSize: 15 }}>
              {status === "success" ? successHeader : failHeader}
            </Text>
            <Text style={{ fontSize: 12 }}>
              {status === "success" ? successMessage : failMessage}
            </Text>
          </View>
          <TouchableOpacity onPress={instantPopOut}>
            <Entypo name="cross" size={24} color="black" />
          </TouchableOpacity>
        </View>
      </Animated.View>

      <Button
        title="Success Message"
        onPress={() => {
          setStatus("success");
          popIn();
        }}
        style={{ marginTop: 30 }}
      ></Button>

      <Button
        title="Fail Message"
        onPress={() => {
          setStatus("fail");
          popIn();
        }}
        style={{ marginTop: 30 }}
      ></Button>

    </View>
  );
};
export default Home;
const styles = StyleSheet.create({
  toastContainer: {
    height: 60,
    width: 350,
    backgroundColor: "#f5f5f5",
    justifyContent: "center",
    alignItems: "center",
    borderRadius: 10,
    shadowColor: "#000",
    shadowOffset: {
      width: 0,
      height: 2,
    },
    shadowOpacity: 0.25,
    shadowRadius: 3.84,
    elevation: 5,
  },
  toastRow: {
    width: "90%",
    flexDirection: "row",
    alignItems: "center",
    justifyContent: "space-evenly",
  },
  toastText: {
    width: "70%",
    padding: 2,
  },
});

```

最后，您需要将`Home.js`文件导入到`App.js`文件，如下面的代码片段所示:

```
// App.js
import React from 'react';
import { StyleSheet, Text, View, SafeAreaView } from 'react-native';
import Home from './screens/Home';

export default function App() {
  return (
    <SafeAreaView style={styles.container}>
      <Home />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});

```

## 结论

在这篇博文中，我们从头开始创建了一条祝酒辞。我们没有使用任何外部库——我们唯一需要的是 [React 本地动画库](https://blog.logrocket.com/best-react-native-animation-libraries/)。此外，根据我们希望看到成功还是失败的消息，有条件地呈现 toast 消息。

整个项目的[源代码可以在我的 GitHub 上找到。我希望这个教程对你有帮助！](https://github.com/kevintomas1995/logRocket_toasMessage)

## [日志火箭](https://lp.logrocket.com/blg/react-native-signup):立即在您的 React Native 应用程序中重新创建问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)**