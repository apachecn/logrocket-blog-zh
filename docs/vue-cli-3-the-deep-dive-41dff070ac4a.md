# Vue CLI 3 —深潜日志火箭博客

> 原文：<https://blog.logrocket.com/vue-cli-3-the-deep-dive-41dff070ac4a/>

![](img/02b86c88ff4285562e7c7168342f804b.png)

Let’s face it — you’re probably not here for the image

Vue CLI 是一个简单但功能强大的工具，用于搭建 Vue.js 项目。

它可以确保各种构建工具与合理的默认设置一起顺利工作，因此您可以专注于编写您的应用程序，而不是花时间与配置斗争

Vue CLI 3 大概是目前 Vue 生态系统中发生的最好的事情，原因如下:

#### 1.基于插件的架构

新的 CLI 实现了我们所说的基于插件的架构，这是对使用基于模板的架构的早期版本的重大改进。

这意味着，现在只有一个模板，你需要的其他功能都将作为插件添加进来。

#### 2.灵活性

新的 CLI 使得启动一个新项目变得非常容易，而不必考虑将来可能需要的所有功能。

你可以创建项目，直接进入编码，当需要一个特性时，就添加它！

#### **3。零配置快速成型**

这允许你像服务一个 html 文件一样服务你的 vue 应用或组件。

#### 4.武契特

新的 CLI 允许您将 vuex 添加到您的项目中，就像您在以前的版本中添加 vue 路由器一样。

#### **5。打字稿**

现在有官方的 CLI 支持引导 Typescript Vue.js 应用程序

#### 6.自定义插件

如果所有好的官方 CLI 插件还不够，还有创建你自己的自定义插件的支持(当然，发布你的插件，让其他人可以使用它们。

### 安装 Vue CLI 3

要安装 CLI，请运行以下代码。

```
npm install -g @vue/cli
```

这将安装最新版本的 Vue CLI。完成后，您可以通过运行以下命令来确认您安装的版本:

```
vue -V
```

### CLI 命令中的更改

与之前的版本相比，CLI 命令有了一点小小的变化，新的命令被添加到了这个包中。

创建一个名为 myproject 的新项目。

`vue serve <filename>`发球*。js* 或者*。零配置开发模式下的 vue* 文件。

`vue build <filename>`从*构建生产就绪包。js* 或者*。零配置的 vue* 文件。

调用已安装的插件生成器来创建插件工作所需的文件。

`vue inspect`显示应用程序的 webpack 配置，因为它已被完全抽象化。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`vue init`是为仍然喜欢使用以前的遗留版本 2 的用户保留的，但是要使用这个命令，您必须安装一个全局桥

为此，请在您的终端中运行下面的代码。

```
npm install -g @vue/cli-init
```

一旦安装完成。你现在可以随时在版本 3 中直接使用版本 2。

```
vue init webpack newapp
```

### 创建新项目

要创建一个新项目，请在您的终端中运行下面的代码

```
vue create projectname
```

其中**项目名称**是您想要创建的应用程序的名称。

系统将提示您选择预设，默认预设(包含 babel & eslint 配置)或手动选择您想要的功能。

如果您选择默认预设，CLI 将创建您的项目并安装必要的插件来启动和运行它。

如果您选择手动选择特性，CLI 将继续向您显示所有现成的受支持插件，并要求您选择要添加到项目中的插件。

要选择，使用键盘上的**空格键**或键 **A** 来选择所有插件。

完成后，点击**回车**键继续。

根据所选择的插件，将会显示额外的提示，只需选择您需要的插件，然后按 enter 键，让 CLI 完成它的工作。

新 CLI 的一个很酷的地方是，创建的项目还会在您的机器上为您的项目自动创建一个新的 repo。

### 支持开箱即用的插件

1.  打字稿`@vue/typescript`
2.  渐进式网络应用程序(PWA) `@vue/pwa`
3.  路由器视图
4.  武契特
5.  Css 预处理程序(postcss，css 模块，sass，less & stylus)
6.  过磅器/格式化器`@vue/eslint`
7.  单元测试`@vue/mocha`或`@vue/jest`
8.  E2E 测试`@vue/cypress`或`@vue/nightwatch`

### **预设**

在使用 CLI 创建新项目并手动选择功能时，会创建一个预设。

CLI 使用创建的预设来创建项目文件。

它是用 JSON 编写的，包含了你创建新应用时选择的所有特性。

预设可以重复使用，因此在创建未来的应用程序时，您可以轻松地直接进入应用程序，而无需经历整个功能选择过程。

要使用 CLI 从以前保存的预设创建新项目，请将目录更改为预设的位置，并运行以下代码

```
vue create -p presetname newproject
```

这使用指定的预置文件创建一个名为 *newproject 的项目。*

### 向现有项目添加 CLI 插件

要向已经创建的项目添加插件，只需运行下面的命令

```
vue add @vue/pwa
```

其中`@vue/pwa`是您想要添加的插件的名称，在本例中是 Progressive Web App 插件。

需要注意的一点是，新的 CLI 现在接受包的简写名称，例如`@vue/pwa`是 CLI 包`@vue/cli-plugin-pwa`的简写。

CLI 将名称`@vue/value`解析为`@vue/cli-plugin-value`以安装软件包。

### 零配置快速成型

新的命令行界面使得服务*变得超级简单。vue* 或*。使用`vue serve`和`vue build`命令在开发或生产模式下创建 js* 文件。

这非常适合于您只想测试您的想法，并且您真的不想为配置而烦恼的情况。

要使用此命令，您必须首先安装 vue CLI 全局服务。

为此，只需在您的终端中运行下面的代码。

```
npm install -g @vue/cli-service-global
```

安装完成后，您可以使用`vue serve`或`vue build`。

**vista 服务**

```
vue serve app.vue
```

其中 *app.vue* 是您想要提供的组件或文件的名称。

`serve`命令还提供了一个选项，一旦它运行完命令就启动你的浏览器，而不是只向你显示它当前为应用服务的 url。

为此，运行`vue serve -o app.vue`

视图构建

```
vue build app.vue
```

其中 *app.vue* 是您想要提供的组件或文件的名称。

`vue build`命令构建一个生产就绪包，它还允许您指定是将其构建为库还是 web 组件。

构建为库，`vue build -t lib app.vue`而构建为 web 组件，`vue build -t wc app.vue`。

### **环境变量**

新的 CLI 现在允许您使用 ***来使用环境变量。env*** 文件在你的项目的根目录下。

该文件由 ***键=值*** 对组成。

每个人都需要管理至少两个环境，这意味着我们需要为这个环境指定变量。

为了有效地做到这一点，CLI 引入了我们所说的**模式。**

模式只是环境的另一个名称，它指定您是处于开发、生产还是测试模式。

创建仅由特定模式使用的变量。您必须将模式名称作为后缀添加到 ***。env*** 文件名。

`.env.development`为开发模式& `.env.production`为生产模式。

**注意:**对于将变量嵌入到客户端捆绑代码中的 CLI，变量需要以`VUE_APP_`名称为前缀。

添加变量`seckey`现在变成了`VUE_APP_SECKEY`

#### 调整 Webpack 配置

Vue CLI 提供了一种非常简单灵活的方式来调整内部 webpack 配置。

为此，您必须使用`vue.config.js`中的`configureWebpack`选项

```
// vue.config.js
module.exports = {
  configureWebpack: {
    plugins: [
      new MyAwesomeWebpackPlugin()
    ]
  }
}
```

以防您的项目根目录中还没有 *vue.config.js* 文件。您必须手动创建它。

您可能想知道:“我如何知道内部 Webpack 配置中已经有什么，这样我就知道我需要添加什么？”

这就是`vue inspect`命令变得方便的地方。

该命令将所有内部 Webpack 配置输出到您的终端。

要将其输出到文件中，只需指定一个文件名，如下所示

```
vue inspect > output.js
```

### 摘要

在撰写本文时，新的 CLI 仍处于测试阶段，因此可能会有一些错误。事实上，建议不要在生产中使用，除非你喜欢冒险。

干杯！

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。