# 使用 bcrypt 在 Node.js 中进行密码散列

> 原文：<https://blog.logrocket.com/password-hashing-node-js-bcrypt/>

任何系统设计者的最大责任是保护用户数据。数据泄露会造成价值数百万的损失，根据 Imperva 的调查，美国的数据泄露成本最高。

当数据只是纯文本时，误用数据的可能性更高。如果您未能保护数据，下一步是通过加密使其不可读，这样攻击者就不会从中获得太多信息。例如，假设有人获得了你社交媒体个人资料的电子邮件和密码。在这种情况下，很容易在你不知情的情况下访问你的个人资料。但是，如果你的密码被加密了会怎么样呢？即使在攻击后，您的帐户也是安全的。

如果你想保护用户的邮件，那很好，但是保护用户密码是必须的。尽管用户必须设置强密码，但用户和系统都要进行密码保护。幸运的是，有许多方法可以执行加密/解密来帮助提高密码的安全性。本文将向您展示如何对 Node.js 中的 bcrypt 库使用密码散列。

*向前跳转:*

## 什么是密码哈希？

[密码哈希](https://blog.logrocket.com/building-a-password-hasher-in-node-js/)是使用特定算法将密码转换为字母数字的过程。当坏人破坏数据时，散列是有益的。有了哈希，他们得到的数据是哈希格式的，哈希后的数据是无法理解的。一些流行的密码哈希算法包括 bcrypt 和 SHA。在本文中，我们将重点介绍如何使用 bycrypt 来散列 Node.js 中的密码。

```
hash('[email protected]') = 1b21hb2hb1u2gu3g2fxy1v2ux1v2y3vu12g4u3ggvgu43598sa89da98sd79adshuavusdva9sdguasd

```

## 使用 bcrypt 在 Node.js 中进行密码散列

Bcrypt 是一个 l [库，帮助你散列密码](https://www.npmjs.com/package/bcrypt)。它使用基于 [Blowfish 密码](https://en.wikipedia.org/wiki/Blowfish_(cipher))的 [`password-hashing`函数](https://en.wikipedia.org/wiki/Password-hashing_function)。Blowfish 密码是一种对称分组密码，可提供业界最佳的加密速率；因此，它可以用于密码套件和加密产品。

Bcrypt 使用 [salt](https://en.wikipedia.org/wiki/Salt_(cryptography)) 来抵御彩虹表、暴力等攻击。Bcrypt 是一个自适应函数，所以如果你频繁调用 bcrypt 的函数，它会变得更慢。这阻碍了攻击者从暴力攻击中获益的能力。

### Bcrypt 依赖项

Bcrypt 需要一些依赖项才能正常工作。Bcrypt 需要 [node-gyp](https://github.com/nodejs/node-gyp) 包，该包为 [Node.js](https://blog.logrocket.com/tag/node/) 编译本地附加模块。Bcrypt 也依赖于 [Python](https://blog.logrocket.com/implementing-cryptography-python/) ，你将需要≥v2.x. Windows 用户需要 C#和 C++选项随他们的 VS 实例一起安装。您还将需要 [OpenSSL v0.7.7](https://www.openssl.org/) 。

## Node.js 中使用 bcrypt 的密码哈希示例

出于数据安全的考虑，在存储用户密码之前，对其进行加盐和哈希处理是非常重要的。Bcrypt 将简单的密码转换成称为哈希的固定长度字符。在散列密码之前，bcrypt 应用一个`salt`，这是一个独特的随机字符串，使散列不可预测。

让我们创建一个 Node.js 项目，并使用 bcrypt 散列密码。制作了一个`server`文件后，需要安装 bcrypt:

```
$ mkdir bcrypt_demo
$ cd mkdir
$ npm init -y
$ touch app.js
$ npm install bcrypt --save

```

现在，您已经准备好使用 bcrypt 了。让我们导入它并将`saltRounds`定义为成本或工作因素:

```
const bcrypt = require("bcrypt")
const saltRounds = 10
const password = "[email protected]"

```

### Node.js 中使用 JavaScript `async` promise 的密码加密

JavaScript `Promise`是由 [`async`](https://blog.logrocket.com/the-visual-learners-guide-to-async-js-62a0a03d1d57/) 函数返回的对象，表示当前状态。当`Promise`返回给调用者时，它根据条件提供处理操作成功或失败的方法。

密码加密有两种方法。下面是第一种方法:

```
bcrypt
  .genSalt(saltRounds)
  .then(salt => {
    console.log('Salt: ', salt)
    return bcrypt.hash(password, salt)
  })
  .then(hash => {
    console.log('Hash: ', hash)
  })
  .catch(err => console.error(err.message))

```

首先，我们将使用 bcrypt 的`genSalt`函数创建一个`salt`。这里，`genSalt`将把一个自变量作为一个`saltRound`数。然后，如果成功，我们将把结果和密码一起提供给 hash。

作为成功的结果，我们将获得散列。在这个方法中，我们使用了 JavaScript 的`async` promise。

一旦触发节点`app.js`，您可以看到如下所示的输出:

```
Salt: $2b$10$t7oxiwchWGHa/B9w0AzrYO
Hash: $2b$10$t7oxiwchWGHa/B9w0AzrYO2WH2rQbA86YSuQjSTmwIrpC/0ZXN7V2

```

该哈希将与其他详细信息一起存储在数据库中。还有一件事，如果我重新运行代码，你认为我会得到结果吗？它每次都会生成相同的输出吗？

### 使用`bcrypt.compare`函数散列 Node.js 中的密码

显然，不是！`bcrypt.hash`每次都会根据特殊的 salt 生成一个唯一的 hash。这就是它如何防止彩虹表攻击。现在，让我们看看第二种方法:

```
bcrypt
  .hash(password, saltRounds)
  .then(hash => {
    console.log('Hash ', hash)
  })
  .catch(err => console.error(err.message))

```

这里，我们将调用`only has`函数，并且只提供`saltRound`。这也将每次生成一个唯一的散列。现在，我们将如何验证散列？这是执行用户登录所必需的。

因此，对于 bcrypt，我们有一个`bcrypt.compare`函数来处理这一部分:

```
bcrypt
  .hash(password, saltRounds)
  .then(hash => {
          userHash = hash 
    console.log('Hash ', hash)
    validateUser(hash)
  })
  .catch(err => console.error(err.message))

function validateUser(hash) {
    bcrypt
      .compare(password, hash)
      .then(res => {
        console.log(res) // return true
      })
      .catch(err => console.error(err.message))        
}

```

如果`res`为真，则为其匹配密码生成的散列。

## Node.js bcrypt 密码哈希信息

正如您在最后看到的，您将得到一个 60 个字符长的散列:

```
$[algorithm]$[cost]$[salt][hash]
// $2b$10$b63K/D03WFBktWy552L5XuibmiD5SxCrKg9kHCqOYaZwxRjIg14u2

```

哈希的分叉是这样的:

*   `Algorithm`:将是`"$2a$" or "$2b$"`，意思是`BCrypt`
*   `Cost`:表示用于确定迭代次数的指数`2^n`
*   `Salt` : (16 字节(128 位))，base64 编码为 22 个字符
*   `Hash` : (24 字节(192 位))，base64 编码为 31 个字符

### 密码散列数据成本

散列数据将经过一系列的`saltRounds`，产生任何系统或用户都无法预测的安全散列。然后，模块将使用给定值并执行`2^r`。哈希选项数据成本通常是指一轮哈希所需的时间，这取决于系统的硬件。

在 2GHz 内核处理器上，您可以大致预期以下内容:

```
rounds=8 : ~40 hashes/sec
rounds=9 : ~20 hashes/sec
rounds=10: ~10 hashes/sec
rounds=11: ~5  hashes/sec
rounds=12: 2-3 hashes/sec
rounds=13: ~1 sec/hash
rounds=14: ~1.5 sec/hash
rounds=15: ~3 sec/hash
rounds=25: ~1 hour/hash
rounds=31: 2-3 days/hash

```

## 使用 bcrypt 在 Node.js 中进行密码散列的好处

与 MD5、SHA1、SHA2 和 SHA3 等其他哈希方法相比，Bcrypt 具有明显的优势。它们都可以在更短的时间内执行大量数据的哈希运算。假设攻击者有一个强大的系统，能够在几秒钟内尝试 7 亿到 9 亿个密码。您的包含字母数字和特殊字符值的密码将在几秒钟内被破解。

现在您知道了，所有这些哈希方法都不能用来加密密码。现在的主要问题是，bcrypt 如何在这里提供显著的优势？Bcrypt 是基于 Blowfish 键控时间表构建的，并使用了一个工作因子，它决定了哈希函数的开销。了解了这一点后，如果攻击者在单个时间范围内发出多个请求，bcrypt 会变得更慢。所以一般来说，破解一个密码需要 12 年。

此外，bcrypt 使用 salt，这有助于防止像彩虹表攻击这样的攻击，并适合于保护密码。

## 结论

如您所知，保护数据以避免重大损害至关重要。攻击者可能会找到访问您的数据存储的方法，但是对于攻击者来说，加密良好的密码是浪费时间和精力。他们不会从我们的加密数据中得到任何好处。

Node.js 允许我们毫无障碍地使用 bcrypt。在处理用户的密码和其他敏感数据时，没有理由避免它。像 bcrypt 这样的安全散列函数对于构建一个健壮的系统应该是必要的。我建议你用它来储存密码。如果您已经使用 bcrypt 完成了散列，就不必处理暴露用户敏感信息的问题。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.