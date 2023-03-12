# 使用 Vue 路由器的动态路由

> 原文：<https://blog.logrocket.com/dynamic-routing-using-vue-router/>

现代 web 应用程序非常复杂，通常包括多个层次结构、路由和路径；就其本身而言，静态路由(其中手动定义的路由是非自适应的并且具有固定值)不再足以满足现代开发者和应用用户的需求。

顾名思义，动态路由要灵活得多。动态路由允许我们根据实时网络变化定义多条路径来显示信息。我们可以使用动态路由使 URL 更容易记忆和用户友好，创建相似但包含不同数据的页面。

在本教程中，我们将使用 Vue 路由器探索 Vue 中的动态路由，了解它如何提高我们应用程序的性能。我们开始吧！

## 设置 Vue 应用程序

为了[理解 Vue](https://blog.logrocket.com/ultimate-guide-vue-router/) 中的动态路由，首先，我们将分别使用 Vue CLI 或 Yarn 设置一个新的 Vue 应用程序:

```
# Vue CLI
npm install -g @vue/cli
# yarn
yarn global add @vue/cli

```

运行以下代码以验证 Vue 是否已正确安装:

```
vue --version

```

现在，安装了 Vue 之后，我们可以创建一个新的应用程序，如下所示:

```
vue create routing-example

```

要在 Vue 中创建一个路由，我们需要定义一个链接到特定组件的路径，我们希望在加载 URL 后显示该组件。

对于我们的例子，我们将创建一个有两个页面的应用程序，一个主页和一个博客页面。让我们定义这两个页面的视图:

> 注意:为了简单起见，下面的代码不包含我们两个页面的任何样式或内容，而是侧重于路由组件:

```
// /views/Home.vue
<template>
  <div>
    <h1> Welcome to Home page </h1>
    <p>This is the page that opens on <strong>/</strong> route</p>
  </div>
</template>

// /views/Blogs.vue
<template>
  <div>
    <h1> Welcome to blogs page </h1>
    <p>This is the page opens on <strong>/blogs</strong> route</p>
  </div>
</template>

```

## 添加静态路由

接下来，让我们向组件添加两条静态路由。我们还将定义一个组件来显示到达路线的时间。创建一个名为`routes`的单独文件夹；在内部，创建一个名为`index.js`的文件，并添加以下代码:

```
// /router/index.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Home from '../views/Home.vue'
import Blogs from '../views/Blogs.vue'

Vue.use(VueRouter)

const routes = [
    {
        path: '/',
        name: 'Home',
        component: Home
    },
    //  Added our new route file named profile.vue
    {
        path: '/blogs',
        name: 'Blogs',
        component: Blogs
    }
]

// Create Vue Router Object
const router = new VueRouter({
    mode: 'history',
    base: process.env.BASE_URL,
    routes
})

export default router

```

为了运行我们的应用程序，我们将向我们的`App.vue`文件添加一个路由器，如下所示:

```
// App.vue
<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/blogs">Blogs</router-link>
    </div>
    <router-view/>
  </div>
</template>

<script>

export default {
  name: 'App',  
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}

#nav {
  font-size: 1.25rem;
  margin: 2rem;
}
</style>

```

如果你前往`[http://localhost:8080/](http://localhost:8080/)`，你会看到打开主页和博客页面的两条路线。

## 在 Vue 中添加动态路由

当你的应用程序简单时，使用静态路由是相当简单的，然而，实际上，你的项目将比主页和博客页面更复杂。

例如，假设我们的博客有 100 篇文章，每篇都有一个唯一的 URL。为每篇博客文章创建一个新组件并单独添加一个路径并不是一个可行的选择。

相反，我们可以在应用程序中添加动态路由。首先，让我们创建一个新的`Post`组件，它将作为显示每个独特博客的模板:

```
// /views/Post.vue
<template>
  <div>
    <h1> Welcome to Post page </h1>
    <p>This is the page that opens on <strong>/blog/{{ $route.params.id }}</strong> route</p>
  </div>
</template>

```

用`routes`数组中的新路线更新`router/index.js`文件。记住包括`Post`组件的导入。

在下面的代码中，我们使用`:id`作为路线中的动态值:

```
{
    path: '/post/:id',
    name: 'Post',
    component: Post,
}

```

重启你的应用程序，在浏览器中进入`localhost:8080/post/dynamic-routing`。您应该会看到上面有确切路线的`post`页面。

现在，当您向博客页面添加新文章时，`Post`组件会为 URL 创建一个模板，将其简化并使用`:id`作为动态值。我们可以以编程方式传递任何 ID，一个单一的路由将处理所有这些请求。

## 嵌套路由

既然我们已经使用动态路由定义了博客的基本结构，那么我们可以使用嵌套路由来定义与博客相关的附加特性，嵌套路由将多个路由组合成一个特定的层次结构。

例如，假设我们为作者`example.com/author`定义了一条基本路线。在`author`中，我们为个人作者嵌套了以下页面:

*   `example.com/author/mohit`
*   `example.com/author/john`

为了查找单个作者写的所有文章，我们将在层次结构上再向下嵌套一层，并创建一个遵循类似于`/author/mohit/posts`的结构的嵌套路由。

首先，向`routes`数组添加另一个元素。在包含该路线所有子路线的对象中，我们将添加一个`children`字段:

```
{
    path: '/author/:id',
    name: 'Author',
    component: Author,
    children: [
            {
                    path: 'posts',
                component: AuthorPosts,
            }
    ]
}

```

## URL 重定向

URL 重定向，也称为 URL 转发，是一种我们可以用来从多个地址访问单个网页的技术。本质上，当用户试图打开一个已被重定向的 URL 时，浏览器会打开重定向到的 URL。URL 重定向是隐私保护和防止页面移动时断开链接的好方法。

为了重定向一个 URL，我们可以在`route`对象中添加`redirect`属性。例如，假设我们想根据文章的名称搜索一篇文章。我们可以使用查询参数将其重定向到类似的路由:

```
{
  // /search/postName -> /search?q=postName
  path: '/search/:postName',
  redirect: to => {
    return { path: '/search', query: { q: to.params.postName } }
  },
}

```

## 使用别名路由

别名是访问特定 URL 的另一个术语。例如，如果我们希望用户输入`[http://www.mohitkhare.com](http://www.mohitkhare.com)`就可以访问站点`mohitkhare.com`，我们可以给`mohitkhare.com`域添加一个别名。在这种情况下，`mohitkhare.com`是`[http://www.mohitkhare.com](http://www.mohitkhare.com)`别名的父站点。

我们还可以使用`alias`属性为路由添加一个别名。继续我们的例子，假设我们想要为`blogs`路由添加一个别名。无论用户何时进入`posts`路线，他们都会在博客页面上看到相同的内容:

```
{
  path: '/blogs',
  name: 'Blogs',
  component: Blogs,
  alias: '/posts'
}

```

## 摘要

在本教程中，我们探索了使用 Vue Router 向我们的 Vue.js 应用程序添加静态和动态路由。我们还学习了如何通过添加嵌套路由、URL 重定向和别名来改进和定制我们的路由。

通过动态路由，我们可以通过我们的路由发送动态数据，使我们能够简化冗长、难以理解的 URL，并将网页分类到嵌套的层次结构中。Vue 路由器包括许多其他伟大的功能，如命名路由和路由转换。我希望你喜欢这个教程！

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。