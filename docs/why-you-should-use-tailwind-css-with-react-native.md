# 为什么应该在 React Native 中使用 Tailwind CSS

> 原文：<https://blog.logrocket.com/why-you-should-use-tailwind-css-with-react-native/>

谈到造型，React Native 是一个很好的选择。内置的[样式表](https://reactnative.dev/docs/stylesheet)方法允许您在 JSX 之外轻松分离样式。

坚持使用 React Native 通常是个好主意，但是它也有自己的缺陷。首先，很难在一些外部位置维护样式以重用它们。另一个问题是在整个代码库中管理适当的命名约定。这些问题可以通过在 React Native 中使用 Tailwind 来解决。

Tailwind CSS 完全改变了构造 CSS 的常用方法。在学习良好的代码实践的同时，开发人员很快理解了关注点分离以及在两个文件之间拆分 HTML 和 CSS 的重要性。[效用至上的 CSS](https://blog.logrocket.com/top-utility-first-css-frameworks/) 似乎完全不同。

虽然开发人员社区对实用优先的 CSS 有些不满，但很难不注意到 Tailwind CSS 越来越受欢迎。作为其能力的证明，Tailwind 获得了 CSS 2020 年状态调查中最受欢迎技术的奖项。

那么，有什么大惊小怪的？

## 效用第一类方法

在 React Native 中，实用程序优先的类通过元素的 class 或 style 属性将特定的 CSS 属性应用于元素。以前的方法是将样式对象内联应用于元素，或者引用应用了自定义名称的样式表对象的键。

实用程序优先的类方法允许您简单地向元素添加一个类名，而不需要编写自定义样式。实现颜色、版式和结构，以及为每个样式元素提供一个有意义的名称的决定已经做出。

Tailwind 基于一个可靠的设计系统为您提供默认值，这使得整个代码库保持一致。初看起来，学习曲线似乎很陡，但是精通这种风格的开发人员能够以更快、更统一的方式工作。此外，当一个团队使用一组精选的有限选项时，更容易雇佣一个新的开发人员并保持您的风格方法的一致性。

最后，由于样式表被重用，代码库将停止增长，因此更容易维护。

## 比较样式组件方法

出于多种原因，使用样式化组件是一种很好的样式化方法。然而，乍一看不知道一个给定的组件是 React 还是一个愚蠢的组件真的会减慢速度。当我们创建从未被重用的组件时，也有过早抽象的情况。

### 考虑内嵌样式

当涉及到将代码与 Tailwind 类混合时，一个缺点是我们的代码会很快变得冗长。通常，我们会不惜一切代价避免内联样式，因为它们会对代码的可读性产生负面影响，并可能损害整体性能。开发人员认为样式表方法比内联样式更有性能，因为它一次通过桥发送整个对象。这在 [React 原生源代码](https://github.com/facebook/react-native/blob/master/Libraries/StyleSheet/StyleSheet.js#L222)中进一步阐明。

## 在 React Native 中使用顺风

虽然有几个顺风包非常适合 React Native，但我选择使用 [`tailwind-react-native-classnames`](https://github.com/jaredh159/tailwind-react-native-classnames) 而不是 [`tailwind-rn`](https://github.com/vadimdemedes/tailwind-rn) 。`tailwind-react-native-classnames`基于 [Classnames](https://github.com/JedWatson/classnames#readme) ，这是一个 JavaScript 实用程序，用于改进 Web 上的 Tailwind 代码，因此使用 Tailwind CSS 的开发人员应该更熟悉它。

您可以随意使用不同的库，但是对我来说，带标签的模板函数方法比像在`[react-native-tailwindcss](https://github.com/TVke/react-native-tailwindcss)`中那样传递给数组的样式更具视觉吸引力。您也可以选择简单地从`[react-native-tailwind](https://www.npmjs.com/package/react-native-tailwind)`库中导入 React 本地组件，并将类传递给`className` prop。

有两种方法可以使用`tailwind-react-native-classnames`库将 Tailwind 类应用到元素中。基本的一个使用 ES6 标记的模板函数，它只是将类名包装在反斜杠中，如下面的`View`元素所示。第二种方法使用 Classnames 包，允许组合类和处理更复杂的情况。请注意下面的`Text`元素:

```
import tw from 'tailwind-react-native-classnames';

const MyComponent = () => (
 <View style={tw`bg-blue-100`}>
   <Text style={tw.style('text-md', invalid && 'text-red-500')}>Hello</Text>
 </View>
);

```

### 反应本机特定类

我们有两个 React 本地类来使用特定于平台的样式，它们可以安全地与标记模板函数一起使用:

```
tw`ios:pt-4 android:pt-2`;

```

### 顺风类名

如果你看一下[顺风](https://tailwindcss.com/docs/list-style-type)主页左侧的菜单，你会看到几个部分。其中只有一些与 React Native 相关，但是如果您已经知道如何在 React Native 中设计组件的样式，您将很快掌握可以使用的内容。您最感兴趣的部分是:

*   **布局**:处理大小和位置等事情
*   **柔性盒**:用柔性盒定位元件
*   **间距**:填充和边距的类别
*   **尺寸**:宽度和尺寸等级
*   字体设计:与字体、字母间距和文本相关的一切
*   **背景**:背景颜色、图像、背景不透明度等类别
*   **边框**:半径、宽度和颜色类别

也有**效果**部分，你可以找到不透明，但也有可能使用类。使用`tailwind-react-native-classnames`中的`shadow`，位于该部分的**框阴影**部分。

我也发现这个[小抄](https://nerdcave.com/tailwind-cheat-sheet)非常有用。当试图指定值时，可以检查元素属性的代码是否正确。比如设置元素宽度，可以使用`w-`类名，设置数字`w-40`得到 160px。

## 比较样式表和 Tailwind CSS 方法

现在，让我们使用这两种方法构建一个简单的布局，比较 Tailwind 代码和 React 本机代码。对于这个例子，我决定用卡片创建 [ScrollView](https://blog.logrocket.com/common-bugs-react-native-scrollview/) 。将选定的变量传递给组件，该组件决定标题集的不同背景和颜色。

![Scrollview Cards Simple Layout Tailwind React-native](img/502fd663508e1c49b5c22209f258b860.png)

本文的完整代码示例位于 Git 存储库[tailwindcsreactnative](https://github.com/IdaszakDaniel/TailwindCssReactNative)中。你也可以运行来自[博览会](https://expo.io/@idaszakdaniel/projects/tailwind_css_react_native)的例子。首先，我们将使用样式表方法构建上面的组件:

```
const ListItem = ({
 uri,
 selected = false,
 text=""
}) => {
 return (
   <View style={[styles2.container, { ...(!selected && { backgroundColor: '#FFFFFF'})}]}>
     <View style={styles2.logoBackground}>
       <Image
         style={styles2.logo}
         source={ uri }
       />
     </View>
     <Text style={[styles2.text, { ...(!selected && { color: 'black'})}]}>{ text }</Text>
     <TouchableOpacity style={styles2.button}>
       <Text style={styles2.buttonText}>Details</Text>
     </TouchableOpacity>
   </View>
 )
}

```

可以看到，传递了三个道具:`uri`用于图像，`text`用于卡片标题，以及关于组件是否被选中的信息。根据`selected`的真假，在主视图中添加了`backgroundColor`，在`Text`组件中添加了`color`。

样式表用于设置该组件的样式:

```
const styles2 = StyleSheet.create({
 container: {
   height: 256,
   width: 160,
   backgroundColor: 'rgba(59,130,246,1)',
   borderRadius: 12,
   padding: 15,
   margin: 5,
   alignItems: 'center',
   justifyContent: 'center',
   shadowColor: "#000",
   shadowOffset: {
     width: 0,
     height: 3,
   },
   shadowOpacity: 0.27,
   shadowRadius: 4.65,
   elevation: 6,
 },
 logoBackground:{
   width: 112,
   height: 112,
   borderRadius: 55,
   backgroundColor: '#E4F0FE'
 },
 logo: {
   width: 110,
   height: 110,
   borderRadius: 55
 },
 text: {
   color: 'white',
   fontSize: 18,
   fontWeight: 'bold',
   marginVertical: 10
 },
 button: {
   height: 40,
   width:'100%',
   backgroundColor: 'white',
   borderRadius: 20,
   alignItems: 'center',
   justifyContent: 'center',
   borderWidth: 1,
   borderColor: 'rgba(59,130,246,1)'
 },
 buttonText: {
   color: 'rgba(59,130,246,1)',
   fontSize: 17,
   fontWeight: 'bold'
 }
});

```

现在，我们可以用 Tailwind CSS 方法和`tailwind-react-native-classnames`库构建相同的组件。

```
import React from 'react';
import { Text, View, TouchableOpacity, Image } from 'react-native';
import tw from 'tailwind-react-native-classnames';

export const ListItemTW = ({
 uri,
 selected = false,
 text=""
}) => (
 <View style={tw.style(
   'h-64 w-40 bg-blue-500 rounded-xl p-4 m-1 items-center justify-center shadow-lg',
   !selected && 'bg-white'
 )}>
   <View style={tw`w-28 h-28 rounded-full bg-indigo-50`}>
     <Image
       style={tw`w-28 h-28 rounded-full`}
       source={ uri }
     />
   </View>
   <Text style={tw.style(
     'text-white text-lg font-bold my-4',
     !selected && 'text-black'
   )}>
     { text }
   </Text>
   <TouchableOpacity
     style={
       tw`h-10 w-full bg-white rounded-full items-center justify-center border border-blue-500`
     }
   >
     <Text style={tw`text-lg text-blue-500 font-bold`}>
       Details
     </Text>
   </TouchableOpacity>
 </View>
)

```

那么，有什么区别呢？注意，Tailwind 组件有 36 行，而普通样式表组件有 76 行。最大的缺点是有几行很长的代码。其中两个甚至是多行的，因为使用了 Classname 的`tw.style`来连接带有条件添加样式的类。

使用预定义的样式有它的优点和缺点。如上所述，好处包括更快的开发时间和易于维护。最大的缺点是，如果您使用一些不常见的值，比如标准值中不包含的尺寸或颜色，您将不得不定制您的样式。我认为最简单的方法是将样式对象传递给`tw.style`，它应该可以被正确识别。

## 摘要

在项目中使用 Tailwind CSS 可以统一整个代码库。您可以应用许多开发人员熟知的预定义列表中的类名，而不是为样式表中的样式提供自定义名称。

此外，应该提高开发速度，因为对组件进行样式化和评估对特定元素应用什么样式更容易。这种方法也有缺点，比如使一些组件过于冗长。我相信这些问题可以通过将一些较长的、可重用的部分导出为样式化组件或者将它们导出到外部文件来解决，但是这取决于您来决定项目的最佳方法。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)