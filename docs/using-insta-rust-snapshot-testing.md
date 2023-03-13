# 使用 Insta 进行生锈快照测试

> 原文：<https://blog.logrocket.com/using-insta-rust-snapshot-testing/>

如果您在您的应用程序中使用 Rust，您很可能在一个代码可靠性和效率是您开发过程的焦点的环境中工作。尽管 Rust 提供了一个具有丰富的内存安全特性的编译器，但是当涉及到业务流程和应用程序逻辑时，您仍然需要测试您的代码以确保它按照预期执行，尤其是在变化是一个持续因素的环境中。

Rust 有很多测试策略，从单元测试到集成测试。在本文中，我们将探索使用 [Insta](https://github.com/mitsuhiko/insta) 的快照测试，了解它如何补充您的开发工作。接下来，你可以在 GitHub 上找到完整的样板[项目。我们开始吧！](https://github.com/agustinustheo/rust-todo-cli-insta-test/tree/step-1)

*向前跳转:*

## 什么是快照测试？

快照测试是一种通过将输出与一组已定义的期望值进行比较来验证代码正确性的方法。例如，如果您以前编写过集成测试，那么您的部分测试可以被视为快照，因为您是在将预期结果与实际输出进行比较。

默认情况下，Rust 已经使用 [`assert_eq!`](https://doc.rust-lang.org/std/macro.assert_eq.html) 函数拥有这个特性，但是它只允许你与原始 Rust 类型进行比较。快照测试要求您对更复杂的数据结构进行比较。

通常，快照测试是在前端而不是后端进行的，因为前端应用程序返回 HTML 而不是常规字符串。与其解析 HTML 并检查每个特定的元素，不如比较输出更节省时间。

在测试整个程序的输出时，您可以充分利用快照测试的潜力，从而测试网页中的更多元素，而不必担心所有结果是否一致。

## Insta 是什么？

[Insta](https://insta.rs) 是 Rust 应用程序的快照测试库，为您运行和更新测试提供了一个简单直观的界面:

![Insta Interface Example](img/bdaa73c8b822ce9a95b7dc00f6db525e.png)

正如你在上面的截图中看到的，在 Insta 中调试测试非常容易，在 Insta CLI 的帮助下，你可以很容易地用更新的测试输出来更新所有失败的测试输出。请记住，您不应该在每次出现故障时都更新结果。您应该只在更改某个测试的代码输出时才实现更新，从而最大限度地减少代码更新导致的错误数量。

Insta 仅通过 [Serde 支持 CSV、JSON、TOML、YAML 和 RON 文件，Serde 是一个数据序列化库](https://blog.logrocket.com/json-and-rust-why-serde_json-is-the-top-choice/)，它可以将各种类型的数据结构编码为更紧凑的格式，反之亦然。

## Insta 如何工作

Insta 有许多不同风格的支持。如前所述，您可以使用 Insta 对 JSON 文件、CSV 文件甚至 YAML 文件进行快照测试。但是，有趣的是 Insta 宏是如何在引擎盖下运行的。

Insta 使用 Serde 提供多文件支持。尽管如此，Insta 并没有将它们分成更小的包裹，而是依靠[货运公司的`features`](https://github.com/mitsuhiko/insta/blob/08a9ec31d3725f0620d9d302357cf017ee70bcf4/Cargo.toml#L40-L44) 将所有包裹作为一个整体无缝运输，因此客户可以通过`features`只下载他们需要的东西:

```
// Cargo.toml
[features]
csv = ["dep_csv", "serde"]
json = ["serde"]
ron = ["dep_ron", "serde"]
toml = ["dep_toml", "serde"]
yaml = ["serde"]

```

主 Insta 快照断言库只比较两个字符串。所以，最后，只要您通过了`SerializationFormat`，宏`assert_snapshot!`就会编译并正常工作。

下图演示了断言库流程:

![Insta Assertion Library Flow](img/aa117b7437b2c17443dec6d9b72b6fcc.png)

[主运行时](https://github.com/mitsuhiko/insta/blob/08a9ec31d3725f0620d9d302357cf017ee70bcf4/src/runtime.rs#L559)仅由`assert_snapshot!`组成，而其他宏作为包装器。`assert_snapshot!`宏中的过程非常简单。Insta [通过`.snap`文件读取旧快照](https://github.com/mitsuhiko/insta/blob/08a9ec31d3725f0620d9d302357cf017ee70bcf4/src/runtime.rs#L569)，[用`SnapshotAssertionContext::new_snapshot`创建新快照](https://github.com/mitsuhiko/insta/blob/08a9ec31d3725f0620d9d302357cf017ee70bcf4/src/runtime.rs#L584)，运行基本比较，然后如果值相等则返回`Ok(())`。

宏包装器存在于 [`macros.rs`文件](https://github.com/mitsuhiko/insta/blob/master/src/macros.rs)中，文件将调用 [`_assert_serialized_snapshot`函数](https://github.com/mitsuhiko/insta/blob/08a9ec31d3725f0620d9d302357cf017ee70bcf4/src/macros.rs#L308)，函数将它们一起通过管道传输到`assert_snapshot`。每个宏包装器都有一个硬编码的`SerializedFormat`。所有的序列化都位于 [`serialization.rs`文件](https://github.com/mitsuhiko/insta/blob/08a9ec31d3725f0620d9d302357cf017ee70bcf4/src/serialization.rs#L42)和[`content`目录](https://github.com/mitsuhiko/insta/tree/master/src/content)中。

## 敦促 vs .`assert_eq`

在引擎盖下，Insta 和`assert_eq`都做序列化以外的事情。两种断言解决方案的最大区别是 Insta 原生支持序列化。同时，你必须使用 Serde 进行手动序列化，以达到使用`assert_eq!`时与 Insta 相同的结果。

即使在`assert_snapshot`函数内部，Insta 也会进行简单的字符串间比较。使用`assert_eq`将获得类似的结果。`assert_eq`的比较过程要比 Insta 的轻量级得多，而且与直接使用 Insta 相比，将 Insta 与`assert_eq`进行比较并不理想，因为这需要大量样板代码和额外的工作。

## Insta 入门

安装 Insta 很简单。像任何其他货物包一样，打开您的`Cargo.toml`并添加您的依赖项。在这种情况下，您将添加 Insta 和 Serde:

```
// Cargo.toml // ...
[features]
json = ["serde"]

[dependencies]
serde = { version = "1.0.117", optional = true }

[dev-dependencies]
insta = { version = "1.26.0", features = ["json", "csv"] }
serde = { version = "1.0.117", features = ["derive"] }

```

在这个例子中，我们将使用`json`和`csv`特性来制作一个您可以测试的简单程序。我们将创建一个简单的待办事项列表 CLI 应用程序来跟踪一段时间内的任务。

首先，在`src/main.rs`文件中创建一个基本的样板文件:

```
// main.rs
use std::env;
use std::io::{self, BufRead};
use std::path::Path;

struct Task {
    name: String,
    is_completed: bool,
}

fn readline() -> String {
    let mut strr: String = "".to_string();
    let stdin = io::stdin();
    for line in stdin.lock().lines() {
        strr = line.unwrap().to_string();
        break;
    }
    strr
}

fn main() -> io::Result<()> {
    let mut tasks: Vec<Task> = vec![];
    while true {
        println!("{}", readline());
        break;
    }
    Ok(())
}
fn add_task(tasks: &Vec<Task>) -> io::Result<()> {
    // TODO: Add logic
    Ok(())
}
fn list_tasks(tasks: &Vec<Task>) -> io::Result<()> {
    // TODO: List logic
    Ok(())
}
fn complete_task(tasks: &Vec<Task>) -> io::Result<()> {
    // TODO: Complete logic
    Ok(())
}

```

这个简单的 CLI 将允许用户创建或更新新任务。代码的结构可能会给你一些提示，告诉你我们将使用 Insta 做什么。但是，我们不要想得太多。

接下来，我们将定义每个函数来添加更多的结构，重点是`main`函数:

```
fn main() -> io::Result<()> {
    let mut tasks: Vec<Task> = vec![];
    loop {
        list_tasks(&tasks);

        let option = readline();
        _ = match option.as_str() {
            "1" => {
                println!("Enter new task name: ");
                let name = readline();
                add_task(&mut tasks, name);
            },
            "2" => {
                println!("Enter task to complete: ");
                let level: i32 = readline().parse::<i32>().unwrap();
                complete_task(&mut tasks, level);
            },
            _ => break,
        };
    }
    Ok(())
}

```

`main`功能会将你的命令重定向到应用程序的其他功能，或者列出任务，创建新任务，或者完成它们。为了简化事情，现在，我们不会有一个任务删除功能。但是，如果需要，您可以稍后实现一个。

CRUD 是创建、读取、更新和删除的缩写。暂时不介意删除部分。我们将从创建函数`add_task`开始。

由于一个可变的`tasks`向量从`main`传递给了`add_task`函数，您可以使用`.push`修饰符向该向量添加一个新项:

```
fn add_task(tasks: &mut Vec<Task>, name: String) -> io::Result<()> {
    tasks.push(Task {
        name: name,
        is_completed: false,
    });
    Ok(())
}

```

### 阅读

每次打开 CLI 时，您都希望列出任务列表。`list_tasks`是已经在`loop`开始时声明的`main`；你需要做的就是定义它。为了使事情更简单，将`tasks`向量传递给`list_tasks`函数。然后，迭代它们并打印它们的名称和状态:

```
fn list_tasks(tasks: &Vec<Task>) {
    for _ in 0..50 {
        println!("\n");
    }
    println!("Tasks List: ");
    for task in tasks {
        println!("Name: {}", task.name);
        println!("Is Completed: {}", task.is_completed);
    }
    println!("Choose the following options:
1\. Add tasks
2\. Complete tasks
3\. Exit");
}

```

Rust 没有清晰的 CLIs 屏幕选项；你可以通过打印 50 次白线来解决这个问题。

最后，要更新任务，只需更新它们的状态。可以直接访问矢量项，修改`is_completed`属性:

```
fn complete_task(tasks: &mut Vec<Task>, level: i32) -> io::Result<()> {
    tasks[level as usize].is_completed = true;
    Ok(())
}

```

现在，尝试运行应用程序。你应该能够创建和完成新的任务。转到您的终端并键入`cargo run`，您应该会看到如下内容:

![Insta Simple Example Application New Task](img/dda6f76fa81da640e012e8bf84053ef1.png)

它不是最复杂的应用程序，但对于我们的教程来说，它是有效的。输入`1`并按**进入**。重命名任务`new task`，您将收到以下内容:

![Add New Task Insta Example App](img/a0354434b2fa9013fbebb79f1736750d.png)

如果你很难理解，你可以在 GitHub 分支的[中看到我们到目前为止所做的事情。现在，您已经验证了一切都在运行，您可以继续进行快照测试。](https://github.com/agustinustheo/rust-todo-cli-insta-test/tree/step-2)

Serde 是可选的依赖项，Insta 是开发依赖项，所以不能将它们包含在主应用程序上下文中。您必须在它们前面加上一个`#[cfg(test)]`宏。如下复制`Task`结构:

```
#[cfg(test)]
#[derive(serde::Serialize, Clone)]
struct Task {
    pub name: String,
    pub is_completed: bool,
}

#[cfg(not(test))]
#[derive(Clone)]
struct Task {
    pub name: String,
    pub is_completed: bool,
}

```

测试中使用的`Task`将是可序列化和可克隆的，因此我们可以存储同一个对象的多个副本，而不会破坏它。我们将在测试中使用带有`#[cfg(test)]`的结构，在运行带有`cargo run`的项目时使用`#[cfg(not(test))]`。我们将为不同的上下文分离结构；虽然这不是最佳实践，但它将节省您的时间，让您专注于更重要的 Insta 测试。

为了使`add_task`和`complete_task`可测试，它们必须在每次运行时返回一个`Task`结构:

```
fn add_task(tasks: &mut Vec<Task>, name: String) -> io::Result<Task> {
    let task = Task {
        name: name,
        is_completed: false,
    };
    tasks.push(task.clone());
    Ok(task)
}

// fn list_tasks....

fn complete_task(tasks: &mut Vec<Task>, level: i32) -> io::Result<Task> {
    tasks[level as usize].is_completed = true;
    Ok(tasks[level as usize].clone())
}

```

先前添加的`Clone`导数将用于`add_task`功能。要使用 Insta 为该函数编写单元测试，请在文件底部添加以下代码:

```
#[cfg(test)]
extern crate insta;

```

然后，像这样测试`add_task`函数:

```
#[cfg(test)]
mod tests {
    use super::*;
    use insta::{assert_json_snapshot, assert_compact_json_snapshot, assert_csv_snapshot};

    #[test]
    fn test_json_add_task_struct_vec() {
        let mut tasks: Vec<Task> = vec![];

        let task: Task = add_task(&mut tasks, "name".to_string()).unwrap();
        assert_json_snapshot!(task, @r###"{
  "name": "name",
  "is_completed": false
}"###);
        assert_compact_json_snapshot!(task, @r###"{"name": "name", "is_completed": false}"###);
        assert_json_snapshot!(tasks, @r###"[
  {
    "name": "name",
    "is_completed": false
  }
]"###);
        assert_compact_json_snapshot!(tasks, @r###"[{"name": "name", "is_completed": false}]"###);
    }
}

```

通过在终端或命令提示符下执行`cargo test`命令来运行测试。所有测试都应该成功通过，您就大功告成了！您仍然可以添加更多的测试，如果您想看到最终的项目，[请访问 GitHub 分支](https://github.com/agustinustheo/rust-todo-cli-insta-test/tree/step-3)。

如果您对这个项目中的代码或这些功能的测试有任何问题，请随意在 GitHub 库中[创建一个问题。](https://github.com/agustinustheo/rust-todo-cli-insta-test/issues/new)

## 结论

当开发人员编写应用时，[他们面临测试的挑战](https://blog.logrocket.com/end-to-end-testing-for-rust-web-services/)。在理想的情况下，我们应该能够运行我们的代码，并确保在将它部署到生产环境之前，它能按预期工作。然而，现实世界的软件开发远非理想，因此测试是我们开发工作流程中必不可少的一部分。在开发不允许出现故障的关键任务系统或应用程序时，尤其如此。

除了使用 C/C++，像 Rust 这样提供严格类型系统的语言正越来越多地成为任务关键型应用程序的选择，尤其是在考虑速度和内存安全的情况下。

快照测试通过验证输出来帮助您验证代码的正确性。如果你正在管理一个不断变化的代码库，这是非常有用的，所以当你进行更新或更改时，你会发现是否有什么东西坏了。

Insta 提供了通用断言宏的包装器，供您在程序中使用。考虑到项目的需求，通过对它们进行测试并为部署做好准备，Insta 可以处理大部分样板代码，否则您可能不得不自己动手。

Insta 有很多特性超出了本文的范围，但是你可以在[官方文档页面](https://insta.rs/docs/)上阅读更多内容，并观看 Insta 的[官方视频讲解。](https://www.youtube.com/watch?v=rCHrMqE4JOY)

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。