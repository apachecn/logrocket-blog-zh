# fnm 入门，Rust-build node . js 版本管理器

> 原文：<https://blog.logrocket.com/fnm-rust-built-node-js-version-manager/>

在 web 开发行业，Node.js 是一个家喻户晓的名字。这是因为它为一切提供动力，从网飞的视频流到帮助宇航员在太空安全的 T2。

当在开发机器上安装 Node.js 时，通常使用以下步骤安装环境:

1.  首先导航到 [Node.js 网站](https://nodejs.org/en/)获取最新的 LTS 版本:
    ![Download the Node.js LTS version](img/5b81c7ccde1a593fb697abe40f3ddf61.png)
2.  不同操作系统的安装过程有所不同。例如，如果您在 Windows 或 macOS 上，运行可执行安装程序。或者，如果你在 Linux 上，使用 [NodeSource](https://github.com/nodesource/distributions/blob/master/README.md) 在你的机器上安装节点:
    ![Install Node.js for your OS](img/0786c2318c9bac9bce49d6d3fe7136b8.png)
3.  随着时间的推移，您还必须手动检查更新是否可用。如果是这种情况，您必须卸载 Node 的早期版本，并重新执行此过程。

你可能会对自己说，“当然，这个过程对我很有效。”但是，这个过程有一个小缺陷:太繁琐，太耗时。这是因为以下原因:

1.  Node.js 不会在有更新时通知用户，这意味着开发人员必须定期检查更新
2.  此外，每次更新可用时，您都必须重新安装节点，这浪费时间和精力
3.  最后，你不能在一台电脑上安装不同版本的 Node。例如，在某个项目依赖于节点 v 16 而另一个应用程序使用节点 v 18 的情况下，这是有问题的

这就是 [`fnm`](https://github.com/Schniz/fnm) 的用武之地。这是一个用 Rust 编写的软件，允许开发人员相对容易地在节点版本之间切换。

在本文中，您将了解到`fnm`项目。以下是我们将要介绍的内容:

## 什么是 fnm，为什么要使用它？

fnm，即快速节点管理器，是用 Rust 编写的节点版本管理器。因为它是一个版本管理器，所以它可以轻松安装不同的 Node.js 版本。您可以通过`install`命令安装节点:

![Install Node.js using fnm](img/95e7569fe480d24520434dbf77d6b8df.png)

除此之外，卸载是轻而易举的。例如，我们可以使用`uninstall`命令删除不同的节点版本:

```
fnm uninstall <version> #uninstall a version of Node.js
fnm uninstall 19.3.0 #use this version of Node.js

```

而且，这个项目是用 Rust 写的。这意味着`fnm`给牌桌带来了速度和稳定性。

## 安装 fnm

`fnm`团队捆绑了一个[安装脚本](https://github.com/Schniz/fnm/blob/master/.ci/install.sh)，使得下载软件变得轻而易举。要运行该脚本，请在终端中键入以下命令:

```
curl -fsSL https://fnm.vercel.app/install | bash

```

不想用终端？没问题！前往[发布版](https://github.com/Schniz/fnm/releases)页面并安装它们的二进制文件。

![Install fnm binaries if you don't want to use the terminal](img/c8bc08bbd3b16cb1387da27f7afc9a9e.png)

完成后，通过编写以下 bash 命令来验证一切正常:

```
fnm --help

```

这将返回以下结果:

![Check to see if the fnm installation was successful](img/edd943cb0fee3195183e7970adfa1d82.png)

这意味着我们的安装成功了！在下一节中，我们将学习如何使用`fnm`安装 Node.js。

## 使用 fnm 管理 Node.js 版本

要下载并安装特定版本的 Node.js，请使用以下语法:

```
fnm install <version>

```

例如，这个命令在开发机器上安装`14.15.0`:

```
fnm install 14.15.0

```

或者，如果您想使用最新版本，只需使用`--latest`标志，如下所示:

```
fnm install --latest

```

这将是结果:

![Install the latest Node.js version using fnm](img/2f551a0df7989a0316ba1f2a0cd8360e.png)

否则，要安装`lts`版本，只需传递`--lts`参数:

![Install the LTS Node version using fnm](img/8f13266e76b73a84d3ff881dd154f9a8.png)

### 列出所有 Node.js 版本
这个 bash 命令告诉`fnm`列出所有可供下载的节点版本:

```
fnm ls-remote

```

![A list of all Node.js versions](img/86b96e51ceb85430e4cff9494fe04c25.png)

如果您想查看系统上安装了什么版本，只需写下:

```
fnm list

```

![See all of the versions installed on your system](img/acf1216ab649417c9e9f2a81ee2fe8c8.png)

如你所见，在我的例子中，两个 [Node.js 版本(`18`和`19` )](https://blog.logrocket.com/exploring-competitive-features-node-js-v18-v19/) 被安装在本地机器上。

### 使用 Node.js 的特定版本

要使用 Node 的特定版本，我们必须运行`use`命令。它遵循以下语法:

```
fnm use <version>

```

例如，如果您希望您的计算机切换到节点`19.3.0`:

```
fnm use 19.3.0

```

![Switch your Node.js versions](img/fc99e02a70a97589ac2942b001964fe1.png)

我们可以用`current`命令来验证我们已经更改了我们的版本:

```
fnm current 

```

![Confirm the version switch](img/7ddf924ff61edc969218545a207afd62.png)

这表明我们的机器现在运行的是节点版本`19.3.0`。

### 卸载 Node.js 版本

要清除某些节点安装，请像这样使用`uninstall`关键字:

```
fnm uninstall <version>

```

例如，这将删除节点`19.3.0`:

```
fnm uninstall 19.3.0 #uninstall the latest version of node

```

## 设置 Node.js 版本别名

别名允许开发人员从语义上“命名”某些节点版本。这是一个很棒的特性，因为这意味着如果程序员正在处理许多项目，他们不需要记住多个节点版本。

要设置别名，请使用以下语法:

```
fnm alias <version> <name> 

```

举个例子，

```
fnm alias 18.12.1 my-project

```

上面的命令将`my-project`别名分配给节点版本`18.12.1`。

为了验证我们的别名是否配置成功，我们可以重新运行`fnm list`:

![A successful alias configuration](img/0420b05b8eba827217b058762581b8e7.png)

## 卸载 fnm

在某些情况下，开发人员可能[会遇到 bug](https://github.com/Schniz/fnm/issues)，比如开发期间的[命令问题](https://github.com/Schniz/fnm/issues/427)。因此，您可能需要重新安装`fnm`工具来解决这个问题。在本节中，您将学习如何从机器中删除`fnm`。

要清除 fnm，我们必须首先找到它的安装目录:

```
fnm env #get all environment variables

```

![Find the fnm installation directory](img/f25e619f24218c79bd04d22977389498.png)

这里，`FNM_DIR`变量表示`fnm`的位置。下一步，转到路径，简单地删除`fnm`文件夹，就像这样:

```
#in this case, fnm was in the 'share' folder
cd $HOME/.local/share
rm -rf fnm #removing this folder will uninstall this software

```

我们完事了。

## fnm 的替代品

### NVM

[NVM](https://github.com/nvm-sh/nvm) [(节点版本管理器)](https://github.com/nvm-sh/nvm)是`fnm`的替代品，允许开发者安装和管理他们的节点安装。一些示例命令包括:

```
nvm use 18.15.0 #use a certain Node version
nvm install --lts #install Node to local machine

```

与`fnm`相比，你可能会注意到[比](https://github.com/nvm-sh/nvm/issues/2724)慢得多。此外，该应用程序不支持 Windows 操作系统。作为一种变通方法，nvm 团队推荐使用 [nvm-windows](https://github.com/coreybutler/nvm-windows) ，但它并未得到官方支持。

### 沃尔特河

与 nvm 类似， [Volta](https://volta.sh/) 也是一款节点版本管理工具。就像`fnm`一样，它也是用铁锈建造的，从而给桌子带来了稳定性和速度。此外，它允许创建者“锁定”某些节点引擎。这意味着，如果有一个项目依赖于某个节点安装，Volta 将自动切换到该节点版本，以防止堆栈中的不稳定问题。

```
volta install node # install latest Node LTS
volta pin [email protected] # tell Volta that our project will use this node version

```

## 结论

自从推出以来，快速节点管理器已经成为在我的电脑上安装节点的默认方式。尽管存在其他替代工具，如 [nvm](https://github.com/nvm-sh/nvm) 和[Volta](https://docs.volta.sh/guide/),`fnm`一直是我的首选工具，因为它的速度和简单性。

非常感谢您的阅读！编码快乐！

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.