# Web3.py 教程:用 Python - LogRocket 博客开发以太坊区块链的指南

> 原文：<https://blog.logrocket.com/web3-py-tutorial-guide-ethereum-blockchain-development-with-python/>

## 介绍

这些天来，在 web 发展空间里发生了很多事情——你可能听说过 Web 3.0。如果你是这个领域的新手，不完全理解它的含义，或者你被这个新技术淹没了——好吧，不要担心！这篇文章是专为你写的。

在这篇文章中，我们将带着以太坊区块链深入去中心化网络的世界，也被称为 Web 3.0。我们将使用 [web3.py 客户端](http://web3.py/)，这是一个 Python 编程语言客户端，用于与以太坊区块链接口。

最终，我们的计划是涵盖如何连接到以太坊区块链并与之互动。要完全理解并跟随这篇文章，你应该具备:

*   至少有 Python 的基础知识
*   了解如何使用终端
*   构建在区块链上运行的应用程序的知识，也称为智能合约
*   Solidity 编程语言的知识

## 什么是 Web 3.0？

在我们深入这篇文章之前，让我们试着在一个高层次上理解网络的现状，以及 Web 3.0 是如何融入其中的。

正如你可能已经知道的，我们正从 Web 2.0 时代走出来，这个时代基本上加强了中央集权，将过多的控制权掌握在少数几个有权势的人手中。当然，这也带来了许多与[公平数据使用、隐私等相关的挑战和问题](https://ethereum.org/en/developers/docs/web2-vs-web3/)。

有了 Web 3.0，就有了一种自主意识，它为每个人提供了一个公平开放的网络，让每个人都能成为玩家。基本上，这是一场使网络去中心化、可验证和安全的运动。该生态系统依靠高度互操作的系统来消除不可信的第三方中介，并因此而蓬勃发展。

就如何拥有、管理和处理用户数据而言，可验证和公平的用户体验确实是这一演变的真正本质。

与 Web 2.0 不同，Web 3.0 实现了一种对等网络模型，其中每个对等点在网络上维护相同数据的副本，并且整个网络也保持同步。在这种意义上，这些对等体也称为节点。

Web 3.0 实现了用户在线信息的可验证性，并提供了一套用于构建新型应用程序的协议，在这些应用程序中，用户知道他们提供和接收的信息级别，更像是一个开放的 Web，支持点对点应用程序、自主分散应用程序(DApps)、加密安全应用程序等等。

交易高度安全，用户拥有并操作他们的数据。网络作为数据的连接，通过各种协议以分散的方式互连和互操作，并通过[智能合同](https://blog.logrocket.com/develop-test-deploy-smart-contracts-ganache/)起作用。

### 区块链术语定义

Web3 基金会定义了这项新技术的一些要点，强调了在线信息尽可能开放的必要性。

下面是我们深入研究之前需要了解的一些术语:

*   节点——节点存储数据，因此我们可以方便地查询区块链的状态以获得我们需要的数据
*   块——一组或一个互连节点网络被称为一个块；例如，不同以太坊节点的网络被称为以太坊区块链
*   区块链——由一系列区块组成的去中心化点对点网络。第一个块通常被称为起源块，每个后续块都有一个对它之前的块的引用，没有中央权威，每个对等体都在保持链的活力和竞争下一个块中包含什么事务中发挥作用
*   交易——这些或多或少是从一个账户到另一个账户的指令，为了安全起见，这些指令以加密的方式签名；交易通常会引起区块链状态的变化。

![How the nodes, within the block, interact with the EVM, web3.py library, and smart contracts](img/677cc42dad52ac81be5b052cfc6347db.png)

How the nodes, within the block, interact with the EVM, web3.py library, and smart contracts

## Web3.py 和以太坊区块链入门

以太坊区块链是一种分散式技术，为数百万个应用提供动力，如今通常是分散式的。它还为加密货币以太供电。

使用不同的编程语言客户端，有许多不同的方式连接到以太坊区块链。在这篇文章中，我们将关注使用基于 Python 的 [web3.py 库](https://github.com/ethereum/web3.py)与这项技术的交互。

首先，你需要设置一些基本的东西。如果您不确定您的机器上是否安装了最新的 Python 版本，请[继续检查](https://www.python.org/downloads/)。您还需要一个以太坊节点，这是一种连接到该技术或网络的方式，就像我们需要一个 URL 来连接到外部第三方 API 一样。

```
python --version

```

我们还将介绍一些必要的基本功能，并在与以太坊区块链互动时为您提供更多高级功能。基本上，我们将涵盖:

## 选择并连接到我们的以太坊节点

首先，让我们选择并连接到一个以太坊节点。然后，我们将执行一些基本操作。

在选择要使用的节点或提供者时，我们可以选择本地或托管节点选项。在本文中，我们将使用托管版本 [Infura](https://infura.io/) ，原因很简单，连接到本地节点需要大量的工作，包括下载区块链的完整历史记录所需的时间、磁盘空间、计算时间、带宽等等。设置、运行和维护一个节点非常复杂，这实际上不是本文的目的。

也有许多其他人提供免费计划，但 Infura 最适合我们这篇文章的用例，因为我们只对了解这项技术感兴趣，目前还不打算建立一家区块链初创公司。

如果你感兴趣，web3.py 文档[广泛地概述了何时使用本地还是托管节点](https://web3py.readthedocs.io/en/stable/node.html#your-ethereum-node)的争论，以及两者的[最佳用例](https://web3py.readthedocs.io/en/stable/web3.eth.account.html#local-vs-hosted)。我推荐的两个自运行提供商选项是 [Geth](https://geth.ethereum.org/) 和[奇偶校验](https://www.parity.io/)，它们是用于连接到以太坊区块链的本地提供商。

通常，有三种连接以太坊节点的基本方式:

*   通过 HTTP
*   通过网络套接字
*   通过 IPC

目前最受支持的节点是 HTTP 节点，但是 IPC 节点更安全，因为它们依赖于本地文件系统。

### 设置 Infura

现在，让我们前往 Infura 并创建一个帐户。有了 Infura，我们可以通过 HTTP 和 WebSocket 协议即时访问以太网。

继续注册，验证您的电子邮件，并在`infura.io`仪表板上创建一个新项目。给项目起一个你喜欢的名字。出于演示目的，我在这里将我的称为`web3_test`。

在项目仪表板上，您可以看到项目所需的密钥和所有凭证，以及连接到托管以太网或节点所需的端点。请注意，我们还可以在应用程序上设置其他类型的安全性，例如使用 JWT 进行应用程序身份验证。查看 LogRocket 博客上的这篇文章，了解与 JWT 合作的详细概述。

![The Infura dashboard](img/cf748d146a0bfdc96a7958c9450c7250.png)

The Infura dashboard

连接到以太坊节点的格式如下所示:

```
https://<endpoint>.infura.io/v3/<project_id>

```

端点可以是`mainnet`、`ropsten`、`Kovan`、`rinkeby`等等。仪表板以 HTTP 和 WebSockets 两种格式显示了`url`,因此您可以选择适合您的用例的格式。

web3.py 附带了一些我们也可以使用的默认提供者。通常，使用 web3.py 库运行的应用程序的每个实例只支持通过提供者到区块链的单个连接。对于我们的基本用例来说，这通常就足够了，但是我们也可以利用多个提供者来构建多个 web3.py 实例。

在这篇文章中，我们将使用 HTTP 端点。让我们继续将端点添加到我们的`.env`文件中。因此，请务必创建一个新的`.env`文件，以防您还没有这样做。此外，确保将该文件添加到`.gitignore`文件中。

## 质疑以太坊区块链

现在我们已经设置好了，我们将[创建一个小的回购协议](https://github.com/firebase007/web3.py),我们可以用它来尝试与以太坊区块链互动。

首先，让我们在机器上创建一个新文件夹。导航到该文件夹，然后[按照概述的步骤创建虚拟环境](https://github.com/firebase007/web3.py)。确保在本地机器上安装新创建的虚拟环境，以防万一您不想进行系统范围的安装。

在这个文件夹中，我们有`.env`文件、`.gitignore`文件和`test_web3.py`文件，这是我们将在演示中使用的文件。`.env`文件包含我们的 Infura URL，其密钥存储为`INFURA_URL`。我们应该确保将`.env`文件也包含在`.gitignore`文件中。

还要确保你已经安装了`pip`，Python 的包管理器，并且更新到了最新版本。要安装 web3.py 库并开始构建，我们需要通过运行以下命令来安装 web3:

`pip install web3`

我们的`test_web3.py`文件现在看起来像这样，因为我们只是要测试到网络的连接是否是活动的:

```
from web3 import Web3
from decouple import config

infura_url = config('INFURA_URL')
print(infura_url)

# HTTPProvider:
w3 = Web3(Web3.HTTPProvider(infura_url))
res = w3.isConnected()
print(res)

```

正如你所看到的，我们还安装了一个新的包， [`decouple`](https://pypi.org/project/python-decouple/) ，它允许我们的代码访问环境变量，而不管它运行的环境是什么。注意，这是个人的选择，因为在这种情况下，我们也可以利用`python dot env`包来访问环境变量。

```
pip install python-decouple

```

当我们在终端中运行`python test_web3.py`时，我们得到以下输出:

```
(.venv-py3) [email protected] web3py_tutorial % python test_web3.py        
https://mainnet.infura.io/v3/<project_id>
True

```

这意味着我们可以访问我们的`env`变量，我们连接到网络。所以，让我们开始查询。例如，要获得最新的块，我们可以:

```
latest_block = w3.eth.get_block('latest')

print(latest_block)

```

并得到下面的结果:

```
True
AttributeDict({'baseFeePerGas': 106360190089, 'difficulty': 10166935943834278, 'extraData': HexBytes('0xe4b883e5bda9e7a59ee4bb99e9b1bc380021'), 'gasLimit': 29980831, 'gasUsed': 1834730, 'hash': HexBytes('0x9519c67cff19cc78de4c79c5a83a695a9ee532d813ee8eeb2f880d048563f8d6'), 'logsBloom': HexBytes('0x9022500244180211008021008181033400080000280400000045000a020280402414000008000020000009000248010002004080488021008a40000000200002000000802200082818000208010000640000284020600200000030008000000a102200000200808000241000000008c02100000000200400430001d00008c010000100060280000000c0000000600188804004010140000804020040001000800a0002000080280206014090c80280100000000100008000408204020048100340000002010000a40800000040400000000802010500001000000006010120000118200000000000020000004008009040000004000000404058401000000004'), 'miner': '0x829BD824B016326A401d083B33D092293333A830', 'mixHash': HexBytes('0xe4b3c7d6e45ea145a0b386ce5c68c522d85ab28f782648236ec6930e5fe8d913'), 'nonce': HexBytes('0x53dec07d101b2e87'), 'number': 13549938, 'parentHash': HexBytes('0x34045975d949f8a5c0db0c1e9e2461f8453a4aab3a3dd6e7602ef1eb331874fe'), 'receiptsRoot': HexBytes('0x77ddb86550c14c8a02185239f56861e011cfdba87409b9b8abcbd66cbcbcfbc7'), 'sha3Uncles': HexBytes('0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347'), 'size': 5611, 'stateRoot': HexBytes('0xa8c91e4a0ec984bc8e0606b2b8bf603bf9a6daf3c8aef6c1342f8683c968b2d7'), 'timestamp': 1636025184, 'totalDifficulty': 33727232539851256019228, 'transactions': [HexBytes('0x6df18cdb083066fd81f2376f82b7ebbdc26d0e6eee2a73dcc7d0e14f0d2e647e'), HexBytes('0xa5d3fc54f0d7c3f1b03882f72d6b1cb90dbc93ea9a3ff8701d905aa5b95007c3'),HexBytes('0x93f0ae71ad39561dba27bee65f8800de8228d97b9ce460d2f983759f8e0d7abd'), 'uncles': []})
(.venv-py3) [email protected] web3py_tutorial % 

```

正如我们在上面看到的，每个块都有一个对它之前的块或产生它的块的引用。这被称为`parentHash`并且仅仅是前一个块的散列。

## 检查以太坊地址的有效性

让我们看看如何检查以太坊地址是否有效。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
//checking an eth address is valid with the is_address method
is_address_valid = w3.isAddress('0x6dAc6E2Dace28369A6B884338B60f7CbBF7fb9be')

print(is_address_valid) //returns True

```

基本上，如果值是可识别的[以太坊地址格式](https://web3py.readthedocs.io/en/stable/web3.main.html#addresses)之一，这个方法返回`True`。有方法[验证地址是否是校验和地址](https://web3py.readthedocs.io/en/stable/web3.main.html#web3.Web3.isChecksumAddress)，以及将地址转换为校验和地址。

为了获得以太坊地址中的余额，我们执行以下操作:

```
check_sum = w3.toChecksumAddress('0xd7986a11f29fd623a800adb507c7702415ee7718')
balance = w3.eth.get_balance(check_sum)
print(balance)

// run the code above 
(.venv-py3) [email protected] web3py_tutorial % python test_web3.py
156805980000000000

```

注意，在我们能够检查余额之前，我们首先将地址转换成一个[校验和地址](https://web3py.readthedocs.io/en/stable/web3.main.html#web3.Web3.isChecksumAddress)。如果不这样做，我们会得到下面的错误:

```
  File "/Users/retina/.venv-py3/lib/python3.8/site-packages/web3/_utils/validation.py", line 182, in validate_address
    raise InvalidAddress(
web3.exceptions.InvalidAddress: ('Web3.py only accepts checksum addresses. The software that gave you this non-checksum address should be considered unsafe, please file it as a bug on their platform. Try using an ENS name instead. Or, if you must accept lower safety, use Web3.toChecksumAddress(lower_case_address).', '0xd7986a11f29fd623a800adb507c7702415ee7718')

```

因此，转换为校验和地址总是一个好主意。另外，注意得到的余额是一种叫做`Wei`的格式。要将这种货币转换成我们熟悉的东西——很可能是以太——我们可以使用下面的方法。

```
ether_value  = w3.fromWei(balance, 'ether')
print(ether_value)
Decimal('')

// we get the result below which the balance in that account
0.15680598

```

注意，这个方法返回转换成指定货币的`Wei`中的值。该值以小数形式返回，以确保非常高的精度。

我们还可以通过事务散列来查找事务，如下所示:

```
trans = w3.eth.get_transaction('0x0e3d45ec3e1d145842ce5bc56ad168e4a98508e0429da96c1ff89f11076da36d')

print(trans)

AttributeDict({'accessList': [], 'blockHash': None, 'blockNumber': None, 'chainId': '0x1', 'from': '0xeEE20e4eE5381042bd81c44f1a0Fcf5c5345464a', 'gas': 56659, 'gasPrice': 120459037304, 'hash': HexBytes('0x0e3d45ec3e1d145842ce5bc56ad168e4a98508e0429da96c1ff89f11076da36d'), 'input': '0x095ea7b30000000000000000000000007a250d5630b4cf539739df2c5dacb4c659f2488dffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff', 'maxFeePerGas': 120459037304, 'maxPriorityFeePerGas': 1567486718, 'nonce': 237, 'r': HexBytes('0xb14b058d01455f54579260c47bfbeccc82fdcdf3939e58fcc808d59f67470ffc'), 's': HexBytes('0x209ca148e807d34f794ca1fa82007b6ef52ede94c0d98b37c1e75722101323c8'), 'to': '0xd665ce6Ef8AdA72B1CF946A6a71508bDD6D2EE04', 'transactionIndex': None, 'type': '0x2', 'v': 0, 'value': 0})
(.venv-py3) [email protected] web3py_tutorial % 

```

或者我们可以查找交易收据，如下所示:

```
trans_receipt = w3.eth.get_transaction_receipt('0xd0f9e247581f9d4c5177fb315e7115e50fc9f673e0915b4b64f3ef5c1b8b81aa')
print(trans_receipt)

AttributeDict({'blockHash': HexBytes('0x166eff2ec3e1375ff70c1dd49b7e4e00dab4802f094fbf81d4021d6d0ac48cb8'), 'blockNumber': 13557150, 'contractAddress': None, 'cumulativeGasUsed': 1719841, 'effectiveGasPrice': 270600000000, 'from': '0xDBD0C0C297035F3D9FD6788B6deC7A28dAd97C63', 'gasUsed': 47216, 'logs': [AttributeDict({'address': '0xd665ce6Ef8AdA72B1CF946A6a71508bDD6D2EE04', 'blockHash': HexBytes('0x166eff2ec3e1375ff70c1dd49b7e4e00dab4802f094fbf81d4021d6d0ac48cb8'), 'blockNumber': 13557150, 'data': '0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff', 'logIndex': 23, 'removed': False, 'topics': [HexBytes('0x8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925'), HexBytes('0x000000000000000000000000dbd0c0c297035f3d9fd6788b6dec7a28dad97c63'), HexBytes('0x0000000000000000000000007a250d5630b4cf539739df2c5dacb4c659f2488d')], 'transactionHash': HexBytes('0xd0f9e247581f9d4c5177fb315e7115e50fc9f673e0915b4b64f3ef5c1b8b81aa'), 'transactionIndex': 46})], 'logsBloom': HexBytes('0x00000000000000000000000000000000000000000000000000010400000000000000000000000000000000000000000000000000000880000000000000200000000000000000000000000000000000000000000000000000000000000000000000080000000000000000000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000000000000200000000000800000000000000000000000000000000000000020000010000000000000000000000000000000000000000000000000000000000000'), 'status': 1, 'to': '0xd665ce6Ef8AdA72B1CF946A6a71508bDD6D2EE04', 'transactionHash': HexBytes('0xd0f9e247581f9d4c5177fb315e7115e50fc9f673e0915b4b64f3ef5c1b8b81aa'), 'transactionIndex': 46, 'type': '0x0'})
(.venv-py3) [email protected] web3py_tutorial % 

```

## 什么是智能合约？

智能合约基本上是在区块链上运行的程序，并且基于某些预先定义的条件。最基本的，它们由控制驻留在以太坊区块链上特定地址的数据状态的函数组成。

智能合约不同于普通用户帐户，因为它们是在区块链上编程和部署的，并将按编程运行。与普通用户帐户一样，智能合约也有地址，这意味着我们可以像在区块链上的用户帐户/地址上一样在其上进行交易。

另一方面，创建一个合同可能会花费一些钱，通常称为“汽油费”，因为您将使用计算和网络存储。此外，从外部帐户到合同地址的交易可以触发代码，这反过来会导致许多操作，具体取决于智能合同的编程方式。

使用智能合同进行交互或工作需要大量工具来帮助我们快速实现目标。其中包括:

在本教程中，我们将只使用契约地址及其 ABI 来调用智能契约函数。

现在，让我们继续使用`web3.py`与以太坊区块链上的智能合约公开功能进行交互。

## 与智能合同功能交互

正如您可能已经猜到的，我们可以使用几种方法来与智能合约进行交互。然而，为了与公开的智能合约函数进行交互，我们需要关于它们的地址和抽象二进制接口(ABI)的信息，ABI 是包含智能合约如何工作的细节的 JSON 数组。

web3.py 库公开的 API 通过 JSON RPC 与以太坊区块链交互，这是一种轻量级的无状态协议。关于 JSON RPC 协议的更多信息，请查看[规范链接](https://www.jsonrpc.org/specification)。由于这不是[写智能合同的帖子](https://blog.logrocket.com/develop-ethereum-smart-contracts-truffle-suite/)，我们将与区块链以太坊上部署的现有合同进行互动。为此，我们需要一个已部署契约的地址及其 ABI。

让我们前往[以太扫描](https://etherscan.io/)，一个块浏览器，得到一个。复制十八处的地址，是`0xd665ce6Ef8AdA72B1CF946A6a71508bDD6D2EE04`。现在，为了读取当前契约的状态，我们传递地址和 ABI:

```
address = '0xd665ce6Ef8AdA72B1CF946A6a71508bDD6D2EE04'
abi = '[{"inputs":[],"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"owner","type":"address"},{"indexed":true,"internalType":"address","name":"spender","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"}],"name":"Approval","type":"event"},{"anonymous":false,"inputs":[{"indexed":false,"internalType":"uint256","name":"_maxTxAmount","type":"uint256"}],"name":"MaxTxAmountUpdated","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"previousOwner","type":"address"},{"indexed":true,"internalType":"address","name":"newOwner","type":"address"}],"name":"OwnershipTransferred","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"from","type":"address"},{"indexed":true,"internalType":"address","name":"to","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"}],"name":"Transfer","type":"event"},{"inputs":[{"internalType":"address","name":"owner","type":"address"},{"internalType":"address","name":"spender","type":"address"}],"name":"allowance","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"approve","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"account","type":"address"}],"name":"balanceOf","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"decimals","outputs":[{"internalType":"uint8","name":"","type":"uint8"}],"stateMutability":"pure","type":"function"},{"inputs":[{"internalType":"address","name":"notbot","type":"address"}],"name":"delBot","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"manualsend","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"manualswap","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"name","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"pure","type":"function"},{"inputs":[],"name":"openTrading","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"owner","outputs":[{"internalType":"address","name":"","type":"address"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"removeStrictTxLimit","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"renounceOwnership","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address[]","name":"bots_","type":"address[]"}],"name":"setBots","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"bool","name":"onoff","type":"bool"}],"name":"setCooldownEnabled","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"symbol","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"pure","type":"function"},{"inputs":[],"name":"totalSupply","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"pure","type":"function"},{"inputs":[{"internalType":"address","name":"recipient","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"transfer","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"sender","type":"address"},{"internalType":"address","name":"recipient","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"transferFrom","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"stateMutability":"payable","type":"receive"}]'

contract_instance = w3.eth.contract(address=address, abi=abi)
res = contract_instance.functions.totalSupply().call()
print(res)
//1000000000000000000000
(.venv-py3) [email protected] web3py_tutorial % 

```

从上面我们可以看到，我们已经调用了公共函数`totalSupply()`，它为我们提供了令牌的总供应量。

我们还可以调用契约定义中可用的其他公开公开的方法。例如，我们可以检查符号:

```
symbol = contract_instance.functions.symbol().call()
print(symbol)
//SHIBACHU
(.venv-py3) [email protected] web3py_tutorial % 

```

我们现在已经了解了如何从已部署的智能合约中读取数据。需要注意的是，该契约上还存在其他公共方法或函数，比如查询账户余额、执行外部转账、批准外部转账的`balanceOf`方法。

我们还可以在区块链上执行的其他操作包括:

此处未涉及的其他合同 API 可在文档的本节[中参考。如果你想了解如何编译、部署和测试你的智能合约和 DApps，你也可以参考](https://web3py.readthedocs.io/en/stable/overview.html#id3)[的安全帽文档](https://hardhat.org/getting-started/)。

## 在区块链以太坊进行交易

为了[在以太坊区块链上进行交易](https://web3py.readthedocs.io/en/stable/overview.html#making-transactions)，我们需要访问[我们的私钥](https://web3py.readthedocs.io/en/stable/web3.eth.account.html#local-vs-hosted-keys)，如果我们使用远程或托管节点，我们需要自己管理这些私钥。如果我们打算[执行诸如签署交易、消息等动作](https://web3py.readthedocs.io/en/stable/web3.eth.account.html#some-common-uses-for-local-private-keys)，则需要一个密钥。

如果我们使用 metamask(一种允许用户通过浏览器扩展轻松访问其以太坊钱包，然后能够与 DApps 进行交互的钱包)，我们可以轻松导出我们的私钥，并使用 web3.py 中的本地私钥工具来签署和发送交易。

![The MetaMask interface](img/a56247f566cd5cb18970979e402e9bfe.png)

## 结论

Web3.py 通过一组公开的 API 与以太坊区块链进行交互。这个库是基于最初对 [web3.js 库](https://blog.logrocket.com/ethereum-blockchain-development-using-web3-js/)的工作而构建的。

web3.py 库提供了一个特定于编程语言的客户端接口，用于与以太坊区块链中已经存在的数据进行交互。使用 web3.py 库，我们可以在区块链上创建或执行新的事务，从区块链读取数据，存储这些数据，并将其用于我们想要的任何特定用例。

在 web3.py 文档的[示例部分已经详细介绍了与以太坊区块链交互的更多示例。您还可以在那里找到 web3.eth 库](https://web3py.readthedocs.io/en/stable/examples.html)的 [API 文档以及与以太坊区块链交互所需的所有方法。](https://web3js.readthedocs.io/en/v1.5.2/)

LogRocket 博客早些时候报道了[如何使用 Ganache](https://blog.logrocket.com/develop-test-deploy-smart-contracts-ganache/) 开发、测试和部署智能合约，并写了一篇关于[使用 Truffle Suite](https://blog.logrocket.com/truffle-suite-tutorial-develop-ethereum-smart-contracts/) 开发以太坊智能合约的详细文章，以及一篇关于[使用`web3.js`](https://blog.logrocket.com/ethereum-blockchain-development-using-web3-js/) 的类似教程。干杯，下次再见！🥂

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。