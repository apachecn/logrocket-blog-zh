# JavaScript 承诺:种族，全部，全部解决，然后是博客

> 原文：<https://blog.logrocket.com/javascript-promises-race-all-allsettled-then/>

*编者按:这篇文章于 2021 年 11 月 8 日更新，以改进编码教程，解决自 2019 年以来发生的 JavaScript 承诺的变化，并更彻底地讨论它们的有益用例。*

JavaScript 中的承诺用于处理异步操作。承诺是表示异步操作最终完成或失败的对象。在 ES2015 原生引入之前，开发人员必须依赖将回调传递到函数或第三方库中，才能使用 JavaScript 中的承诺。

在采用 promises 之前，像 [async.js](https://caolan.github.io/async/v3/) 这样的库被用来处理异步代码。现在，可以使用原生的[承诺对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)，而不必依赖于第三方实现。这样，我们可以避免安装第三方承诺或依赖回调，并陷入[回调地狱](http://callbackhell.com/)。

由于承诺现在是一种天然的构造，它们变得更加平易近人。在本文中，我想介绍一些方法，这些方法将帮助您处理一些更复杂的用例，同时还可以同时处理多个承诺。这些方法是:

1.  `Promise.all()`
2.  `Promise.race()`
3.  `Promise.allSettled()`
4.  `Promise.prototype.catch()`

但是首先，我想介绍一下基于承诺的语法带来的一个主要好处。

## 声明式编程为您的代码增加了好处

当使用方法链接语法和方法名称背后的逻辑(即`then`和`catch`)时，我们可以构建一个代码块，专注于声明它的意图，而不是实际指定它需要如何做我们需要的事情。

让我解释一下。如果你想获取列表中的每个数字，并使其翻倍，会怎么样？你会怎么做？

我们通常学习编写代码的方式是像计算机一样思考:*你需要迭代列表中的每一项，所以你需要一个位置计数器，它需要从 0 到数组中的数字数量，对于每个数字，你需要将其翻倍，并可能将其添加到另一个不同的数组中。*

翻译成:

```
let list = [1,2,3,4,5];
let results = []
for(let counter = 0; counter &lt; list.length; counter++) {
       results[i] = list[i] * 2;
}
console.log(results);
//[2,4,6,8,10]

```

现在，我所建议的是去思考需要发生什么，然后写下来。换句话说:将每个数字映射到它的双精度值。

```
let list = [1,2,3,4,5];
let results = list.map( i => i * 2 );

console.log(results);
//[2,4,6,8,10]

```

这是一个非常简单的例子，但是它展示了声明式编程背后的强大力量。

### 承诺如何有助于更好的代码？

这种方法上的简单改变可以帮助你写出更干净、更易读的代码。阅读第二个例子背后的认知负荷比第一个例子低得多，因为当你使用`for`循环时，你必须在头脑中解析代码并逐行执行，而`map`是你可以在更高层次上快速解释的东西。

这同样适用于使用承诺编写代码。在不使用承诺并且必须传递多个回调函数来响应多个事件的情况下，代码会变得复杂和难以阅读。

使用像`Promise.all()`这样的承诺方法——它将一个承诺数组作为一个参数 a 返回一个承诺，当所有传递的承诺都被解析时该承诺被解析——以及[链接承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises#chaining)的概念，您可以编写更干净、更易读的代码。与使用回调不同，使用承诺，您可以用一种更简洁和直接的方式来可视化您的数据所经历的步骤。

让我展示给你看:

```
authenticateUser(username, pwd, (err, isAuth) => {
    if(err) return dealWithYourErrors(err);
    if(!isAuth) return dealWithUnauthorizedAccess(username);
    getSessionToken(username, (err, token) => {
        if(err) return dealWithYourErrors(err);
        loadUserDetails(username, (err, details) => {
            if(err) return dealWithYourErrors(err);
            let user = new User(username, token, details);
            performAction(user, (err, result) => { //this is what you wanted to do all along
                if(err) return dealWithYourErrors(err);
                sendBackResponse(result);
            })
        })
    })
})

```

上面是嵌套回调的一个经典例子，其中有几条信息需要从不同的服务中获取(或者在不同的步骤中，由于一些其他的逻辑)。

默认情况下，回调只让您[串行处理异步行为](https://blog.logrocket.com/improve-async-programming-with-javascript-promises/)，在这种情况下，这并不理想。`getSessionToken`和`loadUserDetails`都可以并行执行，因为它们不需要对方的结果来执行运算。可悲的是，并行运行`getSessionToken`和`loadUserDetails`需要一些额外的代码，比如使用 async.js 或者编写自己的逻辑。

此外，代码的整个结构是必不可少的，因为它明确说明了如何处理错误和串行调用。您(从事此项工作的开发人员)需要在编写这些步骤时考虑它们，以确保正确的行为。

但是基于承诺的方法可以这样写:

```
authenticateUser(username, pwd)
    .then( preActions )
    .then( performAction )
    .catch(dealWithYourErrors);

```

我相信我们都同意，这是一个简单得多的写作和阅读。让我向您展示这些函数的模拟实现，因为所有这些函数都需要返回承诺:

```
function authenticateUser(user, pwd){ //main function called by the developer
    return new Promise( (resolve, reject) => {
        //auth logic goes here...
        resolve(user); //assuming user and pwd are valid...
    })
}
/** once logged in, we'll need to get the session token and load the user's details
*/
function preActions(username) { 
    return Promise.all([getSessionToken(username), loadUserDetails(username)]);
}

function getSessionToken(username) {
    return new Promise( (resolve, reject) => {
        //logic for getting the session token
        resolve("11111")
    })
}
function loadUserDetails(username) {
    return new Promise( (resolve, reject) => {
        //here is where you'd add the logic for getting the user's details
        resolve({name: 'Fernando'});
    })
}
function performAction() {
    //the actual action: we're just logging into stdout the arguments received
    console.log(arguments);
}
function dealWithYourErrors(err) {
    console.error(err);
}

```

以下是上述代码的亮点:

*   `preActions`使用本地`Promise`对象的`all`方法并行调用两个函数。如果他们中的任何一个失败了(从而拒绝了他们各自的承诺)，那么整个集合都将失败，并且`catch`方法将被调用
*   其他人只是在归还承诺

上面的例子完美地过渡到了我想介绍的第一个方法:`all`。

## 无极.所有()方法

当你不得不处理多个并行异步调用时，[`all`方法](https://blog.logrocket.com/understanding-promise-all-in-javascript/)让你鱼和熊掌兼得。

根据定义，`Promise.all`将运行您的所有承诺，直到满足以下条件之一:

*   它们都解析，这将依次解析该方法返回的承诺
*   其中一个失败了，这将立即拒绝返回的承诺

关于`Promise.all`要记住的是最后一点:你不能处理部分失败。如果其中一个承诺被拒绝，那么整个过程就会停止，并调用失败回调。如果被拒绝的承诺不是在做任务关键的事情，并且其内容可能会丢失，这就不理想了。

想象一个从主数据库获取数据并使用外部服务来丰富结果的搜索服务。这些外部服务不是必需的，它们只是为了帮助您提供更多信息(如果有的话)。

在搜索过程中让这些第三方服务失败会导致整个方法失败，从而中止搜索过程并阻止向您的用户返回有效的搜索结果。

正是在这里，你希望你的内在逻辑允许你所有的承诺被执行，忽略可能的拒绝。

## 用`Promise.allSettled()`解决部分`Promise.all`故障

如果您来自上述用例，那么它就是您所有问题的解决方案。这个方法是提议添加到 JavaScript 规范中的，现在已经被添加到 promise 对象中。

`Promise.allSettled()`法的要旨是，不像前面的`Promise.all()`法，一旦第一个承诺被拒绝，这个不会失败。相反，它将返回一个值列表。这些值将是具有两个属性的对象:

1.  退回承诺的状态(`rejected`或`fulfilled`)
2.  已履行承诺的价值或拒绝承诺的原因

下面的例子显示了实现的运行。

```
var resolved = Promise.resolve(42);
var rejected = Promise.reject(-1);

Promise.allSettled([resolved, rejected]).then(function (results) {
    assert.deepEqual(results, [
        { status: 'fulfilled', value: 42 },
        { status: 'rejected', reason: -1 }
    ]);
});

Promise.allSettled([resolved, rejected]).then(function (results) {
    assert.deepEqual(results, [
        { status: 'fulfilled', value: 42 },
        { status: 'rejected', reason: -1 }
    ]);
});

```

不要让方法的名字迷惑了你，很多人认为“allSettled”和“allResolved”意思一样，这是不正确的。一旦承诺得到*解决*或*拒绝*，它就*解决*——否则，它就是*待定*。查看[一个承诺可能拥有的状态和命运的完整列表](https://github.com/domenic/promises-unwrapping/blob/master/docs/states-and-fates.md)了解更多细节。

## 利用`Promise.race()`实现多个承诺

`race`方法是 promise 对象允许您处理多个承诺的另一种方式。`Promise.race()`方法返回一个承诺，只要 iterable 数组中的一个承诺被满足或拒绝，这个承诺就会被满足或拒绝。这个承诺数组作为参数传递给方法。

当传递给该方法的任何一个承诺被结算时(即，履行或拒绝，但不是待定)，该方法返回一个履行或拒绝的承诺，并带有该承诺的值或原因。

`Promise.race()`方法类似于`Promise.all()`，但是主要的区别是`Promise.race`在返回一个已解析的承诺之前不等待所有的承诺都被解析。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们来谈谈为什么你想要几个并行的承诺，并且只从第一个得到解决的承诺中得到结果。

## 使用`Promise.prototype.catch()`回应被拒绝的承诺

在回应被拒绝的承诺时,`Promise.prototype.catch()`方法非常方便。
例如:

```
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
          reject("Promise has been rejected")
  }, 3000)
});

promise1.catch((reason) => {
  console.error(reason);
});

```

这里，`promise1`是一个三秒后被拒绝的新承诺。然后调用`promise1.catch()`,它运行一个作为参数传递给它的函数。反过来，这个函数有一个参数——`reason`——这是拒绝的原因。

如果`onRejected`抛出错误或返回被拒绝的承诺，则`catch()`返回的承诺被拒绝；否则，`catch()`返回的承诺解决。

由于`catch()`方法返回一个承诺，所以可以被链接。再看前面的例子，可以链接另一个`catch()`方法来捕捉任何进一步的错误或拒绝:

```
promise1.catch((reason) => {
  console.error(reason);
  throw "Error"
}).catch(reason => console.log(reason))

```

## 什么时候用`race`？

有几个例子可以说明为什么要使用`race`方法。现在让我们看两个:

### 1.性能检查

例如，如果性能是您正在构建的项目的一个重要部分，您可能希望拥有数据源的多个副本，以便您可以根据网络流量或其他外部因素，尝试查询所有副本，以找到最快的副本。

您可以在没有承诺的情况下做到这一点，但是这种方法会有额外的开销，因为您必须处理逻辑，以了解哪个承诺首先返回，以及如何处理其他未决请求。

有了 promises 和`race`方法，您可以简单地专注于从所有来源获取数据，让 JavaScript 处理剩下的事情。

```
const fetch = require('node-fetch');
// sources for data
let sources = ["https://catfact.ninja/fact", "https://www.boredapi.com/api/activity"];

// map through the sources and create and 
// return a Promise for each request to the data source 
// creating a new array of promises
let checks = sources.map(source => {
    return new Promise((resolve, reject) => {
        // get the start time
        let start = (new Date()).getTime();
        fetch(source)
            .then(res => res.json())
            .then(data => {
                // send an object of the data, the data source(URL) and time elapsed
                resolve({
                    data: data,
                    source: source,
                    time: (new Date()).getTime() - start
                });
            })
            .catch(err => {
                reject(err);
            })
    })
})

// run the Promise.race() method on the newly created array of promises
Promise.race(checks).then(check => {
    // log out data returned by the first promise to resolve
    console.log(check);
})

```

上面的代码示例显示了如何通过映射源 URL 数组来创建承诺数组。

在`map`方法中，创建一个新的承诺，从数据源 URL 获取数据，如果承诺在`resolve()`中被解析，则返回数据、源 URL 和经过的时间。
如果`catch()`方法有错误，承诺被拒绝并返回错误`reject(err)`。

本质上，我是在检查哪个数据源最快，而不必添加任何特定的逻辑来处理异步解析。如果我想比较结果，我将不得不改为使用`Promise.allSettled`调用。

### 2.确定何时放置装载指示器

另一个你可能想考虑使用`race`方法的例子是当你试图决定是否在你的 UI 中显示一个加载指示器的时候。创建 spa 的一个很好的经验法则是，你的异步调用应该[为用户触发一个加载指示器](https://blog.logrocket.com/spinners-notifications-react-app/)，让他们知道有事情发生了。

但是当底层请求发生得非常快时，这个规则就不理想了，因为你可能在 UI 中得到的只是一条闪烁的消息，一些过得太快的消息。此外，加载时间有时取决于太多的因素，以至于您无法创建一个规则来决定何时显示指示器，以及何时不显示指示器而直接执行请求。

你可以玩转拒绝和解决的概念，就像这样:

```
function yourAsynchronousRequest(params) {
  return new Promise((resolve, reject) => {
       //here is your request code, it'll resolve once it gets the actual data from the server
  });
}

function showDataToUser(params) {
  return yourAsynchronousRequest(params).then( data => console.log("data fetched:", data));
}

function timeout() {
  return new Promise((resolve, reject) => {
    setTimeout(() => reject(), TIMEOUTLIMIT); //TIMEOUTLIMIT is a constant you configured
  });
}

function showLoadingIndicator() {
  console.log("please wait...")
}

Promise.race([showDataToUser(), timeout()]).catch(showLoadingIndicator);

```

现在，竞争是针对实际的异步请求，超时被设置为一个限制器。决定是否显示加载指示器的逻辑隐藏在`race`方法之后。

这里，一个异步请求和一个`timeout`函数被传递给`race()`方法。一旦异步函数能够从服务器获得实际数据，它就会进行解析。

另一方面，`timeout`函数在指定的时间后拒绝承诺。
这将阻止`showLoadingIndicator()`功能立即运行，直到设定的时间过去。

这样，如果`showDataToUser()`返回的承诺在`timeout`过去之前解决并拒绝承诺，将显示用户数据。如果不是并且承诺被拒绝，`.catch`将用于运行`showLoadingIndicator()`。

## 最后的想法

承诺是有趣的，忽略它们不是我当年最好的举动之一，所以我非常高兴我已经决定将它们纳入我的日常编码习惯，如果你还没有，我强烈建议你也这样做。

如果你正在使用这些方法，请在评论中告诉我，我特别想知道你对`Promise.race`方法有什么样的用例！

下次再见！

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.