# 常见的 JavaScript“gotchas”-log rocket 博客

> 原文：<https://blog.logrocket.com/common-javascript-gotchas-638bdea2a224/>

自从我们克服了 [Harmony](https://en.wikipedia.org/wiki/ECMAScript#4th_Edition_%28abandoned%29) 以来，JavaScript 已经获得了许多新的、甜蜜的特性，虽然更多的特性可以让我们编写出可读的、高质量的代码，但我们也很容易过分追求新的、闪亮的东西，并陷入一些潜在的陷阱。

让我们回顾一下我经常看到的一些困惑的来源，不管是新的还是旧的。

### 箭头函数和对象文字

[Arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) 提供了一个更简洁的语法，其中一个可用的特性是你可以把你的函数写成一个 lambda 表达式，带有一个隐式返回值。这对于函数式代码来说很方便，比如当您必须使用函数来映射数组时。这将是相当多的具有常规功能的空行。

例如:

```
const numbers = [1, 2, 3, 4];
numbers.map(function(n) {
  return n * n;
});
```

成为一个简洁易读的带有 lambda 风格箭头函数的一行程序:

```
const numbers = [1, 2, 3, 4];
numbers.map(n => n * n);
```

这个 arrow 函数的用例将会像预期的那样工作，它将值与自身相乘，然后返回一个包含`[1, 4, 9, 16]`的新数组。

但是，如果您尝试映射到对象中，但是语法并不像人们直觉上预期的那样，例如，假设我们尝试将数字映射到包含如下值的对象数组中:

```
const numbers = [1, 2, 3, 4];
numbers.map(n => { value: n });
```

这里的结果实际上将是一个包含未定义值的数组。虽然看起来我们在这里返回了一个对象，但是解释器看到了完全不同的东西。花括号被解释为 arrow 函数的块作用域，value 语句实际上是一个标签。如果我们将上面的箭头函数外推至解释器实际执行的结果，它看起来会像这样:

```
const numbers = [1, 2, 3, 4];
numbers.map(function(n) {
  value:
  n
  return;
});
```

解决方法非常微妙，我们只需要将对象放在括号中，这样就可以将它变成一个表达式，而不是块语句，就像这样:

```
const numbers = [1, 2, 3, 4];
numbers.map(n => ({ value: n }));
```

将计算出一个数组，该数组包含一个对象数组，其值与预期值相同。

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)

### 箭头函数和绑定

关于箭头函数的另一个警告是，它们没有自己的`this`绑定，这意味着它们的`this`值将与封闭词法范围的`this`值相同。

因此，尽管语法被认为“更光滑”,但箭头函数不是好的“ol”函数的替代物。你很快会遇到这样的情况，你的`this`绑定不是你想象的那样。

例如:

```
let calculator = {
  value: 0,
  add: (values) => {
    this.value = values.reduce((a, v) => a + v, this.value);
  },
};
calculator.add([1, 2, 3]);
console.log(calculator.value);
```

虽然人们可能认为这里的`this`绑定是那里的 calculator 对象，但实际上它将导致`this`要么是未定义的，要么是全局对象，这取决于代码是否在严格模式下运行。这是因为这里最接近的词法范围是全局范围，在未定义的严格模式下，否则，它就是浏览器中的窗口对象(或者 Node.js 兼容环境中的进程对象)。

常规函数确实有一个`this`绑定，当在一个对象上被调用时，它将指向该对象，所以使用常规函数仍然是成员函数的方法。

```
let calculator = {
  value: 0,
  add(values) {
    this.value = values.reduce((a, v) => a + v, this.value);
  },
};
calculator.add([10, 10]);
console.log(calculator.value);
```

此外，由于 arrow 函数没有`this`绑定[function . prototype . call](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call),[function . prototype . bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)和 [Function.prototype.apply](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 也不能使用它们。当 arrow 函数被声明时,`this`绑定是固定的，不能改变。

因此，在下面的例子中，我们将遇到与前面相同的问题，当调用加法器的 add 函数时，`this`绑定是全局对象，尽管我们试图用 [Function.prototype.call](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) 覆盖它:

```
const adder = {
  add: (values) => {
    this.value = values.reduce((a, v) => a + v, this.value);
  },
};
let calculator = {
  value: 0
};
adder.call(calculator, [1, 2, 3]);
```

箭头函数很简洁，但是在需要绑定的地方，它们不能代替常规的成员函数。

### 自动分号插入

虽然这不是新特性，但自动分号插入(ASI)是 JavaScript 中比较奇怪的特性之一，所以值得一提。理论上，大多数情况下可以省略分号(许多项目就是这样做的)。如果这个项目有先例，你应该遵循它，但是，你需要意识到 ASI 是一个特性，否则你最终会得到具有欺骗性的代码。

举以下例子:

```
return
{
  value: 42
}
```

人们可能会认为它会返回对象文字，但实际上它会返回 undefined，因为分号的插入使它成为一个空的返回语句，后面跟着一个 block 语句和一个 label 语句。

换句话说，实际被解释的最终代码看起来更像下面这样:

```
return;
{
  value: 42
};
```

根据经验，即使使用分号，也不要以大括号、方括号或模板字符串开始一行，因为 ASI 总是出现。

### 浅集

集合是浅层的，这意味着具有相同值的重复数组和对象将导致集合中有多个条目。

例如:

```
let set = new Set();
set.add([1, 2, 3]);
set.add([1, 2, 3]);

console.log(set.length);
```

这个集合的大小是 2，如果你从引用的角度来考虑，这是有意义的，因为它们是不同的对象。

然而，字符串是不可变的，所以一个集合中的多个字符串如下:

```
let set = new Set();
set.add([1, 2, 3].join(','));
set.add([1, 2, 3].join(','));
console.log(set.size);
```

将最终得到一个大小为 1 的集合，因为字符串是不可变的，并且被固定在 JavaScript 中，如果您发现自己需要存储一组可以序列化和反序列化的对象，这可以作为一种变通方法。

### 类和时间死区

在 JavaScript 中，常规函数被提升到词法范围的顶部，这意味着下面的例子将会如人们所预期的那样工作:

```
let segment = new Segment();
 function Segment() {
  this.x = 0;
  this.y = 0;
}
```

但是对于类来说就不一样了，类实际上并没有被提升，在你试图使用它们之前，需要在词法范围内被完全定义。

例如:

```
let segment = new Segment();

class Segment {
  constructor() {
    this.x = 0;
    this.y = 0;
  }
}
```

将导致 ReferenceError，因为它们不像函数那样被提升。

### 最后

最后是一个有点特殊的例子，看看下面的片段:

```
try {
  return true;
} finally {
  return false;
}
```

你认为它会返回什么值？答案既直观，同时也可能变得不直观。人们可能认为第一个 return 语句使函数实际返回并弹出调用堆栈，但这是该规则的例外，因为 finally 语句总是运行的，所以 finally 块中的 return 语句改为返回。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 最后

JavaScript 容易学习，但很难掌握，换句话说，它容易出错，除非开发人员仔细了解他们在做什么和为什么要做什么。

对于 ECMAScript 6 来说尤其如此，尤其是它的甜蜜特性，尤其是箭头函数，一直都在出现。如果要我猜的话，我会说这是因为开发人员认为它们比常规函数更漂亮，但它们不是常规函数，也不能替代它们。

时不时的略读一下[说明书](https://www.ecma-international.org/ecma-262/9.0/index.html)也无妨。这不是世界上最令人兴奋的文档，但就规范而言，它还不错。

像 AST Explorer 这样的工具也有助于揭示一些正在发生的事情。在某些情况下，人类和计算机倾向于以不同的方式解析事物。

也就是说，我将把最后一个例子留给你们作为练习。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.