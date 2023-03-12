# 提升 CSS 选择器技能的完整指南

> 原文：<https://blog.logrocket.com/level-up-your-css-selector-skills/>

***编者按:**本帖于 2021 年 7 月更新，以反映 CSS 选择器的最新信息，包括 2021 年 5 月 [CSS 伪元素模块 4 级](https://drafts.csswg.org/css-pseudo-4/)更新。*

我使用 CSS 已经很多年了，但是有一件事我直到最近才深入探讨，那就是 CSS 选择器的话题。

我为什么需要这么做？我们现在对选择器了如指掌，对吧？

问题是(至少对我来说)随着时间的推移，很容易习惯于在每个项目中使用相同的可信任的选择器集来实现您需要做的事情。

因此，我主动对 CSS 选择器进行了深入的回顾，并发现了一些有趣的选择器，它们对我来说是新的，以一种我以前从未想到过的方式使用，或者将很快可用。

W3C 指定的最新 CSS 选择器包含在[选择器第 4 级文档](https://www.w3.org/TR/selectors-4/)中，最后一次更新是在 2018 年末。这些选择器中的大部分现在都在所有主流浏览器中，但是有几个，比如`:has()`，仍然悬而未决。伪元素有单独的规范，最新的是 [CSS 伪元素模块 Level 4](https://drafts.csswg.org/css-pseudo-4/) ，2021 年 5 月再次更新。所以，应该很快会有更多的！

我邀请你和我一起看看各种类型的 CSS 选择器。其中有多少你已经在日常工作中使用了？我很想知道。

准备好提升你的 CSS 选择器技能了吗？那好吧，我们走。

## 组合器 CSS 选择器

让我们从熟悉的领域开始。组合选择器用于选择子元素和兄弟元素，并且已经存在了很长时间。

*   通用子选择器(空格)，例如`A B`
*   直接子选择器，例如`A > B`
*   相邻兄弟选择器，例如`A + B`
*   通用同级选择器，例如`A ~ B`

旁边的选择器`A + B`你应该很熟悉。它选择紧随`A`之后的元素`B`。但是一般的同胞选择器`A ~ B`呢？这将选择所有跟在`A`后面的兄弟元素`B`。

这里有一个它们都在起作用的例子:

参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
的笔 [组合子选择器](https://codepen.io/dgwyer/pen/RyvYXd)。

选择 New York 行是因为它紧跟在第一行之后，最后两个城市突出显示，因为 general sibling 选择器匹配第四个城市之后的所有城市。

## 属性 CSS 选择器

我真的很喜欢属性选择器。当您需要将包含属性的元素与特定值进行匹配时，它们非常灵活。

参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
的 Pen [属性选择器#1](https://codepen.io/dgwyer/pen/MGLZEK) 。

这个例子演示了选择所有的复选框输入元素，并对它们相关的标签应用样式，使它们加粗并染成蓝色。

然后，我们用特定的名称`chk2`覆盖复选框的样式，并将其关联的标签涂成红色。请注意，其他表单元素标签不受影响，也没有应用标签样式。

属性选择器不仅仅用于表单元素，它们还可以针对任何元素的属性。您可以匹配任何属性，而不仅仅是官方支持的属性。此外，您可以检查属性是否存在，如下所示:

```
button[icon]
```

这匹配包含一个`icon`属性的`<button>`元素。它将匹配属性，无论它是空的还是设置为特定值。

更多示例:

参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
的 Pen [属性选择器#2](https://codepen.io/dgwyer/pen/pVGGye) 。

第一个链接没有`target`属性，所以不匹配。接下来的两个链接是匹配的，因为它们要么有一个空的`target`属性，要么有一个特定的值。最后，最后一个链接被设置为`pink`，因为它匹配`fluffy`属性。它的值是不相关的，只是必须存在以匹配`a[fluffy]`选择器。

一个实际的例子是突出显示没有`alt`属性的图像。这个属性对于可访问性是必需的，所以对于 SEO 来说，确保所有图像元素都包含这个属性是很重要的。

我们可以使用以下规则来实现这一点:

```
img:not([alt]) {
 border: 2px red dashed;
}
```

参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
的 Pen [属性选择器#3](https://codepen.io/dgwyer/pen/OZdqLp) 。

如果你想匹配一个属性值的特定部分，那么有一些非常有用的选择器可用。

`A[attr^=val]`:属性以`val`
`A[attr|=val]`开头:属性的值与`val`完全匹配或以`val`开头，后面紧跟着一个破折号
`A[attr$=val]`:属性以`val`
`A[attr*=val]`结尾:属性在值字符串
`A[attr~=val]`中的任意位置包含`val`的属性:值字符串为`val`或在空格分隔的列表中包含`val`的属性

下面是每种方法的一个例子:

参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
的 Pen [属性选择器#4](https://codepen.io/dgwyer/pen/MGLdvE) 。

前两个例子非常相似，除了`A[attr|=val]`也匹配这个值，后跟一个用破折号分隔的字符串。这对于匹配语言属性很有用。如`<p lang="en-us">`。

使用`A[attr$="val"]`和`::after`可以轻松匹配文件扩展名，因此您也可以轻松显示匹配的文件。注意使用了`attr()`和连接，用一个静态字符串将它连接起来。

`A[attr*=val]`展示了无论使用什么协议或子域，如何匹配特定的域。

最后，我们有`A[attr~=val]`，它非常适合匹配由空格分隔的值列表组成的属性中的值。这只匹配整个单词而不是单词片段，因为`*=`操作符会匹配单词的复数形式。

以上所有属性选择器的例子都是区分大小写的。但是我们有锦囊妙计。如果我们在右方括号前插入一个`i`，我们可以打开区分大小写的匹配。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
的 Pen [属性选择器#5](https://codepen.io/dgwyer/pen/LmaYzp) 。

用户界面选择器

## 如果您曾经处理过样式表单，那么您以前肯定遇到过这些类型的伪类:

`:enabled`

*   `:disabled`
*   `:checked`
*   例如，我们可以使用`:checked`来设计一个简单的待办事项列表。

参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
的笔 [用户界面选择器#1](https://codepen.io/dgwyer/pen/BxbjoO) 。

这是非常标准的，但是我们还有其他一些有趣的伪类。

参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
的笔 [用户界面选择器#2](https://codepen.io/dgwyer/pen/ELMoNK) 。

我们可以使用伪类直接用 CSS 匹配输入值是否有效，以及在提交表单之前检查是否需要任何元素。

`:valid`

*   `:invalid`
*   `:required`
*   `:optional`(即不需要)
*   参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
    的笔 [用户界面选择器#3](https://codepen.io/dgwyer/pen/qYvpMP) 。

如果您开始在个人电子邮件输入框中输入内容，那么它必须是有效的。但是，工作电子邮件地址始终是必填的，并且需要有效，因此不能为空。还要注意我们如何链接伪类(例如，`:required:invalid`)来获得我们需要的东西。

接下来，我们有两个伪类可以匹配表单元素(支持`min`和`max`属性)是否在范围内。

参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
的笔 [用户界面选择器#4](https://codepen.io/dgwyer/pen/Jvzpgz) 。

这里的有效范围是在`10`和`20`之间。如果超出范围，您会看到红色背景。同样，我们可以使用 reset 按钮类型来重置 number input 元素的默认值。

为了圆满完成这一部分，让我们看看`:read-only`、`:read-write`和`:placeholder-shown`伪类。

参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
的笔 [用户界面选择器#5](https://codepen.io/dgwyer/pen/ZoPREB) 。

使用这些可以让你轻松匹配只读或可写(可编辑)的元素。匹配的元素也不必来自输入字段，如示例笔中所示。

最后，`:placeholder-shown`将匹配尚未交互的元素，并且仍然显示默认的占位符文本。

您可能希望使用更多的伪类来设计用户界面，例如:

当一个元素有焦点时:`(:focus)`

*   当用户悬停在一个元素上时:`(:hover)`
*   链接何时被访问:`(:visited)`
*   媒体处于不同状态时:`(:playing)`和`(:paused`。
*   可以咨询[本页](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes)了解更多伪类。

结构选择器

## 结构选择器非常强大，它根据元素在 DOM 中的位置匹配元素。它们让您可以灵活地用 CSS 匹配元素，否则需要 JavaScript 来做同样的事情。

这种类型的选择器不同于目前展示的选择器，因为其中一些选择器允许您传入一个参数来修改选择器的工作方式。

例如，`:nth-child()`有一个参数，用于匹配相对于它的特定子元素。该值可以是索引(从`1`开始)或表达式。

因此，如果我们有一个条目列表，下面的选择器将匹配第三个条目:

相反，它可以是一个简单的表达式，使伪类更加强大。

```
ul:nth-child(3)
```

有效表达式包括:

`ul:nth-child(2)`:匹配第二个子元素

*   `ul:nth-child(4n)`:匹配每第四个子元素(4，8，12，…)
*   `ul:nth-child(2n + 1)`:每隔一个子元素匹配一个(1，3，5，…)
*   `ul:nth-child(3n — 1)`:匹配每第三个子元素，偏移量为负一(2，5，8，…)
*   `ul:nth-child(odd)`:匹配奇数编号的元素(1，3，5，…)
*   `ul:nth-child(even)`:匹配偶数编号的元素(2，4，6，…)
*   表达式变量`n`总是从零开始，所以为了精确地计算出哪些元素将被匹配，从`n`开始作为零，然后`n`作为 1，以此类推来编译一个元素列表。

您可以使用`:nth-last-child()`来匹配反向计数的子元素(从最后一个子元素开始)。如果你想匹配某种类型的子元素，你可以使用向前计数的`:nth-of-type()`(从第一个兄弟元素开始)，和向后计数的`:nth-last-of-type()`。

您可以使用索引或表达式作为所有这些结构选择器的参数。

参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
的笔 [结构选择器#1](https://codepen.io/dgwyer/pen/MGxzEq) 。

`:nth-last-child()`和`:nth-last-of-type()`与`:nth-child()`和`:nth-of-type()`非常相似，只不过它们是从最后一个元素而不是第一个元素开始匹配的。

通过尝试不同的组合，你可以很有创造性地使用选择器。例如，前面的笔示例包含选择器:

它匹配最后一个无序列表中最后一个列表项的第二个元素之后的伪元素。如果你正在努力解码一个复杂的选择器，那么最好是从右向左读，这样就可以从逻辑上解构它。

```
ul:last-of-type li:nth-last-of-type(2)::after {
  content: “ (2nd from end)”;
  /* Other styles… */
}
```

下一组选择器是专门的结构化选择器，因为它们只匹配特定的子元素。你不能给他们传递表达式来修改他们的行为。

`:first-child`

*   `:last-child`
*   `:only-child`
*   `:first-of-type`
*   `:last-of-type`
*   参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
    的笔 [结构选择器#2](https://codepen.io/dgwyer/pen/JvzwJE) 。

See the Pen [Structural Selectors #2](https://codepen.io/dgwyer/pen/JvzwJE) by David Gwyer ([@dgwyer](https://codepen.io/dgwyer))
on [CodePen](https://codepen.io).

乍一看，这里发生了很多事情，使用这些类型的选择器时需要小心，因为可能会得到意想不到的结果。

例如，您可能想知道为什么**等等……**文本在`<section>`标签中是蓝色的。实际上，所有的部分内容都是蓝色的，因为它是主 div 容器的最后一个子容器。其他部分元素通过其他选择器覆盖它们自己的颜色，使单个段落保持蓝色。

CSS 内容选择器

## 内容选择器属于用于匹配内容的一组专用选择器。可供我们立即使用的有:

`::first-line`:匹配块级元素的第一行

*   `::first-letter`:匹配块级元素第一行的第一个字母
*   `::selection`:匹配文档中被用户高亮显示的部分(如在文本上点击和拖动鼠标)
*   参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
    的笔 [内容选择器#1](https://codepen.io/dgwyer/pen/xjBBqN) 。

See the Pen [Content Selectors #1](https://codepen.io/dgwyer/pen/xjBBqN) by David Gwyer ([@dgwyer](https://codepen.io/dgwyer))
on [CodePen](https://codepen.io).

`::first-line`和`::first-letter`仅适用于块级元素。另外，注意只在特定的元素上使用`::first-letter`,否则，每个段落都会有首字下沉，这可能不是你想要的！

目前还没有一些令人兴奋的内容选择器，但是当它们被支持的时候，它们将会打开所有的可能性。

以下是需要注意的内容选择器列表:

`::spelling-error`:检查可编辑元素的拼写和语法

*   `::grammar-error`:匹配语法错误
*   `::target-text`:URL 片段直接指向的文本。这是目前 Chrome 和 Edge 的一项实验性功能
*   杂项选择器

## 我们刚刚有时间提到几个不属于前面任何类别的选择器。别担心，我们快完成了！

`:target`选择器将 ID 与当前 URL 的一部分相匹配的元素作为目标。因此，如果我们有一个元素，它的`id`是`part1`和 URL:

我们可以将该元素与:

```
https://mysite.com#part1
```

如果你有一个大的选择器，那么`:is()`可以帮助简化它。例如，如果您有以下选择器:

```
:target { border: 1px red solid; }
```

那么可以用`:is()`来简化，相当于:

```
nav p.content,
header p.content,
main p.content,
sidebar p.content,
footer p.content {
  margin: 15px;
  padding: 10px;
}
```

不错！这将有助于使样式表更具可读性。

```
:is(nav, header, main, sidebar, footer) p:content {
margin: 15px;
padding: 10px;
}
```

接下来，我们有`:any-link`，这是一个方便的选择器，它的功能与`:link`和`:visited`的功能相同。

所以，这两个选择器实际上是一样的:

这就把我们带到了本文中要讨论的最后一个选择器:

```
:any-link {
  color: red;
}
:link, :visited {
  color: red;
}
```

这两者都与你网站的语言有关。

`:dir()`接受一个参数`ltr`或`rtl`，这取决于你想要匹配的文本的方向，并且它目前只在 Firefox 中受支持。

`:dir(rtl)`将内容与 RTL 方向匹配的所有元素。

HTML 文档中的每个元素都可以通过使用`lang`属性来设置自己的语言。

相同的基本文本被输入到三个`<div>`标签中，但是在内容的末尾添加了特定的国家。另外，`lang`属性中使用的国家代码代表相应的国家。

```
<div lang=”en”>The language of this element is set to English.</div>
<div lang=”el”>Η γλώσσα αυτού του στοιχείου έχει οριστεί στα ελληνικά.</div>
<div lang=”is”>Tungumál þessa þáttar er sett á íslensku.</div>
```

`en`:英语

*   `el`:希腊语
*   `is`:冰岛语
*   使用`:lang()`选择器可以匹配`<div>`元素:

这里有一支笔来演示:

```
:lang(en) { color: red; }
:lang(el) { color: green; }
:lang(is) { color: blue; }
```

参见 [CodePen](https://codepen.io) 上大卫·格怀尔( [@dgwyer](https://codepen.io/dgwyer) )
的笔 [杂项选择器](https://codepen.io/dgwyer/pen/xjBoNG)。

CSS 选择器的资源

如果您在试图找出一个选择器时遇到困难，或者需要深入研究 CSS 规范，这里有一些有用的资源，您可能想看看:

## CSS Diner :这是一个测试你选择器能力的有趣游戏

[MDN Web Docs:](https://developer.mozilla.org/en-US/)CSS 的首选资源

*   [CSS 规范](https://www.w3.org/Style/CSS/specs.en.html):具体为[选择器第 4 级、](https://www.w3.org/TR/selectors-4/) [CSS 伪元素模块第 4 级、](https://drafts.csswg.org/css-pseudo-4/)为最新的[选择器](https://www.w3.org/TR/selectors-4/)，伪类，伪元素
*   [我可以使用](https://caniuse.com/):来检查浏览器兼容性吗
*   结论
*   我希望这篇文章对你有用。我在温习 CSS 选择器技能和尝试各种可能性的过程中获得了很多乐趣。现在，使用纯 CSS 可以做很多很酷的事情，这对于那些可以在纯 CSS 中做一些非常高级的样式和动画的设计师来说是很令人兴奋的，而且所有这些都不需要一行 JavaScript 代码。快乐造型！

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

## .

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

[LogRocket](https://lp.logrocket.com/blg/css-signup) is like a DVR for web and mobile apps, recording everything that happens in your web app or site. Instead of guessing why problems happen, you can aggregate and report on key frontend performance metrics, replay user sessions along with application state, log network requests, and automatically surface all errors.

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).