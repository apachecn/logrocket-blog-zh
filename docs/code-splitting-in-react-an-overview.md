# React 中的代码拆分:概述

> 原文：<https://blog.logrocket.com/code-splitting-in-react-an-overview/>

尽管 JavaScript 是一种简单的语言，但它可以生成复杂得惊人的代码库。部分原因是有各种各样的类和模块可用。大多数实质性的 JavaScript 程序和框架都有许多依赖关系，这可能会使一个看似简单的项目快速积累大量代码。

项目的代码越多，浏览器的加载速度就越慢。因此，您经常需要平衡依赖项的大小和您期望 JavaScript 的性能。代码分割是达到这种平衡的有效方法。

## 什么是代码拆分？

许多 JavaScript 框架[将所有依赖关系](https://reactjs.org/docs/code-splitting.html)捆绑到一个大文件中。这使得将 JavaScript 添加到 HTML 页面变得很容易。这个包只需要一个链接标签，而且设置页面所需的调用也更少，因为所有的 JavaScript 都在一个地方。理论上，以这种方式捆绑 JavaScript 应该可以加快页面加载速度，降低页面需要处理的流量。

然而，在某一点上，一个包增长到一定的大小，解释和执行代码的开销减缓了页面的加载，而不是加快了页面的加载。这个临界点对于每个页面都是不同的，你应该测试你的页面，找出这个临界点在哪里。没有一个通用的指导方针——它完全依赖于被加载的依赖项。

代码分割的关键是找出页面的哪些部分需要使用不同的 JavaScript 依赖项。代码分割允许您有策略地从包中省略某些依赖项，然后只在需要的地方插入它们。这意味着它们只有在需要的时候才会被加载——只有在需要的时候才加载 JavaScript，这加快了页面的加载时间。

这乍一看似乎违反直觉，因为页面仍然加载相同数量的代码，但这种差异是因为页面可能不会执行它加载的所有代码。例如，如果页面中的动态元素需要很大的依赖关系，那么这种依赖关系可能会导致明显的页面延迟。然而，如果仅在使用动态元素时加载依赖性，则依赖性可能足够小，不会产生滞后。

代码拆分在大型 React 应用程序中是一种常见的做法，它提供的速度提升可以决定用户是继续使用 web 应用程序还是离开。许多研究表明，网页留给用户的印象只有不到三秒钟，因此，哪怕只剩下不到一秒钟的时间也意义重大。因此，以三秒或更少的加载时间为目标是理想的。

## React 中的代码拆分是如何工作的？

在 React 中实现代码拆分有几种方法。不同的捆绑器以不同的方式工作，但是 React 有多种方法来定制捆绑，而不管使用什么捆绑器。

### 动态导入

也许在 React 中分割代码最简单的方法是使用动态“导入”语法。有些绑定器可以本地解析动态导入语句，而有些则需要一些配置。动态导入语法适用于静态站点生成和服务器端呈现。

动态导入使用 **`then`** 函数只导入需要的代码。对导入代码的任何调用都必须在该函数内部。

```
import("./parseText").then(parseText => {
  console.log(parseText.count("This is a text string", "text"));
});

```

### 使用 React.lazy

React.lazy 允许在许多上下文中延迟加载导入。它还不能用于服务器端渲染，但其功能的多样性弥补了这一点。React.lazy 函数允许您动态导入一个依赖项，并在一行代码中将该依赖项呈现为一个组件。如果动态导入失败，必须在另一个显示回退内容的组件中呈现该组件。

这可以包括错误内容，尽管这不是必需的。如果包含错误内容，它必须包含在称为错误边界的特殊类型的组件中。错误边界组件必须位于任何动态加载的组件之上，以确保它们正确显示。

```
import React, { Suspense } from 'react';

const comp1 = React.lazy(() => import('./comp1'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <comp1 />
      </Suspense>
    </div>
  );
}

```

### 选择分割点

React 中代码拆分的目标是尽可能给用户最好的动态体验。这使得在代码中选择动态加载依赖项的位置成为一项重要的任务。

React 提供了一个很好的默认起点，因为用户希望在页面之间切换时有轻微的延迟。路由上的动态代码分割通常包含一些自身的依赖性，但是由于用户经常期望某种形式的页面转换，所以在加载期间有目的的延迟可能是有用的。

## 代码分割如何与组件块一起工作？

React 还允许您基于组件而不是原始功能来拆分代码。有时，开发人员会有一组 React 组件来创建他们页面的关键部分，他们会多次使用同一组组件。当发生这种情况时，每次需要组件时延迟加载组件的依赖关系会降低页面速度。

React-loadable 函数使您能够创建一个定制的加载器，该加载器只需动态导入一次所需的代码块。将动态导入包装在加载器中可以防止它们被包含在页面加载包中。

```
import Loadable from 'react-loadable';

function ProgressDiv() {
  return <div>Div Loading In Progress...</div>;
}

const LoaderContainerComponent = Loadable({
  loader: () => import('./loadable-another-component'),
  LoadingComponent: ProgressDiv
});

class MyComponent extends React.Component {
  render() {
    return <LoaderContainerComponent/>;
  }
}

```

无论是多次加载一个组件还是一组组件，都可以使用相同的过程。加载一组组件通常需要多个动态导入并提取所需的组件类型，但基本思想是相同的。

即使优化了分块，也很难跟踪块的内容。默认情况下，块有随机生成的名称，这使得调试很困难。

一些 bundlers，比如 Webpack，允许您通过在组件加载器实现中添加注释来命名块。命名块可以帮助您计算加载每个块需要多少开销。加载太多的小块可能会比加载一个大块更慢，即使大块中的所有内容在每次页面加载时都不会被使用。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

React 消除了代码拆分过程中的大量手工工作，允许开发人员专注于创建健壮的程序。代码分割是现代 web 的一个重要部分，所以难怪这么多开发人员在 React 中依赖它。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)