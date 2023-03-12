# React Native - LogRocket 博客中长列表的 RecyclerListView 与 FlatList

> 原文：<https://blog.logrocket.com/recyclerlistview-vs-flatlist-long-lists-react-native/>

## 介绍

在 React 和 [React Native](https://blog.logrocket.com/tag/react-native/) 中，高效呈现长列表一直是个问题。这样做可能是一个令人生畏的挑战，React 生态系统中已经开发了几个包来解决这个问题。React Native 中一些值得注意的解决方案包括内置的`FlatList`组件和 [RecyclerListView](https://github.com/Flipkart/recyclerlistview) 第三方包。

这两个软件包有许多共同之处。这两种方法都在很大程度上进行了优化，以呈现更长更复杂的列表。本文将通过关注内置的`FlatList`组件和第三方 RecyclerListView 包的工作方式来对它们进行比较。

如果您有兴趣学习如何使用`FlatList`组件或 RecyclerListView 包来呈现列表，您可以查看它们各自的文档。本文的主要目标是通过关注它们是如何工作的来比较这两者。

希望这种比较能够帮助您选择一个符合您的用例的。

## React Native 的`FlatList`组件是什么？

[内置的`FlatList`组件](https://blog.logrocket.com/deep-dive-react-native-flatlist/)是`VirtualizedList`组件的方便包装器。`VirtualizedList`是`FlatList`和`SectionList`组件的基础实现。

`FlatList`组件是 React Native 中呈现基本平面列表的实际接口。它性能卓越，功能丰富。它的一些显著特征包括:

*   支持水平模式
*   标题支持
*   页脚支持
*   分离器支架
*   拉至刷新
*   完全跨平台
*   可配置的可视性回调
*   滚动加载
*   多柱支架

要获得`FlatList`组件特性的完整列表，您可以查看 React 本地文档的相应部分。

首先，`FlatList`组件延迟呈现列表项。为了减少内存使用和处理时间，`FlatList`渲染即将出现在屏幕上的项目，并删除那些已经滚出屏幕的项目。它将屏幕外列表项的视图替换为适当间隔的空格。

它还在屏幕外异步呈现内容，以限制内存并实现平滑滚动。然而，如果滚动速率快于填充速率，这具有暂时显示空白内容的缺点。

这种内置的性能优化旨在使`FlatList`比其他内置的列表呈现组件如`ScrollView`更有性能。

尽管功能丰富，性能更高，`FlatList`也有它的缺点。虽然创建和销毁屏幕上和屏幕外项目的视图有助于减少内存使用，但它的计算开销很大。最终，许多对象不得不被垃圾回收。如果用户滚动整个列表，`FlatList`会创建与列表中项目一样多的视图——随着用户的滚动，创建和销毁视图。

因为内容在屏幕外异步呈现以限制内存并实现平滑滚动，所以快速滚动一个长列表会在屏幕上产生可见的空白。这种效果在滚动速度更快的 Android 上可以更加明显。

## 什么是 RecyclerListView 包？

与内置组件`FlatList`不同，RecyclerListView 是一个第三方包，用于呈现一长串项目。它支持 React Native 和 React Native Web，您可以像这样安装它:

```
# Using npm

npm install recyclerlistview

# Using yarn

yarn add recyclerlistview

```

RecyclerListView 是一个纯 JavaScript 的列表视图，没有任何本机依赖关系。这个包的灵感来自 Android 上的`RecyclerView`和 iOS 上的`UICollectionView`。它是高性能的，和内置的`FlatList`组件一样功能丰富。

它的一些显著特征包括:

*   支持水平模式
*   页脚支持
*   它是跨平台的，所以它可以在网络上工作
*   支持交错网格布局
*   支持可变高度项目
*   从`GridView`到`ListView`的即时布局切换，反之亦然
*   终点到达检测
*   滚动位置保持

RecyclerListView 包是考虑到`FlatList`的缺点而构建的。RecyclerListView 智能地回收以前创建的视图，而不是为屏幕上的元素创建视图并销毁屏幕外元素的视图。

RecyclerListView 包有三个构造块，即:

*   数据和布局提供程序
*   可视性跟踪器
*   虚拟投标人

通过数据和布局提供程序，您可以指定数据、数据类型以及估计尺寸或精确尺寸。然后，RecyclerListView 使用指定的类型来决定是创建新视图还是重用在视口中不可见的现有视图。

可见性跟踪器，顾名思义，跟踪可见项目并通知虚拟渲染器关于视口的状态。然后，虚拟渲染器使用这些信息来生成渲染堆栈。

## 比较`FlatList`和 RecyclerListView

如前所述，`FlatList`和 RecyclerListView 是 React Native 和 React Native Web 的性能列表视图。然而，`FlatList`的性能不如 RecyclerListView。

`FlatList`是内置组件。它被优化为在屏幕外异步呈现视图，以限制内存并实现平滑滚动。它破坏了屏幕外的项目，代之以适当大小的空白空间。然而，这种优化也有其缺点。

当用户滚动很长的列表时创建和销毁视图是低效的，并且在处理很长的列表时可能会导致性能下降。值得一提的是，如果用户上下滚动整个列表，那么`FlatList`至少会创建与条目数量一样多的视图。这是有问题的，尤其是在处理无限列表的时候。

另一方面，RecyclerListView 是为了解决上面强调的内置`FlatList`组件的一些缺点而构建的。RecyclerListView 不像`FlatList`那样创建和销毁视图，而是回收它们。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

屏幕外视图被有效地回收以显示在视图中的项目。由于这种优化，RecyclerListView 包比`FlatList`组件更高效，性能也更好。

如果您希望在 React 本地应用程序中呈现简单的列表，内置的`FlatList`组件很可能就足够了。它很容易上手，并且有很好的文档。

然而，如果您正在处理长的或无限的列表，很容易遇到一些性能瓶颈，这些瓶颈来自于上面强调的使用`FlatList`。在这种情况下，RecyclerListView 包可能是比`FlatList`更好的选择。

不幸的是，如果您选择使用 RecyclerListView 包，您将需要做更多的工作。使用 RecyclerListView 不像`FlatList`那么直接，它的文档也没那么棒。你需要玩它来达到某种程度的熟悉。

## 结论

经常会遇到需要呈现一个很长或无限长的列表的问题。React Native 有内置的`FlatList`组件专门为此进行了优化，但是这个内置的解决方案也有它的缺点。

开发 RecyclerListView 包是为了解决内置`FlatList`组件的局限性。它支持 React Native 和 React Native Web。

顾名思义，RecyclerListView 包可以有效地回收视图外的项目，而不是像`FlatList`组件那样创建和销毁视图。如果您的用例涉及呈现一个很长的项目列表，可能是几个视图的内容，那么 RecyclerListView 是比`FlatList`组件更好的选择。

虽然`FlatList`组件有很好的文档记录并且容易获取，但是 RecyclerListView 包却没有。使用 RecyclerListView 包的缺点(尽管它带来了巨大的性能优势)是很难设置并且文档相对较差。

如果我遗漏了`FlatList`和 RecyclerListView 之间的一些相似或不同之处，请在下面的评论区告诉我。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)