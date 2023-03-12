# 使用 TinyBase 进行反应式状态管理

> 原文：<https://blog.logrocket.com/using-tinybase-reactive-state-management/>

## TinyBase 简介

TinyBase 是一个新的状态管理库，它提供了一种更结构化的、类似 SQL 的数据操作方法。这个新的库为将状态管理合并到应用程序中提供了一个简单的语法，并且它的 npm 库不使用依赖项。

在本教程中，我们将带您了解 TinyBase 的基础知识。请注意，TinyBase 是一个新项目，还没有经受住关键的生产环境，因此本演练是实验性的。

然而，首先让我们看看 TinyBase 与其他状态管理库的不同之处。

### TinyBase 的特性

在此之前，我们有各种各样的[状态管理库](https://blog.logrocket.com/how-to-choose-the-right-react-state-management-solution/)，每一个都有自己的一套功能。除此之外，我们还有其他库，它们扩展了以前的库的特性。以下是 TinyBase 相对于竞争对手的一些优势:

*   熟悉的类似 SQL 的概念——如果您以前有使用 SQL 的经验，那么开始使用 TinyBase 将是轻而易举的事情，因为它有熟悉的 SQL 语法和自己的表、行和单元格的概念
*   持久数据——tiny base 还提供了一个集成模块，用于将数据持久存储到各种浏览器存储介质中，如`localStorage`、`sessionStorage`，甚至能够指定一个定制的存储介质
*   事件监听器——您还可以注册监听器来用 TinyBase 存储数据，以便在对这些数据进行任何更改时，调用您想要的函数。因此，TinyBase 是反应性的！
*   数据模式—您可以使用模式来确保存储中的值条目总是与您预期的相匹配。对于每个定义的模式，您可以另外设置一个默认类型

## 装置

您可以轻松下载 TinyBase 核心文件，或者将其 CDN 链接复制并粘贴到您的应用程序中:

```
<html>
  <head>
    <title>TinyBase App</title>
    <script src="https://unpkg.com/tinybase/lib/umd/tinybase.js"></script>
  </head>
  <body>
    ...
  </body>
</html>
```

或者通过 npm 安装:

```
npm install tinybase
```

如果您通过 CDN 使用 TinyBase，我们将需要一个额外的事件监听器来检查`TinyBase`对象何时可用。这需要我们更新之前的示例，如下所示:

```
<html>
  <head>
    <title>TinyBase App</title>
    <script src="https://unpkg.com/tinybase/lib/umd/tinybase.js"></script>
    <script>
      addEventListener('load', () => {
        const { createStore } = TinyBase;
      });
    </script>
  </head>
  <body>
      ...
  </body>
</html>
```

并且，假设您安装了带有 npm 的包，我们可以使用 ES6 import 在我们的应用程序中包含它的主要功能:

```
import { createStore } from "tinybase";
```

## 核心概念

### 商店

store 是 TinyBase 存储表格数据的主要位置。使用`createStore()`函数，我们可以很容易地创建一个如下所示的:

```
import { createStore } from "tinybase";
const store = createStore()
```

从这里，我们可以定义我们的表，添加数据更改侦听器，将存储数据持久化到浏览器存储中，等等。

每个 TinyBase 存储都有一个简单的层次结构:

*   每个商店都有规定数量的表
*   每个表格都有固定的行数
*   每行都有一定数量的单元格

### 桌子

表格用于以行和单元格的形式组织数据，并用于以结构格式存储和显示记录。

除了表侦听器之外，TinyBase 还允许我们在表上执行三个基本操作(设置、获取和删除)。

#### 创建表格

TinyBase 有两种创建表的方法:创建单个表的`setTable()`方法和创建一个或多个表的`setTables()`方法:

```
const store = createStore();

// Create single table
store.setTable("users", {
  1: {
    name: "John Doe",
    age: 12,
    isVerified: true,
  },
});

// Create multiple tables

store.setTables({
  users: {
    1: {
      name: "John Doe",
      age: 12,
      isVerified: true,
    },
  },
  posts: {
    1: {
      title: "Hello World",
      body: "Hello World",
    },
  },
// ...
});
```

上面的例子显示了如何利用`setTable()`和`setTables()`方法。第一个例子是创建一个名为`users`的表，其中有一行包含虚拟用户信息。在第二个例子中，创建了两个表:`users`和`posts`。

#### 获取表格

`getTable()`和`getTables()`方法用于访问特定的表信息或商店中所有可用的表。第一种需要指定我们希望检索其数据的表的名称:

```
// This function returns all tables.
console.log(store.getTables());

// Returns only the table 'users'.
console.log(store.getTable("users"));
```

#### 删除表格

TinyBase 还有两种删除表的方法，一种是删除特定的表，另一种是删除存储中的所有表:

```
// Delete table 'posts'
store.delTable("posts");

// Delete all tables in the store.
store.delTables();
```

### 行

这个上下文中的行表示表中的单个隐式组织的数据项，就像在数据库设置中一样。对于行，我们可以执行以下操作:

*   `setRow()` —该方法接受三个参数:表标识符、新行名和包含新行数据的对象。此外，如果存储中不存在该表，该方法将创建一个新表:

    ```
    store.setRow("users", 2, {   name: "Jane Doe",   age: 15,   isVerified: true, });
    ```

*   `getRow()` —使用它从一行中获取包含所有数据的对象。这个方法有两个参数，一个是应该从中检索行的表名，另一个是行标识符:

    ```
    console.log(store.getRow("users", "1")); // returns - { name: 'John Doe', age: 12, isVerified: true }
    ```

### 细胞

TinyBase 在设置和检索单元格方面具有类似的功能:

*   `setCell()` —如果指定的单元格标识符已经存在，此函数将对其进行更新，否则将在该行中添加新的单元格:

    ```
    // Will update table 'users' row '1' name with James Doe store.setCell("users", "1", "name", "James Doe");  // Will append table 'users' row '1' with a new cell 'address' store.setCell("users", "1", "address", "North Pole");
    ```

*   `getCell()` —给定表格和行标识符，此函数返回包含单个单元格的值的对象:

    ```
    store.getCell("users", "1", "address"); // returns - North Pole
    ```

### 听众

TinyBase 还提供了向表、行和单元格添加侦听器的能力。这些侦听器用于检测数据更改，并采取适当的响应措施。

例如，要监听表中的数据变化，给定表 id，我们有:

```
const listenerId = store.addTableListener("users", () => {
  console.log("users table changed");
});
```

因此，如果这个表中的数据发生变化，我们将在控制台中得到消息“users table updated”。

我们还可以向表中的特定行添加侦听器:

```
const listenerId = store.addRowListener("users", "1", () => {
  console.log("Row '1' in table 'users' had changed");
});
```

库中有超过 10 个监听器方法，在这篇文章中一个一个地介绍它们会很乏味。作为参考，[所有的方法都可以在这里找到](https://tinybase.org/api/store/interfaces/store/store/methods/listener/)。

### 持久化数据

TinyBase 还提供了一个 persister 模块，允许你在不同的位置保存和加载存储数据，比如浏览器的`localStorage`和`sessionStorage`，这样这些数据就可以在浏览器会话或重新加载之间保存。

为了将数据保存到浏览器的存储器中，我们有`createLocalPersister`和`createSessionPersister`方法:

```
const { createStore, createLocalPersister } = TinyBase;
const store = createStore();

store.setTables({
  users: {
    1: {
      name: "John Doe",
      age: 12,
      isVerified: true,
    },
   // ...
  },
});

const persister = createLocalPersister(store, "users");
persister.save();
```

我们将能够通过`localStorage`访问甚至保存新数据到我们的商店:

```
// Get store data from localStorage
console.log(localStorage.users)
// returns - {"users":{"1":{"name":"John Doe","age":12,...
// Add new data to store data directly from localStorage
localStorage.setItem("users", '{"users":{"3":{"name":"Random User"}}}');
```

## 结论

TinyBase 仍处于早期阶段，但它已经拥有了许多令人惊叹的功能。有一定的缺点。

例如，一个或多个函数可以任意执行相同操作的方式，就像您可以使用`setCell()`方法创建一个表或行的方式，但是，这是可以理解的。

此外，图书馆还有很多扩展的机会。如果你对 TinyBase 还能做什么感到好奇，可以看看它的[演示应用](https://tinybase.org/demos/)。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)