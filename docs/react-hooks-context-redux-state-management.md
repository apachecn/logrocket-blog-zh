# React 钩子 vs. Redux:钩子和上下文会取代 Redux 吗？- LogRocket 博客

> 原文：<https://blog.logrocket.com/react-hooks-context-redux-state-management/>

***编者按:**本文最后更新于 2022 年 6 月 15 日，以反映 React v18 中所做的更改。*

Redux Toolkit 降低了将 Redux 引入我们的应用程序的复杂性。然而，除了 React v16.8 中引入的`useContext`和`useReducer`钩子之外，通过使用 React 上下文 API，我们可以实现健壮的状态管理并避免 prop drilling，而无需导入额外的库。

在最好的情况下，这使它成为 React 应用程序中状态管理的一个不完美的解决方案。然而，太多的 React 开发人员默认使用 Redux 进行状态管理，而没有考虑其他选择。

在本教程中，我们将向您介绍用于状态管理的 [React 上下文 API](https://reactjs.org/docs/context.html) ，并解释 React 钩子和上下文 API 如何取代 Redux。

我们将讨论以下内容:

如果你更喜欢视觉学习，下面的视频描述了 React Context API，并提供了为什么应该用 React 钩子和上下文替换 Redux 的一些理由。

 [https://www.youtube.com/embed/eBYJ7O482Dc?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&start=1&wmode=transparent](https://www.youtube.com/embed/eBYJ7O482Dc?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&start=1&wmode=transparent)

视频

为了在 React 中处理断开的组件之间的数据，开发人员使用 prop drilling。

没有组件可以访问的全局状态。因此，如果您想将数据从顶级组件传递到第五级组件，那么您必须将数据作为道具传递到树的每一级，直到到达您想要的组件。

这导致编写大量额外的代码，并且赋予组件永远不会使用的属性也会影响它们的架构设计。

为了解决这个问题，我们需要提供一个所有组件都可以访问的全局状态，不管它们的嵌套有多深。

通过解决这个问题，Redux，一个用于管理应用程序状态的开源 JavaScript 库，成为 React 开发人员的首选解决方案。

## Redux vs. React 钩子和上下文 API

在某种程度上，Redux 对于 React 应用程序中的状态管理非常有效，并且有一些优点。然而，它的冗长使它很难学习，并且使它工作所需的额外代码会引入许多不必要的复杂性。

另一方面，有了 React Hooks 和 [`useContext` API](https://podcasts.apple.com/us/podcast/podrocket-a-web-development-podcast-from-logrocket/id1539945251) ，就不需要安装外部库或者添加一堆文件和文件夹来让我们的应用工作。这使得它成为 React 应用程序中处理全局状态管理的更简单、更直接的方法。

让我们仔细看看 Redux、React Hooks 和 Context API，看看它们是如何工作的，开发人员在使用这些工具时面临哪些挑战，以及使用 React Hooks 和 Context 如何帮助您克服一些与 Redux 相关的常见问题。

## Redux 是什么？

根据 [Redux 文档](https://redux.js.org/)，Redux 是 JavaScript 应用程序的可预测状态容器，它帮助我们编写行为一致、在不同环境中运行且易于测试的应用程序。

正确钻取一个缺点是，它要求我们编写大量额外的代码来从顶级组件访问数据。对于 Redux，这个缺点变得更加严重，因为它需要额外的代码来设置全局状态。Redux 需要三个主要的构建部分来运行:动作、reducers 和存储。

### 行动

动作是用于向 Redux 存储发送数据的对象。它们通常有两个属性:一个`type`属性用于描述动作做什么，一个`payload`属性包含应该在应用程序状态中更改的信息:

```
// action.js
const reduxAction = payload => {
  return {
    type: 'action description',
    payload
  }
};

export default reduxAction;

```

`type`通常全部大写，单词之间用下划线隔开。比如`SIGNUP_USER`或者`DELETE_USER_DATA`。

### 还原剂

Reducers 是实现动作行为的纯函数。它们获取当前应用程序状态，执行一个操作，然后返回一个新状态:

```
const reducer = (state, action) => {
  const { type, payload } = action;
  switch(type){
    case "action type":
      return {
        ["action description"]: payload
      };
    default:
      return state;
  }
};

export default reducer;

```

### 商店

应用程序的状态保存在存储中。任何 Redux 应用程序中只有一个商店:

```
import { createStore } from 'redux'

const store = createStore(componentName);

```

由于我们的应用程序只能有一个 Redux 存储，为了给所有组件创建一个根 Redux，我们需要 Redux 的 [`combineReducers`](https://redux.js.org/api/combinereducers) 方法。设置 Redux 需要大量的代码，想象一下当我们有多个组件要处理时，我们的代码库会是什么样子。

尽管 Redux 解决了我们的状态管理问题，但它使用起来确实非常耗时，有一个困难的学习曲线，并且给我们的应用程序带来了全新的复杂性。

幸运的是，React 上下文 API 解决了这个问题。当与 React Hooks 结合使用时，我们有了一个状态管理解决方案，它的设置耗时更少，具有更简单的学习曲线，并且需要最少的代码。

## 什么是 React 上下文 API？

React v16.3 中引入了新的上下文 API。React 上下文使您能够共享 React 组件树的全局数据，如当前已验证的用户、主题或首选语言。

根据 [React 文档](https://reactjs.org/docs/context.html) , 上下文提供了一种通过组件树传递数据的方式，而不必在每一层手动向下传递属性。本质上，React 上下文 API 是 React 管理不直接连接的多个组件中的状态的方式。

为了创建上下文，我们将使用 React 的`createContext`方法，该方法接受一个参数作为其默认值:

```
import React, {createContext} from 'react';

const newContext = createContext({ color: 'black' });

```

`createContext`方法返回一个带有`Provider`和`Consumer`组件的对象:

```
const { Provider, Consumer } = newContext;

```

`Provider`组件使状态对所有子组件可用，不管它们在组件层次结构中的嵌套有多深。`Provider`组件接收一个`value`道具，我们将在这里传递当前值:

```
<Provider value={color: 'blue'}>
  {children}
</Provider>

```

顾名思义，`Consumer`消耗来自`Provider`的数据，而不需要任何道具钻探:

```
<Consumer>
  {value => <span>{value}</span>}}
</Consumer>

```

如果没有钩子，与 Redux 相比，上下文 API 可能看起来不怎么样。但是，当与`useReducer`挂钩结合使用时，我们有了一个最终解决 React 中状态管理问题的解决方案。

## **什么是 React 钩子？**

钩子是一种能够在基础代码中执行定制代码的函数。钩子本质上是特殊的函数，允许我们钩入 React 的核心特性。

React 钩子通过允许我们容易地处理功能组件的状态管理，提供了一种编写基于类的组件的替代方法。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### `useContext`钩子

您可能已经注意到，在描述 React Context API 时，我们需要将我们的内容包装在一个`Consumer`组件中，然后传递一个函数作为子组件，这样我们就可以访问或使用我们的状态。

这样做会引入不必要的组件嵌套，并增加代码的复杂性。挂钩让事情变得更加干净和简单。要用`useContext`钩子访问我们的状态，我们只需要用我们创建的`context`作为它的参数来调用它:

```
const newContext = React.createContext({ color: 'black' });

const value = useContext(newContext);

console.log(value); // this will return { color: 'black' }

```

现在，我们可以简单地通过`value`变量访问我们的状态，而不是将我们的内容包装在`Consumer`组件中。

### `useReducer`钩子

React v16.8 附带了`useReducer`钩子。就像 JavaScript 中的`reduce()`方法一样，`useReducer`钩子接收两个值作为它的参数，一个 reducer 函数和一个初始状态。然后，它返回一个新状态:

```
const [state, dispatch] = useReducer((state, action) => {
  const { type } = action;
  switch(action) {
    case 'action description':
      const newState = // do something with the action
      return newState;
    default:
      throw new Error()
  }
}, []);

```

在上面的代码块中，我们定义了状态和一个相应的方法`dispatch`来处理它。当我们调用`dispatch`方法时，`useReducer()`钩子将根据我们的方法在其动作参数中接收到的`type`执行一个动作:

```
...
return (
  <button onClick={() =>
    dispatch({ type: 'action type'})}>
  </button>
)

```

## 如何在 React 上下文中使用`useReducer`钩子

### 建立我们的商店

现在我们已经知道了上下文 API 和`useReducer`钩子是如何单独工作的，让我们看看当我们将它们结合起来为我们的应用程序获得理想的全局状态管理解决方案时会发生什么。

我们将在一个`store.js`文件中创建我们的全局状态:

```
// store.js
import React, {createContext, useReducer} from 'react';

const initialState = {};
const store = createContext(initialState);
const { Provider } = store;

const StateProvider = ( { children } ) => {
  const [state, dispatch] = useReducer((state, action) => {
    switch(action.type) {
      case 'action description':
        const newState = // do something with the action
        return newState;
      default:
        throw new Error();
    };
  }, initialState);

  return <Provider value={{ state, dispatch }}>{children}</Provider>;
};

export { store, StateProvider }

```

在我们的`store.js`文件中，我们使用 React 中的`createContext()`方法来创建一个新的上下文。

记住，`createContext()`方法返回一个带有`Provider`和`Consumer`组件的对象。这一次，我们将只在需要访问我们的状态时使用`Provider`组件和`useContext`钩子。

注意我们是如何在`StateProvider`中使用`useReducer`钩子的。当我们需要操作我们的状态时，我们将调用`dispatch`方法并传入一个对象，将期望的`type`作为它的参数。

在我们的`StateProvider`中，我们用一个`state`的`value`道具和来自`useReducer`钩子的`dispatch`返回了我们的`Provider`组件。

### 全局访问状态

为了全局访问我们的状态，我们需要在我们的`StoreProvider`中包装我们的根`<App/>`组件，然后在我们的`ReactDOM.render()`函数中呈现它:

```
// root index.js file
import { createRoot } from "react-dom/client";
import App from './App';
import { StateProvider } from './store.js';

const rootElement = document.getElementById("root");
const root = createRoot(rootElement);

root.render(
  <StateProvider>
    <App />
  </StateProvider>
);

```

现在，我们的存储库`context`可以从组件树中的任何组件访问。为此，我们将从 React 导入`useContext`钩子，从我们的`./store.js`文件导入`store`:

```
// exampleComponent.js
import React, { useContext } from 'react';
import { store } from './store.js';

const ExampleComponent = () => {
  const globalState = useContext(store);
  console.log(globalState); // this will return { color: red }
};

```

### 从状态中添加和移除数据

我们已经看到了如何访问我们的全局状态。为了从我们的状态中添加和删除数据，我们将需要来自我们的`store`上下文的`dispatch`方法。我们只需要调用`dispatch`方法，并传入一个带有`type`参数的对象，即在我们的`StateProvider`组件中定义的动作描述:

```
// exampleComponent.js
import React, { useContext } from 'react';
import { store } from './store.js';

const ExampleComponent = () => {
  const globalState = useContext(store);
  const { dispatch } = globalState;

  dispatch({ type: 'action description' })
};

```

## 结论

在本教程中，我们探索了在 React 应用程序中使用 Redux 进行状态管理和使用 React 上下文 API 以及`useContext`钩子和`useReducer`钩子的[之间的区别。当我们使用 Redux 进行状态管理时，我们必须处理 prop drilling，这意味着我们必须编写大量额外的代码来启动应用程序。通过使用上下文在组件中嵌套组件，父组件的所有功能在子组件中都可用。](https://blog.logrocket.com/react-context-api-deep-dive-examples/)

我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。

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