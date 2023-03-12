# Redux Toolkit 的新监听器中间件 vs. Redux-Saga - LogRocket 博客

> 原文：<https://blog.logrocket.com/redux-toolkits-new-listener-middleware-vs-redux-saga/>

***编者按:**这篇文章于 2022 年 6 月 23 日更新，以反映 Redux Thunk 的更准确信息，这是 Redux 维护者[马克·埃里克森](https://blog.isquaredsoftware.com/)在评论部分引起我们注意的。*

## 介绍

Redux 是 JavaScript 中最受欢迎的状态管理库之一，尤其是在 React 开发人员中。它使得管理复杂的 UI 状态变得更加容易。Redux reducers 是 Redux 的主要构建模块，按照设计，它是纯函数。您可以通过使用普通 Redux 调度简单的同步操作来启动状态更新。

然而，现实世界的应用程序需要执行的不仅仅是简单的动作调度。您需要使用中间件库(如 [Redux Thunk](https://blog.logrocket.com/using-redux-toolkits-createasyncthunk/) 、Redux-Saga 和最近发布的监听器中间件)来增强 Redux 的能力，以管理副作用和更复杂的同步和异步流程。

由于 [Redux Toolkit](https://blog.logrocket.com/smarter-redux-redux-toolkit/) (RTK)成为了编写现代 Redux 代码的事实上的工具集，准确的说 Redux Thunk 也成为了默认的中间件，因为它默认是 RTK 的一部分。但是，如果默认的中间件库不符合您的用例，您可以使用不同的中间件库。

尽管简单，但思维也有局限性。最常见的限制之一是无法运行代码来响应调度的操作或状态更新。这样做需要编写定制的中间件或者使用像 Redux-Saga 这样更强大的中间件库。Thunks 也相对难以测试。

创建新的侦听器中间件的原因就是为了填补这一空白。在本文中，我将比较新的监听器中间件和 Redux-Saga，并强调一些交叉特性。在此之前，让我在接下来的小节中介绍一下监听器中间件和 Redux-Saga。

### 内容

## Redux Toolkit 新监听器中间件简介

如上所述，Redux 维护者讨论了新的监听器中间件功能，以增强 RTK 的能力，并为 Sagas 涵盖的大多数用例提供内部解决方案。经过无尽的迭代终于登陆了 RTK v1.8.0。中间件的主要功能是让用户响应分派的动作，或者运行代码来响应状态更新。

根据维护者的说法，监听器中间件在概念上类似于 React 的`useEffect`钩子。`useEffect`钩子用于运行 React 功能组件中的副作用。当它的一个依赖关系发生变化时，它会在组件挂载和后续重新渲染时立即运行。

类似地，您可以绝对控制侦听器何时运行。您可以注册一个侦听器，以便在调度某些操作时、在每次状态更新时或者在满足某些条件后运行。要使用监听器中间件，像任何其他 RTK 功能一样导入`createListenerMiddleware`函数。它在 RTK v1.8.0 或更高版本中可用。

```
import { configureStore, createListenerMiddleware } from "@reduxjs/toolkit";
const listenerMiddleware = createListenerMiddleware();

```

您可以在设置期间静态地向中间件添加侦听器，或者在运行时动态地添加和删除它们。要在设置时静态添加它，您需要调用中间件实例的`startListening`方法，如下所示:

```
listenerMiddleware.startListening({
  actionCreator: addTodo,
  effect: async (action, listenerApi) => {
    console.log(listenerApi.getOriginalState());
    console.log(action);
    await listenerApi.delay(5000);
    console.log(listenerApi.getState());
  },
});

```

在上面的例子中，`effect`回调将在分派指定的动作后运行。默认情况下，`effect`回调采用两个参数:调度的动作和监听器 API 对象。监听器 API 对象具有诸如`dispatch`和`getState`的函数，用于与 Redux 存储交互。

在监听器的`effect`回调中，您可以执行副作用、取消正在运行的监听器实例、生成子进程、分派动作以及访问状态。

如果想在运行时动态地删除或添加侦听器，可以调度标准的内置操作。注册侦听器回调时，可以使用以下属性之一来指定它何时严格运行:

*   动作类型:`type`属性是触发`effect`回调的确切动作类型字符串
*   动作创建者:`actionCreator`属性正是触发`effect`回调的动作创建者
*   matcher:`matcher`属性使用 RTK matcher 匹配许多动作中的一个，并在匹配时触发`effect`回调
*   谓词:`predicate`属性是一个函数，它返回`true`或`false`来决定`effect`回调是否应该运行。它可以访问已调度的操作以及当前和以前的状态

上面概述的属性属于在向中间件添加监听器时传递给`startListening`函数的对象。

使用 RTK，您可以像添加任何其他中间件一样添加侦听器中间件:

```
export const store = configureStore({
  reducer: rootReducer,
  middleware: (getDefaultMiddleWare) => {
    return getDefaultMiddleWare({ thunk: false }).prepend(listenerMiddleware);
  }
});

```

## 什么是 Redux-Saga？

Redux-Saga 是 Redux 的流行中间件库之一。这使得处理异步操作和副作用变得容易多了。与 Redux Thunk 等其他库不同，Redux-Saga 使用 [ES6 生成器](https://blog.logrocket.com/javascript-iterators-and-generators-a-complete-guide/)。因此，您应该了解 ES6 生成器，以便正确地实现 Sagas。

### 发电机功能

您可以使用`function*`构造来声明一个生成器函数。下面是一个生成器函数的基本例子。如果您觉得不熟悉，请在继续之前，按照开头段落中的链接理解迭代器和生成器:

```
function* countToThree() {
  yield 1;
  yield 2;
  yield 3;
}

const counter = countToThree();

console.log(counter.next()); // {value: 1, done: false}
console.log(counter.next()); // {value: 2, done: false}
console.log(counter.next()); // {value: 3, done: false}

```

调用生成器函数不像常规函数那样执行函数体。相反，它使用执行函数体的`next`方法返回一个迭代器对象。

调用`next`方法将执行函数体，直到遇到第一个`yield`关键字。它暂停执行并返回一个具有属性`value`和`done`的对象。`value`属性保存产生的值，而`done`是一个布尔值，指定是否所有的值都已产生。

再次调用`next`将恢复功能执行，直到遇到下一个`yield`。它再次暂停执行，并像以前一样返回一个具有属性`value`和`done`的对象。这个过程随着你继续调用`next`而继续。

### 理解传奇

一个典型的 Redux-Saga 中间件设置有观察者传奇和工作者传奇。监视者传奇是监视分派动作的生成器函数。工人传奇是你从守望者传奇中获得的生成器功能，通常负责产生副作用。

下面的代码简单说明了如何实现观察者和工作者传奇:

```
const fetchTodo = (url) => fetch(url).then((res) => res.json());

function* workerSaga(action) {
  const { url } = action.payload;
  try {
    const todo = yield call(fetchTodo, url);
    yield put(addTodo(todo));
  } catch (error) {
    yield put(setError({ error }));
  }
};

function* watcherSaga() {
  yield takeEvery(fetchTodo.toString(), workerSaga);
};

```

函数`call`、`put`和`takeEvery`是辅助效果，是 Redux-Saga API 的一部分。查看文档以了解更多关于它们如何工作的信息。

`watcher``Saga`发生器功能为指定动作的每一个`dispatch`运行。在 worker Saga 中，您可以运行副作用、访问状态、分派操作以及取消正在运行的流程。

如果您将 Redux-Saga 与 RTK 一起使用，您可以像任何其他中间件一样将其添加到中间件列表中:

```
const SagaMiddleware = createSagaMiddleware();

export const store = configureStore({
  reducer: rootReducer,
  middleware: (getDefaultMiddleware) => {
    return getDefaultMiddleware({ thunk: false }).prepend(SagaMiddleware);
  },
});

SagaMiddleware.run(rootSaga);

```

## 新监听器中间件与 Redux-Saga 的比较

前几节向您介绍了监听器中间件和 Redux-Saga。正如所指出的，监听器中间件涵盖了大多数主要的 Redux-Saga 用例。在这一节中，我们将比较监听器中间件和 Redux-Saga 中的一些功能。

在我们开始之前，值得一提的是监听器中间件的`effect`回调是在调用根 reducer 并更新状态之后运行的。因此，如果您的目标是从`effects`回调中严格更新状态，那么调度一个将触发`effect`而不更新状态的动作。之后，您可以运行一些副作用逻辑，并从`effect`回调中调度另一个动作来更新状态。

## 延迟效果执行

使用新的监听器中间件，可以暂停或延迟`effect`回调的执行。`delay`函数将`effect`回调中的代码执行延迟一段时间，之后继续执行。它将毫秒数作为参数，并返回一个在指定的毫秒数后解析的承诺。

`delay`函数是监听器 API 对象的一部分。您可以在监听器中间件中这样使用它:

```
listenerMiddleware.startListening({
  actionCreator: fetchTodo,
  effect: async (action, listenerApi) => {
    const { todoId } = action.payload;
    const todo = await api.fetchTodo(todoId);
    await listenerApi.delay(500);
    listenerApi.dispatch(addTodo(todo));
  },
});

```

Redux-Saga 还有一个`delay`函数，类似于监听器中间件的`delay`函数。它将毫秒数作为参数，并延迟指定的持续时间。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

以下是 Redux-Saga 中上述功能的等效实现:

```
function* fetchTodo(action){
  const { todoId } = action.payload;
  const todo = yield api.fetchTodo(todoId);
  yield delay(500);
  yield put(addTodo(todo));
}

```

## 去抖动

监听器中间件没有像 Redux-Saga 那样的内置去抖功能。然而，您可以使用诸如`cancelActiveListeners`和`delay`这样的函数来实现类似的功能。它们是监听器 API 对象的一部分。

调用`cancelActiveListeners`将取消除调用它的实例之外的所有其他正在运行的侦听器实例。然后，您可以将执行延迟一段特定的时间。当延迟期间没有任何相关的动作分派或状态更新时，最新的监听器实例将运行完成:

```
listenerMiddleware.startListening({
  actionCreator: fetchTodo,
  effect: async (action, listenerApi) => {
    listenerApi.cancelActiveListeners();
    await listenerApi.delay(500);
  },
});

```

上面的监听器中间件实现类似于 Redux-Saga 中内置的`debounce`函数:

```
function* watcherSaga() {
  yield debounce(500, fetchTodo.toString(), workerSaga);
}

```

## 节流

像去抖动一样，监听器中间件没有像 Redux-Saga 那样的内置节流功能。然而，您可以使用监听器 API 对象的`subscribe`、`delay`和`unsubscribe`函数来实现类似的功能。取消订阅某个侦听器会将其从侦听器列表中删除。

然后，您可以使用`delay`函数将执行延迟一段特定的时间。在延迟期间，中间件将忽略所有应该触发`effect`回调的动作分派或状态更新。此后，您可以重新订阅监听器:

```
listener.startListening({
  type: fetchTodo.toString(),
  effect: async (action, listenerApi) => {
    listenerApi.unsubscribe();
    console.log('Original state ', listenerApi.getOriginalState());
    await listenerApi.delay(1000);
    console.log('Current state ', listenerApi.getState());
    listenerApi.subscribe();
  }
});

```

你需要同步调用`getOriginalState`，否则它会抛出一个错误。

监听器中间件中的上述实现类似于 Redux-Saga 中的内置`throttle`函数:

```
function* watcherSaga() {  
  yield throttle(1000, fetchTodo.toString(), workerSaga)  
}

```

## 观看每一个行动调度

在对新监听器中间件的介绍一节中，我提到过您可以指定何时以四种方式之一触发`effect`回调。您可以使用传递给`startListening`函数的对象的动作`type`、`actionCreator`、`matcher`或`predicate`属性。

谓词是一个函数，它可以访问被调度的动作、以前的和当前的状态。如果谓词返回`true`，那么`effect`回调就会运行。因此，如果它总是返回`true`，如下例所示，`effect`回调会在每次动作分派或状态更新时运行:

```
listenerMiddleware.startListening({
  predicate: (action, currState, prevState) => true,
  effect: async (action, listenerApi) => {
    console.log('Previous state ', listenerApi.getOriginalState());
    console.log('Current state ', listenerApi.getstate());
  },
});

```

监听器中间件中的上述功能类似于 Redux-Saga 的带有`*`通配符的`takeEvery`助手效果。使用带有`*`的`takeEvery`来监视每个进来的动作分派，不管它是什么类型，然后产生一个新的子任务。不同之处在于监听器中间件在状态更新后运行其`effect`回调:

```
function* watchEveyDispatchAndLog(){
  yield takeEvery('*', logger);
}

```

## 创建一次性监听器

如果您想用新的监听器中间件创建一个一次性监听器，您可以在运行一些代码后使用`unsubscribe`函数将监听器从中间件中移除。因此，同一动作的未来调度不会触发`effect`回调:

```
listenerMiddleware.startListening({ 
  actionCreator: fetchTodo,
  effect: async (action, listenerApi) => {
    console.log(action);
    listenerApi.unsubscribe();
  },
});

```

但是，请注意，`unsubscribe`函数不会取消已经运行的`effect`回调实例。在取消订阅之前，您可以使用`cancelActiveListeners`功能取消正在运行的实例。

以上功能相当于在 Redux-Saga 中使用`take`辅助效果来指定观看哪个动作调度:

```
function* watchIncrementVisitCount(){
    yield take(incrementVisitCount());
    yield api.incrementVisitCount();
}

```

以上佐贺将严格采取指定动作的第一次调度，之后停止观看。虽然上面的例子只取了第一个 dispatch，但是您可以修改它来查看任意多的 dispatch。

## 启动子任务

可以使用监听器 API 的`fork`函数在监听器回调中启动子任务。函数`fork`将异步或同步函数作为参数。您可以使用它在`effect`回调中执行额外的任务:

```
listenerMiddleware.startListening({
  actionCreator: fetchTodo,
  effect: async (action, listenerApi) => {
    const task = listenerApi.fork(async (forkApi) => {
    });
    const result = await task.result;
  },
});

```

上面的监听器中间件功能类似于在 Redux-Saga 中使用`fork`或`spawn`助手效果运行额外的任务。`fork`效果创建附属任务，而`spawn`创建分离任务；

```
function* fetchTodos() {
  const todo1 = yield fork(fetchTodo, '1');
  const todo2 = yield fork(fetchTodo, '2');
}

```

## 取消正在运行的监听程序实例

对于同一个侦听器的多个运行实例，侦听器中间件提供了`cancelActiveListeners`实用函数，用于取消`effect`回调中的其他实例。因此，回调将针对最新的派单运行:

```
listenerMiddleware.startListening({
  actionCreator: fetchTodo,
  effect: async (action, listenerMiddlewareApi) => {
    listenerMiddlewareApi.cancelActiveListeners();
     },
});

```

监听器中间件的上述功能类似于 Redux-Saga 的`takeLatest`效果创建器。如果先前启动的 Saga 任务仍在运行，效果创建器还会取消这些任务，转而使用最新的任务:

```
function* watchFetchTodo() {
  yield takeLatest(addTodo.toString(), fetchTodo);
};

```

## 捆绑大小

监听器中间件的包大小大约是 Redux-Saga 的一半。下表显示了从 [bundlephobia](https://bundlephobia.com/) 获得的 Redux-Saga 和监听器中间件的包大小。

包裹

缩小的尺寸

| 缩小+压缩尺寸 | 还原传奇 | 14kB |
| --- | --- | --- |
| 5.3kB | 监听器中间件 | 6.6kB |
| 2.5kB | Redux 工具包(RTK) | 39.3 千字节 |
| 12.7kB | 学习曲线 | 尽管功能强大，但使用 Redux-Saga 最常提到的缺点之一是其陡峭的学习曲线，尤其是如果你不熟悉生成器和传奇的话。 |

## 与 Redux-Saga 不同，新的监听器中间件公开了一个最小的功能集，您可以很快学会。然后，您可以灵活地使用它们来复制一些常见的 Redux-Saga 用例，如前面的小节中所示。

测试

与同时代的 Thunks 相比，使用 Redux-Saga 的一个好处是，Redux-Saga 的生成器功能和内置的辅助效果使得测试一些常见模式变得简单明了。

## 像 Redux-Saga 一样，测试新的侦听器中间件的一些常见模式是很容易的，在文档中有很多关于如何测试一些常见模式的示例。

侦听器中间件与 Redux Thunk:应该使用哪一个，何时使用？

添加了新的监听器中间件后，RTK 现在默认包含 Redux thunk 和新的监听器中间件。你可能会对选择哪个和出于什么目的感到困惑。

## 感谢 RTK 的核心维护者之一 Mark Erikson，他在下面的评论部分强调了您可以使用 RTK Query 获取数据，使用 Thunks 获取需要与存储对话的逻辑，如果您的代码需要对动作或状态变化做出反应，还可以使用监听器。如果 RTK 中的默认功能不符合您的用例，您可以将 Sagas 作为最后的手段。

结论

总的来说，新的侦听器中间件是 Redux-Saga 的一个更简单、更轻量级的替代方案，使用起来很简单。除非你维护一个严重依赖 Redux-Saga 的代码库，否则它是值得探索的。如果不符合你的用例，可以用 Redux-Saga 或者另一个效果很好的中间件。

## 虽然监听器中间件是 RTK 为 Redux-Saga 似乎解决的大多数问题提供内部解决方案所缺少的功能，但这并不意味着它涵盖了所有用例。

另外值得一提的是，本文做了一个基本的比较。我们可能不知道监听器中间件的真正能力或局限性，直到它在生产中被广泛使用，即使许多开发人员已经创造性地使用它来解决问题。

如果我错过了什么，请在下面的评论区告诉我。

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

## 然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

LogRocket automatically aggregates client side errors, JS exceptions, frontend performance metrics, and user interactions. Then LogRocket uses machine learning to tell you which problems are affecting the most users and provides the context you need to fix it.

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).