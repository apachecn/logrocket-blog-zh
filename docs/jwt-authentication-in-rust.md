# Rust - LogRocket 博客中的 JWT 认证

> 原文：<https://blog.logrocket.com/jwt-authentication-in-rust/>

JSON Web token(jwt)是一种安全地表示系统间属性或声明的标准。它们可以以客户机-服务器的方式使用，以实现无状态授权，而 cookies 本质上是有状态的。

然而，它们比这更灵活，还可以用在许多其他方面。一个突出的用例是微服务架构中的安全用户状态传播。在这样的设置中，jwt 的用例可以完全局限于后端，前端有状态授权机制。登录后，会话令牌被映射到 JWT，然后在微服务集群中用于授权请求(访问控制)，以及分发用户状态(信息分发)。

这样做的好处是，其他服务或客户端不需要重新提取信息，这些信息存储在 JWT 中。例如，用户角色、用户电子邮件或您需要定期访问的任何内容都可以编码到 JWT 中。因为 jwt 是加密签名的，所以存储在其中的数据是安全的，不容易被操纵。

在本教程中，我们将解释如何在 Rust web 应用程序中使用 jwt 实现身份验证和授权。我们不会详细讨论 jwt 本身；关于这个话题已经有了大量的资源。

我们将构建的示例将更多地关注 JWTs 的访问控制部分，因此我们将只在令牌中保存用户 ID 和用户角色——这是我们确保用户被允许访问资源所需的一切。

与安全相关的博客帖子一样，这里有一个简短的免责声明:这篇博客帖子中显示的代码还没有准备好投入生产，不应该复制/粘贴。这个例子的唯一目的是展示在构建身份验证/授权系统时可能会用到的一些概念、技术和库。

说完了，让我们开始吧！

## 设置

接下来，您需要一个最新的 Rust 安装(1.39+)和一个发送 HTTP 请求的工具，比如 cURL。

首先，创建一个新的 Rust 项目。

```
cargo new rust-jwt-example
cd rust-jwt-example

```

接下来，编辑`Cargo.toml`文件并添加您需要的依赖项。

```
[dependencies]
jsonwebtoken = "=7.2"
tokio = { version = "0.2", features = ["macros", "rt-threaded", "sync", "time"] }
warp = "0.2"
serde = {version = "1.0", features = ["derive"] }
serde_json = "1.0"
thiserror = "1.0"
chrono = "0.4"

```

我们将使用轻量级 warp 库构建 web 应用程序，该库使用 tokio 作为其异步运行时。我们将使用 Serde 进行 JSON 处理，使用 Thiserror 和 Chrono 分别处理错误和日期。

为了处理 JSON Web 令牌，我们将使用名为 [jsonwebtoken](https://crates.io/crates/jsonwebtoken) 的机箱，这是一个成熟的、在 Rust 生态系统中广泛使用的工具。

## 网络服务器

我们将从创建一个简单的 web 服务器开始，该服务器有几个端点和一个内存用户存储。在实际应用中，我们可能会有一个用于用户存储的数据库。但是，因为这对我们的例子来说并不重要，我们将简单地把它们硬编码在内存中。

```
type Result<T> = std::result::Result<T, error::Error>;
type WebResult<T> = std::result::Result<T, Rejection>;
type Users = Arc<RwLock<HashMap<String, User>>>;

```

这里我们为`Result`定义了两个助手类型，指定了一个内部结果类型用于在整个应用程序中传播错误，一个外部结果类型用于向调用者发送错误。

我们还定义了`Users`类型，它是一个共享的`HashMap`。这是我们的内存用户存储，我们可以这样初始化它:

```
mod auth;
mod error;

#[derive(Clone)]
pub struct User {
    pub uid: String,
    pub email: String,
    pub pw: String,
    pub role: String,
}

#[tokio::main]
async fn main() {
    let users = Arc::new(RwLock::new(init_users()));
    ...
}

fn init_users() -> HashMap<String, User> {
    let mut map = HashMap::new();
    map.insert(
        String::from("1"),
        User {
            uid: String::from("1"),
            email: String::from("[email protected]"),
            pw: String::from("1234"),
            role: String::from("User"),
        },
    );
    map.insert(
        String::from("2"),
        User {
            uid: String::from("2"),
            email: String::from("[email protected]"),
            pw: String::from("4321"),
            role: String::from("Admin"),
        },
    );
    map
}

```

我们使用一个`HashMap`，它使我们能够很容易地通过用户的 ID 进行搜索。该映射被包装在一个`RwLock`中，因为多个线程可以同时访问用户映射。这也是它最终被放入`Arc`的原因——一个原子的、引用计数的智能指针——它使我们能够在线程之间共享这个映射。

由于我们正在构建一个异步 web 服务，并且我们无法预先知道我们的处理程序未来将在哪些线程上运行，所以我们需要使我们传递的所有内容都是线程安全的。

我们将用两个用户设置用户映射:一个角色为`User`，另一个角色为`Admin`。稍后，我们将创建端点，这些端点只能由`Admin`角色访问。这样，我们可以测试我们的授权逻辑是否按预期工作。

由于我们使用 warp，我们还需要构建一个过滤器来将用户地图传递给端点。

```
fn with_users(users: Users) -> impl Filter<Extract = (Users,), Error = Infallible> + Clone {
    warp::any().map(move || users.clone())
}

```

完成第一步设置后，我们可以定义一些基本的路由并启动 web 服务器。

```
#[tokio::main]
async fn main() {
    let users = Arc::new(RwLock::new(init_users()));

    let login_route = warp::path!("login")
        .and(warp::post())
        .and_then(login_handler);

    let user_route = warp::path!("user")
        .and_then(user_handler);
    let admin_route = warp::path!("admin")
        .and_then(admin_handler);

    let routes = login_route
        .or(user_route)
        .or(admin_route)
        .recover(error::handle_rejection);

    warp::serve(routes).run(([127, 0, 0, 1], 8000)).await;
}

pub async fn login_handler() -> WebResult<impl Reply> {
    Ok("Login")
}

pub async fn user_handler() -> WebResult<impl Reply> {
    Ok("User")
}

pub async fn admin_handler() -> WebResult<impl Reply> {
    Ok("Admin")
}

```

在上面的代码片段中，我们定义了三个处理程序:

*   `POST /login` —使用电子邮件和密码登录
*   `GET /user` —每个用户的端点
*   `GET /admin` —仅供管理员使用的终端

先不要担心`.recover(error::handle_rejection)`；稍后我们将处理错误处理。

## 证明

让我们构建登录功能，以便用户和管理员可以进行身份验证。

第一步是获取`login_handler`中的凭证。

```
#[derive(Deserialize)]
pub struct LoginRequest {
    pub email: String,
    pub pw: String,
}

#[derive(Serialize)]
pub struct LoginResponse {
    pub token: String,
}

```

这是我们为登录机制定义的 API。客户机发送一封电子邮件和密码，并接收一个 JSON Web 令牌作为响应，然后客户机可以使用这个令牌通过将这个令牌放入`Authorization: Bearer $token`头字段来发出认证请求。

我们把这个定义为`login_handler`的一个体，就像这样:

```
async fn main() {
    ...
    let login_route = warp::path!("login")
        .and(warp::post())
        .and(with_users(users.clone()))
        .and(warp::body::json())
        .and_then(login_handler);
    ...
}

```

在`login_handler`中，签名和实现更改为:

```
pub async fn login_handler(users: Users, body: LoginRequest) -> WebResult<impl Reply> {
    match users.read() {
        Ok(read_handle) => {
            match read_handle
                .iter()
                .find(|(_uid, user)| user.email == body.email && user.pw == body.pw)
            {
                Some((uid, user)) => {
                    let token = auth::create_jwt(&uid, &Role::from_str(&user.role))
                        .map_err(|e| reject::custom(e))?;
                    Ok(reply::json(&LoginResponse { token }))
                }
                None => Err(reject::custom(WrongCredentialsError)),
            }
        }
        Err(_) => Err(reject()),
    }
}

```

这里发生了什么事？首先，我们通过调用`.read()`来访问共享的`Users`地图，这为我们提供了地图上的读锁。这就是我们目前所需要的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

然后，我们迭代这个只读版本的用户映射，试图找到一个具有传入主体中提供的`email`和`pw`的用户。

如果我们没有找到用户，我们返回一个`WrongCredentialsError`，告诉用户他们没有使用有效的凭证。否则，我们用现有用户的用户 ID 和角色调用`auth::create_jwt`，这将返回一个`token`。这是我们返回给调用者的内容。

接下来我们来看看`auth`模块。

在`auth.rs`中，我们首先定义一些有用的数据类型和常量。

```
const BEARER: &str = "Bearer ";
const JWT_SECRET: &[u8] = b"secret";

#[derive(Clone, PartialEq)]
pub enum Role {
    User,
    Admin,
}

impl Role {
    pub fn from_str(role: &str) -> Role {
        match role {
            "Admin" => Role::Admin,
            _ => Role::User,
        }
    }
}

impl fmt::Display for Role {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        match self {
            Role::User => write!(f, "User"),
            Role::Admin => write!(f, "Admin"),
        }
    }
}

#[derive(Debug, Deserialize, Serialize)]
struct Claims {
    sub: String,
    role: String,
    exp: usize,
}

```

`Role`枚举仅仅是`Admin`和`User`角色的映射，所以我们不需要处理字符串，这对于像这样的安全关键的东西来说太容易出错了。

我们还定义了 helper 方法，在来自`Role`枚举的字符串之间进行转换，因为这个角色保存在 JWT 中。

另一个重要类型是`Claims`。这是我们将保存在内部并期望我们的 jwt 的数据。`sub`描绘了所谓的主体，所以在这种情况下是“谁”。`exp`是令牌的到期日期。我们还将用户`role`作为自定义数据点放在那里。

这两个常量是预期的`Authorization`头的前缀和非常重要的`JWT_SECRET`。这是我们用来签署 JSON Web 令牌的密钥。在真实的系统中，这将是一个很长的、安全存储的、定期更改的字符串。如果这个秘密泄露出去，任何人都可以解码用这个秘密创建的所有 jwt。例如，您还可以为每个用户使用不同的密码，这样，在数据泄露的情况下，只需更改这个密码，您就可以轻松地使用户的所有令牌失效。

接下来我们来看看`create_jwt`函数。

```
use jsonwebtoken::{decode, encode, Algorithm, DecodingKey, EncodingKey, Header, Validation};

pub fn create_jwt(uid: &str, role: &Role) -> Result<String> {
    let expiration = Utc::now()
        .checked_add_signed(chrono::Duration::seconds(60))
        .expect("valid timestamp")
        .timestamp();

    let claims = Claims {
        sub: uid.to_owned(),
        role: role.to_string(),
        exp: expiration as usize,
    };
    let header = Header::new(Algorithm::HS512);
    encode(&header, &claims, &EncodingKey::from_secret(JWT_SECRET))
        .map_err(|_| Error::JWTTokenCreationError)
}

```

首先，我们计算这个令牌的到期日期。在这种情况下，我们只将其设置为未来 60 秒。这对测试来说很好，因为我们不必等待令牌过期很久。

可以使用不同的策略来定义到期集，但是由于这些令牌是安全关键的，并且包含敏感的信息，因此它们肯定会在某个时候到期。一些系统依赖于刷新令牌机制，设置短的(几分钟/几小时)过期时间，并向调用者提供刷新令牌，如果旧令牌过期，可以使用该刷新令牌来获得新令牌。

接下来，我们用用户的 ID、用户的角色和到期日期创建`Claims`结构。之后是我们与`jsonwebtoken`箱子的第一次互动。

如果你以前和 jwt 打过交道，你会知道它们由三部分组成:

1.  页眉
2.  有效载荷
3.  签名

这反映在这里，因为我们创建了一个新的头部和编码这个头部，加上我们的有效载荷(索赔)与上述秘密。如果失败，我们将返回一个错误。否则，我们返回结果 JWT。

现在用户可以登录我们的服务，但是我们还没有处理授权的机制。我们接下来会看到这一点。

## 批准

我们待在`auth.rs`模块内。因为我们使用 warp，所以向我们的处理程序添加额外功能(比如中间件)的最佳方式是使用过滤器。

所以我们定义了一个`with_auth`过滤器。

```
use warp::{
    filters::header::headers_cloned,
    http::header::{HeaderMap, HeaderValue, AUTHORIZATION},
    reject, Filter, Rejection,
};

pub fn with_auth(role: Role) -> impl Filter<Extract = (String,), Error = Rejection> + Clone {
    headers_cloned()
        .map(move |headers: HeaderMap<HeaderValue>| (role.clone(), headers))
        .and_then(authorize)
}

```

例如，可以使用`.and(with_auth(Role::Admin)`将这个过滤器添加到端点，这意味着这个处理程序只能由具有`Admin`角色的用户访问。

因为在现实世界的系统中，我们很可能在这一步中连接到数据库、缓存或其他外部系统，所以我决定创建一个`async`过滤器。在这种情况下，这并不是严格必需的，但是在用户存储不是静态的内存映射的任何情况下，它都会派上用场。

我们需要采取几个步骤来授权用户:

*   获取`Authorization`头；如果它不存在，就失败
*   验证标头，确保它具有有效的格式(`Bearer $JWT`)；如果不是这样，就失败
*   从标题中提取 JWT 字符串；如果这不起作用，就失败
*   解码 JWT；如果无效或过期，则失败
*   检查保存在 JWT 中的角色，并与给定的`role`进行比较；例如，如果 JWT 角色是`User`，但是端点需要`Admin`，则失败
*   从 JWT 中提取`uid`,将其传递给修饰的处理程序

那可是好几步啊！我们需要小心处理错误，因为这里的任何错误都会导致严重的漏洞。

在上面的`with_auth`函数中，我们使用`headers_cloned()` warp 过滤器来获取存储在 map 中的请求头的副本。然后我们将它与`role`捆绑在一起，并将其传递给`authorize`函数，这是授权功能的核心。

```
async fn authorize((role, headers): (Role, HeaderMap<HeaderValue>)) -> WebResult<String> {
    match jwt_from_header(&headers) {
        Ok(jwt) => {
            ...
        }
        Err(e) => return Err(reject::custom(e)),
    }
}

```

由于这是一个`async`函数，我们需要在过滤器中使用`and_then`。正如我在上面提到的，在这个例子中这是不必要的，但是在一个真实的例子中，你也可以在这里传递一个外部系统的句柄，你可能需要它来进行授权。例如，用于将会话令牌映射到内部令牌或获取一些所需元数据的缓存或数据库。

在这个例子中，我们首先使用头文件映射调用`jwt_from_header`函数，从`Authorization`头文件中获取 JWT。

```
fn jwt_from_header(headers: &HeaderMap<HeaderValue>) -> Result<String> {
    let header = match headers.get(AUTHORIZATION) {
        Some(v) => v,
        None => return Err(Error::NoAuthHeaderError),
    };
    let auth_header = match std::str::from_utf8(header.as_bytes()) {
        Ok(v) => v,
        Err(_) => return Err(Error::NoAuthHeaderError),
    };
    if !auth_header.starts_with(BEARER) {
        return Err(Error::InvalidAuthHeaderError);
    }
    Ok(auth_header.trim_start_matches(BEARER).to_owned())
}

```

这个函数执行前两步，检查`Authorization`头是否存在，是否有效，是否包含`Bearer`前缀，并提取 JWT。如果一切顺利，它将这个字符串返回给调用者。

回到`authorize`函数，下一步是`decode`JWT 以获得有效的`Claims`结构。

```
async fn authorize((role, headers): (Role, HeaderMap<HeaderValue>)) -> WebResult<String> {
    match jwt_from_header(&headers) {
        Ok(jwt) => {
            let decoded = decode::<Claims>(
                &jwt,
                &DecodingKey::from_secret(JWT_SECRET),
                &Validation::new(Algorithm::HS512),
            )
            .map_err(|_| reject::custom(Error::JWTTokenError))?;

            if role == Role::Admin && Role::from_str(&decoded.claims.role) != Role::Admin {
                return Err(reject::custom(Error::NoPermissionError));
            }

            Ok(decoded.claims.sub)
        }
        Err(e) => return Err(reject::custom(e)),
    }
}

```

如果 JWT 过期、格式错误或以任何方式无效，此解码步骤将失败，我们将在此停止。`jsonwebtoken`库甚至为验证步骤提供了一些定制选项，这在[官方文档](https://docs.rs/jsonwebtoken/7.2.0/jsonwebtoken/struct.Validation.html)中有很好的描述。

如果验证成功，我们可以检查用户角色。如果我们在一个`Admin`端点，JWT 角色也需要是`Admin`。如果不是，我们扔一个`NoPermissionError`。

因为我们只有这两个角色，所以这个检查相当容易，但是如果有几个角色，就会变得相当复杂。以安全和可维护的方式处理这种访问控制的一个有用的库是 [casbin](https://github.com/casbin/casbin-rs) ，它也有一个维护良好的 Rust crate。

一旦用户通过了角色检查，我们就在修饰的处理程序中传递用户的 ID。这很有用，因为用户的身份将与许多个性化端点相关，例如获取用户配置文件或个人数据。

这就完成了`with_auth`过滤器，我们只需要在`main`中为我们的处理程序使用它。

```
async fn main() {
    ...
    let user_route = warp::path!("user")
        .and(with_auth(Role::User))
        .and_then(user_handler);
    let admin_route = warp::path!("admin")
        .and(with_auth(Role::Admin))
        .and_then(admin_handler);
    ...
}

pub async fn user_handler(uid: String) -> WebResult<impl Reply> {
    Ok(format!("Hello User {}", uid))
}

pub async fn admin_handler(uid: String) -> WebResult<impl Reply> {
    Ok(format!("Hello Admin {}", uid))
}

```

那很容易！只需用过滤器修饰现有的处理程序，并将传入的用户 ID 放入处理程序签名中。我们还打印了这个用户 ID，以便以后测试。

## 错误处理

就安全性而言，良好的错误处理至关重要。您不希望有一个向外部泄露太多信息的无所不包的处理程序。错误应该对调用者有所帮助，而不会泄露系统内部工作的任何信息。

在`error.rs`模块中，我们首先定义一个自定义的`Error`类型，一个`ErrorResponse`类型，并实现 warp 的`Reject`特征，这样这些错误就可以用于从处理程序返回。

```
#[derive(Error, Debug)]
pub enum Error {
    #[error("wrong credentials")]
    WrongCredentialsError,
    #[error("jwt token not valid")]
    JWTTokenError,
    #[error("jwt token creation error")]
    JWTTokenCreationError,
    #[error("no auth header")]
    NoAuthHeaderError,
    #[error("invalid auth header")]
    InvalidAuthHeaderError,
    #[error("no permission")]
    NoPermissionError,
}

#[derive(Serialize, Debug)]
struct ErrorResponse {
    message: String,
    status: String,
}

impl warp::reject::Reject for Error {}

```

最后，我们添加了最初在`main`中使用的`handle_rejection`函数。

```
pub async fn handle_rejection(err: Rejection) -> std::result::Result<impl Reply, Infallible> {
    let (code, message) = if err.is_not_found() {
        (StatusCode::NOT_FOUND, "Not Found".to_string())
    } else if let Some(e) = err.find::<Error>() {
        match e {
            Error::WrongCredentialsError => (StatusCode::FORBIDDEN, e.to_string()),
            Error::NoPermissionError => (StatusCode::UNAUTHORIZED, e.to_string()),
            Error::JWTTokenError => (StatusCode::UNAUTHORIZED, e.to_string()),
            Error::JWTTokenCreationError => (
                StatusCode::INTERNAL_SERVER_ERROR,
                "Internal Server Error".to_string(),
            ),
            _ => (StatusCode::BAD_REQUEST, e.to_string()),
        }
    } else if err.find::<warp::reject::MethodNotAllowed>().is_some() {
        (
            StatusCode::METHOD_NOT_ALLOWED,
            "Method Not Allowed".to_string(),
        )
    } else {
        eprintln!("unhandled error: {:?}", err);
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            "Internal Server Error".to_string(),
        )
    };

    let json = warp::reply::json(&ErrorResponse {
        status: code.to_string(),
        message,
    });

    Ok(warp::reply::with_status(json, code))
}

```

大部分都是样板文件，用于处理 warp 中的拒绝，并在最后将它们转换成 JSON 响应。

有趣的部分是当我们处理我们的自定义类型时。在这种情况下，我们映射可能发生在状态代码上的错误。由于我们将错误的`Display`实现定义为只包含一个有用的错误消息，我们可以简单地将错误字符串化。

如果您将内部上下文添加到您的错误中，您应该非常小心，并且总是定义新的、轻量级的和有限的错误，以便向外部公开与安全相关的错误。您绝不会想要泄露任何关于内部工作的信息，比如堆栈跟踪。

在真实的系统中，定义一个额外的`SecurityError`类型也是有意义的，它被精心设计为不包含任何有意义的信息，并且完美地映射到每一个可能的与 auth 相关的情况。

## 测试

既然身份验证和授权机制都实现了，最后一步就是看它是否工作。

我们可以使用`cargo run`启动服务器，这将在端口 8000 上本地启动一个 web 服务器。

然后，我们可以作为`User`登录并尝试访问两个端点:

```
curl http://localhost:8000/login -d '{"email": "[email protected]", "pw": "1234"}' -H 'Content-Type: application/json'

{"token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIxIiwicm9sZSI6IlVzZXIiLCJleHAiOjE2MDMxMzQwODl9.dWnt5vfcGdwypEQUr3bLMrZYfdyxj3v6-io6VREWHXebMUCKBddf9xGcz4vHrCXruzx42zrS3Kygiqw3xV8W-A"}

curl http://localhost:8000/user -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIxIiwicm9sZSI6IlVzZXIiLCJleHAiOjE2MDMxMzQwODl9.dWnt5vfcGdwypEQUr3bLMrZYfdyxj3v6-io6VREWHXebMUCKBddf9xGcz4vHrCXruzx42zrS3Kygiqw3xV8W-A' -H 'Content-Type: application/json'

Hello User 1

curl http://localhost:8000/admin -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIxIiwicm9sZSI6IlVzZXIiLCJleHAiOjE2MDMxMzQwODl9.dWnt5vfcGdwypEQUr3bLMrZYfdyxj3v6-io6VREWHXebMUCKBddf9xGcz4vHrCXruzx42zrS3Kygiqw3xV8W-A' -H 'Content-Type: application/json'

{"message":"no permission","status":"401 Unauthorized"}

```

到目前为止，一切顺利。登录成功并返回了有效的 JWT。我们使用这个 JWT 向`/user`和`/admin`发出认证请求。第一个如预期的那样工作了，第二个返回了一个错误。

接下来让我们试试管理员:

```
curl http://localhost:8000/login -d '{"email": "[email protected]", "pw": "4321"}' -H 'Content-Type: application/json'

{"token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIyIiwicm9sZSI6IkFkbWluIiwiZXhwIjoxNjAzMTM0MjA1fQ.uYglVKRvb3h0bDC0Uz8FwGTu4v__Rl3toVI9fMI4_IT8keKde_SZRFQ4ii_PKzI4wjmDsZlnpULe6Tg0vWfEnw"}

curl http://localhost:8000/admin -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIyIiwicm9sZSI6IkFkbWluIiwiZXhwIjoxNjAzMTM0MjA1fQ.uYglVKRvb3h0bDC0Uz8FwGTu4v__Rl3toVI9fMI4_IT8keKde_SZRFQ4ii_PKzI4wjmDsZlnpULe6Tg0vWfEnw' -H 'Content-Type: application/json'

Hello Admin 2

curl http://localhost:8000/user -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIyIiwicm9sZSI6IkFkbWluIiwiZXhwIjoxNjAzMTM0MjA1fQ.uYglVKRvb3h0bDC0Uz8FwGTu4v__Rl3toVI9fMI4_IT8keKde_SZRFQ4ii_PKzI4wjmDsZlnpULe6Tg0vWfEnw' -H 'Content-Type: application/json'

Hello User 2

```

太好了！管理员可以访问两个端点，并且我们记录了正确的用户 ID。如果这是一个真实的系统，我们将为验证、成功和错误情况编写一套详尽的测试。

模糊与身份验证相关的端点也是增加实现健壮性的好方法。没有什么比发送数十亿个随机值到某个东西中更能确保没有奇怪的边缘情况了！

你可以在 [GitHub](https://github.com/zupzup/rust-jwt-example) 上找到完整的示例代码。

## 结论

在本教程中，我们使用 JSON Web 令牌实现了一个基本的身份验证和授权模型。

在 Rust 生态系统中，板条箱是一个成熟且广泛使用的选项。虽然我们在这个例子中使用了 warp，但是这里使用的思想和技术可以很好地移植到任何其他 Rust web 框架中。

jwt 是处理授权和安全有效地分发信息的强大工具，Rust 社区再次证明了它的能力——这是它在 web 服务领域日益成熟的一个很好的标志。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。