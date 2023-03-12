# bun:JavaScript 运行时采用 Node.js 和 Deno - LogRocket 博客

> 原文：<https://blog.logrocket.com/bun-javascript-runtime-taking-node-js-deno/>

Bun 是一个新的、速度极快的 JavaScript 运行时，人人都在谈论它。为了理解为什么 [Bun](https://bun.sh) 如此重要，我们先来回顾一些重要的 JavaScript 历史。

## 什么是 JavaScript 引擎？

当 JavaScript 最初被创建时，它只在浏览器中运行，最初是 Netscape Navigator。然而，开发人员需要能够读取 JavaScript 代码并将其转换成可以在计算机上运行的软件。这项技术被称为 JavaScript 引擎。在撰写本文时，有三个主要的 JavaScript 引擎支持您喜爱的浏览器:

*   V8:由谷歌为 Chrome 开发
*   SpinderMonkey:由 Mozilla 为 Firefox 创建
*   JavaScriptCore:苹果公司为 Safari 开发的

每个 JavaScript 引擎在对 JavaScript 规范的支持、采用新 JavaScript 特性的速度以及易用性和性能方面都有自己的细微差别。

## JavaScript 运行时简介

最终，在 2009 年[，Ryan Dahl](https://javascript.plainenglish.io/a-brief-history-of-javascript-9289a4d344d2) 第一次开始开发一个允许 JavaScript 在浏览器之外运行的工具。当选择一个引擎来构建这个工具时，他选择了 V8。

他创建的是一个 JavaScript 运行时，一个在浏览器之外运行 JavaScript 的工具。它让 JavaScript 能够访问更广泛的计算机网络和文件系统，以创建 web 服务器和任何类型的应用程序。

Node.js 从此大受欢迎，成为前端和后端 web 开发的首选工具。创建 Node.js 时，许多现代 JavaScript 标准还不存在，比如 Fetch API、ES 模块等等。

看到 TypeScript 的增长和 web 标准的健壮性，Ryan Dahl 使用 Rust 创建了 Node.js 的[继任者，称为 Deno](https://blog.logrocket.com/what-is-deno/) 。Deno 提供了速度改进、对 web 标准的拥抱，以及对 TypeScript 和 JSX 的一流支持。

## Bun 是什么？

2022 年，前 Stripe 开发者 Jared Sumner 发布了 [Bun](https://bun.sh/) 。Bun 是用 Zig 编程语言开发的运行时，它也包含 web 标准，但旨在与 Node.js APIs 兼容，因此开发人员可以轻松地迁移现有代码。

最有趣的选择之一是 Bun 使用 JavaScriptCore 作为其引擎，不像 Node.js 和 Deno 使用 V8。其结果是运行速度极快，还为 JavaScript 开发人员提供了几个高质量的特性。

Bun 也有一流的打字稿和 JSX 集成。它旨在提供 transpilers 的许多特性，如 Babel，以及 Bundlers，如 Webpack、Rollup、Snowpack 和 Vite。

## 带 Bun 去试驾

要开始使用 Bun，首先，我们必须安装它。根据 Bun 文档，安装只需要以下命令:

```
curl https://bun.sh/install | bash

```

请记住，这个命令只适用于 Mac 和 Linux。所以，如果你使用的是 Windows，你需要为 Linux 设置[窗口子系统](https://docs.microsoft.com/en-us/windows/wsl/about)来安装 Bun。

安装完成后，请务必阅读确认提示，了解如何将 Bun 添加到您的`PATH`中。它将要求您在您的`.bashrc`或`.zshrc`文件中添加以下行:

```
BUN_INSTALL="/home/<username>/.bun"
PATH="$BUN_INSTALL/bin:$PATH"

```

现在，如果您运行`bun` `--version`，您应该会得到一个版本号，确认您已经正确安装了它。

## 编写和运行我们的第一个 Bun 脚本

创建一个名为`script.js`的文件，并在其中添加以下代码:

```
Bun.serve({
    fetch(request){
        return new Response("Hello World")
    }
})
console.log("Listening on Port 3000")

```

`Bun.serve`启动服务器并获取一个带有服务器配置的对象。在每个请求中，请求对象被传递给一个函数，该函数被存储为配置对象上的`fetch`属性。

我们可以通过使用命令`bun run script.js`来运行`Bun.serve`，然后转到`localhost:3000`来查看对我们请求的响应。如果我们想改变它将服务于哪个端口，我们可以向传递给`Bun.serve`的对象添加一个`port`属性。

## 用 Bun 写文件

Bun 有一个非常简单的 API 来写文件。让我们修改我们的脚本，在每次提交请求时写入文件:

```
let count = 1
Bun.serve({
    fetch(request){
        Bun.write(`${count}.txt`, request.url)
        count += 1
        return new Response("Hello World")
    },
})
console.log("Listening on Port 3000")

```

运行上面的代码并访问`localhost:3000/cheese`，您会看到创建了两个新文件，`1.txt`和`2.txt`。`Bun.write`的第一个参数是写的目标，像文件或`stdout`，第二个参数是写什么。

## 内置对 SQLite3 的支持

与其他 JavaScript 运行时不同，您不必安装 SQLite3，因为它是开箱即用的。让我们用下面的代码创建一个名为`db.js`的新文件:

```
import { Database } from "bun:sqlite";
// Create a new Database File
const db = new Database("db.sqlite3");
// Create a table in the database
db.run("CREATE TABLE IF NOT EXISTS cheeses (name VARCHAR(100));")
// Insert Some Values into the table
db.run("INSERT INTO cheeses VALUES ('gouda'), ('munster'), ('brie');")
// Query the table
const result = db.query("SELECT * FROM cheeses;").all()
// Log results
console.log(result)

```

用`bun run db.js`运行代码，您应该会看到终端上记录的插入记录。

## 将`.env`文件与 Bun 一起使用

另一个非常好的地方是能够开箱即用地使用`.env`文件。您可以像在 Node.js 中一样简单地使用`process.env`来访问它们，而不需要安装任何库。用以下命令创建一个`.env`文件:

```
VARIABLE=cheddar

```

现在，让我们用下面的代码更新我们的`script.js`:

```
// let count = 1
Bun.serve({
    fetch(request){
        // Bun.write(`${count}.txt`, request.url)
        // count += 1
        return new Response(process.env.VARIABLE)
    },
})
console.log("Listening on Port 3000")

```

现在，当我们运行`bun run script.js`并访问`localhost:3000`时，我们应该会看到从我们的`.env`文件返回的信息。

## 结论

除了超快之外，Bun 还有一些非常好的特性，使得许多更普通的任务变得非常容易，比如写文件、管理简单的数据库和使用环境变量。

Bun 会超越 Deno，挑战 Node.js 的宝座吗？我们必须等等看。Bun 至少会像 Deno 一样展示 Node.js 在开拓自己的空间时可以采用的许多创新。

不管怎样，对于各地的 JavaScript 开发人员来说，在这个领域拥有另一个运行时是一个巨大的胜利。在撰写本文时，Bun 仍处于早期开发阶段，许多 API 和特性尚未实现。然而，目前可用的内容令人印象深刻，因此值得继续关注。

一定要看看我第一次试运行 Bun 的视频，如果你有任何问题，请留下评论。编码快乐！

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。