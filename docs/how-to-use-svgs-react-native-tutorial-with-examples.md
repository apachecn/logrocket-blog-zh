# 如何使用 react-native-svg - LogRocket Blog 在 React Native 中导入 SVG 文件

> 原文：<https://blog.logrocket.com/how-to-use-svgs-react-native-tutorial-with-examples/>

当你开发 React 本地应用时，你可能会被要求实现图标。现在，简单的方法就是提取图标的`.png`或`.jpeg`文件，并在 React Native 的`Image`组件中使用它。这将为你做的把戏，但你不会得到清晰的质量，你最终会膨胀你的应用程序与更高的图像文件大小，这将增加你的应用程序包的大小。

你应该使用 [SVG 格式](https://blog.logrocket.com/svg-vs-canvas/)，而不是在你的 React 原生应用中使用`.png`或`.jpeg`文件。SVG 是一种基于矢量的格式，可以在不影响质量的情况下无限缩放。

在本指南中，我们将演示如何使用 react-native-svg 库在 React 本机应用程序中实现 SVG 图标。

我们将通过实际例子介绍以下内容:

## 什么是可缩放矢量图形(SVG)？

SVG 是一种基于 XML 的格式，用于渲染矢量图像。矢量图像可以根据您的需要任意缩放，而无需像素化，因为矢量图像由数学公式驱动，不像其他图像格式(如`.png`和`.jpeg`)那样具有像素。

由于 SVG 格式的矢量特性，图像与分辨率无关。SVG 图像在任何屏幕上都看起来很清晰，从新智能手机上的华丽的 285 DPI 像素密度屏幕到标准显示器的 85 DPI 屏幕。与其他图像格式相比，SVG 文件的大小也很小。

如果您在文本编辑器中打开一个 SVG 文件，您将看到一个包含数字和各种节点的大型 XML 代码。在本教程中，我们不会关注 SVG 本身。相反，我们将演示如何在移动应用程序屏幕上呈现 SVG。

## React 原生支持 SVGs 吗？

在移动应用程序中呈现 SVG 不像在 web 上那么简单，在 web 上，您可以直接使用 SVG 作为图像源，或者将 SVG 代码粘贴到 HTML 文件中。这是因为没有内置的 React 本地组件可以直接呈现 SVG。

由于 React Native 不提供对 SVG 的默认支持，我们必须从 npm 注册表安装一些库。幸运的是，有一个流行的 npm 包，名为 [react-native-svg](https://github.com/react-native-svg/react-native-svg) ，对于大多数用例来说都很好。

让我们从建立一个 React 本地项目开始。运行以下命令:

```
npx react-native init SvgDemoApp

```

要安装 react-native-svg 和[react-native-SVG-transformer](https://github.com/kristerkari/react-native-svg-transformer)包，进入项目目录并运行以下命令:

```
cd SvgDemoApp
npm i react-native-svg
npm i --save-dev react-native-svg-transformer

```

react-native-svg 为 Android 和 iOS 平台上的 react 原生项目提供 svg 支持。react-native-svg-transformer 使您能够在 React Native 项目中导入本地 svg 文件，就像您在 web 上的 [Creact React App](https://blog.logrocket.com/getting-started-with-create-react-app-d93147444a27/) 项目中所做的那样。

如果您使用的是 [Expo CLI 而不是 React Native CLI](https://reactnative.dev/docs/environment-setup) ，您可以通过运行以下命令开始。

```
expo init SvgDemoApp
expo install react-native-svg

```

## 在 React Native 中呈现 SVG 形状

让我们看看如何使用 react-native-svg 库在应用程序中呈现 svg。

在您最喜欢的编辑器中打开项目，从 react-native-svg 导入`Svg`和`Circle`组件，如下所示。

```
import Svg, { Circle } from 'react-native-svg';

```

`<Svg>`组件是呈现任何 SVG 形状所需的父组件。它就像是所有 SVG 形状的容器组件。如果您正在呈现任何 SVG 形状，例如圆形或多边形，您必须使用它作为 SVG 组件周围的包装组件。

```
<Svg height="50%" width="50%" viewBox="0 0 100 100" >
       ...
</Svg>

```

`<Svg>`组件需要三个道具:`height`、`width`和`viewBox`。`viewBox`道具描述了你的 SVG 在空间中的位置。`height`和`width`道具不言自明。

在`<Svg>`组件内部，如下所示渲染`<Circle>`组件:

```
<Svg height="50%" width="50%" viewBox="0 0 100 100" >
    <Circle cx="50" cy="50" r="50" stroke="purple" strokeWidth=".5" fill="violet" />
</Svg>

```

`<Circle>`组件将`x`和`y`坐标分别作为`cx`和`cy`道具。这些坐标定义了 SVG 组件在容器中的定位方式和位置。如果您要呈现一个不同的 SVG 形状，比如一个矩形，那么同样的形状将分别用`x`和`y`道具来表示。

要描述圆的半径，可以将一个整数作为字符串传递给`r` prop。您可以设定该值来增加或减少渲染的 SVG 圆的大小。

`stroke`属性可以用来表示 SVG 元素周围边框的颜色，而`strokeWidth`表示该边框的粗细。最后,`fill`属性表示呈现的 SVG 元素的颜色。这些属性类似于原生 HTML `<svg>`元素的属性，在大多数 SVG 组件中是通用的。

看看在屏幕上呈现 SVG 圆的完整代码:

```
import React from 'react';
import { StyleSheet, View } from 'react-native';
import Svg, { Circle } from 'react-native-svg';

export default function App() {
  return (
    <View style={styles.container}>
      <Svg height="50%" width="50%" viewBox="0 0 100 100" >
        <Circle cx="50" cy="50" r="50" stroke="purple" strokeWidth=".5" fill="violet" />
      </Svg>
    </View>
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

您应该会看到一个紫色的 SVG 圆圈，如下所示:

![React Native SVG Circle Shape Example](img/cc75bf08c0bd471dab47eecc24531b5d.png)

注意，我已经将`<Svg>`组件包装在了`<View>`组件中。您可以将您的 SVG 组件包装在任何通用容器组件中，比如`<View>`或任何其他定制包装器组件。这允许您使用 [Flexbox 布局](https://blog.logrocket.com/how-to-build-a-basic-flexbox-layout-a-tutorial-with-examples/)在屏幕上的任何地方放置和定位 SVG 组件，如上面的例子所示。

类似地，您可以呈现其他 SVG 形状，如矩形、多边形、直线、椭圆等。

## 如何在 React Native 中渲染 SVG 图像和图标

既然您已经了解了如何使用 react-native-svg 库呈现任何 svg，那么让我们来探索如何在您的应用程序中呈现 SVG 图标和图像。

这里，您需要使用一个名为`SvgUri`的不同组件，所以让我们从库中导入它:

```
import { SvgUri } from 'react-native-svg';

```

`<SvgUri>`组件带`width`、`height`和`uri`道具。您可以指定指向 SVG 的 URL 的`uri`属性。例如，如果您希望呈现这个 SVG 的[，您可以将这个 URL 分配给您的`<SvgUri>`组件的`uri`属性，如下所示:](https://dev.w3.org/SVG/tools/svgweb/samples/svg-files/debian.svg)

```
<SvgUri
    width="100%"
    height="100%"
    uri="https://dev.w3.org/SVG/tools/svgweb/samples/svg-files/debian.svg"
/>

```

这与在 React 中渲染图像非常相似，在 React 中，您将指定`<img>`的`src`属性作为图像的 URL。

上面的代码应该在屏幕上呈现 SVG，如下所示:

![React Native SVG Icon Example](img/30d1e71e0b1b29e5cfad193b0b8e645c.png)

您可以使用`<SvgUri>`组件的`width`和`height`道具来调整 SVG 的宽度和高度。与呈现 SVG 形状时不同，这里不需要容器组件。

您可能需要引用本地 SVG 图标或项目内部的图像。当您设置示例项目时，回想一下您还安装了库`react-native-svg-transformer`。您可以使用它来呈现项目中的本地 SVG 图标或图像。

首先，您需要对您的项目进行一些配置更改。转到你项目的`metro.config.js`文件。如果您的项目中不存在该文件，您需要创建它。

然后，在其中添加以下代码:

```
const { getDefaultConfig } = require('metro-config');

module.exports = (async () => {
  const {
    resolver: { sourceExts, assetExts },
  } = await getDefaultConfig();
  return {
    transformer: {
      babelTransformerPath: require.resolve('react-native-svg-transformer'),
    },
    resolver: {
      assetExts: assetExts.filter(ext => ext !== 'svg'),
      sourceExts: [...sourceExts, 'svg'],
    },
  };
})();

```

接下来，下载一个 SVG 并保存在您的项目中，类似于您下载要在项目中使用的图像的方式。假设您将 SVG 文件命名为`image.svg`。您现在可以像导入任何其他组件一样导入这个 SVG 文件:

```
import SVGImg from './image.svg';

```

并将其呈现在任何组件中:

```
<SVGImg width={200} height={200} />

```

上面的代码应该在屏幕上呈现相同的 SVG。如果您的项目要求您在本地呈现 SVG 图标，您可以遵循这种方法在您的应用程序中呈现不同的 SVG 图标。

## 使用 XML 字符串呈现 SVG

在极少数情况下，如果您不能使用`<SvgUri>`组件引用本地 SVG，您可以使用 XML 字符串在 React Native 应用程序中呈现 SVG。

假设您已经在项目中下载了[这个 SVG 文件](http://thenewcode.com/assets/images/thumbnails/homer-simpson.svg)。如果进入这个 SVG 文件，您会注意到一堆带有`<svg>` HTML 元素的 XML 代码。您可以使用`<SvgXml>`组件从 XML 代码直接呈现 SVG:

```
import { SvgXml } from 'react-native-svg';

```

从 SVG 文件的 XML 代码中复制`<svg>`元素中的所有内容，并将其存储在一个变量中。

```
const xml = `
<svg version="1.1" id="Layer_1"  xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
width="500px" height="500px" viewBox="0 0 500 500" enable-background="new 0 0 500 500" xml:space="preserve">
...
</svg>
`;

```

然后，将上述变量传递给`<SvgXml>`组件中的`xml` prop，如下所示:

```
<SvgXml xml={xml} width="100%" height="100%" />

```

瞧啊。您现在应该在屏幕上看到上面的 SVG。

![React Native SVG Homer Simpson Example](img/dd846b35774005086fa3e5f040023305.png)

## 结论

SVG 非常适合渲染您想要在 React 本机应用程序中使用的图像和图标。您甚至可以使用它们来创建复杂的形状和图案，为您的应用程序设计增添更多美学吸引力。

请记住，在本地存储大量的 SVG 文件仍然会使您的应用程序膨胀。您应该始终避免在项目中保存大量 SVG 文件并在本地引用它们。如果你绝对需要，你可以使用[这个便利的工具](https://jakearchibald.github.io/svgomg/)优化你的 SVG 文件。

我希望本教程能让您在 React 原生项目中更容易地使用 SVG。您还可以探索和使用 [react-native-svg 官方文档](https://github.com/react-native-svg/react-native-svg)中的示例。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)