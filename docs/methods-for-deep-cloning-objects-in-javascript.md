# 深度克隆 JavaScript 中对象的方法

> 原文：<https://blog.logrocket.com/methods-for-deep-cloning-objects-in-javascript/>

## 介绍

在 JavaScript 中，对象就像是键值对的存储或集合。它们是一种[结构数据类型](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)，可以看作是属性的集合。这些属性可以是其他数据类型的值，包括像`Boolean`、`Number`、`undefined`等原始类型。，甚至其他对象。因此，有了对象，我们可以构建更复杂的数据结构。

由于 JS 中对象的性质，它们通常存储在内存中，只能通过引用进行复制。这意味着变量本身并不存储一个对象，而是存储一个标识符，它表示内存中特定对象的地址或引用。因此，对象不能以与原语相同的方式处理。

对于原始数据类型，一旦变量被赋值，它们就不能被复制。因此，更改变量的值永远不会更改底层的基元类型。这意味着一旦这些类型的值被赋给了一个变量，就不可能再改变它们，这就是所谓的不变性。但是，它们可以组合在一起以获得新的值。

另一方面，对象是可变的数据类型。在本文中，我们将探索在 JavaScript 中修改或改变对象的方法。这需要针对一般对象行为执行浅层或深层克隆或复制。

## 介绍对象行为

重申一下，对象是引用类型，因此，当我们复制一个对象变量时，我们间接地创建了一个对存储在计算机内存中其他地方的同一对象的引用。因此，当复制一个对象变量时，只复制对该对象的引用，而不是实际的对象！

让我们看一个例子来更好地理解这个概念:

```
let user = { name: "Alexander" }

// this instead copies a reference to the previous object
let newUser = user
```

在上面的例子中，我们有两个变量，每个引用内存中的同一个对象。在这种情况下，变量`newUser`引用了内存中最初声明的变量`user`。请注意，这仅适用于引用类型，如对象和数组；对于像字符串或布尔值这样的基本类型，情况并非如此。

> **注意**:我们可以利用`Object.is()`的方法来判断这两个值实际上是否是同一个值。在浏览器控制台上运行`console.log(Object.is(user, newUser))`应该会返回布尔值`true`。

## 对象复制方法

JavaScript 提供了许多复制对象的方法，但是它们不提供深度复制。在大多数情况下，执行浅层复制是默认行为。

我们应该注意，ES6 为语言中的浅层复制对象提供了两种较短的语法。它们包括`[Object.assign()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)`和[扩展语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)，它复制所有[可枚举自身属性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Enumerability_and_ownership_of_properties)的值。)从一个对象到另一个对象。

> **注意**:浅拷贝成功地拷贝了[原语类型](https://flaviocopes.com/javascript-types/#primitive-types)，如数字和字符串，但是任何对象引用都不会被递归地拷贝，而是新的、被拷贝的对象将引用相同的初始对象。

让我们一个接一个地看看它们:

### 用`Object.assign()`方法复制一个对象

在[对象构造方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign#)中，`Object.assign()`用于将一个或多个源对象的值和属性复制到目标对象。它返回目标对象，该对象具有从源对象复制的属性和值。

由于`Object.assign()`复制属性值，它不适合深度克隆。基本上，我们可以使用这种方法对一个对象进行浅层克隆，并将两个或更多的对象合并成一个具有相同属性的更大的对象。

*   **参数:**
    *   `target`–值和属性被复制到的目标对象
    *   `sources`–从中复制值和属性的源对象
*   **返回值:**
    *   此方法返回目标对象。

现在，让我们看一个使用这种方法将两个对象合并在一起的非常简单的例子:

```
let objectA = {a: 1, b: 2}

let objectB = {c: 3, d: 4}

Object.assign(objectA, objectB)

console.log(objectA);
// → { a: 1, b: 2, c: 3, d: 4 }
```

这里，目标对象是`objectA`，而源对象是`objectB`。使用`object.assign()`类似于使用 lodash `clone`方法浅层复制对象。让我们看另一个例子:

```
const clone = require('lodash.clone')
var objA = { 
  a: 1,
  b: {
        c: 2,
        d: {
            e: 3
      }
  }
}
var objB = clone(objA)
objA.b.c = 30
console.log(objA)
// { a: 1, b: { c: 30, d: { e: 3 } } }
console.log(objB)
// { a: 1, b: { c: 30, d: { e: 3 } } }
```

作为浅层拷贝，克隆的是值，拷贝的是对象引用，而不是对象本身。因此，如果我们在原始对象中编辑一个对象属性，它在复制的对象中也会被修改，因为在这种情况下引用的内部对象是相同的。

### 使用扩展语法复制对象

[扩展运算符](https://github.com/tc39/proposal-object-rest-spread)是 ES2018 的一个特性，它将[扩展](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)属性添加到对象文字中。它提供了一种非常方便的方式来执行浅层克隆，相当于`Object.assign()`所做的。对于对象，spread 运算符用于创建具有新值或更新值的现有对象的副本。

它将可枚举属性从提供的对象复制到新的对象上。让我们按照语法来看一个用法示例:

```
const copied = { ...original }
```

现在让我们看一个真实的例子:

```
const objA = { 
    name: 'Alexander', 
    age: 26, 
}

const objB = { 
    Licensed: true, 
    location: "Ikeja" 
}

const mergedObj = {...objA, ...objB}
console.log(mergedObj) 

// { name: 'Alexander', age: 26, Licensed: true, location: 'Ikeja' }
```

从上面我们可以看出`mergedObj`是`objA`和`objB`的翻版。实际上，对象上的每个可枚举属性都将被复制到最终的`mergedObj`对象中。扩展操作符只是`Object.assign()`方法的简写，但是两者之间有一些微妙的区别，包括`Object.assign()`触发`[setters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set)`，而扩展操作符不触发。

> **注意**:如果一个对象在对该对象进行浅层复制时引用了其他对象，那么我们会将引用复制到外部对象。当执行深度复制时，这些外部对象也被复制，因此新的克隆对象完全独立于旧的对象。

### JavaScript 中深度克隆对象的推荐方法

大多数时候，当我们决定在程序中复制对象时，我们的意图实际上是通过引用进行复制，这或多或少是对对象进行浅层复制。然而，当涉及到深度嵌套的对象时，`Object.assign()`或`spread`的行为是不同的。

从本质上讲，在语言中没有一种一致的方法来克隆或复制对象，不管它们的结构如何，就对象是如何构造的而言。

这里出现的一个问题是复制深度嵌套的对象，比方说，两到三层，如果我们对新对象进行更改，它不会影响作为目标的原始对象。那么我们如何正确地深度克隆一个对象呢？

要执行深度复制，我们最好的选择是依赖一个经过良好测试、受欢迎并由社区维护良好的库:Lodash。Lodash 提供了`clone`和`cloneDeep`函数来分别执行浅层克隆和深层克隆。

比如深度复制 Node.js 中的对象时，我们可以利用 [Lodash](https://lodash.com/docs/4.17.10#cloneDeep) `cloneDeep()`方法。下面显示了一个示例:

```
const cloneDeep = require('lodash.clonedeep')

let objA = {
    a: 1,
    b: {
        c: 2,
        d: {
            e: 3
        }
    }
}

// copy objA save as new variable objB
let objB = cloneDeep(objA)

// change the values in the original object objA
objA.a = 20
objA.b.c = 30
objA.b.d.e = 40

console.log(JSON.stringify(objA))
// → {"a":20,"b":{"c":30,"d":{"e":40}}}

// objB which is the cloned object is still the same
console.log(JSON.stringify(objB))
// → {"a":1,"b":{"c":2,"d":{"e":3}}}
```

Lodash `cloneDeep()`方法类似于`clone`，除了它递归克隆`value`同时保留对象继承。这个库的优点是我们可以单独导入每个函数——不需要将整个库导入到我们的项目中。这可以大大减少我们的程序依赖的大小。

为了利用 Node.js 中的 Lodash 克隆方法，我们可以通过运行`npm i lodash.clonedeep`进行深度克隆和`npm i lodash.clone`进行浅层克隆来安装它。我们可以这样使用它:

```
const clone = require('lodash.clone')
const cloneDeep = require('lodash.clonedeep')

const shallowCopy = clone(originalObject)
const deepCopy = clonedeep(originalObject)
```

> **注意**:复制从内置 JavaScript 对象派生的对象会产生额外的、不需要的属性。

### 本地深度克隆

HTML 标准包括 [**一种内部结构化克隆/序列化算法**](https://html.spec.whatwg.org/multipage/structured-data.html#safe-passing-of-structured-data) ，可以创建对象的深度克隆。尽管仍然局限于某些内置类型，但它可以保留克隆数据中的引用，允许支持循环和递归结构，否则这些结构会导致 JSON 出错。

Node.js 中的支持仍然是试验性的，`v8`模块[直接公开了结构化序列化 API](https://nodejs.org/api/all.html#v8_serialization_api)。例如，克隆一个对象非常简单:

```
const v8 = require('v8');

const structuredClone = obj => {
  return v8.deserialize(v8.serialize(obj));
};
```

更多细节可以在这里找到[。](https://nodejs.org/api/all.html#v8_serialization_api)

### 其他对象克隆方法

#### 遍历每个对象属性并将其复制到一个新的空对象中

这包括遍历源对象的属性，并将它们一个接一个地复制到目标对象。其思想是创建一个新对象，并通过迭代和复制其属性来复制现有对象的结构。

让我们看一个例子:

```
let user = {
  name: "Alexander",
  age: 26
};

let clone = {}; // the new empty object

// let's copy all user properties into it
for (let key in user) {
  if (user.hasOwnProperty(key)) {
  clone[key] = user[key];
 }
}

// now clone is a fully independent object with the same content
clone.name = "Chinedu"; // changed the data 

console.log(user.name); // still Alexander in the original object
```

#### 使用 JSON.parse/stringify 克隆对象

这提供了一种非常快速的深度克隆对象的方法。然而，它不是非常可靠和标准的，因为它会丢失一些数据。

使用这种方法，源对象*必须*是 JSON 安全的。如果我们在对象中不使用`Date`、`undefined`、`Infinity`、函数、正则表达式、映射、集合或其他复杂类型，深度克隆对象的一个非常简单的方法是使用:

```
JSON.parse(JSON.stringify(object))
```

让我们看一个例子:

```
const a = {
  string: 'string',
  number: 123,
  bool: false,
  nul: null,
  date: new Date(),  // string
  undef: undefined,  // lost
  inf: Infinity,  // 'null'
  re: /.*/,  // lost
}

console.log(typeof a.date) // returns  object

const clone = JSON.parse(JSON.stringify(a))

console.log(typeof clone.date)  // returns string 

console.log(clone)
// 
{
  string: 'string',
  number: 123,
  bool: false,
  nul: null,
  date: '2020-09-28T15:47:23.734Z',
  inf: null,
  re: {}
}
```

> **注意**:这个方法需要某种异常处理来保证它的安全，以防源对象不能被转换成 JSON。

## 结论

默认情况下，JavaScript 总是通过值传递，这意味着改变变量值永远不会改变底层的原始类型。然而，对于通过引用传递的非原始数据类型(数组、函数和对象)，我们总是可以改变数据，导致单个对象值在不同的时间具有不同的内容。

克隆一个 JavaScript 对象是最常用的任务，因为我们不希望创建一个已经存在的相同对象。正如我们现在所知道的，对象是通过引用来分配和复制的。换句话说，变量存储的不是对象值，而是一个引用。因此，复制这样的变量或将其作为函数参数传递会复制引用，而不是对象。

对于只存储像数字和字符串这样的基本类型的简单对象，前面讨论的浅层复制方法将会起作用。浅复制意味着复制第一个级别，并引用更深的级别。但是，如果对象属性引用其他嵌套对象，则不会复制实际对象，因为我们只会复制引用。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.