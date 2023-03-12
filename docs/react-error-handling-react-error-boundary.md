# React 错误处理与 react-error-boundary - LogRocket 博客

> 原文：<https://blog.logrocket.com/react-error-handling-react-error-boundary/>

***编者按:**本文于 2022 年 4 月 13 日更新，以反映 react-error-boundary 的最新信息，包括使用`withErrorBoundary`函数作为高阶组件。*

在我们的应用程序中，错误是必然会发生的，无论是与服务器相关的错误、边缘情况还是其他。因此，已经开发了许多方法来防止这些错误干扰用户和开发人员的体验。在 React 中，一种这样的方法是使用错误边界。

在本文中，我们将使用 react-error-boundary 检查 React 错误处理。我们将讨论以下内容:

## React 中的错误边界

React 16 中引入了错误边界，作为捕捉和处理组件 UI 部分中发生的 JavaScript 错误的一种方式。所以错误边界只捕捉发生在生命周期方法、渲染方法和类似于`useEffect`的钩子内部的错误。根据 React 文档，错误边界不处理以下错误:

*   事件处理程序
*   异步代码(例如，`setTimeout`或`requestAnimationFrame`回调)
*   服务器端渲染
*   在错误边界本身(而不是其子系)中掷回的错误

所以基本上，错误边界只处理代码中涉及 React 的部分的错误。

要创建错误边界，我们只需创建一个类组件，并定义一个状态变量来确定错误边界是否捕捉到了错误。我们的类组件也应该至少有三个方法:

1.  名为`getDerivedStateFromError`的静态方法，用于更新错误边界的状态
2.  一种生命周期方法，用于在我们的错误边界捕捉到错误时执行操作，比如记录到错误记录服务
3.  一个`render`方法，用于呈现我们的错误边界的子元素或出错时的回退 UI

这里有一个例子(摘自 [React 文档](https://reactjs.org/docs/error-boundaries.html))展示了我们简单的错误边界应该是什么样子:

```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // Update state so the next render will show the fallback UI.
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // You can also log the error to an error reporting service
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}

```

## 反应误差边界

react-error-boundary 是 react 错误边界的包装器，允许开发人员在代码中实现错误边界，而无需从头开始构建。使用 react-error-boundary，我们可以简单地用提供的`ErrorBoundary`组件包装我们预期会出错的组件，并传入一些额外的道具来定制我们的错误边界的行为。

在本文中，我将使用 react-error-boundary 来处理 react 应用程序中的错误。让我们来看看图书馆提供了什么。

### `ErrorBoundary`组件

`ErrorBoundary`组件是 react-error-boundary 中的主要组件。它允许我们用更少的代码实现典型的 React 错误边界。

这里有一个非常基本的`ErrorBoundary`用例:

```
function App(){
  ...
  return (
    <ErrorBoundary
          FallbackComponent={OurFallbackComponent}
        >
          <ComponentThatMightThrowAnError/>
    </ErrorBoundary>
  );
}

const OurFallbackComponent = ({ error, componentStack, resetErrorBoundary }) => {
  return (
    <div>
      <h1>An error occurred: {error.message}</h1>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  );
};

```

在上面的组件中，我们简单地用`ErrorBoundary`组件包装我们的组件，并将我们的回退组件传递给`FallbackComponent` prop，这样如果有错误(可以由 react-error-boundary 捕获和处理)，我们的回退组件将被呈现；否则，我们的组件将被渲染。

我们还有`fallbackRender` prop，这是一个基于渲染 prop 的 API，用于以内联方式指定我们的回退组件。下面是上面使用`fallbackRender`属性的代码块:

```
function App(){
  ...
  return (
    <ErrorBoundary
      fallbackRender =  {({error, resetErrorBoundary, componentStack}) => (
          <div>
          <h1>An error occurred: {error.message}</h1>
          <button onClick={resetErrorBoundary}>Try again</button>
        </div>
      )}
    >
      <ComponentThatMightThrowAnError/>
    </ErrorBoundary>
  );
}

```

`ErrorBoundary`还有一个`onError` prop，它充当一个监听器，当我们的错误边界捕获并处理其子组件中的错误时，就会触发这个监听器。正是从这个位置，我们可以选择将这样的错误记录到我们可能使用的任何错误记录服务中。

```
function App(){
  ...

  return (
    <ErrorBoundary
      onError = {(error, componentStack) => {
        logToErrorLoggingService(error, componentStack);
      }}
      ...
    >
      <ComponentThatMightThrowAnError/>
    </ErrorBoundary>
  );
}

```

### 重置误差边界

react-error-boundary 还为我们的代码提供了一种从错误边界捕获的错误中恢复的方法。这是通过使用复位键和传递给回退组件的`resetErrorBoundary`函数来完成的。

解释其工作原理的最佳方式是使用一个直接取自 [React 文档](https://reactjs.org/docs/error-boundaries.html)的示例代码块:

```
function ErrorFallback({error, componentStack, resetErrorBoundary}) {
  return (
    <div role="alert">
      <p>Something went wrong:</p>
      <pre>{error.message}</pre>
      <pre>{componentStack}</pre>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  )
}

function Bomb() {
  throw new Error('💥 KABOOM 💥')
}

function App() {
  const [explode, setExplode] = React.useState(false)
  return (
    <div>
      <button onClick={() => setExplode(e => !e)}>toggle explode</button>
      <ErrorBoundary
        FallbackComponent={ErrorFallback}
        onReset={() => setExplode(false)}
        resetKeys={[explode]}
      >
        {explode ? <Bomb /> : null}
      </ErrorBoundary>
    </div>
  )
}

```

从上面的代码中我们可以看到，创建了一个状态钩子，用于确定`App`组件是呈现一个抛出错误的`Bomb`组件还是一个错误安全组件。重置键也被传递给错误边界组件。这些重置键确定是否将重置错误边界的内部状态。如果其中一个重置键在渲染过程中改变，错误边界的内部状态将被重置。

另一方面，调用`resetComponent`函数会触发我们的`ErrorBoundary`组件的`onReset`处理程序，在这里我们将`explode`状态值设置为`false`。这导致我们的`App`组件呈现一个错误安全的组件。

我们还有`onResetKeysChange`处理程序，当 reset 键的值改变时触发，导致错误边界内部状态的重置。

### `useErrorHandler`挂钩

react-error-boundary 库的另一个重要特性是，它允许开发人员使用错误边界来捕获传统 react 错误边界无法捕获的错误。这意味着我们现在可以使用错误边界来捕捉 API 请求、事件处理程序和其他可能发生错误的代码部分中的错误。

有两种方法可以使用`useErrorHandler`挂钩:

1.  `const handleError = useErrorHandler()`:然后我们可以调用`handleError(error)`并传入错误对象，就像下面的例子一样
2.  当我们自己处理错误状态或者从另一个钩子得到错误状态时，这很有用

这就是我们如何使用第一种方法捕获 API 请求中的错误:

```
 const App = () => {
  return (
    <ErrorBoundary
      FallbackComponent={CharacterFallback}
    >
      <ComponentWhereErrorMightOccur/>
    </ErrorBoundary>
  );
};

const ComponentWhereErrorMightOccur = () => {
  const handleError = useErrorHandler();
  const callAPI = () => {
    const result = fetch(apiURL)
    .then(
      (response) => response.json(),
      (error) => handleError(error))
    .then((data) => {
      return data["results"];
    });
    return result;
  };
  useEffect(() => {
    (async function () {
      await callAPI();
    })();
  }, []);
  return (
    ...
  );
};

```

如您所见，我们需要做的就是将从我们的 API 获取数据返回的错误对象传递给我们的`handleError`函数，该函数由`useErrorHandle`钩子返回。这样，我们的误差边界就更有用了。

### `withErrorBoundary`起特设的作用

React-error-boundary 允许我们利用`withErrorBoundary`函数作为[高阶组件](https://reactjs.org/docs/higher-order-components.html) (HOC)来管理组件内的问题。这样，我们可以专注于开发组件，而将错误处理留给函数。此外，该方法减少了实现组件及其测试所需的代码量。

这里有一个来自[反应-错误-边界文档](https://github.com/bvaughn/react-error-boundary)的例子，说明如何使用它:

```
import {withErrorBoundary} from 'react-error-boundary'

const ComponentWithErrorBoundary = withErrorBoundary(ComponentThatMayError, {
  FallbackComponent: ErrorBoundaryFallbackComponent,
  onError(error, info) {
    // Do something with the error
    // E.g. log to an error logging client here
  },
})

const ui = <ComponentWithErrorBoundary />

```

## 结论

react-error-boundary 使 react 开发人员能够减少必须编写的代码量，并扩展他们的错误边界功能，以捕捉常规错误边界无法识别的其他形式的错误。在 GitHub 上了解更多关于 react-error-boundary 的信息。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)