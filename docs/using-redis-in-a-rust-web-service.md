# 在 Rust web 服务中使用 Redis

> 原文：<https://blog.logrocket.com/using-redis-in-a-rust-web-service/>

Redis 多年来一直是网络生态系统的主要部分。它通常用于缓存，作为消息代理，或者简单地作为数据库。

在本指南中，我们将演示如何在 Rust web 应用程序中使用 [Redis](https://redis.io/) 。

我们将使用奇妙的 [warp](https://github.com/seanmonstar/warp) web 框架构建应用程序。本教程中的技术将与其他 Rust web 堆栈非常相似。

我们将探索使用 Redis 的三种方法:

*   直接，每个请求使用一个异步连接
*   使用同步连接池
*   使用异步连接池

作为 Redis 的客户端库， [redis-rs](https://github.com/mitsuhiko/redis-rs) 是最稳定、应用最广泛的 crate，所以所有的变种都以它为基础。

对于同步池，我们将使用基于 [r2d2](https://github.com/sfackler/r2d2) 的 [r2d2-redis](https://github.com/sorccu/r2d2-redis) 。对于异步解决方案，我们将使用 [mobc](https://github.com/importcjj/mobc) 。还有很多其他的异步连接池，比如[死池](https://github.com/bikeshedder/deadpool)和 [bb8](https://github.com/khuey/bb8) ，它们都以类似的方式工作。

应用程序本身并没有做太多事情。每种不同的 Redis 连接方法都有一个处理程序，它将一个硬编码的字符串放入 Redis，到期时间为 60 秒，然后再次读取它。

事不宜迟，我们开始吧！

## 设置

首先，让我们设置一些共享类型，并为每个 Redis 连接方法定义一个模块:

```
mod direct;
mod mobc_pool;
mod r2d2_pool;

type WebResult<T> = std::result::Result<T, Rejection>;
type Result<T> = std::result::Result<T, Error>;

const REDIS_CON_STRING: &str = "redis://127.0.0.1/";

```

定义两个`Result`类型是为了节省一些输入，并表示内部`Errors` ( `Result`)和外部`Errors` ( `WebResult`)。

接下来，定义这个内部的`Error` -type 并为其实现`Reject`，这样它就可以用于从处理程序返回 HTTP 错误。

```
#[derive(Error, Debug)]
pub enum Error {
    #[error("mobc error: {0}")]
    MobcError(#[from] MobcError),
    #[error("direct redis error: {0}")]
    DirectError(#[from] DirectError),
    #[error("r2d2 error: {0}")]
    R2D2Error(#[from] R2D2Error),
}

#[derive(Error, Debug)]
pub enum MobcError {
    #[error("could not get redis connection from pool : {0}")]
    RedisPoolError(mobc::Error<mobc_redis::redis::RedisError>),
    #[error("error parsing string from redis result: {0}")]
    RedisTypeError(mobc_redis::redis::RedisError),
    #[error("error executing redis command: {0}")]
    RedisCMDError(mobc_redis::redis::RedisError),
    #[error("error creating Redis client: {0}")]
    RedisClientError(mobc_redis::redis::RedisError),
}

#[derive(Error, Debug)]
pub enum R2D2Error {
    #[error("could not get redis connection from pool : {0}")]
    RedisPoolError(r2d2_redis::r2d2::Error),
    #[error("error parsing string from redis result: {0}")]
    RedisTypeError(r2d2_redis::redis::RedisError),
    #[error("error executing redis command: {0}")]
    RedisCMDError(r2d2_redis::redis::RedisError),
    #[error("error creating Redis client: {0}")]
    RedisClientError(r2d2_redis::redis::RedisError),
}

#[derive(Error, Debug)]
pub enum DirectError {
    #[error("error parsing string from redis result: {0}")]
    RedisTypeError(redis::RedisError),
    #[error("error executing redis command: {0}")]
    RedisCMDError(redis::RedisError),
    #[error("error creating Redis client: {0}")]
    RedisClientError(redis::RedisError),
}

impl warp::reject::Reject for Error {}

```

对于几种错误类型来说，这是一大堆样板文件，但是由于目标是实现三种方法来做同样的事情，如果我们想要有好的错误，这是很难避免的。

上面定义了我们将实现的每个 Redis 方法的一般错误类型和`Error`类型。错误本身只是处理连接、池创建和命令执行错误。稍后你会看到他们的行动。

## 直接使用`redis-rs`(异步)

现在是时候实现与 Redis 交互的第一种方式了——每个请求使用一个连接。这个想法是为每个进来的请求创建一个新的连接。如果流量不是很大，这种方法是可以的，但是对于成百上千的并发请求，这种方法的伸缩性不好。

redis-rs 支持同步和异步 API。维护人员做了大量工作来保持两个 API 非常相似。虽然我们将关注直接使用 crate 的异步方式，但同步版本看起来几乎完全一样。

首先，让我们建立一个新的连接。

```
use crate::{DirectError::*, Result};
use redis::{aio::Connection, AsyncCommands, FromRedisValue};

pub async fn get_con(client: redis::Client) -> Result<Connection> {
    client
        .get_async_connection()
        .await
        .map_err(|e| RedisClientError(e).into())
}

```

在上面的代码片段中，一个`redis::Client`被传入，一个异步连接传出，处理错误。稍后我们将看看`redis::Client`的创建。

现在可以打开一个连接了，下一步是创建一些助手来启用 Redis 中的设置值并再次获取它们。在这个简单的例子中，我们将只关注`String`值。

```
pub async fn set_str(
    con: &mut Connection,
    key: &str,
    value: &str,
    ttl_seconds: usize,
) -> Result<()> {
    con.set(key, value).await.map_err(RedisCMDError)?;
    if ttl_seconds > 0 {
        con.expire(key, ttl_seconds).await.map_err(RedisCMDError)?;
    }
    Ok(())
}

pub async fn get_str(con: &mut Connection, key: &str) -> Result<String> {
    let value = con.get(key).await.map_err(RedisCMDError)?;
    FromRedisValue::from_redis_value(&value).map_err(|e| RedisTypeError(e).into())
}

```

这一部分在所有三个实现之间非常相似，因为这只是运行中的`redis-rs` API。该 API 很好地反映了 Redis 命令，`FromRedisValue`特性是一种将值转换成预期数据类型的便捷方式。

到目前为止，一切顺利。接下来是基于广泛使用的 r2d2 机箱的同步池。

## 使用 r2d2(同步)

据我所知, [r2d2](https://github.com/sfackler/r2d2) 机箱是第一个被广泛使用的连接池，它现在仍然被广泛使用。这个底箱的 Redis 风味是`[r2d2-redis](https://github.com/sorccu/r2d2-redis)`。

因为我们现在使用的是连接池，所以这个池的创建也需要在`r2d2`模块中处理。这里是起点:

```
pub type R2D2Pool = r2d2::Pool<RedisConnectionManager>;
pub type R2D2Con = r2d2::PooledConnection<RedisConnectionManager>;

const CACHE_POOL_MAX_OPEN: u32 = 16;
const CACHE_POOL_MIN_IDLE: u32 = 8;
const CACHE_POOL_TIMEOUT_SECONDS: u64 = 1;
const CACHE_POOL_EXPIRE_SECONDS: u64 = 60;

pub fn connect() -> Result<r2d2::Pool<RedisConnectionManager>> {
    let manager = RedisConnectionManager::new(REDIS_CON_STRING).map_err(RedisClientError)?;
    r2d2::Pool::builder()
        .max_size(CACHE_POOL_MAX_OPEN)
        .max_lifetime(Some(Duration::from_secs(CACHE_POOL_EXPIRE_SECONDS)))
        .min_idle(Some(CACHE_POOL_MIN_IDLE))
        .build(manager)
        .map_err(|e| RedisPoolError(e).into())
}

```

在定义了一些常量来配置连接池之后，比如打开和空闲连接、连接超时和连接的生命周期，连接池本身是使用`RedisConnectionManager`创建的，它获取传递给它的 redis 连接字符串。

不用太担心配置值；他们只是来炫耀他们有空。正确设置它们将取决于您的用例，并且大多数连接池都有一些适用于基本应用程序的默认值。

我们需要一种方法从池中获取连接，并从 Redis 中再次设置和获取帮助器。

```
pub fn get_con(pool: &R2D2Pool) -> Result<R2D2Con> {
    pool.get_timeout(Duration::from_secs(CACHE_POOL_TIMEOUT_SECONDS))
        .map_err(|e| {
            eprintln!("error connecting to redis: {}", e);
            RedisPoolError(e).into()
        })
}

pub fn set_str(pool: &R2D2Pool, key: &str, value: &str, ttl_seconds: usize) -> Result<()> {
    let mut con = get_con(&pool)?;
    con.set(key, value).map_err(RedisCMDError)?;
    if ttl_seconds > 0 {
        con.expire(key, ttl_seconds).map_err(RedisCMDError)?;
    }
    Ok(())
}

pub fn get_str(pool: &R2D2Pool, key: &str) -> Result<String> {
    let mut con = get_con(&pool)?;
    let value = con.get(key).map_err(RedisCMDError)?;
    FromRedisValue::from_redis_value(&value).map_err(|e| RedisTypeError(e).into())
}

```

如你所见，这非常类似于直接使用`redis-rs`板条箱。不是传入一个`redis::Client`，而是传入一个池，我们尝试使用配置的超时从池中获取一个连接。

在`set_str`和`get_str`中，唯一的变化是在每次调用这些函数时都调用了`get_con`。在前一种情况下，这意味着要为每个命令创建一个 Redis 连接，这样效率会更低。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

但是因为我们有一个预先创建连接的池，所以我们可以在这里获得它们，它们可以在请求之间自由共享，而在我们当前的请求中没有 Redis 操作。

此外，请注意，除了`async`和`await`部分之外，API 几乎完全相同，所以从一个部分移动到另一个部分并不太痛苦，如果这是您在某些时候必须要做的事情。

## 使用 mobc(异步)

说到异步，让我们看看三种方法中的最后一种:异步连接池。

如上所述，这有几个板条箱。我们将使用 [mobc](https://github.com/importcjj/mobc) ，主要是因为我已经在生产环境中成功地使用了它。同样，其他选项的工作方式类似，看起来都是很好的选项。

让我们定义配置并创建池。

```
pub type MobcPool = Pool<RedisConnectionManager>;
pub type MobcCon = Connection<RedisConnectionManager>;

const CACHE_POOL_MAX_OPEN: u64 = 16;
const CACHE_POOL_MAX_IDLE: u64 = 8;
const CACHE_POOL_TIMEOUT_SECONDS: u64 = 1;
const CACHE_POOL_EXPIRE_SECONDS: u64 = 60;

pub async fn connect() -> Result<MobcPool> {
    let client = redis::Client::open(REDIS_CON_STRING).map_err(RedisClientError)?;
    let manager = RedisConnectionManager::new(client);
    Ok(Pool::builder()
        .get_timeout(Some(Duration::from_secs(CACHE_POOL_TIMEOUT_SECONDS)))
        .max_open(CACHE_POOL_MAX_OPEN)
        .max_idle(CACHE_POOL_MAX_IDLE)
        .max_lifetime(Some(Duration::from_secs(CACHE_POOL_EXPIRE_SECONDS)))
        .build(manager))
}

```

这和 r2d2 很像，不是巧合；许多连接池板条箱从 r2d2 出色的 API 中获得了灵感。

要完成异步池实现，连接、获取和设置:

```
async fn get_con(pool: &MobcPool) -> Result<MobcCon> {
    pool.get().await.map_err(|e| {
        eprintln!("error connecting to redis: {}", e);
        RedisPoolError(e).into()
    })
}

pub async fn set_str(pool: &MobcPool, key: &str, value: &str, ttl_seconds: usize) -> Result<()> {
    let mut con = get_con(&pool).await?;
    con.set(key, value).await.map_err(RedisCMDError)?;
    if ttl_seconds > 0 {
        con.expire(key, ttl_seconds).await.map_err(RedisCMDError)?;
    }
    Ok(())
}

pub async fn get_str(pool: &MobcPool, key: &str) -> Result<String> {
    let mut con = get_con(&pool).await?;
    let value = con.get(key).await.map_err(RedisCMDError)?;
    FromRedisValue::from_redis_value(&value).map_err(|e| RedisTypeError(e).into())
}

```

现在看起来应该很熟悉了。解决方案有点像第一个和第二个选项的混合:池被传入，并在开始时获取一个连接，但这次是以异步方式使用`async`和`await`。

## 将这一切结合在一起

既然我们已经介绍了基础知识，并且有了三种独立的连接 Redis 并与之交互的方法，下一步就是将它们都集成到一个 warp web 应用程序中。

让我们从`main`函数开始，了解应用程序是如何构建的，以及运行它需要什么。

```
#[tokio::main]
async fn main() {
    let redis_client = redis::Client::open(REDIS_CON_STRING).expect("can create redis client");
    let mobc_pool = mobc_pool::connect().await.expect("can create mobc pool");
    let r2d2_pool = r2d2_pool::connect().expect("can create r2d2 pool");

    let direct_route = warp::path!("direct")
        .and(with_redis_client(redis_client.clone()))
        .and_then(direct_handler);

    let r2d2_route = warp::path!("r2d2")
        .and(with_r2d2_pool(r2d2_pool.clone()))
        .and_then(r2d2_handler);

    let mobc_route = warp::path!("mobc")
        .and(with_mobc_pool(mobc_pool.clone()))
        .and_then(mobc_handler);

    let routes = mobc_route.or(direct_route).or(r2d2_route);
    warp::serve(routes).run(([0, 0, 0, 0], 8080)).await;
}

```

看起来不需要太多，代码很少！

在应用程序开始时，我们将尝试创建一个 Redis 客户机和两个连接池。如果他们中的任何一个失败了，我们就会失败。

接下来，我们将讨论每种不同方法的路线定义。这些看起来非常相似，唯一的区别是`with_xxx`滤镜。

```
fn with_redis_client(
    client: redis::Client,
) -> impl Filter<Extract = (redis::Client,), Error = Infallible> + Clone {
    warp::any().map(move || client.clone())
}

fn with_mobc_pool(
    pool: MobcPool,
) -> impl Filter<Extract = (MobcPool,), Error = Infallible> + Clone {
    warp::any().map(move || pool.clone())
}

fn with_r2d2_pool(
    pool: R2D2Pool,
) -> impl Filter<Extract = (R2D2Pool,), Error = Infallible> + Clone {
    warp::any().map(move || pool.clone())
}

```

上面的过滤器只是简单的提取过滤器，使传递进来的东西对使用它们的处理程序可用。在每一种情况下，客户端和池都被克隆并移入处理程序中—这里没有魔法。

最后，但同样重要的是，让我们看看实际的处理程序，看看如何使用我们上面定义的 API。

首先，直接使用`redis-rs`:

```
async fn direct_handler(client: redis::Client) -> WebResult<impl Reply> {
    let mut con = direct::get_con()
        .await
        .map_err(|e| warp::reject::custom(e))?;
    direct::set_str(&mut con, "hello", "direct_world", 60)
        .await
        .map_err(|e| warp::reject::custom(e))?;
    let value = direct::get_str(&mut con, "hello")
        .await
        .map_err(|e| warp::reject::custom(e))?;
    Ok(value)
}

```

按照`with_direct`过滤器，我们得到传入的`redis::Client`,并立即创建一个 Redis 连接。

然后，处理程序将`direct world`写入 Redis 中的`hello`键，然后立即从那里读取。

`r2d2`处理程序看起来非常相似，但是有一个`R2D2Pool`被传入，没有所有的`async`和`await`的东西。

```
async fn r2d2_handler(pool: R2D2Pool) -> WebResult<impl Reply> {
    r2d2_pool::set_str(&pool, "r2d2_hello", "r2d2_world", 60)
        .map_err(|e| warp::reject::custom(e))?;
    let value = r2d2_pool::get_str(&pool, "r2d2_hello").map_err(|e| warp::reject::custom(e))?;
    Ok(value)
}

```

当然，在异步 web 框架中使用同步池通常是不明智的。但是有很多框架不是基于 async/await 的，对于这些框架来说，同步池将是完美的。

最后，下面是使用 mobc 的异步池:

```
async fn mobc_handler(pool: MobcPool) -> WebResult<impl Reply> {
    mobc_pool::set_str(&pool, "mobc_hello", "mobc_world", 60)
        .await
        .map_err(|e| warp::reject::custom(e))?;
    let value = mobc_pool::get_str(&pool, "mobc_hello")
        .await
        .map_err(|e| warp::reject::custom(e))?;
    Ok(value)
}

```

如果您看一下三个 Redis 客户机实现——过滤器、路由和处理程序——您会发现它们差别很小。这意味着您将能够根据自己的需要从一个迁移到另一个，而不必从头重写一切。

终于到了运行应用程序的时候了。

```
cargo run

```

接下来，用 Docker 启动一个本地 Redis 实例。

```
docker run -p 6379:6379 redis:5.0

```

如果您使用`curl`来调用上面定义的端点，您应该会看到正确的响应。

```
curl http://localhost:8080/direct
curl http://localhost:8080/r2d2
curl http://localhost:8080/mobc

```

对于很少调用 Redis 的非常简单的应用程序，每次都获得一个新的连接是非常好的。但是如果每个请求有几个 Redis 调用，同时有许多请求发生，那么连接池将大大提高性能。

当有疑问时，进行测试、分析，并根据获得的数据做出明智的决定。

你可以在 [GitHub](https://github.com/zupzup/rust-redis-web-example) 上找到这个例子的完整代码。

## 结论

在本文中，我们探讨了在 Rust web 应用程序中使用 Redis 的三种方法。最适合您的应用程序的方法将取决于您的用例以及您构建应用程序的方式。无论如何，能够在板条箱和方法之间切换而不必改变太多是一个巨大的优势。

与同步和异步连接池的丰富生态系统一起，板条箱已准备好投入生产使用，并在可用性和性能之间取得了巨大的平衡。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。