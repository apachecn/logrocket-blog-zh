# ESLint - LogRocket 博客的现代、快速替代方案

> 原文：<https://blog.logrocket.com/modern-faster-alternatives-eslint/>

*   [Libraries to integrate with ESLint](#libraries-integrate-eslint)

    ## linters 和代码格式化程序的优势

    linter 的核心目的是帮助你改进你的代码。这种改进是通过分析您的代码并根据预先配置的自定义或默认规则集通知您出现的问题来实现的。这些规则集在 ESLint 中被称为`rules`。本质上，通过这些规则集，linters 帮助开发团队实现一致的编码风格，并找到由于不一致的编码风格而可能发生的错误。

    另一方面，代码格式化程序通过解析您的代码并使用它自己的规则重新打印它来实施一致的风格。它会考虑代码编辑器的最大行长度设置，并在必要时换行。一个著名的代码格式化程序的例子是[更漂亮的](https://blog.logrocket.com/using-prettier-eslint-automate-formatting-fixing-javascript/)；我们将在接下来的章节中深入探讨这个问题。

    ## 什么是 ESLint？

    ESLint 属于我们在上一节中讨论过的 linter 类别。它是最受欢迎的 JavaScript 林挺工具之一，目前每周下载量约为 2400 万次。

    它主要擅长做两件事:发现代码中的问题，并自动修复它们。ESLint 要么内置于大多数文本编辑器中，要么可以作为扩展集成，这表明了它的广泛流行。它是高度可定制的，您可以根据项目的需要扩展默认规则来编写定制规则。

    ESLint 的解析器是语法感知的(它可以理解你的代码语法之间的关系),所以你遇到由传统的查找和替换算法产生的不一致错误的几率非常低。

    接下来，让我们看看两类库:一类是可以替代 ESLint 的库，另一类是可以与 ESLint 集成的库。

    ## ESLint 的现代替代品

    ### 罗马

    [Rome](https://rome.tools/) 是一个工具链，旨在统一功能(如格式化、林挺、捆绑等。)由多个库提供。它旨在取代 JavaScript 生态系统中的 ESLint 和其他工具库，如 Babel、webpack、appelliter 等。

    Rome 的目标是建立在一个联合基础上，为处理代码、显示错误和配置提供可靠的体验。罗马有严格的规定，只允许最低限度的配置。Rome 还具有一流的 IDE 支持，一个复杂的解析器以完全保真的方式表示源文本，以及顶级的错误恢复。

    #### 罗马优于埃斯林的地方

    ##### 表演

    最初，罗马是用 JavaScript 构建的，但现在用 Rust 重写；其意图是，这种结构上的根本性转变将在性能、内存使用和类型安全方面带来巨大的改进，而不会损害灵活性等其他方面。

    ##### 第三方依赖关系

    通过 Rust，开发人员可以创建专注于基本需求(如正确性)的内部 API，而不是依赖第三方 JavaScript 依赖*。*这是 Rust 及其社区重视的结果，它允许 Rome 使用来自社区的依赖项，而无需在代码大小和性能之间做出权衡。

    ##### 特征

    Rome 不仅仅是一个 linter，它是一个库下几个工具的组合，因此，Rome 提供了比 ESLint 更多的功能和特性。

    Rome 仍在开发中，尽管解析和代码格式化特性已经发布，可以通过 [npm](https://www.npmjs.com/package/rome) 获得。总之，它的目标是崇高的，随着时间的推移，它可能会成为 JavaScript 生态系统工具的重要颠覆者。

    ### 快速棉绒

    Quick-lint-js 是一个 JavaScript 林挺工具；它帮助我们找到 JavaScript 程序中的错误，并给我们代码的即时反馈，在必要的地方突出语法错误。它非常简洁，允许我们在没有定制配置或额外插件的情况下使用该工具。Quick-lint-js 旨在成为 ESLint 的替代方案，它在某些方面优于 ESLint，所以我们来看一些。

    #### quick-lint-js 相对于 ESLint 的优势

    ##### 表演

    根据这些[基准](https://quick-lint-js.com/benchmarks/)，在某些限制条件下，如输入延迟，quick-lint-js 比 ESLint 快 130 倍以上。基准测试也有助于它成为最快的 JavaScript linter 和最少的能源消耗者。

    ##### 小巧的设计，较少的功能

    Quick-lint-js 的简约设计有助于其性能，因为更少的基础设施和更少的组件降低了运行时解析成本，这意味着 quick-lint-js 可以更快地给出语法错误的反馈。

    ##### 没有配置

    它开箱即用，不需要任何定制的配置文件来让它工作。

    Quick-lint-js 是一个值得考虑的选择，特别是如果您希望获得高性能、易用性和较小的体积膨胀。然而，在社区支持、支持的规则数量和定制配置/可定制性方面，ESLint 仍然是王者。

    ### 标准 JS

    [**标准 JS**](https://standardjs.com/) 是 JavaScript 的 linter、code styler、formatter。它通过一个`.eslintrc.json`文件强制执行零配置方法，而不是 ESLint 中使用的定制配置。举例来说，用户必须将社区惯例置于个人风格之上。

    #### 标准 JS 的优势

    ##### 简单

    标准 JS 通过完全消除定制配置的可能性，提供了一种在项目中加强代码质量的直接方法。没有`.eslintrc.json`种配置文件需要管理；标准 JS 为我们管理一切。

    ##### 自动格式化代码

    标准 JS 提供了一个简单的命令，通过运行`standard --fix`来修复不一致的代码。

    如果你不介意使用社区创建的约定和风格指南，标准 JS 是值得考虑的。但是，如果你需要基于团队/个人喜好定制配置的灵活性，ESLint 更好。

    ### 出版

    是一个基于 Babel 的代码转换器，内置了对 ESLint 的支持。它是高度可配置的，利用了一个插件系统，这意味着所有的规则都可以作为插件使用。这保持了初始 API 的最小化，并允许开发人员通过只安装他们需要的规则来扩展它。

    输出由三个核心模块组成:解析器(解析抽象语法树中的代码，然后将其解析回代码)、加载器(处理插件)和运行器(执行插件)。它主要用于 JavaScript，但也可用于其他语言，如 TypeScript、YAML 和 Markdown。让我们来看看与 ESLint 的比较，以及我们可能希望在 ESLint 的什么地方使用它。

    #### 输出优于 ESLint 的优势

    ##### 应用修复

    ESLint 通常避免应用复杂的[修复，这些修复可能会改变运行时行为](https://eslint.org/docs/developer-guide/working-with-rules#applying-fixes)。另一方面，Putout 可以进行这些更剧烈的代码转换，直接影响您的代码库。

    ##### 极简 API

    由于其插件优先的架构，初始 API 尽可能保持精简，防止不必要的膨胀，并允许开发人员选择扩展。一些插件规则也比 ESLint 中可用的规则小，例如[调试器规则](https://github.com/coderaiser/putout#%EF%B8%8F-in-doubt-about-using-putout)。

    我们还可以将 output 与 ESLint 一起使用，允许 output 处理代码转换，而 ESLint 处理代码转换后的任何格式错误。我们可以使用插件 [eslint-plugin-putout](https://github.com/coderaiser/putout/tree/master/packages/eslint-plugin-putout) 来帮助这个集成。

    ### JSHint

    JSHint 是作为 JSLint 的一个更具可配置性的版本创建的；它由 John Crawford 于 2011 年发布，类似于 ESLint，它可以帮助我们检测 JavaScript 程序中的语法错误。

    因为它旨在成为 JSLint 的一个可配置版本，并提供了大量可以通过配置文件禁用或启用的选项，这提供了某种形式的灵活性。每个可用选项都有充分的文档记录。

    JSHint 支持许多框架，如 JQuery、Mocha 和 Node.js，也是许多流行代码编辑器的插件。

    *   JSHint 基本支持 ES6 语法，但不支持 ESNext 语法；它也不支持 JSX。这可能是一个交易破坏者，尤其是当考虑将其用于更现代的项目时
    *   JSHint 没有自定义规则支持
    *   JSHint 有简单、清晰、简明的文档，尤其是关于项目中可用的所有选项
    *   与 JSHint 不同，ESLint 的错误消息非常详细并且易于理解，因为所有消息都使用了自然语言
    *   如果违反了规则并需要修复，JSHint 不会显示导致错误的规则的名称，这使得跟踪变得更加困难

    总之，对于我们将遇到的大多数场景，ESLint 是两者中更成熟、更健壮的解决方案。

    ## 要与 ESLint 集成的库

    ### 较美丽

    [更漂亮](https://prettier.io/)是一个代码格式化程序。它在 JavaScript 生态系统和特定的其他语言如 Markdown 和 JSON 中提供支持。beauty 从零开始重新打印我们的代码，考虑到行的长度，并在必要的地方包装代码。它去掉了所有默认样式，并确保格式化后的代码符合一致的样式。

    Prettier 在整个代码库中强制执行一致的代码样式，因为它解析掉任何原始样式，并使用自己的规则重新打印解析的 [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) (抽象语法树)。与 Rome 不同，appellite 不是 ESLint 的直接替代方案，但是我们可以在使用 ESLint 的代码质量规则来捕获 bug 的同时，为代码格式化配置 appellite。

    下面我们就来看看如何将 beautiful 与 ESLint 融合。

    #### 将 beauty 与 ESLint 整合

    我必须承认，网上有很多关于配置 Prettier 与 ESLint 一起工作的指南。这通常因 JavaScript 框架和项目环境而异，但是我们将研究一种在普通 JavaScript 应用程序中集成这两者的简单方法。

    首先要做的是为你的代码编辑器安装 ESLint 和 Prettier。对于 VS 代码编辑器，我们可以在 VS 代码扩展市场上找到 ESLint 和更漂亮的扩展。

    接下来，我们必须安装三个软件包；在终端中键入以下命令:

    ```
    npm install --save-dev eslint eslint-config-prettier eslint-plugin-prettier

    ```

    [eslint-config-appellister](https://github.com/prettier/eslint-config-prettier)包禁用了所有可能与 appellister 冲突的 ESLint 规则。这让我们可以使用 ESLint 配置，而不会妨碍我们使用更漂亮的配置。然后我们可以使用[eslint-plugin-appearlier](https://github.com/prettier/eslint-plugin-prettier)包将 appearlier 规则集成到 ESLint 规则中。

    最后，我们必须在 ESLint 配置文件中设置更漂亮的规则。将以下配置添加到应用程序根目录下的`.eslintrc`文件中:

    ```
    { 
     "extends": ["prettier"],
     "plugins": ["prettier"],
     "rules": { 
     "prettier/prettier": ["error"]
     },
    }

    ```

    这样，我们应该能够在我们的项目中无缝地使用更漂亮的 ESLint，并且不应该有任何关于两个库之间规则冲突的问题。

    ### 巴比伦式的城市

    [Babel](https://babeljs.io/) 是一个 JavaScript 编译器，主要用于将代码从较新的 ECMAScript 版本(ES6+)转换到较早的 ECMAScript 版本，因此它为当前和较旧的浏览器或环境提供了向后兼容的版本。

    我们可以使用 [Babel](https://blog.logrocket.com/babel-vs-typescript/) 来转换语法，为目标环境中不可用的特性提供聚合填充，以及源代码转换。Babel 通过语法转换器支持最新的 ECMAScript 特性；它们允许我们使用最新的语法，而无需等待浏览器的支持。

    像 Prettier 一样，Babel 不是 ESLint 的替代品，但可以利用 ESLint 的林挺特性为我们的应用程序提供一组更强大的特性。

    #### 用 ESLint 集成 Babel

    ESLint 的默认解析器和规则只支持最新的最终 ECMAScript 标准，[不支持实验特性](https://github.com/eslint/eslint/blob/a675c89573836adaf108a932696b061946abf1e6/README.md#what-about-experimental-features)。因此，我们必须使用一个 npm 库——[@ babel/eslint-parser](https://www.npmjs.com/package/@babel/eslint-parser)——在有效的 Babel 代码上运行 ESLint。

    让我们看看在现有的 Babel 项目中建立这个库的简单方法。转到终端，运行以下命令来安装库:

    ```
    npm install eslint @babel/eslint-parser --save-dev

    ```

    同样，当安装完成时，转到`.eslintrc.js`文件(这通常是我们配置 ESLint 的地方),并将其设置为首选解析器:

    ```
    module.exports = {
     parser: "@babel/eslint-parser",
    };

    ```

    瞧，我们现在应该能够使用 ESLint 为林挺我们的巴别塔代码。在[文档](https://www.npmjs.com/package/@babel/eslint-parser)中也有关于高级配置的指南。

    ### 网络包

    Webpack 是一个面向现代 JavaScript 应用的静态模块捆绑器——这意味着它将我们的 JavaScript 代码及其依赖项构建到一个文件中。Webpack 识别应用程序中的所有依赖项(包括图像和其他非代码文件),并在内部构建依赖图。然后，它将这些内容打包成几个包，可以加载到浏览器上。

    Webpack 默认情况下不需要任何配置就可以工作，但是它支持为更复杂的项目定制配置。让我们看看如何配置 webpack 来与 ESLint 一起工作。

    #### 将 webpack 与 ESLint 集成

    Webpack 有一个强大的插件系统，允许社区提供其他不可用的功能。为了使这变得简单，我们将利用一个[流行的社区插件](https://www.npmjs.com/package/eslint-webpack-plugin)来集成 webpack 和 ESLint。

    在现有的 webpack 项目中，在终端上运行以下命令:

    ```
    npm install eslint-webpack-plugin --save-dev

    ```

    安装完成后，将此选项添加到现有的 webpack 配置文件中:

    ```
    const ESLintPlugin = require('eslint-webpack-plugin');
    module.exports = {
     // ...
     plugins: [new ESLintPlugin(options)], // plugin options
     // ...
    };

    ```

    插件配置也需要特定的选项；我们可以在这里找到所有可用的选项。将 webpack 与 ESLint 集成就是这么简单！

    ## 结论

    仅此而已！我们学习了 linters、代码格式化程序，还查看了一些提供这些功能的流行库。我们还深入研究了 ESLint 的一些替代方案，并最终研究了 ESLint 如何与其中一些库集成，从而为我们提供一组更强大的功能。