# 实验 Node.js:测试新的性能钩子

> 原文：<https://blog.logrocket.com/experimental-node-js-testing-the-new-performance-hooks-31fcdd2a747e/>

对于任何希望部署到生产环境中的应用程序来说，性能监控都是一个非常重要的主题。一旦您开始发现性能问题，就不应该开始考虑性能监控，相反，它应该是您开发过程的一部分，以便在生产中出现问题之前检测出可能的问题。

也就是说，考虑到 Node 代码的异步特性，使用常规工具对其进行分析可能具有挑战性。尤其是因为部分时间可能在代码之外，而在 EventLoop 本身之内。这就是为什么如果这种语言为您提供了描述它的实际工具，您应该认真考虑它们。

在本文中，我将介绍如何使用性能计时 API 的实际例子，该 API 目前(在撰写 11.13 版本时)处于实验模式。意思是，欢迎您使用它，但是请记住，它们可能会在没有太多警告的情况下，将我们即将看到的方法的实际契约从一个版本更改为另一个版本。

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)

### 挂钩和性能指标

但是在我们开始之前，我想快速浏览一下这两个概念，因为它们不是同一个模块的一部分，尽管一起使用它们效果很好。

一方面，我们有**性能计时 API** ，它允许开发人员精确测量事件循环中用户代码的效果，以及它如何影响应用程序的性能。基本上，如果你想认真地衡量你的应用程序的性能，你会想在某个时候阅读关于 [**【性能钩子】**](https://nodejs.org/api/perf_hooks.html) 的内容。

但是另一方面，有另一个不相关的模块叫做[**【async _ hooks】**](https://nodejs.org/api/async_hooks.html)，它允许你借用 V8 的异步机制，并添加钩子(基本上是函数调用)，这些钩子可以在异步资源的生命周期之前、开始、之后和结束时执行(换句话说，一个回调函数)。

简而言之，使用这个模块，你可以在回调函数生命的不同阶段执行操作(例如，在调用之前，在垃圾收集结束时)。

将这两者混合在一起的目的是能够从异步操作中收集指标，而不必自己手动修改代码。有了这两个模块，我将向您展示如何通过检查事件循环来检查一些操作的内部工作。正如您可能猜到的，这将允许您打开和关闭这种能力，而对您的项目几乎没有影响。现在，让我们来看一些例子。

### 你能用钩子做什么？

当谈到测量时间时，这两个模块都可以被认为是非常低级的，这意味着尽管它们一开始可能有点难以理解，但是一旦你理解了，你就可以真正地进入并测量你代码的每个角落。兔子洞有多深由你来定义。

让我展示给你看。

### 测量需要依赖项所需的时间

首先，让我们看看性能挂钩 API 本身，它已经非常强大，允许您收集一些非常有趣的数据。

例如，过度依赖依赖项的项目可能出现的一个问题是启动时间长，这是由于在依赖项加载期间花费了大量时间。

通过添加以下内容，您可以大致了解瓶颈在哪里:

```
let start = (new Date()).getTime()
//your requires go here…
let end = (new Date()).getTime()
console.log(end — start, “ ms”)
```

在这里，您会发现模块加载需要多长时间，但是如果您只有几个直接依赖项呢？有时候，一个依赖项可以反过来依赖于另外十个依赖项，其中每一个都需要另外十个依赖项。所以你实际上是相当依赖的，通过对前面的代码做这样肤浅的分析，你不能真正确定你的问题到底出在哪里。

相反，如果我们在性能测量 API 的帮助下专注于我们的分析，我们可以覆盖 *require* 函数，并在整个启动过程中捕获每一个需求。让我展示给你看:

```
'use strict';
const {
  performance,
  PerformanceObserver
} = require('perf_hooks');
const mod = require('module');

// Monkey patch the require function
mod.Module.prototype.require = performance.timerify(mod.Module.prototype.require);
require = performance.timerify(require);

// Activate the observer
const obs = new PerformanceObserver((list) => {
  const entries = list.getEntries();
  entries.forEach((entry) => {
    console.log(`require('${entry[0]}')`, entry.duration);
  });
  obs.disconnect();
});
obs.observe({ entryTypes: ['function'], buffered: true });

require(‘getpass’); 
```

执行上述代码会导致:

```
require(‘getpass’) 2.443011
require(‘getpass’) 2.432565
require(‘tty’) 0.003704
require(‘fs’) 0.003543
require(‘assert-plus’) 0.886344
require(‘assert’) 0.003409
require(‘stream’) 0.001784
require(‘util’) 0.001229
```

这里，我们使用了来自 *perf_hooks* 模块的两个实体。

### 表演

这个对象提供了 *timerify* 方法(当然还有其他方法)。这种方法允许您将一个函数包装在另一个函数周围，后者将提供原始函数的时间度量。这使我们能够从*请求得到时间数据，*我们用 *timerify 包装它(和它的原型)。*

### PerformanceObserver 类

该类允许您创建一个观察器实例，并在性能时间线上有新条目时做出反应。把时间轴想象成一个栈，你只能在它的末尾添加数据，也就是说你添加了一个条目。

因此，观察器允许您设置一个处理函数，一旦条目被推入堆栈，这个函数就会被调用。倒数第二行设置观察者的目标:类型等于“function”的条目，并确保行为被缓冲。换句话说，一旦所有的*请求*调用结束，我们的回调将被调用。

这最后一位不是必需的，您可以很好地按如下方式构建观察器:

```
const obs = new PerformanceObserver((list) => {
  const entry = list.getEntries()[0]
  console.log(`require('${entry[0]}')`, entry.duration);
});
obs.observe({ entryTypes: ['function'], buffered: false}); 
```

具有类似的输出:

```
require(‘tty’) 0.003969
require(‘fs’) 0.004216
require(‘assert’) 0.003542
require(‘stream’) 0.00289
require(‘util’) 0.002521
require(‘assert-plus’) 1.069765
require(‘getpass’) 4.109317
require(‘getpass’) 4.16102
```

这里隐藏的神奇之处在于，条目不是由您直接添加的，而是由包装的 *require* 函数添加的。这就是 *timerify* 的工作方式，返回的函数确保将类型为*‘函数’*的条目添加到时间线中，我们的观察者为我们选取它们。

现在，你可以想象，如果你正在检查像 *ExpressJS* 或 *request，*这样的需求链，这个列表会更长。

### 测量您自己的代码

现在，我想向您展示如何使用同一个观察器，但是在您自己的代码中，为此，我们将不得不手动触发测量(稍后我们将看到如何使用异步钩子自动完成，不要担心)。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

对于测量，我们将创建标记，这些标记只是时间线中的相关点，然后，我们将测量它们之间的时间，以计算我们的延迟。

具体来说，下面的代码将通过获取一些最流行的搜索引擎(Google，Yahoo！，冰和 DuckDuck 去)。一旦所有四个请求都完成，我们将简单地打印出一个单词来通知用户。对于这个例子，我们关心的是时间，而不是内容。

我们衡量代码性能的想法是，计算每个请求需要多长时间，为此，我们将在请求完成之前创建一个标记，在请求结束时创建另一个标记，最后，我们将测量差异。

代码看起来会像这样:

```
'use strict';
const {
  performance,
  PerformanceObserver
} = require('perf_hooks');
const request = require("request")

function queryEngines(done) {
    const urls = [
        "http://www.google.com",
        "http://yahoo.com",
        "http://bing.com",
        "http://duckduckgo.com"
    ]

    let results = []

    urls.forEach( (url) => {
        performance.mark(url + "-init") //initial mark for the current URL

        request(url, (err, cnt) => {
            performance.mark(url + "-end") //final mark for the same URL
            performance.measure(url, url + "-init", url + "-end") //calculate the time difference between the start and end 

            results.push(cnt)
            if(results.length === urls.length) {
                return done(results)
            }
        })
    })
}

// Activate the observer
const obs = new PerformanceObserver((list) => {
  const entry = list.getEntries()[0]
  console.log(`Time for ('${entry.name}')`, entry.duration);
});
obs.observe({ entryTypes: ['measure'], buffered: false});  //we want to react to full measurements and not individual marks

queryEngines( (pages) => {
    console.log("Done!")
}) 
```

输出如下所示:

```
Time for (‘http://www.google.com’) 155.920343
Time for (‘http://duckduckgo.com’) 435.809226
Time for (‘http://bing.com’) 679.744093
Time for (‘http://yahoo.com’) 3194.186238
Done!
```

请注意，出于某种原因，雅虎！需要太长时间才能返回。如果你看一下上面的代码，对于每个 URL，我们在请求之前和它返回的时候设置一个关键点(mark ), measure 方法简单地计算时间差并发送一个触发器给 observer，observer 然后执行它的回调并打印出数据。

### 回车，异步挂钩

根据我们代码的性质，挂钩异步事件的能力将会派上用场。让我们先来看看我们的代码:

```
'use strict';
const {
  performance,
  PerformanceObserver
} = require('perf_hooks');
const async_hooks = require("async_hooks")

const request = require("request")

const map = new Map()

//Creating the async hook here to piggyback on async calls
const hook = async_hooks.createHook({
  init(id, type, triggerID, resource) {
    if (type == 'GETADDRINFOREQWRAP') {
        if(!firstMark) firstMark = resource.hostname + "-Init"
          performance.mark(resource.hostname + '-Init');
      map.set(id, resource.hostname)
    }
  },
  destroy(id) {

    if (map.has(id)) {
          let host = map.get(id)
      map.delete(id);
      performance.mark(host +"-After")
      performance.measure(host,
                          host + "-Init",
                          host + "-After")
    }
  }
});
hook.enable();

//Original code starts here
function queryEngines(done) {
    const urls = [
        "http://www.google.com",
        "http://yahoo.com",
        "http://bing.com",
        "http://duckduckgo.com"
    ]

    let results = []

    urls.forEach( (url) => {
        request(url, (err, cnt) => {
            results.push(cnt)
            if(results.length === urls.length) {
                return done(results)
            }
        })
    })
}

//The performance observer is not changed
const obs = new PerformanceObserver((list) => {
  const entry = list.getEntries()[0]
  console.log(`Time for ('${entry.name}')`, entry.duration);
});
obs.observe({ entryTypes: ['measure'], buffered: false});

queryEngines( (pages) => {
    console.log("Done!")
}) 
```

该代码的输出是:

```
Time for (‘yahoo.com’) 10.285394
Time for (‘www.google.com’) 19.315204
Time for (‘bing.com’) 16.543073
Time for (‘duckduckgo.com’) 20.414387
Time for (‘www.bing.com’) 14.802698
Time for (‘yahoo.com’) 4.843614
Time for (‘www.yahoo.com’) 58.130851
Done!
```

这里有几件事要注意。让我们从头开始。

### 创建挂钩

createHook 方法允许开发人员定义一组要执行的回调，这取决于它被分配到的方法的名称。正如我已经提到的，这里有四个可能的名称:init、before、after 和 destroy，它们指的是异步资源生命周期中的不同步骤。

### 定义回调

因为我们实际上不需要对发生的事情进行太多的控制，所以我只是简单地定义了要调用的第一个和最后一个回调。这是希望我能够复制前一个例子的行为。如您所见，结果并不完全相同，我稍后会解释原因。

这里重要的一点是，您会注意到我只为“GETADDRINFOREQWRAP”类型的异步操作的 *init* 事件发送了一个标记，这些事件与 HTTP 请求相关。 *init* 方法的参数是:

*   **Id** :给予特定异步资源的 Id
*   **类型**:预定义的类型列表。你可以看看文档中的完整列表，尽管遗憾的是官方文档并没有真正解释它们
*   **triggerID** :分配给创建这个特定异步资源的函数的 ID。基本上，父代的 ID，您可以沿着 triggerID 在层次结构中一路向上找到第一个父代
*   **资源**:与资源相关的对象的额外信息。特别是，您可以看到我们如何使用该对象来访问主机名

我还在 *destroy* 事件上发送标记，只要相关的异步动作是感兴趣的(这就是地图的作用)。在这个事件中，我不仅发送标记，还发送单个 URL 的整个过程的度量。

### 结果呢

尽管代码背后的逻辑应该是可靠的，但是我们得到的结果并不完全是我们所期望的，不是吗？！两个主要区别是:

1.  持续时间的数字加起来比不上我们之前得到的，甚至都不接近
2.  测量值比预期的多，因为一些 URL 重复

持续时间的差异是由于我们不能明确地附加到我们想要的功能上。也许通过更多的修改和调试，您可以获得更好的结果，但是在每个请求中会涉及到大量的异步资源。使用当前版本的代码，我们能够理解请求何时开始，但不能准确理解它何时结束，只能理解它的一部分何时结束。所以我们得到的持续时间是部分的。

话虽如此，我们的发现仍然非常有用，因为下一个区别。

如您所见，有两个请求发送给 Bing，三个请求发送给 Yahoo！，如果你仔细想想，即使最新结果中的持续时间没有加起来，请求的数量似乎解释了为什么雅虎！是之前花时间最长的一个。但是为什么我们会得到不同的结果呢？

为了调试钩子，你不能只使用 *console.log* ，你不能使用任何异步函数，否则，日志记录的行为实际上会触发另一个钩子。因此，推荐的方法是使用同步版本的 writeFile 方法写入文件。

因此，您可以像这样重写 init 挂钩:

```
init(id, type, triggerID, resource) {
        let meta = {
            event: "[init]",
            type, id, triggerID
        }

        fs.writeFileSync("./perf.log", JSON.stringify(meta) + "\n\t", {flag: "a"} )
        for(let p in resource) {
        if(typeof(resource[p]) != "function") {
          fs.writeFileSync("./perf.log", "[resource ] " + p + ":" + util.inspect(resource[p]) + "\n\t", {flag: "a"} )
        }

        }
    if (type == 'GETADDRINFOREQWRAP') {
          performance.mark(resource.hostname + '-Init');
      map.set(id, resource.hostname)
    }
  },
```

在这段代码中，我不仅记录了基本数据，而且还检查了资源对象，试图根据动作类型确定哪些信息是可访问的。特别是，您会发现许多 TickObjects 引用了请求的实际响应对象，并且在其中，您会发现重定向请求。特别是对雅虎！对于 Bing 来说，它有多个请求。

换句话说，通过挂钩“GETADDRINFOREQWRAP”类型的操作，我们不仅仅检查我们手动执行的请求，还检查由于 HTTP 协议的机制而发生的以下请求。

因此，尽管获得相同的持续时间结果有点困难，但通过使用异步挂钩，我们可以深入了解我们编写的代码的内部工作方式。

# 结论

在 Node 的官方文档中，性能挂钩和异步挂钩仍然被标记为实验性的，所以如果您开始使用这些模块，请考虑这一点。没有人说这些界面会改变，但是也没有真正的保证它们会保持现在的样子。

也就是说，摆弄这些特性并不是浪费时间，因为你可以瞥见不久的将来会发生什么，也可以找到 bug 并通过报告它们来帮助项目(或者见鬼！甚至修复它们)。

希望这篇文章能帮助你理解一些复杂的文档，如果你希望在自己的代码中使用它，也能帮助你理解它。

请在评论中告诉我你是否使用过这些模块，或者你是否能想到其他方法来使用它们以获得更多的见解！

感谢你的阅读，下次再见！

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.