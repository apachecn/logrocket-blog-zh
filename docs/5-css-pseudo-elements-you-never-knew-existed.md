# 你从来不知道存在的 5 个 CSS 伪元素

> 原文：<https://blog.logrocket.com/5-css-pseudo-elements-you-never-knew-existed/>

在本帖中，我将提请您注意一些您可能不知道或以前没有使用过的伪元素。主要目标是帮助您避免编写不必要的 JavaScript 代码行，因为您可以轻松地用 CSS 实现这些内容。

## 什么是伪元素？

根据 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements) 的说法，CSS 伪元素是添加到选择器中的一个关键字，它让您可以对所选元素的特定部分进行样式化。例如，`::first-line`可用于将样式应用于段落的第一行。

以下是伪元素的一般语法:

```
selector::pseudo-element {  
    property:  value;  
    }
```

有了上面的解释，我们现在可以回到我们的话题。我将列出一些你从来不知道存在的伪元素，并引起你的注意。

## 1.`::first-letter`伪元素

如果一个文本的第一个字母前面没有任何其他内容(比如图像或内联表格)，那么使用:`:first-letter`伪元素为该文本的第一个字母添加一种特殊的样式。这是博客作者非常常用的风格。它只能应用于块级元素。

例如，假设您想要设计一篇文章的第一个字母的样式，使其具有不同于其他字母的颜色和大小。使用`::first-letter`伪元素比使用 JavaScript 获取特定元素的首字母更容易做到这一点。

```
p:first-letter{
       font-size: 300%;
       color: red;
       float: left;
     }
```

您可能会想到的下一个问题是，我可以用`::first-letter`伪元素设计哪些属性？

以下属性适用于`::first-letter`伪元素:

*   `font`属性
*   `background`属性
*   `margin`属性
*   `padding`属性
*   `border`属性
*   `text-decoration`
*   `vertical-align`(仅当`float`为`none`时)
*   `text-transform`
*   `line-height`
*   `color`
*   `float`
*   等等。

见 [CodePen](https://codepen.io) 上 Olawanle Joel([@ olawanlejoel](https://codepen.io/olawanlejoel))
的笔 [首字母伪元素](https://codepen.io/olawanlejoel/pen/BaKXjeY)。

## 2.`::first-line`伪元素

`::first-line`伪元素用于向文本或块级元素的第一行添加特殊样式。第一行的长度可以由很多因素决定，比如元素的宽度、文档的宽度、文本的字体大小等等。

例如，如果我想设计一篇新闻博客文章的第一行，使用`::first-line`伪元素会更容易，而不是使用 JavaScript 来获得第一行。

```
p::first-line { 
     background-color: #fff;
     color: #000; 
     word-spacing: 10px;
    }

```

以下属性适用于`::first-line`伪元素:

*   `font`属性
*   `background`属性
*   `word-spacing`
*   `letter-spacing`
*   `text-decoration`
*   `vertical-align`
*   `text-transform`
*   `line-height`
*   `color`
*   `text-shadow`
*   等等。

见 [CodePen](https://codepen.io) 上 Olawanle Joel([@ olawanlejoel](https://codepen.io/olawanlejoel))
的笔 [一线伪元素](https://codepen.io/olawanlejoel/pen/ExKqPBx)。

## 3.`::selection`伪元素

`::selection`伪元素是用户高亮显示的文档部分或元素。它用于将样式应用于用户突出显示的文档部分(即，当用户在文本上单击并拖动他/她的鼠标时)。

默认的文本选择背景颜色是蓝色，此属性用于更改默认颜色。

语法:

```
::selection { 
        css declarations; 
    }
```

例如:

假设我想将背景色从默认颜色(蓝色)改为绿色。这可以使用`::selection`伪元素轻松实现。

```
::selection {  
    color:  red;  
    background:  yellow;  
    }
```

上述样式通常适用于该页面，但是您也可以决定将单个元素或 CSS 选择器作为目标:

```
p::selection {  
 color:  red;  
 background:  yellow;  
 }
```

只有三个属性`::selection`可以使用:

*   `color`
*   `background`属性(`background-color`、`background-image`等)。)
*   `text-shadow`

## 4.`::backdrop`伪元素

CSS 伪元素是视口的一个盒子大小，它直接呈现在全屏模式下的任何元素下面。这是全屏 API 的一部分，如果你想变得有艺术感或者有创造力，它会为你赢得一些积分。

当它是顶层中最顶层的元素时，`::backdrop`伪元素使得隐藏、样式化或完全隐藏位于该元素之下的所有东西成为可能。

每当您在浏览器中进入全屏模式时，大多数浏览器倾向于显示黑色背景。使用`::backdrop`伪元素，您可以将黑色背景更改为您喜欢的任何颜色！

语法:

```
::backdrop{ 
   css declarations; 
  }
```

例如，如果我想将视频切换到全屏模式时使用的样式更改为灰蓝色(而不是大多数浏览器默认的黑色)，我可以这样做:

```
video::backdrop {
  background-color: #448;
}
```

在 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/::backdrop) 上阅读更多关于背景虚拟元素的信息。你也可以查看这个[链接](https://fullscreen-requestfullscreen-demo.glitch.me/)的例子。

## 5.`::placeholder`伪元素

`::placeholder`伪元素选择带有占位符文本的表单元素，并允许您设置占位符文本的样式。

> **注意**:在大多数浏览器中，占位符文本的默认颜色是浅灰色。

例如，假设我的网站只使用了两种颜色:粉色和白色。如果我希望占位符文本是粉红色的，使用`::placeholder`伪元素可以很容易地实现。

```
<!-- HTML -->
< input  type="text"  placeholder="Enter your full name">

/* CSS */
::placeholder {  
color:  pink;  
}
```

上面的代码将改变所有连接到这个粉红色样式的占位符文本的颜色。但是，如果我们只是希望它应用于特定的领域或选定的领域呢？在这种情况下，我们必须添加选择器。

```
<!-- HTML -->
< input  type="text" class="name"  placeholder="Enter your name">

/* CSS */
.name::placeholder {  
color:  pink;  
}
```

因为我们实际上是在设计文本样式，所以您将使用的主要 CSS 属性是那些与文本样式相关的属性，如`color`、`font-size`、`font-weight`等。

见 [CodePen](https://codepen.io) 上 Olawanle Joel([@ olawanlejoel](https://codepen.io/olawanlejoel))
的笔 [占位符伪元素](https://codepen.io/olawanlejoel/pen/MWyNKMx)。

结论

## 这是许多开发人员忘记使用或者甚至没有意识到的五个伪元素。我希望这篇文章能够用清晰的例子恰当地解释它们。

感谢阅读！

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).