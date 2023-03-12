# 在生命周期方法中使用 React 的 useEffect 钩子

> 原文：<https://blog.logrocket.com/using-react-useeffect-hook-lifecycle-methods/>

***编者按:**本帖更新于 2022 年 3 月 17 日，更新任何过时的信息，以及用`useEffect` 部分* *更新功能组件中使用`componentDidMount`的[，用`shouldComponentUpdate``componentDidUpdate`](#using-componentdidupdate-useeffect)部分更新[更新阶段。](#updating-phase-shouldcomponentupdate-componentdidupdate)*

React 给我们带来了一些不同的概念，比如虚拟 DOM。它还介绍了不同的库和解决开发中可能遇到的问题的新方法。

因此，我们总是在改进我们的代码，以创建更易维护和编写更好的应用程序。但是，React 最重要的特性之一(内置于 React 核心本身)是 React 组件的生命周期和生命周期方法。

因此，在本帖中，我们将讨论:

## 组件生命周期和生命周期方法

React 向我们介绍的最重要的特性之一是虚拟 DOM，它允许我们通过使用虚拟 DOM 来比较实际的 DOM，从而轻松地替换 DOM 中的特定元素。

更新我们的组件是我们应用程序的重要组成部分。向用户展示我们更新的内容是至关重要的，我们应该总是知道在交互之后会向用户展示什么。

在 React 中，我们有一个称为 React 组件生命周期的东西，它是 React 组件阶段的集合，我们将深入研究它

### 安装阶段用`componentDidMount`

这是 React 组件生命周期的第一个阶段，在这个阶段，组件被创建并插入到 DOM 中。在这个生命周期阶段，我们有`componentDidMount`生命周期方法，并在组件挂载时执行:

```
componentDidMount() {
  console.log("The component has mounted successfully!");
  this.setState({
    loaded: true
  })
}

```

`componentDidMount`允许我们使用`setState`,因此我们可以在生命周期方法中轻松地设置和修改我们的状态。这个生命周期方法执行 API 调用，调用远程端点，并检索数据。

在这个阶段，`render`方法将组件呈现到 DOM 中，并且是唯一需要的方法。

### 用`shouldComponentUpdate`和`componentDidUpdate`更新阶段

这个更新阶段发生在组件挂载并呈现到 DOM 中之后。当我们的道具或状态有更新时，React 组件就会更新。

我们有一些生命周期方法可以在这个特定的生命周期中使用，比如`shouldComponentUpdate`和`componentDidUpdate`。

生命周期方法非常简单；我们只需返回一个布尔值来确定 React 是否应该更新组件。该方法的默认值为`true`:

```
shouldComponentUpdate() {
  return true;
}

```

当组件将要更新/重新呈现时，`shouldComponentUpdate`由 React 调用。这个方法告诉 React 组件是更新还是跳过。

所以，当 React 在要更新的组件上调用`shouldComponentUpdate`方法时，React 会检查`shouldComponentUpdate`调用结果。如果结果是`true`，React 将继续更新组件。如果结果是`false`，React 跳过更新组件:

```
const instance = new HelloComponent()
// HelloComponent is to be updated.
// React will shouldComponentUpdate method.
const isToRender = instance.shouldComponentUpdate()
if(isToRender) {
  // if the 'isToRender' is true, then the HelloComponent is updated.
  instance.render()
}

```

我们还可以使用 React 钩子在功能组件中模拟`shouldComponentUpdate`方法。使用`useMemo`将`shouldComponentUpdate`方法添加到功能组件中:

```
function Parent({a, b}) {
  const HelloWorld = useMemo(() => <HelloComponent a={a} />, [a])
  return {HelloWorld}
}

```

这里，`useMemo`钩子接受两个参数:一个函数和一个依赖数组。

当`useMemo`中的任何依赖关系改变时，调用函数参数。所以，看看我们上面的代码，`HelloComponent`只有在`a`改变了它之前的值时才会呈现。

组件更新后，调用`componentDidUpdate`生命周期方法。此生命周期方法比较特定道具或状态是否发生变化:

```
componentDidUpdate(prevProps) {
  if (this.props.name !== prevProps.name) {
    console.log("Name has changed!");
  }
}

```

### 使用`componentWillUnmount`的卸载阶段

这个生命周期负责清理我们的 DOM，特别是当我们想从 DOM 中删除一个组件时；在 React 中，这被称为卸载。

对于这个生命周期阶段，我们只有一个生命周期方法，叫做`componentWillUnmount`。当一个组件将要从 DOM 中删除时，调用这个生命周期方法:

```
componentWillUnmount() {
  console.log("Component unmounted!");
}

```

## 不推荐的 React 生命周期方法

在 [React 版本 16.3.0](https://reactjs.org/blog/2018/03/29/react-v-16-3.html) 中，一些生命周期方法被弃用:

*   `componentWillMount`
*   `componentWillReceiveProps`
*   `componentWillUpdate`

这些生命周期方法被否决的一个主要原因是，当 React 实现异步渲染时，这些生命周期方法之一的不正确使用可能会导致大的错误，鼓励不安全的编码实践，并且在某些情况下，导致内存泄漏。

如果您仍然在实际应用中使用这些生命周期方法中的一种，并计划升级到最新的 React 版本，您应该知道在 [React 17.0](https://reactjs.org/blog/2019/08/08/react-v16.9.0.html) 版本中它们被完全移除了。

虽然您以前可能在应用程序中使用过这些生命周期方法中的一种，并且它们在许多情况下确实很有帮助和有用，但是您可能已经注意到所有这些生命周期方法都是在类组件中使用的。

查看[这篇解释了这些生命周期方法如何在类组件](https://blog.logrocket.com/the-new-react-lifecycle-methods-in-plain-approachable-language-61a2105859f3/)中工作的文章。现在我们有了 React 钩子，我们可以在功能组件中处理和管理我们的状态数据，那么我们如何在功能组件中使用生命周期方法呢？

为了管理我们在功能组件中的副作用，我们有了`[useEffect](https://blog.logrocket.com/guide-to-react-useeffect-hook/)` [钩子](https://blog.logrocket.com/guide-to-react-useeffect-hook/)，这个钩子可以帮助我们在功能组件中使用生命周期方法。

让我们更多地了解一下`useEffect`钩子，看看我们如何在我们的功能组件中应用生命周期方法。

## 使用`useEffect`在功能组件中应用生命周期方法

在类组件中，我们有生命周期方法来在组件的特定生命周期阶段执行操作。为了让我们做类似的事情，并在我们的功能组件中执行副作用，React 团队创建了`useEffect`钩子。

为了理解`useEffect`钩子是如何工作的，让我们首先从 React 导入它:

```
import { useEffect } from "react";

```

现在，在我们的组件中，我们调用`useEffect`钩子:

```
useEffect(() => {
  // Inside this callback function we perform our side effects.
});

```

这里，它接收一个回调函数作为第一个参数；这个回调函数将是我们的“效果”钩子在我们组件的每次渲染后被调用，这就是为什么我们有第二个参数。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

作为第二个参数，`useEffect`钩子接收一个依赖数组。但这意味着什么呢？

在这个数组中，我们可以传递`useEffect`钩子将要观察的依赖项。绕过一系列依赖关系，`useEffect`钩子将只在其中一个依赖关系改变时运行。

所以，想象一下，我们有一个道具叫`username`；如果我们将这个属性作为依赖项传递给`useEffect`钩子，那么只有当`username`属性改变时，它才会运行:

```
useEffect(() => {
  // Pass an array of dependencies and the useEffect hook will only run if one of the dependencies changes.
}, [name]);

```

如果你传递一个空数组给`useEffect`钩子，它在渲染后只会运行一次。在 [React 文档](https://reactjs.org/docs/hooks-effect.html)中，`useEffect`钩子的基本解释如下。

"如果你熟悉 React 类生命周期方法，你可以把`useEffect`钩子看作是`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`的组合."

因为有了`useEffect`钩子，所以表演副作用非常简单。在我们的例子中，我们将执行一些生命周期方法的等价物。

### 在带有`useEffect`的功能组件中使用`componentDidMount`

这就是我们如何使用`useEffect`钩子在功能组件中执行`componentDidMount`的等效操作:

```
 useEffect(() => {
  // Inside this callback function we perform our side effects.
});

```

通过简单地调用`useEffect`钩子并传递回调函数，我们执行了与`componentDidMount`生命周期方法等效的方法。非常容易。

### 使用`componentDidUpdate`和`useEffect`

要使用`useEffect`钩子执行`componentDidUpdate`的等效操作，我们应该这样做:

```
useEffect(() => {
  // Inside this callback function we perform our side effects.
}, [dependency]);

```

就是这样。这几乎和前面的例子一样，但是这次我们把我们的依赖关系数组作为第二个参数传递，在这个数组里面，我们传递我们想要观察的依赖关系。

如果您没有传递任何依赖关系，`useEffect`钩子将仍然作为`componentDidUpdate`生命周期方法工作。

在组件初始安装时，`useEffect`运行回调函数，而不检查`dependency`是否已经改变。

然后，在组件的生命周期中，当状态改变并且组件更新时，检查依赖关系数组以查看自从最近的更新以来它是否改变了。

如果任何依赖关系发生变化，回调函数将会执行。如果所有依赖项都没有改变，则不会执行回调函数:

```
useEffect(() => {
  // side-effects code here...
}, [dependency1, dependency2, dependency3, ...., dependencyN])

```

如果`useEffect`没有依赖关系，那么每当组件更新时，回调函数就会不断地执行:

```
useEffect(() => {
  // side-effects code here...
})

```

### 使用`componentWillUnmount`和`useEffect`

[为了在组件卸载](https://blog.logrocket.com/understanding-react-useeffect-cleanup-function/)后进行清理，我们有一个简单的方法来使用`useEffect`钩子执行`componentWillUnmount`的等效操作。

我们需要做的唯一一件事就是像这样在`useEffect`钩子的回调函数中返回一个函数:

```
useEffect(() => {
  window.addEventListener("mousemove", () => {});
  return () => {
    window.removeEventListener("mousemove", () => {})
  }
}, []);

```

就是这样。这非常简单，我们可以使用`useEffect`钩子来执行副作用，类似于我们在类组件中使用的生命周期方法，代码简洁明了。

既然我们已经有了 React 钩子，就没有必要再使用类组件了。

今天，我们可以轻松地将所有类组件迁移到功能组件，如果生命周期方法是您尚未迁移的原因，现在您可以安全地迁移到功能组件，并在您的应用程序中使用 React 钩子。

## 结论

在本文中，我们了解了更多关于 React 组件的生命周期及其工作原理。然后，我们查看了 React 中的生命周期方法，以及如何使用`useEffect`钩子在功能组件中使用生命周期方法。

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