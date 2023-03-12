# 过度使用 React 上下文日志的陷阱

> 原文：<https://blog.logrocket.com/pitfalls-of-overusing-react-context/>

在很大程度上，反应和状态是齐头并进的。随着 React 应用的增长，管理状态变得越来越重要。

随着 [React 16.8 和钩子](https://blog.logrocket.com/introducing-react-16-8-featuring-official-support-for-hooks/)的引入，React 上下文 API 有了显著的改进。现在我们可以把它和钩子结合起来模仿`react-redux`；有些人甚至用它来管理他们的整个应用程序状态。然而，React 上下文有一些缺陷，过度使用它会导致性能问题。

在本教程中，我们将回顾过度使用 React 上下文的潜在后果，并讨论如何在下一个 React 项目中有效地使用它。

### 什么是反应上下文？

React Context 提供了一种在应用程序中共享数据(状态)的方式，而无需传递每个组件的属性。它使您能够通过提供者和消费者使用上下文中保存的数据，而无需进行适当的演练。

```
const CounterContext = React.createContext();

const CounterProvider = ({ children }) => {
  const [count, setCount] = React.useState(0);

  const increment = () => setCount(counter => counter + 1);
  const decrement = () => setCount(counter => counter - 1);
  return (
    <CounterContext.Provider value={{ count, increment, decrement }}>
      {children}
    </CounterContext.Provider>
  );
};

const IncrementCounter = () => {
  const { increment } = React.useContext(CounterContext);
  return <button onClick={increment}> Increment</button>;
};

const DecrementCounter = () => {
  const { decrement } = React.useContext(CounterContext);
  return <button onClick={decrement}> Decrement</button>;
};

const ShowResult = () => {
  const { count } = React.useContext(CounterContext);
  return <h1>{count}</h1>;
};

const App = () => (
  <CounterProvider>
    <ShowResult />
    <IncrementCounter />
    <DecrementCounter />
  </CounterProvider>
);

```

注意，我有意将`IncrementCounter`和`DecrementCounter`分成两个部分。这将帮助我更清楚地演示与 React 上下文相关的问题。

如你所见，我们有一个非常简单的上下文。它包含两个函数，`increment`和`decrement`，处理计数器的计算和结果。然后，我们从每个组件中提取数据，并显示在`App`组件上。没什么特别的，只是你典型的 React 应用。

![Gordon Ramsay Asking, "What Is the Problem?"](img/59d818204f42ec790cc79c1aa82bfd85.png)

从这个角度来看，您可能想知道使用 React 上下文有什么问题？对于这样一个简单的应用程序，管理状态很容易。然而，随着您的应用程序变得越来越复杂，React Context 可能很快成为开发人员的噩梦。

### 使用 React 上下文的利与弊

尽管 React Context 实现起来很简单，对于某些类型的应用程序来说也很棒，但它是以这样一种方式构建的，即每次上下文的值发生变化时，组件使用者都会重新呈现。

到目前为止，对于我们的应用程序来说，这还不是一个问题，因为如果组件在上下文的值改变时不重新呈现，它将永远不会获得更新的值。然而，重新呈现将不限于组件消费者；将重新呈现与上下文相关的所有组件。

要查看它的运行情况，让我们更新我们的示例。

```
const CounterContext = React.createContext();

const CounterProvider = ({ children }) => {
  const [count, setCount] = React.useState(0);
  const [hello, setHello] = React.useState("Hello world");

  const increment = () => setCount(counter => counter + 1);
  const decrement = () => setCount(counter => counter - 1);

  const value = {
    count,
    increment,
    decrement,
    hello
  };

  return (
    <CounterContext.Provider value={value}>{children}</CounterContext.Provider>
  );
};

const SayHello = () => {
  const { hello } = React.useContext(CounterContext);
  console.log("[SayHello] is running");
  return <h1>{hello}</h1>;
};

const IncrementCounter = () => {
  const { increment } = React.useContext(CounterContext);
  console.log("[IncrementCounter] is running");
  return <button onClick={increment}> Increment</button>;
};

const DecrementCounter = () => {
  console.log("[DecrementCounter] is running");
  const { decrement } = React.useContext(CounterContext);
  return <button onClick={decrement}> Decrement</button>;
};

const ShowResult = () => {
  console.log("[ShowResult] is running");
  const { count } = React.useContext(CounterContext);
  return <h1>{count}</h1>;
};

const App = () => (
  <CounterProvider>
    <SayHello />
    <ShowResult />
    <IncrementCounter />
    <DecrementCounter />
  </CounterProvider>
);

```

我添加了一个新组件`SayHello`，它显示来自上下文的消息。每当这些组件渲染或重新渲染时，我们还会记录一条消息。这样，我们可以看到变更是否影响了所有组件。

```
// Result of the console
 [SayHello] is running
 [ShowResult] is running
 [IncrementCounter] is running
 [DecrementCounter] is running

```

当页面完成加载时，所有消息都将出现在控制台上。目前还没什么可担心的。

让我们点击`increment`按钮，看看会发生什么。

```
// Result of the console
 [SayHello] is running
 [ShowResult] is running
 [IncrementCounter] is running
 [DecrementCounter] is running

```

如您所见，所有组件都重新呈现。点击`decrement`按钮具有相同的效果。每当上下文的值改变时，所有组件的消费者都将重新呈现。

你可能还在想，谁在乎呢？React 上下文不就是这样工作的吗？

![Joey From "Friends" Shrugging](img/abed578413ebd0c69b2d7dcb48c32feb.png)

对于这么微小的 app，我们完全不用担心使用 React Context 带来的负面影响。但是在一个状态频繁变化的大型项目中，这个工具产生的问题比它帮助解决的问题还要多。一个简单的更改会导致无数次重新渲染，最终会导致严重的性能问题。

那么我们如何避免这种降低性能的重新渲染呢？

### 用`useMemo()`防止重新渲染

也许记忆是解决我们问题的方法。让我们用`useMemo`更新我们的代码，看看记忆我们的值是否能帮助我们避免重新呈现。

```
const CounterContext = React.createContext();

const CounterProvider = ({ children }) => {
  const [count, setCount] = React.useState(0);
  const [hello, sayHello] = React.useState("Hello world");

  const increment = () => setCount(counter => counter + 1);
  const decrement = () => setCount(counter => counter - 1);

  const value = React.useMemo(
    () => ({
      count,
      increment,
      decrement,
      hello
    }),
    [count, hello]
  );

  return (
    <CounterContext.Provider value={value}>{children}</CounterContext.Provider>
  );
};

const SayHello = () => {
  const { hello } = React.useContext(CounterContext);
  console.log("[SayHello] is running");
  return <h1>{hello}</h1>;
};

const IncrementCounter = () => {
  const { increment } = React.useContext(CounterContext);
  console.log("[IncrementCounter] is running");
  return <button onClick={increment}> Increment</button>;
};

const DecrementCounter = () => {
  console.log("[DecrementCounter] is running");
  const { decrement } = React.useContext(CounterContext);
  return <button onClick={decrement}> Decrement</button>;
};

const ShowResult = () => {
  console.log("[ShowResult] is running");
  const { count } = React.useContext(CounterContext);
  return <h1>{count}</h1>;
};

const App = () => (
  <CounterProvider>
    <SayHello />
    <ShowResult />
    <IncrementCounter />
    <DecrementCounter />
  </CounterProvider>
);

```

现在让我们再次点击`increment`按钮，看看它是否工作。

```
<// Result of the console
 [SayHello] is running
 [ShowResult] is running
 [IncrementCounter] is running
 [DecrementCounter] is running

```

不幸的是，我们仍然遇到同样的问题。每当我们的上下文的值改变时，所有组件的消费者都被重新呈现。

![Michael Scott Making a Sad Face](img/e79cb09ea2a906ae81082e66e72383c8.png)

如果记忆不能解决问题，我们是否应该停止用反应上下文来管理我们的状态？

### 应该使用 React 上下文吗？

在开始项目之前，您应该确定如何管理您的状态。有无数可用的解决方案，其中只有一个是 React Context。要确定哪种工具最适合您的应用，问自己两个问题:

1.  你应该什么时候使用它？
2.  你打算如何使用它？

如果您的状态频繁更新，React Context 可能不如像 [React Redux](https://blog.logrocket.com/when-and-when-not-to-use-redux-41807f29a7fb/) 这样的工具有效或高效。但是，如果您的静态数据更新频率较低，比如首选语言、时间更改、位置更改和用户身份验证，那么使用 React 上下文传递 props 可能是最好的选择。

如果您确实选择使用 React 上下文，请尽可能地将您的大上下文拆分成多个上下文，并使您的状态靠近其组件消费者。这将帮助您最大化 React Context 的特性和功能，对于简单的应用程序来说，React Context 在某些情况下可能非常强大。

那么，[应该使用 React 上下文](https://blog.logrocket.com/use-hooks-and-context-not-react-and-redux/)吗？答案取决于何时和如何。

### 最后的想法

React Context 对于状态变化不频繁的简单应用程序来说是一个优秀的 API，但是如果您在更复杂的项目中过度使用它，它会很快变成开发人员的噩梦。了解该工具在构建高性能应用程序时是如何工作的，可以帮助您确定它对于管理项目中的状态是否有用。尽管 React Context 在处理高频率的状态变化时有其局限性，但如果使用得当，它是一个非常强大的状态管理解决方案。

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