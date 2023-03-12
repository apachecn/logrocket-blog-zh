# 添加暂停功能，以确保 Solidity 智能合同

> 原文：<https://blog.logrocket.com/pause-functionality-secure-solidity-smart-contracts/>

当我第一次开始在 Solidity 中开发[时，我想了很多关于我的合同代码的安全性，以及我将如何处理黑客攻击或窃取资金的企图。我主要是在区块链上开发游戏，处理来自真实玩家的真实资金，因此保护通过我的 DApp 收集并存储在合同中的资金非常重要。](https://blog.logrocket.com/writing-smart-contracts-solidity/)

如果黑客发现了利用我的代码的方法，我该怎么办？如果发现了漏洞，我该如何保护资金？

在这篇文章中，我将分享一些在 Solidity 中实现暂停功能的见解——这是一个重要的工具，可以增加您保持合同安全的武器库。我将使用一个 Solidity 彩票智能合约项目来说明其中一种方法。

*向前跳转:*

## 为什么要考虑智能合约安全性？

上传到区块链的合同代码是不可变的，所以在黑客攻击的情况下使其离线是不可能的。当您的合同功能受到潜在攻击时，试图诊断问题可能会导致时间损失甚至更大的损失。

彻底的单元测试和代码审查是必不可少的，但是在您的合同上计划和实现安全控制是任何区块链项目的重要预防措施。漏洞利用的可能性是非常真实的，尤其是如果您的 DApp 变得非常受欢迎，越来越多的人开始查看您的合同代码。

我最近在 Solidity 写了一个彩票游戏项目，并在 Polygon 上推出。安全性对我来说至关重要，因为我计划在游戏进行期间保留资金，然后在游戏结束后将奖金分发给玩家。

我采用了多种解决方案来保护我的合同功能和与之相关的资金，包括暂停功能的能力。在这篇文章中，我将讨论添加暂停功能的利与弊。我还将回顾在 Solidity smart 契约中实现暂停功能的三种方法。如果您正在寻找如何编写彩票智能合同的信息，我希望这篇关于暂停功能的文章会对您有所帮助。

## 什么时候暂停智能合同是有益的？

首先，让我们考虑一下暂停函数如何成为一个有用的契约修饰符。

当您将代码推送到区块链时，您需要决定授予每个函数什么级别的可见性。暂停功能是保护所有用户都可以使用的功能的一种方式(例如，`public`或`external`功能)。暂停修饰符或条件可以暂时移除一个功能的工作能力，而不会影响其他契约功能，也不会选择破坏整个契约并从头开始。

想象一下，你的 DApp 提供了一项服务，通过你的智能合约来铸造一个 NFT，或者出售一个数字产品。暂时保留这些销售或薄荷糖的能力对你来说可能很重要。

在我的彩票智能合约中，我想要保护的关键公共函数包括`buyTicket`，一个接收资金并为玩家铸造彩票的函数，以及`finalizeWinner`，一个关闭彩票、选择随机中奖者并处理支付中奖者和佣金的事务的函数。我选择在我的彩票智能合约中添加暂停功能，以便在我需要暂时离线时保护这些功能。

暂停功能在通过初始硬币发行(ico)创建新代币时也很有用。当您向用户提供新令牌时，您可能希望在初始销售仍在进行时暂停令牌持有者在交易所进行交易的能力。投机性买家购买你的代币，意图立即出售获利，这可能会损害你代币的价值。对传输实施临时暂停可以防止这种行为。

## 暂停智能合约有什么缺点吗？

暂停智能合同的功能非常强大，但它会对消费者对您的 DApp 的信任产生负面影响。

例如，通过 ICO 购买令牌的消费者可能对未来传输的暂时暂停感到满意，但不希望这种暂停永远持续下去，或者发现可以在没有事先通知的情况下重新实施。

一般来说，如果消费者知道暂停功能是可能的，并且可以在任何时候应用，这可能会导致对 DApp 完整性的质疑。实现在特定时间点或特定条件下永久消除暂停条件的能力，对于您的业务可能非常重要。用 Solidity 编写的智能合同也可以实现这一功能。

## 如何使用 Solidity 中的暂停功能

让我们来看看向 Solidity 智能契约添加暂停功能的三种方法:全局布尔变量方法、Pausable.sol 方法和带有暂停控制的全局布尔变量方法。

### 全局布尔变量方法

向可靠性契约添加暂停功能的最直接的方法是简单地声明一个全局布尔变量`paused`，在函数中添加带有`require`条件的变量，并创建一个`setPaused`函数，使您能够更改该值。

这里有一个例子:

```
// A global boolean variable is created to manage pause capabilities called paused
   bool public paused;

// This variable is then implemented in the function with a "require" statement that its value resolve to false, otherwise a message is displayed and the function will not execute
   function transfer1(address to, uint256 amount) external {
       require(paused == false, "Function Paused");
       require(balances[msg.sender] >= amount, "Not enough tokens");

       balances[msg.sender] -= amount;
       balances[to] += amount;
   }

// Another function must be added to change the value of the bool variable 'paused'. This function must be restricted in use to prevent any user from accessing it. In this case, we use a require statement to restrict use to the contract owner 
   function setPaused(bool _paused) public {
       require(msg.sender == owner, "You are not the owner");
       paused = _paused;
   }

```

我决定将全局布尔方法合并到我的可靠性彩票合同中。我是这样实现的:

```
// This function is used to buy a ticket to a game, and calls another function to mint the NFT   
   function buyTicket(address from, uint lottoID) public payable {
      require (lottoArray[lottoID - 1].isLive, "Lotto is not live");
require(paused == false, "The Buy Ticket function has been paused");    

lottoPlayers.push(Player(from, lottoID));

mintTicket(from, lottoID);

      if (address(this).balance > lottoArray[lottoID - 1].targetValue) {
           endLotto(false, lottoID);
           emit LottoClosed(lottoID);
       }
   }

```

### Pausable.sol 方法

另一种添加暂停功能的方法是流行的 Pausable.sol，它是许多 DApps 用来标准化暂停动作的 [OpenZeppelin 开源智能契约库](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/Pausable.sol)的一个模块。

首先，您将使用一个`import`语句从 Pausable.sol 继承暂停修饰符，然后可以在您编写的任何函数中使用它:

```
import "@openzeppelin/contracts-ethereum-package/contracts/utils/Pausable.sol";

```

接下来，如果您想只在`pause`为`false`时提供函数，您将在函数中包含`whenNotPaused`作为修饰符。

```
// This transfer function has whenNotPause set as a modifier in its function declaration. It can only be run if whenNotPaused is true. 
   function transfer2(address to, uint256 amount) external whenNotPaused {      
       require(balances[msg.sender] >= amount, "Not enough tokens");
       balances[msg.sender] -= amount;
       balances[to] += amount;
   }

```

下面是`whenNotPaused`在 Pausable.sol 契约中的定义:

```
/*** @dev Modifier to make a function callable only when the contract is not paused. */
   modifier whenNotPaused() {
       require(!_paused, "Pausable: paused");
       _;
   }

```

### 具有暂停控制方法的全局布尔变量

如果添加一个选项来移除暂停功能对于项目的完整性很重要，则可以使用带有暂停控制方法的全局布尔变量来添加此功能。

首先，您将创建一个布尔变量`canPause`，将该变量设置为您的`setPause`函数中的一个条件，然后在一个新函数中将该变量的设置硬编码为`false`，该函数只能用于移除暂停功能。`canPause` 函数一旦被调用，就无法逆转。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

下面的示例显示了 Solidity 中带有暂停控制方法的全局布尔变量:

```
// A global variable to govern pause capabilities called canPause
   bool public canPause = true;

// The setPaused function here is updated with a new require statement for canPause that ensures canPause is true before it executes
   function setPaused(bool _paused) public {
       require(msg.sender == owner, "You are not the owner");
       require(canPause == true);
       paused = _paused;
   }

// This function removes pause capability permanently by the contract owner. Ensure that this function is restricted in usage.
function removePauseCapability() public {
       require(msg.sender == owner, "You are not the owner");
       paused = false;
       canPause = false;
       emit removePause();
   }

```

## 结论

在本文中，我们回顾了将暂停功能整合到 Solidity smart 契约中的三种不同方法。暂停只是你的 Web3 开发者武库中的一个工具。

可以使用其他策略来增强智能合同的安全性，包括使用修饰符来限制合同所有者或预先批准的列表对功能的访问，调整功能的可见性设置以消除外部查看者的访问(例如，在功能上使用内部或私有修饰符)，甚至添加提取所有资金和完全销毁合同的能力。最后一个选项将永久停止所有合同功能。

尽管暂停不像其中一些方法那样极端，但它仍然是 Solidity 中管理合同安全性的重要工具。所有开发人员都应该考虑使用 pause，尤其是对于面向公众的函数。

在您的项目投入使用之前，我建议您考虑一下可能会实现暂停等条件的场景。你的合同存在哪些潜在的漏洞？恶意行为者会如何攻击你的应用？一份安全的智能合同将在项目启动时为您省去许多麻烦。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。