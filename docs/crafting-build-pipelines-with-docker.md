# 用 Docker - LogRocket 博客制作管道

> 原文：<https://blog.logrocket.com/crafting-build-pipelines-with-docker/>

大家都知道 [Docker](https://blog.logrocket.com/docker-for-front-end-developers/) 。它是无处不在的应用程序打包和分发工具，似乎从无到有，接管了我们的行业！如果你正在阅读这篇文章，这意味着你已经理解了 Docker 的基础知识，并且现在正在寻求创建一个更复杂的构建管道。

在过去，优化我们的 Docker 图像是一项具有挑战性的工作。在应用程序投入生产之前，各种各样的魔术被用来减小它们的大小。现在情况不同了，因为 Docker 增加了对多阶段构建的支持。

在本文中，我们将探讨如何为 Node.js 应用程序使用多阶段构建。例如，我们将使用一个 [TypeScript](https://blog.logrocket.com/7-bad-excuses-for-not-using-typescript-dbf5e603a9a8/) 构建过程，但是同样的事情也适用于任何构建管道。因此，即使您更喜欢使用 Babel，或者您可能需要构建一个 React 客户端，Docker 多阶段构建也可以为您工作。

这篇文章附带的代码是 GitHub 上的[，在这里可以找到一个多阶段类型脚本构建的 docker 文件示例。](https://github.com/ashleydavis/docker-nodejs-basic-example)

## Node.js 的基本单级 docker 文件

让我们从 Node.js 的一个基本 Docker 文件开始，我们可以将正常的 Docker 构建过程可视化，如下面的图 1 所示。

![Normal Docker Build Process](img/24a1999572e2567601212a677b3207ba.png)

Figure 1: Normal Docker build process.

我们使用`docker build`命令将 Docker 文件转换成 Docker 图像。然后，我们使用`docker run`命令将我们的图像实例化到 Docker 容器中。

下面清单 1 中的 Dockerfile 只是 Node.js 的一个标准的普通 Dockerfile。我们在这里所做的就是复制`package.json`，安装生产依赖项，复制源代码，最后启动应用程序。

这个 docker 文件是针对常规 JavaScript 应用程序的，所以我们还不需要构建过程。我只向您展示这个简单的 docker 文件，以便您可以将它与我即将向您展示的多阶段 docker 文件进行比较。

##### 清单 1:node . js 的普通 docker 文件

```
FROM node:10.15.2

WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install --only=production
COPY ./src ./src
EXPOSE 3000
CMD npm start
```

清单 1 是一个非常普通的 Docker 文件。事实上，在引入多阶段构建之前，所有的 Docker 文件看起来都很像这样。既然 Docker 支持多阶段构建，我们可以将简单的 Dockerfile 想象成图 2 所示的单阶段构建过程。

![Single-Stage Docker Build Pipeline](img/fec04469552fa90f8547c27712f891ad.png)

Figure 2: A single-stage build pipeline.

## 需要多个阶段

在构建我们的映像时，我们已经可以在 docker 文件中运行我们想要的任何命令，那么为什么我们还需要一个多阶段的构建呢？

为了找出原因，让我们升级我们的简单 Dockerfile 来包含一个 TypeScript 构建过程。清单 2 展示了升级后的 Dockerfile。我把更新的线加粗了，这样你就可以很容易地把它们挑出来。

##### 清单 2:我们已经升级了简单的 Dockerfile，以包含一个 TypeScript 构建过程

```
FROM node:10.15.2

WORKDIR /usr/src/app
COPY package*.json ./
COPY tsconfig.json ./
RUN npm install
COPY ./src ./src
RUN npm run build
EXPOSE 80
CMD npm start
```

我们可以很容易地直接看到这导致的问题。要亲自查看它，您应该从这个映像实例化一个容器，然后打开它并检查它的文件系统。

我这样做了，并使用 Linux tree 命令列出了容器中的所有目录和文件。您可以在图 3 中看到结果。

请注意，我们无意中在产品映像中包含了开发和构建过程的所有碎片。这包括我们的原始 TypeScript 源代码(我们在生产中不使用它)，TypeScript 编译器本身(我们也不在生产中使用它)，加上我们可能已经安装到 Node.js 项目中的任何其他开发依赖项。

![Docker Container Contents](img/367faabf0405f0c82a100f0c88ac055e.png)

FIgure 3: The debris from development and the build process is bloating our production Docker image.

请记住，这只是一个微不足道的项目，所以我们实际上并没有看到太多的 cruft 留在我们的生产形象。但是您可以想象这对一个有许多源文件、许多开发依赖项和生成临时文件的更复杂的构建过程的真实应用程序来说是多么糟糕！

我们不希望生产中出现这种额外的膨胀。额外的尺寸使我们的集装箱更大。当我们的容器比需要的大时，这意味着我们没有有效利用我们的资源。容器表面积的增加也可能是一个安全问题，在这种情况下，我们通常倾向于最小化应用程序可攻击的表面积。

如果我们可以扔掉我们不想要的文件，只保留我们想要的文件，这不是很好吗？这正是 Docker 多阶段构建可以为我们做的。

## 用多阶段构建制作 Dockerfile 文件

我们将把 Dockerfile 分成两个阶段。图 4 显示了拆分后我们的构建管道的样子。

![Multi-Stage Docker Build Pipeline](img/5a9f17beec7804689ee65684fb74421f.png)

Figure 4: A multi-stage Docker build pipeline to build TypeScript.

我们新的多阶段构建管道有两个阶段:*构建阶段 1* 是构建我们的类型脚本代码；*构建阶段 2* 创造了我们的生产码头工人形象。在这条流水线的末端产生的最终 Docker 图像只包含它需要的，而忽略了我们不想要的 cruft。

为了创建我们的两阶段构建管道，我们基本上只需在一个文件中创建两个 Docker 文件。清单 3 展示了添加了多个阶段的 docker 文件。第一个`FROM`命令启动第一阶段，第二个`FROM`命令启动第二阶段。

将它与常规的单级 docker 文件进行比较，您会发现它实际上看起来像是两个 docker 文件挤在了一起。

##### 清单 3:用于构建类型脚本代码的多阶段 docker 文件

```
# 
# Build stage 1.
# This state builds our TypeScript and produces an intermediate Docker image containing the compiled JavaScript code.
#
FROM node:10.15.2

WORKDIR /usr/src/app
COPY package*.json ./
COPY tsconfig.json ./
RUN npm install
COPY ./src ./src
RUN npm run build

#
# Build stage 2.
# This stage pulls the compiled JavaScript code from the stage 1 intermediate image.
# This stage builds the final Docker image that we'll use in production.
#
FROM node:10.15.2

WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install --only=production
COPY --from=0 /usr/src/app/build ./build
EXPOSE 80
CMD npm start
```

为了创建这个多阶段 docker 文件，我简单地将清单 2 分成几个独立的 docker 文件。第一阶段仅包含构建 TypeScript 代码所需的内容。第二阶段仅包含生成最终成品 Docker 映像所需的内容。然后，我将这两个 docker 文件合并成一个文件。

最需要注意的是第二阶段`--from`的使用。在清单 3 中，我用粗体显示了这一行，这样您就可以很容易地识别出来。这是我们用来从第一阶段提取构建文件的语法，我们在这里称之为*阶段 0* 。我们将编译好的 JavaScript 文件从第一阶段拖到第二阶段。

我们可以很容易地检查以确保我们得到了想要的结果。在创建了新的映像并实例化了一个容器之后，我开始检查文件系统的内容。您可以在图 5 中看到，我们已经成功地从生产映像中移除了碎片。

![Cleaned Docker Container Contents](img/ce33aa36d893cce6a2e0129877ca944c.png)

Figure 5: We have removed the debris of development from our Docker image.

现在，我们的映像中有更少的文件，更小，表面积更小。耶！任务完成。

但是，这具体意味着什么呢？

## 多阶段构建的效果

新的构建管道对我们的生产形象到底有什么影响？

我测量了前后的结果。清单 2 生成的单级图像重 955MB。在转换成清单 3 中的多阶段构建之后，映像现在达到 902MB。这是一个合理的缩减—我们从映像中删除了 53MB！

虽然 53MB 看起来很多，但实际上我们只削减了超过 5%的大小。我知道你现在要说什么:*但是 Ash，我们的形象仍然巨大无比！在这个图像中仍然有太多的膨胀。*

嗯，为了让我们的图像更小，我们现在需要使用`alpine`，或者精简的 Node.js 基础图像。我们可以通过将第二个构建阶段从`node:10.15.2`改为`node:10.15.2-alpine`来实现。

这将我们的生产映像缩减到 73MB，这是一个巨大的胜利！现在，我们从丢弃垃圾中得到的节约更像是高达 60%。好了，我们现在真的有进展了！

这突出了多阶段构建的另一个好处:我们可以为每个构建阶段使用单独的 Docker 基础映像。这意味着您可以通过使用不同的基础映像来自定义每个构建阶段。

假设您有一个阶段依赖于不同映像中的一些工具，或者您创建了一个为您的构建过程定制的特殊 Docker 映像。这给了我们在构建构建管道时很大的灵活性。

## 它是如何工作的？

您可能已经猜到了这一点:每个阶段或构建过程都会产生自己单独的 Docker 映像。您可以在图 6 中看到这是如何工作的。

由一个阶段产生的 Docker 图像可以被后面的阶段使用。一旦产生了最终图像，所有的中间图像都被丢弃；我们拿走我们想要的最终图像，其余的被扔掉。

![Each Stage In The Build Produces An Image](img/edc4b975028d3abec7c821497955695c.png)

Figure 6: Each stage of a multi-stage Docker build produces an image.

## 添加更多阶段

没有必要在两个阶段停下来，尽管这通常是所需要的；我们可以根据需要添加任意多个阶段。图 7 示出了一个具体的例子。

这里，我们在第一阶段构建类型脚本代码，在第二阶段构建 React 客户端。此外，还有第三个阶段，根据前两个阶段的结果生成最终图像。

![Creating More Complex Build Processes](img/e9f33be4fdaab4df6d8186b57b2c643c.png)

Figure 7: Using a Docker multi-stage build, we can create more complex build pipelines.

## 专业提示

现在是时候给你留下一些高级技巧供你自己探索了:

1.  您可以命名您的构建阶段！您不必将它们保留为默认的 0、1 等。命名您的构建阶段将使您的 docker 文件更具可读性。
2.  了解您对基础映像的选择。使用正确的基础映像可以在构建您的构建管道时消除很多困惑。
3.  如果构建过程的复杂性失控，构建一个定制的基础映像。
4.  可以从外部图片拉！就像您从早期阶段提取文件一样，您也可以从发布到 Docker 存储库的图像中提取文件。这给了你一个选择，如果它很贵并且不经常改变的话，你可以在早期构建阶段进行预烘焙。

## 结论和资源

Docker 多阶段构建使我们能够创建更复杂的构建管道，而不必求助于魔术。它们帮助我们精简我们的生产 Docker 图像并消除臃肿。它们还允许我们结构化和模块化我们的构建过程，这使得孤立地测试我们的构建过程的部分变得更加容易。

因此，请享受 Docker 多阶段构建的乐趣，不要忘记看看 GitHub 上的[示例代码。](https://github.com/ashleydavis/docker-nodejs-basic-example)

这里还有关于多阶段构建的 Docker 文档。

## 关于作者:

Ashley Davis 是一名经验丰富的软件开发人员和作家。他是类似于 T1 的 CTO，帮助企业使用机器学习来管理他们的数字资产。

Ash 也是 Data-Forge Notebook 的开发者，这是一个笔记本式的应用程序，用于用 JavaScript 和 TypeScript 进行原型开发、探索性编码和数据分析。

Ash 是《与 JavaScript 的数据角力》和《T2 引导微服务》的作者。要了解 Ash 的最新工作，请在 Twitter 上关注[他，并关注他的博客](https://twitter.com/ashleydavis75)[数据辩论者](http://www.the-data-wrangler.com/)。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.