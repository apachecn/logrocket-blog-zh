# Rust - LogRocket 博客的 8 个外部服务

> 原文：<https://blog.logrocket.com/8-external-services-for-rust/>

开发人员通常使用外部服务，如数据库、工作队列和缓存或数据处理管道，来为他们的 web 应用程序添加新的维度和功能。随着 Rust 越来越受欢迎，自然地，它的用于实现通信协议和其他外部服务的高质量板条箱生态系统也越来越受欢迎。

在本指南中，我们将了解 Rust 社区提供的一些最佳高性能外部服务。

## 1.`lapin`

`[lapin](https://crates.io/crates/lapin)`提供高级消息队列协议(AMQP)的完整异步实现，用于与 RabbitMQ 消息代理进行通信。它是创建微服务、工作队列和服务间通信的基础库。

`lapin`是多个板条箱的基础库，通过不同的包装器支持 Tokio 运行时、async-io 运行时和 async-std 运行时。它具有 AMQP 的生产级实现，并支持原生 SSL 和 OpenSSL。`lapin`机箱还支持不同类型的认证，例如 SSL 证书和用户名/密码认证。

**生产就绪**:是
**异步支持**:是

## 2.`rdkafka`

`[rdkafka](https://crates.io/crates/rdkafka)`为 Rust 提供了一个 Kafka 的实现。它是完全异步的。

这个`rdkafka`箱子是基于 [librdkafka](https://github.com/edenhill/librdkafka) C 库的。它为 librdkafka 库提供了一个安全完整的接口。支持 Kafka 大于 0.8 的所有版本。

`rdkafa`速度极快；根据`rdkafka's` [自述](https://github.com/fede1024/rust-rdkafka#one-million-messages-per-second)上的基准测试，它每秒可以处理多达 100 万条消息。`rdkafka`在 librdkafka 图书馆内部使用`rdkafka-sys`板条箱进行 FFI。

`rdkafka`的特点包括:

*   从单个或多个主题进行消费的能力
*   自动消费者再平衡
*   可定制的重新平衡，带有重新平衡前后的回调
*   同步和异步消息生成
*   可定制的偏移提交
*   能够创建和删除主题，添加和编辑分区
*   能够改变代理和主题配置

**生产就绪**:是
**异步支持**:是

## 3.`nats`

机箱为 Rust 中的 NATS 消息系统提供支持。它同时支持同步和异步，并且开箱即可与 Tokio 和 async-std 一起使用。这是用纯铁锈写的。

是 Rust 的官方客户端，由 nat.io 团队维护。它支持基于 JWT、令牌、密钥和基于用户名/密码的身份验证。

`nats`箱的主要优点是它能够生成迭代器。

`nats`板条箱的特点包括:

*   基本发布/订阅
*   请求/回复(信号和流)
*   证明
*   连接丢失时的重新连接
*   TLS 支持
*   直接异步支持

## 4.`arrow`和`datafusion`

`[arrow](https://docs.rs/arrow/2.0.0/arrow/)`提供了 Apache Arrow 所需的数据类型，而`[datafusion](https://crates.io/crates/datafusion)`提供了类似于查询`arrow`数组和数据布局的 SQL。

一般来说，`arrow` crate 提供了开发使用箭头数组的代码的功能，而`datafusion`提供了 SQL 中常见的大多数操作，join 和 window 函数除外。

`arrow`实现规范中的所有格式，除了某些字典。它还支持一些垂直业务的 SIMD 业务。

`datafusion`支持异步执行、自定义函数、聚合、整体执行节点。

**生产就绪**:是
**异步支持**:是

## 5.`tantivy`

`[tantivy](https://crates.io/crates/tantivy)`是全文搜索的构建块。Tt 支持 17 种拉丁语言和许多其他地区语言的符号化。`tantivy`还支持多线程和 SIMD，实现快速高效的索引和搜索。

根据`tantivy`机箱提供的[基准](https://tantivy-search.github.io/bench/)，`tantivy`有一个自然的查询语言(如“logrocket”和“frontend”)，速度极快。

显著特征:

*   单值和多值 u64、i64 和 f64 快速字段(相当于 Lucene 中的 doc 值)
*   `&[u8]`快速字段
*   文本、i64、u64、f64、日期和分层方面字段
*   LZ4 压缩文档存储
*   范围查询
*   分面搜索

**生产就绪**:是
**异步支持**:不适用

## 6.`elasticsearch`

板条箱是一个官方的弹性搜索客户端。它支持使用 Tokio 运行时的异步，以及`native-tls`和`rust-tls`。

虽然是 alpha 版本，但是它有一个完整的 Elasticsearch 协议的实现。它支持基于凭据和基于证书的身份验证。

**生产就绪**:否
**异步支持**:是

## 7.`redis`

`[redis](https://crates.io/crates/redis)`是一个内存数据库，可用于创建缓存、工作队列和创建微服务。Rust 对 Redis 数据库有很好的支持。

正如“[11 Rust 的数据库驱动程序和 ORM 已准备好投入生产](https://blog.logrocket.com/11-database-drivers-and-orms-for-rust-that-are-ready-for-production/)”中所述，“`redis`提供了高级和低级 API。所有的查询都是流水线式的，这意味着可以同时发送多个查询。

拥有 Redis 通信协议的完整实现，但尚不支持发布/订阅。机箱使用 Tokio 作为异步运行时，并支持 Lua 脚本、自动重新连接和使用`r2d2`机箱的连接池。

## 8.`pulsar`

`[pulsar](https://crates.io/crates/pulsar)`是 Apache Pulsar 的异步客户端。它是用纯 Rust 编写的，不依赖于 Pulsar CPP 库。

`pulsar`机箱支持开箱即用的 Tokio 和`async-std`运行时。

显著特征:

*   使用 DNS 查找的基于 URL 的(`pulsar://`和`pulsar+ssl://`)连接
*   多主题消费者(基于正则表达式)
*   TLS 连接
*   可配置的执行器(Tokio 或`async-std`)
*   带指数补偿的自动重新连接
*   消息批处理
*   用 LZ4、zlib、zstd 或 Snappy 压缩(可以用 Cargo 功能停用)

**生产就绪**:是
**异步支持**:是

## 比较 Rust 外部服务

| 图书馆 | 生产就绪 | 异步支持 | 外部服务 |
| --- | --- | --- | --- |
| `[lapin](https://crates.io/crates/lapin)` | 是啊 | 是啊 | Redis，AMQP 通讯协定 |
| `[rdkafka](https://crates.io/crates/rdkafka)` | 是 | 是 | 阿帕奇卡夫卡 |
| `[nats](https://crates.io/crates/nats)` | 是 | 是 | NATS.io |
| `[arrow](https://docs.rs/arrow/2.0.0/arrow/)` | 是 | 不 | 阿帕奇箭头 |
| `[datafusion](https://crates.io/crates/datafusion)` | 是 | 是 | Apache Arrow (SQL 查询) |
| `[tantivy](https://crates.io/crates/tantivy)` | 是 | 不适用 | 全面测试搜索 |
| `[elasticsearch](https://crates.io/crates/elasticsearch)` | 不 | 是 | 弹性搜索 |
| `[redis](https://crates.io/crates/redis)` | 是啊 | 是啊 | 再说一遍 |
| `[pulsar](https://crates.io/crates/pulsar)` | 是 | 是 | 阿帕奇脉冲星 |

[log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

## 调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。

Modernize how you debug your Rust apps — [start monitoring for free](https://lp.logrocket.com/blg/rust-signup).