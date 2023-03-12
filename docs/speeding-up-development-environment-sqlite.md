# 使用 SQLite 加速您的开发环境

> 原文：<https://blog.logrocket.com/speeding-up-development-environment-sqlite/>

当我们寻求不同的工具和技术来加速我们的开发工作流时，数据库管理仍然被忽视。在使用 PostgreSQL 这样的关系数据库时，设置和维护它仍然是严格和耗时的。

幸运的是，SQLite，一个自包含的 SQL 数据库引擎，减少了我们开发环境中使用数据库的大量劳动。

## SQLite 如何工作

来自[官方文件](https://www.sqlite.org/index.html):

> SQLite 是一个 C 语言库，实现了一个[小](https://www.sqlite.org/footprint.html)、[快](https://www.sqlite.org/fasterthanfs.html)、[自含](https://www.sqlite.org/selfcontained.html)、[高可靠](https://www.sqlite.org/hirely.html)、[全功能](https://www.sqlite.org/fullsql.html) SQL 数据库引擎。

除了速度快之外，SQLite 是自包含的，这使它成为许多应用程序的最佳选择，无论是移动应用程序还是 web 应用程序。它消除了应用程序依赖外部数据库来运行的需要。考虑到这一点，SQL 可以成为在我们的开发环境中使用数据库的一个非常好的选择。

因为我们的 SQLite 数据库将存在于我们的项目中，所以它的内容将在我们所有的开发环境中保持一致。数据库设置只需完成一次，并且在不同开发环境中对项目进行其他初始化时，我们不需要做任何额外的工作来设置数据库。

这将大大加快我们的工作流程，因为它消除了数据不一致的问题，以及每次我们决定使用新的开发环境时连接到新的数据库、迁移和播种数据的困难。

### 何时不使用 SQLite

在许多情况下，SQLite 是一个很好的选择，但是考虑到您的应用程序的大小和类型，它可能不是您的生产环境的最佳选择。

SQLite 不支持并发。这意味着一次只有一个用户可以写入数据库，因此当使用需要多个用户并发写入数据库的应用程序时，PostgreSQL 可能是您的生产环境的更好选择。

此外，SQLite 可以很好地处理每天点击量不超过 100，000 的应用程序。如果您的应用程序预计会超过这个限制，那么 SQLite 可能不是生产的最佳选择。然而，由于我们关注的是我们的开发环境，这可能不是一个问题。

让我们通过构建 Node.js 应用程序来演示如何使用 SQLite 来加速我们的开发环境。我们将展示 SQLite 如何与 PostgreSQL 等其他关系数据库一起使用，SQLite 用于开发，PostgreSQL 用于生产。

## 搭建我们的 Node.js 应用程序

让我们首先用 Express.js 搭建一个新的 Node.js 应用程序。让我们通过在终端上运行以下命令来实现这一点:

```
node --version
```

这将返回我们已经安装的节点的版本。[如果命令返回错误消息，单击此处](https://nodejs.org/en/download/)查看节点安装说明。

接下来，我们将安装 Express application generator，我们将使用它来搭建我们的节点应用程序:

```
npm install -g express-generator
```

现在让我们在终端上运行以下命令:

```
express node_sqlite
```

然后`npm install`安装默认的 npm 软件包。

如果您正确遵循了所有步骤，那么当您进入新的应用程序目录并运行`npm start`时，您应该能够查看您的节点应用程序:

```
cd node_sqlite
npm start
```

我们的新应用程序目录应该是这样的:

```
├── bin
├── public
├── routes
├── views
├── package.json
├── app.js
```

## 安装所需的依赖项

> *   为了处理我们的数据库查询，我们将使用 Sequelize，一个基于 promise 的 Node.js ORM(对象关系映射器)
> *   为了让我们的应用程序可以访问我们的环境变量，我们需要`dotenv`包

让我们用下面的命令安装我们的包:

```
npm i -s sequelize sequelize-cli dotenv
```

在我们初始化 Sequelize 之前，我们将把下面的脚本添加到我们的`package.json`文件中，该文件可以在根目录中找到:

```
"sequelize": "node_modules/.bin/sequelize"
```

这使得我们更容易从终端访问 Sequelize 命令。使用我们新添加的脚本，我们的`package.json`文件应该是这样的:

```
// ./package.json

{
  "name": "sqlite-test",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www",
    "sequelize": "node_modules/.bin/sequelize"
  },
  "dependencies": {
    ...
  }
}
```

现在，让我们通过在终端上运行以下命令来初始化 Sequelize:

```
npm run sequelize init
```

这将为存放模型文件生成一个`models`文件夹，我们将使用它来描述我们数据库的逻辑结构，并为我们的数据库配置文件生成一个`config`文件夹。

我们希望将我们的生产数据库 URL 存储为一个环境变量。这简化了将来更新它的过程。为此，我们将在根目录下创建一个名为`.env`的文件，并向其中添加一个变量`DATABASE_URL`:

```
// .env

DATABASE_URL= your database url here
NODE_ENV=development
```

注意，我们还在我们的`.env`文件中添加了一个变量`NODE_ENV`。这表明我们将在其中运行我们的应用程序的环境。这里，我们使用了`development`。

现在我们已经设置好了，让我们导航到`./config/config.json`文件。这是 Sequelize 为我们的应用程序生成的文件。默认情况下，它应该是这样的:

```
{
  "development": {
    "username": "root",
    "password": null,
    "database": "database_development",
    "host": "127.0.0.1",
    "dialect": "mysql",
    "operatorsAliases": false
  },
  "test": {
    "username": "root",
    "password": null,
    "database": "database_test",
    "host": "127.0.0.1",
    "dialect": "mysql",
    "operatorsAliases": false
  },
  "production": {
    "username": "root",
    "password": null,
    "database": "database_production",
    "host": "127.0.0.1",
    "dialect": "mysql",
    "operatorsAliases": false
  }
}
```

因为我们将从这里访问我们的生产数据库 URL(存储在我们的`.env`文件中),所以让我们将它转换成一个 JavaScript 模块，而不是一个 JSON 文件。为此，我们首先将文件`config.json`重命名为`config.js`，然后用下面的代码块替换它的内容:

```
module.exports = {
  development: {
  },
  test: {
  },
  production: {
  },
};
```

接下来，我们将详细介绍我们的开发、测试和生产环境。让我们编辑我们的`config.js`文件，如下所示:

```
require('dotenv').config();

module.exports = {
  development: {
    dialect: "sqlite",
    storage: "./sqlite-dev.db"
  },
  test: {
    dialect: "sqlite",
    storage: "./sqlite-test.db"
  },
  production: {
    url: process.env.DATABASE_URL,
    dialect: 'postgres',
  },
};
```

请注意我们是如何将`sqlite`用于开发和测试环境，然后将`postgres`用于生产环境的。根据我们应用程序的类型/规模，我们可以继续使用`sqlite`作为我们的生产环境。我们还在`line 1`上初始化了我们的`dotenv`模块。

## 生成我们的数据库模型

下一步，我们将使用 Sequelize 在数据库中创建一个表。如果我们的节点环境在我们的`dotenv`文件中被设置为`development`或`test`(正如我们所做的)，Sequelize 将在我们的根目录中生成一个新的 SQLite 数据库，其名称与我们在创建表之前在配置文件中使用的名称相同。

让我们通过在终端上运行以下命令来创建一个名为`User`的表:

```
npm run sequelize -- model:generate --name User --attributes username:string,password:string
```

这将在我们的数据库中创建一个包含列`username`和`password`的表`User`。当我们导航到我们的根目录时，我们会看到一个名为`sqlite-dev.db`的文件。这是我们新创建的 SQLite 数据库。

要在数据库管理系统中查看我们的 SQLite 数据库，我们可以使用 DB Browser for SQLite 工具。下面是[下载链接](https://sqlitebrowser.org/)。

## 为我们的数据库生成一个种子文件

种子文件用于向我们的数据库添加初始数据。这些数据通常用于测试。在我们的例子中，我们将向 SQLite 数据库添加三个默认用户。要为我们的用户表生成一个种子文件，让我们在终端上运行以下命令:

```
npm run sequelize -- seed:generate --name user
```

这将在目录`./seeders`中创建一个新文件。根据日期的不同，它的名字看起来会类似于`20200428202218-user.js`。

默认情况下，生成的文件应该如下所示:

```
'use strict';
module.exports = {
  up: (queryInterface, Sequelize) => {
    /*
      Add altering commands here.
      Return a promise to correctly handle asynchronicity.
      Example:
      return queryInterface.bulkInsert('People', [{
        name: 'John Doe',
        isBetaMember: false
      }], {});
    */
  },
  down: (queryInterface, Sequelize) => {
    /*
      Add reverting commands here.
      Return a promise to correctly handle asynchronicity.
      Example:
      return queryInterface.bulkDelete('People', null, {});
    */
  }
};
```

让我们编辑成这样:

```
'use strict';
module.exports = {
  up: queryInterface =>
    queryInterface.bulkInsert('Users', [
      {
        username: 'johndoe',
        password: 'dontstorepasswordsthisway',
        createdAt: new Date().toDateString(),
        updatedAt: new Date().toDateString()
      },
      {
        username: 'janedoe',
        password: 'youreallyshouldhashpasswords',
        createdAt: new Date().toDateString(),
        updatedAt: new Date().toDateString()
      },
      {
        username: 'ritadoe',
        password: 'outofpasswordideas',
        createdAt: new Date().toDateString(),
        updatedAt: new Date().toDateString()
      }
    ], {}),
  down: (queryInterface, Sequelize) => {
    /*
      Add reverting commands here.
      Return a promise to correctly handle asynchronicity.
      Example:
      return queryInterface.bulkDelete('People', null, {});
    */
  }
};
```

现在我们已经生成了种子文件，我们可以通过在终端上运行以下命令来播种数据库:

```
npm run sequelize db:seed:all
```

我们应该会看到类似于以下内容的成功消息:

```
Loaded configuration file "config\config.js".
Using environment "development".
== 20200428202218-user: migrating =======
== 20200428202218-user: migrated (0.020s)
```

## 结论

完成这些后，我们可以继续创建控制器来查询和改变我们的 SQLite 数据库。使用 SQLite 使得访问我们的数据库更快，因为不需要从我们的应用程序进行外部调用。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

正如我们在介绍中所讨论的，我们的数据库设置只需要做一次，然后在不同的开发环境中对我们的项目进行其他初始化，就不需要为我们的应用程序设置或配置数据库了。

下面是我们 API 设置的 GitHub 存储库的链接: [node_sqlite_setup](https://github.com/ebenezerdon/node_sqlite)

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.