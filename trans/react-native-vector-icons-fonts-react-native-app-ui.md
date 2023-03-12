# React-Native-vector-React 本地应用程序的图标- LogRocket 博客

> 原文：<https://blog.logrocket.com/react-native-vector-icons-fonts-react-native-app-ui/>

***编者按:**这篇文章于 2022 年 5 月 3 日更新，以反映关于 react-native-vector-icons 的最新信息，包括对 iOS 中有时出现的自动链接错误的快速修复。*

当谈到用户界面/UX 时，字体和图标很重要。图标为用户导航你的应用提供视觉帮助，你选择的字体(和字体颜色)为你的应用或品牌定下基调。

现实一点:你不太可能从一家在合同中使用卡通字体的公司购买人寿保险，比如合金墨水或 T2 纯素食风格。我无法想象一个没有图标的应用程序。说真的，如果你在 App Store 中知道一个，给我一个带链接的评论——我很好奇！

在本 React Native 教程中，我们将向您展示如何:

与该演示相关的完整源代码可在 [GitHub](https://github.com/claysimps/FontAndIconExample) 上获得。

*向前跳转:*

## 创建 React 本机项目

要初始化 React 本地项目，请将以下内容粘贴到您的终端中:

```
npx react-native init yourAppNameHere --template react-native-template-typescript && cd yourAppNameHere

```

这将使用 TypeScript 模板创建一个新的 React 本机项目。导航到项目文件夹。

下一步是为每个操作系统构建我们的应用程序。一、iOS:

```
yarn run ios 

```

现在，对于 Android:

```
yarn run android

```

## 链接和取消链接 React 本机资源

作为软件开发人员，任何改进我们工作流程的东西都是受欢迎的变化。 [React Native Asset](https://www.npmjs.com/package/react-native-asset) 使得链接和解除链接比使用`react-native link`更简单。

首先，全局安装`react-native-asset`:

```
yarn global add react-native-asset

```

在您的项目文件夹中，创建一个名为`assets`的新文件夹，然后在其中创建另一个名为`fonts`的文件夹。或者您可以使用终端:

```
mkdir -p assets/fonts

```

前往[谷歌字体](https://fonts.google.com/)下载字体家族。对于这个例子，我们将使用[的 Nunito](https://fonts.google.com/specimen/Nunito?query=nunito&preview.text_type=custom#standard-styles) ，但是你可以自由选择不同的字体。

下载完文件后，将其解压缩，并将选择的字体粗细添加到 fonts 文件夹中:

![Nunito In Google Fonts](img/391385df05cbe358f8306d7bd407e01c.png)

现在，在您的项目文件夹中创建一个名为`react-native.config.js`的新文件:

```
touch react-native.config.js

```

然后，添加以下内容:

```
module.exports = {
  assets: ['./assets/fonts'],
};

```

最后，使用 React Native Asset 链接字体文件。在您的终端中键入以下内容:

```
react-native-asset

```

如果您需要删除和取消链接字体，只需从`fonts`文件夹中删除它，然后再次运行`react-native-asset`。就这么简单。

## 在 React Native 中创建文本组件

让我们创建一个自定义文本组件来使用我们的新字体。创建一个名为`components`的文件夹，其中包含一个名为`Text.tsx`的文件，然后添加以下代码:

```
import React from 'react';
import {
  Text as ReactText,
  StyleSheet,
  StyleProp,
  TextStyle,
} from 'react-native';

type TextProps = {
  children: React.ReactNode;
  style?: StyleProp<TextStyle>;
};

export const Text = ({style, children}: TextProps) => {
  return <ReactText style={[styles.font, style]}>{children}</ReactText>;
};

const styles = StyleSheet.create({
  font: {
    fontFamily: 'Nunito-Regular',
  },
});

```

上面的代码创建了一个具有`Nunito`的`font-family`和一个常规的`font-weight`的`Text`组件，我们可以在我们的应用程序中使用它。我们从 React Native 获取孩子和风格类型(`TextProps`)。

现在我们有了我们的`Text`组件，让我们使用它吧！

用以下内容替换您的`App.tsx`文件:

```
import React from 'react';
import {SafeAreaView, StyleSheet, StatusBar} from 'react-native';
import {Text} from './components/Text';

const App = () => {
  return (
    <>
      <StatusBar barStyle="dark-content" />
      <SafeAreaView style={styles.container}>
        <Text>This font-weight is 'regular' </Text>
        <Text style={styles.boldFont}>This font-weight is 'bold' </Text>
      </SafeAreaView>
    </>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  boldFont: {
    fontFamily: 'Nunito-Bold',
  },
});

export default App;

```

注意，我们有两个不同的`font-weight`。第 11 行的`Text`组件有一个加粗的`font-weight`。为此，使用`StyleSheet.create`(第 23 行)用粗体覆盖`font-weight`设置，然后将值传递给`Text`组件中的 style prop。

![React Native Font-Weight Example](img/cdcdaced1771a87e2a7d94e17e12e640.png)

## 反应原生矢量图标

拥有超过 3000 个[免费图标](https://oblador.github.io/react-native-vector-icons/)和 15.7k GitHub stars， [React Native Vector Icons](https://github.com/oblador/react-native-vector-icons) 是满足你所有图标需求的绝佳选择。许多流行的 UI 库，比如 Magnus UI、React Native Paper 和 React Native Elements，都使用 React Native Vector 图标，所以你有很好的伙伴！如果你没有找到你想要的，你甚至可以自己制作图标。

要使用这个软件包，您需要安装它。走向你的终端，输入:

```
yarn add react-native-vector-icons && yarn add -D @types/react-native-vector-icons

```

### 为 iOS 安装 react-native-vector-icons

在 iOS 中安装 React Native Vecor 图标库和 dev 依赖项，如下所示:

```
cd ios && pod install && cd .. && yarn run ios

```

接下来，导航到您的 iOS 文件夹，安装您项目的`CocoaPods`，然后导航回您的项目文件夹。

### iOS 自动链接或更新错误故障排除

如果你在 iOS 上安装 React 本地矢量图标时遇到自动链接或更新错误的问题，这里有一个简单的小技巧可以解决这个问题。运行:

```
yarn react-native link react-native-vector-icons && yarn react-native unlink react-native-vector-icons && yarn ios

```

### 为 Android 安装 react-native-vector 图标

通过打开`android/app/build.gradle`(不是`android/build.gradle`)并添加以下导入语句，在 Android 上安装 React Native Vecor 图标库和开发依赖项:

```
import org.apache.tools.ant.taskdefs.condition.Os

```

![Installing React Native Vector Icons Android](img/21d84b43525a0ab0f4a9bec29d0950ec.png)

然后，在终端中运行以下命令:

```
yarn run android

```

## 在 React Native 中构建图标组件

现在我们要构建一个可重用的图标组件。

在您的`components`文件夹中，创建一个名为`Icon.tsx`的新文件:

```
cd components && touch Icon.tsx && cd ..

```

然后，添加以下代码:

```
import React from 'react';
import MIcon from 'react-native-vector-icons/MaterialCommunityIcons';

MIcon.loadFont();

type IconSizeProps = {
  iconSizes: keyof typeof IconSizes;
};

export interface IconProps {
  size: IconSizeProps['iconSizes'];
  name: string;
  color: string;
}

export const IconSizes = {
  small: 13,
  medium: 18,
  large: 23,
  extraLarge: 27,
};

export const MaterialIcon = ({size, name, color}: IconProps) => (
  <MIcon name={name} size={IconSizes[size]} color={color} />
);

```

对于您想要使用的每个图标捆绑包，像我们在第 2 行(导入材料社区图标捆绑包集)和第 4 行(加载图标捆绑包)中一样导入并加载它。

以这种方式加载图标消除了添加任何本地代码来使用它们的需要。
我们可以用三个道具来导出组件，这三个道具允许我们控制图标的颜色、大小和类型:

*   `size`由`IconSizes`预定义
*   `name`定义图标类型
*   `color`定义图标颜色

如果您是 TypeScript 新手，并且您想知道第 7 行和第 11 行发生了什么，将鼠标悬停在`iconSizes`上，您会看到:

![React Native TypeScript IconSizes Example](img/2a79b9cf9df15fd841efd2218e5aac0b.png)

### `keyof`

在 TypeScript 中，可以从 JavaScript 值创建类型。`[[keyof]]([https://www.typescriptlang.org/docs/handbook/2/keyof-types.html](https://www.typescriptlang.org/docs/handbook/2/keyof-types.html))` [操作符](https://www.typescriptlang.org/docs/handbook/2/keyof-types.html)接受一个对象类型(第 16 行的`IconSizes`)并返回其键的字符串或文字联合:

```
iconSizes: string | number | symbol
```

### `typeof`

TypeScript 版本的`[typeof]([https://www.typescriptlang.org/docs/handbook/2/typeof-types.html](https://www.typescriptlang.org/docs/handbook/2/typeof-types.html))`返回对象值的类型:

```
 iconSizes: {
    small: number;
    medium: number;
    large: number;
    extraLarge: number;
}
```

### `keyof typeof`

keyof typeof 对对象的键进行索引，并返回文本类型的并集:

```
iconSizes: "small" | "medium" | "large" | "extraLarge"
```

现在，由于 TypeScript 的强大功能，每次使用 size prop 时，IDE 都会知道应该提供什么值，并为您显示选项。要在 Mac 上访问 VS 代码中的自动完成功能，请同时按下 **ctrl** 和**空格键**。

![Enabling Autocomplete VS Code](img/4ac52430f5079ae8bc2f5eaaa996c8a4.png)

让我们回到`App.tsx`来测试我们新的`Icon`组件。

```
import React from 'react';
import {SafeAreaView, StyleSheet, StatusBar} from 'react-native';
import {Text} from './components/Text';
// Import our icon component
import {MaterialIcon} from './components/Icon';

const App = () => {
  return (
    <>
      <StatusBar barStyle="dark-content" />
      <SafeAreaView style={styles.container}>
        <Text>This font-weight is 'regular' </Text>
        <Text style={styles.boldFont}>This font-weight is 'bold' </Text>
        {/* Add icons here */}
        <MaterialIcon size="large" color="purple" name="home" />
        <MaterialIcon size="extraLarge" color="black" name="github" />
      </SafeAreaView>
    </>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  boldFont: {
    fontFamily: 'Nunito-Bold',
  },
});

export default App;

```

出于演示的目的，我在文本下面添加了一个 home 图标和 GitHub 徽标，使用了不同的大小和颜色。

![React Native Example App With Icons](img/8b41af54d4c508092bb6f653a50b72fb.png)

## 在 React Native 中创建图标按钮组件

按钮和图标一起看起来很棒，所以让我们做一个。对于我们的按钮组件，我们将使用我们之前用新的图标包构建的自定义字体。

在 components 文件夹中，创建一个名为`IconButton.tsx`的新文件:

```
cd components && touch IconButton.tsx && cd ..

```

添加以下代码:

```
import React from 'react';
import FontAwesomeIcon from 'react-native-vector-icons/FontAwesome';
import {Text} from './Text';
import {IconSizes, IconProps} from './Icon';

FontAwesomeIcon.loadFont();

type IconButtonProps = IconProps & {
  text: string;
  onPress: () => void;
};

export const IconButton = ({
  onPress,
  size,
  name,
  color,
  text,
}: IconButtonProps) => (
  <FontAwesomeIcon.Button
    onPress={onPress}
    name={name}
    size={IconSizes[size]}
    color={color}>
    <Text>{text}</Text>
  </FontAwesomeIcon.Button>
);

```

如果我们想使用来自 [Material-UI 社区图标](https://www.npmjs.com/package/material-ui-community-icons)的图标，我们可以导入并使用我们已经构建的图标组件。但是这一次，我们将使用[字体 Awesome 的图标包集合](https://fontawesome.com/icons?d=gallery)，所以我们需要加载新的图标包(第 6 行)。

现在，让我们将我们的`IconButton`导入到`App.tsx`中:

```
import React from 'react';
import {SafeAreaView, StyleSheet, StatusBar} from 'react-native';
import {Text} from './components/Text';
// Import our icon component
import {MaterialIcon} from './components/Icon';
// Import our icon button component
import { IconButton } from './components/IconButton';

const App = () => {
  return (
    <>
      <StatusBar barStyle="dark-content" />
      <SafeAreaView style={styles.container}>
        <Text>This font-weight is 'regular' </Text>
        <Text style={styles.boldFont}>This font-weight is 'bold' </Text>
        {/* Add icons here */}
        <MaterialIcon size="large" color="purple" name="home" />
        <MaterialIcon size="extraLarge" color="black" name="github" />
        {/* Add icon button here */}
          <IconButton
          onPress={() => {}}
          color="white"
          size="extraLarge"
          name="facebook"
          text="Login in with Facebook"
        />
      </SafeAreaView>
    </>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  boldFont: {
    fontFamily: 'Nunito-Bold',
  },
});

export default App;

```

与我们之前构建的`MaterialIcon`组件一样，您可以使用提供的道具设置颜色、大小和图标类型。

![React Native Icon Component Example](img/ea19de5f1d7d1df7d2906b73f5dcf60c.png)

## 结论

在本文中，我们演示了如何在 iOS 或 Android 应用程序中添加图标和字体。我们还回顾了如何使用 react-native-vector-icons 库，并分享了如何排除 iOS 自动链接错误。

在应用程序的设计阶段，仔细阅读图标是值得的；图标捆绑包中有一些真正的宝石。如果你正在寻找一些具体的东西，你应该尝试许多相似的，描述性的关键字。例如，搜索“设置”将返回一个齿轮图标，但是如果您想要一个具有多个齿轮的图标，请尝试搜索“齿轮”

![React Native Icon Search](img/45288d252e5e2b82aaad810029ed1cf1.png)

![React Native Icon search.](img/d023c3844aace4d8de7b9476ac7e37d6.png)

既然你已经知道如何定制你的字体，不要害怕用一个漂亮的字体对来扩展你的文本组件——例如，标题用 [Nunito](https://fonts.google.com/specimen/Nunito?preview.text_type=custom) ,段落用 [Roboto](https://fonts.google.com/specimen/Roboto?preview.text_type=custom) :

![React Native Example App With Nunito And Roboto Fonts](img/12043f7d9054b3bd9f1af23040902d80.png)

你有它！你可以自定义你的字体和添加图标到你的心的内容。现在向前迈进，创造一些令人惊奇的东西——如果你在卖人寿保险，就不要用纯素食的方式！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)