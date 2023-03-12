# 了解 React Hooks 的常见问题

> 原文：<https://blog.logrocket.com/understanding-common-frustrations-react-hooks/>

***编者按*** : *这篇 React Hooks 的文章最后一次更新是在 2022 年 8 月 4 日，包括了一个关于 React Hooks 中常见错误消息的部分。*

React Hooks 于 2019 年 2 月通过 v16.8 推出。尽管他们的目标是允许用户使用状态和其他 React 特性而无需编写类。虽然这一开始看起来令人兴奋，但随之而来的还有一些挫折和陷阱。

在本文中，我们将回顾 React 钩子的常见问题。我们将讨论 React Hooks 想要解决的问题，类组件有什么问题，以及在 React Hooks 中可能看到的常见错误消息和如何解决它们。我们开始吧！

## React 钩子解决什么问题？

在我详述我对 React Hooks 的失望之前，我想声明一下，在很大程度上，我是 React Hooks 的粉丝。

经常听说钩子存在的主要原因是为了替换类组件。可悲的是，React 官方网站的主标题[介绍钩子](https://reactjs.org/docs/hooks-intro.html)用这样一个不那么大胆的声明来贬低钩子:

> 钩子是 React 16.8 中的新增功能。它们允许您使用状态和其他 React 特性，而无需编写类。

这个解释并没有给我很多使用 React 钩子的动力，除了“类并不酷，伙计！""

在我看来，React 钩子允许我们以一种比以前的模式更优雅的方式解决横切关注点，比如[混合](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)、[高阶组件](https://reactjs.org/docs/higher-order-components.html)和[渲染道具](https://reactjs.org/docs/render-props.html)。日志和认证等功能不是特定于组件的，React 挂钩允许我们将这种可重用的行为附加到组件上。

## 类组件有什么问题？

无状态组件接受一些道具并返回一个 React 元素，这个概念有一些美丽而纯粹的东西。这是一个纯粹的功能，因此，副作用免费。

```
export const Heading: React.FC = ({ level, className, tabIndex, children, ...rest }) => {
  const Tag = `h${level}` as Taggable;

  return (

      {children}

  );
};

```

不幸的是，副作用的缺乏使得这些无状态组件有点受限，最终，某处的某些东西必须操纵状态。如果组件需要在渲染周期之间保持状态，类组件是唯一的选择。这些类组件通常被称为容器组件，它们执行副作用并将道具传递给这些纯粹的无状态组件函数。

对于基于[类的生命周期事件](https://blog.logrocket.com/react-lifecycle-methods-tutorial-examples/)，有几个被很好记录的问题。最大的抱怨之一就是你经常要在`componentDidMount`和`componentDidUpdate`中重复逻辑。

```
async componentDidMount() {
  const response = await get(`/users`);
  this.setState({ users: response.data });
};

async componentDidUpdate(prevProps) {
  if (prevProps.resource !== this.props.resource) {
    const response = await get(`/users`);
    this.setState({ users: response.data });
  }
};

```

如果您使用 React 有一段时间，您将会遇到这个问题。

有了 React 钩子，可以在一个地方使用[效果钩子](https://reactjs.org/docs/hooks-effect.html)处理这个副作用代码。

```
const UsersContainer: React.FC = () => {
  const [ users, setUsers ] = useState([]);
  const [ showDetails, setShowDetails ] = useState(false);

 const fetchUsers = async () => {
   const response = await get('/users');
   setUsers(response.data);
 };

 useEffect( () => {
    fetchUsers(users)
  }, [ users ]
 );

 // etc.

```

挂钩是一个相当大的改进，但是这与我们以前拥有的纯无状态函数相比是一个很大的进步。这让我第一次感到沮丧。

## 又一个需要学习的 JavaScript 范例

声明一下，我是一个 50 岁的 React 粉丝。在用疯狂的观察者和计算属性完成一个 [ember](https://emberjs.com/) 应用程序后，单向数据流将永远在我心中占有一席之地。

`useEffect`和 friends 的问题在于，它在 JavaScript 领域的其他地方都不存在。它是不寻常的，有怪癖，对我来说，精通并消除这些怪癖的唯一方法是在现实世界中使用它，并经历一些痛苦。

没有使用计数器的教程能让我进入心流。我是一名自由职业者，除了 React 之外，我还使用其他框架，这让我感到疲劳。事实上，我需要设置`[eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks)`来保持我在这个特定范例中的正直和狭隘，这让我感到有点警惕。

## 去地狱，带着依赖数组回来

[`useEffect`](https://reactjs.org/docs/hooks-effect.html) 钩子可以接受一个可选的第二个参数，称为 dependencies 数组，它允许您优化 React 何时执行效果回调。React 将通过`[Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)`对每个值进行比较，以确定是否有任何变化。如果任何元素与上一个渲染周期不同，那么效果将根据新值运行。

这种比较适用于原始的 JavaScript 类型，但是如果其中一个元素是对象或数组，就会出现问题。`Object.is`将通过引用来比较对象和数组，没有办法覆盖这个功能并提供一个自定义的比较器。

通过引用检查对象或函数的相等性是一个常见的陷阱，我可以用下面我遇到的一个问题的简化版本来说明这一点:

```
const useFetch = (config: ApiOptions) => { 
 const [data, setData] = useState(null);

 useEffect(() => {
    const { url, skip, take } = config;
    const resource = `${url}?$skip=${skip}&amp;take=${take}`;
    axios({ url: resource }).then(response => setData(response.data));
  }, [config]); // <-- will fetch on each render

  return data;
};

const App: React.FC = () => {
  const data = useFetch({ url: "/users", take: 10, skip: 0 });
  return <div>{data.map(d => <div>{d})}</div>;
};

```

在第 14 行，如果我们不做些什么来确保每次都使用相同的对象，那么在每次渲染时都会有一个新的对象被传递到`useFetch`中。在这种情况下，最好检查该对象的字段，而不是对象引用。

我确实理解为什么 React 没有走深入对象比较的道路。如果不小心，您可能会遇到一些严重的性能问题。我似乎经常重新考虑这个问题，并且有许多针对这个问题的修复。您的对象越动态，您开始添加的工作区就越多。

有一个 [ESLint 插件](https://github.com/facebook/react/tree/master/packages/eslint-plugin-react-hooks)，你真的应该在你选择的文本编辑器中使用[自动修复设置](https://oprea.rocks/blog/automatically-fix-eslint-code-validation-errors-in-visual-studio-code/)来自动应用 ESLint 修复。我确实担心任何需要外部插件来检查正确性的新特性。

事实上，`[use-deep-object-compare](https://github.com/kentcdodds/use-deep-compare-effect)`、`[use-memo-one](https://github.com/alexreardon/use-memo-one)`和其他的存在证明了这是一个足够普遍的问题，或者至少是一个困惑点。

## React 依赖于钩子被调用的顺序

第一批上架的定制 React 钩子是几个使用钩子查询远程 API 的`useFetch`实现。大多数都回避了从事件处理程序调用远程 API 的问题，因为钩子只能从功能组件的开始处调用。

如果我们拥有的数据有分页链接，我们想在用户点击链接时重新运行效果，该怎么办？下面是一个简单的`useFetch`例子:

```
const useFetch = (config: ApiOptions): [User[], boolean] => {
  const [data, setData] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const { skip, take } = config;

    api({ skip, take }).then(response => {
      setData(response);
      setLoading(false);
    });
  }, [config]);

  return [data, loading];
};

const App: React.FC = () => {
  const [currentPage, setCurrentPage] = useState<ApiOptions>({
    take: 10,
    skip: 0
  });

  const [users, loading] = useFetch(currentPage);

  if (loading) {
    return <div>loading....</div>;
  }

  return (
    <>
      {users.map((u: User) => (
        <div>{u.name}</div>
      ))}
      <ul>
        {[...Array(4).keys()].map((n: number) => (
          <li>
            <button onClick={() => console.log('what do we do now?')}>{n + 1}</button>
          </li>
        ))}
      </ul>
    </>
  );
};

```

第一次渲染时会调用一次`useFetch`钩子，代码如下:

```
  <ul>
    {[...Array(4).keys()].map((n: number) => (
      <li>
        <button onClick={() => console.log('what do we do now?')}>{n + 1}</button>
      </li>
    ))}
  </ul>

```

但是我们如何从这些按钮的事件处理程序中调用`useFetch`钩子呢？

React 挂钩的规则明确规定:

> 不要在循环、条件或嵌套函数中调用钩子。相反，总是在 React 函数的顶层使用钩子。

每次组件渲染时，React 挂钩都需要以相同的顺序调用。[反应过度](https://overreacted.io/why-do-hooks-rely-on-call-order/)漂亮地阐明了为什么会这样的几个原因。

你绝对不能这么做:

```
<button onClick={() => useFetch({ skip: n + 1 * 10, take: 10 })}>
  {n + 1}
</button>

```

从事件处理程序调用`useFetch`钩子会破坏钩子的规则，因为你会破坏钩子在每次渲染时被调用的顺序。

## 从钩子返回一个可执行函数

许多钩子现在返回一个可以从顶级声明外部调用的函数。React 挂钩的规则仍然有效，对`/api/user/1`的调用可以从事件处理程序中触发。

下面的代码来自我写的一个名为`[react-abortable-fetch](https://github.com/dagda1/cuttingedge/tree/main/packages/react-abortable-fetch)`的包:

```
const { run, state } = useFetch(`/api/users/1`, { executeOnMount: false });
return (
  <button
    disabled={state !== 'READY'}
    onClick={() => {
      run();
    }}
  >
    DO IT
  </button>
);

```

调用`useFetch`返回一个带有状态属性和`run`函数的对象。`run`函数将实际执行远程查询。

## React 挂钩中的常见错误消息

React 钩子经常抛出一些令人沮丧的错误。让我们回顾一下 React 钩子中的一些常见错误信息，并讨论它们为什么会出现。

### "在回调中不能调用 React 挂钩"

当在嵌套函数中调用 React 挂钩时，会出现此错误。让我们看一个例子:

```
import React, { useState } from "react";

export const WithCounter = (WrappedComponent) => {
  return (props) => {
    const [count, setCount] = useState(0);
    const increment = () => setCount((count) => count + 1);
    return <WrappedComponent count={count} increment={increment} {...props} />;
  };
};

const ClickCounter = (props) => {
  return <button onClick={props.increment}>clicked {props.count} times</button>;
};

export default function App() {
  const Comp = WithCounter(ClickCounter);
  return (
    <div className="App">
      <Comp />
    </div>
  );
}

```

看到我们有一个高阶计算`WithCounter`。在它的回调函数中，注意我们使用了`useState`钩子。这不符合 React Hook 的规则。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

上面的代码会抛出`React Hook "useState" cannot be called inside a callback. React Hooks must be called in a React function component or a custom React Hook function.` 错误。在第 5 行，`const [count, setCount] = useState(0);`，我们在那里调用 React `useState`钩子。即使该函数被用作`App`组件中的组件，React 也会将该组件视为回调函数。

不应在嵌套函数中调用 React 挂钩

### "必须在 React 函数组件或自定义 React 挂钩函数中调用 React 挂钩"

这个错误类似于我们刚刚了解到的上面的错误。React 挂钩必须在 React 函数组件或自定义 React 挂钩内调用。

假设我们有一个 React 组件，`App`:

```
function App() {}

```

我们可以在这里调用任何 React 挂钩或自定义挂钩:

```
function App() {
  const [counter, setCounter] = useState(0);
}

```

我们刚刚在`App`组件中调用了一个`useState`钩子。这符合 React 的钩子法则。但是现在，让我们假设我们在`App`组件中有一个函数是这样的:

```
function App() {
  const counterFn = () => {
    const [counter, setCounter] = useState(0);
    setCounter(counter++)
  }

  return (
    <>
      <div>
        <button onClick={counterFn}>Incr Counter</button>
      </div>
    </>
  )
}

```

这不是使用 React 钩子的好方法。请注意，我们正在函数内部调用`useState`。这会抛出错误:`React hooks must be called in a React function component or a custom React hook function`。这是因为`onClick`功能处理器`counterFn`不是功能组件。

我们可以在自定义的 React 钩子内部调用 React 钩子。现在，React 知道一个函数是一个名字以`use`开头的钩子，可能会调用其他 React 钩子。

让我们看一个例子:

```
function useCounter() {
  let counter = 0
  const setCounter = () => {
    counter++
  }
  return { counter, setCounter }
}

```

上面的函数是一个自定义的 React 钩子，因为它的名字以`use`开头。

```
function useCounter() {
  const [counter, setCounter] = useState(0)
  return { counter, setCounter }
}

```

这也是一个自定义的 React 挂钩。它不仅名字以`use`开头，而且调用 React `useState`钩子。由于`useCounter`是一个函数而不是一个 React 功能组件，在其中调用 React 钩子是可行的。在这种情况下，React 不会给我们`React hooks must be called in a React function component or a custom React hook function`错误。

### " React 挂钩`useEffect`缺少依赖项"

如果我们没有向`useEffect`钩子依赖数组添加必要的依赖，就会出现这个错误。让我们看一个例子:

```
function List(props) {
  const [counter, setCounter] = useState(0);

  useEffect(() => {
    setCounter(counter)
  }, []);

  return <div>Counter: {counter} </div>;
}

```

我们这里有一个`useEffect`。在这种情况下，`useEffect`将在初始挂载时运行一次，并且不会再次运行。现在，看一下`useEffect`内部的回调，我们看到它用`counter`状态作为参数来调用`setCounter`。

我们可以看到`useEffect`依赖于`counter`状态。在这种情况下，React 会显示警告:`Either include it or remove the dependency array. eslintreact-hooks/exhaustive-deps`。

为了避免这个错误，我们需要移除依赖数组或者在依赖数组中包含`counter`状态。让我们将`counter`状态包含在`useEffect`依赖数组中:

```
function List(props) {
  const [counter, setCounter] = useState(0);

  useEffect(() => {
    setCounter(counter)
  }, [counter]);

  return <div>Counter: {counter} </div>;
}

```

现在，这将清除我们控制台中的警告。

## 结论

React 钩子有问题，这是无法回避的事实。我一直很喜欢 React 的声明式方法，在这种方法中，状态会改变，UI 会更新。`useEffect`和 friends 的依赖数组听起来像是实现这一点的声明性方式。

如果依赖数组只包含原语，那么这很好。不幸的是，当依赖数组中有对象和函数时，问题就出现了。引用检查是 JavaScript 中对象和函数的比较方式。一个放置不当的箭头函数将导致一个`useEffect`钩子陷入无限循环。然后，开发人员需要考虑在`useCallback`、`useRef`、`useMemo`等之间做出选择。，想出制胜的公式。

React 钩子只能从顶层调用的事实导致了应该在框架层处理的变通办法。

React 钩子也迫使你使用闭包，我有很多使用闭包时意外发生的事情留下的伤疤。由于代码在闭包中执行而导致的陈旧状态是 Hooks linter 要解决的问题之一。