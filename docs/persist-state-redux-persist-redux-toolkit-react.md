# 在 React 中使用 Redux Toolkit 持久化状态

> 原文：<https://blog.logrocket.com/persist-state-redux-persist-redux-toolkit-react/>

使用 Redux Persist 库，开发人员可以将 Redux 存储保存在持久存储中，例如本地存储。因此，即使在刷新浏览器后，站点状态仍然会被保留。Redux Persist 还包含一些方法，允许我们定制持久化和再水化的状态，所有这些都使用一个易于理解的 API。

在本文中，我们将学习如何在 React 中使用 [Redux Persist](https://github.com/rt2zz/redux-persist) 和 [Redux Toolkit。要阅读本文，您应该熟悉 React 和 Redux 工具包。您还应该在计算机上安装 Node.js。](https://blog.logrocket.com/smarter-redux-redux-toolkit/#bootstrapping-react-redux-toolkit)

## 设置 React

我已经创建了一个使用 Redux Toolkit 进行状态管理的应用程序。我们将在本教程中使用它来了解 Redux Persist 是如何工作的。开始，[c](https://github.com/Tammibriggs/auth-app.git)T2【lone】GitHub 回购。您可以使用以下命令来完成此操作:

```
$ git clone https://github.com/Tammibriggs/auth-app.git

$ cd auth-app

$ npm install

```

接下来，我们可以用`npm start`命令启动应用程序。在我们的应用程序中，我们会看到一个表单，其中有一个字段可以输入我们的姓名和电子邮件。在输入所需的输入并提交表单后，我们将被带到我们的个人资料页面，该页面看起来类似于下图:

![React Profile Page](img/22e9395e390e403be4d032f0f81e88a9.png)

当我们刷新浏览器时，我们的数据将会丢失。让我们学习如何使用 Redux Persist 将状态保存在持久存储中，以便即使在刷新之后，数据仍然保持不变。我们还将了解如何定制持久化的内容，以及如何合并传入的状态。我们开始吧！

## 用 Redux Persist 保持状态

首先，我们将使用以下命令向我们的应用程序添加 Redux Persist:

```
$ npm i redux-persist

```

接下来，我们需要修改我们的存储，我们将在克隆应用程序的`src`目录中的`redux`文件夹中找到它。目前，我们的商店看起来像下面的代码:

```
// src/redux/store.js
import { configureStore } from "@reduxjs/toolkit";
import userReducer from "./slices/userSlice";

export const store = configureStore({
  reducer: userReducer,
  devTools: process.env.NODE_ENV !== 'production',
})

```

我们将对我们的`store.js`文件进行以下修改，以使用 Redux Persist:

```
// src/redux/store.js
import { configureStore } from "@reduxjs/toolkit";
import userReducer from "./slices/userSlice";
import storage from 'redux-persist/lib/storage';
import { persistReducer, persistStore } from 'redux-persist';
import thunk from 'redux-thunk';

const persistConfig = {
  key: 'root',
  storage,
}

const persistedReducer = persistReducer(persistConfig, userReducer)

export const store = configureStore({
  reducer: persistedReducer,
  devTools: process.env.NODE_ENV !== 'production',
  middleware: [thunk]
})

export const persistor = persistStore(store)

```

在上面的代码中，我们将存储中的属性`reducer`的值从`userReducer`替换为`persistedReducer`，这是一个增强的缩减器，配置为将`userReducer`状态持久化到本地存储。除了本地存储，我们还可以使用其他存储引擎[比如](https://github.com/rt2zz/redux-persist#storage-engines)`[sessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)`和 [Redux Persist Cookie 存储适配器](https://github.com/abersager/redux-persist-cookie-storage#redux-persist-cookie-storage-adapter)。

要使用不同的存储引擎，我们只需要用我们想要使用的存储引擎修改`persistConfig`的`storage`属性的值。例如，要使用`sessionStorage`引擎，我们将首先如下导入它:

```
import storageSession from 'reduxjs-toolkit-persist/lib/storage/session'

```

然后，修改`persistConfig`,看起来像下面的代码:

```
const persistConfig = {
  key: 'root',f
  storageSession,
}

```

在上面对存储的修改中，我们还包括了 [Thunk 中间件，它将在不可序列化的值](https://blog.logrocket.com/async-actions-bare-redux-thunk-custom-middleware/#using-thunk-redux-toolkit-manage-asynchronous-actions)到达缩减器之前拦截并停止它们。当在不使用 Thunk 中间件的情况下使用 Redux Persist 时，我们会在浏览器的控制台上看到一个错误消息`a non-serializable value was detected in the state`。

最后，我们将我们的存储作为参数传递给`persistStore`，它是持久化和再水合状态的函数。使用该功能，我们的存储将被保存到本地存储，即使在浏览器刷新后，我们的数据仍将保留。

在大多数用例中，我们可能希望延迟应用程序 UI 的呈现，直到持久数据在 Redux store 中可用。为此，Redux Persist 包含了 [`PersistGate`](https://github.com/ryanwillis/reduxjs-toolkit-persist/blob/main/docs/PersistGate.md) 组件。要使用`PersistGate`，请转到`src`目录中的`index.js`文件，并添加以下导入:

```
// src/index.js
import { persistor, store } from './redux/store';
import { PersistGate } from 'redux-persist/integration/react';

```

现在，修改`render`函数调用，看起来像下面的代码:

```
// src/index.js
root.render(
  <React.StrictMode>
    <Provider store={store}>
      <PersistGate loading={null} persistor={persistor}>
        <App />
      </PersistGate>
    </Provider>
  </React.StrictMode>
);

```

在本节中，我们介绍了使用 Redux Persist 时的基本设置。现在，让我们探索 Redux Persist 的可用选项和用例。

## 使用 Redux Persist 的嵌套持久化

如果我们在 Redux Toolkit 中有两个或更多的 reducers，比如`userReducer`和`notesReducer`，并且我们想将它们添加到我们的存储中，我们可能会如下配置存储:

```
const store = configureStore({
  reducer: {
    user: userReducer,
    notes: notesReducer
  },
})

```

我们也可以如下使用`combineReducers`，它做同样的事情:

```
const rootReducer = combineReducers({ 
  user: userReducer,
  notes: NotesReducer
})

const store = configureStore({
  reducer: rootReducer
})

```

为了在这种情况下使用 Redux Persist，我们将提供`rootReducer`作为`persistReducer`的参数，然后用持久化的 reducer 替换我们商店中的`rootReducer`,如下所示:

```
const rootReducer = combineReducers({ 
  user: userReducer,
  notes: NotesReducer
})

const persistedReducer = persistReducer(persistConfig, rootReducer)

const store = configureStore({
  reducer: persistedReducer
})

```

但是，如果我们想设置一个不同的配置呢？例如，假设我们想将`userReducer`的存储引擎改为`sessionStorage`。为此，我们可以使用[嵌套持久化](https://github.com/rt2zz/redux-persist#nested-persists)，这是一个允许我们嵌套`persistReducer`的特性，让我们能够为减速器设置不同的配置。

下面是一个嵌套持久化的例子，我将`userReducer`的存储改为`sessionStorage`:

```
const rootPersistConfig = {
  key: 'root',
  storage,
}

const userPersistConfig = {
  key: 'user',
  storage: storageSession,
}

const rootReducer = combineReducers({
  user: persistReducer(userPersistConfig, userReducer),
  notes: notesReducer
})

const persistedReducer = persistReducer(rootPersistConfig, rootReducer)

const store = configureStore({
  reducer: persistedReducer
})

```

## 指定如何合并传入状态

合并包括将持久状态保存回 Redux 存储中。当我们的应用程序启动时，我们的初始状态被设置。不久之后，Redux Persist 从存储中检索我们的持久状态，然后覆盖任何初始状态。这个过程是自动进行的。

默认情况下，合并过程会自动合并一个级别。假设我们有一个传入的初始状态，如下所示:

```
{user: {name: 'Tammibriggs'}, isLoggedIn: true} // incoming state
{ user: {name: '', email: ''}, isLoggedIn: false, status: 'Pending'} // initial state

```

合并后的状态将类似于以下代码:

```
{ user: {name: 'Tammibriggs'}, isLoggedIn: true, status: 'Pending'} // reconciled/merged state

```

初始状态与传入状态和顶级属性值合并。在传入状态下，这些被替换而不是合并，这就是为什么`user`中的`email`属性丢失的原因。在我们的代码中，这将类似于以下内容:

```
const mergedState = { ...initialState };

mergedState['user'] = persistedState['user']
mergedState['isLoggedIn'] = persistedState['isLoggedIn']

```

Redux Persist 中的这种合并称为`autoMergeLevel1`，是 Redux Persist 中的[默认状态协调器。其他的状态协调器包括`hardSet`，它用进入的状态完全覆盖初始状态，以及`autoMergeLevel2`，它合并两个深度级别。](https://github.com/rt2zz/redux-persist#state-reconciler)

在我们之前的例子中，`user`中的`email`属性不会丢失。协调或合并的状态将类似于以下代码:

```
{ user: {name: 'Tammibriggs' email:''}, isLoggedIn: true, status: 'Pending'} // reconciled/merged state

```

例如，要设置一个状态协调器，如果我们想使用`autoMergeLevel2`，我们只需要在`persistConfig`中指定一个`stateReconciler`属性:

```
import autoMergeLevel2 from 'redux-persist/lib/stateReconciler/autoMergeLevel2';

const persistConfig = {
  key: 'root',
  storage,
  stateReconciler: autoMergeLevel2
}

```

## 自定义保留的内容

通过使用传递给`persistReducer`的`config`对象的`blacklist`和`whitelist`属性，我们可以定制我们状态的一部分来持久化。使用`blacklist`属性，我们可以指定不持久化状态的哪一部分，而`whitelist`属性则相反，指定持久化状态的哪一部分。

例如，假设我们有以下减速器:

```
const rootReducer = combineReducers({ 
  user: userReducer,
  notes: notesReducer
})

```

如果我们想防止`notes`持续存在，那么`config`对象应该如下所示:

```
const rootPersistConfig = {
  key: 'root',
  storage,
  blacklist: ['notes']
}

// OR

const rootPersistConfig = {
  key: 'root',
  storage,
  whitelist: ['users']
}

```

`blacklist`和`whitelist`属性接受一个字符串数组。每个字符串必须匹配由我们传递给`persistReducer`的 reducer 管理的状态的一部分。当使用`blacklist`和`whitelist`时，我们只能瞄准一层深度。但是，如果我们想在上面的状态中定位一个属性，我们可以利用嵌套持久化。

例如，假设`userReducer`初始状态如下:

```
const initialState = {
  user: {},
  isLoggedIn: false,
}

```

如果我们想防止`isLoggedIn`持续，我们的代码将如下所示:

```
const rootPersistConfig = {
  key: 'root',
  storage,
}

const userPersistConfig = {
  key: 'user',
  storage,
  blacklist: ['isLoggedIn']
}

const rootReducer = combineReducers({
  user: persistReducer(userPersistConfig, userReducer),
  notes: notesReducer
})

const persistedReducer = persistReducer(rootPersistConfig, rootReducer);

```

现在，`isLoggedIn`属性不会被持久化。

## 结论

在本教程中，我们学习了如何使用 Redux Toolkit 中的 Redux Persist 将数据保存在持久存储中。因此，即使在浏览器刷新后，我们的数据仍将保留。我们还探索了定制 Redux Persist 的几个选项，例如，指定使用哪个存储引擎，以及使用`blacklist`和`whitelist`属性定制在我们的状态中持久化的内容。

虽然在撰写本文时，Redux Persist 正在维护中，已经有一段时间没有更新了，但它仍然是一个非常棒的工具，拥有强大的社区支持。我希望你喜欢这个教程，如果你有任何问题，一定要留下评论。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)