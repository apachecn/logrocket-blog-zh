# Top React toast 库比较

> 原文：<https://blog.logrocket.com/top-react-toast-libraries-compared/>

## 介绍

您可能会发现，有时您希望根据用户的交互状态或其他变量向用户发送通知。这就是 toast 通知派上用场的地方。

虽然可以为 React 创建一个定制的 toast 组件，但是您会发现一个免费的库可以满足您的开发需求。请继续阅读，比较四个顶级的 React toast 库。

## 反应热烤面包片

反应热吐司的创造者将其描述为“镇上最好的吐司”我特别喜欢这个图书馆的一点是它的设计:它有我见过的设计最好的烤面包之一。

### 使用

通过`toast` API，我们可以访问`success`方法。`Toaster`组件负责呈现所有的祝酒词。这就是用 react-hot-toast 制作基本吐司的全部步骤:

```
import toast, { Toaster } from "react-hot-toast";
const successToast = () => toast.success("This is a success toast.");

export default function App() {
  return (
    <div>
      <button onClick={successToast}>Success toast</button>
      <Toaster />
    </div>
  );
}

```

### 烤面包变体

react-hot-toast 提供了四种吐司变体:`success`、`error`、`loading`和`promise`。

```
toast.success();
toast.error();
toast.loading();
toast.promise();

```

当您想要在某个事件之后更新 toast 时，比如说，一旦从 API 获取数据已经完成，toast 就很有用。

```
import toast, { Toaster } from "react-hot-toast";

const simulateFetch = () => {
  toast.promise(fetchData(), {
    loading: "Fetching data",
    success: "Data fetched successfully",
    error: "Error when fetching",
  });
};

export default function App() {  
  return (
    <div>
      <button onClick={simulateFetch}>Fetch Data</button>
      <Toaster />
    </div>
  );
} 

```

有些情况下，您可能希望创建自定义的 toast 来满足您的项目需求。react-hot-toast 当然是可定制的，所以你可以这样做。这里，我们将一个函数传递给`toast`,它返回自定义的 toast:

```
const customToast = () =>
  toast((t) => (
    <span>
      I am a custom toast
      <button onClick={() => toast.dismiss(t.id)}>Dismiss</button>
    </span>
  ));

export default function App() {
  return (
    <div>
      <button onClick={customToast}>show custom toast</button>
      <Toaster />
    </div>
  );
}

```

### 配置

`toast` API 的第二个参数是一个`options`对象，用它可以配置每个 toast 的行为。我们可以指示 toast 的持续时间、更改其样式、设置自定义图标、定义其可访问性属性以及更改其图标的主题。

```
import { AiFillAndroid } from "react-icons/ai";

toast.error("error toast", {
  duration: 7000,

  //icon config
  iconTheme: {
    primary: "white",
    secondary: "black",
  },

  //ARIA config
  role: "status",
  ariaLive: "polite",

  //toast styles
  style: {
    background: "red",
    color: "whitesmoke",
    icon: <AiFillAndroid />,
  },
});

```

### 特征

有几种方法可以用 React Hot 吐司来消除吐司。你可以拒绝一个祝酒词。如果您没有传入一个对`dismiss`方法的 toast 引用，它将立刻取消所有的 toast。

```
const successToast = toast.success('This is a successt toast');
toast.dismiss(successToast);

toast.dismiss() //this will dismiss all toasts at once 

```

### 开发者体验

除了精心设计的祝酒词，react-hot-toast 还有结构良好的文档。我也很喜欢给每一个吐司添加自定义图标是多么容易。这是一个值得一看的图书馆。

## Notyf

Notyf 是一个轻量级且易于使用的 JavaScript 库，用于创建 toast 通知。它与 React、Vue 和 Angular 兼容。

### 使用

我们创建了一个`Notyf`类的新实例，并用它来设置我们的 toast。这里，我们从实例中访问`success`方法。该方法将包含 toast 消息的字符串作为其唯一的参数。我们将`successToast`功能附加到一个按钮的`onClick`事件上，这就是使用 Notyf 设置一个基本 toast 的全部工作。

```
import "./styles.css";
import { Notyf } from "notyf";
import "notyf/notyf.min.css";

const notyf = new Notyf();
const successToast = () => notyf.success("This is a success toast");

export default function App() {
  return (
    <div className="App">
      <button onClick={successToast}>show success toast</button>
    </div>
  );
}

```

### 烤面包变体

与本文中的其他库不同，Notyf 自带了两种类型的祝酒词:成功祝酒词和错误祝酒词。如果该库提供额外的内置 toast 变体，那就太棒了。

```
notyf.success("success toast");
notyf.error("error toast");

```

### 配置

Notyf 实例可以把一个可选对象作为它的参数，在这个对象中我们定义了 toast 的行为。这里，我们定义了 toast 的持续时间，我们希望它出现的位置，以及我们是否希望用户能够取消 toast。

```
const notyf = new Notyf({
  duration: 1000,
  dismissiblea: true,
  position: {
    x: "right",
    y: "top",
  },
}

```

### 特征

您可以取消特定的祝酒词。当调用`dismiss`方法时，`successToast`将被解除，即使它的持续时间还没有过去。你也可以拒绝所有用`dismissAll`方式出现的祝酒词。

```
const successToast = () => notyf.success("This is a success toast");
notyf.dismiss(successToast);

notyf.dismissAll(); //dismiss all open toasts

```

虽然 Notyf 只提供了开箱即用的`success`和`error`祝酒词，但是您可以创建自己的定制祝酒词。我们在`types`数组中定义并样式化任何定制的祝酒词。

在下面的代码片段中，我们设置了一个定制的加载 toast，它在获取数据或提交表单时非常有用。为了使用定制的 toast，我们使用`open`方法，并向它传递一个包含定制 toast 的名称及其消息的对象。

```
//define custom loading toast
const notyf = new Notyf({
  types: [
    {
      type: "loading",
      background: "black",
      duration: 2000,
      dismissible: true,
    },
    //other custom toasts if any
  ],
});

//call loading toast
const showCustomToast = () => {
  notyf.open({
    type: "loading",
    message: "Fetching data...",
  });
};

```

### 开发者体验

如果这个库包含一个在异步事件完成时更新的加载 toast，开发者的体验会更好。除此之外，Notyf 是一个非常简单的 toast 库。虽然 Notyf 不像本文中的其他 React toast 库那样包含很多特性，但它仍然涵盖了大多数常见的用例。

## 对 Toast 通知做出反应

React Toast 通知是 React 的一个可配置、可组合的 Toast 通知系统。

### 使用

要使用 React Toast 通知，将您的应用程序包装在`ToastProvider`中，它为`Toast`后代提供上下文。

```
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";
import { ToastProvider } from "react-toast-notifications";

ReactDOM.render(
  <React.StrictMode>
    <ToastProvider>
      <App />
    </ToastProvider>
  </React.StrictMode>,
  document.getElementById("root")
);

```

通过`useToast`钩子，我们可以访问`addToast`方法。它的第一个参数是包含 toast 消息的字符串，第二个参数是配置对象。在 config 对象中，我们将 toast 的外观设置为`success`，将`autoDismiss`设置为`true`。

```
import { useToasts } from "react-toast-notifications";

export default function App() {
  const { addToast } = useToasts();
  const successToast = () => {
    addToast("This is a success toast", {
      appearance: "success",
      autoDismiss: true,
    });
  };
  return (
    <div className="App">
      <button onClick={successToast}>show success toast</button>
    </div>
  );
}

```

### 烤面包变体

React toast 通知中有四种 Toast 通知:`success`、`error`、`warning`和`info`。

```
addToast("success toast", { appearance: "success"});
addToast("error toast", { appearance: "error"});
addToast("warning toast", { appearance: "warning"});
addToast("info toast", { appearance: "info"});

```

### 配置

我们可以通过向`ToastProvider`传递属性来配置 React Toast 通知中的 Toast。

```
<ToastProvider 
  placement="top-center" 
  transitionDuration={2000}
  autoDismiss="true"
 autoDismissTimeout={5000}>
  <App />
</ChakraProvider>

```

用`ToastProvider`配置时，`placement`、`transitionDuration`、`autoDismiss`、`autoDismissTimeout`作为道具传递，然后由后代 toast 组件继承。

### 特征

我们可以从`useToast`钩子访问以下方法:

```
const { addToast, removeToast, removeAllToasts, updateToast } = useToast()

```

我们已经知道了`addToast`方法是如何工作的。我们可以用`removeToast`删除特定的祝酒词，用`removeAllToasts`删除所有打开的祝酒词。当我们想要在一个操作完成后更新一个 toast 时，比如说，在获取了一些数据后，`updateToast`方法会很有用。

### 开发者体验

尽管 React Toast 通知的 API 很简单，但它并不是最容易使用的。就文档而言，还有改进的空间，特别是在展示如何将用户 ID 传递给 toast，以及如何删除和更新 toast 方面。总的来说，React Toast 通知缺乏广泛而清晰的文档。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 反应迟钝

[React](https://github.com/fkhadra/react-toastify)[–](https://github.com/fkhadra/react-toastify)[Toastify](https://github.com/fkhadra/react-toastify)是一个高度可定制的 React toast 库，允许我们轻松地将通知添加到我们的应用程序中。

### 使用

React-Toastify 有一个类似于 react-hot-toast 的 API。通过`toast` API，我们可以访问不同的 toast 变体。出现祝酒词需要使用`ToastContainer`组件。

```
import { ToastContainer, toast } from "react-toastify";
import "react-toastify/dist/ReactToastify.css";

export default function App() {
  const successToast = () => toast.success("This is a success toast!");
  return (
    <div className="App">
      <button onClick={successToast}>show success toast</button>
      <ToastContainer />
    </div>
  );
}

```

### 烤面包变体

React-Toastify 是本文中拥有最多变体的库。它提供了六种 toast 变体:`success`、`error`、`warn`、`info`、`dark`，以及一个默认变体。

```
toast.success("success toast")
toast.error("erorr toast")
toast.warn("warning toast")
toast.info("info toast")
toast.dark("dark mode toast")
toast("default toast")

```

### 配置

我们可以将一个`options`对象作为第二个参数传递给`toast`。在这里，我们可以定义祝酒词的行为。

```
toast.dark("This is a success toast!", {
  position: toast.POSITION.BOTTOM_RIGHT,
  autoClose: 2000,
  pauseOnHover: true,
  draggable: true,
});

```

我们可以用`cssTransition`方法替换默认的 toast 动画。它接受一个对象，在这个对象中我们为 toast 定义了`enter`和`exit`动画。

```
import "animate.css/animate.min.css";
import { ToastContainer, toast, cssTransition } from "react-toastify";

const bounce = cssTransition({
  enter: "animate__animated animate__bounceIn",
  exit: "animate__animated animate__bounceOut",
});

const customAnimationToast = () => {
  toast.dark("Hey 👋, see how easy!", {
    transition: bounce,
  });
};

export default function App() {
  return (
    <div className="App">
      <button onClick={customAnimationToast}>show bounce toast</button>
      <ToastContainer transition={bounce} />
    </div>
  );
}

```

### 特征

我们可以在 React-Toastify 中以编程方式更新祝酒词。然而，与 react-hot-toast 不同，在 react-hot-toast 中，我们可以在变量中存储对 toast 的引用，我们必须使用`useRef`钩子来跟踪我们想要更新的 toast。

```
import { useRef } from "react";

export default function App() {
  let toastId = useRef(null);

  const successToast = () => {
    toastId = toast("Fetching data...", { autoClose: false });
  };

  const updateToast = () => {
    toast.update(toastId, { type: toast.TYPE.INFO, autoClose: 5000 });
  }

  return (
    <div>
      <button onClick={successToast}>show success toast</button>
      <Button onClick={updateToast}>update toast</Button>
      <ToastContainer ref={toastId} />
    </div>
  );
}

```

### 开发者体验

React-Toastify 比上面任何一个库都有更多的 toast 变体，并且它有很多令人敬畏的特性。至于 API，它非常类似于 react-hot-toast 和 React Toast 通知 API。

### 结论

在比较了这四个 React toast 库并了解了如何在我们的应用程序中使用它们之后，是时候分享一下对其中最佳库的看法了。我最喜欢用 react-hot-toast。除了精心设计的 toast 和简单明了的 API，它还附带了一个`promise` toast，可以很容易地异步更新 toast。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)