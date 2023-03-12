# 如何在 Rust 中创建 GraphQL 服务器

> 原文：<https://blog.logrocket.com/how-to-create-a-graphql-server-in-rust/>

随着围绕 Rust 的生态系统的增长，它正成为后端 web 服务的一个更有吸引力的选择。在本指南中，我们将向您展示如何在 Rust 中使用 Juniper 库对 Postgres 进行查询和变异。

作为参考，最终应用程序的完整源代码可以在 [GitHub](https://github.com/joshua-cooper/rust-graphql-intro) 上找到。

## 项目设置

首先用 Cargo 创建一个新项目，并将所需的依赖项添加到`Cargo.toml`中。

```
cargo new graphql_intro
cd graphql_intro
# Cargo.toml
[dependencies]
warp = "0.2"
tokio = { version = "0.2", features = ["macros"] }
serde_json = "1.0"
futures = { version = "0.3.1", features = ["compat"] }
futures-macro = "=0.3.1"
juniper = { git = "https://github.com/graphql-rust/juniper", branch = "async-await", features = ["async"] }
tokio-postgres = { version = "0.5", features = ["with-uuid-0_8"] }
uuid = { version = "0.8", features = ["v4"] }

```

我们将使用 Juniper 来实现特定于 GraphQL 的功能，使用 warp 来实现 web 服务器，使用`tokio-postgres`来访问数据库。因为我们将使用异步信任，所以需要一个执行器来轮询未来。在这个例子中，我们将使用 Tokio 提供的带有异步主函数的宏特性标志的执行器。

## 设置 web 服务器

在开始使用 GraphQL 之前，我们应该设置一个 web 服务器来处理 HTTP 请求。Juniper 的一大优点是，它可以轻松地与任何 web 服务器配合使用。如果你想使用 warp 之外的东西，请随意修改这个例子。

web 服务器中只需要两条路由:一条处理 GraphQL 请求，另一条服务于 graph QL 测试客户机。

```
// main.rs
use warp::Filter;
use std::sync::Arc;
use juniper::http::graphiql::graphiql_source;

#[tokio::main]
async fn main () {
    let schema = Arc::new(Schema::new(QueryRoot, MutationRoot));
    // Create a warp filter for the schema
    let schema = warp::any().map(move || Arc::clone(&schema));

    let ctx = Arc::new(Context { client });
    // Create a warp filter for the context
    let ctx = warp::any().map(move || Arc::clone(&ctx));

    let graphql_route = warp::post()
        .and(warp::path!("graphql"))
        .and(schema.clone())
        .and(ctx.clone())
        .and(warp::body::json())
        .and_then(graphql);

    let graphiql_route = warp::get()
        .and(warp::path!("graphiql"))
        .map(|| warp::reply::html(graphiql_source("graphql")));

    let routes = graphql_route.or(graphiql_route);

    warp::serve(routes).run(([127, 0, 0, 1], 8000)).await;
}

```

这还不能编译；我们仍然需要定义`Schema`、`QueryRoot`、`MutationRoot`和`Context`类型。

首先，我们定义了一些模式，并把它做成一个 warp 过滤器，这样我们就可以在我们的 route 处理程序中访问它。然后我们对一个上下文做了同样的事情，它可以包含像数据库连接这样的东西。

接下来，我们创建了`graphql_route`变量，这是一个 warp 过滤器，将任何 POST 请求与路径“graphql”匹配，然后使模式、上下文和 JSON 主体对一个名为`graphql`的处理程序可用，我们将在后面定义它。

类似地，`graphiql_route`变量是一个过滤器，它将任何 GET 请求匹配到路径“graphiql ”,并以 HTML 响应 GraphiQL 客户端。

最后，将这些过滤器组合起来，启动服务器。

为了编译这段代码，让我们定义那些我们错过的类型。

```
use juniper::RootNode;
use tokio_postgres::Client;

struct QueryRoot;
struct MutationRoot;

#[juniper::graphql_object(Context = Context)]
impl QueryRoot {}

#[juniper::graphql_object(Context = Context)]
impl MutationRoot {}

type Schema = RootNode<'static, QueryRoot, MutationRoot>;

struct Context {
    client: Client,
}

impl juniper::Context for Context {}

```

接下来，定义`graphql`路由处理器。

```
use std::convert::Infallible;
use juniper::http::GraphQLRequest;

async fn graphql(
    schema: Arc<Schema>,
    ctx: Arc<Context>,
    req: GraphQLRequest,
) -> Result<impl warp::Reply, Infallible> {
    let res = req.execute_async(&schema, &ctx).await;
    let json = serde_json::to_string(&res).expect("Invalid JSON response");
    Ok(json)
}

```

创建到 Postgres 数据库的连接。

```
use tokio_postgres::NoTls;

#[tokio::main]
async fn main() {
    let (client, connection) = tokio_postgres::connect("host=localhost user=postgres", NoTls)
        .await
        .unwrap();

    // The connection object performs the actual communication with the database,
    // so spawn it off to run on its own.
    tokio::spawn(async move {
        if let Err(e) = connection.await {
            eprintln!("connection error: {}", e);
        }
    });

    // ...
}

```

请注意，我们在这里创建的客户端是在`ctx`中使用的。从现在开始，您需要有一个正在运行的 Postgres 实例来连接它。如果您想使用 Docker，下面的命令将为您启动一个数据库容器。

```
docker run --rm -it -e POSTGRES_PASSWORD=postgres -p 5432:5432 postgres:alpine

```

如果您的数据库使用不同的凭证，请确保将它们放在`tokio_postgres::connect()`中。此时，您可以编译并运行服务器，然后在浏览器中打开[http://localhost:8000/graphiql](http://localhost:8000/graphiql)来查看它是否工作。

## 数据库模式

在使用 GraphQL 解析器之前，要做的最后一件事是定义一个数据库模式。对于这个例子，我们将创建一个客户 API，允许我们查询客户数据并创建新的客户条目。

让我们在连接建立之后创建一个表。

```
client
    .execute(
        "CREATE TABLE IF NOT EXISTS customers(
            id UUID PRIMARY KEY,
            name TEXT NOT NULL,
            age INT NOT NULL,
            email TEXT UNIQUE NOT NULL,
            address TEXT NOT NULL
        )",
        &[],
    )
    .await
    .expect("Could not create table");

```

execute 方法中的第一个参数只是普通的 SQL，第二个参数是 paramaters 数组。在这种情况下，我们没有使用任何参数，但我们稍后会使用它们。

## GraphQL

现在我们已经设置了服务器样板和数据库，我们可以开始实现 GraphQL 解析器来使它正常工作。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们为客户数据创建一个结构。

```
#[derive(juniper::GraphQLObject)]
struct Customer {
    id: String,
    name: String,
    age: i32,
    email: String,
    address: String,
}

```

请注意结构上方的 derive 宏。这就是我们要使我们的自定义数据类型与 Juniper 无缝协作所需要做的一切。因为我们的数据库目前是空的，所以我们将从 GraphQL 突变开始添加客户数据。

### GraphQL 突变

还记得之前的`MutationRoot`吗？它是实现所有 GraphQL 变化的结构。

我们可以做的最简单的改变是为新客户创建一个记录。这里有一个我们之前创建的`impl`块中的虚拟`register_customer`方法。

```
#[juniper::graphql_object(Context = Context)]
impl MutationRoot {
    async fn register_customer(
        ctx: &Context,
        name: String,
        age: i32,
        email: String,
        address: String,
    ) -> juniper::FieldResult<Customer> {
        Ok(Customer {
            id: "1".into(),
            name,
            age,
            email,
            address,
        })
    }
}

```

同样，我们利用 Rust 宏来删除大量样板文件。在这种情况下，我们使包含数据库连接的上下文对所有突变都可用。

现在，`register_customer`方法立即返回一个 ID 为`1`的用户，以及传入的姓名、年龄、电子邮件和地址。用户被包裹在`Ok()`中，因为这个方法返回一个`Result`。

现在你可以运行服务器并在[打开浏览器 http://localhost:8000/graphiql](http://localhost:8000/graphiql)来测试这个虚拟突变。

```
mutation {
  registerCustomer(name: "John Smith", age: 29, email: "[email protected]", address: "19 Small Street London") {
    id,
    name,
    age,
  }
}

```

如您所见，我们惯用的 Rust snake_cased 方法名已经自动更改为惯用的 GraphQL camelCase，并且所有的 GraphQL 类型都是从我们的`Customer`结构中派生出来的。如果您试图删除`registerCustomer`的一些参数，您会得到一个不错的错误消息。Rust 没有空类型，所以我们必须明确选择 Rust 的`Option`类型的可空字段。

我们可以将数据存储在数据库中，而不仅仅是返回数据。

```
async fn register_customer(
    ctx: &Context,
    name: String,
    age: i32,
    email: String,
    address: String,
) -> juniper::FieldResult<Customer> {
    let id = uuid::Uuid::new_v4();
    let email = email.to_lowercase();
    ctx.client
        .execute(
            "INSERT INTO customers (id, name, age, email, address) VALUES ($1, $2, $3, $4, $5)",
            &[&id, &name, &age, &email, &address],
        )
        .await?;
    Ok(Customer {
        id: id.to_string(),
        name,
        age,
        email,
        address,
    })
}

```

现在我们正在生成一个随机 UUID，然后将用户保存在数据库中。我们还通过将提供的电子邮件转换成小写来使其规范化。您可以看到参数化的 SQL 和这里的`tokio_postgres`是什么样子，其中`execute`的第二个参数是我们想要使用的数据的引用列表。

与虚拟示例不同，这可能会产生错误。幸运的是，Juniper 会相应地格式化响应。任何实现`std::fmt::Display`的东西都可以被格式化为错误，所以很容易使用定制的错误消息。但是现在，我们将只使用由`tokio_postgres`提供的那些。

其他的突变也会和这个相似。让我们看看如何更新客户的电子邮件和删除客户记录。

```
async fn update_customer_email(
    ctx: &Context,
    id: String,
    email: String,
) -> juniper::FieldResult<String> {
    let uuid = uuid::Uuid::parse_str(&id)?;
    let email = email.to_lowercase();
    let n = ctx
        .client
        .execute(
            "UPDATE customers SET email = $1 WHERE id = $2",
            &[&email, &uuid],
        )
        .await?;
    if n == 0 {
        return Err("User does not exist".into());
    }
    Ok(email)
}

async fn delete_customer(ctx: &Context, id: String) -> juniper::FieldResult<bool> {
    let uuid = uuid::Uuid::parse_str(&id)?;
    let n = ctx
        .client
        .execute("DELETE FROM customers WHERE id = $1", &[&uuid])
        .await?;
    if n == 0 {
        return Err("User does not exist".into());
    }
    Ok(true)
}

```

这一次，我们检查更新了多少行。如果没有行被更新，这意味着用户不存在，我们返回一个错误。

### 你想要什么

查询以类似于突变的方式实现，但是使用了`QueryRoot`而不是`MutationRoot`。我们的第一个查询将简单地查找具有给定 ID 的客户。

```
#[juniper::graphql_object(Context = Context)]
impl QueryRoot {
    async fn customer(ctx: &Context, id: String) -> juniper::FieldResult<Customer> {
        let uuid = uuid::Uuid::parse_str(&id)?;
        let row = ctx
            .client
            .query_one(
                "SELECT name, age, email, address FROM customers WHERE id = $1",
                &[&uuid],
            )
            .await?;
        let customer = Customer {
            id,
            name: row.try_get(0)?,
            age: row.try_get(1)?,
            email: row.try_get(2)?,
            address: row.try_get(3)?,
        };
        Ok(customer)
    }
}

```

我们没有在数据库客户机上使用`execute`，而是使用`query_one`，它将返回一个数据库行或一个错误。我们会做类似的事情来查询客户列表。

```
async fn customers(ctx: &Context) -> juniper::FieldResult<Vec<Customer>> {
    let rows = ctx
        .client
        .query("SELECT id, name, age, email, address FROM customers", &[])
        .await?;
    let mut customers = Vec::new();
    for row in rows {
        let id: uuid::Uuid = row.try_get(0)?;
        let customer = Customer {
            id: id.to_string(),
            name: row.try_get(1)?,
            age: row.try_get(2)?,
            email: row.try_get(3)?,
            address: row.try_get(4)?,
        };
        customers.push(customer);
    }
    Ok(customers)
}

```

这一次，我们使用了`query`,因为我们期望从数据库中得到不止一行。之后，循环遍历每一行，并将每个客户添加到一个向量中。在 GraphiQL 测试客户端中尝试此查询；它应该返回一个客户列表。

```
{
  customers {
    id
    name
    email
    address
  }
}

```

## 结论

现在，您应该已经很好地理解了 Rust 中的 GraphQL 如何与 Juniper 协同工作。这里我们只使用了一种数据类型，但是添加更多的数据类型也是一样的。您甚至可以使用具有嵌套自定义数据类型的字段，由于 Juniper 提供的宏，一切都将正常工作。下一步可能是向 API 添加身份验证和权限，并为所有可能的失败情况使用自定义错误类型。

Rust 是构建可靠和高性能 web 后端的一个很好的选择，它强大的宏支持使使用 GraphQL 成为一种绝对的乐趣。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.