# Go - LogRocket 博客的 5 个结构化日志包

> 原文：<https://blog.logrocket.com/5-structured-logging-packages-for-go/>

从表面上看，日志记录似乎是一项非常简单的任务，只需要向控制台或文件中写入一条消息。但是，当您遵循日志记录最佳实践时，您必须考虑日志级别、构建日志、记录到不同的位置、向日志添加适量的上下文等等。综合起来，所有这些细节使日志记录成为一项复杂的任务。

结构化日志记录背后的想法是让您的日志条目具有易于处理的一致格式，通常是 JSON，允许您以各种方式过滤日志条目。例如，您可以搜索包含特定用户 ID 或错误消息的日志，或者筛选出与服务相关的条目。当您的日志被结构化后，从其中获得相关的度量也将变得容易，比如计费信息。

### 为什么您可能会考虑结构化日志包

出于各种原因，您应该考虑结构化日志包:

1.  因为 Golang 内置日志库生成非结构化日志，所以跟踪日志既困难又耗时
2.  结构化日志包允许您向日志中添加更多字段，以便查询它们并使调试更容易
3.  它节省了故障排除的时间，因为结构化日志是用 JSON 格式化的，这使得它们更容易阅读、查询和存储

### 当 Go 中内置的日志库足够时

Golang 有一个名为 log 的内置日志库，它带有默认的日志记录器，可以写入标准错误并添加每个记录消息的日期，而无需进行配置。如果您需要代码的快速反馈，日志对于本地开发非常有用。

尽管缺少日志级别(如调试、警告或错误)和对以 JSON 格式输出日志的支持，它还允许您创建定制的日志程序并将日志输出保存到文件中。

在本文中，我们将检查和比较五个使结构化日志记录变得轻而易举的包。我们开始吧！

## 1.活力

Zap 是一个流行的面向 Go 的结构化日志库。由优步开发的 Zap 承诺比其他可比较的日志包，甚至是标准库中的`log`包有更好的性能。

Zap 提供了两个独立的记录器，`Logger`用于性能至关重要的情况，而`SugaredLogger`优先考虑人体工程学和灵活性，同时仍然提供快速。

在下面的例子中，我们使用一个`zap.SugaredLogger`结构的实例在程序执行时记录一条消息，产生一个结构化的 JSON 输出，其中包含日志级别信息、时间戳、文件名、行号和日志消息:

```
package main

import (
    "log"

    "go.uber.org/zap"
)

func main() {
    logger, err := zap.NewProduction()
    if err != nil {
        log.Fatal(err)
    }

    sugar := logger.Sugar()

    sugar.Info("Hello from zap logger")
}

// Output:
// {"level":"info","ts":1639847245.7665887,"caller":"go-logging/main.go:21","msg":"Hello from zap logger"}

```

通过修改编码器配置或从头开始创建自己的配置，您可以自定义希望在记录器中显示的确切字段。例如，您可以通过设置以下配置选项，将`ts`字段更改为`timestamp`，并使用更加人性化的日期格式:

```
func main() {
    loggerConfig := zap.NewProductionConfig()
    loggerConfig.EncoderConfig.TimeKey = "timestamp"
    loggerConfig.EncoderConfig.EncodeTime = zapcore.TimeEncoderOfLayout(time.RFC3339)

    logger, err := loggerConfig.Build()
    if err != nil {
        log.Fatal(err)
    }

    sugar := logger.Sugar()

    sugar.Info("Hello from zap logger")
}

// Output:
// {"level":"info","timestamp":"2021-12-18T18:21:34+01:00","caller":"go-logging/main.go:23","msg":"Hello from zap logger"}

```

如果您需要向日志中添加额外的结构化上下文，您可以使用任何以`w`结尾的`SugaredLogger`方法，比如`Infow`、`Errorw`、`Fatalw`等等。`SugaredLogger`类型还提供了通过其`printf`风格的方法记录模板化消息的能力，包括`Infof`、`Errorf`和`Fatalf`:

```
sugar.Infow("Hello from zap logger",
  "tag", "hello_zap",
  "service", "logger",
)

// Output:
// {"level":"info","timestamp":"2021-12-18T18:50:25+01:00","caller":"go-logging/main.go:23","msg":"Hello from zap logger","tag":"hello_zap","service":"logger"}

```

当登录应用程序中对性能敏感的部分时，您可以随时通过在`SugaredLogger`上调用`DeSugar()`来切换到标准的、更快的`Logger` API。但是，这样做之后，您将只能使用显式类型的字段向日志添加额外的结构化上下文，如下所示:

```
l := sugar.Desugar()

l.Info("Hello from zap logger",
  zap.String("tag", "hello_zap"),
  zap.Int("count", 10),
)

```

## 2.零对数

Zerolog 是一个用于结构化 JSON 日志记录的专用库。Zerolog 旨在使用更简单的 API 优先考虑性能；默认情况下，提供了一个全局记录器，您可以使用它进行简单的日志记录。要访问这个记录器，导入`log`子包，如下所示:

```
package main

import (
    "github.com/rs/zerolog"
    "github.com/rs/zerolog/log"
)

func main() {
    zerolog.SetGlobalLevel(zerolog.InfoLevel)

    log.Error().Msg("Error message")
    log.Warn().Msg("Warning message")
    log.Info().Msg("Info message")
    log.Debug().Msg("Debug message")
    log.Trace().Msg("Trace message")
}

// Output:
// {"level":"error","time":"2021-12-19T17:38:12+01:00","message":"Error message"}
// {"level":"warn","time":"2021-12-19T17:38:12+01:00","message":"Warning message"}
// {"level":"info","time":"2021-12-19T17:38:12+01:00","message":"Info message"}

```

Zerolog 允许七个日志级别，从最不严重的`trace`到最严重的`panic`。您可以使用`SetGlobalLevel()`方法为全局记录器设置您喜欢的日志记录级别。在上面的例子中，记录级别被设置为`info`，因此只有级别大于或等于`info`的`log`事件才会被写入。

Zerolog 还支持上下文日志记录。通过代表一个`log`事件的`zerolog.Event`类型的方法，Zerolog 可以很容易地在每个 JSON 日志中添加额外的字段。

通过`Logger`上的一个 level 方法创建`Event`的实例，如`Error()`，然后由`Msg()`或`Msgf()`完成。在下面的例子中，我们使用这个过程向一个`log`事件添加上下文:

```
log.Info().Str("tag", "a tag").Int("count", 123456).Msg("info message")

// Output:
// {"level":"info","tag":"a tag","count":123456,"time":"2021-12-20T09:01:33+01:00","message":"info message"}

```

记录错误也可以通过在一个`Event`上的一个特殊的`Err()`方法来执行，如果错误不是`nil`，它会在日志消息中添加一个`error`字段。如果您希望将该字段的名称改为除`error`之外的名称，请按如下方式设置`zerolog.ErrorFieldName`属性:

```
err := fmt.Errorf("An error occurred")

log.Error().Err(err).Int("count", 123456).Msg("error message")

// Output:
// {"level":"error","error":"An error occurred","count":123456,"time":"2021-12-20T09:07:08+01:00","message":"error message"}

```

您可以[查看文档](https://github.com/rs/zerolog#error-logging-with-stacktrace)了解更多关于添加堆栈跟踪到您的错误日志的信息。

除了可以通过`log`子包访问的全局记录器之外，您还可以使用自定义设置创建其他记录器实例。这些记录器可以基于全局记录器或通过`zerolog.New()`创建的另一个记录器。

在下面的例子中，我们将服务的名称添加到通过`childLogger`创建的每个`log`事件中，这将有助于在日志聚合服务中过滤来自特定应用程序的`log`事件:

```
chidLogger := log.With().Str("service", "foo").Logger()

chidLogger.Info().Msg("An info message")

// Output:
// {"level":"info","service":"foo","time":"2021-12-20T13:45:03+01:00","message":"An info message"}

```

## 3.洛根太太

Logrus 通过一个与标准库记录器兼容的 API 为 Go 应用程序提供结构化日志记录。如果您已经在使用 [stdlib `log`包](https://pkg.go.dev/log)，但是您需要构建您的日志来扩展您的日志记录过程，那么很容易切换到 Logrus。简单地将`logrus`包别名为`log`，如下面的代码所示:

```
package main

import (
  log "github.com/sirupsen/logrus"
)

func main() {
  log.WithFields(log.Fields{
    "tag": "a tag",
  }).Info("An info message")
}

// Output:
// INFO[0000] An info message                               tag="a tag"

```

与 Zap 和 Zerolog 不同，Logrus 默认情况下不输出 JSON，但是您可以通过`SetFormatter()`方法很容易地改变这一点。您还可以将输出从默认的标准误差更改为任何一个`io.Writer`，比如一个`os.File`。您还可以更改默认的严重级别，范围从`trace`到`panic`:

```
func main() {
    log.SetFormatter(&log.JSONFormatter{})
    log.SetOutput(os.Stdout)
    log.SetLevel(log.InfoLevel)

    log.WithFields(log.Fields{
        "tag": "a tag",
    }).Info("An info message")
}

// Output: {"level":"info","msg":"An info message","tag":"a tag","time":"2021-12-20T14:07:43+01:00"}

```

标准文本和 JSON 格式化程序支持几个选项，您可以根据自己的需要进行配置。如果支持的第三方格式化程序能更好地满足您的需求，您也可以使用它们。

Logrus 使用`WithFields()`方法支持上下文日志记录，如前面的代码片段所示。如果想在日志语句之间重用字段，可以将`WithFields()`的返回值保存在一个变量中。通过该变量进行的后续日志记录调用将输出这些字段:

```
childLogger := log.WithFields(log.Fields{
  "service": "foo-service",
})

childLogger.Info("An info message")
childLogger.Warn("A warning message")

// Output:
// {"level":"info","msg":"An info message","service":"foo-service","time":"2021-12-20T14:18:08+01:00"}
// {"level":"warning","msg":"A warning message","service":"foo-service","time":"2021-12-20T14:18:08+01:00"}

```

虽然 Logrus 在特性方面与列表中的其他选项相比很有竞争力，但是在性能方面却落后于其他选项。在撰写本文时，Logrus 目前处于维护模式，因此它可能不是新项目的最佳选择。然而，这无疑是我将密切关注的一个工具。

## 4.顶点/对数

[apex/log](https://github.com/apex/log) 是一个受 Logrus 启发的用于 Go 应用程序的结构化日志包。作者 TJ Holowaychuk 创建了这个包来简化 Logrus API，并为常见用例提供更多的处理程序。一些默认的处理程序包括`text`、`json`、`cli`、`kinesis`、`graylog`和`elastic search`。要查看默认处理程序的完整列表，您可以浏览[处理程序目录](https://github.com/apex/log/tree/master/handlers)，并且您可以通过满足[日志处理程序](https://pkg.go.dev/github.com/apex/log?utm_source=godoc#Handler)接口来创建自定义处理程序。

以下示例展示了 apex/log 的基本功能。我们将使用内置的 JSON 处理程序来写入标准输出，它可以是任何一个`io.Writer`。apex/log 使用`WithFields()`方法向日志条目添加上下文。您还可以通过`Logger`类型设置一个自定义日志记录器，允许您配置处理程序和日志级别:

```
package main

import (
    "os"

    "github.com/apex/log"
    "github.com/apex/log/handlers/json"
)

func main() {
    log.SetHandler(json.New(os.Stdout))

    entry := log.WithFields(log.Fields{
        "service":  "image-service",
        "type":     "image/png",
        "filename": "porsche-carrera.png",
    })

    entry.Info("upload complete")
    entry.Error("upload failed")
}

// Output:
// {"fields":{"filename":"porsche-carrera.png","service":"image-service","type":"image/png"},"level":"info","timestamp":"2022-01-01T11:48:40.8220231+01:00","message":"upload complete"}
// {"fields":{"filename":"porsche-carrera.png","service":"image-service","type":"image/png"},"level":"error","timestamp":"2022-01-01T11:48:40.8223257+01:00","message":"upload failed"}

```

apex/log 包的设计考虑到了日志集中化。您可以按原样编组和解组来自多个服务的 JSON 日志条目，而不必因为字段名的不同而分别处理每个日志条目。

apex/log 通过将上下文字段放在一个`fields`属性中，而不是像在 Logrus 中那样在 JSON 对象的根级别折叠它们，来促进这个操作。这个简单的改变使得在生产者端和消费者端无缝地利用相同的处理程序成为可能:

```
package main

import (
    "os"

    "github.com/apex/log"
    "github.com/apex/log/handlers/cli"
)

func main() {
    logger := log.Logger{
        Handler: cli.New(os.Stdout),
        Level:   1, // info
    }

    entry := logger.WithFields(log.Fields{
        "service":  "image-service",
        "type":     "image/png",
        "filename": "porsche-carrera.png",
    })

    entry.Debug("uploading...")
    entry.Info("upload complete")
    entry.Error("upload failed")
}

// Output:
// • upload complete           filename=porsche-carrera.png service=image-service type=image/png
// ⨯ upload failed             filename=porsche-carrera.png service=image-service type=image/png

```

## 5.日志 15

Log15 旨在生成人类和机器都易于阅读的日志，从而便于遵循最佳实践。Log15 包使用一个简化的 API，强制您只记录键/值对，其中键必须是字符串，而值可以是您想要的任何类型。它还将其输出格式默认为 [logfmt](https://brandur.org/logfmt) ，但这可以很容易地更改为 JSON:

```
package main

import (
    log "github.com/inconshreveable/log15"
)

func main() {
    srvlog := log.New("service", "image-service")

    srvlog.Info("Image upload was successful", "name", "mercedes-benz.png", "size", 102382)
}

// Output:
// INFO[01-01|13:18:24] Image upload was successful              service=image-service name=mercedes-benz.png size=102382

```

创建新的[记录器](https://pkg.go.dev/github.com/inconshreveable/log15?utm_source=godoc#Logger)时，您可以添加上下文字段，这些字段将包含在记录器生成的每个日志条目中。提供的日志级方法，如`Info()`和`Error()`，也提供了一种简单的方法来在必需的第一个参数(即日志消息)后添加上下文信息。要更改用于写日志的处理程序，在一个`Logger`上调用`SetHandler()`方法。

Log15 提供的处理程序是可组合的，因此您可以组合它们来创建适合您的应用程序的日志记录结构。例如，除了将所有条目记录到标准输出中之外，您还可以将错误和更高级别记录到 JSON 格式的文件中:

```
package main

import (
    log "github.com/inconshreveable/log15"
)

func main() {
    srvlog := log.New("service", "image-service")

    handler := log.MultiHandler(
        log.LvlFilterHandler(log.LvlError, log.Must.FileHandler("image-service.json", log.JsonFormat())),
        log.CallerFileHandler(log.StdoutHandler),
    )

    srvlog.SetHandler(handler)

    srvlog.Info("Image upload was successful")
    srvlog.Error("Image upload failed")
}

// Output:
// INFO[01-01|13:49:29] Image upload was successful              service=image-service caller=main.go:17
// EROR[01-01|13:49:29] Image upload failed                      service=image-service caller=main.go:18

```

`MultiHandler()`方法用于将每个日志条目分派给所有注册的处理程序。

在我们的示例中，`LvlFilterHandler()`将严重性级别为`error`或更高的 JSON 格式的条目写入文件。`CallerFileHandler`向日志输出添加一个`caller`字段，其中包含行号和调用函数的文件。`CallerFileHandler`将`StdoutHandler`换行，以便条目在修改后打印到标准输出。

除了`CallerFileHandler()`之外，还提供了`CallerFuncHandler()`和`CallerStackHandler()`方法，分别用于向每个日志输出添加调用函数名和堆栈跟踪。

如果您需要任何默认处理程序都没有提供的功能，您也可以通过实现[处理程序接口](https://pkg.go.dev/github.com/inconshreveable/log15?utm_source=godoc#Handler)来创建自己的处理程序。

## 性能比较

使用 Zap 库[中的](https://github.com/rs/zerolog#benchmarks)[基准测试套件](https://github.com/uber-go/zap/tree/master/benchmarks)，观察到以下结果:

记录一条消息和十个字段:

| 图书馆 | 时间 | 分配的字节数 | 分配的对象 |
| --- | --- | --- | --- |
| 零对数 | 767 ns/升 | 552 b/上 | 6 分配数量/上限 |
| 嗖:嗖 | 848 ns/op | 704 B/op | 2 个分配/操作 |
| :zap: zap(加糖) | 1363 ns/上 | 1610 b/上 | 20 分配/操作 |
| Logrus | 5661 ns/op | 6092 B/op | 78 分配/操作 |
| 顶点/对数 | 15332 ns/上 | 3832 b/上 | 65 阿拉克/上 |
| 日志 15 | 20657 ns/升 | 5632 B/op | 93 分配/操作 |

用已经有十个上下文字段的记录器记录消息:

| 图书馆 | 时间 | 分配的字节数 | 分配的对象 |
| --- | --- | --- | --- |
| 零对数 | 52 ns/升 | 0 b/上 | 0 分配/上移 |
| 嗖:嗖 | 283 ns/op | 0 B/op | 0 分配/操作 |
| :zap: zap(加糖) | 337 ns/升 | 80b/升 | 2 个专用/上行链路 |
| Logrus | 4309 ns/op | 4564 B/op | 63 分配/操作 |
| 顶点/对数 | 13456 ns/升 | 2898 b/上 | 51 阿拉克/上 |
| 日志 15 | 14179 ns/升 | 2642 B/op | 44 个分配/操作 |

记录没有任何上下文或`printf`样式模板的静态字符串:

| 图书馆 | 时间 | 分配的字节数 | 分配的对象 |
| --- | --- | --- | --- |
| 零对数 | 50 ns/升 | 0 b/上 | 0 分配/上移 |
| 嗖:嗖 | 236 ns/op | 0 B/op | 0 分配/操作 |
| 标准库 | 453 ns/升 | 80b/升 | 2 个专用/上行链路 |
| :zap: zap(加糖) | 337 ns/op | 80 B/op | 2 个分配/操作 |
| Logrus | 1244 ns/升 | 1505 b/上 | 27 个阿罗哈/上 |
| apex/纪录 | 2751 ns/升 | 584 b/上 | 11 分配数量/上限 |
| 日志 15 | 5181 ns/升 | 1592 b/上 | 26 同上 |

如您所见，Zerolog 和 Zap 是撰写本文时性能最好的两种解决方案。为了验证这些结果，您应该在您的机器上使用每个库的最新版本运行基准测试套件。

## 结论

在本文中，我们研究了在 Go 应用程序中实现[结构化日志方法的五个库，包括 Zap、Zerolog、Logrus、apex/log 和 Log15。每个库都提供了 JSON 日志记录、日志级别、记录多个位置的能力等特性，使它们成为适合任何项目的日志记录解决方案。](https://blog.logrocket.com/comparing-go-debugging-tools/)

如果性能是一个决定性因素，那么 Zerolog 或无糖 Zap 不会错。否则，我建议为您的项目选择具有最佳 API 的库。感谢您的阅读，祝您编码愉快！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)