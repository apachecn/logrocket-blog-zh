# 帮助你入门的 4 个选项林挺你的 JavaScript 

> 原文：<https://blog.logrocket.com/four-options-to-help-you-get-started-linting-your-javascript-b4b829828648/>

你几乎总是可以打赌，一篇好文章是好编辑的恩人。在这方面，代码和散文没什么不同。作为开发人员和程序员，我们享受的一个好处是编辑器，或代码翻译器，它们可以内置到我们的工作流中。

林挺是检查您的代码是否有任何错误的行为或过程。关于如何优化给定代码的有效性，有许多想法。但是检查以确保它没有错误并且符合特定的风格指南是基线。有时这是一致性和可读性的问题，有时是让代码实际运行的问题。

谈到 JavaScript 林挺，有几个工具是与众不同的。让我们来看四个 linters，它们可以帮助你开始或改进你的林挺过程:JSLint、standardJS、JSHint 和 ESLint。

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/for/javascript-error-tracking/)

## JSLint

![](img/c272917ae200a7815f6cb7cfd3d16b36.png)

JSLint 由道格拉斯·克洛克福特于 2002 年创立，他也写了一本可以说是 T2 最好的关于 JavaScript 的书。JSLint 为表格带来了简单性和速度。但它也非常固执己见，这可能是一件好事，也可能是一件坏事。

JSLint 由一个页面站点组成，该站点由一个文本字段控制，您可以在其中粘贴代码。单击“JSLint”按钮，任何错误、文体错误、语法错误或其他错误都会显示在文本字段下。文本字段下是一个小选项列表，可通过复选框进行配置。选项包括容忍额外的空白，使用“this”关键字(Crockford 在他的演讲中不鼓励这样做)，以及包含 Node.js。

如果您不依赖任何特定的风格指南，并且想要一个可靠的来源来检查您的代码，JSLint 是一个很好的选择。对于测试代码片段，或者如果您正在寻找一种快速 lint 小项目的方法——可能是一个只包含一个 JavaScript 文件的单页静态站点，它特别有效。

## 标准 j

![](img/b35d68a05d9b125d6c90254f507a32d0.png)

仅基于 GitHub 的星星， [standardJS](https://standardjs.com/) 是最受欢迎的选项，有近 19，000 颗星星。它是完全自以为是的，意味着它根本不可定制。但是，如果你不依赖任何特定的风格指南，这可能是一件好事。它以节点 CLI 的形式出现，可以使用您选择的终端或命令行进行全局安装或作为开发依赖项进行安装:

```
$ npm install standard --global // or $ npm install standard --save-dev
```

因为 standardJS 将 Node 和 npm 作为先决条件，并且因为它是从命令行或通过 npm 脚本运行的，所以标准比 JSLint 的标准稍微高了一些。但是因为它是不可配置的，所以你没有什么可担心的。你可以从命令行运行它，作为一个单词的命令，它会检查你当前工作目录中每个扩展名为`.js`的文件。

它发现的任何错误都将被打印到您的终端或命令行。您可以从 standardJS [文档](https://standardjs.com/index.html#install)中看到类似于这个例子的输出:

```
$ standard Error: Use JavaScript Standard Style lib/torrent.js:950:11: Expected '===' and instead saw '=='.
```

如果需要指定文件或目录，可以将路径作为参数，并使用通配符。它也接受通配符。在本例中，standardJS 将在`src`目录及其子目录中查找并 lint 任何 JavaScript 文件:

`$ standard "src/**/*.js" --fix`

文件路径后面的`--fix`标志是在发现错误时自动修复错误的选项。这可以节省大量的时间，但也是一个很好的学习练习，让你自己去修正错误。

如果你想在决定是否使用它之前探索一下 standardJS 使用的惯例和规则，完整的列表可以在这里找到。对于那些寻找快速可靠的方法来开始使用 JavaScript linter 的人来说，StandardJS 是一个很好的选择。

## JSHint

![](img/668491266f64d21c5885a726001affd3.png)

JSHint 开始是 JSLint 的一个分支。目标是制造一个更加可配置的棉绒。如果你一直在使用 standardJS，或者另一个固执己见的 linter，并且你正在寻找一种方式开始定制你自己的林挺规则，JSHint 可能适合你。它具有上述棉绒的大部分优点，还有其他一些优点。

像 JSLint 一样， [JSHint 主页](http://jshint.com/)有一个文本字段，你可以在那里粘贴代码。文本字段右边的`Metrics`字段将在您输入时实时更新，记录您代码的运行统计列表，例如它包含多少个函数。当然，它还会显示找到的任何林挺错误。

如果您不喜欢复制/粘贴方法，并且希望将它应用到您的项目中，可以使用 npm 全局安装 JSHint，或者将其作为项目依赖项安装:

```
$ npm install jshint --global // or $ npm install jshint --save-dev
```

安装后，您将使用 CLI 来 lint 您的代码。以下是分别检查单个文件和目录的两个命令示例:

```
$ jshint index.js // or $ jshint src/
```

在第一个例子中，JSHint 将 lint`index.js`文件，在第二个例子中，它将递归地搜索‘src/’目录并 lint 它找到的任何 JavaScript 文件。JSHint 将打印它在终端中发现的任何错误。

如果您不关心定制，JSHint 可以像上面的例子中描述的那样使用，它会工作得很好。但是，从这里开始，复杂性会显著增加，因为 JSHint 是完全可配置的，并且它还公开了一个 API，这意味着它可以在您自己的 JavaScript 文件中用作 JavaScript 模块。

一个定制的配置应该存储在一个名为`.jshintrc`的文件中，文件可能是这样的:

```
{ "esversion": 5, "eqeqeq": true, "strict": true }
```

此示例从上到下将 ECMAScript 版本设置为 5，在比较值时需要使用三个等号(`===`或`!==`)而不是两个(`==`或`!=`)，并强制执行严格模式。您可以通过在命令行的一个`-- config`标志后面指定您的`.jshintrc`文件的路径，或者在您的项目`package.json`文件中将它们声明为‘jshintConfig’属性，来包含您的定制配置。JSHint 将对您没有自定义的任何规则使用其默认选项。

命令行选项可能如下所示:

```
// looks for '.jshintrc' in the current directory $ jshint --config './.jshintrc'
```

虽然`package.json`选项可能看起来像这样:

```
{
 "jshintConfig": { "esversion": 5, "eqeqeq": true, "strict": true } }
```

您可以使用这些基础知识开始使用 JSHint 定制您自己的林挺规则。如果你想了解更多，官方文档包含了关于如何使用 JSHint API 的详尽描述，以及定制它来满足你的需求的所有方法。

## 埃斯林特

![](img/4e4471c1f1ef7350274dcd47fc330c8a.png)

抛开 GitHub 明星不谈，谈到 JavaScript，林挺·埃斯林可能是最常见的，也将是很多人的首选。在它自己的[文档](https://eslint.org/docs/user-guide/getting-started)中，它将自己与 JSLint 和 JSHint 进行比较，看它用于解析 JavaScript 的方法。而且，与 JSHint 类似，您可以使用缺省值，并随着您的偏好或需求的变化添加定制。

要开始使用 ESLint，请全局安装或作为开发依赖项安装:

```
$ npm install eslint --save-dev // or $ npm install eslint --global
```

如果您全局安装 ESLint，它的配置将应用于您运行它所针对的任何和所有项目文件。但是如果您希望不同的项目有不同的配置，您可以将它作为开发依赖项安装，并为每个项目创建不同的配置文件。请注意，如果 ESLint 是作为项目依赖项安装的，而不是全局安装的，那么您需要从您的`node_modules`文件夹中运行可执行文件，如下所示:

`$ ./node_modules/.bin/eslint --init`

当您运行上面的命令时，将通过一系列的问题引导您配置 ESLint。(注意:无论您计划如何定制您的林挺规则，您都必须从这一步开始，因为 ESLint 需要这个过程生成的`.eslintrc`文件，然后它才能 Lint 您的代码。)

问你的第一个问题是如何配置 ESLint。您有三种选择:使用流行的风格指南，回答关于您的风格的问题，或者让 ESLint 通过检查您的文件来决定如何设置规则，从而为您配置自身。如果马上自己配置它的前景看起来令人生畏，您可以依靠使用由少数几个已知组织之一开发的流行风格指南。

不管你走哪条路，ESLint 都会用你的答案在当前工作目录下生成一个名为`.eslintrc`的文件。如果以后想修改林挺规则，就要修改这个文件。

这里有一个 JSON 格式的示例`.eslintrc`文件，它使用默认的 [Airbnb JavaScript 样式指南](https://github.com/airbnb/javascript)规则，并包括两个自定义规则来关闭严格模式并允许`console.log()`语句:

```
{
 "extends": "airbnb-base", "rules": { "strict": "off", "no-console": "off" } }
```

如果你选择回答关于你的风格的问题，它会问你使用哪个 ECMAScript 版本，你是否喜欢制表符或空格，分号或没有，你是否使用 JSX 和/或反应。ESLint 对 React 和补充插件的现成支持可能会使它成为 React 开发者的最佳选择。至少对于那些刚刚开始接触林挺的人来说是这样。

安装 ESLint 并生成一个`.eslintrc`文件后，您可以使用 CLI 开始林挺您的代码。缺省情况下，ESLint 会查找您的`.eslintrc`文件，因此您不需要在命令行中指定任何配置。但是您可以使用各种标志来改变 ESLint 的行为。在下面的例子中，` -- quiet`标志告诉 ESLint 只显示错误，而不是同时显示警告和错误。`--fix`标志告诉它尝试自动修复它发现的任何错误。

```
// run eslint against file1.js $ ./node_modules/.bin/eslint file1.js // run eslint against file1.js and file2.js with flags to modify behavior $ ./node_modules/.bin/eslint file1.js file2.js --quiet --fix
```

与我们讨论的其他 CLI 一样，如果需要，您可以使用通配符和文件路径来代替特定的文件名。虽然 ESLint 是高度可配置的，但它通过使用一个可接近的设置指南来简化默认配置方法的学习曲线。如果你真的想深入了解定制，[官方文档](https://eslint.org/docs/user-guide/getting-started)包含了你可以用 ESLint 做的所有事情的详细解释。

## 后续步骤和结论

总而言之:

*   JSLint 非常适合检查代码片段或单个文件。它的一个潜在缺点是不适合大型项目。
*   StandardJS 非常适合那些想要轻松入门和/或在工作流中构建 linter 和构建脚本的人。但是，它是不可配置的。因此，如果你需要制定自定义规则，你可能会想看看 JSHint 或 ESLint。
*   JSHint 也可以通过 npm 安装，它的林挺规则是完全可配置的。这可能是好的也可能是坏的，取决于你的需求和技能水平。您可以从默认规则开始，并根据需要进行定制。它还提供了一个单页面站点，您可以使用它来 lint 片段或单个文件。
*   ESLint 可以通过 npm 安装，并像 JSHint 一样内置到工作流中。其 CLI 的问答格式可以帮助您入门。在其开箱即用的形式，它包括行业标准，开源风格指南和林挺规则，可适用于任何项目。

我们研究的所有四种 linters 都是可靠的、有信誉的，因为它们是由 web 开发社区中的知名人士和组织使用和开发的。他们中的任何一个都会给任何人带来好处。如果您已经掌握了本文中讨论的基础知识，下一步将是学习如何使用 npm 脚本或 Webpack 之类的捆绑器将它们进一步集成到您的工作流中。

任何工具的好坏取决于你如何使用它。这对 linters 和它们帮助你完善的代码来说都是正确的。即使您是单独开发，并且不需要担心整个开发团队的代码一致性，您仍然可以从内置编辑器中受益。这是学习正确编写 JavaScript 的非常有效的方法。不管你使用哪种棉绒，使用棉绒只能帮助你。你可以打赌你的代码质量会提高，你作为开发人员的技能也会提高。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.