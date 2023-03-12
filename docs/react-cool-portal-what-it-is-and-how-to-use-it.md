# React-cool-portal:它是什么以及如何使用它

> 原文：<https://blog.logrocket.com/react-cool-portal-what-it-is-and-how-to-use-it/>

如果您熟悉 React，您很可能已经意识到 React 在单个`div`标签下呈现所有 HTML 元素，通常给出一个 ID`root`。

```
ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```

如果您试图在根元素之外呈现另一个元素，比如模态元素或工具提示元素，这将是一个恼人的限制。为了解决这个问题，引入了 React 门户。

## React 门户简介

在 React 16.0 版本中，引入了门户来解决无法呈现根节点之外的其他元素的问题。

以下是 React 文档中关于如何利用 React 门户的一个示例:

```
const appRoot = document.getElementById('app-root');
const modalRoot = document.getElementById('modal-root');

class Modal extends React.Component {
  constructor(props) {
    super(props);
    this.el = document.createElement('div');
  }
  componentDidMount() {
    modalRoot.appendChild(this.el);
  }
  componentWillUnmount() {
    modalRoot.removeChild(this.el);
  }

  render() {
    return ReactDOM.createPortal(
      this.props.children,
      this.el
    );
  }
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {clicks: 0};
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    // This will fire when the button in Child is clicked,
    // updating Parent's state, even though button
    // is not direct descendant in the DOM.
    this.setState(state => ({
      clicks: state.clicks + 1
    }));
  }

  render() {
    return (
      <div onClick={this.handleClick}>
        <p>Number of clicks: {this.state.clicks}</p>
        <p>
          Open up the browser DevTools
          to observe that the button
          is not a child of the div
          with the onClick handler.
        </p>
        <Modal>
          <Child />
        </Modal>
      </div>
    );
  }
}

function Child() {
  // The click event on this button will bubble up to parent,
  // because there is no 'onClick' attribute defined
  return (
    <div className="modal">
      <button>Click</button>
    </div>
  );
}

ReactDOM.render(<Parent />, appRoot);
```

正如您所看到的，代码有点冗长，如果缩短它的长度，会更容易阅读和理解。快进到 React 版本 16.8 中钩子的引入。

钩子允许开发人员在不改变组件层次结构的情况下重用有状态逻辑。它们改变了 React 开发人员编写代码和编写状态的方式。事实上，许多 React 库，比如 react-redux 和 react-router，正在或者已经转向一个更加基于钩子的 API。

许多新的基于钩子的库也正在发布，为某些服务或代码提供一些抽象。一个这样的库是 react-cool-portal。在本文中，我们将探索 react-cool-portal，最后，我们将构建一个动画模型。我们开始吧！

## 什么是 react-cool-portal？

正如我前面提到的，react-cool-portal 是 react 门户上基于钩子的抽象。使用 react-cool-portal，创建门户非常简单:

```
const {Portal} = usePortal();
```

钩子还带有各种方法和事件监听器，允许您执行更灵活的操作。以下是 react-cool-portal 的一些特性:

*   您可以将元素/组件呈现给`<body>`中的默认元素或指定的 DOM 元素
*   react-cool-portal 带有各种状态控制器和事件监听器，允许您更灵活地处理门户
*   它可以作为一个包装器来构建您的定制钩子
*   它会自动删除未使用的门户容器，从而防止 DOM 混乱
*   它支持 TypeScript 类型定义
*   它具有服务器端呈现兼容性
*   它的尺寸很小( [~ 1.4KB gzipped](https://bundlephobia.com/result?p=react-cool-portal) )，除了`react`和`react-dom`之外，没有任何外部依赖

在本文中，我们将探索 react-cool-portal，最后，我们将构建一个动画模型。我们开始吧！

### 探索反应-冷却-门户

正如我前面提到的，您可以用`usePortal`钩子创建一个门户。`usePortal`返回一个包含`Portal`组件和一些函数的对象:

```
const {Portal, show, hide, isShow, toggle} = usePortal();
```

`show`函数用于显示门户(挂载它),而`hide`用于隐藏门户(卸载它)。`isShow`是一个布尔值，当门户被安装时返回`true`，否则返回`false`。`toggle`是根据门户的当前状态显示或隐藏门户的功能。

我们可以向我们的`usePortal`钩子提供一个对象形式的参数，用于进一步的配置，如下所示:

```
const {Portal} = usePortal({
  defaultShow: false,
  internalShowHide: true,
  onShow : e => {
  },
  onHide: e => {
  },
  containerId: "portal",
  clickOutsideToHide: true,
  escToHide: true
});
```

使用`defaultShow`，我们可以选择默认显示或隐藏门户内容。`internalShowHide`启用/禁用我们门户的显示/隐藏功能，以便我们可以随心所欲地处理门户。

`onShow`和`onHide`分别是当`isShow`设置为`true`或`isShow`设置为`false`时触发的事件处理程序。`containerId`，默认值为`react-cool-portal`，用于设置门户的 ID。

`clickOutsideToHide`定义我们是否想要通过点击门户外部来隐藏门户，而`escToHide`定义我们是否想要通过点击`esc`键来隐藏门户。

这基本上就是开始使用 react-cool-portal 所需要知道的全部内容！现在，我们将利用目前所学的知识建立一个动画模型。

## 构建动画模型

与任何 React 项目一样，您必须在开始工作之前创建一个新项目。为此，您可以轻松使用 create-react-app:

```
npx create-react-app animated-modal
```

一旦你创建了一个新项目，你需要安装 react-cool-portal，就像这样:

```
npm install --save react-cool-portal
```

由于这是一个非常小的项目，我将在一个文件`App.js`中编写我所有的 JavaScript/JSX，在另一个文件`App.css`中编写我所有的 CSS。

在我们继续之前，让我们想象一下我们想要创建什么。我们希望有一个按钮，当点击时，将显示一个动画模式。然后我们可以从模态本身退出模态。

也就是说，我们需要创建一个状态，根据按钮是否被点击来呈现`Portal`组件:

```
const [showModal, setShowModal] = useState(false);
```

我们还需要另一个状态来存储动画状态(将触发动画的不同 CSS 类):

```
const [animationState, setAnimationState] = useState("");
```

我们目前应该有这个:

```
const { Portal, show, hide } = usePortal({
    defaultShow: false,
  });
  const [showModal, setShowModal] = useState(false);
  const [animationState, setAnimationState] = useState("");
```

现在让我们创建一个简单的按钮元素，当它被点击时会将`showModal`设置为`true`，并调用`show`函数。

```
return (
    <div>
      <button
        onClick={() => {
          setShowModal(true);
          show();
        }}
      >
        Open Modal
      </button>    
    </div>
  );
```

现在让我们编写模态标记:

```
return (
    <div>
      <button
        onClick={() => {
          setShowModal(true);
          show();         
        }}
      >
        Open Modal
      </button>
      {showModal && (
        <Portal>
          <div className= "modal" tabIndex={-1}>
            <div
              className={`modal-dialog ${animationState}`}
              role="dialog"
              aria-labelledby="modal-label"
              aria-modal="true"
            >
              <div className="modal-header">
                <h5 id="modal-label">Modal header</h5>
                <span
                  className="modal-exit"
                >
                  close
                </span>
              </div>
              <div className="modal-body">
                <p>Modal Body</p>
              </div>
            </div>
          </div>
        </Portal>
      )}
    </div>
  );
```

在我们的 CSS 中，我们将有两个不同的动画:`slideIn`和`slideOut`。当点击按钮打开模态时，动画状态变为`slideIn`，并且`slideIn`类被附加到模态对话框`div`。

为此，我们将使用`onShow`事件处理程序将当前动画状态设置为`slideIn`。所以每当模态被显示时，`slideIn`类将被附加到它上面。

```
const { Portal, show, hide } = usePortal({
    defaultShow: false,
    onShow: () => {
      setAnimationState("slideIn");
    },
  });
```

我们的 modal 有一个`<span>`元素，它将用于在单击时关闭 modal。当这个`<span>`元素被点击时，我们将动画状态设置为`slideOut`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
<span
  className="modal-exit"
  onClick={() => {
    setAnimationState("slideOut");
  }}
>
 close
</span>
```

我们现在将利用 React 提供的动画事件之一:`onAnimationEnd`。传递给它的事件处理程序将在动画结束后运行。

在我们的例子中，一旦模态对话框上的动画结束，我们将检查当前的动画状态。如果是`slideOut`，我们就隐藏模态。一旦完成，我们将设置动画状态为空字符串。

```
 <div
  className={`modal-dialog ${animationState}`}
   role="dialog"
   aria-labelledby="modal-label"
   aria-modal="true"
    onAnimationEnd={() => {
       if(animationState == "slideOut"){
          hide();
        }
        setAnimationState("");
    }}
>
```

我们的`App`组件现在应该是这样的:

```
import React, { useState } from "react";
import "./App.css";
import usePortal from "react-cool-portal";

function App() {
  const { Portal, show, hide } = usePortal({
    defaultShow: false,
    onShow: () => {
      setAnimationState("slideIn");
    },
  });
  const [showModal, setShowModal] = useState(false);
  const [animationState, setAnimationState] = useState("");
  return (
    <div>
      <button
        onClick={() => {
          setShowModal(true);
          show(); 
        }}
      >
        Open Modal
      </button>
      {showModal && (
        <Portal>
          <div className= "modal" tabIndex={-1}>
            <div
              className={`modal-dialog ${animationState}`}
              role="dialog"
              aria-labelledby="modal-label"
              aria-modal="true"
              onAnimationEnd={() => {
                if(animationState == "slideOut"){
                  hide();
                }
                setAnimationState("");
              }}
            >
              <div className="modal-header">
                <h5 id="modal-label">Modal header</h5>
                <span
                  className="modal-exit"
                  onClick={() => {
                    setAnimationState("slideOut");
                  }}
                >
                  close
                </span>
              </div>
              <div className="modal-body">
                <p>Modal Body</p>
              </div>
            </div>
          </div>
        </Portal>
      )}
    </div>
  );
}
```

这就是 JavaScript 的全部内容——让我们继续讨论 CSS。这是模态的 CSS:

```
body{
  --curve: cubic-bezier(0.22, 1, 0.36, 1);
}
#react-cool-portal{
  position: absolute;
  top:0;
  left: 0;
  min-width: 100vw;
  height: 100%;
}
.modal{
  height: 100%;
  width: 100%;
  display: flex;
  z-index: 20;
  justify-content: center;
  align-items: center;
  background-color: rgba(0,0,0,0.7);
}
.modal-dialog{
  background-color: white;
  border-radius: 10px;
  width: 80%;
  max-width: 400px;
  padding: 1rem;

}
.modal-header{
  font-weight: 400;
  font-size: 1.5rem;
  display: flex;
  justify-content: space-between;
}
.modal-header #modal-label{
  margin:0;
}
.modal-exit{
  font-size: 1rem;
  color: red;
  cursor: pointer;
}
.slideIn{
  animation: slideIn 0.5s var(--curve) 0s 1 normal none;
}
.slideOut{
  animation: slideOut 0.5s var(--curve) 0s 1 normal forwards;
}
@keyframes slideIn {
  0% {
    transform: translateY(-2rem);
    opacity: 0;
  }

  100% {
    transform: translateY(0);
    opacity: 1;
  }
}
@keyframes slideOut {
  100% {
    transform: translateY(-2rem);
    opacity: 0;
  }
  0% {
    transform: translateY(0);
    opacity: 1;
  }
}
```

你现在应该有一个很好的动画模型了！

## 结论

就这样，我们结束了！您可以基于 react-cool-portal 创建一个名为`useModal`的定制钩子，以实现代码重用。

这个项目回购的链接可以在[这里](https://github.com/NodeJSs/react-cool-portal-article)找到。你可以在这里找到现场演示。最后，你可以在这里了解更多关于 react-cool-portal [的信息。](https://github.com/wellyshen/react-cool-portal)

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。