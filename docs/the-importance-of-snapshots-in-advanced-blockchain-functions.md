# 快照在高级区块链函数中的重要性

> 原文：<https://blog.logrocket.com/the-importance-of-snapshots-in-advanced-blockchain-functions/>

自创建以来，区块链的常见使用案例和应用发生了巨大变化。如今，区块链技术在金融支付、供应链、身份验证、保险和医疗保健等领域发挥着重要作用。

然而，为了长期保持相关性，区块链平台可能需要做出某些改变。例如， [Tron，一种流行的加密货币](https://tron.network)，正在将它的代币从以太坊迁移到它自己的 mainnet，从而为一个完全去中心化的互联网创建基础设施，它能够托管去中心化的应用。

因为区块链网络以存储大量数据而闻名，所以在任何给定的区块链中，由一个节点保存的数据量很可能是巨大的，并且将继续扩展。这对于加入网络的新节点来说是令人望而生畏的，它们必须在加入网络之前从整个网络下载数据。

在本文中，我们将探讨快照和反转状态在区块链应用程序中的重要性，了解我们如何利用它们来管理应用程序的存储。

## 快照简介

快照是包含区块链在任何特定时间的当前状态的文件。快照捕获完整的区块链分类帐，包括所有现有地址及其相关数据，如交易、费用、余额、元数据等。它保存在硬盘上的一个目录中。

当一个节点加入网络时，它必须下载整个区块链，从原始块到生成的最后一个块，其大小范围从 1.5 GB 到 140 GB。另一方面，快照允许节点通过仅获得最新状态来赶上网络。

为了更好地理解快照，请考虑在源区块链上收集所有帐户和智能合同的最新状态和事务处理需要多少劳动力。还要记住，只要网络在运行，全局状态就会随着新事务的接收而改变。

### 区块链快照:用例

让我们回顾一下区块链快照的一些常见使用情形。首先， [airdrops](https://www.investopedia.com/terms/a/airdrop-cryptocurrency.asp) 指的是将数字资产转移到区块链社区活跃成员的钱包里，通常是免费的或以社区服务作为回报。在这种情况下，快照用于找出在给定时间点持有令牌的社区成员。

代币迁移是指将代币持有者的余额从一个区块链转移到完全不同的区块链的过程。它主要用于硬分叉时，原始区块链的令牌持有者将获得新区块链的令牌。

替代硬币是加密货币的衍生产品或改进版本，是由于原始加密货币的明显缺点而产生的。在创建替代硬币之前，会拍摄加密货币区块链的快照，从而提供原始区块链的初始分布情况。替代币的一个例子是基于比特币的[比特币现金](https://www.investopedia.com/terms/b/bitcoin-cash.asp)。

## 入门指南

Ganache 是一个在本地开发以太坊和 Corda DApps 的工具。使用 Ganache，您可以在一个安全且可预测的环境中[构建、部署和测试您的 dapp](https://blog.logrocket.com/develop-test-deploy-smart-contracts-ganache/)。

### 以编程方式使用 Ganache

要安装 Ganache，请运行以下命令:

```
npm install ganache

```

接下来，将以下代码添加到您现有的 Ganache 项目中:

```
const provider = ganache.provider();
const [from, to] = await provider.send("eth_accounts");
const startingBalance = BigInt(await provider.send("eth_getBalance", [from] ));

// take a snapshot
const snapshotId = await provider.send("evm_snapshot");

// send value to another account (over-simplified example)
await provider.send("eth_subscribe", ["newHeads"] );
await provider.send("eth_sendTransaction", [{from, to, value: "0xffff"}] );
await provider.once("message"); // Note: `await provider.once` is non-standard

// ensure balance has updated
const newBalance = await provider.send("eth_getBalance", [from] );
assert(BigInt(newBalance) < startingBalance);

// revert the snapshot
const isReverted = await provider.send("evm_revert", [snapshotId] );
assert(isReverted);

// ensure balance has reverted
const endingBalance = await provider.send("eth_getBalance", [from] );
const isBalanceReverted = assert.strictEqual(BigInt(endingBalance), startingBalance);
console.log({isBalanceReverted: isBalanceReverted});

```

在上面的代码中，我们创建了一个提供者，并检查了一个帐户的余额，以此作为开始的参考点。然后，我们使用`evm_snapshot` RPC 方法对当前块的区块链状态进行快照；它不接受任何参数并返回一个 ID，我们将其存储为`snapshotId`。

接下来，我们订阅一个名为`newHeads`的事件。订阅是通过一个常规的 RPC 调用创建的，方法是`eth_subscribe`，第一个参数是订阅名称。如果成功，它将返回订阅 ID。对于每个与订阅匹配的事件，将发送一个通知，通知中包含与订阅 ID 相关的数据。

接下来，我们将值从一个帐户发送到另一个帐户，模拟区块链上的活动。之后，我们确认我们开始时的余额与现在的余额不同。

最后，我们使用`evm_revert` RPC 方法将区块链的状态恢复到之前的快照 。它只接受一个参数，即要恢复到的快照 ID。如果没有传递快照 ID，它将恢复到最新的快照。需要注意的是，在成功的`evm_revert`之后，不能再次使用相同的快照 ID。

然后，我们通过检查我们的起始余额是否与当前余额相同来确认我们的恢复成功，这意味着我们之前进行的交易被恢复。

### 使用 Ganache 命令行

Ganache CLI 是以太坊开发工具 Truffle 套件的一部分，是 Ganache 的命令行版本。要安装 Ganache CLI，请运行以下命令:

```
npm install -g ganache-cli

```

要确认安装是否成功，请运行以下命令:

```
ganache-cli --version

```

如果 Ganache CLI 安装成功，版本号将出现在您的屏幕上。您可以通过运行以下命令，通过 Ganache CLI 拍摄快照:

```
ganache-cli --database.dbPath="./data/save/" -i="5777" -d -m="YOUR_12_WORDS_HERE"

```

`--database.dbPath`指定保存链数据库的目录路径。如果数据库已经存在，Ganache CLI 将初始化该链，而不是创建新的链。

`-I`指定 RPC 方法返回的网络 ID。默认为当前时间或分叉区块链的网络 ID(如果已配置)。`-d`根据预定义的确定性种子生成确定性地址，`-m`使用特定的硬盘钱包助记符生成初始地址。

如果成功，将生成一些文件并保存到`./data/save`目录。如果数据已经存在于该目录中，它将返回到相同的状态。

## 摘要

区块链行业是一个快节奏的行业。利用区块链技术的平台必须以极快的速度适应，才能保持相关性。因此，这些平台可能必须从一个区块链实例迁移到另一个实例，以保持竞争力和安全性。

区块链上的数据迁移包括从源区块链拷贝数据，然后在目标区块链上重新创建数据。然而，这个数据可能非常大，强调需要减少区块链下载时间。

快照是包含区块链在任何给定时间点的状态的文件。快照允许新节点通过仅从区块链下载最新状态来赶上网络，它们通常用于引导区块链、侧链和新节点。在[比特币论坛](https://bitcointalk.org/index.php?topic=563972.0)中介绍了一个比特币替代币的快照文件格式和引导程序。我希望你喜欢这个教程，如果你有任何问题，一定要留下评论。编码快乐！

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。