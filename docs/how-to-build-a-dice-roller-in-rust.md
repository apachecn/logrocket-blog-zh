# 如何在 Rust - LogRocket 博客中构建掷骰子

> 原文：<https://blog.logrocket.com/how-to-build-a-dice-roller-in-rust/>

让我们澄清一下:我是一个超级龙与地下城的书呆子。让一群成年人聚在一个房间里玩假装游戏是很特别的。

《D&D》涉及运用你的想象力为你的角色做出选择，但是一些行动的结果是由掷骰子决定的。

举个例子，如果你想用你的长剑向一个地精挥去，首先你需要翻滚看看你是否击中了。假设你掷出一个 20 面骰子(d20)，在你的掷骰子中加入一些数字，然后等待地下城主告诉你是否命中。如果你击中，你掷更多的骰子来决定你造成多少伤害。举例来说，如果你在试图击中时在你的 d20 上掷出一个 20，这意味着你击中了一个致命一击，造成一系列额外的伤害。

我要带着这个去哪里？在本教程中，我将向您展示如何创建一个 web 服务来在您访问某个 URL 时为您掷骰子。这将是一个相对基础的项目，适合新手 Rust 的有经验的程序员。

## 入门指南

我们将把 [Rocket](https://rocket.rs/) 用于我们的 web 服务。虽然它使用夜间生锈而不是稳定生锈，它很容易使用，应该工作得很好。

首先，确保你已经安装了夜间 Rust。

```
$ rustup toolchain install nightly

```

接下来，创建一个名为`roll-server`的`cargo`项目。

```
$ cargo new roll-server

```

将每夜 Rust 设置为该项目的默认设置。

```
$ cd roll-server
$ rustup override set nightly

```

将 Rocket 添加到您的`Cargo.toml`中，并禁用默认功能。在撰写本文时，它的一个依赖项(`ring`)中有一个 bug 阻止了 Rocket 的构建。

```
[dependencies]
rocket = { version = "0.4.4", default-features = false }

```

接下来，修改你的`main.rs`看起来像 Rocket 的“[入门](https://rocket.rs/v0.4/guide/getting-started/#getting-started)”指南中的例子，只是为了确保一切都按预期工作。

```
#![feature(proc_macro_hygiene, decl_macro)]
#[macro_use] extern crate rocket;

fn main() {
    rocket::ignite().mount("/", routes![index]).launch();
}

#[get("/")]
fn index() -> &'static str {
    "Hello, world!"
}

```

用`cargo run`运行项目。如果你访问`localhost:8000`，你应该会在你的浏览器中看到`Hello, World!`。现在，您已经准备好深入研究这个项目了。

## 路线

在我们的应用程序中有两条主要路线:`/roll/<dice>`和`/roll/crit/<dice>`。第一个掷出你在路线的`<dice>`部分指定的骰子。第二种方法对掷骰子应用一些特殊的规则来计算致命一击的伤害。

在 Rocket 中，通过创建一个函数并在上面放置一个描述路径的属性来处理对特定路径的请求。例如，为了响应路径`/foo/bar`，您将创建以下函数。

```
#[get("/foo/bar")]
fn my_handler() -> &'static str {
    "foo bar"
}

```

在我们的例子中，`/roll/<dice>`和`/roll/crit/<dice>`路径都以`/roll`开始。让我们在`/roll`路径下挂载`/<dice>`和`/crit/<dice>`处理程序，而不是在每个处理程序中显式写出`/roll`。应用程序的框架如下。

```
// main.rs
fn main() {
    rocket::ignite()
        .mount("/roll", routes![normal, critical])
        .launch();
}

#[get("/<dice>")]
fn normal(dice: String) -> String {
    format!("normal: {}", dice)
}

#[get("/crit/<dice>")]
fn critical(dice: String) -> String {
    format!("critical: {}", dice)
}

```

如果您运行应用程序并访问`localhost:8000/roll/foo`，您应该会看到`normal: foo`。同样，如果你去`localhost:8000/roll/critical/foo`，你应该去看看`critical: foo`。

## 从语法上分析

既然您可以从路径中提取一个字符串，那么您需要对它做一些事情。但是，不能只接受任何字符串作为路径的一部分。如果用户访问了`/roll/foo`会怎样？他们会掷出什么骰子？

相反，我们只接受有效的骰子符号。这是表示要掷出的骰子的数量和大小的简洁方式。符号的形式是`<number>d<size>`，所以`4d12`将代表四个 12 面骰子。

要确定哪些字符串是带有正则表达式的有效骰子符号，请将[正则表达式](https://github.com/rust-lang/regex)添加到您的`Cargo.toml`中。

```
[dependencies]
rocket = { version = "0.4.4", default-features = false}
regex = "1"

```

接下来，创建文件`parse.rs`，这是放置所有解析逻辑的地方。我们将把一个类似于`4d6`的字符串解析成一个代表骰子数量和大小的结构`RollCmd`。

我们将骰子的数量限制在 255 个，因为这已经是一吨重的骰子了，而且正好适合一个`u8`。更进一步，我们可以认识到掷出零骰子是没有意义的，所以我们将解析成一个`NonZeroU8`。骰子的大小是固定的，所以我们将使用一个枚举来表示可用的大小。最后，我们需要一个类型来表示事情出错的各种方式。我们也将为此使用一个`enum`。

将所有这些放在一起，您应该有以下类型定义。

```
// parse.rs
use std::num::NonZeroU8;

#[derive(Debug, PartialEq)]
pub(crate) enum ParseError {
    InvalidDiceNumber,
    InvalidDiceSize,
    UnableToParse,
}

#[derive(Debug, PartialEq, Copy, Clone)]
pub(crate) enum DiceSize {
    D4,
    D6,
    D8,
    D10,
    D12,
    D20,
    D100,
}

#[derive(Debug, PartialEq)]
pub(crate) struct RollCmd {
    pub num: NonZeroU8,
    pub size: DiceSize,
}

```

下一个难题是正则表达式。使用正则表达式`^([1-9]\d*)d(\d+)$`。再简单一点，就会允许无效输入。再严格一些，就会丢失关于哪些部分解析不正确的信息。

让我们看看成品，然后把它分成小块。

```
// parse.rs
pub(crate) fn parse_dice_str(dice_str: &str) -> Result<RollCmd, ParseError> {
    let dice_regex = Regex::new(r"^([1-9]\d*)d(\d+)$").unwrap();
    let caps = dice_regex.captures(dice_str).ok_or(ParseError::UnableToParse)?;
    let dice_num = caps.get(1)  // Option<Match>
        .ok_or(ParseError::InvalidDiceNumber)?  // Match
        .as_str().parse::<NonZeroU8>()  // Match -> str -> Result<NonZeroU8, Err>
        .map_err(|_| {ParseError::InvalidDiceNumber})?;  // NonZeroU8
    let dice_size = caps.get(2)
        .ok_or(ParseError::InvalidDiceSize)?
        .as_str()
        .parse::<DiceSize>()?;
    Ok(RollCmd {
        num: dice_num,
        size: dice_size
    })
}

```

首先，我们用`Regex::new`编译正则表达式，然后解开它。这跳过了任何错误处理，通常是不被允许的。我知道这个正则表达式会正确编译，所以在这种情况下没问题。接下来，我们将正则表达式应用于用户提供的字符串。然后我们使用`?`操作符来获取匹配或者立即返回一个错误。

下一部分更复杂，所以我注释了类型。我们将使用`?`操作符做同样的事情，然后尝试将字符串解析成`NonZeroU8`。如果出现错误，我们将丢弃它并返回我们自己的错误。解析骰子大小基本相同，但这次我们将通过告诉编译器如何将字符串转换为`DiceSize`并指定出错时返回的错误类型，直接从`parse`返回一个`ParseError`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
// parse.rs
use std::str::FromStr;

impl FromStr for DiceSize {
    type Err = ParseError;
    fn from_str(s: &str) -> Result<Self, Self::Err> {
        match s {
            "4" => Ok(DiceSize::D4),
            "6" => Ok(DiceSize::D6),
            "8" => Ok(DiceSize::D8),
            "10" => Ok(DiceSize::D10),
            "12" => Ok(DiceSize::D12),
            "20" => Ok(DiceSize::D20),
            "100" => Ok(DiceSize::D100),
            _ => Err(ParseError::InvalidDiceSize)
        }
    }
}

```

## 掷色子

既然我们已经知道了要滚动什么，我们就可以研究如何滚动了。我们将使用`rand`箱来生成我们的随机掷骰子。请将它添加到您的`Cargo.toml`中。

```
rand = "0.7"

```

现在创建一个名为`roll.rs`的文件。这是您编写处理骰子滚动的代码的地方。掷骰子将会是`usize`(`NonZeroUsize`更有意义，但是数学运算是为`usize`定义的)。

下面是保存掷骰子的结构和将生成它们的函数:

```
// roll.rs
#[derive(Debug, PartialEq)]
pub(crate) struct Rolls(pub Vec<usize>);

pub(crate) fn roll_normal(cmd: &RollCmd) -> Rolls {
    todo!()
}

pub(crate) fn roll_critical(cmd: &RollCmd) -> Rolls {
    todo!()
}

pub(crate) generate_rolls(cmd: &RollCmd) -> Vec<usize> {
    todo!()
}

```

`generate_rolls`函数处理所有常见的掷骰子操作，然后`roll_normal`和`roll_critical`做他们自己特定的工作。让我们看看随机数是如何产生的。

```
// roll.rs
pub(crate) fn generate_rolls(cmd: &RollCmd) -> Vec<usize> {
    let mut rng = thread_rng();
    let distribution = Uniform::new_inclusive(1, usize::from(cmd.size));
    let rolls: Vec<usize> = (0..cmd.num.get())
        .map(|_| {
            distribution.sample(&mut rng).into()
        }).collect();
    rolls
}

```

这样就随机掷出了`cmd.num`次。掷骰子来自`[1, cmd.size]`的均匀概率分布，这意味着骰子上的每个数字出现的可能性是相等的。我们制作面包卷，把它们收集在一个`Vec`里，然后归还。

如果你正在密切关注，你可能已经注意到第三行的`usize::from(cmd.size)`。这个操作将一个`DiceSize`转换成一个`usize`。我们通过实现`From`特征来告诉编译器如何做到这一点。

```
// parse.rs
impl From<DiceSize> for usize {
    fn from(d: DiceSize) -> Self {
        match d {
            DiceSize::D4 => 4,
            DiceSize::D6 => 6,
            DiceSize::D8 => 8,
            DiceSize::D10 => 10,
            DiceSize::D12 => 12,
            DiceSize::D20 => 20,
            DiceSize::D100 => 100,
        }
    }
}

```

一旦我们掷骰子，我们可以把它们传给`roll_normal`和`roll_critical`。对于`roll_normal`，我们将只返回掷骰子。对于`roll_critical`，我们将在已经掷出的骰子上增加一次全伤害掷骰(例如`4d6`变成`4d6 + 24`)。

```
// roll.rs
pub(crate) fn roll_normal(cmd: &RollCmd) -> Rolls {
    let rolls = generate_rolls(cmd);
    Rolls(rolls)
}

pub(crate) fn roll_crit(cmd: &RollCmd) -> Rolls {
    let mut rolls = generate_rolls(cmd);
    let num = usize::from(u8::from(cmd.num.get()));
    let size = usize::from(cmd.size);
    let crit = num.checked_mul(size).unwrap();
    rolls.push(crit);
    Rolls(rolls)
}

```

当我们将骰子的数量和大小相乘时，会返回一个`Result`,因为乘法运算可能会溢出。我们打开这个`Result`,因为我们的最大骰子数 255 和最大骰子大小 100 永远不会导致溢出。

## 反应

此时，我们已经完成了所有的计算，我们需要在考虑解析错误的同时响应请求。在我们的路由处理程序中，我们将返回一个`Result`，其中`Err`将是一个将 HTTP 状态设置为`400 Bad Request`的类型。Rocket 有一个内置的类型来为我们做这件事:`rocket::response::status::BadRequest`。

我们将再次使用`?`操作符来处理错误，这意味着我们需要告诉编译器如何将`ParseError`转换成`BadRequest`。

```
// parse.rs
use rocket::response::status::BadRequest;

impl From<ParseError> for BadRequest<String> {
    fn from(p: ParseError) -> Self {
        match p {
            ParseError::InvalidDiceNumber => {
                BadRequest(Some(String::from("Number of dice must be <= 255")))
            }
            ParseError::InvalidDiceSize => BadRequest(Some(String::from(
                "Dice size must be 4, 6, 8, 10, 12, 20, or 100",
            ))),
            ParseError::UnableToParse => BadRequest(Some(String::from(
                "Unable to parse, must be of the form <number>d<size>",
            ))),
        }
    }
}

```

每个分支中的`Some(foo)`将响应的主体设置为`foo`,这样用户就知道哪里出错了。

接下来，把纸卷缝成一串。

```
1 + 2 + 3 + 4 = 10

```

你可以用一个叫做`assemble_response`的新函数来做这件事。

```
// main.rs
fn assemble_response(rolls: &Rolls) -> String {
    let roll_str: String = rolls
        .0
        .iter()
        .map(|d| d.to_string())
        .collect::<Vec<String>>()
        .join(" + ");
    let sum_str = rolls.0.iter().sum::<usize>().to_string();
    [roll_str, sum_str].join(" = ")
}

```

我们快完成了！我们所要做的就是将这些片段放在我们的`normal`和`critical`处理程序中，这些处理程序是我们在项目开始时制作的。

```
// main.rs
#[get("/<dice>")]
fn normal(dice: String) -> Result<String, BadRequest<String>> {
    let cmd = parse_dice_str(dice.as_ref())?;
    let rolls = roll_normal(&cmd);
    let resp = assemble_response(&rolls);
    Ok(resp)
}

#[get("/crit/<dice>")]
fn critical(dice: String) -> Result<String, BadRequest<String>> {
    let cmd = parse_dice_str(dice.as_ref())?;
    let rolls = roll_crit(&cmd);
    let resp = assemble_response(&rolls);
    Ok(resp)
}

```

## 结论

希望您在构建这个掷骰子服务时和我一样开心！还有很多工作要做。如果你想更深入一点，这里有一些让你开始的想法:

*   当用户访问`/stats`时掷骰子，这将需要掷出六次`4d6`并从每一次掷骰子中掷出最低的数字
*   记录自服务器启动以来掷出的骰子总数。为此，您需要研究 Rocket 的[状态](https://rocket.rs/v0.4/guide/state/#state)文档

这个项目的代码可以在 [GitHub](https://github.com/zmitchell/roll-server) 上找到。如果你有问题或者想要提交上面提到的任何一个项目，新的贡献者总是受欢迎的！

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。