# 详细了解 SQL.js 的基本特性

> 原文：<https://blog.logrocket.com/detailed-look-basic-sqljs-features/>

SQL 或结构化查询语言是一种特定于领域的语言，旨在访问和操作关系数据库中的数据。在 SQL 语句的帮助下，您可以在关系数据库上执行创建、更新、检索和删除数据等操作。

关系数据库是一种数据模型，它使用行和列来组织表中的数据。虽然可能有多个使用 SQL 的关系数据库管理系统，但几乎所有这些系统都被设计为作为服务器端进程工作。其中包括:

*   关系型数据库
*   一种数据库系统
*   SQLite
*   MSSQL

在本教程中，我们将讨论 SQL.js，这是一个 JavaScript SQL 库，使您能够完全在浏览器中创建和查询关系数据库。

我们将通过每个部分的详细示例来完成以下步骤:

## 什么是 SQL.js？

[SQL.js](https://github.com/sql-js/sql.js/) 是一个 JavaScript 库，允许您完全在浏览器中创建和查询关系数据库。它使用存储在浏览器内存中的虚拟数据库文件，所以它不会持久保存对数据库所做的更改。

这个库还使用 [Emscripten](https://emscripten.org/docs/introducing_emscripten/about_emscripten.html) 将 SQLite 编译成 [WebAssembly (Wasm)](https://webassembly.org/) 。有了这个特性，您可以轻松地在 SQL.js 中使用现有的 SQLite 数据库，还可以将 SQL.js 中创建的数据库转换为 SQLite。

## 使用 SQL.js 的利与弊

使用 SQL.js 有几个好处。它是为客户端构建的，完全在客户端工作，这意味着它不需要任何服务器端进程来工作。它比 MySQL、PostgreSQL 和其他需要第三方软件才能使用的软件更容易设置。开始使用 SQL.js 就像在现有的 HTML 项目中安装 jQuery 一样简单。SQL.js 支持执行包含多个语句的单个 SQL 字符串，如下所示:

```
sqlstr = "CREATE TABLE tableName(colA, colB);";
sqlstr += "INSERT INTO hello VALUES (0, 'hello');"
    ....

```

但是还有一个重要的缺点:使用 SQL.js 时对数据库的更改不是持久的。再读一遍:当你重新加载你的浏览器时，对你的数据库所做的所有改变都将不复存在。这是因为 SQL.js 使用存储在浏览器内存中的虚拟数据库文件。但是，您可以导入任何现有的 SQLite 文件，并将创建的数据库导出为 JavaScript 类型的数组。

## 安装 SQL.js

### 浏览器安装

将 SQL.js 集成到一个新的基于客户端的项目中非常容易。您可以从包含 CDN 或下载源文件并将它们链接到您的标记页面开始。

默认情况下，SQL.js 使用 WebAssembly，除了 JavaScript 库之外，还需要加载一个`.wasm`文件。您可以[在线下载该文件](https://sql.js.org/dist/sql-wasm.wasm)，并使用`locateFile`功能将该文件加载到您的网页中，如下所示:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Page Title</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/sql.js/1.5.0/sql-wasm.js"></script>
    <script>
      // Load sql.js WebAssembly file
      let config = {
        locateFile: () => "/path/to/downloaded/sql-wasm.wasm",
      };
      initSqlJs(config).then(function (SQL) {
          console.log("sql.js initialized 🎉");
      });
    </script>
  </head>
  <body></body>
</html>

```

在上面的代码块中，我们使用`initSqlJs`异步加载 Wasm 二进制文件，并在加载所需文件后初始化 SQL.js。

### Node.js 安装

在基于节点的项目中安装 SQL.js 也非常简单。要安装它，您只需运行:

```
npm install sql.js

```

或者，您可以从前面的链接中下载`sql-wasm.js`和`sql-wasm.wasm`，并使用 Node.js `require`函数将它们加载到您的项目中。

此外，在基于节点的项目中，您可以跳过`locateFile`方法来加载`sql-wasm.wasm`文件，因为如果它与您正在处理的当前文件在同一个文件夹中，它会自动加载。因此，我们的代码将如下所示:

```
var initSqlJs = require("./sql-wasm.js");

initSqlJs().then(function (SQL) {
  console.log("sql.js initialized 🎉");
});

```

## 编写 SQL 查询和准备好的语句

既然我们已经完成了安装和初始化 SQL.js 所需的所有步骤，让我们深入了解它的用法。

### 创建数据库

下面的代码创建了一个新的数据库:

```
const db = new SQL.Database();

```

值得注意的是，`SQL.Database()`方法接受一个可选参数`data`，它必须是一个代表 SQLite 数据库文件的`Uint8Array`。例如，在 Node.js 中，我们可以加载一个现有的`.sqlite`文件，如下所示:

```
let fs = require("fs");
let initSqlJs = require("./sql-wasm.js");
let filebuffer = fs.readFileSync("/path/to/sample.sqlite");

initSqlJs().then(function (SQL) {
  // Create a new database with our existing sample.sqlite file
  const db = new SQL.Database(filebuffer);
});

```

在上面的代码中，我们使用内置的 Node.js `fs`和`path`模块来读取我们现有的`sample.sqlite`文件。

### 运行 SQL 语句

SQL 语句可以是在数据库中创建或检索一条信息的请求，也可以是对现有数据执行操作的请求。

使用 SQL.js，您可以轻松运行一条语句，而无需读取其结果。语法如下所示:

```
db.run(stmt);

```

参数`stmt`当然是您的 SQL 语句。下面是一个如何在数据库中创建一个名为`users`的新表的例子，该表包含 ID、姓名、电话号码和地址列。它还会在该表中插入一个新行。

```
let initSqlJs = require("./sql-wasm.js");

initSqlJs().then(function (SQL) {
  const db = new SQL.Database();
  // RUNNING SQL QUERIES 👇
  db.run("CREATE TABLE users (id, name, phone, address);");
  db.run(
    `INSERT INTO users (id, name, phone, address)
        VALUES (1, 'John Doe', '+234-907788', '12 Igodan Street, Okitipupa')`
  );
});

```

### 准备好的 SQL 语句

您可以使用预处理语句高效地重复执行相同或相似的 SQL 语句。预处理语句的解析时间比运行 SQL 语句要短得多，因为只需对查询做一次准备。它们对于 SQL 注入也非常有用，因为您不需要对参数值进行转义，参数值稍后会使用不同的协议进行传输。

借助 SQL.js，我们还可以使用`.prepare()`方法编写预准备语句:

```
var stmt = db.prepare(preparedStatement);

```

下面是一个示例，用于获取我们之前的数据库中 ID 在 1 到 10 之间的所有用户:

`var stmt = db.prepare("SELECT * FROM users WHERE id BETWEEN $start AND $end");`

```
  stmt.bind({ $start: 1, $end: 2 });
  while (stmt.step()) {
    var row = stmt.getAsObject();
    console.log("Here is a user row: " + JSON.stringify(row));
  }

```

在编写好准备好的 SQL 语句之后，我们使用`.bind()`方法来绑定语句所需的值(上面示例中的`start`和`end`)。接下来，我们将使用一个`while`循环返回所有可能的行，并将它们记录到控制台。

### 将数据库写入磁盘

SQL.js 还提供了一个通过`db.export()`方法将数据库作为`.sqlite`文件导出/写入磁盘的选项。结果将作为`Uint8Array`返回，您可以使用 Node.js `Buffer`类和文件系统包将数据库写入磁盘:

```
var fs = require("fs");
/***
  Code to create a database here
***/
// Export database 👇
var data = db.export();
var buffer = new Buffer(data);
fs.writeFileSync("new-db.sqlite", buffer);

```

运行上面的代码后，您应该会在项目根文件夹中看到一个名为`new-db.sqlite`的新文件。

## 结论

在本文中，我们介绍了 SQL.js、它提供的特性以及如何在实际应用程序中使用它。虽然 SQL.js 可能非常适合构建离线优先的应用程序，如笔记应用程序，但如果您希望从管理面板管理用户数据，您可能需要考虑使用其他关系数据库(MySQL、PostgreSQL)。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。

### 进一步阅读