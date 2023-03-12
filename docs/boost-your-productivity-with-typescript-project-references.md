# 使用 TypeScript 项目参考提高您的工作效率

> 原文：<https://blog.logrocket.com/boost-your-productivity-with-typescript-project-references/>

如果您在 monorepo 中有一个不使用 [TypeScript 项目引用](https://www.typescriptlang.org/docs/handbook/project-references.html)的 TypeScript 代码库，那么您就错过了一个技巧。

TypeScript 项目引用从 TypeScript 3.0 开始就存在了，它允许您在当前包所依赖的`tsconfig.json`中指定依赖包。当您构建一个包含依赖项的包时，依赖项首先被构建。

下面的`tsconfig.json`指定这个包引用了一个`common`包，并且`common`包是在当前包之前构建的:

```
{
  "extends": "../tsconfig-base.json",
  "compilerOptions": {
    "outDir": "../lib/animals",
    "rootDir": ".",
  },
  "references": [
    { "path": "../core" }
  ]
}
```

项目引用是通过具有`path`属性的对象的`references`数组来指定的。`path`属性是包含`tsconfig.json`文件的不同位置的相对路径。

如果项目没有使用项目引用，那么所有的包都必须单独构建，如果有多个依赖包，这将是一个巨大的负担。

## 增量构建

当传输包含项目引用的包时，`-b`或`--build`开关被添加到 [tsc](https://www.typescriptlang.org/docs/handbook/compiler-options.html) 编译器中:

```
 > tsc -b                           # Use the tsconfig.json in the current directory
 > tsc -b src                       # Use src/tsconfig.json
 > tsc -b foo/prd.tsconfig.json bar # Use foo/prd.tsconfig.json and bar/tsconfig.json
```

一个成功的构建将输出一个`tsconfig.tsbuildinfo`，在后续的构建中使用，以确保只构建新代码或更改过的代码，从而缩短构建时间。`tsconfig.tsbuildinfo`文件包含构建整个项目所需的所有文件的签名和时间戳。在后续的生成中，TypeScript 将使用该信息来检测最经济的类型检查方式，并向项目发出更改。

## 观察 monorepo 中所有包的变化

真正推动我的 TypeScript 开发的是让`tsc`观察 monorepo 的每个包中的变化的能力。

我有一个这样的脚本，它在我开发的整个过程中都在运行:

```
"watch": "tsc -b ./tsconfig.packages.json --watch"
```

当`watch`脚本运行时，整个 monorepo 中的任何 TypeScript 更改都将导致新的高效构建:

![tsc watching for changes](img/d89105c49d970d7b16bc7d99758d467b.png)

## Monorepo 项目结构

在大多数 monorepos 中，单独的包节点位于一个名为`packages`的文件夹中。下面是 monorepo 的一个例子，其中各种`tsconfig.json`文件以外科手术般的精度有策略地放置:

```
tsconfig.base.json         # base tsconfig.json that all packages will extend
tsconfig.packages.json     # tsconfig.json that is used to watch eveything
all packages
/package.json
/packages
  /common                  # Common utitliies used by all package
    /src
      .index.ts            # entry point for package
    /tsconfig.json         # Config file for 'common' project
    /package.json
  /web                     # Depends on 'common'
    /src
      .index.ts            # entry point for package
    /tsconfig.json         # Config file for 'web' project
  /api                     # Depends on 'common'
    /src
      .index.ts            # entry point for package
    /tsconfig.json         # Config file for 'api' project
    /package.json
```

在根文件夹中是一个由 monorepo 中的所有包扩展的`tsconfig.base.json`文件:

```
{
  "compilerOptions": {
    "baseUrl": ".",
    "composite": true,
    "declaration": true,
    "declarationMap": true,
    "paths": {
      "common": ["packages/common/src"],
      "web": ["packages/web/src"],
      "api": ["packages/api/src"]
    }
  }
}
```

这里实际上发生了很多事情:

*   `"composite": true`指示`tsc`存在项目引用
*   `"declarationMap": true`为`.d.ts`申报文件添加源地图。它在 VS 代码中的意思是`Go to Definition`,将会转到实际的文件，而不是`.d.ts`文件
*   `paths`字段包含一个对象，每个对象键(本例中为 common、web 或 API)指向一个包的位置。有了这个设置，我们可以导入包，而不需要讨厌的相对或绝对路径。
    例如`import { a } from 'web';`

在这个例子中，每个包使用`tsconfig`的`extends`选项扩展`tsconfig.base.json`:

```
{
  "extends": "../tsconfig-base.json",
  "compilerOptions": {
    "rootDir": ".",
  },
  "references": [
    { "path": "../core" }
  ]
}
```

## 观察和构建

项目结构根处的`tsconfig.packages.json`引用了我们想要观察或构建的所有包:

```
{
  "files": [],
  "references": [
  {
    "path": "./packages/common"
  },
  {
    "path": "./packages/web"
  },
  {
    "path": "./packages/api"
  }
 ]
}
```

有了这个结构，我们现在可以一次性构建所有的包:

```
tsc -b ./tsconfig.packages.json
```

或者观看:

```
tsc -b ./tsconfig.packages.json
```

## 有用的开关

使用项目引用时，以下开关很方便:

```
--verbose: Prints out verbose logging to explain what is going on (may be combined with any other flag)
--dry: Shows what would be done but does not actually build anything
--clean: Deletes the outputs of the specified projects (may be combined with --dry)
--force: Acts as if all projects are out of date
--watch: Watch mode (may not be combined with any flag except --verbose)
```

## 网络包

像 webpack 和 Rollup 这样的 Bundlers 在相当长的一段时间内没有提供对项目引用的支持，而 Rollup 只提供部分支持。这无助于采用。

## ts web 包加载程序

配置 webpack 加载器`ts-loader`时，需要将`projectReferences`选项设置为 true:

```
{
  test: /\.tsx?$/,
  use: {
    loader: 'ts-loader',
    options: {
      projectReferences: true,
    },
  },
},
```

## 收场白

这些天来，我在 monorepo 的大部分主要开发工作中，包括客户项目和我自己的个人开发。如果我使用 TypeScript 作为我的主要语言，那么我将启用项目引用，就像我在这里概述的那样。我发现它们极大地提高了生产率，但并不是每个人都在使用。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.