# Docker 卷与绑定装载- LogRocket 博客

> 原文：<https://blog.logrocket.com/docker-volumes-vs-bind-mounts/>

当 Docker 容器被销毁时，从现有的 Docker 映像创建一个新容器不会对原始容器进行任何更改。因此，每当您销毁一个容器并创建一个新容器时，您都会丢失数据。

为了避免丢失数据，Docker 提供了卷和绑定挂载，这是在 Docker 容器中持久化数据的两种机制。在本教程中，我们将检查卷和绑定装载，然后看一些例子和每个用例。

我们开始吧！

## 绑定安装

自从 Docker 早期用于数据持久化以来，绑定挂载就已经在 Docker 中可用。绑定挂载会将文件或目录从您的主机挂载到您的容器，然后您可以通过它的绝对路径引用它。

要使用绑定挂载，文件或目录不需要已经存在于您的 Docker 主机上。如果不存在，将按需创建。绑定挂载依赖于具有特定目录结构的主机文件系统。您必须显式创建文件或文件夹的路径来放置存储。

关于绑定挂载的另一个重要信息是它们允许访问敏感文件。根据 Docker 文档，您可以通过在容器中运行的进程来更改主机文件系统。这包括创建、修改和删除系统文件和目录，这可能会带来非常严重的安全隐患。它甚至会影响非 Docker 流程。

### 开始使用绑定装载

要在容器上使用绑定挂载，需要使用两个标志选项，`--mount`和`-v`。这两个选项最显著的区别是，`--mount`更加冗长和明确，而`-v`更像是`--mount`的简写。它将您传递给`--mount`的所有选项合并到一个字段中。

从表面上看，这两个命令都创建了一个 PostgreSQL 容器，并设置了一个卷来保存数据。然而，在某些情况下，使用`--mount`和`-v`的区别会明显不同。例如，当您使用[服务](https://docs.docker.com/engine/reference/commandline/service/)时，最好使用`--mount`，因为您需要指定比`-v`更多的选项。

通过运行以下命令，使用`--mount`标志指定绑定装载:

```
docker run --rm --name postgres-db -e POSTGRES_PASSWORD=password --mount type=bind,source="$pwd",target=/var/lib/postgresql/data -p 2000:5432 -d postgres

```

使用此代码通过`-v`标志指定它:

```
docker run --rm --name postgres-db -e POSTGRES_PASSWORD=password --v "$pwd":/var/lib/postgresql/data -p 2000:5432 -d postgres

```

注意，在这两种情况下，我们指定工作目录`$pwd`作为源。基本上，我们告诉 Docker 在我们当前所在的目录中创建绑定挂载。

## Docker 卷

[Volumes](https://docs.docker.com/storage/volumes/) 是在 Docker 容器中添加数据持久化层的一个很好的机制，特别是在关闭容器后需要持久化数据的情况下。

Docker 卷完全由 Docker 本身处理，因此独立于您的目录结构和主机的操作系统。当您使用一个卷时，会在主机上的 Docker 存储目录中创建一个新目录，并由 Docker 管理该目录的内容。

### 使用卷的好处

在 Docker volumes 中，存储与容器的生命周期无关，而是存在于容器之外。这有很多好处。首先，您可以随意多次终止容器，并保持数据的持久性。在多个容器中重用存储也很容易；例如，一个容器向存储器写入数据，而另一个容器从存储器读取数据。

由于卷不与任何容器相关联，因此您可以轻松地同时将它们附加到多个正在运行的容器。您还会发现，使用卷不会增加 Docker 容器的大小。最后，您可以使用 Docker CLI 来管理卷，例如，检索卷列表或删除未使用的卷。

### 开始使用卷

现在，让我们来看一个例子！

假设您想要创建一个 PostgreSQL 容器，并且您对持久化数据感兴趣。从`$HOME/docker/volumes/postgres`中一个名为`postgres`的文件夹开始。

像绑定挂载一样，我们可以添加以下代码来使用`--mount`标志指定该卷:

```
docker run --rm --name postgres-db -e POSTGRES_PASSWORD=password --mount type=volume,source=$HOME/docker/volumes/postgres,target=/var/lib/postgresql/data -p 2000:5432 -d postgres

```

或者，下面是使用简写标志`-v`的相同命令:

```
docker run --rm --name postgres-db -e POSTGRES_PASSWORD=password --v $HOME/docker/volumes/postgres:/var/lib/postgresql/data -p 2000:5432 -d postgres

```

如果你使用 Mac 或 Linux，你必须将数据存储在`$HOME/docker/volumes/`下，如果你使用 Windows，则必须存储在`C:\ProgramData\docker\volumes`下。否则，Docker 不会将您的数据作为一个卷来处理或管理。

## 用例

在决定何时使用卷或绑定装载时，有几个重要因素需要考虑。如果您希望您的存储或持久层完全由 Docker 管理，并且只能通过 Docker 容器和 Docker CLI 访问，您应该选择使用卷。

但是，如果您需要完全控制存储，并计划允许除 Docker 之外的其他进程访问或修改存储层，那么 bind mounts 就是合适的工具。

## 比较卷和绑定装载

根据 Docker 文档，使用卷是开始在 Docker 容器中持久化数据的最简单方法。总的来说，相比之下，绑定挂载更受限制。

根据我们目前所见，这一见解并不令人惊讶。一个很好的经验法则是，如果您不确定在 Docker 容器中持久存储数据的方法，请使用卷。

绑定装载的一个关键区别在于，绑定装载可以被 Docker 之外的进程访问和修改。如前所述，当您希望将 Docker 与其他进程集成时，这可能是一个好处，但如果您关注安全性，这也可能会令人头疼。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 结论

现在我们已经看到了卷和绑定挂载之间的核心区别，让我们回顾一下使卷成为 Docker 中持久化数据的推荐机制的一些优点。

首先，在容器之间共享卷更安全；它们只能在单个目录(`$HOME/docker/volumes`)中指定，完全由 Docker 自己管理。您还可以将卷存储在主机之外的远程主机或云提供商上。

您可以使用 Docker CLI 和 Docker API 来管理卷，并且可以从容器中预填充新卷的内容。此外，volumes 可以在 Linux 和 Windows 上工作，非常适合使用这两种操作系统的团队。

在研究了卷和绑定挂载之后，我们发现卷通常是持久化数据的更好选择。一定要在评论里让我知道你更喜欢哪种方法！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)