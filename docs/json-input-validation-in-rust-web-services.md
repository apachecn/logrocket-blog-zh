# Rust web 服务中 JSON 输入验证的简单指南

> 原文：<https://blog.logrocket.com/json-input-validation-in-rust-web-services/>

当使用稍微复杂的域对象构建 web 服务时，良好的输入验证至关重要。用户输入的验证不仅在安全性方面非常重要——永远不要相信外部输入——而且在可用性方面也非常重要。

如果 REST 端点的调用者遇到错误，您希望能够告诉他们哪里出错了，而不是简单地显示`400 Bad Request`。最佳的输入错误处理是以这样一种方式完成的，如果您得到无效的 JSON，您可以告诉用户错误大致位于请求有效负载中的什么位置。

如果请求负载是有效的 JSON，下一步就是确保它符合您的规范。Rust 和奇妙的 [serde](https://blog.logrocket.com/json-and-rust-why-serde_json-is-the-top-choice/) 机箱在这里很有帮助，因为如果使用了错误的数据类型，对结构的反序列化将会失败。同样，如果发生这种情况，你需要告诉用户，例如，`name`需要是一个`String`而不是一个`Object`，而不是简单地回复`JSON Parse Error`。

但是我们还没有完成。一旦传入的 JSON 被正确地验证并解析为一个结构，我们自己的验证就开始了——基于我们的业务逻辑的验证。例如，您可能想要验证一个`email`实际上是一个符合规范的电子邮件，或者一个`username`不包含禁止的字符，有一定的长度，等等。这一部分比其他部分更容易处理，但是它经常导致许多小的、容易出错的验证函数，这些函数很难组合并提炼出清晰的错误消息。

在本教程中，我们将解释如何在一个 [warp](https://github.com/seanmonstar/warp) web 服务中解决 Rust 中的这些问题。我们的解决方案将是直观和用户友好的，而不会损害代码的可维护性。

我们开始吧！

## 设置

接下来，您需要的只是一个相当新的 Rust 安装(1.39+)和一个发出 HTTP 请求的工具，比如 cURL。

首先，创建一个新的 Rust 项目。

```
cargo new rust-json-validation-example
cd rust-json-validation-example

```

接下来，编辑`Cargo.toml`文件并添加您需要的依赖项。

```
tokio = { version = "0.2", features = ["macros", "sync", "rt-threaded"] }
warp = "0.2"
serde = {version = "1.0", features = ["derive"] }
serde_json = "1.0"
validator = "0.10"
validator_derive = "0.10"
serde_path_to_error = "0.1"
thiserror = "1.0.20"
bytes = "0.5.6"

```

您将需要 warp 和用于 web 服务器的 [tokio](https://github.com/tokio-rs/tokio) 以及用于反序列化传入主体的 [serde](https://github.com/serde-rs/serde) 。`serde_path_to_error`库将是你改进验证的第一站，而`validator`和`validator_derive`箱将有助于稍后的数据验证。

## JSON 主体验证

为了演示在 warp 中发送无效 JSON 或无法成功反序列化的内容时的默认行为，让我们创建一个具有单一路由的小型 web 服务器。

```
type Result<T> = std::result::Result<T, Rejection>;

#[derive(Deserialize, Debug)]
struct CreateRequest {
    pub email: String,
    pub address: Address,
    pub pets: Vec<Pet>,
}

#[derive(Deserialize, Debug)]
struct Address {
    pub street: String,
    pub street_no: usize,
}

#[derive(Deserialize, Serialize, Debug)]
struct Pet {
    pub name: String,
}

#[tokio::main]
async fn main() {
    let basic = warp::path!("create-basic")
        .and(warp::post())
        .and(warp::body::json())
        .and_then(create_handler);

    let routes = basic
        .recover(handle_rejection);

    println!("Server started at localhost:8080!");
    warp::serve(routes).run(([127, 0, 0, 1], 8080)).await;
}

async fn create_handler(body: CreateRequest) -> Result<impl Reply> {
    Ok(format!("called with: {:?}", body))
}

#[derive(Serialize)]
struct ErrorResponse {
    message: String,
    errors: Option<Vec<FieldError>>,
}

#[derive(Serialize)]
struct FieldError {
    field: String,
    field_errors: Vec<String>,
}

pub async fn handle_rejection(err: Rejection) -> std::result::Result<impl Reply, Infallible> {
    let (code, message, errors) = if err.is_not_found() {
        (StatusCode::NOT_FOUND, "Not Found".to_string(), None)
    } else if let Some(e) = err.find::<warp::filters::body::BodyDeserializeError>() {
        (
            StatusCode::BAD_REQUEST,
            e.source()
                .map(|cause| cause.to_string())
                .unwrap_or_else(|| "BAD_REQUEST".to_string()),
            None,
        )
    } else {
        eprintln!("unhandled error: {:?}", err);
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            "Internal Server Error".to_string(),
            None,
        )
    };

    let json = warp::reply::json(&ErrorResponse {
        message: message.into(),
        errors,
    });
    Ok(warp::reply::with_status(json, code))
}

```

这样，我们就创建了我们的请求对象——在本例中，我们可以对一些字段进行有趣的验证，比如`email`、一个地址和一个`Pets`列表。

然后，我们创建了一个最小的处理程序，它将这个作为 JSON 主体，并在被调用时打印出来。

接下来，我们在`handle_rejection`助手中定义了一些基本的错误处理。这是 warp 处理错误的方式，在 warp 的术语中，这被称为`Rejections`。你现在可以忽略`FieldError`；我们将在稍后进行数据验证时再次讨论它。

还有一种预定义的在主体反序列化期间发生错误的情况:`warp::filters::body::BodyDeserializeError`。如果我们遇到这样的错误，我们将返回 400 错误，并将错误原因转换为字符串。

从呼叫者的角度来看，情况是这样的。让我们用 cURL 发送一个错误的有效载荷:

```
curl -X POST http://localhost:8080/create-basic -H "Content-Type: application/json" -d '{ "email": 1, "address": { "street": "warpstreet", "street_no": 1 }, "pets": [{ "name": "nacho" }] }'

```

我们得到下面的错误。

```
{"message":"invalid type: integer `1`, expected a string at line 1 column 13","errors":null}

```

这还不算太糟——至少，它告诉我们错误所在的行和列(email 是我们错误的有效负载中的一个数字)——但是让错误真正告诉您`email`是问题所在会好得多。

无论如何，这比没有`BodyDeserializeError`处理程序要好得多。在这种情况下，答案应该是:

```
{"message":"Internal Server Error","errors":null}

```

## 更好的 JSON 验证

为了改善错误，我们将使用`serde_path_to_error` crate，顾名思义，它将 serde 错误转换为 JSON 中发生错误的路径。

让我们为它创建另一个处理程序，看看有什么变化。

```
#[tokio::main]
async fn main() {
...
    let basic_path = warp::path!("create-path")
        .and(warp::post())
        .and(warp::body::aggregate())
        .and_then(create_handler_path);

    let routes = basic
        .or(basic_path)
        .recover(handle_rejection);
...
}

async fn create_handler_path(buf: impl Buf) -> Result<impl Reply> {
    let des = &mut serde_json::Deserializer::from_reader(buf.reader());
    let body: CreateRequest = serde_path_to_error::deserialize(des)
        .map_err(|e| reject::custom(Error::JSONPathError(e.to_string())))?;
    Ok(format!("called with: {:?}", body))
}

#[derive(Error, Debug)]
enum Error {
    #[error("JSON path error: {0}")]
    JSONPathError(String),
}

impl warp::reject::Reject for Error {}

pub async fn handle_rejection(err: Rejection) -> std::result::Result<impl Reply, Infallible> {
...
    } else if let Some(e) = err.find::<Error>() {
        match e {
            Error::JSONPathError(_) => (StatusCode::BAD_REQUEST, e.to_string(), None),
        }
...
}

```

如您所见，我们在路径`create-path`处添加了另一个处理程序。这里最大的不同是我们使用了`warp::body::aggregate()`而不是`warp::body::json()`，这将在我们有机会干预之前自动反序列化负载和错误。

在这种情况下，我们希望使用有效负载的聚合字节缓冲区。在实践中，如果您要重复使用这个模式，您可能会编写自己的自定义`json()`过滤器。

在处理程序中，我们创建了一个反序列化器，并使用`serde_path_to_error::deserialize`函数将传入的 JSON 缓冲区反序列化为一个`CreateRequest`结构。如果失败，它会触发一个`JSONPathError`——一个实现 warp 的`Reject`特征的自定义错误类型，该特征在它下面定义。这是在 warp 中执行自定义错误处理的简单方法。

让我们看看现在的反应是什么样的。我们会发送和以前一样的有效载荷。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
{"message":"JSON path error: email: invalid type: integer `1`, expected a string at line 1 column 13","errors":null}

```

如果结构中有更深层次的错误，比如宠物的名字，该怎么办？

```
curl -X POST http://localhost:8080/create-path -H "Content-Type: application/json" -d '{ "email": "[email protected]", "address": { "street": "warpstreet", "street_no": 1 }, "pets": [{ "name": 1 }] }'

```

这将产生以下错误。

```
{"message":"JSON path error: pets[0].name: invalid type: integer `1`, expected a string at line 1 column 107","errors":null}

```

那好多了。现在，错误告诉调用者哪个字段有问题，甚至提供了如何修复它的提示。在处理反序列化错误方面，我不认为我们能做得更好。

另一种方法是将响应放入一个更容易解析的数据结构中——例如，GUI 可以从中创建一个清晰的错误消息。但是因为我们讨论的是直接的无效请求，所以在这种情况下这并不重要。当我们进入数据验证阶段时，它变得更加相关。

## 数据有效性

在这种情况下，数据验证仅仅意味着确保提供的数据符合我们对值应该是什么样子的规范——例如，`email`或`url`是有效的，或者我们只想接受`https://`URL。

要实现这一点，您可以简单地为每个字段和每个传入的结构编写一个验证函数，但这很繁琐。也很难在一个更大的应用程序中结合许多不同的、复杂的领域对象，导致代码，尤其是容易出错的逻辑重复。

在 Rust 生态系统中的其他库当中， [validator](https://github.com/Keats/validator) crate 在这里非常有用。这个基于宏的库使您能够以声明方式定义结构和字段的验证规则。这种方法听起来可能很熟悉，因为在其他语言中有很多这样的库，比如 marshmallow、Java Spring 等。

让我们看看它在实践中是如何工作的:

```
use validator::{Validate, ValidationErrors, ValidationErrorsKind};

#[derive(Deserialize, Debug, Validate)]
struct CreateRequest {
    #[validate(email)]
    pub email: String,
    #[validate]
    pub address: Address,
    #[validate]
    pub pets: Vec<Pet>,
}

#[derive(Deserialize, Debug, Validate)]
struct Address {
    #[validate(length(min = 2, max = 10))]
    pub street: String,
    #[validate(range(min = 1))]
    pub street_no: usize,
}

#[derive(Deserialize, Serialize, Debug, Validate)]
struct Pet {
    #[validate(length(min = 3, max = 20))]
    pub name: String,
}

#[tokio::main]
async fn main() {
...
    let basic_path_validator = warp::path!("create-validator")
        .and(warp::post())
        .and(warp::body::aggregate())
        .and_then(create_handler_validator);

    let routes = basic
        .or(basic_path)
        .or(basic_path_validator)
        .recover(handle_rejection);
...
}

async fn create_handler_validator(buf: impl Buf) -> Result<impl Reply> {
    let des = &mut serde_json::Deserializer::from_reader(buf.reader());
    let body: CreateRequest = serde_path_to_error::deserialize(des)
        .map_err(|e| reject::custom(Error::JSONPathError(e.to_string())))?;

    body.validate()
        .map_err(|e| reject::custom(Error::ValidationError(e)))?;
    Ok(format!("called with: {:?}", body))
}

#[derive(Error, Debug)]
enum Error {
    #[error("JSON path error: {0}")]
    JSONPathError(String),
    #[error("validation error: {0}")]
    ValidationError(ValidationErrors),
}

...

```

我们添加了另一个处理程序`create-validator`，它使用了我们用于`create-path`的相同方法。所以我们建立了良好的 JSON 输入处理，然后非常简单地调用`body.validate()`来验证传入的数据，传播带有任何错误的新的自定义错误。

然而，真正的问题在于我们的数据结构的定义。在那里，我们为我们的结构派生了`Validate`特征，并且对于每个字段，使用`validate`宏来定义关于是否以及如何验证该字段的规则。这可以是诸如电子邮件验证之类的花哨东西，也可以是诸如字符串长度之类的基本东西，甚至可以是定制的验证函数。查看[文档](https://github.com/Keats/validator)查看更多示例；功能相当强大，可定制。

在上面的例子中，省略了对`ValidationError`的错误处理，因为它有点复杂。我们从`validator`箱子中得到的`ValidationErrors`对象包含了我们想要的所有信息，甚至可以被字符串化以给出一个有用的错误。但是在我们的例子中，我们想把它解析成我们定制的`FieldError`结构，在那里我们可以把错误映射到发生错误的字段。

让我们来看看实现这一点的基本方法。

```
pub async fn handle_rejection(err: Rejection) -> std::result::Result<impl Reply, Infallible> {
...
    } else if let Some(e) = err.find::<Error>() {
        match e {
            Error::JSONPathError(_) => (StatusCode::BAD_REQUEST, e.to_string(), None),
            Error::ValidationError(val_errs) => {
                let errors: Vec<FieldError> = val_errs
                    .errors()
                    .iter()
                    .map(|error_kind| FieldError {
                        field: error_kind.0.to_string(),
                        field_errors: match error_kind.1 {
                            ValidationErrorsKind::Struct(struct_err) => {
                                validation_errs_to_str_vec(struct_err)
                            }
                            ValidationErrorsKind::Field(field_errs) => field_errs
                                .iter()
                                .map(|fe| format!("{}: {:?}", fe.code, fe.params))
                                .collect(),
                            ValidationErrorsKind::List(vec_errs) => vec_errs
                                .iter()
                                .map(|ve| {
                                    format!(
                                        "{}: {:?}",
                                        ve.0,
                                        validation_errs_to_str_vec(ve.1).join(" | "),
                                    )
                                })
                                .collect(),
                        },
                    })
                    .collect();

                (
                    StatusCode::BAD_REQUEST,
                    "field errors".to_string(),
                    Some(errors),
                )
            }
        }
...
}

fn validation_errs_to_str_vec(ve: &ValidationErrors) -> Vec<String> {
    ve.field_errors()
        .iter()
        .map(|fe| {
            format!(
                "{}: errors: {}",
                fe.0,
                fe.1.iter()
                    .map(|ve| format!("{}: {:?}", ve.code, ve.params))
                    .collect::<Vec<String>>()
                    .join(", ")
            )
        })
        .collect()
}

```

如您所见，这比您预期的要复杂一些。理论上，对于任意深度嵌套的结构，您也可以任意深度嵌套结果字段错误结构。我们需要做一个决定，定义分界点是`1`级。在深度的`1`级别之外，我们简单地将底层字段错误的整个字符串添加到顶层字段错误中。

实际上，这可以用递归很好地解决，但是我们在这里保持简单。

让我们检查一下代码，看看这里发生了什么。如上所述，我们从库中获得一个`ValidationErrors`对象，我们希望将它解析成一个`Vec<FieldError`。到目前为止，一切顺利。

如果我们使用`errors()`迭代器迭代`ValidationErrors`，我们会得到一个`ValidationErrorKinds`的集合。有三种错误类型:

1.  **字段**表示一个字段中发生的错误，如`email` —简单
2.  **Struct** 表示错误发生在嵌套结构中
3.  **列表**表示错误发生在嵌套列表中

对于每种错误，我们得到一个字段名和实际错误的元组。我们可以简单地将字段名放入`FieldError`上的`field`属性中。既然我们得到了一个`&&str`并且想要一个`String`，我们需要调用`to_string()`。

现场误差有点棘手。我们需要匹配上面提到的三个案例。如果我们遇到一个`ValidationErrorsKind::Field`，这很简单，我们只需将错误格式化成一个字符串。

如果我们遇到一个`ValidationErrorsKind::Struct`，我们调用`validation_errs_to_str_vec`助手，它遍历这个结构中的所有错误，将它们格式化为字符串，并用一个`,`将它们连接起来，返回这个结构中这些连接起来的错误的列表。

对于最后一种情况，`ValidationErrorsKind::List`，我们将两者结合起来，因为列表中的每一项本身也可能是一个结构体。我们为每个字段调用`validation_errs_to_str_vec`，在字符串中用发生错误的列表索引标记它。

如果你在最后一点上有困难，不要太担心。实际上，返回的验证错误会根据具体情况以不同的方式进行解析和传递。这只是一种简化的，不可否认的，不完美的方法。

也就是说，让我们看看如果我们违反了一些数据验证规则，我们会得到什么样的错误。

首先，让我们看看无效邮件会发生什么。

```
curl -X POST http://localhost:8080/create-validator -H "Content-Type: application/json" -d '{ "email": "chipexample.com", "address": { "street": "warpstreet", "street_no": 1 }, "pets": [{ "name": "nacho" }] }'

```

=>

```
{"message":"field errors","errors":[{"field":"email","field_errors":["email: {\"value\": String(\"chipexample.com\")}"]}]}

```

我们得到一个错误数组，字段设置正确，在字段中，字段错误还包含显示错误值的无效电子邮件错误。

现在，让我们添加另一个错误，并使用一个街道名称，这对于我们的规则来说太长了。

```
curl -X POST http://localhost:8080/create-validator -H "Content-Type: application/json" -d '{ "email": "chipexample.com", "address": { "street": "warpstreetistoolonghere", "street_no": 1 }, "pets": [{ "name": "nacho" }] }'

```

=>

```
{"message":"field errors","errors":[{"field":"address","field_errors":["street: errors: length: {\"max\": Number(10), \"min\": Number(2), \"value\": String(\"warpstreetistoolonghere\")}"]},{"field":"email","field_errors":["email: {\"value\": String(\"chipexample.com\")}"]}]}%

```

第二个错误被正确添加，我们得到了一些关于长度的信息，这是这里所期望的。正如您在格式化的内部错误字符串中看到的，我们可以更进一步，创建一个漂亮的结构化错误，为用户字段提供`max`和`min`。

最后，我们来试试嵌套的情况，发一个太短的宠物名。

```
curl -X POST http://localhost:8080/create-validator -H "Content-Type: application/json" -d '{ "email": "[email protected]", "address": { "street": "warpstreet", "street_no": 1 }, "pets": [{ "name": "" }, {"name": "a"}] }'

```

=>

```
{"message":"field errors","errors":[{"field":"pets","field_errors":["0: \"name: errors: length: {\\\"value\\\": String(\\\"\\\"), \\\"min\\\": Number(3), \\\"max\\\": Number(20)}\"","1: \"name: errors: length: {\\\"value\\\": String(\\\"a\\\"), \\\"max\\\": Number(20), \\\"min\\\": Number(3)}\""]}]}

```

因为这些是列表中的错误，并且因为我们以非常简单的方式实现了这一点，所以这些错误只是连接在`field_errors`数组中，我们只是获得了索引`0`和`1`的错误字符串，显示名称太短。

你可以在 [GitHub](https://github.com/zupzup/example-rust-json-input-validation) 上找到本教程的完整代码。

## 结论

JSON 输入验证是任何现代 web 应用程序的核心问题，Rust 生态系统已经有了一些很好的工具来处理它。

在本例中，我们研究了如何处理 JSON 反序列化错误，并在下一步中验证数据。有很多方法可以做到这一点——我们刚刚讨论了展示生态系统中两个特定库的无数方法中的一种——但我希望它能帮助您了解如何解决 Rust 中的类似问题。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。