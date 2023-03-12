# 用主题 UI 构建 React 定制组件库

> 原文：<https://blog.logrocket.com/build-react-custom-component-library-theme-ui/>

React 是一个 JavaScript 工具包，可以为在线和移动应用程序创建漂亮的用户界面。它很容易与其他 JavaScript 框架和库接口，并且具有简短的、可重用的组件。

由于其强大的模块化，React 组件库加快了 UI 开发，并提供了很大的自由度。有几个[流行的 React UI 库](https://blog.logrocket.com/top-9-ui-libraries-kits-react/)；但是，它们可能无法提供所有项目所需的定制级别。

在本教程中，我们将回顾如何使用 TypeScript 在 React with [Theme UI](https://theme-ui.com) 中构建和发布自定义组件库。

## 什么是主题 UI？

主题 UI 是一个库，它使用基于约束的设计概念来创建主题化的用户界面。有了一个针对最佳开发人员人机工程学的广泛 API，主题 UI 可以用来创建定制的组件库、设计系统、web 应用程序、Gatsby 主题等等。

## 入门指南

本实践教程具备以下先决条件:

*   [Node.js](https://nodejs.org/) 已安装
*   Ubuntu 20.04，或者你选择的操作系统
*   VS 代码，或者您选择的任何 IDE

## 项目设置

我们将首先运行以下命令为我们的项目创建一个文件夹:

```
mkdir themecomponentui && cd themecomponentui

```

接下来，我们将使用`npm init`命令创建一个`package.json`文件来初始化一个新的 React 项目。然后，我们将使用以下命令安装 React 和 TypeScript:

```
npm i -D react @types/react typescript

```

上面命令中的`-D`标志表示模块应该作为`devDependencies`安装，因为我们在构建过程中需要它们。

安装了 React 和 TypeScript 之后，让我们根据下面的文件夹结构来组织我们的项目:

📦themecomponentui
┣ 📂src
┃ ┣ 📂components
┃ ┃ ┣ 📂Button
┃ ┃ ┣ 📂Input

现在，让我们为项目配置 TypeScript。

## 配置 TypeScript

我们将使用 TypeScript 来构建组件，使我们能够将该库作为一个模块来使用。

首先，我们将通过使用以下命令创建一个`tsconfig.json`文件来配置 TypeScript:

```
npx tsc --init

```

然后，我们将使用下面的代码片段更新`tsconfig.json`文件:

```
{
  "compilerOptions": {
    "esModuleInterop": true,
    "strict": true,
    "skipLibCheck": true,
    "jsx": "react",
    "module": "ESNext",
    "declaration": true,
    "declarationDir": "types",
    "sourceMap": true,
    "outDir": "dist",
    "moduleResolution": "node",
    "emitDeclarationOnly": true,
    "allowSyntheticDefaultImports": true,
    "forceConsistentCasingInFileNames": true,
  },
  "exclude": [
    "dist",
    "node_modules",
  ],
}

```

在上面的代码中，我们添加了以下配置:

*   `"jsx": "react"`:将 JSX 码转换成 React 码
*   `"skipLibCheck": true`:跳过声明文件的类型检查
*   为我们的库生成现代的 JavaScript 模块(ES6 或更高版本)
*   `"declarationDir": "types"`:设置. d.ts 文件的目录
*   `"sourceMap": true`:支持将 JavaScript 代码映射回其 TypeScript 文件源，以便调试
*   `"outDir": "dist"`:设置项目构建的目录
*   `"moduleResolution": "node"`:遵循 Node.js 规则查找模块
*   `"emitDeclarationOnly": true`:允许 Rollup 生成 JavaScript 导出类型声明

既然已经配置了 TypeScript，让我们继续配置 Rollup。

## 配置汇总

Rollup 是一个 JavaScript 模块捆绑器，它将微小的代码块组合起来，创建更大更复杂的东西，比如一个库或应用程序。它不像 CommonJS 和 AMD 那样使用独特的解决方案，而是利用代码的标准化 es 模块结构。

为了开始使用 Rollup，我们将使用以下命令安装它:

```
npm i -D rollup

```

在上面的代码中，我们将`-D`标志添加到 Rollup 安装命令中，以将其添加到我们的`devDependencies`中。安装完成后，我们还将安装以下汇总插件:

我们将运行以下命令来安装插件:

```
npm i -D @rollup/plugin-node-resolve @rollup/plugin-commonjs @rollup/plugin-typescript rollup-plugin-peer-deps-external rollup-plugin-terser rollup-plugin-dts

```

安装完成后，我们将通过使用以下代码片段在项目根目录中创建一个`rollup.config.js`文件来配置 Rollup:

```
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";
import typescript from "@rollup/plugin-typescript";
import dts from "rollup-plugin-dts";
import { terser } from "rollup-plugin-terser";
import peerDepsExternal from 'rollup-plugin-peer-deps-external';
const packageJson = require("./package.json");

export default [
    {
        input: "src/index.ts",
        output: [
            {
                file: packageJson.main,
                format: "cjs",
                sourcemap: true,
            },
            {
                file: packageJson.module,
                format: "esm",
                sourcemap: true,
            },
        ],
        plugins: [
            peerDepsExternal(),
            resolve(),
            commonjs(),
            typescript({ tsconfig: "./tsconfig.json" }),
            terser(),
        ],
        external: ["react", "react-dom"]
    },
    {
        input: "dist/esm/types/index.d.ts",
        output: [{ file: "dist/index.d.ts", format: "esm" }],
        plugins: [dts()],
    },
];

```

在上面的代码中，我们配置了 CommonJS 和 ES 模块来处理项目构建过程。这将使我们能够使用命名导出和树摇动。它还提供了增强的静态分析、浏览器支持以及与其他 JavaScript 版本的兼容性。

接下来，我们需要在`package.json`文件中指定 CommonJS 文件和 es 模块的路径。我们将打开`package.json`文件并添加以下代码片段配置:

```
"main": "dist/cjs/index.js",
"module": "dist/esm/index.js",

```

最后，我们将在`scripts`对象中添加构建命令:

```
...
"scripts": {
   ...
    "build": "rollup -c"
  },
...

```

## 创建自定义组件

现在，让我们创建自定义组件。首先，我们将安装主题用户界面:

```
npm install -D theme-ui @emotion/react @emotion/styled @mdx-js/react

```

主题 UI 是一个灵活的 API 框架，所以我们可以选择使用原始 UI 组件库或者使用 [`sx`道具](https://theme-ui.com/sx-prop/)。出于本教程的演示目的，我们将使用`sx`道具。

## 创建按钮组件

接下来，让我们创建按钮组件。我们将在`src/components/Button`文件夹中创建`Button.tsx`和`index.ts`文件。

打开`Button.tsx`文件并添加以下代码片段:

```
/** @jsxImportSource theme-ui */
import * as React from "react";
import { MouseEventHandler } from "react";

export interface ButtonProps {
    label?: string;
    color?: string;
    fontFamily?: string
    onClick?: MouseEventHandler<HTMLButtonElement>
}

const Button = (props: ButtonProps) => {
    return <button onClick={props.onClick} sx={{ color: props.color, fontFamily: props.fontFamily }}>{props.label}</button>;
};
export default Button;

```

在上面的代码中，我们在文件的顶部添加了`/** @jsxImportSource theme-ui */`注释来启用主题 UI `sx`道具。

接下来，我们导入 React 框架`MouseEventHandler`方法来启用按钮点击事件。

然后，我们定义了`interface` `ButtonProps`，指定了`Button`组件的属性。`?`符号表示一个可选属性(例如`color?: string`)，意味着如果我们不在组件中提供该属性，TypeScript 不会抛出错误。

接下来，我们创建了`Button`组件，并使用带有其他属性的主题 UI 的`sx`道具来设置样式。

现在，让我们使用下面的代码片段导出`index.ts`文件中的`Button`组件:

```
export { default } from './Button'

```

## 创建`Input`组件

我们将通过在`src/components/Input`文件夹中创建`Input.tsx`和`index.ts`文件来创建`Input`组件。

打开`Input.tsx`文件，并添加以下代码片段:

```
/** @jsxImportSource theme-ui */
import * as React from "react";
import { ChangeEventHandler } from "react"

export interface InputProps {
    label?: string;
    disabled?: boolean;
    fontFamily?: string;
    placeholder?: string;
    paddding?: string;
    id?: string;
    onChange?: ChangeEventHandler<HTMLInputElement>
}

const Input = (props: InputProps) => {
    return (
        <div>
            <label htmlFor={props.id ? props.id : 'text'}>{props.label}</label>
            <input type='text' id={props.id ? props.id : 'text'} disabled={props.disabled} placeholder={props.placeholder} sx={{ padding: !props.paddding && "4px", display: 'block' }}></input>;
        </div>
    );
};
export default Input;

```

在上面的代码中，我们在文件的开头添加了`/** @jsxImportSource theme-ui */`注释，以使用主题 UI 的`sx`属性来设置组件的样式。

接下来，我们定义了`Input`组件的`interface`。属性`onChange?`将使我们能够添加一个`onChange`事件到输入中。

我们创建了`Input`组件，它接受与`InputProps`接口匹配的`props`，并使用主题 UI 的`sx`属性和其他属性来设计组件的样式。

现在，让我们使用以下命令导出`index.ts`文件中的`Input`组件:

```
export { default } from './Input'

```

我们将在`src/components`文件夹中创建一个`index.ts`文件，并像这样导出组件:

```
export { default as Button } from './Button';
export { default as Input } from './Input'

```

现在，我们将构建项目:

```
npm run build

```

上面的命令将在项目根目录下创建一个`dist`文件夹。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 发布到国家预防机制

下一步是将我们新创建的组件发布到 npm，这样我们就可以在项目中使用它们，并与朋友们分享。

要开始，请登录您的 npm 帐户:

```
npm login

```

如果你没有帐号，你可以在这里注册一个 npm 帐号。

登录后，输入您的帐户凭据以获得认证。动态口令代码将发送到您注册的电子邮件地址。要求时输入 OTP pin。

现在，通过运行以下命令发布您的包:

```
npm publish --access public

```

您已经成功地向 npm 发布了 React 自定义组件库！

## 结论

在本教程中，我们介绍了主题 UI 库，并演示了如何使用主题 UI 来构建自定义的 React 组件库。我们还演示了如何为项目构建配置 TypeScript 和 Rollup，以及如何向 npm 发布自定义库。您可以通过创建更多带有主题 UI 的组件来扩展本教程，比如表单、框和网格。

感谢阅读。欢迎在下面分享和评论。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)