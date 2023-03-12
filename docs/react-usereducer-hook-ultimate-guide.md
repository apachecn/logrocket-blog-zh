# React useReducer Hook 终极指南

> 原文：<https://blog.logrocket.com/react-usereducer-hook-ultimate-guide/>

***编者按**:这篇文章最后一次更新是在 2022 年 3 月 28 日，包括了对更新的工具和框架的引用。*

`useReducer`是 React v16.8 附带的附加钩子之一。作为`useState`钩子的替代，`useReducer`帮助您管理 React 应用程序中复杂的状态逻辑。当与其他钩子如`useContext`、[、`useReducer`结合使用时，可以很好地替代 Redux、反冲或 MobX](https://blog.logrocket.com/use-hooks-and-context-not-react-and-redux/) 。在某些情况下，这绝对是更好的选择。

虽然 Redux、反冲和 MobX 通常是在大型 React 应用程序中管理全局状态的最佳选择，但许多 React 开发人员在可以用钩子有效处理状态的时候，往往会跳入这些第三方状态管理库。

当您考虑开始使用像 [Redux 这样的第三方库的复杂性(Redux Toolkit](https://blog.logrocket.com/using-typescript-with-redux-toolkit/) 使其变得容易得多)以及所需的大量样板代码时，使用 React 钩子和上下文 API 管理状态就成了一个很有吸引力的选择。在我们的应用程序中，不需要安装外部包或添加一堆文件和文件夹来管理全局状态。

但是，黄金法则仍然存在。组件状态为组件状态，Redux 为应用程序状态。在本教程中，我们将深入探索`useReducer`钩子，回顾你应该和不应该使用它的场景。我们开始吧！

## 目录

## `useReducer`挂钩是如何工作的？

与`useState`挂钩一样，`useReducer`挂钩用于存储和更新状态。它接受一个`reducer`函数作为第一个参数，初始状态作为第二个参数。

`useReducer`返回一个保存当前状态值的数组和一个`dispatch`函数，您可以向该函数传递一个动作并在以后调用它。虽然这类似于 Redux 使用的模式，但还是有一些不同。

例如，`useReducer`函数与特定的减速器紧密耦合。我们只将动作对象分派给 reducer，而在 Redux 中，dispatch 函数将动作对象发送给存储。在分派时，组件不需要知道哪个缩减器将处理该动作。

对于那些可能不熟悉 Redux 的人，我们将进一步探讨这个概念。Redux 中有三个主要构件:

*   存储:保存应用程序状态数据的不可变对象
*   reducer:返回一些状态数据的函数，由一个动作`type`触发
*   一个动作:一个告诉缩减器如何改变状态的对象。它必须包含一个`type`属性，并且可以包含一个可选的`payload`属性

让我们看看这些构建块与使用`useReducer`钩子管理状态相比如何。以下是 Redux 中的一个商店示例:

```
import { createStore } from 'redux'

const store = createStore(reducer, [preloadedState], [enhancer])

```

在下面的代码中，我们用`useReducer`钩子初始化状态:

```
const initialState = { count: 0 }

const [state, dispatch] = useReducer(reducer, initialState)

```

Redux 中的 reducer 函数将接受前一个 app 状态和正在调度的动作，计算下一个状态，并返回新的对象。Redux 中的 Reducers 遵循以下语法:

```
(state = initialState, action) => newState

```

让我们考虑下面的例子:

```
// notice that the state = initialState and returns a new state

const reducer = (state = initialState, action) => {
   switch (action.type) {
      case 'ITEMS_REQUEST':
         return Object.assign({}, state, {
            isLoading: action.payload.isLoading
         })
      case ‘ITEMS_REQUEST_SUCCESS':
         return Object.assign({}, state, {
            items: state.items.concat(action.items),
            isLoading: action.isLoading
         })
      default:
         return state;
   }
}
export default reducer;

```

React 不使用`(state = initialState, action) => newState` Redux 模式，所以 reducer 函数的工作方式有点不同。下面的代码显示了如何使用 React 创建减速器:

```
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}  

```

下面是一个可以在 Redux 中执行的操作的示例:

```
{ type: ITEMS_REQUEST_SUCCESS, payload: { isLoading: false } }

// action creators
export function itemsRequestSuccess(bool) {
   return {
      type: ITEMS_REQUEST_SUCCESS,
      payload: {
      isLoading: bool,
    }
   }
}

// dispatching an action with Redux
dispatch(itemsRequestSuccess(false))    // to invoke a dispatch function, you need to pass action as an argument to the dispatch function

```

`useReducer`中的动作以类似的方式工作:

```
// not the complete code
switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    default:
      throw new Error();
  }

// dispatching an action with useReducer
 <button onClick={() => dispatch({type: 'increment'})}>Increment</button>

```

如果上面代码中的动作类型是`increment`，我们的状态对象增加了`1`。

### 减速器功能

JavaScript `reduce()`方法对数组的每个元素执行一个 reducer 函数，并返回一个值。`reduce()`方法接受一个 reducer 函数，它本身最多可以接受四个参数。下面的代码片段说明了缩减器是如何工作的:

```
const reducer = (accumulator, currentValue) => accumulator + currentValue;
[2, 4, 6, 8].reduce(reducer)
// expected output: 20

```

在 React 中，`useReducer`本质上接受一个返回单个值的 reducer 函数:

```
  const [count, dispatch] = useReducer(reducer, initialState);

```

reducer 函数本身接受两个参数并返回一个值。第一个参数是当前状态，第二个是动作。状态就是我们正在操作的数据。reducer 函数接收一个由`dispatch`函数执行的动作:

```
function reducer(state, action) { }

dispatch({ type: 'increment' })

```

这个动作就像是你传递给 reducer 函数的一个指令。基于指定的动作，reducer 函数执行必要的状态更新。如果您以前使用过像 Redux 这样的状态管理库，那么您一定遇到过这种状态管理模式。

### 指定初始状态

初始状态是传递给`useReducer`钩子的第二个参数，它代表默认状态:

```
const initialState = { count: 1 }

// wherever our useReducer is located
const [state, dispatch] = useReducer(reducer, initialState, initFunc)

```

如果不向`useReducer`传递第三个参数，它会把第二个参数作为初始状态。第三个参数是`init`函数，是可选的。这种模式也遵循 Redux 状态管理的黄金法则之一；应该通过发出动作来更新状态。千万不要直接写信给政府。

然而，值得注意的是，Redux `state = initialState`约定与`useReducer`的工作方式不同，因为初始值有时取决于道具。

### 懒惰地创建初始状态

在编程中，惰性初始化是一种策略，它将对象的创建、值的计算或其他一些昂贵的过程延迟到第一次需要它的时候。

如上所述，`useReducer`可以接受第三个参数，这是一个可选的`init`函数，用于惰性地创建初始状态。它允许您提取逻辑来计算 reducer 函数之外的初始状态，如下所示:

```
const initFunc = (initialCount) => {
    if (initialCount !== 0) {
        initialCount=+0
    }
  return {count: initialCount};
}

// wherever our useReducer is located
const [state, dispatch] = useReducer(reducer, initialCount, initFunc);

```

如果值还不是`0`，上面的`initFunc`将在页面装载时将`initialCount`重置为`0`，然后返回状态对象。注意这个`initFunc`是一个函数，不仅仅是一个数组或对象。

### `dispatch`法

`dispatch`函数接受一个对象，该对象表示当它被调用时我们想要执行的动作类型。基本上，它将动作类型发送给 reducer 函数来执行它的工作，当然，这是更新状态。

要执行的动作在我们的 reducer 函数中指定，然后传递给`useReducer`。reducer 函数将返回更新后的状态。

将由我们的组件调度的动作应该总是被表示为一个带有`type`和`payload`键的对象，其中`type`代表被调度动作的标识符，`payload`是该动作将添加到状态中的信息。

`dispatch`是从`useReducer`钩子返回的第二个值，可以在我们的 JSX 中用来更新状态:

```
// creating our reducer function
function reducer(state, action) {
  switch (action.type) {
   // ...
      case 'reset':
          return { count: action.payload };
    default:
      throw new Error();
  }
}

// wherever our useReducer is located
const [state, dispatch] = useReducer(reducer, initialCount, initFunc);

// Updating the state with the dispatch functon on button click
<button onClick={() => dispatch({type: 'reset', payload: initialCount})}> Reset </button>

```

注意我们的 reducer 函数是如何使用从`dispatch`函数传递来的有效负载的。它将我们的状态对象设置为有效载荷，即无论`initialCount`是什么。

注意，我们可以通过 props 将`dispatch`函数传递给其他组件，仅此一点就允许我们用`useReducer`替换 Redux。

假设我们有一个组件，我们想把它作为道具传递给我们的调度函数。我们可以从父组件轻松做到这一点:

```
<Increment count={state.count} handleIncrement={() => dispatch({type: 'increment'})}/>

```

现在，在子组件中，我们接收 props，当发出 props 时，将触发 dispatch 函数并更新状态:

```
<button onClick={handleIncrement}>Increment</button>

```

### 逃避急件

如果`useReducer`钩子返回与当前状态相同的值，React 将退出而不渲染子元素或触发效果，因为它使用了`[Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description)`比较算法。

## 用`useReducer`钩子构建一个简单的计数器应用程序

现在，让我们通过使用`useReducer`钩子构建一个简单的计数器应用程序来运用我们的知识:

```
import React, { useReducer } from 'react';

const initialState = { count: 0 }
 // The reducer function
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 }
    case 'decrement':
      return { count: state.count - 1 }
    case 'reset':
      return {count: state.count = 0}
    default:
     return { count: state.count  }
  }
}

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, initialState)

  return (
    <div>
      Count: {state.count}
       <br />
       <br/>
       <button onClick={() => dispatch({ type: 'increment' })}>Increment</button>
       <button onClick={() => dispatch({ type: 'decrement'})}>Decrement</button>
       <button onClick={() => dispatch({ type: 'reset'})}>Reset</button>
    </div>
  );
};

export default Counter;

```

首先，我们用`0`初始化状态，然后我们创建一个 reducer 函数，它接受计数的当前状态作为参数和动作。缩减器根据动作类型更新状态。`increment`、`decrement`和`reset`都是动作类型，当它们被分派时，相应地更新我们的应用程序的状态。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

为了增加状态计数`const initialState = { count: 0 }`，当`increment`动作类型被分派时，我们简单地将`count`设置为`state.count + 1`。

## `useState`对`useReducer`

[`useState`是管理简单状态转换的基本钩子](https://reactjs.org/docs/hooks-reference.html#basic-hooks)， [`useReducer`是管理更复杂状态逻辑的附加钩子](https://reactjs.org/docs/hooks-reference.html#additional-hooks)。然而，值得注意的是，`useState`在内部使用`useReducer`，这意味着你可以使用`useReducer`做任何你可以用`useState`做的事情。

然而，这两个挂钩之间有一些主要的区别。使用`useReducer`，您可以避免通过组件的不同级别传递回调。相反，`useReducer`允许您传递一个提供的`dispatch`函数，这反过来将提高触发深度更新的组件的性能。

然而，这并不意味着在每次渲染时会重新调用`useState`更新函数。当您有一个复杂的逻辑来更新状态时，您不会直接使用 setter 来更新状态。相反，您将编写一个复杂的函数，该函数将调用带有更新状态的 setter。

因此，建议使用`useReducer`，它返回一个在重新渲染之间不会改变的`dispatch`方法，并且您可以在 reducers 中拥有操作逻辑。

同样值得注意的是，使用`useState`，调用状态更新函数来更新状态，但是使用`useReducer`，调用`dispatch`函数，并且至少有一个类型的动作被传递给它。

现在，让我们看看这两个钩子是如何声明和使用的。

### 用`useState`声明状态

```
const [state, setState] = useState('default state');

```

`useState`返回保存当前状态值的数组和用于更新状态的`setStatemethod`。

### 用`useReducer`声明状态

```
const [state, dispatch] = useReducer(reducer, initialState)

```

`useReducer`返回一个保存当前状态值的数组和一个在逻辑上实现与`setState`相同目标的`dispatchmethod`，更新状态。

用`useState`更新状态如下:

```
<input type='text' value={state} onChange={(e) => setState(e.currentTarget.value)} />

```

用`useReducer`更新状态如下:

```
&lt;button onClick={() => dispatch({ type: 'decrement'})}>Decrement</button>

```

我们将在本教程的后面更深入地讨论`dispatch`函数。可选地，一个动作对象也可以有一个`payload`:

```
<button onClick={() => dispatch({ type: 'decrement', payload: 0})}>Decrement</button>

```

`useReducer`在管理复杂的状态形状时非常方便，例如，当状态不仅仅由原始值组成时，如嵌套数组或对象:

```
const [state, dispatch] = useReducer(loginReducer,
  {
    users: [
      { username: 'Philip', isOnline: false},
      { username: 'Mark', isOnline: false },
      { username: 'Tope', isOnline: true},
      { username: 'Anita', isOnline: false },
    ],
    loading: false,
    error: false,
  },
);

```

管理这种本地状态更容易，因为参数相互依赖，并且所有的逻辑都可以封装到一个 reducer 中。

## 何时使用`useReducer`钩

随着应用程序规模的增长，您很可能会处理更复杂的状态转换，此时您最好使用`useReducer`。

`useReducer`提供了比`useState`更可预测的状态转换，当状态变化变得如此复杂，以至于您希望有一个地方来管理状态时，这变得更加重要，就像 render 函数一样。

一个很好的经验是，当您不再管理原始数据，即字符串、整数或布尔值，而是必须管理一个复杂的对象，如数组和其他原始数据时，您可能更适合使用`useReducer`。

如果你是一个视觉学习者，下面的视频给出了详细的解释，以及何时使用`useReducer`钩子的实际例子。

 [https://www.youtube.com/embed/o-nCM1857AQ?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/o-nCM1857AQ?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

### 创建登录组件

为了更好地理解何时使用`useReducer`，让我们创建一个登录组件，并比较我们如何使用`useState`和`useReducer`钩子来管理状态。

首先，让我们用`useState`创建登录组件:

```
import React, { useState } from 'react';

export default function LoginUseState() {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [isLoading, showLoader] = useState(false);
  const [error, setError] = useState('');
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const onSubmit = async (e) => {
    e.preventDefault();
    setError('');
    showLoader(true);
    try {
      await function login({ username, password }) {
        return new Promise((resolve, reject) => {
          setTimeout(() => {
            if (username === 'ejiro' && password === 'password') {
              resolve();
            } else {
              reject();
            }
          }, 1000);
        });
      }
      setIsLoggedIn(true);
    } catch (error) {
      setError('Incorrect username or password!');
      showLoader(false);
      setUsername('');
      setPassword('');
    }
  };
  return (
    <div className='App'>
      <div className='login-container'>
        {isLoggedIn ? (
          <>
            <h1>Welcome {username}!</h1>
            <button onClick={() => setIsLoggedIn(false)}>Log Out</button>
          </>
        ) : (
          <form className='form' onSubmit={onSubmit}>
            {error && <p className='error'>{error}</p>}
            <p>Please Login!</p>
            <input
              type='text'
              placeholder='username'
              value={username}
              onChange={(e) => setUsername(e.currentTarget.value)}
            />
            <input
              type='password'
              placeholder='password'
              autoComplete='new-password'
              value={password}
              onChange={(e) => setPassword(e.currentTarget.value)}
            />
            <button className='submit' type='submit' disabled={isLoading}>
              {isLoading ? 'Logging in...' : 'Log In'}
            </button>
          </form>
        )}
      </div>
    </div>
  );
}

```

请注意我们是如何处理所有这些状态转换的，比如`username`、`password`、`isLoading`、`error`和`isLoggedIn`，而我们实际上应该更关注用户想要在登录组件上采取的动作。

我们使用了五个`useState`钩子，我们不得不担心这些状态何时被转换。我们可以重构上面的代码，使用`useReducer`并将所有的逻辑和状态转换封装在一个 reducer 函数中:

```
import React, { useReducer } from 'react';

function loginReducer(state, action) {
  switch (action.type) {
    case 'field': {
      return {
        ...state,
        [action.fieldName]: action.payload,
      };
    }
    case 'login': {
      return {
        ...state,
        error: '',
        isLoading: true,
      };
    }
    case 'success': {
      return {
        ...state,
        isLoggedIn: true,
        isLoading: false,
      };
    }
    case 'error': {
      return {
        ...state,
        error: 'Incorrect username or password!',
        isLoggedIn: false,
        isLoading: false,
        username: '',
        password: '',
      };
    }
    case 'logOut': {
      return {
        ...state,
        isLoggedIn: false,
      };
    }
    default:
      return state;
  }
}
const initialState = {
  username: '',
  password: '',
  isLoading: false,
  error: '',
  isLoggedIn: false,
};
export default function LoginUseReducer() {
  const [state, dispatch] = useReducer(loginReducer, initialState);
  const { username, password, isLoading, error, isLoggedIn } = state;
  const onSubmit = async (e) => {
    e.preventDefault();
    dispatch({ type: 'login' });
    try {
      await function login({ username, password }) {
        return new Promise((resolve, reject) => {
          setTimeout(() => {
            if (username === 'ejiro' && password === 'password') {
              resolve();
            } else {
              reject();
            }
          }, 1000);
        });
      }
      dispatch({ type: 'success' });
    } catch (error) {
      dispatch({ type: 'error' });
    }
  };
  return (
    <div className='App'>
      <div className='login-container'>
        {isLoggedIn ? (
          <>
            <h1>Welcome {username}!</h1>
            <button onClick={() => dispatch({ type: 'logOut' })}>
              Log Out
            </button>
          </>
        ) : (
          <form className='form' onSubmit={onSubmit}>
            {error && <p className='error'>{error}</p>}
            <p>Please Login!</p>
            <input
              type='text'
              placeholder='username'
              value={username}
              onChange={(e) =>
                dispatch({
                  type: 'field',
                  fieldName: 'username',
                  payload: e.currentTarget.value,
                })
              }
            />
            <input
              type='password'
              placeholder='password'
              autoComplete='new-password'
              value={password}
              onChange={(e) =>
                dispatch({
                  type: 'field',
                  fieldName: 'password',
                  payload: e.currentTarget.value,
                })
              }
            />
            <button className='submit' type='submit' disabled={isLoading}>
              {isLoading ? 'Logging in...' : 'Log In'}
            </button>
          </form>
        )}
      </div>
    </div>
  );
}

```

请注意使用`useReducer`的新实现如何让我们更加关注用户将要采取的行动。比如，当`login`动作被调度时，我们可以清楚地看到我们想要发生的事情。我们希望返回当前状态的副本，将我们的`error`设置为空字符串，并将`isLoading`设置为 true:

```
case 'login': {
      return {
        ...state,
        error: '',
        isLoading: true,
      };
    }

```

我们当前实现的美妙之处在于，我们不再需要关注状态转换。相反，我们热衷于用户要执行的动作。

## 何时不使用`useReducer`挂钩

尽管能够在我们的应用中使用`useReducer`钩子来处理复杂的状态逻辑，但重要的是要注意，在某些场景中，像 [Redux 这样的第三方状态管理库可能是更好的选择](https://blog.logrocket.com/why-use-redux-reasons-with-clear-examples-d21bffd5835/)。

一个简单的答案是，除非 vanilla React 有问题，否则应该避免使用 Redux 或任何其他第三方状态管理库。如果你仍然不清楚是否需要它，很可能你并不需要。

让我们回顾一些使用像 Redux 或 MobX 这样的库更有意义的具体案例。

### 当您的应用程序需要单一的真实来源时

使用像 Redux 这样的库来集中应用程序的状态和逻辑使得创建通用应用程序状态变得轻而易举，因为来自服务器的状态可以很容易地序列化到客户端应用程序。拥有单一的事实来源也使得实现强大的功能变得容易，比如撤销/重做特性。

### 当你想要一个更可预测的状态时

使用像 Redux 这样的库可以帮助您编写在不同环境中运行时行为一致的应用程序。如果将相同的状态和动作传递给一个 reducer，总是会产生相同的结果，因为 reducer 是纯函数。另外，Redux 中的状态是只读的，改变状态的唯一方法是发出一个动作，一个描述发生了什么的对象。

### 当状态提升到顶层组件不再足够时

当将所有东西都保持在顶级 React 组件的状态中不再足够时，使用像 Redux 这样的库是最好的。

### 状态持久性

有了像 Redux 和 MobX 这样的库，您可以轻松地将状态保存到`localStorage`中，并让最终用户可以使用它，甚至在页面刷新之后。

有了所有这些好处，还值得注意的是，使用像 Redux 这样的库，而不是使用带有`useReducer`的纯 React，会带来一些折衷。例如，Redux 有一个巨大的学习曲线，通过使用 Redux 工具包的[可以最小化，这肯定不是编写代码的最快方法。相反，它旨在为您提供一种管理应用程序状态的绝对且可预测的方式。](https://redux.js.org/introduction/getting-started#redux-toolkit-example)

## 结论

在本文中，我们深入探讨了 React 的`useReducer`钩子，回顾了它是如何工作的，何时使用它，并将其与`useState`钩子进行了比较。

我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。编码快乐！

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