# . eth.link vs .eth.limo:比较。eth 域名解析

> 原文：<https://blog.logrocket.com/eth-link-vs-eth-limo/>

DNS(域名系统)诞生于大约四十年前，它提供了为网络中的节点分配人类可读名称的能力。没有名字，这样的节点将不得不通过 IP 地址来识别，这是非常难以记忆，书写和阅读。

ENS(以太坊名称服务)旨在解决以太坊区块链的同样问题。它将人类可读的名称(如“name.eth”)映射到以太坊地址或内容散列，这对于手写来说是非常容易出错的。

## ENS 简介

根据官方文档，ENS 被定义为“一个分布式的、开放的、可扩展的命名系统。”作为一个分散的系统，ENS 不受任何中央实体或党派的控制。因此，不存在单点故障。此外，ENS 支持 IPFS(星际文件系统)，这是一种在分布式文件系统中存储和共享数据的对等协议。

ENS 建立在两个基本组件上:注册中心和解析器。前者是一个智能契约，记录了在 ENS 上注册的所有域，以及关于每个域的一些元数据(所有者、解析器和该域下所有记录的缓存时间)。解决者也是聪明的契约。它们将人类可读的名称(即域名)转换成十六进制地址，反之亦然。

因此，在 ENS 中解析名称需要两个步骤:

1.  向注册中心询问对应于给定域的解析器的地址
2.  查询解析器(例如，询问对应于给定域的地址)

ENS 支持以太坊域，。eth，以及最流行的“DNS”域名，如。com，。io 和. org。

这听起来不错，但是，在撰写本文时，大多数浏览器都无法解决这个问题。eth 域没有一点帮助。一些第三方扩展可以完成这项工作，但是它们需要额外的步骤来设置。由于这个原因，到目前为止已经创建了两个网关:eth.link 和 eth.limo。

在这篇博文中，我们将对它们进行比较。

## 以太网链接

eth.link 是从 DNS 访问 ENS 中的信息的集中方式，由 Cloudflare 管理，cloud flare 是一家提供 CDNs(内容交付网络)和 DDoS(分布式拒绝服务)缓解服务的公司。

在幕后，eth.link 使用通配符 DNS 记录(`*.eth.link`)来捕获所有 ENS 域的请求。换句话说，eth.link 利用了。链接域以获取有关给定。eth 域。例如，`mydomain.eth.link`的 DNS A 记录请求将触发通配符`*.eth.link`记录，并返回`mydomain.eth`的 ENS 中的 A 记录。

eth.link 完全在 Cloudflare Workers 上运行，它本质上是一个在世界各地的几个服务器(由 Cloudflare 拥有)上执行的运行时。在本文的其余部分，为了简单起见，我们将把“工人”称为服务器本身。

每当用户试图访问`mydomain.eth.link`，工作人员就向 ENS(即以太坊区块链)查询 CID(内容标识符)。cid 直接来源于资源的内容，因此它们通常被称为自描述标识符。它们基本上是描述内容的散列，比如一个网页。

一旦建立了`mydomain.eth.link`的 CID，工作人员就用 CID 查询 IPFS 网关，以便获得网页的内容。然后将内容转发给用户。

eth.link 的主要问题当然是它的集中化，这使得 Cloudflare 掌握了很多控制权。这也使得它更容易出现以下问题:

1.  单点故障和停机，因为 eth.link 在 Cloudflare 的基础架构上运行
2.  缺乏透明度:Cloudflare 拥有基础架构和与之相关的所有数据(流量、正常运行时间和停机时间等)。因此，社区只知道 Cloudflare 与他们共享什么
3.  审查制度
4.  中间人攻击:能够访问 Cloudflare 服务器的恶意员工或攻击者可以操纵要提供的内容

为了解决上面列出的问题，另一个网关诞生了:eth.limo。

## 豪华轿车公司

eth.limo 是 eth.link 的分散替代方案，与 eth.link 类似，eth.limo 基于通配符 DNS 记录，`*.eth.limo`。然而，它不是由单个组织管理，而是由 DAO(去中心化自治组织)提供动力，因此其内部工作方式是在智能合同中定义的，不能单方面改变。

流程与上述流程相同。当用户试图访问`mydomain.eth.limo`时，LIMO 服务从以太坊的主网络获取网页的 CID，然后返回所请求网页的内容。该项目仍处于非常早期的阶段，但目标是能够让任何人部署个人豪华轿车服务。

开发人员也在考虑让任何人为官方网络操作一个节点，以便建立一个社区运营的 CDN。这与 eth.link 的方法截然不同，在 eth . link 的方法中，网络的节点完全在 Cloudflare 的控制之下。

早期测试表明 eth.limo 比 eth.link 快，尽管负载仍然相差很大，因为 eth.limo 仍在 alpha 测试中:

![Eth.limo Is Still In Alpha Test](img/6d8645da5e861682e0efe59436a424fc.png)

来源:[https://twitter.com/eth_limo/status/1433093424178253828?s=20](https://twitter.com/eth_limo/status/1433093424178253828?s=20)

eth.limo 比 eth.link 年轻。因此，它仍在测试和开发中。然而，它的路线图侧重于用户隐私和社区治理和监督。

## 结论

在这篇文章中，我们看到了两种解决方案的简单比较。使用 ENS 的 eth 域。一方面，我们拥有 Cloudflare 支持 eth.link 的经验和基础设施能力。另一方面，我们有一个围绕去中心化构建的社区驱动型项目，重点关注用户隐私 eth.limo。

即使这两个项目在工作方式上很相似，但也有很大的不同。eth.limo 肯定不如 eth.link 成熟，但它有一个非常雄心勃勃的计划。由于它的核心原则更加符合以太坊和区块链的原则，我认为它在未来会成为一个非常有趣的项目。然而，目前它才刚刚起步，只使用 eth.limo 可能是一个有风险的选择。

另一方面，eth.link 更“适合生产”，因为它存在的时间更长。因此，如果我要开始一个使用. eth 域的个人项目，我可能会想同时使用这两个域，目标是在 eth.limo 变得更加稳定时，迁移到只使用 eth . limo。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在撰写本文时，这是仅有的两种可能性。由于选择能力是向分散化应用程序和 web 过渡的重要组成部分，我们将会看到未来是否会出现新的替代方案。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。