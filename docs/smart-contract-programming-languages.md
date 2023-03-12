# 区块链 5 大智能合约编程语言

> 原文：<https://blog.logrocket.com/smart-contract-programming-languages/>

每个人都在谈论智能合约、区块链、分散金融和加密货币。然而，如果没有开发商，这些都不会存在，所以对区块链开发商的需求继续激增。

许多编程语言允许您编写智能合约。在本文中，我们将探索最常用的智能契约编程语言。

## 什么是智能合约？

智能合同是一种计算机程序，它根据合同条款自动执行操作，无需中介。例如，如果您想使用智能合同机制购买一块土地，您的土地所有权文件将在付款完成后立即发送给您。你不需要信任第三方网站在支付后转让所有权。

此外，你可以在分散交易所(DEX)中看到智能合约的作用，如 [PancakeSwap](https://pancakeswap.finance/) ，它允许你用你的代币交换另一个代币。如果你有 BNB，你可以很容易地把它换成以太坊，而不必与客户支持人员交谈。

智能合约编程语言允许您编写在区块链上实现智能合约的程序。

那么，你问，什么是顶级的智能合同语言？让我们开始吧。

## 1.固态

Solidity 是一种面向对象的静态类型编程语言，旨在允许开发人员创建智能合同。

Solidity 是基于现有的编程语言设计的，如 [C++](https://en.m.wikipedia.org/wiki/C%2B%2B) 、 [Python](https://en.m.wikipedia.org/wiki/Python_(programming_language)) 和 JavaScript，所以它使用了这些语言中类似的语言结构，最有可能使其易于开发人员采用。

下面是一个具有可靠性的智能合同示例:

```
pragma solidity ^0.8.7;

contract MyContract {

    constructor() public{

        value = "My value";
    }

    string public value;

    function get() public view returns (string memory){
        return value;
    }

    function set(string memory _value) public{
        value = _value;
    }
} 

```

如果您是 JavaScript 或 C++开发人员，这看起来会很熟悉。

Solidity 作为第一种智能契约编程语言，已经被市场广泛采用，并被用于构建许多分散式应用程序。它是为了在以太坊上编写智能合约而开发的，就像 Java 和 [Java 虚拟机(JVM)](https://docs.oracle.com/en/java/javase/17/vm/java-virtual-machine-technology-overview.html) 一样，Solidity 运行在[以太坊虚拟机(EVM)](https://ethereum.org/en/developers/docs/evm/) 上。

### 用 Solidity 编写智能合同的优势

*   Solidity 有一个庞大的、容易接近的社区。因为 [Solidity 是第一个智能契约编程语言](https://blog.logrocket.com/writing-smart-contracts-solidity/)，并且是专门为以太坊网络上的智能契约编程开发的，所以它获得了广泛的社区支持，使得新开发人员在遇到困难时可以很容易地获得帮助
*   Solidity 是图灵完备的，所以它不仅限于运行少数算法——它可以用来计算所有可计算的函数
*   可靠性提供了大多数现代编程语言中都有的概念。它有函数，字符串操作，类，变量，算术运算等。此外，Solidity 支持映射数据结构，它充当哈希表，由键类型和键值对组成
*   如果你已经知道如何用 Python、C++和 JavaScript 等流行的编程语言编程，Solidity 的学习曲线并不陡峭，因为它的大部分语法都是从这些语言借鉴来的

### 用 solidity 编写智能合同的缺点

*   Solidity 是一门较新的语言，尽管社区已经在帮助开发库和它的工具，但这门语言还有很多工作要做，你必须完全自己实现

区块链使用 Solidity 的例子有 [Tendermint](https://tendermint.com/) 、[币安智能链](https://docs.binance.org/smart-chain/wallet.html)、[以太坊经典](http://ethereumclassic.org/)、 [Tron](http://tron.network/) 、[雪崩](https://www.avax.network/)、[对手方](http://www.counterparty.io/)、[海德拉](https://hedera.com/)。

## 2.锈

根据[栈溢出调查](https://insights.stackoverflow.com/survey/2020#most-loved-dreaded-and-wanted) , [Rust](https://www.rust-lang.org/) 连续五年成为最受欢迎的编程语言之一。

![Stack Overflow Developer Survey](img/b1501e6102e98a3a0a1b82284c36a6b7.png)

Rust 是一种低级的静态类型编程语言，速度快，内存效率高——在一个可伸缩性没有商量余地的行业，Rust 作为一种语言找到了自己的位置。Rust 是一种相对较新的编程语言，它拥有强大的功能，同时保留了简单性、存储效率、可靠性和复杂性的组合。

默认情况下，Rust 假定了最佳的设计和开发实践，并且如果您愿意的话，还会给你一个机会来改变它们。Rust 没有垃圾收集器，这意味着在运行时不会发生意外事件(由语言引起)。

所有这些因素使得 Rust 成为编程区块链的绝佳选择。这并不奇怪，最快的区块链之一，索拉纳，是建立在生锈的核心。

Rust 的编译器有一个彩色编码的输出和一个更详细的错误输出来帮助调试。

在许多情况下，Rust 通过突出显示相关代码来显示错误的原因以及在哪里可以找到错误，并附有解释。此外，在某些情况下，它还提供了对错误的修复。

下面是一个使用 Rust 的智能合约示例:

```
use borsh::{BorshDeserialize, BorshSerialize};
use near_sdk::{env, near_bindgen};
use near_sdk::collections::UnorderedMap;

#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;
pub struct StatusMessage {
    records: UnorderedMap<String, String>,
}
#[near_bindgen]
impl StatusMessage {
    pub fn set_status(&mut self, message: String) {
        let account_id = env::signer_account_id();
        self.records.insert(&account_id, &message);
    }
    pub fn get_status(&self, account_id: String) -> Option<String> {
        return self.records.get(&account_id);
    }
}

```

Rust 包含一些面向对象的特性；您可以创建结构和数据。但是与其他面向对象的语言不同，它不展示继承性。

使用 Rust 的智能合约区块链包括[索拉纳](https://coinsem.com/solana-all-you-need-to-know/?amp=1)、[波尔卡多特](https://polkadot.network/)、区块链附近的。你可以在这个 GitHub 库中找到许多用 Rust 构建的区块链项目。

### 用 Rust 编写智能合约的优势

*   因为 Rust 是一种低级编程语言，并且是为效率而设计的，所以您可以构建具有高吞吐量的分散式应用程序
*   信任不变性允许您编写可预测的程序，这是构建在区块链上的应用程序类型所需要的

### 用 Rust 编写智能合约的缺点

*   Rust 是一种新的编程语言，有一些新概念。可能需要一些时间来习惯使用它，尤其是如果您是编程新手
*   与 Python 和 JavaScript 等其他语言相比，几乎所有语言的库都很有限
*   很啰嗦。使用 Rust，你将不得不显式地陈述每一个程序结构，这意味着你将比用 JavaScript 和 Python 这样的语言写更多的代码

## 3.Java Script 语言

JavaScript 是一种通用编程语言，它在区块链领域找到了一席之地。因为 JavaScript 是一种入门级语言，大多数区块链倾向于创建一个 JavaScript 包装器或库，以允许开发人员轻松地跳入生态系统，并尽快开始构建令人惊叹的产品。

[Hyperledger Fabric](https://github.com/hyperledger/fabric-chaincode-node) 是一个区块链，允许你用一些编程语言建立一个智能契约，包括 JavaScript (Node.js)。

这里有一个[示例，展示了智能合同](https://hyperledger.github.io/fabric-chaincode-node/main/api/)在 HyperLedger Fabric 中的样子:

```
'use strict';

const { Contract } = require('fabric-contract-api');
const util = require('util');

/**
 * A program to support updating values in a smart contract
 */
class UpdateSmartContractValue extends Contract

    constructor(){
        super('UpdateSmartContractValue');
    }

    async transactionA(ctx, newValue) {
        let oldValue = await ctx.stub.getState(key);
        await ctx.stub.putState(key, Buffer.from(newValue));
        return Buffer.from(newValue.toString());
    }

    async transactionB(ctx) {
      //  .....
    }

};

module.exports = UpdateSmartContractValue

```

该社区构建了 [web3.js](https://web3js.readthedocs.io/en/v1.5.2/) ，这是一个库集合，允许您使用 HTTP、WebSocket 或 IPC 与以太坊智能合约进行交互。

Solana 基金会也围绕 Solana Rust 程序构建了 JavaScript 包装器，允许 JavaScript 开发者尽快在区块链上构建 Dapps。

已经用 JavaScript 构建了几个工具来帮助区块链开发，但不是用于区块链的核心，因为它的[弱类型检查](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)。

### 用 JavaScript 编程智能合约的优势

*   它是一种流行且成熟的编程语言，拥有大量的社区支持
*   与其他语言相比，你将享受更快的开发时间，尤其是新的语言

### 用 JavaScript 编写智能合同的缺点

*   动态类型:对于像智能合同这样的关键任务应用程序，类型安全是一个重要的特性。当大多数开发人员喜欢为构建在区块链上的应用程序使用静态类型语言时，JavaScript 实现了动态类型安全

## 4.把衣服洗了

Vyper 是一种面向契约的类似 Python 的编程语言，目标是[以太坊虚拟机(EVM)](https://ethereum.org/en/developers/docs/evm/) 。它具有特定于协定的特性，例如侦听器的事件通知程序、自定义全局变量和全局常量。

Vyper 的建立是为了解决 Solidity 中存在的安全问题。它的开发是为了补充坚固性，而不是取代它。

Vyper 故意减少了 Solidity 的功能，以使契约更安全，更容易审计，因此，它不支持修饰符、继承、内联汇编、函数和运算符重载、递归调用、无限长循环和二进制定点。

### 优势

*   [使用 Vyper](https://blog.logrocket.com/how-to-build-secure-smart-contracts-using-vyper/) 构建安全的智能合同是可能的，也是自然的，因为它们不太容易受到攻击
*   Vyper 代码是人类可读的。对 Vyper 来说，读者的简单性比作者的简单性更重要
*   Vyper 的一个显著特点是能够计算与特定 Vyper 函数调用相关的气体消耗的精确上限

### 不足之处

*   Vyper 对纯函数的支持有限，因此任何标有`constant`的东西都不允许改变状态

如果你看过或者使用过 Python 代码，那么你几乎就能编写出 [Vyper 代码](https://vyper.readthedocs.io/en/v0.1.0-beta.12/testing-contracts.html)。

这里有一个来自[文档](https://vyper.readthedocs.io/en/stable/)的例子，只是为了让你有个感觉:

```
class VyperContract:
    """
    An alternative Contract Factory which invokes all methods as `call()`,
    unless you add a keyword argument. The keyword argument assigns the prep method.
    This call
    > contract.withdraw(amount, transact={'from': eth.accounts[1], 'gas': 100000, ...})
    is equivalent to this call in the classic contract:
    > contract.functions.withdraw(amount).transact({'from': eth.accounts[1], 'gas': 100000, ...})
    """

    def __init__(self, classic_contract, method_class=VyperMethod):
        classic_contract._return_data_normalizers += CONCISE_NORMALIZERS
        self._classic_contract = classic_contract
        self.address = self._classic_contract.address
        protected_fn_names = [fn for fn in dir(self) if not fn.endswith('__')]
        for fn_name in self._classic_contract.functions:
            # Override namespace collisions
            if fn_name in protected_fn_names:
                _concise_method = mk_collision_prop(fn_name)
            else:
                _classic_method = getattr(
                    self._classic_contract.functions,
                    fn_name)
                _concise_method = method_class(
                    _classic_method,
                    self._classic_contract._return_data_normalizers
                )
            setattr(self, fn_name, _concise_method)

    @classmethod
    def factory(cls, *args, **kwargs):
        return compose(cls, Contract.factory(*args, **kwargs))

```

## 5.尤尔

Yul 是一种中间编程语言，被编译成字节码以满足不同后端的需求。Solidity 编译器有一个使用 Yul 作为中间语言的实验实现。Yul 用于独立模式和 Solidity 内部的在线装配。

Yul 计划支持 EVM 和 [ewasm](https://github.com/ewasm) (以太坊风味的 WebAssembly)。它被设计成两个平台的可用共同点。

Yul 是高级优化阶段的一个很好的目标，可以使 EVM 和 ewasm 平台同等受益。

### 使用 Yul 的优势

*   可读性:用 Yul 编写的程序是可读的，即使代码是由 Solidity 的编译器生成的。Yul 提供了高级结构，如循环、函数调用以及 if 和 switch 语句
*   由于从 Yul 代码到字节码的代码转换，Yul 易于使用
*   Yul 使用一种简单灵活的语言来创建合同，并且是初学者友好的
*   Yul 是静态类型的，以避免混淆概念，如值和引用。它有一个默认类型，可以随时省略

以下是 Yul 代码的一个示例:

```
object "SmartContract" {
  code {
    // Smart contract constructor
    datacopy(0, dataoffset("Runtime"), datasize("Runtime"))
    return(0, datasize("Runtime"))
  }
  object "Runtime" {
    code {
       // Runtime code
    }
  }
}

```

大多数基于以太坊的项目很可能已经使用了 Yul。

### 使用 Yul 的缺点

*   因为 Yul 需要被编译成字节码，所以它需要额外的时间来完成整个编译过程，因此在开发过程中非常耗时

## 结论

您应该使用上面列表中的哪种编程语言取决于您想要使用的区块链。例如，对于区块链以太坊来说，坚固是大多数开发者的首选。

当然，我们期待更多的传统语言支持和更多的区块链语言出现，因为它仍然是一个新兴的空间。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。