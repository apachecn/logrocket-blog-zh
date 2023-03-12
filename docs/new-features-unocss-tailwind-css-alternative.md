# UnoCSS 中的新特性:一个顺风 CSS 替代方案- LogRocket 博客

> 原文：<https://blog.logrocket.com/new-features-unocss-tailwind-css-alternative/>

UnoCSS 是一个完全可定制的原子 CSS 引擎，灵感来自于 [Windi CSS](http://windicss.org/) 、Tailwind CSS 和 [Twind](https://github.com/tw-in-js/twind) 。UnoCSS 不是一个 CSS 框架，但它是一个可以用来创建框架的引擎。它提供了流行的实用优先 CSS 框架的公共超集，如 Tailwind CSS、Windi CSS 和 Bootstrap。

由于向 CSS 引入了几个令人鼓舞的特性，UnoCSS 正成为人们谈论最多的样式工具之一。例如，UnoCSS 是灵活的，没有核心实用程序，它的功能通过几个预置来提供。

在本教程中，我们将浏览这些新特性，学习如何在我们的应用程序中应用它们。要跟随本教程，您需要以下内容:

*   一个好的文本编辑器
*   对 CSS 及其框架有很好的理解

我们开始吧！

## UnoCSS 入门

要通过 Vite 安装 UnoCSS，请运行以下命令:

```
npm i -D unocss
```

> 要深入了解可以用来在应用程序中安装 UnoCSS 的其他包，请查看这个 [GitHub repo](https://github.com/antfu/unocss/tree/main/packages) 。

然后，用下面的代码导入 UnoCSS:

```
// vite.config.ts
import Unocss from 'unocss/vite'
export default {
  plugins: [
    Unocss({ /* options */ })
  ]
}

```

最后，在主条目中添加`uno.css`:

```
// main.ts
import 'uno.css'

```

现在我们已经安装了 UnoCSS，让我们看看它带来的新特性，在我们的演示项目中安装每一个。

## UnoCSS 默认和自定义预设

通过预设，您可以在 UnoCSS 中创建自己的自定义框架。您还可以将您的自定义规则和变体组合成预设，以便与其他人共享。

[@unocss/preset-uno](https://github.com/antfu/unocss/tree/main/packages/preset-uno) 是默认预设。它是常见的[实用优先 CSS 框架的超集，如 Tailwind CSS](https://blog.logrocket.com/creating-custom-themes-tailwind-css/) 、WindiCSS 和 Bootstrap。来自这些流行框架的不同实用程序在 UnoCSS 中都是合法使用的:

```
.ml-3 { margin-left: 0.75rem; } // TailwindCSS
.ms-2 { margin-inline-start: 0.5rem; } // Bootstrap
.mt-10px { margin-top: 10px; } // WindiCSS
.ma4 { margin: 1rem; } // Tachyons

```

其他预设包括:

要在应用程序中设置预设，请使用以下代码:

```
// vite.config.ts
import Unocss from 'unocss/vite'
import { presetUno, presetAttributify } from 'unocss'

export default {
  plugins: [
    Unocss({
      presets: [
        presetAttributify({ /* preset options */}),
        presetUno(),
        // ...custom presets
      ]
    })
  ]
}

```

如果您指定要使用的预设，默认预设将被忽略。您也可以通过将`presets`设置为空数组来禁用默认预设:

```
// vite.config.ts
import Unocss from 'unocss/vite'

export default {
  plugins: [
    Unocss({
      presets: [], // disable default preset
      rules: [
        // your custom rules
      ]
    })
  ]
} 

```

## 用户现在可以控制 CSS 范围

UnoCSS 允许用户控制 CSS 重置，调整他们在 UnoCSS 上使用的框架以适应他们的需求，并打开了 CSS 范围的可能性。

例如，在 [Vite 插件](https://www.npmjs.com/package/@unocss/vite)上有一个`scoped-vue`模式，它为单个组件生成作用域样式。现在，您可以成功地将作用域样式作为组件库进行移动，而不会与用户的 CSS 发生冲突。请参见下图:

```
<template>
  <div class="m-2 rounded"><slot></div>
<template>
<!-- the following will be inject in the bundler -->
<style scoped>
.m-2{margin:0.5rem;}
.rounded{border-radius:0.25rem;}
</style>  

```

## UnoCSS 是完全可定制的

与其他基于实用程序的 CSS 框架不同，UnoCSS 在任何应用程序中都是完全可定制的。它从 Tailwind CSS 等其他框架中获取预设逻辑，但设计人员可以改变预设代码中的特定属性。

假设在设计应用程序的样式时，您想要添加一个 0.25 `rem`的元素边距。不用写传统的`margin: 0.25rem`，可以用`m-1`来表示:

```
rules: [
  ['m-1', { margin: '0.25rem' }]
]

```

现在，如果你想给网页上的任何元素 25 px 的边距，你只需使用`m-1`。

## UnoCSS 属性模式

Attribufy 模式是 UnoCSS 中一个惊人的新特性，它允许您用属性或类来组织和分组您的实用程序。它保留了常规的 Tailwind CSS 语法，但使您能够使用带有类和属性的 Tailwind CSS 实用程序。假设您正在使用 Tailwind CSS 的实用程序设计网页上的一个按钮:

```
<button id="start-btn" class="bg-pink-700 px-9 py-3 text-white text-2xl rounded-lg hover:bg-pink-400">Start</button>

```

按钮的代码会变得很长，很难跟上。然而，UnoCSS [属性化模式](https://windicss.org/posts/attributify.html)让您可以轻松地将这些实用程序转换为属性:

```
<button 
id="start-btn" 
bg="pink-700 hover:pink-400"
p="x-9 y-3"
text="white 2xl"
border="rounded-lg"
>
Start
</button>
```
UnoCSS also provides support for attributes that do not have a particular value. For instance, you can now express 
```
<div class="flex justify-center gap-4" />
```
as:
```
<div flex justify-center gap-4 />
```

以这种方式使用属性模式为更干净、更有组织的代码铺平了道路。

## UnoCSS 使用纯 CSS 图标

UnoCSS 使用完全为 CSS 而不是 JavaScript 制作的图标，使得在应用程序中使用图标更加容易。用下面的命令安装从[图标化](https://iconify.design/)预置的 UnoCSS 图标:

```
npm i -D unocss @unocss/preset-icons @iconify/json

```

安装这个预置后，你会发现一个名为`@iconify/json`的文件夹，就是保存 Iconify 的图标数据的文件夹。您可以在您的`vite.config.js`中导入它，如下所示:

```
import { defineConfig } from 'vite'
import Unocss from 'unocss'
import UnocssIcons from '@unocss/preset-icons'
export default defineConfig({
  plugins: [
    Unocss({
      // when `presets` is specified, the default preset will be disabled
      // so you could only use the pure CSS icons in addition to your
      // existing app without polluting other CSS 
      presets: [
        UnocssIcons({
          // options
          prefix: 'i-',
          extraProperties: {
            display: 'inline-block'
          }
        }),
        // presetUno() - if you want to use other atomic CSS as well
      ],
    }),
  ],
})

```

您也可以安装单独的图标集。例如，您可以为[碳图标](https://www.carbondesignsystem.com/guidelines/icons/library/)安装`@iconify-json/carbon`，或者为[材料设计图标](https://materialdesignicons.com/)安装`@iconify-json/mdi`。

## UnoCSS 使用自定义变量

[变体](https://windicss.org/utilities/variants.html#variants)允许你指定一个实用程序什么时候应该被激活，例如，你可以使用屏幕大小，系统主题，或者任何`pseudo-selector`比如`:checked`或者`:hover`。

您可以使用`:seperator`指定变量，并在需要时以下列方式组合它们:

```
md:bg-black-500 sm:hover:bg-grey-300 dark:bg-white

```

假设您想使用 TailwindCSS 的`:hover`变体。您可以这样做:

```
variants: [
  // hover:
  (matcher) => {
    if (!matcher.startsWith('hover:'))
      return matcher
    return {
      // slice `hover:` prefix and passed to the next variants and rules
      matcher: matcher.slice(6),
      selector: s => `${s}:hover`,
    }
  }
],
rules: [
  [/^m-(\d)$/, ([, d]) => ({ margin: `${d / 4}rem` })],
]

```

在上面的代码中，`match`控制变量何时启用。如果生成的 CSS 选择器是一个字符串，它将被用来匹配这个算法。`selector`提供自定义返回值的选项。

比如我们对`hover:m-2`进行匹配，就会从用户中取出，发给所有变体进行匹配。然后它返回结果`m-2`，该结果将用于另一轮匹配变量。如果没有更多的变量被匹配，`m-2`然后去匹配规则，生成`.m-2 { margin: 0.5rem; }`。

最后，通过将`:hover`附加到`selector`钩子，将转换后的变量应用到生成的 CSS。结果是前面的 CSS:

```
.hover\:m-2:hover { margin: 0.5rem; }

```

`m-2`只有当用户将鼠标悬停在元素上时才有效。

## UnoCSS 性能

鉴于 UnoCSS 的灵活性，有理由怀疑它是否比其他 CSS 框架执行得更有效。UnoCSS 开发人员编写了一个简单的基准程序来比较性能，结果如下:

```
10/21/2021, 2:17:45 PM
1656 utilities | x50 runs
none                            8.75 ms /    0.00 ms 
unocss       v0.0.0            13.72 ms /    4.97 ms (x1.00)
windicss     v3.1.9           980.41 ms /  971.66 ms (x195.36)
tailwindcss  v3.0.0-alpha.1  1258.54 ms / 1249.79 ms (x251.28)

```

这个结果解释了 UnoCSS 可以比 Tailwind 的 JIT 和 Windi CSS 快 200 倍。大约零开销意味着您可以轻松地将 UnoCSS 集成到您预先存在的应用程序中，并使它与其他框架一起工作，而不会有任何性能损失。

## 结论

尽管它很新，但由于其简单性、灵活性和可靠性，UnoCSS 在开发人员社区中的声誉正在迅速增长。使用 UnoCSS，您可以构建像 TailwindCSS、WindiCSS、Tachyons 和您的自定义类库这样的框架。在本文中，我们讨论了开始使用 UnoCSS 的基础知识，回顾了它的优点和基本配置。如有疑问，一定要在下方留言评论！

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。