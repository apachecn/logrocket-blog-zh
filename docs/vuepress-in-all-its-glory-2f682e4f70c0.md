# VuePress 在其所有荣耀日志火箭博客

> 原文：<https://blog.logrocket.com/vuepress-in-all-its-glory-2f682e4f70c0/>

[VuePress](https://github.com/vuejs/vuepress) 是一款 Vue 供电的静态现场发电机，由两部分组成:

1.  主题系统
2.  为编写技术文档而优化的默认主题

Vue.js 的创始人尤雨溪构建了 VuePress 来满足各种基于 Vue 的项目的文档需求。

VuePress 真正酷的一点是，它允许你从一个 Markdown 文件中构建一个单页应用程序，该应用程序有自己的预渲染静态 HTML，该文件由 [Vue](https://github.com/vuejs/vue) 、 [VueRouter](https://github.com/vuejs/vue-router) 和 [Webpack](https://webpack.js.org/) 提供支持。

## 为什么选择 VuePress？

使用 VuePress 有几个原因，但最突出的是:

### 1.降价文件中的 Vue

VuePress 允许你在 Markdown 文件中写 Vue。它能够做到这一点是因为它将 Markdown 文件编译成 HTML，并在 Vue 组件中将它们作为模板进行处理。

### 2.内置降价扩展

VuePress 支持一些非常棒的扩展，允许你将 Markdown 中的书写提升到一个新的水平。举个例子，

*   `YAML front matter`:在您的 Markdown 文件中设置预定义变量或创建自定义变量，并可在任何文件中使用`$page`变量进行访问。
*   `Header Anchors`:自动将标题转换为锚定标签
*   `Links`:自动将 Markdown 中的链接转换为 VueRouter 的`<router-link>`用于 SPA 导航
*   `Emoji`:在 Markdown 中写表情符号
*   `Line Highlighting in Code Blocks`:高亮显示 Markdown 代码块中的一行代码
*   `Table of Contents`:告诉处理器选取 Markdown 文件中的所有标题元素，并将其呈现为带有链接的项目列表。要做到这一点，你必须在 Markdown 文件

    ```
    [[toc]]
    ```

    中你希望它呈现的地方写下标签
*   `[GitHub-Style Tables](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#tables)`:在 Markdown 中绘制表格。

### 3.默认主题

VuePress 带有一个完全响应的主题，已经嵌入了一些小功能，如基于标题的搜索，可定制的导航栏和侧边栏，可选的主页，自动生成的 GitHub 链接，以及页面**编辑链接**。

### 4.自定义主题

VuePress 使定制默认主题以及创建完全自定义的主题变得容易。

### 5.服务人员

VuePress 在配置中有一个选项，允许它自动生成并注册一个服务工作者，该服务工作者将缓存内容以供离线使用。

### 6.谷歌分析

VuePress 还集成了谷歌分析功能。要启用它，您必须在配置中添加您的 Google Analytics ID。

### 7.多语言支持

这个有点不言自明，但是留下一个空位感觉怪怪的。

## 入门指南

开始使用 VuePress 有两种方式:

1.  全球安装
2.  将其添加到现有项目中

要全局安装:

```
npm install -g vuepress
```

要添加到现有项目:

```
npm install -D vuepress
```

安装完成后，我们创建 Markdown 文件，它将成为静态站点的内容，然后运行下面的代码来预览它。

```
vuepress dev
```

VuePress 设置已经启用了开箱即用的热重新加载功能，因此当您对 Markdown 文件进行更改时，它会在您的浏览器中自动更新。

在浏览器中查看预览，你会注意到它非常单调，几乎没有提到的功能存在。

启用其中一些功能(主页、侧边栏、导航等)。)，我们需要创建一个配置文件。

## 配置文件

这个文件基本上是一个 JavaScript 文件，允许我们定制/启用或禁用项目中的特性。

对于这个 Javascript 文件，它需要导出一个对象。

要创建这个文件，首先必须在项目中创建一个`.vuepress`文件夹，然后在其中创建一个空文件，并将其命名为`config.js`。

要激活标题和搜索功能，我们只需将`title`和`description`添加到导出的对象中。

```
module.exports = {
  title: 'Hello VuePress World',
  description: 'Just playing around... Skr Skr!'
}
```

配置选项分为五个部分:

### 1.基本配置

这包括启动和运行所需的最少配置，包括:

```
module.exports = {
  dest: 'docs',
  title: 'VuePress',
  description: 'Just playing around.. Skr! Skr!',
  head: [
    ['link', { rel: 'icon', href: `/logo.png` }],
    ['link', { rel: 'manifest', href: '/manifest.json' }],
    ['meta', { name: 'theme-color', content: '#3eaf7c' }],
    ['meta', { name: 'apple-mobile-web-app-capable', content: 'yes' }],
    ['meta', { name: 'apple-mobile-web-app-status-bar-style', content: 'black' }],
    ['link', { rel: 'apple-touch-icon', href: `/icons/apple-touch-icon-152x152.png` }],
    ['link', { rel: 'mask-icon', href: '/icons/safari-pinned-tab.svg', color: '#3eaf7c' }],
    ['meta', { name: 'msapplication-TileImage', content: '/icons/msapplication-icon-144x144.png' }],
    ['meta', { name: 'msapplication-TileColor', content: '#000000' }]
  ],
  serviceWorker: true,
  ga: 'UA-109510157-1',
  port: 8000
}
```

### 2.主题化配置

这包括所有与站点中使用的主题相关的配置。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

*   `theme`:传递您喜欢使用的自定义主题的名称
*   `themeConfig`:为特定的选定主题指定所有可能的主题配置

### 3.降价配置

这允许您根据自己的需要定制 Markdown 扩展。它们包括:

*   `markdown.slugify`:让 VuePress 将标题文本转换成 slugs
*   `markdown.anchor`:处理器将把标题标签渲染成链接
*   `markdown.toc`:决定处理器是否应该渲染`[[toc]]`
*   `markdown.config`:添加 VuePress Markdown 系统中没有的附加插件

### 4.构建配置

这允许您指定如何构建生产就绪文件。它包括:

*   `postcss`:选择 PostCSS 加载器的选项
*   `configureWebpack`或`chainWebpack`:修改 VuePress 中的内部 webpack 配置

### 5.浏览器兼容性配置

这提供了让 VuePress 构建来禁用一些向后兼容特性的选项，如 ES5 transpilation、IE 的 polyfills 等。

以上可以用[常青](https://vuepress.vuejs.org/config/#evergreen)来做。

## 将默认主题部分耦合在一起

默认主题由一个主页组成，该主页包含以下内容:

1.  英雄形象
2.  动作按钮
3.  由三列组成的部分，每一列都有标题和题注
4.  页脚

默认主题已经在主题的 YAML 前台创建了一些预设变量，允许我们设置将在网站上使用的实际数据。

要设置这些值，您需要在项目的根目录下创建一个`readme.md`文件，并在下面的元数据中指定正确的值:

```
---
home: true
heroImage: /hero.png
actionText: Get Started →
actionLink: /guide/
features:
- title: Simplicity First
  details: Minimal setup with markdown-centered project structure helps you focus on writing.
- title: Vue-Powered
  details: Enjoy the dev experience of Vue + webpack, use Vue components in markdown, and develop custom themes with Vue.
- title: Performant
  details: VuePress generates pre-rendered static HTML for each page, and runs as an SPA once a page is loaded.
footer: MIT Licensed | Copyright © 2018-present Evan You
---

# Hello VuePress World!
```

### 解释上面的代码

*   `home`:关闭或显示主页视图
*   `heroImage`:指定主页上英雄形象的链接
*   `actionText`:指定主页上动作按钮的文字
*   `actionLink`:允许您指定点击动作按钮时导航到的链接
*   `Features`:将内容指定到默认主题的所有 3 列中
*   `footer`:允许您指定页面页脚的内容

### 导航栏链接

为了启用页面标题上的导航链接，默认主题在`config.js`中提供了一个`themeConfig.nav`选项，允许您设置各种导航链接。

```
module.exports = {
  themeConfig: {
    nav: [
      // Normal Links
      { text: 'Guide', link: '/guide/' },
      { text: 'External', link: 'https://google.com' },

      // Links with dropdown
      {
        text: 'Languages',
        items: [
          { text: 'Chinese', link: '/language/chinese' },
          { text: 'Japanese', link: '/language/japanese' }
        ]
      },

      // Grouped Items inside dropdown
      {
        text: 'Languages',
        items: [
          { 
            text: 'Group1', 
            items: [
              { text: 'Chinese', link: '/language/chinese' },
              { text: 'Japanese', link: '/language/japanese' }
            ] 
          },
        ]
      }
    ]
  }
}
```

### 侧栏布局

为了启用侧边栏布局，默认主题提供了一个`themeConfig.sidebar`选项，允许我们指定侧边栏中的各种链接。

侧边栏还有一个链接分组功能，你可以指定它是否可以折叠。

要使侧边栏项目可见，被引用的文件**必须存在**。

```
---
home: true
heroImage: /hero.png
actionText: Get Started →
actionLink: /guide/
features:
- title: Simplicity First
  details: Minimal setup with markdown-centered project structure helps you focus on writing.
- title: Vue-Powered
  details: Enjoy the dev experience of Vue + webpack, use Vue components in markdown, and develop custom themes with Vue.
- title: Performant
  details: VuePress generates pre-rendered static HTML for each page, and runs as an SPA once a page is loaded.
footer: MIT Licensed | Copyright © 2018-present Evan You
---

# Hello VuePress World!
```

**注意:**侧边栏只适用于没有将`home`选项设置为`true`的 Markdown 文件。

### 切换颜色

默认的主题样式表是用名为 stylus 的预处理器构建的，它允许我们在样式表中定义变量。

这使得我们很容易改变一些预设的颜色和样式得到应用。

要覆盖某些样式，您必须在`.vuepress`文件夹中创建一个`override.styl`文件，并指定值的变化。

您当前可以覆盖的变量列表:

```
// colors
$accentColor = #3eaf7c
$textColor = #2c3e50
$borderColor = #eaecef
$codeBgColor = #282c34
$arrowBgColor = #ccc

// layout
$navbarHeight = 3.6rem
$sidebarWidth = 20rem
$contentWidth = 740px

// responsive breakpoints
$MQNarrow = 959px
$MQMobile = 719px
$MQMobileNarrow = 419px
```

## 部署

部署 VuePress 网站非常容易，取决于您选择的平台。

首先，我们必须建立一个生产就绪的网站。

为此，我们将终端的目录更改为项目的根目录，然后运行下面的代码

```
vuepress build
```

这将我们的 Markdown 编译成 Vue 组件，然后由 VuePress build setup 处理，生成一个完全预渲染的单页应用程序，它在`.vuepress/dist`目录中既快速又 SEO 友好，除非在`config.js`文件中另有说明。

现在，您可以像部署任何其他静态网站一样，部署您的生产就绪站点。

觉得这篇文章有帮助？别忘了鼓掌和分享🙂

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。