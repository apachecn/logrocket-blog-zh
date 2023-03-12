# NFTs 作为访问令牌

> 原文：<https://blog.logrocket.com/nft-access-tokens-build-crypto-subscription-model/>

NFTs，NFTs，NFTs……这个网络 3 词已经在全世界变得司空见惯。创作者和开发者一样，对不可替代的令牌感兴趣。创作者将 NFTs 视为一种数字艺术，但开发者和企业家将 NFTs 视为一种实用工具，可用于解决若干问题，并为许多日常应用程序增加价值。

作为一名 Web3 开发人员和爱好者，我对 NFT 如何有效地充当验证用户身份的访问令牌很感兴趣。您是否订阅过 over-the-top (OTT)平台？如果您是一名开发人员，您肯定会熟悉这种平台背后的机制。

在本文中，我们将比较传统和应用内加密订阅模式。然后，我们将浏览一个教程，演示如何创建 ERC-1155 智能合约，并使用智能合约、JavaScript、Web3.js 和 Moralis 构建应用内 NFT 订阅模型。

*向前跳转:*

## 先决条件

要跟随本文的教程部分，您需要以下内容:

在我们进入教程之前，让我们比较一下传统和应用内加密订阅模式。

## 传统订阅模式

当用户注册并完成订阅计划的交易时，他们的登录凭据将与他们的订阅计划的详细信息一起存储在数据库中。当用户登录应用程序时，应用程序会检查他们是否订阅了某个计划，然后相应地加载数据。

## 应用内加密订阅模式

现在让我们看看订阅模式在 Web3 世界中是如何运作的。当用户连接他们的 Web3 钱包(例如，MetaMask)并通过在 crypto(例如，ETH 或 MATIC)中支付订阅价格来订阅计划时，会在用户地址的链上生成一个 NFT。

无论用户在哪里登录 DApp 并连接他们的钱包，DApp 都会检查用户是否拥有任何订阅计划的 NFT，然后相应地加载数据。在这种情况下，NFT 充当平台或 DApp 的访问或订阅令牌。

与传统的订阅模型相比，加密订阅模型有几个优点。首先，用户可以很容易地通过交易 NFT 来转移他们订阅的所有权以换取加密。与传统模型相比，加密订阅模型也更高效，并提供了更安全的所有权证明机制。

让我们创建自己的应用内加密订阅模型。

## 应用内 NFT 订阅模式教程

要开发应用内 NFT 订阅模式，我们将从构建 ERC-1155 智能合约开始。然后，我们将编写一些 JavaScript 函数来将订阅模型添加到 DApp 中，并在 Polygon 的 Mumbai testnet 上部署该 DApp。

我们将使用 Web3.js API 与网络和我们的智能合约进行交互，并使用道德 IPFS 来存储 NFT 元数据。

让我们开始吧！

### 创建 ERC-1155 智能合同

开发我们的订阅模型的第一步是使用 [ERC-1155 多令牌标准](https://ethereum.org/en/developers/docs/standards/tokens/erc-1155/)在 Solidity 中编写智能合同。

为了开发 OTT 平台的智能合约，我们将使用 [OpenZeppelin 库](https://www.openzeppelin.com/contracts)。让我们试着使用 solidity 为我们的智能契约编写代码。

我们首先需要从 OpenZeppelin 库中导入以下必需的 Solidity 文件:

*   `SafeMath.sol`:对数字或`uint`执行数学运算
*   `ownable.sol`:存储合同部署者的地址数据，并将其识别为合同所有者
*   帮助我们创建 ERC-1155 合同，并帮助用户在购买或订阅计划时创建 NFT

```
//SPDX-Licence-Identifier: MIT
pragma solidity ^0.8.7;

import "github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeMath.sol";
import "github.com/OpenZeppelin/openzeppelin-contracts/contracts/token/ERC1155/ERC1155.sol";
import "github.com/OpenZeppelin/openzeppelin-contracts/contracts/access/Ownable.sol";

```

接下来，我们需要命名并启动我们的智能合约，还需要启动智能合约的`uint`的`SafeMath.sol`文件。

```
contract DFlix is ERC1155, Ownable {

    using SafeMath for uint256;

}

```

现在，我们有了一个构造函数，它输入并保存`DFlix` 合同的`name`、`symbol`和元数据`uri`，以及一个`uint`变量`totalPlans`，该变量返回可用订阅计划的数量。

```
uint256 public totalPlans; 

constructor(string memory name, string memory symbol, string memory uri) ERC1155(uri) {}

```

契约包括存储所需数据的结构和映射，例如计划的`name`、元数据`uri`、活动计数`subscribers`、`price`(在 Wei 中)、有效`time`周期(以毫秒计)以及订阅者的`plan`和订阅者的`date`。

`struct plan`与`uint`映射，并在映射`plans`中存储`plan`细节；这些是 NFT。接下来，`struct subscriber`与`address`进行映射，并在映射`subscribers`中存储`subscriber`或用户的订阅细节。

```
struct plan {
        string name;
        string uri;
        uint256 subscribers;
        uint256 price;
        uint time;
    }

    struct subscriber {
        uint256 plan;
        uint256 date;
    }

    mapping(uint256 => plan) internal plans;

    mapping(address => subscriber) internal subscribers;

```

接下来，我们有一些修饰符，比如`correctId`，它验证函数调用方作为参数给出的`planId`是否存在。`ifExpired`是另一个起修饰作用的功能；它检查用户的数据，并通过返回一个布尔值来通知他们是否有资格订阅。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
    modifier correctId(uint id) {
        require(id <= totalPlans && id>0, "provide a correct planID");
        _;
    }

    function ifExpired(uint id) internal view returns(bool) {
        if(subscribers[msg.sender].plan == id) {
            if((block.timestamp).sub(subscribers[msg.sender].date) < plans[id].time) {
                return false;
            } else {
                return true;
            }
        } else {
            return true;
        }
    }

```

在这里，我们还有一个不常用的(但很有用！)函数`setURI`，可以为整个 ERC1155 合约设置元数据`uri`。

```
    function setURI(string memory uri) external onlyOwner {
        _setURI(uri);
    }

```

接下来，我们有主要的函数:`addPlan`和`updatePlan`。这些函数只能由协定的所有者调用。`addPlan`和`updatePlan`帮助车主在自己的平台上添加订阅计划；该计划的数据可以在以后更新。这些函数处理存储在映射`plans;`中的数据，它们的函数参数代表必须由所有者提供的所需数据。

```
    function addPlan(string memory _name, string memory uri, uint256 price, uint time) external onlyOwner {
        totalPlans = totalPlans.add(1);
        uint256 id = totalPlans.add(1);
        plans[id] = plan(_name, uri, 0, price, time);
    }

    function updatePlan(uint id, string memory _name, string memory uri, uint256 price, uint time) external onlyOwner {
        plans[id] = plan(_name, uri, plans[id].subscribers, price, time);
    }

```

这就把我们带到了最重要的契约函数:`subscribe`。我们来分解一下。`subscribe`函数检查用户是否使用`correctId`修饰符提供了正确的`planId`，使用`ifExpired`函数验证他们是否有资格订阅，并检查他们是否在 crypto(例如 ETH 或 MATIC)中支付了正确的`price`，因为它是一个`payable`函数。

在执行这些检查后，`subscribe`功能增加计划的订阅计数，并用`planId`和当前的`timestamp`更新订阅者的数据。然后，它会通过减少前一个计划(或同一计划，如果它已过期)的订阅计数来清除用户以前的所有数据。它还使用`_burn`功能销毁用户的任何非活动计划的 NFTs。

最后，该函数为用户的订阅计划生成一个 NFT，并在 crypto (ETH/MATIC)中收取订阅费，因为它是一个付费函数。

智能合约规定用户一次只能持有一个 NFT；当用户订阅一个新的计划时，他们先前计划的 NFT 就会被烧掉。

```
    function subscribe(uint256 planId) external correctId(planId) payable {
        require(ifExpired(planId) == true, "your current plan hasn't expired yet");
        require(msg.value == plans[planId].price, "please send correct amount of ether");
        plans[planId].subscribers = (plans[planId].subscribers).add(1);
        subscribers[msg.sender] = subscriber(planId, block.timestamp);
        _burn(msg.sender, subscribers[msg.sender].plan, balanceOf(msg.sender, subscribers[msg.sender].plan));
        plans[subscribers[msg.sender].plan].subscribers = (plans[subscribers[msg.sender].plan].subscribers).sub(balanceOf(msg.sender, subscribers[msg.sender].plan));
        _mint(msg.sender, planId, 1, "");
        payable(msg.sender).transfer(msg.value);
    }

```

现在，让我们看看智能合约中列出的其余功能。

`withdraw`函数只能由所有者调用。所有者可以调用此功能，通过将合同的 ETH 或 MATIC 余额直接转移到他们的钱包来收取他们的收入。

```
    function withdraw(uint256 amount) external onlyOwner {
        (bool success, ) = payable(owner()).call{value: amount}("");
        require(success, "transfer failed");
    }

```

这里剩下的函数是读取/调用函数。`currentPlan`检查用户是否有活动计划，并从存储在`subscribers`映射中的数据返回用户当前的活动计划 Id。如果用户没有任何活动的订阅计划，该功能将失败或恢复。

```
    function currentPlan(address user) public view returns(uint) {
        require((block.timestamp).sub(subscribers[msg.sender].date) < plans[subscribers[msg.sender].plan].time, "deosn't have any active plan");
        return subscribers[user].plan;
    }

```

`tokenURI`函数返回计划 NFT 的`uri`。`tokenSupply`返回一个计划的订阅数，`tokenPrice`返回一个计划的价格，`totalSupply`返回可用的订阅计划数，`balance`返回合同的`balance`，该合同只能由所有者调用。

```
    function tokenURI(uint id) public correctId(id) view returns(string memory) {
        return plans[id].uri;
    }

    function tokenSupply(uint id) public correctId(id) view returns(uint) {
        return plans[id].subscribers;
    }

    function tokenPrice(uint id) public correctId(id) view returns(uint) {
        return plans[id].price;
    }

    function totalSupply() public view returns(uint) {
        return totalPlans;
    }

```

现在，让我们包装所有代码块，并查看完整形式的智能合约:

```
//SPDX-Licence-Identifier: MIT
pragma solidity ^0.8.7;

import "github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeMath.sol";
import "github.com/OpenZeppelin/openzeppelin-contracts/contracts/token/ERC1155/ERC1155.sol";
import "github.com/OpenZeppelin/openzeppelin-contracts/contracts/access/Ownable.sol";

contract DFlix is ERC1155, Ownable {

    using SafeMath for uint256;

    struct plan {
        string name;
        string uri;
        uint256 subscribers;
        uint256 price;
        uint time;
    }

    struct subscriber {
        uint256 plan;
        uint256 date;
    }

    mapping(uint256 => plan) internal plans;

    mapping(address => subscriber) internal subscribers;

    uint256 public totalPlans;

    constructor(string memory name, string memory symbol, string memory uri) ERC1155(uri) {}

    modifier correctId(uint id) {
        require(id <= totalPlans && id>0, "provide a correct planID");
        _;
    }

    function setURI(string memory uri) external onlyOwner {
        _setURI(uri);
    }

    function ifExpired(uint id) internal view returns(bool) {
        if(subscribers[msg.sender].plan == id) {
            if((block.timestamp).sub(subscribers[msg.sender].date) < plans[id].time) {
                return false;
            } else {
                return true;
            }
        } else {
            return true;
        }
    }

    function addPlan(string memory _name, string memory uri, uint256 price, uint time) external onlyOwner {
        totalPlans = totalPlans.add(1);
        uint256 id = totalPlans.add(1);
        plans[id] = plan(_name, uri, 0, price, time);
    }

    function updatePlan(uint id, string memory _name, string memory uri, uint256 price, uint time) external onlyOwner {
        plans[id] = plan(_name, uri, plans[id].subscribers, price, time);
    }

    function subscribe(uint256 planId) external correctId(planId) payable {
        require(ifExpired(planId) == true, "your current plan hasn't expired yet");
        require(msg.value == plans[planId].price, "please send correct amount of ether");
        plans[planId].subscribers = (plans[planId].subscribers).add(1);
        subscribers[msg.sender] = subscriber(planId, block.timestamp);
        _burn(msg.sender, subscribers[msg.sender].plan, balanceOf(msg.sender, subscribers[msg.sender].plan));
        plans[subscribers[msg.sender].plan].subscribers = (plans[subscribers[msg.sender].plan].subscribers).sub(balanceOf(msg.sender, subscribers[msg.sender].plan));
        _mint(msg.sender, planId, 1, "");
        payable(msg.sender).transfer(msg.value);
    }

    function currentPlan(address user) public view returns(uint) {
        require((block.timestamp).sub(subscribers[msg.sender].date) < plans[subscribers[msg.sender].plan].time, "deosn't have any active plan");
        return subscribers[user].plan;
    }

    function tokenURI(uint id) public correctId(id) view returns(string memory) {
        return plans[id].uri;
    }

    function tokenSupply(uint id) public correctId(id) view returns(uint) {
        return plans[id].subscribers;
    }

    function tokenPrice(uint id) public correctId(id) view returns(uint) {
        return plans[id].price;
    }

    function totalSupply() public view returns(uint) {
        return totalPlans;
    }

    function balance() public view onlyOwner returns (uint) {
        return address(this).balance;
    }

    function withdraw(uint256 amount) external onlyOwner {
        (bool success, ) = payable(owner()).call{value: amount}("");
        require(success, "transfer failed");
    }

}

```

这个智能契约演示了 NFT 如何充当访问令牌的示例。

接下来，我们需要在 Remix IDE 上编译智能合约，然后将其部署到[以太坊](https://ethereum.org/en/)，或者任何其他基于 EVM 的网络，比如[多边形](https://polygon.technology/)。如果你不熟悉在 Remix IDE 上编译合同，你可能会发现[这个指南](https://blog.logrocket.com/build-erc-20-exchange-platform/)很有帮助。

### 将订阅模型添加到 DApp

我们将使用 JavaScript 构建一个应用内 NFT 订阅模型，并使用 Web3.js API 将订阅模型添加到我们的 DApp 前端。

首先，使用以下命令安装 Web3.js API:

```
npm i Web3

```

然后，用您选择的 RPC 节点启动它。对于本教程，我们将使用[道德快速节点](https://moralis.io/speedy-nodes/)。

我们将使用道德 IPFS 来存储 NFT 元数据。

首先，在 Moralis 上创建一个帐户。接下来，创建一个服务器并使用其 [UNPKG](https://unpkg.com/) CDN 链接安装 Moralis SDK。

接下来，使用`Moralis.start()`函数用`addId`和`serverUrl`初始化 Moralis SDK。

我们将使用 Web3.js 和合同的 ABI 和地址启动我们的智能合同。

```
import Web3 from "Web3"
import "https://unpkg.com/moralis/dist/moralis.js";

var NODE_URL = "https://speedy-nodes-nyc.moralis.io/<access-key>/polygon/mumbai";
var provider = new Web3.providers.HttpProvider(NODE_URL);
var Web3 = new Web3(provider);

const serverUrl = "https://<key>.usemoralis.com:2053/server";
const appId = "<app-id>";
Moralis.start({ serverUrl, appId });

// Initiate the contract
var contract = new Web3.eth.Contract(abi, address)

```

### 与订阅模型交互

有了 Web3.js，我们可以在契约中调用重要的函数。让我们看看如何与订阅模式进行交互，以便:

#### 如何添加订阅计划

`addPlan`函数使用 Web3.js `utils`将`price`从 ETH 转换为 Wei。接下来，`uploadFiles`函数将元数据对象文件(如图像)上传到 IPFS。文件值被 IPFS `url`替换。

在执行了`uploadFiles`函数之后，元数据以 JSON 文件的形式上传到 IPFS。然后用 Web3.js 调用合同的 addPlan 函数(包括订阅计划的`name`、`time`期间、元数据`url`、【转换为魏】等参数)

```
// this is my function to upload files like images inside the metadata to IPFS

var data;

async function uploadFiles() {

  for (var i = 0; i < Object.keys(data).length; i++) {

    if (Object.values(data)[i].type && Object.values(data)[i].lastModified) {

      const a = new Moralis.File(Object.values(data)[i].name,Object.values(data)[i]);

      await a.saveIPFS().then(() => {

        data[Object.keys(data)[i]] = "https://ipfs.io/ipfs/" + a.hash();

      });

    }

  }

}

const addPlan = async function (name, price, time, metadata) {
  const _price = Web3.utils.toWei(price.toString(), "ether");
  data = metadata;
  await uploadFiles();
  const a = new Moralis.File("metadata.json", {
    base64: btoa(JSON.stringify(data)),
  });
  a.saveIPFS().then(async () => {
    var url = "https://ipfs.io/ipfs/" + a.hash();
    await window.ethereum.request({ method: "eth_requestAccounts" });
    await window.ethereum.request({
      method: "eth_sendTransaction",
      params: [
        {
          from: user.address,
          to: contract._address,
          data: contract.methods.
                addPlan(name, url, _price, time).encodeABI(),
        },
      ],
    });
  });
};

```

现在让我们看看使用 Web3.js 调用我们的契约中的函数的其他函数。

#### 如何订阅计划

`subscribe`函数调用`window.ethereum`请求为该计划付款。

```
const subscribe = async function (id) {
   var price = await contract.methods.tokenPrice(id).call();
   await window.ethereum.request({ method: "eth_requestAccounts" });
   await window.ethereum.request({
     method: "eth_sendTransaction",
     params: [
       {
         from: user,
         to: contract._address,
         value: Web3.utils.toHex(price),
         data: contract.methods.subscribe(id).encodeABI(),
       },
     ],
  });
};

```

#### 如何检查用户是否有活动计划

使用 Web3.js 调用 read 函数`currentPlan`。该函数用于确定用户是否有活动的订阅计划。

```
const currentPlan = async function (user) {
  return await contract.methods.currentPlan(user).call();
};

```

#### 如何从合同中收回收入

`withdraw` 函数使用`window.ethereum`在 MATIC 中提取合同收入，因为该合同被部署到 Polygon 的孟买网络。

```
const withdraw = async function (amount) {
  await window.ethereum.request({ method: "eth_requestAccounts" });
  await window.ethereum.request({
    method: "eth_sendTransaction",
    params: [
      {
        from: user,
        to: contract._address,
        data: contract.methods.withdraw(amount).encodeABI(),
      },
    ], 
  });
};

```

到目前为止，您应该对如何使用 NFTs 作为订阅模型的访问令牌有了更好的理解。我希望这个练习对你有帮助！

## 结论

在本文中，我们演示了 NFT 如何充当访问令牌。我们浏览了一个演示，展示了如何在前端使用 Web3.js，在后端使用 Solidity，在 IPFS 使用 Morlais 来设置 NFT 订阅模型并将其添加到 DApp。

希望你喜欢这个 Solidity 和 Web3.js 教程。如果您有建议或反馈，或者如果您在代码中遇到任何漏洞或错误，请使用下面的评论。

下篇再见。在那之前，继续学习，继续建设，继续探索 Web3 空间。

WAGMI！

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。