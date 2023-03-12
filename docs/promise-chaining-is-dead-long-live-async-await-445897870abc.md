# 承诺链已经死了。异步/等待日志火箭博客万岁

> 原文：<https://blog.logrocket.com/promise-chaining-is-dead-long-live-async-await-445897870abc/>

![](img/9bbfaddb0055b485a0a57c81ed458553.png)

虽然异步函数一直存在，但它们经常被搁置不用。Async/await 可能被一些人认为是一个弃儿。

为什么？

一个常见的误解是 async/await 和 promises 是完全不同的东西。

剧透一下，他们不是！Async/await 基于承诺。

仅仅因为你使用承诺并不意味着你被束缚在承诺链的野蛮中。

在本文中，我们将了解 async/await 如何真正让开发人员的生活变得更加轻松，以及为什么您应该停止使用承诺链。

让我们看看承诺链:

```
// Using promise chaining
getIssue()
  .then(issue => getOwner(issue.ownerId))
  .then(owner => sendEmail(owner.email, 'Some text'))
```

现在让我们看看用 async/await 实现的相同代码:

```
// Using async functions
const issue = await getIssue()
const owner = await getOwner(issue.ownerId)
await sendEmail(owner.email, 'Some text')
```

嗯，它看起来像简单的语法糖，对不对？

像大多数人一样，我经常发现我的代码看起来简单、干净、易读。其他人似乎也同意。但是到了要做出改变的时候，修改比预想的要难。这并不奇怪。

这正是承诺链所发生的事情。

我们来看看为什么。

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)

### 易于阅读，易于维护

假设我们需要在之前的代码中实现一个非常小的改变(例如，我们需要在邮件内容中提到问题编号——类似于`Some text #issue-number`)。

我们该怎么做？对于 async/await 版本，这是微不足道的:

```
const issue = await getIssue()
const owner = await getOwner(issue.ownerId)
await sendEmail(owner.email, `Some text #${issue.number}`) // tiny change here
```

前两行不受影响，第三行只需要做很小的改动。

承诺链版本呢？让我想想。

在最后的`.then()`中，我们可以访问`owner`，但不能访问`issue`引用。这就是承诺链开始变得混乱的地方。我们可以试着用这样的东西来清理它:

```
getIssue()
  .then(issue => {
    return getOwner(issue.ownerId)
      .then(owner => sendEmail(owner.email, `Some text #${issue.number}`))
  })
```

正如你所看到的，一个小的调整需要改变几行原本漂亮的代码(比如`getOwner(issue.ownerId)`)。

### 代码是不断变化的

当实现非常新的东西时尤其如此。例如，如果我们需要在电子邮件内容中包含来自对名为 getSettings()的函数的异步调用的附加信息，该怎么办？

它可能看起来像这样:

```
const settings = await getSettings() // we added this
const issue = await getIssue()
const owner = await getOwner(issue.ownerId)
await sendEmail(owner.email,
  `Some text #${issue.number}. ${settings.emailFooter}`) // minor change here
```

你如何使用承诺链来实现它？您可能会看到类似这样的内容:

```
Promise.all([getIssue(), getSettings()])
  .then(([issue, settings]) => {
    return getOwner(issue.ownerId)
      .then(owner => sendEmail(owner.email,
        `Some text #${issue.number}. ${settings.emailFooter}`))
  })
```

但是，对我来说，这导致了草率的代码。每当我们需要对需求进行更改时，我们都需要对代码进行太多的更改。恶心。

因为我不想嵌套更多的`then()`调用，而且我可以并行地调用`getIssue()` 和`getSettings()`，所以我选择了做一个`Promise.all()`，然后做一些解构。的确，这个版本比`await`版本更好，因为它是并行运行的，但还是很难读懂。

我们能否优化`await`版本，使事情并行运行，而不牺牲代码的可读性？让我们看看:

```
const settings = getSettings() // we don't await here
const issue = await getIssue()
const owner = await getOwner(issue.ownerId)
await sendEmail(owner.email,
  `Some text #${issue.number}. ${(await settings).emailFooter}`) // we do it here
```

我已经删除了`settings`任务右侧的`await`，并将它移到了`sendEmail()`呼叫中。通过这种方式，我创造了一个承诺，但不是等到我需要它的价值的时候。与此同时，其他代码可以并行运行。就这么简单！

### 不需要 Promise.all()因为它已经死了

我已经演示了如何在不使用`Promise.all()`的情况下简单有效地并行运行承诺。所以这意味着它完全死了，对吗？

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

嗯，有些人可能会说，一个完美的用例是当你有一组值，你需要把它和一组承诺联系起来。例如，你有一个要读取的文件名数组，或者一个要下载的 URL 数组，等等。

我认为那些人错了。相反，我建议使用外部库来处理并发性。例如，我将使用蓝鸟的 Promise.map()，在那里我可以设置一个[并发限制](http://bluebirdjs.com/docs/api/promise.map.html#map-option-concurrency)。如果我必须下载 N 个文件，用这个工具我可以指定不超过 M 个文件同时下载。

### 您几乎可以在任何地方使用 await

当你试图简化事情时，Async/await 大放异彩。想象一下，如果使用承诺链，这些表达式会有多复杂。但是有了 async/await，它们就简单明了了。

```
const value = await foo() || await bar()

const value = calculateSomething(await foo(), await bar())
```

### 还不服气？

假设你对我喜欢漂亮的代码和易于维护不感兴趣。相反，你需要确凿的事实。它们存在吗？

没错。

当将 promise chaining 合并到他们的代码中时，开发人员会在每次有`then()`调用时创建新的函数。这本身会占用更多的内存，但是这些函数总是在另一个上下文中。因此，这些函数变成了闭包，这使得垃圾收集变得更加困难。此外，这些函数通常是匿名函数，会污染堆栈跟踪。

既然我们正在讨论堆栈跟踪:我应该提到有一个可靠的[提议](https://docs.google.com/document/d/13Sy_kBIJGP0XT34V1CV3nkWya4TwYx9L3Yv45LdGB6Q/edit)来为异步函数实现**更好的堆栈跟踪**。这太棒了，有趣的是…

*只要开发者坚持只使用异步函数和异步生成器，而不是手工编写 Promise 代码*

…如果使用承诺链，将不起作用。所以这又是一个总是使用异步/等待的理由！

### 如何迁移

首先(现在应该很明显了):开始使用异步函数，停止使用承诺链。

其次，您可能会发现 Visual Studio 代码对此非常方便:

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

### 结论

*   Async/await 已经得到了广泛的支持。除非你需要支持 IE 你很好。
*   Async/await 可读性更强，易于维护。
*   只使用 async/await 也有技术上的原因。
*   有了 Visual Studio 代码和其他 ide，你可以很容易地移植现有的 promise chained 代码！