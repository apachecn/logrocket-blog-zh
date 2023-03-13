# 用 Rayon Rust 实现数据并行

> 原文：<https://blog.logrocket.com/implementing-data-parallelism-rayon-rust/>

并发和并行计算是软件开发领域的热门话题。公平地说，这种流行是当之无愧的，因为它们为程序提供了显著的性能提升。然而，当不正确地实现时，它们会导致难以调试的问题，比如数据竞争和死锁。

幸运的是，Rust 编程语言配备了无所畏惧的并发性，这是通过 Rust 的[所有权](https://blog.logrocket.com/understanding-ownership-in-rust/)和[借用检查器](https://blog.logrocket.com/introducing-the-rust-borrow-checker/)模型实现的。因此，许多导致数据竞争和死锁的并发错误都是在编译时捕获的。

在本文中，我们将探索如何用 Rayon Rust 实现数据并行，以及 [rayon crate](https://crates.io/crates/rayon) 如何通过使顺序迭代器转换成相似的对应物变得更容易来改进 Rust 编程语言的并行特性。

在开始之前，确保您熟悉 Rust 编程语言，并对 Rust 迭代器有基本的了解。要重温 Rust 基础知识，请查看我们的[指南，了解 Rust](https://blog.logrocket.com/getting-up-to-speed-with-rust/) 的最新进展。

*向前跳转:*

## 理解 Rust 中的顺序迭代器

默认情况下，Rust 标准库提供的迭代器按顺序执行操作。假设我们有一个 1000 人的名单。现在，假设我们想检查名字`Alice`是否能在列表中找到。为此，我们可以使用标准库中的各种迭代器方法，如下所示:

```
let names = vec![ ... ]; 
let is_present = names.iter().any(|name| name == "Alice"); 

```

虽然这种方法有效，但是列表中的名字是按照数字顺序遍历的，直到找到`Alice`为止。如果在列表的后面找到了`Alice`，这可能不是最好的方法。那么，我们来看一个更实际的例子。假设我们想下载一个 2000 MB 的大图像。下面是我们可能如何按顺序进行(*原型*):

```
/** !! Does not compile !! **/ 

// Using reqwest crate 

fn main()-> reqwest::Result<()> { 
    let ranges = &[(0, 249MB), (250MB, 499MB), (500MB, 749 MB), (750MB, 999MB), (1000MB,1249MB), (1250MB, 1499MB), (1500MB, 1749MB), (1750MB, 2000MB)]; 
ranges.iter().for_each(|(start, end)| { 
        let client = reqwest::blocking::Client::new(); 
        let response = client.get("random_video_url.mp4").header(RANGE, RangeHeader((start, end))).send().unwrap(); 
        std::fs::write(format!("file{}", a), response.bytes().unwrap()) 
}); 
// finally, join all files 
Ok(()) 
} 

```

即使我们将文件分成了块，这种方法也可以顺序下载文件。首先下载第一个块，第二个和第三个，依此类推。所有这些都发生在一个线程上。然而，这种方法有几个缺点。假设服务器只允许每个线程每秒最多 1MB。这意味着在最大网络连接下，下载大约需要 2000 秒。已经超过 33 分钟了！

## 实现并行迭代器

并行执行操作通常会提高进程的速度。回到我们之前的例子，每兆字节需要一秒钟来下载。让我们假设文件被分成八个相等的块，每个块都有 250MB，这样每个块都有自己的线程。

这意味着每个线程需要大约 250 秒来下载它的块。因为线程同时运行(并行运行)，所以总时间仍然是 250 秒——大约 4 分钟！可能需要几秒钟来合并文件以获得完整的文件。

用原型代码来说:

```
/** !! Does not compile !! **/ 
use rayon::prelude::*; 
use reqwest::header::RANGE; 

// — Snip – // 
let ranges = &[(0, 249MB), (250MB, 499MB), (500MB, 749 MB), (750MB, 999MB), (1000MB,1249MB), (1250MB, 1499MB), (1500MB, 1749MB), (1750MB, 2000MB)]; 
ranges.par_iter().for_each(|(start, end)| { 
        let client = reqwest::blocking::Client::new(); 
        let response = client.get("random_video_url.mp4").header(RANGE, RangeHeader((start, end))).send().unwrap(); 
        std::fs::write(format!("file{}", a), response.bytes().unwrap()) 
}); 
// finally, join all files  

```

你可能已经注意到了`par_iter`方法的使用。这种方法是由[人造丝](https://docs.rs/rayon/1.6.1/rayon/index.html)货箱提供的。Rayon 允许程序员轻松地将标准库提供的各种迭代器方法转换成并行执行的迭代器。要将顺序迭代器代码片段转换成并行代码片段，首先用`use rayon::prelude::*;`导入 rayon crate，并用相应的并行版本的`into_par_iter`或`par_iter`替换`into_iter`或`iter`方法。以下是一些例子:

```
use rayon::prelude::*; 
let names = vec![ ... ]; 
let is_present = names.par_iter().any(|name| name == "Alice"); 

// sequential 
fn sum_of_char_len(list: &[&str]) -> usize { 
    list.iter().map(|word| word.len()).sum() 
} 

// parallel 
fn par_sum_of_char_len(list: &[&str]) -> usize { 
    list.par_iter().map(|word| word.len()).sum() 
} 

```

## 使用人造丝的`par_bridge`方法

`par_iter`和`into_par_iter`方法通常作用于内置集合，比如列表和散列表。但是，它们不适用于自定义迭代器类型。对于并行执行的自定义迭代器来说，该方法非常方便。为了说明这一点，下面是一个简单的素数生成器:

```
struct PrimeGenerator { 
    start: usize, 
    end: usize, 
} 

fn is_prime(num: usize) -> bool { 
    if num <= 1 { 
        return false; 
    } 
    for i in 2..num { 
        if num % i == 0 { 
            return false; 
        } 
    } 
    true 
} 

impl Iterator for PrimeGenerator { 
    type Item = usize; 

    fn next(&mut self) -> Option<Self::Item> { 
        while !is_prime(self.start) { 
            self.start += 1; 
        } 
        if self.start > self.end { 
            None 
        } else { 
            let prime = self.start; 
            self.start += 1; 
            Some(prime) 
        } 
    } 
} 

```

这里，我们创建了一个简单的`struct`并实现了`Iterator`特征。`iterator`在指定的范围内产生一个质数。要并行执行这个`iterator`，我们必须使用`par_bridge`方法，如下所示:

```
fn get_primes() { 
    let primes = PrimeGenerator { 
        start: 0, 
        end: 20, 
    }; 
    println!("ranges are {:?}", primes.par_bridge().collect::<Vec<_>>()) 
} 

```

> 注意:这将打印一个没有特定顺序的质数列表。

因为`par_bridge`产生一个`iterator`，几个`iterator`方法可以应用于它。这些包括，`map`，`for_each`，`sum`等等。你可以点击查看整个列表[。](https://docs.rs/rayon/1.6.1/rayon/iter/trait.ParallelIterator.html)

让我们在下面想象一下:

```
// sum prime numbers between a particular range 
let primes = PrimeGenerator { 
        start: 0, 
        end: 20, 
    }; 
let sum_of_primes: usize = primes.par_bridge().sum(); 

```

## 探索人造丝的实例

并行迭代器是一种在应用程序中引入[并发](https://blog.logrocket.com/deep-dive-concurrency-rust-programming-language/)的灵活方式，无需在几个线程间共享任务的麻烦。然而，rayon 还提供了`join`、`scope`和`ThreadPoolBuilder`实例，允许您创建定制任务。让我们深入了解每一个问题。

### `join`法

`[join](https://docs.rs/rayon/latest/rayon/fn.join.html)`函数允许程序员潜在地并行执行两个[闭包](https://blog.logrocket.com/optimizing-rust-code-with-closures-environment-capturing/)。Rayon 的`parallelization`方法是基于一个被称为[偷工减料](https://en.wikipedia.org/wiki/Work_stealing)的原理。第一个闭包在当前线程上执行，而第二个闭包可以在另一个线程上执行。如果有一个空闲线程，该线程将执行闭包。

然而，当没有空闲线程时，当第一个闭包返回时，第二个闭包在当前线程上执行。假设另一个空闲线程已经在执行第二个闭包(被盗)。在这种情况下，当前线程等待小偷(当前执行第二个闭包的空闲线程)完成。也就是说，传递给`join`方法的闭包应该是 CPU 绑定的任务，而不是像 [I/O](https://blog.logrocket.com/asynchronous-i-o-and-async-await-packages-in-rust/) 那样的阻塞任务。你可以在这里了解更多关于[的信息。下面是`join`的一些用法:](https://docs.rs/rayon/latest/rayon/fn.join.html#warning-about-blocking-io)

```
fn strings_to_num(slice: &[&str]) -> Vec<usize> { 
    slice.iter().map(|s| { 
        s.parse::<usize>().expect(&format!("{} is not a number", s)) 
    }).collect() 
} 

fn factorial(mut n: u128) -> u128 { 
    (1..=n).reduce(|multiple, next| multiple * next).unwrap() 
} 

fn test_join() { 
    let (a, b) = rayon::join(|| factorial(30), 
                            || strings_to_num(&["12", "100", "19887870", "56", "9098"]), 
    ); 
    println!("factorial of 1000 is {}", a); 
    println!("numbers are {:?}", b) 
} 

```

> 注意:因为闭包在不同的线程上执行，所以其中一个闭包的崩溃不会取消另一个闭包的执行。

### `scope`法

`[scope](https://docs.rs/rayon/latest/rayon/fn.scope.html)`函数允许您异步生成任意数量的任务。类似于`join`，`scope`一旦所有的任务在其中产生，就返回。让我们看一个例子:

```
fn test_scope() { 
    let mut map: Option<HashMap<String, usize>> = None; 
    let mut factorial = 1; 
    let mut other = None; 
    rayon::scope(|s| { 
        s.spawn(|_s| { 
            let iter = 
                (0..10000).enumerate().map(|(a, b)| (format!("index {}", a), b)); 
            map = Some(HashMap::from_iter(iter)); 
        }); 
        s.spawn(|_s| { 
            factorial = factorial_sequential(30); 
        }); 
        s.spawn(|_s| { 
            other = Some("value") 
        }) 
    }); 

    println!("map {:?}", map.unwrap()); 
    println!("factorial {:?}", factorial); 
    println!("other {:?}", other); 
} 

```

有几点你应该注意。第一，不像`join`，`scope`不返回任何东西。然而，将值写入比`scope`更长寿的局部变量是可能的。例如，上面定义的`map`变量比`scope`函数寿命长。这允许`scope`写入`map`。另外，`map`变量可以在`scope`之外读取。

其次，遵循 [Rust borrow checker 规则](https://blog.logrocket.com/introducing-the-rust-borrow-checker/)，不允许多个 spawn 闭包修改同一个变量。此外，不能在一个闭包里有一个可变借位，而在另一个闭包里有一个不可变借位。因此，下面的代码不会编译:

```
// !! Does not compile !!
// — Snip — //
rayon::scope(|s: &Scope| {
        s.spawn(|_s| { 
            let iter = 
                (0..10000).enumerate().map(|(a, b)| (format!("index {}", a), b)); 
            map = Some(HashMap::from_iter(iter)); // first mutable usage occurs here 
        }); 
        s.spawn(|s: &Scope| { 
            factorial = factorial_sequential(30); 
            println! ("my map is {:?}", map) // reading of variable here produces compile error (immutable borrow) 
        }); 
        s.spawn(|s: &Scope| { 
            other = Some("value") 
        }) 
    }); 
// —- Snip —- // 

```

## 使用`non-Send`和`non-Sync`类型

在 Rust 中，实现了`[Send](https://doc.rust-lang.org/core/marker/trait.Send.html)`和`[Sync](https://doc.rust-lang.org/core/marker/trait.Sync.html)`特征的类型通常被认为是线程安全的。也就是说，它们可以在多个线程之间共享，而不会出现未定义的行为或数据竞争。如果在这种情况下合适，编译器会自动实现这些特征。因此，在上面的例子中，`[Send](https://doc.rust-lang.org/core/marker/trait.Send.html)`和`[Sync](https://doc.rust-lang.org/core/marker/trait.Sync.html)`特征被自动实现为`usize`和`[&str](https://blog.logrocket.com/understanding-rust-string-str/)`。

上面的例子没有编译，因为一个线程可能正在读取`map`变量，而另一个线程可能正在写入它，这可能导致数据竞争。为了解决这个问题，go-to 方法是将自定义类型的变量包装在`Arc`中。您可能想添加`Mutex`来使`atomic`变量可变。对于原始类型，比如整数，最好使用由`[std::sync::atomic](https://doc.rust-lang.org/std/sync/atomic/index.html)`提供的类型。现在，让我们修改上面的代码，以便它可以编译:

```
fn test_scope() { 
    let map: Arc<Mutex<Option<HashMap<String, usize>>>> = Arc::new(Mutex::new(None)); 
    let mut factorial = 1; 
    let mut other = None; 
    rayon::scope(|s: &Scope| { 
        s.spawn(|_s| { 
            let iter = 
                (0..10000).enumerate().map(|(a, b)| (format!("index {}", a), b)); 
            let mut map = map.lock().unwrap(); 
            *map = Some(HashMap::from_iter(iter)); //note the use of the "dereference" symbol * 
        }); 
        s.spawn(|_s| { 
            factorial = factorial_sequential(30); 
            println!("my map is  {:?}", map.lock().unwrap()) // "map" may or may not be None a 
        }); 
        s.spawn(|_s| { 
            other = Some("value") 
        }) 
    }); 
    println!("map {:?}", map.lock().unwrap()); 
   // —- Snip —- // 
} 

```

## 结论

尽管并发和异步任务很难实现，但它们有可能提高您编写的程序的性能。幸运的是，Rust 去掉了棘手的部分，让编写异步应用变得轻而易举。Rayon 作为一个附件来进一步简化这项任务。通过其方便的并行迭代器实现，灵活性超出了您的想象。你越是尝试异步代码问题，你就会变得越好。编码快乐！

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。