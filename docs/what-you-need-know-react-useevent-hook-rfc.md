# 关于 React useEvent Hook RFC ，你需要知道什么

> 原文：<https://blog.logrocket.com/what-you-need-know-react-useevent-hook-rfc/>

在 React 中，引用相等是一个重要的概念，它影响应用程序中组件重新呈现的频率。在本文中，我们将探索 React 的 [`useEvent`钩子，它允许您定义一个具有始终稳定的函数标识的事件处理程序，帮助管理应用程序中的引用相等性。](https://github.com/reactjs/rfcs/blob/useevent/text/0000-useevent.md)

值得注意的是，在撰写本文时，`useEvent`钩子还不可用，目前 React 社区正在讨论它。

## JavaScript 中的引用相等

在 JavaScript 中，您可以使用 identity 运算符比较两个值是否相等，这也称为严格相等。例如，在下面的代码中，您将比较值`a`和`b`:

```
a === b; // strict equality or indentity operator

```

结果是一个布尔值，它告诉你值`a`是否等于`b`。

对于原始数据类型，使用实际值进行比较。例如，如果`a` = `10`，`b` = `10`，那么身份运算符将返回`true`。

像对象和函数一样，复数值也是一种引用类型。即使它们具有相同的代码，两个函数也不相等。看看下面的例子，其中 identity 操作符将返回`false`:

```
let add = (a, b) => a + b;
let anotherAdd = (a, b) => a + b;
console.log(add === anotherAdd); // false

```

但是，如果比较同一个函数的两个实例，它会返回`true`:

```
let thirdAdd = add;
console.log(add === thridAdd); // true

```

换句话说，`thirdAdd`和`add`在指称上是相等的。

## 为什么 referential equality 在 React 中很重要？

理解 React 中的引用相等很重要，因为我们经常用相同的代码创建不同的函数。例如，考虑以下代码:

```
function AComponent() {
  // handleEvent is created and destroyed on each re-render
  const handleEvent = () => {
    console.log('Event handler');
  }
  // ...
}

```

React 销毁当前版本的`handleEvent`函数，并在每次`AComponent`重新渲染时创建一个新版本。但是，在某些情况下，这种方法不是很有效，例如:

*   您使用一个类似于`useEffect` 的[钩子，在其依赖数组中接受一个事件处理程序](https://blog.logrocket.com/underrated-react-hooks-youre-missing-out-on/)
*   您有一个接受事件处理程序的内存化组件

在这两种情况下，您都希望维护事件处理程序的单个实例。但是，每次重新呈现时，您都会得到一个新的函数实例，这会进一步影响性能，要么重新呈现一个内存化的组件，要么触发`useEffect`回调。

你可以通过使用`useCallback`钩子很容易地解决这个问题，如下所示:

```
function AComponent() {
  const handleEvent = useCallback(() => {
    console.log('Event handled');
  }, []);
  // ...
}

```

`useCallback`钩子对函数进行记忆，这意味着无论何时用唯一的输入调用一个函数，`useCallback`钩子都会保存该函数的副本。因此，如果在重新渲染期间输入没有改变，您将得到相同的函数实例。

但是，当您的事件处理程序依赖于一个状态或属性时，`useCallback`钩子会在每次改变时创建一个新的处理函数。例如，看一下下面的代码:

```
function AComponent() {
  const [someState, setSomeState] = useState(0);
  const handleEvent = useCallback(() => {
    console.log('log some state: `, someState);
  }, [someState]);

  // ...
}

```

现在，每次重新呈现组件时，都不会创建函数。但是，如果`someState`改变了，它将创建一个新的`handleEvent`实例，即使函数的定义保持不变。

## `useEvent`钩子

`useEvent`钩子试图解决这个问题；您可以使用`useEvent`钩子来定义一个事件处理程序，它的函数标识总是稳定的。换句话说，在每次重新渲染期间，事件处理程序在引用上是相同的。本质上，事件处理程序将具有以下属性:

*   不会在每次属性或状态改变时重新创建该函数
*   该函数将访问属性和状态的最新值

你可以如下使用`useEvent`钩子:

```
function AComponent() {
  const [someState, setSomeState] = useState(0);
  const handleEvent = useEvent(() => {
    console.log('log some state: `, someState);
  });
  // ...
}

```

因为`useEvent`钩子确保了一个函数只有一个实例，所以你不需要提供任何依赖。

## 从 RFC 实现`useEvent`钩子

下面的例子是来自 RFC 的 [`useEvent`钩子的近似实现:](https://github.com/reactjs/rfcs/blob/useevent/text/0000-useevent.md)

```
// (!) Approximate behavior

function useEvent(handler) {
  const handlerRef = useRef(null);

  // In a real implementation, this would run before layout effects
  useLayoutEffect(() => {
    handlerRef.current = handler;
  });

  return useCallback((...args) => {
    // In a real implementation, this would throw if called during render
    const fn = handlerRef.current;
    return fn(...args);
  }, []);
}

```

在组件的每次渲染中，都会调用`useEvent`钩子。每次渲染时，`handler`函数被传递给`useEvent`钩子。`handler`函数总是有最新的`props`和`state`值，因为它本质上是渲染组件时的一个新函数。

在`useEvent`钩子内，`useLayoutEffect`钩子也被每次渲染调用，并将`handlerRef`改变为`handler`函数的最新值。

在真实版本中，在所有 `useLayoutEffect` 函数被调用之前， `handlerRef` 会被切换到最新的处理函数。

最后一块是`useCallback`返回。`useEvent`钩子返回一个用空的依赖数组`[]`包装在`useCallback`钩子中的函数。这就是函数总是具有稳定的引用标识的原因。

你可能想知道这个函数怎么总是有新的值`props`和`state`。如果仔细观察一下，用于`useCallback`钩子的匿名函数使用了`handlerRef`的当前值。这个`current`值代表了`handler`的最新版本，因为它在`useLayoutEffect`被调用时被切换。

## 什么时候不应该使用`useEvent`钩？

在某些情况下，你不应该使用`useEvent`钩子。我们来了解一下什么时候，为什么。

首先，你不能在渲染过程中使用用`useEvent`钩子创建的函数。例如，下面的代码将失败:

```
function AComponent() { 
  const getListOfData = useEvent(() => {
    // do some magic and return some data
    return [1, 2, 3];
  });

  return <ul>
    {getListOfData().map(item => <li>{item}</li>}
  </ul>;
}

```

## 卸载`useEffect`与`useLayoutEffect`

未安装的`useEffect`挂钩和`useLayoutEffect`挂钩将具有不同版本的`useEvent`处理器。看一下下面的例子:

```
function Counter() {
  const [counter, setCounter] = React.useState(0);
  const getValue = useEvent(() => {
    return counter;
  });
  React.useLayoutEffect(() => {
    return () => {
      const value = getValue();
      console.log('unmounting layout effect:', value);
    };
  });
  React.useEffect(() => {
    return () => {
      const value = getValue();
      console.log('unmounting effect:', value);
    };
  });
  return (
    <React.Fragment>
      Counter Value: {counter}
      <button onClick={() => setCounter(counter + 1)}>+</button>
    </React.Fragment>
  );
}

```

如果你运行这个程序，你会看到卸载的`useLayoutEffect`有旧版本的`getValue`事件处理程序。请随意查看 [Stackblitz 示例](https://react-ts-uuk4dv.stackblitz.io)。

## 结论

虽然`useEvent`钩子还不能使用，但对 React 开发人员来说，它绝对是一个有前途的发展。在本文中，我们探索了`useEvent`钩子背后的逻辑，回顾了应该和不应该使用它的场景。

关注这个`useEvent`钩子绝对是值得的，我期待着最终能够将它集成到我的应用程序中。我希望你喜欢这篇文章。编码快乐！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)