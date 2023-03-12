# 在 Deno - LogRocket 博客中设置 cron 作业

> 原文：<https://blog.logrocket.com/setting-up-cron-jobs-in-deno/>

自动化可以通过改进和简化流程和重复性任务来节省时间。在类似 unix 的系统中，您可以使用 cron 作业自动化任务。

## 什么是 cron 工作？

cron 作业是系统进程(cron)的 unix 术语，它允许您在指定的权限集下安排任务在以后运行。本质上，它是一个调度程序，使应用程序能够调度一个作业在特定的日期或时间自动运行。在本文中，我们将把 cron 作业集成到一个 [Deno](https://deno.land/) 应用程序中。

## 先决条件

*   对 JavaScript 的理解
*   文本编辑器(在我们的例子中，我们将使用 [VS 代码](https://code.visualstudio.com/)
*   安装在本地机器上的邮递员

## 安装 Deno

如果您的本地机器上还没有安装 Deno，您可以使用 [Homebrew](https://brew.sh/) 安装它:

```
brew install deno

```

安装完成后，在您的终端上运行`deno`以确认安装成功。

Deno 有一个智能作业调度程序库，我们将使用它。我们将首先在主目录中为我们的应用程序创建一个目录:

```
cd desktop && mkdir denocron && cd denocron
touch index.ts

```

为了在我们的应用程序中实现 cron 作业，我们必须将模块导入到我们的应用程序中:

```
import {cron, daily, monthly, weekly} from 'https://deno.land/x/deno_cron/cron.ts';

```

除了定义任务运行的自定义时间，Deno cron 已经提供了一些方法来创建每周、每月和每天的时间表。

## 在 Deno 中定义自定义计划

我们可以使用这个模块通过使用`cron`方法为任务运行创建一个自定义时间。该方法使用 cron 模式调度任务，其格式如下:

```
cron('* * * * * *', () => {
    // run some task
});

```

让我解释一下这里发生了什么。

*   第一个星号表示秒数。该值介于 0-59 之间
*   第二个星号表示分钟数，也是 0-59 之间的值
*   第三个星号表示小时数，其值在 0-23 之间
*   第四个星号代表一个月中的某一天，其值介于 0-31 之间
*   第五个星号代表一年中的月份，其值介于 0-31 之间
*   第六个星号表示一周中的某一天，其值介于 0-7 之间

我们可以编写一个每秒运行一次的简单作业:

```
cron('*/1 * * * * *', () => {
    // run some task
    console.log('This is a same thing')
});

```

要运行我们的应用程序，打开终端并运行`deno run index.ts`。

## 使用 Denon 运行我们的应用程序

就像 [Node.js](https://nodejs.org/en/) 有 [nodemon](https://nodemon.io/) 一样，Deno 也有`denon`包，每当发生变化时，它都会重新加载我们的应用程序。

要安装它，请打开您的终端并运行以下命令:

```
deno install -qAf --unstable https://deno.land/x/[email protected]/denon.ts

```

这个命令将在我们的开发机器上全局安装`denon`包。

现在我们可以运行我们的应用程序了，我们可以使用这个命令:`denon index.ts`。如果你用的是 MacBook，你可能会遇到一个错误，比如说`command not found: denon`:

![An Error Showing the Command Wasn't Found](img/b69a1f2728b73fd13831451684fccc5e.png)

如果遇到此错误，您可以执行下列操作之一:

如果您使用的是`zsh`终端，您可以通过运行以下命令进行配置:

```
export PATH="/Users/<user>/.deno/bin:$PATH"

```

其中,`<user>`目录是您在本地机器上的帐户名称。

如果您使用的是 bash 终端，您可以使用这个命令来配置它:

```
echo 'export PATH="$HOME/.deno/bin:$PATH"' >> ~/.bashrc

```

## cron 作业的用例:自动化电子邮件

cron 作业的一个常见用例是创建电子邮件和时事通讯的自动发送。我们可以编写一个简单的函数，它能够在每个月的第一天午夜执行一个函数。为此，我们将使用`cron`方法:

```
cron('1 0 0 1 */1 *', () => {
    // run a function
});

```

此方法采用计划配置和计划将要运行时要调用的方法。

我们可以编写一个每 30 秒运行一次的简单 cron 作业:

```
let task = cron('*/30 * * * * *', () => {
    // run some task
    console.log('This is a same thing')
});

```

这里有一个每 30 分钟运行一次的简单作业:

```
cron('1 */30 * * * *', () => {
    checkStock();
});

```

除了定义自定义作业之外，Deno cron 还有自己的内置方法。例如，我们有每日、每周和每月方法，它们将在指定的时间运行:

```
daily(() => {
    console.log('I run on daily basis')
});

weekly(() => {
console.log('This method will run on weekly bases')
});

everyMinute(()=> {
  console.log('This method will run on 60 seconds')  
})

```

要使用这种方法，我们必须首先导入它:

```
import { cron, everyMinute, daily, weekly } from 'https://deno.land/x/deno_cron/cron.ts';

```

要停止所有 cron 作业，我们可以使用`stop()`方法。要启动所有 cron 作业，请使用`start()`方法。

为了了解这是如何工作的，让我们设置一些布尔值。首先，导入这个方法:

```
import { cron, start,stop } from 'https://deno.land/x/deno_cron/cron.ts';
let task = cron('*/1 * * * * *', () => {
    // run some task
    console.log('This is the same thing')
});
let someBool = false
if (someBool) {
    start()
} else {
    stop()
}

```

我们首先从 url 导入`cron`、`start`和`stop`方法，然后使用 cron 方法创建一个每分钟运行的作业。

要运行应用程序，请将`someBool`的值设置为`true`。一旦完成，作业将每一分钟在控制台上记录`This is the same thing`。还要注意的是，`start`和`stop`方法用于控制作业的流程。

!["This is a same thing" Logging Automatically](img/5f30aebbc45e27e909758881a9390cbb.png)

## 结论

当构建一个大规模的应用程序时，Cron 作业可以派上用场，在这个应用程序中，您希望向所有客户发送预定的时事通讯，自动化您的任何消息，或者完成自动化的任务。

下面是这个项目的[源代码。](https://github.com/Wisdom132/deno/tree/master/cron)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)