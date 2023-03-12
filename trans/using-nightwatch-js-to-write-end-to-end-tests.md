# 使用 Nightwatch.js 编写端到端测试

> 原文：<https://blog.logrocket.com/using-nightwatch-js-to-write-end-to-end-tests/>

在这篇博文中，我将向用户介绍端到端测试，向开发人员介绍 [Nightwatch.js](https://nightwatchjs.org/) ，这是一个流行的端到端测试框架。我们将设置它，解释它是如何工作的，并进一步展示它如何通过与持续集成管道(如 [circleCI](https://circleci.com/) )集成来帮助更快地运送产品。

## 什么是端到端测试？

端到端测试从用户的角度测试应用程序，因此您可以确定应用程序的业务用例得到满足的程度。

这个过程包括使用基于浏览器的自动化测试工具，比如 Nightwatch，以及在浏览器中测试我们的应用程序的 [Cypress](https://www.cypress.io/) 。然后，您可以根据您的测试结果做出断言。

端到端测试包括从端到端测试应用程序的每个特性，也就是从前端组件和构建块到后端特性和功能。它测试它们相互集成的程度，以满足应用程序的目标。

## 守夜人简介

Nightwatch 是用 [Node.js](https://nodejs.org/en/) 编写的端到端测试库，它利用 W3C webdriver API 与 web 浏览器进行交互，给出命令，并根据给出的命令执行断言。

## 为什么是守夜人？

Nightwatch 提供了许多开箱即用的优势，包括:

*   干净的语法
*   内置测试转轮
*   连续累计
*   易于扩展(我使用 Nightwatch 来实现这个特性，因为它允许我编写自己的定制命令和断言来进行测试)

## 使用 Nightwatch 进行端到端测试

webdriver 是一个用于自动化 web 浏览器的通用库，所有浏览器都有特定的 webdriver 服务器实现。Nightwatch 通过一个 restful API 与浏览器 webdriver 服务器进行 HTTP 调用通信。这些协议由 W3C Webdriver 规范定义。

Nightwatch 向 webdriver 服务器发送至少两个请求，以执行命令或断言:

1.  请求使用 CSS 选择器或 X 路径定位元素
2.  请求对给定元素执行实际命令或断言

Nightwatch 的这种方法与一个浏览器交互，并执行自动化测试。

![Nightwatch Requests to Webdriver](img/94e9a49b2d6480c04b723ffca444d0b9.png)

Photo credit by [https://nightwatchjs.org/gettingstarted/](https://nightwatchjs.org/gettingstarted/).

或者，Nightwatch 将使用 [Selenium 服务器](https://www.selenium.dev/downloads/) (Selenium Grid)并行测试我们的浏览器。网格使我们能够用多种浏览器创建会话，并并行测试它们，以确保我们的 web 应用程序与各种浏览器兼容。

## Selenium 服务器是如何工作的？

Selenium 服务器充当 Nightwatch 测试脚本(用 WebDriver API 编写)和浏览器驱动程序(由 WebDriver 协议控制)之间的代理。

服务器将 Nightwatch 中脚本的命令转发给驱动程序，并将驱动程序的响应返回给正在进行断言的脚本。Selenium 服务器可以处理不同语言的多个脚本，并且可以启动和管理不同版本和实现的多个浏览器。

![Multiple Requests and Browsers](img/0b006585105457917873ef007021738e.png)

Photo credit by [https://nightwatchjs.org/gettingstarted/](https://nightwatchjs.org/gettingstarted/).

可以在本地或远程托管我们的 selenium 服务器(通过[酱实验室](https://saucelabs.com/)或[浏览器堆栈](https://www.browserstack.com/)，或者作为外部基础设施，这取决于我们的偏好和使用案例。

## 如何在本地设置守夜人

首先，使用`create-react application
- npx create-react-app my-app`引导一个新的 react 应用程序

现在，安装您选择的浏览器网络驱动程序。对于本文，我们将在 Chrome 和 FireFox 上运行我们的测试，所以让我们安装这两个驱动程序:

```
- npm install geckodriver --save-dev
- npm install chromedriver --save-dev
```

在本地安装 Selenium 服务器:

```
- npm install selenium-server --save-dev
```

现在我们来下载 Selenium 服务器:[Selenium-Server-standalone-4 . 0 . 0-alpha-2 . zip](https://selenium-release.storage.googleapis.com/4.0/selenium-server-standalone-4.0.0-alpha-2.zip)。

解压文件，在项目的根目录下创建一个 **bin** 文件夹，并将`selenium-server-4.0.0-alpha-2.jar`移动到其中。

接下来，[下载 Java](https://www.oracle.com/java/technologies/javase-downloads.html) 。因为 Selenium 服务器是一个 Java 应用程序，所以您需要在您的机器上安装 Java。通过运行`java -version`检查您是否已经安装了它。

现在，让我们安装节点 Selenium 服务器包 [Selenium-server](https://www.npmjs.com/package/selenium-server) 。这个包导出一个路径字符串，该字符串包含之前保存的 Selenium 服务器二进制文件/可执行文件的路径。

在项目的根目录下创建一个`nightwatch.conf.js`文件，然后添加以下配置:

```
module.exports = {
    src_folders: ['e2e'],
    test_settings: {
      default: {
        selenium_port: 4444,
        selenium_host: "localhost",
        silent: true,
        screenshots: {
          enabled: false,
          path: "",
        },
        globals: {
          abortOnAssertionFailure: false,
          waitForConditionPollInterval: 300,
          waitForConditionTimeout: 10000,
          retryAssertionTimeout: 5000,
        },
        selenium : {
          start_process : true,
          server_path: require("selenium-server").path
        }
      },
      selenium: {
        selenium: {
          start_process: true,
          port: 4444,
          server_path: require("selenium-server").path,
          cli_args: {
            "webdriver.chrome.driver": require("chromedriver").path,
            "webdriver.gecko.driver": require("geckodriver").path
          },
        },
        webdriver: {
          start_process: false,
        },
      },
      "chrome": {
        extends: "selenium",
        desiredCapabilities: {
          browserName: "chrome",
          chromeOptions: {
            w3c: false,
          },
        },
      },

      "firefox": {
        extends: "selenium",
        desiredCapabilities: {
          browserName: "firefox",
        },
      },
    },
  }

```

**T2`src_folders:`**

这里，我们指定存储测试的默认路径应该是一个名为`e2e`的文件夹。这个属性是一个数组，也就是说，它可以接受几个实体，我们的测试运行人员会在这些实体中寻找要运行的测试。

让我们深入主要部分。

### 夜视中的测试设置

**T2`test_settings`**

我们在其中定义测试环境的对象。它由一个默认对象组成，如下所示。

**T2`default`**

**test_settings** 下的这个属性定义了其他环境继承的环境。这里，我们将 Selenium 服务器及其端口定义为本地。以下是在上述文件中默认情况下进行的配置:

```
default: {
        selenium_port: 4444,
        selenium_host: "localhost",
        silent: true,
        screenshots: {
          enabled: true,
          path: "tests_output",
        },
        globals: {
          abortOnAssertionFailure: false,
          waitForConditionPollInterval: 300,
          waitForConditionTimeout: 10000,
          retryAssertionTimeout: 5000,
        },
        selenium : {
          start_process : true,
          server_path: require("selenium-server").path
        }
      }

```

让我们进一步解释上面的代码:

1.  `selenium_port`:Selenium 端口号正在接受连接
2.  `selenium_host`:Selenium 服务器接受连接的主机 IP 地址
3.  `screenshots`:该配置允许我们启用截图，并设置测试的默认目录截图，以便在我们使用 save screenshot 命令请求时保存
4.  这些是确保测试灵活性所需的全局配置。让我们突出几个:
    *   `abortOnAssertionFailure`:该控件用于在断言失败时中止测试执行，并跳过其余部分
    *   `waitForConditionTimeout`:这将覆盖`waitFor commands`的默认轮询间隔(500 毫秒)
    *   `waitForConditionTimeout`:覆盖`waitFor`命令的默认超时和默认`waitFor`值
    *   `retryAssertionTimeout`:自动重试失败的断言，直到超时，测试运行程序才会放弃
5.  `selenium`:包含 Selenium 服务器相关配置选项的对象。如果不使用 Selenium，应该设置 webdriver 选项
6.  `server_path`:这是之前下载的 Selenium 驱动被引用的路径；因此需要 selenium_server 节点包。

### Selenium 服务器设置

现在，让我们来看看 Selenium 服务器的设置。

```
selenium: {
        selenium: {
          start_process: true,
          port: 4444,
          server_path: require("selenium-server").path,
          cli_args: {
            "webdriver.chrome.driver": require("chromedriver").path,
            "webdriver.gecko.driver": require("geckodriver").path
          },
        },
        webdriver: {
          start_process: false,
        },
      }

```

要进一步分解它:

1.  `start_process`:决定是否自动管理 selenium 进程
    1.  `Server_path`:这是服务器路径的位置。这里我们使用节点`selenium_server`路径来解析我们的服务器路径
    2.  `Port`:指 Selenium 服务器监听和 Nightwatch 连接的端口
    3.  因为我们想在 Firefox 和 Chrome 中并行运行我们的测试，所以我们决定只使用这两个。
    4.  `Cli_args`: These involve a list of CLI arguments to be passed to the Selenium process. Above, we specify different drivers based on the type of browsers we want to run out tests. This was why we installed the following previously:

        ```
        - npm install geckodriver --save-dev
        - npm install chromedriver --save-dev
        ```

        上面的包已经被设置为获取和运行它们所安装的平台上各自的浏览器驱动程序，这些驱动程序由节点标识(Linux、macOs、Windows)

        **web driver**:web driver 作为子进程在后台运行，自动启动和停止。因为我们使用 Selenium 服务器与我们的 web 驱动程序交互，所以我们禁用了这个配置。

    5.  配置 web 浏览器设置

## 下面的代码包含由 Selenium 服务器管理的 web 浏览器(Firefox 和 Chrome)的单独配置:

![Folder Structure Example](img/679d619d03930a327179cf9722adc739.png)

```
 "chrome": {
        extends: "selenium",
        desiredCapabilities: {
          browserName: "chrome",
          chromeOptions: {
            w3c: false,
          },
        },
      },

      "firefox": {
        extends: "selenium",
        desiredCapabilities: {
          browserName: "firefox",
        },
      },
    },
  }

```

按照这些说明，我们应该有一个类似上面的文件夹结构。

编写端到端测试

## 创建应用程序结构

### 我在 React 中构建了一个演示应用程序，因此您可以看到端到端测试的运行情况。这个演示应用服务器是用一个假的 REST API 构建的，使用了 [JSON 服务器](https://www.npmjs.com/package/json-server)，在[这篇博文](https://github.com/Adekoreday/E2E-Tutorial-LogRocket)的源代码中可以找到。

![Subscription Successful Alert](img/3853220001235717db0ce200eb649470.png)

如果我单击任何一个 subscribe 按钮，它会创建一个对我们的伪 API 的请求来订购新的订阅。如果成功，会弹出警报通知我们。

成功时，警报显示“订阅成功。开火！”

如果失败，警报会显示“目前无法订阅，请重试！”

![Subscription Failure Alert](img/1905da4a06f4b2613118d55b5dedf74a.png)

为 React 应用程序编写端到端测试

### 测试可以在项目的 **E2E 文件夹**中找到。以下是对订阅功能的测试:

这篇文章的目标不是讲授在 Nightwatch 中编写端到端测试的细节，而是我将解释上面的测试，这是一个非常适用的用例。

```
module.exports = {
    "SUBSCRIBE_USER": function(browser) {
     // the aside element css selector
        const alertElement = "div[role=alert]";
        browser
        .url("http://localhost:3005/")
        .waitForElementVisible("body")
        .click('#subscribe2')
        .waitForElementVisible(alertElement)
        .assert.containsText(alertElement, "Subscription successful. Fire on!")
        .saveScreenshot('subscription.png');
    }
}

```

上面的代码在浏览器中导航到 [http://localhost:3005/](http://localhost:3005/) 。

```
 browser
        .url("http://localhost:3005/")

```

在执行任何其他命令或断言之前，`waitForElementVisible`命令会等待 5000 毫秒，直到一个元素在页面上可见。在这种情况下，元素是页面的主体。

```
.waitForElementVisible("body")
```

click 命令使我们能够单击作为参数传递给它的任何 CSS 选择器，因此我们单击了 ID 为`#subscribe2`的**“标准”订阅按钮**。

```
.click('#subscribe2')

```

`assert.containsText`检查它接受的 CSS 选择器是否包含期望的文本。

```
.assert.containsText(alertElement, "Subscription successful. Fire on!")

```

最后，我们将测试的截图保存在 test_output 文件夹中，其名称与 Nightwatch 配置中定义的名称相同。

```
.saveScreenshot('subscription.png');

```

![Screenshot to Save](img/9e6127ef82e2942227277fbbe1e810e0.png)

我们在项目目录中运行以下命令来执行以下操作:

首先，启动假服务器:

然后，启动 React 应用程序:

```
npm run server:start

```

最后，运行 E2E 测试:

```
npm run start

```

我们还可以运行以下命令来完成上述测试中的所有步骤:

```
npm run e2e

```

上面的命令等效于下面的命令。

```
npm run e2e-test
```

请注意，我们同时添加了一个包，因为我们需要同时运行这些命令。

```
concurrently -k --success first \”npm run start\” \”npm run server:start\” \”npm run e2e\””

```

我们还需要确保在任何命令失败或结束时终止所有其他命令进程。

![Terminate Commands](img/13d03463984bfb4e53049b3f3952a46e.png)

将端到端测试链接到 CircleCI

### 既然我们确定我们的测试在本地运行，我们需要确保它在我们的 CI 管道上运行，以加快开发时间。

下面是这个测试的 CircleCI 配置文件。

使用 CircleCI 进行持续集成和开发

## 让我们从为您的项目设置 CircleCI 开始。

完成后，编辑根目录中的`.circleci/config.yml file`,并粘贴以下内容:

我们使用了 CircleCI 便利图像，它为我们提供了浏览器在 CircleCI 上运行的 docker 容器中进行测试所需的稳定 docker 图像。

```
version: 2.1
orbs:
  browser-tools: circleci/[email protected]
jobs:
  build:
    docker:
      - image: 'cimg/node:15.0.1-browsers'
        auth:
          username: $DOCKERHUB_USER
          password: $DOCKERHUB_PASSWORD
    working_directory: ~/repo

    steps:
      - checkout
      - browser-tools/install-browser-tools

      - run: npm install

      # run tests!
      - run: npm test
      - run: npm run e2e-test

```

我们还利用了 CircleCI orbs，它安装了各种浏览器和用于浏览器测试的工具。它包括 Chrome、FireFox、 [ChromeDriver](https://www.npmjs.com/package/chromedriver) 和 [GeckoDriver](https://www.npmjs.com/package/geckodriver) 。

```
      - image: 'cimg/node:15.0.1-browsers'

```

![Npm Run e2e Test](img/2b67a4bd1a4253553abb4a8231981e2a.png)

```
orbs:
  browser-tools: circleci/[email protected]

```

结论

## 在本文中，我们讨论了端到端测试是如何工作的，处理端到端测试的各种方法——比如在各种浏览器中并行测试以确保浏览器兼容性——以及利用 Selenium 服务器网格。

我们还学习了如何在本地设置它，并将其连接到我们的 CI/CD 管道。编码快乐！

200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

## 部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

.

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.