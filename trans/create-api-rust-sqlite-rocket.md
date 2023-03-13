# 用 SQLite 和 Rocket 在 Rust 中创建一个 API

> 原文：<https://blog.logrocket.com/create-api-rust-sqlite-rocket/>

无论你使用 SQLite 是因为它是世界上最流行的数据库引擎，还是使用 Rust 是因为它是 T2 最受欢迎的语言，这两种技术都不会出错。SQLite 和 Rust 将为您提供速度和效率。

本教程将演示如何使用 SQLite 作为 Rust APIs 的数据库上下文系统。我们将创建一个 SQLite 数据库，设置并安装 Rocket 框架来编写服务器逻辑，然后使用 Diesel 框架来处理到 SQLite 数据库的连接。

我们开始吧！

*向前跳转:*

## 先决条件

要跟随本教程，您需要以下内容:

*   熟悉 Rust 编程语言和货物构建系统和软件包管理器
*   对数据库连接有基本了解
*   能够在您喜欢的环境中启动开发项目

## 什么是 SQLite？

SQLite 是一个关系数据库管理系统，在设置复杂性和资源使用方面有一种轻量级的感觉。它是无服务器的，不需要任何配置。因为 SQLite 实际上是驻留在单个跨平台文件中的数据库，所以它不需要管理。

SQLite 事务是 ACID 兼容的(原子的、一致的、隔离的和持久的)，提供了对多线程操作的安全访问。尽管听起来很轻量级和“精简”,但它拥有 SQL2 标准中大多数重要的查询语言特性。

## 用 Cargo 初始化项目

Cargo 使得启动任何类型的防锈项目变得更加容易。对于我们的 SQLite API 项目，我们将使用以下命令进行初始化:

```
cargo new rust-rocket-sqlite-api --bin

```

这就创建了`Cargo.toml`文件。

## 为 SQLite 设置

SQLite 是本教程中默认的数据库存储，所以下一步，为您的机器安装 SQLite 驱动程序。

SQLite 预装在新的 Mac 电脑上，但是(如果需要)Mac 命令如下:

```
brew install sqlite 

```

以下是适用于 Linux 用户的安装命令:

```
sudo apt install sqlite3

```

现在，确认安装，就像这样:

```
sqlite3 --version

```

接下来，运行一个实例:

```
sqlite3

```

您应该会看到下面的输出:

![Terminal Output Confirming SQLite Installation](img/1ca4a5ac580c572c370d5519a7cfc352.png)

如果你不熟悉如何使用 SQLite3 数据库，你可以在这里了解更多。

## 用 Rust 连接到 SQLite 数据库

现在，让我们连接到 SQLite 数据库。

首先，在终端中使用以下命令运行 SQLite 实例:

```
sqlite3

```

接下来，我们需要一个数据文件。使用下面的命令创建一个名为`data`的文件夹，并导航到其中:

```
sqlite filename.db

```

我们将创建一个名为`data.db`的文件。接下来，我们将创建一个包含用户名和密码的表:

```
create table users(username text PRIMARY KEY, password text);

```

如果我们运行`.tables`命令，我们会看到`users`现在是数据库引擎中的一个表:

![Creating SQLite Table Usernames Passwords](img/efaf3b647b76101ceefa5af4149c7b77.png)

接下来，让我们用值填充用户表:

```
 insert into users(username, password) values ("Jon Doe", "j0hnd03");
sqlite> select * from users;

```

在`Cargo.toml`文件中，将`SQLite`机箱添加到依赖关系部分:

```
[dependencies]
sqlite = "0.30.1"

```

现在，构建项目以确保没有错误:

```
cargo build

```

该命令将下载并构建依赖关系箱，并编译项目。

## 熟悉 Rocket 框架

Rocket 是一个像 Actix web 框架一样的 Rust 后端 Web 开发的流行框架。它比 Actix Web 更新，提供了一些优势。首先，[用 Rocket](https://blog.logrocket.com/rust-web-apps-using-rocket-framework/) 编写应用程序更容易，因为大部分样板代码都在幕后。

另一个优势是 Rocket 的中间件组件和处理程序更容易实现，因为它们不需要对过程有任何深刻的理解。

要安装 Rocket，只需在`Cargo.toml`文件中的依赖项下指定框架及其版本。因为我们使用 Rocket 作为 web 框架，所以我们不需要在依赖文件中指定 SQLite 及其版本:

```
[dependencies]
rocket = "0.5.0-rc.2"

```

## 熟悉 Diesel 框架

[Diesel](https://diesel.rs) 是一个 [ORM 框架，可以用来创建和处理到 SQLite 数据库的连接](https://blog.logrocket.com/interacting-databases-rust-diesel-vs-sqlx/)。柴油依赖于`libpq`、`libmysqlclient`和`libsqlite3`。要安装 Diesel，我们只需要在依赖文件中指定它。我们可以给生锈的火箭添加柴油，就像这样:

```
[dependencies]
rocket = "0.5.0-rc.2"
diesel = { version = "2.0.2", features = ["sqlite"] }
dotenvy = "0.15"

```

这里，我们还添加了`dotenvy`库来管理环境变量。这个库查找以`.env`结尾的文件，以找到要加载的所有环境变量。我们现在将创建一个名为`.env`的文件，并输入以下变量:

```
DATABASE_URL=data.db

```

我们将在本地使用`diesel cli`来管理项目。要安装它，只需运行以下命令:

```
cargo install diesel_cli

```

虽然这是一个通用命令，但是我们可以去掉任何不需要的库并指定我们想要的库。在这种情况下，我们需要 SQLite，因此我们将运行以下代码:

```
cargo install diesel_cli --no-default-features --features sqlite 

```

该命令阻止安装默认设置，SQLite 除外。接下来，我们将使用 Diesel 来设置迁移:

```
diesel setup

```

## 构建待办事项 API

为了演示我们已经安装的包的用法，我们将构建一个待办事项 API。

让我们首先创建一个新项目:

```
cargo new rusty-rocket --bin

```

### 使用 Diesel 创建迁移

接下来，我们将使用 Diesel 创建一个目录来处理迁移。在您运行以下命令后，Diesel 会自动执行此操作:

```
diesel migration generate create_tasks

```

结果目录被命名为`create_tasks`，它包含两个文件:

*   `up.sql`:申请迁移的语句
*   `down.sql`:恢复迁移的语句

您可以在这些文件中编写迁移。例如，我们的`CREATE`语句将在`up.sql`文件中:

```
-- Your SQL goes here
CREATE TABLE IF NOT EXISTS tasks
(
    id INTEGER PRIMARY KEY NOT NULL,
    name TEXT NOT NULL,
    description TEXT NOT NULL
);

```

并且相应的语句将在`down.sql`文件中:

```
DROP TABLE tasks;

```

将这些语句添加到各自的文件后，使用以下命令应用迁移:

```
diesel migration run

```

要确认这是否有效，请检查父目录中名为`db.sqlite3`的新文件。你可以把它添加到你的`.gitignore`文件中，如果你使用的话，还有`target/`和`.env`文件。

同样，尝试运行`diesel migration redo`来确认`down.sql`文件正确地撤销了`up.sql`文件。

### 用 Rust 编写 API 逻辑

到目前为止，我们已经使用 Diesel 创建了数据库。现在，我们需要编写一个模块来连接数据库，还需要编写待办事项 API 逻辑。

但是首先，让我们对我们的`Cargo.toml`文件做如下修改:

```
[package]
name = "rusty-rocket"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
rocket = { version = "0.5.0-rc.2", features = ["json"] }
sqlx = { version = "0.6", features = ["runtime-tokio-native-tls", "sqlite"] }
tokio = { version = "1", features = ["full"] }

```

我们以前所有的依赖项以及更多的依赖项都包含在这些父包中。

接下来，我们将通过创建一个以模块名称命名的文件夹以及一个同名的 Rust 源文件来添加一个模块。我们将创建一个`database.rs`文件和一个名为`database`的目录。在这个目录中，我们将添加两个文件，一个用于请求，一个用于响应:

1.  `requests`的`requests.rs`文件:

```
use rocket::serde::Deserialize;

#[derive(Deserialize, Debug)]
#[serde(crate = "rocket::serde")]
pub struct TaskRequest {
    pub name: String,
    pub description: String,
}

```

`responses`的`2) responses.rs`文件:

```
use rocket::serde::Serialize;
use sqlx::FromRow;

#[derive(Serialize, FromRow, Debug)]
#[serde(crate = "rocket::serde")]
pub struct Task {
    pub id: i64,
    pub name: String,
    pub description: String,
}

```

现在。我们将通过在`database.rs`文件中调用它来公开这个模块。我们还将连接到数据库，并编写用于创建和轮询任务的`create`和`get`函数。

逻辑是这样的:

```
use sqlx::{Pool, Sqlite};

pub mod requests;
pub mod responses;

use responses::Task;

pub type DBResult<T, E = rocket::response::Debug<sqlx::Error>> = std::result::Result<T, E>;

pub async fn create_task(
    pool: &Pool<Sqlite>,
    name: &String,
    description: &String,
) -> DBResult<i64> {
    let mut connection = pool
        .acquire()
        .await?;
    let id = sqlx::query_as!(
            Task,
            r#"
        INSERT INTO tasks (name, description) VALUES (?, ?);
        "#,
            name,
            description
    )
        .execute(&mut connection)
        .await?
        .last_insert_rowid();
        Ok(id)
}

pub async fn get_task(pool: &Pool<Sqlite> id: i64) -> DBResult<Task> {
    let mut connection = pool.acquire()
        .await?;
    let task = sqlx::query_as!(
        Task,
        r#"
        SELECT id, name, description from tasks
        WHERE id = ?;
        "#,
            id
    )
        .fetch_one(&mut connection)
        .await?;
        Ok(task)
}

pub async fn get_tasks(pool: &Pool<Sqlite>) -> DBResult<Vec<Task>> {
    let mut connection = pool.acquire()
        .await
        .unwrap();
    let tasks = sqlx::query_as::<_, Task>(
        r#"
        select id, name, description from tasks;
        "#
    )
        .fetch_all(&mut connection)
        .await?;
        Ok(tasks)
}

```

### 给生锈的火箭布线

是时候在 Rocket 框架的帮助下在`main.rs`文件中编写路由逻辑了:

```
#[macro_use]
extern crate rocket;
mod database;

use database::requests::TaskRequest;
use database::responses::Task;
use database::{create_task, get_task, get_tasks, DBResult};
use rocket::serde::json::Json;
use rocket::State;
use sqlx::{Pool, Sqlite, SqlitePool};

#[post("/tasks", format = "json", data = "<task>")]
async fn create(task: Json<TaskRequest>, pool: &State<Pool<Sqlite>>) -> DBResult<Json<Task>> {
    let id = create_task(pool, &task.name, &task.description).await?;
    let task = get_task(pool, id).await?;
    Ok(Json(task))
}

#[get("/tasks")]
async fn index(pool: &State<Pool<Sqlite>>) -> DBResult<Json<Vec<Task>>> {
    let tasks = get_tasks(pool).await?;
    Ok(Json(tasks))
}

#[get("/tasks/<id>")]
async fn detail(id: i64, pool: &State<Pool<Sqlite>>) -> DBResult<Json<Task>> {
    let task = get_task(pool, id).await?;
    Ok(Json(task))
}

#[rocket::main]
async fn main() -> Result<(), rocket::Error> {
    let pool = SqlitePool::connect("sqlite://db.sqlite3")
        .await
        .expect("Couldn't connect to sqlite database");

    sqlx::migrate!()
        .run(&pool)
        .await
        .expect("Couldn't migrate the database tables");

    let _rocket = rocket::build()
        .mount("/", routes![index, create, detail])
        .manage(pool)
        .launch()
        .await?;
    Ok(())
}

```

现在，我们可以继续运行项目，在父目录中使用以下命令:

```
cargo run

```

下面是构建后 Rocket 在控制台中运行的输出:

![Rocket Running Background After API Build](img/eb468ae7b550776ff7efb16c3aff1a03.png)

## 结论

在本教程中，我们演示了如何使用 Rocket 和 Diesel 构建一个简单的待办事项 API。我们看到 Rust 如何使用 Rocket 处理与 SQLite 数据库的连接。

这个示例项目可以按照您喜欢的任何方式构建。例如，使用这里描述的概念，您可以为模式和模型创建模块，而不是将它们写在`database.rs`文件中。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。