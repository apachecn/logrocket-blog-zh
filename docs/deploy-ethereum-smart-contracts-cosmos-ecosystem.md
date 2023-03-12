# 在宇宙生态系统上部署以太坊智能合约

> 原文：<https://blog.logrocket.com/deploy-ethereum-smart-contracts-cosmos-ecosystem/>

我们中的许多人使用像脸书这样的网站与朋友和爱人联系，在网上分享我们生活中的时刻，让他们看到并回应。然而，脸书是唯一控制其服务器上存储的数据。因此，许多用户觉得他们实际上是在向脸书提供他们的个人数据，脸书可以选择编辑、限制或删除他们共享的数据。

然而，区块链和 [Web3 通过去中心化](https://blog.logrocket.com/best-practices-web3-providers/#web3-benefits)解决了这些问题，有效地消除了政府和组织的这种权力，确保这些数据的所有权仍然属于用户。

在本教程中，我们将[探索区块链框架 Cosmos SDK](https://cosmos.network/) ，我们将学习如何将为以太坊编写的智能合约部署到 Cosmos 区块链。我们开始吧！

## 宇宙

Cosmos 是一个用于构建区块链的 SDK。有了 Cosmos，我们可以编写自定义的区块链，在测试网中测试区块链，在主网上启动区块链，并连接到其他 Cosmos 区块链。

Cosmos 是开源的，这意味着 SDK 是由 Cosmos 开发者社区构建和维护的。用 Go 编程语言编写的 Cosmos 是模块化的，可扩展的，并可与其他 Cosmos 区块链互操作。

## 在 Cosmos 上部署智能合同

你可以在宇宙区块链上部署用 Solidity 写的智能合约，包括 ERC-20 代币。因此，您可以在宇宙区块链上部署您为区块链以太坊编写的智能合约。

在本节中，我们将通过部署到 [Evmos](https://evmos.dev/intro/overview.html) 来学习如何在宇宙区块链上部署智能合约。Evmos 是一个可扩展的区块链，使用运行在 [Tendermint Core](https://tendermint.com/core/) consensus 引擎上的 Cosmos SDK 构建。

Evmos 兼容 EVM 和 Web3，具有快速事务，并实现了 Tendermint 核心。我们将[使用 Truffle](https://blog.logrocket.com/truffle-suite-tutorial-develop-ethereum-smart-contracts/) 编写我们的智能契约，但是首先，我们必须安装 Truffle 实用工具。

## 创建一个松露项目

通过运行以下命令安装 Truffle 工具:

```
npm install -g truffle
```

要创建新的 Truffle 项目，请运行以下命令:

```
mkdir cosmoprj
cd cosmoprj
truffle init

```

![Install Truffle Code](img/ddd5d047f74f2d1b26132c879cd04926.png)

![Truffle Install Success](img/0729d6adbaa567bcc6f866e4acdab17e.png)

我们的项目将具有以下结构:

```
cosmoprj/
├── /contracts
├─── /Migrations.sol
├── /migrations
├─── /1_initial_migration.js
├── /test
├── package.json
└── truffle-config.js

```

*   包含我们的迁移脚本
*   包含我们的智能合同
*   `package.json`:包含我们的依赖
*   包含我们的块菌配置
*   包含我们的测试脚本

## 创建一份`Hello`智能合同

我们将创建一个名为`Hello`的智能契约，它将打印消息`Hello World`。运行下面的命令创建一个合同`Hello`文件:

```
truffle create contract Hello

```

现在，我们将在我们的`contracts`文件夹中看到一个`Hello.sol`文件。打开文件并添加以下代码:

```
// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.9.0;

contract Hello {
  function sayHello() public pure returns (string memory)  {
    return "Hello, world!";
  }
}

```

我们有一个名为`sayHello`的函数，它返回一个字符串。`public`关键字表示这个函数可以被任何人调用，`pure`关键字表示这个函数不会修改任何状态。

## 添加 Evmos 配置

现在，我们将智能合约部署到 Evmos Testnet。Evmos 为[提供了许多我们可以使用的测试网](https://evmos.dev/testnet/join.html)，但是对于本教程，我们将使用`Evmos Testnet`测试网。我们将在我们的`truffle-config.js`文件中配置这个测试网络:

```
const HDWalletProvider = require("@truffle/hdwallet-provider");

const mnemonic =
  "candy maple cake sugar pudding cream honey rich smooth crumble sweet treat";

module.exports = {
  networks: {
    evmos: {
      provider: () =>
        new HDWalletProvider(mnemonic, "https://eth.bd.evmos.dev:8545"),
      network_id: 9000,
      chain_id: 9000,
    },
  },

  // Set default mocha options here, use special reporters etc.
  mocha: {
    // timeout: 100000
  },

  // Configure your compilers
  compilers: {
    solc: {
      version: "0.8.13", // Fetch exact
    },
  },
};

```

我们需要`[@truffle/hdwallet-provider](https://www.npmjs.com/package/@truffle/hdwallet-provider)`包，它允许我们连接到 Evmos Testnet。`@truffle/hdwallet-provider`包也是一个支持高清钱包的 Web3 提供商，用于为从 12 个单词的助记符中导出的地址签署交易。`mnemonic`变量是 12 个单词的助记符，我们用它来生成我们的私钥，并由此生成我们的地址。

我们将`"https://eth.bd.evmos.dev:8545"`作为提供者 URL 传递，这是 Evmos Testnet 的 URL。

`network_id`是我们正在连接的网络的 ID，`chain_id`是我们正在连接的链的 ID。我们使用`solc`属性来配置 Solidity 编译器；我们用的是版本`0.8.13`。

接下来，让我们安装`@truffle/hdwallet-provider`包:

```
npm install --save @truffle/hdwallet-provider

```

## 编译我们的智能合同

让我们使用`truffle compile`命令来编译我们的智能契约:

```
truffle compile
```

![Truffle Compile Smart Contract](img/7ccec7b144fe2e60f033469d5654a9e4.png)

我们将看到生成了一个新的`build`文件夹，其中包含已编译的智能契约。编译后的智能合同存储在`build/contracts`文件夹中。

在文件夹里，我们会看到`Hello.json`和`Migrations.json`。这些文件是编译后的智能合约，它们包含智能合约的 ABI 和字节码。

## 部署我们的智能合同

完成后，我们可以部署我们的智能合同。我们将使用`truffle migrate`命令:

```
truffle migrate --network evmos

```

上面的代码将我们的智能合约部署到 Evmos Testnet。看到我们使用了`--network`标志来指定我们想要部署到的网络，在我们的例子中，是`evmos`网络。我们将在控制台中看到以下内容:

![Connect Evmos Truffle Smart Contract](img/70ce348a12a7d28dfd586ee233d72cec.png)

![Evmos Block Number Output](img/50950da051718c1f28b86c9a48488548.png)

我们的块号是`805055`，它是被执行的事务的块号。我们将使用这个块号来查询智能合同。

让我们在 Evm 浏览器 [`https://evm.evmos.dev/blocks/805055`](https://evm.evmos.dev/blocks/805055) 中查看这个块:

![Evmos Explorer Block Number View](img/25954f31e314447de7b6a86ba2bf208e.png)

![Evmos Explorer Block Details Cont](img/79041264ecfcb8d150a64579a5b919ba.png)

![Evmos Explorer Block Detail](img/1f3fed947e3dd102fdf601883aa34012.png)

## 结论

Cosmos 和 Evmos 的一个好处是它们都与以太坊区块链兼容。我们可以将我们的智能合约移植到宇宙区块链，并在没有任何配置的情况下轻松部署到 Evmos 测试网。

在本文中，我们学习了什么是 Cosmos，并回顾了如何使用 Truffle 创建智能合同。最后，我们学习了如何将我们的智能合约部署到 Evmos Testnet。我希望你喜欢这篇文章，快乐编码！

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。