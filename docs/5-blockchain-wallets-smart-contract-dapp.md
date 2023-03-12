# 为你的智能合同或 DApp 博客准备 5 个区块链钱包

> 原文：<https://blog.logrocket.com/5-blockchain-wallets-smart-contract-dapp/>

超过 82 个加密钱包可供您在下一个智能合同或 DApp 中使用。然而，并非所有这些钱包都是可用的；有些不能与您的智能合同集成，或者不提供软件开发工具包(SDK)。一个好的智能合约钱包支持区块链互操作性，并且是链不可知的，具有多会话功能。

从 82 个钱包中找出一个具有上述特征的钱包并不容易。因此，我编制了一个最佳钱包列表，您可以使用它来集成您的智能合约和 DApp，并包括在为您的用例选择正确的钱包时您应该寻找的一切。

### 内容

## 什么是智能合约，为什么区块链钱包是必要的？

智能合同是双方之间的协议，规定当满足特定条件时，哪些动作将在区块链网络中自动执行。该协议将被转化为一个典型的使用 Solidity 的程序。

有时，当满足特定条件时，智能合约必须支付或接收来自另一方的密码。这就是加密钱包需要与您的 DApp 集成的地方。

加密钱包是保存加密货币和代币的软件存储。这种钱包也有利于加密货币交易，并且不限于单一的加密货币类型。

在接下来的部分中，我们将回顾五个钱包，您的智能合约和 DApp 可以使用它们来执行加密交易。

## 阿尔根特

[Argent](https://www.argent.xyz/) 无疑是最好的智能合约钱包，因为它促进了低汽油费，并给你一个免费的 ENS，用“Keharley.eth”取代长钱包地址，如`3FZbgi29cpjq2GjdwV8eyHuJJnkLtktZc5`，这是一个 ENS(以太坊名称服务)地址。与一般的长地址相比，ENS 地址容易记忆，对非极客来说也很好看。

Argent 支持多重签名技术，增强了钱包的安全性。找回钱包时，您也不需要种子短语。

Argent 只专注于促进智能合约，但您可以使用 WalletConnect 将其连接到一个分散的应用程序。你可以在这里查看他们的开发者文档[，在这里](https://docs.argent.xyz/wallet-connect-and-argent)查看 GitHub 库[。](https://github.com/argentlabs/argent-contracts/)

## 波蒂斯

[Portis](https://www.portis.io/) 只需四行代码即可轻松与您的分散式应用和智能合同集成:

```
import Portis from '@portis/web3';
import Web3 from 'web3';

const portis = new Portis('YOUR_DAPP_ID', 'mainnet');
const web3 = new Web3(portis.provider);

```

使用这些公事包程式库时，请确定您没有硬式编码公事包 DApp ID 或任何与公事包相关的秘密。

Portis 使用密码和电子邮件地址来验证用户，而不使用私钥。可以收发多种加密货币，但最大的亮点是接受借记卡和信用卡；它不仅专注于加密。

Portis 支持区块链互操作性和以下区块链:

*   以太坊
*   比特币
*   黎明的女神
*   斯卡莱
*   马基奇
*   Ubiq
*   雷霆核心

## 比特币基地钱包

[比特币基地钱包](https://www.coinbase.com/wallet)(原名 WalletLink)用于连接其他比特币基地钱包。仅当您已经拥有比特币基地帐户，并且您的 DApp 仅与其他比特币基地帐户进行交易时，才可使用此钱包。

您可以在此了解如何集成比特币基地钱包 SDK [。比特币基地钱包有一个 Node.js 包，您可以使用以下命令进行安装:](https://github.com/coinbase/coinbase-wallet-sdk)

```
#npm install @coinbase/[email protected]

```

您也可以像这样使用纱线卷装管理器:

```
#yarn add @coinbase/[email protected]
yarn add @coinbase/wallet-sdk

```

最后，比特币基地钱包的好处之一是它的 [chrome 扩展](https://chrome.google.com/webstore/detail/coinbase-wallet-extension/hnfanknocfeofbddgcijnmhnfnkdnaad?shortlink=fdb9b250&c=wa_i_m_n_i_m_wal_coi_0_Wallet-LP-Single-Button&pid=Wallet%20LP)，可以让你看到实时加密图表和管理 NFT。

元掩码很流行，尽管它的扩展有一些错误和问题。使用 Metamask 进行 DApp 交易很方便，因为很多人都熟悉它，而且[有很多故障排除文章](https://blog.logrocket.com/understanding-resolving-metamask-error-codes/)。

Metamask 有一个[以太坊提供者 API](https://docs.metamask.io/guide/ethereum-provider.html) ，你可以用它来请求用户的账户，并提示他们从你的 DApp 签署交易。你可以在这里查看完整的开发者文档。

## 分类帐

[账本](https://www.ledger.com/)是一个硬件钱包，这使得它不同于之前所有的钱包。但是，它确实附带了可以安装在手机上与实体钱包互动的软件。就像任何其他钱包一样，Ledger 也允许您进行交易和出售您所有的加密资产。

Ledger 提供了一个 SDK，您可以使用它与 wallet 进行通信。你可以在这里学习如何整合他们的钱包[。](https://developers.ledger.com/)

## WalletConnect

[WalletConnect](https://walletconnect.com/) 不一定是钱包，而是一种用于连接钱包和智能合约或 DApp 的协议。它提供区块链互操作性，支持不同的链，并附带会话管理。它还拥有一个区块链 SDK，允许开发人员将他们的应用程序与 WalletConnect 顺利连接。该 SDK 由推送服务器和 QR 码组成。

使用 WalletConnect，您只需要三行代码和项目 ID 就可以将其与您的 DApp 集成:

```
import WalletConnectClient from "@walletconnect/client";

const client = await WalletConnectClient.init({
 projectId: "enter your project id here",
});

```

## 如何为您的 DApp 或智能合约选择加密钱包

### 文档和支持

糟糕的文档质量和支持是开发人员从一个钱包转移到另一个钱包的主要原因。

在选择钱包之前，先去钱包的网站，查看他们的文档页面；确保你计划使用的每一个功能都解释清楚。这些钱包大多存在不到五年，所以没有各种贡献者制作的丰富教程。因此，文档是您获得如何使用钱包 SDK 的信息的唯一地方。

还有，一定要加入钱包的社区，比如 Slack workspace 或者 Discord server。检查团队是否积极回答问题或试图解决钱包用户提出的问题。

使用新的 Web3 库时，不可避免地会出现错误和 bug，所以我们的目标是选择一个拥有丰富文档和许多在线问题解决方案的钱包。

### SDK 和可维护性

JavaScript 在这个领域被广泛使用，所以如果你计划在你的 DApp 前端使用 JavaScript 框架，那么你有很多钱包选项。对于 Android 开发来说， [Kotlin](https://blog.logrocket.com/tag/kotlin/) 已经取代了 Java，反正钱包 SDK 更喜欢用 Kotlin 集成。至于 iOS， [Swift](https://blog.logrocket.com/tag/swift/) 依然是首选语言。

具有高质量文档的 wallet SDK 将易于维护。此外，SDK 应该为开发人员使用简单的技术和简单的故障排除方法。必须检查 wallet SDK 是否持续更新，以提高安全性和可用性。

易于维护的钱包应具备以下特点

*   易于理解的代码
*   无需费力即可快速修复的能力
*   干净的代码和高质量的代码标准

### 钱包可扩展性

在寻找钱包的可扩展性时，你应该问以下问题:钱包支持多少种加密货币？交易费用是多少？它支持多链还是不可知链技术？

如果钱包的汽油费很低，并且对上述问题提供了肯定的答案，那么它就很容易扩展。它还将允许扩展你的 DApp 和智能合同，因为钱包将不会限制你的一小撮区块链网络。

### 认证和安全因素

安全钱包应该易于认证，并提供双重认证。像 [Authereum](https://authereum.com/welcome/) 这样的钱包可以用来验证其他网站。

检查钱包的安全完整性很重要。安全的钱包应该符合[加密货币安全标准](https://www2.deloitte.com/mt/en/pages/technology/articles/mt-article-cryptocurrency-security-standard-CCSS.html)。这些安全标准是钱包安全可靠的标志。

此外，确保查找任何可能影响您未来智能合同的安全漏洞。钱包还需要有良好的声誉，并被不同的区块链项目使用，以证明其可信度。

## 结论

为您的智能合约选择钱包后，检查钱包的性能和增长情况。有些钱包会意外关闭，确保您的 DApp 不会突然失去功能非常重要。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

希望这篇文章给了你一些选择最适合你的智能合约或 DApp 的区块链钱包的提示和技巧，并向你介绍了一些可靠的选择。欢迎在下面留下任何评论或问题。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。