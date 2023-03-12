# 5 大 Go web 框架- LogRocket 博客

> 原文：<https://blog.logrocket.com/5-top-go-web-frameworks/>

正在寻找 web 的顶级 Go 框架吗？你来对地方了。

Go 是由 Google 设计的多参数、静态类型和编译的编程语言。它类似于 C 语言，如果你是 C 语言的爱好者，这是一门容易掌握的语言。许多开发人员已经接受了这种语言，因为它的垃圾收集、内存安全和结构化类型系统。

根据 2020 年 Stack Overflow 开发者调查，Go 现在被认为是网站上第五大最受“喜爱”的语言，也是那些还不知道 Go 的开发者最想学习的第三大语言。

Go 主要用于 web 应用程序，这就是为什么我们将查看五大 Go web 框架及其特性，以了解哪一个最适合您自己的开发。

在本帖中，我们将回顾使用 Go 的原因，使用 Go 框架的利弊，以及当前五个顶级 Go 框架，包括:

现在，让我们开始吧。

## 为什么要用 Go？

在回顾五大围棋框架之前，围棋真正的用途是什么？除了构建一般的 web 应用程序之外，这种语言的范围还包括广泛的用例:

*   命令行应用程序
*   云原生开发
*   创建实用程序和独立库
*   开发数据库，如 CockroachDB
*   游戏开发
*   开发业务

创建 Go web 框架是为了简化 Go web 开发过程，而不用担心设置问题，更关注项目的功能。

在没有框架的情况下使用 Go 是可能的，但是，这要繁琐得多，开发人员必须不断地重写代码。这就是 web 框架的用武之地。

例如，使用框架，开发人员可以选择一个最喜欢的框架，更多地关注业务逻辑，而不是在每个新项目中围绕数据库连接编写包装器。

## 使用 Go web 框架的优点

在我们研究五大 Go web 框架之前，让我们回顾一下 Go 流行的几个原因。

### 静态打字

静态类型在运行时提供了更好的性能，因为它主要用于构建在编译时高度优化的高性能应用程序。

静态类型也会发现隐藏的问题，比如类型错误。例如，如果我需要创建一个整数变量，编译器现在会指出它是一个整数，并且只接受一个整数。这使得管理大型项目的代码变得更加容易。

### 可用的软件包

许多开发人员已经在 Go 标准包的基础上创建了生产就绪包。这些包通常成为特定特性的标准库。例如，Gorilla Mux 是由社区为路由而创建的，因为最初的 Go 路由器相当有限。

Github 上有所有 Go 相关的包，比如 MongoDB，Redis，MySQL。

### 快速发展

这些框架的开发时间既快又简单。包已经可用，可以很容易地导入，消除了编写冗余代码的需要，这对开发人员来说是一个胜利。

### 内置并发性

Go 的 Goroutines 提供了简单的并发性，提供了对并发性的语言级支持、轻量级线程、避免变异的严格规则以禁止竞争条件，以及整体的简单性。

## 使用 Go 框架的缺点

使用 Go 框架时，唯一需要注意的是错误处理。在 Go 中处理错误仍然很困难，因为它通过返回错误和响应而变得麻烦和嘈杂，并且它的严格类型使得编写起来更困难。

## 5 大 Go 框架

### 杜松子酒

Gin 是一个用 Go 编写的 HTTP web 框架，在 Github 上有超过 50k 名明星，在[非常受欢迎。](https://github.com/gin-gonic/gin)

目前，它是构建微服务最流行的方法，因为它提供了一种简单的方法来构建请求处理管道，您可以在其中插入中间件。

它还拥有一个类似 Martini 的 API，根据 Gin 的 GitHub 页面，由于有了 httprouter，它的速度比 T2 快了 40 倍。以下是它的一些令人惊叹的功能。

#### 杜松子酒的一般特征

##### 错误管理

Gin 提供了方便的错误管理。这意味着当在 HTTP 请求过程中遇到任何错误时，Gin 会在错误发生时记录这些错误:

```
c.AbortWithStatusJSON(400, gin.H{
  "error": "Blah blahhh"
})

// continue
c.JSON(200, gin.H{
  "msg": "ok"
})

```

##### 创建中间件

创建中间件也非常容易，通过用`r := gin.New()`创建一个路由器和用`r.Use(gin.Logger())`添加一个日志中间件，就可以将中间件插入到请求管道中。

你也可以用一个恢复中间件和`r.Use(gin.Recovery())`。

##### 杜松子酒的表演

Gin 的性能得益于它的路由分组和内存小。Gin 的路由分组能力让 Gin 中的路由无限嵌套而不影响性能。

它的快速性能还得益于它的小内存，Gin 在运行时使用或引用它。服务器消耗的内存越多，速度就越慢。由于 Gin 占用内存少，所以它提供了更快的性能。

##### JSON 验证

最后，Gin 提供了对 JSON 验证的支持。用 JSON 请求可以验证所需的值，比如来自客户端的输入数据。这些值在保存到内存中之前必须经过验证，因此通过验证它们，开发人员可以避免保存不准确的值。

Gin 是一个简单易用的框架，如果你刚刚开始使用 Golang，Gin 被认为是最理想的框架，因为它简单易用。

关于 [jumpstart 教程，可以查看此链接](https://gin-gonic.com/docs/quickstart/)。

### 比戈

[Beego 是另一个 Go web 框架](https://beego.me/)，主要用于构建快速开发的企业 web 应用。

Beego 有四个主要部分，使它成为一个可行的 go 框架:

*   基本模块，包含`log`、`config`和`governor`
*   网络服务器
*   任务，其工作方式类似于 cron 作业
*   一个客户端，包含 ORM、httplib 和缓存模块

#### Beego 一般特征

##### 支持企业应用程序

因为 Beego 专注于企业应用程序，这些应用程序往往非常大，有许多代码支持许多功能，所以模块化结构为特定的用例安排模块，从而优化性能。

因此，该框架为配置模块、日志模块和缓存模块等提供了一个很好的模块化结构。

它还使用[一个常规的 MVC 架构](https://github.com/beego/beedoc/tree/master/en-US/mvc)来处理应用程序中的特定开发方面，这对企业应用程序也是有益的。

##### 支持命名空间路由

Beego 也支持名称空间路由，它为一个`Route`定义了`Controller`的位置。下面是一个例子:

```
func init() {

ns :=
    beego.NewNamespace("/v1",
        beego.NSRouter("/auth", &controllers.AuthController{}),
        beego.NSRouter("/scheduler/task",&controllers.TaskController{}),
    )

    beego.AddNamespace(ns) 
}

```

Beego 通过 Swagger 实现的自动化 [API 文档为开发人员提供了创建 API 文档所需的自动化，而无需浪费时间手动创建。](https://blog.logrocket.com/documenting-your-express-api-with-swagger/)

Route annotation 允许开发人员为给定 URL 的路由目标定义任何组件。这意味着不需要在路由文件中再次注册路由；只有控制器应该使用`Include`。

使用下面的路由注释，Beego 会自动解析它们并将其转换为路由:

```
// Weather API
type WeatherController struct {
    web.Controller
}

func (c *WeatherController) URLMapping() {
    c.Mapping("StaticBlock", c.StaticBlock)
    c.Mapping("AllBlock", c.AllBlock)
}

// @router /staticblock/:key [get]
func (this *WeatherController) StaticBlock() {
}

// @router /all/:key [get]
func (this *WeatherController) AllBlock() {
}

```

然后，注册`Controller`:

```
web.Include(&WeatherController{})

```

### 彩虹女神

Iris 是一个相当于 Express.js 的 web 框架，对于来自 Node.js 社区的人来说更容易使用。

它附带了`Sessions`、API 版本控制、WebSocket、依赖注入、WebAssembly、典型的 MVC 架构等等，使得它对第三方库非常灵活。

GitHub 上的[超过 20k 颗星星，Iris 最受欢迎是因为它的简单性和用个人库快速扩展框架的能力。](https://github.com/kataras/iris)

#### 虹膜的一般特征

如前所述，Iris 的主要特性之一是它与外部库完全一致且灵活，允许用户挑选他们想在框架中使用的内容

使用内置的记录器打印和记录服务器请求，用户不需要使用外部设备，降低了使用 Iris 的复杂性。

像 Beego 一样，它为较大的应用程序提供 MVC 支持，并且它的自动化 API 版本化通过将它们放在 route 的较新版本中来方便添加新的集成。

Iris 的智能和快速压缩提供了更快的性能，并且测试与 Ngrok 集成是轻而易举的，它允许开发人员与其他开发人员共享本地 web 服务器进行测试。

这个特定框架的一个伟大之处是，作者可以快速回复 GitHub 上的问题，这使得它在遇到错误时很有帮助。

### 回声

[Echo 是 Labstack 用 GitHub](https://echo.labstack.com/) 上的 [20k stars 创建的另一个有前途的框架](https://github.com/labstack/echo)。它也被视为一个微框架，它更多的是一个标准库和路由器，并有完整的文档供开发人员使用和理解。

这个框架非常适合那些想从头开始学习如何创建 API 的人，因为它有大量的文档。

#### 回声一般特征

Echo 允许开发人员定义自己的中间件，也有内置的中间件可供使用。这使开发人员能够创建定制的中间件来获得特定的功能，同时让内置的中间件加速生产。

Echo 还支持 HTTP/2 以获得更快的性能和更好的用户体验。

它的 API 还支持各种 HTTP 响应，如 JSON、XML、stream、blob、file、attachment、inline 和定制的中央 HTTP 错误处理。

最后，它支持各种模板引擎，为开发人员选择引擎提供了灵活性和便利性。

### 纤维

Fiber 是另一个用 Go 编写的类似 Express.js 的 web 框架,它拥有低内存使用和丰富的路由。Fiber 构建在用于 Go 的最快 http 引擎的 [Fasthttp HTTP 引擎之上，提供了最快的 Go 框架之一。](https://github.com/valyala/fasthttp)

创建 Fiber 的主要目的是极简主义和 Unix 哲学，以提供简单和模块化的软件技术，Fiber 的想法是让新的 Go 开发者能够快速创建 web 应用程序。

#### 纤维一般特征

光纤拥有内置的速率限制器，有助于减少特定端点的流量。例如，如果用户试图持续登录某个帐户，并且知道这可能是恶意活动，这将很有帮助。

它的静态文件，如样式表、脚本和图像，可以从服务器处理和提供，使它们易于缓存，消耗更少的内存，并且内容在每次请求时保持静态。

它对 WebSocket 双向 TCP 连接的支持对于创建实时通信非常有用，比如聊天系统。

就像我们在这篇文章中提到的其他 Go 框架一样，它具有通用的中间件支持，支持各种模板引擎，内存使用和内存占用低，并且为新用户提供了简单明了的文档。

## 结论

在本文中，我们研究了五个顶级的 Go web 框架。这个列表并不意味着这些是最好的，也不表明你应该选择哪一个。您最喜欢的可能不在列表中，但这并不妨碍它根据您的用例成为更好的框架。

虽然其中一些框架是受其他框架的启发，一些是为了覆盖其他框架没有覆盖的领域而构建的，并且大多数框架都具有相似的特性，但是最佳框架取决于您的用例，因此请选择最适合您的 Go 项目的框架。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)