# 用毛毛雨和 React 写 DApp 前端-日志火箭博客

> 原文：<https://blog.logrocket.com/using-drizzle-react-write-dapp-frontends/>

框架通过提供构建任何前端应用程序所需的初始代码/设置和工具，使得引导和配置 web 项目变得更加容易和快速。区块链和 DApp 的世界发展也不例外。

在本教程中，我们将向您展示如何使用毛毛雨框架在以太坊上构建前端应用程序。我们将检查一个实际的例子，其中我们将使用毛毛雨和反应建立一个 DApp。

以下是我们将要介绍的内容:

为了轻松理解我们将在本教程中涉及的概念，您需要以下基本知识:

## 毛毛雨是什么？

[毛毛雨](https://www.trufflesuite.com/docs/drizzle/overview)以[松露组曲](https://blog.logrocket.com/develop-ethereum-smart-contracts-truffle-suite/)为框架。它是一个 JavaScript 库，为 Web3、帐户和契约实例化以及附加的契约功能提供接口，并帮助连接必要的同步。

毛毛雨是主要的图书馆。它包含了毛毛雨的核心逻辑，并与您的应用程序手动集成。

毛毛雨可以作为 npm 包安装，这意味着您需要在本地机器上安装 Node.js，以便通过终端使用 Node.js 命令来安装毛毛雨。

或者，你可以使用[块菌盒](https://www.trufflesuite.com/boxes)来处理安装。

## 毛毛雨模块

核心库为 Web3 和智能合约中的前端应用程序提供了一个反应式存储。反应商店的形式类似于 [Redux](https://blog.logrocket.com/why-use-redux-reasons-with-clear-examples-d21bffd5835/) 商店，这意味着当它检测到智能合同或 UI 的状态更新时，它会自动更新。我们可以将毛毛雨整合到任何基于 JavaScript 的项目中

有两个主要模块可以和毛毛雨一起安装。这些模块提供了使构建 DApps 更容易的接口。

*   [毛毛雨-反应](https://github.com/trufflesuite/drizzle-react-legacy)帮助你用毛毛雨轻松启动一个反应应用。它提供了一个`DrizzleProvider`组件和`drizzleConnect`帮助器方法，使毛毛雨与 React 应用程序的连接更加容易。这些是高阶的`Provider`组件，帮助包装应用程序中的其他组件，并将毛毛雨状态传递给它们
*   [毛毛雨 React 组件](https://www.trufflesuite.com/docs/drizzle/react/react-components)提供了常用 DApp 函数特有的有用 React 组件。诸如`ContractData`、`ContractForm`和`LoadingContainer`的 DApp 函数有助于连接我们的账户，轻松地从智能合约读取数据，并将数据写入区块链

在下一节中，我们将在 React 应用程序中利用上述两个模块以及毛毛雨核心库构建一个 DApp。

## 毛毛雨如何与 React 集成

在我们的毛毛雨应用程序中，我们使用 Web3 提供者连接到以太坊区块链。然后，使用配置文件，我们将应用程序配置为与我们希望它连接到的智能合约进行交互。

毛毛雨设置并管理一个内部状态，该状态存储从智能契约中的函数或表达式返回的数据。毛毛雨管理这种状态，并使其保持最新。然后，通过 React 和 climb-React，我们在状态改变时更新/重新呈现 UI，使 UI 与智能合约的状态保持同步。

毛毛雨使用 [React 的上下文 API](https://blog.logrocket.com/react-context-api-deep-dive-examples/) 的概念来实现通过整个应用程序的组件树向下传递数据。这样，我们的应用程序的上下文对于我们应用程序的每个工作部分都是可用的。

使用毛毛雨，我们可以设置并集成在本地开发环境(本地机器)上编写的智能契约到我们的前端，或者通过它的公共地址和 JSON 接口将任何智能契约导入到我们的前端。在本地开发期间运行`compile`和`migrate`基于 Truffle 的命令构建和部署智能合约，然后为我们提供包含 JSON 接口的`build/contracts`文件夹。

对于本教程，我们将重点关注构建 DApp 的前端，以及如何将其与智能合约或 Etherscan.io 上的现有智能合约集成

## 如何建立一个前端 DApp 使用毛毛雨和反应

现在让我们通过使用 Truffle 框架从头启动我们的项目来设置我们的本地开发环境。我们将安装和使用 Truffle box 以及 [ganache-cli](https://blog.logrocket.com/develop-test-deploy-smart-contracts-ganache/) 工具，这使我们能够设置本地区块链，而无需运行我们自己的实际以太坊节点。

在终端上运行以下命令，安装上述必要的工具。然后，创建一个名为`drizzle-dapp`的文件夹，把目录换到里面，初始化一个前端 DApp。

```
npm install -g truffle
npm install -g ganache-cli
mkdir drizzle-react-tutorial
truffle unbox drizzle
ganache-cli -b 3

```

命令`truffle unbox drizzle`创建一个完整的代码库，其中包含构建我们的项目所需的所有样板代码。我们将关注于`/app`文件夹，因为这是前端所在的位置。

![Boilerplate Codebase](img/218a6e99d4d85605ffd2b86f4f98f1a0.png)

上述命令还会在我们的本地计算机上全局安装 ganache-cli。`ganache-cli -b 3`命令模拟真实的区块链，在我们的前端和智能合约之间建立 DApp 账户集成。

### 在 React 中初始化毛毛雨和`DrizzleStore`

接下来，让我们在`/app/src/index.js`文件中为我们的 DApp 导入并初始化毛毛雨:

```
import { Drizzle, generateStore } from "drizzle";
import { DrizzleContext } from "drizzle-react";

import SimpleStorage from "./../build/contracts/SimpleStorage.json";

const options = { contracts: [SimpleStorage] };
const drizzleStore = generateStore(options);
const drizzle = new Drizzle(options, drizzleStore);

ReactDOM.render(
  <DrizzleContext.Provider drizzle={drizzle}>
      <App />
  </DrizzleContext.Provider>,
document.getElementById("root"));

```

在上面的代码中，我们导入了在`drizzle`和`drizzle-react`上公开的功能，并导入了从`truffle unbox drizzle`命令提供的起始代码中可用的样本契约。

同样，使用上面的代码，我们初始化了一个 store、`drizzleStore`和一个`drizzle`对象。

接下来，让我们编辑并更新`/app/src/App.js`文件:

```
import { DrizzleContext } from "@drizzle/react-plugin";
import { Drizzle } from "@drizzle/store";
import drizzleOptions from "./drizzleOptions";
import MyComponent from "./MyComponent";
import "./App.css";

const drizzle = new Drizzle(drizzleOptions);
const App = () => {
  return (
    <DrizzleContext.Provider drizzle={drizzle}>
      <DrizzleContext.Consumer>
        {drizzleContext => {
          const { drizzle, drizzleState, initialized } = drizzleContext;
          if (!initialized) {
            return "Loading..."
          }
          return (
            <MyComponent drizzle={drizzle} drizzleState={drizzleState} />
          )
        }}
      </DrizzleContext.Consumer>
    </DrizzleContext.Provider>
  );
}
export default App;

```

从上面的代码中，毛毛雨上下文提供者在前面的对象旁边提供了一个额外的对象，一个`initialised`状态。这告诉我们什么时候毛毛雨被初始化并连接到一个 web3 提供者。

一旦状态`initialised`可用或完全连接，我们就将它传递给我们的定制组件`MyComponent`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 在其他组件中消耗`DrizzleContext`

现在，让我们打开`MyComponent`组件所在的`/app/src/MyComponent.js`文件。在这里，我们将消耗通过`DrizzleContext`对象提供的所有道具。

```
import { newContextComponents } from "@drizzle/react-components";
...
const { AccountData, ContractData, ContractForm } = newContextComponents;

export default ({ drizzle, drizzleState }) => {
  return (
    <div className="App">
      <div className="section">
        <h2>Active Account</h2>
        <AccountData
          drizzle={drizzle}
          drizzleState={drizzleState}
          accountIndex={0}
          units="ether"
          precision={3}
        />
      </div>
      <div className="section">
        <h2>SimpleStorage</h2>
        <p>
          This shows a simple ContractData component with no arguments, along with a form to set its value.
        </p>
        <p>
          <strong>Stored Value: </strong>
          <ContractData
            drizzle={drizzle}
            drizzleState={drizzleState}
            contract="SimpleStorage"
            method="storedData"
          />
        </p>
        <ContractForm drizzle={drizzle} contract="SimpleStorage" method="set" />
      </div>
    </div>  
  )
}

```

在上面的代码中，我们只是将 DApp 的帐户与毛毛雨集成在一起，以管理帐户数据。这也适用于`ContractData`对象。顾名思义，这是毛毛雨和样本契约`SimpleStorage.sol`之间的数据交互。

### `CacheCall`和`CacheSend`功能

现在我们已经在前端设置了毛毛雨，让我们使用作为`drizzle`上的接口可用的`cacheCall`和`cacheSend`函数来调用智能合约。该功能还可以访问`drizzleStore`来存储来自商店内智能合同的响应。

接下来，让我们定义一个函数来发送合同数据并将响应存储在 DApp 的州中。

```
...
export default ({ drizzle, drizzleState }) => {
  ...
  const fetchData = () => {
    const state = drizzle.store.getState();
    const key = drizzle.contracts.SimpleStorage.methods.storedData.cacheCall();

    return state.contracts.SimpleStorage.methods.storedData[key].value;
  }
  ....
}
...

```

上面的代码片段使用`cacheCall`方法简单地调用了智能契约`SimpleStorage`。该调用返回将存储在`drizzleStore`和`key`中的响应数据，用于从`drizzleStore`访问该数据。

然后，使用`drizzleStore`上的`key`,上面的函数返回智能合约调用 UI 时可用的`value`。这样，我们可以轻松地调用我们的智能契约，并将响应与 UI 同步。

接下来，让我们定义另一个函数来发送事务并将响应存储在我们的存储中:

```
...
export default ({ drizzle, drizzleState }) => {
  ...
  cnnst state = drizzle.store.getState();
  const id = drizzle.contracts.SimpleStorage.methods.set.cacheSend(
    3,
    { from: Addresse }
  );

  if (state.transactionStack[id]) {
    const transactionHash = state.transactionStack[id];
    return state.transactions[transactionHash].status;
  }
  ...
}
...

```

上面的代码片段检索应用程序的状态，然后初始化一个事务，同时为它提供`gas`和`from`选项。`from`代表进行交易的`address`。调用`cacheSend`方法返回一个`id`，这个`id`用于访问 DApp 状态上的事务状态。

现在，我们已经将 DApp 的前端连接到智能合约。在终端上，运行以下代码来编译和迁移智能合约:

```
truffle compile # compiles all the smart contract within the /contracts folder
truffle migrate # deploys the smart contract on the ganache-cli blockchain

```

现在，我们已经准备好了智能合约和应用程序的前端。我们应该能够在输入字段中键入一个值并进行交易，然后我们的智能合约的状态将立即自动更新，UI 也是如此。`ContractForm`组件为我们提供了与智能契约交互的输入和表单。

## 结论

在本教程中，我们引入了毛毛雨作为 Truffle 套件的框架，它可以轻松地将我们的 DApp 与我们的智能合约状态同步在一起。然后，我们探讨了在 DApp 中安装和使用毛毛雨的三种方法。我们还讲述了如何在我们的应用程序中设置 Drizzzle 上下文。

毛毛雨有助于减少引导 DApp 项目所需的样板文件的数量。下一步，我们可以扩展我们的 DApp 来构建任何东西，从游戏到购物清单或博客平台，都在区块链平台上。

您可以通过构建更多的组件来扩展本教程中使用的代码，以便针对您希望纳入 DApp 的功能进行呼叫或发送交易。然后，扩展智能合约来处理这些功能。本教程中使用的代码可以在 [GitHub](https://github.com/IkehAkinyemi/Drizzle-react) 上获得。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。