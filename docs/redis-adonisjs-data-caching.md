# 在 AdonisJs 中使用 Redis 进行数据缓存

> 原文：<https://blog.logrocket.com/redis-adonisjs-data-caching/>

AdonisJs 是专门为编写微服务而构建的 Node.js 框架。像 must 后端框架一样，AdonisJs 支持 Redis，这有助于加快应用程序中的请求/响应时间，使过程无缝和应用程序轻量级。Redis，代表**Re**mote**Di**rectory**S**server，是一个开源的、[内存](https://en.wikipedia.org/wiki/In-memory_database)数据结构存储。

Redis 是一个磁盘持久的键值数据库，支持多种数据结构或数据类型，这意味着它支持基于映射键值的字符串来存储和检索数据(类似于传统数据库中支持的数据模型)，同时也支持其他复杂的数据结构，如列表、集合等。

接下来，我们将看看 Redis 支持的数据结构。简单地说，它缓存你的文件，这样你就不需要每次都请求数据库。

根据每月[的数据库引擎排名](https://db-engines.com/en/ranking/key-value+store)，Redis 通常是最受欢迎的键值数据库，也是科技界十大最受欢迎的数据库之一。

> **注意**，您需要在本地机器上安装 Redis，因为如果没有在本地安装，它将无法与 AdonisJs 一起工作。

关于如何在您的开发机器上安装 Redis 的说明可以在下面找到:

在安装过程中，您的默认端口应该设置为`6379`。这是 Redis 的专用端口，尽管如果另一个端口正在使用，它也会使用端口`6380`。

## 简介和先决条件

在本教程中，我们将介绍如何:

*   创建 AdonisJs 应用程序的新实例
*   安装 Redis 并在我们的应用程序上设置它
*   配置我们的数据库来发出请求
*   使用 Redis' `get`和`set`方法
*   展示使用 Redis 和从数据库直接调用时请求时间的差异
*   使用 Redis 的发布/订阅方法来发布和订阅请求

要继续学习本教程，您应该具备 JavaScript、AdonisJs 和 SQL 数据库的基础知识，因为本教程不涉及基础知识。

### 系统需求

首先，我们需要在本地计算机上全局安装 Adonis CLI，以便我们可以使用 CLI 命令来创建和服务我们的应用程序:

```
npm -g @adonisjs/cli
```

接下来，我们可以创建一个新的 AdonisJs 应用程序，并使用以下代码运行它；这将创建 AdonisJs 的新实例。然后，我们进入应用程序文件夹并运行应用程序:

```
adonis new adonis_redis

cd adonis_redis

adonis serve --dev
```

## 为 AdonisJs 应用程序配置 Redis

现在我们已经启动并运行了我们的应用程序，我们将安装 Redis:

```
npm install @adonisjs/redis
```

这将创建一个名为`start/redis.js`的新文件，其中将包含我们的`subscribe`方法。我们将在教程的后面解决这个问题。

安装完成后，我们可以在`start/app.js`文件中注册 Redis 提供者:

```
const providers = [
  '@adonisjs/redis/providers/RedisProvider'
]
```

然后，我们将下面的代码添加到我们的`server.js`文件中，以在应用程序中启动 Redis。如果没有这个附加功能，Redis 将不会在应用程序每次运行时都运行。

```
new Ignitor(require('@adonisjs/fold'))
  .preLoad('start/redis')
  .appRoot(__dirname)
```

现在我们将把我们的`REDIS_CONNECTION`添加到`.env`文件中，它将指向`config/redis.js`文件中的`local`配置。

```
REDIS_CONNECTION=local
```

完成后，我们将继续创建控制器、路由和模型，以便从数据库中获取数据，并使用 Redis 缓存数据。然后我们将在 Postman 中从 Redis 获取数据，以查看响应时间的差异。

## 设置数据库

拥有一个数据库是本教程的基础，所以让我们继续设置一个数据库。为此，我们将使用下面的代码在应用程序中安装`@adonisjs/lucid`包。Lucid 是一个用于 Adonis 的 SQL ORM，它以一种[活动记录模式](https://en.wikipedia.org/wiki/Active_record_pattern)存储数据:

```
adonis install @adonisjs/lucid
```

安装完成后，我们可以向我们的`start/app.js`文件添加一个 Lucid 提供程序，这将把 Lucid 包添加到应用程序的提供程序列表中:

```
const providers = [
  '@adonisjs/lucid/providers/LucidProvider'
]

const aceProviders = [
  '@adonisjs/lucid/providers/MigrationsProvider'
]
```

完成后，我们使用下面的代码将`mysql`安装到我们的应用程序中:

```
npm install mysql
```

然后我们配置我们的`.env`文件，使用`mysql`配置连接到我们的数据库。这很重要，因为默认情况下，AdonisJs 使用 SQLite 进行数据存储。

```
DB_CONNECTION=mysql
DB_USER=root
DB_PASSWORD=
DB_DATABASE=adonis_redis
```

现在将`config/database.js`中的连接更改为 MySQL 连接:

```
connection: Env.get('DB_CONNECTION', 'mysql'),
```

## 创建控制器

我们将创建一个`User`控制器，从我们的数据库中获取所有用户。为此，我们使用下面的代码生成一个新的控制器:

```
adonis make:controller User --type http
```

然后，我们将我们的`database`包导入到我们的`UserController.js`文件中，以连接和访问数据库:

```
// app/Controllers/UserController.js

const Database = use('Database')
```

接下来，我们添加一个`async`函数，该函数将调用数据库并获取所有用户，然后将其作为 JSON 返回:

```
// app/Controllers/UserController.js

async index({ response }) {
  let users = await Database.table('users').select('*');
  return response.status(201).json({
      status: true,
      message: 'fetched all users',
      data: users
  });
}
```

## 创建`users`路线

设置好控制器后，我们现在可以配置路由，这样我们就可以对应用程序进行 API 调用。转到`start/routes.js`并添加:

```
// start/routes.js

Route.get('users', 'UserController.index');
```

我们创建了一个`users`路由，它调用了`User`控制器中的`index`函数。现在我们可以使用我们在[邮递员](https://www.postman.com/)中的路线来获取我们所有的用户。

![Response Time Using Postman](img/3a4c977a4c4fa3d2c09366ae15da2c39.png)

这里的响应时间是 2.07 秒，这对于一个低流量的 web 应用程序来说是可以的。但是，当您运行一个高流量应用程序，同时有许多请求发生时，这使得应用程序的加载时间非常慢。

现在我们已经测试了使用 Redis 缓存数据的应用程序，让我们继续使用 Redis 来查看响应时间的巨大减少，这是使应用程序更快、更用户友好的关键。

## 使用`get` / `set`方法

`get`方法从 Redis 获取一个键值并返回一个字符串。`set`方法保存一个带有字符串值的键，如果键值已经包含了一个字符串，那么它会覆盖这个键值。我们可以在我们的`app/Controllers/Http/UserController.js`文件中使用这些方法来缓存数据库中的用户:

```
// app/controllers/Http/UserController.js

async index() {
  const cachedUsers = await Redis.get('users')
        if (cachedUsers) {
          return JSON.parse(cachedUsers)
        }

        const users = await Database.table('users').select('*');
        await Redis.set('users', JSON.stringify(users))
        return users
      }
}
```

上面，我们从键`users`中获取一个值，如果它不为空，我们将把它作为 JSON 文件返回。如果它是空的，我们从数据库中获取数据，然后用数据库中的数据设置一个新的键。

## 设置发布/订阅

发布/订阅实现了消息传递系统，其中发送者(Redis 中称为发布者)发送消息，而接收者(订阅者)接收消息。

为了设置我们的发布/订阅，我们将在`start/redis.js`中创建一个订阅。sub 将直接连接到数据库，并在每次启动发布序列时将数据保存在 JSON 中。

首先，在`start/redis.js`中导入数据库和 Redis:

```
const Database = use('Database')
const Redis = use('Redis')
```

然后创建一个新的`subscribe`方法，从数据库中获取所有用户。`set`创建一个新的键`users`，它包含 JSON 格式的所有用户的值，并将其存储在 Redis 中。

```
// start/redis.js

Redis.subscribe('users', async () => {
    let users = await Database.table('users').select('*');
    await Redis.set('users', JSON.stringify(users));
})
```

现在，在我们的`User`控制器中，我们导入 Redis，检查是否已经创建了一个`users` JSON 文件，并返回该文件；否则，我们将访问数据库，获取所有用户，并发布响应。

```
// app/Controller/Http/UserController.js

const Database = use('Database')
const Redis = use('Redis')

async index({ response }) {
        let cachedUsers = await Redis.get('users');
        if(cachedUsers) {
            let users = JSON.parse(cachedUsers);
            return response.status(201).json({
                status: true,
                message: 'fetched all users',
                data: users
            });
        }else {
            let users = await Database.table('users').select('*');
            Redis.publish('users', '');
            return response.status(201).json({
                status: true,
                message: 'fetched all users',
                data: users
            });
        }
    }
```

现在在邮递员上运行你的`[http://127.0.0.1:3333/users](http://127.0.0.1:3333/users)`。请注意响应时间的差异:

![Response Time Using Redis](img/4cc1f899245c009926565ee66c7f29fb.png)

请求/响应时间大幅缩短，从无 Redis 时的 2.07 秒缩短到有 Redis 时的 33 毫秒！

## 结论

在本教程中，我们已经能够创建一个新的 AdonisJs 应用程序并在其中安装 Redis。然后，我们配置 Redis 来缓存数据，配置我们的数据库，使用 Redis `get`、`set`、publish 和 subscribe 方法，并减少应用程序中的请求/响应时间。

您可以继续将[库](https://github.com/Kennethekandem/adonis_redis_caching.git)克隆到您的系统中，并根据您的喜好进行调整。你必须复制`.env.example`文件并粘贴到`.env`中，然后使用`adonis key:generate`创建一个新的密钥。希望这篇教程对你有帮助。✌️

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)