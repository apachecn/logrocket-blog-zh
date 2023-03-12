# 用 Storybook - LogRocket 博客构建一个 React 原生组件库

> 原文：<https://blog.logrocket.com/build-a-react-native-component-library-with-storybook/>

构建组件 UI 库是在组织或代码库中实施设计标准的一个很好的方法。然而，它可能会带来严重的技术障碍，尤其是在大型库中，因为它很容易以复杂和难以阅读的代码库而告终。

幸运的是，已经开发了各种工具来简化组件库的构建过程。在本文中，我们将了解一些工具，它们使在 React Native 中构建组件库变得更加容易，主要是 Storybook 和 Shopify Restyle。

React Native 使得使用 React 和 Javascript 创建跨平台的强大移动应用程序变得前所未有的简单。凭借大量的社区支持，React Native 还拥有一个健康的插件和工具生态系统，使软件开发人员更容易编写可扩展的移动应用程序。

首先，我们将看看 Storybook 以及它可以为软件开发人员做什么，然后使用 Storybook 在 React Native 中创建一个非常基本的组件库和一个名为 Shopify Restyle 的伟大 UI 库。在本指南结束时，您将已经创建并记录了按钮和文本组件。

## 什么是童话书？

为了理解 Storybook 是什么，我们首先要了解组件库实际上是什么。

组件库是一组预构建的 UI 组件，整个组织可以在多个项目或屏幕中重用这些组件。组件库节省了产品团队的时间，并使单元测试和单独查看应用程序的各个部分变得更加容易。

Storybook 是一个开源工具，用于构建和记录独立的 UI 组件。它使得创建和测试 UI 组件的过程更加简单。

![Visual depiction of Storybook process](img/fb538da403b55c2a1cb43851a7cfc128.png)

例如，在创建登录页面时，您可能会有几个基本组件，如按钮、输入和文本。这些组件中的每一个都可以被制作成一个可重用的组件，然后您可以使用 Storybook 对其进行文档化。

![Gif of Storybook in use](img/f6e3ac8da618c5a9b2a6a4ce7025765f.png)

Google 和 Atlassian 等公司的软件团队使用 Storybook 来记录整个组织的组件库，以创建可重用和可共享的代码库。

## 反应本土和商店化风格

Shopify Restyle 是 React Native 的开源样式解决方案。

来自[重新设计文档](https://github.com/Shopify/restyle#shopifyrestyle):

> Restyle 库提供了一个类型强制系统，用于在 React Native 和 TypeScript 中构建 UI 组件。这是一个用于构建 UI 库的库，以主题能力为核心焦点。

使用 Restyle，很容易构建围绕一个中心主题构建的组件。这个主题与组件样式和道具相关联，允许你从道具中获取场景间隔或颜色等信息。

例如，要将一个按钮组件设计成某种颜色，Restyle 使构建按钮变得很容易，因此您可以使用`<Button color="primary400" />`而不是手动键入一个十六进制值。

Restyle 的核心是一个`theme`对象，您定义它来包含所有组件的颜色、大小、间距和默认样式。它还提供了两个预构建的组件——`Box`和`Text`，使得构建更大的组件变得非常容易。

## 设置项目

让我们深入实际构建 React Native 中的组件库。为了简单起见，我们将在本教程中使用 Expo 和 Typescript 来利用 Restyle 提供的强大类型。我已经创建了一个回购协议，它包含了这个项目的后续提交，你可以在这里查看。

首先，让我们在当前目录中初始化一个新的 Expo 项目:

```
npm i -g expo-cli && expo init -t expo-template-blank-typescript

```

这将提示您输入应用程序名称，然后为我们的新应用程序创建所有文件和 install base 依赖项。

然后，我们将安装 Storybook 和 Restyle 的依赖项:

```
# Setup Storybook for React Native
npx -p @storybook/cli sb init --type react_native

# Install storybook loader to autoload stories
yarn add -D react-native-storybook-loader 

```

接下来，将以下内容添加到您的`package.json`中，以运行故事书加载器:

```
{
  "scripts": {
    "prestorybook": "rnstl"
  }
}

```

最后，更新您的应用入口点(`App.tsx`)以使用 Storybook:

```
// App.tsx
import React from 'react';

export { default } from './storybook';

```

现在，用`yarn storybook`启动故事书服务器，用`yarn ios`启动开发服务器(或者如果你运行的是 android 模拟器，用`yarn android`)。

\ ![Storybook welcome screen with blank mobile app](img/a65ed5b501ff2bcbd99e234acdf39697.png)

您应该会看到上面的屏幕。如果没有，关闭所有正在运行的进程，然后再次启动`storybook`和 dev 服务器。

恭喜你。您现在已经有了一个用 Storybook 设置的 Expo 项目！

让我们看看我们的文件。如果您查看根目录，您会看到一个名为`storybook`的新文件夹，其中有一个名为`stories`的子文件夹。这是配置故事书和故事的地方。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

你可以在上面的故事书网页中看到，我们有两个故事，`Button`故事有两个变体，`with text`和`with some emoji`，然后是`Welcome`故事有变体`to Storybook`。

现在让我们开始使用 Restyle 构建我们自己的组件库吧！在接下来的部分中，我们将设置 Restyle 主题，然后使用`Box`和`Text`组件构建一个新的`Button`组件。

## 配置重新样式

一、安装 Restyle: `yarn add @shopify/restyle`。

接下来，我们将为我们的应用程序设置一个核心主题。

创建一个新文件，`src/theme.ts`并将以下内容放入其中:

```
// src/theme.ts

import { createTheme } from '@shopify/restyle'

const palette = {
  purpleLight: '#8C6FF7',
  purplePrimary: '#5A31F4',
  purpleDark: '#3F22AB',

  greenLight: '#56DCBA',
  greenPrimary: '#0ECD9D',
  greenDark: '#0A906E',

  black: '#0B0B0B',
  white: '#F0F2F3',
};

const theme = createTheme({
  colors: {
    primary: palette.purplePrimary,
    ...palette
  },
  spacing: {
    s: 8,
    m: 16,
    l: 24,
    xl: 40,
  },
  breakpoints: {
    phone: 0,
    tablet: 768,
  },
});

export type Theme = typeof theme;
export default theme;

```

然后，通过更新`App.tsx`将您的应用程序包装在 Restyle 主题提供程序中:

```
// App.tsx

import React from 'react';
import {ThemeProvider} from '@shopify/restyle';

import Storybook from './storybook';
import theme from './src/theme';

const App = () => (
  <ThemeProvider theme={theme}>
    <Storybook/>
  </ThemeProvider>
);

export default App;

```

如果你重新启动你的应用程序，你可能会注意到没有变化，但我们已经成功地配置我们的应用程序使用 Restyle！现在让我们创建我们的`Box`、`Text`和`Button`组件。

让我们使用 Bash 创建我们的`Components`文件夹:

```
mkdir -p ./src/Components

```

首先，让我们在`src/Components/Box.tsx`中创建我们的`Box`组件:

```
// Box.tsx
import {createBox} from '@shopify/restyle';
import {Theme} from '../theme';

const Box = createBox<Theme>();

export default Box;

```

然后，`src/Components/Text.tsx`中我们的`Text`组件:

```
// Text.tsx
import {createText} from '@shopify/restyle';
import {Theme} from '../theme';

const Text = createText<Theme>();

export default Text;

```

最后，我们将在`src/Components/Button.tsx`中创建我们的`Button`组件:

```
// Button.tsx
import React from "react";
import { BoxProps, TextProps } from "@shopify/restyle";
import { ActivityIndicator, TouchableHighlight } from "react-native";
import { Theme } from "../theme";
import Box from "./Box";
import Text from "./Text";

type ButtonProps = {
  onPress: () => void;
  loading?: boolean;
  textProps?: TextProps<Theme>;
} & Partial<BoxProps<Theme>>;

const Button: React.FC<ButtonProps> = ({
  children,
  onPress,
  loading,
  textProps,
  ...props
}) => (
  <TouchableHighlight underlayColor="transparent" onPress={onPress}>
    <Box
      py="m"
      px="xl"
      backgroundColor="primary"
      borderRadius={8}
      shadowOffset={{ height: 2, width: 0 }}
      shadowRadius={5}
      shadowColor="black"
      shadowOpacity={0.2}
      {...props}
    >
      {loading ? (
        <ActivityIndicator color="white" />
      ) : (
        <Text color="white" {...textProps}>
          {children}
        </Text>
      )}
    </Box>
  </TouchableHighlight>
);

export default Button;

```

现在，让我们指出我们正在用`Box`和`Text`组件做的几件了不起的事情。注意我们如何在`Box`组件中使用像`py`和`backgroundColor`这样的道具。我们正在传递我们在主题中定义的值，比如用于间距的`m`或用于按钮颜色的`primary`，这非常酷！

现在我们已经构建了这些组件，让我们在`src/Components/index.ts`中导出所有这些组件:

```
// index.ts
export { default as Button } from './Button';
export { default as Text } from './Text';

```

现在，让我们更新我们的故事，这样我们就可以在故事书里看到这些了。

首先，我们将删除`storybook/stories/Welcome`和`storybook/stories/Button`来去掉这个我们不需要的故事，并将`storybook/stories/index.js`更新为`*import*` `'./Button.stories';`。

创建`storybook/stories/Button.stories.js`:

```
// Button.stories.js

import React from 'react';
import { action } from '@storybook/addon-actions';
import { text } from '@storybook/addon-knobs';
import { storiesOf } from '@storybook/react-native';
import { Button, Text } from '../../src/Components';
import CenterView from './CenterView';

storiesOf('Button', module)
  .addDecorator((getStory) => <CenterView>{getStory()}</CenterView>)
  .add('with text', () => (
    <Button onPress={action('clicked-text')}>
      <Text>{text('Button text', 'Hello Button')}</Text>
    </Button>
  ))
  .add('with some emoji', () => (
    <Button onPress={action('clicked-emoji')}>
      <Text>😀 😎 👍 💯</Text>
    </Button>
  ))
  .add('loading', () => (
    <Button onPress={action('loading')} loading>
      <Text>Test</Text>
    </Button>
  ));

```

现在，如果你看我们的故事书，你应该会看到一些漂亮的按钮！

![Example mobile app with Storybook and a button with emojis](img/74d714eb79ff9b871302f0f4b0b54904.png)

## 结论

现在，您有了一个用 Expo、Storybook 和 Restyle 构建的基本组件库！如果你想了解更多关于 Restyle 的知识，我推荐你阅读 [Restyle 文档](https://github.com/Shopify/restyle)。你也可以在这里找到完成的代码库。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)