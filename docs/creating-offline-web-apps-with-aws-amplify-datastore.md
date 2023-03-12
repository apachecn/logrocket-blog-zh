# 用 AWS Amplify DataStore 创建离线 web 应用程序

> 原文：<https://blog.logrocket.com/creating-offline-web-apps-with-aws-amplify-datastore/>

在当今世界，似乎每个人都是联系在一起的。你在家、在工作、在火车上都随身带着智能手机，一切都很好。

好吧，至少直到他们不是。

甚至在我居住的地方(德国斯图加特——一个 60 万人口的城市)，也有我没有任何移动互联网连接的地方。

在市中心的一个地铁站，我的连接总是掉线。如果我不得不等待下一趟火车，我就只能用智能手机上的东西了，其他什么也做不了。

不一定非要这样。

借助 DataStore library——面向前端开发人员的 Amplify 无服务器框架的最新成员——您现在可以通过几行代码为您的移动应用添加离线功能。

在本文中，我将向您展示如何将 AWS Amplify 添加到 React 项目中，并在 AWS 上启用离线功能以及与云后端的同步。

这些要点将用一个简单的杂货清单应用程序来说明。

## 先决条件

Amplify CLI 在后台使用 [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) ，因此您需要在开始之前对其进行正确设置。

你还需要 Node.js 版本 12 和 NPM 版本 6。

我使用了 [Cloud9 IDE](https://console.aws.amazon.com/cloud9/) ，因为它预装了 AWS CLI、Node.js 和 NPM。

### CLI 安装

首先，你需要安装两个 CLI:`create-react-app`和`amplify`。

```
$ npm i -g create-react-app @aws-amplify/cli
```

我们在这里使用 create-react-app CLI，因为它允许您非常容易地与服务人员一起设置 react 应用程序。当没有互联网连接时，服务人员需要在浏览器中显示该应用程序。

离线时在浏览器中显示页面只是工作的一半。您在使用应用程序时对数据所做的更新需要保存在某个地方，稍后再同步回您的后端。

这就是 Amplify 框架发挥作用的地方。它允许您在 CLI 的帮助下创建 AWS 支持的无服务器后端，并通过 JavaScript 库简化您的前端到这些后端服务的连接。

### 项目设置

如果您正在使用 Cloud9 并希望使用它的本地 AWS 概要文件，您必须向 AWS 概要文件添加一个符号链接。Cloud9 创建并管理一个`credentials`文件，但是 Amplify 搜索一个配置文件。

```
$ ln -s ~/.aws/credentials ~/.aws/config
```

要初始化 React 项目并向其中添加 Amplify 服务，请使用以下命令:

```
$ create-react-app grocerize
$ cd grocerize
$ amplify init
```

Amplify 的 init 命令会问你几个问题。您可以使用默认值回答所有这些问题—只有 environment 命令需要一些输入。这里可以用“dev”。

它将在云中为您创建部署基础架构。

在这之后，您应该有一个名为 grocerize 的 React 项目目录。在它应该是一个有一些文件的放大目录。

## 添加 AWS 后端服务

你需要两个后端服务:`auth`和`api`。

这两个将允许您的用户登录，然后处理所有需要完成的同步工作，因此您的用户的数据不会留在他们的设备上。

### 添加身份验证

您可以通过将`auth`类别添加到您的 Amplify 项目来添加认证。这是通过以下命令完成的:

```
$ amplify add auth
```

同样，您可以使用该命令的默认值。它将以 CloudFormation 模板的形式创建基础架构代码。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这些将在以后用于创建一个 Amazon Cognito 的无服务器认证服务。

## 添加 GraphQL API

GraphQL 是用于保持用户的本地数据与云同步的机制的核心。

您需要使用以下命令通过 Amplify CLI 添加支持同步的 GraphQL API:

```
$ amplify add api
```

在这里，您必须选择“GraphQL”，给 API 一个口语名称—最好使用您的 React 项目名称“grocerize”—并使用“Amazon Cognito User Pool”作为默认授权类型，这样 API 就可以使用我们在前面一步中添加的 auth 类别。

这里的下一个问题很重要:

"您想为 GraphQL API 配置高级设置吗？"

是的，你想这么做！如果不这样做，您将得到一个不支持同步的普通 GraphQL API，然后数据存储库只能在以后脱机工作。

在你回答了这个问题“是”之后，你将会被问几个额外的问题。

以下是您最终获得“支持同步的 GraphQL API”所需的答案:

```
? Configure additional auth types? No
? Configure conflict detection? Yes
? Select the default resolution strategy Auto Merge
? Do you have an annotated GraphQL schema? No
? Do you want a guided schema creation? Yes
? What best describes your project: Single object with fields...
? Do you want to edit the schema now? No
```

冲突检测是这里的重要部分。如果你想了解更多细节，你可以在 [Amplify 文档](https://aws-amplify.github.io/docs/js/datastore#conflict-resolution)中阅读。

在我们添加了`api`类别之后，Amplify CLI 创建了一些 CloudFormation 模板，我们可以使用它们来获得在云中初始化的 AWS AppSync、Lambda 和 DynamoDB 驱动的后端。

### 生成数据模型

下一步是为数据存储库生成模型。

数据存储以不同的编程语言实现，适用于不同的平台，如 Web、Android 和 iOS。

在为您使用的语言生成模型代码时，它使用 GraphQL 作为事实的来源。

首先，您需要在以下位置更新 GraphQL 模式:

```
grocerize/amplify/backend/api/grozerize/schema.graphql
```

只需用以下代码替换该文件的内容:

```
type Item @model @auth(rules: [{allow: owner}]) {
  id: ID!
  name: String!
  amount: Int!
  purchased: Boolean
}
```

该模式将创建一个单一类型，它由云中的 DynamoDB 表支持，并由 Cognitos 授权机制保护。只有它的创建者可以读取或写入它。

这种型号是我们购物清单上的一项。它有一个名字，一个数量，和一个购买状态。

要将模型作为 JavaScript 类用于 React 项目，您需要运行 Amplify 的代码生成器，如下所示:

```
$ amplify codegen models
```

之后，应该会出现一个新目录`grocerize/src/models`。它保存 JavaScript 文件，Amplify 的数据存储库以后可以使用这些文件。

## 部署后端服务

到目前为止，您只部署了 Amplify 工作所需的基础设施，即部署支持您的应用程序的实际服务。

要部署它们，只需使用以下命令:

```
$ amplify push
```

如果要求您生成 GraphQL 查询代码，您可以拒绝。我们不会直接使用 GraphQL，而是通过 DataStore 来使用，在上一步中我们已经为它生成了模型。

只有 4 个 CLI 命令，我们有一个成熟的 AWS 支持的无服务器后端。今天的技术让前端开发人员能够做的事情是不是很疯狂？

该命令需要几分钟才能完成。然后，您有了一个新的 JavaScript 文件`grocerize/src/aws-exports.js`，其中包含所有 AWS 凭证，您可以在以后使用这些凭证来配置您的前端。

## 连接前端

要连接到前端，您需要安装 Amplify JavaScript 库并从您的 GraphQL 代码生成模型。

### 安装前端库

要将 React 前端连接到新部署的无服务器后端，您需要放大 JavaScript 库。

您可以使用 npm 安装它们:

```
$ npm i @aws-amplify/core \
@aws-amplify/auth \
@aws-amplify/ui-react \
@aws-amplify/datastore
```

核心包主要包含用于连接云服务的基本 Amplify 代码。

`ui-react`包包含注册和登录的 React 组件。

它建立在`auth`包的基础上，该包执行实际的认证工作。

`datastore`包用于在本地存储数据，并通过 GraphQL 将其同步到云端。

### 更新前端代码

现在，您必须实现实际的前端代码。第一部分是`grocerize/src/index.js`。

用以下代码替换其内容:

```
import React from 'react';
import ReactDOM from 'react-dom';
import Amplify from "@aws-amplify/core";
import "@aws-amplify/auth";

import awsconfig from "./aws-exports";
import App from './App';
import * as serviceWorker from './serviceWorker';

Amplify.configure(awsconfig);

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);

serviceWorker.register();
```

那么，让我们看看这里发生了什么。

其中大部分仍然是一个基本的 React 设置。当您之前使用`amplify push`命令部署我们的基础设施时，您使用由 Amplify CLI 生成的`aws-exports.js`文件配置了 Amplify 库。

对底部的`serviceWorker.register()`的最后一次调用将使我们的 React 项目能够被渲染，即使服务器不再可达，从而使它离线可用。

下一个要更新的文件是`grocerize/src/App.js`。用以下代码替换其内容:

```
import React from "react";
import { DataStore, Predicates } from "@aws-amplify/datastore";
import { withAuthenticator } from "@aws-amplify/ui-react";
import { Item } from "./models";

class App extends React.Component {
  state = {
    itemName: "",
    itemAmount: 0,
    items: [],
  };

  async componentDidMount() {
    await this.loadItems();
    DataStore.observe(Item).subscribe(this.loadItems);
  }

  loadItems = async () => {
    const items = await DataStore.query(Item, Predicates.ALL);
    this.setState({ items });
  };

  addItem = async () => {
    await DataStore.save(
    new Item({
        name: this.state.itemName,
        amount: this.state.itemAmount,
        purchased: false,
    })
    );
    this.setState({itemAmount: 0, itemName: ""});
  }

  purchaseItem = (item) => () =>
    DataStore.save(
    Item.copyOf(item, (updated) => {
        updated.purchased = !updated.purchased;
    })
    );

  removeItem = (item) => () => DataStore.delete(item);

  render() {
    const { itemAmount, itemName, items } = this.state;

    return (
    <div style={{ maxWidth: 500, margin: "auto" }}>
        <h1>GROCERIZE</h1>
        <h2>Your Personal Grocery List</h2>
        <p>Add items to your grocery list!</p>
        <input
        placeholder="Gorcery Item"
        value={itemName}
        onChange={(e) => this.setState({ itemName: e.target.value })}
        />
        <input
        placeholder="Amount"
        type="number"
        value={itemAmount}
        onChange={(e) =>
            this.setState({ itemAmount: parseInt(e.target.value) })
        }
        />
        <button onClick={this.addItem}>Add</button>
        <h2>Groceries:</h2>
        <ul style={{listStyleType:"none"}}>
        {items.map((item) => (
            <li key={item.id} style={{fontWeight: item.purchased? 100 : 700}}>
            <button onClick={this.removeItem(item)}>remove</button>
            <input type="checkbox" checked={item.purchased} onChange={this.purchaseItem(item)}/>
            {" " + item.amount}x {item.name}
            </li>
        ))}
        </ul>
    </div>
    );
  }
}

export default withAuthenticator(App);
```

这段代码是我们的应用程序使用的唯一屏幕。

在顶部，我们导入数据存储库、从 GraphQL 模式生成的项目模型以及用于身份验证的更高阶组件。

App 组件使用数据存储在呈现时加载所有项目。

第一次呈现项目后，组件使用其`loadItems`方法订阅数据存储中任何项目的更改。

该方法将收集所有项目，并重新呈现屏幕。

`loadItems`、`addItem`、`purchaseItem`和`removeItem`方法构成了该应用的基本 CRUD 操作。编程模型非常简单，因为您在本地存储您的数据，这几乎是即时发生的，并且在后台一切都与您的云基础架构同步。

在应用程序组件被导出之前，它被打包到`withAuthenticator`高阶组件中，该组件将在用户可以与应用程序交互之前显示一个登录屏幕。

## 使用应用程序

要启动开发服务器，只需运行以下命令:

```
$ npm start
```

如果在浏览器中打开开发服务器 URL，将会提示您注册。

正如您在注册和登录过程中看到的，后端预配置了安全密码要求和电子邮件激活。这都是可以配置的。

登录后，你可以随心所欲地创建你的购物清单。

如果你在智能手机上使用这个应用程序，当你在杂货店里的互联网连接断开时，你仍然可以与你的购物清单互动，检查你买了什么，等等。

当连接稍后恢复时，您的所有更改都将同步到后端，因此您可以在另一台设备上修改它们。

## 结论

AWS Amplify 对于前端开发人员来说是一个非常强大的工具，新的数据存储及其离线和同步功能使其工作起来更加愉快。

编程模型保持简单，所有繁重的工作都由数据存储在后台完成。

在几分钟内，您可以创建一个无服务器的后端，拥有普通移动应用程序所需的一切，Amplify 使用 CloudFormation 来部署 AWS 服务的事实使其具有多功能性，经得起未来的考验。