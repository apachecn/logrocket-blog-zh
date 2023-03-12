# 如何检测前端项目中的死代码

> 原文：<https://blog.logrocket.com/how-detect-dead-code-frontend-project/>

在一个项目中有大量的死代码会对你的应用不利，原因有很多。死代码使得代码库更难大规模维护。这也有可能在开发团队中造成混乱，不知道哪些代码是相关的，哪些是正在处理的，哪些是可以安全忽略的。

避免这些陷阱的最好方法是确保我们有适当的工具，允许我们可靠地和自动地检测死代码。

在本文中，我们将介绍在前端项目中检测死代码的三种方法，外加一个额外的附加部分:

## 使用 ESLint 检测并删除死代码

ESLint 可能是使用最广泛的 JavaScript linter，在 npm 上每周有近 2500 万次下载。它是——或者至少应该是——每个 JavaScript 项目不可或缺的一部分。在[许多其他有用的东西](https://blog.logrocket.com/using-prettier-eslint-automate-formatting-fixing-javascript/)中，ESLint 允许我们用其恰当命名的`no-unused-vars` [规则](https://eslint.org/docs/rules/no-unused-vars)来检测文件中未使用的变量。

这条规则从两个方面防止我们引入死代码。首先，它会告诉我们是否声明了一个没有在文件的其他地方使用的变量:

```
// Variable is defined but never used
let x;

// Write-only variables are not considered as used.
let y = 10;
y = 5;

// A read for a modification of itself is not considered as used.
let z = 0;
z = z + 1;

```

其次，它会告诉我们函数中是否有未使用的参数:

```
// By default, unused arguments cause warnings.
(function(foo) {
    return 5;
})();

// Unused recursive functions also cause warnings.
function fact(n) {
    if (n < 2) return 1;
    return n * fact(n - 1);
}

// When a function definition destructures an array, unused entries from the array also cause warnings.
function getY([x, y]) {
    return y;
}

```

要启用该规则，只需将它添加到 ESLint 配置文件中的`rules`对象中。同一文件中的`"extends": "eslint:recommended"`属性也启用了该规则。在这里找到更多关于 ESLint 规则以及如何配置它们的信息[。上面描述的大多数行为都是可配置的，并且可以调整以适应特定的项目需求。](https://eslint.org/docs/rules/)

ESLint 规则是一个优秀的死代码检测工具。强烈建议将其用于本地开发和持续集成管道中。

但是这个规则本身并不足以确保我们检测到项目中所有的死代码。怎么会这样

绝大多数现代前端项目使用 [ECMAScript](https://blog.logrocket.com/how-to-use-ecmascript-modules-with-node-js/) 模块通过导入和导出来组织和重用代码。这意味着，即使一个变量或函数没有在文件中使用，只要它被导出，它就不再被认为是未使用的。

```
// Variable is defined but never used
const x = 10

// The no-unused-vars rule is not broken
export const y = 20

```

我们似乎已经达到了这个 ESLint 规则的极限。那么，我们如何知道所有导出的代码都被导入并在项目中的其他地方使用，因此不是死的呢？

我们可以继续使用我们的 linter，并利用一个名为`eslint-plugin-import`的插件，更具体地说，它的[规则](https://github.com/import-js/eslint-plugin-import/blob/main/docs/rules/no-unused-modules.md) `no-unused-modules`，它允许我们在没有导出的情况下检测两个模块，以及在其他模块中没有导入的导出。

要设置它，只需安装它:

```
npm install eslint-plugin-import --save-dev

```

然后，将插件和规则添加到 ESLint 配置文件中:

```
"plugins: {
  ...otherPlugins,
  'import',
},
"rules: {
  ...otherRules,
  "import/no-unused-modules": [1, {"unusedExports": true}]
}

```

虽然这个插件非常优秀，并且受到了积极的支持，但是仍然有很好的理由使用另一种方法。例如，如果我们的项目根本没有使用 ESLint 怎么办？也许我们希望只在 CI 管道中检测未使用的导入和导出，以使我们的 linter 更轻，从而加快本地开发，并且我们不希望为两种环境支持不同的配置。或者也许我们在这个设置中遇到了一些[未解决的问题](https://github.com/import-js/eslint-plugin-import/issues?q=is%3Aissue+is%3Aopen+no-unused-modules)。

不管是什么情况，都有一个替代的解决方案:webpack

## 使用 webpack 进行死代码检测

Webpack 是一个模块捆绑器，广泛应用于现代网络应用中。它的主要目的是[捆绑 JavaScript 文件以便在浏览器中使用。](https://blog.logrocket.com/guide-performance-optimization-webpack/)本质上，webpack 用于创建应用程序的依赖图，并将项目的每个模块组合成一个包。这使得该工具非常适合检测未使用的导入和导出，即死代码。

虽然 webpack 会自动尝试[删除它生成的包](https://blog.logrocket.com/tree-shaking-json-files-webpack/)中未使用的代码(在这里了解更多关于树摇动[的信息)，但有一个方便的](https://webpack.js.org/guides/tree-shaking/)[插件](https://www.npmjs.com/package/webpack-deadcode-plugin)可以帮助我们在编写代码的过程中检测未使用的文件和代码导出——web pack-deadcode-plugin。

要将插件添加到您的项目中，请先安装它:

```
npm install webpack-deadcode-plugin --save-dev

```

现在将它添加到您的 webpack 配置文件中，如下所示:

```
const DeadCodePlugin = require('webpack-deadcode-plugin');

const webpackConfig = {
  ...
  optimization: {
    usedExports: true,
  },
  plugins: [
    new DeadCodePlugin({
      patterns: [
        'src/**/*.(js|jsx|css)',
      ],
      exclude: [
        '**/*.(stories|spec).(js|jsx)',
      ],
    })
  ]
}

```

该插件将自动报告未使用的文件和未使用的出口到您的终端。这是一个有用的工具，可以增强开发过程，并确保我们不会在项目中引入死代码。

但它有其局限性。

首先，工具在终端中的输出可能会丢失或难以解析，这取决于在同一位置显示的其他输出。这会导致使用不便。

其次，将它包含在您的 CI 渠道中可能会稍微困难一些。原因是，根据[文档](https://www.npmjs.com/package/webpack-deadcode-plugin):

> 该插件将向您的终端报告未使用的文件和未使用的导出，但这些不是您的 webpack 构建过程的一部分，因此，它不会使您的构建失败

最后，也许是最重要的，当在 TypeScript 项目中使用插件时，它的输出可能不正确。

幸运的是，TypeScript 本身可以用于死代码检测！

## 使用 TypeScript 检测死代码

在我们的项目中使用 TypeScript 有许多优点。其中之一是它为我们提供了一种检测死代码的简单方法。

首先，我们可以配置 TypeScript，不允许使用未使用的[局部变量](https://www.typescriptlang.org/tsconfig#noUnusedLocals)和[函数参数](https://www.typescriptlang.org/tsconfig#noUnusedParameters)。这类似于上面的`no-unused-vars` ESLint 规则。为了通过 TypeScript 执行这些规则，我们可以将它们添加到我们的`tsconfig`文件中:

```
{
  "compilerOptions": {
    ...otherOptions,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
  }
}

```

但是，即使有这些检查，我们仍然面临未使用出口的问题。让我们用`[ts-prune](https://www.npmjs.com/package/ts-prune)`来做这项工作。它易于使用，需要很少的配置(如果有的话)。

要使用它，我们需要安装它:

```
npm install ts-prune --save-dev

```

然后，将它的脚本添加到我们的`package.json`文件中:

```
{
  "scripts": {
    "find-deadcode": "ts-prune"
  }
}

```

现在，每次我们在项目中运行`npm run find-deadcode`时，我们都会有一个所有未使用的导出列表。

请注意，上面的脚本将检测未使用的导出，即使它们在模块内部使用。要退出此行为，请修改您的脚本以排除这些行为:

```
"find-deadcode": "ts-prune | grep -v '(used in module)'"

```

最后，如果您想在 CI 中使用`ts-prune`,您将需要更改退出代码，以便在有未使用的导出时出现错误。下面是最后的修改:

```
"find-deadcode": "ts-prune | (! grep -v 'used in module')"

```

在给出的选项中，TypeScript 可以最严格地强制执行死代码检测。因此，如果您正在处理一个 Typescript 项目，使用特定于 TypeScript 的工具——包括`tsconfig`、`@typescript-eslint/eslint-plugin`(与 ESLint 结合)和`ts-prune`中的编译选项——通常会是最佳的方法。

## 额外好处:使用 depcheck 检测未使用的依赖项

当我们谈到死代码检测时，让我们简单地讨论一下如何确保我们的项目中没有未使用的依赖项。让我们使用 [depcheck](https://www.npmjs.com/package/depcheck) ，一个分析项目中依赖关系的工具。它可以告诉我们:

*   如何使用每个依赖项
*   哪些依赖是无用的
*   `package.json`中缺少哪些依赖关系

安装时使用:

```
npm install -g depcheck

```

接下来，运行检查。

```
npx depcheck

```

Depcheck 使用一个`special`组件，它允许我们识别常规导入/导出流之外使用的依赖项。这些包括在配置文件、npm 命令、脚本等中使用的依赖关系。

## 结论

在本文中，我们探索了不同的方法来检测前端项目中的死代码。这些方法可以互换使用，也可以相互结合使用。通常情况下，选择我们的理想设置很大程度上取决于特定的用例。

同样重要的是要注意，这些并不是现有的唯一死代码检测工具。它们是根据底层工具(ESLint、webpack 和 TypeScript)的流行程度和受欢迎程度选择的。但是，根据项目的特殊性，最佳解决方案可能不在这个列表中。

如果你觉得这篇文章有用，[访问我的博客](https://blog.whereisthemouse.com/)和[在 Twitter 上关注我](https://twitter.com/iva_kop)了解更多技术内容。

编码快乐！✨

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.