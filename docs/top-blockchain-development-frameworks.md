# 6 大区块链发展框架- LogRocket 博客

> 原文：<https://blog.logrocket.com/top-blockchain-development-frameworks/>

区块链彻底改变了我们在互联网上看问题和做事的方式。许多公司正转向区块链，以增加透明度和不变性，降低交易成本，并消除对中间人的需求。

区块链网络消除了对集中式数据库的需求，因为网络中的每个人都可以看到交易并验证它们。这在网络的参与者之间建立了信任和透明度。

决定转到区块链需要考虑很多因素。其中最基本和最重要的考虑是选择正确的区块链框架来运行您的企业。

有太多的区块链开发框架需要在一篇博文中探究。相反，我们将关注区块链开发世界中最流行和最广泛使用的六个框架:

1.  [以太坊](#ethereum)
2.  [总账结构](#hyperledger-fabric)
3.  [总账锯齿](#hyperledger-sawtooth)
4.  [EOSIO](#eosio)
5.  [Corda](#corda)
6.  [号房](#quorum)号房

我们将评估每种方法的共识算法、分类账类型、加密货币和智能合约支持。

## 什么是区块链？

区块链是基于去中心化的联网技术，就像一个点对点的网络。区块链的去中心化就像 Napster 一样工作:网络中的每一方都相互连接。这不同于您可能习惯的普通客户机-服务器网络。区块链网络中的每个节点既是客户端又是服务器。

在区块链网络中，公共数据库、分类帐或记录由网络中的节点维护和共享。该分类账包含网络中发生的交易，并且这些交易由网络中的多数共识来验证。一旦新交易被验证并被输入分类帐，该分类帐就被广播到网络中的所有节点。这样，节点就有了包含最新交易的当前分类帐。

很容易理解为什么各行各业的公司都在采用区块链技术来促进有价值资产的安全交易，如数字文件、财产、货币等等。

事不宜迟，让我们探索一下开发基于区块链的应用程序的六个最流行的框架。我们将重点介绍关键特性，并分析优势和劣势，以帮助您在选择合适的平台来发展您的区块链网络时做出更明智的决策。

## 1.以太坊

![Ethereum](img/9fdcf6f90625f11f6fb2d61cf09ec9b0.png)

[以太坊](https://ethereum.org/en/)是世界上最流行、最广泛使用的区块链开发平台。事实上，这是第一个区块链开发平台。它建于 2015 年，引入了一项被称为[智能合约](https://ethereum.org/en/developers/docs/smart-contracts/#:~:text=A%20%22smart%20contract%22%20is%20simply,runs%20on%20the%20Ethereum%20blockchain.&text=Smart%20contracts%20are%20a%20type,network%20and%20run%20as%20programmed.)的革命性功能。

智能合约是包含功能和状态的程序。每个智能合约在以太坊区块链的一个特定地址上运行。因为智能合约是以太坊上[账户的自治类型，它们可以](https://ethereum.org/en/developers/docs/accounts/)[发送交易](https://ethereum.org/en/developers/docs/transactions/)并且也有余额。

以太坊的另一个牛逼功能是[以太坊虚拟机(EVM)](https://ethereum.org/en/developers/docs/evm/) 。EVM 的虚拟机是运行 Ehtereum 帐户和智能合同的虚拟计算机。

EVM 使你能够创建运行在以太坊上的分散式应用程序。DApps 的使用案例范围正在迅速扩展到金融行业之外，延伸到医疗保健、物流、房地产、法律系统等多个领域。

以太坊的智能合约是用 [Solidity](https://soliditylang.org/) 语言写的。以太网有一个**无许可**分类帐类型，并对公众开放使用。它的共识机制是工作的证明，这是众所周知的相当缓慢。

以太坊有一种加密货币叫做以太。以太用于支付在以太坊区块链中创建和初始化交易的费用。

| **分类账类型** | **共识** | **加密货币** |
| --- | --- | --- |
| 未经许可 | 啪 | 醚 |

## 2.超腿织物

![Hyperledger Fabric](img/eed847e7b6a46de65e7480832b920d04.png)

[Hyperledger Fabric](https://hyperledger-fabric.readthedocs.io/en/release-2.2/) 是由 Hyperledger Hub 开发的许可分布式分类帐框架。Hyperledger Hub 是由 Linux 基金会开发的一个项目，用于集中式和分散式区块链平台的开放开发。

Fabric 面向希望使用、集成或构建基于区块链的解决方案和应用程序的企业。

Hyperledger Fabric 与以太坊的相似之处不仅在于其允许的分类帐类型，还在于其模块化架构。这种模块化为 Fabric 提供了一种即插即用的接口，您可以在其中选择自己喜欢的服务，比如共识算法、智能契约类型等。

Hyperledger Fabric 还支持[智能合约](https://hyperledger-fabric.readthedocs.io/en/latest/sdk_chaincode.html)。Fabric 上的智能合约可以用 Go、Java 和 JavaScript 编写。

如果您遇到文档中未涉及的任何问题，Hyperledger Fabric 团队会随时[回答您的问题](https://hyperledger-fabric.readthedocs.io/en/latest/questions.html)。

| **分类账类型** | **共识** | **加密货币** |
| --- | --- | --- |
| 未经许可 | 可插拔框架 | 没有人 |

## 3.超分类帐锯齿

![Hyperledger Sawtooth](img/32853b2cb549af9bd5ba6876ecb99937.png)

[Hyperledger 锯齿](https://sawtooth.hyperledger.org/)是 Hyperledger Hub 的另一个模块化区块链平台，旨在开发分布式分类帐应用程序和网络。Hyperledger 锯齿由 Linux 基金会推出，现在由 IBM 和 Digital Assets 维护。

企业使用 Hyperledger 锯齿构建可扩展的健壮系统，并部署高度安全的区块链解决方案。就像织物和以太坊一样，超分类帐锯齿有一个允许的分类帐类型。

Hyperledger 锯齿有一系列高级功能和集成，包括:

*   一个名为 [Seth](https://sawtooth.hyperledger.org/docs/seth/releases/latest/introduction.html) (锯齿-以太坊)的集成项目，它使得在 Hyperledger 锯齿上部署以太坊智能合约成为可能
*   并行处理，与其他区块链平台相比，有助于更快的交易处理
*   一个动态的、未定义的共识协议，这意味着你可以随时改变共识算法

说到共识算法，Hyperledger 锯齿有很多，包括:

*   PoET(运行时间证明),它依靠安全的指令执行来实现 Nakamoto 式共识算法的扩展优势，而没有工作证明算法的功耗缺点
*   PBFT(实用拜占庭容错)，一种基于投票的算法，使用动态网络成员资格、定期视图更改和块追赶过程功能，在网络参与者中投票达成协议
*   Raft，一个基于 [Raft](http://raft.github.io/) 的共识引擎。这种共识使用相对独立的子问题的日志来商定一个值并做出决策

查看 Hyperledger 锯齿源代码可在 [GitHub](https://github.com/hyperledger/sawtooth-core) 上获得。

| **分类账类型** | **共识** | **加密货币** |
| --- | --- | --- |
| 未经许可 | 可插拔框架 | 没有人 |

## 4.EOSIO

![EOSIO](img/26a7d0fa1bfe0043d8f94dcb7e957dd1.png)

[EOSIO](https://eos.io/) 是 Block.one 于 2018 年推出的高性能开源区块链平台。EOSIO 为构建区块链应用程序提供了一个快速、可靠、高度安全的平台。

EOSIO 允许您在其现有网络上部署智能合约。您还可以构建自己的 EOSIO 网络，并在其上部署智能合约。

EOSIO 智能合约是用 C++编程语言编写的。要了解如何在 EOSIO 上编写和部署智能合同，请参见[官方文档](https://eos.io/)。

虽然不像以太坊这样的中坚分子那样受欢迎，但 EOSIO 有一些独特的卖点。开发人员为他们的区块链项目选择 EOSIO 是因为它:

*   快速高效
*   高度可配置
*   高度安全
*   高度兼容
*   [以开发者为中心](https://eos.io/for-developers/)

最重要的是，EOSIO 网站在让社区了解最新[新闻](https://eos.io/news-insights/)和[事件](https://eos.io/events/)方面做得非常好。

| **分类账类型** | **共识** | **加密货币** |
| --- | --- | --- |
| 许可的 | 委托股权证明 | 没有人 |

## 5.科尔达

![R3 Corda](img/23f0ababeda8f2deb454ec90db17ac7c.png)

[Corda](https://www.r3.com/corda-platform/) 是 R3 财团在 2015 年打造的开源区块链平台。Corda 最初是为金融机构设计的，但后来扩展到了其他领域，如医疗保健、保险、数字资产和金融。下一代区块链框架标榜自己是“DLT 金融服务及其他领域的首选平台”

Corda 具有权限分类帐类型，并支持智能合同功能，这意味着您可以在 Corda 区块链上编写和部署智能合同。Corda 智能合约可以用 Java 或 Kotlin 编写。

该平台没有挖掘功能，因此部分事务在大多数节点中是看不到的。换句话说，Corda 事务不是对所有节点开放的。Corda 中没有加密货币或代币。

Corda 有一个可插拔的共识，这意味着它有许多[共识算法](https://docs.r3.com/en/platform/corda/4.8/open-source/key-concepts-consensus.html)可供选择。

Corda 具有有效性一致性和唯一性一致性:

*   有效性共识检查事务被每个状态和输入的契约接受，并且事务具有所有必需的签名
*   如果交易的输入是唯一的并且没有在其他交易中使用过，则唯一共识同意一个值

| **分类账类型** | **共识** | **加密货币** |
| --- | --- | --- |
| 许可的 | 可插入的 | 没有人 |

## 6.法定人数

![Quorum](img/7f202e9c3e7604bd9c73cd7434e05a85.png)

[Quorum](https://consensys.net/quorum/) 是一个基于以太坊的开源区块链平台。它建于 2016 年左右，旨在服务于金融行业，使企业能够“利用以太坊实现高价值的区块链应用”

最近， [Quorum 被 ConsenSys 从摩根大通](https://www.businesswire.com/news/home/20200825005497/en/ConsenSys-Acquires-Quorum%C2%AE-Platform-from-J.P.-Morgan)手中收购。许多公司信任 Quorum，并在其业务中采用了它，包括 Microsft、JP Morgan、Covantis、南非储备银行、SiaChain、Komgo 等。

Quorum 支持那些希望在业务中采用区块链平台的企业。它有一个许可的分类帐类型，但也允许根据客户的需求定制。此外，Quorum 支持公共和私有网络以及智能合同。

就像在以太坊中一样，Quorum 中的智能合约是用 Solidity 编写的，从以太坊切换到 Quorum 非常容易。法定人数的共识算法是基于投票的；它根据事务和阻塞从节点获得的票数来同意事务和阻塞。

有关如何开始使用 Quorum 区块链平台的更多信息，请参见官方[开发者文档](https://consensys.net/quorum/developers/)。

| **分类账类型** | **共识** | **加密货币** |
| --- | --- | --- |
| 许可的 | 投票 | 没有人 |

## 结论

有许多可用的区块链开发框架，但是这里列出的是世界上最流行的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在本指南中，我们介绍了区块链的概念，描述了它如何改变我们所知的世界，并列举了最广泛使用的区块链开发平台。我们评估了每个平台的优缺点，回顾了与每个平台相关的常见用例，并检查了支持的共识算法、分类帐类型和加密货币。

区块链是一个复杂的话题，尤其是在有各种平台可用于开发基于区块链的应用程序的情况下。最好的学习方法是从基础开始，发现它们是如何工作的，并在此基础上进行构建。请关注这个空间，了解更多区块链内容！

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。