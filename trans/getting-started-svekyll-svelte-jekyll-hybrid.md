# Svekyll 入门:Svelte 和 Jekyll 混合- LogRocket 博客

> 原文：<https://blog.logrocket.com/getting-started-svekyll-svelte-jekyll-hybrid/>

Svekyll 是一个静态站点生成器，它结合了[杰基尔](#what-is-jekyll)的简单约定和[苗条](#what-is-svelte)的惊人力量。使用 Svekyll，您可以轻松地将主题应用到您的`config.yml`文件中。另外，Svekyll 支持 [Tailwind CSS 和 daisyUI](https://blog.logrocket.com/daisyui-tailwind-components-react-apps/) 。扩展这些主题比使用 CSS 框架需要更少的工作。

在这篇文章中，我们将学习如何开始使用 Svekyll，Jekyll 和 Svelte。我们还将讨论每种方法的最佳用例，以及 Svekyll 的一些现有和即将推出的特性。我们开始吧！

### 先决条件

要阅读本文，您需要以下内容:

*   HTML、CSS 和 JavaScript 的基础知识
*   您选择的终端
*   代码编辑器

## 什么是哲基尔？

Jekyll 是一个开源的静态站点生成器，允许你用 markdown 编写你的代码，并把它变成布局。当你制作一个包含内容的模板时，你可以运行 Jekyll，你将会得到一个完整的站点，里面都是独立的静态 HTML 页面。之后，您可以将页面上传到您选择的任何服务器，让您的静态网站保持运行。

## Jekyll 用例

为了更清楚地了解 Jekyll，让我们简单回顾一下它的一些用例。

### 更快的页面交付

当客户端向应用程序的服务器发出请求时，通常情况下，服务器必须与数据库进行通信，才能从应用程序的数据库获得响应。

然而，有了 Jekyll，您的服务器不必在收到每个请求时都与数据库通信，总是能更快、更熟练地将页面传递给应用程序的用户。

### 安全性

如前所述，应用程序的服务器不必在每次请求时都与数据库通信。通常，在应用程序数据库出现安全漏洞的情况下，您可能会面临丢失数据的风险。但是，有了 Jekyll，你的 app 更安全。

### 维护更少，成本更低

Jekyll 是一种廉价的网站托管方式。因为 Jekyll 没有数据库，它需要较少的维护，因此节省了维护费用。此外，用 Jekyll 构建的 web 应用免费托管在 [GitHub 页面](https://pages.github.com/)上。

### 支持博客

Jekyll 是博客感知的，这意味着你可以在 markdown 中写你的内容。

## 什么是苗条？

Svelte 是一个 JavaScript 工具，用于构建像 React、Angular 或 Vue 这样的 UI 组件。然而，与这些库和框架不同，Svelte 是一个编译器，它将声明性代码转换为与本机浏览器 API 一起工作的命令性代码。因此，您可以在小的包中获得高性能的代码。

组件在`.svelte`文件中创建，该文件由三个主要部分组成:

1.  包含用 JavaScript 编写的代码的脚本，也可以用 TypeScript 编写
2.  CSS 的一个表示为`<style>`的样式标签，它也可以使用像 SASS 这样的预处理程序
3.  一种表示为 HTML 的模板，具有用于编写声明性代码的各种语法

在需要反应状态的情况下，可以用关键字`let`定义一个变量，然后用括号`[]`在 HTML 文件中引用它。

当涉及到跨组件通信时，Svelte 为组件之间共享数据提供了多种策略。对于更复杂的组件，Svelte 有一个[上下文 API](https://svelte.dev/tutorial/context-api) ，就像 React 一样。

Svelte 也有类似 observables 的商店。您可以在组件中的任何地方共享它们，并通过在它们前面加上美元符号来在模板中订阅它们。

Svekyll 利用 Jekyll 的力量来制作静态站点，并将其与 Svelte 的力量相结合，以获得一个伟大的 UI，给你一个强大的静态站点和一个美妙的 UI！现在，让我们来看看 Svekyll 的一些用例。

## Svekyll 用例

Svekyll 博客反应非常迅速；你可以在不到五秒钟的时间内建立一个普通的博客。让我们看看 Svekyll 的一些用例。

### 复杂可视化

Svekyll 将 Svelte 用于其静态站点 UI，提供了来自没有运行时的框架的性能增益。因此，具有大量 DOM 元素和用户交互的交互式页面速度更快，响应更快。

### 简单学习曲线

Svekyll 有一个简单的学习曲线，只需要 HTML、CSS 和 JavaScript 的基础知识，以及熟悉 Jekyll 和 Svelte。

### 更快的网站

用 Svekyll 构建的站点速度更快，包大小更小，适用于处理能力有限、网络连接速度较慢的设备。

现在我们已经熟悉了 Svekyll，让我们回顾一下它当前和即将推出的一些特性。

## Svekyll 特征

### YAML 前沿问题

[YAML 前台](https://assemble.io/docs/YAML-front-matter.html)用于维护页面及其内容的元数据。您可以使用 YAML front matter 定制任何`.md`文件。Svekyll 允许您在帖子中添加 YAML 前沿内容，然后在您的模板中使用它，如下所示:

```
___
Title: "YAML front matter"
description: "A simple way to include yfm" 
published: true
my key: "some value"
___
Mykey: {my key}

```

代码块内部的信息是 front matter，它定义了一些关于存储在键值对中的内容的基本信息，而 YAML 是 Svekyll 中 front matter 的默认语言。

### Svekyll 和 Tailwind CSS

[Tailwind CSS](https://tailwindcss.com/) 是一个实用至上的 CSS 库，用于构建定制用户界面。Tailwind CSS 没有定义像`button`这样的类，而是定义了像`display-flex`这样的类。

我们不是定义一张卡片，而是定义组成这张卡片的所有部分。这看起来很有压力，因为有太多的类需要定义，然而，当你想在代码中修改和重定义类的时候，这将会更容易。Tailwind CSS 取出未使用的类，与 Svekyll 配合得非常好。

要样式化 Svekyll，添加到`user.css`文件。注意，Svekyll 的样式和结构包含在`Svekyll.css`文件中。为了避免上游合并冲突，您不应该更改这个文件。

在 Tailwind CSS 中调整单个类并不困难，使得将来的编辑变得容易。看看下面代码块中的 Tailwind CSS 示例:

```
.sveyll-header {
        @apply p-8
        bg-green 300
        border-green 600
        hover: bg-red-600;
}

```

可以查看 Svekyll 支持的[主题。](https://svekyll.com/Theme/)

### 大树

daisyUI 是一个 Tailwind CSS 组件库，用于创建带有组件类和可定制主题的干净的 HTML。由于 Svekyll 的[对 Tailwind CSS](https://blog.logrocket.com/how-to-use-tailwind-css-with-svelte/) 的开箱即用支持，您可以将 Svekyll 与 daisyUI 等 Tailwind CSS 组件框架一起使用。虽然你可以用 Svekyll 使用标准 CSS，但我还是强烈推荐 Tailwind CSS。

### 网络提及

[web reference](https://webmention.io/)是一个 JSON feed 和评论工具，可以嵌入到你的博客中。在撰写本文时，web reference 还处于测试阶段，还不容易使用。默认情况下，web reference l看起来更像一个普通的注释 / 会话 工具，但是您可以通过定制样式来以您想要的方式显示它。要使用网络提及，您必须添加一个站点，但首先，您必须添加您的`_config.yml.`:

```
Site:logrocket.com
comments:
    webmentions:
         enabled:true
      #feed:"https:// your url goes here.... 
Links:
          link:https//githib.com/logrocket
          text:"Github"
          icon:'github'

```

您必须为两个评论都配置了 web reference。你还应该有一个社交网站，比如 Twitter 或 GitHub 账户，这样 Webmentions 就可以验证你的身份。

接下来，访问 [Webmentions.io](https://webmention.io/) ，然后输入你的网页地址并认证。现在，你可以登录 [birdy.gy](https://webmention.io/) 开始收集。Webmentions 将提供对社交服务的评论，然后为您提供一个 JSON 提要。

### 静态外托管

ExtraStatic 提供了一种在互联网上管理页面的最快方法。ExtraStatic 提供一流的 Svekyll 托管。ExtraStatic 只托管静态博客，Svekyll 专门为 Svekyll 优化了它的 CI。使用静态 Svekyll 托管，不到十秒钟就可以更新更改。

ExtraStatic 将增加的一个功能是收集以太坊微支付的选项，使您能够轻松地从您的读者那里接收以太坊支付。

### 搜索引擎优化

SEO，搜索引擎优化，通过提高网站在搜索引擎上的排名来增加网站的可见性。

使用静态网站的一个主要好处是它们比动态网站更快。默认情况下，Svekyll 为 SEO 优化每一篇文章和页面，帮助您的网站排名高于许多动态网站。每篇博文都是根据 YAML 头版的标题和描述进行优化的。

要自定义 SEO，您必须导入 SEO 商店，然后设置您选择的标题和描述，如下所示:

```
<script>
       import seo from '$lib/store';
       $seo { tittle: "This is my title", description: "This is my description"}
</script>

```

修改 SEO 为您提供了搜索引擎中单页静态索引的所有好处。

## 结论

在本文中，我们介绍了 Svelte 和 Jekyll，了解了 Svekyll 是如何将它们结合起来创建令人惊叹的静态网站的。尽管在撰写本文时 Svekyll 的一些特性还不可用，但 Svekyll 是一个构建静态站点的神奇工具，您应该尝试一下。在下面给我们留下评论，让我们知道你的想法。我希望你喜欢这篇文章！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)