# Redux 没有死- LogRocket 博客

> 原文：<https://blog.logrocket.com/redux-isnt-dead/>

Redux 仍然是前端生态系统中最受关注的库之一，这是有充分理由的。它的可预测性允许我们编写跨平台(客户端、本机和服务器)行为一致的应用程序，它易于调试，并且可以与任何 UI 层一起工作。

但是围绕 Redux 的对话并不总是积极的；事实上，Redux 团队[去年发布了 Redux Toolkit](https://blog.logrocket.com/smarter-redux-with-redux-toolkit/) 以回应许多对它挥之不去的批评。尽管如此——或者正因为如此——许多国家管理的新选择已经出现，由此得出结论，Redux 可能最终会被淘汰。

根据标题，你已经知道了这篇文章的立场——不，Redux 没有死。为了捍卫这一立场，我们将介绍以下内容:

## 在我们的新播客中，我们采访了 Redux 的主要维护者 Mark Erikson。

### 现在听着。

## 为什么要用 Redux？

在软件开发中，哪里有问题，我们可以肯定有人会很快找到解决方案。创建 Redux 是为了解决状态管理的问题。

在 React 中，我们可以在顶级组件中有一个全局状态。这个顶层组件将状态作为道具向下传递给需要数据的组件(即子组件)。

```
class App extends Component {
    constructor() {
        super()
        this.state = {
           friends: [
              { name:'Victor', age: 22 }, 
              { name:'Matt', age: 30 }, 
              { name:'Kate', age: 40 }
           ],
        }
    } 

    render() {
        const allFriends = this.state.friends.map(friend => friend.name)   
        return (
            <div className="tc">
                <h1 className="f1 pa">Friends</h1>
                <FriendList names ={allFriends}/>
            </div>
        );
    }   
}

```

在上面的代码示例中，子组件`FriendList`接收数据`allFriends`作为道具。它也可以被再次传递下去。(我知道我不应该在 2021 年的一个类组件中这样做，但你明白这个想法。)

顶层组件中的状态总是可以更新的。子组件将通过从顶层组件接收要更新的函数来更新全局状态。

```
class App extends Component {
    constructor() {
        super()
        this.state = {
           friends: [
              { name:'Victor', age: 22 }, 
              { name:'Matt', age: 30 }, 
              { name:'Kate', age: 40 }
           ],
          searchField: ''
        }
    } 

        onSearchChange = (event) => {
          this.setState({searchField: event.target.value}) 
        }  

    render() {
        const filteredFriends = this.state.friends.filter(friend => {
            return friend.name.toLowerCase().includes(this.state.searchField) 
        })

        return (
            <div className="tc">
                <h1 className="f1 pa">Friends</h1>
                <SearchBox searchChange={this.onSearchChange}/>
                <FriendList names ={filteredFriends}/>
            </div>
        );
    }   
}

```

在上面的示例代码中，我们的`searchField`状态总是使用`onSearchChange`函数更新，并传递给`SearchBox`组件。

下面是我们的`SearchBox`接收更新函数的样子:

```
const SearchBox = ({searchChange}) => {
    return (
        <div> 
            <input
            className="br3 pa2 b bg-light-blue" 
            type="search" 
            placeholder="Search Robots"
            onChange={searchChange}
            />
        </div>
    );
}

```

当您的应用程序变得更大时，所有这些钻取和处理组件 *C* 从组件 *A* 获取数据的工作都变得很麻烦。

最近有争论说我们并不严格需要 Redux，但事实并不如此明确。直到您构建了一个状态管理不那么简单的应用程序，您才会意识到您需要 Redux。

> 我想修改这一点:不要使用 Redux，直到你有香草反应的问题。见[https://t.co/RhzRGzEIe0](https://t.co/RhzRGzEIe0)@ cam Jackson 89

同样的，前 React 团队成员 Pete Hunt，[也说](https://github.com/petehunt/react-howto#learning-flux)“你会知道什么时候你需要 Flux。如果你不确定你是否需要它，你就不需要它。”如果你不确定你是否需要 X，那么你就不需要它。

所以，概括一下，我们应该使用 Redux，如果:

*   我们的状态会经常更新；在这里，Redux 提供了“真理的单一来源”
*   我们的应用程序有许多组件需要的大量状态
*   我们更新状态的功能/逻辑将会很复杂

就像其他工具一样，Redux 也有它的缺点和不足。

## Redux 的替代方案

在我们深入 Redux 替代方案之前，必须清楚状态管理有不同的架构。React 和其他 UI 库都使用这些模式来管理状态及其数据流。

伊尔哈姆·瓦哈比在这条推文中简洁地总结了三种模式:原子、代理和流动。

### 原子的

这个架构类似于 React 用于上下文 API 和`useState`的架构。这种模式允许您将状态创建为一个原子，并将它们分割成更小的原子，不像 Redux store，它是一个包含所有状态的大容器。

Jotai 就是使用这种架构的一个例子。让我们来看一个代码示例。

```
import { atom } from 'jotai'

const countAtom = atom(0)
const friendAtom = atom('Matt')
const friendsAtom = atom(['Victor', 'Matt', 'Kate'])

```

上述状态被分裂成更小的部分，并被视为一个原子。在组件中使用特定的原子将如下所示:

```
import { useAtom } from 'jotai'

const Friend = () => {
  const [friend] = useAtom(friendAtom)
  return (
    <div>
      <p>{friend}</p>
    </div
  )
}

```

[反冲](https://blog.logrocket.com/simple-state-management-react-recoil/)是另一种使用原子架构的 Redux 替代方案。

### 代理人

这种模式使用 [JavaScript `Proxy`对象](https://www.javascripttutorial.net/es6/javascript-proxy/)来访问状态。`Proxy`包装一个对象，变异该对象的传统行为。它的主要目的是创建自定义行为或重新定义基本运算符。

MobX 和 Valtio 是使用这种架构的两个流行的状态管理库。根据 Valtio 的说法，它将你传递给它的对象变成一个自我感知的代理。

```
import { proxy, useSnapshot } from 'valtio'

const bio = proxy({ age: 23, name: 'Victor' })  

```

在应用程序中的任何位置对状态进行更改:

```
bio.friends = { name: 'Matt' }
bio.friends.amount = { number: 1 }

```

或者从代理复制数据以用于呈现:

```
function Bio() {
  const snap = useSnapshot(bio)
  return (
    <div>
      {snap.name}
      <button onClick={() => ++bio.friends.amount}>+1</button>
    </div>
  )
}

```

Valtio 强迫您从快照中读取，并从其来源进行变异。该组件肯定会重新呈现状态中被改变的部分。

### 流量

Redux 和 Zustand 用的是 Flux 架构。这个模式有几个组件链接在一起处理状态:动作、调度程序、存储和控制器视图。

我们将以 Zustand 为例；它没有 Redux 大，样板文件也少得多。

```
import create from 'zustand'

const useStore = create(set => ({
  myName: 'Victor',
  age: 23,
  friends: 0,
  increaseFriends: () => set(state => ({ friends: state.friends + 1 })),
  loseAllFriends: () => set({ friends: 0 })
}))

```

Zustand 把我们店当钩子。`set`关键字将状态组合成`useStore`。

在组件中使用状态非常简单。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
function BearCounter() {
  const bears = useStore(state => state.bears)
  return <h1>{bears} around here ...</h1>
}

function Bio() {
  const name = useStore(state => state.myName)
  const increaseFriends = useStore(state => state.increaseFriends)
  return (
    <h1>I am {name}</h1>
    <button onClick={increaseFriends}>Increase My Friends</button>
  )
}

```

基于它们的架构，上面提到的替代方案——Jotai、反冲、Zustand 和 Valtio——有不同的管理状态的方法，在许多方面，它们是作为对 Redux 使用的模式的响应而出现的。根据您的需求，您的应用程序可能会受益于使用原子方法(Jotai 或反冲),甚至是 Zustand 的 flux-like 方法，而不是 Redux，因为它的 API 很少。

## 常见的冗余误解

### Redux 有太多不必要的样板文件

Redux 受到了 JavaScript 社区的大量抨击，不仅因为它的“样板”代码，还因为它的学习曲线。许多开发人员忽略了一个事实，Redux 使用了一种需要大量样板代码的设计模式:flux 架构，它利用其单独的组件进行状态管理。

Flux 使用 action 组件(方法)将数据传递给 dispatcher。调度程序获得动作，并帮助重新分配将状态保存到其回调的属性。这个属性就是我们所知的有效载荷*。*

然后，存储充当我们的状态和逻辑容器，它链接到回调。所有这些操作都需要大量的样板文件。甚至那些抨击 Redux 的人也不得不佩服这种架构。

### Redux 太夸张了

丹·阿布拉莫夫本人毫不掩饰这样一个事实:你的项目可能不需要 Redux。我会说，只有当你的应用程序扩展并变得复杂时，你才需要 Redux 一个较小的应用程序或个人项目可能可以很好地使用上下文 API。

说到上下文 API，当您必须共享全局状态数据时，它工作得很好——不需要一直将数据作为道具传递给每个组件。事实上，有时这就足够了，像 Redux 这样复杂的外部状态管理解决方案是多余的。但是，我们应该清楚，上下文不是状态管理解决方案，而是将数据带到嵌套组件的更简单的方法。

同样，有些人声称 Redux 已经死了，因为 React 的钩子 API 已经完全能够处理状态，尤其是当[与上下文 API](https://blog.logrocket.com/use-hooks-and-context-not-react-and-redux/) 一起使用时。这并不完全是错误的，但这种反弹很大程度上是支柱钻井压力的结果。如果你不想非要把道具钻成组件，那么 Redux 应该不是你的选择。

我的观点是，对 Redux 的很多批评都是这种误解的结果。许多开发者很少使用 Redux，或者在不必要的时候使用它。选择 Redux 之前最好先了解你的项目。

## Redux 的未来

在许多方面，未来现在都归功于 Redux Toolkit (RTK)。这个工具包有助于 Redux 默认提供的大量样板代码，提供了简化的存储设置、reducers 和动作。它还包括所有与 Redux 一起使用的软件包，因此我们不必单独安装它们——如 Reselect、Redux Thunk、Immer 等软件包。

安装这个工具包很容易:

```
# In your already existing React application, run either of these commands

# npm
npm install @reduxjs/toolkit

# Yarn
yarn add @reduxjs/toolkit

```

RTK 提供的 API 有`createStore()`、`createReducer()`、`createAction()`和`createSlice()`，有助于简化 Redux 样板代码。这是对 Redux 开发的一个改进。

为你制造减速器和动作，并连接它们。React Redux 现在也有了`useDispatch`和`useSelector`API，这意味着您可以连接到调度操作和存储，而不必使用 HOC。

在我看来，Redux 在未来几年不会去任何地方，因为它与 React 非常契合。对这两个库都没有依赖；React 将用于应用程序的视图端，而 Redux 处理状态。

在状态管理方面，钩子和上下文仍然没有 Redux 强大。事实上，已经清楚地解释了它们不是真正的状态管理解决方案。它们只是状态提供者——您仍然需要在组件中提供逻辑。

Redux 采用了发布/订阅架构:您的组件订阅了状态更改，并且组件还可以使用调度来发布状态更改。在这种情况下，您可以尝试使用上下文 API，但是最终您可能不得不使用 Redux。关于使用上下文进行状态管理[如何会降低应用程序性能](https://blog.theodo.com/2019/07/how-i-ruined-my-application-performances-by-using-react-context-instead-of-redux/)的第一手故事已经有了。

重点是 Redux 给你的不仅仅是状态管理。从设计模式到应用程序增长时的易维护性和可伸缩性，您可以将状态管理逻辑从 UI 层中分离出来。Redux 仍然有用，仍然有前途，而且肯定不会死。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)