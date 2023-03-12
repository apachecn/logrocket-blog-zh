# WeakMap 和 WeakSet:理解 JavaScript 弱引用

> 原文：<https://blog.logrocket.com/weakmap-weakset-understanding-javascript-weak-references/>

由于 JavaScript 语言的设计方式，它不经常使用弱引用。然而，在某些情况下，它们可能被证明是至关重要的，例如当开发人员必须存储额外的数据并且需要自动管理内存使用时。

在这篇文章中，我们将了解 JavaScript 中的这些弱引用，以及如何通过利用语言中的两个对象来使用它们:`WeakMap`和`WeakSet`。

## 理解强引用

让我们先来看看 JavaScript 中什么是正常的或强引用。按照最简单的定义，强引用是将对象保存在内存中的引用。让我们在实践中看一看，以理解我们在谈论什么:

```
let dog = { name: "badger" };

const pets = [dog];

dog = null;

console.log(pets); // [{ name: "badger" }]

```

通过将一个变量创建为一个对象，我们可以将该对象放入一个数组中，并通过将它的值设置为`null`，从我们创建的变量中删除对原始对象的引用。

虽然我们不能再通过`dog`变量访问对象，因为在`pets`数组和对象之间有一个强引用，但是对象被保存在内存中，可以通过`pets[0]`访问。

换句话说，强引用防止通过垃圾回收从内存中移除对象。

## 理解弱引用

简而言之，弱引用是对一个对象的引用，如果它是内存中对该对象的唯一引用，那么它不会阻止垃圾收集。

正常引用(被认为是强引用)会阻止对象的垃圾收集，即使它是引用它的唯一对象；这不是弱引用的情况。

让我们把这个理论应用到实践中，用前面的强引用的例子，把它放到弱引用的上下文中。现在忽略`WeakMap`的使用；我们将在本文后面更深入地解释这一点。现在，让我们看看弱引用行为:

```
let pets = new WeakMap();
let dog = { name: "badger" };

pets.set(dog, "okay");
console.log(pets); // WeakMap{ {...} -> "Okay" } <= dog set to the WeakMap

dog = null; // Overwrite the reference to the object
console.log(pets); // WeakMap(0) <= dog has been garbage collected.

```

通过利用`WeakMap`和随之而来的弱引用，我们可以看到这两种类型的引用之间的区别。虽然对原始`dog`对象的强(普通)引用仍然存在，但是`dog`对象仍然存在于`WeakMap`中，我们可以毫无问题地访问它。

但是，当我们通过将变量重新分配给`null`来覆盖对原始`dog`对象的引用时，内存中对原始对象的唯一引用是来自我们创建的`WeakMap`的弱引用。

因为它是一个弱引用，所以它不会阻止垃圾收集的发生。这意味着当[JavaScript 引擎](https://blog.logrocket.com/tag/vanilla-javascript/)再次运行垃圾收集过程时，`dog`对象将从内存和我们分配给它的`WeakMap`中删除。

要注意的关键区别是，强引用可以防止对象进行垃圾回收，而弱引用则不能。

默认情况下，JavaScript 对其所有引用使用强引用，使用弱引用的唯一方式是使用`WeakMap`或`WeakSet`。

## 什么是垃圾收集？

虽然垃圾收集是一个详细而复杂的主题，但在讨论引用时理解它是很重要的。

[Garage collection 是由 JavaScript 引擎](https://blog.logrocket.com/javascript-garbage-collection-browser-vs-server/)控制的自动化流程。当一个值可到达时，它被保证存储在内存中而不是被垃圾收集，并且有两种方法可以认为一个值是可到达的。

首先，它们是可达值基本集合的一部分，如全局变量、当前执行的函数及其局部变量/参数，以及更多的内部值。

另一种是通过引用或引用链从根到达任何值。例如，想象我们在一个全局变量中创建一个对象；这是全局空间可达的，因此被认为是可达的。

现在，如果我们创建另一个对象并从我们创建的全局对象引用它，它也是可达的，因为它是通过全局对象引用的。

然而，如果我们通过将全局对象设置为`null`来移除它，那么我们可以通过引用访问的对象突然变得不可访问，因此它将被垃圾收集。

这特别引用了强引用，因为它们是 JavaScript 中的默认引用。但是，这同样适用于弱引用，唯一的例外是，如果对一个对象的唯一引用是弱引用，它不会阻止垃圾回收，并且该对象会被移除。

这是对垃圾收集如何工作的高级概述；本质上，如果某个东西不可达，它就被从内存中删除，这样内存就可以用在其他位置。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## `Sets`对`WeakSets`

[根据 MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) ，“`Set`对象是值的集合。您可以按插入顺序循环访问集合中的元素。`Set`中的一个值只能出现一次；这在`Set`的收藏中是独一无二的。"

简单地说，`Set`就像一个只能包含唯一值的数组，但是我们仍然可以像使用 for 循环和`.forEach`这样的方法遍历数组一样遍历它。

类似于`Set`，`WeakSet`是彼此唯一的对象的集合，但不同之处在于`WeakSet`只能存储对象，不能包含任何类型的任意值，如字符串或数字。

最终，顾名思义，`WeakSets`确实是弱的，这意味着它们使用弱引用。

同样值得注意的是使用弱引用的一个有趣的副作用是`WeakSet`是不可枚举的。这意味着无法循环遍历其中包含的项，因为集合中没有存储当前对象的列表；它们被弱引用，并且可以在任何时候被删除。

下面是一个使用中的`WeakSet`的例子，以及我们可以调用它的方法:

```
const pets = new WeakSet();
const cat = {name: "fluffy"};
const dog = {name: "badger"};

pets.add(cat);
pets.add(dog);

pets.has(cat);    // true
pets.has(dog);    // true

pets.delete(cat); // removes cat from the set
pets.has(cat);    // false, cat has been removed
pets.has(dog);    // true, dog is retained

```

## `Maps`对`WeakMap`

[根据 MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) ，`Map`对象保存键值对，并记住键的原始插入顺序。任何值(对象和原始值)都可以用作键或值。

这意味着`Map`就像一个对象，我们可以在其中存储键-值对，并通过键访问包含在`Map`中的值。然而，与 JavaScript 中的标准对象不同，我们必须使用`.get()`方法来访问值。

与`Map`相比，`WeakMap`非常相似，但是它保存的引用是弱引用，这意味着如果它们在其他地方没有被强引用，它不会阻止垃圾收集删除它引用的值。

此外，`WeakMap`由于弱引用而具有不可枚举的副作用。

最后，我们必须使用对象作为键，但是值可以是任意值，比如字符串或数字。下面是一个使用`WeakMaps`的例子，以及我们可以对它使用的方法:

```
const wm1 = new WeakMap();
const wm2 = new WeakMap();

const obj1 = {};
const obj2 = window;

wm1.set(obj1, 100);
wm1.set(obj2, 'Hello');
wm2.set(obj1, obj2); // You can set the value to be anything including an object or function
wm2.set(obj2, undefined); // Or, undefined
wm1.set(wm2, wm1); // Or, even a WeakMap itself

wm1.get(obj1); // 100

wm1.has(obj1); // true
wm1.delete(obj1);
wm1.has(obj1); // false

```

## 结论

在结束之前，让我们考虑弱引用的一个潜在用例，以及本文中涉及的两个对象。

如果您需要临时存储额外的数据，并且不想担心清理内存或如何删除对象，那么使用弱引用绝对是一个救星。

但是，在 JavaScript 中不太可能经常需要使用`WeakMaps`、`WeakSets`，甚至是弱引用。

在偶然的情况下，知道它们很方便，了解它们的基础知识也很棒，但是在大多数情况下，使用正常的(强)参考。

我希望这篇关于 JavaScript 中弱引用的文章对你有所帮助，如果有帮助的话，[请考虑在 Twitter](https://twitter.com/MrConerMurphy) 上关注我，我在那里发布了关于 JavaScript 生态系统的有用且可行的技巧和内容。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.