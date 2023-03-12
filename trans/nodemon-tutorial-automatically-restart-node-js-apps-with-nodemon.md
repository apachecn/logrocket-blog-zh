# Nodemon 教程:使用 Nodemon - LogRocket 博客自动重启 Node.js 应用程序

> 原文：<https://blog.logrocket.com/nodemon-tutorial-automatically-restart-node-js-apps-with-nodemon/>

## 介绍

在开发环境中，尤其是那些涉及大量迭代的环境，我们不希望在修改源代码时手动重启服务器或后端应用程序。

这是 [Nodemon](https://nodemon.io/) 解决的问题。它作为一个实用程序库来跟踪服务器的变化，并自动重启我们的应用程序。

根据文档，Nodemon 是开发基于 Node.js 的应用程序的完美工具。事实上，它是根据节点的 CLI 命令的属性构建的，因为它是对`node`命令的包装。

基本上，它的工作原理是检测源代码当前目录中的变化，并重启服务器以适应这些新的变化。

回想一下，为了运行 Node.js 应用程序，我们从`node`命令开始并附加文件名。在使用 Nodemon 的开发中，我们需要做的就是运行 Nodemon `filename`，Nodemon 会为我们监视我们的文件。

然而，要使用 Nodemon，我们需要在我们的机器上全局安装它，或者在本地基于每个项目安装它。

当在每个项目的基础上安装时，我们需要做的就是更新项目的`package.json`文件中的`script`标签。为了在开发模式下使用 Nodemon，我们可以在同一个文件的`script`标签中添加一行`"dev": "nodemon app.js"`。

当全局安装时，Nodemon 在我们的系统路径上可用，并且开箱即用。

在本教程中，我将通过探索 Nodemon 的特性以及如何使用它们来解释它是如何工作的。

我们将涵盖以下内容:

*   什么是 Nodemon？
*   在本地和全局安装 Nodemon
*   Nodemon 配置和使用
*   Nodemon 的功能

为了跟随本教程，读者应该对 Node.js 和 Express 有一个基本的了解。

## 什么是 Nodemon？

正如我前面提到的，Nodemon 是一个实用程序或助手工具，它监视文件更改并自动重启我们的 Node.js 应用程序。它是开源的，维护得很好，被社区中的很多人使用。

要查看可用 Nodemon 命令的列表，我们可以使用下面显示的`-h`标志或`--help`标志。所以我们可以运行:

```
nodemon -h 
 //or 
nodemon --help

```

在开发基于 Node.js 的应用程序时，我们应该使用 Nodemon 有几个原因。

首先，它很容易设置。其次，一旦安装，它会自动运行，因为它不需要调用任何实例，最后，它有助于开发环境的快速迭代。

现在，让我们在下面探索如何安装 Nodemon。

## 安装 Nodemon

正如我们前面提到的，Nodemon 既可以全局安装在我们的系统路径上，也可以作为开发依赖项本地安装。

要在我们的路径上全局安装 Nodemon，我们可以继续运行下面带有全局`-g`标志的命令:

```
npm install -g nodemon

```

这意味着可以在我们的开发机器上从系统路径使用和运行这个包。

否则，我们可以运行:

```
npm install --save-dev nodemon

```

以上命令将在我们的机器上本地安装 Nodemon，或者作为开发依赖项安装。请注意，我们将 Nodemon 安装为带有`--save-dev`标志的开发依赖项，因为我们不希望它成为生产依赖项。

> 当 Nodemon 被全局安装时，我们不需要在本地环境中做任何其他设置，因为我们可以从系统上的任何路径运行 Nodemon，它会为我们监视我们的源文件。

运行上述命令后，我们的`package.json`文件将被修改，Nodemon 库及其版本号将被保存在键`"devDependencies"`下，如下所示:

```
{
  "name": "test-app",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
}
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  }
{
  "author": "",
  "license": "ISC",
  "private": false,
  "devDependencies": {
    "nodemon": "^2.0.7"
  },
 }

```

作为一个开发依赖项，我们需要做的就是在我们的`package.json`文件中添加一个`dev`脚本，以便能够在我们的本地设置中使用 Nodemon。现在让我们通过显示一个简单的 Node.js 应用程序来理解 Nodemon 的所有特性和配置选项。

让我们从位于`app.js`文件中的服务器设置开始。该文件的内容如下所示:

```
require('dotenv').config()
require('./mongoClient')

const express = require('express')
const config = require('./config')
const routes = require('./routes')
const app = express()

// add routes here
routes(app)

// catch 404 and forward to error handler
app.use((req, res, next) => {
    const err = new Error('Not Found')
    console.log(err)
    err.status = 404
    res.send('Route not found')
    next(err)
})

app.listen(process.env.PORT || config.port, () => {
    console.log(`${config.appName} listening on port ${config.port}!`)
})

module.exports = app

```

让我们看看如何使用 Nodemon 运行这个服务器文件。在我们的项目中使用 Nodemon 的最基本和最简单的设置显示在下面位于根目录的`package.json`文件中:

```
{
  "name": "nodemon_tutorial",
  "version": "1.0.0",
  "description": "A Tutorial for Understanding Nodemon",
  "main": "app.js",
  "scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js"
  },
  "directories": {
    "model": "model",
    "controllers": "controllers"
  },
  "repository": {
    "type": "git",
    "url": ""
  },
  "keywords": [
    "Node.js",
    "JavaScript"
  ],
  "author": "Alexander Nnakwue",
  "license": "MIT",
  "devDependencies": {
    "nodemon": "^2.0.4"
  },
  "dependencies": {
    "bcrypt": "^5.0.1",
    "debug": "^4.1.1",
    "dotenv": "^8.2.0",
    "express": "^4.17.1",
    "express-async-handler": "^1.1.4",
    "mongoose": "^5.9.25",
    "mongoose-paginate": "^5.0.3",
    "path": "^0.12.7",
    "uuidv4": "^6.2.0"
  },
  "engines": {
    "node": "13.7.0",
    "npm": "6.13.6"
  }
}

```

正如我们在下面的脚本标签中看到的:

```
"scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js"
  },

```

所以当我们运行`npm run dev`时，它运行 Nodemon 并为我们监视我们的文件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们用 Nodemon 运行该文件时的内容如下所示:

```
[nodemon] clean exit - waiting for changes before restart
[nodemon] restarting due to changes...
[nodemon] starting `node app.js`
(node:97775) ExperimentalWarning: Conditional exports is an experimental feature. This feature could change at any time
Nodemon_tutorial listening on port 5000!
mongodb connected
Mongoose: users.createIndex({ email: 1 }, { unique: true, background: true })
Mongoose: users.createIndex({ phoneNumber: 1 }, { unique: true, background: true })

```

> 该脚本的任何输出都带有前缀`[nodemon]`，否则应用程序的所有输出(包括错误)都将按照预期被回显。

使用`package.json`文件中的`--inspect`标志(对于监听调试器的 Node.js 进程很有用)，我们运行`npm run dev`时的输出如下所示:

```
[email protected] backend-server % npm run dev

> [email protected] dev /Users/retina/Dropbox/My Mac (alexander)/Desktop/nodemon_tutorial/nodemon_tutorial
> nodemon --inspect app.js

[nodemon] 2.0.7
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): *.*
[nodemon] watching extensions: js,mjs,json
[nodemon] starting `node --inspect app.js`
Debugger listening on ws://127.0.0.1:9229/8618d563-210c-4070-bed5-b077016abae2
For help, see: https://nodejs.org/en/docs/inspector
(node:98970) ExperimentalWarning: Conditional exports is an experimental feature. This feature could change at any time
Nodemon_tutorial listening on port 5000!

```

从`1.1.x`版本开始，Nodemon 会在当前工作目录下的`package.json`文件中搜索`scripts.start`属性或者`main`属性，为我们启动 app。

让我们试试这个:

```
(aba) [email protected] backend-server % nodemon
[nodemon] 2.0.7
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): *.*
[nodemon] watching extensions: js,mjs,json
[nodemon] starting `node app.js`
(node:99661) ExperimentalWarning: Conditional exports is an experimental feature. This feature could change at any time
Nodemon_tutorial listening on port 5000!
mongodb connected
Mongoose: users.createIndex({ email: 1 }, { unique: true, background: true })
Mongoose: users.createIndex({ phoneNumber: 1 }, { unique: true, background: true }) 

```

从上面我们可以看到，我们的应用程序仍然像它应该的那样工作。注意，在这里，我已经从`package.json`文件中删除了`scripts`属性，并且只使用`nodemon`命令重新运行应用程序:

```
// delete the script tag and run nodemon afresh
"scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js"
  }

```

为此，我们需要在我们的系统路径中全局安装`nodemon`。当我们运行命令`npm i nodemon -g`来全局安装 Nodemon 时的输出如下所示:

```
[email protected] ~ % npm i nodemon -g
/usr/local/bin/nodemon -> /usr/local/lib/node_modules/nodemon/bin/nodemon.js

> [email protected] postinstall /usr/local/lib/node_modules/nodemon
> node bin/postinstall || exit 0

+ [email protected]
added 120 packages from 57 contributors in 13.404s

```

## Nodemon 配置和使用

Nodemon 既支持本地配置，也支持全局配置，就像我们可以全局配置系统或基于每个项目配置系统一样。

对于本地设置，我们可以在项目的当前工作目录中有一个`nodemon.json`文件，对于全局设置，我们可以在系统的主路径中有相同的文件。

此外，我们可以在项目`package.json`文件中为那些喜欢将所有包配置保存在一个地方的人设置一个`nodemonConfig`。

请注意，这些配置在执行哪些配置时会附带一个警告。如果我们指定一个`--config`文件或者提供一个本地`nodemon.json`文件，任何`package.json`配置都会被忽略。

您可能已经注意到，有三种方式可以配置 Nodemon 用于开发——本地、全局和通过节点 CLI。当我们键入`nodemon --help options`时，我们可以看到所有可供我们使用的 CLI 选项。

关于配置 Nodemon 的可用方法的更多信息，我们也可以在终端上键入`*nodemon --help config*`。

通常，控制 Nodemon 的选项是通过 CLI 传入的，并且可以在我们运行`nodemon --help option`时列出。

现在，让我们检查几个[配置中的一些，我们可以通过向配置(`--config`)文件或我们的`nodemon.json`文件传递更多参数来添加](https://github.com/remy/nodemon/blob/master/doc/sample-nodemon.md)。在开发我们的项目时，这些配置中的一些可能是有用的。下面我们来看看其中的一些。

### 监控多个目录

默认情况下，Nodemon 监视当前的工作目录，但是这可以通过使用`--watch`选项添加特定的路径来改变。

例如，我们可以通过运行下面的命令来指示 Nodemon 只监视我们的`services`目录或`nodemon_tutorial`目录中的`app.js`文件的变化:

```
nodemon --watch nodemon_tutorial/app.js --watch services 

```

> 总是为我们打算监视的每个目录添加一个`--watch`标志。当传递多个目录时，这通常是必需的。

### 忽略文件

由于 Nodemon 默认情况下会在文件发生更改时重新启动 web 服务器，因此我们可以通过命令行配置 Nodemon 来忽略某些特定文件、目录或文件模式中的更改，从而有效地更改这一默认设置。

请看下面的例子，我们忽略了`tests`文件夹:

```
nodemon --ignore tests/ 

```

上述命令在运行时，将自动忽略在`tests`文件夹中所做的任何更改。另外，请注意，默认情况下，Nodemon 会忽略`node_modules`和`.git`文件夹中的文件。很神奇吧？我们继续吧。

### 延迟重启

检查新文件更改的默认超时通常约为一秒钟。但是，在某些情况下，我们打算将 Nodemon 检查文件更改的时间延迟更长时间。

我们可以通过使用`--delay`命令来做到这一点。请参见下面的代码示例:

```
nodemon --delay 3000ms index.js  

```

`3000ms`是应用重启前的延迟值，单位为毫秒(也可以是秒)。因此，Nodemon 只会在这段时间过后重新启动 web 服务器。

### 要关注的扩展

当目录或子目录中有文件更改时，我们可以指定要监视的扩展名。

例如，我们可以用`-e`(或`--ext`)命令行参数指定我们自己的列表。请参见下面的代码示例:

```
nodemon -e njk

```

> 默认情况下，Nodemon 会查找扩展名为`.js`、`.mjs`、`.coffee`、`.litcoffee`和`.json`的文件。

### 运行非节点代码

除了节点程序，Nodemon 还可以用来监控其他程序。根据文件扩展名，Nodemon 将继续执行该命令。

关于运行非节点代码的更多细节，读者可以查看[文档](https://github.com/remy/nodemon#running-non-node-scripts)。

> 当 Nodemon 运行时，我们可以手动重启我们的应用程序。所以不用停止并重启 Nodemon，我们只需输入`rs`并按下**回车**，Nodemon 就会为我们重启服务器或正在运行的进程。

## Nodemon 的特性

### 将输出管道化到文件(或任何其他地方)

我们可以通过指定一组规则来告诉 Nodemon 不要写`stdout`。更多细节可以在文档中找到[。](https://github.com/remy/nodemon#pipe-output-to-somewhere-else)

### 添加默认可执行文件

在`nodemon.json`配置文件的帮助下，Nodemon 可以声明一个默认的可执行文件。这反过来将允许我们观察不特定于`node.js`的变化。

为此，我们可以利用`execMap`属性。注意，通常建议使用全局`nodemon.json`向我们的配置添加一个`execMap`选项。你可以在文档中阅读更多[细节。](https://github.com/remy/nodemon#default-executables)

### 状态改变时触发事件

我们可以在 Nodemon 重启时添加一个通知。此外，当[事件](https://github.com/remy/nodemon/wiki/Events#states)发生时，可以触发一个动作。关于这个的更多细节可以在文档中找到[。](https://github.com/remy/nodemon#triggering-events-when-nodemon-state-changes)

### 可用作模块

从 1.0.0 版本开始，Nodemon 可以作为 Node.js 中的一个必需模块工作。通过这样做，我们可以扩展它的功能，使它适合我们的其他需求。

更多细节可以在这里的文档中找到。

## 结论

在 Node.js 中，Nodemon 可以被比作魔棒，因为它能够在文件发生变化时自动重启 web 应用程序。换句话说，Nodemon 只是消除了程序员在每次更改后重复手动停止和重启开发中的应用程序源代码的需要。

它是一个实用工具，可以减少快速开发的麻烦。作为一个工具，它会持续监视 Node.js 应用程序，并在自动重启服务器之前安静地等待文件更改。为了添加更多的额外配置，我们可以使用一个`nodemon.json`文件。

该文件可用于向我们的应用程序添加一些额外的配置，以允许我们监控多个目录、忽略文件、延迟重启等等。关于 Nodemon 如何工作的更多细节和“陷阱”以及可能的问题解决方案，读者也可以参考 [FAQ](https://github.com/remy/nodemon/blob/master/faq.md) 。

FAQ 文档还包含一些我们可以参考的技巧和诀窍，包括本教程中没有提到或涉及的 Nodemon 的其他用例。

例如，在重启时清除控制台，在`.env`文件改变时重启 Nodemon，覆盖默认的忽略规则，等等。文档的链接可以在 [GitHub](https://github.com/remy/nodemon/tree/master/doc) 上找到。

再次感谢您的阅读。请在下面的评论区留下你的任何评论或问题，或者在 Twitter 上联系我。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.