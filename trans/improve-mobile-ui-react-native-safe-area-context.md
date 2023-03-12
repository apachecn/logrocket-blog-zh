# 使用 React 本地安全区域上下文日志博客改进移动用户界面

> 原文：<https://blog.logrocket.com/improve-mobile-ui-react-native-safe-area-context/>

在构建移动应用程序时，开发人员必须反复确保内容能够在不同类型的屏幕上正确呈现。耗时且容易出错，我们可以使用像 [React Native 的`safe-area-context` API](https://github.com/th3rdwave/react-native-safe-area-context) 这样的工具来简化这个过程。

在本教程中，我们将使用`safe-area-context`来定位移动应用程序中不规则图形周围的网页内容，例如，状态栏、主页指示器和凹口。要继续学习本教程，您应该熟悉 React Native 和 React Hooks。我们开始吧！

> 注意:要跟进，您可以查看本文中使用的待办事项列表应用程序的完整代码库

## 移动用户界面限制

让我们假设您已经构建了以下 React 本地应用程序，该应用程序从待办事项列表中添加和删除项目:

 [https://www.youtube.com/embed/lgDiP8m30Co?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/lgDiP8m30Co?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

我们的待办事项应用很实用，也很容易使用。在 web 视图中，我们的应用程序看起来不会不正常。然而，您会很快注意到移动 UI 的几个问题。例如，如果用户试图以横向模式查看应用程序，他们会遇到以下错误:

 [https://www.youtube.com/embed/LdBdBcc7--k?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/LdBdBcc7--k?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

当用户转动手机并试图使用横向模式时，由于手机屏幕尺寸的原因，我们的应用程序的标题会被部分遮挡。

当 web 应用程序适应移动屏幕尺寸时，某些元素被移动界面元素阻塞或扭曲是很常见的。让我们使用`safe-area-context` API 来改进 header 元素的可视化呈现。

## 开始使用`safe-area-context`

首先，我们将在项目中安装`safe-area-context`。导航到您的文件夹的根项目，并运行以下命令:

```
npm install react-native-safe-area-context

```

如果你正在使用 React Native ≥v0.6.0，`safe-area-context`将自动链接到你的项目。但是，如果您使用的是 React Native 的早期版本，请在项目的根文件夹中运行以下命令来链接库:

```
react-native link react-native-safe-area-context

```

`safe-area-context`使用两个组件与 React 本地组件交互，提供者和消费者，它们之间有父子关系。消费者允许我们使用最近的提供商提供的值。

在`safe-area-context`库中，只有一个组件`SafeAreaProvider`充当提供者。让我们用`SafeAreaProvider`组件包装我们的应用程序的根组件，它将向后代消费者提供它重叠的任何系统元素的值或样式。

将以下代码添加到`App.js`:

```
import React from "react";
import { Text, View, StyleSheet, ScrollView, TouchableOpacity, KeyboardAvoidingView } from "react-native";
import { SafeAreaView, SafeAreaProvider } from "react-native-safe-area-context";
import Task from "./components/Tasks";

export default function App() {
  const [task, setTask] = useState();
  const [taskItems, setTaskItems] = useState([]);
  const handleAddTask = () => {
    Keyboard.dismiss();
    setTaskItems([...taskItems, task]);
    setTask(null);
  };
  const completeTask = (index) => {
    let itemsCopy = [...taskItems];
    itemsCopy.splice(index, 1);
    setTaskItems(itemsCopy);
  };
  return (
    <SafeAreaProvider>
      <ScrollView>
        <View style={styles.container}>
          <View style={styles.taskWrapper}>
            <Text style={styles.sectionTitle}>Today's Tasks</Text>

            <View style={styles.items}>
              {/* This is where the tasks will go   */}
              {taskItems.map((item, index) => {
                return (
                  <TouchableOpacity onPress={() => completeTask(index)}>
                    <Task key={index} text={item} />
                  </TouchableOpacity>
                );
              })}
            </View>
          </View>
          {/* Write a task*/}

          <KeyboardAvoidingView
            behavior={Platform.OS === "ios" ? "padding" : "height"}
            style={styles.writeTaskWrapper}
          >
            <TextInput
              style={styles.input}
              placeholder={"Write a task"}
              value={task}
              onChangeText={(text) => setTask(text)}
            />
            <TouchableOpacity onPress={() => handleAddTask()}>
              <View style={styles.addWrapper}>
                <Text style={styles.addText}>+</Text>
              </View>
            </TouchableOpacity>
          </KeyboardAvoidingView>
        </View>
      </ScrollView>
    </SafeAreaProvider>
  );
}

```

接下来，我们将使用消费者组件包装任何我们预计可能受到移动设备屏幕修改影响的元素。在我们的例子中，我们将包含待办事项列表应用程序标题的`Text`子组件与`SafeAreaView`包装在一起，后者是`safe-area-context`中的主要消费者组件:

```
// ./App.js

import React from "react";
import { Text, View, StyleSheet, ScrollView } from "react-native";
import { SafeAreaView, SafeAreaProvider } from "react-native-safe-area-context";

export default function App() {
  return (
    <SafeAreaProvider>
      <ScrollView>
        <View style={styles.container}>

          ...

          <View style={styles.taskWrapper}>
            <SafeAreaView>
              <Text style={styles.sectionTitle}>Today's Tasks</Text>
            </SafeAreaView>
          </View>

          ...

        </View>
      </ScrollView>
    </SafeAreaProvider>
  );
}

```

`SafeAreaView`的行为就像一个常规的 React Native `View`组件，但是，它包括额外的填充和边距，将被包含的组件放置在远离任何移动屏幕修改的位置。

`SafeAreaView`包括一个用于`edges`的道具，允许你定制组件边缘的安全区域。`edges`取`top`、`right`、`bottom`、`left`的值，默认为`all`。

让我们看看添加了`SafeAreaView`组件后，我们的应用程序标题的定位有何不同:

 [https://www.youtube.com/embed/769bnGDLjLE?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/769bnGDLjLE?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

在上面的视频中，我们的应用程序的标题更加集中。无论设备类型或屏幕尺寸如何，由`SafeAreaView`提供的额外样式可防止移动屏幕修改阻塞标题。

`safe-area-context`包含 Jest 的内置测试。你可以在[文档](https://github.com/th3rdwave/react-native-safe-area-context)中阅读更多关于测试的内容。

## 使用`SafeAreaView`优化渲染

当用户旋转设备时,`SafeAreaView`组件可以防止渲染延迟。此外，React Native 使用一个名为`initialWindowMetrics`的提供者属性，您可以使用它来加速应用程序的初始呈现。让我们在`App.js`中加上`initialWindowMetrics`:

```
// ./App.js

import React from "react";
import { Text, View, StyleSheet, ScrollView } from "react-native";
import { SafeAreaView, SafeAreaProvider, InitialWindowMetrics } from "react-native-safe-area-context";

export default function App() {
  return (
    <SafeAreaProvider initialMetrics={initialWindowMetrics}>
      <ScrollView>
        <View style={styles.container}>

          ...

          <View style={styles.taskWrapper}>
            <SafeAreaView>
              <Text style={styles.sectionTitle}>Today's Tasks</Text>
            </SafeAreaView>
          </View>

          {/* Include TextInput and button for adding items here */}

        </View>
      </ScrollView>
    </SafeAreaProvider>
  );
}

```

`initialWindowsMetrics`属性被指定为`initialMetric`属性。只有当你的应用程序的提供者没有重新挂载，或者你的应用程序没有使用 [React 导航库](https://blog.logrocket.com/react-navigation-vs-react-native-navigation/)时，才应该使用这个属性。

## 摘要

与 web 应用程序相比，在移动应用程序中处理和管理数据可能是一个挑战。然而，像`safe-area-context`这样的工具提供了一种途径来构建容易适应不同移动屏幕的界面，为不同设备提供统一的 UX。

`safe-area-context`易于使用，只需用`SafeAreaView`组件包装元素即可。虽然默认值本身是有效的，但是您可以根据您的应用添加自定义值到`padding`、`margin`和`edges`。我希望你喜欢这个教程！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)