# 通过批量更新简化 React 应用的状态管理

> 原文：<https://blog.logrocket.com/simplifying-state-management-in-react-apps-with-batched-updates/>

使用`useState`或`this.setState`更新组件状态后，组件的某些部分会根据更新重新渲染。更重要的是，如果您有各种调用来更新像`onClick`这样的 React 事件处理程序中的状态，React 会批量更新，而不是一次更新一个，从而减少组件渲染的次数。

但是，您可能并不总是调用事件处理程序来更新状态，在这些情况下(例如在 Promise 或 SetTimeout 中)，React 会同步更新，而不是成批更新。这意味着您将获得多次重新渲染。考虑下面的例子(或者查看 CodeSandbox 上的[演示):](https://codesandbox.io/s/batched-update-snippet1-4wuk8)

```
import React, { Fragment, useState } from "react";
import ReactDOM from "react-dom";
function Component() {
  const [item1, setItem1] = useState("Initial Item 1");
  const [item2, setItem2] = useState("Initial Item 2");
  console.log("render: ", item1, item2);
  function handleClickWithPromise() {
    Promise.resolve().then(() => {
      setItem1("Updated Item 1");
      setItem2("Updated Item 2");
    });
  }
  function handleClickWithoutPromise() {
    setItem1("Updated Item 1");
    setItem2("Updated Item 2");
  }
  return (
    <Fragment>
      <button onClick={handleClickWithPromise}>
        {item1} - {item2} (with promise)
      </button>
      <button onClick={handleClickWithoutPromise}>
        {item1} - {item2} (without promise)
      </button>
    </Fragment>
  );
}
function App() {
  return <Component />;
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

在这个例子中，我们有两个状态值`item1`和`item2`，当两个按钮中的任何一个被点击时，我们更新它们的值。但是，在第一个按钮中，我们在承诺范围内进行更新。

默认情况下，React 批处理在已知方法(如生命周期方法或事件处理程序)中进行的更新，但是当更新在回调中(如在`SetTimeout`或 Promises 中)时，React 不会这样做。这意味着，如果有多次调用来更新状态，React 会在每次调用时重新呈现组件。

当`Component`第一次渲染时，控制台输出`item1`和`item2`的初始值。

![Initial render](img/ff628d1db70581adee070208c200668e.png)

然后，如果您单击第一个按钮，组件会重新渲染两次，您会看到最初的渲染和随后的两次重新渲染:

![Initial render and additional two re-renders](img/f8b44788548576863be029a7e0718f9e.png)

但是，如果您刷新页面并单击第二个按钮，您会看到初始呈现和仅一次重新呈现，尽管仍然有两次对状态的更新:

![Initial render and only one re-render](img/df22b1cfe2b2569490faf7435cf62ce5.png)

为什么可能呢？因为 React 在已知的方法中自动批量更新，在本例中是一个[事件处理程序](https://blog.logrocket.com/a-guide-to-react-onclick-event-handlers-d411943b14dd/)。请随意使用[演示](https://codesandbox.io/s/batched-update-snippet1-4wuk8)亲自测试功能:

当您在一个承诺中包含多个状态调用时，就像上面的例子一样，您可以强制 React 进行批量更新，因此只会导致一次重新呈现。这可以通过包装调用以更新`*ReactDOM.unstable_batchedUpdates()*`中的状态来实现，如下所示:

```
function handleClickWithPromise() {
    Promise.resolve().then(() => {
      ReactDOM.unstable_batchedUpdates(() => {
        setItem1("Updated Item 1");
        setItem2("Updated Item 2");
      });
    });
  }
```

接下来，用上面的片段更新[之前的演示](https://codesandbox.io/s/batched-update-snippet1-4wuk8)上的`handleClickWithPromise()`函数，就像我们目前在这个[沙箱](https://codesandbox.io/s/objective-surf-bs3ky)上的一样。现在，如果你点击第一个按钮，更新将被批量处理，导致只有一个渲染。如果您查看您的控制台，您应该会在单击第一个按钮后看到以下内容:

![](img/453d530c8892df7e7f320e36f2bc6be7.png)

这不像上次我们点击第一个按钮时有两次重新渲染(有承诺)。作为在`*ReactDOM.unstable_batchedUpdates()*`中包装更新状态调用的结果，我们获得了与第二个按钮完全相同的功能(没有承诺)。

正如我前面提到的，如果你使用`*unstable_batchedUpdates()*`，请记住这是一个实现细节。React 的未来版本可能会将此作为默认行为，您不必使用不稳定的 API。

## 该不该用？

这个方法的名字确实让人有点担心在生产中使用是否安全。然而，React 团队已经[鼓励过](https://github.com/facebook/react/issues/16377)(在撰写本文时，仍然如此)在适当的时候使用这个 API。因此，可以肯定地说，虽然“不稳定”，但它足够稳定，可以用于今天的生产。

## 何时使用它

如果您需要多次调用来更新状态，就像上面的例子一样，您可能会发现有一种更好的方法。根据我的经验，在大多数情况下，我看到开发人员进行多次调用来更新状态，这些调用可以很容易地被一次调用所取代。让我们来看一些实例，在这些实例中，您可能会多次调用来更新状态。

```
this.setState({ ...this.state, foo: 42 });
if (condition) {
    this.setState({ ...this.state, isBar: true });
}
```

可以重构上面的代码，用一个调用来更新状态，如下所示:

```
let newState = { this.state, foo: 42 };
if (condition) {
    newState = { ...this.state, isBar: true };
}
this.setState(newState);
```

当然，你正在创建一个全新的变量，这没关系。通常，正如我们前面看到的，React 会自动批量更新某些函数，而不是其他函数。因此，当您试图将调用次数减少到`setState`时，您应该慎重考虑。

另一种需要多次调用来更新状态的情况是:

```
// Increment foo
this.setState({ ...this.state, foo: this.state.foo + 1 });
this.setState({ ...this.state, foo: this.state.foo + 1 });
```

在这种情况下，随后的调用会利用先前调用所产生的更新值。同样，上面的代码可以这样重构:

```
function incrementFooBy(delta) {
    return (previousState, currentProps) => {
        return { ...previousState, foo: previousState.foo + delta };
    };
}
this.setState(incrementFooBy(2));
```

这里，我们使用 currying 来根据先前的状态和预期的更改“编写”更新内容，然后将值传递给`this.setState`。

## 它适用于钩子吗？

这可能是你想问的一个问题，所以让我就此打住，说是的。随着[钩子](https://blog.logrocket.com/frustrations-with-react-hooks/)的引入，我看到了很多这样的案例。考虑下面这个例子:

```
const [value, updateValue] = useState({});
const [anotherValue, updateAnotherValue] = useState({});

updateValue({ content: "Hello" });
updateAnotherValue({ content: "World" });
```

有时当你使用钩子时，你会发现自己在一个单一的功能组件中创建了多个状态对象。如果你这样做，这可能是一个信号，表明你的功能组件违反了单一责任原则，做了不止一件事。如果多个状态对象放在一起有意义，那么您应该将这些值组合成一个状态对象，如下所示:

```
const [allValues, updateAllValues] = useState({});

updateAllValues({firstContent: "Hello", secondContent: "World" });
```

或者将两个状态对象分离成它们自己独立的功能组件，如果它们在一起没有意义的话。如果你不属于上面提到的任何一种情况，那么我认为你应该使用`*unstable_batchedUpdates*`。

我觉得有必要提一下，进行多次调用来更新状态并没有那么糟糕，特别是因为 React 在某些情况下会自动批量更新，而在其他情况下，它并不会真正产生任何性能问题。因此，如果你发现自己需要使用`*unstable_batchedUpdates*`，那么你一定处于非常罕见的情况。

## 会被弃用吗？

根据 Dan Abramov 对 Stackoverflow 问题的回复:

“但是，我们不会在次要版本中删除[`unstable_batchedUpdates`]，因此在 React 17 之前，如果您需要在 React 事件处理程序之外的某些情况下强制批处理，您可以放心地依赖它。”

他对 Github 问题的另一个评论是:

“这是预期的行为，因为我们目前只在已知的反应范围内批量更新(例如，在同步生命周期方法期间，或在事件处理程序期间)。如上所述，您可以使用`unstable_batchedUpdates`来解决这个问题。未来，批处理将在任何地方默认开启。”

截至撰写本文时，官方路线图博客帖子中没有提到任何 React 版本中的`unstable_batchedUpdates`将被弃用，除了 Dan 的评论之外，也没有太多信息更准确地说明 API 何时将被弃用。

## 最后的想法

在这篇文章中，我们仔细研究了批量更新特性，并展示了它如何简化 React 应用程序中的语句管理和呈现过程。提到了这个 featured 目前还不稳定，值得注意的是目前可以使用。首先，只需将状态调用放入传递给`*ReactDOM.unstable_batchedUpdates*`的回调函数中。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)