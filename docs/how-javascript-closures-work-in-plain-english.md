# JavaScript 闭包如何工作，用简单的英语——log rocket 博客

> 原文：<https://blog.logrocket.com/how-javascript-closures-work-in-plain-english/>

JavaScript 是一种被广泛采用的语言，可以用来构建任何东西，从简单的登录页面到生产级的全栈应用程序。随着 JavaScript 和编程的发展，开发人员开始意识到面向对象编程(OOP)范式对于大多数用例来说是不可取的。函数式编程作为许多与 OOP 相关的棘手问题的解决方案而出现。

闭包是函数式编程领域中一个被广泛讨论的话题，但是它们的定义通常很松散，并且使用技术术语。我们将尽力用通俗的语言解释 JavaScript 闭包是如何工作的。

学完本教程后，您应该明白:

*   如何识别闭包
*   什么是闭包，它的行为与执行上下文和调用堆栈有什么关系
*   闭包的常见用例

## 理解 JavaScript 闭包

我们将从展示闭包是什么样子开始。

```
function makeCounter() {
  let count = 0;
  return function increment() {
    count += 1;
    return count;
  };
};

const countIncrementor = makeCounter();
countIncrementor(); // returns 1
countIncrementor(); // returns 2
```

尝试自己运行代码。从技术上讲，名为`makeCounter`的函数返回另一个名为`increment`的函数。即使在执行了`makeCount`函数之后，这个`increment`函数也可以访问`count`变量。这里闭包的一部分是`count`变量；即使在`makeCounter`完成之后，当它被定义时，它也可用于`increment`函数。另一部分是`increment`功能。

想象你有一栋房子和一个围绕着它的花园。一旦你打开了通向花园的门，又关上了，你就不能再打开它了——花园变得无法进入。你饿了，幸运的是，你的花园里有一棵橘子树和一棵苹果树。你拿一个小包，摘一个橘子和一个苹果，然后回到你的房子里。记住，你不能再出去了。

现在，一旦你在你的房子里，你可以把橘子或苹果从袋子里拿出来，当你再饿的时候就吃。这个例子中的小袋子是封闭的。一个闭包包含了所有的变量和函数，当你在花园里的时候，甚至当你在房子里不能再出去的时候。

让我们看看这是如何在代码中实现的:

```
function makeFruitGarden() {
  let fruits = ['apple', 'orange'];
  return function() {
    return fruits.pop();
  };
};

const consumeFruit = makeFruitGarden();
consumeFruit(); // returns orange
consumeFruit(); // returns apple
```

因为当执行`makeFruitGarden`时，返回的函数可以使用`fruits`变量，所以`fruits`变量和内部函数成为闭包。每当执行`consumeFruit`时，返回一个`fruit`——来自`fruits`数组的最后一个元素，因为`pop()`正在被使用。一旦两种水果都吃完了，就没什么可吃的了。

## 理解词汇范围

要真正理解闭包，您应该熟悉术语“作用域”相对于你所指的任何事物，词法范围是当前环境的一个奇特术语。

在下面的例子中，名为`myName`的变量的作用域称为“全局作用域”。

```
// global scope
const myName = "John Doe"

function displayName() {
   // local/function scope
  console.log(myName);
};

displayName()
```

在阅读`var`如何不是块范围的以及`const` / `let`如何是块范围的时，你可能已经看到了这个概念。值得注意的是，在 JavaScript 中，函数总是创建自己的作用域。这被称为`local`或`function`范围，如代码示例所示。

如果你一直在关注，你可能会认为`myName`和`displayName`是闭包的一部分。你是对的！但是因为这里的函数和变量存在于全局范围内，所以称之为闭包没有太大的价值。

JavaScript 中有许多类型的作用域，但是对于闭包，有三种作用域您应该知道:

1.  **全局范围**是每个人居住的默认范围。把它当成你的街道
2.  **外部函数作用域**是返回函数的函数。它有自己的范围。把它当成你的花园
3.  **内部/局部函数作用域**是成为闭包的返回函数。把它当成你的房子

现在让我们深入一些用例。

## 闭包的常见用例

### 携带

函数 currying 是函数式编程中另一个强大的概念。要在 JavaScript 中实现 curried 函数，可以使用闭包。

Currying 一个函数可以描述为转换一个函数，并且是这样执行的:`add(1, 2, 3)`到`add(1)(2)(3)`。

```
function add(a) { 
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
};

add(1)(2)(3) // returns 6
```

`add`函数接受一个参数，然后返回两个嵌套在一起的函数。currying 的目标是接受一堆参数，最终得到一个值。

### 高阶函数

高阶函数的目标是将函数作为参数并返回结果。像`map`和`reduce`这样的数组方法就是高阶函数的例子。

```
const arrayOfNumbers = [1, 2, 3];
const displayNumber = (num) => {
  console.log(num);
}
arrayOfNumbers.forEach(displayNumber)
```

这里的 [`Array.prototype.forEach`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 高阶函数接受`displayNumber`作为参数，然后对`arrayOfNumbers`中的每个元素执行。如果您使用过 Vue 或 React 之类的 UI 框架，您可能会对高阶组件很熟悉，它们本质上与高阶函数是一回事。

那么高阶函数和 currying 有什么区别呢？高阶函数将函数作为参数返回值，而 curried 函数将函数作为结果返回，最终得到一个值。

### DOM 元素管理器

这是一种常见的设计模式，通常用于获取和设置 DOM 元素的属性。在下面的例子中，我们将制作一个元素管理器来设计元素的样式。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
function makeStyleManager(selector) {
    const element = document.querySelector(selector);
    const currentStyles = {...window.getComputedStyle(element)};

    return {
        getStyle: function(CSSproperty) {
            return currentStyles[CSSproperty];
        },
        setStyle: function(CSSproperty, newStyle) {
            element.style[CSSproperty] = newStyle;
        },
    };
};

const bodyStyleManager = makeStyleManager('body');
bodyStyleManager.getStyle('background-color'); // returns rgb(0,0,0)
bodyStyleManager.setStyle('background-color', 'red'); // sets bg color to red
```

`makeStyleManager`返回一个对象，该对象提供对两个函数的访问，这两个函数是闭包的一部分，与`element`和`currentStyles`变量在一起。即使在`makeStyleManager`已经完成执行之后，`getStyle`和`setStyle`函数仍然可以访问变量。

## 结论

JavaScript 闭包可能很难理解，即使对于有专业经验的开发人员来说也是如此。理解闭包最终会让你成为更好的开发人员。

当闭包被用在看起来奇怪或者没有意义的代码库中时，你现在应该能够识别它了。闭包是函数式编程中的一个关键概念，我希望这篇指南能帮助你在掌握它的过程中向前迈进一步。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

。

### 进一步阅读