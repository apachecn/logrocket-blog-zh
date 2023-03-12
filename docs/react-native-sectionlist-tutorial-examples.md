# React Native SectionList 教程示例

> 原文：<https://blog.logrocket.com/react-native-sectionlist-tutorial-examples/>

在本教程中，我们将演示如何使用`SectionList`在 React 本地应用程序中呈现大型的分段列表。

## 什么是 SectionList？

顾名思义，SectionList 使您能够在 UI 中显示一个分段列表。这是一个高性能的界面，提供了有用的功能，包括滚动加载、拉动刷新、部分分隔符支持等。

为了展示`SectionList`如何工作，我们将创建一个显示新任务和已完成任务的待办事项应用程序。

最终结果将如下所示:

![Scrolling Section List](img/12f7c0cefad1d7ab57c8421a5a47464e.png)

## 创建新的 React 本机应用程序

您可以使用 [Create React Native app](https://reactnative.dev/blog/2017/03/13/introducing-create-react-native-app) CLI 工具创建 React Native App。

打开 CMD 或终端，键入以下命令:

```
create-react-native-app <your-app-name>   

```

这将为您的 React 本机应用程序创建新的样板代码。现在在代码编辑器中打开这个项目。

## 将 SectionList 添加到 React 本机应用程序中

在我们可以将`SectionList`添加到 React 本地应用程序之前，我们需要一些数据显示在我们的应用程序中。

打开您的`app.js`文件并添加以下数据(出于本演示的目的):

```
  const newTaskData = [{
    title: "New Tasks",
    data: [
      {
        id: "1",
        task: "Buy groceries"
      },
      {
        id: "2",
        task: "Feed Cat"
      },
      {
        id: "3",
        task: "Sleep for 3 hours"
      },
      {
        id: "4",
        task: "Water Plants"
      },
      {
        id: "5",
        task: "Drink Water"
      },
    ]
  }];
  const completedTaskData = [{
    title: "Completed Tasks",
    data: [
      {
        id: "6",
        task: "Make a section list tutorial"
      },
      {
        id: "7",
        task: "Share this tutorial"
      },
      {
        id: "8",
        task: "Ask doubt in the Comments"
      },
      {
        id: "9",
        task: "Subscribe to logrocket"
      },
      {
        id: "10",
        task: "Read next Article"
      },
      {
        id: "11",
        task: "Read next Article 2"
      },
      {
        id: "12",
        task: "Read next Article 3"
      },
      {
        id: "13",
        task: "Read next Article 4"
      },
      {
        id: "14",
        task: "Read next Article 5"
      },
      {
        id: "15",
        task: "Read next Article 6"
      },
      {
        id: "16",
        task: "Read next Article 7"
      },
      {
        id: "17",
        task: "Read next Article 8"
      },
      {
        id: "18",
        task: "Read next Article 9"
      },
      {
        id: "19",
        task: "Read next Article 10"
      },
    ]
  }];

```

这是我们将在`SectionList`中使用的虚拟数据。

添加完数据后，就该实现`SectionList`了。使用下面的代码添加`SectionList`:

```
import { SectionList } from 'react-native';
...
...
<SectionList
        sections={[...newTaskData, ...completedTaskData]}
        renderItem={({item})=>(
            <Text style={styles.taskItem}>{item.task}</Text>
        )}
        renderSectionHeader={({section})=>(
          <Text style={styles.taskTitle}>{section.title}</Text>
        )}
        keyExtractor={item=>item.id}
        stickySectionHeadersEnabled
      />

```

如您所见，我们将一些虚拟数据作为单个数组传递给了一个`sections` prop。这个数组负责创建不同的部分。我们只是将数组展开，然后将它们合并成一个截面支柱。

接下来，我们必须返回`renderItem prop`的单个项目视图。我们将返回一个`Text`组件，并在文本中显示该项目的任务属性。这将打印列表中的任务。

## 向您的列表添加标题

现在我们需要使用`renderSectionHeader`属性添加我们的头。

就像我们在`renderItem`属性中返回一个视图一样，我们也必须在这里返回一个视图。在这段代码中，我们再次返回一个`Text`组件。这一次，我们获取部分数据并打印其中的`title`属性。

当使用`SectionList`或者迭代和返回视图时，指定键是一个好习惯。

key 属性用于标识列表中的哪些项目被更新、更改或删除。这使得你的列表更有性能。

要为所有视图添加关键点，使用`keyExtractor`属性，如上面的代码所示。

我们必须返回一个唯一的值，某种 ID。如果数据中没有任何唯一的 ID，我们可以使用索引。

要使用`keyExtractor`中的索引，我们可以使用下面的代码:

```
  keyExtractor={(item, index)=>index.toString()}

```

注意，我们返回的是`index.toString()`，而不仅仅是`index`，因为键应该是一个字符串，而不是一个数字。

我们还添加了一个道具`stickySectionHeadersEnabled`，用于在滚动列表时将标题粘贴到顶部。

## 将样式添加到`SectionList`

现在让我们给我们的列表添加一些样式。

在下面的代码示例中，我们向列表添加了一些基本样式。请随意添加您自己的风格，以适应您的个人偏好或项目的要求。

```
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#eafffe'
  },
  taskItem:{
    padding: 10,
    marginVertical: 15,
    fontSize: 16
  },
  taskTitle:{
    backgroundColor: "#ffffff",
    fontSize: 20,
    fontWeight: "bold",
    padding: 10,
    elevation: 4,
    margin: 10,
    marginBottom: 0,
    borderRadius: 10
  }
});

```

这些是用于本教程的基本样式；这里没什么特别的。

以下是完整的代码:

```
import React from 'react';
import { SectionList, StyleSheet, Text, View } from 'react-native';
export default function App() {
  const newTaskData = [{
    title: "New Tasks",
    data: [
      {
        id: "1",
        task: "Buy groceries"
      },
      {
        id: "2",
        task: "Feed Cat"
      },
      {
        id: "3",
        task: "Sleep for 3 hours"
      },
      {
        id: "4",
        task: "Water Plants"
      },
      {
        id: "5",
        task: "Drink Water"
      },
    ]
  }];
  const completedTaskData = [{
    title: "Completed Tasks",
    data: [
      {
        id: "6",
        task: "Make a section list tutorial"
      },
      {
        id: "7",
        task: "Share this tutorial"
      },
      {
        id: "8",
        task: "Ask doubt in the Comments"
      },
      {
        id: "9",
        task: "Subscribe to logrocket"
      },
      {
        id: "10",
        task: "Read next Article"
      },
      {
        id: "11",
        task: "Read next Article 2"
      },
      {
        id: "12",
        task: "Read next Article 3"
      },
      {
        id: "13",
        task: "Read next Article 4"
      },
      {
        id: "14",
        task: "Read next Article 5"
      },
      {
        id: "15",
        task: "Read next Article 6"
      },
      {
        id: "16",
        task: "Read next Article 7"
      },
      {
        id: "17",
        task: "Read next Article 8"
      },
      {
        id: "18",
        task: "Read next Article 9"
      },
      {
        id: "19",
        task: "Read next Article 10"
      },
    ]
  }];
  return (
    <View style={styles.container}>
      <SectionList
        sections={[...newTaskData, ...completedTaskData]}
        renderItem={({item})=>(
            <Text style={styles.taskItem}>{item.task}</Text>
        )}
        renderSectionHeader={({section})=>(
          <Text style={styles.taskTitle}>{section.title}</Text>
        )}
        keyExtractor={item=>item.id}
        stickySectionHeadersEnabled
      />
    </View>
  );
}
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#eafffe'
  },
  taskItem:{
    padding: 10,
    marginVertical: 15,
    fontSize: 16
  },
  taskTitle:{
    backgroundColor: "#ffffff",
    fontSize: 20,
    fontWeight: "bold",
    padding: 10,
    elevation: 4,
    margin: 10,
    marginBottom: 0,
    borderRadius: 10
  }
});

```

## 有用的`SectionList`道具

让我们简单地放大几个你应该知道的有用的 SectionList 道具:`ItemSeparatorComponent`和`onEnReached`。

*   当您想要将任何视图显示为节列表中每个项目的分隔符时,`ItemSeparatorComponent`非常有用
*   当您滚动到列表底部时，会调用`onEndReached`。如果您想在列表底部加载一些数据，这会很有帮助

## 结论

在本教程中，我们演示了如何使用`SectionList`在 React 本地应用程序中呈现大型的分段列表。我们介绍了创建 React 原生应用程序、添加`SectionList`、为列表创建标题以及添加自定义样式的过程。

正如你所看到的，在 React 本地应用中使用`SectionList`是一种非常方便的显示数据的方式，并且很容易维护你的应用的 UI。如果没有 SectionList 组件，由于维护 UI 及其变化的挑战，显示节列表将是一场噩梦。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)