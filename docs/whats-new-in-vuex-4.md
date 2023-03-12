# Vuex 4 中的新内容

> 原文：<https://blog.logrocket.com/whats-new-in-vuex-4/>

## 介绍

多年来，前端应用程序变得越来越复杂和分层。随着越来越多的数据在前端处理，最大的挑战可能是管理客户端应用程序可能处于的各种复杂状态。这就是为什么状态管理可能是构建健壮的前端应用程序中最关键的挑战。

在过去几年中，Redux、MobX 和 Vuex 已经成为 JavaScript 生态系统中最流行的状态管理库。尽管 Redux 可能是其中最受欢迎的，但 Vuex 已经巩固了其作为基于 Vue.js 构建的 web 应用程序的事实上的状态管理库的地位，这主要是因为它相对简单并且与 Vue 集成。

## 为什么是 Vuex？

Redux 和 Vuex 的灵感都来自于 [Flux](https://facebook.github.io/flux/) 架构。然而，主要的区别在于它们如何处理状态。在更受 React 开发者欢迎的 Redux 中，状态是绝对不可变的；有了 Vuex，您就有了更容易理解、更简洁、更直观的特定变异规则。

Vue 组件从 Vuex 存储中获取它们的状态，它本质上是一个对象，但是具有与普通 JavaScript 对象不同的特征:它们是反应性的，您不能直接改变存储的状态。改变存储状态的唯一方法是显式提交[突变](https://vuex.vuejs.org/guide/mutations.html)。

## 有什么新鲜事吗？

Vuex 经历了相当多的迭代才达到目前的状态。在本文中，我们将关注 VueX 最新版本`v4.0.0-beta`中的新特性。

版本 4 的主要目标是支持 Vue 3 中引入的新的[组合 API](https://composition-api.vuejs.org/) 和[简化 Vuex](https://github.com/vuejs/roadmap#vuex-4x) 的整体使用。它还旨在为 TypeScript 支持更健壮的推理。我们将通过几个例子详细探讨每一个问题。

### 重大变化

截至发稿时，`[[email protected]](https://github.com/vuejs/vuex/releases/tag/v4.0.0-beta.1)`刚刚发布。主要的突破性变化之一是在 Vue 组件中删除了`this.$store`的全局类型。该功能的目的是使 typescript 用户能够在组件中编写完整的类型层。因此，开发人员可以进行手动声明，实现完全类型化的结构，这在 Vuex 3 中几乎是不可能的。

综上所述，根据[发布说明](https://github.com/vuejs/vuex/releases/tag/v4.0.0-beta.1):

> 使用 TypeScript 时，必须提供自己的增强声明。

以下是上述发行说明中提供的示例:

```
// vuex-shim.d.ts
declare module "@vue/runtime-core" {
  // Declare your own store states.
  interface State {
    count: number
  }
  interface ComponentCustomProperties {
    $store: Store<State>;
  }
}
```

接下来，我们将对此进行更深入的探讨。

### 状态

商店的定义通常从定义`state`开始:

```
interface Actor {
  name: string
  age: number
  }
interface State {
  loading: boolean
  data: Array<Actor>
}
export const state:State = {
  loading: false,
  data: [{name: 'John',age: 25}]
```

导出状态的类型是很重要的，因为它将被用于 getters、突变和动作的定义中。这里有一个附带的 GitHub repo 示例。

### 突变

就像在 Redux 和其他 Flux 实现中一样，将突变存储为常量是很常见的。这里的方法是将我们所有的潜在突变存储为一个`MutationTypes`枚举。

```
// mutation-types.ts:
export enum MutationTypes {
  SET_LOADING = 'SET_LOADING',
  FETCH_ACTORS = 'FETCH_ACTORS',
  ADD_ACTOR = 'ADD_ACTOR',
  REMOVE_ACTORS = 'REMOVE_ACTOR'
}
```

我们现在可以为我们的每个突变声明一个“契约”。这是 Redux 中常见的模式，类似于`reducers`。变异只是一个函数，它接受状态和一个可选的有效载荷，对状态进行变异，然后返回最新更新的状态。

根据我们新发现的知识，让我们为我们的突变声明一个类型。为此，我们有几个选择:更健壮的方法是使用 TypeScript 的`generic`对我们的突变进行分类:

```
// mutuations.ts
import { MutationTypes } from './mutation-types'
import { State } from './state'
export type Mutations<S = State> = {
  \[MutationTypes.SET_LOADING\](state: S, payload: boolean): void
}
```

目前看起来不错吧？现在让我们编写一个实现:

```
import { MutationTree } from 'vuex'
import { MutationTypes } from './mutation-types'
import { State,Actor } from './state'
export type Mutations<S = State> = {
  \[MutationTypes.SET_LOADING\](state: S, payload: boolean): void,
  \[MutationTypes.REMOVE_ACTORS\](state: S, payload: Actor): Array<Actor>,
  \[MutationTypes.ADD_ACTOR\](state: S, payload: Actor): Array<Actor>,
}
export const mutations: MutationTree<State> & Mutations = {
    \[MutationTypes.SET_LOADING\](state, payload: boolean) {
      state.loading = payload
      return state
    },
  \[MutationTypes.REMOVE_ACTORS\](state, payload: Actor) {
    state.data = [payload]
    return state.data
  },
  \[MutationTypes.ADD_ACTOR\](state, payload: Actor) {
    state.data = [payload]
    return state.data
  }
}
```

`mutations`存储所有可能实现的突变。这将最终用于建造商店。

### 行动

让我们为我们的应用程序编写一个简单的`action`。首先，让我们用一个枚举键入`action`类型:

```
// action-types.ts
export enum ActionTypes {
  GET_ACTOR = 'GET_ACTORS',
}
```
`actions.ts`
```
import { ActionTypes } from './action-types'
export const actions: ActionTree<State, State> & Actions = {
  async \[ActionTypes.GET_ACTORS\]({ commit }) {
   const allActors = await fetch('actorsAPI').then((actors)=> commit(MutationTypes.ADD_ACTOR,actors))
   return allActors
  },
}
```

### 吸气剂

Getters 也可以是静态类型的。getter 与 mutation 没有太大区别，因为它本质上是一个接收状态并对其执行计算的函数。

下面展示了一个例子，它将`state`作为第一个参数，并以大写形式返回`actor`名称。这可能会变得非常复杂，但它遵循相同的基本原则:

```
// getters.ts
import { GetterTree } from 'vuex'
import { State } from './state'
export type Getters = {
  capitalizeName(state: State): string[]
}
export const getters: GetterTree<State, State> & Getters = {
  capitalizeName: (state) => {
    return state.data.map(actor => actor.name.toUpperCase())
  },
}
```

### 全局`$store`类型

如前所述，您现在必须显式地键入您的存储，以便在您的组件中访问它们。所以所有默认的 Vuex 类型——getter、commit 和 dispatch——都将被我们的自定义类型所取代。

为了使我们定义的类型可以全局访问并正确工作，我们需要将它们传递给 Vue，就像这样:

```
// vuex-shim.d.ts
import {State} from '../state'
declare module "@vue/runtime-core" {
    // Declare your own store states.
    interface State {
      count: number
    }

    interface ComponentCustomProperties {
      $store: Store<State>;
    }
  }
```

## 组件中的用法

现在我们有了一个类型化的存储，让我们在一个组件中利用它来巩固概念。我们将会看到在组件中使用组合 API 语法，因为这是 Vue 的主要变化，也是 Vuex 4 的核心目的之一。

### 组合 API

我们必须用组合 API 通过`useStore`钩子访问商店。`useStore`钩子实质上返回了我们的`store`:

```
export function useStore() {
  return store as Store
}

<script lang="ts">
import { defineComponent, computed, h } from 'vue'
import { useStore } from '../store'
import { MutationTypes } from '../store/mutation-types'
import { ActionTypes } from '../store/action-types'
export default defineComponent({
  name: 'CompositionAPIComponent',
  setup(props, context) {
    const store = useStore()
    const actors = computed(() => store.state.data)
    const capitalizeActors = computed(() => store.getters.capitalizeName)
    async  function removeActor() {
      store.commit(MutationTypes.REMOVE_ACTORS,  {name: 'John',age: 67})
    }

    async function addActor() {
      const result = await store.dispatch(ActionTypes.GET_ACTORS, {name: 'John',age: 67})
      return result
    }
    return () =>
      h('section', undefined, [
        h('h2', undefined, 'Composition API Component'),
        h('p', undefined, actors.value.toString()),
        h('button', { type: 'button', onClick: addActor }, 'Add Actor'),
             h('button', { type: 'button', onClick: removeActor}, 'Remove Actor'),
      ])
  },
})
</script>
```

我们得到的是一个完全静态类型的存储。我们只能用适当的有效载荷提交/分派声明的突变/动作；否则，我们会得到一个错误。

这对于静态分析来说非常棒，对于自文档化代码来说非常好。如果您试图发送一个格式错误的有效负载或一个未启动的操作，那么 TypeScript 编译器会对您大喊大叫，并引导您纠正错误。太棒了，不是吗？

## 结论

我们已经了解了 Vuex 4 的新增功能和即将推出的功能。在撰写本文时，`[v4.0.0-beta.1](https://github.com/vuejs/vuex/releases/tag/v4.0.0-beta.1)`是 Vuex 4 的唯一一个版本，它的主要特性发布具有突破性的变化，也就是说，为商店提供了健壮的类型，并与 Vue 3 中的复合 API 进行了出色的集成。

`v4.0.0-beta.3`还附带了一个主要特性:`createLogger`函数是从`Vuex/dist/logger`中导出的，但是它现在包含在核心包中。您应该直接从 Vuex 包中导入函数。这是一个需要注意的小而重要的特征。

必须记住，Vuex 4 仍处于测试阶段，因此被标记为预发布，这意味着我们可以期待更多尚未完全实现或发布的令人兴奋的变化。这些包括去掉`mapXXX`和消除分离动作和突变的需要。

作为 Vue 和 Vuex 的粉丝和用户，这是一个激动人心的时刻。请关注 Vuex 4 的正式发布，它包含了所有令人惊叹的新特性——我特别喜欢增强的静态类型。再一次[这里是附带的 GitHub repo](https://github.com/olajohn-ajiboye/vuex4) 的代码示例。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。