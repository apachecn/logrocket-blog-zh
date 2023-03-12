# 比较顶级零运行时 CSS-in-JS 库

> 原文：<https://blog.logrocket.com/comparing-the-top-zero-runtime-css-in-js-libraries/>

许多 web 开发人员在 GitHub 上搜索一个资源库来为其做出贡献。当他们找到一个令人兴奋的项目并开始做贡献时，他们面临的一个常见挑战是定位特定组件的风格定义在哪里，尤其是在一个更大的项目中。

幸运的是，有一个简单的解决方案:web 开发人员应该在同一个文件中定义组件和样式。我们可以使用 CSS-in-JS 语法技术在同一个文件中编写组件和样式，而不会太复杂。

根据 styled-components 的创建者马克斯·斯托伊伯的说法，超过 60%的 React 安装都会安装 CSS-in-JS 库。用 JS 编写 CSS 非常流行，尤其是在使用 React 或 Angular 这样的 JS 框架时。许多库可以简化你编写 CSS-in-JS 的过程。

## 什么是 CSS-in-JS？

那么，CSS-in-JS 到底是什么？这是一种样式化技术，其中 [JavaScript 用于样式化组件](https://blog.logrocket.com/5-things-you-can-do-in-css-in-js-that-you-didnt-know-about-c422fb67ceb6/)。我们使用变量来定义组件的 CSS。变量将包含所有的 CSS 属性，并且内容将被包装在组件中，因此它也将包含所有已定义的 CSS 属性。

随着基于组件的样式变得越来越流行，CSS-in-JS 的使用近来有所增加。此外，大多数现代 JavaScript 框架都是基于组件的，这推动了 CSS-in-JS 的使用。正因为如此，CSS 现在已经成为 JavaScript 的一个模块，可以在任何需要的时候定义和使用。

在 React 这样的现代框架中，您可以使用内联技术在您的 JSX 部分编写 CSS。用于编写 CSS-in-js 的 JS 文件。但是这种技术可能会令人困惑，可读性差，并且会中断代码流。在通过库编写 CSS-in-JS 时，它不能取代 CSS 的模块化。

## 使用 CSS-in-JS

当我们使用 CSS-in-JS 时，我们在一个变量中定义样式，通过将组件包装在变量标签中，我们可以用它来样式化组件。

`styled`标签从库中导入。它创建了一个已经定义了一些样式的 React 组件。它后面是您想要使用的 HTML 标记名——在下面的例子中，我们使用的是`h1`。`h1`标签将根据定义的 CSS 属性进行定制。编码完成后，我们定义属性，如下所示:

```
const Title = styled.h1`
  font-family: sans-serif;
  font-size: 48px;
  color: #f15f79;
`;

```

接下来，我们将内容包装在变量标签中。

```
const App = () => {
     return(
          <Title>
               Hello world!
          </Title>         
)
;

```

维奥拉。这就是在大多数 CSS-in-JS 库中定义样式的方式。现在我们已经介绍了基础知识，让我们看看使用 CSS-in-JS 的一些优点。

## 使用 CSS-in-JS 的优势

*   **简单的代码共享**:共享代码将变得更加容易和高效，因为其他人可以很容易地理解代码，而不是试图在项目中寻找组件和样式
*   DOM 上的负载更少:因为我们在同一个文件中定义 CSS 和组件，组件的 CSS 只有在组件加载时才会加载，减少了虚拟 DOM 上不必要的负载
*   **在 CSS 中使用 JavaScript**:CSS 是在 JavaScript 文件中定义的，这意味着我们可以使用复杂的 JavaScript 逻辑来定义 CSS 的属性值
*   CSS 中更好的错误处理:因为 CSS 也经历编译过程，我们会在编译过程中收到错误信息，这使得在 CSS 中发现和解决错误变得容易
*   可移植性:将样式和组件放在同一个文件中使得在其他项目中使用组件变得容易

## 使用 CSS-in-JS 的缺点

使用 CSS-in-JS 有一些缺点。其中一些是:

*   因为样式是在`.js`文件中定义的，如果 JavaScript 被禁用，它将影响组件的样式
*   样式被双重解析，一次由库解析，然后在插入样式时由浏览器解析
*   传统上，当我们加载网页时，浏览器只是读取 CSS 并应用它。使用 CSS-in-JS 时，浏览器会动态生成一个 CSS 样式标签，然后读取并将其应用到网页。读取和生成该文件会耗费性能时间

## 使用零运行时 CSS-in-JS

改善由于双重解析而损失的性能时间的一个解决方案是，库应该将 CSS-in-JS 块转换成一个单独的。首先是 css 文件。然后，浏览器将读取这些样式并将其应用于网页，最终节省生成样式标签时通常会浪费的运行时间。这被称为零运行时 CSS-in-JS。对于性能至关重要的大规模或复杂的项目，它尤其有用。

> 零运行时意味着你用 CSS-in-JS 语法创作你的风格，但是产生的是 CSS，就像其他预处理程序一样。
> –[CSS-Tricks](https://css-tricks.com/the-unseen-performance-costs-of-modern-css-in-js-libraries/)

为了实现零运行时 CSS-in-JS，您可以利用各种库。这里有一些受欢迎的可以考虑。

## 卢纳里亚

![Linaria Syntax Logo On A Pink Background](img/328e3824e79a9d94aed18b5451a5f7ba.png)

Linaria 是最流行的零运行时库，它是用 TypeScript 编写的。拥有超过 7.1K 的 GitHub stars 和 260 个 GitHub forks。这是我个人最喜欢使用的，因为它具有易于使用的语法，并且与几乎所有现代框架兼容。Linaria 将 CSS-in-JS 转换成一个单独的。css 文件，同时创建生产版本。

它提供了许多功能，包括:

*   **基于动态属性的样式**:你可以使用一个属性来定义 CSS 属性的值
*   CSS SourceMap :在大型项目中，很难找到为组件定义样式的地方。如果 CSS SourceMap 是在编写 CSS 时定义的`true`，它将在 dev tools 中显示生成的 CSS 的类名的来源
*   林挺 CSS : [stylelint](https://stylelint.io/) 将帮助你避免任何 CSS 错误，并在你的风格中加强惯例
*   **JavaScript for logic** :你可以在编写 CSS 的时候使用 JavaScript 逻辑

下面是如何在你的机器上安装它。

```
npm install @linaria/core @linaria/react @linaria/babel-preset @linaria/shaker
import { css } from '@linaria/core';
import { modularScale, hiDPI } from 'polished';
import fonts from './fonts';

// Write your styles in `css` tag
const header = css`
  text-transform: uppercase;
  font-family: ${fonts.heading};
  font-size: ${modularScale(2)};

  ${hiDPI(1.5)} {
    font-size: ${modularScale(2.5)};
  }
`;

// Then use it as a class name
<h1 className={header}>Hello world</h1>;

```

### 使用 Linaria 的缺点

*   难以实现:正确实现 Linaria 可能会令人困惑，因为它需要建立巴别塔
*   **设置捆绑器**:从 JS 文件中提取 CSS 需要使用捆绑器，比如 [Rollup](https://rollupjs.org/guide/en/) 或者 [webpack](https://webpack.js.org/) ，这可能很难设置
*   **插件支持** : Linaria 不为 Rust 提供高质量的插件支持

## 阿斯特罗特夫尼龙草皮

![Astroturf Written On A Brown Card With A Green Background ](img/c04a82b237bf36b43e5ff34fa471b87f.png)

[Astroturf](https://github.com/4Catalyzer/astroturf) 由 4Catalyzer 开发，在 GitHub 上拥有超过 2000 颗星星。这是 Linaria 的绝佳替代品。Astroturf 通过保持 CSS 完全静态，没有运行时解析，帮助您实现零运行时。您可以通过 Astroturf 的作用域样式表 React 以及使用 props 和组件变体以多种方式编写 CSS-in-JS。你可以在这里看到[的实现](https://4catalyzer.github.io/astroturf/introduction)。

主要功能包括:

*   灵活性:有些框架只能处理特定的 CSS，但是 Astroturf 是灵活的，可以与大多数框架兼容
*   **道具支持**:支持道具特性，根据道具对样式组件做出反应
*   **使用您现有的工具**:您可以使用 [Sass](https://sass-lang.com/) 和 [PostCSS](https://postcss.org/) 在 JavaScript 中编写样式定义

以下是开始的方法。

```
npm i astroturf
import React from "react";
import { css } from "astroturf";
const btn = css`
  color: black;
  border: 1px solid black;
  background-color: white;
`;
export default function Button({ children }) {
  return <button className={btn}>{children}</button>;
}

```

### Astroturf 的缺点

*   糟糕的文档:它在 GitHub 上缺少一个合适的自述文件。它还缺乏投稿指南，文档也很短，经常遗漏重要的细节
*   **实现**:为了提取样式，Astroturf 需要捆绑一个 CSS 解析器，比如 rollup 或 webpack，这可能很难实现

## 雷沙多

![Reshadow Website](img/ce52f8a59da3210ae4501115d3cc2073.png)

Artur Kenzhaev 开发的 Reshadow ，在 GitHub 上有 340+颗星。它是用 JavaScript 编写的，并提供了许多功能，如虚拟 DOM-like React 的影子 DOM 开发者体验，其中一项是编写 CSS-in-JS。

Reshadow 提供以下福利:

*   PostCSS 的好处:你将拥有 PostCSS 的所有好处来自动化日常 CSS 操作
*   **静态样式** : Reshadow 提供了一个静态样式提取选项，将所有的 CSS-in-JS 提取到一个单独的。css 文件
*   CSS 模块:你可以使用 CSS 模块来定义 CSS，就像我们在 JavaScript 中做的一样

安装如下:

```
npm install --save reshadow
import {css} from 'reshadow'

const styles = css`
    button {
        font-size: 16px;
        cursor: pointer;
        padding: 10px 15px;
        border-radius: 20px;
        border: 2px solid;
        background-color: white;
        color: darkorange;
    }
`

const Button = ({children, ...props}) => styled(styles)(
    <button {...props}>
        {children}
    </button>,
);

```

### Reshadow 的缺点

*   **糟糕的文档和内容**:网上关于 Reshadow 的内容很少，文档也不够全面，所以学习 Reshadow 需要时间，而且可能很困难
*   贡献者更少:Reshadow 的贡献者很少，这阻碍了解决 GitHub 库相关问题的进程

## 结论

零运行时 CSS-in-JS 提供了许多好处，尤其是当开发人员参与多个大型项目时。CSS-in-JS 的前景非常光明，尤其是在 web 开发和现代框架方面。我希望当你开始你的下一个项目时，你会添加这些库之一。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)