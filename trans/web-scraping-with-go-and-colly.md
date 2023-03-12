# 在 Go 中创建一个网络爬虫

> 原文：<https://blog.logrocket.com/web-scraping-with-go-and-colly/>

Web 抓取是一种技术，可以让您从网站上检查、解析和提取数据，否则由于缺乏专用 API，这些数据将很难访问。Web 爬行包括系统地浏览互联网，从“种子”URL 开始，并递归地访问爬行器在每个被访问的页面上找到的链接。

Colly 是一个用于编写网页抓取器和爬虫的 Go 包。它基于 Go 的 net/HTTP(用于网络通信)和 goquery(允许您使用“类似 jQuery”的语法来定位 HTML 元素)。

在这篇文章中，我们将收集生日在某一天的名人的详细信息。我们将利用 Colly 的权力从 IMDB 网站获取这些数据。

## 开始使用并安装应用依赖项

为了跟进，您需要安装一个装有 Go 的系统(最好是 1.14 或更高版本)。

> 注意:下面使用的 shell 命令是针对 Linux/macOS 的，但是如果您的操作系统不同，您可以随意使用它的等效命令。

创建您希望这段代码所在的目录，并初始化一个新的 Go 模块:

```
$ mkdir birthdays-today && cd birthdays-today
$ go mod init gitlab.com/idoko/birthdays-today
```

Colly 是我们需要安装的唯一一个外部包，因为它具有发出 HTTP 请求和解析内置 HTML DOM 的能力。运行以下命令，将其作为应用程序依赖项引入:

```
$ go get github.com/go-colly/colly
```

## 熟悉 Colly

Colly 的核心是`[Collector](https://godoc.org/github.com/gocolly/colly#Collector)`组件。收集器负责进行网络调用，它们是可配置的，允许您修改`UserAgent`字符串、限制要爬行到特定域的 URL，或者让爬虫异步运行。您可以用下面的代码初始化一个新的`Collector`:

```
c := colly.NewCollector(
  // allow only IMDB links to be crawled, will visit all links if not set
  colly.AllowedDomains("imdb.com", "www.imdb.com"),
  // sets the recursion depth for links to visit, goes on forever if not set
  colly.MaxDepth(3),
  // enables asynchronous network requests
  colly.Async(true),
)
```

或者，您可以让 Colly 使用默认选项，只需调用:

```
c := colly.NewCollector()
```

收集器还可以附加诸如`OnRequest`和`OnHTML`之类的回调。这些回调在集合生命周期的不同阶段执行(类似于 React 的生命周期方法)，例如，Colly 在收集器发出 HTTP 请求之前调用`OnRequest`方法。你可以在 [Colly 的 godoc](https://godoc.org/github.com/gocolly/colly#Collector.OnError) 页面上找到支持回调的完整列表。

对于更复杂的抓取器，你也可以配置收集器来[在 Redis](https://github.com/gocolly/redisstorage) 上存储访问过的 URL 和 cookies 或者[附加一个调试器](http://go-colly.org/docs/best_practices/debugging/)到它们上面，看看到底发生了什么。

## 与目标网站建立合作关系

让我们创建两个独立的函数—`main`和`crawl`。我们的程序自动调用`main`，后者又调用`crawl`访问并从网页中提取我们需要的信息。稍后，我们将扩展`main`来读取所需的月份和日期作为命令行参数，这样我们就可以获得任何一天的生日列表:

```
package main

import (
  "encoding/json"
  "flag"
  "fmt"
  "github.com/gocolly/colly"
  "log"
  "strings"
)

func main() {
  crawl()
}

func crawl() {
  c := colly.NewCollector(
    colly.AllowedDomains("imdb.com", "www.imdb.com"),
  )
  infoCollector := c.Clone()

  c.OnRequest(func(r *colly.Request) {
    fmt.Println("Visiting: ", r.URL.String())
  })

  infoCollector.OnRequest(func(r *colly.Request) {
    fmt.Println("Visiting Profile URL: ", r.URL.String())
  })

  c.Visit("https://www.imdb.com/search/name/?birth_monthday=12-20")
}
```

上面的代码片段初始化了一个收集器，并将其限制在“IMDB”域中。因为我们的 scraper 包含两个子任务(获取生日列表和获取个人名人页面)，所以我们使用`c.Clone()`复制创建的收集器。我们还为收集器附加了不同的`OnRequest`实现，以了解它们何时开始运行。最后，它用一个“种子”URL 调用`c.Visit`，这个 URL 列出了所有出生于 12 月 20 日的名人。

## 用 Colly 遍历 HTML 页面

默认情况下，IMDB 列表每页显示 50 个项目，通过*下一个*链接可以转到下一页。我们将递归地访问下面的页面，通过将下面的代码块附加到`crawl`函数的末尾(就在调用`c.Visit`之前)，将`OnHTML`回调附加到原始的收集器对象，从而获得完整的列表:

```
c.OnHTML("a.lister-page-next", func(e *colly.HTMLElement) {
   nextPage := e.Request.AbsoluteURL(e.Attr("href"))
   c.Visit(nextPage)
})
```

代码以下一个链接*为目标，并将其转换为完整的绝对 URL。然后访问该 URL，在下一个页面上也会发生同样的事情。请注意，这种快速、自动访问网站的方式可能会阻止您的 IP 地址。您可以探索 Colly 的[限制规则](http://go-colly.org/docs/examples/rate_limit/)来模拟您的请求之间的随机延迟。*

类似地，将另一个用于访问个人名人页面的`OnHTML`监听器连接到第一个收集器:

```
c.OnHTML(".mode-detail", func(e *colly.HTMLElement) {
   profileUrl := e.ChildAttr("div.lister-item-image > a", "href")
   profileUrl = e.Request.AbsoluteURL(profileUrl)
   infoCollector.Visit(profileUrl)
})
```

在上面的代码片段中，我们委托`infoCollector`来访问单独的页面。这样，我们就可以监听页面何时准备好，并提取我们需要的数据。

## 将 html 封送到 struts

接下来，让我们设置`movie`和`star`结构来保存每个名人的数据。movie struct 表示个人页面上列出的热门电影的详细信息，star struct 包含他们的个人资料。在`main.go`文件中的`main`函数之前添加以下代码片段:

```
type movie struct {
   Title string
   Year string
}
type star struct {
   Name  string
   Photo string
   JobTitle string
   BirthDate string
   Bio string
   TopMovies []movie
}
```

接下来，在`crawl`函数中的`infoCollector`上附加一个新的`OnHTML`监听器。回调将通过 profile 容器(ID 为`content-2-wide`的 div)，提取并打印其中包含的名人数据。

为了便于理解，这里有一个[示例 IMDB 配置文件页面](https://www.imdb.com/name/nm0925316/):

![IMDB profile page of Persia White](img/0f938a24111b190757d6fee18636e94e.png)

```
infoCollector.OnHTML("#content-2-wide", func(e *colly.HTMLElement) {
   tmpProfile := star{}
   tmpProfile.Name = e.ChildText("h1.header > span.itemprop")
   tmpProfile.Photo = e.ChildAttr("#name-poster", "src")
   tmpProfile.JobTitle = e.ChildText("#name-job-categories > a > span.itemprop")
   tmpProfile.BirthDate = e.ChildAttr("#name-born-info time", "datetime")

   tmpProfile.Bio = strings.TrimSpace(e.ChildText("#name-bio-text > div.name-trivia-bio-text > div.inline"))

   e.ForEach("div.knownfor-title", func(_ int, kf *colly.HTMLElement) {
      tmpMovie := movie{}
      tmpMovie.Title = kf.ChildText("div.knownfor-title-role > a.knownfor-ellipsis")
      tmpMovie.Year = kf.ChildText("div.knownfor-year > span.knownfor-ellipsis")
      tmpProfile.TopMovies = append(tmpProfile.TopMovies, tmpMovie)
   })
   js, err := json.MarshalIndent(tmpProfile, "", "    ")
   if err != nil {
      log.Fatal(err)
   }
   fmt.Println(string(js))
})
```

除了从页面中提取生物数据之外，上面的代码还循环遍历该人出演的热门电影(由 div 标识，其类别为`knownfor-title`)并将它们存储在电影列表中。然后它打印一个格式化的 JSON 表示的`star`结构。你也可以将它添加到名人数组中，或者存储在数据库中。

## 使用标志接收 CLI 参数

我们的 scraper 几乎准备好了，尽管它只获取特定日期(01/11)的生日列表。为了使它更加动态，我们将添加对 CLI 标志的支持，以便我们可以将任何日期和月份作为命令行参数传入。

用下面的代码替换当前的`main`函数:

```
func main() {
   month := flag.Int("month", 1, "Month to fetch birthdays for")
   day := flag.Int("day", 1, "Day to fetch birthdays for")
   flag.Parse()
   crawl(*month, *day)
}
```

上面的代码块允许我们指定我们感兴趣的月份和日期，例如`go run ./main.go--month=10 -- day=10`将获取生日在 10 月 10 日的名人列表。

接下来，修改`crawl`函数，将它的签名从`func crawl()`改为`*func* crawl(month int, day int)`，以接受月和日参数。

通过用下面的代码替换包含`c.Visit("https://www.imdb.com/search/name/?birth_monthday=10-25")`的行，使用种子 URL 中的函数参数:

```
startUrl := fmt.Sprintf("https://www.imdb.com/search/name/?birth_monthday=%d-%d", month, day)
c.Visit(startUrl)
```

使用以下命令构建并运行 scraper:

```
$ go build ./main.go
$ ./main --month=10 --day=10
```

你应该会得到类似下面截图的响应:
![running web scraper](img/4245ceca396dbbbd66fc37a0770cafc2.png)

## 结论

在本文中，我们学习了如何抓取网站，并从我们访问的页面中提取信息来满足我们的需求。完整的源代码可在 [GitLab](https://gitlab.com/idoko/birthdaystoday) 上获得。有兴趣进一步探索 Colly 吗？以下是一些有用的链接:

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)