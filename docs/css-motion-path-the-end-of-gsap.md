# CSS 运动路径:GSAP 的终结？

> 原文：<https://blog.logrocket.com/css-motion-path-the-end-of-gsap/>

CSS 动画曾经相当有限。

处理除了非常基本的效果之外的任何东西通常意味着冗长而复杂的声明。

此外，通过一条路径移动元素需要熟练地使用同步平移和旋转:这正是浏览器差异会变成噩梦的那种事情。

为了解决这个问题，自从网络动画出现以来，开发人员一直在转向 JS 解决方案(或者至少，自从我们决定放弃 Flash 以来，但我喜欢假装 Flash 从未发生过)。)

过去，移动东西是好的 ol' jQuery 的一个很好的用途，但是在紧要关头事情可能会变得资源密集型。

然后 GSAP 出来了，给了我们无限的动画力量和更好的性能，这很快就变成了行业标准。

但是 CSS 也一直在进步，最近发布的 Firefox 72 和 Chromium-powered Edge 意味着我们可以开始实施 [CSS 运动路径规范](https://drafts.fxtf.org/motion-1/)来解决这种情况。

但这是否意味着我们可以摆脱纯粹的 CSS 动画，抛弃 GSAP？

## 通过路径定位元素

运动路径模块的核心是`offset-path`属性。它采用一个`path()`函数作为它的值，允许我们为要定位的元素定义一个 SVG 路径。

```
.container{
  offset-path: path('M 0 100 L 200 150 L 300 150');
}
```

如果您曾经使用过 CSS clip-path，这应该看起来很熟悉。本质上，它定义了直线经过的点以及到达这些点的不同方式。

接下来，`offset-distance`属性允许我们定义元素在它的`offset-path`上的位置。它可以带任何 CSS `<length>`单位(`%`、`px`等)。

在大多数情况下，使用百分比将是更好的方法。

例如，下面的代码将把一个元素放在它的`offset-path`的正中间。

```
.element{
  offset-distance: 50%;
}
```

下面是一个在整个路径中放置了各种元素的示例:

> 没有描述

使用动画路径

尽管顾名思义，在使用运动路径模块本身的属性时并不涉及运动:该部分仍然是通过转场、CSS 动画或 Web 动画 API 来处理不同的运动路径属性。

## 因此，要真正移动路径上的元素，我们可以使用一个移动`offset-distance`的`@keyframes`声明。

dot { offset-path:path(' M 0 0 Q 150 500 300 0 ')；动画:移动 4s 三次-贝塞尔(0.78，0，0.17，0.99)无限交替；} @关键帧移动{ 100%{偏移距离:100%；} } .dot{背景:浅蓝色；宽度:50px 高度:50px 显示器:flex 对齐-项目:居中；justify-content:居中；边框:2px 纯色 slateblue 边框半径:5px} section{ position:相对；宽度:300px 高度:300px 边距:自动；} svg { position:绝对；笔画宽度:2px} body{背景:Azure 显示器:flex 伸缩方向:列；对齐-项目:居中；justify-content:居中；最小高度:100vh 溢出-x:隐藏；} *{框大小:边框-框；}

Therefore, to actually move the element on the path we can use a `@keyframes` declaration that shifts the `offset-distance`.

```
.element{
  offset-path: path('M0,0 C40,160 60,160 100,0');
  animation: move 2000ms;
}
@keyframes move {
  100% {
    offset-distance: 100%;
  }
}
```

控制元素的旋转

我们还可以使用`offset-rotate`属性来控制元素在经过其定义的路径时应该如何旋转。

## 更多来自 LogRocket 的精彩文章:

默认的`auto`值将确保元素总是面向路径的方向，根据需要自动旋转。如果我们想通过面向给定方向的路径定位元素，我们可以使用 CSS `<degree>`值来代替。

* * *

### 下面的代码将确保元素在通过路径时保持其原始方向。

* * *

类似于我们对`offset-position`所做的，我们可以在整个关键帧声明中控制`offset-rotate`，并相应地调整元素方向。

请记住，为了让旋转平滑过渡，我们必须在角度中声明所有的旋转；从一个角度改变到`auto`会导致跳动。

```
.element{
  offset-rotate: 0deg;
}
```

将两者结合起来，我们可以得到如下结果:

移动元素{ offset-path:path(' M 0 100 L 200 150 L 300 150 ')；动画:移动 4s 易进难出无限；} @关键帧移动{ 0% { offset-rotate:15 deg；} 30%{偏移-旋转:15 度；} 60%{偏移-旋转:-20 度；} 100%{偏移距离:100%；偏移-旋转:0 度；} } .移动元素{背景:浅蓝色；宽度:50px 高度:20px 显示器:flex 对齐-项目:居中；justify-content:居中；边框:2px 纯色 slateblue 边框半径:5px} section{ position:相对；宽度:300px 高度:300px 边距:自动；} svg { position:绝对；笔画宽度:2px} body{背景:Azure 溢出-x:隐藏；} *{框大小:边框-框；}

Combining both, we can get results as follows:

使用`:hover`和转场制作动画

记住，我们也可以使用简单的过渡来制作运动路径属性的动画

## 在下面的示例中，所有元素都从其路径的起点开始定位，并在悬停时显示动画。

部分:悬停>。dot{偏移距离:100%；偏移-旋转:360 度；} .dot:n-child(1){ offset-path:path(' M 50 50 Q 0 50 0 ')；} .dot:n-child(2){ offset-path:path(' M 50 50 Q 50 0 100 0 ')；} .dot:n-child(3){ offset-path:path(' M 50 50 Q 100 50 100 100 ')；} .dot:n-child(4){ offset-path:path(' M 50 50 Q 50 100 0

```
.element:hover{
  offset-distance: 100%;
  offset-rotate: 360deg;
}
```

通过路径环绕文本

> 如果我们尝试将运动路径属性应用于文本元素，我们会看到整个文本块被视为一个整体。如果我们想要的是文本环绕路径(并有可能通过它被动画化)，我们需要让每个字母表现为一个单独的元素。

第一种方法实际上是用一个实用程序分割文本，比如 [splitting.js](https://splitting.js.org/) 。虽然这样做很好，但是它会污染 dom，给每个字母加上一个`<span>`，并导致屏幕阅读器拼出单词。

屏幕阅读器友好的选项是[使用带有 textPath 元素](https://www.youtube.com/watch?v=Tae96ze3xwY)的实际 SVG 文本。

## 添加外部样式表/笔此处添加的任何 URL 都将按顺序添加，并放在编辑器中的 CSS 之前。通过使用其 URL 和适当的 URL 扩展名，您可以使用另一个笔中的 CSS。JavaScript 预处理器巴贝尔包括 JSX 处理。

为路径本身制作动画

就像我们用`clip-path`做的有趣的事情一样，`offset-path`的路径声明可以被动画化。

为此，我们必须确保动画的每一步都有相同数量的节点，以便浏览器能够在它们之间平滑过渡。

> 如果我们在任何一个步骤中提供不同数量的节点，浏览器将无法猜测中间的节点，而只是简单地从一个步骤跳到下一个步骤，而不进行转换。

dot { offset-path:path(' M 0 100 Q 100 0 200 100 Q 300 200 400 100 ')；位置:绝对；偏移-旋转:0 度；动画:移动 3s 缓入缓出无限交替；} @关键帧移动{ 100% { offset-path:path(' M 0 100 Q 100 200 200 100 Q 300 0 400 100 ')}。点:第 n 种类型(2) {偏移距离:20%；

即将推出的功能和改进

浏览器当前对`offset-path`的实现只允许我们为后面的元素声明一个`path()`函数。根据规范的工作草案，我们应该也能使用`<basic-shape>`(如圆形、多边形等)。所以我们可以期待在不久的将来。

它还允许我们使用一个 SVG 路径 id 作为值(例如`offset-path: url(#my-path)`)，这将帮助我们在 HTML 中绘制一个动画路径，并让它相应地缩放。

> 还有一个额外的属性，目前只有 Firefox 实现了:`offset-anchor`属性允许我们定义元素相对于其偏移路径的锚点。

它的默认设置是`50 %/50 %`，这意味着元素在路径上居中。通过改变`offset-anchor`，我们可以操纵元素的哪一部分固定在路径上，类似于我们对`transform-origin`所做的。

关于可达性的一句话

## 我将是第一个承认到处使用这个新工具的人，但是动画应该被有目的地和负责任地使用。

动画可能会引发前庭障碍患者的恶心，让任何有注意力障碍的人分心，或者只是让一些可能喜欢禁用它的用户感到讨厌。

因此，考虑在[偏好减少运动](https://blog.logrocket.com/new-in-chrome-74-prefers-reduced-motion-media-query-50cd89d3e769/)媒体查询中实现这一点，以保证这些用户的安全。

结论

运动路径模块刚刚使 CSS 动画强大了一百倍，我们刚刚开始发现许多用例。

## 但它仍然有一些缺点:首先，为了解决房间里的大象，Safari 还不支持该规范，这可能是兼容性的一个障碍。

它也没有改变 CSS 动画本身的怪癖，例如编写和维护关键帧的复杂性(特别是当我们需要将它们链接在一起并同步时)，以及它们的缓动功能对两个贝塞尔处理程序的限制，这使得像反弹效果这样的东西比在支持多个贝塞尔函数的外部库中更难创建和维护。

因此，GSAP 将会留在这里，至少会持续一段时间。

在我看来，运动路径所做的就是将动画从 CSS 切换到 JS 的门槛移动了很多。

```
.element{
  offset-path: path('M0,0 C40,160 60,160 100,0');
  animation: move 2s ease-out;
}

@keyframes move {
  100% {
    offset-distance: 100%;
  }
}

@media screen and (prefers-reduced-motion: reduce){
  .element{
    animation-duration: 1ms; /* takes it immediately to the ending position */
  }
}
```

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

What Motion Path does, in my opinion, is moving the threshold at which we would switch from CSS to JS for animations quite a bit.

## Is your frontend hogging your users' CPU?

As web frontends get increasingly complex, resource-greedy features demand more and more from the browser. If you’re interested in monitoring and tracking client-side CPU usage, memory usage, and more for all of your users in production,

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

[LogRocket](https://lp.logrocket.com/blg/css-signup) is like a DVR for web and mobile apps, recording everything that happens in your web app or site. Instead of guessing why problems happen, you can aggregate and report on key frontend performance metrics, replay user sessions along with application state, log network requests, and automatically surface all errors.

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).