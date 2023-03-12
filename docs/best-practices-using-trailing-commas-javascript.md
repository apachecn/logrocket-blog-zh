# 在 JavaScript 中使用尾部逗号的最佳实践

> 原文：<https://blog.logrocket.com/best-practices-using-trailing-commas-javascript/>

尾随逗号，也称为悬空或终止逗号，是在元素列表的最后一项后键入的逗号符号。自从 JavaScript 语言引入[以来，尾随逗号在数组文字中是合法的。后来，对象文字加入了数组。随着 ES2017(也称为 ES8)的推出，几乎在任何地方都允许尾随逗号。](https://blog.logrocket.com/tag/vanilla-javascript/)

这似乎是一个小小的改变，但也有一些难以察觉的后果。虽然大多数新的语言特性都很受欢迎，但是如果你不小心的话，这个特性会给你带来麻烦。

在本指南中，我们将详细了解尾随逗号。我们将从常见的数据类型开始，比如数组和对象，在这些数据类型中，您可以安全地在项目列表的末尾添加一个逗号。然后，我们将继续学习语言结构，如参数列表、函数调用和析构赋值。在文章的最后，我们将讨论使用结尾逗号的注意事项。

## 在数组中使用尾随逗号

您可以安全地在数组中的最后一个表达式后包含一个尾随逗号，如下所示:

```
const arr = [
  "one",
  "two",
  "three",
];

```

注意不要在末尾添加多个逗号，否则会创建一个`undefined`元素。例如，以下数组完全合法，但包含四个元素:

```
const arr = [
  "one",
  "two",
  "three",,
];

console.log(arr.length);    // => 4

```

数组不一定有从`0`开始的连续索引。您可以创建一个包含几个“间隙”的数组——这样的数组被称为[稀疏数组](https://en.wikipedia.org/wiki/Sparse_matrix)。例如，下面的数组包含六个元素，其中三个是`undefined`:

```
const sparseArray = [1,,,4,5,,];

```

因此，重要的是要记住,`length`属性的值并不总是表示数组中元素的数量。您甚至可以拥有一个没有元素并且长度为`1`的数组:

```
const arr = [,];

console.log(arr.length);    // => 1

```

然而，在实践中，您很少需要使用稀疏数组。如果这样做，您现有的代码很可能会像处理包含`undefined`元素的普通数组一样处理它。

## 在对象中使用尾随逗号

与数组类似，对象的最后一个属性后面可以有一个逗号:

```
const person = {
  firstName: "John",
  lastName: "Davis",
  age: 30,
}

```

从 ECMAScript 5 开始，对象文字中的尾随逗号就是合法的了。一些 JavaScript 风格的指南，像那些由 Airbnb 和 T2 谷歌创建的指南，甚至鼓励你养成一个习惯，始终包含一个结尾逗号，这样你以后在对象末尾添加新属性时就不太可能遇到语法错误。

请注意，与数组不同，您不能创建稀疏对象，尝试这样做会导致语法错误:

```
const person = {
  firstName: "John",
  ,
  age: 30,
}

// logs:
// => Uncaught SyntaxError: Unexpected token ','

```

正如我之前提到的，除了数组和对象之外，JavaScript 中还有其他一些结构可能有一个尾随逗号。

## 在参数列表和函数调用中使用尾随逗号

有时将函数的参数放在单独的一行是很有用的，特别是如果有一个很长的参数列表，或者您想要容纳描述每个参数的注释。例如:

```
function createRectangle(
  w,    // (number) the width of the rectangle
  h     // (number) the height of the rectangle
) { /* ... */ }

```

随着函数的发展，您可能会发现自己需要向函数中添加更多的参数。但是对于您添加的每个新参数，您必须转到前一行并添加一个逗号:

```
function createRectangularPrism(
  w,    // (number) the width
  h,    // (number) the height
  d     // (number) the depth
) { /* ... */ }

```

即使是有经验的开发人员也不总是记得在前一行添加逗号，这会导致错误。更糟糕的是，commit diffs 将在那一行显示一个代码更改，仅仅是因为您后来添加了一个逗号(稍后将详细介绍)。

幸运的是，ES2017 也允许在函数参数后面添加逗号:

```
function createRectangularPrism(
  w,    // (number) the width
  h,    // (number) the height
  d,    // (number) the depth
) { /* ... */ }

```

这只是编码风格的改变，并没有增加未命名的参数或导致任何其他副作用。

此外，ES2017 更新让我们能够在函数调用中的参数末尾添加逗号。一些程序员喜欢将函数调用的每个参数放在自己的行上。如果你是他们中的一员，那么后面的逗号将再次避免你将来可能出现的错误:

```
createRectangle (
  5,
  10,
)

```

这段代码用两个参数调用`createRectangle()`函数。如果您稍后决定添加第三个参数，您不必编辑任何现有的行。同样的规则也适用于类或对象的方法定义，因为它们也是函数:

```
const myObj = {
  createRectangle(    // defines a method
    w,
    h,
  ) { /* ... */ }
}

```

## 在析构赋值语法中使用尾随逗号

析构赋值语法允许您快速地将数组或对象中的值提取到不同的变量中。当析构时，可以在赋值的左边添加一个尾随逗号。例如，下面的代码析构一个数组:

```
const numbers  = [10, 20, 30];
const [n1, n2, n3,] = numbers;

console.log(n1);    // => 10

```

同样，您可以使用析构来“解包”对象的属性:

```
const car = {
    color: 'red',
    type: 'coupe',
    hp: 500
};

const {color, type, hp,} = car;

console.log(color);    // => red

```

但是类似于普通 JavaScript 对象的 JSON 对象呢？他们可以使用尾随逗号吗？

## 在 JSON 中使用尾随逗号

JSON 文件格式是在 21 世纪初引入的。由于 JSON 基于 JavaScript 的对象语法，并且它是在 2009 年 ECMAScript 5 推出之前发明的，所以结尾逗号不能在 JSON 中使用(记住，在 ES5 中，对象文字中的结尾逗号是合法的)。

例如，以下代码将引发错误:

```
JSON.parse('[1, 2, 3, 4, ]');
// => Uncaught SyntaxError: Unexpected token ] in JSON at position 13

```

这行代码也是如此:

```
JSON.parse('{"foo" : 1, }');
// => Uncaught SyntaxError: Unexpected token } in JSON at position 12

```

有很多在线工具可以帮助你解决这个问题。例如，您可以利用[这个 JSON 格式化程序](https://jsonformatter.curiousconcept.com/)来自动查找并删除 JSON 代码中的尾部逗号。

## 模块导入和导出中的尾随逗号

在现代 JavaScript 中，创建由称为模块的独立代码块组成的程序是一种常见的做法。正如在经典脚本中给对象添加一个逗号是合法的一样，在模块的最后一个导出项后面有一个逗号也是合法的。当您以后想要包含更多导出时，这很方便。例如:

```
// module 1
var foo = 10;
let bar = 20;
const baz = 30;

export {foo, bar, baz, };

```

这段代码使用`export`关键字来公开`foo`、`bar`和`baz`变量。这意味着独立文件中的其他模块可以使用`import`语句来访问这些变量:

```
// module 2
import {
  foo,
  bar,
  baz,    // notice the trailing comma, which is legal when importing identifiers 
} from './module1.js'

```

## 为什么应该开始使用尾随逗号？

JavaScript 程序员过去常常避免在数组中包含尾随逗号，因为早期版本的 Internet Explorer 会抛出错误(即使它从一开始在 JavaScript 中就是合法的)。但是事情已经改变了。现在许多编码风格都推荐始终使用结尾逗号，这是有充分理由的。

如果您经常在数组、对象或参数/形参列表的末尾添加新的项，那么已经有一个尾随的逗号意味着您不必记得在前面的行添加一个逗号，如果您以后需要添加的话。

您可能还会发现自己经常剪切和粘贴属性。同样，有一个尾随逗号可以减少重新排序条目的麻烦，并防止将来出现语法错误。

此外，因为您不需要更改曾经是最后一项的行，版本控制系统将产生更清晰的差异。假设你有这个功能:

```
function myFunction(
  p1,
  p2
) { /* ... */ }

myFunction(
  'arg1',
  'arg2'
);

```

如果您添加一个名为`p3`的新参数，那么 diff 输出将类似于:

```
function myFunction(
  p1,
- p2
+ p2, // Change this line to add a comma
+ p3  // Add p3
) { /* ... */ }

myFunction (
  'arg1',
-  'arg2'
+ 'arg2', // Change this line to add a comma
+ 'arg3'  // Add arg3
);

```

这里，在函数声明和函数调用中有两个变化。让我们看看如果你的函数已经有一个尾随逗号会发生什么:

```
function myFunction(
  p1,
  p2,
) { /* ... */ }

myFunction(
  'arg1',
  'arg2',
);

```

有了尾随逗号，在 diff 输出中只有两处变化:

```
function myFunction(
  p1,
  p2,
+ p3  // Add p3
) { /* ... */ }

myFunction (
  'arg1',
  'arg2',
+ 'arg3'  // Add arg3
);

```

本节的要点是，使用尾随逗号可以更容易地向函数添加新参数，或者在数组和对象中复制/粘贴属性。它还有助于产生更清晰的差分输出。

但是尾随逗号并不是在任何地方都适用，如果你不小心的话，使用它们可能会适得其反。

## 何时不使用尾随逗号

您可能认为也可以在 rest 参数语法中使用尾随逗号，因为在其他各种 JavaScript 结构中也允许尾随逗号。但事实并非如此:

```
function sum(...theArgs,) {    // notice the trailing comma
  return theArgs.reduce((previous, current) => {
    return previous + current;
  });
}

console.log(sum(1, 2, 3));
// => Uncaught SyntaxError: parameter after rest parameter

```

在 rest 参数后使用尾随逗号是非法的，即使您在析构语法中使用它:

```
const numbers  = [10, 20, 30];
const [n1, ...n2,] = numbers;
// => Uncaught SyntaxError: rest element may not have a trailing comma

```

因此，请记住，尽管在析构语法中使用尾随逗号是有效的，但您不能在 rest 参数后使用它。

除了析构语法，还有一个地方使用尾随逗号可能会给你带来麻烦:函数。考虑下面的例子:

```
function myFunction(,) { // ... }    // => Uncaught SyntaxError: Unexpected token ','

myFunction(,);     // => Uncaught SyntaxError: Unexpected token ','

```

这段代码的第一行定义了一个没有参数和逗号的函数，这导致了一个`SyntaxError`。您可以没有参数，也可以没有逗号，或者在参数后面跟一个逗号。当你调用一个函数时也是如此:你不能有一个唯一参数是逗号的函数调用。

## 结论

在 JavaScript 语言中，逗号符号的用法已经经历了几次修订，在每一次修订中，更多的语言结构增加了对结尾逗号的支持。在本文中，我们研究了结尾逗号在不同结构中的工作方式，包括数组、对象、JSON 对象、参数列表、函数调用以及模块导入和导出。

然后，我们学习了尾部逗号在哪里可以合法使用，在哪里不可以。通常，当您经常复制/粘贴属性或将新项目添加到列表末尾时，应该使用尾随逗号。您也可以利用它们来产生更清晰的差分输出。但是，请记住，您不应该将它们与 rest 参数语法一起使用，并且您不能拥有唯一的参数是逗号的函数声明/调用。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.