# Rust 中的异步实用指南

> 原文：<https://blog.logrocket.com/a-practical-guide-to-async-in-rust/>

编程语言有不同的方法来表示异步操作。如果你曾经在 JavaScript 中使用过 [async/await](https://blog.logrocket.com/async-await-in-typescript/) ，Rust 处理并发的方式应该很熟悉。关键词是相同的，基本模型是相似的，除了在 Rust 中，延迟计算被称为期货而不是承诺。不太熟悉的是，您需要选择一个运行时来实际运行您的异步代码。

Rust 的目标是一切，从裸机、嵌入式设备到运行在高级操作系统上的程序，像 C++一样，专注于零成本抽象。这会影响标准库中包含和不包含的内容。

一旦你掌握了诀窍，你会发现在 Rust 中开始使用 async 并不困难。如果你是第一次用 Rust 编写异步程序，或者只是需要使用异步库而不知道从哪里开始，那么这篇指南就是为你准备的。我会尽量让你尽快开始，同时向你介绍所有你应该知道的要点。

## 必需品；要素

‌An 异步应用程序应该从 Rusts 生态系统中获取至少两箱数据:

1.  `futures`，一个存在于`rust-lang`仓库中的官方锈箱
2.  您选择的运行时，如 [Tokio](https://crates.io/crates/tokio) 、 [async_std](https://crates.io/crates/async-std) 、 [smol](https://crates.io/crates/smol) 等。

有些人不想引入不必要的依赖项，但是这些依赖项和`chrono`或`log`箱子一样重要。唯一的区别是，它们关注的是异步。

出于本教程的目的，我们将使用`tokio`。您应该至少了解一个运行时，并首先关注它。您可以稍后查看其他运行时。您可能会注意到它们在功能方面有很多共同点，即使 API 或实现可能有所不同。
‌
为了在 Rust 中使用 async 时提供最佳体验，您应该启用一些`features`。你的`Cargo.toml`的`dependencies`部分应该是这样的:

```
[dependencies]
futures = { version = "0.3.*" }
tokio = {version = "0.2.*", features = ["full"] }

```

您的`main.rs`应该如下所示。

```
use futures::prelude::*;
use tokio::prelude::*;

fn main() {
    todo!();
}

```

## 运行时间

‌Contrary 你可能已经习惯了其他语言，Rust 没有内置的运行时。我们不会在这里讨论其利弊，但是您需要做出选择，并将其作为一个依赖项。

有些库要求您使用特定的运行时，因为它们依赖运行时内部机制来提供可靠的 API 供您使用，或者将它们自己的 API 包装在现有的运行时周围。一个例子是`actix_web` web 框架，它用自己的 API 包装了`tokio`。

大多数时候，你可以选择任何你想要的运行时。但是，无论您选择哪个运行时，在启动 coding:‌之前，有三个基本操作您应该弄清楚

1.  如何启动运行时
2.  如何生出一只`Future`
3.  如何产生阻塞或 CPU 密集型任务

如果你知道这些基本操作，‌You 可以完成大多数任务。以`Tokio`为例，让我们来看一下这三个例子。

### 1.启动运行时

您可以显式实例化运行时，并在其上生成未来。你所创造的未来将是你的程序的主要入口，所以把它想象成一个异步的`main`函数。

```
async fn app() {
    todo!()
}

fn main() {
    let mut rt = tokio::runtime::Runtime::new().unwrap();
    let future = app();
    rt.block_on(future);
}

```

你也可以使用较短的版本。基本上做同样的事情。

```
#[tokio::main]
async fn main() {

}

```

### ‌2.在运行时生成一个`Future`

当您想要并发运行未来(即，同时进行的任务)时，这很方便。

```
use tokio::task;

async fn our_async_program() {
    todo!();
}

async fn app() {
    let concurrent_future = task::spawn(our_async_program());
    todo!()
}

```

### ‌3.产生阻塞或 CPU 密集型任务

这是编写异步代码时的一个常见问题。如果你想利用运行时并发运行你的代码，你应该避免在`Futures`中阻塞或运行 CPU 密集型代码。你用 async Rust 编写的大部分代码实际上会在一个`Future`中执行，这一点很重要。
‌
大多数运行时都提供了一种将这项工作卸载到不同线程的方法，这有助于避免阻塞真正驱动您的未来完成的线程。在`tokio`，你可以通过`task::spawn_blocking`做到这一点。
‌
使用我们的例子，我们可以做以下事情。

```
use tokio::task;

fn fib_cpu_intensive(n: u32) -> u32 {
    match n {
        0 => 0,
        1 => 1,
        n => fib_cpu_intensive(n - 1) + fib_cpu_intensive(n - 2),
    }
}

async fn app() {
    let threadpool_future = task::spawn_blocking(||fib_cpu_intensive(30));
    todo!()
}

```

每个运行时都有稍微不同的 API 来完成这些任务，但是它们都支持它们。如果你知道要寻找什么，你会更容易开始。

## 异步项目启动模板

此时，我们可以像编写普通同步代码一样轻松地在 Rust 中使用 async，但是让我们再深入一点，讨论一些以后可能会派上用场的东西。

我将提供一个模板，您可以使用它来启动您知道需要编写异步代码的应用程序。我喜欢显式实例化运行时，这就是我们将在下面的模板中做的。

```
use futures::prelude::*;
use tokio::prelude::*;
use tokio::task;
use log::*;

// Just a generic Result type to ease error handling for us. Errors in multithreaded
// async contexts needs some extra restrictions
type Result<T> = std::result::Result<T, Box<dyn std::error::Error + Send + Sync>>;

async fn app() -> Result<()> {
    // I treat this as the `main` function of the async part of our program. 
    todo!()
}

fn main() {
    env_logger::init();
    let mut rt = tokio::runtime::Runtime::new().unwrap();

    match rt.block_on(app()) {
        Ok(_) => info!("Done"),
        Err(e) => error!("An error ocurred: {}", e),
    };
}

```

‌I've 又拉进了几个板条箱，包括:

*   运行时(`tokio`)
*   Rust 的日志门面，`log`
*   一个伐木工(`env_logger`

‌Our 现在看起来是这样的:

```
[dependencies]
futures = { version = "0.3.*"}
tokio = {version = "0.2.*", features = ["full"] }
log = "0.4.*"
env_logger = "0.7.*"

```

记住`env_logger`依赖于`RUST_LOG`环境变量来确定日志级别。

我的大多数异步应用程序项目都是以上面提到的一个`main.rs`和一个`Cargo.toml`开始的。随着项目的发展，如果有必要，我可以添加更好的错误处理或日志记录。处理应用程序中错误的一个流行的板条箱是[。](https://crates.io/crates/anyhow) [async-log](https://crates.io/crates/async-log) 对于改进异步上下文中的日志记录很有用。

在整个教程中，我们将在所有代码中使用这个基本模板。但是，您可能会注意到，我对日志输出进行了一些调整，以更好地适应我想要显示的内容。如果您想继续，您应该将`main.rs`中的日志初始化更改为以下内容。

```
let start = std::time::Instant::now();
env_logger::Builder::from_default_env().format(move |buf, rec| {
    let t = start.elapsed().as_secs_f32();
    writeln!(buf, "{:.03} [{}] - {}", t, rec.level(),rec.args())
}).init();

```

## Rust 中的异步函数

Rust 中的‌Async 函数与你所熟悉的有些不同。当你学习 Rust 时，你可能会注意到它非常精确地描述了函数的参数类型和函数返回的类型。
‌
异步函数的一个重要区别是:所有的返回类型都被“包装”在一个`Future`中。

您可能会阅读 Rust 中关于`Futures`的文档，并认为您的异步函数应该是这样的:

```
async fn our_async_program() -> impl Future<Output = Result<String>> {
    future::ok("Hello world".to_string()).await
}
```

‌This 错了！如果你这样做，你想多了。一个`async`函数已经包装了返回类型，所以你可以用你习惯的方式编写函数。
‌
这是你实际想要的:

```
async fn our_async_program() -> Result<String> {
    future::ok("Hello world".to_string()).await
}
```

```
`future::ok`是我们从`futures`箱中得到的方便方法之一。它包含一个立即返回`ready`的未来值。
这可能看起来有点奇怪，因为 Rust 在声明正确类型时通常非常严格，但这实际上是一个巨大的人体工程学提升，因为它自动包装了来自我们的`async`函数的返回类型。
 ‌ 
你会经常看到使用异步块的例子，比如`async { ... }`。这些函数类似于异步函数，因为它们返回一种特殊的未来，包装我们从闭包返回的任何东西。这些闭包的一个缺点是，你必须[跳过一些关卡](https://rust-lang.github.io/async-book/07_workarounds/03_err_in_async_blocks.html)通过`?`返回错误。返回类型可能很难推理，这会在您开始编写异步 Rust 时造成一些不必要的混乱。
我的建议是，如果可以的话，使用异步函数，尤其是如果你打算从未来返回任何东西——至少在你熟悉不同的返回类型和 Rust 中异步的工作方式之前。
发出 web 请求
铁锈期货是懒惰的。默认情况下，在第一次轮询之前，它们不会做任何事情。当你展望未来时，它会被轮询。
例如，如果您在程序开始时调用一个返回未来值的函数，但在程序结束前没有调用它，那么在您到达等待它的点(最后)之前，实际的请求不会发出。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们把到目前为止所学的东西付诸实践。
 [Reqwest](https://crates.io/crates/reqwest) 是一个用于创建 web 请求的流行客户端库。我们将把它与[slow ly endpoint](http://slowwly.robertomurray.co.uk/)一起使用，它使我们能够为服务器响应定义一个延迟，并在我们的并发性方面给我们更多的确定性，使它更容易推理。
 ‌ 
让我们通过在`[dependencies]`部分添加`reqwest = "0.10.*"`来将`reqwest`添加到我们的`Cargo.toml`中。
 ‌ 
创建几个请求，看看会发生什么。

```
fn slowwly(delay_ms: u32) -> reqwest::Url {
    let url = format!(
    "http://slowwly.robertomurray.co.uk/delay/{}/url/http://www.google.co.uk", 
    delay_ms,
    );
    reqwest::Url::parse(&url).unwrap()
}

async fn app() -> Result<()> {
    info!("Starting program!");
    let _resp1 = reqwest::get(slowwly(1000)).await?;
    info!("Got response 1");
    let _resp2 = reqwest::get(slowwly(1000)).await?;
    info!("Got response 2");
    Ok(())
}

```

‌Running 这给了我们以下的输出。

```
1.264 [INFO] - Got response 1
2.467 [INFO] - Got response 2
2.468 [INFO] - Done

```

‌The 时间以秒/毫秒为单位。在 1.246，我们得到了来自端点的第一个响应(记住，我们在第一个请求中要求延迟一秒)。大约一秒钟后，在 2.467，我们得到了第二个回应。整个程序运行时间为 2.468 秒。
我们的程序正在运行，但这并不是真正的并发，对吗？老实说，比复杂的同步程序好不了多少。
 ‌ 
让我们实际利用我们的异步运行时，并发运行请求。

```
async fn request(n: usize) -> Result<()> {
    reqwest::get(slowwly(1000)).await?;
    info!("Got response {}", n);
    Ok(())
}

async fn app() -> Result<()> {
    let resp1 = task::spawn(request(1));
    let resp2 = task::spawn(request(2));

    let _ = resp1.await??;
    let _ = resp2.await??;

    Ok(())
}

```

此时，我们应该将我们的请求重构到一个单独的函数，原因有两个:

1.  我们希望在从请求中获得结果后进行日志记录，因此我们需要将请求和日志记录都封装在一个任务中，并生成到运行时中等待
2.  另一种方法是使用`async { }`块。虽然可以为我们的`Result<()>`指定一个返回类型，但是[很难处理](https://rust-lang.github.io/async-book/07_workarounds/03_err_in_async_blocks.html)，所以为了本教程的目的，我们将避免这样做

‌If 我们运行我们的代码，我们应该得到这个:

```
1.247 [INFO] - Got response 2
1.256 [INFO] - Got response 1
1.257 [INFO] - Done

```

看起来好多了。我们的第二个请求在 1.247 结束，第一个请求在 1.256 结束。整个程序用时 1.257 秒，比第一个例子用时的一半还少。
使用`spawn`使我们能够并发运行我们的请求。由于 Tokio 默认为多线程运行时，以这种方式产生的任务也可以在不同的内核上并行运行。
CPU 密集型任务
既然我们已经让程序并发运行，我们可以将一些 CPU 密集型任务与一些 I/O 相关的任务结合起来，创建一个更复杂的场景。
让我们稍微扩展一下我们的示例，提出 10 个请求，并在收到响应时对每个响应进行一些分析。看到从响应中得到的字节中 1 和 0 的比率，我们非常兴奋，所以我们将返回 1 和 0 的计数，并在最后报告比率。

```
use futures::future::join_all;

// Now we want to both fetch some data and do some CPU intensive analysis on it
async fn get_and_analyze(n: usize) -> Result<(u64, u64)> {
    let response: reqwest::Response = reqwest::get(slowwly(1000)).await?;
    info!("Dataset {}", n);

    // we get the body of the request
    let txt = response.text().await?;

    // We send our analysis work to a thread where there is no runtime running
    // so we don't block the runtime by analyzing the data
    let res= task::spawn_blocking(move ||analyze(&txt)).await?;
    info!("Processed {}", n);
    Ok(res)
}

// Counting the number of ones and zeros in the bytes we get.
fn analyze(txt: &str) -> (u64, u64) {
    let txt = txt.as_bytes();
    // Let's spend as much time as we can and count them in two passes
    let ones = txt.iter().fold(0u64, |acc, b: &u8| acc + b.count_ones() as u64);
    let zeros = txt.iter().fold(0u64, |acc, b: &u8| acc + b.count_zeros() as u64);
    (ones, zeros)
}

async fn app() -> Result<()> {

    // This is new. We can collect futures in a collection. Nice to know!
    let mut futures = vec![];

    for i in 1..=10 {
        let fut = task::spawn(get_and_analyze(i));
        futures.push(fut);
    }

    let results = join_all(futures).await;

    let mut total_ones = 0;
    let mut total_zeros = 0;

    // Returning errors using `?` in iterators can be a bit difficult. Using a
    // simple for loop to inspect and work with our results can often be more
    // ergonomic
    for result in results {
        // `spawn_blocking` returns a `JoinResult` we need to unwrap first
        let ones_res: Result<(u64, u64)> = result?;
        let (ones, zeros) = ones_res?;
        total_ones += ones;
        total_zeros += zeros;
    }

    info!("Ratio of ones/zeros: {:.02}",total_ones as f64 / total_zeros as f64);
    Ok(())
}

```

需要注意一些事情:

*   `Futures`板条箱有许多方便的工具，包括`join_all`，它将一组期货视为一个单一的期货，并驱动它们全部完成，以及来自同一个板条箱的`[FuturesUnordered](https://docs.rs/futures/0.3.5/futures/stream/struct.FuturesUnordered.html)` API。
*   我们可以正常的收集期货
*   在编写异步代码时，可能会有很多错误。这是正常的，但有时会有点混乱。像`[rust-analyzer](https://github.com/rust-analyzer/rust-analyzer)`这样的工具可以帮助你跟踪返回了什么错误。一个错误处理箱，比如[总之](https://crates.io/crates/anyhow)也能帮助 here‌

让我们看看我们的程序输出了什么。

```
1.270 [INFO] - Dataset 7
1.275 [INFO] - Dataset 3
1.285 [INFO] - Dataset 2
1.285 [INFO] - Dataset 4
1.291 [INFO] - Dataset 9
1.297 [INFO] - Dataset 1
1.301 [INFO] - Dataset 5
1.308 [INFO] - Dataset 6
1.312 [INFO] - Dataset 8
1.322 [INFO] - Dataset 10
1.374 [INFO] - Processed 7
1.377 [INFO] - Processed 3
1.378 [INFO] - Processed 4
1.378 [INFO] - Processed 2
1.380 [INFO] - Processed 9
1.384 [INFO] - Processed 1
1.385 [INFO] - Processed 5
1.391 [INFO] - Processed 8
1.391 [INFO] - Processed 6
1.397 [INFO] - Processed 10
1.397 [INFO] - Ratio of ones/zeros: 0.95
1.397 [INFO] - Done

```

‌Since:我们立即发送所有数据集，每个数据集需要一秒钟返回，我们所有的响应几乎同时作为数据集返回。然后，每个响应都被发送到线程池进行分析。
我们可以看到，数据集不一定按照它们进来的顺序完成处理，因为它们是在单独的 CPU 内核上处理的。
产卵还是不产卵？
既然我们应该产生未来任务、CPU 受限任务和阻塞任务，那么我们可以在异步函数中写些什么呢？
通常，您可以编写大部分代码，而不必太担心这一点，但是阻塞和 CPU 密集型任务应该让您停下来，考虑是否应该重构代码的这一部分，以便它可以在为处理这些任务而设计的线程池上生成。
如果您遇到可能需要下列模块之一的情况，那么您应该检查您的运行时是否有一个异步替代模块来执行您想要执行的任务。

*   `std::sync`
*   `std::thread`
*   `std::fs`
*   `std::net`

如果您的运行时没有对等的，您可以使用`spawn_blocking`并在线程池中执行操作，就像您处理 CPU 密集型任务一样，并使用`await`处理结果。
一般来说，调用您的操作系统并可能导致操作系统暂停您正在调用的线程的函数对并发性尤其有害，因为您也将暂停执行器。
 ‌ 
使用`thread::sleep`是一个典型的例子，因为这个原因，你应该在异步上下文中避免使用这个函数。使用`sleep`来延迟操作很有吸引力，但这不是一个好主意，因为操作系统会在整个过程中暂停调用线程。大多数运行时都有办法导致延迟或睡眠一个不阻塞执行器的任务，从而禁用该线程上运行的所有其他任务。
对于受 CPU 限制的任务，界限要模糊得多。总的来说，我会鼓励你不要对此疑神疑鬼。请记住，如果您决定减少对`spawn_blocking`的调用次数，这比反过来更容易重构。
当有疑问时，产卵。
到目前为止，您应该已经准备好编写 async Rust 了，我希望您会发现开始下一个 async 项目会更容易。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

. 

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。

```