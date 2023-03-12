# 用 Zustand - LogRocket 博客管理反应状态

> 原文：<https://blog.logrocket.com/managing-react-state-zustand/>

***编者按:**这篇文章于 2022 年 2 月 14 日更新，删除了任何过时的信息，并添加了使用 Zustand 保持状态的* *[部分。](#using-zustand-persist-state)*

在现代前端框架中管理状态，Redux 一直是王者。但现在，许多新的竞争对手带着新的想法加入了战斗，不顾一切地用易用性和简单性的承诺推翻 Redux。

拿我来说，我对越来越多的管理你的状态的新方法感到兴奋。在本文中，我将介绍最简单和最小的:Zustand。

您将了解 Zustand 是什么，它与 Jotai 或反冲等其他现代工具有何不同，以及何时应该使用它而不是 Redux 总之，我们将涵盖以下内容:

## Zustand 是什么？

首先，我并不是说 [Zustand](https://zustand.surge.sh/) 是目前最好用的工具。在大多数情况下，哪个工具是最好的这个问题实际上无法回答，或者至少必须用一句可怕的话来回答，“这要看情况。”

![Tweet That Reads, "Becoming A Senior Engineer Is Just Saying "It Depends" Over And Over Again Until You Quit/Retire"](img/dcd21161153411404c357ad039ccf34d.png)

为了全面了解 Zustand，让我们回顾一下该库的一些细节，它在市场上的定位，并与其他库进行比较。

Zustand 是由`react-spring`、`react-three-fiber`和许多其他令人敬畏的工具、[poimanders](https://pmnd.rs/)的创造者创建和维护的。它只有 1.5kB，可能是所有库中最小的——你可以在几分钟内通读源代码。

## Zustand 入门

Zustand 以简洁著称。在他们为自己的软件包创建的(非常漂亮的)网站上，您可以看到一个非常简单的示例，它只用四行代码就创建了一个全局可用的状态:

```
import create from 'zustand'

const useStore = create(set => ({
  bears: 0,
  increasePopulation: () => set(state => ({ bears: state.bears + 1 })),
}))

```

状态管理库的主要功能叫做`create`。它接受一个回调函数作为第一个参数，第一个参数又接受一个在操作内存时应该使用的`set`函数。

函数`create`然后返回另一个函数，在我们的例子中，这个函数叫做`useStore`。从返回值的名称可以看出，它返回一个钩子，因此可以将其插入 React 应用程序中的任何位置，如下所示:

```
>function BearCounter() {
  const bears = useStore(state => state.bears)
  return <h1>{bears} around here ...</h1>
}

```

无论在哪里注入这个钩子和使用状态，当状态改变时，组件将重新呈现，用这些小代码行使它成为一个全功能的全局状态。

您还可以从同一个钩子中提取动作，该动作可以在任何地方改变状态，如下所示:

```
function Controls() {
  const increasePopulation = useStore(state => state.increasePopulation)
  return <button onClick={increasePopulation}>one up</button>
}

```

但是，如何执行异步操作或者从服务器获取保存到存储中的内容呢？

嗯，你可以让你的变异函数异步，Zustand 会在它准备好的时候设置你的状态。这样，您就不再需要担心组件内部的异步函数了:

```
const useStore = create(set => ({
  fishies: {},
  fetch: async pond => {
    const response = await fetch(pond)
    set({ fishies: await response.json() })
  }
}))

```

状态管理不能再简单了吧？但它看起来很像其他现代工具，如 Jotai 或反冲，你说呢？看起来似乎是这样，但是让我们来看看这些库之间的一些差异。

## 使用 Zustand 保持状态

使用全局状态管理工具的一个最常见的用例是，您希望在网站的整个生命周期中保持您的状态。例如，如果您创建了一个调查工具，您希望保存用户的答案和状态。

现在，如果用户不小心重新加载你的页面，所有的答案和指针都会丢失。这是一个常见的用例，您希望保持这种确切的状态。

也就是说，即使用户重新加载页面或关闭窗口，响应和状态也会被保留，并且可以在用户再次访问站点时恢复。

Zustand 用一个叫做`persist`的漂亮的“内置电池”中间件解决了这个特殊的用例，它可以以你想要的任何方式保存你的商店。要在应用程序的`sessionStorage`中保持状态，您唯一需要做的就是添加以下内容:

```
import create from "zustand"
import { persist } from "zustand/middleware"

export const useStore = create(persist(
  (set, get) => ({
    anwers: [],
    addAnAnswer: (answer) => set((prevState) => (
      { answers: [...prevState.answers, answer] }
    ))
  }),
  {
    name: "answer-storage", // unique name
    getStorage: () => sessionStorage, // (optional) by default the 'localStorage' is used
  }
))

```

如您所见，该商店与没有持久性的商店完全相同。唯一的区别是它额外包装了持久中间件。您还需要给商店一个唯一的名称，以便在浏览器中识别它。

可选地，您还可以决定您想要哪种持久性；默认情况下，这是`localStorage`，但是如果您希望状态只在用户的会话中保持不变，您也可以选择`sessionStorage`。

## Zustand 比 Redux 好吗？

在管理全局状态方面，Redux 可能仍然是使用最广泛的库。然而，像 Zustand 这样的库试图用一种更实用、更简单的方式来解决管理全局状态的问题。

让我们来看看 Zustand 和 Redux 到底有什么不同。

### 简单

使用 Redux 的一个缺点是您必须编写大量的代码来获得全局状态。您必须创建 reducers、actions 和 dispatch 函数来改变一个非常简单的状态。

Zustand 的强大之处在于，创建一个全局状态只需四行代码。也就是说，如果你的状态很简单，状态可以节省你很多时间。

### 范围

Redux 和 Context 一样，需要用一个`provider`组件来包装，该组件将状态注入到所有用`provider`打包的组件中，这样就可以在所有打包的 React 组件中使用该状态。

有了 Zustand，这就没必要了。创建存储后，您可以将它注入到任何您想要的地方，并为项目中的所有组件注入一次。但这可能是 state 的最大优势之一:代码不在 React 中。

因此，您可以在没有 React 组件的情况下调用的函数中从您的状态获取数据。例如，在向后端发出请求之前使用请求函数。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 开发者体验

像 Redux 这样受欢迎并且已经存在一段时间的库的最大优势之一是，开发人员体验、文档和社区都要好得多，因此更容易找到帮助或问题的答案。

即使我会说 Redux vs. Zustand 也是如此，但我认为 Zustand 的开发者体验仍然是积极的。文档类似于库本身，实用，并专注于您实际需要的东西。

显然，它没有像 Redux 那样被广泛使用，因此社区和资源也没有被广泛传播。然而，因为这个库非常简单，所以没有那么多问题、难题或教程。

除了社区和文档，当讨论 Redux 与另一个库相比时，你得到的第一个论点是 Redux 开发工具是强大的。

首先，我不认为你应该仅仅通过调试工具来决定一个库，但是这是一个有效的论点。但是在 Zustand 中，你可以像在 Redux store 中一样使用[调试工具](https://github.com/pmndrs/zustand#redux-devtools)。是不是很神奇？

![Redux Debug Tool](img/0e58514cbfb91eb00cb9050b8b3adf42.png)

## 祖斯坦德 vs 约泰 vs 反冲

有趣的是， [Jotai](https://github.com/pmndrs/jotai/issues/13) 库和 Zustand 出自同一位创作者。但是，区别在于心智模式和你如何构建你的应用程序。

[根据 Jotai 文档](https://jotai.org/docs/basics/comparison),“Zustand 基本上是一个商店(你可以创建多个商店，但它们是分开的。)Jotai 是构成它们的原始原子。从这个意义上说，这是对心智模型进行编程的问题。

“Jotai 可以看做是`useState+useContext`的替代品。原子共享一个大的上下文，而不是创建多个上下文。Zustand 是一个外部商店，挂钩是将外部世界连接到 React 世界。”

在我看来，最后一句话是最重要的一句话，它让 Zustand 与其他状态管理工具如此不同。它基本上是为 React 构建的，但并不依赖于 React。

这意味着它可以成为连接反应世界和非反应世界的工具。这怎么可能呢？因为状态不是建立在 React 的上下文 API 之上的。您可能还注意到，在安装过程中，您不需要在应用程序中的某个地方添加根提供者。

## 是什么让 Zustand 如此特别？

Zustand 有两点给我留下了深刻的印象:它不仅适用于 React，而且是 100%非个人化的。

我以前提到过它，但使 Zustand 成为一个伟大工具的是它不依赖于 React 上下文，因此，不依赖于 React 应用程序或 React 本身。

例如，你可以组合不同应用程序的状态，不管它们使用什么框架(我看着你，微前端)。

而且，它完全没有个人化。虽然这听起来很明显，但在 React 的状态管理世界中，我立即加入了 Redux 生态系统的行列，甚至没有考虑它会带来什么好处。

Zustand 是简单性战胜工程性的一个例子(对于 Jotai 或反冲等其他库也是如此)。

## 使用 Zustand 的缺点

总的来说，Zustand 对于实用主义程序员和那些使用 React 的人来说是一个很棒的库，但是要与另一个库结合使用。

然而，Zustand 也有它的缺点。首先，文档可以改进。截至撰写本文时，目前唯一的文档是[项目的自述文件](https://github.com/pmndrs/zustand/blob/master/readme.md)。

虽然它写得很好，所以你可以很容易地理解这个库，但它并没有涵盖所有的用例。

例如，如果我们查看`persist`函数，您可以在示例中看到两个配置选项，但是要查看所有可用选项，您必须打开代码并直接检查实现。或者，如果您使用 TypeScript，您可能会通过键入来找出它。

商店结构也很笨重。创建商店时，必须始终在`create`函数中完成，并且`edit`函数需要将`set`函数添加到`callback`函数中。

这意味着你必须在`callback`函数的范围内编写你的`state`函数，或者你必须将`set`函数传递给它。当编写更复杂的操作函数时，这可能会很笨拙。

## 状态管理的当前状态

在我看来，我们最初使用 Redux 的日子已经屈指可数了。全局状态管理可能相当棘手，因此，应该不是人为复杂的事情。

我并不是说 Redux 没有用，但它可能会导致您过度设计一个最初简单的状态，这就是为什么我对 Zustand 宣扬简单的想法印象如此深刻。现在，我们有很多选项可以选择，所以 Redux 可能不再是所有状态管理的默认选择。

但是最后，它确实会因项目而异，并且说有一个库可以解决我们所有的问题是不现实的，但是至少我们有更多的选择，并且它不应该成为所有应用程序中选择 Redux 进行状态管理的默认选项。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)