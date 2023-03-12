# 主持你自己的苗条/札幌应用程序-日志火箭博客

> 原文：<https://blog.logrocket.com/hosting-your-own-svelte-sapper-app/>

你已经使用 Sapper 和 Svelte 构建了一个令人惊叹的应用程序，但是现在呢？我们在哪里举办它，使它对世界可用？本文将列出一种可能的方法中涉及的步骤，即使用 [DigitalOcean](https://m.do.co/c/cfc7d387f018) 自托管您自己的 Sapper 应用程序。

我最近经历了这个过程，构建了一个应用程序，并使用 [Vercel](https://vercel.com) 托管它。他们有一个灵活的部署过程，但由于他们更适合无服务器应用程序，我很快意识到我需要比他们提供的更多。于是我租了一个虚拟服务器，把 app 搬到了那里。

如果你喜欢，你可以看看我的[Shop Ireland](https://www.shopireland.ie)Sapper/svelite 项目。它运行在作为节点应用的 [DigitalOcean](https://m.do.co/c/cfc7d387f018) droplet 上，带有 Nginx 反向代理。我还有第二个应用程序，作为 API 层从亚马逊获取产品信息。

## 我们在这篇文章中讨论的内容

在本文中，我将介绍设置服务器来运行节点项目(如 Sapper 应用程序)的步骤。如果你有兴趣运行你自己的 svelet/Sapper 应用程序，我希望这可以作为一个好的起点。

> 注意:这是基于 Ubuntu 版本编写的。在新版本中，一些细节可能会发生变化。

### 主题:

*   设置您的数字海洋水滴
*   节点和 Nginx 代理服务器
*   使用 Git 推送应用程序代码
*   使用 PM2 运行您的应用
*   使用缓存设置 Nginx 服务器块
*   添加域名
*   测试您的站点

## 虚拟服务器入门

我的网站每天只有大约 500 次页面浏览，所以它不需要一个强大的托管计划。

有许多提供虚拟服务器的服务可供选择。当您租用虚拟服务器时，您将获得一个安全的服务器片，作为一台独立的机器。一般来说，它们不会附带像 phpMyAdmin 这样的管理工具，但是我们不需要这样的东西。我也很熟悉 Ubuntu，所以我选择了它作为平台。

首先，我选择了一台入门级服务器，每月 5 美元，位置靠近我的主要受众。即使以这个价格，它也足够强大来运行一个苗条和笨拙的应用程序。

当注册和设置您的机器时，选择使用 SSH 登录是一个好主意。您应该能够添加您的公共 SSH 密钥的内容。这可以在(Mac/Linux)中找到:

```
cat ~/.ssh/id_rsa.pub
```

如果没有，[本指南中的“创建 RSA 密钥对”步骤应该有助于](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-1804#step-1-%E2%80%94-create-the-rsa-key-pair)。

一旦您注册了您的机器，我们就可以登录并开始设置我们的服务器！

## 登录并设置 SSH、sudo 和防火墙

我们需要进行一些初始设置。首先使用 ssh 登录(`YOUR-IP`是设置期间给定的 IP 地址):

```
ssh [email protected]_IP_ADDRESS
```

登录后，按照这个简短的指南通过[设置一个用户。这应该会让您使用相同的 SSH 密钥进行自己的登录。然后，您将能够使用(`username`是您在上述步骤中选择的任何名称)登录到您的服务器:](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04)

```
ssh [email protected]_IP_ADDRESS
```

你将只能通过 SSH 访问服务器，因为防火墙会阻止其他端口，但我们会在安装 Nginx 时解决这个问题。您还可以使用`sudo`！

接下来，我们将设置 Node 和 Nginx 反向代理。

## 节点和 Nginx 代理服务器

因为我正在设置一个使用 [Express](https://expressjs.com/) 的 Sapper 应用程序，所以我们需要 Node。首先向我们的系统添加一些存储库信息。以下代码下载并运行一个添加必要的 repo URLs 的脚本。

如果你想知道这些脚本的作用，你可以在这里找到这些脚本:

```
cd ~ curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh sudo bash nodesource_setup.sh
```

设置完成后，安装节点:

```
sudo apt install nodejs
```

一旦完成，您应该能够看到您的节点版本与`nodejs -v`。最后一件事，一定要添加`build-essential`包，因为有些包会需要它:

```
sudo apt install build-essential
```

## Nginx

Nginx(发音为 Engine-X)是一个轻量级、快速的 web 服务器，非常适合充当我们的节点应用程序的网关。它非常强大，可以做很多事情，但我们将主要依靠它来进行缓存。

我们从更新然后安装软件包开始:

```
sudo apt update sudo apt install nginx
```

在我们运行它之前，我们需要让防火墙知道。对于本指南，我不打算直接在 Nginx 上安装 SSL，但是如果你想这样做[本指南有更深入的信息](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04)。

让我们将 Nginx HTTP 访问添加到防火墙:

```
sudo ufw allow 'Nginx HTTP'
```

我们可以通过运行`systemctl status nginx`来检查 Nginx 的状态。如果显示`Active`，您应该能够导航到您的 IP 地址并看到成功页面。

接下来，我们将暂时把 Nginx 配置放在一边，设置我们的 Sapper 应用程序和 API 代码。

## 使用 Git 推送应用程序代码

构建、测试和部署项目的方法有很多。对于这个项目，我想要一些非常简单的东西。我知道一次只有一个人在做这件事，我不想引入太多复杂的层次。

以前，我会求助于 FTP 之类的工具将文件上传到服务器。谢天谢地，现在我们有了更好的部署方法，其中一种方法就是使用一个 [Git 钩子](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)。使用 Git 钩子，我们可以将代码从我们的`master`分支自动复制到服务器。在接下来的步骤中，我将重用[这篇概述](https://macarthur.me/posts/deploying-code-with-a-git-hook)中的一些代码。

我们从创建存储代码的目录开始。你可以叫他们任何你喜欢的名字，但在这种情况下，我会选择`repo`和`www`。`repo`代表我们将代码推入的 Git 存储库，`www`目录包含我们的实时代码:

```
cd ~/ mkdir -p repo/YOUR_APP mkdir -p www/YOUR_APP
```

`YOUR_APP`部分是你的应用名称。随你怎么称呼它，但一定要用相同的名字替换对`YOUR_APP`的进一步引用。

我们导航到我们的 repo 文件夹，并设置一个裸 Git repo:

```
cd ~/repo/YOUR_APP git init --bare
```

现在我们设置了一个 [Git 钩子](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)来执行我们在推送到这个回购之后想要运行的命令。现在我们创建`post-receive`钩子。

我使用`nano`来编辑服务器上的文件，但是根据需要替换这个命令。我将使用以下方法创建并保存挂钩:

```
cd hooks nano post-receive
```

并添加以下内容:

```
#!/bin/bash
GITDIR="/home/don/repo/YOURAPP" TARGET="/home/don/www/YOUR_APP"
while read oldrev newrev ref do BRANCH=$(git rev-parse --symbolic --abbrev-ref $ref)
if [[ $BRANCH == "master" ]];
then
   # Send a nice message to the machine pushing to this remote repository.
   echo "Push received! Deploying branch: ${BRANCH}..."

   # "Deploy" the branch we just pushed to a specific directory.
   git --work-tree=$TARGET --git-dir=$GIT_DIR checkout -f $BRANCH
else
   echo "Not master branch. Skipping."
fi
# We will add other steps here
done
```

然后用`Control+X`和`y`保存文件。

在继续之前，我们需要使该文件可执行:

```
chmod +x post-receive
```

上面的代码设置了一些变量(根据需要调整`YOUR_APP`部分！)然后在`do`和`done`行中运行代码来应用我们的钩子。

目前，它所做的只是复制我们的代码，但前提是我们已经推送了`master`分支。让我们试一试。为了使用它，我们需要在我们的项目中添加一个`remote`。在我们的**本地**机器上的项目文件夹中，添加以下 remote:

```
git remote add origin [email protected]_IP_ADDRESS:/home/USER_NAME/repo/YOUR_APP
```

对于上面所有的 shouty 位，确保用相关的用户名、IP 地址和应用程序目录的名称来替换它们。

现在，当您向 master 推送时(您可能需要先进行更改)，您应该会看到类似这样的内容:

```
remote: Push received! Deploying branch: master...
```

还有一些其他的噪音。如果您返回到您的服务器并检查`www/YOUR_APP`目录，您应该会看到您的应用程序文件！

在我们继续之前，让我们通过添加多个源来简化 Git 部署过程。调整以下命令以包含您的 Git repo 位置。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
git remote set-url origin [email protected]:username/your-app.git git remote set-url --add --push origin [email protected]_IP_ADDRESS:/home/USER_NAME/repo/YOUR_APP git remote set-url --add --push origin [email protected]:username/your-app.git
```

非常感谢 Alex 关于这个问题的有益文章。现在我们可以部署代码了，让我们运行我们的应用程序。

## 使用 PM2 运行您的应用

我通常使用`npm start`(或者在本地工作时有时使用`npm run dev`)运行我的节点应用程序。在服务器上，我们当然也可以这样做，但是除非我们使用某种服务来监控我们的应用程序，否则它可能会崩溃并变得没有响应。

有某种工具来自动重启我们的应用程序，以及在服务器重启时启动任何应用程序，这很好。PM2 是一个有用的工具，它管理我们的应用程序并确保它们保持运行。

首先在全球范围内安装它，以便它可以在任何地方使用:

```
sudo npm install pm2 -g
```

## 运行 Sapper 应用程序

让我们运行应用程序。首先，我们需要安装依赖项:

```
cd ~/www/YOUR_APP npm install
```

在运行我们的 Sapper 应用程序之前，我们需要构建它。在`dev`环境中工作时，我们不需要编译时的构建步骤，但在生产环境中运行时，我们需要这个步骤。因此，在我们开始运行我们的应用程序之前，请使用以下工具进行构建:

```
npm run build
```

这应该会输出很多行内容，因为它构建了应用程序的所有 JavaScript 片段。然后，我们可以使用 PM2 运行应用程序。

虽然我们可以通过简单的`pm2 start app.js --name App`为大多数节点应用程序设置 PM2，但我们需要使用我们的`npm start`命令。我们可以这样做:

```
pm2 start npm --name "AppName" -- start
```

运行该命令后，我们使用以下命令保存 PM2 系统的当前状态:

```
pm2 save
```

您还可以使用`pm2 list`随时查看正在运行的应用程序的状态。详见`pm2 examples`。

最后，我们希望确保应用程序在服务器重启时加载。通过以下方式进行设置:

```
sudo env PATH=$PATH:/usr/local/bin pm2 startup -u USER_NAME
```

务必像以前一样用您实际选择的用户名替换`USER_NAME`。

您现在可以测试您的应用程序了。如果一切按计划进行，它应该在端口`3000`上运行:

```
curl http://localhost:3000
```

如果你在这里得到一个错误，检查你的代码是否都在适当的位置，并运行`pm2 restart AppName`来重新启动它。

## 改进 Git 挂钩

随着应用程序的运行，我们现在可以改进 Git 挂钩，让它处理构建步骤，运行 npm install，并在每次部署时重启 PM2。通过在`done`行之前添加以下内容来更新我们的钩子:

```
cd /home/don/www/YOUR_APP npm install --production npm run build pm2 restart AppName
```

再次注意用您的值替换`YOUR_APP`和`AppName`。

现在，当您推送至 master 时，它应该会安装依赖项，构建应用程序，并使用 PM2 重启应用程序。

## 使用缓存设置 Nginx 服务器块

我们已经运行了我们的应用程序，并在`localhost:3000`获得了预期的输出，所以下一步是让 Nginx 充当反向代理。这将捕捉任何对端口 80 的 web 请求，并将它们定向到我们的应用程序(或缓存版本)。

## 贮藏

在我们设置服务器块来告诉 Nginx 在哪里可以找到我们的应用程序之前，我们将快速设置缓存。Nginx 有一个缓存选项，它查看从我们的应用程序发回的头，并将请求的副本保存到磁盘。然后，它将这个保存的版本返回给每个新请求，直到缓存过期。

首先为我们的缓存创建一个存储目录:

```
sudo mkdir /var/cache/nginx
```

然后，我们将在服务器块中配置缓存设置。

## 添加服务器块

我们首先在我们的`sites-available`目录中建立一个条目。

```
sudo nano /etc/nginx/sites-available/YOUR_SITE.vhost
```

在这个文件中，我们添加了一些指令来告诉 Nginx 如何提供我们的应用程序:

```
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m max_size=10g inactive=480m use_temp_path=off;
server { listen 80; listen [::]:80;
  server_name example.com;

  location / {
     proxy_cache my_cache;
     proxy_cache_use_stale error timeout updating http_500 http_502 http_503 http_504;
     proxy_cache_revalidate on;
     proxy_pass http://localhost:3000;
     proxy_http_version 1.1;
     proxy_set_header Upgrade $http_upgrade;
     proxy_set_header Connection 'upgrade';
     proxy_set_header Host $host;
     proxy_cache_bypass $http_upgrade;
  }
}
```

使用`Control + X, y`退出并保存。

开始时，`proxy_cache_path`设置缓存的位置以及要存储多少个`levels`(如果您认为您将存储数百万个请求，这可以调整)。`keys_zone`部分设置一个命名的区域，以及它在内存中有多大，用于存储关于缓存内容的元数据。我们将`max_size`设置为缓存可以占用的最大空间量。在这种情况下，这是一个慷慨的`10g`。

在这种情况下，我们将可选的`inactive`值设置为 8 小时，并将`use_temp_path`设置为`off`，因为这样可以节省一点性能，因为它不再写两次内容。

接下来是`server`块本身。在这里，我们设置了端口，设置了一个`location`，并告诉`proxy_pass`将请求传递给我们在`[http://localhost:3000](http://localhost:3000)`上的应用程序。

我们还告诉服务器在哪里可以找到我们上面定义的`proxy_cache`，我们使用了一个非常有趣的可选设置`proxy_cache_use_stale`。这告诉服务器，如果由于某种原因文件返回错误，则使用旧的缓存条目。

有很多设置可用，您可以在本文中找到更多包含完整 Nginx 缓存选项的[。](https://docs.nginx.com/nginx/admin-guide/content-cache/content-caching/)

## 激活服务器块

我们现在在`sites-available`中有一个文件，但是我们需要通过从`sites-enabled`创建一个到它的链接来激活它。

```
sudo ln -s /etc/nginx/sites-available/YOUR_SITE.vhost /etc/nginx/sites-enabled/
```

在`sites-enabled`目录中可能已经有一个“默认”文件或类似文件。我们不需要它，所以你可以删除它。

现在我们已经有了从`sites-enabled`链接的文件，我们可以使用以下命令进行测试以检查我们的配置是否正常:

```
sudo nginx -t
```

如果一切都不顺利，检查错别字或丢失的分号！如果是，重启 Nginx 以激活更改:

```
sudo service nginx restart
```

我们现在应该能够通过这个 Nginx 反向代理在端口`80`上访问我们的内容:

```
curl http://localhost:80
```

如果一切顺利，让我们继续设置我们的公共网址和 HTTPS。

## 添加域名

有了在端口 80 上运行的应用程序和 IP 地址，是时候以 URL 的形式给这个应用程序一个家了。

有很多方法来处理域名，但我喜欢做的是使用 [CloudFlare](https://www.cloudflare.com/) 并利用他们免费的“灵活的”SSL 服务。它不是 100%安全，但可以升级，为处理敏感数据的网站或应用程序提供端到端加密。

在这种情况下，我的应用程序没有登录组件，也没有向浏览器发送任何敏感数据，所以我对他们最简单的 SSL 设置感到满意。如果你想添加更多，你可以[设置自己的 SSL](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-18-04) 或使用其他服务。

因此，如果我们使用的是 [CloudFlare](https://www.cloudflare.com/?utm_expid=.AQMGpiI2QjmXS6qIoQiqFQ.0&utm_referrer=https%3A%2F%2Fblog.logrocket.com%2F) ，请前往注册。然后添加一个新站点，并选择灵活的 SSL 选项。

如果您已经有一个域，它会提示您移动记录。您可能不想保留旧的记录，而是利用这个机会添加一个指向您的服务器 IP 地址的`A`记录，以及一个指向`YOUR_DOMAIN.rocks`的`www`的 CNAME。

设置完成后，系统会提示您将两台域名服务器添加到您的域中。记下这些。

## 设置您的名称服务器

登录到您的注册商并导航到您设置名称服务器的部分。更新列表，仅包含 CloudFlare 提供的两个。

然后，返回到 CloudFlare，按按钮继续。可能需要几个小时才能检测到更新的名称服务器。一旦它检测到变化，它会发电子邮件给你，你的网站是活的！

## 测试您的站点

现在，您应该可以使用`[https://YOUR_DOMAIN.rocks](https://YOUR_DOMAIN.rocks)`在自己的域名下访问您的应用程序。

## 解决纷争

那么，如果您看到可怕的`520`错误，您应该怎么做呢？一些可以尝试的事情:

*   检查您的应用程序是否已使用`npm run build`构建，并且是否成功
*   检查`pm2 list`以查看应用程序是否正在运行
*   在`sudo service nginx status`中寻找`Active`
*   仔细检查定位服务器块，确保它正在寻找`localhost:3000`

最后，您还可以使用以下命令在两者的默认位置检查日志:

```
sudo tail -f /var/log/nginx/access.log
```

```
sudo tail -f /var/log/nginx/error.log
```

## 总结和后续步骤

我们现在应该让我们的应用程序在我们自己的托管服务器上运行。从这里，我们可以考虑添加服务，如数据库，为我们可能构建的其他应用程序和 API 设置更多的`location`块，等等。

## 你自己看吧

你可以在网上的[商店爱尔兰](https://www.shopireland.ie)看到最终结果。这是一个运行在 Sapper 后端的苗条应用程序，另外还有一个运行在同一服务器上的 API 来提供产品数据。

多亏了轻量级的前端，Nginx 缓存，这个网站很快，我可以使用 Git 轻松地部署更改。

我希望这篇文章是有用的，可以帮助你把你的令人敬畏的 Sapper 项目放到网上！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)