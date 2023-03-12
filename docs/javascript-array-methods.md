# 如何实现每一个 JavaScript 数组方法

> 原文：<https://blog.logrocket.com/javascript-array-methods/>

为了在给定的数组上使用一个方法，我们键入`[].methodName`。它们都在`Array.prototype`对象中定义。然而，在这里，我们不会使用这些；相反，我们将从简单的方法开始定义我们自己的版本，并在这些基础上构建，直到我们得到所有的版本。

没有比把东西拆开再组装起来更好的学习方法了。请注意，在我们的实现工作时，我们不会覆盖现有的方法，因为这从来都不是一个好主意(我们导入的一些包可能依赖于它)。此外，这将允许我们比较我们的版本与原始方法的差异。

所以与其写这个:

```
Array.prototype.map = function map() {
 *// implementation*
};
```

我们要这样做:

```
function map(array) {
 *// takes an array as the first argument
 // implementation*
}
```

我们也可以通过使用`class`关键字并扩展`Array`构造函数来实现我们的方法，如下所示:

```
class OwnArray extends Array {
 public constructor(...args) {
   super(...args);
 }

 public map() {
   *// implementation*
   return this;
 }
}
```

唯一的区别是，我们将使用关键字`this`，而不是使用`array`参数。

然而，我觉得这会带来不必要的混乱，所以我们将坚持第一种方法。

解决了这个问题，让我们通过实现最简单的方法开始吧——`forEach`方法！

## 迭代集合

`Array.prototype.forEach`方法接受一个回调函数，并对[数组](https://blog.logrocket.com/writing-for-readability-with-declarative-arrays-64f0b1a807f3/)中的每一项执行该函数，而不会以任何方式改变数组。

```
[1, 2, 3, 4, 5].forEach(value => console.log(value));
```

#### 履行

```
function forEach(array, callback) {
 const { length } = array;

 for (let index = 0; index < length; index += 1) {
   const value = array[index];
   callback(value, index, array);
 }
}
```

我们遍历数组并对每个元素执行回调。这里需要注意的重要一点是，该方法不返回任何东西——因此，在某种程度上，它返回`undefined`。

#### 方法链接

使用数组方法的好处是可以将操作链接在一起。考虑以下代码:

```
function getTodosWithCategory(todos, category) {
 return todos
   .filter(todo => todo.category === category)
   .map(todo => normalizeTodo(todo));
}
```

这样，我们就不必将`map`的结果保存到变量中，通常会得到更好看的代码。

可惜，`forEach`不返回输入数组！这意味着我们不能进行以下操作:

```
*// Won't work!*
function getTodosWithCategory(todos, category) {
 return todos
   .filter(todo => todo.category === category)
   .forEach((value) => console.log(value))
   .map(todo => normalizeTodo(todo));
}
```

这里的`console.log`当然是没用的。

#### 日志记录实用程序功能

我已经编写了一个简单的实用函数，它将更好地解释每个方法做什么:它接受什么作为输入，它返回什么，以及它是否改变了数组。

```
function logOperation(operationName, array, callback) {
 const input = [...array];
 const result = callback(array);

 console.log({
   operation: operationName,
   arrayBefore: input,
   arrayAfter: array,
   mutates: mutatesArray(input, array), // shallow check
   result,
 });
}
```

下面是为我们的`forEach`实现运行的实用函数:

```
logOperation('forEach', [1, 2, 3, 4, 5], array => forEach(array, value => console.log(value)));
```

```
{
  operation: 'forEach',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 1, 2, 3, 4, 5 ],
  mutates: false,
  result: undefined
}
```

由于我们将方法实现为函数，所以我们必须使用下面的语法:`forEach(array, ...)`而不是`array.forEach(...)`。

注意:我还为每种方法创建了测试用例，以确保它们如预期的那样工作——你可以在库中找到它们[。](https://github.com/maciejcieslar/array-methods)

最常用的方法之一就是`Array.prototype.map`。它让我们通过将现有值转换成新值来创建一个新数组。

```
[1, 2, 3].map(number => number * 5);
*// -> [5, 10, 15]*
```

#### 履行

```
function map(array, callback) {
 const result = [];
 const { length } = array;

 for (let index = 0; index < length; index += 1) {
   const value = array[index];

   result[index] = callback(value, index, array);
 }

 return result;
}
```

提供给该方法的回调将旧值作为参数，并返回一个新值，然后将其保存在新数组中的同一索引下，这里称为`result`。

这里需要注意的是，我们返回了一个新的数组；我们不修改旧的。这是一个重要的区别，因为数组和对象在这里是作为引用传递的。如果你对引用和价值的事情感到困惑，[这里有一篇很棒的文章](https://codeburst.io/explaining-value-vs-reference-in-javascript-647a975e12a0)。

```
logOperation('map', [1, 2, 3, 4, 5], array => map(array, value => value + 5));
```

```
{
  operation: 'map',
  input: [ 1, 2, 3, 4, 5 ],
  output: [ 6, 7, 8, 9, 10 ],
  mutates: false
}
```

另一个非常有用的方法是`Array.prototype.filter`。顾名思义，它过滤掉回调返回的值是`false`。每个值都保存在一个新数组中，稍后返回。

```
[1, 2, 3, 4, 5].filter(number => number >= 3);
*// -> [3, 4, 5]*
```

#### 履行

```
function filter(array, callback) {
 const result = [];

 const { length } = array;

 for (let index = 0; index < length; index += 1) {
   const value = array[index];

   if (callback(value, index, array)) {
     push(result, value);
   }
 }

 return result;
}
```

我们获取每个值，并检查所提供的回调是否返回了`true`或`false`，并适当地将该值附加到新创建的数组或丢弃它。

注意，这里我们对`result`数组使用了`push`方法，而不是将值保存在输入数组中的相同索引处。这样，`result`就不会因为丢弃的值而出现空槽。

```
logOperation('filter', [1, 2, 3, 4, 5], array => filter(array, value => value >= 2));
```

```
{
  operation: 'filter',
  input: [ 1, 2, 3, 4, 5 ],
  output: [ 2, 3, 4, 5 ],
  mutates: false
}
```

诚然，`reduce`方法是更复杂的方法之一。然而，其使用的广泛性不能被夸大，因此很好地掌握它是如何工作的是至关重要的。它接受一个数组并输出一个值。从某种意义上来说，它*将*数组降低到那个值。

这个值是如何计算的，确切地说，是需要在回调中指定的。让我们考虑一个例子——对`reduce`最简单的用法，也就是对一组数字求和:

```
 [1, 2, 3, 4, 5, 6, 7, 8, 9, 10].reduce((sum, number) => {
   return sum + number;
 }, 0) *// -> 55*
```

注意这里的回调是如何接受两个参数的:`sum`和`number`。第一个总是前一次迭代返回的结果，第二个是我们当前在循环中考虑的数组元素。

所以在这里，当我们在数组上迭代时，`sum`将包含到循环的当前索引为止的数字的总和，因为每次迭代我们只是将数组的当前值加到它上面。

#### 履行

```
function reduce(array, callback, initValue) {
 const { length } = array;

 let acc = initValue;
 let startAtIndex = 0;

 if (initValue === undefined) {
   acc = array[0];
   startAtIndex = 1;
 }

 for (let index = startAtIndex; index < length; index += 1) {
   const value = array[index];
   acc = callback(acc, value, index, array);
 }

 return acc;
}
```

我们创建两个变量，`acc`和`startAtIndex`，并用它们的默认值初始化它们，分别是参数`initValue`和`0`。

然后，我们检查`initValue`是否未定义。如果是，我们必须将数组的第一个值设置为初始值，为了不重复计算初始元素，将`startAtIndex`设置为`1`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

每次迭代，`reduce`方法将回调的结果保存在累加器(`acc`)中，然后在下一次迭代中可用。对于第一次迭代，累加器被设置为`initValue`或`array[0]`。

```
logOperation('reduce', [1, 2, 3, 4, 5], array => reduce(array, (sum, number) => sum + number, 0));
```

```
{
  operation: 'reduce',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 1, 2, 3, 4, 5 ],
  mutates: false,
  result: 15
}
```

## 搜索

对数组的什么操作比搜索某个特定值更常见？这里有一些方法可以帮助我们做到这一点。

顾名思义，`findIndex`帮助我们找到数组内部给定值的索引。

```
[1, 2, 3, 4, 5, 6, 7].findIndex(value => value === 5); *// 4*
```

该方法为数组中的每一项执行提供的回调，直到回调返回`true`。然后，该方法返回当前索引。如果找不到值，则返回`-1`。

#### 履行

```
function findIndex(array, callback) {
 const { length } = array;

 for (let index = 0; index < length; index += 1) {
   const value = array[index];

   if (callback(value, index, array)) {
     return index;
   }
 }

 return -1;
}
```

```
logOperation('findIndex', [1, 2, 3, 4, 5], array => findIndex(array, number => number === 3));
```

```
{
  operation: 'findIndex',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 1, 2, 3, 4, 5 ],
  mutates: false,
  result: 2
}
```

`find`与`findIndex`的唯一区别在于它返回实际值而不是其索引。在我们的实现中，我们可以重用已经实现的`findIndex`。

```
[1, 2, 3, 4, 5, 6, 7].findIndex(value => value === 5); *// 5*
```

#### 履行

```
function find(array, callback) {
 const index = findIndex(array, callback);

 if (index === -1) {
   return undefined;
 }

 return array[index];
}
```

```
logOperation('find', [1, 2, 3, 4, 5], array => find(array, number => number === 3));
```

```
{
  operation: 'find',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 1, 2, 3, 4, 5 ],
  mutates: false,
  result: 3
}
```

`indexOf`是另一种获取给定值索引的方法。然而，这一次我们将实际值作为参数而不是函数来传递。同样，为了简化实现，我们可以使用之前实现的`findIndex`！

```
[3, 2, 3].indexOf(3); *// -> 0*
```

#### 履行

```
function indexOf(array, searchedValue) {
 return findIndex(array, value => value === searchedValue);
}
```

基于我们正在搜索的值，我们向`findIndex`提供一个适当的回调。

```
logOperation('indexOf', [1, 2, 3, 4, 5], array => indexOf(array, 3));
```

```
{
  operation: 'indexOf',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 1, 2, 3, 4, 5 ],
  mutates: false,
  result: 2
}
```

`lastIndexOf`的工作方式与`indexOf`相同，只是它从数组的末尾开始。我们也(像`indexOf`)将我们正在寻找的值作为参数而不是回调来传递。

```
[3, 2, 3].lastIndexOf(3); *// -> 2*
```

#### 履行

```
function lastIndexOf(array, searchedValue) {
 for (let index = array.length - 1; index > -1; index -= 1) {
   const value = array[index];

   if (value === searchedValue) {
     return index;
   }
 }

 return -1;
}
```

我们对`findIndex`做了同样的事情，但是我们没有执行回调，而是比较了`value`和`searchedValue`。如果比较产生`true`，我们返回索引；如果我们没有找到值，我们返回`-1`。

```
logOperation('lastIndexOf', [1, 2, 3, 4, 5, 3], array => lastIndexOf(array, 3));
```

```
{
  operation: 'lastIndexOf',
  arrayBefore: [ 1, 2, 3, 4, 5, 3 ],
  arrayAfter: [ 1, 2, 3, 4, 5, 3 ],
  mutates: false,
  result: 5
}
```

当我们想要检查一个数组的所有元素是否满足给定的条件时，`every`方法就派上了用场。

```
[1, 2, 3].every(value => Number.isInteger(value)); *// -> true*
```

您可以将`every`方法视为逻辑*和*的数组等价物。

#### 履行

```
function every(array, callback) {
 const { length } = array;

 for (let index = 0; index < length; index += 1) {
   const value = array[index];

   if (!callback(value, index, array)) {
     return false;
   }
 }

 return true;
}
```

我们对每个值执行回调。如果`false`在任一点被返回，我们退出循环，整个方法返回`false`。如果循环在没有触发`if`语句的情况下终止(所有元素产生`true`，该方法返回`true`。

```
logOperation('every', [1, 2, 3, 4, 5], array => every(array, number => Number.isInteger(number)));
```

```
{
  operation: 'every',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 1, 2, 3, 4, 5 ],
  mutates: false,
  result: true
}
```

现在来看看`every` : `some`完全相反的情况。即使回调只有一次执行返回`true`，函数也会返回`true`。与`every`方法类似，您可以将`some`方法视为逻辑*或*的数组等价物。

```
[1, 2, 3, 4, 5].some(number => number === 5); *// -> true*
```

#### 履行

```
function some(array, callback) {
 const { length } = array;

 for (let index = 0; index < length; index += 1) {
   const value = array[index];

   if (callback(value, index, array)) {
     return true;
   }
 }

 return false;
}
```

我们对每个值执行回调。如果`true`在任一点被返回，我们退出循环，整个方法返回`true`。如果循环在没有触发`if`语句的情况下终止(所有元素产生`false`，该方法返回`false`。

```
logOperation('some', [1, 2, 3, 4, 5], array => some(array, number => number === 5));
```

```
{
  operation: 'some',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 1, 2, 3, 4, 5 ],
  mutates: false,
  result: true
}
```

`includes`方法的工作方式类似于`some`方法，但是我们提供了一个值作为参数来比较元素，而不是回调。

```
[1, 2, 3].includes(3); *// -> true*
```

#### 履行

```
function includes(array, searchedValue) {
 return some(array, value => value === searchedValue);
}
```

```
logOperation('includes', [1, 2, 3, 4, 5], array => includes(array, 5));
```

```
{
  operation: 'includes',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 1, 2, 3, 4, 5 ],
  mutates: false,
  result: true
}
```

## 变平

有时我们的数组变得有两到三层深，我们想把它们变平，也就是减少它们嵌套的程度。例如，假设我们想要将所有的值带到顶层。对我们有帮助的是，该语言增加了两个新的内容:`flat`和`flatMap`方法。

`flat`方法通过从嵌套数组中取出值来减少嵌套的深度。

```
[1, 2, 3, [4, 5, [6, 7, [8]]]].flat(1); *// -> [1, 2, 3, 4, 5, [6, 7, [8]]]*
```

由于我们作为参数提供的级别是`1`，因此只有第一级数组被展平；其余的保持不变。

```
[1, 2, 3, [4, 5]].flat(1) *// -> [1, 2, 3, 4, 5]*
```

#### 履行

```
function flat(array, depth = 0) {
 if (depth < 1 || !Array.isArray(array)) {
   return array;
 }

 return reduce(
   array,
   (result, current) => {
     return concat(result, flat(current, depth - 1));
   },
   [],
 );
}
```

首先，我们检查深度参数是否小于`1`。如果是，就意味着没有什么要展平的，我们应该简单地返回数组。

其次，我们检查`array`参数实际上是否属于`Array`类型，因为如果不是，那么扁平化的概念就没有意义，所以我们简单地返回这个参数。

我们使用了以前实现过的`reduce`函数。我们从一个空数组开始，然后取`array`的每个值并展平它。

注意，我们用`(depth - 1)`调用`flat`函数。对于每个调用，我们递减`depth`参数，以免导致无限循环。一旦展平完成，我们将返回值追加到`result`数组中。

注意:`concat`函数在这里用来合并两个数组。该功能的实现解释如下。

```
logOperation('flat', [1, 2, 3, [4, 5, [6]]], array => flat(array, 2));
```

```
{
  operation: 'flat',
  arrayBefore: [ 1, 2, 3, [ 4, 5, [Array] ] ],
  arrayAfter: [ 1, 2, 3, [ 4, 5, [Array] ] ],
  mutates: false,
  result: [ 1, 2, 3, 4, 5, 6 ]
}
```

`flatMap`，顾名思义，是`flat`和`map`的组合。首先，我们根据回调进行映射，然后将结果拉平。

在上面的`map`方法中，对于每个值，我们精确地返回一个值。这样，一个有三个元素的数组在映射后仍然有三个元素。使用`flatMap`，在所提供的回调函数中，我们可以返回一个数组，该数组稍后会被展平。

```
[1, 2, 3].flatMap(value => [value, value, value]); *// [1, 1, 1, 2, 2, 2, 3, 3, 3]*
```

每个返回的数组都被展平，我们得到的不是一个嵌套了三个数组的数组，而是一个包含九个元素的数组。

#### 履行

```
function flatMap(array, callback) {
 return flat(map(array, callback), 1);
}
```

按照上面的解释，我们首先使用`map`，然后将得到的数组平铺一层。

```
logOperation('flatMap', [1, 2, 3], array => flatMap(array, number => [number, number]));
```

```
{
  operation: 'flatMap',
  arrayBefore: [ 1, 2, 3 ],
  arrayAfter: [ 1, 2, 3 ],
  mutates: false,
  result: [ 1, 1, 2, 2, 3, 3 ]
}
```

## 连接、追加和反转数组

正如您刚才看到的，`concat`方法对于合并两个或多个数组非常有用。它被广泛使用，因为它不会改变数组；相反，它返回一个新数组，所有提供的数组都合并到这个新数组中。

```
[1, 2, 3].concat([4, 5], 6, [7, 8]) *// -> [1, 2, 3, 4, 5, 6, 7, 8]*
```

#### 履行

```
function concat(array, ...values) {
 const result = [...array];
 const { length } = values;

 for (let index = 0; index < length; index += 1) {
   const value = values[index];

   if (Array.isArray(value)) {
     push(result, ...value);
   } else {
     push(result, value);
   }
 }

 return result;
}
```

`concat`将一个数组作为第一个参数，将数量不确定的值作为第二个参数，这些值*可以是*数组(也可以是其他任何值，比如原始值)。

首先，我们通过复制提供的数组来创建`result`数组(使用 *spread* 操作符，它将提供的数组的值传播到一个新的数组中)。然后，当我们迭代提供的其余值时，我们检查该值是否是一个数组。如果是，我们使用`push`函数将其值追加到`result`数组中。

如果我们做了`push(result, value)`，我们将只把数组作为一个元素添加。相反，通过使用扩展操作符`push(result, ...value)`，我们将数组的所有值追加到`result`数组中。在某种程度上，我们将数组展平一层深度！

否则，如果当前值不是一个数组，我们也将该值推送到`result`数组——当然，这次没有使用 *spread* 操作符。

```
logOperation('concat', [1, 2, 3, 4, 5], array => concat(array, 1, 2, [3, 4]));
```

```
{
  arrayAfter: [ 1, 2, 3, 4, 5 ],
  mutates: false,
  result: [
    1, 2, 3, 4, 5,
    1, 2, 3, 4
  ]
}
```

`join`方法将一个数组转换成一个字符串，用一个选择的字符串来分隔值。

```
['Brian', 'Matt', 'Kate'].join(', ') *// -> Brian, Matt, Kate*
```

#### 履行

```
function join(array, joinWith) {
 return reduce(
   array,
   (result, current, index) => {
     if (index === 0) {
       return current;
     }

     return `${result}${joinWith}${current}`;
   },
   '',
 );
}
```

我们使用了`reduce`函数:我们将提供的数组传递给它，并将初始值设置为一个空字符串。到目前为止很简单。

对`reduce`的回调是神奇的地方:reduce 遍历提供的数组并将结果字符串组合在一起，在数组的值之间放置所需的分隔符(作为`joinWith`传递)。

`array[0]`值需要一些特殊的处理，因为在那一点上`result`仍然是未定义的(它是一个空字符串)，我们也不希望在第一个元素前面有分隔符(`joinWith`)。

```
logOperation('join', [1, 2, 3, 4, 5], array => join(array, ', '));
```

```
{
  operation: 'join',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 1, 2, 3, 4, 5 ],
  mutates: false,
  result: '1, 2, 3, 4, 5'
}
```

`reverse`方法颠倒数组中值的顺序。

```
[1, 2, 3].reverse(); *// -> [3, 2, 1]*
```

#### 履行

```
function reverse(array) {
 const result = [];

 const lastIndex = array.length - 1;

 for (let index = lastIndex; index > -1; index -= 1) {
   const value = array[index];
   result[lastIndex - index] = value;
 }

 return result;
}
```

想法很简单:首先，我们定义一个空数组，并保存作为参数提供的数组的最后一个索引。我们反向迭代所提供的数组，将每个值保存在数组`result`中的`(lastIndex - index)`位置，然后返回。

```
logOperation('reverse', [1, 2, 3, 4, 5], array => reverse(array));
```

```
{
  operation: 'reverse',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 1, 2, 3, 4, 5 ],
  mutates: false,
  result: [ 5, 4, 3, 2, 1 ]
}
```

## 添加、移除和追加值

`shift`方法将一个数组的值下移一个索引，通过这样做删除第一个值，然后返回该值。

```
[1, 2, 3].shift(); *// -> 1*
```

#### 履行

```
function shift(array) {
 const { length } = array;
 const firstValue = array[0];

 for (let index = 1; index < length; index += 1) {
   const value = array[index];
   array[index - 1] = value;
 }

 array.length = length - 1;

 return firstValue;
}
```

我们首先保存所提供的数组的原始长度及其初始值(当我们将所有内容移动 1 时，我们将删除该值)。然后我们遍历数组，将每个值下移一个索引。一旦完成，我们更新数组的长度并返回曾经的初始值。

```
logOperation('shift', [1, 2, 3, 4, 5], array => shift(array));
```

```
{
  operation: 'shift',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 2, 3, 4, 5 ],
  mutates: true,
  result: 1
}
```

`unshift`方法将一个或多个值添加到数组的开头，并返回该数组的长度。

```
[2, 3, 4].unshift(1); *// -> [1, 2, 3, 4]*
```

#### 履行

```
function unshift(array, ...values) {
 const mergedArrays = concat(values, ...array);
 const { length: mergedArraysLength } = mergedArrays;

 for (let index = 0; index < mergedArraysLength; index += 1) {
   const value = mergedArrays[index];
   array[index] = value;
 }

 return array.length;
}
```

我们首先连接`values`(作为参数传递的单个值)和`array`(我们想要取消移位的数组)。这里需要注意的是，`values`排在第一位；它们将被放置在原始数组的前面。

然后，我们保存这个新数组的长度，并对其进行迭代，将其值保存在原始数组中，并覆盖原来的数组。

```
logOperation('unshift', [1, 2, 3, 4, 5], array => unshift(array, 0));
```

```
{
  operation: 'unshift',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 0, 1, 2, 3, 4, 5 ],
  mutates: true,
  result: 6
}
```

从数组中取出一个值很简单:我们只需使用它的索引来引用它。然而，有时我们想从数组中取出更大的一部分——比方说，一次取出三个或四个元素。这时`slice`方法就派上用场了。

我们指定开始和结束索引，然后`slice`将从原始数组中这些索引处截取的数组交给我们。但是，请注意，end index 参数不是包含性的；在下面的例子中，只有索引`3`、`4`和`5`的元素进入结果数组。

```
[1, 2, 3, 4, 5, 6, 7].slice(3, 6); *// -> [4, 5, 6]*
```

#### 履行

```
function slice(array, startIndex = 0, endIndex = array.length) {
 const result = [];

 for (let index = startIndex; index < endIndex; index += 1) {
   const value = array[index];

   if (index < array.length) {
     push(result, value);
   }
 }

 return result;
}
```

我们从`startIndex`到`endIndex`迭代数组，并将每个值推送到`result`。我们在这里还使用了默认的参数，这样当没有参数被传递时，`slice`方法简单地创建了一个数组的副本。我们通过默认将`startIndex`设置为`0`并将`endIndex`设置为数组的长度来实现这一点。

注意:`if`语句确保我们`push`只有在给定索引下的值存在于原始数组中。

```
logOperation('slice', [1, 2, 3, 4, 5], array => slice(array, 1, 3));
```

```
{
  operation: 'slice',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 1, 2, 3, 4, 5 ],
  mutates: false,
  result: [ 2, 3 ]
}
```

`splice`方法同时从数组中删除给定数量的值，并在它们的位置插入一些其他值。虽然一开始并不明显，但我们可以添加比移除更多的值，反之亦然。

首先，我们指定起始索引，然后指定要删除多少个值，剩下的参数是要插入的值。

```
const arr = [1, 2, 3, 4, 5];

arr.splice(0, 2, 3, 4, 5);

arr *// -> [3, 4, 5, 3, 4, 5]*
```

#### 履行

```
function splice<T>(array: T[], insertAtIndex: number, removeNumberOfElements: number, ...values: T[]) {
  const firstPart = slice(array, 0, insertAtIndex);
  const secondPart = slice(array, insertAtIndex + removeNumberOfElements);

  const removedElements = slice(array, insertAtIndex, insertAtIndex + removeNumberOfElements);

  const joinedParts = firstPart.concat(values, secondPart);
  const { length: joinedPartsLength } = joinedParts;

  for (let index = 0; index < joinedPartsLength; index += 1) {
    array[index] = joinedParts[index];
  }

  array.length = joinedPartsLength;

  return removedElements;
}
```

想法是在`insertAtIndex`和`insertAtIndex + removeNumberOfElements`进行两次切割。这样，我们把`slice`原来的阵列分成了三块。第一个片段(`firstPart`)和第三个片段(这里称为`secondPart`)将进入最终的数组。

我们将在这两者之间插入作为参数传递的值。我们用`concat`方法来做这件事。剩下的中间部分是`removedElements`，我们最后返回。

```
logOperation('splice', [1, 2, 3, 4, 5], array => splice(array, 1, 3));
```

```
{
  operation: 'splice',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 1, 5 ],
  mutates: true,
  result: [ 2, 3, 4 ]
}
```

方法删除数组的最后一个值并返回它。

```
[1, 2, 3].pop(); *// -> 3*
```

#### 履行

```
function pop(array) {
 const value = array[array.length - 1];

 array.length = array.length - 1;

 return value;
}
```

首先，我们将数组的最后一个值保存在一个变量中。然后，我们简单地将数组的长度减一，结果删除了最后一个值。

```
logOperation('pop', [1, 2, 3, 4, 5], array => pop(array));
```

```
{
  operation: 'pop',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [ 1, 2, 3, 4 ],
  mutates: true,
  result: 5
}
```

`push`方法让我们在数组末尾追加值。

```
[1, 2, 3, 4].push(5); *// -> [1, 2, 3, 4, 5]*
```

#### 履行

```
export function push(array, ...values) {
 const { length: arrayLength } = array;
 const { length: valuesLength } = values;

 for (let index = 0; index < valuesLength; index += 1) {
   array[arrayLength + index] = values[index];
 }

 return array.length;
}
```

首先，我们保存原始数组的长度，以及在它们各自的变量中要追加多少值。然后，我们迭代所提供的值，并将它们追加到原始数组中。

我们从`index = 0`开始循环，所以每一次迭代我们都把数组的长度加到`index`。这样，我们不会覆盖原始数组中的任何值，而是实际追加它们。

```
logOperation('push', [1, 2, 3, 4, 5], array => push(array, 6, 7));
```

```
{
  operation: 'push',
  arrayBefore: [ 1, 2, 3, 4, 5 ],
  arrayAfter: [
    1, 2, 3, 4,
    5, 6, 7
  ],
  mutates: true,
  result: 7
}
```

当我们想要用占位符值填充一个空数组时,`fill`方法很有用。如果我们想创建一个具有指定数量的`null`元素的数组，我们可以这样做:

```
[...Array(5)].fill(null) *// -> [null, null, null, null, null]*
```

#### 履行

```
function fill(array, value, startIndex = 0, endIndex = array.length) {
 for (let index = startIndex; index <= endIndex; index += 1) {
   array[index] = value;
 }

 return array;
}
```

所有的`fill`方法实际上只是在指定的索引范围内替换数组的值。如果未提供该范围，该方法将替换数组的所有值。

```
logOperation('fill', [...new Array(5)], array => fill(array, 0));
```

```
{
  operation: 'fill',
  arrayBefore: [ undefined, undefined, undefined, undefined, undefined ],
  arrayAfter: [ 0, 0, 0, 0, 0 ],
  mutates: true,
  result: [ 0, 0, 0, 0, 0 ]
}
```

## 带发电机

最后三种方法的特殊之处在于它们返回生成器的方式。如果您不熟悉生成器，可以跳过它们，因为您可能不会很快使用它们。

`values`方法返回一个生成数组值的生成器。

```
const valuesGenerator = values([1, 2, 3, 4, 5]);

valuesGenerator.next(); *// { value: 1, done: false }*
```

#### 履行

```
function values(array) {
 const { length } = array;

 function* createGenerator() {
   for (let index = 0; index < length; index += 1) {
     const value = array[index];
     yield value;
   }
 }

 return createGenerator();
}
```

首先，我们定义`createGenerator`函数。在其中，我们迭代数组并产生每个值。

`keys`方法返回一个生成数组索引的生成器。

```
const keysGenerator = keys([1, 2, 3, 4, 5]);

keysGenerator.next(); *// { value: 0, done: false }*
```

#### 履行

```
function keys(array) {
 function* createGenerator() {
   const { length } = array;

   for (let index = 0; index < length; index += 1) {
     yield index;
   }
 }

 return createGenerator();
}
```

实现是完全一样的，但是这一次，我们产生一个索引，而不是一个值。

`entries`方法返回一个生成索引-值对的生成器。

```
const entriesGenerator = entries([1, 2, 3, 4, 5]);

entriesGenerator.next(); *// { value: [0, 1], done: false }*
```

#### 履行

```
function entries(array) {
 const { length } = array;

 function* createGenerator() {
   for (let index = 0; index < length; index += 1) {
     const value = array[index];
     yield [index, value];
   }
 }

 return createGenerator();
}
```

同样的实现，但是现在我们将索引和值组合在一起，并在一个数组中生成它们。

## 摘要

有效地使用数组的方法是成为优秀开发人员的基础。熟悉他们内部错综复杂的运作是我所知道的最好的擅长方式。

注意:我没有在这里介绍`sort`和`toLocaleString`，因为它们的实现过于复杂，对我来说，对初学者来说太复杂了。我也没有讨论`copyWithin`，因为它从来没有被使用过——它绝对没有用。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.