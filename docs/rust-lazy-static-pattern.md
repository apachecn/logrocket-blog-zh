# 揭秘 Rust 的懒惰静态模式

> 原文：<https://blog.logrocket.com/rust-lazy-static-pattern/>

静态数据是计算机编程中的一个基本概念，指的是存储在全局内存空间中并在程序的整个生命周期中持续存在的数据。在 Rust 中，静态数据用于存储程序中所有线程共享的值，并保证在使用前进行初始化。但是 Rust 中的静态数据有多种形式，其中一种叫做`lazy_static`。

`lazy_static`是 Rust 中的一种模式，其中一个值仅在第一次被访问时被初始化。这与在编译时初始化的常规静态数据和在第一次线程安全访问时初始化的延迟初始化静态数据不同。惰性静态值以线程安全的方式初始化，可用于存储全局变量或共享常量数据。

在本文中，我们将探讨 Rust 中的`lazy_static`的概念及其各种用途。我们将看看`lazy_static`是如何工作的，使用它的优缺点，以及如何在 Rust 项目中使用它的一些实际例子。

我们将涵盖:

## `lazy_static`如何工作

要在 Rust 中使用`lazy_static`，您需要在您的项目中包含`lazy_static`机箱。这个箱子提供了一个叫做`lazy_static!`的宏，允许你定义一个`lazy_static`变量。下面是一个如何声明`lazy_static`变量的例子:

```
use lazy_static::lazy_static;

lazy_static! {
    static ref MY_VAR: String = "some value".to_string();
}

```

如您所见，`lazy_static!`宏采用了定义`lazy_static`的代码块。在本例中，我们定义了一个名为`MY_VAR`的静态变量，它的类型为`String`，并用值`"some value"`初始化。

当第一次访问`MY_VAR`时，它将被初始化为值`"some value"`。后续访问将返回初始化的值，而不重新初始化它。这就是`lazy_static`值不同于常规静态数据的地方，常规静态数据在编译时初始化，在运行时不能更改。

要访问一个`lazy_static`值，您可以使用与任何其他静态变量相同的语法。例如:

```
fn main() {
    println!("My lazy static value is: {}", *MY_VAR);
}

```

需要注意的是`lazy_static`值存储在堆中，而不是堆栈中。这意味着它们遵循与堆分配数据相同的规则，例如当不再需要它们时需要释放它们。但是，因为`lazy_static`值只初始化一次，并且在所有线程之间共享，所以可以有效地访问它们，而不需要重复分配和释放。

## 在铁锈中使用`lazy_static`

现在我们已经看到了`lazy_static`是如何工作的，让我们探索一下它在 Rust 项目中的一些使用方法。

### 线程安全的全局变量

使用`lazy_static`的主要好处之一是能够存储线程安全的全局变量。因为惰性静态值是以线程安全的方式初始化的，所以可以从多个线程安全地访问它们，而不需要额外的同步。这在您希望避免锁定和解锁共享资源的开销的情况下尤其有用。

例如，假设一个程序有多个线程需要访问一个共享的计数器变量。如果没有`lazy_static`，您将需要使用互斥锁来同步对计数器的访问:

```
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let counter = Arc::new(Mutex::new(0));
    let mut handles = Vec::new();
    for _ in 0..10 {
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();
            *num += 1;
        });
        handles.push(handle);
    }
    for handle in handles {
        handle.join().unwrap();
    }
    println!("Result: {}", *counter.lock().unwrap());
}

```

在这个例子中，我们使用一个 Arc(原子引用计数)和一个互斥体来同步对`counter`的访问。这样做很好，但是它会在每次访问计数器时锁定和解锁互斥体，从而增加程序的开销。

或者，我们可以使用`lazy_static`将计数器存储为一个全局变量，避免同步的需要:

```
use lazy_static::lazy_static;
use std::sync::atomic::{AtomicUsize, Ordering};
use std::thread;

lazy_static! {
    static ref COUNTER: AtomicUsize = AtomicUsize::new(0);
}

fn main() {
    let mut handles = Vec::new();

    for _ in 0..10 {
        let handle = thread::spawn(|| {
            COUNTER.fetch_add(1, Ordering::SeqCst);
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", COUNTER.load(Ordering::SeqCst));
}

```

在这个例子中，我们使用`AtomicUsize`作为`lazy_static`变量来存储计数器。这允许我们在计数器上执行原子操作，例如`fetch_add`，它以线程安全的方式将`counter`递增一个给定值。因为计数器被存储为一个全局变量，并通过`lazy_static`宏来访问，所以我们不需要担心同步或者锁定和解锁互斥体的开销。

### 共享常量数据

惰性静态对于存储共享常量数据也很有用。因为该值只初始化一次，所以可以高效地访问它，而不需要重复计算。这可以提高程序的性能，尤其是在计算该值的成本很高的情况下。

例如，考虑一个需要高精度计算圆周率值的程序。这可能是一项计算量很大的任务，尤其是当程序需要多次计算圆周率的值时。为了避免这种开销，我们可以使用`lazy_static`将 pi 的计算值存储为一个全局常数:

```
use lazy_static::lazy_static;

lazy_static! {
    static ref PI: f64 = compute_pi();
}

fn compute_pi() -> f64 {
    // expensive computation to determine the value of pi
    3.14159265358979323846
}

fn main() {
    println!("The value of pi is: {}", *PI);
}

```

在这个例子中，pi 的值只在第一次访问`lazy_static`变量时计算一次。对 pi 值的后续访问将返回初始化的值，而无需重新计算。这可以通过避免多次执行 pi 的昂贵计算来提高程序的性能。

### 性能优化

除了存储线程安全的全局变量和共享常量数据，`lazy_static`还可以作为 Rust 中的一种性能优化技术。通过避免在实际需要之前初始化数据，`lazy_static`可以帮助减少程序的内存和计算开销。

例如，考虑一个程序，它有一个仅在特定情况下需要的大型数据结构。如果没有`lazy_static`，我们可能会在程序开始时初始化数据结构，即使程序的大部分执行都不需要它:

```
fn main() {
    let data = initialize_data();

    if condition {
        use_data(&data);
    }
}

fn initialize_data() -> Vec<i32> {
    // expensive operation to initialize data structure
    vec![1, 2, 3, 4, 5]
}

fn use_data(data: &Vec<i32>) {
    // use the data structure
}

```

在这个例子中，数据结构在程序开始时被初始化，即使它是不需要的。如果数据结构很大并且不满足条件，这可能是一种浪费，因为它给程序增加了不必要的内存和计算开销。

为了避免这种开销，我们可以使用`lazy_static`来延迟数据结构的初始化，直到真正需要它的时候:

```
use lazy_static::lazy_static;

lazy_static! {
    static ref DATA: Vec<i32> = initialize_data();
}

fn main() {
    if condition {
        use_data(&*DATA);
    }
}

fn initialize_data() -> Vec<i32> {
    // expensive operation to initialize data structure
    vec![1, 2, 3, 4, 5]
}

fn use_data(data: &Vec<i32>) {
    // use the data structure
}

```

在这个例子中，仅当满足条件并且访问了`DATA`变量时，数据结构才被初始化。通过避免不必要的数据结构初始化，这有助于减少程序的内存和计算开销。

## 使用`lazy_static`的优缺点

虽然`lazy_static`在 Rust 中是一个有用的工具，但是理解使用它的优点和缺点是很重要的。

`lazy_static`的主要优势之一是能够存储线程安全的全局变量和共享常量数据。正如我们在前面的例子中看到的，`lazy_static`可以通过避免同步和重复计算的开销来帮助提高程序的性能。它使用起来也相对简单，语法简单易懂。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

然而，使用`lazy_static`也有一些限制。一个潜在的问题是初始化竞争条件的可能性，其中多个线程可能试图同时初始化同一个`lazy_static`值。为了避免这种情况，您可以使用`once_cell`箱，它提供了一个线程安全的单元，只能初始化一次。

`lazy_static`的另一个缺点是它增加了程序的复杂性。通过使用它，您为您的程序增加了一个额外的抽象层，这对于其他开发人员来说可能并不明显。这使得理解和调试程序变得更加困难，尤其是如果你不熟悉`lazy_static`机箱的话。

## `lazy_static`的替代方案

如前所述，`lazy_static`的一个潜在问题是初始化竞争条件的可能性，其中多个线程可能试图同时初始化相同的`lazy_static`值。为了避免这种情况，我们可以使用`once_cell`箱，它提供了一个线程安全的单元，只能初始化一次。

### `once_cell` **货箱**

`once_cell` crate 提供了一个名为`OnceCell`的类型，它是一个只能初始化一次的单个值的容器。一旦在`OnceCell`中初始化了一个值，就可以从多个线程中安全地访问它，而不需要额外的同步。

这里有一个如何在 Rust 中使用`OnceCell`的例子:

```
use once_cell::sync::OnceCell;

static DATA: OnceCell<Vec<i32>> = OnceCell::new();

fn main() {
    let data = DATA.get_or_init(|| vec![1, 2, 3, 4, 5]);
    println!("Data: {:?}", data);
}

```

在这个例子中，我们使用`OnceCell`的`get_or_init`方法用整数向量的值初始化`DATA`变量。如果`DATA`已经被初始化，`get_or_init`将简单地返回初始化的值。这保证了`DATA`只初始化一次，即使是从多线程访问。

### `LazyLock` **货箱**

`lazy_static`的另一个替代品是`LazyLock`，它是一个板条箱，提供线程安全的惰性初始化器。和`lazy_static`一样，`LazyLock`允许你定义一个值，这个值只在第一次被访问时被初始化。然而，与`lazy_static`不同的是，`LazyLock`使用锁来同步对值的访问，确保它只被初始化一次，即使存在多个线程。

这里有一个如何在 Rust 中使用`LazyLock`的例子:

```
use lazy_lock::LazyLock;

lazy_lock::lazy_lock! {
    static DATA: Vec<i32> = vec![1, 2, 3, 4, 5];
}

fn main() {
    let data = DATA.lock().unwrap();
    println!("Data: {:?}", data);
}

```

在这个例子中，我们使用`lazy_lock!`宏来定义一个名为`DATA`的`LazyLock`变量。当访问`DATA`变量时，使用互斥锁锁定它，以确保它只被初始化一次。这有助于避免初始化竞争情况，并允许从多个线程安全地访问`DATA`。

## `lazy_static`、`OnceCell`和`LazyLock`的区别

这些变量之间的一个关键区别是它们处理初始化竞争条件的方式。`lazy_static`不提供任何同步机制，因此多个线程有可能试图同时初始化同一个`lazy_static`值。这可能导致竞态条件和未定义的行为。另一方面，`OnceCell`和`LazyLock`都使用同步机制来确保值只被初始化一次，即使存在多个线程。

另一个区别是您对初始化过程的控制程度。在`lazy_static`中，你使用一个宏来定义初始化值，该值在第一次被访问时被自动初始化。使用`OnceCell`，您可以更好地控制初始化过程，因为您可以指定一个闭包，如果值还没有初始化，就调用这个闭包来初始化值。如果初始化过程更复杂或涉及昂贵的计算，这可能是有用的。`LazyLock`也允许你为初始化指定一个闭包，类似于`OnceCell`。

在性能方面，`lazy_static`可能比其他两个选项更有优势，因为它没有同步开销。因为`lazy_static`不使用锁或其他同步机制，所以在某些情况下，它可能比`OnceCell`和`LazyLock`更快。但是，需要注意的是，这将取决于具体的用例以及初始化过程的开销。

总的来说，`lazy_static`、`OnceCell`和`LazyLock`之间的选择将取决于您的具体需求和您的程序的要求。如果你需要存储一个线程安全的全局变量或者共享常量数据，并且愿意接受初始化竞争条件的潜在风险，那么`lazy_static`可能是一个不错的选择。另一方面，如果您需要确保值只初始化一次，并且愿意接受同步的额外开销，那么`OnceCell`或`LazyLock`可能是更好的选择。

## 结论

总之，`lazy_static`是 Rust 中存储线程安全全局变量和共享常量数据的有用工具。通过避免重复计算以及锁定和解锁共享资源的开销，它可以提高程序的性能。但是，了解初始化争用情况的可能性并使用适当的措施来避免它们是很重要的。

总的来说，是否在 Rust 程序中使用`lazy_static`是在提高性能的好处和它引入的额外复杂性之间的权衡。如果您正在从事一个需要线程安全全局变量或共享常量数据的项目，那么`lazy_static`可能是一个值得考虑的有用工具。另一方面，如果你正在开发一个简单的程序，其中`lazy_static`的好处是不必要的，那么最好坚持使用常规的静态数据。

与任何编程工具一样，理解`lazy_static`的能力和局限性并在项目中恰当地使用它是很重要的。通过揭开`lazy_static`的概念及其用途的神秘面纱，我们可以就何时以及如何在我们的 Rust 程序中使用它做出明智的决定。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。