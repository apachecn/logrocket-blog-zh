# 如何用 CSS 制作 SVG 动画:示例教程

> 原文：<https://blog.logrocket.com/how-to-animate-svg-css-tutorial-examples/>

***编者按**:本文最后更新于 2022 年 7 月 12 日，以包括额外的例子。*

网页动画是一种乐趣，它通过提供视觉反馈、引导用户完成任务以及使网站整体变得生动活泼来改善用户体验。有几种方法可以创建网页动画，包括使用 JavaScript 库、gif、嵌入式视频和 CSS。

与庞大的 gif 和视频相比，使用 SVG 和 CSS 添加到网站的动画具有更快的加载时间。您还可以制作简单的动画，而不必向网站的页面加载添加另一个 JavaScript 库。

在这篇文章中，我们将学习如何使用 SVG 和 CSS 创建轻量级、可伸缩的动画。尽管我们将在演示中使用 Sass，CSS 也可以。我们开始吧！

 [https://www.youtube.com/embed/eZT-OrhxiMY?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/eZT-OrhxiMY?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

## 用 CSS 制作 SVG 动画的常见用例

在开始编写代码之前，让我们回顾一下使用动画 SVG 的一些实用方法。

### 核标准情报中心

动画 SVG 非常适合表示微交互和状态变化的图标。它们在引导用户进行下一步操作时也很有帮助，比如在入职培训中。常见的用例包括加载、上传、菜单切换以及播放和暂停视频。

### 插图

插图是另一个常见的用例。它们可以作为空白状态包含在产品中，演示如何在仪表板上生成数据。其他流行的用例包括动画表情符号和贴纸。还有动画插画，有助于点亮登录页面。

## 如何为动画准备 SVG

因为从简化的 SVG 代码开始更容易，所以制作 SVG 动画的第一部分就是准备它们。您可以按照以下步骤来完成:

### 优化 SVG 代码

当创建一个 SVG 时，它通常会有一些额外的、不必要的代码，所以优化它是很重要的。您可以使用类似于 [SVGOMG](https://jakearchibald.github.io/svgomg/) 的工具来减小文件大小并删除任何不必要的标签和元数据。

### 创建有意的分组

如果需要，您可以通过在代码编辑器中打开 SVG 并记下用于对 SVG 元素进行分组的`<g>`元素来创建有意的分组。如果你想将一组元素制作成动画，将它们包装在`<g></g>`中，并用类或 ID 命名。

如果我们想以同样的方式设计多个路径，我们也可以考虑将 ID 名转换成类名。

### 注意堆叠顺序

这似乎与直觉相反，但是最后列出的形状将被粘贴到前面提到的形状上。如果我们希望一个形状出现在背景中，我们必须确保它列在 SVG 代码的顶部。SVG 形状是按照从上到下的顺序绘制的。

### 将 SVG 样式设置为首选的初始状态

SVG 有类似于 CSS 样式的[表示属性](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/Presentation)，但是直接在 SVG 上设置。一个常见的例子是`fill`颜色。由于这些样式是在 SVG 上设置的，您可能会认为它们对浏览器有很大的影响。事实证明，我们在外部设置的任何 CSS 或 Sass 都会自然地覆盖 SVG 样式，而不需要使用`!important`声明。

但是，请注意 SVG 上的设置，以便为页面加载期间显示的内容做好准备。例如，在应用 CSS 样式之前，一个缓慢加载的页面会显示 SVG 的动画。我建议你在宽度和高度上离开，以避免这一点；Sara Soueidan 在关于这个主题的博客文章中很好地解释了无样式 SVG 的闪现。

## 将 CSS 应用于 SVG

现在我们已经整理好了 SVG，让我们探索一些添加 CSS 的选项。

在将 CSS 应用于 SVG 时，有一些注意事项。一个限制是我们不能使用外部样式表对外部链接的 SVG 应用样式。

在 HTML 中内嵌 SVG 代码使得 SVG 元素及其内容成为文档 DOM 树的一部分，因此它们受到文档 CSS 的影响。这种方法是我最喜欢的，因为它将样式与标记分开。

在回顾下面的其他选项时，我们会发现它们都是交织在一起的。如果您使用 Rails，有一些 gem 可以自动将 SVG 嵌入到视图中。因此，在您的代码中，您可以简单地引用外部 SVG，然后它会在编译时被嵌入。这种方法的另一个好处是内联 SVG 意味着少了一个 HTTP 请求，从而提高了性能:

我们可以在`<style>`标签中添加 CSS 样式，嵌套在`<svg>`标签中:

参见 [CodePen](https://codepen.io) 上卢克·图比尼斯([@卢克·格罗基特](https://codepen.io/lukelogrocket) )
的 Pen [SVG–2](https://codepen.io/lukelogrocket/pen/axaeWO)。

如果您想保留 SVG 中引用的样式，但实际上并不包含在 SVG 中，那么您可以使用`<?xml-stylesheet>`标记链接到 SVG 中的外部样式表:

参见 [CodePen](https://codepen.io) 上卢克·图比尼斯([@卢克·格罗基特](https://codepen.io/lukelogrocket) )
的 Pen [SVG–3](https://codepen.io/lukelogrocket/pen/qwMejZ)。

css 也可以使用内联样式属性在 lemon 上设置

参见 [CodePen](https://codepen.io) 上卢克·图比尼斯([@卢克·格罗基特](https://codepen.io/lukelogrocket) )
的 Pen [SVG–4](https://codepen.io/lukelogrocket/pen/EJeqva)。

用 CSS 可以制作什么动画？

## 你可以用 CSS 制作很多动画。首先，您可以使用 CSS 动画或 CSS 过渡来制作 CSS 属性的动画，其值可以随时间变化[。要获得这些属性的完整列表，请查看 MDN Web 文档中的](https://blog.logrocket.com/css-transitions-animating-hamburger-menu-button/)[动画 CSS 属性列表](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animated_properties)。为了激发灵感，让我们回顾几个演示。

我们将讨论两种主要类型的动画，它们的区别在于它们提供的控制量。

旋转装载机

### 我们将创建的第一个 SVG 动画是一个旋转加载器，就像我们通常在应用程序的加载屏幕上看到的那样。我们首先设置 SVG，它是一个带有深色象限的环。

我们给 SVG 一个 ID`loading-spinner`，然后定义动画和过渡。动画引用了`@keyframes`的名字，其中`transform: rotate`被设置为从`0`度到`360`度，一个完整的旋转。这就是让这个旋转器活起来所需要的一切！

厚颜无耻

```
#loading
  animation: loading-spinner 1s linear infinite

@keyframes loading-spinner
  from
    transform: rotate(0deg)
  to
    transform: rotate(360deg)

```

### 想要更光滑的东西？SVG 支持渐变，因此您可以使用相同的 Sass 实现更平滑的效果，但是 SVG 对环应用了渐变，请参见下面的定义`#spinner-gradient-a`:

参见 [CodePen](https://codepen.io) 上 Hope Armstrong([@ Hope Armstrong](https://codepen.io/hopearmstrong))
用 CSS / Sass 制作的带渐变的旋转加载 SVG 图标的笔。

See the Pen [Rotating Loading SVG Icon with Gradient Animated with CSS / Sass](https://codepen.io/hopearmstrong/pen/yrymwg) by Hope Armstrong ([@hopearmstrong](https://codepen.io/hopearmstrong))
on [CodePen](https://codepen.io).

现在，让我们用`transform: scale`来创建这个变形栏加载图标。

请看 [CodePen](https://codepen.io) 上霍普阿姆斯特朗([@霍普阿姆斯特朗](https://codepen.io/hopearmstrong) )
用 CSS / Sass 制作的 Pen [SVG 加载栏动画。](https://codepen.io/hopearmstrong/pen/VNYoNq)

See the Pen [SVG Loading Bars Animated with CSS / Sass](https://codepen.io/hopearmstrong/pen/VNYoNq) by Hope Armstrong ([@hopearmstrong](https://codepen.io/hopearmstrong))
on [CodePen](https://codepen.io).

SVG 由三个大小相等、间隔均匀的矩形组成。对于 SVG 和所有三个`<rect>`元素，已经为每个元素添加了 id，因此它们可以很容易地被 Sass 定位。

超文本标记语言

### Sass 将动画应用于每个条形。关键帧告诉小节在时间线中的四个位置沿 Y 轴改变缩放:开始时、四分之一处、中间以及四分之三处。

动画中的第一个数字表示动画长度，而第二个数字设置延迟。因为我想让这些条形在不同的时间改变大小，所以我为每个条形添加了不同的延迟:

注意`transform-origin: center`告诉变换从条的中心开始缩放；否则，它将自上而下扩展，看起来就像钢筋钻入了地下。测试一下，你就会明白我的意思。

```
#loading-bar
  &-left
    animation: loading-bar-morph 1s linear .1s infinite
    transform-origin: center
  &-middle
    animation: loading-bar-morph 1s linear .2s infinite
    transform-origin: center
  &-right
    animation: loading-bar-morph 1s linear .4s infinite
    transform-origin: center

@keyframes loading-bar-morph 
  0%
    transform: scaleY(1)
  25%
    transform: scaleY(0.3)
  50%
    transform: scaleY(0.7)
  75%
    transform: scaleY(0.15)

```

默认情况下，SVG 位于左上角的`(0, 0)`点。如果您习惯于使用 HTML 元素，这是一个关键的区别，HTML 元素的默认转换起点总是在`(50%, 50%)`。

线条画动画

### 我们可以添加一个画线效果，让 SVG 看起来就像正在被绘制一样。因为它依赖于笔画，所以它需要一个带线条的 SVG。我将向您介绍如何完成一行代码，然后您将知道如何完成其余部分。

更多来自 LogRocket 的精彩文章:

* * *

### 首先，用`stroke-dasharray`给线条加上虚线。该数字以像素为单位表示虚线的长度。你会希望它是线的长度:

* * *

然后，添加`stroke-dashoffset`以沿线重新定位破折号。使其与线条本身一样长，这样看起来就像一条实线。动画的最终帧看起来是这样的:

```
#line
  stroke-dasharray: 497

```

现在，它可以被制作成动画了。让我们添加激活`stroke-dashoffset`的关键帧，使其从没有可见笔画的完全偏移变为实心笔画的`0px`偏移。注意动画属性中的`forwards`。

```
#line
  stroke-dasharray: 497
  stroke-dashoffset: 497

```

这是一个`animation-fill-mode`命令，告诉动画在播放后保持最终状态。没有它，动画将返回到它的第一帧，作为它的最终静止点:

动画插图

```
#line
  stroke-dasharray: 497
  stroke-dashoffset: 497
  animation: draw 1400ms ease-in-out 4ms forwards

@keyframes draw
  from
    stroke-dashoffset: 1000
  to
    stroke-dashoffset: 0

```

### 让我们使用 [Animista](http://animista.net/) 的心跳动画来演示动画插图有脉冲效果。Animista 是预制 CSS 动画效果的一个很好的资源，您可以重复使用和迭代。

对于这颗跳动的心脏，悬停时会触发一些动画。心脏有 110%的比例变化，眼睛变小，嘴变大，脸红，心脏跳动:

```
#smiley-love
  #smiley
    &-blush
      display: none
  a
    display: inline-block
    &:hover
      #smiley
        transform: scale(1.1)
        transform-origin: center
        -webkit-animation: heartbeat 1.5s ease-in-out infinite both
        animation: heartbeat 1.5s ease-in-out infinite both
        &-blush
          display: inherit
        &-eye-left
          transform-origin: center
          transform: scale(.7) translate(-8px)
        &-eye-right
          transform-origin: center
          transform: scale(.7) translate(8px)
        &-mouth
          transform: translateY(-22px) scale(1.6)
          transform-origin: center

/* ----------------------------------------------
 * animation heartbeat
 * Generated by Animista on 2019-3-24 18:51:13
 * w: http://animista.net, t: @cssanimista
 * ---------------------------------------------- */

@-webkit-keyframes heartbeat
  from
    -webkit-transform: scale(1)
            transform: scale(1)
    -webkit-transform-origin: center center
            transform-origin: center center
    -webkit-animation-timing-function: ease-out
            animation-timing-function: ease-out
  10%
    -webkit-transform: scale(0.91)
            transform: scale(0.91)
    -webkit-animation-timing-function: ease-in
            animation-timing-function: ease-in
  17%
    -webkit-transform: scale(0.98)
            transform: scale(0.98)
    -webkit-animation-timing-function: ease-out
            animation-timing-function: ease-out
  33%
    -webkit-transform: scale(0.87)
            transform: scale(0.87)
    -webkit-animation-timing-function: ease-in
            animation-timing-function: ease-in
  45%
    -webkit-transform: scale(1)
            transform: scale(1)
    -webkit-animation-timing-function: ease-out
            animation-timing-function: ease-out
@keyframes heartbeat
  from
    -webkit-transform: scale(1)
            transform: scale(1)
    -webkit-transform-origin: center center
            transform-origin: center center
    -webkit-animation-timing-function: ease-out
            animation-timing-function: ease-out
  10%
    -webkit-transform: scale(0.91)
            transform: scale(0.91)
    -webkit-animation-timing-function: ease-in
            animation-timing-function: ease-in
  17%
    -webkit-transform: scale(0.98)
            transform: scale(0.98)
    -webkit-animation-timing-function: ease-out
            animation-timing-function: ease-out
  33%
    -webkit-transform: scale(0.87)
            transform: scale(0.87)
    -webkit-animation-timing-function: ease-in
            animation-timing-function: ease-in
  45%
    -webkit-transform: scale(1)
            transform: scale(1)
    -webkit-animation-timing-function: ease-out
            animation-timing-function: ease-out

```

我们再做一个。在这个例子中，我使用`transform: translate`改变了冰棒图像上的水滴的位置，从而制作了它们的动画。为了让它们消失，我制作了`opacity`的动画。现在，它看起来像是一个炎热的夏天！

参见 [CodePen](https://codepen.io) 上霍普阿姆斯特朗([@霍普阿姆斯特朗](https://codepen.io/hopearmstrong) )
用 CSS / Sass 制作的钢笔 [融化冰棍 SVG 动画。](https://codepen.io/hopearmstrong/pen/qwdBwR)

See the Pen [Melting Popsicle SVG Animated with CSS / Sass](https://codepen.io/hopearmstrong/pen/qwdBwR) by Hope Armstrong ([@hopearmstrong](https://codepen.io/hopearmstrong))
on [CodePen](https://codepen.io).

让我们使用 SVGs 创建一个汉堡菜单；当用户悬停在菜单上时，动画将被触发:

请看 [CodePen](https://codepen.io) 上 Emadamerho Nefe([@ Nefe James](https://codepen.io/nefejames))
的笔 [动画 SVG 汉堡菜单](https://codepen.io/nefejames/pen/qBojWvj)。

See the Pen [Animated SVG Hamburger Menu](https://codepen.io/nefejames/pen/qBojWvj) by Emadamerho Nefe ([@nefejames](https://codepen.io/nefejames))
on [CodePen](https://codepen.io).

我们来分解一下上面的代码。首先，我们用`rect`元素建立了一个汉堡包 SVG，并画了三个矩形。我们分别给了三个`rect`元素类`top`、`middle`、`bottom`。

动画的核心发生在悬停上。当用户悬停时，我们改变矩形的位置，并将`top`和`bottom`矩形分别旋转 45%和-45%。我们还将`middle`矩形的不透明度设置为`0`。

淡入和淡出文本

### 另一个很酷的动画是淡入淡出文本动画:

请看 [CodePen](https://codepen.io) 上 Emadamerho Nefe([@ Nefe James](https://codepen.io/nefejames))
的钢笔 [淡入 SVG 文本](https://codepen.io/nefejames/pen/VwXWwPB)。

See the Pen [Fade-in SVG Text](https://codepen.io/nefejames/pen/VwXWwPB) by Emadamerho Nefe ([@nefejames](https://codepen.io/nefejames))
on [CodePen](https://codepen.io).

这里，我们使用`text`元素建立了一个基于文本的 SVG，定义了一个将在`0`和`1`之间切换文本不透明度的`fadeIn`动画，并将`fadeIn`动画应用于 SVG。

Wavy SVG text

### 最后一个演示是一个波浪文本动画，其中世界上的每个字母都将上下起伏，看起来像波浪的运动。我们也可以称这个动画为有弹性的文本动画。

请看 [CodePen](https://codepen.io) 上 Emadamerho Nefe([@ Nefe James](https://codepen.io/nefejames))
的笔 [波浪 SVG 文字动画](https://codepen.io/nefejames/pen/poLwBoR)。

See the Pen [Wavy SVG Text Animation](https://codepen.io/nefejames/pen/poLwBoR) by Emadamerho Nefe ([@nefejames](https://codepen.io/nefejames))
on [CodePen](https://codepen.io).

对于 SVG，我们使用`text`元素来定义单词 wavy 的每个字母。我们用`translateY`将字母按下`3px`。稍后，我们将使它作为波动的一部分弹出。为了完成这个效果，我们定义了一个名为`wavyText`的动画，我们延迟了每个字母的动画，使它们一个接一个地弹出。

虽然我们可以用 CSS 制作 SVG 动画，但我们也可以使用其他工具来处理和创建动画。SVG 动画可能有点复杂，但是下面的工具让我们制作 SVG 动画变得非常简单。

结论

## 现在你已经知道了用 CSS 制作 SVG 动画的几种不同方法，我希望你能从中受到启发，为 web 创建自己的动画！只用几行 CSS 就把静态 SVG 变得生动有趣。

一旦你掌握了一些技巧，处理更复杂的动画就更容易了。网上和像 [CodePen](https://codepen.io/) 这样的网站上有无穷无尽的灵感。快乐动画！

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).