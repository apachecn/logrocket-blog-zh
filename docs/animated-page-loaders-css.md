# 如何在 CSS - LogRocket 博客中构建动画页面加载器

> 原文：<https://blog.logrocket.com/animated-page-loaders-css/>

动画页面加载器帮助用户对工具或网站有耐心。它让他们知道系统没有崩溃，告诉他们加载页面需要多长时间，并通过提供一些有趣的内容来吸引他们的注意力。最精彩的部分？用一些基本的网页设计技巧，用 CSS 创建可下载的动画是很容易的。

如今，在网站上看到动画加载器是很常见的，尤其是随着 React web 应用程序和网站的增长。这是我们可以提高应用程序感知性能的许多方法之一，或者换句话说，让网站看起来比实际加载得更快。

在本文中，我将讨论动画页面加载器的好处，然后用 CSS 构建一个简单的转轮，您可以用它作为构建更复杂、信息量更大的加载器的起点。

## 什么是页面加载器？

页面加载器是一个网页组件，当您按下链接或按钮时，它提供加载页面覆盖。您可以添加不同类型的页面加载器，如微调器、加载栏和沙漏动画。

页面加载器还起到通知的作用，向用户保证系统仍在处理他们的请求。动画页面加载器通常是简单的动画，用于在服务器进行处理时娱乐客人。

它们是 web 页面的基本元素，在开发过程中不应该被忽略，因为如果用户想知道他们的请求是否被处理，他们会感到沮丧。

## 为什么要在网站上使用页面加载器？

不可避免地，网站或应用程序中的某些元素需要加载时间。没有人喜欢等待，所以你必须让你的用户在那几毫秒内参与进来。

页面加载器是感知性能不可或缺的一部分，或者说，不管实际等待时间有多长，感觉像是在加载。这是因为在用户等待的时候，它们会让用户分心，让用户感觉时间过得很快。

页面加载器还可以指出用户需要等待的原因。这可以减少挫折感，因为它给你的用户一个可以理解的不方便的原因。您不必过于精确，但简单的陈述，如“请等待，我们正在加载您的内容”或“我们正在下载您的文档”就可以了。

最后，页面加载器可以通过估计还剩下多少等待时间来减少用户的挫折感。时间管理设定期望值，帮助用户耐心等待。您可以将此评分显示为百分比或进度的可视表示。

## 为什么 CSS 是页面加载器的最佳选择？

创建页面加载器时使用 CSS 有几个原因。

首先，它很容易编辑。您可以快速调整时间、颜色、速度和其他动画功能。

第二，用 CSS 制作动画比用 JavaScript 更快更流畅。这是因为 CSS 将动画逻辑卸载到浏览器，而 JavaScript 动画的速度取决于与它一起使用的库。

## 用 CSS 创建一个基本的动画页面加载器

虽然构建最漂亮、最酷的页面加载器很诱人，但我们只用 CSS 就能做得很好。在本教程中，我们将构建[这个旋转的圆形页面加载器](https://codepen.io/sharur7/pen/mdBbrOd)。

让我们开始吧。在 HTML 文件中，添加一个 div 并将类名命名为`"loader"`:

```
<html>
<body>
  <div class="loader"></div>
 </body>
</html>

```

现在，使用 CSS 类选择器`.loader`来设计你的 CSS 加载器动画。您可以定义多个属性，如颜色、大小和对齐方式:

```
body{
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: white;
}

.loader{
  width: 100px;
  height: 100px;
  border-radius: 100px;
  border-top: 5px solid red;
  border-left: 3px solid brown;
  border-bottom: 1px solid purple;
  border-right: transparent;
  animation: spinner 0.8s linear infinite;
}

```

在上面的代码中，我们将加载器的`height`和`width`指定为 100px。因为我们在做一个圆形的旋转器，所以`border-radius`是 100 像素。

`border-top`、`border-bottom`和`border-left`属性决定了我们的加载器的大小、样式和颜色。

设置`border-bottom`属性将创建一个单独的、旋转的、带状的动画。改变`border-right`或`-left`属性将改变色带的长度和大小。

最后，我们得到了加载器的动画，它决定了加载器的名称、持续时间、计时和迭代。在我们的示例中，制作了微调器动画，使其在每个循环中从开始到结束以相同的速度移动 0.8 秒，无限期:

```
> animation: spinner 0.8s linear infinite;

```

最后要做的是设置动画的关键帧。这些将显示加载器在动画序列中的给定时间将如何渲染:

```
@keyframes spinner{
  0%{
    transform: rotate(0deg);
  }

  100%{
    transform: rotate(360deg);
  }
}

```

在我们的示例中定义了两个关键帧。第一个发生在`0%`，这是动画序列的第一部分。然后，装载机被设置为旋转零度。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

第二个关键帧出现在`100%`，这是动画序列的最后一部分。

然后将加载器设置为旋转 360 度，以便微调器的动画从圆的顶部开始，并完成一个完整的旋转以形成一个 0.8 秒的圆

恭喜你！你现在知道了如何只用 HTML 和 CSS 来构建一个简单但加载效果极佳的页面加载器。

## 页面加载器的更多例子

### 显示持续时间

加载动画的持续时间决定了页面加载或获取请求所需的时间。这些可能会提供一个具体的百分比，加载时间，或数量。它们也可以被可视化地表现出来，比如一个正在完成的圆或者一个正在填满的条。

参见 [CodePen](https://codepen.io) 上 Sharvari Raut([@ sharur 7](https://codepen.io/sharur7))
的笔 [CSS 加载栏](https://codepen.io/sharur7/pen/MWvRJQB)。

无尽的动画

### 无休止的动画是不确定指示器的一个例子。它要求用户等待，但不指明要等多久。当等待时间未知或很短时，可以使用它们。

参见 [CodePen](https://codepen.io) 上 Sharvari Raut([@ sharur 7](https://codepen.io/sharur7))
的 Pen[Loader 无限动画](https://codepen.io/sharur7/pen/XWaQNoZ)。

自定义动画

好玩的动画一般都是直接加到网站上的 SVG 加载器。这些动画有时很难只用 CSS 来构建。这些装载机吸引用户的注意力，使他们的等待时间更舒适。

### 一些 JavaScript 库帮助快速构建这些动画，比如 [p5.js](https://p5js.org/) 、 [GraphicsJS](http://www.graphicsjs.org/) 、 [D3.js](https://d3js.org/) 等等。

参见 [CodePen](https://codepen.io) 上 Sharvari Raut([@ sharur 7](https://codepen.io/sharur7))
的 Pen [Fun Loader](https://codepen.io/sharur7/pen/oNeORzE) 。

等待装货人的原因

一些加载器用一些文本让我们知道等待的原因。这些对于保持用户在我们的网站上是有效的。例如，加载器可以告诉您进程处于哪个步骤，比如“获取数据”、“连接到对等点”和“删除文件”这些信息有助于与用户交流并获得适当的理解。

参见 [CodePen](https://codepen.io) 上 Sharvari Raut([@ sharur 7](https://codepen.io/sharur7))
的笔 [计算结果时钟加载器](https://codepen.io/sharur7/pen/JjyVbdr)。

### 结论

当设计一个网站时，你应该考虑所有的 UI 细节。如果加载时间太长，一半的用户甚至在有机会看到你的网站之前就离开了，那么拥有一个好的网站是没有帮助的。

为了防止这种情况发生，你应该在你的网站上添加有趣的动画加载器来保持用户的注意力，直到所有的内容都被加载。添加创意，设计一个有趣的等待 gif，每个用户都可以享受看到！

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

## 现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

As web frontends get increasingly complex, resource-greedy features demand more and more from the browser. If you’re interested in monitoring and tracking client-side CPU usage, memory usage, and more for all of your users in production,

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

[LogRocket](https://lp.logrocket.com/blg/css-signup) is like a DVR for web and mobile apps, recording everything that happens in your web app or site. Instead of guessing why problems happen, you can aggregate and report on key frontend performance metrics, replay user sessions along with application state, log network requests, and automatically surface all errors.

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).