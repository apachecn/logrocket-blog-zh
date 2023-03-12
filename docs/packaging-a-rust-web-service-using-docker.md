# 使用 Docker 打包 Rust web 服务

> 原文：<https://blog.logrocket.com/packaging-a-rust-web-service-using-docker/>

自从几年前 Docker 掀起了一股容器热潮以来，将 web 应用程序打包成 Docker 容器已经成为某种标准，尤其是在云中。

这种方法对于本地开发和服务器部署都有一些好处。一旦构建了映像，它就不会改变，并且可以在任何运行 Docker 引擎的平台上以最小的性能损失执行(在 Linux 上)。

在本教程中，我们将演示如何在 Docker 容器中放置一个 Rust web 应用程序。我们将使用不同的基本映像和权衡来介绍两种方法。

## 基本 web 应用程序

首先，让我们用 [warp](https://github.com/seanmonstar/warp) 构建一个非常基本的 web 应用程序来测试不同的 docker 设置。

在这种情况下，您需要的唯一依赖项是 tokio 和 warp 本身:

```
[dependencies]
tokio = { version = "0.2", features = ["macros", "rt-threaded"] }
warp = "0.2"

```

您还需要一个运行中的 web 服务器和一个`/health`端点来查看是否一切正常。

```
use warp::{Filter, Rejection, Reply};

type Result<T> = std::result::Result<T, Rejection>;

#[tokio::main]
async fn main() {
    let health_route = warp::path!("health").and_then(health_handler);

    let routes = health_route.with(warp::cors().allow_any_origin());

    println!("Started server at localhost:8000");
    warp::serve(routes).run(([0, 0, 0, 0], 8000)).await;
}

async fn health_handler() -> Result<impl Reply> {
    Ok("OK")
}

```

基本 web 应用程序的代码并不特别令人兴奋。然而，在将服务器绑定到 IP 和端口时，一定要注意到`0.0.0.0`的重要性。在 docker 内部使用`127.0.0.1`或`localhost`是行不通的。

运行`cargo run`将在`[http://localhost:8000](http://localhost:8000)`上启动一个带有`/health`端点的本地服务器，当它被调用时会返回`OK`。

## Docker 图像(Debian)

现在是时候将整个东西放入 Docker 容器中了。

构建要在云中运行的 Docker 映像时，一个好的实践是使用多构建设置，其中可执行文件在`builder`步骤中创建，然后复制到一个不同的、更小的映像中。

这很有用，因为要构建应用程序，您需要 Rust、Cargo 和许多其他软件包。如果您只是将可执行文件保存在这个容器中并在那里运行，那么生成的映像将会非常大(在本例中大约为 1.5 GB)。

然而，对于多阶段构建，您可以使用一个非常简单的基础映像来实际运行创建的可执行文件。

首先，让我们看看如何使用一个纤巧的 Debian Buster 基本映像。下面将是一个工作`Dockerfile`来做到这一点。

```
FROM rust:1.43 as builder

RUN USER=root cargo new --bin rust-docker-web
WORKDIR ./rust-docker-web
COPY ./Cargo.toml ./Cargo.toml
RUN cargo build --release
RUN rm src/*.rs

ADD . ./

RUN rm ./target/release/deps/rust_docker_web*
RUN cargo build --release

FROM debian:buster-slim
ARG APP=/usr/src/app

RUN apt-get update \
    && apt-get install -y ca-certificates tzdata \
    && rm -rf /var/lib/apt/lists/*

EXPOSE 8000

ENV TZ=Etc/UTC \
    APP_USER=appuser

RUN groupadd $APP_USER \
    && useradd -g $APP_USER $APP_USER \
    && mkdir -p ${APP}

COPY --from=builder /rust-docker-web/target/release/rust-docker-web ${APP}/rust-docker-web

RUN chown -R $APP_USER:$APP_USER ${APP}

USER $APP_USER
WORKDIR ${APP}

CMD ["./rust-docker-web"]

```

这是相当多的文字，所以让我们一步一步来。

如上所述，有两个构建步骤:简单地使用 Rust 1.43 基础映像的`builder`，以及使用 Debian 的第二步。

在构建器步骤中，使用了一个简单的伪依赖缓存机制。首先，使用`cargo`创建一个空的 Rust 项目，然后将所有依赖项(以`Cargo.toml`的形式)复制到该项目中。

然后，在删除`src`文件夹中的所有内容之前，会触发一个发布构建。使用 Docker，对于`Dockerfile`中的每个命令，都会创建一个层。在后续构建中，只有输入发生了变化的层才需要重新构建。

这就是你独立于你自己的代码构建依赖关系的原因——因为依赖关系的变化通常比依赖关系少得多。如果您只更改`src`中的代码，则不需要重新构建任何依赖层。不幸的是，由于 Rust 的编译时间仍然很长，特别是对于复杂的 web 应用程序，这可以节省您相当多的时间。

a .移除`src`中的所有文件后，将项目的所有文件复制到 Docker 工作目录中，从依赖项中移除构建的二进制文件，并触发另一个发布构建——在本例中，使用整个代码。

现在让我们看看`Dockerfile`的第二部分，在这里我们创建了一个可运行的容器映像。

这一部分中的大多数命令都是在任何其他语言的 Docker 中用于 web 应用程序的通用命令。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

首先，我们将使用`apt`、`ca-certificates`和`tzdata`安装一些必需的包，这对于 web 应用程序来说是一个很好的基线。

接下来，公开应用程序端口并创建一个非 root 用户，用于运行可执行文件。最有趣的部分是`COPY` `--from-builder`命令，它使我们能够将`builder`步骤中构建的可执行文件复制到这个容器中。

最后，新创建的用户可以简单地启动可执行文件。

要构建这个容器，只需执行下面的代码。

```
docker build -t rust-debian -f ./debian/Dockerfile .

```

然后，您可以使用以下命令来执行它:

```
docker run -p 8000:8000 rust-debian

```

这将运行 Rust web 应用程序，调用`[http://localhost:8000/health](http://localhost:8000/health)`将返回 OK。成功！

查看`docker images`的输出，我们可以看到以下图像大小:

```
rust-debian          latest      d5ae1c61b310     About a minute ago   84.7MB

```

低于 90MB —不算太坏。但是我们可以做得更好。让我们看看如何用阿尔卑斯山的基础图像做同样的事情。

## 码头图片(阿尔卑斯)

大多数阿尔卑斯山的`Dockerfile`是相同的，但是有一些重要的区别。

```
FROM ekidd/rust-musl-builder:stable as builder

RUN USER=root cargo new --bin rust-docker-web
WORKDIR ./rust-docker-web
COPY ./Cargo.lock ./Cargo.lock
COPY ./Cargo.toml ./Cargo.toml
RUN cargo build --release
RUN rm src/*.rs

ADD . ./

RUN rm ./target/x86_64-unknown-linux-musl/release/deps/rust_docker_web*
RUN cargo build --release

FROM alpine:latest

ARG APP=/usr/src/app

EXPOSE 8000

ENV TZ=Etc/UTC \
    APP_USER=appuser

RUN addgroup -S $APP_USER \
    && adduser -S -g $APP_USER $APP_USER

RUN apk update \
    && apk add --no-cache ca-certificates tzdata \
    && rm -rf /var/cache/apk/*

COPY --from=builder /home/rust/src/rust-docker-web/target/x86_64-unknown-linux-musl/release/rust-docker-web ${APP}/rust-docker-web

RUN chown -R $APP_USER:$APP_USER ${APP}

USER $APP_USER
WORKDIR ${APP}

CMD ["./rust-docker-web"]

```

在`builder`中，最重要的变化是基础图像，这里使用了 [rust-musl-builder](https://hub.docker.com/r/ekidd/rust-musl-builder/) 图像，而不是官方的 Rust 图像。

这样做的原因是我们想要构建一个静态 Rust 二进制文件，我们可以将它复制到 Alpine base 映像中。因为 Alpine 也是围绕`musl-libc`构建的，所以使用官方 Docker 映像的可执行文件是行不通的。

除此之外，构建器阶段也非常相似。唯一改变的是可执行文件的路径。

在 runner 阶段，我们现在使用`alpine:latest`作为基础映像，并且必须使用`apk`和一些不同的命令来创建运行应用程序的用户。步骤本身基本相同，只是命令和路径有点不同。

这个`Dockerfile`的基本结构与基于 Debian 的非常相似，所以构建和运行它的方式也是一样的。

```
docker build -t rust-alpine -f ./alpine/Dockerfile .
docker run -p 8000:8000 rust-alpine

```

同样，打开`[http://localhost:8000/health](http://localhost:8000/health)`，你会得到一个成功的响应。

我们首先切换到 Alpine 的原因是图像大小，所以让我们看看我们在这里的位置。

```
rust-alpine          latest      492d0305ebcb     About a minute ago   16MB

```

16MB —图像大小减少了 80%。然而，虽然这种大小上的减少很好，但是这里涉及到什么权衡呢？与 Debian 相比，使用 Alpine 镜像有什么缺点吗？

让我们来看看在选择哪张图片作为基础时所涉及的含义。

## 选择基础图像

Alpine 的明显优势是图像尺寸更小。然而，正如在`[rust-musl-builder](https://hub.docker.com/r/ekidd/rust-musl-builder/)`文档中所描述的，在过去，OpenSSL 的工作一直存在问题。虽然这个问题已经解决了，但是如果你依赖 C 库，你可能会遇到麻烦，因为使用标准工具链会更容易处理 C 库。

使用基于 Alpine 的映像还存在与内存分配相关的严重性能问题。安迪·格罗夫追溯到了 musl 的使用。在某些情况下，切换到 jemalloc 分配器会有所帮助。

我个人没有遇到上述问题，但我确实注意到，与 Debian 相比，在 Alpine 容器中运行应用程序时，内存占用更低。根据您想要运行的工作负载，这些指标会有所不同，因此这里不可能给出什么灵丹妙药。

一般来说，使用哪种堆栈是您自己的决定。它需要基于对您的用例很重要的权衡。

与其他任何运营决策一样，对于生产 web 服务来说，不存在一刀切的做法。您需要对您的用例进行测试、基准测试和概要分析，然后决定什么是最适合的。

## 结论

在本指南中，我们介绍了如何以两种不同的方式封装 Rust web 应用程序，这两种方式有着截然不同的利弊。

上面概述的两种方法并不是实现这一点的唯一方法，正如前面提到的，您为生产服务采用的方法必须基于您自己独特的需求和数据。然而，这篇文章中的例子是一个很好的起点，可以让你尝试使用 Docker with Rust。

你可以在 [GitHub](https://github.com/zupzup/rust-docker-web) 上找到这个例子的完整代码。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。