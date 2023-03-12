# 尝试开沟...Rust 的捕获和空检查

> 原文：<https://blog.logrocket.com/ditching-try-catch-and-null-checks-in-rust/>

这篇文章是由一个刚进入 Rust 世界的 JavaScript 开发者写的。从这篇文章中获取价值并不需要 JS 背景！但是，如果你是一个由网络开发人员转型而来的农村人，你会更理解我的观点。

似乎在过去十年中构建的语言都在遵循一个共同的趋势:淘汰面向对象模型，引入[函数式编程](https://blog.logrocket.com/functional-programming-in-go/) (FP)。

Web 开发人员可能已经看到 FP 模式出现在现代前端框架中，比如 React 使用[他们的钩子模型](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/)。但是转到 Rust，当你围绕它构建一个完整的编程语言时，你会看到 FP 是多么强大——而接近`try...catch`和`null`只是冰山一角！

让我们探索抛出和捕捉异常的缺陷，Rust 的`Result`枚举和模式匹配能为你做什么，以及这如何扩展到处理`null`值。

## 什么是铁锈？

给你新的 [Rustaceans](https://www.rust-lang.org/) (yee-claw！🦀)，Rust 是一种底层的类型化语言，对所有程序员来说都足够友好。很像 C，Rust 直接编译成机器码(原始二进制)，所以 Rust 程序可以编译和运行得非常快。他们也非常重视交流和文档，拥有繁荣的[贡献者社区](https://www.rust-lang.org/community)和[过多的优秀教程](https://www.rust-lang.org/learn)。

## 为什么不应该在生锈时使用`try...catch`块

如果你像我一样，你已经习惯了在你的 JavaScript 代码库中跳`catch`舞。以这个场景为例:

```
// Scenario 1: catching a dangerous database call
app.get('/user', async function (req, res) {
  try {
    const user = await dangerousDatabaseCall(req.userId)
    res.send(user)
  } catch(e) {
    // couldn't find the user! Time to tell the client
    // it was a bad request
    res.status(400)
  }
})

```

这是典型的服务器模式。去调用数据库，当它工作时发送响应给用户，当它不工作时发送一些类似`400`的错误代码。

但是我们怎么知道在这里使用`try...catch`呢？嗯，有了像`dangerousDatabaseCall`这样的名字和对数据库的一些直觉，我们*知道*它可能会在出错时抛出一个异常。

现在让我们来看看这个场景:

```
// Scenario 2: forgetting to catch a dangerous file reading
app.get('/applySepiaFilter', async function (req, res) {
  const image = await readFile("/assets/" + req.pathToImageAsset)
  const imageWithSepiaFilter = applySepiaFilter(image)
  res.send(imageWithSepiaFilter)
})

```

当然，这是一个人为的例子。但是，简而言之，每当我们调用`applySepiaFilter`时，我们都希望从服务器的`/assets`中读取所请求的文件，并应用颜色过滤器。

但是等等，我们忘了在这上面加上一个`try...catch`！因此，每当我们请求一些不存在的文件时，我们都会收到一个讨厌的内部服务器错误。这最好是一种`400`“错误请求”状态。😕

现在你可能会想，“好吧，但是*我*不会忘记那个`try...catch`……”可以理解！一些 Node.js 程序员可能会立即意识到`readFile`抛出了异常。

但是当我们使用库函数*而没有*记录异常或者使用我们自己的抽象(如果你像我一样好斗，可能根本没有记录)时，这变得更加难以预测😬).

总结 JS 异常处理的一些核心问题:

*   **如果一个函数曾经** `throw` **s，调用者必须*记住*来处理那个异常。**不，你的花式 [ESlint](https://eslint.org/) 设置在这里帮不了你！这可能会导致我称之为`try...catch`的焦虑:把一切都包在`try`块中，以防出错。或者更糟的是，你会完全忘记`catch`一个异常，导致像我们未被捕获的`readFile`调用那样的令人窒息的失败
*   **异常的类型是不可预测的。对于围绕多个故障点的`try...catch`包装器来说，这可能是个问题。例如，如果我们的`readFile`爆炸应该返回一个状态代码，而`applySepiaFilter`失败应该返回另一个状态代码，那该怎么办？我们有多个`try...catch`区块吗？如果我们需要查看异常的`name`字段(哪个[可能不可靠](https://stackoverflow.com/questions/3656854/javascript-list-of-exceptions)浏览器端)呢？**

我们来看看 Rust 的`Result` enum。

## 使用 Rust 的`Result`枚举和模式匹配

这里有个惊喜:Rust 没有`try...catch`块。见鬼，就我们所知，他们甚至没有“例外”。

### 了解`match`铁锈

💡如果您已经了解模式匹配，请随意跳到下一部分。

在探索这怎么可能之前，让我们先理解 Rust 的模式匹配思想。这里有一个场景:

一位饥饿的顾客从我们的韩国街头食品菜单上点了一份`meal`，我们希望根据他们选择的`orderNumber`为他们提供不同的`meal`。

在 JavaScript 中，您可能会遇到一系列这样的条件:

```
let meal = null
switch(orderNumber) {
  case 1:
    meal = "Bulgogi"
    break
  case 2:
    meal = "Bibimbap"
    break
  default:
    meal = "Kimchi Jjigae"
    break
}
return meal

```

这足够易读，但是它有一个明显的缺陷(除了使用难看的`switch`语句):在我们的`switch`案例中，我们的`meal`需要以`null` *开始，而*需要使用`let`进行重新分配。要是`switch`真的能像这样*返回*一个值就好了…

```
// Note: this is not real JavaScript!
const meal = switch(orderNumber) {
  case 1: "Bulgogi"
  case 2: "Bibimbap"
  default: "Kimchi Jjigae"
}

```

你猜怎么着？Rust 让你做到了这一点！

```
let meal = match order_number {
  1 => "Bulgogi"
  2 => "Bibimbap"
  _ => "Kimchi Jjigae"
}

```

神圣的语法，蝙蝠侠！😮

这就是 Rust 的表情驱动设计的妙处。在这种情况下，`match`被认为是一个表达式，可以:

1.  动态执行一些逻辑(将订单号与餐串匹配)
2.  最后返回该值(可分配给`meal`)

条件句也可以是表达式。JavaScript 开发人员可能会触及三元组:

```
const meal = orderNumber === 1 ? "Bulgogi" : "Something else"

```

Rust 只是让你写一个`if`语句:

```
let meal = if order_number == 1 { "Bulgogi" } else { "Something else" }

```

是的，你可以跳过`return`这个词。Rust 表达式的最后一行总是返回值。🙃

### 将`match`应用于异常

好的，那么这如何应用于异常呢？

这次让我们先来看一个例子。假设我们正在编写与前面相同的`applySepiaFilter`端点。为了清楚起见，我将使用相同的`req`和`res`助手:

```
use std::fs::read_to_string;

// first, read the requested file to a string
match read_to_string("/assets/" + req.path_to_image_asset) {
  // if the image came back ay-OK...
  Ok(raw_image) => {
    // apply the filter to that raw_image...
    let sepia_image = apply_sepia_filter(raw_image)
    // and send the result.
    res.send(sepia_image)
  }
  // otherwise, return a status of 400
  Err(_) => res.status(400)
}

```

嗯，那些`Ok`和`Err`包装纸是怎么回事？让我们比较一下 Rust 的`read_to_string`和 Node 的`readFile`的返回类型:

*   在 Node land 中，`readFile`返回一个`string`你可以立即*使用*
*   在 Rust 中，`read_to_string`不是*返回一个字符串，而是返回[一个`Result`类型的](https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html)在*周围缠绕*一个字符串。完整的返回类型看起来像这样:`Result<std::string::String, std::io::Error>`。换句话说，这个函数返回的结果是一个字符串或者 I/O 错误*

这意味着我们不能处理`read_to_string`的结果，直到我们“解开”它(即，弄清楚它是一个字符串还是一个错误)。如果我们试图把一个`Result`当作一个字符串来处理，会发生以下情况:

```
let image = read_to_string("/assets/" + req.path_to_image_asset)
// ex. try to get the length of our image string
let length = image.len()
// 🚨 Error: no method named `len` found for enum
// `std::result::Result<std::string::String, std::io::Error>`

```

第一种更危险的方法是自己调用`unwrap()`函数:

```
let raw_image = read_to_string("/assets/" + req.path_to_image_asset).unwrap()

```

🚨但是这不太安全！如果你试图调用`unwrap`而`read_to_string`返回某种错误，整个程序将会因为所谓的[恐慌](https://doc.rust-lang.org/std/macro.panic.html)而崩溃。记住，Rust 没有`try...catch`，所以这可能是一个非常棘手的问题。

第二种也是更安全的方法是通过模式匹配来解开我们的结果。让我们用一些澄清性的评论来回顾一下前面的内容:

```
match read_to_string("/assets/" + req.path_to_image_asset) {
  // check whether our result is "Ok," a subtype of Result that
  // contains a value of type "string"
  Result::Ok(raw_image) => {
    // here, we can access the string inside that wrapper!
    // this means we're safe to pass that raw_image to our filter fn...
    let sepia_image = apply_sepia_filter(raw_image)
    // and send the result
    res.send(sepia_image)
  }
  // otherwise, check whether our result is an "Err," another subtype
  // that wraps an I/O error. 
  Result::Err(_) => res.status(400)
}

```

注意我们在最后的`Err`中使用了下划线`_`。这是“我们不关心这个值”的一种老套说法，因为我们总是返回一个状态`400`。如果我们真的关心那个错误对象，我们可以像我们的`raw_image`一样获取它，甚至通过异常类型做另一层模式匹配。

### 为什么模式匹配是处理异常的更安全的方法

那么，为什么要处理像`Result`这样不方便的“包装器”呢？乍一看，这似乎很烦人，但从设计上看，它们确实很烦人，因为:

1.  无论何时出现错误，您都必须处理，用模式匹配定义成功和失败案例的行为。而且，如果你真的想得到结果并继续前进，你可以使用`unwrap()`选择加入不安全行为
2.  根据函数的返回类型，你总是知道函数*何时会*出错，这意味着不再有`try...catch`焦虑，也不再有简单的类型检查👍

## 如何在 Rust 中使用`null`

这又是 Rust 能解决的 JS 的一个毛角落。对于函数返回值，当我们需要考虑某种特殊或默认情况时，我们会用到`null`(或`undefined`)。当一些转换失败，一个对象或数组元素不存在，等等，我们可能会抛出一个`null`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

但是在这些上下文中，null 只是一个无名的异常！我们可能会接触到 JS 中的`null`返回值，因为`throw`出现异常感觉不安全或极端。我们想要的是一种引发异常的方法，但是没有错误类型或错误消息的麻烦，并且希望调用者使用一个`try...catch`。

拉斯特也意识到了这一点。因此，Rust 将`null`从语言中驱逐出去，并引入了[的`Option`包装器](https://doc.rust-lang.org/std/option/)。✨

假设我们有一个`get_waiter_comment`函数，它根据顾客留下的小费来给予顾客称赞。我们可以用这样的东西:

```
fn get_waiter_comment(tip_percentage: u32) -> Option<String> {
    if tip_percentage <= 20 {
        None
    } else {
        Some("That's one generous tip!".to_string())
    }
}

```

当我们不想要称赞时，我们可以返回一个空字符串。但是通过使用`Option`(很像使用`null`)，更容易判断我们是否有赞美要展示。看看这个`match`语句的可读性如何:

```
match get_waiter_comment(tip) {
  Some(comment) => tell_customer(comment)
  None => walk_away_from_table()
}

```

### 何时使用`Option`与`Result`

`Result`和`Option`之间的界限很模糊。我们可以很容易地将前面的例子重构为:

```
fn get_waiter_comment(tip_percentage: u32) -> Result<String> {
    if tip_percentage <= 20 {
        Err(SOME_ERROR_TYPE)
    } else {
        Result("That's one generous tip!".to_string())
    }
}
...
match get_waiter_comment(tip) {
  Ok(comment) => tell_customer(comment)
  Err(_) => walk_away_from_table()
}

```

唯一的区别是我们需要为我们的`Err`案例提供一些错误对象，这可能是一个麻烦，因为被调用者需要提供一个错误`type /`消息来使用，调用者需要检查错误消息是否真的值得阅读和匹配。

但是在这里，很明显错误消息不会给我们的`get_waiter_comment`函数增加多少价值。这就是为什么我通常会选择`Option`，直到我有一个很好的理由换成`Result`类型。不过，决定权在你！

## 总结(没有双关语)

Rust 处理`exception`和`null`的方法是类型安全的巨大胜利。有了表达式、模式匹配和包装器类型的概念，我希望您已经准备好在整个应用程序中安全地处理错误！

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。