# 使用 Dagger 和 Docker 以获得更好的 CI/CD

> 原文：<https://blog.logrocket.com/using-dagger-with-docker-for-better-ci-cd/>

在开发或本地环境中构建和管理应用程序可能是开发人员的日常工作。像任何事情，如果更直白一点不是很好吗？幸运的是，有一种工具叫做匕首！

在本文中，我们将展示如何使用 Dagger 和 Docker 来改进您的 [CI/CD](https://blog.logrocket.com/ci-cd-tools-compared-circleci-travis-ci-vs-major-csps/) 管道。

*向前跳转:*

## 为什么 CI/CD 很重要

CI/CD 管道已经成为我们软件开发生命周期的重要部分。虽然它们使得可靠和一致地发布软件变得容易得多，但是它们也增加了一定程度的复杂性，并且需要开发人员记住一点学习曲线。

大多数 CI/CD 工具都托管在云上，这意味着您必须将您的更改推送到远程存储库，并且通常要等待很长时间才能执行 CI 流程。

你可能会想为什么我们不能在本地运行它。嗯，大多数 CI/CD 工具都不能在本地工作，而能在本地工作的工具也很难安装。我们需要的是能够在我们的机器上本地运行我们的 CI/CD 管道，同时在我们选择的基于云的 CI/CD 工具中运行。

这就是匕首的用处。

## 匕首是什么？

Dagger 是一个用于 CI/CD 的开源开发工具包。它使用 Cue 工作，这是一种由 Google 开发的强大的配置语言，有助于验证和定义基于文本的动态配置。我们还将使用 BuildKit，在其中执行 Dagger 的配置。

此外，客户机-服务器架构与 Docker 的概念相同。同时，Dagger [守护进程](https://docs.dagger.io/1223/custom-buildkit/)引擎可以在任何容器运行时运行。

Dagger 旨在通过标准化我们编写 CI/CD 管道的方式来解决许多问题。在本教程中，我们将探索如何安装和设置 Dagger 并构建一个容器映像。

在我们继续之前，有必要熟悉以下技术:

*   Dagger:一个允许在本地开发、测试和执行管道的工具
*   CUE:一种帮助验证和定义基于文本的动态配置的语言
*   Docker:一个用于开发、发布和运行应用程序的开源平台
*   CI/CD:持续集成(CI)和持续交付(CD)是一种现代软件开发实践，在这种实践中，增量代码变更是一致且可靠的

> **注意**，Dagger 的配置在 [BuildKit](https://docs.docker.com/develop/develop-images/build_enhancements/) 中执行——这是 Docker 引擎最常用的功能之一

## **安装**

在开始之前，您需要安装 Docker 引擎，因为这是 Dagger 工作所必需的。如果你没有安装 Docker，你可以使用 Docker [安装](https://docs.docker.com/get-docker/)脚本来安装。

### 码头工人

运行以下命令开始:

```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh

```

一旦完成，您可以运行`docker version`来确保 Docker 安装正确。

### 匕首

下一步是安装 Dagger，这可以使用以下安装脚本来完成:

```
cd /usr/local
curl -L https://dl.dagger.io/dagger/install.sh | sh

```

如果您不是超级用户，您可能需要在最后一个命令前面加上`sudo`。

一旦完成，运行命令`dagger version`确认 Dagger 的安装。

> **注意**，如果你没有运行 Linux 或 macOS，或者你在安装过程中遇到任何问题，你可以查看官方安装指南[这里](https://docs.dagger.io/install/)。

## 匕首能做什么

在任何 CI 系统中，您都需要以某种格式(很可能是 YAML)定义步骤和操作，并在您的 CI 系统中运行它。

匕首可以:

1.  用你喜欢的编程语言自动操作
2.  在本地机器上立即测试和调试
3.  在任何 Docker 兼容的运行时上与现有管道集成
4.  重用大型且不断增长的目录中的操作
5.  使用提示语言将它们联系在一起

## 匕首动作和生命周期

正如在[官方文档](https://docs.dagger.io/1221/action/)中提到的，动作是 Dagger 的基本构建模块——它们将复杂的自动化抽象成可以重用的简单组件。

可以使用以下命令执行操作:

```
dagger do <action_name>

```

Dagger 的一个很酷的特点是不需要区分管道和步骤；一切都只是一个动作。

有两种类型的匕首动作:

*   核心动作，由 Dagger 引擎本身实现
*   复合动作，由其他动作组成

复合动作有四个生命周期阶段:

*   定义:描述动作的输入、输出、子动作及其连接的提示语法
*   集成:动作定义和 Dagger 计划之间的关系
*   发现:识别复合动作
*   执行:运行复合动作

![Lifecycle Of A Dagger Composite Action](img/c411c3e55e560bfecf675b98e7a265fd.png)

## 建立我们的匕首计划

一切从计划开始！关键字`dagger.#Plan`用于识别 Dagger 配置文件中的一个计划。

计划是行动的执行上下文。它规定:

*   向最终用户呈现什么操作
*   这些任务之间的相关性
*   任务和客户端系统之间的交互

现在，让我们继续使用 Dagger 来构建容器图像。

## 用匕首塑造码头工人形象

Dagger 可以执行常规的 Docker 文件来构建容器映像。

首先，您需要创建一个包含以下内容的`main.py`文件(这将是一个 Python 应用程序):

```
# filename: main.py

from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello_world():
    return "<p>Hello, Dagger!</p>"

```

下一步是创建 Dagger 配置文件；`dagger.cue`。

```
// filename: dagger.cue
package main

import (
    "dagger.io/dagger"
    "universe.dagger.io/docker"
)

#PythonBuild: docker.#Dockerfile & {
    dockerfile: contents: """
        FROM python:3.7-stretch
        RUN apt-get update -y
        RUN apt-get install -y python-pip python-dev build-essential
        COPY . /app
        WORKDIR /app
        RUN pip install flask
        ENTRYPOINT ["python"]
        CMD ["main.py"]
        """
}

dagger.#Plan & {
    client: filesystem: "./": read: contents: dagger.#FS
    actions: build: #PythonBuild & {
        source: client.filesystem."./".read.contents
    }
}

```

从 Dagger 配置中可以看到，该操作从我们之前创建的 Python 应用程序构建了一个 Docker 映像。

此外，构建步骤是使用内联方法定义的，这允许我们在 Dagger 的配置中编写 Dockerfile 内容。

这是初始化和更新项目所必需的，因为这会安装这些必需的 Dagger 包。您可以使用以下命令来完成此操作:

```
dagger project init
dagger project update

```

现在，我们可以使用 Docker 继续执行构建计划。若要触发生成操作，请运行以下命令:

```
dagger do build 

```

您还可以通过检查 Docker 映像列表来验证映像是否存在，从而确认 Dagger 已经使用 Docker 文件内容成功构建了容器映像，如下所示:

![Dockerfile Contents ](img/e5d53b0a5831adc1cb85a06d53debb0d.png)

现在，您知道如何用 Dagger 构建容器图像了！

## 在 CI/CD 中使用 Dagger 和 Docker 的好处

Dagger 正在努力取代复杂的 Bash 脚本，许多现有的 CI/CD 系统需要这些脚本来代替更模块化、更快速和更便携的组件。

您可以创建可重用的模块，这些模块适应您的需求并依赖于您用来生成它们的工具，其他人可以使用您的模块作为起点来创建适合他们独特用例的新模块。

作为一个在咨询公司工作的人，我们有许多用例，在这些用例中，我们必须频繁地在使用不同 CI/CD 堆栈的项目之间切换。因此，在当前 CI 运行程序的基础上构建一个抽象层的承诺对我来说很有吸引力——独立于 CI 运行程序，我们可以建立自己的流程，并将它们应用到我们的项目中。

## 结论

结束我们今天的文章，有几个用例来说明为什么您可能希望将 Dagger 与 Docker 一起用于您的 CI/CD 管道。

如果您经常编写复杂的构建或部署脚本，在团队之间共享大部分脚本，花费大量时间尝试调试 CI 中的问题，或者更喜欢本地构建是一个简单的脚本，那么 Dagger 很可能是您的工具。

同样，如果您的 CI 管道在可以并行化的情况下花费太长时间来串行执行任务，那么它可能也适合您。最后，我们今天工作的项目展示了使用 Dagger 创建容器图像是多么简单！

以下是一些对您的项目有用的附加资源:

在下面的评论区让我知道你使用 Dagger 和 Docker 的经历吧！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)