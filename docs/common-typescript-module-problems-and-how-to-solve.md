# 常见的 TypeScript 模块问题及其解决方法

> 原文：<https://blog.logrocket.com/common-typescript-module-problems-and-how-to-solve/>

## 介绍

当我们必须通过`tsconfig.json`文件手动配置项目流时，TypeScript 中的构建过程会变得非常复杂。这是因为这些配置需要[理解 TypeScript 编译器和模块系统](https://blog.logrocket.com/organize-code-in-typescript-using-modules/)。

我自己从事过许多 TypeScript 项目，已经能够发现使用 TypeScript 模块时出现的两个常见问题，更重要的是，如何有效地解决它们。

### 先决条件

为了从这篇文章中获得最大的收获，您需要具备以下条件:

*   JavaScript 和 TypeScript 方面的深厚背景
*   对打字稿模块系统有深刻的理解

## 问题 1:依赖项的位置不规则

一般情况下，`node-modules`目录通常位于项目的根目录(即 baseUrl)下，如下图所示:

```
projectRoot
├── node_modules
├── src
│   ├── file1.ts
│   └── file2.ts
└── tsconfig.json
└── package.json

```

然而，有时模块并不直接位于`baseUrl`之下。例如，看看下面的 JavaScript 代码:

```
// index.js
import express from "express";

```

像 [webpack](https://blog.logrocket.com/guide-performance-optimization-webpack/) 这样的加载器使用映射配置在运行时将模块名(在本例中是`express`)映射到`index.js`文件，从而在运行时将上面的代码片段翻译成`node_modules/express/lib/express`。

此时，当我们在 TypeScript 项目中使用上面翻译的代码片段时，我们必须配置 TypeScript 编译器来使用`"paths"`属性处理模块导入:

```
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": "./src", 
    "paths": {
      "express": ["node_modules/express/lib/express"]
    }
  }
}

```

但是，使用上面的配置，TypeScript 编译器会抛出以下错误:
`express module not found`

下面是幕后发生的事情:即使`node_modules`位于`src`目录之外，TypeScript 编译器也会在`src`目录中搜索`node_modules`，从而确定没有找到该模块。

## 解决方案 1:找到正确的目录

相对于`"baseUrl"`解析`"paths"`中的映射。因此，我们的配置应该如下所示:

```
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": "./src", // This must be specified if "paths" is.
    "paths": {
      "express": ["../node_modules/express/lib/express"] // This mapping is relative to "baseUrl"
    }
  }
}

```

当我们使用这种配置时，TypeScript 编译器从`src`目录“跳”上一个目录并定位到`node_modules`目录。

或者，下面的配置也有效:

```
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".", // This must be specified if "paths" is.
    "paths": {
      "express": ["node_modules/express/lib/express"] // This mapping is relative to "baseUrl"
    }
  }
}

```

当我们使用这种配置时，TypeScript 编译器将在项目的根目录中搜索`node_modules`目录。

## 问题 2:多个后备位置

我们的第二个常见问题也与位置有关。让我们考虑以下项目配置:

```
projectRoot
├── view
│   ├── file1.ts (imports 'view/file2' and 'nav/file3')
│   └── file2.ts
├── components
│   ├── footer
│   └── nav
│       └── file3.ts
└── tsconfig.json

```

这里，`view/file2`模块位于视图目录中，`nav/file3`位于组件目录中。

在多个位置解析模块可能有点困难，因为在代码的这一点上，编译器不知道如何从不同的位置解析这些模块。在下一节中，我们将回顾如何解决这个问题。

## 解决方案 2:定位模块并解析导入

使用下面的配置，我们可以告诉编译器在两个位置(即`["*", "components/*"]`)查找项目中的任何模块导入:

```
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "*": ["*", "components/*"]
    }
  }
}

```

在这个例子中，数组中的`"*"`值表示模块的确切名称，而`"components/*"`值是模块名称(“组件”)加上一个前缀。

我们现在可以指示编译器如下解析这两个导入:

```
import 'view/file2'

```

然后，编译器会用数组中的第一个位置(`"*"`)替换`'view/file2'`，并将其与`baseUrl`组合，得到`projectRoot/view/file2.ts`。这将允许找到该模块。

在这一步之后，编译器将转到下一个导入:

```
import 'nav/file3':

```

同样，编译器会用数组中的第一个位置(`"*"` ) —也称为`nav/file3`——替换`'nav/file3'`，并将其与`baseUrl`组合在一起，产生`projectRoot/nav/file3.ts`。这一次，该文件不存在，所以编译器将用第二个位置`"components/*"`替换`'nav/file3'`，并将其与`baseUrl`合并。这将导致`projectRoot/components/nav/file3.ts`，从而允许模块被发现。

## 默认情况下 TypeScript 如何解析模块

如果不像前面讨论的那样配置 TypeScript 编译器，TypeScript 将默认采用 Node.js 运行时解析策略，以便在编译时定位所请求的模块。

为此，TypeScript 编译器将寻找。ts 文件、. d.ts、.tsx 和 package.json 文件。如果编译器找到了 package.json 文件，那么它将检查该文件是否包含指向类型文件的类型属性。如果没有找到这样的属性，编译器将在移动到下一个文件夹之前尝试查找索引文件。

这里有一个例子。类似于`/projectRoot/view/file1.ts`中的`import { b } from 'view/file2'`的导入语句将导致尝试定位`".view/file2"`的以下位置:

1.  `/projectRoot/view/file2.ts`存在吗？
2.  `/projectRoot/view/file2.tsx`存在吗？
3.  `/projectRoot/view/file2.d.ts`存在吗？
4.  `/projectRoot/view/file2/package.json`(如果它指定了一个`"types"`属性)是否存在？
5.  `/projectRoot/view/file2/index.ts`存在吗？
6.  `/projectRoot/view/file2/index.tsx`存在吗？
7.  `/projectRoot/view/file2/index.d.ts`存在吗？

如果此时没有找到该模块，那么将重复相同的过程，从最近的父文件夹跳出一步，如下所示:

1.  `/projectRoot/file2.ts`存在吗？
2.  `/projectRoot/file2.tsx`存在吗？
3.  `/projectRoot/file2.d.ts`存在吗？
4.  `/projectRoot/file2/package.json`(如果它指定了一个`"types"`属性)是否存在？
5.  `/projectRoot/file2/index.ts`存在吗？
6.  `/projectRoot/file2/index.tsx`存在吗？
7.  `/projectRoot/file2/index.d.ts`存在吗？

## 结论

有时，我们可能会遇到一些复杂的情况，在这种情况下，诊断模块未被解析的原因可能会非常困难。在这种情况下，使用`tsc --traceResolution`启用编译器模块解析跟踪可以提供关于模块解析过程中发生的事情的更多信息，允许我们选择正确的解决方案。

在这篇文章中强调了常见的 TypeScript 模块问题，并提供了解决方案，我希望配置 TypeScript 编译器来处理您的 TypeScript 项目中的模块会变得容易一些。

希望这篇文章对你有所帮助。您也可以查看官方的 TypeScript [文档](https://www.typescriptlang.org/docs/handbook/module-resolution.html)，深入了解 TypeScript 模块解析。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.