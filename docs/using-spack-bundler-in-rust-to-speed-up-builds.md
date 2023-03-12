# Rust - LogRocket 博客中的 spack bundler 加速 JavaScript 构建

> 原文：<https://blog.logrocket.com/using-spack-bundler-in-rust-to-speed-up-builds/>

因为大多数 JavaScript bundlers 和 transpilers 都是用 JavaScript 本身编写的，所以它们往往缺乏效率。然而，如果 bundlers 和 transpilers 是用更快、更好编译和优化的语言编写的，比如 Python 或 Rust，它们可以运行得更快。

在本文中，我们将讨论如何在 Rust 中使用带有`swc-project`的`spack`捆绑器来加速 JavaScript 构建。

## 什么是`swc-project`？

是一个用 Rust 写的 bundlers 和 transpilers 的集合。`swc-project`可以 transpile Typescript，JSX，TSX，以及其他版本的 JavaScript。和巴别塔一样，SWC 是一个阅读源代码的工具；然而， [SWC 的工作速度比巴别塔](https://blog.logrocket.com/why-you-should-use-swc/)快大约 20 倍。

`swc`可以使用`.swcrc`文件进行配置；看不同配置[这里](https://swc.rs/docs/configuring-swc)。

## 通过`swc`与`spack`进行传输

`spack`是一个 JS 捆绑器，支持通过`swc`进行传输。它是`webpack`的的[替代品，支持死代码消除和树抖动。](https://blog.logrocket.com/benchmarking-bundlers-2020-rollup-parcel-webpack)

`spack`纯用 Rust 写，Deno 用；Deno 最近在`spack`的基础上增加了新的 TypeScript 捆绑和传输选项。

`spack`内部依靠`swc_bundler`板条箱进行资产捆绑。

```
const { config } = require("@swc/core/spack");
const path = require("path");
module.exports = config({
 // starting point
  entry: {
    web: path.join(__dirname, "src", "index.ts"),
  },
 // output file
  output: {
    path: path.join(__dirname, "dist"),
  },
  module: {},
});

```

## 配置捆扎机和传输机

`spack`使用`.swcrc`文件为传输器配置，使用`spack.config.js`文件为捆扎机配置。

要了解它是如何工作的，创建一个名为`spack-demo`的目录。在`spack-demo`文件夹中，添加`index.js`文件和多个演示文件。将`spack.config.js`和`.swcrc`文件添加到目录中。

文件结构应该是这样的。

```
├── index.js
├── spack.config.js
└── .swcrc

```

使用`npm i @swc/core @swc/cli @swc/helpers`安装所需的软件包。

对于捆绑:`spack.config.js`

```
// importing config creator
const { config } = require("@swc/core/spack");

// import path module
const path = require("path");

// export config
module.exports = config({

// start file
  entry: {
    build: path.join(__dirname, "index.js"),
  },

// output file
  output: {
    path: path.join(__dirname),
  },
  module: {},
});

```

用于运输:`.swcrc`

```
{
    "jsc": {
// transpile to es2015
        "target": "es2015",
        "parser": {
// file use ecmascript
            "syntax": "ecmascript",
        }
    }
}

```

## 使用`spack`捆绑 JavaScript

我们之前添加的样板代码可以用于 React.js 和 Node.js 等大多数代码库所需的基本绑定和传输。

对于一个演示，用 index.js 创建一个名为`mod`的目录，并在根目录中导入`index.js`。文件结构应该是这样的。

```
├── index.js
├── mod
│   └── index.js
├── spack.config.js
└── .swcrc

```

请注意，`mod/index.js`和`index.js`包含用于演示`spack`捆绑功能的伪代码:

`mod/index.js`

```
export const sum=(a,b)=>a+b;

```

`index.js`

```
import {sum as add} from "./mod"
console.log(add(1,2));

```

`build.js`(该文件由`spack`生成)

```
var sum = function(a, b) {
    return a + b;
};
console.log(sum(1,2));

```

`spack`根据`.swcrc`文件中的`target`键，使用`swc`转换成多个版本的 JavaScript。`swc`支持 ES5、ES2015、ES2016、ES2018、ES2019 和 ES2020。

在我们之前的演示中，我们编写了由新版 es 2015 Javascript 支持的代码，但是使用`spack`，我们可以将代码转换到旧版浏览器支持的旧版 Javascript 中。几乎所有的浏览器都支持 JavaScript 的 ES5 版本。

要了解它是如何工作的，将`target`改为`es5`，并给`mod/index.js`添加一些新的代码。

`mod/index.js`

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
export const sum=(a,b)=>({...a,...b})

```

`.swcrc`

```
{
    "jsc": {
// transpile to es5
        "target": "es5",
        "parser": {
// file use ecmascript
            "syntax": "ecmascript",
        }
    }
}

```

您的`build.js`输出文件现在将如下所示:

```
function _defineProperty(obj, key, value) {
    if (key in obj) {
        Object.defineProperty(obj, key, {
            value: value,
            enumerable: true,
            configurable: true,
            writable: true
        });
    } else {
        obj[key] = value;
    }
    return obj;
}
function _objectSpread(target) {
    for(var i = 1; i < arguments.length; i++){
        var source = arguments[i] != null ? arguments[i] : {
        };
        var ownKeys = Object.keys(source);
        if (typeof Object.getOwnPropertySymbols === "function") {
            ownKeys = ownKeys.concat(Object.getOwnPropertySymbols(source).filter(function(sym) {
                return Object.getOwnPropertyDescriptor(source, sym).enumerable;
            }));
        }
        ownKeys.forEach(function(key) {
            _defineProperty(target, key, source[key]);
        });
    }
    return target;
}
var sum = function(a, b) {
    return _objectSpread({
    }, a, b);
};
console.log(sum({
}, {
}));

```

当您仔细查看`build.js`输出文件时，您会发现它现在包含一些额外的代码来支持新的 JS 特性(比如 spread operator)。所有额外的代码都是由`spack`注入的，这样我们的代码可以在新旧浏览器上完美运行，包括那些只支持 ES5 版本 JavaScript 的浏览器。

## 捆绑多个 JavaScript 条目

当处理多个包时，可以通过缓存公共文件或模块并避免重复工作来优化构建。`spack`同时支持多个捆绑包，跨捆绑包缓存通用文件。

`spack.config.js`

```
const { config } = require("@swc/core/spack");
const path = require("path");
module.exports = config({
  entry: {
    web: path.join(__dirname, "src", "index.js"),
    node: path.join(__dirname, "src", "node.js"),
  },
  output: {
    path: path.join(__dirname, "dist"),
  },
  module: {},
});

```

## 捆绑打字稿

`spack`由 Deno 用于传输和捆绑打字稿。它完全支持使用`swc`的 TypeScript transpilation 和 bundling。`swc`编译通过了 TypeScript repo 的所有测试。将`syntax`键改为 TypeScript 可以支持 TypeScript。

```
{
    "jsc": {
// transpile to es5
        "target": "es5",
        "parser": {
// file use ecmascript
            "syntax": "typescript",
        }
    }
}

```

## 结论

`spack`还处于早期阶段，但在它可以生产之前，它仍然可以在 Rust `swc-project`中用于开发环境中的捆绑。`spack`也可以作为一个基础库，用来创建一个更健壮的具有多种特性的捆绑器，就像 Deno 用来捆绑打字稿一样。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.