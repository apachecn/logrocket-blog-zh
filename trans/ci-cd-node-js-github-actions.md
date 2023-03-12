# 带有 GitHub 操作的 Node.js 中的 CI/CD-log rocket 博客

> 原文：<https://blog.logrocket.com/ci-cd-node-js-github-actions/>

持续集成/持续部署是一种软件工程实践，有助于团队更好地协作并改进他们的整体软件。使用 GitHub Actions，您可以轻松地将其集成到您的 GitHub 项目中，而无需使用外部平台。

在本教程中，我们将看到如何使用 GitHub 操作来建立到项目的 CI/CD 管道。

要使用本教程，您需要以下内容:

*   安装的节点
*   Node.js 和 Express 的基础知识
*   良好的 Git 知识
*   将使用 Jest 和 Heroku，但并不强制遵循

在我们深入研究针对 CI/CD 的 GitHub 动作之前，让我们了解一下什么是持续集成，什么是持续部署。

## 什么是持续集成？

持续集成(CI)是一种软件工程实践，它需要频繁地提交共享存储库。你可能已经习惯了这种做法，以至于你可能想知道为什么会有这样一个术语。

为了更好地理解这一点，让我们考虑 CI 的反面。在 CI 出现之前，人们会在功能分支上工作几周或几个月，然后试图将这个分支合并成一个主分支。想想在这样的合并过程中所有可能出错的地方——合并冲突和失败的测试，仅举几例。

持续集成试图通过鼓励小规模和频繁的代码更新来避免所有这些问题。当代码提交到存储库时，可以根据设置工作流来构建和测试它，以确保代码不会引入任何错误。

## 什么是持续部署？

持续部署意味着代码变更一旦被合并，就会自动部署/发布到测试或生产环境中。这经常与连续交付互换，因为它们非常相似。唯一的区别是，在连续交付中，需要人为干预(例如，点击按钮)来发布变更。然而，在连续部署中，一切都是自动发生的。在本文的其余部分，我们将 CD 称为持续部署。

让我们概述一下 CI/CD 的一些优点。

### CI/CD 的优势

除了上面已经提到的优点之外，还有更多优点:

*   故障隔离更简单、更快速。因为变更较小，所以在部署后隔离导致 bug 的变更更容易。这使得在必要时修复或回滚更改变得更加容易
*   因为 CI/CD 鼓励小的、频繁的变更，所以代码评审时间更短
*   CI/CD 管道的主要部分是项目关键流程的自动化测试。这使得防止可能破坏生产流程的变更变得更加容易
*   更好的代码质量得到了保证，因为您可以配置管道来测试林挺规则

现在，让我们考虑如何使用 GitHub 操作为 Node.js 项目配置 CI/CD 管道。在开始编写代码之前，让我们先简要了解一下 GitHub 的动作。

## 什么是 GitHub 动作？

根据 GitHub Actions 上的 [GitHub 文档](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions),“GitHub Actions 是一个持续集成和持续交付(CI/CD)平台，它允许您自动化您的构建、测试和部署管道。您可以创建工作流，为您的存储库构建和测试每个“拉”请求，或者将合并的“拉”请求部署到生产环境中。”

这意味着使用 GitHub Actions，您可以设置 CI/CD 管道，在对存储库执行某些操作时运行这些管道。您可以决定为每个创建或合并的 pull request (PR)运行测试，您可以自动部署合并的 PR，您甚至可以设置一个工作流，以便在创建 PR 时添加适当的标签。

那么它是如何工作的呢？我们将使用一个例子来解释如何为存储库设置它。

## 设置 GitHub 操作

1.  在 GitHub 上创建一个存储库，也可以使用现有的存储库。在存储库中，点击`Actions`选项卡。您将看到这个屏幕。我们已经建议了一个简单的工作流，它具有最少的必要结构，您可以选择自己建立一个工作流。

![Getting Started with GitHub Actions](img/90d1b8d3bf6aeb5e6f0b39b0847a1e93.png)

点击**配置**按钮，进入**简单工作流程。**您将看到此页面。让我们试着理解这里发生了什么。

![GitHub Actions Simple Workflow](img/7427bada4fc0c5169cd61c4c925436fd.png)

### 工作流程

记下创建文件的目录:`.github/workflows`。工作流是运行一个或多个作业的可配置自动化流程。您可以看到这里创建的工作流文件是一个 YAML 文件。工作流由您的`.github/workflows`
目录中的 YAML 文件定义，并由文件中定义的事件触发。

创建的文件包含以下代码。我们将用它来解释 GitHub 动作的其他组件，工作流是其中一个组件:

```
# This is a basic workflow to help you get started with Actions

name: CI

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/[email protected]

      # Runs a single command using the runners shell
      - name: Run a one-line script
        run: echo Hello, world!

      # Runs a set of commands using the runners shell
      - name: Run a multi-line script
        run: |
          echo Add other actions to build,
          echo test, and deploy your project.

```

### 事件

在创建的每个工作流中，您需要指定触发工作流的特定事件:

```
# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

```

样例工作流中的这个片段表明，每当向`main`分支发出 push 或 pull 请求时，工作流都会运行。工作流也可以被安排在特定时间运行，就像 cron 作业一样。你可以在这里读到它。

### 乔布斯

作业是工作流应该在同一个运行器上执行的一组步骤。这可能是一个 shell 脚本，也可能是一个操作。步骤在同一个运行器中按顺序执行，并且相互依赖。这很好，因为数据可以从一个步骤共享到另一个步骤。

作业并行运行，但是您也可以将一个作业配置为依赖于另一个作业。例如，当构建成功或者测试已经通过时，您可能想要部署一个合并的 PR。

Runners
这表示作业应该在哪个服务器上运行。它可以是 Ubuntu Linux、Microsoft Windows 或 macOS，或者您可以托管您自己的运行程序来运行作业。

在示例工作流中，我们希望作业在最新版本的 Ubuntu 上运行:

```
# The type of runner that the job will run on
    runs-on: ubuntu-latest

```

### 行动

一个动作执行一个复杂的、重复的任务。这是 GitHub Actions 平台的定制应用程序。动作对于减少建立工作流所需的代码量非常重要。您可以编写一个动作，或者使用 GitHub Marketplace 中已经存在的动作。

以下是示例工作流中使用的一个操作片段:

```
# Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
- uses: actions/[email protected]

```

对于我们的应用程序，我们需要使用 Node.js 动作来构建我们的节点应用程序，并使用 Heroku 动作来部署我们的应用程序。我们稍后将回到这一点。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

现在，将文件重命名为您选择的名称。我把我的改名为`main.yml`，以后参考。提交这个工作流(点击**开始提交**按钮)，然后将我们的库合并并克隆到我们的本地机器中。

要查看 GitHub 的操作，让我们在刚刚克隆的项目中创建一个非常简单的节点应用程序。如果您想将 GitHub 操作添加到现有项目中，您可以跳过这一部分。

## 设置项目

让我们安装我们需要的依赖项。我们将在应用程序中使用 Express，在测试应用程序时使用 Jest 和 SuperTest:

```
npm install express 
npm install jest supertest --save-dev

```

### 创建应用程序并添加测试

接下来，我们将`index.js`和`app.js`文件添加到一个`src`目录中。在您的终端中，运行以下命令:

```
mkdir src
cd src
touch index.js app.js app.test.js

```

打开创建的`app.js`文件，添加以下代码。

```
const express = require("express");
const app = express();

app.get("/test", (_req, res) =>  {
  res.status(200).send("Hello world")
})
module.exports = app;

```

在`index.js`文件中，添加以下代码:

```
const app =  require( "./app");
const port = process.env.PORT || 3000;

app.listen(port, () =>
  console.log('Example app listening on port 3000!'),
);

```

让我们为刚刚创建的端点添加一个测试。在`app.test.js`中，添加以下代码:

```
const app = require("./app")
const supertest = require("supertest")
const request = supertest(app)

describe("/test endpoint", () => {
    it("should return a response", async () => {
        const response = await request.get("/test")
        expect(response.status).toBe(200)
        expect(response.text).toBe("Hello world");
    })
})

```

在`package.json`文件中，将`start`和`test`脚本添加到脚本中:

```
"scripts": {
    "start": "node src",
    "test": "jest src/app.test.js"
}

```

运行`npm start`和`npm test`以确保一切按预期运行。

## 设置工作流

让我们回到从我们的存储库中取出的 GitHub 工作流:`main.yml`文件，或者任何您命名的文件。每当 pull 请求被合并到`main`分支时，我们将修改这个文件来构建应用程序并运行测试，并将这个应用程序部署到 Heroku。

因此，在该文件中，更改:

```
# Controls when the workflow will run
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

```

对此:

```
on:
  push:
    branches: [ main ]

```

因为我们正在构建一个节点应用程序，所以我们需要一个操作来为构建设置 Node.js。我们不需要从头开始构建，因为 GitHub Marketplace 中已经提供了这个动作。所以我们去 GitHub Marketplace 寻找一个我们可以使用的操作。

在 GitHub 上，点击顶部导航栏中的`Marketplace`。搜索 Node，你会在**动作**下看到一个**设置 Node.js 环境**动作。

![GitHub Setup Node.js Environment](img/0f259b3557222b061d0f3f17b73df561.png)

单击它可以查看该操作的描述以及如何使用它。您将会看到这个带有描述的屏幕。

![Setup Node.js Environment Description](img/d9bdc40d329053b7344d811646b4d3e9.png)

我们将用这里的步骤替换工作流中的步骤。

所以我们替换这段代码:

```
  # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/[email protected]

      # Runs a single command using the runners shell
      - name: Run a one-line script
        run: echo Hello, world!

      # Runs a set of commands using the runners shell
      - name: Run a multi-line script
        run: |
          echo Add other actions to build,
          echo test, and deploy your project.

```

有了这个:

```
steps:
  - uses: actions/[email protected]
  - uses: actions/[email protected]
    with:
      node-version: '14.x'
  - run: npm install
  - run: npm test

```

我们可以通过在步骤中添加名称来使其更容易理解:

```
steps:
    - uses: actions/[email protected]
    - name: Use Node.js
      uses: actions/[email protected]
      with: 
        node-version: "14.x"

    - name: Install dependencies
      run: npm install

    - name: Run test
      run: npm test

```

在这一点上，如果我们把它推到我们的主分支，我们将看到这个动作运行。但是因为我们想更进一步将自动部署添加到 Heroku 中，所以我们将在工作流中添加第二个作业。

## 部署到 Heroku

同样，我们不需要从头开始为这个部署构建动作。GitHub 市场拯救了这一天。因此，我们将回到市场，搜索**部署到 Heroku** 。您可以根据自己的需要决定使用自己选择的操作。如果你在 Docker 容器中运行你的应用，你可能想使用 Docker 的容器。

我们将使用 AkhileshNS 的第一个操作“Deploy to Heroku ”,因为我们正在部署一个简单的 Node.js 应用程序。让我们点击它，看看如何使用它。

![Deploy to Heroku](img/497b59731ab7624d9088e5ce16866d7a.png)

在**入门**部分，有关于如何使用该动作的详细信息。

![Getting Started with Heroku](img/f212384a1770864166fa534b0dd3dd92.png)

我们将在构建部分复制示例代码，将其添加到作业中，并修改它以满足我们的需求。因此，将这个添加到`main.yml`文件中:

```
 build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/[email protected]
      - uses: akhileshns/[email protected] # This is the action
        with:
          heroku_api_key: ${{secrets.HEROKU_API_KEY}}
          heroku_app_name: "YOUR APP's NAME" #Must be unique in Heroku
          heroku_email: "YOUR EMAIL"

```

由于我们已经有了一个构建作业，我们将把这个作业重命名为`deploy`。此外，我们需要这个作业仅在测试成功运行时运行，因此为了防止它与构建作业并行运行，我们将添加它依赖于构建。

上面的代码将被修改为:

```
 deploy:
    runs-on: ubuntu-latest
    needs: [build]
    steps:
      - uses: actions/[email protected]
      - uses: akhileshns/[email protected] 
        with:
          heroku_api_key: ${{secrets.HEROKU_API_KEY}}
          heroku_app_name: "YOUR APP's NAME" #Must be unique in Heroku
          heroku_email: "YOUR EMAIL"

```

现在请注意，为了运行这个作业，我们需要一个 Heroku 帐户。在那里你将获得`HEROKU_API_KEY`和一个 Heroku 应用程序名称。如果你没有账户，你可以在这里注册。注册后，或者如果您已经有一个帐户，您可以从您的帐户设置中获得您的`HEROKU_API_KEY`。点击导航右上角的图片，进入您的帐户设置。向下滚动到 API 密钥，复制您的 API 密钥。

为了让我们的工作流能够访问这个密钥，我们需要将它添加到我们的存储库的**秘密**中。所以在你的 Github repo 中，进入**设置>秘密**，点击**新秘密**。输入 **HEROKU_API_KEY** 作为名称，粘贴从 HEROKU 复制的 API key 作为值。

之后，为了确保我们的 Heroku 应用程序名称是唯一的，并防止部署失败，我们可以在 Heroku 上创建一个新的应用程序。在您的仪表盘上，点击**新建**并按照步骤创建应用程序。

![Create New Heroku App](img/7ade02168c622a87aa465f7d0e9e5337.png)

复制应用程序名称，并使用您创建的应用程序名称和 Heroku 电子邮件地址更新工作流程。

## 测试工作流程

我们现在准备测试我们的工作流程。为了确保一切就绪，下面是`main.yml`文件应该包含的内容。由于这是一个 YAML 文件，请确保其间距正确:

```
name: Main
on:
  push:
    branches: [ main ]
  workflow_dispatch:
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/[email protected]
      - name: Use Node.js
        uses: actions/[email protected]
        with: 
          node-version: "14.x"
      - name: Install dependencies
        run: npm install
      - name: Run test
        run: npm test

  deploy:
    runs-on: ubuntu-latest
    needs: [build]
    steps:
      - uses: actions/[email protected]
      - uses: akhileshns/[email protected] 
        with:
          heroku_api_key: ${{secrets.HEROKU_API_KEY}}
          heroku_app_name: "sarah-oo"
          heroku_email: "[email protected]"

```

让我们把这个提交到我们的主分支。

如果您转到**动作**，您将看到您的推送触发了一个工作流运行。

![GitHub Actions Add Workflow](img/4f747b37f5672599fca87ff07bf5bd98.png)

您可以点击工作流程，了解其进度详情。

![GitHub Actions Workflow Details](img/e8e0bebafcaf30f7b36cb7baf5a45c29.png)

从上图可以看出，构建是成功的，部署正在进行中。另请注意，部署作业仅在构建作业完成后运行。如果一切顺利，您将获得如下所示的成功部署。

![Successful Heroku Deployment](img/11f32a833cd4c7eac65fe8c37f25c106.png)

现在让我们来看看我们部署的应用程序。转到`<Name of your app>.herokuapp.com/test`你应该会看到“你好，世界！”在屏幕上。

干得好，能走到这一步。

## 结论

在本文中，我们讨论了什么是 CI/CD 及其优势。我们还讨论了 GitHub 动作，并使用一个简单的工作流来展示如何用它来设置 CI/CD 管道。您可以根据存储库的需要创建多个工作流。例如，如果您在一个有许多参与者的存储库上工作，您可以决定创建一个工作流，它在创建对主分支的拉请求时运行，而另一个工作流在合并拉请求时运行。

GitHub 动作的一个好处是，您不必从头开始构建工作流所需的所有动作。marketplace 已经有很多你可以使用或定制的动作来满足你的需求。您还可以构建特定于组织需求的自定义操作。所有这些使得 GitHub Actions 成为一个令人兴奋的工具，可以用来构建 CI/CD 管道。

感谢你的阅读，我真的希望这篇教程能成为你开始使用 [GitHub Actions](https://blog.logrocket.com/github-actions-how-to-autodeploy-your-app/) 的好指南。

进一步阅读，可以参考 GitHub Actions 上的[官方文档](https://docs.github.com/en/actions)。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.