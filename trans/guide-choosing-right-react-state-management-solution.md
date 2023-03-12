# 选择正确的 React 状态管理解决方案指南

> 原文：<https://blog.logrocket.com/guide-choosing-right-react-state-management-solution/>

***编者按**:这篇文章于 2022 年 9 月 23 日更新，添加了为什么我们需要 React 中的状态管理的信息，添加了之前没有包含在文章中的其他状态管理工具，如 Jotai、MobX 和 Zustand，并添加了哪个状态管理工具最适合 React 的信息。*

状态管理是每个开发人员在构建 React 应用程序时面临的一个基本挑战——而且不是一个小问题。React 中有许多有效的状态管理方法，每种方法都解决了一系列突出的问题。

作为开发人员，重要的是不仅要了解不同的方法、工具和模式，还要了解它们的用例及权衡。

考虑状态管理的一个有用的方法是根据我们在项目中解决的问题。在本文中，我们将介绍在 React 中管理状态的常见用例，并了解何时应该考虑使用每种解决方案。我们将通过构建一个简单的计数器应用程序来实现这一点。

## 为什么我需要 React 中的状态管理？

首先，我们来讨论状态管理的重要性。React 中的 State 是一个 JavaScript 对象，它可以根据用户的操作改变组件的行为。状态也可以被认为是一个组件的记忆。

React 应用程序由管理自身状态的组件构建而成。这对于小型应用程序来说是可行的，但是随着应用程序复杂性的增加，处理组件之间的共享状态变得越来越复杂和困难。

下面是一个简单的例子，说明金融科技应用程序中的成功交易可能会影响其他几个组件:

*   新交易将改变主页上显示的可用余额
*   现在，新交易将显示为用户总交易历史中的最新交易

这就是为什么在开发可伸缩的 React 应用程序时，状态管理是必不可少的。从长远来看，如果状态管理不正确，应用程序无疑会遇到问题。像这样不断地排除故障和重建应用程序可能会变得单调乏味。

## React 中的本地组件状态

实现计数器最简单的方法是使用带有`useState`钩子的本地组件状态。

```
import { useState } from 'react'

const Counter = () => {
    const [count, setCount] = useState(0)

    const increaseCount = () => {
        setCount(count + 1)
    }

    const decreaseCount = () => {
        if (count > 0) {
            setCount(count - 1)
        }
    }
    return (
        <div>
            <h1>{count}</h1>
            <button onClick={decreaseCount}>-</button>
            <button onClick={increaseCount}>+</button>
        </div>
    )
}

export default Counter

```

我们结束了，对吗？文章结束？不完全是。

如果这是一个真实的项目，很可能在未来，我们需要在我们的应用程序的其他地方更多的按钮和标题。确保它们的外观和行为一致是一个好主意，这就是为什么我们可能应该将它们转换成可重用的 React 组件。

## React 中的组件道具

将我们的`Button`和`Header`变成独立的组件揭示了一个新的挑战。我们需要一些方法在他们和主要的`Counter`组件之间进行交流。

这就是组件道具发挥作用的地方。对于我们的`Header`组件，我们添加了一个`text`道具。对于我们的`Button`，我们需要一个`label`道具和一个`onClick`回调。我们的代码现在看起来像这样:

```
import { useState } from 'react'

const Header = ({ text }) => <h1>{text}</h1>

const Button = ({ label, onClick }) => (
    <button onClick={onClick}>{label}</button>
)

const Counter = () => {
    const [count, setCount] = useState(0)

    const increaseCount = () => {
        setCount(count + 1)
    }

    const decreaseCount = () => {
        if (count > 0) {
            setCount(count - 1)
        }
    }
    return (
        <div>
            <Header text={count} />
            <Button onClick={decreaseCount} label="-" />
            <Button onClick={increaseCount} label="+" />
        </div>
    )
}

export default Counter

```

这看起来棒极了！但是想象一下下面的场景:如果我们只需要在我们的 home route 上显示计数，并且有一个单独的 route `/controls`来显示计数和控制按钮，那会怎么样呢？我们应该如何着手这件事？

## React 中的路由

假设我们正在构建一个单页面应用程序，现在我们需要处理第二种状态——我们正在走的路线。例如，让我们看看如何用 [React 路由器](https://reactrouter.com)来实现这一点。

```
import { BrowserRouter as Router, Switch, Route, Link } from 'react-router-dom'
import { useState } from 'react'

const Header = ({ text }) => <h1>{text}</h1>

const Button = ({ label, onClick }) => (
    <button onClick={onClick}>{label}</button>
)

const Home = ({ count }) => {
    return <Header text={count} />
}

const Controls = ({ count, decreaseCount, increaseCount }) => {
    return (
        <>
            <Header text={count} />
            <Button onClick={decreaseCount} label="-" />
            <Button onClick={increaseCount} label="+" />
        </>
    )
}

const App = () => {
    const [count, setCount] = useState(0)
    const increaseCount = () => {
        setCount(count + 1)
    }
    const decreaseCount = () => {
        if (count > 0) {
            setCount(count - 1)
        }
    }

    return (
        <Router>
            <nav>
                <Link to="/">Home</Link>
                <Link to="/controls">Controls</Link>
            </nav>
            <Switch>
                <Route path="/controls">
                    <Controls
                        increaseCount={increaseCount}
                        decreaseCount={decreaseCount}
                        count={count}
                    />
                </Route>
                <Route path="/">
                    <Home count={count} />
                </Route>
            </Switch>
        </Router>
    )
}

export default App

```

不错！我们现在有了各自的路线，一切都按预期进行。但是，您可能会注意到一个问题。我们将计数状态保存在`App`中，并使用 props 将它传递给组件树。但是似乎我们一遍又一遍地传递同一个道具，直到我们到达需要使用它的组件。当然，随着我们的应用程序的增长，它只会变得更糟。这就是所谓的支柱钻孔。

让我们修理它！

## 使用 React 的上下文 API 和`useReducer`

如果有一种方法可以让我们的组件访问`count`状态，而不必通过 props 接收它，这不是很好吗？React 上下文 API 和`useReducer`钩子的组合就是这样做的:

```
import { BrowserRouter as Router, Switch, Route, Link } from 'react-router-dom'
import { createContext, useContext, useReducer } from 'react'

const initialState = 0

const reducer = (state, action) => {
    switch (action.type) {
        case 'INCREMENT':
            return state + 1
        case 'DECREMENT':
            return state - 1 >= 0 ? state - 1 : 0
        default:
            return state
    }
}

const CountContext = createContext(null)

const useCount = () => {
    const value = useContext(CountContext)
    if (value === null) throw new Error('CountProvider missing')
    return value
}

const CountProvider = ({ children }) => (
    <CountContext.Provider value={useReducer(reducer, initialState)}>
        {children}
    </CountContext.Provider>
)

const Header = ({ text }) => <h1>{text}</h1>

const Button = ({ label, onClick }) => (
    <button onClick={onClick}>{label}</button>
)

const Home = () => {
    const [state] = useCount()
    return <Header text={state} />
}

const Controls = () => {
    const [state, dispatch] = useCount()
    return (
        <>
            <Header text={state} />
            <Button onClick={() => dispatch({ type: 'DECREMENT' })} label="-" />
            <Button onClick={() => dispatch({ type: 'INCREMENT' })} label="+" />
        </>
    )
}

const App = () => {
    return (
        <CountProvider>
            <Router>
                <nav>
                    <Link to="/">Home</Link>
                    <Link to="/controls">Controls</Link>
                </nav>
                <Switch>
                    <Route path="/controls">
                        <Controls />
                    </Route>
                    <Route path="/">
                        <Home />
                    </Route>
                </Switch>
            </Router>
        </CountProvider>
    )
}

export default App

```

厉害！我们已经解决了支柱钻孔的问题。通过创建一个描述性的缩减器，使我们的代码更具声明性，这给了我们额外的加分。

我们对我们的实现很满意，对于许多用例来说，这确实是我们所需要的。但是，如果我们可以持久化计数，这样就不会在每次刷新页面时重置为 0，这不是很好吗？并拥有应用程序状态的日志？坠机报告呢？

了解我们的应用程序崩溃时的确切状态，以及如何利用令人惊叹的开发工具将非常有帮助。嗯，我们可以使用 Redux 做到这一点！

## 使用 Redux 进行状态管理

通过使用 [Redux](https://redux.js.org/) 来管理我们应用的状态，我们可以做到以上所有的事情，甚至更多。该工具背后有一个强大的社区和一个可以轻松利用的[丰富的生态系统](https://redux.js.org/introduction/ecosystem)。

让我们用 [Redux Toolkit](https://redux-toolkit.js.org/) 来设置我们的计数器。

```
import { BrowserRouter as Router, Switch, Route, Link } from 'react-router-dom'
import { configureStore, createSlice } from '@reduxjs/toolkit'
import { useSelector, useDispatch, Provider } from 'react-redux'

const counterSlice = createSlice({
    name: 'counter',
    initialState: {
        value: 0,
    },
    reducers: {
        increment: state => {
            state.value += 1
        },
        decrement: state => {
            if (state.value > 0) {
                state.value -= 1
            }
        },
    },
})

const store = configureStore({
    reducer: { counter: counterSlice.reducer },
})

const { increment, decrement } = counterSlice.actions

const Header = ({ text }) => <h1>{text}</h1>

const Button = ({ label, onClick }) => (
    <button onClick={onClick}>{label}</button>
)

const Home = () => {
    const count = useSelector(state => state.counter.value)
    return <Header text={count} />
}

const Controls = () => {
    const count = useSelector(state => state.counter.value)
    const dispatch = useDispatch()
    return (
        <>
            <Header text={count} />
            <Button onClick={() => dispatch(decrement())} label="-" />
            <Button onClick={() => dispatch(increment())} label="+" />
        </>
    )
}

const App = () => {
    return (
        <Provider store={store}>
            <Router>
                <nav>
                    <Link to="/">Home</Link>
                    <Link to="/controls">Controls</Link>
                </nav>
                <Switch>
                    <Route path="/controls">
                        <Controls />
                    </Route>
                    <Route path="/">
                        <Home />
                    </Route>
                </Switch>
            </Router>
        </Provider>
    )
}
export default App

```

这看起来真的很棒！我们的状态现在存储在全局 Redux store 中，用纯函数管理(Redux Toolkit 使用 [Immer](https://github.com/immerjs/immer) 来保证不变性)。我们已经可以利用令人敬畏的 [Redux DevTools](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en) 。

但是像处理副作用，或者使状态持久化，或者实现日志记录和/或崩溃报告这样的事情呢？这就是我们之前提到的 Redux 生态系统发挥作用的地方。

处理副作用有多种选择，包括 [redux-thunk](https://github.com/reduxjs/redux-thunk) 和 [redux-saga](https://redux-saga.js.org/) 。像 [redux-persist](https://github.com/rt2zz/redux-persist) 这样的库非常适合将 redux 存储中的数据保存在本地或会话存储中，使其持久化。

总之 Redux 很棒！它在 React 世界中被广泛使用，理由很充分。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

但是，如果我们更喜欢一种更分散的国家管理方法呢？也许我们担心性能，或者在 React 树的不同分支中有频繁的数据更新，所以我们希望避免不必要的重新渲染，同时保持一切同步。

或者，也许我们需要一种好的方法来从我们的状态中获取数据，并在客户机上高效、可靠地进行计算。如果我们想在不牺牲应用程序范围内状态观察能力的情况下实现所有这一切，会怎么样呢？输入反冲。

## 有反冲的原子态

建议我们能够用一个简单的计数器应用程序达到 React Context 或 Redux 的极限有点牵强。为了获得更好的原子状态管理用例，请查看戴夫·麦凯布关于 T2 反冲的精彩视频。

然而，从原子的角度考虑状态确实有助于扩展我们对状态管理的理解。另外，反冲 API 玩起来很有趣，所以让我们用它来重新实现我们的计数器。

```
import { BrowserRouter as Router, Switch, Route, Link } from 'react-router-dom'
import { atom, useRecoilState, RecoilRoot } from 'recoil'

const countState = atom({
    key: 'count',
    default: 0,
})

const Header = ({ text }) => <h1>{text}</h1>

const Button = ({ label, onClick }) => (
    <button onClick={onClick}>{label}</button>
)

const Home = () => {
    const [count] = useRecoilState(countState)
    return <Header text={count} />
}

const Controls = () => {
    const [count, setCount] = useRecoilState(countState)
    const increaseCount = () => {
        setCount(count + 1)
    }
    const decreaseCount = () => {
        if (count > 0) {
            setCount(count - 1)
        }
    }
    return (
        <>
            <Header text={count} />
            <Button onClick={decreaseCount} label="-" />
            <Button onClick={increaseCount} label="+" />
        </>
    )
}

const App = () => {
    return (
        <RecoilRoot>
            <Router>
                <div className="App">
                    <nav>
                        <Link to="/">Home</Link>
                        <Link to="/controls">Controls</Link>
                    </nav>
                    <Switch>
                        <Route path="/controls">
                            <Controls />
                        </Route>
                        <Route path="/">
                            <Home />
                        </Route>
                    </Switch>
                </div>
            </Router>
        </RecoilRoot>
    )
}

export default App

```

使用反冲感觉很像使用 React 本身。回顾一下我们最初的例子，就会发现这两者是多么的相似。反冲也有自己的一套开发工具。要记住的一个重要的考虑是，这个库仍然是实验性的，可能会发生变化。慎用。

好吧，我们可以有一个反冲计数器。但是国家管理层的偏好取决于我们的优先级。如果应用程序是由一个团队开发的，而开发人员、设计人员、项目经理和其他所有人在用户界面上使用同一种语言是非常重要的，那该怎么办？

此外，如果这种语言可以在我们的应用程序中用高度声明性的代码直接表达会怎么样？如果我们能保证我们永远不会到达不可能的状态，从而消除一整类的错误，那会怎么样呢？你猜怎么着？我们可以。

## 带有 XState 的状态机

所有这些都可以通过状态图和状态机来实现。状态图有助于可视化应用程序的所有可能状态，并定义可能的状态。他们很容易在整个团队中被理解、分享和讨论。

下面是我们的计数器状态图:

![Counter Is Allowed To Increase, It Is Not Possible For Counter To Decrease ](img/6f4cc9fc77f9aafdaabd4e24c33de925.png)

尽管这是一个微不足道的实现，但我们已经可以看到使用状态机的一个很酷的优点。最初，不可能递减计数器，因为它的初始值是 0。这个逻辑被声明为我们的状态机，并在图表中可见，而对于我们探索的其他方法，一般来说，很难找到它的正确位置。

下面是我们的状态机在实践中的应用:

```
>import { BrowserRouter as Router, Switch, Route, Link } from 'react-router-dom'
import { useMachine } from '@xstate/react'
import { createMachine, assign } from 'xstate'

export const counterMachine = createMachine({
    initial: 'active',
    context: { count: 0 },
    states: {
        active: {
            on: {
                INCREMENT: {
                    actions: assign({ count: ctx => ctx.count + 1 }),
                },
                DECREMENT: {
                    cond: ctx => ctx.count > 0,
                    actions: assign({
                        count: ctx => ctx.count - 1,
                    }),
                },
            },
        },
    },
})

const Header = ({ text }) => <h1>{text}</h1>

const Button = ({ label, onClick }) => (
    <button onClick={onClick}>{label}</button>
)

const Home = () => {
    const [state] = useMachine(counterMachine)
    return <Header text={state.context.count} />
}

const Controls = () => {
    const [state, send] = useMachine(counterMachine)
    return (
        <>
            <Header text={state.context.count} />
            <Button onClick={() => send('DECREMENT')} label="-" />
            <Button onClick={() => send('INCREMENT')} label="+" />
        </>
    )
}

const App = () => {
    return (
        <Router>
            <nav>
                <Link to="/">Home</Link>
                <Link to="/controls">Controls</Link>
            </nav>
            <Switch>
                <Route path="/controls">
                    <Controls />
                </Route>
                <Route path="/">
                    <Home />
                </Route>
            </Switch>
        </Router>
    )
}

export default App

```

哇，这真是太棒了！然而，我们在这里仅仅是[对状态机的皮毛](https://blog.logrocket.com/using-state-machines-with-xstate-and-react/)。要了解更多关于它们的信息，请查看 [XState](https://xstate.js.org/docs/) 的文档。

好吧，最后一个场景！如果我们简单的前端计数器应用程序有一个后端会发生什么？如果我们需要与服务器通信以获取或修改计数，该怎么办？此外，如果我们想要处理与数据获取相关的挑战，比如异步、加载状态、缓存和重新获取，那该怎么办？

## 用 Jotai 实现简单灵活的状态管理

我们已经在反冲部分介绍了原子模型，Jotai 采用了类似的方法。otai 甚至受到了反冲原子模型的启发，所以这对我们来说应该是小菜一碟。让我们重新实现我们的计数器应用程序，但是这次使用 Jotai。

```
import { BrowserRouter as Router, Switch, Route, Link } from 'react-router-dom'
import { atom, useAtom } from 'jotai'

// Create your atoms and derivatives
const countState = atom(0)

const Header = ({ text }) => {text}

const Button = ({ label, onClick }) => {label}

const Home = () => {
    const [count] = useAtom(countState)
    return 
}

const Controls = () => {
    const [count, setCount] = useAtom(countState)

    const increaseCount = () => {
        setCount(count + 1)
    }

    const decreaseCount = () => {
        if (count > 0) {
            setCount(count - 1)
        }
    }
    return (
        <>        
    )
}

const App = () => {
    return (
                        Home
                        Controls            

    )
}

export default App

```

我们可以看到 Jotai 和反冲是多么相似。用 Jotai 感觉也像用 React 的`useState`。

使用 Jotai，可以通过组合原子来创建状态，并根据原子依赖性来优化渲染。这消除了对记忆技术的需求，并且克服了 React 上下文的额外的重新呈现问题。

## 使用 MobX 进行简单、可扩展的状态管理

MobX 深受面向对象编程和反应式编程原则的影响。它允许您将特定的数据片段标识为“可观察的”，然后包装这些数据并跟踪对这些数据所做的任何更改，更新正在观察这些数据的任何其他代码。

使用 MobX 为我们的计数器应用程序重写状态管理相当容易，所以让我们这样做:

```
 import { BrowserRouter as Router, Switch, Route, Link } from 'react-router-dom'
 import { observable, action } from 'mobx';

const appState = observable({
  count: 0,
  incCounter: action("decrease", function () {
    appState.count += 1;
  })
  decCounter:action("increase", function() {
    appState.count -= 1;
  })
})

const Header = ({ text }) => {text}

const Button = ({ label, onClick }) => {label}

const Home = () => {
    const count = appState.count
    return 
}

const Controls = () => {
    const count = appState.count

    const increaseCount = appState.incCounter 

    const decreaseCount = () => {
        if (count > 0) {
            appState.decCounter
        }
    }
    return (
        <>        
    )
}

const App = () => {
    return (
                        Home
                        Controls            

    )
}

export default App

```

## 使用 Zustand 进行状态管理

Zustand 是一个强大而简洁的状态管理库。它的 API 是围绕钩子构建的，这使得它易于理解和使用。Zustand 解决了常见问题，如混合渲染器之间的[僵尸子问题](https://react-redux.js.org/api/hooks#stale-props-and-zombie-children)、[反应并发](https://github.com/bvaughn/rfcs/blob/useMutableSource/text/0000-use-mutable-source.md)和[上下文丢失](https://github.com/facebook/react/issues/13332)。

让我们使用 Zustand 设置我们的计数器应用程序:

```
 import { BrowserRouter as Router, Switch, Route, Link } from 'react-router-dom'
import create from "zustand";

const useStore= create((set) => ({
  count: 0;
  increment: ()=> set((state) => ({ count: state.count + 1})),
  decrement: ()=> set((state) => ({ count: state.count - 1}))
}))

const Header = ({ text }) => {text}

const Button = ({ label, onClick }) => {label}

const Home = () => {
    const count = useStore((state) => state.count)
    return 
}

const Controls = () => {
    const count = useStore((state) => state.count)

    const increaseCount = useStore((state) => state.increment)    }

    const decreaseCount = () => {
        if (count > 0) {
            useStore(useStore(state) => state.decrement)
        }
    }
    return (
        <>        
    )
}

const App = () => {
    return (
                        Home
                        Controls            

    )
}

export default App

```

Zustand 易于使用和设置；你需要做的就是创建一个商店(你的商店是一个钩子！)，如上例所示。存储可以包含任何东西，包括函数、对象和原语。我们现在可以在应用程序中使用钩子了。

Zustand 和 Redux 都基于不可变的状态模型，因此，如果您理解 Redux，您应该能够理解 Zustand。

## 使用 React 查询提取数据

我想强调的最后一个 React 状态管理工具是 [React Query](https://react-query.tanstack.com/) 。它是专门设计来使数据获取变得容易，并解决上面概述的问题(以及更多)。让我们看看它的实际效果。

```
import { BrowserRouter as Router, Switch, Route, Link } from 'react-router-dom'
import { ReactQueryDevtools } from 'react-query/devtools'
import axios from 'axios'
import {
    useQuery,
    useMutation,
    QueryClient,
    QueryClientProvider,
} from 'react-query'

const useCount = () => {
    return useQuery('count', async () => {
        const { data } = await axios.get('https://our-counter-api.com/count')
        return data
    })
}

const useIncreaseCount = () => {
    return useMutation(() =>
        axios.post('https://our-counter-api.com/increase', {
            onSuccess: () => {
                queryClient.invalidateQueries('count')
            },
        }),
    )
}

const useDecreaseCount = () => {
    return useMutation(
        () => axios.post('https://our-counter-api.com/descrease'),
        {
            onSuccess: () => {
                queryClient.invalidateQueries('count')
            },
        },
    )
}
const Header = ({ text }) => <h1>{text}</h1>

const Button = ({ label, onClick }) => (
    <button onClick={onClick}>{label}</button>
)

const Home = () => {
    const { status, data, error } = useCount()
    return status === 'loading' ? (
        'Loading...'
    ) : status === 'error' ? (
        <span>Error: {error.message}</span>
    ) : (
        <Header text={data} />
    )
}

const Controls = () => {
    const { status, data, error } = useCount()
    const increaseCount = useIncreaseCount()
    const decreaseCount = useDecreaseCount()

    return status === 'loading' ? (
        'Loading...'
    ) : status === 'error' ? (
        <span>Error: {error.message}</span>
    ) : (
        <>
            <Header text={data} />
            <Button onClick={() => decreaseCount.mutate()} label="-" />
            <Button onClick={() => increaseCount.mutate()} label="+" />
        </>
    )
}
const queryClient = new QueryClient()

const App = () => {
    return (
        <QueryClientProvider client={queryClient}>
            <Router>
                <ReactQueryDevtools />
                <nav>
                    <Link to="/">Home</Link>
                    <Link to="/controls">Controls</Link>
                </nav>
                <Switch>
                    <Route path="/controls">
                        <Controls />
                    </Route>
                    <Route path="/">
                        <Home />
                    </Route>
                </Switch>
            </Router>
        </QueryClientProvider>
    )
}

export default App

```

以上是一个相当幼稚的实现，有很大的改进空间。值得注意的是，我们可以轻松地进行服务器调用，缓存它们，并在需要时使缓存无效。此外，使用 React Query，管理组件中的加载和错误状态的任务变得更加简单。

这是一个伟大的工具，可以用于任何后端。如果你想知道如何用 GraphQL 设置它，可以看看我写的关于它的文章。

## 哪个状态管理工具最适合 React？

上面讨论的所有状态管理库都试图解决同一个问题，每个库都提供了一种独特的方法来处理整个应用程序中的共享数据。

找到最好的状态管理库取决于您正在进行的项目和您自己的个人偏好。对于 React 的`useState`非常适合这项工作的情况，有些库可能是多余的。

毫无疑问，Redux 一直是社区的最爱，它可以在许多旧的 React 代码库中找到。这样做的结果是，对 Redux 有一个透彻的了解总体来说确实是有益的。

一般来说，学习 Redux 和反冲是一个很好的前进路线。反冲以非常低的学习曲线有效地处理了状态管理问题，并且对 Redux 的透彻理解将大大减少维护旧的 React 代码库所需的时间。

## 结论

React 中的状态管理是一个广泛的话题。本文中讨论的方法、模式和库的列表既不全面也不明确。我们的目标是阐明以一种特殊的方式解决一个特定问题背后的思维过程。

最后，React 中的状态管理归结为意识到不同的选项，理解它们的好处和权衡，并最终选择最适合我们用例的解决方案。

编码快乐！✨

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