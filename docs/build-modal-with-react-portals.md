# 使用 React 门户构建一个模态| LogRocket 博客

> 原文：<https://blog.logrocket.com/build-modal-with-react-portals/>

模态对于快速吸引用户的注意力非常有用。它们可用于收集用户信息、提供更新或鼓励用户采取行动。一项针对 20 亿个弹出窗口的研究显示，前 10%的玩家转化率超过 9%。

然而，我认为公平地说，情态动词的建立需要一些耐心。跟踪所有的 z 索引值、图层和 DOM 层次结构并不容易。这种困难也延伸到需要在顶层呈现的其他元素，比如覆盖图或工具提示。

在 React 应用程序中，组件或元素作为最近的父节点的子节点安装到 DOM 中。从上到下，标准层的层次结构如下:

```
root node => parent nodes => child nodes => leaf nodes
```

如果父节点的溢出属性设置为 hidden，或者在更高层有元素，则子节点不能出现在顶层，并且被限制在父节点的可见区域。我们可以尝试设置一个非常高的 z-index 值来将子元素带到顶层，但是这种策略可能会很繁琐，而且并不总是成功的。

这就是 React 门户的优势所在。React Portals 为元素提供了在默认层次结构之外呈现的能力，而不会损害组件之间的父子关系。

在本文中，我们将讨论:

本文中使用的方法也可以应用于构建:

*   工具提示
*   整页，顶层边栏
*   全球搜索工作服
*   隐藏溢出父容器内的下拉列表

所以，事不宜迟，让我们开始这个魔术吧。

## 创建 React 应用程序入门

让我们从使用 [Create React app](https://blog.logrocket.com/getting-started-with-create-react-app-d93147444a27/) 样板文件或您自己的 React App 设置创建一个新的 React App 开始。

```
# using yarn
yarn create react-app react-portal-overlay
# using npx
npx create-react-app react-portal-overlay

```

接下来，转到应用程序目录并启动 React 应用程序:

```
# cd into app directory
cd react-portal-overlay
# start using yarn
yarn start
# start using npm
npm run start

```

## 我们在构建什么组件？

我们将创建两个组件，并在 Create React App 样板中已经可用的`App`组件中呈现它们。

但是首先，这里有一些重要的组件定义:

*   `ReactPortal`:一个包装组件，它创建一个门户并在默认层次结构之外的容器中呈现内容
*   `Modal`:使用`ReactPortal`渲染带有 JSX 内容的基本模态组件
*   `App`(任何组件):我们将使用`Modal`组件并保持其活动状态(打开或关闭)的位置

## 创建 React 门户

使用从`react-dom`导入的 [`createPortal`函数](https://reactjs.org/docs/portals.html)可以创建一个 React 门户。它需要两个参数:

1.  `content`:任何有效的可呈现 React 元素
2.  `containerElement`:一个有效的 DOM 元素，我们可以将`content`附加到它上面

```
ReactDOM.createPortal(content, containerElement);

```

我们将在`src/components`目录下创建一个新组件`ReactPortal.js`，并添加以下代码片段:

```
// src/components/ReactPortal.js
import { createPortal } from 'react-dom';

function ReactPortal({ children, wrapperId }) {
  return createPortal(children, document.getElementById(wrapperId));
}
export default ReactPortal;

```

`ReactPortal`组件接受`wrapperId`属性，该属性是 DOM 元素的 ID 属性，充当门户的`container`。

需要注意的是，`createPortal()`函数不会为我们创建`containerElement`。该函数期望`container`已经在 DOM 中可用；我们必须自己添加它，以便门户在元素中呈现内容。

如果在 DOM 中找不到这样的元素，我们可以定制`ReactPortal`组件来创建具有所提供 ID 的元素。

首先，我们添加一个 helper 函数来创建一个具有给定 ID 的空的`div`，将它附加到主体，并返回元素。

```
function createWrapperAndAppendToBody(wrapperId) {
  const wrapperElement = document.createElement('div');
  wrapperElement.setAttribute("id", wrapperId);
  document.body.appendChild(wrapperElement);
  return wrapperElement;
}

```

接下来，让我们更新`ReactPortal`组件以使用`createWrapperAndAppendToBody`助手方法:

```
// Also, set a default value for wrapperId prop if none provided
function ReactPortal({ children, wrapperId = "react-portal-wrapper" }) {
  let element = document.getElementById(wrapperId);
  // if element is not found with wrapperId,
  // create and append to body
  if (!element) {
    element = createWrapperAndAppendToBody(wrapperId);
  }

  return createPortal(children, element);
}

```

这种方法有局限性。如果`wrapperId`属性发生变化，`ReactPortal`组件将无法处理最新的属性值。为了解决这个问题，我们需要将任何依赖于`wrapperId`的逻辑转移到另一个操作或副作用中。

### 为什么追加而不前置？还有，可以和 root 一起静态添加吗？

当没有为元素设置 z 索引时，DOM 层次结构的默认行为是，在层次结构中显示较低的元素将具有较高的优先级。简单来说，顺序很重要。因此，追加到 DOM 中的主体(在所有元素之后)将确保门户容器元素在层次结构中具有更高的优先级。

```
<body>
    <div id="root" />
<div id="portal-root" />
</body>

```

在上面的代码片段中，ID 为`portal-root`的 div 将具有更高的优先级，因为它出现得更晚。直接修改 HTML 或以编程方式添加门户根完全取决于您；出于本文的考虑，我们将以编程方式添加和移除门户容器元素。

## 处理动态`wrapperId`

React 挂钩`useLayoutEffect`和`useEffect`实现了相似的结果，但用法略有不同。一个快速的经验法则是，如果效果需要同步，并且在 DOM 上有任何直接突变，就使用`useLayoutEffect`。由于这种情况很少发生，`useEffect`通常是最好的选择。`useEffect`异步运行。

在这种情况下，我们直接改变 DOM，并希望在 DOM 被重画之前同步运行效果，所以使用`useLayoutEffect`钩子更有意义。

首先，让我们将`find`元素和创建逻辑移到`useLayoutEffect`钩子中，将`wrapperId`作为依赖项。接下来，我们将把`element`设置为状态。当`wrapperId`改变时，组件会相应地更新。

```
import { useState, useLayoutEffect } from 'react';
// ...

function ReactPortal({ children, wrapperId = "react-portal-wrapper" }) {
  const [wrapperElement, setWrapperElement] = useState(null);

  useLayoutEffect(() => {
    let element = document.getElementById(wrapperId);
    // if element is not found with wrapperId or wrapperId is not provided,
    // create and append to body
    if (!element) {
      element = createWrapperAndAppendToBody(wrapperId);
    }
    setWrapperElement(element);
  }, [wrapperId]);

  // wrapperElement state will be null on the very first render.
  if (wrapperElement === null) return null;

  return createPortal(children, wrapperElement);
}

```

现在，我们需要解决清理问题。

## 处理效果清理

我们直接改变 DOM，在找不到元素的情况下，在主体上附加一个空的`div`。因此，我们需要确保在卸载`ReactPortal`组件时，动态添加的空`div`从 DOM 中移除。此外，我们必须避免在清理过程中删除任何现有的元素。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们添加一个`systemCreated`标志，并在调用`createWrapperAndAppendToBody`时将其设置为`true`。如果`systemCreated`是`true`，我们将从 DOM 中删除该元素。更新后的`useLayoutEffect`看起来会像这样:

```
// ...
  useLayoutEffect(() => {
    let element = document.getElementById(wrapperId);
    let systemCreated = false;
    // if element is not found with wrapperId or wrapperId is not provided,
    // create and append to body
    if (!element) {
      systemCreated = true;
      element = createWrapperAndAppendToBody(wrapperId);
    }
    setWrapperElement(element);

    return () => {
      // delete the programatically created element
      if (systemCreated && element.parentNode) {
        element.parentNode.removeChild(element);
      }
    }
  }, [wrapperId]);
// ...

```

我们已经创建了门户，并将其定制为自动防故障。接下来，让我们创建一个简单的模态组件，并使用 React Portal 呈现它。

## 构建简单的模态组件

为了构建模态组件，我们将首先在`src/components`下创建一个新目录`Modal`，并添加两个新文件`Modal.js`和`modalStyles.css`。

模态组件接受几个属性:

*   `isOpen`:一个布尔标志，表示模态的状态(打开或关闭),并且在呈现模态的父组件中被控制
*   `handleClose`:通过点击关闭按钮或任何触发关闭的动作调用的方法

只有当`isOpen`为`true`时，模态组件才会呈现内容。模态组件将在`false`上`return null`，因为我们不想在 DOM 关闭时保留模态。

```
// src/components/Modal/Modal.js
import "./modalStyles.css";

function Modal({ children, isOpen, handleClose }) {
  if (!isOpen) return null;

  return (
    <div className="modal">
      <button onClick={handleClose} className="close-btn">
        Close
      </button>
      <div className="modal-content">{children}</div>
    </div>
  );
}
export default Modal;

```

## 设计演示模型

现在，让我们给模型添加一些样式:

```
/* src/components/Modal/modalStyles.css */
.modal {
  position: fixed;
  inset: 0; /* inset sets all 4 values (top right bottom left) much like how we set padding, margin etc., */
  background-color: rgba(0, 0, 0, 0.6);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease-in-out;
  overflow: hidden;
  z-index: 999;
  padding: 40px 20px 20px;
}

.modal-content {
  width: 70%;
  height: 70%;
  background-color: #282c34;
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2rem;
}

```

这段代码将使模态占据整个视窗，并将`.modal-content`垂直和水平居中对齐。

## 用 Escape 键关闭模态

点击`Close`按钮，触发`handleClose`，可以关闭模态。让我们添加通过按 escape 键关闭模态的功能。为了实现这一点，我们将附加`useEffect` keydown 事件监听器。我们将删除效果清理中的事件监听器。

在 keydown 事件中，如果按下了`Escape`键，我们将调用`handleClose`:

```
// src/components/Modal/Modal.js
import { useEffect } from "react";
import "./modalStyles.css";

function Modal({ children, isOpen, handleClose }) {
  useEffect(() => {
    const closeOnEscapeKey = e => e.key === "Escape" ? handleClose() : null;
    document.body.addEventListener("keydown", closeOnEscapeKey);
    return () => {
      document.body.removeEventListener("keydown", closeOnEscapeKey);
    };
  }, [handleClose]);

  if (!isOpen) return null;

  return (
    <div className="modal">
      <button onClick={handleClose} className="close-btn">
        Close
      </button>
      <div className="modal-content">{children}</div>
    </div>
  );
};

export default Modal;

```

我们的模态组件现在可以开始运行了！

## 逃离默认的 DOM 层次结构

让我们在一个应用程序中呈现演示`Modal`组件。

为了控制模态的打开和关闭行为，我们将用`useState`钩子初始化状态`isOpen`，并将其默认设置为`false`。接下来，我们将添加一个按钮点击，`button onClick`，它将`isOpen`状态设置为`true`，并打开模态。

现在，我们将把`isOpen`和`handleClose`作为属性发送给`Modal`组件。`handleClose`属性只是一个回调方法，它将`isOpen`状态设置为`false`，以便关闭模态。

```
// src/App.js
import { useState } from "react";
import logo from "./logo.svg";
import Modal from "./components/Modal/Modal";
import "./App.css";

function App() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <button onClick={() => setIsOpen(true)}>
          Click to Open Modal
        </button>

        <Modal handleClose={() => setIsOpen(false)} isOpen={isOpen}>
          This is Modal Content!
        </Modal>
      </header>
    </div>
  );
}

export default App;

```

点击**点击打开模式**按钮可以打开模式。可以通过按退出键或点击**关闭**按钮来关闭模态。任何一个动作都会触发`handleClose`方法并关闭模态。

如果我们看一下 DOM 树，我们会看到根据默认的 DOM 层次结构，`modal`被呈现为`header`的子节点。

![Modal built without ReactPortal](img/ae74ec2ffc475da16b7df5831c7f6c12.png)

Modal built without `ReactPortal`

让我们用`ReactPortal`包装模态的返回 JSX，以便在 DOM 层次结构之外和提供的容器元素之内呈现模态。动态容器作为 DOM 中主体的最后一个子容器被追加。

组件`Modal`更新后的返回方法应该如下所示:

```
// src/components/Modal/Modal.js
import ReactPortal from "../ReactPortal";
// ...

function Modal({ children, isOpen, handleClose }) {
  // ...

  return (
    <ReactPortal wrapperId="react-portal-modal-container">
      <div className="modal">
        // ...
      </div>
    </ReactPortal>
  );
}
// ...

```

因为我们还没有添加一个 ID 为`react-portal-modal-container`的容器，所以将用这个 ID 创建一个空的`div`,然后将它追加到主体中。`Modal`组件将呈现在这个新创建的容器中，在默认的 DOM 层次结构之外。只有产生的 HTML 和 DOM 树被改变。

头部和`Modal`组件之间的 React 组件的父子关系保持不变。

![Modal built with ReactPortal](img/80584faf8e606b03eb2c37f9373ba9e6.png)

Modal built with `ReactPortal`

如下所示，我们的演示模态渲染正确，但其用户界面的打开和关闭感觉太即时了:

![Modal built without CSS Transition](img/55868167ef47fa5cb68527f64b0cf289.png)

Modal built without CSS Transition

## 使用 CSS 过渡应用过渡

为了调整模态打开和关闭的过渡，我们可以在`Modal`组件关闭时移除`return null`。我们可以通过 CSS 控制模态的可见性，使用`opacity`和`transform`属性以及一个有条件添加的类`show/hide`。

这个`show/hide`类可用于设置或重置可见性，并使用 transition 属性来制作打开和关闭的动画。这很好地工作，除了即使在关闭后模态仍然保留在 DOM 中。

我们也可以将`display`属性设置为`none`，但是这与`return null`的结果相同。这两个属性都可以立即从 DOM 中删除元素，而无需等待过渡或动画完成。这就是 [`CSSTransition`组件](https://reactcommunity.org/react-transition-group/css-transition)来拯救的地方。

通过将待转换的[元素包装在`CSSTransition`组件](https://blog.logrocket.com/react-transition-group-examples-tutorial/)中，并将`unmountOnExit`属性设置为`true`，转换将运行，然后一旦转换完成，该元素将从 DOM 中移除。

首先，我们安装`react-transition-group`依赖项:

```
# using yarn
yarn add react-transition-group
# using npm
npm install react-transition-group

```

接下来，我们导入`CSSTransition`组件，并使用它将`ReactPortal`下的所有内容包装在模态的返回 JSX 中。

组件的触发、持续时间和样式都可以通过设置`CSSTransition`属性来控制:

*   `in`:触发进入或退出状态的布尔标志
*   `timeout`:每个状态(进入、退出等)的转换持续时间。)
*   `unmountOnExit`:退出后卸载组件
*   `classNames`:类名将作为每个状态(进入、退出等)的后缀。)来控制 CSS 定制
*   `nodeRef`:对需要转换的 DOM 元素的 React 引用(在本例中，是`Modal`组件的根`div`元素)

可以使用`useRef`钩子创建一个`ref`。这个值被传递给`CSSTransition`的`nodeRef`属性。它作为一个`ref`属性附加到`Modal`的根`div`上，将`CSSTransition`组件与需要转换的元素连接起来。

```
// src/components/Modal/Modal.js
import { useEffect, useRef } from "react";
import { CSSTransition } from "react-transition-group";
// ...

function Modal({ children, isOpen, handleClose }) {
  const nodeRef = useRef(null);
  // ...

  // if (!isOpen) return null; <-- Make sure to remove this line.

  return (
    <ReactPortal wrapperId="react-portal-modal-container">
      <CSSTransition
        in={isOpen}
        timeout={{ entry: 0, exit: 300 }}
        unmountOnExit
        classNames="modal"
        nodeRef={nodeRef}
      >
        <div className="modal" ref={nodeRef}>
          // ...
        </div>
      </CSSTransition>
    <ReactPortal wrapperId="react-portal-modal-container">
  );
}
// ....

```

接下来，让我们为由`CSSTransition`组件添加的状态前缀类`modal-enter-done`和`modal-exit`添加一些转换样式:

```
.modal {
  ...
  opacity: 0;
  pointer-events: none;
  transform: scale(0.4);
}

.modal-enter-done {
  opacity: 1;
  pointer-events: auto;
  transform: scale(1);
}
.modal-exit {
  opacity: 0;
  transform: scale(0.4);
}

...

```

演示模型的 UI 的打开和关闭现在看起来更流畅了，这是在不影响 DOM 负载的情况下实现的:

![Modal built with CSS Transition](img/f8366e83a61ea4db530b3f894350dfaa.png)

Modal built with CSS Transition

## React 门户的其他用例

门户最适合那些需要出现在所有其他元素之上的元素。例如，简档悬浮卡是一种提供关于用户简档的快速信息而无需点击和访问简档本身的流行方式，即，更快速地传递信息。

同样，整页加载屏幕是使用门户的好方法。当一个更长的任务正在进行中时，最好在整个屏幕上阻止用户操作，以避免意外取消。(有趣的事实:你也可以列出提示和技巧，或者推广应用程序本身鲜为人知的功能，以保持用户在加载期间的参与度！)

您可能还想为下拉菜单和工具提示之类的东西使用门户，由于父容器的视窗有限，这些东西通常会被删除。

## 使用 React 门户时需要注意的事项

关于使用 React 门户，您应该记住三件重要的事情:

1.  默认的 DOM 层次结构:始终将门户容器作为最后一个元素添加，以避免设置 z 索引，因为当没有设置 z 索引时，元素的顺序定义了图层层次结构
2.  React 组件行为:只改变 HTML 树结构；反应组件行为和父子关系不受影响
3.  多个门户容器:一次使用多个门户根元素可能需要正确的排序；也就是说，下拉菜单呈现在一个模型中，它和模型都是使用门户构建的。在这个用例中，dropdown 需要在 DOM 中出现在模态容器之后，这样我们就不必再处理 z-index，可以根据需要对容器进行排序

## 结论

在本文中，我们用一个 React 门户模式示例演示了 React 门户的功能。然而，React 门户的应用不仅限于模态或覆盖。我们还可以利用 React 门户在包装级别的所有组件之上呈现组件。

通过用`ReactPortal`包装组件的 JSX 或组件本身，我们可以改变默认的 DOM 层次结构行为，并在任何组件上获得 React 门户的好处:

```
import ReactPortal from "./path/to/ReactPortal";

function AnyComponent() {
  return (
    <ReactPortal wrapperId="dedicated-container-id-if-any">
      {/* components JSX to render */}
    </ReactPortal>
  );
}

```

暂时就这样吧！你可以在这个 [GitHub repo](https://github.com/KRRISH96/react-portal-overlay) 中找到这篇文章的最终组件和样式，并访问[最终`ReactPortal`和动作中的模态组件](https://react-portal-overlay.netlify.app/)。

感谢您的阅读。我希望这篇文章对你有所帮助。请与其他可能发现它有益的人分享。再见。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)