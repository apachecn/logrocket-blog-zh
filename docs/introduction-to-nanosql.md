# nanoSQL 简介

> 原文：<https://blog.logrocket.com/introduction-to-nanosql/>

nanoSQL 是一个通用的 JavaScript 客户端库，用于连接几个不同的数据库，包括内存数据库和永久数据库。

随着 web 应用程序变得越来越复杂，您可能会发现自己不仅仅使用一个数据库来保存和检索数据。例如，您可能在一个应用程序中同时使用 MySQL 和 Redis。

当后端数据库保存和更新永久数据时，您可以使用 Redis 作为前端层的数据库来存储会话令牌和缓存，或者为管理仪表板或竞技游戏创建实时统计数据。MySQL 等传统数据库的读/写速度太慢，无法创造现代用户期望的“即时”体验。

要连接到您的数据库，您需要一个数据库客户端库。如果你使用 Node.js，你可以使用 [mysqljs](https://github.com/mysqljs/mysql) 连接到 MySQL，使用 [Redis](https://www.npmjs.com/package/redis) 连接到 Redis 数据库。这意味着您需要学习两套不同的数据库客户端库，并知道如何编写正确的查询来实现您想要做的事情。

nanoSQL 解决了这个问题。它使您能够连接和使用不同的数据库系统，而无需为您要使用的每个数据库安装专用的客户端库。它通过创建一种可以在所有类型的受支持数据库中使用的标准化查询语言来做到这一点。

通过使用 nanoSQL，您可以并行运行几个数据库，每个数据库使用自己的适配器。您可以创建一个连接到 MySQL 的 nanoSQL 实例和另一个连接到 Redis 的实例。

另一个好处是，只要使用 JavaScript，就可以在客户机、服务器或移动设备中使用 nanoSQL。

## 行动中的 nanoSQL

让我们看看 nanoSQL 如何帮助我们使用相同的查询语言和 API 来操作本地存储和 MySQL 数据库。

我们将使用 React 创建一个小型表单组件，将临时用户输入保存在本地存储中。这样，当重新加载应用程序时，我们可以获取之前输入的数据。当用户点击提交时，应用程序将通过用 Express 构建的 REST API 将数据保存在 MySQL 的一个表中。

在我们开始之前，确保您的机器上安装了 MySQL。最简单的方法之一是安装 [XAMPP](https://www.apachefriends.org/index.html) ，其中包括用于 MySQL 管理的 phpMyAdmin。

一旦 MySQL 开始运行，就要使用 Express 编写 REST API。这款应用的代码可以在 [GitHub](https://github.com/nsebhastian/intro-nanosql-react) 上找到。

## 后端的 nanoSQL

首先，使用`npm init`为您的应用程序创建一个 npm 包目录。您可以用自己的信息填写包裹详情。

接下来，在目录中安装必要的软件包。

```
npm install express @nano-sql/core @nano-sql/adapter-mysql

```

`express`包用于创建 REST API，而`@nano-sql/core`和`@nano-sql/adapter-mysql`用于连接和操作 MySQL 数据库。

安装完成后，创建一个名为`server.js`的新文件，并开始导入所需的模块。

```
const express = require('express');
const bodyParser = require("body-parser");
const { nSQL } = require("@nano-sql/core");
const { MySQL } = require("@nano-sql/adapter-mysql");

```

创建要在您的应用程序中使用的 Express server 实例。我们将使用`express.json()`函数，以便 Express 可以从传入的 POST 请求中识别 JSON 对象。

接下来，创建一个中间件来允许来自`localhost:3000`的 CORS 请求，稍后我们将把它用于 React 应用程序。我们还将设置 Express 监听本地主机端口 5000 上的请求。

```
const app = express();
app.use(express.json());
app.use(function (req, res, next) {
  res.setHeader('Access-Control-Allow-Origin', 'http://localhost:3000');
  res.setHeader('Access-Control-Allow-Methods', 'GET,POST,PUT,DELETE,OPTIONS');
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type, Access-Control-Allow-Headers');
  next();
});
const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
  console.log(`Listening on port ${PORT}`);
});

```

是时候使用 nanoSQL 的 createDatabase 函数将 Express 与 MySQL 连接起来了，该函数有三个参数:

1.  `id`参数是数据库的唯一标识符
2.  `mode`参数用于定义数据库；您可以指定内置模式或任何一种受支持的适配器
3.  `tables`参数接受定义数据库的对象数组。您可以通过使用一个`model`对象来定义表格及其列的`name`，如下所示

因为我们使用的是 MySQL，所以我们将使用之前从适配器导入的 MySQL 函数。我们需要在该函数中指定我们的 MySQL 配置。

```
nSQL().createDatabase({
  id: "mysql-db",
  mode: new MySQL({ 
    // specify your MySQL credentials here
    host: "localhost",
    database: "test",
    user: "root",
    password: ""
  }),
  tables: [
    {
      name: "tb_users",
      model: {
        "id:uuid": {pk: true},
        "name:string": {},
        "age:int": {},
        "role:string": {}
      }
    }
  ],
})

```

虽然在生产环境中不建议使用用户、主机、数据库、密码和端口等凭证，但为了简化教程，我们将把它们写在这个文件中。您可能需要在生产中使用环境变量，但这超出了本指南的范围。

现在，nanoSQL 已经连接到我们的 MySQL，我们可以创建快速路由来对数据库进行特定的操作。我们将创建三个操作数据库的简单路径:

1.  `/get`用于从表中检索所有数据
2.  `/save`用于将新条目保存到表格中
3.  `/delete/:id`用于删除具有指定 ID 的条目

首先，创建`/get`路由，从`tb_users`表中选择所有行。nanoSQL 中的查询语法如下所示:

```
nSQL("table name")
.query("query type", ...query args)
...optional filtering, etc..
.exec()

```

您不用在字符串中编写整个查询，而是使用函数并将它们链接在一起。这看起来非常类似于查询构建器，比如 Rails 的活动记录。

首先创建`/get`路线并查询表中的所有行。

```
app.get('/get', (req, res) => {
  nSQL("tb_users").query("select").exec()
  .then( rows => {
    res.json(rows)
  })
});

```

这里是`/save`和`/delete/:id`路线的路线。我们不像在大多数 SQL 查询中那样使用`insert`，而是使用`[upsert](https://nanosql.io/query/upsert.html#upsert)`，它可以执行插入或更新，这取决于我们在调用中定义的查询参数的数量。

```
app.post('/save', (req, res) => {
  const { name, age, role } = req.body
  nSQL("tb_users").query("upsert", {name, age, role}).exec()
  .then(
    res.json({message: 'Data is saved!'})
  )
});

app.delete('/delete/:id', (req, res) => {
  const { id } = req.params
  nSQL("tb_users").query("delete").where(['id', '=', id]).exec()
  .then(
    res.json({message: 'User is deleted!'})
  )
});

```

最后，删除路径将接受一个`id`参数，并在找到具有匹配 ID 的行时执行删除操作。

将你写的代码与 GitHub 上的源代码进行比较。

## 在前端创建一个反应表单

REST API 完成后，我们可以开始编写一个使用这个 API 的 React 应用程序。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

打开项目目录中的终端，使用`create-react-app`引导 React 应用程序。我们将把这个应用程序命名为“客户端”

```
npx create-react-app client

```

安装 React 应用程序后，进入目录并安装`axios`和`@nano-sql/core`。

```
npm install axios @nano-sql/core

```

是时候写我们应用程序的前端了。让我们从编写新的`index.js`组件开始。

在该文件中呈现您的`App`组件:

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
ReactDOM.render(<App />, document.getElementById('root'));

```

接下来，创建一个名为`App.js`的文件，导入所需的模块，并写入您的状态。

我们将创建一个具有四个状态属性的简单组件:

```
import React from 'react';
import Axios from 'axios';
import {nSQL} from '@nano-sql/core';

const API_URL = 'http://localhost:5000/' // for connecting to API later

class App extends React.Component {
  state = {
    name: '',
    age: '',
    role: '',
    users: []
  }

```

`name`、`age`和`role`属性将用于存储表单输入值，而`users`将存储稍后从 Express 检索的数据。

就在状态下面，写一个函数来检索用户数据，消费我们之前创建的`/get`路由。

```
getMerchant = () => {
  Axios.get(`${API_URL}get`)
  .then(res => {
    this.setState({users: res.data});
  });
}

```

从`componentDidMount`方法调用该函数，以便组件一呈现，用户数据就被填充。

```
componentDidMount() {
  this.getMerchant();
}

```

接下来，记下`render`方法。简单地使用状态属性来存储输入值(除了`users`)。我们只需要使用`map`函数来呈现`users`值。

就在表单下方，添加一个从 MySQL 数据库中删除数据的按钮。

```
render() {
  const {name, age, role, users} = this.state
  return(
    <div className='app'>
      <h1>Auto-save form with NanoSQL for local storage and MySQL</h1>
      <h2>Current data: </h2>
      <ul>
      {
        users.map( user => {
          return (
           <li key={user.id}> {user.id} / {user.name} / {user.age} / {user.role} </li>
          )
        })
      }
      </ul>
      <form onSubmit={this.handleSubmit}>
      <div>
        <label>Name: </label>
        <input
          name="name"
          type="text"
          placeholder="Enter name"
          value={name}
          onChange={event => this.setState({name: event.target.value})}
          />
      </div>
      <div>
        <label>Age: </label>
        <input
          name="age"
          type="number"
          placeholder="Enter age"
          value={age}
          onChange={event => this.setState({age: event.target.value})}
          />
      </div>
      <div>
        <label>Role: </label>
        <input
          name="age"
          type="text"
          placeholder="Enter role"
          value={role}
          onChange={event => this.setState({role: event.target.value})}
          />
      </div>
      <button type='submit'>Submit</button>
      </form>
      <button onClick={this.handleDelete}>Delete</button>
    </div>
  )
}

```

最后要处理的是表单提交和用户删除。

```
handleSubmit = (e) => {
  e.preventDefault()
  const {name, age, role} = this.state
  Axios.post(`${API_URL}/save`, {
    name,
    age,
    role
  })
  .then(response => {
    alert(response.data.message)
    this.setState({
      name: '',
      age: '',
      role: ''
    })
    this.getMerchant()
  })
}

handleDelete = () => {
  let id = prompt('Enter user id');
  if (id){
    Axios.delete(`${API_URL}delete/${id}`)
    .then(response => {
      alert(response.data.message)
      this.getMerchant()
    })
  }
}

```

现在你的申请完成了。运行`npm start`并填写表格，尝试一下。您应该能够毫无问题地添加和删除用户数据。

## 将临时用户输入保存在本地存储中

由于我们使用的是 nanoSQL，所以让我们试着在前端使用它。我们将在浏览器的本地存储数据库中保存表单的状态属性— `name`、`age`和`role`。当我们关闭应用程序并再次打开它时，我们的应用程序将从本地存储中检索状态值。

回到我们之前写的`componentDidMount`方法，添加下面的代码。

```
componentDidMount() {
  this.getMerchant()

  nSQL().createDatabase({
    id: "ls-db",
    mode: "LS",
    tables: [
      {
        name: "tb_temp",
        model: {
            "id:uuid": {pk: true},
            "data:string": {},
            "form:string": {}
        }
      }
    ]
  }).then(() => {
    nSQL("tb_temp").query("select").where(['form', '=', 'user']).exec().then((row) => {
      console.log(row)
      if(row.length){
        console.log('update state')
        const data = JSON.parse(row[0].data)
        this.setState({
          name: data.name,
          age: data.age,
          role: data.role
        })
      } else {
        const {name, age, role} = this.state
        nSQL("tb_temp")
        .query("upsert",{data: JSON.stringify({name, age, role}), form: 'user'})
        .exec()      
      }
    })
  })
}

```

如果上面的代码看起来很熟悉，那是因为你以前写过它。我们正在执行与 MySQL 相同的`createDatabase`功能。该表有三列:

1.  `id`作为表的主键
2.  `data`存储格式为 JSON 字符串的表单值
3.  `form`作为数据的唯一标识符，这在应用程序中有多个表单时非常有用

连接到数据库后，查询表中具有指定的`form`值的任何行。当找到一行时，将它放入状态。如果没有数据，只需插入一个带有`user` `form`的新行，但是带有`data`列的初始状态值。您需要此行，以便以后用户在表单中键入内容时可以更新它。

最后一步是在状态改变时更新本地存储行。您可以使用`componentDidUpdate`方法来实现。

```
componentDidUpdate() {
  const {name, age, role} = this.state
  nSQL("tb_temp")
  .query("upsert",{data: JSON.stringify({name, age, role})})
  .where(['form', '=', 'user'])
  .exec().then(
    console.log('Data updated')
  )
}

```

当组件由于状态改变而更新时，我们将检索状态属性并用它更新`data`列。这就是我们需要做的！

现在打开 React 应用程序，在表单中输入一些内容。关闭应用程序，然后再次打开。您将看到您在表单中输入的值仍然存在，等待您提交。

## 结论

随着您的 web 应用程序变得越来越复杂，总有一天您需要利用不止一个数据库系统。为了给用户创造无缝体验，您可能需要同时使用前端和后端存储系统，这意味着您需要学习如何高效地操作它们。

nanoSQL 通过启用标准化的查询语言来帮助您减少开发时间，以便您可以在不同的数据库系统上使用相同的 API。

即使它的名字中有“SQL ”, nanoSQL 也支持 noSQL 数据库，如 MongoDB 和 ScyllaDB。这意味着当您将这些数据库添加到您的架构中时，您不需要学习新的查询语言。在不同的数据库之间跳转是一件小事。

您甚至可以将自己的查询功能添加到 nanoSQL 中。如果你正在用多数据库架构开发软件，一定要查看一下 [nanoSQL 文档](https://nanosql.io/setup.html)，看看它是否适合你。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.