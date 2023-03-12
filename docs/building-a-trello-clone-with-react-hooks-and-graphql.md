# 用 React 钩子和 GraphQL 构建一个 Trello 克隆

> 原文：<https://blog.logrocket.com/building-a-trello-clone-with-react-hooks-and-graphql/>

本文解释了如何使用 React 钩子和 GraphqQL 服务器构建一个类似 Trello 的应用程序。在进入应用程序之前，让我给你解释一下为什么我们需要构建它。

学习一个概念或技术应该在实践中结束。练习我们所学的是记忆概念的好方法。它会伴随你很长时间。这是我们制造特雷罗克隆体的主要原因。

同时，Trello 包含了一些有趣的概念，如嵌套拖放、web 套接字等。

现在，让我们看看如何构建这个应用程序。我们将首先为它开发后端，然后我们将开发前端。

## 演示

![A gif of a Trello app.](img/b01af900f7463f0e33d8100218b01812.png)

## 后端

最初，我想用 [Hasura](https://hasura.io/) ，这是一个在几分钟内构建 GraphQL 的很棒的工具。

但是我们的应用程序需要一些不仅仅是 CRUD 的功能。尽管我们可以用 Hasura 编写自己的自定义解析器，但我想从头开始构建它，以便更容易地解释它。

在开始编写代码之前，让我们看看我们的应用程序中涉及了什么类型的实体。这里，我们有两个主要实体:

*   **部分**–包含所有的卡片。用户将创建一个部分，并在其中添加卡片。
*   **卡片**–它们包含了所有的任务细节。用户创建了一个卡片，并将所有的任务细节存储在里面。
    T3

## 属国

*   [Apollo-server-express](https://www.npmjs.com/package/apollo-server-express)–它有助于将 Apollo graphQL 服务器与 ExpressJS 连接起来
*   [Express](https://www.npmjs.com/package/express)–Express 是运行服务器的 Node.js 框架
*   mongose–mongose 连接 express 服务器和 MongoDB
*   一个 JavaScript 实用程序库

`npm install apollo-server apollo-server-express express lodash mongoose`

一旦我们安装了所需的依赖项，让我们创建 GraphQL 服务器。创建一个文件`server.js`并添加以下代码:

```
const express = require("express");
const { ApolloServer, gql } = require("apollo-server-express");
const merge = require("lodash/merge");
const mongoose = require("mongoose");
const { PubSub } = require("apollo-server");
const { createServer } = require("http");
require("dotenv").config();

const typeDefs = gql``;

const resolvers = {};

const MONGO_USER = process.env.MONGO_USER || "root";
const MONGO_PASS = process.env.MONGODB_PASS;
mongoose
  .connect(
    `mongodb://${MONGO_USER}:${MONGO_PASS}@ds131902.mlab.com:31902/trello-hooks-graphql-clone`,
    { useNewUrlParser: true, useUnifiedTopology: true }
  )
  .then(() => {
    console.log("mongodb connected successfully");
    const server = new ApolloServer({
      typeDefs,
      resolvers
    });
    const app = express();
    server.applyMiddleware({ app });
    const httpServer = createServer(app);

    const PORT = process.env.PORT || 4444;
    httpServer.listen({ port: PORT }, () => {
      console.log(`Server is running in port ${PORT}`);
    });
  })
  .catch((err) => {
    console.log(err);
  });
```

在上面的代码中，我们导入`ApolloServer`和`gql`来运行 GraphQL 服务器，导入 Mongoose 来连接 MongoDB。

```
const express = require("express");
const { ApolloServer, gql } = require("apollo-server-express");
const mongoose = require("mongoose");
const { PubSub } = require("apollo-server");
const { createServer } = require("http");
require("dotenv").config();
```

之后，我们为 Apollo GraphQL 服务器定义了`typeDefinitions`和`resolvers`。

```
const typeDefs = gql;

const resolvers = {};
```

### 类型定义

它包含所有的 GraphQL 模式定义。这里，我们有两个实体:**段**和**卡**。大多数情况下，实体应该被定义为一种类型。

这里需要注意的一点是，GraphQL 有两种根类型:

Query 处理所有的获取操作，包括 GET 请求和 REST API 协议。变异处理数据操作，这是 REST API 中的 POST 请求。

## 下决心者

基本上，解析器解析我们在模式中定义的类型。例如，如果您在模式中定义了一个查询类型，那么它应该在解析器中被解析。

之后，我们需要将我们的服务器与 MongoDB 数据库连接起来。我们可以使用 Mongoose connect 方法来实现:

```
const MONGO_USER = process.env.MONGO_USER || "root";
const MONGO_PASS = process.env.MONGODB_PASS;
mongoose
  .connect(
    `mongodb://${MONGO_USER}:${MONGO_PASS}@ds131902.mlab.com:31902/trello-hooks-graphql-clone`,
    { useNewUrlParser: true, useUnifiedTopology: true }
  )
  .then(() => {
    console.log("mongodb connected successfully");
  })
  .catch((err) => {
    console.log(err);
  });
```

注意:我在这里使用的是 [mlab](http://mlab.com/) 托管服务。你可以在本地运行，或者使用一些托管服务，如 [mlab](http://mlab.com/) 或 [mongodb atlas](https://www.mongodb.com/cloud/atlas) 。

一旦我们与 MongoDB 成功连接，让我们创建我们的 GraphQL 服务器并运行它:

```
const server = new ApolloServer({
      typeDefs,
      resolvers,
      context: () => ({
      }),
    });
    const app = express();
    server.applyMiddleware({ app });
    const httpServer = createServer(app);
    server.installSubscriptionHandlers(httpServer);
    const PORT = process.env.PORT || 4444;
    httpServer.listen({ port: PORT }, () => {
      console.log(`Server is running in port ${PORT}`);
    });
```

到目前为止，我们已经建立了一个基本的 GraphQL 服务器，它与 MongoDB 数据库相连。现在，是时候在应用程序中创建业务逻辑了。

### 文件结构

![A card mutation dropdown menu.](img/ceb56f2a40516d28e39ff58c91494cbe.png)

我根据特征和实体来组织文件。他们被称为[功能文件夹](https://blog.risingstack.com/node-hero-node-js-project-structure-tutorial/)。在每个实体或特性的内部，我有`index.js`，它是特定特性的根文件。

`mutationResolvers`和`queryResolvers`文件夹处理查询和变异解析器，以及包含 Mongoose 模式模型的`model.js`文件。

## 截面实体

在为区段编写查询和变更之前，我们需要在我们的 GraphQL SDL(模式定义语言)中创建区段类型。无论我们想在节中存储什么数据，都应该在类型中定义:

```
type Section {
    id: ID!
    title: String!
    label: String!
    pos: Int!
    description: String
    cards: [Card]
 }
```

为了更好地理解它，可以把它看作一个键值对:在左边，它将是 keyname。在右边，它将是一个值类型。

这里，我们有值类型 String 和 Int。

如果你仔细观察，我们会发现一个叫做卡片的值类型。虽然这是新的，但其他值类型很简单。

卡片是我们在开始时讨论过的实体。就像我们有一个叫做 Section 的类型。我们也有定制类型的卡。

为什么在类型部分中定义它？

在 GraphQL 中，我们称这些关系为[。](https://www.apollographql.com/docs/apollo-server/schema/schema/#the-schema-definition-language)

每个部分将包含许多卡片。这是 1 对 N 的关系。如果我们将其转换为 GraphQL 模式，它将类似于上面的类型。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

之后，我们需要为 Section 实体定义查询和变异类型:

```
extend type Query {
    fetchSections: [Section]
  }
  extend type Mutation {
    insertSection(request: insertSectionInput): Section
    updateSectionPos(request: updateSectionPosInput): Section
  }
```

这里，我们定义了查询类型`fetchSections`，这非常简单。

如果我们点击 GraphQL 查询，它应该返回所有的部分。

有趣的是，Mutation 有两个操作字段:`insertSection`和`updateSectionPos`，还有一个参数。

每个根类型(查询和变异)都可以接受参数并返回数据。数据应该包括模式中定义的类型。

**重要提示**:参数类型应该单独定义为 GraphQL 模式中的输入。您可以在 GraphQL 中使用 Type 关键字进行输入。

## `index.js`

```
const mutationResolvers = require("./mutationResolvers");
const queryResolvers = require("./queryResolvers");
const { gql } = require("apollo-server-express");
const sectionTypeDefs = gql`
  input insertSectionInput {
    title: String!
    label: String!
    pos: Int!
  }
  input updateSectionPosInput {
    sectionId: String!
    pos: Int!
  }
  type Section {
    id: ID!
    title: String!
    label: String!
    pos: Int!
    description: String
    cards: [Card]
  }
  extend type Query {
    hello: String
    fetchSections: [Section]
  }
  extend type Mutation {
    insertSection(request: insertSectionInput): Section
    updateSectionPos(request: updateSectionPosInput): Section
  }
`;
const sectionResolvers = {
  Query: {
    ...queryResolvers,
  },
  Mutation: {
    ...mutationResolvers,
  },
};
module.exports = {
  sectionTypeDefs,
  sectionResolvers,
};
```

## 模型

在编写查询和变异的解析器之前，让我们为以下部分创建 Mongoose 模型:

```
const Mongoose = require("mongoose");
const sectionSchema = new Mongoose.Schema({
  title: {
    type: String,
    required: true,
  },
  label: {
    type: String,
    required: true,
  },
  description: String,
  pos: {
    type: Number,
    required: true,
  },
});

class Section {
  static getSections() {
    return this.find().sort("pos").exec();
  }
  static getSectionById(sectionId) {
    return this.findOne({
      _id: Mongoose.mongo.ObjectID(sectionId),
    }).exec();
  }
  static insertSection(sectionInfo) {
    const section = this(sectionInfo);
    return section.save();
  }
  static updatePos(sectionId, pos) {
    return this.findOneAndUpdate(
      {
        _id: Mongoose.mongo.ObjectID(sectionId),
      },
      {
        $set: {
          pos,
        },
      },
      {
        new: true,
      }
    ).exec();
  }
}
sectionSchema.loadClass(Section);
module.exports = Mongoose.model("Section", sectionSchema);
```

一旦我们为这个部分创建了模型，我们就可以直接将它导入到我们的解析器中，并在那里使用它。然而，这将在我们的解析器中创建一个依赖项，所以将依赖项注入解析器总是好的。

为此，我们有了 GraphQL 上下文。GraphQL 上下文将与我们应用程序中的所有解析器共享。所以，我们可以在上下文中传递这个模型。

让我们这样做:

```
const server = new ApolloServer({
      typeDefs,
      resolvers,
      context: () => ({
        card: cardModel,
        section: sectionModel
      }),
    });
```

## 变化

让我们为 Section 实体编写变异解析器。它包含两个突变。它们是:

*   插入部分
*   更新部分数据

`insertSection.js`

```
module.exports = async (__, args, cxt) => {
  try {
    const sectionInfo = {
      title: args.request.title,
      label: args.request.label,
      pos: args.request.pos,
    };
    const section = await cxt.section.insertSection(sectionInfo);
    cxt.publisher.publish(cxt.SUBSCRIPTION_CONSTANTS.SECTION_ADDED, {
      sectionAdded: section,
    });
    return section;
  } catch (e) {
    console.log(e);
    return null;
  }
};
```

在这里，我们将数据插入 Mongodb。这里需要注意的重要一点是，我们没有直接向这个解析器中导入任何东西，这使得测试过程更加容易。

## 询问

查询类似于 GraphQL 中的突变，除了它不改变数据库中的数据。

一旦我们完成了截面实体，让我们将它添加到我们的`server.js`文件中:

```
const express = require("express");
const { ApolloServer, gql } = require("apollo-server-express");
const merge = require("lodash/merge");
const mongoose = require("mongoose");
const { createServer } = require("http");
require("dotenv").config();
const { sectionResolvers, sectionTypeDefs } = require("./section");
const sectionModel = require("./section/model");
const typeDefs = gql`
  ${cardTypeDefs}
`;

const customResolvers = {
  Section: {
    cards(parent, args, cxt) {
      return cxt.card.getCardBySectionId(parent._id);
    },
  },
};
const resolvers = merge(
  sectionResolvers,
);
const MONGO_USER = process.env.MONGO_USER || "root";
const MONGO_PASS = process.env.MONGODB_PASS;
mongoose
  .connect(
    `mongodb://${MONGO_USER}:${MONGO_PASS}@ds131902.mlab.com:31902/trello-hooks-graphql-clone`,
    { useNewUrlParser: true, useUnifiedTopology: true }
  )
  .then(() => {
    console.log("mongodb connected successfully");
    const server = new ApolloServer({
      typeDefs,
      resolvers,
      context: () => ({
        section: sectionModel
      }),
    });
    const app = express();
    server.applyMiddleware({ app });
    const httpServer = createServer(app);
    server.installSubscriptionHandlers(httpServer);
    const PORT = process.env.PORT || 4444;
    httpServer.listen({ port: PORT }, () => {
      console.log(`Server is running in port ${PORT}`);
    });
  })
  .catch((err) => {
    console.log(err);
  });
```

点击此处查看完整的截面实体[。](https://github.com/ganeshmani/react-hooks-graphql-trello-clone/tree/master/server/section)

## 卡实体

卡的功能类似于第节。

![card entity.](img/a09ce97d109e98c60817efa93566ea25.png)

### 模型

```
const Mongoose = require("mongoose");
const cardSchema = new Mongoose.Schema(
  {
    title: {
      type: String,
      required: true,
    },
    label: {
      type: String,
      required: true,
    },
    description: String,
    pos: {
      type: Number,
      required: true,
    },
    sectionId: {
      type: Mongoose.Schema.Types.ObjectId,
      ref: "Section",
    },
  },
  { timestamps: true }
);
class Card {
  static insertCard(cardInfo) {
    const card = this(cardInfo);
    return card.save();
  }
  static getCardBySectionId(sectionId) {
    return this.find({ sectionId }).sort("pos").exec();
  }
  static updatePos(cardId, pos, sectionId) {
    return this.findOneAndUpdate(
      {
        _id: Mongoose.mongo.ObjectID(cardId),
      },
      {
        $set: {
          pos,
          sectionId,
        },
      }
    ).exec();
  }
}
cardSchema.loadClass(Card);
module.exports = Mongoose.model("Card", cardSchema);
```

其他功能类似于截面实体。你可以在这里查看完整的源代码。

## 捐款

我们来到了 GraphQL 服务器中另一个有趣的话题。订阅处理 GraphQL 中的 web 套接字连接。

让我们使用订阅为我们的 GraphQL 服务器实现 web 套接字。就像查询和变异一样，订阅是 GraphQL 中的一个根类型:

```
const { PubSub } = require("apollo-server");

const typeDefs = gql`
type Subscription {
    sectionAdded: Section
    cardAdded: Card
    onSectionPosChange: Section
    onCardPosChange: Card
  }
`

const SubscriptionsResolvers = {
  Subscription: {
    sectionAdded: {
      subscribe: () =>
        pubsub.asyncIterator([SUBSCRIPTION_CONSTANTS.SECTION_ADDED]),
    },
    cardAdded: {
      subscribe: () =>
        pubsub.asyncIterator([SUBSCRIPTION_CONSTANTS.CARD_ADDED]),
    },
    onSectionPosChange: {
      subscribe: () =>
        pubsub.asyncIterator([SUBSCRIPTION_CONSTANTS.ON_SECTION_POS_CHANGE]),
    },
    onCardPosChange: {
      subscribe: () =>
        pubsub.asyncIterator([SUBSCRIPTION_CONSTANTS.ON_CARD_POS_CHANGE]),
    },
  },
};

const resolvers = merge(
  cardResolvers,
  sectionResolvers,
  SubscriptionsResolvers
);
```

一旦我们在`server.js`中定义了订阅，我们需要在适当的变化中调用 pub sub。

例如，在`insertSection.js`文件中，我们需要调用发布者来使订阅在我们的服务器上工作。

```
cxt.publisher.publish(cxt.SUBSCRIPTION_CONSTANTS.CARD_ADDED, {
      cardAdded: card,
    });
```

注意:我们将发布者依赖注入到解析器中，就像我们在模型中所做的一样。

完整的服务器代码可以在[这里](https://github.com/ganeshmani/react-hooks-graphql-trello-clone/tree/master/server)找到。

## 前端

让我们开始构建应用程序中更有趣的部分。

```
npx create-react-app trello-clone
```

### 属国

*   [@ Apollo/React-Hooks](https://www.apollographql.com/docs/react/api/react-hooks/)–使用 React Hooks 处理 GraphQL 操作，例如查询、变异和订阅。
*   [GraphQL](https://www.npmjs.com/package/graphql)–需要在客户端运行 graph QL。
*   [react-smooth-dnd](https://www.npmjs.com/package/react-smooth-dnd)–处理 React 应用程序中发生的所有拖放操作。
*   [样式化组件](https://styled-components.com/)–我们将在应用程序中使用样式化组件。

```
npm install @apollo/react-hooks apollo-link apollo-link-http apollo-link-ws graphql graphql-tag react-smooth-dnd
```

查看这篇[文章](https://blog.logrocket.com/comparing-hooks-libraries-for-graphql/)，了解如何在 React 应用程序中设置 GrapQL 挂钩:

```
import React from "react";
import "./App.css";
import Board from "./components/Board/Board";
import { ApolloProvider } from "@apollo/react-hooks";
import { HttpLink } from "apollo-link-http";
import { split } from "apollo-link";
import { getMainDefinition } from "apollo-utilities";
import { InMemoryCache } from "apollo-cache-inmemory";
import ApolloClient from "apollo-client";
import { WebSocketLink } from "apollo-link-ws";
const httpLink = new HttpLink({
  uri: "http://localhost:4444/graphql",
});
const wsLink = new WebSocketLink({
  uri: "ws://localhost:4444/graphql",
  options: {
    reconnect: true,
  },
});
const link = split(
  ({ query }) => {
    const { kind, operation } = getMainDefinition(query);
    return kind === "OperationDefinition" && operation === "subscription";
  },
  wsLink,
  httpLink
);
const client = new ApolloClient({
  link,
  cache: new InMemoryCache(),
});
function App() {
  return (
```

React 中的组件

![board-component-trello.png](img/ef91a59749188c6d50d2e9b9a9639626.png)

```
  );
}
export default App;
```

### 让我们创建电路板组件:

在上面的代码中，cards 容器功能以及 Add Section 按钮将触发 GraphQL 中的变异。

一旦添加了一个部分，subscription 将返回数据并将其添加到我们在组件中定义的 Boards 状态。

```
import React, { useState, useEffect } from "react";
import CardContainer from "./Cards/CardsContainer";
import { IoIosAdd } from "react-icons/io";
import sortBy from "lodash/sortBy";
import {
  BoardContainer,
  CardHorizontalContainer,
  AddSectionDiv,
  AddSectionForm,
  AddSectionLink,
  AddSectionLinkSpan,
  AddSectionLinkIconSpan,
  AddSectionInput,
  ActiveAddSectionInput,
  SubmitCardButtonDiv,
  SubmitCardButton,
  SubmitCardIcon,
} from "./board.styles";
const Board = () => {
  const [isAddSectionInputActive, setAddSectionInputActive] = useState(false);
  const [addSectionInpuText, setAddSectionInputText] = useState("");
  const [boards, setBoards] = useState([]);

  const onAddSectionSubmit = () => {
    if (addSectionInpuText) {
      //Add Section Logic comes here
    }
  };
  return (
    <BoardContainer>
      //Card Container Logic Comes Here
      <AddSectionDiv onClick={() => setAddSectionInputActive(true)}>
        <AddSectionForm>
          {isAddSectionInputActive ? (
            <React.Fragment>
              <ActiveAddSectionInput
                onChange={(e) => setAddSectionInputText(e.target.value)}
              />
              <SubmitCardButtonDiv>
                <SubmitCardButton
                  type="button"
                  value="Add Card"
                  onClick={onAddSectionSubmit}
                />
                <SubmitCardIcon>
                  <IoIosAdd />
                </SubmitCardIcon>
              </SubmitCardButtonDiv>
            </React.Fragment>
          ) : (
            <React.Fragment>
              <AddSectionLink href="#">
                <AddSectionLinkSpan>
                  <IoIosAdd size={28} />
                  Add another list
                </AddSectionLinkSpan>
              </AddSectionLink>
              <AddSectionInput />
            </React.Fragment>
          )}
        </AddSectionForm>
      </AddSectionDiv>
    </BoardContainer>
  );
};
export default Board;
```

让我们添加变异和初始获取查询，并在组件中导入 React 挂钩:

之后，定义查询、变异和订阅:

一旦它被定义，让我们对 GraphQL 操作使用 React 钩子。

```
import { useMutation, useSubscription, useQuery } from "@apollo/react-hooks";
import gql from "graphql-tag";
```

`useQuery`返回负载、错误和数据值，我们可以用它们来显示状态。

```
const BOARD_QUERY = gql`
  query {
    fetchSections {
      id
      title
      label
      pos
      description
      cards {
        id
        title
        label
        description
        pos
      }
    }
  }
`;
const BOARD_SUBSCRIPTION = gql`
  subscription {
    sectionAdded {
      id
      title
      label
      description
      pos
      cards {
        id
        title
        label
        pos
        description
      }
    }
  }
`;
const ADD_SECTION = gql`
  mutation AddSection($title: String!, $label: String!, $pos: Int!) {
    insertSection(request: { title: $title, label: $label, pos: $pos }) {
      title
      description
      id
      label
    }
  }
`;
```

一旦我们得到数据，我们就在状态中设置它，并在组件中呈现它。

```
const { loading, error, data } = useQuery(BOARD_QUERY);
```

现在，让我们在组件中添加`useMutation`挂钩:

一切看起来都很棒。但是，当我们添加该部分时，它只会在用户刷新页面后才会反映出来。我们可以通过向我们的应用程序添加订阅来解决这个问题:

```
 useEffect(() => {
    if (data) {
      setBoards(data.fetchSections);
    }
  }, [data]);

//render
 {boards.length > 0 &&
    boards.map((item, index) => (
      <CardContainer item={item} key={index} boards={boards} />
  ))}
```

我们已经成功地将 API 添加到组件中。让我们看看如何将拖放集成到我们的应用程序中。

```
  const [AddSection, { insertSection }] = useMutation(ADD_SECTION);

  //when use clicks the add Section button
const onAddSectionSubmit = () => {
    if (addSectionInpuText) {
      AddSection({
        variables: {
          title: addSectionInpuText,
          label: addSectionInpuText,
          pos:
            boards && boards.length > 0
              ? boards[boards.length - 1].pos + 16384
              : 16384,
        },
      });
    }
  };
```

拖放

```
  const { data: { sectionAdded } = {} } = useSubscription(BOARD_SUBSCRIPTION);

//useEffect will be called whenever Subscriptions is called

useEffect(() => {
    if (sectionAdded) {
      setBoards(boards.concat(sectionAdded));
    }
  }, [sectionAdded]);
```

首先，将所需的库导入我们的组件:

### 之后，用 Dnd 容器包装我们的组件:

查看此[回购](https://github.com/kutlugsahin/react-smooth-dnd)以了解更多关于功能和道具的信息。

```
import { Container } from "react-smooth-dnd";
```

一旦我们包装了我们的组件，我们需要将一个函数绑定到`onDrop` props。

```
<Container
        orientation={"horizontal"}
        onDrop={onColumnDrop}
        onDragStart={() => {
          console.log("on drag start");
        }}
        getChildPayload={(index) => {
          return boards[index];
        }}
        dragHandleSelector=".column-drag-handle"
        dropPlaceholder={{
          animationDuration: 150,
          showOnTop: true,
          className: "cards-drop-preview",
        }}
      >
    //Our Component comes here
   </Container>
```

有趣的部分来了:一旦我们得到了`removedIndex`、`addedIndex`和`payload`，我们就使用一个效用函数来计算更新后的位置。让我们看看它是如何工作的。

Trello POS 的工作原理

```
const onColumnDrop = ({ removedIndex, addedIndex, payload }) => {
    if (data) {
      let updatePOS = PosCalculation(
        removedIndex,
        addedIndex,
        data.fetchSections
      );
      let newBoards = boards.map((board) => {
        if (board.id === payload.id) {
          return { ...board, pos: updatePOS };
        } else {
          return board;
        }
      });
      let sortedBoards = sortBy(newBoards, [
        (board) => {
          return board.pos;
        },
      ]);
      setBoards([...sortedBoards]);

      //Graphql Mutation Comes Here
    }
  };
```

![Diagram for moving a Trello card from right to left.](img/0cf4c06504c2cc676686384f3f973d8d.png)

### 如果它从左向右移动，逻辑将是:

![Diagram of a Trello card from left to right.](img/c302d978021f66a6ff1e6efcf140857a.png)

让我们编写 POS 计算的功能:

之后，我们用一个新的 pos 更新状态，并调用一个 GraphQL API 来更新我们的服务器。

卡片组件

```
export default function (removedIndex, addedIndex, arr) {
  let pos;
  if (addedIndex === arr.length - 1) {
    pos = arr[arr.length - 1].pos + 16384;
  } else if (addedIndex === 0) {
    pos = arr[0].pos / 2;
  } else if (addedIndex < removedIndex) {
    let beforePOS = arr[addedIndex - 1].pos;
    let afterPOS = arr[addedIndex].pos;
    pos = (beforePOS + afterPOS) / 2;
  } else if (addedIndex > removedIndex) {
    let beforePOS = arr[addedIndex + 1].pos;
    let afterPOS = arr[addedIndex].pos;
    pos = (beforePOS + afterPOS) / 2;
  }
  return pos;
}
```

卡片组件和拖放的功能将类似于我们在纸板组件中所做的。我会让你们把它当作一个练习，并在下面的评论中展示你们的进步。

### 不过作为参考，看看这个[回购](https://github.com/ganeshmani/react-hooks-graphql-trello-clone)。

结论

正如我在本文开头所说，克隆一个应用程序将是学习一项技术/概念的好方法。试着自己重建它，并在下面的评论中告诉我进展如何。

## 实践可以让你成为更好的开发者和问题解决者。

编码快乐！

点击查看完整源代码[。](https://github.com/ganeshmani/react-hooks-graphql-trello-clone)

使用 LogRocket 消除传统反应错误报告的噪音

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

## 自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

automatically aggregates client side errors, React error boundaries, Redux state, slow component load times, JS exceptions, frontend performance metrics, and user interactions. Then LogRocket uses machine learning to notify you of the most impactful problems affecting the most users and provides the context you need to fix it.

Focus on the React bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/react-signup-issue-free).