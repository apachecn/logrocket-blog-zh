# 在 Node.js - LogRocket 博客中使用 ES 模块

> 原文：<https://blog.logrocket.com/es-modules-in-node-today/>

***编者按:**本文更新于 2021 年 3 月 3 日。*

## 介绍

> "在软件可以重用之前，它首先必须是可用的."–[拉尔夫·约翰逊](https://en.wikipedia.org/wiki/Ralph_Johnson_(computer_scientist))

[模块](https://en.wikipedia.org/wiki/Module)是软件程序的独立构件。它们基本上是一种设计模式，在编程语言中实现了[m](https://en.wikipedia.org/wiki/Modular_programming)模块化设计的特性。模块系统支持多种语言，并且非常流行，因为处理、打包和管理依赖关系的方式决定了处理大型且不断增长的源代码的难易程度。

在模块化设计中，为了可重用性、灵活性和降低复杂性，与特定特性或功能相关的业务逻辑以标准化格式进行打包(模块化)。由于没有全局变量或共享状态，这种设置提供了一个松散耦合的系统，因为有一个平滑的通信接口。

尽管模块的概念根据语言的不同而有很大的不同，但它们类似于 Java 等语言中的 T2 命名空间的概念。模块通过将代码库分成可重用的组件来实现代码组织，这样每个组件执行单独的功能，并且可以组合或组成更大的功能或整个应用程序。

在 Node.js 中，[模块系统](https://nodejs.org/docs/latest/api/modules.html#modules_modules)已经从早期采用 [CommonJS](https://requirejs.org/docs/commonjs.html) 走了很长一段路。今天， [ECMAScript 模块](https://nodejs.org/dist/latest-v13.x/docs/api/esm.html#esm_ecmascript_modules) (ES 模块)，现在已经稳定并适合生产使用，是封装代码以在客户端和服务器端 JavaScript 中重用的官方标准。

## 目录

在本文中，我们将学习 Node 中的 ES 模块。然而，我们将简要探讨用 CommonJS 处理和组织服务器端代码的其他方法。

为什么？以便我们有一个参考点来认识 ES 模块的好处。实质上，我们将了解它试图解决的挑战，这些挑战是早期模块系统无法解决的。

我们将会看到:

*   **ES 模块简介** —在这里，我们以激动人心的方式介绍 ES 模块
*   **ES 模块简史** —在这里，我们了解从早期的模块系统到 ES 模块的过渡。我们还将简要考察这些模块系统之间的互操作性
*   **在节点**中增加对 es 模块的支持—在这里，我们了解如何在节点中增加对 ES 模块的支持。我们还将学习如何迁移旧的代码库来开始使用 ES 模块
*   **比较和对比特性** —在这里，我们将了解这两种模块系统的特性以及它们之间的比较
*   **最后，ES 模块向前移动**

## 先决条件

为了轻松地学习本教程，建议安装最新版本的 Node.js。关于如何操作的说明可在[节点文档](https://nodejs.org/en/)中找到。

此外，为了更好地理解上下文，读者可能需要对 Node 中的 [CommonJS](https://requirejs.org/docs/commonjs.html) 模块系统有相当的了解。对于今天学习 Node.js 模块系统或者在自己的节点项目中应用 ES 模块的新人来说，同样是欢迎的。

## 什么是 ES 模块？

随着 Node 版本 15.3.0 的发布(目前版本为 15.11.0)， [ES 模块](https://nodejs.org/api/esm.html#esm_introduction)现在可以在没有实验标志的情况下使用，因为它们现在是稳定的，并且与 NPM 生态系统兼容。关于稳定性指数的细节可以在 node.js ESM 文档中找到[这里](https://nodejs.org/api/documentation.html)。在 ES 模块中，使用`import`和`export`关键字来定义模块，而不是使用 CommonJS 中的`require()`函数。下面是它们的使用方法:

```
export function sayLanguage(language) {
    console.log(`I love ${language}!`);
  }

//f.js

import {sayLanguage} from './f.js';

console.log(sayLanguage('JavaScript'));

//g.js

{
  "name": "esm",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "type": "module",
  "author": "",
  "license": "ISC"
}
//package.json

```

> **注意**:读者应该注意上面 package.json 文件中的 type 字段。为了能够加载一个 ES 模块，我们需要在这个文件中设置“type”:“module ”,或者，作为替代，我们可以使用。mjs 文件扩展名。js 文件扩展名。此外，从节点版本 12.7.0 和 13.2.0，加载 ECMAScript 模块不再需要我们传递任何命令行标志。

```
Alexs-MacBook-Pro:esm terra-alex.$ node g.js
I love JavaScript!
undefined
Alexs-MacBook-Pro:esm terra-alex.$
```

上面显示了运行我们的代码的结果/终端输出。

## 今天在 Node 中增加了对 ES 模块的支持

这种支持以前是在`--experimental-module`旗的后面。目前，从版本 13.14.0 到 14.0.0，不再需要这样做，实现现在是稳定的，现在可以与早期的 commonJS 模块系统一起使用。

以`.mjs`或`.js`扩展名结尾的文件(最近的`package.json`文件带有`field`类型)被视为 es 模块，如前所示。因此，本质上，当我们在与上面的`package.json`相同的文件夹中运行`node g.js`时，该文件被视为一个 ESM。此外，如果我们将字符串参数传递给带有标志`--input-type=module`的 Node.js 标准输入，那么它就是一个 ESM。更多细节可以在[这里](https://nodejs.org/api/packages.html#packages_determining_module_system)找到。

需要注意的是，在 ESM 中，如果没有父`package.json`文件中的`type`字段或上面指定的其他方式，Node 会抛出如下所示的错误:

```
(node:2844) Warning: To load an ES module, set "type": "module" in the package.json or use the .mjs extension.(Use `node --trace-warnings ...` to show where the warning was created). Also, as an aside, we cannot make use of import statements outside of modules.
```

## 包范围

如前所述，由父`package.json`文件及其下所有文件夹中的`type`标志定义的包范围存在于该包的当前范围中。此外，以`.mjs`扩展名结尾的文件总是作为 es 模块加载，而不管那个包的范围。

同样，所有其他形式的没有扩展名并且在父`package.json`文件中没有`type`标志的文件都被视为 CommonJS。此外，以`.cjs`扩展名结尾的文件被视为 CJS 模块，而不考虑包的范围。

重要的是要知道，因为 Node.js 现在同时支持 ES 模块和 commonJS 模块，所以包作者需要始终确保他们的 package.json 文件中的`type`字段始终被包含，而不管模块类型如何。这允许向后兼容；例如，在 Node.js 的默认类型改变的情况下，Node 将知道如何处理或解释我们程序中的文件。更多详情[此处](https://nodejs.org/api/packages.html#packages_determining_module_system)。

## ES 模块中的导入和导出语法

在 ES 模块中，说明符类似于在关键字`from`之后使用的基于字符串的文件路径。有两种[算法](https://nodejs.org/api/esm.html#esm_resolution_algorithm)来加载一个 ES 模块说明符和确定一个解析的 URL 的模块格式。下面显示了一个示例:

```
import {cat} from 'animals';
```

例如，本例中的`animal`说明符是一个 npm 包。引用说明符的其他方式包括绝对和相对文件路径或 URL，以及其他包中的路径。文档的[部分](https://nodejs.org/api/esm.html#esm_import_specifiers)给出了示例。

> **注意** : Node.js 内置模块现在可以由 14.13.1 版的绝对 URL 字符串加载或引用，当使用 import 关键字时，必须提供文件扩展名来解析相对或绝对 URL 说明符。

虽然`import`语句只允许在 ES 模块中使用，但是它们可以引用 ESM 或 CommonJS 模块。例如:

```
import packageMain from 'commonjs-package'; // Works

import { method } from 'commonjs-package'; // Errors
```

> **注意** : [引用 JSON 或 Wasm 等其他类型文件的导入语句](https://nodejs.org/api/esm.html#esm_import_statements)仍然是实验性的，需要分别传递`--experimental-json-modules`和`--experimental-wasm-modules`标志，因为它们只在 commonJS 中受支持。从节点版本 14.8.0 开始，我们能够在不传递 CLI 标志的情况下利用顶级 await。包括在 ESM 的异步函数(也称为顶级 await)之外使用 await 关键字的详细信息，可以在[这里](https://nodejs.org/api/esm.html#esm_experimental_json_modules)找到。

对于 ES 模块中的导出，我们可以利用以下内容:

> **注意**:所有内置节点包都支持上述所有类型的导出(命名，默认)。自节点版本 14.13.0 起，增加了对在 ESM 中检测名为 exports 的 CommonJS 的支持。

## 包入口点

在项目的`package.json`文件中，现在有两个字段可以定义包的入口点:`main`和`exports`。虽然`main`字段仅定义了包的主入口点，但是`exports`字段提供了一种替代方式，在此可以定义主入口点，同时还提供了封装包的好处。

包中的模块文件可以通过在包名后附加一个路径来访问。另一种方式是如果包的`package.json`包含一个`exports`字段，包内的文件只能通过`exports`对象中定义的路径访问。

例如，要为一个包设置主入口点，建议在包的`package.json`文件中定义`exports`和`main`。更多细节可以在[文档](https://nodejs.org/api/esm.html#esm_package_entry_points)中找到。

## CommonJS 模块系统

在引入 ES 模块之前，社区非常依赖 CommonJS 来打包服务器端 JavaScript 代码。在 CommonJS 模块系统中，每个文件都被视为一个模块，它通过使用 [`exports`对象](https://github.com/nodejs/ecmascript-modules/blob/modules-lkgr/doc/api/modules.md#exports)来公开一组 API(通过一个定义良好的接口)。为了更好地理解这一点，下面是一个使用由模块系统创建的对象的示例:

```
function sayName(name) {
    console.log(`My name is ${name}.`)
  };

function sayAge(age){
  console.log(`I'm ${age} years old.`)
  };

module.exports = {sayName, sayAge};
//a.js
```

要使用这些函数(作为模块导入到不同的文件中)，我们可以使用 [`require`函数](https://github.com/nodejs/ecmascript-modules/blob/modules-lkgr/doc/api/modules.md#requireid)。基于公开的 API 的模块类型，它接受由相对或绝对路径或名称指定的模块标识符(ID ),如下所示:

```
const {sayName, sayAge} = require('./a') 
// assuming a.js is in the same folder path

console.log(sayName('Alex')) // My name is Alex.

console.log(sayAge(25)) // I'm 25 years old.

//b.js
//TO RUN THE CODE SAMPLE TYPE: $ node b.js on your terminal
```

正如我们在上面看到的，`require`对象返回/导入从`a.js`文件导出的模块内容。为了了解更多关于`module`、`export`和`require`关键字的[实现](https://github.com/nodejs/node/blob/7c63bc6540f4ad21f911f38f8708ed988f433ce7/lib/internal/modules/cjs/loader.js#L166)，我们可以在这里查看模块包装[。](https://github.com/nodejs/ecmascript-modules/blob/modules-lkgr/doc/api/modules.md#the-module-wrapper)

CommonJS 规范也可以在[这里](https://github.com/commonjs/commonjs/blob/master/docs/specs/modules/1.0.html.markdown)获得。规范[强调了](https://github.com/commonjs/commonjs/blob/master/docs/specs/modules/1.0.html.markdown#commonjs-modules)一个模块系统为了支持其他模块系统并与之互操作而必须具备的最少特性。

CommonJS 实现允许定义文件加载的结构。在这种方法中，同步加载或解析其他文件所需的代码。由于这个原因，捕捉和检测故障点或调试代码变得更加容易和简单。

为什么？因为模块或导出文件中存在的变量在该模块的作用域内，或者是该模块的私有变量，而不在全局作用域内，所以这样的错误被正确地传播。此外，由于关注点的巨大分离，模块从父级加载到子级，沿着依赖图向下遍历。

> **注意**:当包装函数返回时，exports 对象被缓存，然后作为`require()`方法的返回值返回。这就像一个循环。怎么会？在下一次调用`module.exports`方法时，重复使用包装函数包装模块的相同过程。但是必须检查该模块是否已经存储在高速缓存中。

包装函数的签名如下所示:

```
(function(exports, require, module, __filename, __dirname) {
// Module code actually lives in here
});
```

接受 ID 和父模块作为参数的`Module`对象包含`export`对象:

```
function Module(id = '', parent) {
  this.id = id;
  this.path = path.dirname(id);
  this.exports = {};
  this.parent = parent;
  updateChildren(parent, this, false);
  this.filename = null;
  this.loaded = false;
  this.children = [];
};
```

`updateChildren`方法扫描文件路径，直到到达文件系统的根目录。它的工作是用新的`parent`更新`Module`对象的`children`属性，视情况而定。下面是签名:

```
function updateChildren(parent, child, scan) {
  const children = parent && parent.children;
  if (children && !(scan && children.includes(child)))
   children.push(child);
}
```

让我们看一个例子来更好地理解这一点。在上面的`b.js`文件中，添加这行代码来打印模块和参数对象:

```
console.log(module, arguments);
```

运行`node b.js`后，我们得到以下输出:

```
[email protected] es-modules in Node % node b.js
My name is Alex.
undefined
I'm 25 years old.
undefined
<ref *1> Module {
  id: '.',
  path: '/Users/retina/Desktop/es-modules in Node',
  exports: {},
  parent: null,
  filename: '/Users/retina/Desktop/es-modules in Node/b.js',
  loaded: false,
  children: [
    Module {
      id: '/Users/retina/Desktop/es-modules in Node/a.js',
      path: '/Users/retina/Desktop/es-modules in Node',
      exports: [Object],
      parent: [Circular *1],
      filename: '/Users/retina/Desktop/es-modules in Node/a.js',
      loaded: true,
      children: [],
      paths: [Array]
    }
  ],
  paths: [
    '/Users/retina/Desktop/es-modules in Node/node_modules',
    '/Users/retina/Desktop/node_modules',
    '/Users/retina/node_modules',
    '/Users/node_modules',
    '/node_modules'
  ]
} [Arguments] {
  '0': {},
  '1': [Function: require] {
    resolve: [Function: resolve] { paths: [Function: paths] },
    main: Module {
      id: '.',
      path: '/Users/retina/Desktop/es-modules in Node',
      exports: {},
      parent: null,
      filename: '/Users/retina/Desktop/es-modules in Node/b.js',
      loaded: false,
      children: [Array],
      paths: [Array]
    },
    extensions: [Object: null prototype] {
      '.js': [Function (anonymous)],
      '.json': [Function (anonymous)],
      '.node': [Function (anonymous)]
    },
    cache: [Object: null prototype] {
      '/Users/retina/Desktop/es-modules in Node/b.js': [Module],
      '/Users/retina/Desktop/es-modules in Node/a.js': [Module]
    }
  },
  '2': Module {
    id: '.',
    path: '/Users/retina/Desktop/es-modules in Node',
    exports: {},
    parent: null,
    filename: '/Users/retina/Desktop/es-modules in Node/b.js',
    loaded: false,
    children: [ [Module] ],
    paths: [
      '/Users/retina/Desktop/es-modules in Node/node_modules',
      '/Users/retina/Desktop/node_modules',
      '/Users/retina/node_modules',
      '/Users/node_modules',
      '/node_modules'
    ]
  },
  '3': '/Users/retina/Desktop/es-modules in Node/b.js',
  '4': '/Users/retina/Desktop/es-modules in Node'
}
```

如上图，我们可以看到第 6 行的[模块对象](https://github.com/nodejs/node/blob/master/lib/internal/modules/cjs/loader.js#L156)的所有属性，包括`filename`、`id`、`children`、路径深度等。此外，我们可以看到`argument`对象，它由`export`对象、`require`函数、文件和文件夹路径以及`Module`组成(这本质上是包装器函数所做的，但是它执行包含在文件/模块中的代码)。

最后，作为练习，我们可以继续在`b.js`文件中打印`require`函数。要了解更多关于`require`函数的输出，我们可以查看这一节节点源代码中的[实现](https://github.com/nodejs/node/blob/master/lib/internal/modules/cjs/loader.js#L1087)。

> **注意**:要特别强调`[load](https://github.com/nodejs/node/blob/master/lib/internal/modules/cjs/loader.js#L890:8)`和`validateString`方法。`validateString`方法，顾名思义，检查传入的模块 ID 是否是有效的字符串。要在更高的层次上理解 require 函数，您可以查看节点文档的[知识部分](https://nodejs.org/en/knowledge/getting-started/what-is-require/)。

## 两个模块系统的互操作性

在 CommonJS 中，[模块在运行时被评估之前被包装成函数](https://github.com/nodejs/ecmascript-modules/blob/modules-lkgr/doc/api/modules.md#the-module-wrapper)。对于 ES 模块，通过`import`和`export` [绑定](https://github.com/nodejs/node-eps/blob/master/002-es-modules.md#21-types)提供的代码重用在被评估之前已经被[异步创建或加载](https://github.com/nodejs/node-eps/blob/master/002-es-modules.md#31-async-loading)。要了解 ESM 在引擎盖下是如何工作的，你可以查看这里的。现在让我们进一步探索🙂

为了快速比较，CommonJS 模块在其生命周期中会经历以下阶段:

*解析—>加载—>包装—>评估—>缓存*

这证实了一个事实，即对于 CommonJS，在模块被包装和评估之前，没有办法确定什么作为模块导出。这对于 ES 模块来说非常不同，因为在代码被评估之前，导入的符号已经被语言解析和理解了。

当代码被解析时，就在它被评估之前，一个内部的[模块记录](https://tc39.es/ecma262/#sec-source-text-module-records)被创建，并且只有在这个数据结构是格式良好的之后，文件才被解析并且代码被评估。

例如:

```
//d.mjs
const check = () => {
  console.log('Just checking`);
};
export.check = check;

//e.mjs assuming they are on the same folder path
import {check} from './d'
```

在上面的`e.mjs`文件中，Node.js 在进一步执行或评估这段代码之前解析并验证导入。对于 CommonJS 模块来说，情况并非如此:只有在模块被包装和评估之后，导出的符号才是已知的。

这种不兼容性是负责 ECMAScript 的标准机构打算为 ESM 和节点的现有 CommonJS 模块系统实现互操作性的众多原因之一。

此外，[当前说明符解析](https://nodejs.org/api/esm.html#esm_customizing_esm_specifier_resolution_algorithm)并不支持 CommonJS 加载器的所有默认行为。其中一个主要区别是文件扩展名的自动解析和导入包含索引文件的目录的能力。

例如，如果我们从一个有`index.js`的目录中执行`import './directory'`，ES 模块不会像在 CommonJS 中那样在指定的文件夹中寻找一个`index.js`文件。相反，它会抛出一个错误。这可以通过传递实验标志`--experimental-specifier-resolution=[mode]`来修复。更多详情[此处](https://nodejs.org/api/esm.html#esm_customizing_esm_specifier_resolution_algorithm)。

> **注意**:为了定制默认的模块解析，可以选择通过 Node.js 的`--experimental-loader ./loader-name.mjs`参数提供加载器挂钩。加载器 API 目前正在重新设计，这意味着它们将来会改变。

此外，虽然 import 语句可以引用 ES 模块和 commonJS 模块，但是只允许在 ES 模块中使用 import 语句。但是，对于加载 ES 模块，commonJS 支持动态导入表达式。另外，可以使用`module.createRequire()`方法在 ESM 中构造一个 require 函数。

关于与 CommonJS 互操作性的更多细节可以在文档的这个[部分](https://nodejs.org/api/esm.html#esm_interoperability_with_commonjs)中找到。

## 两种模块系统的特点

*   CommonJS 和 ES 模块都支持动态`import()`。它可以用来包含 CommonJS 代码中的 ES 模块文件
*   ECMAScript 6 还提供了可以从 URL 加载的模块，而 CommonJS 仅限于相对和绝对文件路径。这一新的改进不仅使加载变得更加复杂，而且速度也很慢
*   Node.js 不理解的格式的源代码可以转换成 JavaScript。更多详情可在[这里](https://nodejs.org/api/esm.html#esm_transpiler_loader)找到
*   ESM 中对[无扩展主入口点](https://github.com/WICG/import-maps#extension-less-imports)的支持已经取消
*   [proposal-import-meta](https://github.com/tc39/proposal-import-meta) 提供当前 es 模块文件的绝对 URL。它目前是 TC39 规范中的第 4 阶段提案
*   动态导入可用于导入 ES 和 CommonJS 模块。在 CommonJS 模块中，它可以用来加载 es 模块。请注意，它返回一个承诺
*   使用`import`关键字时，必须提供文件扩展名。必须完全指定目录索引(如`'./database/index.js'`)
*   通过使用[有条件的出口，双 CommonJS 和 ESM 现在是可能的。](https://nodejs.org/api/esm.html#esm_conditional_exports)现在，Node.js 可以运行 ES 模块入口点，一个包可以同时包含 CommonJS 和 ESM 入口点
*   从 14.13.1 版本开始，ESM 增加了对使用`node: URLs`加载 Node.js 内置模块的支持，允许内置模块被有效的绝对 URL 字符串引用

> **注意**:函数`require`不应该在 ES 模块中使用。这是因为 ES 模块是异步执行的。要从 CommonJS 模块加载 ES 模块，我们可以利用`import()`。

读者还应该注意到，ESM 和 commonJS 模块之间仍然存在一些已知的差异。例如，ESM 导入目前不支持本机模块。此外，ES 模块加载器有自己的缓存系统，不依赖于 commonJS 术语中的`require.cache`。

其他包括在 commonJS 模块系统中发现的 *_filename* 或 *_dirname* 不可用。ESM 通过使用`import.meta.url`提供了复制这种行为的其他方式。

关于 es 模块和 commonJS 模块之间差异的更多细节，读者可以查看文档的这一[部分。](https://nodejs.org/api/esm.html#esm_differences_between_es_modules_and_commonjs)

## ES 模块向前发展

ES 模块不再被标记为实验性的，并且从节点版本 15.3.0 开始，在技术实现方面是稳定的。这意味着它们现在可以用于生产了。因此，挑战在于包的作者、维护者和开发者明确定义`package.json`文件中的类型字段和规范中讨论的其他有用的约定。关于这个的更多细节可以在这里找到[。](https://nodejs.org/api/esm.html#esm_writing_dual_packages_while_avoiding_or_minimizing_hazards)

如今，在一个应用程序中同时使用 CommonJS 和 ESM 是可能的，但是摩擦更少。但是当然，CommonJS 模块需要知道正在加载的模块是 CommonJS 模块还是 es 模块，因为后者只是异步加载的。

文件的[包装章节](https://nodejs.org/api/packages.html#packages_dual_commonjs_es_module_packages)详细介绍了与双包装危害相关的问题以及避免或减少这些危害的方法。

此外，根据 ESM 规范，默认情况下，与 CommonJS 模块一样，使用 import 关键字并不能完成带有文件扩展名的文件路径。所以这个也要事先明确说明。更多细节可在概述两个模块系统之间差异的章节[中找到。](https://nodejs.org/docs/latest-v15.x/api/esm.html#esm_differences_between_es_modules_and_commonjs)

## 结论

在引入 ES6 标准之前，在服务器端 JavaScript 中没有任何组织源代码的本机实现。社区非常依赖 CommonJS 模块格式。

如今，随着 ES 模块的引入和 API 的稳定，开发人员可以享受到与发布规范相关的许多好处。本文强调了这两种模块系统之间的转换及其互操作性。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

请注意，本文没有介绍加载器 API，因为它们仍然是实验性的，在未来的版本中肯定会有所变化。要了解更多信息，请查看文档的本节。

像 [Babel](https://babeljs.io/) 和 [esm](https://github.com/standard-things/esm#readme) 这样的工具，可以将新的语法翻译成与旧环境兼容的代码，这种转换变得更加容易。

从长远来看，整个起草过程是重要的一步，为今后的进一步改进铺平了道路。如果你有任何问题，请在下面的评论区告诉我，或者给我的 [Twitter 账号](https://twitter.com/alex_nnakwue)发消息。感谢阅读🙂

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.