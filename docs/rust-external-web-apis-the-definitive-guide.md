# Rust 外部 web APIs:权威指南

> 原文：<https://blog.logrocket.com/rust-external-web-apis-the-definitive-guide/>

外部 web API 是一种除了 web 开发人员之外，还可以被更多人访问的 API。我们将用语言实现的外部 web API 称为包装器，因为它们将本机 web API 提供的功能和接口封装到一个易于使用的库中。库提供了与整个服务进行交互的简单方法，消除了手动向各个端点编写 HTTP 请求的需要。

API 包装器，无论是内部的还是外部的，都有各种不同的特征来区分它们，使得一些包装器比其他的包装器更适合和更有用。在本指南中，我们将评估 Rust 中一些最流行的外部 API，排名不分先后，包括:

我们将根据以下标准评估上述每个 API:

1.  **稳定性** —维护、更新率、开发者响应、许可证、依赖量、发布等。
2.  **人气** — GitHub 明星，crates.io 统计，forks，社区等。
3.  **开发者体验** —易用性、文档、问题、社区、Rust 代码规范、语法等。

最后，我们将概述一些步骤来帮助你[为你的 Rust 项目](#howtochoose)选择最好的外部 web API。

## Spotify 的 Rust API 包装

原生 Spotify web API 提供对一组[端点](https://developer.spotify.com/documentation/web-api/reference/)的访问，每个端点都有自己唯一的路径。像这个列表中的许多其他包装器一样，通过 API key 或 OAuth 进行的[认证是访问 Spotify web API 所必须的。](https://blog.logrocket.com/9-rust-authentication-libraries-that-are-ready-for-production/)

最流行的 Spotify Rust web API 包装器是`aspotify`和`rspotify`。

### `aspotify`

`aspotify`是 Spotify API 的 Rust 包装器。它为 Spotify 的所有对象模型提供了 Rust 结构，并为其所有端点提供了功能。

`aspotify`使用 [`reqwest`](https://docs.rs/reqwest/) 来处理 API 调用。目前，它支持所有稳定的 Spotify 功能。还计划增加对其他 [Rust HTTP 客户端](https://blog.logrocket.com/the-state-of-rust-http-clients/)的支持，这对于喜欢使用不可知库的 Rust 开发者来说将是一个巨大的突破。

一个缺点是`aspotify`相对缺乏文档。也就是说，该库包含一个很好的示例展示，可以帮助新手入门。

*   稳定性:⦿⦿⦿⦿⦾
*   知名度:⦿⦿⦾⦾⦾
*   开发者体验:⦿⦿⦿⦾⦾

### `rspotify`

[`rspotify`](https://github.com/ramsayleung/rspotify) 是用于 [Spotify Web API](https://developer.spotify.com/web-api/) 的健壮轻量级包装器。它包含 Spotify 所有端点的功能，从获取元数据到访问用户信息(关注用户、艺术家和播放列表、保存的曲目管理等)。).可以想象，这个 web API 也需要认证。

`rspotify`的缪斯是 [Spotipy](https://github.com/plamere/spotipy) ，一个流行的 python Spotify web API。像`aspotify`，`rspotify`在幕后使用`reqwest`，但是它有更好的支持和[文档](https://docs.rs/rspotify/0.10.0/rspotify/)，加上有用的代码示例。它看起来可以直接跳到您的产品代码上了。

*   稳定性:⦿⦿⦿⦿⦾
*   知名度:⦿⦿⦿⦿⦿
*   开发者体验:⦿⦿⦿⦿⦾

### `egg-mode`

`[egg-mode](https://crates.io/crates/egg-mode)`是一个与 Twitter 公共 API 交互的 Rust 库。尽管 Twitter API 非常庞大，`egg-mode`仍然致力于实现整个公共 API，让任何人都能尽可能容易地在 Rust 中使用 Twitter API。

在`egg-mode`中，端点作为裸函数可用，这很有趣，因为与其他外部 web APIs 不同，认证细节是作为参数而不是作为根工作器添加的。

与其他 Rust Twitter 包装器相比，`egg-mode`相当新。尽管如此，它足够完整和稳定，可以用于生产。该库非常受欢迎，维护良好，有文档记录，并且充满了涵盖大多数用例的优秀示例。

*   稳定性:⦿⦿⦿⦿⦾
*   知名度:⦿⦿⦿⦿⦾
*   开发者体验:⦿⦿⦿⦿⦾

## Discord API 的 Rust 库

Discord 提供了一个成熟的 API 接口，帮助其强大而活跃的社区创建第三方应用。幸运的是，Rust 中有两个稳定的库来促进这种交互:`serenity`和`discord-rs`。

### `serenity`

[`serenity`](https://github.com/serenity-rs/serenity) 是一个著名的 Discord API 的 Rust 库，可以让你创建成熟的 Discord 机器人。您可以访问整个 Discord API 并执行诸如发送消息、提及用户、语音通道操作、获取/发送反应等操作。 [`serenity`的尖端特性](https://crates.io/crates/serenity)范围从高级到低级接口。

`serenity`使用`[Client::new(<token>)](https://docs.rs/serenity/*/serenity/client/struct.Client.html#method.new)`对 API 进行无缝认证，其中您提供了自己的 Discord bot 令牌。在此之后，您可以与 API 进行交互。`serenity`使您能够用`validate_token`检查您以前的令牌，这是可伸缩性的一个优点。

这个库是独一无二的，因为它自动处理分片连接和缓存，这简化了复杂性并避免了对 Discord API 的不必要的 HTTP 请求。

宁静是一种纯粹的快乐。它有过多的[很好解释的例子](https://github.com/serenity-rs/serenity/tree/current/examples)，它自己的[不和谐服务器](https://discord.com/invite/9X7vCus)，伟大的维护/发布时间表，问题管理，和[广泛的文档](https://docs.rs/serenity)。

专业提示:您可以使用 [`serenity-utils`](https://crates.io/crates/serenity_utils) 来扩展 serenity，并为使用`serenity`创建的 Discord 机器人提供转换、提示和菜单功能。

*   **稳定** : ⦿⦿⦿⦿⦿
*   **人气** : ⦿⦿⦿⦿⦿
*   **开发者体验** : ⦿⦿⦿⦿⦿

### `discord-rs`

`[discord-rs](https://github.com/SpaceManiac/discord-rs)`是一个 Rust 客户端库，用于 [Discord](https://discord.com/) 聊天客户端 API。

使用`discord-rs`，在您使用您的 bot 令牌认证之后，您可以使用 web 套接字连接访问 Discord 事件。

项目据说偶尔会维护。你不应该期待很多突破性的变化，也不应该期待最近的更新。文档写得很好，并且在库中包含了很好的例子。

*   **稳定** : ⦿⦿⦿⦿⦾
*   **人气** : ⦿⦿⦿⦾⦾
*   **开发者体验** : ⦿⦿⦿⦾⦾

## GitHub 防锈板条箱

GitHub 的 API 让你可以访问一整套旨在帮助你管理 GitHub 上的存储库的功能。除了非常被动之外，GitHub API 还有许多端点，如果您正在构建下一个问题管理机器人或 GitHub 动作工作流，这些端点可能会变得难以管理。

### `hubcaps`

[`hubcaps`](https://crates.io/crates/hubcaps) 提供了一套与 GitHub API 交互的构建模块。它为存储库、分支、标签、部署、发布、拉取、发布、gists、钩子、搜索和团队提供了一个接口，并使向 Github API 服务认证变得非常容易。

非常稳定，已经持续了相当长的一段时间。它因使用 GitHub API 而闻名，并且已经有很多未解决的问题。还有无数的例子可以帮助你开始。

*   **稳定** : ⦿⦿⦿⦿⦿
*   **人气** : ⦿⦿⦿⦿⦾
*   **开发者体验** : ⦿⦿⦿⦿⦾

### `octocrab`

[`octocrab`](https://crates.io/crates/octocrab) 是一个第三方 GitHub API 客户端，可以让你在 Rust 中创建自己的 GitHub 集成。它附带了一个高级的、强类型的、语义 API，由直接与 API 交互的 Rust 函数和一个低级的 HTTP API 组成，后者使您能够扩展当前的函数或创建定制的函数。

`octocrab`公开了一组 HTTP 行为，这些行为一直使用任何先前的身份验证和配置。这个公开的 API 使您能够很好地控制 HTTP 调用生成的所有请求和响应。

因为 GitHub 变化很快，所以一个库很难绑定 API 的每个全新特性。但是，`octocrab`动作很快，尽可能多地和原生 GitHub API 配对。`octocrab`遵循当前针对 Rust 的最佳实践，而不损害其众所周知的灵活性，使您能够扩展`octocrab`的 API，并围绕 GitHub 的 API 提供强大的绑定。

虽然`octocrab`很新，但它是一个稳定的、维护良好的库，遵循 Rust 惯例，并且有良好的文档记录。

*   **稳定** : ⦿⦿⦿⦿⦿
*   **人气** : ⦿⦿⦿⦿⦾
*   **开发者体验** : ⦿⦿⦿⦿⦾

## 生锈的电报机器人

机器人是运行在 Telegram 内部的第三方应用程序。用户可以通过向机器人发送消息、命令和[内嵌请求](https://core.telegram.org/bots#inline-mode)来与机器人互动。

### `telegram-bot`

[Rust 电报机器人库](https://github.com/telegram-rs/telegram-bot)，或`telegram-bot` API，涵盖了[广泛的电报机器人能力](https://docs.rs/telegram-bot/0.7.0/telegram_bot/prelude/index.html)。它给你所有你需要的工具，用几行代码就能构建一个完整的电报机器人。

显示你在机器人中可能需要的东西的实体目录。它得到了很好的维护和稳定，社区很快跟进问题。

*   **稳定** : ⦿⦿⦿⦿⦿
*   **人气** : ⦿⦿⦿⦿⦾
*   **开发者体验** : ⦿⦿⦿⦿⦾

### **T2`teloxide`**

[`teloxide`](https://github.com/teloxide/teloxide) 将自己描述为一个优雅的电报机器人框架。

让在 Telegram 中创建机器人的过程变得令人愉快。它设计精巧，几乎涵盖了整个 Telegram bot API。

`teloxide`提供了一个高级的 Rust 实现，使得开发变得简单而有趣。它遵循功能反应式设计，具有完整的对话管理子系统，并提供强类型的 bot 命令。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

除了优秀的文档，这些特性证明了`teloxide`的创建是为了让开发者的工作更容易。它已经可以投入生产了，并且严格遵循官方的 Telegram bot API，后者变化非常频繁。

*   **稳定** : ⦿⦿⦿⦿⦿
*   **人气** : ⦿⦿⦿⦿⦾
*   **开发者体验** : ⦿⦿⦿⦿⦿

## AWS 用防锈板条箱

Amazon web services (AWS)是一组广泛的开发人员服务，以其可访问性和完整性而闻名。AWS 包含 170 多种服务，从数据库和身份验证到人工智能和 NLP 产品。

AWS 服务必须拥有每种现有语言的 SDK 和包装器，因为当您开始使用云时，这些服务变得非常重要。

### `rusoto_core`

Rusoto 在 Rust 社区中以覆盖广泛的流行 AWS 服务而闻名，这使得它成为使用 Rust 和 AWS 的开发人员的首选。

[`rusoto-core`](https://crates.io/crates/rusoto_core) 是核心板条箱，内含常用功能。Rusoto 将每个[支持的 AWS 服务](https://www.rusoto.org/supported-aws-services.html)分成一个单独的箱子，充当一个排序树摇动库，在其中你只使用你需要的服务。这些服务是从 [`botocore`](https://github.com/boto/botocore) API 定义中生成的

就像本指南中列出的大多数 web APIs 一样，Rusoto 需要一个身份验证 API——在 Rusoto 中，它由自己独立的机箱管理。这种模块化的简单性使得与 Rusoto 一起工作非常愉快。

Rusoto 有一个欣欣向荣的社区(T1)和丰富的文档(T3)，并且非常稳定，维护得很好。

*   **稳定** : ⦿⦿⦿⦿⦿
*   **人气** : ⦿⦿⦿⦿⦿
*   **开发者体验** : ⦿⦿⦿⦿⦿

## 其他 Rust web APIs

下面的 Rust 库和包并不完全符合上面列出的任何类别，但是它们仍然值得你拥有。

### `urlshortener`

顾名思义， [`urlshortener`](https://crates.io/crates/urlshortener) 旨在通过最小化的接口实现尽可能多的网址缩写服务。

```
let req = providers::request(long_url, &Provider::GooGl { api_key: key.to_owned() });

```

`urlshortener`提供了一长串可用于缩短 URL 的实用程序；有些需要认证，有些则不需要。

*   **稳定** : ⦿⦿⦿⦿⦿
*   **人气** : ⦿⦿⦿⦿⦾
*   **开发者体验** : ⦿⦿⦿⦿⦾

### `wikipedia`

通过 HTTP 请求访问维基百科是一项艰巨的任务。通常需要考虑很多参数，结果很难解析。

幸运的是， [`wikipedia`](https://crates.io/crates/wikipedia) 库将与维基百科端点打交道的麻烦转化为易于使用的 Rust 函数。

是同类图书馆中唯一一个可以广泛使用的图书馆。它已经很稳定，有很好的文档记录，并且很容易学习。

*   **稳定** : ⦿⦿⦿⦿⦿
*   **人气** : ⦿⦿⦿⦿⦾
*   **开发者体验** : ⦿⦿⦿⦿⦾

### `pusher`

[Pusher](https://pusher.com) 帮助您构建实时功能，并在您的网络和移动应用程序中推送通知。

`[pusher-rs](https://crates.io/crates/pusher)`为[推杆支架 API](https://pusher.com/docs/channels/library_auth_reference/rest-api) 提供防锈绑定。它使您能够在前端触发事件，并访问您的推送通道的内容。该内容包括关于应用程序的通道、属性的有意义的信息，对于存在通道，还包括当前订阅它们的用户。

拥有强大的文档和结构良好的代码，对于生产使用来说非常稳定。

*   **稳定** : ⦿⦿⦿⦿⦿
*   **人气** : ⦿⦿⦿⦿⦾
*   **开发者体验** : ⦿⦿⦿⦿⦿

### `slack-hook`

是一个 Rust crate，专门用于通过 webhooks 向 Slack 发送消息。它可以用来在几行内将任何 Rust 应用程序连接到您的 Slack 工作区。

是一个非常受欢迎的锈箱，因为它能快速完成工作。它有非常好的文档，并且非常稳定。

*   **稳定** : ⦿⦿⦿⦿⦿
*   **人气** : ⦿⦿⦿⦿⦿
*   **开发者体验** : ⦿⦿⦿⦿⦾

### `rants`

[NATS.io](https://nats.io/) 是一个面向云原生应用、物联网消息和微服务架构的开源消息系统。

[`rants`](https://docs.rs/rants/0.5.0/rants/) 是 NATS 客户端协议的一个薄薄的包装。学习使用客户端最简单的方法是阅读 [NATS 客户端协议文档](https://docs.nats.io/nats-protocol/nats-protocol)，其中涵盖了所有提供的协议。

`rants`的一个亮点是它允许你在 [`native-tls`](https://github.com/sfackler/rust-native-tls) 和 [`rustls`](https://github.com/ctz/rustls) 板条箱之间选择你喜欢的 [TLS 支架](https://blog.logrocket.com/rust-cryptography-libraries-a-comprehensive-list/#tls)。

是一个非常稳定的包装器。它遵循 NATS 客户端协议中使用的概念和术语。它是最新的，维护得很好。

*   **稳定** : ⦿⦿⦿⦿⦿
*   **人气** : ⦿⦿⦿⦿⦾
*   **开发者体验** : ⦿⦿⦿⦿⦾

## 如何选择外部 Rust web API

我们在本指南中介绍了很多 Rust 外部 web APIs，但这只是冰山一角。虽然这个列表并不详尽，但它足够全面，可以让您知道在选择外部 Rust web API 用于生产时应该注意什么。

在为 Rust 项目寻找一个好的开源库时，有几个标准需要考虑。这里有一个快速的备忘单，下次你在 Rust 中寻找外部 web API 时可以参考:

1.  转到 [crates.io](https://crates.io/) 并输入您要寻找的服务(如`yahoo-weather`)。Rust 开发者的另一个有用资源是 [Awesome Rust](https://rust.libhunt.com/) ，这是一个精选的 Rust 库列表
2.  如果你有一个以上的结果，比较他们最近的下载；在大多数情况下，数字越大，包装越受欢迎
3.  向下滚动到所选板条箱页面的底部，并分析使用情况图表。通常，如果图表从左到右的值增加，这表明它已经引起了社区的注意
4.  打开存储库。如果是开源的，你可以在页面顶部找到回购的链接
5.  调查最近的提交、未解决问题的数量和星号，甚至自述文件的结构方式。这些通常是指示软件包维护状态的标志
6.  最后，浏览一下文档和可用的代码示例。大多数包使用 [`rust-doc`](https://doc.rust-lang.org/rustdoc/what-is-rustdoc.html) 生成单据。通过打开不同的模块，您可以直观地看到它们在实践中是如何使用的以及它们所采用的参数。一些软件包，如 [rusoto](https://www.rusoto.org/) ，有定制的文档，在社区中很受欢迎，因为它们更加平易近人

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。