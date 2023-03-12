# Node.js ORMs:为什么不应该使用它们

> 原文：<https://blog.logrocket.com/node-js-orms-why-shouldnt-use/>

***编者按**:这篇文章于 2022 年 10 月 5 日更新，包括了关于在 Node.js 中使用 Sequelize、Knex 和其他 ORM 的信息，何时使用 ORM，以及 Node.js 的“最佳”ORM。这篇文章也被更新，删除了过时的信息，并进行了一般性编辑。*

简而言之，ORM 指的是对象关系映射。这意味着我们将关系 sequel 对象——外键和主键——映射到实际对象。

这样做的结果是 ORM 在我们的数据库中为 CRUD 操作创建了一个结构。因此，它还通过允许开发人员对对象进行操作而不是经历编写 SQL 查询的麻烦，在 SQL 语句上提供了一个抽象层。因此，ORM 提高了可读性、文档性和开发速度。

然而，在本教程中，我们将考虑在项目中避免使用 ORM 的三个原因。虽然讨论的概念适用于每种语言和平台，但是代码示例将使用 Node.js 风格的 JavaScript 编写，并且我们将考虑从 npm 存储库中获得的包。

我不打算贬低这篇文章中提到的任何模块。他们每个人都付出了很多努力。它们被世界各地的生产应用程序所使用，这些应用程序每天都愉快地响应大量的请求。我也使用 ORM 部署过应用程序，没有后悔过。

## Node.js 中的 ORM 是什么？

ORM 是对象和关系数据库系统之间的映射过程。不同的数据库系统以无数种方式访问数据，ORM 可以帮助您维护对象，即使它们访问的源和应用程序会随着时间的推移而改变。

ORM 通常用于简化数据库之间的数据迁移。

在我们解释为什么不应该在 Node.js 中使用 ORM 的原因之前，让我们先列出一些好处。如果使用得当，Node.js 中的 ORM 使您能够:

*   避免冗余代码
*   轻松地从一个数据库切换到另一个
*   查询多个表(ORM 将面向对象的查询方法转换为 SQL)
*   多关注业务逻辑，少写界面

## 通过 Node.js 使用 ORMs

ORM 是强大的工具。本文中我们将要研究的 ORM 能够与 SQL 后端进行通信，比如 SQLite、PostgreSQL、MySQL 和 MSSQL。本文中的例子将利用 PostgreSQL，这是一个非常强大的开源 SQL 服务器。

有一些 ORM 能够与 NoSQL 后端通信，比如由 MongoDB 支持的 Mongoose ORM，但是我们不会在这篇文章中考虑这些。

首先，运行以下命令在本地启动一个 PostgreSQL 实例。它的配置方式是，对默认 PostgreSQL 端口`localhost:5432`的请求将被转发到容器。它还会将文件写入主目录中的磁盘，以便后续实例化将保留我们已经创建的数据:

```
mkdir -p ~/data/pg-node-orms
docker run 
  --name pg-node-orms 
  -p 5432:5432 
  -e POSTGRES_PASSWORD=hunter12 
  -e POSTGRES_USER=orm-user 
  -e POSTGRES_DB=orm-db 
  -v ~/data/pg-node-orms:/var/lib/postgresql/data 
  -d 
  postgres

```

既然您已经运行了一个数据库，我们需要向数据库添加一些表和数据。这将允许我们对数据进行查询，并更好地理解各种抽象层。

运行下一个命令启动交互式 PostgreSQL 提示符:

```
docker run 
  -it --rm 
  --link pg-node-orms:postgres 
  postgres 
  psql 
  -h postgres 
  -U orm-user 
  orm-db

```

在提示符下，输入上一个代码块`hunter12`中的密码。现在您已经连接好了，将以下查询复制并粘贴到提示符中，然后按下`Enter`键:

```
CREATE TYPE item_type AS ENUM (
  'meat', 'veg', 'spice', 'dairy', 'oil'
);

CREATE TABLE item (
  id    SERIAL PRIMARY KEY,
  name  VARCHAR(64) NOT NULL,
  type  item_type
);

CREATE INDEX ON item (type);

INSERT INTO item VALUES
  (1, 'Chicken', 'meat'), (2, 'Garlic', 'veg'), (3, 'Ginger', 'veg'),
  (4, 'Garam Masala', 'spice'), (5, 'Turmeric', 'spice'),
  (6, 'Cumin', 'spice'), (7, 'Ground Chili', 'spice'),
  (8, 'Onion', 'veg'), (9, 'Coriander', 'spice'), (10, 'Tomato', 'veg'),
  (11, 'Cream', 'dairy'), (12, 'Paneer', 'dairy'), (13, 'Peas', 'veg'),
  (14, 'Ghee', 'oil'), (15, 'Cinnamon', 'spice');

CREATE TABLE dish (
  id     SERIAL PRIMARY KEY,
  name   VARCHAR(64) NOT NULL,
  veg    BOOLEAN NOT NULL
);

CREATE INDEX ON dish (veg);

INSERT INTO dish VALUES
  (1, 'Chicken Tikka Masala', false), (2, 'Matar Paneer', true);

CREATE TABLE ingredient (
  dish_id   INTEGER NOT NULL REFERENCES dish (id),
  item_id   INTEGER NOT NULL REFERENCES item (id),
  quantity  FLOAT DEFAULT 1,
  unit      VARCHAR(32) NOT NULL
);

INSERT INTO ingredient VALUES
  (1, 1, 1, 'whole breast'), (1, 2, 1.5, 'tbsp'), (1, 3, 1, 'tbsp'),
  (1, 4, 2, 'tsp'), (1, 5, 1, 'tsp'),
  (1, 6, 1, 'tsp'), (1, 7, 1, 'tsp'), (1, 8, 1, 'whole'),
  (1, 9, 1, 'tsp'), (1, 10, 2, 'whole'), (1, 11, 1.25, 'cup'),
  (2, 2, 3, 'cloves'), (2, 3, 0.5, 'inch piece'), (2, 13, 1, 'cup'),
  (2, 6, 0.5, 'tsp'), (2, 5, 0.25, 'tsp'), (2, 7, 0.5, 'tsp'),
  (2, 4, 0.5, 'tsp'), (2, 11, 1, 'tbsp'), (2, 14, 2, 'tbsp'),
  (2, 10, 3, 'whole'), (2, 8, 1, 'whole'), (2, 15, 0.5, 'inch stick');

```

现在您已经有了一个填充的数据库。您可以键入`quit`来断开与`psql`客户端的连接，并取回对终端的控制权。如果您想再次运行原始 SQL 命令，您可以再次运行相同的`docker run`命令。

最后，您还需要创建一个名为`connection.json`的文件，其中包含以下 JSON 结构。节点应用程序稍后将使用它来连接到数据库:

```
{
  "host": "localhost",
  "port": 5432,
  "database": "orm-db",
  "user": "orm-user",
  "password": "hunter12"
}

```

## ORMs 和 Node.js:抽象层

在深入研究太多代码之前，让我们澄清几个不同的抽象层。就像计算机科学中的一切一样，当我们添加抽象层时，也有权衡。每增加一层抽象，我们都试图用开发人员生产力的提高来换取性能的下降(尽管情况并非总是如此)。

### 低级:数据库驱动程序

这基本上是你能得到的最低级的东西——除了手动生成 TCP 包并将它们传递给数据库。

数据库驱动程序将处理与数据库的连接(有时是连接池)。在这一级，您将编写原始的 SQL 字符串，并将它们传递给数据库，并接收来自数据库的响应。

在 Node.js 生态系统中，有许多库在这一层运行。这里有三个受欢迎的库:

*   mysql : MySQL (13k 星/ 330k 周下载量)
*   pg : PostgreSQL (6k 星/ 52 万周下载量)
*   sqlite3 : SQLite (3k 星/ 12 万周下载量)

这些库的工作方式基本相同:获取数据库凭证，实例化一个新的数据库实例，连接到数据库，以字符串的形式发送查询，并异步处理结果。

下面是一个简单的例子，它使用`pg`模块来获取烹饪鸡 tikka masala 所需的配料列表:

```
#!/usr/bin/env node

// $ npm install pg

const { Client } = require('pg');
const connection = require('./connection.json');
const client = new Client(connection);

client.connect();

const query = `SELECT
  ingredient.*, item.name AS item_name, item.type AS item_type
FROM
  ingredient
LEFT JOIN
  item ON item.id = ingredient.item_id
WHERE
  ingredient.dish_id = $1`;

client
  .query(query, [1])
  .then(res => {
    console.log('Ingredients:');
    for (let row of res.rows) {
      console.log(`${row.item_name}: ${row.quantity} ${row.unit}`);
    }

    client.end();
});

```

### 中级:查询生成器

这是使用更简单的数据库驱动模块和成熟的 ORM 之间的中间层次。在这一层运行的最著名的模块是 Knex。

这个模块能够为一些不同的 SQL 方言生成查询。这个模块依赖于前面提到的一个库——您需要安装计划与 Knex 一起使用的特定库。

*   [knex](https://github.com/tgriesser/knex) :查询生成器(8k 星/ 170k 周下载量)

在创建 Knex 实例时，您需要提供连接细节，以及计划使用的方言，然后就可以开始进行查询了。您编写的查询将非常类似于底层的 SQL 查询。

一个好处是，您能够以编程方式生成动态查询，这比您将字符串连接在一起形成 SQL(这通常会引入安全漏洞)要方便得多。

下面是一个简单的例子，它使用`knex`模块来获取烹饪鸡 tikka masala 所需的配料列表:

```
#!/usr/bin/env node

// $ npm install pg knex

const knex = require('knex');
const connection = require('./connection.json');
const client = knex({
  client: 'pg',
  connection
});

client
  .select([
    '*',
    client.ref('item.name').as('item_name'),
    client.ref('item.type').as('item_type'),
  ])
  .from('ingredient')
  .leftJoin('item', 'item.id', 'ingredient.item_id')
  .where('dish_id', '=', 1)
  .debug()
  .then(rows => {
    console.log('Ingredients:');
    for (let row of rows) {
      console.log(`${row.item_name}: ${row.quantity} ${row.unit}`);
    }

    client.destroy();
});

```

### 高级别:ORM

这是我们要考虑的最高层次的抽象。当使用 ORM 时，我们通常需要提前做更多的配置。顾名思义，ORM 的目的是将关系数据库中的记录映射到应用程序中的对象(通常，但不总是，类实例)。

这意味着我们在应用程序代码中定义了这些对象的结构以及它们之间的关系。

*   [序列](https://www.npmjs.com/package/sequelize) : (16k 星/ 270k 周下载量)
*   [书架](https://github.com/bookshelf/bookshelf):基于 Knex 星/ 23k 周下载量)
*   [水线](https://github.com/balderdashy/waterline) : (5k 星/ 20k 周下载)
*   [异议](https://github.com/Vincit/objection.js):基于 Knex 星/ 20k 周下载量)

## 对 Node.js 使用 Sequelize

在这个例子中，[我们将看看最流行的 ORM:Sequelize](https://blog.logrocket.com/using-sequelize-with-typescript/)。我们还将使用 Sequelize 对原始 PostgreSQL 模式中表示的关系进行建模。

下面是使用 [Sequelize](https://sequelize.org/v3/) 模块获取烹饪鸡肉 tikka masala 所需的配料列表的相同示例:

```
#!/usr/bin/env node

// $ npm install sequelize pg

const Sequelize = require('sequelize');
const connection = require('./connection.json');
const DISABLE_SEQUELIZE_DEFAULTS = {
  timestamps: false,
  freezeTableName: true,
};

const { DataTypes } = Sequelize;
const sequelize = new Sequelize({
  database: connection.database,
  username: connection.user,
  host: connection.host,
  port: connection.port,
  password: connection.password,
  dialect: 'postgres',
  operatorsAliases: false
});

const Dish = sequelize.define('dish', {
  id: { type: DataTypes.INTEGER, primaryKey: true, autoIncrement: true },
  name: { type: DataTypes.STRING },
  veg: { type: DataTypes.BOOLEAN }
}, DISABLE_SEQUELIZE_DEFAULTS);

const Item = sequelize.define('item', {
  id: { type: DataTypes.INTEGER, primaryKey: true, autoIncrement: true },
  name: { type: DataTypes.STRING },
  type: { type: DataTypes.STRING }
}, DISABLE_SEQUELIZE_DEFAULTS);

const Ingredient = sequelize.define('ingredient', {
  dish_id: { type: DataTypes.INTEGER, primaryKey: true },
  item_id: { type: DataTypes.INTEGER, primaryKey: true },
  quantity: { type: DataTypes.FLOAT },
  unit: { type: DataTypes.STRING }
}, DISABLE_SEQUELIZE_DEFAULTS);

Item.belongsToMany(Dish, {
  through: Ingredient, foreignKey: 'item_id'
});

Dish.belongsToMany(Item, {
  through: Ingredient, foreignKey: 'dish_id'
});

Dish.findOne({where: {id: 1}, include: [{model: Item}]}).then(rows => {
  console.log('Ingredients:');
  for (let row of rows.items) {
    console.log(
      `${row.dataValues.name}: ${row.ingredient.dataValues.quantity} ` +
      row.ingredient.dataValues.unit
    );
  }

  sequelize.close();
});

```

## Knex 与 Sequelize 与其他 ORM

在前面的章节中，我们已经尝试对一些 ORM 进行分类，但是在这一节中，我们将对这些 ORM 进行比较。

让我们从序列开始。红杉是一种成熟的 ORM。它功能丰富，迫使你将 SQL 隐藏在对象表示的后面。

另一方面，Knex 更低级，因为它是一个普通的查询构建器。Knex 很棒，因为它允许您轻松地找到并查看数据库中正在发生的事情，而不需要 ORM 的抽象。然而，随着应用程序的增长和变得越来越复杂，我们发现使用 Knex 处理复杂的关系会变得乏味和耗时。

Objection.js 坐在中间。它结合了不同 ORM 的优点，而没有损害编写原始 SQL 查询的能力。

## ORM 真的有必要吗？

既然您已经看到了如何使用不同的抽象层执行类似查询的例子，那么让我们深入了解为什么您应该对使用 orm 保持警惕的三个原因。

### 1.你学错了

很多人选择 ORM 是因为他们不想花时间去学习底层的 SQL。人们认为 SQL 很难学，通过学习 ORM，我们可以简单地用一种语言而不是两种语言编写我们的应用程序。

乍一看，这似乎站得住脚。ORM 将和应用程序的其他部分用相同的语言编写，而 SQL 是一种完全不同的语法。

然而，这种思路有一个问题。问题是 ORM 代表了你能得到的一些最复杂的库。ORM 的表面积非常大，从里到外学习它并不容易。

一旦你学会了一种特定的 ORM，这种知识可能不会很好地转移。如果您从一个平台切换到另一个平台，比如从 JS/Node.js 切换到 C#/.NET，这是正确的。但是，如果您在同一平台内从一个 orm 切换到另一个 ORM，比如从 Sequelize 到 Bookshelf with Node.js，这可能就不那么明显了。

考虑下面的 ORM 例子，每个例子都生成一个所有素食食谱项目的列表。

带序列:

```
#!/usr/bin/env node

// $ npm install sequelize pg

const Sequelize = require('sequelize');
const { Op, DataTypes } = Sequelize;
const connection = require('./connection.json');
const DISABLE_SEQUELIZE_DEFAULTS = {
  timestamps: false,
  freezeTableName: true,
};

const sequelize = new Sequelize({
  database: connection.database,
  username: connection.user,
  host: connection.host,
  port: connection.port,
  password: connection.password,
  dialect: 'postgres',
  operatorsAliases: false
});

const Item = sequelize.define('item', {
  id: { type: DataTypes.INTEGER, primaryKey: true, autoIncrement: true },
  name: { type: DataTypes.STRING },
  type: { type: DataTypes.STRING }
}, DISABLE_SEQUELIZE_DEFAULTS);

// SELECT "id", "name", "type" FROM "item" AS "item"
//     WHERE "item"."type" = 'veg';
Item
  .findAll({where: {type: 'veg'}})
  .then(rows => {
    console.log('Veggies:');
    for (let row of rows) {
      console.log(`${row.dataValues.id}t${row.dataValues.name}`);
    }
    sequelize.close();
  });

```

带书架:

```
#!/usr/bin/env node

// $ npm install bookshelf knex pg

const connection = require('./connection.json');
const knex = require('knex')({
  client: 'pg',
  connection,
  // debug: true
});
const bookshelf = require('bookshelf')(knex);

const Item = bookshelf.Model.extend({
  tableName: 'item'
});

// select "item".* from "item" where "type" = ?
Item
  .where('type', 'veg')
  .fetchAll()
  .then(result => {
    console.log('Veggies:');
    for (let row of result.models) {
      console.log(`${row.attributes.id}t${row.attributes.name}`);
    }
    knex.destroy();
  });

```

带水线:

```
#!/usr/bin/env node

// $ npm install sails-postgresql waterline

const pgAdapter = require('sails-postgresql');
const Waterline = require('waterline');
const waterline = new Waterline();
const connection = require('./connection.json');

const itemCollection = Waterline.Collection.extend({
  identity: 'item',
  datastore: 'default',
  primaryKey: 'id',
  attributes: {
    id: { type: 'number', autoMigrations: {autoIncrement: true} },
    name: { type: 'string', required: true },
    type: { type: 'string', required: true },
  }
});

waterline.registerModel(itemCollection);

const config = {
  adapters: {
    'pg': pgAdapter
  },

  datastores: {
    default: {
      adapter: 'pg',
      host: connection.host,
      port: connection.port,
      database: connection.database,
      user: connection.user,
      password: connection.password
    }
  }
};

waterline.initialize(config, (err, ontology) => {
  const Item = ontology.collections.item;
  // select "id", "name", "type" from "public"."item"
  //     where "type" = $1 limit 9007199254740991
  Item
    .find({ type: 'veg' })
    .then(rows => {
      console.log('Veggies:');
      for (let row of rows) {
        console.log(`${row.id}t${row.name}`);
      }
      Waterline.stop(waterline, () => {});
    });
});

```

反对:

```
#!/usr/bin/env node

// $ npm install knex objection pg

const connection = require('./connection.json');
const knex = require('knex')({
  client: 'pg',
  connection,
  // debug: true
});
const { Model } = require('objection');

Model.knex(knex);

class Item extends Model {
  static get tableName() {
    return 'item';
  }
}

// select "item".* from "item" where "type" = ?
Item
  .query()
  .where('type', '=', 'veg')
  .then(rows => {
    for (let row of rows) {
      console.log(`${row.id}t${row.name}`);
    }
    knex.destroy();
  });

```

简单的`read`操作的语法在这些例子中有很大的不同。随着您试图执行的操作复杂性的增加，比如涉及多个表的操作，orm 语法在不同的实现之间会有更大的不同。

光是 Node.js 的 ORM 至少有几十个，所有平台的 ORM 至少有几百个。学习所有这些工具将是一场噩梦！

幸运的是，我们只需要担心一些 SQL 方言。通过学习如何使用原始 SQL 生成查询，您可以轻松地在不同的平台之间转移这些知识。

### 2.复杂的 ORM 调用可能效率低下

回想一下，ORM 的目的是获取存储在数据库中的底层数据，并将其映射到我们可以在应用程序中进行交互的对象中。当我们使用 ORM 获取某些数据时，这通常会带来一些低效。

例如，考虑我们在抽象层一节中首先看到的查询。在那个查询中，我们只是想要一个特定食谱的成分和各自数量的列表。首先，我们通过手工编写 SQL 来进行查询。接下来，我们使用查询构建器 Knex 进行查询。最后，我们使用 ORM Sequelize 进行查询。让我们看看这三个命令生成的查询。

#### 用`pg`驱动手写。

第一个查询正是我们手工编写的。它代表了准确获得我们想要的数据的最简洁的方法:

```
SELECT
  ingredient.*, item.name AS item_name, item.type AS item_type
FROM
  ingredient
LEFT JOIN
  item ON item.id = ingredient.item_id
WHERE
ingredient.dish_id = ?;

```

当我们给这个查询加上前缀`EXPLAIN`并将其发送到 PostgreSQL 服务器时，我们得到一个`34.12`的开销操作。

#### 使用 Knex 生成

下一个查询主要是为我们生成的，但是由于 Knex 查询构建器的显式特性，我们应该对输出结果有一个很好的预期:

```
select
  *, "item"."name" as "item_name", "item"."type" as "item_type"
from
  "ingredient"
left join
  "item" on "item"."id" = "ingredient"."item_id"
where
"dish_id" = ?;

```

为了可读性，我添加了新的行。除了我手写的例子中的一些小的格式和不必要的表名之外，这些查询是相同的。事实上，一旦运行了`EXPLAIN`查询，我们会得到同样的分数`34.12`。

#### 用有序 ORM 生成

现在让我们看看 ORM 生成的查询:

```
SELECT
  "dish"."id", "dish"."name", "dish"."veg", "items"."id" AS "items.id",
  "items"."name" AS "items.name", "items"."type" AS "items.type",
  "items->ingredient"."dish_id" AS "items.ingredient.dish_id",
  "items->ingredient"."item_id" AS "items.ingredient.item_id",
  "items->ingredient"."quantity" AS "items.ingredient.quantity",
  "items->ingredient"."unit" AS "items.ingredient.unit"
FROM
  "dish" AS "dish"
LEFT OUTER JOIN (
  "ingredient" AS "items->ingredient"
  INNER JOIN
  "item" AS "items" ON "items"."id" = "items->ingredient"."item_id"
) ON "dish"."id" = "items->ingredient"."dish_id"
WHERE
"dish"."id" = ?;

```

为了可读性，我添加了新的行。

可以看出，这个查询与前两个查询有很大不同。为什么它的表现如此不同？嗯，由于我们定义的关系，Sequelize 试图获得比我们要求的更多的信息。特别是，当我们真正关心属于那个`dish`的`ingredients`时，我们正在获取关于`dish`本身的信息。

这个查询的成本，根据`EXPLAIN`，是`42.32`。

### 3.ORM 不能做所有的事情

不是所有的查询都可以用 ORM 操作来表示。当我们需要生成这些查询时，我们不得不退回到手工生成 SQL 查询。这通常意味着大量使用 ORM 的代码库仍然会有一些手写的查询。这里的含义是，作为从事这些项目之一的开发人员，我们最终需要了解 ORM 语法以及一些底层 SQL 语法。

一种不太适合 ORM 的常见情况是查询包含子查询。考虑这样一种情况，我知道我已经在数据库中购买了菜肴#2 的所有配料，但是，我仍然需要购买菜肴#1 所需的任何配料。为了获得这个列表，我可能会运行以下查询:

```
SELECT *
FROM item
WHERE
  id NOT IN
    (SELECT item_id FROM ingredient WHERE dish_id = 2)
  AND id IN
(SELECT item_id FROM ingredient WHERE dish_id = 1);

```

据我所知，这个查询不能用前面提到的 ORM 清晰地表示。为了应对这些情况，ORM 通常提供将原始 SQL 注入查询接口的能力。

Sequelize 提供了一个`.query()`方法来执行原始 SQL，就好像您正在使用底层数据库驱动程序一样。使用 Bookshelf 和 Objection，您可以访问在实例化过程中提供的原始 Knex 对象，并使用它的查询构建器功能。

Knex 对象还有一个执行原始 SQL 的`.raw()`方法。使用 Sequelize，您还可以获得一个`Sequelize.literal()`方法，该方法可用于在 Sequelize ORM 调用的不同部分散布原始 SQL。

但是在每种情况下，您仍然需要了解一些底层 SQL 来生成某些查询。

## 为什么首先要使用 Sequelize？

在前面的章节中，我们给出了不需要 ORM 的原因。然而，作为最受欢迎的 Node.js ORM，并且在撰写本文时有大约 135 万的周下载量，Sequelize 的受欢迎程度让我们困惑。

虽然我们在前一节提出了有效的观点，但我们必须注意到，每周有 135 万人不会错。因此，让我们首先来看看人们可能使用 Sequelize 的一些原因:

*   Sequelize 可以覆盖多种数据库，如 Oracle、Postgres、MySQL、MariaDB、SQLite、DB2、Microsoft SQL Server 和 Snowflake
*   Sequelize 支持原始查询。这使得开发人员能够通过提供用于编写原始查询的`sequelize.query`方法来平衡事情
*   它很容易使用
*   它有坚实的交易支持
*   它防范 SQL 注入漏洞
*   它有模型验证
*   它附带了类型脚本支持

## 在 Node.js 中使用查询生成器:最佳时机

使用低级数据库驱动模块是相当诱人的。为数据库生成查询时没有开销，因为我们是手动编写查询的。我们的项目所依赖的整体依赖性也被最小化。然而，生成动态查询可能非常繁琐，在我看来，这是使用简单数据库驱动程序的最大缺点。

例如，考虑一个 web 界面，当用户想要检索项目时，可以在其中选择标准。如果用户只能输入一个选项，如颜色，我们的查询可能如下所示:

```
SELECT * FROM things WHERE color = ?;

```

这个查询与简单的数据库驱动程序配合得很好。然而，考虑一下颜色是否是可选的，是否有第二个可选字段叫做`is_heavy`。我们现在需要支持该查询的几种不同排列:

```
SELECT * FROM things; -- Neither
SELECT * FROM things WHERE color = ?; -- Color only
SELECT * FROM things WHERE is_heavy = ?; -- Is Heavy only
SELECT * FROM things WHERE color = ? AND is_heavy = ?; -- Both

```

然而，由于上述原因，一个全功能的 ORM 也不是我们想要的工具。

在这些情况下，查询构建器是一个非常好的工具。Knex 公开的接口与底层 SQL 查询如此接近，以至于我们不得不总是知道 SQL 查询是什么样子的。这种关系类似于 TypeScript 之类的东西如何翻译成 JavaScript。

只要您完全理解它生成的底层 SQL，使用查询构建器是一个很好的解决方案。永远不要把它作为一种工具来隐藏底层发生的事情。只在方便的情况下使用它，并且在你确切知道它在做什么的情况下使用它。

如果您发现自己对生成的查询的实际情况有疑问，您可以向`Knex()`实例化调用添加一个调试字段。这样做看起来像这样:

```
const knex = require('knex')({
  client: 'pg',
  connection,
  debug: true // Enable Query Debugging
});

```

事实上，本帖中提到的大多数库都包含了某种调试正在执行的调用的方法。

## 那么…我应该使用 ORM 吗？

从我们目前的讨论中，我们已经看到了 ORM 的弱点。然而，在这一小节中，我们将看看 ORM 是一个好选择的某些场景。

一般来说，因为 ORM 提供了数据库的高级抽象，所以它提供的控制比原始查询少。因此，它的性能比原始查询慢。但是为了弥补这些缺点，ORM 通常附带了许多优秀的特性:

*   跨多个数据库轻松移植
*   代码生成。在具有相当大团队的复杂项目中，数据库可能会快速变化。作为构建过程的一部分，从数据库重新生成类和映射的能力是非常必要的。如果您使用 ORM，您的代码可能不是最快的，但是您的编码将是最快的
*   ORM 工具将您从编写样板 SQL 查询中解放出来，并使您的代码保持干燥，从而使您能够专注于问题领域并加速开发过程

综上，使用 ORM 实现代码标准化、安全性、可维护性、语言抽象、DRY 等。

## Node.js“最好”的 ORM 是哪个？

到目前为止，我们已经了解了一些 Node.js ORM 和查询生成器。然而，为 Node.js 选择“最好的”ORM 并不容易，因为它们都有优点和缺点。在我看来，最好的 Node.js ORM 会根据您的应用程序需求而变化。

以前，我们根据抽象级别将用于查询和操作数据的库分为三类:

*   低级:最快并提供最多控制的数据库驱动程序。然而，将字符串连接在一起形成 SQL 是乏味的，并且会导致安全漏洞
*   中级:查询构建器，如 [Knex.js](https://knexjs.org/) ，运行在数据库客户机之上。它们使您能够以更方便的方式通过编程生成动态查询。它们也相当快，但是当处理复杂的关系时，Knex 可能会很乏味和耗时
*   最高层:像[这样的 ORM 将](https://sequelize.org/)序列化，覆盖在数据库之上，并提供简单的 API 来执行对数据库的操作。这些是最慢的，但是功能丰富，简单而强大的 API 意味着更好的开发体验和开发速度的提高

因为没有 ORM 可以解决你的所有问题，所以最好的 ORM 是最适合你的应用需求的 ORM。有了上面的信息，你应该知道你愿意交易什么，你的应用需要什么。因此，您可以选择最适合 Node.js 应用程序的 ORM。

## 结论

我们已经研究了抽象数据库交互的三个不同层次，即低级数据库驱动程序、查询构建器和高级 ORM。

我们还研究了使用每一层以及生成的 SQL 查询的利弊，包括使用数据库驱动程序生成动态查询的困难、ORM 增加的复杂性，以及使用查询生成器的好处。

感谢您的阅读，并确保在您构建下一个项目时考虑到这一点。

完成之后，您可以运行以下命令来完全删除 docker 容器，并从您的计算机中删除数据库文件:

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.

```
docker stop pg-node-orms
docker rm pg-node-orms
sudo rm -rf ~/data/pg-node-orms

```