# JavaScript ES6 代理的实际用例

> 原文：<https://blog.logrocket.com/practical-use-cases-for-javascript-es6-proxies/>

元编程是一种强大的技术，它使您能够编写能够创建其他程序的程序。在代理和许多类似特性的帮助下，ES6 使得在 JavaScript 中使用元编程变得更加容易。 [ES6 代理](https://blog.logrocket.com/use-es6-proxies-to-enhance-your-objects/)促进了对象中基本操作的重新定义，为各种可能性打开了大门。

在本指南中，我们将向您展示如何在实际情况中应用 ES6 代理。

## 先决条件和结果

本教程主要面向有 JavaScript 经验并且至少熟悉 ES6 代理概念的开发人员。如果你对代理作为一种设计模式有很深的理解，这种知识应该可以转化。

阅读本指南后，您应该能够:

*   了解什么是 ES6 代理、如何实现以及何时使用它
*   使用 ES6 代理进行访问控制、缓存和数据绑定

## ES6 代理的剖析:目标、处理程序和陷阱

从根本上来说，代理是成为其他东西的替代品的东西或人，所以无论它是什么，它都必须通过替代品才能达成真正的交易。ES6 代理以同样的方式工作。

为了有效地实现和使用 ES6 代理，您必须理解三个关键术语:

1.  **目标** —代理正在替代的真实交易，目标是代理背后的东西。这可以是任何对象
2.  **处理程序** —包含所有代理陷阱逻辑的对象
3.  **陷阱** —类似于操作系统中的陷阱，[陷阱](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)在这个上下文中是以某种方式提供对对象的访问的方法

将所有这些放在一起，下面是最简单的实现，其中如果给定的属性在使用 ES6 代理的对象中不存在，您可以返回不同的内容。

```
const target = {
    someProp: 1
}

const handler = {
    get: function(target, key) {
        return key in target ? 
        target[key] : 
        'Doesn't exist!';
    }
}

const proxy = new Proxy(target, handler);
console.log(proxy.someProp) // 1
console.log(proxy.someOtherProp) // Doesn't exist!

```

ES6 代理是一个强大的特性，它促进了 JavaScript 中对象的虚拟化。

## 数据绑定:同步多个对象

由于数据绑定的复杂性，它通常很难实现。可以在 JavaScript 的模型-视图-控制器库中看到 ES6 代理实现双向数据绑定的应用，其中当 DOM 发生变化时，对象被修改。

简而言之，数据绑定是一种将多个数据源绑定在一起以实现同步的技术。

假设有一个 id 为`username`的`<input>`。

```
<input type="text" id="username" /> 

```

假设您想让这个输入的值与对象的属性保持同步。

```
const inputState = {
    id: 'username',
    value: ''
}

```

当`input`的值改变时，通过监听输入的`change`事件，然后更新`inputState`的值，可以很容易地修改`inputState`。然而，相反——当`inputState`被修改时更新`input`——是相当困难的。

在这种情况下，ES6 代理会有所帮助。

```
const input = document.querySelector('#username')
const handler = {
    set: function(target, key, value) {
        if (target.id && key === 'username') {
            target[key] = value;
            document.querySelector(`#${target.id}`)
            .value = value;
            return true
        }
        return false
    }
}

const proxy = new Proxy(inputState, handler)
proxy.value = 'John Doe'
console.log(proxy.value, input.value) 
// 'John Doe' will be printed for both

```

这样，当`inputState`改变时，`input`将反映已经做出的改变。结合监听`change`事件，这将产生一个简单的`input`和`inputState`的双向数据绑定。

虽然这是一个有效的用例，但通常不鼓励这样做。稍后会详细介绍。

## 缓存:增强代码性能

缓存是一个古老的概念，它允许非常复杂和大型的应用程序保持相对高的性能。缓存是存储某些数据片段的过程，以便在请求时可以更快地提供这些数据。缓存不会永久存储任何数据。缓存失效是确保缓存新鲜的过程。这是开发者共同的奋斗。正如 Phil Karlton 所说，“在计算机科学中只有两件困难的事情:缓存失效和命名。”

ES6 代理使缓存更容易。例如，如果您想检查某个对象中是否存在某个东西，它将首先检查缓存并返回数据，或者如果该数据不存在，则执行其他操作来获取该数据。

假设您需要进行大量的 API 调用来获取特定的信息并对其进行处理。

```
const getScoreboad = (player) => {
    fetch('some-api-url')
    .then((scoreboard) => {
        // do something with scoreboard
    })
}

```

这将意味着每当需要玩家的记分牌时，必须进行新的呼叫。相反，您可以在第一次请求记分板时缓存它，随后的请求可以从缓存中取出。

```
const cache = { 
    'John': ['55', '99']
}
const handler = { 
    get: function(target, player) {
        if(target[player] {
            return target[player]
        } else {
            fetch('some-api-url')
            .then((scoreboard => {
                target[player] = scoreboard
                return scoreboard
            })
        }
    }
}
const proxy = new Proxy(cache, handler)
// access cache and do something with scoreboard

```

这样，只有当缓存不包含玩家的记分牌时，才会进行 API 调用。

## 访问控制:控制进出对象的内容

最简单的用例是访问控制。ES6 代理的大部分功能都在访问控制之下。我们演示如何实现代理的场景是访问控制的一个例子。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们探索一些使用 E6 代理的访问控制的实际应用。

### 1.确认

ES6 代理最直观的用例之一是验证对象内部的内容，以确保对象中的数据尽可能准确。例如，如果您想要强制产品描述的最大字符数，您可以这样做:

```
const productDescs = {}
const handler = {
    set: function(target, key, value) {
        if(value.length > 150) {
            value = value.substring(0, 150)
        }
        target[key] = value
    }
}
const proxy = new Proxy(productDescs, handler)

```

现在，即使你添加了超过 150 个字符的描述，它也会被缩短并添加。

### 2.提供对象的只读视图

有时，您可能希望确保对象不会被以任何方式修改，并且只能用于读取目的。JavaScript 提供了`Object.freeze()`来做到这一点，但是当使用代理时，行为更加可定制。

```
const importantData = {
    name: 'John Doe',
    age: 42
}

const handler = {
    set: 'Read-Only',
    defineProperty: 'Read-Only',
    deleteProperty: 'Read-Only',
    preventExtensions: 'Read-Only',
    setPrototypeOf: 'Read-Only'
}

const proxy = new Proxy(importantData, handler)

```

现在，当你试图以任何方式改变对象时，你只会收到一个字符串`Read Only`。否则，您可能会引发一个错误，指示该对象是只读的。

### 3.私有财产

JavaScript 本身没有私有属性，除了闭包。当`Symbol`数据类型被引入时，它被用来模拟私有属性。但是随着`Object.getOwnPropertySymbols`方法的引入，它被搁置了。ES6 代理不是一个完美的解决方案，但它们在紧要关头也能发挥作用。

一个常见的约定是通过在私有属性的名称前添加下划线来标识私有属性。这个约定使您能够使用 ES6 代理。

```
const object = {
    _privateProp: 42
}

const handler = {
    has: function(target, key) {
        return !(key.startsWith('_') && key in target)
    },
    get: function(target, key, receiver) {
        return key in receiver ? target[key] : undefined
    }
}

const proxy = new Proxy(object, handler)
proxy._privateProp // undefined

```

添加`ownKeys`和`deleteProperty`将使这个实现更接近真正的私有属性。同样，您仍然可以在开发人员控制台中查看代理对象。如果您的用例符合上述实现，它仍然适用。

## 为什么以及何时使用代理

ES6 代理不适合性能密集型任务。这就是为什么执行必要的测试是至关重要的。代理可以用在任何需要对象的地方，代理提供的复杂功能只需要几行代码，这使它成为元编程的理想特性。

代理通常与另一个称为[反射](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect)的元编程特性一起使用。

## 摘要

希望本指南已经帮助您理解了为什么 ES6 代理是如此伟大的工具，尤其是对于元编程。你现在应该知道:

*   什么是 ES6 代理
*   如何以及何时实现代理
*   如何使用 ES6 代理来执行访问控制、数据绑定和缓存
*   ES6 代理不适合性能密集型任务

要了解更多信息，请查看以下资源。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.