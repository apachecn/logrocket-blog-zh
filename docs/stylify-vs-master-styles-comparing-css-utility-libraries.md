# Stylify 与 Master 风格:比较类似 Tailwind 的 CSS 实用程序库

> 原文：<https://blog.logrocket.com/stylify-vs-master-styles-comparing-css-utility-libraries/>

CSS 是一种用于设计网页样式的声明式全局语言。当它依赖于 HTML 时，每当 HTML 改变时，它就需要改变，这使得它难以大规模维护。

这个问题在很久以前就用面向对象 CSS 的概念解决了，它催生了具有 Bootstrap 和布尔玛等许多库的组件 CSS，同时还引入了可重用性。

基于组件的框架所带来的可重用性是有限的，由此产生了实用优先的框架，如 Tailwind CSS。

在本文中，我们将比较两个实用至上的 CSS 库——Stylify 和 Mater Styles 用它您可以编写更少的代码来构建独特的、响应性更强的 UI。

我们还将讨论它们提供的开发人员体验以及它们非常适合的项目类型。

## **先决条件**

*   熟悉基本的 CSS
*   效用第一框架的基础知识

## 什么是效用至上的 CSS？

基于组件的库带有一组预构建的组件和语义类，它们不会告诉你 HTML 元素应该是什么样子，而是告诉你它是什么。

基于组件的库的问题是，每当 HTML 改变时，CSS 就会中断。这个问题可以通过使用实用优先库来解决。

实用程序优先库为您提供了助手类，用于在 HTML 标记中构建定制的复杂样式。这些类是单一用途的、易于定制的、面向样式的，可以洞察 HTML 元素。

## 什么是风格化？

Stylify 是一个轻量级(20KB)的 CSS 库，它基于你编写的 HTML 动态生成实用优先的 CSS。

它带有一个[原生预置](https://stylify.dev/docs/stylify/native-preset)，使用原生 CSS 属性值作为选择器，可以匹配来自多个浏览器的几乎所有 CSS 属性。使用 Stylify，可以分别为每个文件、组件、页面或布局生成 CSS。

### 句法

Stylify 为您提供了使用逻辑运算符组合屏幕的选项，如&& (and)和|| (or)，以及一种特殊的语法，即 __(双下划线)表示空格，^ (carret)表示引号。

这允许您编写如下选择器:

```
<p class="
margin:0__auto 
font-family:^Roboto__Mono^,__monospace
">
  Hello World!
</p> 
```

上面的代码将被精简并生成以下 CSS:

```
._nmed{
 margin:0 auto
}
._l4hja{
 font-family:'Roboto Mono', sans-serif
} 
```

### 安装 Stylify

要使用通过 CDN 构建的默认配置，请复制以下代码并将其放入您的 HTML 文件中:

```
<script src="https://unpkg.com/@stylify/[[email protected]](/cdn-cgi/l/email-protection)/dist/stylify.native.min.js"></script> 
```

您还可以使用 npm 或 Yarn 之类的包管理器通过 CLI 安装 Stylify:

```
// for npm
npm i -D @stylify/stylify
// for yarn
yarn add -D @stylify/stylify 
```

参考这个[指南列表](https://stylifycss.com/docs/integrations/browser)了解如何将 Stylify 与 Laravel、Next.js 等工具集成。

此外，请参考本[指南](https://stylifycss.com/docs/get-started/migrating-to-stylify)了解如何将 Stylify 集成到现有项目中。Stylify 还附带了多个可以使用 npm 或 Yarn 安装的包。

## 什么是大师风范？

主样式不仅仅是一个框架或一个库；它也是一个生态系统，包括用于构建用户界面的工具。

它的概念是基于设计可重用性、增强语法的虚拟 CSS 和动态分配。它提供了超过 172 种现成的样式，无需任何配置。

### 句法

Master 的语法完全不同于其他实用优先的 CSS 库，非常接近原生 CSS。

Master Styles 提供了系统的 CSS 语法，并提供了速记、缩写和符号语法来加速和减少代码行。

以下是在母版样式中使用 32px(像素)字体大小和绿色背景编写一个简单段落的三种方法:

```
<p class="text-align:center font-size:2rem background-color:green-50"></p> 
```

使用属性简写将减少代码行:

```
<p class="text:center font:32 background:green-50"></p> 
```

使用首字母缩略词将进一步减少代码:

```
<p class="t:center f:32 bg:green-50"></p> 
```

### 安装主样式

您可以使用软件包管理器安装主样式。要安装主样式，请复制您首选的软件包管理器的代码:

```
// using npm
npm install @master/styles @master/normal.css
// using yarn
yarn add @master/styles @master/style @master/normal.css
// using pnpm
pnpm install @master/styles @master/style @master/normal.css 
```

下一步是将 node_modules 中的 **@master/styles** 导入到 JavaScript 条目文件中:

```
import '@master/styles'; 
```

然后将 node_modules 中的 **@master/normal.css** 导入到 css 条目文件中:

```
import '@master/normal.css.' 
```

参考[本指南](https://docs.master.co/styles/setup)了解如何将主风格集成到不同的框架中。

## 风格化与主风格的比较

现在，让我们从易用性、性能、开发人员体验、组件以及使用每个库创建示例按钮的过程等方面来比较 Stylify 和 Master Styles 库。

### 易用性

这两个框架都很容易设置和使用，而且它们的学习曲线都很短，很容易上手——特别是如果您已经熟悉像 Tailwind 这样的实用优先的 CSS 框架。

库的易用性主要取决于其文档的直观性和信息量。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在这种情况下，Stylify 的文档更具描述性，在向新用户介绍库方面做得更好。另一方面，Master Styles 的文档比 Stylify 的文档稍微复杂一些，对于新手来说需要更多的时间来入门。

### 表演

选择框架时，性能和速度是至关重要的因素。然而，规模是影响框架性能的一个重要因素，因为规模越小，框架速度越快。

虽然两个库都是轻量级的，但是 Master Styles (13KB)比 Stylify (28KB)小一点；比风格化更快。

此外，Master Styles 提供了一种混合架构，允许您在服务器端从 HTML 预先生成 CSS，然后通过反向解析在客户端使用 JIT，这为您提供了运行时灵活性并提高了页面加载速度。

它们都提供了很好的语法来减少代码行，并提供了实用程序类来帮助定制 ui。

### 开发者体验

开发人员的经验是选择框架的另一个关键因素——两个库都是框架无关的。

Stylify 提供了与 Next.js、Nuxt.js、Laravel、Symfony 等框架的无缝集成。它还可以很好地与 [Webpack](https://stylify.dev/docs/integrations/webpack) 和 [Rollup.js](https://stylify.dev/docs/integrations/rollupjs) 配合使用。将 Stylify 集成到现有项目中很简单。

Master Styles 还提供了与 React、Next.js、Angular、Nuxt.js 和 Laravel 等框架的集成。与大多数 CSS 库不同，Master 在浏览器中实时运行。

### 成分

Stylify 为您提供了创建组件并在需要时重用它们的选项。这遵循了 [DRY 原则](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)(不要重复自己)来帮助减少重写相同代码行的麻烦。它还允许您将组件与实用选择器混合使用。

在撰写本文时，主样式不支持组件的使用。需要时，您必须重复重写相同的代码行。

但是，它提供了直观的简化方式，允许您通过使用缩写来减少代码行。

### 创建示例按钮

以下是如何在 Stylify 中创建一个简单的红色按钮:

```
<button class="font-weight:bold font-size:24px border-radius:4px padding:15px width:400px margin:50px__auto__0__auto text-align:center background:red color:white">Red Button</button> 
```

![Master Styles Red Button Example](img/e62719624db024a332e522e2dc4c22a3.png)

以下是如何在母版样式中创建蓝色按钮的示例:

```
<button class="font-size:24px border-radius:4px padding:15px width:400px margin:50px;auto;0;50px text-align:center color:white bg:blue-60 ">Blue Button</button> 
```

![Master Styles Blue Button Example](img/0da3fd99897f1823c80f16e271e0f33b.png)

这两个框架遵循相似的模式来创建优秀的 UI，但有一些基本的区别。他们两人有一些共同点，比如:

*   它们允许使用本机 CSS 属性，如填充、边距等。
*   它们都允许使用变量
*   它们都是框架不可知的
*   它们都很轻
*   它们都是实用优先的 CSS 库

## 什么时候应该使用 Stylify 和 Master 样式？

选择相对容易——对较大的项目使用 Stylify，对需要几个或要求很高的动画的图像密集型网站和网页使用 Master 样式。

在更大的项目中，更多的组件将被重用，这意味着 CSS 将增长缓慢，这将导致更快的页面加载。这使得 Stylify 成为大型项目的最佳选择。

另一方面，母版样式提供了像“@”(动画)和“~”(过渡)这样的符号，这些符号提供了完整的功能并使动画元素变得更容易。

它还提供了一组常用的关键帧(用于 spirits ),使功能变得生动，并允许您创建自定义关键帧。这使得母版样式成为高强度动画网页的更好选择。

## **结论**

本文比较了被称为下一代类似顺风的 CSS 实用程序库的两个弱势 CSS 框架——Stylify 和 Master Styles。我们基于性能、学习曲线、相似性和其他因素比较了这两个框架。

尽管两个框架相似，Stylify 提供了组件的使用，这使得它比大多数实用优先的 CSS 库更具动态性。它非常适合较大的项目，而另一方面，主样式是动画网页的最佳选择。

Stylify 和 Master Styles 哪个是更好的 CSS 库取决于开发者的用例。虽然这两个库都是新的，但是使用这两个框架的体验都非常好，我鼓励您查看它们的文档来开始使用。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。