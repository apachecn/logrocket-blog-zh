# 避免 React Hooks 中的常见错误

> 原文：<https://blog.logrocket.com/avoiding-common-mistakes-in-react-hooks/>

尽管 React Hooks 组件已经存在好几年了，但是在使用这些特性时仍然会经常出现一些错误。在本文中，我们将讨论在转换到 React 挂钩时的一些常见失误，以及如何避免它们。

## 副作用导致的代码重复

虽然 React Hooks 组件允许我们实现与其前身相同的功能，但实现的过程却有很大的不同。对于`class`组件，副作用会在不同的组件生命周期中运行。相比之下，React Hooks 会因为组件状态的改变而产生副作用。这有时会导致重复。

以一个`Counter`组件为例，它在状态`count`更新时更新页面标题:

```
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }
  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}

```

你会注意到`componentDidMount`和`componentDidUpdate`有相同的代码。这是因为当组件被添加到 DOM 时，以及在所有后续状态更新时，代码都必须运行。这种重复的发生是因为我们必须在组件生命周期的不同阶段运行相同的副作用。即使代码被提取到一个方法中，该方法仍然要被调用两次，这无助于避免重复。

由于 React 挂钩会因状态改变而产生副作用，因此可以避免复制问题，如下所示:

```
import React, { useState, useEffect } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}

```

由于默认情况下`useEffect`在每次渲染和后续更新后运行，它解决了复制的问题。

您还可以使用`useState`对特定状态变量的状态变化做出反应(没有双关的意思):

```
function Counter() {
  const [count, setCount] = useState(0)

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  }, [count]);

  ...
}

```

在这种情况下,“效果”在状态`count`改变时运行。这使得状态和副作用的逻辑紧密联系在一起，更容易阅读和理解。

## 用`useEffect`处理动作

尽管`useEffect`钩子很有用，但有时它会变得“太有用”,从而导致代码过于复杂。这可以从下面的`TodoList`组件中看出:

```
function TodoList({ onSuccess }) {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [todos, setTodos] = useState(null);

  const fetchTodos = () => {
    setLoading(true);
    callApi()
      .then((res) => setTodos(res))
      .catch((err) => setError(err))
      .finally(() => setLoading(false));
  };

  useEffect(() => {
    fetchTodos();
  }, []);

  useEffect(() => {
    if (!loading && !error && todos) {
      onSuccess();
    }
  }, [loading, error, todos, onSuccess]);

  return <div>{todos.map(todo => <Todo item={todo} /> )}</div>;
}

```

有两个`useEffect`钩子:一个在初始渲染时运行，第二个在`loading`和`error`为假但`todos`已被填充时运行(换句话说，当 API 调用成功并调用作为`prop`传递的方法`onSuccess`)。这是一个过度工程化的例子，实际上会使代码变得复杂。

因为我们只想在 API 调用成功时运行`onSuccess`，所以可以在`.then`处理程序和组件中调用它，如下所示:

```
function TodoList({ onSuccess }) {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [todos, setTodos] = useState(null);

  const fetchTodos = () => {
    setLoading(true);
    callApi()
      .then((res) => {
         setTodos(res);
         onSuccess();
      })
      .catch((err) => setError(err))
      .finally(() => setLoading(false));
  };

  useEffect(() => {
    fetchTodos();
  }, []);

  return <div>{todos.map(todo => <Todo item={todo} /> )}</div>;
}

```

为了限制代码的复杂性，避免用`useEffect`处理动作。

## 不需要重新渲染时使用`useState`

任何 React 组件的关键元素之一是它驱动数据流时的状态，以及由此产生的对用户界面的更新。每次状态改变都会触发组件及其子组件的重新呈现。为了避免性能问题，只有在绝对必要时才使用`useState`。

为了理解这一点，让我们看一个简单的组件，它有两个按钮——一个更新状态`count`,另一个使用状态`count`进行 API 调用:

```
function ClickButton(props) {
  const [count, setCount] = useState(0);

  const onClickCount = () => {
    setCount((c) => c + 1);
  };

  const onClickRequest = () => {
    apiCall(count);
  };

  return (
    <div>
      <button onClick={onClickCount}>Counter</button>
      <button onClick={onClickRequest}>Submit</button>
    </div>
  );
}

```

在本例中，您将看到状态从未在`render`方法中使用，这意味着每次状态改变时都会导致不必要的渲染。在这种情况下，使用`useRef`钩子会更合适，因为它在渲染之间保留了它的值，也不会导致重新渲染。

组件应该这样写:

```
function ClickButton(props) {
  const count = useRef(0);

  const onClickCount = () => {
    count.current++;
  };

  const onClickRequest = () => {
    apiCall(count.current);
  };

  return (
    <div>
      <button onClick={onClickCount}>Counter</button>
      <button onClick={onClickRequest}>Submit</button>
    </div>
  );
}

```

为了避免不必要的重新渲染，避免在状态不更新用户界面时使用`useState`。

## 使用`onClick`触发导航

这个问题是 web 开发中的一个普遍的不良实践，并不是 React Hooks 所特有的。假设我们有一个链接到另一个页面的按钮:

```
function ClickButton(props) {
  const history = useHistory();

  const onClick = () => {
    history.push('/next-page');
  };

  return <button onClick={onClick}>Go to next page</button>;
}

```

在按钮上贴一个`onClick`监听器并使用`useHistory`导航到下一页可能很诱人，因为这是简单的客户端导航…对吗？不对。

这个场景中的第一个问题是这个按钮没有被检测为链接，这使得屏幕阅读器几乎不可能检测到它。此外，将鼠标悬停在按钮上不会在屏幕底部显示后续链接，尽管这是一个 UX 提示，许多用户已经开始将它与链接联系起来。

为了避免给用户造成混乱，应该总是使用`<Link />`来触发导航，如下所示:

```
function ClickButton(props) {
  return (
    <Link to="/next-page">
      <span>Go to next page</span>
    </Link>
  );
}

```

## 重写 React 钩子的测试

当一个类组件被转换成带有钩子的函数组件时，重写测试就成了一个问题。理解测试是否需要重写取决于测试是否依赖于组件的实现细节。下面是一个示例测试:

```
test('updateCount updates the count state', () => {
  // using enzyme
  const wrapper = mount(<Counter initialCount="0" />)
  expect(wrapper.state('count')).toBe(0)
  wrapper.instance().updateCount(1)
  expect(wrapper.state('count')).toBe(1)
})

```

如果组件是用钩子编写的，那么测试将会中断，因为它依赖于特定于类组件的属性(即`.state`)。

更好的方法是从用户的角度进行测试，因为用户不关心组件的实现细节。在这种情况下，重写测试将如下所示:

```
import { render, screen } from '@testing-library/react';

test('updateCount updates the count state', () => {
  // using React Testing Library
  render(<Counter initialCount="0" />)
  expect(screen.getByText("Initial count: 0")).toBeInTheDocument()

  userEvent.click(screen.getByText("Update count"))
  expect(screen.getByText("Initial count: 1")).toBeInTheDocument()
})

```

使用这种方法，测试不会因为实现细节的变化而中断。

## 结论

在本文中，我们已经了解了在转换到 React 钩子时出现的一些常见错误。我们还了解了如何避免这些错误以及应该遵循的最佳实践。点击了解更多关于 React 钩子[的信息。](https://reactjs.org/docs/hooks-intro.html)

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