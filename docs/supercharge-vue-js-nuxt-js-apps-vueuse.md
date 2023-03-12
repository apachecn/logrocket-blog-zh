# 用 VueUse 增强你的 Vue.js 和 Nuxt.js 应用

> 原文：<https://blog.logrocket.com/supercharge-vue-js-nuxt-js-apps-vueuse/>

Vue.js 和 Nuxt.js 是已经因其出色的开发人员体验而广为人知的框架。自从 [Vue 3](https://blog.logrocket.com/definitive-guide-vue-3-components/) 发布以来(Nuxt 3 就是在上面编写的)，我们获得了一组 API，允许我们使用导入的函数而不是声明选项来创作 Vue 组件。这就是通常所说的[组合 API](https://blog.logrocket.com/how-to-build-applications-with-vues-composition-api/) 。这个 API 使我们能够在 Vue 的反应性 API、生命周期挂钩及其底层的[依赖注入系统](https://vuejs.org/api/composition-api-dependency-injection.html)上创建抽象。

在本文中，我们将讨论 Vue 开发人员如何利用 VueUse 的功能来有效地简化开发和减少样板代码，同时允许他们快速原型化和测试想法。

### 内容

## VueUse 和组合 API

硬币本身是一个基本的 Vue 合成工具的集合。VueUse 利用了 composition API 提供的 API，并提供了 200 多个灵活且类型安全的函数，在组件中添加了基本的交互，否则可能需要您编写代码。

VueUse playground 让你可以随心所欲地尝试 API。这个操场是如此庞大的图书馆的完美补充。我们有能力安装其他库，实时玩 VueUse 的功能，甚至分享到你正在实验的操场的链接。

任何开发人员都可以编写一个看起来像 VueUse 提供的可组合函数，但是，VueUse 函数经过结构化、测试和优化，对于任何 Vue 开发人员来说都是一把实用的瑞士军刀。

## VueUse 入门

因为 VueUse 只是一个函数集合，开始使用它应该像在项目中添加一个`utils`文件并简单地从其中导入函数一样无缝。

要在您的 Vue 项目中安装 VueUse，您可以通过 npm install 获得它:

```
npm i @vueuse/core

```

或者通过 CDN，曝光为`window.VueUse`:

```
<script src="https://unpkg.com/@vueuse/shared"></script>
<script src="https://unpkg.com/@vueuse/core"></script>

```

[查看他们的入门页面](https://vueuse.org/guide/#installation)以便在 Nuxt 和其他环境中安装。

## 用 VueUse 作曲

将 composables 与 VueUse 一起使用很容易。给你一个函数，它只做一件事，或者提供一组特性。这个函数处理特性背后的整个复杂性，并且能够返回 [refs](https://vuejs.org/api/reactivity-core.html#ref) ，它们是 Vue 中的反应性和可变对象。ref 然后被用作 Vue 反应变量，并且可以被使用或变异

下面是一个`useClipboard` VueUse 函数的例子。这个可组合的函数能够读写剪贴板，并响应复制、剪切和粘贴命令。甚至检查它在当前环境中是否受支持:

```
import { useClipboard } from '@vueuse/core'

const source = ref('Something you need to copy')
const { text, copy, copied, isSupported } = useClipboard({ source })

```

使用简单且非常灵活；你可以在你的 Vue 应用中使用它。该函数将返回您需要的变量的引用。`useClipboard` [源代码](https://github.com/vueuse/vueuse/blob/main/packages/core/useClipboard/index.ts)简洁而完整，为你提供了实现这一功能所需的最简单的 API。

如果您想用 reactive 而不是`refs`使用相同的函数，您可以简单地用 vue 的`reactive()`函数包装它们:

```
import { reactive } from 'vue'
import { useMouse } from '@vueuse/core'

const clipboard = reactive(useClipboard())

// "all available refs" will be unwrapped.
console.log(clipboard.copied)

```

### 配置组件

VueUse 函数提供了很大的灵活性，例如，您可以使用[事件过滤器](https://vueuse.org/guide/config.html#event-filters)来节流、去抖甚至暂停它们:

```
import { debounceFilter, throttleFilter, useMouse } from '@vueuse/core'

// mouse position will be updated after mouse idle for 1s
const { x, y } = useMouse({ eventFilter: throttleFilter(1000) })

// mouse position will be updated after mouse idle for 100ms
const { x, y } = useMouse({ eventFilter: debounceFilter(100) })

```

VueUse 还可对其他特定功能的[反应时间](https://vueuse.org/guide/config.html#reactive-timing)进行进一步配置。相对于 Vue 生命周期事件，您可以更改事件发生的时间。

### 无渲染组件

VueUse 通过`@vueuse/components`包提供无渲染组件。在无渲染组件中，ref 绑定是自动完成的，VueUse 提供了带有插槽和道具的现成组件来捕捉事件、触发和编辑组件。

下面是一个 VueUse 组件的示例，它使我们能够在网页中“眼药水”(捕捉颜色):

```
<script setup>
import { useEyeDropper } from '@vueuse/components'
<script/>

<template>
  <UseEyeDropper v-slot="{ isSupported, sRGBHex, open }">
    <button :disabled="!isSupported" @click="open">
      sRGBHex: {{ sRGBHex }}
    </button>
  </UseEyeDropper>
<template>

```

如果从`@vueuse/core`导入，同样的功能也可以提供可用的无功参考。这为开发人员提供了使用和重用这些函数的最大灵活性。

VueUse 的函数库是完全可树摇的、经过测试的、类型化的、SSR 友好的，并针对 Vue 进行了很好的优化，使其安全且对开发者友好。

### 在反应性转换中简洁地使用组件

[反应性转换](https://vuejs.org/guide/extras/reactivity-transform.html#reactivity-transform)是一个实验性的编译时转换，它允许我们在不使用`.value`的情况下访问我们的 Vue `ref`。它们是通过在等价的宏上加上前缀`$`来创建的，就像这样:

*   `ref` → `$ref`
*   `computed` → `$computed`
*   `shallowRef` → `$shallowRef`
*   `customRef` → `$customRef`
*   `toRef` → `$toRef`

对于 VueUse 来说，这意味着我们能够以一种析构的方式使用来自可组合的引用:

```
import { useMouse } from '@vueuse/core'
const { x, y } = $(useMouse())
console.log(x, y) // no need to write x.value and y.value 

```

## VueUse 中的突出功能

很明显，我们可以用 VueUse 提供的功能构建很多很酷的东西；每个功能都有特定的用例。下面，你可以找到一个关于我们如何使用这些功能来轻松构建和扩展令人兴奋的界面和交互的概要。

在 VueUse 上，功能按类别分开，重新组合成两种不同的类型:核心和附加功能。核心功能是轻量级和无依赖性的功能，在内部使用 Vue 反应来执行任务。附加组件是流行包的包装功能版本，遵循可组合的方式。

在接下来的几节中，我将列出每一个类别，并对其包含的功能进行简要的解释，以及用它们可以构建什么的例子。

### 浏览器

浏览器功能是反应式功能，能够与浏览器 API 进行交互。您有能力检查权限；使用通知、全屏和振动等 APIs 甚至基于断点(`useBreakpoint`)操纵你的 UI。

### 传感器

传感器功能能够捕获用户事件并使用设备 API。这些功能包括从鼠标或键盘抓取不同的事件(`useMousePressed`、`useMouse`、`onStartTyping`、`onMagicKeys`)到 DOM 发出的事件，如`useElementHover`和`onClickOutside`。

VueUse 传感器的功能非常强大，因为它们与反应性有关。有时你需要实时事件，但有时你可能需要节流，VueUse 做得很好。

### 动画

动画功能大多与时间相关。然而，它们不仅仅是特定于动画的；您还可以使用它们来根据时间更新您的 UI。

这些函数包括`useNow`和`useTimestamp`，给我们当前日期(实时)。我们还有`useInterval`和`useTimeout`，它们的行为类似于 JavaScript `setTimeout`和`setInterval`以及`useTransition`。它可以执行从简单到复杂的自定义转换。

VueUse 还公开了一个名为 [@vueuse/motion](https://motion.vueuse.org/) 的附加组件。@vueuse/motion 使用 popmotion 来创建漂亮的动画。@vueuse/motion 还允许您将它的 API 用作指令或可组合的 API。访问[文档](https://motion.vueuse.org/)获取更多关于如何用这个伟大的附加组件制作动画的例子。

### 状态

状态功能使您能够访问与您的 Vue 组件和浏览器状态相关的所有内容。这一类中的亮点是`createGlobalState`、`useLocalStorage`和`useRefHistory`。

`createGlobalState`可用于在不同的 Vue 实例之间创建可重用和可共享的状态。`useLocalStorage`使我们能够反应性地使用浏览器的`localStorage`,`useRefHistory`跟踪一个`ref`的变化历史。

### 元素

元素组件帮助您被动地访问 HTML 元素的状态。您可以使用`useMouseInElement`来获取鼠标在元素中的相对位置，或者使用`useDraggable`来使任何 HTML 元素可拖动。`useDraggable`功能非常强大，因为你可以跟踪他们的位置，并将他们的坐标保存在浏览器存储器中。

### 成分

组件函数允许您被动地处理组件的许多方面。有一些函数可以绑定模板引用，以便安全地与组件生命周期交互。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我在这个类别中的亮点是`useVirtualList`，它能够将一个渲染的数组虚拟成一行。此函数允许您呈现显示容器内数组中的项目所需的最少数量的元素。

### 看

手表的功能就像类固醇上的 Vue `watch`功能。它们使您能够只观看一次，观看和过滤事件，以去抖或节流的方式观看，甚至观看和忽略某些变化。

### 网络

这些是与网络事件交互的功能。它们能够进行异步 HTTP 调用，并返回调用或连接的状态。

这个类别中突出显示的函数是`useFetch`和`useWebSocket`，因为它们可能是另一个独立的包，并且仍然非常受欢迎。

`useFetch`使我们能够在 Vue 应用中获取、重新获取、拦截和中止 HTTP 请求。它非常强大，因为它处理反应的方式；该函数返回引用和回调，使您能够跟踪请求的状态，中止并在 fetch 事件后触发其他函数。

VueUse 还提供了一个可组合的包装器来包装 [Axios](https://github.com/axios/axios) 库。`useAxios`实现所有 Axios 功能，同时保持 Vue 对事件的反应。

### 公用事业

此类别包含任何其他功能，可帮助您在 Vue 应用程序中创建或处理反应元素和功能。

上面讨论的功能只是您可以使用的 200 多个功能中的一部分，这个列表还在不断增加。

## 您可以使用 VueUse 构建的应用

*   使用`@vueuse/gesture`附加包可以很容易地实现像 Trello 看板这样复杂的动画拖放界面
*   可以通过组合`useDraggable`和`useResizeObserver`来构建一个图像分类 UI，使用户能够选择图像的一部分并进行分类，以帮助用户创建选择框并跟踪它们相对于被分类图像的位置
*   VueUse 可以成为在浏览器上创建游戏的好伙伴，因为它支持用`@vueuse/gestures`、`useGamepad`、`useMagicKeys`和`useMouse`输入。这简化了与本机 API 交互的代码编写

VueUse 贡献者总是寻找创造性的方法来展示如何利用每个功能的例子。浏览文档可以成为开发人员的灵感来源。

## 编写自己的可组合程序

编写 Vue 组件和使用它们一样简单。下面是一个有助于被动跟踪鼠标位置的可组合示例:

```
// mouse.js
import { ref, onMounted, onUnmounted } from 'vue'

// by convention, composable function names start with "use"
export function useMouseCustom() {
  // state encapsulated and managed by the composable
  const x = ref(0)
  const y = ref(0)

  // a composable can update its managed state over time.
  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  // a composable can also hook into its owner component's
  // lifecycle to setup and teardown side effects.
  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  // expose managed state as return value
  return { x, y }
}

```

上面的`useMouseCustom`函数遵循了 VueUse 公布的一些概要指南。这些指导方针帮助您编写更好的组件，这些组件在 Vue 版本之间向后兼容，副作用最小或没有副作用。

## 结论

我相信 VueUse 是 Vue 生态系统的一大补充。它在开发人员体验方面带来的价值是巨大的。任何级别的开发人员都可以使用这个库，并且可以进行任何可能的改进。

在这篇文章中，我们看到了 VueUse 如何利用 composition API 的力量来创建可以增强开发工作流的函数。这些功能已经过测试，安全且易于集成到任何大小的 Vue/Nuxt 应用中。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。