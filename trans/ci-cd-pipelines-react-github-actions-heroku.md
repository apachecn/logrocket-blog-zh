# 使用 React、GitHub 操作和 Heroku - LogRocket 博客的 CI/CD 管道

> 原文：<https://blog.logrocket.com/ci-cd-pipelines-react-github-actions-heroku/>

技术行业的创新速度很快，要求开发人员在快速交付产品和服务的同时，仍然优先考虑高质量。集成和交付曾经是一个手动过程，但自动化使它变得更快、更可靠。

在本文中，我们将探索持续集成和持续交付或部署。CI/CD 自动交付我们的产品和功能，使其更快，并帮助我们保持高质量。有几个工具可以用来设置 CI/CD 管道，但是，在本文中，我们将设置 GitHub 操作，使用 Heroku 作为我们的云托管服务，使用 GitHub 托管我们的存储库。

在本教程中，我们将使用以下工具:Git、GitHub、GitHub Actions、Heroku、React、 [Draw.io](https://app.diagrams.net/) 和 [Swimlanes.io](https://swimlanes.io/) 。你可以在我的 [GitHub 档案](https://github.com/zafar-saleem/github-actions-heroku)上访问这个项目的资源库。我们开始吧！

![CID Pipeline Github Action Heroku](img/32c24d10fae12c8f08ca7f3522280b8c.png)

CI/CD Pipeline using Github Actions and Heroku.

***CI/CD 管道使用 Github 动作和 Heroku***

目录

# CI/CD 基础

CI/CD 有三个重要的术语，我们需要在进入代码之前理解它们。

## 连续累计

持续集成是工程师和开发人员的自动化过程，允许我们测试代码中的新功能，确保它们按预期工作。

## 连续交货

接下来是连续交付，确保新的变更经过良好的测试，没有错误，并准备好部署到生产环境中。

## 持续部署

在持续部署阶段，对应用程序的更改通过合并到特定的分支(如`main`)来部署到生产环境中。

下面的示例图详细描述了 CI/CD 流程:

![CICD Process Flow Diagram](img/64988006fc73255fc14aecfd133c8a13.png)

CI/CD.

# Git 和 CI/CD 战略

让我们为我们的示例应用程序设置 CI/CD 策略。我们将有一个 GitHub 存储库，它有两个分支，`main`和`develop`。我们还将从`develop`分支创建一个新的`feature`分支，在那里我们将开发一个新的特性，并将这些变化推送到它自己的特性中。然后，我们将针对 GitHub 上的`develop`分支创建一个拉请求。

我们还将有两个 CI/CD `yml`文件用于配置，`development`和`production`。当我们成功地将一个拉请求合并到`develop`分支时，设置`development`文件来触发 CI/CD 管道，并将我们的更改部署到 Heroku 开发环境中。

当我们从`develop`分支提取最新的变更，将`develop`合并到`main`，并最终将这些变更推送到远程`main`分支时，`production.yml`文件被设置为触发 Heroku 上生产环境的 CI/CD 管道。

下图详细描述了这一策略:

![Github Actions CICD Diagram](img/475ced93cde89779b0e3bf24c59f550c.png)

Git and CI/CD Strategy.

## 创建新的 React 项目

首先，我们必须建立一个简单的 React 项目。运行下面的命令，在您的`projects`文件夹中创建一个 React 项目:

```
npx create-react-app github-actions-heroku

```

上面的命令将生成一个 React 项目并安装所有需要的依赖项。下面是我们将使用的一些依赖项及其在撰写本文时的当前版本:

```
react 17.0.2
react-dom 17.0.2
react-scripts 5.0.0
node 17.2.0

```

## 建立 Heroku

我们将建立两个环境，一个用于开发，另一个作为生产环境。继续在[Heroku.com](https://www.heroku.com/)登录您的 Heroku 账户。我在这篇博文中使用了我的免费帐户:

![Heroku Account Setup](img/d4260df4a5210857c3800d04cef9809d.png)

登录后，只需点击**新**按钮，如下所示:

![Heroku New Project Button](img/b5f6c4c0adc4edc8c836d6ce98a6d4b7.png)

然后，点击下拉菜单中的**创建新应用**。继续给你的应用命名。我在调用我的`github-actions-heroku-develop`，如下图。

因为我已经用这个名称创建了一个项目，所以它在我的帐户上显示为不可用。但是，您应该继续操作，点击**创建应用**，将会为您创建一个开发环境:

![Name New Project Heroku](img/953bbcbea60cc174144ebc578b1ec08e.png)

我们将把一个拉请求合并到`develop`分支，然后我们的应用程序将被部署到这个环境中。

现在，让我们重复相同的过程来创建一个新的应用程序，我们将其命名为`github-actions-heroku-prod`。当我们将`develop`分支合并到`main`中，并将这些变更推送到远程`main`分支时，这些变更将被部署到这个环境中。

设置好 Heroku 环境后，我们应该继续点击 Heroku 仪表盘上的**个人资料图片** **图标**，然后点击**账户** **设置**，如下所示:

![Heroku Account Settings](img/05517abab65ddb37e75764d8798bda79.png)

现在，向下滚动到 **API 密钥**部分以显示 API 密钥。将它复制并粘贴到某个安全的地方，因为我们在建立 GitHub 存储库时会用到它:

![Heroku API Key](img/31742b64674f87138aeb660de3032825.png)

## 无浏览器 Heroku CLI 授权

您还可以使用 Heroku CLI 直接从命令行生成新的授权密钥。为此，请确保您的本地机器上安装了 [heroku-cli](https://devcenter.heroku.com/articles/heroku-cli#install-the-heroku-cli) 。另外，如果您还没有这样做，请在 heroku-cli 上验证您的 Heroku 帐户。首先，登录，像这样:

```
heroku login -i

```

输入您的凭据并成功登录后，您可以使用以下命令生成新令牌:

```
heroku authorizations:create

```

这将立即建立一个新的 OAuth 通道和一个 OAuth 令牌，我们可以用它来代替我们之前从 Heroku 帐户中复制的令牌。

现在我们已经完成了 Heroku 的设置，让我们继续设置 GitHub repo。

## 设置 GitHub 存储库

继续登录您的 GitHub 帐户。点击右上角的 **+符号** **，然后点击**新增** **储存库**:**

![New GitHub Repository](img/d24fa53433ae65a58231537638eabfde.png)

按如下方式填写表格:

![Register New GitHub Repository Form](img/f2b5dbd158c71ce2249d0d8ee52278b0.png)

在上面的截图中，项目名称`github-actions-heroku`不可用，因为我已经为本文构建了一个同名的项目。因此，我将使用我已经创建的那个。但是，您应该继续创建项目。

一旦你在 GitHub 上创建了一个项目，你会看到如下所示的屏幕:

![New GitHub Project Output](img/8a049d19be5104f928f816e0997a5c68.png)

现在，您可以保留这个屏幕。我们一会儿将回到这个话题。回到您的本地环境终端，进入您之前创建的本地项目:

```
cd github-actions-heroku

```

首先，从项目中删除`.git`文件夹，如下所示:

```
rm -rf .git

```

然后，将这个 repo 初始化为一个 Git 项目:

```
git init

```

上面的命令会自动创建一个`main`分支。接下来，用下面的命令创建一个名为`develop`的新分支:

```
git checkout -b develop

```

现在，回到`main`分支:

```
git checkout main

```

使用以下命令将 GitHub repo 添加到本地 repo 中:

```
git remote add origin [email protected]:zafar-saleem/github-actions-heroku.git

```

下一阶段，提交您的更改，并将其推送到远程`main`分支:

```
git add -A
git commit -m "Initial commit"
git push origin main

```

现在，我们来看看`develop`分支:

```
git checkout develop

```

最后，我们将把更改推送到远程`develop`分支:

现在，让我们回到我们在`github-actions-heroku`上的项目，点击**设置** > **秘密** > **新存储库秘密**，如下所示:

![New Github Repository Secret](img/836b32aef1132dff1e10f272c95a5ff8.png)

一旦您点击**新存储库密码**，您将看到以下屏幕:

![GitHub New Repository Secret Screen](img/2b6209da191182229d665a54e18c1b46.png)

如上所示，相应地命名您的秘密，并粘贴您之前复制的 Heroku API 密钥。然后，点击**添加秘密**。现在，我们已经完成了 GitHub 和本地回购。

## 设置 GitHub 操作

现在，在你喜欢的编辑器中打开`github-actions-heroku`；我用的是崇高的文字。在项目的根目录下创建一个名为`.github`的文件夹。在`.github`中，创建一个名为`workflows`的新文件夹。

在`workflows`文件夹中，创建一个名为`development.yml`的文件，并将下面的代码粘贴到其中:

```
name: Development workflow

on:
  push:
    branches:
      - develop

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Development Code
        uses: actions/[email protected]
      - name: Use Node.js 17.x
        uses: actions/[email protected]
        with:
          node-version: 17.x
      - run: npm install
      - run: npm run build --if-present
      - name: Deploy to Heroku
        uses: akhileshns/[email protected]
        with:
          heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
          heroku_app_name: 'github-actions-heroku-develop'
          heroku_email: '[email protected]'

```

这个文件的顶部是一个开发工作流。每当我们向`develop`分支推送任何更改或合并一个拉取请求时，线路`6`上的管道将被自动触发。我们用的是`latest ubuntu`机器。

在第`25`行，我们使用的是来自 GitHub secrets 的`heroku_api_key`，这是我们之前设置的。第`26`行的 Heroku 应用程序名称应该与我们之前设置的 Heroku 环境相同。

现在，我们完成了`development.yml`。让我们在`workflows`文件夹中创建一个名为`production.yml`的新文件，并将以下内容粘贴到其中:

```
name: Production workflow

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/[email protected]
      - name: Use Node.js 17.x
        uses: actions/[email protected]
        with:
          node-version: 17.x
      - run: npm install
      - run: npm run build --if-present
      - name: Deploy to Heroku
        uses: akhileshns/[email protected]
        with:
          heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
          heroku_app_name: 'github-actions-heroku-prod'
          heroku_email: '[email protected]'

```

上面的代码是用于生产的，每当我们对主回购进行新的更改时就会被触发。

## 测试

为了测试我们的工作流是否如预期的那样工作，我们将首先确保我们在本地项目的`develop`分支上:

```
git checkout -b feature/branch // creates a feature branch from develop branch
// make changes to App.js file.
git commit -am "minor changes"
git push origin feature/branch

```

在 GitHub 上，针对`develop`分支创建一个 pull 请求。合并这个 pull 请求，它应该会触发 GitHub 项目的`Actions`选项卡中的开发管道。让它结束，然后按照下面的步骤:

```
git checkout develop
git pull origin develop
git checkout main
git merge develop
git push origin main

```

要查看被触发的`production`管道，请转到您的 GitHub 项目并点击**动作**选项卡。一旦两者都成功完成并部署，您就可以继续前进到 Heroku 并测试部署的最新变更。

## 通过 Dockerfile 进行备用部署

我们还能够通过 Docker 容器进行部署，这个过程非常简单。为此，我们需要在 docker 文件的末尾添加一个新的`CMD`命令。Heroku 将使用这个命令在容器中启动一个 web 服务器。然后，我们将把`usedocker`作为新的先决条件添加到 GitHub 工作流文件中。

有了这个新增加的内容，我们的`production.yml`文件看起来会像这样:

```
name: Production workflow
on:
  push:
    branches:
      - main
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/[email protected]
      - name: Use Node.js 17.x
        uses: actions/[email protected]
        with:
          node-version: 17.x
      - run: npm install
      - run: npm run build --if-present
      - name: Deploy to Heroku
        uses: akhileshns/[email protected]
        with:
          heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
          heroku_app_name: 'github-actions-heroku-prod'
          heroku_email: '[email protected]'
          usedocker: true

```

在部署之前，仔细检查一下`usedocker`指令是否已经附加到了`development.yml`文件中。

此外，如果您想向 Docker 构建指令传递额外的参数，您可以提供一个从环境中自动提取的`arg`名称列表:

```
name: Production workflow
on:
  push:
    branches:
      - main
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/[email protected]
      - name: Use Node.js 17.x
        uses: actions/[email protected]
        with:
          node-version: 17.x
      - run: npm install
      - run: npm run build --if-present
      - name: Deploy to Heroku
        uses: akhileshns/[email protected]
        with:
          heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
          heroku_app_name: 'github-actions-heroku-prod'
          heroku_email: '[email protected]'
          usedocker: true
          docker_build_args: |
            SECRET_KEY
            FOO
        env:
          SECRET_KEY: ${{ secrets.MY_SECRET_KEY }}
          FOO: ${{ secrets.BAR }}

```

一切都应该按预期运行！

## 结论

在本文中，我们用 React、GitHub actions 和 Heroku 介绍了 CI/CD。我们还回顾了使用 Dockerfile 文件的替代部署。通过自动化集成和交付，我们可以提高将应用程序部署到生产环境的速度和准确性。我希望你喜欢这篇文章。编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。