# 了解如何阅读 Rust 中的文件

> 原文：<https://blog.logrocket.com/how-to-read-files-rust/>

使用文件可能是软件工程中一个挑剔但不可避免的部分，作为一名开发人员，您经常需要从外部资源加载信息以在您的项目中使用。

在这篇博文中，你将学习如何在 [Rust](https://blog.logrocket.com/tag/rust/) 中读取文件。具体来说，您将学习如何用 Rust 编程语言读取 JSON 文件、YAML 文件和 TOML 文件。

*向前跳转:*

## 访问文件

首先，我们首先需要创建一个样本文件，我们将通过我们的项目来访问它。您可以手动创建该文件，也可以使用 Rust 标准库提供的 write()函数。

让我们在终端上使用以下命令引导 Rust starter 项目:

```
cargo new sample_project

```

接下来，在我们项目的根目录下创建一个新文件，在那里我们将有我们的源代码。

这个文件将被称为`info.txt`，它将包含一小段随机的文本，如下所示:

```
// info.txt
Check out more Rust articles on LogRocket Blog

```

## 将文件作为字符串读取

首先，我们需要用一个`use`语句导入文件模块。Rust 提供了一个标准库`std`箱，为`fs`模块提供文件读写操作。

```
use std::fs;
fn main() {
    let file_contents = fs::read_to_string("info.txt")
        .expect("LogRocket: Should have been able to read the file");
    println!("info.txt context =\n{file_contents}");
}

```

使用上面的代码片段，我们打开并读取位于路径值的文件，该路径值作为参数在`fs`模块的`read_to_string`函数中传递。此外，我们必须指定如果由于任何原因文件打不开会发生什么；例如，有一个权限错误或类似的问题。在`expect`函数中，我们传递文件打不开时要显示的文本。

使用`cargo run`命令，上述程序将被编译并运行，然后输出我们之前创建的文件的内容。对于这个例子，它将与`info.txt`的内容具有相同的值。

## 将文件作为向量读取

如果您想将文件的内容存储在内存中以便于访问或修改，将文件作为向量读取会很有用。它对于读取二进制文件也很有用，因为字节向量比字符串更能准确地表示数据。将文件作为一个向量读取，可以让你一次将整个文件读入内存，而不是一段一段地读取。如果您需要多次访问该文件或者想要对整个文件执行操作，这可能会更方便。

要将文件作为向量读取，可以使用`Read`特征的`read_to_end`方法:

```
// Rust
use std::fs::File;
use std::io::Read;

fn main() -> std::io::Result<()> {
    let mut file = File::open("info.txt")?;
    let mut contents = Vec::new();
    file.read_to_end(&mut contents)?;

    println!("File contents: {:?}", contents);

    Ok(())
}

```

这段代码打开一个名为`"info.txt"`的文件，并将其读入一个名为`contents`的字节向量。`read_to_end`方法从当前位置读取文件到文件末尾，并将数据附加到`contents`向量的末尾。

## 用缓冲区读取文件

使用缓冲区读取 Rust 文件比一次读取整个文件更有效，因为它允许程序以块的形式处理数据。这对于可能无法完全放入内存的大文件尤其有用。

要使用 buffer 读取文件，可以使用`BufReader` struct 和`BufRead` trait:

```
// Rust
use std::fs::File;
use std::io::{BufReader, BufRead};

fn main() -> std::io::Result<()> {
    let file = File::open("info.txt")?;
    let reader = BufReader::new(file);

    for line in reader.lines() {
        let line = line?;
        println!("{}", line);
    }

    Ok(())
}

```

这段代码打开一个名为`"info.txt"`的文件，并创建一个`BufReader`来逐行读取它。`BufReader`分块(或“缓冲区”)读取文件，而不是一次读取所有文件，这对大文件来说更有效。

## 在 Rust 中处理文件 I/O 错误

想知道当一个 Rust 文件无法打开或读取时如何处理错误？您可以使用`std::io::Result`类型和`?`运算符。在上面的例子中，`?`操作符用于传播打开或读取文件时出现的任何错误。如果出现错误，它将中断函数的执行，并将错误返回给调用者。

下面是一个处理特定 I/O 错误的示例:

```
// Rust
use std::fs::File;
use std::io::Read;

fn main() -> std::io::Result<()> {
    let mut file = match File::open("info.txt") {
        Ok(file) => file,
        Err(error) => {
            match error.kind() {
                std::io::ErrorKind::NotFound => {
                    println!("File not found");
                    return Ok(());
                }
                _ => return Err(error),
            }
        }
    };
    let mut contents = Vec::new();
    file.read_to_end(&mut contents)?;

    println!("File contents: {:?}", contents);

    Ok(())
}

```

在这个例子中，代码试图打开文件`"info.txt"`。如果没有找到该文件，它将打印一条消息并返回`Ok(())`。如果发生任何其他错误，它会将错误返回给调用者。如果文件成功打开，它会像以前一样将文件内容读入一个向量。

## Serde 框架

[Serde](https://serde.rs/) 是一个框架，用于高效、通用地**序列化**和**反序列化** Rust 数据结构。对于本文的这一部分，我们将利用`serde` crate 来解析 JSON 文件和 TOML 文件。

`serde`库的基本优点是，它允许您直接将连接数据解析成 Rust `structs`，该 Rust`structs`与我们的源代码中定义的类型相匹配。这样，您的项目在源代码编译时就知道每个传入数据的预期类型。

## 读取 JSON 文件

JSON 格式是一种用于存储复杂数据的流行数据格式。在用于在网络上交换有线数据的常见数据格式中，这是最主要的数据格式。它在 JavaScript 项目中被广泛使用。

我们可以通过静态类型的方法和动态类型的方法来解析 Rust 中的 JSON 数据。

动态类型的方法最适合于根据源代码中预定义的数据结构不确定 JSON 数据格式的情况，而静态类型的方法用于确定 JSON 数据格式的情况。

要开始，您必须安装所有必需的依赖项。

在`Cargo.toml`文件中，我们将首先添加`serde`和`serde_json`板条箱作为依赖项。除此之外，确保可选的派生特性被启用，这将帮助我们生成(反)序列化的代码。

```
//Cargo.toml
[dependencies]
serde = { version = 1.0, features = [“derived”] }
serde_json = "1.0"

```

### 动态解析 JSON

首先，我们编写一个`use`声明来导入 [`serde_json`](https://docs.rs/serde_json/latest/serde_json/) 板条箱。`Value` enum 是`serde_json` crate 的一部分，它代表任何有效的 JSON 值——可以是字符串、空值、布尔值、数组等。

在根目录中，我们将创建一个. json 文件来存储任意 JSON 数据，我们将读取数据并将其解析为源代码中定义的有效数据结构。创建一个数据文件夹，然后创建一个 sales.json 文件并用这个 [JSON 数据](https://github.com/IkehAkinyemi/json_dynamic/blob/main/data/sales.json)更新它。

现在，我们有了连线数据，我们可以使用`serde_json` crate 来更新 main.rs 文件，以编写解析 JSON 数据的代码:

```
use serde_json::Value;
use std::fs;
fn main() {
    let sales_and_products = {
        let file_content = fs::read_to_string("./data/sales.json").expect("LogRocket: error reading file");
        serde_json::from_str::<Value>(&file_content).expect("LogRocket: error serializing to JSON")
    };
    println!("{:?}", serde_json::to_string_pretty(&sales_and_products).expect("LogRocket: error parsing to JSON"));
}

```

在上面的代码片段中，我们硬编码了`sales.json`文件的路径。然后，使用`serde_json`，我们为 JSON 数据格式提供(解)序列化支持。

根据 JSON 格式的规则，`from_str`将一个连续的字节片作为参数，并从中反序列化一个类型为`Value`的实例。您可以检查`Value`类型以了解更多关于它的(反)序列化的信息。

这是运行代码片段的输出:

```
"{\n  \"products\": [\n    {\n      \"category\": \"fruit\",\n      \"id\": 591,\n      \"name\": \"orange\"\n    },\n    {\n      \"category\": \"furniture\",\n      \"id\": 190,\n      \"name\": \"chair\"\n    }\n  ],\n  \"sales\": [\n    {\n      \"date\": 1234527890,\n      \"id\": \"2020-7110\",\n      \"product_id\": 190,\n      \"quantity\": 2.0,\n      \"unit\": \"u.\"\n    },\n    {\n      \"date\": 1234567590,\n      \"id\": \"2020-2871\",\n      \"product_id\": 591,\n      \"quantity\": 2.14,\n      \"unit\": \"Kg\"\n    },\n    {\n      \"date\": 1234563890,\n      \"id\": \"2020-2583\",\n      \"product_id\": 190,\n      \"quantity\": 4.0,\n      \"unit\": \"u.\"\n    }\n  ]\n}"

```

在一个实际的项目中，除了显示输出之外，我们还想访问 JSON 数据中的不同字段，操纵数据，甚至试图将更新的数据存储在另一个文件或同一个文件中。

记住这一点，让我们尝试访问`sales_and_products`变量上的一个字段，更新它的数据，并可能将其存储在另一个文件中:

```
use serde_json::{Number, Value};
// --snip--

fn main() {
    // --snip--
    if let Value::Number(quantity) = &sales_and_products\["sales"\][1]["quantity"] {
        sales_and_products\["sales"\][1]["quantity"] =
            Value::Number(Number::from_f64(quantity.as_f64().unwrap() + 3.5).unwrap());
    }
    fs::write(
        "./data/sales.json",
        serde_json::to_string_pretty(&sales_and_products).expect("LogRocket: error parsing to JSON"),
    )
    .expect("LogRocket: error writing to file");
}

```

在上面的代码片段中，我们利用`Value::Number`变量对`sales_and_products\["sales"\][1]["quantity"]`进行模式匹配，我们期望它是一个数值。

使用`Number`结构上的`from_f64`函数，我们将从操作`quantity.as_f64().unwrap() + 3.5`返回的有限`f64`值转换回一个`Number`类型，然后我们将它存储回`sales_and_products\["sales"\][1]["quantity"]`，更新它的值。

(注意:确保使`sales_and_products`成为可变变量)

然后，使用 write 函数和一个文件路径作为参数，我们用调用`serde_json::to_string_pretty`函数的结果值创建和更新一个文件。这个结果值将与我们之前在终端上输出的值相同，但是格式良好。

### 静态解析 JSON

另一方面，如果我们完全确定 JSON 文件的结构，我们可以使用不同的方法，包括在项目中使用预定义的数据。

这是反对动态解析数据的首选方法。静态版本的源代码在开头声明了三个结构:

```
use serde::{Deserialize, Serialize};
#[derive(Deserialize, Serialize, Debug)]
struct SalesAndProducts {
    products: Vec<Product>,
    sales: Vec<Sale>
}
#[derive(Deserialize, Serialize, Debug)]
struct Product {
    id: u32,
    category: String,
    name: String
}
#[derive(Deserialize, Serialize, Debug)]
struct Sale {
    id: String,
    product_id: u32,
    date: u64,
    quantity: f32,
    unit: String
}
fn main() {}

```

第一个结构将包含在 JSON 对象的`sales`和`products`字段中的内部数据格式分组。剩下的两个结构定义了存储在 JSON 对象外部字段中的预期数据格式。

为了将 JSON 字符串解析(读取)到上述结构中，`Deserialize`特性是必要的。并且要将上述结构格式化(即写入)成有效的 JSON 数据格式，必须存在`Serialize`特征。简单地在终端上打印这个结构(调试跟踪)就是`Debug`特性派上用场的地方。

我们的`main`函数体应该类似于下面的代码片段:

```
use std::fs;
use std::io;
use serde::{Deserialize, Serialize};

// --snip--

fn main() -> Result<(), io::Error> {
    let mut sales_and_products: SalesAndProducts = {
        let data = fs::read_to_string("./data/sales.json").expect("LogRocket: error reading file");
        serde_json::from_str(&data).unwrap()
    };
    sales_and_products.sales[1].quantity += 1.5;
    fs::write("./data/sales.json", serde_json::to_string_pretty(&sales_and_products).unwrap())?;

    Ok(())
}

```

函数`serde_json::from_str::SalesAndProducts`用于解析 JSON 字符串。增加橙子销售量的代码变得非常简单:

```
sales_and_products.sales[1].amount += 1.5

```

与我们的动态方法相比，源文件的其余部分保持不变。

## 静态解析 TOML

在本节中，我们将重点阅读和解析 TOML 文件。大多数配置文件可以以 TOML 文件格式存储，由于其语法语义，它可以很容易地转换成类似字典或 HashMap 的数据结构。由于它的语义力求简洁，所以读和写都相当简单。

我们将静态读取并解析这个 [TOML 文件](https://github.com/IkehAkinyemi/toml_static/blob/main/data/config.toml)。这意味着我们知道我们的 TOML 文件的结构，我们将在这一节中使用预定义的数据。

我们的源代码将包含以下将映射的`structs`；解析 TOML 文件时更正其内容:

```
#![allow(dead_code)]
use serde::{Deserialize, Serialize};
use std::fs;

#[derive(Deserialize, Debug, Serialize)]
struct Input {
    xml_file: String,
    json_file: String,
}

#[derive(Deserialize, Debug, Serialize)]
struct Redis {
    host: String,
}

#[derive(Deserialize, Debug, Serialize)]
struct Sqlite {
    db_file: String
}

#[derive(Deserialize, Debug, Serialize)]
struct Postgresql {
    username: String,
    password: String,
    host: String,
    port: String,
    database: String
}

#[derive(Deserialize, Debug, Serialize)]
struct Config {
    input: Input,
    redis: Redis,
    sqlite: Sqlite,
    postgresql: Postgresql
}

fn main() {}

```

仔细看看上面的代码片段，您会发现我们定义了每个结构来映射到 TOML 文件中的每个表/头，结构中的每个字段映射到表/头下的键/值对。

接下来，使用`serde`、`serde_json`和`toml`箱子，我们将编写代码来读取和解析主函数体中的 TOML 文件。

```
// --snip--
fn main() {
    let config: Config = {
        let config_text = fs::read_to_string("./data/config.toml").expect("LogRocket: error reading file");
        toml::from_str(&config_text).expect("LogRocket: error reading stream")
    };
    println!("[postgresql].database: {}", config.postgresql.database); 
}

```

输出:

```
[postgresql].database: Rust2018

```

上面代码片段的不同部分是`toml::from_str`函数，它试图解析我们使用`fs::read_to_string`函数读取的`String`值。使用`Config`结构作为指导的`toml::from_str`函数知道从`String`值中期待什么。

另外，我们可以使用下面几行代码轻松地将上面的`config`变量解析为 JSON 值:

```
// --snip--
fn main() {
    // --snip--
    let _serialized = serde_json::to_string(&config).expect("LogRocket: error serializing to json");
    println!("{}", serialized);
}

```

输出:

```
{"input":{"xml_file":"../data/sales.xml","json_file":"../data/sales.json"},"redis":{"host":"localhost"},"sqlite":{"db_file":"../data/sales.db"},"postgresql":{"username":"postgres","password":"post","host":"localhost","port":"5432","database":"Rust2018"}}

```

## 静态解析 YAML

项目中使用的另一个流行的配置文件是 YAML 文件格式。在本节中，我们静态地阅读和解析 Rust 项目中的 YAML 文件。我们将使用这个 [YAML 文件](https://github.com/IkehAkinyemi/zero2prod/blob/main/configuration.yaml)作为本节的示例。

我们将利用配置箱来解析 YAML 文件，作为第一种方法，我们将定义必要的结构来充分解析我们的 YAML 文件的内容。

```
#[derive(serde::Deserialize)]
pub struct Settings {
    pub database: DatabaseSettings,
    pub application_port: u16,
}
#[derive(serde::Deserialize)]
pub struct DatabaseSettings {
    pub username: String,
    pub password: String,
    pub port: u16,
    pub host: String,
    pub database_name: String,
}
fn main() {}

```

接下来，我们将在我们的`main`函数中读取并解析 YAML 文件。

```
// --snip--
fn main() -> Result<(), config::ConfigError> {
    let mut settings = config::Config::default(); // --> 1
      let Settings{database, application_port}: Settings = {
        settings.merge(config::File::with_name("configuration"))?; // --> 2
        settings.try_into()? // --> 3
      };

      println!("{}", database.connection_string());
      println!("{}", application_port);
      Ok(())
}

impl DatabaseSettings {
    pub fn connection_string(&self) -> String { // --> 4 
        format!(
            "postgres://{}:{}@{}:{}/{}",
            self.username, self.password, self.host, self.port, self.database_name
        )
    }
}

```

上面的代码片段比前面的例子有更多可移动的部分，所以让我们解释每一部分:

1.  我们使用字段类型的默认值来初始化`Config`结构。您可以检查`Config`结构来查看默认字段
2.  使用 config::File::with_name 函数，我们搜索并定位一个名为 configuration 的 YAML 文件。如文档所定义的，我们使用`Config`结构上的合并功能合并到一个配置属性源中
3.  使用前一行代码的源代码，我们尝试将 YAML 文件内容解析为我们定义的`Settings`结构
4.  这是一个在`DatabaseSettings`结构上定义的实用函数，用于格式化和返回 Postgres 连接字符串

以上示例的成功执行将输出:

```
postgres://postgres:[email protected]:5432/newsletter
8000

```

## **结论**

在本文中，我们探讨了如何在 Rust 项目中读取不同的文件。Rust 标准库提供了各种执行文件操作的方法，特别是读/写操作，我希望这篇文章对展示如何在 Rust 中读取文件有用。

我们还冒昧地查看了 Serde crate，以及它如何在帮助我们将 YAML、JSON 或 TOML 等不同文件格式解析为 Rust 程序可以理解的数据结构方面发挥重要作用。

我们研究了三种流行的文件格式；YAML、JSON 和 TOML。作为本文的一部分，您可以探索 Rust [crates.io](https://crates.io/) 来发现您可以用来读/写本文范围之外的配置管理文件的其他 crates，如 INI、XML，以及您的下一个 Rust 项目中的更多内容。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。