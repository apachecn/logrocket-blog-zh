# 如何在 Ubuntu v22.04 上用 Docker Compose 运行 Laravel

> 原文：<https://blog.logrocket.com/how-to-run-laravel-docker-compose-ubuntu-v22-04/>

Laravel 是最流行的 PHP 框架之一。据 BuiltWith 称，Laravel 被世界各地超过 72 万个 T2 网站使用。Laravel 是一个自以为是的框架，模块化，易于使用，并且有一个[新兴的生态系统](https://i.ibb.co/Fh8TknP/laravel-ecosystem.jpg)。在这篇文章中，你将学习如何在 Ubuntu 22.04 上使用 Docker Compose 运行 Laravel。

向前跳:

## 先决条件

*   安装了 Docker 和 Docker Compose 的 Ubuntu v22.04。对于本教程，我们将使用 Docker v20.10.20 和 Docker Compose v2.12.2
*   码头工人的经验
*   一些关于 Laravel 的先验知识会有所帮助
*   任何关于 Docker 多阶段构建的知识都是有用的
*   了解 Apache 的工作原理及其与 PHP 的关系

## Docker 是什么？

Docker 是一个开源平台，它使软件工程师能够将他们的应用程序打包到称为容器的工件中，使它们易于运输，并避免了将“我的机器上的作品”添加到搜索查询中的问题。

那么，什么是容器呢？容器是一种使用户能够将整个堆栈打包成工件的技术。在这个上下文中，整个堆栈指的是操作系统、具有任何依赖关系的语言、您的应用程序代码及其依赖关系。因此，在每次部署时，您交付的是整个堆栈，而不仅仅是发生变化的五行代码。

容器由来已久，但在 2014 年被 Docker 普及。

映像是按照[开放容器倡议(OCI)标准](https://opencontainers.org/)构建的，因此它们可以在任何地方运行。

## 带背包的 Laravel 应用示例

在这篇使用 Docker 和 Docker Compose 运行 Laravel 的指南中，我们将使用一个简单的 Laravel 应用程序。这个基本的 Laravel 应用程序将使用一个 MySQL 数据库，为了展示事情是如何工作的，我们将使用流行的 [Backpack](https://backpackforlaravel.com/) Laravel 包，它用于构建和定制管理面板。

为了保持简单，因为 Laravel 已经提供了用户表和用户模型，我们将把它与 Backpack 连接起来，以查看和编辑用户。这里需要注意的是，如果您编辑一个用户，该用户将无法登录，因为密码不会被加密。然而，这不是本教程的关注点。对于这篇文章，重点是在 Ubuntu v22.04 上使用 Docker 和 Docker Compose 运行带有 MySQL 的 Laravel 应用程序。

我们应用程序的完整代码可以在这个 [GitHub 库](https://github.com/geshan/laravel-backpack-demo)中找到。还包括 [Dockerfile](https://github.com/geshan/laravel-backpack-demo/blob/master/Dockerfile) 和 [docker-compose.yml](https://github.com/geshan/laravel-backpack-demo/blob/master/docker-compose.yml) 文件。

登录后，终端应用程序会列出访问 [`http://localhost:8082/admin/user`](http://localhost:8082/admin/user) 的用户:

![Laravel Application Listing Users](img/6c4749c80cdea1de79e70ab91be2d4f5.png)

## 拉勒维尔帆

Laravel 9.x 的默认[安装说明](https://laravel.com/docs/9.x/installation#getting-started-on-linux)随 Laravel Sail 一起提供。Sail 是一款很棒的产品，也是 Laravel 生态系统不可或缺的一部分。其 GitHub 报告称，“Sail 为 Laravel 提供了 Docker 驱动的本地开发体验，兼容 macOS、Windows (WSL2)和 Linux。”那么，为什么我们不使用它呢？

Sail 对于开发环境来说是一个很好的工具，但是它不适合类似生产的环境，因为:

*   Sail 太通用了，包含了多个东西，比如数据库、Redis 和使用 MeiliSearch 的搜索。在生产中，这些依赖被卸载到其他服务，如 RDS 或 open search
*   [Dockerfile](https://github.com/laravel/sail/blob/1.x/runtimes/8.1/Dockerfile) 非常适合开发环境。它有 xdebug，Node.js 16，和 yarn。它也有 MySQL 和 Node.js 的客户端，但是这些东西增加了容器的体积和重量，使得它不适合类似生产的环境
*   Sail 没有使用官方的 Docker 图像，而是以 Ubuntu v22.04 作为基础图像，并根据需要添加内容。它看起来更像一个虚拟机(VM ),而不是一个优化的 Docker 映像
*   Sail 没有使用 Docker 的功能，如 [Docker 多阶段构建](https://geshan.com.np/blog/2019/11/how-to-use-docker-multi-stage-build/)，这不仅可以减少图像的大小，还可以使 Docker 图像在某些环境下得到很好的优化

这将带我们进入下一步，在这里您将克隆存储库，并了解使用 Docker 和 Docker Compose 运行 Laravel 应用程序所涉及的部分。

## 设置 Docker 和 Docker 撰写

首先，您可以使用`git clone [[email protected]](/cdn-cgi/l/email-protection):geshan/laravel-backpack-demo.git`克隆存储库。您可以使用`cd laravel-backpack-demo`进入文件夹，查看文件结构:

![Laravel Backpack Demo](img/b4a604e4ed51293835e36a8f8e93b28d.png)

它看起来很像其他的 Laravel 9.x 项目，但是您会发现一些其他有趣的文件。首先是`/db/init.sql`文件，除了默认的 Laravel 数据库结构之外，它还用于加载 MySQL Docker 容器中的一些用户和迁移数据。

然后在`/docker`文件夹中有一些 Docker 特有的配置文件。因为我们将在 Apache 中使用默认的 PHP Docker 映像，所以这里有一个 Apache 配置。同样，为了在生产中获得更好的性能，还包括了 [Opcache](https://www.php.net/manual/en/book.opcache.php) 配置文件。不会用到，但是放在那里做参考。

然后，在项目的根目录下，有三个文件将构成本教程的基础。`docker-compose-sail.yml`文件是默认的 Docker Compose 文件，它随 Laravel 9.x 的 Sail 安装一起提供。它已经从`docker-compose.yml`重命名为`docker-compose-sail.yml`，这样我们就可以使用`docker-compose.yml`文件作为默认文件。之后，我们有了利用多阶段 Docker 构建的 Docker 文件。

## Dockerfile for Laravel

在多阶段构建之后，在 Ubuntu 22.04 上运行 Laravel 9.x(撰写本文时为 9.19)的 docker 文件如下所示:

```
FROM composer:2.4 as build
COPY . /app/
RUN composer install --prefer-dist --no-dev --optimize-autoloader --no-interaction

FROM php:8.1-apache-buster as dev

ENV APP_ENV=dev
ENV APP_DEBUG=true
ENV COMPOSER_ALLOW_SUPERUSER=1

RUN apt-get update && apt-get install -y zip
RUN docker-php-ext-install pdo pdo_mysql

COPY . /var/www/html/
COPY --from=build /usr/bin/composer /usr/bin/composer
RUN composer install --prefer-dist --no-interaction

COPY docker/apache/000-default.conf /etc/apache2/sites-available/000-default.conf
COPY .env.dev /var/www/html/.env

RUN php artisan config:cache && \
    php artisan route:cache && \
    chmod 777 -R /var/www/html/storage/ && \
    chown -R www-data:www-data /var/www/ && \
    a2enmod rewrite

FROM php:8.1-apache-buster as production

ENV APP_ENV=production
ENV APP_DEBUG=false

RUN docker-php-ext-configure opcache --enable-opcache && \
    docker-php-ext-install pdo pdo_mysql
COPY docker/php/conf.d/opcache.ini /usr/local/etc/php/conf.d/opcache.ini

COPY --from=build /app /var/www/html
COPY docker/apache/000-default.conf /etc/apache2/sites-available/000-default.conf
COPY .env.prod /var/www/html/.env

RUN php artisan config:cache && \
    php artisan route:cache && \
    chmod 777 -R /var/www/html/storage/ && \
    chown -R www-data:www-data /var/www/ && \
    a2enmod rewrite

```

这个 Docker 文件从 PHP Composer 2.4 官方 Docker 映像的构建阶段开始。在这个阶段，整个应用程序被复制到映像中的`/app`文件夹，Composer 安装在生产模式下运行，没有交互，并留下了开发依赖项。所以此时，所有的非开发 Composer 包都已经安装在了`vendor`文件夹中。

接下来可以看到`dev`阶段。这也是稍后将在`docker-compose.yml`文件中使用的阶段。从[官方 PHP](https://hub.docker.com/_/php) v8.1 Apache Docker 镜像开始。它将一些环境变量如`APP_ENV`设置为`dev`，`APP_DEBUG`设置为 true，`COMPOSER_ALLOW_SUPERUSER`设置为`1`。这些变量是不言自明的。然后它安装了 zip，这是安装其他包所需要的。

因为我们在这个项目中只使用 MySQL，所以它只安装了`pdo`和`pdo_mysql` PHP 扩展。然后，我们从构建阶段获得合成可执行文件。这是使用多阶段 Docker 构建的另一个优势。因为我们将使用这个映像进行开发，所以我们用`composer install`安装了所有的依赖项，包括开发依赖项。

之后，我们将 Apache 配置文件从`/docker/apache/000-default.conf`复制到容器中的正确位置。该文件的内容如下所示:

```
<VirtualHost *:80>

  ServerAdmin [email protected]
  DocumentRoot /var/www/html/public/

  <Directory /var/www/>
    AllowOverride All
    Require all granted
  </Directory>

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

这是一个简单的 Apache 配置文件，从`/var/www/html/public`开始在端口 80 上为网站提供服务。它还有一些关于虚拟主机日志的指令。接下来，我们将`.env.dev`文件复制为容器上的`.env`文件。这个文件包含所有的配置，比如 MySQL 数据库的凭证。

最后，我们运行常规的 Laravel artisan 命令来配置缓存、路由缓存并使存储可写。然后确保用户`www:data`拥有正确的权限，并启用重写 Apache 模块。它应该可以工作，因为图像使用了 [apache2 前台](https://hub.docker.com/layers/library/php/8.1-apache-buster/images/sha256-b11d9c766bcf836c1c0553b22ffdeff46eb117605f214cad5c47843ac84d5ede?context=explore)作为命令。

下一个阶段是`production`，也是从官方的`php:8.1-apache`形象开始。它配置并启用 [OPcache](https://www.php.net/manual/en/intro.opcache.php) 用于生产中的缓存。OPcache 通过在共享内存中存储预编译的脚本字节码来提高 PHP 性能，这使得它非常适合生产用例。

然后像开发阶段一样安装`pdo`和`pdo_mysql`。还会加载 OPcache 配置。如果你对 OPcache 配置感兴趣，请查看[这个文件](https://github.com/geshan/laravel-backpack-demo/blob/master/docker/php/conf.d/opcache.ini)。接下来，它从构建阶段复制整个应用程序，在执行`compose install`时忽略了开发依赖。

同样的 Apache 配置也在这个 Docker 阶段加载，但是环境变量`.env.prod`被复制到`/var/www/html/.env`路径。在实际的应用程序中，这一行可能会被省略，以便以其他方式注入运行时环境变量，比如使用 Kubernetes secrets。最后，使用相同的命令让 Apache 服务于我们的 Laravel 应用程序。在下一节中，您将了解 Docker 合成文件。

## 拉威尔式码头

上述 Docker 文件是用 Docker 合成文件定义构建和运行的。您可以使用常规的 Docker 命令构建和运行单个容器。当你想运行多个容器并且不想输入带有多个参数的长命令时，Docker Compose 就派上了用场。

Docker Compose 文件是一个 YAML 文件，用于配置应用程序的服务。下面是运行我们的 Laravel 应用程序的 Docker Compose 文件和所需的 MySQL 容器。让我们仔细分析一下它的内容:

```
version: '3'
services:
  app:
    build:
      context: ./
      target: dev
    volumes:
      - .:/var/www/html
      - .env.dev:/var/www/html/.env
    ports:
      - "8082:80"
    environment:
      - APP_ENV=local
      - APP_DEBUG=true
    networks:
      - laravel
    depends_on:
      - mysql    
  mysql:
      image: 'mysql/mysql-server:8.0'
      environment:
        MYSQL_ROOT_HOST: "%"
        MYSQL_ROOT_PASSWORD: "root"
        MYSQL_USER: "laravel"
        MYSQL_PASSWORD: "FhgVoFuOrWspc3TgBIA2K4dZGuJTPwSYBoLnNckcaxy"
        MYSQL_DATABASE: "laravel"
        MYSQL_ALLOW_EMPTY_PASSWORD: 1
      ports:
        - 3906:3306
      volumes:
        - 'mysql8:/var/lib/mysql'
        - './db/init.sql:/docker-entrypoint-initdb.d/01init.sql'
      networks:
        - laravel
networks:
    laravel:
        driver: bridge
volumes:
    mysql8:
        driver: local

```

Docker 合成文件从[版本 3](https://docs.docker.com/compose/compose-file/compose-file-v3/) 开始，它有一组特性。然后我们定义我们的服务。第一个是`app`服务，它将 PHP 和 Apache 服务器运行在相同的容器中。为此，我们指示 Docker Compose Docker 文件与 context 在同一个目录中，目标(读取阶段)是 dev。因此，我们希望 Docker Compose 从我们在上一节中讨论的多阶段支持 Dockerfile 构建`dev`阶段。

在这个`app`服务中，我们然后在 volumes 部分指定我们想要从本地机器同步到容器的文件。我们说我们希望当前目录中的所有文件都同步到容器中的`/var/www/html`。我们还指定本地的`.env.dev`文件需要与同一路径的`.env`文件同步。

接下来，我们指定本地端口`8082`映射到容器端口 80。在容器的端口 80 上，Apache 服务器将会运行。之后，我们添加两个值为`local`的环境变量`APP_ENV`和值为`true`的`APP_DEBUG`。当我们使用这个容器进行本地开发时，如果发生错误，这些设置将在浏览器上显示错误。

之后，我们将 app 服务的网络指定为`laravel`。在 [Docker Compose](https://docs.docker.com/compose/networking/) 中的联网使多个服务能够相互通信。对于这个例子，我们将使用[桥接网络](https://docs.docker.com/network/bridge/)，它允许连接到同一个桥接网络的容器之间的通信。因此，我们指定应用容器`depends_on` MySQL 容器在文件中被定义。它不是一个硬依赖，而是用于控制启动和关闭的[序列。](https://docs.docker.com/compose/startup-order/)

文件中接下来定义了`mysql`服务。它是由 MySQL 提供的一个 [MySQL 服务器镜像](https://hub.docker.com/r/mysql/mysql-server)。对于本指南，我们使用 8.0 版。这里定义了一些重要的环境变量，比如根用户的密码`ROOT_HOST`，以及另一个 MySQL 用户和密码。数据库被指定为`laravel`。

主机上的端口 3906 映射到运行 MySQL 服务器的容器上的 3306。为了在重启后保持数据的持久性，定义了一个名为`mysql8`的卷，它被映射到容器上的默认数据目录 MySQL 的`/var/lib/mysql`中。另一个重要的卷映射是入口点的`./db/init.sql`文件。这样做是为了在第一次启动时，我们的虚拟用户数据将被加载到空的 MySQL 服务器实例上。接下来，网络被定义为`laravel`并且`laravel`网络被设置为桥接网络。

最后但同样重要的是，名为`mysql8`的 MySQL 服务器卷被标记为本地驱动程序。这意味着 Docker 将管理数据的存储位置。您可以通过运行`docker volume ls`命令来了解有关该卷的更多信息。您还可以了解其他有用的 [Docker 命令](https://geshan.com.np/blog/2022/05/docker-commands/)，比如 Docker 图像、日志等等。在下一节中，您将了解 Docker `ignore`文件。

## 不要忽略`dockerignore`文件

类似于我们如何使用`.gitignore`文件从 git 中排除某些文件，我们可以使用`.dockerignore`文件来排除复制到 Docker 映像和容器的文件。例如，下面是这个例子中使用的 [`.dockerignore`文件](https://github.com/geshan/laravel-backpack-demo/blob/master/.dockerignore)。

```
.git
.env

```

上面的文件指示 Docker 不要将`.git`文件夹和`.env`文件复制到容器中。这不仅有助于保持较小的集装箱尺寸，还能缓解任何[安全问题](https://securityaffairs.co/wordpress/137371/security/millions-git-folders-exposed-public.html)。接下来，我们将在 Ubuntu 22.04 上构建并运行 Laravel 的容器。

## 在本地构建和运行

现在我们已经定义并描述了我们的`Dockerfile`和`docker-compose.yml`文件。要构建映像，您可以运行以下命令:

```
docker compose build

```

它将在开始时显示以下输出:

![Docker Compose Output](img/0b1aee36f262522562ff6d074f0e5178.png)

它从 DockerHub 中提取所有需要的 Docker 图像层来构建图像。在我的测试中，下载所有的图层几乎花了三分钟。当我使用 Docker 20.10 时，它默认使用 [BuildKit](https://docs.docker.com/build/buildkit/) 。它以以下输出结束:

![Docker Output Using BuildKit](img/d96876e53c714630b8601445358564c9.png)

此时，最好将 composer 依赖项拉入本地，因为它将与 Docker Compose 文件中的卷映射在一起。您可以通过运行以下命令来实现:

```
docker run --rm --interactive --tty \
  --volume $PWD:/app \
  composer install

```

这将在您的机器上本地提取所有依赖项，稍后，当卷被映射时，这些依赖项也将在容器上可用。请记住，如果没有使用 Docker Compose 或者整个文件夹没有映射为一个卷，则不需要这样做。它将产生以下输出:

![If Docker Compose Is Not Used](img/88c2df7f03c0393de0c4989de95ff309.png)

Then, to run the container, we can execute:

```
docker compose up

```

根据您的 Docker 和 Docker Compose 版本，如果您使用的是旧版本的 Docker Compose，也可以是`docker-compose up`。它将产生以下结果:

![Docker-Compose Up Command Being Used](img/aee5374ad454251f93d1f0171d456719.png)

这将启动服务和它们的相关容器。一旦两个容器都打开了，你可以在 [`http://localhost:8082`](http://localhost:8082) 查看浏览器上的应用程序，它将显示默认的 Laravel 页面:

![Default Laravel Page](img/79152173470909a267f71e925aa45406.png)

我在`Documentation`之外加了一个`-`，看看更新效果如何，效果不错。要登录并查看用户，您可以导航到`[http://localhost:8082/admin/login](http://localhost:8082/admin/login)`，使用`[[email protected]](/cdn-cgi/l/email-protection)`作为用户，使用`testpass`作为密码，点击**登录**:

![Laravel Login](img/1ffd072897251041f84a9d4d70a1a083.png)

这在不运行任何`artisan migrate`的情况下是可能的，因为初始数据已经用文件`./db/init.sql`加载，该文件有两个用户。登录后点击左侧的**用户**即可看到:

![List Of Users](img/0447c91759dd945cd2ae8f9198c152fe.png)

你有它！Laravel 9.x 在 Ubuntu 22.04 上运行 MySQL，带有 Docker 和 Docker Compose。这个 Docker 形象除了设置简单，还有制作的舞台，比 Laravel Sail 优化很多。

## 结论

在本指南中，您了解了 Docker 是什么。然后你了解了一个简单的 Laravel 9.x app，用 Backpack 展示用户。之后，您将了解 Docker 文件和 Docker Compose 文件，以便在 Ubuntu v22.04 上使用 Docker 运行此应用程序。在此过程中，您还了解了`dockerignore`以及如何为 Laravel 构建和运行 Docker 容器，这些容器适用于本地开发，也支持生产模式。

编码快乐！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)