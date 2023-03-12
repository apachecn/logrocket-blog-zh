# 在 React 组件中使用 setState-log rocket 博客

> 原文：<https://blog.logrocket.com/using-setstate-react-components/>

***编者按**:本帖最后更新于 2021 年 7 月 30 日。它可能仍然包含过时的信息。*

与道具一样，React 中的状态是存储数据并影响组件呈现或行为方式的对象。与 props 不同，状态完全在组件中管理，并且可以随着时间的推移而改变。

用户操作、网络活动、API 请求或特定的应用程序行为通常会触发 React 组件的状态变化。

在本文中，我们将回顾:

## React 中的组件状态是什么？

有状态的组件称为有状态组件，而没有状态的组件称为无状态组件。

一个组件可以有一个初始状态集，访问它，并更新它。在下面的代码块中，我们设置组件的初始状态。这是通过构造函数方法完成的:

```
import React, { Component } from 'react';

class Food extends Component {
  constructor(props) {
    super(props)

    this.state = {
      fruits: ['apple', 'orange'],
      count: 0
    }
  }
}
```

因为状态是普通的 JavaScript 对象，`this.state`必须等于一个对象:

```
this.state = {
  fruits: ['apple', 'orange'],
  count: 0
}
```

避免将`state`对象与其他实例属性混淆。很容易假设您可以在构造函数中定义另一个对象，并尝试像使用`state`一样使用它，但是`state`实例是一个特殊的实例，因为 React 管理它:

```
...
//constructor function above

this.state = {
  fruits: ['apple', 'orange'],
  count: 0
}

this.user = {
  name: 'Obaseki Nosa'
}

...
```

虽然`this.state`和`this.user`都是在构造函数中初始化的对象，但是只有`this.state`与`setState()`发生反应，并由 React 管理。

## 如何在 React 中访问组件状态？

我们可以使用`this.state.property_name`像访问其他对象一样访问组件状态。

为了访问上面例子中的`count`,我们；将使用`this.state.count`:

```
render() {
  return (
    <div className = "container">
      <h2> Hello!!!</h2>
      <p> I have {this.state.count} fruit(s)</p>
    </div>
  );
}
```

## 如何在 React 中更新我的组件状态？

尽管从技术上讲，可以从代码中的任何地方写入到`this.state`，但它不会提示重新呈现，这将导致在试图通过`this.state`访问值时出现不稳定和不正确的状态值。

您应该直接写入`this.state`的唯一地方是组件的构造函数方法。

在其他地方使用`setState()`方法；这样做接受一个最终合并到组件现有状态的对象。

例如，以下内容不会重新渲染组件:

```
// Wrong
this.state.name = 'Obaseki Nosa';
```

而是使用`setState()`。

## 我如何在 React 中使用`setState()`？

`setState()`调度改变组件的状态对象，并告诉 React 组件及其子组件必须用更新后的状态重新呈现:

```
// Correct
this.setState({name: 'Obaseki Nosa'});
```

React 有意等待，直到所有组件在它们的事件处理程序中调用`setState()`后再重新呈现。这通过避免不必要的重新呈现来提高性能。

`setState()`可以被认为是请求而不是立即命令来更新组件。这就是为什么试图在`setState()`之后立即使用`this.state`会导致不正确的行为:

```
// Trying to change the value of this.state.count from previous example
this.setState({
  count: 4
});

console.log(this.state.count); // 0
```

`this.state.count`返回`0`,因为即使该值是用`setState()`设置的，在试图用`this.state`使用该值之前，它只是被调度而不是重新呈现。

`setState()`总是导致重新招标，除非`shouldComponentUpdate()`返回`false`。

## 在 React 生命周期方法中使用`setState()`

在 [React 的生命周期方法](https://blog.logrocket.com/react-lifecycle-methods-tutorial-examples/)中调用`setState()`需要一定程度的谨慎。有一些方法调用`setState()`会导致不希望的结果，还有一些方法需要完全避免。让我们来看看几种方法，以及它们在调用`setState()`时的反应

### `render()`

在这里调用`setState()`使得一个组件产生无限循环成为可能。

`render()`函数应该是纯的，这意味着它不修改组件的状态。每次调用它都返回相同的结果，并且它不直接与浏览器交互。

在这种情况下，避免在这里使用`setState()`。

### `constructor()`

不要在`constructor()`中调用`setState()`。相反，如果一个组件需要使用本地状态，直接在构造函数中将初始状态赋给`this.state`。

### `componentDidMount()`

`componentDidMount()`组件挂载后立即调用。您可以在`componentDidMount()`中立即调用`setState()`并触发额外的渲染，但这发生在浏览器更新屏幕之前，调用两次`render()`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### `componentDidUpdate()`

`componentDidUpdate()`更新后立即调用。您可以在这里立即调用`setState()`,但是它必须包含在如下示例所示的条件中，否则会导致无限循环:

```
componentDidUpdate(prevProps, prevState) {
  let newName = 'Obaseki Nosa'
  // Don't forget to compare states
  if (prevState && prevState.name !== newName) {
    this.setState({name: newName});
  }
}

```

### `componentWillUnmount()`

不要在这里调用`setState()`，因为组件不会重新呈现。一旦组件实例被卸载，它就不会再被装载。

## 结论

我们对`setState()`的概述到此结束。使用`setState()`时需要记住的一些事情包括:

1.  `setState()`是 async，这意味着当试图立即访问该值时，不能保证状态已经更新
2.  你只能用`setState`改变`state`,而 React 将对改变做出反应

干杯！！！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)