# Rust - LogRocket 博客中的异步 I/O 和异步/等待包

> 原文：<https://blog.logrocket.com/asynchronous-i-o-and-async-await-packages-in-rust/>

Rust 的异步故事已经进行了一段时间了。但是自从去年年底[稳定了`async/await`语法](https://docs.rs/futures/0.3.6/futures/stream/trait.StreamExt.html)，事情真的开始变得更好了。

Async 在 Rust 中的工作方式不同于其他语言，比如 JavaScript。一个主要的区别是 Rust 中的异步需要一个执行器来工作。甚至在标准图书馆里都找不到。

在本教程中，我们将研究三个 Rust 异步包，评估它们的生产就绪性，并演示如何构建快速、可靠和高度并发的应用程序。

1.  托基奥
2.  [异步标准](#asyncstd)
3.  [期货](#futures)

## Rust 中的异步是如何工作的？

在其核心，async/await in Rust 是建立在 [`Futures`](https://doc.rust-lang.org/std/future/trait.Future.html) 之上的。如果你来自 JavaScript，你可以把未来想象成承诺:它们是还没有完成计算的价值。然而，与承诺不同的是，期货不会有任何进展，除非它们被明确地[调查](https://doc.rust-lang.org/std/future/trait.Future.html#tymethod.poll)。这就是遗嘱执行人出现的地方。执行器是一个运行时，当它们准备好取得进展时，它通过轮询来为您管理未来。就像我前面提到的，标准库不包括。因此，为了开始使用异步 Rust，我们需要使用一个外部执行器箱。

您可能认为这增加了一层不必要的复杂性，但它允许我们有更多的控制，并使用一个根据我们的应用程序的需求进行调整的执行器，而不是像大多数其他语言一样被迫使用单一的解决方案。

对于主要是 IO 绑定的应用程序，异步被用作操作系统线程的替代。与操作系统线程不同，孕育未来的成本很低，而且你可以同时运行数百万个线程。

让我们看看一些顶级异步板条箱是否生锈。

## 1.时男

Tokio 是最受欢迎的处理异步锈的板条箱。除了执行器，Tokio 还提供了许多标准库类型的异步版本。

这个机箱中的大部分功能都是您需要启用的可选功能。当不需要这些特性时，这有助于减少编译时间和二进制文件的大小。为了便于演示，我们将使用`"full"`特性来启用它们。

```
[dependencies]
tokio = { version = "0.3", features = ["full"] }

```

现在，您可以生成一个 Tokio 运行时，并给它一个运行的未来。

```
fn main() {
    tokio::runtime::Runtime::new().unwrap().block_on(async {
        println!("Hello world");
    })
}

```

这里，我们的应用程序代码可以放在`block_on`方法的异步块中。这个样板文件可以用一个宏来代替，这个宏允许您使用一个异步 main 函数。

以下是通常引导 Tokio 运行时的方式。

```
#[tokio::main]
async fn main() {
    println!("Hello world");
}

```

由于现在这是一个异步函数，我们也应该尝试使用`await`。为了简单起见，让我们使用 [`sleep`](https://docs.rs/tokio/0.3.0/tokio/time/fn.sleep.html) 返回一个未来以供等待。

```
use std::time::Duration;
use tokio::time::sleep;

#[tokio::main]
async fn main() {
    let (v1, v2, v3) = tokio::join!(
        async {
            sleep(Duration::from_millis(1500)).await;
            println!("Value 1 ready");
            "Value 1"
        },
        async {
            sleep(Duration::from_millis(2800)).await;
            println!("Value 2 ready");
            "Value 2"
        },
        async {
            sleep(Duration::from_millis(600)).await;
            println!("Value 3 ready");
            "Value 3"
        },
    );

    assert_eq!(v1, "Value 1");
    assert_eq!(v2, "Value 2");
    assert_eq!(v3, "Value 3");
}

```

这里，我们使用了`tokio::join!`来同时运行多个期货。它将等待所有的未来完成，并返回元组中每个的结果。在这种情况下，第三个未来将首先完成，然后是第一个，最后是第二个。

### 阻塞码

使用`sleep`也为我们提供了一个很好的机会，来看看在不使用 await 的情况下在异步上下文中阻塞会发生什么。

把第二个未来的睡眠改成这样:

```
std::thread::sleep(Duration::from_millis(2800));

```

这里我们使用了标准库`sleep`，它不返回未来值，而是阻塞整个线程。如果你现在运行这个，你会发现只要第二个未来受阻，所有的未来都不会有进展。由于我们没有使用 await，所以第二个未来无法知道让步并将控制权交还给`tokio`运行时。如果运行时被阻塞，它的所有未来也会被阻塞。

幸运的是，东京支持我们。 [`tokio::task`](https://docs.rs/tokio/0.3.0/tokio/task/index.html) 模块包含绿色线程的实现，类似于 [Go 的 goroutines](https://docs.rs/tokio/0.3.0/tokio/task/index.html) 。有了`[spawn_blocking](https://docs.rs/tokio/0.3.0/tokio/task/index.html#spawn_blocking)`，你可以让 Tokio 运行时在一个专用的线程池中运行阻塞代码，让其他的未来继续前进。

如果你用这个来阻挡`sleep`，你的第二个未来会是这样的:

```
async {
    tokio::task::spawn_blocking(|| {
        std::thread::sleep(Duration::from_millis(2800));
    })
    .await
    .unwrap();
    println!("Value 2 ready");
    "Value 2"
},

```

现在，代码再次按预期运行。当然，这是一个人为的例子，但是阻塞睡眠可以用任何 CPU 密集型的阻塞代码来代替，Tokio 会处理剩下的部分。

### Tokio 任务

虽然您可以通过在 Tokio 的线程池中生成阻塞代码来做得很好，但是为了充分利用 futures 和 async/await，让我们从上到下使用异步代码。我们将使用 [`tokio::task::spawn`](https://docs.rs/tokio/0.3.0/tokio/task/fn.spawn.html) (一个异步版本的 [`std::thread::spawn`](https://doc.rust-lang.org/std/thread/fn.spawn.html) )将期货生成到它们自己的后台任务中。我们将通过创建一个异步工作池来测试这一点，该工作池可以接收在后台运行的作业。

首先使用枚举定义作业列表。

```
#[derive(Debug)]
enum Message {
    SendWelcomeEmail { to: String },
    DownloadVideo { id: usize },
    GenerateReport,
    Terminate,
}

```

这里要注意的主要是`Message`的`Terminate`变体，它告诉工人在不再需要工作时停止处理工作。我们还派生了`Debug`,这样我们可以稍后打印出消息。

接下来，我们需要使用 Tokio 提供的其中一个通道与工人进行通信。

```
use std::sync::Arc;
use tokio::sync::mpsc::unbounded_channel;
use tokio::sync::Mutex;

#[tokio::main]
async fn main() {
    let (sender, receiver) = unbounded_channel();
    let receiver = Arc::new(Mutex::new(receiver));
}

```

这里，我们使用一个无界通道，它是标准库中 MPSC 通道的异步替代。在生产中，我强烈推荐使用 [`tokio::sync::mpsc::channel`](https://docs.rs/tokio/0.3.0/tokio/sync/mpsc/fn.channel.html) ，这是一个有限大小的通道，当应用程序处于负载下时，它会提供背压，以防止应用程序不堪重负。接收者还被包装在一个 [`Arc`](https://doc.rust-lang.org/std/sync/struct.Arc.html) 和一个 [Tokio `Mutex`](https://docs.rs/tokio/0.3.0/tokio/sync/struct.Mutex.html) 中，因为它将在多个工作者之间共享。这还不能编译，因为它不能推断出我们要通过通道发送的值的类型。

现在咱们可以催生几个工人了。

```
let size = 5;
let mut workers = Vec::with_capacity(size);

for id in 0..size {
    let receiver = Arc::clone(&receiver);
    let worker = tokio::spawn(async move { /* ... */ });
    workers.push(worker);
}

```

这产生了一些工人，并将他们的 [`JoinHandle`](https://docs.rs/tokio/0.3.0/tokio/task/struct.JoinHandle.html) 推到一个`Vec`上。现在剩下的就是填写消息处理程序了。

```
use std::time::Duration;
use tokio::time::sleep;

// ...

let worker = tokio::spawn(async move {
    loop {
        let message = receiver
            .lock()
            .await
            .recv()
            .await
            .unwrap_or_else(|| Message::Terminate);
        println!("Worker {}: {:?}", id, message);
        match message {
            Message::Terminate => break,
            _ => sleep(Duration::from_secs(1 + id as u64)).await,
        }
    }
});

```

这就永远循环下去，打印出每一条信息。如果它收到一个`Terminate`消息，它就中断循环。对于任何其他消息，它都会休眠。在您自己的应用程序中，您将匹配您想要处理的每条消息，并在这里放置一些真正的逻辑。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

小心互斥保护的生命周期。不使用`while let Some(message) = receiver.lock()...`的原因是，直到 while 循环的内容执行之后，它才会放弃互斥保护。这意味着在您处理消息时互斥体将被锁定，并且一次只能有一个工作者工作。

为了允许工人干净地终止，在`main`函数完成之前向所有工人发送一个`Terminate`消息是很重要的。如果不这样做，作业将被中断，并可能处于不良状态。

```
for _ in &workers {
    let _ = sender.send(Message::Terminate);
}
for worker in workers {
    let _ = worker.await;
}

```

第一个循环发送消息的次数与工作线程的数量一样多。然后第二个等待每个工人完成。我们在这里使用`let _ = ...`来丢弃结果。这种代码应该放在您不再需要工人的地方。在我们的例子中，那是在`main`的最后。

注意这里我们没有在`send`方法后使用`await`。这是因为我们使用了一个无限的渠道，它永远不会阻止一个发送者。有界信道可以阻止发送者，所以你需要在发送时使用`await`。你也可以用 [`try_send`](https://docs.rs/tokio/0.3.0/tokio/sync/mpsc/struct.Sender.html) 。

在这个清理代码上面，让我们把工作发送给工人。

```
sender.send(Message::DownloadVideo { id: 10 }).unwrap();
sender.send(Message::GenerateReport).unwrap();
sender.send(Message::SendWelcomeEmail { to: "[email protected]".into() }).unwrap();
sender.send(Message::DownloadVideo { id: 25 }).unwrap();

```

在这个例子中，我们使用了通道，一个互斥体，并产生了异步任务。如果您已经使用了标准的库等价物，那么其中的每一个都有可识别的 API。

我强烈认为 Tokio 已经为生产做好了准备。也就是说，当前版本被视为即将发布的 1.0 版本的测试版。一旦发布，Tokio 团队将致力于维护它至少五年。我发现整个 [Tokio stack](https://tokio.rs/) 的质量也很高，使用起来也很愉快。

## 2.异步标准

顾名思义， [async-std](https://async.rs/) 试图成为 Rust 标准库的异步版本。它的目标与它的主要竞争对手 Tokio 相似，但不太受欢迎，因此在野外也不太经得起考验。

虽然 Tokio 和 async-std 的 API 不完全相同，但它们非常相似，大多数概念都可以在两者之间转换。同样，如果您有使用 Rust 标准库的经验，那么这两个库应该已经很熟悉了。对于仍然好奇的人，我已经在 [GitHub](https://github.com/joshua-cooper/async-rust-example) 上发布了我们所做的最后一个例子的代码，以及一个 async-std 端口。

最重要的是要知道 Tokio 和 async-std 并不是 100%兼容的。构建在其中一个之上的库可能无法在另一个的运行时中工作。

目前，核心语言和标准库只提供了最低限度的异步/等待支持；其余的是由社区写的板条箱。随着时间的推移，当找到正确的抽象时，一些基础部分可能会合并到标准库中，更多的库可能会成为运行时不可知的。在此之前，一定要检查您使用的任何异步库是否依赖于特定的运行时。

如果稳定性是重中之重，我会推荐 Tokio 而不是 async-std，因为它更成熟，并且在它的基础上构建了更多的库。对于任何探索 Rust async 的人来说，尝试 async-std 仍然是一个好主意。谁知道呢，也许你会发现它符合你的需要。

## 3.未来

[`futures-rs`机箱](https://crates.io/crates/futures)为 Rust 中的 async 提供了许多共享的基础组件。Tokio 和 async-std 都将期货箱的一部分用于共享特征等方面。事实上，`std::future::Future`最初是从这个板条箱中取出的，其他零件可能会在某个时候被移到标准库中。

这个机箱包含的一个很好的例子是[流模块](https://docs.rs/futures/0.3.6/futures/stream/index.html)。流本质上是异步迭代器，这个模块提供了 [`Stream`](https://docs.rs/futures/0.3.6/futures/stream/trait.Stream.html) 和 [`StreamExt`](https://docs.rs/futures/0.3.6/futures/stream/trait.StreamExt.html) 特征，其中包括类似迭代器可用的组合函数。

## 结论

在我看来，async Rust 处于一个很好的位置，尽管有一些关于运行时兼容性的问题。Tokio 和 async-std 都是通用异步运行时，为标准库类型提供异步替代。

对于一个生产应用程序，我目前推荐 Tokio，因为它很成熟，而且基于它构建了大量的板条箱。对于寻找包含电池的框架的人来说，Rust 还不是一个好的选择。但对于那些喜欢用更小、更模块化的部分来构建应用程序的人来说，Tokio 及其周围的生态系统非常出色。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。