# 使用 Kea - LogRocket 博客简化 React 状态管理

> 原文：<https://blog.logrocket.com/simplify-react-state-management-with-kea/>

有几种方法可以在 React 组件之间共享数据。首先，我们可以通过 props 将数据从父母传递给孩子。React 还具有上下文 API，可以在具有任何关系的组件之间传递数据，只要我们将上下文提供者组件包装在 React 组件内，我们就可以在这些组件之间共享数据。

我们还有全球状态管理解决方案，如 [Redux](https://redux.js.org/) 和 [MobX](https://mobx.js.org/README.html) ，让我们可以在整个应用程序中轻松共享数据。

任何想要获得状态的最新值的组件都可以订阅带有全局状态管理解决方案的数据存储。

另一个状态管理解决方案是 [Kea](https://kea.js.org/) ，它的工作方式类似于 Redux。我们可以订阅用 Kea 创建的存储来获取数据并设置最新状态。Kea 是由 Redux 驱动的，所以很多概念，比如 reducers 和 stores 也将与 Kea 一起使用。

在本文中，我们将了解如何在 React 应用程序中使用 Kea 作为全局状态管理解决方案。

## 基本状态管理

我们可以通过运行以下命令，使用 create -react-app 创建一个应用程序:

```
npx create-react-app kea-app
```

然后我们就可以安装 Kea 需要的库了，就是 Kea 本身，Redux，React-Redux。为了安装它们，我们运行以下代码:

```
npm i kea redux react-redux reselect
```

然后，我们可以通过编写以下代码，使用 Kea 编写一个简单的应用程序，作为我们应用程序范围的全局状态管理解决方案:

```
//index.js
import React from "react";
import ReactDOM from "react-dom";
import { resetContext, getContext } from "kea";
import { Provider } from "react-redux";
resetContext({
  createStore: {},
  plugins: []
});

import App from "./App";

const rootElement = document.getElementById("root");
ReactDOM.render(
  <Provider store={getContext().store}>
    <App />
  </Provider>,
  rootElement
);

```

```
//App.js
import React from "react";
import { kea, useActions, useValues } from "kea";

const logic = kea({
  actions: () => ({
    setName: name => ({ name })
  }),

  reducers: ({ actions }) => ({
    name: [
      "",
      {
        [actions.setName]: (_, payload) => payload.name
      }
    ]
  })
});

const Name = () => {
  const { name } = useValues(logic);
  return <p>{name}</p>;
};

export default function App() {
  const { setName } = useActions(logic);
  return (
    <div className="App">
      <input onChange={e => setName(e.target.value)} />
      <Name />
    </div>
  );
}

```

在上面的代码中，我们导入了 React Redux 的`Provider`组件，然后将其包装在整个应用程序中，让 Kea 作为应用程序范围的状态管理库。

然而，我们将`getContext().store`作为商店的值传入，而不是像我们通常做的那样作为 Redux 商店。我们将`createStore`和`plugins`属性保留为一个空对象，并在传递给`resetContext`的对象中包含一个数组，因为我们没有使用任何插件，并且在创建商店时没有更改任何选项。

然后在`App.js`中，我们用`kea`函数创建一个对象，它具有我们将在商店中使用的逻辑。它包括为我们的存储检索和设置值的逻辑。

我们在`App.js`中有以下内容来创建`logic`对象，我们将使用它从存储中读取和写入值:

```
const logic = kea({
  actions: () => ({
    setName: name => ({ name })
  }),

  reducers: ({ actions }) => ({
    name: [
      "",
      {
        [actions.setName]: (_, payload) => payload.name
      }
    ]
  })
});
```

我们有带有方法的`actions`属性，我们将使用这些方法来设置存储中的`name`状态的值。`reducers`属性将动作名作为对象的键。

reducer 数组的第一个条目是它的默认值。

它使用函数名作为 reducer 函数的标识符，我们在 reducer 数组的第二个条目的对象中有这个函数。像 Redux reducer 一样，我们用 reducer 函数返回我们想要在存储中设置的值。

然后我们通过调用 Kea 的`useActions`函数和传入的`logic`对象来设置存储中的`name`值。它有`setName`方法，我们可以用它返回的对象调用它。

在`App`的输入元素中，我们调用`setName`将`name`的值设置为输入的值。

然后在`Name`组件中，我们调用 Kea 的`useValues`方法，使用我们之前创建的`logic`对象作为参数，然后从存储中获取`name`值并呈现它。

因此，输入到输入中的文本将显示在其下方的`Name`组件中。

## 听众

侦听器是在调度操作后运行的函数。如果我们希望能够取消侦听器中的这些操作，它们会很有用。

要使用它，我们可以通过运行以下命令来添加`kea-listeners`包:

```
npm i kea-listeners
```

我们可以用它来监听 Kea 正在执行的操作，然后用它来触发另一个操作，如下所示:

```
//index.js
import React from "react";
import ReactDOM from "react-dom";
import { resetContext, getContext } from "kea";
import { Provider } from "react-redux";
import listeners from "kea-listeners";
import App from "./App";

resetContext({
  createStore: {},
  plugins: [listeners]
});

const rootElement = document.getElementById("root");
ReactDOM.render(
  <Provider store={getContext().store}>
    <App />
  </Provider>,
  rootElement
);

```

```
//App.js
import React from "react";
import { kea, useActions, useValues } from "kea";

const logic = kea({
  actions: () => ({
    setCount: count => ({ count }),
    setDoubleCount: doubleCount => ({ doubleCount })
  }),

  listeners: ({ actions, values, store, sharedListeners }) => ({
    [actions.setCount]: ({ count }) => {
      actions.setDoubleCount(count * 2);
    }
  }),

  reducers: ({ actions }) => ({
    count: [
      0,
      {
        [actions.setCount]: (_, payload) => payload.count
      }
    ],
    doubleCount: [
      0,
      {
        [actions.setDoubleCount]: (_, payload) => payload.doubleCount
      }
    ]
  })
});

const Count = () => {
  const { count, doubleCount } = useValues(logic);
  return (
    <p>
      {count} {doubleCount}
    </p>
  );
};

export default function App() {
  const { count } = useValues(logic);
  const { setCount } = useActions(logic);
  return (
    <div className="App">
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <Count />
    </div>
  );

```

在上面的代码中，我们通过将`listeners`插件添加到数组中来添加`listeners`插件，我们将该数组设置为`index.js`中`plugins`属性的值。

然后我们可以监听正在`listeners`属性中运行的`actions.setCount`动作。将`listeners`属性设置为一个对象，该对象接受具有`actions`、`values`、`store`和`sharedListeners`属性的对象。

在上面的例子中，我们通过使用`actions`属性访问 action 方法来调用`setDoubleCount`动作。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们还定义了`doubleCount`缩减器，这样我们就可以像上面一样调用`setDoubleCount`动作来更新`doubleCount`状态的值。然后在`Count`组件中，我们用`logic`调用`useValues`来获取`count`和`doubleCount`并显示值。

因此，当我们单击 Increment 按钮时，我们得到一个递增 1 的计数，即`count`，另一个递增 2 的计数，即`doubleCount`。

## 取消操作

我们可以添加一个`breakpoint`方法调用，它返回一个等待指定毫秒数的承诺，如果再次调用相同的操作，我们可以取消该操作。

例如，我们可以编写以下代码来创建一个可取消的操作:

```
//App.js
import React from "react";
import { kea, useActions, useValues } from "kea";

const logic = kea({
  actions: () => ({
    setName: name => ({ name }),
    setResult: result => ({ result })
  }),
  listeners: ({ actions, values, store, sharedListeners }) => ({
    [actions.setName]: async ({ name }, breakpoint) => {
      await breakpoint(3000);
      const res = await fetch(`https://api.agify.io?name=${name}
      `);
      breakpoint();
      actions.setResult(await res.json());
    }
  }),

  reducers: ({ actions }) => ({
    name: [
      "",
      {
        [actions.setName]: (_, payload) => payload.name
      }
    ],
    result: [
      "",
      {
        [actions.setResult]: (_, payload) => payload.result
      }
    ]
  })
});

export default function App() {
  const { result } = useValues(logic);
  const { setName } = useActions(logic);
  return (
    <div className="App">
      <input onChange={e => setName(e.target.value)} />
      <button onClick={() => setName("")}>Cancel</button>
      <p>{result.name}</p>
    </div>
  );
}

```

在上面的代码中，我们有一个带`actions.setName`键的方法，它被设置为一个`async`函数，并接受一个`breakpoint`函数。我们调用`breakpoint`函数，等待 3000 毫秒，让我们取消请求。

我们还有一个取消按钮，它也调用`setName`动作，让我们取消动作。当第二次调用`setName`动作时，第二个断点调用中断取消动作。

## 萨迦

要将 sagas 合并到 Kea 中，我们必须通过运行以下命令来安装 Redux-Saga 和 Kea Saga 包:

```
npm install --save kea-saga redux-saga
```

然后，我们可以添加传奇，并使用它们与 Kea 如下:

```
//index.js
import React from "react";
import ReactDOM from "react-dom";
import { resetContext, getContext } from "kea";
import { Provider } from "react-redux";
import sagaPlugin from "kea-saga";
import App from "./App";
resetContext({
  createStore: true,
  plugins: [sagaPlugin({ useLegacyUnboundActions: false })]
});

const rootElement = document.getElementById("root");
ReactDOM.render(
  <Provider store={getContext().store}>
    <App />
  </Provider>,
  rootElement
);
```

在上面的代码中，我们添加了来自`kea-saga`的`sagaPlugin`作为我们的 Kea 插件。我们还必须将`createStore`设置为`true`,以便在我们的商店中使用传奇故事:

```
//App.js
import React from "react";
import { kea, useActions, useValues } from "kea";
import { put } from "redux-saga/effects";

const logic = kea({
  actions: () => ({
    setCount: count => ({ count }),
    setDoubleCount: doubleCount => ({ doubleCount })
  }),
  start: function*() {
    console.log(this);
  },

  stop: function*() {},

  takeEvery: ({ actions }) => ({
    [actions.setCount]: function*({ payload: { count } }) {
      yield put(this.actions.setDoubleCount(count * 2));
    }
  }),

  reducers: ({ actions }) => ({
    count: [
      0,
      {
        [actions.setCount]: (_, payload) => payload.count
      }
    ],
    doubleCount: [
      0,
      {
        [actions.setDoubleCount]: (_, payload) => payload.doubleCount
      }
    ]
  })
});

const Count = () => {
  const { count, doubleCount } = useValues(logic);
  return (
    <p>
      {count} {doubleCount}
    </p>
  );
};

export default function App() {
  const { setCount } = useActions(logic);
  const { count } = useValues(logic);
  return (
    <div className="App">
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <Count />
    </div>
  );
}

```

在上面的代码中，我们在传递给`kea`函数的对象中有我们的 saga 方法。每次发出新值时都会调用`takeEvery`,所以我们可以用它来运行代码，就像我们上面做的另一个动作一样。

我们使用`yield`关键字来返回用于设置动作的值。`put`用于安排商场的行动调度。

`this.actions.setDoubleCount(count * 2)`返回我们想要为`setDoubleCount`发出的值，因此`yield`和`put`一起将动作发送给`setDoubleCount`，并通过`useValue`钩子将值发送给我们的组件。

`start`方法是一个生成器函数，当我们的存储初始化时会调用它，所以我们可以将任何存储初始化代码放入其中。

因此，当我们点击 increment 按钮时，`setCount`函数被调用，它更新存储中的`count`状态。然后调用`takeEvery`方法，该方法调度`setDoubleCount`动作。然后，该值被发出，并在`Count`组件中结束。

所以左边的数字会增加 1，右边的数字会增加 2。

## 谢谢

Thunks 是 Redux 产生副作用的另一种方式。它让我们一次调度多个动作，也让我们用 Redux 运行异步代码。它在 Kea 做同样的事情。

要在 Kea 中使用 Thunk，我们需要安装 Kea Thunk 和 Redux Thunk 软件包，如下所示:

```
//index.js
import React from "react";
import ReactDOM from "react-dom";
import { resetContext, getContext } from "kea";
import { Provider } from "react-redux";
import thunkPlugin from "kea-thunk";
import App from "./App";
resetContext({
  createStore: true,
  plugins: [thunkPlugin]
});

const rootElement = document.getElementById("root");
ReactDOM.render(
  <Provider store={getContext().store}>
    <App />
  </Provider>,
  rootElement
);
```

```
//App.js
import React from "react";
import { kea, useActions, useValues } from "kea";
const delay = ms => new Promise(resolve => window.setTimeout(resolve, ms));

const logic = kea({
  actions: () => ({
    setCount: count => ({ count }),
    setDoubleCount: doubleCount => ({ doubleCount })
  }),

  thunks: ({ actions, dispatch, getState }) => ({
    setCountAsync: async count => {
      await delay(1000);
      actions.setCount(count);
      await delay(1000);
      actions.setDoubleCount(count * 2);
    }
  }),

  reducers: ({ actions }) => ({
    count: [
      0,
      {
        [actions.setCount]: (state, payload) => payload.count
      }
    ],
    doubleCount: [
      0,
      {
        [actions.setDoubleCount]: (state, payload) => payload.doubleCount
      }
    ]
  })
});
const Count = () => {
  const { count, doubleCount } = useValues(logic);
  return (
    <p>
      {count} {doubleCount}
    </p>
  );
};

export default function App() {
  const { setCountAsync } = useActions(logic);
  const { count } = useValues(logic);
  return (
    <div className="App">
      <button onClick={() => setCountAsync(count + 1)}>Increment</button>
      <Count />
    </div>
  );
}

```

在上面的代码中，我们用以下代码添加了`kea-thunk`插件:

```
plugins: [thunkPlugin]
```

然后在我们传递给`kea`函数的对象的`thunks`属性中，我们定义了 thunk，它有一个异步`delay`函数来暂停 thunk 1 秒钟。然后我们调度`setCount`动作，并在调用`delay`后调度`setDoubleAction`再等待一秒钟。

我们不能用 actions 函数运行异步代码，因为它们应该是纯同步函数。

在分派动作时，使用 thunks 是运行异步代码的好方法。

最后，我们应该得到一个增量按钮，我们可以点击这个按钮，在按钮被点击后一秒钟增加`count`，两秒钟后增加`doubleCount`。

## 结论

对于状态管理，Kea 是 Redux 的替代方案。它有各种插件来进行状态管理，比如 sagas 和 thunks。

它的工作方式类似于 Redux，并使用 Redux 作为其状态管理解决方案的基础。

它的工作原理是创建一个带有动作和减少器的存储。它们和 Redux 中的一样。此外，我们可以添加侦听器来侦听动作分派事件。我们也可以通过 Kea 的插件添加传奇和 thunks。

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