# 在 Deno 中使用 Redis

> 原文：<https://blog.logrocket.com/using-redis-in-deno/>

缓存是一个临时的数据存储区，信息保存在这里供以后使用。实现缓存系统有助于提高 Deno 应用程序的速度，因为它获取资源的时间更少。

在本教程中，我们将探索缓存数据的概念，并向您展示如何使用 Deno 集成 Redis 特性。

## 什么是德诺？

[Deno](https://blog.logrocket.com/deno-1-0-what-you-need-to-know/) 是一个现代的、安全的 JavaScript 和 [TypeScript](https://blog.logrocket.com/whats-new-in-typescript-4-0/) 运行时，使用 V8 引擎。Deno 内置了对 TypeScript 的支持，这意味着您不需要编写额外的 webpack 配置来在您的应用程序中设置 TypeScript。

Deno 默认采用安全性，这意味着它不允许文件、网络和环境访问，除非您明确允许。

## 什么是里兹？

Redis 是一个速度极快的内存数据结构项目，用于实现具有可选持久性的分布式内存键值数据库。Redis 可以用作缓存系统，也可以用作消息拦截器。

就像数据库一样，Redis 支持数据结构，比如字符串、散列、列表、集合、带有范围查询的排序集合和流。基本上，Redis 使用 RAM 存储数据，速度非常快。但是，如果重新启动服务器，这些值将会丢失，除非启用 Redis 持久性，该持久性以指定的时间间隔执行数据集的时间点快照。

## 如何将 Redis 与 Deno 一起使用

在开始编写 [Deno](https://deno.land/) 代码之前，必须在本地机器上安装 Redis。

要在 Mac 上安装 [Redis](https://redis.io/) ，您可以通过运行以下命令来使用 Homebrew:

```
brew install redis

```

安装 Redis 后，将其作为一项服务在本地计算机上运行:

```
brew services start redis

```

如果要停止 Redis 服务，请运行:

```
brew services stop redis

```

重新启动，运行:

```
brew services restart redis

```

要确认 Redis 在我们的本地机器上正确运行，请运行:

```
redis-cli ping

```

如果这个命令在终端上返回`PONG`，那么您就可以开始了。

下一步是确认 Deno 是否正确安装在您的本地机器上。打开您的终端，键入以下内容:

```
deno --version

```

如果这显示了`deno`、`V8`和 TypeScript 的版本，那么就可以开始了。否则，您可以使用自制软件安装它:

```
brew install deno

```

现在，您可以为您的项目创建一个目录。我们将在我们的`redis.ts`文件中测试 Redis 特性。

## 创建 Redis 连接

每当您想在项目中使用 Redis 时，第一步是创建一个 Redis 连接。默认情况下，Redis 运行在端口`6379`上。

要创建连接，请将以下内容添加到您的`redis.ts`文件中:

```
import { connect } from "https://denopkg.com/keroxp/deno-redis/mod.ts";
const redis = await connect({
  hostname: "127.0.0.1",
  port: 6379
});
console.log(await redis.ping())

```

使用`connect`方法通过指定的端口连接到 Redis CLI。要测试 Redis 连接，使用`redis.ping()`方法，该方法返回一个您必须等待的承诺。

要运行应用程序，您必须首先传递`--allow-net`标志以允许网络特权。运行`deno run--allow-net redis.ts`启动应用程序。这将在你的控制台上记录`PONG`，这表明连接是成功的。

## 设置键值对

您可以使用`set`和`get`方法在 Redis 中存储和检索数据。`set`方法接受两个参数:`name`和要存储的值。

```
await redis.set('name', 'Wisdom Ekpot');
let name = await redis.get('name')
console.log(name)

```

你应该总是使用`redis`方法，因为它总是返回一个承诺。

## 存储数据

您可以使用提供的方法在 Redis 中存储数据，比如`hmset`。

`hmset`用于设置一个字段的值，该字段被指定为 hash 的存储关键字。此方法会覆盖任何现有字段。如果该键不存在，则会创建一个新的键来保存哈希。

您可以编写一个简单的函数添加到 Redis 中:

```
let add = async(key:string,name:string,email:string) => {
    let addPerson = await redis.hmset(key, {
        'name': name,
        'email': email
    })
    return addPerson
}
console.log(await add('key1','Wisdom Ekpot','[email protected]'))

```

这将使用键`key1`向 Redis 添加一个新项目，并在控制台上返回`OK`。

## 使用密钥获取数据

`hgetall`返回特定键的散列的所有字段和值。

您可以使用以下键获取存储在 Redis 中的数据:

```
let getParticular = async (id:string) => {
   return await redis.hgetall(id);
}
console.log(await getParticular('key1'))

```

## 删除带有密钥的项目

您可以使用`del`方法删除一个键，这需要将键名作为参数:

```
let deleteKey = async (id:string) => {
    let deleted = await redis.del(id);
    return deleted
}

console.log(await deleteKey('key1'))

```

## 重定向群集和配置

Redis 集群是一种跨多个 Redis 节点自动粉碎数据的机制。Redis `meet`方法在启用集群模式的情况下连接多个 Redis 节点。

要创建一个新的集群，使用`redis.meet()`方法，该方法将`port`作为一个参数:

```
await redis.cluster_meet("127.0.0.1", <port>);

```

现在可以使用`redis.nodes`方法列出所有创建的节点:

```
 await redis.cluster_nodes();

```

现在，这实际上不起作用，因为默认情况下集群是禁用的。您可能会遇到这个错误:`this instance has cluster support disabled`。

Redis 使您能够检查您的配置。您可以像这样检查集群是否已启用:

```
let config = await redis.config_get("cluster-enabled");
console.log(config)

```

这将返回控制台上的`[ "cluster-enabled", "no" ]`。要启用它，使用`config_set`方法，该方法接受`config_name`和配置的值。

因此，要启用集群，您可以这样做:

```
await redis.config_set('cluster-enabled', 'yes')

```

## Redis raw 命令

Deno 还使您能够运行原始 Redis 命令。所有原始命令都必须通过`executor`类。这个命令将回复作为承诺返回，所以等待请求总是一个好主意。

```
await redis.executor.exec("SET", "name", "Wisdom Ekpot")
let get = await redis.executor.exec("GET", "name");
console.log(get)

```

## 结论

Redis 提供了大量旨在帮助您扩展应用程序的特性。将 Redis 集成到 Deno 应用程序中可以使它变得更快，因为从缓存中调用数据非常高效。

本教程中使用的源代码可以在 [GitHub](https://github.com/Wisdom132/deno/tree/master/redis) 上获得。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)