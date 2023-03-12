# 如何访问回调日志中正确的 this-LogRocket 博客

> 原文：<https://blog.logrocket.com/access-correct-this-inside-callback-javascript/>

编写 JavaScript 对新手和有经验的开发人员都是一种威胁，因为它对流行的编程概念进行了一些非正统的实现。本文讨论了两个棘手的概念联合起来挫败毫无戒心的程序员的场景:

1.  回收
2.  `this`(上下文)

这其中的每一个都可能是一场噩梦，但是当挑战是在回调中访问正确的`this`时，它变得更加棘手。在本文中，我们将弄清楚这一点，并看看如何显式地强制上下文绑定指向我们选择的对象。

为了让我们谨慎行事，我们必须[回顾一下什么是回调](https://blog.logrocket.com/evolution-async-programming-javascript/)。

## 什么是回调？

回调是作为参数传递给另一个函数的函数。通常，回调会在外部函数中的某个点被调用。

> **注意**:接受回调的外部函数称为高阶函数。

因为回调是一个函数，而函数是 JavaScript 中的对象，所以回调有自己的一套方法和属性。当在高阶函数中执行回调时，它会被赋予一个`this`属性，该属性完全取决于调用它的方式，而不是定义它的位置/方式/时间。

我们可以通过查看被调用的高阶函数来跟踪回调中的`this`值。回调中的`this`的大多数问题是由于封闭函数的实际定义可能具有局部范围的属性。然而，当使用回调中的`this`绑定访问该属性时，它并不存在，因为回调的上下文会根据它被调用的方式而动态变化。

> **Pro 提示**:当一个函数(回调)被调用时，JavaScript 解释器会创建一个执行记录(执行上下文)，这个上下文包含了函数的信息。其中一个是`this`引用，它在函数执行期间是可用的。

下面是一个回调函数的例子:

```
function HOF(callback){
  callback(); 
}

function callback(){
  console.log(this);
}

HOF(callback) // points to the global Window Object
```

在上面的例子中，我们有一个名为 HOF(高阶函数)的函数，它接收一个回调函数，将它的`this`值记录到控制台。

这是一个跟踪回调中的`this`值以查看它在哪里被调用的很好的例子，因为回调的上下文发生了变化，并且它的`this`值根据它在封闭函数中被调用的方式被重新分配。

> **注意**:在被封闭函数调用的回调中，`this`上下文发生变化。保存的值`this`被重新分配给调用该函数的函数——调用点。

在这种情况下，封闭函数`HOF`是在全局范围内定义和调用的，因此回调中的`this`绑定将指向`Window`对象。

> **注意**:`Window`对象是一个客户端对象，代表浏览器中一个打开的窗口。

让我们看看在不同场景下使用`this`值时的一些行为:

```
function bar() {
    console.log(this);
}

bar(); // points to the global Window Object
```

这很简单。`bar()`函数在全局范围内，所以它的`this`值将指向`Window`对象。但是，如果我们将同一个函数放入一个对象的方法中，我们会得到一个不同的绑定:

```
let sample = {bar: bar};

sample.bar(); // points to the object above
```

这段代码的输出将指向我们刚刚创建的`sample`对象。这或许是最期待也是最直观的绑定；我们倾向于期望`this`值指向点左边的对象，但是在 JavaScript 中并不总是这样。

最后，如果在`new`构造函数中使用:

```
new bar();
```

这段代码的输出将指向一个继承自`bar.prototype`的对象。

这一切都相当简单，直到我们遇到嵌套回调的情况，函数似乎应该有一个`this`绑定来引用其词法封闭函数，该函数拥有在创作时定义的所有属性。但是在这一点上，我们倾向于忽略一个事实，即函数的上下文绑定完全独立于它的词法声明，并且是由它如何被调用决定的。

当出现这种情况时，有几种方法可以解决由于在回调中无法访问正确的`this`而导致的错误。

## 在回调中访问正确的`this`的 3 种方法

### 1.使用箭头功能

[JavaScript 箭头函数](https://blog.logrocket.com/anomalies-in-javascript-arrow-functions/)在 ECMAScript 6 中引入。它们是传统函数表达式的紧凑替代，没有自己的`this`绑定。这确保了无论何时在箭头函数中使用对`this`的引用，它都像普通变量一样在范围内被查找。

让我们快速看一下这个[堆栈溢出问题](https://stackoverflow.com/questions/20279484/how-to-access-the-correct-this-inside-a-callback)，它是围绕回调中的`this`绑定:

```
function MyConstructor(data, transport) {
    this.data = data;
    transport.on('data', function () {
        console.log(this.data);
    });
}

// Mock transport object
let transport = {
    on: function(event, callback) {
        setTimeout(callback, 1000);
    }
};

// called as
let obj = new MyConstructor('foo', transport);
```

这是一个更棘手的场景，回调中的`this`绑定引用了`Window`对象，似乎很难跟踪和调试。当我们运行这段代码时，它输出`undefined`，但是我们可以通过将匿名函数表达式改为箭头函数来轻松解决这个问题。然后，代码变成:

```
[...]
    transport.on('data', () => {
        console.log(this.data);
    });
}
[...]
```

就是这样——就像在函数声明中改变几个字符一样简单，我们已经解决了`this`绑定问题。

### 2.创建另一个变量来存储`this`对象

大多数时候，当我们试图在回调中访问`this`时，我们真正想要访问的是它所指向的对象。实现这一点的一种方法是创建一个变量，并在回调范围之前存储`this`的值(尽管有些程序员不愿意这样做，因为这样看起来很混乱)。

我见过有人叫它`that`或者`self`，但是叫什么真的不重要，只要够直观就行。这种方法之所以有效，是因为变量遵循词法范围的规则，因此可以在回调中访问。这种方法的一个额外好处是，无论回调的动态`this`绑定是什么，您仍然可以访问。

下面是一个使用上面代码片段的示例:

```
function MyConstructor(data, transport) {
    this.data = data;
    let that = this;
    transport.on('data', function() {
        alert(that.data);
    });
}
```

这和之前的解决方案一样，解决了在回调中访问`this`的问题。

### 3.显式绑定`this`到一个对象

当我们定义回调时，我们可以明确地指定我们希望`this`是什么。使用`[bind()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)`方法，我们可以设置`this`值，并确保它在执行过程中保持不变，不管函数是在哪里被调用或传递的。

每个函数都有一个返回新函数的`bind()`方法，该函数的`this`属性被绑定到一个指定的对象。返回的函数将具有与原始函数完全相同的行为；唯一的区别是你可以完全控制`this`属性指向什么。

让我们以相同的代码片段为例:

```
function MyConstructor(data, transport) {
    this.data = data;
    let boundFunction = (function() { 
        alert(this.data);             
    }).bind(this); // we call bind with the `this` value of the enclosing function
    transport.on('data', boundFunction);
}
```

这就解决了问题，并使我们能够更好地控制回调的`this`绑定。

## 结论

我们已经对现代 JavaScript 中最棘手和最令人畏惧的两个概念进行了肤浅的探索。无论何时，当你在一个有回调的代码库中，并且它似乎正在访问错误的`this`时，试着在高阶函数中跟踪回调的执行，根据高阶函数是如何被调用的，找到它的`this`绑定可能是什么的线索。

如果失败或证明困难，记住你的技术在纠正这一威胁的武器库。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.