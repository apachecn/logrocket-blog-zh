# JavaScript 箭头函数中的异常

> 原文：<https://blog.logrocket.com/anomalies-in-javascript-arrow-functions/>

## 介绍

就个人而言，我认为箭头函数是 ES6 规范中引入的 JavaScript 语言中最棒的语法添加之一——顺便提一下，这是我的观点。自从我知道它们以来，我几乎每天都在使用它们，我想大多数 JavaScript 开发人员也是如此。

和普通的 JavaScript 函数一样，箭头函数可以有多种用途。但是，它们通常用在需要匿名函数表达式的地方——例如，作为回调函数。

下面的例子展示了如何将一个箭头函数用作回调函数，特别是对于像`map()`、`filter()`、`reduce()`、`sort()`等数组方法。

```
const scores = [ */* ...some scores here... */* ];
const maxScore = Math.max(...scores);

*// Arrow Function as .map() callback*
scores.map(score => +(score / maxScore).toFixed(2));
```

乍一看，似乎可以用常规 JavaScript 函数的每一种方式来使用或定义箭头函数，但事实并非如此。出于很好的理由，箭头函数并不意味着与常规 JavaScript 函数的行为方式完全相同。也许箭头函数可以被认为是异常的 JavaScript 函数。

尽管 arrow 函数的语法非常简单，但这不是本文的重点。本文旨在揭示 arrow 函数与常规函数行为不同的主要方式，以及如何利用这些知识为开发人员带来优势。

**请注意:**在整篇文章中，我使用术语*常规函数*或*常规 JavaScript 函数*来指代使用 function 关键字定义的传统 JavaScript 函数语句或表达式。

## TL；速度三角形定位法(dead reckoning)

*   无论是在严格模式还是非严格模式下，箭头函数都不能有重复的命名参数。

*   箭头函数没有`arguments`绑定。但是，它们可以访问最近的非箭头父函数的 arguments 对象。很大程度上依赖于命名参数和 rest 参数来捕获传递给箭头函数的参数。

*   箭头函数永远不能用作构造函数。因此，不能用 new 关键字调用它们。因此，箭头函数不存在原型属性。

*   箭头函数中的值在函数的整个生命周期中保持不变，并且总是绑定到最近的非箭头父函数中的值。

## 命名函数参数

JavaScript 中的函数通常用命名参数来定义。命名参数用于根据位置将变量映射到函数范围内的局部变量。

考虑以下 JavaScript 函数:

```
function logParams (first, second, third) {
  console.log(first, second, third);
}

*// first => 'Hello'
// second => 'World'
// third => '!!!'*
logParams('Hello', 'World', '!!!'); *// "Hello"  "World"  "!!!"*

*// first => { o: 3 }
// second => [ 1, 2, 3 ]
// third => undefined*
logParams({ o: 3 }, [ 1, 2, 3 ]); *// {o: 3}  [1, 2, 3]*
```

`logParams()`函数由三个命名参数定义:`first`、`second`和`third`。命名参数被映射到根据位置调用函数时使用的参数。如果命名参数比传递给函数的参数多，那么剩下的参数就是`undefined`。

对于命名参数，常规 JavaScript 函数在非严格模式下表现出奇怪的行为。在非严格模式下，常规 JavaScript 函数允许重复的命名参数。以下代码片段显示了该行为的后果:

```
function logParams (first, second, first) {
  console.log(first, second);
}

*// first => 'Hello'
// second => 'World'
// first => '!!!'*
logParams('Hello', 'World', '!!!'); *// "!!!"  "World"*

*// first => { o: 3 }
// second => [ 1, 2, 3 ]
// first => undefined*
logParams({ o: 3 }, [ 1, 2, 3 ]); *// undefined  [1, 2, 3]*
```

我们可以看到，`first`参数是重复的；因此，它被映射到传递给函数调用的第三个参数的值，完全覆盖传递的第一个参数。这不是一个可取的行为。

好消息是这种行为在严格模式下是不允许的。在严格模式下定义带有重复参数的函数会抛出一个`Syntax Error`，表示不允许重复参数。

```
*// Throws an error because of duplicate parameters (Strict mode)*
function logParams (first, second, first) {
  "use strict";
  console.log(first, second);
}
```

### 箭头函数如何处理重复的参数？

这里有一些关于箭头函数的内容:

> *与常规函数不同，无论是在严格模式还是非严格模式下，箭头函数都不允许有重复的参数。重复的参数将导致抛出一个`Syntax Error`。*

```
*// Always throws a syntax error*
const logParams = (first, second, first) => {
  console.log(first, second);
}
```

## 函数重载

函数重载是定义一个函数的能力，这样就可以用不同的调用签名(形状或参数数量)来调用它。好的一面是 JavaScript 函数的参数绑定使得这成为可能。

首先，考虑这个非常简单的重载函数，它计算传递给它的任意数量的参数的平均值:

```
function average() {
 *// the number of arguments passed*  const length = arguments.length;

  if (length == 0) return 0;

 *// convert the arguments to a proper array of numbers*  const numbers = Array.prototype.slice.call(arguments);

 *// a reducer function to sum up array items*  const sumReduceFn = function (a, b) { return a + Number(b) };

 *// return the sum of array items divided by the number of items*  return numbers.reduce(sumReduceFn, 0) / length;
}
```

我试图让函数定义尽可能详细，这样就可以清楚地理解它的行为。可以使用从零到函数可以接受的最大参数数(应该是 255)的任意数量的参数来调用该函数。

下面是调用`average()`函数的一些结果:

```
average(); *// 0*
average('3o', 4, 5); *// NaN*
average('1', 2, '3', 4, '5', 6, 7, 8, 9, 10); *// 5.5*
average(1.75, 2.25, 3.5, 4.125, 5.875); *// 3.5*
```

现在尝试使用 arrow 函数语法复制`average()`函数。我是说，这能有多难？首先，你要做的就是:

```
const average = () => {
  const length = arguments.length;

  if (length == 0) return 0;

  const numbers = Array.prototype.slice.call(arguments);
  const sumReduceFn = function (a, b) { return a + Number(b) };

  return numbers.reduce(sumReduceFn, 0) / length;
}
```

当你现在测试这个函数的时候，你意识到它抛出了一个`Reference Error`，你猜怎么着？在所有可能的原因中，它在抱怨`arguments`没有被定义。

### 你错在哪里？

这里还有一些关于箭头函数的东西:

> *与常规函数不同，箭头函数不存在`arguments`绑定。但是，它们可以访问非箭头父函数的`arguments`对象。*

基于这种理解，您可以将`average()`函数修改为常规函数，它将返回立即调用的嵌套箭头函数的结果，该函数应该可以访问父函数的`arguments`。这将看起来像这样:

```
function average() {
  return (() => {
    const length = arguments.length;

    if (length == 0) return 0;

    const numbers = Array.prototype.slice.call(arguments);
    const sumReduceFn = function (a, b) { return a + Number(b) };

    return numbers.reduce(sumReduceFn, 0) / length;
  })();
}
```

很明显，这解决了没有定义`arguments`对象的问题。但是，您必须在常规函数中使用嵌套的箭头函数，对于这样一个简单的函数来说，这似乎是不必要的。

### 你能换一种方式做这件事吗？

既然访问`arguments`对象显然是这里的问题，那么有没有替代方法呢？答案是肯定的。向 ES6 休息参数问好。

使用 ES6 rest 参数，您可以获得一个数组，该数组允许您访问传递给函数的全部或部分参数。这适用于所有类型的函数，无论是常规函数还是箭头函数。它看起来是这样的:

```
const average = (...args) => {
  if (args.length == 0) return 0;
  const sumReduceFn = function (a, b) { return a + Number(b) };

  return args.reduce(sumReduceFn, 0) / args.length;
}
```

哇！rescue 的 Rest 参数——您最终得到了一个优雅的解决方案，将`average()`函数实现为一个箭头函数。

对于依赖 rest 参数来访问函数参数，有一些警告:

*   rest 参数与函数中的内部`arguments`对象不同。rest 参数是一个实际的函数参数，而`arguments`对象是一个绑定到函数范围的内部对象。

*   一个函数只能有一个 rest 参数，并且必须始终是最后一个参数。这意味着一个函数可以有命名参数和 rest 参数的组合。

*   rest 参数可能无法捕获函数的所有参数，尤其是当它与命名参数一起使用时。然而，当它是唯一的函数参数时，它捕获所有的函数参数。另一方面，函数的`arguments`对象总是捕获函数的所有参数。

*   rest 参数指向包含所有捕获的函数参数的数组对象，而`arguments`对象指向包含所有函数参数的类似数组的对象。

在继续之前，考虑另一个非常简单的重载函数，它将数字从一种基数转换成另一种基数。可以用一到三个参数调用该函数。但是，当使用两个或更少的参数调用它时，它会在实现中交换第二个和第三个函数参数。

下面是常规函数的样子:

```
function baseConvert (num, fromRadix = 10, toRadix = 10) {
  if (arguments.length < 3) {
    // swap variables using array destructuring
    [toRadix, fromRadix] = [fromRadix, toRadix];
  }
  return parseInt(num, fromRadix).toString(toRadix);
}
```

下面是对`baseConvert()`函数的一些调用:

```
*// num => 123, fromRadix => 10, toRadix => 10*
console.log(baseConvert(123)); // "123"

*// num => 255, fromRadix => 10, toRadix => 2*
console.log(baseConvert(255, 2)); // "11111111"

*// num => 'ff', fromRadix => 16, toRadix => 8*
console.log(baseConvert('ff', 16, 8)); // "377"
```

根据您对没有自己的`arguments`绑定的箭头函数的了解，您可以使用箭头函数语法重写`baseConvert()`函数，如下所示:

```
const baseConvert = (num, ...args) => {
 *// destructure the `args` array and
  // set the `fromRadix` and `toRadix` local variables*
  let [fromRadix = 10, toRadix = 10] = args;

  if (args.length < 2) {
    *// swap variables using array destructuring*
    [toRadix, fromRadix] = [fromRadix, toRadix];
  }

  return parseInt(num, fromRadix).toString(toRadix);
}
```

请注意，在前面的代码片段中，我使用了 ES6 数组析构语法来设置数组项中的局部变量以及交换变量。你可以通过阅读本指南来了解更多关于析构的知识:" [ES6 析构:完整指南](https://codeburst.io/es6-destructuring-the-complete-guide-7f842d08b98f)"

## 构造函数

可以用关键字`new`调用一个常规的 JavaScript 函数，对于这个函数来说，它就像一个创建新实例对象的类构造函数。

下面是一个将函数用作构造函数的简单示例:

```
function Square (length = 10) {
  this.length = parseInt(length) || 10;

  this.getArea = function() {
    return Math.pow(this.length, 2);
  }

  this.getPerimeter = function() {
    return 4 * this.length;
  }
}

const square = new Square();

console.log(square.length); *// 10*
console.log(square.getArea()); *// 100*
console.log(square.getPerimeter()); *// 40*

console.log(typeof square); *// "object"*
console.log(square instanceof Square); *// true*
```

当用关键字`new`调用一个常规的 JavaScript 函数时，调用函数的内部方法`[[Construct]]`来创建一个新的实例对象并分配内存。之后，正常执行函数体，将`this`映射到新创建的实例对象。最后，函数隐式返回`this`(新创建的实例对象)，除非在函数定义中指定了不同的返回值。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

同样，所有常规的 JavaScript 函数都有一个`prototype`属性。函数的`prototype`属性是一个包含属性和方法的对象，当用作构造函数时，这些属性和方法由函数创建的所有实例对象共享。

最初，`prototype`属性是一个空对象，带有一个指向函数的`constructor`属性。但是，可以使用属性和方法对其进行扩充，以向使用函数作为构造函数创建的对象添加更多功能。

这里是对前面的`Square`函数的一个小修改，它在函数的原型而不是构造函数本身上定义方法。

```
function Square (length = 10) {
  this.length = parseInt(length) || 10;
}

Square.prototype.getArea = function() {
  return Math.pow(this.length, 2);
}

Square.prototype.getPerimeter = function() {
  return 4 * this.length;
}

const square = new Square();

console.log(square.length); *// 10*
console.log(square.getArea()); *// 100*
console.log(square.getPerimeter()); *// 40*

console.log(typeof square); *// "object"*
console.log(square instanceof Square); *// true*
```

正如你所看到的，一切仍然如预期的那样工作。事实上，这里有一个小秘密:ES6 类在后台做一些类似于上面代码片段的事情——它们只是简单的语法糖。

### 那么箭头函数呢？

它们也和普通的 JavaScript 函数一样有这种行为吗？答案是否定的。这里，再一次，是关于箭头函数的其他东西:

> *与常规函数不同，箭头函数永远不能用 new 关键字调用，因为它们没有`[[Construct]]`方法。因此，箭头函数也不存在`prototype`属性。*

可悲的是，这是千真万确的。箭头函数不能用作构造函数。不能用关键字`new`调用它们。这样做会抛出一个错误，指示该函数不是构造函数。

因此，像`new.target`这样存在于可作为构造函数调用的函数内部的绑定，对于箭头函数来说是不存在的；相反，它们使用最近的非箭头父函数的`new.target`值。

此外，因为箭头函数不能用关键字`new`调用，所以它们真的不需要有原型。因此，箭头函数不存在`prototype`属性。

由于 arrow 函数的`prototype`是`undefined`，试图用属性和方法来增加它，或者访问它的属性，将会抛出一个错误。

```
const Square = (length = 10) => {
  this.length = parseInt(length) || 10;
}

*// throws an error*
const square = new Square(5);

*// throws an error*
Square.prototype.getArea = function() {
  return Math.pow(this.length, 2);
}

console.log(Square.prototype); *// undefined*
```

## 什么是`this`？

如果您编写 JavaScript 程序已经有一段时间了，那么您会注意到 JavaScript 函数的每次调用都与一个调用上下文相关联，这取决于调用函数的方式或位置。

函数中`this`的值在很大程度上取决于调用时函数的调用上下文，这通常会让 JavaScript 开发人员不得不问自己一个著名的问题:`this`的值是多少？

下面是对不同类型的函数调用的`this`值的总结:

*   用`new`关键字调用的**:**`this`指向函数内部`[[Construct]]`方法创建的新实例对象。`this`(新创建的实例对象)通常默认返回，除非在函数定义中明确指定了不同的返回值。

*   **不使用`new`关键字**直接调用:在非严格模式下，`this`指向 JavaScript 宿主环境的全局对象(在 web 浏览器中，这通常是`window`对象)。但是在严格模式下，`this`的值是`undefined`；因此，试图访问或设置`this`上的属性将会抛出一个错误。

*   **通过绑定对象**间接调用:`Function.prototype`对象提供了三种方法，使得函数在被调用时可以绑定到任意对象，即:`call()`、`apply()`和`bind()`。当使用这些方法调用函数时，`this`指向指定的绑定对象。

*   **作为对象调用方法** : `this`指向在其上调用函数(方法)的对象，而不管该方法是被定义为对象的自身属性还是从对象的原型链中解析出来的。

*   **作为事件处理程序**调用:对于用作 DOM 事件监听器的常规 JavaScript 函数，`this`指向触发事件的目标对象、DOM 元素、`document`或`window`。

首先，考虑这个非常简单的 JavaScript 函数，它将被用作一个点击事件监听器，比如一个表单提交按钮:

```
function processFormData (evt) {
  evt.preventDefault();

  *// get the parent form of the submit button*
  const form = this.closest('form');

  *// extract the form data, action and method*
  const data = new FormData(form);
  const { action: url, method } = form;

  *// send the form data to the server via some AJAX request
  // you can use Fetch API or jQuery Ajax or native XHR*
}

button.addEventListener('click', processFormData, false);
```

如果您尝试这段代码，您将看到一切都正常工作。事件侦听器函数中的值`this`，就像您之前看到的，是触发 click 事件的 DOM 元素，在本例中是`button`。

因此，可以使用以下命令指向 submit 按钮的父表单:

```
this.closest('form');
```

目前，您使用常规的 JavaScript 函数作为事件监听器。如果您将函数定义更改为使用全新的 arrow 函数语法，会发生什么情况？

```
const processFormData = (evt) => {
  evt.preventDefault();

  const form = this.closest('form');
  const data = new FormData(form);
  const { action: url, method } = form;

  *// send the form data to the server via some AJAX request
  // you can use Fetch API or jQuery Ajax or native XHR*
}

button.addEventListener('click', processFormData, false);
```

如果你现在尝试这样做，你会发现你得到一个错误。从表面上看，似乎`this`的值不是你所期望的。出于某种原因，`this`不再指向`button`元素，而是指向全局`window`对象。

### 如何修复`this`绑定？

你还记得`Function.prototype.bind()`吗？当您为提交按钮设置事件监听器时，您可以使用它来强制将`this`的值绑定到`button`元素。这是:

```
*// Bind the event listener function (`processFormData`) to the `button` element*
button.addEventListener('click', processFormData.bind(button), false);
```

哎呀！这似乎不是你想要的解决办法。`this`仍然指向全局`window`对象。这是箭头函数特有的问题吗？这是否意味着箭头函数不能用于依赖于`this`的事件处理程序？

### 你错在哪里？

这是我们要讲的关于箭头函数的最后一件事:

> *与常规函数不同，箭头函数没有自己的`this`绑定。`this`的值被解析为最近的非箭头父函数或全局对象的值。*

这就解释了为什么事件监听器箭头函数中`this`的值指向窗口对象(全局对象)。因为它没有嵌套在父函数中，所以它使用最近的父作用域(即全局作用域)中的 this 值。

然而，这并不能解释为什么不能使用`bind()`将事件监听器 arrow 函数绑定到`button`元素。对此有一个解释:

> *与常规函数不同，arrow 函数中的`this`值保持不变，并且在整个生命周期中不会改变，无论调用上下文如何。*

箭头函数的这种行为使得 JavaScript 引擎可以优化它们，因为函数绑定可以预先确定。

考虑一个稍微不同的场景，其中事件处理程序是使用对象的方法中的常规函数定义的，并且还依赖于同一对象的另一个方法:

```
({
  _sortByFileSize: function (filelist) {
    const files = Array.from(filelist).sort(function (a, b) {
      return a.size - b.size;
    });

    return files.map(function (file) {
      return file.name;
    });
  },

  init: function (input) {
    input.addEventListener('change', function (evt) {
      const files = evt.target.files;
      console.log(this._sortByFileSize(files));
    }, false);
  }

}).init(document.getElementById('file-input'));
```

这里有一个带有一个`_sortByFileSize()`方法和一个`init()`方法的一次性对象文字，它会被立即调用。`init()`方法获取一个 file `input`元素，并为 input 元素设置一个 change 事件处理程序，该处理程序根据文件大小对上传的文件进行排序，并将它们记录在浏览器的控制台上。

如果您测试这段代码，您会发现当您选择要上传的文件时，文件列表不会被排序并记录到控制台；相反，控制台上会抛出一个错误。问题来自这一行:

```
console.log(this._sortByFileSize(files));
```

在事件监听器函数内部，`this`指向触发事件的 DOM 元素，在本例中是`input`元素；因此`this._sortByFileSize`是未定义的。

为了解决这个问题，您需要将事件监听器内部的`this`绑定到包含方法的外部对象，以便您能够调用`this._sortByFileSize()`。在这里，你可以使用`bind()`如下:

```
init: function (input) {
  input.addEventListener('change', (function (evt) {
    const files = evt.target.files;
    console.log(this._sortByFileSize(files));
  }).bind(this), false);
}
```

现在一切都按预期运行。这里不使用`bind()`,您可以简单地用箭头函数替换事件监听器常规函数。箭头函数将使用来自父`init()`方法的`this`值，这将是必需的对象。

```
init: function (input) {
  input.addEventListener('change', evt => {
    const files = evt.target.files;
    console.log(this._sortByFileSize(files));
  }, false);
}
```

在继续之前，再考虑一个场景。假设您有一个简单的计时器函数，可以作为构造函数调用来创建以秒为单位的倒计时计时器。它使用`setInterval()`继续递减计数，直到持续时间过去或间隔被清除。这是:

```
function Timer (seconds = 60) {
  this.seconds = parseInt(seconds) || 60;
  console.log(this.seconds);

  this.interval = setInterval(function () {
    console.log(--this.seconds);

    if (this.seconds == 0) {
      this.interval && clearInterval(this.interval);
    }
  }, 1000);
}

const timer = new Timer(30);
```

如果你运行这段代码，你会发现倒计时器好像坏了。它一直在控制台上记录`NaN`。

这里的问题是在传递给`setInterval()`的回调函数内部，`this`指向的是全局的`window`对象，而不是在`Timer()`函数范围内新创建的`instance`对象。因此，`this.seconds`和`this.interval`都是`undefined`。

和前面一样，要解决这个问题，您可以使用`bind()`将`setInterval()`回调函数中`this`的值绑定到新创建的实例对象，如下所示:

```
function Timer (seconds = 60) {
  this.seconds = parseInt(seconds) || 60;
  console.log(this.seconds);

  this.interval = setInterval((function () {
    console.log(--this.seconds);

    if (this.seconds == 0) {
      this.interval && clearInterval(this.interval);
    }
  }).bind(this), 1000);
}
```

或者，更好的方法是，您可以用一个箭头函数替换`setInterval()`回调常规函数，这样它就可以使用最近的非箭头父函数`this`的值，在本例中是`Timer`。

```
function Timer (seconds = 60) {
  this.seconds = parseInt(seconds) || 60;
  console.log(this.seconds);

  this.interval = setInterval(() => {
    console.log(--this.seconds);

    if (this.seconds == 0) {
      this.interval && clearInterval(this.interval);
    }
  }, 1000);
}
```

现在您已经完全理解了 arrow 函数如何处理`this`关键字，需要注意的是 arrow 函数对于需要保留`this`的值的情况并不理想——例如，当定义需要引用对象的对象方法或者用需要引用目标对象的方法扩充函数的原型时。

## 不存在的绑定

在本文中，您已经看到了一些绑定，它们在常规 JavaScript 函数中可用，但在 arrow 函数中不存在。相反，箭头函数从其最近的非箭头父函数中导出此类绑定的值。

总之，下面是 arrow 函数中不存在的绑定列表:

*   **`arguments`** :调用时传递给函数的参数列表
*   **`new.target`** :用`new`关键字作为构造函数调用的函数的引用
*   **`super`** :对函数所属对象的原型的引用，前提是将其定义为简洁的对象方法
*   **`this`** :对函数调用上下文对象的引用

## 结论

嘿，我真的很高兴尽管读了很长时间，你还是读完了这篇文章，我强烈希望你在阅读的时候学到了一两件事。谢谢你的时间。

JavaScript 箭头函数真的很棒，并且具有这些很酷的特性(我们在本文中已经讨论过了),这使得 JavaScript 工程师可以很容易地对它们进行优化，而对于普通的 JavaScript 函数来说这是不可能的。

在我看来，我会说你应该尽可能多地使用箭头函数——除非在你不能使用的情况下。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.