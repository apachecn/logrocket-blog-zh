# 可靠性设计模式的开发者指南

> 原文：<https://blog.logrocket.com/developers-guide-solidity-design-patterns/>

由于区块链和 [DApps](https://blog.logrocket.com/how-build-dapp-host-ipfs-fleek/) (去中心化应用)的持续流行，开源 DApps 看到了来自各种各样开发者的贡献的增长。大多数 DApps 和区块链应用的核心是使用 Solidity 开发的智能合约。

对开源项目的贡献在 [Solidity](https://blog.logrocket.com/daml-vs-solidity-choose-smart-contract-language/) 社区中引起了关注，因为这些项目对人们的金钱有着现实世界的影响，并且当来自不同背景的开发人员在一个项目上合作时，几乎可以肯定应用程序中会有错误和代码冲突。这就是为什么为 DApps 实践适当的标准如此重要。

为了保持优秀的标准，消除风险，减轻冲突，构造可伸缩的和安全的智能契约，有必要在 Solidity 中研究和使用设计模式和风格的正确实现。

本文将讨论可靠性设计模式；你必须熟悉坚固性才能跟上。

### 内容

## 什么是可靠性设计模式？

作为开发者，你可以从网上的各种资源中学习使用 Solidity，但这些材料并不相同，因为 Solidity 中实现事物的方式和风格有很多不同。

设计模式是可重用的传统解决方案，用于解决反复出现的设计缺陷。从一个地址转移到另一个地址是一个经常关注可靠性的实际例子，可以用设计模式来调整。

在固体中转移乙醚时，我们使用`Send`、`Transfer`或`Call`方法。这三种方法有相同的单一目标:将以太送出智能合约。让我们看看如何使用`Transfer`和`Call`方法来实现这个目的。下面的代码示例演示了不同的实现。

首先是`Transfer`法。使用这种方法时，所有接收智能合约都必须定义回退功能，否则传输事务将失败。可用的气体限制为 2300 气体，这足以完成转移交易并有助于防止再入攻击:

```
function Transfer(address payable _to) public payable {     
  _to.transfer(msg.value); 
} 

```

上面的代码片段定义了`Transfer`函数，该函数接受一个接收地址作为`_to`，并使用`_to.transfer`方法启动指定为`msg.value`的以太网传输。

接下来是`Call`方法。使用此方法可以触发合同中的其他函数，并且可以选择设置函数执行时要使用的气费:

```
function Call(address payable _to) public payable {
    (bool sent) = _to.call.gas(1000){value: msg.value}("");
    require("Sent, Ether not sent");
}

```

上面的代码片段定义了`Call`函数，它接受一个接收地址作为`_to`，将事务状态设置为 boolean，返回的结果在数据变量中提供。如果`msg.data`为空，那么`receive`函数会在`Call`方法之后立即执行。回退在没有 receive 函数实现的地方运行。

在智能合约之间转移以太网的最佳方式是使用`Call`方法。

在上面的例子中，我们使用了两种不同的技术来转移乙醚。您可以使用`Call`指定您想要消耗多少汽油，而默认情况下`Transfer`有固定的汽油量。

这些技术是在 Solidity 中实践的模式，用于实现`Transfer`的重复出现。

为了保持上下文的连贯性，以下部分是 Solidity 规范的一些设计模式。

## 行为模式

### 守卫检查

智能合约的主要功能是确保交易的要求通过。如果任何条件失败，契约将恢复到以前的状态。Solidity 通过使用 EVM 的错误处理机制来抛出异常并将契约恢复到异常前的工作状态，从而实现了这一点。

下面的智能契约展示了如何使用所有三种技术实现守卫检查模式:

```
contract Contribution {
  function contribute (address _from) payable public {
    require(msg.value != 0);
    require(_from != address(0));
    unit prevBalance = this.balance;
    unit amount;

    if(_from.balance == 0) {
      amount = msg.value;
    } else if (_from.balance < msg.sender.balance) {
      amount = msg.value / 2;
    } else {
      revert("Insufficent Balance!!!");
    }

    _from.transfer(amount);
    assert(this.balance == prevBalance - amount);
  }
}

```

在上面的代码片段中，Solidity 使用以下代码处理错误异常:

`require()`声明函数执行的条件。它接受单个条件作为参数，如果条件评估为 false，则抛出异常，终止函数的执行，而不消耗任何气体。

`assert()`评估一个函数的条件，然后抛出一个异常，将契约恢复到以前的状态，如果执行后需求失败，则消耗供气。

抛出一个异常，返回任何提供的气体，如果对函数的要求失败，将函数调用恢复到契约的原始状态。`revert()`方法不评估也不需要任何条件。

### 状态机

状态机模式基于系统以前和当前的输入来模拟系统的行为。开发人员使用这种方法将大问题分解成简单的阶段和转换，然后用它们来表示和控制应用程序的执行流程。

状态机模式也可以在智能合约中实现，如下面的代码片段所示:

```
contract Safe {
    Stages public stage = Stages.AcceptingDeposits;
    uint public creationTime = now;
    mapping (address => uint) balances;

    modifier atStage(Stages _stage) {
      require(stage == _stage);
      _;
    }

    modifier timedTransitions() {
      if (stage == Stages.AcceptingDeposits && now >=
      creationTime + 1 days)
      nextStage();
      if (stage == Stages.FreezingDeposits && now >=
      creationTime + 4 days)
      nextStage();
      _;
    }
    function nextStage() internal {
      stage = Stages(uint(stage) + 1);
    }
    function deposit() public payable timedTransitions atStage(Stages.AcceptingDeposits) {
      balances[msg.sender] += msg.value;
    }
    function withdraw() public timedTransitions atStage(Stages.ReleasingDeposits) {
      uint amount = balances[msg.sender];
      balances[msg.sender] = 0;
      msg.sender.transfer(amount);
    }
}

```

在上面的代码片段中，`Safe`契约使用修饰符来更新各个阶段之间的契约状态。这些阶段决定了何时可以存款和取款。如果合同当前状态不是`AcceptingDeposit`，用户不能存入合同，如果当前状态不是`ReleasingDeposit`，用户不能退出合同。

### 神谕

以太坊契约有自己的生态系统，它们在那里交流。系统只能通过事务导入外部数据(通过将数据传递给一个方法)，这是一个缺点，因为许多契约用例涉及来自区块链以外的来源的知识(例如，股票市场)。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这个问题的一个解决方案是使用 oracle 模式连接到外部世界。当 oracle 服务和智能合约异步通信时，oracle 服务充当 API。事务通过调用智能契约函数开始，该函数包括向 oracle 发送请求的指令。

基于这种请求的参数，oracle 将获取一个结果，并通过在主契约中执行回调函数来返回该结果。基于 Oracle 的合同与区块链的分散网络概念不相容，因为它们依赖于单个组织或团体的诚实性。

Oracle services [21](https://docs.oracle.com/en/database/oracle/oracle-database/21/whats-new.html) 和 [22](https://docs.oracle.com/en/cloud/saas/b2b-service/22a/index.html) 通过对所提供的数据进行有效性检查来解决这一缺陷。请注意，oracle 必须为回调调用付费。因此，oracle 费用与回调调用所需的以太网费用一起支付。

下面的代码片段显示了 oracle 契约与其消费者契约之间的事务:

```
contract API {
    address trustedAccount = 0x000...; //Account address
    struct Request {
        bytes data;
        function(bytes memory) external callback;
    }
    Request[] requests;
    event NewRequest(uint);

    modifier onlyowner(address account) {
        require(msg.sender == account);
        _;
    }
    function query(bytes data, function(bytes memory) external callback) public {
        requests.push(Request(data, callback));
        NewRequest(requests.length - 1);
    }
    // invoked by outside world
    function reply(uint requestID, bytes response) public
    onlyowner(trustedAccount) {
    requests[requestID].callback(response);
    }
}

```

在上面的代码片段中，`API`智能契约使用`query`函数向`knownSource`发送查询请求，后者执行`external callback`函数并使用`reply`函数从外部源收集响应数据。

### 随机性

尽管在 Solidity 中生成随机和唯一的值是多么棘手，但它的需求量很大。块时间戳是以太坊中随机性的来源，但它们是危险的，因为矿工可以篡改它们。为了防止这个问题，像块散列 PRNG 和 Oracle RNG 这样的解决方案应运而生。

下面的代码片段显示了使用最新块散列的这种模式的基本实现:

```
// This method is predicatable. Use with care!
function random() internal view returns (uint) {
    return uint(blockhash(block.number - 1));
}

```

上面的`randomNum()`函数通过散列块号(`block.number`，它是区块链上的一个变量)生成一个随机且唯一的整数。

## 安全模式

### 存取限制

因为 Solidity 中没有内置的管理执行权限的手段，所以一个常见的趋势就是限制函数执行。函数的执行应该只在特定的条件下进行，如时间、调用者或交易信息以及其他标准。

下面是一个调节函数的例子:

```
contract RestrictPayment {
    uint public date_time = now;

    modifier only(address account) {
        require(msg.sender == account);
        _;
    }

    function f() payable onlyowner(date_time + 1 minutes){
      //code comes here
    }
}

```

上述限制契约防止任何不同于`msg.sender`的`account`执行`payable`功能。如果不满足`payable`函数的要求，`require`用于在函数执行前抛出异常。

### 检查效果交互

check effects 交互模式降低了恶意契约在外部调用后试图接管控制流的风险。该合同很可能在以太网转移过程中将控制流转移给外部实体。如果外部契约是恶意的，它有可能会中断控制流，并导致发送方返回到不希望的状态。

为了使用这种模式，我们必须意识到我们的函数的哪些部分是易受攻击的，这样一旦我们找到易受攻击的可能来源，我们就可以做出响应。

以下是如何使用此模式的示例:

```
contract CheckedTransactions {
    mapping(address => uint) balances;
    function deposit() public payable {
        balances[msg.sender] = msg.value;
    }

    function withdraw(uint amount) public {
        require(balances[msg.sender] >= amount);
        balances[msg.sender] -= amount;
        msg.sender.transfer(amount);
    }
}

```

在上面的代码片段中，如果条件`balances[msg.sender] >= amount`失败，则使用`require()`方法抛出异常。这意味着，用户不能提取大于`msg.sender`余额的`amount`。

### 安全以太网传输

虽然加密货币转账不是 Solidity 的主要功能，但它们经常发生。正如我们前面讨论的，`Transfer`、`Call`和`Send`是固体中转移以太的三种基本技术。除非意识到它们的不同，否则不可能决定使用哪种方法。

除了本文前面讨论的两种方法(`Transfer`和`Call`)之外，在固体中传输以太还可以使用`Send`方法。

`Send`和`Transfer`的相似之处在于，它消耗的汽油量与默认值(2300)相同。然而，与`Transfer`不同，它返回一个布尔结果，表明`Send`是否成功。大多数 Solidity 项目不再使用`Send`方法。

下面是`Send`方法的一个实现:

```
function send(address payable _to) external payable{
    bool sent = _to.send(123);
    require(sent, "send failed");
}

```

上面的`send`函数，如果从`_to.send(123)`返回的 sent 的`Boolean`值是`false`，则使用`require()`函数抛出异常。

### 拉-推

这种设计模式将以太网转移的风险从合同转移到用户身上。在以太网传输过程中，一些事情可能会出错，导致事务失败。在拉推模式中，涉及到三方:发起传输的实体(契约的作者)、智能契约和接收者。

该模式包括映射，这有助于跟踪用户的未清余额。用户调用一个函数来收回分配给他们的以太网，而不是将以太网从合同交付给接收者。其中一笔转账的任何错误都不会影响其他交易。

以下是拉-拉的一个例子:

```
contract ProfitsWithdrawal {
    mapping(address => uint) profits;
    function allowPull(address owner, uint amount) private {
        profits[owner] += amount;
    }
    function withdrawProfits() public {
        uint amount = profits[msg.sender];
        require(amount != 0);
        require(address(this).balance >= amount);
        profits[msg.sender] = 0;
        msg.sender.transfer(amount);
    }
}

```

在上面的`ProfitsWithdrawal`合同中，如果用户的余额大于或等于分配给用户的利润，允许用户提取映射到其`address`的利润。

### 紧急停止

经过审计的智能合同可能包含漏洞，这些漏洞只有在卷入网络事件时才会被发现。合同发布后发现的错误将很难修复。在这种设计的帮助下，我们可以通过阻止对关键函数的调用来停止契约，防止攻击者，直到智能契约得到纠正。

应该只允许授权用户使用停止功能，以防止用户滥用它。从`false`到`true`设置状态变量，以确定合同的终止。终止合同后，您可以使用访问限制模式来确保没有任何关键功能的执行。

如果状态变量指示启动紧急制动，则函数修改会抛出异常，可用于实现这一点，如下所示:

```
contract EmergencyStop {
    bool Running = true;
    address trustedAccount = 0x000...; //Account address
    modifier stillRunning {
        require(Running);
        _;
    }
    modifier NotRunning {
        require(¡Running!);
        _;
    }
    modifier onlyAuthorized(address account) {
        require(msg.sender == account);
        _;
    }
    function stopContract() public onlyAuthorized(trustedAccount) {
        Running = false;
    }
    function resumeContract() public onlyAuthorized(trustedAccount) {
        Running = true;
    }
}

```

上面的`EmergencyStop`契约使用修饰符来检查条件，如果满足这些条件中的任何一个，就会抛出异常。契约使用`stopContract()`和`resumeContract()`函数来处理紧急情况。

通过将状态变量重置为`false`可以恢复合同。这种方法应该像紧急停止功能一样防止未经授权的调用。

## 可升级性模式

### 代理人

这种模式允许在不破坏任何组件的情况下升级智能合约。使用这种方法时，会使用一个名为`Delegatecall`的特殊消息。它将函数调用转发给委托，而不公开函数签名。

代理契约的回退函数使用它来启动每个函数调用的转发机制。`Delegatecall`唯一返回的是一个布尔值，它指示执行是否成功。我们对函数调用的返回值更感兴趣。请记住，升级合同时，存储顺序不得改变；只允许添加。

下面是实现这种模式的一个例子:

```
contract UpgradeProxy {
    address delegate;
    address owner = msg.sender;
    function upgradeDelegate(address newDelegateAddress) public {
        require(msg.sender == owner);
        delegate = newDelegateAddress;
    }
    function() external payable {
        assembly {
            let _target := sload(0)
            calldatacopy(0x01, 0x01, calldatasize)
            let result := delegatecall(gas, _target, 0x01, calldatasize, 0x01, 0)
            returndatacopy(0x01, 0x01, returndatasize)
            switch result case 0 {revert(0, 0)} default {return (0, returndatasize)}
        }
    }
}

```

在上面的代码片段中，`UpgradeProxy`处理一种机制，一旦`owner`通过调用将`delegate`契约数据的副本传输到新版本的回退函数来执行契约，该机制就允许升级`delegate`契约。

### 存储器阵列构建

这种方法可以快速高效地从合同存储中聚合和检索数据。在 EVM 中，与合同的记忆进行交互是最昂贵的操作之一。确保消除冗余并仅存储所需的数据有助于最大限度地降低成本。

我们可以使用视图功能修改从合同存储中聚合和读取数据，而不会产生更多费用。每次需要搜索时，都在内存中重新创建数组，而不是将数组存储在存储器中。

易于迭代的数据结构，如数组，用于使数据检索更容易。当处理具有多个属性的数据时，我们使用自定义数据类型(如 struct)对其进行聚合。

还需要映射来跟踪每个聚合实例的预期数据输入数量。

下面的代码说明了这种模式:

```
contract Store {
    struct Item {
        string name;
        uint32 price;
        address owner;
    }
    Item[] public items;
    mapping(address => uint) public itemsOwned;
    function getItems(address _owner) public view returns (uint[] memory) {
        uint\[] memory result = new uint[\](itemsOwned[_owner]);
        uint counter = 0;
        for (uint i = 0; i < items.length; i++) {
            if (items[i].owner == _owner) {
                result[counter] = i;
                counter++;
            }
        }
        return result;
    }
}

```

在上面的`Store`契约中，我们使用`struct`来设计列表中项目的数据结构，然后我们将项目映射到其所有者的`address`。为了获得一个地址所拥有的条目，我们使用`getItems`函数聚集一个名为`result`的内存。

### 永久存储

此模式维护升级的智能协定的内存。因为旧合同和新合同是在区块链上单独部署的，所以累积存储仍保留在其旧位置，用户信息、帐户余额以及对其他有价值信息的引用都存储在该位置。

永久存储应该尽可能独立，通过实现多个数据存储映射(每个数据类型一个映射)来防止对数据存储的修改。将抽象的值转换成 sha3 哈希的映射充当键值存储。

因为所提出的解决方案比传统的值存储更加复杂，所以包装器可以降低复杂性并使代码清晰易读。在使用永久存储的可升级契约中，包装器使得处理不熟悉的语法和带有散列的键变得更加容易。

下面的代码片段展示了如何使用包装器来实现永久存储:

```
function getBalance(address account) public view returns(uint) {
    return eternalStorageAdr.getUint(keccak256("balances", account));
}
function setBalance(address account, uint amount) internal {
    eternalStorageAdr.setUint(keccak256("balances", account), amount);
}
function addBalance(address account, uint amount) internal {
    setBalance(account, getBalance(account) + amount);
}

```

在上面的代码片段中，我们使用`enternalStorageAdr.getUint()`中的`keccak256`哈希函数从永久存储中获得了一个`account`的余额，同样用于设置帐户的余额。

## 内存与存储

`Storage`、`memory`或`calldata`是以变量形式声明动态数据类型的位置时使用的方法，但我们现在将集中讨论`memory`和`storage`。术语`storage`指的是智能合约的所有实例共享的状态变量，而`memory`指的是每个智能合约执行实例中数据的临时存储位置。让我们看看下面的代码示例，看看这是如何工作的:

使用`storage`的示例:

```
contract BudgetPlan {
        struct Expense {
                uint price;
                string item;
        } 
        mapping(address => Expense) public Expenses;
        function purchase() external {
                Expense storage cart = Expenses[msg.sender]
                cart.string = "Strawberry" 
                cart.price = 12
        }
}

```

在上面的`BudgetPlan`合同中，我们为账户的费用设计了一个数据结构，其中每个费用(`Expense`)是一个包含`price`和`item`的结构。然后我们声明了`purchase`函数来给`storage`添加一个新的`Expense`。

使用`memory`的示例:

```
contract BudgetPlan {
        struct Expense {
                uint price;
                string item;
        } 
        mapping(address => Expense) public Expenses;
        function purchase() external {
                Expense memory cart = Expenses[msg.sender]
                cart.string = "Strawberry" 
                cart.price = 12
        }
}

```

几乎和使用`storage`的例子一样，一切都是一样的，但是在代码片段中，当执行`purchase`函数时，我们在内存中添加了一个新的`Expense`。

## 结束语

开发人员应该坚持设计模式，因为有不同的方法来实现特定的目标或实现某些概念。

如果你实践这些可靠性设计模式，你会发现你的应用程序有了实质性的变化。您的应用程序将更容易开发，更干净，更安全。

我建议你在下一个 Solidity 项目中至少使用其中一种模式来测试你对这个主题的理解。

欢迎提出任何与这个话题相关的问题，或者在下面的评论区留下你的评论。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。