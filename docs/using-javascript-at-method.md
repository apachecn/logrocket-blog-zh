# 使用 JavaScript 的。at()方法

> 原文：<https://blog.logrocket.com/using-javascript-at-method/>

## JavaScript `.at()`方法是什么？

JavaScript `.at()`方法允许开发人员根据索引无缝地抓取元素。

在 JavaScript 中选择元素是开发中常见的事情，但是，在使用`.at()`方法之前，JavaScript 已经有了从列表的开头或结尾或者在一个字符串中选择元素或字符的方法和技术。

括号符号`[]`通常用于获取特定索引处的元素。但这也有缺点。例如，我们不能使用像`arr[-1]`这样的负索引语法来访问列表中的最后一项，这在 Python 中很流行。

然后，开发人员必须求助于使用`slice()`方法和`length`属性来从列表末尾抓取项目，但是这些方法也有各自的缺点。

在本教程中，我们将看看 JavaScript `.at()`方法，它的用例，以及与先前存在的方法相比，它如何改善开发人员的体验。

## 可索引对象原型

可索引对象是可以创建索引条目的对象。在 JavaScript 中，它们包括像`Array`、`String`和`TypedArray`这样的类。

位于这些可索引对象的原型上的`.at()`方法表示为:`Array.prototype.at()`、`String.prototype.at()`和`%TypedArray%.prototype.at()`。因此，我们可以直接在对象实例上调用它。例如，数组的语法如下:

```
array.at(index)
```

## 在`.at()`之前获取列表元素的现有方法

为了了解`.at()`方法的优势，我们将快速浏览一些现有的方法进行比较。这也将作为初学者的复习。

让我们考虑一个名为`arr`的元素数组:

```
const arr = [1, 2, "three", 4, 5, true, false];

```

通过在`arr`数组上使用括号符号`[]`，我们可以获得特定索引处的元素。例如，`arr[0]`返回第一个元素，`1`，依此类推。但是，为了从未知长度的末端获取一个项目，我们使用了`length`属性或`slice()`方法。

### 使用`length`属性

属性的语法是这样写的:

```
arr[arr.length - N];

```

这里，`N`等于从列表末尾开始的第 n 个元素，通过使用该语法，我们可以从列表末尾获取任何元素。

在下面的代码中，我们使用语法来获取`arr`数组的最后一个元素:

```
const arr = [1, 2, "three", 4, 5, true, false];
const lastItem = arr[arr.length - 1];
console.log(lastItem);  // Expected Output: false

```

这样做很好，但是对于一个简单的任务来说，语法可能不方便而且很乏味。此外，在处理函数的返回值时，它的一个缺点是迫使我们在应用语法之前首先将返回值存储在一个变量中:

```
function appendNumber(arr, N) {
  arr.push(N);
  return arr;
}

const tempArr = appendNumber([1, 2, "three", 4, 5, true, false], 6);
console.log(tempArr[tempArr.length - 1]); // Expected Output: 6

```

在上面的代码中，在应用`length`属性之前，`appendNumber()`函数的返回值首先存储在`tempArr`变量中。

### `slice()`法

开发人员还可以使用下面的语法使用`slice()`方法来获取列表的最后一项:

```
arr.slice(-1)[0]

```

这种语法允许负索引，您将在本教程后面的`.at()`方法中看到。

这里的负索引表示从数组末尾的偏移量。例如，`slice(-1)`从后面删除最后一项并返回一个新数组；`slice(-2)`删除最后两个，依此类推。

但是在这里，焦点是最后一项，因此在语法中是`slice(-1)`。然后，`[0]`符号选择该索引处的项目。

使用该语法，我们可以获取`arr`数组的最后一项，如下所示:

```
const arr = [1, 2, "three", 4, 5, true, false];

console.log(arr.slice(-1)[0]); // Expected Output: false

```

与上面的`length`属性不同，这个方法不强迫我们在使用语法之前存储函数的返回值。因此使其更加灵活:

```
function appendNumber(arr, N) {
  arr.push(N);
  return arr;
}

console.log(appendNumber([1, 2, "three", 4, 5, true, false], 6).slice(-1)[0]); // 6

```

语法看起来很奇怪，没有表达出它的意图。当然，这也很不方便。

### 为什么不用`arr[-1]`访问最后一个数组元素？

这个问题经常出现在 JavaScript 初学者身上，尤其是如果他们来自 Python 这样的编程语言。

JavaScript 中的`arr[-1]`符号是一个有效的对象属性。记住 JavaScript 中的一切，包括数组，都是对象。所以每当我们使用括号符号时，例如，`arr[0]`，我们用键`0`引用对象的属性。

通过重写对象符号中的`arr`数组，我们得到了这样的结果:

```
const arr = {
  0: 1,
  1: 2,
  2: "three",
  // ...
};

console.log(arr[0]); // Expected Output: 1

```

在上面的代码中，我们没有一个键`-1`。所以，`arr[-1]`返回值`undefined`。如果对象属性有一个键`-1`，如下面的代码所示，`arr[-1]`返回其相应的值:

```
const arr = {
  "-1": "valid"
};

console.log(arr[-1]); // Expected Output: valid

```

这意味着我们不能使用`arr[-1]`来获取最后一个元素，因为它已经是一个有效的语法了。为了使用负索引语法从列表末尾返回一个元素，我们将使用`.at()`方法。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 使用`.at()`语法

当使用`.at()`语法时，它接收要返回的项目的索引。当传递负索引时，它从列表或字符串的末尾开始计数，并返回找到的项或字符。否则，它返回`undefined`:

```
at(index)

```

## `.at()`实践中的方法

`.at()`方法接收要返回的项目的索引。在这一节中，我们将讨论它的用例。

让我们重温一下`arr`数组，看看`.at()`方法如何让我们无缝地返回一个索引元素:

```
const arr = [1, 2, "three", 4, 5, true, false];

console.log(arr.at(0)); // Expected Output: 1
console.log(arr.at(2)); // Expected Output: "three"
console.log(arr.at(-1)); // Expected Output: false
console.log(arr.at(-3)); // Expected Output: 5
```

```
console.log(arr.at(7)); // Expected Output: undefined

```

当一个正索引传递给`.at()`方法时，它返回该索引处的元素。对于负索引，它从列表中的最后一个元素开始倒数，并返回该元素。

在上面的代码中，`at(-1)`从数组末尾开始计数 1，并返回`false`，这是找到的元素。同理，`at(-3)`从末尾数三，返回`5`。但是，如果找不到`.at()`索引，它将返回一个未定义的值。

像数组一样，我们可以对字符串做同样的事情:

```
const str = "The last alphabet is z";

console.log(str.at(0)); // Expected Output: T
console.log(str.at(-1)); // Expected Output: z

```

正如我们所见，这种方法使用起来很愉快。仅仅通过`.at(-1)`，我们就获得了`str`字符串的最后一个字符。如果我们对`length`属性执行同样的任务，我们会有一个更长的语法，就像这样:

```
console.log(str[str.length - 1]); // Expected Output: z

```

### 处理函数的返回值

与`length`属性不同的是，`.at()`方法不会强迫我们在使用函数之前将返回值存储在变量中。

下面的代码输出推入数组的最后一个元素:

```
function appendNumber(arr, N) {
  arr.push(N);
  return arr;
}

console.log(appendNumber([1, 2, "three", 4, 5, true, false], 6).at(-1));
// Expected Output: 6

```

在代码中，`.at()`方法直接应用于返回值，而无需先将值存储在变量中。

### `.at()`方法接受带小数的数字

当带有小数的数字传递给`.at()`方法时，它会考虑小数点之前的值，并返回该索引处的项目。

让我们来看看下面的代码:

```
const arr = [1, 2, "three", 4, 5, true, false];
console.log(arr.at(0.6)); // Expected Output: 1
console.log(arr.at(-3.6)); // Expected Output: 5

```

在上面的代码中，第一个控制台输出位于`0`索引处的项目，而第二个控制台从数组末尾开始计数 3，并输出找到的项目。

当我们想要随机选择一个索引元素时，这是有益的。这可以用一个石头剪子布游戏项目来演示。我们可以使用`.at()`方法语法来确定计算机的随机选择。

下面的代码说明了我们如何应用`.at()`方法来随机选择计算机的选项:

```
const computerOptions = ["rock", "paper", "scissors"];
const randomIndex = Math.random() * computerOptions.length;

console.log(computerOptions.at(randomIndex));

```

![Rock-Paper-Scissors Generator Showing "Paper," "Rock,""Paper"](img/c5ff1b24a4350acddc8151a77c334fbc.png)

[在 CodeSandbox](https://codesandbox.io/s/wonderful-river-dttqe?file=/script.js) 上亲自尝试一下。

[用`Math.random()`，我们得到一个在`0`(含)和数组长度之间的浮点数](https://blog.logrocket.com/building-random-number-generator-javascript-nodejs/)。然后，当`.at()`方法接收这个浮点数作为索引时，它会考虑小数点之前的值，并返回该索引处的元素。

除非我们首先对随机数取整(即将该数四舍五入到最接近的整数)，否则不可能对同一任务使用方括号符号，如下面的代码所示:

```
const randomIndex = Math.floor(Math.random() * computerOptions.length);

console.log(computerOptions[randomIndex]);

```

这是因为括号符号为带小数的数字返回一个`undefined`值:

```
const arr = [1, 2, "three", 4, 5, true, false];
console.log(arr[0.6]); // Expected Output: undefined

```

`.at()`方法为我们节省了使用`Math.floor`对随机数取整的额外步骤。

### 浏览器对 JavaScript 的`.at()`方法的支持

现代浏览器广泛支持`.at()`方法。点击查看[浏览器支持。](https://caniuse.com/mdn-javascript_builtins_array_at)

![Browser Support For Using The At() Method](img/ee5e9aa8fa4053a14f6d867899485a08.png)

## 结论

正如我们在本教程中所看到的,`.at()`方法在根据索引抓取项目时非常有用。与先前存在的方法相比，它使用起来也很简洁。

如果你有任何问题或贡献，请在评论区分享。如果你喜欢这个教程，努力在网络上分享它。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.