# Firefox 66 的新特性:动画 CSS 网格

> 原文：<https://blog.logrocket.com/new-in-firefox-66-animating-css-grid-b4ed90ac32f5/>

Firefox 在不断改进，Mozilla 团队设法用每个新版本给我们带来惊喜。

我们已经看到[最近在他们的开发工具上的显著改进](https://blog.logrocket.com/firefox-devtools-for-css-authors-1511f41d1e3)推动许多开发人员在开发中使用 Firefox，CSS 模块和 API 的浏览器实现也在不断增长。

最近登陆的一个功能是 CSS Grid 的动画。根据 [CSS Grid level 1 规范](https://drafts.csswg.org/css-grid/#track-sizing)，一些网格属性应该是可动画的，但是没有浏览器实现过这方面。所有的供应商都选择妥协，并在没有 Grid 的情况下发布了 Grid，所以我认为这是一个很难(或者不可能)实现的特性，我也不期望它会出现。但幸运的是，我错了。

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)

### 动画网格行和列

从 Firefox 66 开始，控制网格轨迹尺寸的属性现在是可动画化的。这意味着`grid-template-rows`和`grid-template-columns` 可以通过适当的插值在动画中操作。

实现非常简单:与任何其他 CSS 动画一样，只需为元素定义`animation`，并设置一个控制它的`@keyframes`规则。例如:

```
.element{
   grid-template-columns: 1fr 1fr 1fr;
   animation: resize 2000ms ease infinite alternate;
 }

 @keyframes resize {
   to {
     grid-template-columns: 1fr 2fr 1fr;
   }
 }
```

将提供一个具有三个等宽列的网格(除非任何网格项目具有更大的固有大小… [FR 单元不是那么简单](https://github.com/w3c/csswg-drafts/issues/1777))来来回回地动画显示一个具有更宽中间列的网格。

尽管有一些限制。引用规范:

> *动画:作为长度、百分比或计算的简单列表，假设唯一的区别是列表中长度、百分比或计算组件的值*

这意味着动画只能影响不同轨迹的大小(而不是从模板中添加/移除轨迹),如果混合了单位类型，插值将不起作用。例如，一个轨道不能从 40px 到 1fr 制作动画。

米歇尔·巴克的这个例子展示了一个网格动画的基本作品(为了更好的测量，有一些颜色的变化):

> 没有描述

要记住的一个好技巧是使用空轨道来推送实际内容。我们可以将元素放置在特定的轨迹(列/行)中，并设置空轨迹动画，以便“推动”该元素:

上面的代码将把元素放在第二行，从上到下再推回来(因为第一行将从零高度增长到占据所有的空白空间)。

```
<div class="grid">
  <div class="element"></div>
</div>
```

```
.grid{ 
  display: grid;
  animation: push 2s linear infinite alternate;
}

.element{
  grid-row: 2;
}

@keyframes push{
  from{
    grid-template-rows: 0fr auto;
  }
  to{
    grid-template-rows: 1fr auto;
  }
}
```

我最喜欢的这种技术的例子之一是安德鲁·哈佛的这支棒极了的笔，它重新创造了一个不错的老的有弹性的 DVD 标志:

一个 CSS 网格动画实验。最好在 Firefox 夜间观看。…

> A CSS grid animation experiment. Best viewed in Firefox Nightly. …

我使用相同的技术创建了一个纯 CSS 无限 Pong 演示:

网格{宽度:100%；身高:100%；显示:网格；网格-模板-列:20px 0fr 20px 1fr 20px 动画:pong-h 2s 线性无限交替，pong-v 1.4s 线性无限交替；will-change:网格-模板-列，网格-模板-行；/*快速脏 hack 修复边界*/background-image:linear-gradient(透明 40px，深灰色 40px，深灰色 calc(100%–90px)，透明 0)；填充顶部:50px}

> 间隙也可以制作动画

一个已经存在了相当长一段时间但仍不为大多数人所知的特性是网格间隙动画。还可以通过定义一个简单的`@keyframes`规则来激活`grid-gap`、`grid-column-gap`和`grid-row-gap`属性。这部分规范甚至拥有近乎完美的浏览器支持。

### 这为其他酷的效果打开了可能性。例如，我们可以分离实际的元素，或者让它们开始时相距很远，然后在中心发生冲突。

下面的代码将使两个网格项目完全脱离屏幕，并折叠到中间

Manuel Matuzovic 的这支笔展示了如何在“真实世界”中使用它(它在加载时是动画的，如果你错过了，请刷新):

没有描述

```
.grid{
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 300px;
  animation: gap 2s;
}

@keyframes gap{
  from{
    grid-gap: 100%;
  }
  to{
    grid-gap: 0%;
  }
}
```

关于性能的说明

> 即使有这些限制，动画 CSS 网格属性也可以实现令人惊叹的效果。不过，有一个警告，与任何高度/宽度变化一样，动画网格列、行或间隙会触发布局的变化，这可能会影响性能。

布局计算在[渲染管道](https://developers.google.com/web/fundamentals/performance/rendering/)的中间，这意味着浏览器将不得不在动画期间不断地重复*布局* **和**以下步骤(*绘制*和*合成*)。

### 通常认为只对改变合成步骤的属性进行动画制作是一种好的做法(这几乎意味着只进行不透明和变换)，所以如果你要在制作中使用 CSS 网格动画，请有目的地负责任地使用。为了最小化对性能的影响，仅动画化具有很少后代的元素，并使用简单的剪辑/堆叠上下文树。

此外，考虑使用[的 will-change 属性](https://developer.mozilla.org/en-US/docs/Web/CSS/will-change)来通知浏览器哪些部分将被动画化，这样他们就可以进行优化。请记住，`will-change`上的多个属性应该以逗号分隔的值链接起来。所以，如果你想让列和行模板都有动画效果，使用`will-change: grid-template-columns, grid-template-rows;`。

其他浏览器呢？

![](img/f6c8a1ceda2369b1bb1d5aa6fcca0768.png)

The full pixel pipeline

Chromium 浏览器*类似于*动画 CSS 网格，但是没有插值。这意味着我们可以定义一个受尊重的`@keyframes`动画，但是它会在步骤之间跳跃，而不是平滑过渡。想想如何将一个元素的`visibility`从`hidden`到`visible`制作成跳动的外观，同时过渡`opacity`使其逐渐出现。

嗯，Chromium 让网格状的可视性变得生动，而 Firefox 让它变得不透明。

### 但是嘿！一如既往，有了 CSS，*我们就能黑掉它*。我们可以将网格轨迹(行或列)定义为某种自动大小(`auto`、`min-content`、`max-content`)，然后制作网格元素的`width` / `height`动画。

下面是基于上述代码的一个基本示例:

添加外部样式表/笔此处添加的任何 URL 都将按顺序添加，并放在编辑器中的 CSS 之前。通过使用其 URL 和适当的 URL 扩展名，您可以使用另一个笔中的 CSS。JavaScript 预处理器巴贝尔包括 JSX 处理。

But hey! As always, with CSS, *we can hack it*. We might define the grid track (row or column) as some automatic size (`auto`,`min-content`,`max-content`) then animate the `width`/ `height` of the grid-elements.

```
.container{
  display: grid;
  grid-template-columns: auto 1fr;
}

.element{
  width: 0;
  animation: grow 3s ease-in-out infinite alternate;
}

@keyframes grow{
  to{
    width: 200px;
  }
}
```

更多来自 LogRocket 的精彩文章:

> 不是非常实用或高性能，但它为一些给定的用例提供了一种插值网格动画

类似的方法可以用于基于过渡而不是关键帧的动画:

* * *

### CSS 网格应该有动画属性…但是浏览器还没有实现它。这里有一个最典型的使用方法…

* * *

结论

CSS 网格动画的实现让我们可以在界面上添加惊人的效果。对性能的影响和缺乏其他更流行的浏览器的支持可能意味着我们不会经常看到这一点，但 Firefox 团队为开发人员和设计人员提供了一个很好的实验工具，为出色的 CSS 效果和工具铺平了道路。

> 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

### .

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

## 现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

As web frontends get increasingly complex, resource-greedy features demand more and more from the browser. If you’re interested in monitoring and tracking client-side CPU usage, memory usage, and more for all of your users in production,

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

[LogRocket](https://lp.logrocket.com/blg/css-signup) is like a DVR for web and mobile apps, recording everything that happens in your web app or site. Instead of guessing why problems happen, you can aggregate and report on key frontend performance metrics, replay user sessions along with application state, log network requests, and automatically surface all errors.

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).