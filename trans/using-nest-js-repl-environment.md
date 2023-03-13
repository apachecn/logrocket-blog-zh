# 使用 NestJS REPL 环境

> 原文：<https://blog.logrocket.com/using-nest-js-repl-environment/>

读取-评估-打印循环，更好地称为 REPL，是一个交互式计算机环境，它读取用户输入，评估并向用户显示结果。REPL 环境是一个方便的工具，可以快速探索特定系统环境和编程语言中可用的特性和功能。

在本文中，您将学习如何在您的项目中使用 [NestJS](https://blog.logrocket.com/tag/nestjs/) REPL 环境。我们还将强调如何利用内置节点 REPL 环境来充分利用 NestJS REPL 环境。

*向前跳转:*

## REPL 和内斯特杰的简要概述

REPL 环境的一个简单例子是大多数现代 web 浏览器的开发工具中的控制台。

以类似的方式，节点运行时环境也有一个内置的 REPL 环境，您可以使用它来交互式地执行简单的 JavaScript 代码。

与上面提到的环境一样，NestJS 也有一个 REPL 环境，您可以用它来检查应用程序的依赖图，并从终端调用提供者和控制器上的方法。

值得注意的是，NestJS REPL 环境是最近随 NestJS 版本 9 一起发布的——因此，它是对 NestJS 的一个相对较新的补充。

## 如何建立一个 NestJS REPL 环境

首先，在以 REPL 模式运行 NestJS 应用程序之前，必须设置和配置 NestJS REPL 环境。如果您有一个 NestJS 项目，按照下面的步骤配置 NestJS REPL 环境。为此，我们假设您正在运行一个使用 Nest CLI 创建的项目，并且样板文件保持不变。

### 创建`repl.ts` **文件**

在包含`main.ts`文件的同一个目录中创建一个`repl.ts`文件。之后，您可以将以下代码复制并粘贴到其中:

```
js
import { repl } from '@nestjs/core';
import { AppModule } from './app.module';

const bootstrap = async () => {
  await repl(AppModule);
};

bootstrap();

```

在上面的例子中，我们声明了一个异步函数来引导 NestJS REPL 环境。

### 启动 NestJS REPL 环境

在终端上运行下面的命令，启动 NestJS REPL 环境。

```
sh
npm run start -- --entryFile repl

```

![Terminal to launch Nest.js REPL environment.](img/4a061695a651c60a5a8bd9bd218b0b21.png)

不用每次想为项目启动 REPL 环境时都运行上面的命令，给你的`package.json`文件添加一个新的脚本是很方便的。每当您想要打开 REPL 环境时，在终端上执行命令`npm run start:repl`。

```
json
{
 ...
 "scripts":{
    "start:repl": "npm run start -- --entryFile repl"
 }
 ...
}

```

传递给上面命令的`repl`选项返回节点 REPL 服务器对象的一个实例。因此，节点 REPL 环境中的所有功能在 NestJS REPL 环境中都是可用的。

## NestJS REPL 环境本地函数

NestJS REPL 环境附带了几个本机函数。本节将探讨 NestJS REPL 环境的一些主要特性。

### 如何使用内置的 NestJS REPL 帮助工具

NestJS REPL 环境有一个内置的实用程序，您可以使用它来获得关于 REPL 环境中的本机函数的帮助。如果您处于交互式 REPL 模式，请使用`help`功能，如下所示:

```
sh
help()

```

![Interactive REPL Help Function](img/298560f158a4a9dea787ad70cff29f1b.png)

在交互式 REPL 会话中调用上面代码中的`help`函数将打印可用的 REPL 函数和每个 REPL 函数的单行摘要。这些内置函数包括:`get`、`debug`、`resolve`、`select`和`methods`。

类似地，您也可以在上述任何函数上使用`.help`来获得特定 REPL 函数的帮助。例如，`get.help`命令将打印出对`get`函数的一行描述、它所采用的参数以及它的返回类型。

![Help On Specific Function ](img/b3111e55312cf7d7c39da1c9a316b2db.png)

### 如何打印注册的模块

模块是编写 NestJS 应用程序的推荐方式。它们有助于组织应用程序中的组件——每个 NestJS 应用程序必须至少有一个根模块，但是典型的真实世界中的 NestJS 应用程序通常有几个模块。

如果您想打印所有注册模块及其控制器和提供者的列表，那么`debug`函数非常方便。像这样调用 NestJS REPL 中的`debug`函数:

```
sh
debug() 

```

![REPL Debug Function](img/a991a6feb672d92702d3d68ee0a95c05.png)

如果要打印特定模块的组件，请将该模块作为参数传递:

```
sh
debug(AppModule)

```

此外，您还可以使用`debug.help`命令来访问内置的 REPL 文档。它返回对`debug`函数及其函数签名的一行描述，如下所示:

![Use The Debug.help Command To Access The Built-in REPL Documentation](img/5a54f0da4ea3f09f02b6f9ff083860d4.png)

### 如何查看控制器和提供者中的公共方法

**控制器**和**提供者**是 NestJS 应用程序的核心构件。在典型的 NestJS 应用程序中，控制器处理 HTTP 请求和响应，同时将更复杂的任务委托给提供者。

您可以使用`methods`函数来查看应用程序的控制器和提供者中的公共方法。要打印特定控制器或提供者中的方法，将控制器或提供者的名称作为参数传递给`methods`函数，如下所示:

```
sh
methods(QuotesController)

```

![Methods Function](img/3e4c0db4b8d12b88428e962ccb36bf27.png)

此外，您还可以使用`methods.help`命令来访问关于`methods`功能的内置文档。在终端上运行`methods.help`命令，打印文档及其函数签名的单行摘要。

![Use Methods.help Command To Access The Built-in Documentation](img/875412b2096db8add2e6667a93fedf41.png)

### 如何检索控制器或可注入的实例

NestJS REPL 的主要好处是从命令行与 NestJS 应用程序进行交互。

如果你想访问你的应用程序的控制器和可注入的实例，那么`get`函数就很方便，而且它对于从命令行测试你的控制器和服务中的方法也很有用——注意你可以互换使用`get`和`$`。

以下示例显示了如何检索控制器的实例。

```
sh
get(QuotesController).getQuotes()

```

![Retrieve Instance Of A Controller.](img/e07f04be40b3b84b208e32d58c0afe4f.png)

您还可以访问控制器或服务实例，并在使用之前将其保存在变量中，如下所示:

```
sh 
const quotesController = get(QuotesController)
quotesController.getQuotes()

```

像上面强调的其他功能一样，您可以使用`get.help`或`$.help`命令在终端上打印内置的`get`文档。

![Built-in Get Documentation On Terminal](img/f8c356c4f8c83a6c069aca6850331832.png)

### 如何解决控制器或可注入的瞬态实例

正如上面指出的，您可以使用`get`函数来检索控制器和可注入的实例。

然而，将`get`用于作用域或瞬态提供者会抛出错误。因此，您需要使用`resolve`函数来解析可注入变量或控制器的瞬态实例。其用法与上面提到的其他功能类似。

您可以像这样将请求范围的 injectable 或 controller 传递给`resolve`函数:

```
sh
const quotesController = await resolve(QuotesController)
quotesController.getQuotes()

```

类似地，使用`resolve.help`命令获得对`resolve`函数及其参数和返回类型的一行描述。

![Resolve Function](img/b35956dee408dde324f82468a47b6195.png)

### 如何导航模块树

如果您想在选择一个特定的模块后浏览模块树，那么`select`函数会很有用。像前面的函数一样，`select`将您想要选择的模块作为参数。

```
sh
select(AppModule).get(QuotesController).getQuotes()

```

![Select Function](img/6026b0019e1da50993a1e221e83ad712.png)

类似地，使用`select.help`命令访问关于`select`函数的内置文档。

![Select Help Command](img/2769b56b12dcd9bd1bb17651e431f8b3.png)

## 如何使用节点 REPL 环境

启动 NestJS REPL 环境将返回节点 REPL 服务器对象。因此，您可以从 NestJS REPL 内部访问节点 REPL 环境的所有特性。换句话说，通过将 NestJS REPL 与内置的节点 REPL 一起使用，您可以充分利用它。

在本节中，我们将了解节点 REPL 环境的一些特性。

### 如何将评估的命令保存到文件中

可以从 NestJS REPL 环境中访问的节点 REPL 提供了将特定 REPL 会话中评估的命令保存到文件中的功能。

您可以使用带有文件名的`.save`命令，就像这样:

```
sh
.save test.js

```

![.save Command](img/b339dc8929d6a4b06c56b80fe318ff02.png)

上述命令将创建`test.js`文件，并保存在当前 REPL 会话中评估的命令。

### 如何将 JavaScript 文件加载到当前 REPL 会话中

您可以将评估的命令保存到文件中。同样，您可以使用`.load`命令将文件的内容加载到当前的 REPL 会话中。您需要指定文件名，如下例所示。

```
sh
.load test.js

```

![.load Command](img/13fc3e979d412d12b86d005831f125e9.png)

### 如何进入编辑器模式

在 REPL 模式下，一次只能执行一条完整的语句。要在执行前输入多行代码，使用`.editor`命令启动编辑器模式。

![The .editor Command](img/2bc51c762206fe83788d42722adac04b.png)

之后就可以输入多行代码再执行了。您可以使用`CTRL+D`组合键退出编辑器模式，或者使用`CTRL+C`组合键取消编辑模式。

### 特殊下划线变量

节点 REPL 环境有一个`_`变量，您可以使用它从 NestJS REPL 环境中访问最后一次操作的结果。如果您想使用前面操作的结果，这是一个方便的变量。

上述特征决不是穷尽的。查看节点[文档](https://nodejs.org/api/repl.html),了解节点 REPL 环境的其他特性，这些特性可能对 NestJS REPL 环境有用。

## 结论

NestJS REPL 环境是 NestJS 9 附带的，是对 NestJS 的一个相对较新的补充。

像大多数 REPL 环境一样，您可以使用它从命令行与您的 NestJS 应用程序进行交互。值得注意的是，它的用途包括检查应用程序的依赖图，以及从终端调用提供者和控制器上的方法。

正如我们前面看到的，NestJS REPL 环境有几个本机函数。这些功能包括:`get`、`debug`、`resolve`和`select`。为了充分利用它，使用 NestJS REPL 环境和内置的节点 REPL 环境。

如果我错过了什么或者你自己使用 NestJS REPL 环境的经历，请在下面的评论区告诉我！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)