# 如何在 Next.js - LogRocket 博客中使用 Redux

> 原文：<https://blog.logrocket.com/use-redux-next-js/>

***编者按:**这篇文章于 2023 年 2 月 22 日更新，包含了关于在 Next.js 中保持认证状态的信息，并提供了关于在 Next.js 中何时使用或避免使用 Redux 的额外见解。*

Redux 是 React 生态系统中最受欢迎的状态管理解决方案之一。如今，有[大量的替代品](https://blog.logrocket.com/react-hooks-context-redux-state-management/)，但 Redux 仍然是最值得信赖和广泛使用的工具。

由于这个原因，许多使用 Next.js 的项目也想利用 Redux。但是在下一个应用程序中使用 Redux 有一些问题，设置并不总是简单明了的。这就是为什么本文将带您了解如何使用 Redux 建立 Next.js 项目。

## 内容

## 为什么要在 Next.js 中使用 Redux？

在下一个应用程序中使用 Redux 有很多原因。让我们来看看其中的一些。

### 共享状态

通常，中央状态用于管理树中组件之间的共享数据。在 React 中，数据只向下流动，这意味着您可以将数据从父组件传递到子组件。

这种限制有时会使事情变得困难，因为组件树中的组件可能不靠近，甚至可能没有父子路径。

在这种情况下，使用一个包装所有组件的公共存储完全有意义，您可以考虑 Redux。

### Redux 很厉害

Redux 作为一个状态管理解决方案非常强大。它已经存在了一段时间，所以它有很好的社区支持。

如果您正在构建一些严重的东西，并且不确定将来可能会出现哪些用例，Redux 很可能会为您提供一个解决方案。虽然没有什么是完全经得起未来考验的，但 Redux 是长期项目的安全赌注。

### 每个人都知道 Redux

在许多项目中，速度通常是优先考虑的。许多 React 开发人员已经熟悉了 Redux，如果可能的话，公司通常希望在所有项目中使用相同的工具。

这意味着即使你在一家公司工作，在 Next 中建立一个新项目，你也可能会被迫使用 Redux，所以仅仅根据流行程度来学习如何使用它是一个好主意。

### Redux 非常灵活

Redux 如此受欢迎的一个主要原因是它的灵活性。Redux 提供了广泛的特性、中间件、缓存和性能。它有一个非常活跃的开发者社区。此外，Redux 可以分离应用程序内部的关注点，从而在将来实现更好的代码管理。

如果你开始在你的应用中使用 Redux，你将不会被它的特性所限制。事实上，它将开启许多新的可能性。您可以选择在您的应用程序中包含或不包含这些功能。

即使你选择不使用 Redux，学习一个新的概念从来都不是坏事！因此，让我们深入研究一下，看看如何将 Redux 集成到 Next.js 应用程序中。

## 使用 Next.js 和 Redux 构建示例应用程序

今天，我们将构建一个简单的应用程序，它跟踪用户是否登录，然后根据状态更改按钮上方的文本:

![Sample app login button](img/9cd9b5a90cec78622b3239c413951283.png)

![Sample app logout button](img/6f5731f1d4424d222167bdf0c144c31a.png)

这个项目旨在演示如何使用 Redux，所以我在这里保持简单，这样我们就可以专注于 Redux 与 Next 的集成。展望未来，我们有两个选择。我们可以使用普通的 [Redux](https://redux.js.org/) ，也可以使用 [Redux 工具包](https://redux-toolkit.js.org/)。

Redux 正在许多遗留项目中使用，但是推荐使用 Redux Toolkit ,因为它减少了大量样板代码，并提高了性能。然而，这两者的设置几乎相同。

让我们通过运行以下命令来创建启动项目:

```
yarn create next-app --typescript

```

您可以通过运行`yarn dev`并在浏览器上访问 [http://localhost:3000/](http://localhost:3000/) 来查看项目的运行情况。

### 安装依赖项

让我们安装 Redux Toolkit 所需的依赖项:

```
yarn add @reduxjs/toolkit react-redux

```

当我们使用 Next 时，我们需要一个额外的包来处理我们的服务器端渲染:

```
yarn add next-redux-wrapper

```

### 创建切片

让我们创建一个名为`store`的新文件夹，并在其中创建一个名为`authSlice.ts`的文件。[官方文档](https://redux.js.org/tutorials/essentials/part-2-app-structure)将切片定义为:“你的应用中单个特性的 Redux reducer 逻辑和动作的集合。”

我们将把我们的`authState`的逻辑放在这个`authSlice.ts`文件中:

```
import { createSlice } from "@reduxjs/toolkit";
import { AppState } from "./store";
import { HYDRATE } from "next-redux-wrapper";

// Type for our state
export interface AuthState {
  authState: boolean;
}

// Initial state
const initialState: AuthState = {
  authState: false,
};

// Actual Slice
export const authSlice = createSlice({
  name: "auth",
  initialState,
  reducers: {
    // Action to set the authentication status
    setAuthState(state, action) {
      state.authState = action.payload;
    },
  },

  // Special reducer for hydrating the state. Special case for next-redux-wrapper
  extraReducers: {
    [HYDRATE]: (state, action) => {
      return {
        ...state,
        ...action.payload.auth,
      };
    },
  },
});

export const { setAuthState } = authSlice.actions;

export const selectAuthState = (state: AppState) => state.auth.authState;

export default authSlice.reducer;

```

这是一个简单的切片。任何使用 Redux 的普通 React 应用程序的一个片段都是这样的。接下来还没有什么特别的。

我们在这里做的唯一一件事是在我们的商店中定义`authState`并创建设置名为`setAuthState`的`authState`的动作。

在第 27 行，您会注意到我们在这里添加了一个特殊的缩减器，名为`HYDRATE`。`HYDRATE`动作处理器必须在现有状态(如果有的话)的基础上正确地协调水合状态。

基本上，当任何页面刷新发生时，如果你从一个页面导航到另一个页面，或者调用了`getStaticProps`或`getServerSideProps`函数，一个`HYDRATE`动作将在那一刻被调度。这个动作的`payload`会包含静态生成或者服务器端渲染时的状态，所以你的 reducer 必须把它和现有的客户端状态正确合并。

### 创建商店

接下来，创建一个名为`store.ts`的文件来创建商店，并在那里添加我们的`authSlice`:

```
import { configureStore, ThunkAction, Action } from "@reduxjs/toolkit";
import { authSlice } from "./authSlice";
import { createWrapper } from "next-redux-wrapper";

const makeStore = () =>
  configureStore({
    reducer: {
      [authSlice.name]: authSlice.reducer,
    },
    devTools: true,
  });

export type AppStore = ReturnType<typeof makeStore>;
export type AppState = ReturnType<AppStore["getState"]>;
export type AppThunk<ReturnType = void> = ThunkAction<
  ReturnType,
  AppState,
  unknown,
  Action
>;

export const wrapper = createWrapper<AppStore>(makeStore);

```

注意第 22 行，我们导出了一个特殊的`wrapper`函数。这个包装器消除了我们在普通 React 应用程序中使用的`Provider`的需要。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 更新应用程序

我们还需要做最后一件事来完成 Redux 架构的设置。打开`_app.tsx`文件，像这样包装我们的组件:

```
import "../styles/globals.css";
import type { AppProps } from "next/app";
import { wrapper } from "../store/store";

function MyApp({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />;
}

export default wrapper.withRedux(MyApp);

```

注意第 9 行，我们用`withRedux`包装了组件。我们也可以包装单个页面，但不推荐这样做；当我们从一个页面导航到另一个页面时，如果那个特定的页面没有换行，它就会崩溃。

## 使用 Redux 商店

我们的 Redux 设置完成了！让我们像这样在`index.tsx`页面中使用我们的 Redux 存储:

```
import type { NextPage } from "next";
import { selectAuthState, setAuthState } from "../store/authSlice";
import { useDispatch, useSelector } from "react-redux";

const Home: NextPage = () => {
  const authState = useSelector(selectAuthState);
  const dispatch = useDispatch();
  return (
    <div>
      <div>{authState ? "Logged in" : "Not Logged In"}</div>
      <button
        onClick={() =>
          authState
            ? dispatch(setAuthState(false))
            : dispatch(setAuthState(true))
        }
      >
        {authState ? "Logout" : "LogIn"}
      </button>
    </div>
  );
};

export default Home;

```

任何 Redux 存储都有两个主要目的:读取和更新。
在第 6 行，你可以看到我们正在使用`react-redux`提供的`useSelector`函数读取状态。

我们有一个可以切换`authState`的按钮，基于此，我们正在更改按钮上的文本。

## 在 Next.js 中保持页面导航之间的状态

现在我们已经成功地建立了我们的 Redux 商店。您可以通过单击按钮来验证它，这将基于当前状态调度操作并更新存储，最终将更改状态。

但是如果你刷新你的页面，你会看到这个状态不再持续。这是因为，在 Next 中，每个页面都是按需呈现的，这意味着当您从一个页面导航到另一个页面时，以前的状态将会消失。

对于这种情况，如果用户已经登录，那么每当您切换到另一个页面时，用户将自动注销，因为初始的`authState`被定义为 false。

为了解决这个问题，我们将利用我们之前创建的包装器函数，并使用 Next 的特殊函数`getServerSideProps`，因为每次页面加载时都会调用这个函数。

让我们将下面的代码添加到我们的`index.tsx`文件中:

```
export const getServerSideProps = wrapper.getServerSideProps(
  (store) =>
    async ({ params }) => {
      // we can set the initial state from here
      // we are setting to false but you can run your custom logic here
      await store.dispatch(setAuthState(false)); 
      console.log("State on server", store.getState());
      return {
        props: {
          authState: false,
        },
      };
    }
);

```

我们在这里生成的是`getServerSideProps`函数内部的初始状态，所以即使你刷新页面；您将看到状态值保持不变。

## 在 Next.js 中持久化身份验证状态

当涉及到身份验证时，状态不仅需要在页面转换之间持久化，还需要在刷新期间持久化。这是必要的，这样用户就不必在每次刷新页面时登录。

为了正确地持久化认证状态，我们可以使用`[redux-persist]([https://www.npmjs.com/package/redux-persist](https://www.npmjs.com/package/redux-persist))`库。让我们看看如何整合这一点。

首先，安装依赖项，如下所示:

```
yarn add redux-persist

```

然后，打开`store.ts`文件，创建一个名为`makeStore`的新函数:

```
import { persistReducer, persistStore } from "redux-persist";
import storage from "redux-persist/lib/storage";

const rootReducer = combineReducers({
  [authSlice.name]: authSlice.reducer,
});

const makeConfiguredStore = () =>
  configureStore({
    reducer: rootReducer,
    devTools: true,
  });

export const makeStore = () => {
  const isServer = typeof window === "undefined";
  if (isServer) {
    return makeConfiguredStore();
  } else {
    // we need it only on client side
    const persistConfig = {
      key: "nextjs",
      whitelist: ["auth"], // make sure it does not clash with server keys
      storage,
    };
    const persistedReducer = persistReducer(persistConfig, rootReducer);
    let store: any = configureStore({
      reducer: persistedReducer,
      devTools: process.env.NODE_ENV !== "production",
    });
    store.__persistor = persistStore(store); // Nasty hack
    return store;
  }
};

// Previous codes 

export const wrapper = createWrapper<AppStore>(makeStore);

```

在上面的代码中，我们试图理解我们是在处理服务器状态还是客户端状态。因为在服务器端，我们不需要持久性。

接下来，我们根据它是客户机还是服务器调用不同配置的`makeConfiguredStore`。在处理客户端状态时，我们需要使用`redux-toolkit`导出的`persistReducer`函数创建一个`persistedReducer`。我们使用`persistConfig`来指定密钥和存储类型。具体到`redux-tookit`，这里就不赘述了。然后，我们将这个`persistStore`赋给`store.__persistor`并返回它。

我们需要修改我们的`_app.tsx`文件来利用这个持久化的缩减器。

最终的代码如下所示:

```
import "../styles/globals.css";
import type { AppProps } from "next/app";
import { wrapper } from "../store/store";
import { PersistGate } from "redux-persist/integration/react";
import { useStore } from "react-redux";
function MyApp({ Component, pageProps }: AppProps) {
  const store: any = useStore();
  return (
    <PersistGate persistor={store.__persistor} loading={<div>Loading</div>}>
      <Component {...pageProps} />
    </PersistGate>
  );
}
export default wrapper.withRedux(MyApp);

```

基本上，我们用`PersistGate`包装我们的组件，并传递`store.__persistor`和一个可选的`loading`组件，以向用户显示我们的状态何时恢复。

现在，如果一切顺利，我们现在可以刷新我们的应用程序，身份验证状态将持续！

万岁！我们成功地将`redux-persist`添加到我们的应用程序中，允许我们管理身份验证。

## 什么时候应该避免在 Next.js 中使用 Redux？

在某些情况下，Redux 可能不是最佳选择。像其他任何东西一样，Redux 也有一些缺点，在与 Next.js 集成时尤其如此。

我们来讨论一些不应该使用 Redux 的场景。

### 你正在建立一个小规模的项目

如果您只是在构建一个小规模的项目，那么 Redux 可能有些过头了。在 Next.js 中，默认的状态管理解决方案能够并且将能够处理几乎所有的用例。因此，除非您构建一些重要的东西或概念验证，否则您应该考虑默认的状态管理解决方案。

### 设置很复杂

在 Next.js 中集成 Redux 是一个复杂的过程。如果您需要快速发布一个特性，使用 Redux 可能不是您的最佳选择。

### 反应的上下文应用编程接口就足够了

[上下文](https://reactjs.org/docs/context.html)是 React 自己为组件间共享状态的解决方案。根据[文件](https://reactjs.org/docs/context.html):

> “上下文提供了一种通过组件树传递数据的方式，而不必在每一级手动向下传递属性。”

这和 Redux 在做的事情一模一样。所以如果你不需要 Redux 那些花哨的功能，那就别费劲用了。对于大多数用例，React 的上下文 API 就足够了。

## 结论

这就是如何将 Redux 与 Next.js 应用程序集成的方法！你可以在这里找到这个项目的 GitHub 库。我也鼓励你阅读 [next-redux-wrapper](https://github.com/kirill-konshin/next-redux-wrapper) 的文档，以了解更多关于其他用例的信息。

祝您愉快！

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。