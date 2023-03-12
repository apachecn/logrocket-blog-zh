# React iframes 的最佳实践

> 原文：<https://blog.logrocket.com/best-practices-react-iframes/>

在构建网页时，开发人员通常需要包含来自其他网页的资源。浏览网页时你可能会发现一些常见的例子，包括 Twitter 上的分享按钮、脸书的喜欢按钮和谷歌地图上的地图显示。

检索这类数据的一种流行方法是使用 iframe。iframe 是 inline frame 的缩写，本质上是框架中的框架。使用`<iframe/>`标签，您可以轻松地将其他来源的外部内容直接嵌入您的网页。开发人员还使用 iframe 将某些资源从同一网页中隔离出来，例如，通过渲染将组件封装在 iframe 中。

在本教程中，我们将通过查看这两个不同的用例来探索 React 中的 iframes。首先，让我们了解一些关于 iframes 如何工作以及我们应该如何使用它们的背景信息。我们开始吧！

## 在 React 中使用 iframes 的最佳实践

当资源在 iframe 中呈现时，它独立于嵌入它的父组件运行。因此，无论是父组件的 CSS 样式还是它的 JavaScript 都不会对 iframe 产生任何影响。

在 React 中，开发人员使用 iframes 创建沙盒组件或与其父组件隔离的应用程序。在 iframe 中，当一段内容从外部来源嵌入时，它完全由来源而不是嵌入它的网站控制。

因此，只嵌入来自可信来源的内容非常重要。还要记住，iframes 会耗尽额外的内存。如果内容太重，会拖慢你的网页加载时间，所以要谨慎使用 iframes。

## 嵌入来自外部来源的页面

首先，我们将学习如何嵌入来自外部资源的页面，这可能是 iframes 更常见的用例。如今，你很少看到一个 web 应用程序没有从外部来源加载任何内容。

例如，想想你在网页上找到了多少 YouTube 视频，在应用程序外看到了多少 Instagram 帖子，在博客上看到了多少脸书评论区，甚至在网页上看到了多少广告。这些元素都被嵌入到网站中，其复杂程度从单行代码到整个代码段不等。

我们可以使用下面一行代码向 React 应用程序添加一个 Twitter Tweet 按钮:

```
<iframe src="https://platform.twitter.com/widgets/tweet_button.html"></iframe>

```

我们将在下面的代码中使用上面的代码片段来生成一个 Tweet 按钮，如下图所示。当用户点击 Tweet 按钮时，所选内容将在其 Twitter 主页上的新 Tweet 中打开:

```
function App() {
  return (
    <div className="App">
      <h3>Iframes in React</h3>
      <iframe src="https://platform.twitter.com/widgets/tweet_button.html"></iframe>
    </div>
  );
}

export default App;

```

![React Iframe External Source Embed](img/ebab5c2cd458adcc0b4d473f18f07c9d.png)

## iframe 标记属性

现在，让我们回顾一下 iframe 标签的一些有用的属性，这些属性将允许您修改和定制 iframe。例如，`src`用来设置你想要嵌入的网页的地址。例如，我们可以使用`src`标签来嵌入一个 YouTube 视频，如下所示:

```
<iframe src="https://www.youtube.com/embed/uXWycyeTeCs" ></iframe>

```

![Iframe Tag Attribute](img/f20dd607f26ee3c95470b180b191c7fc.png)

`srcdoc`用于设置要嵌入的内嵌 HTML。注意，如果两者都存在的话，`srcdoc`属性将覆盖`src`属性。在下面的代码片段中，我们将使用`srcdoc`命令覆盖 Youtube 视频的`src`命令，该命令使用 hello 消息作为占位符:

```
<iframe src="https://www.youtube.com/embed/uXWycyeTeCs" srcDoc='<p>Hello from Iframe</p>' ></iframe>

```

我们将使用`height`和`width`属性来设置 iframe 的高度和宽度。默认单位是像素，但是您也可以使用其他单位。在下面的代码片段中，我们将设置显示 YouTube 视频的 iframe 的尺寸，如下面的屏幕截图所示:

```
<iframe src="https://www.youtube.com/embed/uXWycyeTeCs" width={1000} height={500} ></iframe>
```

![Iframe Height Width Attribute](img/014614794ad2e6b5a5de9fd2e0f2a1e2.png)

`allow`属性根据请求的来源设置`<iframe>`可用的特性，例如，访问自动播放、麦克风等等。在下面的截图中，我们为我们的 YouTube 视频设置了`allow`，值如下:`accelerometer`、`autoplay`、`clipboard-write`、`encrypted-media`、`gyroscope`和`picture-in-picture full`。

```
<iframe src="https://www.youtube.com/embed/uXWycyeTeCs" width={1000} height={500} allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture full"></iframe>

```

![Iframe Hooks Allow Attribute](img/ec7b9328aa4d504e8f81c577511aa292.png)

`title`属性用于设置 iframe 中内容的描述。虽然`title`属性对 iframe 的 UI 没有影响，但它有助于提高可访问性，为屏幕阅读器提供有价值的输入。在下面的代码片段中，我们向我们的 YouTube 视频添加了一个`title`:

```
<iframe src="https://www.youtube.com/embed/uXWycyeTeCs" width={1000} height={500} title='A youtube video on React hooks'></iframe>

```

接下来，我们使用`name`属性来设置 iframe 的名称，并在 JavaScript 中引用该元素。

为了对 iframe 的内容设置限制，我们使用了`Sandbox`。正如本教程前面提到的，我们不能控制从外部来源发送的内容，但是，我们可以使用`Sandbox`限制我们在 iframe 中接受的内容。若要应用所有限制，请将属性的值留空。或者，您可以添加标志来放宽限制。

例如，`allow-modals`将允许外部页面打开模态，而`allow-scripts`允许资源运行脚本:

```
<iframe src="https://www.youtube.com/embed/uXWycyeTeCs" width={1000} height={500} sandbox='allow-scripts allow-modal' ></iframe>

```

正确使用`Sandbox`属性及其标志可以极大地提高应用程序的安全性，尤其是当您嵌入的资源来自恶意来源时。

为了设置浏览器应该如何加载 iframe 内容，我们将使用`loading`属性；`loading`取`eager`或`lazy`。当设置为`eager`时，iframe 会立即加载，即使它在可视视口之外，这是默认值。`lazy`延迟加载，直到达到浏览器定义的距视窗的计算距离:

```
<iframe src="https://www.youtube.com/embed/uXWycyeTeCs" width={1000} height={500} sandbox='allow-scripts allow-modal' loading='eager'></iframe>

```

## 在 iframe 中呈现 React 应用程序或组件

当您不需要控制 iframe 中呈现的内容时，我们刚刚介绍的方法是一个很好的策略。如果您想向页面访问者显示 YouTube 上的视频或 Instagram 上的帖子，只需在`src`属性中添加 URL。

但是，如果你正在构建一个复杂的应用程序，例如，一个允许用户在同一个平台上构建独立应用程序的 CodeSandbox，或者一个当用户点击一个按钮时被触发的聊天机器人，该怎么办呢？在这两个例子中，您控制着内容，但是您也想将它们与应用程序的其他部分隔离开来。

在这一节中，我们将探索在 iframe 中呈现 React 应用程序或组件。当您想要减少 CSS 过剩或在另一个应用程序中使用成熟的应用程序而没有任何干扰时，这是一个好策略，尤其是当您想要 iframe 的内容与其父级共享状态时。

让我们尝试将我们的 iframe 呈现为 iframe 的直接子元素:

```
function App() {

  return (
    <div className='App'>
        <p>Iframes in React</p>
        <iframe >
           <MyComponent />
        </iframe>
    </div>
  );
}

export default App;

```

然而，当我们运行上面的代码时，我们什么也没有得到。

![Iframe Empty Src Srcdoc](img/18d732e60bc88b372f12b0df7a3ce45c.png)

您只能使用`src`属性来设置我们想要呈现的 URL。因为我们试图在同一个应用程序中呈现一个组件，所以`src`属性不起作用。

或者，我们可以使用`srcdoc`属性，它接受一个内嵌的 HTML 来嵌入。然而，我们试图使用大量冗长的代码来呈现整个应用程序或组件。我们需要找到一种方法在 iframe 主体中呈现组件，而不是将其作为子组件。为此，我们将使用一个门户。

## React 门户

根据 React 文档，门户允许我们将子组件呈现到位于父组件 DOM 层次结构之外的 DOM 节点中。基本上，门户让我们在任何我们想去的地方呈现孩子。

您可以使用以下命令创建门户:

```
ReactDOM.createPortal(child, container)

```

在这种情况下，子元素是 React 元素、片段或字符串，容器是门户应该呈现到的 DOM 位置或节点。

有了 React 门户，我们可以选择在 DOM 层次结构中放置 DOM 节点的位置。为此，我们将首先建立一个对现有的已挂载 DOM 节点的引用。在这种情况下，它将在给定的`<iframe>`的`contentWindow`中。然后，我们将使用它创建一个门户。门户的内容也被视为父虚拟 DOM 的子内容。

假设我们有以下名为`MyComponent.js`的文件:

```
import React from 'react'

function MyComponent() {
    return (
        <div>
            <p style={{color: 'red'}}>Testing to see if my component renders!</p>
        </div>
    )
}

export default MyComponent;

```

现在，让我们创建一个名为`CustomIframe.js`的文件，并编写以下代码:

```
import React, { useState } from 'react'
import { createPortal } from 'react-dom'

const CustomIframe = ({
  children,
  ...props
}) => {
  const [contentRef, setContentRef] = useState(null)

  const mountNode =
    contentRef?.contentWindow?.document?.body

  return (
    <iframe {...props} ref={setContentRef}>
      {mountNode && createPortal(children, mountNode)}
    </iframe>
  )
}

export default CustomIframe;

```

我们用`useState()`钩子创建了一个`ref`，因此，一旦状态被更新，组件将重新呈现。我们还访问了 iframe 文档体，然后创建了一个门户来呈现传递给`iframe`的子文档体:

```
import './App.css';
import CustomIframe from './CustomIframe';
import MyComponent from './MyComponent';
function App() {

  return (
    <CustomIframe title='A custom made iframe'>
        <MyComponent />
      </CustomIframe>
  );
}

export default App;

```

你可以把任何一个 React app 或者组件作为`CustomIframe`的子体传递，就可以了！
React 应用或组件将被封装，这意味着您可以独立开发和维护它。

您还可以使用一个名为`react frame component`的库来实现与上面相同的封装。要安装它，请运行以下命令:

```
npm install --save react-frame-component
```

如下封装您的组件:

```
import Frame from 'react-frame-component';

function App() {

  return (
    <div className='App'>
        <p>Iframes in React</p>
        <Frame >
           <MyComponent />
        </Frame>
    </div>
  );
}

export default App;

```

## 结论

在本教程中，我们在两个不同的用例中探索了 React 中的 iframes。首先，我们学习了如何使用 iframe 将网页中的外部内容嵌入到 web 应用程序中，将父元素和子元素的 JavaScript 和 CSS 分开。其次，我们学习了如何在 iframe 中隔离应用程序的某些部分。

iframes 是开发人员必须学习的有用元素。我希望你喜欢这个教程！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)