# 如何用 PostgreSQL 和 GraphQL 创建 React 本地应用程序:第 1 部分

> 原文：<https://blog.logrocket.com/create-a-react-native-app-with-postgresql-and-graphql-part-1/>

在为 iOS 和 Android 智能手机开发移动应用程序时，React Native 是一个很好的选择。使用 React Native，您可以编写一个在两个平台上都可以工作的应用程序，唯一的区别是视图级别，iOS 和 Android 呈现不同。在这个由两部分组成的系列中，我们将了解如何使用 React Native 和 GraphQL 开发应用程序。为此，我们将构建一个简单的笔记应用程序，允许我们的用户添加笔记，查看，编辑和删除它们。

对于第一部分，我们将使用 PostgreSQL 数据库构建一个 GraphQL 服务器，作为应用程序的后端。[在第二部分](https://blog.logrocket.com/how-to-create-a-react-native-app-with-postgresql-and-graphql-part-2/)中，我们将使用 React Native 构建一个应用程序，该应用程序将使用我们服务器中的数据。

让我们开始设置服务器。我们项目的文件夹结构将如[本报告](https://github.com/jimmykimani/GraphQL-Server-Tutorial-with-Apollo-Server-and-Express/tree/notes/starter-files)所示。

为了构建我们的应用程序，您需要熟悉:

*   NodeJS
*   表达
*   数据库
*   移动开发

## 如何使用 Express 设置 Apollo 服务器

我们的后端服务器将运行在[节点 JS](https://nodejs.org/en) 和 [Express](https://expressjs.com/) 上。我们需要安装一些初始依赖项，但首先，我们需要启动我们的应用程序，运行:

```
npm init
```

现在让我们安装第一组依赖项:

```
npm install apollo-server apollo-server-express express graphql cors --save
```

我们已经安装的依赖项之一是`apollo-server`，Apollo Server 是使用任何来源的数据为 GraphQL 客户端快速构建生产就绪、自文档化 API 的最佳方式。当我们在 Express 中使用它时，它可能会在几个流行的图书馆中使用，如[哈比神](https://github.com/hapijs/hapi)和 [Koa](https://koajs.com/) 。

现在我们已经准备好开始创建我们的服务器了。将以下代码添加到`src/index.js`。

```
import cors from 'cors';
import express from 'express';
import {
    ApolloServer
} from 'apollo-server-express';

const app = express();

app.use(cors());

const server = new ApolloServer({});

server.applyMiddleware({
    app,
    path: '/graphql'
});

app.listen({
    port: 8000
}, () => {
    console.log('Apollo Server on http://localhost:8000/graphql');
});
```

让我们回顾一下到目前为止我们所拥有的，在上面的代码中，我们已经初始化了一个 Express 应用程序和一个 Apollo 服务器实例。使用 Apollo Server 的`applyMiddleware()`方法，您可以选择加入任何中间件，在我们的例子中是 Express 应用程序。此外，您可以指定 GraphQL API 端点的路径。

你可能还注意到我们将`cors`应用到了我们的 [Express 应用](https://blog.logrocket.com/forget-express-js-opt-for-these-alternatives-instead/)，这是为了允许外部域访问我们的应用。

下一步是为 Apollo 服务器实例定义模式和解析器。但是首先，让我们了解一下模式和解析器。

## (计划或理论的)纲要

提供给 Apollo 服务器的 [GraphQL 模式](https://graphql.org/learn/schema/)是通过 GraphQL 读写数据的所有可用数据。该模式包含来自强制顶级`Query`类型的类型定义，该类型允许读取后跟字段和嵌套字段的数据。这些是使用 GraphQL 模式语言定义的，并允许我们以一种语言不可知的方式谈论 GraphQL 模式。

以下是我们的`schema/notes.js`中的内容:

```
import {
  gql
} from 'apollo-server-express';

export default gql `
  extend type Query {
    notes: [Note!]
    note(id: ID!): Note!
  }

  type Note {
    id: ID!
    text: String!
  }
`;
```

让我们来看看我们的模式定义中需要注意的一些术语。我们已经介绍了`Query`类型，但是让我们看看其余的:

*   `notes`被定义为`[Note!]`，它代表一个`Note`对象的数组。感叹号意味着**不可为空，**这意味着当您查询`notes`字段时，您总是可以期待一个数组(包含零个或多个项)
*   每个`Note`对象包含一个定义为`ID`的`id`字段。`ID`标量类型代表一个惟一的标识符，通常用于重新获取一个对象或作为缓存的键。它的序列化方式与字符串的序列化方式相同。然而，将它定义为`ID`意味着它不适合人类阅读
*   `Note`对象还有一个被定义为`String`的`text`字段，它是内置的**标量**类型之一，是一个 UTF 8 字符序列
*   您还会注意到`note`字段以`(id: ID!)`的形式提供了`id`选项。这允许使用 [GraphQL 参数](https://graphql.org/learn/queries/#arguments)来细化查询。这允许我们通过在查询中提供它的`ID`来获取单个音符。参数*也是不可空的*，因为它必须在获取单个便笺时提供。

上述模式应放在`src/schema/notes.js`中。然后，我们可以在`src/schema`目录中使用以下代码创建一个`index.js`文件:

```
import { gql } from "apollo-server-express";

    import noteSchema from "./notes";

    const linkSchema = gql`
      type Query {
        _: Boolean
      }

      type Mutation {
        _: Boolean
      }

      type Subscription {
        _: Boolean
      }
    `;

    export default [linkSchema, noteSchema];
```

## 下决心者

解析器负责操作和返回数据，可以把它们看作查询处理程序。`Query`类型中的每个顶级查询都有一个解析器，但是我们将为每个字段创建自己的解析器。解析器被分组在一个 JavaScript 对象中，通常称为**解析器映射**。

让我们在`src/resolvers`中创建一个`notes.js`文件，并向其中添加以下代码:

```
export default {
    Query: {
        notes: (parent, args, {
            models
        }) => {
            return Object.values(models.notes)
        },
        note: (parent, {
            id
        }, {
            models
        }) => {
            return models.notes[id]
        }
    }
}
```

上述解析器允许我们使用`note`字段查询单个音符，并使用`notes`字段提供一个`id`参数或一组音符。一旦我们使用[序列](https://sequelize.org)将我们的应用程序链接到数据库，我们将清理我们的解析器，并添加突变，这将允许我们执行除读取数据之外的操作。目前，这些解决了我们将在`src/models/index.js`中拥有的伪数据，它包含以下代码:

```
let notes = {
    1: {
        id: '1',
        text: 'Hello World',
    },
    2: {
        id: '2',
        text: 'By World',
    },
};

export default {
    notes
};
```

现在，我们已经为如何存储数据建立了框架，让我们看看如何使用[查询和变异](https://graphql.org/learn/queries/)进行数据操作。

## 查询和突变

GraphQL 中的查询和变异允许我们访问和操作 GraphQL 服务器上的数据。查询负责读取操作，而变异负责创建、更新和删除操作。

为了创建我们的查询和变异，我们必须首先在我们的模式中定义它们。让我们编辑`src/schema/notes.js`如下:

```
import {
  gql
} from 'apollo-server-express';

export default gql `
  extend type Query {
    notes: [Note!]
    note(id: ID!): Note!
  }

  extend type Mutation {
    createNewNote(text: String!): Note!
    deleteNote(id: ID!): Boolean!
    updateNote(id: ID!, text: String!): Note!
  }

  type Note {
    id: ID!
    text: String!
  }
`;
```

前面已经定义了查询，所以我们新添加的是`Mutation`类型。从定义的类型中，您可以了解返回的数据是什么样子的。例如，`createNewNote`接受一个字符串形式的`text`参数，并生成和返回一个`Note`对象，`updateNote`与此类似，但它也接受`id`参数，以便找到要更新的注释。同时，`deleteNote`获取一个用于标识目标笔记的`id`布尔值，并返回一个指示该笔记是否已被成功删除的布尔值。

这是测试查询的方法。在您的 [graphql playground 中键入以下代码:](https://github.com/prisma/graphql-playground)

### 获取所有笔记

获取并返回所有保存的便笺。

```
query {
  notes {
    id
    text
  }
}
```

接下来点击*执行查询*按钮，您应该能够看到以下结果:

### 结果

```
{
  "data": {
    "notes": [
      {
        "id": "1",
        "text": "Hello World"
      },
      {
        "id": "3",
        "text": "By World"
      }
    ]
  }
}
```

### 按 id 获取便笺

这将返回一段与给定参数`id`匹配的数据

```
query{
  note(id: "1") {
    id
   text
  }
}
```

### 结果

```
{
  "data": {
    "note": {
      "id": "1",
      "text": "Hello World"
    }
  }
```

接下来，我们需要编写一个突变解析器。让我们这样编辑`src/resolvers/notes.js`
:

```
import uuidv4 from 'uuid/v4';

export default {
    Query: {
        notes: (parent, args, {
            models
        }) => {
            return Object.values(models.notes)
        },
        note: (parent, {
            id
        }, {
            models
        }) => {
            return models.notes[id]
        }
    },
    Mutation: {
        createNewNote: (parent, {
            text
        }, {
            models
        }) => {
            const id = uuidv4();
            const newNote = {
                id,
                text
            }
            models.notes[id] = newNote;
            return newNote;
        },

        deleteNote: (parent, {
            id
        }, {
            models
        }) => {
            const {
                [id]: note, ...otherNotes
            } = models.notes
            if (!note) {
                return false
            }
            models.notes = otherNotes
            return true
        },
}
```

对于`createNewNote`突变，我们正在使用`uuid/v4`库生成唯一的`id`，因此我们需要通过运行以下命令来安装它:

`npm install uuid/v4 --save`

解析器接受一个文本参数，生成一个惟一的`id`并使用它们创建一个`Note`对象，然后将该对象追加到注释列表中。然后，它返回已经创建的新便笺。

`deleteNote`变异采用一个`id`参数，找到一个注释并创建一个新的`notes`对象，没有要删除的注释，并用新的对象替换旧的`notes`对象。如果没有找到笔记，它返回`false`，如果找到并删除了笔记，则返回`true`。

`updateNote`变异将`id`和`text`作为参数，并相应地更新`notes`对象中的`Note`，最后返回更新后的注释。

当我们在浏览器上运行`npm start`并转到`[localhost:3000/graphql](3000/graphql)`来运行我们的应用程序时，我们现在可以通过 GraphQL playground 访问这些端点。然后，您可以尝试以下操作:

### 创建新便笺

接受文本参数，创建并返回注释。

```
mutation {
  createNewNote( text: "Hello GraphQl") {
    id
    text
  }
}
```

### 结果

```
{
  "data": {
    "createNewNote": {
      "id": "3",
      "text": "Hello GraphQl"
    }
  }
}
```

### 更新注释

接受文本和 id 参数，更新并返回注释。

```
mutation {
  updateNote(id:3, text:"Graphql is awesome") {
    id
    text
  }
}
```

### 结果

```
{
  "data": {
    "updateNote": {
      "id": "4",
      "text": "Graphql is awesome"
    }
  }
}
```

### 删除注释

接受 id 参数并删除匹配的注释。

```
mutation {
  deleteNote(id:5)
}
```

## 添加带序列的 PostgreSQL

我们已经准备好了大部分应用程序，所以接下来我们需要考虑的是如何将数据保存到数据库中。我们的数据库将 [PostgreSQ](https://www.postgresql.org/) L，所以继续从他们的网站安装它，然后启动它。

之后，创建一个名为`mynotes`的数据库。为此，您可以在终端中运行命令`psql`来打开 PosgreSQL CLI。一次开放运行:

```
CREATE DATABASE mynotes;
CREATE USER postgres;
GRANT ALL PRIVILEGES ON DATABASE members TO postgres;
```

接下来，您需要将 Node.js 的[PostgreSQL](https://github.com/brianc/node-postgres)和 [Sequelize (ORM)](https://github.com/sequelize/sequelize) 添加到您的项目中。运行:

```
npm install pg sequelize --save
```

下一步是更新我们的模型，准备链接到数据库，如下所示。该模型定义了存储在数据库中的每条数据的形状:

```
const note = (sequelize, DataTypes) => {
  const Note = sequelize.define("note", {
    text: DataTypes.STRING
  });
  return Note;
};

export default note;
```

接下来，从应用程序的`src/models/index.js`文件中连接到数据库:

```
import Sequelize from "sequelize";

const sequelize = new Sequelize(
  process.env.DATABASE,
  process.env.DATABASE_USER,
  process.env.DATABASE_PASSWORD,
  {
    dialect: "postgres"
  }
);

const models = {
  Note: sequelize.import("./notes")
};

Object.keys(models).forEach((key) => {
  if ("associate" in models[key]) {
    models[key].associate(models);
  }
});

export { sequelize };
export default models;
```

您需要定义数据库名称、数据库超级用户和用户密码。您可能还想定义一种数据库方言，因为 Sequelize 也支持其他数据库。

在同一个文件中，如果您有多个模型(因此有上面的`foreEach`循环),您可以将所有的模型物理地相互关联，以将它们作为数据库的数据访问层(模型)公开给应用程序。在我们的例子中，我们只有一个模型，但这仍然是很好的了解。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

现在让我们在项目的根目录下创建一个`.env`文件来保存一些关键数据作为环境变量。数据库凭证(数据库名称、数据库超级用户名称、数据库超级用户密码)可以作为环境变量与我们希望运行服务器的端口一起存储。

在`.env`文件中添加以下变量或根据您的喜好进行更改:

```
PORT=3000
DATABASE=mynotes
DATABASE_USER=postgres
DATABASE_PASSWORD=postgres
```

接下来，让我们更新`src/index.js`以在应用程序启动时同步我们的数据库。进行以下更改:

```
...
import 'dotenv/config';
import models, {
    sequelize
} from './models';
...

sequelize.sync().then(async () => {
    app.listen({
        port: 8000 // you could change this to process.env.PORT if you wish
    }, () => {
        console.log('Apollo Server on http://localhost:8000/graphql');
    });
});
```

我们现在已经将应用程序链接到了数据库，所以接下来我们需要做的是替换解析器中的逻辑。

## 如何将解析器连接到数据库

我们以前有虚拟数据，但有了 Sequelize，我们现在可以将数据与数据库同步，从而实现数据持久性。为了实现这一点，我们需要更新我们在`src/resolvers/notes.js`中的解析器来集成 Sequelize API。

```
export default {
  Query: {
    notes: async (parent, args, { models }) => {
      return await models.Note.findAll();
    },

    note: async (parent, { id }, { models }) => {
      return await models.Note.findByPk(id);
    }
  },
  Mutation: {
    createNewNote: async (parent, { text }, { models }) => {
      return await models.Note.create({
        text
      });
    },

    deleteNote: async (parent, { id }, { models }) => {
      return await models.Note.destroy({
        where: {
          id
        }
      });
    },
    updateNote: async (parent, { id, text }, { models }) => {
      await models.Note.update(
        {
          text
        },
        {
          where: {
            id: id
          }
        }
      );
      const updatedNote = await models.Note.findByPk(id, {
        include
      });
      return updatedNote;
    }
  }
};
```

你会注意到我们不再需要`uuid/v4`,因为 Sequelize 自动处理我们数据库中的 id 分配。`findAll()`和`findByPk()`是数据库操作常用的顺序化方法，`findAll()`返回某个表中的所有条目，而`findPk()`识别并返回符合某个标准的条目，比如我们示例中的匹配`id`。

我们使用的其他顺序化方法包括`update()`和`destroy()`来分别更新和删除记录。

除此之外，我们使用 [async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 来实现对数据库的基于承诺的异步 Javascript 请求。

## 结论

太好了！我们都准备好了，下一步是将我们的 GraphQL 后端与我们的前端应用程序集成，这将在本教程的第二部分中为移动设备[构建。我们将与阿波罗和 React Native 合作，这应该是令人兴奋的。敬请期待！](https://blog.logrocket.com/how-to-create-a-react-native-app-with-postgresql-and-graphql-part-2/)

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)