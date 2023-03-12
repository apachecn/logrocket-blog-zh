# Rust - LogRocket 博客中的文件上传和下载

> 原文：<https://blog.logrocket.com/file-upload-and-download-in-rust/>

许多 web 应用程序，尤其是面向用户的应用程序，都提供了上传文件的界面。这可以从简单的文件，如头像图片，到复杂的项目，如超安全，加密签署的合同。

在任何情况下，对于许多 web 服务来说，这都是必须的——就像上传后能够再次下载或访问这些文件一样重要。

在本指南中，我们将演示如何在 Rust web 应用程序中实现文件上传和下载。我们将使用 [warp](https://github.com/seanmonstar/warp) web 框架，但是基础知识将主要适用于其他异步 web 框架。

有一个漂亮的板条箱用于处理异步多部分流，称为 [mpart-async](https://crates.io/crates/mpart-async) ，它流式传输传入的文件，而不是像 warp 此时所做的那样将它们完全加载到内存中(对此有一个[问题](https://github.com/seanmonstar/warp/issues/323))。

然而，出于本教程的目的，我们将使用 warp 的内置方法来处理多部分请求。如果您的用例涉及到处理需要在加载到内存之前流过或验证的大文件，那么前面提到的`mpart-async` crate 是一个明智的方法。

我们将构建一个示例 web 应用程序，使用户能够上传高达 5 MB 的`.pdf`和`.png`文件。每个文件将以随机生成的名称放在本地的`./files`文件夹中，并可通过`GET /files/$filename`端点下载。

## 设置

接下来，您只需要一个相当新的 Rust 安装(1.39+)和一个发送 HTTP 请求的工具，比如 cURL。

首先，创建一个新的 Rust 项目。

```
cargo new rust-upload-download-example
cd rust-upload-download-example

```

接下来，编辑`Cargo.toml`文件并添加您需要的依赖项。

```
[dependencies]
tokio = { version = "0.2.21", features = ["macros", "rt-threaded", "fs"] }
warp = "0.2.3"
uuid = { version = "0.8", features = ["v4"] }
futures = { version = "=0.3.5", default-features = false }
bytes = "0.5.6"

```

我们使用 warp 来构建 web 服务，它在底层使用 Tokio。其他依赖项用于处理 warp 的文件上传。使用`uuid`，我们将为上传的文件创建唯一的名称，期货和字节箱将帮助我们处理传入的文件流。

## 网络服务

让我们首先创建一个基本的 Warp web 应用程序，让用户从本地文件夹下载文件。

```
#[tokio::main]
async fn main() {
    let download_route = warp::path("files").and(warp::fs::dir("./files/"));

    let router = download_route.recover(handle_rejection);
    println!("Server started at localhost:8080");
    warp::serve(router).run(([0, 0, 0, 0], 8080)).await;
}

async fn handle_rejection(err: Rejection) -> std::result::Result<impl Reply, Infallible> {
    let (code, message) = if err.is_not_found() {
        (StatusCode::NOT_FOUND, "Not Found".to_string())
    } else if err.find::<warp::reject::PayloadTooLarge>().is_some() {
        (StatusCode::BAD_REQUEST, "Payload too large".to_string())
    } else {
        eprintln!("unhandled error: {:?}", err);
        (
            StatusCode::INTERNAL_SERVER_ERROR,
            "Internal Server Error".to_string(),
        )
    };

    Ok(warp::reply::with_status(message, code))
}

```

在这个代码片段中，我们在`GET /files`定义了一个`download_route`，它使用 Warp 的`fs::dir`过滤器，从给定的路径提供文件。这就是我们实现文件下载所要做的一切。

当然，根据您的具体使用情况和您正在处理的文件的大小，或者如果您有安全顾虑，下载文件的逻辑会比这更复杂。但是对于我们的例子来说已经足够了。

## 上传文件

接下来，我们来看看`upload`路线定义。

```
// in main
let upload_route = warp::path("upload")
    .and(warp::post())
    .and(warp::multipart::form().max_length(5_000_000))
    .and_then(upload);

let router = upload_route.or(download_route).recover(handle_rejection);

```

上传路由是一个`POST`端点。我们使用 Warp 的`multipart::form()`过滤器来传递多部分请求。

我们也可以在这里定义一个最大长度。您可能已经注意到，在`handle_rejection`错误处理方法中，我们显式地处理了`PayloadTooLarge`错误。当有效负载超过此限制时，会触发此错误。

最后，让我们看看`upload`处理程序。这是这个小应用程序的核心部分，它有点复杂，所以我们将一步一步地介绍它。

```
async fn upload(form: FormData) -> Result<impl Reply, Rejection> {
    let parts: Vec<Part> = form.try_collect().await.map_err(|e| {
        eprintln!("form error: {}", e);
        warp::reject::reject()
    })?;

```

我们立即看到函数签名中的`FormData`。这实际上是一个`warp::multipart::FormData`，它是由多个`Part`元素组成的流。既然我们正在处理一个`futures::Stream`，我们可以使用 [`TryStreamExt`](https://docs.rs/futures/0.3.5/futures/stream/trait.TryStreamExt.html#method.try_collect) 特性来帮助一些助手。在这种情况下，我们使用`try_collect`函数将整个流异步收集到一个集合中，如果失败，记录错误。

为了理解下一部分，让我们看一个使用 cURL 发送到这个服务器的示例请求。

```
curl --location --request POST 'http://localhost:8080/upload' \
--header 'Content-Type: multipart/form-data' \
--form '[email protected]/home/somewhere/picture.png'

```

正如您在`--form`选项中看到的，我们用名称`file`定义我们的文件。我们还可以添加额外的参数，比如文件名或一些额外的元数据。

下一步是迭代上面收集的`Part`以查看是否有`file`字段。

```
    for p in parts {
        if p.name() == "file" {
            let content_type = p.content_type();
...

```

我们迭代得到的部分，如果其中一个被称为`file`，我们将假设它是一个文件。现在我们需要确保它具有正确的内容类型——在本例中是 PDF 或 PNG——并进一步处理它。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
            let file_ending;
            match content_type {
                Some(file_type) => match file_type {
                    "application/pdf" => {
                        file_ending = "pdf";
                    }
                    "image/png" => {
                        file_ending = "png";
                    }
                    v => {
                        eprintln!("invalid file type found: {}", v);
                        return Err(warp::reject::reject());
                    }
                },
                None => {
                    eprintln!("file type could not be determined");
                    return Err(warp::reject::reject());
                }
            }

```

我们可以使用`Part`的`.content_type()`方法来检查实际的文件类型。 [rust-mime-sniffer](https://github.com/flier/rust-mime-sniffer) 是另一个可以用来检测文件类型的有用工具。

我们还根据文件类型设置了`file_ending`,因此我们可以将它附加到我们稍后想要创建的文件中。

如果文件是未知类型或者没有已知类型，我们记录错误并返回。

下一步是将`Part`转换成我们可以实际写入磁盘的字节向量。

```
            let value = p
                .stream()
                .try_fold(Vec::new(), |mut vec, data| {
                    vec.put(data);
                    async move { Ok(vec) }
                })
                .await
                .map_err(|e| {
                    eprintln!("reading file error: {}", e);
                    warp::reject::reject()
                })?;

```

此时，我们可以使用`Part`的`.stream()`或`.data()`方法来获得包含数据的底层`bytes::Buf`。

在这个例子中，我们再次将整个部分转换成一个流，并使用来自`TryStreamExt`的另一个助手`.try_fold()`，将所有的缓冲区连接成一个包含所有数据的`Vec<u8>`。

这个`try_fold`本质上类似于任何其他的`.reduce()`或`.fold()`函数，除了它是异步的。我们定义一个初始值(一个空向量)，然后将每一段数据添加到其中。

如果这些失败了，我们再次记录错误并返回。

此时，我们解析并验证了传入的文件，并准备好将数据写入磁盘，这是最后一步。

```
            let file_name = format!("./files/{}.{}", Uuid::new_v4().to_string(), file_ending);
            tokio::fs::write(&file_name, value).await.map_err(|e| {
                eprint!("error writing file: {}", e);
                warp::reject::reject()
            })?;
            println!("created file: {}", file_name);
        }
    }
    Ok("success")
}

```

首先，我们使用`Uuid`箱创建一个随机生成的唯一文件名，并添加上面计算的`file_ending`。然后，使用 Tokio 的`fs::write`，它是`std::fs::write`的异步等价物，我们用生成的文件名将数据写到一个文件中。如果成功，我们记录文件名并向调用者返回成功消息。

让我们使用下面的命令来尝试一下。

```
# first, upload some png
curl --location --request POST 'http://localhost:8080/upload' \
--header 'Content-Type: multipart/form-data' \
--form '[email protected]/home/somewhere/picture.png'

# check the logs for the file name, or go into the ./files folder
created file: ./files/7d678724-9480-489e-8a33-57e1ae5adb4d.png

# request the file and pipe it to a new png
curl http://localhost:8080/files/7d678724-9480-489e-8a33-57e1ae5adb4d.png > new_picture.png

```

有用！太棒了。所有这一切都在不到一百行 Rust 代码中完成，只需使用几个箱子就可以进行一些非常基本的错误处理和输入验证。

你可以在 [GitHub](https://github.com/zupzup/warp-upload-download-example) 找到完整的示例代码。

## 结论

在 web 服务中高效、可靠地处理文件上传并不是一件容易的事情，但是 Rust 生态系统提供了所有的工具来实现这一点，甚至提供了异步传输文件的选项来提高速度和灵活性。

上面的例子是现实世界实现的起点。当它用于生产系统时，还有更多的事情需要考虑。也就是说，Rust web 生态系统已经具备了为您的用户创造出色的上传和下载体验的基础。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。