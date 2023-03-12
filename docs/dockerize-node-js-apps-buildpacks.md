# Dockerize Node.js 应用程序与 Buildpacks 

> 原文：<https://blog.logrocket.com/dockerize-node-js-apps-buildpacks/>

Docker 长期以来一直是创建易于分发和部署的工件的首选工具。

Docker 映像可以承载几乎任何语言编写的代码；每个主要的操作系统都支持执行 Docker 映像的能力，并且所有云提供商都至少有一个允许部署 Docker 映像的平台。

然而，从您的定制应用程序代码创建 Docker 映像需要一点专业知识，特别是如果您在对代码进行更改时定期重建映像的话。

每次构建映像时，很容易不必要地下载数千个包，浪费时间、消耗带宽和金钱。

云原生构建包是一种构建 Docker 映像的便捷方式，它利用了大型托管提供商在生成和托管 Docker 映像方面的十年经验。通过获取这些最佳实践，构建包确保您的 Docker 映像构建快速高效。

在本文中，我们将研究 Docker 映像如何托管一个简单的 Node.js 应用程序，回顾您可能遇到的一些常见陷阱，并探索 Buildpacks 如何允许您有效地创建 Docker 映像，通常不需要额外的配置。

## 示例 Node.js Express 应用程序

我们将在本文中构建的示例应用程序非常简单。运行 [Express 应用程序生成器](https://expressjs.com/en/starter/generator.html)的最终结果就是生成一个显示“欢迎使用 Express”的 web 页面

th [is](https://github.com/mcasperson/NodeJSExpressExample) [应用的](https://github.com/mcasperson/NodeJSExpressExample)[源代码可以在 GitHub](https://github.com/mcasperson/NodeJSExpressExample) 上找到。

虽然简单，但示例应用程序演示了如何构建托管 Node.js 应用程序的 Docker 映像，以及构建 Docker 映像的简单方法可能导致的一些低效问题。

## 教程先决条件

为了跟进这篇文章，您需要安装一些工具。

首先，你需要 Node.js， [Node.js 网站提供下载和说明](https://nodejs.org/en/download/)。最新的长期支持(LTS)版本是合适的。

接下来，你需要 [Docker，你可以在他们的网站上找到下载和说明](https://docs.docker.com/get-docker/)。虽然 [Windows 最近获得了原生容器支持](https://www.docker.com/products/windows-containers)，但这篇文章的重点是构建 Linux Docker 映像。

Windows 和 macOS 都通过一个无缝的虚拟机层支持 Linux Docker 映像，作为开发人员，您几乎不用考虑这个问题。

最后，要使用 Buildpacks，你必须安装`[pack](https://buildpacks.io/docs/tools/pack/)` [CLI 工具，它可以从 Buildpacks 网站](https://buildpacks.io/docs/tools/pack/)获得。

## 构建简单的 Docker 映像

构建 Docker 映像的传统方法是在名为 `[Dockerfile](https://blog.logrocket.com/reduce-docker-image-sizes-using-multi-stage-builds/#what-are-docker-images)`的文件中定义流程。我们可以在下面看到一个构建 Node.js 示例应用程序的示例:

```
FROM node
WORKDIR /usr/src/app
COPY . .
RUN npm install
EXPOSE 3000
CMD [ "npm", "start" ]

```

[完整的](https://docs.docker.com/engine/reference/builder/) `[Dockerfile](https://docs.docker.com/engine/reference/builder/)` [参考信息可在 Docker 网站](https://docs.docker.com/engine/reference/builder/)上获得。我们的示例只使用了可用命令的一小部分，但这足以对我们的示例应用程序进行分类。

`FROM`命令定义了在其上构建的基础映像。所有主流工具和编程语言都提供了受支持的 Docker 映像，开发者可以基于这些映像创建自己的映像，Node.js 也不例外。

可用 Node.js Docker 图像的完整列表可以在 DockerHub 上找到，这里使用默认的`node`图像:

```
FROM node

```

Docker 映像本质上是一个文件系统，包含运行支持应用程序的 Linux 进程所需的所有文件。在这个文件系统上，我们将创建一个名为`/usr/src/app`的目录来存放 Node.js 应用程序。

`WORKDIR`命令创建所提供的目录，并将其用作任何后续命令的工作目录:

```
WORKDIR /usr/src/app

```

然后，您可以使用`COPY`命令将 Node.js 应用程序源代码从本地 PC 复制到 Docker 映像中。

第一个参数是本地文件的位置。`.`表示本地文件在当前工作目录下。

第二个参数是 Docker 映像中文件复制到的位置。感谢上面的`WORKDIR`命令，作为第二个参数传递的`.`导致文件复制到`/usr/src/app`:

```
COPY . .

```

此时，您可以像在本地一样测试和构建应用程序。对于这个简单的例子，构建应用程序意味着用`npm`下载任何依赖项。您可以使用`RUN`命令在 Docker 映像的上下文中运行命令:

```
RUN npm install

```

`EXPOSE`命令打开一个端口，该端口从使用结果 Docker 映像创建的容器到 Docker 主机。示例应用程序默认端口为 3000:

```
EXPOSE 3000

```

最后，定义基于该映像的容器启动时要运行的命令。在`package.json`文件中定义的`npm start`命令作为启动节点 web 服务器的快捷方式，允许您使用`CMD`命令运行相同的命令:

```
CMD [ "npm", "start" ]

```

要构建 Docker 映像，从保存`Dockerfile`文件的目录中运行以下命令。这指示 Docker 使用当前目录中的`Dockerfile`文件来构建一个名为`expressapp`的映像:

```
docker build . -t expressapp

```

一旦构建了映像，就可以使用以下命令运行它:

```
docker run -p 3000:3000 expressapp

```

这将从名为`expressapp`的映像创建一个容器，并将本地 PC 上的端口 3000 暴露给容器内部的端口 3000。您现在可以在 [http://localhost:3000](http://localhost:3000) 打开示例应用程序。

现在，您已经成功地[对示例 Node.js 应用程序](https://blog.logrocket.com/node-js-docker-improve-dx/#dockerize-app-docker-multi-stage-build)进行了停靠。然而，上面显示的方法确实有一些明显的缺点。

## 探索 Docker 图像层

在幕后，Docker 图像由多个层组成。每一层都代表映像的一个增量变化，这些层组合起来生成由容器执行的结果文件系统。

`Dockerfile`中的每个命令都创建一个新层。为了提高构建 Docker 映像时的性能，如果`Dockerfile`中的指令不变，并且复制到映像中的外部文件也不变，这些层将被重用。

您可以通过使用以下命令重新构建映像来看到这一点:

```
docker build . -t expressapp

```

请注意，这一次映像的构建速度比第一次快得多。这是因为`Dockerfile`文件和复制到图像中的文件都没有改变，从而允许 Docker 重用先前生成的层。

现在让我们更改一个文件，强制 Docker 重新构建组成图像的层。首先，编辑文件`views\index.jade`，将欢迎消息改为`Welcome to Express from Docker`:

```
extends layout

block content
  h1= title
  p Welcome to #{title} from Docker

```

现在，使用以下命令重建映像:

```
docker build . -t expressapp

```

请注意，在构建映像时，节点依赖项会再次下载。这是因为命令`COPY . .`检测到复制的文件发生了变化，这意味着由该命令生成的前一层不能重复使用。

这也意味着任何后续层都不能被重用，命令`RUN npm install`被迫再次下载依赖项。

对于这样一个小应用程序来说，这是一个小小的不便，但是对于更大的 Node.js 应用程序来说,可能需要下载数百兆字节的依赖项，以应对应用程序源代码的每次更改。这不是特别有效，也不是可持续的长期办法。

这个问题的典型解决方法是只复制`package.json`文件，运行`npm install`，然后复制剩余的应用程序源代码。我们可以在下面看到一个例子:

```
FROM node
WORKDIR /usr/src/app
COPY package.json .
RUN npm install
COPY . .
EXPOSE 3000
CMD [ "npm", "start" ]

```

这是一个改进，因为对应用程序源代码的更改不需要再次下载依赖项。只要`package.json`文件没有改变，你可以重用由`COPY package.json .`和`RUN npm install`命令生成的层。

尽管如此，对`package.json`文件的任何更改都会导致所有依赖项重新下载。如果您可以在映像构建之间共享`node_modules`目录，就像直接从本地 PC 构建时它在磁盘上的保存方式一样，这不是很好吗？

这就是 Buildpacks 可以提供帮助的地方。

## 使用构建包生成映像

您可以将 Buildpacks 视为由 Google、Heroku 和 Cloud Foundry 等公司开发和维护多年的构建脚本，以尽可能最方便、最有效的方式将您的应用程序编译成 Docker 映像。

为了证明这一点，删除`Dockerfile`文件，因为你不再需要它了。Node.js 应用程序现在没有特殊的 Docker 配置文件。

现在，使用以下命令构建一个 Docker 映像:

```
pack build expressappbuildpack

```

如果您第一次运行`pack`命令，它会提示您选择一个默认的构建器:

```
Please select a default builder with:

pack config default-builder <builder-image>

Suggested builders:
Google: gcr.io/buildpacks/builder:v1 Ubuntu 18 base image with buildpacks for .NET, Go, Java, Node.js, and Python
Heroku: heroku/buildpacks:18 Base builder for Heroku-18 stack, based on ubuntu:18.04 base image
Heroku: heroku/buildpacks:20 Base builder for Heroku-20 stack, based on ubuntu:20.04 base image
Paketo Buildpacks: paketobuildpacks/builder:base Ubuntu bionic base image with buildpacks for Java, .NET Core, NodeJS, Go, Ruby, NGINX and Procfile
Paketo Buildpacks: paketobuildpacks/builder:full Ubuntu bionic base image with buildpacks for Java, .NET Core, NodeJS, Go, PHP, Ruby, Apache HTTPD, NGINX and Procfile
Paketo Buildpacks: paketobuildpacks/builder:tiny Tiny base image (bionic build image, distroless-like run image) with buildpacks for Java Native Image and Go

Tip: Learn more about a specific builder with:
pack builder inspect <builder-image>

```

很多高质量的构建者都是由 Google、Heroku、Paketo 这样的团队提供的。我倾向于使用 Heroku提供的[构建器，它是用下面的命令配置的:](https://blog.logrocket.com/free-services-deploy-node-js-app/#heroku)

```
pack config default-builder heroku/buildpacks:20

```

现在，再次运行构建命令:

```
pack build expressappbuildpack

```

看着`pack`工具检测到应用程序是针对 Node.js 编写的，安装所有的依赖项，并生成 Docker 映像。使用以下命令运行新的 Docker 映像:

```
docker run -p 3000:3000 expressappbuildpack

```

和以前一样，示例应用程序可以从 [http://localhost:3000](http://localhost:3000) 获得。

### 构建包如何有利于创建 Docker 映像

值得花点时间考虑一下你刚刚取得的成就。没有 Docker 配置或`Dockerfile`，也没有特殊的标志或设置来指示您有 Node.js 应用程序，`pack`命令成功地生成了嵌入 Node.js 应用程序的 Docker 映像。

此外，构建包在处理应用程序依赖性的方式上更加智能。为了演示这一点，在`package.json`文件的末尾添加一些空白(比如新的一行)并保存更改。

以前，对`package.json`文件的任何更改，甚至是添加空白等无关紧要的更改，都会导致 Docker 无法重用任何以前生成的层。这反过来导致所有依赖关系重新下载。

但是，如果您使用`pack`重新构建 Docker 映像，现有的依赖项将被重用。您将看到一条类似于`[INFO] Reusing node modules`的日志消息，表明先前下载的依赖项仍然可用。

这是因为 Buildpacks 巧妙地使用 Docker 卷来保存文件，比如构建之间的依赖关系，这比依赖层缓存要健壮得多。

因此，只需一个简单的命令，您就可以对 Node.js 应用程序进行 Dockerize，而不必担心破解`Dockerfile`来防止不必要的依赖项下载。

## 结论

Docker 是构建和分发应用程序的一个非常强大的工具，但是要充分利用它，开发者必须对`Dockerfile`中的命令如何与 Docker 层相关，以及在什么情况下层可以重用，什么情况下不可以重用有一个相当详细的了解。

Buildpacks 抽象出了构建 Docker 映像所需的大量知识，它具有高质量和久经考验的脚本，利用卷等高级功能来快速高效地构建映像。

构建包使得只需调用一次`pack`就可以构建 Docker 映像。

这篇文章演示了从定制的`Dockerfile`手动构建 Docker 映像，并探讨了应用程序依赖项会不必要地重新下载的情况。

然后，我们使用`pack`命令构建一个 Docker 映像，演示即使在通常重建层的情况下，依赖关系也是如何被重用的。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.