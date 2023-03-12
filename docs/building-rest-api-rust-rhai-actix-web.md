# 用 Rhai 和 Actix 在 Rust 中构建 REST API

> 原文：<https://blog.logrocket.com/building-rest-api-rust-rhai-actix-web/>

Rhai 是一种高级脚本语言，允许您用脚本编写应用程序的逻辑。这些脚本可以嵌入 Rust 程序并在其中执行。

由于 Rhai 脚本比 Rust 更容易理解，因此它们可以更容易地创建更复杂的程序。

在本文中，您将学习如何在 Rust 中嵌入 Rhai 脚本，并使用 Rhai 和 Actix Web 框架构建 RESTful APIs，以及以下内容:

要阅读本文，您应该熟悉 Rust、Rhai 和 RESTful APIs。

## 什么是 Rhai？

Rhai 或 RhaiScript 是一种小型、快速、可嵌入的脚本语言，可以集成到 Rust 程序中。 [Rust 程序可以在运行的同时执行 Rhai 脚本](https://docs.rs/rhai/latest/rhai/)，这让你可以用一个容易理解的脚本实现复杂的算法。

“Rhai”这个名字的灵感来自于 C++的脚本语言 Chai。就像 Rhai 可以嵌入到 Rust 中一样，Chai 也可以嵌入到 C++中，并允许您在一个易于理解的脚本中抽象复杂的算法。

ChaiScript 和 RhaiScript 使用类似 JavaScript 的语法。这些脚本语言提供了一种向各自的编程语言添加脚本的安全方式。

## 带锈使用 Rhai 的好处

Rhai 有很多好处。例如，Rhai 脚本比 Rust 的其他脚本语言更快[，包括像 JavaScript、Lua 和 Python 这样的流行选项。它们也是动态类型的，这提供了灵活性并降低了复杂性。](https://blog.logrocket.com/comparing-rust-scripting-language-game-development/)

此外，Rhai 是一种比 Rust 更高级的语言，这使得开发人员可以轻松地在 Rust 程序中实现复杂的算法。Rhai 脚本可以很容易地嵌入到 Rust 程序中，与程序的变量进行交互，并在沙箱环境中运行。

最后，Rhai 脚本是内存安全的，不会影响 Rust 程序。这是因为他们运行在一个受控的环境中，没有能力改变其沙箱之外的环境。

## 在 Rust 中运行 Rhai 脚本的指南

要在 Rust 程序中执行 Rhai 脚本，您需要遵循以下步骤。

首先，将 Rhai 库添加到您的`Cargo.toml`文件中:

```
[dependencies]
rhai = "1.6.1"

```

接下来，使用以下命令构建项目以下载库:

```
cargo build

```

您还需要设置 Rust 项目。您可以使用下面的命令创建一个空的:

```
cargo init rhai_tutorial --bin

```

在构建项目之后，您可以开始编写您的 Rhai 脚本。在接下来的步骤中，我们将建立一个“Hello world”程序，作为使用 Rhai 脚本的 Rust 程序的简单示例。

首先，将以下内容复制到您的`main.rs`文件中:

```
// import the scripting engine
use rhai::{ Engine };

fn main() {
    // Create the scripting engine
    let engine = Engine::new();

    // Run the script at "src/my_script.rhai"
    let result = engine.eval_file::<String>("src/my_script.rhai".into()).unwrap();

    // print the results of the script
    println!("{}", result);
}

```

上面的程序执行我们将在下一步中创建的脚本，然后打印出脚本的返回值。

接下来，在`src`文件夹中创建一个名为`my_script.rhai`的新文件，并将以下内容复制到其中。这个脚本返回“Hello，World！”回到铁锈。

```
// a simple function that appends "Hello, "
// to the beginning of the argument
fn hello(name) {
    return "Hello, " + name;
}

// Rhai return the value of the last expression
hello("World!");

```

要运行这个程序，在`src/main.rs`文件中编写 Rust 程序，在`src/my_script.rhai`文件中编写 Rhai 脚本。

使用上述步骤创建示例后，使用以下命令运行它:

```
$ cargo run
   Compiling rhai_tutorial v0.1.0 (..../rhai_tutorial)
    Finished dev [unoptimized + debuginfo] target(s) in 19.09s
     Running `target/debug/example`
Hello, World!

```

在 Rhai 脚本中，`hello();`函数获取一个字符串，将`"Hello, "`附加到字符串的开头，然后返回结果。

Rust 执行一个 Rhai 脚本后，它接收脚本最后一行的值。在本例中，Rust 收到“Hello，World！”并打印到终端上。

您还可以编写可以从 Rhai 脚本中调用的 Rust 函数。这对于创建需要访问一些 Rust 功能的 Rhai 脚本非常有用。让我们在下一个例子中看看如何实现这一点。

首先，将以下内容复制到您的`main.rs`文件中:

```
// import the scripting engine
use rhai::{Engine};

fn main() {
    // Create scripting engine
    let mut engine = Engine::new();

    // create a hello function using an anonymous function
>   let hello = |name: String| -> String {
>       let mut text = String::from("Hello, ");
>       text.push_str(&name);
>       return text;
>   };

    // register the function in the engine
>   engine.register_fn("hello", hello);

    // Run the script at "my_script.rhai"
    let result = engine.eval_file::<String>("src/my_script.rhai".into()).unwrap();

    // print the results of the script
    println!("{}", result);
}

```

接下来，将以下内容复制到您的`my_script.rhai`文件中:

```
// Rhai return the value of the last expression
hello("World!");

```

现在如果你运行这个程序，你应该会看到“你好，世界！”和以前一样。然而，我们没有在您的 Rhai 脚本中编写函数，而是通过编写一个从 Rhai 脚本调用的 Rust 函数来实现这一点。

## 为 Rust 设置 Actix Web 框架

[表述性状态转移(REST)API 旨在](https://blog.logrocket.com/10-best-practices-for-rest-api-design/)促进客户端和服务器之间的通信。由于客户机独立于服务器工作，它们使用 API 作为接口来发送和接收请求。

Actix Web 框架是构建 REST APIs 的常用框架。 [Rust web 框架提供了你需要的工具](https://blog.logrocket.com/current-state-rust-web-frameworks/)来在 Rust 中快速构建大型的、可伸缩的、高效的 REST APIs。

要设置这个框架，请遵循以下步骤。

首先，将下面的依赖项添加到项目的`Cargo.toml`文件中:

```
[dependencies]
actix-web = "4.0.1"

```

接下来，使用下面的命令构建项目以下载库。

```
cargo build

```

安装完这个库之后，就可以开始在 Rust 中创建 RESTful APIs 了。按照下面的例子，你应该在你的`main.rs`文件中工作。

## 使用 Actix Web 的简单 Rust REST API 示例

以下是使用 Actix Web 框架的 API 的简单示例:

```
// import the the necessary modules from the library
use actix_web::{
  HttpServer,
  App,
  get,
  Responder,
};

// create a "/" route that
// responds with "Hello, World"

#[get("/")]
async fn greet() -> impl Responder {
  "Hello, World!"
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
  // create the server
  HttpServer::new(|| {
    App::new()
      .service(greet)
  })
    .bind(("127.0.0.1", 8080))  // bind the server to localhost:8080
    .unwrap()                // unwrap the result of the bind trait
    .run()                    // run the server
    .await
}

```

当您运行上面的程序并向 [http://localhost:8080/](http://localhost:8080/) 发出请求时，它会以“Hello，World！”

## 带有动态路由的 Rust REST API 示例

下面是你可以使用这个框架创建的 API 的另一个例子。在本例中，程序创建了一个动态路由，它对 URL 最后一段中的参数值说“Hello ”:

```
use actix_web::{
  HttpServer,
  App,
  get,
  Responder,
  web,
};

// if you send a request to /users, you get "Hello, users".

#[get("/{name}")]
async fn greet(path: web::Path<(String, )>) -> impl Responder {
  // assign the first of the path's parameter to "name"
  let name = path.into_inner().0;

  // Append "Hello, " to the beginning of the string, 
  // and return it
  format!("Hello, {name}!")
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
  HttpServer::new(|| {
    App::new()
      .service(greet)
  })
    .bind(("127.0.0.1", 8080))   // bind the server to localhost:8080
    .unwrap()                // unwrap the result of the bind trait
    .run()                    // run the server
    .await
}

```

动态路由是具有一个或多个灵活 URL 段的路由。在 Rust 中，通过柔性段相对于其它柔性段的位置来参照柔性段。在 Actix Web 中，这个位置使用零索引:第一个出现在零处，第二个出现在一处。

URL 段是斜线之间的路径的一部分。例如，在 URL[http://example.com/path/to/resource](http://example.com/path/to/resource)中，段是“路径”、“到”和“资源”

假设您向这个示例 URL 发送了一个请求:[http://localhost:8080/humans](http://localhost:8080/humans)

作为我们刚刚创建的程序的结果，你会得到一个“你好，人类！”回应。这是因为最后一个 URL 段指定了“人类”，然后在程序中将其分配给`name`。

## 带多个参数的`greet`函数示例

让我们进一步看前面的例子。以下是带有一个、两个或三个参数的`greet`函数的示例。

像以前一样，让我们从单个参数开始。将以下代码复制到您的文件中:

```
#[get("/{name}")]
async fn greet(path: web::Path<(String, )>) -> impl Responder {
  let name = path.into_inner().0;
  format!("Hello, {name}")
}

```

现在我们用两个参数:`name`和`age`。用下面的代码替换文件内容:

```
#[get("/{name}/{age}")]
async fn greet(path: web::Path<(String, i32)>) -> impl Responder {
  let name: String = path.into_inner().0;
  let age: i32 = path.into_inner().1;
  format!("Hello, {name} you are {age} years")
}

```

现在我们将添加第三个参数:`hair_color`。对于这三个参数，使用下面的代码:

```
#[get("/{name}/{age}/{hair_color}")]
async fn greet(path: web::Path<(String, i32, String)>) -> impl Responder {
  let name: String = path.into_inner().0;
  let age: i32 = path.into_inner().1;
  let hair_color: String = path.into_inner().2;
  format!("Hello, {name} you are {age} with {hair_color} hair")
}

```

一个 URL 上可以有任意数量的灵活段。这样，您可以添加逻辑来处理在 API 运行之前未知的路由。

## 使用 REST APIs 扩展复杂的功能，从 Rust 扩展到 Rhai

Rhai 是一个评估引擎，这意味着你不能运行复杂的功能，比如数据库操作。要使用这些复杂的功能，您需要从 Rust 扩展它们。

在下面的 REST API 项目中，使用 Rust 创建 API 和路由，而 Rhai 处理逻辑。

用于构建这种 API 的库是`actix-web`和`rhai`。按照以下步骤安装这些库。

首先，将以下依赖项添加到项目的`Cargo.toml`文件中。

```
[dependencies]
actix-web = "4.0.1"
rhai = "1.6.1"

```

接下来，让我们构建项目。使用以下命令在项目中安装库:

```
cargo build

```

现在我们准备开始了。

### 用 Rust 注册项目的端点

本节中的示例项目有两个端点。

端点`/multiply/{num1}/{num2}`用于两个数相乘。例如，如果您向[http://localhost:8080/multiply/5/10](http://localhost:8080/multiply/5/10)发送一个 GET 请求，您将得到“50”作为响应。

同时，`/add/{num1}/{num2}`用于两个数相加。如果您向[http://localhost:8080/add/5/10](http://localhost:8080/add/5/10)发送一个 GET 请求，您将得到“15”作为响应。

下面的程序将被写入我们的`main.rs`文件:

```
use actix_web::{
    HttpServer,
    get,
    App,
    web::Path,
    Responder,
};
use rhai::Engine;

#[get("/multiply/{num1}/{num2}")]
async fn multiply(path: Path<(i64, i64)>) -> impl Responder {
    // get the numbers from the url path
    let (num1, num2) = path.into_inner();

    // create an instance of the rhai engine
    let mut engine = Engine::new();

    // register an API that exposes the numbers to Rhai
    engine.register_fn("num1", move || num1);
    engine.register_fn("num2", move || num2);

    // run the script
    let result = engine.eval_file::<i64>("src/multiply.rhai".into()).unwrap();

    // return the result
    format!("{result}")
}

#[get("/add/{num1}/{num2}")]
async fn add(path: Path<(i64, i64)>) -> impl Responder {
    // get the numbers from the url path
    let (num1, num2) = path.into_inner();

    // create an instance of the rhai engine
    let mut engine = Engine::new();

    // register an API that exposes the numbers to Rhai
    engine.register_fn("num1", move || num1);
    engine.register_fn("num2", move || num2);

    // run the script
    let result = engine.eval_file::<i64>("src/add.rhai".into()).unwrap();

    // return the result
    format!("{result}")
}

// When Rust executes the Rhai script, Rhai returns the result of the last expression

#[actix_web::main]
async fn main() -> std::io::Result<()> {

    HttpServer::new(|| {
        App::new()
            .service(multiply)
            .service(add)
    })
        .bind(("127.0.0.1", 8080))
        .unwrap()
        .run()
        .await
}

```

复制了上面的代码后，现在可以在 Rhai 引擎中使用`multiply`和`add`函数了。在`src`文件夹中创建两个文件:

*   `multiply.rhai`，执行`/multiply`路径的逻辑。
*   `add.rhai`，它执行`/add`端点的逻辑

将以下脚本复制到`multiply.rhai`文件中:

```
fn multiply(num1, num2) {
  return num1 * num2;
}

let num1 = num1();
let num2 = num2();

multiply(num1, num2);

```

将以下脚本复制到`add.rhai`文件中:

```
fn add(num1, num2) {
  return num1 + num2;
}

let num1 = num1();
let num2 = num2();

add(num1, num2);

```

在编译和执行 Rust 程序后，通过在浏览器中打开一个例子，如[http://localhost:8080/add/5/15](http://localhost:8080/add/5/15)来测试结果。这个例子应该产生类似下面的结果:
![Result Of Running Rust Program With Add Function Of Five Plus Fifteen Showing Outcome Of Twenty On Webpage At Localhost8080](img/dcadbd32b0561d39cb29b060cd9c2e05.png)

## 结论

在本文中，我们讨论了创建 Rhai 脚本，使用 Actix Web 构建后端 API，以及在后端 API 中使用 Rhai 脚本。

Rhai 是构建 Rust 程序的一个非常有用的工具，它帮助开发人员用更容易理解的脚本编写复杂的代码，然后可以嵌入到 Rust 程序中。

看看本文最后一个项目的 Github repo】来检查或比较你的工作。如果你有兴趣了解这个主题的更多信息，请阅读[的 Rhai 书籍](https://rhai.rs/book/#the-rhai-book)或者学习[如何用 Rust](https://blog.logrocket.com/create-backend-api-with-rust-postgres/) 创建后端 API。

我希望这篇文章对你有用。感谢阅读！

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。