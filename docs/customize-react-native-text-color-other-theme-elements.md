# 自定义 React 原生文本颜色和其他主题元素

> 原文：<https://blog.logrocket.com/customize-react-native-text-color-other-theme-elements/>

在 React 原生应用程序中使用正确的配色方案，不仅有助于吸引用户，还能促使他们参与到应用程序的某些部分中。然而，想出合适的颜色可能相当棘手。

许多开发人员发现自己在选择配色方案时左右为难。这导致他们花费数小时在颜色之间切换，而他们应该专注于设计应用程序的总体布局。

如果你是这些开发者中的一员，那么你来对地方了。在这篇文章中，我们将展示如何快速为你的应用程序设计一个配色方案，还将介绍一些最佳实践来指导你的过程。

向前跳:

## 先决条件

要跟随本指南，请从本次世博会小吃项目的[中获取启动应用程序。随着本教程的进行，我们将对应用程序进行更改。](https://snack.expo.dev/@ubahthebuilder/to-do-app)

## 从简单的黑白布局开始

如果你还不确定应用程序应该使用什么样的配色方案，最好从简单的黑白布局开始。

黑色和白色几乎可以和任何颜色搭配。最重要的是，使用简单的黑白主题可以让你首先专注于组织总体布局，并确保元素看起来尽可能好。

一般来说，在引入颜色之前，你的布局需要工作。上色只能让东西好看；它不能解决主要问题。

黑白布局应该使用:

*   白色背景上的黑色文本
*   黑色背景上的白色文本

这是我们的入门待办事项应用程序的黑白外观:

![Mobile App With Black Text Title Today's Tasks Followed By Three Black Rounded Rectangles Containing White Check Boxes And Task Descriptions With Option To Add New Task At Bottom](img/e1583e4efbbcc85998a6189909a53cff.png)

当你对布局感到满意时，你可以考虑引入一种“醒目”的颜色，使主题看起来更生动。

## 给你的主题增加一种颜色

许多移动应用程序使用黑、白和第三种颜色的组合来给主题添加一些变化。

获得第三种颜色最简单的方法是从应用程序中的图像中选择一种。用这种方法，你更有可能得到一致的配色方案。如果你不确定要组合哪些颜色，你可以使用 Adobe Color 这样的颜色生成工具来解决这个问题:

![Adobe Color Tool Ui Showing Options At Top Left To Extract Gradient Or Browse New Accessibility Tools, Color Wheel With Five Color Picker Spokes, And Row Of Five Rectangles Displaying Colors Chosen With Spokes, Ranging From Dark Pink To Periwinkle](img/778af860d2d58dbcda205022ffd23fc2.png)

[Adobe Color](https://color.adobe.com/create/color-wheel) 向您展示与您选择的基色协调工作的颜色。至少有一些[色彩理论的基本知识](https://en.wikipedia.org/wiki/Color_theory)是有帮助的。虽然这是一个需要深入探讨的话题，但我们还是谈谈它的基本原则吧。

根据色彩理论，有三原色——红色、黄色和蓝色。通过混合这三种原色，我们可以得到三种二次色——绿色、橙色和紫色。如果我们将三原色和二原色混合，我们可以得到六种三原色。

让我们看看这个基本理论，因为它与我们目标的色轮有关。首先，轮盘显示所有的原色、二色和三色，互补色彼此相对放置:

![Adobe Color Tool Wheel Demonstrating Five Variants Of Primary Colors: Light Blue, Light Red, Light Yellow, Slightly Darker Blue, Slightly Lighter Red](img/ab6d91778b5c73fb537e7e2fa213004e.png)

既然我们已经理解了为应用程序选择颜色背后的逻辑，那么让我们为 React Native 应用程序的待办事项列表添加第三种颜色。

## 将三色配色方案应用到 React 原生应用中

在这个例子中，我将把十六进制值为`#55BCF6`的蓝色[应用到任务框中的正方形和圆形。此外，我将使用一个十六进制值为`#E8EAED`的浅色版本作为主背景。](https://blog.logrocket.com/advanced-guide-setting-colors-css/)

打开您的`App.js`文件，向下滚动到样式表。首先，将主容器的背景颜色从现有的十六进制值`#fff`更改为`#E8EAED`——我们的浅蓝色:

```
 container: {
    // other CSS      
    backgroundColor: '#E8EAED',    
  },

```

接下来，将`+`按钮的背景颜色从`#000`更改为`#fff`:

```
addWrapper: {
    // other CSS
    backgroundColor: '#fff'
  },

```

接下来，进入`components/Task.js`文件。将正方形的背景颜色和圆形的边框颜色都改为`#55BCF6`——我们的深蓝色:

```
square: {
    // other CSS
    backgroundColor: '#55BCF6'
  },

  circular: {
    // other CSS
    borderColor: '#55BCF6'
  }

```

最后，将每个任务框中的文本改为黑色:

```
  itemText: {
    // other CSS
    color: '#000'
  },

```

您的应用程序现在应该类似于下图:

![Same To Do App Ui As Shown Previously, Now With Off White Background, White Rounded Rectangle Task Boxes, All Black Text, And Blue Checkboxes](img/4489603f9973dbfdded707e5bc82354f.png)

我们的示例应用程序使用三种颜色——白色、黑色和蓝色。我们添加的颜色越多，事情就变得越复杂。

一般来说，你的应用程序的配色方案最好尽可能简单。你可以使用特定颜色的不同阴影，就像我们在示例应用程序中使用蓝色一样。但总的来说，你在应用程序中使用的颜色越少，你的用户体验就会越好。

只有在有充分理由的情况下才添加新颜色，并确保您添加的任何新颜色都与 React 原生应用程序中的现有颜色很好地融合。

## 用`expo-font`添加自定义字体

你可以通过`expo-font`在你的 React 原生应用中使用自定义字体。对于我们的应用程序，让我们使用谷歌字体的 Orbitron 字体系列。

*   [下载 Orbitron 字体系列](https://fonts.google.com/specimen/Orbitron)到你的本地机器上
*   在小吃项目的`assets`文件夹中创建一个名为`fonts`的文件夹
*   将电脑中的字体导入到项目中
*   将导入的字体移动到`assets/fonts`文件夹

现在你已经将字体上传到你的项目中，在你的`package.json`文件中的依赖对象中添加以下字段:

```
"expo-font": "10.2.1",

```

Expo Snack 会自动将库添加到您的项目中。

接下来，在 App.js 中，从文件顶部的`expo-font`导入`useFonts`钩子:

```
import { useFonts } from 'expo-font';

```

将字体加载到您的应用程序功能中:

```
export default function App() {

  const [fontsLoaded] = useFonts({
    'Orbitron-Regular': require('./assets/fonts/Orbitron-Regular.ttf'),
  });

  // return (...)
}

```

向下滚动到样式表，将字体系列应用到主`sectionTitle`:

```
sectionTitle: {
   // Other CSS
   fontFamily: 'Orbitron-Regular'
 },

```

打开`components/Task.js`文件，执行上述相同的步骤。这一次，将字体应用到`.itemText`元素:

```
  itemText: {
    // other css
    fontSize: '12px',
    fontFamily: 'Orbitron-Regular'
  },

```

您的应用程序现在应该看起来像这样:

![Same To Do App Ui As In Previous Image, Now With Font Customized To Orbitron Font Family](img/1b58e4b2f47ec4035f28989dee2f980a.png)

这里我们使用不同大小的相同字体。

当你引入另一种字体时，事情开始变得更加复杂。你必须考虑这些字体如何一起工作。当你引入第三种字体、第四种字体等等时，事情会变得更加复杂。

一般来说，就像颜色一样，字体越多，就越难处理。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 自定义 React 原生文本的颜色、粗细和透明度

在 React 原生应用中定制文本时，首先要考虑的是颜色。

重要的是你要选择一种与背景色形成鲜明对比的颜色。如果你的文本对比太微妙——就像，它与背景融为一体——人们可能很难在你的应用程序上阅读文本，这会对用户体验产生负面影响。

在前面的 React 本地应用程序示例中，我们将每个任务框中的文本颜色设置为黑色。这个选择与白色的任务框形成了鲜明的对比。从 W3C 网页可访问性倡议中了解更多关于[为什么选择对比度好的颜色很重要的信息](https://www.w3.org/WAI/perspective-videos/contrast/)。

与文本相关的其他重要 UX 元素包括透明度和字体样式，如粗体和斜体。通过使用不同的字体变体，人们可以很容易地浏览应用程序上的文本。

**粗体文本**通常用于强调你希望用户看到的单词和短语。这可能是一个好处，一个特别优惠，一个标题，或其他形式的紧急信息。

斜体字可以用来表示微妙的信息，如旁注、提示和其他种类的非紧急信息。

字体通常有不同的粗细——正常、粗体和浅色，仅举几个例子。您可以在 React Native 中使用`[fontWeight](https://reactnative.dev/docs/text-style-props#fontweight)`[属性](https://reactnative.dev/docs/text-style-props#fontweight)来控制文本的粗细:

```
  itemText: {
    fontSize: '12px',
    fontWeight: 'bold' // other values include thin, light, normal, etc.
  },

```

通过使文本更加透明来表现微妙的信息也很常见。我们可以在示例应用程序中看到这一点。看看提示用户添加新任务的文本是如何变灰的，其对比度比其他文本低:

```
  addText: {
    color: '#000',
    opacity: 0.2,     
  }

```

![To Do App Ui Shown With Red Box Around Widget For Adding New Task To Show How Font Opacity Can Be Applied To React Native App](img/5f5c3731a69fbc6b0d0c397e60264a35.png)

调整文本的不透明度时，确保文本与其背景之间的对比度足够高，以使所有人(包括视力不好的人)都能轻松阅读内容，这一点很重要。

## 结论

一个好的 React 原生配色方案可以帮助你脱颖而出，传达你是谁，你是什么，并最终帮助人们通过你的应用程序与你联系。在测试不同的颜色之前，准备好布局是很重要的。

请记住，对于所有 Reach 原生主题元素，包括文本颜色、字体、整体配色方案等等，越少越好。不要超过四种颜色，以免使设计复杂化。尽量坚持用两三种颜色。

感谢您的阅读，祝您度过愉快的一周。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)