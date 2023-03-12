# Node.js 对林挺的深度探究

> 原文：<https://blog.logrocket.com/deep-dive-linting-in-node/>

## 介绍

在 JavaScript/Node.js 生态系统中，有许多工具用于林挺我们的代码。一些最流行的包括 JSLint、JSHint 和 ESLint。这些工具有助于确保我们的源代码中的问题或错误(主要是程序员引起的)可以通过静态代码分析检测出来——解析和分析源代码并返回新的结果。

这些工具的存在使得开发人员坚持最佳实践，并在代码风格方面坚持统一的标准。反过来，这不仅有助于防止错误(从而减少了调试中的许多来回),而且在风格方面加强了代码的一致性。

虽然 linters 以前已经存在，但在过去，它们主要是作为一个实用程序库，用来分析和修复 C 编程语言和其他基于 Unix 的软件中的问题。

如今，林挺描述了在代码库上应用规则的过程，这些规则有助于捕捉违反配置文件中指定的规则的错误和代码模式。在本帖中，我们将探讨在 Node 中工作时的一些林挺规则。

我们还将了解这些规则如何帮助改进编码标准，并在开发人员中实施最佳实践和一致性。最终目标是代码是一致的、干净的，并且容易被机器或解释器理解。

## 棉绒有什么帮助？

因为随着时间的推移，代码库可能会变得非常大，并且由于 JavaScript 编程语言的性质，linters 有助于提供一种机制来检查代码库中的程序员错误或 bug，以便帮助修复它们。

通常，会有一个配置文件，其中包含一系列的规则，这些规则概括了最佳实践，从而确保代码质量和良好的语法格式。虽然不同的开发人员可以决定在他们的项目中使用不同的林挺工具，但最终目标通常是相同的。

> 对于 JavaScript 语言，JSLint 的著名作者道格拉斯·克洛克福特在《JavaScript 如何工作》一书中已经介绍并解释了其中的一些规则。

一些概述的规则定义了发生了什么，以及程序如何对程序员引入的某些问题做出反应。例如，当我们的代码中有未使用的变量或不可访问的代码，甚至是错误的类型时，应该怎么办？

作为代码格式化的有用工具，Linters 也很方便，尽管现在我们有像[prettle](https://blog.logrocket.com/automate-formatting-and-fixing-javascript-code-with-prettier-and-eslint/)这样的风格指南，这是一种处理风格和格式规则的流行工具，使程序员能够遵循特定语言的最佳实践。

林挺规则在像 JavaScript 这样的解释型语言中很重要，因为它们确保在运行之前快速发现错误。

这不同于像 Go 和 TypeScript(一个 JavaScript 超集)这样的编译语言，在代码编译阶段会发现并抛出错误。

实质上，林挺规则将贯穿我们的源代码，并发现与以下内容相关的问题:

*   格式不规则
*   程序员引起的错误或缺陷
*   糟糕的编码实践
*   风格错误

举个很好的例子，假设我们正在用 JavaScript 或 Node 开发一个 API，我们有一个未定义的变量作为参数传递给一个函数。如果不实际使用 linter 或使用编译语言，甚至不编写任何单元测试，就很难检测或修复这类问题。

通过在运行前分析我们的代码，我们可以确信在一天结束时，我们已经建立了一个舒适的代码可靠性和质量水平。

总的来说，当在大型跨职能团队中工作时，Node 中的林挺规则特别有用，在这种团队中，具有不同意识形态和编写代码模式的开发人员在同一个代码库上协作。本质上，这些规则确保代码质量始终处于检查之中。

## 使用带有样式参考线的升降器

随着 JavaScript 社区中 linters 使用的增加，样式指南被发明出来帮助修正 linters 的样式规则。

样式指南通过用不一致的样式重印代码库的特定部分来工作。因此，我们不一定要用 linters 指定风格规则，因为我们可以简单地配置一个风格指南来处理这部分，用 linters 来处理与代码质量特别相关的规则。

> 使用 ESlint，我们可以同时实现样式和格式规则。这意味着我们用 linters 配置或指定的规则可以应用于代码质量和格式方面。然而，它通常与 style guides 一起使用，比如 Prettier，这是专门用于文体目的的。

风格指南的一个好处是，它们强制执行某种编写代码的方式(假定的最佳实践)，所有在同一代码库工作的团队成员都必须适应或遵守这种方式。如今，最流行的风格指南之一是 [Javascript 标准风格](https://standardjs.com/)，这是一个带有自动代码修正器和 linters 的 Javascript 风格指南。

事实上，Javascript 标准样式是建立在 ESLint 之上的。因此，不需要配置文件，因为工具中已经内置了预配置的样式规则、格式和代码质量规则。

如果你注意到了，我特别提到了 ESLint。这是因为它是当今 JavaScript 生态系统中使用最多、最流行的林挺工具之一。事实上，它正逐渐成为节点应用的标准，在 npm 上每周约有 1900 万次下载。

虽然前面提到的其他林挺库可以在 JavaScript 运行的任何环境中工作，但我们将大量引用 ESlint 库来探索一些林挺规则。然而，这些规则包罗万象，普遍适用。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 探索林挺规则

在这一节中，我们将探讨这些规则如何帮助开发人员编写更好的代码。具体来说，我们将参考 ESLint 库并探索它的一些规则。但是在我们开始之前，让我们看看它的一些特性。

根据文档，ESLint 是 JavaScript 的语法检查器和格式化程序。它很容易扩展，并且为每个规则提供了一堆自定义插件。我们也可以通过编写自己的插件来配置自己的规则。

该库改进并修复了与早期林挺工具相关的一些缺点，这些工具不具有可扩展性和可定制性，并且阻止开发人员创建自己的规则。

ESLint 有几个原则，允许每个规则独立存在，并且可以随时打开或关闭。最大的好处之一是该库不支持任何特定的编码风格，这意味着开发人员可以根据他们项目的需求和用例自由地添加或删除规则。

此外，ESLint 有两个主要的规则类别，由不同的子类别组成。

首先是包含代码质量相关规则的 API。为了更容易理解，它们被进一步细分为不同的类别:

*   与语法/逻辑错误相关的规则
*   与最佳做法有关的规则
*   与语言中的严格模式相关的规则
*   与变量声明相关的规则

其次，我们有与风格指南相关的风格规则。这些规则通常是主观的，基于个人偏好。不要忘了，我们还可以决定将 ESLint 与一个像 Prettier 这样的风格指南结合起来处理这些风格规则。

### 代码质量规则

[no-unused-vars](https://eslint.org/docs/rules/no-unused-vars) 旨在消除未使用的变量、函数和函数参数。默认情况下，这个规则是用变量的`all`选项和参数的`after-used`选项启用的。可以在文档中的[这里](https://eslint.org/docs/rules/no-unused-vars#args)找到参数的链接。

```
{
    "rules": {
        "no-unused-vars": ["error", { "vars": "all", "args": "after-used", "ignoreRestSiblings": false }]
    }
}

```

[基数](https://eslint.org/docs/rules/radix)–使用`parseInt()`函数时，通常会省略第二个参数(基数)，让函数尝试从第一个参数确定它是什么类型的数字。

在下面的示例中，ESLint 会将下面 parseInt 的第一次使用标记为不安全:

```
/ without a radix argument - Unsafe
const count = parseInt(countString);

// with a radix parameter specified - Safe
const count = parseInt(countString, 10);

```

[定义前不使用](https://eslint.org/docs/rules/no-use-before-define)会在遇到对尚未声明的标识符的引用时警告开发者。在 ES6 之前，变量和函数声明被提升到作用域的顶部，所以可以在声明变量之前使用它们。

请参见下面的规则配置:

```
{
   "no-use-before-define": ["error", { "functions": true, "classes": true }]
}

```

[无无用捕获](https://eslint.org/docs/rules/no-useless-catch)报告`catch`子句，该子句仅`throw`一般性捕获错误。返回原始错误的`catch`子句是多余的，对程序的运行时行为没有影响。

请参阅以下规则的正确用法:

```
try {
  runCode();
} catch (e) {
  handleAxiosError(e);
}

```

[否-自我比较](https://eslint.org/docs/rules/no-self-compare)-将变量与其自身进行比较通常是错误的。这是因为，无论是谁阅读代码，都会感到困惑，并可能引入运行时错误。

[无无用返回](https://eslint.org/docs/rules/no-useless-return)旨在报告多余的`return`语句。后面没有任何内容的`return;`语句是多余的，对函数的运行时行为没有影响。

[非常量赋值](https://eslint.org/docs/rules/no-const-assign)不允许重新赋值`const`变量。我们不能修改使用`const`关键字声明的变量，因为这将引发运行时错误。

[no-used-constructor](https://eslint.org/docs/rules/no-useless-constructor)不允许创建不必要的构造函数。因此，没有必要提供空的构造函数或简单地委托给其父类的构造函数。

[prefere-destructing](https://eslint.org/docs/rules/prefer-destructuring)–在 ES6 中，增加了一个新的语法，用于从数组索引或对象属性创建变量，称为 destructing。

[no-param-reassign](https://eslint.org/docs/rules/no-param-reassign)–通常情况下，函数参数的赋值是无意的，表示错误或程序员错误。该规则不允许重新分配`function`参数。

### 文体规则

[max-len](https://eslint.org/docs/rules/max-le) 用于强制最大线路长度。为了提高可读性和可维护性，编码人员开发了一个约定，将代码行数限制在 X 个字符以内(传统上是 80)。

[不混合空格和制表符](https://eslint.org/docs/rules/no-mixed-spaces-and-tabs)不允许混合空格和制表符进行缩进。大多数代码约定要求缩进使用制表符或空格。因此，如果一行代码同时缩进了制表符和空格，这通常是一个错误。

关键词间距–作为语言的重要组成部分，风格指南通常指的是关键词周围应该使用的间距。此规则强制关键字前后的间距保持一致。

[非三元](https://eslint.org/docs/rules/no-ternary)不允许三元运算符。三元运算符用于有条件地为变量赋值。有人认为使用三元运算符会导致代码不清晰。

骆驼案执行骆驼案命名惯例。该规则查找位于源代码中的任何下划线(`_`)。它忽略前导和尾随下划线，只检查变量名中间的下划线。

另外，值得注意的是，这些规则报告的一些问题可以通过`--fix`命令行选项自动修复。此外，规则报告的一些问题可以通过编辑建议手动修复。

> 顺便提一下， [npm-package-json-lint](https://github.com/tclindner/npm-package-json-lint) 是一个可配置的 linter，用于执行 npm package.json 文件中的标准。[JavaScript 编程语言的代码约定](http://javascript.crockford.com/code.html)是一个详细描述 JavaScript 代码风格的社区标准的文档。

## 结论

Linters 有助于发现并最终修复我们代码中的问题。他们扫描源代码，报告语法错误和 bug，并在我们的代码风格中执行约定，以帮助提高整体代码质量。

我们可能已经知道，不同的人有不同的代码编写模式。带有风格指南的 Linters 包含了一些与我们采用的风格类型相关的规则和最佳实践，从而在代码库中加强了一定程度的一致性。

简而言之，这篇文章探讨了一些适用于节点生态系统的林挺规则，以及它们如何帮助在团队中巩固和灌输共同的标准。它还允许团队成员编写干净、可读和清晰的代码。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.