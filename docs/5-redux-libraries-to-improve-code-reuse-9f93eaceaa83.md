# 提高代码重用的 5 个关键 Redux 库

> 原文：<https://blog.logrocket.com/5-redux-libraries-to-improve-code-reuse-9f93eaceaa83/>

![](img/08f4f1fd9d9e7c75ec50fcaf4f63708c.png)

[**Redux**](https://redux.js.org) 是一个非常流行的状态容器，在许多现代前端 JavaScript 应用程序中使用。它是框架不可知的，可以用在用普通 JavaScript 或任何现代 JavaScript 框架如 React、Angular、VueJS 等构建的应用程序中。

使用 Redux 的最大好处之一是应用程序的状态是全局的，在一个地方通常被称为*存储*。Redux 架构利用了*动作*和*减少器*的概念来触发和处理存储中的状态变化。这使得整个应用程序的状态管理和更改检测非常可预测。

当处理一个项目时，Redux 通常与其他库一起使用，以增强应用程序的状态管理过程。

在本文中，我们将探索 5 个流行的 Redux 库，以提高跨应用程序的代码重用。下面是本文中库的快速列表:

1.  [重复动作](https://redux-actions.js.org)
2.  [重新选择](https://github.com/reduxjs/reselect)
3.  [Redux-Saga](https://redux-saga.js.org)
4.  [可重复观察的](https://redux-observable.js.org/)
5.  [规格化](https://github.com/paularmstrong/normalizr)

*本文中的大部分代码片段将基于通过* `*react-redux*` *连接到 Redux store 容器的 React 组件。*

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)

> Redux 中的通量标准动作。

Redux 动作提供了一种声明性的机制来描述可以改变应用程序状态的*意图*，将这种意图的实现留给应用程序的其他方面(*reducer*)。这种设计模式非常类似于 Flux 中使用的状态管理模式。

然而，使用 Redux，您会得到大量冗长的样板代码。您总是试图在您的 reducers 和 action creators 中跟踪 action 类型常量的名称。这有时可能是压倒性的，这就是 [**重复动作**](https://redux-actions.js.org) 发挥作用的地方。

### 通量标准行动(FSA)

如果 Flux 和 Redux 中的动作符合一致的结构，那么处理它们会容易得多。这就是为什么[**【FSA】**](https://github.com/redux-utilities/flux-standard-action)规范被创建的原因，以使行动标准化，从而符合一致且人性化的结构。

Redux-Actions 是一个非常轻量级的包，用于在 Redux 应用程序中创建和处理 Flux 标准动作。以下代码片段显示了典型 FSA 的格式:

```
// Basic Flux Standard Action
// with optional `meta` object
{
  type: 'PHOTO_FETCH_REQUESTED',
  payload: {
    photoId: 875
  },
  meta: {
    timestamp: Date.now()
  }
}

// Error Flux Standard Action
{
  type: 'PHOTO_FETCH_FAILED',
  payload: new Error('Photo not found.'),
  error: true
}
```

### 创建和处理操作

假设我们想为一个应用程序创建一个非常简单的可暂停计数器小部件。通常，计数器最基本的动作之一是递增计数器的值。我们可以使用`redux-actions`为这个动作创建一个缩减器，如下所示:

```
import { createAction, handleAction } from 'redux-actions';

// Default state
const INITIAL_STATE = {
  counter: 0
};

// Create the action
const increment = createAction('INCREMENT_COUNTER');

// Create the reducer
const incrementReducer = handleAction(
  increment,
  (state, action) => {
    return { ...state, counter: state.counter + 1 };
  },
  INITIAL_STATE
);
```

简单地增加计数器对我们的小部件来说还不够有趣。假设我们向状态添加了一个标志，指示计数器是否在递增。我们可以定义一个额外的动作和 reducer 来处理这个标志的切换。然而，我们可以使用`handleActions`创建一个单独的缩减器来处理这两个动作。

以下是展示商店外观的完整代码片段:

```
import { createStore } from 'redux';
import { createAction, handleActions } from 'redux-actions';

const INITIAL_STATE = {
  counter: 0,
  counting: false
};

const increment = createAction('INCREMENT_COUNTER');
const toggle = createAction('TOGGLE_COUNTING');

const reducer = handleActions(
  {
    [increment]: state => ({ ...state, counter: state.counter + 1 }),
    [toggle]: state => ({ ...state, counting: !state.counting })
  },
  INITIAL_STATE
);

const store = createStore(reducer, INITIAL_STATE);

export default store;
```

可以在 [**代码沙盒**](https://codesandbox.io/s/m72ml6y74j) 上获得计数器小部件的现场演示。

## 我们不只是写 Redux，我们也谈论它。现在听着:

或者以后订阅

### 重复使用作用减少剂

### 使用`redux-actions`创建动作的一个主要好处是，它使得隔离动作减少器成为可能，这反过来增强了动作减少器在具有类似需求的应用程序状态的其他部分中的重用。

可重用缩减器的一个很好的例子是处理异步操作的加载状态。下面是它可能的样子:

这里，我们创建了一个包装器，用加载状态来扩充一个已经存在的状态对象。然后，这个包装器可以用来创建几个带有加载状态的状态对象及其相应的缩减器。下面是一个简单的演示:

```
import { combineReducers } from 'redux';
import { createAction, handleActions } from 'redux-actions';

// Basic loading state
const LOADING_STATE = {
  counter: 0,
  active: false
};

const KEY_REGEX = /^[a-z]+(_[a-z]+)*$/i;

export default function withLoadingState (key, initialState, initialReducer) {

  if (!(typeof key === 'string' && KEY_REGEX.test(key))) {
    throw new Error(`Cannot use invalid key: '${key}'.`);
  }

  const KEY = key.toUpperCase();

  // Create loading state actions for the given key
  // Actions include: reset, start and complete loading state
  const reset = createAction(`${KEY}_LOADING_RESET`);
  const start = createAction(`${KEY}_LOADING_START`);
  const complete = createAction(`${KEY}_LOADING_COMPLETE`);

  // Create reducer for the loading state actions
  const loadingReducer = handleActions(
    {
      [reset]: state => ({ ...LOADING_STATE }),
      [start]: state => ({ ...state, active: true }),
      [complete]: state => ({ ...state, active: false, counter: state.counter + 1 })
    },
    LOADING_STATE
  );

  // Combine loading state with current state
  const state = {
    current: initialState,
    loading: LOADING_STATE
  };

  // Create a combined reducer for both loading state and current state
  const reducer = combineReducers({
    current: initialReducer,
    loading: loadingReducer
  });

  // Export the final state, reducer and actions
  return { state, reducer, actions: { reset, start, complete } };
};
```

您可以在 [**代码沙箱**](https://codesandbox.io/s/10071p67q4) 上获得一个现场演示，展示如何在应用程序的不同部分重用加载状态逻辑。

```
import { createActions, handleActions } from 'redux-actions';
import withLoadingState from './with-loading-state';

const POST_STATE = { data: null, error: null };

const { fetchSuccessful, fetchFailed } = createActions('POST_FETCH_SUCCESSFUL', 'POST_FETCH_FAILED');

const postReducer = handleActions(
  {
    [fetchSuccessful]: (state, action) => ({ ...state, error: null, data: action.payload.post }),
    [fetchFailed]: (state, action) => ({ ...state, error: action.error.message ? action.payload : null })
  },
  POST_STATE
);

/**
 * Augmenting current post state with loading state
 * Returned object contains: state, reducer and actions
 *
 * postWithLoading = {
 *  state: {
 *    current: { data: null, error: null },
 *    loading: { active: false, counter: 0 }
 *  },
 *  reducer: (Fn...),
 *  actions: {
 *    reset: { type: 'POST_LOADING_RESET' },
 *    start: { type: 'POST_LOADING_START' },
 *    complete: { type: 'POST_LOADING_COMPLETE' }
 *  }
 * }
 */
const postWithLoading = withLoadingState('POST', POST_STATE, postReducer);
```

冗余的记忆选择器。

> 使用 Redux 时，您经常要做的一件事是访问应用程序不同部分的全局状态。Redux 存储提供了获取存储当前状态的`getState()`方法。

然而，这个方法的问题是它返回存储的整个状态，即使您可能只对整体状态的一小部分感兴趣。

Redux 使用称为*选择器*的状态函数来选择整体状态的块。典型的选择器函数如下所示:

`getSelectedItems`选择器功能的问题在于它没有被记忆。因此，Redux 存储状态的每次变化都需要重新计算选择器函数。这就是 [**重选**](https://github.com/reduxjs/reselect) 库的用武之地。

```
function getSelectedItems(state) {
  const items = state.items;
  const selected = state.selected;

  return items.filter(item => selected.includes(item.id));
}
```

Reselect 是一个简单的库，用于创建记忆化的、可组合的选择器函数。重选选择器可用于高效地计算 Redux 存储中的派生数据。以下是使用通过重新选择创建的选择器函数的主要优点:

**选择器可以计算派生数据**，允许 Redux 存储最小可能状态

*   **选择器高效**。除非选择器的一个参数发生变化，否则不会重新计算选择器
*   **选择器是可组合的**。它们可以用作其他选择器的输入
*   撰写选择器

### 以下代码片段显示了使用 Reselect 的`createSelector()`函数重新创建的先前选择器函数的记忆版本:

这里，`getSelectedItems`选择器由两个选择器组成，即`getItems`和`getSelected`，使用 Reselect 的`createSelector()`功能。像这样的组合使得构建专门的选择器成为可能，这些选择器从状态中计算不同形式的派生数据。

```
import { createSelector } from 'reselect';

const getItems = (state) => state.items;
const getSelected = (state) => state.selected;

const getSelectedItems = createSelector(
  [getItems, getSelected],
  (items, selected) => items.filter(item => selected.includes(item.id))
);
```

例如，可以从`getSelectedItems`选择器中创建一个新的选择器，它返回所选商品的应付总额减去折扣。这将是它的样子:

这演示了选择器可以多么容易地由其他选择器组成，从而提高代码重用。

```
const getSelectedItemsAmount = createSelector(
  [getSelectedItems],
  (items) =>
    items.reduce((total, item) => {
      return total + Number(item.price) - ( Number(item.discount) || 0 )
    }, 0).toFixed(2)
);
```

然后，这些选择器可以用于使用`react-redux`将 React 组件连接到 Redux 状态，如下所示:

使用选择器属性改进代码重用

```
import React from 'react';
import { connect } from 'react-redux';
import { getSelectedItems, getSelectedItemsAmount } from './selectors';

function SelectedItemsList(props) {
  return (
    <React.Fragment>
      <ul>
        { props.selectedItems.map(item => (
          <li key={item.id}>

              **{item.name}**
              { item.price } (Over { Math.floor(item.discount / item.price * 100) }% Savings)

          </li>
        )) }
      </ul>

        **Overall Amount:**
        { props.selectedItemsAmount }

    </React.Fragment>
  )
}

const mapStateToProps = (state) => ({
  selectedItems: getSelectedItems(state),
  selectedItemsAmount: getSelectedItemsAmount(state)
});

export default connect(mapStateToProps)(SelectedItemsList);
```

### 为了进一步提高代码重用，Reselect 的选择器函数可以接受第二个`props`参数，该参数映射到连接组件的 props。因此，几个组件实例可以使用相同的选择器函数，但使用不同的属性，动态地从商店的状态中获取数据。

假设我们想根据组件的属性，用另一种货币重新计算商品价格。我们所要做的就是根据作为第二个参数接收的`props`中指定的货币来修改`getItems`选择器上商品的价格。

以下示例演示了这种情况:

有趣的是，从`getItems`选择器派生的所有其他选择器也会根据需要更新它们的派生数据。

```
// BEFORE (Without Price Conversion)
const getItems = (state) => state.items;

// AFTER (With Price Conversion)
const getItems = (state, props = {}) => {
  const currency = `${props.currency}`.toUpperCase();
  const RATES = {
    GBP: 0.78,
    EUR: 0.88,
    YEN: 6.94
  };

  // Usually the rate will be gotten from an external service
  const rate = Object.keys(RATES).includes(currency)
    ? RATES[currency]
    : 1.00;

  return state.items
    .map(({ price, discount, ...item }) => ({
      ...item,
      price: (price * rate).toFixed(2),
      discount: (discount * rate).toFixed(2)
    }));
}
```

重选:改进了选择器缓存和记忆

### 基于 props 构建选择器函数会导致对最终选择器的优化进行一些权衡。

这是因为`**reselect**`为每个被调用的选择器保留一个限制为`**1**`条目的缓存。因此，当用不同的属性调用选择器时，缓存就会失效。

解决这个问题的一种方法是利用 [**重选**](https://github.com/toomuchdesign/re-reselect) 包来创建具有更深记忆和扩展缓存的选择器。

这个包可以确保在调用选择器函数时使用缓存的选择器，而不是新的选择器。它之所以能够做到这一点，是因为它使用某种形式的*缓存键*来确定一个选择器之前是否被缓存过。

使用`**re-reselect**`包，可以使用`createCachedSelector`默认导出函数而不是`**reselect**`中的`createSelector`函数来组成派生的选择器。

更多来自 LogRocket 的精彩文章:

* * *

### 然而，`createCachedSelector`函数返回一个以`resolveFunction`为参数的函数。

* * *

这个`resolveFunction`是用与结果选择器相同的参数定义的，并且必须返回一个字符串，表示用于缓存结果选择器的缓存键。

下面是我们之前的选择器使用`createCachedSelector`函数后的样子:

Redux 更好的副作用管理和可测试性。

```
import createCachedSelector from 're-reselect';

const resolveCurrencyCacheKey = (state, props = {}) => {
  let { currency } = props;
  const CURRENCIES = ['USD', 'GBP', 'EUR', 'YEN'];

  currency = (currency && typeof currency === 'string')
    ? currency.toUpperCase()
    : 'USD';

  return `currency:${ CURRENCIES.includes(currency) ? currency : 'USD' }`;
}

const getSelectedItems = createCachedSelector(
  [getItems, getSelected],
  (items, selected) => items.filter(item => selected.includes(item.id))
)(resolveCurrencyCacheKey);

const getSelectedItemsAmount = createCachedSelector(
  [getSelectedItems],
  (items) =>
    items.reduce((total, item) => {
      return total + Number(item.price) - ( Number(item.discount) || 0 )
    }, 0).toFixed(2)
)(resolveCurrencyCacheKey);
```

> Redux 作为一个状态管理器，在处理跨应用程序的同步动作方面做得很好。然而，大多数应用程序需要涉及许多不同复杂程度的异步操作，比如 DOM 事件、AJAX 请求等。这些异步动作可以被称为*副作用*。

这就是 [**Redux-Saga**](https://redux-saga.js.org) 派上用场的地方。Redux-Saga 使得以一种可预测的方式轻松、高效地处理应用程序副作用成为可能。Redux-Saga 是一个 Redux *中间件*，因此它可以完全访问 Redux 应用程序状态，也可以调度 Redux 动作。

Redux-Saga 使用*Saga*来处理副作用。一个*传奇*就像是应用程序中的一个独立线程，专门负责处理副作用。Redux-Saga 依赖 ES6 生成器来控制异步流。因此，通过实现，sagas 应该是生成器函数。

如果你已经习惯于使用 [**redux-thunk**](https://github.com/reduxjs/redux-thunk) 中间件包来处理异步动作，那么你会立即注意到使用 Redux-Saga 的好处。

`**redux-thunk**`依赖于动作创建者和懒惰调度，`**redux-saga**`依赖于*效果*和*传奇*，使得代码可维护、易测试、易实现延迟执行、并行执行、竞争执行等执行流程。

设置中间件

### 首先，您必须在 Redux 应用商店上设置并应用`redux-saga`中间件。以下代码片段显示了设置:

这里，我们为一个虚拟照片应用程序设置了一个简单的 Redux store 和一些操作。我们还通过使用`redux-saga`包创建的 saga 中间件增强了商店。

```
import createSagaMiddleware from 'redux-saga';
import { createStore, applyMiddleware } from 'redux';
import rootSaga from './sagas';

// Create Saga middleware
// and apply it as a store enhancement
const sagaMiddleware = createSagaMiddleware();
const createStoreWithSaga = applyMiddleware(sagaMiddleware)(createStore);

// Initial store state
const INITIAL_STATE = {
  photo: null,
  error: null,
  fetching: false
};

// Reducer for the store
const reducer = (state, action) => {
  switch (action.type) {
    case 'PHOTO_FETCH_REQUESTED':
      return { ...state, fetching: true };
    case 'PHOTO_FETCH_SUCCESSFUL':
      return { ...state, fetching: false, error: null, photo: action.photo };
    case 'PHOTO_FETCH_FAILED':
      return { ...state, fetching: false, error: action.error };
    default:
      return state;
  }
};

// Create the store with Saga enhancement
const store = createStoreWithSaga(reducer, INITIAL_STATE);

// Run the root saga through the middleware
sagaMiddleware.run(rootSaga);

export default store;
```

最后，我们通过 saga 中间件运行一个导出为`rootSaga`的 saga。目前，我们还没有定义这个传奇，所以我们将继续创建它。

创造传奇

### 如前所述，传奇是生成器函数。生成器函数是 ES6 的主要新增功能之一，在处理异步执行流时非常有用，因为它们能够暂停和恢复代码执行。

在继续之前，您可能有兴趣了解一些关于[发生器函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)的知识。以下代码片段显示了一个简单的生成器函数:

下面是包含根传奇的`sagas.js`文件的样子:

```
function* infiniteGenerator () {
  for (let i = 1; true; i++) {
    yield i;
  }
}
```

在这段代码中，我们首先从`redux-saga`包中导入一些名为*效果*的特殊函数。接下来，我们创建两个助手函数:一个测试普通的 JavaScript 对象，另一个从 [**Picsum API**](https://picsum.photos/) 获取照片。

```
import { call, put, takeLatest, fork } from 'redux-saga/effects';

// Simple helper to test for plain JavaScript objects
const _isPlainObject = value => {
  return Object.prototype.toString.call(value) === '[object Object]';
}

// Fetch a photo by ID from the Picsum API
const fetchPhoto = photoId => {
  return fetch(`https://picsum.photos/list`)
    .then(response => response.json())
    .then(photos => photos.find(photo => photo.id == photoId));
}

// Worker Saga for handling async photo fetching
function* photoFetchWorkerSaga (action) {
  try {
    const photo = yield call(fetchPhoto, action.payload.id);
    if (_isPlainObject(photo)) {
      yield put({ type: 'PHOTO_FETCH_SUCCESSFUL', photo });
    } else {
      yield put({ type: 'PHOTO_FETCH_FAILED', error: 'Photo not found.' });
    }
  } catch (e) {
    yield put({ type: 'PHOTO_FETCH_FAILED', error: e.message });
  }
}

// Saga that looks for latest photo fetch request
// and triggers the worker
export default function* rootSaga() {
  yield takeLatest('PHOTO_FETCH_REQUESTED', photoFetchWorkerSaga);
}
```

最后，我们用`redux-saga`的特效创作了我们的传奇。当被触发时，`photoFetchWorkerSaga`根据动作负载从 Picsum API 获取一张照片。

如果获取成功，它将调度`PHOTO_FETCH_SUCCESSFUL`动作。否则，它将调度`PHOTO_FETCH_FAILED`动作。

在`rootSaga`中，我们观察每一个`PHOTO_FETCH_REQUESTED`动作，并使用`takeLatest`效果触发照片获取工人传奇。

但是，`takeLatest`效果只返回最后一次调用的结果，忽略其余的。如果你对每次调用的结果感兴趣，那么你应该使用`takeEvery`效应。

还原传奇效应

### 下面是由`**redux-saga**`包提供的一些效果的简要列表:

**调用** —运行传递指定参数的函数。如果该函数返回一个承诺，它将暂停该传奇，直到该承诺被解决或拒绝

*   **put** —分派一个 Redux 操作
*   **fork** —以非阻塞方式运行传递的函数
*   **take** —暂停 saga，直到收到指定的 Redux 动作
*   **takeEvery** —返回为指定的 Redux 操作触发的每个调用的结果
*   **takeLatest** —仅返回为指定 Redux 操作触发的最后一次调用的结果，忽略其余的调用。这种效果可以用来实现某种形式的动作取消
*   **race** —同时运行多个效果，并在一个效果完成后终止所有效果
*   使用 RxJS 对 Redux 进行强大的副作用处理。

> 虽然 Redux-Saga 在管理副作用和简化可测试性方面做得很好，但还是值得考虑一下 [**Redux-Observable**](https://redux-observable.js.org) 包。Redux-Observable 允许您在处理 Redux 应用程序中的副作用时，获得使用 [**RxJS**](http://reactivex.io/rxjs) 带来的所有反应式编程的美妙之处。

可重复观测的史诗

### 要使用`**redux-observable**`，您还需要安装`**rxjs**`作为应用程序的依赖项，这意味着您需要了解 RxJS 可观测量。Redux-Observable 使用*史诗*以非常强大的方式处理副作用。

一个*史诗*是一个简单的函数，它接受一个动作流并返回另一个动作流。Redux-Observable 自动订阅引擎盖下每个注册的 epic，作为观察者传递 Redux store `dispatch`方法，大概是这样的:

顺便说一下，这是一部史诗的签名:

```
epic(action$, state$).subscribe(store.dispatch)
```

在 epic 内部，您可以使用 RxJS 提供的任何可观察模式，只要您始终确保 epic 返回的最终流是一个动作。这是一部非常简单的史诗:

```
function (
  action$: Observable<Action>,
  state$: StateObservable<State>
): Observable<Action>;
```

这个 epic 监听每个`'PING'`动作，并将它们映射到一个新的`'PONG'`动作。每当 Redux store 调度一个`'PING'`动作时，它就会导致一个`'PONG'`动作也被调度。

```
import { mapTo } from 'rxjs/operators';
import { ofType } from 'redux-observable';

const pingEpic = action$ => {
  return action$.pipe(
    ofType('PING'),
    mapTo({ type: 'PONG' })
  );
}
```

中间件设置

### 就像 Redux-Saga 一样，需要一个中间件设置来使 Redux-Observable 监听 Redux 存储上的动作。下面是显示中间件设置的代码片段:

这里，我们创建了一个中间件设置和存储配置，与我们之前为 Redux-Saga 示例创建的非常相似。

```
import { createStore, applyMiddleware } from 'redux';
import { createEpicMiddleware } from 'redux-observable';
import rootEpic from './epics';

// Create Epic middleware
// and apply it as a store enhancement
const epicMiddleware = createEpicMiddleware();
const createStoreWithEpic = applyMiddleware(epicMiddleware)(createStore);

// Initial store state
const INITIAL_STATE = {
  photo: null,
  error: null,
  fetching: false
};

// Reducer for the store
const reducer = (state, action) => {
  switch (action.type) {
    case 'PHOTO_FETCH_REQUESTED':
      return { ...state, fetching: true };
    case 'PHOTO_FETCH_SUCCESSFUL':
      return { ...state, fetching: false, error: null, photo: action.photo };
    case 'PHOTO_FETCH_FAILED':
      return { ...state, fetching: false, error: action.error };
    default:
      return state;
  }
};

// Create the store with Epic enhancement
const store = createStoreWithEpic(reducer, INITIAL_STATE);

// Run the root epic through the middleware
epicMiddleware.run(rootEpic);

export default store;
```

但是，请注意，Redux-Observable epic 中间件只希望运行一个根 epic。因此，应用程序的所有 epic 都需要合并到一个根 epic 中，就像 Redux reducers 一样。

创造史诗

### 正如我们之前看到的，epic 是一个常规函数，它可以接受一个`**action$**`流和可选的`**state$**`流作为参数，并返回另一个动作流。

在 epic 函数内部，可以使用 RxJS 提供的任何有效的可观察模式，这就是真正的力量所在。

下面的代码片段展示了我们之前的照片获取应用程序的一个 epic:

虽然看起来我们使用 Redux-Observable 编写的代码比使用 Redux-Saga 编写的代码多，但是真正的优势来自于链接 RxJS 操作符。

```
import { of } from 'rxjs';
import { ajax } from 'rxjs/ajax';
import { combineEpics, ofType } from 'redux-observable';
import { map, mergeMap, catchError } from 'rxjs/operators';

// Simple helper to test for plain JavaScript objects
const _isPlainObject = value => {
  return Object.prototype.toString.call(value) === '[object Object]';
};

const photoFetchEpic = (action$, state$) => {
  return action$.pipe(
    // Listen for only 'PHOTO_FETCH_REQUESTED' actions
    ofType('PHOTO_FETCH_REQUESTED'),

    // Map action to emit AJAX request
    mergeMap(action =>
      ajax.getJSON('https://picsum.photos/list').pipe(
        map(photos => photos.find(({ id }) => id === action.payload.photo_id)),
        map(photo =>
          _isPlainObject(photo)
            ? { type: 'PHOTO_FETCH_SUCCESSFUL', photo }
            : { type: 'PHOTO_FETCH_FAILED', error: 'Photo not found.' }
        ),
        // handle AJAX error
        catchError(err => of({ type: 'PHOTO_FETCH_FAILED', error: err.message }))
      )
    )
  );
};

// Create root epic by combining all other epics
const rootEpic = combineEpics(photoFetchEpic);

export default rootEpic;
```

例如，假设我们想要修改照片获取操作，如下所示:

短时间内的去抖请求

*   终止已取消提取的网络请求
*   如果失败，最多重试 3 次照片获取请求
*   最多获取 10 张照片，忽略后续请求
*   我们所要做的就是简单地将一些 RxJS 操作符链接到我们已经存在的 epic 上，这样我们就很好了。这看起来是这样的:

根据模式规范化嵌套的 JSON。

```
import { of } from 'rxjs';
import { ajax } from 'rxjs/ajax';
import { combineEpics, ofType } from 'redux-observable';
import { map, take, retry, debounceTime, switchMap, catchError } from 'rxjs/operators';

const photoFetchEpic = (action$, state$) => {
  return action$.pipe(
    // Listen for only 'PHOTO_FETCH_REQUESTED' actions
    ofType('PHOTO_FETCH_REQUESTED'),

    // Debounce requests within a 500ms time frame
    debounceTime(500),

    // Use switchMap instead of mergeMap to ensure that
    // in-flight AJAX requests can be terminated
    switchMap(action =>
      ajax.getJSON('https://picsum.photos/list').pipe(
        map(photos => photos.find(({ id }) => id === action.payload.photo_id)),

        map(photo =>
          _isPlainObject(photo)
            ? { type: 'PHOTO_FETCH_SUCCESSFUL', photo }
            : { type: 'PHOTO_FETCH_FAILED', error: 'Photo not found.' }
        ),

        // Retry the request a maximum of 3 times on failure
        retry(3),

        catchError(err => of({ type: 'PHOTO_FETCH_FAILED', error: err.message }))
      )
    ),

    // Take only the first 10 photos fetched successfully
    // and ignore subsequent requests
    take(10)
  );
};
```

> 应用程序 Redux 存储中的大量数据通常来自于在应用程序的某个时候向某个 API 发出 AJAX 请求。

这些 API 大多返回深度嵌套对象的 JSON 数据，对于我们的 Redux 应用程序来说，使用这种结构的数据通常非常困难。这就是 [**规格化**](https://github.com/paularmstrong/normalizr) 发挥作用的地方。

Normalizr 是一个非常轻量级且功能强大的库，用于获取带有模式定义的 JSON，并返回包含 id 的嵌套实体，这些 id 收集在字典中。

一个简单的例子将快速展示 Normalizr 对于根据模式规范化嵌套的 JSON 数据是多么有用。但是首先，让我们尝试创建一个规范化模式。

假设我们有一个新闻提要的 JSON 数据，如下所示:

我们可以使用 Normalizr 为这个 JSON 数据定义模式，如下所示:

```
{
  "id": 123456,
  "title": "Robots serve humans food in a Nigerian restaurant",
  "body": "...",
  "comments": [
    { "id": 123, "comment": "Interesting" },
    { "id": 124, "comment": "Could this be real..." }
  ],
  "relatedFeeds": [
    {
      "id": 123457,
      "title": "A tech company suffers losses from software hack"
    },
    {
      "id": 123458,
      "title": "Automobile giant acquired by German motor company"
    }
  ]
}
```

创建模式后，我们可以使用它来规范化 JSON 数据，如下所示:

```
import { schema } from 'normalizr';

const comment = new schema.Entity('comments');
const relatedFeed = new schema.Entity('relatedFeeds');

const feed = new schema.Entity('feeds', {
  comments: [comment],
  relatedFeeds: [relatedFeed]
});
```

以下是标准化数据的样子:

```
import { schema, normalize } from 'normalizr';

const JSON_DATA = {
  // ... the JSON data here
};

const normalizedData = normalize(JSON_DATA, feed);
```

结论

```
{
  "result": 123456,
  "entities": {
    "comments": {
      "123": {
        "id": 123,
        "comment": "Interesting"
      },
      "124": {
        "id": 124,
        "comment": "Could this be real..."
      }
    },
    "relatedFeeds": {
      "123457": {
        "id": 123457,
        "title": "A tech company suffers losses from software hack"
      },
      "123458": {
        "id": 123458,
        "title": "Automobile giant acquired by German motor company"
      }
    },
    "feeds": {
      "123456": {
        "id": 123456,
        "title": "Robots serve humans food in a Nigerian restaurant",
        "body": "...",
        "comments": [
          123,
          124
        ],
        "relatedFeeds": [
          123457,
          123458
        ]
      }
    }
  }
}
```

### 在本教程中，我们已经能够探索 Redux 中常用的 5 个库，以提高代码的可重用性并构建强大的应用程序。

鼓掌并跟随

#### 如果你觉得这篇文章很有见地，如果你不介意的话，请随意鼓掌。

你也可以在 Medium ( [Glad Chinda](https://medium.com/u/ddcd0e9719e5) )上关注我，获取更多对你有帮助的有见地的文章。你也可以在推特上关注我( [@gladchinda](https://twitter.com/@gladchinda) )。

***享受编码……***

使用 LogRocket 消除传统反应错误报告的噪音

## 是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

Focus on the React bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/react-signup-issue-free).