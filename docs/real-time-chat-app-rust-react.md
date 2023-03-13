# 用 Rust 和 React 构建一个实时聊天应用

> 原文：<https://blog.logrocket.com/real-time-chat-app-rust-react/>

如果你想开发一个既快速又可靠的实时聊天应用，可以考虑使用 Rust 和 React。Rust 以其速度和可靠性而闻名，而 [React 是构建用户界面最流行的前端框架之一](https://survey.stackoverflow.co/2022/#most-popular-technologies-webframe-prof)。

在本文中，我们将演示如何使用 Rust 和 React 构建一个实时聊天应用程序，该应用程序提供聊天、检查用户状态和指示用户何时输入的功能。我们将使用 WebSockets 来实现双向的客户机-服务器通信。

*向前跳转:*

## 实时聊天应用简介

实时聊天应用程序允许用户通过文本、语音或视频进行实时交流。与电子邮件或即时消息等其他类型的通信相比，这种类型的应用程序允许更即时的消息传递。

聊天应用程序必须实时工作有几个原因:

*   **改进的性能**:更即时的交流让对话更自然
*   **更快的响应速度**:实时功能改善了用户体验
*   **卓越的可靠性**:通过实时功能，减少了信息丢失或延迟的机会

## WebSockets 简介

WebSockets 支持实时聊天应用程序中客户端和服务器之间的双向通信。使用 Rust 构建 WebSocket 服务器将使服务器能够处理大量连接而不会变慢。这是由于 Rust 的速度和可靠性。

现在我们对 WebSockets 有了更好的理解，让我们开始构建我们的实时聊天应用程序吧！

## 入门指南

首先，让我们回顾一些先决条件:

*   Rust :确保你的电脑上安装了 Rust。如果没有，使用下面的命令安装:

    ```
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh // if you are in windows see more installation method here https://forge.rust-lang.org/infra/other-installation-methods.html
    ```

*   **React** :确保您的环境为 React 开发做好了准备；如果您还没有 React，请使用以下命令之一安装它:

    ```
    // on mac brew install node // on linux nvm install v14.10.0 // on windows you can download nodejs installer here https://nodejs.org/en/download/
    ```

接下来，运行以下命令，验证所有组件都已安装并正常工作:

```
rustc --version
cargo --version
node --version
npm --version

```

## 设计实时聊天应用程序架构

让我们为我们的实时聊天应用程序创建一些设计架构。我们将构建一个简单的服务器；我们的应用程序架构将涵盖以下特性:

*   **聊天**:两个用户之间通过直接信息交流
*   **输入指示器**:当用户开始输入聊天内容时通知接收者
*   **用户状态**:表示用户在线还是离线

![Real-Time Chat App Architecture](img/ca2f5d9d88a4a020534d64d29a426652.png)

Real-time chat application system architecture.

这种架构非常简单，易于遵循。它仅由几个组件组成:

*   WebSocket 服务器:这是我们应用程序中最重要的组件；它处理客户和房间之间的所有通信
*   房间管理器:这个组件负责管理我们应用程序中的所有房间。它将创建、更新和删除房间。该组件将位于 HTTP 服务器上
*   用户管理器:这个组件负责管理我们应用程序中的所有用户。它将创建、更新和删除用户。这个组件也将位于 HTTP 服务器上
*   消息管理器:这个组件负责管理我们应用程序中的所有消息。它将创建、更新和删除消息。这个组件将位于 WebSocket 服务器和 HTTP 服务器上。它将用于存储从 WebSockets 传入的消息，并在用户通过 Rest API 打开聊天室时检索数据库中已经存在的所有消息

## 在 Rust 中构建 WebSocket 服务器

我们可以用很多包来编写 Rust 中的 WebSocket 服务器。对于本教程，我们将使用[Actix Web](https://actix.rs)；这是一个成熟的软件包，易于使用。

首先，使用以下命令创建一个 Rust 项目:

```
cargo new rust-react-chat

```

接下来，将这个包添加到`Cargo.toml`文件中:

```
[package]
name = "rust-react-chat"
version = "0.1.0"
edition = "2021"

[dependencies]
actix = "0.13.0"
actix-files = "0.6.2"
actix-web = "4.2.1"
actix-web-actors = "4.1.0"
rand = "0.8.5"
serde = "1.0.147"
serde_json = "1.0.88"

```

现在，安装`diesel_cli`；我们将用它作为我们的 ORM:

```
cargo install diesel_cli --no-default-features --features sqlite

```

项目结构应该是这样的:

```
.
├── Cargo.lock
├── Cargo.toml
├── README.md
├── chat.db
├── .env
└── src
    ├── db.rs
    ├── main.rs
    ├── models.rs
    ├── routes.rs
    ├── schema.rs
    ├── server.rs
    └── session.rs
└── static
└── ui

```

现在，这里有一些关于文件夹的信息:

*   这个文件夹包含了我们所有的 Rust 代码
*   这个文件夹包含了我们所有的静态资产、HTML 文件、JavaScript 文件和图片
*   这个文件夹包含了我们的 React 代码；稍后我们将把它编译成`static`文件，并导出到`static`文件夹

接下来，让我们为 WebSocket 服务器编写入口点:

```
// src/main.rs
#[macro_use]
extern crate diesel;
use actix::*;
use actix_cors::Cors;
use actix_files::Files;
use actix_web::{web, http, App, HttpServer};
use diesel::{
    prelude::*,
    r2d2::{self, ConnectionManager},
};
mod db;
mod models;
mod routes;
mod schema;
mod server;
mod session;
#[actix_web::main]
async fn main() -> std::io::Result<()> {
    let server = server::ChatServer::new().start();
    let conn_spec = "chat.db";
    let manager = ConnectionManager::<SqliteConnection>::new(conn_spec);
    let pool = r2d2::Pool::builder().build(manager).expect("Failed to create pool.");
    let server_addr = "127.0.0.1";
    let server_port = 8080;
    let app = HttpServer::new(move || {
        let cors = Cors::default()
            .allowed_origin("http://localhost:3000")
            .allowed_origin("http://localhost:8080")
            .allowed_methods(vec!["GET", "POST"])
            .allowed_headers(vec![http::header::AUTHORIZATION, http::header::ACCEPT])
            .allowed_header(http::header::CONTENT_TYPE)
            .max_age(3600);
        App::new()
            .app_data(web::Data::new(server.clone()))
            .app_data(web::Data::new(pool.clone()))
            .wrap(cors)
            .service(web::resource("/").to(routes::index))
            .route("/ws", web::get().to(routes::chat_server))
            .service(routes::create_user)
            .service(routes::get_user_by_id)
            .service(routes::get_user_by_phone)
            .service(routes::get_conversation_by_id)
            .service(routes::get_rooms)
            .service(Files::new("/", "./static"))
    })
    .workers(2)
    .bind((server_addr, server_port))?
    .run();
    println!("Server running at http://{server_addr}:{server_port}/");
    app.await
}

```

以下是我们正在使用的软件包的一些信息:

*   `actix_cors`:将用于调试 UI；我们将接受来自`localhost:3000`或`localhost:8080`的帖子和请求
*   `actix_web`:针对 Actix Web 包中所有与 HTTP 相关的特性
*   `actix_files`:用于将静态文件嵌入到我们的一个路径中
*   `diesel`:将用于从我们的 SQLite 数据库中查询数据。如果您愿意，可以将它改为 Postgres 或 MySQL
*   `serde_json`:将用于解析我们将发送给 React 应用程序的 JSON 数据

### 创建路线

现在让我们为我们的服务器创建路由。由于我们将使用 REST HTTP 和 WebSocket 服务器，因此可以很容易地将所有内容放在一个文件中。

首先，添加我们需要的所有包:

```
// src/routes.rs
use std::time::Instant;
use actix::*;
use actix_files::NamedFile;
use actix_web::{get, post, web, Error, HttpRequest, HttpResponse, Responder};
use actix_web_actors::ws;
use diesel::{
    prelude::*,
    r2d2::{self, ConnectionManager},
};
use serde_json::json;
use uuid::Uuid;
use crate::db;
use crate::models;
use crate::server;
use crate::session;
type DbPool = r2d2::Pool<ConnectionManager<SqliteConnection>>;

```

然后，添加一个将主页嵌入到根 URL 的路由:

```
// src/routes.rs
pub async fn index() -> impl Responder {
    NamedFile::open_async("./static/index.html").await.unwrap()
}

```

这是我们 WebSocket 服务器的入口点。现在它在`/ws`路线上，但是你可以把它改成你喜欢的任何路线名称。因为我们已经在`main.rs`文件中注册了我们需要的所有依赖项，所以我们可以将依赖项传递给函数参数，就像这样:

```
// src/routes.rs
pub async fn chat_server(
    req: HttpRequest,
    stream: web::Payload,
    pool: web::Data<DbPool>,
    srv: web::Data<Addr<server::ChatServer>>,
) -> Result<HttpResponse, Error> {
    ws::start(
        session::WsChatSession {
            id: 0,
            hb: Instant::now(),
            room: "main".to_string(),
            name: None,
            addr: srv.get_ref().clone(),
            db_pool: pool,
        },
        &req,
        stream
    )
}

```

接下来，我们需要向我们的路由添加一个 REST API，以便获得必要的数据来使我们的聊天工作:

```
// src/routes.rs
#[post("/users/create")]
pub async fn create_user(
    pool: web::Data<DbPool>,
    form: web::Json<models::NewUser>,
) -> Result<HttpResponse, Error> {
    let user = web::block(move || {
        let mut conn = pool.get()?;
        db::insert_new_user(&mut conn, &form.username, &form.phone)
    })
    .await?
    .map_err(actix_web::error::ErrorUnprocessableEntity)?;
    Ok(HttpResponse::Ok().json(user))
}
#[get("/users/{user_id}")]
pub async fn get_user_by_id(
    pool: web::Data<DbPool>,
    id: web::Path<Uuid>,
) -> Result<HttpResponse, Error> {
    let user_id = id.to_owned();
    let user = web::block(move || {
        let mut conn = pool.get()?;
        db::find_user_by_uid(&mut conn, user_id)
    })
    .await?
    .map_err(actix_web::error::ErrorInternalServerError)?;
    if let Some(user) = user {
        Ok(HttpResponse::Ok().json(user))
    } else {
        let res = HttpResponse::NotFound().body(
            json!({
                "error": 404,
                "message": format!("No user found with phone: {id}")
            })
            .to_string(),
        );
        Ok(res)
    }
}
#[get("/conversations/{uid}")]
pub async fn get_conversation_by_id(
    pool: web::Data<DbPool>,
    uid: web::Path<Uuid>,
) -> Result<HttpResponse, Error> {
    let room_id = uid.to_owned();
    let conversations = web::block(move || {
        let mut conn = pool.get()?;
        db::get_conversation_by_room_uid(&mut conn, room_id)
    })
    .await?
    .map_err(actix_web::error::ErrorInternalServerError)?;
    if let Some(data) = conversations {
        Ok(HttpResponse::Ok().json(data))
    } else {
        let res = HttpResponse::NotFound().body(
            json!({
                "error": 404,
                "message": format!("No conversation with room_id: {room_id}")
            })
            .to_string(),
        );
        Ok(res)
    }
}
#[get("/users/phone/{user_phone}")]
pub async fn get_user_by_phone(
    pool: web::Data<DbPool>,
    phone: web::Path<String>,
) -> Result<HttpResponse, Error> {
    let user_phone = phone.to_string();
    let user = web::block(move || {
        let mut conn = pool.get()?;
        db::find_user_by_phone(&mut conn, user_phone)
    })
    .await?
    .map_err(actix_web::error::ErrorInternalServerError)?;
    if let Some(user) = user {
        Ok(HttpResponse::Ok().json(user))
    } else {
        let res = HttpResponse::NotFound().body(
            json!({
                "error": 404,
                "message": format!("No user found with phone: {}", phone.to_string())
            })
            .to_string(),
        );
        Ok(res)
    }
}
#[get("/rooms")]
pub async fn get_rooms(
    pool: web::Data<DbPool>,
) -> Result<HttpResponse, Error> {
    let rooms = web::block(move || {
        let mut conn = pool.get()?;
        db::get_all_rooms(&mut conn)
    })
    .await?
    .map_err(actix_web::error::ErrorInternalServerError)?;
    if !rooms.is_empty() {
        Ok(HttpResponse::Ok().json(rooms))
    } else {
        let res = HttpResponse::NotFound().body(
            json!({
                "error": 404,
                "message": "No rooms available at the moment.",
            })
            .to_string(),
        );
        Ok(res)
    }
}

```

现在，让我们来处理 WebSocket 连接。首先，让我们再次导入我们需要的所有包:

```
// src/server.rs
use std::collections::{HashMap, HashSet};
use serde_json::json;
use actix::prelude::*;
use rand::{self, rngs::ThreadRng, Rng};
use crate::session;
#[derive(Message)]
#[rtype(result = "()")]
pub struct Message(pub String);
#[derive(Message)]
#[rtype(usize)]
pub struct Connect {
    pub addr: Recipient<Message>,
}
#[derive(Message)]
#[rtype(result = "()")]
pub struct Disconnect {
    pub id: usize,
}
#[derive(Message)]
#[rtype(result = "()")]
pub struct ClientMessage {
    pub id: usize,
    pub msg: String,
    pub room: String,
}
pub struct ListRooms;
impl actix::Message for ListRooms {
    type Result = Vec<String>;
}
#[derive(Message)]
#[rtype(result = "()")]
pub struct Join {
    pub id: usize,
    pub name: String,
}

```

接下来，让我们实现 trait 来管理 WebSocket 连接。这段代码将处理来自用户的所有消息，并将它们发送回聊天室的参与者:

```
// src/server.rs
#[derive(Debug)]
pub struct ChatServer {
    sessions: HashMap<usize, Recipient<Message>>,
    rooms: HashMap<String, HashSet<usize>>,
    rng: ThreadRng,
}
impl ChatServer {
    pub fn new() -> ChatServer {
        let mut rooms = HashMap::new();
        rooms.insert("main".to_string(), HashSet::new());
        Self {
            sessions: HashMap::new(),
            rooms,
            rng: rand::thread_rng()
        }
    }
    fn send_message(&self, room: &str, message: &str, skip_id: usize) {
        if let Some(sessions) = self.rooms.get(room) {
            for id in sessions {
                if *id != skip_id {
                    if let Some(addr) = self.sessions.get(id) {
                        addr.do_send(Message(message.to_owned()));
                    }
                }
            }
        }
    }
}
impl Actor for ChatServer {
    type Context = Context<Self>;
}
impl Handler<Connect> for ChatServer {
    type Result = usize;
    fn handle(&mut self, msg: Connect, _: &mut Context<Self>) -> Self::Result {
        let id = self.rng.gen::<usize>();
        self.sessions.insert(id, msg.addr);
        self.rooms
            .entry("main".to_string())
            .or_insert_with(HashSet::new)
            .insert(id);
        self.send_message("main", &json!({
            "value": vec![format!("{}", id)],
            "chat_type": session::ChatType::CONNECT
        }).to_string(), 0);
        id
    }
}
impl Handler<Disconnect> for ChatServer {
    type Result = ();
    fn handle(&mut self, msg: Disconnect, _: &mut Self::Context) -> Self::Result {
        let mut rooms: Vec<String> = vec![];
        if self.sessions.remove(&msg.id).is_some() {
            for (name, sessions) in &mut self.rooms {
                if sessions.remove(&msg.id) {
                    rooms.push(name.to_owned());
                }
            }
        }
        for room in rooms {
            self.send_message("main", &json!({
                "room": room,
                "value": vec![format!("Someone disconnect!")],
                "chat_type": session::ChatType::DISCONNECT
            }).to_string(), 0);
        }
    }
}
impl Handler<ClientMessage> for ChatServer {
    type Result = ();
    fn handle(&mut self, msg: ClientMessage, _: &mut Self::Context) -> Self::Result {
        self.send_message(&msg.room, &msg.msg, msg.id);
    }
}
impl Handler<ListRooms> for ChatServer {
    type Result = MessageResult<ListRooms>;
    fn handle(&mut self, _: ListRooms, _: &mut Self::Context) -> Self::Result {
        let mut rooms = vec![];
        for key in self.rooms.keys() {
            rooms.push(key.to_owned());
        }
        MessageResult(rooms)
    }
}
impl Handler<Join> for ChatServer {
    type Result = ();
    fn handle(&mut self, msg: Join, _: &mut Self::Context) -> Self::Result {
        let Join {id, name} = msg;
        let mut rooms = vec![];
        for (n, sessions) in &mut self.rooms {
            if sessions.remove(&id) {
                rooms.push(n.to_owned());
            }
        }
        for room in rooms {
            self.send_message(&room, &json!({
                "room": room,
                "value": vec![format!("Someone disconnect!")],
                "chat_type": session::ChatType::DISCONNECT
            }).to_string(), 0);
        }
        self.rooms
            .entry(name.clone())
            .or_insert_with(HashSet::new)
            .insert(id);
    }
}

```

### 处理用户会话

现在，让我们处理用户会话。在这里，我们将接收一条消息，将其保存到数据库中，然后将其发送回聊天室中的参与者。

首先，导入所有包:

```
// src/session.rs
use std::time::{Duration, Instant};
use actix::prelude::*;
use actix_web::web;
use actix_web_actors::ws;
use serde::{Deserialize, Serialize};
use diesel::{
    prelude::*,
    r2d2::{self, ConnectionManager},
};
use crate::db;
use crate::models::NewConversation;
use crate::server;

```

您可以在此处更改连接到 WebSocket 的持续时间。因此`HEARTBEAT`是保持与客户端连接的持续时间。并且`CLIENT_TIMEOUT`是检查客户端是否仍然连接的持续时间:

```
// src/session.rs
const HEARBEET: Duration = Duration::from_secs(5);
const CLIENT_TIMEOUT: Duration = Duration::from_secs(10);
type DbPool = r2d2::Pool<ConnectionManager<SqliteConnection>>;

```

现在让我们创建一些结构来存储我们需要的所有数据:

```
// src/session.rs
#[derive(Debug)]
pub struct WsChatSession {
    pub id: usize,
    pub hb: Instant,
    pub room: String,
    pub name: Option<String>,
    pub addr: Addr<server::ChatServer>,
    pub db_pool: web::Data<DbPool>,
}
#[derive(PartialEq, Serialize, Deserialize)]
pub enum ChatType {
    TYPING,
    TEXT,
    CONNECT,
    DISCONNECT,
}
#[derive(Serialize, Deserialize)]
struct ChatMessage {
    pub chat_type: ChatType,
    pub value: Vec<String>,
    pub room_id: String,
    pub user_id: String,
    pub id: usize,
}

```

此结构将用于以下用途:

*   `WsChatSession`:定制 Actix Web actor 的实现
*   `ChatMessage`:定义发送给用户和从用户接收的对象

现在，让我们实现我们会话的`Actor`和流`Handler`:

```
// src/session.rs
impl Actor for WsChatSession {
    type Context = ws::WebsocketContext<Self>;
    fn started(&mut self, ctx: &mut Self::Context) {
        self.hb(ctx);
        let addr = ctx.address();
        self.addr
            .send(server::Connect {
                addr: addr.recipient(),
            })
            .into_actor(self)
            .then(|res, act, ctx| {
                match res {
                    Ok(res) => act.id = res,
                    _ => ctx.stop(),
                }
                fut::ready(())
            })
            .wait(ctx);
    }
    fn stopping(&mut self, _: &mut Self::Context) -> Running {
        self.addr.do_send(server::Disconnect { id: self.id });
        Running::Stop
    }
}
impl Handler<server::Message> for WsChatSession {
    type Result = ();
    fn handle(&mut self, msg: server::Message, ctx: &mut Self::Context) -> Self::Result {
        ctx.text(msg.0);
    }
}
impl StreamHandler<Result<ws::Message, ws::ProtocolError>> for WsChatSession {
    fn handle(&mut self, item: Result<ws::Message, ws::ProtocolError>, ctx: &mut Self::Context) {
        let msg = match item {
            Err(_) => {
                ctx.stop();
                return;
            }
            Ok(msg) => msg,
        };
        match msg {
            ws::Message::Ping(msg) => {
                self.hb = Instant::now();
                ctx.pong(&msg);
            }
            ws::Message::Pong(_) => {
                self.hb = Instant::now();
            }
            ws::Message::Text(text) => {
                let data_json = serde_json::from_str::<ChatMessage>(&text.to_string());
                if let Err(err) = data_json {
                    println!("{err}");
                    println!("Failed to parse message: {text}");
                    return;
                }
                let input = data_json.as_ref().unwrap();
                match &input.chat_type {
                    ChatType::TYPING => {
                        let chat_msg = ChatMessage {
                            chat_type: ChatType::TYPING,
                            value: input.value.to_vec(),
                            id: self.id,
                            room_id: input.room_id.to_string(),
                            user_id: input.user_id.to_string(),
                        };
                        let msg = serde_json::to_string(&chat_msg).unwrap();
                        self.addr.do_send(server::ClientMessage {
                            id: self.id,
                            msg,
                            room: self.room.clone(),
                        })
                    }
                    ChatType::TEXT => {
                        let input = data_json.as_ref().unwrap();
                        let chat_msg = ChatMessage {
                            chat_type: ChatType::TEXT,
                            value: input.value.to_vec(),
                            id: self.id,
                            room_id: input.room_id.to_string(),
                            user_id: input.user_id.to_string(),
                        };
                        let mut conn = self.db_pool.get().unwrap();
                        let new_conversation = NewConversation {
                            user_id: input.user_id.to_string(),
                            room_id: input.room_id.to_string(),
                            message: input.value.join(""),
                        };
                        let _ = db::insert_new_conversation(&mut conn, new_conversation);
                        let msg = serde_json::to_string(&chat_msg).unwrap();
                        self.addr.do_send(server::ClientMessage {
                            id: self.id,
                            msg,
                            room: self.room.clone(),
                        })
                    }
                    _ => {}
                }
            }
            ws::Message::Binary(_) => println!("Unsupported binary"),
            ws::Message::Close(reason) => {
                ctx.close(reason);
                ctx.stop();
            }
            ws::Message::Continuation(_) => {
                ctx.stop();
            }
            ws::Message::Nop => (),
        }
    }
}
impl WsChatSession {
    fn hb(&self, ctx: &mut ws::WebsocketContext<Self>) {
        ctx.run_interval(HEARBEET, |act, ctx| {
            if Instant::now().duration_since(act.hb) > CLIENT_TIMEOUT {
                act.addr.do_send(server::Disconnect { id: act.id });
                ctx.stop();
                return;
            }
            ctx.ping(b"");
        });
    }
}

```

## 准备数据库

接下来，让我们准备数据库。我们将使用 SQLite 来简化事情。下面是该模式的外观:

![SQLite Database Query Schema](img/06dcf2073030b0fa5e932c4b9b51ede8.png)

该表将用于以下目的:

*   `users`:存储用户数据。由于我们目前没有实现完整的身份验证系统，我们现在只保存用户名和电话号码
*   `rooms`:存储所有聊天室列表
*   列出所有信息在我们数据库中的存储位置

接下来，让我们为我们的模式生成数据库迁移:

```
// shell
diesel migration generate create_users
diesel migration generate create_rooms
diesel migration generate create_conversations

```

下面是迁移 SQL 的外观:

```
-- migrations/2022-11-21-101206_create_users/up.sql
CREATE TABLE users (
  id TEXT PRIMARY KEY NOT NULL,
  username VARCHAR NOT NULL,
  phone VARCHAR NOT NULL,
  created_at TEXT NOT NULL,
  unique(phone)
)

-- migrations/2022-11-21-101215_create_rooms/up.sql
CREATE TABLE rooms (
  id TEXT PRIMARY KEY NOT NULL,
  name VARCHAR NOT NULL,
  last_message TEXT NOT NULL,
  participant_ids TEXT NOT NULL,
  created_at TEXT NOT NULL
)

-- migrations/2022-11-21-101223_create_conversations/up.sql
CREATE TABLE conversations (
  id TEXT PRIMARY KEY NOT NULL,
  room_id TEXT NOT NULL,
  user_id TEXT NOT NULL,
  content VARCHAR NOT NULL,
  created_at TEXT NOT NULL
)

```

我们还需要添加一些虚拟数据，以便稍后为客户端提供一些初始渲染示例:

```
diesel migration generate dummy_data

```

下面是数据的样子:

```
-- migrations/2022-11-24-034153_generate_dummy_data/up.sql
INSERT INTO users(id, username, phone, created_at) 
VALUES
("4fbd288c-d3b2-4f78-adcf-def976902d50","Ahmad Rosid","123","2022-11-23T07:56:30.214162+00:00"),
("1e9a12c1-e98c-4a83-a55a-32cc548a169d","Ashley Young","345","2022-11-23T07:56:30.214162+00:00"),
("1bc833808-05ed-455a-9d26-64fe1d96d62d","Charles Edward","678","2022-12-23T07:56:30.214162+00:00");
INSERT INTO rooms(id, name, last_message, participant_ids, created_at)
VALUES
("f061383b-0393-4ce8-9a85-f31d03762263", "Charles Edward", "Hi, how are you?", "1e9a12c1-e98c-4a83-a55a-32cc548a169d,1bc833808-05ed-455a-9d26-64fe1d96d62d", "2022-12-23T07:56:30.214162+00:00"),
("008e9dc4-f01d-4429-ba31-986d7e63cce8", "Ahmad Rosid", "Hi... are free today?", "1e9a12c1-e98c-4a83-a55a-32cc548a169d,1bc833808-05ed-455a-9d26-64fe1d96d62d", "2022-12-23T07:56:30.214162+00:00");
INSERT INTO conversations(id, user_id, room_id, content, created_at)
VALUES
("9aeab1a7-e063-40d1-a120-1f7585fa47d6", "1bc833808-05ed-455a-9d26-64fe1d96d62d", "f061383b-0393-4ce8-9a85-f31d03762263", "Hello", "2022-12-23T07:56:30.214162+00:00"),
("f4e54e70-736b-4a79-a622-3659b0b555e8", "1e9a12c1-e98c-4a83-a55a-32cc548a169d", "f061383b-0393-4ce8-9a85-f31d03762263", "Hi, how are you?", "2022-12-23T07:56:30.214162+00:00"),
("d3ea6e39-ed58-4613-8922-b78f14a2676a", "1bc833808-05ed-455a-9d26-64fe1d96d62d", "008e9dc4-f01d-4429-ba31-986d7e63cce8", "Hi... are free today?", "2022-12-23T07:56:30.214162+00:00");

```

### 生成模式

现在让我们生成模式并运行迁移:

```
diesel database setup
diesel migration run   

```

CLI 自动生成的模式如下所示:

```
// src/schema.rs
// @generated automatically by Diesel CLI.
diesel::table! {
    conversations (id) {
        id -> Text,
        room_id -> Text,
        user_id -> Text,
        content -> Text,
        created_at -> Text,
    }
}
diesel::table! {
    rooms (id) {
        id -> Text,
        name -> Text,
        last_message -> Text,
        participant_ids -> Text,
        created_at -> Text,
    }
}
diesel::table! {
    users (id) {
        id -> Text,
        username -> Text,
        phone -> Text,
        created_at -> Text,
    }
}
diesel::allow_tables_to_appear_in_same_query!(
    conversations,
    rooms,
    users,
);

```

上面的代码是自动生成的，所以不要对这个文件做任何修改。

### 创建 struts

让我们创建一些结构来存储所有的表。需要记住的一点是，属性在结构中的顺序应该与在架构文件中的顺序相同。如果订单不符，你会得到错误的数据。

```
// src/model.rs
use serde::{Deserialize, Serialize};
use crate::schema::*;
#[derive(Debug, Clone, Serialize, Deserialize, Queryable, Insertable)]
pub struct User {
    pub id: String,
    pub username: String,
    pub phone: String,
    pub created_at: String
}
#[derive(Debug, Clone, PartialEq, Serialize, Deserialize, Queryable, Insertable)]
pub struct Conversation {
    pub id: String,
    pub room_id: String,
    pub user_id: String,
    pub content: String,
    pub created_at: String
}
#[derive(Debug, Clone, Serialize, Deserialize, Queryable, Insertable)]
pub struct Room {
    pub id: String,
    pub name: String,
    pub last_message: String,
    pub participant_ids: String,
    pub created_at: String,
}
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct NewUser {
    pub username: String,
    pub phone: String,
}
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct NewConversation {
    pub user_id: String,
    pub room_id: String,
    pub message: String,
}
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct RoomResponse {
    pub room: Room,
    pub users: Vec<User>,
}

```

### 设置查询

现在，让我们从数据库中获取数据。

首先，导入依赖项:

```
// src/db.rs
use chrono::{DateTime, Utc};
use diesel::prelude::*;
use std::{
    collections::{HashMap, HashSet},
    time::SystemTime,
};
use uuid::Uuid;
use crate::models::{Conversation, NewConversation, Room, RoomResponse, User};
type DbError = Box<dyn std::error::Error + Send + Sync>;

```

由于 SQLite 没有日期功能构建，我们将创建一个:

```
// src/db.rs
fn iso_date() -> String {
    let now = SystemTime::now();
    let now: DateTime<Utc> = now.into();
    return now.to_rfc3339();
}

```

#### 通过电话号码查找用户

这里，我们将设置一个查询，该查询将实现一个简单的登录特性，并使我们能够通过电话号码找到用户。我们使用这种登录方法只是作为一个例子。在生产中，您会希望使用一种易于验证和调试的方法:

```
// src/db.rs
pub fn find_user_by_phone(
    conn: &mut SqliteConnection,
    user_phone: String,
) -> Result<Option<User>, DbError> {
    use crate::schema::users::dsl::*;
    let user = users
        .filter(phone.eq(user_phone))
        .first::<User>(conn)
        .optional()?;
    Ok(user)
}

```

#### 添加新用户

这里有一个查询，用于存储注册我们应用程序的新用户。这也是我们认证系统的一部分。同样，请不要将这种方法用于您的生产应用程序:

```
// src/db.rs
pub fn insert_new_user(conn: &mut SqliteConnection, nm: &str, pn: &str) -> Result<User, DbError> {
    use crate::schema::users::dsl::*;
    let new_user = User {
        id: Uuid::new_v4().to_string(),
        username: nm.to_owned(),
        phone: pn.to_owned(),
        created_at: iso_date(),
    };
    diesel::insert_into(users).values(&new_user).execute(conn)?;
    Ok(new_user)
}

```

添加新用户后，我们现在插入新对话:

```
// src/db.rs
pub fn insert_new_conversation(
    conn: &mut SqliteConnection,
    new: NewConversation,
) -> Result<Conversation, DbError> {
    use crate::schema::conversations::dsl::*;
    let new_conversation = Conversation {
        id: Uuid::new_v4().to_string(),
        user_id: new.user_id,
        room_id: new.room_id,
        content: new.message,
        created_at: iso_date(),
    };
    diesel::insert_into(conversations)
        .values(&new_conversation)
        .execute(conn)?;
    Ok(new_conversation)
}

```

#### 查找聊天室和参与者

接下来，让我们设置一个查询，从数据库中获取所有聊天室和参与者:

```
// src/db.rs
pub fn get_all_rooms(conn: &mut SqliteConnection) -> Result<Vec<RoomResponse>, DbError> {
    use crate::schema::rooms;
    use crate::schema::users;
    let rooms_data: Vec<Room> = rooms::table.get_results(conn)?;
    let mut ids = HashSet::new();
    let mut rooms_map = HashMap::new();
    let data = rooms_data.to_vec();
    for room in &data {
        let user_ids = room
            .participant_ids
            .split(",")
            .into_iter()
            .collect::<Vec<_>>();
        for id in user_ids.to_vec() {
            ids.insert(id.to_string());
        }
        rooms_map.insert(room.id.to_string(), user_ids.to_vec());
    }
    let ids = ids.into_iter().collect::<Vec<_>>();
    let users_data: Vec<User> = users::table
        .filter(users::id.eq_any(ids))
        .get_results(conn)?;
    let users_map: HashMap<String, User> = HashMap::from_iter(
        users_data
            .into_iter()
            .map(|item| (item.id.to_string(), item)),
    );
    let response_rooms = rooms_data.into_iter().map(|room| {
        let users = rooms_map
            .get(&room.id.to_string())
            .unwrap()
            .into_iter()
            .map(|id| users_map.get(id.to_owned()).unwrap().clone())
            .collect::<Vec<_>>();
        return RoomResponse{ room, users };
    }).collect::<Vec<_>>();
    Ok(response_rooms)
}

```

## 用 React 构建客户端 UI

让我们为我们的客户端应用程序设计一个用户界面；最终结果将如下所示:

![CHat App Client UI Built with React](img/72c691484845f8cd3baadeb653c283ac.png)

首先，用 Next.js 创建一个 UI 项目:

```
yarn create next-app --js ui

```

将顺风 CSS 添加到项目中:

```
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

```

现在，更改顺风`config`文件:

```
// ui/tailwind.config.js
content: [
  "./pages/**/*.{js,ts,jsx,tsx}",
  "./components/**/*.{js,ts,jsx,tsx}",
]

```

我们将添加这个`package.json`配置，以便将我们的 Next.js 应用程序导出为静态 HTML 页面，这样我们就可以使用 Actix Web 通过文件服务器访问它们:

```
// ui/package.json
{
  "name": "ui",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build && next export -o ../static",
...

```

接下来，将 Tailwind CSS 实用程序导入到`globals.css`文件中:

```
// ui/styles/global.css
@tailwind base;
@tailwind components;
@tailwind utilities;

```

现在，让我们为我们的客户端应用程序创建一些组件。

### `avatar`组件

在这里，我们将为每个用户创建头像:

```
// ui/components/avatar.js
function getShortName(full_name = '') {
    if (full_name.includes(" ")) {
        const names = full_name.split(" ");
        return `${names[0].charAt(0)}${names[1].charAt(0)}`.toUpperCase()
    }
    return `${full_name.slice(0,2)}`.toUpperCase()
}
export default function Avatar({ children, color = '' }) {
  return (
    <div className='bg-blue-500 w-[45px] h-[45px] flex items-center justify-center rounded-full' style={{backgroundColor: color}}>
      <span className='font-bold text-sm text-white'>{getShortName(children)}</span>
    </div>
  )
}

```

### `login`组件

在这里，我们将创建用户登录组件:

```
// ui/components/login.js
import { useState } from "react";
async function createAccount({ username, phone }) {
    try {
        const url = "http://localhost:8080/users/create";
        let result = await fetch(url, {
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify({ username, phone })
        });
        return result.json();
    } catch (e) {
        return Promise.reject(e);
    }
}
async function signIn({ phone }) {
    try {
        const url = "http://localhost:8080/users/phone/" + phone;
        let result = await fetch(url);
        return result.json();
    } catch (e) {
        return Promise.reject(e);
    }
}
export default function Login({ show, setAuth }) {
    const [isShowSigIn, setShowSignIn] = useState(false);
    const showSignIn = () => {
        setShowSignIn(prev => !prev)
    }
    const FormCreateUsername = ({ setAuth }) => {
        const onCreateUsername = async (e) => {
            e.preventDefault();
            let username = e.target.username.value;
            let phone = e.target.phone.value;
            if (username === "" || phone === "") {
                return;
            }
            let res = await createAccount({ username, phone });
            if (res === null) {
                alert("Failed to create account");
                return;
            }
            setAuth(res)
        }
        return (
            <form action="" className="mt-4 space-y-2" onSubmit={onCreateUsername}>
                <div>
                    <label className="text-sm font-light">Username</label>
                    <input required type="text" name="username" placeholder="John Doe"
                        className="w-full px-4 py-2 mt-2 border rounded-md focus:outline-none focus:ring-1 focus:ring-blue-600" />
                </div>
                <div>
                    <label className="text-sm font-light">Phone</label>
                    <input required type="text" name="phone" placeholder="+1111..."
                        className="w-full px-4 py-2 mt-2 border rounded-md focus:outline-none focus:ring-1 focus:ring-blue-600" />
                </div>
                <div className="flex items-baseline justify-between">
                    <button type="submit"
                        className="px-6 py-2 mt-4 text-white bg-violet-600 rounded-lg hover:bg-violet-700 w-full">Submit</button>
                </div>
                <div className="pt-2 space-y-2 text-center">
                    <p className="text-base text-gray-700">Already have a username? <button onClick={showSignIn} className="text-violet-700 font-light">Sign In</button></p>
                </div>
            </form>
        )
    }
    const FormSignIn = ({ setAuth }) => {
        const onSignIn = async (e) => {
            e.preventDefault();
            let phone = e.target.phone.value;
            if (phone === "") {
                return;
            }
            let res = await signIn({ phone });
            if (res === null) {
                alert("Failed to create account");
                return;
            }
            if (!res.id) {
                alert(`Phone number not found ${phone}`);
                return;
            }
            setAuth(res)
        }
        return (
            <form action="" className="mt-4 space-y-2" onSubmit={onSignIn}>
                <div>
                    <label className="text-sm font-light">Phone</label>
                    <input required type="text" name="phone" placeholder="+1111..."
                        className="w-full px-4 py-2 mt-2 border rounded-md focus:outline-none focus:ring-1 focus:ring-blue-600" />
                </div>
                <div className="flex items-baseline justify-between">
                    <button type="submit"
                        className="px-6 py-2 mt-4 text-white bg-violet-600 rounded-lg hover:bg-violet-700 w-full">Submit</button>
                </div>
                <div className="pt-2 space-y-2 text-center">
                    <p className="text-base text-gray-700">Don't have username? <button onClick={showSignIn} className="text-violet-700 font-light">Create</button></p>
                </div>
            </form>
        )
    }
    return (
        <div className={`${show ? '' : 'hidden'} bg-gradient-to-b from-orange-400 to-rose-400`}>
            <div className="flex items-center justify-center min-h-screen">
                <div className="px-8 py-6 mt-4 text-left bg-white  max-w-[400px] w-full rounded-xl shadow-lg">
                    <h3 className="text-xl text-slate-800 font-semibold">{isShowSigIn ? 'Log in with your phone.' : 'Create your account.'}</h3>
                    {isShowSigIn ? <FormSignIn setAuth={setAuth} /> : <FormCreateUsername setAuth={setAuth} />}
                </div>
            </div>
        </div>
    )
}

```

### `room`组件

在这里，我们将创建聊天室组件:

```
// ui/components/room.js
import React, { useState, useEffect } from "react";
import Avatar from "./avatar";
async function getRooms() {
    try {
        const url = "http://localhost:8080/rooms";
        let result = await fetch(url);
        return result.json();
    } catch (e) {
        console.log(e);
        return Promise.resolve(null);
    }
}
function ChatListItem({ onSelect, room, userId, index, selectedItem }) {
    const { users, created_at, last_message } = room;
    const active = index == selectedItem;
    const date = new Date(created_at);
    const ampm = date.getHours() >= 12 ? 'PM' : 'AM';
    const time = `${date.getHours()}:${date.getMinutes()} ${ampm}`
    const name = users?.filter(user => user.id != userId).map(user => user.username)[0];
    return (
        <div
            onClick={() => onSelect(index, {})}
            className={`${active ? 'bg-[#FDF9F0] border border-[#DEAB6C]' : 'bg-[#FAF9FE] border border-[#FAF9FE]'} p-2 rounded-[10px] shadow-sm cursor-pointer`} >
            <div className='flex justify-between items-center gap-3'>
                <div className='flex gap-3 items-center w-full'>
                    <Avatar>{name}</Avatar>
                    <div className="w-full max-w-[150px]">
                        <h3 className='font-semibold text-sm text-gray-700'>{name}</h3>
                        <p className='font-light text-xs text-gray-600 truncate'>{last_message}</p>
                    </div>
                </div>
                <div className='text-gray-400 min-w-[55px]'>
                    <span className='text-xs'>{time}</span>
                </div>
            </div>
        </div>
    )
}
export default function ChatList({ onChatChange, userId }) {
    const [data, setData] = useState([])
    const [isLoading, setLoading] = useState(false)
    const [selectedItem, setSelectedItem] = useState(-1);
    useEffect(() => {
        setLoading(true)
        getRooms()
            .then((data) => {
                setData(data)
                setLoading(false)
            })
    }, [])
    const onSelectedChat = (idx, item) => {
        setSelectedItem(idx)
        let mapUsers = new Map();
        item.users.forEach(el => {
            mapUsers.set(el.id, el);
        });
        const users = {
            get: (id) => {
                return mapUsers.get(id).username;
            },
            get_target_user: (id) => {
                return item.users.filter(el => el.id != id).map(el => el.username).join("")
            }
        }
        onChatChange({ ...item.room, users })
    }
    return (
        <div className="overflow-hidden space-y-3">
            {isLoading && <p>Loading chat lists.</p>}
            {
                data.map((item, index) => {
                    return <ChatListItem
                        onSelect={(idx) => onSelectedChat(idx, item)}
                        room={{ ...item.room, users: item.users }}
                        index={index}
                        key={item.room.id}
                        userId={userId}
                        selectedItem={selectedItem} />
                })
            }
        </div>
    )
}

```

### `conversation`组件

在这里，我们将创建用户对话组件:

```
// ui/components/conversation.js
import React, { useEffect, useRef } from "react";
import Avatar from "./avatar"
function ConversationItem({ right, content, username }) {
    if (right) {
        return (
            <div className='w-full flex justify-end'>
                <div className='flex gap-3 justify-end'>
                    <div className='max-w-[65%] bg-violet-500 p-3 text-sm rounded-xl rounded-br-none'>
                        <p className='text-white'>{content}</p>
                    </div>
                    <div className='mt-auto'>
                        <Avatar>{username}</Avatar>
                    </div>
                </div>
            </div>
        )
    }
    return (
        <div className='flex gap-3 w-full'>
            <div className='mt-auto'>
                <Avatar color='rgb(245 158 11)'>{username}</Avatar>
            </div>
            <div className='max-w-[65%] bg-gray-200 p-3 text-sm rounded-xl rounded-bl-none'>
                <p>{content}</p>
            </div>
        </div>
    )
}
export default function Conversation({ data, auth, users }) {
    const ref = useRef(null);
    useEffect(() => {
        ref.current?.scrollTo(0, ref.current.scrollHeight)
    }, [data]);
    return (
        <div className='p-4 space-y-4 overflow-auto' ref={ref}>
            {
                data.map(item => {
                    return <ConversationItem
                        right={item.user_id === auth.id}
                        content={item.content}
                        username={users.get(item.user_id)}
                        key={item.id} />
                })
            }
        </div>
    )
}

```

现在让我们准备好与 WebSocket 服务器和 REST API 服务器交互所需的钩子。

### `useWebsocket`挂钩

这个钩子用于连接 WebSocket 服务器，使我们能够发送和接收消息:

```
// ui/libs/websocket.js
import { useEffect, useRef } from "react";
export default function useWebsocket(onMessage) {
    const ws = useRef(null);
    useEffect(() => {
        if (ws.current !== null) return;
        const wsUri = 'ws://localhost:8080/ws';
        ws.current = new WebSocket(wsUri);
        ws.current.onopen = () => console.log("ws opened");
        ws.current.onclose = () => console.log("ws closed");
        const wsCurrent = ws.current;
        return () => {
            wsCurrent.close();
        };
    }, []);
    useEffect(() => {
        if (!ws.current) return;
        ws.current.onmessage = e => {
            onMessage(e.data)
        };
    }, []);
    const sendMessage = (msg) => {
        if (!ws.current) return;
        ws.current.send(msg);
    }
    return sendMessage;
}

```

### `useLocalStorage`挂钩

这个钩子使我们能够从 localStorage 获取用户数据:

```
// ui/libs/useLocalStorage
import { useEffect, useState } from "react";
export default function useLocalStorage(key, defaultValue) {
  const [storedValue, setStoredValue] = useState(defaultValue);
  const setValue = (value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      if (typeof window !== "undefined") {
        window.localStorage.setItem(key, JSON.stringify(valueToStore));
      }
    } catch (error) {
    }
  };
  useEffect(() => {
    try {
      const item = window.localStorage.getItem(key);
      let data = item ? JSON.parse(item) : defaultValue;
      setStoredValue(data)
    } catch (error) {}
  }, [])
  return [storedValue, setValue];
}

```

### `useConversation`挂钩

我们将使用这个钩子来获取基于给定房间`id`的对话:

```
import { useEffect, useState } from "react";
const fetchRoomData = async (room_id) => {
    if (!room_id) return;
    const url = `http://localhost:8080/conversations/${room_id}`;
    try {
        let resp = await fetch(url).then(res => res.json());
        return resp;
    } catch (e) {
        console.log(e);
    }
}
export default function useConversations(room_id) {
    const [isLoading, setIsLoading] = useState(true);
    const [messages, setMessages] = useState([]);
    const updateMessages = (resp = []) => {
        setIsLoading(false);
        setMessages(resp)
    }
    const fetchConversations = (id) => {
        setIsLoading(true)
        fetchRoomData(id).then(updateMessages)
    }
    useEffect(() => fetchConversations(room_id), []);
    return [isLoading, messages, setMessages, fetchConversations];
}

```

## 构建聊天应用程序

现在让我们连接所有的组件和钩子，在 React with Next.js 中构建我们的聊天应用程序。

首先，让我们导入我们需要的所有依赖项:

```
// ui/pages/index.js
import Head from 'next/head'
import React, { useEffect, useState } from 'react'
import Avatar from '../components/avatar'
import ChatList from '../components/rooms'
import Conversation from '../components/conversation'
import Login from '../components/login'
import useConversations from '../libs/useConversation'
import useLocalStorage from '../libs/useLocalStorage'
import useWebsocket from '../libs/useWebsocket'

```

现在，让我们为聊天页面设置状态:

```
// ui/pages/index.js
...
export default function Home() {
  const [room, setSelectedRoom] = useState(null);
  const [isTyping, setIsTyping] = useState(false);
  const [showLogIn, setShowLogIn] = useState(false);
  const [auth, setAuthUser] = useLocalStorage("user", false);
  const [isLoading, messages, setMessages, fetchConversations] = useConversations("");
  ...
}

```

以下函数将处理所有进出 WebSocket 服务器的消息:

*   `handleTyping`:更新状态显示打字指示器
*   `handleMessage`:处理状态的传入和传出消息
*   `onMessage`:处理从 WebSocket 服务器检索到的消息
*   `updateFocus`:告知 WebSocket 服务器当前用户是否仍在输入消息
*   `onFocusChange`:让 WebSocket 服务器知道当前用户何时完成输入
*   `submitMessage`:当用户点击**发送**按钮时，更新消息状态，然后将消息发送给服务器

下面是我们如何在代码中使用这些函数:

```
// ui/pages/index.js
  const handleTyping = (mode) => {
    if (mode === "IN") {
      setIsTyping(true)
    } else {
      setIsTyping(false)
    }
  }
  const handleMessage = (msg, userId) => {
    setMessages(prev => {
      const item = { content: msg, user_id: userId };
      return [...prev, item];
    })
  }
  const onMessage = (data) => {
    try {
      let messageData = JSON.parse(data);
      switch (messageData.chat_type) {
        case "TYPING": {
          handleTyping(messageData.value[0]);
          return;
        }
        case "TEXT": {
          handleMessage(messageData.value[0], messageData.user_id);
          return;
        }
      }
    } catch (e) {
      console.log(e);
    }
  }
  const sendMessage = useWebsocket(onMessage)
  const updateFocus = () => {
    const data = {
      id: 0,
      chat_type: "TYPING",
      value: ["IN"],
      room_id: room.id,
      user_id: auth.id
    }
    sendMessage(JSON.stringify(data))
  }
  const onFocusChange = () => {
    const data = {
      id: 0,
      chat_type: "TYPING",
      value: ["OUT"],
      room_id: room.id,
      user_id: auth.id
    }
    sendMessage(JSON.stringify(data))
  }
  const submitMessage = (e) => {
    e.preventDefault();
    let message = e.target.message.value;
    if (message === "") {
      return;
    }
    if (!room.id) {
      alert("Please select chat room!")
      return
    }
    const data = {
      id: 0,
      chat_type: "TEXT",
      value: [message],
      room_id: room.id,
      user_id: auth.id
    }
    sendMessage(JSON.stringify(data))
    e.target.message.value = "";
    handleMessage(message, auth.id);
    onFocusChange();
  }

```

我们将使用以下函数来处理更新消息以及用户登录和注销的状态:

*   `updateMessages`:当用户切换聊天室时，获取给定房间`id`的对话
*   `signOut`:将状态更新为注销，并从本地存储中删除用户数据

我们将在代码中使用这些函数，如下所示:

```
// ui/pages/index.js
  const updateMessages = (data) => {
    if (!data.id) return;
    fetchConversations(data.id)
    setSelectedRoom(data)
  }
  const signOut = () => {
    window.localStorage.removeItem("user");
    setAuthUser(false);
  }
  useEffect(() => setShowLogIn(!auth), [auth])

```

现在，让我们向客户端显示所有数据:

```
  return (
    <div>
      <Head>
        <title>Rust with react chat app</title>
        <meta name="description" content="Rust with react chat app" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <Login show={showLogIn} setAuth={setAuthUser} />
      <div className={`${!auth && 'hidden'} bg-gradient-to-b from-orange-400 to-rose-400 h-screen p-12`}>
        <main className='flex w-full max-w-[1020px] h-[700px] mx-auto bg-[#FAF9FE] rounded-[25px] backdrop-opacity-30 opacity-95'>
          <aside className='bg-[#F0EEF5] w-[325px] h-[700px] rounded-l-[25px] p-4 overflow-auto relative'>
            <ChatList onChatChange={updateMessages} userId={auth.id} />
            <button onClick={signOut} className='text-xs w-full max-w-[295px] p-3 rounded-[10px] bg-violet-200 font-semibold text-violet-600 text-center absolute bottom-4'>LOG OUT</button>
          </aside>
          {room?.id && (<section className='rounded-r-[25px] w-full max-w-[690px] grid grid-rows-[80px_minmax(450px,_1fr)_65px]'>
            <div className='rounded-tr-[25px] w-ful'>
              <div className='flex gap-3 p-3 items-center'>
                <Avatar color='rgb(245 158 11)'>{room.users.get_target_user(auth.id)}</Avatar>
                <div>
                  <p className='font-semibold text-gray-600 text-base'>{room.users.get_target_user(auth.id)}</p>
                  <div className='text-xs text-gray-400'>{isTyping ? "Typing..." : "10:15 AM"}</div>
                </div>
              </div>
              <hr className='bg-[#F0EEF5]' />
            </div>
            {(isLoading && room.id) && <p className="px-4 text-slate-500">Loading conversation...</p>}
            <Conversation data={messages} auth={auth} users={room.users} />
            <div className='w-full'>
              <form onSubmit={submitMessage} className='flex gap-2 items-center rounded-full border border-violet-500 bg-violet-200 p-1 m-2'>
                <input
                  onBlur={onFocusChange}
                  onFocus={updateFocus}
                  name="message"
                  className='p-2 placeholder-gray-600 text-sm w-full rounded-full bg-violet-200 focus:outline-none'
                  placeholder='Type your message here...' />
                <button type='submit' className='bg-violet-500 rounded-full py-2 px-6 font-semibold text-white text-sm'>Sent</button>
              </form>
            </div>
          </section>)}
        </main>
      </div>
    </div>
  )

```

## 结论

在本文中，我们讨论了 WebSockets 的特性，它在 Rust 中的应用，以及如何在`actix-web`包中使用它。我们演示了如何创建一个高效的实时聊天应用程序，使用 React 和 Next.js 建立到 Actix Web 服务器的 WebSocket 连接。这篇文章的代码可以在 [GitHub](https://github.com/ahmadrosid/rust-react-chat) 上找到。

为了进一步改进我们的示例实时聊天应用程序，您可以让它显示用户状态(即在线或离线)并为用户创建一个在线群聊。

如果您对本文有任何疑问，请随时发表评论。编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。