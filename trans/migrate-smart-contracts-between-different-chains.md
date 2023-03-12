# 如何在不同链之间迁移智能合约

> 原文：<https://blog.logrocket.com/migrate-smart-contracts-between-different-chains/>

智能合同可能会迁移到另一个区块链有几个原因。一些显而易见的原因是较低的汽油费或更快的交易时间。也有这样的情况，将合同转移到不同的链中是有意义的，以便调试合同，提供额外的安全性，甚至在由于盗窃或技术问题而无法访问合同时提供对合同的访问。

本智能合约迁移指南演示了如何将智能合约从本地开发链迁移到各个区块链，以及如何在不同链之间迁移智能合约。本文还概述了几种区块链，以及它们在智能合约迁移方面的差异。

向前跳:

## 先决条件

要阅读本文的教程部分，请确保您具备以下条件:

## 区块链之间迁移的注意事项

几个因素可能会影响将智能合同从一个区块链迁移到另一个的决定。让我们来看几个例子。

### 以太坊 vs 币安智能链

以太坊区块链是一个分散的、透明的、安全的公共区块链。在撰写本文时，以太坊使用了一个[工作一致性算法的证明](https://ethereum.org/en/developers/docs/consensus-mechanisms/pow/#:~:text=Ethereum%2C%20like%20Bitcoin%2C%20currently%20uses,certain%20kinds%20of%20economic%20attacks.)。智能合同可以在以太网上创建。

在以太坊区块链上做的每一个手术，无论成功与否，都必须用油费来支付。一项操作可能是从一个账户向另一个账户发送一定数量的以太(以太坊的加密货币)或部署一份合同。

在矿商将交易加入区块之前，必须支付天然气费。以太坊强制规定了每个区块的最大气体量，称为[区块气体限制](https://ethereum.org/en/developers/docs/gas/)。

[币安智能链(BSC](https://academy.binance.com/en/articles/an-introduction-to-binance-smart-chain-bsc) )使用一个[利益一致性算法](https://academy.binance.com/en/articles/what-is-a-blockchain-consensus-algorithm)证明。像以太坊一样，它支持创建智能合约。

从以太坊迁移到 BSC 的一些激励因素是币安较低的汽油费和更快的交易时间。这些好处使得 BSC 比以太坊更具可扩展性。以太坊智能合约可以很容易地迁移到 BSC 平台，只需对代码进行最少的更改或不做任何更改。

但是，BSC 确实有一些缺点，包括分权较少。这是因为有几个集中的实体，如 21，000 个节点验证器，这些验证器直接或间接归[币安](https://www.binance.com/en)所有，负责网络的较低天然气费用。

### 多边形与以太坊

与 BSC 类似，[多边形区块链](https://polygon.technology)使用一种利益一致性算法。Polygon 支持智能合约的创建，并通过允许在其区块链上启动的智能合约的交叉通信来实现互操作性。该网络的令牌 MATIC 提供了多种功能，如支付煤气费、下注和投票。

从以太坊迁移到 Polygon 的一些激励因素是 Polygon 更快的交易时间和更低的交易费用。Polygon 的结构也使它比以太坊更具可扩展性。随着多边形网络上节点数量的增加，生态系统扩展到足以容纳节点并处理它们的事务。

现在，让我们看看如何在不同的链之间迁移智能契约。

## 演示:从本地开发链进行智能合约迁移

在本教程中，我们将使用一个用 [Solidity 编写并部署在 Tuffle Suite 框架](https://blog.logrocket.com/truffle-suite-tutorial-develop-ethereum-smart-contracts/)上的智能契约。Truffle Suite 与其他以太坊虚拟机区块链集成，具有创建、测试、编译和部署智能合约的内置功能。

在将智能契约部署到本地开发链之后，我们将演示如何将它部署到单个区块链。我们将使用 MetaMask 进行部署，以便轻松查看我们的帐户余额和交易历史。

### 将智能合同部署到 Ganache

我们首先将智能合约部署到 [Ganache](https://trufflesuite.com/docs/ganache/) ，这是一个在 Truffle 环境中构建的区块链模拟工具。这将允许我们确保代码正确编译，并且部署没有问题。

首先，在您的终端中从这个 [GitHub repo](https://github.com/debemenitammy/contract-project) 克隆智能合约代码。然后，在项目的目录中，运行以下命令来安装依赖项:

```
npm install

```

在继续之前，确保您已经安装了 Ganache，因为这是您将使用的开发区块链。

现在，在您的系统上运行 Ganache 并点击**快速启动**，如下所示:

![Ganache](img/df17902ff1267119993340f6854b0c8d.png)

接下来，我们将使用在`2_deploy_migration.js`文件中定义的迁移来部署智能合约。

首先，在您的终端和项目的目录中，运行以下命令来启动开发区块链:

```
truffle develop

```

这将使您的开发区块链有十个地址，每个地址包含 100 个 ETH 和私钥:

![Ten Keys](img/570a7f45cb8a1b2b47a00faa982d96b6.png)

接下来，通过运行以下命令来执行`migration`文件夹中的所有代码，从而部署智能合约:

```
migrate -reset

```

![Migrate](img/3cef90e126abcfb44938aece3d519a7e.png)

![Deploy Migration](img/fca8d6dcef409d6ed8467119f496e317.png)

终端的输出显示`2_deploy_migration.js`文件已经部署。输出还显示合同信息，包括事务散列、块号和合同地址。

注意，一些智能合约信息，比如`value sent`和`total cost`，是在 ETH(以太坊的货币)中提供的。迁移智能合同时，您必须首先将 ETH 中的任何信息更新为合同要迁移到的区块链的本地货币。

要退出环境，在终端中键入两次 **CTRL** + **C** 。

现在，让我们看看如何将智能合约迁移到以太坊区块链。

### 将智能合约部署到以太坊的测试网(Rinkeby)

要将智能合约从本地区块链部署到以太坊的 Rinkeby 网络，您必须首先拥有一个地址。

要生成地址，请打开 MetaMask 钱包并连接到 Rinkeby 测试网络。

要获得免费乙醚，请使用[水龙头链](https://faucets.chain.link/)，连接到 Rinkeby，并将钱包地址粘贴到字段中。

然后，点击**发送请求**。

如下图，这应该会向你的钱包发送 0.1ETH。

![ETH in Wallet](img/4b636974c910bb3ad655a702baa38d63.png)

接下来，是时候配置松露了。在您的代码编辑器中，打开`truffle-config.js`文件来定义您的网络配置。

检查`truffle-config.js`文件的文档，其中定义了以下内容:

```
/**
* Use this file to configure your truffle project. It's seeded with some
* common settings for different networks and features like migrations,
* compilation and testing. Uncomment the ones you need or modify
* them to suit your project as necessary.
*
* More information about configuration can be found at:
*
* https://trufflesuite.com/docs/truffle/reference/configuration
*
* To deploy via Infura you'll need a wallet provider (like @truffle/hdwallet-provider)
* to sign your transactions before they're sent to a remote public node. Infura accounts
* are available for free at: infura.io/register.
*
* You'll also need a mnemonic - the twelve-word phrase the wallet uses to generate
* public/private key pairs. If you're publishing your code to GitHub make sure you load this
* phrase from a file you've .gitignored so it doesn't accidentally become public.
*
*/

```

接下来，在 Infura:[infura.io/register](https://infura.io/dashboard)上创建一个账户，然后创建一个新项目，点击右上角的**项目设置**。

![Project Settings](img/8995764e4bce5dd3bc0011bf2e3440f6.png)

在常规选项卡的密钥下，从端点下拉列表中选择 **RINKEBY** 。您应该会看到您的项目 ID 以及您在 Infura 上的节点的 URL。

![Select Rinkeby](img/87876e4f7b4bc6528def1f1ef61a0b6a.png)

***注意，*** *您将使用安装在您的* `package.json` *文件中的* `@truffle/hdwallet-provider` *依赖项来将您的提供者设置为元掩码*上的私钥

在您的`truffle-config.js`文件中，需要`@truffle/hdwallet-provider`依赖项并创建一个数组来存储您的私钥:

```
const HDWalletProvider = require('@truffle/hdwallet-provider');
const privateKeys = ['<app your private key>'];

```

现在，向下滚动到 networks 部分，添加一个开发脚本，在 MetaMask 上将`provider`设置为您的私钥，在 Infura 上为您的节点设置 URL:

```
development: {
     provider: () => new
HDWalletProvider("<Your private key on metamask", `https://rinkeby.infura.io/v3/71acb374b9dd4e1eb06eeb9a75a8cd1a`),
     network_id: 4,       // Any network (default: none)
},

```

你会注意到在上面的代码中,`network_id`被设置为 4；这是林克比的[网络 ID。其他以太坊网络有不同的 id。](https://besu.hyperledger.org/en/stable/Concepts/NetworkID-And-ChainID/)

接下来，通过运行以下命令执行迁移和您的配置:

```
truffle migrate -network development --reset 

```

![Deploy Migration](img/f1f4cfebd00bd012a265ea0567772143.png)

现在，复制事务散列。导航至 MetaMask 并点击**查看 Etherscan** 上的账户。在事务列表中，您将看到刚刚部署的事务哈希:

![Transaction Hash](img/15d8ecb4a0d2d8196b13e25a8b00d09d.png)

单击单个交易查看更多详细信息:

![Individual Transaction](img/735ce00cf58998ca0a3cde3b24bd5bb5.png)

### 将智能合同部署到 Polygon 的测试网(孟买)

要将智能合约部署到 Polygon 的孟买网络，首先要将元掩码钱包连接到 Polygon。打开 MetaMask 并点击**添加网络**，然后将以下内容添加到适当的字段:

![Metamask Settings](img/d741d617d5c282de9d79ec096eb681b9.png)

然后点击**保存，**meta mask 将连接到多边形。

因为您正在将合同迁移到 Polygon 的 testnet，所以您将需要 MATIC 令牌来支付您的事务。要获取 MATIC 令牌，请打开元掩码钱包并复制钱包地址。然后，导航到[多边形龙头](https://faucet.polygon.technology/)并将地址粘贴到钱包地址字段。点击**提交**，然后点击**确认**。

![Confirm Details](img/7d74a124108d718608abf7eab6505ce5.png)

如下所示，您应该看到请求已提交的确认。

![Request Submitted](img/39eef0d0622294d82da6d097070d6ad3.png)

几分钟后，您应该会看到元掩码钱包中的令牌。成功转移 MATIC 代币后，它们将出现在您的钱包余额中。

![Matic Tokens](img/8417870abc9309762016c165f124904d.png)

接下来，在代码编辑器中，打开`truffle-config.js`文件。在网络脚本中，将 Polygon 的网络提供者添加到以太坊的网络提供者下面。您将使用在[多边形文档](https://docs.polygon.technology/docs/develop/network-details/network/)中定义的孟买测试网的 RPC URL。

复制 [RPC 链接](https://rpc-mumbai.matic.today)如下图，[https://RPC-Mumbai . matic . today](https://rpc-mumbai.matic.today):

![RPC Link](img/8145c07b46bc098fec2690e1c83539b1.png)

然后，将 RPC 链接作为`provider` URL 粘贴到代码中。同样将 chainid(如上)粘贴到代码中作为`network_id`:

```
polygonTestnet: {
     provider: () => new HDWalletProvider(privateKeys, `https://rpc-mumbai.matic.today`),
     network_id: 80001,       // Any network (default: none)
   },

```

现在，打开您的终端，使用以下命令运行到多边形 testnet 的迁移和配置:

```
truffle migrate --network polygonTestnet --reset

```

您可以在下面看到您的合同已成功迁移:

![Successful Migration](img/9abc28b026a31e191c85ba871c28e4da.png)

现在，复制事务散列。打开你的元掩码账户，点击浏览器中的**查看账户**:

![View Metamask Account](img/232180572af61e78c06b149cd08168b4.png)

您应该会在[多边形扫描](https://polygonscan.com)区块链浏览器中看到已部署的合同和事务:

![Deployed Contract](img/1045694529724b942d939ab09720d282.png)

### 将智能合同部署到币安智能连锁

将您的智能合同部署到币安智能连锁的测试网。你需要得到 BNB 代币(这是币安智能链的本地货币)。

首先将 MetaMask 钱包连接到 BSC。打开元掩码，点击**添加网络:**

![Add Network](img/1616e734cae68ff315fde0362897d319.png)

接下来，将以下内容添加到相应的字段中:

![Add Network Page](img/0cd1d814ba05c40fe8537a515562dfb4.png)

然后，点击**保存，**，元掩码将连接到 BSC，并将 BNB 令牌设置为零:

![Zero BNB](img/ac9f40045941ef1e58649966298c09b9.png)

要获得免费的 BNB 代币，请打开您的 MetaMask 钱包并复制您的钱包地址。然后，导航到[币安智能连锁水龙头](https://testnet.binance.org/faucet-smart)。在地址输入中，粘贴您的元掩码钱包地址。

![Smart Chain Faucet](img/0785c394e78feea69f2938dd2d445f06.png)

现在，点击**给我 BNB** ，1 BNB 将被转移到你的 MetaMask 钱包。

![Funding Request Accepted](img/265e6eb70c2eb5331f16c54ec712fd09.png)

您可以通过检查元掩码帐户余额来确认令牌已发送:

![Metamask Balance](img/85a8b6f1386a5cc00ac0f99439813a49.png)

现在，在您的代码编辑器中编辑`truffle-config.js`文件，并为币安智能链网络添加以下配置:

```
 bscTestnet: {
     provider: () => new HDWalletProvider(privateKeys, `https://data-seed-prebsc-1-s1.binance.org:8545/`),
     network_id: 97,       // Any network (default: none)
   },

```

要将您的合同部署到币安智能链测试网，请打开您的终端并运行以下命令:

```
truffle migrate --network bscTestnet --reset

```

![Deploy Migration](img/b2a5c64ebb187c68000e01f3b69190ad.png)

## 链之间的智能契约迁移

我们讨论了一些可能影响将智能合同从一个区块链迁移到另一个的因素。现在，让我们看看几种智能契约迁移技术。

### 通过副链迁移

波尔卡多特网络为区块链提供了一个熟悉而安全的环境，让他们可以轻松地相互交流。

副链是在波尔卡多特生态系统中相互作用的第一层区块链。副链相互独立，但平行运行。它们由网络的[中继链](https://wiki.polkadot.network/docs/learn-architecture#relay-chain)连接。这种连接允许[跨链互操作性，或者通信和共享数据的能力](https://blog.logrocket.com/blockchain-bridges-cross-chain-data-sharing-guide/)。

Polkadot 网络通过使用[验证者](https://wiki.polkadot.network/docs/learn-validator)和[提名者](https://wiki.polkadot.network/docs/learn-nominator)来利用利益相关共识机制的证明。[整理器](https://wiki.polkadot.network/docs/learn-collator)(如下图所示)，保存网络交易的信息。

![Relay Chain](img/d62606d7c27ef1d8be5a7dbac33f1d67.png)

Parachains 使开发人员能够使用 Polkadot 的跨共识消息格式(XCM)在 Polkadot 生态系统内迁移合同，而不是包装和解包令牌(我们将在下一节讨论)。

XCM 用于格式化在链、智能合约或桥之间发送的消息。XCM 支持一种定义的格式，用于在较长时间内将交易从一个钱包发送到另一个钱包。

XCM 有不同功能的消息协议:[垂直消息传递](https://wiki.polkadot.network/docs/learn-crosschain#vmp-vertical-message-passing) (VMP)和[跨链消息传递](https://wiki.polkadot.network/docs/learn-crosschain#xcmp-cross-chain-message-passing) (XCMP)。

此外，XCM 使用一个[交叉一致性虚拟机](https://wiki.polkadot.network/docs/learn-crosschain#xcvm-cross-consensus-virtual-machine) (XCVM)，它包含几个作为程序运行的指令和要传输的消息。XCVM 中的指令由传输的消息及其目的地组成。

此外，在 Polkadot 和其他第一层区块链(如以太坊和币安智能链)之间存在桥梁，从而实现互操作性。

### 通过包装和解包令牌进行迁移

每个区块链都有根据本国数字货币设计的可交易代币。当智能契约在不同的链之间迁移时，需要有一种方法让为一个区块链设计的令牌在另一个上工作。

这有点像去一个使用不同货币的国家旅行。然而，区块链通常利用包装和解包的概念，而不是将一种类型的代币换成另一种。

包装需要将代币的价值与其原始加密货币挂钩。包装令牌基本上是一种可在其他区块链上使用的可交易令牌。作为一个例子，让我们说一些以太坊的 ERC-20 是包装。它们的价值将与其当前价值挂钩，但它们现在被视为包装以太网(WETH)。代币可用于元掩码钱包等，以在各种区块链(BSC、IOTA 等)上进行交互。)

包装令牌的最大好处之一是原始资产得以保留，但包装的令牌继承了新区块链的功能。这些功能中的一些可以包括最小化交易时间和低汽油费。

解包代币是指将代币反转为原始加密货币的过程。例如，我们可以将其解包为原始加密货币 ETH。

这里有一个演示，展示了使用 [OpenZeppelin ERC-20 标准](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol)包装和解包以太币令牌:

```
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;
import https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol;
contract WETH is ERC20 {
    constuctor() ERC20("Wrapped Ether", "WETH") public {};

    // define a mint function to create new wrapped ether token
    function mint() external payable {
        _mint(msg.sender, msg.value); 
    }
    // define a burn function to create an unwrapped token
    function burn(uint amount) external { //specify the amount that you want back
        payable(msg.sender).transfer(amount); // transfers 
        _burn(msg.sender, amount); //sending back the ether to the user
    }
}

```

让我们仔细看看上面代码块中的单独代码行:

*   第 1 行:指定用于 Solidity 编译器的许可证
*   第 2 行:指定实体版本
*   第 3 行:导入 OpenZeppelin 中定义的 ERC-20 令牌标准
*   第 4 行:定义创建包装和未包装令牌的契约
*   第 5 行:定义了一个`constructor``()`函数，一旦契约被执行，它就将以太的名称设置为“Wrapped Ether ”,并将它的符号设置为“WETH”
*   第 7 行:定义一个`mint()`函数，它接受一个 Ether 值并创建一个新的包装 Ether 令牌；外部限定符意味着可以从其他契约调用该函数；payable 限定符表示该函数可以接受和发送以太网
*   第 9 行:使用 OpenZeppelin 代码中定义的`_mint`函数；它接受事务调用者的地址作为`msg.sender`，接受发送的以太值作为`msg.value`
*   第 12 行:定义一个`burn()`函数，该函数接受用户想要的令牌数量，并发送回一个未包装的令牌
*   第 13 行:通过用 payable 限定符包装事务调用方的地址`msg.sender`来指定要转移的金额，以使其能够转移 Ether 使用 OpenZeppelin 中定义的`transfer`函数接受要转移的金额
*   第 14 行:使用 OpenZeppelin 中定义的`_burn()`函数向事务的调用者发送一个未包装的令牌(原始以太)

## 结论

在本文中，我们演示了如何在 Ganache 中建立智能合约，然后使用 MetaMask 将其迁移到以太坊的 Rinkeby testnet、Polygon 的 Mumbai testnet、币安智能链和 Polygon。我们还讨论了在不同链之间迁移智能契约的不同策略:包装和解包令牌，以及副链。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。