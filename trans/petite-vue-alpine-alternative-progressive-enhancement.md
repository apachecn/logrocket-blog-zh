# petite-vue:渐进增强的阿尔卑斯山替代方案

> 原文：<https://blog.logrocket.com/petite-vue-alpine-alternative-progressive-enhancement/>

***编者按**:本文最后一次更新于 2022 年 8 月 12 日，以反映对`petite-vue`的更改，包括捆绑包大小。*

根据 Vue 的创造者尤雨溪的说法，`[petite-vue](https://github.com/vuejs/petite-vue#comparison-with-standard-vue)`是一种受 Alpine 启发的 Vue 替代版本，针对渐进增强进行了优化。对于熟悉 Vue 并希望将 Vue 添加到在服务器端渲染的项目中的开发人员来说，`petite-vue`提供了完美的方法。

在这篇文章中，我们将探索`petite-vue`做什么，它如何工作，以及它如何与标准 Vue 和 Alpine 的[进行比较。我们还将介绍如何开始使用`petite-vue`,并看看它的一些用例。](https://blog.logrocket.com/intro-to-alpine-js-for-vue-developers/)

## 什么是渐进增强？

渐进式改进是一种方法，它允许 web 开发人员开始用 HTML 编程，并根据需要包含其他技术。您可以开始仅使用 HTML 静态构建网站，然后向页面添加交互性或客户端状态。

`petite-vue`针对由服务器框架呈现的现有 HTML 页面上的小交互进行了优化，从而简化了渐进式增强。现在，让我们看看它是如何工作的。

## `petite-vue`的基本特征

为了理解`petite-vue`是如何工作的，我们必须考虑它的基本特征。

有了`petite-vue`，你就不用担心构建工具了。相反，您可以简单地将它包含在一个`script`标签中，以便在 HTML 页面中获得它的特性:

```
<script src="https://unpkg.com/petite-vue" defer init></script>

<!-- anywhere on the page -->
<div v-scope="{ count: 0 }">
  {{ count }}
  <button @click="count++">inc</button>
</div>

```

### 小捆尺寸

在撰写本文时，Vue 和 Alpine 的最新版本的包大小分别为 [34.2kB](https://bundlephobia.com/package/vue@3.2.37) 和 [13.5kB](https://bundlephobia.com/package/alpinejs@3.10.3) minified 和 gzipped。另一方面，`petite-vue`的束大小仅为 [6。](https://bundlephobia.com/package/petite-vue@0.4.1) [9](https://bundlephobia.com/package/petite-vue@0.4.1) [kB](https://bundlephobia.com/package/petite-vue@0.4.1) 并且本意是轻量级的。

### Vue 兼容模板语法

已经熟悉 Vue 模板语法的开发人员会发现在 Vue 和`petite-vue`之间转换很容易。作为 Vue 本身的子集， `petite-vue` 使用了 Vue 的大部分相同语法。比如 `petite-vue` 使用类似 `{{ count }}` 的模板插值和类似 `@click` 的模板事件监听器。

### 没有虚拟 DOM

与 Vue、React 和大多数其他前端库和框架不同，`petite-vue`不使用虚拟 DOM。相反，它就地改变了 DOM。因此，`petite-vue`在网页上运行，不需要编译器，减小了整体大小。

### 由`@vue/reactivity`驱动

[`@vue/reactivity`](https://github.com/vuejs/vue-next/tree/master/packages/reactivity) 包负责处理 Vue 和 Alpine 中的反应性；`petite-vue`使用同样的反应技术。

## `petite-vue`与标准 Vue 相比如何

`petite-vue`在很多方面和 Vue 相似。如前所述，它提供了与标准 Vue 相同的模板语法和`@vue/reactivity`模型。然而，最显著的区别是`petite-vue`是为渐进增强而制造的。

标准 Vue 是为使用构建步骤来构建具有大量交互的单页面应用程序(spa)而设计的。Vue 使用渲染函数来替换现有的 DOM 模板，而另一方面，`petite-vue`遍历现有的 DOM 并对其进行适当的变异，因此不需要构建步骤。

## `petite-vue`独家功能

`petite-vue`引入了一些标准 Vue 中没有的功能，有助于优化渐进式增强。让我们来看看他们吧！

### `v-scope`

在`petite-vue`中，`v-scope`是一个标记页面中由`petite-vue`控制的区域的指令。您还可以使用`v-scope`指令来传递页面中特定区域可以访问的状态。

### `v-effect`

`v-effect`是用于执行`petite-vue`中内联反应语句的指令。在下面的代码片段中，`count`变量是反应性的，所以每当计数改变时`v-effect`就会重新运行，然后用`count`的当前值更新`div`:

```
<div v-scope="{ count: 0 }">
  <div v-effect="$el.textContent = count"></div>
  <button @click="count++">++</button>
</div>

```

### 生命周期事件

`petite-vue`附带了两个生命周期事件，`@mounted`和`@unmounted`，它们允许您监听`petite-vue`何时在您的页面上挂载或卸载。

## Vue 兼容特性

现在我们已经看到了`petite-vue`带来的新特性，让我们回顾一下它在 Vue 中已经存在的特性:

*   `{{ }}`:文本绑定
*   `v-bind`和`:`:类别和样式特殊处理
*   `v-on`和`@`:事件处理
*   `v-model`:表示所有输入类型和非字符串`:value`绑定
*   `v-if` / `v-else` / `v-else-if`
*   `v-for`
*   `v-show`
*   `v-html`
*   `v-pre`
*   `v-once`
*   `v-cloak`
*   `reactive()`
*   `nextTick()`
*   模板参考

## Vue 独有的功能

由于范围小，`petite-vue`放弃了标准 Vue 中的一些功能:

*   `ref()`和`computed()`
*   渲染函数:`petite-vue`没有虚拟 DOM
*   收集类型的反应性:`Map`、`Set`等。
*   `Transition`、`keep-alive`、`<teleport>`和`<suspense>`组件
*   `v-for`:深度破坏
*   `v-on="object"`
*   `v-is`和`<component :is="newComponent">`
*   `v-bind:style`自动前缀

## 与阿尔卑斯山相比如何

虽然 [`petite-vue`受到阿尔卑斯山](https://blog.logrocket.com/getting-started-alpine/)的启发，并解决了类似的问题，但由于其极简主义和与 Vue 的兼容性，它与阿尔卑斯山不同。

例如，`petite-vue`大约是 Alpine 的一半大小，与 Alpine 不同，它没有配备过渡系统。

另外，Alpine 和`petite-vue`的设计不同。虽然 Alpine 在某些方面类似于 Vue 的结构，但`petite-vue`更符合标准 Vue，如果你想在 Vue 和`petite-vue`之间转换，可以最小化开销。

## 开始使用`petite-vue`

要开始使用`petite-vue`，您需要包含一个指向`petite-vue`包的`script`标签。举个例子，让我们创建一个由`petite-vue`支持的简单投票应用程序。

首先，创建一个`index.html`文件。在它的主体中，添加以下代码片段:

```
 <script src="https://unpkg.com/petite-vue" defer init></script>
  <div v-scope="{ upVotes: 0, downVotes: 0 }">
    <p>
      {{ upVotes }} <button @click="upVotes++">&#128077;</button>
    </p>
    <p>
      {{ downVotes }} <button @click="downVotes++">&#128078;</button>
    </p>
  </div>

```

在浏览器解析 HTML 内容之后，`script`标签上的`defer`属性导致脚本加载`petite-vue`被加载。`init`属性告诉`petite-vue`自动查询并初始化所有有`v-scope`的元素，`v-scope`告诉`petite-vue`处理页面的哪个区域。我们还传递了对该区域可用的`upVotes`和`downVotes`状态。

### 手动初始化

如果您不想让`petite-vue`自动初始化所有具有`v-scope`属性的元素，您可以通过更改脚本来手动初始化它们:

```
<script src="https://unpkg.com/petite-vue"></script>
<script>
  PetiteVue.createApp().mount()
</script>

```

或者，您可以使用`petite-vue`的 ES 模块构建:

```
<script type="module">
  import { createApp } from 'https://unpkg.com/petite-vue?module'
  createApp().mount()
</script>

```

### `petite-vue` CDN 制作网址

在我们的例子中，我们使用一个 [CDN URL](https://blog.logrocket.com/using-cdns-to-optimize-website-performance/) 访问`petite-vue`。我们使用了一个简写的 URL `[https://unpkg.com/petite-vue](https://unpkg.com/petite-vue)`，这对于原型制作来说很好，但是对于生产来说不太好。我们希望避免解析和重定向成本，所以我们将在生产中使用完整的 URL。

全局构建生产 URL `[https://unpkg.com/[email protected]/dist/petite-vue.iife.js](https://unpkg.com/petite-vue@0.4.1/dist/petite-vue.iife.js)`公开了`PetiteVue`全局，并且还支持自动初始化。

ESM 构建生产 URL `[https://unpkg.com/[email protected]/dist/petite-vue.iife.js](https://unpkg.com/petite-vue@0.4.1/dist/petite-vue.iife.js)`必须在`<scripttype="module">`块中使用。

## 何时使用`petite-vue`

我们已经了解了很多关于`petite-vue`的特性和它能做什么。让我们回顾一下`petite-vue`最适合哪种情况:

*   不需要构建工具时的快速原型制作
*   在服务器呈现的框架中添加 Vue 功能，如 Sails、Laravel 或 Rails
*   构建登录页面或营销页面，这些页面可以是静态的，很少交互
*   任何你通常会用到阿尔卑斯山的地方

## 结论

是 Vue 的一个轻量级版本，为页面增加了高效的交互。在本文中，我们通过考虑其独有的功能、与标准 Vue 的相似性以及为获得其轻质尺寸而进行的功能权衡，对`petite-vue`进行了初步了解。

在撰写本文时，`petite-vue`仍然非常新，并且包含了对任何潜在错误的免责声明。然而，`petite-vue`已经是一个很有潜力和实用性的功能选项了。它对于快速原型开发、将 Vue 功能分散到服务器呈现的框架中以及构建静态页面特别有帮助。

我希望你喜欢这篇文章！给我们留下评论，让我们知道你是否打算尝试`petite-vue`。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。