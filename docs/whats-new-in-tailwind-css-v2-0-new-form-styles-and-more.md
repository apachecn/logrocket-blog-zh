# Tailwind CSS v2.0 的新特性:新的表单样式和更多日志

> 原文：<https://blog.logrocket.com/whats-new-in-tailwind-css-v2-0-new-form-styles-and-more/>

## 介绍

根据其官方网站的说法，Tailwind CSS 是一个“实用优先的 CSS 框架，包含了许多类…可以直接在你的标记中构建任何设计。”它是底层的，与其他基本上告诉你做什么的 CSS 框架相比，它不是固执己见的。这允许你以声明的方式用最少的 CSS 来组成你的 UI 元素。

这篇文章介绍了 Tailwind CSS 2.0 中的一些新特性，以及如何在你的项目中使用它们。它还将涵盖一些突破性的变化，最后是向 2.0 版本的迁移。我们开始吧！

## 顺风 CSS v2.0 的新特性和变化

### 黑暗模式

最近，黑暗模式已经成为一个非常可取的功能。要在顺风应用中添加黑暗模式样式，给类加上前缀`dark:`，就像这样:

```
<div class="bg-white dark:bg-gray-800">
  ...
</div>
```

默认情况下不启用黑暗模式，因为它会影响最终的束尺寸。要启用黑暗模式，将以下内容添加到`tailwind.config.css`文件:

```
// tailwind.config.js
module.exports = {
  darkMode: 'media',
  // ...
}
```

在引擎盖下，它使用`[prefers-color-scheme](https://blog.logrocket.com/new-media-queries-you-need-to-know/)`媒体功能来切换`dark`变体。因此，当用户的操作系统启用黑暗模式时，`dark`变量优先于无前缀的类。

还有一种方法可以手动切换黑暗模式，而不依赖于操作系统的偏好。在顺风配置文件中，用`class`替换`media`:

```
// tailwind.config.js
module.exports = {
  darkMode: 'class',
  // ...
}
```

当类别`dark`出现在根`<html>`标签上时，将应用黑暗模式类别:

```
<!-- Dark mode enabled -->
<html class="dark">
<body>
  <!-- Will be black -->
  <div class="bg-white dark:bg-black">
    <!-- ... -->
  </div>
</body>
</html>
```

### 新的调色板

Tailwind CSS 2.0 带有一个全新的调色板，共有 220 个值，远远多于上一版本的 90 个值。现在每种颜色的色调范围从 50-900。调色板还包括五种灰色，范围从“蓝灰色”到“暖灰色”

所有颜色都增加了一种新的超浅色阴影`50`,可以这样使用:

```
<div class="bg-gray-50">This div has a gray background.</div>
```

### 不再支持 IE11

随着 v2 的发布，Tailwind 团队决定放弃对 IE11 的支持。这将导致较小的构建大小，因为最终构建中没有捆绑聚合填充。焦点将转移到 CSS 自定义属性等更新的功能上，这些功能提供了更大的可能性。

如果您仍然需要对 IE11 的支持，您可以继续使用 Tailwind CSS v1.9，它仍然很棒——尽管它没有提供本文中提到的新功能。

### 新的表单样式和`custom-forms`插件

使用以前版本的 Tailwind CSS，表单元素看起来非常光秃秃，因此必须编写大量的自定义样式，使它们看起来有点像样。

随着 2.0 版本的发布，一个名为`@tailwindcss/custom-forms`的新官方插件也发布了。它将表单控件重置为一种可以用内置的实用程序类轻松设计的状态。

```
<!-- This will be a nice rounded checkbox with an indigo focus ring and an indigo checked state -->
<input
  type="checkbox"
  class="h-4 w-4 rounded border-gray-300 focus:border-indigo-300 focus:ring-2 focus:ring-indigo-200 focus:ring-opacity-50 text-indigo-500"
/>
```

默认情况下不包含它，所以如果您想使用它，您必须显式地将它添加到您的`tailwind.config.js`文件的`plugins`部分:

```
// tailwind.config.js
module.exports = {
  // ...
  plugins: [require('@tailwindcss/forms')],
}
```

你可以查看[文档](https://github.com/tailwindlabs/tailwindcss-forms)了解更多细节。

### 新的轮廓环实用程序

Tailwind 团队增加了新的轮廓环工具，可以用来在元素上创建一个方框阴影。这对于指示按钮上的悬停/聚焦状态特别有用:

![Box Shadow on Focus](img/b15a0ab94a19fa2ec519bccdaaf7f900.png)

Box shadow on focus, courtesy of the Tailwind CSS docs.

```
<button class="... focus:ring-4 focus:ring-green-500 focus:ring-opacity-50">
  Button
</button>
```

环形实用程序使用`box-shadow`而不是`border`，这将导致布局回流并扭曲用户体验。

### 扩展间距、版式和不透明度

间距实用程序已经扩展到包括分数值— `0.5`、`1.5`等。：

```
<span class="ml-3.5">Just a little nudge.</span>
```

更高间距的实用程序也有所扩展:

```
<div class="p-96">This is too much padding.</div>
```

排版比例也被扩展到包括`7xl`、`8xl`和`9xl`:

```
<h1 class="text-9xl">What is this, an Apple website?</h1>
```

不透明度扩展到包括步骤`10`，从`0`到`100`:

```
<figure class="opacity-20">
  <p>Can you see me?</p>
</figure>
```

### 新的`text-overflow`实用程序

添加了新的溢出实用程序来控制`text-overflow`属性，包括`overflow-ellipsis`和`overflow-clip`。这将允许分别向截断的文本添加省略号或剪切文本。

```
<p class="overflow-ellipsis overflow-hidden">
  Look ma no whitespace-nowrap ipsum...
</p>
```

关于 Tailwind CSS 2.0 中的变化的完整列表，请查看[发行说明](https://blog.tailwindcss.com/tailwindcss-v2)。

## 迁移到顺风 CSS v2.0

由于新版本中有许多新的变化，包括一些突破性的变化，因此在迁移过程中必须采取一些步骤。据该团队称，大多数项目的迁移时间应该不到 30 分钟。让我们看看如何升级 Tailwind CSS，以及一些需要注意的事情。

### 安装顺风 CSS v2.0 和 PostCSS 8

新版本需要 PostCSS 和 Autoprefixer 作为对等依赖项，因此它们必须与 Tailwind 并行安装:

```
npm install [email protected] [email protected] [email protected]
```

如果遇到任何问题，您可能需要运行 [PostCSS 7 兼容性构建](https://tailwindcss.com/docs/installation#post-css-7-compatibility-build)。

### 升级至 Node.js 12.13 或更高版本

顺风 CSS v2.0 要求 Node ≥12.13 来构建你的 CSS。如果您运行的是较低版本的 Node，则需要对其进行升级。

需要进行的其他一些更改包括:

*   更新排版和表单插件，如`@tailwindcss/typography`和`@tailwindcss/custom-forms`(到`@tailwindcss/forms`)
*   删除未来和实验配置选项

    ```
    module.exports = {    // these are no longer needed    future: {      defaultLineHeights: true,      purgeLayersByDefault: true,      removeDeprecatedGapUtilities: true,    },    experimental: {        additionalBreakpoint: true,        extendedFontSizeScale: true,        extendedSpacingScale: true,    },    // ... }
    ```

*   更新重命名的实用程序类
*   用环形工具替换`shadow-outline`和`shadow-xs`
*   明确配置您的调色板

有关升级指南的完整参考，请访问 [Tailwind CSS 文档](https://tailwindcss.com/docs/upgrading-to-v2)。

## 结论

在这篇文章中，我们介绍了 Tailwind CSS v2.0 中引入的一些新变化。我们还讨论了如何将现有应用程序迁移到新版本，以及一些可能影响某些项目的重大变化。如果您的项目依赖于这些不再受支持的特性，您可以继续使用 v1.9。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。