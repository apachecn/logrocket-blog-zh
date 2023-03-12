# 五大去中心化应用开发框架

> 原文：<https://blog.logrocket.com/top-5-decentralized-app-development-frameworks/>

随着最近去中心化应用程序(DApp)的兴起，专门为简化 DApp 开发而创建的框架变得越来越普遍。

像前端开发人员的框架一样，DApp 框架是为构建软件而编写的代码片段，这有助于更容易地为区块链构建应用程序。虽然没有必要使用框架，但它确实有助于更好更快地开发软件。

在本文中，我们将比较区块链工程师通常使用的几种流行框架，并查看它们的优点、缺点和网络，包括:

但是，在我们开始之前，让我们讨论一下可用的 DApps 类型。

## 什么是 DApps？

分散式应用程序(DApps)提供了一种与 web 应用程序交互的新方式。

你可能听说过“区块链是未来”或者“未来是分散的”这种心态渗透到基于区块链的新技术的快速发布中，这意味着开发人员可以访问可靠的框架。

基于区块链技术的 DApps 运行在由几台计算机或服务器组成的对等(P2P)网络上，而不是传统的中央服务器上。这些应用程序[建立在智能合同](https://blog.logrocket.com/smart-contract-programming-languages/)和共识流程或机制的概念之上。

这意味着，当一个应用程序被分散时，它首先是用底层区块链技术创建的，然后被推送到首选的区块链网络。

请记住，区块链网络是由数千台被标识为节点或服务器的计算机组成的集合，每台计算机归一个人所有。每个节点、服务器和计算机都变成了分布式数据库，这意味着每台其他服务器都有相同数据的副本。

考虑到这一点，构建 DApp 的一些好处是，他们没有任何停机时间，因为他们没有中央服务器。

它们也不容易受到网络攻击，并且无法关闭，因为没有中央服务器，所有的服务器都是分布式的。如果一台分布式服务器受到攻击，其他服务器就会知道。

它也是开源的，对开发者透明开放。

总之，DApp 不依赖传统的中央服务器或服务提供商，如谷歌云、Heroku 或数字海洋。相反，它在区块链的部署使它变得透明，没有存储数据的中心位置，也没有第三方控制。

## DApps 的类型

不同类型的 DApp 可以通过区块链技术解决方案的不同部门看到，DApp 就是为了解决这些问题而建立的。例如，第一座 DApp [就建在以太坊区块链](https://blog.logrocket.com/ethereum-blockchain-development-using-web3-js/)上。

类似地，比特币诞生的主要原因是将金融中间人(即银行)从货币转移过程中移除。有了比特币，转账可以 P2P，不用银行。

和比特币一样，DApps 在许多其他领域也扮演着重要角色。

## 分散融资

去中心化金融(DeFi)是一个术语，指的是使用比特币或以太坊等去中心化货币来执行操作的金融服务。

传统上，我们的金融服务是通过中央机构运作的，如银行和政府，包括提供贷款、储蓄计划和保险等金融服务。

然而，许多人认为，由于欺诈、腐败和管理不善，由一个中央机构来执行这些金融业务是有风险的。这就诞生了德菲。

构建 DeFi 应用程序需要选择平台(这包括以太坊、索拉纳、币安智能合约等等)。DeFi 的主要目标是去除中间人，同时提供透明度和分散化。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

DeFi 应用程序用于分散的交换、保险和贷款服务，如 [Pancackeswap](https://pancakeswap.finance/) 和 [1inch](https://app.1inch.io/#/1/swap/ETH/DAI) 等应用程序。

### 分散自治组织

分散自治组织，或 DAO，是一个完全通过代码/智能契约运行的组织。这些组织按照给他们的规则运作，然后完全按照这些条款运作。

因此，DAO 可以在没有人类活动帮助的情况下运营公司，分散的董事会为 DAO 的运行制定规则，这意味着不需要重复的董事会会议。

一个可能的例子是无人驾驶出租车公司。在这个例子中，我们可以有一个 DAO Lyft 或 DAO 优步，它们完全在部署在任何区块链上的智能合约集合上运行。

这将简化运营，如利润发送到哪里，MakerDao 如何运营，何时何地发送出租车，何时给汽车加油等等。

### 商品管理

商品管理 DApp 通常与供应链流程和系统相关联。传统的供应链无法提供对产品的洞察力或透明度，这意味着我们并不总是知道一个产品真正是由什么制成的，或者它来自哪里。

然而，有了供应链中的区块链，产品可追溯性是可用的；您可以追踪产品的来源，因为存储在区块链中的数据是分散的。这方面的一个例子是 [GX 封锁](https://www.gxblocks.com/)。

## 比较 DApp 框架

现在让我们看看构建 DApps 最常见的框架。

### 建筑工人

Hardhat 是一个构建智能契约的框架，为专业人员提供了一个开发环境。这个开发环境允许用户编译、运行测试、检查智能合约的错误或调试，以及部署分散的应用程序。

#### 安全帽安装

要安装 Hardhat，请确保您的计算机上安装了 Node.js。然后，用`npm init -y`初始化一个项目，并运行下面的命令:

```
$ npm install -D hardhat

```

如果安装正确，使用以下命令执行 Hardhat:

```
$ npx hardhat 

888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

Welcome to Hardhat v2.0.8

? What do you want to do? …
❯ Create a sample project
  Create an empty hardhat.config.js
  Quit

```

`npx`安装时附带 Node.js，帮助执行安装在本地环境中的可执行文件。

要确保 Hardhat 在您的项目中执行，请再次运行命令:

```
$ npx hardhat
Hardhat version 2.0.8

Usage: hardhat [GLOBAL OPTIONS] <TASK> [TASK OPTIONS]

GLOBAL OPTIONS:

--config A Hardhat config file.
--emoji Use emoji in messages.
--help Shows this message, or a task's help if its name is provided
--max-memory The maximum amount of memory that Hardhat can use.
--network The network to connect to.
--show-stack-traces Show stack traces.
--tsconfig A TypeScript config file.
--verbose Enables Hardhat verbose logging
--version Shows hardhat's version.

AVAILABLE TASKS:

accounts Prints the list of accounts
check Check whatever you need
clean Clears the cache and deletes all artifacts
compile Compiles the entire project, building all artifacts
console Opens a hardhat console
flatten Flattens and prints contracts and their dependencies
help Prints this message
node Starts a JSON-RPC server on top of Hardhat Network
run Runs a user-defined script after compiling the project
test Runs mocha tests

To get help for a specific task run: npx hardhat help [task]

This same command also checks the available tasks Hardhart offers. Hardhat also offers an ecosystem of plugins, meaning there are more libraries you must install when working with Hardhat, including the following:
```

```
npm install --save-dev @nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers

```

这包括:

*   测试智能合同的插件
*   `hardhat-ethers`，允许您从前端与智能合约进行交互
*   `chai`，Node.js 测试库

#### 安全帽网络

请记住，智能合约不能像传统应用程序那样部署到中央服务器；相反，他们必须部署到以太坊区块链网络，这需要花费以太币。

因为以太坊网络是服务器，所以请确保在部署到以太坊网络(也称为 mainnet)之前测试您的智能合约。为了测试，我们需要一个本地的区块链，这是 Hardhat 通过 Hardhat Network 提供给我们的。

要启动此网络，请运行以下命令:

```
$ npx hardhat node
Started HTTP and WebSocket JSON-RPC server at http://127.0.0.1:8545/

Accounts
========
Account #0: 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266 (10000 ETH)
Private Key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

Account #1: 0x70997970c51812dc3a010c7d01b50e0d17dc79c8 (10000 ETH)
Private Key: 0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d
...

```

在您的项目文件夹中，您应该在 contracts 文件夹中看到`Greeter.sol`:

```
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;

import "hardhat/console.sol";

contract Greeter {
    string private greeting;

    constructor(string memory _greeting) {
        console.log("Deploying a Greeter with greeting:", _greeting);
        greeting = _greeting;
    }

    function greet() public view returns (string memory) {
        return greeting;
    }

    function setGreeting(string memory _greeting) public {
        console.log("Changing greeting from '%s' to '%s'", greeting, _greeting);
        greeting = _greeting;
    }
}

```

要对此进行编译，请运行以下命令:

```
npx hardhat compile

```

#### 安全帽福利

使用 Hardhat 作为您的 DApp 框架的一些最常见的好处包括。记住，这些是最常见的，你也可能有个性化的好处:

首先，Hardhat 正在积极维护中，因为它是构建分散式应用程序最常用的框架之一。

这要归功于它的开源格式和庞大的社区支持，这要归功于 GitHub 上超过 17k 的用户和在发表这篇文章时拥有 46k 成员的活跃的 Discord 社区。它在不断地发展和快速成长。

而且，[它的技术基于 JavaScript 和 TypeScript](https://hardhat.org/guides/waffle-testing.html) ，JavaScript 开发人员很容易掌握。

#### 安全帽缺点

Hardhat 的一个主要缺点是，它显然是为专业人士而不是初学者准备的，因为它的文档非常简要地介绍了如何入门。在研究 Hardhat 之前，构建 DApps 的初学者可能需要尝试其他框架，如 Truffle。

### 松露

Truffle 是在以太坊区块链上构建去中心化应用的另一个框架。它是最流行的，也是先驱框架之一。它提供了编译、部署和测试的帮助。

大多数其他框架遵循了 Truffle 的道路，如果你是 DApp 开发的新手，你很可能会遇到 Truffle，因为它的设置对初学者很友好。

它还可用于其他与挣值管理兼容的(EVM)区块链，如币安智能链、Hyperledger、Polygon 等。它还支持 Solidity 和 Vyper 语言。

而且，如果你熟悉 Node.js，这是一个优势，因为 Truffle 是用 JavaScript 编写的。

#### 块菌装置

您必须在 PC 上安装 Node.js 才能开始。安装很容易，您只需在项目中运行以下命令:

```
$ npm install -g truffle

```

接下来，在您的项目中初始化 Truffle:

```
$ truffle init

```

如果成功，您应该会看到以下内容:

```
Starting init...
================

> Copying project files to /Users/macbookpro/Desktop/Projects/dapp-truffle

Init successful, sweet!

Try our scaffold commands to get started:
  $ truffle create contract YourContractName # scaffold a contract
  $ truffle create test YourTestName         # scaffold a test

使用 Truffle、Ganache 和毛毛雨的指南，包括快速入门指南、测试说明、平台命令、部署等。

```

项目结构如下:

```
$ dapp-truffle ls -1
contracts
migrations
test
truffle-config.js

```

#### 松露网络

Truffle 提供了一个开发网络来运行和测试 DApps，而不需要部署到 mainnet，并且[作为测试](https://blog.logrocket.com/complete-guide-blockchain-testing/)的本地开发区块链。

在`truffle-config.js`文件中，网络对象带有开发和高级网络配置:

```
networks: {
  development: {
    host: "127.0.0.1",
    port: 8545,
    network_id: "*", // match any network
    websockets: true
  },
  live: {
    host: "178.25.19.88", // Random IP for example purposes (do not use)
    port: 80,
    network_id: 1,        // Ethereum public network
  }
}

```

开发配置是运行以下命令的默认网络:

```
$ truffle migrate --network

```

要编译智能合约，请使用以下命令:

```
truffle compile

```

#### 松露的好处

由于其受欢迎和广泛使用，Truffle 得到了用户的广泛支持，[在发布](https://github.com/trufflesuite/truffle)时有超过 35000 名 GitHub 用户。

而且，像 Hardhat 一样，维护是连续的，以提供积极的支持，Truffle 支持本地区块链、testnet，并且很容易部署到 mainnet。

#### 块菌的缺点

在 Truffle 中调试不像 Hardhat 那么容易，因为 Truffle 要求您创建事件并将它们放入日志中，让您寻找那个事件。而在 Hardhat 中，你在终端上有一个`console.log`提示信息，让你调试得更快。

### 从事

Embark 是 EVM 区块链的另一个框架，被认为是一个全栈框架。这意味着该框架提供了同时构建整个分散式应用程序的前端和后端的解决方案。

关注你的[坚实智能合同](https://blog.logrocket.com/complete-guide-blockchain-testing/#creating-smart-contract)和前端(HTML 和 JavaScript)代码的变化，并将它们重新部署到区块链网络。

#### 上船安装

在安装 Embark 之前，请确保您的计算机上已经安装了 Node.js。此外，Embark 还要求您安装以下软件:

虽然这些是可选的，但仍然建议安装它们。

安装 Embark 非常简单:

```
$ npm -g install embark

```

要开始使用 Embark，我们必须首先运行以下命令:

```
$ embark demo
$ cd embark_demo

```

这将创建一个 scaffold 应用程序，其中包含所有需要使用的文件夹。有了这个想法，您就可以运行应用程序来启动以太坊和 IPFS 节点。请记住，开始监视代码中的变化，以便重新部署:

```
$ embark run

```

#### Embark 网络

请记住，我们仍然必须连接到区块链网络，以便我们可以通过交互来测试它；Embark 可以与 Geth 或 Parity 等客户合作。

在`config/blockchain.js`文件中，您可以配置您想要的区块链。以下代码是 Embark 文档中的一个示例:

```
module.exports = {
  default: {
    enabled: true,
    client: "geth"
  },
  development: {  
    clientConfig: {
      miningMode: 'dev'
    }
  },
  testnet: {
    endpoint: "https://external-node.com",
    accounts: [
      {
        mnemonic: "12 word mnemonic"
      }
    ]
  }
}

```

#### Embark 的好处

Embark 来自 GitHub 社区的明星比 Hardhat 稍多，[大约有 34k 明星](https://github.com/embarklabs/embark)。它还带有一个 UI 仪表板，为那些对部署到 web 而不是控制台感兴趣的人提供了 Embark 正在进行的过程的概览。

如前所述，它是一个帮助构建全栈解决方案的框架，这是本文中提到的其他框架所不能做到的。

#### 着手劣势

根据我在尝试运行 Embark 时的观察，我在安装时遇到了一些错误。之后，当我检查当前安装的版本时，我的终端上出现了警告。

此外，这个项目看起来已经过时了，因为在撰写本文时已经有 14 个月没有人对它做出贡献了。

### 布朗尼

Brownie 是一个为 EVM 构建应用程序的框架，但它是基于 Python 的，这意味着它支持编程语言 Vyper，这是一种 Python 智能合约语言。布朗尼也支持坚固性。

与本文提到的其他框架不同，Brownie 的测试语言是使用[假设](https://github.com/HypothesisWorks/hypothesis/tree/master/hypothesis-python)的 Python。

总的来说，Brownie 负责部署、测试和调试。当部署到本地或测试网区块链时，部署是容易的。

Brownie 还支持 mainnet fork，如果您想与某些在本地或 testnet 区块链上不可用的智能合约进行通信，它会对当前区块链进行实时快照。

#### 布朗尼装置

可以通过`pipx`或`pip`进行安装，但布朗尼推荐使用`pipx`的方式。

如果没有，安装`pipx`:

```
python3 -m pip install --user pipx
python3 -m pipx ensurepath

```

然后，安装布朗尼:

```
pipx install eth-brownie

```

#### 布朗尼网络

Brownie 提供了允许测试的开发和生活环境。这些环境可以在终端上使用几个命令进行配置。

要查看现有网络，请使用以下命令:

```
$ brownie networks list
Brownie - Python development framework for Ethereum

The following networks are declared:

Ethereum
  ├─Mainnet (Infura): mainnet
  ├─Ropsten (Infura): ropsten
  ├─Rinkeby (Infura): rinkeby
  ├─Goerli (Infura): goerli
  └─Kovan (Infura): kovan

Ethereum Classic
  ├─Mainnet: etc
  └─Kotti: kotti

Development
  ├─Ganache-CLI: development
  └─Ganache-CLI (Mainnet Fork): mainnet-fork

```

要添加新网络，请添加以下命令:

```
$ brownie networks add \[environment\] [id] host=\[host\] [KEY=VALUE, ...]

```

#### 布朗尼福利

Brownie 有一个内置的项目交互控制台，这是其他框架所没有的。例如，通过控制台添加网络可以让您运行命令来快速添加网络，而不是将它写入您的代码库中。

它也优于其他语言，因为它完全支持 Solidity 和 Vyper，这是两种不同的智能合约语言。

此外，通过运行测试时的交互模式，如果测试失败，它会停止并显示在控制台中，提醒用户。

#### 布朗尼的缺点

因为 Brownie 是一个基于 Python 的框架，JavaScript 开发者将无法轻松使用该框架。

### OpenZeppelin

OpenZepplin 是一个带有插件的工具包，有助于更快地构建智能合约。例如，它有[主要的标准令牌，如 ERC-20 和 ERC-721](https://blog.logrocket.com/create-deploy-erc-20-token-ethereum-blockchain/) 实现等等。

要使用 OpenZeppelin 中的任何智能合约，您必须将它们导入到您自己的智能合约中。注意，OpenZeppelin 实际上是作为 npm 包分发的，这意味着您必须首先安装 Node.js。

#### OpenZeppelin 安装

要安装 OpenZeppelin，请使用以下命令:

```
$ npm install @openzeppelin/cli

```

要设置 OpenZeppelin 项目，请使用以下命令:

```
$ npx openzeppelin init

```

根据下面编写的 OpenZeppelin 文档中的智能合约示例，我们必须编译以下代码:

```
// contracts/Counter.sol
pragma solidity ^0.5.0;

contract Counter {
    uint256 public value;

    function increase() public {
      value++;
    }
}

```

要进行编译，请运行以下命令:

```
$ oz compile

```

你可以使用`openzeppelin compile`或者`oz compile`，但是`oz`只是一个短命令来选择。

#### 开放齐柏林网络

部署我们的智能合约就像编译它们一样简单；OpenZeppelin 使用 [Ganache 作为开发网络](https://blog.logrocket.com/develop-test-deploy-smart-contracts-ganache/)，在这里你可以拥有一个本地的区块链。如果您还没有 Ganache，您应该安装它:

```
$ npm install ganache-cli

```

要启动新流程，请运行以下命令:

```
$ npx ganache-cli --deterministic

```

然后，部署智能合同:

```
$ npx oz deploy
✓ Compiled contracts with solc 0.5.9 (commit.e560f70d)
? Choose the kind of deployment: upgradeable
? Pick a network: development
? Pick a contract to instantiate: Counter
✓ Added contract Counter
✓ Contract Counter deployed
? Call a function to initialize the instance after creating it?: No
✓ Setting everything up to create contract instances
✓ Instance created at 0xCfEB869F69431e42cdB54A4F4f105C19C080A601

```

#### OpenZeppelin 的优势

OpenZeppelin 有许多已实现的功能，如创建 ERC-20。因为这在 OpenZeppelin 中已经可用，所以您不需要重新发明轮子，节省了开发时间。

它还有完整的文档，帮助您理解 OpenZeppelin 提供了什么。它的大多数合同功能都是可升级的，这意味着你可以自己修改插件。

#### OpenZeppelin 的缺点

OpenZeppelin 提供了可升级的合同，但缺点是有一个管理角色来处理实现，这可能会带来安全风险。

## 如何为你的 DApp 项目选择合适的框架

为一个项目选择合适的框架的最好方法是理解你想要构建什么以及你需要什么必要的工具包。也许您想要基于 Python 的东西，这无疑表明 Brownie 框架是您唯一的选择。

选择框架时要考虑的另一个方面是了解它的社区和对所选框架的支持。有重大 bug 怎么办？你和你的团队能修好它吗？或者社区是否足够强大来帮助修复它？

## 结论

随着最近 DApps 开发的兴起，越来越多的工具开始帮助更快地构建应用程序。最常见和使用最多的是 Hardhat 和 Truffle，但这并不局限于所有的开发者。

如果你有一个项目，浏览每个框架的文档，看看哪个最适合你。请注意，这些框架不断进行维护，可能也需要您的支持，因为它们在 Web3 中是开源的。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。