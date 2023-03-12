# React 中的语法高亮指南

> 原文：<https://blog.logrocket.com/guide-syntax-highlighting-react/>

软件开发人员读的代码和他们写的一样多。因此，为了增强代码的可读性，您不仅应该努力编写可读的代码，还应该应用适当的代码格式和语法突出显示。

开发人员日常使用几种编程和非编程计算机语言。每种语言都有自己的语法，因此，需要突出语法。为了在浏览器环境中应用语法高亮，存在诸如 Prism、Highlight 和 [React](https://blog.logrocket.com/tag/react/) 语法高亮器之类的库。

这些库各有优缺点。其中一些库是为普通 JavaScript 开发的，而其他库也支持 React 等前端框架。

本文将全面介绍 Prism 等流行的语法高亮库，以及如何在 React 中使用它们。我们还将强调他们的优势和劣势。

### 目录

## `prism.js`简介

Prism 是 JavaScript 中最受欢迎的语法高亮库之一，在 GitHub 上有超过 10，000 颗星。您可以将它与普通 JavaScript 和 React 等框架一起使用。它支持多种语言，拥有丰富的主题和插件生态系统，可以用来扩展其核心功能。

为了体验 Prism，您可以通过 CDN 快速加载它，或者下载源代码并使用`script`标签将其添加到您的标记中。您还可以下载对特定语言、主题和插件的支持。

核心 Prism 库占地面积很小。它的压缩包大小约为 2kB。虽然你用 core Prism 下载的额外主题和插件可能会增加软件包的大小，但是你可以完全控制你可以下载的插件或主题。

此外，与一些语法高亮器不同，Prism 强迫您使用语义 HTML 来表示代码。可定制性和其他几个特性使得 Prism 成为语法突出显示的流行选择。

Babel-plugin-prismjs 是一个将 Prism 与 Babel 和 webpack 一起使用的便捷包。在下一节中，我们将看看如何在 React 应用程序中使用这个插件。

## 如何在 React 中使用 Prism 进行语法高亮显示

如前一节所述，开始使用 Prism 最简单、最快速的方法是使用 CDN。虽然 CDN 可能很方便，但在使用 React 这样的框架时，几乎总是不可能使用它。因此，您需要从 NPM 安装它。

```
# using npm
npm install prismjs
# using yarn
yarn add prismjs

```

如果你想在 React 应用程序中使用 Prism 进行语法高亮显示，那么 Babel 插件就派上了用场。您可以使用它来配置要与 Prism 捆绑的语言、主题和插件。您可以从 NPM 将它作为开发依赖项进行安装。

```
# using npm
npm i -D babel-plugin-prismjs
# using yarn
yarn add -D babel-plugin-prismjs

```

您需要在您的 Babel 配置文件中注册插件，如下所示:

```
{
  "plugins": [
    ["prismjs", {
        "languages": ["javascript", "css", "markup"],
        "plugins": ["line-numbers"],
        "theme": "twilight",
        "css": true
    }]
  ]
}

```

您可以在配置文件中包含项目所需的语言、插件和主题。查看 Prism 文档，获得支持语言的完整列表以及可以使用的主题和插件。

如果你想使用`babel-plugin-prismjs`插件，设置一个自定义的 react 应用程序。然而，要与 [Create React App](https://blog.logrocket.com/getting-started-with-create-react-app-d93147444a27/) 一起使用，你需要弹出——我认为这样做违背了使用 Create React App 的目的。

此外，一些 React 语法突出显示包使用 Prism。我们将在下面的章节中探讨其中的一些。如果您的项目不是基于定制的 React 应用程序，您可以考虑使用其中的一个。

完成上述设置后，将 Prism 导入 React 组件并调用`useEffect`钩子中的`highlightAll`方法，如下所示:

```
import React, { useEffect } from "react";
import Prism from "prismjs";

function App() {
  useEffect(() => {
    Prism.highlightAll();
  });
  return (
    <div>
      <p>
        You can declare a variable in JavaScript using the
        const, let or var keyword. </p> </div> ); } export default App;
```

当使用 Prism 时，您需要将内联代码包装在`code` HTML 标签中，并根据您突出显示的语言，对其应用适当的类。类名应该采用`lang-xxxx`或`language-xxxx`的形式。

`xxxx`是语言的占位符。在上面的例子中，我们向`code`元素添加了`lang-javascript`类，因为我们突出显示了 JavaScript 代码。

正如上一节所指出的，Prism 迫使您在向标记中添加代码时使用语义 HTML。因此，在突出显示代码块时，必须将代码包装在`pre`元素中。

在呈现代码块时，将您的功能提取到一个单独的组件以实现可重用性是很方便的，如下例所示。你可以把代码块和语言作为道具传递。

```
import React, { useEffect } from "react";
import Prism from "prismjs";

const CodeBlock = ({ code, language }) => {
  useEffect(() => {
    Prism.highlightAll();
  }, []);
  return (
    <pre>
      <code children={code} className={`language-${language}`} />
    </pre>
  );
};

export default CodeBlock;

```

## 如何使用棱镜反应渲染器

您也可以使用 prism-react-renderer 来突出显示 react 应用程序中的代码，而不是像我们在上一节中那样将 Prism 与 React 应用程序一起使用。

prism-react-renderer 与 prism 的修改版本捆绑在一起，默认情况下，它提供了对一些常见语言和主题的支持。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

需要安装 NPM 的[p](https://www.npmjs.com/package/prism-react-renderer)[rism](https://www.npmjs.com/package/prism-react-renderer)[–](https://www.npmjs.com/package/prism-react-renderer)[react](https://www.npmjs.com/package/prism-react-renderer)[–](https://www.npmjs.com/package/prism-react-renderer)[渲染器](https://www.npmjs.com/package/prism-react-renderer)才能使用。

```
sh
# install with npm
npm i prism-react-renderer

# install with yarn
yarn add prism-react-renderer

```

Prism react 渲染器将`Highlight`公开为其主要组件，并导出一些默认道具，您可以将这些道具传递给`Highlight`。您需要将一个`render`道具传递给`Highlight`组件，如下例所示。

```
import React from "react";
import Highlight, { defaultProps } from "prism-react-renderer";

export const CodeBlock = ({ code, language }) =&gt; {
  return (
    &lt;Highlight {...defaultProps} code={code} language={language}&gt;
      {({ className, style, tokens, getLineProps, getTokenProps }) =&gt; {
        return (
          &lt;pre className={className} style={style}&gt;
            <code>
                  {tokens.map((line, idx) => {
                    return (
                      <div {...getLineProps({ line, key: `line-${idx}` })}>
                        {line.map((token, i) => {
                          return (
                            <span
                              {...getTokenProps({ token, key: `token-${i}` })}
                            />
                          );
                        })}
                      </div>
                    );
                  })}
                </code>
          &lt;/pre&gt;
        );
      }}
    &lt;/Highlight&gt;
  );
};
</code></pre>
```

上面的代码块演示了如何用 prism-react-renderer 包突出显示一个简单的代码块。`Highlight`组件将几个参数传递给渲染属性。您可以使用这些道具来呈现您的代码块，如上例所示。

你可以导入我们上面写的`CodeBlock`组件，在渲染代码块的时候传入`code`和`language`道具。这样做可以使组件可重用。

```
import { CodeBlock } from "./CodeBlock";

const code = `
  const add = (a, b) => {
    return a + b;
  }
`;

function MyComponent() {
  return (
    <div className="App">
      <CodeBlock code={code} language="javascript" />
    </div>
  );
}

```

## react-syntax-highlight 简介

react-syntax-highlighter 是 react 中用于语法高亮显示的 React 组件。它使用 Prism 和 Highlight 在内部突出显示语法。Prism 和 Highlight 是浏览器环境中流行的语法高亮工具。

你从 NPM 安装它，并传递必要的道具来开始使用 react-syntax-highlighter。

```
# using npm
npm i react-syntax-highlighter

# using yarn
yarn add react-syntax-highlighter

```

与前几节中描述的一些包不同，您不需要额外的配置就可以让它工作。让我们在下一节看看如何在 React 中使用它来突出显示语法。

## 如何使用 react-syntax-highlight

安装 react-syntax-highlighter 后，可以导入并呈现必要的组件。如前一节所述，您可以选择使用高亮或棱镜来高亮显示。

但是，react-syntax-highlight 导出一个默认情况下使用高亮显示的组件。在呈现时，您需要将配置选项作为道具传递，如下所示:

```
import SyntaxHighlighter from "react-syntax-highlighter";
import { dracula } from "react-syntax-highlighter/dist/esm/styles/hljs";

const code = `
console.log("hello world");
`;

function App() {
  return (
      <SyntaxHighlighter children={code} language="javascript" style={dracula} />
  );
}

```

如上例所示，您将想要突出显示的代码和格式化语言作为字符串属性进行传递。除了`children`、`language`和`style`道具，还有几个其他的格式选项，比如显示和样式化行号。请务必查阅文档以获得可用选项的完整列表。

正如在简介中指出的，react-syntax-highlighter 与 Highlight 和 Prism 捆绑在一起。除了使用上面例子中的高光，你还可以使用棱镜。使用 Prism 时，还需要导入想要使用的 Prism 主题，如下例所示。

```
import { Prism as SyntaxHighlighter } from 'react-syntax-highlighter';
import { dark } from 'react-syntax-highlighter/dist/esm/styles/prism';

```

尽管上面描述的使用方法加载了一些现成的特性，但是它占用了大量内存。因此，react-syntax-highlighter 有一个轻量级的构建，只加载和捆绑您需要的功能。

但是，请注意，灯光构建没有默认样式。所以，你需要自己导入主题。此外，使用`registerLanguage`函数导入并注册语言，如下例所示。

```
import { Light as SyntaxHighlighter } from "react-syntax-highlighter";

import typescript from "react-syntax-highlighter/dist/esm/languages/hljs/typescript";
import a11yDark from "react-syntax-highlighter/dist/esm/styles/hljs/a11y-dark";

SyntaxHighlighter.registerLanguage('typescript', typescript);

```

默认情况下，上面的灯光构建示例使用高光。你可以类似地使用 Prism。导入`PrismLight`而不是`Light`，如下例所示。

```
import { PrismLight as SyntaxHighlighter } from "react-syntax-highlighter";

import typescript from "react-syntax-highlighter/dist/esm/languages/prism/typescript";
import a11yDark from "react-syntax-highlighter/dist/esm/styles/prism/a11y-dark";

SyntaxHighlighter.registerLanguage('typescript', typescript);

```

有关 react-syntax-highlighter 包的更多特性和限制，请查看文档。

## 彩虹简介

上面提到的语法高亮库是最流行的，也是最容易与 React 一起使用的。

然而，它们并不是唯一的语法高亮库。Rainbow 是另一个简单的轻量级语法高亮包。这是值得探索的，尤其是当你没有使用 React 这样的框架时。

## 结论

当发布包含内联代码片段或代码块的内容以增加可读性时，语法高亮是不可避免的。在浏览器环境中突出显示代码时，有几个库可供选择——最流行的语法突出显示库是 Prism 和 Highlight。

如上所述，您可以在普通 JavaScript 中使用一些语法高亮库，在 React 等框架中使用其他语法高亮库。Babel 插件可以让你将 Prism 与 webpack 这样的捆绑器一起使用。但是，您也可以使用 React 包，如 prism-react-renderer 或 react-syntax-highlighter，它们捆绑了 prism 的修改版本。

当考虑核心库时，Prism 占用的空间很小。然而，当你用额外的主题和插件扩展它的核心功能时，它就变得相当大了。

虽然我们在本文中关注的是可与 React 一起使用的语法高亮器，但是其他几个语法高亮库，比如 Rainbow，也值得一试。如果您没有使用 React 这样的前端框架，Rainbow 是一个很好的语法高亮库。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)