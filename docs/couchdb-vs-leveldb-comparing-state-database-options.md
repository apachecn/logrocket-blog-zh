# CouchDB 与 LevelDB:比较状态数据库选项

> 原文：<https://blog.logrocket.com/couchdb-vs-leveldb-comparing-state-database-options/>

## 介绍

Hyperledger Fabric 是一个开源的许可区块链框架，用于开发企业区块链解决方案和应用程序。Hyperledger Fabric 项目是 [Hyperledger](https://hyperledger-fabric.readthedocs.io/en/release-2.2/blockchain.html#what-is-hyperledger-fabric) 的主要区块链项目之一，Hyperledger 是一个多项目合作项目，由 Linux 基金会于 2015 年启动，旨在推进跨行业区块链技术。今天，Hyperledger Fabric 现在有超过 [120，000 个贡献组织](https://hyperledger-fabric.readthedocs.io/en/release-2.2/blockchain.html#what-is-hyperledger-fabric)和[超过 15，000 名工程师贡献者](https://hyperledger-fabric.readthedocs.io/en/release-2.2/blockchain.html#what-is-hyperledger-fabric)共同推进该项目。

Hyperledger Fabric 具有模块化(即插即用)架构，可以达到每秒 1000 次以上的事务(TPS)，可以通过[升级达到 20000 TPS](https://cointelegraph.com/news/upgraded-hyperledger-fabric-sees-7-fold-increase-in-transaction-speed)。Hyperledger Fabric 还提供了一组特殊的功能，使其有别于其他区块链技术，并且更容易被企业采用。这些功能是:

Hyperledger Fabric 已经有了很多实现，并广泛应用于银行和金融、国际贸易和物联网等行业。Hyperledger Fabric 由许多组件组成，如签署者、提交者和数据库。

### 在 Hyperledger 结构网络中进行交互的选项

要在 Hyperledger 结构网络中进行交互，需要使用链码(也称为智能合同)。这些智能合约可以用不同的编程语言编写，包括 Go、Node.js 和 Java。

Hyperledger Fabric 在数据库中存储由链代码执行的交易的状态。Hyperledger 结构网络存储的记录主要有两种类型:

*   **交易日志**:这是 Hyperledger Fabric 网络的区块链方面，包括导致 Hyperledger Fabric 网络当前状态的所有交易的记录。存储在事务日志中的数据是不可变的，并且存储在 LevelDB 数据库中
*   **世界状态**:这是给定时间点分类账的当前值。默认情况下，结构网络的主数据库是 LevelDB 数据库，并且仅当给定事务完成时才被填充；然而，它可以被一个治疗床数据库所取代

在本文中，我们将讨论如何在 CouchDB 和 LevelDB 数据库中选择一个作为您的世界状态数据库，包括以下主题:

尽情享受吧！

## 为什么不用 PostgreSQL 或者 MySQL 之类的数据库呢？

Hyperledger Fabric 仅支持 LevelDB 和 CouchDB，不支持 PostgreSQL、MongoDB 或 MySQL 等数据库。Hyperledger 论坛中的[讨论指出，为了提供对可插拔数据库的支持，必须对网络和数据库本身进行许多更改。该项目已经暂时从 Hyperledger 的开发管道中删除。](https://lists.hyperledger.org/g/fabric/topic/replacing_couchdb_with_a/74953512)

但是，仍然有一些方法可以将 PostgreSQL 这样的数据库设置为您的状态数据库。按照下面的说明，您可以[派生 Fabric 项目并插入一个数据库](https://docs.google.com/document/d/1ZdxPWdxUwEDwRAKY8tewgjqh2qTZCm6RZ6tU9ccm4dM/edit?usp=sharing),如 PostgreSQL、MySQL 或 MongoDB。这个来自 IBM 的[示例还展示了一种可以用来在 Hyperledger Fabric 项目中设置 PostgreSQL 的方法。](https://developer.ibm.com/patterns/blockchain-postgres-fabric-certificate-wallet/)

## 什么是 LevelDB？

[LevelDB 数据库](https://github.com/google/leveldb)是一个快速数据库，允许您存储键值对。LevelDB 数据库由 Sanjay Ghemawat 和 Jeff Dean 在 Google 编写，是目前 Hyperledger Fabric 用于存储事务日志的唯一数据库。如前所述，它也是用于在 Hyperledger Fabric 中存储世界状态的默认数据库。

LevelDB 键值对存储在任意字节数组中。LevelDB 只支持`Key`、`Key range`和组合键查询，即`Put(key, value)`、`GET(key, value)`和`Delete(key)`。

不支持 SQL 查询、索引或关系数据模型，因为 LevelDB 不是 SQL 数据库。

### LevelDB 的优势

*   LevelDB 是一个简单、快速的数据库，与 CouchDB 相比开销更小
*   数据用键存储和映射
    *   您可以执行简单的操作，如`Put(key, value)`、`GET(Key, Value)`和`Delete(Key)`，这使得 LevelDB 使用起来不那么复杂
*   LevelDB 支持对数据、快照进行向前和向后迭代，并以原子批处理的方式进行更改

### LevelDB 的限制

*   不支持索引和查询
*   它只允许简单的操作，如`Put(key, value)`、`GET(key, value)`和删除(键)
*   没有关系数据模型的熟悉特性，因为它不是 SQL 数据库。数据只能存储在键值对中
*   从 LevelDB 数据库中查询大型数据集既低效又复杂，因为不允许复杂的查询和索引

## 什么是 CouchDB？

CouchDB 数据库是一个开源的、面向文档的数据库，用 Erlang 实现，以 JSON 格式收集和存储数据。Hyperledger Fabric 用户可以用 CouchDB 数据库替换他们默认的 LevelDB world state 数据库，这也是唯一可用的替代方法。

CouchDB 是一个 NoSQL 数据库，允许对存储的 JSON 内容进行丰富的查询。CouchDB 数据库中的默认查询语言是 JavaScript，数据可以在没有设置模式的情况下以 JSON 格式存储。

CouchDB 支持索引和分页。您还可以基于键进行查询，就像 LevelDB 数据库一样。

### CouchDB 的优势

*   CouchDB 允许 JSON 查询和索引，这使得比 LevelDB 更容易满足您的 Hyperledger Fabric 网络审计和报告需求
*   作为一个面向文档的数据库，CouchDB 允许您将数据作为数组或字典存储在数据库中
*   CouchDB 数据通过 HTTP URI 公开。这使得对您的数据执行 HTTP 操作(`GET`、`DELETE`、`PUT`、`POST`)成为可能
*   CouchDB 使用索引使得查询链代码的大型数据集更加高效和灵活

### CouchDB 的局限性

*   CouchDB 作为一个独立的数据库与 Hyperledger 结构网络并行运行
*   在数据库管理、设置、配置等方面需要更多的投入。运行网络

## 为您的下一个 Hyperledger Fabric 项目选择州数据库

在选择州数据库之前，您应该知道您必须决定一个数据库，因为 Hyperledger Fabric 不允许您在 Hyperledger Fabric 网络启动后更改数据库。

如果您正在运行一个具有少量事务的简单 Hyperledger Fabric 项目，LevelDB 的易用性和速度使其非常适合使用。

然而，对于更复杂的项目，最好使用 CouchDB 数据库。CouchDB 拥有 LevelDB 数据库的所有特性，比如基于键获取和设置数据，但是还提供了对其他特性的访问，比如以 JSON 格式存储数据、索引、发出查询、分页等。，所有这些都使处理数据变得更加容易。

CouchDB 提供的特性使得管理具有高交易量的 Hyperledger 结构网络变得更加容易。CouchDB 还使得在运行 Hyperledger Fabric 网络的现代组织中完成审计和报告需求变得更加容易。

## 结论

在本文中，我们讨论了 Hyperledger Fabric、CouchDB 和 LevelDB 中可用的世界状态数据库选项。我们解释了每个数据库的优点和局限性，并讨论了如何为您的下一个 Hyperledger Fabric 项目选择一个数据库。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。