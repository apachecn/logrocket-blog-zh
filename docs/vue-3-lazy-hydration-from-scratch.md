# 从零开始在 Vue 3 中实现惰性水合作用

> 原文：<https://blog.logrocket.com/vue-3-lazy-hydration-from-scratch/>

随着 web 应用程序的复杂性和规模不断增长，对性能的关注也在增加。开发人员通常通过采用服务器端呈现(SSR)来从客户端卸载一些呈现过程来解决这个问题。

然而，即使在服务器上呈现 HTML，网站性能仍然会受到影响。虽然 HTML 以快速和 SEO 友好的方式交付，但水合过程——使应用程序在客户端交互——可能是昂贵的。反过来，像交互时间(TTI)和估计输入延迟(EIL)这样的指标对于复杂、深度嵌套 HTML 的应用程序来说会直线下降。

现在，你可以用代码分割等技术来解决这个问题，或者在延迟代码交付的同时立即加载应用程序的重要部分，并补充其他组件。这可能会提高您的度量，但仍然会在用户看不到或无法与之交互的组件上浪费加载时间。

这就是懒惰的水合作用介入的地方。让我们看看那是什么，它是如何工作的，以及如何在 [Vue 3](https://blog.logrocket.com/new-features-in-vue-3-and-how-to-use-them-2/) 中实现它。

## 部分水合与惰性水合

为了理解水合作用的变体和它们如何工作，你首先需要熟悉部分水合作用。

顾名思义，在部分水合中，你只水合应用程序的某些部分。这在实现所谓的[“孤岛架构”](https://jasonformat.com/islands-architecture)时非常有用，其中不同的应用程序部分被视为独立的实体。这使得应用程序的每个部分都独立于其他部分运行，这使得它们可以单独水合。

让我们想想部分水合和岛屿架构如何应用于像博客这样的网站。你可以将工具栏和评论区等交互部分水化，但将其他部分如内容本身保持完全静态。这种方法提高了你的网站的性能和 UX，并且没有资源浪费在静态内容上，使得交互部分更快地水合。

惰性水合作用建立在部分水合作用的概念上，并使其更进一步。这个概念在任何已经包含 SSR、基本水合和异步组件的框架的实现中都是相似的。

你不仅可以决定 web 应用程序的哪些部分应该被补充水分，还可以决定什么时候应该补充水分。例如，只有当组件空闲时，当它在视口中时，或者在响应各种其他触发器(如`Promise`解析或用户交互)时，您才能合成组件。

这将资源节约和性能优化带到了另一个层次。您不再需要为用户永远看不到或接触不到的组件补水，让 TTI 几乎是瞬间完成的！

## Vue 的惰性水合作用

Vue 2 有一个很棒的、相当受欢迎的库，名为 [vue-lazy-hydration](https://github.com/maoberlehner/vue-lazy-hydration) 。它提供了一个无渲染的`LazyHydrate`组件和一堆像`hydrateWhenVisible`这样的手动函数包装器，用于包装你想延迟合成的组件。它还允许您在不同条件下进行水合，例如:

*   当浏览器空闲时(用`requestIdleCallback`)
*   当组件在视口内时(用`IntersectionObserver`)
*   在用户的交互上(`click`、`mouseover`等)。)
*   带有手动触发器(`Promise`)、布尔开关等。)
*   `never`(适用于静态、仅含 SSR 的组件)

可悲的是，在发布时，这个或任何其他突出的懒惰水合库都不支持 Vue 3。也就是说，Vue 3 的 vue-lazy-hydration 支持正在开发中，似乎有计划在 Nuxt 3 出来后在 T2 发布。

这让我们要么[继续使用 Vue 2](https://blog.logrocket.com/refactoring-your-vue-2-apps-to-vue-3/) 进行惰性水合，要么实现我们自己的机制，这就是我们在本文中要做的。

## 在 Vue 3 中实现延迟水合

有了像 Vue 这样内置 SSR 和水合支持的 UI 框架，实现惰性水合就相当容易了。

您将需要一个包装器或无呈现组件，在服务器上自动呈现您的组件，同时在客户端使用条件呈现来延迟水合，直到满足某些条件。

我决定基于[反应-懒惰-水合](https://github.com/hadeeb/react-lazy-hydration)来实现 Vue 3 懒惰水合。它的代码比 vue-lazy-hydration 的更简单，而且可翻译性更好，React Hooks 可以很好地与 Vue Composition API 进行转换。

### 组件声明和道具

我们从一个基本的 Vue 3 组件开始，它包含了额外的类型脚本和一个用于检查浏览器全局变量是否可用的`isBrowser`实用函数。

```
<script lang="ts">
import { defineComponent, onMounted, PropType, ref, watch } from "vue";

type VoidFunction = () => void;

const isBrowser = () => {
  return typeof window === "object";
};

export default defineComponent({
  props: {},
  setup() {},
});
</script>
<template></template>

```

我们的惰性水合包装器将包括与前面提到的库类似的功能。为此，我们必须接受相当广泛的配置属性。

```
// ...
export default defineComponent({
  props: {
    ssrOnly: Boolean,
    whenIdle: Boolean,
    whenVisible: [Boolean, Object] as PropType<
      boolean | IntersectionObserverInit
    >,
    didHydrate: Function as PropType<() => void>,
    promise: Object as PropType<Promise<any>>,
    on: [Array, String] as PropType<
      (keyof HTMLElementEventMap)[] | keyof HTMLElementEventMap
    >,
  },
  // ...
});
// ...

```

有了上面的道具，我们将支持仅支持 SSR 的静态组件，以及当浏览器空闲、组件可见或在给定的`Promise`解析后的水合。

最重要的是，`on`将支持用户交互的水合，而`didHydrate`将允许组件水合后的回调。

### 设置功能

在`setup`中，我们首先初始化几个必需的值。

```
// ...
export default defineComponent({
  // ...
  setup() {
    const noOptions =
      !props.ssrOnly &&
      !props.whenIdle &&
      !props.whenVisible &&
      !props.on?.length &&
      !props.promise;
    const wrapper = ref<Element | null>(null);
    const hydrated = ref(noOptions || !isBrowser());
    const hydrate = () => {
      hydrated.value = true;
    };
  },
});
// ...

```

我们将使用一个`wrapper`模板 ref 来访问包装器元素，使用一个`hydrated` ref 来保存反应布尔值，该值决定了水合的当前状态。

注意我们如何初始化`hydrated` ref。当没有设置选项时，默认情况下，组件将立即水合。否则，在通过 SSR 时，水合将在客户端延迟。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`hydrate`只是一个设置`hydrated`为`true`的单向辅助函数。

### 水合回调注册

接下来，我们开始创建逻辑，用一个`onMounted`回调和一个`watch`效果。

```
// ...
onMounted(() => {
  if (wrapper.value && !wrapper.value.hasChildNodes()) {
    hydrate();
  }
});
watch(
  hydrated,
  (hydrate) => {
    if (hydrate && props.didHydrate) props.didHydrate();
  },
  { immediate: true }
);
// ...

```

在`onMounted`回调中，我们检查元素是否有任何子元素。如果没有，我们可以立即补水。

`watch`效果处理`didHydrate`回调。注意`immediate`选项——无论是在 SSR 期间还是在没有提供选项的情况下，当水合作用没有延迟时，这个选项都很重要。

### 设置主要`watch`效果

现在，我们进入主要的`watch`效果，它将处理所有选项并适当地设置`hydrated` ref。

```
// ...
watch(
  [() => props, wrapper, hydrated],
  (
    [{ on, promise, ssrOnly, whenIdle, whenVisible }, wrapper, hydrated],
    _,
    onInvalidate
  ) => {
    if (ssrOnly || hydrated) {
      return;
    }

    const cleanupFns: VoidFunction[] = [];
    const cleanup = () => {
      cleanupFns.forEach((fn) => {
        fn();
      });
    };

    if (promise) {
      promise.then(hydrate, hydrate);
    }
  },
  { immediate: true }
);
// ...

```

该效果将触发道具以及`wrapper`和`hydrate`裁判的变化。

首先，我们检查组件是只在服务器端呈现，还是已经被水合。我们这样做是因为，在这两种情况下，都不需要进一步评估效果，所以我们可以从函数中`return`。

如果该过程继续，我们初始化当效果无效时的清理函数，并处理基于`Promise`的惰性水合。

### 基于可见性的水合作用

接下来，仍然在效果内部，我们处理基于可见性的水合作用。如果支持`IntersectionObserver`,我们初始化它，传递默认或提供的选项。否则，我们会立即补充水分。

```
// ...
if (whenVisible) {
  if (wrapper && typeof IntersectionObserver !== "undefined") {
    const observerOptions =
      typeof whenVisible === "object"
        ? whenVisible
        : {
            rootMargin: "250px",
          };

    const io = new IntersectionObserver((entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting || entry.intersectionRatio > 0) {
          hydrate();
        }
      });
    }, observerOptions);

    io.observe(wrapper);

    cleanupFns.push(() => {
      io.disconnect();
    });
  } else {
    return hydrate();
  }
}
// ...

```

请注意从`wrapper`元素断开`IntersectionObserver`实例的清理回调。

### 基于浏览器空闲的水合作用

我们遵循类似的基于浏览器空闲的水合结构，这一次使用了`requestIdleCallback`和`cancelIdleCallback`。

```
if (whenIdle) {
  if (typeof window.requestIdleCallback !== "undefined") {
    const idleCallbackId = window.requestIdleCallback(hydrate, {
      timeout: 500,
    });
    cleanupFns.push(() => {
      window.cancelIdleCallback(idleCallbackId);
    });
  } else {
    const id = setTimeout(hydrate, 2000);
    cleanupFns.push(() => {
      clearTimeout(id);
    });
  }
}

```

`requestIdleCallback`跨浏览器兼容性[低于 80%](https://caniuse.com/requestidlecallback)，特别是在 iOS 和 macOS 上都没有 Safari 的支持，所以我们必须用`setTimeout`实现一个回退，延迟水合并将其推到异步队列。

如果您正在使用 TypeScript，您应该注意到，目前，您不会在默认的 lib 中找到`requestIdleCallback`。为了正确输入，你需要安装[@ types/requestidlecallback](https://www.npmjs.com/package/@types/requestidlecallback)。

### 基于用户交互的水合作用

最后，我们处理基于用户事件的水合作用。在这里，事情相对简单，因为我们只需遍历事件并相应地设置事件侦听器。

```
if (on) {
  const events = ([] as Array<keyof HTMLElementEventMap>).concat(on);

  events.forEach((event) => {
    wrapper?.addEventListener(event, hydrate, {
      once: true,
      passive: true,
    });
    cleanupFns.push(() => {
      wrapper?.removeEventListener(event, hydrate, {});
    });
  });
}

onInvalidate(cleanup);

```

之后记得调用`onInvalidate`注册清理功能，效果准备好了！

### 完成模板

为了完成组件，从`setup`函数返回模板中需要的引用。

```
// ...
export default defineComponent({
  // ...
  setup() {
    // ...
    return {
      wrapper,
      hydrated,
    };
  },
});
// ...

```

然后，在模板中，呈现包装`<div>`，分配 refs，并有条件地呈现组件以进行惰性水合。

```
<template>
  <div ref="wrapper" :style="{ display: 'contents' }" v-if="hydrated">
    <slot></slot>
  </div>
  <div ref="wrapper" v-else></div>
</template>

```

## 使用我们的惰性水合成分

随着我们的懒惰水合组件准备就绪，是时候测试它了！

### 搭建我们的 Vue 3 SSR 应用

首先，你需要设置好你的环境，或者是 SSR-或者是[静态站点生成器(SSG)-就绪](https://blog.logrocket.com/static-site-generation-with-react-from-scratch/)。从技术上来说，任何带有预渲染 HTML 和 Vue 3 并启用了水合功能的东西都应该可以工作，但你的里程数可能会有所不同。

由于目前还没有 [Nuxt.js](https://github.com/nuxt/nuxt.js/issues/5708) 和 [Gridsome](https://github.com/gridsome/gridsome/issues/1289) 与 Vue 3 兼容，你最好的选择是使用像 [vite-plugin-ssr](https://vite-plugin-ssr.com/) 这样的东西。这样的解决方案将允许您在没有太多麻烦的情况下实现 SSR，同时利用 Vite 提供的丰富开发经验。

您可以使用以下命令搭建新的 vite-plugin-ssr 应用程序:

```
npm init [email protected]

```

然后，按照上面的指导或者从[这个 GitHub 要点](https://gist.github.com/areknawo/b7673ff99276edd4dee90a0a60b13bfd)中设置懒惰水合组件。

准备好之后，转到任何可用的页面，在`<LazyHydrate>`中包装一个交互组件，并使用它！

```
<template>
  <h1>Welcome</h1>
  This page is:
  <ul>
    <li>Rendered to HTML.</li>
    <li>
      Interactive. <LazyHydrate when-visible><Counter /></LazyHydrate>
    </li>
  </ul>
</template>
<script lang="ts">
import Counter from "./_components/Counter.vue";
import LazyHydrate from "./_components/LazyHydrate.vue";

export default {
  components:{
    Counter,
    LazyHydrate,
  }
};
</script>

```

使用不同的选项，查看组件何时交互，用`didHydrate`回调检查它何时水合，等等！

### 将惰性水合作用与异步成分相结合

为了进一步改善你的应用程序的 TTI 指标和加载时间，你可以将延迟水合与[异步组件](https://v3.vuejs.org/guide/component-dynamic-async.html#async-components)结合起来。这将把你的应用程序分割成更小的块，准备好按需加载。这样，你的懒惰水合成分将只在水合作用发生时加载。

```
import { defineAsyncComponent } from "vue";
import LazyHydrate from "./_components/LazyHydrate.vue";

export default {
  components: {
    Counter: defineAsyncComponent({
      loader: () => import("./_components/Counter.vue"),
    }),
    LazyHydrate,
  },
};

```

请记住，您必须小心使用这种方法，因为动态获取组件可能会给用户带来明显的延迟。在这种情况下，您必须有选择地推迟哪些组件，并且需要实现后备内容，比如在获取和解析代码时使用加载器。

然而，即使考虑到所有这些，惰性异步组件仍然有巨大的潜力来大幅提高大型复杂应用程序的性能，尤其是那些严重依赖交互式图形或隐藏对话框等元素的应用程序。

## 结果

这就是你所知道的——在 Vue 3 中解释并实现的惰性水合作用！使用本文中实现的组件，您可以优化您的 SSR/SSG 应用程序，提高其性能、响应能力和用户体验。

对于`<LazyHydrate>`组件的完整代码，请查看[GitHub 要点](https://gist.github.com/areknawo/b7673ff99276edd4dee90a0a60b13bfd)。请随意尝试。如果你有任何改进的想法，请在 GitHub 上告诉我。

一定要关注 vue-lazy-hydration 的更新。据说下一个版本将利用[新的 Vue 3 节点 API](https://github.com/maoberlehner/vue-lazy-hydration/issues/68#issuecomment-814942389)，因此可能会比这篇文章中的简单实现更具性能或提供更多功能。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。