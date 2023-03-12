# 建立区块链社交媒体平台——LogRocket 博客

> 原文：<https://blog.logrocket.com/design-build-blockchain-social-media-platform/>

集中式社交媒体，如脸书和 Twitter，并不适合所有的使用案例。例如，集中式社交媒体平台被绑定到一个中央机构，即提供商。该提供商有能力删除或隐藏用户帖子。

此外，由于集中式社交媒体是一个可变的记录，它可能会被更改。这有各种各样的后果。例如，用户可能很难证明他们何时发布了某个内容。这可能会对一些商业案例产生影响，如专利诉讼或基于专家预测的评估。

可变性也带来了恶意行为的风险，用户的帖子可能被修改，或者内容可能在另一个用户的个人资料下发布。

这就是区块链可以改变游戏规则的地方。区块链是一个几乎不变的分类账。它提供了不可伪造或擦除的永久记录，使其成为某些社交网络用例的理想选择。

在本文中，我们将设计并构建一个区块链社交媒体平台，名为 Chirper，它将运行在以太坊之上。

## 内容

要设计一个去中心化的区块链社交媒体平台，我们需要考虑信息将如何存储以及如何访问。

### 信息存储

向区块链提供信息的唯一方法是将其作为交易的一部分。一旦交易被添加到区块，它就成为区块链永久记录的一部分。这并不意味着存储的信息易于搜索。

一旦数据进入区块链，我们可以对其采取额外的操作，但是我们在区块链上写的所有操作都需要钱。出于这个原因，最好将我们自己限制在那些对实现功能绝对必要的操作上。

### 索引

在撰写本文时，区块链以太坊中有超过 1500 万块。将它们全部下载来寻找消息是不可行的。因此，我们将保留一个由地址和数组之间的映射组成的索引。

对于每个地址，该数组将包含该地址发布到的块的序列号。在用户应用程序收到这个列表后，它可以查询以太坊端点来检索块，并在它们中搜索消息。

## 指定数据流和控制流

接下来，我们需要确定用户将如何发布消息，以及他们将如何找到消息。

### 发布消息

发布消息需要两个操作:

*   将消息作为事务的一部分写入
*   将消息的块添加到索引中

这是我们将用来实现消息发布的流程:

1.  用户应用程序用消息向链上合同发送事务；该消息被自动写入以太坊永久记录
2.  协定验证它是由事务直接调用的，而不是由另一个智能协定调用的。这一步是必要的，因为我们用来查找消息的算法只有在被直接调用时才有效(因为它查看事务)
3.  该合同识别发送者和当前阻止号
4.  该协定将块号追加到发送方的消息块数组中。如果发送方还没有消息块数组，这没有关系，因为它被视为零长度数组

### 查找邮件

这是我们将用来使用户能够查找、阅读和解释消息的流程:

1.  用户应用程序使用与所请求的消息相关联的地址调用链上契约。因为这是一个 [`view`函数](https://docs.soliditylang.org/en/v0.8.15/contracts.html#view-functions)(一个只读函数)，所以它只在单个节点上执行，不消耗任何资源
2.  用户应用程序读取列表中的块，包括它们的事务，并对它们进行过滤，以找到满足这些条件的相关事务:
    *   有我们要找的发信人作为来源吗
    *   将`Chirper`合同作为目的地
    *   (可选)具有正确的函数签名。只有当 Chirper 中有多个接受事务的函数时，才需要这样做；如果一个帖子只有一个函数，那么我们不需要检查函数签名
3.  用户应用程序将事务调用数据转换为字符串，以获取用户发布的帖子
4.  用户应用程序将块号转换为时间戳
5.  用户应用程序向用户显示帖子(或帖子的子集)

本文中使用的应用程序源代码可以在 [GitHub](https://github.com/qbzzt/qbzzt.github.io/tree/master/LogRocket/20220720-chirper) 上获得。它包含两个文件，[一个可靠性合同](https://github.com/qbzzt/qbzzt.github.io/blob/master/LogRocket/20220720-chirper/contracts/Chirper.sol)和 [JavaScript 代码](https://github.com/qbzzt/qbzzt.github.io/blob/master/LogRocket/20220720-chirper/test/chirper-test.js)，其中包括 API(如何使用合同)和测试(如何验证合同是否正确工作)。

### 撰写担保合同

我们将用一个最小的契约实现区块链上的社交网络。

最初几行代码指定了许可证和用于编译合同的 [Solidity 编程语言](https://docs.soliditylang.org/en/develop/)的版本。Solidity 仍在快速发展，其他版本，无论是更早的(v0.7.x)还是更晚的(v0.9.x)都可能无法正确编译契约。

```
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;

```

接下来，我们定义合同:

```
contract Chirper {

```

然后，我们包括数据结构，该数据结构保存发送到该契约的每个地址的阻止列表。

```
    // The blocks in which an address posted a message
    mapping (address => uint[]) MessageBlocks;

```

下面是离链应用程序调用来发布信息的函数。

```
    function post(string calldata _message) external {
        // We don't need to do anything with the message, we just need it here
        // so it becomes part of the transaction.

```

我们并不真的需要`_message`，它只是一个参数，以便链外代码将它放入事务中。然而，如果我们不做任何事情，编译器会抱怨。因此，我们将包括它以避免这种行为！

```
        (_message);

```

现在，只有当消息在事务本身中时，才可能在事务中查看消息。如果另一个合同在内部调用了`Chirper`合同，这种情况不会发生；因此，我们不支持这一行动。

```
        require(msg.sender == tx.origin, "Only works when called directly");

```

***N.B.** ，这是教育代码，为简洁而优化；在一个生产系统中，我们可能会以更高的 gas 成本支持内部调用，方法是将那些特定的帖子存储起来，而不是*

来自同一个用户的多个帖子可以被添加到同一个块。当这种情况发生时，我们不想浪费资源多次写入块号。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
        // Only add the block number if we don't already have a post
        // in this block
        uint length = MessageBlocks[msg.sender].length;

```

如果列表为空，那么当前块当然不在列表中。

```
        if (length == 0) {
            MessageBlocks[msg.sender].push(block.number);

```

如果列表不为空，则最后一个条目位于索引`length-1`。如果列表中的任何条目是当前块，它将是最后一个条目。因为块序列号只会增加，所以检查最后一个条目是否小于当前块号就足够了。

```
        } else if (MessageBlocks\[msg.sender\][length-1] < block.number) {

```

我们使用 [`push`函数](https://docs.soliditylang.org/en/v0.8.15/types.html#array-members)向数组末尾添加一个值。

```
            MessageBlocks[msg.sender].push(block.number);
        }
    }   // function post

```

此函数返回特定发件人的阻止列表。

```
    function getSenderMessages(address sender) public view 
        returns (uint[] memory) {
        return MessageBlocks[sender];
    }   // function getSenderMessages

}   // contract Chirper

```

### 创建 JavaScript API

现在，我们将创建 JavaScript API，使用户能够与智能合约进行交互。

将 JavaScript API 放在一个单独的模块中会使事情变得不必要的复杂。相反，您可以在 GitHub repo 中的[测试文件顶部看到代码。](https://github.com/qbzzt/qbzzt.github.io/blob/master/LogRocket/20220720-chirper/test/chirper-test.js)

消息数据以十六进制数字的格式发送，表示消息字符的 ASCII 码，用零填充，长度为 64 个字符的整数倍。比如消息是`Hello`，我们得到的数据就是`"48656c6c6f0000…0000"`。

我们使用下面的函数将从 [ethers.js 库](https://docs.ethers.io/v5/)中获得的数据转换为普通字符串:

```
const data2Str = str => {

```

首先，我们使用`String.match`和一个正则表达式将这个字符串分成一个数组。

`[0-9a-f]`匹配单个十六进制数字；`{2}`告诉程序匹配其中的两个。`[0-9a-f]` {2}周围的斜线告诉系统这是一个正则表达式。`g`指定我们想要一个全局匹配，匹配所有匹配正则表达式的子字符串，而不仅仅是第一个。

在这个调用之后，我们有了一个数组，`["48", "65", "6c", "6c", "6f", "00", "00" … "00"]`

```
  bytes = str.match(/[0-9a-f]{2}/g)

```

现在，我们需要删除所有的填充零。一种策略是使用`filter`函数。`filter`接收一个函数作为输入，并且只返回那些函数返回`true`的数组成员。在这种情况下，只有那些不等于`"00"`的数组成员。

```
  usefulBytes = bytes.filter(x => x != "00")

```

下一步是将`0x`添加到每个十六进制数中，以便正确解释它。为此，我们使用了`map`函数。Map 还接受一个函数作为输入，并对数组的每个成员运行该函数，将结果作为数组返回。在这次通话之后，我们有了`["0x48", "0x65", "0x6c", "0x6c", "0x6f"]`

```
  hexBytes = usefulBytes.map(x => '0x' + x)

```

现在，我们需要将 ASCII 码数组转换成实际的字符串。我们使用`String.fromCharCode`来做这件事。但是，该函数要求每个字符有一个单独的参数，而不是一个数组。语法`..["0x48",` `"0x65",` `"0x63"` `etc.]`将数组成员变成单独的参数。

```
  decodedStr = String.fromCharCode(...hexBytes)

```

最后，前六个字符不是真正的字符串，而是元数据(例如，字符串长度)。我们不需要这些角色。

```
  result = decodedStr.slice(6)
  return result
}  // data2Str

```

下面是根据特定的`Chirper`合同从特定的发送者处获取所有消息的函数:

```
const getMsgs = async (chirper, sender) => {

```

首先，我们调用 Chirper 来获取包含相关消息的块列表。`getSenderMessages`方法返回一个整数数组，但是因为以太坊整数的范围可以达到 2^256-1，所以我们接收一个由 [BigInt](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt) 值组成的数组。`.map(x => x.toNumber())`将它们转换成我们可以用来检索数据块的普通数字。

```
  blockList = (await chirper.getSenderMessages(sender)).map(x => x.toNumber())

```

接下来，我们检索块。这个操作有点复杂，所以我们一步一步来。

为了检索块，包括头和事务，我们使用 ethers.js 函数`[provider.getBlockWithTransactions()](https://docs.ethers.io/v5/api/providers/provider/#Provider-getBlockWithTransactions)`。

JavaScript 是单线程的，所以这个函数立即返回一个 Promise 对象。我们可以通过使用`async x => await ethers…`来告诉它等待，但是这样效率会很低。

相反，我们使用`map`来创造一系列的承诺。然后，我们使用`[Promise.all](https://blog.logrocket.com/understanding-promise-all-in-javascript/)`等待，直到所有的承诺都得到解决。这给了我们一个[块对象](https://docs.ethers.io/v5/api/providers/types/#providers-Block)的数组。

```
  blocks = await Promise.all(
    blockList.map(x => ethers.provider.getBlockWithTransactions(x))
  )

```

`timestamp`是块的函数，不是事务。这里我们创建了一个从块号到时间戳的映射，这样我们可以在以后将时间戳添加到消息中。块号包含在每个[交易对象](https://docs.ethers.io/v5/api/providers/types/#providers-TransactionResponse)中。

```
  // Get the timestamps
  timestamps = {}
  blocks.map(block => timestamps[block.number] = block.timestamp)

```

每个块对象包括一个事务列表；然而，`map`给了我们一个事务数组的数组。在单个数组中处理事务更容易，所以我们用`[Array.flat()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat)`将其扁平化。

```
  // Get the texts
  allTxs = blocks.map(x => x.transactions).flat()

```

现在我们已经从包含我们需要的事务的块中获得了所有的事务。然而，这些交易大部分都是不相关的。我们只希望交易从我们想要的发送者，其目的地是啁啾本身。

如果 Chirper 有多个方法，我们将需要过滤控制调用哪个方法的数据的前四个字节。但是由于 Chirper 只有一种方法，所以这一步不是必需的。

```
  ourTxs = allTxs.filter(x => x.from == sender && x.to == chirper.address)

```

最后，我们需要使用我们之前定义的`data2Str`函数将事务转换成有用的消息。

```
  msgs = ourTxs.map(x => {return {
    text: data2Str(x.data),
    time: timestamps[x.blockNumber]
  }})

  return msgs
}   // getMsgs

```

该函数发布一条消息。与`getMsgs`相反，这是对区块链的一个微不足道的呼叫。它会一直等到事务实际添加到块中，以便保留消息的顺序。

```
const post = async (chirper, msg) => {
  await (await chirper.post(msg)).wait()
}    // post

```

### 用华夫饼和柴测试合同

我们将使用 Waffle 编写合同测试，这是一个智能合同测试库，使用 ethers.js。参考本教程[了解更多关于使用 Waffle 测试以太坊合同的信息](https://hardhat.org/hardhat-runner/docs/other-guides/waffle-testing)。

我们将使用 [Chai 库](https://www.chaijs.com/)进行测试。

```
const { expect } = require("chai")

```

Chai 的工作方式是你用一个`async()`函数`describe`各种实体(在这个例子中是`Chirper`契约)要么成功要么失败。

```
describe("Chirper",  async () => {

```

在`describe`函数中有`it`函数，它们是实体应该具有的行为。

```
  it("Should return messages posted by a user", async () => {
    messages = ["Hello, world", "Shalom Olam", "Salut Mundi"]

```

我们的第一步是使用`[ContractFactory](https://docs.ethers.io/v5/api/contract/contract-factory/)`部署`Chirper`契约，如下所示:

```
    Chirper = await ethers.getContractFactory("Chirper")
    chirper = await Chirper.deploy()

```

接下来，我们发布数组中的所有消息。在进行下一次发布之前，我们对每一次发布进行`await`处理，这样我们就不会得到无序的消息并导致下面的相等比较失败。

```
    for(var i=0; i<messages.length; i++)
      await post(chirper, messages[i])

```

[`getSigners`函数](https://hardhat.org/hardhat-runner/docs/other-guides/waffle-testing#testing-from-a-different-account)获取我们的客户拥有凭证的账户。第一个条目是默认的，所以它是`post`函数使用的条目。

```
    fromAddr = (await ethers.getSigners())[0].address

```

接下来，我们调用`getMsgs`来获取消息。

```
    receivedMessages = await getMsgs(chirper, fromAddr)

```

使用`map`可以让我们检查我们发送的消息列表是否等于我们接收的消息列表。`map`中的函数可以接受两个参数:列表中的值和它的位置。

```
    messages.map((msg,i) => expect(msg).to.equal(receivedMessages[i].text))

  })   // it should return messages posted ...

```

检索正确的消息是不够的。为了显示应用程序工作正常，我们还必须显示应该被过滤掉的消息实际上被过滤掉了。

```
  it("Should ignore irrelevant messages", async () => {  
    Chirper = await ethers.getContractFactory("Chirper")
    chirper1 = await Chirper.deploy()

```

要创建来自不同地址的消息，我们需要获取该地址的钱包。

```
    otherWallet = (await ethers.getSigners())[1]

```

然后，我们使用 [`connect`函数](https://docs.ethers.io/v5/api/contract/contract/#Contract-connect)创建一个带有新签名者的新的`Chirper`契约对象。

```
    chirper1a = chirper1.connect(otherWallet)    

```

从我们正在寻找的地址发送的消息，但是发送到另一个`chirper`实例，也是不相关的。

```
    chirper2 = await Chirper.deploy()
    await post(chirper1, "Hello, world")
    // Different chirper instance
    await post(chirper2, "Not relevant")
    // Same chirper, different source address
    await post(chirper1a, "Hello, world, from somebody else")
    await post(chirper1, "Hello, world, 2nd half")
    await post(chirper2, "Also not relevant (different chirper)")
    await post(chirper1a, "Same chirper, different user, also irrelevant")

    receivedMessages = await getMsgs(chirper1, 
        (await ethers.getSigners())[0].address)
    expected = ["Hello, world", "Hello, world, 2nd half"]
    expected.map((msg,i) => expect(msg).to.equal(receivedMessages[i].text))     
  })   // it should ignore irrelevant messages

}) //describe Chirper

```

## 结论

在本文中，我们展示了如何构建一个分散的社交媒体平台 Chirper，它运行在以太坊区块链上。

与脸书或 Twitter 不同，在我们的区块链平台上发布消息需要用户付费。这是可以预料的。分散式系统比集中式系统成本更高。所以，我们不太可能像在免费社交网络上那样看到那么多的猫图片或政治迷因！

另一方面，我们的 Chirper 区块链社交媒体平台提供了一些优势。

首先，这是一个永久不可更改的记录。其次，数据在链上是公开可用的，业务逻辑是开源的，与用户界面分离。这意味着用户可以切换用户界面，同时保持对相同数据的访问，而不是被绑定到特定提供商的用户界面。

因为一个更好的用户界面不需要与网络效果作斗争来获得认可，所以会有更多的实验和竞争空间，从而带来更好的用户体验。

我希望你喜欢这篇文章。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。