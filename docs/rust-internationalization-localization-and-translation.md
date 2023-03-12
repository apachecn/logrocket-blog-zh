# Rust 国际化、本地化和翻译

> 原文：<https://blog.logrocket.com/rust-internationalization-localization-and-translation/>

在过去的几年里，随着越来越多的企业寻求在世界舞台上推广他们的产品和服务，Rust 越来越受欢迎。根据 Stack Overflow 的“ [2020 年开发者调查](https://insights.stackoverflow.com/survey/2020#technology-most-loved-dreaded-and-wanted-languages-loved)”，使用过 Rust 的受访者中有高达 86%的人计划再次使用它，这使得 Rust 成为 2020 年最受欢迎的编程语言。

国际化(i18n)是计划向全球受众推出其应用和软件的公司的一个关键考虑因素。在这个 Rust 国际化教程中，我们将检查八个最流行的 Rust i18n 机箱，并评估每个机箱的生产就绪性和稳定性。

在本指南中，我们将看看八个最流行的 Rust 国际化板条箱，并评估每个板条箱的生产就绪性和稳定性。

我们将讨论以下内容:

## 什么是国际化？

国际化是开发适应任何地区、语言或文化的用户的内容和应用程序的过程。换句话说，国际化是通过提供适合每个地区的内容，使应用程序可以全球访问的过程。

国际化有时被缩写为 i18n，因为在开头的“I”和结尾的“n”之间有 18 个字母

## Rust 国际化:关键术语

在继续我们的 Rust 国际化教程之前，让我们定义一些关键术语:

*   **国际化(i18n)** 是指设计能够适应各种语言和地区的计算机程序或网络应用程序
*   **本地化**是通过为给定地区或语言添加特定于地区的组件来调整国际化 web 应用程序的过程。它使用国际化提供的灵活性和功能
*   翻译是将字符串从一种语言替换成另一种语言的过程

## 为什么要用 Rust 的国际化？

在静态网站时代，本地化是通过在多个地区复制整个网站结构来完成的。由于每一页都是单独翻译的，因此翻译者可以直接对每个结构进行更改。国际化工具简化了这个繁琐、耗时、易出错的过程。

Rust 最丰富的功能之一是语言环境，这使得将应用程序翻译成各种语言变得很容易。

## 通用本地化系统

一般来说，有三种常见的本地化系统:

1.  [gettext](#gettext)
2.  [项目流畅](#projectfluent)
3.  [ICU 报文格式](#icumessageformat)

让我们更详细地检查每一个。

### gettext

[g](https://en.wikipedia.org/wiki/Gettext) [ettext](https://en.wikipedia.org/wiki/Gettext) 是一个本地化系统，将翻译文件从编程语言中分离出来，并提供对任意数量的复数形式字符串的支持。Sun Microsystems 在 1993 年实现了 gettext 的第一个版本。1995 年，GNU 项目发布了一个自由软件实现。从那以后，gettext 被许多编程语言使用，包括 Rust。

下面是如何使用 gettext:

![Gettext Localization System Use Instruction Chart](img/2d3e0f921bd1af71cc5a77da3828b6bb.png)

1.  使用`gettext(), _()`接受源代码中的翻译字符串
2.  生成。pot(可移植对象模板)文件，使用`xgettext`或其他软件帮助跟踪翻译后的字符串和使用了`gettext()/_()`的行
3.  生成。使用 PoEdit、Emacs 等软件为每种语言/地区创建 po(可移植对象)文件。为每个源文本添加适当的翻译
4.  编译为。翻译完成后的 mo(机器对象)文件，便于分发

![Machine Object File Compilation](img/09003c07a44bd4cd8eb84538a16c0018.png)

### 流畅项目

[Fluent](https://www.projectfluent.org/) 是一个翻译系统，通过提供对诸如性别、复数、变化等语法表达的支持，专注于产生听起来自然的翻译。

Fluent 使用名为 FTL(Fluent 翻译列表的缩写)的文件格式中的表达式来处理翻译。FTL 文件代表一种包含函数、属性、变量、选择器、术语等的语言。每个语言环境都必须在不同的语言环境目录中创建自己的 FTL 文件，并按照相同的语法模式进行翻译。

```
# en-US
# Simple variable
text-layout = Fast track your productivity by testing out your code on the spot
# Attributes
form-layout = 
  .button = Save and Execute
  .font-seletion = Font Size

#es
# Simple variable
text-layout = Acelere su productividad probando su código en el lugar
# Attributes
form-layout = 
  .button = Guardar y ejecutar
  .font-seletion = Tamaño de fuente

```

### ICU 消息格式

[Unicode 国际组件](https://unicode-org.github.io/icu/) [(ICU](https://unicode-org.github.io/icu/) [)](https://unicode-org.github.io/icu/) 是一种标准编码方案，提供全功能 Unicode 服务，如日期操作、访问本地化信息的资源包、字符集转换等。它提供了关于源消息中数据期望的常规翻译。

ICU 依赖于各种数据格式，包括 XML、JSON、YAML 和用于消息格式化的属性文件，并使用包含各种标准的消息语法，包括变量、选择、复数、日期、时间等。

```
{    
   "text": "Just write text without any variables"
}

# Representing numbers { variableName, number, style }. 
{ 
  "amount" : "Your total is {total, number, ::currency/EUR}"
}

{  
   "created_at": "The todo was posted on {publishedAt, date, medium} at {publishedAt, time, short}"
}

# Spanish version
{  
  "created_at": "La tarea fue publicada en {publishedAt, date, medium} a {publishedAt, time, short}"
}

```

让我们放大八个最流行的 Rust 国际化 API，比较它们的生产就绪性、稳定性和整体开发人员体验。

1.  [流畅](#fluent)
2.  [流利的英语](#fluentlangneg)
3.  [T2`gettext`](#gettext)
4.  [T2`gettext-rs`](#gettext-rs)
5.  [`r_i18n`](#ril8n)
6.  [JSON 获取文本](#jsongettext)
7.  [`i18n_codegen`](#il8ncodegen)
8.  [火箭 I18N](#rocketi18n)

### 流利的

Fluent 旨在改进平台上的翻译过程。它支持语法操作、解析器和核心本地化结构使用低级结构在单一语言环境中格式化和存储消息。

Fluent 还提供了一个 API，该 API 支持用户友好的助手、框架绑定、错误回退、用户请求的语言之间的语言协商、资源以及用于处理所选资源的输入/输出。

*   生产就绪:是
*   稳定:是

### 流利的语言

[Fluent LangNeg](https://github.com/projectfluent/fluent-langneg-rs) 基于`[fluent-langneg](https://github.com/projectfluent/fluent.js/tree/master/fluent-langneg)`，一个与[项目 Fluent](https://www.projectfluent.org/) 关联的 JavaScript 库实现，以前叫做`fluent-locale`。它使用 [`unic-langid`](https://github.com/zbraniecki/unic-locale) 和 [`unic-locale`](https://github.com/zbraniecki/unic-locale) 来检索和操作地区标识符。Fluent LangNeg 还提供了在不同区域列表之间进行协商的选项。

*   生产就绪:是
*   稳定:是

### `gettext`

[Rust gettext](https://docs.rs/gettext/0.4.0/gettext/) 基于 [GNU gettext](https://www.gnu.org/software/gettext/) ，支持并为大量语言建模，如 [php-gettext](https://github.com/php-gettext/Gettext) 、 [gettext-go](https://godoc.org/github.com/robfig/gettext-go/gettext) 、 [js-gettext](https://developer.mozilla.org/en-US/docs/Mozilla/Localization/gettext) 等等。它将翻译后的数据存储在一个. mo 文件中，该文件与语言文件分开，因为它不强制使用目录结构进行存储。

在撰写本文时，该机箱仍处于开发阶段。

*   生产就绪:否
*   稳定:是

### `gettext-rs`

[`gettext-rs`](https://github.com/Koka/gettext-rs) 使用 Rust 的 GNU [Gettext](https://www.gnu.org/software/gettext/) FFI 绑定，基于环境变量构建脚本执行。它目前只支持 Linux 和 Windows。

*   生产就绪:否
*   稳定:是

### `r_i18n`

[`r_i18n`](https://crates.io/crates/r_i18n) 是一个通过从配置目录中为每种语言创建的 JSON 文件中访问字符串的相关键值对来翻译字符串的工具。

*   生产就绪:是
*   稳定:是

### JSON 获取文本

[`json-gettext`](https://www.gnu.org/software/gettext/) 从 JSON 语言环境文件中提取字符串，得到用户的目录。本地化的工作原理是在 Rust 必须访问的目录中创建不同的 JSON 语言环境。

json-gettext 提供了对[R](http://rocket.rs)ocket web 框架和 unic languid 的支持。

*   生产就绪:是
*   稳定:是

### `i18n_codegen`

[i18n *codegen* JSON 文件中的键变成了一个可以通过实现来访问的方法。这个过程旨在帮助您避免常见的错误，如丢失插值、i18n 键输入错误等。](https://crates.io/crates/i18n_codegen)

*   生产就绪:是
*   稳定:是

### 火箭 I18N

[`rocket_i18n8`](https://github.com/Plume-org/rocket_i18n) 设计用于通过 i18n 宏在 [rocket](https://rocket.rs/) 或 [actix web](https://actix.rs/) 框架上基于用户请求翻译区域设置。Rocket I18N 被构建为仅支持编译模板，例如[r](https://github.com/kaj/ructe)cute 和[A](https://github.com/djc/askama)skama 模板引擎。

*   生产就绪:是
*   稳定:是

## 结论

通过这些国际化 API，您可以将业务扩展到其他地区，这有助于提高 Rust 应用程序的整体用户参与度。

满足您需求的最佳国际化工具将取决于您的特定用例以及您希望应用程序实现的目标。上面提到的一些 API 比其他 API 更适合生产，也更稳定，所以请确保在提交解决方案之前做好准备。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。