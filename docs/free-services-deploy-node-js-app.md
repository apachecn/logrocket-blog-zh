# 免费部署 Node.js 应用程序的 3 项服务

> 原文：<https://blog.logrocket.com/free-services-deploy-node-js-app/>

难道您不想将您的应用程序从您的本地开发环境中移出，放在一个可公开访问的 URL 中吗？尽管如此，找到一个好的(免费的)服务来部署 Node.js 应用程序并不容易。

在本教程中，我们将了解如何将使用 Express.js 和 MySQL 构建的 Node.js API 部署到三个不同的服务。我们将使用我为之前在 Node.js 和 MySQL 上的文章构建的编程语言 API 应用程序。

这篇文章中列出的所有服务都是完全免费的，最棒的是你不需要去掏你的信用卡——根本不需要。不过，只是提醒一下:当我们不支付任何费用时，会有一些权衡要做。

### 先决条件

如上所述，我们将使用编程语言 API，它与 MySQL 在 [freeDB.tech](https://freeDB.tech) 上连接。但是，在继续之前，请确保:

1.  您有一个 GitHub 帐户，并且知道 Git/GitHub 的基础知识，比如派生一个存储库
2.  您可以将第三方应用程序添加到您的 GitHub 帐户中，用于身份验证和其他目的

让我们开始吧！为了继续，请派生这个 [GitHub 库](https://github.com/geshan/programming-langugages-api),因为我们将用它作为例子。

## 1.Qovery

[Qovery](https://qovery.com) 宣称自己是“部署全栈应用的最简单方式”你需要将你的 Node.js 应用进行 Docker 化，以便轻松地部署到 Qovery，所以如果你的应用还没有进行 Docker 化，请继续按照本教程学习 [Node.js 和 Docker](https://blog.logrocket.com/node-js-docker-improve-dx/) 。

Qovery 用户界面直观且非常有用。我们将从在 Qovery 上注册开始。进入[登录/注册页面](https://console.qovery.com/login)，点击**用 GitHub** 按钮登录。在 Github 中授权 Qovery 后，您应该登录到 Qovery 控制台。登录后，点击左上角的**创建新项目**按钮:

![Quovery Create New Project](img/98367132d528dc2d5eb6339ab51cdc98.png)

因此，我们将看到以下屏幕:

![Qovery UI Create New Project Screen](img/ec24f1a726f3e6b20fb6820f5f3b4560.png)

选择**qeverycommunity**作为组织，在**项目名称**字段中输入*编程语言 api* 。点击**创建**，我们将看到下一个屏幕:

![Quovery Community Project Created Screen](img/0b3b58696d79c11a6c1bb8369b4f0cd1.png)

随后，点击**进入项目**。您应该会看到类似以下页面的内容:

![Qovery Programming Languages Api Go To Project Page](img/7b322689718bc1d574b931941ad9caa5.png)

在这个页面上，点击**创建一个应用**。

![Quovery Create Application](img/ac32270fdf8e121ac22c27b46de9cbea.png)

然后点击**我有个应用**。

![Qovery Connect Github Repo Programming Languages](img/9a79da3afe4ca80ceafd324e0d348004.png)

现在选择**连接到 GitHub** 并选择我们之前分叉的 repo 编程语言 api。

![Qovery Name Application](img/ddbef74c07a9a09be530cdd28cc6eaa2.png)

之后，检查应用程序的名称(如果你愿意，你可以更改它)并点击下一个的**。**

![Qovery Database Selection Screen](img/f51ddfbbf0d241eeefddb5277edbf197.png)

在数据库选择屏幕上，点击**下一步**而不选择任何数据库，因为我们的 MySQL 数据库已经托管和配置在不同的服务上。

![Quovery Programming Languages API Repository](img/09abc1733fd06312f8581c27a80c3044.png)

最后，点击 **Deploy** ，等待一段时间，让应用程序准备就绪。

![Quovery Programming Languages API Application Created](img/3c55f25ab616d450965b392a5b7ee757.png)

接下来，点击“转到项目”按钮。

![Qovery Go To Project Status Deploying](img/cbf35f4a39c04ba0536c73ca4cf135aa.png)

等待更多的时间来部署应用程序。您将看到状态变为正在运行，如下所示:

![Quovery App Deploying Status Running](img/2aa952aceda372fd6ac89a98a93fd0ac.png)

点击**打开**链接查看我们的应用程序运行情况:

![Open Link App Running Visual](img/3887bea9778011abe89f873ba1554201.png)

我们可以将`/programming-languages`添加到 URL 中，以查看 GET API 提供的编程语言列表，如下所示:

![Program Languages List Add Url Get Api](img/cbbed160ebb592236d2bd42f4d2e4002.png)

太好了！我们的应用程序正在 Qovery 上运行。除了易于部署之外，Qovery 还提供了其他出色的特性，比如每个分支的环境和 URL。要了解更多功能，请查看[qo every docs](https://docs.qovery.com/docs/getting-started/what-is-qovery/)。

## 2.韦尔塞尔

[Vercel](https://vercel.com) ，原名 Zeit，作为 AWS Lambda 之上的一层来运行你的应用。这是一个无服务器平台，可以运行各种各样的东西，更侧重于前端。幸运的是， [Node.js](https://vercel.com/docs/runtimes#official-runtimes/node-js) 是 Vercel 上的官方运行时之一。我们的常规 Node.js Express API 可以通过一个配置良好的`vercel.json`文件在 Vercel 上运行。让我们看看如何:

![Vercel Signup Page Display](img/904ffc3a77ba2f45c5297d3b650dc22b.png)

进入 [Vercel 注册页面](https://vercel.com/signup)，点击**继续 GitHub** 。认证后，点击**新建项目**:

![Vercel New Project Profile Page](img/982cc1aa0872f4dc274e0dba9badbc72.png)

您将看到下面的屏幕:

![Vercel New Project Homepage Clone Template Import Git Repository](img/f5cc4d699491e832f2326e846b8b3b72.png)

在你的 GitHub 库列表中，搜索*编程*，找到编程语言 api repo，然后点击**导入**。

![Github Programming Languages Api Repo Select](img/b540b9e8590112eed147c39f81e2c7fb.png)

因此，在你的个人账户上点击**选择**——注意只有个人账户在 Vercel 上是[免费的](https://vercel.com/pricing)。

![Vercel Import Programming Languages Api Project](img/d2765a40729d4eb931759a7201a21ce4.png)

点击后，**继续**确认根目录是基础文件夹，如上。

![Vercel Import Project Preset Other Deploy](img/db316dd594faebb95296714fe426f729.png)

在上图中，选择**其他**作为框架，点击**部署**。构建项目需要一些时间:

![Vercel Project Deployed Build Time](img/ae71d0842a1767ac3efc818c4f2c1654.png)

然后，您将看到祝贺页面，以访问已部署的项目:

![Vercel Congratulations Page Deployed ](img/c7abf5cdba902cd73a8f1c3aa762aab6.png)

当你点击**访问**时，你会看到如下内容:

![Vercel Visit Message Ok](img/e501ea5d4669ca7d8824f932f37bb1d7.png)

为了检查数据库连接，我们可以添加`/programming-languages`来查看以下输出:

![Database Connection Check Add Programming Languages](img/d9ecf714cf8a6ddd703890a5e2edec06.png)

万岁！我们的应用程序现在运行在 Vercel 上。请小心 repo 中的`[vercel.json](https://github.com/geshan/programming-langugages-api/blob/master/vercel.json)`文件 Vercel 需要该配置来运行我们项目的 Node.js 构建。

## 3\. Heroku

Heroku 是平台即服务市场的先驱。它最初是一个 Ruby 部署服务，现在已经扩展到支持 Node.js、Python 和 PHP 等语言。

让我们继续将我们的应用程序部署到 Heroku。首先，你需要[注册](https://signup.heroku.com/):

![Heroku Create Free Account](img/c02d73e9e92036ecd64ab9cda3bf5bb2.png)

然后登录 Heroku。

![Heroku Log In Personal Account](img/f913a3920801b9d53c4df6277708331c.png)

在仪表盘页面，点击**新建**，然后**新建 app** ，如上图。

![Heroku Create New App Name](img/8c00863c1e7c15409fed958533412feb.png)

之后，输入应用程序的名称，就像上面的例子中的`programming-languages-api`。你可能需要输入不同的和可用的内容。因此，点击“创建应用程序”按钮。

![Heroku Github Connect Programming Languages Repo](img/2295c425d87577115984a2c66472b402.png)

在上面的屏幕上，点击 **GitHub** 并选择你在第一步中分叉的编程语言 api repo 旁边的 **Connect** 。

![Heroku Connect Github Repository](img/41b346863edef2f59b0e964afe0e47f4.png)

之后，向下滚动并点击主分支的**部署分支**。

![Heroku Deploy Branch Master Branch](img/313c11d5189c82d1c45578b652e1d157.png)

我们将看到构建、部署日志和成功消息，如下所示:

![Heroku Build Deployment Logs Success Message](img/8d30d8d91e78eea20b8e0a42f3e79964.png)

点击**查看**查看如下输出:

![Heroku View Output Message Ok](img/503e1657d14bacf01fc8d5175aaf376d.png)

接下来，我们可以将`/programming-languages`添加到 URL，以查看应用程序是否能够连接到数据库并正确呈现行:

![Heroku View Output Message Okk](img/58f90bc860efb168cf3abe581436202a.png)

耶！我们的应用在 Heroku 上也运行良好。另一个技巧是为您的主/主要分支打开**启用自动部署**。这意味着对主分支的每个更改都将被自动部署。

您可以在**手动展开**部分上方找到**启用自动展开**按钮，如下图所示:

![Automatic Deploys Github](img/f82e237849013cd9380c4d8fb972d504.png)

Heroku free 计划的一个问题是， [dyno 在 30 分钟不活动后会休眠](https://devcenter.heroku.com/articles/free-dyno-hours#dyno-sleeping)。一个解决办法是使用 [Kaffeine](https://kaffeine.herokuapp.com/) 让你的应用保持清醒，即使在 30 分钟没有请求命中它之后。

## 要考虑的其他选项

这三项令人惊叹的服务让我们可以免费部署 Node.js 应用程序，也不需要输入我们的信用卡号码。当然，也有其他服务可以让你免费运行 Node.js 应用，但是你需要输入你的账单信息。

以下是我尝试过的一些选择:

*   [Google Cloud Run](https://cloud.google.com/run) :运行无服务器容器的 Google Cloud 平台的一部分。免费的每个月可以收到多达 200 万个请求
*   [Fly.io](https://fly.io/) :一种 CDN-as-backend 的概念。在用户附近部署应用服务器。你可以免费运行[三个小虚拟机](https://fly.io/docs/about/pricing/#free-tier)
*   [Openode.io](https://openode.io) :只对开源项目免费。但是最实惠的 T2 计划 T3 是每月 0.75 美元，50 MB 内存

如果你愿意放下你的信用卡，以上任何一个选项在紧急情况下都可以发挥作用。

## 结论

以上所有的免费服务都有一个命令行工具。如果您对 CLI 更熟悉，也可以尝试一下。

下一步是尝试将自定义域映射到您的应用程序(如果适用)。查看上述服务的部署日志和应用程序日志会很有帮助。

下次再见，部署愉快！

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.