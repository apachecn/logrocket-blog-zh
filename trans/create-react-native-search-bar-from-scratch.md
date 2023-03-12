# 从头开始创建一个 React 原生搜索栏

> 原文：<https://blog.logrocket.com/create-react-native-search-bar-from-scratch/>

试着想象一下没有机会过滤数据的今天的数字世界。不可思议，对吧？

搜索栏是实现这种过滤器的一个例子。如果你现在想到手机上的任何一个应用，你的脑海里应该会蹦出很多搜索栏的例子。

在本教程中，我们将从头开始在 React Native 中创建一个搜索栏。React Native 是一个基于 JavaScript 的框架，它使得构建跨平台(iOS 和 Android)的移动应用程序变得非常容易。

观看下面的视频，获得这个小项目的第一印象！正如您所看到的，示例应用程序将包含一个屏幕，其中列出了您可以应用过滤器的编程语言。

[https://www.youtube.com/embed/klLqbhvoV6g](https://www.youtube.com/embed/klLqbhvoV6g)

视频

## 先决条件

我在开发 React 本地应用程序时使用了 [Expo](https://docs.expo.dev) ，所以您应该通过在您的机器上运行以下代码来安装 Expo 客户端:

```
npm install --global expo-cli
```

如果您不使用 Expo，本教程中的所有内容应该仍然有效，除了我们稍后将使用的 Expo 矢量图标。如果你没有使用 Expo，我建议你用 [React Native Vector 图标](https://github.com/oblador/react-native-vector-icons)替换这些图标。

除了使用 Expo，我可以推荐使用 Visual Studio 代码或类似的东西。JavaScript 和 React/React Native 的一些基础知识会帮助你理解这个教程。

## 从头开始构建 React 原生搜索栏

首先，让我们深入实际代码！首先，转到您想要存储项目的目录。在这个目录中，运行`expo init my-project`来初始化 Expo 项目。你可以用任何你喜欢的名字代替`my-project`。

然后，使用`cd my-project`转到新创建的目录，并运行`expo start`来启动开发服务器。Expo 让你决定选择使用哪种设备；我在上面的演示和视频中使用的设备是一台 iPhone 12 Pro Max。

如果你想让应用程序马上运行，只需从 [GitHub repo](https://github.com/kevintomas1995/logRocket_searchBar) 中克隆代码，并在应用程序目录中运行`npm install`。
下面，我总结了终端命令:

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

### React 本地搜索栏的项目目录

为了保持清晰，我总是在我的 React 原生项目中添加一个`screens`文件夹和`components`文件夹。在这里，`screens`文件夹将只包含一个文件，因为我们只需要查看一个屏幕，搜索栏将在那里运行。

`components`文件夹将包含任何 React 本地组件，这些组件可以外包给一个单独的文件。在这个示例应用程序中，我们在这个文件夹中有两个文件，分别叫做`List.js`和`SearchBar.js`。

我们首先来看一下`SearchBar.js`文件。

## 在`SearchBar.js`中定义我们的搜索栏

下面，您将在我们的`SearchBar.js`文件中找到内容。这个组件还不包含任何过滤逻辑，它只负责显示搜索栏本身。

```
// SearchBar.js
import React from "react";
import { StyleSheet, TextInput, View, Keyboard, Button } from "react-native";
import { Feather, Entypo } from "@expo/vector-icons";

const SearchBar = ({clicked, searchPhrase, setSearchPhrase, setCLicked}) => {
  return (
    <View style={styles.container}>
      <View
        style={
          clicked
            ? styles.searchBar__clicked
            : styles.searchBar__unclicked
        }
      >
        {/* search Icon */}
        <Feather
          name="search"
          size={20}
          color="black"
          style={{ marginLeft: 1 }}
        />
        {/* Input field */}
        <TextInput
          style={styles.input}
          placeholder="Search"
          value={searchPhrase}
          onChangeText={setSearchPhrase}
          onFocus={() => {
            setClicked(true);
          }}
        />
        {/* cross Icon, depending on whether the search bar is clicked or not */}
        {clicked && (
          <Entypo name="cross" size={20} color="black" style={{ padding: 1 }} onPress={() => {
              setSearchPhrase("")
          }}/>
        )}
      </View>
      {/* cancel button, depending on whether the search bar is clicked or not */}
      {clicked && (
        <View>
          <Button
            title="Cancel"
            onPress={() => {
              Keyboard.dismiss();
              setClicked(false);
            }}
          ></Button>
        </View>
      )}
    </View>
  );
};
export default SearchBar;

// styles
const styles = StyleSheet.create({
  container: {
    margin: 15,
    justifyContent: "flex-start",
    alignItems: "center",
    flexDirection: "row",
    width: "90%",

  },
  searchBar__unclicked: {
    padding: 10,
    flexDirection: "row",
    width: "95%",
    backgroundColor: "#d9dbda",
    borderRadius: 15,
    alignItems: "center",
  },
  searchBar__clicked: {
    padding: 10,
    flexDirection: "row",
    width: "80%",
    backgroundColor: "#d9dbda",
    borderRadius: 15,
    alignItems: "center",
    justifyContent: "space-evenly",
  },
  input: {
    fontSize: 20,
    marginLeft: 10,
    width: "90%",
  },
});

```

正如你在第 2-4 行看到的，我们不需要导入太多来创建一个定制的搜索栏。这里最有趣的导入是`@expo/vector-icons`，我们将使用它来实现一些好看的图标。

从第 9 行开始，我们定义了实际的搜索栏。在随后的几行(67-83)中，您会注意到我使用了条件样式来描述搜索栏的两个“版本”:未点击的搜索栏和点击的搜索栏。

如果您查看以下代码片段，这将变得更加清楚:

```
<View
        style={
          clicked
            ? styles.searchBar__clicked
            : styles.searchBar__unclicked
        }
      >
```

如果`clicked`设置为真，则`styles.searchBar__clicked`将被应用。否则，将应用`styles.searchBar__unclicked`。

![The search bar before the user clicks into the text input field](img/770d124461cb85ed4dfcf32ef43103cc.png)

The search bar before the user clicks into the text input field

![The search bar after the user clicks into the text input field](img/53e0d7214120ac7b7942f2abbd97fb85.png)

The search bar after the user clicks into the text input field

通过`clicked`确定是否点击了该条。我们将在本教程稍后查看主屏幕时进入这种状态，所有的道具都在主屏幕上管理。

在包含搜索栏的`<View>`中，我们还可以找到搜索图标(第 17-22 行)、文本输入(第 24-32 行)和十字图标(第 35-38 行)。让我们在下一节回顾一下这些。

## 在 React Native 中实现文本输入监听器

这里的文本输入包含了一些有趣的道具。先说`onChangeText`属性。

有了这个特性，您可以添加一个监听器，它将在每次`TextInput-field`中的文本改变时触发。在我们的例子中，我们调用`setSearchPhrase`函数。这样，我们可以将文本输入从子组件(`SearchBar.js`)传递到父组件(`Home.js`)。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这个文本输入然后通过`searchPhrase`传递给`TextInput`组件的`value`属性。该属性定义了文本输入的实际显示值，因此得名。

最后，我们定义了`onFocus`属性，这样当搜索栏被点击时，它会告诉应用程序该做什么。只有当`clicked`设置为`true`时，才会显示十字(第 34 行)和取消按钮(第 41 行)。

如果您问自己为什么我们甚至需要将这些数据传递给父组件，要知道我们不仅需要在`SearchBar.js`组件中的搜索短语和其他数据，还需要在`List.js`组件中，我们将在那里实际过滤数据。

下图说明了不同组件之间传递的数据的连接:

![passing data through components](img/235e611b505d549893efaab39a44027e.png)

## 过滤`List.js`中输入的文本

为了呈现数据，我使用了一个名为 FlatList 的 [React 本地组件。如果您查看`List.js`的第 41-45 行，您会注意到在我们的例子中，FlatList 将`data`、`renderItem`和`keyExtractor`作为属性。](https://blog.logrocket.com/deep-dive-react-native-flatlist/)

```
// List.js
import React from "react";
import {
  StyleSheet,
  Text,
  View,
  FlatList,
  SafeAreaView,
} from "react-native";

// definition of the Item, which will be rendered in the FlatList
const Item = ({ name, details }) => (
  <View style={styles.item}>
    <Text style={styles.title}>{name}</Text>
    <Text style={styles.details}>{details}</Text>
  </View>
);

// the filter
const List = ({ searchPhrase, setCLicked, data }) => {
  const renderItem = ({ item }) => {
    // when no input, show all
    if (searchPhrase === "") {
      return <Item name={item.name} details={item.details} />;
    }
    // filter of the name
    if (item.name.toUpperCase().includes(searchPhrase.toUpperCase().trim().replace(/\s/g, ""))) {
      return <Item name={item.name} details={item.details} />;
    }
    // filter of the description
    if (item.details.toUpperCase().includes(searchPhrase.toUpperCase().trim().replace(/\s/g, ""))) {
      return <Item name={item.name} details={item.details} />;
    }
  };

  return (
    <SafeAreaView style={styles.list__container}>
      <View
        onStartShouldSetResponder={() => {
          setClicked(false);
        }}
      >
        <FlatList
          data={data}
          renderItem={renderItem}
          keyExtractor={(item) => item.id}
        />
      </View>
    </SafeAreaView>
  );
};

export default List;

const styles = StyleSheet.create({
  list__container: {
    margin: 10,
    height: "85%",
    width: "100%",
  },
  item: {
    margin: 30,
    borderBottomWidth: 2,
    borderBottomColor: "lightgrey"
  },
  title: {
    fontSize: 20,
    fontWeight: "bold",
    marginBottom: 5,
    fontStyle: "italic",
  },
});

```

我们需要在第 11 行定义的第一件事是`Item`，它将显示编程语言的名称和细节。在另一个步骤中，从第 20 行开始，我们定义这个`Item`实际发生了什么。这是我们根据文本输入过滤编程语言列表的地方。

在`renderItem`函数中，我们定义了刚刚定义的项目应该如何在`FlatList`组件中呈现。在我们的例子中，我们区分了三种情况。在第一种情况下(第 22 行)，我们指的是没有输入的情况(`(searchPhrase === "")`)。这里，我们简单地呈现列表中所有可用的项目。

通过第 26 行的`(item.name.toUpperCase().includes(searchPhrase.toUpperCase().trim().replace(/\s/g, "")))`,我们过滤了编程语言的名称。我们将输入和名称都转换为大写，并删除所有空格。

在`renderItem`函数中列出了三个检查，它们是实际的过滤器。在`List.js`的第 27 行，我们检查项目名称(编程语言)是否包含文本输入(`searchPhrase`)。这将为我们的数据中的每个项目完成，如果检查结果为真，那么项目将被呈现。

在第 30 行，我们做了同样的事情，只是我们将它应用于编程语言的描述。

实现过滤逻辑的方法有很多种，所以您可以自行尝试一些东西！

现在，我们已经为 FlatList 准备好了[的一切，让我们看看我们实际上从这个`List`组件返回了什么(第 35-50 行)。在第 37 行，您将看到我将平面列表包装在一个`View`中。这是因为我想实现一个功能，确保当我们点击或点击搜索栏外的任何地方时，键盘将被关闭，`clicked`将被设置为`false`。`onStartShouldSetResponder={() => { setClicked(false) }}`实现了这一逻辑。](https://blog.logrocket.com/how-to-use-flatlist-component-in-react/)

来自`FlatList`(第 43 行)的`data`属性中的数据将由其父组件提供。`renderItem`将从 20 号线设置到我们的`const renderItem`。对于`keyExtractor`，我们将使用相应项目的`id`。

`keyExtractor`提取一个项目的唯一键。这对于缓存和有效地重新排序`FlatList`中的项目非常重要。

在下一步中，我将浏览`Home.js`组件，在那里所有的东西都聚集在一起！坚持住，你差点就成功了！

## 在`Home.js`文件中完成我们的 React 本地搜索栏

下面是我们的`Home.js`文件的样子:

```
// Home.js
import React, { useState, useEffect } from "react";
import {
  StyleSheet,
  Text,
  SafeAreaView,
  ActivityIndicator,
} from "react-native";
import List from "../components/List";
import SearchBar from "../components/SearchBar";

const Home = () => {
  const [searchPhrase, setSearchPhrase] = useState("");
  const [clicked, setClicked] = useState(false);
  const [fakeData, setFakeData] = useState();

  // get data from the fake api endpoint
  useEffect(() => {
    const getData = async () => {
      const apiResponse = await fetch(
        "https://my-json-server.typicode.com/kevintomas1995/logRocket_searchBar/languages"
      );
      const data = await apiResponse.json();
      setFakeData(data);
    };
    getData();
  }, []);

  return (
    <SafeAreaView style={styles.root}>
      {!clicked && <Text style={styles.title}>Programming Languages</Text>}
      <SearchBar
        searchPhrase={searchPhrase}
        setSearchPhrase={setSearchPhrase}
        clicked={clicked}
        setClicked={setClicked}
      />
      { : (

          <List
            searchPhrase={searchPhrase}
            data={fakeData}
            setClicked={setClicked}
          />

      )}
    </SafeAreaView>
  );
};

export default Home;

const styles = StyleSheet.create({
  root: {
    justifyContent: "center",
    alignItems: "center",
  },
  title: {
    width: "100%",
    marginTop: 20,
    fontSize: 25,
    fontWeight: "bold",
    marginLeft: "10%",
  },
});

```

首先，我们在第 9 行和第 10 行导入我们的定制组件`List`和`SearchBar`。下面，我们定义三种不同的状态，其中两种(`clicked`和`searchPhrase`)你应该很熟悉。第三个`fakeData`，将是第 18 行`useEffect`挂钩中使用的[。](https://blog.logrocket.com/guide-to-react-useeffect-hook/)

为了让这个例子尽可能真实，我模拟了从一个 API 端点获取数据。在`useEffect`钩子内部，我们定义了一个名为`getData`的异步函数，在这里我们从[假 API](https://my-json-server.typicode.com) 获取数据，并将数据存储在名为`fakeData`的常量中。定义函数后，我们立即在第 26 行调用它。我没有在第 27 行定义任何依赖关系，所以这个钩子只有在组件被渲染后才会被执行。

标题“编程语言”用`{!clicked && <Text style={styles.title}>Programming Languages</Text>}`实现，并将根据`clicked`的值有条件地呈现。这意味着只有当`searchBar`不是`clicked`时，文本“编程语言”才会被渲染。

下一步，我们用道具`searchPhrase`、`setSearchPhrase`、`clicked`和`setClicked`来呈现我们的`SearchBar`组件。

获取数据可能需要几秒钟的时间，所以我们需要在这段时间内进行渲染。在这种情况下，让我们渲染一个加载微调器。因此，只要`fakeData`未定义，我们将使用`!fakeData ? (<ActivityIndicator` `size="large" />)`渲染加载微调器，否则将渲染`List`组件。

差不多就是这样了！最后，您只需要像这样将这个`Home.js`文件导入到您的`App.js`文件中:

```
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';
import Home from './screens/Home';

export default function App() {
  return (
    <View style={styles.root}>
      <Home />
    </View>
  );
}

```

## 结论

在这篇文章中，我们回顾了如何创建自己的自定义 React 原生搜索栏，而无需安装任何外部库！数据是从一个假的 API 端点获取的，并用一个 FlatList 组件显示。此外，我们还讨论了如何实现条件样式。

请随意使用这段代码作为进一步开发的基础。例如，您可以在服务器端实现数据过滤，而不是像我们在这里所做的那样在客户端实现。这个项目的[源代码可以在我的 GitHub 上找到。](https://github.com/kevintomas1995/logRocket_searchBar)

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)