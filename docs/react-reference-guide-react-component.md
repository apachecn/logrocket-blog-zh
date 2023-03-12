# 反应参考指南:反应。组件-日志火箭博客

> 原文：<https://blog.logrocket.com/react-reference-guide-react-component/>

的反应。组件类是创建 React 组件的两种方式之一，另外还有功能组件。的反应。组件 API 提供了更多有助于调整组件行为的特性。

#### *向前跳转:*

* * *

# 组件生命周期

类组件中的组件生命周期可以定义为方法的集合，在组件本身的不同阶段，可以访问这些方法来运行一些代码。

组件生命周期方法可以分为三个阶段:

## 增加

在幕后，React 使用了一个虚拟 DOM，它是浏览器中呈现的 DOM 的内存表示。虚拟 DOM 是 DOM 的副本，可以在不使用任何 DOM APIs 的情况下进行更新。

对虚拟 DOM 的更新告诉 React 将虚拟 DOM 的当前版本与以前的版本进行比较。一旦 React 知道哪些虚拟 DOM 对象已经改变，React 就会使用 [ReactDOM](https://reactjs.org/docs/react-dom.html) 只更新*真实 DOM 中的那些对象。例如:*

```
ReactDOM.render(<App />, domContainer);
```

挂载阶段是创建对应于 React 组件的实例和 DOM 节点并将它们插入 DOM 的过程。

以下是安装阶段可用的生命周期方法，按以下顺序调用:

*   `constructor()`
*   `static getDerivedStateFromProps()`
*   `render()`
*   `componentDidMount()`

## 更新

当一个组件被添加到 DOM 时——也就是安装过程——该组件仍然存储在内存中，以便 React 在状态改变时能够感知。当 React 检测到组件状态或道具的更改并重新渲染它时，可以定义更新阶段。

以下是更新阶段可用的生命周期方法，按以下顺序调用:

*   `static getDerivedStateFromProps()`
*   `shouldComponentUpdate()`
*   `render()`
*   `getSnapshotBeforeUpdate()`
*   `componentDidUpdate()`

## 卸载

卸载阶段是指不再需要的组件被销毁/从 DOM 中删除。这个阶段对于执行清除操作特别有用，例如删除事件侦听器、取消网络请求或清除以前的订阅。

以下是卸载阶段可用的一种生命周期方法:

你可以查看[这个交互式的、可访问的图](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)，它解释了每个生命周期方法何时被调用。

##### ***准备好一探究竟了吗？深入了解 React 的挂单后挂钩。***

生命周期方法

* * *

# 接下来，我们将回顾 React 类组件可用的各种生命周期方法。

`constructor()`

## React 组件的构造函数在挂载之前被调用。

您希望在组件初始化时运行的代码应该放在这里，这些代码可以是初始化本地状态或绑定事件处理程序。

*即使你可以在构造函数中初始化状态，你**也不应该**调用其中的`setState()`函数*。

*在每个构造函数方法中，在任何其他行之前添加`super(props)`行是很重要的，因为它将`this`值赋给了属性*。这是因为，根据定义，`super()`函数指的是父类构造函数，在本例中是`React.Component`。

```
constructor(props) {
  super(props);
  // Don't call this.setState() here!
  this.state = { counter: 0 };
  this.handleClick = this.handleClick.bind(this);
}
```

如果你试图在不调用`super(props)`的情况下访问组件的属性，你会得到一个`undefined`错误。因此，调用`super(props)`允许您访问构造函数中的`this`，这反过来允许您访问构造函数中的`props`。

注意:如果你没有初始化一个状态或者绑定方法，你不需要为你的类组件实现一个构造函数。

> `render()`

* * *

## `render()`方法是组件生命周期中唯一需要的方法。它在组件生命周期的安装和更新阶段被调用。

`render()`方法返回以下任何一项:

使用 JSX 创建的反应元素

*   片段，它允许您重新运行多个元素
*   字符串和数字，在 DOM 中呈现为文本节点
*   React 门户，它提供了将子组件呈现到父组件的 DOM 层次结构之外的 DOM 节点中的方法
*   React 鼓励`render()`方法是纯的，没有副作用——也就是说，当传递相同的输入时，该方法将总是返回相同的输出。*这意味着你**不应该**在`render()`方法中调用`setState()`函数。*

如果您需要修改组件的状态，可以在`componentDidMount()`或其他生命周期中完成。

```
render() {
  // Don't call this.setState() here!
  return (
    <p>Hello</p>
  )
}
```

***更细致地看看 React 的渲染方法。单击此处查看 8 种条件渲染方法。***

更多来自 LogRocket 的精彩文章:

* * *

### `setState()`

* * *

* * *

## `setState()`函数用于更新组件的状态，并通知 React 组件需要用更新后的状态重新呈现。使用`setState()`是更新组件状态的唯一方式。

无论何时使用`setState()`调用更新状态，重要的是要注意这是一个异步请求，而不是一个即时命令。这意味着 React 可能会延迟对组件的状态更新，或者为了提高性能而批量更新多个`setState()`请求。

您可以通过向`setState()`传递一个对象或者传递一个更新函数来使用`setState()`函数，下面将详细解释。

用对象更新状态

### 当您调用`setState()`函数并传递一个对象时，React shallow 将您提供的对象合并到当前状态中——也就是说，如果您有一个现有状态(如下面的代码片段)并调用`this.setState({hasCountStarted: true})`，只有`hasCounterStarted`的值会被修改。

用函数更新状态

```
this.state = {
  counter: 0,
  hasCounterStarted: false
}
```

### 您还可以通过将 updater 函数传递给`setState()`函数来更新状态。更新器函数包含在状态更新发生时对当前`state`和`props`的引用。

当您希望用依赖于当前状态的值来更新状态时，此方法特别有用。请参见下面的示例。

```
(state, props) => stateChange
```

This method is particularly useful when you want to update the state with values that depend on the current state. See the example below.

如上所述，因为`setState()`是一个异步请求，也就是说，更新可能会被延迟，所以读取`this.state`并依赖它的更新值可能会很棘手。确保获得更新状态的一种方法是使用`setState()`函数接受的回调方法。

回调方法是一个可选的第二个参数，一旦`setState()`完成并且组件被重新呈现，该参数将被执行。请参见下面的示例。

The callback method is an optional second parameter that will be executed once `setState()` is completed and the component is re-rendered. See the example below.

***您可以通过批量更新来进一步简化状态管理—看看如何操作。***

`componentDidMount()`

* * *

## 在完成了`render()`方法之后，也就是在组件已经被挂载并插入到 DOM 树中之后，立即调用`componentDidMount()`,并且只调用一次。

它通常是进行外部 API 调用、添加事件侦听器或为组件设置订阅的好地方。

您可以在这个生命周期中安全地调用`setState()`函数。调用`setState()`函数会导致额外的渲染，但这没关系，因为它会在浏览器更新页面之前发生。

如果您在`componentDidMount()`方法中设置了事件侦听器或订阅，请不要忘记在`componentWillUnmount()`方法中删除它们。参见下面的示例，了解如何在`componentDidMount()`获取外部数据并使用该数据更新状态。

If you set up event listeners or subscriptions in the `componentDidMount()` method, don’t forget to remove them in the `componentWillUnmount()` method. See the example below on how to fetch external data in `componentDidMount()` and updating the state with that data.

`componentDidUpdate()`

* * *

## `componentDidUpdate(prevProps, prevState, snapshot)`在组件生命周期的更新阶段被调用。它在组件重新呈现后被调用，通常用于更新 DOM 以响应对`prop`或`state`的更改。

该方法接收以下参数:

`prevProps`–先前的`prop`值

*   `prevState`–先前的`state`值
*   `snapshot`–仅当您的组件在组件生命周期中使用`getSnapshotBeforeUpdate()`方法时，此值才可用
*   也可以在这里调用`setState()`函数，只要它被包装在一个条件中，以检查状态/属性相对于前一个状态/属性的变化。这样做是为了防止渲染的无限循环。

`componentDidUpdate()`方法的一个典型用例是，当您需要在先前和当前状态不相同的情况下，只调用一个 API**。**

 **在组件生命周期中，除了当`shouldComponentUpdate()`方法返回`false`时，`componentDidUpdate()`方法是 ***总是在每次渲染后调用*** 。

```
componentDidUpdate (prevProps, prevState) {
  if (prevState.user.id !== this.state.user.id) {
    // Make your API call here...
  }
}
```

`componentWillUnmount()`

* * *

## 当一个组件将要被销毁并从 DOM 中移除时，调用`componentWillUnmount()`。

这个方法对于取消网络请求、删除事件监听器以及清理可能已经在`componentDidMount()`中设置的订阅非常有用。

```
componentWillUnmount() {
  // Cancel network requests, event listeners or subscriptions here...
  clearInterval(this.timer);
  this.chatService.unsubscribe();
}
```

了解上述生命周期方法如何与 useEffect 挂钩集成。

`shouldComponentUpdate()`

* * *

## `shouldComponentUpdate(nextProps, nextState)`是一种组件生命周期方法，用于确定组件是否应该更新/重新呈现。默认情况下，它返回一个值`true`,因为每当组件的属性或状态改变时，组件都会重新呈现自己。

但是，如果您希望在满足特定条件时只重新渲染*，则可以使方法返回`false`。*

 *它接收两个参数，`nextProps`和`nextState`，这两个参数通过与当前的`prop`和`state`值进行比较来检查组件是否应该更新。

例如，在上面的代码块中，如果`state`中的当前`user.id`在`nextState`中有相同的值，组件将不会被更新/重新呈现。

```
shouldComponentUpdate(nextProps, nextState) {
  if (this.state.user.id === nextState.user.id) {
    return false;
  }
}
```

除了最初的渲染之外，每次渲染*都会调用这个方法，同样重要的是要注意，当内部子组件的状态改变时，返回`false`不会阻止它们重新渲染。*

`componentDidCatch()`

* * *

## React 最近引入了一个叫做错误边界的概念来处理 React 组件中的错误。

错误边界是 React 组件，帮助捕捉子组件树中任何位置的 JavaScript 错误，然后记录错误信息/显示带有错误信息的回退 UI。

这对调试非常有帮助。您可以将错误边界视为 JavaScript `catch`块，但只适用于 React 组件。那么误差边界是如何产生的呢？

如果一个类组件有`componentDidCatch()`或`getDerivedStateFromError()`方法中的一个(或两个都有),它就会成为一个错误边界。

在子组件中出现错误后，调用`componentDidCatch(error, info)`方法。它接收`error`和`info`参数；`error`包含抛出的错误消息，`info`是一个具有`componentStack`属性的对象，包含组件堆栈跟踪信息。

通过将错误边界组件放置在应用程序组件树的顶部来使用它们。例如:

```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  componentDidCatch(error, info) {
    // Once this method is invoked, set the hasError state to be true
    this.setState({hasError: true });
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>Oh no! Something went wrong.</h1>;
    }

    // if not, just render the child component
    return this.props.children; 
  }
}
```

`static getDerivedStateFromProps()`

```
<ErrorBoundary>
  <SomeProvider>
    <App />
  </SomeProvider>
</ErrorBroundary>
```

* * *

## `static getDerivedStateFromProps(props, state)`是在`render()`方法之前调用的组件生命周期方法。它接收`props`和`state`的参数，并返回一个对象来更新`null`的状态或值。

每当组件接收到新的属性时就会调用它，这对于状态依赖于属性随时间变化的情况非常有用。

例如，在上面的代码块中，如果来自`props`的`user.id`与`state`不同，即`state`依赖于对`props`的更改，则`getDerivedStateFromProps()`方法仅返回新状态*。*

```
static getDerivedStateFromProps(props, state) {
  if (props.user.id !== state.user.id) {
    return {
      isNewUser: true,
      user: props.user,
    };
  }

  // Return null to indicate no change to the state.
  return null;
}
```

`static getDerivedStateFromError()`

* * *

## `static getDerivedStateFromError(error)`是一个生命周期方法，在后代组件抛出错误后被调用。它从组件接收错误，然后返回一个值来更新状态。

与`componentDidCatch()`方法一样，它也用于误差边界组件中，可以用来代替`componentDidCatch()`。

`getSnapshotBeforeUpdate()`

```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // Do something with the error here...
    console.error(error)
    // Update state so the next render will show the fallback UI.
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>Oh no! Something went wrong.</h1>;
    }

    // if not, just render the child component
    return this.props.children; 
  }
}
```

* * *

## `getSnapshotBeforeUpdate(prevProps, prevState)`是在 DOM 更新之前调用的生命周期方法。

它允许您在 DOM 发生变化之前获取信息。该方法应该返回快照值或`null`。返回值总是作为参数传递给`componentDidUpdate()`。

`getSnapshotBeforeUpdate()`的一个典型用例是，在 DOM 更新之前，您想要记住页面上特定元素的滚动位置。假设页面上有一个定义了高度的评论区，一旦添加了新的评论，我们会自动向下滚动到新的评论。

下面的 CodeSandbox 演示了如何使用`getSnapshotBeforeUpdate()`来做到这一点。

The CodeSandbox below demonstrates how to do that using `getSnapshotBeforeUpdate()`.

***你也可以用钩子实现 getSnapshotBeforeUpdate()—是的，真的。在这里阅读。***

##### 类别属性

这些是 React 类组件可用的属性。

* * *

# `props`

`props`是 properties 的简称。它们用于在 React 组件之间传递数据。在 React 类组件中，`props`可以通过使用`this.props`来访问。

## `state`

React 组件中的`state`包含特定于该组件的数据，这些数据可能会随时间而变化。它是用户定义的，并且总是一个普通的 JavaScript 对象。

## React 组件中的状态可以通过使用`this.state`来访问，该状态的值可以通过使用`setState()`函数来修改。需要注意的是，永远不要使用`this.state`直接修改状态；而是用`setState()`代替。

`defaultProps`

`defaultProps`是类组件的一个属性，允许您为类设置默认的`props`。当预期的`props`未通过时，使用`defaultProps`值。

## 因此，如果在没有指定`props.color`的情况下在其他地方使用了`<CustomButton />`组件，它将退回到被定义为`defaultProps`的值。

`displayName`

```
class CustomButton extends React.Component {
  // ...
}

// Set default props outside the class component
CustomButton.defaultProps = {
  color: 'blue'
};
```

`displayName`属性是一个字符串，可以用于调试 React 组件。默认情况下，它是定义组件的函数或类的名称。但是，如果您想显示不同的名称，可以显式设置它。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

## .

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

The LogRocket Redux middleware package adds an extra layer of visibility into your user sessions. LogRocket logs all actions and state from your Redux stores.

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).*****