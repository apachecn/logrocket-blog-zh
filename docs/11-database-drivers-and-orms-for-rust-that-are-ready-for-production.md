# Rust 的 11 个数据库驱动程序和表单已准备好投入生产

> 原文：<https://blog.logrocket.com/11-database-drivers-and-orms-for-rust-that-are-ready-for-production/>

由于更多的原因，我们没有时间在这篇文章中探讨，Rust 在开发人员社区中迅速流行起来。它的一个卖点是它支持所有主要的数据库和存储格式——从基于文件的存储平台到基于内存的混合数据库。

在本指南中，我们将比较 11 个最流行、最稳定的数据库驱动程序和可用于 Rust 的 ORM。最后，我们将在一个方便的表格中汇编所有关键要点，以帮助您为下一个 Rust 项目选择正确的数据库。

## 数据库驱动程序

数据库驱动程序是在应用程序和数据库之间执行通信的一段代码。它实现了应用程序和数据库之间的数据交换协议和格式。

### 1.`mysql`

MySQL 是一个非常流行的数据库，也是大多数使用 SQL 数据库的应用程序的首选。 [`mysql`](https://docs.rs/mysql/19.0.1/mysql/) 机箱提供了 MySQL 协议的纯 Rust 实现。它支持基于文本的协议和二进制协议。

`mysql`支持语句缓存和连接池。TLS 通过`nativetls`板条箱支撑。板条箱使用`params`来创建行。一个缺点是 MySQL 不支持异步。

### 2.`mysql_async`

正如你可能已经猜到的， [`mysql_async`](https://docs.rs/mysql_async/0.24.2/mysql_async/) 是`mysql`驱动的异步版本。它有很多类似于`mysql`中的 API，但是它可能没有一些新的特性，因为箱子是由不同的开发者维护的。

`mysql_async`完全支持 MySQL 协议，包括基于 TLS 的加密。`mysql_async` crate 还允许你维护一个连接池。这个箱子是用纯铁锈写的。

### 3.`tokio_postgres`

异步支持:是

Postgres 是一个非常受欢迎的数据库。Rust 有一个针对 Postgres 协议的异步实现，它构建在 tokio 异步运行时之上，但也可以与其他运行时一起工作。

查询的执行是完全流水线化的，即驱动程序可以发送多个查询。这意味着 [`tokio-postgres`](https://docs.rs/tokio-postgres/0.5.5/tokio_postgres/) 可以同时处理多个数据响应。它还支持使用`nativetls`的 TLS。驱动程序不支持连接池，因为可以在同一连接上发送多个查询。

`tokio_postgres`为各种配置提供了多个包装器，例如驱动程序的同步版本、OpenSSL 版本、`native_tls`版本等等。

还有几个其他板条箱具有各种附加功能，包括:

*   `postgres-types`
*   `postgres-native-tls`
*   `postgres-openssl`
*   `postgres`

### 4.`redis`

Redis 是一个内存数据库，可用于创建缓存、工作队列和微服务。Rust 对 Redis 数据库有很好的支持。 [`redis`](https://docs.rs/redis/0.17.0/redis/) 机箱提供了高级和低级 API。所有的查询都是流水线式的，这意味着可以同时发送多个查询。

全面实施 Redis 通信协议。对发布/订阅的支持仍在进行中。`redis`使用 tokio 作为异步运行时。这个机箱支持 Lua 脚本、自动重新连接和使用`r2d2`机箱的连接池。

### 5.`rusqlite`

异步支持:否

SQLite 是一个具有 SQL 支持的进程内数据库，被多个平台用于维护数据存储。Android 应用程序通常使用 SQLite 作为本地数据库。

Rust 在 SQLite C 驱动周围有一个包装器。 [`rusqlite`](https://docs.rs/rusqlite/0.24.0/rusqlite/) 机箱使用`bindgen`来创建包装器，这会导致编译时间很长。`rusqlite`是一个安全的 SQLite 包装器。还支持`commit`、`insert`等挂钩。

`rusqlite`不包括异步支持。

### 6.`leveldb`

由 Google 创建的 LeveDB 是一个键-值存储，并且仅仅是一个键-值存储。它不是一个 SQL 数据库；这更像是 NoSQL。

许多数据库使用 LevelDB 作为存储引擎。Chrome 使用 LevelDB 实现 IndexDB。

[`leveldb`](http://skade.github.io/leveldb/leveldb/) 板条箱为 LevelDB 提供安全绑定。像`rusqlite`一样，它可能会增加编译时间，因为它使用了`bindgen`。也和`rusqlite`一样，没有异步支持。

### 7.`mongodb`

MongoDB 是一个 NoSQL 数据库。Rust 有一个官方的 MongoDB 驱动，支持异步。它是用纯 Rust 编写的，使用 tokio 运行时来支持异步。

[`mongodb`](https://docs.rs/mongodb/1.1.1/mongodb/) 支持聚合以及各种数据库操作。它使用`bson`箱创建一个 BSON 文档。在撰写本文时，还不支持事务。

### 8.`memcache`

Memcached 是一个免费的、开源的、高性能的分布式内存对象缓存系统。 [`memcache`](https://docs.rs/memcache/0.14.0/memcache/) 是一个纯 Rust 写的 Memcached 客户端。它支持 Memcached 的多个实例。包括自动 JSON 序列化和压缩在内的一些特性还不可用。

### 9.`cdrs`

异步支持:否

Cassandra 是一个分布式、可伸缩的 SQL 数据库。`crdrs`是 Cassandra 和 ScyllaDB 的数据库驱动。

crate 提供了 Cassandra 协议的完整实现，并支持集群。它还支持使用`nativetls`的 TLS。

[`cdrs-async`](https://crates.io/crates/cdrs-async) 是基于 tokio 异步运行时的`cdrs`机箱的异步版本。

## ORMs

对象关系映射器(ORM)用 Rust 结构映射稳定行。ORMs 提供了对多个 SQL 数据库的支持。许多都是完整的数据库解决方案，包括多个数据库驱动程序、连接池支持和迁移工具。下面是两个最受欢迎的。

### 1.`diesel`

异步支持:否

`diesel`是 Postgres、MySQL 和 SQLite 的 ORM。它提供了一个名为`diesel_cli`的迁移工具。`diesel` 是类型安全的，意味着表类型是使用迁移文件创建的。它的目标是在多个数据库上产生和相同的行为。

`diesel` 不支持异步，不支持按查询分组。在 stable Rust 上，由于缺乏对过程宏的支持，这个错误有点奇怪。它可以包括长嵌套类型。

可以为数据库更改驱动程序。连接池是通过`r2d2`支持的。

### 2.`quaint`

[`quaint`](https://docs.rs/quaint/0.1.13/quaint/) 是一个支持异步操作的 ORM。它为运行时创建查询提供了 AST。

`quaint`支持多个 SQL 数据库，包括 MySQL、Postgres 和 SQLite，以及连接池。它不是数据库类型安全的 ORM。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

Rust 社区为管理数据库迁移构建了一些很棒的工具，其中一些是独立于 ORM 的。最适合您的解决方案将取决于您项目的目标和要求。

为了帮助您做出明智的选择，我们在一个方便的表格中收集了与上述每个数据库驱动程序和 ORM(以及更多)相关的关键要点、优点、缺点和特性。

| **名称** | **类型** | 产品。准备好了吗？ | **异步支持？** | **纯锈？** | **优点** | **缺点** | **注释** | **维护** |
| [T2`mysql`](https://docs.rs/mysql/19.0.1/mysql/) | 驾驶员 | 是 | 不 | 是 | MySQL 协议的全面实现

*   连接池
*   使用`nativetls`的 TLS 支持
*   缺少 ORM

 | 不支持异步

*   在数千个项目中使用

 | 活跃的

*   [T2`mysql_async`](https://docs.rs/mysql_async/0.24.2/mysql_async/)

 | 驾驶员 |
| 是 | 是 | 是 | MySQL 协议的全面实现 | 与`mysql`几乎相同的 API | 异步支持

*   使用`nativetls`的 TLS 支持
*   连接池
*   可能缺少`mysql`板条箱的某些特征
*   活跃的
*   [T2`tokio_postgres`](https://docs.rs/tokio-postgres/0.5.5/tokio_postgres/)

 | 驾驶员

*   是

 | 是 | 是 |
| 完全流水线执行 | 同时发送多个查询 | 支持多运行时 | TLS 支持 | 同步包装可用 | 带有各种附加物的多个板条箱

*   postgres 类型
*   postgres-native-tls
*   postgres-openssl
*   postgres
*   活跃的

 | [T2`redis`](https://docs.rs/redis/0.17.0/redis/) | 驾驶员

*   是
*   是
*   是
*   支持集群
*   `r2d2`水池

 | 自动重新连接 |
| Lua 脚本支持 | 不完全支持发布/订阅 |  | 全面实施 Redis | 提供高级和低级 API | 支持流水线操作

*   活跃的
*   [T2`rusqlite`](https://docs.rs/rusqlite/0.24.0/rusqlite/)
*   驾驶员
*   是

 | 不

*   不

 | 用 C 语言编写 SQLite 库的包装器

*   挂钩支架
*   安全生锈
*   由于`bindgen`增加了构建时间

 |  |
| SQLite 是一个进程数据库 | 活跃的 | [T2`leveldb`](http://skade.github.io/leveldb/leveldb/) | 驾驶员 | 是 | 不

*   不
*   快速键值对存储
*   安全包装

 | 几乎完整的包装

*   由于`bindgen`增加了构建时间

 | 活跃的

*   [T2`mongodb`](https://docs.rs/mongodb/1.1.1/mongodb/)

 | 驾驶员 |
| 是 | 是 | 是 | 官方 MongoDB 驱动程序 | 支持聚合 | 不支持 MongoDB 事务

*   MongoDB 客户端的信任绑定
*   活跃的

 | [T2`memcache`](https://docs.rs/memcache/0.14.0/memcache/)

*   驾驶员

 | 是 | 不 |
| 是 | 内存数据库 | 支持多个数据库服务器 |  | 不支持自动 JSON 序列化和压缩 | 

*   `memcached`是分布式服务器
*   活跃的

 | [T2`cdrs`](https://docs.rs/cdrs/2.3.3/cdrs/)

*   驾驶员

 | 是

*   不

 | 是 |
| 支持 [ScyllaDB](https://www.scylladb.com/) | 全面实施规范 | TSL/SSL 支持 | 多重服务事件支持 | –提供异步版本( [`cdrs-async`](https://crates.io/crates/cdrs-async) ) | 活跃的

*   [T2`diesel`](https://docs.rs/diesel/1.4.5/diesel/)
*   ORM

 | 是

*   不

 | 是

*   `memcached` is the distributed server

 | 强类型 |
| 支持各种 SQL 数据库 | 超快的序列化和反序列化 | 大多数数据库的行为相同 | 支持连接池 | 不同的司机没有选择 | 不支持按查询分组

*   活跃的
*   [T2`quaint`](https://docs.rs/quaint/0.1.13/quaint/)
*   ORM
*   是

 | 是 | 是 | 构建 SQL 查询的语法 |
| 支持池 | 支持 MySQL、Postgres 和 Sqlite 数据库 | 非类型安全抽象 | 活跃的 | [T2`rustorm`](https://docs.rs/rustorm/0.17.0/rustorm/) | ORM

*   不
*   不
*   是
*   表格行的简单序列化
*   支持多个数据库

 | 池支持

*   No choice for different drivers
*   No support for group by query

 | 活跃的 | [T2`tql`](https://docs.rs/tql/0.1.0/tql/) |
| ORM | 不 | 不 | 是 | 编译时 SQL 查询创建 | 

*   对稳定 Rust 的错误处理较差
*   活跃的
*   [T2`migrant`](https://crates.io/crates/migrant)

 | 工具

*   是

 | 不适用的 | 是 |
| 支持 MySQL 迁移 | 自我更新 | 支持壳牌(REPL) | 消极的 | [T2`refinery`](https://docs.rs/refinery/0.3.0/refinery/) | 工具

*   是
*   是
*   是

 | 支持多个驱动程序 | 活跃的 | [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性 |
| 调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣， | . | LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。 | 现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。 | Yes |  |  |  | Active |
| [`migrant`](https://crates.io/crates/migrant) | Tool | Yes | N/A | Yes |  |  |  | Passive |
| [`refinery`](https://docs.rs/refinery/0.3.0/refinery/) | Tool | Yes | Yes | Yes |  |  |  | Active |

## [LogRocket](https://lp.logrocket.com/blg/rust-signup): Full visibility into web frontends for Rust apps

Debugging Rust applications can be difficult, especially when users experience issues that are difficult to reproduce. If you’re interested in monitoring and tracking performance of your Rust apps, automatically surfacing errors, and tracking slow network requests and load time,

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

[LogRocket](https://lp.logrocket.com/blg/rust-signup) is like a DVR for web and mobile apps, recording literally everything that happens on your Rust app. Instead of guessing why problems happen, you can aggregate and report on what state your application was in when an issue occurred. LogRocket also monitors your app’s performance, reporting metrics like client CPU load, client memory usage, and more.

Modernize how you debug your Rust apps — [start monitoring for free](https://lp.logrocket.com/blg/rust-signup).