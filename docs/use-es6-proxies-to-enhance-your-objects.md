# 使用 ES6 代理增强对象的 3 种方法

> 原文：<https://blog.logrocket.com/use-es6-proxies-to-enhance-your-objects/>

编程中我最喜欢的一个方面是元编程，它指的是使用语言本身来改变语言的基本构件的能力。开发人员使用这种技术来增强语言，甚至在某些情况下，创建新的定制语言，称为[领域特定语言](https://en.wikipedia.org/wiki/Domain-specific_language)(简称 DSL)。

许多语言已经提供了深层次的元编程，但是 JavaScript 缺少一些关键的方面。

是的，这是真的，JavaScript 足够灵活，它允许您对语言进行相当大的扩展，考虑如何在运行时向对象添加属性，或者如何通过将不同的函数作为参数传递给它来轻松增强函数的行为。但尽管如此，仍然有一些限制，新的代理现在允许我们超越。

在这篇文章中，我想介绍使用代理可以做的三件事，这将特别增强您的对象。希望在本文结束时，您能够扩展我的代码，并可能将它应用于您自己的需求！

## 代理是如何工作的？快速介绍

代理基本上将您的对象或函数包装在一组陷阱周围，一旦这些陷阱被触发，您的代码就会被执行。简单吧？

我们可以玩的陷阱有:

| **陷阱** | **描述** |
| --- | --- |
| getPrototypeOf | 当您在自己的对象上调用同名方法时触发。 |
| setPrototypeOf | 和以前一样，但是这个方法不同。 |
| 可扩展 | 当我们试图理解一个对象是否可以被扩展时触发(例如，在运行时向它添加新的属性)。 |
| 预防高血压 | 和以前一样，但是对于这个特殊的方法(顺便说一下，它忽略了你在运行时添加到对象中的任何新属性)。 |
| getOwnPropertyDescriptor | 该方法通常返回给定对象属性的描述符对象。当使用方法时，会触发这个陷阱。 |
| 定义属性 | 调用此方法时执行。 |
| 有 | 当我们使用运算符中的**时触发(就像我们使用`if(``'``value``'` `in array)`时一样)。这非常有趣，因为您不局限于为数组添加这个陷阱，您还可以扩展其他对象。** |
| 得到 | 非常简单，当您试图访问一个属性值时触发(即`yourObject.prop`)。 |
| 设置 | 与上一个相同，但是在属性上设置值时触发。 |
| 删除属性 | 基本上，当你使用`delete`操作符时，一个陷阱被触发。 |
| 自有密钥 | 在对象上使用`getOwnPropertyNames`和`getOwnPropertySymbols`方法时触发。 |
| 应用 | 调用函数时触发。我们会非常关注这件事，你就等着瞧吧。 |
| 建造 | 用`new`操作符实例化一个新对象时触发。 |

这些都是标准的陷阱，非常欢迎你查看 Mozilla 的 Web Docs，了解每一个陷阱的更多细节，因为我将在本文中重点讨论其中的一个子集。

也就是说，创建新代理的方式，或者换句话说，用代理包装对象或函数调用的方式，看起来像这样:

```
let myString = new String("hi there!")
let myProxiedVar = new Proxy(myString, {
  has: function(target, key) {
    return target.indexOf(key) != -1;
  }
})
console.log("i" in myString)
// false
console.log("i" in myProxiedVar)
//true
```

这是代理的基础，我马上会展示更复杂的例子，但它们都基于相同的语法。

## 代理 vs 反映

但是在我们开始看例子之前，我想快速回答这个问题，因为这是一个经常被问到的问题。有了 [ES6](https://blog.logrocket.com/how-python-can-help-you-learn-es6/) ，我们不仅仅得到了代理，我们还得到了`[Reflect](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect)` [对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect)，乍一看，它们做的完全一样，不是吗？

主要的困惑是因为大多数文档都指出`Reflect`有和我们上面看到的代理处理程序相同的方法(即陷阱)。尽管这是真的，但这里有 1:1 的关系，对象的行为和它的方法与全局对象`Object`更相似。

例如，下面的代码:

```
const object1 = {
  x: 1,
  y: 2
};

console.log(Reflect.get(object1, 'x'));
```

将返回 1，就像你直接尝试访问属性一样。因此，不用改变预期的行为，您可以用不同的(在某些情况下，更动态的)语法来执行它。

## 增强#1:动态属性访问

现在让我们看一些例子。首先，我想向您展示如何为检索属性值的操作提供额外的功能。

我的意思是，假设你有一个对象，比如:

```
class User {
  constructor(fname, lname) {
    this.firstname =  fname
    this.lastname = lname
  }
}
```

您可以很容易地获得名或姓，但不能一下子就简单地请求全名。或者，如果您想获得全部大写的名称，您必须链接方法调用。这绝不是一个问题，在 JavaScript 中就是这样做的:

```
let u = new User("fernando", "doglio")
console.log(u.firstname + " " + u.lastname)
//would yield: fernando doglio
console.log(u.firstname.toUpperCase())
//would yield: FERNANDO
```

但是使用代理，有一种方法可以让你的代码更具声明性。想一想，如果您可以让您的对象支持如下语句会怎么样:

```
let u = new User("fernando", "doglio")
console.log(u.firstnameAndlastname)
//would yield: fernando doglio
console.log(u.firstnameInUpperCase)
//would yield: FERNANDO
```

当然，我们的想法是将这种通用行为添加到任何类型的对象中，避免手动创建额外的属性和污染对象的名称空间。

这就是代理发挥作用的地方，如果我们包装我们的对象并为获取属性值的动作设置一个陷阱，我们可以截取属性的名称并解释它以获得想要的行为。

下面的代码可以让我们做到这一点:

```
function EnhanceGet(obj) {
  return new Proxy(obj, {
    get(target, prop, receiver) {

      if(target.hasOwnProperty(prop)) {
          return target[prop]
      }
      let regExp = /([a-z0-9]+)InUpperCase/gi
      let propMatched = regExp.exec(prop)

      if(propMatched) {
        return target[propMatched[1]].toUpperCase()
      } 

      let ANDRegExp = /([a-z0-9]+)And([a-z0-9]+)/gi
      let propsMatched = ANDRegExp.exec(prop)
      if(propsMatched) {
          return [target[propsMatched[1]], target[propsMatched[2]]].join(" ")
      }
      return "not found"
     }
  });
}
```

我们基本上为`get`陷阱设置了一个代理，并使用正则表达式来解析属性名。尽管我们首先检查这个名字是否确实符合一个真实的属性，如果符合，我们就返回它。然后，我们检查正则表达式的匹配，当然，捕获实际的名称，以便从对象中获取值，然后进一步处理它。

现在，您可以将该代理用于您自己的任何对象，并且属性 getter 将得到增强！

## 增强#2:对无效属性名的自定义错误处理

接下来，我们有了另一个小而有趣的增强。每当你试图访问一个对象上不存在的属性时，你不会真的得到一个错误，JavaScript 就是这样允许的。你得到的只是返回的`undefined`而不是它的值。

如果我们想定制返回值，或者甚至抛出一个异常，而不是获得该行为，因为开发人员正试图访问一个不存在的属性。

我们完全可以使用代理来实现这一点，方法如下:

```
function CustomErrorMsg(obj) {
  return new Proxy(obj, {
    get(target, prop, receiver) {
      if(target.hasOwnProperty(prop)) {
          return target[prop]
      }
      return new Error("Sorry bub, I don't know what a '" + prop + "' is...")
     }
  });
}
```

现在，该代码将导致以下行为:

```
> pa = CustomErrorMsg(a)
> console.log(pa.prop)
Error: Sorry bub, I don't know what a 'prop' is...
    at Object.get (repl:7:14)
    at repl:1:16
    at Script.runInThisContext (vm.js:91:20)
    at REPLServer.defaultEval (repl.js:317:29)
    at bound (domain.js:396:14)
    at REPLServer.runBound [as eval] (domain.js:409:12)
    at REPLServer.onLine (repl.js:615:10)
    at REPLServer.emit (events.js:187:15)
    at REPLServer.EventEmitter.emit (domain.js:442:20)
    at REPLServer.Interface._onLine (readline.js:290:10)

```

我们可以像我提到的那样更极端，做一些像这样的事情:

```
function HardErrorMsg(obj) {
  return new Proxy(obj, {
    get(target, prop, receiver) {
      if(target.hasOwnProperty(prop)) {
          return target[prop]
      }
      throw new Error("Sorry bub, I don't know what a '" + prop + "' is...")
     }
  });
}
```

现在，我们迫使开发人员在使用您的对象时更加小心:

```
> a = {}
> pa2 = HardErrorMsg(a)
> try {
... console.log(pa2.property)
 } catch(e) {
... console.log("ERROR Accessing property: ", e)
 }
ERROR Accessing property:  Error: Sorry bub, I don't know what a 'property' is...
    at Object.get (repl:7:13)
    at repl:2:17
    at Script.runInThisContext (vm.js:91:20)
    at REPLServer.defaultEval (repl.js:317:29)
    at bound (domain.js:396:14)
    at REPLServer.runBound [as eval] (domain.js:409:12)
    at REPLServer.onLine (repl.js:615:10)
    at REPLServer.emit (events.js:187:15)
    at REPLServer.EventEmitter.emit (domain.js:442:20)
    at REPLServer.Interface._onLine (readline.js:290:10)

```

见鬼，使用代理，你可以很好地为你的集合添加验证，确保你为你的属性分配了正确的数据类型。

您可以做很多事情，使用上面显示的基本行为来塑造符合您特定需求的 JavaScript。

## 增强#3:基于方法名的动态行为

我想介绍的最后一个例子与第一个类似。之前我们是否能够通过使用属性名来链接额外的行为(如“InUpperCase”结尾)来添加额外的功能，现在我想对方法调用做同样的事情。这将允许我们不仅通过在名字中添加额外的位来扩展基本方法的行为，还可以接收与这些额外的位相关联的参数。

让我举个例子来说明我的意思:

```
myDbModel.findById(2, (err, model) => {
  //....
})
```

如果您过去使用过数据库 ORM(例如 Sequelize 或 Mongoose)，那么这段代码应该很熟悉。该框架能够根据您设置模型的方式来猜测您的 ID 字段调用了什么。但是如果你想把它扩展成:

```
 myDbModel.findByIdAndYear(2, 2019, (err, model) => {
  //...
})
```

并更进一步:

```
myModel.findByNameAndCityAndCountryId("Fernando", "La Paz", "UY", (err, model) => {
  //...
})
```

我们可以使用代理来增强我们的对象，允许这样的行为，允许我们提供扩展的功能，而不必手动添加这些方法。此外，如果您的数据库模型足够复杂，所有可能的组合都变得太多，以至于无法添加，甚至以编程方式添加，我们的对象最终会包含太多我们没有使用的方法。通过这种方式，我们可以确保我们只有一个包罗万象的方法来处理所有的组合。

在本例中，我将创建一个假的 MySQL 模型，简单地使用一个自定义类，以保持简单:

```
var mysql      = require('mysql');
var connection = mysql.createConnection({
  host     : 'localhost',
  user     : 'user',
  password : 'pwd',
  database : 'test'
});

connection.connect();

class UserModel {
    constructor(c) {
        this.table = "users"
        this.conn = c
    }
}
```

构造函数上的属性仅供内部使用，表中可以包含您想要的所有列，这没有什么区别。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
let Enhacer = {
    get : function(target, prop, receiver) {
      let regExp = /findBy((?:And)?[a-zA-Z_0-9]+)/g
      return function() { //
          let condition = regExp.exec(prop)
          if(condition) {
            let props = condition[1].split("And")
            let query =  "SELECT * FROM " + target.table + " where " + props.map( (p, idx) => {
                let r = p + " = '" + arguments[idx] + "'"
                return r
            }).join(" AND ")
            return target.conn.query(query, arguments[arguments.length - 1])
          }
      }
    }
}
```

现在这只是一个处理程序，我将马上向您展示如何使用它，但首先有几点:

*   注意正则表达式。我们在前面的例子中也使用了它们，但是它们更简单。这里我们需要一种方法来捕捉一个重复的模式:findBy + propName +以及我们需要的任意多次。
*   通过`map`调用，我们确保将每个属性名称映射到我们收到的值。我们使用`arguments`对象获得实际值。这就是为什么我们返回的函数**不能是箭头函数**(那些没有`arguments`对象可用的函数)。
*   我们还使用了目标的`table`属性和它的`conn`属性。如您所料，目标是我们的对象，这就是为什么我们在构造函数中定义了它们。为了保持代码的通用性，这些道具需要来自外部。
*   最后，我们用两个参数调用`query`方法，我们假设我们的伪方法收到的最后一个参数是实际的回调。这样我们就能抓住它并传递下去。

就是它，TL；上面的 DR 应该是:我们将方法名转换成一个 SQL 查询，并使用实际的`query`方法执行它。

下面是如何使用上面的代码:

```
let eModel = new Proxy(new UserModel(connection), Enhacer) //create the proxy here

eModel.findById("1", function(err, results) { //simple method call with a single parameter
    console.log(err)
    console.log(results)
})
eModel.findByNameAndId('Fernando Doglio', 1, function(err, results) { //extra parameter added
    console.log(err)
    console.log(results)
    console.log(results[0].name)
})
```

就是这样，之后的结果会像你一样被使用，不需要任何额外的东西。

## 结论

这将是这篇文章的结尾，希望它有助于澄清代理背后的一些困惑，以及您可以用它们做什么。现在让你的想象力自由驰骋，用它们来创建你自己的 JavaScript 版本吧！

下一次再见！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)