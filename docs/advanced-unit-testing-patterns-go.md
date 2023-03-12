# Go 中的高级单元测试模式

> 原文：<https://blog.logrocket.com/advanced-unit-testing-patterns-go/>

一个好的开发人员总是测试他们的代码，然而，普通的测试方法在某些情况下可能过于简单。根据项目的复杂程度，您可能需要运行高级测试来准确评估代码的性能。

在本文中，我们将研究一些用于 Go 测试的模式，它们将帮助您为任何项目编写有效的测试。我们将涵盖诸如模仿、测试夹具、测试助手和黄金文件等概念，并且您将看到如何在现实世界的场景中应用每种技术。

要理解本文，您应该对 Go 中的[单元测试有所了解。我们开始吧！](https://blog.logrocket.com/a-deep-dive-into-unit-testing-in-go/)

## 测试 HTTP 处理程序

首先，让我们考虑一个常见的场景，测试 HTTP 处理程序。HTTP 处理程序应该与它们的依赖项松散耦合，这样可以很容易地隔离一个元素进行测试，而不会影响代码的其余部分。如果您的 HTTP 处理程序最初设计得很好，测试应该相当简单。

### 检查状态代码

让我们考虑一个检查以下 HTTP 处理程序的状态代码的基本测试:

```
func index(w http.ResponseWriter, r *http.Request) {
    w.WriteHeader(http.StatusOK)
}

```

上面的`index()`处理程序应该为每个请求返回一个 200 OK 响应。让我们通过下面的测试来验证处理程序的响应:

```
func TestIndexHandler(t *testing.T) {
    w := httptest.NewRecorder()
    r := httptest.NewRequest(http.MethodGet, "/", nil)

    index(w, r)

    if w.Code != http.StatusOK {
        t.Errorf("Expected status: %d, but got: %d", http.StatusOK, w.Code)
    }
}

```

在上面的代码片段中，我们使用`httptest`包来测试`index()`处理程序。我们返回了一个`httptest.ResponseRecorder`，它通过`NewRecorder()`方法实现了`http.ResponseWriter`接口。`http.ResponseWriter`记录任何突变，允许我们在测试中做出断言。

我们还可以使用`httptest.NewRequest()`方法创建一个 HTTP 请求。这样做指定了处理程序期望的请求类型，如请求方法、查询参数和响应体。也可以在通过`http.Header`类型获得`http.Request`对象后设置请求头。

在用`http.Request`对象和响应记录器调用了`index()`处理程序之后，您可以使用`Code`属性直接检查处理程序的响应。要对响应的其他属性(如标头或正文)进行断言，可以访问响应记录器上的适当方法或属性:

```
$ go test -v
=== RUN   TestIndexHandler
--- PASS: TestIndexHandler (0.00s)
PASS
ok      github.com/ayoisaiah/random 0.004s

```

### 外部依赖性

现在，让我们考虑另一个常见的场景，其中我们的 HTTP 处理程序依赖于外部服务:

```
func getJoke(w http.ResponseWriter, r *http.Request) {
    u, err := url.Parse(r.URL.String())
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }

    jokeId := u.Query().Get("id")
    if jokeId == "" {
        http.Error(w, "Joke ID cannot be empty", http.StatusBadRequest)
        return
    }

    endpoint := "https://icanhazdadjoke.com/j/" + jokeId

    client := http.Client{
        Timeout: 10 * time.Second,
    }

    req, err := http.NewRequest(http.MethodGet, endpoint, nil)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }

    req.Header.Set("Accept", "text/plain")

    resp, err := client.Do(req)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }

    defer resp.Body.Close()

    b, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }

    if resp.StatusCode != http.StatusOK {
        http.Error(w, string(b), resp.StatusCode)
        return
    }

    w.Header().Set("Content-Type", "text/plain")
    w.WriteHeader(http.StatusOK)
    w.Write(b)
}

func main() {
    mux := http.NewServeMux()

我一直在想一个关于动量的老爸笑话。。。但是我似乎不能让它继续下去。

    mux.HandleFunc("/joke", getJoke)

    http.ListenAndServe(":1212", mux)
}

```

在上面的代码块中，`getJoke`处理程序需要一个`id`查询参数，它用这个参数从[随机爸爸笑话 API 中获取一个笑话。](https://icanhazdadjoke.com/)

让我们为这个处理程序编写一个测试:

```
func TestGetJokeHandler(t *testing.T) {
    table := []struct {
        id         string
        statusCode int
        body       string
    }{
        {"R7UfaahVfFd", 200, "My dog used to chase people on a bike a lot. It got so bad I had to take his bike away."},
        {"173782", 404, `Joke with id "173782" not found`},
        {"", 400, "Joke ID cannot be empty"},
    }

    for _, v := range table {
        t.Run(v.id, func(t *testing.T) {
            w := httptest.NewRecorder()
            r := httptest.NewRequest(http.MethodGet, "/joke?id="+v.id, nil)

            getJoke(w, r)

            if w.Code != v.statusCode {
                t.Fatalf("Expected status code: %d, but got: %d", v.statusCode, w.Code)
            }

            body := strings.TrimSpace(w.Body.String())

            if body != v.body {
                t.Fatalf("Expected body to be: '%s', but got: '%s'", v.body, body)
            }
        })
    }
}

```

我们使用表驱动测试来针对一系列输入测试处理程序。第一个输入是一个有效的`Joke ID`，它应该返回一个 200 OK 响应。第二个是无效的 id，应该返回 404 响应。最后的输入是一个空的 ID，它应该返回一个 400 错误的请求响应。

当您运行测试时，它应该会成功通过:

```
$ go test -v
=== RUN   TestGetJokeHandler
=== RUN   TestGetJokeHandler/R7UfaahVfFd
=== RUN   TestGetJokeHandler/173782
=== RUN   TestGetJokeHandler/#00
--- PASS: TestGetJokeHandler (1.49s)
    --- PASS: TestGetJokeHandler/R7UfaahVfFd (1.03s)
    --- PASS: TestGetJokeHandler/173782 (0.47s)
    --- PASS: TestGetJokeHandler/#00 (0.00s)
PASS
ok      github.com/ayoisaiah/random     1.498s

```

注意，上面代码块中的测试向真正的 API 发出 HTTP 请求。这样做会影响被测试代码的依赖性，对于单元测试代码来说，这是一个糟糕的实践。

相反，我们应该模拟 HTTP 客户端。我们在 Go 中有几种不同的模仿方法，我们将在下面探讨。

## 围棋中的嘲讽

在 Go 中模仿 HTTP 客户端的一个相当简单的模式是创建一个自定义接口。我们的接口将定义函数中使用的方法，并根据调用函数的位置传递不同的实现。

上面的 HTTP 客户端的自定义接口应该类似于下面的代码块:

```
type HTTPClient interface {
    Do(req *http.Request) (*http.Response, error)
}

```

我们对`getJoke()`的签名将类似于下面的代码块:

```
func getJoke(client HTTPClient) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
      // rest of the function
    }
}

```

`getJoke()`处理程序的原始主体被移到返回值内部。为了支持`HTTPClient`接口，从主体中移除了`client`变量声明。

`HTTPClient`接口包装了一个`Do()`方法，该方法接受 HTTP 请求并返回 HTTP 响应和错误。

当我们在`main()`函数中调用`getJoke()`时，我们需要提供`HTTPClient`的具体实现:

```
func main() {
    mux := http.NewServeMux()

    client := http.Client{
        Timeout: 10 * time.Second,
    }

    mux.HandleFunc("/joke", getJoke(&client))

    http.ListenAndServe(":1212", mux)
}

```

`http.Client`类型实现了`HTTPClient`接口，所以程序继续调用随机爸爸笑话 API。我们需要用一个不同的`HTTPClient`实现来更新测试，这个实现不通过网络发出 HTTP 请求。

首先，我们将创建一个`HTTPClient`接口的模拟实现:

```
type MockClient struct {
    DoFunc func(req *http.Request) (*http.Response, error)
}

func (m *MockClient) Do(req *http.Request) (*http.Response, error) {
    return m.DoFunc(req)
}

```

在上面的代码块中，`MockClient`结构通过提供调用`DoFunc`属性的`Do`方法来实现`HTTPClient`接口。现在，当我们在测试中创建一个`MockClient`的实例时，我们需要实现`DoFunc`函数:

```
func TestGetJokeHandler(t *testing.T) {
    table := []struct {
        id         string
        statusCode int
        body       string
    }{
        {"R7UfaahVfFd", 200, "My dog used to chase people on a bike a lot. It got so bad I had to take his bike away."},
        {"173782", 404, `Joke with id "173782" not found`},
        {"", 400, "Joke ID cannot be empty"},
    }

    for _, v := range table {
        t.Run(v.id, func(t *testing.T) {
            w := httptest.NewRecorder()
            r := httptest.NewRequest(http.MethodGet, "/joke?id="+v.id, nil)

            c := &MockClient{}

            c.DoFunc = func(req *http.Request) (*http.Response, error) {
                return &http.Response{
                    Body:       io.NopCloser(strings.NewReader(v.body)),
                    StatusCode: v.statusCode,
                }, nil
            }

            getJoke(c)(w, r)

            if w.Code != v.statusCode {
                t.Fatalf("Expected status code: %d, but got: %d", v.statusCode, w.Code)
            }

            body := strings.TrimSpace(w.Body.String())

            if body != v.body {
                t.Fatalf("Expected body to be: '%s', but got: '%s'", v.body, body)
            }
        })
    }
}

```

在上面的代码片段中，`DoFunc`针对每个测试用例进行了调整，因此它返回一个定制的响应。现在，我们已经避免了所有的网络调用，因此测试将以更快的速度通过:

```
$ go test -v
=== RUN   TestGetJokeHandler
=== RUN   TestGetJokeHandler/R7UfaahVfFd
=== RUN   TestGetJokeHandler/173782
=== RUN   TestGetJokeHandler/#00
--- PASS: TestGetJokeHandler (0.00s)
    --- PASS: TestGetJokeHandler/R7UfaahVfFd (0.00s)
    --- PASS: TestGetJokeHandler/173782 (0.00s)
    --- PASS: TestGetJokeHandler/#00 (0.00s)
PASS
ok      github.com/ayoisaiah/random     0.005s

```

当您的处理程序依赖于另一个外部系统(如数据库)时，您可以使用相同的原则。将处理程序从任何特定的实现中分离出来，可以让您在测试中轻松模拟依赖关系，同时在应用程序代码中保留真正的实现。

## 在测试中使用外部数据

在 Go 中，您应该将测试的外部数据放在一个名为`testdata`的目录中。当您为程序构建二进制文件时，`testdata`目录会被忽略，因此您可以使用这种方法来存储您想要用来测试程序的输入。

例如，让我们编写一个从二进制文件生成`base64`编码的函数:

```
func getBase64Encoding(b []byte) string {
    return base64.StdEncoding.EncodeToString(b)
}

```

为了测试这个函数是否产生正确的输出，让我们将一些样本文件和它们对应的`base64`编码放在项目根目录下的`testdata`目录中:

```
$ ls testdata
img1.jpg img1_base64.txt img2.jpg img2_base64.txt img3.jpg img3_base64.txt

```

为了测试我们的`getBase64Encoding()`函数，运行下面的代码:

```
func TestGetBase64Encoding(t *testing.T) {
    cases := []string{"img1", "img2", "img3"}

    for _, v := range cases {
        t.Run(v, func(t *testing.T) {
            b, err := os.ReadFile(filepath.Join("testdata", v+".jpg"))
            if err != nil {
                t.Fatal(err)
            }

            expected, err := os.ReadFile(filepath.Join("testdata", v+"_base64.txt"))
            if err != nil {
                t.Fatal(err)
            }

            got := getBase64Encoding(b)

            if string(expected) != got {
                t.Fatalf("Expected output to be: '%s', but got: '%s'", string(expected), got)
            }
        })
    }
}

```

每个样本文件的字节从文件系统中读取，然后输入到`getBase64Encoding()`函数中。随后将输出与预期输出进行比较，预期输出也是从`testdata`目录中检索的。

让我们通过在`testdata`中创建一个子目录来使测试更容易维护。在我们的子目录中，我们将添加所有的输入文件，允许我们简单地迭代每个二进制文件，并将实际输出与预期输出进行比较。

现在，我们可以在不接触源代码的情况下添加更多的测试用例:

```
$ go test -v
=== RUN   TestGetBase64Encoding
=== RUN   TestGetBase64Encoding/img1
=== RUN   TestGetBase64Encoding/img2
=== RUN   TestGetBase64Encoding/img3
--- PASS: TestGetBase64Encoding (0.04s)
    --- PASS: TestGetBase64Encoding/img1 (0.01s)
    --- PASS: TestGetBase64Encoding/img2 (0.01s)
    --- PASS: TestGetBase64Encoding/img3 (0.01s)
PASS
ok      github.com/ayoisaiah/random     0.044s

```

## 使用黄金档

如果您使用的是 Go 模板，那么最好根据预期的输出来测试生成的输出，以确认模板正在按预期工作。Go 模板通常很大，所以不建议像我们在本教程中所做的那样在源代码中硬编码预期的输出。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们探索 Go 模板的另一种方法，它简化了整个项目生命周期中测试的编写和维护。

黄金文件是一种特殊类型的文件，包含测试的预期输出。测试函数读取黄金文件，将其内容与测试的预期输出进行比较。

在下面的例子中，我们将使用一个`html/template`来生成一个 HTML 表，其中包含库存中每本书的一行:

```
type Book struct {
    Name          string
    Author        string
    Publisher     string
    Pages         int
    PublishedYear int
    Price         int
}

var tmpl = `<table class="table">
  <thead>
    <tr>
      <th>Name</th>
      <th>Author</th>
      <th>Publisher</th>
      <th>Pages</th>
      <th>Year</th>
      <th>Price</th>
    </tr>
  </thead>
  <tbody>
    {{ range . }}<tr>
      <td>{{ .Name }}</td>
      <td>{{ .Author }}</td>
      <td>{{ .Publisher }}</td>
      <td>{{ .Pages }}</td>
      <td>{{ .PublishedYear }}</td>
      <td>${{ .Price }}</td>
    </tr>{{ end }}
  </tbody>
</table>
`

var tpl = template.Must(template.New("table").Parse(tmpl))

func generateTable(books []Book, w io.Writer) error {
    return tpl.Execute(w, books)
}

func main() {
    books := []Book{
        {
            Name:          "The Odessa File",
            Author:        "Frederick Forsyth",
            Pages:         334,
            PublishedYear: 1979,
            Publisher:     "Bantam",
            Price:         15,
        },
    }

    err := generateTable(books, os.Stdout)
    if err != nil {
        log.Fatal(err)
    }
}

```

上面的`generateTable()`函数从一部分`Book`对象中创建 HTML 表格。上面的代码将产生以下输出:

```
$ go run main.go
<table class="table">
  <thead>
    <tr>
      <th>Name</th>
      <th>Author</th>
      <th>Publisher</th>
      <th>Pages</th>
      <th>Year</th>
      <th>Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>The Odessa File</td>
      <td>Frederick Forsyth</td>
      <td>Bantam</td>
      <td>334</td>
      <td>1979</td>
      <td>$15</td>
    </tr>
  </tbody>
</table>

```

为了测试上面的函数，我们将捕获实际结果并将其与预期结果进行比较。我们将像在上一节中一样将预期结果存储在`testdata`目录中，但是，我们必须做一些更改。

假设我们在库存中有以下图书列表:

```
var inventory = []Book{
    {
        Name:          "The Solitare Mystery",
        Author:        "Jostein Gaarder",
        Publisher:     "Farrar Straus Giroux",
        Pages:         351,
        PublishedYear: 1990,
        Price:         12,
    },
    {
        Name:          "Also Known As",
        Author:        "Robin Benway",
        Publisher:     "Walker Books",
        Pages:         208,
        PublishedYear: 2013,
        Price:         10,
    },
    {
        Name:          "Ego Is the Enemy",
        Author:        "Ryan Holiday",
        Publisher:     "Portfolio",
        Pages:         226,
        PublishedYear: 2016,
        Price:         18,
    },
}

```

这个图书列表的预期输出将跨越多行，因此，很难将其作为字符串文字放在源代码中:

```
<table class="table">
  <thead>
    <tr>
      <th>Name</th>
      <th>Author</th>
      <th>Publisher</th>
      <th>Pages</th>
      <th>Year</th>
      <th>Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>The Solitaire Mystery</td>
      <td>Jostein Gaarder</td>
      <td>Farrar Straus Giroux</td>
      <td>351</td>
      <td>1990</td>
      <td>$12</td>
    </tr>
    <tr>
      <td>Also Known As</td>
      <td&gt;Robin Benway</td>
      <td>Walker Books</td>
      <td>308</td>
      <td>2013</td>
      <td>$10</td>
    </tr>
    <tr>
      <td>Ego Is The Enemy</td>
      <td>Ryan Holiday</td>
      <td>Portfolio</td>
      <td>226</td>
      <td>2016</td>
      <td>$18</td>
    </tr>
  </tbody>
</table>

```

除了适用于较大的输出，黄金文件可以自动更新和生成。

虽然可以编写一个助手函数来创建和更新黄金文件，但是我们可以利用 [goldie](https://github.com/sebdah/goldie) ，这是一个专门为黄金文件创建的实用程序。

使用以下命令安装最新版本的 goldie:

```
$ go get -u github.com/sebdah/goldie/v2

```

让我们继续使用 goldie 测试`generateTable()`函数:

```
func TestGenerateTable(t *testing.T) {
    var buf bytes.Buffer

    err := generateTable(inventory, &buf)
    if err != nil {
        t.Fatal(err)
    }

    actual := buf.Bytes()

    g := goldie.New(t)
    g.Assert(t, "books", actual)
}

```

上面的测试在一个字节的缓冲区中捕获了`generateTable()`函数的输出。然后，它将缓冲区的内容传递给`goldie`实例上的`Assert()`方法。缓冲区中的内容将与`testdata`目录中的`books.golden`文件的内容进行比较。

最初，运行测试会失败，因为我们还没有创建`books.golden`文件:

```
$ go test -v
=== RUN   TestGenerateTable
    main_test.go:48: Golden fixture not found. Try running with -update flag.
--- FAIL: TestGenerateTable (0.00s)
FAIL
exit status 1
FAIL    github.com/ayoisaiah/random     0.006s

```

错误消息建议我们添加`-update`标志，这将使用缓冲区的内容创建`books.golden`文件:

```
$ go test -v -update
=== RUN   TestGenerateTable
--- PASS: TestGenerateTable (0.00s)
PASS
ok      github.com/ayoisaiah/random     0.006s

```

在随后的运行中，我们应该删除`-update`标志，这样我们的黄金文件就不会不断更新。

对模板的任何更改都会导致测试失败。例如，如果您将价格字段更新为欧元而不是美元，您将立即收到一个错误。出现这些错误是因为`generateTable()`函数的输出不再匹配黄金文件的内容。

Goldie 提供了不同的功能来帮助您在出现这些错误时发现变化:

```
$ go test -v
=== RUN   TestGenerateTable
    main_test.go:48: Result did not match the golden fixture. Diff is below:

        --- Expected
        +++ Actual
        @@ -18,3 +18,3 @@
               <td>1990&lt;/td>
        -      <td>$12</td>
        +      <td>€12</td>
             </tr><tr>
        @@ -25,3 +25,3 @@
               <td>2013</td>
        -      <td>$10</td>
        +      <td>€10</td>
             </tr><tr>
        @@ -32,3 +32,3 @@
               <td>2016</td>
        -      <td>$18</td>
        +      <td>€18</td>
             </tr>

--- FAIL: TestGenerateTable (0.00s)
FAIL
exit status 1
FAIL    github.com/ayoisaiah/random     0.007s

```

在上面的输出中，这一变化被清晰地突出显示了出来。这些更改是有意的，因此我们可以通过使用`-update`标志更新黄金文件来再次通过测试:

```
$ go test -v -update
=== RUN   TestGenerateTable
--- PASS: TestGenerateTable (0.00s)
PASS
ok      github.com/ayoisaiah/random     0.006s

```

## 结论

在本教程中，我们学习了一些高级的围棋测试技术。首先，我们深入研究了我们的 HTTP 包，并学习了如何用自定义接口模拟我们的 HTTP 客户端。然后，我们回顾了如何在测试中使用外部数据，以及如何使用 goldie 创建黄金文件。

我希望这篇文章对你有用。如果你有任何额外的技术想要分享，请在下面留下评论。感谢您的阅读，祝您编码愉快！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)