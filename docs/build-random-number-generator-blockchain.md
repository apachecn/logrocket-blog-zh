# 建立一个区块链随机数生成器

> 原文：<https://blog.logrocket.com/build-random-number-generator-blockchain/>

区块链的结构依赖于决定论。在区块链生态系统中，每个网络的状态都是公开的；完全透明。如果你知道状态和输入，你就可以计算输出。决定论与共识有关，这是区块链的进步能够被证实的原因。没有这种决定论，对区块链进步的独立验证将是不可能的，因为区块链将不再是分散的。

对于大多数用例来说，随机数只有在实际使用时才能知道。这意味着区块链的基础——透明和共识——使得随机数的产生变得非常困难。

在本文中，我们将介绍如何克服为区块链生成随机数的限制。我们将通过如何建立和测试一个利用随机数的赌场赌博游戏的可靠性合同。我们还将讨论一些防止区块链赌博游戏中滥用的策略。

***注意，***[合并后的](https://ethereum.org/en/upgrades/merge/)*EVM 本身可能有随机性的来源；然而，即使实现了 EIP 4499，[随机性仍然远非完美](https://eips.ethereum.org/EIPS/eip-4399#security-considerations)*

***提前跳跃:***

## 随机性的用例

对于某些目的，例如统计采样，使用[伪随机数](https://en.wikipedia.org/wiki/Pseudorandomness)就足够了——伪随机数看起来是随机的，但实际上是由确定性过程生成的。然而，在一些用例中，可以预测的看似随机的数字根本不够好。

让我们看一些例子。

### WindowsNT 文件系统（NTFileSystem 的缩写）

许多 NFT 项目，比如[光学朋克](https://www.optipunks.com)、[乐观兔子](https://www.optimisticbunnies.com)和[乐观强盗](https://optimistic.loogies.io)，在制造时会随机分配属性给他们的 NFT。由于一些属性比其他属性更有价值，铸币者必须知道铸币的结果，直到铸币之后。

### 比赛

许多游戏依赖于随机性，要么是为了做决定，要么是为了产生对玩家隐藏的信息。如果没有随机性，区块链游戏将局限于所有信息为所有玩家所知的游戏，如国际象棋或跳棋。

## 提交/揭示协议

那么，我们如何在完全透明的区块链上生成随机数呢？记住，“区块链上没有秘密”。

答案就在最后三个字里，“在区块链上”。为了生成随机数，我们将使用一个秘密数字，交互的一方有，另一方没有。但是，我们会确保秘密号码不在区块链上。

提交/揭示协议允许两个或更多的人使用[加密散列函数](https://en.wikipedia.org/wiki/Cryptographic_hash_function)来达到相互同意的随机值。让我们来看看它是如何工作的:

1.  A 方生成一个随机数，`randomA`
2.  A 端发送一个带有该号码散列的消息`hash(randomA)`。这个
    将值`randomA`提交给 A 方，因为虽然没有人能猜出`randomA`的值，但是一旦 A 方提供了它，每个人都可以检查它的值是否正确
3.  B 侧发送具有另一个随机数的消息，`randomB`
4.  A 方在第三条消息中显示了`randomA`的值
5.  双方都接受随机数是两个值的`randomA ^ randomB`、[、【异或】、](https://en.wikipedia.org/wiki/Exclusive_or)

这里 XOR 的优势在于，它是由双方平等确定的，因此任何一方都不能选择一个有利的“随机”值。

## 赌场赌博游戏教程

为了了解如何在实际的区块链游戏中使用随机数生成器，我们将回顾赌场赌博游戏 Casino.sol 的代码。Casino.sol 是用 Solidity 编写的，使用了 commit/reveal 方案；可以在 [GitHub](https://github.com/qbzzt/qbzzt.github.io/tree/master/LogRocket/20220615-random) 上访问。

### 建立合同

让我们浏览一下 Casino.sol 赌博游戏的代码；就在这个 [GitHub](https://github.com/qbzzt/qbzzt.github.io/blob/master/LogRocket/20220615-random/contracts/Casino.sol) 文件里。

首先，我们指定许可和可靠性版本:

```
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;

```

接下来，我们定义一个名为`Casino`的契约。[可靠性契约](https://blog.logrocket.com/writing-smart-contracts-solidity/)有点类似于其他编程语言中的对象。

```
contract Casino {

```

现在，我们创建一个[结构](https://docs.soliditylang.org/en/v0.8.14/structure-of-a-contract.html#struct-types)，`ProposedBet`，在这里我们将存储有关建议赌注的信息:

```
 struct ProposedBet {
   address sideA;
   uint value;
   uint placedAt;
   bool accepted;  
 }    // struct ProposedBet

```

这个结构不包括承诺，即`hash(randomA)`值，因为该值被用作定位`ProposedBet`的键。但是，它包含以下字段:

| 田 | 类型 | 目的 |
| --- | --- | --- |
| `sideA` | 地址 | 提议下注的[地址](https://docs.soliditylang.org/en/v0.8.14/types.html#address) |
| `value` | 整数 | 赌注的大小在魏，最小的乙醚面额 |
| `placedAt` | 整数 | 提案的[时间戳](https://en.wikipedia.org/wiki/Unix_time) |
| `accepted` | Boolean | 建议是否已被接受 |

***注意，*** 本例中没有使用`placedAt` *字段，但是我将在本文后面解释为什么跟踪这些信息很重要*

接下来，我们创建一个`AcceptedBet`结构来存储赌注被接受后的额外信息。

这里一个有趣的区别是,`sideB`直接为我们提供了`randomB`,而不是散列。

```
 struct AcceptedBet {
   address sideB;
   uint acceptedAt;
   uint randomB;
 }   // struct AcceptedBet

```

下面是存储建议和接受的赌注的[映射](https://docs.soliditylang.org/en/v0.8.14/types.html#mapping-types):

```
 // Proposed bets, keyed by the commitment value
 mapping(uint => ProposedBet) public proposedBet;
 // Accepted bets, also keyed by commitment value
 mapping(uint => AcceptedBet) public acceptedBet;

```

接下来，我们设置一个事件，`BetProposed`。[事件](https://docs.soliditylang.org/en/v0.8.14/abi-spec.html#events)是 Solidity smart contracts 中用于向外界发送消息的标准机制。这个事件告诉世界，一个用户(在这个例子中是`sideA`)正在提议一个赌注，赌注是多少。

```
 event BetProposed (
   uint indexed _commitment,
   uint value
 );

```

现在，我们设置另一个事件，`BetAccepted`。这一事件告诉全世界(特别是提议打赌的`sideA`)，是时候揭晓`randomA`了。没有办法只从区块链向特定用户发送消息。

```
 event BetAccepted (
   uint indexed _commitment,
   address indexed _sideA
 );

```

接下来，我们创建一个事件`BetSettled`。该事件在赌注结算时发出。

```
event BetSettled (
   uint indexed _commitment,
   address winner,
   address loser,
   uint value   
 );
```

现在，我们创建一个`proposeBet`函数。承诺是该函数的唯一参数。

其他一切(赌注的价值和`sideA`的身份)都是交易的一部分。

注意这个函数是`payable`。这意味着它可以接受乙醚付款。

```
 // Called by sideA to start the process
 function proposeBet(uint _commitment) external payable {

```

大多数外部调用的函数，比如这里显示的`proposedBet`，都是从一堆`[require](https://docs.soliditylang.org/en/latest/control-structures.html#error-handling-assert-require-revert-and-exceptions)`语句开始的。

```
require(proposedBet[_commitment].value == 0,
     "there is already a bet on that commitment");
   require(msg.value > 0,
     "you need to actually bet something");
```

当我们编写一个智能契约时，我们必须假设有人试图恶意调用这个函数。这种假设将促使我们采取保护措施。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在上面的代码中，我们有两个条件:

1.  如果已经有一个关于承诺的赌注，拒绝这个。否则，人们可能会试图用它来覆盖现有的赌注，这将导致投入的金额永远卡在合同中
2.  如果赌注是 0 魏，拒绝

如果这两个条件都不满足，我们将信息写入`proposedBet`。

由于以太坊存储的工作方式，我们不需要创建一个新的结构，填充它，然后将它分配给映射。相反，每个承诺值都有一个结构，用零填充——我们只需要修改它。

```
   proposedBet[_commitment].sideA = msg.sender;
   proposedBet[_commitment].value = msg.value;
   proposedBet[_commitment].placedAt = block.timestamp;
   // accepted is false by default

```

现在，我们向全世界宣布提议的赌注和金额:

```
   emit BetProposed(_commitment, msg.value);
 }  // function proposeBet

```

我们需要两个参数来知道用户正在接受什么:承诺和用户的随机值。

```
 // Called by sideB to continue
 function acceptBet(uint _commitment, uint _random) external payable {

```

在下面的代码中，我们在接受赌注之前检查了三个潜在的问题:

1.  如果赌注已经被某人接受，就不能再被接受
2.  如果`sideA`的地址是零，这意味着实际上没有人下注
3.  `sideB`需要下注与`sideA`相同的金额

```
   require(!proposedBet[_commitment].accepted,
     "Bet has already been accepted");
   require(proposedBet[_commitment].sideA != address(0),
     "Nobody made that bet");
   require(msg.value == proposedBet[_commitment].value,
     "Need to bet the same amount as sideA");

```

如果所有的要求都满足了，我们创建新的`AcceptedBet`，在`proposedBet`中标记它已经被接受，然后发出一个`BetAccepted`消息。

```
   acceptedBet[_commitment].sideB = msg.sender;
   acceptedBet[_commitment].acceptedAt = block.timestamp;
   acceptedBet[_commitment].randomB = _random;
   proposedBet[_commitment].accepted = true;
   emit BetAccepted(_commitment, proposedBet[_commitment].sideA);
 }   // function acceptBet

```

下一个函数是伟大的`reveal`！

`sideA`显示`randomA`，我们可以看到谁赢了:

```
 // Called by sideA to reveal their random value and conclude the bet
 function reveal(uint _random) external {

```

我们不需要承诺本身作为参数，因为我们可以从`randomA`中导出它。

```
   uint _commitment = uint256(keccak256(abi.encodePacked(_random)));

```

为了降低意外将 ETH 发送到它将被阻塞的地址的风险，Solidity 只允许我们将它发送到类型为`[address payable](https://docs.soliditylang.org/en/latest/types.html#address)`的地址。

```
   address payable _sideA = payable(msg.sender);
   address payable _sideB = payable(acceptedBet[_commitment].sideB);

```

约定的随机值是两个随机值的异或，如下所述:

```
   uint _agreedRandom = _random ^ acceptedBet[_commitment].randomB;

```

我们将在合同中的多个地方使用赌注的值，因此为了简洁和可读性，我们将创建另一个变量`_value`来保存它。

```
   uint _value = proposedBet[_commitment].value;

```

在两种情况下,`proposedBet[_commitment].sideA == msg.sender`不等于承诺。

1.  用户没有下注
2.  作为`_random`提供的值不正确。在这种情况下，`_commitment`将是一个不同的值，因此该位置的建议下注将没有正确的值给`sideA`。

```
   require(proposedBet[_commitment].sideA == msg.sender,
     "Not a bet you placed or wrong value");

```

```
   require(proposedBet[_commitment].accepted,
     "Bet has not been accepted yet");

```

上述`proposedBet[_commitment].accepted`功能只有在赌注被接受后才有意义。

接下来，我们使用该值的最低有效位来决定获胜者:

```
   // Pay and emit an event
   if (_agreedRandom % 2 == 0) {

```

在这里，我们把赌注给了赢家，并发出一条消息告诉全世界赌注已经定了。

```
     // sideA wins
     _sideA.transfer(2*_value);
     emit BetSettled(_commitment, _sideA, _sideB, _value);
   } else {
     // sideB wins
     _sideB.transfer(2*_value);
     emit BetSettled(_commitment, _sideB, _sideA, _value);     
   }

```

现在，我们将删除不再需要的 bet 存储。

任何人都可以回顾区块链，看看当时的承诺和赌注的揭示价值。删除此数据的目的是收集气体退款，用于清理不再需要的存储。

```
   // Cleanup
   delete proposedBet[_commitment];
   delete acceptedBet[_commitment];

```

最后，我们有了函数和契约的结尾:

```
 }  // function reveal
}   // contract Casino

```

### 带有汇总的事务处理

在撰写本文时，以太坊上最经济的交易方式是[使用汇总](https://ethereum.org/en/layer-2/)。

基本上，汇总是一个将所有事务写入以太坊的区块链，但是在其他更便宜的地方运行处理。记住，任何人都可以验证区块链状态，因为以太坊是不受审查的。

然后，状态根被发送到第 1 层，并且有保证(或者是[数学](https://ethereum.org/en/developers/docs/scaling/zk-rollups/)或者是[经济](https://ethereum.org/en/developers/docs/scaling/optimistic-rollups/))它是正确的值。通过使用状态根，可以证明状态的任何部分——例如，证明某物的所有权。

这种机制意味着处理(可以在 rollup 或第 2 层上完成)非常便宜，而交易数据(必须存储在以太坊或第 1 层上)相比之下非常昂贵。举个例子，在我使用的 [rollup](https://www.optimism.io) 中，1 层气体是 2 层气体成本的 20000 倍。你可以[点击这里查看当前第一层和第二层天然气价格的比率](https://public-grafana.optimism.io/d/9hkhMxn7z/public-dashboard?orgId=1&refresh=5m)。

为此，`reveal`只取`randomA`。

我可以编写 Casino.sol 游戏来获得承诺的值，然后它可以区分不正确的值和不存在的赌注。但是，在汇总时，这将显著增加事务的成本。

### 测试合同

[casino-test.js](https://github.com/qbzzt/qbzzt.github.io/blob/master/LogRocket/20220615-random/test/casino-test.js) 是测试 Casino.sol 契约的 JavaScript 代码。这是重复的，所以我只解释有趣的部分。

ethers 包`[ethers.utils.keccak256](https://docs.ethers.io/v5/api/utils/hashing/#utils-keccak256)`上的散列函数接受一个包含十六进制数的字符串。如果该数字更小，则不会转换为 256 位，因此例如`0x01`、`0x0001`和`0x000001`都散列为不同的值。为了创建一个与 Solidity 上生成的散列完全相同的散列，我们需要一个 64 个字符的数字，即使它是`0x00..00`。这里使用散列函数是确保我们生成的值是 32 字节的简单方法。

```
const valA = ethers.utils.keccak256(0xBAD060A7)

```

我们想要检查两种可能的结果:一个`sideA`获胜和一个`sideB`获胜。

如果`sideB`发送的值与`sideA`散列的值相同，则结果为零(任何数字 xor 本身为零)，因此`sideB`输了。

```
const hashA = ethers.utils.keccak256(valA)
const valBwin = ethers.utils.keccak256(0x600D60A7)
const valBlose = ethers.utils.keccak256(0xBAD060A7)

```

当使用安全帽 EVM 进行本地测试时，恢复原因作为堆栈跟踪中的一个`[Buffer](https://nodejs.org/api/buffer.html)`对象提供。当连接到实际的区块链时，我们在`reason`字段中得到它。

这个函数让我们在剩余的代码中忽略这个差异。

```
// Chai's expect(<operation>).to.be.revertedWith behaves
// strangely, so I'm implementing that functionality myself
// with try/catch
const interpretErr = err => {
 if (err.reason)
   return err.reason
 else
   return err.stackTrace[0].message.value.toString('ascii')
}

```

下面是使用[柴测试库](https://www.chaijs.com/)的标准方法。我们用一段代码和一些语句来表示应该发生的动作。

```
describe("Casino", async () => {
 it("Not allow you to propose a zero Wei bet", async () => {

```

下面是[创建契约](https://docs.ethers.io/v5/api/contract/contract-factory/#ContractFactory--creating)的新实例的标准 Ethers 机制:

```
   f = await ethers.getContractFactory("Casino")
   c = await f.deploy()

```

默认情况下，交易的`value`(附加金额)为零。

```
   try {
     tx = await c.proposeBet(hashA)

```

函数调用`tx.wait()`返回一个`Promise`对象。表达式`await <Promise>`暂停直到承诺被解决，然后要么继续(如果承诺被成功解决)要么抛出一个错误(如果承诺以错误结束)。

```
     rcpt = await tx.wait()

```

如果没有错误，则意味着接受了零魏赌注。这意味着代码没有通过测试。

```
     // If we get here, it's a fail
     expect("this").to.equal("fail")

```

在这里，我们捕获错误，并验证该错误是否与我们预期的 Casino.sol 合同中的错误相匹配。

如果我们使用安全帽 EVM 运行，我们得到的缓冲区包括一些其他字符，所以最简单的方法就是`match`确保我们看到的是错误字符串，而不是检查是否相等。

```
   } catch(err) {
     expect(interpretErr(err)).to
       .match(/you need to actually bet something/)       
   }
 })   // it "Not allow you to bet zero Wei"

```

其他错误情况，比如这个，非常相似:

```
 it("Not allow you to accept a bet that doesn't exist", async () => {
   .
   .
   .
 })   // it "Not allow you to accept a bet that doesn't exist"

```

为了改变合同交互的默认行为(例如，将付款附加到交易中)，[我们添加了一个覆盖散列作为额外的参数](https://docs.ethers.io/v5/api/contract/contract/#Contract-functionsCall)。在这种情况下，我们发送 10Wei 来测试这种下注是否被接受:

```
 it("Allow you to propose and accept bets", async () => {
   f = await ethers.getContractFactory("Casino")
   c = await f.deploy()
   tx = await c.proposeBet(hashA, {value: 10})

```

如果交易成功，当`tx.wait()`的承诺被解决时，我们得到收据。

此外，该回执包含所有发出的事件。在这种情况下，我们期望有一个事件:`BetProposed`。

当然，在产品级代码中，我们还会检查发出的参数是否正确。

```
   rcpt = await tx.wait()
   expect(rcpt.events[0].event).to.equal("BetProposed")
   tx = await c.acceptBet(hashA, valBwin, {value: 10})
   rcpt = await tx.wait()
   expect(rcpt.events[0].event).to.equal("BetAccepted")     
 })   // it "Allow you to accept a bet"   

```

有时我们需要一些成功的操作来达到我们想要测试的失败，例如尝试接受一个已经被接受的赌注:

```
 it("Not allow you to accept an already accepted bet", async () => {
   f = await ethers.getContractFactory("Casino")
   c = await f.deploy()
   tx = await c.proposeBet(hashA, {value: 10})
   rcpt = await tx.wait()
   expect(rcpt.events[0].event).to.equal("BetProposed")
   tx = await c.acceptBet(hashA, valBwin, {value: 10})
   rcpt = await tx.wait()
   expect(rcpt.events[0].event).to.equal("BetAccepted")

```

在本例中，如果赌注已经被接受，交易将恢复，但它仍将停留在区块链上。这意味着如果`sideA`过早透露，其他任何人都可以接受带有赢值的赌注。

```
   try {
     tx = await c.acceptBet(hashA, valBwin, {value: 10})
     rcpt = await tx.wait()  
     expect("this").to.equal("fail")     
   } catch (err) {
       expect(interpretErr(err)).to
         .match(/Bet has already been accepted/)
   }         
 })   // it "Not allow you to accept an already accepted bet"

```

```
 it("Not allow you to accept with the wrong amount", async () => {
     .
     .
     .
 })   // it "Not allow you to accept with the wrong amount"  
 it("Not allow you to reveal with wrong value", async () => {
     .
     .
     .
 })   // it "Not allow you to accept an already accepted bet"
 it("Not allow you to reveal before bet is accepted", async () => {
     .
     .
     .
 })   // it "Not allow you to reveal before bet is accepted" 

```

到目前为止，我们对所有事情都使用一个地址。然而，要检查两个用户之间的赌注，我们需要有两个用户地址。

我们将使用 Hardhat 的`[ethers.getSigners()](https://hardhat.org/guides/waffle-testing.html#setting-up)`来获取签名者的数组；所有的地址都来自同一个助记符。然后，我们将使用`[Contract.connect](https://docs.ethers.io/v5/api/contract/contract/#Contract-connect)`方法来获得一个契约对象，该对象通过其中一个签名者。

```
 it("Work all the way through (B wins)", async () => {
   signer = await ethers.getSigners()
   f = await ethers.getContractFactory("Casino")
   cA = await f.deploy()
   cB = cA.connect(signer[1])

```

在这个系统中，以太既被用作赌博的资产，又被用作支付交易的货币。因此，`sideA`余额的变化部分是为`reveal`交易付款的结果。

为了了解赌注是如何改变平衡的，我们看一下`sideB`。

我们检查一下`preBalanceB`:

```
   .
   .
   .
   // A sends the transaction, so the change due to the
   // bet will only be clearly visible in B
   preBalanceB = await ethers.provider.getBalance(signer[1].address)   

```

并将其与`postBalanceB`进行比较:

```
   tx = await cA.reveal(valA)
   rcpt = await tx.wait()
   expect(rcpt.events[0].event).to.equal("BetSettled")
   postBalanceB = await ethers.provider.getBalance(signer[1].address)       
   deltaB = postBalanceB.sub(preBalanceB)
   expect(deltaB.toNumber()).to.equal(2e10)  
 })   // it "Work all the way through (B wins)"
 it("Work all the way through (A wins)", async () => {
     .
     .
     .
     .
   expect(deltaB.toNumber()).to.equal(0)
 })   // it "Work all the way through (A wins)"
})     // describe("Casino")

```

## 防止赌博游戏中的滥用

当你写一个智能合同时，你应该考虑恶意用户可能会如何试图滥用它，然后实施策略来阻止这些行为。

### 保护不被泄露

由于合同中没有规定`sideA`必须透露随机数，所以心怀恶意的输了的`sideA`可以避免发布`reveal`交易，并阻止`sideB`收取赌注。

幸运的是，这个问题有一个简单的解决方案:保留一个`sideB`接受赌注的时间戳。如果从时间戳开始已经过了预定的时间长度，并且`sideA`没有用有效的`reveal`来响应，则让`sideB`发出`forfeit`交易来收取赌注。

这就是跟踪函数被调用的时间的原因，前面创建了`placedAt`字段。

### 防止抢跑

以太坊交易不会立即执行。相反，它们被放入一个名为 mempool 的实体中，挖掘者(或合并后的块提议者)可以选择将哪些事务放入它们提交的块中。

通常，所选择的交易是同意支付最多汽油的交易，因此提供最多利润。

一旦`sideA`在内存池中看到`sideB`的`acceptBet`事务，并且随机值会导致`sideA`丢失，`sideA`就可以发出不同的`acceptBet`事务(可能来自不同的地址)。

如果`sideA`的`acceptBet`事务给了矿工更多的汽油，我们可以期待矿工首先执行他们的事务。这样，`sideA`就可以退出赌局，而不是输掉。

这种被称为[前置](https://consensys.github.io/smart-contract-best-practices/attacks/frontrunning/)的策略，是由以太坊的去中心化结构和`sideB`提交`acceptBet`交易后`sideA`和`sideB`之间的信息不对称而成为可能的。

我们无法解决权力下放的问题；内存池必须是可用的，至少对矿工(以及合并后的利益相关者)而言，网络必须是不受审查的。

然而，我们可以通过消除不对称性来防止抢跑。

当`sideB`提交`acceptBet`交易时，`sideA`已经知道`randomA`和`randomB`，因此可以看到谁赢了。然而，`sideB`在`reveal`之前毫无头绪。

如果`sideB`的`acceptBet`只公开了`hash(randomB)`，那么`sideA`也不知道谁赢了，使得前端运行交易没有用。然后，一旦`sideB`接受赌注是区块链的一部分，`sideA`和`sideB`都可以发布揭露交易。

一旦一方发出`reveal`信号，另一方就知道谁赢了，但是如果我们加上`forfeit`交易，除了交易本身的小额费用之外，拒绝透露没有任何好处。

需要注意的一个潜在问题是`sideB`可能会做出与`sideA`完全相同的承诺。然后，当`sideA`显示时，`sideB`也能显示同样的数字。一个数与其自身的异或始终为零。然而，由于这个特定游戏的编写方式，在这个场景中`sideB`将简单地确保`sideA`获胜。

## 结论

在本文中，我们逐行回顾了一个赌场赌博游戏的可靠性合同，以演示如何为区块链构建一个随机数生成器。在确定性机器上创建随机数并不简单，但是通过将任务交给用户，我们成功地实现了一个非常好的解决方案。

我们还回顾了防止区块链博彩游戏中滥用或敌对行为的几种策略。

只要双方都对随机的结果感兴趣，我们就可以确信结果。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。