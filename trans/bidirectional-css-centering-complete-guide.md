# 双向 CSS 居中:一个完整的指南

> 原文：<https://blog.logrocket.com/bidirectional-css-centering-complete-guide/>

当使用 CSS 时，我们认为页面元素是盒子，这些盒子的排列呈现了 CSS 布局的概念。

自然，每个页面元素在文档布局流中都有一个正常的位置。但是，我们经常改变默认行为来创建我们想要的精确布局。当我们想要将文档中的对象放在另一个对象的中心时，就会出现这种情况。

这是开发过程中的一项常见任务，然而，随着时间的推移，这项任务似乎变得困难和复杂，尤其是当它以垂直轴为中心时。

这使得开发人员求助于各种难以管理的方法和变通办法，如使用 CSS 表格属性，这会导致浏览器中的不一致并在屏幕阅读器上失败，或者使用具有负边距的绝对定位，只将指定宽度的对象居中。

另一个技巧是使用`position: absolute;`和`transform`属性使元素在容器中居中。但是，这种方法会创建笨拙的布局，并且每个容器只允许一个元素，而不需要额外的工作。

本质上，开发人员根据任务使用不同的方法来使对象居中，这就是使对象居中的痛苦所在:有太多的变通方法。

然而，在现代 CSS 中，我们不再需要经历这种压力，因为我们现在对如何在双向布局中居中对象有了更多的控制。

在本指南中，我们将研究 CSS 中的现代双向居中技术，同时遵循最佳实践来使对象居中，以便在设备和屏幕之间很好地缩放。

## 了解双向 CSS 居中

顾名思义，双向居中字面意思是“双向居中”

在 CSS 的上下文中，它被解释为在两个方向上使对象居中，即水平方向(也称为主轴)和垂直方向(也称为横轴)。

我们也可以把双向看作是在两个相反的方向上起作用并且不受流动方向影响的任何东西。

通过在 CSS 的上下文中应用这一概念，双向居中使不受更改布局流方向影响的对象居中。

换句话说，不管布局流程如何，即书写方向是否像英文文本一样从左到右，`ltr`，对象都保持在中心；`rtl`，喜欢阿拉伯文字；或者当书写模式设置为`vertical-*`时，如日语和蒙古语。

### 单向居中

单向居中包括单向居中，即水平或垂直地将对象居中。在这种情况下，对象的中心对齐取决于布局的流向。

让我们看看单向定心是如何工作的，以便更好地理解双向定心是如何工作的。

这里，我们有一个生成以下布局的 HTML 标记:

```
<div class="container">
  <div class="obj">Center this obj</div>
</div>

```

![Generated Layout Showing Horizontal And Vertical Axis With Grey Object Box In Left-Hand Corner](img/6e59549be574d32df7244a6951e57113.png)

考虑到一个`ltr`书写方向，我们设计了对象的样式以查看文档布局流中的初始位置。现在，通过[将物体垂直居中](https://blog.logrocket.com/13-ways-vertically-center-html-elements-css/)在它的容器内，我们得到如下结果:

![Centering Object Vertically, Object Appears On Left-Hand Side In Center](img/e9baafd2c3604215bac8dd290ee5b46a.png)

这里，我们可以使用 CSS flex 将对象垂直居中:

```
.container {
  display: flex;
  align-items: center;
  /* ... */
}

```

现在，忽略代码，专注于对象定位。如果我们更改此文档的书写模式或书写方向，对象的垂直居中对齐会受到影响。

下面的代码将书写模式更改为`vertical-*`，就像我们在蒙古语文本中看到的一样:

```
.container {
  /* ... */
  writing-mode: vertical-lr;
}

```

这种附加样式产生了一种新的布局:

![Unidirectional Centering On Horizontal Axis, Grey Object Is Centered On Top](img/3de69307c02f6cadc462dff8f6b2932b.png)

正如我们所看到的，对象的中心对齐取决于布局流的方向，这是单向居中。

另一方面，使用双向居中，无论布局流向如何，对象都必须保持在中心，如下所示。

![Bidirectional Centering Object On Both Axis In Center](img/46396cd0f7a933fee0daa22a65870051.png)

接下来，让我们看看如何实现这种类型的对象居中。

## 使用`position`和`transform`属性进行对象居中

如前所述，使用`position: absolute;`方法被认为是一种遗留技术。但是使用这个技巧的本质是让我们看到局限性，并更好地欣赏我们将在后面介绍的现代技术。

让我们重温一下之前的标记:

```
<div class="container">
  <div class="obj">Center this obj</div>
</div>

```

要使子元素在容器中居中，我们可以简单地应用以下代码:

```
.container {
  position: relative;
  /* ... */
}

.obj {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  /* ... */
}

```

通过使容器 a `position: relative;`和元素中心 a `position: absolute;`意味着我们可以[移动元素到容器的中心(参见 CodeSandbox)](https://codesandbox.io/s/vigilant-easley-o90cl?file=/style.css) 。

![Centering An Object Inside A Container](img/3e605cd04bf5bafcabaa500e13186dad.png)

使用这种方法的一个限制是，它通常只允许每个容器有一个元素，没有额外的样式。由于重叠的文本，它还会导致笨拙的布局。

因此，我们将学习现代技术，而不是使用这种或其他任何方法。

## 使用 CSS flexbox 进行对象居中

使用 CSS flexbox，我们可以轻松地控制对象在另一个对象中的位置。当我们将`display: flex`应用于父元素时，这意味着我们希望直接子元素成为 flex 项目，这样我们就可以应用对齐属性来控制这些项目。

同样，让我们使用早期的 HTML 标记:

```
<div class="container">
  <div class="obj">Center this obj</div>
</div>

```

通过使容器元素成为 flex 容器并应用对齐属性，如下所示，我们使[子元素完美地位于容器中心(参见 CodeSandbox)](https://codesandbox.io/s/ecstatic-neumann-tprfm?file=/style.css) :

```
.container {
  display: flex;
  align-items: center;
  justify-content: center;

  /* ... */
}

```

![Centering The Child Element With The Flex Container Alignment Properties](img/0dc60dd660d90b8dd4bc555130f6b394.png)

应用于 flex 容器的`align-items:center;`将子元素垂直居中，而`justify-content: center;`将元素在主轴上居中，即水平居中。

这种方法优于传统技术的一个优点是灵活性。我们可以在一个容器中无缝地集中多个对象，这本身就鼓励了代码的可维护性。

### 利用 flexbox 实现双向对中的实例

在处理页面的英雄部分、弹出消息或居中覆盖时，我们可以找到现实生活中的例子。

在这一部分，我们将看到如何完美地将页面的主要内容居中。让我们考虑以下标记:

```
<section class="hero">
  <h1>
    <!-- ... -->
  </h1>
  <p>
    <!-- ... -->
  </p>
  <button>Contact us</button>
</section>

```

为了简洁起见，我们从代码中删除了[内容(参见 CodeSandbox)](https://codesandbox.io/s/wonderful-meadow-jvg6q?file=/index.html) 。

通过在父容器元素`section`上应用 flex 和 alignment 属性，我们使子元素完美地在容器中居中:

```
.hero {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;

  /* ... */
}

```

然后我们得到以下结果:

![Applying Flex And Alignment Properties To Parent Container Element](img/015d48ca957a845d122bd02f9c7bdaea.png)

在 CSS 代码中，我们在对齐属性旁边添加了一个`flex-direction: column;`,使内容沿着横轴定位。

这是因为 flexbox 是一维布局模型，这意味着它一次处理一维布局，无论是行还是列。并且，默认情况下，`flex-direction`被设置为`row`，使得内容位置沿着主轴。

通过添加`flex-direction: column;`，我们将默认设置改为垂直对齐。

### 居中图像和内嵌元素

这种方法不仅适用于文本块的居中，就像我们上面看到的那样；我们也可以像锚文本链接一样将图像和内嵌元素居中。

在一个真实的项目中，我们可能希望在其他文本块的中间添加一个图像和一个文本链接。因此，让我们来看看它的标记，并实现 flexbox 方法来使内容居中:

```
<section class="hero">
  <img src="..." alt="" />
  <h1>...</h1>
  <p>...</p>
  <a href="#">...</a>
</section>

```

同样，为了简洁起见，我们删除了[元素的内容(参见 CodeSandbox)](https://codesandbox.io/s/eager-platform-xswpr?file=/index.html:352-374) 。使用相同的 flexbox 属性，我们的内容完全居中，如下所示:

![Centering Objects With Flexbox](img/c76ef51e04ae11bc0f0c3f3e56ea09a2.png)

正如我们所看到的，使用 flexbox，将对象居中是简单且易于管理的。这种方法非常灵活，我们可以在 flex 容器中添加或删除内容，并且在没有额外样式的情况下仍然具有完美的居中效果。

在我们现实项目中的几乎所有情况下，我们将经常使用这种方法来使对象居中。

### Flexbox 浏览器支持

所有现代浏览器都支持 Flexbox，因此我们可以放心地使用它来将对象放在布局的中心。查看当前[浏览器支持这里](https://caniuse.com/flexbox)。

![Flexbox Browser Support](img/83595bf925e5c28dee2742611a115d1d.png)

## 使用 CSS 网格将对象居中

像 flexbox 一样，我们也可以将一个`display: grid;`应用到一个容器元素，这样直接的子元素就变成了网格项。然后，我们可以使用对齐属性使内容在容器中居中。

下面的 CSS 代码使对象在容器中居中:

```
.container {
  display: grid;
  place-content: center;

  /* ... */
}

```

该 CSS 产生与 flexbox 示例相同的[中心对齐(参见 CodeSandbox)](https://codesandbox.io/s/interesting-gates-qhyoh?file=/style.css) 。

这里，我们只使用了两行 CSS。CSS 属性控制内容如何同时对齐和两端对齐。并且，通过分配一个`center`值，项目将在容器内居中对齐。

`place-content`是`align-content`和`justify-content`的简写，意味着我们可以将上面的 CSS 改写成这样:

```
.container {
  display: grid;
  align-content: center;
  justify-content: center;
  /* ... */
}

```

### 使用网格进行双向居中的实例

使用 flexbox 部分中介绍的相同 hero 示例，让我们改为应用以下网格样式:

```
.hero {
  display: grid;
  place-content: center;
  /* ... */
}

```

[然后，我们得到以下输出(参见 CodeSandbox)](https://codesandbox.io/s/thirsty-butterfly-pmovi?file=/style.css) :

![Output With Grid Styles](img/883ea45e50630dc333333b1431d48273.png)

如图所示,**联系我们**按钮拍摄周围内容的宽度。这是因为当 CSS `place-content`属性应用于一个网格容器时，网格项立即采用宽度最大的项的大小。

与 flexbox 方法相比，这种效果带来了差异。现在，根据我们想要的布局，我们可以很容易地决定使用这些方法中的哪一种。

但同样，在大多数情况下，我们将使用 flexbox 来居中对象，因为它可以无缝地产生我们想要的任何居中布局。

### 网格布局浏览器支持

网格不像 flexbox，[对浏览器的支持](https://caniuse.com/css-grid)。

![Grid Browser Support](img/4ca6d4d56cbcb65a1fff18e07987c714.png)

## 结论

现在，我们可以使用现代方法来居中任何类型的对象，而不是通过数十种技术(包括不必要的传统方法)来居中对象。

我们还讲述了现实生活中的例子和最佳实践，因此我们知道如何在我们自己的项目中应用它们。

我希望你喜欢阅读本指南。如果你有问题或贡献，请在评论区分享。最后，努力在网络上分享这个指南。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。