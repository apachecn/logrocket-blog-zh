# 用 Docker 和 Docker Compose 容器化一个简单的 Django 应用程序

> 原文：<https://blog.logrocket.com/containerizing-a-simple-django-application-with-docker-and-docker-compose/>

如果你已经写了一段时间的代码，你可能已经听说过 Docker 或者更花哨的术语，比如容器。本文向您介绍了 Docker 容器化的概念，同时指导您容器化一个简单的 Django web 应用程序。学完本主题后，您应该熟悉以下内容:

*   虚拟化
*   集装箱化(使用码头工人)
*   码头工
*   写入 dockerfile
*   复合坞站
*   使用 Dockerfile 和 docker-compose 在 docker 环境中设置 Django 应用程序

## 要求

要遵循本教程，建议在您的计算机上安装以下软件:

*   Git / GitHub
*   Visual Studio 代码(或您选择的任何文本编辑器)
*   Django 的工作知识

## 了解虚拟化

传统上，如果您要将 web 应用程序部署到 DigitalOcean 或 Linode 等服务器托管服务提供商，您需要设置一个虚拟机或虚拟计算机，然后通过 git、FTP 或其他方式传输您编写的代码。这个过程称为虚拟化。

随着时间的推移，许多开发人员开始看到这一过程的缺点，因为它更加昂贵(考虑到为适应操作系统的变化而进行更改所花费的时间)。开发人员想要一种方法来统一开发和生产环境，这就是容器化思想的来源。

## 什么是容器，为什么超级酷？

简而言之，一个容器容纳了一个开发环境，也就是说，你的应用程序以及它运行所需的依赖项。

作为开发人员，容器使您能够将应用程序及其依赖项打包，并将它们从一个计算环境移动到另一个计算环境，而无需进行许多重大更改。

因为容器化相对来说更具可移植性、可伸缩性和高效性，像 Docker 这样的平台正日益成为开发应用程序的流行选择。

## 介绍 Docker

Docker 是一个开发的工具箱，使您能够使用容器创建、管理和运行应用程序。它允许开发人员轻松地将任何应用程序打包、运输和运行为一个可移植的、自给自足的轻量级容器，几乎可以在任何地方运行。

## 安装 Docker

要开始在您的计算机上设置 Docker，建议您遵循各自主机操作系统的官方文档。Windows 用户安装 [docker Desktop for windows](https://docs.docker.com/docker-for-windows/install/) 后就可以使用 Docker 了。Linux 和 OSX 用户可以分别安装 [Docker for Linux](https://docs.docker.com/engine/install/) 和 [Docker for Mac](https://docs.docker.com/get-docker/#docker-for-mac) 。

## 设置我们的应用程序

对于本教程，我已经建立了一个 starter 存储库，其中包含用 Django 编写的正在开发的轮询应用程序的源代码。随着我们在本演练中的深入，我们将设置一个`Dockerfile`来概述我们的应用程序将在其中运行的容器的指令，然后设置一个`docker-compose.yml`文件来简化我们的工作流。

在安装了 git 的计算机上，导航到您选择的文件夹，例如/desktop，并通过运行以下命令从 GitHub 存储库中克隆启动文件:

```
git clone https://github.com/theafolayan/django-docker-starter.git
```

一旦成功，导航到项目的根文件夹并通过运行以下命令在 VSCode 中打开:

```
cd django-docker-starter && code .
```

![The structure of the downloaded project should look similar to this when open in VSCode:](img/29fc50ac32b5da8d6d2c9586ce330bcc.png)

在这个目录中，创建一个名为`Dockerfile`的没有任何扩展名的区分大小写的文件。这个 Docker 文件是指定 Docker 容器配置的地方。它基本上是一个文件，在这里你为你的容器写一组每次启动时运行的指令，也就是每次你运行`docker build`命令时。

接下来，创建一个`requirements.txt`文件，其中列出了我们的应用程序所需的所有依赖项。稍后，这个文件将在 docker 文件中使用，以记录应该在我们的容器中安装哪些依赖项。

在`requirements.txt`文件中，添加 Django 版本 3.1.2 作为依赖项，如下例所示，并点击 save:

```
Django==3.1.2
```

## 什么是 Dockerfile？

编写 Docker 文件的想法对您来说可能有点复杂，但是请记住，这只是制作自定义 Docker 图像的书面方法。Dockerfile 文件自然包含以下内容:

1.  您想要在其上构建自己的图像的基础图像。把这个想象成另一个图像，作为你自己的容器的基础。这可能是一个操作系统、一种编程语言(在我们的例子中是 Python)或者一个框架
2.  要安装在 Docker 映像中的软件包和必需的实用程序
3.  要复制到 Docker 映像上的脚本和文件。这通常是您的应用程序的源代码

在阅读或编写 docker 文件时，请记住以下几点:

*   包含指令的行以它们各自的关键字开始，例如，RUN、FROM、COPY、WORKDIR 等。
*   包含注释的行以`#`符号开始。在执行 Dockerfile 指令之前，这些行被删除

## 写我们的文档

我们的 Django 应用程序将位于官方 python [Docker 图像](https://hub.docker.com/_/python)上。

在 Dockerfile 文件中，编写以下说明并点击 save:

```
#Tells Docker to use the official python 3 image from dockerhub as a base image
FROM python:3
# Sets an environmental variable that ensures output from python is sent straight to the terminal without buffering it first
ENV PYTHONUNBUFFERED 1
# Sets the container's working directory to /app
WORKDIR /app
# Copies all files from our local project into the container
ADD ./app
# runs the pip install command for all packages listed in the requirements.txt file
RUN pip install -r requirements.txt
```

## 了解 Docker 撰写

Docker Compose 是一个很棒的开发工具，可以让您定义和运行需要多个服务运行的应用程序。

Docker Compose 通常使用一个`docker-compose.yml`文件来配置您的应用程序将使用的服务，通过运行`docker compose up`，您能够从您的配置文件创建和启动这些服务。在大多数典型的 web 应用程序中，这些服务由一个 web 服务器(比如 Nginx)和一个数据库(比如 PostgreSQL)组成。在这个例子中，我们的应用程序使用了 SQLite 数据库，因此不需要设置像 Postgres 这样的外部数据库服务。

为了利用 Docker Compose 为我们提供的特性，在与 Dockerfile 相同的根文件夹中创建一个`docker-compose.yml`文件，并在其中添加以下代码:

```
version: '3.8'
services:
   web:
       build: .
       command: python manage.py runserver localhost:8000
       ports:
           - 8000:8000
```

下面逐行解释了`docker-compose.yml`文件的内容:

```
version: '3'
```

这告诉 Docker 应该使用哪个版本的`docker-compose`来运行我们的文件。在撰写本文时，可用的最新版本是“3.8”，一般来说，如果您将来阅读这篇文章，语法应该在接下来的几个版本中保持基本相同。

一旦建立了`docker-compose`文件，打开一个终端，运行`docker-compose up`命令来构建和服务您的应用程序。接下来，在您的浏览器上前往`localhost:8000`，查看您的容器化 Django 应用程序的运行情况。

![You should get a screen similar to this when you open up localhost:8000 in your browser](img/a7a8f82b9a14e5eb6af3a08cf6e90ede.png)

如果你想关闭容器，打开一个新的终端并运行`docker-compose down`。

## 结论

到目前为止，我们已经在本演练中介绍了很多，我们从熟悉虚拟化、容器化和其他 Docker 相关术语开始。接下来，我们学习了什么是 Dockerfile，以及如何创建 docker file 来封装 Django 应用程序。最后，我们通过一个`docker-compose.yml`文件设置`docker-compose`来配置我们的应用程序将使用的服务。

虽然在 Django 应用程序中使用 Docker 没有唯一正确的方法，但是为了确保您的应用程序尽可能安全，使用下面列出的官方指南是一个很好的实践。你可以在 GitHub 上查看包含完全容器化应用[的回购。
更多资源:](https://github.com/theafolayan/docker-django-starter/tree/final)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)