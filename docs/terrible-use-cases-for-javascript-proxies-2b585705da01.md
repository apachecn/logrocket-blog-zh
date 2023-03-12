# JavaScript 代理的可怕用例

> 原文：<https://blog.logrocket.com/terrible-use-cases-for-javascript-proxies-2b585705da01/>

![](img/43779c5ffdf34044ff7750a46f300c20.png)

ECMAScript 6 为 JavaScript 引入了许多新的语言特性，其中包括代理。在我看来，这是 JavaScript 最被低估的特性。

[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)允许我们拦截和重新定义内部操作的行为，如属性获取器、设置器、赋值、调用操作等等，从而使我们能够进行运行时元编程。

现在，代理的实际、真实、实用的良好用例少之又少。在大多数情况下，同样的事情可以用一点重复的样板代码来实现，但性能要好得多。尽管如此，代理是伟大的，令人难以置信的强大。让我们来看看一些可怕的用例，来展示代理是多么神奇。

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)

### 原谅属性名

我们可以覆盖的操作之一是对象的属性 getter。因此，让我们使用这个来提供一个自动更正的属性查找，使用 [Levenshtein 距离](https://en.wikipedia.org/wiki/Levenshtein_distance)来近似用户想要的属性名称。

首先，我们需要定义一个函数来返回两个字符串之间的 Levenshtein 距离。Levenshtein 距离本质上是将一个字符串更改为另一个字符串所需的单字符编辑(插入、删除或替换)的最小数量的度量。

我们将使用递归变体，因为它比更优化的变体更简单易懂。然而，应该注意的是，与使用查找表的迭代方法相比，它的效率也非常低:

```
function levenshtein(a, b) {
  if (a.length == 0) {
    return b.length;
  }

  if (b.length == 0) {
    return a.length;
  }

  let cost = (a.charAt(a.length - 1) == b.charAt(b.length - 1)) ? 0 : 1;

  return Math.min(
    levenshtein(a.substring(0, a.length - 1), b) + 1,
    levenshtein(a, b.substring(0, b.length - 1)) + 1,
    levenshtein(a.substring(0, a.length - 1), b.substring(0, b.length - 1)) + cost,
  );
}
```

计算出 Levenshtein 距离后，通过将属性名数组简化为与目标属性距离最短的字符串来获得最匹配的属性名是相当简单的:

```
function getClosestPropertyName(names, name) {
  let lowest = Infinity;

  return names.reduce(function(previous, current) {
    let distance = levenshtein(current, name);
    if (distance < lowest) {
      lowest = distance;
      return current;
    }

    return previous;
  }, '');
}
```

最后，转到实际的代理对象，代理被定义为具有目标对象和处理程序对象的对象。目标是由代理虚拟化的对象，而处理程序是其属性是陷阱的对象，或者是当对代理进行操作时定义代理行为的函数。

因此，为了使对象的属性得到“自动修正”,我们将定义一个函数，该函数将目标作为参数，并返回一个重新定义 get 陷阱的代理:

```
function autoCorrect(target, recursive) {
  return new Proxy(target, {
    get: function(target, name) {
      if (!(name in target)) {
        name = getClosestPropertyName(Object.getOwnPropertyNames(target), name);
      }

      return target[name];
    },
  });
}
```

当使用时，将产生以下结果:

```
Math = autoCorrect(Math);
console.log(Math.PI); // 3.141592653589793
console.log(Math.PIE); // 3.141592653589793
console.log(Math.PIEE); // 3.141592653589793
```

Get 陷阱也覆盖下标运算符，因为成员和下标运算符使用此陷阱，这意味着以下内容与上面的示例等效:

```
Math = autoCorrect(Math);
console.log(Math["PI"]); // 3.141592653589793
console.log(Math["PIE"]); // 3.141592653589793
console.log(Math["PIEE"]); // 3.141592653589793
```

### 严格类型的对象

前面用例的一个稍微有用的变化是不允许使用未知属性，而是抛出一个错误指出“最有可能”的候选。

我们将像以前一样重用同一个 Levenshtein 函数，但不是添加一个工厂函数来创建代理，而是通过返回一个代理到构造的对象而不是对象本身，将它嵌入到类构造函数中:

```
 class Person {
  constructor() {
    this.age = '';
    return new Proxy(this, {
      get: function(target, name) {
        if (!(name in target)) {
          let alt = getClosestPropertyName(Object.getOwnPropertyNames(target), name);
          throw new ReferenceError(`${name} is not defined, did you mean ${alt}?`);
        }

        return target[name];
      },
      set: function(target, name, value) {
         if (!(name in target)) {
          let alt = getClosestPropertyName(Object.getOwnPropertyNames(target), name);
          throw new ReferenceError(`${name} is not defined, did you mean ${alt}?`);
        }

        target[name] = value;
      },
    });
  }
}
```

当访问不存在的属性时，会产生以下错误:

```
p = new Person();
p.age = 30;
p.name = "Luke"
p.jedi = true; // ReferenceError: jedi is not defined, did you mean age?
```

### 结论

代理的功能非常强大，可以被广泛使用和滥用，但是重要的是要记住，代理不能被预处理器模拟，必须得到运行时本身的支持。引入的特性不向后兼容的情况很少见。在大多数情况下，我们可以在没有代理的情况下实现同样的目标，尽管这可能会涉及更多的样板代码。

另一件要记住的事情是，使用代理不是免费的，有一个不小的开销，因为还有另一个层次的间接性在起作用。所以在某些情况下，编译时元编程可能比运行时元编程更受欢迎。

最后，代理虽然相当神奇，但并不一定会产生非常干净和容易理解的代码，但它们值得了解，因为在某些情况下，它们可能是最好的方式，甚至是唯一的方式。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.