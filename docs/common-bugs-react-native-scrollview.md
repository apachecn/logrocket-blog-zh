# React Native ScrollView 中的常见 bug 及修复方法

> 原文：<https://blog.logrocket.com/common-bugs-react-native-scrollview/>

## 介绍

React Native 的 ScrollView 组件是一个通用容器，它可以包含多个元素——视图、文本、可按对象，甚至另一个 ScrollView。将所有这些元素放入 ScrollView 组件后，您可以使用它来垂直(默认)或水平(通过将其添加为道具)滚动它们。

我们可以在 App Store 和 Google Play 上的几乎所有应用程序中看到滚动组件。今天，我将解释在 React Native 中使用 ScrollView 时的一些常见错误以及如何避免它们，并提供一些提示和技巧来帮助提供最佳的用户体验。

## 何时使用滚动视图

使用 ScrollView 的第一个也是最常见的错误是不知道何时使用它。React Native 中有两个常见的列表组件:ScrollView 和 FlatList。

要确定使用哪一个，我们只需记住一件事:ScrollView 最适合显示少量有限大小的元素，因为 ScrollView 的所有子组件都是一次呈现的，即使它们当前在屏幕上不可见。

这就是为什么如果你的 ScrollView 中有很多元素，其中一些是，例如，较高的，纵向风格的图片，ScrollView 的性能可能会有点滞后，让用户不愉快。

例如，每当您以列表的形式从 API 获取数据时，在应用程序中显示它们的最佳方式是使用 FlatList 组件，它的实现方式不同。因为 FlatList 只呈现当前显示在屏幕上的元素——而不是一次呈现所有元素——所以它能够以更高效的方式显示长列表。

FlatList 向我们展示了一长串道具，这些道具是为呈现长数据列表的最佳 UX 和性能而设计的，包括:

*   `removeClippedSubviews`–一个布尔值，当启用时(默认情况下为`false`)，将视口之外的视图从本地视图层次结构中分离出来，这减少了花费在主线程上的时间，从而降低了丢帧的风险
*   `initialNumToRender`–这是要呈现到平面列表中的项目的初始数量。最佳实践是为每个设备定义覆盖视口的项目的精确数量，这可以提高组件初始渲染的性能。将该数值设置得太低可能会导致空白区域

关于如何提高 FlatList 的性能，还有一些更好的技巧，在这里的文档[中有很好的解释。我希望通过阅读上面的段落，我能帮助你理解什么时候使用哪个组件。](https://reactnative.dev/docs/optimizing-flatlist-configuration#props)

## 样式滚动条查看正确的方式

现在我们知道了*何时*使用 ScrollView 组件，让我们看看*如何*使用它。初学者通常会浪费大量时间来弄清楚如何正确地设计 ScrollView 的样式，因为它有两个样式道具:`style`和`contentContainerStyle`。以下是对这些道具的简短解释:

*   属性用于 ScrollView 父元素的样式，我们可以把它看作一个简单的、不可滚动的视图。我们将设置窗口视口中 ScrollView 元素的大小及其相对于同级元素的位置。此父容器不能超过视口的高度或宽度
*   `contentContainerStyle` prop 是我上面描述的父 ScrollView 中的一个可滚动容器。这是 ScrollView 的移动部分，我们在这里应用样式，允许我们在里面对齐项目，添加填充等等。

这里有一个活生生的例子，你可以在 [snack.expo.io](https://snack.expo.io/@karniej/bd5446) 上测试:

所以现在我们知道了什么时候使用 ScrollView 以及如何设置它的样式，我将解释一些可能引起一些混乱并导致 bug 的常见道具。

## 跟踪滚动位置

使用 ScrollView 时，我们经常需要跟踪滚动位置。要正确做到这一点，我们应该使用`onScroll`道具。滚动期间，该事件每帧最多触发一次。单个滚动事件是这样一个对象:

```
{
  nativeEvent: {
    contentInset: {bottom, left, right, top},
    contentOffset: {x, y},
    contentSize: {height, width},
    layoutMeasurement: {height, width},
    zoomScale
  }
}
```

为了正确使用这些值，我们必须知道这些属性的作用:

*   `contentInset`–这是从 ScrollView 的边缘到其内容的距离的边距；默认的对象值是`{top: 0, left: 0, bottom: 0, right: 0}`
*   `contentOffset`–该值是用户已经从开始滚动的距离，当我们想要跟踪滚动的值时最常用。如果是水平 ScrollView 组件，我们用的是`contentOffset.x`；如果是垂直的，我们用`contentOffset.y`
*   `contentSize`–我们使用这个道具来获取 ScrollView 中内容的`height`和`width`。它通常与`layoutMeasurement`和`contentOffset`一起使用——例如，确定用户是否已经滚动到内容的末尾。我们在使用这个道具时必须特别小心，因为出于性能原因，它在 Android 上是*而不是*可用的
*   `layoutMeasurement`–这是视口的`height`和`width`
*   `zoomScale`–滚动视图内容的当前比例值。当 ScrollView 只包含一个图像组件时，我们可以将它与`maximumZoomScale`和`minimumZoomScale`道具一起使用来放大图像

由于性能原因，正确使用`onScroll`和跟踪滚动位置可能是一项棘手的任务。这就是为什么记住我们用一个滚动事件调用多少必要的操作总是很重要。

为了更好地控制滚动事件，我们使用了一个`scrollEventThrottle` prop，它控制用户滚动时滚动事件的触发频率(以毫秒为单位的时间间隔)。较低的数量可以提高跟踪滚动值位置(`contentOffset`)的准确性，但这通常会导致性能问题。

数值 1-16 之间的差异肉眼看不出来，因为 JS 运行循环与屏幕刷新率同步(在大多数情况下，16 是推荐的数值)。设置较高的数字会导致通过 React 本机桥发送较少的代码，从而提高性能。

## 嵌套滚动视图

最后但同样重要的是，我想谈谈在另一个 ScrollView 组件中嵌套一个 ScrollView 组件。例如，当实现水平和垂直元素的混合时，我们有时可能需要这种组合。

默认情况下，嵌套 ScrollViews 在 iOS 上是自动启用的，但为了在 Android 上使用它，我们必须将`nestedScrollEnabled` props 设置为`true`，并且还要记住我们的 Android API 必须在 21+级(React Native v0.63 中的默认为 24)。

## 摘要

尽管 ScrollView 组件中有许多有趣的道具，允许我们创建大量有趣的用户体验，但我认为我已经根据我的经验强调了 ScrollView 组件最常见的错误和重要的用例。

我希望我已经用一种帮助你更容易使用这个组件的方式解释了它们。如果您在使用 ScrollView 组件时有任何其他问题，请随时在评论部分提出——我会关注它，并回答您可能有的所有问题。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)