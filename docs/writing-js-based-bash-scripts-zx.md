# 用 zx - LogRocket 博客编写基于 JS 的 Bash 脚本

> 原文：<https://blog.logrocket.com/writing-js-based-bash-scripts-zx/>

## 介绍

Bash 是一种命令语言，通常以命令行解释程序的形式出现，用户可以通过终端软件执行命令。例如，我们可以使用 Ubuntu 的终端来运行 Bash 命令。我们还可以通过 shell 脚本创建和运行 Bash 脚本文件。

程序员在许多自动化场景中使用 shell 脚本，例如构建过程、CI/CD 或计算机维护相关的活动。作为一种全功能的命令语言，Bash 支持管道、变量、函数、控制语句和基本的算术运算。

然而，Bash 不是一种通用的开发人员友好的编程语言。不支持 OOP，不支持 JSON 这样的结构，不支持数组以外的常用数据结构，不支持内置的字符串或数组操作方法。这意味着程序员经常需要从 Bash 调用单独的 Python 或节点脚本来满足这种需求。

这就是 zx 项目的用武之地。zx 引入了一种使用 JavaScript 编写类似 Bash 的脚本的方法。

相比之下，JavaScript 几乎拥有开发人员需要的所有内置特性。zx 通过为几个关键的与 CLI 相关的 Node.js 包提供包装 API，让程序员用 JavaScript 编写 shell 脚本。因此，您可以使用 zx 编写开发人员友好的、类似 Bash 的 shell 脚本。

在这篇文章中，我将解释 zx 并教你如何在你的项目中使用它。

### Bash 和 zx 的比较

Bash 是一种单通解释命令语言，最初由 Brian Fox 开发。程序员经常在 Unix 或类似 Unix 的命令的帮助下使用它。

大多数时候，Bash 启动单独的进程来执行不同的子任务。例如，如果您使用`expr`命令进行算术运算，Bash 解释器将总是产生另一个进程。

原因是`expr`是一个命令行程序，需要一个单独的进程来运行。当您向脚本文件添加更多逻辑时，您的 shell 脚本可能看起来很复杂。由于产生了额外的进程和解释，您的 shell 脚本也可能最终执行缓慢。

zx 项目实现了一个类似于 Bash 的 shell 脚本执行器，但是使用了 JavaScript 模块。它提供了一个内置的异步 JavaScript API 来调用其他类似于 Bash 的命令。除此之外，它还为几个基于 Node.js 的命令行助手提供了包装函数，如 [chalk](https://www.npmjs.com/package/chalk) 、[minimit](https://www.npmjs.com/package/minimist)、 [`fs-extra`](https://www.npmjs.com/package/fs-extra) 、 [OS](https://nodejs.org/api/os.html) 和 [Readline](https://nodejs.org/api/readline.html) 。

### zx 是怎么工作的？

每个 zx shell 脚本文件都以`.mjs`为扩展名。第三方 API 的所有内置函数和包装器都是预先导入的。因此，您不必在基于 JavaScript 的 shell 脚本中使用额外的 import 语句。

zx 接受来自标准输入、文件和 URL 的脚本。它将你的 zx 命令集作为 ECMAScript 模块(MJS)导入执行，命令执行流程使用 [Node.js 的子流程 API](https://nodejs.org/api/child_process.html) 。

现在，让我们使用 zx 编写一些 shell 脚本来更好地理解这个项目。

## zx 脚本教程

首先，在开始编写 zx 脚本之前，需要全局安装 [zx npm 包](https://npmjs.com/package/zx)。确保您已经安装了 Node.js v14.8.0 或更高版本。

在您的终端上运行以下命令来安装 zx 命令行程序。

```
npm install -g zx

```

在您的终端中输入`zx`,检查程序是否安装成功。您将得到如下所示的输出。

![The output when zx is installed successfully](img/bf495bffa7c79ba18708f55ad2539bc2.png)

### zx 的基础

让我们创建一个简单的脚本来获取 Git 项目的当前分支。

在您的一个项目中创建`get_current_branch.mjs`，并添加以下代码。

```
#!/usr/bin/env zx
const branch = await $`git branch --show-current`
console.log(`Current branch: ${branch}`)

```

第一行是 [shebang 行](https://en.wikipedia.org/wiki/Shebang_%28Unix%29),它告诉操作系统的脚本执行器选择正确的解释器。`$`是一个函数，当它与`await`关键字一起使用时，执行给定的命令并返回其输出。最后，我们使用`console.log`来显示当前分支。

使用以下命令运行您的脚本，以获取项目的当前 Git 分支。

```
zx ./get_current_branch.mjs

```

它还会显示您已经执行的每个命令，因为 zx 默认打开了详细模式。如下所示更新您的脚本，去掉额外的命令细节。

```
#!/usr/bin/env zx
$.verbose = false
const branch = await $`git branch --show-current`
console.log(`Current branch: ${branch}`)

```

由于最上面的 shebang 行，您也可以在没有 zx 命令的情况下运行脚本。

* * *

### log 火箭的更多精彩文章:

* * *

```
chmod +x ./get_current_branch.mjs
./get_current_branch.mjs

```

#### 颜色和格式

zx 也公开了 chalk 库 API。因此，我们可以使用它进行着色和格式化，如下所示。

```
#!/usr/bin/env zx
$.verbose = false
let branch = await $`git branch --show-current`
console.log(`Current branch: ${chalk
                                .bgYellow
                                .red
                                .bold(branch)}`)

```

更多的着色和格式化方法可以在 [chalk 的官方文档](https://www.npmjs.com/package/chalk)中找到。

#### 用户输入和命令行参数

zx 提供了`question`函数来捕获来自命令行界面的用户输入。您可以使用`choices`选项启用传统的 Unix 制表符结束。

以下脚本从用户处捕获文件名和模板。之后，它使用用户输入的配置搭建一个文件。第二个问题可以用制表符补全。

```
#!/usr/bin/env zx
$.verbose = false
let filename = await question('What is the filename? ')
let template = await question('What is your preferred template? ', {
  choices: ["function", "class"] // Enables tab completion.
})
let content = ""

if(template == "function") {
    content = `function main() {
    console.log("Test");
}`;
}
else if(template == "class") {
    content = `class Main {
    constructor() {
        console.log("Test");
    }
}`;
}
else {
    console.error(`Invalid template: ${template}`)
    process.exit();
}
fs.outputFileSync(filename, content)

```

经过解析的命令行参数对象可作为全局`argv`常量使用。解析是使用 minimist Node.js 模块完成的。

看一下下面的例子，它捕获了两个命令行参数值。

```
#!/usr/bin/env zx
$.verbose = false
const size = argv.size;
const isFullScreen = argv.fullscreen;
console.log(`size=${size}`);
console.log(`fullscreen=${isFullScreen}`);

```

如下所示运行上面的脚本文件，检查命令行参数的支持。

```
./yourscript.mjs --size=100x50 --fullscreen

```

#### 网络请求

程序员经常使用`curl`命令通过 Bash 脚本发出 HTTP 请求。zx 为[节点获取模块](https://www.npmjs.com/package/node-fetch)提供了一个包装器，它将特定模块的 API 公开为`fetch`。优点是 zx 不会像 Bash 使用`curl`那样为每个网络请求生成多个进程——因为节点获取包使用节点的标准 HTTP APIs 来发送网络请求。

让我们做一个简单的 HTTP 请求来熟悉 zx 的网络请求 API。

```
#!/usr/bin/env zx
$.verbose = false
let response = await fetch('https://cheat.sh');
if(response.ok) {
    console.log(await response.text());
}

```

上面的 zx 脚本将在 node-fetch 模块的帮助下下载并显示特定 URL 的内容。它不像 Bash 的网络调用那样产生单独的进程。

#### 构建命令管道

在 shell 脚本中，管道是指多个顺序执行的命令。我们经常在 shell 脚本中使用众所周知的管道字符(`|`)将输出从一个进程传递到另一个进程。zx 提供了两种不同的构建管道的方法。

我们可以在命令集中使用`|`字符，类似于 Bash 脚本——或者我们可以使用 zx 内置 API 中的`.pipe()`链方法。在以下示例脚本中，检查管道是如何以两种方式实现的。

```
#!/usr/bin/env zx
$.verbose = false
// A pipeline using |
let greeting = await $`echo "Hello World" | tr '[l]' [L]`
console.log(`${greeting}`)
// The same pipeline but with the .pipe() method
greeting = await $`echo "Hello World"`
    .pipe($`tr '[l]' [L]`)

console.log(`${greeting}`)

```

### 高级用例

除了基于 JavaScript 的 shell 脚本支持，zx 还支持其他一些有用的特性。

默认情况下，zx 使用 Bash 解释器来运行命令。我们可以通过修改配置变量`$.shell`来改变默认的 shell。下面的脚本使用了`sh` shell，而不是`bash`。

```
$.shell = '/usr/bin/sh'
$.prefix = 'set -e;'

$`echo "Your shell is $0"` // Your shell is /usr/bin/sh

```

您可以使用 zx 命令行程序来执行用 JavaScript 编写的特定 Markdown 文件的代码片段。如果您提供一个 Markdown 文件，zx 命令行程序将解析并执行代码块。

让我们看一个例子。从 zx GitHub 下载这个[示例 Markdown 文件，保存为`markdown.md`。之后，运行下面的命令来执行代码块。](https://github.com/google/zx/blob/main/examples/markdown.md)

```
zx markdown.md 

```

zx 命令行程序也可以从 URL 运行脚本。像提供文件名一样提供到 zx 脚本的链接。下面的远程脚本将显示一条问候消息。

```
zx https://raw.githubusercontent.com/shalithasuranga/zx-scripting-examples/main/greeting.mjs

```

您也可以从基于节点的 web 应用程序中导入`$`函数。然后，可以从 web 应用程序的后端运行命令。

如下图导入 zx 的`$`函数，从其他 JavaScript 源文件调用操作系统的命令。

```
import { $ } from 'zx'
await $`whoami`

```

#### 将 zx 与 TypeScript 一起使用

zx 也有 TypeScript 定义，尽管还没有完全支持。因此，程序员可以通过 TypeScript 使用 zx 的所有内置 API。我们可以直接将类型脚本文件作为 zx 文件提供给 zx 命令行程序。然后，zx 将 transpile 并执行提供的 TypeScript 源文件。

此外，可以在基于 TypeScript 的 web 应用程序中使用 zx 来执行操作系统的命令。

## 结论

Bash 脚本是自动化开发过程的好方法。但是，当 Bash 脚本变得复杂时，有时您可能不得不用其他编程语言编写单独的脚本。

zx 项目提供了一种用 [JavaScript](https://blog.logrocket.com/tag/vanilla-javascript) 和[类型脚本](https://blog.logrocket.com/tag/typescript)编写类似 Bash 的脚本的简单方法。它提供了 Bash 式的最小 API 来给我们正在做的事情一种 shell 脚本的感觉——即使我们正在编写一个 JavaScript 源文件。

此外，zx 促使开发人员编写没有分号的基于 JavaScript 的 shell 脚本，以使 zx 脚本和 Bash 脚本在语法上相似。

然而，zx 不是 Bash 的替代品——它在内部使用命令行解释器(默认为 Bash)来执行命令。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。