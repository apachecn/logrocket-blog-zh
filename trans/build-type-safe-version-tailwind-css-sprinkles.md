# 用 Sprinkles - LogRocket 博客构建你自己的类型安全版本的 Tailwind CSS

> 原文：<https://blog.logrocket.com/build-type-safe-version-tailwind-css-sprinkles/>

我是 CSS 库[香草提取物](https://github.com/seek-oss/vanilla-extract)的忠实粉丝；在我看来，它是自 Tailwind CSS 以来最令人兴奋的 CSS 工具。如果你是第一次接触[香草精华](https://blog.logrocket.com/vanilla-extract-tutorial-create-zero-runtime-stylesheets-in-typescript/)，它是一个由 Mark Dalgleish 创建的 CSS-in-JS 库，允许你为你的 CSS 利用 TypeScript 的力量。

我对香草精的杀手锏——Sprinkles API——感到特别兴奋。Sprinkles 允许您根据基本原则创建完全定制的设计系统。你可以使用 Sprinkles 来创建你自己的原子的、实用优先的 CSS 框架，而不是使用现成的库，例如 [Tailwind 和 Tachyon](https://blog.logrocket.com/tailwindcss-vs-tachyons/) 。

在本教程中，我们将设置一个新的 Next.js 应用程序，并使用 vanilla-extract 的 Sprinkles API 来创建一个自定义的、实用程序优先的 CSS 框架，称为 Sprinkles Tailwind。然后，我们将使用我们的新框架在 Tailwind CSS 主页上重新创建产品卡演示。在这个过程中，我们将看到 vanilla-extract 和 Sprinkles 如何处理伪类、响应式设计和黑暗模式主题。

![](img/2ced96058db692dbe7b4498e04f312c4.png)

### 内容

## 为什么要用糖粉？

那么，当我们可以简单地使用已经如此流行的工具 Tailwind 时，为什么还要开发我们自己的基于 Sprinkles 的、实用程序优先的 CSS 库呢？三个原因:类型安全、自定义和局部范围。

首先，使用 Sprinkles，您可以获得与任何其他 Typescript 变量相同的类型提示和编译时验证，不再需要文档验证和类名输入错误。

其次，Tailwind 既固执己见，又有一点学习曲线，尤其是如果你没有使用 Tailwind 预设语法的话。有了 Sprinkles，你写的是 CSS 而不是 Tailwind 的速记类名。因为 Sprinkles 没有任何内置的值，所以您可以按照自己喜欢的方式创建 CSS 框架。

最后，还有本地范围。带有 CSS 模块的香草香精的一个好处是，所有的实用程序类现在默认都是组件范围的。这意味着你可以轻松地将多个设计系统整合到一个应用中，而不用担心风格冲突。

让我们首先创建一个新的 Next 应用程序并安装 vanilla-extract。你可以在 [GitHub repo 这里](https://github.com/Shimin-Zhang/TailSprinkles)查看这个演示项目的完整源代码。

首先创建一个新的 Next 类型脚本版本，如下所示:

```
npx [email protected] --typescript sprinkles-tailwind

```

在运行`yarn dev`以确保我们的安装工作正常后，我们将安装香草精和 Sprinkles:

```
yarn add @vanilla-extract/css @vanilla-extract/babel-plugin @vanilla-extract/next-plugin @vanilla-extract/sprinkles

```

接下来，通过将`next.config.js`更新为以下内容，将 vanilla-extract/next-plugin 包含在我们的 webpack 构建过程中:

```
const {
  createVanillaExtractPlugin
} = require('@vanilla-extract/next-plugin');
const withVanillaExtract = createVanillaExtractPlugin();

/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
};

module.exports = withVanillaExtract(nextConfig);

```

最后，因为我们希望我们的新 Sprinkles 类型能够被 TypeScript 编译器和 IDE 的 TypeScript 服务器获取，所以我们将把`*.css.ts`添加到项目的`tsconfig.json`中:

```
{
    ...
    "include": ["next-env.d.ts", "**/*.ts", "**/*.css.ts", "**/*.tsx"],
    ...
}

```

## 创建一个带有点缀的设计系统

Tailwind CSS 的主要卖点之一是它如何迫使用户在定义良好的设计系统的约束下工作，而不是不一致的一次性值。

Tailwind 还预装了一个默认系统，为常见的布局需求提供了合理的值和合理的快捷方式。在我们的项目 Sprinkles Tailwind 中，我们将从 [Open Props](https://open-props.style/) 项目中获取调色板、排版和尺寸比例。我们还将为布局添加一些常见的顺风值。

首先，我们将在`./styles`目录下创建一个`colors.css.ts`文件，并定义我们的颜色值:

```
const palette = {

'gray-0': '#f8f9fa',

'gray-1': '#f1f3f5',
... 

'gray-9': '#212529',

'red-0': '#fff5f5',

... /* See https://github.com/Shimin-Zhang/TailSprinkles/blob/main/styles/colors.css.ts for full palette */

};

export default palette;

```

我们将对`border.css.ts`、`size.css.ts`、`typography.css.ts`进行同样的操作，以建立我们设计系统的基础。

一旦我们建立了我们的设计系统值，我们将像这样定义我们的 Sprinkles 系统:

```
import {
  defineProperties,
  createSprinkles,
} from '@vanilla-extract/sprinkles';

import palette from './colors.css';
import { fontFamily, fontWeight, lineHeight, fontSize } from './typography.css';
import { size, space } from './size.css';
import { borderSize, borderRadius } from './border.css';

const responsiveProperties = defineProperties({
  properties: {
    display: ['none', 'flex', 'block', 'inline'],
    flexDirection: ['row', 'column'],
    justifyContent: [
      'stretch',
      'flex-start',
      'center',
      'flex-end',
      'space-around',
      'space-between'
    ],
    alignItems: [
      'stretch',
      'flex-start',
      'center',
      'flex-end'
    ],
    paddingTop: space,
    paddingBottom: space,
    paddingLeft: space,
    paddingRight: space,
...
  },
  shorthands: {
    padding: [
      'paddingTop',
      'paddingBottom',
      'paddingLeft',
      'paddingRight'
    ],
    paddingX: ['paddingLeft', 'paddingRight'],
    paddingY: ['paddingTop', 'paddingBottom'],
...
    placeItems: ['justifyContent', 'alignItems']
  }
});

const systemProperties = defineProperties({
  properties: {
    color: palette,
    background: palette,
    fontFamily: fontFamily,
    fontSize: fontSize,
    ...
  }
});

export const sprinkles = createSprinkles(
  responsiveProperties,
  systemProperties
);

export type Sprinkles = Parameters<typeof sprinkles>[0];

```

Sprinkles 的`defineProperties`函数将我们的设计系统令牌转换成一组样式值。然后通过`createSprinkles`将这些值组合起来，这样用户就可以以 CSS-in-JS 的方式使用它们。

请注意，我们将我们的属性分为两个独立的类别。`responsiveProperties`是我们稍后将以类似于顺风的视口前缀(即`md:`)的响应方式进行样式化的对象。系统属性将取决于主题，类似于顺风的`dark:`前缀。然后，我们导出生成原子类的 Sprinkles 对象及其类型，这为我们提供了类型安全性。

`shorthands`键允许我们定义 CSS 速记值，类似于 CSS 速记和 Tailwind `px-*, p-*`类。

接下来，我们将创建一个新页面并导入新的类型安全实用程序框架:

```
import Head from 'next/head'
import { sprinkles } from '../styles/sprinkles.css';

export default function Colors() {
    return (
        <div>
            <Head>
            </Head>
            <h1 className={
              sprinkles({
                ... styles go in here
              })
            }>
                Welcome To Colors
            </h1>
        </div>
    );
}

```

## 将 Sprinkles 库与 Tailwind CSS 进行比较

让我们看看 Sprinkles 的安全类型的行动。首先，我们将尝试添加一个有效的 CSS 属性`backgroundColor`，它没有在我们的 Sprinkles 应用程序中定义。我们看到它抛出了一个类型错误:

![Error message when user inputs an unknown style prop](img/611954174b459e5b2eec3bc9a064a738.png)

接下来，我们添加`color`键，看到有效值列表显示为建议:

![Auto-complete CSS values](img/522d25c4ce3bc2dcf25c82fb1f19724c.png)

正如所料，使用不在系统中的颜色值会立即引发类型错误:

![Error message when user inputs an unknown style value](img/05c2dabc51e43877b5c67f54b22dbb5e.png)

这比在文档中查找正确的 Tailwind 类名，输入错误，然后等待浏览器刷新才注意到它要快得多。

设计系统设置完毕后，我们现在可以从 Tailwind 主页获取产品卡示例，复制示例 HTML，并将其翻译到我们新的 Sprinkles Tailwind 框架中:

```
<h1>
  Tailwind Sprinkles
</h1>
<div 
className = {
  st({
    display: 'flex',
    fontFamily: 'sans-serif',
    width: 'size-content-3',
    marginY: 'size-auto',
    background: 'white',
    borderRadius: 'radius-3',
    boxShadow: 'shadow-2'
  })
}>
  <div 
    className = {
      st({
        flex: 'none',
        width: 'size-12',
        height: 'size-13',
        position: 'relative',
      })
    }
    >
    <img src="/jacket.jpg" alt="" 
    loading="lazy" className = {
      st({
        position: 'absolute',
        width: 'full',
        height: 'full',
        objectFit: 'cover',
        inset: '0'
      })}/>
  </div>
    ... 
    /* See https://github.com/Shimin-Zhang/TailSprinkles/blob/main/pages/index.tsx for full file*/

```

因为我们已经在`sprinkles.css.ts`文件中定义了我们的缩写，所以我们能够在我们的样式中使用`marginY`,而不是分别编写`marginTop`和`marginBottom`。

我们在设计产品卡时遇到了一些问题，仅靠 Sprinkles 无法解决。Sprinkles 是普通 CSS 属性之上的最小抽象层，因此我们无法访问像`srOnly`和伪 select `:hover`这样的实用程序类来指示我们的按钮是可点击的。

我们需要引入香草精来创造更复杂的风格:

```
// First we import vanilla-extract
import { style } from '@vanilla-extract/css';

// We create more complex classes
export const srOnly = style({
  position: 'absolute',
  width: '1px',
  height: '1px',
  padding: '0',
  margin: '-1px',
  overflow: 'hidden',
  clip: 'rect(0, 0, 0, 0)',
  whiteSpace: 'nowrap',
  borderWidth: '0'
});

export const clickable = style ({
  ':hover': {
    cursor: 'pointer'
  }
})

```

这需要一些配置，但我们成功地复制了相同的夹克产品卡！虽然这不是一个精确的复制(我们的设计系统是基于开放的道具)，但它是一个合理的复制，抓住了原始设计的精神:

![Product card replication with Sprinkles](img/3709b4a9466cefd8f97bdd156429dab4.png)

仔细看看生成的 HTML，我们看到 Sprinkles 根据我们的设计标记生成了带前缀的实用程序类名。请注意，每个类都通过 CSS 模块作用于组件:

![A chrome devtools panel showing encapsulated class names](img/dee152a3b7ee7fa1dfb9eaef3e992dd8.png)

## 实现主题和响应式设计

我们的卡在桌面上看起来很棒，但是当我们缩小视窗时，它没有反应。为了解决这个问题，我们将在 Sprinkles 设置中添加基于视口的条件。还记得之前我们将布局属性命名为`responsiveProperties`吗？我们现在将添加视口信息:

```
const responsiveProperties = defineProperties({
  conditions: {
    mobile: {},
    tablet: { '@media': 'screen and (min-width: 768px)' },
  },
  defaultCondition: 'tablet',
  properties: {
    ... same as before

```

因为我们从桌面变体开始，所以我们无法从移动优先的设计开始，而将默认为平板电脑视图。响应式风格很容易用糖屑创造出来；我们将使用`property: { condition-name: value }`语法，而不是我们目前看到的`property: value`格式。

以下是附加移动视口布局的组件外观:

```
        <div 
        className = {
          st({
            display: 'flex',
            fontFamily: 'sans-serif',
            width: {
              tablet: 'size-content-3',
              mobile: 'size-auto'
            },
            marginY: 'size-auto',
            marginX: {
              tablet: 'size-auto',
              mobile: 'size-3',
            },
            background: 'white',
            borderRadius: 'radius-3',
            boxShadow: 'shadow-2'
          })
        }>

```

在添加了其余的移动视口布局后，我们的卡现在可以响应了:

![Mobile view-port card](img/247be2b9fc3d3123b70b6320bf3315e8.png)

我们的产品卡真的是走到一起了！

## 添加黑色主题

最近，我们通常需要组件的最后一个特性是在不同主题之间切换的能力。让我们给我们的产品卡添加一个黑色主题，来演示如何使用 Sprinkles。

与响应式设计一样，我们首先向系统属性添加一个条件和一个默认值:

```
const systemProperties = defineProperties({
  conditions: {
    lightMode: {},
    darkMode: { '@media': '(prefers-color-scheme: dark)' }
  },
  defaultCondition: 'lightMode',

```

与我们的响应式规模调整不同，这里默认的`lightMode`已经是我们想要的了。我们只需要在需要的时候加入黑暗主题的变化。和 responsive props 一样，我们将使用条件名来区分这两个主题。

之前的响应式设计和黑暗模式的`<div>`现在看起来是这样的:

```
<div 
className = {
  st({
    display: 'flex',
    fontFamily: 'sans-serif',
    width: {
      tablet: 'size-content-3',
      mobile: 'size-auto'
    },
    marginY: 'size-auto',
    marginX: {
      tablet: 'size-auto',
      mobile: 'size-3',
    },
    background: {
     lightMode: 'white',
     darkMode: 'grape-9',
    },
    borderRadius: 'radius-3',
    boxShadow: 'shadow-2'
  })
}>

```

现在，我们最终的黑色主题产品卡看起来像这样:

![Dark theme product card](img/d138c7e512e18a841aebc8e78b4a25c4.png)

## 结论

我希望这篇文章已经说服你，下次当你开始一个新项目，并在市场上寻找一个新的 CSS 框架时，尝试一下香草香精。

就像 Tailwind CSS 一样，Sprinkles 框架允许您创建一个以设计系统为基础的实用程序优先的 CSS 框架。

与 Tailwind CSS 不同，使用 Sprinkles，您可以获得与其他 TypeScript 文件相同的类型安全保证，您可以按照自己想要的方式完全自定义它，并且可以获得 CSS 模块的范围安全。

我非常看好 vanilla-extract 引入的类型安全 CSS 范式。请在下面的评论区分享你从 Tailwind CSS 迁移到 Sprinkles 的想法和经验。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。