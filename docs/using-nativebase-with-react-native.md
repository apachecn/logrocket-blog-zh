# 使用 NativeBase 和 React Native 

> 原文：<https://blog.logrocket.com/using-nativebase-with-react-native/>

React Native 是一个使用 JavaScript 和 React 构建跨平台原生应用的框架。React Native 使用 JSX 和基于组件的 UI 系统，就像 React 一样，但是 React Native 使用本地组件来创建本地 UI 元素，而不是 DOM 组件。React Native 有一个非常低级的 API 和原子组件，迫使你为每个新项目重复重写许多基本组件。

在开发用户界面时，每个组件都需要大量的基本样式。造型可能很乏味，你可能不想为你开发的每个应用程序创建一个新的设计系统。理想情况下，您应该有一个包含组件基本样式的样式库。

这就是 NativeBase 的用武之地。在本指南中，我们将向您展示如何在 React 本机应用程序中使用和定制 NativeBase 组件。

## 什么是 NativeBase？

NativeBase 是一个开源的 UI 库，它可以帮助您使用按钮、卡片、输入字段等用户界面组件。

可以把它想象成一个用 React Native 构建移动应用的 CSS 框架。你可以立即找到合适的款式。您所要做的就是从 NativeBase 库中获取并调用该组件。

## 安装 NativeBase

使用`react-native init`命令创建一个新的 React 本地项目，然后将`cd`放入项目目录。

```
react-native init NativeBaseIntroApp
cd NativeBaseIntroApp

```

通过运行以下命令安装 NativeBase。

```
npm i native-base
react-native link

```

## 基本 NativeBase 组件

`Header`组件为应用程序呈现标题。它期望它的子组件是`Left`、`Body`和`Right`组件。

`Left`组件将需要呈现的组件包装在头部的左侧。例如，它可能是一个抽屉导航图标。类似地，`Right`组件是需要显示在标题右侧的组件的包装器。

`Body`组件充当在`Header`中心呈现的组件的包装器。`Title`和`Subtitle`组件通常由`Body`组件包装，分别用于显示标题和副标题。

```
import React, { Component } from "react";
import {
  Container,
  Header,
  Left,
  Body,
  Right,
  Title,
  Subtitle,
} from "native-base";
export default class App extends Component {
  render() {
    return (
      <Container>
        <Header>
          <Left />
          <Body>
            <Title>Header</Title>
            <Subtitle>Example subtitle</Subtitle>
          </Body>
          <Right />
        </Header>
      </Container>
    );
  }
}

```

### `Button`

组件是任何应用程序的基本 UI 元素。按钮用于指示屏幕上的交互操作，如提交表单。

要在屏幕上呈现一个按钮，使用`Button`组件。为了呈现按钮内部的文本，添加来自`native-base`的`Text`组件作为子组件。

```
import React, { Component } from "react";
import { Container, Header, Content, Button, Text } from "native-base";
class App extends Component {
  render() {
    return (
      <Container>
        <Header />
        <Content>
          <Button>
            <Text>Example Button</Text>
          </Button>
        </Content>
      </Container>
    );
  }
}

export default App;

```

您可以向`onPress`道具添加一个功能来触发所需的功能。还有各种按钮主题道具，包括`primary`、`success`、`info`、`warning`、`danger`、`light`、`dark`。

```
import React, { Component } from "react";
import { Alert } from "react-native";
import { Container, Header, Content, Button, Text } from "native-base";
class App extends Component {
  showAlert = () => Alert.alert("Message", "Button Clicked!!");

  render() {
    return (
      <Container>
        <Header />
        <Content>
          <Button onPress={this.showAlert} primary>
            <Text>Interactive Button</Text>
          </Button>
        </Content>
      </Container>
    );
  }
}

export default App;

```

您可以使用`transparent`属性使按钮透明，并使用`disabled`属性确定按钮是否被禁用。同样，要创建一个全宽按钮，你可以使用`fullwidth`道具。

NativeBase 的`Button`组件的子组件中也可以有一个`Icon`，以及`Text`组件。

```
import React, { Component } from "react";
import { Alert } from "react-native";
import { Container, Header, Content, Button, Text, Icon } from "native-base";
class App extends Component {
  showAlert = () => Alert.alert("Message", "Button Clicked!!");

  render() {
    return (
      <Container>
        <Header />
        <Content>
          <Button onPress={this.showAlert} primary fullwidth iconLeft>
            <Icon name="cog">
            <Text>Settings</Text>
          </Button>
        </Content>
      </Container>
    );
  }
}

export default App;

```

### `Card`

卡片是一个容器组件，用于在屏幕上向用户显示信息。它可以包含页眉、页脚、上下文背景颜色、不同类型的内容等等。

默认情况下,`Card`组件添加一个方框阴影，并提供卡片之间的间距和对齐。`CardItem`组件是`Card`的子组件，可以接受`Text`、`Button`、`Image`等输入。一个`Card`组件可以有任意数量的`CardItem`组件作为其子组件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
import React, { Component } from "react";
import {
  Container,
  Header,
  Content,
  Card,
  CardItem,
  Body,
  Text,
} from "native-base";
class App extends Component {
  render() {
    return (
      <Container>
        <Header />
        <Content>
          <Card>
            <CardItem>
              <Body>
                <Text>
                  Lorem ipsum dolor sit amet, consectetur adipiscing elit. Proin
                  nulla velit, imperdiet sit amet posuere ut, luctus et quam.
                  Nam molestie pretium mauris, vitae rutrum ipsum vulputate ac.
                </Text>
              </Body>
            </CardItem>
          </Card>
        </Content>
      </Container>
    );
  }
}

export default App;

```

要在卡片中添加页眉和页脚，请对第一个`CardItem`组件实例使用`header`属性，对最后一个`CardItem`组件实例使用`footer`。

```
// ...
<Card>
  <CardItem header>
    <Text>Example Card Header</Text>
  </CardItem>
  <CardItem>
    <Body>
      <Text>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Proin nulla
        velit, imperdiet sit amet posuere ut, luctus et quam. Nam molestie
        pretium mauris, vitae rutrum ipsum vulputate ac.
      </Text>
    </Body>
  </CardItem>
  <CardItem footer>
    <Text>Example Card Footer</Text>
  </CardItem>
</Card>
// ...

```

您还可以通过使用`CardItem`组件上的`button`属性来创建一个可触摸的购物车条目。当你想向用户显示更多内容时，最好是在新的屏幕上，这个`button`道具很有用。

```
// ...
<Card>
  <CardItem
    header
    button
    onPress={() => Alert.alert("Message", "Card Header Pressed")}
  >
    <Text>Example Card Header</Text>
  </CardItem>
  <CardItem>
    <Body>
      <Text>The is dummy card text.</Text>
    </Body>
  </CardItem>
  <CardItem
    footer
    button
    onPress={() => Alert.alert("Message", "Card Footer Pressed")}
  >
    <Text>Example Card Footer</Text>
  </CardItem>
</Card>
// ...

```

### `List`

`List`组件用于在屏幕上显示信息列表。`List`组件必须包含一个或多个`ListItem`组件。

`ListItem`组件提供了造型道具，如`selected`、`icon`、`header`等。，使项目看起来更具交互性和动态性。你也可以使用`itemDivider`道具来分割列表项。

在引擎盖下，`List`组件建立在 React Native 的`FlatList`组件之上，所以任何`FlatList`道具都是有效的`List`组件道具。

```
import React, { Component } from "react";
import { Container, Header, Content, List, ListItem, Text } from "native-base";
class App extends Component {
  render() {
    return (
      <Container>
        <Header />
        <Content>
          <List>
            <ListItem>
              <Text>Victor Wayne</Text>
            </ListItem>
            <ListItem>
              <Text>John Doe</Text>
            </ListItem>
            <ListItem>
              <Text>Jane Doe</Text>
            </ListItem>
          </List>
        </Content>
      </Container>
    );
  }
}

export default App;

```

该列表也可以使用网络调用从外部 API 动态生成。在下面的例子中，用户列表是由组件状态组成的，组件状态是从外部 JSON API 获取的。

下面你可以看到`Thumbnail`组件是用来显示用户头像的。`Thumbnail`组件的工作方式与 React Native 的`Image`组件类似，只是它是圆形的，通常用于显示小图像，如头像。

```
import React, { Component } from "react";
import {
  Container,
  Header,
  Content,
  List,
  ListItem,
  Text,
  Left,
  Body,
  Right,
  Thumbnail,
} from "native-base";

class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      users: [],
      page: 1,
    };
  }

  fetchUsers = async () => {
    try {
      const res = await fetch(`${JSON_API_URL}&page=${this.state.page}`);
      const { results: users } = await res.json();
      if (this.state.page === 1) this.setState({ users });
      else this.setState({ users: [...this.state.users, ...users] });
    } catch (err) {
      console.log(err);
    }
  };

  componentDidMount() {
    this.fetchUsers();
  }

  render() {
    return (
      <Container>
        <Header />
        <Content>
          <List>
            {this.state.users.map((user) => (
              <ListItem avatar key={user.id}>
                <Left>
                  <Thumbnail source={{ uri: user.image_url }} />
                </Left>
                <Body>
                  <Text>{user.name}</Text>
                </Body>
              </ListItem>
            ))}
          </List>
        </Content>
      </Container>
    );
  }
}

```

## 结论

NativeBase 是一个优秀的库，可以帮助您快速启动开发过程。使用 NativeBase 组件，您不必从头开始创建按钮、容器和列表等基本组件。

最终，NativeBase 可以节省您大量的时间和精力，尤其是在处理更广泛的应用程序时，使您能够更有效地处理业务逻辑，而不必太担心 UI 设计。

要了解更多信息，请参见 [NativeBase 文档](https://docs.nativebase.io/Components.html)。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)