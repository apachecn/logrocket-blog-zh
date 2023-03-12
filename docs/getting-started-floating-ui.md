# 浮动 UI 入门

> 原文：<https://blog.logrocket.com/getting-started-floating-ui/>

## 介绍

浮动元素是“浮动”在 UI 顶部而不干扰内容流动的元素。工具提示是浮动元素的例子；它们是当用户悬停在特定区域时出现在页面上的短消息。我们可以使用工具提示来创建用户入职流程，向用户发送更新和提醒，提供关于某个特性的更多信息，等等。

Popper 一直以来都是创建浮动元素最流行的 JavaScript 库之一。然而，一个新玩家来了:它的继任者，[浮动 UI](https://floating-ui.com) 。

浮动 UI 有几个升级。它是跨平台兼容的，可以在 React 和 React 本机应用程序中使用。它比波普小；Popper 重 3kb，浮动 UI 600 字节。默认情况下，它也是可树摇动的，而 Popper 不是。浮动 UI 不仅仅是 Popper 的一个替代，而且是一个升级，有很多好处。

在本文中，我们将学习浮动用户界面，以及如何使用它来创建浮动元素。

## 关于浮动 UI

浮动 UI 是一个可扩展的低级库，用于创建[交互式元素](https://blog.logrocket.com/build-interactive-blog-with-react-sandpack/)，如工具提示、弹出窗口、下拉菜单、菜单等等。

浮动 UI 公开了原语，我们可以用它将一个浮动元素放置在给定的引用元素旁边。它还支持 web、React、React Native、WebGL、Canvas 等等。

## 入门指南

运行以下命令安装浮动 UI:

```
npm install @floating-ui/dom

```

我们还可以使用 ESM 或 UMD 格式通过 CDN 加载浮动 UI，如下所示:

```
<script type="module">
  import * as FloatingUIDOM from 'https://cdn.skypack.dev/@floating-ui/[email protected]';
</script>

```

## `computePosition`功能

`computePosition`函数是浮动 UI 的核心。它计算将浮动元素放置在给定的引用元素旁边所需的坐标，引用元素是触发浮动元素的元素。

让我们构建一个基本的工具提示来看看`computePosition`是如何工作的。

我们从设置 HTML 开始:

```
<!DOCTYPE html>
<html lang="en">
  <body>
    <button id="button" aria-describedby="tooltip">My button</button>
    <div id="tooltip" role="tooltip">My tooltip oltip with more content</div>
    <script src="/index.js" type="module" />
  </body>
</html>

```

接下来，我们设计工具提示的样式，并将其位置设置为`absolute`,这样它就可以浮动，不会干扰其他内容的流动。

```
#tooltip {
  color: #fff;
  background: #363636;
  font-size: 1.2rem;
  padding: 10px 15px;
  border-radius: 8px;
  position: absolute;
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.1), 0 3px 3px rgba(0, 0, 0, 0.05);
}

button {
  border-radius: 8px;
  border: none;
  outline: none;
  font-size: 1.2rem;
  cursor: pointer;
  padding: 10px 15px;
  color: #fff;
  background: rgb(48, 19, 129);
}

```

设置好工具提示的结构和样式后，让我们来研究功能:

```
import {computePosition} from 'https://cdn.skypack.dev/@floating-ui/[email protected]';

const button = document.querySelector('#button');
const tooltip = document.querySelector('#tooltip');

computePosition(button, tooltip).then(({x, y}) => {
  // Do things with `x` and `y`
  Object.assign(tooltip.style, {
    left: `${x}px`,
    top: `${y}px`,
  });
}); 

```

`button`是引用元素，`tooltip`是浮动元素。

我们可以将工具提示的位置更改为不同的位置，如下所示:

```
computePosition(button, tooltip, {
  placement: 'top-start',
})then(({ x, y }) => {
  //other code below
};

```

我们可以在 12 个核心位置放置元素:

*   `left-start`、`left`和`left-end`
*   `top-start`、`top`和`top-end`
*   `right-start`、`right`和`right-end`
*   `bottom-start`、`bottom`和`bottom-end`

浮动元素的默认位置是`bottom`。

## 中间件

中间件是在调用`computePosition`和最终返回之间运行的一段代码，用于修改或向消费者提供数据。它改变浮动元素的位置和行为。

中间件是基本布局定位之外的每一个功能的实现方式。

浮动 UI 提供了几个中间件:

*   `offset`在参考元素和浮动元素之间放置间距
*   `shift`移动浮动元素以确保其全部内容始终可见。它还通过处理剪裁和溢出问题来确保元素不会溢出到视口之外
*   `flip`为我们修改坐标，这样如果浮动元素太靠近视窗的顶部，那么`bottom`位置会自动将它定位在底部，反之亦然
*   处理浮动元素的大小调整
*   `autoPlacement`通过选择空间最大的位置，自动选择浮动元素的位置
*   `inline`改善跨多行的内嵌参考元素的定位，例如超链接

让我们用这些中间件来扩展基本工具提示的行为:

```
computePosition(button, tooltip, {
    placement: "top",
    middleware: [offset(4), flip(), shift({padding: 5})],
  }).then(({ x, y }) => {
    //other code below
});

```

上面，我们使用`offset`在工具提示和按钮之间添加了 4px 的间距。

除了修复内容剪辑问题之外，`shift`中间件接受一个选项对象，我们在其中定义工具提示和视口边缘之间的间距。我们将间距设置为 5px。

我们安排中间件的顺序很重要；`offset`必须始终位于数组的开头。

## 悬停时显示工具提示

目前，工具提示始终可见。然而，它应该只在我们悬停在按钮上时显示。

让我们来设置这个功能:

```
function setUpTooltip() {
  computePosition(button, tooltip, {
    placement: "top",
    middleware: [offset(4), flip(), shift({ padding: 5 })],
  }).then(({ x, y }) => {
    Object.assign(tooltip.style, {
      left: `${x}px`,
      top: `${y}px`,
    });
  });
}

function showTooltip() {
  tooltip.style.display = "block";
  setUpTooltip();
}

function hideTooltip() {
  tooltip.style.display = "none";
}

```

上面，我们将工具提示逻辑移动到函数`setUpTooltip`中，这样当我们希望工具提示显示时，我们可以调用该函数。

我们还创建了两个函数，`hideTooltip`和`showTooltip`。`hideTooltip`将工具提示显示设置为`none`。`showTooltip`将工具提示的显示设置为`block`和等级`setUpTooltip`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们想在鼠标离开按钮时调用`hideTooltip`,在鼠标悬停在按钮上时调用`showTooltip`:

```
[
  ["mouseenter", showTooltip],
  ["mouseleave", hideTooltip],
].forEach(([event, listener]) => {
  button.addEventListener(event, listener);
});

```

这里，我们将事件侦听器和函数附加到按钮上。这样，工具提示将只在悬停时出现。

下面是工具提示的最终代码:

```
import {
  computePosition,
  flip,
  shift,
  offset,
} from "https://cdn.skypack.dev/@floating-ui/[email protected]";

const button = document.querySelector("#button");
const tooltip = document.querySelector("#tooltip");

function setUpTooltip() {
  computePosition(button, tooltip, {
    placement: "top",
    middleware: [offset(4), flip(), shift({ padding: 5 })],
  }).then(({ x, y }) => {
    Object.assign(tooltip.style, {
      left: `${x}px`,
      top: `${y}px`,
    });
  });
}

function showTooltip() {
  tooltip.style.display = "block";
  setUpTooltip();
}

function hideTooltip() {
  tooltip.style.display = "none";
}

[
  ["mouseenter", showTooltip],
  ["mouseleave", hideTooltip],
  ["focus", showTooltip],
  ["blur", hideTooltip],
].forEach(([event, listener]) => {
  button.addEventListener(event, listener);
});

```

## 使用带有 React 的浮动用户界面

我们可以轻松地将浮动 UI 集成到 React 应用程序中。

首先，我们必须将 React 库安装到 React 应用程序中，如下所示:

```
npm install @floating-ui/react-dom

```

浮动 UI 提供了一个我们可以在 React 应用程序中使用的`useFloating`钩子。让我们使用这个钩子来设置 React 中的基本工具提示:

```
import { useFloating, shift, offset, flip } from "@floating-ui/react-dom";

export default function App() {
  const { x, y, reference, floating, strategy } = useFloating({
    placement: "right",
    middleware: [offset(4), flip(), shift({ padding: 5 })],
  });

  return (
    <>
      <button ref={reference}>Button</button>
      <div
        id="tooltip"
        ref={floating}
        style={{ top: y, left: x }}
      >
        Tooltip
      </div>
    </>
  );
}

```

`useFloating`钩子接受`computePosition`的所有选项，这意味着我们可以定义工具提示的位置并添加中间件。

## 结论

在本文中，我们了解了浮动 UI，它是如何工作的，它的不同特性，以及如何将它集成到 React 应用程序中。

虽然浮动 UI 比 Popper 提供了一些好处，但我希望看到的一件事是演示如何有条件地在 React 的悬停上显示工具提示。遗憾的是，文档中没有涉及到这一点。此外，由于这是一个新的库，因此几乎没有开发人员内容或支持。因此，虽然浮动 UI 是一个伟大的新工具，但这些是我们在使用它时应该考虑的事情。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)