# 在 Node.js - LogRocket 博客中使用 execa 运行命令

> 原文：<https://blog.logrocket.com/running-commands-with-execa-in-node-js/>

Node.js 内置了`[child_process](https://nodejs.org/api/child_process.html)`模块。它提供了允许我们编写在子进程中运行命令的脚本的方法。这些命令可以运行我们运行脚本的机器上安装的任何程序。

## execa 是什么？

[execa](https://www.npmjs.com/package/execa) 库为`child_process`模块提供了一个包装器，以方便使用。这个受欢迎的库是由多产的开源开发者[辛德雷·索胡斯](https://github.com/sindresorhus/)创建和维护的，它每周被下载数百万次。

在本文中，我们将了解`execa`库的好处以及如何开始使用它。我们还将深入研究错误处理和流程控制的基础知识，以及处理子流程输出的选项。

要跟进，您需要:

*   熟悉 JavaScript 和 Node.js 的基础知识
*   要在终端中轻松运行命令
*   安装 [Node.js](https://nodejs.org/en/download/) > /= v14.13.1
*   在 macOS、Linux 或 Windows 上运行 node . js([Windows 子系统 for Linux](https://docs.microsoft.com/windows/wsl/about) )

本文所有代码[均可在 GitHub](https://github.com/simonplend/using-execa-es-module-in-node) 上获得。

## 使用 execa 的好处

与内置的 Node.js `child_process`模块相比，execa 提供了几个好处。

首先，execa 公开了一个基于承诺的 API。这意味着我们可以在代码中使用 async/await，而不需要像使用异步`child_process`模块方法那样创建回调函数。如果我们需要，还有一个同步运行命令的`execaSync`方法。

Execa 还方便地转义并引用我们传递给它的任何命令参数。例如，如果我们传递一个带有空格或引号的字符串，execa 会为我们处理转义它们。

程序在输出的末尾包含一个新行是很常见的。这有利于命令行的可读性，但在脚本环境中没有帮助，所以默认情况下，execa 会自动删除这些新行。

如果正在执行我们的脚本(`node`)的进程由于某种原因而死亡，我们可能不希望子进程一直存在。Execa 会自动为我们清理子进程，确保我们不会以“僵尸”进程告终。

使用 execa 的另一个好处是它支持在 Windows 上用 Node.js 运行子进程。它使用流行的[交叉衍生](https://www.npmjs.com/package/cross-spawn)包，该包解决了 Windows 上 Node.js' `child_process.spawn`方法的已知问题。

## execa 入门

打开您的终端，创建一个名为`tutorial`的新目录，然后切换到该目录:

```
mkdir tutorial

cd tutorial

```

现在，让我们创建一个新的`package.json`文件:

```
npm init --yes

```

接下来，安装 [execa](https://www.npmjs.com/package/execa) 库:

```
npm install execa

```

### 在 Node.js 中使用纯 ES 模块包

Node.js 支持两种不同的模块类型:

*   [CommonJS 模块](https://nodejs.org/api/modules.html) (CJS):使用`module.exports`导出函数和对象，使用`require()`将它们加载到另一个模块中
*   [ECMAScript 模块](https://nodejs.org/api/esm.html) (ESM):使用`export`导出函数和对象，使用`import`将它们加载到另一个模块中

Execa 随着其 [v6.0.0 发布](https://github.com/sindresorhus/execa/releases/tag/v6.0.0)成为一个纯粹的 ESM 包。这意味着我们必须使用对 ES 模块有[支持的 Node.js 版本，以便使用这个包。](https://simonplend.com/what-you-need-to-know-about-es-modules-in-node-js/)

对于我们自己的代码，我们可以通过在我们的`package.json`中添加`"type": "module"`来向 Node.js 显示我们项目中的所有模块都是 es 模块，或者我们可以将各个脚本的文件扩展名设置为`.mjs`。

让我们更新我们的`package.json`文件:

```
  {
    "name": "tutorial",
+   "type": "module",

```

我们现在可以在我们创建的任何脚本中从`execa`包中导入 execa 方法:

```
import { execa } from "execa";

```

尽管 ES 模块在 Node.js 包和应用程序中被采用，但 CommonJS 模块仍然是使用最广泛的模块类型。如果您因为任何原因无法在代码库中使用 ES 模块，您将需要在 CommonJS 模块中使用`import`方法:

```
async function run() {
  const { execa } = await import("execa");

  // Code which uses `execa` here.
}

run();

```

注意，我们需要将对`import`函数的调用包装在一个`async`函数中，因为 CommonJS 模块不支持[顶级 await](https://nodejs.org/api/esm.html#top-level-await) 。

### 使用 execa 运行命令

现在，我们将创建一个使用 execa 运行以下命令的脚本:

```
echo "execa is pretty neat!"

```

`echo`程序打印出传递给它的文本字符串。

让我们创建一个名为`run.js`的新文件。在这个脚本中，我们将从 execa 包中导入`execa`方法:

```
// run.js

import { execa } from "execa";

```

然后，我们将使用`execa`方法运行`echo`命令:

```
// run.js

const { stdout } = await execa("echo", ["execa is pretty neat!"]);

```

`execa`返回的承诺用一个对象解析。您会注意到在上面的代码中,`stdout`属性被从这个对象中解包并赋给了一个变量。

让我们记录下`stdout`变量，这样我们可以看到它的值是什么:

```
// run.js

console.log({ stdout });

```

现在我们可以运行脚本了:

```
node run.js

```

我们应该会看到以下输出:

```
{ stdout: 'execa is pretty neat!' }

```

### 什么是`stdout`？

程序可以访问“标准流”。在本教程中，我们将使用其中的两个:

*   `stdout`:标准输出是程序将其输出数据写入的流
*   `stderr`:标准错误是程序写入错误信息和调试数据的流

当我们运行一个程序时，这些标准流通常连接到父进程。如果我们在终端中运行一个程序，这意味着程序发送给`stdout`和`stderr`流的数据将被终端接收和显示。

当我们运行本教程中的脚本时，子进程的`stdout`和`stderr`流连接到父进程`node`，允许我们访问子进程发送给它们的任何数据。

## 与 execa 合作

现在我们有了一个可以在子进程中运行命令的脚本，我们将深入了解使用 execa 的基础知识。

### 捕捉`stderr`

让我们修改一下`run.js`中调用`execa`方法从对象中解包`stderr`属性的那一行:

```
- const { stdout } = await execa("echo", ["execa is pretty neat!"]);
+ const { stdout, stderr } = await execa("echo", ["execa is pretty neat!"]);

```

然后更新`console.log`行:

```
- console.log({ stdout });
+ console.log({ stdout, stderr });

```

现在，让我们再次运行脚本:

```
node run.js

```

我们应该会看到以下输出:

```
{ stdout: 'execa is pretty neat!', stderr: '' }

```

您会注意到`stderr`的值是一个空字符串(`''`)。这是因为`echo`命令没有向标准错误流写入数据。

[ls](https://www.man7.org/linux/man-pages/man1/ls.1.html) (1)程序列出了文件和目录的信息。如果文件不存在，它将向标准错误流中写入一条错误消息。

让我们替换正在`run.js`中执行的命令:

```
- const { stdout, stderr } = await execa("echo", ["execa is pretty neat!"]);
+ const { stdout, stderr } = await execa("ls", ["missing-file.txt"]);

```

当我们运行脚本(`node run.js`)时，我们现在应该看到以下输出:

```
Error: Command failed with exit code 2: ls missing-file.txt
...
{
  shortMessage: 'Command failed with exit code 2: ls missing-file.txt',
  command: 'ls missing-file.txt',
  escapedCommand: 'ls missing-file.txt',
  exitCode: 2,
  signal: undefined,
  signalDescription: undefined,
  stdout: '',
  stderr: "ls: cannot access 'missing-file.txt': No such file or directory",
  failed: true,
  timedOut: false,
  isCanceled: false,
  killed: false
}

```

用`execa`方法运行这个命令时抛出了一个错误。这是因为子进程'[的退出代码](https://en.wikipedia.org/wiki/Exit_status)不是`0`。退出代码`0`通常表明一个进程成功运行，其他任何值都表明有问题。`ls`的手册页定义了以下退出代码及其含义:

```
Exit status:
    0      if OK,

    1      if minor problems (e.g., cannot access subdirectory),

    2      if serious trouble (e.g., cannot access command-line argument).

```

由`execa`方法抛出的错误对象包含一个`stderr`属性，该属性包含由`ls`写入标准错误流的数据。

我们现在需要实现错误处理，这样如果命令失败，我们就可以优雅地处理它，而不是让它破坏我们的脚本。

注意:程序通常会成功运行，但也会将调试消息写入`stderr`。

### execa 中的错误处理

我们可以将命令包装在一个`try...catch`语句中，并输出一个定制的错误消息，如下所示:

```
// run.js

try {
  const { stdout, stderr } = await execa("ls", ["missing-file.txt"]);

  console.log({ stdout, stderr });
} catch (error) {
  console.error(
    `ERROR: The command failed. stderr: ${error.stderr} (${error.exitCode})`
  );
}

```

现在，当我们运行我们的脚本(`node run.js`)时，我们应该看到以下输出:

```
ERROR: The command failed. stderr: ls: cannot access 'missing-file.txt': No such file or directory (2)

```

### 取消子进程

一旦我们开始执行一个命令，我们可能想要取消这个过程，例如，如果它花费的时间比预期的长。Execa 提供了一个`cancel`方法，我们可以调用它来向子进程发送一个`SIGTERM` [信号](https://en.wikipedia.org/wiki/Signal_(IPC)#SIGTERM)。

让我们替换掉`run.js`中除了`import`语句以外的所有代码。我们将使用 [sleep](https://www.man7.org/linux/man-pages/man1/sleep.1.html) 程序创建一个运行五秒钟的子进程:

```
// run.js

const subprocess = execa("sleep", ["5s"]);

```

您会注意到在这个函数调用前面没有关键字`await`。这是因为我们希望首先定义一个函数，该函数将在一秒钟后运行并取消子进程:

```
// run.js

setTimeout(() => {
  subprocess.cancel();
}, 1000);

```

然后我们可以在一个`try...catch`块中`await`这个`subprocess`承诺:

```
// run.js

try {
  const { stdout, stderr } = await subprocess;

  console.log({ stdout, stderr });
} catch (error) {
  if (error.isCanceled) {
    console.error(`ERROR: The command took too long to run.`)
  } else {
    console.error(error);
  }
}

```

当调用了`subprocess.cancel`方法时，error 对象的`isCanceled`属性被设置为`true`。这使我们能够确定错误的原因并显示特定的错误消息。

现在，当我们运行我们的脚本(`node run.js`)时，我们应该看到以下输出:

```
ERROR: The command took too long to run.

```

如果我们需要强制一个子进程结束，我们可以调用`[subprocess.kill](https://www.npmjs.com/package/execa#user-content-killsignal-options)` [方法](https://www.npmjs.com/package/execa#user-content-killsignal-options)而不是`subprocess.cancel`。

### 使用 execa 从子进程管道输出

由`execa`方法返回的对象中的`stdout`和`stderr`属性是[可读流](https://nodejs.org/api/stream.html#stream_readable_streams)。我们已经看到了如何将发送给这些流的数据保存在变量中。例如，我们可以将可读的流通过管道传输到可写的流中，以查看子进程运行时的输出。

让我们删除对`run.js`中`setTimeout`的调用:

```
- setTimeout(() => {
-   subprocess.cancel();
- }, 1000);

```

让我们将命令改为再次使用`echo`:

```
- const subprocess = execa("sleep", ["5s"]);
+ const subprocess = execa("echo", ["is it me you're looking for?"]);

```

然后，我们将把来自子流程的`stdout`流通过管道传输到父流程的`stdout`流中(`node`):

```
subprocess.stdout.pipe(process.stdout);

```

现在，当我们运行该脚本时，我们应该会看到以下输出:

```
is it me you're looking for?
{ stdout: "is it me you're looking for?", stderr: '' }

```

我们的脚本现在在运行时输出来自子流程'`stderr`流的数据，然后记录保存流数据的`stderr`和`stdout`变量的值。

### 将输出重定向到文件

我们可以将子进程的数据写到一个文件中，而不是将子进程的数据传送到父进程的流中。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

首先，我们需要`import`内置 Node.js [fs](https://nodejs.org/api/fs.html) 模块:

```
// run.js

import * as fs from "fs";

```

然后我们可以替换现有的对`pipe`方法的调用:

```
- subprocess.stdout.pipe(process.stdout);
+ subprocess.stdout.pipe(fs.createWriteStream("stdout.txt"));

```

这将创建一个`[fs.WriteStream](https://nodejs.org/api/fs.html#class-fswritestream)`实例，来自`subprocess.stdout`可读流的数据将通过管道传输到该实例。

当我们运行脚本时，应该会看到以下输出:

```
{ stdout: "is it me you're looking for?", stderr: '' }

```

我们还应该看到已经创建了一个名为`stdout.txt`的文件，其中包含来自子流程`stdout`流的数据:

```
$ cat stdout.txt
is it me you're looking for?

```

## 结论

在本文中，我们学习了如何使用 execa 从 Node.js 脚本中运行命令。我们已经看到了它提供的优于内置 Node.js `child_process`模块的好处，并了解了我们需要应用来有效使用 execa 的基本模式。

本文所有代码[均可在 GitHub](https://github.com/simonplend/using-execa-es-module-in-node) 上获得。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.