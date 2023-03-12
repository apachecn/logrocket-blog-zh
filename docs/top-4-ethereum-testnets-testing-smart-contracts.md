# 测试智能合约的 4 大以太坊测试网

> 原文：<https://blog.logrocket.com/top-4-ethereum-testnets-testing-smart-contracts/>

以太坊是世界上最受欢迎的区块链之一。它的流行是由于它的可伸缩性和处理大量事务的能力。然而，这也是一个非常昂贵的区块链。

因为以太坊具有必须遵循的协议特性，所以它可以被模拟出来用于测试网。

在本教程中，我们将比较四个顶级以太坊测试网。我们将讨论以下内容:

我们将回顾每个测试网的关键特性，并基于它们的水龙头、Eth 客户端和共识协议对它们进行比较。本指南结束时，您将了解每个 testnet 使用的各种共识协议，如何获得免费的 Eth 进行测试，以及用于连接每个 testnet 网络的 Eth 客户端。

## 什么是测试网？

以太坊测试网是用来测试以太坊协议的节点集合。测试在测试网上运行，以确保协议按预期工作。

测试网类似于模拟，因为它们用于在受控环境中测试协议。就像我们在单元测试中编写测试一样，我们编写智能契约并在 testnet 中测试它们。

与编写测试并将其部署在测试网上相比，编写智能合同并将其部署在 mainnet 上是一项昂贵得多的操作。这是因为在我们可以在 mainnet 上部署我们的智能合同之前，我们必须支付汽油费，而这些汽油费是真正的钱。

显然，当你只是在测试你的智能合约时，你不想花真金白银。这就是测试网的用武之地。测试网提供免费的加密货币，你可以用它来支付汽油费。由于这种免费的加密货币没有价值，我们不能在 mainnet 中使用它。

简单地说，在 mainnet 上部署智能合约之前，您应该在 testnet 上测试它们。

## Rinkeby

[Rinkeby](https://www.rinkeby.io/) 是一个以太坊测试网，用于测试以太坊协议。它是以太坊主网的一个分支。Rinkeby 使您能够在部署到真正的以太坊 mainnet 之前，在测试环境中开发 DApps。

Rinkeby 由以太坊基金会于 2017 年 4 月建造，由 [Geth](https://geth.ethereum.org/) 开发者团队维护。它使用权威证明[共识机制](https://blog.logrocket.com/guide-blockchain-consensus-protocols/)。

在 rinkeby.io 加载 Rinkeby 站点会向你展示 rinkeby 网络。Rinkeby 显示网络统计信息，包括节点、事务和块的数量。此外，它还显示了每秒的事务数、每秒的块数和对等体数，对等体是连接到 Rinkeby 的节点数。

![Rinkeby Dashboard](img/b2a83799f39090bccb68f5ce5a9d9d89.png)

Rinkeby 站点有一个块资源管理器，它会以可读的格式向您显示区块链:

![Testnet Explorer](img/93453cdbeead3d1a1f38d45a885fbd62.png)

要编写一个智能契约并将其部署在 Rinkeby testnet 上，第一步是在桌面浏览器上安装 [MetaMask](https://metamask.io/) 扩展。

首先去 [Chrome 商店搜索 MetaMask](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn?hl=en) 。单击 MetaMask 扩展并安装它。

安装完成后，进入 Chrome 菜单，点击**插件**按钮。然后，点击**扩展**，然后点击**元掩码**。

MetaMask 将弹出并提示您登录。如果您没有帐户，可以点击**创建帐户**按钮创建一个。之后，您可以登录 MetaMask。

![Ropsten Test Networks](img/349d8251125a6e48a5c53aab6a1feb78.png)

正如你所看到的，有几个测试网可供选择，包括以太坊主网，林克比测试网，科万测试网，罗普斯滕测试网，戈利测试网。Localhost:8545 是在您的机器上本地运行的以太坊节点。

选择 Rinkeby 测试网络:

![Select Rinkeby Test Network](img/6e6d1d4c7f6c413eccdc150ed6b3054e.png)

如你所见，我们有 0 个 Eth。

首先，我们要复制我们的以太坊地址。为此，点击**复制**图标来复制你的以太坊地址。

让我们看看如何在 Rinkeby 网络上获得免费 Eth。官方水龙头位于[这里](https://faucet.rinkeby.io/)。粘贴你的 Rinkeby testnet 地址，获得免费的 Eth。

Rinkeby 网络上也有提供免费 Eth 的第三方网站，比如 [Rinkeby Ether 水龙头](https://rinkeby-faucet.com/)。只需在**我的地址**字段中粘贴您的以太坊地址，点击**提交**即可获得免费以太坊。

如果上面的水龙头坏了，你可以用这里的水龙头。

在 Rinkeby 水龙头上得到免费的 Eths 是相当困难的。它包括为 Rinkeby 发布社交媒体帖子，并等待 8 小时以获得 3Eth，一天以获得 7.5 Eth，三天以获得 18.5 Eth。等待的时间太长了。

Rinkeby 只使用 Geth 客户端在网络上进行交易。这迫使您在 Rinkeby 网络上进行交易时切换到 Geth 客户端。这是另一个巨大的挫折。

一个关键的优势是，Rinkeby 使用了权威证明，这在区块链世界很快被接受。

## 很难吗

Kovan 是另一个用来测试以太坊协议的测试网。像林克比一样，科万是 mainnet 的一个分支。

Kovan 由以太坊基金会于 2017 年 6 月建造，也像 Rinkeby 一样，由 Geth 开发团队维护。它也使用权威共识机制的证明。

总的来说，Kovan 支持与 Rinkeby 相同的功能。你也可以使用官方水龙头在这个科万测试网上获得免费的 Eth。

你可以在科万测试网上写 [ERC-20](https://blog.logrocket.com/create-deploy-erc-20-token-ethereum-blockchain/) 、ERC-721 和 ERC-1155 代币。它显示了最新的块，最新的交易，以及 ERC-20，-721，-和 1155 令牌。您还可以搜索地址、交易、区块等。

![Kovan Testnet Explorer](img/3ce63a0de43e25c409499d856b4c78a5.png)

我们在柯万得到的自由醚叫做柯万醚(kEth)。在科万获得免费 kEth，可以使用[官方水龙头](https://faucet.kovan.network/)。每个 GitHub 帐户每 24 小时可以立即获得一个 KEth。你也可以在[科万·吉特聊天室](https://gitter.im/kovan-testnet/faucet#)获得免费的 kEth。

![Kovan Gitter Chat Room](img/6675d71d3e4b39d1ac3c7e6d34424c26.png)

要获得免费的 kEth，请加入聊天室并粘贴您的 Kovan testnet 地址。

![Testnet Address](img/e97924a563c40d47dadfff5b916f136f.png)

现在我们有自由了！

![Free Eth](img/9f23df53d111c6aa245804a55e45b7c5.png)

![Kovan Test Network](img/d465d8da87c1a950d14e82b49b038144.png)

Kovan 使用权威共识证明机制，不像以太坊使用利害关系共识证明。这使得 Kovan testnet 与它所代表的以太坊 mainnet 有些不同。

与其他测试网相比，在 Kovan 上获得免费的 eth 相对容易。你所要做的就是把你的 Eth 地址发送到 Kovan Gitter 频道。

## 罗普斯滕

[Ropsten](https://ropsten.etherscan.io/) 由以太坊基金会于 2017 年 8 月创建，与许多流行的以太坊测试网一样，由 Geth 开发团队维护。它使用权威证明共识机制。

[Ropsten 网站](https://ropsten.etherscan.io/)和科万测试网网站一样，显示最新区块和最新交易。您还可以查看 ERC-20、-721 和-1155 令牌以及搜索地址、交易、块等。

![Ropsten Testnet Explorer](img/aa3729cef6fbc6ebde7c7a37f15f3490.png)

要在 Ropsten testnet 上获得免费 Eth，我们可以使用[官方水龙头](https://faucet.ropsten.be/)。

![Ropsten Eth Faucet](img/3f2c2420ec4d688dc6089b890e429299.png)

只需粘贴您的 Eth 地址，然后点击**给我发送测试 Eth**即可获得一个免费的 0.3 Eth。

Ropsten 测试网络被认为是与以太坊主网关系最密切的测试网络。协商一致机制是行之有效的证明。

2017 年 2 月， [Ropsten 受到攻击](https://www.coindesk.com/markets/2017/10/04/ethereum-testnet-faces-attack-but-byzantium-delay-unlikely/)并被叫停。攻击者将天然气上限提高到 200 米，采矿奖励提高到 12 Eth。攻击被停止， [Ropsten 测试网络于 2017 年 8 月恢复](https://github.com/ethereum/ropsten/blob/master/revival.md)。

与其他测试网相比，使用 Ropsten 的优势在于它是最忠实地模拟以太坊主网的测试网。它的共识算法与以太坊主网相同，这使它成为以太坊开发者的一个很好的选择。

此外，在 Ropsten 上获得免费的 eth 比在其他 testnets 上更容易。你所要做的就是把你的 Eth 地址贴在水龙头链接上，在~5 分钟内获得 0.3 Eth。此外，Ropsten 支持 Geth 和奇偶校验客户端，这使得它与其他测试网络相比具有很高的兼容性。

## 格里

[Goerli](https://goerli.etherscan.io/) 由奇偶团队于 2018 年 11 月创建。像上面描述的许多测试网一样，它使用权威共识机制的证明。

Goerli 网站显示了最新的区块和交易，以及 ERC-20、-721 和-1155 代币。像我们评估过的其他测试网一样，您可以搜索地址、事务、块等。，利用戈利。

关于 Goerli testnet 最新版本的信息可以在 GitHub 上找到。

你可以使用官方 [Goerli testnet 水龙头](https://goerli-faucet.slock.it/)将 Eth 免费发送到你在 Goerli Testnet 中的以太坊地址。只需粘贴您的以太坊地址，并按下**请求 0.05 戈特**按钮即可接收 0.05 戈特。

## 结论

在本指南中，我们演示了如何在 Rinkeby、Kovan、Ropsten 和 Goerli 以太坊测试网上获得免费 Eth。我们还演示了如何使用各自的水龙头在这些测试网上释放 Eth。

我们首先在以太网中引入了测试网的概念。从那里，我们深入研究了四个流行的以太坊测试网，分析了每一个，并指出了它们的网络站点、共识机制和龙头。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。