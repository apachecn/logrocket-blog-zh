# JSON 和 Rust:为什么 serde_json 是首选

> 原文：<https://blog.logrocket.com/json-and-rust-why-serde_json-is-the-top-choice/>

JSON 已经成为 web 上最常见的数据交换格式之一，所以服务器端语言对它的良好支持是至关重要的。幸运的是，处理 JSON 是一个容易生锈的领域，这在很大程度上要感谢 [serde](https://crates.io/crates/serde) 和 [serde_json](https://crates.io/crates/serde_json) 机箱。这些都是 Rust 生态系统中久经考验的箱子，是利用 Rust 的高级抽象同时保持低级控制的好例子。

虽然有很多其他 JSON 板条箱可用，但 serde_json 是目前最受欢迎的。围绕 serde 构建的大型生态系统使其成为用 Rust 编写的 web 服务器的首选。

在本教程中，我们将探索 serde_json，并演示如何使用 Rust 的类型系统来表达 json 数据。

## 入门指南

要开始使用 serde_json，您必须首先在您的类型上实现 [`Serialize`](https://docs.serde.rs/serde/trait.Serialize.html) 和 [`Deserialize`](https://docs.serde.rs/serde/trait.Deserialize.html) 特征。由于派生宏，这对于大多数类型来说是微不足道的。要使用派生宏，请确保在依赖项中为 serde 启用了“派生”特性标志。

```
# cargo.toml

[dependencies]
serde = { version = "1", features = ["derive"] }
serde_json = "1"

```

现在我们可以像使用任何其他派生宏一样使用它们。

```
use serde::{Deserialize, Serialize};

#[derive(Debug, Deserialize, Serialize)]
struct Person {
    name: String,
    age: usize,
    verified: bool,
}

```

这就是用支持 serde 的 crate 将`Person`序列化和反序列化成任何数据格式所需要做的全部工作。调试是可选的，但是我们将使用它来进行演示。将 JSON 字符串转换成`Person`的实例现在就像调用`serde_json::from_str`一样简单。

```
fn main() {
    let json = r#"
        {
          "name": "George",
          "age": 27,
          "verified": false
        }
    "#;

    let person: Person = serde_json::from_str(json).unwrap();

    println!("{:?}", person);
}

```

这里有几件事情需要指出，首先是显式的`Person`类型注释。在这个例子中，编译器没有办法推断出`person`的类型；它可能是实现`Deserialize`的任何类型。在更完整的例子中，当传递`person`时，它可以从函数参数类型中推断出来。

另一件要注意的事情是对`unwrap()`的调用。反序列化可能以多种方式失败，所以`serde_json::from_str`返回一个`Result`来让我们处理这些失败。来自 serde_json 的错误非常丰富，给了我们足够的信息来确定到底哪里出错了。例如，在删除了`age`字段的情况下运行与上面相同的代码会触发下面的错误消息。

```
Error("missing field `age`", line: 5, column: 9)

```

如果 JSON 中有语法错误，您会得到类似的消息。不使用`unwrap`，你可以使用 [`serde_json::Error`](https://docs.rs/serde_json/1.0.57/serde_json/struct.Error.html) 提供的方法提取上面看到的相同信息，并尽可能优雅地处理错误。

我最喜欢在 Rust 中使用 JSON 的一点是，由于使用了`Result`类型，它提供了完整的类型检查，没有样板代码和编译时强制执行的错误处理。使用 JSON 的地方几乎总是在系统边界，在那里您可以接收各种各样的意外输入。拥有一流的、一致的错误处理支持使得处理这些系统边界变得更加愉快和可靠。

## 服务器示例

到目前为止，我们仅仅触及了 serde 和 serde_json 的皮毛。为了展示它们的作用，我们将创建一个服务器来计算各种形状的周长和面积。我们希望请求发送如下所示的 JSON:

```
{
  "calculation": "area",
  "shape": "circle",
  "radius": 4.5
}

```

在 Rust 中，这通常是一个好主意，我们将从考虑类型开始。JSON 中的`calculation`字段的值只是一个字符串。虽然我们可以使用 Rust `String`，但是我们需要执行一些不变量，这些不变量不能被`String`类型捕获。我们并不允许任何字符串值，我们只是想允许`perimeter`或`area`。一款`enum`很适合这样的产品。

```
#[derive(Debug, Deserialize, Serialize)]
enum Calculation {
    Perimeter,
    Area,
}

```

JSON 不包含枚举的概念，但这没关系，因为 serde 足够灵活，可以将这些数据类型融入到 JSON 等价物中。默认情况下，`Calculation`的变量将被转换成 JSON 字符串`Perimeter`和`Area`。这很好，但是我们更希望字符串都是小写的。为此，我们需要使用我们的第一个 [serde 属性宏](https://serde.rs/variant-attrs.html)。

```
#[derive(Debug, Deserialize, Serialize)]
#[serde(rename_all = "lowercase")]
enum Calculation {
    Perimeter,
    Area,
}

```

顾名思义，`rename_all = "lowercase"`会将枚举变量映射到 JSON 中的小写字符串。

我们想要的 JSON 格式中的下一个字段是形状名称和该形状的属性。这些场彼此紧密耦合。圆应该有半径，但矩形不应该。为了在我们的类型中加强这种耦合，我们可以使用带有关联数据的`enum`。

```
#[derive(Debug, Deserialize, Serialize)]
enum Shape {
    Circle {
        radius: f64,
    },
    Rectangle {
        length: f64,
        width: f64,
    },
}

```

默认情况下，这由外部标记的 JSON 对象表示，并添加了我们不想要的嵌套。

```
{
  "Circle": {
    "radius": 4.5
  }
}

```

我们可以用另一个属性来解决这个问题。

```
#[derive(Debug, Deserialize, Serialize)]
#[serde(rename_all = "lowercase", tag = "shape")]
enum Shape {
    Circle {
        radius: f64,
    },
    Rectangle {
        length: f64,
        width: f64,
    },
}

```

`tag = "shape"`属性导致 JSON 对象在内部用键`shape`标记，给出如下内容。

```
{
  "shape": "circle",
  "radius": 4.5
}

```

现在我们可以创建一个`Request`类型，将一个`Calculation`和一个`Shape`放在一起。

```
#[derive(Debug, Deserialize, Serialize)]
struct Request {
    calculation: Calculation,
    shape: Shape,
}

```

您可能已经注意到，这种类型增加了另一层嵌套，与我们想要的 JSON 格式不匹配。

```
{
  "calculation": "area",
  "shape": {
    "shape": "circle",
    "radius": 4.5
  }
}

```

同样，我们可以用一个属性来解决这个问题。

```
#[derive(Debug, Deserialize, Serialize)]
struct Request {
    calculation: Calculation,
    #[serde(flatten)]
    shape: Shape,
}

```

这一次，`flatten`属性用于删除一层嵌套。在 JSON 对象包含一些没有可预测名称的键的情况下，这也很有用。为了解决这个问题，您可以创建一个类型，其中已知字段直接映射到一个`struct`字段，未知字段可以收集在一个扁平的`HashMap`中。

你现在可以用我们之前测试`Person`类型的方法来测试我们到目前为止做了什么。

```
fn main() {
    let json = r#"
        {
          "calculation": "perimeter",
          "shape": "circle",
          "radius": 2.3
        }
    "#;

    let request: Request = serde_json::from_str(json).unwrap();

    println!("{:?}", request);
}

```

我鼓励您尝试输入 JSON，看看类型验证有多健壮，错误有多大帮助。

我们的应用程序需要的最后一个类型是一个`Response`，它包含计算的结果。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
#[derive(Debug, Deserialize, Serialize)]
struct Response {
    result: f64,
}

```

有了所有的类型，我们就可以构建实际执行计算的逻辑了。

```
use std::f64::consts::PI;

fn calculation_handler(request: Request) -> Response {
    let result = match (request.calculation, request.shape) {
        (Calculation::Perimeter, Shape::Circle { radius }) => PI * 2.0 * radius,
        (Calculation::Perimeter, Shape::Rectangle { length, width }) => 2.0 * length + 2.0 * width,
        (Calculation::Area, Shape::Circle { radius }) => PI * radius * radius,
        (Calculation::Area, Shape::Rectangle { length, width }) => length * width,
    };

    Response { result }
}

```

由于我们在类型系统中实施了不变量，我们的逻辑变得简单且易于推理。在这种情况下，它是一个纯粹的函数，接受一个`Request`并返回一个`Response`，使其可测试并与任何 web 框架完全解耦。serde 的一个优点是，它不强迫您将逻辑和类型耦合到它，因为派生宏不修改现有的东西；他们纯粹添加了 trait 实现。

正如我前面提到的，serde_json 可以利用 serde 周围的生态系统。许多箱子包含实现`Serialize`或`Deserialize`特征的类型的通用代码。web 框架就是一个很好的例子。我们的处理程序已经有了一个类似 HTTP 请求-响应循环的类型签名，所以我们将能够把它插入到任何与 serde 集成的 web 框架中。

为了构建 web 服务器，我们需要添加一些新的依赖项。

```
[dependencies]
serde = { version = "1", features = ["derive"] }
tokio = { version = "0.2", features = ["macros"] }
warp = "0.2"

```

接下来，用设置和启动服务器的`async`函数替换`main`函数。

```
use warp::Filter;

#[tokio::main]
async fn main() {
    let endpoint = warp::post()
        .and(warp::body::json())
        .map(|body| warp::reply::json(&calculation_handler(body)));
    warp::serve(endpoint).run(([127, 0, 0, 1], 5000)).await;
}

```

不要太担心这里发生的事情；重要的部分是`map`内部的封闭。为了让我们的逻辑与 warp web 服务器一起工作，我们需要做的就是将响应包装在`warp::reply::json`中。warp 可以通过在引擎盖下使用 serde_json 来做到这一点。

此时，我们可以尝试将带有 JSON 主体的 POST 请求发送到 localhost:5000，以测试包括错误消息在内的所有内容现在是否都可以在我们的 HTTP API 上工作。

现在，您应该已经很好地掌握了如何使用 Rust 类型来处理 JSON。从这个例子中得到的一个关键信息是，您可以使用与您交互的 JSON 没有精确结构的 Rust 类型。当使用比简单的键值对象更适合问题的 Rust 类型系统的高级特性时，我们可以自由地使用它们，而不用担心额外的样板文件。

这个例子的完整代码在 [GitHub](https://github.com/joshua-cooper/rust-json-example) 上。

## 不使用类型工作

尽管通常最好使用自己的类型，并通过 serde_json 派生出`Serialize`和`Deserialize`特征，但有时您要么不能，要么不想。对于这些情况，您可以直接使用 serde_json `Value`类型。这是一个枚举，包含 JSON 中每种可能的数据类型的变量。

```
// serde_json::Value

pub enum Value {
    Null,
    Bool(bool),
    Number(Number),
    String(String),
    Array(Vec<Value>),
    Object(Map<String, Value>),
}

```

创建`Value`的一个简单方法是使用`serde_json::json`宏。这实质上允许您直接在 Rust 源代码中编写 JSON。如果您有一个代表对象或数组的`Value`，您可以使用`Value::get`访问这些字段，类似于`Vec::get`和`HashMap::get`。

```
use serde_json::json;

fn main() {
    let value = json!({
        "name": "Bob",
        "age": 51,
        "address": {
            "country": "Germany",
            "city": "Example City",
            "street": "Example Street"
        },
        "siblings": ["Alice", "Joe"]
    });

    println!(
        "{:?}",
        value.get("address").and_then(|name| name.get("country"))
    );
}

```

这个例子将打印`Some(String("Germany"))`。如您所料，`get`返回一个`Option`，因为索引或键可能不存在。

这就是在没有自定义类型的情况下使用 serde_json 的全部内容。可以想象，这对于处理带有未知键的 JSON 和快速原型很有用。由于 web 框架之类的东西在`Serialize`和`Deserialize`特征上是通用的，而`serde_json::Value`实现了这些特征，所以你可以使用这些`Value`而不会有任何额外的摩擦。

## 结论

作为 Rust 最成熟的产品之一，serde 和 serde_json 使得使用 json 变得轻而易举。在本指南中，我们讨论了如何使用自己的数据类型来表示已知的 JSON 结构，以及如何处理未知的 JSON 结构。Serde 的数据模型非常灵活，所以你应该能够处理任何符合人体工程学的 JSON 数据，最大限度地减少样板文件的生锈。

简而言之，任何 Rust 开发者都不应该担心 JSON 支持。与其他主流语言相比，它非常适合生产，可以说是同类中最好的。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。