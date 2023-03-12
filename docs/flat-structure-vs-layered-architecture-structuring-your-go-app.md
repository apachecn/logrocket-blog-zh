# 构建您的 Golang 应用程序:平面结构与分层架构

> 原文：<https://blog.logrocket.com/flat-structure-vs-layered-architecture-structuring-your-go-app/>

构建一个伟大的应用程序从它的结构开始。一个应用程序的结构为应用程序的开发定下了基调，从一开始就把它做好是很重要的。

Go 是一种相对简单的语言，它对应用程序应该如何构造没有任何看法。在本文中，我们将探索两种构建 Go 应用程序的主要方法。

在我们继续之前，重要的是要注意没有一种结构是适合所有应用的。我们将涉及的一些内容可能不适合您的库或项目。但是，您应该了解可以使用什么，这样您就可以轻松地决定如何最好地构建您的应用程序。

## 构建扁平化结构的 Go 应用

这种构建项目的方法将所有文件和包保存在同一个目录中。

最初，这可能看起来是一种糟糕的项目组织方式，但是有些构建非常适合它。使用平面结构的示例项目将具有以下结构:

```
flat_app/
  main.go
  lib.go
  lib_test.go
  go.mod
  go.sum

```

使用这种结构的主要优点是易于使用。所有创建的包都位于同一个目录中，因此在需要时可以很容易地修改和使用它们。

这种结构最适合用于构建库、简单脚本或简单 CLI 应用程序。 [HttpRouter](https://github.com/julienschmidt/httprouter) ，一个广泛用于构建 API 的路由库，使用了类似的平面结构。

然而，一个主要的缺点是，随着项目变得越来越复杂，它将变得几乎不可能维护。例如，像这样的结构不适合构建 REST API，因为 API 有不同的组件使它能够很好地工作，比如控制器、模型、配置和中间件。这些组件不应该都保存在一个文件目录中。

理想情况下，在引导应用程序时，应该使用平面结构。一旦你对混乱感到不舒服，你可以升级到任何其他的项目结构。

## 使用平面结构构建简单的 API

为了演示平面结构，让我们为笔记应用程序构建一个 API。

通过运行以下命令为此项目创建一个新目录:

```
mkdir notes_api_flat

```

这个目录被命名为`notes_api_flat`,因为这个应用程序可能会使用其他结构，我们将在后面介绍。

现在，初始化项目:

```
go mod init github.com/username/notes_api_flat

```

这个应用程序将允许用户存储笔记。我们将使用 [SQLite3](https://www.sqlite.org/index.html) 存储笔记，使用 [Gin](https://gopkg.in/gin-gonic/gin.v1) 进行路由。运行下面的代码片段来安装它们:

```
go get github.com/mattn/go-sqlite3
go get github.com/gin-gonic/gin

```

接下来，创建以下文件:

*   `main.go`:应用程序的入口点
*   `models.go`:管理对数据库的访问
*   `migration.go`:管理创建表格

创建它们之后，文件夹结构应该如下所示:

```
notes_api_flat/
  go.mod
  go.sum
  go.main.go
  migration.go
  models.go

```

### 写作`migration.go`

将以下内容添加到`migration.go`中，以创建存储我们笔记的表格。

```
package main
import (
  "database/sql"
  "log"
)
const notes = `
  CREATE TABLE IF NOT EXISTS notes (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title VARCHAR(64) NOT NULL,
    body MEDIUMTEXT NOT NULL,
    created_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL
  )
`
func migrate(dbDriver *sql.DB) {
  statement, err := dbDriver.Prepare(notes)
  if err == nil {
    _, creationError := statement.Exec()
    if creationError == nil {
      log.Println("Table created successfully")
    } else {
      log.Println(creationError.Error())
    }
  } else {
    log.Println(err.Error())
  }
}

```

在上面的代码片段中，我们将包声明为`main`。注意，我们不能将它设置为与`main.go`中的不同，因为它们在同一个目录中。因此，每个文件中所做的一切都是全局可用的，因为所有文件都位于同一个包中。

请注意，我们导入了与 SQL 交互所需的包，以及记录任何可能发生的错误的日志包。

接下来，我们有一个 SQL 查询，它创建一个包含以下字段的 notes 表:`id`、`title`、`body`、`created_at`和`updated_at`。

最后，我们定义了函数`migrate`，它执行上面写的查询，并打印过程中出现的任何错误。

### 正在创建`models.go`

将以下内容添加到`models.go`:

```
package main
import (
  "log"
  "time"
)
type Note struct {
  Id        int       `json:"id"`
  Title     string    `json:"title"`
  Body      string    `json:"body"`
  CreatedAt time.Time `json:"created_at"`
  UpdatedAt time.Time `json:"updated_at"`
}
func (note *Note) create(data NoteParams) (*Note, error) {
  var created_at = time.Now().UTC()
  var updated_at = time.Now().UTC()
  statement, _ := DB.Prepare("INSERT INTO notes (title, body, created_at, updated_at) VALUES (?, ?, ?, ?)")
  result, err := statement.Exec(data.Title, data.Body, created_at, updated_at)
  if err == nil {
    id, _ := result.LastInsertId()
    note.Id = int(id)
    note.Title = data.Title
    note.Body = data.Body
    note.CreatedAt = created_at
    note.UpdatedAt = updated_at
    return note, err
  }
  log.Println("Unable to create note", err.Error())
  return note, err
}
func (note *Note) getAll() ([]Note, error) {
  rows, err := DB.Query("SELECT * FROM notes")
  allNotes := []Note{}
  if err == nil {
    for rows.Next() {
      var currentNote Note
      rows.Scan(
        &currentNote.Id,
        &currentNote.Title,
        &currentNote.Body,
        &currentNote.CreatedAt,
        &currentNote.UpdatedAt)
      allNotes = append(allNotes, currentNote)
    }
    return allNotes, err
  }
  return allNotes, err
}
func (note *Note) Fetch(id string) (*Note, error) {
  err := DB.QueryRow(
    "SELECT id, title, body, created_at, updated_at FROM notes WHERE id=?", id).Scan(
    &note.Id, &note.Title, &note.Body, &note.CreatedAt, &note.UpdatedAt)
  return note, err
}

```

`model`包含注释结构定义和允许注释与数据库交互的三种方法。注释结构包含注释可以拥有的所有数据，并且应该与数据库中的列同步。

`create`方法负责创建一个新的注释，并返回新创建的注释和过程中出现的任何错误。

`getAll`方法将数据库中的所有笔记作为一个切片，并返回该过程中出现的任何错误。

`Fetch`方法从它的`id`中获得一个特定的音符。这些方法以后都可以直接用来获取笔记。

### 在 Go 中完成 API

API 中剩下的最后一部分是路由。修改`main.go`以包含以下代码:

```
package main
import (
  "database/sql"
  "log"
  "net/http"
  "github.com/gin-gonic/gin"
  _ "github.com/mattn/go-sqlite3"
)
// Create this to store instance to SQL
var DB *sql.DB
func main() {
  var err error
  DB, err = sql.Open("sqlite3", "./notesapi.db")
  if err != nil {
    log.Println("Driver creation failed", err.Error())
  } else {
    // Create all the tables
    migrate(DB)
    router := gin.Default()
    router.GET("/notes", getAllNotes)
    router.POST("/notes", createNewNote)
    router.GET("/notes/:note_id", getSingleNote)
    router.Run(":8000")
  }
}
type NoteParams struct {
  Title string `json:"title"`
  Body  string `json:"body"`
}
func createNewNote(c *gin.Context) {
  var params NoteParams
  var note Note
  err := c.BindJSON(&params)
  if err == nil {
    _, creationError := note.create(params)
    if creationError == nil {
      c.JSON(http.StatusCreated, gin.H{
        "message": "Note created successfully",
        "note":    note,
      })
    } else {
      c.String(http.StatusInternalServerError, creationError.Error())
    }
  } else {
    c.String(http.StatusInternalServerError, err.Error())
  }
}
func getAllNotes(c *gin.Context) {
  var note Note
  notes, err := note.getAll()
  if err == nil {
    c.JSON(http.StatusOK, gin.H{
      "message": "All Notes",
      "notes":   notes,
    })
  } else {
    c.String(http.StatusInternalServerError, err.Error())
  }
}
func getSingleNote(c *gin.Context) {
  var note Note
  id := c.Param("note_id")
  _, err := note.Fetch(id)
  if err == nil {
    c.JSON(http.StatusOK, gin.H{
      "message": "Single Note",
      "note":    note,
    })
  } else {
    c.String(http.StatusInternalServerError, err.Error())
  }
}

```

在这里，我们导入所有需要的包。注意最后的导入:

```
"github.com/mattn/go-sqlite3"

```

这个代码片段是使用 SQLite 所必需的，尽管它没有被直接使用。main 函数首先初始化数据库，如果失败就退出。数据库实例存储在`DB`全局变量中，以便于访问。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

接下来，我们通过调用在`migrations.go`中定义的`migrate`函数来迁移表。

我们不需要导入任何东西来使用这个函数，因为它在`main`包中并且全球可用。

接下来，定义路线。我们只需要三条路线:

*   对`/notes`的一个`GET`请求，该请求检索已经创建并存储在数据库中的所有笔记
*   对`/notes`的`POST`请求创建一个新的便笺，并将其保存到数据库中
*   对`/note/:note_id`的`GET`请求通过其`id`来检索便笺

这些路由有单独的处理程序，这些处理程序使用 note 模型来执行所需的数据库操作。

### 使用扁平结构的好处

我们可以看到，通过使用扁平结构，我们可以快速构建简单的 API，而无需管理多个包。这对库作者特别有用，因为大多数模块都需要成为基础包的一部分。

### 使用扁平结构的缺点

尽管使用扁平结构有很多好处，但它并不是构建 API 的最佳选择。首先，这种结构非常有限，它自动地使函数和变量全局可用。

也没有真正的关注点分离。我们试图将模型从迁移和路由中分离出来，但是这几乎是不可能的，因为它们仍然可以彼此直接访问。这可能会导致一个文件修改了一个不应该修改的项目，或者在另一个文件不知道的情况下，所以这个应用程序不容易维护。

我们将讨论的下一个结构解决了使用扁平结构的许多问题。

## 在 Go 中使用分层架构(经典的 MVC 结构)

这种结构根据文件的功能对文件进行分组。处理与数据库(模型)通信的包的分组和存储方式不同于处理来自路由的请求的包。

让我们看看分层的体系结构是什么样子的:

```
layered_app/
  app/
    models/
      User.go         
    controllers/
      UserController.go
  config/
    app.go
  views/
    index.html
  public/
    images/
      logo.png
  main.go
  go.mod
  go.sum

```

注意分离。因此，很容易维护以这种方式构建的项目，并且使用 MVC 结构可以减少代码中的混乱。

尽管分层架构对于构建简单的库并不理想，但它非常适合构建 API 和其他大型应用程序。这通常是使用 [Revel](https://revel.github.io/) 构建的应用程序的默认结构，这是一个用于构建 REST APIs 的流行 Go 框架。

## 使用分层架构更新 Go 应用程序

既然您已经看到了一个使用分层架构的示例项目，让我们将我们的项目从平面结构升级到 MVC 结构。

创建一个名为`notes_api_layered`的新文件夹，并通过运行下面的代码片段在其中初始化一个 Go 模块:

```
mkdir notes_api_layered
go mod init github.com/username/notes_api_layered

```

安装所需的 SQLite 和 Gin 包。

```
go get github.com/mattn/go-sqlite3
go get github.com/gin-gonic/gin

```

现在，更新项目文件夹结构，如下所示:

```
notes_api_layered/
  config/
    db.go
  controllers/
    note.go
  migrations/
    main.go
    note.go
  models/
    note.go
  go.mod
  go.sum
  main.go

```

正如你在新的文件夹结构中看到的，所有的文件都根据它们的功能进行了排列。所有模型都位于模型的目录中，对于迁移、控制器和配置也是如此。

接下来，我们将在扁平结构实现中所做的工作重构到这个新结构中。

从数据库配置开始，将以下内容添加到`config/db.go`:

```
package config
import (
  "database/sql"
  _ "github.com/mattn/go-sqlite3"
)
var DB *sql.DB
func InitializeDB() (*sql.DB, error) {
  // Initialize connection to the database
  var err error
  DB, err = sql.Open("sqlite3", "./notesapi.db")
  return DB, err
}

```

在这里，我们声明了一个名为`config`的包，并导入了所有相关的库来实现与数据库的通信。注意，我们可以声明多个包，因为它们不在同一个目录中。

接下来，我们创建一个`DB`变量来保存到数据库的连接，因为每个模型拥有不同的数据库实例并不理想。注意:变量名或函数名以大写字母开头意味着它们应该被导出。

然后我们声明并导出一个`InitializeDB`函数，它打开数据库并将数据库引用存储在`DB`变量中。

一旦我们完成了数据库设置，我们接下来就要进行迁移。我们在迁移文件夹中有两个文件:`main.go`和`note.go`。

`main.go`处理加载要执行的查询，然后执行它们，而`note.go`包含特定于 notes 表的 SQL 查询。

如果我们有其他表，例如一个用于注释的表，那么它们也有一个迁移文件，其中包含创建注释表的查询。

现在，将以下内容添加到`migrations/note.go`:

```
package migrations
const Notes = `
CREATE TABLE IF NOT EXISTS notes (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title VARCHAR(64) NOT NULL,
  body MEDIUMTEXT NOT NULL,
  created_at TIMESTAMP NOT NULL,
  updated_at TIMESTAMP NOT NULL
)
`

```

更新`migrations/main.go`以包括:

```
package migrations
import (
  "database/sql"
  "log"
  "github.com/username/notes_api_layered/config"
)
func Run() {
  // Migrate notes
  migrate(config.DB, Notes)
  // Other migrations can be added here.
}
func migrate(dbDriver *sql.DB, query string) {
  statement, err := dbDriver.Prepare(query)
  if err == nil {
    _, creationError := statement.Exec()
    if creationError == nil {
      log.Println("Table created successfully")
    } else {
      log.Println(creationError.Error())
    }
  } else {
    log.Println(err.Error())
  }
}

```

如前所述，`migrations/main.go`处理从单个迁移文件加载查询，并在调用`Run`方法时运行它。`migrate`是一个私有函数，不能在此模块之外使用。唯一对外输出的功能是`Run`。

运行迁移之后，我们需要更新模型。这里分层结构实现和平面结构实现之间的变化非常小。

所有外部使用的方法都应该导出，所有对`DB`的引用都应该改为`config.DB`。

应用这些更改后，`models/note.go`应该如下所示:

```
package models
import (
  "log"
  "time"
  "github.com/username/notes_api_layered/config"
)
type Note struct {
  Id        int       `json:"id"`
  Title     string    `json:"title"`
  Body      string    `json:"body"`
  CreatedAt time.Time `json:"created_at"`
  UpdatedAt time.Time `json:"updated_at"`
}
type NoteParams struct {
  Title string
  Body  string
}
func (note *Note) Create(data NoteParams) (*Note, error) {
  var created_at = time.Now().UTC()
  var updated_at = time.Now().UTC()
  statement, _ := config.DB.Prepare("INSERT INTO notes (title, body, created_at, updated_at) VALUES (?, ?, ?, ?)")
  result, err := statement.Exec(data.Title, data.Body, created_at, updated_at)
  if err == nil {
    id, _ := result.LastInsertId()
    note.Id = int(id)
    note.Title = data.Title
    note.Body = data.Body
    note.CreatedAt = created_at
    note.UpdatedAt = updated_at
    return note, err
  }
  log.Println("Unable to create note", err.Error())
  return note, err
}
func (note *Note) GetAll() ([]Note, error) {
  rows, err := config.DB.Query("SELECT * FROM notes")
  allNotes := []Note{}
  if err == nil {
    for rows.Next() {
      var currentNote Note
      rows.Scan(
        &currentNote.Id,
        &currentNote.Title,
        &currentNote.Body,
        &currentNote.CreatedAt,
        &currentNote.UpdatedAt)
      allNotes = append(allNotes, currentNote)
    }
    return allNotes, err
  }
  return allNotes, err
}
func (note *Note) Fetch(id string) (*Note, error) {
  err := config.DB.QueryRow(
    "SELECT id, title, body, created_at, updated_at FROM notes WHERE id=?", id).Scan(
    &note.Id, &note.Title, &note.Body, &note.CreatedAt, &note.UpdatedAt)
  return note, err
}

```

我们已经声明了一个新的包`models`，并且我们从`github.com/username/notes_api_layered/config`导入了配置。这样，一旦调用了`InitializeDB`函数，我们就可以访问已经初始化的`DB`。

对控制器的修改也很小，主要是导出函数和从新创建的模型导入模型。

更改以下代码片段:

```
var note Note
var params NoteParams

```

对这一个:

```
var note models.Note
var params models.NoteParams

```

修改后，控制器将如下所示:

```
package controllers
import (
  "net/http"
  "github.com/gin-gonic/gin"
  "github.com/username/notes_api_layered/models"
)
type NoteController struct{}
func (_ *NoteController) CreateNewNote(c *gin.Context) {
  var params models.NoteParams
  var note models.Note
  err := c.BindJSON(&params)
  if err == nil {
    _, creationError := note.Create(params)
    if creationError == nil {
      c.JSON(http.StatusCreated, gin.H{
        "message": "Note created successfully",
        "note":    note,
      })
    } else {
      c.String(http.StatusInternalServerError, creationError.Error())
    }
  } else {
    c.String(http.StatusInternalServerError, err.Error())
  }
}
func (_ *NoteController) GetAllNotes(c *gin.Context) {
  var note models.Note
  notes, err := note.GetAll()
  if err == nil {
    c.JSON(http.StatusOK, gin.H{
      "message": "All Notes",
      "notes":   notes,
    })
  } else {
    c.String(http.StatusInternalServerError, err.Error())
  }
}
func (_ *NoteController) GetSingleNote(c *gin.Context) {
  var note models.Note
  id := c.Param("note_id")
  _, err := note.Fetch(id)
  if err == nil {
    c.JSON(http.StatusOK, gin.H{
      "message": "Single Note",
      "note":    note,
    })
  } else {
    c.String(http.StatusInternalServerError, err.Error())
  }
}

```

在上面的代码片段中，我们将函数转换为方法，这样就可以通过`NoteController.Create`而不是`controller.Create`来访问它们。这是为了说明拥有多个控制器的可能性，这是大多数现代应用的情况。

最后，我们更新`main.go`来反映重构:

```
package main
import (
  "log"
  "github.com/gin-gonic/gin"
  "github.com/username/notes_api_layered/config"
  "github.com/username/notes_api_layered/controllers"
  "github.com/username/notes_api_layered/migrations"
)
func main() {
  _, err := config.InitializeDB()
  if err != nil {
    log.Println("Driver creation failed", err.Error())
  } else {
    // Run all migrations
    migrations.Run()

    router := gin.Default()

    var noteController controllers.NoteController
    router.GET("/notes", noteController.GetAllNotes)
    router.POST("/notes", noteController.CreateNewNote)
    router.GET("/notes/:note_id", noteController.GetSingleNote)
    router.Run(":8000")
  }
}

```

在重构之后，我们有了一个精简的`main`包，它导入了所需的包:`config`、`controllers`和`models`。然后，它通过调用`config.InitializeDB()`来初始化数据库。

现在我们可以继续路由。应该更新路由以使用新创建的音符控制器来处理请求。

### 在 Go 中使用分层结构的好处

使用分层结构的最大好处是，从目录结构中，您可以了解每个文件和/或文件夹在做什么。关注点也有明确的分离，因为每个包都有一个单独的功能要执行。

有了分层架构，这个项目很容易扩展。例如，如果添加了一个允许用户评论笔记的新特性，那么实现起来会很容易，因为所有的基础工作都已经完成了。在这种情况下，只需要创建`model`、`migration`和`controller`，然后更新路线，viola！该功能已添加。

### 使用分层结构的缺点

对于简单的项目来说，这种架构可能是多余的，并且在实现它之前需要大量的计划。

## 结论

总之，我们已经看到，为您的 Go 应用程序选择一个结构取决于您正在构建什么，项目有多复杂，以及您打算在它上面工作多长时间。

对于创建简单的项目，使用平面结构就可以了。然而，当项目更复杂时，重要的是后退一步重新思考应用程序，并为应用程序选择更适合的结构。

构建 Go 应用程序时常用的其他结构是[域驱动开发](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjpjKnykPDyAhXj1uAKHTgPAMcQFnoECAcQAQ&url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FDomain-driven_design&usg=AOvVaw1CJc4fwlGZ4rOeLQPGbdCz)和[六角形架构](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwiU3cjokPDyAhVq5OAKHfQXC8gQFnoECAYQAQ&url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FHexagonal_architecture_(software)&usg=AOvVaw3aAMLnpZDa8Dvev8I1rDCH)。如果您的应用程序继续扩展，那么学习这些知识也是值得的。