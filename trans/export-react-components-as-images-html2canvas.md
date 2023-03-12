# 使用 html2canvas - LogRocket Blog 将 React 组件导出为图像

> 原文：<https://blog.logrocket.com/export-react-components-as-images-html2canvas/>

作为一名开发人员，您可能总是在寻找新的方法或解决方案，以使您构建的应用程序更加有用。例如，如果您正在开发一个生成图形数据的应用程序，您可能希望为用户提供导出一些内容的功能，以便在以后共享或参考。

许多数据可视化库允许用户导出图像。然而，如果你正在寻找一个可以一次从一个页面导出多个表格和图形的库，那么 [html2canvas](https://html2canvas.hertzen.com) 可能是你的解决方案。

在本教程中，我们将演示如何使用 html2canvas 将 React 组件导出为图像。

但首先，让我们开始解决一些基本问题:

## html2canvas 是什么？

html2canvas 是一个 JavaScript 库，使用户能够制作整个网页或网页部分的截图。截图直接在用户的浏览器上拍摄。

从技术上讲，html2canvas 实际上并不截屏，而是根据页面上的数据创建一个视图。这会产生一个 HTML5 canvas 元素，可用于创建图像文件或在用户屏幕上生成屏幕截图的预览。

## 它是如何工作的？

html2canvas 将网页作为画布图像读取。它遍历网页的 DOM，读取所有当前的元素和样式。在 html2canvas 收集了所有的页面结构信息之后，它创建了一个页面的表示。由于所有的图像都是在用户的浏览器上创建的，html2canvas 不需要任何服务器端的渲染。

最终的图像非常接近网页的复制品，但不是 100%完美。这是因为它是网页的再现，而不是实际的截图。

## 如何使用？

若要使用 html2canvas 呈现元素，请使用以下语法:

```
html2canvas(element[options]);

```

`html2canvas`函数接受 DOM `element`并返回一个包含`<canvas>`元素的`P``romise`。

接下来，我们将使用`then()`承诺履行处理程序:

```
const captureElement = (element) => {
html2canvas(element).then(canvas => {
document.body.appendChild(canvas)
})
}

```

或者，我们也可以使用`async-await`语法来避免回调。

```
const captureElement = async (element) => {
const canvas = await html2canvas(element);
document.body.appendChild(canvas);
}

```

我个人更喜欢`async-await`语法，因为它比将`.then()`链接到`Promise`要干净得多。

## 有哪些局限性？

html2canvas 有一些限制。例如，该库不能呈现像 Java 小程序或 Flash 这样的内容插件。

另外，html2canvas 只呈现它能理解的属性。有许多 CSS 属性，如`box-shadow`或`border-image`，目前不支持这个库。这里有一个[支持和不支持 CSS 属性的完整列表](https://html2canvas.hertzen.com/features/)。

另一个限制是，如果没有代理，html2canvas 将无法读取任何包含跨原点内容的 canvas 元素。跨源图像是那些从第三方或另一个域加载的图像。示例包括 iframes、样式表或脚本。

## 演示:将 React 组件导出为图像

现在我们已经深入了解了 html2canvas 是什么以及它是如何工作的，是时候学习如何使用这个库将 React 组件导出为图像了。

### 设置用户界面

首先，我们将在 JSX 标记中创建一个`<div>`容器。我们将使用它来指定一个区域作为图像下载。我们还将添加一个带有`onClick`事件处理程序的`button`来下载图像。

```
import React from "react";
import "./styles.css";

export default function App() {
return (
<>
<div className="parent">
<div>
<p>
Quis blandit turpis cursus in hac habitasse. Commodo quis imperdiet
massa tincidunt nunc pulvinar sapien et ligula. Sit amet dictum sit
amet justo donec. Cursus mattis molestie a iaculis. Vel pretium
lectus quam id leo in vitae. Quam nulla porttitor massa id neque
aliquam vestibulum morbi blandit.
</p>
</div>
</div>
<button>
Capture Image
</button>
</>
);
}

```

### 实现逻辑

一旦我们设置了用户界面，下一步就是添加代码，允许我们将 React 组件作为图像下载。为此，我们将遵循三个步骤:

1.  安装 html2canvas npm 包

```
npm i html2canvas
```

2.  导入并使用`html2canvas`功能

为此，我们将在`src`文件夹中创建一个`utils`目录，然后创建一个新文件`exportAsImage.js`:

```
import html2canvas from "html2canvas";

const exportAsImage = async (el, imageFileName) => {
const canvas = await html2canvas(element);
const image = canvas.toDataURL("image/png", 1.0);
// download the image
};

```

在上面的代码片段中，`html2canvas`在参数中获取一个 DOM 元素，然后使用该元素创建一个画布图像。然后，它[返回一个包含画布元素的承诺](https://blog.logrocket.com/javascript-promises-race-all-allsettled-then/)。

3.  下载导出的图像

```
import html2canvas from "html2canvas";const exportAsImage = async (el, imageFileName) => {
const canvas = await html2canvas(element);
const image = canvas.toDataURL("image/png", 1.0);
downloadImage(image, imageFileName);
};const downloadImage = (blob, fileName) => {
const fakeLink = window.document.createElement("a");
fakeLink.style = "display:none;";
fakeLink.download = fileName;

fakeLink.href = blob;

document.body.appendChild(fakeLink);
fakeLink.click();
document.body.removeChild(fakeLink);

fakeLink.remove();
};

export default exportAsImage;

```

在`downloadImage`函数中，我们在内存中创建一个锚链接来模拟点击并下载图像。一旦点击事件被模拟，假锚链接`fakeLink`将从 DOM 中删除。

接下来，在`App.js`文件中，我们导入`exportAsImage`函数并将其附加到按钮的`onClick`处理程序。我们使用`useRef``()`钩子创建一个对 DOM 元素的引用，并把它和文件名一起传递给`exportAsImage`函数。

```
export default function App() {
const exportRef = useRef();

return (
<>
<div className="parent">
<div ref={exportRef}>
<p>...</p>
</div>
</div>
<button onClick={() => exportAsImage(exportRef.current, "test")}>
Capture Image
</button>
</>
)
}

```

这就是将 React 组件作为映像下载的过程！

## 处理水平溢出

html2canvas 在导出垂直布局的图像时几乎可以完美地工作。但是，它在水平布局方面有一些限制。该库只能捕获可见元素。任何在视窗之外的元素都将被排除在最终的屏幕截图之外。类似地，没有显示在屏幕上的元素的任何部分也将从最终的屏幕截图中排除。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

不幸的是，垂直布局不适合许多 React 应用程序。一个例子是，在一个页面上需要包含许多表格、图像或图表。在垂直布局中，并排比较图形或表格更加困难。图像通常会非常高，这不会带来良好的用户体验。

这个问题的一个解决方法是将`html`和`body`标签设置为一个较大的值，这样它们可以轻松容纳所有可用的数据，这样容器就不会隐藏任何数据。

接下来，我们捕获数据并创建快照。然后，我们将`html`和`body`标签设置回它们的初始值。

下面是这个方法的更新函数:

```
const exportAsImage = async (element, imageFileName) => {
const html = document.getElementsByTagName("html")[0];
const body = document.getElementsByTagName("body")[0];
let htmlWidth = html.clientWidth;
let bodyWidth = body.clientWidth;
const newWidth = element.scrollWidth - element.clientWidth;
if (newWidth > element.clientWidth) {
htmlWidth += newWidth;
bodyWidth += newWidth;
}
html.style.width = htmlWidth + "px";
body.style.width = bodyWidth + "px";
const canvas = await html2canvas(element);
const image = canvas.toDataURL("image/png", 1.0);
downloadImage(image, imageFileName);
html.style.width = null;
body.style.width = null;
};

```

在这个函数中，我们首先检索`html`和`body`标签的初始宽度。然后，我们注意到一个元素的内部宽度，以及在不添加水平滚动条的情况下包含视窗中所有数据所需的最小宽度。

我们定义如下:

*   `clientWidth`:以像素为单位的元素内部宽度，包括填充
*   `scrollWidth`:一个元素需要放入容器的最小宽度

想知道为什么我们需要这些价值观？嗯，通过保持大小的动态性，而不是静态性，我们可以容纳最大数量的用例。比如说。如果屏幕上只有两个视觉效果，我们就不需要把容器的宽度设置得很高。但是，如果屏幕上有几个视觉效果，我们可能需要一个高的容器宽度。

对于每种情况，我们比较`clientWidth`和`scrollWidth`以了解是否需要改变宽度。有两种可能的情况:

1.  `clientWidth`小于`scrollWidth`

在这种情况下，容器的某些部分是隐藏的。为了克服这个问题，我们将把`scrollWidth`和`clientWidth`之间的差异添加到`html`和`body`标签的宽度中。

2.  `clientWidth`大于或等于`scrollWidth`

在这个场景中，容器的所有部分都是可见的。在这种情况下，不需要调整`html`和`body`标签的宽度。

## 结论

html2canvas 是一个健壮的、易于使用的解决方案，用于将 React 组件导出为图像。它确实有一些限制，所以请务必[阅读文档](https://html2canvas.hertzen.com/documentation)并测试所有可能的场景，以确保您能够生成想要的视图。本教程中使用的演示可在 [CodeSandbox](https://codesandbox.io/s/export-react-component-as-image-tgqmq) 上获得。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)