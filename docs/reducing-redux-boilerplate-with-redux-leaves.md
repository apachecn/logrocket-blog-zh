# 用 Redux-Leaves 减少 Redux 样板文件

> 原文：<https://blog.logrocket.com/reducing-redux-boilerplate-with-redux-leaves/>

Redux 是一个用一个问题换另一个问题的软件库的典型例子。

虽然 redux 使您能够使用 flux 模式全局管理应用程序状态[，但它也导致您的应用程序充满乏味的样板代码。](https://www.freecodecamp.org/news/an-introduction-to-the-flux-architectural-pattern-674ea74775c9/)

即使是最简单的更改也需要声明类型、操作，并向已经庞大的 switch 语句添加另一个 case 语句。

随着状态和变化的复杂性不断增加，你的规约变得更加复杂和费解。

如果你能去掉大部分样板文件会怎么样？

## 我们不只是写 Redux，我们也谈论它。现在听着:

或者以后订阅

### 回车:Redux-Leaves

## Redux-Leaves 是一个 JavaScript 库，它为如何处理 Redux 应用程序中的状态变化提供了一个新的框架。在标准的 redux 设置中，有一个或几个控制器管理应用程序的不同部分。

相反，Redux-Leaves 将数据的每个节点，或其术语中的“叶”，视为一等公民。每片叶子都自带还原器，不用写了。

这使您能够从应用程序中删除大量样板文件。

让我们比较这两种方法，然后看看如何从传统的 redux 设置迁移到使用 Redux-Leaves 的设置。

如何开始使用 Redux-Leaves

### 让我们首先构建一个简单的 greenfield 应用程序，它只使用 redux 和 Redux-Leaves。这样，您可以在尝试将该工具添加到现有项目之前对其进行测试。

然后，我们将看看如何在现有项目中添加冗余叶。我们将使用`create-react-app`通过构建链和其他工具快速建立一个环境。

开始您的项目

### 对于这个例子，我们将使用 Twitter 作为我们的模型。我们将存储一个推文列表并添加到其中。

```
npx create-react-app my-redux-leaves-demo && cd my-redux-leaves-demo
yarn init
yarn add redux redux-leaves
```

在一个`store.js`文件中，让我们看看 redux 案例，并将其与 Redux-Leaves 的工作方式进行比较。

添加记录:Redux 版本

### 通常，每当您需要向 state 添加新的突变时，您会创建:

类型常数

*   动作创建者功能
*   减速器开关语句中的一个实例。
*   下面是我们添加推文的 redux 示例:

添加记录:Redux-Leaves 版本

### 使用 Redux-Leaves，不需要定义一个 reducer 函数。Redux-Leaves 初始化函数提供了一个我们可以传递给`createStore`的缩减器。

```
import { createStore } from 'redux'

const initialState = {
  tweets: [],
}

const types = {
  ADD_TWEET: 'ADD_TWEET',
}

const actions = {
  pushTweet: (tweet) => ({
    type: types.ADD_TWEET,
    payload: tweet,
  })
}

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case 'ADD_TWEET':
      return {
        ...state,
        tweets: [
          ...state.tweets,
          action.payload,
        ]
     }
  default:
    return state
  }
}

const store = createStore(reducer)
store.dispatch(actions.pushTweet({ text: 'hello', likes: 0 }))
```

此外，它提供了一个 actions 对象，该对象提供了 action creator 函数，因此我们也不必担心从头开始编写这些函数。

考虑到所有这些，没有必要声明类型常量。再见，样板！

下面是一段与上面代码功能相当的代码，用 Redux-Leaves 编写:

比上一个例子简洁多了。随着您需求的增长，结果会更加激烈。

```
import { createStore } from 'redux'
import { reduxLeaves } from 'redux-leaves’

const initialState = {
  tweets: [],
}

const [reducer, actions] = reduxLeaves(initialState)
const store = createStore(reducer)

store.dispatch(actions.tweets.create.push({ text: 'hello', likes: 0 }))
```

在一个标准的 redux 应用程序中，您必须为每一个变异编写新的类型并扩展您的 reducer。

Redux-Leaves 处理许多开箱即用的情况，所以情况并非如此。

更多来自 LogRocket 的精彩文章:

* * *

### 你如何调度这些变异？

* * *

带有 Redux-Leaves 内置动作创建器。状态中的每一段数据都是一片树叶。在我们的例子中，tweets 数组是一片树叶。

有了对象，叶子可以嵌套。tweet 本身被认为是一片叶子，它的每个子字段也是一片叶子，以此类推。每个都有自己的动作创建者。

各种数据类型的操作创建器概述

## Redux-Leaves 为每种类型的叶子提供了三个动作创建器，不管是哪种类型:

**更新**:将一片叶子的值设置为你想要的任何值

*   **复位**:将一片叶子的值设置回初始状态
*   **清除**:取决于数据类型。数字变成 0。布尔变成假的。字符串、数组和对象变空(分别为`''`、`[]`和`{}`)
*   除此之外，Redux-Leaves 还提供了一些特定于类型的创建器。例如，布尔类型的叶子具有开、关和切换动作创建器。

有关完整列表，请参考 [Redux-Leaves 文档](https://redux-leaves.js.org/docs/defaults/push)。

创建动作的两种方法

### 您可以直接使用 create 函数并以这种方式调度操作，也可以声明可以在其他地方调用的操作。

第二种方式更接近于 redux 当前的运行方式，但也因此产生了更多的样板文件。

我将由您来决定哪种方法最适合您的需求。

用 bundle 创建复杂的动作

```
// method #1
store.dispatch(actions.tweets.create.push({ text: 'hello', likes: 0 }))

// method #2
const addTweet = actions.tweets.create.push
store.dispatch(addTweet({ text: 'hello', likes: 0 }))
```

### 样板代码可以节省时间，但是它不能处理每一个真实的用例。如果你想一次更新多个叶子怎么办？

Redux-Leaves 提供了一个捆绑功能，将许多动作合并为一个。

如果你想在添加推文时跟踪最近的时间戳，它应该是这样的:

第一个参数是要调度的操作数组，第二个参数是可选的自定义类型。

```
const updateTweet = (tweet) => bundle([
  actions.most_recent.create.update(Date.now()),
  actions.tweets.create.push(tweet),
], 'UPDATE_WITH_RECENCY_UPDATE')

store.dispatch(updateTweet({ text: 'hello', likes: 0 }))
```

但即使这样，也可能有一些情况是这样的。如果你的减速器需要更多的逻辑怎么办？

如果在更新状态的一部分时需要引用另一部分，该怎么办？对于这些情况，也可以编写定制的叶子减少器。

这种可扩展性使得 Redux-Leaves 大放异彩:它提供了足够的内置功能来处理简单的用例，并能够在需要时扩展该功能。

使用叶 reducer 创建自定义 reducer 操作

### 发微博时，用户只需在文本框中输入内容，然后点击提交。

他们不负责提供所有的元数据。一个更好的 API 应该是只需要一个字符串来创建一条 tweet，并抽象出实际的结构。

这种情况是定制叶缩减器的一个很好的用例。

叶 reducer 的核心形状与其他 reducer 相同:它接受状态和动作，并返回状态的更新版本。

但是，它们的不同之处在于，叶缩减器不直接与单个数据相关。在应用程序的任何叶上都可以调用叶缩减器。

这是 Redux-Leaves 帮助你避免重复的另一种方式。

还要注意叶 reducer 中的`state`并没有引用整个全局状态——只引用了它被调用的叶。

在我们的例子中，`leafState`是 tweets 数组。

如果需要引用全局状态，可以将它作为可选的第三个参数传入。

向`reduxLeaves`功能添加自定义叶减速器。对象中的密钥成为它在应用程序中的函数签名。

```
const pushTweet = (leafState, action) => [
  ...leafState,
  {
    text: action.payload,
    likes: 0,
    last_liked: null,
    pinned: false,
  }
]
```

然后，为定制的 reducers 分派动作看起来就像内置的一样:

```
const customReducers = {
  pushTweet: pushTweet,
}

const [reducer, actions] = reduxLeaves(initialState, customReducers)
const store = createStore(reducer)
```

输出以下内容:

```
store.dispatch(actions.tweets.create.pushTweet('Hello, world!'))
console.log('leaves version', store.getState())
```

迁移到落叶层

```
{
  tweets: [
    { 
      text: “Hello, World!”,
      likes: 0, 
      last_liked: null, 
      pinned: false, 
    }
  ]
}
```

### 如果你正在做一个现有的项目，并考虑移动 Redux-Leaves，你可能不想一下子把整个项目都拿出来。

更安全的策略是一次一个动作地替换现有的 redux 代码。

如果您已经为您的应用程序准备好了测试——在尝试像这样重构一个库之前，您应该这样做——那么这个过程应该是平稳而简单的。

替换一个动作并运行测试。当他们通过时，重复。

为此，我推荐使用 [reduce-reducers](https://www.npmjs.com/package/reduce-reducers) Redux 实用程序。减压器-减压器可将现有的减压器与新的减压器相结合。

有了这个工具，无需重写任何代码就可以向应用程序添加 Redux-Leaves。

```
yarn add reduce-reducers
```

此更新不应改变您的应用程序的行为。旧的 reducerss 和新的 reducer 都可以更新存储。

```
import { createStore } from 'redux'
import { reduxLeaves } from 'redux-leaves'
import reduceReducers from 'reduce-reducers’

Const initialState = {
  // initial state
}

const myOldReducer = (state = initialState, action) => {
  // big case statement goes here
}

const leafReducers = {} // we’ll put custom reducers here if/when we need them

const [reducer, actions] = reduxLeaves(initialState, leafReducers)

const comboReducer = reduceReducers(myOldReducer, reducer) 

const store = createStore(comboReducer)
```

因此，您可以逐个删除和替换操作，而不是一次重写所有内容。

最终，您将能够在不改变功能的情况下做出一个美味的拉请求，使您的代码库缩短几千行。

如果您愿意的话，这一更改允许在不修改现有案例的情况下对新代码使用 Redux-Leaves。

结论

## 在我的书中，通过添加另一个库来消除一个库的复杂性是一个违反直觉的主张。

一方面，您可以利用 Redux-Leaves 来减少样板代码并提高开发人员添加功能的速度。

然而，添加另一个库意味着团队中的开发人员需要熟悉另一个 API。

如果你独自工作或者在一个小团队中工作，那么学习曲线可能不是问题。只有您和您的团队才能知道 redux 是否是您项目的正确决策。

减少的代码库和更快的开发速度值得增加的依赖性和学习吗？这取决于你。

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).