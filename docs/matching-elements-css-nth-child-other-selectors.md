# 使用:n-child 和其他选择器匹配 CSS 中的元素

> 原文：<https://blog.logrocket.com/matching-elements-css-nth-child-other-selectors/>

Web 开发人员经常需要设计页面上特定元素的样式。使用 CSS 伪类选择器是定位这些元素的基本工具。

在这篇博客中，我们将探索`:nth-child`及其相关选择器如何允许我们根据它们在一组相似元素中的位置来定位特定元素。这对于创建替换样式或突出显示 HTML 文档中的重要元素特别有用。

我们将涵盖:

到本文结束时，您将会很好地理解如何使用这些选择器来更有效地设计和布局您的 web 页面。无论你是刚刚开始使用 CSS，还是一个经验丰富的专业人士，希望将你的技能提升到一个新的水平，这个博客都有适合你的东西。

我们开始吧！

## `:nth-child`选择器

CSS 中的`:nth-child`选择器允许我们根据元素在父容器中的位置选择特定的元素并设置样式。

假设您有一个 HTML 文档，在一个`ul`容器中有一列`li`元素。假设您想要设置列表项的样式，使所有奇数元素的背景颜色不同于偶数元素。你可以使用`:nth-child`选择器来达到这个效果。

使用`:nth-child`选择器的语法如下:

```
element:nth-child(n) {
  /* style rules */
}

```

在上面所示的`:nth-child`选择器语法中:

*   `element`是您要选择并设置样式的 HTML 元素
*   `n`是元素在子元素列表中的位置
    *   应为正整数
    *   如果元素的值为负或零，则不匹配任何元素

例如，`li:nth-child(1)`将选择第一个子元素，`li:nth-child(2)`将选择第二个子元素，依此类推。您可以在下面的代码栏中看到这一点:

参见 [CodePen](https://codepen.io) 上 Rishi pur war([@ Rishi 111](https://codepen.io/rishi111))
的 Pen [:第 n 子选择器示例](https://codepen.io/rishi111/pen/vYaGqJd)。

需要注意的是,`:nth-child`选择器将选择容器中指定元素类型的所有子元素——包括内部子元素。

除了整数，我们还可以使用公式来选择特定间隔的元素。最常见的公式是`an+b`，其中`a`和`b`为整数:

*   `a`值代表选择元素的间隔
*   `b`值代表开始的元素
*   `n`值可以是任何非负数或零；它的值从零开始

让我们通过下面的例子来更清楚地理解这个公式:

```
li:nth-child(2n+1)

```

在上面的例子中，我们将公式中的`a`值设置为`2`，这意味着`:nth-child`选择器将选择子元素列表中的每两个元素。公式中的`b`值是`1`，这意味着选择器将从列表中的第一个元素开始。

例如，考虑以下 HTML 代码:

```
<ul>
  <li>List 1</li>
  <li>List 2</li>
  <li>List 3</li>
  <li>List 4</li>
  <li>List 5</li>
  <li>List 6</li>
</ul>

```

由于`n`的值从`0`开始，将`li:nth-child(2n+1)`应用到上面的 HTML 代码将选择以下子元素:

*   如果是`n = 0`，那么`2 x 0 + 1 = 1`，选择列表中的第一个元素
*   如果`n = 1`，那么`2` `x 1 + 1 = 3`，即选择列表中的第三个元素
*   如果是`n = 2`，那么`2 x 2 + 1 = 5`，选择列表中的第五个元素

因此，`li:nth-child(2n+1)`选择器将选择列表中的第一、第三和第五个`li`元素。您可以在下面的代码栏中看到这一点:

参见 [CodePen](https://codepen.io) 上 Rishi pur war([@ Rishi 111](https://codepen.io/rishi111))
的 Pen [:第 n 子选择器公式示例](https://codepen.io/rishi111/pen/OJwNeKY)。

您可以使用这个`an+b`公式，通过改变`a`和`b`的值来选择不同间隔和起点的元素。例如:

*   `li:nth-child(3n+2)`将从第二个元素开始每隔三个元素选择一个
*   `li:nth-child(4n+3)`将从第三个元素开始选择每第四个元素

请查看下面的 CodePen，看看它是如何工作的:

参见 [CodePen](https://codepen.io) 上 Rishi pur war([@ Rishi 111](https://codepen.io/rishi111))
的 Pen [:不同值](https://codepen.io/rishi111/pen/YzOqZmr)的第 n 子选择器公式演示。

注意，在上面的例子中，两个`li:nth-child`选择器规则在第 11 个`li`元素上重合。由于代码中稍后出现的规则会覆盖同类型的早期规则，在本例中，“列表 11”被赋予黄色背景色。

除了使用公式之外，您还可以将关键字`odd`和`even`与`:nth-child`选择器结合使用。例如，`:nth-child(odd)`将选择所有奇数编号的子元素，而`:nth-child(even)`将选择所有偶数编号的子元素。

查看下面的 CodePen 以获得一些示例:

参见 [CodePen](https://codepen.io) 上 Rishi pur war([@ Rishi 111](https://codepen.io/rishi111))
的 Pen [:使用奇偶关键字](https://codepen.io/rishi111/pen/VwBjeaG)的第 n 子选择器。

请注意，在上面的示例中，每个列表项都应用了多种样式。偶数编号的子元素都有红色背景的蓝色粗体文本，而奇数编号的子元素都有黄色背景的绿色粗体文本。

### `:nth-child`选择器备忘单

我为你准备了一个小的备忘单，让你快速参考在 CSS 代码中使用`:nth-child`选择器的各种语法和例子。请参阅下表了解更多信息:

## `:nth-of-type`选择器

CSS 中的`:nth-of-type`选择器用于选择给定类型的元素，这些元素是其父元素的`nth`子元素。这个选择器类似于`:nth-child`选择器，但是它只选择特定类型的元素，而不是所有的元素。

以下是`:nth-of-type`选择器的语法:

```
:nth-of-type(n) {
  /* style rules go here */
}

```

参见下面代码笔中使用`:nth-of-type`选择器的示例:

参见 [CodePen](https://codepen.io) 上 Rishi pur war([@ Rishi 111](https://codepen.io/rishi111))
的 Pen[:n-of-type 选择器示例](https://codepen.io/rishi111/pen/YzjWYXj)。

在上面的例子中，`h2:nth-child(2)`选择器不会选择“标题 3 ”,因为它不是其父对象的第二个子对象。由于`:nth-child`规则考虑所有元素，而不考虑指定的类型，在这种情况下，第一个`h1`标签将是第一个子元素，因此“标题 2”被选为第二个子元素:

![Demo Of Nth Child Selector Being Used To Select Second Child Element And First H2 Element In A List](img/b855c8f05e7cfbfc592939e1e9259eb3.png)

假设您想使用`:nth-child`选择器来选择第二个`h2`标签。因为本例中的第二个`h2`标签实际上是父容器中从开始算起的第三个元素，所以应该使用下面的代码:

```
h2:nth-child(3) {
  color: red;
}

```

另一方面，`h2:nth-of-type(2)`选择器将选择第二个`h2`标签，因为当在父容器中寻找第二个`h2`标签时，它只考虑指定类型的元素。因此，该选择器不会将`h1`标签视为第一个孩子，也不会将`p`标签视为最后一个孩子。

换句话说，`h2:nth-of-type(2)`选择器将选择第二个`h2`标签，只要它是父容器中指定类型的第二个元素，而不管它的位置:

![Demo Showing How Nth Of Type Selector Excludes Child Elements That Are Not Of A Specified Type And Is Being Used To Select The Second Element Of A Specified Type](img/cc4cd87058ea3921a251aefc955ac03f.png)

## `:nth-last-child`选择器

CSS 中的`:nth-last-child`选择器允许我们从最后一个子元素开始，根据它在父容器中的位置选择特定的元素并设置样式。这对于将样式应用于列表末尾的元素非常有用。

以下是使用`:nth-last-child`选择器的语法:

```
element:nth-last-child(n) {
  /* Styles go here */
}

```

`:nth-last-child`选择器的工作方式类似于`:nth-child`选择器。唯一的区别是它从子元素列表的末尾开始计数，而不是从开始计数。

这意味着您可以使用与使用`:nth-child`选择器相同的规则和技术来使用`:nth-last-child`选择器选择元素。

例如，我们可以使用`:nth-last-child(2)`选择倒数第二个子元素，或者使用`:nth-last-child(odd)`选择从列表末尾开始的所有奇数子元素。

你可以在下面的代码栏中看到一些如何使用`:nth-last-child`选择器的例子:

参见 [CodePen](https://codepen.io) 上 Rishi pur war([@ Rishi 111](https://codepen.io/rishi111))
的 Pen[:n-last-child 选择器示例](https://codepen.io/rishi111/pen/oNMLbyJ)。

## `:nth-last-of-type`选择器

CSS 中的`:nth-last-of-type`选择器用于选择作为其父元素的`nth`子元素，从最后一个子元素开始计数。该选择器仅选择与所选元素类型相同的元素。

以下是`:nth-last-of-type`选择器的语法:

```
:nth-last-of-type(n) {
  /* style rules go here */
}

```

在这个 CSS 选择器中，`n`可以是一个整数，一个关键字如“even”或“odd”，或者一个公式。

使用`:nth-last-of-type`选择器的一些例子如下:

参见 [CodePen](https://codepen.io) 上 Rishi pur war([@ Rishi 111](https://codepen.io/rishi111))
的 Pen[:n-last-of-type 选择器示例](https://codepen.io/rishi111/pen/ExpybRg)。

现在，你可能想知道`:nth-last-child`和`:nth-last-of-type`选择器之间的区别。

两者的根本区别在于`:nth-last-of-type`选择器更加具体。换句话说，`:nth-last-of-type`选择器只考虑指定的元素类型，而`:nth-last-child`选择器考虑所有类型的元素。

让我们借助下面的代码来理解这种差异:

```
<div>
  <h1>Heading 1</h1>
  <h2>Heading 2</h2>
  <h2>Heading 3</h2>
  <h2>Heading 4</h2> <!-- second to last h2 tag -->
  <h2>Heading 5</h2> <!-- last h2 tag -->
  <p>Paragraph 1</p>
</div>

```

在这种情况下，`:nth-last-child`选择器会认为`p`标签是最后一个子标签。因此，假设您想要选择倒数第二个`h2`标签，“标题 4”，使用`:nth-last-child`选择器，如下所示:

```
h2:nth-last-child(2)

```

上面的代码不会选择“Heading 4 ”,因为它不是其父级的倒数第二个子级。相反，“标题 5”将被选为倒数第二个子元素:

![Demo Using Nth Last Child Selector To Select Second To Last Overall Child Element In A List Including Elements Of Various Types](img/533cb190d3b1914f224092ecc0aee3d5.png)

另一方面，`h2:nth-last-of-type(2)`将正确地选择倒数第二个`h2`标签“Heading 4 ”,因为它将只在父容器中寻找`h2`元素——指定的类型。因此，它不会将`p`标签视为最后一个子标签，也不会将`h1`标签视为第一个子标签。

在`nth-last-of-type(2)`的情况下，目标元素在父容器中的位置并不重要，只要它是指定类型的倒数第二个元素:

![Demo Showing How Nth Last Of Type Selector Excludes Elements That Are Not Of A Specified Type And Is Being Used To Select The Second To Last Element Of A Specified Type In A List Of Various Element Types](img/0f96156a1acdc60dfd3185c78abb6f59.png)

参见下面的 CodePen 示例，了解与`h2:nth-last-child(2)`相比，`h2:nth-last-of-type(2)`选择了哪个元素:

参见 [CodePen](https://codepen.io) 上 Rishi pur war([@ Rishi 111](https://codepen.io/rishi111))
的笔[区别:n-last-child 和:n-last-of-type 选择器](https://codepen.io/rishi111/pen/zYLBbJq)。

结论

## 这篇博文介绍了几种有用的 CSS 选择器，它们可以用来匹配 HTML 文档中的元素。

具体来说，我们查看了`:nth-child`、`:nth-last-child`、`:nth-of-type`和`:nth-last-of-type`选择器。这些 CSS 选择器允许我们根据元素在父容器或指定类型中的位置来选择元素。

概括来说，这篇博文的要点是:

`:nth-child`选择器根据元素在父容器中的位置选择元素，而不考虑它的类型

*   `:nth-of-type`选择器根据元素在父容器中的位置来选择元素，但前提是该元素属于特定类型
*   `:nth-last-child`选择器根据元素在父容器中的位置选择元素，从最后一个子容器开始计数，不考虑它的类型
*   `:nth-last-of-type`选择器根据元素在父容器中的位置选择元素，从最后一个子容器开始计数，但前提是它是特定的类型
*   我们鼓励你在你的项目中尝试这些选择器，以及[探索其他高级 CSS 选择器](https://blog.logrocket.com/advanced-css-selectors-for-common-scenarios/)或通读我们的[完整指南来提升你的 CSS 选择器技能](https://blog.logrocket.com/level-up-your-css-selector-skills/)。

感谢您花时间阅读这篇文章。如果你喜欢阅读它，在阅读过程中遇到任何问题，或者有更多的问题，请在评论区告诉我。如果你喜欢我在这里做的事情，并想帮助我继续做下去，别忘了点击分享按钮。

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).