# 用 Viper - LogRocket 博客处理 Go 配置

> 原文：<https://blog.logrocket.com/handling-go-configuration-viper/>

Go 有很多可以为你的应用处理配置的包。但是 [Viper](https://github.com/spf13/viper) 是最受欢迎和最完整的配置解决方案之一，可以帮助您轻松配置您的应用程序。

## 毒蛇是什么？

Viper 使你的应用程序符合[十二因素应用程序](https://12factor.net/)清单，这是一种构建 SaaS 应用程序的方法。这份清单最初由 Heroku 起草，通过使用声明式格式来设置自动化，有助于使您的应用程序更具可移植性和弹性。

这有助于代码库中的新开发人员轻松快速地入门，同时也最小化了生产和开发之间的差异——所有这些都有助于持续部署。

Viper 目前支持:

*   为应用程序设置默认值
*   从 JSON、TOML、YAML、HCL、`.env`文件和 Java 属性配置文件中读取
*   从环境变量中读取—它有一个从配置文件中读取的优先顺序:
    *   对`Set`的显式调用
    *   旗
    *   `.env`文件
    *   配置
    *   键/值存储
    *   系统默认值

## 为什么要用 Viper？

理想情况下，Viper 使我们能够花更少的时间配置应用程序，而花更多的时间实际构建我们的应用程序。Viper 通过确保我们可以轻松设置配置来实现这一点。

在很多情况下，我们需要为应用程序设置默认值，或者从不同的文件类型加载配置变量，这就是使用 Viper 非常有用的地方。它甚至可以实时读取配置变量，使用标志，并使您能够编组和解组。

Viper 的用例非常多，当与 Viper 一起使用时，用 Go 构建应用程序变得更加易于管理。在本文中，您将学习如何使用 Viper，并将其应用到这些用例中。

## 安装 Viper

安装 Viper 类似于在 Go 中安装任何包。第一步是初始化 Go mod 文件。最好的方法是用`git init`初始化文件夹。

接下来，使用`git remote add origin ORIGIN_URL`设置 git 原点，然后使用`go mod init`初始化项目。它将创建一个`go.mod`文件。

将任何包安装到项目中所需要的就是这个`go.mod`文件。要安装 Viper，请在终端中运行以下命令:

```
go get github.com/spf13/viper

```

## 在 Go 中使用 Viper

### 从`.env`文件中读取

让我们从一个`.env`文件加载环境变量。在项目的根目录下创建`.env`文件，并在那里定义环境变量。例如，假设您有三个存储数据库地址、端口和 API 秘密的变量。该文件将类似于如下所示的文件:

```
PORT=3000
MONGO_URI=mongodb+srv://nemo0:[email protected]/myFirstDatabase?retryWrites=true&w=majority
API_SECRET=Y1VFGSea2EPa6v3gFY84swUJbZCHY8WEzKfdq6uPUyRjYR

```

要在您的`main.go`文件中使用这些`.env`变量，您必须让 Viper 知道您的`.env`文件的路径和文件名。如果你的 Go 文件和`.env`文件在正确的位置，你可以用 Viper 初始化`.env`文件，如下所示:

```
viper.SetConfigFile(".env")

```

要读取`.env`文件，您必须添加以下代码:

```
viper.ReadInConfig()

```

一旦在设置配置文件后添加了上面的行，就可以用`viper.Get("KEY")`读取环境值，其中`KEY`是变量名。例如，`viper.Get("PORT")`将从`.env`文件中读取`PORT`。

以上示例的完整代码如下所示:

```
package main

import (
    "fmt"

    "github.com/spf13/viper"
)

func main() {
    viper.SetConfigFile(".env")
    viper.ReadInConfig()

    fmt.Println(viper.Get("PORT"))
}

```

运行这段代码将在控制台中输出`3000`。

但是，正如我们所知，Viper 不仅限于`.env`文件。让我们更进一步，从 JSON 文件中读取配置。

### 用 Viper 从 JSON 文件中读取值

假设您在项目根目录的一个`configs`文件夹中有一个`config.json`文件。要从该文件中读取数据，您可以编写:

```
    viper.AddConfigPath("./configs")
    viper.SetConfigName("config") // Register config file name (no extension)
    viper.SetConfigType("json")   // Look for specific type
    viper.ReadInConfig()

    port := viper.Get("prod.port")

    fmt.Println(port)

```

`viper.AddConfigPath("./configs")`行设置配置文件路径。您可以通过添加多个`AddConfigPath`方法来指定多个路径，Viper 将按照您提供的顺序读取它们。

`viper.SetConfigName("config")`注册配置文件。在大多数情况下，不要在这里添加扩展名。`viper.SetConfigType("json")`会告诉 Viper 从 JSON 文件只读，是可选字段。最后，在添加了`viper.ReadInConfig()`之后，你就可以从配置文件中读取数据了。

如果您有一个如下所示的配置 JSON 文件，您可以使用`viper.Get("prod.port")`读取`port`变量，如上面的代码示例所示。

```
{
  "prod": {
    "port": "3000"
  }
}

```

### 在 Viper 中使用`WatchConfig()`

Viper 让您能够通过`WatchConfig()`方法实时读取配置文件中的更改。这里有一个用 [fsnotify](https://github.com/fsnotify/fsnotify) 包实现的简单例子，这是一个跨平台的系统通知包。

```
viper.WatchConfig()
viper.OnConfigChange(func(e fsnotify.Event) {
    fmt.Println("Config file changed:", e.Name)
})

```

`WatchConfig()`方法将在配置文件中寻找变化，而`onConfigChange`是一个可选的方法，将在每次文件变化时运行。

## 在 Viper 中使用标志

有些情况下，您可能不想在 CLI 中重复键入敏感信息。毒蛇可以帮忙！您可以将敏感信息保存在您的`.env`或任何其他配置文件中，从那里读取它，并将其传递给 CLI。

有一个名为 Cobra 的包，它使我们能够创建强大的 CLI 应用程序，并帮助我们设置标志值。

这里有一个例子:

```
package main

import (
    "fmt"

    "github.com/spf13/cobra"
    "github.com/spf13/viper"
)

func main() {
    command := &cobra.Command{
        Run: func(c *cobra.Command, args []string) {
            fmt.Println(viper.GetString("Flag"))
        },
    }

    viper.SetDefault("Flag", "Flag Value")

    command.Execute()
}

```

这里，除了使用`fmt`之外，我们还使用了两个包。第一个包是`cobra`，它创建一个 CLI 命令，第二个包是`viper`，它设置默认值。

上例中的`command`变量保存了一个在 CLI 上运行并打印标志值的基本函数。使用`viper.SetDefault("Flag", "Flag Value")`设置标志的默认值。

我们使用`command.Execute()`方法运行 CLI 应用程序。运行这个文件将在终端中输出`Flag Value`,因为它被设置为标志的默认值。您也可以从单独的配置文件中读取并将值绑定到一个标志。

### 用 Viper 进行封送和解封

Viper 可以帮助你用[编组](https://github.com/spf13/viper#marshalling-to-string)和[解组](https://github.com/spf13/viper#unmarshaling)值。封送是将字符串转换成 JSON 的过程，而解封则相反。Viper 给出了`Marshal`和`Unmarshal`两种方法来完成任务。

## 结论

在本文中，我们讨论了使用 Viper 和 Go 的用例。您已经学习了如何安装和设置 Viper，我们还讨论了常见的用例，比如从不同的文件类型加载环境变量。我们还介绍了如何在 Go 中使用`WatchConfig`方法和标志。

Viper 有比我在本文中提到的更多的用例。你可以访问 [GitHub repo](https://github.com/spf13/viper) 来了解 Viper 提供的所有特性。使用 Viper 可以帮助您以更健壮的方式设置应用程序。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)