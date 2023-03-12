# 理解 Magic-RegExp 作为 RegExp 的替代

> 原文：<https://blog.logrocket.com/understanding-magic-regexp-regexp-alternative/>

正则表达式(或 Regex)是一种模式，可用于在字符串中搜索与该模式匹配的内容。它通常用于查找和替换操作，以及表单输入验证和不同类型的字符串操作。

Magic-RegExp 的目标是创建一个可编译的、类型安全的、可读的 RegEx 替代方法，使这个过程变得更加容易。

当您刚刚开始使用正则表达式时，它可能看起来有点奇怪，可能需要一些时间来适应。

为了充分理解这个主题，我们探索一下为什么在我们的项目中使用正则表达式是很重要的。

很多时候，我们的任务是验证必须符合模式的输入。解决这个问题的一个方法(我认为不是一个好方法)是尝试写一些`if`语句；但是，随着我们的继续，我们会发现它可能很难阅读和维护——因此需要一个更好的解决方案，比如正则表达式(或 RegExp)。

在本文中，我们将了解正则表达式，以及如何使用 Magic-RegExp 来改进和简化项目正则表达式的创建。

让我们跳进来吧！

*向前跳转:*

## 在 RegExp 中创建

在 JavaScript 中，我们主要有两种创建正则表达式的方法。

第一种，也是首选的方法，是使用字面符号，在两个正斜杠之间写表达式:

```
const pattern = /[a-zA-Z]+ing$/;

```

我们可以使用 new 关键字及其构造函数创建 RegExp 对象的实例:

```
const pattern = new RegExp('[a-zA-Z]+ing');

```

如果模式将作为用户输入，我们主要使用第二种方法，因为它支持赋值变量，而第一种方法是不可能的。

## 使用 RegExp 的限制

### 验证 URL

让我们假设我们想要验证一个 URL——让我们看看一个有效的 URL 可以有不同的格式。

```
http://www.google.com
www.google.com
google.com
telegram.org
egghead.io
w3schools.com

世界上最大的网络开发者网站。HTML 教程这是一个标题，这是一个段落。亲自尝试了解更多通过我们免费的“我的学习”计划跟踪您的进度。登录您的帐户，开始赢取积分！注册免费的 W3Schools 著名的颜色选择器:玩游戏测试你的技能！

TypeScript 通过向语言中添加类型来扩展 JavaScript。TypeScript 通过在运行代码之前捕捉错误并提供修复来加快您的开发体验。

```

在本文的范围内，为了简洁起见，我们将把对有效 URL 的探索限制在上面。

### 开发伪代码

让我们开发一个伪代码来帮助我们编写验证相似模式的正则表达式。

1.  可以以`http`或者`https`开头(这个好像是`optional`)
2.  后面是冒号`:`和两个正斜杠`//`(这也是`optional`)
3.  紧随其后的是`www.`(这似乎也是`optional`)
4.  它后面跟着一个可以包含数字的字母
5.  它后面是一个`.`(句号)
6.  它可以以`io`、`org`或`com`结尾

为了从上面的伪代码开发我们的模式，我们必须熟悉一些正则表达式语法。

要跟踪并实时查看比赛，您可以使用工具 [regex101](https://regex101.com/r/WrPyo5/1) 。随意添加更多的网址，并使用这个工具。

### 伪代码 1 和 2

必须以`http`或`https`开头:

我们使用脱字符号来表示开始，后面跟着字母`http`，`^`，所以我们有`^http`，但是我们可以有一个可选的`s`。为了使一个字母可选，我们在它后面添加了特殊字符`?`，所以我们的表达式现在是`^https?`。

这之后是一个冒号`:`——我们简单地附加这个东西，所以我们有`^https?:`，然后是两个正斜杠`//`。理想情况下。我们应该像添加冒号`:`一样添加这个字符，但是我们不能这样做，因为它们是特殊字符，所以我们必须用一个`\`反斜杠对它们进行转义。

因此，我们的表达式现在变成了`^https?:\/\/`，但是从上面的 URL 示例中我们可以看到，URL 可以以“https://”或“http://”开头，这意味着整个表达式是可选的。从上面我们已经建立的例子中，我们通过在它后面附加一个`?`来使一个字母可选。如果我们这样做，它将只使最后一个字母可选；但是我们想要的是使整个组可选——因此我们用括号将它括起来，使它成为一个捕获组(实际上，使它作为一个单元运行),然后我们添加了`?`。

下面是我们得到的表达式:
`(^https?:\/\/)?`

### 伪代码 3

我们将“www”附加到前面的表达式中，我们有了`(^https?:\/\/)?www`，但是后面是一个`.`，这是一个特殊的字符，因此我们必须对它进行转义，所以我们的表达式现在将是`(^https?:\/\/)?www\.`。根据我们的分析，整个“www。”是可选的，所以我们把它放在组里然后使它可选:
`(^https?:\/\/)?(www\.)?`

### 伪代码 4

然后，一个有效的 URL 后跟一个可以包含数字的单词。任何单词字符都可以包含`a-z (a, b, c to z)`或`A-Z (A to Z)`或`0-9`甚至一个`_`——组合在一起就是这样:`[A-Za-z0-9_]`；或者，在一个特殊字符下:`\w`。

这两者是等效的，但后者因为简洁而更受青睐。我们将把它添加到我们的表达式中:
`^(https?:\/\/)?(www\.)?[A-Za-z0-9_]`
`^(https?:\/\/)?(www\.)?\w`

上述两种模式是等价的，但是为了简洁起见，我们将坚持后者。

如果我们使用像 [Regex101](https://regex101.com/) 这样的在线工具运行测试，我们会发现它只匹配一个单词字符，我们需要匹配的是任何单词字符的一个或多个出现。有一个特殊字符允许我们这样做，它是加号`+`，表示前面的字符出现一次或多次。

因此，我们的表达式现在将是:
`^(https?:\/\/)?(www\.)?\w+`

### 伪代码 5

然后它后面跟着一个`.`，这是一个我们必须转义的特殊字符，添加这个 in 将我们的表达式修改为:
`^(https?:\/\/)?(www\.)?\w+\.`

### 伪代码 6

这表示我们的 URL 可以以“com”、“io”或“org”结尾。为此，我们需要另一个特殊字符，`|`。这用来表示一组中的一个选择。

我们现在有:
`^(https?:\/\/)?(www\.)?\w+\.(com|org|io)$/`

此外，我们可以添加修改 RegExp 默认行为的标志。这些标志如下:

*   i: `ignoreCase`这意味着它应该不区分大小写(将匹配“HI”(“HI”、“Ih”或“iH”)的任何组合)
*   g: `global`表示应该尽可能多的匹配；默认情况下，它在第一次匹配后停止
*   m: `multiline`意味着它应该继续跨多行搜索匹配

将这些相加得到表达式:
`/^(https?:\/\/)?(www\.)?\w+\.(com|org|io)$/mig`

我们可以使用两种方法来测试我们的表达式是否如预期的那样工作:

```
const pattern = /^(https?:\/\/)?(www\.)?\w+\.(com|org|io)$/mig
pattern.test('www.google.com') // returns true
pattern.test('lovelife') // return false

搜索世界信息，包括网页、图像、视频等。谷歌有许多特殊功能来帮助你准确地找到你想要的东西。

www.google.com
google.com
telegram.org
egghead.io
w3schools.com

世界上最大的网络开发者网站。HTML 教程这是一个标题，这是一个段落。亲自尝试了解更多通过我们免费的“我的学习”计划跟踪您的进度。登录您的帐户，开始赢取积分！注册免费的 W3Schools 著名的颜色选择器:玩游戏测试你的技能！

https://www.typescriptlang.org`.match(/^(https?:\/\/)?(www\.)?\w+\.(com|org|io)$/gmi)

['http://www.google.com', 'www.google.com', 'google.com', 'telegram.org', 'egghead.io', 'w3schools.com', 'https://www.w3schools.com', 'https://www.typescriptlang.org']
The match method retunrs an array of all matches.

```

从上面可以明显看出，一开始使用 RegExp 可能会有点令人不安，因为需要理解相当多的术语以及如何应用它们。这就是 Magic-RegExp 的用武之地。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## Magic-RegExp 入门

Magic-RegExp 简化了创建正则表达式的过程，使其读起来像普通英语——它是一个编译好的、类型安全的、可读的 RegExp 替代物。

要开始，我们可以简单地使用我们最喜欢的包管理器安装它；`npm`、`pnpm`或`yarn`

```
npm install magic-regexp

```

我们也可以将它包含在流行的框架中，如 Nuxt (Vue.js)或 Next.js (React)。我建议访问官方[页面](https://regexp.dev/getting-started/setup)了解更多细节。

我们可以尝试使用上面的伪代码重新创建上面的正则表达式，但是首先我们将启动一个新的 Node.js 应用程序来测试这个。

```
mkdir regExp // make a project directory
cd regExp  // go into the directory
touch app.js // create an entry point for our application
npm init -y // set default options

npm i magic-regexp

```

现在我们已经设置好了项目，我们可以打开 app.js 并开始编写代码。

```
import {
  createRegExp,
  exactly,
  wordChar,
  oneOrMore,
  anyOf,
} from "magic-regexp";
const regExp = createRegExp(
  exactly("http")
    .and(exactly("s").optionally())
    .and("://")
    .optionally()
    .and(exactly("www.").optionally())
    .and(oneOrMore(wordChar))
    .and(exactly("."))
    .and(anyOf("com", "org", "io")),
  ["g", "m", "i"]
);
console.log(regExp); 

/(https?:\/\/)?(www\.)?\w+\.(com|org|io)/gmi

```

我们导入`createRegExp`函数，并使用附带的函数来组成我们的表达式，如上所示。

从上面，我们可以看到它读起来更接近于简单的英语，我们可以很容易地从伪代码中写出来。

即使生成的 RegExp 并不完全相同(不同之处在于开始的`^`和结束的`$`)；它们非常相似，并且毫无问题地通过了我们所有的测试条件。

这非常简单且容易实现，因为不了解 RegExp 的内部工作原理就可以实现。很整洁，是吧？

关于 magic-regexp 更全面的列表，请访问[文档](https://regexp.dev/getting-started/examples)

## 结论

很明显，通过使用 Magic-RegExp 库，我们可以在很少或没有 RegExp 知识的情况下完成很多工作。

它可以更快地产生表达式，更容易调试和阅读，虽然它有包大小的开销，但这是最小的，很容易被忽略。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。