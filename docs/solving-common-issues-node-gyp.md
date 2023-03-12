# 使用 node-gyp - LogRocket 博客解决常见问题

> 原文：<https://blog.logrocket.com/solving-common-issues-node-gyp/>

node-gyp 是 generate your projects 的缩写，是一个让我们为 Node.js 构建原生附加组件的工具。由于历史原因，我们将在本文中概述，使用 [node-gyp](https://github.com/nodejs/node-gyp) 很容易导致难以解决的问题。

在本文中，我们将 node-gyp 的所有主要问题分为三类，包括 node-gyp 的依赖性、附加组件编译错误和绑定契约违反。我们将逐一查看每个类别，并提供解决方案。

如果您还不熟悉 node-gyp 或者自己还没有遇到过这些问题，我建议您先查看一下[项目的自述文件](https://github.com/nodejs/node-gyp/blob/master/README.md)。我们开始吧！

## 节点-gyp 依赖关系

在我们开始之前，重要的是要注意，如果您已经安装了 Node.js，那么您可能已经安装了 node-gyp，但是没有全局公开。如果您只需要在项目设置期间编译附加组件，Node.js 应该会帮您完成。然而，如果你是一个插件开发者，你可能需要[全局安装 node-gyp](https://github.com/nodejs/node-gyp#installation)。

要使用 node-gyp，首先，我们需要安装一个 Python 运行时、`make`实用程序和一个 C 或 C++编译器。我们已经遇到了第一个需求的问题。node-gyp 希望 Python≥3.6，而不是 Python v2.x。如果您不是 Python 开发人员，您可能不会意识到 Python v.3 与它以前的主要版本不向后兼容。

此外，许多现代操作系统从第一次启动时就提供默认运行时，然而，这些运行时可能不符合 node-gyp 的要求。在使用 node-gyp 之前，确保正确地[为您的系统](https://github.com/nodejs/node-gyp)安装正确的依赖项。

最后，许多软件开发人员倾向于忽略 CPU 架构，经常假设 64 位版本的 x86 指令集可以工作。但是随着新的 CPU 不断出现在市场上，这与事实相去甚远。

## 加载项编译错误

通常情况下，开发人员用 C 写 Node.js 的附加组件，用 N-API 绑定，或者用 C++写 NAN 绑定。稍后我们将深入讨论绑定，但是现在，请记住至少有两种方法可以将附加代码连接到 Node.js 运行时。

通常，当您安装一个使用 node-gyp 的外部依赖项时，node-gyp 会下载与您的体系结构和系统上的 Node.js 版本相匹配的预构建二进制文件。node-gyp 只有在找不到匹配时才会编译附加代码，这可能会导致问题。

大多数编程语言都是向后兼容的，或者至少有一个次要的发布版本。想象一下，一个插件的作者编写了代码，但是使用了比您的系统所支持的版本更高的语言版本。您根本无法编译这个附加组件。

要解决编译错误，您可以派生其代码库，或者升级或降级您的编译器工具链。

## 违反约束性合同

之前，我们提到了两种类型的绑定，NAN，Node.js 的本机抽象，以及 Node-API，以前称为 N-API。第三类绑定包括 V8、libuv 和 Node.js 头文件。

V8 是 Node.js 使用的 JavaScript 和 WebAssembly 引擎，Libuv 是 Node.js 用来获得异步 I/O 支持的 C 库。

第一种绑定方法 NAN 提供了头文件，使插件能够直接与 V8 运行时交互。它类似于使用普通的 V8 头文件，只是需要额外的小心。因为每个 Node.js 版本可能使用不同的 V8 版本，所以使用 NAN 的插件间接依赖于其用户使用的 V8 版本。

如果附加代码期望从 V8 获得比它所提供的更多或不同的功能，则附加代码可能在链接阶段失败。或者更糟的是，链接可能会成功，只是由于存在不正确的头文件，插件会在运行时崩溃。

为了弥补这些缺陷，Node.js 现在公开了 [Node-API](https://nodejs.org/api/n-api.html#node-api) ，这是一个独立于 JavaScript 运行时的稳定 API。然而，这种方法也不是完美的。目标节点版本需要在附加组件使用的版本中实现 Node-API。要知道哪个版本的 Node-API 可以用于特定的 Node.js 安装，您应该[查看版本矩阵](https://nodejs.org/api/n-api.html#node-api-version-matrix)。

可悲的是，没有简单的方法来约束违反合同的行为。如果您是代码的作者，您可以调整它，否则，您可能需要升级或降级您的 Node.js 版本。在本文的后面，我们将介绍一些处理类似问题的更通用的方法。

## 问题分类

您可能已经意识到，作为 node-gyp 问题报告的问题实际上是用于构建附加代码的工具链的问题。现在，我们来看一下如何对问题进行分类，以应用正确的解决方案。当你遇到问题时，你可以问自己以下问题:

*   问题报告人是谁？是附加发行商还是附加消费者？
*   您机器的架构和工具链是什么？
*   目标 Node.js 版本有哪些？
*   插件使用 NAN 还是 Node-API？
*   该插件使用哪种编程语言？

简而言之，插件发布者在修复潜在问题时拥有的工具比消费者更多。架构和工具链影响整个构建过程。目标 Node.js 版本可能会损害 NAN 的协作方式，并且一些低版本不支持 Node-API。附加组件的编程语言会影响 Node 和 NAN/Node-API 绑定之间的接触点。

值得注意的是，安装一个使用 node-gyp 构建的附加组件可能会引发许多问题，每个问题都属于不同的类别。

检查架构和工具链需要了解机器的软件和硬件。您应该获得有关您的操作系统和 CPU/GPU 的信息。您可能在 x86-64 兼容的指令集上工作，但是最好验证这些信息。

要检查终端上当前使用的 Python 3 版本，请运行以下命令:

```
python3 --version

```

在您的终端中，运行下面的命令来检查您正在使用的 C/C++编译器工具链的版本:

```
gcc --version

```

要检查您计算机上的 Node.js 版本，请使用以下代码:

```
node --version

```

您可以使用 Node.js 版本管理器动态更改执行的 Node.js 版本，比如 Linux 和 macOS 上可用的 [n](https://github.com/tj/n) ，或者 [nvm](https://github.com/nvm-sh/nvm) 。然而，为了避免以自动化的方式使用每个工具，这可能会导致我们之前列出的一些问题，我们将检查附加绑定。

## 检查附加绑定

我们需要获得上述附加组件的代码库。我们需要查看的最重要的文件是`binding.gyp`文件，通常位于附加组件的根目录下。我们应该在其中搜索所有的`require`语句；`require("nan")`表示插件使用 NAN，`require("node-addon-api")`表示插件使用 Node-API。

为了进一步说明，你可以在 GitHub 上看到 [NAN](https://github.com/Blizzard/node-rdkafka/blob/master/binding.gyp) 和 [Node-API](https://github.com/mapbox/node-sqlite3/blob/master/binding.gyp) 中的`binding.gyp`文件的例子。

我们还可以在整个项目中搜索 NAN 或 Node-API 宏，`#include <nan.h>`和`#include <node_api.h>`。一些项目也可能使用 V8 头文件，它使用`#include <v8.h>`宏。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 检查编程语言

您应该通过文件扩展名来查看编程语言。c 使用`.h`作为头文件，使用`.c`作为源文件。另一方面，C++对头文件使用`.h`、`.hpp`和`.hxx`，对源文件使用`.cpp`和`.cxx`。

使用`node-addon-api`模块的附加组件是 C++项目。否则，获得 C++包装类的声明而不打算使用它们是没有意义的。

## 通用故障排除策略

此时，如果您已经实施了上述解决方案，但仍未解决您面临的问题，我们已经准备了一些通用的想法供您尝试。

在您的操作系统上设置 Node.js 环境和 node-gyp 依赖关系可能并不简单。作为替代，让我们尝试在一个 [Docker 镜像中而不是在您的本地机器](https://blog.logrocket.com/node-js-docker-improve-dx/)中构建附加组件，允许您控制所有的依赖项，包括操作系统。如果您可以构建 Docker 映像，但在本地设置上仍然有问题，那么您就知道问题出在哪里了。

例如，我们准备了以下 Dockerfile 片段，它在 Alpine Linux 实例上构建 SQLite3 附加组件:

```
FROM alpine:3.14.2
RUN apk update
RUN apk add --no-cache \
   build-base \
   curl \
   git \
   linux-headers \
   nodejs \
   npm \
   py3-pip \
   python3 \
   python3-dev \
   sqlite
RUN pip3 install --upgrade pip

WORKDIR /opt/showcase

RUN npm init -y
RUN apk add sqlite
RUN npm install sqlite3 --verbose --build-from-source

```

您可以将其保存为您选择的目录中的`Dockerfile`,并使用`docker build .`运行

我们使用 Alpine Linux 是因为它的轻量级特性。您可能已经注意到，我们必须手工添加依赖项。作为一个练习，您可以删除一些依赖项，并查看每个依赖项如何影响 SQLite3 的安装。此外，您可能会检查 node-rdkafka 的建议 Dockerfile，尽管它有些过时。

你也可以尝试改变 Docker 镜像中的架构，例如，你可以选择一个 32 位版本的 Ubuntu 。我们相信您可以对旧项目使用这个提示，但是请注意，您不太可能在 32 位主机上运行 64 位虚拟化操作系统。

使用 Docker 升级或降级 Node.js 版本并不重要。为了加快进程，Node.js Docker 团队[为不同版本](https://hub.docker.com/_/node/)提供了 Docker 镜像。

如果您可以访问基于 NAN 的附加组件的代码库，您可以尝试切换到 Node-API。它要求你有编写附加组件的经验。你也应该有足够的资源来承担这样的任务。

## 结论和资源

希望在本教程结束时，您已经能够解决 node-gyp 的问题。我们学习了如何通过询问一系列问题将问题分为三个常见类别，然后针对每个类别提供独特的解决方案。然后，我们提供了一个更通用的解决 node-gyp 问题的解决方案，它使用了 docker 文件。

在极少数情况下，上面建议的步骤对您不起作用，您可以浏览以下资源和支持渠道。我希望你喜欢这个教程，并对 node-gyp 有更深的理解。

资源:

支持:

200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

## 部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

.

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.