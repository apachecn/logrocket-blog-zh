# JavaScript ES6: 5 改进您的代码日志博客的新抽象

> 原文：<https://blog.logrocket.com/javascript-es6-5-new-abstractions-to-improve-your-code-54a369e82407/>

#### 利用强大的 ES6 特性编写更好、更优雅、更可预测的 JavaScript。

![](img/27268f7348e26bf6469661470c4fe7a8.png)

JavaScript 是一种非常强大的编程语言，可以在各种平台上运行，尤其是随着像 *Node.js* 这样的 JavaScript 运行时的出现。不同类别和水平的程序员越来越多地采用这种语言。

和大多数事情一样，自语言诞生以来，它的各种版本都有不少变化。然而，该语言的 ES6 规范(*通常称为 ES2015* )增加了许多语法改进和新功能。这使得编写 JavaScript 程序更高效，更少出错，而且更有趣。

这些新特性和语法改进包括:*类*，*模块*，*承诺*，*模板文字*，*析构*，*箭头函数*，*生成器*，*集合和映射*，*符号*，以及*类型化数组*，*代理*，

在本文中，我们将探索 ES6 的五个特性，并考虑如何利用它们来改进 JavaScript 代码。以下是感兴趣的特性:

1.  *模板文字*
2.  *默认和休息参数*
3.  *箭头功能*
4.  *解构*
5.  *类*

* * *

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)

### 1.模板文字

在 ES6 中，引入了模板文字来处理一些与格式化和表示字符串相关的挑战。使用模板文字，您可以轻松地创建多行字符串。它还可以执行增强的字符串替换，并对看似危险的字符串(如要嵌入到 HTML 中的字符串)进行适当的格式化。

在 ES6 之前，字符串由一对*单引号* ( `‘string’`)或一对*双引号* ( `“string”`)分隔。在 ES6 中，字符串也可以用一对*反勾* ( ``string``)来分隔。这样的字符串被称为**模板文字**。

正如单引号和双引号分隔符一样，如果字符串包含反勾号字符，反勾号也可以在模板文本中转义。要对模板文本中的反勾号字符进行转义，必须在反勾号字符之前放置一个反斜杠(`)。但是请注意，在模板文本中，单引号和双引号不需要转义。`

 `这里有一个简单的例子:

```
const greeting = `Good morning!`;
const shortcut = ``cmd` + `shift` + `G``;

console.log(greeting); // "Good morning!"
console.log(shortcut); // "`cmd` + `shift` + `G`"
```

以这种方式使用模板文字与使用由引号分隔的常规 JavaScript 字符串没有什么不同。当处理*多行字符串*、*字符串替换、*和*标记模板*时，我们开始获得真正的优势。

#### 多行字符串

在 ES6 之前，JavaScript 中的字符串被限制为一行。然而，在开始新行之前用反斜杠(`)结束一行使得创建看似多行的字符串成为可能，即使新行不在字符串中输出:`

 ````
const message = "Hello Glad, 
Your meeting is scheduled for noon today.";

console.log(message);
// Hello Glad, Your meeting is scheduled for noon today.
```

如果你想在字符串中输出一个换行符，你需要在换行符前使用换行符转义序列(`n`):

```
const message = "Hello Glad,n
Your meeting is scheduled for noon today.";

console.log(message);
// Hello Glad,
// Your meeting is scheduled for noon today.
```

> 使用 ES6 模板文本，字符串输出时格式保持不变。

字符串中的所有换行符和空格都被保留，这使得无需任何附加语法就可以轻松创建多行字符串。然而，由于保留了空白，所以在缩进字符串时应该小心。

考虑这个例子:

```
const html = (`
<html>
  <body>
    Template literals are super cool.
  </body>
</html>
`).trim();

console.log(html);
// <html>
//   <body>
//     Template literals are super cool.
//   </body>
// </html>
```

请注意，新行和缩进保留在字符串中。`trim()`方法也用于删除 html 字符串开头和结尾的任何换行符和空格。

#### 字符串替换

模板文字也使字符串替换变得有趣。在 ES6 之前，*字符串连接*严重依赖于创建动态字符串。

这里有一个简单的例子:

```
const price = 24.99;

console.log("The item costs $" + price + " on the online store.");
// The item costs $24.99 on the online store.
```

使用 ES6 模板文字，可以按如下方式进行替换:

```
const price = 24.99;

console.log(`The item costs $${price} on the online store.`);
// The item costs $24.99 on the online store.
```

字符串替换由开始的`${`和结束的`}`分隔，并且可以在两者之间包含任何有效的 JavaScript 表达式。

在前面的例子中，我们将一个简单变量的值代入模板文本。假设我们想在商店所有商品的价格上增加 10%的折扣。

它看起来是这样的:

```
const price = 24.99;
const discount = 10;

console.log(`The item costs $${(price * (100 - discount) / 100).toFixed(2)} on the online store.`);
// The item costs $22.49 on the online store.
```

这里，我们用一个 JavaScript 表达式的值来替代计算折扣价的值。

> 模板文字本身就是 JavaScript 表达式，因此可以嵌套在其他模板文字中。

#### 模板标签

有了带标签的模板，您甚至可以更好地控制模板文字的替换和转换。一个*模板标签*仅仅是一个定义模板文字应该如何转换的函数。

模板标签函数可以接受多个参数。第一个参数是包含模板文本中所有文本字符串的数组。其余的参数对应于模板文本中的替换。因此，第二个参数对应于第一个替换，第三个参数对应于第二个替换，依此类推。

这里有一个简单的例子。给定以下模板文字:

```
`The price of ${quantity} units of the item on the online store is $${quantity * price}.`
```

为此模板文本传递给模板标记的第一个参数将是文本字符串数组，如下所示:

```
[
  'The price of ',
  ' units of the item on the online store is $',
  '.'
]
```

第二个参数将是`quantity`的值，第三个参数将是`(quantity * price)`的值。

让我们继续创建一个名为`pricing`的模板标签，我们可以用它来转换定价摘要。这将确保价格值四舍五入到小数点后两位。它还将确保在任何价格转换成`USD`之前的`$`货币符号。

下面是函数:

```
function pricing(literals, ...replacements) {
  // Initialize the final string
  let finalString = '';

  for (let i = 0; i < replacements.length; i++) {
    // Get the current literal and replacement
    const literal = literals[i];
    const replacement = replacements[i];

    // Trim trailing whitespaces from the current literal
    const trimmed = literal.trimRight();
    const length = trimmed.length;

    // Check if current replacement is a number
    const isNumber = typeof replacement === 'number';

    // Check if current literal string ends with $
    const isPrice = /$$/.test(trimmed);

    // Check if number is followed by literal that ends with $
    // and use the desired formatting
    finalString += (isNumber && isPrice)
      ? `${trimmed.substr(0, length - 1).trimRight()} USD ${replacement.toFixed(2)}`
      : `${literal}${replacement}`;
  }

  // Attach the last literal to the final string
  return finalString + literals[literals.length - 1];
}
```

您会注意到，在这段代码中，我们使用了一个名为`replacements`的 rest 参数来捕获模板文本中的所有替换。*我们将在下一节*中了解更多关于休息参数的信息。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

现在我们已经创建了一个模板标签，使用它是最简单的部分。

> 要使用模板标签，只需在模板文字的第一个反勾号(```)分隔符之前附加模板标签的名称。

下面是一个使用我们刚刚创建的`pricing`模板标签的例子:

```
const price = 24.99;
const discount = 10;
const quantity = 4;

const totalPrice = quantity * price * (100 - discount) / 100;

// WITHOUT TEMPLATE TAG
console.log(`The price of ${quantity} units of the item on the online store is $${totalPrice}.`);
// The price of 4 units of the item on the online store is $89.964.

// WITH TEMPLATE TAG (pricing)
console.log(pricing`The price of ${quantity} units of the item on the online store is $${totalPrice}.`);
// The price of 4 units of the item on the online store is USD 89.96.
```

* * *

### 2.默认和静止参数

JavaScript 中的函数是非常重要的对象。很有可能你遇到过这样的说法:

> “职能是一等公民”。

JavaScript 函数也是如此，因为你可以在你的程序中传递它们，就像你传递其他常规值一样。

然而，JavaScript 函数在 ES6 之前没有任何显著的语法改进。在 ES6 中，我们现在有了一些语法上的改进，比如*默认参数*、 *rest 参数*、*箭头函数*等。

#### 默认参数

在 ES6 之前，基本上没有为函数参数设置默认值的语法。然而，当调用时没有向函数参数传递值时，有一些为函数参数设置后备值的技巧。这里有一个简单的例子:

```
// METHOD 1: Short-circuiting
// Using the logical OR (||) operator
function convertToBase(number, base) {
  number = parseInt(number) || 0;
  base = parseInt(base) || 10;

  return number.toString(base);
}

// METHOD 2: Ternary (?:) operator
// With additional type check (safer option)
function convertToBase(number, base) {
  number = (typeof number !== "undefined") ? parseInt(number) : 0;
  base = (typeof base !== "undefined") ? parseInt(base) : 10;

  return number.toString(base);
}
```

在这个代码片段中，我们已经能够为函数参数设置默认值。因此，这些参数的行为就像它们是可选的一样，因为当参数没有被传递时，会使用回退值。

在 ES6 中，您可以用默认值初始化函数参数，当参数未被传递或为`undefined`时将使用该默认值。下面是我们如何用默认参数重写之前的`convertToBase()`函数:

```
function convertToBase(number = 0, base = 10) {
  return parseInt(number).toString(parseInt(base));
}
```

ES6 中的命名函数参数具有与`let`声明相同的行为。ES6 中的默认值不仅限于文字值或原始值。

> 任何 JavaScript 表达式也可以用作函数参数的默认值。

这里有一个例子:

```
function getDefaultNumberBase() {
  return 10;
}

function convertToBase(number = 0, base = getDefaultNumberBase()) {
  return parseInt(number).toString(parseInt(base));
}
```

这里，我们使用来自`getDefaultNumberBase()`的返回值作为`base`参数的默认值。当设置另一个参数的默认值时，您甚至可以使用前一个参数的值。这里有一个例子:

```
function cropImage(width, height = width) {
*// ...implementation* }
```

在这个代码片段中，`height`参数将被设置为`width`参数的值，只要它没有被传递或者它是`undefined`。

尽管在设置默认值时可以使用以前的参数值，但不能使用在函数体内声明的变量。这是因为默认参数有自己的作用域，它与函数体的作用域是分开的。

#### 休息参数

`arguments`对象是在调用时捕获传递给函数的所有参数的最终手段。这使得创建可以接受不同数量参数的重载函数成为可能。

> 然而，`arguments`对象虽然类似于数组，但在对其执行某些数组操作之前，需要将其转换为实际的数组。

这里有一个简单的例子:

```
function sum() {
  // Convert arguments to array
  var args = Array.prototype.slice.call(arguments);

  // Compute sum using array reduce()
  return args.reduce(function(a, b) { return a + Number(b) }, 0);
}
```

该函数计算传递给它的任意数量的参数之和。如果参数不是一个`number`，它会尝试使用`Number()`全局函数将其转换为一个数字。如果没有参数被传递，它将返回`0`。注意，为了使用`reduce()`方法，`arguments`对象首先被转换成一个数组，并赋给了`args`变量。

在 ES6 中，引入了*静止参数*。一个 **rest 参数** **仅仅是一个** **命名的函数参数，前面有三个点** ( `...`)。rest 参数被赋予一个数组，该数组包含传递给函数的其余参数。下面是我们如何使用 rest 参数重写之前的`sum()`函数:

```
function sum(...args) {
  // Compute sum using array reduce()
  return args.reduce((a, b) => a + Number(b), 0);
}
```

关于 rest 参数的使用，有几点值得注意。

1.  一个函数只能有一个 rest 参数。
2.  rest 参数(如果存在)必须是最后一个参数。

3.  rest 参数与`arguments`对象不同。它只捕获其他命名参数之后剩余的参数，而`arguments`对象捕获传递给函数的所有参数。

4.  rest 参数不能在对象文本设置器中使用。

#### 传播算子

假设我们有一个包含班级学生分数的数组，我们想计算学生的平均分数。基本上，我们将首先计算分数的总和，然后将总和除以分数的数量。

我们可以使用在上一节中创建的`sum()`函数来计算分数的总和。然而，问题是我们有一个分数数组和 sum expects 数字作为参数。

在 ES6 之前，`Function.prototype.apply()`方法可以用来处理这样的情况。此方法将一个数组作为其第二个参数，该参数表示调用函数时应该使用的参数。

这里有一个例子:

```
const scores = [42, 68, 49, 83, 72, 65, 77, 74, 86, 51, 69, 47, 53, 58, 51];
const totalScore = sum.apply(null, scores);
const averageScore = totalScore / scores.length;

console.log(totalScore); // 945
console.log(averageScore); // 63
```

在 ES6 中，引入了一个新的操作符，称为*扩展操作符* ( `...`)。它与 rest 参数密切相关，对于处理数组和其他*迭代*非常有用。使用扩展运算符，我们可以如下计算`totalScore`:

```
const totalScore = sum(...scores);
```

> 因此，对于大多数用例，spread 操作符是对`Function.prototype.apply()`方法的一个很好的替代。

### 3.箭头功能

ES6 中另一个非常重要的语法改进是引入了*箭头函数*。Arrow 函数使用了一种全新的语法，当以它们最适合的方式使用时，它提供了一些很大的优势。

箭头函数的语法省略了`function`关键字。此外，使用*箭头* ( `=>`)将函数参数与函数体分开，因此得名*箭头函数*。

尽管箭头函数比常规函数更紧凑、更短，但它们在某些定义使用方式的方面与常规函数有很大不同:

1.  箭头函数不能用作构造函数，它们没有原型。因此，在箭头函数中使用`new`关键字通常会导致错误。
2.  箭头函数没有`arguments`对象，因此函数参数必须使用命名参数和 rest 参数。也不允许使用重复的命名参数。

3.  箭头函数内部的`this`绑定不能修改，它总是指向最近的非箭头父函数。

#### 箭头函数语法

箭头函数可能看起来略有不同，这取决于您想要实现的目标。

让我们来看看一些表格:

**无参数**

如果 arrow 函数没有参数，则必须在箭头(`=>`)前使用一对空括号(`()`)，如下面的代码片段所示。

```
// USING REGULAR FUNCTION
const getTimestamp = function() {
  return +new Date;
}

// USING ARROW FUNCTION
const getTimestamp = () => {
  return +new Date;
}
```

对于像这样只返回 JavaScript 表达式值的非常简单的箭头函数，可以省略函数体周围的关键字`return`和一对花括号(`{}`)。

因此，arrow 函数可以重写为:

```
const getTimestamp = () => +new Date;
```

但是，如果从 arrow 函数返回一个对象文字，则需要用一对括号(`()`)将它括起来，否则 JavaScript 引擎会将对象文字的花括号(`{}`)视为包含函数体，这将导致语法错误。这里有一个例子:

```
// Returned object literal wrapped in parentheses
const getProfile = () => ({
  name: 'Glad Chinda',
  gender: 'Male',
  birthday: 'August 15'
});
```

**带参数**

对于只接受一个命名参数的箭头函数，可以省略参数列表周围的括号对，如下面的代码片段所示:

```
// Pair of parentheses is omitted
const computeSquare = num => num * num;
```

但是，有些情况下不能省略参数列表周围的括号。下面是一些这样的情况:

1.  当有多个命名参数时

```
// Pair of parentheses cannot be omitted
const addNumbers = (numA, numB) => numA + numB;
```

2.当有默认参数时，即使它是唯一的参数

```
// The traditional function body wrapped in curly braces
// is used here to aid readability.
// Pair of parentheses cannot be omitted

const factorial = (n = 1) => {
  return (n <= 1) ? 1 : n * factorial(n - 1);
}
```

3.当有 rest 参数时，即使它是唯一的参数

```
// Pair of parentheses cannot be omitted
const range = (...numbers) => Math.max(...numbers) - Math.min(...numbers);
```

4.当有一个析构参数时，即使它是唯一的参数

```
// Pair of parentheses cannot be omitted
const extractName = ({ name = null }) => name;

console.log(extractName({
  name: 'Glad Chinda',
  role: 'author'
})); // "Glad Chinda"
```

**传统功能体**

如前所示，非常简单的箭头函数只返回 JavaScript 表达式的值，可以省略函数体周围的关键字`return`和一对花括号(`{}`)。但是，如果您愿意，您仍然可以使用传统的函数体，尤其是当函数有多个语句时。

```
const snakeCase = value => {
  const regex = /[A-Z][^A-Z]+/g;
  const withoutSpaces = value.trim().replace(/s+/g, '_');

  const caps = withoutSpaces.match(regex);
  const splits = withoutSpaces.split(regex);

  let finalString = splits.shift();

  for (let i = 0; i < splits.length; i++) {
    finalString += `${caps[i]}_${splits[i]}_`;
  }

  return finalString
    .toLowerCase()
    .replace(/_+/g, '_')
    .replace(/^_?(.+?)_?$/, '$1');
}
```

上面的函数试图模仿 JavaScript 库 *Lodash* 的`snakeCase()`方法。这里，我们必须使用传统的用花括号(`{}`)括起来的函数体，因为函数体内有如此多的 JavaScript 语句。

> 与常规函数不同，箭头函数不存在`arguments`对象。但是，他们可以访问非箭头父函数的`arguments`对象。

```
function fetchLastScore() {
  return () => {
    console.log(arguments[arguments.length - 1]);
  }
}

fetchLastScore(42, 68, 49, 83, 72)(); // 72
```

#### 立即调用函数表达式(IIFEs)

JavaScript 中函数的一个有用应用是在*立即调用函数表达式(IIFEs)* 中观察到的，这些函数是 ***函数，它们被立即定义和调用，而不保存对函数*** 的引用。这种函数应用通常见于一次性初始化脚本、JavaScript 库，这些脚本库公开了类似 *jQuery* 等模块化公共接口。

使用常规的 JavaScript 函数，IIFEs 通常采用以下形式之一:

```
// FIRST FORM:
// Wrap the function expression in parentheses
// The invocation expression comes afterwards

(function(a, b) {
  // ...function body here
})(arg1, arg2);

// SECOND FORM:
// Wrap the function expression together with
// the invocation expression in parentheses

(function(a, b) {
  // ...function body here
}(arg1, arg2));
```

如果 arrow 函数用括号括起来，arrow 函数语法也可以与 IIFEs 一起使用。

```
// IIFE: With Arrow Function
// The arrow function is called immediately with a list of arguments
// and the return value is assigned to the `compute` variable

const compute = ((...numbers) => {

  // Private members

  const length = numbers.length;
  const min = Math.min(...numbers);
  const max = Math.max(...numbers);

  const sum = numbers.reduce((a, b) => a + Number(b), 0);

  // Expose an inteface of public methods

  return {
    sum: () => sum,
    avg: () => sum / length,
    range: () => max - min
  };

})(42, 68, 49, 83, 72, 65, 77, 74, 86, 51, 69, 47, 53, 58, 51);

// Access the exposed public methods

console.log(compute.sum()); // 945
console.log(compute.avg()); // 63
console.log(compute.range()); // 44
```

#### 回调函数

回调函数在异步程序中大量使用，在数组方法中也大量使用，如`map()`、`filter()`、`forEach()`、`reduce()`、`sort()`、`find()`、`findIndex()`等。

> 箭头函数非常适合用作回调函数。

在前面的代码片段中，我们看到了 arrow 函数如何与`reduce()`一起使用来计算一组数字的总和。使用箭头功能更紧凑、更整洁。同样，这里是比较:

```
// WITHOUT ARROW FUNCTION
const sum = numbers.reduce(function(a, b) {
  return a + Number(b);
}, 0);

// WITH ARROW FUNCTION
const sum = numbers.reduce((a, b) => a + Number(b), 0);
```

让我们做一些更复杂的事情来演示如何使用 arrow 函数作为数组回调来帮助我们用更少的代码完成更多的工作。我们将模仿 JavaScript 库 *Lodash* 的`flattenDeep()`方法。此方法递归展平数组。然而，在我们的实现中，我们将递归地展平传递给函数的参数数组。

下面是`flattenDeep()`函数的代码片段:

```
const flattenDeep = (...args) => args.reduce(
  (a, b) => [].concat(a, Array.isArray(b) ? flattenDeep(...b) : b)
); 
```

这就是箭头函数在用作回调函数时有多酷，尤其是在使用接受回调函数的数组方法时。

#### 这和箭头功能

许多 JavaScript 程序中混淆和错误的一个主要来源是`this`的值解析。

> 根据函数调用的范围和上下文，解析为不同的值。

例如，当使用`new`关键字调用一个函数时，`this`指向由构造函数创建的实例，然而，当没有使用`new`关键字调用同一个函数时，`this`指向全局对象(在*非严格模式下*)，该对象在浏览器环境中是`window`对象。

这里有一个简单的例子。在下面的代码片段中，不带`new`关键字调用`Person()`会意外创建一个名为`name`的全局变量，因为该函数处于*非严格模式*。

```
function Person(name) {
  this.name = name;
}

var person = Person('Glad Chinda');

console.log(person); // undefined
console.log(name); // "Glad Chinda"
console.log(window.name); // "Glad Chinda"
```

与`this`混淆的另一个常见原因是 DOM 事件监听器。

> 在事件监听器中，`this`指向事件所指向的 DOM 元素。

考虑下面的代码片段:

```
function ScrollController(offset) {
  this.offsets = { offsetY: offset };
}

ScrollController.prototype.registerScrollHandler = function() {
  window.addEventListener('scroll', function(event) {
    if (window.scrollY === this.offsets.offsetY) {
      console.log(`${this.offsets.offsetY}px`);
    }
  }, false);
}

var controller = new ScrollController(100);
controller.registerScrollHandler();
```

这段代码看起来一切都很好。但是，当您开始垂直滚动浏览器窗口时，您会看到控制台上记录了一个错误。错误的原因是`this.offsets`是`undefined`，我们试图访问`undefined`的`offsetY`属性。

问题是:**怎么可能** `**this.offsets**` **就是** `**undefined**` **？**

这是因为事件侦听器中的`this`的值不同于封闭原型函数中的`this`的值。事件监听器中的`this`指向`window`，T3 是事件目标，`offsets`在`window`上不作为属性存在。因此，事件监听器内部的`this.offsets`是`undefined`。

`Function.prototype.bind()`可用于显式设置函数的`this`绑定。下面是如何通过使用`Function.prototype.bind()`显式设置`this`绑定来修复错误:

```
// Using .bind() on event listener to resolve the value of `this`

ScrollController.prototype.registerScrollHandler = function() {
  this.element.addEventListener('scroll', (function(event) {
    if (window.scrollY === this.offsets.offsetY) {
      console.log(`${this.offsets.offsetY}px`);
    }
  }).bind(this), false);
}
```

在这里，我们用圆括号将事件侦听器包装起来，并调用了从封闭原型函数传递值`this`的`bind()`方法。调用`bind()`实际上会返回一个带有指定`this`绑定的新函数。现在一切都很完美，没有任何错误。

> 对于 ES6 箭头功能，没有`this`绑定。因此，箭头函数使用来自其最近的非箭头函数祖先的值`this`。

在像我们这样的情况下，不使用实际返回新函数的`bind()`,我们可以使用箭头函数——因为来自封闭原型函数的`this`绑定被保留。

这是:

```
// Using arrow function for event listener

ScrollController.prototype.registerScrollHandler = function() {
  this.element.addEventListener('scroll', event => {
    if (window.scrollY === this.offsets.offsetY) {
      console.log(`${this.offsets.offsetY}px`);
    }
  }, false);
}
```

* * *

### 4.解构

析构是 JavaScript 语法的另一个非常重要的改进。*析构*使得从复杂结构(如数组和对象)中访问局部变量并为其赋值成为可能，无论这些值在父数组或对象中的嵌套有多深。析构有两种形式:**析构对象**和**析构数组**。

#### 对象析构

为了说明对象析构，假设我们有一个国家对象，如下所示:

```
const country = {
  name: 'Nigeria',
  region: 'Africa',
  codes: {
    cca2: 'NG',
    dialcode: '+234'
  },
  cities: [
    'Lagos',
    'Abuja',
    'Port Harcourt',
    'Benin',
    'Ibadan',
    'Calabar',
    'Warri'
  ]
}
```

我们想向游客展示一些关于这个国家的信息。下面的代码片段展示了一个非常基本的`countryInfo()`函数，它就是这样做的:

```
function countryInfo(country) {
  const name = country.name;
  const region = country.region || 'the world';
  const code2 = country.codes.cca2;
  const dialcode = country.codes.dialcode;
  const cities = country.cities;

  return (
`
COUNTRY TIPS:

${name}(${code2}) is one of the largest countries in ${region}.
There are so many important cities you can visit in ${name}
and here are some of them:

${cities.slice(0, 3).join(', ')} and ${cities.slice(3).length} others.

Phone numbers in ${name} usually begin with ${dialcode}.
`
  ).trim();
}

console.log(countryInfo(country));

// COUNTRY TIPS:
//
// Nigeria(NG) is one of the largest countries in Africa.
// There are so many important cities you can visit in Nigeria
// and here are some of them:
//
// Lagos, Abuja, Port Harcourt and 4 others.
//
// Phone numbers in Nigeria usually begin with +234.
```

在这个代码片段中，我们已经能够从 country 对象中提取一些值，并将它们分配给`countryInfo()`函数中的局部变量——效果非常好。

使用 ES6 析构，我们可以提取这些值，并用更优雅、更简洁的语法将它们赋给变量。下面是旧代码片段和 ES6 析构的比较:

```
// OLD METHOD
const name = country.name;
const region = country.region || 'the world';
const code2 = country.codes.cca2;
const dialcode = country.codes.dialcode;
const cities = country.cities;

// ES6 DESTRUCTURING
const {
  name,
  region = 'the world',
  codes: { cca2: code2, dialcode },
  cities
} = country;
```

上面代码片段中的这种析构形式被称为*对象析构*——因为我们从一个对象中提取值并将它们赋给局部变量。

> 对于对象析构，在赋值表达式的左边使用对象文字。

您甚至可以对函数参数使用对象析构，如下面的代码片段所示:

```
const person = {
  name: 'Glad Chinda',
  birthday: 'August 15'
}

// FUNCTION WITHOUT DESTRUCTURED PARAMETERS
function aboutPerson(person = {}) {
  const { name, birthday, age = 'just a few' } = person;

  console.log(`My name is ${name} and I'm ${age} years old. I celebrate my birthday on ${birthday} every year.`);
}

// FUNCTION WITH DESTRUCTURED PARAMETERS
function aboutPerson({ name, birthday, age = 'just a few' } = {}) {
  console.log(`My name is ${name} and I'm ${age} years old. I celebrate my birthday on ${birthday} every year.`);
}

aboutPerson(person);

// My name is Glad Chinda and I'm just a few years old. I celebrate my birthday on August 15 every year.
```

#### 数组破坏

数组析构用于从数组中提取值并将它们赋给局部变量。假设我们将一种颜色的 RGB(红绿蓝)值表示为数组，如下所示:

```
const color = [240, 80, 124];
```

我们想要显示给定颜色的 RGB 值。下面是如何通过数组析构来实现的。

```
// Array Destructuring
const [red, green, blue] = color;

console.log(`R: ${red}, G: ${green}, B: ${blue}`);
// R: 240, G: 80, B: 124
```

> 对于数组析构，数组文字用在赋值表达式的左边。

通过数组析构，可以跳过不需要的赋值。假设我们只想要颜色的蓝色值。下面是我们如何跳过红色和绿色值，而不把它们赋给局部变量。

```
const [,, blue] = color;

console.log(`B: ${blue}`);
// B: 124
```

数组析构也可以和函数参数一起使用，就像对象析构一样。然而，还有其他一些方法可以用来解决常见的问题。

一个非常重要的用例是在**交换变量**。假设我们想在数据库中搜索存储在两个日期之间的记录。我们可以编写一个简单的函数，接受两个`Date`对象:`fromDate`和`toDate`，如下所示:

```
function fetchDatabaseRecords(fromDate, toDate) {
*// ...execute database query* }
```

我们希望确保`fromDate`总是在`toDate`之前——因此我们希望在`fromDate`在`toDate`之后的情况下简单地交换日期。下面是我们如何使用数组析构来交换日期:

```
function fetchDatabaseRecords(fromDate, toDate) {
  if (fromDate > toDate) {
    // swap the dates using array destructuring
    [fromDate, toDate] = [toDate, fromDate];
  }

  // ...execute database query
}
```

> 关于析构的更详细的指南，你可以看看 [ES6 析构:完整指南](https://codeburst.io/es6-destructuring-the-complete-guide-7f842d08b98f)。

* * *

### 5.班级

类是一些 JavaScript 开发人员长期以来一直想要的一个特性，尤其是那些以前有过其他面向对象编程语言经验的人。JavaScript ES6 语法增强最终包括了类。

尽管类现在是 JavaScript 的一部分，但它们的行为方式与其他经典编程语言并不完全相同。它们更像是以前模拟基于类的行为的方法的语法糖。因此，它们仍然基于 JavaScript 的原型继承模型工作。

在 ES6 之前，类是使用*构造函数*模拟的，实例方法基本上是通过增强构造函数的原型创建的。因此，当用关键字`new`调用构造函数时，它返回一个构造函数类型的实例，该实例可以访问其原型中的所有方法。`this`的值指向构造函数实例。

这里有一个例子:

```
// The Rectangle constructor
function Rectangle(length, breadth) {
  this.length = length || 10;
  this.breadth = breadth || 10;
}

// An instance method
Rectangle.prototype.computeArea = function() {
  return this.length * this.breadth;
}

// Create an instance using the new keyword
var rectangle = new Rectangle(50, 20);

console.log(rectangle.computeArea()); // 1000

// rectangle is also an instance of Object
// Due to JavaScript's prototypal inheritance
console.log(rectangle instanceof Rectangle); // true
console.log(rectangle instanceof Object); // true
```

#### 类别语法

类和函数在很多方面都很相似。正如函数一样，可以使用*类声明*和*类表达式*使用`class`关键字来定义类。

> 与函数一样，类是第一手公民，可以作为值在程序中传递。

然而，类和函数之间有一些显著的不同。

1.  类声明没有被提升，其行为类似于`let`声明。
2.  类构造函数必须总是用`new`调用，而类方法不能用`new`调用。

3.  类定义代码总是处于*严格模式*。

4.  所有的类方法都是不可枚举的。

5.  不能从类内部修改类名。

下面是我们之前使用类语法重写的`Rectangle`类型:

```
class Rectangle {
  // The class constructor
  constructor(length, breadth) {
    this.length = length || 10;
    this.breadth = breadth || 10;
  }

  // An instance method
  computeArea() {
    return this.length * this.breadth;
  }
}

// Create an instance using the new keyword
const rectangle = new Rectangle(50, 20);

console.log(rectangle.computeArea()); // 1000

// rectangle is also an instance of Object
// Due to JavaScript's prototypal inheritance
console.log(rectangle instanceof Rectangle); // true
console.log(rectangle instanceof Object); // true

console.log(typeof Rectangle); // function
console.log(typeof Rectangle.prototype.computeArea); // function
```

这里，我们使用一个特殊的`constructor()`方法来定义类构造函数逻辑，并设置所有的实例属性。事实上，无论何时在一个类上使用`typeof`操作符，它都会返回`“function”`——无论是否为该类显式定义了构造函数。

还要注意的是，`computeArea()`实例方法实际上是添加到底层类构造函数的原型对象中的。这就是为什么在`Rectangle.prototype.computeArea`上使用`typeof`操作符也会返回`“function”`的原因。

基于这些相似性，您可以得出这样的结论:类语法主要是在前面用于创建自定义类型的方法之上的语法糖。

让我们看另一个稍微复杂一点的例子，演示如何使用类表达式并将类作为参数传递给函数。

```
// An anonymous class expression
// assigned to a variable
const Rectangle = class {

  // The class constructor
  constructor(length, breadth) {
    this.length = length || 10;
    this.breadth = breadth || 10;
  }

  // An instance method
  computeArea() {
    return this.length * this.breadth;
  }

}

// A class passed as argument to a function
// Notice how the class is instantiated with new
const computeArea = (Shape, ...dimensions) => {
  return (new Shape(...dimensions)).computeArea();
}

console.log(computeArea(Rectangle, 50, 20)); // 1000
```

这里，我们首先创建了一个匿名的类表达式，并将其赋给了`Rectangle`变量。接下来，我们创建了一个函数，它接受一个`Shape`类作为第一个参数，接受实例化`Shape`的维度作为剩余的参数。代码片段假设它接收的任何`Shape`类都实现了`computeArea()`方法。

#### 扩展类

就像其他面向对象的编程语言一样，JavaScript 类具有类扩展的功能。因此，可以从*父*类创建具有修改功能的*派生*或*子*类。

假设我们有一个用于创建矩形的`Rectangle`类，我们想要创建一个用于创建等长和等宽的矩形(正方形)的`Square`类。我们可以这样做:

```
class Rectangle {
  constructor(length, breadth) {
    this.length = length || 10;
    this.breadth = breadth || 10;
  }

  computeArea() {
    return this.length * this.breadth;
  }
}

// The Square class extends the Rectangle class
class Square extends Rectangle {

  constructor(length) {
    // super() calls the constructor of the parent class
    super(length, length);
  }

}

const square = new Square;

// Square inherits the methods and properties of Rectangle
console.log(square.length); // 10
console.log(square.breadth); // 10
console.log(square.computeArea()); // 100

// square is also an instance of Rectangle
console.log(square instanceof Square); // true
console.log(square instanceof Rectangle); // true
```

首先，注意`extends`关键字的使用，它表明我们想要从父类创建一个派生类。

> 派生类继承父类原型中的所有属性和方法，包括构造函数。

还要注意，我们使用一个`super`引用从派生类的构造函数中调用父类的构造函数。当您想要增强派生类中继承方法的功能时，这非常有用。

例如，从`Square`类中调用`super.computeArea()`将调用在`Rectangle`类中实现的`computeArea()`方法。

> 对`super()`的调用必须在每个派生类的构造函数中进行，并且必须在引用`this`之前进行。

这是因为调用`super()`设置了`this`的值。然而，`super()`不应该在非派生类中使用，因为它被认为是一个语法错误。

创建派生类不仅限于扩展类。派生类通常是通过扩展任何可以用作构造函数并且具有原型的 JavaScript 表达式来创建的，比如 JavaScript 函数。因此，以下是可能的:

```
function Person(name) {
  this.name = name || 'Glad Chinda';
}

Person.prototype.getGender = function() {
  return this.gender;
}

class Male extends Person {
  constructor(name) {
    super(name);
    this.gender = 'MALE';
  }
}

const me = new Male;

// Male inherits the methods and properties of Person
console.log(me.getGender()); // "MALE"

// me is also an instance of Person
console.log(me instanceof Male); // true
console.log(me instanceof Person); // true
```

#### 静态类成员

到目前为止，我们一直在看*实例方法*和*属性*。有时候你需要直接应用于类的*静态方法*或*属性*，并且不会从一个实例改变到另一个实例。在 ES6 之前，静态成员可以按如下方式添加:

```
function Lion() {
  // constructor function
}

// Static property
Lion.category = 'ANIMAL';

// Static method
Lion.animalType = function() {
  return 'CAT';
}

console.log(Lion.category); // "ANIMAL"
console.log(Lion.animalType()); // "CAT"

```

对于 ES6 类，`static`关键字被放在方法名之前，以表明该方法是一个*静态方法*。然而，*静态属性*不能从类内部创建。下面是我们如何创建静态成员:

```
class Lion {
  // Static method
  static animalType() {
    return 'CAT';
  }
}

// Static property
Lion.category = 'ANIMAL';

console.log(Lion.category); // "ANIMAL"
console.log(Lion.animalType()); // "CAT"
```

> 静态类成员也由派生类继承。它们可以被派生类重写，就像实例方法和属性一样。

这里有一个简单的例子:

```
class Lion {
  // Static method
  static animalType() {
    return 'CAT';
  }
}

// Static property
Lion.category = 'ANIMAL';

// Derived Lioness class
class Lioness extends Lion {

  // Override static method
  static animalType() {
    return `${super.animalType()}::LION`;
  }

}

console.log(Lioness.category); // "ANIMAL"
console.log(Lioness.animalType()); // "CAT::LION"
```

#### 更多功能

还有几个值得考虑的类特性，其中之一是 ***访问器属性*** 。在需要在类原型上有属性的情况下，它们会非常有用。

这里有一个简单的例子:

```
class Person {
  constructor(firstname, lastname) {
    this.firstname = firstname || 'Glad';
    this.lastname = lastname || 'Chinda';
  }

  get fullname() {
    return `${this.firstname} ${this.lastname}`;
  }

  set fullname(value) {
    const [ firstname, lastname ] = value.split(' ');
    if (firstname) this.firstname = firstname;
    if (lastname) this.lastname = lastname;
  }
}

const me = new Person;
console.log(me.fullname); // "Glad Chinda"

me.fullname = "Jamie";
console.log(me.fullname); // "Jamie Chinda"

me.fullname = "John Doe (Junior)";
console.log(me.fullname); // "John Doe"
```

类的另一个非常类似于对象文字的好特性是能够为类成员使用 ***计算名称*** 。这些计算出的名称也可以用于访问者属性。

> 计算出的名称通常是包含在一对*方括号* ([])之间的 JavaScript 表达式。

这里有一个简单的例子:

```
const prefix = 'compute';

class Square {
  constructor(length) {
    this.length = length || 10;
  }

  // A computed class method
  [`${prefix}${Square.prototype.constructor.name}Area`]() {
    return this.length * this.length;
  }
}

const square = new Square;
console.log(square.computeSquareArea()); // 100
```

### 结论

尽管这是一篇很长的文章，但我坚信我们大多数人都已经学会了一些使用 JavaScript ES6 新特性来改进代码的方法。

在编写改进的代码时，还应该考虑其他超越 ES6 的特性，如 *ES6 模块*、*承诺*、*异步函数*、*生成器*等。

#### 鼓掌并跟随

如果你觉得这篇文章很有见地，如果你不介意的话，请随意鼓掌。

你也可以在 Medium ( [Glad Chinda](https://medium.com/@gladchinda) )上关注我，获取更多对你有帮助的有见地的文章。你也可以在推特上关注我( [@gladchinda](https://twitter.com/@gladchinda) )。

***享受编码……***

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)``