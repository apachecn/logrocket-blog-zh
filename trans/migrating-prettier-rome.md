# 从漂亮的地方迁移到罗马

> 原文：<https://blog.logrocket.com/migrating-prettier-rome/>

在今天这个时代，建立一个前端项目可能是相当令人生畏的。您需要为您的堆栈考虑的配置列表似乎永无止境。你必须考虑一个 web 框架、代码捆绑工具、代码编译、依赖管理、静态分析(又名林挺)、测试框架、类型检查、代码格式化等等。

在撰写本文时，Rome 已经支持各种特性。它可以处理 TypeScript 和 JavaScript，并带有代码林挺和格式。但是他们的路线图远远不止这些，到 2023 年中期，JSON、HTML、代码编译、代码捆绑和代码测试都将出现。

Rome 强调强约定和最小配置的哲学，这样开发人员可以专注于重要的事情:开发。最重要的是，它将自己定位为像 [ESLint](https://eslint.org/) 和[pretty](https://prettier.io/)这样的工具的更高性能版本。最终，罗马想要取代所有这些工具和其他工具。

版本 10 的发布标志着罗马自重写 Rust 以来的第一个稳定版本。他们将自己的代码格式化程序称为 Prettier 的替代程序，在移植过程中只需要很少的改动，甚至不需要改动，并声称性能显著提高。本文将从 Prettier 开始深入这个迁移过程，展示必要的步骤和不同之处，并验证前面提到的说法。

*向前跳转:*

## 更改代码格式设置

要将代码格式的设置从 Prettier 更改为 Rome，需要执行几个步骤。首先，你需要安装罗马和卸载漂亮。对于这些步骤，我们必须执行以下操作:

```
yarn add rome --save-dev
yarn remove prettier

```

在项目中安装了 Rome 之后，您需要初始化配置文件`rome.json`。虽然这不是绝对必要的，但它可以将所有配置规则放在一个地方，并保持 CLI 命令的整洁。

如果您已经有了一个更漂亮的配置文件，这将会感觉很熟悉，并且会使迁移稍微容易一些。

```
yarn rome init

```

使用此`init`命令将创建一个默认设置的配置文件，如下所示。默认情况下，linter 和 formatter 都是(隐式)启用的。在本文中，我们将忽略配置的 linter 部分。您可以随意禁用或配置它；这不会影响本文中的迁移过程。

```
// Resulting `rome.json`
{
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true
    }
  }
}

```

从 Prettier 迁移到 Rome 时要记住的一件事是更新提交过程中的格式化步骤。如果您正在使用类似 husky 的东西在提交前格式化您的文件，那么您应该将脚本更新为 Rome 的等价物:

```
yarn rome format --write

```

这将自动考虑配置文件。

然后，在您的`rome.json`中，在现有的`linter`条目旁边创建两个额外的条目，称为`formatter`和`javascript`。在这些部分中，您可以分别为所有文件和 JavaScript 文件配置格式规则。我们将在本文后面介绍可用规则的完整列表。

您可以包含的一个可选规则是`formatter.enabled`。如果不包含它，默认情况下将启用格式化程序。为了这篇文章，我们将使它明确。

```
{
    // Linter stuff...
    "formatter": {
        "enabled": true,
        // Formatting rules
    },
    "javascript": {
        "formatter": {
            // JavaScript formatting rules
        }
    }
} 

```

### 排除文件

下一步是配置哪些文件应该由 Rome 控制，哪些不应该。如果你想在所有的文件上运行罗马，那么你不必添加任何东西。但是，如果您想排除任何文件，有两种方法可以做到这一点。

如果您只想从格式化程序中排除这些文件，但是想将它们包含在 Rome 的其他部分中，比如 linter，那么您需要在`formatter`条目中进行修改。如果你想将它们从罗马的所有特征中排除，那么你需要在你的`rome.json`中创建一个额外的`files`条目。

在这两种情况下，您都必须添加一个接受一组 Unix shell 样式模式的`ignore`字段。这相当于 Prettier 的`.prettierignore`文件，但不需要单独的文件，语法风格也略有不同。然而，它们做的是完全相同的事情。以下示例显示了如何以两种方式忽略两个文件夹的 JavaScript 文件:

```
{
    // Linter stuff...
    "formatter": {
        "enabled": true,
        "ignore": ["scripts/*.js", "config/*.js"]
        // Formatting rules
    },
    "files": {
        "ignore": ["scripts/*.js", "config/*.js"]
    }
}

```

与 Prettier 类似，Rome 也允许对需要被格式化忽略的代码进行一次性的异常处理。它遵循注释中类似的模式，即从`// prettier-ignore`到`// rome-ignore format: <explanation>`。虽然注释的解释部分是可选的，但在撰写本文时冒号是强制的，以便 Rome 遵守。

### 在罗马使用 VS 代码

可选地，罗马在市场上有一个官方 VS 代码扩展。这与 Rome 的特性无缝集成，建议这样做，以便您的开发过程与 Rome 完全同步。

要将 Rome 配置为 VS 代码的默认格式化程序(和 linter)，请参考[官方文档](https://docs.rome.tools/guides/getting-started/#vs-code)。不幸的是，VS Code 是目前唯一受支持的编辑器，但很可能将来会有其他编辑器加入进来。

## 迁移格式规则

如前所述，配置格式化规则可以在`rome.json`文件中完成。它们是我们之前创建的`formatter`或`javascript.formatter`条目中的字段。尽管 Rome 标榜自己是 Prettier 的替代产品，但需要注意的是，它只支持有限数量的格式选项。

罗马格式器背后的所有设计决策都归结为一个首要目标:结束对风格的争论，这种争论往往更耗时而不值得。为了做到这一点，Rome 实际上遵循了与 Prettier 相似的理念，将可配置选项保持在最少。

对于罗马团队来说，这防止了风格的争论变成罗马是否应该支持他们的讨论。对于使用 Rome 的开发团队来说，这应该可以防止关于琐碎的风格问题的讨论变得不重要和浪费时间。

在撰写本文时，通用罗马格式化程序的可配置规则列表如下:

*   `formatWithErrors`:确定文件有语法错误时是否可以格式化。据我们所知，没有比这更好的选择了
*   `indentStyle`:决定是否通过制表符或空格进行缩进。这相当于 Prettier 的`useTabs`，但是使用了 enum 而不是 boolean 标志
*   `indentSize`:指定缩进的大小。这和 Prettier 的`tabWidth`一模一样
*   `lineWidth`:指定在格式化程序换行到下一行之前，一行中可以有多少个字符。这相当于更漂亮的`printWidth`

对于 JavaScript 和 TypeScript 文件，列表如下:

*   `quoteStyle`:确定字符串文字使用哪种类型的引号。这相当于 Prettier 的`singleQuote`，但是使用了 enum 而不是 boolean 标志
*   `quoteProperties`:指定对象文字内部的属性名需要引用的样式。这和 Prettier 的`quoteProps`类似，但是只支持`asNeeded`和`preserve`选项
*   `trailingComma`:确定尾部逗号应用于多行逗号分隔的语法结构的样式。这和 Prettier 的`trailingComma`一模一样
*   `semicolons`:决定语句末尾是否打印分号。这相当于更漂亮的`semi`

## 结论

JavaScript 领域最新、最激动人心的构建工具之一是 Rome。它旨在成为所有必要构建步骤(如代码林挺、格式化、编译等)的一体化和更高性能的替代品。随着它最近的稳定发布，我们开始将一个项目的代码格式化程序从 Prettier 迁移到 Rome，以验证它的声明。

基于我们所看到的，从实现的角度来看，迁移过程相对简单。换入 Rome 并更新相关的脚本命令后，迁移中最困难的部分是将旧配置移植到 Rome。

由于其固执己见和极小的哲学，罗马只有有限的配置列表可用于格式化程序。这种情况的好处是，它限制了向罗马迁移时要考虑的事情的数量。事实上，所有可用的格式规则几乎都与 Prettier 的一一兼容，迁移将不会是一个麻烦的过程。

然而，这确实意味着有可能你不能将你的整个更漂亮的配置迁移到罗马。尤其是如果你的漂亮设置非常详细，迁移前后的格式风格会有所不同，因为某些规则不被支持。根据你的情况，这可能是一个非常重要的结果，需要记住。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。