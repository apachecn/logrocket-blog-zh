# JavaScript 记录和元组提议:概述

> 原文：<https://blog.logrocket.com/the-javascript-record-and-tuple-proposal-an-overview/>

## 介绍

[ECMAScript](https://tc39.es/proposal-record-tuple/#sec-overview) `Record`和`Tuple`提案为 JavaScript 引入了两种新的数据结构:记录和元组。这些数据结构允许我们在 JavaScript 中创建前两个复合原语值。

[复合原语](https://github.com/tc39/proposal-record-tuple#compound-primitive-types)由其他组成值组成，这意味着它们可以包含简单的原语，如字符串、数字和布尔，以及复合原语本身，即记录和元组。

包括复合原语类型在内的原语共享几个不同的特性。首先，它们是绝对不可变的，这意味着我们不能像改变非原语(例如，对象)那样改变它们，因为它们返回的是新值，而不是原始值的副本。

此外，由于它们非常不可变的性质，我们可以使用严格的等式操作符(`===`)来比较它们，甚至有更多的保证。这意味着这些数据类型可以通过它们的内容进行严格的比较，如果它们包含相同的组成元素，我们可以确定它们是相等的。

## JavaScript 中不变性的先前进展

ECMAScript [关于不可变数据结构](https://github.com/sebmarkbage/ecmascript-immutable-data-structures)的提议曾被考虑过，但由于一些相关的复杂性和缺乏足够的用例，最终被放弃了。

如今，用户依靠像 [Immutable.js](https://immutable-js.github.io/immutable-js/) 和 [Immer](https://github.com/mweststrate/immer) 这样的库来处理语言中对象和数组的[深度不变性。例如，Immer 的方法依赖于生成冻结的对象。然而，使用这些库可能会导致一些潜在的问题。](https://blog.logrocket.com/to-mutate-or-immutate/)

首先，做同一件事有不同的方法，它们不能很好地互操作。此外，这些库中使用的语法不像在 JS 中集成的那样符合人体工程学。最后，很难让这些库很好地与外部类型系统一起工作。

有了这个新的`Record`和`Tuple`提议，就有了一个可靠的标准处理方式，因为它只基于原语。通过设计仅基于原语的记录和元组，该建议定义了一种清晰的比较方式，同时消除了这些库引入的复杂性。

## 仔细查看`Record`和`Tuple`提案

[提案](https://github.com/tc39/proposal-record-tuple)目前处于 TC39 流程的第 2 阶段，这意味着它仍然是一项正在进行的工作，并且可能会根据社区反馈进行更改。它是由 TC39 成员罗宾·里卡德和彭博的里克·巴顿带头发起的。

按照这个提议，记录和元组分别是对象和数组的不可变版本。本质上，记录被设计成具有类似对象的结构，而元组在结构上是类似数组的。而且我们前面提到过，记录和元组只能包含原语，包括其他记录和元组。

### 句法

记录和元组当前是使用前面的`#`修饰符声明的。这就是它们与语言中的对象和数组声明的区别。让我们看一些当前提出的定义这些新数据类型的语法的例子。

记录声明:

```
const rec1 = #{} // an empty record
const rec2 = #{ a: 1, b: 2 } // a record containing two KV pairs
const rec3 = #{ a: 1, b: #[2, 3] } // a record with two elements including a tuple containing 2 elements

```

元组声明:

```
const tup1 = #[]  // an empty tuple
const tup2 = #[1, 2]  // a tuple containing two elements
const tup3 =#[1, 2, #{ a: 3 }] // a tuple with three elements including a record containing 1 element

```

虽然建议的语法已经在语言的其他地方使用(例如，[私有类字段](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Private_class_fields))，但它与对象和数组文字的语法相似，所以用户理解起来不会太难。也就是说，[正在讨论](https://github.com/tc39/proposal-record-tuple/issues/10)是完全使用一个新的关键字，还是对`{| |}`和`[||]`使用完全不同的语法。

> **注**:关于可能出现的语法错误的详细信息，请查看建议书文档的[这一节](https://github.com/tc39/proposal-record-tuple#syntax-errors)。

## 浏览记录和元组

让我们在下面更详细地探索这些新的数据类型。

### 记录

正如我们提到的，记录类似于对象，但是它们是不可变的。记录的语法类似于我们定义对象的方式，前面有一个`#`符号。下面我们来声明一个样本记录:

```
const rec1 = #{ a: 10, b: 20, c: 30 }

```

再次注意，只有基本类型可以作为记录中的属性。因此，我们可以在记录中包含元组和其他记录，因为它们都是原语。例如:

```
const rec2 = #{x: #[1,2,3], y: #{ a: 10, b: 20, c: 30 }}

```

> **注意**:试图创建包含除原始数据类型之外的任何类型的记录或元组会导致`typeError`。关于深度不变性设计决策的更多细节可以在[这里](https://github.com/tc39/proposal-record-tuple#why-deep-immutability)找到。

#### 有记录的对象方法

我们也可以利用记录的对象方法。例如，让我们在上面的例子中使用对象扩展语法:

```
const rec3 = #{x: #[1,2,3], ...rec2}

console.log(rec3) 
// rec3 return value
#{x: Tuple, y: Record}
        1\. ▶x: Tuple
            1\.  0: 1
            2\.  1: 2
            3\.  2: 3
        2\. ▶y: Record
            1\.  a: 10
            2\.  b: 20
            3\.  c: 30

```

再举一个例子，让我们通过将上面的`rec3`记录登录到操场上的控制台来提取它的键。

```
console.log(Object.keys(rec3))
// ["x", "y"]
   1\.  0: "x"
   2\.  1: "y"

```

我们还可以使用标准方法对记录应用析构，如下所示:

```
const {name, ...rest} = #{ name: "Alex", occupation: "Farmer", age: 98 };

console.log(name); // Alex
console.log(rest); // Object {age: 98, occupation: "Farmer"}
console.log(#{...rest}); // Record #{age: 98, occupation: "Farmer"}

```

正如我们可以访问常规对象中的属性一样，我们也可以访问记录:

```
console.log(rec3.x); // #[1, 2, 3]

```

### 元组

元组类似于 JavaScript 中的数组，但是，同样，它们是不可变的。让我们再来看看它们的语法:

```
const tup1 = #[1, 2, 3, 4]

```

#### 具有元组的数组方法

同样，记录支持对象方法，元组支持数组方法。例如，我们可以像访问数组一样访问元素的位置或索引:

```
console.log(tup1[1]) // 2

```

我们也可以利用扩展运算符来组合两个元组:

```
const tup2 = #[5,6,7,8,9]

const tup3 = #[...tup1, ...tup2];

console.log(tup3) // #[1, 2, 3, 4, 5, 6, 7, 8, 9]

```

元组也支持标准的数组方法，如`map`:

```
const tup = #[1, 2, 3]
console.log(tup.map(x => x * 2)); 
// #[1, 2, 3] 

```

> **注意**:对`Tuple.prototype.map`的回调可能只返回原语。

同样，我们可以使用下面的标准方法对元组应用析构:

```
const [head, ...rest] = #[1, 2, 3];

console.log(head); // 1
console.log(rest); // Array [2, 3]
console.log(#[...rest]); // Tuple #[2, 3]

```

一般来说，对象和数组支持相同的方法来有效地处理 JavaScript 中的记录和元组，尽管在某些情况下有细微的差别，我们将在后面探讨。

> **注意**:记录和元组作为映射的键和作为集合的元素同样重要。根据该提议，由于语言中原始类型的性质，当与记录和元组一起使用时，映射和集合变得更强大。
> 
> 或者，记录和元组不能用作`[WeakMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)`中的键或`[WeakSet](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakSet)`中的值。这是因为[在(非弱)地图中将它们专门用作键](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap#Description)是没有意义的，因为原语是不允许的。
> 
> 我们还应该注意，在这些情况下只应该使用对象类型，因为它们是非原语。关于该主题的更多详细信息，请参见提案文件中的[。](https://github.com/tc39/proposal-record-tuple#usage-in-mapsetweakmapweakset)

### 记录/元组数据类型相等

有了这个新提议，我们可以很容易地通过值来比较复合原语，而不像对象或数组那样只能通过引用或标识来比较。下面我们来看一些使用记录和元组的例子。

比较元组和记录:

```
console.log(#{x: 1, y: 4} === #{y: 4, x: 1})
//true

console.log(#['a', 'b'] === #['a', 'b'])
//true

```

比较对象和数组:

```
console.log({x: 1, y: 4} === {x: 1, y: 4})
//false 

console.log(["a", "b"] === ["a", "b"])
//false

```

正如我们所看到的，记录和元组在比较时总是相等的。另一方面，对象和数组是不相等的，因为它们不是基元，如前所述。

本质上，如果记录和元组的结构和内容是相同的，那么根据严格相等(`===`)运算，它们的值被认为是相等的。此外，从上面的例子中我们可以看到，记录键的插入顺序不影响记录的相等性，这与对象不同。

> **注意**:严格的相等对于这些数据类型很重要，这样用户就不需要担心哪个记录/元组被操作或者它是在哪里被创建的；换句话说，它确保了可预测的行为。

### 遍历元组和记录

就像数组一样，元组是可迭代的:

```
const tup = #[1,2,3]

for (const o of tup) { 
console.log(o);  
}

// 1,2,3

```

或者，与对象类似，记录只能与类似于`Object.entries`的 API 一起迭代:

```
const rec = #{z: 1, a: 2 }

// Object.entries can be used to iterate over Records, just like with Objects
for (const [key, value] of Object.entries(rec)) { 
console.log(key) 
}

//  1\. "a" 2\. "z"

```

### 将记录和元组转换为普通对象/数组

为了将记录转换回 JS 中的对象，我们需要做的就是将它包装在一个`Object`构造函数中:

```
const rec = #{x: 1, y: 4})
console.log(Object(rec)
// returns an Object {x: 1, y: 4} 

```

同样，要将元组转换成数组，我们需要做的就是使用`Array.from`方法:

```
const tup = #['a', 'b']
console.log(Array.from(tup))
// returns an array  ['a', 'b']

```

### 从对象和数组转换

我们可以分别使用`Record()`和`Tuple.from()`方法将对象和数组转换成记录和元组。注意，`Record()`和`Tuple.from()`只能处理记录、元组或其他原语。让我们看一些例子。

对于记录:

```
const obj = { a: 1, b: 2, c: 3 }
const rec1 = Record(obj);
console.log(rec1)
//#{ a: 1, b: 2, c: 3 }

```

对于元组:

```
const arr = [1, 2, 3]
const tup = Tuple.from(arr); 
console.log(tup)
//#[1, 2, 3]

```

> **注意**:嵌套的对象引用会导致`TypeError`，因为当前的提议草案[不包含递归转换例程](https://github.com/tc39/proposal-record-tuple/issues/122)。

### 用`Tuple`进行类似数组的操作

在这种情况下，`Tuple.prototype.pushed`类似于使用`Array.prototype.push`。然而，当涉及到对元组的这些操作时，它们是不可变的，因为它们总是返回新的修改版本:

```
const tup1 = #[1, 2];
console.log(tup1.pushed(3)) // #[1, 2, 3]

```

类似地，`Tuple.prototype.sorted`方法类似于在语言中使用`Array.prototype.sort`方法:

```
const tup2 = #[3, 2, 1]
console.log(tup2.sorted) // #[1, 2, 3]

```

### 记录/元组上的`JSON.parseImmutable`和`JSON.stringify`

该提案增加了`JSON.parseImmutable`，它将允许我们从 JSON 字符串中提取记录或元组。这类似于`JSON.parse`如何处理对象和数组。

> **注**:本文写作时，游乐场不支持`JSON.parseImmutable`。

另外，`JSON.stringify`在记录和元组上的行为分别等同于`JSON.stringify`在对象或数组上的行为。

`JSON.stringify`上书记载:

```
const rec = #{ a: #[1, 2, 3] }

console.log(JSON.stringify(rec));

//"{"a":[1,2,3]}"

```

`JSON.stringify`在物体上:

```
const obj = { a: [1, 2, 3] }

console.log(JSON.stringify(obj));

//"{"a":[1,2,3]}"

```

更多详情可参见提案文件[。此外，这篇文章的所有例子都可以在](https://github.com/tc39/proposal-record-tuple#jsonstringify)操场[找到。](https://rickbutton.github.io/record-tuple-playground/#eyJjb250ZW50IjoiLy8gY29uc3QgcmVjMSA9ICN7IGE6IDEwLCBiOiAyMCwgYzogMzAgfVxuLy8gY29uc3QgcmVjMiA9ICN7eDogI1sxLDIsM10sIHk6ICN7IGE6IDEwLCBiOiAyMCwgYzogMzAgfX1cblxuXG4vLyBjb25zdCByZWMzID0gI3t4OiAjWzEsMiwzXSwgLi4ucmVjMn1cblxuLy8gY29uc29sZS5sb2cocmVjMylcblxuLy8gY29uc29sZS5sb2cocmVjMy54KTsgXG4vLyBjb25zb2xlLmxvZyhPYmplY3Qua2V5cyhyZWMzKSlcblxuLy8gY29uc29sZS5sb2coT2JqZWN0LmtleXMocmVjMykgPT09ICNbXCJ4XCIsIFwieVwiXSApXG5cbi8vIGNvbnN0IHR1cDEgPSAjWzEsIDIsIDMsIDRdXG5cbi8vIGNvbnNvbGUubG9nKHR1cDFbMV0gPT09IDIpIFxuXG5cbi8vIGNvbnN0IHR1cDIgPSAjWzUsNiw3LDgsOV1cblxuLy8gY29uc3QgdHVwMyA9ICNbLi4udHVwMSwgLi4udHVwMl07XG5cbi8vIGNvbnNvbGUubG9nKHR1cDMpXG5cbi8vIGNvbnN0IHR1cDMgPSNbMSwgMiwgI3sgYTogMyB9XVxuXG4vLyBjb25zdCByZWMzID0gI3sgYTogMSwgYjogI1syLCAzXSB9XG5cblxuLy8gY29uc29sZS5sb2coI3t4OiAxLCB5OiA0fSA9PT0gI3t5OiA0LCB4OiAxfSlcblxuLy8gY29uc29sZS5sb2coI1snYScsICdiJ10gPT09ICNbJ2EnLCAnYiddKVxuXG4vLyBjb25zb2xlLmxvZyh7eDogMSwgeTogNH0gPT09IHt4OiAxLCB5OiA0fSlcblxuLy8gY29uc29sZS5sb2coW1wiYVwiLFwiYlwiXSA9PT0gW1wiYVwiLCBcImJcIl0pXG5cblxuXG5cbi8vIGNvbnN0IHtuYW1lLCAuLi5yZXN0fSA9ICN7IG5hbWU6IFwiQWxleFwiLCBvY2N1cGF0aW9uOiBcIkZhcm1lclwiLCBhZ2U6IDk4IH07XG5cbi8vIGNvbnNvbGUubG9nKG5hbWUpOyAvLyBBbGV4XG4vLyBjb25zb2xlLmxvZyhyZXN0KTsgLy8gT2JqZWN0IHsgb2NjdXBhdGlvbjogXCJGYXJtZXJcIiwgYWdlOiA5OCB9XG4vLyBjb25zb2xlLmxvZygjey4uLnJlc3R9KTsgLy8gUmVjb3JkICN7IG9jY3VwYXRpb246IFwiRmFybWVyXCIsIGFnZTogOTggfVxuXG4vLyBjb25zb2xlLmxvZyhPYmplY3Qua2V5cygjeyBhOiAxLCBiOiAyIH0pKVxuXG4vLyBjb25zb2xlLmxvZyhPYmplY3Qua2V5cygjeyBhOiAxLCBiOiAyIH0pID09PSAjW1wiYVwiLCBcImJcIl0pOyBcblxuLy8gY29uc3QgdHVwID0gI1sxLCAyLCAzXVxuLy8gY29uc29sZS5sb2codHVwLm1hcCh4ID0+IHggKiAyKSk7XG5cblxuXG4vLyBjb25zdCBbaGVhZCwgLi4ucmVzdF0gPSAjWzEsIDIsIDNdO1xuXG4vLyBjb25zb2xlLmxvZyhoZWFkKTsgLy8gMVxuLy8gY29uc29sZS5sb2cocmVzdCk7IC8vIEFycmF5IFsyLCAzXVxuLy8gY29uc29sZS5sb2coI1suLi5yZXN0XSk7IC8vIFR1cGxlICNbMiwgM11cblxuXG5cbi8vIGNvbnN0IHJlY29yZCA9ICN7IHo6IDEsIGE6IDIgfTtcblxuLy8gY29uc29sZS5sb2coXG4vLyCgIKAgI1suLi5PYmplY3Qua2V5cyhyZWNvcmQpXSA9PT0gI1tcImFcIiwgXCJ6XCJdXG4vLyApOyAvLyB0cnVlXG5cbi8vIGNvbnNvbGUubG9nKFxuLy8goCCgICNbLi4uT2JqZWN0LnZhbHVlcyhyZWNvcmQpXSA9PT0gI1syLCAxXVxuLy8gKTsgLy8gdHJ1ZVxuXG4vLyBjb25zb2xlLmxvZyhcbi8vIKAgoCAjWy4uLk9iamVjdC5lbnRyaWVzKHJlY29yZClbMF1dID09PSAjW1wiYVwiLCAyXVxuLy8gKTsgLy8gdHJ1ZVxuXG5cblxuLy8gY29uc3QgcmVjID0gI3t6OiAxLCBhOiAyIH1cblxuLy8gLy8gT2JqZWN0LmVudHJpZXMgY2FuIGJlIHVzZWQgdG8gaXRlcmF0ZSBvdmVyIFJlY29yZHMsIGp1c3QgbGlrZSB3aXRoIE9iamVjdHNcbi8vIGZvciAoY29uc3QgW2tleSwgdmFsdWVdIG9mIE9iamVjdC5lbnRyaWVzKHJlYykpIHsgXG4vLyBjb25zb2xlLmxvZyhrZXkpIFxuLy8gfVxuXG5cbmNvbnNvbGUubG9nKEpTT04uc3RyaW5naWZ5KCN7IGE6ICNbMSwgMiwgM10gfSkpO1xuXG5cblxuXG5cblxuXG4iLCJzeW50YXgiOiJoYXNoIiwiZG9tTW9kZSI6ZmFsc2V9)

## 结论

`Record`和`Tuple`提案仍在进行中。目前，它们都是实验性的特性，旨在从根本上解决深度不变性。现在，用户依靠像 Immutable.js 和 Immer 这样的库来处理深度不变性的情况。但是，正如我们之前讨论的，这可能会带来问题。

这种提议设计提供了防止常见编程错误的保证，因为记录和元组中的一切都不像对象或数组。该设计确保记录和元组保持不变。

本质上，与使用`Object.freeze()`相反，记录和元组的结构是有保证的。我们知道，`Object.freeze`只执行一个浅层操作，它也不保证与对象或数组严格相等。因此，有了语言中固有的深度不变性，我们就不必依赖库，库提供了对对象或数组的浅层克隆。

在这个提案的介绍中，我们已经能够涵盖这些数据类型的基本用例，以及我们将如何使用它们的一些例子。要了解更多信息，你可以在 [GitHub](https://github.com/tc39/proposal-record-tuple#javascript-records--tuples-proposal) 上找到该提议的链接，包括规范、食谱和官方教程。

您还可以查看一个[后续提案](https://github.com/tc39/proposal-deep-path-properties-for-record)，它将为记录添加深层路径属性。最后，为了练习我们在本教程中提到的例子，来看看[游乐场](https://rickbutton.github.io/record-tuple-playground/#eyJjb250ZW50IjoiIiwic3ludGF4IjoiaGFzaCIsImRvbU1vZGUiOmZhbHNlfQ==)。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.