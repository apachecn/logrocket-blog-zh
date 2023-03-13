# 现代 CSS 样板文件应该是什么样的？

> 原文：<https://blog.logrocket.com/what-should-modern-css-boilerplate-look-like/>

CSS 样板文件是一组非原创和非特定的 CSS 规则，设计用于 web 项目中的固有用途。使用它可以帮助你在几秒钟内启动并运行你的 CSS。

您可以在网上找到 CSS 样板模板，或者您可以创建并维护自己的模板，以制作`1rem = 10px`，重置各种 CSS 属性，在原始 HTML 元素上实施您的首选项，等等。我确信有许多 CSS 规则你习惯性地包含在你的所有项目中(其中一些可能做你认为 CSS 应该做的事情)，所以即使你没有有意识地使用 CSS 样板，你几乎肯定有一个的素质。

使用流行的 CSS 样板文件的问题在于，它们往往过于热心，对一般网站的需求做了太多的假设，并重置了所有已知的 CSS 属性。事实上，将其中一些称为膨胀软件并不完全不准确。也就是说，许多定制的 CSS 样板文件比他们需要的更加奢侈，包含许多冗余代码，并且随着时间的推移会变得越来越大。

在本文中，我将向您展示我认为现代 CSS 样板文件的必备内容。结果呢？希望有一些更简单、更可预测、更容易维护的东西。我们还将讨论一些有用但不重要的 CSS 规则。

*向前跳转:*

## 不要重设边距

有相当多的 HTML 元素是 web 浏览器自动应用边距的，尽管重置它们(即`margin: 0;`)很常见，但您不应该这样做，因为 web 浏览器这样做是有正当理由的。`<body>`元素使用边距来确保网页保持可读，并且在 CSS 没有加载的情况下，触摸目标不会落在移动屏幕的边缘。内容元素(如`<h1>`、`<p>`和`<figure>`)应用边距的原因是一样的——可访问性。`<dialog>`元素使用边距使自己在屏幕中间居中。

我并不是说我们应该让浏览器为我们决定一切，而是因为你肯定会根据设计规范定义你自己的边距(对吗？)，为什么重置它们只是为了以后重新定义它们？这就是我对 CSS 重置的总体感觉，这也是我不会过多谈论它们的原因。

## 肯定重置了盒子模型

假设您将一个元素的`width`设置为`700px`，将`border`设置为`1px`，将`padding`也设置为`1px`——现在该元素的物理宽度是多少？它仍然是在内部形成边框和填充的`700px`，还是现在添加了边框和填充的`704px`？大多数人都会同意，如果你将`width`设置为`700px`，那么这就是物理宽度，但通常情况下，它实际上会是`704px`。为了理解你为什么想要阅读关于 [CSS 盒子模型](https://blog.logrocket.com/css-margin-vs-padding/)。

要使选择器的盒子模型以大多数人认为更符合逻辑的方式运行，您需要用这个 CSS 规则来重置它:

```
box-sizing: border-box;

```

有了这个 CSS 规则，`700px`就意味着`700px`，任何边框和填充都将在内部形成，减少内容区域的宽度，而不是添加到元素的外部。

虽然，因为不知道你到底要添加什么样的边框和填充，你只是想使用通用选择器`(*)`将规则应用于所有东西:

```
* {
    box-sizing: border-box;
}

```

编写`*`比向目标编写一个特定选择器列表要快。此外，目前通用选择器对性能的影响可以忽略不计，并且不会影响特异性权重值。

然而，它并不针对[伪元素](https://blog.logrocket.com/css-pseudo-elements-guide/)，所以考虑到使用`::before`和`::after`是多么的普遍，您可能更喜欢下面的代码片段:

```
*,
*::before,
*::after {
    box-sizing: border-box;
}

```

但是为什么盒子模型不能在盒子之外更合理地工作呢？原因是,`box-sizing`属性是一个相对较新的属性，让 web 浏览器改变它们呈现布局的方式会完全破坏已经存在的布局。

所以即使它不是默认的；我想大多数开发人员认为这是最好的方法。

## 你如何处理边框取决于你自己

`border: none;`是一个 CSS 规则，在很多 CSS 重置中都有体现。默认情况下，Firefox 上的按钮、对话框、表单相关元素和图像都有边框。这对于可用性来说是非常好的，因为边框可以增加交互元素的启示，但是如果你想以不同的方式处理启示，可以使用下面的代码片段来删除所有的边框:

```
* {
    border: none;
}

```

## 强制图像行为

元素是内联的，所以它们下面通常会有那个烦人的小空间。你可以通过修改`vertical-align`来解决这个问题，但是没有一个值在每种情况下都有效，而且你也不太可能希望你的图片是内嵌的。你最好和`display: block;`解决这个问题。

我还加入了`max-width: 100%;`，它可以防止图像毫无理由地炸毁它们的容器。为什么？因为容器应该包含！

```
img {
    display: block;
    max-width: 100%;
}

```

当然，还有[许多其他的内联元素](https://developer.mozilla.org/en-US/docs/Web/HTML/Inline_elements)，但是它们要么不常见，要么使它们成为块级的可能有点冒昧。

## 重置(大多数)列表

无论你使用有序列表(`ol`)、无序列表(`ul`)还是菜单(`<menu>`)，你都不太可能需要列表标记(例如，项目符号、数字等)。，)，因为它们在用户界面上看起来相当难看。下面的代码片段删除了它们，但不是从文章中使用的那些以自然形式使用它们有意义的地方删除的:

```
menu:not(article menu),
ol:not(article ol),
ul:not(article ul) {
    list-style: none;
}

```

令人恼火的是，它们的左侧也有一些填充，这是你本来就想去掉的:

```
menu,
ol,
ul {
    padding-left: 0;
}

```

此外，由于列表标记出现在列表的外部，使用上面的代码片段删除填充会导致列表标记(我们不重置的文章中使用的标记)与相邻的内容重叠或溢出视口，因此您需要将它们切换到`inside`:

```
article ol,
article ul {
    list-style-position: inside;
}

```

> 提示:将`role="list"`属性值添加到您希望屏幕阅读器宣布的所有列表中，因为`list-style: none;`在一些 web 浏览器中禁用了这一功能。

## 增强链接下划线

作为一名设计师，我认为字体设计是设计美学中最重要的部分。我还认为按钮被过度使用了，尽管这是有道理的，因为带下划线的链接看起来并不舒服。然而，现在有一种方法可以让下划线变得更粗(以匹配字体的大小和粗细)和更空间感。它只需要两个 CSS 属性:

```
a {
    /* Places underlines below the descenders */
    text-underline-position: under;

    /* Sets the thickness as a percentage of the font size */
    text-decoration-thickness: 8;
}

```

## 更改根字体大小

如果你更喜欢使用`rem`单位([更便于访问](https://blog.logrocket.com/a-developers-guide-to-designing-accessible-websites/))，`1rem`等于`16px`(默认根字体大小)。自然，当`1rem`等于`10px`时，将`px`转换为`rem`要容易得多，但是要做到这一点，您需要将根字体大小改为`62.5%`，就像这样:

```
/* <html> = the root */
html {
  font-size: 62.5%; /* (62.5/100) * 16px = 10px */
}

```

## 禁用文本膨胀算法

在智能手机和平板电脑上，web 浏览器使用文本膨胀算法来放大使用 100%视区宽度的文本元素的字体大小。这样做的初衷是好的(为了增强可读性)，但这可能是不可预测的，有些元素会被算法命中，有些则不会。

此外，我敢肯定你不希望网络浏览器为你决定字体大小——因为我也不希望这样。任何优秀的设计师或开发人员都知道文本应该在所有设备上都可读，并投入时间和精力使网站具有响应性。要禁用这个算法，您需要将`text-size-adjust: none;`应用到根/ `<html>`:

```
html {
  -webkit-text-size-adjust: none; /* for iOS Safari */
  text-size-adjust: none; /* for other mobile browsers */
}

```

非常简单——`scroll-behavior: smooth;`让用户能够平滑地滚动到它们，而不是立即捕捉到同一个页面的锚点和文本片段。此外，为了迎合那些出于可访问性原因而喜欢减少运动的人，该规则被包装在一个`prefers-reduced-motion: no-preference`媒体查询中:

```
@media (prefers-reduced-motion: no-preference) {
  html {
        scroll-behavior: smooth;
  }
}

```

## 重新思考焦点是如何工作的

`:focus-visible`伪类提供了一种不太激进的方法来设计当前关注的交互元素。更具体地说，使用下面的代码片段，您可以用它来删除鼠标用户的轮廓，但保留键盘用户的轮廓。*只有一个选择器，不多不少！*

```
:focus:not(:focus-visible) {
    outline: none;
}

```

## 用光标提供交互式元素

许多交互元素，比如按钮，在悬停时不像锚标签那样有指针(手形)光标。我一直觉得这很奇怪，尤其是对于没有明确定义区域的交互元素，比如大多数标签。光标根本没有改变*，在我看来，这表明规范有时会出错。这是一个只影响桌面用户的小警告，但我认为这是一个值得修正的问题。*

 *以下代码片段将`pointer`光标添加到还没有替代光标的交互元素中，例如`text inputs`、`textareas`等等:

```
label,
button,
select,
summary,
[type=radio],
[type=submit],
[type=checkbox] {
    cursor: pointer;
}

```

## 结束语

仔细考虑你的 CSS 样板文件是很重要的。毕竟，it *是你将要工作的每个网站的基础。这包括定期回顾它，以及回顾随着时间的推移你对它所做的任何改变。如果你曾经在设计系统上工作过(或者仅仅是设计版本化)，你可能会发现回顾变更的概念非常熟悉，以及变更的结果感觉有目的和最小化。*

我期待着听到你的 CSS 样板文件中的 CSS 规则，以及在阅读完这篇文章后你是否打算对它进行修改。请在下面的框中留言！

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。*