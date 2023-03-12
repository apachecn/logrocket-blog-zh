# Rust 的电子邮件箱:lettre 和 imap 

> 原文：<https://blog.logrocket.com/email-crates-for-rust-lettre-and-imap/>

如今，大多数 web 应用程序都需要以某种方式与电子邮件交互，因此良好的语言支持至关重要。Rust 当然有处理电子邮件的基本支持，但我不会说它能覆盖所有的用例。这个生态系统非常小，目前还没有很好地支持 async/await。

也就是说，尝试现有的解决方案，看看它们是如何工作的，这是一个有用的练习。在本教程中，我们将向您展示如何在 Rust 中发送电子邮件和连接到 IMAP 服务器。

## 用信件创建电子邮件

发送电子邮件最流行的板条箱是 [`lettre`](https://crates.io/crates/lettre) 。截至目前，letter (0.9)的最新稳定版本不包括异步 API，但 0.10 版本也有 alpha 版本，支持异步 via [`tokio`](https://crates.io/crates/tokio) 和 [`async-std`](https://crates.io/crates/async-std) 。

让我们先来看看没有异步的当前版本，然后使用 alpha 版本将代码移植到异步实现。

将以下内容添加到`Cargo.toml`即可开始。

```
[dependencies]
lettre = "0.9"
lettre_email = "0.9"

```

在 0.9 版本中，我们需要使用`lettre_email`来创建电子邮件，然后使用`lettre`来发送它们，所以我们需要指定两者。

首先创建一个基本的电子邮件。

```
use lettre_email::EmailBuilder;

fn main() {
    let email = EmailBuilder::new()
        .to("[email protected]")
        .from("[email protected]")
        .subject("Example subject")
        .text("Hello, world!")
        .build()
        .unwrap();
}

```

这使用构建器模式来创建表示电子邮件的数据结构。如果我们想指定一个名字和一个地址，我们可以使用一个元组，就像这样:

```
let email = EmailBuilder::new()
    .to(("[email protected]", "Alice Smith"))
    .from(("[email protected]", "Bob Smith"))
    .subject("Example subject")
    .text("Hello, world!")
    .build()
    .unwrap();

```

添加 HTML 主体就像在构建器上调用相应的方法一样简单。

```
let email = EmailBuilder::new()
    .to("[email protected]")
    .from("[email protected]")
    .subject("Example subject")
    .html("<h1>Hello, world!</h1>")
    .build()
    .unwrap();

```

假设我们想在电子邮件中发送附件。以下是将本地文件附加到电子邮件的方法:

```
use lettre_email::mime;
use lettre_email::EmailBuilder;
use std::path::Path;

fn main() {
    let email = EmailBuilder::new()
        .to("[email protected]")
        .from("[email protected]")
        .subject("Example subject")
        .html("<h1>Hello, world!</h1>")
        .attachment_from_file(
            &Path::new("path/to/file.pdf"), // Path to file on disk
            Some("Cookie-recipe.pdf"),      // Filename to use in the email
            &mime::APPLICATION_PDF,
        )
        .unwrap()
        .build()
        .unwrap();
}

```

或者，我们可以使用`attachment(...)` builder 方法直接从内存中发送一个字节向量，而不是磁盘上的一个文件。

对于我没有提到的东西，还有一些构建器方法，所以如果你想对电子邮件做些别的事情，看看`lettre_email::EmailBuilder` 的[文档。](https://docs.rs/lettre_email/0.9.4/lettre_email/struct.EmailBuilder.html)

## 用信件发送电子邮件

有了 lettre，我们可以使用任何实现了 [`lettre::Transport`](https://docs.rs/lettre/0.9.3/lettre/trait.Transport.html) 特征的东西来发送我们之前创建的电子邮件。

最容易上手的交通工具是 [`StubTransport`](https://docs.rs/lettre/0.9.3/lettre/stub/struct.StubTransport.html) 。顾名思义，这只是一个存根，而不是真正的传输。这对测试非常有用。

```
use lettre::stub::StubTransport;
use lettre::Transport;
use lettre_email::EmailBuilder;

fn main() {
    let email = EmailBuilder::new()
        .to("[email protected]")
        .from("[email protected]")
        .subject("Example subject")
        .html("<h1>Hello, world!</h1>")
        .build()
        .unwrap();

    let mut mailer = StubTransport::new_positive();

    let result = mailer.send(email.into());

    println!("{:?}", result);
}

```

因为我们使用了`new_positive()`来创建存根传输，所以`send(...)`方法总是成功的。如果你想强制发送失败，查看文档中的`StubTransport::new(...)`。

为了让它实际发送电子邮件，我们只需要用一个真正的传输来替换存根`mailer`。对于这个例子，让我们使用`SmtpTransport`。

因为 SMTP 传输比存根有更多的配置选项，所以它使用一个名为`SmtpClient`的构建器。

```
use lettre::smtp::authentication::Credentials;
use lettre::{SmtpClient, Transport};
use lettre_email::EmailBuilder;

fn main() {
    let email = EmailBuilder::new()
        .to("[email protected]")
        .from("[email protected]")
        .subject("Example subject")
        .html("<h1>Hello, world!</h1>")
        .build()
        .unwrap();

    let mut mailer = SmtpClient::new_simple("smtp.hello.com")
        .unwrap()
        .credentials(Credentials::new("username".into(), "password".into()))
        .transport();

    let result = mailer.send(email.into());

    println!("{:?}", result);
}

```

现在，这实际上将通过您连接的 SMTP 服务器发送电子邮件。在这里，我们使用`new_simple(...)`创建了一个客户端，它使用提供的域构建一个加密传输来验证 TLS 证书。这是创建`SmtpTransport`的推荐方式。幸运的是，这也是最简单的。之后，我们只是传递一些凭证，然后发送电子邮件，就像一个存根。

## 移植到异步/等待

如果你喜欢生活在风口浪尖上，你可能想尝试一下 lettre 的 alpha 版本的异步支持。lettre 的 0.10 版本也将在一个特性标志下包含电子邮件生成器，所以我们不再需要使用`lettre_email`。

首先，改变你的`Cargo.toml`依赖。

```
[dependencies]
lettre = { version = "0.10.0-alpha.2", features = ["builder", "tokio02-native-tls"] }
tokio = { version = "0.2", features = ["macros"] }

```

注意，我们启用了构建电子邮件和 tokio 运行时支持的可选特性。

现在，创建和发送电子邮件的代码如下所示:

```
use lettre::transport::smtp::authentication::Credentials;
use lettre::{AsyncSmtpTransport, Message, Tokio02Connector, Tokio02Transport};

#[tokio::main]
async fn main() {
    env_logger::init();

    let email = Message::builder()
        .to("[email protected]".parse().unwrap())
        .from("[email protected]".parse().unwrap())
        .subject("Example subject")
        .body("Hello, world!")
        .unwrap();

    let mailer = AsyncSmtpTransport::<Tokio02Connector>::relay("smtp.hello.com")
        .unwrap()
        .credentials(Credentials::new("username".into(), "password".into()))
        .build();

    let result = mailer.send(email).await;

    println!("{:?}", result);
}

```

这里的主要变化是我们用`lettre::Message`代替了`lettre_email::EmailBuilder`，用`lettre::AsyncSmtpTransport`代替了`lettre::SmtpClient`。小心这个版本中的其他重大变化。例如，我们现在需要在将地址传递给消息构建器函数中的`to(...)`和`from(...)`之前解析地址。

另外，请注意 lettre 0.10 是一个 alpha 版本，还不支持 0.9 的所有功能。大多数应用程序不需要异步就可以了，所以在这个版本稳定之前，请继续使用我们之前看到的同步版本。

## 因特网邮件访问协议

对于许多应用程序来说，发送电子邮件更加重要，但是您可能会遇到监控和交互接收电子邮件的需求。目前在 Rust 中最好的方法是使用`[imap](https://crates.io/crates/imap)`板条箱。像 lettre 的当前版本一样，这个机箱不提供异步 API。

首先，创建一个包含这些依赖项的新项目。

```
[dependencies]
imap = "2"
native-tls = "0.2"

```

接下来，连接到 IMAP 服务器。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
use native_tls::TlsConnector;

fn main() {
    let domain = "imap.example.com";
    let tls = TlsConnector::builder().build().unwrap();
    let client = imap::connect((domain, 993), domain, &tls).unwrap();
}

```

我们需要将域作为要连接的地址传递一次，并作为验证 TLS 证书的域传递一次。

客户端未经身份验证，接下来让我们登录。

```
let mut imap_session = client.login("[email protected]", "password").unwrap();

```

现在我们有了一个 [`imap::Session`](https://docs.rs/imap/2.3.0/imap/struct.Session.html) 可以用来与我们的远程邮箱进行交互。

例如，让我们阅读收件箱中前五封邮件的正文。

```
imap_session.select("INBOX").unwrap();

let messages = imap_session.fetch("1,2,3,4,5", "RFC822").unwrap();

for message in messages.iter() {
    if let Some(body) = message.body() {
        println!("{}", std::str::from_utf8(body).unwrap());
    } else {
        println!("Message didn't have a body!");
    }
}

imap_session.logout().unwrap();

```

首先，我们选择想要使用的邮箱—在本例中是`"INBOX"`。然后，我们获取收件箱中的消息号`1`到`5`。您在 fetch 方法中看到的`"RFC822"`决定了电子邮件正文的格式。一旦我们得到一个包含消息的响应，我们就对它们进行迭代，提取每个主体并打印出来。最后，我们通过注销来结束我们的会话。

参见 [`Fetch`类型](https://docs.rs/imap/2.3.0/imap/types/struct.Fetch.html)以获取关于您还能从消息中得到什么的文档。

除了阅读电子邮件，让我们来看看如何管理您的邮箱。

要创建和删除它们:

```
imap_session.create("work").unwrap();
imap_session.delete("work").unwrap();

```

如果您尝试创建`"INBOX"`或已存在的邮箱，创建将会失败。同样，如果您尝试删除`"INBOX"`或不存在的邮箱，删除将会失败。

最后，让我们看看如何监视邮箱的变化。

```
imap_session.select("INBOX").unwrap();
imap_session.idle().unwrap().wait();

```

在这里，`idle()`会返回一个 [`Handle`](https://docs.rs/imap/2.3.0/imap/extensions/idle/struct.Handle.html) 让我们可以`wait()`上。这将一直阻止，直到选定的邮箱发生更改。也可以使用`wait_keepalive()`通过 keepalive 等待，使用`wait_with_timeout(Duration)`通过超时阻塞。在检测到更改之前一直阻塞的能力很有帮助，因为它可以帮助我们避免在消耗更多资源的较慢的循环中进行轮询。

在我看来，`imap`有一个很好的 API，很容易让你理解。如果你通读了`Session`的文档，你会对这个箱子还能做什么有一个很好的想法。

请记住，这些示例中使用的大多数方法都会发出网络请求，因此它们可能会失败。为了简单起见，我大量使用了`unwrap()`,但是您绝对应该在实际应用程序中处理错误。

## 结论

总的来说，Rust 的电子邮件支持并不出色——到目前为止。不使用 async/await 发送电子邮件有最好的支持，所以如果这就是你所需要的，你可能已经可以使用它了。如果你想处理收到的电子邮件，那么`imap`箱是你最好的选择。

我在本文中没有提到 POP3，这是有充分理由的 Rust 中对 POP3 的支持实际上是不存在的。

对于电子邮件绝对需要 async/await 的应用程序，我还不会使用 Rust。请注意，如果您只想将这些板条箱放入异步应用程序中，您可以使用异步运行时提供的线程池实现。比如 [`tokio::task::spawn_blocking(...)`](https://docs.rs/tokio/0.2.22/tokio/task/index.html#spawn_blocking) 。

Rust 中的大部分网络生态系统已经非常稳固，所以我相信电子邮件的故事会随着时间的推移而改善。在那之前，你可能最好对大量使用电子邮件的应用程序使用另一种语言。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。