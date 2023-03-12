# 什么是 globalThis，为什么您应该开始使用它？

> 原文：<https://blog.logrocket.com/what-is-globalthis-why-use-it/>

JavaScript 语言越来越多地用于各种环境中。除了 web 浏览器这种最常见的 JavaScript 主机环境之外，您还可以在服务器、智能手机甚至机器人硬件上运行 JavaScript 程序。

每个环境都有自己的对象模型，并提供不同的语法来访问全局对象。例如，在网络浏览器中，可以通过`window`、`self`或`frames`访问全局对象。然而，在 Node.js 中，这些属性并不存在，您必须使用`global`来代替。在 Web Workers 中，只有`self`可用。

这些引用全局对象的不同方式使得编写在多种环境下工作的可移植 JavaScript 代码变得困难。幸运的是，正在制定一个[提案，旨在通过引入一个在所有环境中都可用的标准属性`globalThis`来解决这个问题。](https://github.com/tc39/proposal-global)

在本文中，我们将首先看看流行的 JavaScript 环境中的全局对象，然后看看`globalThis`如何提供访问它的统一机制。

## `window`

`window`属性用于在浏览器环境中引用当前文档的全局对象。在代码的顶层，使用`var`关键字声明的变量变成了`window`的属性，可以从代码的任何地方访问:

```
var a = [10, 20];

console.log(window.a);          // → [10, 20]
console.log(a === window.a);    // → true
```

通常情况下，使用属性时不需要直接引用`window`，因为引用是隐含的。然而，当存在与全局变量同名的局部变量时，使用`window`是唯一的选择:

```
var a = 10;

(function() {
  var a = 20;   
  console.log(a);           // → 20
  console.log(window.a);    // → 10
})();
```

如您所见，`window`对于引用全局对象非常有用，不管代码运行的范围是什么。注意，`window`实际上指的是`window.window`。所以，`window.window === window`。

除了标准的 JavaScript 属性和方法之外，`window`对象还包含几个额外的属性和方法，允许我们控制 web 浏览器窗口和文档本身。

## `self`

Web Workers API 没有`Window`对象，因为它没有浏览上下文。相反，它提供了包含通常由`window`携带的数据的`WorkerGlobalScope`接口。

为了在 Web Workers 中访问全局对象，我们使用了`self`，它是`Window`对象的`window`属性的同义词。类似于`window`，`self`是对全局对象的引用，可用于使引用显式而非隐式:

```
// a web worker
console.log(self);    // => DedicatedWorkerGlobalScope {...}

var a = 10;

console.log(self.a);          // → 10
console.log(a === self.a);    // → true
```

在浏览器环境中，这段代码将记录`Window`而不是`DedicatedWorkerGlobalScope`。因为`self`的值会根据使用它的环境而变化，所以有时它比`window`更好。当`self`在 web worker 上下文中引用`WorkerGlobalScope.self`时，它在浏览器上下文中引用`window.self`。

重要的是不要将`self`属性与声明局部变量的常见 JavaScript 模式相混淆，后者用于维护对上下文的引用。例如:

```
const obj = {
  myProperty: 10,
  myMethod: function(){
    console.log(this === obj);    // => true

    // store the value of this in a variable for use in nested functions
    const self = this;

    const helperFunction = (function() {
      console.log(self === obj);  // => true (self refers to the outer this value)
      console.log(this === obj);  // => false (this refers to the global object. In strict mode, it has a value of undefined)
    })();
  }
};

// invoke myMethod on the object obj.
obj.myMethod();
```

## `frames`

在浏览器环境中访问全局对象的另一种方法是使用`frames`属性，它的工作方式类似于`self`和`window`:

```
// browser environment
console.log(frames);    // => Window {...}
```

这个只读属性通常用于获取当前窗口的子框架列表。例如，您可以使用`window.frames[0]`或`frames[0]`来访问第一帧。

## `global`

在 Node.js 中，可以使用`global`关键字访问全局对象:

```
// node environment
console.log(global);    // => Object [global] {...}
```

`window`、`self`或`frames`在节点环境中不起作用。请记住，Node.js 中的顶级作用域不是全局作用域。在浏览器中，`var abc = 123`会创建一个全局变量。然而，在 Node.js 中，变量将是模块本身的局部变量。

## `this`

在浏览器中，您可以在程序的顶层使用`this`关键字来引用全局对象:

```
this.foo = 123;
console.log(this.foo === window.foo);    // => true
```

`this`在非严格模式下运行的内部函数或箭头函数也引用全局对象。但是在严格模式下运行的函数却不是这样，在严格模式下，`this`的值为`undefined`:

```
(function() {
  console.log(this);    // => Window {...}
})();

(() => {
  console.log(this);    // => Window {...}
})();

(function() {
  "use strict";
  console.log(this);    // => undefined
})();
```

在节点模块中，顶层的`this`不引用全局对象。取而代之的是与`module.exports`相同的值。在函数内部(节点环境)，`this`的值是根据函数的调用方式决定的。在 JavaScript 模块中，顶层的`this`是`undefined`。

## 介绍`globalThis`

`globalThis`旨在通过定义一个标准的全局属性来整合日益分散的访问全局对象的方式。`globalThis`提案目前处于第 4 阶段，这意味着它已准备好纳入 ES2020 标准。所有流行的浏览器，包括 Chrome 71+、Firefox 65+和 Safari 12.1+都已经支持该功能。也可以在 Node.js 12+中使用。

```
// browser environment
console.log(globalThis);    // => Window {...}

// node.js environment
console.log(globalThis);    // => Object [global] {...}

// web worker environment
console.log(globalThis);    // => DedicatedWorkerGlobalScope {...}
```

通过使用`globalThis`，您的代码将在窗口和非窗口环境中工作，而不必编写额外的检查或测试。在大多数环境中，`globalThis`直接指那个环境的全局对象。然而，在浏览器中，内部使用代理来考虑 iframe 和跨窗口安全性。但是，在实践中，它不会改变您编写代码的方式。

通常，当您不确定您的代码将在什么环境中使用时，或者当您想要使您的代码在不同的环境中可执行时，`globalThis`属性就非常方便了。但是，您必须使用 polyfill 在不支持该功能的旧浏览器上实现该功能。

另一方面，如果您确定您的代码将在什么环境中使用，那么使用一种现有的引用环境的全局对象的方法，这样您就不需要为`globalThis`包含一个 polyfill。

## 创建一个`globalThis`聚合填充

在引入`globalThis`之前，跨不同环境访问全局对象的一种常见方式是使用以下模式:

```
function getGlobalObject() {
  return Function('return this')();
}

if (typeof getGlobalObject().Promise.allSettled !== 'function') {
  // the Promise.allSettled() method is not available in this environment
}
```

这段代码的问题在于，`Function`构造函数和`eval`不能在实施[内容安全策略(CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) 的网站中使用。Chrome 的扩展系统也因为 CSP 不允许这样的代码运行。

引用全局对象的另一种模式如下:

```
function getGlobalObject() {
  if (typeof globalThis !== 'undefined') { return globalThis; }
  if (typeof self !== 'undefined') { return self; }
  if (typeof window !== 'undefined') { return window; }
  if (typeof global !== 'undefined') { return global; }
  throw new Error('cannot find the global object');
};

if (typeof getGlobalObject().Promise.allSettled !== 'function') {
  // the Promise.allSettled() method is not available in this environment
}
```

这种模式通常在 web 上使用。但是这也有几个缺陷，使得它在某些情况下不可靠。幸运的是，Chrome DevTools [的 Mathias Bynens 提出了一个没有这些缺点的创造性模式](https://mathiasbynens.be/notes/globalthis#robust-polyfill):

```
(function() {
  if (typeof globalThis === 'object') return;
  Object.defineProperty(Object.prototype, '__magic__', {
    get: function() {
      return this;
    },
    configurable: true // This makes it possible to `delete` the getter later.
  });
  __magic__.globalThis = __magic__; // lolwat
  delete Object.prototype.__magic__;
}());

// Your code can use `globalThis` now.
console.log(globalThis);
```

与其他方法相比，这种 polyfill 是一种更健壮的解决方案，但它仍然不完美。正如 Mathias 提到的，修改`Object`、`Object.defineProperty`或`Object.prototype.__defineGetter__`可能会破坏聚合填充。

## 包扎

很难编写一个在多种环境下都能工作的可移植的 JavaScript 代码。每个主机环境都有稍微不同的对象模型。因此，要访问全局对象，需要在不同的 JavaScript 环境中使用不同的语法。

随着`globalThis`属性的引入，访问全局对象将变得更加简单，并且不再需要检测代码运行的环境。

乍一看，`globalThis`似乎是一件很容易填充的事情；但是在实际操作中，要做到正确是非常复杂的。所有现有的解决方法都不完美，如果不小心的话，可能会引入错误。

ECMAScript 正在快速发展，您可以期待新的功能会更频繁地推出。要了解规格的最新补充，请查看[已完成提案的列表](https://github.com/tc39/proposals/blob/master/finished-proposals.md)。

## 实现新的 JS 特性？了解 JavaScript 错误如何影响用户。

追踪生产 JavaScript 异常或错误的原因是耗时且令人沮丧的。如果您对监控 JavaScript 错误感兴趣，并想看看它们是如何影响用户的，[试试 LogRocket](https://logrocket.com/signup/) 。[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://logrocket.com/signup/)[https://logrocket.com/signup/](https://logrocket.com/signup/)

LogRocket 就像是网络应用的 DVR，记录下你网站上发生的每一件事。LogRocket 使您能够聚合和报告错误，以查看它们发生的频率以及它们影响了多少用户群。您可以轻松地重放发生错误的特定用户会话，以查看导致错误的用户操作。

LogRocket 让你的应用程序记录带有标题+正文的请求/响应，以及关于用户的上下文信息，以全面了解问题。它还记录页面上的 HTML 和 CSS，甚至可以重建最复杂的单页面应用程序的像素级完美视频。

增强您的 JavaScript 错误监控能力–––[开始免费监控](https://logrocket.com/signup/)。