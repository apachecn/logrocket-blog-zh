# 用顺风 CSS 创建自定义主题

> 原文：<https://blog.logrocket.com/creating-custom-themes-tailwind-css/>

为你的网站提供不同的主题有很大的吸引力。它不仅允许你通过点击一个按钮来改变网站的整体外观，而且它也成为了一个重要的辅助功能。

使用 [Tailwind CSS](https://blog.logrocket.com/theming-react-components-tailwind-css/) 有多种方式来创建定制主题，这是一个实用的 CSS 框架。在本文中，我们将介绍实现替换主题的各种方法以及每种方法的优缺点。

## 使用插件主题化顺风 CSS

主题化你的顺风 CSS 网站最简单的方法是在一个模式中使用你的颜色(主题),并启用一个插件如 [Nightwind](https://nightwindcss.com/) 来反转它。例如，Nightwind 会对你的调色板进行映射，使光线模式下的刻度颜色`500`变成黑暗模式下的`400`，或者一种模式下的`bg-red-900`变成另一种模式下的`bg-red-50`。

为了防止 Nightwind 中的这种着色反转，您可以使用实用程序类`nightwind-prevent`，该类禁用 Nightwind 对元素的效果，而不影响其后代节点。另一方面，使用`nightwind-prevent-block`也涵盖了后代。

您可以在此查看一个活生生的例子:

> 一个为 Tailwind CSS 开发的高级在线平台，让你可以直接在浏览器中使用 Tailwind 的所有构建时特性。

### 赞成使用夜风主题

*   需要最少努力的自动化过程
*   某些插件效果通常可以被覆盖

### 使用夜风的缺点

*   太确定了。例如，如果你使用`bg-white`作为网站背景，它的黑暗模式变体是令人不愉快的漆黑

### 夜风的替代品:

## 使用内置的黑暗模式来设置顺风 CSS 的样式

以`variant`的形式，Tailwind 提供了一个主题化 API，它提供了我们将在本文中讨论的所有方法中最强的控制。默认情况下，`dark`变量只对与颜色相关的类启用，比如文本、背景和边框颜色。

```
<p class="text-black dark:text-white">The quick brown fox jumps over the lazy dog</p>
```

使用这个标记，`dark:text-white`类将在启用黑暗模式时将段落变白。这是一种比使用插件更加明确和强大的主题化方法。

使用这个 API，你可以创建一个在黑暗和光明模式下看起来完全不同的站点。这里，我们根据主题展示了不同的图像:

```
<div>
  <img class="dark:hidden" src="./sun.png" alt="the sun">
  <img src="./moon.png" alt="the moon">
</div>
```

[这是 Tailwind](https://twitter.com/quarterdeck/status/1350416361701470210) 中黑暗模式 API 背后的许多可能性的主要例子，当处于黑暗模式时，网站的整个外观和感觉都会改变，而不仅仅是反转颜色:

![](img/8776329fc5ade04bb37f91add40ec862.png)

## 使用顺风 CSS 启用黑暗模式

Tailwind 支持两种黑暗模式变体策略:`media`和`class`。

```
// tailwind.config.js
module.exports = {
  darkMode: 'media', // or 'class'
}
```

`media`策略通过查询 [`prefer-color-scheme`](https://blog.logrocket.com/new-media-queries-you-need-to-know/#preferscolorscheme) 媒体查询，从用户自己的操作系统设置中为黑暗模式选择当前主题，生成如下所示的 CSS:

```
@media (prefers-color-scheme: dark) {
  .dark\:bg-white {
    background-color: white;
  }
}
```

使用这种策略，用户只能通过在操作系统级别上切换黑暗模式偏好来切换主题。

这就是`class`策略的用武之地。它通过使用 CSS 作用于类名`dark`来启用黑暗模式，从而为您提供对应用程序当前主题的更多控制。这意味着黑暗模式将为任何具有`dark`类的元素的后代启用:

```
.dark .dark\:bg-white {
  background-color: white;
}
```

```
<div>
  <!-- the text will be black -->
  <p class="text-black dark:text-white"></p>
</div>

<div class="dark">
  <!-- the text will be white -->
  <p class="text-black dark:text-white"></p>
</div>
```

通常，`dark`类进入根`html`标签来启用或禁用整个应用程序的黑暗模式。然而，通过使用`class`变体，默认用户操作系统偏好的便利性不再是自动的。

我们可以通过在`html`标签上硬编码(或排除)`dark`类来为所有用户选择一个默认主题，但更动态的方法是将当前主题存储在`localStorage`中，并使用操作系统偏好作为后备。

为此，我们可以使用以下 JavaScript 代码:

```
const isDarkSet = localStorage.theme === 'dark';
const isThemeStored = 'theme' in localStorage;
const isDarkPrefered = window.matchMedia('(prefers-color-scheme: dark)').matches;

if (isDarkThemeSet || (!isThemeStored && isDarkPrefered)) {
  document.documentElement.classList.add('dark')
} else {
  document.documentElement.classList.remove('dark')
}
```

这需要尽可能早地放在文档中，以避免[闪现未样式化的内容](https://en.wikipedia.org/wiki/Flash_of_unstyled_content) (FOUC)，其中站点在页面变得可见后改变其 CSS。

为此，我们可以在`index.html`文件中的`html`标签下的`script`标签中添加这段代码。例如，一些 web 框架，如 Next.js，不直接公开`index.html`文件。接下来，代码必须放在`[pages/_document.js](https://nextjs.org/docs/advanced-features/custom-document)`里面。

之后，要启用或禁用黑暗模式，我们可以切换`dark`类并更新`localStorage`:

```
const themeSwitch = document.querySelector('.switch')

themeSwitch.addEventListener('click', () => {
  document.documentElement.classList.toggle('dark')
  localStorage.theme = localStorage.theme === 'dark' ? 'light' : 'dark'
})

```

> 一个为 Tailwind CSS 开发的高级在线平台，让你可以直接在浏览器中使用 Tailwind 的所有构建时特性。

### 在顺风 CSS 中启用黑暗模式的好处

*   主题化影响的不仅仅是颜色。它可以影响资产、布局等。

### 骗局

*   变得冗长，因为标记中有更多的实用程序类
*   限于两个主题

## 通过 CSS 自定义属性使用顺风 CSS 进行多主题化

CSS 自定义属性的主要用途之一是创建主题。在 Tailwind 中，使用自定义属性不像使用普通 CSS 那样简单，这是因为它是在幕后组成的。

例如，支持边框颜色实用程序是毫不费力的，因为在`border`简写旁边有一个手写的`border-color`属性。但是，支持文本不透明度并不那么简单，因为 CSS 不提供文本不透明度属性。

Tailwind CSS 使用 CSS 自定义属性来规避这些限制。为了使文本不透明度实用程序类成为可能，文本颜色类是这样编写的:

```
.text-black {
  --text-opacity: 1;
  color: rgba(0, 0, 0, var(--text-opacity));
}
```

使用这个，可以通过改变`--text-opacity`自定义属性来改变元素的文本不透明度:

```
.text-opacity-50 {
  --text-opacity: 0.5;
}
```

将这两个类组合在一个元素中——`text-black text-opacity-50`——将得到预期的半透明文本。

### 在顺风 CSS 中扩展配置

与使用真实颜色扩展 Tailwind 的默认调色板不同，Tailwind 无法访问自定义属性背后的值来正确添加中间自定义属性:

```
module.exports = {
  theme: {
    extend: {
      colors: {
        white: '#ffffff',
        variable: 'var(--white-color)',
      },
    },
  },
}
```

顺风可以将十六进制颜色转换成 T2 颜色。对于`variable`颜色就不一样了。

幸运的是，Tailwind 通过使用颜色函数而不是字符串来支持添加中间自定义属性:

```
module.exports = {
  theme: {
    extend: {
      colors: {
        variable: ({ opacityValue }) =>
          opacityValue
            ? `rgba(var(--white-color), ${opacityValue})`
            : `rgb(var(--white-color))`,
      },
    },
  },
}
```

该函数接收`opacityValue`作为参数，我们可以用它来创建颜色实用程序类。这意味着我们必须使用`RGB`值来定义我们的自定义属性。

### 使用自定义属性创建主题

要使用自定义属性创建主题，我们可以使用调色板扩展`base`样式:

```
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --bg-primary: 255, 255, 255;
    --bg-secondary: 245, 246, 247;
    --bg-tertiary: 238, 238, 238;

    --text-primary: 0, 0, 0;
    --text-secondary: 56, 63, 74;
    --text-tertiary: 255, 255, 255;
  }
}
```

在我们的`tailwind.config.js`文件中，我们可以使用`generateColorClass`函数将它们注册为新颜色:

```
const generateColorClass = (variable) => {
  return ({ opacityValue }) =>
    opacityValue
      ? `rgba(var(--${variable}), ${opacityValue})`
      : `rgb(var(--${variable}))`
}

const textColor = {
  primary: generateColorClass('text-primary'),
  secondary: generateColorClass('text-secondary'),
  tertiary: generateColorClass('text-tertiary'),
}

const backgroundColor = {
  primary: generateColorClass('bg-primary'),
  secondary: generateColorClass('bg-secondary'),
  tertiary: generateColorClass('bg-tertiary'),
}

module.exports = {
  theme: {
    extend: {
      textColor,
      backgroundColor,
    },
  },
}

```

这将为`text`和`background`颜色属性创建`primary`、`secondary`和`tertiary`实用程序类。

为了提供伴随这些实用程序类的多个主题，我们可以更新我们的`base`样式:

```
@layer base {
  :root {
    --bg-primary: 255, 255, 255;
    --bg-secondary: 245, 246, 247;
    --bg-tertiary: 238, 238, 238;

    --text-primary: 0, 0, 0;
    --text-secondary: 56, 63, 74;
    --text-tertiary: 255, 255, 255;
  }

  .dark {
    /*
    * update all colors:
    * --bg-primary: ...
    */
  }

  .coffee {
    /*
    * update all colors:
    * --bg-primary: ...
    */
  }
}
```

和前面的方法很相似，我们可以通过在根`html`元素上有一个`dark`或`coffee`类来启用这些主题。

> 一个为 Tailwind CSS 开发的高级在线平台，让你可以直接在浏览器中使用 Tailwind 的所有构建时特性。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 使用 CSS 自定义属性创建主题的优点

*   您可以创建无限数量的主题
*   与内置的黑暗模式变体方法相比，提供了一种自动和轻松的方法，因为内置的黑暗模式将创建更多的实用程序类

### 使用自定义属性的缺点

*   您不能轻易覆盖自定义属性

## 结论

在本文中，我们介绍了如何使用插件、内置的黑暗模式变体和 CSS 自定义属性，使用 Tailwind CSS 对您的应用程序进行主题化。每种方法在使用前都有利弊要考虑，其中一种方法可能最大限度地控制，而另一种方法则优先考虑易用性。感谢阅读。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。