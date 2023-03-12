# Rust - LogRocket 博客的 6 个部署库

> 原文：<https://blog.logrocket.com/6-deployment-libraries-for-rust/>

Rust 越来越受欢迎，这在很大程度上要归功于核心团队对挑战 C/C++等现有系统现状的嗜好。大多数 Rust 程序员对生产率、优化、性能和可靠性的好处赞不绝口，这使得它成为现有语言的注重性能的应用程序和模块/包的首选。

因此，公司争先恐后地将他们的网络应用和系统迁移到 Rust 也就不足为奇了。部署可能是这个过程中的一个主要障碍。

在本指南中，我们将向您介绍目前可用于 Rust 的一些部署库和服务。我们将根据三个标准评估每个工具:

*   部署程序
*   定价
*   生产准备就绪

在我们开始之前，让我们快速回顾一些软件部署基础知识，以确保我们在同一页上。

## 什么是部署？

软件部署是指使软件系统可供最终用户使用所必需的活动、过程和步骤。“部署”一词也可能意味着将软件系统或应用程序移动或迁移到远程服务器。

软件部署可以指自动或手动流程的更新、补丁或新应用，包括但不限于安装、构建、测试、发布和性能监控。

在过去的几十年中，创新蓬勃发展，软件开发团队为软件交付构建新的系统、架构、原则和模式，以提高产品发布的速度和准确性。例如，如果代码/特性通过了部署阶段，那么今天一个特性可以被推送并立即在生产中可用。这一过程带来了各种部署选项，包括基础设施即服务(IaaS)、平台即服务(Paas)、虚拟化服务器等。

随着 Rust 越来越受欢迎，托管公司和服务已经将他们的资源用于支持 Rust for web。开发人员迫不及待地想把他们成熟的性能应用程序放到网上。

在我们继续之前，理解几个关键术语很重要。

*   [**【PaaS】**](https://en.wikipedia.org/wiki/Platform_as_a_service)—基于云计算的服务，提供一个平台来帮助开发者/用户处理应用部署的复杂方面，如基础设施(自动化脚本、运行、管理、监控等)。)
*   [**【IaaS】**](https://en.wikipedia.org/wiki/Infrastructure_as_a_service)—提供基础设施(防火墙、安全、扩展、位置、资源等)的即时在线计算平台。)来简化物理服务器或数据中心的拥有和管理。一些基础架构包括 Oracle VirtualBox、Oracle VM、VMware 等。
*   [**【VPS】**](https://en.wikipedia.org/wiki/Virtual_private_server)—相当于[专用物理服务器](https://en.wikipedia.org/wiki/Dedicated_hosting_service)，这是一个为用户提供操作系统实例副本的过程，用户可以完全访问该副本以安装几乎任何兼容的东西，但是底层物理硬件与其他用户共享

事不宜迟，让我们探索一下 Rust 社区提供了哪些部署工具。

## 1\. Heroku

[Heroku](https://www.heroku.com/) 是一款云 PaaS 工具，支持 PHP、Python、Go、Closure、Node.js、Scala 等语言集合。在过去的两年里，Heroku 发布了一个用于部署 Rust 的构建包，支持 cargo 和 Rustup，使得使用 [Rocket](https://rocket.rs/) 和 [Actix](https://actix.rs/) 轻松部署 Rust web 应用变得更加容易。

*   **价格**:免费开始
*   **生产就绪**:否

### 部署程序

Heroku 推荐安装 [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) 启动。

在 Heroku 上部署 Rust 的好处包括:

*   自动更新 Rust 到最新的稳定版本
*   易生锈型开关
*   支持导出以允许其他构建包访问 Rust 工具链
*   为用另一种语言(如 C/C++)编写的项目轻松编译 Rust 扩展

该构建包被称为 [emk/rust 或 EMK](https://github.com/emk/heroku-buildpack-rust) **，**，尽管它还不是官方的，因为 rust 在他们的主网站上不被支持。也就是说，您可以通过将构建包添加到您现有的项目中来进行测试。

## 2.码头工人

[Docker](https://www.docker.com/) 是一种 PaaS 服务，它使用虚拟化来交付资源，如作为容器的包中的软件、库和其他配置文件。

Docker 使您能够构建和发布任何软件，因为它帮助您使用`Dockerfile`跟踪软件包。官方图片在 Docker Hub 上的下载量超过 1000 万。

*   **定价**:相对价格(基于正在使用的 VPS)
*   **生产就绪**:是

### 部署程序

有几个为 Rust 打造的夜间[容器/图像](https://hub.docker.com/search?q=rust&type=image)，但是[官方 Docker 图像](https://hub.docker.com/_/rust/)由 Rust 团队管理。此外，使用 [Docker 的多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)可以帮助你避免工具链编译，这需要你将应用程序编译的二进制文件复制到容器中。

## 3.提供；给予

[Render](https://render.com/) 是一家基于云的 Paas 公司，提供强大的功能和可扩展性，并处理各种规模的应用程序。

*   **价格**:7 美元
*   **生产就绪**:是

### 部署

Render 完全支持 Rocket 和 Actix 框架。它也可以使用`Cargo`进行设置，因为它提供了简单的点击和最少的配置就可以轻松部署。

## 4.韦尔塞尔

Vercel ，原名 ZEIT Now，是一款部署工具，让开发者能够毫不费力地部署他们的应用。它在 Go、Node.js 等中提供自定义域、持续开发、高性能、无服务器的功能。

*   **价格**:新手免费；随增长付费计划
*   **生产就绪**:否

### 部署

now builder 允许 Rust 代码在 now 平台上作为 lambdas 运行。用于在 ZEIT 上部署 Rust 的基于社区的构建器现在仍在进行中。

## 5.NGINX

NGINX 是一个流行的 web 服务器，提供负载平衡、反向代理、邮件代理等功能。

### 部署

使用 NGINX 部署 Rust 就像代理通过 web 框架服务器并创建一个监视 Rust web 框架服务器状态的系统服务一样简单。

## 6.聪明云

[Clever Cloud](https://www.clever-cloud.com/en/rust-hosting) 是一家总部位于欧洲的 PaaS 公司，提供与 Heroku 密切相关的服务，帮助开发者快速部署和运行他们的应用。

*   **价格**:5 美元左右
*   **生产就绪**:是

### 部署

智能云使 Rust 应用的部署、构建和扩展变得简单。它提供了与 git 集成的平台服务。它还有助于减轻构建包的压力，因为它通过点击和选择其仪表板来处理应用程序构建。它支持`Cargo`和主要的 Rust web 框架。

前往[官方文档](https://www.clever-cloud.com/doc/rust/rust/)获取在智能云上部署 Rust 应用的分步指南。

## 结论

在本指南中，我们探讨了部署的基本概念，并描述了可用于在 web 上部署 Rust 应用程序的六个库和服务。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。