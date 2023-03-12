# 深入研究 CSS :where()和:is()函数

> 原文：<https://blog.logrocket.com/deep-dive-css-where-is-functions/>

***编者按:*** *这篇深入探讨 CSS `:where()`和`:is()`函数的文章最后一次更新是在 2023 年 1 月 4 日，目的是更新任何过时的信息，包括关于`:is()`函数的章节，并讨论浏览器兼容性。*

CSS `:where()`函数接受一个选择器列表作为参数并缩小它们，允许你写更少的代码并同时将它们一起样式化。

在本教程中，我们将讨论`:where()`伪类函数，并演示如何在生产中使用它。我们将回顾与`:where()`函数相关的[堆栈](https://blog.logrocket.com/best-practices-stacking-elements-css/)、[特异性](https://blog.logrocket.com/deep-dive-css-specificity/)和[宽容](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes)。我们还将看一些具体的用例，并讨论它与`:is()`函数的相似之处和不同之处。让我们开始吧！

*向前跳转*

## CSS `:where()`是什么？

根据 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/:where) ，`:where()`是一个 CSS 功能伪类选择器，它接受一个选择器列表作为参数，并将给定的样式应用于列表中的任何元素。`:where()`对于使一个长的选择器列表变短非常有用。

在 CSS 中，当多个元素同时应用了相同的样式规则时，我们通常会写一长串用逗号分隔的选择器。

在下面的例子中，我们将相同的样式应用于在`header`、`main`和`footer`元素中找到的所有`<a>`标签:

```
header a:hover,
main a:hover,
footer a:hover {
  color: green;
  text-decoration: underline;
}

```

在上面的代码片段中，我们只选择了三个元素，但是随着元素和选择器数量的增加，代码将开始看起来不整洁，并且可能变得难以阅读和理解。这就是`:where()`伪类函数发挥作用的地方。

下面是上面的例子使用`:where()`函数的样子:

```
:where(header, main, footer) a:hover {
  color: red;
  text-decoration: underline;
}

```

让我们仔细看看这段代码是如何工作的。

当浏览器到达代码片段时，代码指示浏览器寻找`header`、`main`和`footer`选择器，并将这些选择器中的所有锚标记作为目标。然后，当用户悬停在这些锚标签上时，浏览器应该应用指定的样式，在本例中是`red`和`underline`。

这个伪类函数让我们可以用一种更短、更容易理解的方式编写一个长的选择器列表。

## 组合、分割和堆叠`:where()`功能

使用`:where()`函数，我们可以以多种方式和组合对元素进行分组。我们可以将`:where()`函数放在选择器的开头、中间或结尾。

下面是一个有多个选择器和样式的例子:

```
/* first list */
header a:hover,
main a:hover,
footer a:hover {
  color: green;
  text-decoration: underline;
}

/* second list */
article header > p,
article footer > p{
  color: gray;
}

/* third list */
.dark-theme button,
.dark-theme a,
.dim-theme button,
.dim-theme a{
  color: purple;
}

```

下面是用`:where()`函数重写的相同代码:

```
/* first list */
/* at the beginning */
:where(header, main, footer) a:hover {
  color: red;
  text-decoration: underline;
}

/* second list */
/* in the middle */
article :where(header, footer) > p {
  color: gray;
}

/* third list */
/* at the end */
.dark-theme :where(button, a) {
  color: purple;
}
.dim-theme :where(button, a) {
  color: purple;
}

```

在第一个列表中，我们指定将`red`和`underline`样式应用于`hover`上的`header`、`main`和`footer`元素。在第二个列表中，我们指定`article`、`header`和`footer`元素应该使用`gray`进行样式化。

为了更加清晰，我们将第三个列表分成了两个`:where()`函数。在这个列表中，我们指定了`button`和`a`元素应该用`.dark-theme`、`.dim-theme`和`purple`进行样式化。

现在，我们将进一步减少第三个列表函数，将它们变成一个`:where()`函数:

```
/* stacked */
:where(.dark-theme, .dim-theme) :where(button, a) {
  color: purple;
}

```

这种减少复杂选择器列表的策略被称为[堆栈](https://blog.logrocket.com/best-practices-stacking-elements-css/)。

## 特异性和`:where()`功能

[特异性是浏览器通过查看](https://blog.logrocket.com/deep-dive-css-specificity/)来决定什么样的 CSS 属性值或样式应该应用于特定的元素。

CSS `:where()`函数的特异性始终为零。因此，任何以该函数为目标的元素也会自动获得一个为零的特定性。这给了我们一种力量，可以轻松地取消任何我们想要的元素的样式，同时将它的特异性降低到零。

下面是一个 HTML 有序列表的例子:

```
<div>
  <h2>First list no class</h2>
  <ol>
    <li>List Item 1</li>
    <li>List Item 2</li>
  </ol>
</div>
<div>
  <h2>Second list with class</h2>
  <ol class="second-list">
    <li>List Item 1</li>
    <li>List Item 2</li>
  </ol>
</div>

<div>
  <h2>Third list with class</h2>
  <ol class="third-list">
    <li>List Item 1</li>
    <li>List Item 2</li>
  </ol>
</div>

```

在上面的代码片段中，有三个有序列表，每个列表中有两个项目。第二和第三个列表有一个给定的`class`，而第一个列表没有。

没有任何样式，我们可以看到每个列表都是按数字排序的:

![CSS Numerically Ordered List ](img/a5f3b1ed58bcfb4e7d40c045eea2e203.png)

现在，让我们添加一些样式:

```
:where(ol[class]) {
  list-style-type: none;
}

```

在上面的代码片段中，我们使用`:where()`伪类函数来选择所有应用了`class`的`<ol>`标签。

下面，我们看到第二个和第三个列表都有一个`class`，它们被`:where()`函数作为目标，并且它们的`list-style-type`被移除了:

![CSS :where() Function Before Second and Third Style Was Removed](img/533c93a59cea9c3121ead0c846f5deae.png)

现在，让我们添加一些额外的样式:

```
:where(ol[class]) {
  list-style-type: none;
}

.second-list {
  list-style-type: disc;
}

```

仅针对使用其名称`class`的第二个列表，我们可以看到它现在显示了项目符号，而第三个列表仍然没有`list-style`类型:

![Third CSS List With Styling](img/07c6dff59ea8e2de18c97edc364cdbf4.png)

您可能想知道，“这难道不是应该的吗，因为新的样式写在`:where()`函数样式的下面？”不，不是的，我们一会儿就知道了。

让我们看看当我们将刚刚添加的代码移动到代码块的顶部并将`:where()`函数部分移动到底部时会发生什么:

```
.second-list {
  list-style-type: disc;
}

:where(ol[class]) {
  list-style-type: none;
}

```

请注意，样式仍然没有改变:

![CSS List With No Styling Change](img/d176696a34a831f9e6927a59a62ce55c.png)

请记住，`:where()`函数没有特异性。不管新代码是放在`:where()`函数片段之前还是之后，以`:where()`为目标的元素的特性都将变为零，其样式也将失效。

为了进一步说明这一点，让我们将第二个列表的样式添加到第三个列表中，跟在代码中的`:where()`函数后面:

```
.second-list {
  list-style-type: disc;
}

:where(ol[class]) {
  list-style-type: none;
}

.third-list{
  list-style-type: disc;
}

```

第二个和第三个列表都显示有项目符号，与代码放置无关:

![CSS Styling List With Bullet Points](img/3ff7a5aa249cfbe27f5aee608659a801.png)

在 [CodePen](https://codepen.io) 上使用 CSS :where()函数见笔[。](https://codepen.io/timonwa/pen/abjmErY)

在 [CodePen](https://codepen.io) 上使用 CSS :where()函数见笔[。](https://codepen.io/timonwa/pen/abjmErY)

现在，让我们看看 CSS `:where()`函数将如何反应，如果其中一个元素是一个无效的选择器。

## `:where()`功能和宽恕

CSS 通常被认为对于选择器列表是不宽容的。如果浏览器不能识别列表中的一个选择器，那么整个选择器列表将被视为无效，并且它们的样式将不会被应用。然而，`:where()`伪类函数却不是这样。

如果一个`:where()`函数中的一个元素被一个无效的选择器定位，那么这个元素将不会得到任何样式。其余的元素仍然会得到样式。`:where()`功能将跳过无效的选择器到下一个(`valid`)选择器。这就是为什么`:where()`被称为宽容的选择器。

在下面的例子中，`:unsupported`对于许多浏览器来说是一个无效的选择器。即使在不支持`:unsupported`选择器的浏览器中，上面的代码也会被正确解析，并且仍然匹配`:valid`选择器，如下所示:

```
:where(:valid, :unsupported) {
  ...
}

```

然而，在不支持`:unsupported`选择器的浏览器中，以下代码将被忽略，即使它们支持`:valid`选择器:

```
:valid, :unsupported {
  ...
}

```

## `:where()`功能的特殊用例

在一些特殊的用例中，`:where()`函数可能是一个有用的工具，但是也有一些情况下应该避免使用它。几乎所有在使用`:where()`伪类函数时发生的挫折都可以归结为特殊性。因为`:where()`没有专一性，所以我们需要非常小心何时何地使用这个函数。

首先，让我们来看几个`:where()`特别有用的用例。

### 改进 CSS 重置

CSS 重置是指在加载任何其他样式之前加载一组样式规则，以清除浏览器的内置样式。CSS 重置通常放在 CSS 样式表的顶部或开头，所以它们首先加载。开发人员通常在开始设计元素和网站的样式之前，先用它们来删除浏览器给几个元素的默认样式。CSS 重置也有助于消除不同浏览器之间的不一致。

CSS 重置是临时的样式，在以后的样式化过程中会改变。然而，根据 CSS 重置中使用的一个元素或一组元素的选择器的简单性或复杂性，可能很难在代码中稍后覆盖初始样式。

例如，假设我们将网站上的所有锚定标签设为绿色。然后，我们决定将所有页脚锚定标签的样式设置为灰色。

由于在 CSS 重置中选择的复杂性，新的(灰色)样式没有被应用。重置中的选择器比代码后面使用的选择器具有更高的特异性，只针对脚注定位标记，因此不应用灰色样式。

现在，如果我们将`:where()`伪类函数添加到 CSS 重置中，这将自动赋予重置中的所有元素一个为零的特异性。这使得我们以后更容易更改样式，而不必担心特异性冲突。

### 拆卸样式

如果我们想要删除或取消样式，或者减少一个元素或一组元素的特殊性，那么`:where()`函数会很有用。这种改变将发生在我们把它放入代码的时候，就像一个迷你的重置。

### 使变小

[代码越短越容易阅读和调试](https://blog.logrocket.com/the-complete-best-practices-for-minifying-css/)。一个好的经验法则是检查任何超过两个逗号或三个列表项的代码，看看是否可以使用`:where()`函数来缩小。对于两个或更多选择器的组合，这也是一个有用的策略；比如`section` > `header` > `p` > `a`。

现在，让我们看一个应该避免使用`:where()`函数的用例。

### 保持造型

如果确保一个元素或一组元素的风格或特性在未来的任何时候都不会改变是很重要的，那么就不要使用`:where()`伪类。任何以此选择器为目标的元素的样式和特征都将失效。

## CSS `:is()`函数

CSS `:is()`函数的工作方式与`:where()`函数几乎相同。你可以用它来简化复杂的选择器，也可以把它放在选择器的开头、中间或者结尾，就像`:where()`函数一样。

它也是宽容的，就像`:where`函数一样。因此，当其中一个选择器无效时，浏览器将忽略该选择器，但是有效选择器的样式将被添加到所选元素中。

## `:where()`和`:is()`功能的区别

这两个函数的区别在于，`:where()`函数的特异性始终为零，而`:is()`函数的特异性取决于其最具体参数的特异性。例如，让我们看看`header`元素中的一段文字:

```
<header>
  <p>This is a paragraph text.</p>
</header>

```

然后，让我们尝试使用四种不同的选择器来更改文本颜色:

```
header p {
  color: blue;
}

:is(header, section) p {
  color: green;
}

p {
  color: blue;
}

:where(header, section) p {
  color: blue;
}

```

第一个选择器给文本一个颜色`blue`。使用`:is()`的第二个选择器与第一个选择器具有相同的特异性，但是因为它在第一个选择器之后，所以它将文本颜色从`blue`更改为`green`。第三个选择器的特异性低于第一个和第二个，对文本没有影响。

最后，第四个函数使用了`:where()`函数，它对文本没有任何影响，因为它的具体性为零:

参见 [CodePen](https://codepen.io) 上 Timonwa ( [@timonwa](https://codepen.io/timonwa) )
使用 CSS :is()函数的笔。

参见 [CodePen](https://codepen.io) 上 Timonwa ( [@timonwa](https://codepen.io/timonwa) )
使用 CSS :is()函数的笔。

浏览器兼容性

## 所有的浏览器，无论是桌面的还是移动的，都提供了对 CSS `:where`函数的完全支持，包括对其宽容本质的支持。因此，您不必担心您的样式是否会在浏览器中正确呈现。

结论

## CSS `:where()`伪类函数对于改进 CSS 重置、删除代码中任何一点的样式、使代码更易于阅读和调试非常有用。

在本文中，我们展示了如何在生产中使用`:where()`函数，并研究了几个用例。我们也看到了它与 CSS `:is`函数的相似之处以及它们之间的区别。

你对这个 CSS 选择器有什么想法？你能想到其他有用的例子吗？请在下面的评论中告诉我们。

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).