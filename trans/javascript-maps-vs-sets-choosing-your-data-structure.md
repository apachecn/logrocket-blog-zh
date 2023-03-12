# JavaScript 映射与集合:选择你的数据结构

> 原文：<https://blog.logrocket.com/javascript-maps-vs-sets-choosing-your-data-structure/>

## 介绍

数据的组织方式对于我们有效地对数据执行某些操作或解决与数据相关的某些问题的能力起着至关重要的作用。例如，您可以在常数时间内从双向链表中删除任何项，而如果链表被表示为数组，那么这可能需要线性时间。类似地，当数组被排序时，相对于当数组未被排序时，在对数时间内可以更有效地搜索关键字数组中的关键字的存在。

一些非常流行的编程语言，如 Java 和 Python，提供了大量现成的有用的数据结构实现，而无处不在的 JavaScript 编程语言在这方面显得相当贫乏。然而，像大多数编程语言一样，JavaScript 附带了一些非常基本的数据类型，如数组、字符串、对象、集合、映射等。

### 键控集合

在 ECMAScript 2015 规范更新(通常称为 ES6)之前，JavaScript 提供了`Array`对象作为唯一的标准内置索引集合——尽管还有其他外来对象，如`arguments`和`String`对象，它们的行为类似于对整数索引属性键进行特殊处理的数组，通常称为类似数组的对象，但并不是真正的索引集合。

从 ES2015 开始，JavaScript 中增加了一些新的标准内置类型，例如:

还添加了许多类型化的数组对象，就像数组一样，它们本身也是索引集合。除此之外，该语言还增加了一个名为[键控集合](https://blog.logrocket.com/es6-keyed-collections-maps-and-sets/)的新类别，内置了以下对象类型:

顾名思义，键集合中的每个元素(称为条目)都可以通过某种键来标识，这样集合中的键就不同了，这意味着每个键都精确地映射到集合中的一个条目。如果您熟悉[散列表](https://blog.logrocket.com/know-your-javascript-data-structures/)，那么您可能已经推断出它们在确保平均访问时间与集合中的元素数量呈次线性关系方面的用处。

在这篇文章中，我们将看看如何使用 JavaScript 的`Map`和`Set`对象来有效地解决问题。在我们开始之前，让我们考虑一个简单的问题。

下面是一个示例问题:

> 💡**包含重复的**
> 给定一个整数数组`nums`，如果任何元素在数组中出现至少两次，则返回`true`，如果每个元素都不同，则返回`false`。

在继续之前，暂停一会儿，试着自己解决这个问题。如果对`nums`数组进行排序，会简化解决方案吗？

现在，这里有一个解决问题的方法:

```
function hasDuplicates(nums) { 
  // 1\. Sort the array in-place (sorting makes it easier) 
  nums.sort((a, b) => a - b);

  if (nums.length > 1) { 
    // 2\. Loop through the sorted array until a duplicate is found 
    for (let i = 1, len = nums.length; i < len; i++) { 
      // If a duplicate is found, return immediately 
      if (nums[i] == nums[i - 1]) return true; 
    } 
  }

  // 3\. If it ever gets here, no duplicate was found 
  return false; 
}

```

毫无疑问，对于给定的问题约束，这个解决方案是可行的。其工作原理非常简单——如果整数数组已经排序，那么就有可能在一个[单遍](https://en.wikipedia.org/wiki/One-pass_algorithm)中检查数组中是否存在两个连续的相等的整数。因为不能保证整数数组已经排序，所以在检查重复的整数之前，解决方案首先尝试对数组进行排序。

让我们分析一下我们的解决方案。上述解决方案的[运行时间](https://en.wikipedia.org/wiki/Time_complexity)将随着输入数组大小的增长而线性增长。虽然这不是一件坏事，但也不是太好，因为即使对于预先排序的数组，它仍然需要大量的时间来处理，因为首先要花大量的时间对数组进行排序。

该解决方案还使用 [`Array.prototype.sort`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort) 就地对输入数组[进行排序，从而修改原始输入数组。因此，排序不需要额外的内存。](https://en.wikipedia.org/wiki/In-place_algorithm)

重要的是要注意，如果问题要求输入数组的原始顺序保持不变，那么在使用这个解决方案之前必须制作输入数组的副本。这相当于使用了额外的内存，随着输入数组的增长，内存会以线性方式增长。

现在，这是否是一个可接受的解决方案取决于许多因素，包括但不限于:

*   问题的约束，例如问题输入的最大大小
*   计算资源的限制，例如机器的可用内存
*   可接受的折衷方案，例如接受使用辅助空间，如果这有可能提高运行时间的话，等等。

如果我们确定整数数组可能还没有被排序，并且我们也不介意使用一些辅助空间——假设我们可以获得更快的运行时间——那么这个解决方案不是最好的。随着我们的进展，我们将很快看到，我们实际上可以提出一个解决方案，其运行时间随着输入的大小而线性增长，而不是线性增长。

## 定义和理解`Map`对象

我们可以将 ECMAScript 2015 规范对`Map`对象的定义总结如下:

*   它是键/值对的集合，其中键和值可以是任意的 ECMAScript 语言值
*   它是一个有序的集合，这意味着它的元素的插入顺序很重要，并且在迭代集合时被遵循
*   集合中的键是不同的或唯一的，并且只能出现在映射集合中的一个键/值对中
*   对于 ECMAScript [SameValueZero](https://tc39.es/ecma262/#sec-samevaluezero) 比较算法，集合中的每个键只能出现一次

这意味着任何有效的 JavaScript 值——包括[原始值](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)和对象引用，包括像`NaN`和`undefined`这样不合适的值——都可以用作`Map`对象集合中的键。

## 与`SameValueZero`进行相等比较

为了确定关键字是否已经存在于`Map`对象集合中——换句话说，确保关键字是不同的——使用了 [ECMAScript SameValueZero 比较算法](https://tc39.es/ecma262/#sec-samevaluezero)。

我们使用这种比较算法是因为，如果使用列出的算法之一:

*   严格的相等性比较算法:这将使得无法确定值为`NaN`的键是否已经存在于集合中，因为`NaN === NaN`总是计算为`false`
*   相同值比较算法:这使得确定值为`NaN`的键是否已经存在于集合中成为可能，但是键`+0`和`-0`是不同的键，将被视为不同的键，尽管`+0 === -0`的值总是为`true`

然而，SameValueZero 比较算法的行为类似于 SameValue 比较算法，只是它认为`+0`和`-0`是同一个键。如果 SameValueZero 比较算法被实现为一个 JavaScript 函数，它将如下所示:

```
function SameValueZero(x, y) {
  return x === y || (Number.isNaN(x) && Number.isNaN(y)); 
}

```

## 什么是地图条目？

包含在一个`Map`对象集合中的每个键/值对通常被称为一个入口对象，或入口。entry 对象通常用两个元素的数组表示——更像其他大多数编程语言中的元组[——第一个元素是键，第二个元素是值。](https://blog.logrocket.com/the-javascript-record-and-tuple-proposal-an-overview/)

通用`Map`对象条目的类型定义应该如下所示(在 TypeScript 中):

```
type MapEntry<Key, Value> = [Key, Value];

```

也就是说，您可以像使用数组一样，在`Map`对象条目上使用 JavaScript 语法，如[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)，如下面的 [`for...of`循环](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)示例所示:

```
/**
 * Iterating over entries of `Map` object using a 
 * `for...of` loop — assuming that `map` has been 
 * defined already as a `Map` object. 
 */
for (const [key, value] of map) { 
  console.log(key, value); 
}

```

`Map`和`Set`对象都从它们对应的构造函数的`prototype`对象中继承了一个`entries()`方法。这个`entries()`方法为集合中包含的所有条目返回一个[迭代器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterator_protocol),按照它们的插入顺序。

然而，对于`Map`对象，`entries()`方法返回的迭代器也作为集合的默认迭代器。

## 用 JavaScript 创建地图对象

在本文发表时，创建`Map`对象的唯一方法是调用全局`Map`构造函数。构造函数必须用`new`关键字调用——否则，将抛出一个`TypeError`。

当不带参数调用`Map`构造函数时，返回 0 `size`的空`Map`对象。

```
// Throws a`TypeError` — when invoked without `new` keyword 
const throwTypeErrorMap = Map();

// Creates an empty `Map` object of 0 `size`
const mapA = new Map();

// Omitting the parentheses — when invoked without arguments
// Also creates an empty `Map` object of 0 `size`
const mapB = new Map;

console.log(mapA.size); // 0 
console.log(mapB.size); // 0

```

还可以用可选的`iterable`参数调用`Map`构造函数。当指定时，`iterable`必须是一个 JavaScript 对象:

*   正确实现了[的可迭代协议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol)——很多内置的 JavaScript 对象都实现了这个协议，比如`Array`、`String`、`Set`以及`Map`
*   返回一个迭代器对象，该对象产生一个两元素、类似数组(entry)的对象，其第一个元素是一个将用作`Map`键的值，其第二个元素是与该键相关联的值

如果`iterable`参数不满足这两个要求，将抛出一个`TypeError`——唯一的例外是当`iterable`是值`null`或`undefined`时，在这种情况下，效果与不带任何参数调用`Map`构造函数相同，并且创建一个空的为 0 `size`的`Map`对象。

让我们更加关注上面提到的第二个要求。很明显，新的`Map`对象不能从字符串原语创建，即使`String`对象本身是可迭代的对象。

```
// Map from String — throws a `TypeError` 
const throwTypeErrorMap = new Map("programming");

```

当我们从另一个 iterable 对象创建一个新的`Map`对象时，首先创建一个空的`Map`对象，然后对 iterator 对象产生的每个 entry 对象执行以下步骤，iterable 返回该对象:

1.  从条目对象中提取第一个和第二个元素，分别为`key`和`value`
2.  Check if an entry with `key` already exists in the `Map` object collection using `SameValueZero` comparison
    1.  如果存在，将条目的当前值更新为`value`
    2.  如果它不存在，用那个`key`和`value`向`Map`对象集合的末尾追加一个新条目(如果`key`是`0`，在向集合追加一个新条目之前，将其更改为`+0`)

    const pairs = [[1，3]，[3，3]，[4，2]，[2，2]]；

    // (1)从数组或集合中映射
    //这里从用于创建映射的`pairs`数组和
    中创建一个集合。然而，地图也可以直接从`pairs`数组中创建
    //的。
    const mapA =新地图(新集合(对))；

    console . log(map . size)；//4
    console . log(…地图)；/ [1，3] [3，3] [4，2] [2，2]

    // (2)来自地图
    的地图//新地图包含了原地图
    的所有项目//但是，两个地图是完全不同的对象。
    //把它想象成创建一个地图的克隆。
    const mapB =新地图(mapA)；

    console . log(…mapA)；// [1，3] [3，3] [4，2] [2，2]
    console . log(…mapB)；// [1，3] [3，3] [4，2] [2，2]
    console . log(mapA = = = mapB)；//false
    console . log(mapa . size = = = mapb . size)；//真

    // (3) Map from Object
    //在 ES6 中，添加了`Object.entries()`方法，
    //它返回一个条目数组，表示对象中每个键的
    //键/值对。
    const mapC = new Map(object . entries({
    language:" JavaScript "，
    hello:" world "
    })；

    console . log(mapc . size)；//2
    console . log(…mapC)；// ["语言"，" JavaScript"] ["你好"，"世界"]

既然我们能够创建新的`Map`对象，让我们继续探索它们的实例属性和方法。

## `Map`对象实例的属性和方法

### 检查尺寸

我们已经看过几次`size`属性的实际应用。顾名思义，`size`返回任意时刻`Map`对象中条目的数量。

您可能有兴趣知道,`size`属性是一个访问器属性，而不是数据属性。而且，它只有一个`get accessor`功能，没有`set accessor`功能。这就是它的值不能被赋值操作覆盖的原因。

每当你访问一个`Map`对象的`size`属性时，它的`get accessor`函数就会被调用，这个函数主要计算并返回当前在`Map`对象中的元素(条目)的数量。

### 查找一个键

在一些情况下，只知道一个带有特定关键字的条目是否出现在`Map`对象中就足够了。每个`Map`对象最初都有一个`has()`方法——可以调用该方法来断言在`Map`对象中是否存在具有指定键的条目。如果指定的键存在，`has()`方法返回一个布尔值— `true`，否则返回`false`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
const M = new Map(Object.entries({ 
  language: "JavaScript", 
  hello: "world" 
}));

console.log(M.has("hello")); // true 
console.log(M.has("Hello")); // false 
console.log(M.has("language")); // true 
console.log(M.has("world")); // false

```

除了检查一个键是否存在于一个`Map`对象中，能够读取与那个键相关的条目的值也是非常重要的。因此，每个`Map`对象最初都有一个用于此目的的`get()`方法。

当使用不存在条目的键调用`get()`方法时，它返回`undefined`。

```
const M = new Map(Object.entries({ 
  language: "JavaScript", 
  hello: "world" 
}));

console.log(M.get("hello")); // "world" 
console.log(M.get("Hello")); // undefined 
console.log(M.get("language")); // "JavaScript" 
console.log(M.get("world")); // undefined 

```

虽然`get()`方法为不存在的键返回`undefined`，但是在检查`Map`集合中是否存在键时不应该依赖它，因为集合中的键也可能有值`undefined`。

确定集合中某个键是否存在的最准确的方法是使用`has()`方法。

### 添加、更新和删除条目

从一个`Map`对象中添加、更新或删除一个或多个条目的能力是必不可少的，每个`Map`对象都有`set()`、`delete()`和`clear()`方法。

`set()`方法将一个 JavaScript 值作为其参数，并将该值附加到`Set`对象的末尾，前提是它还不在`Set`对象中。如果指定的值已经存在于`Set`对象中，它将被忽略。

`add()`方法返回带有附加值的同一个`Set`对象，使其服从[方法链接](https://en.wikipedia.org/wiki/Method_chaining)，或者一次调用多个`add()`调用的过程。

另一方面，`delete()`方法将从`Map`对象中移除与指定键相关联的条目——假设在`Map`对象中有这样的条目。如果这个删除操作的结果是从`Map`对象中删除了一个条目，那么它返回`true`；否则返回`false`。

在某些情况下，完全删除给定的`Map`对象中的所有条目可能是有用的。虽然这可以通过对`Map`对象进行多次`delete()`调用来实现，但是如果在一次方法调用中完成，显然会更有意义。

这正是`clear()`方法所做的。调用`clear()`方法清空`Map`对象并返回`undefined`。

```
// Convert object to map 
const M = new Map(Object.entries({ 
  language: "JavaScript" 
}));

console.log(M.size); // 1 
console.log(...M); // ["language", "JavaScript"]

// (1) Add and update some map entries 
M.set("year", 1991); 
M.set("language", "Python");

console.log(M.size); // 2 
console.log(...M); // \["language", "Python"\] ["year", 1991]

// (2) Add or update several values at once (using chaining) 
M.set("version", 3) 
  .set("year", 2000) 
  .set("version", "2.0");

console.log(M.size); // 3 
console.log(...M); // \["language", "Python"\] ["year", 2000] ["version", "2.0"]

// Delete some entries from the map 
console.log(M.delete("Year")); // false 
console.log(M.delete("year")); // true 
console.log(M.delete("year")); // false 
console.log(M.delete("version")); // true

console.log(M.size); // 1 
console.log(...M); // ["language", "JavaScript"]

// Empty the map 
M.clear();

console.log(M.size); // 0

```

### 迭代集合

我们可能想对一个`Map`对象做的另一件事是查看其中的键、值或条目。

您可以使用 [`for...of`循环](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)循环通过`Map`对象中的每个条目(按插入顺序)。这是因为每个 iterable 都有一个 [`Symbol.iterator()`方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/iterator)，该方法返回其默认的[迭代器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterator_protocol)——它负责为循环产生值序列。

除了我们之前看到的`for...of`循环之外，默认迭代器返回的值序列也是[扩展运算符(`...` )](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) 、[、`yield*`语句](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield*)和[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)所基于的序列。

我们已经看到了`entries()`方法，它根据插入顺序返回一个迭代器，用于一个`Map`对象中的所有条目。如前所述，`entries()`方法返回的迭代器也作为`Map`对象的默认迭代器。

也就是说，以下代码片段中显示的两个`for...of`循环是相同的，并且将产生完全相同的值序列:

```
const M = new Map([[1, 3], [3, 3], [4, 2], [2, 2]]);

// (a) Iteration using the default iterator ([Symbol.iterator]) 
for (const [key, value] of M) { 
  console.log(key, value);
}

// (b) Iteration using the `entries()` iterator 
for (const [key, value] of M.entries()) { 
  console.log(key, value); 
} 

```

值得注意的是，除了其`[Symbol.iterator]`方法提供的默认迭代器之外，iterable 对象还可以提供其他迭代器。这适用于 JavaScript 中大多数内置的可迭代对象，包括`Map`对象。

事实上，每个`Map`对象最初都有三个返回迭代器的方法，即:

*   `entries()`
*   `keys()`
*   `values()`

顾名思义，`keys()`方法返回一个迭代器，该迭代器产生与`Map`对象的每个条目相关联的键(按插入顺序)。`values()`方法返回一个迭代器，该迭代器产生与`Map`对象的每个条目相关的值。

下面的代码片段演示了几种方法，我们可以利用一个`Map`对象的 iterable 行为来访问其中每个元素的值或键。

```
const M = new Map([[1, 3], [3, 3], [4, 2], [2, 2]]);

// Using the spread operator (...) to pass values 
// in the Map object as function arguments. 
console.log(...M.values()); // 3 3 2 2

// Using the spread operator in building an array 
// with the unique keys of the Map object. 
const arr = [...M.keys()];

console.log(arr); // [1, 3, 4, 2] 
console.log(arr[0]); // 1 
console.log(arr[3]); // 2 
console.log(arr.length); // 4

// Using destructuring assignment with a `Map` object 
// to extract the first, second and remaining keys. 
const [first, second, ...remainingKeys] = M.keys();

console.log(first); // 1 
console.log(second); // 3 
console.log(remainingKeys); // [4, 2] 
console.log(remainingKeys.length); // 2

// Iteration using a for...of loop 
// to read all the keys in the collection. 
for (const key of M.keys()) { 
  console.log(key); 
}

// 1 
// 3 
// 4 
// 2

```

## 用`forEach()`方法迭代`Map`对象

我们已经能够探索相当多的方法来迭代一个`Map`对象。然而，还有一种非常有用的迭代方法——`forEach()`方法。

就像数组一样，`Map`对象的`forEach()`方法接受一个回调函数作为它的第一个参数，这个函数在`Map`对象的每个条目时被触发。`forEach()`方法还接受一个可选的第二个参数，它表示在执行回调函数时将使用的`this`值。

调用`forEach()`回调函数时，`Map`对象的每个条目都有三个参数:

*   第一个参数是迭代中与当前条目相关联的值
*   第二个参数是与迭代中的当前条目相关联的键
*   第三个参数是`Map`对象本身

```
const M = new Map([[1, 4], [3, 5], [4, 0], [2, 2]]);
M.forEach(function _callback(value, key, map) {
   console.log([...map]);
   const replacement = this[value];
   if (replacement) map.set(key, replacement);
   else if (Number.isInteger(value)) map.delete(key);
}, "hello");

console.log([...M]);

// [[1, 4], [3, 5], [4, 0], [2, 2]]
// [[1, "o"], [3, 5], [4, 0], [2, 2]]
// [[1, "o"], [4, 0], [2, 2]]
// [[1, "o"], [4, "h"], [2, 2]]
// [[1, "o"], [4, "h"], [2, "l"]]
```

明确地说，前面代码片段中的`forEach()`方法调用导致下面的`_callback()`调用:

```
_callback.call("hello", 1, 4, M); 
_callback.call("hello", 3, 5, M); 
_callback.call("hello", 4, 0, M); 
_callback.call("hello", 2, 2, M);

```

## 什么是 JavaScript `Set`对象？

`Set`对象是唯一 JavaScript 值的有序集合。

对于每个`Set`对象，存在以下不变量:

*   它是一个有序的集合:元素的插入顺序很重要，在迭代集合时会遵循这个顺序
*   集合中的值是不同的或唯一的:对于 [ECMAScript SameValueZero 比较算法](https://tc39.es/ecma262/#sec-samevaluezero)，每个值在集合中只能出现一次

任何有效的 JavaScript 值都可以包含在集合中——包括[原始值](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)和对象引用，包括不合适的值，如`NaN`和`undefined`。

## JavaScript 中的映射与集合

由于我们已经在上一节探索了`Map`对象，在我们继续之前，让我们看看它们与`Set`对象的比较。

| 设置对象 | 绘制物体 |
| --- | --- |
| 一维集合:它们只存储唯一的值 | 二维集合:它们将记录存储为键/值对，并且每个键在集合中都是唯一的 |
| 对于每个条目，键和值都指向相同的值或引用 | 对于每个条目，键和值都指向相同的值或引用 |
| 集合对象的默认迭代器(【symbol . iterator】)是从其 values() 方法返回的迭代器 | 默认迭代器是从 entries() 方法中获得的 |
| set() 和 get() 方法没有在 Set.prototype 对象中定义；对象定义了一个 add () 方法 | 在 Set.prototype 对象中定义了 set() 和 get() 方法 |

随着我们对 JavaScript `Set`对象探索的深入，我们会发现`Set`对象与`Map`对象的更多不同之处，以及它们的一些相似之处。

## 创建集合对象

就像`Map`对象一样，创建`Set`对象的唯一方法是调用全局`Set`构造函数。构造函数必须用关键字`new`调用——否则，将抛出一个`TypeError`。当不带参数调用`Set`构造函数时，返回一个空的 0 `size`的`Set`对象。

```
// Throws a `TypeError` — when invoked without `new` keyword 
const throwTypeErrorSet = Set();

// Creates an empty `Set` object of 0 `size` 
const setA = new Set();

// Omitting the parentheses — when invoked without arguments 
// Also creates an empty `Set` object of 0 `size`
const setB = new Set;

console.log(setA.size); // 0 
console.log(setB.size); // 0 

```

还可以用可选的`iterable`参数调用`Set`构造函数。当被指定时，`iterable`必须是一个正确实现[可迭代协议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol)的 JavaScript 对象。许多内置的 JavaScript 对象实现了这个协议——比如`Array`、`String`和`Map`以及`Set`——这意味着这些都是有效的对象，可以作为`iterable`参数传递给`Set`构造函数。

如果`iterable`是值`null`或`undefined`，那么效果就和不带任何参数调用`Set`构造函数一样——会创建一个空的为 0 `size`的`Set`对象。否则，对于没有正确实现 iterable 协议的任何其他`iterable`值，将抛出一个`TypeError`。

与`Map`对象不同，从另一个可迭代对象创建新的`Set`对象具有去杜平的效果，即从可迭代对象的内部[迭代器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/iterator)产生的值中消除冗余的重复值。这是因为`Set`对象的一个重要属性，即它必须只包含不同的离散值。

```
// (1) Set from String 
// Set contains all the unique characters of the string 
const testString = "programming"; 
const uniqueChars = new Set(testString);

console.log(testString.length); // 11 
console.log(uniqueChars.size); // 8 
console.log(...uniqueChars); // p r o g a m i n

// (2) Set from Array 
// Set contains all the distinct elements of the array 
const integers = [1,1,1,3,3,4,3,2,4,2]; 
const distinctIntegers = new Set(integers);

console.log(integers.length); // 10 
console.log(distinctIntegers.size); // 4 
console.log(...distinctIntegers); // 1 3 4 2

// (3) Set from Set 
// New set contains all the items of the original set 
// However, both sets are entirely different objects. 
// Think of it as creating a clone of a set. 
const setA = new Set([1,1,1,3,3,4,3,2,4,2]); 
const setB = new Set(setA);

console.log(...setA); // 1 3 4 2 
console.log(...setB); // 1 3 4 2 
console.log(setA === setB); // false 
console.log(setA.size === setB.size); // true 

```

让我们再来看看前面的示例问题，并利用我们目前所学的关于`Set`对象的知识。这一次，我们将从`nums`数组中创建一个新的`Set`对象，只包含不同的整数(没有重复的)。然后，我们可以通过比较`Set`对象的大小和`nums`数组的长度来确定`nums`数组是否包含重复项。

这是新解决方案的样子:

```
function hasDuplicates(nums) { 
  // Create a new set from `nums` containing only its distinct 
  // integers (i.e de-duplicate the `nums` array). 
  const distinct = new Set(nums);

  // If the size of the distinct set matches the length of the 
  // nums array, then there are no duplicates, and vice-versa. 
  return distinct.size != nums.length; 
}

```

通过使用一个`Set`对象，我们已经能够实现一个解决方案，它的运行时间保证随着输入数组的大小线性增长，即使它需要一些额外的内存来执行。在内存中存储唯一项时，一组有重复项的项比没有重复项的项占用的空间少。

换句话说，就内存使用而言，最坏的情况发生在集合只包含唯一的项目而不包含重复的项目时，在这种情况下，所使用的空间量与项目数相匹配。

## `Set`对象实例的属性和方法

### 检查尺寸

与`Map`对象一样，`size`属性返回任意时刻`Set`对象中值的数量。同样，`Set.prototype`对象的`size`属性是一个访问器属性，而不是数据属性。

`Set`也只有 get 访问器函数，没有 set 访问器函数——因此，它不能被赋值操作覆盖。

每当你访问一个`Set`对象的`size`属性时，它的 get 访问函数将被调用，它将计算并返回当前在`Set`对象中的元素(值)的数量。

### 检查值是否存在

每个`Set`对象最初都有一个`has()`方法，可以调用该方法来断言具有指定值的元素是否出现在`Set`对象中。与`Map`对象一样，`has()`方法返回一个布尔值——如果指定的值存在，则返回`true`，否则返回`false`。

```
const uniqueChars = new Set("programming");

console.log(...uniqueChars); // p r o g a m i n

console.log(uniqueChars.has("p")); // true 
console.log(uniqueChars.has("A")); // false 
console.log(uniqueChars.has("a")); // true 
console.log(uniqueChars.has("t")); // false 

```

由于`Set`对象是一维的(只存储唯一的值)，对它们来说拥有一个`get()`方法是不切实际的，不像`Map`对象。因此，`Set.prototype`对象没有定义一个`get()`方法。

### 添加和删除值

能够在一个`Set`对象中添加或删除一个或多个值是非常重要的，每个`Set`对象最初都有`add()`、`delete()`和`clear()`方法。

`add()`方法将一个 JavaScript 值作为其参数，并将该值附加到`Set`对象的末尾，前提是它还不在`Set`对象中。如果指定的值已经存在于`Set`对象中，它将被忽略。

`add()`方法返回相同的`Set`对象，并增加了值，这使得它可以服从[方法链接](https://en.wikipedia.org/wiki/Method_chaining)，或者熟悉的一次调用多个`add()`调用的过程。

就像使用`Map`对象一样，`Set`对象的`delete()`方法将从`Set`对象中移除与指定值相关联的元素，前提是这样的元素存在于`Set`对象中。如果这个删除操作的结果是从`Set`对象中删除了一个元素，那么它返回`true`；否则返回`false`。

同样，调用`clear()`方法清空`Set`对象并返回`undefined`。

```
// Create new set of integers 
const integers = new Set([1,1,1,3,3,4,3,2,4,2]);

console.log(integers.size); // 4 
console.log(...integers); // 1 3 4 2

// Add some values to the set 
integers.add(5); 
integers.add(1);

console.log(integers.size); // 5 
console.log(...integers); // 1 3 4 2 5

// Add several values at once (using chaining) 
integers.add(7).add(2).add(9);

console.log(integers.size); // 7 
console.log(...integers); // 1 3 4 2 5 7 9

// Delete some values from the set 
console.log(integers.delete(3)); // true 
console.log(integers.delete(8)); // false 
console.log(integers.delete(3)); // false 
console.log(integers.delete(1)); // true

console.log(integers.size); // 5 
console.log(...integers); // 4 2 5 7 9

// Empty the set 
integers.clear();

console.log(integers.size); // 0

```

既然我们已经了解了一些我们可以用`Set`对象做的事情，让我们回到我们之前对原始示例问题的解决方案，看看我们是否可以进一步优化它。(你可能已经猜对了，我们可以。)

仔细检查我们以前的解决方案会发现它做得有点多。它总是考虑输入数组中的每个整数，将它们添加到`Set`对象中(就像多次使用`add()`方法一样),然后检查其大小，这通过遍历每个元素来计数并返回`Set`对象中的元素数量。

这个解决方案的问题是它不保守。很有可能通过考虑数组中的前几个整数来找到重复的整数，因此考虑数组中剩余的整数就变得多余了。

为了优化这个解决方案，我们可以决定不把整数添加到`Set`对象中，只要我们没有遇到已经添加到`Set`对象中的整数，我们就继续。

优化后的解决方案如下:

```
function hasDuplicates(nums) { 
  // 1\. Create an empty set to hold distinct integers
  const distinct = new Set();

  // 2\. Loop through the integers until a duplicate is found
  for (const int of nums) {
    // 2a. If a duplicate is found, return immediately
    if (distinct.has(int)) return true;

    // 2b. Otherwise, add the integer to the distinct set
    distinct.add(int);
  }

  // 3\. If it ever gets here, no duplicate was found
  return false;
}

```

## 迭代键控集合

通常有必要查看包含在`Set`对象中的值。这对于数组或[索引集合](https://tc39.es/ecma262/#sec-indexed-collections)来说是非常容易实现的——因此，我们可以使用属性访问括号符号(`arr[i]`)在某个索引(`i`)处轻松访问数组的元素(`arr`)。

不幸的是，这种元素访问对于`Set()`对象来说是[不可直接实现的，因为`Set`对象是键控集合。](https://tc39.es/ecma262/#sec-keyed-collections)

然而，就像数组和其他可迭代对象一样，您可以使用 [`for...of`循环](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)遍历`Set`对象中每个元素的值(按插入顺序)，或者您可以使用它通过[扩展运算符(`...` )](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) 、 [`yield*`语句](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield*)或[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)产生的值序列。

下面的代码片段演示了几种方法，我们可以利用一个`Set`对象的 iterable 行为来访问其中每个元素的值。

```
const integers = new Set([1,1,1,3,3,4,3,2,4,2]);

// Using the spread operator (...) to pass values
// in the Set object as function arguments.
console.log(...integers); // 1 3 4 2

// Using the spread operator in building an array
// with the unique values from the Set object.
const arr = [...integers];

console.log(arr); // [1, 3, 4, 2]
console.log(arr[0]); // 1
console.log(arr[3]); // 2
console.log(arr.length); // 4

// Using destructuring assignment with a `Set` object
const [first, second, ...remainingIntegers] = integers;

console.log(first); // 1
console.log(second); // 3
console.log(remainingIntegers); // [4, 2]
console.log(remainingIntegers.length); // 2

// Iteration using a `for...of` loop
for (const integer of integers) {
  console.log(integer);
}

// 1
// 3
// 4
// 2

```

就像`Map`对象一样，每个`Set`对象最初都有三个返回迭代器的方法— `values()`、`keys()`和`entries()`。

顾名思义，`values()`方法返回一个新的迭代器，该迭代器产生`Set`对象中每个元素的值(按插入顺序)。由`values()`方法返回的迭代器产生与由`[Symbol.iterator]`方法返回的默认迭代器完全相同的值序列。

出于迭代的目的，`Set`对象的`keys()`方法的行为与`values()`方法完全一样，它们可以互换使用。事实上，`Set`对象的`values`、`keys`和`[Symbol.iterator]`属性最初都指向同一个值(函数)。因此，下面的`for...of`循环将记录完全相同的值序列。

```
const integers = new Set([1,1,1,3,3,4,3,2,4,2]);

// (a) Iteration using the default iterator (`[Symbol.iterator]`)
for (const integer of integers) {
  console.log(integer);
}

// (b) Iteration using the `values()` iterator
for (const integer of integers.values()) {
  console.log(integer);
}

// (c) Iteration using the `keys()` iterator
for (const integer of integers.keys()) {
  console.log(integer);
}

```

一些基本的集合操作可以通过迭代一个或多个`Set`对象来实现。例如，下面的代码片段显示了如何实现`union`和`intersection`集合操作。

```
function union(setA, setB) {
  const setUnion = new Set(setA);

  for (const value of setB) {
    setUnion.add(value);
  }

  return setUnion;
}

function intersection(setA, setB) { 
  const setIntersection = new Set();

  for (const value of setB) {
    if (setA.has(value)) {
      setIntersection.add(value);
    }
  }

  return setIntersection;
}

```

正如`Map`对象一样，`Set`对象也有一个`forEach()`方法，具有类似的调用签名。然而，考虑到`Set`对象的一维性质，调用`forEach()`回调函数时有三个参数:

*   第一个参数是迭代中当前元素的值
*   第二个参数总是与第一个参数相同
*   第三个参数是`Set`对象本身

```
const S = new Set([1,1,1,3,3,4,3,2,4,2]);

S.forEach(function _callback(value, _, set) {
   console.log([...set]);
   const replacement = this[value];
   if (replacement) set.add(${value}${replacement});
   if (Number.isInteger(value)) set.delete(value);
}, "hello");

// [1, 3, 4, 2]
// [3, 4, 2, '1e']
// [4, 2, '1e', '3l']
// [2, '1e', '3l', '4o']
// ['1e', '3l', '4o', '2l']
// ['1e', '3l', '4o', '2l']
// ['1e', '3l', '4o', '2l']
// ['1e', '3l', '4o', '2l']

console.log(...S); // 1e 3l 4o 2l

```

为了清楚起见，前面代码片段中的`forEach()`方法调用会导致下面的`_callback()`调用:

```
_callback.call("hello", 1, 1, S);
_callback.call("hello", 3, 3, S);
_callback.call("hello", 4, 4, S);
_callback.call("hello", 2, 2, S);
_callback.call("hello", '1e', '1e', S);
_callback.call("hello", '3l', '3l', S);
_callback.call("hello", '4o', '4o', S);
_callback.call("hello", '2l', '2l', S);

```

## 偶然`undefined`——什么意思？

当不带任何参数调用`Set`构造函数时，您已经知道它创建了一个空的`Set`对象。然而，这同样不适用于`add()`方法。

当不带任何参数调用`Set`对象的`add()`方法时，它实际上向集合中添加了一个值为`undefined`的元素，如果它还不存在的话。

换句话说，对于给定的`Set`对象`S`，`S.add()`与`S.add(undefined)`完全相同。这就是我想称之为偶然的`undefined`——因为它可能不是有意的。

您可能已经推断出了在没有任何参数的情况下调用`has()`和`delete()`方法时它们的行为。与`add()`方法一样，不带任何参数调用这些方法与用`undefined`作为第一个参数调用它们完全一样。因此，对于给定的`Set`对象`S`，`S.has()`检查`undefined`是否作为值存在于`Set`对象中，而`S.delete()`从集合中移除值`undefined`，如果它存在的话。

```
// Creates an empty set object 
const S = new Set();

// Add some items to the set object 
S.add(5); 
S.add("hello"); console.log(...S); // 5 'hello'

// Adds undefined to the set object 
S.add(); console.log(...S); // 5 'hello' undefined

console.log(S.has(5)); // true 
console.log(S.has("world")); // false

// Logs `true` because `undefined` exists in the set 
console.log(S.has()); // true

// Logs `true` because `undefined` was removed from the set 
console.log(S.delete()); // true

// Logs `false` because `undefined` does not exist in the set 
console.log(S.has()); // false 

```

也就是说，一定要用至少一个参数显式调用`Set`对象的`add()`、`delete()`和`has()`方法，以避免处理意外的`undefined`值。

## 从`Set`对象中移除重复项

在我们结束关于 JavaScript `Set`对象的这一部分之前，让我们看看如何使用我们到目前为止所学的知识来解决之前的示例问题的修改版本。

> 💡**包含重复项(2)** 给定一个整数数组`nums`，返回数组中至少出现两次的元素个数，如果每个元素都不同，则返回`0`。

在继续之前，暂停一会儿，试着自己解决这个问题。解决方案可能有点棘手——如何确保一个重复的整数不会被计数超过一次？

现在，这里有一个解决问题的方法:

```
function countDuplicates(nums) { 
  // Create an empty set for distinct integers 
  // (i.e integers appearing only once) 
  const distinct = new Set();

  // Create an empty set for duplicate integers 
  const duplicates = new Set();

  // Create a variable to keep track of the duplicates count 
  let count = 0;

  // Loop through the integers while counting duplicates 
  for (const int of nums) { 
    // If duplicate integer is found (it has already been counted), 
    // continue with the iteration to the next integer. 
    if (duplicates.has(int)) continue;

    if (distinct.delete(int)) {
      // If integer was successfully deleted from the `distinct` set,
      // that means it has been seen once before. Hence add it, to
      // the `duplicates` set and increment `count`.
      duplicates.add(int);
      count++;
    } else {
      // Integer is being seen for the first time and should be added
      // to the `distinct` set.
      distinct.add(int);
    }
  }

  // Finally, return the duplicates count 
  return count; 
}

```

## `Map`还是`set`？

到目前为止，我们已经能够详细探索 JavaScript `Map`和`Set`对象。但是除此之外，我们还需要能够确定在解决问题时什么时候使用一个而不是另一个就足够了。

前面，我们看到`Set`对象是一维集合，而`Map`对象是二维集合。这可以作为一个线索来决定哪一个最适合某个特定的问题。

换句话说，在除了键之外还需要其他信息的情况下，`Map`对象应该优先于`Set`对象使用。大多数情况下，需要这些附加信息来做出决策或计算程序的最终输出。

为了进一步证明这一点，让我们考虑另一个流行的问题。

> 💡 **Two Sum** 给定一个整数数组和一个特定的目标，如果数组中存在两个数加起来达到目标，则返回`true`，否则返回`false`。

如果要对数组进行排序，那么就有可能提出这个问题的线性时间解决方案，而不需要任何辅助空间。但是由于数组有可能还没有排序，我们需要使用一个`Set`对象来提供一些辅助空间，这样我们就可以在线性时间内解决问题，而不用承担首先排序数组的昂贵任务。

```
function twoSum(nums, target) { 
  // 1\. Create an empty set for complements 
  // (i.e complement = target - num) 
  const complements = new Set();

  // 2\. Loop through integers until a complement is found 
  for (const num of nums) { 
    // 2a. If a complement is found, return immediately 
    if (complements.has(target - num)) return true;

    // 2b. Otherwise, add the integer to the complements set
    complements.add(num);
  }

  // 3\. If it ever gets here, no complement was found 
  return false; 
}

```

这里，如果有两个数加起来达到指定的目标，我们需要返回`true`，否则返回`false`。因此，我们只对数字本身感兴趣，这就是为什么我们只需要使用一个`Set`对象来解决问题。

现在，让我们改为说，我们修改问题返回两个数字的数组索引。我们最好使用一个`Map`对象。这是因为，除了数字本身，我们现在还对它们在数组中对应的索引感兴趣——这两者都不能包含在一个单独的`Set`对象中。

```
function twoSum(nums, target) { 
  // 1\. Create an empty map for integers against indices 
  // (i.e Map<integer, index>) 
  const indices = new Map();

  // 2\. Loop through integers until a complement is found 
  for (let i = 0, len = nums.length; i < len; i++) { 
    // 2a. Compute the complement of the current integer 
    const complement = target - nums[i];

    // 2b. If the complement already exists in the map,
    // get the complement index from the indices map and
    // return early ([complement index, current index])
    if (indices.has(complement)) {
      return [indices.get(complement), i];
    }

    // 2c. Otherwise, add the current integer and index
    // to the indices map
    indices.set(nums[i], i);
   }

  // 3\. If it ever gets here, no complement was found 
  return null; 
}

```

## 其他`Map`和`Set`用途

`Map`和`Set`对象在建模[复合数据结构](https://blog.logrocket.com/the-javascript-record-and-tuple-proposal-an-overview/)以解决特定类型的问题时非常有用。

一般来说，每当您需要能够查找或检查平均访问时间与可用项的数量呈次线性关系(近似恒定时间)的项的存在时，您应该考虑使用`Set`或`Map`对象。

## 使用`Map`对象进行数据缓存

当为了缓存数据而对数据结构建模时，在执行`get()`或`put()`操作之前，`Map`对象可以用作查找表来检查缓存中是否存在关键字。

通常，缓存实现包括某种从缓存中删除项目以释放空间的策略——最流行的缓存回收策略是:最少使用(LFU)和最近最少使用(LRU)。

例如，考虑 LRU 高速缓存的`get()`操作:期望能够在大约恒定的时间内使用其高速缓存键从高速缓存中获取记录，并且在该过程中，该记录被排列为最近使用的记录，因为它是最近被访问的。

为了满足上述期望，需要快速查找缓存键——这就是`Map`对象或任何其他形式的散列表的优势所在。为了保持最近访问记录的正确排序，可以使用优先级队列。

然而，大多数实现都使用双向链表，因为它既能从列表中的当前位置删除记录，又能将其重新插入到列表的开头位置，所有这些都是在恒定的时间内完成的。

一个典型的 LRU 缓存的极简实现蓝图可能看起来有点像这样(为了简洁起见，省略了完整的实现细节):

```
interface ICache<K, V> { 
  get: (key: K) => V; 
  put: (key: K, data: V) => void; 
}

class LRUCache<K, V> implements ICache<K, V> { 
  /** 
   * A DLL is used to maintain the order of the items 
   * in the cache according to how recently they were 
   * used (accessed or added). 
   *
   * Using a DLL makes it possible to remove an item 
   * from any position in the list (in constant time). 
   */ 
  protected list = new DoublyLinkedList<V>();

  /** 
   * A Map object is used as a lookup table to check 
   * for the existence of a key in the cache with an 
   * average access time that is sublinear on the 
   * number of cache items (approximately constant 
   * time). 
   */ 
  protected table = new Map<K, V>();

  /** 
   * @param size {number} The number of items that 
   * can be stored in the cache. 
   */ 
  constructor(protected size: number) {}

  get(key: K): V {} 
  put(key: K, data: V): void {} 
}

```

## 带有地图和集合的图形表示

使用以下两种图形表示形式中的任何一种将问题数据表示为图形时，大多数连通性问题都可以得到更好的解决:

*   邻接矩阵
*   邻接表

对于大多数问题，邻接表表示应该足够了——为此，可以使用`Map`和`Set`对象。

大多数邻接表实现使用数组和/或链表，但是也有可能使用`Map`和`Set`对象。`Map`对象将图形中的每个顶点存储为其键，并将它们在`Set`对象中对应的相邻顶点列表作为其值。

用邻接表表示的无向图的典型实现(使用`Map`和`Set`对象)看起来应该有点像这样:

```
interface IGraph<V> { 
  addVertex: (vertex: V) => void; 
  addEdge: (fromVertex: V, toVertex: V) => void; 
  removeVertex: (vertex: V) => void; 
  removeEdge: (fromVertex: V, toVertex: V) => void; 
}

class UndirectedGraph<V> implements IGraph<V> { 
  /** 
   * A Map object is used to map each vertex in the 
   * graph to a set of vertices that are connected 
   * to it. 
   */ 
  protected list = new Map<V, Set<V>>();

  addVertex(vertex: V): void { 
    if (!this.list.has(vertex)) { 
      // An array can be used to represent the set 
      // of vertices — but in this implementation, 
      // a Set object is used instead. 
      this.list.set(vertex, new Set<V>()); 
    } 
  }

  addEdge(fromVertex: V, toVertex: V): void { 
    this.addVertex(fromVertex); 
    this.addVertex(toVertex); 
    (this.list.get(fromVertex) as Set<V>).add(toVertex); 
    (this.list.get(toVertex) as Set<V>).add(fromVertex); 
  }

  removeVertex(vertex: V): void { 
    if (this.list.has(vertex)) { 
      for (const toVertex of this.list.get(vertex) as Set<V>) {
        this.removeEdge(vertex, toVertex); 
      }
      this.list.delete(vertex); 
    } 
  }

  removeEdge(fromVertex: V, toVertex: V): void { 
    if (this.list.has(fromVertex) && this.list.has(toVertex)) { 
      (this.list.get(fromVertex) as Set<V>).delete(toVertex); 
      (this.list.get(toVertex) as Set<V>).delete(fromVertex); 
    } 
  } 
}

```

## 不相交集和动态连通性

连通性问题的利基可以使用称为不相交集的特殊数据结构来解决。不相交集用于维护一组元素(节点),这些元素(节点)被划分为多个不重叠(不相交)的子集，也称为连通分量。

不相交集合以有效执行两个操作的方式构造，即:

*   `find`:检查元素或节点所属的子集
*   `union`:将两个子集合并成一个子集；也可用于检测无向图中的循环

下面的不相交集实现使用一个`Map`对象来维护它的非重叠子集(详细描述了实现):

```
interface IDisjointSet<T> { 
  find: (node: T) => T; 
  union: (nodeA: T, nodeB: T) => void; 
}

class DisjointSet<T> implements IDisjointSet<T> { 
  /** 
   * A Map object is used to link each node to the 
   * root of its corresponding connected component 
   * subset (using a disjoint-set data structure). 
   */ 
  protected subsets = new Map<T, T | number>();

  addNode(node: T): void { 
    if (!this.subsets.has(node)) { 
      this.subsets.set(node, -1); 
    } 
  }

  find(node: T): T { 
    let root = node;

    while (true) {
      const parent = this.subsets.get(root) as T;

      if (!this.subsets.has(parent)) {
        if (node !== root) {
          this.subsets.set(node, root);
        }

        return root;
      }

      root = parent;
    }
  }

  union(nodeA: T, nodeB: T): void { 
    const rootA = this.find(nodeA); 
    const rootB = this.find(nodeB);

    const sizeA = this.subsets.get(rootA) as number;
    const sizeB = this.subsets.get(rootB) as number;
    const sizeAB = sizeA + sizeB;

    if (sizeA < sizeB) {
      this.subsets.set(rootB, rootA);
      this.subsets.set(rootA, sizeAB);
    } else {
      this.subsets.set(rootA, rootB);
      this.subsets.set(rootB, sizeAB);
    }
  }

  isConnected(nodeA: T, nodeB: T): boolean { 
    return this.find(nodeA) === this.find(nodeB); 
  }
}

```

## 结论

JavaScript 中的映射和集合对于许多应用程序来说非常方便，当试图有效地解决许多问题时——尤其是当需要高效的查找时。事实上，它们是 JavaScript 的专用散列表实现，类似于 Java 中的`HashMap`和`HashSet`类型——尽管有一些细微的区别。

为了保证安全的垃圾收集，可以考虑使用更加严格的 [`WeakMap`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap) 和 [`WeakSet`键控收集。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakSet)

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.