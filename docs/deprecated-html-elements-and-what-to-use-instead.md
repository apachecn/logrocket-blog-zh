# 弃用的 HTML 元素(以及用什么代替)

> 原文：<https://blog.logrocket.com/deprecated-html-elements-and-what-to-use-instead/>

我们生活的方方面面都在不断变化。当然，技术世界也不例外。新的语言、框架、最佳实践和其他标准会定期推出。虽然这些变化并不总是惊天动地的，但我们仍然需要跟上它们，以避免遇到不必要的问题。

在本指南中，我们将概述 HTML5 带来的变化，并提出一些实现同等效果的替代方法。我们还会提供一些技巧来帮助你避免在你的应用中使用不推荐的 HTML 元素。(提示:通常涉及到使用 CSS 代替)。

我们将讨论从 HTML5 开始被否决的下列元素(以及它们推荐的 CSS 对应元素):

## 为什么不应该使用废弃的 HTML 元素

假设您正在运行几个月前构建的应用程序，您注意到页面是空白的或者不断返回错误。有什么可能是错的？

您查看控制台，注意到您使用的库已经更改了导入某些内容的过程，或者一些代码行不再以正确的语法编写。如果是制作网站，你已经知道这样的事件的负面效应。

显然，您不能监控所有的事情，但是您应该睁大眼睛，竖起耳朵，关注您日常使用的一些重要技术的变化。

HTML 存在于我们的许多代码库中。不管使用什么框架或库，底层的标记语言都是 HTML。这意味着，无论你编写 React、Vue.js、Svelte 还是其他任何一种语言，你的网站或应用程序都需要 HTML 才能正常运行。自然，这需要跟上 HTML 世界的变化。

HTML 中的一些元素在 HTML5 中被弃用。弃用元素意味着对它们的支持可能会随时停止，所以为了安全起见，最好使用更先进的技术。

许多不赞成使用的元素今天仍然被广泛使用，并且很好地服务于它们的目的。这些元素被弃用不是因为它们不好或无用，而是因为有更新的替代元素可以使用。这些选择可以是新的 HTML 标签或者 CSS 样式的形式。

让我们回顾一些已经失宠的主要 HTML 元素，并强调一些建议的替代元素。

## `<marquee>`

元素创建滚动内容。这可以是文本、图像、链接或其他元素。要让项目在页面上移动或滚动，您可以将它们包装在一个`<marquee>`标签中。

将许多功能打包到一个标签中。听起来很酷，对吧？嗯，是也不是。

虽然 marquee 标记使创建动画内容变得容易，但它本质上也是表示性的，这不是 HTML 元素所要求的。元素没有明确定义它的内容是什么。这也给使用辅助技术的人带来了问题。

除了在某些情况下看起来很舒服之外，使用 marquee 在页面上移动元素会造成糟糕的用户体验。很难阅读文本或看到字幕中的项目以及点击它们。有些人创建了嵌套链接的跑马灯，这显然使得访问这样的链接变得很困难。

这是否意味着我们不能再创作移动内容了？当然不是。创建移动对象可能很有趣，所以我们仍然可以实现 marquee 效果，而不使用不推荐使用的元素。

### 用 CSS 动画代替`<marquee>`

CSS 动画使您能够创建字幕效果，并通过使用`prefers-reduced-motion`媒体查询为那些不想要的人停止所有动画，使其对用户友好。您还可以根据用户的动作暂停动画。

让我们使用悬停时的 CSS `animation-play-state: paused`属性让动画在用户悬停时暂停:

```
 <!-- Using the marquee element -->
    <marquee>This uses the marquee element</marquee>

    <!-- Using CSS to create it -->
    <p class="moving-text">This is made with CSS</p>

    <!-- The styles -->
    <style>

        /* Makes the animation pause on hover */
        .moving-text:hover{
            animation-play-state: paused;
        }

        /* The animation */
        @keyframes marquee{
            0%{transform: translateX(100%);}
            100%{transform: translateX(-100%);}
        }

        /* media query to enable animation for only those who want it */
        @media (prefers-reduced-motion: no-preference) {
            .moving-text{
                animation: marquee 15s linear infinite;
            }
        }
    </style>

```

在上面的代码片段中，您会注意到使用`<marquee>`元素只需要一行代码就可以实现滚动效果，而对 CSS 做同样的事情需要几行代码。尽管如此，今天推荐的最佳实践是使用 CSS 创建样式和表示效果。

## `<acronym>`

有时写一个单词的首字母缩略词或缩写比写它的全文更容易。例如，当您可以简单地使用“HTML”时，您不想继续编写“超文本标记语言”但是你也想让你的读者知道缩短的文本意味着什么。

这就是`<acronym>`标签的由来。该标签使您能够显示缩写，并在悬停时显示缩写文本的完整含义。在它提供的标题属性的帮助下，当显示短文本时，标题属性中输入的内容在悬停时显示。

### 用`<abbr>`代替`<acronym>`

虽然`<acronym>`是一个非常有用的函数，但是现在已经被弃用了，所以你不应该使用它。相反，你应该使用一个新的替代物来做同样的事情:标签`<abbr>`。

`<abbr>`标签显示缩写词。当你悬停在它上面时，会显示全文或扩展。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

为了显示全文，您的`<abbr>`标记应该在 title 属性中有一个适当的标题:

```
<acronym title="World Wide Web">WWW (This uses the acronym tag)</acronym>

<abbr title="World Wide Web">WWW (This uses the abbr tag)</abbr>
```

上面的代码片段显示了如何使用这两个元素。每个都有相同的输出。

## `basefont`

当您需要将默认样式应用到网页上的所有元素时，`basefont`通常是首选。

`basefont`设置 HTML 文档中所有文本上下文的默认字体系列、字体大小和颜色。它适用于文档的所有内容。您页面的所有内容都按照`basefont`元素中的指定显示，除非另一种样式专门应用于它们。

如前所述，HTML 文档并不意味着控制页面的显示；这就是 CSS 的作用。用 CSS 很容易实现`basefont`元素的功能。

### 使用`font family`、`font size`和`color instead`

您可以通过样式化页面的 body 元素并使用 CSS 中可用的`font family`、`font size`和`color`属性来复制`basefont`的效果:

```
<body>
    <!-- how the basefont is used -->
    <basefont face = "cursive,serif" color = "green" size = "4"/>

    <p>Using the HTML base font</p>

    <p>Using CSS</p>

    <!-- the CSS alternative -->
    <style>
        body{
            font-family: cursive;
            color: green;
            font-size: 1rem;
        }
    </style>

</body>

```

## `<blink>`

`<blink>`标签的功能是为其中包含的文本创建闪烁效果。blink 标签中的一个元素闪烁，在页面上产生闪烁效果。

这种元素通常用于吸引访问者的注意力或用于装饰目的。出于各种原因，它已被弃用。

其中一个原因是，它给访问该页面的人带来了糟糕的用户体验。对于患有癫痫和其他残疾的人来说，暴露在他们无法控制的闪烁颜色的屏幕下也会产生不利影响。

### 使用 CSS `opacity`道具代替`<blink>`

闪烁效果本质上是表示性的，意味着它应该用 CSS 而不是 HTML 来创建。

如果您仍然想要为页面上的元素创建闪烁效果，您可以使用`opacity`属性通过 CSS 动画来实现。确保使用`prefers-reduced-motion`媒体查询为不希望动画出现在屏幕上的用户禁用动画。

```
<style>
  @keyframes blinker {
    50% {
      opacity: 0;
    }
  }
@media(preferes-reduced-motion: no-preference){
  .blink {
    animation: blinker 0.6s linear infinite;
  }
}
</style>

<body>
  <p class="blink">Blinking text with CSS</p>

  <blink>Created with blink element</blink>
</body>
```

## `<dir>`

元素用于列出或包装目录中的内容。它基本上用于包含目录标题。包装在`<dir>`中的物品通常是`<li>`元素。

### 用`<ol>`和`<ul>`代替`<dir>`

标签`<dir>`已经被弃用，取而代之的是几个功能更多、用途相同的替代标签。

包含在`<dir>`标签中的`<li>`元素有一个默认的项目符号列表样式类型。默认情况下，`<ol>`和`<ul>`元素等替代元素会根据使用的列表显示不同的列表样式。如果使用有序列表`<ol>`元素，列表显示为数字，如果使用无序列表`<ul>`，默认列表样式为项目符号。

`<ul>`和`<ol>`元素具有足够描述性的语义。如果使用了`<ul>`标签，你就知道会得到一个无序列表，如果使用了`<ol>`标签，你就知道列表是有序的。

```
<!-- using the dir element -->
    <dir>
        <li>I am one</li>
        <li>I am two</li>
    </dir>

    <!-- An alternative using the ul element -->
    <ul>
        <li>I am one</li>
        <li>I am two</li>
    </ul>
```

虽然这两个元素需要相同的代码行，但是让我们确保使用当前 web 标准 ul。

## `<frame>`

`<frame>`元素描述了页面上可以加载和显示另一个网页的特定位置或框架。它使您能够在一个网页中显示其他网站。页面上可以显示多个框架。

要使用`<frame>`元素，我们可以将其包装在一个`<frameset>`元素中，该元素用于指定布局，包括它将容纳的列数或行数以及每个框架的大小。`<frameset>`包装一组框架，并在网页上将它们作为一个单元进行样式设置。

### 用`<iFrame>`代替`<frame>`

`<frame>`元素现在已经过时，这意味着它已经被完全删除，不应该在您的代码库中使用。由于性能问题和使用屏幕阅读器的人缺乏可访问性，对`<frame>`元素的支持被停止。

虽然您不能再使用`<frame>`元素来嵌入页面和其他媒体项目，但是我们可以使用`<iFrame>`元素来实现同样的功能。`<iFrame>`执行与`<frame>`元素相同的功能，不需要嵌套在`<frameset>`标签中。

`<iFrame>`标签用于在页面上显示网页和媒体:

```
    <!-- Using the frame element -->
    <frameset cols="50%, 50%">
        <frame src="https://google.com"/>
        <frame src="https://logrocket.com"/>
    </frameset>

    <!-- using iframe -->
    <iframe 
        title="iframe demo"
        src="https://logrocket.com"
    ></iframe>
```

## `<strike>`

`<strike>`元素呈现中间有一条水平线的文本。元素用于描述已删除或不再相关的内容。

### 用`text-decoration`代替`<strike>`

从 HTML5 开始，`<strike>`已经过时，浏览器对它的支持很少。

为了在文本上实现相同的删除线效果，可以使用`<del>`元素，或者更好的是，使用 CSS `text-decoration`属性来设置它的样式。如果你想描述已经被删除的文本内容，使用`<del>`元素是合适的，因为它有语义含义，描述了包装在标签中的文本的状态。

如果删除线效果纯粹是为了视觉效果，那么使用 CSS `text-decoration`属性是最好的选择。

```
<!-- Using the strike element -->
<strike>Using the strike element</strike>

<!--Using CSS to get same effect-->
<p>Creating same effect with CSS</p>

<style>
  p{
    text-decoration: line-through;
  }
</style>

```

## `<center>`

顾名思义，`<center>`元素将内容水平居中对齐。元素使得文本居中变得非常简单；你所要做的就是将文本放在中央标签中，这样你就有了一个水平居中的文本块。

那么，为什么`<center>`被弃用了呢？由于其表象的本质，它被弃用。`<center>`元素定义了文本的外观，而不是描述内容。例如，它没有说明它的内容是一个段落、一个部分还是一个浮动在文档中的图像。

虽然`<center>`元素很好地服务于它的居中目的，但是它没有完成定义其内容的语义角色。还有一个问题是屏幕阅读器不能很好地使用它，等等。

### 使用`text-align: center`代替

使用`<center>`标签并不是让页面内容居中的唯一方法。使用 CSS 中的`text-align: center`属性可以达到同样的效果。你也可以使用`margin`和`flexbox`属性。

```
    <!-- Using the center element -->
    <center>Hi</center>

    <!-- Centering text using CSS -->
    <p>Hi</p>

    <style>
        p{
            text-align: center;
        }
    </style>

```

## 结论

在代码中使用不推荐使用或过时的元素会给代码库带来深远的长期问题。对于不包含不赞成使用或过时元素的每一行代码，您已经主动修复了一个即将出现的问题。

与其在代码库出现问题后才更新 HTML 元素，不如在编写代码和构建 web 时跟上变化并遵循当前的 web 标准。

请记住，防止问题出现比不得不不断解决问题要好。毕竟，没有人想成为这样的同事或前雇员，留下一个充满不赞成使用的元素和错误的代码库，让其他人去修复。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)