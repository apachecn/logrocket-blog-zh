# 自定义 Node.js。环境文件-日志火箭博客

> 原文：<https://blog.logrocket.com/customizing-node-js-env-files/>

应用程序通常需要部署到许多不同的环境中，包括试运行、测试和生产，而不需要构建特定于环境的构件。

像 12 因素应用程序这样的方法指定应用程序的代码和配置是独立的，但是在部署期间组合起来以适应特定的环境。

环境变量是定义和使用特定于环境的配置值的首选方法之一，因为所有主要的操作系统都支持它们。

它们可以在大多数云提供商平台即服务(PaaS)产品中定义，并且是配置平台的常用方法，如 Docker。

但是，有时在本地配置文件中定义应用程序设置会更方便。

例如，在本地开发和调试应用程序时，尤其是在跨多个功能分支工作时，在一个文件中定义一组环境变量的能力简化了开发人员的体验。

`.env`文件为定义环境变量提供了一个流行的解决方案，特别是在 Node.js 中。例如，像 Heroku 这样的平台使用`.env`文件作为他们推荐的最佳实践的一部分。

在本文中，我们将查看一个通过环境变量配置的简单 Node.js 应用程序，并探索如何用覆盖默认值的新环境变量定制一个`.env`文件。

然后，我们将演示如何使用多个定制的`.env`文件在多个特定于环境的配置之间快速切换。

## 先决条件

GitHub 上提供了我们将要使用的[示例应用程序。为了在本地运行应用程序，](https://github.com/mcasperson/NodeJsEnvFile)[确保安装了 node . js](https://nodejs.org/en/download/)。

或者，您可以[在 CodeSandBox](https://githubbox.com/mcasperson/NodeJsEnvFile) 中运行应用程序。点击链接，在一个新的沙箱中打开 GitHub
源代码，通过一个随机的 URL 进行构建、运行和公开。

## 示例 Node.js 应用程序

下面的代码展示了一个简单的 Node.js web 服务器，它配置了两个环境变量:`PORT`，它定义了 web 服务器监听的端口，以及`MYNAME`，它定义了 HTTP 响应中返回的名称:

```
const http = require('http');
const port = parseInt(process.env.PORT, 10) || 5000;
const name = process.env.MYNAME || "Matthew"
http.createServer((request, response) => {
    response.writeHead(200, {
        'Content-Type': 'text/plain'
    });
    response.write('Hello, ' + name + '!');
    response.end();
}).listen(port);

```

从导入`http`模块开始:

```
const http = require('http');

```

我们可以尝试从`PORT`环境变量中解析一个整数值，或者如果环境变量未定义或者包含整数以外的内容，则默认为端口 5000:

```
const port = parseInt(process.env.PORT, 10) || 5000;

```

HTTP 响应中返回的名称可以在`MYNAME`环境变量中找到，或者默认为字符串`Matthew`:

```
const name = process.env.MYNAME || "Matthew"

```

启动 web 服务器后，它监听我们刚刚定义的端口:

```
http.createServer((request, response) => {
  // ...
}).listen(port);

```

HTTP 响应是在传递给`createServer()`函数的函数中构造的。这里，我们返回上面定义的名称:

```
    response.writeHead(200, {
        'Content-Type': 'text/plain'
    });
    response.write('Hello, ' + name + '!');
    response.end();

```

现在，我们可以使用以下代码运行应用程序:

```
node index.js

```

打开 [http://localhost:5000](http://localhost:5000) 查看`Hello, Matthew!`响应，这意味着我们的 Node.js 应用程序运行正常。

## 为什么我们需要覆盖环境变量？

为什么要首先用环境变量来覆盖像端口号这样的值呢？

这些值已经可以作为命令行参数传入，或者从 JSON 或 YAML 配置文件加载，这比公开简单的名称和值对的环境变量提供了更多的灵活性。

然而，因为 PaaS 解决方案经常需要从环境变量中读取值，所以`PORT`环境变量成为定义应用程序监听哪个端口的事实上的标准。

例如， [Heroku](https://blog.logrocket.com/free-services-deploy-node-js-app/) 、 [AWS](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_nodejs.container.html#nodejs-platform-console) 、 [Azure](https://docs.microsoft.com/en-us/azure/app-service/configure-language-nodejs?pivots=platform-linux#get-port-number) 和 [Google Cloud](https://cloud.google.com/appengine/docs/standard/nodejs/runtime#application_startup) 都需要部署到其服务的 Node.js 应用程序监听`PORT`环境变量定义的端口。

还经常需要使用在方法中明确定义的环境变量，如 12 因素 App 方法。

在帖子的介绍中，我们注意到这种方法需要在代码之外定义应用程序的配置；[十二因素应用配置部分](https://12factor.net/config)更详细地介绍了:

> 十二因素应用程序将配置存储在环境变量中(通常简称为 env vars 或 env)。Env 变量很容易在部署之间改变，而不需要改变任何代码；与配置文件不同，它们被意外签入代码仓库的可能性很小；与定制配置文件或其他配置机制(如 Java 系统属性)不同，它们是与语言和操作系统无关的标准。–[“十二因素应用程序”，亚当·威金斯](https://12factor.net/config)

通过环境变量配置我们的应用程序，我们可以确保我们的代码很容易在各种平台上部署和定制。

## 在 Node.js 中设置环境变量

部署到 PaaS 解决方案的 Node.js 应用程序通常必须假设该应用程序正在监听一个随机的端口。我们可以通过将`PORT`环境变量设置为 5000 以外的值来本地演示这一点。

我们还将设置`MYNAME`环境变量来演示如何定义特定于应用程序的配置。

在 Linux 和 macOS 中，我们可以这样定义这些环境变量:

```
PORT=5001 MYNAME=Jane node src/index.js

```

在 Windows PowerShell 中，我们可以像下面这样定义环境变量:

```
$env:PORT="5001"
$env:MYNAME="Jane"
node src\index.js

```

这个过程对于我们的两个环境变量来说是可管理的，但是如果我们必须在每次应用程序运行时定义几十个环境变量，这个过程很快就变得乏味了。在一个`.env`文件中定义环境变量提供了一个方便的解决方案。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 正在 Node.js 中加载`.env`文件

Node.js 本身并不加载`.env`文件，所以我们必须使用 [`dotenv`包来加载文件，并通过`process.env`来公开值](https://www.npmjs.com/package/dotenv)。

首先在`dependencies`属性下的`package.json`文件中添加`dotenv`作为项目依赖:

```
{
    "name": "node-env-file-demo",
    "version": "1.0.0",
    "description": "Node.js example loading .env files",
    "main": "src/index.js",
    "scripts": {
      "start": "nodemon src/index.js"
    },
    "dependencies": {
        "dotenv": "10.0.0"
    },
    "devDependencies": {
      "nodemon": "1.18.4"
    },
    "keywords": []
  }

```

接下来，下载包含以下内容的依赖项:

```
npm install

```

要加载`.env`文件，我们必须加载`dotenv`包，并在`index.js`文件的开头调用`configure()`函数:

```
require('dotenv').config();
const http = require('http');
const port = parseInt(process.env.PORT, 10) || 5000;
const name = process.env.MYNAME || "Matthew"
http.createServer((request, response) => {
    response.writeHead(200, {
        'Content-Type': 'text/plain'
    });
    response.write('Hello, ' + name + '!');
    response.end();
}).listen(port);

```

我们的应用程序现在准备加载`.env`文件。

## 在 Node.js 中定义自定义环境变量

我们现在可以在项目的根目录下创建`.env`文件。

定制一个`.env`文件包括在每行的开始定义我们想要覆盖的环境变量名，后跟`=`和变量值。在下面的例子中，我们为`PORT`和`MYNAME`环境变量定义了新的值:

```
PORT=5001
MYNAME=Jane

```

现在，使用以下命令运行应用程序:

```
node src/index.js

```

接下来打开[http://localhost:5001](http://localhost:5001)；请注意，端口发生了变化，消息`Hello, Jane!`返回。

## 预加载`dotenv`

在我们的代码中调用`require('dotenv').config()`的另一种方法是使用`-r`或`--require`命令行选项来预加载`dotenv`:

```
node -r dotenv/config src/index.js

```

这种方法将环境变量注入 Node.js 应用程序，否则该应用程序不支持`.env`文件，并且不编辑原始源代码。

## 在 Node.js 中使用多个`.env`文件

为了允许开发人员在开发过程中在许多定制的环境文件之间快速切换，我们可以配置`dotenv`通过`DOTENV_CONFIG_PATH`环境变量从一个定制文件中加载环境变量。

为了演示这一点，在项目的根目录下创建一个名为`.env.development`的文件，其内容如下:

```
PORT=5002
MYNAME=Jill

```

然后，将`DOTENV_CONFIG_PATH`环境变量设置为`.env.development`，并使用我们刚刚介绍的预加载方法运行应用程序。

在 Windows PowerShell 上，使用以下命令运行应用程序:

```
$env:DOTENV_CONFIG_PATH=".env.development"
node -r dotenv/config src/index.js

```

在 Linux 或 macOS 上，使用以下命令运行应用程序:

```
DOTENV_CONFIG_PATH=.env.development node -r dotenv/config src/index.js

```

我们的应用程序现在可以在 [http://localhost:5002](http://localhost:5002) 获得，并将返回`Hello, Jill!`。

## 从 Git 中排除`.env`文件

因为`.env`文件通常包含敏感信息，比如数据库连接字符串，我们不想将这些值提交给 Git 存储库。如果我们这样做了，我们将与任何可以访问应用程序源代码的人共享我们的密码。

环境变量巧妙地避免了这个问题，因为就其本质而言，它们的值没有在文件中定义。然而，我们必须明确地从 Git 存储库中排除`.env`文件，以防止共享它们。

我们可以通过在项目的根目录下创建一个名为`.gitignore`的文件并添加以下代码行来做到这一点:

```
.env*

```

现在，Git 忽略了`.env`文件和任何其他以`.env`开头的文件，比如`.env.development`，这意味着任何敏感信息都不会出现在我们的 Git 存储库中。

## 结论

在一个`.env`文件中包含环境变量是一种定义许多相关配置值的便捷方式，无需将它们作为操作系统的一部分来管理，也无需在应用程序每次运行时在命令行中定义它们。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.