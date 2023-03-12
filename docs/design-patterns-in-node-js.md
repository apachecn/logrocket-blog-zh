# Node.js 中的设计模式:实用指南

> 原文：<https://blog.logrocket.com/design-patterns-in-node-js/>

设计模式是任何软件开发人员日常工作的一部分，不管他们是否意识到这一点。

在本文中，我们将看看如何在野外识别这些模式，并看看如何在自己的项目中使用它们。

## 什么是设计模式？

简而言之，设计模式是一种让你组织解决方案代码的方式，这种方式可以让你获得某种好处。比如更快的开发速度，代码重用性等等。

所有模式都很容易适应 OOP 范例。尽管考虑到 JavaScript 的灵活性，您也可以在非 OOP 项目中实现这些概念。

当谈到设计模式时，有太多的模式要在一篇文章中涵盖，事实上，已经有专门关于这个主题的书籍，并且每年都有新的模式产生，使得它们的列表不完整。

一个非常常见的模式分类是在 [GoF 的书](https://en.wikipedia.org/wiki/Design_Patterns)(四人组的书)中使用的，但是因为我将只回顾其中的一小部分，我将忽略这个分类，而只是给你一个你现在就可以看到并开始在你的代码中使用的模式列表。

## 立即调用函数表达式(IIFE)

我要展示的第一个模式允许您同时定义和调用一个函数。由于 JavaScript 作用域的工作方式，使用 IIFEs 可以很好地模拟类中的私有属性。事实上，这种特殊的模式有时被用作其他更复杂的需求的一部分。我们一会儿就知道怎么做了。

### 生活是什么样子的？

但是在我们深入研究用例及其背后的机制之前，让我快速向您展示一下它到底是什么样子的:

```
(function() {
   var x = 20;
   var y = 20;
   var answer = x + y;
   console.log(answer);
})();

```

通过将上述代码粘贴到 Node.js REPL 甚至浏览器的控制台中，您会立即得到结果，因为顾名思义，您在定义函数时就在执行它。

IIFE 的模板由一个匿名函数声明组成，在一组括号内(将定义转换为函数表达式，也称为赋值),然后在它的末尾是一组调用括号。像这样:

```
(function(/*received parameters*/) {
//your code here
})(/*parameters*/)
```

### 用例

虽然这听起来很疯狂，但实际上有一些好处和使用案例，使用生命可能是一件好事，例如:

### 模拟静态变量

还记得[静态变量](https://en.wikipedia.org/wiki/Static_variable)吗？从其他语言如 C 或 C#中。如果您不熟悉它们，静态变量会在您第一次使用时被初始化，然后它会取您最后一次设置的值。好处是，如果你在一个函数中定义了一个静态变量，无论你调用它多少次，这个变量对这个函数的所有实例都是通用的，所以它极大地简化了如下情况:

```
function autoIncrement() {
    static let number = 0
    number++
    return number
}

```

每次我们调用上面的函数时，它都会返回一个新的数字(当然，假设静态关键字在 JS 中是可用的)。我们可以用 JS 中的生成器做到这一点，这是真的，但假设我们无法访问它们，您可以像这样模拟一个静态变量:

```
let autoIncrement = (function() {
    let number = 0

    return function () {
     number++
     return number
    }
})()
```

你在那里看到的，是生活中所有封闭的魔力。纯粹的魔法。你基本上是在返回一个新的函数，这个函数将被分配给`autoIncrement`变量(由于生命的实际执行)。通过 JS 的作用域机制，您的函数将始终可以访问 number 变量(就像它是一个全局变量一样)。

### 模拟私有变量

正如您可能已经知道的(我猜也可能不知道)，ES6 类将每个成员都视为公共的，这意味着没有私有属性或方法。这是不可能的，但是感谢生命，如果你想的话，你可以模拟它。

```
const autoIncrementer = (function() {
  let value = 0;

  return {
    incr() {
        value++
    },

    get value() {
        return value
    }
  };
})();
> autoIncrementer.incr()
undefined
> autoIncrementer.incr()
undefined
> autoIncrementer.value
2
> autoIncrementer.value = 3
3
> autoIncrementer.value
2

```

上面的代码向您展示了一种方法。尽管您没有明确定义一个可以在以后实例化的类，但是请注意，您是在定义一个结构，一组属性和方法，它们可以利用您正在创建的对象所共有的变量，但是这些变量不能从外部访问(如失败的赋值所示)。

## 工厂方法模式

特别是这个，是我最喜欢的模式之一，因为它是一个工具，你可以实现它来清理你的代码。

本质上，工厂方法允许您将创建对象的逻辑(即创建哪个对象以及为什么创建)集中在一个地方。这允许你忘记那部分，专注于简单地请求你需要的对象，然后使用它。

这可能看起来是一个小好处，但是请耐心听我说，这是有意义的，相信我。

### 工厂方法模式是什么样子的？

如果先看它的用法，然后再看它的实现，这个特定的模式会更容易理解。

这里有一个例子:

```
( _ => {

    let factory = new MyEmployeeFactory()

    let types = ["fulltime", "parttime", "contractor"]
    let employees = [];
    for(let i = 0; i < 100; i++) {
     employees.push(factory.createEmployee({type: types[Math.floor( (Math.random(2) * 2) )]})    )}

    //....
    employees.forEach( e => {
     console.log(e.speak())
    })

})()

```

从上面的代码中得到的关键信息是，您正在将对象添加到同一个数组中，所有这些对象共享同一个接口(从某种意义上说，它们拥有相同的方法集)，但是您并不真正需要关心创建哪个对象以及何时创建。

您现在可以看看实际的实现，正如您所看到的，有很多东西要看，但很简单:

```
class Employee {

    speak() {
     return "Hi, I'm a " + this.type + " employee"
    }

}

class FullTimeEmployee extends Employee{
    constructor(data) {
     super()
     this.type = "full time"
     //....
    }
}

class PartTimeEmployee extends Employee{
    constructor(data) {
     super()
     this.type = "part time"
     //....
    }
}

class ContractorEmployee extends Employee{
    constructor(data) {
     super()
     this.type = "contractor"
     //....
    }
}

class MyEmployeeFactory {

    createEmployee(data) {
     if(data.type == 'fulltime') return new FullTimeEmployee(data)
     if(data.type == 'parttime') return new PartTimeEmployee(data)
     if(data.type == 'contractor') return new ContractorEmployee(data)
    }
}

```

### 用例

前面的代码已经展示了一个通用的用例，但是如果我们想更具体一些，我喜欢使用这个模式的一个特殊用例是处理错误对象的创建。

假设有一个大约有 10 个端点的 Express 应用程序，其中每个端点都需要根据用户输入返回两到三个错误。我们正在谈论 30 个像下面这样的句子:

```
if(err) {
  res.json({error: true, message: “Error message here”})
}

```

现在，这不会是一个问题，除非，当然，直到下一次你不得不突然添加一个新的属性到错误对象。现在你必须检查你的整个项目，修改所有 30 个地方。这可以通过将错误对象的定义转移到一个类中来解决。这很好，除非，当然，你有不止一个错误对象，同样，你必须根据一些只有你知道的逻辑来决定实例化哪个对象。明白我想说什么了吗？

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

如果您要集中创建错误对象的逻辑，那么您在整个代码中所要做的事情应该是这样的:

```
if(err) {
  res.json(ErrorFactory.getError(err))
}

```

就这样，你完成了，你再也不用改变那条线了。

## 单一模式

这是另一个老歌，但好东西。请注意，这是一个非常简单的模式，但是它可以帮助您跟踪您正在实例化的类的实例数量。实际上，它可以帮助你始终保持这个数字为 1。主要地，单例模式允许你实例化一个对象一次，然后在每次你需要它的时候使用它，而不是创建一个新的对象，而不必跟踪对它的引用，无论是全局地还是只是把它作为一个依赖项到处传递。

### 单例模式是什么样的？

通常，其他语言使用单个静态属性来实现这种模式，一旦实例存在，就存储在这个静态属性中。这里的问题是，正如我之前提到的，我们无法访问 JS 中的静态变量。所以我们可以用两种方式实现它，一种是用类代替类。

另一种方法是使用 ES6 模块，让我们的单例类使用一个局部全局变量来存储我们的实例。通过这样做，类本身被导出到模块之外，但是全局变量仍然在模块的本地。

我知道，但相信我，这听起来比看起来要复杂得多:

```
let instance = null

class SingletonClass {

    constructor() {
     this.value = Math.random(100)
    }

    printValue() {
     console.log(this.value)
    }

    static getInstance() {
     if(!instance) {
         instance = new SingletonClass()
     }

     return instance
    }
}

module.exports = SingletonClass

```

你可以这样使用它:
const Singleton = require("。/singleton”)

```
const obj = Singleton.getInstance()
const obj2 = Singleton.getInstance()

obj.printValue()
obj2.printValue()

console.log("Equals:: ", obj === obj2)

```

输出结果当然是:

```
0.5035326348000628
0.5035326348000628
Equals::  true

```

确认我们确实只实例化对象一次，并返回现有的实例。

### 用例

当试图决定你是否需要一个类似单例的实现时，你需要考虑一些事情:你真正需要多少个类的实例？如果答案是 2 或更多，那么这不是你的模式。

但是，当必须处理数据库连接时，您可能需要考虑它。

考虑一下，一旦连接到数据库，在整个代码中保持连接并使其可访问可能是个好主意。请注意，这可以用很多不同的方法来解决，是的，但是这个模式确实是其中之一。

使用上面的例子，我们可以把它推断成这样:

```
const driver = require("...")

let instance = null

class DBClass {

    constructor(props) {
     this.properties = props
     this._conn = null
    }

    connect() {
     this._conn = driver.connect(this.props)
    }

    get conn() {
     return this._conn
    }

    static getInstance() {
     if(!instance) {
         instance = new DBClass()
     }

     return instance
    }
}

module.exports = DBClass

```

现在，如果使用 getInstance 方法，您肯定无论您在哪里，都将返回唯一的活动连接(如果有的话)。

## 观察者模式

这是一个非常有趣的模式，从某种意义上来说，它允许您通过对输入做出反应来响应输入，而不是主动检查输入是否被提供。换句话说，使用这种模式，您可以指定等待哪种类型的输入，并被动地等待，直到输入被提供，以便执行您的代码。如果你愿意，这是一种一劳永逸的交易。

在这里，观察者就是你的对象，它们知道自己想要接收的输入类型和要做出的反应，这意味着“观察”另一个对象并等待它与它们交流。

另一方面，可观察值将让观察者知道新的输入何时可用，因此他们可以对其做出反应，如果适用的话。如果这听起来很熟悉，那是因为它是，Node 中处理事件的任何东西都在实现这种模式。

### 观察者模式是什么样的？

你自己写过 HTTP 服务器吗？大概是这样的:

```
const http = require('http');

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Your own server here');
});

server.on('error', err => {
    console.log(“Error:: “, err)
})

server.listen(3000, '127.0.0.1', () => {
  console.log('Server up and running');
});

```

在那里，隐藏在上面的代码中，您看到的是野外的观察者模式。至少是它的一个实现。您的服务器对象将充当可观察对象，而您的回调函数是实际的观察者。这里类似事件的接口(见加粗的代码)、on 方法和事件名称可能会使视图有点模糊，但请考虑下面的实现:

```
class Observable {

    constructor() {
     this.observers = {}
    }

    on(input, observer) {
     if(!this.observers[input]) this.observers[input] = []
     this.observers[input].push(observer)
    }

    triggerInput(input, params) {
     this.observers[input].forEach( o => {
         o.apply(null, params)    
     })
    }
}

class Server extends Observable {

    constructor() {
     super()
    }

    triggerError() {
     let errorObj = {
         errorCode: 500,
         message: 'Port already in use'
     }
     this.triggerInput('error', [errorObj])
    }
}

```

现在，您可以再次以完全相同的方式设置同一个观察者:

```
server.on('error', err => {
    console.log(“Error:: “, err)
})

```

如果您调用 triggerError 方法(该方法向您展示了如何让您的观察者知道有新的输入)，您将得到完全相同的输出:

```
Error:: { errorCode: 500, message: 'Port already in use' }
```

> 如果您考虑在 Node.js 中使用这种模式，请先看看 [EventEmitter](https://nodejs.org/api/events.html) 对象，因为它是 Node.js 自己对这种模式的实现，可能会节省您一些时间。

### 用例

正如您可能已经猜到的，这种模式非常适合处理异步调用，因为从外部请求获得响应可以被认为是一个新的输入。如果我们的项目中没有不断涌入的异步代码，Node.js 中还有什么呢？因此，下次当您不得不处理异步场景时，请考虑一下这种模式。

如您所见，这种模式的另一个广泛应用是触发特定事件。这种模式可以在任何倾向于异步触发事件(比如错误或状态更新)的模块中找到。一些例子是 [HTTP 模块](https://nodejs.org/api/http.html) 、任何数据库驱动程序，甚至是 [socket.io、](https://socket.io/) ，它允许你在你自己的代码之外触发的特定事件上设置观察者。

## 责任链

责任链模式是 Node.js 世界中的许多用户已经使用过的模式，甚至没有意识到这一点。

它包括以一种允许您将请求的发送者与能够实现它的对象分离的方式来组织您的代码。换句话说，让对象 A 发送请求 R，你可能有三个不同的接收对象 R1、R2 和 R3，A 怎么知道应该把 R 发送给哪一个呢？A 应该关心这个吗？

最后一个问题的答案是:不，不应该。因此，相反，如果 A 不应该关心谁来处理这个请求，我们为什么不让 R1、R2 和 R3 自己决定呢？

这就是责任链发挥作用的地方，我们正在创建一个接收对象链，它将尝试满足请求，如果不能，它们将只传递它。听起来熟悉吗？

### 责任链是什么样子的？

这是这种模式的一个非常基本的实现，正如你在底部看到的，我们有四个可能的值(或请求)需要处理，但是我们不关心谁来处理它们，我们只需要，至少，一个函数来使用它们，因此我们只是将它发送到链中，让每个人决定是应该使用它还是忽略它。

```
function processRequest(r, chain) {

    let lastResult = null
    let i = 0
    do {
     lastResult = chain[i](r)
     i++
    } while(lastResult != null && i < chain.length)
    if(lastResult != null) {
     console.log("Error: request could not be fulfilled")
    }
}

let chain = [
    function (r) {
     if(typeof r == 'number') {
         console.log("It's a number: ", r)
         return null
     }
     return r
    },
    function (r) {
     if(typeof r == 'string') {
         console.log("It's a string: ", r)
         return null
     }
     return r
    },
    function (r) {
     if(Array.isArray(r)) {
         console.log("It's an array of length: ", r.length)
         return null
     }
     return r
    }
]

processRequest(1, chain)
processRequest([1,2,3], chain)
processRequest('[1,2,3]', chain)
processRequest({}, chain)

```

输出是:

```
It's a number:  1
It's an array of length:  3
It's a string:  [1,2,3]
Error: request could not be fulfilled

```

### 用例

在我们的生态系统中，这种模式最明显的例子是 ExpressJS 的[中间件。使用这种模式，您实际上建立了一个函数链(中间件),它评估请求对象并决定对其进行操作或忽略它。您可以将该模式视为上述示例的异步版本，其中不是检查函数是否返回值，而是检查将什么值传递给它们调用的下一个回调。](http://expressjs.com/en/guide/using-middleware.html)

```
var app = express();

app.use(function (req, res, next) {
  console.log('Time:', Date.now());
  next(); //call the next function on the chain
});

```

中间件是这种模式的一个特殊实现，因为不是只有链中的一个成员完成请求，而是所有成员都可以完成请求。然而，其背后的基本原理是相同的。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)监视器出现故障，生产中节点请求变慢

部署节点设计模式可以为确保应用程序的性能和可靠性带来巨大的好处。如果您对确保对节点后端或第三方服务的网络请求成功感兴趣，请尝试 LogRocket 。[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://logrocket.com/signup/)[https://logrocket.com/signup/](https://logrocket.com/signup/)

LogRocket 就像是网络应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求以及日志 Redux、NgRx。和 Vuex 动作/状态。[开始免费监控](https://logrocket.com/signup/)。

## 最后的想法

这些只是你每天可能会遇到的一些模式，而你却没有意识到。我鼓励你去研究其余的，即使你没有找到直接的使用案例，现在我已经向你展示了它们中的一些在野外的样子，你可能会自己开始看到它们！希望这篇文章对这个主题有所启发，并帮助你比以往更快地改进你的编码 foo。下次再见！