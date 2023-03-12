# 约泰 vs 反冲:有什么区别？- LogRocket 博客

> 原文：<https://blog.logrocket.com/jotai-vs-recoil-what-are-the-differences/>

在这一点上，React 有太多的状态管理库几乎是一个迷因，但没有人会在短期内停止创建新的状态管理库。看起来除了使用普通的老式 React 状态，状态管理的方法可以大致分为:

*   流(Redux 状态)
*   代理(Mobx 州)
*   原子(反冲，约泰)

功劳归于[伊尔哈姆·瓦哈比格](https://twitter.com/ilhamwahabigx/status/1328910118570790913)。

本文将重点比较 Jotai 和反冲，但是让我简单描述一下这两种方法之间的区别。在一般情况下，Flux 方法的特点是，应用程序状态的所有变化都是由动作引起的，组件使用选择器订阅状态的各个部分。

通过这种方式，我们可以获得出色的开发人员体验和良好的性能。但是并不是每个人都喜欢编写动作和选择器(有人会称之为样板文件)，所以代理方法可以让您访问整个状态，并自动检测状态的哪些部分在组件中使用，并只订阅该部分的更新。

原子状态更接近 React 状态，并存储在 React 树中(flux 和 proxy 将数据存储在树外，可以在没有 React 的情况下使用)。这就是为什么原子状态通常与 React 上下文和 useState 相比较，而不是与其他状态管理库相比较。

## 原子特征

反冲允许您创建一个可以在多个组件之间共享的状态。它看起来很像一个普通的钩子。

状态被分裂成原子，这些原子比 redux store 之类的东西要小得多，也轻得多。它们是用`atom`函数创建的，可以彼此独立创建，也可以按需创建。这使得代码分割更加容易。

与使用`useState`的常规 React 上下文相比，原子可以用于高频更新。

反冲从 0.0.11 开始支持并发模式，相比之下，像 Redux 这样的工具还没有具体的支持计划。

派生或计算的状态可以异步解析(或引发错误)。在标准的 React 方式中，这应该用`<Suspense>`和`<ErrorBoundary>`来处理。

此外，派生状态可以有多个依赖项(可以是动态的)。这意味着它只订阅来自当前依赖关系集的更新。

## Jotai 又小又简单

你可以从介绍中的列表中收集到的是，祖斯坦德、瓦特里奥和约泰都可以用公式“X 但更简单、更小”来描述。现在，让我们从它有多小开始。根据[乔泰](https://bundlephobia.com/result?p=jotai@0.11.2)和[后坐力](https://bundlephobia.com/result?p=recoil@0.1.2)的 bundle 恐惧症，是 3.3 kb vs 14。

至于`node_modules`的大小，分别是 [1.21MB](https://packagephobia.com/result?p=recoil) vs [182kB](https://packagephobia.com/result?p=jotai) 。但是在我看来，当你运行这段代码的时候，最大的区别就显现出来了:`Object.keys(require("jotai")).length` vs `Object.keys(require("recoil")).length,`也就是 5 vs 30。

实际上，整个 Jotai API 就是这样:`Provider`、`atom`和`useAtom`。这并不是 Jotai 导出的全部特性，因为有些特性是作为不同端点的一部分导出的:`jotai/utils`和`jotai/devtools`，但是显示了反冲和 Jotai 的作者采用的方法的不同。

因此，对于那些刚刚开始使用这些库的人来说，Jotai 可能看起来不那么令人畏惧。但是反冲会有很多华而不实的文档，会有更多的人使用和谈论它，所以这里有一个肤浅的图表来证明这一点:

![Github stars for Jotai vs. Recoil displayed in a graph.](img/40a44b9a5f8c5c1bb44885e45e5a473b.png)

source: [https://star-history.t9t.io/#facebookexperimental/Recoil&pmndrs/jotai](https://star-history.t9t.io/#facebookexperimental/Recoil&pmndrs/jotai)

### 与反冲相比，Jotai 的仪式较少

Jotai 中的原子没有`key`属性，所以你可以用令人满意的短`const counterAtom = atom(0)`来代替`const counterState = atom({key: “counter”, default: 0})`。

这可能是一个潜在的问题。例如，如果您想标识一个用于调试的原子，无论如何您都要添加`counterAtom.debugLabel = "counter"`。另一个区别是，如果您的原子模块被更新，React Fast Refresh 将无法保持旧状态，因为所有新原子不再与旧原子在引用上相等(这在反冲中工作，因为它比较了`key`字符串)。

### Jotai 中的选择器

因此，如果 Jotai 可以只使用`atom`和`useAtom`，你能跳过所有额外的后坐力功能，只使用`atom`和`useRecoilState`吗？不，你不能。事实上，Jotai `atom`用于实现`atom`和`selector`。在如何设置原子和选择器的初始状态方面有一些不同。

在反冲中，初始值在`default`选项中设置，可以是一个原始值，一个承诺，或者你可以传递另一个原子，这样它的状态将被使用。异步值将导致渲染暂停。同样，对于高级用例，可以使用来自`effects_UNSTABLE`的`setSelf`而不是`default`(带触发器`get`)。

反冲选择器由返回另一个选择器/原子的值、承诺或状态的函数定义。您将该功能设置为`default`选项，选择器没有`effects_UNSTABLE`。

在 Jotai 中，这两个东西将被创建为具有相同的功能:`atom`。如果第一个参数是一个函数，那么您正在创建一个选择器(用 Jotai 术语来说是派生的 atom)。如果是别的东西，你就是在创建一个原子(基本的或者原始的原子)。Jotai 中初始化原子的所有高级用例都必须作为派生原子来实现，包括使用 promises 的异步初始化。

API 的复制不止于此。为了支持 atom 家族，反冲必须同时拥有`atomFamily`和`selectorFamily`，在 Jotai 中它们统一在`atomFamily`下(由`jotai/utils`输出)。

### 重置原子和`DefaultValue`

继续改进人机工程学的趋势:虽然有时将 atom 或选择器重置为其默认值可能是有用的，但这意味着对于您在反冲中编写的每个可写选择器，您必须考虑 setter 中的值可能是您想要设置的新值，或者是表示选择器被重置的特殊值`DefaultValue`。

比较一下这在 Jotai 中有多简单(这是用 TypeScript):

```
export const tempFahrenheitAtom = atom(32);

export const tempCelciusAtom = atom(
  (get) => ((get(tempFahrenheitAtom) - 32) * 5) / 9,
  (get, set, newValue: number) =>
    set(tempFahrenheitAtom, (newValue * 9) / 5 + 32)
);
```

此代码直接来自[反冲](https://recoiljs.org/docs/api-reference/core/useRecoilState/)文档:

```
const tempFahrenheit = atom({
  key: 'tempFahrenheit',
  default: 32,
});

const tempCelcius = selector({
  key: 'tempCelcius',
  get: ({get}) => ((get(tempFahrenheit) - 32) * 5) / 9,
  set: ({set}, newValue) => set(tempFahrenheit, (newValue * 9) / 5 + 32),
});
```

而且它有一个 bug！如果你重置`tempCelcius`，它实际上会变成`NaN`。幸运的是，这个问题被 TypeScript 抓住了([，但也不是每个人都喜欢这个](https://github.com/facebookexperimental/Recoil/issues/244))。要解决这个问题，您必须显式处理默认值，并且您必须对每个可写反冲选择器进行类似的处理:

```
export const tempCelcius = selector<number>({
  key: "tempCelcius",
  get: ({ get }) => ((get(tempFahrenheit) - 32) * 5) / 9,
  set: ({ set }, newValue) =>
    set(
      tempFahrenheit,
      newValue instanceof DefaultValue ? newValue : (newValue * 9) / 5 + 32
    )
});
```

在 Jotai 中，可重置原子通过`atomWithReset`(由`jotai/utils`导出)选择加入，如果您想要创建一个可写的派生原子，您可以通过允许`RESET`值来显式声明它:

```
export const tempCelciusAtom = atom(
    (get) => ((get(tempFahrenheitAtom) - 32) * 5) / 9,
    (get, set, newValue: number | typeof RESET) =>
      set(
        tempFahrenheitAtom,
        newValue === RESET ? newValue : (newValue * 9) / 5 + 32
      )
  )

```

并且如果一个原子是可重置的，TypeScript 将确保您只能使用`useResetAtom`或`set(RESET)`。

### 原子状态是如何存储的

正如我之前提到的，[反冲](https://github.com/facebookexperimental/Recoil/issues/5#issuecomment-62879694)和 [Jotai](https://github.com/pmndrs/jotai/issues/13#issuecomment-694723143) 都在 React 树中存储状态。

它们还使用 React context，对于频繁更新的数据，不推荐使用 React Context(默认情况下，如果 Context 值发生变化，使用 Context 的每个组件都将重新呈现)。因此，为了优化原子改变时需要重新呈现的组件数量，使用了定制更新订阅。

React Experiments 反冲使用实验性的`useMutableSource`，用`useRef`存储原子状态。Jotai 将 atom 状态存储在用模拟`useMutableSource`的`use-context-selector`创建的上下文中，即使对于旧版本的 React(使用`useReducer`)也是如此。见[这条推文](https://twitter.com/dai_shi/status/1333758874516480001)。

状态存储方式的最大区别在于并发模式。您可以看到，与 Redux 之类的东西相比，这两种方法都更好，但彼此没有太大的区别。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

更多信息见[此链接](https://github.com/dai-shi/will-this-react-global-state-work-in-concurrent-mode)。

将所有数据存储在 React 树中还意味着，如果您的应用程序不是由 React 的一个实例控制，您必须使用一个桥来连接它们，Jotai 和反冲都可以使用这个桥。

### 杂项助手

Jotai 的作者不是反冲用户，所以在 GitHub 问题中提出了许多反冲中的和 Jotai 初始版本中缺少的功能。如果你看一下回购协议，你会发现其中一些(到目前为止)不在项目范围内。

有些可以在现有 Jotai 特性的基础上实现，并添加了实现的例子。后来，当 Jotai 的作者们理解了对该特性的需求时，一些被添加到了库中。

有些特性是 Jotai 特有的，比如 reducer atoms(`jotai/utils`的`atomWithReducer`和`useReducerAtom`)、immer integration ( `jotai/immer`)以及对光学的支持(`jotai/optics`)。但是总的趋势是反冲有更多的内置特性来支持更多的用例。

第一组突出的特性是帮助使用异步选择器的函数。例如，如果您想在不适合使用`Suspense`的环境中使用异步原子，您可以使用`useRecoilStateLoadable`或`useRecoilValueLoadable`来使用它，这样它就不会再抛出错误或承诺。

其他值得注意的帮手还有`waitForAll`、`waitForAny`、`waitForNone`、`noWait`。

Jotai 中缺少的下一个功能是`useRecoilCallback`(通过扩展`useRecoilSnapshot`)，这可能是脱离 React 组件的一个很好的出口，可以直接处理反冲状态。

因为 Jotai 将 atom 状态存储在 React 状态中，所以我相信类似的东西在 Jotai 中是不可能实现的。

### 反冲快照

这就给我带来了 Jotai 和反冲的最大区别:[反冲快照](https://recoiljs.org/docs/api-reference/core/Snapshot/%5D(https://recoiljs.org/docs/api-reference/core/Snapshot/)。它是用来使反冲开发工具的工作。

它可以用来编写不需要您渲染 React 的测试，以及支持几种实验性的技术，如在本地存储中保持全局状态、浏览器历史状态或 URL。访问整个状态(实际上，访问状态的不可变快照)非常有用:保证所有原子都符合它们的依赖关系——即使是异步的。

对于高级用例，您有一个 API 来通知所有创建新快照的事务，包括`useRecoilTransactionObserver_UNSTABLE`和`useRecoilSnapshot`。

您可以使用`useGotoRecoilSnapshot`切换到旧快照。`snapshot.getID`可用于检测状态是否更改为旧快照。

### 回归状态

这是引入后坐力时大力推广的功能之一。六个月过去了，我仍然对它的现状不满意。

总体思路可以这样描述:让我们将状态的一部分存储到持久存储中，如`localStorage`或浏览器历史，以便以后我们可以从存储中恢复该状态或在状态之间快速移动(例如时间旅行，或进入相同的 UI 状态以再现用户错误报告)。

我不知道你，但我在 2016 年推出 Redux 时听到了所有这些。

在最初的推介中，持久性是在通过快照存储整个状态的上下文中讨论的。现在[推荐](https://recoiljs.org/docs/guides/atom-effects#local-storage-persistence)使用原子效果，并使用`effects_UNSTABLE`彼此独立地保存和恢复原子状态。

这与 Jotai 推荐的[是一致的。](https://github.com/pmndrs/jotai/blob/master/docs/persistence.md)

我没有得到证实，但看起来脸书使用了某种工具来保存后坐力数据。然而，它从未被开源或恰当地描述过。文档将描述使用原子的键或族原子参数的原始值有多重要，例如，这看起来很随意，但可能描述了特定系统的局限性。

所以在我们对这个工具了解更多，并且所有与持久性相关的 API 都被标记为不稳定之前，我会说使用 Jotai 提供的稳定 API 更有意义。

### 开发工具

为反冲原子和选择器创建快照和指定“键”有助于调试。反冲有一个专门的开发工具扩展，应该带来应用程序范围的可观察性，时间旅行，等等(目前 UI 和功能都还过得去)。

Jotai 通过 React Dev 工具(如果您使用`atom.debugLabel`)和对 Redux Dev 工具的实验支持(这几乎限制了您一次只能调试一个原子，也限制了您可以在上使用什么类型的原子)具有初步的可观察性。)

然而，它可能不会很快与反冲开发工具相提并论。

### 原子的初始值

这与持久性有关，但是可以用于不同的原因。例如，它可以用于为服务器端呈现的应用程序合并状态。您可以在一个地方初始化所有的原子值，这与反冲非常相似:

`<RecoilRoot initializeState={({ set }) => { set(counterState, 1); }}>`
和
`<Provider initialValues={[[counterAtom, 1]] as const}>`

我必须指出，反冲的方式是类型安全的(0.0.10 之后)，而在 Jotai 中，`initialValues`的类型实际上是`[any, any][]`。如果值是由服务器生成的，而不是由开发人员硬编码在 Typescript 中，这可能还是一个有争议的问题。

在结束这篇文章之前，我想提一些小问题:

*   这两个项目都没有对服务器端渲染的官方支持，但是网上有关于如何做的方法
*   现在还没有足够的数据来知道这些项目会得到怎样的支持。例如，`immutable-js-oss`表示“在脸书使用”不是一个足够好的指标
*   Jotai 有一些特殊的特性，这使它成为编写自己的库的一个很好的选择:体积小，运行时可能比手动 React 上下文更快，并且在 `Provider`中有一个`scope` 字段
*   我没有足够的经验来判断哪一个更适合编写测试
*   有趣的提示:您可以(但可能不应该)使用 atom identity，即使在处理类似于[待办事项](https://github.com/pmndrs/jotai/issues/5)的列表时，也不要使用`id`或`key`(注意，因为 0.12 `todoAtom.key`变成了`todoAtom.toString()`

## 结论

和生活中的所有事情一样，使用哪个库的问题的答案是:这很复杂。一个简单的轴来比较它们中的一个是否适合你的项目，就是它有多大。项目越小，就越难证明使用反冲是正确的(在编写[的时候，整个 redux 工具包比反冲](https://bundlephobia.com/result?p=@reduxjs/toolkit@1.5.0)占用的空间更小)。

另一方面，活动部件越多，像异步状态的调试和协调这样的事情就会发挥越大的作用。

或者你可能想使用反冲，因为你脑海中有一个只能通过快照或原子效果实现的功能(记住，这些仍然是实验性的，使用 [Jotai](https://github.com/pmndrs/jotai/issues/211) 可能有 90%的可能性)。)

至于用户社区，与 Redux 和 MobX 这样的老牌公司相比，这两个库都很小，看起来反冲总是会更受欢迎，因此在潜在雇主和回答堆栈溢出问题的人眼中更重要。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)