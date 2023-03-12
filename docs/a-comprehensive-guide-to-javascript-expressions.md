# JavaScript 表达式和操作符综合指南

> 原文：<https://blog.logrocket.com/a-comprehensive-guide-to-javascript-expressions/>

## 介绍

如果你和我一样是 web 开发人员，每天都要和 JavaScript 代码打交道。然而，尽管在我们的日常工作中使用这个库，我们中的许多人并不知道所有的 JavaScript 表达式和操作符，这些会使我们的生活变得更容易。

在本文中，我列出了 JavaScript 中一些必须知道的表达式和操作符，包括每个表达式和操作符的简单明了的例子。让我们开始吧！

## JavaScript 表达式

表达式是任何有效的代码单元(由一组文字、变量、运算符和其他更简单的表达式组成)，在解析时产生一个值。表达式可以像变量名一样简单，等于我们赋予它的任何值(比如 x = 5)。

正如你可能已经知道的，我们赋给变量的值可以是任何值，从数字，到字符串，到布尔值。

有👇🏼五类表情；前三个相当简单，而后两个稍微复杂一点。

### 1.算术:

这些表达式使用算术运算符，如 5 或 5.864。

### 2.字符串:

这些表达式有一组像`"nada"`或`"5.864"`这样的字符作为值。

### 3.逻辑:

这些表达式等于真或假，通常通过一个逻辑运算符，如`&&`或`||`。

### 4.主要表达式:

这些是我们在 JavaScript 代码中使用的基本关键字和关键字符(您可能知道其中的大部分):

#### `this`**:T2**

就像在`this.propertyName`里一样。`this`表达式引用执行上下文中的对象属性。

…现在您可能想知道:`this`的执行上下文是什么？通常，它是全局上下文(例如，在浏览器中，它会是`window`)。一个例外是在一个对象方法中使用(例如`user.fullName()`)。在这种情况下，`this`在方法(`fullname()`)中被调用，并将引用对象上下文(`user`)。

更多信息，codeSTACKr 将在本视频的[中详细解释`this`。](https://www.youtube.com/watch?v=Pi3QC_fVaD0)👈🏼

#### `function`、`function*`和`async`功能:

大家知道，`function`定义了函数表达式(duh)。显而易见，函数是一个代码过程，它接受输入(一组语句)并以任务性能的形式返回输出。

另一方面，`function*`定义了一个生成器函数，它通过生成一系列结果而不是单个值来简化编写迭代器的任务。

虽然可以在[异步编程](https://blog.logrocket.com/understanding-asynchronous-javascript/)中使用`function*`，但是简单使用一个`async`函数更好。异步函数使得*[异步的、基于承诺的行为能够以更简洁的风格](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)编写，同时避免了显式配置承诺链的需要。*

 *一般来说，异步函数将允许您执行一系列任务，而不必等待一个任务完成后再运行下一个。

想了解更多信息，我推荐看看这个视频。

#### `yield`、`yield*`、**、**、 **:**

首先，让我们区分一下`yield`和`return`以及`yield`和`await`:

`return`用于常规函数，`yield`用于生成器函数(`function*`)。不同的是，在一个函数中，我们可以简单地`return`一个值。相比之下，在生成器函数中，我们生成一个值序列，所以`yield`用于生成多个值，直到我们停止调用那个`function*`。

另一方面，`await`仅用于`async`功能。一个`async`函数的唯一任务是返回一个承诺，所以`await`将在等待的值上调用`Promise.resolve`。

既然我们已经区分了`return`、`yield`和`await`，你可能会好奇`yield*`到底是什么。这实际上相当简单:`yield*`以下面的方式委托给另一个生成器函数:

```
function* function1() {
yield "I'm the value from function1 👋 but when function2 is called, it delegates to function1 the task of generating a value";
}
function* function2() {
yield* function1();
}
console.log(function2().next().value);
// expected output: "I'm the value from function1, but when function2 is called, it delegates to function1 the task of generating a value "
```

#### `class`:

Quora 上的一个用户将一个类描述为“一个对象的蓝图”，我非常同意这个比较。

为了理解类表达式的概念(在 ES6 中引入)，看看它在一个例子中是如何工作的是很有用的:

```
class ClothingItem {
constructor(type, season) {
this.type = type;
this.season = season;
}
description() {
return `This ${this.type} is for ${this.season}`;
}
}
console.log(new ClothingItem("dress", "winter"));
// expected output: Object {season: "winter", type: "dress"}
console.log(new ClothingItem("dress", "winter").description());
// expected output: "This dress is for winter"

```

如此处所示，在我们使用`constructor()`定义了对象的实例属性之后，我们能够使用`description()`将数据绑定到一个方法。

#### 数组初始值设定项/文字语法`[]` **:**

有[种不同的方法来初始化数组](https://www.w3docs.com/snippets/javascript/how-to-declare-and-initialize-an-array-in-javascript.html)，但是最简单的方法是使用`[]`:

```
let myEmptyArray = [];
console.log(myEmptyArray);
// expected output: []

```

然后，您可以将数组元素推入其中(`myEmptyArray.push(475)`)，或者甚至在初始化阶段定义它们(`let myArray = [1, 100]`)。

#### 对象初始化器/文字语法`{}` **:**

类似于我们可以用文字语法而不是构造函数语法初始化一个数组，我们也可以只用`{}`初始化一个对象:

```
let myEmptyObject = {};
console.log(myEmptyObject);
// expected output: Object {}

```

#### Regexp(正则表达式的缩写)`/ab+c/i`:

RegExp 用于将文本与模式进行匹配，确保用户在字段中输入的内容与电子邮件或数字等模式相匹配。

不久前，我发现了这个伟大的[工具](https://regexr.com/)来学习、构建和测试 RegExp。但是，对于帮助我快速获得我需要的正则表达式的快速备忘单，我使用了 [iHateRegex](https://ihateregex.io/) 😉。

![](img/0d52b6bf71bbbce7425a7d4dafbf86c6.png)

#### 分组操作符`()` **:**

我们称之为分组操作符的括号只是控制任何给定表达式中求值的优先级。

如你所知，`1 + 2 * 3`将产生与`1 + (2 * 3)` (7)相同的结果。然而，如果你改变括号的顺序，你就改变了谁先被求值。例如，`(1 + 2) * 3`将返回 9。

作为一名程序员，在需要使用三元运算符计算许多条件的情况下，这很方便:

```
condition1 ? "statement 1" : (condition2 ? "statement 2" : "statement 3");

```

### 5.左侧表达式:

左侧(LHS)表达式指的是特定表达式或赋值的位置。不出所料，您会在代码块的左侧找到它们。它们包括以下内容:

#### 属性访问器:

属性访问器为我们提供了一种通过使用以下两种语法之一来访问对象属性的方法:

*   带点符号`object.property`
*   带括号符号`object["property"]`

看看下面的例子:

```
const myObject = {
firstObject: "Boku",
secondObject: "Anata",
};
console.log(myObject.firstObject);
// Expected output: "Boku"
console.log(myObject["secondObject"]);
// Expected output: "Anata"

```

#### `new`:

正如我们在前面的`[class]`表达式示例中看到的，您可以使用`new`关键字创建一个对象的实例。点击阅读更多关于`new`操作者[的细节。](https://codeburst.io/javascript-for-beginners-the-new-operator-cee35beb669e)

#### `new.target`:

`new.target`简单地检测一个函数或构造函数是否被使用`new`关键字调用。在这段视频和这篇[文章](https://theanubhav.com/2017/12/17/understanding-newTarget/)中了解更多关于这个元属性[的信息。👈🏻](https://youtu.be/0XtqX7onHHg?t=140)

#### `super`:

关键字`super`用于访问和调用父构造函数。例如，当您有两个共享公共部分的构造函数时，它在类继承方面会很方便。为了避免重复代码，您可以调用`super()`。

这里有一个`super`在起作用的例子:

```
class Movie {
constructor(name, year) {
this.name = name;
this.year = year;
}
MovieDescription() {
return `Movie: ${this.name}, year: ${this.year}.`;
}
}
console.log(new Movie("Ma Rainey's Black Bottom", "2020"));
// expected output: Object { name: "Ma Rainey's Black Bottom", year: "2020"}
console.log(new Movie("Ma Rainey's Black Bottom", "2020").MovieDescription());
// expected output: "Movie: Ma Rainey's Black Bottom, year: 2020."
class TvShow extends Movie {
constructor(name, year, seasons) {
super(name, year);
this.seasons = seasons;
}
TvShowDescription() {
return `Tv Show: ${this.name}, number of seasons: ${this.seasons}, year: ${this.year}.`;
}
}
console.log(new TvShow("F.R.I.E.N.D.S", "1994", 10));
// expected output: Object { name: "F.R.I.E.N.D.S", seasons: 10, year: "1994"}
console.log(new TvShow("F.R.I.E.N.D.S", "1994", 10).TvShowDescription());
// expected output: "Tv Show: F.R.I.E.N.D.S, number of seasons: 10, year: 1994."

```

#### 传播语法`...obj` **:**

扩展语法`...`允许您扩展表达式。例如，如果你需要将一个数组添加到一个数组中，你可能会得到这样的结果(如果你不使用`...` ): `[a, [b, c], d]`。

使用 spread 运算符的一种方法是分布数组元素:

```
let childArray = ["b", "c"];
let parentArray = ["a", ...childArray, "d"];
console.log(parentArray);
// expected output: [a, b, c, d]

```

spread 语法还有一些其他的用法，在本文的[中有所介绍。](https://codeburst.io/javascript-es6-the-spread-syntax-f5c35525f754)

## JavaScript 运算符

既然我们已经看到了表达式能做什么，是时候开始讨论操作符了。运算符用于完全从简单的表达式中构建复杂的表达式。我们将在下面详细解释。

运算符是我们用来生成右侧(RHS)值的工具。它们可以像加法操作符`a + b = c`一样简单，其中生成的右边值是 c，或者稍微复杂一点，例如使用条件操作符:`(c > a) ? "c is greater than a": "c is not greater than a"`。

有三种类型的运算符:一元、二元和三元。在接下来的几节中，我们将用简单易懂的例子来讨论这三个问题。

### 一元运算符

一元运算符是只需要一个操作数(表达式)来生成值的运算符。比如在`2++`中我只需要一个操作数(`2`)就可以生成一个值。

一元运算符有很多种，我们将在下面讨论。

#### 一元算术运算符:

##### 增量**运算符** **`++`** :

递增运算符非常简单:它增加 1。但是，请注意，它的行为会因操作数是前缀还是后缀而有所不同:

```
let a = 2;
console.log(a++);
// expected output: 2
console.log(a);
// expected output: 3
let b = 2;
console.log(++b);
// expected output: 3
console.log(b);
// expected output: 3
```

##### 减量运算符`--` **:**

递增运算符的原理同样适用于递减运算符:

```
let a = 2;
console.log(a--);
// expected output: 2
console.log(a);
// expected output: 1
let b = 2;
console.log(--b);
// expected output: 1
console.log(b);
// expected output: 1
```

##### 一元加运算符`+` **:**

一元加运算符`+`做了一件简单的事情:它将其操作数转换为一个数字(如果它还不是一个数字的话):

```
let a = "2";
console.log(a);
// expected output: "2"
console.log(+a);
// expected output: 2

```

这个技巧可以方便地将字符串转换成数字。你可能会问:如果不能转换成数字呢？在这种情况下，`+"some_string"`返回`NaN`。

##### 一元否定运算符`-` **:**

一元求反运算符与`+`做同样的事情(将一个字符串转换成一个数字)，但是它还通过对其操作数求反做了更多的工作:

```
let a = "2";
console.log(a);
// expected output: "2"
console.log(-a);
// expected output: -2
```

##### 一元逻辑运算符:

逻辑运算符是与逻辑值一起使用的运算符，或者我们通常所知的布尔值(真/假)。因此，一元逻辑运算符是只需要一个布尔操作数来生成值的运算符。

###### 逻辑非运算符`!`:

`!`运算符在应用于`truthy`表达式时返回`false`，👉🏼反之亦然。

```
let a = 2;
let b = 4;
console.log(a < b);
// expected output: true
console.log(!(a < b));
// expected output: false
console.log(!(a > b));
// expected output: true
console.log(!"truthy");
// expected output: false
console.log(!"truthy");
// expected output: false
```

##### 一元按位运算符:

作为人类，我们使用十进制来理解数字(1、4.5、5000 等等)。另一方面，计算机以二进制格式(0 和 1 的组合)处理数字。

按位运算符的作用是计算操作数，不是基于十进制值，而是基于二进制 32 位表示:

```
let decimal = 9;
let binary = decimal.toString(2);
console.log(binary);
// expected output: "1001"
// 32 bit integer : "00000000000000000000000000001001"

```

幸运的是，这种 32 位表示发生在幕后。按位运算符的输出仍然是标准的 JavaScript 输出，我们将在下面介绍。

##### 按位非运算符`~` **:**

一元按位非运算符(`~`)反转其操作数的位。

```
const a = 3;
// 32-bit integer: 00000000000000000000000000000011
console.log(~a);
// expected output: -4
// 32-bit integer: 11111111111111111111111111111100

```

这里发生的是 NOT 操作符接受我们操作数的(`3` ) 32 位表示`00000000000000000000000000000011`，将 0 转换成 1，然后将 1 转换成 0。

要将十进制转换成二进制或 32 位整数，请查看这个有用的工具。

##### `delete` **操作员:**

你猜对了:只要属性属于一个对象(包括数组)，这个操作符就删除它所应用的操作数:

```
const programmer = {
alias: "rosen",
age: 30,
};
console.log(programmer.alias);
// expected output: "rosen"
delete programmer.alias;
console.log(programmer.alias);
// expected output: undefined

```

但是，请注意，您不能在普通变量上使用`delete`。

```
const programmer = "rosen";
console.log(programmer);
// expected output: "rosen"
delete programmer;
console.log(programmer);
// expected output: "rosen"
```

##### `void` **操作员:**

如果出于某种原因，您需要一个表达式返回 undefined(即使它应该返回一些东西)，那么可以使用`void`操作符。

```
function notVoid() {
return "I am not void!";
}
console.log(notVoid());
// expected output: "I am not void!"
console.log(void notVoid());
// expected output: undefined

```

##### `typeof` **操作员:**

最后，顾名思义，`typeof`操作符指定了它所应用的表达式类型:

```
console.log(typeof 3);
// expected output: "number"
console.log(typeof "3");
// expected output: "string"
console.log(typeof (3 > "3"));
// expected output: "string"
function three() {}
console.log(typeof three);
// expected output: "function"
array = [];
console.log(typeof array);
// expected output: "object"

```

### 二元运算子

与一元运算符不同，二元运算符需要两个操作数来生成一个值。

例如，如果将大于(`>`)的比较运算符应用于两个表达式，它只能生成一个值(`true`或`false`)(在这种情况下，`2 > 5`将计算为`false`)。

#### 标准 **a** 算术运算符:

##### 加法运算符`+` **:**

```
let a = 4;
let b = 2;
console.log(a + b);
// expected output: 6
```

##### 减法运算符`-` **:**

```
let a = 4;
let b = 2;
console.log(a - b);
// expected output: 2

```

##### 除法运算符`/` **:**

```
let a = 4;
let b = 2;
console.log(a / b);
// expected output: 2
```

##### 乘法运算符`*` **:**

```
let a = 4;
let b = 2;
console.log(a * b);
// expected output: 8
```

##### 求幂运算符`**` **:**

取幂运算符计算底数的指数。在下面的例子中，你会发现 4 是底数，2 是指数，结果预期的输出是 16。

```
let a = 4;
let b = 2;
console.log(a ** b);
// expected output: 16

```

##### 余数运算符`%` **:**

也称为模数，余数(`%`)运算符返回两个操作数相除的“余数”。

```
let a = 4;
let b = 2;
console.log(a % b);
// expected output: 0
let c = 3;
console.log(a % c);
// expected output: 1
```

#### 比较运算符:

顾名思义，比较运算符比较应用它们的操作数，然后返回一个`true`或`false`。

请注意，您可以比较任何操作数，无论是数字、字符串、布尔值还是对象。例如，字符串基于它们的 unicode 值进行比较。在比较不同类型的操作数时，JavaScript 会将操作数转换成兼容的类型进行比较。

```
string = "string";
console.log(string.charCodeAt()); // returns a string unicode value
// expected value: 115
console.log(string < 3);
// expected value: false
console.log(false > true);
// expected value: false
console.log(true > false);
// expected value: true
function operand1() {
return "hello";
}
bye = ["zaijian", "matta", "besslama", "tchao"];
console.log(operand1() !== bye);
// expected value: true
```

#### 等式运算符:

有四种不同的等式运算符:`==`、`!=`、`===`和`!==`。在下面的示例中，我们将展示每种方法的具体工作原理，但首先，请记住以下几点:

*   等于`==`和不等于`!=`运算符在比较操作数之前对它们进行转换，因此`3 == "3"`的计算结果为`true`，即使我们正在比较一个数字和一个字符串。
*   另一方面，严格等于`===`和严格不等于`!==`运算符会考虑它所比较的操作数的类型。因此，在这种情况下，`3 === "3"`将返回`false`。

##### 等号运算符`==` **:**

```
console.log(3 == "3");
// expected value: true
console.log(3 == 3);
// expected value: true
```

##### 不相等运算符`!=` **:**

```
console.log(3 != "3");
// expected value: false
console.log(3 != 3);
// expected value: false
```

##### 严格相等运算符`===` **:**

```
console.log(3 === "3");
// expected value: false
console.log(3 === 3);
// expected value: true
```

##### 严格不等于运算符`!==` **:**

```
console.log(3 === "3");
// expected value: true
console.log(3 === 3);
// expected value: false

```

#### 关系运算符:

##### 大于运算符`>` **:**

```
console.log(3 > 1);
// expected value: true
```

##### 大于或等于运算符`>=` **:**

```
console.log(3 >= "3");
// expected value: true
```

##### 小于运算符`<` **:**

```
console.log("3" < 1);
// expected value: false
```

##### 小于或等于运算符`<=` **:**

```
console.log(3 <= 1);
// expected value: false
```

#### 逻辑运算符:

##### 逻辑与运算符`&&` **:**

在返回`true`或`false`之前，`&&`操作符必须评估它的两个操作数。这也意味着如果表达式中只有一个是`false`，AND 将返回`false`。

```
console.log(3 > 1 && "3" > 0);
// expected value: true

```

##### 逻辑或运算符* `||` **:**

另一方面，如果任一操作数是`true`，那么`||`操作符将返回`true`。因此，如果第一个操作数的计算结果为`true`，那么期望值将作为`true`返回，而无需检查第二个操作数。

```
console.log(3 > 1 || "3" == 0);
// expected value: true
```

#### 按位运算符:

正如本指南前面所讨论的，按位运算符基于它们的 32 位表示来计算它们的操作数；然后在标准 JavaScript 输出中返回该值。

要更深入地讨论 JavaScript 位操作符的用例，我推荐阅读这篇文章。

##### 按位逻辑运算符:

###### 按位 AND 运算符`&`:

当两个操作数二进制表示中的 32 位中的一位具有相反的值(0 对 1)时，按位 AND 运算符(`&`)在其评估结果中放入 0:

```
const a = 3;
// 32-bit integer: 00000000000000000000000000000011
const b = 7;
// 32-bit integer: 00000000000000000000000000000111
console.log(a & b);
// expected output: 3
// 32-bit integer: 00000000000000000000000000000011

```

正如您在这里看到的，`b`的二进制表示中的`1`与`a`的二进制表示中的`0`在同一位置冲突，已经被反转为`0`。

###### 按位异或运算符`^`:

按位 XOR 运算符(`^`)遵循与按位`&`运算符完全不同的逻辑。与后者不同，`^`只将两个二进制操作数中的`1`(在同一位置)转换为`0`:

```
const a = 3;
// 32-bit integer: 00000000000000000000000000000011
const b = 7;
// 32-bit integer: 00000000000000000000000000000111
console.log(a ^ b);
// expected output: 4
// 32-bit integer: 00000000000000000000000000000100

```

###### 按位 OR 运算符`|`:

按位 OR 运算符(`|`)遵循与`&`相同的逻辑，只是将该位还原为 1(而不是 0)。

```
const a = 3;
// 32-bit integer: 00000000000000000000000000000011
const b = 7;
// 32-bit integer: 00000000000000000000000000000111
console.log(a | b);
// expected output: 7
// 32-bit integer: 00000000000000000000000000000111
```

#### **按位移位运算符:**

在前面的例子中，我们已经看到了按位逻辑操作符如何获取 32 位操作数，对它们进行求值，并输出一个结果，在这个结果中，它们还原某个位的值。

另一方面，按位移位运算符采用其 LHS 操作数的 32 位二进制表示形式，并将一位移位到特定位置(由其 RHS 操作数指定)。

为了更好地形象化这个和每个移位操作符是如何工作的，让我们看一下下面的例子:

### 左移位操作符`<<`:

这里，左移运算符采用`a`的 32 位二进制表示，向左移动 7 ( `b`)位，并丢弃超出的部分(000000)。

```
const a = 3;
// 32-bit integer: 00000000000000000000000000000011
const b = 7;
console.log(a << b);
// expected output: 384
// 32-bit integer: 00000000000000000000000110000000

```

### 右移位操作符`>>`:

右移位操作符`>>`的操作与左移位操作符`<<`相同，但有两处不同:

1.  它向相反的方向移动，并且
2.  它保留了操作数的符号

```
const a = 5;
// 32-bit integer: 00000000000000000000000000000101
const b = 2;
console.log(a >> b);
// expected output: 1
// 32-bit integer: 00000000000000000000000000000001
const c = -5;
// 32-bit integer: -00000000000000000000000000000101
console.log(c >> b);
// expected output: -2
// 32-bit integer: -00000000000000001111111111111110
```

### **无符号(补零)右移位运算符** `>>>` **:**

无符号(零填充)右移位运算符`>>>`，通过`b` (1 位)将`a`的 32 位二进制表示向右移位，类似于`>>`运算符。这里的主要区别是`>>>`可以把负数变成正数，就像这样:

```
const a = 3;
// 32-bit integer: 00000000000000000000000000000011
const b = 1;
console.log(a >>> b);
// expected output: 1
// 32-bit integer: 00000000000000000000000000000001
const c = -3;
// 32-bit integer: -00000000000000000000000000000011
console.log(c >>> b);
// expected output: 2147483646
// 32-bit integer: 01111111111111111111111111111110

```

#### 可选链接运算符`?.` **:**

像许多开发人员一样，您可能试图获得一个对象链深处的值，但是因为它为空或未定义，所以会导致错误。

不要在复杂的对象中使用链接操作符`.`,下次你可以选择使用可选的链接操作符`?.`。该运算符允许您搜索值，而无需验证链中的每个引用。

```
const holiday = {
name: "christmas",
companions: "family",
travel: {},
};
console.log(holiday.travel.country);
// expected output: undefined
// Causes errors
console.log(holiday?.travel.country);
// expected output: undefined
// Only returns undefined

```

#### 逗号运算符`,`:

当然我们都知道我们心爱的`,`操作者，但是刷新一下我们的记忆也无妨！逗号操作符将变量声明(如`a = [6, 3, 1, 8]`)和表达式分开，这样它们可以按顺序执行(如循环变量声明:`var i = 0; i < 100; i++`)。

#### `in` **操作员:**

如果对象具有操作符正在寻找的给定属性,`in`操作符返回 true。

```
const holiday = {
name: "christmas",
companions: "family",
travel: {},
};
console.log("name" in holiday);
// expected output: true

```

#### `instanceof` **操作员:**

如果你想确认一个属性是一个特定操作符的实例，你可以像这样使用`instanceof`:

```
class ClothingItem {
constructor(type, season) {
this.type = type;
this.season = season;
}
}
const clothes = new ClothingItem("dress", "winter");
console.log(clothes instanceof ClothingItem);
// expected output: true
console.log(clothes instanceof Object);
// expected output: true
```

#### **赋值运算符:**

顾名思义，赋值运算符将一个值(基于它的 RHS 操作数)赋给它的 LHS 操作数。

##### **主要任务`=` :**

主赋值运算符由等号组成，它在 a `= b`中将 b 赋值给 a。

```
a = 1;
b = 4;
console.log(a);
// expected output: 1
a = b;
console.log(a);
// expected output: 4

```

##### 析构赋值( **`[a, b] = [1, 2]`** ，`{a, b} = {a:1, b:2}` **):**

通过析构赋值语法，可以首先从数组或对象中提取数据，然后将数据赋给不同的变量:

```
const array = [6, 3, 1, 8];
const [a, b, c, d] = array;
console.log([a, b, c, d]);
// expected output: [6, 3, 1, 8]
console.log(a);
// expected output: 6
const object = {
first: 6,
second: 3,
third: 1,
fourth: 8,
};
const { first, second, third: e, fourth } = object;
console.log({ first, second, e, fourth });
// expected output: Object {
//   e: 1,
//   first: 6,
//   fourth: 8,
//   second: 3
// }
console.log(e);
// expected output: 1

```

##### 逻辑赋值运算符:

我们讨论的与表达式相关的逻辑运算符只计算其操作数，然后返回一个布尔值。另一方面，逻辑赋值操作符计算它们左边的操作数，并根据布尔值，根据右边的操作数给它们赋值。

###### **逻辑与** `&&=` **:**

```
a = 4;
b = 0;
b &&= a;
console.log(b);
// expected value: 0
// As b = 0 which evaluates to false, b isn't assigned a's value.
a &&= b;
console.log(a);
// expected value: 0
// As a = 4 which evaluates to true, a is assigned b's value.

```

###### **逻辑或** `||=` **:**

`||=`做与`&&=`相反的工作。

```
a = 4;
b = 0;
a ||= b;
console.log(a);
// expected value: 4
// As a = 4 which evaluates to true, a isn't assigned b's value.
b ||= a;
console.log(b);
// expected value: 4
// As b = 0 which evaluates to false, b is assigned a's value.

```

###### **逻辑无效运算符** `??=` **:**

如果你从未听说过，`??=`操作符做两件事:第一，它检查它左边的操作数是否有值，第二，给任何没有值的操作数赋值。

在这个例子中，LHS 操作数是`life.time`和`life.money`。因为`life.money`没有值，所以逻辑 nullish 操作符会给它赋值。由于`life.time`确实有一个值(`50`，所以不会受到`??=`的影响。

```
const life = { time: 50, money: null };
console.log((a.time ??= 10));
// expected output: 50
console.log((a.money ??= 25));
// expected output: 25

```

#### 速记运算符:

在下一节中，我们将研究一下我们在上一节中研究过的算术和位运算的简写二进制运算符。

##### 对于标准算术运算:

##### 添加`+=`:

```
(a = 4), (b = 2);
a += b;
console.log(a);
// expected output: 6

```

##### 减法`-=`:

```
(a = 4), (b = 2);
a -= b;
console.log(a);
// expected output: 2

```

##### 分部`/=`:

```
(a = 4), (b = 2);
a /= b;
console.log(a);
// expected output: 2

```

##### 乘法运算`*=`:

```
(a = 4), (b = 2);
a *= b;
console.log(a);
// expected output: 8

```

##### 求幂运算`**=`:

```
(a = 4), (b = 2);
a **= b;
console.log(a);
// expected output: 16

```

##### 余数`%=`:

```
(a = 4), (b = 2);
a %= b;
console.log(a);
// expected output: 0

```

#### 对于位运算:

##### 按位逻辑与`&=`:

```
(a = 4), (b = 2);
a &= b;
console.log(a);
// expected output: 0

```

##### 按位逻辑或`^=`:

```
(a = 4), (b = 2);
a ^= b;
console.log(a);
// expected output: 6

```

##### 逐位逻辑异或`|=`:

```
(a = 4), (b = 2);
a |= b;
console.log(a);
// expected output: 6

```

##### 按位左移`<<=`:

```
(a = 4), (b = 2);
a <<= b;
console.log(a);
// expected output: 16

```

##### 按位符号传播右移`>>=`:

```
(a = 4), (b = 2);
a >>= b;
console.log(a);
// expected output: 1

```

##### 按位补零右移`>>>=`:

```
(a = 4), (b = 2);
a >>>= b;
console.log(a);
// expected output: 1

```

### 条件运算符( **`condition ? ifTrue : ifFalse`** ):

最后，如果不讨论唯一一个接受三个操作数的操作符，那将是我们的疏忽；女士们先生们:条件运算符。

```
a = 6;
console.log(a > 5 ? "bigger" : "smaller");
// expected output: "bigger"
console.log(a < 5 ? "bigger" : "smaller");
// expected output: "smaller"

```

您会注意到，条件(也称为三元)运算符检查条件是真还是假(因此有了问号`?`)，然后根据条件是真还是假执行两个表达式中的一个(放在冒号`:`之间)。

## 摘要

恭喜你！您已经完成了这本全面的 JavaScript 表达式和运算符指南。为了将来参考，将下面的汇总表加入书签可能会很有用，它概括了我们在本文中涉及的所有材料(只需在新的选项卡中打开图像并放大即可！).

![](img/21ab9e6bf99e7ffe5b57cf3aef7a8f98.png)

JavaScript expressions and operators

感谢您的阅读，请查看我在[NadaRifki.com](https://www.nadarifki.com/)的作品。我也总是很乐意阅读你在 Twitter 上的评论或留言( [@RifkiNada](https://twitter.com/RifkiNada) )。😜

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.*