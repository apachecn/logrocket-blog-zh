# 用 CSS 创建自定义鼠标光标

> 原文：<https://blog.logrocket.com/creating-custom-mouse-cursor-css/>

作为用户和网站之间的中间人，光标既可以限制也可以极大地增强用户体验网站的方式。这就是为什么精心设计的时尚自定义光标已经成为当今 UI 和 UX 的重要组成部分。

自定义光标是一个机会，以一种引人入胜的方式给你的用户指明方向，并在你的网站上为他们创造一个难忘的、身临其境的体验。

为许多潜在用户创建自定义游标。例子包括通过投影仪学习的学生、行动不便的用户以及某品牌的目标受众。

在本教程中，我们将看看什么是自定义光标，并学习如何使用 CSS(和 JavaScript)来创建自定义光标，这将给你的网站带来创造性的优势。我们将涵盖:

要跟随本教程，您应该对 HTML、CSS 和 JavaScript 有所了解。

## CSS 中光标的概述

我们已经每天都在与自定义光标进行交互。当您将鼠标悬停在按钮上并且指针光标变为手形时，或者当您将鼠标悬停在文本上并且光标变为文本光标时，这种交互性是通过自定义光标实现的。

然而，我们可以通过光标向用户提供许多其他的创造性体验。

在我们开始创建自己的自定义光标之前，您应该知道 CSS 为一些经常执行的任务提供了现成的光标。

这些光标向您显示在您悬停的确切位置可以做什么。示例包括指示您应该单击链接、拖放元素、放大和缩小事物等的光标。

你所要做的就是使用 CSS `cursor`属性指定你想要的光标类型。

CSS 中的一些可用光标包括:

```
.auto            { cursor: auto; }
.default         { cursor: default; }
.none            { cursor: none; }
.context-menu    { cursor: context-menu; }
.help            { cursor: help; }
.pointer         { cursor: pointer; }
.progress        { cursor: progress; }
.wait            { cursor: wait; }
.cell            { cursor: cell; }
.crosshair       { cursor: crosshair; }
.text            { cursor: text; }
.vertical-text   { cursor: vertical-text; }
.alias           { cursor: alias; }
.copy            { cursor: copy; }
.move            { cursor: move; }
.no-drop         { cursor: no-drop; }
.not-allowed     { cursor: not-allowed; }
.all-scroll      { cursor: all-scroll; }
.col-resize      { cursor: col-resize; }
.row-resize      { cursor: row-resize; }
.n-resize        { cursor: n-resize; }
.e-resize        { cursor: e-resize; }
.s-resize        { cursor: s-resize; }
.w-resize        { cursor: w-resize; }
.ns-resize       { cursor: ns-resize; }
.ew-resize       { cursor: ew-resize; }
.ne-resize       { cursor: ne-resize; }
.nw-resize       { cursor: nw-resize; }
.se-resize       { cursor: se-resize; }
.sw-resize       { cursor: sw-resize; }
.nesw-resize     { cursor: nesw-resize; }
.nwse-resize     { cursor: nwse-resize; }

```

将鼠标悬停在下面的方框上，查看光标的运行情况:

参见 [CodePen](https://codepen.io) 上 Samson Omo jola([@ Caesar 222](https://codepen.io/Caesar222))
的笔 [CSS 光标](https://codepen.io/Caesar222/pen/abYwvRY)。

[直接在 CodePen 上查看代码并与不同的光标互动。](https://codepen.io/Caesar222/pen/abYwvRY)

点击这里查看 CSS 光标的完整列表。

虽然这些光标很有用，并且有一些基本的样式，但我们当然可以更有创造性地使用光标。

## 如何用 CSS 创建自定义光标

用 CSS 创建自定义光标是一个非常简单的过程。您必须采取的第一步是找到您想要用来替换默认光标的图像。你既可以自己设计一个，也可以从 FontAwesome 这样的图标库中获得一个符合你需求的免费 PNG。

接下来，使用`url`将 CSS `cursor`属性指向图像的位置。现在`cursor`属性知道它应该使用 URL 上的任何图像作为它的光标:

```
body {
      cursor: url(‘path-to-image.png’), auto;
}

```

为了确保这个光标在你的网站的所有部分都被使用，使用`cursor`属性的最好地方是在你的 HTML 的`body`标签中。但是，如果您愿意，您可以将自定义光标分配给特定元素，而不是整个网站。

您还可以向您的`cursor`属性添加一个`fallback`值。当使用[自定义 CSS 属性时，该值确保](https://blog.logrocket.com/how-to-use-css-variables-like-a-pro/)如果作为您自定义属性的图像由于某种原因丢失或无法加载，那么您的用户将有另一种选择。

在这种情况下，`auto`是您的定制`cursor`属性的`fallback`描述符。如果自定义光标不可用，您的用户将会看到常规光标。

您可以提供多个自定义光标(多个备用)供您的网站使用。您只需将它们的路径添加到`cursor`属性中:

```
body {
      cursor: url(‘path-to-image.png’), url(‘path-to-image-2.svg’), url(‘path-to-image-3.jpeg’), auto;
}

```

上面的代码中有三个后备游标。

## 如何将鼠标光标变为指针

假设您有一个表格，并且希望每当用户将鼠标光标悬停在表格中的某一行上时，鼠标光标变为指针(例如，手形图标)。您可以使用 CSS `cursor`属性来实现这一点。

这里有一个例子:

```
<style>
  /* Style the table */
  table {
    font-family: arial, sans-serif;
    border-collapse: collapse;
    width: 100%;
  }

  /* Style the table cells */
  td, th {
    border: 1px solid #dddddd;
    text-align: left;
    padding: 8px;
  }

  /* Style the table rows */
  tr:hover {
    cursor: pointer;
  }
</style>

<table>
  <tr>
    <th>Name</th>
    <th>Age</th>
    <th>City</th>
  </tr>
  <tr>
    <td>John</td>
    <td>30</td>
    <td>New York</td>
  </tr>
  <tr>
    <td>Jane</td>
    <td>25</td>
    <td>Chicago</td>
  </tr>
  <tr>
    <td>Bill</td>
    <td>35</td>
    <td>Los Angeles</td>
  </tr>
</table>

```

在上面的代码中，当鼠标悬停在所有表格行上时，我们使用`tr:hover`选择器将`cursor`属性应用于这些行。将`cursor`属性设置为`pointer`，将鼠标光标变为手形图标。

## 如何用 CSS 隐藏鼠标光标

要用 CSS 隐藏鼠标光标，可以使用`cursor`属性并将其值设置为`none`。这里有一个例子:

```
<style>
  /* Style the body element */
  body {
    cursor: none;
  }
</style>

<body>
  <!-- Your content goes here -->
</body>

```

这将在整个网页中隐藏鼠标光标。如果您只想隐藏特定元素的鼠标光标，您可以将`cursor`属性应用于该元素，而不是`body`元素。

在几种情况下，隐藏鼠标光标可能是有用的。例如:

*   在游戏或交互式应用程序中，隐藏鼠标光标有助于为用户创造更加身临其境的体验
*   在演示文稿或幻灯片放映中，隐藏鼠标光标可以减少分心并保持对内容的关注
*   在全屏视频或媒体中，隐藏鼠标光标有助于防止用户意外点击控件或其他元素

请记住，根据不同的用例，隐藏鼠标光标可能会让一些用户感到困惑或不知所措。这种策略应该谨慎使用，并且只在必要时使用。

## 如何用 JavaScript 创建自定义光标

用 JavaScript [创建一个定制光标需要操作 DOM 元素](https://blog.logrocket.com/custom-events-in-javascript-a-complete-guide/)。

基本上，我们将创建一些 DOM 元素作为我们的自定义光标，然后使用 JavaScript 来操作它们。然后，当我们四处移动光标时，这些自定义元素将随着我们的光标四处移动。

我们将使用 CSS 来设计自定义光标，而不是设计图像或在线下载图像。我希望我们使用一些动画来吸引用户。将光标移至下面的方框，查看我所描述内容的示例:

参见 [CodePen](https://codepen.io) 上 Samson Omo jola([@ Caesar 222](https://codepen.io/Caesar222))
的笔[动画定制 CSS 光标](https://codepen.io/Caesar222/pen/abYwvRY)。

[直接在 CodePen 上查看完整代码并与此光标互动。](https://codepen.io/Caesar222/pen/yLKXKbe)

如您所见，光标由两个元素组成——一个大圆和一个小圆。我们将创建两个`div`元素，并将它们命名为`class`:

```
<div class="cursor small"></div>
<div class="cursor big"><div>

```

接下来，我们将使用 CSS 创建圆圈。在下面的代码中，我们给大圆分配了宽度和高度各为`50px`的值。为了使它成为一个圆，我们给它一个边界半径`50%`。

小圆会是空心的，所以我们给它一个边框，边框半径`50%`。然后，我们给它分配宽度和高度各为`6px`。

我们通过给`cursor`一个值`none`来禁用默认光标，这样我们可以在它的位置呈现自定义光标。

为了给大圆添加动画，我们使用`@keyframes`。

我们的`animation-duration`是`2.0s`。在这个持续时间的开始，我们将`background-color`设置为`green`，将`opacity`设置为`0.2`。中途，我们将圆的`background-color`设置为`orange`。在 2 秒持续时间结束时，我们将圆的`background-color`设置为`red`。

为了让动画一遍又一遍地重复，我们将`animation-iteration-count`设置为`infinite`:

```
body{
  background-color: #171717;
  cursor: none;
  height: 120vh;
}
.small{
  width: 6px;
  height: 6px;
  border: 2px solid #fff;
  border-radius: 50%;
}
.big{
  width: 50px;
  height: 50px;
  border-radius: 50%;
  margin-bottom: 20rem;
  animation-name: stretch;
            animation-duration: 2.0s;
            animation-timing-function: ease-out;
            animation-direction: alternate;
            animation-iteration-count: infinite;
            animation-play-state: running;
}

@keyframes stretch {
            0% {

                opacity: 0.2;
                background-color: green;
                border-radius: 100%;
            }

            50% {
                background-color: orange;
            }

            100% {
                background-color: red;
            }
        }

```

现在，为了让元素随着鼠标的移动而移动，我们将使用 JavaScript。

在下面的代码中，我们使用一个事件监听器来监听用户在网页上移动鼠标的时间。每当这个事件发生时，我们使用一个函数来获得鼠标的`x`和`y`坐标。之后，我们使用`x`和`y`坐标来移动我们的`div`元素，作为我们光标的代表:

```
const cursorSmall = document.querySelector('.small');
const cursorBig = document.querySelector('.big');

const positionElement = (e)=> {
  const mouseY = e.clientY;
  const mouseX = e.clientX;

  cursorSmall.style.transform = `translate3d(${mouseX}px, ${mouseY}px, 0)`;

  cursorBig.style.transform = `translate3d(${mouseX}px, ${mouseY}px, 0)`;

}

window.addEventListener('mousemove', positionElement)

```

[在 CodePen 上的交互式光标旁边查看完整的代码。](https://codepen.io/Caesar222/pen/yLKXKbe)

如您所见，我们使用了`querySelector`来访问 DOM 上的两个`div`元素。然后，我们向网页添加了一个事件监听器，用于监听和检测用户何时移动鼠标。

当这种情况发生时，事件监听器触发一个`positionElement`函数。在这个函数中，我们将这些坐标映射到已经选择了 DOM 位置的`div`元素。

我们使用`transform`和`translate3d`移动与鼠标的`x`和`y`坐标相对应的`div`元素。`transform`在水平和垂直方向重新定位元素，而`translate3d`在 3D 空间重新定位元素。

随着鼠标的移动，它的`x`和`y`值发生变化，`transform`和`translate3d`在事件监听器的帮助下得到更新，每个`div`的位置也相应变化。

## 创建自定义光标时的 UX 和浏览器兼容性

创建自定义光标时，您应该始终牢记用户的最佳利益。实现这一点的一种方法是创建一个最能代表或解释元素本质的光标。

虽然自定义光标给你的网站一个很好的接触和独特的感觉，但重要的是不要过度。过度定制你的光标会使你的用户在网站上的体验变得复杂，从而使他们感到沮丧和困惑。

在决定创建自定义光标之前，应该考虑其功能。例如，自定义光标的功能在旧浏览器上能正常工作吗？

请注意，一些较旧的浏览器不具备支持许多新的 CSS 和 JavaScript 特性的能力。如果你围绕自定义光标设计网站的某些区域，而这些区域使用了用户浏览器之外的技术，他们将无法参与你的网站。

## 创建考虑可访问性的自定义光标

如前所述，自定义光标对于行动不便或有其他辅助功能需求的用户很有帮助。例如，视力不好的人可能需要易于跟踪的真正大的鼠标指针，或者具有自定义颜色的鼠标指针，使它们在各种背景中突出。

鼠标也可以被编程来反转它所悬停的任何元素的颜色。这使得用户很容易跟踪他们在屏幕上阅读或看到的内容。文本光标或插入符号也可以加粗，这样视力不好的用户在输入时不会忘记光标在页面上的位置。

大光标也可以让有运动障碍的用户受益，因为它们比小光标更容易移动和放置在元素上。

## 结论

在本教程中，我们学习了现成的 CSS 光标，如何使用 CSS 创建自定义光标，如何为您的网页提供多个光标，以及如何使用 CSS 和 JavaScript 中的动画来创建自定义光标。

如果实施正确，自定义光标是吸引用户、保持用户参与并有效引导他们的好方法。如果你对用 CSS 创建自定义光标有任何进一步的想法或问题，请在评论部分告诉我。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。