# 慌！Rust 与误差的关系

> 原文：<https://blog.logrocket.com/panic-vs-error-rust/>

正如每个开发人员都知道的那样，在应用程序开发过程中，错误往往更容易发生。错误处理是大多数编程语言中的一个基本概念，它帮助开发人员在程序交付给最终用户之前发现并解决程序中的错误，从而提高 UX。

幸运的是，Rust 以其对可靠性的承诺和对错误处理的支持而闻名。在本文中，我们将探索 Rust 如何使开发人员能够发现并处理他们代码中的错误，阐明`panic!`和错误之间的区别。我们开始吧！

## 目录

## 错误处理:Rust 与 JavaScript

抛出错误在其他编程语言中很常见，意思是当代码中出现错误时，后续的进程[直到通过`try...catch`](https://blog.logrocket.com/exception-handling-in-javascript/) 得到正确处理后才会执行。

但是，在《铁锈》中，没有[也没有`try...catch`](https://blog.logrocket.com/ditching-try-catch-and-null-checks-in-rust/) 。作为背景，我们将回顾一个其他编程语言通常如何使用`try...catch`处理错误的例子，稍后用另一个例子展示 Rust 如何区分`panic!`和错误:

```
async function insertLaptop({ 
  storageSize,  
  model, 
  ram, 
  isRefurbished 
  }) { 
try { 
  if (isRefurbished) { 
    throw new Error("Cannot insert a Refurbished laptop"); 
  } 
const result = await db.laptop.add({ storage, model,ram }); 
return { success: true, id: result.id }; 
} catch(error) { 
return { success: false, id: undefined }; 
  }  
} 

```

在上面的 JavaScript 代码片段中，`insertLaptop`函数向数据库添加一个新的笔记本实例，只要这台笔记本没有翻新。该函数包装有一个`try...catch`错误处理机制。`try`语句允许你定义一个代码块，在它被执行的时候对它进行错误测试，而`catch`语句允许你定义一个代码块，如果在`try`块中出现错误，就执行这个代码块。

当错误发生时，JavaScript 通常会停止并生成一条错误消息，其中包含一些关于错误原因的信息。用专业术语来说，JavaScript 会抛出一个异常。这可以防止代码在出现错误时完全崩溃。这是大多数编程语言的普遍做法。但是，在 Rust 中，`panic!`和 errors 处理这样的异常。

## 生锈的错误

为了更好地理解 Rust 中的错误，我们需要理解 Rust 是如何对其错误进行分组的。与大多数不区分错误类型的编程语言不同，Rust 将错误分为两大类，可恢复的和不可恢复的错误。此外，Rust 也没有例外。它有可恢复错误的类型结果`<T,` `E>`和当程序遇到不可恢复的错误时停止进一步执行的`panic!`宏。

## `Result`枚举和可恢复错误

在 Rust 中，大多数错误都属于可恢复的类别。[可恢复错误](https://web.mit.edu/rust-lang_v1.25/arch/amd64_ubuntu1404/share/doc/rust/html/book/second-edition/ch09-02-recoverable-errors-with-result.html)不会严重到促使程序突然停止或失败，因为它们很容易被解释，因此可以被处理或纠正。对于可恢复的错误，应该向用户报告错误以重试操作，或者向用户提供替代的操作过程。

一个很好的例子是当用户输入一个无效的电子邮件。错误消息可能类似于`please insert a valid email address and retry`。`Result`类型或`Result`枚举通常在 Rust 函数中使用，以返回一个`Ok`成功值或一个`Err`错误值，如下例所示:

```
enum Result<T,E> { 
    OK(T), 
    Err(E) 
} 

```

`T`和`E`是泛型类型参数。`Ok(T)`代表案例成功时的返回值并包含一个值，而`Err(E)`代表错误案例并返回一个错误值。值得注意的是，当错误是预期的和可恢复的时，我们从函数中返回`Result`。

因为`Result`有这些通用类型参数，我们可以在许多不同的情况下使用`Result`类型和在其上定义的函数，在这些情况下，我们想要返回的成功值和错误值可能不同:

```
use std::fs::File; 
fn main() { 
    let f = File::open("main.jpg");  
    //this file does not exist 
    println!("{:?}",f); 
} 

Err(Error { repr: Os { code: 2, message: "No such file or directory" } }) 

```

上面的代码是一个打开指定文件的简单程序。如果文件已经存在，程序返回`OK(File)`,如果没有找到文件，程序返回`Err`。对于这个错误以及值类型为`Result <T,` `E>`的其他类型的错误，有一个可恢复的错误。它允许我们在不中断整个程序的情况下访问错误值。

## 不同错误的匹配

让我们重温一下前面的代码片段。不管`File::open`为什么失败，代码都会`panic!`。为了更好地调试我们的程序，我们可能需要根据失败的原因采取[不同的步骤。例如，如果代码由于不存在的文件而失败，我们可以创建文件并返回新的文件。](https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html#matching-on-different-errors)

如果`File::open`由于其他原因失败，比如用户没有打开指定文件的权限，我们希望代码为`panic!`，就像它在前面的代码片段中所做的那样。为了达到这个结果，我们添加了一个内部的`match`表达式:

```
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let logo_image = File::open("main.jpg");

    let logo_file = match logo_image {
        Ok(file) => file,
        Err(error) => match error.kind() {
            ErrorKind::NotFound => match File::create("main.jpg") {
                Ok(fc) => fc,
                Err(e) => panic!("We encountered a problem creating this file: {:?}", e),},
            other_error => {
                panic!("There was a problem opening the specified file: {:?}", other_error);
            }
        },
    };
}

```

在上面的代码片段中，我们导入了`io::ErrorKind`，它包含了`io::Error`，一个来自 Rust 标准库的[结构](https://doc.rust-lang.org/book/ch05-01-defining-structs.html)。`io::Error`是由`Err`变量中的`File::open`返回的值类型，有一个名为`kind`的方法。它包含几个变量，代表可能由`io`操作产生的不同种类的错误。在我们的例子中，我们得到`ErrorKind::NotFound`，这意味着我们试图打开的文件不存在。

该函数在`error.kind()`上还有一个内部`match`表达式。我们正在检查条件，这意味着由`error.kind()`返回的值是否是`ErrorKind`枚举的`NotFound`变体。如果满足这个条件，使用`File::create`创建一个名为`main.jpg`的新文件。`File::create`也可能失败，所以我们为内部`match`表达式包含了另一个选项。

如果`File::create`失败，我们打印一个不同的错误消息。外层`match`的第二个选项保持不变，这意味着当程序遇到除了`ErrorKind`枚举的`NotFound`变量之外的任何错误时，它都会死机。

## `Result <T,E>`类型助手方法

还有其他`Result <T, E>`类型的[助手方法](https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html#shortcuts-for-panic-on-error-unwrap-and-expect)，可以用来比`match`更好地传达意图。

### 生锈`unwrap`

`unwrap`方法更适合更具体的任务。在我们之前的例子中，我们可以使用`unwrap`。如果该值是一个`Ok`变量，`unwrap`将返回`Ok`中的值。如果`Result`是`Err`的变体，`unwrap`会为我们调用`panic!`宏。我们将使用上面的例子来展示`unwrap`的作用:

```
use std::fs::File;

fn main() {
    let logo_image = File::open("main.jpg").unwrap();
}

```

如果我们在没有`main.jpg`文件的情况下运行这段代码，我们会遇到来自`panic!`调用的错误消息，这个调用是由`unwrap`方法生成的:

```
thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: Os {
code: 2, kind: NotFound, message: "No such file or directory" }',
src/main.rs:4:49

```

### 生锈`expect`

`expect`是另一个助手方法，它的工作方式类似于`unwrap`,但是可以定制`panic!`错误消息。让我们看看`expect`的行动:

```
use std::fs::File;

fn main() {
    let logo_file = File::open("main.jpg")
        .expect("main.jpg file should be included in the project for the program to run");
}

```

在上面的代码中，我们使用了`expect`而不是`unwrap`。它返回文件句柄或在出错时调用`panic!`宏。不同之处在于，错误消息将是传递给`expect`的参数，而不是`unwrap`使用的默认`panic!`消息。

让我们看看调用不存在的`main.jpg`文件时的输出:

```
thread 'main' panicked at 'main.jpg file should be included in the project for the 
program to run: Os {
code: 2, kind: NotFound, message: "No such file or directory" }',
src/main.rs:5:10

```

## `panic!`宏和不可恢复的错误

无法处理的不可恢复的错误通常是由 bug 引起的。当它们发生时，不可恢复的错误会导致程序突然失败。此外，程序不能恢复到正常状态，重试失败的操作，或撤消错误。不可恢复错误的一个例子是试图访问超出`array x`结尾的位置。

在 Rust 中，`panic!`是一个[宏](https://doc.rust-lang.org/book/ch19-06-macros.html)，当程序遇到不可恢复的错误时，它会终止程序。简单来说，`panic!`指的是一个关键性的错误。`panic!`宏允许程序立即终止，并向程序调用者提供反馈。

### 如何触发`panic!`

有两种方法可以触发`panic!`。第一种选择是手动`panic!`一个 Rust 程序。第二种是通过采取一个导致代码`panic!`的动作。

为了手动`panic!`Rust 程序，我们使用了`panic!`宏。像触发一个函数一样调用它:

```
fn main() { 
panic!("panic vs. error in rust"); // because of panic, the program will terminate 
//immediately.  
println!("I am the unreachable statement ");  
} 

```

在上面的例子中，我们已经包含了一个`panic!`宏。一旦程序遇到它，它会立即终止:

```
--&gt; hello.rs:3:5 
| 
2 | panic!("panic vs. error in rust");// because of panic, the program will terminate immediately.  
| --------------------------------- any code following this expression is unreachable 
3 | println!("I am the unreachable statement ");  
| ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ unreachable statement 
| 
= note: `#[warn(unreachable_code)]` on by default 
= note: this warning originates in the macro `println` (in Nightly builds, run with -Z macro-backtrace for more info) 
warning: 1 warning emitted 

```

下面是调用`panic!`宏的条件语句:

```
fn main() { 
  let no = 112;  
  //try with no above 100 and below 100  
  if no>=100 { 
  println!("Thank you for inserting your number, Registration is successful "); 
  } else { 
  panic!("NUMBER_TOO_SHORT");  
  } 
  println!("End of main"); 
} 

```

下面是变量小于 100 时的输出:

```
thread 'main' panicked at 'NUMBER_TOO_SHORT', hello.rs:7:8 
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace 

```

当变量大于或等于 100 时，输出如下:

```
Thank you for inserting your number, Registration is successful 
End of main 

```

下面的例子显示了一个直接来自库的`panic!`调用的实例，这是一个 bug 的结果，而不是手动调用。下面的代码试图访问 vector 中的索引。该错误是由于向量超出了有效索引的范围:

```
fn main() { 
let f = vec![1, 2, 3]; 
v[40]; 
} 

```

程序试图访问我们的向量的第 41 个元素(索引为 40 的向量)，但是向量只有三个元素。在这种情况下，铁锈会`panic!`。使用`[]`应该返回一个元素，但是如果你传递一个无效的索引，Rust 在这里不会返回正确的元素:

```
thread 'main' panicked at 'index out of bounds: the len is 3 but the index is 40', hello.rs:15:5 
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace  

```

## 恢复或停止执行

当决定你的代码是否应该`panic!`时，我们需要考虑我们的代码结束于一个糟糕状态的可能性。一个[坏状态](https://doc.rust-lang.org/book/ch09-03-to-panic-or-not-to-panic.html#guidelines-for-error-handling)是当一些假设、保证、契约或不变量被破坏时，例如当无效值、矛盾值或丢失值被传递到您的代码时，加上以下一个或多个:

*   不良状态是意料之外的事情，而不是偶尔会发生的事情，比如用户以错误的格式输入数据
*   在这之后，您的代码需要依赖于不处于这种糟糕的状态，而不是在每一步都检查问题
*   没有一种好的方法将这些信息编码到您使用的类型中

当用户向我们的代码传递没有意义的值时，最好尽可能返回一个错误。这为用户提供了决定下一步行动的选项。

然而，在某些情况下，继续可能被证明是有害或不安全的。在这种情况下，最好打电话给`panic!`并提醒用户有一个 bug，提示他们在开发阶段修复它。

另一个适合调用`panic!`的情况是当你调用不受你控制的外部代码时，它返回一个你无法修复的无效状态。然而，当预计会失败时，返回一个`Result`比生成一个`panic!`更合适。

## 查看堆栈跟踪或回溯跟踪

类似于其他编程语言，`panic!`提供了错误的堆栈跟踪或回溯跟踪
。但是，除非将环境变量`RUST_BACKTRACE`设置为非零值，否则回溯不会显示在终端上。因此，如果在前面的代码示例中执行以下命令语句:

```
RUST_BACKTRACE=1 ./hello  

```

### 堆栈跟踪

```
RUST_BACKTRACE=1 cargo run 
thread 'main' panicked at 'index out of bounds: the len is 3 but the index is 40', hello.rs:15:5 
stack backtrace: 
0: rust_begin_unwind 
at /rustc/a55dd71d5fb0ec5a6a3a9e8c27b2127ba491ce52/library/std/src/panicking.rs:584:5 
1: core::panicking::panic_fmt 
at /rustc/a55dd71d5fb0ec5a6a3a9e8c27b2127ba491ce52/library/core/src/panicking.rs:142:14 
2: core::panicking::panic_bounds_check 
at /rustc/a55dd71d5fb0ec5a6a3a9e8c27b2127ba491ce52/library/core/src/panicking.rs:84:5 
3: <usize as core::slice::index::SliceIndex<[T]>>::index 
4: core::slice::index::<impl core::ops::index::Index<I> for [T]>::index 
5: <alloc::vec::Vec<T,A> as core::ops::index::Index<I>>::index 
6: hello::main 
7: core::ops::function::FnOnce::call_once 
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace. 

```

## 摘要

总而言之，Rust 有两种错误，一种是从`Result`类型返回的错误值，一种是触发`panic!`宏产生的错误。`Result`类型返回一个可恢复的错误，或者换句话说，一个不会导致程序终止的错误。

另一方面，`panic!`宏触发一个不可恢复的错误，或者导致 Rust 程序立即终止。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。