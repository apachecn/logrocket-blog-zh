# 如何用 Docker 运行 Phoenix 应用程序

> 原文：<https://blog.logrocket.com/run-phoenix-application-docker/>

假设您刚刚在 Phoenix 中完成了一个应用程序的构建，现在，您准备与世界其他地方共享它。您可能想知道如何在发布环境中导航，以及在哪里发布您的应用程序。

简单地说，你可以使用 Docker 在任何地方发布你的项目。仙丹需要[一些运行时的依赖，主要是二郎神](https://blog.logrocket.com/build-rest-api-elixir-phoenix/#what-is-elixir-and-phoenix-web-framework)，以便启动光束。在 Phoenix 发布文档的 [**版本**部分](https://hexdocs.pm/phoenix/releases.html#containers)中，有一个很好的 docker 文件，可以作为一个很好的起点。在本教程中，我将剖析这个 does 文件，并解释每个步骤的作用，使您能够挑选您喜欢的部分，以及可能需要更改的部分，以满足您自己项目的需要。我们开始吧！

## 入门指南

只是作为一个引子，我将在这篇文章中包括 Node.js 和 npm，它们不包括在文档的官方 docker 文件中:

```
ARG ELIXIR_VERSION=1.14.0
ARG OTP_VERSION=25.0.3
ARG DEBIAN_VERSION=bullseye-20210902-slim
ARG BUILDER_IMAGE="hexpm/elixir:${ELIXIR_VERSION}-erlang-${OTP_VERSION}-debian-${DEBIAN_VERSION}"
ARG RUNNER_IMAGE="debian:${DEBIAN_VERSION}"

```

在上面的代码中，我们分别定义了每个步骤，这在本教程的后面部分会更有意义。现在，我们定义我们希望用哪个 Elixir 版本进行编译，我们希望用哪个 Erlang OTP 版本，以及我们希望用哪个 Linux 映像作为我们的主力。

## 构建步骤

我们将从使用一个构建器映像开始:

```
FROM ${BUILDER_IMAGE} as builder
# install build dependencies
RUN apt-get update -y && apt-get install -y build-essential git nodejs npm curl \
  && apt-get clean && rm -f /var/lib/apt/lists/*_*
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - \
  && apt-get install -y nodejs
# prepare build dir
WORKDIR /app

```

上面的代码包含了我们需要的大部分依赖项，比如 Node.js、npm 和 cURL。但是，如果你需要更多的，你可以在这里添加。请记住，不是每个图像都需要添加 Node.js，所以如果您愿意，可以删除这一步。

这就搞定了。从现在开始，一切都取决于我们的应用:

```
# install hex + rebar
RUN mix local.hex --force && \
  mix local.rebar --force
# set build ENV
ENV MIX_ENV="prod"
# install mix dependencies
COPY mix.exs mix.lock ./
RUN mix deps.get --only $MIX_ENV
RUN mkdir config
# copy compile-time config files before we compile dependencies

# to ensure any relevant config change will trigger the dependencies
# to be re-compiled.
COPY config/config.exs config/${MIX_ENV}.exs config/
RUN mix deps.compile
COPY priv priv
COPY lib lib
COPY assets assets
WORKDIR assets
RUN node --version
RUN npm i -g yarn; yarn set version stable
RUN yarn install
WORKDIR ../
# compile assets
RUN mix assets.deploy
# Compile the release
RUN mix compile

```

首先，我们使用 [mix 安装钢筋 v3](https://hexdocs.pm/phoenix/Mix.Tasks.Phx.New.html) 和[六角](https://hex.pm)。Rebar 处理原生 Erlang 库，而 mix 获取我们的 Elixir 依赖项；您可以将它与 npm 对于 Node.js 的意义进行比较。然后，我们复制我们的 mixfile，它表示我们对项目的依赖，以及来自我们源代码的 lockfile 和 configs。

然后我们获取所有的依赖项并编译它们。请注意，这只编译依赖项，而不是我们的项目文件；这是两个独立的步骤。最后，我们复制我们的项目文件:

*   `priv`:迁移和静态文件
*   我们的源代码
*   我们的 JavaScript 和 CSS 代码

接下来的五个步骤是可选的。如果您使用 Node.js 和 npm，将您的`workdir`更改为`assets`文件夹，使用 Yarn 或 npm 安装依赖项，然后将`workdir`更改回`src/`。

在这一点上，我们可以部署我们的资产，这是一个特殊的步骤，使我们所有的 JavaScript 和 CSS 文件为部署做好准备。

接下来，我们编译余下的 Elixir 源代码，使项目中的每个文件都为最后的构建步骤，即发布构建做好准备:

```
# Changes to config/runtime.exs don't require recompiling the code
COPY config/runtime.exs config/
COPY rel rel
RUN mix release

```

注意我们是如何在编译步骤之后复制运行时配置的。这是一个很好的提醒，所有其他配置文件中的所有配置都被编译到版本中，因此在这一点上是不可更改的。但是，运行时配置中的配置，顾名思义，是在运行时读取的。

将构建一个发布文件，其中包含运行我们的应用程序所需的一切。

## 运行时步骤

```
# start a new build stage so that the final image will only contain
# the compiled release and other runtime necessities
FROM ${RUNNER_IMAGE}
RUN apt-get update -y && apt-get install -y libstdc++6 openssl libncurses5 locales \
  && apt-get clean && rm -f /var/lib/apt/lists/*_*
# Set the locale
RUN sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen && locale-gen
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8
WORKDIR "/app"
RUN chown nobody /app
# set runner ENV
ENV MIX_ENV="prod"
# Only copy the final release from the build stage
COPY --from=builder --chown=nobody:root /app/_build/${MIX_ENV}/rel/myapp ./
USER nobody
CMD ["/app/bin/server"]

```

同样，上面的代码是从官方的 Phoenix 文档中摘录的，但是我会再次澄清注释中的不足之处。

在这里，我们引用我们之前的`ARGS`，但是这一次，我们只取 Linux 映像。这本身就使得我们的运行时 Docker 映像特别小。

我们之前安装了`node_modules`、mix packages 等等的设置的另一个好处是，为了运行我们的 Elixir 应用程序，我们只需要在构建部分的最后从我们的`mix release`步骤中创建二进制文件，这极大地减小了我们的构建映像大小。

我们允许所有人接触我们的应用程序目录，将我们的用户更改为严格限制用户，然后运行应用程序。

## 结论

尽管本文中的步骤很长，但基本上可以归结为以下几个可以运行的命令:

```
> mix phx.gen.release --docker

```

上面的代码将生成一个 docker 文件，类似于我们在本文中讨论的文件，但是有一些不同。标准的 Phoenix 项目不使用 Node.js，因此，它不包含我在 docker 文件中包含的 Node.js 步骤。

这个 docker 文件是部署 Phoenix 应用程序的起点，在本文中，我展示了如何修改它以满足您的需求。在这种情况下，我们包括了 npm 和 Node.js 步骤。从这里开始，你所需要做的就是到处玩，找出你需要的细节。

拥有一个独立的构建器和运行时映像最酷的一点是，在构建器映像中包含太多数据并没有实际成本。是的，它会减慢你的构建速度，但是大多数这些东西都可以在你的管道中被缓存，并且在本地，它会被自动缓存。不管怎样，您的运行时映像将会很小，并且可以快速发布，因为它是一个准系统的 Linux 发行版。

我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)