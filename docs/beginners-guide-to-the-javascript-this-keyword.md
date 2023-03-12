# JavaScript this keyword-log rocket 博客初学者指南

> 原文：<https://blog.logrocket.com/beginners-guide-to-the-javascript-this-keyword/>

理解编程语言的基本概念会大有帮助。在 JavaScript 中，`this`关键字就是这样一个基石。

JavaScript 中的关键字`this`可能是一块难啃的骨头。一些初学者纠结于措辞，其他人纠结于它的动态本质。

在本教程中，我们将试图揭开 JavaScript 的关键字`this`的神秘面纱，这样做有助于你练习调试问题，否则会非常复杂。

## 什么是`this`关键词？

在短短几行代码的幕后，发生了如此多的事情，要执行上述代码，您必须回答两个基本问题:

1.  代码在哪里执行(例如，在函数中还是全局执行)？
2.  确切地说，代码是什么(例如，数组方法或对象方法)？

`this`关键字有助于回答代码在哪里，因为它是所谓的[执行上下文](https://stackoverflow.com/questions/9384758/what-is-the-execution-context-in-javascript-exactly)的一部分。执行上下文定义了一段代码的邻域。不幸的是，它的命名很糟糕，这导致了新 JavaScript 开发人员的一些困惑。

默认情况下，所有代码都在全局执行上下文中执行，而不是像函数体那样在局部执行上下文中执行。把全局执行上下文想象成你的院子，把局部执行上下文想象成你的房子。

范围定义了代码在执行上下文中的可见性/可访问性。例如，说变量`cats`在函数`catMaker()`的作用域之外意味着函数`catMaker()`不能访问变量`cats`，因为`cats`不在`catMaker()`的作用域链中。范围链定义了特定执行上下文可以访问的所有变量。

要吸收的信息很多，但是你真的需要理解这个才能理解`this`。如果你还在挣扎着跟上，不要着急——你并不孤单。试着再读一遍，或者去参考资料部分寻找更多有用的指南。

## 你会在哪里找到`this`？

让我们看看你可能会遇到`this`关键词的一些地方。

### 全局执行上下文

全局执行上下文是默认的执行上下文，其中有一个局部执行上下文。如果您要编写一些代码，相应的上下文将被定义如下。

```
let myName = "John Doe";
// global execution context

function sayName() {
   // local execution context
   console.log(myName);
}
```

在全局执行上下文中，`this`的值与浏览器中的`window`对象相同。可以把 window 对象想象成浏览器中的一个选项卡(因为它包含了关于它的各种有趣的细节)。要验证`this`是否与全局执行上下文中的`window`对象相同，您只需运行下面这段代码。

```
console.log(this === window); // prints true
```

### 简单函数

函数有它们自己的执行上下文和范围，但是如果你在全局执行上下文中定义一个函数，`this`的值将再次与窗口对象相同。

```
function someFunc() {
  return this;
}

someFunc() === window; // returns true
```

这可能是希望的，也可能是不希望的。如果您想避免这种情况，可以启用 JavaScript 中的严格模式。这实际上迫使 JavaScript 在适当的地方抛出更多的错误，最终产生更可预测的代码。当启用严格模式时，`this`将屈服于未定义。

```
function someFunc() {
  "use strict"
  console.log(this);
}

someFunc(); // returns undefined
```

也可能有这样的情况，你想把函数的`this`改成别的，或多或少地改变函数的上下文。为此，您可以使用函数 [`call()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) 、 [`apply()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 和 [`bind()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) 。从后者开始，`bind()`函数用您提供的值`this`绑定一个函数，并返回一个新函数。

```
const obj = { 
   message: "hello world"
}

function printMessage() {
   console.log(this.message);
};

const boundPrintMessage = printMessage.bind(obj);
printMessage(); // prints undefined
boundPrintMessage(); // prints "hello world"
```

`bind()`函数是一个非常强大的工具，可以帮助你创建可重用的代码并解决一些棘手的情况，其中一些我们将在后面讨论。

如果你想避免返回一个绑定到值`this`的新函数，你应该考虑使用`call()`或者`apply()`。`call()`和`apply()`都允许你调用一个你提供的值为`this`的函数，除了用`call()`，你可以把参数传入函数，用`apply()`，你把那些参数作为数组传递。

```
const user = {
 name: 'John Doe'
}

function printUser(likes) {
  console.log(`My name is ${this.name}, and I like ${likes}`)
};

printUser.call(user, 'apples')
printUser.apply(user, ['apples'])
```

### 箭头功能

箭头函数，也称为 ES6 胖箭头函数，除了几个关键的例外，几乎与普通函数相同。首先，与普通函数不同，`this`的值不会默认为`window`对象。您可以通过在对象中声明箭头函数来演示这一点。

```
const obj = {
  message: "hello world",
  arrowFunc: () => console.log(this.message),
  plainFunc: function() {
   console.log(this.message);
  }
}

obj.arrowFunc() // prints undefined
obj.plainFunc() // prints hello world
```

因为在这种情况下箭头函数没有自己的值`this`，所以不建议使用箭头函数作为对象方法。你可能会想，既然`bind()`允许你改变函数的`this`的值，你可以使用`bind()`来避免这种行为，但是这是行不通的。

```
const obj = {
  message: "hello world",
  arrowFunc: () => console.log(this.message),
  plainFunc: function() {
   console.log(this.message);
  }
}

const boundArrowFunc = obj.arrowFunc.bind(obj);
boundArrowFunc(); // prints undefined
```

引入`call()`、`apply()`和`bind()`是为了允许函数在您定义的范围内执行，但是箭头函数中`this`的值取决于它定义的范围。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 班级

ES6 类总是在严格模式下运行，因此类的`this`值与`window`对象不同。正如你可能知道的，ES6 类是一种[语法糖](https://en.wikipedia.org/wiki/Syntactic_sugar#:~:text=In%20computer%20science%2C%20syntactic%20sugar,style%20that%20some%20may%20prefer.)，所以如果你用传统函数风格写一个 ES6 类，`this`的值将是窗口对象。

类中`this`的值取决于它们如何被调用。因此，在某些情况下，您可能希望将`this`的值设置为类实例的值。

```
class Person {
  constructor() {
   this.name = "John Doe"
   this.sayName = this.sayName.bind(this); // Try running the code without this line
 } 
   sayName() {
    console.log(this.name);
  }
}

const somePerson = new Person();
somePerson.sayName();
const sayName = somePerson.sayName;
sayName();
```

如果您熟悉 React 的使用，那么在编写类组件时，您可能会熟悉这种称为硬绑定的模式。当您没有将事件处理程序中`this`的值绑定到类的值时，`this`的值往往是未定义的。

## 如何确定`this`可以解决什么问题

我们已经讨论了一些最常见的情况，但是，当然，你可能会遇到其他情况。参考以下提示，帮助确定在给定场景中`this`可以解决什么问题。

1.  如果代码不在主体/块中，那么`this`与浏览器中的`window`对象相同
2.  如果代码被作为对象方法调用，并通过点符号表示，那么向左看；左边的就是`this`的样子
3.  如果代码在函数内部，那么当严格模式不活动时，`this`与`window`对象相同。当严格模式被启用时，`this`是未定义的，因为它应该是
4.  如果函数是一个对象方法，那么如果它是一个普通函数，`this`解析为定义该函数的对象，而箭头函数将引用封闭的执行上下文
5.  如果你想定义一个函数的`this`值应该是多少，你应该使用`call()`、`apply()`或`bind()`。

## 结论

当你开始与复杂的框架和库争论时，理解 JavaScript 的基础将会给你很大的帮助。如果你想学习如何调试和编写行为正常的无错代码，就必须对像关键字`this`这样的主题有一个坚实的理解。

如果你没有马上理解，不要担心——这种复杂的话题需要一段时间才能理解。为了获得清晰的理解，你必须编写代码，对我们在这篇文章中描述的情况有所了解，并通过反复试验来解决出现的问题。这将有助于巩固你的理解，让你进入下一步。

## 资源

通过理解上下文，更容易地调试 JavaScript 错误

## 调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

.

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.