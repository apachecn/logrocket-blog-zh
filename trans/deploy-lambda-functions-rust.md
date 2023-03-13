# 如何在 Rust 中部署 Lambda 函数

> 原文：<https://blog.logrocket.com/deploy-lambda-functions-rust/>

学习如何使用 Rust 编写的 Lambda 函数对于今天的开发人员来说是一项非常有用的技能。AWS Lambda 提供按需计算服务，无需部署或维护长时间运行的服务器，无需安装和升级操作系统或修补安全漏洞。

在本文中，我们将学习如何创建和部署用 Rust 编写的 Lambda 函数。为了方便起见，这个项目的所有代码都可以在我的 GitHub 上找到。

*向前跳转:*

## 冷启动和生锈

Lambda 多年来一直存在的一个问题是冷启动问题。

一个函数不能立即启动，因为它通常需要几秒钟或更长时间来返回一个响应。不同的编程语言有不同的性能特征；像 Java 这样的一些语言会有更长的冷启动时间，因为它们的依赖性，比如 JVM。

另一方面，Rust 具有极小的冷启动，并且已经成为通过避免较大冷启动来寻求显著性能增益的开发人员的编程语言选择。

让我们跳进来。

## 入门指南

首先，我们需要安装带有`[rustup](#https://doc.rust-lang.org/cargo/getting-started/installation.html)`和`[cargo-lambda](https://www.cargo-lambda.info/guide/installation.html)`的`[cargo](https://doc.rust-lang.org/cargo/)`。我将使用[家酿](https://blog.logrocket.com/how-to-set-up-m1-macbook-web-development/)，因为我在 Mac 上，但你可以看看之前的两个链接，在你自己的机器上安装说明。

```
curl https://sh.rustup.rs -sSf | sh
brew tap cargo-lambda/cargo-lambda
brew install cargo-lambda

```

### 用`new`命令生成样板文件

首先，让我们用`[new](https://www.cargo-lambda.info/commands/new.html)`命令创建一个新的 Rust 包。如果你包含了`--http`标志，它会自动生成一个兼容 [Lambda 函数 URL](https://docs.aws.amazon.com/lambda/latest/dg/lambda-urls.html)[的项目。](https://docs.aws.amazon.com/lambda/latest/dg/lambda-urls.html)

```
cargo lambda new --http rustrocket
cd rustrocket

```

这就生成了一个基本框架，开始用 Rust 编写 AWS Lambda 函数。我们的项目结构包含以下文件:

```
.
├── Cargo.toml
└── src
    └── main.rs

```

`src`目录下的`main.rs`保存着我们用 Rust 编写的 Lambda 代码。

我们一会儿就来看看这段代码，但是让我们先来看看唯一的另一个文件，`[Cargo.toml](https://doc.rust-lang.org/cargo/reference/manifest.html)` [。](https://doc.rust-lang.org/cargo/reference/manifest.html)

### 货物清单文件

是一个 Rust 清单文件，包括一个用 TOML 为你的包编写的配置文件。

```
# Cargo.toml

[package]
  name = "rustrocket"
  version = "0.1.0"
  edition = "2021"

[dependencies]
  lambda_http = "0.6.1"
  lambda_runtime = "0.6.1"
  tokio = { version = "1", features = ["macros"] }
  tracing = { version = "0.1", features = ["log"] }
  tracing-subscriber = { version = "0.3", default-features = false, features = ["fmt"] }

```

*   所有`Cargo.toml`文件的第一部分定义了一个包。`name`和`version`字段是唯一需要的信息——在官方`[package](#https://doc.rust-lang.org/cargo/reference/manifest.html#the-package-section)`文档中可以找到其他字段的详细列表
*   第二部分指定了我们的`[dependencies](#https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html)`，以及要安装的版本

对于我们将在本文中创建的示例，我们只需要添加一个额外的依赖项(`serde`)。

Serde 是一个序列化和反序列化 Rust 数据结构的框架。在撰写本文时，当前版本是`1.0.145`,但是您可以使用以下命令安装最新版本:

```
cargo add serde

```

现在我们的`[dependencies]`包括`serde = "1.0.145"`。

## 用`watch`命令启动开发服务器

现在，让我们用`[watch](https://www.cargo-lambda.info/commands/watch.html)`子命令启动一个开发服务器，模拟与 AWS Lambda 控制平面的交互。

```
cargo lambda watch

```

由于模拟器服务器包括对 Lambda 函数 url 的现成支持，您可以打开[localhost:9000/Lambda-URL/rust rocket](http://localhost:9000/lambda-url/rustrocket)来调用您的函数并在浏览器中查看它。

![Invoke your Lambda function and view it in your browser](img/5b011a79cc38265de60359ac40a35287.png)

打开`src`目录下的`main.rs`可以看到这个函数的代码。

```
// src/main.rs

use lambda_http::{run, service_fn, Body, Error, Request, RequestExt, Response};

async fn function_handler(event: Request) -> Result<Response<Body>, Error> {
    let resp = Response::builder()
        .status(200)
        .header("content-type", "text/html")
        .body("Hello AWS Lambda HTTP request".into())
        .map_err(Box::new)?;
    Ok(resp)
}

#[tokio::main]
async fn main() -> Result<(), Error> {
    tracing_subscriber::fmt()
        .with_max_level(tracing::Level::INFO)
        .with_target(false)
        .without_time()
        .init();
    run(service_fn(function_handler)).await
}

```

现在，我们将通过添加一条新消息对`body`中的文本进行更改。由于标题已经设置为`text/html`，我们可以用 HTML 标签包装我们的消息。

我们的终端也显示了一些警告，可以通过以下更改进行修复:

*   将`event`重命名为`_event`
*   从导入中移除未使用的`RequestExt`

```
// src/main.rs

use lambda_http::{run, service_fn, Body, Error, Request, Response};

async fn function_handler(_event: Request) -> Result<Response<Body>, Error> {
    let resp = Response::builder()
        .status(200)
        .header("content-type", "text/html")
        .body("<h2>Hello from LogRocket</h2>".into())
        .map_err(Box::new)?;
    Ok(resp)
}

```

`main()`功能不会改变。返回浏览器查看更改:

![Main function](img/e532414013f98c1904eca12b904d7ce1.png)

### 在我们的项目中包含附加信息

现在，我们有一个可以被调用来接收响应的函数，但是这个 Lambda 不能接受用户的特定输入，我们的项目不能用于部署和调用多个函数。

为此，我们将在名为`bin`的新目录中创建一个新文件`hello.rs`。

```
mkdir src/bin
echo > src/bin/hello.rs

```

目录让我们能够在一个项目中包含多个函数处理程序，并分别调用它们。

我们还必须在我们的`Cargo.toml`文件的底部添加一个包含新处理程序名称的`[bin](https://doc.rust-lang.org/cargo/reference/cargo-targets.html#binaries)`部分，如下所示:

```
# Cargo.toml

[[bin]]
  name = "hello"

```

包含以下代码，将`event`有效负载转换为`Response`，并通过`[Ok()](#https://doc.rust-lang.org/std/result/%5C)`传递给`main()`函数，如下所示:

```
// src/bin/hello.rs

use lambda_runtime::{service_fn, Error, LambdaEvent};
use serde::{Deserialize, Serialize};

#[derive(Deserialize)]
struct Request {
    command: String,
}

#[derive(Serialize)]
struct Response {
    req_id: String,
    msg: String,
}

pub(crate) async fn my_handler(event: LambdaEvent<Request>) -> Result<Response, Error> {
    let command = event.payload.command;
    let resp = Response {
        req_id: event.context.request_id,
        msg: format!("{}", command),
    };
    Ok(resp)
}

#[tokio::main]
async fn main() -> Result<(), Error> {
    tracing_subscriber::fmt()
        .with_max_level(tracing::Level::INFO)
        .without_time()
        .init();
    let func = service_fn(my_handler);
    lambda_runtime::run(func).await?;
    Ok(())
}

```

### 用`invoke`命令发送请求

使用`[invoke](https://www.cargo-lambda.info/commands/invoke.html)`子命令向控制平面仿真器发送请求。

```
cargo lambda invoke hello \
  --data-ascii '{"command": "hello from logrocket"}'

```

您的终端将对此作出回应:

```
{
  "req_id":"64d4b99a-1775-41d2-afc4-fbdb36c4502c",
  "msg":"hello from logrocket"
}

```

## **将项目投入生产**

到目前为止，我们只是在自己的机器上本地运行 Lambda 处理程序。为了在 AWS 上获得这个处理程序，我们需要构建和部署项目的工件。

### 用`build`命令捆绑功能工件

用`[build](https://www.cargo-lambda.info/commands/build.html)`子命令编译你的函数。这产生了可以上传到 AWS Lambda 的工件。

```
cargo lambda build

```

### 创建 IAM 角色

AWS IAM ( [i](https://aws.amazon.com/iam/) [dentity 和](https://aws.amazon.com/iam/) [a](https://aws.amazon.com/iam/) [访问](https://aws.amazon.com/iam/) [m](https://aws.amazon.com/iam/) [管理](https://aws.amazon.com/iam/))是一个用于创建、应用和管理 AWS 资源上的角色和权限的服务。

它的复杂性和功能范围促使像 Amplify 这样的团队围绕 IAM 开发大量的工具资源。这包括带有新抽象的[库和 SDK](https://docs.aws.amazon.com/amplify/latest/userguide/security-iam.html)，其构建的明确目的是简化开发者使用 IAM 和 [Cognito](https://docs.amplify.aws/lib/auth/getting-started/q/platform/js/) 等服务的体验。

在这个例子中，我们只需要创建一个只读角色，我们将把它放在一个名为`rust-role.json`的文件中。

```
echo > rust-role.json

```

我们将使用 AWS CLI 并发送以下角色的 JSON 定义。在`rust-role.json`中包含以下代码:

```
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {
      "Service": "lambda.amazonaws.com"
    },
    "Action": "sts:AssumeRole"
    }
  ]
}

```

要做到这一点，首先安装 [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) ，它提供了一个[扩展的](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/index.html)列表，用于使用 IAM。我们需要的唯一命令是`[create-role](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/create-role.html)`命令，以及两个选项:

*   包含针对`--assume-role-policy-document`选项的 IAM 策略
*   包含`--role-name`选项的名称`rust-role`

```
aws iam create-role \
  --role-name rust-role \
  --assume-role-policy-document file://rust-role.json

```

这将输出以下 JSON 文件:

```
{
  "Role": {
    "Path": "/",
    "RoleName": "rust-role2",
    "RoleId": "AROARZ5VR5ZCOYN4Z7TLJ",
    "Arn": "arn:aws:iam::124397940292:role/rust-role",
    "CreateDate": "2022-09-15T22:15:24+00:00",
    "AssumeRolePolicyDocument": {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Principal": {
            "Service": "lambda.amazonaws.com"
          },
          "Action": "sts:AssumeRole"
        }
      ]
    }
  }
}

```

复制`Arn`的值，在我的例子中是`arn:aws:iam::124397940292:role/rust-role`，并将其包含在`--iam-role`标志的下一个命令中。

### 用`deploy`命令上传到 AWS

使用`[deploy](https://www.cargo-lambda.info/commands/deploy.html)`子命令将您的函数上传到 AWS Lambda。Cargo Lambda 将尝试使用 Lambda 的默认服务角色策略`AWSLambdaBasicExecutionRole`创建一个执行角色。

如果这些命令失败并显示权限错误，那么您将需要通过添加`--iam-role FULL_ROLE_ARN`来包含上一节中的 AWS IAM 角色。

```
cargo lambda deploy --enable-function-url rustrocket
cargo lambda deploy hello

```

如果一切正常，您将获得一个用于`rustrocket`函数的 Arn 和 URL，以及一个用于`hello`函数的 Arn，如下图所示。

```
🔍 function arn: arn:aws:lambda:us-east-1:124397940292:function:rustrocket
🔗 function url: https://bxzpdr7e3cvanutvreecyvlvfu0grvsk.lambda-url.us-east-1.on.aws/

```

打开[函数 URL](https://bxzpdr7e3cvanutvreecyvlvfu0grvsk.lambda-url.us-east-1.on.aws/) 查看您的函数:

![Function URL.](img/961614b0413b723bd0075835b0652f0b.png)

对于`hello`处理程序，我们将再次使用`invoke`命令，但是这次使用一个用于远程函数的`--remote`标志。

```
cargo lambda invoke \
  --remote \
  --data-ascii '{"command": "hello from logrocket"}' \
  arn:aws:lambda:us-east-1:124397940292:function:hello

```

### 替代部署选项

到目前为止，我们只使用了 Cargo Lambda 来处理我们的 Lambda 函数，但是至少有十几种不同的方式来将函数部署到 AWS Lambda！

这些众多的方法在方法上有很大的不同，但可以大致分为三组:

1.  其他开源项目，如 Cargo Lambda，由个人或一群人维护，可能是 AWS 员工自己或 AWS 顾问，他们与希望构建 AWS 的客户一起工作
2.  由希望通过提供更好、更简化的开发者体验来与 AWS 竞争的公司构建的工具
3.  由 AWS 内部团队创建和推动的服务，这些团队构建新产品，寻求改善现有 AWS 开发人员的生活，同时引入新的和经验较少的工程师

关于使用第二类和第三类工具的说明，请参见`aws-lambda-rust-runtime` GitHub 仓库上的[将二进制代码部署到 AWS Lambda](https://github.com/awslabs/aws-lambda-rust-runtime#2-deploying-the-binary-to-aws-lambda) 一节。

## 最后的想法

Rust 在 AWS 的未来是光明的。他们对支持该项目的团队和基金会进行了大量投资。还有各种 AWS 服务现在也开始整合 Rust，包括:

要了解更多关于在 AWS 上运行 Rust 的信息，请查看 Rust 的 AWS SDK。要在 Lambda 上更深入地使用 Rust，请访问 GitHub 上的 [Rust 运行时库](https://github.com/awslabs/aws-lambda-rust-runtime)。

我希望这篇文章对如何在 Lambda 上运行 Rust 是一个有用的介绍，请在下面留下你对自己经历的评论！

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。