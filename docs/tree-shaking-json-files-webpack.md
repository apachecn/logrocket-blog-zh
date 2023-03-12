# 用 webpack 摇树 JSON 文件

> 原文：<https://blog.logrocket.com/tree-shaking-json-files-webpack/>

树抖动对开发人员来说至关重要，因为它删除了死代码和在最终产品构建中占用空间的未使用的函数、变量和方法。

像 [rollup.js](https://rollupjs.org/) 和 [webpack](https://webpack.js.org/) 这样的流行工具在将多个 JavaScript 文件编译成单个文件时会执行开箱即用的树抖动。

在前端开发中，模块和包以各种方式进行管理，以达到最佳效果。在开发过程中，您的代码将被分割成许多小模块。

当您部署应用程序时，您应该将这些模块捆绑到一个或者几个非常大的文件中，最好使用 [webpack](https://blog.logrocket.com/tag/webpack/) 。

你会问为什么？以下是几个原因。

*   与多个非捆绑的 JavaScript 文件相比，捆绑的文件更容易压缩
*   当加载所有模块时，最好从较少的文件而不是几个文件中加载它们
*   绑定时，未使用的导出会被删除(例如，树抖动)，这有助于节省空间并减小最终的构建大小

## 树摇动 JSON 文件

假设您有一个名为`strings.json`的 JSON 文件。

```
{
  "usedString1": "Hello world!",
  "usedString2": "Hello world again!",
  "unusedString": "I'm never used in the code ever"
}

```

现在，尝试用 JavaScript `index.js`访问它的内容，就像这样:

```
const strings = require("./strings.json");
console.log(strings.usedString1, strings.usedString2);

```

您会看到我们在整个 JSON 文件中只使用了一个键，即`usedString`。`unusedString`是死代码，所以我们不使用它。然而，当您导入/要求`strings.json`时，死代码随之而来，并进入您的最终版本，占用大量空间并不必要地增加您的文件大小。

目前，当您与 webpack 捆绑在一起时，默认情况下不会删除死的 JSON 代码。必须使用 webpack 插件，一般是[web pack-JSON-access-optimizer](https://github.com/privatenumber/webpack-json-access-optimizer)。

让我们看看这个插件树是如何使用 JSON 文件动摇你的代码的。

插件首先将 JSON 结构转换成一个数组，所以`strings.json`现在看起来像这样:

```
["Hello world!", "Hello world again!"]

```

插件编译 JavaScript 代码以适应这个新的数组结构，所以`index.js`现在看起来像这样:

```
const strings = require("./strings.json");
console.log(strings[0], strings[1]);

```

注意在`strings.json`中，插件删除了未使用的值`unusedString`。

这是我制作的一个 GitHub 演示。首先，克隆它。

```
git clone https://github.com/KumarAbhirup/webpack-treeshaking-json

```

现在，运行`yarn`、`yarn build`和`yarn start`。

如果您检查`webpack.config.js`，您会发现它非常简单，除了从第 16 行到第 33 行。

```
const path = require('path');
const DIST_DIR = path.resolve(__dirname, 'dist');

const { JsonAccessOptimizer } = require('webpack-json-access-optimizer');
const { ProvidePlugin } = require('webpack');

let config = {
    entry: './src/index.js',
    output: {
        filename: 'bundle.js',
        path: DIST_DIR
    },
    resolve: {
        extensions: ['.js', '.json']
    },
    module : {
        rules: [
            {
                test: /strings\.json$/,
                use: [
                    'webpack-json-access-optimizer', 
                ],
                type: 'json'
            }
        ]
    },
    plugins: [
        new ProvidePlugin({
            $t: './$tProvider'
        }),
        new JsonAccessOptimizer({
            accessorFunctionName: '$t', // i18n function name
        })
    ],
    optimization: {
        usedExports: false,
    },
    devtool: 'source-map'
};
module.exports = config;

```

仔细看看这里:

```
module : {
    rules: [
        {
            test: /strings\.json$/,
            use: ['webpack-json-access-optimizer'],
            type: 'json'
        }
    ]
},
plugins: [
    new ProvidePlugin({
        $t: './$tProvider'
    }),
    new JsonAccessOptimizer({
        accessorFunctionName: '$t', // i18n function name
    })
],

```

注意，我们告诉 webpack 使用`webpack-json-access-optimizer`插件解析`strings.json`,这样我们就可以对 JSON 文件进行树抖动。

在`plugins`部分，我们让`$t`函数全局可用，这样所有文件都可以访问`strings.json`，比如:`$t('usedString1')`。

现在，看看`./src/$tProvider.js`。

```
const t = require("./strings.json");
const $t = keyString => {
  return t?.[keyString];
};
module.exports = $t;

```

它从`strings.json`获取所有的键-值对，然后导出一个默认函数，该函数通过接受一个键字符串来返回值。

让我们看看我们的`./src/index.js`文件。我们正在使用在全局范围内可用的`$t`函数。

```
console.log($t("usedString1"), $t("usedString2"));

```

现在，如果您编写`yarn build && yarn start`代码，您应该会看到这样的输出。

```
➜  webpack-treeshaking-json git:(main) yarn start
yarn run v1.19.1
$ node dist/bundle.js
Hello world! Hello world again!
✨  Done in 0.26s.

```

## 检查编译的代码

让我们来看看`./dist/bundle.js`中编译的代码:

```
(()=>{var r,o={500:(r,o,e)=>{const t=e(46);r.exports=r=>t?.\[r]},46:r=>{"use strict";r.exports=JSON.parse('["Hello world!","Hello world again!"]')}},e={};r=function r(t){var s=e[t];if(void 0!==s)return s.exports;var l=e[t]={exports:{}};return o[t\](l,l.exports,r),l.exports}(500),console.log(r(0),r(1))})();
//# sourceMappingURL=bundle.js.map

```

您将看到它只使用了代码中实际使用的`strings.json`中的键值对，并且省略了`unusedString`。这样，您可以在最终版本中节省重要的空间。

## 晃动树并移除未使用的导出

让我们看看这个 JavaScript 文件。

```
const sum = (a, b) => {
  return a + b;
};

const multiply = (a, b) => {
  return a * b;
};

const divide = (a, b) => {
  return a / b;
};

console.log(sum(1, 9));

module.exports = { sum, multiply };

```

你可以看到`divide`函数出现在代码中，但是没有在任何地方被使用，但是像`sum`和`multiply`这样的函数在代码中被使用。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`sum()`用在了`console.log()`语句中，当我们导出该函数时，也用在了`module.exports`中。`multiply()`用于导出函数后的`module.exports`。

如果编译时不进行树抖动，那么`divide`函数将会出现在编译后的代码中，即使它没有被使用，也会占用空间。

## 额外学习

### 使用 webpack 消除未使用的导出

使用 webpack 消除未使用的导出也有助于节省空间，大大减少最终的构建大小，并缩短页面加载时间。

将此设置用于您的 webpack 配置:

```
optimization: {
    usedExports: true
}

```

`webpack.config.js`现在应该是这样的:

```
const path = require('path');
const DIST_DIR = path.resolve(__dirname, 'dist');

const { JsonAccessOptimizer } = require('webpack-json-access-optimizer');
const { ProvidePlugin } = require('webpack');

let config = {
    entry: './src/index.js',
    output: {
        filename: 'bundle.js',
        path: DIST_DIR
    },
    resolve: {
        extensions: ['.js', '.json']
    },
    module : {
        rules: [
            {
                test: /strings\.json$/,
                use: [
                    'webpack-json-access-optimizer', 
                ],
                type: 'json'
            }
        ]
    },
    plugins: [
        new ProvidePlugin({
            $t: './$tProvider'
        }),
        new JsonAccessOptimizer({
            accessorFunctionName: '$t', // i18n function name
        })
    ],
    optimization: {
        usedExports: true,
    },
    devtool: 'source-map'
};

module.exports = config;

```

注意上面的`usedExports: true`。有了它，未使用的函数和变量就不会出现在最终的构建和编译代码中。

### 摇树 CSS

如果您之前已经配置了 webpack 来导入 JS 中的 CSS 模块，那么您可能想要学习如何对正在导入的 CSS 进行树抖动。CSS 代码经常不被使用，所以这将有助于优化你的应用程序。

假设您的 webpack 配置中有一个 CSS 规则:

```
{
  test: /\.css$/,
  use: ['style-loader', 'css-loader'],
}

```

你只需要给它添加`sideEffects: true`属性。

```
{
  test: /\.css$/,
  use: ['style-loader', 'css-loader'],
  sideEffects: true
}

```

这样做之后，假设您的 webpack config `mode: 'production'`已经设置好了，它应该会在编译过程中对您导入的所有 CSS 文件进行树抖动——让您获得一个巨大的包大小缩减，使应用程序可以投入生产！

## 结论

当你编译代码的时候，抖动你的代码是至关重要的。Webpack 确实为 JavaScript 导出执行树抖动，但是它不会对 JSON 文件执行树抖动，除非您使用[web pack-JSON-access-optimizer](https://github.com/privatenumber/webpack-json-access-optimizer)。在你的项目中使用这些技术可以节省你的空间并优化你的应用。编码快乐！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)