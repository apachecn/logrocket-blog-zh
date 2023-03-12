# 使用工厂模式克隆 Solidity 智能合同

> 原文：<https://blog.logrocket.com/cloning-solidity-smart-contracts-factory-pattern/>

## 介绍

在区块链开发中，将数据集挖掘到区块链是一个非常昂贵的过程，因为挖掘到下一个区块的每个新数据都要收费。在区块链上部署智能合约会导致将合约数据挖掘到下一个块中，如果部署在以太坊区块链上，这将需要一些在以太中收取的汽油费。

本文将实际演示如何使用工厂模式以正确的方式部署智能契约的多个实例。此外，我们将讨论工厂模式，它的好处，以及何时使用它。

## 先决条件

要阅读本文，您应该先了解使用 Solidity 进行智能契约开发的知识。

本文将涵盖以下内容:

## 工厂模式

工厂设计模式是一种众所周知的编程模式。这个概念很简单:不是直接创建对象的实例，而是由一个单独的对象(工厂)来完成。

这与可靠性是一样的，因为智能合约是对象。在 Solidity 中，工厂是一个将部署其他契约的多个实例的契约。

我们有时需要创建不同类型的对象，但是在运行时执行代码之前，我们不知道我们将实例化哪种类型的对象。在这种情况下，工厂技术就派上了用场。

通常，一个基本的工厂契约应该能够部署同一个契约的多个实例，将这些创建的实例存储在区块链上，并在需要时检索它们。您可能希望添加更多的功能来管理已部署的协定，例如检索特定的协定实例、禁用协定实例等等。

### 在 Solidity 中使用工厂模式的好处

以下是在 Solidity 中使用工厂模式的好处:

1.  部署多个高能效合同
2.  跟踪所有已部署的合同
3.  节省多项合同部署的成本

### 何时使用工厂模式

工厂模式在以下情况下有效:

*   当我们需要在运行时快速生成智能契约的多个实例时
*   当我们处理大量具有相同功能的合同时

### Solidity 中工厂模式的类型

我们将讨论在 [Solidity smart contracts](https://blog.logrocket.com/writing-smart-contracts-solidity/) 中通常实现的两种工厂模式。这些模式包括:

*   普通工厂模式——该工厂模式部署其他契约的多个实例，而不进行任何优化以节省每次部署的时间
*   克隆工厂模式——这种工厂模式部署了其他契约的多个实例，强调优化以节省每次部署的时间

### 我们的第一份可靠智能合同

我们将创建一个简单的智能契约，工厂契约将使用它来部署它的多个实例:

```
// SPDX-License-Identifier: MIT
pragma solidity >0.4.23 <0.9.0;

contract Foundation {
    string public name;
    address public owner;

    constructor(
        string memory _name,
        address _owner
    ) public {
        name = _name;
        owner = _owner;
    }

}

```

因为以太坊是一个开源项目，所以第一行显示了合同的开源许可。第二行指定了执行本合同所需的可靠性版本。

接下来，我们建立`Foundation`契约，它类似于其他面向对象编程语言中的一个类。这里的构造函数用中作为参数提供的值初始化协定的状态变量。当我们创建一个契约实例时，会调用`constructor`函数。

### 撰写我们的第一份工厂合同

基础契约目前无法创建。因此，我们将创建一个工厂契约，它将使用正常的工厂模式创建基础契约的各个实例。

下面是一份正常的工厂合同应该是什么样子的:

```
// SPDX-License-Identifier: MIT
pragma solidity >0.4.23 <0.9.0;
import "./Foundation.sol";
contract FoundationFactory {
    Foundation[] private _foundations;
    function createFoundation(
        string memory name
    ) public {
        Foundation foundation = new Foundation(
            name,
            msg.sender
        );
        _foundations.push(foundation);
    }
    function allFoundations(uint256 limit, uint256 offset)
        public
        view
        returns (Foundation[] memory coll)
    {
        return coll;
    }
}

```

这里，这段代码导入了`Foundation`契约，我们希望为其创建多个实例。`_foundations`变量保存创建的`Foundation`契约的实例。

`createFoundation`函数部署`Foundation`合同的一个实例并将其存储在区块链中，而`allFoundations`函数检索存储在区块链中的`Foundation`合同的所有实例。

### 正常工厂模式的一个缺点

CREATE 操作码的 gas 成本目前为 32，000 Gwei。每部署一个`Foundation`合同实例，收取 32,000 Gwei 的燃气费。

正常工厂模式的主要缺点是[高昂的天然气成本](https://ethereum.stackexchange.com/q/84764/33305)。这就是克隆工厂模式派上用场的地方。

## 克隆工厂模式:部署我们的 Solidity smart 契约的多个实例的正确模式

### 为什么是克隆工厂模式？

因为我们部署的是同一个契约，所以契约的每个实例都有相同的字节码。因此，存储每个部署的所有字节码会重复地增加字节码的 gas 成本的浪费。

这个问题的解决方案是一种机制，只部署一个`Foundation`契约的实例，让所有其他`Foundation`契约的实例充当代理，将调用委托给第一个`Foundation`契约的实例，并允许函数在代理契约的上下文中运行。这样，`Foundation`契约的每个实例都有自己的状态，并简单地使用我们建立的`Foundation`契约的实例作为库。

由[彼得·默里](https://github.com/yarrumretep)、[内特·韦尔奇](https://github.com/nathanwelch)、[乔·梅瑟曼](https://github.com/JAMesserman)创建的 [eip-1167](https://eips.ethereum.org/EIPS/eip-1167) 提供了这种机制。根据它的[文档](https://eips.ethereum.org/EIPS/eip-1167)，“这个标准指定了一个最小的字节码实现，它将所有调用委托给一个已知的固定地址，以一种不可变的方式简单而廉价地克隆契约功能。”

[克隆工厂契约](https://github.com/optionality/clone-factory)是 [eip-1167](https://eips.ethereum.org/EIPS/eip-1167) 标准的参考实现。

### 使用克隆工厂模式

要实现克隆工厂契约，您必须安装克隆工厂包，如下所示:

```
npm install @optionality.io/clone-factory

```

现在我们可以在我们的`FoundationFactory`上实现克隆工厂契约，如下所示

```
// SPDX-License-Identifier: MIT
pragma solidity >0.4.23 <0.9.0;
import "./Foundation.sol";
import "@optionality.io/clone-factory/contracts/CloneFactory.sol";
import "zeppelin-solidity/contracts/ownership/Ownable.sol";

contract FoundationFactory is Ownable, CloneFactory {

  address public libraryAddress;

  event FoundationCreated(address newFoundation);

  function FoundationFactory(address _libraryAddress) public {
    libraryAddress = _libraryAddress;
  }

  function setLibraryAddress(address _libraryAddress) public onlyOwner {
    libraryAddress = _libraryAddress;
  }

  function createFoundation(string _name) public onlyOwner {
    address clone = createClone(libraryAddress);
    Foundation(clone).init(_name);
    FoundationCreated(clone);
  }
}

```

这里，我们从 OpenZeppelin 库导入了`Ownable`契约，以便利用它的`onlyOwner`修饰符，因为它是经过审计的，并且比我们自己编写契约更安全。

有了上面的代码片段，我们就有了一个契约，它会以较低的天然气成本将所有呼叫委托给契约`libraryAddress`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

虽然这是一种更好的机制，但是您应该注意以下几点:

*   确保你的主契约不会自毁，因为这会导致所有克隆体停止工作，从而冻结它们的状态和平衡
*   确保在构造函数中预初始化主协定，因为只有在主协定创建期间才会调用主协定构造函数

### 部署您的克隆工厂合同

现在，如果您正在使用 Truffle 进行开发，您可以通过使用以下内容创建一个新的迁移文件来部署工厂契约:

```
const FoundationFactoryContract = artifacts.require("FoundationFactory");
module.exports = function(deployer) {
 deployer.deploy(FoundationFactoryContract);
}

```

如果你正在用 Hardhat 开发，那么`ethers.getContractFactory`方法已经覆盖了你，所以没有必要自己创建一个工厂契约。

您可以继续部署`Foundation`契约，如下所示:

```
const hre = require("hardhat");
async function main() {
  const Foundation= await hre.ethers.getContractFactory("Foundation");
  const foundation= await NFTMarket.deploy();
  await foundation.deployed();
  console.log("Foundation contract deployed to: ", foundation.address);
}

```

## 普通工厂和克隆工厂模式的比较

为了检查`CloneFactory`和普通工厂之间的 gas 成本差异，我们将每个契约部署到一个 testnet，然后执行每个契约的`createFoundation`函数，然后在 explorer 中检查事务散列以了解使用了多少 gas。以下是执行`createFoundation`功能收取的燃气费:

克隆工厂
汽油费:
基数:12.959896517 Gwei

正常出厂
气费:
基数:25.529794514 Gwei

## 结论

在本文中，我们探讨了工厂模式、它的好处、类型，以及它最适合我们的智能合约的时间。此外，我们已经确定克隆工厂模式是部署我们的 Solidity smart 契约的多个实例的正确模式，因为它的成本效率很高。

希望这篇文章对你有所帮助。你可以在 [T](https://twitter.com/vuemarshmello) [witter](https://twitter.com/vuemarshmello) 和 [LinkedIn](https://www.linkedin.com/in/emmanuel-john-83bb19192/) 上关注我，我会在那里分享关于 web3 和 web 开发的日常技巧。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。