# 如何用 Docker、PostgreSQL 和 go-chi 构建 RESTful API

> 原文：<https://blog.logrocket.com/how-to-build-a-restful-api-with-docker-postgresql-and-go-chi/>

[Go-chi](https://github.com/go-chi/chi/) 是一个轻量级路由器库，用于在 Go 中构建 HTTP 服务。当您希望获得模块化请求处理的好处，而不需要使用成熟的 web 框架所带来的电池时，它尤其有用。

在本教程中，我们将使用 go-chi、PostgreSQL 和 Docker 构建一个容器化的桶列表 API。更具体地说，我们的 API 将公开以下端点:

*   `POST /items`向列表中添加新项目
*   `GET /items`获取列表中所有现有的项目
*   `GET /items/{itemId}`使用 ID 从列表中提取单个项目
*   `PUT /items/{itemId}`更新现有项目
*   `DELETE /items/{itemId}`从列表中删除项目

## 先决条件

要继续学习本教程，您需要:

## 入门指南

首先，在您的首选位置创建项目文件夹，并初始化 Go 模块:

```
$ mkdir bucketeer && cd bucketeer
$ go mod init gitlab.com/idoko/bucketeer
```

你通常会发现 Go 项目使用它们的 GitHub 或 GitLab 路径作为它们的模块名，尽管你也可以用一个自定义域来设置它。

运行以下命令安装我们的应用程序依赖项，包括:

```
$ go get github.com/go-chi/chi github.com/go-chi/render github.com/lib/pq
```

在项目目录中，创建所需的文件夹和文件以匹配以下布局:

```
├── db
│   ├── db.go
│   └── item.go
├── handler
│   ├── errors.go
│   ├── handler.go
│   └── items.go
├── models
│   └── item.go
├── .env
├── docker-compose.yml
├── Dockerfile
├── go.mod
├── go.sum
├── main.go
└── README.md
```

让我们看看上面的一些目录和文件:

*   这里的代码负责直接与我们的数据库交互。这样，数据库引擎就与应用程序的其余部分适当地分离开来
*   `handler`:`handler`包使用`chi`创建并处理我们的 API 路由
*   `models`:包含 Go [结构](https://tour.golang.org/moretypes/2)，这些结构可以绑定到数据库对象或者转换成等价的 JSON 格式
*   `Dockerfile`定义了启动和运行 API 服务器所需的基本映像和命令。`docker-compose.yml`定义了我们的应用依赖关系(服务器使用`Dockerfile`，数据库使用官方 postgres docker 映像)。Docker 网站上有关于[Docker 文件](https://docs.docker.com/compose/compose-file/)和 [docker-compose](https://docs.docker.com/compose/compose-file/) 的详细参考
*   `.env`:它保存了我们的应用程序环境变量(比如数据库凭证)
*   `main.go`是我们的应用入口点。它将负责读取环境变量、设置数据库以及启动和停止 API 服务器

## 用 docker-compose 分解服务

让我们设置`Dockerfile`来将 API 服务器构建到一个二进制文件中，公开服务器端口，并在启动时执行二进制文件。在您喜欢的编辑器中打开它，并将下面的代码添加到其中:

```
FROM golang:1.14.6-alpine3.12 as builder
COPY go.mod go.sum /go/src/gitlab.com/idoko/bucketeer/
WORKDIR /go/src/gitlab.com/idoko/bucketeer
RUN go mod download
COPY . /go/src/gitlab.com/idoko/bucketeer
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o build/bucketeer gitlab.com/idoko/bucketeer

FROM alpine
RUN apk add --no-cache ca-certificates && update-ca-certificates
COPY --from=builder /go/src/gitlab.com/idoko/bucketeer/build/bucketeer /usr/bin/bucketeer
EXPOSE 8080 8080
ENTRYPOINT ["/usr/bin/bucketeer"]
```

接下来，打开`docker-compose.yml`文件，声明`server`和`database`服务:

```
version: "3.7"
services:
  database:
    image: postgres
    restart: always
    env_file:
      - .env
    ports:
      - "5432:5432"
    volumes:
      - data:/var/lib/postgresql/data
  server:
    build:
      context: .
      dockerfile: Dockerfile
    env_file: .env
    depends_on:
      - database
    networks:
      - default
    ports:
    - "8080:8080"
volumes:
  data:
```

此外，用您的特定于应用程序的凭据填充`.env`文件，如下所示:

```
POSTGRES_USER=bucketeer
POSTGRES_PASSWORD=bucketeer_pass
POSTGRES_DB=bucketeer_db
```

## 设置数据库

我们将使用 [golang-migrate](https://github.com/golang-migrate/migrate) 来管理我们的数据库迁移。这样，我们可以跟踪数据库和代码的变化，并确保这些变化是可重复的。按照[安装指南](https://github.com/golang-migrate/migrate/tree/master/cmd/migrate)安装迁移二进制文件，并通过运行以下命令生成数据库迁移:

```
migrate create -ext sql -dir db/migrations -seq create_items_table
```

该命令在`db/migrations`文件夹中创建两个 SQL 文件。当我们运行迁移时，会执行`XXXXXX_create_items_table.up.sql`文件。打开它并添加 SQL 代码以创建一个新表:

```
CREATE TABLE IF NOT EXISTS items(
id SERIAL PRIMARY KEY,
name VARCHAR(100) NOT NULL,
description TEXT,
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

相反，当我们回滚迁移时，会执行`XXXXXX_create_items_table.down.sql`文件。在这种情况下，我们只想在回滚期间删除该表，因此向其中添加以下代码块:

```
DROP TABLE IF EXISTS items;
```

现在，我们可以通过将数据库连接和包含迁移文件的文件夹作为命令行参数传入，使用 *migrate* 应用我们的迁移。下面的命令通过使用在`.env`文件中声明的相同凭证创建一个 bash 环境变量来实现这一点:

```
$ export POSTGRESQL_URL="postgres://bucketeer:[email protected]:5432/bucketeer_db?sslmode=disable"
$ migrate -database ${POSTGRESQL_URL} -path db/migrations up
```

## 使用结构作为模型

我们需要模型来简化我们如何从 Go 代码中与数据库进行交互。对于我们的例子，这个模型在`models`文件夹的`item.go`文件中。使用 chi，我们还可以将它们作为 JSON 对象呈现给 API 消费者。我们通过让我们的模型实现`[chi.Renderer](https://pkg.go.dev/github.com/go-chi/render?tab=doc#Render)`接口来做到这一点，也就是说，通过为它实现一个`Render`方法。打开文件(`models/item.go`)，并向其中添加以下代码:

```
package models
import (
    "fmt"
    "net/http"
)
type Item struct {
    ID int `json:"id"`
    Name string `json:"name"`
    Description string `json:"description"`
    CreatedAt string `json:"created_at"`
}
type ItemList struct {
    Items []Item `json:"items"`
}
func (i *Item) Bind(r *http.Request) error {
    if i.Name == "" {
        return fmt.Errorf("name is a required field")
    }
    return nil
}
func (*ItemList) Render(w http.ResponseWriter, r *http.Request) error {
    return nil
}
func (*Item) Render(w http.ResponseWriter, r *http.Request) error {
    return nil
}
```

## 与 PostgreSQL 交互

现在我们的数据库已经就位，我们可以从我们的 Go 代码连接到它。编辑`db`目录下的`db.go`文件，添加代码管理连接:

```
package db
import (
    "database/sql"
    "fmt"
    "log"
    _ "github.com/lib/pq"
)
const (
    HOST = "database"
    PORT = 5432
)
// ErrNoMatch is returned when we request a row that doesn't exist
var ErrNoMatch = fmt.Errorf("no matching record")
type Database struct {
    Conn *sql.DB
}
func Initialize(username, password, database string) (Database, error) {
    db := Database{}
    dsn := fmt.Sprintf("host=%s port=%d user=%s password=%s dbname=%s sslmode=disable",
        HOST, PORT, username, password, database)
    conn, err := sql.Open("postgres", dsn)
    if err != nil {
        return db, err
    }
    db.Conn = conn
    err = db.Conn.Ping()
    if err != nil {
        return db, err
    }
    log.Println("Database connection established")
    return db, nil
}
```

接下来，编辑`item.go`文件，使其负责与条目表进行交互。这种交互包括获取所有列表项、创建项、使用项的 ID 获取项以及更新和删除它们:

```
package db
import (
    "database/sql"
    "gitlab.com/idoko/bucketeer/models"
)
func (db Database) GetAllItems() (*models.ItemList, error) {
    list := &models.ItemList{}
    rows, err := db.Conn.Query("SELECT * FROM items ORDER BY ID DESC")
    if err != nil {
        return list, err
    }
    for rows.Next() {
        var item models.Item
        err := rows.Scan(&item.ID, &item.Name, &item.Description, &item.CreatedAt)
        if err != nil {
            return list, err
        }
        list.Items = append(list.Items, item)
    }
    return list, nil
}
func (db Database) AddItem(item *models.Item) error {
    var id int
    var createdAt string
    query := `INSERT INTO items (name, description) VALUES ($1, $2) RETURNING id, created_at`
    err := db.Conn.QueryRow(query, item.Name, item.Description).Scan(&id, &createdAt)
    if err != nil {
        return err
    }
    item.ID = id
    item.CreatedAt = createdAt
    return nil
}
func (db Database) GetItemById(itemId int) (models.Item, error) {
    item := models.Item{}
    query := `SELECT * FROM items WHERE id = $1;`
    row := db.Conn.QueryRow(query, itemId)
    switch err := row.Scan(&item.ID, &item.Name, &item.Description, &item.CreatedAt); err {
    case sql.ErrNoRows:
        return item, ErrNoMatch
    default:
        return item, err
    }
}
func (db Database) DeleteItem(itemId int) error {
    query := `DELETE FROM items WHERE id = $1;`
    _, err := db.Conn.Exec(query, itemId)
    switch err {
    case sql.ErrNoRows:
        return ErrNoMatch
    default:
        return err
    }
}
func (db Database) UpdateItem(itemId int, itemData models.Item) (models.Item, error) {
    item := models.Item{}
    query := `UPDATE items SET name=$1, description=$2 WHERE id=$3 RETURNING id, name, description, created_at;`
    err := db.Conn.QueryRow(query, itemData.Name, itemData.Description, itemId).Scan(&item.ID, &item.Name, &item.Description, &item.CreatedAt)
    if err != nil {
        if err == sql.ErrNoRows {
            return item, ErrNoMatch
        }
        return item, err
    }
    return item, nil
}
```

上面的代码设置了五个方法来匹配我们的每个 API 端点。请注意，每个方法都能够返回它们在数据库操作过程中遇到的任何错误。这样，我们可以一路冒泡错误，直到它们被正确处理的地方。

`GetAllItems`检索数据库中的所有项目，并将它们作为一个保存项目切片的`ItemList`返回。

`AddItem`负责在数据库中创建一个新条目。它还通过利用 PostgreSQL 的`[RETURNING](https://www.postgresql.org/docs/9.5/dml-returning.html)`关键字来更新它接收到的`Item`实例的`ID`。

`GetItemById`、`UpdateItem`和`DeleteItem`负责从我们的数据库中获取、更新和删除项目。在这种情况下，我们执行额外的检查，如果数据库中不存在该项目，则返回不同的错误。

## 连接我们的路线处理器

我们现在准备利用 chi 强大的路由功能。我们将首先初始化`handler/handler.go`中的路由处理程序，并实现代码来处理 HTTP 错误，例如 404 Not Found 和 405 Method Not Allowed。打开`handler.go`文件并粘贴以下代码:

```
package handler
import (
    "net/http"
    "github.com/go-chi/chi"
    "github.com/go-chi/render"
    "gitlab.com/idoko/bucketeer/db"
)
var dbInstance db.Database
func NewHandler(db db.Database) http.Handler {
    router := chi.NewRouter()
    dbInstance = db
    router.MethodNotAllowed(methodNotAllowedHandler)
    router.NotFound(notFoundHandler)
    router.Route("/items", items)
    return router
}
func methodNotAllowedHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-type", "application/json")
    w.WriteHeader(405)
    render.Render(w, r, ErrMethodNotAllowed)
}
func notFoundHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-type", "application/json")
    w.WriteHeader(400)
    render.Render(w, r, ErrNotFound)
}
```

接下来，编辑`handler/errors.go`文件以声明我们上面引用的错误响应(即`ErrNotFound`和`ErrMethodNotAllowed`)以及我们稍后将在不同的路由处理程序中使用的错误响应:

```
package handler
import (
    "github.com/go-chi/render"
    "net/http"
)
type ErrorResponse struct {
    Err error `json:"-"`
    StatusCode int `json:"-"`
    StatusText string `json:"status_text"`
    Message string `json:"message"`
}
var (
    ErrMethodNotAllowed = &ErrorResponse{StatusCode: 405, Message: "Method not allowed"}
    ErrNotFound         = &ErrorResponse{StatusCode: 404, Message: "Resource not found"}
    ErrBadRequest       = &ErrorResponse{StatusCode: 400, Message: "Bad request"}
)
func (e *ErrorResponse) Render(w http.ResponseWriter, r *http.Request) error {
    render.Status(r, e.StatusCode)
    return nil
}
func ErrorRenderer(err error) *ErrorResponse {
    return &ErrorResponse{
        Err: err,
        StatusCode: 400,
        StatusText: "Bad request",
        Message: err.Error(),
    }
}
func ServerErrorRenderer(err error) *ErrorResponse {
    return &ErrorResponse{
        Err: err,
        StatusCode: 500,
        StatusText: "Internal server error",
        Message: err.Error(),
    }
}
```

接下来，我们将更新`handler/items.go`，它负责我们在主处理程序文件中指定的所有带有前缀`/items`的 API 端点。在编辑器中打开它，并添加以下内容:

```
package handler
import (
    "context"
    "fmt"
    "net/http"
    "strconv"
    "github.com/go-chi/chi"
    "github.com/go-chi/render"
    "gitlab.com/idoko/bucketeer/db"
    "gitlab.com/idoko/bucketeer/models"
)
var itemIDKey = "itemID"
func items(router chi.Router) {
    router.Get("/", getAllItems)
    router.Post("/", createItem)
    router.Route("/{itemId}", func(router chi.Router) {
        router.Use(ItemContext)
        router.Get("/", getItem)
        router.Put("/", updateItem)
        router.Delete("/", deleteItem)
    })
}
func ItemContext(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        itemId := chi.URLParam(r, "itemId")
        if itemId == "" {
            render.Render(w, r, ErrorRenderer(fmt.Errorf("item ID is required")))
            return
        }
        id, err := strconv.Atoi(itemId)
        if err != nil {
            render.Render(w, r, ErrorRenderer(fmt.Errorf("invalid item ID")))
        }
        ctx := context.WithValue(r.Context(), itemIDKey, id)
        next.ServeHTTP(w, r.WithContext(ctx))
    })
}
```

在顶层，我们指定了包名并导入了所需的包。我们还声明了一个`itemIDKey`变量。稍后，我们将使用这个变量通过 Go 的上下文在中间件和请求处理程序之间传递 itemID URL 参数。

我们还创建了一个 chi [中间件](https://github.com/go-chi/chi/blob/master/middleware)方法(`ItemContext`)来帮助我们从请求 URL 中提取`itemID` URL 参数，并在我们的代码中使用它。中间件检查`itemID`是否存在以及是否有效，并继续将其添加到请求上下文中(使用之前创建的`itemIDKey`变量)。

## 添加新项目

为了创建一个新的桶列表，我们将使用 chi 的`[render.Bind](https://github.com/go-chi/render)`将请求体解码成一个`models.Item`的实例，然后将其发送到数据库中保存。将以下代码添加到`handler/items.go`的末尾，即`ItemContext`函数之后:

```
func createItem(w http.ResponseWriter, r *http.Request) {
    item := &models.Item{}
    if err := render.Bind(r, item); err != nil {
        render.Render(w, r, ErrBadRequest)
        return
    }
    if err := dbInstance.AddItem(item); err != nil {
        render.Render(w, r, ErrorRenderer(err))
        return
    }
    if err := render.Render(w, r, item); err != nil {
        render.Render(w, r, ServerErrorRenderer(err))
        return
    }
}
```

## 获取所有项目

要获取数据库中的所有现有项目，请将下面的代码附加到`handler/items.go`:

```
func getAllItems(w http.ResponseWriter, r *http.Request) {
    items, err := dbInstance.GetAllItems()
    if err != nil {
        render.Render(w, r, ServerErrorRenderer(err))
        return
    }
    if err := render.Render(w, r, items); err != nil {
        render.Render(w, r, ErrorRenderer(err))
    }
}
```

## 查看特定项目

查看一个特定的条目意味着我们必须检索由我们之前实现的`ItemContext`中间件添加到请求上下文中的条目 ID，并从数据库中检索匹配的行:

```
func getItem(w http.ResponseWriter, r *http.Request) {
    itemID := r.Context().Value(itemIDKey).(int)
    item, err := dbInstance.GetItemById(itemID)
    if err != nil {
        if err == db.ErrNoMatch {
            render.Render(w, r, ErrNotFound)
        } else {
            render.Render(w, r, ErrorRenderer(err))
        }
        return
    }
    if err := render.Render(w, r, &item); err != nil {
        render.Render(w, r, ServerErrorRenderer(err))
        return
    }
}
```

类似地，我们将实现从数据库中删除和更新一个现有的条目:

```
func deleteItem(w http.ResponseWriter, r *http.Request) {
    itemId := r.Context().Value(itemIDKey).(int)
    err := dbInstance.DeleteItem(itemId)
    if err != nil {
        if err == db.ErrNoMatch {
            render.Render(w, r, ErrNotFound)
        } else {
            render.Render(w, r, ServerErrorRenderer(err))
        }
        return
    }
}
func updateItem(w http.ResponseWriter, r *http.Request) {
    itemId := r.Context().Value(itemIDKey).(int)
    itemData := models.Item{}
    if err := render.Bind(r, &itemData); err != nil {
        render.Render(w, r, ErrBadRequest)
        return
    }
    item, err := dbInstance.UpdateItem(itemId, itemData)
    if err != nil {
        if err == db.ErrNoMatch {
            render.Render(w, r, ErrNotFound)
        } else {
            render.Render(w, r, ServerErrorRenderer(err))
        }
        return
    }
    if err := render.Render(w, r, &item); err != nil {
        render.Render(w, r, ServerErrorRenderer(err))
        return
    }
}
```

## 把他们聚集在一起

设置好 API 的各个组件后，我们将在`main.go`文件中把它们绑在一起。打开文件并添加以下代码:

```
package main
import (
    "context"
    "fmt"
    "gitlab.com/idoko/bucketeer/db"
    "gitlab.com/idoko/bucketeer/handler"
    "log"
    "net"
    "net/http"
    "os"
    "os/signal"
    "syscall"
    "time"
)
func main() {
    addr := ":8080"
    listener, err := net.Listen("tcp", addr)
    if err != nil {
        log.Fatalf("Error occurred: %s", err.Error())
    }
    dbUser, dbPassword, dbName :=
        os.Getenv("POSTGRES_USER"),
        os.Getenv("POSTGRES_PASSWORD"),
        os.Getenv("POSTGRES_DB")
    database, err := db.Initialize(dbUser, dbPassword, dbName)
    if err != nil {
        log.Fatalf("Could not set up database: %v", err)
    }
    defer database.Conn.Close()

    httpHandler := handler.NewHandler(database)
    server := &http.Server{
        Handler: httpHandler,
    }
    go func() {
        server.Serve(listener)
    }()
    defer Stop(server)
    log.Printf("Started server on %s", addr)
    ch := make(chan os.Signal, 1)
    signal.Notify(ch, syscall.SIGINT, syscall.SIGTERM)
    log.Println(fmt.Sprint(<-ch))
    log.Println("Stopping API server.")
}
func Stop(server *http.Server) {
    ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
    defer cancel()
    if err := server.Shutdown(ctx); err != nil {
        log.Printf("Could not shut down server correctly: %v\n", err)
        os.Exit(1)
    }
}
```

在上面的例子中，我们要求`db`包使用从环境变量中获得的凭证创建一个新的数据库连接。然后，该连接被传递给处理程序供其使用。使用`defer database.Conn.Close()`，我们确保数据库连接在应用程序运行时保持活动。

API 服务器在一个单独的 [goroutine](https://tour.golang.org/concurrency/1) 上启动并保持运行，直到它接收到一个`[SIGINT or SIGTERM](https://www.gnu.org/software/libc/manual/html_node/Termination-Signals.html)`信号，之后它调用`Stop`函数来清理并关闭服务器。

## 用 cURL 测试我们的 API

我们现在准备使用 docker-compose 测试我们的应用程序。在终端中运行下面的命令来构建和启动服务。

```
$ docker-compose up --build
```

在一个单独的终端中，您可以使用 [Postman](https://postman.com) 或者通过运行下面的 *curl* 命令来测试各个端点。

向遗愿清单添加新项目:

```
$ curl -X POST http://localhost:8080/items -H "Content-type: application/json" -d '{ "name": "swim across the River Benue", "description": "ho ho ho"}'
```

上面的命令应该给出与下面类似的响应:

```
{"id":8,"name":"swim across the River Benue","description":"ho ho ho","created_at":"2020-07-26T22:31:04.49683Z"}
```

通过运行以下命令获取当前列表中的所有项目:

```
curl http://localhost:8080/items
```

进而给出以下响应:

```
{
  "items": [
    {
      "id": 1,
      "name": "swim across the River Benue",
      "description": "ho ho ho",
      "created_at": "2020-07-26T22:31:04.49683Z"
    }
  ]
}
```

使用 ID 提取单个项目:

```
$ curl http://localhost:8080/items/8
```

上面的命令应该会返回如下所示的响应:

```
{"id":8,"name":"swim across the River Benue","description":"ho ho ho","created_at":"2020-07-26T22:31:04.49683Z"}
```

## 结论

在本文中，我们使用 chi、Docker 和 PostgreSQL 构建了一个简单的 REST API，并探讨了 chi 的一些优点，比如中间件。完整的源代码可在 [Gitlab](https://gitlab.com/idoko/bucketeer) 上获得。欢迎在 [Gitlab](https://gitlab.com/idoko/bucketeer/-/issues) 上提出问题，或者在 [Twitter](https://twitter.com/firechael) 上联系我，提出问题或反馈。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)