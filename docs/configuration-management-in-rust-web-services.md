# Rust web 服务中的配置管理

> 原文：<https://blog.logrocket.com/configuration-management-in-rust-web-services/>

对于任何重要的 web 服务来说，操作配置值的能力是必不可少的。这可以从通过作业的 cron 表达式设置数据库的主机和凭证，到定义应用程序应该启动的实际端口，非常简单。所有这些值都可能发生变化，并且每次都经历代码更改和部署的过程是非常低效的。

因此，拥有外部配置文件是很有用的，这些文件在启动时加载，并为当前运行设置这些值。这样，您只需更改配置文件中的值并重新启动服务。如果您的配置有一个奇特的重载机制，您甚至可以不重启。

在本教程中，我们将介绍 Rust web 应用程序中配置管理的基础知识。我们将使用 [warp](https://github.com/seanmonstar/warp) 构建一个示例应用程序来创建一个 web 服务器，但是这些概念同样适用于任何其他构建 web 应用程序的方法。

该演示将展示以下功能。

*   分级配置
*   环境变量覆盖
*   嵌套数据结构
*   多种文件格式
*   手动超越控制
*   对配置的全局和本地访问

而且我们甚至不用流汗，因为神奇的 [config-rs](https://github.com/mehcode/config-rs) crate 已经提供了这些功能和更多开箱即用的功能，只需要一点必要的配置。

我们开始吧！

## 设置

接下来，您只需要一个最新的 Rust 安装(1.39+)和一个发送 HTTP 请求的工具，比如 cURL。

首先，创建一个新的 Rust 项目:

```
cargo new rust-config-example
cd rust-config-example

```

接下来，编辑`Cargo.toml`文件并添加您需要的依赖项。

```
[dependencies]
tokio = { version = "0.2", features = ["macros", "rt-threaded"] }
warp = "0.2"
config = "0.10"
serde = {version = "1.0", features = ["derive"] }
lazy_static = "1.4"

```

web 服务本身将用 warp 编写，warp 在内部使用 tokio。为了管理我们的配置，我们将使用神奇的 [config-rs](https://github.com/mehcode/config-rs) 机箱。`lazy_static`箱将使我们能够将配置作为共享全局进行惰性初始化，这是我们将演示的通过应用程序传播配置的两种方式之一。

## 基本配置设置

让我们从定义我们想要配置的值开始。`config`箱支持分层覆盖，这意味着我们可以定义一个默认配置，然后，如果根据环境使用不同的文件，覆盖一些值。

下面是`./config/Default.toml`中的默认配置示例。

```
[server]
port = "8080"
url = "http://localhost:8080"

[log]
level = "info"

[[rules]]
    name = "all"
    rule_set = [
        ">5",
        "<100",
    ]

[[rules]]
    name = "none"
    rule_set = []

```

这里没什么特别的。我们在`server`键上定义了两个属性，一个是日志级别，另一个是一组规则。

如果我们想要覆盖本地开发运行的日志级别，我们可以创建一个文件`./config/Development.toml`，它包含以下内容。

```
[log]
level = "debug"

```

加载这个`Development`配置将设置所有值，除了`log.level`，它将被设置为`debug`而不是`info`。

这很有用，因为它使您能够定义一些在默认配置中不会更改的基本属性，并覆盖在专用配置中更改的属性。

例如，在这种情况下，`Testing`和`Production`配置如下所示。

```
[server]
url = "http://testing.example.org"

[server]
url = "http://example.org"

```

对于每个环境，在`./config`中必须有一个文件。

## 加载配置

创建好这些配置文件后，让我们看看如何将它们加载到 Rust 应用程序中。

在新的 Rust 项目中创建一个`settings.rs`文件。然后，为上面概述的配置值创建数据结构。

```
use config::{Config, ConfigError, Environment, File};

#[derive(Debug, Deserialize, Clone)]
pub struct Log {
    pub level: String,
}

#[derive(Debug, Deserialize, Clone)]
pub struct Server {
    pub port: u16,
    pub url: String,
}

#[derive(Debug, Deserialize, Clone)]
pub struct Rule {
    pub name: String,
    pub rule_set: Vec<String>,
}

#[derive(Debug, Deserialize, Clone)]
pub struct Settings {
    pub server: Server,
    pub rules: Vec<Rule>,
    pub log: Log,
    pub env: ENV,
}

const CONFIG_FILE_PATH: &str = "./config/Default.toml";
const CONFIG_FILE_PREFIX: &str = "./config/";

```

这些结构镜像了`Default.toml`中的字段。如你所见，我们可以使用不同的数据类型，甚至嵌套结构。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们还为加载配置定义了两个常量:默认配置文件和文件夹名称。

但是，我们如何从这些配置文件到填充的结构呢？我们很快就会谈到这一点。

现在，让我们定义出现在`Settings`结构中的`ENV`数据类型。

```
#[derive(Clone, Debug, Deserialize)]
pub enum ENV {
    Development,
    Testing,
    Production,
}

impl fmt::Display for ENV {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            ENV::Development => write!(f, "Development"),
            ENV::Testing => write!(f, "Testing"),
            ENV::Production => write!(f, "Production"),
        }
    }
}

impl From<&str> for ENV {
    fn from(env: &str) -> Self {
        match env {
            "Testing" => ENV::Testing,
            "Production" => ENV::Production,
            _ => ENV::Development,
        }
    }
}

```

这完全是可选的，但我发现它非常有用。它只是一个映射环境字符串的枚举。如果您想根据您运行的环境启用或禁用一些逻辑，那么将它添加到配置中是很好的。你也可以想象有一个巨大的枚举，带有你想要打开或关闭的特性标志。像这样的开关应该少用，但是它们非常方便。

现在让我们加载配置。

```
impl Settings {
    pub fn new() -> Result<Self, ConfigError> {
        let env = std::env::var("RUN_ENV").unwrap_or_else(|_| "Development".into());
        let mut s = Config::new();
        s.set("env", env.clone())?;

        s.merge(File::with_name(CONFIG_FILE_PATH))?;
        s.merge(File::with_name(&format!("{}{}", CONFIG_FILE_PREFIX, env)))?;

        // This makes it so "EA_SERVER__PORT overrides server.port
        s.merge(Environment::with_prefix("ea").separator("__"))?;

        s.try_into()
    }
}

```

首先，我们检查是否设置了一个`RUN_ENV`环境变量(如果没有，我们将默认为`Development`)。这很方便，因为我们会频繁地在本地运行应用程序，但这也意味着如果你不在生产环境中指定环境，它将从`Development`配置开始，这可能不是你想要的。在我们的例子中，简单地在这里失败并抛出一个错误，解释这个 env 变量需要被设置为`Development`、`Testing`或`Production`可能更明智。

接下来，我们创建一个默认的`config::Config`并手动设置`ENV`变量。这是如何在运行时手动设置配置值的另一个例子。

我们使用了`merge`函数，首先是默认的配置文件，然后是基于给定环境选择的文件(例如:`./config/Testing.toml`)。

最后，我们使用`merge`用提供的环境变量覆盖任何已经设置的值。这一点很重要，因为您可能不希望将数据库凭据放在签入的文件中。实现这一点的另一种方法(尽管也不是最安全的方法)是通过环境变量来设置值。

`Environment::with_prefix`选项确保使用任何以`EA`为前缀并与我们的配置路径之一匹配的环境变量。分隔符被设置为`__`，以避免与包含`_`的变量名相关的潜在问题。在这种情况下，`EA_SERVER__PORT`会将`server.port`属性设置为给定值。

仅此而已。调用`Settings::new()`将尝试加载和解析配置，根据所选的环境给出一个完全填充的设置对象。

## 配置 web 应用程序

下一步是将它集成到一个简单的 web 应用程序中。

我们将演示在应用程序中传播这种配置的两种方式。首先，我们将简单地把它作为一个全局变量。我们还会将它直接传递给我们的处理者。

全局变量对于小的应用程序来说是好的，但是一旦事情变得大而复杂，可能会出现耦合问题，您可能会转向一种更明确的方式来发送配置值。

让我们建立一个带有两条路径的 warp web 服务器，上面列出的每一个选项都有一条路径。

```
#[macro_use]
extern crate lazy_static;
use std::convert::Infallible;
use warp::{Filter, Rejection, Reply};

mod settings;

lazy_static! {
    static ref CONFIG: settings::Settings =
        settings::Settings::new().expect("config can be loaded");
}

#[tokio::main]
async fn main() {
    let cfg_route = warp::path("local")
        .and(with_cfg(CONFIG.clone()))
        .and_then(cfg_handler);

    let global_cfg_route = warp::path("global").and_then(global_cfg_handler);

    println!(
        "Server started at localhost:{} and ENV: {}",
        CONFIG.server.port, CONFIG.env
    );

    warp::serve(cfg_route.or(global_cfg_route))
        .run(([0, 0, 0, 0], CONFIG.server.port))
        .await;
}

async fn cfg_handler(cfg: settings::Settings) -> Result<impl Reply, Rejection> {
    Ok(format!(
        "Running on port: {} with url: {}",
        cfg.server.port, cfg.server.url
    ))
}

async fn global_cfg_handler() -> Result<impl Reply, Rejection> {
    Ok(format!(
        "Running with interval: {} and rules: {:?}",
        CONFIG.job.interval, CONFIG.rules
    ))
}

fn with_cfg(
    cfg: settings::Settings,
) -> impl Filter<Extract = (settings::Settings,), Error = Infallible> + Clone {
    warp::any().map(move || cfg.clone())
}

```

哇哦。这么多代码！让我们一步一步来。

最初，我们使用`lazy_static`箱在`settings`中创建一个静态版本的配置。这个静态引用可以在应用程序中的任何地方使用；这是全局变量。

本地变体涉及到代码片段底部的`with_cfg` warp 过滤器，在这里我们允许一条路线被传递给一个`settings::Settings`对象。这样，处理程序显式地获取配置，而不是依赖于可用的全局状态。

在 main 函数中，我们创建了两条路由，并根据配置的端口启动了服务器。正如您在`cfg_handler`和`global_cfg_handler`中看到的，无论哪种方式，访问配置都相当容易。

实际上，您可能不会将整个配置对象发送到世界各地，而是以小块的形式发送。例如，数据库配置可能被传递给数据库池进行初始化，但是数据库池不会关心您的服务器端口。

您可能已经注意到，在全局处理程序中，我们使用了`CONFIG.job.interval`，这是一个我们最初没有定义的键。这是我们将添加到应用程序中的下一个特性:第二个完全独立的配置结构。

## 高级功能

根据应用程序的复杂程度，不仅仅是一组配置文件，而是多组配置文件可能是有意义的。它们可以通过它们处理的领域或者简单的事实来区分，即一些配置是现成的，而一些配置需要由用户来定义。

无论如何，能够使用多个配置文件是很好的，所以让我们看看我们是否能做到这一点。幸运的是，`config`箱的`merge`功能非常强大，基本上可以让我们随心所欲地嵌套和合并。

假设我们想为我们的 cron 作业添加一些`job`配置。我们希望该配置是 JSON，并位于名为`./configjson`的文件夹中。

首先，我们将为每个环境定义配置文件。

`Default.json`:

```
{
    "job": {
        "interval": 1000,
        "enabled": false
    }
}

```

`Development.json`:

```
{
    "job": {
        "interval": 10
    }
}

```

`Testing.json`:

```
{}

```

`Production.json`

```
{
    "job": {
        "interval": 100000,
        "enabled": true
    }
}

```

同样，我们定义了一个默认配置。我们可以改变它，或它的一部分，为不同的环境分层。

接下来，我们将字段添加到`Settings`结构中。

```
#[derive(Debug, Deserialize, Clone)]
pub struct Job {
    pub interval: i64,
    pub enabled: bool,
}

#[derive(Debug, Deserialize, Clone)]
pub struct Settings {
    pub server: Server,
    pub rules: Vec<Rule>,
    pub log: Log,
    pub job: Job,
    pub env: ENV,
}

```

最后，我们将其合并到现有配置之上。

```
const OTHER_CONFIG_FILE_PATH: &str = "./configjson/Default.json";
const OTHER_CONFIG_FILE_PREFIX: &str = "./configjson/";

impl Settings {
    pub fn new() -> Result<Self, ConfigError> {
...
        s.merge(File::with_name(&format!("{}{}", CONFIG_FILE_PREFIX, env)))?;

        s.merge(File::with_name(OTHER_CONFIG_FILE_PATH))?;
        s.merge(File::with_name(&format!(
            "{}{}",
            OTHER_CONFIG_FILE_PREFIX, env
        )))?;
...
    }
}

```

注意合并的顺序。在这种情况下，`configjson`配置将覆盖它之前的任何内容。这对我们的目的很好，因为它定义了新的键。

让我们用`cargo run`和`cURL`试一试它是否有效。

首先，我们将使用默认配置(即，回到`Development`)。

```
cargo run
Server started at localhost:8080 and ENV: Development

curl http://localhost:8080/local
Running on port: 8080 with url: http://localhost:8080

curl http://localhost:8080/global
Running with interval: 10 and rules: [Rule { name: "all", rule_set: [">5", "<100"] }, Rule { name: "none", rule_set: [] }]

```

接下来，我们将显式设置`Production`。

```
RUN_ENV=Production cargo run
Server started at localhost:8080 and ENV: Production

curl http://localhost:8080/local
Running on port: 8080 with url: http://example.org

curl http://localhost:8080/global
Running with interval: 100000 and rules: [Rule { name: "all", rule_set: [">5", "<100"] }, Rule { name: "none", rule_set: [] }]

```

最后，让我们显式设置一个环境变量。

```
RUN_ENV=Testing EA_SERVER__PORT=9090 cargo run
Server started at localhost:9090 and ENV: Testing

```

太棒了——它完全按照我们的计划运行。我们可以使用选择的环境和手动使用环境变量来覆盖。两个配置文件都工作，我们的值设置正确。

你可以在 [GitHub](https://github.com/zupzup/rust-web-configuration-example) 上找到完整的示例代码。

## 结论

在任何重要的 web 应用程序中，配置管理都是一个核心问题，Rust 生态系统提供了您需要的一切，甚至更多。

我之前亲身使用过无数语言和框架的配置系统，不得不说，`config`板条箱钉死了这一点。基础知识易于掌握和快速设置，板条箱在文件格式和数据结构方面提供了强大的扩展选项和极大的灵活性。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。