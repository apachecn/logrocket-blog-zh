# 如何用 Vue 构建一个无头 WordPress 站点

> 原文：<https://blog.logrocket.com/build-headless-wordpress-site-vue/>

直到几年前，开发人员还在使用传统的内容管理系统(CMS)以某种方式管理一切。我所说的一切，是指从后端(如数据和内容管理、角色、权限和管理任务)到前端(主要是 PHP 生成的视图)的一切。

然而，随着现代前端开发的出现，正在发生实质性的转变。在围绕内容管理系统涌现的大规模生态系统中，旧的、单一的内容管理系统不再有一席之地。相反，无服务器架构、以 API 为中心的微服务和静态站点生成器已经证明了它们的存在。

在本文中，我们将使用 Vue 作为前端框架，在 WordPress API 的帮助下创建一个无头 WordPress 环境来管理后端内容。本文的源代码可以在 [Git](https://github.com/ugwutotheeshoes/supabase-impression) [H](https://github.com/ugwutotheeshoes/gatsby-supabase) [ub](https://github.com/ugwutotheeshoes/gatsby-supabase) 上找到。请随意派生和克隆存储库，以便快速开始。

## 先决条件

要阅读本文，您需要以下内容:

*   Node.js 和 npm。运行`node -v && npm -v`命令来验证你已经安装了它们，或者[安装它们](https://nodejs.org/en/)
*   对 JavaScript 和 Vue 的基本理解
*   一个活跃的 [WordPress 账户](https://wordpress.com/start/domains)和站点
*   要在本文后面使用 WordPress 的插件特性，你需要一个 [WordPress 商业](https://wordpress.com/pricing/)账户

## 目录

## Vue 是什么？

Vue 是一个[渐进式 JavaScript 框架](https://blog.logrocket.com/how-to-use-vue-3-typescript/)，用于构建只关注视图层的用户界面。它包括一个库生态系统，帮助处理大型单页应用程序中的复杂性。

## 项目设置和安装

通过在终端中运行以下命令来安装 [Vue CLI](https://cli.vuejs.org/) :

```
npm install -g @vue/cli
# OR
yarn global add @vue/cli

```

安装 Vue CLI 后，我们将导航到首选目录并创建一个新项目:

```
vue create <project-name> 

```

导航到项目目录并安装所需的依赖项:

```
cd <project-name> 
npm i axios

```

在浏览器中运行`npm run serve`以在`[https://localhost:8080/](https://localhost:8080/)`启动开发服务器。在上面的代码片段中，`<project-name>`代表我们的应用程序的名称；你想怎么叫都行。

## 什么是无头 WordPress？

WordPress 是一个免费的、开源的、用 PHP 构建的单片 CMS，并与 MySQL 或 MariaDB 数据库配对。众所周知，WordPress 界面有一个强大的后端，可以处理数据和内容管理、角色和权限以及管理任务。WordPress 前端主要关注显示内容。

通过在前端应用程序中使用 REST API，WordPress 被分离成一个轻量级的内容管理系统，称为 headless WordPress。

REST API 是一种应用程序编程接口，它遵循 REST 架构风格，并允许与 RESTful web 服务进行交互。REST(表述性状态转移)是一组架构约束，而不是协议或标准。

REST 架构最受欢迎的特性之一是能够以多种格式交付数据，比如纯文本、HTML、XML、YAML 和 JSON。JSON 是使用最广泛的文件格式，因为它与语言无关，并且易于被人类和机器理解。

JSON 是一种轻量级、人类可读的开放标准数据格式，看起来像 JavaScript 中的对象。许多编程语言广泛支持 JSON，所以你可以用客户端 JavaScript 构建 WordPress 应用程序，比如块编辑器、移动应用程序、桌面或命令行工具。下面的代码示例展示了一个从 API 接收的 JSON 数据格式的例子:

```
[
  {
    "id": 1,
    "title": "Mollitia voluptatem quia atque facere accusamus facilis beatae doloribus.",
    "slug": "debitis-sed-quasi",
    "content": "Tenetur nesciunt est eligendi reiciendis non qui itaque explicabo. Voluptas id vel saepe...",
    "hit": 828226,
    "category": {
      "id": 26
    },
    "createdAt": "2021-12-22T16:58:37.058Z",
    "publishedAt": "2022-02-17T06:33:56.110Z",
    "image": [
      {
        "url": "http://loremflickr.com/640/480/abstract",
        "name": "f9b496df-63eb-43b4-8769-ffd47fc2f4a3",
        "status": "done",
        "type": "image/jpeg",
        "uid": "4b2e4eb0-a144-4b58-b524-5407f009bd64"
      }
    ],
  },
  {
    "id": 2,
    "title": "Quo praesentium laudantium.",
    "slug": "sed-molestiae-ut",
    "content": "Aut ipsam a aut sit exercitationem sed voluptates eaque. Impedit a est tenetur iste et rem voluptas similique qui...",
    "hit": 658411,
    "category": {
      "id": 45
    },
    "createdAt": "2022-02-26T23:41:57.808Z",
    "publishedAt": "2022-06-01T16:42:06.928Z",
    "image": [
      {
        "url": "http://loremflickr.com/640/480/abstract",
        "name": "cb05d57d-c97d-475e-b602-dad1a611a2a9",
        "status": "done",
        "type": "image/jpeg",
        "uid": "2c8ce7ad-5700-4207-b83c-fac51b37ea0c"
      }
    ],
  },
]

```

用于现代前端开发的 [WordPress REST API](https://developer.wordpress.org/rest-api/) 的引入为开发者提供了一种自由感和极大的灵活性，让他们可以从头开始构建令人惊叹的 UI。

WordPress REST API 提供 REST 端点、URL、页面、分类法和其他内置的 WordPress 数据类型。为了在 WordPress 站点上查询、更改和创建内容，前端应用程序可以通过 WordPress REST API 向发送和接收 JSON 数据。

## 配置我们的 WordPress 站点

让我们首先用一些虚拟帖子建立我们的 WordPress 网站，这些帖子将作为我们前端应用程序的数据源。前往**插件**部分激活 WordPress 网站中使用的 WordPress REST API 插件。你需要从 GitHub 下载 [WP-Rest API](https://github.com/WP-API/Basic-Auth) 插件的 zip 文件，并上传到 WordPress 插件文件夹。

接下来，进入**设置，**点击**永久链接**，然后选择**帖子名称**或者**自定义结构**底座，如下图所示:

![Configure WordPress Site Post Name](img/ae8e5c18f0e273e0326da1c3346aa0a6.png)

**Post name** base slug 作为 API 端点，看起来如下所示:

```
https://example.com/wp-json/wp/v2/posts

```

因为我们正在处理 API 调用，所以测试一下 [Postman](https://www.postman.com/) 上的 URL，看看我们的 WordPress 站点的 post 数据格式。

## 使用 WordPress API

为了在 WordPress REST API 中访问我们的文章，我们将把下面的代码块添加到我们的`App.vue`文件中:

```
export default {
  data() {
    return {
      post: [],
      isLoading: false,
    };
  },
  methods: {
    getPosts() {
      this.isLoading = true;
      fetch("https://example.com/wp-json/wp/v2/posts")
        .then((response) => response.json())
        .then((data) => {
          this.post = data;
          this.isLoading = false;
        });
    },
  },
  mounted() {
    this.getPosts();
  },
};
</script>

```

`getPosts()`方法控制我们的应用程序的加载状态，并对我们的 API URL 进行 API 调用。然后，它将 API 返回的数据存储在`post`数组中，该数组是在`data`属性中创建的。当 Vue 在浏览器中呈现我们的内容时， [`mounted()`](https://vuejs.org/api/options-lifecycle.html#mounted) 生命周期钩子调用`getPosts()`方法。

接下来，我们将显示 API 的内容。使用`App.vue`文件中的 Vue 指令，我们将显示 API 的内容，如下所示:

```
<template>
<div class="container">
    <p v-if="isLoading">Loading...</p>
    <ul v-for="p in post" :key="p.id">
      <h1>{{ p.title }}</h1>
      <p>{{ p.content }}</p>
      <p class="date">{{ p.createdAt }}</p>
    </ul>
  </div>
</template>
<script&gt;

```

`v-if`指令在其条件改变时触发转换，并处理应用程序的加载状态。例如，当从服务器加载数据时，我们可能会遇到这样的情况，即需要一些时间来获取数据。

基于源数据多次呈现元素或模板块，通过在我们的 API 中循环数据数组来显示我们的帖子。应用配置后，我们的应用程序将类似于下图:

![Headless WordPress Configurations Applied](img/9ad9b0604ed22b0ddb3cd6697007a1f6.png)

## 结论

Headless WordPress 是一个开箱即用的功能，它解决了大多数开发者和网站所有者面临的与内容管理系统相关的问题。在本文中，我们探索了使用 WordPress 和 Vue 构建、管理和交付内容的无头集成的可能性。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。