# 全栈 Rust:带示例的完整教程

> 原文：<https://blog.logrocket.com/full-stack-rust-a-complete-tutorial-with-examples/>

在这篇博客的前面，我们介绍了如何使用 warp 用 Rust 创建一个 [CRUD web 服务，以及如何使用 Yew](https://blog.logrocket.com/create-an-async-crud-web-service-in-rust-with-warp/) 用 Rust[构建一个前端 web 应用。](https://blog.logrocket.com/rust-webassembly-frontend-web-app-yew/)

在本教程中，我们将把所有这些放在一起，构建一个简单的全栈 web 应用程序，其特点是有一个数据库支持的 REST 后端和一个基于 Wasm 的单页面应用程序在前端，它调用这个后端。

最重要的是，我们将创建一个共享的 Rust 模块，它将被前端和后端使用，以演示如何在这样的设置中共享代码。

我们将构建一个非常简单的宠物主人应用程序，使用户能够添加主人和他们的宠物。我们的应用程序将为主人和他们的宠物列表提供详细的视图，使他们能够根据需要删除和添加宠物。

以下是我们将要介绍的内容:

你不需要阅读前面提到的文章来理解，但是因为这篇文章包括了这两个概念，所以我们不会对基础知识进行同样深度的讨论。如果你想深入研究，请随意浏览。

事不宜迟，我们开始吧！

## 设置全栈 Rust 应用程序

接下来，你所需要的就是一个[相当新的生锈装置](https://www.rust-lang.org/)。 [Docker](https://blog.logrocket.com/packaging-a-rust-web-service-using-docker/) ，或者其他方式的[运行 Postgres 数据库](https://blog.logrocket.com/create-a-backend-api-with-rust-and-postgres/)，也会很有用。

在这种情况下，由于我们将在 Rust 中编写后端和前端，并且我们将在它们之间共享一些代码，我们将使用 [Cargo](https://blog.logrocket.com/demystifying-cargo-in-rust/) 创建一个多成员工作空间项目。

首先，创建一个新的 Rust 项目:

```
cargo new rust-fullstack-example
cd rust-fullstack-example

```

然后，删除`src`文件夹并编辑`Cargo.toml`文件，如下所示:

```
[workspace]

members = [
    "backend",
    "frontend",

    #Internal
    "common"
]

```

现在我们可以创建三个独立的 Rust 项目:

```
cargo new --lib common
cargo new backend
cargo new --lib frontend

```

导航到`common`目录并编辑`Cargo.toml`文件，添加以下依赖项:

```
[dependencies]
serde = {version = "=1.0.126", features = ["derive"] }

```

接下来，编辑`frontend`中的`Cargo.toml`文件，并添加这些依赖项:

```
[dependencies]
yew = "0.18"
wasm-bindgen = "0.2.67"
serde_json = "1"
serde = {version = "=1.0.126", features = ["derive"] }
anyhow = "1"
yew-router = "0.15.0"
common = { version = "0.1.0", path = "../common" }

```

我们使用[红豆杉](https://yew.rs/)来构建[基于 Wasm 的前端](https://blog.logrocket.com/webassembly-runtimes-compared/)。我们为路由和错误以及 [JSON 处理](https://blog.logrocket.com/json-and-rust-why-serde_json-is-the-top-choice/)添加了更多的实用程序库，以及对我们的`common`库的内部依赖，它将保存前端和后端之间共享的代码。

最后，编辑`backend`中的`Cargo.toml`文件，并添加这些依赖项:

```
[dependencies]
tokio = { version = "=1.6.1", features = ["macros", "rt-multi-thread"] }
warp = "=0.3.1"
mobc = "=0.7.2"
mobc-postgres = { version = "=0.7.0", features = ["with-chrono-0_4", "with-serde_json-1"] }
serde = {version = "=1.0.126", features = ["derive"] }
serde_json = "=1.0.64"
thiserror = "=1.0.24"
common = { version = "0.1.0", path = "../common" }

```

我们使用 [warp](https://github.com/seanmonstar/warp) web 框架来构建后端。由于我们使用 Postgres 数据库来存储我们的数据，我们还将为 Postgres 添加 [mobc](https://github.com/importcjj/mobc) 连接池。

除此之外，由于 Warp 是为 [Tokio](https://tokio.rs/) 优化的，我们需要将它添加为我们的异步运行时。我们将添加一些用于错误和 JSON 处理的实用程序库，以及对我们的`common`项目的内部依赖。

这就是设置。让我们开始为我们的`common`项目中的`frontend`和`backend`编写共享代码。

## 通用功能

我们将从充实`common`模块开始，在这里我们将添加`frontend`和`backend`之间的共享数据模型。在真实的应用程序中，可以共享更多的东西——包括验证、助手、实用程序等。—但是，在这种情况下，我们将坚持使用数据结构。

在`lib.rs`中，我们将为我们的`Owner`和`Pet`模型添加数据模型:

```
use serde::{Deserialize, Serialize};

#[derive(Deserialize, Clone, PartialEq, Debug)]
pub struct Owner {
    pub id: i32,
    pub name: String,
}

#[derive(Serialize, Deserialize, Clone, PartialEq, Debug)]
pub struct OwnerRequest {
    pub name: String,
}

#[derive(Serialize, Deserialize, Clone, PartialEq, Debug)]
pub struct OwnerResponse {
    pub id: i32,
    pub name: String,
}

impl OwnerResponse {
    pub fn of(owner: Owner) -> OwnerResponse {
        OwnerResponse {
            id: owner.id,
            name: owner.name,
        }
    }
}

#[derive(Deserialize, Clone, PartialEq, Debug)]
pub struct Pet {
    pub id: i32,
    pub name: String,
    pub owner_id: i32,
    pub animal_type: String,
    pub color: Option<String>,
}

#[derive(Serialize, Deserialize, Clone, PartialEq, Debug)]
pub struct PetRequest {
    pub name: String,
    pub animal_type: String,
    pub color: Option<String>,
}

#[derive(Serialize, Deserialize, Clone, PartialEq, Debug)]
pub struct PetResponse {
    pub id: i32,
    pub name: String,
    pub animal_type: String,
    pub color: Option<String>,
}

impl PetResponse {
    pub fn of(pet: Pet) -> PetResponse {
        PetResponse {
            id: pet.id,
            name: pet.name,
            animal_type: pet.animal_type,
            color: pet.color,
        }
    }
}

```

我们定义了数据库域结构`Owner`和`Pet`，以及请求和响应数据对象，我们将使用它们在前端和后端之间进行通信。

分享这段代码是件好事，因为如果我们不适应这种变化，在 API 的某个地方添加或删除一个字段会在前端给我们带来编译错误。这可能会节省我们在更新 API 时追踪错误的时间。

`Owner`非常简单，只有一个`name`和数据库 ID。`Pet`型有一个`name`，一个`animal_type`，还有一个可选的`color`。

我们还定义了一些助手，从数据库域对象为 API 创建数据对象。

这就是我们将在`common`项目中投入的全部内容。

让我们继续我们应用程序的后端部分。

## 构建 REST 后端

我们从数据模型的数据库定义开始:

```
CREATE TABLE IF NOT EXISTS owner
(
    id SERIAL PRIMARY KEY NOT NULL,
    name VARCHAR(255) NOT NULL
);

CREATE TABLE IF NOT EXISTS pet
(
    id SERIAL PRIMARY KEY NOT NULL,
    owner_id INT NOT NULL,
    name VARCHAR(255) NOT NULL,
    animal_type VARCHAR(255) NOT NULL,
    color VARCHAR(255),

    CONSTRAINT fk_pet_owner_id FOREIGN KEY (owner_id) REFERENCES pet(id)
);

```

这用它们各自的字段定义了我们的两个数据表。

我们将自下而上构建后端，从数据库层开始，一直到 web 服务器和路由定义。

首先，我们将创建一个`db`模块。在这里，我们将从`mod.rs`中的一些数据库和连接池初始化代码开始:

```
type Result<T> = std::result::Result<T, error::Error>;

const DB_POOL_MAX_OPEN: u64 = 32;
const DB_POOL_MAX_IDLE: u64 = 8;
const DB_POOL_TIMEOUT_SECONDS: u64 = 15;
const INIT_SQL: &str = "./db.sql";

pub async fn init_db(db_pool: &DBPool) -> Result<()> {
    let init_file = fs::read_to_string(INIT_SQL)?;
    let con = get_db_con(db_pool).await?;
    con.batch_execute(init_file.as_str())
        .await
        .map_err(DBInitError)?;
    Ok(())
}

pub async fn get_db_con(db_pool: &DBPool) -> Result<DBCon> {
    db_pool.get().await.map_err(DBPoolError)
}

pub fn create_pool() -> std::result::Result<DBPool, mobc::Error<Error>> {
    let config = Config::from_str("postgres://[email protected]:7878/postgres")?;

    let manager = PgConnectionManager::new(config, NoTls);
    Ok(Pool::builder()
        .max_open(DB_POOL_MAX_OPEN)
        .max_idle(DB_POOL_MAX_IDLE)
        .get_timeout(Some(Duration::from_secs(DB_POOL_TIMEOUT_SECONDS)))
        .build(manager))
}

```

在`init_db`中，我们读取前面提到的`db.sql`文件并执行它来初始化我们的表。

`create_pool`和`get_db_con`助手用于初始化数据库池，并从池中获取新的连接。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

说完这些设置细节，让我们看看`owner.rs`中的第一个域访问对象。

```
pub const TABLE: &str = "owner";
const SELECT_FIELDS: &str = "id, name";

pub async fn fetch(db_pool: &DBPool) -> Result<Vec<Owner>> {
    let con = get_db_con(db_pool).await?;
    let query = format!("SELECT {} FROM {}", SELECT_FIELDS, TABLE);
    let rows = con.query(query.as_str(), &[]).await.map_err(DBQueryError)?;

    Ok(rows.iter().map(|r| row_to_owner(&r)).collect())
}

pub async fn fetch_one(db_pool: &DBPool, id: i32) -> Result<Owner> {
    let con = get_db_con(db_pool).await?;
    let query = format!("SELECT {} FROM {} WHERE id = $1", SELECT_FIELDS, TABLE);

    let row = con
        .query_one(query.as_str(), &[&id])
        .await
        .map_err(DBQueryError)?;
    Ok(row_to_owner(&row))
}

pub async fn create(db_pool: &DBPool, body: OwnerRequest) -> Result<Owner> {
    let con = get_db_con(db_pool).await?;
    let query = format!("INSERT INTO {} (name) VALUES ($1) RETURNING *", TABLE);
    let row = con
        .query_one(query.as_str(), &[&body.name])
        .await
        .map_err(DBQueryError)?;
    Ok(row_to_owner(&row))
}

fn row_to_owner(row: &Row) -> Owner {
    let id: i32 = row.get(0);
    let name: String = row.get(1);
    Owner { id, name }
}

```

所有者有三种数据库操作:

1.  `fetch`获取所有所有者
2.  获取给定 ID 的所有者
3.  创建新的所有者

这些方法的实现相当简单。首先，我们从池中获得一个连接，然后我们定义要执行的 Postgres 查询，并使用给定的值执行它，传播任何错误。

最后，我们使用`row_to_owner`助手将返回的数据库行数据转换成实际的`Owner`结构。

`pet.rs`数据访问对象非常相似:

```
pub const TABLE: &str = "pet";
const SELECT_FIELDS: &str = "id, owner_id, name, animal_type, color";

pub async fn fetch(db_pool: &DBPool, owner_id: i32) -> Result<Vec<Pet>> {
    let con = get_db_con(db_pool).await?;
    let query = format!(
        "SELECT {} FROM {} WHERE owner_id = $1",
        SELECT_FIELDS, TABLE
    );
    let rows = con
        .query(query.as_str(), &[&owner_id])
        .await
        .map_err(DBQueryError)?;

    Ok(rows.iter().map(|r| row_to_pet(&r)).collect())
}

pub async fn create(db_pool: &DBPool, owner_id: i32, body: PetRequest) -> Result<Pet> {
    let con = get_db_con(db_pool).await?;
    let query = format!(
        "INSERT INTO {} (name, owner_id, animal_type, color) VALUES ($1, $2, $3, $4) RETURNING *",
        TABLE
    );
    let row = con
        .query_one(
            query.as_str(),
            &[&body.name, &owner_id, &body.animal_type, &body.color],
        )
        .await
        .map_err(DBQueryError)?;
    Ok(row_to_pet(&row))
}

pub async fn delete(db_pool: &DBPool, owner_id: i32, id: i32) -> Result<u64> {
    let con = get_db_con(db_pool).await?;
    let query = format!("DELETE FROM {} WHERE id = $1 AND owner_id = $2", TABLE);
    con.execute(query.as_str(), &[&id, &owner_id])
        .await
        .map_err(DBQueryError)
}

fn row_to_pet(row: &Row) -> Pet {
    let id: i32 = row.get(0);
    let owner_id: i32 = row.get(1);
    let name: String = row.get(2);
    let animal_type: String = row.get(3);
    let color: Option<String> = row.get(4);
    Pet {
        id,
        name,
        owner_id,
        animal_type,
        color,
    }
}

```

这里我们有以下三种方法:

1.  `fetch`获取属于给定`owner_id`的所有宠物
2.  `create`为给定的`owner_id`创建一个新宠物
3.  `delete`用给定的`id`和`owner_id`删除宠物

在实现方面，它遵循与上面的`owner.rs`完全相同的概念。

数据库层到此结束。让我们向上移动一步，在`src`中实现`handler.rs`。

```
pub async fn list_pets_handler(owner_id: i32, db_pool: DBPool) -> Result<impl Reply> {
    let pets = db::pet::fetch(&db_pool, owner_id)
        .await
        .map_err(reject::custom)?;
    Ok(json::<Vec<_>>(
        &pets.into_iter().map(PetResponse::of).collect(),
    ))
}

pub async fn create_pet_handler(
    owner_id: i32,
    body: PetRequest,
    db_pool: DBPool,
) -> Result<impl Reply> {
    Ok(json(&PetResponse::of(
        db::pet::create(&db_pool, owner_id, body)
            .await
            .map_err(reject::custom)?,
    )))
}

pub async fn delete_pet_handler(owner_id: i32, id: i32, db_pool: DBPool) -> Result<impl Reply> {
    db::pet::delete(&db_pool, owner_id, id)
        .await
        .map_err(reject::custom)?;
    Ok(StatusCode::OK)
}

pub async fn list_owners_handler(db_pool: DBPool) -> Result<impl Reply> {
    let owners = db::owner::fetch(&db_pool).await.map_err(reject::custom)?;
    Ok(json::<Vec<_>>(
        &owners.into_iter().map(OwnerResponse::of).collect(),
    ))
}

pub async fn fetch_owner_handler(id: i32, db_pool: DBPool) -> Result<impl Reply> {
    let owner = db::owner::fetch_one(&db_pool, id)
        .await
        .map_err(reject::custom)?;
    Ok(json(&OwnerResponse::of(owner)))
}

pub async fn create_owner_handler(body: OwnerRequest, db_pool: DBPool) -> Result<impl Reply> {
    Ok(json(&OwnerResponse::of(
        db::owner::create(&db_pool, body)
            .await
            .map_err(reject::custom)?,
    )))
}

```

API-surface 由六个操作组成:

1.  列出所有者
2.  获取给定 ID 的所有者
3.  创建所有者
4.  创建宠物
5.  删除宠物
6.  列出给定主人的宠物

在每种情况下，我们只需在数据库层调用相应的操作，并将返回的`Owner`或`Pet`分别转换为`OwnerResponse`或`PetResponse`，将任何错误直接返回给调用者。

最后，再进一步，我们实现了指向`main.rs`中这些处理程序的实际 web 服务器。

```
mod db;
mod error;
mod handler;

type Result<T> = std::result::Result<T, Rejection>;
type DBCon = Connection<PgConnectionManager<NoTls>>;
type DBPool = Pool<PgConnectionManager<NoTls>>;

#[tokio::main]
async fn main() {
    let db_pool = db::create_pool().expect("database pool can be created");

    db::init_db(&db_pool)
        .await
        .expect("database can be initialized");

    let pet = warp::path!("owner" / i32 / "pet");
    let pet_param = warp::path!("owner" / i32 / "pet" / i32);
    let owner = warp::path("owner");

    let pet_routes = pet
        .and(warp::get())
        .and(with_db(db_pool.clone()))
        .and_then(handler::list_pets_handler)
        .or(pet
            .and(warp::post())
            .and(warp::body::json())
            .and(with_db(db_pool.clone()))
            .and_then(handler::create_pet_handler))
        .or(pet_param
            .and(warp::delete())
            .and(with_db(db_pool.clone()))
            .and_then(handler::delete_pet_handler));

    let owner_routes = owner
        .and(warp::get())
        .and(warp::path::param())
        .and(with_db(db_pool.clone()))
        .and_then(handler::fetch_owner_handler)
        .or(owner
            .and(warp::get())
            .and(with_db(db_pool.clone()))
            .and_then(handler::list_owners_handler))
        .or(owner
            .and(warp::post())
            .and(warp::body::json())
            .and(with_db(db_pool.clone()))
            .and_then(handler::create_owner_handler));

    let routes = pet_routes
        .or(owner_routes)
        .recover(error::handle_rejection)
        .with(
            warp::cors()
                .allow_credentials(true)
                .allow_methods(&[
                    Method::OPTIONS,
                    Method::GET,
                    Method::POST,
                    Method::DELETE,
                    Method::PUT,
                ])
                .allow_headers(vec![header::CONTENT_TYPE, header::ACCEPT])
                .expose_headers(vec![header::LINK])
                .max_age(300)
                .allow_any_origin(),
        );

    warp::serve(routes).run(([127, 0, 0, 1], 8000)).await;
}

fn with_db(db_pool: DBPool) -> impl Filter<Extract = (DBPool,), Error = Infallible> + Clone {
    warp::any().map(move || db_pool.clone())
}

```

有相当多的东西需要打开，所以让我们浏览一下。

最初，我们定义模块和一些类型以节省打字时间。然后，在`main`函数(或`tokio::main`，我们应用程序的异步入口点)中，我们首先初始化数据库池和数据库。

在底部，有一个`with_db`过滤器，这是 warp 中向处理程序传递数据的首选方式——在本例中是连接池。

然后我们为`pet`定义几个路由基，其形式为`/owner/$ownerId/pet`；`pet_param`，在末尾加了一个`/$petId`；还有`owner`，简单的包含`/owner`。

有了这些基础，我们可以定义通向不同处理程序的路线:

*   `GET /owner`列出所有所有者
*   `GET /owner/$ownerId`返回给定 ID 的所有者
*   `POST /owner`创建所有者
*   列出给定主人的所有宠物
*   为给定的主人创造一只宠物
*   删除具有给定 ID 和主人 ID 的宠物

然后，我们用一个 [CORS 配置](https://blog.logrocket.com/the-ultimate-guide-to-enabling-cross-origin-resource-sharing-cors/)将所有东西连接在一起，并在端口`8000`上运行服务器。

`backend`到此结束。您可以通过简单地运行`cargo run`来运行它，如果您有一个 Postgres 数据库运行在端口`7878`(例如，使用 Docker)，您将有 REST API 运行在 [http://localhost:8000](http://localhost:8000) 。

您可以使用 cURL 通过运行如下命令来测试它:

```
curl -X POST http://localhost:8000/owner -d '{"name": "mario"}' -H 'content-type: application/json'

curl -v -X POST http://localhost:8000/owner/1/pet -d '{"name": "minka", "animal_type": "cat", "color": "black-brown-white"}' -H 'content-type: application/json'

```

## 前端实现

现在我们有了一个功能齐全的后端，我们需要一种方法来与之交互。

在`frontend`的例子中，我们将从`lib.rs`的顶部开始，向下遍历组件，因为一步一步地遍历组件树更自然。

我们将使用`yew_router`进行路由。否则，我们将使用与[官方文件](https://yew.rs/getting-started/build-a-sample-app)建议的相同的设置，使用`[trunk](https://github.com/thedodd/trunk)`来构建和服务 web 应用。

在我们的应用程序中，有两个模块，`pet`和`owner`。然而，在我们开始编写任何 Rust 代码之前，我们需要在我们的`frontend`项目根目录下创建我们的`index.html`文件，包括我们将使用的样式:

```
<html>
  <head>
    <title>Rust Fullstack Example</title>
    <style>
        body {
            font-size: 14px;
            font-family: sans-serif;
        }
        a {
            text-decoration: none;
            color: #339;
        }
        a:hover {
            text-decoration: none;
            color: #33f;
        }
        .app {
            background-color: #efefef;
            margin: 100px 25% 25% 25%;
            width: 50%;
            padding: 10px;
        }
        .app .nav {
            text-align: center;
            font-size: 16px;
            font-weight: bold;
        }
        .app .refresh {
            text-align: center;
            margin: 10px 0 10px 0;
        }
        .list-item {
            margin: 2px;
            padding: 5px;
            background-color: #cfc;
        }
        .pet {
            margin-top: 10px;
        }
        .completed {
            text-decoration: line-through;
            background-color: #dedede;
        }
        .detail {
            font-size: 16px;
        }
        .detail h1 {
            font-size: 24px;
        }
        .detail .id {
            color: #999;
        }
        .detail .completed {
            color: #3f3;
        }
        .detail .not-completed {
            color: #f33;
        }
    </style>
  </head>
</html>

```

这个 HTML 文件将被用作一个起点，`trunk`将添加相应的代码片段，使我们的应用程序在构建应用程序时能够在`dist`文件夹中使用它。

### 从根源开始

让我们从顶部的`lib.rs`开始。

我们首先定义一些模块和一个包含根组件的结构，以及一些路由。

```
mod owner;
mod pet;

pub type Anchor = RouterAnchor<AppRoute>;

struct FullStackApp {}

pub enum Msg {}

#[derive(Switch, Clone, Debug)]
pub enum AppRoute {
    #[to = "/app/create-owner"]
    CreateOwner,
    #[to = "/app/create-pet/{id}"]
    CreatePet(i32),
    #[to = "/app/{id}"]
    Detail(i32),
    #[to = "/"]
    Home,
}

```

我们的应用程序有`Home`(例如，列出主人)，查看主人详细信息页面和创建主人和宠物的路线。

然后，我们为我们的`FullStackApp`实现`Component`特征，这样我们就可以使用它作为入口点。

```
impl Component for FullStackApp {
    type Message = Msg;
    type Properties = ();
    fn create(_: Self::Properties, _link: ComponentLink<Self>) -> Self {
        Self {}
    }

    fn update(&mut self, _msg: Self::Message) -> ShouldRender {
        true
    }

    fn change(&mut self, _props: Self::Properties) -> ShouldRender {
        true
    }

    fn view(&self) -> Html {
        html! {
            <div class=classes!("app")>
                <div class=classes!("nav")>
                    <Anchor route=AppRoute::Home>{"Home"}</Anchor>
                </div>
                <div class=classes!("content")>
                    <Router<AppRoute, ()>
                        render = Router::render(move |switch: AppRoute| {
                            match switch {
                                AppRoute::CreateOwner => {
                                    html! {
                                        <div>
                                            <owner::create::CreateForm />
                                        </div>}
                                }
                                AppRoute::CreatePet(owner_id) => {
                                    html! {
                                        <div>
                                            <pet::create::CreateForm owner_id=owner_id/>
                                        </div>}
                                }
                                AppRoute::Detail(owner_id) => {
                                    html! {
                                        <div>
                                            <owner::detail::Detail owner_id=owner_id/>
                                        </div>}
                                }
                                AppRoute::Home => {
                                    html! {
                                        <div>
                                            <owner::list::List />
                                            <br />
                                            <Anchor route=AppRoute::CreateOwner>
                                                { "Create New Owner" }
                                            </Anchor>
                                        </div>
                                    }
                                }
                            }
                        })
                    />
                </div>
            </div>
        }
    }
}

```

我们的根组件实际上做不了多少事情；它只包含一个简单的菜单，带有一个`Home`链接，总是可见的，然后包括路由器，它为我们的每条路由配置应该显示哪个组件以及什么只是额外的标记。

例如，对于默认的`Home`路线`AppRoute::Home`，我们显示了一个所有者列表和一个到`Create New Owner`表单的链接。

最后，我们需要下面的代码片段来让 Wasm-magic 发挥作用，因此我们从`trunk`中获得了一个实际的 web 应用程序:

```
#[wasm_bindgen(start)]
pub fn run_app() {
    App::<FullStackApp>::new().mount_to_body();
}

```

### 所有者列表

让我们从`Home`上显示的所有者列表开始，因为这是最简单的组成部分。

在`owner`模块中，我们创建了一个`mod.rs`文件、一个`create.rs`、一个`detail.rs`和一个`list.rs`文件。

在`mod.rs`中，我们简单地导出这些模块:

```
pub mod create;
pub mod detail;
pub mod list;

```

然后，我们开始实现`list.rs`。

目标是从后端获取所有者列表，并显示链接到其详细页面的每个所有者。

我们首先定义了`List`结构，这是我们组件的基础:

```
pub struct List {
    fetch_task: Option<FetchTask>,
    owners: Option<Vec<OwnerResponse>>,
    link: ComponentLink<Self>,
}

```

`ComponentLink`是 Yew 在组件内部发送消息的方法，例如，触发 web 请求等副作用。

因为我们使用的是 Yew 的`FetchService`，我们还需要保存我们将用来从后端获取所有者的`fetch_task`。

最初,`owners`的列表是`None`,一旦对后端的请求(希望如此)返回所有者列表，列表就会被填充。

然后，我们定义我们的`Msg`枚举，定义由组件处理的消息。

```
pub enum Msg {
    MakeReq,
    Resp(Result<Vec<OwnerResponse>, anyhow::Error>),
}

```

我们只需创建一个发出请求的动作和一个从后端接收结果的动作。

这样，我们可以如下实现`Component`:

```
impl Component for List {
    type Properties = ();
    type Message = Msg;

    fn create(_props: Self::Properties, link: ComponentLink<Self>) -> Self {
        link.send_message(Msg::MakeReq);
        Self {
            fetch_task: None,
            link,
            owners: None,
        }
    }

    fn view(&self) -> Html {
        html! {
            <div>
                { self.render_list() }
            </div>
        }
    }

    fn update(&mut self, msg: Self::Message) -> ShouldRender {
        match msg {
            Msg::MakeReq => {
                self.owners = None;
                let req = Request::get("http://localhost:8000/owner")
                    .body(Nothing)
                    .expect("can make req to backend");

                let cb = self.link.callback(
                    |response: Response<Json<Result<Vec<OwnerResponse>, anyhow::Error>>>| {
                        let Json(data) = response.into_body();
                        Msg::Resp(data)
                    },
                );

                let task = FetchService::fetch(req, cb).expect("can create task");
                self.fetch_task = Some(task);
                ()
            }
            Msg::Resp(resp) => {
                if let Ok(data) = resp {
                    self.owners = Some(data);
                }
            }
        }
        true
    }

    fn change(&mut self, _props: Self::Properties) -> ShouldRender {
        true
    }
}

```

当组件被创建时，我们使用组件链接来触发`MakeReq`，向后端发送对所有者的请求。然后我们初始化组件。

在`update`中，我们处理请求和响应消息，使用`FetchService`向`[http://localhost:8000/owner](http://localhost:8000/owner)`发送请求，在那里我们的后端为我们提供所有者列表。

然后我们解析回调中的响应并调用`Msg::Resp(data)`，如果没有错误发生，它将把数据设置到我们的组件中。

在`render`函数中，我们简单地调用`render_list`，我们在`List`上实现它，如下所示:

```
impl List {
    fn render_list(&self) -> Html {
        if let Some(t) = &self.owners {
            html! {
                <div class=classes!("list")>
                    { t.iter().map(|name| self.view_owner(name)).collect::<Html>() }
                </div>
            }
        } else {
            html! {
                <div class=classes!("loading")>{"loading..."}</div>
            }
        }
    }

    fn view_owner(&self, owner: &OwnerResponse) -> Html {
        html! {
            <div class=classes!("list-item")>
                <Anchor route=AppRoute::Detail(owner.id as i32)>
                    { &owner.name }
                </Anchor>
            </div>
        }
    }
}

```

基本上，如果我们设置了`self.owners`,我们遍历列表并为每个列表呈现`view_owner`。这会创建一个带有所有者 ID 的到`AppRoute::Detail`的链接，这是一个到详细页面的链接。

如果我们没有数据，我们会显示一条`loading…`消息。

这是给列表所有者的。让我们继续阅读`detail.rs`中的详细页面。

### 为所有者创建详细信息页面

所有者详细信息页面有点复杂。在这里，我们需要发出两个请求:一个是获取具有给定所有者 ID 的所有者(这样我们也可以刷新页面并直接使用路线)，以及所有者的宠物列表。此外，我们必须实现删除宠物的功能。

总的想法是一样的:

```
#[derive(Properties, Clone, PartialEq)]
pub struct Props {
    pub owner_id: i32,
}

pub struct Detail {
    props: Props,
    link: ComponentLink<Self>,
    pets: Option<Vec<PetResponse>>,
    owner: Option<OwnerResponse>,
    fetch_pets_task: Option<FetchTask>,
    fetch_owner_task: Option<FetchTask>,
    delete_pet_task: Option<FetchTask>,
}

pub enum Msg {
    MakePetsReq(i32),
    MakeOwnerReq(i32),
    MakeDeletePetReq(i32, i32),
    RespPets(Result<Vec<PetResponse>, anyhow::Error>),
    RespOwner(Result<OwnerResponse, anyhow::Error>),
    RespDeletePet(Response<Json<Result<(), anyhow::Error>>>, i32),
}

```

我们为调用它的组件定义了属性——在本例中，是来自路由路径的`owner id`。

然后，我们定义`Detail`结构，它保存我们组件的数据，包括我们要获取的`pets`和`owner`，以及组件链接、道具和用于获取宠物、获取主人和删除宠物的`FetchTasks`。

让我们来看看组件的实现:

```
impl Component for Detail {
    type Properties = Props;
    type Message = Msg;

    fn create(props: Self::Properties, link: ComponentLink<Self>) -> Self {
        link.send_message(Msg::MakePetsReq(props.owner_id));
        link.send_message(Msg::MakeOwnerReq(props.owner_id));
        Self {
            props,
            link,
            owner: None,
            pets: None,
            fetch_pets_task: None,
            fetch_owner_task: None,
            delete_pet_task: None,
        }
    }

    fn view(&self) -> Html {
        html! {
            <div>
                { self.render_detail(&self.owner, &self.pets)}
            </div>
        }
    }

    fn update(&mut self, msg: Self::Message) -> ShouldRender {
        match msg {
            Msg::MakePetsReq(id) => {
                let req = Request::get(&format!("http://localhost:8000/owner/{}/pet", id))
                    .body(Nothing)
                    .expect("can make req to backend");

                let cb = self.link.callback(
                    |response: Response<Json<Result<Vec<PetResponse>, anyhow::Error>>>| {
                        let Json(data) = response.into_body();
                        Msg::RespPets(data)
                    },
                );

                let task = FetchService::fetch(req, cb).expect("can create task");
                self.fetch_pets_task = Some(task);
                ()
            }
            Msg::MakeOwnerReq(id) => {
                let req = Request::get(&format!("http://localhost:8000/owner/{}", id))
                    .body(Nothing)
                    .expect("can make req to backend");

                let cb = self.link.callback(
                    |response: Response<Json<Result<OwnerResponse, anyhow::Error>>>| {
                        let Json(data) = response.into_body();
                        Msg::RespOwner(data)
                    },
                );

                let task = FetchService::fetch(req, cb).expect("can create task");
                self.fetch_owner_task = Some(task);
                ()
            }
            Msg::MakeDeletePetReq(owner_id, pet_id) => {
                let req = Request::delete(&format!(
                    "http://localhost:8000/owner/{}/pet/{}",
                    owner_id, pet_id
                ))
                .body(Nothing)
                .expect("can make req to backend");

                let cb = self.link.callback(
                    move |response: Response<Json<Result<(), anyhow::Error>>>| {
                        Msg::RespDeletePet(response, pet_id)
                    },
                );

                let task = FetchService::fetch(req, cb).expect("can create task");
                self.delete_pet_task = Some(task);
                ()
            }
            Msg::RespPets(resp) => {
                if let Ok(data) = resp {
                    self.pets = Some(data);
                }
            }
            Msg::RespOwner(resp) => {
                if let Ok(data) = resp {
                    self.owner = Some(data);
                }
            }
            Msg::RespDeletePet(resp, id) => {
                if resp.status().is_success() {
                    self.pets = self
                        .pets
                        .as_ref()
                        .map(|pets| pets.into_iter().filter(|p| p.id != id).cloned().collect());
                }
            }
        }
        true
    }

    fn change(&mut self, props: Self::Properties) -> ShouldRender {
        self.props = props;
        true
    }
}

```

基本原理是一样的，我们的`view`调用一个`render_detail`函数，我们稍后会看到，在`create`中，我们也初始化我们的组件，并通过发送相应的消息和给定的`owner_id`来触发宠物和主人的获取。

在`update`中，我们需要实现获取宠物和主人的请求和响应处理程序。这些几乎与`List`组件中的完全相同，只是 URL 和返回类型不同。

在`MakeDeletePetReq`的处理程序中，我们使用给定的`owner_id`和`pet_id`发送`DELETE`请求。如果成功了，我们就触发`Msg::RespDeletePet`信息。

在那里，如果请求成功，我们只需从宠物列表中删除具有给定 ID 的宠物。这很好，因为这意味着我们不需要重新提取整个宠物列表。

让我们看看所有者详细信息的呈现代码:

```
impl Detail {
    fn render_detail(
        &self,
        owner: &Option<OwnerResponse>,
        pets: &Option<Vec<PetResponse>>,
    ) -> Html {
        match owner {
            Some(o) => {
                html! {
                    <div class=classes!("detail")>
                        <h1>{&o.name}{" ("}<span class=classes!("id")>{o.id}</span>{")"}</h1>
                        {
                            self.view_pet_list(pets)
                        }

                    <br />
                    <Anchor route=AppRoute::CreatePet(o.id as i32)>
                        { "Create New Pet" }
                    </Anchor>
                    </div>
                }
            }
            None => {
                html! {
                    <div class=classes!("loading")>{"loading..."}</div>
                }
            }
        }
    }

    fn view_pet_list(&self, pets: &Option<Vec<PetResponse>>) -> Html {
        match pets {
            Some(p) => {
                html! {
                    p.iter().map(|pet| self.view_pet(pet)).collect::<Html>()
                }
            }
            None => {
                html! {
                    <div class=classes!("loading")>{"loading..."}</div>
                }
            }
        }
    }

    fn view_pet(&self, pet: &PetResponse) -> Html {
        let id = pet.id;
        let owner_id = self.props.owner_id;
        html! {
            <div class=classes!("list-item", "pet")>
                <div><b>{ &pet.name }</b> { " (" } <button onclick=self.link.callback(move |_| Msg::MakeDeletePetReq(owner_id, id))>{"Delete"}</button> {")"}</div>
                <div>{ &pet.animal_type }</div>
                <div>{ &pet.color.as_ref().unwrap_or(&String::new()) }</div>
            </div>
        }
    }
}

```

同样，如果我们有数据，我们就渲染它。否则，我们显示一个`loading…`指示器。一旦我们有了我们的`owner`，我们就在它的名字旁边显示它的 ID。

下面，我们用`view_pet`中宠物的实际渲染来渲染宠物列表。我们还创建了删除宠物的按钮，它有一个触发`MsgMakeDeletePetReq`消息的`onclick`处理程序。

在宠物列表下方，我们显示了一个到**创建宠物**路线的链接。

我们快完成了。现在我们只需要看看创建主人和宠物的组件。先说`create.rs`里的车主:

```
pub struct CreateForm {
    link: ComponentLink<Self>,
    fetch_task: Option<FetchTask>,
    state_name: String,
}

pub enum Msg {
    MakeReq,
    Resp(Result<OwnerResponse, anyhow::Error>),
    EditName(String),
}

```

同样，我们从`Component`结构和`Msg`枚举开始。

在这种情况下，我们需要数据基础设施来请求创建所有者，但是我们还需要一种创建和编辑表单的方法。

为此，我们在组件上创建了`state_name`字段，在`Msg`中创建了`EditName(String)`。

接下来让我们看看`Component`的实现:

```
impl Component for CreateForm {
    type Properties = ();
    type Message = Msg;

    fn create(_props: Self::Properties, link: ComponentLink<Self>) -> Self {
        Self {
            link,
            state_name: String::new(),
            fetch_task: None,
        }
    }

    fn view(&self) -> Html {
        html! {
            <div>
                { self.render_form() }
            </div>
        }
    }

    fn update(&mut self, msg: Self::Message) -> ShouldRender {
        match msg {
            Msg::MakeReq => {
                let body = OwnerRequest {
                    name: self.state_name.clone(),
                };
                let req = Request::post("http://localhost:8000/owner")
                    .header("Content-Type", "application/json")
                    .body(Json(&body))
                    .expect("can make req to backend");

                let cb = self.link.callback(
                    |response: Response<Json<Result<OwnerResponse, anyhow::Error>>>| {
                        let Json(data) = response.into_body();
                        Msg::Resp(data)
                    },
                );

                let task = FetchService::fetch(req, cb).expect("can create task");
                self.fetch_task = Some(task);
                ()
            }
            Msg::Resp(resp) => {
                ConsoleService::info(&format!("owner created: {:?}", resp));
                if let Ok(_) = resp {
                    RouteAgent::dispatcher().send(RouteRequest::ChangeRoute(Route {
                        route: "/".to_string(),
                        state: (),
                    }));
                }
            }
            Msg::EditName(input) => {
                self.state_name = input;
            }
        }
        true
    }

    fn change(&mut self, _props: Self::Properties) -> ShouldRender {
        true
    }
}

impl CreateForm {
    fn render_form(&self) -> Html {
        let edit_name = self
            .link
            .callback(move |e: InputData| Msg::EditName(e.value));

        html! {
            <div class=classes!("pet-form")>
                <div>
                    <input type="text" value={self.state_name.clone()} oninput={edit_name} />
                </div>
                <div>
                    <button onclick=self.link.callback(move |_| Msg::MakeReq)>{"Submit"}</button>
                </div>
            </div>
        }
    }
}

```

正如您所看到的，在`CreateForm`实现内部的`render_form`中，我们创建了一个简单的表单输入字段，它将`self.state_name`作为一个值。这意味着它直接连接到我们的国家。

我们使用`oninput`事件处理程序，每当有人将文本写入输入字段时，用输入字段的值调用`Msg::EditName`消息。

当您查看`Component`实现中的`update`函数时，`Msg::EditName`的处理程序只是将`self.state_name`设置为输入中的给定值。这确保了我们总是拥有组件内表单字段中的值。

一旦我们点击触发`Msg::MakeReq`的`Submit`按钮，这是很重要的。在这里，我们使用`self.state_name`作为`name`的值，创建一个 JSON 有效负载来创建一个所有者。

然后我们将这个有效负载发送到后端端点来创建一个所有者，如果一切顺利，使用`yew_router`的`RouteAgent`和 dispatcher 手动将路由改回`"` / `"`，这是我们的主路由。

### 宠物的详细信息页面

这就是用紫杉处理表单是多么容易！让我们通过创建一个内部有`mod.rs`和`create.rs`的`pet`模块来看看这个难题的最后部分。

在`mod.rs`中，我们再次只导出`create`:

```
pub mod create;

```

在`create.rs`中，我们实现了添加新宠物的组件，这将与我们刚刚实现的主人的`CreateForm`非常相似。

```
#[derive(Properties, Clone, PartialEq)]
pub struct Props {
    pub owner_id: i32,
}

pub struct CreateForm {
    props: Props,
    link: ComponentLink<Self>,
    fetch_task: Option<FetchTask>,
    state_pet_name: String,
    state_animal_type: String,
    state_color: Option<String>,
}

pub enum Msg {
    MakeReq(i32),
    Resp(Result<PetResponse, anyhow::Error>),
    EditName(String),
    EditAnimalType(String),
    EditColor(String),
}

```

`CreatePet`表单将我们想要为其创建宠物的主人的`owner_id`作为道具。

然后，我们定义`state_pet_name`、`state_animal_type`和`state_color`来保持我们三个表单字段的状态，就像我们为所有者所做的一样。

对于`Msg`，情况是一样的:我们需要每个表单字段的处理程序，以及发出`create pet`请求并处理其响应的处理程序。

我们来看看`Component`的实现和渲染逻辑:

```
impl Component for CreateForm {
    type Properties = Props;
    type Message = Msg;

    fn create(props: Self::Properties, link: ComponentLink<Self>) -> Self {
        Self {
            props,
            link,
            state_pet_name: String::new(),
            state_animal_type: String::from("cat"),
            state_color: Some(String::from("black")),
            fetch_task: None,
        }
    }

    fn view(&self) -> Html {
        html! {
            <div>
                { self.render_form(self.props.owner_id) }
            </div>
        }
    }

    fn update(&mut self, msg: Self::Message) -> ShouldRender {
        match msg {
            Msg::MakeReq(id) => {
                let body = PetRequest {
                    name: self.state_pet_name.clone(),
                    animal_type: self.state_animal_type.clone(),
                    color: self.state_color.clone(),
                };
                let req = Request::post(&format!("http://localhost:8000/owner/{}/pet", id))
                    .header("Content-Type", "application/json")
                    .body(Json(&body))
                    .expect("can make req to backend");

                let cb = self.link.callback(
                    |response: Response<Json<Result<PetResponse, anyhow::Error>>>| {
                        let Json(data) = response.into_body();
                        Msg::Resp(data)
                    },
                );

                let task = FetchService::fetch(req, cb).expect("can create task");
                self.fetch_task = Some(task);
                ()
            }
            Msg::Resp(resp) => {
                ConsoleService::info(&format!("pet created: {:?}", resp));
                if let Ok(_) = resp {
                    RouteAgent::dispatcher().send(RouteRequest::ChangeRoute(Route {
                        route: format!("/app/{}", self.props.owner_id),
                        state: (),
                    }));
                }
            }
            Msg::EditName(input) => {
                self.state_pet_name = input;
            }
            Msg::EditAnimalType(input) => {
                ConsoleService::info(&format!("input: {:?}", input));
                self.state_animal_type = input;
            }
            Msg::EditColor(input) => {
                self.state_color = Some(input);
            }
        }
        true
    }

    fn change(&mut self, props: Self::Properties) -> ShouldRender {
        self.props = props;
        true
    }
}

impl CreateForm {
    fn render_form(&self, owner_id: i32) -> Html {
        let edit_name = self
            .link
            .callback(move |e: InputData| Msg::EditName(e.value));
        let edit_animal_type = self.link.callback(move |e: ChangeData| match e {
            ChangeData::Select(elem) => Msg::EditAnimalType(elem.value()),
            _ => unreachable!("only used on select field"),
        });
        let edit_color = self
            .link
            .callback(move |e: InputData| Msg::EditColor(e.value));

        html! {
            <div class=classes!("pet-form")>
                <div>
                    <input type="text" value={self.state_pet_name.clone()} oninput={edit_name} />
                </div>
                <div>
                    <select onchange={edit_animal_type}>
                        <option value="cat" selected=true>{ "Cat" }</option>
                        <option value="dog">{ "Dog" }</option>
                    </select>
                </div>
                <div>
                    <input type="text" value={self.state_color.clone()} oninput={edit_color} />
                </div>
                <div>
                    <button onclick=self.link.callback(move |_| Msg::MakeReq(owner_id))>{"Submit"}</button>
                </div>
            </div>
        }
    }
}

```

先说`CreateForm`中的`render_form`函数。这里，我们再次为宠物的所有字段创建输入字段。然而，这一次有了一个变化:我们对动物类型使用了一个`select`字段，因为我们想将其限制为猫和狗。

这意味着，对于`edit_animal_type`的回调处理程序，我们得到一个`ChangeData`而不是一个`InputData`。在其中，我们需要匹配变化的类型。我们只想对`ChangeData::Select(elem)`做出反应，并获取元素的值，将其发送到我们的组件状态中进行设置。

对于其他两个字段，过程与我们的`Create Owner`组件相同。

就`Component`实现而言，这里也没有什么新东西。我们在后端实现了调用`create pet`端点的处理程序，以及将表单输入字段值传递给状态的处理程序，因此我们可以为这个端点创建有效负载。

随着最后一个组件的完成，我们的 Rust 全栈 web 应用程序实现就完成了！剩下的唯一事情就是测试它是否真的有效。

## 测试我们的 Rust 全栈应用

我们可以通过在端口 7878 上运行 Postgres 数据库来运行`frontend`和`backend`(导航到 [http://localhost:8080](http://localhost:8080) )。

在那里，迎接我们的是一个空的屏幕。我们可以点击**创建新的所有者**，它向我们展示了表单:

![Mario Submit](img/dc7939d96e5f856570c68b81f3534368.png)

提交将创建所有者，我们将在`Home`的列表中看到:

![Create New Owner](img/b527fc2fcf3058862e7364c628c366aa.png)

接下来，让我们单击新所有者，查看所有者详细信息页面:

![Empty Owner Detail](img/196a62d1d5de75cc77c749546e07641a.png)

现在我们可以开始使用`Create New Pet`添加一些宠物了:

![Create New Pet](img/bd8dacdce8d68fab5f4bed7725df9073.png)

完成后，我们会被重定向回**主人详细信息**页面，该页面会显示我们新添加的宠物列表:

![Owner Detail Pets](img/e1ee79fe487c2ba9822c53e4466480d5.png)

最后，我们可以点击宠物旁边的**删除**按钮来删除宠物:

![Delete Button](img/3221239ea8a939a7a3185e833a40b4b3.png)

奇幻；有用！所有这些都是用铁锈写的。

你可以在 [GitHub](https://github.com/zupzup/rust-fullstack-example) 上找到这个例子的完整代码。

## 结论

在本教程中，我们演示了如何完全在 Rust 中构建一个简单的全栈 web 应用程序。我们讨论了如何使用 Cargo 创建多模块工作区，以及如何在应用程序的前端和后端之间共享代码。

因此，Rust web 生态系统仍在不断成熟，所以令人印象深刻的是，你已经可以构建带有太多模糊的现代全栈 web 应用程序。

我很高兴看到 Wasm 之旅如何继续，我非常期待看到 [Rust async web 生态系统](https://blog.logrocket.com/a-practical-guide-to-async-in-rust/)进一步发展，提高稳定性、兼容性和库中的丰富性。

无论如何，Rust 的 web 开发前景看好！

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。