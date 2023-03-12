# JavaScript 中循环之间的技术差异概述

> 原文：<https://blog.logrocket.com/technical-differences-between-loops-javascript/>

在 JavaScript 中使用循环时，需要正确定义两个关键点:`enumerable properties`和`iterable objects`。

## 可枚举属性

可枚举对象的一个定义特征是，当我们通过赋值操作符将属性赋给对象时，我们将内部可枚举标志设置为 true。这是默认值。

但是，我们可以通过将它设置为 false 来改变这种行为。

一个经验法则是一个可枚举的属性总是出现在一个`for … in`循环中。

让我们来看看实际情况:

```
// shows up in a for .... in loop
const gbols = {};
gbols.platform = "LogRocket";

Object.getOwnPropertyDescriptor(gbols, "platform")
{value: "LogRocket", writable: true, enumerable: true, configurable: true}

// doesn't show up in a for .... in loop 
//to have more control of this properties  we use
Object.defineProperty(gbols, 'role', {value: 'Admin', writable: true, enumerable: false})

// Testing this out yeilds
for (const item in gbols) {
console.log(item)
}
 // logs platform
```

## 可迭代对象

如果一个对象定义了它的迭代行为，那么它就是可迭代的。在这种情况下，将在`for …of`构造中循环的值将定义它的迭代行为。可迭代的内置类型包括`Arrays`、`Strings`、`Sets`和`Maps`，而`object`是不可迭代的，因为它没有指定`@iterator method`。

基本上，在 Javascript 中，所有的可迭代对象都是可枚举对象，但并不是所有的可枚举对象都是可迭代对象。

这里有一个概念化的方法:`for …in`在数据中寻找对象，而`for ..of`寻找重复的序列。

让我们看看当使用`Array`数据类型时是什么样子:

```
 const authors = ['Jade', 'Dafe', 'Gbols', 'Daniel'];
// using with a for in loop
for (const author in authors) {
console.log(author)
}
// logs 0,1,2,3

for (const author of authors) {
console.log(author)
}
// logs Jade, Dafe, Gbols, Daniel
```

当使用这个结构时，你需要记住的是，如果调用了`typeof`并且答案产生了`object`，那么你可以使用一个`for …in`循环。

让我们来看看对作者的变量的操作:

```
typeof authors
// logs "object" hence we can use a for ..in
```

乍一看，这似乎令人惊讶，但是需要注意的是，数组是一种特殊的对象，索引是它的键。知道`for ...in`将在一个构造中寻找一个对象可以极大地帮助我们。当一个`for ...in`循环找到一个对象时，它将遍历每个键。

我们可以将`for ..in`在作者数组上循环的方式形象化如下:

```
 const authors = {
0: 'Jade',
1: 'Dafe',
2: 'Gbols',
3: 'Daniel'
}
```

一个重要的注意事项:如果它可以被追踪到一个对象(或者从对象原型链中继承)，那么`for …in`将以不特定的顺序迭代这个键。

同时，如果它实现了一个迭代器`for.. of`结构，它将在每次迭代中遍历值。

## `ForEach`和`map`方法

虽然`forEach`和`map`方法可以用来实现相同的事情，但是它们的行为和性能有所不同。

在基本级别，当调用函数时，它们都接收回调作为参数。

考虑下面的片段:

```
const scoresEach = [2,4 ,8, 16, 32];
const scoresMap = [2,4 ,8, 16, 32];
const square = (num) => num * num;
```

让我们列举一下它们在操作上的一些不同之处。

`forEach`返回`undefined`，而`map`返回一个新的`array`:

```
let newScores = []
const resultWithEach = scoresEach.forEach((score) => {
const newScore = square(score);
newScores.push(newScore);
});
const resultWithMap = scoresMap.map(square);

console.log(resultWithEach) // logs undefined
console.log(resultWithMap) // logs [4, 16, 64, 256, 1024]
```

`Map`是一个纯函数，而`forEach`执行一些变异:

```
console.log(newScores) // logs [4, 16, 64, 256, 1024]
```

在我看来，`map`更倾向于函数式编程范式。我们不需要总是执行一个突变来得到想要的结果，不像`forEach`，我们必须突变`newScores`变量。每次运行时，当提供相同的输入时，`map`功能将产生相同的结果。与此同时，`forEach`副本将从上一次突变的先前值中提取。

### 链接

用`map`链接是可能的，因为返回的结果是一个`array`。因此，可以立即对结果调用任何其他数组方法。换句话说，我们可以称之为`filter`、`reduce`、`some`等。这在`forEach`中是不可能的，因为返回值是未定义的。

### 表演

`map`方法往往比`forEach`方法表现更好。可以用 [JsPerf](https://jsperf.com/)

检查用`map`和`forEach`实现的等效代码块的性能。平均来说，你会发现`map`函数的执行速度至少快了 50%。

注意:这个基准依赖于你使用的机器，以及你的浏览器实现。

## 结论

在上面讨论的所有循环结构中，给我们最多控制权的是`for..of`循环。我们可以用关键字`return`、`continue`和`break`来使用它。这意味着我们可以指定我们希望在`array`中的每个元素上发生什么，以及我们是否希望提前离开或跳过。

记住这些信息，确保根据您希望在代码中实现的目标使用适当的工具。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.