# Vue 3 中的新内容

> 原文：<https://blog.logrocket.com/new-features-in-vue-3-and-how-to-use-them-2/>

期待已久的 [Vue 3 于 2020 年 9 月 18 日正式发布](https://madewithvuejs.com/blog/vue-3-roundup)。

这个新版本有[更新文档](https://v3.vuejs.org/)，Vue 和它的[迁移指南](https://v3.vuejs.org/guide/migration/introduction.html)一起发布。两者都是可读性很强、结构良好的资源，可以帮助您开始使用 Vue 3。

使用 Vue 3 就像使用以前的版本一样简单。如果您在这个过程中发现了任何错误或问题，您可以使用 [Vue Issue Helper](https://new-issue.vuejs.org/?repo=vuejs/vue-next) 报告它们。

在本指南中，我们将带您了解 Vue 框架的当前状态，并为您提供一些提示和基础知识，帮助您开始使用 Vue 3。

我们将讨论以下内容:

1.  [Vue 3 性能](#performance)
2.  [摇树支架](#treeshakingsupport)
3.  [组成 API](#thecompositionapi)
4.  [瞬间移动](#teleport)
5.  [片段](#fragments)
6.  [改进的打字稿支持](#bettertypescriptsupport)
7.  [其他重大变更](#someotherbreakingchanges)
8.  [实验特征](#experimentalfeatures)
9.  [从 Vue 2 迁移](#migratingfromvue2)
10.  [Vue 3 入门](#gettingstartedwithvue3)

我们开始吧！

## Vue 3 性能

Vue 3 的亮点之一就是性能。总体而言，Vue 3 的速度提高了约 55%，更新速度提高了 133%，内存使用率降低了 54%。这可以通过使用 TypeScript 完全重写 DOM 实现来实现。

其他性能改进包括:

1.  更高效的组件初始化
2.  SSR 速度快 2 到 3 倍
3.  编译器通知的快速路径
4.  更新性能提高 1.3 到 2 倍

## 摇树支架

树摇动是消除死的、无用的或未使用的代码的过程，从而减少应用程序的构建规模。

随着新的变化，Vue 3 的构建大小是 13.5kb。当你去掉除了组合 API 支持之外的所有东西时，它已经精简到 11.75kb。

即使有所有的铃铛和哨子，Vue 3 也只有 22.5kb，由于树晃动，它仍然比包含所有功能的 Vue 2 轻。

## 组合 API

这是 Vue 最大的变化之一。它完全是附加的，对以前的选项 API 引入了突破性的改变。随着组合 API 的发展，选项 API 将继续得到支持。

```
// src/components/UserRepositories.vue

export default {
  components: { RepositoriesFilters, RepositoriesSortBy, RepositoriesList },
  props: {
    user: { type: String }
  },
  setup(props) {
    console.log(props) // { user: '' }

    return {} // anything returned here will be available for the rest of the component
  }
  // the "rest" of the component
}

```

## `Teleport`

`Teleport`是 Vue 3 的一个新特性，允许你决定在哪里渲染一个有很多 DOM 父元素的 HTML。现在，您可以在 DOM 的不同位置呈现 HTML，而无需创建全局状态或不同的组件。

```
app.component('modal-button', {
  template: `
    &lt;button @click="modalOpen = true"&gt;
        Open full screen modal! (With teleport!)
    &lt;/button&gt;

    &lt;teleport to="body"&gt;
      &lt;div v-if="modalOpen" class="modal"&gt;
        &lt;div&gt;
          I'm a teleported modal! 
          (My parent is "body")
          &lt;button @click="modalOpen = false"&gt;
            Close
          &lt;/button&gt;
        &lt;/div&gt;
      &lt;/div&gt;
    &lt;/teleport&gt;
  `,
  data() {
    return { 
      modalOpen: false
    }
  }
})

```

当点击按钮时，Vue 将在`body`标签中呈现模态的内容，作为`body`标签的子标签。

## 碎片

Vue 3 现在支持称为片段的多根节点组件。这在 Vue 的旧版本中不受支持。

```
&lt;!-- Layout.vue --&gt;
&lt;template&gt;
  &lt;header&gt;...&lt;/header&gt;
  &lt;main v-bind="$attrs"&gt;...&lt;/main&gt;
  &lt;footer&gt;...&lt;/footer&gt;
&lt;/template&gt;

```

## 改进的类型脚本支持

Vue 3 的代码库是用 TypeScript 写的。这使它能够自动生成、测试和捆绑最新的类型定义。

Vue 现在支持 JSX，类型脚本仍然支持类组件。换句话说，你可以用 TypeScript 或 JavaScript 创建一个 Vue 应用，这取决于你的专业知识。

```
interface Book {
  title: string
  author: string
  year: number
}

const Component = defineComponent({
  data() {
    return {
      book: {
        title: 'Vue 3 Guide',
        author: 'Vue Team',
        year: 2020
      } as Book
    }
  }
})

```

## 其他突破性变化

Vue 3 引入了太多突破性的变化，无法在一篇文章中涵盖。如需了解更多信息，请访问[官方文件](https://v3.vuejs.org/guide/migration/introduction.html#breaking)。

*   全局和内部 API 现在支持树摇动
*   渲染函数 API 已更改
*   Global Vue API 现在使用一个应用程序实例
*   功能组件仅由普通功能创建
*   单文件组件(SFC)上的`function`属性已被否决
*   为了支持`v-model`参数，移除了`model`组件选项和`v-bind`的`sync`修改器
*   创建异步组件需要使用`defineAsyncComponent`方法
*   最佳实践是始终将组件数据选项声明为一个函数
*   `$scopedSlots`属性被替换为`$slots`
*   新属性强制策略

## 实验特性

Vue 3 为 Options API 提供了许多优秀的特性和增强功能，还引入了一些实验性的特性。您可以在开发阶段使用这些功能，并将反馈发送给 Vue 核心团队。

### 焦虑

悬疑是一个重要的特征，尽管它仍处于试验阶段。它充当在满足条件时呈现回退内容的组件，并用于有条件地呈现内容。

当您想进行 API 回调以在完全加载时显示内容，或者在处理过程中显示加载消息时，这很方便。

```
<Suspense>
  <template >
    <Suspended-component />
  </template>
  <template #fallback>
    Loading...
  </template>
</Suspense>

```

上面是一个简单的例子，说明了如何使用`suspense`和 fallback 来处理 API 请求。

### `<script setup>`

当使用 Vue 3 中的`<script>`标签创建一个具有新的复合 API 的单页面组件时，您应该将所有的方法和变量放在`setup()`方法中。但是有了新的`<script setup>`，你可以简单地创建你的组件，而不需要使用`setup()`方法或者返回任何东西。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

老路:

```
<script>
import { ref } from 'vue'
export default {
  setup() {
    const variable = ref(0)
    const inc = () => variable.value++
    return {
      variable,
      inc,
    }
  },
}
</script>
```

新的方式:

```
<script setup>
  import { ref } from 'vue'

  export const count = ref(0)
  export const inc = () =&gt; count.value++
</script>

```

在不使用`setup`方法的情况下，一切仍然正常，代码也更加简洁。

### `<setup vars>`

这也是 Vue 3 中引入的一个非常有用的特性，它允许开发者在我们的`style`声明中引用组件状态。一个非常基本的例子是声明一个反应性属性并在`<style>`标签中访问它。

```
<template>
  <div class="text-color">This is my text </div>
<template>

<script>
  export default {
    data() {
      return {
          color: 'green'
      }
    }
  }
</script>

<style vars="{ color }">
  .text-color {
    color: var(--color);
  }
</style>
```

通过使用`vars`和`var`属性，您可以声明一个带有反应值的 CSS 属性。

## 从 Vue 2 迁移

如果您有一个用 Vue 2 编写的项目，您可能想知道如何处理迁移。Vue 团队将发布 Vue 2 的最后一个更新，它向后兼容 Vue 3。该团队还将为会导致应用程序崩溃的更改添加折旧警告。这个长期支持(LTS)版本将持续 18 个月，这意味着它仍然会获得安全更新，继续使用是绝对安全的。

仍处于测试阶段的[迁移指南](https://v3.vuejs.org/guide/migration/introduction.html)将包括 Vue 3 的兼容性版本和命令行迁移工具，这将有助于自动迁移并在必要时提供提示。

## 开始使用 Vue 3

```
npm init vite-app hello-vue3

const HelloVueApp = {
  data() {
    return {
      message: 'Hello Vue!!'
    }
  }
}

Vue.createApp(HelloVueApp).mount('#hello-vue')
//--------------------------------------------------

&lt;div id="hello-vue" class="demo"&gt;
  {{ message }}
&lt;/div&gt;

```

## 结论

在本教程中，我们探索了 Vue 3 引入的新功能和突破性变化。我们还演示了如何使用`vite`库启动一个新的 Vue 3 项目，以及如何将旧的 Vue 2 项目迁移到 Vue 3。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。