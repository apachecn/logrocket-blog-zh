# 用 Nuxt 的组合 API 处理服务器端应用程序

> 原文：<https://blog.logrocket.com/handling-server-side-applications-with-nuxts-composition-api/>

最近，生态系统中采用 Vue 的组合 API 的情况有所增加。它在组件增长时维护组件的能力，以及它对重用组件的鼓励，在许多大型项目中都很有用。由于服务器端渲染，用 Nuxt 写的项目很难使用 Vue 的 Composition API。Nuxt 团队观察到了这一点，并为用 Nuxt 编写的项目整合了一个组合 API。在本文中，我们将看看 Nuxt 组合 API 是如何工作的，它对 Vue 组合 API 的影响，以及在项目中使用它的最佳实践。

## 入门指南

要将 Nuxt 的 Composition API 添加到您的项目中，请像安装插件一样安装它:

```
npm install @nuxtjs/composition-api --save

/*  For Yarn users */

yarn add @nuxtjs/composition-api
```

成功完成后，在 Nuxt 配置文件中启用该模块:

```
{
  buildModules: [
    '@nuxtjs/composition-api'
  ]
}
```

## 处理应用程序数据

用户在 Nuxt 项目中使用 Vue 的组合 API 时遇到的主要挑战来自于在应用程序的服务器端和客户端之间的通信期间没有返回数据。假设我们想要从 API 中检索数据并显示在 Nuxt 应用程序上:

```
import { ref } from '@nuxtjs/composition-api'

function useTodo () {
  const todo = ref({})

  const fetchPost = async (id) => {
    fetch('https://gorest.co.in/public-api/todos/' + user_id)
      .then(response => response.json())
      .then(json => post.value = json)
  }

  return {
    todo,
    fetchTodo
  }  
}
```

当我们试图在 Nuxt 应用程序中导入并使用`useTodo`函数时，从 API 中检索到的数据无法到达服务器端，因为获取数据的基本操作是异步的，而默认情况下，服务器端的实现是同步运行的。因此，下面的代码示例将返回一个空的`div`:

```
<template>
  <div>{{ data.title }}</div>
</template>

<script>
import { useTodo } from '@/composables'

export default defineComponent({
  setup (props, { root }) {
    const { todo, fetchTodo } = useTodo($root.route.params.user_id)

    fetchTodo()

    return { todo }
  }
})
</script>
```

Nuxt 的 Composition API 提供了一个名为`useFetch`的包装器，用于从服务器端获取数据并将其转发给客户端。它在防止多个异步网络调用方面做了有用的工作，因为它确保导航只在服务器端进行一次，并且结果数据由我们的客户端代码检索并用作其初始状态。使用一个模拟 REST API 示例，让我们看看如何使用`useFetch`包装器检索数据:

```
import { defineComponent } from '@nuxtjs/composition-api'
import { ref, useFetch } from '@nuxtjs/composition-api'

function useTodo (id) {
  const todo = ref({})

  const { fetch: fetchTodo } = useFetch(async () => fetch('https://gorest.co.in/public-api/todos/' + user_id)
    .then(response => response.json())
    .then(json => todo.value = json)
  )

  return {
    post,
    fetchTodo
  }  
}
```

在上面的代码块中，`useFetch`返回`fetchTodo`——一个用于进行异步调用的 fetch 函数。一旦第一次进行这个调用，我们的服务器端代码将包含我们的待办事项列表:

```
<div key="0">
  Aggredior the testimony of the multitude, therefore I will restore the color of whic   h I see.
</div>
```

## 使用 Nuxt 上下文访问附加模块

组合 API 还提供了访问由 Nuxt 上下文提供的属性的能力，比如存储、路由或环境变量。它通过一个名为`useContext`的包装器来实现这个功能，这个包装器也可以访问 Nuxt 模块属性。让我们看看`context`如何利用`nuxtjs/axios`模块发出请求。我们要做的第一件事是安装我们想要从中访问属性的模块:

```
npm install @nuxtjs/axios
```

接下来，我们将它添加到我们的 Nuxt 配置文件中:

```
// nuxt.config.js

export default {
  modules: [
    '@nuxtjs/axios',
  ],

  axios: {
    // proxy: true
  }
}
```

然后我们调用`$axios`属性来使用这个模块。让我们试着用`@nuxt/axios`替换前面的`fetchTodo`示例中的本机`fetch`方法:

```
import { defineComponent } from '@nuxtjs/composition-api'
import { ref, useFetch, useContext } from '@nuxtjs/composition-api'

function useTodo (id) {
  const todo = ref({})
  const { @axios } = useContext()

  const { fetch: fetchTodo } = useFetch(async () =>
      todo.value = await $axios.$get('https://gorest.co.in/public-api/todos/' + id)

  return {
    post,
    fetchTodo
  }  
}
```

## 更新标题和 HTML 属性

Composition API 提供了一个`usemeta()`钩子，使您能够直接与应用程序的标题属性和 meta 标签进行交互。使用`useMeta()`，您可以设置并限制对一个组件的标题状态的修改。让我们来看看这是如何工作的:

```
<template>
  <div>
    Welcome Back
    <nuxt />
  </div>
</template>

import {
  defineComponent,
  useContext,
  useMeta,
  watchEffect,
} from "nuxt-composition-api";

export default defineComponent({
  name: "Home",
  head: {},
  setup() {
    const { route } = useContext();
    const { title } = useMeta();
    watchEffect(() => {
      title.value = route.value.path;
    });
  },
});
</script>
```

在上面的代码示例中，我们利用`useMeta()`标签将我们的头值设置为由`useContext`包装器定义的路由——我们还可以进一步定义路由并个性化它们的头:

```
// StudentProfile.vue

<template>
  <div>
    Student's Profile
    <nuxt-link to="/Grades">Grades</nuxt-link>
  </div>
</template>
<script lang="ts">
import { defineComponent, useMeta } from "nuxt-composition-api";
export default defineComponent({
  name: "StudentProfile",
  head: {},
  setup() {
    const { title } = useMeta();
    title.value = "Here is your profile";
  },
});
</script>
```

通过`useMeta`标签分配给`title.value`的值定义了我们正在查看的任何路线的标题。另外，请注意，`head`属性的声明对于`useMeta`标签的工作非常重要:

```
// Grades.vue

<template>
  <div>
    Grades
    <nuxt-link to="/StudentProfile">Student's Profile</nuxt-link>
  </div>
</template>
<script lang="ts">
import { defineComponent, useMeta } from "nuxt-composition-api";
export default defineComponent({
  name: "Grades",
  head: {},
  setup() {
    const { title } = useMeta();
    title.value = "View your grades here";
  },
});
</script>
```

在浏览器上，它看起来是这样的:

 [https://www.youtube.com/embed/oMDb3hirA58?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/oMDb3hirA58?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

## 组合 API 的性能注意事项

Nuxt 的组合 API 在事实检查方面非常严格，因此在使用过程中需要遵守一些规则。其中一个规则是，当在全局[组件](https://pikax.me/vue-composable/composable/)中应用`useAsync`和`ssrRef`这样的帮助函数时，要确保使用一个唯一的键。未能为相关函数设置唯一键可能会导致常量或变量采用未分配给它们的值。看看下面的代码示例:

```
function useTodo() {

// Only one unique key is generated, no matter how many times this function is called.

    const todo = ssrRef('')

    return todo
}

const a = useTodo()
const b = useTodo()

b.value = 'Buy Gas'
```

在上面的代码示例中，如果没有设置唯一键，客户端上的值`a`也会被初始化为`Buy Gas`。如果给`useTodo`功能分配一个键，就可以防止这种情况:

```
function useTodo(path: string) {
    const task = useAsync(
        () => fetch(`https://api.com/slug/${path}`).then(r => r.json()),
        path,
    )

    return {
        task
    }
}

export default useTodo
```

## 结论

Nuxt 的组合 API 是出于更无缝地处理服务器端应用程序中的组合的需要而创建的。尽管它的采用仍处于早期阶段——看到它支持大型项目并取得与 Vue 的 Composition API 类似的成功并不奇怪。如果你需要看一下这篇文章中的一个例子的代码演示，你可以点击这里查看。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。