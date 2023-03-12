# 为什么在生产日志博客中应该谨慎使用引用

> 原文：<https://blog.logrocket.com/why-you-should-use-refs-sparingly-in-production/>

React 引用使您可以在 React 中直接访问 DOM。这是一个很重要的特性，因为在开发 React 应用程序时，您有时需要对 DOM 执行某些操作。在本帖中，我们将仔细研究 React refs，并试图理解为什么在生产级应用中使用它们可能不是一个好主意。

## 用例

使用 ref 属性给了您执行某些操作和直接操作 DOM 的能力。除非万不得已，否则避免使用 refs 是一条通用的经验法则。官方 React 文档仅概述了三种可能的用例，在这些用例中，由于缺乏更好的替代方案，ref 被完全认为是有用的:

*   管理焦点、文本选择或媒体播放
*   触发命令式动画
*   与第三方 DOM 库集成

在第一种情况下，ref 属性使您能够与 DOM API 交互并执行一些操作，比如获取输入元素的值、管理用户焦点以及管理媒体元素。

命令式动画需要访问负责的 DOM 节点来操作目标元素。这就是 ref 属性通过在应用程序的不同部分对选定的元素执行操作来帮助您实现的。

最后，一些库严重依赖 DOM 运行。例如地图、编辑器、图像处理工具等。这些组件需要完全访问 DOM，并且只能通过 React 中的 ref 属性获得这样的访问权限。

## 参考文献如何在发展中发挥作用

在此之前，我们使用`this.ref`对象与 DOM 交互，这个特性现在已经被弃用，取而代之的是 [React 16.3](https://reactjs.org/blog/2018/03/29/react-v-16-3.html) 附带的新`createRef`。使用它，您可以通过调用`React.createRef()`并使用元素上的`ref`属性将一个 React 元素附加到它来创建一个 ref:

```
import React, { Component } from 'react';
class RefDemo extends Component {
  constructor(props) {
    super(props);
    this.nameInput = React.createRef(); 
  }
  render() {
    return (
      <input ref={this.nameInput}/>
    );
  }
}
```

通过上面的实现，我们可以像这样访问刚刚创建的 ref 的 DOM 节点:

```
this.nameInput.current
```

然后可以在该节点上执行后续操作。例如，为了获得输入元素的值，我们将做:

```
import React, { Component } from 'react';
class RefDemo extends Component {
  constructor(props) {
    super(props);
    this.state = { value: "" }
    this.nameInput = React.createRef(); 
  }
  handleSubmit = e => {
    e.preventDefault();
    this.setState({ value: this.nameInput.current.value})
  };
  render() {
    return (
        <form onSubmit={this.handleSubmit} />
          <input type="text" ref={this.nameInput}/>
        </form>
    );
  }
}
```

当您试图关注输入 DOM 节点时，情况也是如此。您需要对前面的代码片段做一个小的更新:

```
import React, { Component } from 'react';
class RefDemo extends Component {
  constructor(props) {
    super(props);
    this.nameInput = React.createRef();
  }
  handleSubmit = () => {
    this.nameInput.current.focus();
  };
  render() {
    return (
          <input type="text" ref={this.nameInput}/>
          <button onClick={this.handleSubmit}>
            focus!
          </button>
    );
  }
}
```

这些是在构建 React 应用程序时使用 refs 的几种方法，但是，根据您的特定用例，可以使用 refs 执行更多操作。就像我之前提到的，refs 可以帮助你做很多事情，包括减少组件中重新渲染的次数。

## 生产中的参考

如果您正在构建一个生产级应用程序，前面的实现将不被推荐。这是因为你不是在构建应用程序`[The React Way](https://reactjs.org/docs/thinking-in-react.html)`。React 要求您通过 props(而不是 refs)在组件之间进行通信。这就是反应，反应。

具有先前功能的生产就绪型 React 应用程序将采用不同的方法。例如，当一个组件像我们在前面的实现中那样托管一个输入元素时，React 将期望您设置一个事件处理程序来跟踪该输入元素的更改。

这样，当用户在输入字段中键入字符时，事件处理程序将触发并用新值更新您的状态。考虑一下这个:

```
import React from 'react';
class RefDemo extends React.Component {
    state = {
      inputValue: ""
    }
    handleChange = (e) => {
      this.setState({
        inputValue: e.target.value
      })
    }
    render() {
        const { inputValue } = this.state
        return (
          <div>
            <input value={inputValue} onChange={this.handleChange}} />
          </div>
       )
  }
}
```

对状态的更改将导致父组件重新呈现自身，以及带有新值的输入元素。从上面的实现中可以注意到，只要输入值发生变化，就会调用处理程序。处理程序然后调用`setState({ })`，这又重新呈现了组件。这就是 React 方式，也是 React 应用程序在生产中的预期实现。

## 为什么应该谨慎使用引用

也就是说，这里还有一些你应该谨慎使用引用的理由。

### 它打破了封装

React 要求您只能通过 props 在组件之间进行通信。引用建议您可以使用 ref 属性与其他组件进行通信。这将使信息到达期望的目的地，但是，您将丢失应用程序中的数据驱动操作，因为 refs 不能确保数据同步。状态不会更新，组件也不会重新呈现。应用程序状态不会跟踪 DOM 中的变化，这当然会破坏封装。

### 它不遵循反应模式

React 有一个特定的思维模式，一种在构建 React 应用程序时思考的方式。它指定您用状态和组件层次结构来控制应用程序 UI 的每一部分。使用 React refs 来传递数据违背了 React 的思维模式。React 按照设计是由状态驱动的。这意味着每个组件都是有状态的。它有不同的状态(想想不同的 UI 表示)，我们可以改变状态来改变 UI 的外观，不一定只是在事件发生时。

### 它鼓励事件驱动的开发

React 是由数据驱动的设计。使用 React refs 鼓励您根据事件而不是数据变化来更新应用程序 UI。使用 React refs，我们可以在事件发生时更新应用程序 UI(用户单击某个东西)。然而，React 更喜欢状态驱动的实现，其中每个组件都是有状态的，并且可以改变状态来修改 UI 的外观。

### 倾向于覆盖 React 提供的 API

React 为某些功能提供了本地 API，如条件 CSS 类、条件渲染等。用 refs 构建 React 应用程序会影响你对 React 应用程序的看法，让你忽略这些原生 API，转而提倡使用 Refs 来实现那些功能。这些特性(React 为其提供了本机 API)不应该通过使用本机 DOM 元素来实现，因为它可以在 React API 中完全处理。也就是说，有些事情不能纯粹在 React 中完成(比如以编程方式聚焦输入)，这正是 refs 应该出现的地方。

## 参考文献的可能替代方案及建议

*   使用状态控制所有应用程序 UI 更新
*   使用 props 处理组件层次关系
*   道具链乱了就用 Redux

## 结论

在这篇文章中，我们讨论了 React 引用以及如何使用它们。最重要的是，我们已经解释了它的缺点。

一般的经验法则是，在考虑裁判之前，要考虑在道具和状态上做你想做的事情的可能性有多大。如果你没有找到一个合理的实现，那么你应该考虑使用 refs。就我个人而言，我只在需要调用 DOM 元素上的特定函数时使用 refs，到目前为止， [`focus()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) 是我的应用程序中最常见的用法。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)