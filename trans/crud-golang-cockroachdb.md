# 与 Golang 和 CockroachDB - LogRocket 博客的 CRUD

> 原文：<https://blog.logrocket.com/crud-golang-cockroachdb/>

## 介绍

Golang 现在变得非常受欢迎。它速度快，语法简单易学，在后端开发人员中逐渐普及。今天，我们将学习如何使用 Golang 对一个同样流行的数据库 CockroachDB 执行创建、读取、更新和删除(CRUD)操作。

CockroachDB 是一个很容易建立的快速数据库。它很容易扩展(没有手动分片的复杂性)，自动重新平衡和修复自身，并且它还可以在您的集群中无缝地分布事务。这个项目的好选择。

## 初始设置

让我们建立我们的开发环境。首先，我们需要安装 Golang，因为这是我们将在这里使用的语言。点击此[链接，了解如何为您的操作系统](https://golang.org/doc/install)安装它。

接下来，我们需要在蟑螂云上创建一个帐户。我们可以安装并使用一个本地 cocroach db 集群，但是 CockroachDB cloud 使得开始使用 cocroach db 变得很容易，不需要任何额外的设置。点击此[链接创建一个账户](https://cockroachlabs.cloud/signup?referralId=docs_quickstart_free&_ga=2.52179374.641595194.1634584195-1256683923.1634365133)。

*注意，请务必记下您的连接字符串，尤其是密码，因为它只显示一次。本教程还假设你有一些用 Golang 和 SQL T2 编程的知识。*

现在，我们已经设置好了一切，让我们开始工作的应用程序。

## 项目概述

我们将要构建的应用程序是一个全栈的 web 应用程序，它允许我们从蟑螂数据库中获取、添加、更新和删除名字。这是完成后的应用程序的样子:

![Golang and CockroachDB Demo App](img/76c358b1f33bd498eb5508869a778dfb.png)

正如你所看到的，它只是一个简单的，丑陋的 web 应用程序，连接到我们的 CockroachDB 云数据库。事不宜迟，我们开始吧。

在项目文件夹中创建一个名为`server.go`的文件，并添加以下代码:

```
package main

import (
   "fmt"
   "log"
   "os"

   "github.com/gofiber/fiber/v2"
)

func main() {
   app := fiber.New()
   port := os.Getenv("PORT")
   if port == "" {
       port = "3000"
   }
   log.Fatalln(app.Listen(fmt.Sprintf(":%v", port)))
}

```

这里，我们首先导入一些需要的包，包括`os`模块、`log`模块，当然还有我们选择的 web 框架，在本例中是 Go Fiber。

如果你需要 Go Fiber 的入门，这里有一个[链接到文档](https://docs.gofiber.io)供你查阅。我们在这里做的是用`fiber.New`创建一个新的纤程对象，并将其设置为 app 变量。

接下来，我们检查环境变量中的`PORT`变量，如果不存在，我们将端口设置为`3000`。

然后我们调用`app.Listen`来启动一个监听我们端口的 HTTP 服务器。我们将它包装在一个`log.Fatalln()`中，以便在出现任何错误时将输出记录到控制台。在运行这段代码之前，让我们添加一些路线:

```
 func main() {
   app := fiber.New()

   app.Get("/", indexHandler) // Add this

   app.Post("/", postHandler) // Add this

   app.Post("/:name", putHandler) // Add this

   app.Delete("/:name", deleteHandler) // Add this

   port := os.Getenv("PORT")
   if port == "" {
       port = "3000"
   }
   log.Fatalln(app.Listen(fmt.Sprintf(":%v", port)))
}

```

如您所见，我添加了四个方法来处理我们的应用程序的 get、post、put 和 delete 操作，以及四个处理程序方法，每当有人访问这些路线时都会调用这些方法:

```
func indexHandler(c *fiber.Ctx) error {
   return c.SendString("Hello")
}
func postHandler(c *fiber.Ctx) error {
   return c.SendString("Hello")
}

func putHandler(c *fiber.Ctx) error {
   return c.SendString("Hello")
}
func deleteHandler(c *fiber.Ctx) error {
   return c.SendString("Hello")
}

```

将这些方法添加到主方法之上。目前，我们只是在所有路线上回复“你好”。

现在让我们运行我们的应用程序。在命令行上，运行命令`"go mod init"`，然后运行`"go mod tidy"`。这将创建一个`go.mod`文件，并获得应用程序需要的所有依赖项。

为了让我们在开发时有热重装，我们需要一个名为 Air 的 Go 包。
用`"go get github.com/cosmtrek/air"`导入。

通过运行`"go run github.com/cosmtrek/air"`启动您的应用程序。这将启动我们的 web 服务器，并监视项目目录中的所有文件。

![Start Up the Web Server](img/05fbc8b4017e748993bbe4cfcad72af2.png)

现在访问[http://localhost/<PORT&gt](http://localhost/<PORT&gt)；查看应用程序。

![CockroachDB Localhost](img/0372178fd4815a252c67d13929826c08.png)

让我们连接到我们的数据库。导航到您的 CockroachDB 云帐户并获取您的连接字符串。

![CockroachDB Connection String](img/75d34530f2428343be72699557bf8608.png)

点击**连接**并按照**连接字符串**选项卡上的说明获取连接字符串。在您的 main 方法中，在创建 fiber 应用程序的实例之前，添加以下代码。

首先，我们需要导入将用于连接数据库的 SQL 驱动程序。CockroachDB 是一个 SQL 数据库，所以我们可以使用任何 golang Postgres/SQL 数据库驱动程序连接到它。在我们的例子中，我们将使用`pq`驱动程序。将您的导入更新为:

```
import (
   "database/sql" // add this
   "fmt"
   "log"
   "os"
   _ "github.com/lib/pq" // add this

   "github.com/gofiber/fiber/v2"
)

```

`pq`驱动程序依赖于`database/sql`包，所以我们也导入它。我们不会直接使用`pq`驱动程序，所以我们在它的导入前面加了一个下划线。我们将使用`database/sql`包来执行所有的数据库操作，比如连接和执行查询。现在停止应用程序，运行`"go get github.com/lib/pq"`来安装`pq`驱动。

接下来，我们将添加代码来创建数据库连接，并更新我们的路由以将数据库连接传递给我们的处理程序，以便我们可以使用它来执行数据库查询:

```
 connStr := "your connection string"  // add this
   // Connect to database
   db, err := sql.Open("postgres", connStr)
   if err != nil {
       log.Fatal(err)
   }

   app := fiber.New()

   app.Get("/", func(c *fiber.Ctx) error {
       return indexHandler(c, db)
   })

   app.Post("/", func(c *fiber.Ctx) error {
       return postHandler(c, db)
   })

   app.Post("/:name", func(c *fiber.Ctx) error {
       return putHandler(c, db)
   })

   app.Delete("/:name", func(c *fiber.Ctx) error {
       return deleteHandler(c, db)
   })

```

如您所见，代替我们的处理程序，我们现在传递一个函数，该函数接受`fiber`上下文对象并将其与数据库连接一起传递给我们的处理程序。`fiber`上下文对象包含关于传入请求的所有内容，比如头、查询字符串参数、post 主体等。有关更多详细信息，请参考光纤文档。

现在，让我们更新我们的处理程序，以接受指向数据库连接的指针:

```
func indexHandler(c *fiber.Ctx, db *sql.DB) error {
   return c.SendString("Hello")
}

func postHandler(c *fiber.Ctx, db *sql.DB) error {
   return c.SendString("Hello")
}

func putHandler(c *fiber.Ctx, db *sql.DB) error {
   return c.SendString("Hello")
}

func deleteHandler(c *fiber.Ctx, db *sql.DB) error {
   return c.SendString("Hello")
}

```

再次启动应用程序，你会看到它运行没有错误。以下是到此为止的完整代码供参考:

```
package main

import (
   "database/sql"
   "fmt"
   "log"
   "os"

   _ "github.com/lib/pq"

   "github.com/gofiber/fiber/v2"
)

func indexHandler(c *fiber.Ctx, db *sql.DB) error {
   return c.SendString("Hello")
}

func postHandler(c *fiber.Ctx, db *sql.DB) error {
   return c.SendString("Hello")
}

func putHandler(c *fiber.Ctx, db *sql.DB) error {
   return c.SendString("Hello")
}

func deleteHandler(c *fiber.Ctx, db *sql.DB) error {
   return c.SendString("Hello")
}

func main() {
   connStr := "<your connection string>"
   // Connect to database
   db, err := sql.Open("postgres", connStr)
   if err != nil {
       log.Fatal(err)
   }

   app := fiber.New()

   app.Get("/", func(c *fiber.Ctx) error {
       return indexHandler(c, db)
   })

   app.Post("/", func(c *fiber.Ctx) error {
       return postHandler(c, db)
   })

   app.Post("/:name", func(c *fiber.Ctx) error {
       return putHandler(c, db)
   })

   app.Delete("/:name", func(c *fiber.Ctx) error {
       return deleteHandler(c, db)
   })

   port := os.Getenv("PORT")
   if port == "" {
       port = "3000"
   }
   log.Fatalln(app.Listen(fmt.Sprintf(":%v", port)))
}

```

## 亮出我们的路线处理程序

在我们开始充实处理程序之前，让我们用一些虚拟数据填充数据库。导航回 CockroachDB 云控制台，点击**连接**，并按照**命令行**标签中的指示从命令行访问您的数据库。

![CockroachDB Command Line Tab](img/36cea377b2611e669f1a536da76cd55b.png)

每当您创建一个集群时，CockroachDB 都会创建一个名为`defaultdb`的默认数据库。您可以将其更改为您想要的任何值，但是对于我们的用例，我们将使用它。在命令行中连接到数据库 shell 后，执行以下 SQL 命令:

```
create table users (Name varchar(225));
insert into users values('John');

```

这将创建一个名为`users`的表，其中一列是用户名，并将一个名称插入数据库。

退出终端，让我们开始充实我们的处理程序方法。将 get 处理程序修改如下:

```
func indexHandler(c *fiber.Ctx, db *sql.DB) error {
   var res string
   var users []string
   rows, err := db.Query("SELECT * FROM users")
   defer rows.Close()
   if err != nil {
       log.Fatalln(err)
       c.JSON("An error occured")
   }
   for rows.Next() {
       rows.Scan(&res)
       users = append(users, res)
   }
   return c.Render("index", fiber.Map{
       "Users": users,
   })
}

```

好吧，有很多要理解！首先，我们使用`db`对象通过`db.Query()`函数在数据库上执行 SQL 查询。这将向我们返回与我们的查询匹配的所有行以及可能发生的任何错误。我们调用`defer rows.Close()`来关闭这些行，并在函数完成时阻止进一步的枚举。

我们检查是否有错误，然后遍历所有的行，每次迭代都调用`rows.Next()`，并使用`rows.Scan()`方法将该行的当前值赋给`res`变量，我们将其定义为`string`。然后，我们将`res`的值追加到用户数组中。

注意`rows.Scan()`要求您传递一个数据类型与数据库中存储的数据相对应的变量。例如，如果您有多个列，比如姓名和年龄，您可以传入一个包含姓名和年龄字段的结构。更多信息请参考 [SQL 文档](https://pkg.go.dev/database/sql#Rows.Scan)。

然后，我们返回索引视图，并将用户数组传入其中。谈到视图，让我们配置我们的 fiber 应用程序来服务我们的 HTML 视图。这样修改你的主方法:

```
   engine := html.New("./views", ".html")
   app := fiber.New(fiber.Config{
       Views: engine,
   })

```

我们配置我们的 fiber 应用程序使用 HTML 模板引擎，并传入`./views`作为视图所在位置的路径。停止应用程序，用`go get github.com/gofiber/template/html`安装 HTML 引擎，并在你的项目根目录下创建一个名为`views`的文件夹。在`views`中，创建一个名为`index .html`的文件，并添加以下代码:

```
<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta http-equiv="X-UA-Compatible" content="IE=edge">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <title>Document</title>
</head>
<body>
   <h1>Hello world from fiber</h1>
   {{range .Users}}
   <div>
       <h2>{{.}}</h2>
   {{end}}
</body>
</html>

```

这将遍历我们传入的 users 数组，并显示用户名。再次启动应用程序，您应该会看到我们添加到数据库中的用户。

![CockroachDB Loaded User](img/2e56f12b3c1d2a00430674b383af62b2.png)

*注意，如果您在此时启动您的应用程序时出现任何错误，如* `open $HOME/.postgresql/root.crt: no such file or directory` *，只需修改您的连接字符串并删除* `sslmode` *和* `sslrootcert` *查询字符串参数，它应该就可以正常工作了。*

![Modify Connection String](img/4839fe94593ae06a3dc73e6147ff7d9e.png)

对于我们的其他处理程序，修改如下:

```
type user struct {
   Name string
}

func postHandler(c *fiber.Ctx, db *sql.DB) error {
   newUser := user{}
   if err := c.BodyParser(&newUser); err != nil {
       log.Printf("An error occured: %v", err)
       return c.SendString(err.Error())
   }
   if newUser.Name != ""{
   _, err := db.Exec("INSERT into users VALUES ($1)", newUser.Name)
   if err != nil {
       log.Fatalf("An error occured while executing query: %v", err)
   }
   }

   return c.Redirect("/")
}

func putHandler(c *fiber.Ctx, db *sql.DB) error {
   oldName := c.Params("name")
   newName := user{}

   if err := c.BodyParser(&newName); err != nil {
       log.Printf("An error occured: %v", err)
       return c.SendString(err.Error())
   }
   db.Exec("UPDATE users SET Name=$1 WHERE Name=$2", newName.Name, oldName)
   return c.Redirect("/")
}

func deleteHandler(c *fiber.Ctx, db *sql.DB) error {
   userToDelete := c.Params("name")

   db.Exec("DELETE from users WHERE Name=$1", userToDelete)
   return c.SendString("deleted")
}

```

首先，我们创建一个结构来表示我们的用户。然后，在我们的 post 处理程序中，我们从请求体中获取想要插入到数据库中的用户名。然后我们使用`db.Exec()`方法执行一个 SQL 查询，将新用户添加到数据库中。然后我们重定向回主页。

*注意，每当我们期望数据库查询的结果时，我们使用* `db.Query()` *方法，当我们不期望时，使用* `db.Exec()` *方法。同样，请参考此处的 [SQL 文档](https://pkg.go.dev/database/sql#Rows.Scan)了解更多信息。*

对于 put 处理程序，我们从请求查询字符串参数中获取旧名称，从请求正文中获取更新后的名称。然后，我们执行一个查询，用数据库中的新名称替换旧名称。最后，我们重定向回主页。

对于我们的删除处理程序，我们从请求查询字符串参数中获取要删除的名称，并执行一个查询从我们的数据库中删除该名称，然后我们发送回一个表示`"deleted"`的字符串。我们返回这个字符串，这样我们就知道函数已经成功完成。

现在修改您的`index.html`文件，如下所示:

```
<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta http-equiv="X-UA-Compatible" content="IE=edge">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <title>My Cool app</title>
</head>
<body>
   <h1>Hello world from fiber</h1>
   {{range .Users}}
   <div>
       <h2>{{.}}</h2>
        <form action="/{{.}}", method="POST">
           <input type="text" id="name" name="Name">
           <button type="submit">Update name</button>
       </form>
       <button onclick = "removeFromDb('{{.}}')" >Delete {{.}}</button>
   </div>
   {{end}}
   <form action="/" method="post">
       <input type="text" name="Name">
       <button type="submit">Add to db</button>
   </form>
   <script src="index.js"></script>
</body>
</html>

```

在每个名字之后，我们有一个更新名字的表单和一个从数据库中删除名字的按钮。然后我们有了一个表单，可以用来向数据库中添加一个新名字。注意，我们的删除按钮的`onclick`属性调用了一个名为`removeFromDb`的函数。这是在我们正在文件底部导入的`index.js`脚本中定义的。下面是`index.js`文件的样子:

```
function removeFromDb(name){
   fetch(`/${name}`, {method: "Delete"}).then(res =>{
       if (res.status == 200){
           window.location.pathname = "/"
       }
   })
}

```

它只包含一个向数据库发送删除请求的函数，如果请求成功完成，就刷新页面。现在，为了让纤程服务于这个 javascript 文件，将它添加到您的主函数中:

```
   app.Static("/", "./public") // add this before starting the app
   log.Fatalln(app.Listen(fmt.Sprintf(":%v", port)))

```

这告诉 fiber 服务于公共目录中的所有静态文件，这是我们放置`index.js`文件的地方。确保创建此文件夹并添加文件。

下面是完整的`server.go`文件代码供参考:

```
package main

import (
   "database/sql"
   "fmt"
   "log"
   "os"

   _ "github.com/lib/pq"

   "github.com/gofiber/fiber/v2"
   "github.com/gofiber/template/html"
)

func indexHandler(c *fiber.Ctx, db *sql.DB) error {
   var res string
   var users []string
   rows, err := db.Query("SELECT * FROM users")
   defer rows.Close()
   if err != nil {
       log.Fatalln(err)
       c.JSON("An error occured")
   }
   for rows.Next() {
       rows.Scan(&res)
       users = append(users, res)
   }
   return c.Render("index", fiber.Map{
       "Users": users,
   })
}

type user struct {
   Name string
}

func postHandler(c *fiber.Ctx, db *sql.DB) error {
   newUser := user{}
   if err := c.BodyParser(&newUser); err != nil {
       log.Printf("An error occured: %v", err)
       return c.SendString(err.Error())
   }
   if newUser.Name != "" {
       _, err := db.Exec("INSERT into users VALUES ($1)", newUser.Name)
       if err != nil {
           log.Fatalf("An error occured while executing query: %v", err)
       }
   }

   return c.Redirect("/")
}

func putHandler(c *fiber.Ctx, db *sql.DB) error {
   oldName := c.Params("name")
   newName := user{}

   if err := c.BodyParser(&newName); err != nil {
       log.Printf("An error occured: %v", err)
       return c.SendString(err.Error())
   }
   db.Exec("UPDATE users SET Name=$1 WHERE Name=$2", newName.Name, oldName)
   return c.Redirect("/")
}

func deleteHandler(c *fiber.Ctx, db *sql.DB) error {
   userToDelete := c.Params("name")

   db.Exec("DELETE from users WHERE Name=$1", userToDelete)
   return c.SendString("deleted")
}

func main() {
   connStr := "<your connection string>"
   // Connect to database
   db, err := sql.Open("postgres", connStr)
   if err != nil {
       log.Fatal(err)
   }

   engine := html.New("./views", ".html")
   app := fiber.New(fiber.Config{
       Views: engine,
   })

   app.Get("/", func(c *fiber.Ctx) error {
       return indexHandler(c, db)
   })

   app.Post("/", func(c *fiber.Ctx) error {
       return postHandler(c, db)
   })

   app.Post("/:name", func(c *fiber.Ctx) error {
       return putHandler(c, db)
   })

   app.Delete("/:name", func(c *fiber.Ctx) error {
       return deleteHandler(c, db)
   })

   port := os.Getenv("PORT")
   if port == "" {
       port = "3000"
   }

   app.Static("/", "./public")

   log.Fatalln(app.Listen(fmt.Sprintf(":%v", port)))
}

```

如果你正确地遵循了上面的教程，你的应用应该是这样的:

![Final App Demo CockroachDB](img/78ae1fffb80e5bd3ba723bf6fe5d60d8.png)

## 结论

在本文中，我们已经了解了如何在云上建立 CockroachDB 数据库，连接到它并对它执行一些 CRUD 操作，以及如何使用 Golang web 框架 Go Fiber。

现在作为一项任务，尝试更新应用程序看起来更漂亮(添加一些 CSS)。遇到卡时，请参考有关光纤的文档。感谢您的阅读，再见！✌️

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)