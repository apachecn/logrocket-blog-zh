# 用图例状态动态管理状态

> 原文：<https://blog.logrocket.com/react-state-management-legend-state/>

当使用 React 和 React Native 等前端框架构建现代应用程序时，管理状态始终是必要的，并且可能是开发人员最头疼的问题。开发人员需要确定正确的模式来管理应用程序中的状态。

本文将介绍如何使用 Legend-State 作为 React 应用程序的状态管理系统。我们将构建一个简单的例子来看看 Legend-State 是如何工作的，它的用例，以及它与其他状态管理工具有何不同。

向前跳:

## 什么是传奇国家？

[Legend-State](https://legendapp.com/open-source/state) 是一个新的快速状态管理库，它提供了一种优化应用程序的方法，以获得最佳的性能、可扩展性和开发人员体验。传奇之州由杰伊·梅斯特里奇建造和维护。Legend-State 设计有现代浏览器功能，被[传奇](https://www.legendapp.com/)和[勇敢](https://www.bravely.io/)等公司使用。

Legend-State 旨在解决在 React 应用程序中管理状态时开发人员的性能和工作负载问题，React 应用程序在默认情况下没有进行优化。图例状态通过仅在必要时重新渲染组件来提高应用程序的性能，并且它允许您控制在哪些状态下触发重新渲染。Legend-State 使 React 更容易理解，有了它，开发人员可以比简单的、未优化的 React 实现编写更少的代码。

有许多状态管理库，每个都有一组独特的功能。这就是传奇国度与他们的不同之处。

### 细粒度反应性

Legend-State 的反应能力让 React 应用程序更快。它最大限度地减少了渲染次数，并确保组件中的每次更新都会导致最少的渲染次数。使组件变小可以最大限度地提高 React 的效率，因为它要求状态更改只重新呈现最少的组件。每当状态更新通过 props 沿树向下传递时，组件树中的每个组件都会重新呈现。

Legend-State 的细粒度反应性使用两个组件来隔离子组件，以便它们根据其可观测量的变化进行重新渲染，而无需重新渲染父组件:

*   `Computed`:在这个组件中，子组件的改变不会影响或重新渲染父组件，但是父组件的改变会重新渲染子组件
*   `Memo`:与`Computed`类似，`Memo`不会重新渲染父组件变化的子组件。只有当它的可观测量发生变化时，它才会重新渲染

### 简单

Legend-State 非常容易使用，并且没有使用样板代码。它没有特殊的钩子、高阶组件、函数或上下文等。你只需要访问这些状态，你的组件就会自动更新。

### 又快又小

legend-State[超快](https://www.legendapp.com/open-source/state/fast/)，文件大小只有 3Kb，提高了一个网站或 app 的性能。图例状态被设计为尽可能高效，它只在有变化时才重新呈现组件。

### 未电离的

Legend-State 是非终结化的，允许团队声明全局或组件内的状态。Legend-State 在 React 组件中创建状态对象，然后通过`props`或`Context`将它们传递给子组件:

```
import { useObservable } from "@legendapp/state/react"

// via props
const ViaProps = ({ count }) => {
  return <div>Count: {count}</div>
}
const App = () => {
  const count = useObservable(0)
  return <ViaProps count={count} />
}

// via context
const ViaContext = () => {
    const count = useContext(StateContext);
    return (
        <div>
            Count: {count}
        </div>
    )
}
const App = () => {
    const count = useObservable(0)
    return (
        <StateContext.Provider value={count}>
          <ViaContext count={count} />
        </StateContext.Provider>
    )
}

```

### 持续状态

Legend-State 有内置的[持久性](https://www.legendapp.com/open-source/state/persistence)插件，可以从本地或远程存储中保存和加载。这些插件包括用于 web 上本地存储的本地提供者和 React Native 中的 [react-native-mmkv](https://github.com/mrousavy/react-native-mmkv) 。这些插件经过了全面的测试，以确保其准确性。Legend-State 正在为 web 和 React Native 开发 Firebase 和 Firestore 远程持久性插件。

此外，Legend-State 支持 TypeScript，可以在 React 本机应用程序中用于管理状态。

## React 应用程序中的图例状态入门

让我们构建一个简单的投票应用程序来了解 Legend-State 是如何工作的。首先，让我们用下面的代码创建一个项目:

```
npm create-react-app legend-state-app

```

这将初始化一个新的 React 项目。现在将`cd`添加到`legend-state-app`中，并安装图例状态:

```
npm install @legendapp/state

```

运行上面的命令后，我们的图例状态管理库将被安装在我们的项目文件夹中。

### 创建一个`Card`组件

我们将在`src/components`中创建一个`Card.js`文件来显示每个玩家的信息。这将显示玩家的姓名、国籍、国家，以及一个增加和减少投票的按钮:

```
import React from "react";
import { Computed } from '@legendapp/state/react';

const Card = (props) => {
    const { player, increasevoteCount, decreaseVoteCount } = props
    return (
        <section className="container">
            <h1 className="text">{player.name}</h1>
            <h3 className="normal-text">{player.country}</h3>
            <p className="normal-text">{player.club}</p>
               <button className="button" onClick={() => increasevoteCount(player.id)}>Vote</button>
               <button className="button normal" onClick={() => decreaseVoteCount(player.id)}>Unvote</button>
        </section>
    )
});
export default Card;

```

`player`、`increasevoteCount`和`decreaseVoteCount`函数的值将来自父组件:`App.js`作为道具。当你点击`Vote`按钮时，它调用`increasevoteCount`并用它传递`player`的`ID`，当你点击`decreaseVoteCount`时它也这样做。

### 用可观察的事物创造我们的国家

现在，让我们使用可观测量来定义我们的状态，它将包含我们的应用程序中使用的所有状态及其功能。可观察对象是包含任何变量(原语、数组、深度嵌套对象、函数)的对象，这些变量可以在状态发生变化时更新。

React 使用可观测量有不同的方式:

*   使用`enableLegendStateReact()`功能直接渲染可观察值，以自动提取状态，作为一个单独的、存储的组件及其跟踪上下文
*   使用`observer` HOC 使组件自动监控被访问的观察对象的变化
*   使用`useSelector`钩子来计算一个值会自动监控任何被访问的可观察值，并且只有当计算值改变时才会重新渲染结果

对于这个项目，我们将直接使用`enableLegendStateReact()`来渲染可观测的物体。用下面的代码更新我们的`App.js`文件:

```
import React from 'react';
import { enableLegendStateReact, Memo } from "@legendapp/state/react"
import { observable } from '@legendapp/state';

enableLegendStateReact();

const state = observable({
  players: [
    {id: 1, name: 'Messi', club: 'Paris', country: 'ARG',},
    {id: 2, name: 'Ronaldo', club: 'Manchester', country: 'POR'}
  ],
  voteForM: 0,
  voteForC: 0
})

```

我们使用图例状态库中的 observable 函数创建了一个状态。

在 observable 函数中，我们创建了一个保存玩家数据数组的存储和一个记录投票相关状态的存储，其初始值设置为 0。

### 访问和修改可观测量中的状态

为了访问`observable`的原始值，我们将使用`get()`函数:

```
function App() {
  const playersData = state.players.get();

  return (
    <section className='App'>
      <h2>Vote the Best Football player in the world</h2>
      <Memo>
        {() => <h1>Messi: {state.voteForM} - {state.voteForC}: Ronaldo</h1>}
      </Memo>
      <div className='card-container'>
        {
          playersData.map((player) => (
            <div key={player.id} className="card">
              <Card
               player={player}
               increasevoteCount={player.id === 1 ? voteForMessi : voteForRonaldo}
               decreaseVoteCount={player.id === 1 ? unVoteForMessi : unVoteForRonaldo}
              />
            </div>
          ))
        }
      </div>
    </section>
  )
export default App;

```

在上面的代码中，我们有一个包含玩家的 state 属性值的变量`playersData`。我们使用`playerData`变量来映射玩家的数组，并将每个玩家的数据传递给`Card`组件。

我们还看到函数`increasevoteCount`和`decreaseVoteCount`被传递给`Card`组件，并用于`increase`和`decrease`每个玩家的投票。因此，当我们单击`Card`组件中的`vote`或`unvote`按钮时，它会将玩家的`id` *值传递给`App.js`。*

 *要增加和减少投票，我们必须定义函数来处理操作:

```
// increase player vote 
 const voteForMessi = () =>  state.voteForM.set(state.voteForM.get() + 1)
 const voteForRonaldo = () => state.voteForC.set(state.voteForC.get() + 1)

// decrease player vote
  const unVoteForMessi = () =>  state.voteForM.set(state.voteForM.get() - 1)
  const unVoteForRonaldo = () => state.voteForC.set(state.voteForC.get() - 1)

```

该函数获取每个玩家的当前值，并增加或减少投票字段。图例状态观察值使用`set()`函数修改状态。还有其他功能:

### 向我们的应用程序添加样式

在这一点上，我们的应用程序看起来不是很好。因此，让我们添加一些样式来改变应用程序的外观。将`index.css`更新为以下代码:

```
*{
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
.container{
  display: block;
  padding: 10px;
  width: 100%;
  max-width: 50rem;
  color: rgb(17 24 39 );
  border-radius: 0.5rem;
  box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
}
.text{
  font-size: 1.5rem;
  line-height: 2rem;
  font-weight: 700;
  letter-spacing: -0.025em;
  color: rgb(17 24 39);
}
.normal-text{
  font-weight: 500;
  color: rgb(55 65 81 );
}
.button{
  display: block;
  width: 100%;
  align-items: center;
  padding: 0.75rem 0;
  font-weight: 700;
  font-size: 1rem;
  line-height: 1.25rem;
  border-radius: 0.5rem;
  border: none;
  margin: 6px 0;
  color: rgb(255 255 255);
  background-color: rgb(29 78 216);
}
.button.normal{
  background-color: rgb(255 255 255);
  color: rgb(29 78 216);
  border: 1px solid rgb(29 78 216);
}
.button.normal:hover{
  color: rgb(255 255 255);
  background-color: rgb(29 78 216);
}
.button:hover{
  background-color: rgb(21, 57, 156);
}
.App {
  text-align: center;
  padding: 10px;
  line-height: 2rem;
}
.card{
  margin: 6px 0;
}
@media (min-width: 768px) {
  .card-container{
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 20px;
    gap: 20px;
  }
  .card{
    margin: 6px 0;
    width: 30%;
  }
}

```

一旦我们运行`npm start`来启动应用程序，我们得到的是:

![Voting App Example](img/c920457d36233ed16512d205ca4deca6.png)

### 持续状态

Legend-State 提供了一个插件，用于保存状态，防止数据丢失。Legend-State 使用`persistObservable`将来自应用程序的数据保存在本地存储或远程存储中。这样，无论我们重新加载还是关闭页面，状态都不会重置。

为了将我们的数据存储在本地存储中，我们将导入插件并对其进行全局配置:

```
// App.js

import { configureObservablePersistence, persistObservable } from '@legendapp/state/persist';
import { ObservablePersistLocalStorage } from '@legendapp/state/local-storage';

configureObservablePersistence({
  // Use Local Storage on web
  persistLocal: ObservablePersistLocalStorage
});

```

现在，让我们为我们想要坚持的每个可观察值调用`persistObservable`:

```
persistObservable(state.voteForC, {
  local: 'voteC',
})
persistObservable(state.voteForM, {
  local: 'voteM',
})

```

上面，我们保存了`state.voteForC`和`state.voteForM`的值。本地存储密钥被分配一个唯一的名称。有了这些，我们在投票和刷新页面时不会丢失任何新数据。

### 带有图例状态的 API 请求

Legend-State 还提供了在 React 组件中使用的钩子，就像普通的`useState`钩子和`useEffect`钩子一样。但是这一次，它只在必要的时候呈现可观察的事物。我们将使用这个钩子发出一个 API 请求:

```
import {useObservable, useComputed, useObserve, Show } from '@legendapp/state/react'

  const user = useObservable(() =>
    fetch("https://randomuser.me/api/").then(response => response.json())
  )

```

这里，我们使用了`useObservable`钩子来保存并使用`fetch`函数发出一个 API 请求，该函数获取关于一个随机用户的信息。`useObservable`钩子也有助于在一个状态中本地保存多个值，或者当该状态特定于组件的生命周期时:

```
 const details = useComputed(() => {
    const u = user.results.get()
    return u ? `${u[0].name.first} ${u[0].gender} ${u[0].location.country}` : "";
  });

  useObserve(() => console.log(details.get()))

```

我们使用`useComputed`返回用户的姓名、性别和国家。`useComputed`钩子用于根据许多可观察值计算值，如果其中一个值发生变化，钩子将被更新，因为它跟踪自动计算时访问的可观察值。

`useObserve`类似于`useEffect`。它只在可观察到的变化时采取行动:

```
  return (
    <div>
      <Show if={userName} else={<div>Loading...</div>}>
        <div>
          <h1>{userName[0].name.first} {userName[0].name.last}</h1>
          <p>{userName[0].location.country}</p>
          <h2>{userName[0].email}<h2>
        </div>
      </Show>
    </div>
  );

```

这里，我们使用 Legend-State 中的`Show`组件有条件地呈现我们的数据。`Show`组件用于根据`if` / `else`道具有条件的渲染子组件，当条件改变时，父组件不渲染。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 结论

在本文中，我们了解了 Legend-State，这是 React 和 React 本机应用程序的状态管理库，致力于为我们的应用程序提供更好的性能，并为开发人员提供更好的体验。我们使用 Legend-State 创建了一个投票应用程序来管理状态，并详细说明了 Legend-State 与其他状态管理器的不同之处。祝您在下一个 React 或 React 本机应用程序中使用 Legend-State 时愉快！

## 资源

使用 LogRocket 消除传统反应错误报告的噪音

## 是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

Focus on the React bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/react-signup-issue-free).*