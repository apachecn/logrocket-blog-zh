# 利用原生特性的最佳反应原生库

> 原文：<https://blog.logrocket.com/the-best-react-native-libraries-for-leveraging-native-features/>

React Native 社区是网络上最活跃的开源社区之一。[根据 GitHub](https://octoverse.github.com/2019/#top-and-trending-projects) 的报道，React Native repository 是贡献者数量最高的 repos 之一。您可以找到几乎任何您想在下一个移动应用程序中实现的模块和第三方库。

您肯定会遇到与废弃的和不可维护的包相关的问题，尤其是 React Native 早期的包——这是生活的现实。我认为这是一个可以接受的负担，因为维护一个 React 原生库需要维护一个至少依赖于三种语言的项目。

## React 原生 UI 库每个移动开发者都应该知道

了解使用哪些库来创建最佳的用户和开发人员体验是至关重要的。在本教程中，我们将向您介绍 15 个 React 原生 UI 库，这些库我在 2021 年迄今为止所开发的几乎所有 React 原生应用程序中都使用过。

我们将介绍最好的 React 本机 UI 库，用于:

然后，我们将回顾一些使用这些和其他 React 原生 UI 库的[技巧和最佳实践](#tips)，包括如何:

![React Native UI Lets Go Gif](img/4f0ab83de217a108f8629baefa03daa8.png)

### 式样

在 React 社区和 JavaScript 社区中，样式是一个非常固执己见的话题。我几乎使用了所有的 CSS-in-JS 方法来设计我的 React 和 React 本地应用程序。我已经决定在 web 上使用 [Tailwind CSS](https://blog.logrocket.com/tailwind-css-is-it-tomorrows-bootstrap-ebe560f9d00b/) ，在 React Native 上使用 Restyle。

Restyle 是由 Shopify 开发的一个库，带有一个用于构建 UI 组件的强制类型系统。使用 Restyle，您可以通过定义颜色、间距和变化，并基于 React 本机核心组件和 Restyle 实用程序创建组件，来创建一个完整的设计系统。

Restyle 最好的特性是，它强迫你只使用你已经在主题中声明的配置，这保持了应用程序的整洁，并通过尽可能减少他们的选择来指导你的同事。

重新设计要求您首先创建一个反映您的设计系统配置(颜色、间距、断点和变量)的主题，如下所示:

```
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
    mainBackground: palette.white,
    cardPrimaryBackground: palette.purplePrimary,
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

这是使用 Restyle 时组件的外观:

![React Component View Restyle](img/c029466af4ed24c96934fd294b3afe5a.png)

### 航行

如果你两年前问我，我会警告不要使用 [React Navigation](https://reactnavigation.org/) ，因为存在一些性能挑战，并建议使用一个原生库，如 [React Native Navigation](https://github.com/wix/react-native-navigation) 。

幸运的是，React 导航社区在[软件大厦](https://swmansion.com/)的帮助下，开发并优化了 React 原生生态系统中最常用的三个库: [React 原生屏幕](https://github.com/software-mansion/react-native-screens)、 [React 原生手势处理程序](https://github.com/software-mansion/react-native-gesture-handler)和 [React 原生复活](https://github.com/software-mansion/react-native-reanimated)。这三个库是从版本 4 开始你会注意到的巨大性能改进背后的秘密。

今天，React 导航应该是你的首选；你不需要三思。

要开始使用 React 导航，您必须首先安装它的所有依赖项:

```
yarn add react-native-reanimated react-native-gesture-handler react-native-screens react-native-safe-area-context @react-native-community/masked-view

```

React 导航可以使用三种常见类型的导航器:`Stack`、`Tab`和`Drawer`。您还可以组合多个导航器，创建一个复杂的应用程序架构。

下面是堆栈导航器的一个简单示例:

```
   import { createStackNavigator } from '@react-navigation/stack';

const Stack = createStackNavigator();

function MyStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Home" component={Home} />
      <Stack.Screen name="Notifications" component={Notifications} />
      <Stack.Screen name="Profile" component={Profile} />
      <Stack.Screen name="Settings" component={Settings} />
    </Stack.Navigator>
  );
}

```

### 闪屏

向移动应用程序添加闪屏是一项单调乏味的任务，完成单调乏味任务的最佳方式是将其自动化。`[react-native-bootsplash](https://github.com/zoontek/react-native-bootsplash)`使您能够使用 CLI 创建精美的闪屏。所有你需要做的是提供一个图像和背景颜色，该软件包将为你做的工作。

我更喜欢使用`react-native-bootsplash`而不是最流行的软件包`[react-native-splash-screen](https://blog.logrocket.com/building-a-splash-screen-in-react-native/)`，因为前者可以防止你在启动时遇到问题时看到红色错误，而是停留在启动屏幕上，这很烦人。

以下命令在`react-native-bootsplash`中生成一个闪屏:

```
yarn react-native generate-bootsplash assets/bootsplash_logo_original.png \
  --background-color=F5FCFF \
  --logo-width=100 \
  --assets-path=assets \
  --flavor=main

```

![Splash Screen React Native Bootsplash Display](img/ab8545163d1987f02eb621ced68b04ec.png)

### 应用程序图标

这是我们需要自动化的另一项繁琐的工作。为了自动生成应用图标，我们将使用一个名为 [React Native Make](https://github.com/bamlab/react-native-make) 的插件。该插件可通过 React Native CLI 获得，旨在帮助您为 iOS 和 Android 平台生成应用程序图标。

要使用`react-native-make`，您只需要一个 1024×1024 版本的应用程序图标，并准备好使用以下命令生成应用程序图标资源:

```
react-native set-icon --path path-to-image

```

### 加载占位符

我收到的关于 React 原生移动应用的最常见问题之一是，如何创建类似于脸书和 YouTube 等应用的加载体验。

创建这样的体验有多种解决方案，但我推荐使用`[react-content-loader](https://github.com/danilowoz/react-content-loader)`包。这个包是基于`[react-native-svg](https://github.com/react-native-svg/react-native-svg)`和[复活的](https://github.com/software-mansion/react-native-reanimated)库，看起来工作非常流畅。这个包还提供了一个运动场来帮助你在浏览器中创建一个占位符。

下面是一个加载占位符的示例，类似于脸书应用程序中的占位符:

```
import React from "react"
import ContentLoader, { Rect, Circle, Path } from "react-content-loader/native"

const MyLoader = (props) => (
  <ContentLoader 
    speed={2}
    width={400}
    height={460}
    viewBox="0 0 400 460"
    backgroundColor="#d1d1d1"
    foregroundColor="#c4c4c4"
    {...props}
  >
    <Circle cx="31" cy="31" r="15" /> 
    <Rect x="58" y="18" rx="2" ry="2" width="140" height="10" /> 
    <Rect x="58" y="34" rx="2" ry="2" width="140" height="10" /> 
    <Rect x="0" y="60" rx="2" ry="2" width="400" height="400" />
  </ContentLoader>
)

export default MyLoader

```

![Loading Placeholder Example](img/138b93c0817a22aa7e5867886cc77459.png)

### 处理和跟踪错误

我们的目标都是通过使用打字系统和增加测试覆盖率来使我们的 React 本机应用程序无 bug。然而，即使有很高的测试覆盖率，用户仍然肯定会遇到并报告错误。

因此，当应用程序没有按预期运行时，处理您的错误并向用户提供反馈是至关重要的。`[react-native-exception-handler](https://github.com/a7ul/react-native-exception-handler)`提供了一种简单的方法来处理本机和 JavaScript 错误，并向用户提供反馈。

要使`react-native-exception-handler`工作，你必须安装并连接模块。接下来，注册 JavaScript 和本机异常的全局处理程序，如下所示:

```
import { setJSExceptionHandler, setNativeExceptionHandler } from "react-native-exception-handler";

setJSExceptionHandler((error, isFatal) => {
  // This is your custom global error handler
  // You do stuff like show an error dialog
  // or hit google analytics to track crashes
  // or hit a custom api to inform the dev team.
});

const exceptionhandler = (exceptionString) => {
  // your exception handler code here
};
setNativeExceptionHandler(
  exceptionhandler,
  forceAppQuit,
  executeDefaultHandler
);

```

您应该使用第三方工具来跟踪这些错误，该工具会通知您用户在您的应用程序中遇到的错误，以便您在未来的版本中修复它们。

### 获取数据

我在这一节中的建议将取决于您的后端实现。如果您使用 REST API， [react-query](https://github.com/tannerlinsley/react-query) 是您的最佳选择。但是相反，如果你正在使用 graphQL，你可以使用 [urql](https://formidable.com/open-source/urql/) 。这两个库将为您提供在应用程序中处理 API 所需的一切。您将受益于大量独特的功能，如缓存、离线支持、乐观 UI、预取等等。

安装 react-query 后，您可以创建自己的挂钩来获取任务，例如:

```
import {useQuery} from 'react-query';
import {client} from './client';

const getTasks = async () => {
  const {data} = await client.get('/tasks');
  return data;
};

export function useTasks() {
  return useQuery('tasks', getTasks);
}
```
And use it inside your components to fetch tasks : 

```
export const Tasks = () => {
  const {isLoading, data} = useTasks();

  if (isLoading) {
    return <ActivityIndicator color="#000" />;
  }
  return (
    <FlatList
      ListHeaderComponent={() => <Header />}
      data={data || []}
      renderItem={({item}) => <TaskItem {...item} />}
      keyExtractor={(_, index) => `item-${index}`}
      showsHorizontalScrollIndicator={false}
    />
  );
};

```

### 核标准情报中心

我看到的大多数关于在 React Native 中使用图标的文章都建议使用 [React Native 矢量图标](https://github.com/oblador/react-native-vector-icons)作为默认选择。诚然，这个库有许多图标和字体，但更多的时候，我的团队发现自己创造新的字体，以配合我们的图标定制设计。在我看来，用 [react-native-vector-icon](https://github.com/oblador/react-native-vector-icons) 创建一个定制字体是次优的体验，因为每当你想添加一个新图标时，你都需要生成一个新字体。

相反，我的团队开始用令人惊奇的 [SVGR](https://react-svgr.com/playground/) 包将`[react-native-svg](https://github.com/react-native-svg/react-native-svg)`用于我们的图标，它可以从任何 SVG 文件生成一个 React 组件。您甚至可以从 Figma 文件中直接导出 React 组件。

下面是一个使用 [SVGR Figma 插件](https://www.figma.com/community/plugin/749818562498396194/SVG-to-JSX)作为 React 本地组件生成的 SVG 图标的简单示例:

```
import * as React from "react"

function Icon(props) {
  return (
    <svg
      width={48}
      height={1}
      viewBox="0 0 48 1"

      {...props}
    >
      <title>{"Rectangle 5"}</title>
      <path d="M0 0h48v1H0z" fill="#063855" fillRule="evenodd" />
    </svg>
  )
}

export default Icon

```

### 形象

如果您的应用程序主要依赖于图像，您会注意到一些性能问题，尤其是列表和滚动视图。

使用 [FastImage](https://github.com/DylanVann/react-native-fast-image) ，一个高性能的 React 原生映像组件，将帮助您改进您的应用程序，而无需任何额外的努力。它完全取代了 React Native 的 image 组件，并添加了一些令人惊叹的功能，如缓存、优先级排序和重新加载。

### 形式

在 React Native 中有一些很棒的处理表单的解决方案，包括 [Formik](https://formik.org/) 和 [React Hook Form](https://react-hook-form.com/) 。

我以前用 Formik，但是自从我发现 React Hook Form 之后就迷上了它。在我看来，这是在 React 中处理简单和复杂表单的最佳解决方案。

使用 React 钩子形式的其他好处包括状态管理、验证、错误管理和多个数组字段。

### 测试

如果您已经使用测试库测试过您的前端应用程序，那么您的知识和经验将适用于 React Native 中的测试。 [React 原生测试库](https://github.com/callstack/react-native-testing-library)拥有几乎相同的 API。

下面是一个使用 React 本地测试库的简单计数器单元测试:

```
import "react-native";
import React from "react";
import { fireEvent, render } from "@testing-library/react-native";

it("renders correctly", () => {
  // render component
  const { getByText } = render(<Counter />);

  // get buttons and text elements
  const decrement = getByText(/decrement/i);
  const increment = getByText(/increment/i);
  const counterText = getByText(/Current count:/i);

  // Make sure you have the right values
  expect(counterText.props.children).toEqual(["Current count: ", 0]);
  // trigger an event
  fireEvent.press(increment);
  expect(counterText.props.children).toEqual(["Current count: ", 1]);
  fireEvent.press(decrement);
  expect(counterText.props.children).toEqual(["Current count: ", 0]);
});

```

为了自信地测试您的应用程序的行为，我建议使用 Wix 的 [Detox](https://github.com/wix/Detox) 库编写端到端测试。

## 使用 React 本机 UI 库的技巧和最佳实践

下面是一些额外的提示，可以帮助您充分利用上面详述的 React 原生库。

### 创建一个 React 本机模板

如果您希望在即将到来的 React 原生项目中使用所有这些库，那么创建一个简单的模板可能是值得的，这样您就可以轻松地开始一个新项目。

我们最近为我们的移动团队创建了一个模板，用所有上述的库开始新的项目。如果你想用的话，这就是:

```
npx react-native init MyApp --template https://github.com/obytes/react-native-template-obytes

```

### 获取关于新库的更新

了解新库和最佳实践的最有效方法之一是关注开源项目。我喜欢检查我的网络上共享的每个 React 原生项目的 package.json 文件。每当我找到一个新的图书馆，我都会搜索它来了解更多。

此外，请务必关注 GitHub 探索页面上的 [react-native 主题。您将收到大量 React 本地项目和讨论。](https://github.com/topics/react-native)

### 将 TypeScript 与 React Native 一起使用

大约一年半以前，我开始使用 TypeScript 作为 React 原生项目的主要语言，我希望我能更早这样做。使用 TypeScript 有助于提高代码质量和开发人员体验，因为它有助于防止键入时出现错误，并改进自动完成功能。

需要注意的是，TypeScript 只是一个 JavaScript 超集，具有可选的静态类型。它不需要你学习一门全新的语言。

## 结论

我希望你觉得这个 React Native 教程很有趣，内容丰富，而且有趣。我们讨论了使用 React 原生库在移动应用中利用原生特性的好处，推荐了特定的工具来帮助您进行样式设计、调试、数据获取等，并概述了一些技巧和最佳实践来帮助您充分利用这些 React 原生库。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)