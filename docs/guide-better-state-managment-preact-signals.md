# 用预动作信号进行更好状态管理的指南

> 原文：<https://blog.logrocket.com/guide-better-state-managment-preact-signals/>

Preact 是一个小型的、[轻量级的替代产品，与 React](https://preactjs.com/) 具有相同的现代 API。作为其最新更新的一部分，Preact [最近发布了 Signals](https://preactjs.com/blog/introducing-signals/) ，这是一个高性能状态管理库，具有一组用于管理应用程序状态的反应原语。

当值改变时，Signals 自动更新状态，没有依赖数组，并直接更新 DOM 这使得它快如闪电。Preact 团队声称这使得信号成为管理状态的更好方式。

在本文中，我们将了解 Signals 的工作原理、优点、特性等等。

*向前跳转:*

## 什么是信号？

Signals 是一个用[纯 JavaScript](https://blog.logrocket.com/tag/vanilla-javascript/) 编写的库，这意味着我们可以在 [Vue.js](https://blog.logrocket.com/tag/vue/) 、 [Angular](https://logrocket.com/for/angular) 、 [Svelte](https://blog.logrocket.com/tag/svelte/) 以及任何带有 JavaScript 文件的应用中使用它。虽然它可以在任何 JavaScript 文件中使用，但 Preact 团队还为 [Preact](https://github.com/preactjs/signals) 和 [React](https://github.com/preactjs/signals/blob/main/packages/react/README.md#react-integration) 开发了包。Signals 是从 [Solid](https://blog.logrocket.com/whats-new-solidjs-v1-3/) 和 Vue 中汲取灵感而打造的。

根据[文档](https://preactjs.com/guide/v10/signals/)的说法，信号的核心是一个具有`.value`属性的对象，该属性包含一些值。当信号的值改变时，从组件中访问信号的 value 属性会自动更新该组件。我们将对此有更多的了解，并在本文的后面看到它是如何工作的。

在撰写本文时，Preact 信号包目前有三个版本:

## 信号背后的动机

Signals 团队由拥有多年为初创公司和大型企业开发软件应用程序经验的开发人员组成。随着时间的推移，他们发现了管理应用程序状态的一个反复出现的问题。

为了理解 Signals 要解决的问题，让我们考虑一个真实的场景。假设一个父组件拥有某个状态，有两个子组件，其中一个子组件必须访问该状态:

```
import { useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);
  function addToCount() {
    setCount((count) => count + 1);
  }
  return (
    <div className="App">
      <Parent count={count} addToCount={addToCount} />
    </div>
  );
}

function Parent({ count, addToCount }) {
  return (
    <div>
      <ChildOne />
      <ChildTwo count={count} addToCount={addToCount} />
    </div>
  );
}

function ChildOne() {
  return <p>A Dummy Counter App</p>;
}

function ChildTwo({ count, addToCount }) {
  return (
    <div>
      <span>{count}</span>
      <div>
        <button onClick={addToCount}>+</button>
      </div>
    </div>
  );
}
```

在下面的代码片段中，`Parent`充当将`count`状态传递给`ChildTwo`的容器，而`ChildOne`是一个无状态组件。问题在于当我们点击按钮**更新状态**时会发生什么。

`Parent`、`ChildOne`和`ChildTwo`都会重新渲染，即使`ChildTwo`是唯一直接使用状态的组件。虽然我们可以使用 [`memoization`](https://blog.logrocket.com/react-re-reselect-better-memoization-cache-management/) 等方法解决这个不必要的重新渲染问题，但 Preact 团队的目标是用 Signals 彻底消除这个问题。

我们可以通过切换到信号来解决不必要的重新渲染的性能问题:

```
import { signal } from "@preact/signals-react"; //importing signal

export default function App() {
  const count = signal(0); //creating the signal

  function addToCount() {
    return (count.value = count.value + 1); //updating the count
  }

  return (
    <div className="App">
      <Parent count={count} addToCount={addToCount} />
    </div>
  );
}

function Parent({ count, addToCount }) {
  return (
    <div>
      <ChildOne />
      <ChildTwo count={count} addToCount={addToCount} />
    </div>
  );
}

function ChildOne() {
  return <p>A Dummy Counter App</p>;
}

function ChildTwo({ count, addToCount }) {
  return (
    <div>
      <span>{count.value}</span> //accessing the count value
      <div>
        <button onClick={addToCount}>+</button>
      </div>
    </div>
  );
}
```

只需更改四行代码，应用程序的性能就变得更高了。信号是如何实现这一点的？它如何防止不必要的重新渲染，并在响应状态更新时确保最佳性能？

这是可能的，因为信号是包含值的对象。与直接通过组件树传递状态的`useState`场景不同，Signals 只传递对象，作为对值的引用。这确保了当状态改变时，只有访问`signal`对象的`.value`的组件才会呈现。

信号跟踪它们的值何时被访问和更新。当信号的值改变时，从组件中访问信号的`.value`属性会自动重新呈现组件。因此，信号可以在不重新呈现它所经过的组件的情况下被更新，因为那些组件看到的是信号而不是它的值。

## 什么使信号不同？

Signals 是在考虑性能的基础上构建的，以下特性和行为使其与众不同。Signals [背后的团队是这样描述它的](https://preactjs.com/blog/introducing-signals/#:~:text=To%20achieve%20this%20level%20of%20performance%2C%20signals%20were%20built%20on%20these%20key%20principles%3A):

*   默认情况下是惰性的:只观察和更新当前在某处使用的信号——断开的信号不会影响性能
*   最佳更新:如果信号的值没有改变，使用该信号值的组件和效果不会被更新，即使信号的依赖关系已经改变
*   最佳依赖跟踪:框架跟踪所有依赖于你的信号——没有像钩子那样的依赖数组
*   直接访问:访问组件中的信号值会自动订阅更新，而不需要选择器或挂钩

## 用信号管理状态的好处

用信号管理状态的第一个好处是，当状态改变时，信号不会重新呈现整个组件或应用程序。相反，它更新应用程序中附加到被跟踪的状态值的部分。这确保应用程序保持高性能，同时确保对用户操作的反应。根据文档中的[所述，信号之所以独特而伟大，是因为:](https://preactjs.com/guide/v10/signals/)

> 状态更改会以最有效的方式自动更新组件和 UI。自动状态绑定和依赖性跟踪允许信号提供出色的人机工程学和生产力，同时消除最常见的状态管理脚枪。

其次，我们可以声明全局信号，并在整个应用程序中将它们导入到`children`组件中。它通过提供简单直观、易于使用的即插即用 API，简化了状态管理的复杂性。

最后，我们不必像 React 的 [`useEffect`](https://blog.logrocket.com/guide-to-react-useeffect-hook/) Hook 那样设置依赖数组。信号会自动检测依赖关系，并在依赖关系改变时调用效果。

## 用信号管理 React 应用程序状态

让我们实现我们的信号知识，并用它来管理一个简单的计数器应用程序的状态。

首先，用`npx create-react-app signals-react-app`设置一个新的 React 应用。然后，使用`npm install @preact/signals-react`安装 React 包。

将下面的代码复制到您的`App.js`文件中，以设置计数器应用程序:

```
import { signal } from "@preact/signals-react";

const count = signal(0);

function increaseCount() {
  return (count.value = count.value + 1);
}
function reduceCount() {
  return (count.value = count.value - 1);
}

export default function App() {
  return (
    <div className="App">
      <span>{count}</span>
      <div>
        <button onClick={reduceCount}>-</button>
        <button onClick={increaseCount}>+</button>
      </div>
    </div>
  );
}
```

在上面的代码中，我们从`@preact/signals-react`包中访问了`signal`对象，并使用它来设置一个`count`状态。我们传递给信号的值现在可以从它的`.value`属性中访问。

其次，我们设置了两个函数，`increaseCount`和`reduceCount`，分别增加和减少计数的值。我们将这些功能传递给按钮的`onClick`处理程序。

这是信号的一个基本实现，但是它很好地向我们展示了它是如何工作的。

## 使用计算信号的驾驶状态

除了`signal`对象，Signals 还有一个`computed`函数，我们可以用它来设置派生状态。使用`computed`，我们可以从其他信号的值中创建一个新信号。返回的计算信号是只读的，并且当依赖于它的任何信号改变时会自动更新。

让我们建立一个虚拟用户认证应用程序，看看`computed`是如何工作的。下面是一些我们将要建造的东西的图片:

![Signals Examples for State Management](img/e1bdfab460e80a47b77ede692629e0fb.png)

我们将从创建一个包含状态和认证逻辑的`AuthSignal.js`文件开始。将以下代码复制并粘贴到文件中:

```
import { signal, computed } from "@preact/signals-react";

export const user = signal(null); //the default state

//derived state based on whether a user exists
export const isLoggedIn = computed(() => {
  return !!user.value;
});
```

在上面的代码中，我们从`@preact/signals-react`导入了`signal`和`computed`。我们还设置了一个默认值为`null`的`user`信号，这意味着一开始没有用户登录。

我们还使用了`user`信号来导出`isLoggedIn`的值。如果用户存在，`isLoggedIn`将会是`true`。然后，我们导出`user`和`isLoggedIn`用于应用程序的其他部分。

`Header`组件由登录用户名和两个按钮组成:一个用于登录，另一个用于注销。

要设置它，请复制并粘贴下面的代码:

```
import { user } from "../AuthSignals";

const dummyUserData = {
  name: "John Doe",
  email: "[email protected]"
};
export default function Header() {
  return (
    <header>
      <h1>A cool header</h1>
      <ul>
        {user.value && <li>{user.value.name}</li>}
        {!user.value ? (
          <li>
            <button onClick={() => { user.value = dummyUserData }}>
              Login
            </button>
          </li>
        ) : (
          <li>
            <button onClick={() => { user.value = null }}>
              Logout
            </button>
          </li>
        )}
      </ul>
    </header>
  );
}
```

让我们分解一下，看看我们做了什么:

*   从`AuthSignals`导入`user`信号
*   定义了一个包含一些随机用户细节的`dummyUserData`
*   如果有登录用户，则有条件地呈现用户名
*   根据`user`信号值的状态有条件地呈现登录和注销按钮
*   当点击**登录按钮**时，我们用`dummyUserData`填充`user`信号
*   当点击**注销按钮**时，我们将`user`信号设置为`null`

### 设置`Home`组件

`Home`组件显示一条简单的消息，通知我们是否登录。让我们来设置一下:

```
import { isLoggedIn } from "../AuthSignals";
export default function Home() {
  return (
    <div>
      <span>
        {isLoggedIn.value ? "You are logged in" : "You are not logged in"}
      </span>
    </div>
  );
}
```

记住，`isLoggedIn`是一个派生状态，其`Boolean`值根据`user`信号的值而变化。我们根据`isLoggedIn`的值有条件地呈现消息。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

最后，我们将组件放在`App.js`文件中:

```
import Header from "./components/Header";
import Home from "./components/Home";
export default function App() {
  return (
    <div>
      <div className="App">
        <Header />
        <Home />
      </div>
    </div>
  );
}
```

至此，我们已经了解了更多关于`computed`的知识，并使用它创建了一个虚拟用户认证系统。

## Preact 信号与 SolidJS 信号

和 Preact 一样， [Solid](https://www.solidjs.com/) 也有自己的状态管理解决方案，叫做[信号](https://www.solidjs.com/tutorial/introduction_signals)。正如我们前面看到的，Solid 是 Preact 团队从中获得灵感的框架之一。

Solid 提供了一个类似 React 的`useState`钩子的`createSignal`函数:

```
import { createSignal } from "solid-js";

const [count, setCount] = createSignal(0);

function Counter() {
  const increment = () => setCount(count() + 1);

  return (
  <button type="button" onClick={increment}>
    {count()}
  </button>);
}
```

它将初始状态作为一个参数，并返回两个我们可以通过数组析构来访问的项。数组中的第一项是`getter`，第二项是状态的`setter`。

注意，像 Preact 信号一样，我们不需要将`createSignal`保留在组件函数中。与 Preact Signals 的`computed`功能相似，Solid 也提供了一种创建派生信号的方式:

```
const doubleCount = () => count() * 2;

return (
  <button type="button" onClick={increment}>
    {count()}
    <div>Count: {doubleCount()}</div>;
  </button>);
```

尽管两种状态的管理包有一些相似之处，但两者也有一些不同之处。

第一，Preact 和 Solid 的信号有不同的 API，其中 Solid 的实现类似 react 的`useState`。Solid 创建衍生信号的方法与 Preact 不同。与 Preact 不同，Solid 的信号只能用于固态应用。

## 结论

Preact Signals 是一个全新的、受欢迎的解决方案，可以解决与状态管理相关的问题。它仍然是相对较新的，但它似乎有一个充满希望的未来，因为它的简约和简单的架构。

虽然你可能不会马上接受在大型项目中使用它，但是考虑在个人和实践项目中尝试一下。你可能会爱上它。通过[计数器应用](https://codesandbox.io/s/preact-signals-counter-app-fz6hix)和[用户认证系统](https://codesandbox.io/s/preact-signals-auth-demo-fpidmp)获取我们构建的演示应用的代码。

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