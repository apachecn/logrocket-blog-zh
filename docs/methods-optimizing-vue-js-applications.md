# 优化 Vue.js 应用程序的 5 种方法

> 原文：<https://blog.logrocket.com/methods-optimizing-vue-js-applications/>

性能是软件工程中一个敏感的话题。写代码和发货是一回事；让你的用户和你之后的开发者更快更愉快是另一回事。

在本文中，我们将研究如何加速您的 Vue.js web 应用程序，从而改善用户体验和开发人员体验。我们开始吧，好吗？

## 1.延迟加载路由组件

软件工程中的术语“延迟加载”指的是将对象或应用程序的一部分的初始化推迟到需要时进行的模式。

通常，我们的 Vue.js 应用程序被捆绑到一个相对较大的 JavaScript 文件中，但是使用这种技术，每个组件被编译成较小的文件块，每个块对应一个路由组件。因此，我们的应用程序在浏览器中加载速度更快，只有当用户访问新路线时才会请求新的块。

如果你已经熟悉了 Vue 中的延迟加载组件，请记住，这种方法并不用于延迟加载我们代码库中的每个组件，只是那些我们注册到 Vue Router 的组件。注册在`router`组件中的所有其他组件都是这个包的一部分。

您可以浏览本文来了解更多关于延迟加载这些组件的信息。

那么这是怎么做到的呢？让我们来看看:

```
// in router.js file
import Vue from 'vue'
import Router from 'vue-router'

const Home = () => import('./routes/Home.vue');
const Profile = () => import('./routes/Profile.vue');

Vue.use(Router)

export default new Router({
  routes: [
    { path: '/', component: Home },
    { path: '/profile', component: Profile }
  ]
})
```

当我们以这种方式注册我们的组件时，webpack 会自动将我们的代码分割成块。也可以将这些文件分组到一个块中。一个很好的场景是将路由相关的组件分组到一个块中。

```
// specifying the same chunk for components
const Profile = () => import(/* webpackChunkName: "profile" */'./routes/Profile.vue');
const ProfileSettings = () => import(/* webpackChunkName: "profile" */'./routes/ProfileSettings.vue');
```

通过使用注释明确指定块名，webpack 自动将编译后的组件捆绑到同一个块中。

## 2.尽量减少外部库的使用

有了 JavaScript 背后的强大支持，以及 npm 上每天发布和更新的大量包，很有可能在您的 web 应用程序中有您需要的任何包。这很好——但是我们必须小心，不要用不必要的代码来增加应用程序的大小。几乎总有一种更轻的包装替代品。

一个好的做法是，在寻找包之前，检查您想要实现的特性在浏览器中是否是本地可用的。如果原生特性跨浏览器不可靠或者难以实现，那么包可能是最好的选择。

当搜索一个包时，一定要在你找到的各种包中权衡你的选择。以模块化模式构建并支持树抖动的包最适合您的项目。

## 3.压缩和优化图像

图片无疑是影响应用捆绑包大小的最大因素。在渲染过程中，厚重的图像实际上会阻碍应用程序的某些部分快速渲染。

这完全取决于你如何提供你的图像；这两种技术是本地的或在 CDNs 的帮助下。使用 CDN 传送图像将取决于应用程序中要显示的图像数量。

### 优化本地图像

如果您的应用程序只有三到五个图像要显示，那么在本地提供它们是可以的。然而，必须考虑这些文件的大小，并且为了减小文件大小，可能必须压缩和缩小图像。像 Adobe Photoshop 这样的工具可以创造奇迹，但也有免费的在线图像压缩工具:

1.  [图像压缩器](https://imagecompressor.com/)
2.  [TinyPNG](http://tinypng.com)
3.  [公里长](https://www.iloveimg.com/)

### 优化 CDN 图像

优化 CDN 上的图像是优化媒体密集型应用程序的一个好方法。通过 CDN 提供的转换功能，图像的文件大小可以减少 70 %,并且在网站上看起来仍然很好。

如果应用程序中使用的图像总数大约为 10 个或更多，使用 CDN 可能是处理这些文件的最佳方式。以下是一些流行的媒体管理平台:

1.  [云淡风轻](https://cloudinary.com)
2.  [图像套件](https://imagekit.io)

## 4.lazyload 图像

在某些情况下，仅仅压缩网络上的媒体文件是不够的，尤其是在处理媒体密集型网站的时候。这就是延迟加载再次派上用场的地方。在初始页面加载时，仅请求在视口中可见的媒体文件；其余的在用户浏览应用程序时被请求。

对于延迟加载图像，我们可以使用 [vue-lazyload](http://npmjs.com/package/vue-lazyload) 包。这是一个轻量级的、易于使用的库，具有很大的灵活性。

安装:

```
npm i vue-lazyload
```

设置:

```
// in main.js

import Vue from 'vue'
import VueLazyload from 'vue-lazyload'

Vue.use(VueLazyload)
```

使用`v-for`:

```
<ul>
  <li v-for="image in images">
    <img v-lazy="image.src" >
  </li>
</ul>
```

使用原始 HTML:

```
<section v-lazy-container="{ selector: 'img' }">
  <img data-src="//images.com/cat1.jpg">
  <img data-src="//images.com/cat2.jpg">
  <img data-src="//images.com/cat3.png">  
</section>
```

现在，只有当图像进入视窗时才会被请求。如果你正在做一个 [Nuxt](https://nuxtjs.org/) 项目，在`/plugins`目录下创建一个包含`main.js`(如上)内容的文件`vue-lazyload.js`。

像这样在`nuxt.config.js`文件中注册插件:

```
export default {
  plugins: [
    { src: '~/plugins/vue-lazyload.js', mode: 'client' }
  ]
}
```

在[官方文档](https://github.com/hilongjw/vue-lazyload#readme)中还有其他灵活的选项。

## 5.在您的应用中重用功能

当使用外部库时，很容易忘记可重用性。在这一节中，我们将把 [Vue 通知](https://github.com/se-panfilov/vue-notifications)库作为一个例子。

Vue 通知是你的 Vue app 和各种通知库之间的桥梁。它也使得在通知库之间切换变得容易。让我们根据文档来看看如何使用这个库。

```
// in main.js
import Vue from 'vue'
import VueNotifications from 'vue-notifications'
import VueToasted from 'vue-toasted' // https://github.com/shakee93/vue-toasted

function toast({ title, message, type, timeout, cb }) {
  if (type === VueNotifications.types.warn) type = 'show'
  return Vue.toasted\[type\](message, { duration: timeout })
}

Vue.use(VueToasted)

const options = {
  success: toast,
  error: toast,
  info: toast,
  warn: toast
}
Vue.use(VueNotifications, options)
```

在我们组件的`script`部分:

```
import VueNotifications from 'vue-notifications'

export default {
  name: 'Register',
  data () {
    return {
      email: '',
      password: ''
    }
  },
  notifications: {
    showSuccess: {
      type: VueNotifications.types.success,
      title: 'Success!',
      message: 'Account created successfully'
    },
    showError: {
      type: VueNotifications.types.error,
      title: 'Oops!',
      message: "Something's wrong"
    }
  }
  methods: {
    async login(){
      try {
        await sendUserData() // or any other way to send credentials
        this.showSuccess()
      } catch (error){
        // after checking error type
        this.showError({message: error.data.message})
      }
    }
  }
}
```

注意我们是如何将`VueNotifications`包导入到组件中并添加一个`notifications`选项的？如果我们只在一个组件中使用这个包，那会很好，但是我怀疑对于任何大型应用程序来说都是这样。

与其手动将`VueNotifications`导入到组件中并在 10 多个不同的组件中设置自定义选项，不如我们创建一个通用的 mixin 来为用户显示任何通知消息？

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

首先，我们创建文件`/mixins/notifications.js`:

```
import VueNotifications from 'vue-notifications'
export default {
  notifications: {
    showError: {
      title: 'Oops!',
      message:
        "Something's wrong with, please check your internet and try again.",
      type: VueNotifications.types.error
    },
    showSuccess: {
      type: VueNotifications.types.success,
      title: 'Success!',
      message: 'Action successful'
    }
  }
}
```

现在我们可以只导入组件中的 mixin:

```
import notifications from '@/mixins/notifications'

export default {
  name: 'Register',
  mixins: [notifications],
  data () {
    return {
      email: '',
      password: ''
    }
  },
  methods: {
    async login(){
      try {
        await sendUserData() // or any other way to send credentials
        this.showSuccess({ message: 'Account created successfully' })
      } catch (error){
        // after checking error type
        this.showError({ message: error.data.message })
      }
    }
  }
}
```

现在我们的组件不那么杂乱，更容易阅读。通过使用 mixins，我们只导入对我们需要的包的特定部分的引用。

## 结论

虽然还有更多方法可以优化我们的 Vue 应用程序，但上面列出的五种技术是最常用的。

对于那些浏览过这篇文章的人来说，下面是上面强调的几点:

*   延迟加载您的路由组件
*   尽量减少外部库的使用，如果必须使用，一定要权衡选择
*   在传送到网络之前压缩和优化图像
*   延迟加载您的图像，以在整个应用程序中提供流畅的用户体验
*   在你的应用中重用功能，以减少重复代码

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。