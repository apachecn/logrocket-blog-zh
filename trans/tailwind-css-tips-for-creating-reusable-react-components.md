# 创建可重用 React 组件的技巧

> 原文：<https://blog.logrocket.com/tailwind-css-tips-for-creating-reusable-react-components/>

我不会假装自己是一个好的设计师。但是我重视好的设计。Tailwind 是我发现的最好的工具，可以用来构建看起来很棒的定制用户界面。

在这篇博文中，我将分享一些我发现在用 Tailwind 构建可重用的 React 组件时非常有用的技巧。

## 什么是顺风，为什么要用顺风？

Tailwind 是一个实用程序优先的 CSS 库，它将作为类应用的 CSS 属性直接公开给你的标记。在它存在之前，我使用了另一个非常流行的 CSS UI 库。它承诺用最少的努力做出漂亮的设计。起初，我觉得像剪切和粘贴一样快速地扔纽扣、卡片和手风琴很有效率。只上了几节课，一个丑陋的纽扣就变成了漂亮的纽扣。

但是这些快捷方式 UI 库的问题是它们的组件基本上都是固定的。我第一次尝试用这个库设计一个定制的日期选择器时，我发现自己费力地阅读一个难以理解的 CSS 文件。我找到的解决办法是用`important!`来破解。

Tailwind 需要更多的前期工作，但是在没有完全掌握 CSS 的情况下构建一个定制组件要容易得多。这个过程的一部分是学习有效地使用顺风组件。

## 平面标记组件入门

构建好组件的第一个关键是不要构建任何组件。过早的抽象很难收回。除非您确定什么是正确的组件，否则最好从页面级别的平面标记开始。

顺风让这变得非常容易。您可以在一个文件中快速工作，并一起构建结构和样式。多个文件的上下文切换一开始就增加了很多摩擦。当您拆分组件时，您可以将它们保存在同一个文件中，直到您真正需要在其他地方使用该组件。只有在很难将它保存在原处时，才应该提取它。

## 如何选择正确的 API

使用组件时令人困惑的 API 会导致错误。现在为顺风实验室工作的 Robin Malfait 在一次不和谐的聊天中分享了他自己的“组件不成文的规则”。我在这里分享它们，因为我还没有看到更好的选择一个好的组件 API 的总结。

1.  **尽可能明确:**
    *   使用状态而不是布尔。`<Button primary disabled secondary active/>`有太多无效排列。在显式版本(`<Button state={Button.state.ACTIVE} variant={Button.variant.PRIMARY}/>`)中，没有主次之分，也没有激活和禁用之分
2.  `ClassName` **作为道具是码闻:**
    *   创建按钮的隐式版本。很难知道它处于什么状态
3.  **首选本地状态而非全局状态:**
    *   弄清楚状态是从哪里来的
4.  **喜欢道具胜过状态:**
    *   从道具中派生比与你的状态同步要好
5.  **宁要道具不要道具:**
    *   道具增加了组件可能处于的状态
6.  **更喜欢组件而不是道具:**
    *   `<Button big fullWidth/>`应该是`<BigButton/>`或者`<FullWidthButton/>`
7.  **喜欢孩子胜过道具:**
    *   `<Button iconRight={''}>Apple</Button>`应该是`<Button>Apple </Button>`

下面是我使用的用 TypeScript 编写的 badge 组件。这类似于 Robin 在这个[要点](https://gist.github.com/RobinMalfait/490a0560a7cfde985d435ad93f8094c5)中分享的一个例子。这个版本有四种颜色和两种尺寸。

```
import React, { ReactNode } from 'react';
import { classNames } from '../util/classNames';
enum Variant {
    RED,
    YELLOW,
    GREEN,
    BLUE,
}
enum Size {
    LARGE,
    SMALL,
}
type Props = {
    variant: Variant;
    children?: ReactNode;
    size: Size;
};
const SIZE_MAPS: Record<Size, string> = {
    [Size.SMALL]: 'px-2.5 text-xs',
    [Size.LARGE]: 'px-3 text-sm',
};
const VARIANT_MAPS: Record<Variant, string> = {
    [Variant.RED]: 'bg-red-100 text-red-800',
    [Variant.YELLOW]: 'bg-yellow-100 text-yellow-800',
    [Variant.GREEN]: 'bg-green-100 text-green-800',
    [Variant.BLUE]: 'bg-blue-100 text-blue-800',
};
export function Badge(props: Props) {
    const { children, variant, size } = props;
    return (
        <span
            className={classNames(
                'inline-flex items-center py-0.5 rounded-full font-medium leading-4 whitespace-no-wrap',
                VARIANT_MAPS[variant],
                SIZE_MAPS[size],
            )}
        >
            {children}
        </span>
    );
}
Badge.defaultProps = {
    variant: Variant.GRAY,
    size: Size.SMALL,
};
Badge.variant = Variant;
Badge.size = Size;

```

徽章只能处于枚举定义的八种状态之一:
`<Badge variant={Badge.variant.RED} size={Badge.size.BIG}>Text</Badge>`

下面显示了一个普通的 JavaScript 版本，它使用对象而不是枚举来映射不同的状态。

```
import React from 'react';
import { classNames } from '../util/classNames';
const SIZE_MAPS = {
    SMALL: 'px-2.5 text-xs',
    LARGE: 'px-3 text-sm',
};
const VARIANT_MAPS = {
    RED: 'bg-red-100 text-red-800',
    YELLOW: 'bg-yellow-100 text-yellow-800',
    GREEN: 'bg-green-100 text-green-800',
    BLUE: 'bg-blue-100 text-blue-800',
};
export function Badge(props) {
    const { children, variant, size } = props;
    return (
        <span
            className={classNames(
                'inline-flex items-center py-0.5 rounded-full font-medium leading-4 whitespace-no-wrap',
                VARIANT_MAPS[variant],
                SIZE_MAPS[size],
            )}
        >
            {children}
        </span>
    );
}
Badge.variant = VARIANT_MAPS;
Badge.size = SIZE_MAPS;

```

## 奖励:类名助手

在 React 中使用 Tailwind，尤其是构建可重用组件，通常需要有条件地组合类名字符串。大多数人用 npm 上的几个包来做这件事。这没什么错，但是为了防止再多一个依赖包的膨胀，Robin 分享了一个函数来做这件事。非常干净，不是火箭科学，但很方便。

```
export function classNames(...classes: (false | null | undefined | string)[]) {
  return classes.filter(Boolean).join(" ");
}
//usage example
<button className={classNames('this is always applied', 
        isTruthy && 'this only when the isTruthy is truthy', 
        active ? 'active classes' : 'inactive classes')}>Text</button>

```

## 顺风组件的类型

**非组件(模板或片段)**

有时你试图封装的是一个重复的模式，而不是一个组件。如果细节对于大多数实例来说是唯一的，并且不需要与其他实例保持链接，那么它实际上就是一个模板或片段。

一个很好的例子就是 Tailwind 发布的付费产品 Tailwind UI。这些片段可以剪切并粘贴到你的应用程序中，以加速原型制作。假设你发现它们在你的应用程序中的多个地方重复出现，并且有足够的一致性来明确 API 应该是什么，那么它们可能在你的代码中成为可重用的组件。否则，请将它们保存在代码段库中。

**小部件**

一个按钮是一个小组件的典型例子，它经常被顺风提取。下面的例子有 17 个实用程序类。你可能会发现在一个网站上有几十个按钮，它们会共享 17 个类中的 15 个。

```
<button type="button" class="inline-flex items-center px-2.5 py-1.5 border 
    border-transparent text-xs font-medium rounded shadow-sm text-white 
    bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 
    focus:ring-offset-2 focus:ring-indigo-500">
  Button text
</button>

```

在这种情况下，您可以使用 tailwind `@apply`指令将上面的公共类合并成一个新的`btn`类。我通常不这样做，因为对我来说，将大部分组件放在 React 的组件模型中是一个更简单的心理模型。

**小组件(暴露样式)**

在某些情况下，您可能希望创建一个组件来封装样式以外的内容。它可能是一个点击事件处理程序或一些其他行为。这是一个例外的“`classNames`作为道具”是一个代码的味道。在这里，最好使用`classNames`作为道具，将所有的顺风类放在组件上，而不是放在里面。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

**大型复杂构件**

包含多个元素的大型组件——如应用程序外壳、侧栏或标题——很容易被过度参数化。为了使它们尽可能可配置，你需要用道具来填充它们。但这使得阅读代码和了解应用程序外壳的每个版本的状态变得更加困难。

相反，我更倾向于 API 系列中的“组件优先于道具”和“子组件优先于组件”。他们通常开始时非常相似。但是，随着网站的发展和变化，每个实例往往会出现分歧，迫使您添加另一个道具来适应。

一旦它们被链接起来，保持它们的链接似乎总是更容易，直到你突然发现你有一个怪物组件。这里的教训是:当这些大型组件变得难以处理时，不要害怕将其分解。

## 结论

使用 React 是用组件构建现代应用程序的最佳方式之一。Tailwind 是我发现的最简单的方法来设计应用程序，而不需要对 CSS 有很高的掌握。这两者结合在一起，允许您构建外观和工作良好的可重用组件。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)