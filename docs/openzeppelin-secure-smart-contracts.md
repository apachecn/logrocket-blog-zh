# 使用 OpenZeppelin - LogRocket 博客构建安全的智能合约

> 原文：<https://blog.logrocket.com/openzeppelin-secure-smart-contracts/>

安全性是区块链技术的核心基础之一。广为人知的网络安全漏洞实际上被归咎于与使用区块链的系统相关联的系统，而不是区块链本身。尽管如此，对于与分散式应用程序交互的用户来说，安全性通常是最重要的。他们可能会想:“网络有多安全？”或者，“我的交易或资产有多安全？”OpenZeppelin 是一家为分散式应用提供安全产品的公司，旨在解决这些问题。

OpenZeppelin 提供开源的 OpenZeppelin 契约，[用 Solidity](https://blog.logrocket.com/smart-contract-programming-languages/#solidity) 编写，用于构建安全的智能契约。OpenZeppelin Contracts 使用基于以太坊的令牌的 ERC 标准，这些令牌可用于许多类型的项目。为了将与[在以太坊](https://blog.logrocket.com/create-deploy-erc-20-token-ethereum-blockchain/)或其他区块链上构建安全智能合同相关的网络风险降至最低，OpenZeppelin 合同被持续审计和测试。

在本教程中，我们将研究如何使用 OpenZeppelin 契约和 Truffle 框架来构建和测试安全的智能契约。此外，我们将演示如何在 Remix IDE 上使用 OpenZeppelin 契约。

## 在松露项目中使用 OpenZeppelin 合同

让我们探索使用 Truffle 框架和 OpenZeppelin 契约构建智能契约。OpenZeppelin Contracts 包含导入到包含我们的实体代码的源文件中的实体代码。这减少了必须手动编写的代码量。

我们将回顾如何设置松露开发环境，以及如何在松露项目中安装和实现 OpenZeppelin。

### 建立块菌开发环境

让我们使用 Truffle 框架为我们的项目建立一个开发环境。

要全局安装框架，请运行以下命令:

```
npm install truffle -g

```

接下来，创建一个新目录来包含 Solidity 项目，`openzeppelin-contracts`:

```
mkdir openzeppelin-contracts

```

现在，将 cd 放入新创建的文件夹，并运行以下 Truffle 命令:

```
truffle init

```

这将创建一个包含文件夹、源文件和配置文件的 Truffle 模板项目。我们可以调整和/或添加更多的源代码到现有的源代码来构建我们的特定项目。

接下来，让我们用 npm 安装 OpenZeppelin Contracts 包:

```
npm init -y
npm install @openzeppelin/contracts

```

现在，我们将启动一些定制令牌项目，使用 OpenZeppelin 契约来节省开发时间，同时确保项目的安全性。当我们开发项目时，我们将引入一些由包公开的接口，以便在我们的代码中使用。

### 使用 OpenZeppelin 合同和 Truffle 构建 ERC 20 项目

让我们建立一个 ERC-20 可替换令牌项目。

首先，将 cd 放入包含 solidity 源文件的`./contracts`文件夹。在这个目录中，创建一个`.sol`文件，`Rocket.sol`。

```
touch Rocket.sol

```

使用您首选的 IDE，打开项目并用以下代码更新`Rocket.sol`文件，以实现自定义令牌:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import '@openzeppelin/contracts/token/ERC20/ERC20.sol';

contract Rocket is ERC20 {
  constructor(uint256 initialSupply) ERC20("ROCKET", "ROC") {
    _mint(msg.sender, initialSupply * (10 ** decimals()));
  }
}

```

该示例代码表示实现自定义令牌所需的 OpenZeppelin Contracts 包接口导入的最小数量。使用`ERC20`函数，我们为令牌定义了一个自定义名称`ROCKET`，以及一个符号`ROC`。

`constructor`函数上的`initialSupply`参数将保存该令牌的供应总量。访问 GitHub 上的 [OpenZeppelin 契约，查看从`ERC20.sol`文件继承的其他接口。](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol)

现在，让我们为智能契约编写一个单元测试。我们将实现迁移文件以在本地开发智能合约，然后编写测试套件。

打开`/migrations`文件夹，新建一个`.js`文件。然后，运行以下命令创建`2_customToken.js`文件:

```
cd migrations
touch 2_customToken.js

```

在新文件中，添加以下 JavaScript 代码，为我们的自定义令牌项目设置一个迁移过程:

```
const Rocket = artifacts.require("Rocket");

module.exports = function(deployer) {
  deployer.deploy(Rocket, 1000000);
}

```

接下来，让我们在`/test`文件夹中创建一个`.test.js`文件。我们将使用这个`.test.js`文件来测试我们的项目。

```
cd test
touch customToken.test.js

```

在新文件中，添加以下 JavaScript 代码，为我们的自定义令牌项目实现一个测试套件:

```
const Rocket = artifacts.require("contracts/Rocket.sol");

contract("Rocket", (accounts) => {
  before(async () => {
    rocket = await Rocket.deployed();
  });

  it("mint 1M worth of tokens", async () => {
    let balance = await rocket.balanceOf(accounts[0]);
    balance = web3.utils.fromWei(balance);
    assert.equal(balance, 1000000, "Initial supply of token is 1000000");
  });

  it("transfer token to another account", async () => {
    let amount = web3.utils.toWei("10000", "ether");
    await rocket.transfer(accounts[1], amount, { from: accounts[0] });
    let balance = await rocket.balanceOf(accounts[1]);
    balance = web3.utils.fromWei(balance);
    assert.equal(balance, 10000, "token balance is 10000");
  });
});

```

在 Truffle 的帮助下，我们定义了单元测试来检查令牌帐户余额，并确认初始供应量与指定的数量相匹配。

我们还定义了持有令牌的第一个账户`accounts[0]`和另一个账户`account[1]`之间的转账交易。这些是松露框架提供给我们的虚拟账户。我们从`account[0]`向`account[1]`转移价值 10，000 的`ROC`代币。

现在，让我们进行测试:

```
truffle test

Compiling your contracts...
===========================
> Compiling ./contracts/Migrations.sol
> Compiling ./contracts/Rocket.sol
> Compiling @openzeppelin/contracts/token/ERC20/ERC20.sol
> Compiling @openzeppelin/contracts/token/ERC20/IERC20.sol
> Compiling @openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol
> Compiling @openzeppelin/contracts/utils/Context.sol
> Artifacts written to /var/folders/z2/963_51js5f370fvrx34q8cr40000gn/T/test--7499-6UUMhtN9GNHv
> Compiled successfully using:
   - solc: 0.8.11+commit.d7f03943.Emscripten.clang

  Contract: Rocket
    ✓ mint 1M worth of tokens
    ✓ transfer token to another account (69ms)

  2 passing (126ms)

```

`truffle test`命令编译我们的智能契约，并将其迁移到由 Truffle 框架构建的临时本地区块链。接下来，Truffle 根据智能契约中定义的功能和特性运行我们的测试套件。

### 使用 OpenZeppelin 合同和 Truffle 构建 ERC-721 项目

现在，让我们建立一个 ERC-721 不可替换令牌项目。ERC-721 指的是为 NFT 智能合同项目定义的标准。不可替换意味着令牌是唯一的。

让我们在`/contracts`文件夹中创建一个`.sol`文件`SpaceShip.sol`。

接下来，我们将下面的源代码添加到`SpaceShip.sol`文件中:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import"@openzeppelin/contracts/token/ERC721/ERC721.sol";
import"@openzeppelin/contracts/utils/Counters.sol";
contract SpaceShip is ERC721 {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIds;
    constructor() ERC721("SpaceShip","SPSP") {}
    function _mintToken(address account, string memory tokenURI) public returns (uint256){
        _tokenIds.increment();
        uint256 newTokenId = _tokenIds.current();
        _mint(account, newTokenId);
        return newTokenId;
    }
}

```

在这段代码中，我们导入了两个对象:`ERC721`类对象和`Counters`库。我们继承了所有定义在`ERC721`类上的公开接口。使用`Counters`库，我们为使用我们的智能契约铸造的每个 NFT 定义唯一的 id。

我们的造币功能`_mintToken`是每个新造的代币将链接到的账户。我们将接收者的账户 ID`address account`作为参数传递给这个 mint 函数。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

根据以太坊社区同意的元数据规范，`tokenURI`参数指向令牌资源，如图像、视频、访问或任何其他有价值的资源。在`_mintToken`函数中，我们调用智能契约公开的`_mint`函数来执行 NFTs 的实际计算和存储。

## 使用 OpenZeppelin 合同和 Remix 构建 ERC-1155 项目

作为我们 OpenZeppelin 合同之旅的最后一站，我们将在以太坊的 IDE Remix 上建立一个 ERC-1155 可替换性不可知、节能的令牌项目。ERC-1155 项目可以用一个合同一次代表多个令牌。

访问[Remix–ether eum IDE](https://remix.ethereum.org/#optimize=false&runs=200&evmVersion=null)获得一个在线 IDE 的实例，我们将使用它来成功地构建和编译我们的项目。

***注意，本教程假设你有一些混音经验***

 **让我们在`/contracts`文件夹中创建一个`.sol`文件`lemonade.sol`。

接下来，我们将下面的源代码添加到`lemonade.sol`文件中:

```
pragma solidity ^0.8.0;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/master/contracts/token/ERC1155/ERC1155.sol";
import "https://github.com/OpenZeppelin/openzeppelin-contracts/master/contracts/access/Ownable.sol";

contract Lemonade is ERC1155, Ownable {
    uint256 public const LEMONADE = 0;
    constructor() ERC1155("") {
        _mint(msg.sender, LEMONADE, 12, "");
    }

    function mint(address account, uint256 tokenID, uint256 supply) public onlyOwner {
        _mint(account, tokenID, supply, "");
    }

    function burn(address account, uint256 tokenID, uint256 amount) public onlyOwner {
        _burn(account, tokenID, amount);
    }
}

```

在这段代码中，我们通过 GitHub 链接将 OpenZeplin 契约导入到 Remix 中，链接到源文件`ERC1155.sol`和`Ownable.sol`。我们在智能合约中实现了`_mint()`和`_burn()`功能。`_burn()`函数接口消除了 ERC-721 规范的限制因素，因为一旦智能合同部署在区块链上，铸币就会立即受到限制。我们还延长了智能合同，以便我们可以在未来创造更多的非功能性交易。

## 结论

在本教程中，我们演示了如何使用 OpenZeppelin 契约和 Truffle 框架来构建和测试 ERC-20 可替换令牌和 ERC-721 不可替换令牌的安全智能契约。我们还演示了如何使用 OpenZeppelin 契约和 Remix 来为 ERC-1155 不可知的可替换性、节能令牌构建智能契约。

OpenZeppelin 合同可用于构建分散式应用程序，范围从 DeFi、治理、访问和安全智能合同到定制项目，如 NFT 游戏、赌注或游戏赚钱项目。

为了更好地理解 OpenZeppelin 合同如何提高 DApp 开发的效率，请阅读 [EIP 规范](https://eips.ethereum.org)并研究[公司文档](https://docs.openzeppelin.com/openzeppelin/)或 [GitHub](https://github.com/OpenZeppelin) 中的 OpenZeppelin 代码库。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。**