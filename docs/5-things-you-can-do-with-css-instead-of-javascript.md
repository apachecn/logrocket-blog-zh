# 用 CSS 代替 JavaScript 可以做的 5 件事

> 原文：<https://blog.logrocket.com/5-things-you-can-do-with-css-instead-of-javascript/>

我不是第一个声明 CSS 和 JavaScript 之间的界限随着两种语言的发展而变得模糊的人。

从自定义属性(也称为变量)到过滤器、动画或数学运算，CSS 已经采用了许多我们过去在 JavaScript(或流行的 CSS 预处理程序)中所做的事情，并使其对我们可用。

两种语言有不同的用途。随着每一个浏览器版本、功能版本和属性的增加，CSS 正在成为一种非常强大的语言，能够处理我们以前依赖于 JavaScript 的功能。

在本帖中，我们将了解一些你可能没有听说过的 CSS 金块，它们将自然地(并且优雅地！)处理类似于**平滑滚动**、**黑暗模式**、**表单验证**的事情，以及更多以前需要多行 JS 技巧才能工作的特性。

## 平滑滚动

曾经有一段时间，我们不得不依靠 JavaScript(甚至 jQuery)实现来做到这一点，用`window.scrollY`来复制这一行为。多亏了[滚动行为](https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-behavior)属性，那些日子已经一去不复返了。我们现在可以用一行 CSS 代码处理网站上的平滑滚动了！

```
html {
  scroll-behavior: smooth;
}
```

浏览器支持[在 75%](https://caniuse.com/#feat=css-scroll-behavior) 左右，快到了 Edge 76。在下面的代码栏中查看它是如何工作的:

参见 [CodePen](https://codepen.io) 上的 Pen [仅 CSS 平滑滚动](https://codepen.io/imjuangarcia/pen/RwbQLPe)作者胡安·马丁·加西亚( [@imjuangarcia](https://codepen.io/imjuangarcia) )
。

黑暗模式

## 在 [macOS 发布其黑暗模式功能](https://developer.apple.com/design/human-interface-guidelines/macos/visual-design/dark-mode/)和 Safari 实现`prefers-color-scheme`媒体功能后，黑暗模式最近成为热门趋势，这允许我们检测用户是否默认启用了黑暗模式。

您可能会认为黑暗模式是一个要实现的复杂特性，涉及 CSS 变量、每个方案的不同颜色，以及一些 JavaScript 来连接必要的点击事件，以针对网站上的变化。虽然这部分是对的，而且这是目前实现它的标准方式(这是 T2 在我的网站上的做法)，但没必要这么复杂。

魏高[在她的博客](https://dev.wgao19.cc/sun-moon-blending-mode/)上向我们展示了一个聪明的方法，用 CSS 支持的混合模式之一`mix-blend-mode: difference`来达到类似的效果(更像是`reversed mode`)。如果你曾经玩过 [Photoshop 混合模式](https://helpx.adobe.com/photoshop/using/blending-modes.html)，这是同样的事情，但直接在浏览器上。

它的一些优点包括不必指定反转的颜色(混合模式会为您做到这一点)，并且您可以隔离不想更改的元素。它的一些限制是，你没有一个完整的颜色范围，性能可能是一个问题。

目前，[对原生浏览器的支持为 77%加上 13%的前缀](https://caniuse.com/#search=mix-blend-mode)(全球 91%)，Edge 支持将于 76 版推出。

要更深入地了解这种混合模式是如何工作的，请务必查看魏的精彩文章。如果你想做一个有趣的实验，可以看看这个代码笔:

参见 [CodePen](https://codepen.io) 上胡安·马丁·加西亚( [@imjuangarcia](https://codepen.io/imjuangarcia) )
的《笔[【黑暗模式与混合模式:差异](https://codepen.io/imjuangarcia/pen/xxKLMEN)》。

截断文本

这是我个人的最爱之一，因为这是我在开发依赖 CMS 来填充内容的网站时遇到的一个常见问题。文案上的可变长度可能会使你可爱设计的卡片在不同尺寸或分辨率下看起来不一致。戴夫·鲁伯特有一篇关于这个问题的很棒的文章。

## 在过去，我总是不假思索地寻求基于 JavaScript 的解决方案，因为[实现这一结果的大多数 CSS 技术都相当“黑客”](https://css-tricks.com/line-clampin/)。但是当我在[我的网站](https://www.juangarcia.design)中加入一个博客时，我发现了几个 CSS 属性，它们结合在一起，也许能够提供一个本地的、易于实现的解决方案。遇见`text-overflow`和`line-clamp`！

你可以在这里查看代码笔:

参见 [CodePen](https://codepen.io) 上的 Pen [CSS 专用文本截断](https://codepen.io/imjuangarcia/pen/dybdYbB)作者胡安·马丁·加西亚( [@imjuangarcia](https://codepen.io/imjuangarcia) )
。

文本溢出

这是一个被广泛采用的，[完全支持的](https://caniuse.com/#search=text-overflow)纯 CSS 解决方案，用于控制当文本溢出其包含元素时的行为。你可以将它的值设置为`ellipsis`，这样就可以得到 Unicode 字符`…`。到目前为止还不错，但是它的主要限制是，不管文本有多长，你总是会得到一行被截断的文本。因此，这可能是一个完美的适合标题，但不是那么有用的摘录，评论或长篇大论的文本。请参见下面的示例:

这就是`line-clamp`发挥作用的地方。

## 线夹

`line-clamp`属性也不是新的。[戴夫·德桑德罗](https://twitter.com/desandro)大约十年前向我们展示了这项技术[(因此需要旧的 flexbox 实现`display: -webkit-box`和`-webkit-box-orient: vertical`)。](https://dropshado.ws/post/1015351370/webkit-line-clamp)

那这里有什么新鲜事？火狐[在 68 版](https://bugzilla.mozilla.org/show_bug.cgi?id=WebKit-line-clamp)上实现了它，等等……前缀为`-webkit`！现在 Edge 是基于 Chromium 的，我们也可以使用`-webkit`前缀将[浏览器支持提高到 92%](https://caniuse.com/#search=line-clamp) 。

## 这意味着我们现在可以使用三个 CSS 行的组合来截断多行文本，[就像这样](https://codepen.io/imjuangarcia/pen/dybdYbB)。这里唯一的问题是线夹的规范是 [CSS 溢出模块第 3 级](https://drafts.csswg.org/css-overflow-3/#propdef--webkit-line-clamp)的一部分，目前正在编辑的草稿中，这可能意味着规范可能会有一些变化。另一件要考虑的事情是，你不能控制要显示的字符数，这可能会导致一些[不那么方便(但有趣)的截断场景。](https://twitter.com/search?f=tweets&vertical=default&q=karenmcgrane%20truncation%20is%20not&src=typd)但除此之外，CSS 夹紧快乐！

滚动捕捉

CSS 滚动捕捉是 Chrome 用户已经有一段时间的另一个方便的功能，我们现在可以在 Firefox 最新的 68 版本中享受它。我们也将在 76 版的 Edge 上安装它，提升对所有主流浏览器的支持。

你有没有注意到一些花哨的网站会创建全屏部分，并在你滚动时锁定特定位置的视窗？这是这种行为的一个例子。

## 前一阵子让它正常工作是很棘手的，涉及到大量的数学计算和 JavaScript。但是现在，CSS 能够在本地创建类似于[这个](https://codepen.io/imjuangarcia/pen/zYORdMK)的交互。

它的工作方式类似于 Flexbox 或 CSS Grid，因为您需要一个容器元素，在其上设置`scroll-snap-type`和多个设置了`scroll-snap-align`的子元素，如下所示:

HTML:

CSS:

`scroll-snap-type`接受两个不同的值:`mandatory`，它将强制对齐到元素的顶部或底部，或者`proximity`，它将为您计算并在内容足够接近对齐点时进行对齐。

我们可以在父容器上设置的另一个属性是`scroll-padding`，如果您在布局上有固定的元素(比如固定的标题，或者，为什么不呢，一个 cookie 策略通知),这可能会很方便😅)可能会隐藏一些内容。

```
<main class=”parent”>
  <section class=”child”></section>
  <section class=”child”></section>
  <section class=”child”></section>
</main>
```

对于孩子来说，我们现在只有`scroll-snap-align`，它将告诉容器捕捉到哪个点(顶部、中心或底部)。

```
.parent {
  scroll-snap-type: x mandatory;
}

.child {
  scroll-snap-align: start;
}
```

虽然现在你知道这种技术只需要几行 CSS 代码就可以达到这种效果，而且不涉及数学，所以你可能会觉得在你的整个网站上随意添加滚动捕捉很有诱惑力，但请记住网页设计的唯一法则，根据罗宾·伦德尔的说法:**不要弄乱滚动**。这种技术可能对滑块、图片库或页面上的某些特定部分有用，但要谨慎使用，因为它会影响性能和用户的整体体验。

你可以在这里查看代码笔:

在 [CodePen](https://codepen.io) 上查看 Juan martín garcía([@ imjuan Garcia](https://codepen.io/imjuangarcia))
创作的带有滚动捕捉的仅 CSS 水平全屏图库。

粘性导航

对于以前需要大量 JavaScript 数学运算并且实现起来非常昂贵的特性，我们采用了粘性定位。我们以前需要`offsetTop`和`window.scrollY`的地方，现在有了`position: sticky`来为我们完成所有的魔法！具有粘性定位的元素将表现为相对定位的元素，直到到达视口中的给定点，然后变成固定定位的元素。[带`-webkit`前缀](https://caniuse.com/#feat=css-sticky)的浏览器支持度高达 92%。

因此，尽管这听起来像做以下事情一样简单:

要很好地将你的标题设置成粘性的，知道你的 HTML 的结构很重要。例如，如果你的 HTML 结构看起来像这样:

## 标题将只能粘贴在其父标签(在本例中是`<main>`标签)覆盖的区域上。这个“粘性父项”决定了“粘性项”的作用范围。Elad 谢赫特尔在[这篇文章](https://medium.com/@elad/css-position-sticky-how-it-really-works-54cd01dc2d46)中更好地解释了这个问题，而[这里有一个有趣的小实验](https://codepen.io/imjuangarcia/pen/QWLQjqQ)使用这个技术。

你可以在这里查看代码笔:

参见 [CodePen](https://codepen.io) 上胡安·马丁·加西亚( [@imjuangarcia](https://codepen.io/imjuangarcia) )
的[使用粘性定位](https://codepen.io/imjuangarcia/pen/QWLQjqQ)的 Pen【CSS Race】。

```
header {
  position: sticky;
  top: 0;
}
```

To nicely set your header to be sticky, it’s important to know that the structure of your HTML matters! So for example, if your HTML structure looks like this:

```
<main>
  <header>
    <h1>This is my sticky header!</h1>
  </header>
  <section>This is my content</section>
</main>
```

不管是 CSS 还是 JS，还是要监控的。使用 [LogRocket](https://logrocket.com/signup/) 来确保你的前端运行正常。

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产中所有用户的客户端 CPU 使用、内存使用等感兴趣，请尝试 LogRocket 。[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://logrocket.com/signup/)[https://logrocket.com/signup/](https://logrocket.com/signup/)

LogRocket 就像是网络应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 应用的方式— [开始免费监控](https://logrocket.com/signup/)。

## 总结奖励:@支持媒体规则

即使这些 CSS 特性被广泛采用和支持，如上所述，你可能还是想在添加它们之前检查一下它们[在浏览器](https://caniuse.com/)上是否可用。如果是这种情况，你可以使用`@supports`特性查询，这是被浏览器广泛采用的[，它允许你在应用一组样式之前测试浏览器是否支持特定的`property: value`对。语法如下所示:](https://caniuse.com/#feat=css-featurequeries)

使用这个方便的特性查询，您可以在支持它们的浏览器上应用一组样式。该语法听起来可能很熟悉，因为这正是我们编写媒体查询的方式，并且是一种很好的方式来进行所谓的*渐进增强，*现在在拥有这些顶级功能的浏览器上使用这些功能，同时防止在没有这些功能的浏览器上出现不一致的行为。Jen Simmons 在她的[弹性 CSS](https://youtu.be/T8uxmUQZsck) 视频中对此做了更好的解释。

原来如此！希望这些最先进的 CSS 特性能够帮助您发布一个更小的 JavaScript 包，同时仍然能够创建有趣和引人注目的 ui。此外，也可以随意再次检查这些[代码笔](https://codepen.io/collection/DrGkMr)。

Even though these CSS features are widely adopted and supported, as discussed above, you might still want to check if they [are available on the browser](https://caniuse.com/) before adding them. If that’s the case, you can use the `@supports` feature query, which is [widely adopted by browsers](https://caniuse.com/#feat=css-featurequeries) and allows you to test if the browser supports a specific `property: value` pair before applying a set of styles. The syntax looks as follow:

```
@supports (initial-letter: 4) {
  p::first-letter {
    initial-letter: 4;
  }
}
```

With this handy feature query, you can apply a set of styles just on those browsers that support them. The syntax might sound familiar since it’s exactly how we write media queries and is a great way to do what’s called *progressive enhancement,* using these top-notch features right now on browsers that have them, while preventing inconsistent behavior on browsers that don’t have them. Jen Simmons explains this better on her [Resilient CSS](https://youtu.be/T8uxmUQZsck) videos.

So that’s it! Hopefully, these state-of-the-art CSS features will help you ship a smaller JavaScript bundle, while still creating interesting and compelling UIs. Also, feel free to check out these [Codepens](https://codepen.io/collection/DrGkMr) again as well.