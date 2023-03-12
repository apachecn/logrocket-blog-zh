# 从 GraphQL 切换到 REST？用沙发日志博客放松一下

> 原文：<https://blog.logrocket.com/switch-graphql-to-rest-with-sofa/>

从 GraphQL 切换到 REST 或将 GraphQL AP 转换为 REST API 有几个原因。举一个非常基本的例子，您可能想要适应偏好 REST 的 API 消费者。

GraphQL 不是万能的解决方案。它旨在以声明的方式使您能够只选择您需要的信息或操作。这可能是福也可能是祸；要求太多东西会影响你的应用程序的性能。

随着用户群的增长，您可能希望将请求的内容存储在[反向代理服务器](https://www.nginx.com/resources/glossary/reverse-proxy-server/)中，以减少服务器的流量。或者，您可能决定将经常访问的信息保存在靠近使用 CDN 的客户端的位置。

REST API 通常公开许多端点，因此很容易配置 web 缓存来匹配某些 URL 模式、HTTP 方法或特定资源。GraphQ 不是这样的，它只公开一个端点来进行查询。由于每个查询都可能不同，因此配置 web 缓存更加困难。

这些只是利用 GraphQL 和 REST 的优点所带来的一些好处。但在实践中，你如何实现这种转变呢？

一种方法是使用 REST 端点重新实现 GraphQL 查询。另一种方法是实现一个 HTTP 代理服务器，它接受 REST 请求，然后在返回响应之前调用 GraphQL API。

在本教程中，我将带您了解如何使用我的首选解决方案将 GraphQL 查询、变异和订阅转换为 REST API: [Sofa](https://sofa-api.com/) 。

## 为什么是沙发？

Sofa 是安装在 GraphQL 服务器上的 Node.js 库。根据[官方文档](https://sofa-api.com/)，Sofa“获取你的 GraphQL 模式，寻找可用的查询、变异和订阅，并将所有这些转化为 REST API。”

使用 Sofa 将您的 GraphQL API 切换到 REST 可以让您的 REST API 在几分钟内工作，而不是几天、几周甚至几个月。Sofa 简单地采用您现有的 GraphQL 模式并返回相应的 REST 端点，消除了重写代码库或编写代理服务器的需要。通过这种方式，您可以逐渐迁移到 REST，而不会影响现有代码的内部实现。它是自动生成的，并带有最新的 REST API 文档。

## 沙发的工作原理

让我们借助 Sofa、GraphQL、Express 来搭建一个小书作者 API 服务器。然后，我们将从中创建和阅读书籍和作者。

首先，初始化一个新的 Node.js 项目并安装所需的依赖项。

```
mkdir sofa-api-example
cd sofa-api-example
npm init
npm install express typescript graphql express-graphql graphql-tools

```

使用下面的命令创建一个 TypeScript 配置文件(`tsconfig.json`)。

```
npx tsc --init --rootDir src --outDir build \
--esModuleInterop --resolveJsonModule --lib es6 \
--module commonjs --allowJs true --noImplicitAny true

```

接下来，创建一个模式文件(`types.ts`)。

```
// types.ts

export const typeDefs = `
  type Book {
    id: ID!
    title: String!
    author: [Author!]!
    summary: String
    genre: [Genre]
  }
  type Author {
    id: ID!
    firstname: String!
    lastname: String!
    dob: String
  }
  type Genre {
    id: ID!
    name: String!
  }
  input AuthorInput{
    firstname: String!
    lastname: String!
    dob: String
  }
  input BookInput{
    title: String!
    author: String!
    summary: String
    genre: String!
  }
  type Query {
    book(id: ID!): Book
    books: [Book!]
    author(id: ID!): Author
    authors: [Author!]
    genre(id: ID!): Genre
    genres: [Genre!]
  }
  type Mutation {
    addBook(book: BookInput!): Book
    addAuthor(author: AuthorInput!): Author
    addGenre(name: String!): Genre
  }
  type Subscription {
    onBook: Book
  }
  schema {
    query: Query
    mutation: Mutation
    subscription: Subscription
  }
`;

```

定义了模式之后，下一步是定义相应的解析器。

```
// resolver.ts

import { books, authors, genres } from './data';
import Chance from 'chance';
const chance = new Chance();
import { PubSub } from 'graphql-subscriptions';
const pubsub = new PubSub();
const BOOK_ADDED = 'BOOK_ADDED';
export const resolvers = {
  Query: {
    book(_: any, { id }: any){
      return books.find(book => book.id === id)
    },
    books(){
      return books
    },
    author(_: any, { id }: any){
      return authors.find( author => author.id === id)
    },
    authors(){
      return authors;
    },
    genre(_: any, { id }: any){
      genres.find( genre => genre.id === id);
    },
    genres(){
      return genres
    }
  },
  Mutation: {
    addBook(_: any, bookInput: any) {
      const book = {...bookInput, id: chance.guid()}
      books.push(book)
      pubsub.publish(BOOK_ADDED, { onBook: book });
    },
    addAuthor(_: any, authorInput: any) {
      const author = {...authorInput, id: chance.guid()};
      authors.push(author);
      return author;
    },
    addGenre(_: any, name: string) {
      const genre = {name, id: chance.guid()};
      genres.push(genre);
      return genre;
    },
  },
  Subscription: {
    onBook: {
      subscribe: () => pubsub.asyncIterator([BOOK_ADDED]),
    },
  },
}

```

现在我们已经有了模式和解析器，让我们连接 REST 和 GraphQL APIs。

```
// index.ts

import { makeExecutableSchema } from '@graphql-tools/schema';
import express from 'express';
import bodyParser from 'body-parser';
import { graphqlHTTP } from 'express-graphql';
import { typeDefs } from './types';
import { resolvers } from './resovers'
import { useSofa, OpenAPI } from 'sofa-api';
import * as swaggerDocument from './swagger.json';
import * as path from 'path';
import * as swaggerUi from 'swagger-ui-express';
const app = express();
app.use(bodyParser.json());

const schema = makeExecutableSchema({
  typeDefs,
  resolvers,
});

app.use('/api',
  useSofa({
    schema,
  })
);

app.use(
  '/graphql',
  graphqlHTTP({
    schema,
    graphiql: true,
  })
);
const port = 4000;
app.listen(port, () => {
  console.log(`started server on port: ${port}`)
});

```

在上面的文件中，我们使用了来自`@graphql-tools/schema`模块的`makeExecutableSchema`来组合类型定义和解析器。接下来，我们创建了两个独立的 API。第一个是我们使用`useSofa`中间件创建的 REST API，它接受`schema`作为参数。它通过`/api`途径公开 REST APIs。第二个是 GraphQL API，通过`/graphql`途径公开。GraphQL API 启用了 GraphQL UI。

Sofa 将所有 GraphQL 查询转换为`GET`端点，将变异转换为`POST`，并将订阅转换为 webhooks。还可以定制用于特定查询或变异的 HTTP 动词。例如，如果在您的一个突变中需要一个`PUT`而不是`POST`方法，您可以做以下事情。

```
api.use(
  '/api',
  sofa({
    schema,
    method: {
      'Mutation.addGenre': 'PUT',
    },
  })
);

```

现在让我们测试一些 GraphQL 查询和变异以及它们对应的 REST 端点。

### 使用 GraphQL 添加作者

请求:

```
mutation{
  addAuthor(author: {firstname: "John", lastname: "Doe", dob:"2020-08-15"}){
    id
    firstname
    lastname
    dob
  }
}

```

回应:

```
{
  "data": {
    "addAuthor": {
      "id": "cd9aada0-2c59-5f5a-9255-7835ecd19d76",
      "firstname": "John",
      "lastname": "Doe",
      "dob": "2020-08-15"
    }
  }
}

```

### 使用 REST 添加作者

请求:

```
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"author":{"firstname": "John", "lastname": "Doe", "dob":"2020-08-15"}}' \
  http://localhost:4000/api/add-author
{"id":"fd8e1958-cc1f-52b4-8bc1-53710616fd0d","firstname":"John","lastname":"Doe","dob":"2020-08-15"}%

```

回应:

```
{
   "id": "fd8e1958-cc1f-52b4-8bc1-53710616fd0d",
   "firstname": "John",
   "lastname": "Doe",
   "dob": "2020-08-15"
}

```

### 使用 GraphQL 列出图书

请求:

```
{
  books{
    id
    title
    summary
    genre{
      name
    }
    author{
      firstname
      lastname
    }
  }
}

```

回应:

```
{
  "data": {
    "books": [
      {
        "id": "b2ca39a8-e21b-547a-9da4-eff9e0f6e113",
        "title": "Di rujen fug nebitovo dodmikut.",
        "summary": "Za lo zenle mibajfem icudip zezucvod gun vuwtait nu mod asamockin obu ewubub zodez roragu.",
        "genre": [
          {
            "name": "ohva"
          },
          {
            "name": "hohutten"
          }
        ],
        "author": [
          {
            "firstname": "Eunice",
            "lastname": "Hunter"
          }
        ]
      },
      {
        "id": "d2075892-e44b-5a5c-ac75-62d5639655b1",
        "title": "Neti ud ciribnoc re ukse.",
        "summary": "Mazraz zoc maprogna gikmef se ve joge wavca vawavo upkeep hiut madtadla fude uwka lepekjij igimav.",
        "genre": [
          {
            "name": "ohva"
          },
          {
            "name": "dif"
          }
        ],
        "author": [
          {
            "firstname": "Steven",
            "lastname": "Fred"
          }
        ]
      },

```

### 用 REST 列出图书

请求:

```
curl --header "Content-Type: application/json" \
  --request GET \
  http://localhost:4000/api/books

```

回应:

```
[
  {
    "id": "b2ca39a8-e21b-547a-9da4-eff9e0f6e113",
    "title": "Di rujen fug nebitovo dodmikut.",
    "author": [
      {
        "id": "fc118537-2cc8-558c-abb6-0733bf1ddfd1"
      }
    ],
    "summary": "Za lo zenle mibajfem icudip zezucvod gun vuwtait nu mod asamockin obu ewubub zodez roragu.",
    "genre": [
      {
        "id": "6ad4d748-bf88-5a89-8ca0-d73e8de3ed18"
      },
      {
        "id": "492b4ae9-1c07-5f6f-b5a6-9258d24338e1"
      }
    ]
  },
  {
    "id": "d2075892-e44b-5a5c-ac75-62d5639655b1",
    "title": "Neti ud ciribnoc re ukse.",
    "author": [
      {
        "id": "31cbd90d-73a4-5649-a0ce-ad230f41e2f8"
      }
    ],
    "summary": "Mazraz zoc maprogna gikmef se ve joge wavca vawavo upkeep hiut madtadla fude uwka lepekjij igimav.",
    "genre": [
      {
        "id": "6ad4d748-bf88-5a89-8ca0-d73e8de3ed18"
      },
      {
        "id": "ff85e7bb-37bc-5875-9243-0b7fec42b286"
      }
    ]
  },
  {
    "id": "aafc2536-ef57-503a-bf18-309cdad3a835",
    "title": "Et urvowpi josrowus wervek wuce.",
    "author": [
      {
        "id": "fc118537-2cc8-558c-abb6-0733bf1ddfd1"
      }
    ],
    "summary": "Hoot ez poifufo hal urlirguw irpomol sozca zok agloh ak ra ovves kidme.",
    "genre": [
      {
        "id": "6ad4d748-bf88-5a89-8ca0-d73e8de3ed18"
      },
      {
        "id": "ff85e7bb-37bc-5875-9243-0b7fec42b286"
      }
    ]
  },
  {
    "id": "a6152ed4-430f-55cd-b750-ca5bac562640",
    "title": "Lofe melrazlov tu zu ra.",
    "author": [
      {
        "id": "fc118537-2cc8-558c-abb6-0733bf1ddfd1"
      }
    ],
    "summary": "Vibaduf nagad ocele rigo nirjo ermosno fu det cuh fa hej bopozbo hasna cufif monapmok ubaulewol luru.",
    "genre": [
      {
        "id": "492b4ae9-1c07-5f6f-b5a6-9258d24338e1"
      },
      {
        "id": "ff85e7bb-37bc-5875-9243-0b7fec42b286"
      }
    ]
  },
  {
    "id": "a5d9a306-edfa-5564-8c88-0f27ed7d1742",
    "title": "Ehinaj sowum ezufokew amwemah ifumuc.",
    "author": [
      {
        "id": "31cbd90d-73a4-5649-a0ce-ad230f41e2f8"
      }
    ],
    "summary": "Guvek mab itaanubo gogogsar duva pidi vu ropvum luvud hubguz lille odro dadespe suafaka sos.",
    "genre": [
      {
        "id": "6ad4d748-bf88-5a89-8ca0-d73e8de3ed18"
      },
      {
        "id": "ff85e7bb-37bc-5875-9243-0b7fec42b286"
      }
    ]
  },
  {
    "id": "8f507b93-a2c1-54c8-b660-0b40c411480c",
    "title": "Elihin lottev ew bi dernoza.",
    "author": [
      {
        "id": "8989180f-6b7b-5bc2-a367-fcd9b816ed26"
      }
    ],
    "summary": "Vo tazipnep ire joucamu uhjomet ebubekaja eziwenhib piw gatcokup keijsec uculive kajes hehud uv lano.",
    "genre": [
      {
        "id": "ff85e7bb-37bc-5875-9243-0b7fec42b286"
      },
      {
        "id": "85d3ee83-7594-5c8c-85c4-c33233e4323c"
      }
    ]
  },
  {
    "id": "8cf2e033-6823-56de-9424-bc4072c464e3",
    "title": "Jeztoz jisnifa worazet kanpede ti.",
    "author": [
      {
        "id": "8989180f-6b7b-5bc2-a367-fcd9b816ed26"
      }
    ],
    "summary": "Fu tazoj socdigzo hanemnep li da bopacfow lugic nam onekaam og ezurni ku liiz ce ha.",
    "genre": [
      {
        "id": "85d3ee83-7594-5c8c-85c4-c33233e4323c"
      },
      {
        "id": "6ad4d748-bf88-5a89-8ca0-d73e8de3ed18"
      }
    ]
  },
  {
    "id": "1b57d182-a083-589b-845d-03770c22f08f",
    "title": "Waj vudsadso ju umameto nokojjuk.",
    "author": [
      {
        "id": "992b2ec7-cd79-5a22-b0e7-d9fba294456d"
      }
    ],
    "summary": "Bi do ipi riwwifel wugaz fekel tejaak wot vudlavgo hasir giti paj soprakju.",
    "genre": [
      {
        "id": "85d3ee83-7594-5c8c-85c4-c33233e4323c"
      },
      {
        "id": "6ad4d748-bf88-5a89-8ca0-d73e8de3ed18"
      }
    ]
  },
  {
    "id": "0f348d87-15db-53e4-943a-925ba93ce806",
    "title": "Le da tiorloj nansuzve jeesosak.",
    "author": [
      {
        "id": "31cbd90d-73a4-5649-a0ce-ad230f41e2f8"
      }
    ],
    "summary": "Doowam cu tepaluj buv cer danorma sebovo obusoc ne nu hojefiw puov muhogre oke kucjuzpev tacet cuto kimab.",
    "genre": [
      {
        "id": "492b4ae9-1c07-5f6f-b5a6-9258d24338e1"
      },
      {
        "id": "ff85e7bb-37bc-5875-9243-0b7fec42b286"
      }
    ]
  },
  {
    "id": "d48cfe82-5e26-59de-9025-cdf19b4461a9",
    "title": "Ok izu udihap necfisa di.",
    "author": [
      {
        "id": "8989180f-6b7b-5bc2-a367-fcd9b816ed26"
      }
    ],
    "summary": "Re rueh wawule raigomo vijteco oso ceva tuh hup talac popozude zahatu.",
    "genre": [
      {
        "id": "ff85e7bb-37bc-5875-9243-0b7fec42b286"
      },
      {
        "id": "492b4ae9-1c07-5f6f-b5a6-9258d24338e1"
      }
    ]
  }
]

```

## 生成 REST 文档

Sofa 能够使用模式定义文件生成 OpenAPI 文档。为了为我们的 author API 自动生成文档，我们将使用`onRoute`选项扩展 Sofa 中间件。

```
// index.ts

app.use('/api',
  useSofa({
    schema,
    onRoute(info) {
      openApi.addRoute(info, {
        basePath: '/api',
      });
    },
  })
);

openApi.save(path.join(__dirname, '/swagger.yml'));
openApi.save(path.join(__dirname, '/swagger.json'));
app.use('/api/docs', swaggerUi.serve, swaggerUi.setup(swaggerDocument));

```

文档通过使用`swagger-ui-express`中间件的`/api/docs`路径提供。

## 结论

Sofa 利用 GraphQL 的标准化模式和解析器将某些 API 概念映射回 REST。它旨在帮助您加快 API 中 REST 的迁移或支持，并为您的用户提供不同的 API 类型。

我个人会推荐 Sofa，因为它消除了编写新的控制器和端点甚至编写 API 文档的需要。

这篇文章的完整代码可以在 GitHub 上找到。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.