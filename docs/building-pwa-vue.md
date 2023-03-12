# 使用 Vue.js 构建 PWA-log rocket 博客

> 原文：<https://blog.logrocket.com/building-pwa-vue/>

不久前，我在 CodePen 中构建了一个小的[液体单位转换应用程序，可以在盎司、茶匙、杯等之间进行转换。我不认为有人会在现实生活中真正使用它——它主要只是为了娱乐和用](https://codepen.io/collinsworth/pen/bGdmmrY) [Vue](https://vuejs.org/) 学习:

参见 [CodePen](https://codepen.io) 上 Josh Collins worth([@ Collins worth](https://codepen.io/collinsworth))
在 Vue 中的 Pen [动态液体测量表。](https://codepen.io/collinsworth/pen/bGdmmrY)

但是，令人惊讶的是，我发现这个应用程序非常有用。在过去几年里，当我开始在家烹饪和调制鸡尾酒时(你知道为什么)，我发现自己反复使用那个方便的液体转换器应用程序。

不过，只有一个问题:在我的手机上访问这个应用程序是一件痛苦的事情。当然，我可以把它收藏起来，但是那种体验并不好，而且看起来也不比谷歌搜索我需要的东西好多少。当然，有很多很好的替代方案，但是它们没有使用我自己构建的东西有趣。

我发现自己希望我的 CodePen 应用程序是一个真正存在于我手机上的应用程序，我可以像点击 Twitter 或 Slack 一样轻松地打开它。

好消息是:这是非常可行的，[多亏了进步的网络应用](https://blog.logrocket.com/why-you-should-turn-your-app-into-a-pwa/)！

## 什么是渐进式网络应用？

详细定义渐进式网络应用(PWA)这个术语对于[自己的帖子](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Introduction)(甚至[自己的书](https://abookapart.com/products/progressive-web-apps))来说已经足够了，但简单地触及重点:渐进式网络应用是一个网站，其功能使其看起来、感觉起来和行为起来像一个本地应用。

本质上，它是一个增强的网站！怎么会？与传统网站不同，您可以在设备上安装 PWA。你甚至可以在用户访问你的网站时提示他们安装你的 PWA，如我的文字游戏应用 [Quina](https://quina.app/) 的截图所示，它也是一个 PWA(也是用 [Vue](https://vuejs.org/) 构建的):

![Quina App](img/9290a318f08c7546337b720aa5b7689a.png)

You can prompt a user to install your PWA as soon as they visit the URL.

一旦安装了 PWA，你就可以从 dock 或主屏幕上用图标打开它，就像一个原生应用程序一样。

![Quina Icon](img/dddf3bb711b9824e56be9c79d01af498.png)

Once installed, a PWA will show up alongside all the rest of your apps.

一旦打开，PWA 就成为一个独立的全屏体验，而不是像本机应用程序一样在设备的浏览器中打开。请注意自定义状态栏的颜色。俏皮！

![Full Screen Quina](img/77f29ff356e9727c85afb0f812a77acc.png)

A PWA opens in its own fullscreen window — no browser bar, just like a native app!

渐进式网络应用程序甚至可以在设备没有连接到互联网的情况下运行，并可以在设备上本地存储自己的数据。

由于最近对浏览器 API 的改进，PWAs 甚至能够进行设备振动、访问设备的摄像头和蓝牙连接，甚至创建推送通知。(需要注意的是，iOS 限制了一些功能，包括推送通知。就像他们说的，一个坏苹果。)

任何网站都可以是 PWA。尽管名字如此，它实际上并不一定是你所说的“应用程序”。一个静态的博客，一页纸，一个电子商务商店，或一个简单的小册子网站可以是一个 PWA。Twitter 和星巴克是提供 PWA 体验的公司之一。

网站是如何建立的也不重要。许多 pwa 都是使用现代 JavaScript 框架制作的(比如 Vue，我们将在这里重点介绍)，但这只是因为它们很方便。

说到 PWAs，Vue 没有什么特别的，甚至一般来说框架也没有什么特别的。

简单来说:如果它在浏览器中加载，它可以是一个渐进式的 web 应用程序。

打好基础后，让我们具体谈谈如何用 Vue 构建 PWA，Vue 是我个人最喜欢的 web 开发工具之一。

## 如何用 Vue.js 把一个 app 变成 PWA

pwa 有一些要求，包括:

*   JavaScript 服务工作者(允许站点离线加载和本地存储数据)
*   应用程序信息的有效 JSON 清单，填写了正确的信息
*   一组正确命名的图标，有多种尺寸
*   安全的 HTTPS 连接

如今，大多数主机都免费提供 SSL 证书(许多甚至是自动提供的)，所以我们在这里不讨论 HTTPS 的要求。这应该是最简单的部分。

其余的听起来可能不多，但是在创建所有这些东西的过程中有很多样板文件，包括文件和代码本身。如果您要手动操作，至少可以说，这将是一项乏味的任务。

幸运的是，Vue 有一个更简单的方法，这要感谢 [Vue CLI](https://cli.vuejs.org/) ！

Vue CLI 提供了一个方便的 PWA 插件，更好的是，它可以添加到现有的或新的项目中。

## 将 PWA 插件添加到现有的 Vue 项目

如果你想把一个现有的 Vue 项目转换成 PWA，这个插件可以很容易地安装在它自己的系统上。安装 PWA 插件的命令(假设您已经安装了 Vue CLI)很简单:

```
vue add pwa

```

在项目文件夹中的命令行上运行此程序，Vue CLI 将安装将项目转换为渐进式 web 应用程序所需的所有文件和配置。

原始项目甚至不需要用 Vue CLI 创建，尽管您可能需要根据您的项目结构进行一些调整。

如果您已经在现有的 Vue 项目中成功运行了该命令，您可以跳过下一部分，我们将讨论该插件实际上做了什么以及之后如何定制它。

> 请注意，如果您的原始项目是用 [Nuxt](https://nuxtjs.org/) (一个 Vue 元框架)构建的，那么您可以在 [Nuxt PWA 模块](https://pwa.nuxtjs.org/)中找到一个更合适的解决方案。
> 
> Nuxt 和它的插件生态系统在这篇文章的范围内有点难处理，但是我们在这里讨论的 PWA 一般原则也适用于那里。不同之处在于如何输入配置选项。因此，当概念转换时，如果你正在使用 Nuxt，我会让你参考 [Nuxt PWA 模块文档](https://pwa.nuxtjs.org/setup)了解细节。

## 使用 Vue CLI 创建新项目

首先，安装 Vue CLI。

```
npm install -g @vue/cli
# OR
yarn global add @vue/cli

```

安装了 Vue CLI 后，在您的终端中输入以下命令(如果您愿意，可以更改名称`my-vue-pwa`):

```
vue create my-vue-pwa

```

您应该会看到一个提示，询问您想要创建哪种项目:

![Pick Preset](img/ba67b7c0c50d111e571c114cfd1ed1bb.png)

选择**手动选择功能**，你会看到新应用的单点选项列表，包括`Progressive Web App (PWA) Support`。

使用箭头键向下移动到该选项，并按下**空格键**来启用它。如果您愿意，您还可以添加或删除其他功能，但是除了 PWA 选项，我将坚持使用显示的默认值。

![PWA Support Options](img/7a343f9d754d318d04c9de23c5ea748e.png)

Be sure the Progressive Web App (PWA) Support option is checked.

当你对自己的选择满意时，点击**回车**，系统会提示你选择 Vue 2 或 3。你喜欢哪个就去哪个。

然后 Vue CLI 会问你几个关于你的项目的问题，除非你有不这样做的理由，否则我建议每个问题都使用默认设置。安装完成后，您应该会看到类似这样的内容:

![Vue Project](img/3789761e584b9b57f6047ddc45e9cb0a.png)

Our Vue project has been successfully created.

现在我们已经设置好了项目，让我们在 [VS Code](https://code.visualstudio.com/) (或者您喜欢的文本编辑器)中打开项目文件夹。

## Vue PWA 插件如何工作

当您查看为我们生成的项目 Vue CLI 中的文件夹和文件时，您会看到一个名为`registerServiceWorker.js`的文件。顾名思义，这个文件将有助于创建一个服务工作者，它将在本地存储站点的数据。

![RegisterServiceWorker](img/bf964c372a454f4a20c6c3920518f7c2.png)

实际的服务人员是在构建步骤中自动创建的，但是当我们选择“渐进式 web 应用程序”选项时，您可以看到 Vue CLI 为我们做的一些工作。

这意味着我们在开发过程中不会看到我们的服务人员，这实际上是一件好事。在开发过程中，服务人员更多的是碍事，而不是帮忙，因为他们主动提供缓存文件，而不是显示我们最近的更改。

既然如此，我们就来谈谈如何像用户一样实际查看和使用我们的 PWA。

## 测试 PWA

对于构建我们的应用程序的实际代码，Vue 的默认`npm run serve`将很好地为我们服务(完全是双关语)。在开发我们的组件、代码和应用本身的功能时，这将继续是我们的目标。

服务工作者和清单文件(对 PWA 功能至关重要)都是在生产构建期间生成的，在开发环境中并不完全起作用。为了解决这个问题，我们需要构建站点，然后服务它。

你可以用像 [Netlify](https://www.netlify.com/) 、 [Vercel](https://vercel.com/) 或 [Render](https://render.com/) 这样的主机来做这件事。提交、推送并等待构建版本上线，以便看到它并与之交互，这是完全可行的。

有了这么简单的应用程序，构建应该不到一分钟。这种方法还有一个独特的好处:您可以自动获得为 HTTPS 连接安装的 SSL 证书。

也就是说:我通常更喜欢在本地建设和服务。它的速度更快，并且不会花费你在主机上的任何构建时间。该命令将在一行中处理这两种情况:

```
npm run build && npx http-server dist

```

运行它(可能会提示您点击 **y** 来批准临时安装`http-server`包)，您将有一个本地域可以用来本地访问您构建的站点:

![Local Domain](img/d4b12b4deac001eb730eb227f9b891fa.png)

> 注意:有许多 HTTP 服务器包可用。出于这个目的，我喜欢使用`http-server`,因为正如您所看到的，它提供了一系列 IP 和端口供您使用，因此如果您在其中一个上遇到了缓存问题，您可以尝试另一个。但是欢迎您使用另一个包(和/或安装您永久使用的包，而不是每次都使用`npx`)。

让我们在浏览器中打开第一个 IP 地址。你可以在 Mac 上按住 command 键点击 VS 代码，在 Windows 上按住 control 键点击，或者只是简单的复制粘贴。

如果你用 Chrome 打开这个网站，你可能会注意到你现在可以从浏览器栏安装 PWA 了。

![Install App](img/29df56bdb36deb889102c432996129ad.png)

(可悲的是，Firefox 和 Safari 只在 Android 和 iOS 上支持 PWAs，在桌面上不支持。)

点击**安装。**你的应用将被安装并在新的独立窗口中打开。

![Standalone Window](img/4e576705921d7dcf0d693ecf1f9a578c.png)

请注意，我们的 PWA 窗口看起来就像一个应用程序窗口，如果你看你的 dock，你会看到我们的应用程序的默认 Vue 徽标:

![App Logo Taskbar](img/8de15001bb44e06d26ed18eaf993eb99.png)

很酷，对吧！？

PWA 可以轻松地安装在平板电脑或智能手机上，并以相同的方式加载。

一个警告:只要您在浏览器中查看构建的生产站点，服务人员就会非常积极地介入并缓存资产。打开**网络**选项卡并勾选**禁用缓存**复选框。

我还建议在 Chrome 中重新加载时检查**应用>服务人员>的更新，如图所示。**

![Update on Reload](img/71e9fcfe84ea50e864a8fa0f9d0cf05c.png)

Be sure to configure your site’s service worker to avoid caching issues.

如果所有这些都失败了，您可以通过在浏览器的开发工具的同一个面板中更新或取消注册服务工作器来强制刷新。

最后，关于服务人员还有最后一件重要的事情要提:他们本身就是一个复杂的话题。虽然 PWA 插件为我们设置了一个，但它充满了标准的默认设置，可能不会根据您的应用程序的需求进行理想的配置。

## 在 Vue 中定制我们的 PWA

PWA 插件的默认设置是可用的，但是让我们自定义我们的应用程序的颜色和标志。

为了覆盖 web 应用程序清单中的默认值，我们需要在项目的根目录中设置一个`vue.config.js`文件。你可以查看 [PWA 插件文档](https://cli.vuejs.org/core-plugins/pwa.html)了解全部细节，但是无论我们的`vue.config.js`文件中定义了什么，都将优先于 PWA 插件的默认设置并覆盖它们。

根据文档，你也可以在项目的`package.json`文件中定义这些设置，但是我发现 JavaScript 比 JSON 更容易编写，语法上也没有 JSON 严格。

PWA 插件文档列出了你可以控制的所有属性，但是有几个值得强调。

查看`pwa.name`和`pwa.themeColor`，它们都在[配置标题](https://cli.vuejs.org/core-plugins/pwa.html#configuration)下。以下是文档截图，供参考:

![Docs for Reference](img/92e8c8f4a73909cbd19f96bcc77264bd.png)

注意这里的默认值。当我们不选择任何选项时，知道哪些选项将适用是很方便的。(在上面的例子中，来自`package.json`的`name`字段和 Vue 绿色。)

继续在项目的根目录下创建一个名为`vue.config.js`的文件。打开它并添加代码，就像这样:

```
module.exports = {
  // ...Other Vue config options here, possibly
  pwa: {
    name: "Liquid Measurement Converter",
    themeColor: "#ffd100"
  }
};

```

现在，如果我们重新运行构建和服务脚本，我们可以重新安装 PWA。我们应该看到我们的新标题和主题颜色反映出来。

![Liquid Measurement Converter](img/d784456a5453082cbc8f5397558facff.png)

请注意清单中有一些必填字段 PWA 插件只是为我们处理它们。为你的应用取一个简短的名字(只有几个字符)可能是个好主意；“液体测量转换器”在某些情况下会太长。

还有其他颜色选项值得调整；同样，更多细节参见[插件文档](https://cli.vuejs.org/core-plugins/pwa.html)。

### 添加自定义 PWA 图标

PWA 插件为我们做了另一件好事:它在`public/img/icons`中为我们的应用程序生成了一组图标:

![Public Img Icons](img/d11f0ade008badcda08d0dc3ddf5df6e.png)

这些图标的名称和大小很重要。各种浏览器和操作系统将搜索这些图标来帮助识别我们的 PWA。

我们也可以通过我们的网络应用清单来控制我们的图标。为了改变我们希望 PWA 使用的图标，我们需要在`vue.config.js`内部的`pwa`选项对象中添加一个`icons`属性。

下面是默认的样子，仅供参考:

```
module.exports = {
  // ...Other Vue config options here, possibly
  pwa: {
    // ...Other pwa config options
    icons: {
      favicon32: 'img/icons/favicon-32x32.png',
      favicon16: 'img/icons/favicon-16x16.png',
      appleTouchIcon: 'img/icons/apple-touch-icon-152x152.png',
      maskIcon: 'img/icons/safari-pinned-tab.svg',
      msTileImage: 'img/icons/msapplication-icon-144x144.png'
    }
  }
}

```

在 UI 中任何地方提到我们的应用程序，这些图标都将作为我们应用程序的标志。这些将被用作收藏夹图标，应用程序图标，状态栏指示器等。

再次强调:如果没有正确的图标文件，PWA 将无法安装。 *图标必须放在正确的位置，并正确命名，以获得通用支持。*

 *幸运的是，PWA 插件已经添加了我们需要的所有图标。我们需要做的就是用我们选择的相同大小和名称的图标替换每张图片。

如果您想制作一个版本并自动化其余的版本，有几个基于浏览器的 PWA 图标生成器工具可用。我发现 realfavicongenerator.net 是这些工具中最有用的。理想情况下，原始图像应该是 512 像素乘 512 像素，因为这是任何操作系统或浏览器可能使用的最大尺寸。

知道在构建之后，你可以在你的文本编辑器中打开`dist/manifest.json`(或者在浏览器窗口中访问`/manifest.json`)来获得 Vue PWA 插件为你的项目生成的清单文件的第一手信息，这可能是有帮助的，只是为了确保进入`vue.config.js`文件的内容正确地从另一端出来。

### Vue 的其他 PWA 选项

还有其他几个配置选项可用。浏览 [PWA 插件文档](https://cli.vuejs.org/core-plugins/pwa.html)中的选项列表，了解什么是可能的。

如果有疑问，我建议坚持使用默认设置，除非你有令人信服的理由做出改变。在事情变得复杂的地方，文档在链接到解释者方面做得很好。

## 提示用户安装 PWA

用户可以直接从他们的浏览器安装 PWA(取决于浏览器和操作系统)，但该选项通常不太明显。如果我们真的希望用户安装我们的 PWA，那么我们应该让它变得容易。

浏览器供应商有意识地防止令人讨厌的滥用 PWA 提示符，因此有一种特定的方法来实现这一点。这里有一篇关于[如何提供你自己的应用内安装体验](https://web.dev/customize-install/)的帖子，但是我们将专门为 Vue 定制方法。

我们将把代码包装在一个 Vue 组件中，该组件可以放入任何 PWA 项目中:

```
<!-- PWAPrompt.vue -->
<template>
  <div v-if="shown">
    Add app to home screen?

    <button @click="installPWA">
      Install!
    </button>

    <button @click="dismissPrompt">
      No, thanks
    </button>
  </div>
</template>

<script>
export default {
  data: () => ({
    shown: false,
  }),

  beforeMount() {
    window.addEventListener('beforeinstallprompt', (e) => {
      e.preventDefault()
      this.installEvent = e
      this.shown = true
    })
  },

  methods: {
    dismissPrompt() {
      this.shown = false
    },

    installPWA() {
      this.installEvent.prompt()
      this.installEvent.userChoice.then((choice) => {
        this.dismissPrompt() // Hide the prompt once the user's clicked
        if (choice.outcome === 'accepted') {
          // Do something additional if the user chose to install
        } else {
          // Do something additional if the user declined
        }
      })
    },
  }
}
</script>

```

现在，我们需要在我们的应用程序中导入并使用该组件，只要我们希望提示出现，它就会为我们处理其余的事情。

如果用户点击安装，我们不需要做任何事情；浏览器会处理这个问题。我们等待浏览器触发默认的`beforeinstallprompt`事件，拦截它，并显示我们更直观的提示。

例如，如果我们想记录结果，或者显示另一条消息，我们可以在上面的`if` / `else`中做一些额外的事情，但我们不是必须这样做。

但是，我们不能再次强制提示，也不能为 PWA 的安装创建任何其他方式。浏览器可以完全控制何时使用该功能。

> 注意:我没有在这个组件中包含任何样式；只有最小的功能位。这里有一个针对提示的各种方法和最佳实践的指南。

## 排除 PWA 故障

如果您在显示安装提示或验证 PWA 时遇到问题，请仔细检查以下事项。

### 你方的水渍险是在 HTTPS 装船吗？

pwa 需要安全的 HTTPS 连接(除非在本地加载)。

### 你有旅客名单吗？

您应该能够访问 PWA URL 上的`/manifest.json`,并在浏览器中看到它的加载。

### 控制台里有图像 404 吗？

如果你的 PWA 没有在正确的位置以正确的大小命名正确的图像，它可能无法安装。如果是这种情况，开发工具中的控制台应该会显示错误。

### 你有服务人员吗？

PWA 插件为我们解决了这个问题，但是仍然值得验证你是否看到了工人加载。你应该可以在**网络**标签中看到一个`service-worker.js`文件被加载，或者在开发工具的**应用>服务**工作器中看到它被注册。

### 你的服务人员正在运行吗？

如果您处理的更改没有按预期显示，您可能需要尝试隐藏窗口，禁用缓存，和/或在 dev tools 中注销服务工作器。

### 你的提示码正确吗？

检查前一部分，验证您的提示代码正在做它需要做的一切。

### 您当前的浏览器和操作系统支持 PWAs 吗？

记住，Firefox 和 Safari 不支持桌面上的 PWAs。iOS 也限制了一些 PWA 功能，但是 PWA 应该还是可以在 iOS 上安装的。下面是 Chrome 的[安装标准。](https://web.dev/install-criteria/)

## 结论

我按照本文中概述的步骤将 liquid measure conversion CodePen 项目转换为完整的 PWA。

在 [vlmc.netlify.app](https://vlmc.netlify.app) 可以看到成品。我对原来的 UI 格式做了一些明显的调整，使它在手机上使用起来更加友好，但总体思路是一样的。

![Improved UI](img/316d2d4cdf8da8a052d86da5436f6cc0.png)

这里是完成的 GitHub repo ，这里是我完成的 PWAPrompt 组件[，具体来说，如果你想为自己的再利用而调用它。请务必根据需要调整代码。](https://github.com/josh-collinsworth/vue-liquid-measurement-converter/blob/main/src/components/PWAPrompt.vue)

就我个人而言，我很高兴将这个项目转换成 PWA，并且 PWA 在旅途中提供的可能性让我重新充满了活力。进步的网络应用是强大的，令人惊奇的，令人兴奋的！我希望你受到启发，在自己的项目中亲自尝试这项技术。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。*