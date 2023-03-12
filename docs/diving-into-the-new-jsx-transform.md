# 潜入新 JSX 的转变-日志火箭博客

> 原文：<https://blog.logrocket.com/diving-into-the-new-jsx-transform/>

有了 React 17，您不再需要在文件中导入 React 来使用 React。迷茫？本文将告诉您为了将您的代码和知识迁移到这种新的做事方式，您需要知道什么。

## 什么是 JSX 变换？

如果你曾经写过《反应》，你可能已经注意到了普通 JavaScript 文件中怪异的 HTML 语法。这种语法被称为 JSX，并且是*而不是*有效的 JavaScript。看样子:

```
const Welcome = () => {
  return <h1 className="hero">Welcome!</h1>;
};
```

为了在浏览器中运行，您需要通过编译器运行您的代码，通常是 Babel 或 TypeScript，它会将您的代码更改为有效的 JavaScript。因为，事实证明，JSX 只是另一种写法:

```
const Welcome = () => {
  return React.createElement("h1", { className: "hero", children: "Welcome" });
};
```

因此，Babel 或 TypeScript(或任何你选择的工具)将你的代码转换成对函数`React.createElement`的调用——这就是挑战所在。因为，突然间，你的代码中有了一个“React”的引用！如果没有在文件顶部导入 React，JavaScript 也不知道如何处理这些编译后的代码。这就是为什么你需要将`import React from 'react'`添加到所有 JSX 文件的顶部。

将此添加到所有文件的顶部对于初学者来说是一个巨大的绊脚石，对于专家来说是一个痛苦的负担。有了 React 17，您不再需要指定它！

React 团队已经与社区合作创建了一个新的转换，它从一个新的定制入口点自动导入一个新的`jsx`-函数。编译后的代码将如下所示:

```
import { jsx as _jsx } from "react/jsx-runtime";

const Welcome = () => {
  return _jsx("h1", { className: "hero", children: "Welcome" });
};

```

这也有一个潜在的更小的包大小的可爱的副作用！

## 我要做什么呢？

首先，我想重申，这个功能目前只在 React 17 的发布候选中可用。它也将被移植到旧版本，但如果你想今天就试用，你需要坚持使用版本 17。

你需要做什么取决于你用什么来编写你的 React 应用。

*   如果你正在使用 create-react-app，你需要更新到 4.0.0 版本(目前处于测试阶段)
*   如果您使用的是 Next JS，您需要更新到 9.5.3 或更高版本
*   如果你用的是 Gatsby JS，需要更新到 2.24.5 版或以上

如果你有自己的 Babel 配置，你需要多做一些手工步骤。如果您使用的是`@babel/preset-react`，请将其更新到最新版本。如果你使用的是`@babel/plugin-transform-react-jsx`，请更新那个。顺便更新一下`@babel/core`的信息。

```
npm update @babel/core @babel/preset-react

# or

npm update @babel/core @babel/plugin-transform-react-jsx

```

接下来，用以下代码片段更新您的 Babel 配置:

```
{
  "presets": [
    [
      "@babel/preset-react",
      {
        "runtime": "automatic"
      }
    ]
  ]
}

```

或者

```
{
  "plugins": [
    [
      "@babel/plugin-transform-react-jsx",
      {
        "runtime": "automatic"
      }
    ]
  ]
}

```

你完了！

## 删除现有导入

好了，快好了。因为现在你有大量不再需要的进口商品！我们不能让他们到处游荡，对吧？

实际上，你可以。在可预见的未来，一切都将照常运行。但是让死代码到处游荡只是一件麻烦的事情，如果您有资源删除它们，为什么不呢？

React 团队的优秀人员编写了一个自动脚本(所谓的 codemod ),为我们完成了这一切，而不是检查您的数百个组件并手动删除它们。🙌你只需要做以下事情:

```
npx react-codemod update-react-imports
```

这将删除默认 React 导出(`import React from 'react'`)的所有导入，并将任何对钩子和其他函数的引用重写为命名导入。这意味着`React.useEffect`将改为`useEffect`，并且一个`import { useEffect } from 'react'`语句将被添加到文件的顶部。

后一部分让我大吃一惊(我总是用前缀来写它们，因为我发现读和写都更容易)，但它为将来创建 React 的 ES 模块版本扫清了道路。过一段时间我肯定会习惯的😅

## 回报

有了这个烦人的 React 导入，React 将变得更加容易学习。少了一个需要记住的概念，开箱即用会更好。每当你引导一个新的 JSX 文件时，少写一行。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)