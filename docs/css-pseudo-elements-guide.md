# CSS 伪元素指南- LogRocket 博客

> 原文：<https://blog.logrocket.com/css-pseudo-elements-guide/>

***编者按**:这篇文章于 2022 年 9 月 29 日更新，包括 CSS 伪类和伪元素的比较，以及每个伪元素的工作代码演示。*

使用基本的 CSS 选择器及其各种属性很有趣，但是学习伪类和伪元素是成为 CSS 专家的下一步。

作为一名前端开发人员，你应该有 CSS 伪元素的工作知识，包括它们的功能和它们不同的表示和结构应用。

本文涵盖了伪元素的详细概述，它们与伪类的区别，它们不同的类型和用例，最新模块中的新增内容。

向前跳:

## 什么是 CSS 伪元素？

一个 [CSS 伪元素](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements)主要是添加到一个 [CSS 选择器](https://www.w3schools.com/cssref/css_selectors.asp)中的一个关键字，该选择器允许你对所选 HTML 元素的特定部分进行样式化。它作为子元素，为所选实体提供附加功能。

伪元素是在 2015 年首次引入的，只有一个冒号语法。CSS3 后面的模块使用双冒号伪元素语法，如下所示:

```
/* Older way (CSS2) */
selector:pseudo-element {
  property: value;
}

/* Modern way (CSS3 onwards) */
selector::pseudo-element {
  property: value;
}

```

如上所示，双冒号关键字是我们的伪元素，也通过它们的名称来指示它们的功能。我们将在接下来的部分逐一介绍。

### 伪元素与伪类

CSS 伪类是所选元素经历一个事件或一系列事件时的状态。您可以使用伪类来更改特定事件的元素样式。

相比之下，CSS 伪元素的行为类似于子元素本身，并根据其类型向所选元素添加不同的功能。

## CSS 伪元素的类型

我们将在本文的后面部分介绍一些特定于浏览器的实验性伪元素。目前，这里列出了现代浏览器支持的不同伪元素:

1.  `::before`
2.  `::after`
3.  `::first-letter`
4.  `::first-line`
5.  `::marker`
6.  `::placeholder`
7.  `::selection`
8.  `::backdrop`
9.  `::file-selector-button`
10.  `::cue`
11.  `::part()`
12.  `::slotted()`

我们将在本教程中看到的所有例子都可以在[这个 codepen 集合](https://codepen.io/collection/JGzmbo)中找到。您也可以复制粘贴代码，并在您选择的代码编辑器中使用它。

### `::before`

关键字`::before`创建一个伪元素，出现在所选 HTML 元素的内容之前。

默认情况下，它有一个内联显示，需要提供 CSS 内容属性才能工作。请看下面的代码片段，了解`::before`伪元素的语法:

```
.pe-before::before {
  content: "Content injected by the ::before pseudo-element.";
  display: block;
  margin-bottom: 1em;
}

```

上面的代码通过使用`::before`伪元素，用一个`pe-before`类向每个元素注入一些内容。[看这里的演示](https://codepen.io/_rahul/pen/NWMOpVB)。前端开发世界通常将这种类型的注入内容称为生成内容。

这里还有一个例子来演示`::before`的一个高级应用。

让我们以标签列表为例，在标签实体之前显示标签图标或文本是完全有意义的。

使用 flexbox 属性和`::before`伪元素进行一些对齐和间距调整，使其非常容易实现:

```
.tag-list {
  display: flex;
  gap: 1em;
}

.tag-list li,
.tag-list a {
  display: block;
}

.tag-list a::before {
  content: "#";
  ...
}

```

参见 [CodePen](https://codepen.io) 上 Rahul ( [@_rahul](https://codepen.io/_rahul) )
)的前带 CSS::的 Pen [标签列表。](https://codepen.io/_rahul/pen/ExLdmVP)

`::after`

### 除了不是出现在所选 HTML 元素的内容之前，而是出现在它之后，`::after`伪元素的工作方式与`::before`相似。

和`::before`一样，`::after`在默认情况下有一个内嵌显示，并使用一个内容属性来工作。下面的示例演示了它的语法:

点击此处查看运行中的代码:

```
.pe-before::after {
  content: "Content injected by the ::after pseudo-element.";
  display: block;
  margin-top: 1em;
}

```

参见 [CodePen](https://codepen.io) 上 Rahul([@ _ Rahul](https://codepen.io/_rahul))
的笔 CSS::伪元素后。

See the Pen [CSS ::after pseudo-element](https://codepen.io/_rahul/pen/LYmgWwR) by Rahul ([@_rahul](https://codepen.io/_rahul))
on [CodePen](https://codepen.io).

对于`::after`伪元素的高级实现，可以考虑用项目间的正斜杠构建一个“面包屑”导航。

这里有一个简单的方法，通过`::after`和一些校准调整来实现:

参见 [CodePen](https://codepen.io) 上拉胡尔([@ _ 拉胡尔](https://codepen.io/_rahul) )
)用 CSS ::after 的笔[。](https://codepen.io/_rahul/pen/vYjVxoZ)

`::first-letter`

自动定位给定文本块的首字母有助于创建丰富的排版增强功能，如首字下沉。

### 这样做听起来可能有点棘手，但是 CSS 中的`::first-letter`伪元素使它变得相对简单。它表示 block 元素第一行的第一个字母，只有当目标元素的第一个子元素是文本块时才有效。

以下是针对所有段落标签的第一个字母并使它们看起来更粗一点的简单方法:

参见 [CodePen](https://codepen.io) 上 Rahul ( [@_rahul](https://codepen.io/_rahul) )
的笔[CSS::首字母伪元素](https://codepen.io/_rahul/pen/rNvQBrx)。

Here’s how easy it is to target the first letter of all the paragraph tags and make them appear a bit bolder in weight:

```
p::first-letter {
  font-weight: 700;
}

```

大多数基于杂志的博客布局实现了`::first-letter`伪元素来突出文章第一段的第一个字母。这就是众所周知的首字下沉效应:

参见 [CodePen](https://codepen.io) 上 Rahul ( [@_rahul](https://codepen.io/_rahul) ) 【T5)的 CSS::首字母的 Pen [首字下沉效果。](https://codepen.io/_rahul/pen/qBYQWym)

Most magazine-based blog layouts implement the `::first-letter` pseudo-element to highlight the first letter of the first paragraph of an article. It is popularly known as the drop cap effect:

```
main p:first-child::first-letter {
  text-transform: uppercase;
  font-weight: 700;
  font-size: 3em;
  line-height: 1;
  float: left;
  margin: 0 0.5rem 0.1rem 0;
}

```

认识到带有`::first-letter`的元素的第一个字母有时很难识别是至关重要的。例如，如果一个文本在开头包含一个标点符号，伪元素将逻辑上选择该符号，而不是实际的第一个字母。

另一个例子可以是一个[有向图](https://en.wikipedia.org/wiki/Digraph_(orthography))或一个[三向图](https://en.wikipedia.org/wiki/Trigraph_(orthography))，在这里你会想要选择所有的两个或三个字母。在这种情况下，`::first-letter`伪元素只能选择第一个。

另外，注意使用`::before`和`::first-letter`。`::before`伪元素充当给定元素的第一个子元素。这将导致`::first-letter`在选择时更喜欢由它添加的生成内容而不是实际内容。

`::first-line`

使用`::first-line`，我们可以轻松地选择块元素中的第一行文本。这个伪元素考虑元素的宽度和字体大小，以及文档的宽度来确定选择:

### 上面的代码将使每个段落元素的第一行变得更粗。请点击此处查看快速演示:

参见 [CodePen](https://codepen.io) 上 Rahul ( [@_rahul](https://codepen.io/_rahul) )
的笔[CSS::一线伪元素](https://codepen.io/_rahul/pen/gOzBWWK)。

```
p::first-line {
  font-weight: 700;
}

```

The code above will make the first line of every paragraph element bolder in weight. See a quick demo of the same here:

让我们更进一步，改变文章第一行的大小写。这可以通过将`::first-line`与`:first-child`伪类配对来完成:

见 [CodePen](https://codepen.io) 上用 CSS 突出第一行的笔[:](https://codepen.io/_rahul/pen/bGMmWWO)Rahul([@ _ Rahul](https://codepen.io/_rahul))
。

`::marker`

这个伪元素使我们能够选择和修改列表项的标记框中的项目符号图标和编号。它适用于任何带有`list-item`显示屏的东西。`<li>`和`<summary>`元素是它的一些通用应用:

请点击此处观看演示:

### 参见 [CodePen](https://codepen.io) 上 Rahul ( [@_rahul](https://codepen.io/_rahul) )
的笔[CSS::marker 伪元素](https://codepen.io/_rahul/pen/vYjQRZV)。

This pseudo-element enables us to select and modify the bullet icon and number in the marker box of a list item. It works with anything with a `list-item` display. `<li>` and `<summary>` elements are some of its general applications:

```
li::marker {
  content: "♥";
}

```

正如您在上面看到的，`::marker`伪元素使得`::before`伪元素变得不必要。如果将来允许在标记和文本之间增加间距，那就太好了。

向无序列表添加自定义标记是一件轻而易举的事情。让我们对可公开的摘要也做一些类似的事情:

参见 [CodePen](https://codepen.io) 上 Rahul ( [@_rahul](https://codepen.io/_rahul) )
用 CSS ::marker 对细节披露元素进行样式化的笔[。](https://codepen.io/_rahul/pen/Jjveyje)

`::placeholder`

顾名思义，`::placeholder`伪元素允许您对表单输入元素的占位符进行样式化。默认情况下，大多数浏览器都有浅灰色的占位符。下面是我们如何使用下面的 CSS 代码来修改它们:

在下面的演示中，我试图使占位符文本的颜色看起来与字段的背景相关。CSS HSL 颜色函数对于这种情况很方便；您可以保持色调不变，并通过仅修改亮度和饱和度来生成不同的阴影和色调:

参见 [CodePen](https://codepen.io) 上 Rahul ( [@_rahul](https://codepen.io/_rahul) )
的笔[CSS::占位符伪元素](https://codepen.io/_rahul/pen/LYmXPMz)。

### `::selection`

通常，当您选择网页上的文本时，所选内容会以蓝色突出显示。`::selection`伪元素使您能够定制这种高亮显示的样式:

```
input::placeholder {
  color: blue;
}

```

您可以将它挂接到主体或根元素，以便将更改应用到每个元素。下面的演示展示了它的两个不同元素的实现:

参见 [CodePen](https://codepen.io) 上 Rahul ( [@_rahul](https://codepen.io/_rahul) )
的笔[CSS::占位符伪元素](https://codepen.io/_rahul/pen/dyeQyZg)。

`::backdrop`

### CSS 伪元素表示一个视窗大小的框，它直接呈现在全屏模式下的任何元素下面。

让我们通过一个例子来理解这一点，在这个例子中，我们将视频的背景从黑色改为蓝色。查看此 [CodePen 示例](https://codepen.io/_rahul/pen/wvjQeyv)并以全屏模式播放视频:

```
::selection {
  background-color: #ccc;
  color: #888;
}

```

它也适用于对话框元素，当启动时，它会获得一个可定制的背景颜色。单击下面演示中的“显示对话框”按钮，查看它的运行情况:

参见 [CodePen](https://codepen.io) 上 Rahul ( [@_rahul](https://codepen.io/_rahul) )
的<对话> 中的 Pen [CSS::back up in action。](https://codepen.io/_rahul/pen/gOzQREO)

### `::file-selector-button`

HTML 文件输入元素显示了一个似乎不可能用 CSS 样式化的按钮。令您惊讶的是，`::file-selector-button`伪元素实际上允许您定制该按钮，下面是一个工作演示:

在向这个伪元素添加样式时，您可能不喜欢指定输入类型。我建议你总是提到类型，让别人看得懂。使用`::file-input-button`查看添加了一些内容的文件输入看起来有多整洁:

```
video::backdrop {
  background-color: blue;
}

```

参见 [CodePen](https://codepen.io) 上 Rahul ( [@_rahul](https://codepen.io/_rahul) )
在 CSS 中输入的[笔样式文件上传。](https://codepen.io/_rahul/pen/rNvQGBg)

`::cue`

CSS 伪元素选择媒体元素(通常是视频)中的 WebVTT 提示。简而言之，它允许您使用 VTT 音轨来设计字幕、副标题和其他可能的媒体提示:

### 为了避免 CORS 问题，请确保视频和 VTT 文件来自同一个域。下面是由`::cue`驱动的 VTT 字幕的样子:

`::part()`

```
input[type="file"]::file-selector-button {
  background-color: blue;
  color: white;
  ...
}

```

由于其元素封装的性质， [shadow DOM](https://www.w3.org/TR/shadow-dom/) 与页面的其余部分保持隔离。因此，并不是所有的样式都能从影子 DOM 到达附加到常规 DOM 的组件。

`::part()`伪元素是 CSS 伪元素中的一个新成员，它使得在一定程度上对影子 DOM 进行样式化成为可能:

分配一个“part”在这里扮演了一个重要的角色，它将帮助我们稍后使用`::part()`伪元素来修改上面的组件。

### 下面的代码块展示了它如何像一个函数一样工作，并将`part`作为一个参数:

下面是我们讨论的关于`::part()`的所有内容的一个小实现:

```
<style>
  video::cue {
    color: pink;
    background-color: black;
  }
</style>
<video controls
       src="./path/to/video.file">
    <track default
           kind="captions"
           srclang="en"
           src="./path/to/vtt.file">
</video>

```

参见 [CodePen](https://codepen.io) 上 Rahul ( [@_rahul](https://codepen.io/_rahul) )
的 Pen [CSS ::part()伪元素](https://codepen.io/_rahul/pen/gOzQGgo)。

`::slotted()`

```
shadow DOM 中的槽是占位符，用于保存自定义 web 组件标记之间的内容。插槽的一个限制是不能在影子 DOM 树中设计它们的样式。
引入了槽式伪元素计数器，通过将槽式元素作为参数来添加样式。下面是一个简单的`::slotted`实现，使用的是上面的例子:
参见 [CodePen](https://codepen.io) 上 Rahul ( [ @_rahul ](https://codepen.io/_rahul) ) 
的 Pen[CSS::slotted()伪元素](https://codepen.io/_rahul/pen/OJZaxer)。

```
<template id="my-widget">
    <div part="widget">
      <p>...</p>
    </div>
</template>

```

Assigning a “part” plays an important role here and will help us later to alter the above component using the `::part()` pseudo-element.
特定于浏览器的伪元素

```
my-widget::part(widget) {
  ...
}

```

在生产中使用特定于浏览器的伪元素是不切实际的，因为如果其他浏览器没有其他替代方法，这只会增加不一致性。
这类伪元素中比较流行的有`::-moz-appearance`、`::-webkit-appearance`、`::-webkit-search-cancel-button`。外观伪元素用于根据操作系统的主题控制 UI 控件的本机外观。
多年来，Web 开发人员一直使用`::webkit-search-cancel-button`来隐藏基于 WebKit 的浏览器上 HTML 搜索输入中难看的蓝色“取消”搜索控件:
看 [CodePen](https://codepen.io) 上用 CSS 隐藏 webkit 搜索取消按钮的笔[、Rahul ( ](https://codepen.io/_rahul/pen/WNJYXBG)[ @_rahul ](https://codepen.io/_rahul) ) 
。
实验伪元素
目前，一些 CSS4 伪元素仍处于实验模式，这意味着它们仍在开发中，不会在任何浏览器中按预期工作。

```
<template id="my-widget">
  <style>
    div ::slotted(span) {
      color: red;
    }

    section ::slotted(span) {
      color: green;
    }
  </style>
  <div>
    <slot name="div">This is a div slot</slot>
  </div>
  <section>
    <slot name="section">This is a section slot</slot>
  </section>
</template>

```

主要有四种伪元素属于这一类别:
`::target-text`:如果浏览器支持滚动到文本片段，那么`::target-text` CSS 伪元素将允许我们突出显示滚动目标
它将代表被用户代理标记为语法错误的文本段
`::grammar-error`:它将代表被用户代理标记为拼写错误的文本段
`::cue-region`:与`::cue`不同，它将用于样式化整个提示区域，而不仅仅是提示文本
结论
在上面的内容中，我们学习了 CSS 伪元素及其不同的应用。我们还介绍了一些实验性的和特定于浏览器的伪元素，以及为什么目前在生产中使用它们不实际。
尽管伪元素增加了很多功能，而这些功能是用 JavaScript 添加的，但是避免过度使用伪元素会让你的布局更简洁、无故障。
我希望你通过这篇教程增加了更多的 CSS 知识。请在评论中告诉我你的想法。
你的前端是否占用了用户的 CPU？
随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

*   .
*   LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。
*   现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。
*   `::cue-region`: Different from `::cue`, it will be used to style the whole cue region instead of just the cue text

Conclusion
In the contents above, we learned about CSS pseudo-elements and their different applications. We also introduced some experimental and browser-specific pseudo-elements, and why it is currently not practical to use them in production.
Even though pseudo-elements add a lot of functionalities that you would be adding with JavaScript otherwise, avoiding their aggressive use will keep your layouts lighter and glitch-free.
I hope you added a bit more to your CSS knowledge with this tutorial. Let me know your thoughts in the comments.

## Is your frontend hogging your users' CPU?

As web frontends get increasingly complex, resource-greedy features demand more and more from the browser. If you’re interested in monitoring and tracking client-side CPU usage, memory usage, and more for all of your users in production, 

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

[LogRocket](https://lp.logrocket.com/blg/css-signup) is like a DVR for web and mobile apps, recording everything that happens in your web app or site. Instead of guessing why problems happen, you can aggregate and report on key frontend performance metrics, replay user sessions along with application state, log network requests, and automatically surface all errors.

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).

```