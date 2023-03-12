# 用 TypeScript 和 es 模块发布节点模块

> 原文：<https://blog.logrocket.com/publishing-node-modules-typescript-es-modules/>

TypeScript 已经成为一种非常流行的编写 JavaScript 的语言，这是有道理的。它的类型系统和编译器能够在你的软件运行之前在编译时捕捉各种各样的错误，并且额外的代码编辑器功能使它成为一个非常高效的开发环境。

但是，如果您想用 TypeScript 编写一个库或包，同时又提供 JavaScript 以便最终用户不必手动编译您的代码，会发生什么情况呢？我们如何使用像 es 模块这样的现代 JavaScript 特性进行创作，同时还能获得 TypeScript 的所有好处呢？

本文旨在解决所有这些问题，并为您提供一个设置，让您能够自信地编写和共享 TypeScript 库，并为您的软件包的消费者提供一个简单的体验。

## 入门指南

我们要做的第一件事是建立一个新项目。在本教程中，我们将创建一个基本的数学软件包——而不是一个服务于任何真实世界目的的软件包——因为它将让我们演示我们需要的所有打字稿，而不会偏离软件包实际做什么。

首先，创建一个空目录并运行`npm init -y`来创建一个新项目。这将创建您的`package.json`,并给您一个空项目来处理:

```
$ mkdir maths-package
$ cd maths-package
$ npm init -y
```

现在我们可以添加第一个也是最重要的依赖项:TypeScript！

```
$ npm install --save-dev typescript
```

> 在撰写本文时，TypeScript 的最新版本是 3.8。

一旦我们安装了 TypeScript，我们就可以通过运行`tsc --init`来初始化 TypeScript 项目。`tsc`是“TypeScript 编译器”的简称，是 TypeScript 的命令行工具。

为了确保您运行我们刚刚在本地安装的 TypeScript 编译器，您应该在命令前面加上前缀`[npx](https://github.com/npm/npx)`。`npx`是一个很棒的工具，它会在你的`node_modules`文件夹中寻找你给它的命令，所以通过给我们的命令加上前缀，我们确保我们使用的是本地版本，而不是你可能已经安装的任何其他全局版本的 TypeScript。

```
$ npx tsc --init
```

这将创建一个`tsconfig.json`文件，它负责配置我们的 TypeScript 项目。您会看到该文件有数百个选项，其中大部分都被注释掉了(TypeScript 支持在`tsconfig.json`文件中添加注释)。我已经将我的文件削减到仅启用的设置，它看起来像这样:

```
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

我们需要对这个配置进行一些修改，以使我们能够使用 es 模块发布我们的包，所以现在让我们浏览一下选项。

## 配置`tsconfig.json`选项

> 如果你正在寻找所有可能的`tsconfig`选项的综合列表，TypeScript 网站已经为你提供了这个[方便的参考](https://www.typescriptlang.org/v2/en/tsconfig)。

先说`target`。这定义了您将在其中提供代码的浏览器中 JavaScript 支持的级别。如果你不得不处理一组旧的浏览器，它们可能没有所有最新和最好的特性，你可以把这个设置为`ES2015`。如果你真的需要最大的浏览器覆盖范围，TypeScript 甚至会支持`ES3`。

对于本模块，我们将在这里选择`ES2015`,但您也可以相应地随意更改。举个例子，如果我正在为自己构建一个快速的辅助项目，并且只关心最先进的浏览器，我会很乐意将这个设置为`ES2020`。

## 选择模块系统

接下来，我们必须决定在这个项目中使用哪个模块系统。请注意，这不是我们将要在哪个模块系统中进行创作，而是 TypeScript 的编译器在输出代码时将使用哪个模块系统。

发布模块时，我喜欢发布两个版本:

*   一个带有 es 模块的现代版本，这样捆绑工具就可以智能地[树](https://webpack.js.org/guides/tree-shaking/)[–](https://webpack.js.org/guides/tree-shaking/)[抖落](https://webpack.js.org/guides/tree-shaking/)不使用的代码，这样支持 ES 模块的浏览器就可以简单地导入文件
*   一个使用 CommonJS 模块的版本(如果您在 Node 中工作，您将习惯于使用的`require`代码),这样旧的构建工具和 Node.js 环境可以轻松地运行代码

我们稍后将研究如何用不同的选项捆绑两次，但是现在，让我们配置 TypeScript 来输出 ES 模块。我们可以通过将`module`设置为`ES2020`来做到这一点。

现在，您的`tsconfig.json`文件应该如下所示:

```
{
  "compilerOptions": {
    "target": "ES2015",
    "module": "ES2020",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

## 写一些代码

在我们谈论捆绑代码之前，我们需要写一些！让我们创建两个小模块，它们都导出一个函数和一个主入口文件，用于导出我们所有的代码。

我喜欢将我所有的 TypeScript 代码放在一个`src`目录中，因为这意味着我们可以将 TypeScript 编译器直接指向它，所以我将用下面的代码创建`src/add.ts`:

```
export const add = (x: number, y:number):number => {
  return x + y;
}
```

我也会创建`src/subtract.ts`:

```
export const subtract = (x: number, y:number):number => {
  return x - y;
}
```

最后，`src/index.ts`将导入我们所有的 API 方法并再次导出它们:

```
import { add } from './add.js'
import { subtract } from './subtract.js'
export {
  add,
  subtract
}
```

这意味着用户可以通过导入他们需要的东西或者通过获取所有东西来使用我们的功能:

```
import { add } from 'maths-package';

import * as MathsPackage from 'maths-package';
```

请注意，在`src/index.ts`中，我的导入包括文件扩展名。如果您只想支持 Node.js 和构建工具(如 webpack ),这是不必要的，但是如果您想支持支持 ES 模块的浏览器，您将需要文件扩展名。

## 用 TypeScript 编译

让我们看看能否让 TypeScript 编译我们的代码。在这样做之前，我们需要对我们的`tsconfig.json`文件做一些调整:

```
{
  "compilerOptions": {
    "target": "ES2015",
    "module": "ES2020",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./lib",
  },
  "include": [
    "./src"
  ]
}
```

我们所做的两项改变是:

*   这告诉 TypeScript 将我们的代码编译到一个目录中。在本例中，我告诉它将目录命名为`lib`，但是您可以随意命名
*   这告诉 TypeScript 我们希望在编译过程中包含哪些文件。在我们的例子中，我们所有的代码都位于`src`目录中，所以我将它传入。这就是为什么我喜欢把我所有的 TS 源文件保存在一个文件夹中——这使得配置非常简单

让我们试一试，看看会发生什么！我发现在调整我的 TypeScript 配置时，最适合我的方法是调整、编译、检查输出，然后再调整。不要害怕尝试这些设置，看看它们是如何影响最终结果的。

为了编译 TypeScript，我们将运行`tsc`并使用`-p`标志(project 的缩写)告诉它我们的`tsconfig.json`住在哪里:

```
npx tsc -p tsconfig.json
```

如果您有任何类型错误或配置问题，这将是他们出现的地方。如果没有，您应该什么也看不到——但是请注意，您有了一个新的包含文件的`lib`目录！TypeScript 在编译时不会将任何文件合并在一起，但会将每个单独的模块转换成它的 JavaScript 等价物。

让我们看看它输出的三个文件:

```
// lib/add.js
export const add = (x, y) => {
    return x + y;
};

// lib/subtract.js
export const subtract = (x, y) => {
    return x - y;
};

// lib/index.js
import { add } from './add.js';
import { subtract } from './subtract.js';
export { add, subtract };
```

它们看起来非常类似于我们的输入，但是没有我们添加的类型注释。这是意料之中的:我们在 ES 模块中编写代码，并告诉 TypeScript 也以那种形式输出。如果我们使用比 ES2015 更新的 JavaScript 特性，TypeScript 会将它们转换成 ES2015 友好的语法，但在我们的情况下，我们没有这样做，所以 TypeScript 基本上不做任何处理。

这个模块现在可以发布到 npm 上供其他人使用，但是我们有两个问题需要解决:

1.  我们不会在代码中发布任何类型信息。这不会对我们的用户造成破坏，但这是一个错过的机会:如果我们也发布我们的类型，那么使用支持 TypeScript 的编辑器的人和/或用 TypeScript 编写应用程序的人将获得更好的体验。
2.  Node 还不支持开箱即用的 ES 模块。如果能发布一个 CommonJS 版本就太好了，这样 Node 就可以轻松工作了。ES 模块支持将出现在 Node 13 及以后，但生态系统要跟上还需要一段时间。

## 发布类型定义

我们可以通过让 TypeScript 在其编写的代码旁边发出一个声明文件来解决类型信息问题。这个文件以`.d.ts`结尾，将包含我们代码的类型信息。把它想象成源代码，除了不包含类型和实现，它只包含类型。

让我们将`"declaration": true`添加到`tsconfig.json`(在`"compilerOptions"`部分)中，并再次运行`npx tsc -p tsconfig.json`。

> 最高提示！我喜欢在我的`package.json`中添加一个脚本来进行编译，这样就减少了输入的工作量:

```
  "scripts": {
    "tsc": "tsc -p tsconfig.json"
  }
```

> 然后我可以运行`npm run tsc`来编译我的代码。

现在，您会看到每个 JavaScript 文件旁边都有一个类似的`add.d.ts`文件，如下所示:

```
// lib/add.d.ts
export declare const add: (x: number, y: number) => number;
```

所以现在当用户使用我们的模块时，TypeScript 编译器将能够选择所有这些类型。

## 发布到 CommonJS

难题的最后一部分是配置 TypeScript 来输出使用 CommonJS 的代码版本。我们可以通过创建两个`tsconfig.json`文件来做到这一点，一个针对 es 模块，另一个针对 CommonJS。不过，我们可以让 CommonJS 配置扩展我们的默认值并覆盖`modules`设置，而不是复制我们所有的配置。

让我们来创造`tsconfig-cjs.json`:

```
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "module": "CommonJS",
    "outDir": "./lib/cjs"
  },
}
```

重要的部分是第一行，这意味着该配置默认继承了`tsconfig.json`的所有设置。这很重要，因为您不想在多个 JSON 文件之间同步设置。然后，我们覆盖需要更改的设置。我相应地更新了`module`，然后将`outDir`的设置更新为`lib/cjs`，这样我们就可以输出到`lib`中的一个子文件夹。

此时，我也更新了我的`package.json`中的`tsc`脚本:

```
"scripts": {
  "tsc": "tsc -p tsconfig.json && tsc -p tsconfig-cjs.json"
}
```

现在，当我们运行`npm run tsc`时，我们将编译两次，我们的`lib`目录将如下所示:

```
lib
├── add.d.ts
├── add.js
├── cjs
│   ├── add.d.ts
│   ├── add.js
│   ├── index.d.ts
│   ├── index.js
│   ├── subtract.d.ts
│   └── subtract.js
├── index.d.ts
├── index.js
├── subtract.d.ts
└── subtract.js

1 directory, 12 files
```

这有点不整洁；让我们通过相应地更新`tsconfig.json`中的`outDir`选项，将我们的 ESM 输出更新到`lib/esm`中:

```
lib
├── cjs
│   ├── add.d.ts
│   ├── add.js
│   ├── index.d.ts
│   ├── index.js
│   ├── subtract.d.ts
│   └── subtract.js
└── esm
    ├── add.d.ts
    ├── add.js
    ├── index.d.ts
    ├── index.js
    ├── subtract.d.ts
    └── subtract.js

2 directories, 12 files
```

> 随意拥有自己的命名约定或目录结构——这正是我喜欢的，但这并不意味着你也必须这样做！

## 准备发布我们的模块

我们现在已经拥有了向 npm 发布代码所需的所有部分。最后一步是告诉 Node 和我们用户的首选捆绑器如何捆绑我们的代码。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在`package.json`中我们需要设置的第一个属性是`main`。这就是我们的主要切入点。例如，当用户写`const package = require('maths-package')`时，这个文件将被加载。

为了保持良好的兼容性，我喜欢将它设置为 CommonJS 源代码，因为在编写本文时，这是大多数工具默认期望的。所以我们把这个设为`./lib/cjs/index.js`。

接下来，我们将设置`module`属性。这是应该链接到我们包的 ES 模块版本的属性。支持它的工具将能够使用我们这个版本的包。所以这个应该设置为`./lib/esm/index.js`。

接下来，我们将向我们的`package.json`添加一个`files`条目。在这里，我们定义了发布模块时应该包含的所有文件。我喜欢使用这种方法来明确地定义当我们的最终模块被推送到 npm 时，我希望它包含哪些文件。

这让我们能够保持模块的大小——例如，我们不会发布我们的`src`文件，而是发布`lib`目录。如果您在`files`条目中提供了一个目录，那么默认情况下它的所有文件和子目录都会被包含在内，所以您不必列出所有的目录。

> 最高提示！如果你想知道哪些文件将包含在你的模块中，运行`npx pkgfiles`获得一个列表。

我们的`package.json`现在增加了三个字段:

```
  "main": "./lib/cjs/index.js",
  "module": "./lib/esm/index.js",
  "files": [
    "lib/"
  ],
```

还有最后一步。因为我们正在发布`lib`目录，所以我们需要确保当我们运行`npm publish`时，`lib`目录是最新的。[NPM 文档中有一节是关于如何做到这一点的](https://docs.npmjs.com/misc/scripts#use-cases)——我们可以使用`prepublishOnly`脚本。当我们运行`npm publish`时，这个脚本会自动运行:

```
"scripts": {
  "tsc": "tsc -p tsconfig.json && tsc -p tsconfig-cjs.json",
  "prepublish": "npm run tsc"
},
```

> 请注意，还有一个名为`prepublish`的脚本，选择哪个会有点混乱。[NPM 文档提到了这个](https://docs.npmjs.com/misc/scripts#deprecation-note) : `prepublish`被弃用，如果你想只在发布时运行代码，你应该使用`prepublishOnly`。

这样，运行`npm publish`将运行我们的 TypeScript 编译器并在线发布模块！我在`@jackfranklin/maths-package-for-blog-post`下发布了这个包，虽然我不建议你使用它，但是你可以[浏览文件看一看](https://unpkg.com/browse/@jackfranklin/maths-package-for-blog-post@0.1.0/)。我还把所有代码上传到了 [CodeSandbox](https://codesandbox.io/s/xisgq) 中，所以你可以随意下载或破解。

## 结论

就是这样！我希望本教程已经向您展示了，使用 TypeScript 并不像第一次出现时那么令人畏惧，并且稍加调整，就有可能让 TypeScript 毫不费力地输出您可能需要的许多格式。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.