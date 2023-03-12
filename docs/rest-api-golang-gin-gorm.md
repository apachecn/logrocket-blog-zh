# 使用 Gin 和 Gorm 用 Golang 构建 REST API

> 原文：<https://blog.logrocket.com/rest-api-golang-gin-gorm/>

***编者按**:本教程最后一次更新是在 2022 年 11 月 10 日，以使代码示例与 Go v1.19 兼容，并解决评论部分提出的问题。*

Go 成为一种流行语言是有原因的。它提供了与其他“低级”编程语言(如 Java 和 C++)类似的性能，但它也非常简单，这使得开发体验令人愉快。

如果我们可以将快速编程语言与快速 web 框架结合起来，构建一个高性能的 RESTful API，来处理大量的流量，会怎么样？

在做了大量研究来为这个庞然大物找到一个快速可靠的框架之后，我遇到了一个奇妙的开源项目，叫做 [Gin](https://github.com/gin-gonic/gin) 。

向前跳:

## 杜松子酒是什么？

Gin 框架是轻量级的，有很好的文档记录，当然，速度非常快。

与其他 Go web 框架不同，Gin 使用定制版本的 [HttpRouter](https://github.com/julienschmidt/httprouter) ，这意味着它可以比大多数框架更快地浏览您的 API 路径。创作者还声称，它的运行速度比与杜松子酒相对类似的[马提尼](https://github.com/go-martini/martini)快 40 倍。你可以在[这个基准](https://github.com/gin-gonic/gin/blob/master/BENCHMARKS.md)中看到更详细的对比。

然而，Gin 是一个微框架，并不具备开箱即用的丰富功能。它只为您提供构建 API 的基本工具，如路由、表单验证等。

对于认证用户、上传文件和发送电子邮件等任务，您需要安装另一个第三方库或自己实现。

这可能不太适合需要在很短时间内发布大量特性的小型开发团队。另一个 web 框架，如 [Laravel](https://laravel.com/) 和 [Ruby on Rails](https://rubyonrails.org/) ，可能更适合这样的团队。这种框架固执己见，更容易学习，并提供了许多现成的特性，这使您能够在瞬间开发一个功能完整的 web 应用程序。

如果你是一个小团队的一员，这一堆可能是多余的。但是如果你有兴趣做长期投资，你可以利用 Gin 非凡的性能和灵活性。

## 使用 Gin 和 Gorm 在 Go 中构建 REST API

在本教程中，我们将演示如何构建一个书店 REST API 来提供图书数据并执行 CRUD 操作。

在我们开始之前，我假设您:

*   在你的机器上安装 Go
*   了解 Go 语言的基础知识
*   对 RESTful API 有一个大致的了解

让我们首先初始化一个新的 [Go 模块](https://blog.golang.org/using-go-modules)来管理我们项目的依赖项。确保在您的 [Go 环境文件夹](https://github.com/golang/go/wiki/GOPATH)中运行这个命令:

```
$ go mod init

```

现在让我们安装[轧棉](https://github.com/gin-gonic/gin)和[轧棉](https://github.com/jinzhu/gorm):

```
go get github.com/gin-gonic/gin gorm.io/gorm

```

安装完成后，您的文件夹应该包含两个文件:`go.mod`和`go.sum`。这两个文件都包含关于您安装的包的信息，这在与其他开发人员合作时很有帮助。如果有人想参与这个项目，他们需要做的就是在他们的终端上运行`go mod download`命令，在他们的机器上安装所有需要的依赖项。

供参考， [GitHub](https://gist.github.com/Goodnessuc/314604e369497774a85f14c0ce6c485f) 上这个项目的全部源代码。请随意探索并使用代码。

```
$ git clone https://github.com/rahmanfadhil/gin-bookstore.git

```

## 设置服务器

让我们从在`main.go`文件中创建一个 Hello World 服务器开始:

```
package main

import (
  "net/http"
  "github.com/gin-gonic/gin"
)

func main() {
  r := gin.Default()

  r.GET("/", func(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{"data": "hello world"})    
  })

  r.Run()
}

```

我们首先需要声明无论何时运行我们的应用程序都会被触发的`main`函数。在这个函数中，我们将在`r`变量中初始化一个新的 Gin 路由器。我们使用`Default`路由器，因为 Gin 提供了一些有用的中间件，我们可以用它们来调试我们的服务器。

接下来，我们将定义到`/`端点的`GET`路由。如果你曾经使用过其他框架，比如 [Express.js](https://expressjs.com/) 、 [Flask](https://www.palletsprojects.com/p/flask) 或者 [Sinatra](http://sinatrarb.com/) ，你应该熟悉这个模式。

要定义路由，我们需要指定两件事:端点和处理程序。端点是客户端想要获取的路径。例如，如果用户想要获取我们书店中的所有书籍，他们可以获取`/books`端点。另一方面，处理程序决定我们如何向客户机提供数据。这是我们放置业务逻辑的地方，比如从数据库获取数据，验证用户输入，等等。

我们可以向客户端发送几种类型的响应，但是 RESTful APIs 通常以 JSON 格式给出响应。在 Gin 中要做到这一点，我们可以使用请求上下文提供的`JSON`方法。这个方法需要一个 [HTTP 状态代码](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)和一个 JSON 响应作为参数。
最后，我们可以通过简单地调用 Gin 实例的`Run`方法来运行我们的服务器。

为了进行测试，我们将通过运行以下命令来启动我们的服务器:

```
$ go run main.go

```

## 设置数据库

我们需要做的下一件事是建立我们的数据库模型。

Model 是一个类(或 Go 中的结构),它允许我们与数据库中的特定表进行通信。在 Gorm 中，我们可以通过定义 Go 结构来创建模型。该模型将包含表示数据库表中字段的属性。因为我们试图构建一个书店 API，所以让我们创建一个`Book`模型:

```
// models/book.go

package models

type Book struct {
  ID     uint   `json:"id" gorm:"primary_key"`
  Title  string `json:"title"`
  Author string `json:"author"`
}

```

我们的模型非常简单。每本书都应该有一个标题和一个字符串数据类型的作者姓名，以及一个 ID，这是在我们的数据库中区分每本书的唯一数字。

我们还使用反勾标注在每个字段上指定了[标签](https://golang.org/ref/spec#Struct_types)。由于 JSON 和 Go 有不同的命名约定，这允许我们在发送响应时将每个字段映射到不同的名称。
为了稍微组织一下我们的代码，我们可以把代码放在一个叫做模型的独立模块中。

接下来，我们需要创建一个名为`ConnectDatabase`的实用函数，它允许我们创建到数据库的连接，并迁移模型的模式。我们可以把它放在我们的`models`模块的`setup.go`文件中:

```
// models/setup.go

package models

import (
  "gorm.io/gorm"
  _ "gorm.io/driver/sqlite"
)

var DB *gorm.DB

func ConnectDatabase() {

        database, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})

        if err != nil {
                panic("Failed to connect to database!")
        }

        err = database.AutoMigrate(&Book{})
        if err != nil {
                return
        }

        DB = database
}

```

在这个函数中，我们用`gorm.Open`方法创建了一个新的连接。在这里，我们指定我们计划使用哪种数据库以及如何访问它。目前，Gorm 只支持四种类型的 SQL 数据库。出于学习目的，我们将使用 [SQLite](https://www.sqlite.org/index.html) 并将我们的数据存储在`test.db`文件中。为了将我们的服务器连接到数据库，我们需要导入数据库的驱动程序，它位于`gorm.io/driver/sqlite`模块中。

我们还需要检查连接是否创建成功。如果没有，它会将错误打印到控制台并终止服务器。

接下来，我们使用`AutoMigrate`迁移数据库模式。确保在您创建的每个模型上调用此方法。

最后，我们用数据库实例填充`DB`变量。我们将在控制器中使用这个变量来访问我们的数据库。

在`main.go`中，我们需要在运行应用程序之前调用以下函数:

```
package main

import (
  "github.com/gin-gonic/gin"

  "github.com/rahmanfadhil/gin-bookstore/models" // new
)

func main() {
  r := gin.Default()

  models.ConnectDatabase() // new

  r.Run()
}

```

## 设置 RESTful 路线

我们快到了！

我们需要做的最后一件事是实现我们的控制器。在上一节中，我们学习了如何在我们的`main.go`文件中创建一个路由处理器(即控制器)。然而，这种方法使得我们的代码更难维护。代替这样做，我们可以把我们的控制器放在一个叫做`controllers`的独立模块中。

### `Read`处理函数

首先，让我们实现`FindBooks`控制器:

```
// controllers/books.go

package controllers

import (
"net/http"

"github.com/gin-gonic/gin"
"github.com/rahmanfadhil/gin-bookstore/models"
)

// GET /books
// Get all books
func FindBooks(c *gin.Context) {
var books []models.Book
models.DB.Find(&books)

c.JSON(http.StatusOK, gin.H{"data": books})
}
```

这里，我们有一个`FindBooks`函数，它将从我们的数据库中返回所有的书籍。为了访问我们的模型和`DB`实例，我们需要在顶部导入我们的`models`模块。

接下来，我们可以在`main.go`中将我们的函数注册为路由处理器:

```
package main

import (
  "github.com/gin-gonic/gin"

  "github.com/rahmanfadhil/gin-bookstore/models"
  "github.com/rahmanfadhil/gin-bookstore/controllers" // new
)

func main() {
  r := gin.Default()

  models.ConnectDatabase()

  r.GET("/books", controllers.FindBooks) // new

  r.Run()
}

```

现在，让我们运行我们的服务器并点击`/books`端点:

```
{
  "data": []
}

```

如果您看到一个空数组，这意味着您的应用程序正在工作。我们得到这个是因为我们还没有创作出一本书。为此，让我们创建一个 create book 控制器。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### `Create`处理函数

要创建一本书，我们需要一个可以验证用户输入的模式，以防止我们获得无效数据:

```
type CreateBookInput struct {
  Title  string `json:"title" binding:"required"`
  Author string `json:"author" binding:"required"`
}

```

该模式与我们的模型非常相似。我们不需要`ID`属性，因为它将由数据库自动生成。

现在我们可以在控制器中使用该模式:

```
// POST /books
// Create new book
func CreateBook(c *gin.Context) {
  // Validate input
  var input CreateBookInput
  if err := c.ShouldBindJSON(&input); err != nil {
    c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
    return
  }

  // Create book
  book := models.Book{Title: input.Title, Author: input.Author}
  models.DB.Create(&book)

  c.JSON(http.StatusOK, gin.H{"data": book})
}

```

我们首先使用`ShouldBindJSON`方法验证请求体，并传递模式。如果数据无效，它会向客户端返回一个`400`错误，告诉他们哪些字段无效。否则，它将创建一本新书，保存到数据库中，并返回该书。

现在，我们可以在`main.go`中添加`CreateBook`控制器:

```
func main() {
  // ...

  r.GET("/books", controllers.FindBooks)
  r.POST("/books", controllers.CreateBook) // new

  r.Run()
}

```

因此，如果我们试图用这个请求体向`/books`端点发送一个 POST 请求:

```
{
  "title": "Start with Why",
  "author": "Simon Sinek"
}

```

响应应该是这样的:

```
{
  "data": {
    "id": 1,
    "title": "Start with Why",
    "author": "Simon Sinek"
  }
}

```

### 用于单个数据的`Create`处理函数

我们已经成功创作了我们的第一本书。让我们添加一个可以读取单本书的控制器:

```
// GET /books/:id
// Find a book
func FindBook(c *gin.Context) {  // Get model if exist
  var book models.Book

  if err := models.DB.Where("id = ?", c.Param("id")).First(&book).Error; err != nil {
    c.JSON(http.StatusBadRequest, gin.H{"error": "Record not found!"})
    return
  }

  c.JSON(http.StatusOK, gin.H{"data": book})
}

```

我们的`FindBook`控制器与`FindBooks`控制器非常相似。然而，我们只得到第一本书，它与我们从请求参数中得到的 ID 相匹配。我们还需要通过简单地将这本书包装在一个`if`语句中来检查它是否存在。

接下来，将其注册到您的`main.go`:

```
func main() {
  // ...

  r.GET("/books", controllers.FindBooks)
  r.POST("/books", controllers.CreateBook)
  r.GET("/books/:id", controllers.FindBook) // new

  r.Run()
}

```

要获得`id`参数，我们需要从 route path 中指定它，如上所示。

让我们运行服务器并获取`/books/1`来获取我们刚刚创建的书:

```
{
  "data": {
    "id": 1,
    "title": "Start with Why",
    "author": "Simon Sinek"
  }
}

```

### `Update`处理函数

到目前为止，一切顺利。现在让我们添加`UpdateBook`控制器来更新现有的书籍。但是在此之前，我们需要首先定义用于验证用户输入的模式:

```
type struct UpdateBookInput {
  Title  string `json:"title"`
  Author string `json:"author"`  
}

```

`UpdateBookInput`模式与我们的`CreateBookInput`非常相似，除了我们不需要让这些字段成为必填字段，因为用户不需要填写书的所有属性。

要添加控制器:

```
// PATCH /books/:id
// Update a book
func UpdateBook(c *gin.Context) {
  // Get model if exist
  var book models.Book
  if err := models.DB.Where("id = ?", c.Param("id")).First(&book).Error; err != nil {
    c.JSON(http.StatusBadRequest, gin.H{"error": "Record not found!"})
    return
  }

  // Validate input
  var input UpdateBookInput
  if err := c.ShouldBindJSON(&input); err != nil {
    c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
    return
  }

  models.DB.Model(&book).Updates(input)

  c.JSON(http.StatusOK, gin.H{"data": book})
}

```

首先，我们可以从`FindBook`控制器复制代码来抓取单本书，并确保它存在。找到书后，我们需要用`UpdateBookInput`模式验证用户输入。最后，我们使用`Updates`方法更新图书模型，并将更新后的图书数据返回给客户端。

将其注册到您的`main.go`:

```
func main() {
  // ...

  r.GET("/books", controllers.FindBooks)
  r.POST("/books", controllers.CreateBook)
  r.GET("/books/:id", controllers.FindBook)
  r.PATCH("/books/:id", controllers.UpdateBook) // new

  r.Run()
}

```

让我们来测试一下！向`/books/:id`端点发出一个`PATCH`请求来更新书名:

```
{
  "title": "The Infinite Game"
}

```

结果应该如下所示:

```
{
  "data": {
    "id": 1,
    "title": "The Infinite Game",
    "author": "Simon Sinek"
  }
}

```

### `Delete`处理函数

最后一步是实现`DeleteBook`功能:

```
// DELETE /books/:id
// Delete a book
func DeleteBook(c *gin.Context) {
  // Get model if exist
  var book models.Book
  if err := models.DB.Where("id = ?", c.Param("id")).First(&book).Error; err != nil {
    c.JSON(http.StatusBadRequest, gin.H{"error": "Record not found!"})
    return
  }

  models.DB.Delete(&book)

  c.JSON(http.StatusOK, gin.H{"data": true})
}

```

就像更新控制器一样，我们从请求参数中获取图书模型(如果它存在的话),并使用`Delete`方法从我们的数据库实例中删除它，这是我们从中间件中获取的。然后，返回`true`作为结果，因为没有理由将删除的图书数据返回给客户端:

```
func main() {
        // ...
        r := gin.Default()
        models.ConnectDatabase()

        r.GET("/books", controllers.FindBooks)
        r.POST("/books", controllers.CreateBook)
        r.GET("/books/:id", controllers.FindBook)
        r.PATCH("/books/:id", controllers.UpdateBook)
        r.DELETE("/books/:id", controllers.DeleteBook) // new

        err := r.Run()
        if err != nil {
                return
        }
}

```

让我们通过向`/books/1`端点发送一个`DELETE`请求来测试一下:

```
{
  "data": true
}

```

如果我们获取`/books`中的所有书籍，我们将再次看到一个空数组:

```
{
  "data": []
}

```

## 结论

Go 提供了两个所有开发人员都渴望并且所有编程语言都致力于实现的主要品质:简单性和性能。虽然这项技术可能不是每个开发团队的最佳选择，但它仍然是一个非常可靠的解决方案和一项值得学习的技能。

通过从头构建这个项目，我希望您对如何使用 Gin 和 Gorm 开发 RESTful API、它们如何协同工作以及如何实现 CRUD 特性有了基本的了解。仍然有很大的改进空间，比如用 JWT 认证用户，实现单元测试，用 Docker 容器化你的应用，如果你想更深入地挖掘，你可以尝试许多其他很酷的东西。