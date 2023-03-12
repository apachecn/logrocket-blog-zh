# React Hooks 的挫折解决方案

> 原文：<https://blog.logrocket.com/solutions-to-frustrations-with-react-hooks/>

我以前的一篇帖子，【React Hooks 的挫折，获得了惊人的浏览量，一度超过了[黑客新闻](https://news.ycombinator.com/)。这个帖子也得到了很多评论，其中一些改变了我对钩子的看法，给了我一种全新的积极的看法。

[的上一篇文章](https://blog.logrocket.com/frustrations-with-react-hooks/)引用了一个`useFetch`例子，该例子抽象出了调用远程 API 端点的公共代码。抽象是我希望通过钩子可以重用的那种东西。我希望加载和错误状态都打包在一个钩子中，就像我们过去用 Redux 中间件做的那样。下面是我想为客户端代码编写的示例:

```
const asyncTask = useFetch(initialPage);
useAsyncRun(asyncTask);

const { start, loading, result: users } = asyncTask;

if (loading) {
  return <div>loading....</div>;
}

return (
  <>
    {(users || []).map((u: User) => (
      <div key={u.id}>{u.name}</div>
    ))}
  </>
);
```

我举了一个基于 [react-hooks-async](https://github.com/dai-shi/react-hooks-async) 的例子，它有一个`useFetch`钩子。

这里有一个 [CodeSandbox](https://codesandbox.io/s/dependency-array-cxowi?from-embed) 包含缩小的示例:

下面是代码清单:

许多评论提到了这种方法的复杂性，最有说服力的评论提到这种实现不是非常具有声明性。

```
const createTask = (func, forceUpdateRef) => {
  const task = {
    start: async (...args) => {
      task.loading = true;
      task.result = null;
      forceUpdateRef.current(func);
      try {
        task.result = await func(...args);
      } catch (e) {
        task.error = e;
      }
      task.loading = false;
      forceUpdateRef.current(func);
    },
    loading: false,
    result: null,
    error: undefined
  };
  return task;
};

export const useAsyncTask = (func) => {
  const forceUpdate = useForceUpdate();
  const forceUpdateRef = useRef(forceUpdate);
  const task = useMemo(() => createTask(func, forceUpdateRef), [func]);

  useEffect(() => {
    forceUpdateRef.current = f => {
      if (f === func) {
        forceUpdate({});
      }
    };
    const cleanup = () => {
      forceUpdateRef.current = () => null;
    };
    return cleanup;
  }, [func, forceUpdate]);

  return useMemo(
    () => ({
      start: task.start,
      loading: task.loading,
      error: task.error,
      result: task.result
    }),
    [task.start, task.loading, task.error, task.result]
  );
};
```

钩子代表可重用的生命周期行为

## 毫无疑问，评论部分最好的评论来自凯伦·格里戈良，她指出钩子是可重用生命周期行为的地方。

[react-hooks-async](https://github.com/dai-shi/react-hooks-async) 和 [CodeSandbox](https://codesandbox.io/s/dependency-array-cxowi?from-embed) 中的例子使用`useAsyncRun`函数来启动生命周期变更事件:

React 经常被吹捧为声明式框架，我爱上 React 的原因之一是单向数据流的故事。感觉更像是命令而不是宣言。

```
export const useAsyncRun = (asyncTask,...args) => {
  const { start } = asyncTask;
  useEffect(() => {
    start(...args);
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [asyncTask.start, ...args]);

useEffect(() => {
    const cleanup = () => {
      // clean up code here
    };
    return cleanup;
  });
```

反应过来的道

## React 的最佳工作方式是我们改变道具或状态，一个组件自然地做出反应。

卡伦好心地创建了这个[代码沙箱](https://codesandbox.io/s/dependency-array-cl99d)，它不仅简化了事情，而且让事情感觉更真实(是的，这现在是一个真实的单词)和说明性:

[Karen](https://twitter.com/KaaFury) kindly created this [CodeSandbox](https://codesandbox.io/s/dependency-array-cl99d) that not only simplifies things but also makes things feel much more reacty (yes this is now an actual word) and declarative:

`useFetch`现在看起来是这样的:

上面代码中的`useFetch`钩子返回一个`getFetchResult`函数。`getFetchResult`使用从`useReducer`返回的`dispatch`函数来编排生命周期变更。

```
const fetchReducer: FetchReducer = (state, action) => {
  switch (action.type) {
    case "FETCH_START": {
      return { data: null, isLoading: true, error: null };
    }
    case "FETCH_SUCCESS": {
      return { data: action.payload, isLoading: false, error: null };
    }

    case "FETCH_ERROR": {
      return { data: null, isLoading: false, error: action.payload };
    }
    default:
      return state;
  }
};

export const useFetch = (initial) => {
  const [state, dispatch] = useReducer(fetchReducer, initialState);

  const getFetchResult = useCallbackOne(
    async (overrides) => {
      dispatch({ type: "FETCH_START" });
      try {
        const result = await api({ ...initial, ...overrides });
        dispatch({ type: "FETCH_SUCCESS", payload: (result as unknown) as T });
      } catch (err) {
        dispatch({ type: "FETCH_ERROR", payload: err });
      }
    },
    [initial]
  );

  return [state, getFetchResult];
};
```

使用`useState`和`useReducer`是我们用来触发效果变化的，但是是以声明的方式。强制重新呈现是在 React 中逆流而上，违背了 React 的声明性本质。我想我又一次爱上了 React 的单向数据流。单向数据流是吸引我做出反应的原因，它仍然可以驯服沉重的 JavaScript 应用程序的混乱。

React 应该以这种方式工作，我们改变状态，组件知道如何重新呈现，并且响应状态改变而执行`useEffect`代码块。

客户端代码现在看起来像这样:

`getFetchResult`现在可以在组件第一次安装时用在`useEffect`中，也可以用在事件处理程序中。

```
const [fetchResult, getfetchResult] = useFetch<User[]>(initialPage);

  const { data: users, isLoading, error } = fetchResult;

  // to keep reference identity in tact until next remount
  const defaultUsersRef = useRef<User[]>([]);

  // to kick off initial request
  useEffect(() => {
    getfetchResult(initialPage);
  }, [getfetchResult]);

  if (isLoading) {
    return <div>loading....</div>;
  }

  if (error) {
    return <div>error : {JSON.stringify(error)}</div>;
  }

  return (
    <>
      <Users users={users || defaultUsersRef.current} />
      <Knobs onClick={getfetchResult} />
    </>
  );
```

非常感谢 Karen 给我们举了这么好的例子。

同样值得注意的是[悬念](https://reactjs.org/docs/concurrent-mode-suspense.html)可能会很快消失，这可能是真正适合`useFetch`的解决方案。

观察力敏锐的你会注意到`getFetchResult`使用[使用备忘录一](https://github.com/alexreardon/use-memo-one)中的`useCallbackOne`。`useCallbackOne`是`useCallback`的安全替代品。`useCallbackOne`对依赖数组的值进行浅层检查，而不是数组引用。这仍然是 React Hooks 的一个令人沮丧的地方，我们需要一个外部库来实现它，这很好地解决了陈旧的闭包问题。

陈旧的关闭问题

## 我总是害怕闭包，因为在处理闭包的时候会发生一些不可思议的事情。在处理钩子时，闭包是生活中的一个事实。下面的例子很好地说明了这一现象:

这个 [CodeSandbox](https://codesandbox.io/s/nifty-wozniak-1jjtz) 展示了这种邪恶的行为:

```
const useInterval = (callback, delay) => {
  useEffect(() => {
    let id = setInterval(() => {
    callback();
  }, 1000);
    return () => clearInterval(id);
  }, []);
};

const App = () => {
 let [count, setCount] = useState(0);

 useInterval(() => setCount(count + 1), 1000);

 return <h1>{count}</h1>;
};
```

This [CodeSandbox](https://codesandbox.io/s/nifty-wozniak-1jjtz) shows this great evil in action:

所发生的事情是，`useInterval`钩子中的`useEffect`从第一次渲染中捕获计数，其初始值为`0`。`useEffect`有一个空的依赖数组，这意味着它永远不会被重新应用，并且总是从第一次渲染开始引用`0`，计算总是`0 + 1`。

如果您想很好地使用`useEffect`,您需要确保依赖数组包含任何来自外部作用域的值，这些值会随着时间的推移而改变，并被效果所使用。

[react-hooks/exhaustive-deps](https://reactjs.org/docs/hooks-rules.html)林挺规则在很大程度上很好地突出了缺失的依赖项，它正确地指出了作为第二个参数传递给`useEffect`的数组中缺失了`callback`:

我们的问题是传递给`useInterval`的回调是一个箭头函数，这意味着它会在每次渲染时重新创建:

```
const useInterval = (callback, delay) => {
  useEffect(() => {
    let id = setInterval(() => {
      callback();
    }, delay);

    return () => clearInterval(id);
  }, [callback, delay]);
};

const App = () => {
  let [count, setCount] = useState(0);

  useInterval(() => setCount(count + 1), 1000);

  return <h1>{count}</h1>;
};
```

陈旧闭包的一个解决方案

```
useInterval(() => setCount(count + 1), 1000);
```

## 在[的这篇文章](https://overreacted.io/making-setinterval-declarative-with-react-hooks/)中，丹·阿布拉莫夫提出了在可变引用中存储回调的案例。

我已经看到相同的解决方案以不同的形式出现在几个包中，基于在可变 ref 中存储回调的主题。我以来自 [formik](https://github.com/jaredpalmer/formik) 的例子为例，它提供了一个`useEventCallback`钩子，负责将回调存储在一个可变钩子中。

将回调存储在一个可变的 ref 中意味着最新的回调可以在每次渲染时保存在 ref 中。

```
function useEventCallback(fn) {
  const ref = React.useRef(fn);

  useEffect(() => {
    ref.current = fn;
  });

  return React.useCallback(
    (...args) => ref.current.apply(void 0, args),
    []
  );
}

function useInterval(callback, delay) {
  const savedCallback = useEventCallback(callback);

  useEffect(() => {
    function tick() {
      savedCallback();
    }

    let id = setInterval(tick, delay);
    return () => clearInterval(id);
  }, [delay]);
}

const App = () => {
  let [count, setCount] = useState(0);

  useInterval(() => {
    setCount(count + 1);
  }, 1000);

  return <h1>{count}</h1>;
};
```

这个[代码沙盒](https://codesandbox.io/s/floral-feather-fk9r3)展示了`useEventCallback`的运行:

结论

钩子是一种思维转变，我认为我们需要重新调整我们的思维。不戴 React 眼镜，我不会看他们能提供什么。钩子很好地符合 React 的声明性本质，我认为它们是一个很好的抽象，其中状态变化和组件知道如何对状态变化做出反应。太棒了。

## 使用 LogRocket 消除传统反应错误报告的噪音

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

## 自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

automatically aggregates client side errors, React error boundaries, Redux state, slow component load times, JS exceptions, frontend performance metrics, and user interactions. Then LogRocket uses machine learning to notify you of the most impactful problems affecting the most users and provides the context you need to fix it.

Focus on the React bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/react-signup-issue-free).