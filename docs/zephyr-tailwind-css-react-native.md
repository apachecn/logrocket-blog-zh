# 介绍泽法:React Native 的顺风 CSS

> 原文：<https://blog.logrocket.com/zephyr-tailwind-css-react-native/>

用普通的 CSS 构建复杂的网络应用可能是一个令人生畏的挑战。因此，开发人员倾向于使用流行的样式库，如 Bootstrap 和 Tailwind CSS，以缩短开发周期。

与 web 上的样式类似，使用 React Native 中的内置样式功能可能是一个缓慢而费力的过程。有类似于 Bootstrap 和 Tailwind CSS 的解决方案可用于 React Native，其中一个这样的解决方案是 React Native 泽法。React Native 泽法是一个受 CSS 启发的风格库，旨在提高您的开发速度，让 React Native 应用程序运行得更快。

还带来了 Tailwind CSS 体验 React Native。如果你熟悉 Tailwind CSS，你可以很容易地找到泽法，因为两者有相似之处。本文将向您介绍 React Native 泽法的主要特性。

## 泽法本土的反应是什么？

React Native 泽法是一个受顺风 CSS 启发的 React 原生样式库。它在 React Native 的上下文中实现了 Tailwind CSS 的一些核心思想，没有任何原生依赖。

虽然在撰写本文时，React Native 泽法的一些关键特性仍在积极开发中，但以下是这些特性。

*   它提供了一套现成的内置样式实用程序
*   它带有一个可扩展的默认主题
*   它支持开箱即用的黑暗模式

上述特性使 React Native 泽法成为其他库中内置样式功能的更好替代方案。上面的功能绝不是详尽的，我建议查看官方文档以了解 React Native 泽法的更多内置功能。

React Native 具有与 web 不直接兼容的样式功能。因此，可能很难直接将 Bootstrap 和 Tailwind CSS 等专注于 web 的样式解决方案转移到 React Native。然而，由于两个库之间的相似性，您的一些顺风 CSS 技能集可以转移到 React Native 泽法。

如果您有一个现有的 React 本机项目，您可以从 npm 包注册表安装 React 本机泽法，如下所示:

```
# Using npm
npm i react-native-zephyr

# Using yarn
yarn add react-native-zephyr

# Using pnpm
pnpm add react-native-zephyr

```

## 如何使用 React Native 泽法

React Native 泽法使用`StyleProvider`组件来管理应用程序的配色方案等。要开始使用`react-native-zephyr`，请导入`StyleProvider`组件，并将您的应用程序包装在其中。

```
import { StyleProvider } from "react-native-zephyr";
export const App = () => (
  <StyleProvider>
    { /* The rest of your app goes here */}
  </StyleProvider>
);

```

React Native 泽法导出了用于生成核心样式实用程序的`createStyleBuilder`函数。调用`createStyleBuilder`将返回`styles`、`useStyles`和`makeStyledComponent`实用函数。这些造型实用程序函数提供了 React Native 泽法的不同造型方法。

为了便于维护，React Native 泽法建议创建一个专用的工具文件来管理您的造型工具。下面的代码显示了 React Native 泽法的基本样式实用程序文件。

```
import { createStyleBuilder } from "react-native-zephyr";
export const { styles, useStyles, makeStyledComponent } = createStyleBuilder();

```

`createStyleBuilder`函数也有一个可选参数，尽管我们在上面的例子中没有传递它，它可以用来覆盖或扩展默认主题。如果您像我们一样不带参数地调用`createStyleBuilder`，React Native 泽法将使用默认主题。在接下来的章节中，我们将学习如何覆盖和扩展默认主题。

您可以将需要的内容从该实用程序文件导出到应用程序的其他组件。如上例所示，核心的样式工具是`styles`和`makeStyledComponent`函数以及`useStyles`钩子。

## 如何使用 React Native 泽法中的造型方法

如前一节所述，React Native 泽法建议创建一个专用的`styles.js`实用程序文件来管理其实用程序函数。调用`createStyleBuilder`函数返回`styles`和`makeStyledComponent`函数以及`useStyles`钩子。您可以将这些实用函数从`styles.js`文件导出到应用程序中需要它们的组件。

```
import { createStyleBuilder } from "react-native-zephyr";
export const { styles, useStyles, makeStyledComponent } = createStyleBuilder();

```

`styles`函数接受 React 本机泽法类名的数组，并返回 React 本机样式对象。您作为参数传递的类名来自您的主题和处理程序。您可以像这样使用`styles`函数:

```
export default function App() {
  return (
    <View style={styles("flex:1", "bg:black")} >
      <Text style={styles("color:white", "text:5xl")}>
        Hello World
      </Text>
    </View>
  );
}

```

请注意，`styles`功能没有对黑暗模式的现成支持。所以只适合一次性的款式。

您还可以在 React Native 泽法中使用`makeStyledComponent`包装器来设计样式。顾名思义，`makeStyledComponent`是一个制作样式化组件的实用函数。`makeStyledComponent`实用函数将一个组件作为参数，并为该组件添加`class`和`darkClass`道具。它返回可以向其传递样式类的包装组件。

如上所述，最好在您的`styles.js`文件中创建样式化的组件，并像这样导出它们:

```
export const StyledText = makeStyledComponent(Text);
export const StyledView = makeStyledComponent(View);

```

`useStyles`钩子内部使用`styles`方法。与`styles`方法不同，`useStyles`钩子支持黑暗模式。像任何其他 React 挂钩一样，您只能将`useStyles`挂钩与功能组件和另一个挂钩一起使用——它返回一个可以传递给 React 本地元素的样式对象。

下面的代码演示了如何使用`useStyles`钩子。

```
export default function App() {
  const wrapperStyles = useStyles({
    classes: ["flex:1", "bg:black"],
  });
  const textStyles = useStyles({
    classes: ["color:white", "text:5xl"],
  });
  return (
    <View style={wrapperStyles}>
      <Text style={textStyles}>Hello World</Text>
      <StatusBar style="auto" />
    </View>
  );
}

```

## 《反应土著泽法》中的默认主题

React Native 泽法有一个默认的主题，你可以不用配置就可以使用——我们从一开始就在使用它。默认主题的灵感来源于 Tailwind CSS 中的默认主题；如果默认主题不符合您的需求，您也可以扩展它。

默认主题带有几个样式约束；泽法本地使用它来生成样式类。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

如下例所示，当您将`pl:56`样式应用到元素时，React Native 泽法将添加`224px`的左填充。在`pl:56`样式类中，`pl`是`paddingLeft`属性，`56`是默认的主题间距约束，当 React Native 泽法将样式转换为 React Native style 对象时，其大小为`224px`。文档中有一个默认主题约束及其相应比例的完整列表。

```
<StyledText classes={["pl:56"]}>Hello World</StyledText>

```

React Native 泽法使用`spacing`约束来生成边距和填充，以及调整样式类的大小。默认主题包括其他几个约束，比如`opacities`和`letterSpacing`约束。

## 扩展并覆盖 React Native 泽法中的默认主题

虽然它附带了一个默认的主题，但是 React Native 泽法允许您扩展和覆盖它。前面一节中介绍的`createStyleBuilder`函数将一个可选对象作为参数。该对象具有`overrideTheme`和`extendTheme`属性，分别用于覆盖和扩展默认主题。这两个属性的值可以是对象或函数。

如果您将`overrideTheme`属性的值设置为一个对象，如下例所示，React Native 泽法将覆盖默认主题中的颜色。颜色`dark`、`light`和`brandColorPalette`将覆盖下面示例中的默认颜色。

```
export const { makeStyledComponent, styles, useStyles } = createStyleBuilder({
 overrideTheme: {
  colors: {
    dark: "#737373",
    light: "#e5e5e5",
    brandColorPalette: "#a1323e",
  }
 }
});

```

您可以将`overrideTheme`属性的值设置为返回主题约束对象的函数，如下面的代码所示。当您将`overrideTheme`属性的值设置为一个函数时，您将可以访问基本字体大小。

```
export const { makeStyledComponent, styles, useStyles } = createStyleBuilder({
  baseFontSize: 18,
  overrideTheme: ({ baseFontSize }) => {
    return {
      colors: {
        dark: "#737373",
        light: "#e5e5e5",
        brandColorPalette: "#a1323e",
      },
      spacing: {
        enormous: baseFontSize * 100,
      },
    };
  },
});

```

在上面的例子中，React Native 泽法将用您在自定义主题中提供的颜色和间距覆盖默认主题中的颜色和间距。

您可以类似地使用`extendTheme`——与`overrideTheme`属性不同，`extendTheme`属性扩展了默认主题，而不是覆盖它。

```
export const { makeStyledComponent, styles, useStyles } = createStyleBuilder({
  extendTheme: {
    colors: {
      brandColorPalette: "#a1323e",
    },
  },
});

```

如果您想要访问基本字体大小，您也可以将`extendTheme`属性的值设置为一个函数，该函数返回一个对象，如下所示:

```
export const { makeStyledComponent, styles, useStyles } = createStyleBuilder({
  baseFontSize: 12,
  extendTheme: ({ baseFontSize }) => {
    return {
      spacing: {
        enormous: baseFontSize * 100,
      },
      colors: {
        brandColorPalette: "#a1323e",
      },
    };
  },
});

```

## 结论

诸如 Bootstrap 和 Tailwind CSS 之类的样式解决方案很受欢迎，因为它们提高了开发速度。React Native 泽法正试图将顺风 CSS 体验引入 React Native，如果你已经熟悉顺风 CSS，学习起来会很容易。

反应泽法本地配备了一个默认的主题，提供了一套慷慨的价值观，让你出发。如果默认主题不能满足您的需求，您可以按照本文所述扩展或覆盖它。虽然在撰写本文时还很年轻，但还是值得探索一下 React Native 泽法，看看它是否适合您——希望您会发现它对您的项目有用。

React Native 泽法有几个我们在本文中没有涉及的特性。更多特性请查看官方文档。请在下面的评论区告诉我你的想法。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)