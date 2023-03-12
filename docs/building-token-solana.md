# 用 Solana - LogRocket 博客构建自己的令牌

> 原文：<https://blog.logrocket.com/building-token-solana/>

区块链技术发生了很大的变化。除了高度安全这一事实之外，我们还有许多在其他领域提供价值的令牌。

今天，我们要谈谈索拉纳。 [Solana](https://solana.com/) 是世界上最快的区块链，也是加密领域发展最快的生态系统，有数千个项目横跨 DeFi、NFTs、Web3 等。

本教程将集中于使用 Solana 平台创建您的令牌。让我们跳进来吧！

## 什么是令牌(简单地说)？

在区块链世界中，一个令牌代表一组编码在智能契约中的规则。每个令牌属于一个区块链地址。

它本质上是一种安全存储在区块链上的数字资产。

## 为什么要创建自己的令牌？

由于令牌是一种数字资产，您可以创建自己的令牌。

不要说得太多，钱/资产的基本概念是人们认同某样东西有价值的结果。在法定货币出现之前，人们通常只进行商品交易。

他们交换有价值的产品(根据他们所同意的)，以获得另一个相对近似价值的东西。

同样，今天，如果你有一个社区或一个产品，你决定不使用传统货币(如你所在国家的货币)，你可以制作一个数字资产，并要求人们通过它向你支付。

就像钱一样，应该存在某种稀缺——在加密/区块链世界中，这被称为“总供给”。总供应量是指目前存在并在流通的硬币或代币的数量。理想情况下，任何有价值的东西都不应该无限供应。

创建令牌的另一个原因是为了学习。创建你的令牌将教会你一些关于区块链的非常重要的概念，这些概念将对一般区块链和 Web3 开发有用。

## 先决条件

要使用 Solana 创建令牌，您的计算机上需要以下内容:

运行`solana -version`以确认您已经安装了它。

![Solana Installation Confirmation](img/ecb13a40524df5e547d61926313abde7.png)

可能会提示您运行以下命令:

`PATH="/home/localhost/.local/share/solana/install/active_release/bin:$PATH"`

安装 SPL(索拉纳程序库)命令行界面。这是创建令牌所必需的:

运行`cargo install spl-token-cli`。

![SPL During Installation](img/aa1a0d204a6f099b015838dafecf25aa.png)

## 理解一些重要术语

在我们进一步创建 SPL 令牌之前，接下来我想与您分享一些概念。

### 主环境和开发环境

Solana 在两个环境中运行:主环境和开发环境。作为开发人员，您可能对这些术语很熟悉。主网络是生产的主索拉纳网络被保存的地方。

出于开发和测试目的，开发环境就是所使用的环境。对于本教程，我们将使用开发环境。

默认情况下，您的环境设置为“Main”。在我们继续下一步之前，我们应该将我们的环境设置为“开发”:

`solana config set --url [https://api.devnet.solana.com](https://api.devnet.solana.com)`

要检查我们的 Solana 集群环境，运行:`solana config get`。

![Solana Cluster Environment Check](img/f28a39dcc9ef9c6e6bc9fe44527ea95e.png)

这证实了环境正在发展中。

### 交易费用

当一定数量的[加密货币](https://coinmarketcap.com/alexandria/glossary/blockchain)从一个钱包转移到另一个钱包时，需要支付交易费。

交易费在本质上是灵活的，可以根据区块链网络的繁忙程度而变化。

### 总供水量

正如我们前面提到的，总供应量指的是目前存在的、流通的或以某种方式锁定的代币数量。

它是已经开采(或发行)的硬币的总和；铸币)减去已经销毁或烧毁的硬币总数。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 钱包

加密货币钱包是存储加密货币交易的公钥和/或私钥的设备、物理介质、程序或服务。

除了存储密钥的基本功能之外，加密货币钱包通常还提供加密和/或签名信息的功能。

把它想象成你放钱的钱包。就像你可以拥有多个钱包一样，你也可以把同样的原则运用到区块链身上。

## 创建令牌

在我们创建令牌之前，您需要一些索拉纳(甚至一个索拉纳就足够了)。要得到一个索拉纳，你可以从你的终端空投(像这样:`solana airdrop 1`)。

(注意:记住这不是一个真正的 Solana，因为我们处于开发环境中)

要创建令牌，我们将使用之前安装的 SPL 工具。运行`spl-token create-token`。这将创建令牌，也称为令牌标识符。复制并保存它。

![SPL Create Token](img/ce52de595d553979d77d75ddef987372.png)

### 创建帐户

我们上面所做的是创建一个令牌——现在我们需要一个帐户来存储它。

就像我们在银行账户里存钱一样，我们可以在不同的账户里有不同的加密货币。

要创建一个帐户来存储我们的令牌，请运行:`spl-token create-account <token-identifier>`。用您复制出来的实际令牌标识符替换`<token identifier>`。

![Create Account Store Token](img/e19ea9dbbdb1bc1b22cc0c898d1e8190.png)

### 铸造

铸币加密是通过验证数据、创建新块以及通过[利害关系证明](https://www.investopedia.com/terms/p/proof-stake-pos.asp)协议将信息记录到区块链上来生成新硬币的过程。

要使用 Solana 创建令牌，请运行:`spl-token mint <token-identifier> <token-amount>`。

![Mint Solana Token](img/0d71f6b9fadb902451f23d1f8c93d18d.png)

我为此铸造了一百万枚代币。你可以随意多做或少做。请注意，我可以通过再次运行`spl-token mint <token-identifier> <token-amount>`向帐户中刚刚铸造的代币铸造更多代币。

您可以随时通过运行:`spl-token balance <token-identifier>`来检查您的余额。

我们可以创建尽可能多的令牌，并为它们创建单独的帐户。

### 限制总供应量和燃烧

限制总供应量仅仅意味着你想阻止更多的代币被铸造。这其中的原因蕴含在经济学中。

当任何东西过剩的时候，它的价值就会下降。现在你可能会想，如果硬币已经过剩了呢？这是燃烧发生的地方。

在密码中燃烧意味着从流通中永久地去除一些代币。这通常是通过将有问题的令牌转移到烧录地址来完成的，烧录地址是一个永远无法从其检索令牌的钱包地址。这通常被描述为销毁令牌。

限制我们的总供应量是通过禁用我们的造币厂权力或权威来实现的。要为一个特定的令牌这样做，运行:`spl-token authorize <token-identifier> mint` `--disable`。

![Limiting Solana Token Supply](img/8083caaacf53dc242623e246e02c1b70.png)

这意味着我们没有办法在总供应量中增加额外的代币。

为了烧掉一些令牌，我们运行这个:`spl-token burn <token-account-addresss> <amount>`。

我将从我的 100 万枚代币中烧掉 20 万枚代币(总供应量)。

![Burning Solana Token](img/195d75bf78c77b6dfa4f53f511e792d2.png)

### 在钱包之间转移

钱包之间的转账就像我们定期使用银行账户一样简单；我们可以从一个账户转到另一个账户。你只需要钱包的地址。要获得关于传输命令的更多信息，请运行`spl-token transfer --help`。

它应该提供进行转账所需的所有必要信息。

![Running A Transfer](img/15fabc452f8d5a0a729069e193919290.png)

`spl-token transfer \[FLAGS\] [OPTIONS] <TOKEN_ADDRESS> <TOKEN_AMOUNT> <RECIPIENT_ADDRESS or RECIPIENT_TOKEN_ACCOUNT_ADDRESS>`

![Token Transfer Information](img/02dae2b6e23be9c1fe5a7e2b1516b776.png)

### 命名您的令牌

您可以为您的令牌选择一个名称。这就像在主环境中，在将另一个 JSON 块添加到[src/tokens/Solana . token list . JSON](https://github.com/solana-labs/token-list/blob/main/src/tokens/solana.tokenlist.json)中的大型令牌列表之后，发出一个 pull 请求一样简单。

您可以查看第[页](https://github.com/solana-labs/token-list#adding-new-token)了解更多信息。

## 结论

恭喜你。您已经完成了本指南的结尾部分。这是一个教程，重点是创建一个与索拉纳网络令牌，我希望你觉得有用。

除了满足要求之外，我们继续创建我们的令牌，然后创建一个帐户(钱包)，在其中存储令牌。我们还探讨了一些术语，如总供应量、燃烧和铸造，以帮助提供背景。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。