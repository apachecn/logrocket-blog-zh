# 用 JavaScript 和 Node.js 构建一个随机数生成器

> 原文：<https://blog.logrocket.com/building-random-number-generator-javascript-nodejs/>

## 介绍

在一些使用案例中，程序可能需要一个安全的随机数生成源。通常，我们需要随机数用于游戏功能，如骰子或抽奖、私钥生成或其他需要密码安全来源的类似程序。

在 JavaScript 中，我们在`Math`对象中实现了`random()`方法，这是一个内置对象，具有用于执行数学计算的属性或方法。顾名思义，`random()`方法帮助我们生成随机数。

`Math.random()`方法返回一个介于 0(含)和 1(不含)之间的十进制数或浮点伪随机数。用数学术语来说，这表示为`0 <= x < 1`。

虽然使用这种方法在一定范围内产生随机结果有不同的方式，但`Math.random()`并不是一个真正的随机数生成器。这是因为它是伪随机的；随着时间的推移，这些数字将开始重复，并最终显示出非随机模式。你可能知道，计算机很难产生真正的随机数。

然而，虽然在大多数情况下由`Math.random()`生成的伪随机数通常是足够的，但有时我们需要一个密码安全的随机数生成源。我的意思是，我们希望随机数不容易通过一种模式被猜到，或者最终随着时间的推移重复出现。

在本文中，我们将重点介绍针对这些情况推荐的方法。但是在此之前，让我们检查一下`Math.random()`方法的一些基本用例，这样我们就可以学习在 JavaScript 中生成随机数的最简单的方法。

## JavaScript 中`Math.random()`方法的用例

`Math.random()`返回一个非加密随机数，该随机数从一个称为“种子”的隐藏内部表示开始。种子表示在指定范围内均匀生成的隐藏数字序列的起点。下面解释了这种方法的一些用例。

`Math.random()`方法的第一个(也是最简单的)用例是生成 0 到 1 之间的随机十进制或浮点数:

```
const randomNumber = Math.random()
console.log(randomNumber) outputs -&gt; //0.8446144685704831

```

请注意，通过生成 0 到 1 之间的随机数，我们可以将该随机数乘以另一个数，从而将结果放大到我们需要的任何大小。

例如:

```
const max = 6
const randomNumber = Math.floor(Math.random() * max)
console.log(randomNumber) outputs -&gt; 0,1,2,3,4,5 

```

这种方法的另一个用例是用另一个名为`floor`的方法在`Maths`对象中生成一个介于两个特定整数范围之间的随机整数。`floor`方法返回小于或等于指定数字的最大整数。

我们可以在指定范围内生成一个浮点数，如下例所示:

```
// generating floating point numbers within a range
const max = 4
const min= 2
const result = Math.random()*(max - min)
console.log(result) //1.4597999233994834

// generating random integers within a range
const max = 4
const min= 2
const result = Math.random()*(max - min) + min
console.log(Math.floor(result))//3

```

`Math.random()`函数的其他用例包括在具有最大限制的特定范围内生成随机数。在这里，我们可以使用`ceil`，在`Maths`对象中的另一个方法。

现在，在下一节中，让我们探讨一下利用这些伪随机数发生器的一些缺点。

## `Math.random()`中的安全隐患

在安全性方面也有一些缺点。根据 MDN 文档，`Math.random()`不保证加密安全的随机数。因此，在我们的程序中，最好不要将它们用于任何与安全性相关的事情。

这些安全缺陷部分是由于以下原因:

*   在均匀分布中生成随机整数时所采用的不充分且通常有偏差的逻辑
*   关于使用多少位/字节的随机性，浏览器不一致
*   随机结果总是难以一致地重放，这使得它在本质上是不确定的和不规则的
*   内置种子可能被篡改，使其在完整性方面不合适

由于这些漏洞，[万维网联盟](https://www.w3.org/)提出了[网络加密 API](https://www.w3.org/TR/WebCryptoAPI/) 的实现。在撰写本文时，所有流行的浏览器都通过`crypto`对象向 JavaScript 应用程序提供了这个 API 的实现。

让我们快速看一下 Web Crypto API 以及如何使用它。

## Web 加密 API 简介

Web Crypto API 提供了许多加密方法和函数，可以通过`Window.crypto`属性进行访问。在浏览器中，我们可以利用`crypto.getRandomValues(Int32Array)`方法，该方法保证加密随机数的生成。

在服务器端，Node.js 还提供了标准 Web Crypto API 的实现。为了使用这个模块，我们可以用`require('crypto').randomBytes(size)`来初始化它，因为加密包是 Node 本地的。

Web Crypto API 中使用的伪随机数发生器算法(PRNG)可能因不同的浏览器客户端而异。然而，它适用于大多数加密目的，因为内部种子有足够的熵，可能来自外部来源，如 Unix `/dev/urandom`。

在下一节中，我们将讨论如何利用 Web Crypto API，包括语法、我们可以传递的参数以及返回值。下面就从基本用法开始吧。

## 使用 Web 加密 API

`Crypto.getRandomValues()`方法让我们获得加密的强随机值。
本质上，`Crypto`接口代表一种通用的加密功能。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

它在大多数 web 浏览器中都可用，尽管实现可能有所不同，但它们都需要使用具有足够熵的种子。这是为了确保对性能和安全性没有负面影响。

`getRandomValues()`方法是`Crypto`接口中唯一可以在不安全的上下文中使用的成员。因此，在生成加密密钥时不建议使用，因为它们不能保证返回安全的结果。在这种情况下，我们可以利用`generateKey()`方法。

现在，让我们看看 web crypto API 的`getRandomValues`方法的语法、接受的参数和返回值。

### 句法

Web Cryptography API 接受`ArrayBuffer`类和`TypedArray`的实例作为输入来表示字节序列。

请参见下面的语法:

```
typedArray = cryptoObj.getRandomValues(typedArray);

```

### 因素

`typedArray`是基于整数的`TypedArray`对象。可以是`Int8Array`、`Uint8Array`、`Int16Array`、`Uint16Array`、`Int32Array`或`Uint32Array`。

请注意，数组中的所有元素都用随机数填充。

### 返回值

返回值是作为`typedArray`传入的同一个数组，但是它的内容被新生成的随机数所替换。

在下一节中，我们将看看如何编写一个简单的程序来生成安全的加密随机数。

## 生成随机数

出于安全目的(即，任何可能存在攻击者的地方)需要的每个随机值都应该使用加密安全的伪随机数生成器(也称为 CSPRNG)来生成。

这包括验证或重置令牌、彩票号码、API 密钥、生成的密码、加密密钥等等。

那么，如何才能生成安全可靠的随机数呢？最好的选择是采用一个通过设计来解决这些安全问题的库。在 Node 中，我们有几个选项:

*   为了在一个范围内生成随机数，有 [random-number-csprng](https://www.npmjs.com/package/random-number-csprng) ，它使用了底层的加密 API
*   对于 API 密钥或令牌**、**有 [uuid](https://www.npmjs.com/package/uuid) ，具体来说是`uuid.v4()`函数

下面我们来看一个例子。

启动一个简单的节点应用程序，以采用 npm 包中最简单的示例:

```
var Promise = require("bluebird");
var randomNumber = require("random-number-csprng");

Promise.try(function() {
    return randomNumber(10, 30);
}).then(function(number) {
    console.log("Your random number:", number);
}).catch({code: "RandomGenerationError"}, function(err) {
    console.log("Something went wrong!");
});

Output shown below: 
[email protected] random-number-generator % node index.js 
Your random number: 24
[email protected] random-number-generator % node index.js
Your random number: 14
[email protected] random-number-generator % node index.js
Your random number: 20
[email protected] random-number-generator % node index.js
Your random number: 21
[email protected] random-number-generator % node index.js
Your random number: 11
[email protected] random-number-generator % node index.js
Your random number: 26
[email protected] random-number-generator % node index.js
Your random number: 23
[email protected] random-number-generator % node index.js
Your random number: 15
[email protected] random-number-generator % node index.js
Your random number: 22
[email protected] random-number-generator % node index.js
Your random number: 28

```

如上面的例子所示，我们可以在一个范围内生成加密安全的伪随机数。`randomNumber`方法返回一个承诺，该承诺解析为指定范围内的一个随机数。更多细节可以在 [GitHub 资源库 API 部分](https://github.com/joepie91/node-random-number-csprng#api)找到。

## 在 Node.js 中使用 Web Crypto API

Node.js 提供了标准 Web Crypto API 的实现，尽管在撰写本文时，它仍然是该语言的一个实验性特性。

我们可以通过调用`require('crypto').webcrypto`在 Node 中使用这个模块。这返回了一个`Crypto`类的实例，它提供了对 crypto API 其余部分的访问。

> 正如我们之前讨论过的，`crypto.getRandomValues(typedArray)`生成加密的强随机值。关于节点中 web crypto API 的更多细节可以在[节点文档](https://nodejs.org/api/webcrypto.html#webcrypto_class_crypto)中找到。

## 结论

没有模式或算法适用的真正的随机性是否真的存在是有争议的。然而，对于安全相关的代码，我们需要一个攻击者无法预测的随机数。这种情况下，数据是怎么产生的并不重要，只要猜不出来就行。

为此，万维网联盟发布了 Web Cryptography API，它允许浏览器中的 JavaScript 应用程序使用常见的加密功能，而不必使用任何第三方库。

正如我们前面提到的，这个 API 的`crypto.getRandomValues`方法是 web 应用程序获取加密的安全随机数据的最安全的方法。

Web Crypto API 的所有其他特性都可以通过`crypto.subtle`对象访问。链接到网络加密标准可以在[这里](https://www.w3.org/TR/WebCryptoAPI/)找到。

感谢阅读🙂请不要犹豫，在下面的部分提出你的问题或评论。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.