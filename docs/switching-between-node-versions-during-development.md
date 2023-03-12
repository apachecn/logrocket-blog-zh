# 开发期间在节点版本之间切换

> 原文：<https://blog.logrocket.com/switching-between-node-versions-during-development/>

***编者按**:本文最后一次回顾和更新于 2023 年 1 月 31 日。关于使用 nvm 切换节点版本的更多信息，[查看这篇文章](https://blog.logrocket.com/how-switch-node-js-versions-nvm/)。*

有时似乎几乎每周都有新版本发布——每隔几周发布一个小版本，每隔几个月发布一个大版本。如果您是需要定期在不同应用程序和项目之间切换的开发人员，您可能会发现需要运行不同版本的 Node。

幸运的是，有几种方法可以安装多个版本并根据需要进行切换。本文将讨论和比较两种流行的节点版本管理器:用于 Windows 的 nvm 和用于 Linux/Mac 的`n`节点版本管理器。

> **注意**，Windows 和 Linux/Mac 存在不同的 nvm 实现；但是，`n` npm 包只在 Linux/Mac 上受支持。

为了便于比较，让我们假设您正在开发两个应用程序。应用 1 是运行在节点 14.19.0 上的 Angular 12 应用。应用 2 是运行在节点 12.11.1 上的 Angular 11 应用。以下是您需要完成的任务:

*   修复应用程序 1 的 bug x
*   将应用程序 2 升级到 Angular 13

您实际上需要三个版本的 Node 来完成您的任务，因为 Angular 13 升级要求您将应用程序 2 升级到 Node 14.15.0 或更高版本。我们将使用 16.18.1。

*向前跳跃*:

## 如何使用 nvm 更改节点版本

从技术上来说，有两个完全独立的 nvm 项目，它们在不同的操作系统上提供类似的功能，但彼此独立维护:

*   nvm-sh/nvm 是一个 Bash 脚本，可用于管理 Linux 和 Mac 上的节点版本
*   [coreybutler/nvm-windows](https://github.com/coreybutler/nvm-windows/tree/1.1.7) 是一个 windows 应用程序(有或没有安装程序),可用于管理 Windows 上的节点版本

本文重点介绍 nvm-windows。

### 装置

安装非常简单，只需从 GitHub 上下载最新版本的 nvm for Windows installer 即可。在撰写本文时， [1.1.10 是最新发布的](https://github.com/coreybutler/nvm-windows/releases)。下载并解压`nvm-setup.zip`，双击可执行文件进行安装。

安装程序会将`nvm`放在你机器上的一个适当的文件夹中，并更新你的系统环境变量，这样`nvm`和`node`的未来安装都可以在命令行上使用。

> 提示:如果你喜欢安装到你自己的文件夹中，下载`nvm-noinstall.zip`并解压到你喜欢的任何地方。运行附带的`install.cmd`来设置必要的系统环境变量。
> 
> 提示:GitHub 上的[提供了详细的安装说明。](https://github.com/coreybutler/nvm-windows)

安装完成后，打开命令窗口并确认`nvm`可用:

```
D:\>nvm version
1.1.10

```

### 如何切换到 Node 的特定版本？

nvm-windows 允许来回切换节点版本。到目前为止，我们已经安装了所需的版本。让我们看看如何使用 nvm-windows 在节点版本 18.21.1、16.18.1 和 14.19.0 之间切换:

```
D:\>nvm install 18.21.1
...
D:\>nvm install 16.18.1
...
D:\>nvm install 14.19.0
...
D:\>nvm list
    18.12.1
  * 16.18.1 (Currently using 64-bit executable)
    14.19.0
D:\>nvm use 18.12.1
Now using node v18.12.1 (64-bit)
D:\>nvm list
  * 18.12.1 (Currently using 64-bit executable)
    16.18.1
    14.19.0
D:\>nvm use 14.19.0
Now using node v14.19.0 (64-bit)
D:\>nvm list
    18.12.1
    16.18.1
  * 14.19.0 (Currently using 64-bit executable)

```

*   `nvm install`安装了所需的节点版本(默认为 64 位版本，因为当前系统的架构是 64 位)
*   `nvm use`允许我们选择要使用的已安装节点版本
*   `nvm list`列出所有已安装的节点版本，在活动版本旁边标有星号(*)

现在我们知道了如何切换到特定的节点版本，我们可以开始处理我们的示例应用程序了。

### 使用 nvm for Windows 运行应用 1

如果您还记得，您需要使用三个不同版本的 Node 运行两个不同的应用程序来完成所有任务。首先运行应用程序 1。一些命令输出被截断(`...`)以节省空间:

```
D:\>nvm list available
|   CURRENT    |     LTS      |  OLD STABLE  | OLD UNSTABLE |
|--------------|--------------|--------------|--------------|
|    19.5.0    |   18.13.0    |   0.12.18    |   0.11.16    |
|    19.4.0    |   18.12.1    |   0.12.17    |   0.11.15    |
...
D:\>nvm install 14.19.0
Downloading node.js version 14.19.0 (64-bit)...
Complete
Creating C:\Users\Brian\Downloads\nvm-noinstall\temp

Downloading npm version 6.14.16... Complete
Installing npm v6.14.16...

Installation complete. If you want to use this version, type
nvm use 14.19.0

D:\>nvm use 14.19.0
D:\>nvm current
v14.19.0
D:\>nvm list
  * 14.19.0 (Currently using 64-bit executable)    
D:\>node -v
v14.19.0
D:\>cd application1
D:\application1>npm install
...
D:\application1>npm start
> [email protected] start D:\application1
> ng serve

** Angular Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200/ **
...

```

以下是 nvm 的一些关键功能，您刚刚利用它们来运行应用程序:

*   `nvm list available`提供了可供安装的节点版本的方便的部分列表
*   `nvm current`显示当前正在使用的节点版本

安装并激活节点后，一切如常。您可以遵循应用程序要求的任何节点/npm 工作流。

> 提示:您的节点版本彼此完全隔离。例如，如果您在一个版本的 Node 上全局安装了一个包，那么该包在其他版本的 Node 上就不可用。

### 使用 nvm for Windows 运行应用 2

此时，您已经修复了应用程序 1 中的 bug x，现在您可以着手将应用程序 2 升级到 Angular 8:

```
D:\nvm install 12.11.1
...
D:>nvm use 12.11.1
Now using node v12.11.1 (64-bit)
D:>cd application2
D:\application2>npm install
...
D:\application2>npm start
...
D:\application2>nvm install 
...
D:\application2>nvm use 16.18.1
Now using node v16.18.1 (64-bit)
D:\application2>npm i -g @angular/[email protected]
...
D:\application2>ng update @angular/cli @angular/core
...
D:\application2>npm install
...
D:\application2>npm start
...

```

在 nvm(和 Angular CLI)的帮助下，您可以通过几个命令快速完成升级:

*   `nvm install`和`nvm use`安装并激活了 v12.11.1，因此您可以在升级之前验证应用程序是否按预期工作
*   `nvm install`和`nvm use`安装并激活了 v14.19.0，为升级做准备
*   全局安装了`@angular/cli`包，以访问自动升级 Angular 应用程序的`ng update`命令
*   `npm install`和`npm start`测试新升级的应用程序

## 如何使用`n`切换节点版本

`n`节点版本管理器为节点版本之间的安装和切换提供了一个更简单的 CLI。它仅在 Linux 或 Mac 操作系统上受支持。

> 提示:详细的安装和使用说明可以在 GitHub 上的 [tj/n 存储库中找到。](https://github.com/tj/n)

### 装置

如果您已经安装了 Node 和 npm 的版本，那么您可以像安装任何其他 npm 包一样使用`npm install -g n`来安装`n`。

如果您还没有安装 Node 或 npm 的版本，您可以使用 GitHub 的 Bash 脚本安装`n`。它看起来是这样的:

> 提示:您必须安装 Git 来安装带有 Bash 脚本的`n`。

```
~$ curl -L https://bit.ly/n-install | bash
...
=== n successfully installed.
  ...

  Run `n -h` for help.
  To update n later, run `n-update`.
  To uninstall, run `n-uninstall`.

  IMPORTANT: OPEN A NEW TERMINAL TAB/WINDOW or run `. /home/brian/.bashrc`
             before using n and Node.js.
===
~$ . /home/brian/.bashrc
~$ n
node/16.18.1

```

通过从 GitHub 下载并运行`n-install`脚本来安装`n`。安装完成后，运行`n`表明默认情况下安装了 Node 的一个版本。

### 使用`n`节点版本管理器运行应用程序 1

应用程序 1 需要节点 14.19.0，因此您需要首先安装该节点，然后运行应用程序:

```
~$ n 14.19.0
     install : node-v14.19.0
       mkdir : /home/brian/n/n/versions/node/14.19.0
       fetch : https://nodejs.org/dist/v14.19.0/node-v14.19.0-linux-x64.tar.gz
####################################################################################################################################### 100.0%
installed : v14.19.0
~$ node -v
v14.19.0
~$ cd application1
~/application1$ npm install
...
~/application1$ npm start
> [email protected] start ~/application1
> ng serve

** Angular Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200/ **
...

```

安装和激活 Node 版本的`n`命令很简单:`n 14.19.0`。您也可以使用`n latest`来获得最新版本的 Node，或者使用`n lts`来获得最新 LTS 版本的 Node。如果已经安装了 Node 的版本，那么`n`将简单地切换到那个版本。

安装 Node 后，应用程序可以照常运行。

> **N.B.** 与 nvm 类似，节点版本之间完全隔离。例如，全局安装的软件包不会在节点版本之间共享。

### 使用`n`运行应用程序 2

接下来，您需要运行应用 2，并继续进行 Angular 8 升级:

```
$ n 12.11.1
...
$ cd application2
~/application2$ npm install
...
~/application2$ npm start
...
~/application2$ n 16.18.1
...
~/application2$ npm i -g @angular/[email protected]
...
~/application2$ ng update @angular/cli @angular/core
...
~/application2$ npm install
...
~/application2$ npm start
...

```

安装节点 12.11.1 是为了确保应用程序 2 在升级前正常工作。然后，按照 Angular 13 的要求安装节点 16.18.1。Angular CLI 全局安装，应用程序用`ng update`更新。最后，应用程序在升级后开始测试。

您可能已经注意到，`n`使得用一个`n <version>`命令安装和切换到新版本的 Node 稍微快了一些。

### 直接使用节点二进制

`n`提供了直接调用特定节点二进制文件的能力，而无需显式切换到该节点版本。nvm 没有类似的功能:

```
~$ echo "console.log('Node version: ' + process.version)" > index.js
~$ node -v
v16.18.1
~$ n use 14.19.0 index.js
Node version: v14.19.0
~$ n use 12.4.0 index.js
  Error: '12.4.0' is not installed
~$ node -v
v16.18.1

```

在上面的例子中，节点的活动版本是 v16.18.1。当运行`n use 14.19.0 index.js`时，输出表明用于执行脚本的节点版本是 14.19.0。执行后，节点的活动版本仍然是 v16.18.1。请注意，`n use`命令要求请求的节点版本已经由`n`安装。

这种能力在某些情况下很有用。例如，设想一个构建服务器，用于构建不同的应用程序，这些应用程序都有自己所需的节点版本。每个构建都可以用`n use`命令触发，指定该应用程序所需的节点版本。

## 比较 nvm for Windows 和`n`节点版本管理器

nvm for Windows 和`n`有许多共同的特性和一些独特的特性，这些特性会影响您使用每种工具的方式和时间。以下是一些主要差异的总结:

您可以选择在您的 Linux 机器上使用`n`,因为它的 API 很简单。或者，您可能会在 Windows box 上选择 nvm for Windows，在 Linux 构建服务器上选择`n`来管理不同构建作业之间的节点版本。

无论情况如何，这两个工具都出色地满足了动态切换节点版本的需求。快乐节点版本切换！

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.