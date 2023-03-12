# 记录 Django 应用程序日志

> 原文：<https://blog.logrocket.com/dockerizing-django-app/>

***编者按:**这篇文章于 2022 年 4 月 8 日更新，为一些关键术语提供了更多信息，解释了 docker 文件中的每个命令，并将 Django 应用程序的设置分解为更清晰的步骤。*

要实现一个 Django 项目，大多数时候你需要一个现成的解决方案，以库或依赖的形式。

这通常不是问题，通常记录在`requirements.txt`文件中，该文件将包含运行项目所需的包或依赖项及其各自版本的列表。

当您试图与另一个希望运行和测试它的人共享整个项目时，麻烦就开始了，因为不幸的是，每次您对库和依赖项进行重大更改时，用户都必须从头开始执行设置。

这就是集装箱化和码头工人的用武之地。Docker 隔离您的应用程序及其依赖项，并确保您的应用程序具有一致的运行时协议，而不管托管您的应用程序的服务器配置如何。

本指南将带您通过 Docker 建立一个 Django 项目，包括:

## Docker 是什么？

Docker 是一个开源工具，管理[容器化应用](https://www.docker.com/resources/what-container)的开发、测试和部署。

它提供操作系统级别的硬件虚拟化，适用于现代架构。这允许开发人员打包和运输软件及其依赖项，以便作为容器分发。

这是一个非常受欢迎的容器化平台，一劳永逸地解决了库和依赖性问题。

但它最大的特点是什么？无论主机或底层基础设施如何，您的容器化应用程序将始终以相同的方式运行。

简而言之，你现在可以将你的软件需要的所有部分打包在一个单元中，称为 Docker 映像，然后将这个映像发送或与任何人共享。只要收件人的机器上安装了 Docker，他们就可以运行或测试您的项目。不知道为什么一个项目可以在一台机器上运行而不能在另一台机器上运行的日子已经一去不复返了。

Docker 还提供了一项名为 [DockerHub](https://hub.docker.com/) 的服务，允许你在其他开发者和更大的社区中共享和管理 Docker 图片——本质上，它是 Docker 图片的“GitHub”。

它与代码库平台有一些相似之处，例如通过 Docker CLI 中包含的 CLI 命令上传和下载图像。

## 为什么应该使用 Docker？

### 远程服务

开发者可以将 Docker 映像从 Docker Hub 拉到任何一台托管其容器的机器上。这意味着您可以随时随地检索 Docker 映像、构建它并运行它的实例。

### 成本效率

Docker 允许您使用一个基础架构来支持多个容器，而不是使用多个虚拟机来运行由虚拟机管理程序管理的虚拟操作系统的相同副本，维护成本可能很高。

### 可量测性

Docker 容器可以有效地扩展应用程序以承受更多的负载，并在请求数量减少时自动减少应用程序的计算负载。

### 安全性

容器是不可变的，这意味着当您更改映像的配置时，您必须重新构建映像并运行新的实例。

## 使用 Docker 的先决条件

*   精通 Django 开发
*   中级带 CLI 和 [bash](https://linuxconfig.org/bash-scripting-tutorial-for-beginners)

## 码头设备

本教程使用 YAML 文件执行 Docker 脚本，并通过 Docker CLI 执行这些文件。本指南将探索在 Ubuntu 机器上设置 Docker。如果你使用的是不同的操作系统，你可以查阅文档来开始使用 [Windows](https://docs.docker.com/docker-for-windows/install/) 和 [macOS](https://docs.docker.com/docker-for-mac/install/) 。

要下载并设置 Docker，请在您的终端上运行以下命令:

```
sudo apt-get update  
sudo apt-get install docker-ce docker-ce-cli containerd.io  

```

## 设置和归档 Django 应用程序

本指南假设您已经精通 Django，所以不会强调 Django 应用程序的结构。如果你是 Django 的新手，这里的是一个帮助你快速上手的教程。

让我们跳到在 Docker 中运行一个基本的 Django REST 框架应用程序并显示默认页面的步骤。就当是 Django 和 Docker 的`Hello, world!`吧。

使用本教程中提供的指南，您可以对您可能有的任何以前或将来的 Django 项目进行文档化，尤其是那些在`requirements.txt`中列出了库的项目。

### 第一步

首先，运行以下命令，然后按照以下步骤操作:

```
django-admin startproject dj_docker_drf

```

*   导航到您的项目文件夹
*   启动一个名为`sample`的应用
*   在`settings.py`的`INSTALLED_APPS`列表中添加`rest_framework`和`sample`

### 第二步

在`views.py`文件中，添加以下返回消息“来自 DJANGO 和 DOCKER 的 HELLO WORLD”的代码片段。

```
from rest_framework.views import APIView  
from django.http import JsonResponse  

class HomeView(APIView):  

 def get(self, request, format=None):
    return JsonResponse({"message":
    'HELLO WORLD FROM DJANGO AND DOCKER'})  

```

### 第三步

连接主 URL 文件和应用程序 URL 文件，以便当用户在浏览器上访问应用程序时，`HomeView`是默认视图。

为了允许从任何服务器或 IP 地址访问 Django 应用程序，请确保将`settings.py`文件中的`ALLOWED_HOSTS`设置为`*`，如下面的代码片段所示:

```
ALLOWED_HOSTS = [‘*’]

```

### 第四步

最后，在您的根项目文件夹中创建一个`requirements.txt`文件，并添加 DRF 库:

```
django-rest-framework==0.1.0  

```

该应用程序现已准备好进行 Dockerized。

## 创建 Docker 文件和 Docker CLI

请注意，Dockerfile 文件名为。这是为了允许 Docker CLI 跟踪它。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在您的项目根目录中，创建一个名为`Dockerfile`的不带扩展名的文件，并将以下代码片段添加到该文件中:

```
# base image  
FROM python:3.8   
# setup environment variable  
ENV DockerHOME=/home/app/webapp  

# set work directory  
RUN mkdir -p $DockerHOME  

# where your code lives  
WORKDIR $DockerHOME  

# set environment variables  
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1  

# install dependencies  
RUN pip install --upgrade pip  

# copy whole project to your docker home directory. 
COPY . $DockerHOME  
# run this command to install all dependencies  
RUN pip install -r requirements.txt  
# port where the Django app runs  
EXPOSE 8000  
# start server  
CMD python manage.py runserver  

```

让我们研究一下 docker 文件和每个命令的作用。

*   `FROM python:3.8`:这将一个 Python 映像安装到 Docker 映像中。这也是将在容器中运行应用程序的 Python 版本
*   `ENV DockerHOME=/home/app/webapp`:这里我们声明工作目录，并将其赋给变量名`DockerHOME`。这将是容器中 Django 应用程序的根目录
*   `RUN mkdir -p $DockerHOME`:创建一个指定路径的目录，该路径分配给图像中的`DockerHOME`变量
*   `WORKDIR $DockerHOME`:这明确地告诉 Docker 将提供的目录设置为应用程序将驻留在容器中的位置
*   `RUN pip install --upgrade pip`:这将更新用于安装应用程序依赖项的`pip`版本
*   `COPY . $DockerHOME`:这会将所有其他必需的文件及其各自的内容复制到 app 文件夹中，该文件夹是容器中应用程序的根目录
*   `RUN pip install -r requirements.txt`:该命令将在`requirements.txt`文件中定义的所有依赖项安装到容器内的应用程序中
*   `EXPOSE 8000`:这个命令释放容器中的端口 8000，Django 应用程序将在这里运行
*   `CMD python manage.py runserver`:该命令启动服务器并运行应用程序

## 在 Docker 中运行应用程序

要运行该应用程序，您需要执行两个步骤:

1.  构建映像:这是使用`build`命令完成的，该命令使用您刚刚创建的`Dockerfile`。要构建映像，运行下面的命令:`docker build . -t docker-django-v0.0`。这个命令应该在 Docker 文件所在的目录中执行。`-t`标志标记图像，以便在运行容器时可以引用它。
2.  运行映像:这是使用`docker run`命令完成的。这将把构建的图像转换成一个运行的容器。要运行应用程序，请执行以下命令:`docker run docker-django-v0.0`

您可以在 localhost:8000 的浏览器中查看您的应用程序。

## 使用 Docker Compose 运行多个容器

有些情况下，您可能希望在 Docker 中运行多个容器，并按照特定的顺序执行它们。

这就是 [Docker Compose](https://docs.docker.com/compose/) 派上用场的地方。

Docker Compose 是一个用于定义和运行任何类型的多容器应用程序的工具。如果您有一个由几个容器组成的应用程序，您将使用 Docker Compose CLI 按照您指定的顺序运行它们。

例如，一个 web 应用程序具有以下组件:

1.  Web 服务器容器，如 [Nginx](https://www.nginx.com/)
2.  承载 Django 应用的应用容器
3.  承载生产数据库的数据库容器，如 PostgreSQL
4.  托管消息代理的消息容器，如 [RabbitMQ](https://www.rabbitmq.com/)

要运行这样的系统，您将在 Docker Compose YAML 文件中声明这些指令。在这里，您可以定义如何构建映像、每个映像可以在哪个端口上访问，以及最重要的是，容器应该执行的顺序(即，哪个容器依赖于另一个容器才能成功运行项目)。

## 通过 Django 应用程序使用 Docker Compose

让我们使用上面演示的场景来探索 Docker Compose:一个带有 PostgreSQL 数据库、RabbitMQ 消息代理和 Nginx 负载平衡器的 Django 应用程序。按照本[指南](https://docs.docker.com/compose/install/)在您的主机操作系统上安装 CLI 工具。

使用 Docker Compose(与 Docker 类似)，需要一个具有特定名称的特定文件。CLI 工具读取该文件，并使用它来启动 Docker 映像并运行它们。

要创建 Docker 合成文件，请创建一个 YAML 文件，并将其命名为`docker-compose.yml`。理想情况下，它应该位于项目的根目录下。

```
version: '3.7'

services: # the different images that will be running as containers
  nginx: # service name

# location of the dockerfile that defines the nginx image. The dockerfile will be used to spin up an image during the build stage.
    build: ./nginx 

# map the machine port 1339 to the container port 80\. Any traffic from 1339 externally will be passed to port 80 of the NGINX container. You can access this container viea localhost:1339
    ports: - 1339:80 

# static storages provisioned since django does not handle static files in production
    volumes:  - static_volume:/home/app/microservice/static 

# will only start if web is up and running
    depends_on: - web 

# restart service when it fails
    restart: "on-failure" 

  web: # service name

#build the image for the web service from the dockerfile in parent directory.  
    build: . 

# command directive passes the parameters to the service and they will be executed by the service. In this example, these are django commands which will be executed in the container where django lives.
    command: sh -c "python manage.py makemigrations &&
                    python manage.py migrate &&
                    gunicorn microservice_sample_app.wsgi:application --bind 0.0.0.0:${APP_PORT}" 

# map data and files from parent directory in host to microservice directory in docker container
    volumes: - .:/microservice 

    - static_volume:/home/app/microservice/static

# file where env variables are stored. Used as best practice so as not to expose secret keys
    env_file: - .env # name of the env file

# name of the image
    image: microservice_app 

# expose the port to other services defined here so that they can access this service via the exposed port. In the case of Django, this is 8000 by default
    expose: 
      - ${APP_PORT} # retrieved from the .env file

    restart: "on-failure"

# cannot start if db service is not up and running
    depends_on: - db

  db: # service name

# image name of the postgres database. during build, this will be pulled from dockerhub and a container spun up from it.
    image: postgres:11-alpine 

    volumes:
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
      - postgres_data:/var/lib/postgresql/data/

# access credentials from the .env file
    environment: 
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_DB=${DB_NAME}
      - PGPORT=${DB_PORT}
      - POSTGRES_USER=${POSTGRES_USER}

    restart: "on-failure"

  rabbitmq:
    image: 
      rabbitmq:3-management-alpine #image to be pulled from dockerhub during building
    container_name: rabbitmq # container name
    volumes: # assign static storage for rabbitmq to run
      rabbitmq: - ./.docker/rabbitmq/etc/:/etc/rabbitmq/
        - ./.docker/rabbitmq/data/:/var/lib/rabbitmq/
    rabbitmq_logs:  - ./.docker/rabbitmq/logs/:/var/log/rabbitmq/
    environment: # environment variables from the referenced .env file
      RABBITMQ_ERLANG_COOKIE: ${RABBITMQ_ERLANG_COOKIE}
         # auth cretendials
      RABBITMQ_DEFAULT_USER: ${RABBITMQ_DEFAULT_USER} 
      RABBITMQ_DEFAULT_PASS: ${RABBITMQ_DEFAULT_PASS}
    ports: # map external ports to this specific container's internal ports
      - 5672:5672
      - 15672:15672

# can only start if web service is running
    depends_on: 
      - web

volumes:
  postgres_data:
  static_volume:
  rabbitmq:
  rabbitmq_logs:

```

Docker Compose 的一个亮点是`depends_on`指令。从上面的脚本中，我们可以推断出:

*   Nginx 依赖于 web
*   Web 依赖于数据库
*   RabbitMQ 依赖网络

有了这个设置，DB 是我们需要启动的第一个服务，然后是 web，然后是 RabbitMQ，最后是 Nginx。

当您决定终止环境并停止正在运行的容器时，顺序将相反——也就是说，Nginx 将首先运行，DB 将最后运行。

## 构建和运行 Docker 编写脚本

就像 Docker 脚本一样，Docker Compose 脚本也有类似的结构，它有`build`和`run`命令。

`build`命令将按照依赖层次的顺序在`docker-compose.yml`中构建`services`下定义的所有图像。

幸运的是，还有一个结合了`build`和`run`的命令——这叫做`up`。要运行该命令，请在此处执行该命令:

```
 docker-compose up

```

您也可以添加`--build`标志。如果您以前运行过该命令，并且想要构建新的映像，这将非常有用。

```
docker-compose up --build

```

使用完容器后，您可能希望将它们全部关闭，并删除它们正在使用的任何静态存储，比如 PostgreSQL 静态卷。为此，请运行以下命令:

```
docker-compose down -V

```

`-V`标志代表卷。这确保了容器和附加卷已关闭。

跟随官方[文档](https://docs.docker.com/compose/reference/)了解更多关于各种 Docker 编写命令及其用法。

## Django 应用程序中的支持文件

上面的脚本中引用了一些文件，使文件不那么庞大，从而使代码管理更容易。这些文件包括`.env`文件、Nginx 的 Dockerfile 文件和配置文件。以下是每种方法的示例:

### `.env`文件

这个文件的主要目的是存储变量，比如密钥和凭证。环境变量是当前用户环境的一组键值对。这是一种安全的编码实践，可以确保您的个人密钥不会暴露。

```
#Django
SECRET_KEY="my_secret_key"
DEBUG=1
ALLOWED_HOSTS=localhost 127.0.0.1 0.0.0.0 [::1] *

# database access credentials
ENGINE=django.db.backends.postgresql
DB_NAME=testdb
POSTGRES_USER=testuser
POSTGRES_PASSWORD=testpassword
DB_HOST=db
DB_PORT=5432
APP_PORT=8000
#superuser details
DJANGO_SU_NAME=test
[email protected]
DJANGO_SU_PASSWORD=mypass123
#rabbitmq
RABBITMQ_ERLANG_COOKIE: test_cookie
RABBITMQ_DEFAULT_USER: default_user
RABBITMQ_DEFAULT_PASS: sample_password

```

### Nginx Dockerfile 文件

它位于根目录下的一个`nginx`文件夹中。它主要包含两个指令:从 Dockerhub 拉取的图像名称；和配置文件的位置。

创建一个名为`nginx`的文件夹。在这个文件夹中，创建另一个`Dockerfile`，并添加下面的代码片段:

```
FROM nginx:1.19.0-alpine

RUN rm /etc/nginx/conf.d/default.conf
COPY nginx.conf /etc/nginx/conf.d

```

### Nginx 配置文件

这是编写 Nginx 配置逻辑的地方。这个文件应该与 Nginx Dockerfile 文件位于同一个文件夹中——`Nginx`文件夹。

这个配置文件规定了 Nginx 容器的行为方式。下面是一个样本脚本，它保存在一个名为`nginx.conf`的文件中。

```
upstream microservice { # name of our web image
    server web:8000; # default django port
}

server {

    listen 80; # default external port. Anything coming from port 80 will go through NGINX

    location / {
        proxy_pass http://microservice_app;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_redirect off;
    }
    location /static/ {
        alias /home/app/microservice/static/; # where our static files are hosted
    }

}

```

## 结论

Docker 对于后端开发人员来说是一个方便的工具。本教程中的 Docker 提示和指南对于任何组织中的 DevOps 和全栈开发人员职位都至关重要。

本教程演示了如何在 Django 应用程序中设置 Docker，构建 Docker 映像，并将映像实例作为容器运行。

我们还讨论了如何使用`docker compose`命令，通过`Docker-compose.yml`文件以特定的顺序构建和运行多个容器。

如果您想了解更多关于 Docker 的知识并将其集成到您的应用程序中，这个[文档](http://docs.docker.com)是一个很好的资源。您还可以在这里探索如何将 Docker 与其他工具[集成。](https://blog.logrocket.com/tag/docker)

如果你也有兴趣在 Django 中磨练或建立你的技能，Django 文档是一个有用的资源。

感谢阅读！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)