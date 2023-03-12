# 使用 Clap 解析 Rust 中的命令行参数

> 原文：<https://blog.logrocket.com/command-line-argument-parsing-rust-using-clap/>

在本文中，我们将了解如何手动解析传递给 Rust 应用程序的命令行参数，为什么手动解析可能不是大型应用程序的好选择，以及 Clap 库如何帮助解决这些问题，包括:

请注意，您应该能够自如地读写基本的 Rust，比如变量声明、if-else 块、循环和结构。

## 设置示例 Rust 应用程序

比方说，我们有一个包含许多基于节点的项目的项目文件夹，我们想知道，“我们使用了所有包中的哪一个——包括依赖包——以及使用了多少次？”

毕竟，1GB 的`node_modules`不可能都是唯一的依赖项，对吗😰 …?

如果我们做一个漂亮的小程序来计算我们在项目中使用一个包的次数会怎么样？

为此，让我们用 Rust 中的`cargo new package-hunter`建立一个项目。`src/main.rs`文件现在有了默认的主函数:

```
fn main() {
    println!("Hello, world!");
}

```

下一步似乎很简单:获取传递给应用程序的参数。因此，稍后编写一个单独的函数来提取其他参数:

```
fn get_arguments() {
    let args: Vec<_> = std::env::args().collect(); // get all arguements passed to app
    println!("{:?}", args);
}
fn main() {
    get_arguments();
}

```

当我们运行它时，我们得到了一个很好的输出，没有任何错误或混乱:

```
# anything after '--' is passed to your app, not to cargo
> cargo run -- svelte 
    Finished dev [unoptimized + debuginfo] target(s) in 0.01s
     Running `target/debug/package-hunter svelte`
["target/debug/package-hunter", "svelte"]

```

当然，第一个参数是调用应用程序的命令，第二个参数是传递给它的命令。似乎很简单。

### 编写一个计数函数

我们现在可以愉快地继续编写计数函数，它接受一个名称，并对子目录中具有该名称的目录进行计数:

```
use std::collections::VecDeque;
use std::fs;
use std::path::PathBuf;
/// Not the dracula
fn count(name: &str) -> std::io::Result<usize> {
    let mut count = 0;
    // queue to store next dirs to explore
    let mut queue = VecDeque::new();
    // start with current dir
    queue.push_back(PathBuf::from("."));
    loop {
        if queue.is_empty() {
            break;
        }
        let path = queue.pop_back().unwrap();
        for dir in fs::read_dir(path)? {
            // the for loop var 'dir' is actually a result, so we convert it
            // to the actual dir struct using ? here
            let dir = dir?;
            // consider it only if it is a directory
            if dir.file_type()?.is_dir() {
                if dir.file_name() == name {
                    // we have a match, so stop exploring further
                    count += 1;
                } else {
                    // not a match so check its sub-dirs
                    queue.push_back(dir.path());
                }
            }
        }
    }
    return Ok(count);
}

```

我们更新`get_arguments`来返回命令后的第一个参数，在`main`中，我们用那个参数调用`count`。

当我们在一个项目文件夹中运行它时，它出乎意料地完美工作，并返回计数为 1，因为一个项目只包含一次依赖项。

### 创建深度限制

现在，当我们转到一个目录，并尝试运行它时，我们注意到一个问题:它需要多一点时间，因为要遍历更多的目录。

理想情况下，我们希望从项目目录的根目录运行它，这样我们就可以找到所有具有该依赖关系的项目，但是这将花费更多的时间。

因此，我们决定妥协，只探索目录，直到一定的深度。如果目录的深度超过给定的深度，它将被忽略。我们可以向函数添加另一个参数，并更新它以考虑深度:

```
/// Not the dracula
fn count(name: &str, max_depth: usize) -> std::io::Result<usize> {
...
queue.push_back((PathBuf::from("."), 0));
...
let (path, crr_depth) = queue.pop_back().unwrap();
if crr_depth > max_depth {
    continue;
}
...
// not a match so check its sub-dirs
queue.push_back((dir.path(), crr_depth + 1));
...   
}

```

现在应用程序接受两个参数:首先是包名，然后是要探索的最大深度。

然而，我们希望深度是一个可选的参数，所以如果没有给定，它将探索所有的子目录，否则它将停止在给定的深度。

为此，我们可以更新`get_arguments`函数，使第二个参数可选:

```
fn get_arguments() {
    let args: Vec<_> = std::env::args().collect();
    let mdepth = if args.len() > 2 {
        args[2].parse().unwrap()
    } else {
        usize::MAX
    };
    println!("{:?}", count(&args[1], mdepth));
}

```

有了这个，我们可以双向运行它，而且它工作了:

```
> cargo run -- svelte
> cargo run -- svelte 5

```

不幸的是，这不是很灵活。当我们以相反的顺序给出参数时，比如`cargo run 5 package-name`，应用程序在试图将`package-name`解析为数字时崩溃。

### 添加标志

现在，我们可能希望参数有自己的标志，比如`-f`和`-d`，这样我们就可以按任何顺序给它们。(也为旗帜加分 Unix！)

我们再次更新了`get_arguments`函数，这次为参数添加了一个适当的结构，因此返回解析后的参数更加容易:

```
#[derive(Default)]
struct Arguments {
    package_name: String,
    max_depth: usize,
}
fn get_arguments() -> Arguments {
    let args: Vec<_> = std::env::args().collect();
    // atleast 3 args should be there : the command name, the -f flag, 
    // and the actual file name
    if args.len() < 3 {
        eprintln!("filename is a required argument");
        std::process::exit(1);
    }
    let mut ret = Arguments::default();
    ret.max_depth = usize::MAX;
    if args[1] == "-f" {
        // it is file
        ret.package_name = args[2].clone();
    } else {
        // it is max depth
        ret.max_depth = args[2].parse().unwrap();
    }
    // now that one argument is parsed, time for seconds
    if args.len() > 4 {
        if args[3] == "-f" {
            ret.package_name = args[4].clone();
        } else {
            ret.max_depth = args[4].parse().unwrap();
        }
    }
    return ret;
}

fn count(name: &str, max_depth: usize) -> std::io::Result<usize> {
...
}

fn main() {
    let args = get_arguments();
    match count(&args.package_name, args.max_depth) {
        Ok(c) => println!("{} uses found", c),
        Err(e) => eprintln!("error in processing : {}", e),
    }
}

```

现在，我们可以用像`cargo run -- -f svelte`或`cargo run -- -d 5 -f svelte`这样花哨的`-`标志来运行它。

### 参数和标志的问题

然而，这有一些非常严重的错误:我们可以给同一个参数两次，从而完全跳过文件参数`cargo run -- -d 5 -d 7`，或者我们可以给无效的标志，这样运行时没有任何错误消息😭。

我们可以通过检查上面第`27`行的`file_name`不为空来解决这个问题，并且可能在给出不正确的值时打印出预期的结果。但是，当我们向`-d`传递一个非数字时，这也会崩溃，因为我们在`parse`上直接调用`unwrap`。

此外，这个应用程序对于新用户来说可能很棘手，因为它不提供任何帮助信息。用户可能不知道将传递什么参数以及传递的顺序，并且应用程序没有像传统的 Unix 程序那样使用`-h`标志来显示这些信息。

尽管这些只是这个特定应用程序的一点点不便，但随着选项数量的增加，它们的复杂性也会增加，手动维护所有这些变得越来越困难。

这就是拍手的用武之地。

## 什么是拍手？

Clap 是一个库，它提供了为参数生成解析逻辑的功能，为应用程序提供了一个整洁的 CLI，包括对参数的解释和一个`-h`帮助命令。

使用 Clap 非常简单，只需要对我们现有的设置做一些小的改动。

Clap 有两个在很多 Rust 项目中使用的通用版本:V2 和 V3。V2 主要为构建命令行参数解析器提供了一个基于构建器的实现。

V3 是最近的版本(在撰写本文时)，它增加了`derive` [proc-macros](https://blog.logrocket.com/procedural-macros-in-rust/) 以及构建器实现，因此我们可以注释我们的结构，宏将为我们派生出必要的函数。

这两者都有各自的好处，要获得更详细的差异和特性列表，我们可以查看他们的[文档和帮助页面](https://github.com/clap-rs/clap)，它们提供了示例并建议了哪些情况适合使用 derive 和 builder。

在这篇文章中，我们将看到如何在 proc-macro 中使用 Clap V3。

## 将 Clap 添加到项目

要将 Clap 整合到我们的项目中，请在`Cargo.toml`中添加以下内容:

```
[dependencies]
clap = { version = "3.1.6", features = ["derive"] }

```

这将 Clap 作为一个依赖项添加到它的派生特性中。

现在，让我们从`main`中移除`get_arguments`函数及其调用:

```
use std::collections::VecDeque;
use std::fs;
use std::path::PathBuf;
#[derive(Default)]
struct Arguments {
    package_name: String,
    max_depth: usize,
}
/// Not the dracula
fn count(name: &str, max_depth: usize) -> std::io::Result<usize> {
    let mut count = 0;
    // queue to store next dirs to explore
    let mut queue = VecDeque::new();
    // start with current dir
    queue.push_back((PathBuf::from("."), 0));
    loop {
        if queue.is_empty() {
            break;
        }
        let (path, crr_depth) = queue.pop_back().unwrap();
        if crr_depth > max_depth {
            continue;
        }
        for dir in fs::read_dir(path)? {
            let dir = dir?;
            // we are concerned only if it is a directory
            if dir.file_type()?.is_dir() {
                if dir.file_name() == name {
                    // we have a match, so stop exploring further
                    count += 1;
                } else {
                    // not a match so check its sub-dirs
                    queue.push_back((dir.path(), crr_depth + 1));
                }
            }
        }
    }
    return Ok(count);
}
fn main() {}

```

接下来，在`Arguments`结构的`derive`中，添加`Parser`和`Debug`:

```
use clap::Parser;
#[derive(Parser,Default,Debug)]
struct Arguments {...}

```

最后，在`main`中，调用解析方法:

```
let args = Arguments::parse();
println!("{:?}", args);

```

如果我们用`cargo run`运行应用程序，没有任何参数，我们会得到一个错误消息:

```
error: The following required arguments were not provided:
    <PACKAGE_NAME>
    <MAX_DEPTH>

USAGE:
    package-hunter <PACKAGE_NAME> <MAX_DEPTH>

For more information try --help

```

这已经是比我们的手动版本更好的错误报告了！

另外，它会自动提供一个`-h`标志来帮助打印参数及其顺序:

```
package-hunter 

USAGE:
    package-hunter <PACKAGE_NAME> <MAX_DEPTH>

ARGS:
    <PACKAGE_NAME>    
    <MAX_DEPTH>       

OPTIONS:
    -h, --help    Print help information

```

现在，如果我们为`MAX_DEPTH`提供一个数字以外的东西，我们会得到一个错误，说提供的字符串不是一个数字:

```
> cargo run -- 5 test
error: Invalid value "test" for '<MAX_DEPTH>': invalid digit found in string

For more information try --help

```

如果我们以正确的顺序提供它们，我们会得到`println`的输出:

```
> cargo run -- test 5
Arguments { package_name: "test", max_depth: 5 }

```

所有这一切只需要两行新行和[不需要写任何解析代码](https://blog.logrocket.com/parsing-in-rust-with-nom/)或错误处理！🎉

## 更新帮助消息

目前，我们的帮助消息有点乏味，因为它只显示了参数的名称和顺序。如果用户能够看到特定参数的含义，甚至是应用程序版本，以防他们想要报告任何错误，这将对他们更有帮助。

Clap 还为此提供了选项:

```
#[derive(...)]
#[clap(author="Author Name", version, about="A Very simple Package Hunter")]
struct Arguments{...}

```

现在，`-h`输出显示了所有细节，还提供了一个`-V`标志来打印版本号:

```
package-hunter 0.1.0
Author Name
A Very simple Package Hunter

USAGE:
    package-hunter <PACKAGE_NAME> <MAX_DEPTH>

ARGS:
    <PACKAGE_NAME>    
    <MAX_DEPTH>       

OPTIONS:
    -h, --help       Print help information
    -V, --version    Print version information

```

在宏本身的[中写多行关于信息的内容可能有点乏味，所以我们可以使用`///`为结构添加一个文档注释，宏将使用它作为关于信息(如果两者都存在，宏中的注释优先于文档注释):](https://blog.logrocket.com/macros-in-rust-a-tutorial-with-examples/)

```
#[clap(author = "Author Name", version, about)]
/// A Very simple Package Hunter
struct Arguments {...}

```

这提供了与以前相同的帮助。

要添加关于参数的信息，我们可以向参数本身添加类似的注释:

```
package-hunter 0.1.0
Author Name
A Very simple Package Hunter

USAGE:
    package-hunter <PACKAGE_NAME> <MAX_DEPTH>

ARGS:
    <PACKAGE_NAME>    Name of the package to search
    <MAX_DEPTH>       maximum depth to which sub-directories should be explored

OPTIONS:
    -h, --help       Print help information
    -V, --version    Print version information

```

这就有帮助多了！

现在，让我们带回我们拥有的其他特性，比如参数标志(`-f`和`-d`)和设置可选的深度参数。

## 将标志添加到 Clap 中

Clap 使标志参数简单得可笑:我们只需用`#[clap(short, long)]`向结构成员添加另一个 Clap 宏注释。

这里，`short`指的是国旗的速记版，如`-f`，`long`指的是完整版，如`--file`。我们可以任选其一，也可以两者都选。添加后，我们现在有以下内容:

```
package-hunter 0.1.0
Author Name
A Very simple Package Hunter

USAGE:
    package-hunter --package-name <PACKAGE_NAME> --max-depth <MAX_DEPTH>

OPTIONS:
    -h, --help                           Print help information
    -m, --max-depth <MAX_DEPTH>          maximum depth to which sub-directories should be explored
    -p, --package-name <PACKAGE_NAME>    Name of the package to search
    -V, --version                        Print version information

```

由于两个参数都有标志，现在没有位置参数了；这意味着我们不能运行`cargo run -- test 5`,因为 Clap 会寻找标志，并给出一个错误，指出参数没有提供。

相反，我们可以运行`cargo run -- -p test -m 5`或`cargo run -- -m 5 -p test`,它将正确解析两者，给出如下输出:

```
Arguments { package_name: "test", max_depth: 5 }

```

因为我们总是需要包名，所以我们可以让它成为一个位置参数，这样我们就不需要每次都键入`-p`标志。

为此，将`#[clap(short,long)]`从其中移除；现在，没有任何标志的第一个参数将被视为`package name`:

```
> cargo run -- test -m 5
Arguments { package_name: "test", max_depth: 5 }
> cargo run -- -m 5 test
Arguments { package_name: "test", max_depth: 5 }

```

在速记参数中需要注意的一点是，如果两个参数以同一个字母开始，即`package-name`和`path`，并且都启用了一个短标志，那么应用程序将在运行时因调试版本而崩溃，并为发布版本给出一些令人困惑的错误消息。

因此，请确保:

*   所有的争论都是从不同的字母开始的
*   只有一个具有相同起始字母的参数有一个`short`标志

下一步是使`max_depth`可选。

### 使一个论点成为可选的

要将任何参数标记为可选，只需将该参数的类型设为`Option<T>`，其中`T`是原始类型参数。在我们的例子中，我们有以下内容:

```
#[clap(short, long)]
/// maximum depth to which sub-directories should be explored
max_depth: Option<usize>,

```

这应该能行。这种变化也反映在帮助中，其中没有将最大深度列为必需参数:

```
package-hunter 0.1.0
Author Name
A Very simple Package Hunter

USAGE:
    package-hunter [OPTIONS] <PACKAGE_NAME>

ARGS:
    <PACKAGE_NAME>    Name of the package to search

OPTIONS:
    -h, --help                     Print help information
    -m, --max-depth <MAX_DEPTH>    maximum depth to which sub-directories should be explored
    -V, --version                  Print version information

```

而且，我们可以在不给出`-m`标志的情况下运行它:

```
> cargo run -- test
Arguments { package_name: "test", max_depth: None }

```

但是，这仍然有点麻烦；现在我们必须在`max_depth`上运行`match`，如果是`None`，我们像以前一样把它设置为`usize::MAX`。

然而，Clap 在这里也为我们带来了一些东西！如果参数没有给定，我们可以设置它的默认值，而不是使它成为`Option<T>`。

所以这样修改之后:

```
#[clap(default_value_t=usize::MAX,short, long)]
/// maximum depth to which sub-directories should be explored
max_depth: usize,

```

我们可以在提供或不提供`max_depth`值的情况下运行应用程序(`usize`的最大值取决于您的系统配置):

```
> cargo run -- test
Arguments { package_name: "test", max_depth: 18446744073709551615 }
> cargo run -- test -m 5
Arguments { package_name: "test", max_depth: 5 }

```

现在，让我们像以前一样把它挂在`main`中的计数函数上:

```
fn main() {
    let args = Arguments::parse();
    match count(&args.package_name, args.max_depth) {
        Ok(c) => println!("{} uses found", c),
        Err(e) => eprintln!("error in processing : {}", e),
    }
}

```

这样，我们就恢复了原来的功能，但代码更少，还增加了一些额外的功能！

## 修复空字符串错误

`package-hunter`的性能和预期的一样，但是不幸的是，有一个微妙的错误从手动解析阶段就存在了，并且带到了基于 Clap 的版本中。你能猜出它是什么吗？

尽管对于我们的小应用程序来说，这不是一个非常危险的错误，但它可能是其他应用程序的致命弱点。在我们的例子中，当它应该给出一个错误时，它会给出一个错误的结果。

尝试运行以下命令:

```
> cargo run -- ""
0 uses found

```

这里，`package_name`作为一个空字符串传入，而不允许使用空的包名。这是由于我们运行命令的 shell 将参数传递给我们的应用程序的方式造成的。

通常，shell 使用空格来拆分传递给程序的参数列表，因此`abc def hij`将作为三个独立的参数给出:`abc`、`def`和`hij`。

如果我们想在一个论点中包含空格，我们必须用引号将它括起来，就像`"abc efg hij"`。这样，shell 就知道这是一个单独的参数，并照此传递它。

另一方面，这也允许我们将空字符串或只有空格的字符串传递给应用程序。再次鼓掌救援！它提供了一种拒绝参数的空值的方法:

```
#[clap(forbid_empty_values = true)]
/// Name of the package to search
package_name: String,

```

这样，如果我们试图给出一个空字符串作为参数，我们会得到一个错误:

```
> cargo run -- ""
error: The argument '<PACKAGE_NAME>' requires a value but none was supplied

```

但是，这仍然提供了空格作为包名，这意味着`""`是一个有效的参数。要解决这个问题，我们必须提供一个定制的验证器，它将检查名称是否有任何前导或尾随空格，如果有，将拒绝它。

我们将验证函数定义如下:

```
fn validate_package_name(name: &str) -> Result<(), String> {
    if name.trim().len() != name.len() {
        Err(String::from(
            "package name cannot have leading and trailing space",
        ))
    } else {
        Ok(())
    }
}

```

然后，对`package_name`进行如下设置:

```
#[clap(forbid_empty_values = true, validator = validate_package_name)]
/// Name of the package to search
package_name: String,

```

现在，如果我们试图传递一个空字符串或带空格的字符串，它会给出一个错误，就像它应该的那样:

```
> cargo run -- "" 
error: The argument '<PACKAGE_NAME>' requires a value but none was supplied
> cargo run -- " "
error: Invalid value " " for '<PACKAGE_NAME>': package name cannot have leading and trailing space

```

这样，我们就可以用定制的逻辑来验证参数，而无需编写解析它的所有代码。

## 用拍手伐木

应用程序现在工作正常，但是我们没有办法看到在它不工作的情况下发生了什么。为此，我们应该记录我们的应用程序正在做什么，以查看它崩溃时发生了什么。

就像其他命令行应用程序一样，我们应该有一种方法让用户轻松地设置日志的级别。默认情况下，它应该只记录主要的细节和错误，这样日志就不会混乱，但是在应用程序崩溃的情况下，应该有一种模式来记录所有可能的事情。

像其他应用程序一样，让我们的应用程序使用一个`-v`标志来获取详细级别；无标志为最小测井，`-v`为中间测井，`-vv`为最大测井。

为了做到这一点，Clap 提供了一种方法，使得参数的值被设置为它出现的次数，这正是我们在这里所需要的！我们可以添加另一个参数，并将其设置如下:

```
#[clap(short, long, parse(from_occurrences))]
verbosity: usize,

```

现在，如果我们在没有给它一个`-v`标志的情况下运行它，它的值将为零，否则计算`-v`标志出现的次数:

```
> cargo run -- test
Arguments { package_name: "test", max_depth: 18446744073709551615, verbosity: 0 }
> cargo run -- test -v
Arguments { package_name: "test", max_depth: 18446744073709551615, verbosity: 1 }
> cargo run -- test -vv
Arguments { package_name: "test", max_depth: 18446744073709551615, verbosity: 2 }
> cargo run -- -vv test -v
Arguments { package_name: "test", max_depth: 18446744073709551615, verbosity: 3 }

```

使用这个值，我们可以很容易地初始化记录器，并让它记录适当数量的细节。

我没有在这里添加虚拟日志程序代码，因为这篇文章关注的是参数解析，但是您可以在最后的存储库中找到它。

## 清点和查找项目

既然我们的应用程序运行良好，我们想添加另一个功能:列出我们拥有的项目。这样，当我们想要一个好的项目列表时，我们可以很快得到。

Clap 有一个强大的子命令功能，可以为 app 提供多个子命令。若要使用它，请定义另一个具有自己的参数的结构，该结构将成为子命令。主参数结构包含所有子命令共有的参数，然后是子命令。

我们将按照以下方式构建我们的 CLI:

*   log verbosity 和`max_depth`参数将位于主结构中
*   count 命令将使用文件名来查找并输出计数
*   `projects`命令采用一个可选的开始路径来开始搜索
*   `projects`命令接受一个可选的排除路径列表，它会跳过给定的目录

因此，我们添加计数和项目枚举，如下所示:

```
use clap::{Parser, Subcommand};
...
#[derive(Subcommand, Debug)]
enum SubCommand {
    /// Count how many times the package is used
    Count {
        #[clap(forbid_empty_values = true, validator = validate_package_name)]
        /// Name of the package to search
        package_name: String,
    },
    /// list all the projects
    Projects {
        #[clap(short, long, default_value_t = String::from("."),forbid_empty_values = true, validator = validate_package_name)]
        /// directory to start exploring from
        start_path: String,
        #[clap(short, long, multiple_values = true)]
        /// paths to exclude when searching
        exclude: Vec<String>,
    },
}

```

这里，我们将`package_name`移动到`Count`变体，并在`Projects`变体中添加`start_path`和`exclude`选项。

现在，如果我们检查帮助，它会列出这两个子命令，并且每个子命令都有自己的帮助。

然后我们可以更新主函数来适应它们:

```
let args = Arguments::parse();
match args.cmd {
    SubCommand::Count { package_name } => match count(&package_name, args.max_depth, &logger) {
        Ok(c) => println!("{} uses found", c),
        Err(e) => eprintln!("error in processing : {}", e),
    },
    SubCommand::Projects {
        start_path,
        exclude,
    } => {/* TODO */}
}

```

我们也可以像以前一样使用`count`命令来统计使用次数:

```
> cargo run -- -m 5 count test

```

由于`max_depth`是在主`Arguments`结构中定义的，所以它必须在子命令之前给出。

然后，我们可以根据需要为项目命令的排除目录提供多个值:

```
> cargo run -- projects -e ./dir1 ./dir2
["./dir1", "./dir2"] # value of exclude vector

```

我们还可以设置一个自定义分隔符，以防我们不希望值由空格分隔，而是由自定义字符分隔:

```
#[clap(short, long, multiple_values = true, value_delimiter = ':')]
/// paths to exclude when searching
exclude: Vec<String>,

```

现在我们可以使用`:`来分隔值:

```
> cargo run -- projects -e ./dir1:./dir2
["./dir1", "./dir2"]

```

这就完成了应用程序的 CLI。这里没有显示项目列表函数，但是您可以尝试自己编写它，或者在 GitHub 存储库中检查它的代码。

## 结论

现在您已经了解了 Clap，您可以为您的项目创建干净优雅的 CLI 了。它有许多其他特性，如果您的项目需要命令行的特定功能，Clap 很可能已经有了。

你可以查看 [Clap 文档](https://docs.rs/clap/latest/clap/)和 Clap [GitHub 页面](https://github.com/clap-rs/clap)来查看关于 Clap 库提供的选项的更多信息。

[你也可以在这里](https://github.com/YJDoc2/LogRocket-Blog-Code/tree/main/arg-parsing-with-clap)获得该项目的代码。感谢您的阅读！

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。