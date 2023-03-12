# 如何在 React - LogRocket 博客中使用 FlatList 组件

> 原文：<https://blog.logrocket.com/how-to-use-flatlist-component-in-react/>

`FlatList`组件是最简单和最常用的 React 本地组件之一。如果您刚刚开始使用 React Native，您必须了解`FlatList`组件的要点并理解其用途。

在构建 app 的时候，你会碰到一些列表；这些可以是联系人、用户、活动等的列表。它们看起来很容易实现。但是，显示大型数据集或长列表的性能不是很好。这就是 React Native 的`FlatList`组件发挥作用的地方。

## `flatList`组件介绍

`FlatList`组件用于显示大量的可滚动列表项。在引擎盖下，`FlatList`组件利用了`ScrollView`组件。

尽管如此，它还是增加了一个性能因素，比如只呈现屏幕上可见的项目，只更新已经更改的项目。`FlatList`组件非常适合可能会随时间变化的大型数据项。

使用`FlatList`组件需要知道两个主要的和必需的道具——`data`和`renderItem`。

属性是一个数据数组，用作创建列表的源。`renderItem`是一个回调函数，它将接受来自`data`道具的单个项目，并为其呈现一个组件。您不再需要格式化数据—相反，您可以传递一个数组并立即开始呈现。

## 基本用法

假设您有一个包含国家列表的数据数组:

```
js
const data = ["Germany", "Australia", "Sri Lanka", "Japan"];
```

现在，为了呈现国家，创建一个名为`Country`的组件:

```
jsx
import { Text, View } from "react-native";

const Country = ({ name }) => (
  <View>
    <Text>{name}</Text>
  </View>
);
```

在`App`组件中，让数据进入状态:

```
jsx
const data = ["Germany", "Australia", "Sri Lanka", "Japan"];

const App = () => {
    constructor(props) {
        super(props);

        this.state = {
            countries: data
        }
    }

    render() {
        // ...
    }
}
```

接下来，从`react-native`导入`FlatList`组件，并使用它来显示国家:

```
jsx
import { FlatList, Text, View } from "react-native";

// ...

const App = () => {
    constructor(props) {
        super(props);

        this.state = {
            countries: data
        }
    }

    render() {
        return (
            <View style={{ flex: 1 }}>
                <FlatList
                    data={this.state.data}
                    renderItem={({item}) => <Country name={item} />}
                />
            </View>
        );
    }
}
```

`renderItem`函数作为单个参数传递，它是一个对象。

您需要的数据在`item`属性中，因此您可以使用析构来访问数据。在这种情况下，您需要将`item`传递给`Country`组件的`name`支柱。

请注意，根据组件可能采用的道具和道具类型，这在不同的情况下可能会有所不同。

下面，您可以找到这一部分的完整代码:

```
jsx
import React from "react";
import { FlatList, Text, View } from "react-native";

const data = ["Germany", "Australia", "Sri Lanka", "Japan"];

const Country = ({ name }) => (
  <View>
    <Text>{name}</Text>
  </View>
);

const App = () => {
    constructor(props) {
        super(props);

        this.state = {
            countries: data
        }
    }

    render() {
        return (
            <View style={{ flex: 1 }}>
                <FlatList
                    data={this.state.data}
                    renderItem={({item}) => <Country name={item} />}
                />
            </View>
        );
    }
}
export default App;
```

## 从 API 获取数据

在一个更实际的场景中，您可能希望显示从后端服务获取的数据。在本节中，您将看到一些使用`FlatList`组件的真实例子。

因此，首先创建一个新项目:

```
sh
npx react-native init FlatListDemo
```

为了设计组件的样式，使用`react-native-elements`库:

```
sh
yarn add react-native-elements react-native-vector-icons
```

首先在屏幕上添加 header 组件:

```
jsx
import { Header } from "react-native-elements";

const AppHeader = () => (
  <Header
    centerComponent={{ text: "USERS", style: { color: "#fff" } }}
    containerStyle={{ paddingTop: 0, height: 60 }}
  />
);

const App = () => {
  return (
    <View>
      <AppHeader />
    </View>
  );
};
```

接下来，使用 JSON 占位符 API 获取用户信息:

```
jsx
class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      users: [],
      isLoading: false,
    };
  }

  fetchUsers = async () => {
    this.setState({ isLoading: true });
    try {
      const res = await fetch(JSON_API_URL);
      const users = await res.json();
      this.setState({ users });
    } catch (err) {
      console.log(err);
    }
    this.setState({ isLoading: false });
  };

  componentDidMount() {
    this.fetchUsers();
  }

  render() {
    return (

         (

          )}
          keyExtractor={(user) => user.id}
          ItemSeparatorComponent={() => }
        />

    );
  }
}
```

`keyExtractor` prop 检索列表项的键。这些唯一的键通知 FlatList 组件跟踪项目，这对效率和性能非常重要。

您必须为密钥选择一个唯一的值。在这种情况下，它就是`id`。默认情况下，`keyExtractor`会退回到该项的索引，但是您仍然会看到警告。

`ItemSeperatorComponent` prop 允许您在列表项之间添加分隔符组件。在本例中，您添加了`Divider`组件。

## 拉至刷新

拉至刷新是一项基本功能，当用户将屏幕拉至底部并加载新数据时，该功能允许用户刷新屏幕上的数据。

在任何有活动或数据馈送的应用程序中，拉至刷新都是至关重要的，需要刷新才能查看新内容。

当用户在你的应用上花费大量时间浏览提要，并且新数据在某个时间点变得可用时，就会发生这种情况。

在一个`FlatList`组件中实现拉取刷新功能需要两个道具，即`refreshing`和`onRefresh`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

当加载新数据时，`refreshing`属性告诉`FlatList`组件显示一个`RefreshControl`组件。

它接受一个`Boolean`值。`onRefresh`是一个回调属性，当`refreshing`属性设置为`true`时，它调用函数。该函数必须负责加载新数据和更新`FlatList`组件中的列表项。

```
jsx
class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      users: [],
      isLoading: false,
    };
  }

  fetchUsers = async () => {
    this.setState({ isLoading: true });
    try {
      const res = await fetch(JSON_API_URL);
      const users = await res.json();
      this.setState({ users });
    } catch (err) {
      console.log(err);
    }
    this.setState({ isLoading: false });
  };

  componentDidMount() {
    this.fetchUsers();
  }

  render() {
    return (

         (

          )}
          keyExtractor={(user) => user.id}
          refreshing={this.state.isLoading}
          onRefresh={this.fetchUsers}
          ItemSeparatorComponent={() => }
        />

    );
  }
}
```

## 页码

当有大量数据需要加载时，分页是应用程序中的另一个重要功能。

实际上，你不能一次加载所有的数据——这会让你的应用程序非常慢，效率很低，导致性能差，用户体验差。

对于这个例子，使用伪用户生成器 API，因为它有一个分页选项，并且还包含用户头像。

基本思想是在组件中有一个状态变量，它跟踪当前页面，并在用户到达列表末尾时递增。

当用户到达末尾时，用当前页面值获取新的列表数据。这可以使用`onEndReached`属性在`FlatList`组件中实现。`onEndReached`道具接受一个当用户到达列表末尾时触发的功能。

```
jsx
const Loader = () => (
  <View style={{ minHeight: 230, padding: 20 }}>
    <ActivityIndicator
      color="#000"
      size="large"
      style={{ alignSelf: "center" }}
    />
  </View>
);

class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      users: [],
      isLoading: false,
      isLoadingMore: false,
      page: 1,
    };
  }

  fetchUsers = async () => {
    this.setState({ isLoading: true });
    try {
      const res = await fetch(`${JSON_API_URL}&page=${this.state.page}`);
      const { results: users } = await res.json();
      if (this.state.page === 1) this.setState({ users });
      else this.setState({ users: [...this.state.users, ...users] });
    } catch (err) {
      console.log(err);
    }
    this.setState({ isLoading: false });
  };

  refreshUsers = () => {
    this.setState({ page: 1 }, () => {
      this.fetchUsers();
    });
  };

  loadMoreUsers = () => {
    this.setState({ page: this.state.page + 1, isLoadingMore: true }, () => {
      this.fetchUsers();
      this.setState({ isLoadingMore: false });
    });
  };

  componentDidMount() {
    this.fetchUsers();
  }

  render() {
    return (
      <View>
        <AppHeader />
        <FlatList
          data={this.state.users}
          renderItem={({ item }) => (
            <ListItem
              title={`${item.name.first} ${item.name.last}`}
              subtitle={item.login.username}
              leftAvatar={{ source: { uri: item.picture.thumbnail } }}
            />
          )}
          keyExtractor={(user) => user.login.uuid}
          refreshing={this.state.isLoading}
          onRefresh={this.refreshUsers}
          ItemSeparatorComponent={() => <Divider />}
          ListFooterComponent={this.state.isLoadingMore && <Loader />}
          onEndReachedThreshold={0.1}
          onEndReached={this.loadMoreUsers}
        />
      </View>
    );
  }
}
```

## 结论

这就是 React Native 中的`FlatList`组件。

现在您已经了解了`FlatList`组件，您可以从任何 API 获取数据并在您的应用程序中显示列表项。

确保在应用中实现拉刷新和无限滚动，因为这可以改善整体用户体验，并使应用更专业。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)