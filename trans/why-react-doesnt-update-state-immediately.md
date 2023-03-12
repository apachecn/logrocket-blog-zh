# 为什么 React 不立即更新状态

> 原文：<https://blog.logrocket.com/why-react-doesnt-update-state-immediately/>

[Chiamaka Umeh Follow](https://blog.logrocket.com/author/chiamakaumeh/) A frontend developer with a passion for designing highly-responsive user interfaces for JavaScript-based web and mobile apps using React and React Native.

# 为什么 React 不立即更新状态

## 

2022 年 1 月 12 日 2 分钟阅读 707

尽管 React 很受欢迎，但它最大的缺点之一是组件的过度渲染。在开发 React 应用程序时，您可能已经注意到，状态更新在被更改后不会立即反映新的值。React state 是一个普通的 JavaScript 对象，它保存影响渲染输出的信息。

在构建您的项目时，如果您打算在将来更改 React 组件的任何属性，您应该将该属性存储在一个状态中。该状态以`mount`上的初始默认值开始，随后由于用户的动作而改变。每个 React 组件在内部管理自己的状态。

在本文中，我们将探究 React 不立即更新状态的原因。我们将运行一个示例，并阐明当您需要在类和函数组件中对新状态进行更改时应该做什么。我们开始吧！

## React 如何执行状态更新

为了更新 React 组件中的状态，我们将分别在类和函数组件中使用由`React.useState()`钩子返回的`this.setState`函数或 updater 函数。

React 中的状态更新是异步的；当请求更新时，不能保证会立即进行更新。updater 函数对组件状态的更改进行排队，但是 React 可能会延迟这些更改，在一次传递中更新几个组件。

例如，考虑下面的代码:

```
//React
const handleClick = () => {
      setName("Amaka")
      setAge(20)
      setAddress("No 3 Rodeo drive")
}

```

在上面的代码片段中，有三个不同的调用来更新和重新呈现组件。在大多数情况下，一个接一个地调用 updater 函数并在每次调用后重新呈现父组件和子组件是低效的。因此，React 批处理状态更新。

无论`handleClick`事件处理程序中有多少个`setState()`调用，它们在事件结束时只会产生一次重新呈现，这对于在大型应用程序中保持良好的性能至关重要。更新请求的顺序始终得到尊重；React 总是首先处理第一个更新请求。

既然我们已经确定了[延迟更新请求的协调以便批处理](https://blog.logrocket.com/how-when-to-force-react-component-re-render/)它们是有益的，那么有时您需要等待更新来对更新后的值做一些事情。在下一节中，我们将看到如何做到这一点。

## 使用类组件执行操作

### `setState()`回调

`setState()`的第二个参数是可选的回调函数。一旦`setState()`完成并且组件被重新渲染，这个参数将被执行。回调函数保证在应用状态更新后运行:

```
//React

handleSearch  = (e) => {
    this.setState({
    searchTerm: e.target.value
  },() => {
    // Do an API call with this.state.searchTerm
  });
}

```

### `componentDidUpdate`

状态更新发生后，立即调用`componentDidUpdate`函数。为了避免无限循环，您应该始终使用条件语句来确保前一状态和当前状态不相同:

```
//React

componentDidUpdate(prevProps, prevState) {
  if (prevState.count !== this.state.count) {
    // Do something here
  }
}

```

## 使用功能组件执行操作

### `useEffect()`挂钩

当状态更新时，您可以在`useEffect`钩子中执行副作用。状态变量可以作为依赖项添加到这个钩子中，使它在状态值改变时运行。您可以让`useEffect`钩子监听状态变化:

```
//React

import React,{useState, useEffect} from 'react';

const App = () => {
  const [count, setCount] = useState(1);

  useEffect(() => {
    if (count > 5) {
      console.log('Count is more that 5');
    } else {
      console.log('Count is less that 5');
    }
  }, [count]);

  const handleClick = () => {
    setCount(count + 1);
  };

  return (
    <div>
      <p>{count}</p>

      <button onClick={handleClick}>
        add
      </button>
    </div>
  );
};

export default App;

```

只有当作为依赖项提供的状态变量改变时，`useEffect`钩子中的回调函数才会运行。

## 结论

在 React 中，每次状态更新都会导致被更新的组件重新呈现。因为重新呈现是一项开销很大的操作，同步进行状态更新会导致严重的性能问题，例如，增加加载时间或导致应用程序崩溃。通过批处理状态更新，React 避免了不必要的重新渲染，提高了整体性能。我希望你喜欢这篇文章！

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