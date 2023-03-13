# 如何强制一个 Vue 组件重新渲染

> 原文：<https://blog.logrocket.com/force-vue-component-re-render/>

Vue.js 是一个通用的、可增量采用的 JavaScript UI 框架。凭借其[独特的架构和反应系统](https://blog.logrocket.com/your-guide-to-reactivity-in-vue-js/)，只要 JavaScript 状态发生变化，Vue 就会更新 DOM。然而，在某些情况下，这种方法是不够的，我们需要重新呈现单个组件。

在本文中，我们将回顾一些有效地重新呈现 Vue 组件的最佳实践，这样我们就可以避免重新加载整个页面。以下四个基本实践适用于 Vue 2 和 Vue 3。我们开始吧！

*向前跳转:*

## 热重装

每当我们更新一个文件时，热重载不仅仅是重载一个页面。当我们在启用热重载的情况下修改一个`*.vue`文件中的组件时，Vue 将交换该组件的所有实例，而不刷新页面。因此，它保留了我们的应用程序的当前状态以及交换的组件，从而在您显著修改组件的模板或样式时改善了开发人员的体验。

### 使用热重装

每当您使用 Vue CLI 搭建项目时，热重新加载将被启用。此外，当您手动设置一个新项目时，当您使用`webpack-dev-server --hot`为您的项目提供服务时，会自动启用热重装。

对于高级用例，我推荐查看一下 [vue-hot-reload-api](https://github.com/vuejs/vue-hot-reload-api) ，这是 Vue Loader 内部使用的。

### 禁用热重装

热重装始终自动启用，除非在以下情况下:

*   webpack`target`为[t1(SSR)
*   webpack 简化了代码
*   `process.env.NODE_ENV === 'production'`

要特别禁用热重装，使用`hotReload: false`选项:

```
module: {
  rules: [
    {
      test: /\.vue$/,
      loader: 'vue-loader',
      options: {
        hotReload: false // disables Hot Reload
      }
    }
  ]
}

```

### 国家保存规则

当重新渲染组件时，热重载过程遵循一组[状态保存规则](https://vue-loader.vuejs.org/guide/hot-reload.html#state-preservation-rules)。

如果你编辑一个组件的`<template>`,被编辑组件的实例将在适当的位置重新渲染，保持所有现有的私有状态。这可以通过组合模板来创建新的渲染函数，而不会产生副作用。

当一个组件的`<script>`部分被改变时，被修改组件的实例将被销毁并在适当的位置重新创建。这是因为`<script>`中的生命周期挂钩可能会产生副作用，需要重新加载而不是重新渲染来保持一致性。

因此，在组件生命周期挂钩中使用计时器或其他全局副作用时，应该小心谨慎。有时，如果一个组件有全局副作用，我们必须重新加载整个页面。

请记住，应用程序状态不受`<style>`热重载的影响，因为它是通过`vue-style-loader`独立运行的。

## 黑客攻击

Vue 库中包含的 [`v-if`指令](https://vuejs.org/guide/essentials/conditional.html)仅在给定条件为`true`时呈现组件。如果是`false`，组件不会出现在 DOM 中。因此，创造性地使用`v-if`指令可能是一个更好的解决方案。

在这个模板中，让我们设置一个`v-if`指令:

```
<template>
  <MyComponent v-if="renderComponent" />
</template>

```

在`script`部分，我们将添加一个名为`forceRender`的方法，它使用`[nextTick](https://vuejs.org/api/general.html#nexttick)`，一个等待下一次 DOM 更新刷新的实用程序:

```
import { nextTick, ref } from 'vue';
const renderComponent = ref(true);

const forceRender = async () => {
  // Here, we'll remove MyComponent
  renderComponent.value = false;

   // Then, wait for the change to get flushed to the DOM
  await nextTick();

  // Add MyComponent back in
  renderComponent.value = true;
};

```

在我们使用选项 API 而不是[组合 API](https://blog.logrocket.com/how-to-build-applications-with-vues-composition-api/) 的场景中，我们的`script`看起来更像下面的代码:

```
export default {
  data() {
    return {
      renderComponent: true,
    };
  },
  methods: {
    async forceRender() {
   // Remove MyComponent from the DOM
   this.renderComponent = false;

   // Then, wait for the change to get flushed to the DOM
      await this.$nextTick();

      // Add MyComponent back in
      this.renderComponent = true;
    }
  }
};

```

在上面的代码片段中，`renderComponent`最初被设置为`true`，呈现`MyComponent`。每当我们调用`forceRender`，`renderComponent`就被设置为`false`。这样做会停止渲染`MyComponent`，因为`v-if`指令现在计算为`false`。

在`MyComponent`停止渲染后，我们立即等待滴答，然后将`renderComponent`设置回`true`。这也将`v-if`指令设置为`true`，这将呈现`MyComponent`的刷新实例

有两个因素对解读`nextTick()`如何运作至关重要。首先，我们必须等到下一个 tick，除非我们对`renderComponent`的更新会自行取消，我们不会注意到任何变化。

在 Vue 中，可以在状态改变后立即使用`nextTick()`来等待 DOM 更新完成。我们可以将回调作为参数传递，或者等待返回的承诺。

Vue 将销毁之前的组件，因为当我们第二次渲染它时，它会创建一个全新的组件。因此，我们新的`MyComponent`将经历所有典型的生命周期，`created`，`mounted`，等等。

## Vue 的内置`forceUpdate`方法

正如 Vue 官方文档所推荐的，内置的`forceUpdate`方法是手动更新组件的最佳方式之一。

通常，Vue 会更新视图以响应依赖关系的变化。即使所有的依赖关系都没有改变，我们仍然可以通过调用`[forceUpdate](https://vuejs.org/api/component-instance.html#forceupdate)`方法来强制更新，这将强制组件实例重新呈现。

然而，这违背了整个反应系统的目的。因此，在这些情况下，不建议将其作为一种修复方法。我们错误地认为 Vue 会对特定属性或变量的变化做出反应，但这并不总是反应系统的工作方式。

下面的代码演示了如何使用 Vue Options API 调用`forceUpdate`方法:

```
export default {
  methods: {
    ForcesUpdateComponent() {
      // our code
      this.$forceUpdate();  // Notice we have to use a $ here
      // our code
    }
  }
}

```

这个方法只存在于组件实例中，所以我们需要发挥一点创造性，使用 Vue 3 的组合 API 来调用它:

```
import { getCurrentInstance } from 'vue';

const methodThatForcesUpdate = () => {
  // our code
  const instance = getCurrentInstance();
  instance.proxy.forceUpdate();
  // our code
};

```

我们可能想要使用`forceUpdate`方法的唯一情况是当我们已经使用高级反应性 API 显式地创建了一个非反应性组件状态时。然而，鉴于 Vue 的全自动反应系统，这是可选的。

## 换键技术

使用键改变技术，我们提供一个`key`属性来通知 Vue 一个特定的组件被连接到一个特定的数据。如果`key`保持不变，组件不会改变。但是，如果`key`改变了，Vue 知道它需要删除以前的组件并生成一个新的组件。

### 为什么 Vue 需要一个`key`？

让我们更深入地研究一下`key`属性具体做什么，以及我们为什么需要它。让我们假设我们正在呈现一个组件列表，其中包括以下一个或多个组件:

*   地方政府
*   一个初始化过程，或者使用 Vue 3 的`setup`函数，或者在使用选项 API 时使用`created`和`mounted`钩子
*   通过 jQuery 或普通 API 实现的非反应式 DOM 操作

当排序或更新那个列表时，我们不想重新呈现列表上的所有内容；我们只想重新呈现我们更新过的列表部分。为了实现这一点，我们将提供一个`key`属性来帮助 Vue 跟踪什么已经改变，什么没有改变。由于数组的索引与列表中的具体条目无关，在这种情况下使用它没有任何意义。

换键技术被认为是强制 Vue 重新渲染组件的最佳方式。下面是说明这一点的基本方法:

```
<template>
  <MyComponent :key="componentKey" />
</template>

<script>
import { ref } from 'vue';
const componentKey = ref(0);

const forceRender = () => {
  componentKey.value += 1;
};
</script>

```

在上面的代码片段中，我们将为`MyComponent`添加一个`key`属性，然后每当我们需要重新呈现`MyComponent`时就更改那个`key`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

使用 Options API 而不是 Vue 3 或 Composition API，看起来如下所示:

```
export default {
  data() {
    return {
      componentKey: 0,
    };
  },
  methods: {
    forceRender() {
      this.componentKey += 1;
    }
  }
}

```

每当调用`forceRender`时，`componentKey`的值就会改变。当这种情况发生时，Vue 将意识到它必须销毁组件并初始化一个新的组件。我们还将得到一个子组件，它将重置其状态并重新初始化自身。

## 结论

理想情况下，我们应该能够利用 Vue 强大的反应系统，避免使用这种粗糙的解决方法，但有些情况下，我们必须强制组件重新渲染，以完成手头的任务。在本文中，我们介绍了强制 Vue 组件重新呈现的四种不同方法，包括热重载、`v-if` hack、`forceUpdate`方法，最后是密钥更改技术。

正确的方法最终将取决于您发现自己所处的独特情况，然而，密钥更改技术通常是一个安全的赌注。我希望你喜欢这篇文章！如果你有任何问题，一定要留下评论。编码快乐！

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。