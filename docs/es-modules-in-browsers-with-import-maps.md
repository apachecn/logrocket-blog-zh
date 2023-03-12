# 在浏览器中使用 ES 模块

> 原文：<https://blog.logrocket.com/es-modules-in-browsers-with-import-maps/>

## 介绍

ES 模块很久以来一直是 JavaScript 社区的话题。他们的主要目标是带来 JavaScript 模块系统的官方标准化。当某种东西成为 JavaScript 中的标准时，涉及到两个主要步骤。首先，规范必须由 EcmaScript，[批准并最终确定，这已经完成](http://www.ecma-international.org/ecma-262/6.0/index.html)。其次，浏览器应该开始实现它。这一步有点耗时，并且伴随着向后兼容性的所有麻烦。

好消息是浏览器对 ES 模块的支持有了很大的进步。下图显示了包括 Edge、Chrome、Safari 和 Firefox (+60)在内的所有主流浏览器都支持 ES 模块:

![can i use shoing JavaScript ES modules support across browsers](img/ef13ac5da9a32ebc7b7c32f4d78fd58c.png)

说到模块，已经有几次尝试将这种功能引入 JavaScript 世界。例如:

*   Node.js 实现了自己的模块系统
*   捆绑器和构建工具，如 Webpack、Babel 和 Browserify 验证集成模块的使用

所以通过这些努力，很少有模块定义被实现。两种较少使用的方法是:

*   [AMD](https://requirejs.org/docs/whyamd.html) 或异步模块定义
*   [UMD](https://github.com/umdjs/umd) 或通用模块定义

然而，最主要的是:

*   [CommonJS](https://nodejs.org/docs/latest/api/modules.html) 是模块的 Node.js 实现
*   ES 模块，它是本地 JavaScript 定义模块的标准

有几件事我们不会在本文中涉及:

*   我们不会关注 CommonJS，除非它对 ES 模块有直接的功能。如果你有兴趣进一步了解这个模块系统，请阅读[这篇文章](https://blog.risingstack.com/node-js-at-scale-module-system-commonjs-require/)
*   尽管 Node 上支持 es 模块，但本文主要关注的是浏览器中 ES 模块的使用。如果你有兴趣了解更多关于节点中 ES 模块支持的信息，我推荐这篇[官方文档](https://nodejs.org/api/esm.html#esm_ecmascript_modules)，以及[这篇](https://blog.logrocket.com/es-modules-in-node-today/)和[这篇](https://blog.logrocket.com/es-modules-in-node-js-12-from-experimental-to-release/)文章

## 为什么我们甚至需要 ES 模块？

要回答这个问题，我们需要追溯 JavaScript 的基础知识。在 JavaScript 中，像许多其他编程语言一样，我们的重点很大一部分是构建、管理和使用变量和函数。您可以将这些视为构建块，它们将一起用于形成向用户交付最终结果的逻辑序列。然而，随着变量、函数和包含它们的文件数量的增加，维护它们的重要性也在增加。例如，您不能让变量的更改意外地影响代码的其他不相关部分，即使它们共享相同的名称。

在文件层面，我们已经解决了这个问题。您可以使用变量和函数，但也不能在函数范围之外访问和操作变量。如果你需要一个不同函数共享的公共变量，你可以把它放在文件的顶部，这样所有的函数都可以访问它。下面的代码演示了这一点:

```
// file.js

var foo = "I'm global";
var bar = "So am I";

function () {
    var foo = "I'm local, the previous 'foo' didn't notice a thing";
    var baz = "I'm local, too";

    function () {
        var foo = "I'm even more local, all three 'foos' have different values";
        baz = "I just changed 'baz' one scope higher, but it's still not global";
        bar = "I just changed the global 'bar' variable";
        xyz = "I just created a new global variable";
    }
}
```

但是在不同的文件之间有这样的机制呢？

嗯，作为第一次尝试，你可能想做一些类似的事情。假设您的代码库中的几个文件需要访问某种类型的库。这个库，像 [jQuery](https://jquery.com/) 一样，可以是帮助您开发工作流的助手函数的选择。在这种情况下，您需要将库实例放在所有可能需要它的文件都可以访问的地方。处理这个问题的最初步骤之一是将库放在一个全局脚本中。现在您可能会想，既然这些全局脚本是在所有其他文件都可以访问的入口文件中实例化的，那么共享对某些功能或库的访问权的问题就会变得更容易，对吗？不完全是。

这种方法存在某些问题。不同文件和共享库之间的依赖将变得很重要。如果文件和库的数量增加，这将变得令人头疼，因为您必须始终注意脚本文件的顺序，这是处理依赖关系管理的一种隐含方式。以下面的代码为例:

```
<script src="index1.js"></script>
<script src="index2.js"></script>
<script src="main.js"></script>
```

在上面显示的代码中，如果您在引用了来自`index2.js`的某些内容的`index1.js`文件中添加了一些功能，这些功能将不会工作，因为代码执行流在那个时间点还没有到达`index.2`。除了这种依赖性管理之外，在使用脚本标签作为共享功能的方式时，还有其他类型的问题，例如:

*   由于每个请求阻塞线程，处理时间变慢
*   每个脚本启动一个新的 HTTP 请求时的性能问题

你可以想象重构和维护依赖于这种设计的代码是有问题的。每次你想做改变的时候，你都要担心不要破坏其他任何以前的功能。这就是模块来拯救的地方。

[ES 模块，或者一般来说，模块](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)被定义为一组变量和函数，它们被组合在一起并被绑定到一个模块范围。这意味着可以在同一个模块中引用变量，但是也可以显式地导出和导入其他模块。有了这样的架构，如果某个模块被删除，导致部分代码中断，您将能够理解是什么导致了这个问题。

如前所述，已经有过几次将模块设计引入 JavaScript 的尝试。但到目前为止，本机模块设计的最接近的概念是我们将在本文中研究的 ES 模块。

我们将看到一些如何使用 es 模块的基本示例，然后探索在生产现场使用它们的可能性。我们还将看看一些可以帮助我们实现这一目标的工具。

## 浏览器中的 ES 模块

在浏览器中定义一个模块非常容易，因为我们可以访问 HTML 标签。向脚本标签传递一个`type='module'`属性就足够了。当浏览器到达任何带有这个属性的脚本标签时，它知道这个脚本需要被解析为一个模块。它应该是这样的:

```
// External Script
<script type="module" src="./index.js"></script>

// Inline Script
<script type="module">
  import { main } from './index.js';
  // ...
</script>
```

在这种情况下，浏览器将获取任何顶级脚本，并把它放在一个名为`module map`的地方，带有唯一的引用。这样，如果它遇到指向相同引用的另一个脚本，它就继续执行下一个脚本，因此每个模块只被解析一次。现在让我们想象一下`index.js`的内容看起来是这样的:

```
// index.js
import { something } from './something.js'

export const main = () => {
  console.log('do something');
}
//..
```

当我们查看这个文件时，我们看到了`import`和`export`语句，它们是使用和公开依赖关系的方式。因此，当浏览器完成获取和解析这些依赖项的异步旅程时，它只是从入口文件开始这个过程，在这种情况下，就是上面的 HTML 文件，然后继续将主脚本中所有嵌套模块的引用放到`module map`中，直到到达嵌套最多的模块。

请记住，获取和解析模块只是在浏览器中加载模块的第一步。如果你有兴趣阅读更多关于接下来步骤的细节，请仔细阅读这篇文章。

但是对我们来说，我们试图阐明浏览器中 ES 模块使用的一个方面，即使用`[import-maps](https://github.com/WICG/import-maps)`来简化指定模块说明符的过程。

## 为什么以及如何使用`import-maps`？

在加载模块的构建阶段，需要采取两个初始步骤。

第一个是[模块解析](https://www.codementor.io/@elliotaplant/understanding-javascript-module-resolution-systems-with-dinosaurs-il2oqro6e)，它是关于确定从哪里下载模块。第二步是下载模块。这就是浏览器上下文和 Node.js 这样的上下文中的模块之间最大的区别之一。因为 Node.js 可以访问文件系统，所以它处理模块解析的方式与浏览器不同。这就是为什么您可以在 Node.js 上下文中看到这样的内容:

```
const _lodash = require('lodash');
```

此外，在使用 Webpack 等构建工具的浏览器环境中，您可以这样做:

```
import * as _lodash from 'lodash';
```

在本例中，`'lodash'`模块说明符对于 Node.js 进程是已知的，因为它可以访问`filesystem`或通过 npm 包管理器分发的包。但是浏览器只能接受模块指定符的 URL，因为获取模块的唯一机制是通过网络下载它们。这种情况一直持续到一个叫做`import-maps`的关于 ES 模块的新提议出台，以解决这个问题，并在浏览器和其他工具以及捆绑器中的模块使用之间带来更一致的外观和感觉。

所以`import-maps`定义了一个模块导入名称的映射，这允许开发者提供[的裸导入说明符](https://html.spec.whatwg.org/multipage/webappapis.html#resolve-a-module-specifier)，像`import "jquery"`。如果您在当今的浏览器中使用这样的 import 语句，它将抛出，因为它们不被视为相对 URL，而是被显式保留。让我们看看它是如何工作的。

通过在脚本标签上提供属性`type="importmap"`,您可以定义这个映射，然后定义一系列简单的导入名称和一个相对或绝对 URL。请记住，如果您正在指定一个相对 URL，如下例所示，那么该文件的位置应该是相对于定义了`import-maps`的文件，在本例中是`index.html`:

```
// index.html

<script type="importmap">
{
  "imports": {
    "lodash": "/node_modules/lodash-es/lodash.js"
  }
}
</script>
```

定义这个映射后，您可以在代码中的任何地方直接导入`lodash`:

```
import jQuery from 'jquery';
```

但是如果你没有使用`import-maps`，你必须做一些类似下面显示的代码，这是很麻烦的，并且与今天用其他工具定义模块的方式不一致:

```
import jQuery from "/node_modules/jQuery/index.js";
```

所以很明显，使用`import-maps`有助于带来与今天模块使用方式的一致性。如果您习惯于在 NodeJS 或 Webpack 的上下文中要求或导入模块，那么一些基本的基础工作已经为您做好了。让我们探索其中的一些场景，看看它们是如何通过浏览器中的`import-maps`来处理的。

您可能已经看到，在 Node.js 中使用模块说明符时，有时不带扩展名。例如:

```
// requiring something.js file
const something = require('something');
```

这是因为，在幕后，Node.js 或其他类似的工具能够为您定义的模块说明符尝试不同的扩展，直到它们找到一个好的匹配。但是当在浏览器中使用 ES 模块时，这样的功能也可以通过`import-maps`实现。这就是你应该如何定义`import-maps`来达到这个目的:

```
{
  "imports": {
    "lodash/map": "/node_modules/lodash/map.js"
  }
}
```

正如您所看到的，我们定义了没有`.js`扩展名的模块说明符的名称。这样，我们能够以两种方式导入模块:

```
// Either this
import map from "lodash/map"

// Or
import map from "lodash/map.js"
```

有人可能会说无扩展名文件导入有点不明确，这是有道理的。我个人更喜欢精确地定义文件扩展名，即使是在 Node.js 或 Webpack 上下文中定义模块说明符。此外，如果您想使用`import-maps`的无扩展策略，您将会不知所措，因为您必须为包中的每个模块定义额外的无扩展模块说明符，而不仅仅是顶层文件。这很容易失去控制，并降低代码的一致性。

在通过 npm 分发的库和包中，包含几个可以导入到代码中的模块是很常见的。比如像`[lodash](https://lodash.com/)`这样的包就包含了几个模块。有时您想导入顶层模块，有时您可能对包中的特定模块感兴趣。下面是您如何使用`import-maps`来指定这样一个功能:

```
{
  "imports": {
    "lodash": "/node_modules/lodash/lodash.js",
    "lodash/": "/node_modules/lodash/"
  }
}
```

通过指定一个单独的模块说明符名称为`lodash/`并在地址`/node_modules/lodash/`中镜像相同的内容，您可以轻松地导入包中的特定模块，如下所示:

```
// You can directly import lodash
import _lodash from "lodash";

// or import a specific moodule
import _shuffle from "lodash/shuffle.js";
```

## 结论

在本文中，我们一起学习了 ES 模块。我们讨论了为什么模块是必不可少的，以及社区如何使用标准的方式来处理它们。

当谈到今天在浏览器中使用 es 模块时，一系列问题浮现在脑海中，例如旧浏览器的兼容性、回退处理以及 ES 模块在 bundler 和 build 工具之后的真正位置。我强烈认为 ES 模块会继续存在，但是它们的存在并没有消除对捆绑器和构建器的需求，因为它们还有其他重要的用途，比如死代码消除、缩小和树抖动。正如我们已经知道的，Node.js 等流行工具也在较新版本中采用 ES 模块。

ES 模块目前拥有广泛的浏览器支持。围绕 es 模块的一些特性，如 [`dynamic import`](https://github.com/tc39/proposal-dynamic-import) (允许基于函数的导入)以及`[import.meta](https://github.com/tc39/proposal-import-meta)`(支持 Node.js 案例)现在是 JavaScript 规范的一部分。正如我们所探索的，`import-maps`是另一个伟大的特性，它可以让我们消除 Node.js 和浏览器之间的差异。

我可以充满信心地说，ES 模块及其在 JavaScript 社区中的地位前景光明。

## 资源

[https://hacks . Mozilla . org/2018/03/es-modules-a-cartoon-deep-dive/](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)

[https://github.com/WICG/import-maps](https://github.com/WICG/import-maps)

[https://www.sitepoint.com/understanding-es6-modules/](https://www.sitepoint.com/understanding-es6-modules/)

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.