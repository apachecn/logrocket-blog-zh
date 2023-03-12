# redux-observable - LogRocket 博客初学者指南

> 原文：<https://blog.logrocket.com/a-beginners-guide-to-redux-observable-c0381da8ed3a/>

![](img/fa0c18a6bfca2f1c760e758bd80403db.png)

[Redux-Observable](https://www.npmjs.com/package/redux-observable) 是一个 [Redux 中间件](https://www.codementor.io/vkarpov/beginner-s-guide-to-redux-middleware-du107uyud)，它允许你使用 [RxJS 操作符](https://www.npmjs.com/package/rxjs)过滤和映射动作。像`[filter()](https://www.learnrxjs.io/operators/filtering/filter.html)`和`[map()](https://www.learnrxjs.io/operators/transformation/map.html)`这样的 RxJS 操作符让你转换动作流，就像 [JavaScript 的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) `[Array.prototype.filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)`让你转换数组一样。

在本文中，我将向您展示如何使用可以从 Node.js 运行的脚本开始使用 redux-observable。

### 你的第一部史诗

在 redux-observable 中，一个 [" **史诗** "](https://redux-observable.js.org/docs/basics/Epics.html) 是一个接受动作流并返回修改后的动作流的函数。你可以把 epic 看作是 redux-observable 应该调度的附加动作的描述。史诗的概念类似于 redux-saga 中的[【传奇】。](http://thecodebarbarian.com/redux-saga-vs-async-await.html)

在你写第一部史诗之前，你需要[安装 redux-observable](https://www.npmjs.com/package/redux-observable#install) 。本文假设您已经安装了 Node.js 和 npm。要安装 redux-observable 以及 [redux](https://www.npmjs.com/package/redux) 和 RxJS，运行以下命令:

```
install [email protected] [email protected] [email protected]
```

redux-observable API 中最基本的函数是`[createEpicMiddleware()](https://redux-observable.js.org/docs/api/createEpicMiddleware.html)` [函数](https://redux-observable.js.org/docs/api/createEpicMiddleware.html)。这个函数创建了实际的 Redux 中间件，你应该把它传递给 [Redux 的](https://redux.js.org/api/applymiddleware) `[applyMiddleware()](https://redux.js.org/api/applymiddleware)` [函数](https://redux.js.org/api/applymiddleware)。

下面是一个如何创建中间件的示例，该中间件将“CLICK_INCREMENT”类型的操作转换为“INCREMENT”类型的操作:

```
const { createEpicMiddleware } = require('redux-observable');
const { filter, map } = require('rxjs/operators');
const redux = require('redux');

// An 'epic' takes a single parameter, `action$`, which is an RxJS observable
// that represents the stream of all actions going through Redux
const countEpic = action$ => action$.pipe(
  filter(action => action.type === 'CLICK_INCREMENT'),
  map(action => {
    return { type: 'INCREMENT', amount: 1 };
  })
);

const observableMiddleware = createEpicMiddleware();
const store = redux.createStore(reducer, redux.applyMiddleware(observableMiddleware));

// **Must** add the epic to the observable after calling `applyMiddleware()`.
// Otherwise you'll get a warning: "epicMiddleware.run(rootEpic) called before
// the middleware has been setup by redux. Provide the epicMiddleware instance
// to createStore() first"
observableMiddleware.run(countEpic);

// Sample Redux reducer
function reducer(state = 0, action) {
  console.log('Action', action);

  switch (action.type) {
    case 'INCREMENT':
      return state + action.amount;
    default:
      return state;
  }
}
```

假设您将类型为“CLICK_INCREMENT”的操作分派给上述商店，如下所示:

```
store.dispatch({ type: 'CLICK_INCREMENT' });
```

您的`filter()`和`map()`调用将运行，redux-observable 将分派一个‘INCREMENT’类型的附加动作。

下面是`reducer()`函数中`console.log()`语句的输出:

```
{ type: '@@redux/INIT7.2.m.z.p.l' }
Action { type: 'CLICK_INCREMENT' }
Action { type: 'INCREMENT', amount: 1 }
```

请注意，redux-observable 调度了一个额外的操作。“CLICK_INCREMENT”动作仍然会传递到减速器。默认情况下，史诗将动作添加到流中。

## 我们不只是写 Redux，我们也谈论它。现在听着:

或者以后订阅

### 异步调度

### 上面显示的例子是一个简单的介绍，但是并没有抓住为什么你首先要使用 redux-observable。

redux-observable 之所以如此有趣，是因为它能够使用[RxJS’](https://www.learnrxjs.io/operators/transformation/mergemap.html)`[mergeMap()](https://www.learnrxjs.io/operators/transformation/mergemap.html)`[函数](https://www.learnrxjs.io/operators/transformation/mergemap.html)来处理异步函数。换句话说，redux-observable 是 [redux-saga](http://thecodebarbarian.com/redux-saga-vs-async-await.html) 和 [redux-thunk](http://thecodebarbarian.com/async-await-with-react-and-redux-thunk.html) 的可行替代方案。

下面是一个如何将 redux-observable 与一个简单的[异步函数](http://thecodebarbarian.com/common-async-await-design-patterns-in-node.js.html)一起使用的例子:

`countEpic()`现在将等待大约 1 秒钟，然后分派“增量”动作:

```
const { createEpicMiddleware } = require('redux-observable');
const { filter, mergeMap } = require('rxjs/operators');
const redux = require('redux');

const startTime = Date.now();

const countEpic = action$ => action$.pipe(
  filter(action => action.type === 'CLICK_INCREMENT'),
  // `mergeMap()` supports functions that return promises, as well as observables
  mergeMap(async (action) => {
    await new Promise(resolve => setTimeout(resolve, 1000));
    return { type: 'INCREMENT', amount: 1 };
  })
);

const observableMiddleware = createEpicMiddleware();
const store = redux.createStore(reducer, redux.applyMiddleware(observableMiddleware));

observableMiddleware.run(countEpic);

// Sample Redux reducer
function reducer(state = 0, action) {
  console.log(`+${Date.now() - startTime}ms`, action);

  switch (action.type) {
    case 'INCREMENT':
      return state + action.amount;
    default:
      return state;
  }
}

store.dispatch({ type: 'CLICK_INCREMENT' });
```

如果你读过 [*掌握异步/等待*](http://thecodebarbarian.com/new-ebook-mastering-async-await) ，你就知道这不是支持异步/等待的全部。如果你的异步函数出错了会怎么样？下面的`countEpic()`会崩溃:

```
+1ms { type: '@@redux/INIT7.i.8.v.i.t' }
+7ms { type: 'CLICK_INCREMENT' }
+1012ms { type: 'INCREMENT', amount: 1 }
```

为了处理错误，你应该总是在你的 epic 的末尾加上一个 [RxJS](https://www.learnrxjs.io/operators/error_handling/catch.html) `[catchError()](https://www.learnrxjs.io/operators/error_handling/catch.html)`，如下所示:

```
const countEpic = action$ => action$.pipe(
  filter(action => action.type === 'CLICK_INCREMENT'),
  mergeMap(async () => {
    throw new Error('Oops!');
  })
);
```

`countEpic()`现在将分派一个“错误”类型的动作，并显示错误消息:

```
const { createEpicMiddleware } = require('redux-observable');
const { catchError, filter, mergeMap } = require('rxjs/operators');
const redux = require('redux');

const startTime = Date.now();

const countEpic = action$ => action$.pipe(
  filter(action => action.type === 'CLICK_INCREMENT'),
  mergeMap(async () => {
    throw new Error('Oops!');
  }),
  catchError(err => Promise.resolve({ type: 'Error', message: err.message }))
);
```

发出 HTTP 请求

```
+1ms { type: '@@redux/INIT0.a.g.q.3.o' }
+6ms { type: 'CLICK_INCREMENT' }
+8ms { type: 'Error', message: 'Oops!' }
```

### 上面的例子很简单，但是不太现实。让我们将 redux-observable 用于一个更现实的用例:使用 [node-fetch](https://www.npmjs.com/package/node-fetch) 发出一个 HTTP 请求，从 [IEX API](https://iextrading.com/developer/) 获取当前的 MongoDB 股票价格。要获取股票价格，您需要向以下 URL 发出 get 请求:

因为可以使用 async/await 和`mergeMap()`，所以用 redux-observable 发出 HTTP 请求类似于异步调度示例。 [Node-fetch 返回一个承诺](https://www.npmjs.com/package/node-fetch#usage)，所以您可以`await`一个 HTTP 请求，然后用请求的结果分派一个新的动作。

```
://api.iextrading.com/1.0/stock/MDB/price
```

在下面的代码中，每当‘FETCH _ STOCK _ PRICE’类型的动作通过系统时，`fetchEpic()`向 IEX API 发出一个 GET 请求。如果请求成功，`fetchEpic()`将分派一个“FETCH_STOCK_PRICE_SUCCESS”类型的新操作，股票价格为:

为了将`fetchEpic()`粘合到 Redux，reducer(如下所示)存储了一个映射图`prices`，它将股票符号映射到价格。为了在 Redux 中存储 MongoDB 的股票价格，reducer 监听“FETCH_STOCK_PRICE_SUCCESS”类型的操作，而不是“FETCH_STOCK_PRICE”:

```
const fetch = require('node-fetch');

// ...

const fetchEpic = action$ => action$.pipe(
  filter(action => action.type === 'FETCH_STOCK_PRICE'),
  mergeMap(async (action) => {
    const url = `https://api.iextrading.com/1.0/stock/${action.symbol}/price`;
    const price = await fetch(url).then(res => res.text());
    return Object.assign({}, action, { type: 'FETCH_STOCK_PRICE_SUCCESS', price });
  }),
  catchError(err => Promise.resolve({ type: 'FETCH_STOCK_PRICE_ERROR', message: err.message }))
);
```

下面显示的是通过带有`fetchEpic()`和`reducer()`的 Redux store 运行“FETCH_STOCK_PRICE”操作的示例输出。“FETCH_STOCK_PRICE”操作通过，`fetchEpic()`看到这个操作并发出一个 HTTP 请求。

```
// Sample Redux reducer
function reducer(state = { prices: {} }, action) {
  console.log(`+${Date.now() - startTime}ms`, action);

  switch (action.type) {
    case 'FETCH_STOCK_PRICE_SUCCESS':
      const prices = Object.assign({}, state.prices, { [action.symbol]: action.price });
      state = Object.assign({}, state, { prices });
      console.log('New state', state);
      return state;
    default:
      return state;
  }
}

store.dispatch({ type: 'FETCH_STOCK_PRICE', symbol: 'MDB' });
```

当`fetchEpic()`从 IEX API 得到响应时，它发出一个‘FETCH _ STOCK _ PRICE _ SUCCESS’动作，然后 reducer 更新状态:

结论

```
+1ms { type: '@@redux/INITg.3.m.s.8.f.i' }
+5ms { type: 'FETCH_STOCK_PRICE', symbol: 'MDB' }
+198ms { type: 'FETCH_STOCK_PRICE_SUCCESS',
  symbol: 'MDB',
  price: '79.94' }
New state { prices: { MDB: '79.94' } }
```

### Redux-observable 是一个用 React 和 Redux 处理[异步逻辑的工具。这很重要，因为 React 通常不支持异步函数。Redux-observable 是 redux-saga 和 redux-thunk 的一个有趣的替代品，特别是如果你已经有了 RxJS 的经验。所以下次你发现自己想要](http://thecodebarbarian.com/async-await-with-react-and-redux-thunk.html)[编写自己的承诺中间件](https://www.codementor.io/vkarpov/beginner-s-guide-to-redux-middleware-du107uyud)时，给 redux-observable 一个机会。

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

## 调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).