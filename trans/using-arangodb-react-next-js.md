# 将 ArangoDB 与 React 和 Next.js 一起使用- LogRocket Blog

> 原文：<https://blog.logrocket.com/using-arangodb-react-next-js/>

现在，开发人员可以处理现代应用程序中使用的复杂数据类型，而不必集成多个数据库；多模型数据库将不同数据库范例(如图形、文档和关系数据库)的优点结合到一个程序中。

在本教程中，我们将探索 [ArangoDB](https://www.arangodb.com) ，这是一个多模型的开源数据库，为文档、图形和键值提供了灵活的数据模型。我们将使用 React 和 Next.js 构建简单的数据库应用程序。

像它的竞争者 MongoDB、Fauna、DGraph 和 Neo4j 一样，ArangoDB 使用了一个[云托管的数据库服务 Oasis](https://cloud.arangodb.com/home) ，这使得构建和扩展应用程序变得很容易。

## ArangoDB 入门

首先，在[cloud.arangodb.com](https://cloud.arangodb.com/home)上注册一个新的绿洲账户。在菜单上选择**项目**，新建一个 ArangoDB 项目；为了留在 ArangoDB 的免费层内，一定要坚持使用[O](https://www.arangodb.com/enterprise-server/oneshard/)[ne](https://www.arangodb.com/enterprise-server/oneshard/)[S](https://www.arangodb.com/enterprise-server/oneshard/)[硬基础服务](https://www.arangodb.com/enterprise-server/oneshard/)。

接下来，为您的项目选择一个名称，并选择您的云提供商和地区。当您的数据库被引导时，您需要等待几分钟。

要导航到您的 web 仪表板，请单击数据库 URL 旁边的图标。使用`room`作为用户名和您的仪表板密码登录。在**用户**下，创建一个新用户。最后，转到**数据库**并创建一个测试数据库。

### 熟悉阿兰戈查询语言

为了熟悉阿兰戈查询语言(AQL)，让我们创建一个名为`cheese`的新示例集合。转到**查询**部分，运行下面的代码将数据插入到`cheese`集合中:

```
INSERT {name: "American"}
IN cheese

```

继续向`cheese`系列添加一些物品。要从`cheese`集合中获取项目，请运行以下查询:

```
FOR c IN cheese
    RETURN c

```

ArangoDB 语法非常简单明了，您可以在[查询文档](https://www.arangodb.com/docs/stable/aql/data-queries.html)中看到其他查询示例。

## ArangoDB Node.js 驱动程序

在将 ArangoDB 添加到 React 和 Next.js 项目之前，让我们回顾一下 Node.js 中的驱动程序是如何工作的。

创建一个名为`arangotest`的新文件夹。在内部，运行以下命令创建一个新项目:

```
npm init -y

```

按照如下方式安装 [ArangoDB JavaScript 驱动程序](https://www.arangodb.com/docs/stable/drivers/js.html):

```
npm install arangojs

```

现在，创建一个`index.js`文件。通过添加以下代码建立数据库连接，但是，请确保它反映了您的唯一数据库连接字符串:

```
const { Database, aql } = require("arangojs");

// establish database connection
const db = new Database({
  url: "https://XXXXXXXXXX.arangodb.cloud:8529/",
  databaseName: "XXXXXX",
  auth: { username: "XXXXXXX", password: "XXXXXX" },
});

```

现在，我们将定义一个函数，该函数要么在数据库中创建一个新集合，要么在集合已经存在的情况下防止错误发生:

下面的功能:

*   接收集合名称作为参数
*   获取数据库中当前所有集合的列表
*   确定所需的集合是否已经存在
*   如果集合不存在，则创建集合
*   返回集合

```
// function to get collection or create it if it doesn't exist
    const getCollection = async (cName) => {
      // get list of collections in database
      const collections = await db.collections();
// check if collection exists, if so return collection, if not, create it if (collections.includes(cName)) { return await db.collection(cName); } else { return db.createCollection(cName); } };
```

让我们尝试添加一个新的项目，`cat`到我们的集合。下面的函数运行`getCollection`，确保集合存在。它还运行一个通过插值传递`name`参数的查询。AQL 标签处理并保护函数免受注入攻击:

```
// create a new cat
const createCat = async (name) => {
    // make sure cat collection exists
    await getCollection("cats")
    // query to insert a cat
    await db.query(aql`INSERT {name: ${name}} IN cats`)
}
```

现在，让我们运行一个查询结果的函数。AQL 查询返回异步`ArrayCursor`对象。要在数组中获得结果，我们必须循环查询光标，并将结果放入数组中。

下面的函数检查集合是否存在，创建一个数组来存储结果，查询`cats`，遍历`arrayCursor`，将每个`cat`项推入数组，并返回`cats`的数组:

```
const getCats = async () => {
    // make sure cat collection exists
    await getCollection("cats")
    // declare array to hold cats
    let result = []
    // query for cats
    const results = await db.query(aql`FOR c IN cats RETURN c`)
    // loop through array cursor and push results in array
    for await (cat of results){
        result.push(cat)
    }
    // log results
    console.log(result)
    // return the list of cats
    return result
}

```

该项目的完整代码类似于以下代码块:

```
const { Database, aql } = require("arangojs");

// establish database connection
const db = new Database({
  url: "https://xxxxxxxxx.arangodb.cloud:8529/",
  databaseName: "xxxxxx",
  auth: { username: "xxxxxxx", password: "xxxxxxx" },
});

// function to get collection or create it if it doesn't exist
const getCollection = async (cName) => {
  // get list of collections in database
  const collections = await db.collections();

  // check if collection exists, if so return collection, if not, create it
  if (collections.find((c) => c._name === cName)) {
    return await db.collection(cName);
  } else {
    return db.createCollection(cName);
  }
};

// create a new cat
const createCat = async (name) => {
    // make sure cat collection exists
    await getCollection("cats")
    // query to insert a cat
    await db.query(aql`INSERT {name: ${name}} IN cats`)
}

// query cats

const getCats = async () => {
    // make sure cat collection exists
    await getCollection("cats")
    // declare array to hold cats
    let result = []
    // query for cats
    const results = await db.query(aql`FOR c IN cats RETURN c`)
    // loop through array cursor and push results in array
    for await (cat of results){
        result.push(cat)
    }
    // log results
    console.log(result)
    // return the list of cats
    return result
}

createCat("Miles")

getCats()

```

运行这段代码几次后，您会看到每个项目都有几个属性，我们可以将它们用作唯一标识符:

```
  {
    _key: '10012443',
    _id: 'cats/10012443',
    _rev: '_dEUBD1---_',
    name: 'Miles'
  }

```

## 在 React 应用程序中使用 ArangoDB

您希望避免在应用程序的前端公开数据库凭据。为了做到这一点，我们有几个选项仍然可以让你使用你最喜欢的前端框架。

首先，你可以用你最喜欢的 Node.js 框架创建一个 API，比如 Express、Koa.js、Fastify、Sails.js、Loopback、FoalTS 或 NestJS。或者，您可以使用无服务器功能，这很容易通过 [Vercel 或 Netlify](https://blog.logrocket.com/8-ways-to-deploy-a-react-app-for-free/#vercel) 实现。最后，您可以将任何同构框架对与您选择的前端一起使用:

*   reac/next . js(反应/下一个. js)
*   view/ Nuxt.js-检视/nuxt . js
*   苗条/苗条套装
*   角度/角度通用

现在，让我们使用 Next.js 创建一个 API 来从 React 应用程序中访问 ArangoDB。

## 设置您的 Next.js 应用程序

通过在终端中运行以下命令，生成一个新的 Next.js 应用程序:

```
npm init next-app

```

将项目命名为`arangoreact`。一旦项目生成完成，将`CD`放入文件夹并安装`arangojs`:

```
npm install arangojs

```

创建一个名为`utils`的文件夹；在里面，创建一个名为`db.js`的文件。让我们将我们的连接代码包装在它自己的函数中，以便我们可以根据需要进行连接:

```
const { Database, aql } = require("arangojs");

const getConnection = () => {
  // establish database connection
  return new Database({
    url: "https://5e5b9f99f7e1.arangodb.cloud:8529/",
    databaseName: "test",
    auth: { username: "testuser", password: "test" },
  });
};

```

现在，我们将复制我们的`getCollection`函数并修改它，以便它接收连接作为参数:

```
// function to get collection or create it if it doesn't exist
const getCollection = async (cName, db) => {
  // get list of collections in database
  const collections = await db.collections();

  // check if collection exists, if so return collection, if not, create it
  if (collections.find((c) => c._name === cName)) {
    return await db.collection(cName);
  } else {
    return db.createCollection(cName);
  }
};

```

接下来，我们将让我们的函数查询`cats`，然后导出它，以便我们可以在 API 路由中使用它。请注意，我们更新了函数以在其中建立连接:

```
// query cats

export const getCats = async () => {
  // make connection
  const db = getConnection()
  // make sure cat collection exists
  await getCollection("cats", db);
  // declare array to hold cats
  let result = [];
  // query for cats
  const results = await db.query(aql`FOR c IN cats RETURN c`);
  // loop through array cursor and push results in array
  for await (let cat of results) {
    result.push(cat);
  }
  // log results
  console.log(result);
  // return the list of cats
  return result;
};

```

## 在 Next.js 中创建我们的 API 路由

在`pages/api`中创建一个名为`cats.js`的文件:

```
import { getCats } from "../../utils/db";

export default async function (req, res){

    const cats = await getCats()
    console.log(cats)
    res.json(cats)

}

```

现在，您应该能够打开 Next.js 开发服务器`npm run dev`，然后通过从 Postman 或失眠症这样的平台向`localhost:3000/api/cats`发出`GET`请求来测试 API。

## 从 React 组件获取数据

此时，数据通过标准的 API 端点交付。我们将使用从 API 获取数据的标准实践。

首先，我们将使用`useState`钩子声明保存 API 数据的状态。接下来，我们将声明一个用`fetch`请求数据并更新状态的函数。我们将在`useEffect`钩子中调用这个函数，这样它在页面加载时只发生一次。

在`pages/index.js`中添加以下代码:

```
import {useState, useEffect} from "react"

export default function index (){

  // create the state
  const [cats, setCats] = useState([])

  // function to request cats and update state
  const fetchCats = async () => {
    const response = await fetch("/api/cats")
    const data = await response.json()
    setCats(data)
  }

  // call the function on page load with useEffect
  useEffect(() => fetchCats(), [])

  // return JSX using cats data

  return <div>
    {cats.map(cat => (
      <ul>
        {Object.keys(cat).map(key => (
          <li>{key}: {cat[key]}</li>
        ))}
      </ul>
    ))}
  </div>
}

```

现在，如果你访问`localhost:3000`，你会看到我们所有的`cats`都列在页面上。我们刚刚成功地将数据从 ArangoDB 数据库中提取到 React 应用程序中。

## 结论

多模型数据库是改进和组织复杂数据库的规则改变者。在本教程中，我们使用 React 和 Next.js 探索了 ArangoDB。首先，我们通过一个简单的示例熟悉了阿兰戈查询语言，然后我们学习了如何使用在线 ArangoDB 仪表板和 Node.js 驱动程序。

ArangoDB 是在 React 应用程序中处理大量复杂数据库的绝佳选择。使用起来相当简单，它通过结合图形、文档和关系数据库的功能，为映射数据提供了一个简单的解决方案。我希望你喜欢这个教程！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)