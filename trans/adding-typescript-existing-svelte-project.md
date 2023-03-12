# 将 TypeScript 添加到现有的 Svelte 项目中

> 原文：<https://blog.logrocket.com/adding-typescript-existing-svelte-project/>

自 2020 年以来，Svelte 已经正式支持 TypeScript，然而，将 TypeScript 添加到您的 Svelte 项目会从根本上改变您的工具链。尽管我们无法预测工具链的形状，但我们可以假设它使用了 web 应用捆绑器，如 [webpack、Parcel 或 Rollup](https://blog.logrocket.com/benchmarking-bundlers-2020-rollup-parcel-webpack/) 。要将 TypeScript 添加到一个苗条的项目中，我们基本上是告诉 bundler 将 TypeScript 代码转换成 JavaScript 代码。

在本教程中，我们将学习如何使用提到的三个捆绑器为 Svelte 配置 TypeScript。要阅读本文，您需要:

*   打字稿的基础知识
*   了解您当前的工具链
*   纱作为您的包装经理

我们开始吧！

## 共享构建配置

按照本教程中的例子，首先，我们将配置我们的共享构建。您应该将您的源文件存储在`./src/`目录中，将您的构建文件存储在`./build`目录中。

应用程序的入口点将是`./src/index.ts`文件，它将从`./App.svelte`文件导入`App`类，初始化它，并默认导出它，如下例所示:

```
import App from './App.svelte';

export default (new App({ target: document.body }));

```

Svelte 会将 DOM 条目注入到 HTML 页面的主体中。现在，我们可以实现`App`组件:

```
<script lang="ts">
    import { onMount } from 'svelte';

    export let result: number = 2 + 2;

    onMount(() => {
        setTimeout(
            () => { result *= 3; },
            500,
        );
    });
</script>

<div>{ result }</div>

```

在下面的代码块中，`<script lang="ts">`标记声明和变量定义`export let result: number = 2 + 2;`表明代码现在存在于 TypeScript 上下文中。

接下来，通过键入`yarn build`调用 npm 中的`build`脚本。`build`脚本将运行您选择的捆绑器，编译您的项目，并生成一个单独的`./build/bundle.js`文件。要使用`./build/bundle.js`文件，您应该创建一个相应的`./build/index.html`文件:

```
<!DOCTYPE html>
<html lang="en">
<body>
    <script defer src='./bundle.js'></script>
</body>
</html>

```

现在，在浏览器中导航到`./build/index.html`文件，您应该会看到您的应用程序正在运行！现在，让我们来看看三个流行的捆绑器，您可以使用它们来迁移您的应用程序。

## 到达

Rollup 是由 Svelte 的创造者 Rich Harris 创建的库。因此，Svelte 通过自定义的[集成包](https://github.com/sveltejs/template)支持 Rollup 也就不足为奇了。

要使用 Rollup，我们首先需要安装以下软件包作为开发依赖项:

*   `@rollup/plugin-commonjs`
*   `@rollup/plugin-node-resolve`
*   `@rollup/plugin-typescript`
*   `@tsconfig/svelte`
*   `rollup`
*   `rollup-plugin-svelte`
*   `svelte`
*   `svelte-preprocess`
*   `tslib`
*   `typescript`

要安装这些依赖项，我们可以使用下面的部分`./package.json`文件:

```
{
  "scripts": {
    "build": "rollup -c"
  },
  "devDependencies": {
    "@rollup/plugin-commonjs": "20.0.0",
    "@rollup/plugin-node-resolve": "13.0.4",
    "@rollup/plugin-typescript": "8.2.5",
    "@tsconfig/svelte": "2.0.1",
    "rollup": "2.56.2",
    "rollup-plugin-svelte": "7.1.0",
    "svelte": "3.42.1",
    "svelte-preprocess": "4.7.4",
    "tslib": "2.3.1",
    "typescript": "4.3.5"
  }
}

```

或者，我们可以使用 Yarn 安装这些依赖项:

```
yarn add @rollup/plugin-commonjs @rollup/plugin-node-resolve @rollup/plugin-typescript @tsconfig/svelte rollup rollup-plugin-svelte svelte svelte-preprocess tslib typescript -E -D

```

### 汇总的类型脚本配置

接下来，我们将使用下面的`./tsconfig.json`文件为 Svelte 和 Rollup 配置 TypeScript:

```
{
  "extends": "@tsconfig/svelte/tsconfig.json",
  "include": ["src/**/*"],
  "exclude": ["node_modules/*"]
}

```

项目将会导入一些额外的配置到我们的项目中。您可以根据需要添加其他设置，但首先，请确保它们不会与“苗条”相冲突。

### 汇总配置

让我们为 Rollup 添加必要的配置。在下面的代码块中，您将看到我们的示例性`./rollup.config.js`文件，它通过`svelte-preprocess`库设置了正确的预处理:

```
import svelte from 'rollup-plugin-svelte';
import commonjs from '@rollup/plugin-commonjs';
import resolve from '@rollup/plugin-node-resolve';
import sveltePreprocess from 'svelte-preprocess';
import typescript from '@rollup/plugin-typescript';

export default {
    input: './src/index.ts',
    output: {
        format: 'iife',
        file: './build/bundle.js'
    },
    plugins: [
        svelte({
            preprocess: sveltePreprocess(),
        }),
        resolve({ browser: true }),
        commonjs(),
        typescript(),
    ],
};

```

接下来，我们将创建一个 TypeScript 三斜线指令，通知编译器关于对`svelte`包的依赖。我们需要创建一个名为`./src/global.d.ts`的文件，并添加以下内容:

```
/// <reference types="svelte" />

```

完成这些步骤后，您应该能够使用 Rollup 编译您的项目了。

## 网络包

webpack 是另一个流行的捆绑软件，它有一个为苗条身材定制的集成包。为了使用 webpack 将 TypeScript 迁移到 Svelte，我们将[模板-webpack](https://github.com/sveltejs/template-webpack) 减少到只有关键部分。

首先，我们将安装以下软件包作为开发依赖项:

*   `@tsconfig/svelte`
*   `svelte`
*   `svelte-loader`
*   `svelte-preprocess`
*   `ts-loader`
*   `typescript`
*   `webpack`
*   `webpack-cli`

我们可以使用下面的`package.json`来安装这些依赖项:

```
{
    "scripts": {
        "build": "webpack"
    },
    "devDependencies": {
        "@tsconfig/svelte": "1.0.10",
        "svelte": "3.31.2",
        "svelte-loader": "3.0.0",
        "svelte-preprocess": "4.3.0",
        "ts-loader": "8.0.4",
        "typescript": "4.0.3",
        "webpack": "5.16.0",
        "webpack-cli": "4.4.0"
    }
}

```

如果你喜欢使用 Yarn，你可以运行下面的代码:

```
yarn add @tsconfig/svelte svelte svelte-loader svelte-preprocess ts-loader typescript webpack webpack-cli -E -D

```

### webpack 的 TypeScript 配置

现在，让我们使用下面提供的部分`./tsconfig.json`文件定义一个基本的 TypeScript 配置。如您所见，我们将从下面的`@tsconfig/svelte`项目中导入一些配置:

```
{
    "extends": "@tsconfig/svelte/tsconfig.json",
    "include": ["src/**/*"],
    "exclude": ["node_modules/*"]
}

```

### webpack 配置

要为 Svelte 和 TypeScript 配置 webpack，我们必须对 webpack 配置进行以下更改:

*   将入口点定义为类型脚本文件
*   指示 webpack 使用`ts-loader`来处理 TypeScript 文件
*   指导 webpack 使用`svelte-loader`和`svelte-preprocess`来处理瘦文件

存储在`./webpack.config.js`文件中的配置应该类似于下面的代码:

```
const path = require('path');
const sveltePreprocess = require('svelte-preprocess');

module.exports = {
    entry: {
        'bundle': ['./src/index.ts']
    },
    resolve: {
        alias: {
            svelte: path.dirname(require.resolve('svelte/package.json'))
        },
        extensions: ['.mjs', '.js', '.ts', '.svelte'],
        mainFields: ['svelte', 'browser', 'module', 'main']
    },
    output: {
        path: path.join(__dirname, '/build'),
    },
    module: {
        rules: [
            {
                test: /\.ts$/,
                loader: 'ts-loader',
                exclude: /node_modules/
            },
            {
                test: /\.svelte$/,
                use: {
                    loader: 'svelte-loader',
                    options: {
                        preprocess: sveltePreprocess(),
                    }
                }
            }
        ]
    },
};

```

如果您正确地执行了这些步骤，您应该能够使用 webpack 成功地编译和运行您的项目。

## 包裹

最后，我们来看看包裹。目前 Svelte 不支持 Parcel，但是，它是一个受欢迎的通用捆绑器，被开发人员广泛使用。

> 在撰写本文时，[地块 2](https://v2.parceljs.org/getting-started/migration/) 是发布候选地块。以下部分中的信息适用于地块 1。

使用 Parcel 时，需要确保安装了以下开发依赖项:

*   `@tsconfig/svelte`
*   `parcel-bundler`
*   `parcel-plugin-svelte`
*   `svelte`
*   `svelte-preprocess`
*   `typescript`

要执行迁移，您可以使用下面的部分`package.json`文件:

```
{
  "scripts": {
    "build": "parcel build ./src/index.html"
  },
  "devDependencies": {
    "@tsconfig/svelte": "2.0.1",
    "parcel-bundler": "1.12.5",
    "parcel-plugin-svelte": "4.0.9",
    "svelte": "3.42.1",
    "svelte-preprocess": "4.7.4",
    "typescript": "4.3.5"
  }
}

```

要使用 Yarn 安装依赖项，请复制下面的代码片段:

```
yarn add @tsconfig/svelte parcel-bundler parcel-plugin-svelte svelte svelte-preprocess typescript

```

### 包裹的细长结构

为了让我们的苗条文件包含类型脚本代码，我们需要在`./svelte.config.js`文件中指定苗条配置。我们将使用`svelte-preprocess`库。

以下是与 ECMAScript 5 兼容的示例配置脚本:

```
const preprocess = require('svelte-preprocess');

module.exports = {
    preprocess: preprocess(),
};

```

### 宗地的类型脚本配置

让我们使用下面的部分`./tsconfig.json`文件为 TypeScript 配置我们的应用程序。您的配置应该扩展到来自`@tsconfig/svelte`项目的配置之外:

```
{
    "extends": "@tsconfig/svelte/tsconfig.json",
    "include": ["src/**/*"],
    "exclude": ["node_modules/*"]
}

```

现在，请随意测试您的设置。完成这些步骤后，您已经成功地为 package 配置了 Svelte 和 TypeScript。

## 选择正确的捆扎机

您可能知道，捆扎机是一项日新月异的技术。Svelte 需要相当广泛的集成，所以当你升级依赖项时，你应该总是检查你的工具链的行为。

由于 Svelte 和 Rollup 之间固有的兼容性，Rollup 是一个安全的选择，用于您的项目的持久性。虽然我们不能对 webpack 和 packages 说同样的话，但第三方集成包总有可能简化从 TypeScript 到 Svelte 的迁移。

如果您使用不同的捆绑器、自定义捆绑器或根本不使用捆绑器，您可能需要自己深入研究该主题，并编写自定义适配器以允许工具链中的编译。请记住，您需要为 TypeScript 和 Svelte 文件启用 transpilation。

## 结论

尽管由于 Svelte 对 TypeScript 的官方支持，将 TypeScript 添加到 Svelte 应用程序似乎是不言自明的，但是添加正确的配置可能非常复杂。

在本教程中，我们学习了如何使用 Rollup、webpack 和 package 为 Svelte 配置我们的 TypeScript 代码，这三个高性能捆绑器使我们的代码迁移更加容易。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.