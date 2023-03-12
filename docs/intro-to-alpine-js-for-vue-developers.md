# 面向 Vue 开发者的 Alpine.js 简介

> 原文：<https://blog.logrocket.com/intro-to-alpine-js-for-vue-developers/>

Alpine 是一个相对较新的 JavaScript 框架，它借鉴了 Vue 和 React 等其他框架的概念。与这些框架相比，Alpine 既小又轻。

在本指南中，我们将放大 [Alpine.js](https://blog.logrocket.com/getting-started-with-alpine-js/) 和 Vue，并演示如何在 Alpine 中复制 Vue 任务和功能。但是这不会是一篇典型的 X 框架比 Y 框架更好的文章；两者本身都是独一无二的，更合适的框架通常取决于用例。

## 什么是阿尔卑斯山？

在我们开始之前，让我们花点时间来理解 Alpine 背后的哲学。正如它的标语所说，Alpine 是“一个坚固的、在标记中组成 JavaScript 行为的最小框架。”这些行为是使用指令作为属性添加的。

阿尔卑斯语法很大程度上受 Vue 的启发。如果你来自一个 Vue 背景，你很可能会觉得和 Alpine 一起工作很自在。和 Vue 一样，Alpine 在所有指令中都使用了前缀`x-`。与 Vue 不同，Alpine 不使用虚拟 DOM，所以我们可以直接使用实际的 DOM。

## 初始化组件

在 Alpine 中，`x-data`指令用于声明组件的范围和数据。它是 Vue 的`el`和`data`属性的组合。它告诉框架用定义的数据对象初始化一个新组件。

```
// in Vue

<div id="app"></div>

new Vue({
  el: '#app',
  data: {
    posts: []
  }

  // or in the case of a component
  data() {
    return {
      posts: []
    }
  }
})

// in Alpine

<div x-data="{ posts: [] }"></div>

```

您可以将数据提取到一个可重用的函数中，而不是内联声明数据。

```
// in Alpine

<div x-data="posts()"></div>

<script>
  function posts() {
    return {
      posts: []
    }
  }
</script>

```

## 挂钩到组件初始化阶段

与 Vue 不同，Alpine 不提供任何显式的生命周期挂钩。然而，你可以使用`x-init`指令在 Alpine 中实现 Vue 的两个生命周期挂钩——`created`和`mounted`。

`x-init`指令用于在组件初始化时执行一些 JavaScript。这包括从简单的表达式到复杂的函数。把它想象成 Vue 的`created`钩子。

```
// in Vue

<script>
  export default {
    name: 'App',
    data() {
      return {
        posts: []
      };
    },
    created() {
      fetch("https://jsonplaceholder.typicode.com/posts")
        .then(response => response.json())
        .then(data => (this.posts = data))
    }
  }
</script>

// in Alpine

<div
  x-data="{ posts: [] }"
  x-init="
    fetch('https://jsonplaceholder.typicode.com/posts')
      .then(response => response.json())
      .then(data => (posts = data));
  "
>
  <!-- `posts` will be array of a posts returned from the API -->
</div>

```

为了实现 Vue 的`mounted`钩子，您可以从`x-init`返回一个回调，它将在 Alpine 对 DOM 进行初始更新后运行。

```
// in Vue

<script>
  export default {
    name: 'App',
    data() {
      return {
        posts: []
      };
    },
    mounted() {
      fetch("https://jsonplaceholder.typicode.com/posts")
        .then(response => response.json())
        .then(data => (this.posts = data))
    }
  }
</script>

// in Alpine

<div
  x-data="{ posts: [] }"
  x-init="() => {
    fetch('https://jsonplaceholder.typicode.com/posts')
      .then(response => response.json())
      .then(data => (posts = data));
  }
  "
></div>

```

## 插入文字

与 Vue 不同，Alpine 不使用类似小胡子的语法进行插值。相反，它使用`x-text`和`x-html`指令进行插值，其工作方式与它们的 Vue 对应物相同。

```
// in Vue

<template>
  <div>
    <h3>{{ post.title }}</h3>

    <h3 v-text="post.title"></h3>

    <h3 v-html="post.title"></h3>
  </div>
</template>

<script>
  export default {
    name: 'App',
    data() {
      return {
        post: {
          title: "My first post"
        }
      }
    }
  }
</script>

// in Alpine

<div x-data="{ post: { title: 'My first post' } }">
  <h3 x-text="post.title"></h3>
</div>

<div x-data="{ post: { title: 'My first post' } }">
  <h3 x-html="post.title"></h3>
</div>

```

## 事件处理

就像 Vue 一样，Alpine 通过提供一个`x-on`指令来无缝处理鼠标和键盘事件。这个指令将一个事件监听器附加到一个元素上，并在事件被触发时执行一些 JavaScript。

```
// in Vue

<template>
  <div>
    <button @click="deletePost(post)">Delete</button>
  </div>
</template>
<script>
export default {
  name: 'App',
  data() {
    return {
      post: {
        title: "This is my first post"
      }
    };
  },
  methods: {
    deletePost(post) {
      confirm(`Want to delete: ${this.post.title}?`)
    }
  }
};
</script>

// in Alpine

// inline
<div x-data="{ post: { title: 'My first post' } }">
  <button x-on:click="confirm(`Want to delete: ${post.title}?`)">
    Delete
  </button>
</div>

// dedicated function
<div x-data="{ post: { title: 'My first post' } }">
  <button x-on:click="deletePost(post)">
    Delete
  </button>
</div>

<script>
  function deletePost(post) {
    confirm(`Want to delete: ${post.title}?`)
  }
</script>

```

如果您更愿意使用简写形式(`@`)，上面的内容可以改写为:

```
// in Alpine

<div x-data="{ post: { title: 'My first post' } }">
  <button @click="confirm(`Want to delete: ${post.title}?`)">
    Delete
  </button>
</div>

```

就像在 Vue 中一样，可以使用`$event`属性访问 DOM 事件。

注意:`$event`属性只在 DOM 表达式中可用。当使用一个函数时，你必须作为一个参数传递给函数:`<button @click="deletePost(post, $event)"></button>`。

Alpine 还提供了一些事件修改器，包括`keydown`、`away`、`prevent`、`passive`、`stop`、`debounce`、`self`、`once`、`window`等。

```
// in Alpine

<a href="http://example.com" @click.prevent>Example.com</a>

```

## 双向数据绑定

使用表单时，Alpine 提供双向数据绑定，使输入值与组件数据保持同步。

```
// in Vue

<template>
  <div>
    <h3>{{ title }}</h3>

    <input type="text" v-model="title">
  </div>
</template>

<script>
  export default {
    name: 'App',
    data() {
      return {
        title: ''
      }
    }
  }
</script>

// in Alpine

<div x-data="{ title: '' }">
  <h3 x-text="title"></h3>

  <input type="text" x-model="title" />
</div>

```

## 遍历数组

要迭代数组，可以使用`x-for`指令，它的工作方式类似于 Vue 的`v-for`。

```
// in Vue

<ul>
  <li v-for="post in posts" :key="post.id">
    {{ post.title }}
  </li>
</ul>

// in Alpine

<ul>
  <template x-for="post in posts" :key="post.id">
    <li x-text="post.title"></li>
  </template>
</ul>

```

然而，与`v-for`不同的是，`x-for`有一些一些告诫:

*   `x-for`必须在`template`标签上，而不是常规的 DOM 元素上。
*   `<template>`标签中必须有一个元素根。

## 绑定属性

Alpine 支持使用`x-bind`指令的属性绑定。`x-bind`指令使您能够将 HTML 属性绑定到 JavaScript 表达式。该指令的工作方式与`v-bind`相同。

```
// in Vue

<template>
  <div>
    <h3>{{ post.title }}</h3>

    <img
      v-bind:src="post.featuredImage"
      v-bind:alt="post.title"
      v-bind:class="{ hidden: isOnMobile }"
    >

    <button v-bind:disabled="post.isPublished">Publish</button>
  </div>
</template>

<script>
  export default {
    name: 'App',
    data() {
      return {
        post: {
          title: "My first post",
          featuredImage: "https://dummyimage.com/600x400",
          isPublished: false
        },
        isOnMobile: true
      }
    }
  }
</script>

<style>
  .hidden {
    display: none
  }
</style>

// in Alpine

// within the head tag
<style>
  .hidden {
    display: none;
  }
</style>

<div
  x-data="{
    post: {
      title: 'My first post',
      featuredImage: 'https://dummyimage.com/600x400',
      isPublished: false
    },
    isOnMobile: true,
  }"
>
  <h3 x-text="post.title"></h3>

  <img
    x-bind:src="post.featuredImage"
    x-bind:alt="post.title"
    x-bind:class="{ hidden: isOnMobile }"
  />

  <button x-bind:disabled="post.isPublished">Publish</button>
</div>

```

与事件处理一样，您也可以使用简写形式(`:`)。

```
// in Alpine

<img
  :src="post.featuredImage"
  :alt="post.title"
  :class="{ hidden: isOnMobile }"
/>

<button :disabled="post.isPublished">Publish</button>

```

## 切换可见性

Alpine 提供了两个切换可见性的指令——`x-if`和`x-show`——它们的工作方式与 Vue 类似。

```
// in Vue

<template>
  <div>
    <button @click="show = !show" v-text="show ? 'Close' : 'Open'"></button>

    <a href="#" v-if="isAdmin">Edit Post</a>
  </div>
</template>

<script>
  data() {
    return {
      show: false,
      isAdmin: false
    };
  }
</script>

// in Alpine

<div x-data="{ show: false, isAdmin: false  }">
  <button @click="show = !show" x-text="show ? 'Close' : 'Open'"></button>

  <template x-if="isAdmin">
    <a href="#">Edit Post</a>
  </template>
</div>

```

和`x-for`一样，`x-if`也有一些注意事项:

*   `x-if`必须在`template`标签上，而不是常规的 DOM 元素上。
*   `<template>`标签中必须有一个元素根。

## 注意

Alpine 还支持通过一种叫做`$watch()`的方法来监视组件属性的变化。

```
// in Vue

<template>
  <div>
    <p>
      <label>Dollar:</label>
      <input type="text" v-model="dollar">
    </p>
    <p>
      <label>Naira:</label>
      <input type="text" v-model="naira">
    </p>
  </div>
</template>

<script>
  export default {
    name: 'App',
    data() {
      return {
        dollar: 0,
        naira: 0
      };
    },
    watch: {
      dollar(value) {
        this.naira = value * 390
      },
      naira(value) {
        this.dollar = value / 390
      }
    }
  }
</script>

// in Alpine

<div x-data="watcher()" x-init="initWatcher">
  <p>
    <label>Dollar: </label>
    <input type="text" x-model="dollar" />
  </p>
  <p>
    <label>Naira: </label>
    <input type="text" x-model="naira" />
  </p>
</div>
<script>
  function watcher() {
    return {
      dollar: 0,
      naira: 0,
      initWatcher() {
        this.$watch("dollar", value => {
          this.naira = value * 390;
        });
        this.$watch("naira", value => {
          this.dollar = value / 390;
        });
      }
    };
  }
</script>

```

`$watch()`接受两个参数:要监视的属性和在属性改变时运行的回调。回调接受属性的新值。

## 结论

如你所见，Alpine 和 Vue 在很多方面都很相似。事实上，在某些情况下，您可以使用 Vue 组件，简单地将前缀`v-`替换为`x-`来创建 Alpine 组件。

要了解更多关于 Alpine 的信息，请查看 GitHub repo 。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。