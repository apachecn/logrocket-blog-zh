# 带横梁的铁锈中的并行编程

> 原文：<https://blog.logrocket.com/concurrent-programming-rust-crossbeam/>

在本文中，我们将看看[横梁](https://github.com/crossbeam-rs/crossbeam)机箱，它为 Rust 中的并发编程提供了工具。它被 Rust 生态系统中的许多库和框架广泛使用——只要并发编程在它们的领域之内。

Aaron Turon 在 2015 年发布了这篇精彩的博客文章，介绍了 Crossbeam，并对 Rust 无锁编程带来的挑战提出了一些很好的见解；如果你有时间，我绝对推荐你读一读。

事不宜迟，让我们深入探讨 Rust 中使用横梁的并发编程。

*向前跳转:*

## Rust 中的并发编程

TL；这里的 DR 是，用 Rust 构建高效的无锁数据结构是可能的，但是你实际上需要构建一个内存回收机制——很像垃圾收集器。

在上面博客中概述的案例中，Turon 实现了一个基于纪元的内存管理 API，它可以作为在 Rust 中构建无锁数据结构的基础

这种基于纪元的内存回收机制也是库的一部分——如果你想了解更多，可以参考[中的](https://docs.rs/crossbeam/latest/crossbeam/epoch/index.html)。

如果你不太熟悉无锁编程，你可以看看这个很棒的[介绍](https://preshing.com/20120612/an-introduction-to-lock-free-programming/)。

在这篇文章中，我们将看看横梁的 API 的一部分，并实现一些简单的例子，展示如何使用它，为什么它是一个伟大的工具。

如果您有兴趣查看整个 API 以更深入地了解 Crossbeam 中不同的并发编程工具，您也可以查看[文档](https://docs.rs/crossbeam/latest/crossbeam/index.html)。

我们开始吧！

## 设置

接下来，您所需要的只是一个最新的 Rust 安装(在撰写本文时最新的版本是 1.64.0)。

首先，创建一个新的 Rust 项目:

```
cargo new rust-crossbeam-example
cd rust-crossbeam-example 
```

接下来，编辑`Cargo.toml`文件并添加您需要的依赖项:

```
[dependencies]
crossbeam = "0.8.2"
rand = "0.8.5" 
```

> **注意，**横梁和`rand`都是我们在接下来的例子中需要的🙂

## `AtomicCell`举例

我们将从`AtomicCell`开始，Rust 的`[Cell](https://doc.rust-lang.org/nightly/core/cell/struct.Cell.html)`的线程安全实现。

`Cell`是一个可变的内存位置——就像`AtomicCell`一样——不同之处在于它是线程安全的，因此多个线程可以同时访问和改变内存位置，而不会发生数据竞争。

我们可以通过生成线程来测试这一点——在某些情况下，我们还可以加载并打印`AtomicCell`中的值，而在其他情况下，我们可以增加并打印它。一旦线程完成，结果总是需要相同的。

首先，让我们定义一个`run_thread`助手，它接受一个包含`AtomicCell`的`Arc`(一个线程安全、引用计数的智能指针)。我们的可变内存位置，线程的数量，以及它是否应该存储一些东西应该被定义。

```
fn run_thread(val: Arc<AtomicCell<u32>>, num: u32, store: bool) -> thread::JoinHandle<()> {
    thread::spawn(move || {
        if store {
            val.fetch_add(1);
        }
        println!("Hello from thread {}! value: {}", num, val.load());
    })
}

```

这样，我们现在可以创建我们的`AtomicCell`并用数字`12`初始化它。然后，我们将它放入一个`Arc`，这样我们可以在线程之间共享它，使用`run_thread`生成线程，然后等待线程完成。

```
fn main() {
    // AtomicCell Example
    println!("Starting AtomicCell example...");
    let atomic_value: AtomicCell<u32> = AtomicCell::new(12);
    let arc = Arc::new(atomic_value);

    let mut thread_handles_ac: Vec<thread::JoinHandle<()>> = Vec::new();
    for i in 1..10 {
        thread_handles_ac.push(run_thread(arc.clone(), i, i % 2 == 0));
    }

    thread_handles_ac
        .into_iter()
        .for_each(|th| th.join().expect("can't join thread"));

    println!("value after threads finished: {}", arc.load());
    println!("AtomicCell example finished!");
}

```

如果使用`cargo run`(重复)运行，结果将总是相同的:

```
Starting AtomicCell example...
Hello from thread 1! value: 12
Hello from thread 4! value: 13
Hello from thread 5! value: 13
Hello from thread 2! value: 14
Hello from thread 3! value: 14
Hello from thread 8! value: 15
Hello from thread 6! value: 16
Hello from thread 7! value: 16
Hello from thread 9! value: 16
value after threads finished: 16
AtomicCell example finished!

```

`AtomicCell`在应用程序代码中不应该经常使用，但是对于构建处理可变状态的并发编程原语来说，它是一个非常有用的工具。

## `ArrayQueue`举例

我们接下来要看的横梁 API 是`ArrayQueue`。

顾名思义，这是一个线程安全队列。特别是，它是一个有界的(即有限的缓冲区)、多生产者、多消费者的队列。

要查看它的运行情况，我们可以创建许多生产者和消费者线程，它们将值推入队列或从队列中取出值——最后，我们应该会得到一致的结果。

为了实现这一点，我们必须首先创建两个助手函数来创建生产者线程和消费者线程:

```
fn run_producer(q: Arc<ArrayQueue<u32>>, num: u32) -> thread::JoinHandle<()> {
    thread::spawn(move || {
        println!("Hello from producer thread {} - pushing...!", num);
        for _ in 0..20 {
            q.push(num).expect("pushing failed");
        }
    })
}

fn run_consumer(q: Arc<ArrayQueue<u32>>, num: u32) -> thread::JoinHandle<()> {
    thread::spawn(move || {
        println!("Hello from producer thread {} - popping!", num);
        for _ in 0..20 {
            q.pop();
        }
    })
}

```

我们将封装在`Arc`中的`ArrayQueue`传递给助手，并在其中启动一个线程，该线程在一个小循环中，将(生产者的)队列推入(消费者的)队列。

```
fn main() {
    // ArrayQueue Example
    println!("---------------------------------------");
    println!("Starting ArrayQueue example...");
    let q: ArrayQueue<u32> = ArrayQueue::new(100);
    let arc_q = Arc::new(q);

    let mut thread_handles_aq: Vec<thread::JoinHandle<()>> = Vec::new();

    for i in 1..5 {
        thread_handles_aq.push(run_producer(arc_q.clone(), i));
    }

    for i in 1..5 {
        thread_handles_aq.push(run_consumer(arc_q.clone(), i));
    }

    thread_handles_aq
        .into_iter()
        .for_each(|th| th.join().expect("can't join thread"));
    println!("values in q after threads finished: {}", arc_q.len());
    println!("ArrayQueue example finished!");
}

```

然后，在初始化有界队列之后，我们将它放入一个`Arc`，生成我们的生产者和消费者，然后等待线程完成。

> **注意，**我们将其限制为 100 个条目的缓冲区；这意味着如果队列已满，推入队列实际上会失败

如果我们检查结果，我们可以看到，无论顺序如何，结果都是一致的，队列实际上是线程安全的:

```
Starting ArrayQueue example...
Hello from producer thread 1 - pushing...!
Hello from producer thread 4 - pushing...!
Hello from producer thread 2 - pushing...!
Hello from producer thread 2 - popping!
Hello from producer thread 3 - pushing...!
Hello from producer thread 1 - popping!
Hello from producer thread 4 - popping!
Hello from producer thread 3 - popping!
values in q after threads finished: 0
ArrayQueue example finished!

```

`ArrayQueue`，类似于`AtomicCell`，是在[并发](https://blog.logrocket.com/deep-dive-concurrency-rust-programming-language/)的抽象中最有用的东西，而不是实际的应用程序代码。

如果你有一个用例，你需要一个线程安全的队列数据结构，横梁可以满足你！还有另外两个队列实现值得注意:`deque`和`SegQueue`，你可以在这里查看[。](https://docs.rs/crossbeam/latest/crossbeam/index.html#data-structures)

## 渠道示例

通道，这个概念你可能从其他语言如 Go 中知道，在 Crossbeam 中是以多生产者、多消费者的方式实现的。

通道通常用于线程间通信，或者在 Go 的情况下，用于跨线程通信。

例如，通过通道，您可以实现一个 CSP——为协调并发流程而对顺序流程进行通信。

为了看到横梁通道的运行，我们将再次产生生产者和消费者线程。

然而，这一次，生产者将各自向通道发送 1000 个值，消费者将简单地从通道中获取值并处理它们——如果没有发送者，所有线程都将完成。

为此，我们需要编写两个助手函数来创建生产者和消费者线程:

```
fn run_producer_chan(s: Sender<u32>, num: u32) -> thread::JoinHandle<()> {
    thread::spawn(move || {
        println!("Hello from producer thread {} - pushing...!", num);
        for _ in 0..1000 {
            s.send(num).expect("send failed");
        }
    })
}

fn run_consumer_chan(r: Receiver<u32>, num: u32) -> thread::JoinHandle<()> {
    thread::spawn(move || {
        let mut i = 0;
        println!("Hello from producer thread {} - popping!", num);
        loop {
            if let Err(_) = r.recv() {
                println!(
                    "last sender dropped - stopping consumer thread, messages received: {}",
                    i
                );
                break;
            }
            i += 1;
        }
    })
}

```

与上面类似，生产者线程只是将值推入通道。但是，在一个无限循环中，使用者线程尝试从通道接收消息，如果它得到一个错误(如果所有发送者都被丢弃，就会发生这种情况)，那么它将打印该线程已处理的消息数。

为了将这些放在一起，我们创建了一个无界的`channel`，返回一个`Sender`和一个`Receiver`，它们可以被克隆并在线程之间共享(也有缓冲区大小有限的有界通道)。然后，我们产生了我们的生产者和消费者，并让其运行。

我们还用`drop(s)`去掉了最初的`Sender`。因为当所有的`Senders`都被丢弃时，我们依赖于进入错误状态的消费者线程，并且我们将`Sender`克隆到每个线程中，所以我们需要移除初始的`Sender`引用；否则，消费者将简单地永远阻塞在一个无休止的循环中。

```
fn main() {
        // channel Example
    println!("---------------------------------------");
    println!("Starting channel example...");
    let (s, r) = unbounded();

    for i in 1..5 {
        run_producer_chan(s.clone(), i);
    }
    drop(s);

    for i in 1..5 {
        run_consumer_chan(r.clone(), i);
    }

    println!("channel example finished!");
}

```

如果我们重复运行，每个线程处理的消息数量会有所不同，但总数将始终为 4，000，这意味着发送到通道的所有事件都已被处理。

```
Starting channel example...
Hello from producer thread 1 - pushing...!
Hello from producer thread 2 - pushing...!
Hello from producer thread 4 - pushing...!
Hello from producer thread 3 - pushing...!
Hello from producer thread 4 - popping!
Hello from producer thread 2 - popping!
Hello from producer thread 3 - popping!
Hello from producer thread 1 - popping!
last sender dropped - stopping consumer thread, messages received: 376
last sender dropped - stopping consumer thread, messages received: 54
last sender dropped - stopping consumer thread, messages received: 2199
last sender dropped - stopping consumer thread, messages received: 1371
channel example finished!

```

## `WaitGroup`举例

等待组是一个非常有用的概念，适用于必须进行并发处理并且必须等到所有处理都完成的情况。比如说；等待并行地从不同的源收集所有的数据，然后等待每个请求完成，然后再聚合数据并继续计算过程。

其思想是创建一个`WaitGroup`，然后，对于每个并发进程，克隆它(在内部，它只是增加一个计数器)并等待所有等待组被删除(即，计数器回到 0)。这样，以线程安全的方式，我们可以保证所有的线程都完成了。

为了展示这一点，我们将创建一个名为`do_work`的助手，它生成一个随机数，休眠几毫秒，然后进行一些基本的计算，再次休眠，然后结束。

这只是为了让我们的线程做一些事情，每个线程花费不同的时间。

```
fn do_work(thread_num: i32) {
    let num = rand::thread_rng().gen_range(100..500);
    thread::sleep(std::time::Duration::from_millis(num));
    let mut sum = 0;
    for i in 0..10 {
        sum += sum + num * i;
    }
    println!(
        "thread {} calculated sum: {}, num: {}",
        thread_num, sum, num
    );
    thread::sleep(std::time::Duration::from_millis(num));
}

```

然后，随着我们的`WaitGroup`的创建，我们创建了许多线程——在本例中是 50 个——并为每个线程克隆了`WaitGroup`,再次将它放入线程中。

之后，我们等待`WaitGroup`，它将阻塞，直到所有的`WaitGroup`克隆都被丢弃——因此所有的线程都完成了。

```
fn main() {
    // WaitGroup Example
    println!("---------------------------------------");
    println!("Starting WaitGroup example...");
    let wg = WaitGroup::new();

    for i in 0..50 {
        let wg_clone = wg.clone();
        thread::spawn(move || {
            do_work(i);
            drop(wg_clone);
        });
    }

    println!("waiting for all threads to finish...!");
    wg.wait();
    println!("all threads finished!");
    println!("WaitGroup example finished!");
}

```

如果我们运行这个，我们可以看到我们的代码一直在等待我们所有的 50 个线程，不管它们需要多长时间才能完成。

```
Starting WaitGroup example...
waiting for all threads to finish...!
thread 41 calculated sum: 114469, num: 113
thread 31 calculated sum: 116495, num: 115
thread 20 calculated sum: 119534, num: 118
thread 18 calculated sum: 126625, num: 125
thread 37 calculated sum: 144859, num: 143
thread 47 calculated sum: 147898, num: 146
thread 42 calculated sum: 170184, num: 168
thread 11 calculated sum: 185379, num: 183
thread 17 calculated sum: 186392, num: 184
thread 19 calculated sum: 188418, num: 186
thread 35 calculated sum: 195509, num: 193
thread 34 calculated sum: 197535, num: 195
thread 4 calculated sum: 200574, num: 198
thread 39 calculated sum: 202600, num: 200
thread 25 calculated sum: 215769, num: 213
thread 6 calculated sum: 223873, num: 221
thread 22 calculated sum: 227925, num: 225
thread 12 calculated sum: 256289, num: 253
thread 49 calculated sum: 265406, num: 262
thread 30 calculated sum: 267432, num: 264
thread 43 calculated sum: 271484, num: 268
thread 27 calculated sum: 283640, num: 280
thread 23 calculated sum: 303900, num: 300
thread 48 calculated sum: 304913, num: 301
thread 14 calculated sum: 306939, num: 303
thread 0 calculated sum: 309978, num: 306
thread 5 calculated sum: 324160, num: 320
thread 13 calculated sum: 333277, num: 329
thread 40 calculated sum: 338342, num: 334
thread 28 calculated sum: 346446, num: 342
thread 46 calculated sum: 358602, num: 354
thread 29 calculated sum: 362654, num: 358
thread 1 calculated sum: 368732, num: 364
thread 15 calculated sum: 368732, num: 364
thread 38 calculated sum: 386966, num: 382
thread 24 calculated sum: 419382, num: 414
thread 44 calculated sum: 430525, num: 425
thread 45 calculated sum: 430525, num: 425
thread 8 calculated sum: 433564, num: 428
thread 32 calculated sum: 433564, num: 428
thread 16 calculated sum: 442681, num: 437
thread 2 calculated sum: 443694, num: 438
thread 26 calculated sum: 444707, num: 439
thread 36 calculated sum: 454837, num: 449
thread 21 calculated sum: 456863, num: 451
thread 7 calculated sum: 458889, num: 453
thread 33 calculated sum: 459902, num: 454
thread 3 calculated sum: 488266, num: 482
thread 10 calculated sum: 497383, num: 491
thread 9 calculated sum: 505487, num: 499
all threads finished!
WaitGroup example finished!

```

就是这样！完整的示例代码可以在我的 [GitHub](https://github.com/zupzup/rust-crossbeam-example) 账户上找到。

## 结论

在本文中，我们查看了强大的 Crossbeam 库的一些部分，这是 Rust 中在并发编程方面绝对重要的部分。

Crossbeam 提供了更多有用的工具和抽象，如果你有兴趣查看它们，可以随意搜索精彩的[文档](https://docs.rs/crossbeam/latest/crossbeam/index.html)。

Crossbeam 背后关于无锁编程的概念及其与 Rust 结合的含义非常有趣，绝对是一个值得深入探索的领域，以便从根本上了解无锁编程的效果及其各种用途。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。