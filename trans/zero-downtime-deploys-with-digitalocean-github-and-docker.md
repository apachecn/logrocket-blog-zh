# 使用 DigitalOcean、GitHub 和 Docker - LogRocket 博客实现零停机部署

> 原文：<https://blog.logrocket.com/zero-downtime-deploys-with-digitalocean-github-and-docker/>

## 介绍

DigitalOcean 是一个为开发者提供托管应用的平台。他们既提供简陋的虚拟专用服务器(VPS)，他们称之为“水滴”，也提供更高级的产品，如负载平衡器和托管数据库。我们将在随后的章节中讨论以上所有内容。

要遵循本指南，您需要[创建一个数字海洋账户](https://m.do.co/c/d95c995e23b2)。如果你还没有的话，你还需要创建一个 [GitHub](https://github.com) 账户。由于我是 Node.js 开发人员，本指南将使用一个基本的 Node.js 服务(Docker ),尽管它可以很容易地适应您更熟悉的任何平台。

## 建设数字海洋的基础设施

在本演示结束时，您将创建两个$5/mo。水滴，一个 10 美元/月。负载平衡器和一个免费的容器注册表。DigitalOcean 对这些产品按小时收费，所以一旦你建立了一切并让它工作，你可以选择立即拆除基础设施，只需支付几美元。

看一看我们将要构建的基础架构:

![Digital-Ocean-Infrastructure](img/ca37a013efadf592c097e10b3d59c239.png)

一旦一切都完成了，你将有一个 GitHub 动作，自动地将你的库的主分支部署到`api-1`和`api-2`droplet 中。

在正常的构建中，这将导致一定的停机时间，因为随着新代码的部署，一个服务将被关闭，并且运行状况检查需要非零的时间量来确定服务是否被关闭。但是，通过本指南，您将学会以不会导致停机的方式进行部署。虽然这个例子使用运行在两个 droplets 上的服务，但是您可以轻松地将其扩展到三个或更多。

## 部署时间表

在本节中，我们将回顾本文档中涵盖的方法的高级解释，该方法可适用于许多平台，而不仅仅是数字海洋。例如，如果您想使用 HAProxy 作为负载均衡器，将请求路由到一个强大的服务器上的两个 Golang 进程，您完全可以这样做。

下面是将要进行的操作的时间表。为了节省篇幅，我们将对`api-1`实例进行比`api-2`实例更详细的描述，尽管两者将经历相同的过程:

![Digital-Ocean-Deployment-Timeline](img/64cb46b674c998dedd959a03520c2271.png)

在上图中，x 轴代表时间，从左向右移动。当部署过程第一次开始时，有两个服务实例正在运行，API 1 和 API 2，它们都运行代码库的 V1。当这种情况发生时，负载平衡器向它们发送健康检查，以确保它们能够接收请求。

最终，会发生一个部署，导致调用关闭端点。从那时起，健康检查将失败。请注意，即使运行状况检查失败，服务仍然能够处理请求，并且仍然在路由流量。一旦两次检查失败，该服务器实例将从负载平衡器中删除，替换为代码库的 V2，然后重新启动。通过三次健康检查后，负载平衡器再次开始将请求路由到实例。一旦完成，部署过程将继续到下一个服务实例。

从高层次上来说，有两条重要的信息可以从上述信息中获得:

1.  负载平衡器将至少路由到一个可用的实例。
2.  当请求被路由到一个实例时，该实例总是能够提供响应。

有了这些知识，你现在就可以开始阅读我们的数字海洋指南了。

## 部署指南:使用数字海洋实现零停机

### 创建令牌

令牌允许应用程序代表您与 DigitalOcean API 交互。在这个例子中，它们将被用于 GitHub 构建服务器将 Docker 图像推送到容器注册表中，这样你的 droplets 就可以从容器注册表中提取图像。

访问[数字海洋 API 设置](https://cloud.digitalocean.com/account/api/tokens)页面，生成两个新令牌。命名第一个“**GitHub Actions”**和第二个“**水滴注册表 Pull** 在本例中，两者都可以设置为读写访问。记下这些 API 令牌，因为您稍后会用到它们。

这些令牌应该对第三方保密。我们使用两个令牌，这样，如果其中一个受到损害，就可以删除它，而不会影响另一个。

### 生成 SSH 密钥

当通过 SSH 与服务器通信时，使用 SSH 密钥比使用密码安全得多。出于这个原因，您现在将生成一个 SSH 密钥(比密码更长更随机)来访问 droplets。

使用 SSH 密钥将允许您手动连接并执行一些初始设置，还将允许 GitHub 向 droplets 传输文件。

要生成 SSH 密钥，请运行以下命令:

```
$ ssh-keygen -t rsa -f ~/.ssh/api-droplets
# leave password blank

```

该命令将创建两个密钥文件。第一个位于 *~/。ssh/api-droplets* 是您的私人密钥，您不应该与第三方共享。第二个文件位于 *~/。ssh/api-droplets.pub* 并且是公钥。这个你可以少小气一点。

### 创建液滴(VPC)

使用数字海洋界面，[创建两个水滴](https://cloud.digitalocean.com/droplets/new)。

这样做时，系统会提示您提供一些详细信息。对于发行版，选择 **Debian 10** 。对于该计划，选择**基本 5 美元/月**。对于数据中心选项，选择离您最近的数据中心，并确保您稍后创建的负载平衡器在同一个数据中心。我给自己选了 SFO2。

在认证部分，点击**新建 SSH 密钥**按钮。给 SSH 密钥起一个类似 **Droplet SSH Key** 的名字，将 *~ `/.ssh/api-droplets.pub`* 文件的内容粘贴到 SSH 密钥输入中，然后点击**添加 SSH 密钥**。将要产生的液滴数量设置为 **2** 。

对于主机名，称它们为 **api-1** 和 **api-2** 。最后，用一个名为 **http-api** 的新标签来标记这两个水滴。这个标签稍后将被负载平衡器用来将请求匹配到 droplets。

一旦你创建了 droplets，你应该会看到它们像这样列在界面上:

![Droplet-Interface-Visual](img/5d722eb726e34a6dde7a4e136413fbe9.png)

此处列出的 IP 地址是您的 droplet 的公共 IP 地址。这些地址在互联网上唯一地识别你的水滴。使用这些 IP 地址，您现在将从您的开发机器 SSH 到两个 droplets。

为您的第一个 droplet 运行以下命令:

```
$ ssh [email protected]<DROPLET_IP_ADDRESS> -i ~/.ssh/api-droplets
# for first connection, type 'yes' and press enter

```

一旦你连接上了，你需要做一些事情。

首先，在 VPS 上安装 Docker。这将用于封装和运行您的应用程序。你还需要安装`doctl`二进制文件，并使用它进行认证，这允许 VPS 与 DigitalOcean 交互。要执行此设置，请运行以下命令:

```
$ sudo apt install curl xz-utils
# type 'y' and press enter
$ curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh
$ wget https://github.com/digitalocean/doctl/releases/download/v1.54.0/doctl-1.54.0-linux-amd64.tar.gz
$ tar xf ~/doctl-1.54.0-linux-amd64.tar.gz
$ mv doctl /usr/local/bin/
$ doctl auth init
# Paste the <DROPLET_REGISTRY_PULL_TOKEN> and press enter
$ exit

```

提醒一下，你需要在你的两个 droplet 上运行这些命令集，所以一旦你退出，再次为第二个 droplet 的 IP 地址运行`ssh`命令。这只是一个一次性的引导过程，您以后不必再返回。

### 创建负载平衡器

现在你已经创建了你的 droplets，下一步你会想要使用 digital ocean UI[创建一个负载平衡器](https://cloud.digitalocean.com/networking/load_balancers/new)。对于这个演示来说，**小$ 10/月**选项就可以了。如前所述，确保它位于创建两个液滴的同一区域。

在“添加液滴”字段中，键入标签`http-api`并点击结果以动态匹配您的液滴。对于这个项目，将 HTTP 从端口 80 转发到端口 80 的选项就足够了。

编辑高级设置以配置运行状况检查端点。通常，负载平衡器会向/端点发出请求，但此项目需要一个专用端点来进行健康检查。

要设置这个专用端点，请将“路径”更改为`/health`，将“不健康阈值”设置为 **2** ，将“健康阈值”设置为 **3** 。您的配置现在应该如下所示:

![Load-Balancer-DigitalOcean-UI](img/c59f5b4f823964c31780ea1a9fef5fa5.png)

给你的负载平衡器起一个朗朗上口、易于记忆的名字。在我的例子中，我选择了`sfo2-api`。

一旦保存了负载平衡器，您应该会在 UI 中看到它。我的负载平衡器看起来像这样(注意，2 个匹配的 drop 中有 0 个是健康的，因为服务器没有在它们上面运行):

![Load-Balancer-Visual-Healthy-Droplets](img/d2ac31b54ff9e4ec94ead0de54922d5c.png)

正如 droplets 的情况一样，IP 地址是标识负载平衡器的唯一 IP 地址。此时，您可以从开发机器向负载平衡器发出请求，以确保它能够工作。在您的终端中运行以下命令:

```
$ curl -v http://<LOAD_BALANCER_IP_ADDRESS>/

```

当你这样做的时候，你应该得到一个 HTTP `503 Service Unavailable`错误，一个响应体说“没有服务器可以处理这个请求。”这是意料之中的；在我们流程的这一点上，没有健康的服务器。

### 创建容器注册表

接下来，您将使用 digital ocean UI[创建一个容器注册表](https://cloud.digitalocean.com/registry)。这是存放 Docker 图像的地方。

默认情况下，您被限制在 500MB 的空闲存储空间，这对这个实验来说已经足够了。对于更大的项目，你会很快超过这个数字。事实上，这个项目的第一次部署消耗了大约 300MB 的存储，尽管额外的部署只增加了几兆字节。

当你创建注册表时，你需要给它一个唯一的名字。在本例中，我选择了名称`foo`，但是您需要选择一个在所有 DigitalOcean 客户中全球唯一的名称。

### 创建 GitHub 存储库

为了继续使用 DigitalOcean 设置我们的零停机部署，我们将使用 GitHub UI[创建一个新的存储库](https://github.com/new)。

将本地目录配置为指向存储库。确保使用新的`main`分支约定，而不是`master`。GitHub new repository 屏幕提供了你需要的所有命令。

完成后，将以下文件添加到存储库中:

### `.github/workflows/main-deploy.yml`

GitHub 动作利用`.github/workflows/`目录来查找项目要使用的各种动作的描述。例如，您可能有一个文件，它描述了发出拉请求时要执行的操作，比如运行 linter 和一些测试。

在这种情况下，当代码被合并到主分支时，您只需要一个文件来描述部署过程。使用下面的文件作为模板，注意你会想要用你的数字海洋注册表的名字替换`<REGISTRY_NAME>`，就像我使用的`foo`值。

```
name: Deploy to Production
on:
  push:
    branches:
      - main
  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Check Out Repo 
        uses: actions/[email protected]
      - name: Install DigitalOcean Controller
        uses: digitalocean/[email protected]
        with:
          token: ${{ secrets.DIGITALOCEAN_ACCESS_TOKEN }}
      - name: Set up Docker Builder
        uses: docker/[email protected]
      - name: Authenticate with DigitalOcean Container Registry
        run: doctl registry login --expiry-seconds 180
      - name: Build and Push to DigitalOcean Container Registry
        uses: docker/[email protected]
        with:
          context: .
          push: true
          tags: |
            registry.digitalocean.com/<REGISTRY_NAME>/api:latest
            registry.digitalocean.com/<REGISTRY_NAME>/api:sha-${{ github.sha }}

  deploy-api-1:
    needs: build
    runs-on: ubuntu-latest
    steps:
      # Droplets already have docker, doctl + auth, and curl installed
      - name: Deploy api to DigitalOcean Droplet
        uses: appleboy/[email protected]
        with:
          host: ${{ secrets.DO_API1_HOST }}
          username: root
          key: ${{ secrets.DO_API_KEY }}
          port: 22
          script: |
            doctl registry login --expiry-seconds 180
            docker pull registry.digitalocean.com/<REGISTRY_NAME>/api:latest

            echo "calling shutdown endpoint..."
            curl --silent http://localhost/shutdown || true

            echo "giving healthcheck time to fail..."
            sleep 30 # ((unhealthy + 1) * interval)

            docker stop api || true
            docker rm api || true

            echo "starting server instance..."
            docker run -d \
              --restart always \
              -p 0.0.0.0:80:80 \
              --name api \
              registry.digitalocean.com/<REGISTRY_NAME>/api:latest

            echo "giving healthcheck time to recover..."
            sleep 40 # ((healthy + 1) * interval)

            curl --silent --fail http://localhost/health

  deploy-api-2:
    needs: deploy-api-1 # rolling deploy
    runs-on: ubuntu-latest
    steps:
      # Droplets already have docker, doctl + auth, and curl installed
      - name: Deploy api to DigitalOcean Droplet
        uses: appleboy/[email protected]
        with:
          host: ${{ secrets.DO_API2_HOST }}
          username: root
          key: ${{ secrets.DO_API_KEY }}
          port: 22
          script: |
            doctl registry login --expiry-seconds 180
            docker pull registry.digitalocean.com/<REGISTRY_NAME>/api:latest

            echo "calling shutdown endpoint..."
            curl --silent http://localhost/shutdown || true

            echo "giving healthcheck time to fail..."
            sleep 30 # ((unhealthy + 1) * interval)

            docker stop api || true
            docker rm api || true

            echo "starting server instance..."
            docker run -d \
              --restart always \
              -p 0.0.0.0:80:80 \
              --name api \
              registry.digitalocean.com/<REGISTRY_NAME>/api:latest

            echo "giving healthcheck time to recover..."
            sleep 40 # ((healthy + 1) * interval)

            curl --silent --fail http://localhost/health

```

该文件包含三个作业。第一个是`build`，它将在 Ubuntu 虚拟机内部构建 docker 容器。它还标记容器并将其推送到您的容器注册表中。

`deploy-api-1`和`deploy-api-2`作业也运行在 Ubuntu 虚拟机上，但是它们通过 SSH 完成所有的工作。具体来说，它们连接到你的 droplets，提取新的 docker 图像，告诉服务关闭，并等待健康检查失败。之后，旧容器被移除，并且基于新图像的新容器被启动。

随着新容器的启动，将会执行新的运行状况检查。为了安全起见，还将调用健康检查端点。这样，如果调用失败，作业将失败，任何后续部署都不会发生。

不可否认，这个文件的一个突出问题是，每个部署的全部内容都是复制和粘贴的，虽然可以将这些内容转换成可组合/可重用的 GitHub 动作，但这只是另一天的指南。

## 相关文件解释

### **T2`Dockerfile`**

这个文件描述了如何构建 Docker 映像。它非常简单，不一定适合生产，但对于本例来说已经足够了:

```
FROM node:14

EXPOSE 80

WORKDIR /srv/api
ADD . /srv/api

RUN npm install --production

CMD ["node", "api.mjs"]

```

此图基于 Node.js 14 LTS 线。它暗示内部服务监听端口 80。应用程序代码被复制到映像中的 */srv/api/* 目录。然后在最终运行 *api.mjs* 文件之前进行生产安装。

### `.dockerginore`

该文件列出了不应复制到映像中的文件和目录:

```
.git
.gitignore
node_modules
npm-debug.log
test

```

这里最重要的一行是用于`node_modules/`目录的一行。这很重要，因为这些文件应该在映像构建过程中生成，而不是从您的操作系统中复制。

### `.gitignore`

这个文件主要是为了防止`node_modules/`被提交:

```
node_modules
npm-debug.log
```

### `api.mjs`

该文件代表一个非常简单的 API，它将在负载平衡器的后面提供，并且是服务的入口点:

```
#!/usr/bin/env node

import fastify from 'fastify';
const server = fastify();
let die = false;
const id = Math.floor(Math.random()*1000);

server.get('/', async () => ({ api: 'happy response', id }));

server.get('/health', async (_req, reply) => {
  if (die) {
    reply.code(503).send({ status: 'shutdown' });
  } else {
    reply.code(200).send({ status: 'ok' });
  }
});

server.get('/shutdown', async () => {
  die = true;
  return { shutdown: true };
});

const address = await server.listen(80, '0.0.0.0');
console.log(`listening on ${address}`);

```

`GET /`路线主要显示服务能够通过生成一个随机数作为标识符来运行。这个数字将在实例的整个生命周期中保持一致。

`GET /health`是负载平衡器用来了解应用程序是否健康以及是否能够接收请求的。`GET /shutdown`将`die`变量设置为`true`。一旦发生这种情况，任何对`GET /health`的后续请求都会返回一个不愉快的`503`状态代码。这是一种允许我们优雅地声明应该从负载均衡器中删除服务的机制。

### `package.json and package-lock.json`

这两个文件可以通过运行以下命令来生成:

```
$ npm init -y
$ npm install [email protected]

```

这将创建`node_modules/`目录并创建两个包文件。这些包文件稍后将在 Docker 构建过程中使用，以便从[npmjs.com](http://npmjs.com)包存储库中下载必要的包文件。

## GitHub 项目秘密

要运行您的部署，您还需要创建一些 GitHub 项目秘密。GitHub 动作 YAML 文件可以使用这些变量。

要创建您的项目机密，请转到 GitHub 项目的 settings 选项卡并添加四个条目。

您的第一个条目将是`DIGITALOCEAN_ACCESS_TOKEN`。这是您在上一步中生成的 GitHub Actions 访问令牌的值。

您的第二个条目将是`DO_API_KEY`。这将是您先前生成的`~/.ssh/api-droplets`私有密钥文件的内容。粘贴内容时要小心，因为你要确保换行被保留。

最后，您将添加两个条目，`DO_API1_HOST`和`DO_API2_HOST`。这两个文件都包含您创建的两个 API droplets 的 IP 地址。您的机密屏幕现在应该是这样的:

![GitHub-Project-Secrets-Screen](img/3d13ff02f49810013ee4e8e52dce54ec.png)

您之前创建的 GitHub 动作 YAML 文件中引用了所有这四个秘密名称。

## 运行您的首次部署

要运行第一次部署，请执行以下步骤:

1.  将文件更改合并到 GitHub 主分支，方法是创建并合并一个 pull 请求，或者将它直接添加到主分支并进行推送。一旦完成，部署过程就应该开始了。
2.  在 GitHub 存储库中，检查 Actions 选项卡。您应该会看到一个与代码合并到主分支相关的活动操作正在运行。单击它以查看更多信息。在我的屏幕上看起来是这样的:

![First-Deploy-Process-Output](img/38f5115d509a9d115b3aa555b9d528aa.png)

### 故障排除

如果您在流程的这个阶段遇到失败，您可能需要修改前面的步骤。

如果你转录的代码有问题，那么修改它，并再次提交到主分支。这将自动启动另一个构建。

如果您需要更改 GitHub 密码，那么使用 GitHub UI 进行更改——要知道这不会启动另一个部署。相反，再次访问 Actions 选项卡，单击左侧的“Deploy to Production”按钮，并使用右侧的“Run workflow”下拉菜单从主分支再次启动构建。

在我们的示例中，您可以看到在成功完成`build`之后，在第二步中，`api-1`被部署。下一步，也就是部署`api-2`，还没有发生，因为它正在等待`api-1`完成。如果部署失败，那么`api-2`将不会被部署。这为您提供了修复任何问题和部署修复程序的时间。此外，如果这些步骤中的任何一个失败了，你可以点击它们来获得更多的信息。

### 监控应用程序运行状况

负载平衡器的 DigitalOcean 图表显示了应用程序随时间变化的健康状况，根据我的经验，每分钟都会轮询应用程序的健康状况。

根据时间的不同，您可能会看到一个服务先关闭后打开，另一个服务先关闭后打开。如果您在部署第一个更改后等待几分钟，然后触发另一个部署，您应该能够在数字海洋图表中看到效果。

我的情况是这样的:

![Monitor-Application-Health-DigitalOcean-Graph](img/7d0ff1f44a0906f9cf9db962ad98379c.png)

停机时间图清楚地显示`app-1`(绿色)有停机时间。另一个`app-2`(棕色)没有在正确的时间进行投票，导致图表出现尖峰。健康检查图表显示`app-2`受到轻微影响。

`build`步骤将 Docker 图像推送到您的容器存储库中。每次这种情况发生时，图像被标记两次；一个包含`latest`标签，另一个包含构建发生时主分支的 git 提交散列。

这是我的容器注册表在执行两次构建后的样子:

![Container-Registry-Two-Builds](img/1c947c254582e137dafa57b9c817356a.png)

标签`latest`在每次编译时都会被替换。这是用于将 docker 映像部署到生产环境的标签。使用提交散列的标记只是为了方便向您展示它正在工作。更健壮的系统可以使用它来回滚部署到以前的提交。

## 发出负载平衡请求

在我们项目的这一点上，当代码被合并到主分支时，您现在已经得到了一个自动部署到生产的服务。最重要的是，它以这样一种方式做到了这一点，即未来的部署应该实现零停机时间！

现在，您已经准备好证明应用程序是以冗余方式运行的。您可以通过多次运行以下命令来实现这一点:

```
$ curl http://<LOAD_BALANCER_IP_ADDRESS>/
# {"api":"happy response","id":930}
$ curl http://<LOAD_BALANCER_IP_ADDRESS>/
# {"api":"happy response","id":254}

```

在响应中，您应该看到返回了两个不同的`id`值。对于您发出的每个请求，返回的 id 应该是交替的。这是因为默认情况下，负载平衡器被配置为使用“循环”算法来路由请求。

如果其中一台服务器崩溃，它将从循环中删除。使用运行状况检查的配置，负载平衡器可能需要 11 到 20 秒才能意识到其中一个实例已关闭。在此期间，发送到负载平衡器的请求有 50%会失败。更积极的健康检查可以减少这一时间，但很难建立一个 100%抵御故障的系统。

当然，传递 IP 地址并不那么方便，但是您可以为域配置 DNS 设置以指向 IP 地址。再次，另一天的另一个指南。

## 生产化

总的来说，这是一个相当简短的指南，旨在向您展示如何实现零停机部署。它掩盖了许多重要的细节，尤其是关于安全性。虽然不全面，但您应该采取一些额外的步骤来使您的基础架构更加安全:

*   不要暴露端口`:80`上的关闭端点。相反，只监听`127.0.0.1`(本地接口)上的不同端口。请注意，目前任何人都可以调用`[http://<LOAD_BALANCER_IP>/shutdown](http://<LOAD_BALANCER_IP>/shutdown)`来禁用一个微滴。
*   将`healthcheck`端点重命名为更难猜测的名称
*   对于一个真实的应用程序，将来自负载平衡器的 HTTPS 请求转发到 API 上的 HTTP
*   在 droplets 上使用非超级用户帐户

最后，记住 API 服务监听`0.0.0.0`(所有接口)，所以客户端可以通过直接请求 Droplet IP 来绕过负载均衡器。请记住，每个 droplet 公开两个网络接口，一个公共接口和一个私有接口，Node.js 服务应该监听负载平衡器可以到达的私有接口。

## 结论

在正常的构建中，部署通常会导致一些停机时间。在本指南中，我们回顾了如何使用 DigitalOcean、GitHub 和 Docker 以零停机时间的方式进行部署，并可扩展到运行在两个或更多 droplets 上的服务。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)