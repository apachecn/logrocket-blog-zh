# 创建一个 NFT 并将您的元数据上传到 IPFS 日志博客

> 原文：<https://blog.logrocket.com/create-nft-upload-metadata-ipfs/>

不可替代的代币(NFT)行业在过去几年中快速增长。开始是一个疯狂的想法，但是突然，嘣！它成为了下一个大事件。人们仍然在处理区块链背后的想法，但现在，该行业终于准备好将区块链带到一个新的水平。

2021 年 2 月，Figma 首席执行官迪伦·菲尔兹(Dylan Fields)以 750 万美元的价格出售了一件 [NFT 艺术品。Twitter 前首席执行官杰克·多西以 290 万美元的价格](https://www.forbes.com/sites/alexkonrad/2021/03/18/figma-ceo-dylan-field-talks-cryptopunks-nft-beeple-metaverse/?sh=4de05bc85a1d)[出售了他的第一条推文(也是 Twitter 上的第一条推文)。这些数字令人抓狂，尤其是对于这样一种全新的艺术形式。所以即使我不能保证你能赚那么多，我希望你会很兴奋地学习如何建立和销售自己的 NFT！](https://www.theverge.com/2021/3/22/22344937/jack-dorsey-nft-sold-first-tweet-ethereum-cryptocurrency-twitter)

别担心，我掩护你。我将分享[建立一个 NFT](https://blog.logrocket.com/how-to-create-nfts-with-javascript/) 并上传 IPFS 元数据的步骤。

## 什么是 NFT？

NFT 是不可替换令牌的首字母缩写。在智能合约领域，我们可以创建令牌并在区块链上部署它们。

可替代代币意味着代币可以互换，就像美元或比特币一样。价值在哪里都一样。例如，在实物货币中，票据是可替代的。这意味着你可以把一张十美元的钞票换成十张一美元的钞票。价值保持不变，它们可以互换，因为一张 10 美元的钞票并不比另一张更有价值。

不可替换的令牌是不同的，因为它们不可互换。例如，土地和财产是不可替代的。两块土地可能价值不一样，因为一块可能有许多自然资源，而另一块则没有。这是不可替代的；价格取决于某些外部因素。

现在，我们如何将这一切与区块链联系起来？区块链上的可替换代币被称为 ERC-20 代币。该 ERC-20 是以太坊基金会创建的标准格式，用于定义令牌。许多流行的代币已经被创造出来，像 Tether (USDt)，Bitcoin (BTC)，BitTorrent Token (BTT)等等。

这些代币是虚拟货币，可以像真实货币一样交易。你可以交易现实世界中的物品，如汽车、土地或其他商品，并以任何代币支付。

NFT 是使用 ERC-721 标准创建的，该标准承认所有权。这个 ERC-721 是用来存储区块链收藏品的所有权。这些收藏品可以是任何东西，像所有权契约、房子、艺术品等等。

图像，更具体地说是数字艺术作品，是流行的收藏品，因为它们易于存储、共享和出售。

## 如何创建 NFT

构建 NFTs 需要选择(或创建)我们想要拥有的映像。我们将使用 IPFS 来存储图像，生成 JSON NFT 元数据，并将元数据上传到 IPFS。

但是，请注意，图像本身并不存储在区块链上；他们的参考资料是。一个 NFT 有一个唯一的 ID(令牌的散列)、一个 URI(令牌的位置)和所有者的地址。这些以表格形式存储在区块链上，如下所示:

> 我们将在上面使用的 URIs 不是真实的，它们是用来展示真实的样子的。😄

上表中的 URIs 仅包含令牌的 URI。该 URI 指向 IPFS 存储器中令牌的存储。应该有更多关于令牌的信息；诸如令牌描述或令牌符号之类的信息。我们将把它更改为 JSON 元数据，其中包含有关 NFT 的更多信息:

```
{
  "name": "My NFT",
  "description": "This is my NFT",
  "image": "https://ipfs.io/ipfs/Qm.png"
}

```

`name`是 NFT 的名字，`description`是对 NFT 的描述，`image`是图像的位置。还有其他字段，但我们现在不会使用它们。

这是 NFT 的 JSON 元数据，它将存储在区块链中，并在所有节点上复制。有了这个，所有权是不可改变的，永远不能被篡改，除非所有者想把 NFT 转让给别人。

在区块链上，桌子看起来像这样:

你可能会问，为什么要生成元数据？为什么不把图像存储在区块链上呢？这在技术上是可行的，但需要在区块链上保存大量数据。

请记住，在将数据存储到区块链之前，需要支付汽油费。所以，假设你有一个 4.1 兆的小图片。这会花掉你几千美元的油费。你可以想象在区块链上存储你想要的每一张图片会有多贵。

这就是 JSON 元数据的用武之地。元数据指向图像或令牌的细节，令牌被上传到 IPFS，来自 IPFS 的 URI 从元数据中被引用。

## 什么是 IPFS？

IPFS 代表星际文件系统。它是一个分散的文件系统，用于共享和存储文件。根据数据驱动投资者的说法，IPFS 使用内容寻址来唯一地识别全球命名空间中的每个文件。这对于我们的 NFTs 将 NFT 元数据链接到资产或艺术品的存储位置非常重要。

IPFS 与 Google Drive 或 Dropbox 类似，但不相同。Google Drive 和 Dropbox 是集中式存储系统，这意味着它们由网站所有者管理，服务器中的图像可以随意更改。

例如，[这是 Google Drive 中一个红色房子的图片](https://drive.google.com/file/d/1HCUZBnmHurdZhJWkMknEymfHYbkJluLU/view?usp=sharing)。该映像驻留在一个集中的存储系统中，因此映像的所有者可以随意更改该映像。现在，链接的内容可以更改为一个绿色房子的图像(或任何东西)，但仍然保持相同的 URL。

所以我们现在明白了为什么集中式存储系统不适合 NFTs。IPFS 是分散的，不依赖于任何特定的公司，所以这些问题是可以避免的。另外，在 IPFS，文件的 URL 是和文件内容绑定在一起的，所以如果不更改 URL，就不能进行更改。

## 上传图片到 IPFS

我们将使用[塔图姆](https://tatum.io/)上传图像到 IPFS。塔图姆是一个命令行工具，用于上传文件到 IPFS。

我们可以通过几个 API 调用来做到这一点。首先，我们必须选择一个我们想要创造的形象。在我们的例子中，我们将铸造一个带有赃物的猿的图像。😁

![NFT ape with sunglasses](img/b89ead9040dbb383d6c0222fcb339ef6.png)

我们可以使用以下命令将我们的 ape 映像上传到塔图姆:

```
curl --request POST \
  --url https://api-eu1.tatum.io/v3/ipfs \
  --header 'content-type: multipart/form-data' \
  --header 'x-api-key: REPLACE_KEY_VALUE' \
  -F [email protected]_path_to_our_ape_image.jpg

```

上面的命令将上传图像到塔图姆 IPFS。它将返回存储文件的 IPFS CID 标识符:

```
{
  "ipfsHash": "bAerseretERrfdin3gdf"
}

```

这样，我们将创建我们的 NFT 元数据:

```
// JSON
// nft-json-metadata.json
{
  "name": "My NFT",
  "description": "This is my NFT",
  "image": "ipfs://bAerseretERrfdin3gdf"
} 
```

然后，我们将使用之前的 CMD 命令将其上传到塔图姆:

```
curl --request POST \
  --url https://api-eu1.tatum.io/v3/ipfs \
  --header 'content-type: multipart/form-data' \
  --header 'x-api-key: REPLACE_KEY_VALUE' \
  -F [email protected]

```

这将返回存储文件的 CID 标识符。这个 IPFS CID 散列唯一地指向 NFT 元数据。现在，我们要做的下一件事是铸造我们的 NFT JSON 元数据。

塔图姆有一个制造非功能性食物的 API。我们将铸造我们的猿 NFT 到以太坊区块链像这样:

```
curl --request POST \
  --url https://api-eu1.tatum.io/v3/nft/mint \
  --header 'content-type: application/json' \
  --header 'x-api-key: REPLACE_KEY_VALUE' \
  --data '{
    "chain": "ETH",
    "tokenId": "100000",
    "to": "0x687422eEA2cB73B5d3e242bA5456b782919AFc85",
    "contractAddress": "0x687422eEA2cB73B5d3e242bA5456b782919AFc85",
    "erc20": "0x687422eEA2cB73B5d3e242bA5456b782919AFc85",
    "url": "ipfs://bAerseretERrfdin3gdf",
    "provenance": true,
    "authorAddresses": [
      "0x687422eEA2cB73B5d3e242bA5456b782919AFc85"
    ],
    "cashbackValues": [
      "0.5"
    ],
    "fixedValues": [
      "0.5"
    ],
    "fromPrivateKey": "0x05e150c73f1920ec14caa1e0b6aa09940899678051a78542840c2668ce5080c2",
    "nonce": 0,
    "fee": {
      "gasLimit": "40000",
      "gasPrice": "20"
    }
}'

```

就这样，我们把我们的 NFT 铸造成了区块链以太坊！

## 结论

我们从学习什么是 NFT 开始这个教程，以及人们是如何从 NFT 中赚到数百万的。接下来，我们演示了如何使用塔图姆 API 创建区块链以太坊的 NFT。我们还看到了如何为 NFTs 生成元数据并将其上传到塔图姆 IPFS。

NFT 并不像人们想象的那样复杂，你可以很容易地从你喜欢的照片和视频中创建出 NFT，甚至通过出售它们来赚钱。

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。