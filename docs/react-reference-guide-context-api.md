# React 参考指南:上下文 API 

> 原文：<https://blog.logrocket.com/react-reference-guide-context-api/>

上下文是 React 处理多个组件之间共享数据的方式。

#### *向前跳转:*

* * *

## 为什么你可能需要上下文

当在 React 应用程序中使用两个连接的组件时，在它们之间共享数据的最简单方法是通过 props。例如，如果子组件需要来自父组件的数据，它可以通过 props 接收数据。

```
const HomePage = ({ person }) => {
  // use person data from props here
  return (
    <div>
      {person.firstName}
      {person.lastName}
    </div>
  )
}

const ProfilePage = () => {
  const user = {
    firstName: 'Jane',
    lastName: 'Doe'
  }
  return (
    // call HomePage component and pass person as it's props
    <HomePage person={user}/>
  )
}
```

但是，如果在不同的嵌套层次上有两个以上的组件，那么在不相连的组件之间共享数据就变得更加困难。

例如，如果我们想要在顶级组件和第三级组件之间共享数据，我们必须将数据作为道具传递给第二级组件，即使我们不需要那里的数据。

这样，第二级组件就可以将数据传递给第三级组件:

```
const HomePage = ({ person }) => {
  // HomePage receives "person" as a prop for the
  // SettingsPage
  return (
    <SettingsPage person={person} />
  )
}
const SettingsPage = ({ person }) => {
  return (
    <div>
      {person.firstName}
      {person.lastName}
    </div>
  )
}
const ProfilePage = () => {
  const user = {
    firstName: 'Jane',
    lastName: 'Doe'
  }
  return (
    // Even though HomePage does not need the person prop, it still
    // has to be called with it for the SettingsPage component
    <HomePage person={user}/>
  )
}
```

Context 通过提供一个全局状态解决了这个问题，每个组件都可以很容易地访问这个全局状态，而不需要通过 props 将数据从一个父组件传递到多个子组件。

##### ***用 React 和 TypeScript 探索避免道具钻取的其他方法。***

应用程序接口

* * *

## `React.createContext`

### `createContext`方法用于创建组件可以订阅的上下文对象。这些组件能够从树中位于它们之上的最接近的匹配提供者处获得上下文值。

组件通常被包装在提供者中，以便获得它们的上下文值。然而，当树中的组件之上没有匹配的提供者时，组件将从`createContext`方法中的默认参数`{ color: 'black' }`获得其上下文。这在隔离测试组件时尤其有用。

```
const NewContext = React.createContext({ color: 'black' });
```

`Context.Provider`

* * *

### 调用`React.createContext`方法时，它将返回一个提供者组件。提供者是来自上下文对象的反应组件，允许其他组件访问那些上下文值并订阅它们的更改。

提供者组件接受一个`value` prop，然后它的消费子组件可以访问它。一个提供者可以有多个子组件或消费者。

```
const { Provider } = NewContext;
```

当组件没有匹配的父提供者时，它们将使用来自`createContext`方法的默认上下文值。然而，一旦提供者可用，即使它的`value`属性是`undefined`，它的子组件或消费者也会使用它的值。

```
<Provider value={{color: 'blue'}}>
  {children}
</Provider>
```

每当一个提供者的`value`属性改变时，其订阅的消费者将重新呈现。

`Context.Consumer`

* * *

### `React.createContext`方法在被调用时也返回一个消费者组件。消费者是从提供者那里订阅上下文变化的反应组件。

`Context.Consumer`使订阅功能组件中的上下文成为可能。

```
const { Consumer } = NewContext;
```

`Context.displayName`

```
<Consumer>
  {value => <span>{value}</span>}}
</Consumer>
```

* * *

### `Context.displayName`是来自`React.createContext`方法调用的字符串属性。React DevTools 将使用给予上下文的任何`displayName`来决定为该上下文显示什么。

当在 React DevTools 中查看`NewContext`时，其名称应该显示为`NameOfContext`:

```
NewContext.displayName = 'NameOfContext'
```

`Class.contextType`

```
<NewContext.Provider> // Shows up as NameOfContext.Provider
<NewContext.Consumer> // Shows up as NameOfContext.Consumer
```

* * *

### 属性允许组件使用分配给它的上下文对象的最近值。

注意,`class.contextType`属性将允许您只订阅一个上下文。在上面的例子中，`this.context`在`render()`方法中被引用。它也可以在所有其他生命周期方法中引用，包括`componentDidMount()`、`componentDidUpdate()`和`componentWillUnmount()`。

```
class newComponent extends React.Component {
  render() {
    // use the context value assigned to the class.ContextType property
    {this.context}
  }
}
newComponent.contextType = NewContext;
```

警告

* * *

## 需要注意的是，有了上下文，组件失去了很多独立性。因此，重用依赖于上下文的组件更加困难。有了这个缺点，有时使用 component [composition](https://reactjs.org/docs/composition-vs-inheritance.html) 可能会更容易，特别是当您使用 Context 的唯一原因是避免正确的钻取时。


过度使用上下文往往弊大于利。在我们的教程中了解原因。

##### 上下文还使用引用标识来确定何时重新渲染。因此，在某些情况下，当提供者的父级重新呈现时，消费者中可能会触发无意的呈现。这里有一个例子:

在上面的代码片段中，总会有一个新的对象用于`value`，因此，每次提供者重新呈现时，所有的消费者都会重新呈现。要解决这个问题，您必须将值放入父级的状态中，并在 Provider 组件中引用它:

```
class newComponent extends React.Component {
  render() {
    return (
      <NewContext.Provider value={{color: 'blue'}}>
        <ProfilePage />
      </NewContext.Provider>
    )
  }
}
```

这里有一个现场演示，演示了如何用上下文处理动态值。

```
class newComponent extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      value: { color: 'blue' }
    }
  }
  render() {
    return (
      <NewContext.Provider value={{this.state.value}}>
        <ProfilePage />
      </NewContext.Provider>
    )
  }
}
```

Here’s a live demo that illustrates how to handle dynamic values with Context.

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

## 调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).