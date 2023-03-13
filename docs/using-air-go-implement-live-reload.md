# 使用 Air 和 Go 实现实时重装

> 原文：<https://blog.logrocket.com/using-air-go-implement-live-reload/>

Live reload 是一个有用的特性，它允许开发人员在处理代码时实时看到他们的更改。这可以节省大量时间，因为它消除了每次进行更改时手动停止和重新启动服务器的需要。

有几个用于 Go 的实时重载框架，当您修改源代码时，可以使用它们自动重载您的应用程序。这篇博文将介绍如何使用 Air 库在 Go 中实现实时重载。

向前跳:

在我们进入如何在 Go 中实现 live reload 之前，我们先来讨论一下为什么你可能会想在第一时间使用 live reload。

## 为什么要在 Go 中使用 live reload？

实时重载的主要[优势之一是它显著地加速了你的开发工作流程。](https://blog.logrocket.com/complete-guide-full-stack-live-reload/)

您可以简单地保存您的更改并让您的应用程序自己重新加载，而不是每次进行更改时都停止并重新启动您的应用程序。这可以节省您的时间和挫折，特别是如果您正在处理一个大型应用程序或一个需要很长时间才能启动的应用程序。

实时重载对于调试也很有用。通过在做出更改时自动重新加载应用程序，您可以更快地识别和解决问题。

这在处理难以调试的应用程序时尤其有用，例如那些具有复杂依赖关系或启动时间长的应用程序。

## Go 中实时重装与热重装

在 Go 中，“实时重新加载”是指自动重新加载和更新应用程序，而无需手动停止和重启。

“热重新加载”是一个类似的概念，但具体指的是在应用程序仍在运行时更新其代码的能力，而不会中断其当前状态或任何正在进行的进程。

实时重新加载和热重新加载都有助于开发，因为它们允许更快地迭代和测试代码更改，而无需手动停止和重新启动应用程序。

然而，根据应用程序的复杂性，热重装可能不可行，或者可能更难实现。

Go 和 Gin 项目没有对实时重新加载功能的本机支持，所以让我们看看如何设置 Air 库以在 Go 和 Gin 项目中实现实时重新加载。

## Go 应用程序的 Air 库简介

Air 是一个正在开发中的 Go 应用程序的实时重载命令行实用程序。它是一个命令行工具，允许 Go 应用程序实时重新加载。

除了 Air，其他流行的 Go 的 live reload 框架还有 Fresh 和 Realize。它们都以相似的方式工作——当它们检测到源代码发生变化时，会自动重新构建并重新启动应用程序。

[Fresh 是另一个命令行工具](https://github.com/gravityblast/fresh)，旨在用于任何 Go web 服务器，易于集成到您的构建流程中。

Realize 是一个基于 Go 的构建系统，为 Go 应用提供实时重载功能。

因为这些选项都以非常相似的方式工作，所以决定在您的 Go 项目中使用哪个框架主要是一个偏好问题。尝试每一种方法，看看什么对你来说最简单或最直观。

## Go 和 Gin 项目中使用空气的指南

通过遵循这五个步骤，您可以在 Go 和 Gin 项目中轻松开始使用 Air。首先，通过运行以下命令安装 Air CLI 工具:

```
go get -u github.com/cosmtrek/air
```

接下来，通过运行以下命令安装 [Gin web 框架](https://blog.logrocket.com/5-top-go-web-frameworks/#gin):

```
go get -u github.com/gin-gonic/gin
```

创建一个`main.go`文件，并在 Gin 中声明一个简单的`Hello, World!`端点:

```
package main

import (
    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    r.GET("/", func(c *gin.Context) {
        c.String(200, "Hello, World!")
    })
    r.Run()
}
```

在项目的根目录下创建一个`.air.toml`文件。该文件将用于配置 Air。您可以使用以下配置选项:

```
root = "."
testdata_dir = "testdata"
tmp_dir = "tmp"

[build]
  args_bin = []
  bin = "./tmp/main"
  cmd = "go build -o ./tmp/main ."
  delay = 1000
  exclude_dir = ["assets", "tmp", "vendor", "testdata"]
  exclude_file = []
  exclude_regex = ["_test.go"]
  exclude_unchanged = false
  follow_symlink = false
  full_bin = ""
  include_dir = []
  include_ext = ["go", "tpl", "tmpl", "html"]
  kill_delay = "0s"
  log = "build-errors.log"
  send_interrupt = false
  stop_on_error = true

[color]
  app = ""
  build = "yellow"
  main = "magenta"
  runner = "green"
  watcher = "cyan"

[log]
  time = false

[misc]
  clean_on_exit = false

[screen]
  clear_on_rebuild = false
```

最后，使用 Air 从终端运行 Gin 应用程序:

```
air
```

这将启动服务器并观察模板和静态资产的变化。当检测到更改时，服务器将自动重新加载以反映这些更改。

## 码头集装箱用空气指南

在 Go 应用程序中使用带有 [Docker 容器的 live reload 的主要好处之一是，它允许您快速迭代代码，而无需每次都停止和启动容器。](https://blog.logrocket.com/dockerizing-go-application/)

这在开发和测试容器内的应用程序时特别有用，因为它可以帮助您更快更容易地识别和修复问题。

要将 live reload 库与 Docker 一起使用，您需要将本地源代码目录挂载为 Docker 容器中的一个卷。这将允许 Air 库监视对代码的更改，并在必要时自动重新加载服务器。

首先，创建一个`Dockerfile`，其中包含安装和运行 Air 的说明:

```
FROM golang:latest

# Set the current working directory inside the container
WORKDIR /app

# Copy go.mod and go.sum files to the workspace
COPY go.mod go.sum ./

# Download all dependencies
RUN go mod download

# Copy the source from the current directory to the workspace
COPY . .

# Build the Go app
RUN go build -o main .

# Expose port 8080 to the outside world
EXPOSE 8080

# Command to run the executable
CMD ["air"]
```

接下来，在项目的根目录下创建一个包含以下内容的`.air.toml`文件:

```
root = "."
testdata_dir = "testdata"
tmp_dir = "tmp"

[build]
  args_bin = []
  bin = "./tmp/main"
  cmd = "go build -o ./tmp/main ."
  delay = 1000
  exclude_dir = ["assets", "tmp", "vendor", "testdata"]
  exclude_file = []
  exclude_regex = ["_test.go"]
  exclude_unchanged = false
  follow_symlink = false
  full_bin = ""
  include_dir = []
  include_ext = ["go", "tpl", "tmpl", "html"]
  kill_delay = "0s"
  log = "build-errors.log"
  send_interrupt = false
  stop_on_error = true

[color]
  app = ""
  build = "yellow"
  main = "magenta"
  runner = "green"
  watcher = "cyan"

[log]
  time = false

[misc]
  clean_on_exit = false

[screen]
  clear_on_rebuild = false
```

要构建 Docker 映像，请运行以下命令:

```
docker build -t airy-app .
```

这将创建一个名为`gin-app`的 Docker 映像，您可以用它来运行您的 Gin 服务器。要在 Docker 容器中运行服务器，请使用以下命令:

```
docker run -p 8080:8080 -v $PWD:/app airy-app
```

这将在 Docker 容器中启动 Gin 服务器，并在端口 8080 上公开它。然后，您可以通过 [http://localhost:8080](http://localhost:8080) 访问服务器。

## 摘要

Air 是一个用于 Go 的实时重新加载工具，允许开发人员在每次进行更改时自动重新编译并运行他们的代码。

使用 Air，开发人员可以在他们的 Go 项目中快速实现实时重载，从而在开发过程中节省时间和精力。这对于开发 web 应用程序特别有用，因为它允许开发人员实时看到他们的更改，而不必手动停止和启动服务器。

总的来说，Air 对于希望简化工作流程和提高工作效率的 Go 开发者来说是一个很有价值的工具。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)