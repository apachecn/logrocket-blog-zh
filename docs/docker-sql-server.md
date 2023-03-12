# 如何在 Docker 容器中运行 SQL Server

> 原文：<https://blog.logrocket.com/docker-sql-server/>

***编者按**:本 Docker 和 SQL Server 教程最后一次更新是在 2021 年 5 月 28 日，包含了关于 SQL Server 2019 最新版本的信息。它可能仍然包含一些过时的信息。*

不久以前，在 Windows 之外的任何平台上运行--更不用说开发-- NET 应用程序或服务的想法都是可笑的。但随着微软目前对开源的关注，那些日子感觉像是遥远的记忆。

具备使用`dotnet core`开发的能力，跨平台的世界。网络已经开放。从 SQL Server 2017(当前版本为 SQL Server 2019)发布之日起，您就可以开发可以在 Windows、Mac 甚至 Linux 上运行的应用和服务。但是数据库技术呢？

在本指南中，我们将演示如何在 Docker 容器中启动 SQL Server 数据库。

我们将讨论以下内容:

## Docker 和 SQL Server 是什么？

由微软开发的 SQL Server 是世界上最流行的关系数据库技术之一。它的主要功能是存储和检索其他应用程序请求的数据。SQL Server 通常用于支持事务性和分析性工作负载的应用程序中。

Docker 使您能够使用可移植的、自给自足的轻量级容器来创建、管理和运行应用程序。[容器](https://blog.logrocket.com/containerizing-a-simple-django-application-with-docker-and-docker-compose/)使您能够打包您的应用程序及其所需的依赖项，以最小的破坏性更改从一个计算环境运行到另一个计算环境。

## SQL Server 可以在 Docker 上运行吗？

很多。专注于. NET 的开发人员倾向于使用 Microsoft SQL Server 来满足他们的关系数据库需求。但是，在很多人的记忆中，SQL Server 只能在 Windows 平台上运行。我们的跨平台。NET 开发刚刚嘎然而止。

为了解决这个问题，您可以通过利用 [Docker](https://blog.logrocket.com/docker-for-front-end-developers/) 跨多个平台使用 Microsoft SQL Server。

关于如何在 Docker 容器中运行 SQL server 的可视化示例，请查看我们下面的[视频教程](https://www.youtube.com/watch?v=RAE-VcZ3u2A):

 [https://www.youtube.com/embed/RAE-VcZ3u2A?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/RAE-VcZ3u2A?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

若要遵循本指南，请确保您使用的是 SQL Server 2017 或更高版本。否则，您将无法使用跨平台的 SQL Server。最新版本是 [SQL Server 2019 (15.x)](https://www.microsoft.com/en-us/sql-server/sql-server-2019) ，2019 年 11 月 4 日发布。使用这个完整的 [SQL Server 版本历史](https://sqlserverbuilds.blogspot.com/)来确保您的堆栈是最新的。

您必须安装包含`docker-compose`的`docker`。如果你使用[的 Mac 版 Docker 桌面](https://docs.docker.com/docker-for-mac/install/)，这一切通常都会为你处理好。

最后，我们将使用 npm 库 [mssql](https://www.npmjs.com/package/mssql) 来连接、更新和查询数据库容器。您可以从任何终端使用`npm install mssql`命令进行本地或全局安装。

都整理好了吗？很好，让我们来学习如何启动 SQL Server 数据库进行跨平台开发。

## 如何在 Docker 容器中运行 SQL Server

要启动 SQL Server 容器，首先在项目的根目录下创建一个`docker-compose.yml`文件。在该文件中，定义一个使用微软提供的 SQL Server 映像的`sql-server-db`资源。

下面是我们的`docker-compose.yml`文件的样子:

```
version: "3.2"
services:

  sql-server-db:
    container_name: sql-server-db
    image: microsoft/mssql-server-linux:2017-latest
    ports:
      - "1433:1433"
    environment:
      SA_PASSWORD: "change_this_password"
      ACCEPT_EULA: "Y"
```

要启动我们的数据库，我们可以从命令行运行一个`up`命令:

```
$ docker-compose up -d
Pulling sql-server-db (microsoft/mssql-server-linux:2017-latest)...
2017-latest: Pulling from microsoft/mssql-server-linux
59ab41dd721a: Pull complete
57da90bec92c: Pull complete
06fe57530625: Pull complete
5a6315cba1ff: Pull complete
739f58768b3f: Pull complete
0b751601bca3: Pull complete
bcf04a22644a: Pull complete
6b5009e4f470: Pull complete
a9dca2f6722a: Pull complete
Creating sql-server-db ... done
```

我们可以在终端中看到,`sql-server-db`已经成功创建。现在我们可以探索如何连接到它来运行一些查询。让我们从连接到数据库容器开始:

```
$ mssql -u sa -p change_this_password
```

我们现在应该看到我们已经连接到数据库，并且`mssql`正在等待命令。让我们继续运行`.databases`命令，看看我们的 SQL Server 容器中有哪些数据库:

```
mssql> .databases
name  
------
master
model 
msdb  
tempdb

4 row(s) returned

Executed in 1 ms
```

我们看到标准的 SQL Server 数据库出现了— `master`、`model`、`msdb`和`tempdb`。让我们继续创建我们自己的数据库，并在其中创建一个表。我们可以通过创建一个名为`my_db_setup.sql`的 SQL 脚本文件来做到这一点，我们可以在我们的容器中运行它。

```
USE master;
GO

CREATE DATABASE SampleDB;
GO

CREATE TABLE dbo.MyTable (
  id bigint IDENTITY(1,1) PRIMARY KEY,
  name varchar(500) null
)
GO
```

现在我们有了设置脚本，我们可以使用`mssql`对数据库容器运行它:

```
$ mssql -u sa -p change_this_password
mssql> .run my_db_setup.sql
USE master;
OK

Executed in 0 ms
CREATE DATABASE SampleDB;
OK

Executed in 0 ms
CREATE TABLE dbo.MyTable (
  id bigint IDENTITY(1,1) PRIMARY KEY,
  name varchar(500) null
)
OK

Executed in 0 ms
```

现在我们已经运行了我们的脚本，我们可以列出我们的数据库和表来查看刚刚创建的所有内容:

```
mssql> .databases
name    
--------
master  
model   
msdb    
SampleDB
tempdb  

5 row(s) returned

Executed in 1 ms
mssql> .tables
database  schema  name                   type      
--------  ------  ---------------------  ----------
master    dbo     MSreplication_options  BASE TABLE
master    dbo     MyTable                BASE TABLE
master    dbo     spt_fallback_db        BASE TABLE
master    dbo     spt_fallback_dev       BASE TABLE
master    dbo     spt_fallback_usg       BASE TABLE
master    dbo     spt_monitor            BASE TABLE
master    dbo     spt_values             VIEW      

7 row(s) returned

Executed in 1 ms
```

就这样，我们有了自己的数据库，并在其中配置了一个表。所有这些都作为 Docker 容器运行，我们可以与他人共享，并在各种不同的平台上运行。

## 结论

微软已经升级了越来越多的框架、工具和语言来支持跨平台开发。就而言是一个巨大的飞跃。NET/C#开发，因为它可以在 Windows、Mac 和 Linux 上运行。

酪 NET 仅仅是一个框架；我们还需要我们在该生态系统中经常使用的工具来支持多个平台。这就是我们在这里演示的:SQL Server 2017 和更高版本可以通过利用容器技术在任何平台上运行。

通过 Docker compose 文件中的几行代码和类似于`mssql`的简单 npm 库，我们可以在任何平台上启动 SQL Server 数据库。这不仅对于生产部署非常方便，对于开发环境也是如此。

团队中的其他开发人员现在可以通过运行`docker-compose up`和运行我们手头的任何种子数据库脚本来使用同一个数据库。我们甚至可以创建自己的配置了整个数据库的 Docker 映像，然后在 Docker 合成文件中使用该映像。

有了 Docker 和`dotnet core`这样的东西，使用。NET 生态系统比以往任何时候都更有可能。

如果你对这篇博文、AWS、无服务器或一般编码有任何问题，请随时通过 twitter [@kylegalbraith](https://twitter.com/kylegalbraith) 联系我。也可以看看我每周的[边做边学](https://kylegalbraith.com/learn-by-doing)时事通讯或者我的[通过使用 It 学习 AWS](https://kylegalbraith.com/learn-aws)课程来学习更多关于云、编码和 DevOps 的知识。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)