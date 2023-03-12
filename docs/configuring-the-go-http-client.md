# 配置 Go HTTP 客户端- LogRocket 博客

> 原文：<https://blog.logrocket.com/configuring-the-go-http-client/>

当构建与外部服务/产品通信的应用程序时，我们需要一种通用的方法来在两端建立可理解的连接。我们使用 API 来简化和建立客户机和服务器之间的通信。

在本教程中，我们将向您展示如何配置和建立客户端与服务器之间的连接，并向其他项目公开的端点发出 HTTP 请求，以便为我们的应用程序提供资源。向 API 发出请求调用意味着根据 API 的精确文档向 web 服务器发出 HTTP(s)请求。

客户端是通过 HTTP 协议以 URL 的形式向 web 服务器请求特定服务或数据并接收响应的主机(例如，浏览器)。服务器是一台远程计算机，它接受和处理请求，并使用 HTTP/HTTPS 协议发送适当的响应数据。

## Golang HTTP 客户端

Go 标准库在 [`net/http`](https://pkg.go.dev/net/http) 包中为 HTTP 客户端提供了出色的支持。在本指南中，我们将探索 Go 程序向外部资源发出 HTTP/HTTPS 请求所需的所有配置。

我们将使用来自`net/http`库的标准 Go 客户端，通过简单地创建一个类型为`http.Client`的变量来初始化一个 HTTP 客户端。

```
// go/src/http-client/main.go
package main
import (
  "fmt"
  "io/ioutil"
  "net/http"
  "time"
)
func main() {
    c := http.Client{Timeout: time.Duration(1) * time.Second}
    resp, err := c.Get("https://go.dev/")
    if err != nil {
        fmt.Printf("Error %s", err)
        return
    }
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)

    if err != nil {
      fmt.Printf("Error %s", err)
      return
    }

    fmt.Printf("Body : %s", body)
}

```

创建 HTTP 客户端时，我们可以指定并传递某些字段来配置客户端-服务器连接。

在上面的代码片段中，我们指定了一个类型为`time.Duration`的`Timeout`字段。当客户端通过 HTTP 打开与服务器的连接时，服务器可能需要一些时间来响应请求。此字段使我们能够指定从服务器获得响应的最长等待时间。

我们可以在`http.Client`中指定其他字段:

*   `Transport` (type `http.RoundTripper` ) —这定制了在我们的程序中处理和执行 HTTP 请求的过程
*   `CheckedRedirect` (type `func(req *Request, via []*Request)` ) —在请求重定向的情况下，我们可以使用该字段在程序中定义一个函数来处理请求重定向的情况
*   `Jar`(类型`CookieJar` ) —我们可以使用这个字段向 HTTP 请求添加 cookies

在上面的代码中，我们用`DefaultTransport`定义了 HTTP 客户端，没有`CheckRedirect`函数，没有 cookies，超时设置为一秒。

## 获取和发布请求

在前面的代码块中，我们定义了一个对 URL`[https://go.dev/](https://go.dev/)`的 GET 请求。我们向 web 服务器发出一个请求，并将响应和一个可能的错误值分别赋给变量`resp`和`err`。

```
...
  resp, err := c.Get("https://go.dev/")

  if err != nil {
    fmt.Printf("Error %s", err)
    return
  }
...

```

上面的代码片段类似于向 URL 发出 POST 请求的代码。但是在这种情况下，我们需要在发送给 web 服务器的请求体中，将发送的数据与 POST 请求一起附加。

```
...
  postData := bytes.NewBuffer([]byte(`{"post":"boom boom library"}`))
  resp, err := c.Post("https://go.dev/", "application/json", postData)

  if err != nil {
    fmt.Printf("Error %s", err)
    return
  }
...

```

在上面的代码片段中，我们创建了一个新变量，`postData` (type `*bytes.Buffer`)来保存我们希望随请求一起发送的数据。然后，在`c.Post`函数中，我们将`postData`作为参数与数据的 URL 和内容类型一起传递。

为什么不简单地放一个 JSON 字符串作为`postData`？那是因为这个参数必须实现接口`io.Reader`。

现在我们已经看到了如何发出 GET 或 POST 请求，让我们快速地看看如何从`resp`中检索响应:

```
...
  defer resp.Body.Close()
  body, err := ioutil.ReadAll(resp.Body)

  if err != nil {
    fmt.Printf("Error %s", err)
    return
  }

  fmt.Printf("Body : %s", body)
...

```

使用`defer`关键字，我们安排了一个对`resp.Body.Close`的函数调用来关闭`resp.Body`，这是函数返回时从请求返回的数据流。这是程序的必要部分，以避免潜在的与服务器的持久连接。

让我们为我们希望向服务器发出的每种类型的请求构建方法。这可能会涉及更多的代码，但它给了我们拥有自己代码的灵活性。然后，我们可以轻松地在请求旁边附加我们想要的头。

```
// go/src/http-client/main.go
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
    "time"
)

func main() {

  c := http.Client{Timeout: time.Duration(1) * time.Second}
  req, err := http.NewRequest("GET", "https://go.dev/", nil)
  if err != nil {
    fmt.Printf("error %s", err)
    return
  }
  req.Header.Add("Accept", `application/json`)

  resp, err := c.Do(req)
  if err != nil {
    fmt.Printf("Error %s", err)
    return
  }

  defer resp.Body.Close()
  body, err := ioutil.ReadAll(resp.Body)
  if err != nil {
    fmt.Printf("Error %s", err)
    return
  }

  fmt.Printf("Body : %s", body)
}

```

在上面的代码中，我们创建了一个客户端，然后使用`http.NewRequest`方法定义了一个新的请求。通过参数，我们指定了我们想要的请求类型。

`http.Request`有一个如下所示的函数签名:

```
(method, url string, body io.Reader) (*Request, error)

```

使用第一个参数，我们指定请求的方法。然后，我们在第二个参数中指定 URL 和保存数据的`body`——或者在 GET 请求的情况下指定`nil`,因为我们没有要发送的主体。

接下来，我们定义希望追加到请求的`Header`，如下所示:

```
req.Header.Add("Accept", `application/json`)

```

我们使用`Header`字段向服务器添加和传输关于请求的附加信息层。HTTP 1/1 的规范提供了几个`Header`字段:

*   `Content-Length`是发送的消息的大小(以字节为单位)。如果未指定，默认值为`2`
*   `User-Agent`是发送请求的程序的名称和版本。例如，`curl/7.16.3`如果我们使用 curl 来发出请求。如果未指定，默认值为`Go-http-client/1.1`
*   `Authorization`提供成功请求所需的凭证。凭证可以包括 API 密钥、用户名/密码、JWT 等
*   `Accept-Encoding`指定响应中可接受的编码类型。如果未指定，默认值为`gzip`
*   `Content-Type`告诉服务器在请求中将传输什么类型的媒体。如果未指定，默认值为`application/json`
*   `Accept`指定响应可接受的媒体类型。

请求的`Header`字段实现了类型`map\[string\][]string`，其中键是字符串，值是字符串的片段。

## 授权您的请求

HTTP `Authorization`请求头可以提供服务器用来认证用户的凭证，从而允许访问受保护的资源。

```
...
req, err = http.NewRequest("GET", "https://www.xxxx.xxx", nil)
req.Header.Add("Accept", `application/json`)
req.Header.Add("Authorization", fmt.Sprintf("token %s", os.Getenv("TOKEN"))
...

```

在上面的代码片段中，我们使用程序中的`os`包检索了访问令牌。这比直接访问令牌要好。`Getenv`方法检索名为`TOKEN`的环境变量并解析它。

我们还可以在我们的`go`命令前面加上`GITHUB_TOKEN=XXX`，以便在运行程序之前将一个环境变量传递给程序，如下所示:

```
$ GITHUB_TOKEN=xxxxx go run main.go

```

## 结论

在本教程中，我们通过一个简单的过程来配置您的 HTTP 客户端。现在，您可以开始从应用程序向外部资源发出 API 请求。

可以用更多的方法修改`http.NewRequest`，比如`HEAD`、`PUT`、`PATCH`、`DELETE`等。，然后在必要时向函数传递一个主体参数，或者在不需要主体时传递`nil`。一旦响应被我们的程序检索到/可用，我们就可以根据用例在我们的项目中使用它。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)