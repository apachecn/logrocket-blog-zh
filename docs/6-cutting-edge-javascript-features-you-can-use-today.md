# 你今天可以使用的 6 个前沿 JavaScript 特性

> 原文：<https://blog.logrocket.com/6-cutting-edge-javascript-features-you-can-use-today/>

对于一名 JavaScript 程序员来说，这是一个激动人心的时刻。Web 技术正以更快的速度向前发展，浏览器供应商不再羞于立即实现新的创新功能。开发中的这种转变意味着程序员需要不断更新他们的技能，以保持他们角色的竞争力。

在本文中，我们将看看现代浏览器最近实现的六个 ES2020 和 ES2021 特性，并了解它们如何帮助 JavaScript 开发人员编写更少错误、更高效的代码。

## `BigInt`

在 JavaScript 中处理大整数时，我们经常不得不使用第三方库，因为`Number`类型无法安全地表示大于 2 ^(53) 的整数值。

考虑下面的例子:

```
console.log(9999999999999999);    // => 10000000000000000
```

在这段代码中，`9999999999999999`被舍入到`10000000000000000`，因为它大于`Number`类型支持的最大整数。如果不小心，这种舍入会危及程序的安全性。

这是另一个例子:

```
// notice the last digit
9800000000000007 === 9800000000000008;    // => true
```

幸运的是，ECMAScript 最近引入了`BigInt`数据类型，它提供了一种简单的方法来表示大于`Number`所支持范围的整数。

可以通过将`n`加到整数的上来创建一个`BigInt`。

比较:

```
console.log(9800000000000007n);    // => 9800000000000007n
console.log(9800000000000007);     // => 9800000000000008
```

也可以使用构造函数:

```
BigInt('9800000000000007');    // => 9800000000000007n
```

现在，您可以在标准 JavaScript 中对大整数执行算术运算，而不必使用变通方法:

```
9999999999999999 * 3;      // => 30000000000000000

// with BigInt, integer overflow won’t be an issue
9999999999999999n * 3n;    // => 29999999999999997n
```

理解`Number`和`BigInt`是两种不同的数据类型很重要，不能用严格的相等运算符来比较它们:

```
5n === 5;     // => false

typeof 5n;    // => bigint
typeof 5;     // => number
```

但是，您仍然可以使用相等运算符，因为它会在比较之前将操作数隐式转换为相同的类型:

```
5n == 5; // => true
```

您可以像对`Number` s 一样对`BigInt` s 执行算术运算:

```
50n + 30n;    // => 80n
50n - 30n;    // => 20n
50n * 20n;    // => 1000n
50n / 5n;     // => 10n
56n % 10n;    // => 6n
50n ** 4n;    // => 6250000n
```

递增、递减和一元求反运算符也能按预期工作。但是，一元加号(`+`)操作符是一个例外，将它应用到`BigInt`会导致一个`TypeError`:

```
let x = 50n;
++x;    // => 51n
--x;    // => 50n

-50n;    // => -50n
+50n;    // => TypeError: Cannot convert a BigInt value to a number
```

## 看涨凝聚算子

ES2020 为 JavaScript 语言添加了另一个短路操作符:nullish 合并(`??`)操作符。该操作符不同于现有的短路操作符，它检查其左操作数是否为 nullish ( `null`或`undefined`)而不是 falsy。

换句话说，`??`只有在其左操作数为`null`或`undefined`时才返回其右操作数:

```
null ?? 2; // => 2
undefined ?? 2; // => 2

0 ?? 2; // => 0
false ?? true; // => false
```

另一方面，如果左边的操作数是`0`、`-0`、`0n`、`false`、`""`(空字符串)、`null`、`undefined`或`NaN`，则逻辑 OR ( `||`)运算符返回其右边的操作数。比较:

```
null || 2;       // => 2
undefined || 2;  // => 2

0 || 2;           // => 2
false || true;    // => true
```

`??`在为属性或变量设置默认值时特别方便。例如:

```
function Config(darkMode)  {
    this.darkMode = darkMode ?? true;
    // …
}

new Config(null);     // => {darkMode: true}
new Config();         // => {darkMode: true}
new Config(false);    // => {darkMode: false}
```

在给定值为 null 或没有给定值的情况下，`Config`构造函数为`darkMode`属性提供默认值。

`??`在使用 DOM APIs 时也很有用:

```
// querySelector() returns null when the element doesn’t exist in the document
const elem = document.querySelector('elem') ?? document.createElement('elem');
```

请记住，在表达式中使用`??`和其他短路操作符时，必须用括号表示求值顺序，否则代码会抛出错误。

括号还有助于提高代码的可读性:

```
false || (true ?? true);   // no error
false || true ?? true;     // => SyntaxError
```

## `Promise.any()`

ES2015 用两种方法引入了 promise 对象:`Promise.all()`和`Promise.race()`。ES2021 通过添加`Promise.any()`进一步增强了 JavaScript 异步能力。这个新方法返回一个承诺，当给定 iterable 中的一个承诺实现时，该承诺实现；如果所有承诺都拒绝，则返回拒绝。

下面是它的工作原理:

```
const promise = Promise.any([
    Promise.reject('Error'),
    fetch('https://www.google.com/images/branding/googlelogo/2x/googlelogo_color_92x30dp.png', {mode: 'no-cors'}).then(() => 'google.com'),
    fetch('https://en.wikipedia.org/static/images/project-logos/enwiki.png', {mode: 'no-cors'}).then(() => 'wikipedia.org'),
    fetch('https://s.w.org/images/home/swag_col-1.jpg?1', {mode: 'no-cors'}).then(() => 'w.org')
]);

promise.then((fastest) => {
    // the first promise that fulfills
    console.log(fastest);
}).catch((error) => {
    console.log(error);
});
```

这段代码执行三个获取请求。一旦一个承诺被实现，它就返回一个实现了该承诺的值的承诺。`Promise.any()`与`Promise.race()`的不同之处在于它如何处理拒绝。只有当 iterable 中的所有承诺都被拒绝时，`Promise.any()`返回的承诺才会被拒绝:

```
const promise = Promise.any([
    Promise.reject('Exception1'),
    Promise.reject('Exception2'),
    Promise.reject('Exception3')
]);

promise.then((response) => {
    // ...
}).catch((e) => {
    console.log(e.errors);
});

// logs:
// => ["Exception1", "Exception2", "Exception3"]
```

注意所有承诺的拒绝值是如何作为数组传递给`catch()`方法的。或者，您可以使用`async`和`await`来处理结果:

```
(async () => {
    try {
        result = await Promise.any([
            Promise.reject('Exception1'),
            Promise.reject('Exception2'),
            Promise.resolve('Success!')
        ]);

        console.log(result);
    } catch(e) {
        console.log(e);
    }
})();

// logs:
// => Success!
```

## `Promise.allSettled()`

最近添加到 promise 对象中的另一个有用的方法是`Promise.allSettled()`。这个方法是对现有的`Promise.all()`方法的补充，旨在返回所有承诺的结果——无论是拒绝还是履行。

这里有一个例子:

```
const p1 = Promise.resolve('Success');
const p2 = Promise.reject('Exception');
const p3 = Promise.resolve(123);

Promise.allSettled([p1, p2, p3]).then((response) => {
    response.forEach(result => console.log(result.value || result.reason))
});

// logs:
// => Success
// => Error!
// => 123
```

注意所有承诺的结果是如何作为数组传递给`then()`的。在`then()`内部，一个`forEach()`方法在数组的元素上循环。如果`||`操作符的左操作数不是`undefined`，它将被记录到控制台。否则，承诺已被拒绝，并且将记录正确的操作数。

相比之下，`Promise.all()`承诺人一拒绝，马上拒绝。

## 可选链接运算符

可选的链接操作符(`?.`)允许您访问嵌套的属性，而无需验证链中的每个属性。

考虑下面的例子:

```
const obj = {};
const nickname = obj?.user?.profile?.nickname;

console.log(nickname);    // => undefined
```

这段代码试图将嵌套属性的值赋给一个常数。但是，在`obj`中没有这样的属性。另外，`user`和`profile`不存在。但是由于可选的链接操作符，代码返回`undefined`而不是抛出一个错误。

使用常规的链接操作符，您会得到一个错误:

```
const obj = {};
const nickname = obj.user.profile.nickname;

console.log(nickname);    // => TypeError
```

调用对象的方法时，也可以使用可选的链接运算符:

```
const obj = {};
const value = obj.myMethod?.();

console.log(value);    // => undefined
```

这里，`myMethod`在`obj`中不存在；然而，因为它是使用可选的链接操作符调用的，所以返回值是`undefined`。同样，使用常规的链接操作符，您会得到一个错误。

但是如果你想动态地访问一个属性呢？`?.[]`标记允许您使用括号符号引用变量。

它是这样工作的:

```
const obj = {
    user: {
      id: 123
    }
};

const prop = 'nickname';
const nickname = obj?.user?.profile?.[prop];

console.log(nickname);    // => undefined
```

## `globalThis`

尽管创建 JavaScript 的目的是为了在 web 浏览器中执行复杂的功能，但它现在可以在完全不同的环境中运行，如服务器、智能手机甚至机器人。因为每个环境都有自己的对象模型，所以您需要使用不同的语法来访问全局对象。

在浏览器环境中，您可以使用`window`、`frames`或`self`。在 Web Workers 中，你可以使用`self`。在 Node 中，你可以使用`global`。这种差异使得 web 开发人员更难编写可移植的 JavaScript 程序。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`globalThis`在所有环境中提供单一通用属性来访问全局对象:

```
// browser environment
console.log(globalThis);    // => Window {...}

// web worker environment
console.log(globalThis);    // => DedicatedWorkerGlobalScope {...}

// node environment
console.log(globalThis);    // => Object [global] {...}
```

以前，开发人员必须编写额外的检查，以确保他们引用正确的属性。有了`globalThis`，这就不再需要了，代码将在窗口和非窗口上下文中工作。请记住，您可能仍然需要使用 polyfill 来向后兼容旧版本的浏览器。

## 结论

JavaScript 正在快速发展，有趣的新特性不时被添加到语言中。在本文中，我们研究了六个新的 JavaScript 特性，包括`BigInt`、nullish 合并操作符、`Promise.any()`、`Promise.allSettled()`、可选的链接操作符和`globalThis`。

`BigInt`允许表示大的整数值。nullish 合并运算符为 JavaScript 带来了一个新的短路运算符。`Promise.any()`和`Promise.allSettled()`允许进一步控制异步操作。可选的链接操作符简化了对嵌套属性的访问。并且`globalThis`在所有环境中提供单一的通用属性来访问全局对象。

要获得最新功能的更新，请查看已完成提案的列表。如果你有任何问题，欢迎在评论中提问，我也在[推特](https://twitter.com/FarazKelhini)上。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.