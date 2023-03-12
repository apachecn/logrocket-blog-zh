# 用 webpack 的模块联盟构建微前端

> 原文：<https://blog.logrocket.com/building-micro-frontends-webpacks-module-federation/>

好的框架已经存在，可以帮助我们创建微前端，比如 single-spa 和 OpenComponents。但是如果我们不想在我们的架构中集成一个不同的框架呢？让我们看看如何在我们的 Vue 应用中实现微前端。

在本文中，我们将讨论如何通过使用 [webpack 5 的模块联盟](https://webpack.js.org/concepts/module-federation/)来使用和共享微前端组件，并提供示例代码，从而显著简化我们的应用架构。

## 什么是模块联合？

模块联盟是由扎克·杰克逊发明的 JavaScript 架构。这种架构允许在两个不同的应用程序代码库之间共享代码和依赖关系。

代码是动态加载的，如果缺少依赖项，宿主应用程序将下载该依赖项，从而减少应用程序中的代码重复。

## 什么是微前端？

微前端的概念近来越来越受欢迎。对微服务的推动也以微前端的形式给现代 web 带来了同样的实现。随着 monolith 应用程序的扩展，维护变得越来越困难，尤其是在几个团队使用同一个应用程序的时候。

我们可以将微前端视为基于功能的，其中有不同的团队，每个团队处理特定的功能组件，而另一个团队处理其他的东西。最后，所有团队将他们构建的不同组件合并成一个应用程序。

开发人员利用像 [single-spa](https://blog.logrocket.com/micro-frontend-apps-single-spa/) 和 [OpenComponents](https://opencomponents.github.io) 这样的框架来实现这一点，但是随着 webpack 5 和模块联盟的新发布，我们可以很容易地实现相同的目标，但是更容易。

## 微前端的优势

采用微前端方法来构建 web 应用程序可能是最好的策略。如果您正在构建一个大规模的 web 应用程序，其中有许多活动的部分或分支为子应用程序的应用程序，并且您希望整体外观保持一致，那么这一点尤其正确。

让我来强调一下您可能希望转向微前端方法的几个原因:

*   采用微前端方法将允许我们创建端到端的功能架构。这种方法将允许我们在本地开发和部署特性，而不需要大规模的部署基础设施
*   通过更小和更优化的包大小，微前端提供了总体上更好的开发人员和用户体验，因为共享的组件和依赖关系可以在我们需要的任何时候[延迟加载](https://blog.logrocket.com/vue-lazy-loading-components-code-splitting/#whatislazyloading)
*   对我来说，最大的优势之一是开发特定产品的开发团队能够选择自己喜欢的技术堆栈，而不用担心与其他团队的代码不兼容

## 我们如何分割我们的应用程序？

以下是开发人员拆分大型应用程序的一些方法:

1.  通过页面⁠—在我们的前端应用程序中，有时在浏览器中同时运行不同的页面会导致旧设备崩溃，所以最安全的方法是按页面分割。如果你有好的路径，你可以为每个页面运行单独的、特定的微应用程序，这对你团队中的开发人员也有好处，因为他们总是在分配给他们的那个页面上工作
2.  按功能⁠—如果你有一个页面，其中有多个功能执行不同的操作，你可以将这些大功能拆分成较小的应用程序，使其成为运行特定功能的独立应用程序
3.  按节⁠—你也可以按节分割你的应用程序，不同的应用程序共享同一个节或组件

## 概念证明

我们已经解释了一些关于微前端和模块联合的概念。现在是验证概念的时候了。

在这里，我们将演示如何使用模块联合在 Vue 中创建微前端。为了测试这一点，我们将旋转两个不同的应用程序，所以我们可以在其中一个中创建一个微前端，并与另一个共享。

首先，我们创建一个文件夹来托管两个 Vue 应用程序:

```
mkdir vue-mf 

```

我们将在`vue-mf`文件夹中运行我们的 Vue 应用程序。我们不会在这里使用 Vue CLI。相反，我们将使用 webpack 的新版本 webpack 5 来设置 Vue 应用程序。

我们将我们想要共享组件的两个应用程序分别命名为`Company`和`Shop`。我们在`vue-mf`文件夹中为他们每个人创建一个文件夹，然后从 GitHub 中抓取一个由 [Jherr](https://github.com/jherr/wp5-starter-vue-3) 创建的 Vue 的 webpack 启动文件到每个文件夹中:

```
git clone https://github.com/jherr/wp5-starter-vue-3.git

```

现在我们已经设置了应用程序，让我们来看看文件结构:

```
+-- vue-mf/
|   +-- Company/
|   +-- Shops/

```

当我们打开其中一个应用文件夹时，结构如下:

```
+-- vue-mf/
|   +-- Company/
|       +-- src/
|           +-- App.vue
|           +-- bootloader.js
|           +-- index.css
|           +-- index.html
|           +-- index.js
|       +-- package.json
|       +-- webpack.config.js
|   +-- Shops/

```

## 设置 webpack 配置

所以我们有两个 app，`Company`和`Shop`，目前来看完全一样。当我们调查文件结构时，我们看一看`package.json`。我们有自己的 webpack 加载器、CSS 加载器以及我们需要的所有基本加载器和 webpack 工具:

```
const ModuleFederationPlugin = require("webpack/lib/container/ModuleFederationPlugin");
const { VueLoaderPlugin } = require("vue-loader");
module.exports = {
  output: {
    publicPath: "http://localhost:8080/",
  },
  resolve: {
    extensions: [".vue", ".jsx", ".js", ".json"],
  },
  devServer: {
    port: 8080,
  },
  module: {
    rules: [
      {
        test: /.vue$/,
        loader: "vue-loader",
      },
      {
        test: /.css$/i,
        use: ["style-loader", "css-loader"],
      },
      {
        test: /.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
        },
      },
    ],
  },
  plugins: [
    new VueLoaderPlugin(),
    new ModuleFederationPlugin({
      name: "starter",
      filename: "remoteEntry.js",
      remotes: {},
      exposes: {},
      shared: require("./package.json").dependencies,
    }),
    new HtmlWebPackPlugin({
      template: "./src/index.html",
    }),
  ],
};

```

如果我们看一下`webpack.config.js`文件，我们可以看到我们的公共路径被设置为端口`8080`。我们还可以看到 webpack 检查我们的文件扩展名并使用适当的加载器。

这里需要注意的重要一点是我们在解析文件时使用的 Vue loader 插件，以及我们从 webpack 5 导入并使用的模块联合插件，这将允许我们执行共享功能。我们将在本教程的后面回到`ModuleFederationPlugin`的配置。

*注意，确保将另一个应用程序(商店)中的公共路径和开发服务器端口设置为端口`8081`，以便我们能够同时运行这两个应用程序。*

## 创建要共享的组件

在我们的应用程序中，`App.vue`文件将作为主页，所以让我们添加一些标记:

```
<template>
 <div>
     <h1>Our Application Homepage</h1>
 </div>
</template>

```

header 组件是我们希望在应用程序之间共享的应用程序的一部分。假设一个开发团队决定构建头部，那么我们创建一个可以在两个应用程序中共享的`header`组件。

在我们公司应用程序的`src`文件夹中，我们将创建一个`header`组件。为此，我们创建了一个`Header.vue`文件，并在其中创建了`header`组件:

```
<template>
  <div>
      <header>
          App Header
      </header>
  </div>
</template>

```

创建标题后，导航到`App.vue`并导入`header`组件:

```
<template>
 <div>
     <Header />
     <h1>Our Application Homepage</h1>
 </div>
</template>
<script>
import Header from './Header.vue';
export default {
 components: {
     Header,
 },   
}
</script>

```

现在，我们可以通过导航到每个文件夹并运行以下命令来启动我们的开发服务器:

```
yarn && yarn start

```

现在，我们的应用程序在公司应用程序中是这样的。

![Sample App for a Vue Micro-Frontend](img/066dc128fcabcea1962680aefa6344c8.png)

## 通过模块联合插件公开`header`组件

我们现在在公司应用程序中有了标题，我们想在商店应用程序中使用它。因此，我们转向公司应用程序中的 webpack 配置:

```
 plugins: [
    new VueLoaderPlugin(),
    new ModuleFederationPlugin({
      name: "Company",
      filename: "remoteEntry.js",
      remotes: {},
      exposes: {
        "./Header": "./src/Header",
      },
      shared: require("./package.json").dependencies,
    }),
    new HtmlWebPackPlugin({
      template: "./src/index.html",
    }),
  ],

```

在 webpack 模块联合配置中，我们将`name`设置为应用程序名`Company`，将`remoteEntry.js`设置为我们的文件名。当我们导航到`remoteEntry.js`文件名时，我们会看到与我们想要共享的组件和依赖项相关的代码。我们还公开了`header`组件及其位置。

现在，如果我们重启服务器并导航到`[http://localhost:8080/remoteEntry.js](http://localhost:8080/remoteEntry.js)`，我们将会看到:

![remoteentry.js Example for Vue Micro-Frontend](img/01b574279540211982bb29d402e37b28.png)

现在获取远程入口 URL 并切换到我们商店应用程序中的 webpack 配置文件:

```
plugins: [
    new VueLoaderPlugin(),
    new ModuleFederationPlugin({
      name: "Shop",
      filename: "remoteEntry.js",
      remotes: {
        Company: "[email protected]://localhost:8080/remoteEntry.js"
      },
      exposes: {},
      shared: require("./package.json").dependencies,
    }),
    new HtmlWebPackPlugin({
      template: "./src/index.html",
    }),
  ],

```

这里，我们给插件命名为`Shop`，并将远程设置为`remoteEntry` URL。然后，在商店应用程序的`App.vue`文件中，我们从公司应用程序导入并使用`header`组件:

```
<template>
<div>
    <Header />
    <h2>Our Shop Page</h2>
</div>
</template>

<script>
import Header from 'Company/Header';
export default {
 components: {
     Header,
 },   
}
</script>

```

如果我们重新启动服务器，我们可以看到商店页面现在有了标题组件，这意味着我们已经成功地在两个应用程序之间共享了该组件。耶！

*注意，如果负责标题的团队决定为`header`组件推送新的更新，商店应用团队将在商店应用刷新后立即看到更新。*

## 在联合模块之间共享应用程序状态

假设您在 Vue 应用程序中使用了一个状态管理器，比如 Vuex。您可能会问自己如何拥有状态，如何在两个组件之间共享状态，以及如何更新状态。因此，让我们为这两个应用程序安装 Vuex:

```
yarn install [email protected]

```

一旦我们安装了 Vuex，导航到我们公司应用程序中的`bootloader.js`文件，在那里我们初始化我们的 Vue 应用程序。
在这里，我们导入我们的商店并创建一个状态:

```
import { createApp } from "vue";
import { createStore } from 'vuex'
import "./index.css";
import App from "./App.vue";
const app = createApp(App)
const store = createStore({
    state () {
      return {
        cartItems: 0
      }
    }
  })
app.use(store)
app.mount("#app");

```

例如，如果这是一个电子商务商店，我们想显示购物车中的商品数量，我们创建一个`cartItems`状态，并将其显示在公司标题中。然后转到我们的`header`组件，访问状态，并显示它:

```
<template>
  <div>
      <header>
          <h2> App Header</h2>
          <p>items: {{cartCount}}</p>
      </header>
  </div>
</template>
<script>
export default {
    computed: {
        cartCount() {
            return this.$store.state.cartItems
        }
    },
}
</script>f

```

我们已经成功设置了状态，但问题是，如果我们为两个应用程序启动服务器并检查公司应用程序，我们可以看到标题显示状态:

![Header with State for Vue Micro-Frontend](img/6652c830dae0cd524e564c3b0f7c98f5.png)

但是如果我们导航到商店应用程序，我们再也看不到共享的`header`组件，更不用说我们添加的状态了。而是得到一个错误信息，说我们无法读取未定义的状态，因为在我们的商店 app 中，我们还没有设置任何商店。

为了纠正这个问题，我们将公司应用程序的 bootloader 中的所有代码复制并粘贴到商店应用程序的`bootloader.js`文件中。这一次，我们将`cartCount`状态改为`12`。如果我们重新启动服务器，我们现在在商店应用程序中有了我们的标题，购物车商品为`12`。

假设我们想要模仿更多商店商品添加到购物车，那么在商店应用程序中，我们添加一个按钮来增加`cartCount`状态:

```
<template>
<div>
    <Header />
    <h2>Our Shop Page</h2>
    <div>
        <button @click="addItem">Add item</button>
    </div>
</div>
</template>

<script>
import Header from 'Company/Header';
export default {
 components: {
     Header,
 }, 
 methods: {
         addItem() {
             this.$store.state.cartItems += 1
         }
     },  
}
</script>

```

如果我们重新启动商店应用程序，我们可以看到标题中的项目现在更新了。耶！

## 资源

## 结论

我们已经到了本教程的结尾。

在这里，我们讨论了如何通过使用 [webpack 5](https://blog.logrocket.com/changes-coming-to-webpack-in-2021/) 的模块联合来使用和共享微前端组件和示例代码，从而极大地简化我们的应用架构。

您是否应该采用微前端取决于您正在构建的项目的类型，因为这种方法对于小型应用程序或企业来说不是最好的。当与分布式团队一起处理大型项目时，微前端架构方法是您的最佳选择。