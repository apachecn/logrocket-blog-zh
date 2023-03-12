# 如何用 Go - LogRocket 博客从零开始构建区块链

> 原文：<https://blog.logrocket.com/how-to-build-blockchain-from-scratch-go/>

区块链是许多分散应用和加密货币的底层技术。它们被认为是这一代人最重大的发现之一。尽管处于早期阶段，区块链已经应用于许多行业，并为开发人员、艺术家、游戏玩家、内容作者等创造了新的工作角色和机会。

本教程旨在通过指导您使用 Go 从头开始构建一个区块链来教您如何工作。如果你听说过区块链，但仍然不知道他们是如何工作的，这篇文章是给你的。

## 为什么要用围棋建造区块链？

[Go](https://blog.logrocket.com/tag/go/) 提供了许多独特的特性和功能，非常适合构建区块链。例如，Go 可以让你不费吹灰之力就创建出高效、高性能的应用程序。

Go 还非常适合构建需要并行性和并发性的应用程序(如区块链),它能够生成和管理数千个 Goroutines。Go 通过其运行时系统实现了自动垃圾收集和堆栈管理。

最后，它将应用程序编译为机器码和单个二进制文件，支持多种操作系统和处理器架构，并且可以轻松部署在服务器基础设施上。

## 先决条件

要跟随并理解本教程，您需要以下内容:

## 什么是区块链？

区块链是在计算机网络的节点之间分配和共享的交易的数字记录。区块链中的每一个交易都被称为一个块，并通过加密技术链接到另一个块。

当您试图构建一个分散的系统，确保数据的安全性和完整性，同时维护每个系统用户之间的信任时，区块链会很有帮助。

## 什么是街区？

我们之前提到过 blocks，您可能想知道它们是什么。简单来说，一个块就是一组数据，多个块集合在一起形成一个区块链。

区块链中的每个块都具有以下性质:

*   要在区块链上记录的数据，例如交易数据
*   块哈希，使用加密技术生成的块的 ID
*   前一个块的哈希，这是区块链中最后一个块的加密哈希。它被记录在每个块中以将其链接到链上并提高其安全性
*   创建块并将其添加到区块链的时间戳
*   工作证明(PoW)，这是导出当前块的哈希所花费的工作量。我们将在教程的后面深入解释这一点

## 入门指南

让我们从创建一个新的 Go 项目并导入所有必要的包来构建我们的区块链开始。创建一个名为`blockchain.go`的文件，并将以下代码保存在其中:

```
package main

import (
        "crypto/sha256"
        "encoding/json"
        "fmt"
        "strconv"
        "strings"
        "time"
)

```

接下来，我们将创建一个自定义块类型来保存我们的区块链数据。将以下代码添加到`blockchain.go`文件中:

```
type Block struct {
        data         map[string]interface{}
        hash         string
        previousHash string
        timestamp    time.Time
        pow          int
}

```

然后，我们将创建一个包含我们的块的自定义`Blockchain`类型。将以下代码添加到`blockchain.go`文件中:

```
type Blockchain struct {
        genesisBlock Block
        chain        []Block
        difficulty   int
}

```

`genesisBlock`属性表示添加到区块链的第一块。相比之下，`difficulty`属性定义了矿工开采一个区块并将其包括在区块链中所必须付出的最小努力。

## 计算块的散列

就像我们之前讨论的那样，块的散列是使用加密技术生成的标识符。我们将通过使用 [SHA256](https://en.wikipedia.org/wiki/SHA-2) 算法对以前的数据块哈希、当前数据块数据、时间戳和功率进行哈希运算，得出区块链的数据块哈希。

让我们为实现这个功能的`Block`类型创建一个方法:

```
func (b Block) calculateHash() string {
        data, _ := json.Marshal(b.data)
        blockData := b.previousHash + string(data) + b.timestamp.String() + strconv.Itoa(b.pow)
        blockHash := sha256.Sum256([]byte(blockData))
        return fmt.Sprintf("%x", blockHash)
}

```

在上面的代码中，我们执行了以下操作:

*   将块的数据转换为 JSON 格式
*   串联数据块以前的哈希、数据、时间戳和工作证明(PoW)
*   使用 SHA256 算法对之前的串联进行哈希运算
*   返回以 16 为基数的散列结果，小写字母表示 A-F

## 开采新区块

挖掘一个新的块涉及生成一个块散列，该块散列以期望数量的零开始(期望数量是挖掘难度)。这意味着如果区块链的难度是三，你必须生成一个以`"000"`开始的块散列，例如`"0009a1bfb506…"`。

因为我们从块的内容中获得块的散列，所以我们需要不断改变当前块的 PoW 值，直到我们获得满足我们的挖掘条件的散列(起始零>难度)。

为了实现这一点，我们将为我们的`Block`类型创建一个`mine()`方法，它将不断增加 PoW 值并计算块散列，直到我们得到一个有效的散列。

将以下代码添加到`blockchain.go`文件中:

```
func (b *Block) mine(difficulty int) {
        for !strings.HasPrefix(b.hash, strings.Repeat("0", difficulty)) {
                b.pow++
                b.hash = b.calculateHash()
        }
}

```

## 创造创世块

接下来，我们将编写一个函数，为我们的区块链创建一个 genesis 块，并返回一个`Blockchain`类型的新实例。

将以下代码添加到`blockchain.go`文件中:

```
func CreateBlockchain(difficulty int) Blockchain {
        genesisBlock := Block{
                hash:      "0",
                timestamp: time.Now(),
        }
        return Blockchain{
                genesisBlock,
                []Block{genesisBlock},
                difficulty,
        }
}

```

这里，我们将我们的 genesis 块的 hash 设置为`"0"`。因为它是区块链中的第一个块，所以没有前一个哈希的值，并且 data 属性为空。

然后，我们创建了一个新的`Blockchain`类型的实例，并存储了创世纪块和区块链难度。

## 向区块链添加新区块

现在，我们已经为块实现了计算它们的散列和挖掘它们自身的功能，让我们看看如何将新块包括到区块链中。

将以下代码添加到`blockchain.go`文件中:

```
func (b *Blockchain) addBlock(from, to string, amount float64) {
        blockData := map[string]interface{}{
                "from":   from,
                "to":     to,
                "amount": amount,
        }
        lastBlock := b.chain[len(b.chain)-1]
        newBlock := Block{
                data:         blockData,
                previousHash: lastBlock.hash,
                timestamp:    time.Now(),
        }
        newBlock.mine(b.difficulty)
        b.chain = append(b.chain, newBlock)
}

```

在这里，我们为`Blockchain`类型创建了一个`addBlock`方法，它执行以下操作:

*   收集交易的详细信息(发送者、接收者和转账金额)
*   使用事务详细信息创建新块
*   使用以前的块哈希、当前块数据和生成的 PoW 挖掘新块
*   将新创建的块添加到区块链

## 检查区块链的有效性

我们已经成功创建了一个可以记录事务的区块链，我们需要一个功能来检查区块链是否有效，这样我们就知道没有任何事务被篡改。

将以下代码添加到`blockchain.go`文件中:

```
func (b Blockchain) isValid() bool {
        for i := range b.chain[1:] {
                previousBlock := b.chain[i]
                currentBlock := b.chain[i+1]
                if currentBlock.hash != currentBlock.calculateHash() || currentBlock.previousHash != previousBlock.hash {
                        return false
                }
        }
        return true
}

```

在这里，我们重新计算了每个块的哈希值，将它们与其他块的存储哈希值进行比较，并检查任何其他块的先前哈希值是否等于它之前的块的哈希值。如果任何检查失败，区块链就被篡改了。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 使用区块链进行交易

我们现在有一个功能齐全的区块链了！让我们创建一个`main()`函数来展示它的用法。

将以下代码添加到`blockchain.go`文件中:

```
func main() {
        // create a new blockchain instance with a mining difficulty of 2
        blockchain := CreateBlockchain(2)

        // record transactions on the blockchain for Alice, Bob, and John
        blockchain.addBlock("Alice", "Bob", 5)
        blockchain.addBlock("John", "Bob", 2)

        // check if the blockchain is valid; expecting true
        fmt.Println(blockchain.isValid())
}

```

## 结论

在本教程中，您了解了区块链是如何工作的，包括什么是块以及它们包含什么，以及如何计算块哈希、实现挖掘块的一致算法、记录区块链上的事务以及验证区块链的真实性。

Go 区块链的源代码可以通过 GitHub Gist 获得。我迫不及待地想看到你用 Go 构建的令人惊叹的东西，因为它非常适合效率和性能是重中之重的应用程序。

编码快乐！

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。