# 如何使用 CSS 滚动快照-日志火箭博客

> 原文：<https://blog.logrocket.com/how-to-use-css-scroll-snap/>

[Nada Rifki Follow](https://blog.logrocket.com/author/rifkinada/) Nada is a JavaScript developer who likes to play with UI components to create interfaces with great UX. She specializes in Vue.js and loves sharing anything and everything that could help her fellow frontend web developers. Nada also dabbles in digital marketing, dance, and Chinese.

# 如何使用 CSS 滚动捕捉

## 2020 年 3 月 9 日 2 分钟阅读 835

只需要 CSS 来定制你的滚动而不是玩 JavaScript 的想法听起来很棒，不是吗？

今天，我将向您展示 CSS Scroll Snap 如何帮助您做到这一点。

那么 CSS Scroll Snap 是干什么的呢？

## 我打赌你已经使用了一些库来控制你的网站的滚动行为。是的，我和其中一些人相处得很糟糕。

其中一些库会降低页面的性能和用户体验。

更不用说，当你用新的设计更新增加更多的复杂性时，处理它们有时产生的所有错误是一种真正的痛苦。

这就是为什么我更喜欢使用这个简单的 CSS 特性，它允许我在页面中获得漂亮的滚动，而不必依赖于 JavaScript 库。

这些属性也帮助我节省了大量时间。

简而言之，CSS 滚动捕捉使滚动捕捉，每次滚动将视口锁定在特定点(您指定的点)。

另一方面，线性滚动根据控制器的速率移动——无论是鼠标、触摸手势还是箭头键。

现在，让我们看看 CSS 滚动捕捉是如何工作的。

如果你想直接进入一个真实的例子，直接进入第三部分，我用几行 CSS 代码构建了 Instagram 转盘。

让我们开始吧。

CSS 滚动捕捉的工作原理

### CSS 滚动捕捉通过应用两个主要属性来工作:`scroll-snap-type`和`scroll-snap-align`。

以下是您需要了解的每一项内容:

第一个是`scroll-snap-type`，应用于父容器。它接受两个参数:捕捉方向和捕捉行为:

*   关于捕捉行为参数:选择`proximity`使捕捉只出现在离用户停止滚动最近的点。

```
.container {
 scroll-snap-type: [ x | y | block | inline | both ] [ mandatory | proximity ];
}
```

另一方面，当用户滚动时，`mandatory`使捕捉发生在你选择的特定位置。

第二个是`scroll-snap-align`，应用于容器的子容器。

*   这是为 x 轴和 y 轴指定捕捉点的地方:

在我们继续之前，重要的是要注意，除非您指定容器的溢出并给它一个固定的高度，否则这些都不起作用。

```
.children {
 scroll-snap-align: [ none | start | end | center ]{1,2};
}
```

此外，如果其中一个子元素的内容比父容器长，就不应该使用 mandatory。如果您这样做，用户将看不到该内容。

我们还需要熟悉两个属性:

`scroll-padding`(应用于父容器)非常类似于 CSS padding 属性，并充当偏移量。你还有`scroll-padding-top`、`scroll-padding-bottom`、`scroll-padding-left`和`scroll-padding-right`。

1.  `scroll-margin`(应用于容器的子容器)也类似于 CSS margin 属性，并作为开始。你还有`scroll-margin-top`、`scroll-margin-bottom`、`scroll-margin-left`和`scroll-margin-right`。
2.  如果你有兴趣了解更多关于所有滚动捕捉属性和未来属性的信息，Mozilla 文档总是一个很好的起点。

如何使用 CSS Scroll Snap 创建类似 Instagram 的旋转木马

### 为了帮助你更好地理解这个概念，我在 [codepen](https://codepen.io/nrifki/pen/LYVygxW) 上做了一个类似 Instagram 的小转盘。

点击此处观看现场演示:

没有描述

> 如你所见，它包括七个不同大小的盒子。

更多来自 LogRocket 的精彩文章:

让我惊讶的是，除了添加下面的 CSS 行来自动使滚动捕捉到每个框之外，我们没有其他事情可做。

* * *

### 仅用 JavaScript 解决这个问题需要大量额外的工作。

* * *

CSS 滚动捕捉支持跨浏览器吗？

```
<style>
.container {
    width: 60vw;
    height: 70vh;
    margin: 15vh auto;
    overflow-x: scroll;
    scroll-snap-type: x mandatory;
    color: white;
    background-color: oldlace;
    display: flex;
    align-items: center;
}

.child {
    margin-left: 0.5rem;
    height: 90%;
    scroll-snap-align: start;
    padding: 1rem;
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
}

.child:nth-child(1n) {
    background-color: darkorchid;
    flex: 0 0 80%;
}

.child:nth-child(2n) {
    background-color: indigo;
    flex: 0 0 60%;
}

.child:nth-child(3n) {
    background-color: navy;
    flex: 0 0 40%;
}

.child:nth-child(4n) {
    background-color: palegreen;
    flex: 0 0 50%;
}

.child:nth-child(5n) {
    background-color: yellow;
    flex: 0 0 80%;
}

.child:nth-child(6n) {
    background-color: orange;
    flex: 0 0 60%;
}

.child:nth-child(7n) {
    background-color: tomato;
    flex: 0 0 80%;
}
</style>
```

好了，现在让我们进入百万美元的问题:跨浏览器支持。

### 正如你在这里看到的，CSS 滚动捕捉功能在现代浏览器中得到了很好的支持。

你会遇到的唯一问题是 Internet Explorer，它需要旧版本的规范，你可以在这里找到。

另外，我建议您将属性`-webkit-overflow-scrolling: touch;`添加到容器中，以支持 iOS 设备。

结论

差不多就是这样！

### 这很简单，但有时这可以节省我几个小时的代码。尝试使用 JavaScript 实现滚动捕捉，并使用不同宽度的每个子元素重新计算捕捉点，您就会明白我的意思了。

如果您有任何问题或意见，请随时在评论中告诉我，或在 Twitter 上 ping 我。我总是回复。

你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

## .

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

[LogRocket](https://lp.logrocket.com/blg/css-signup) is like a DVR for web and mobile apps, recording everything that happens in your web app or site. Instead of guessing why problems happen, you can aggregate and report on key frontend performance metrics, replay user sessions along with application state, log network requests, and automatically surface all errors.

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).