# 从头开始实现内置的 JavaScript 方法

> 原文：<https://blog.logrocket.com/implementing-built-in-javascript-methods-from-scratch/>

JavaScript 充满了许多内置方法，这些方法有助于在一行代码中完成大量任务。

您可能在项目中使用过它们，但不知道它们是如何工作的。这篇文章是关于研究这些函数的。

我在采访中看到很多公司都要求从头实现这些功能，所以这就是我们要做的！我们将采用一组您几乎每天都会用到的内置 JavaScript 函数，并从头开始实现它们。

我相信这样做也会让你更有信心像专业人士一样使用这些功能。

## 地图

好老图是高阶函数。它遍历给定数组的元素，对每个元素应用转换函数，将元素添加到新数组中，并返回新数组。

这是函数式编程工具箱中最有用的函数之一。

关于 map 需要注意的重要一点是，它允许您在不修改原始列表的情况下转换整个值列表。

这就是所有神奇的事情发生的过程:

```
const Map = (array, fn) => {
 const answer = [];
 for (let i = 0; i < array.length; i++) {
   answer.push(fn(array[i]));
 }
 return answer;
};
```

## 减少

当您有一个值列表，并希望以一种有意义的方式组合成一个值时，Reduce 是一个非常有用的函数。

reduce 函数迭代给定数组的所有值，并返回单个值。

它不像 map 那样返回新的数组。Reduce 输出单个值，可以是数字、字符串或对象。

让我们看看 reduce 是如何工作的:

```
const Reduce = (list, fn, seed) => {
 let result = seed;
 for (let i = 0; i < list.length; i++) {
   result = fn(answer, list[i]);
 }
 return result;
};
```

因此，reduce 包含一个调用它的列表、一个 reducing 函数、一个累加器和一个种子值。

累加器是一个临时/中间结果，保存 reducer 函数返回的值。返回值再次传递给下一个 reducer 函数，该函数对数组中的下一个值运行。

种子值是累加器的第一个值。

如果没有传递种子值，列表中的第一个元素将作为种子。

```
const list = [1,2,3];
list.reduce(function(accumulator, number) {
   return accumulator + number;
});
// returns 6 since 1 becomes the seed
```

## 过滤器

Filter 就像它的名字一样。它返回从原始数组中筛选出的新元素数组。

我们只需要写一个函数，如果我们想在列表中保留当前项，返回 true，否则返回 false。

```
const Filter = (list, fn) => {
 const result = [];
 for (let i = 0; i < list.length; i++) {
   if (fn(list[i])) {
     result.push(list[i]);
   }
 }
 return result;
};
```

下面是我们如何使用它来过滤掉给定数组中的所有奇数:

```
const filterOddOnesOut = nums => nums.filter( num => num % 2 ===
```

## 去抖

如果您曾经考虑过实现自动完成或键入，您可能已经使用过去抖。这是一种在用户打字时限制网络调用数量的方法。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们从头开始实现它:

```
const debounce = (fn, time) => {
 let setTimeoutId;

 return function() {
     if(setTimeoutId) {
       clearTimeout(setTimeoutId);
     }

     setTimeoutId = setTimeout(() => {
       fn.apply(this, arguments);
       setTimeoutId = null;
     }, time);
 }
}
```

现在，当用户输入时，假设我们连续调用去抖函数:

```
debounce(someFunction, 500);
debounce(someFunction, 500);
debounce(someFunction, 500);
```

只有最后一个会被执行，因为如果在超时前调用了新的，那么`clearTimeout`会取消前面的。

## 约束

对于 JavaScript，我们经常需要与作用域进行交互，尤其是当我们使用 React 时。

范围本质上是我们操作的环境和所有对我们可用的东西。一般来说，像`call`和`apply`这样的函数是用来改变 JavaScript 中当前的执行范围的。

这两种方法不仅改变范围，而且立即执行给定的函数。使用 bind，我们仍然改变了作用域，但是返回了一个以后可以调用的函数。

让我们看看如何从头开始编写 bind。

我们将使用 call 方法来实现这一点:

```
const bind = (fn, context) => {
    return function () {
       fn.call(context);
    }
}
```

## 分类

sort 函数从给定的数组中返回一个排序后的数组。让我们看看排序是如何工作的。

为此，我们将使用合并排序算法。当我们调用`Array.prototype.sort`时，它经常在后台使用归并排序。

合并排序是一种分治算法。在这个算法中，我们基本上接受一个列表，把它分成两半，然后递归地调用合并排序，这反过来也做同样的事情。

基本情况是我们只有一个元素的列表。在这种情况下，我们只需返回列表。

当您逐步完成递归调用时，我们将两个排序列表合并在一起:

```
const mergeSort = list => {
// base case
 if (list.length < 2) {
   return list;
 }
 const length = list.length;
 const middle = Math.floor(length / 2);
 const left = list.slice(0, middle);
 const right = list.slice(middle);

 return merge(mergeSort(left), mergeSort(right));
};
const merge = (left, right) => {

 const results = [];

 while (left.length && right.length) {

   if (left[0] <= right[0]) {
     results.push(left.shift());
   }
   else {
     results.push(right.shift());
   }
 }

 return results.concat(left, right);
};
```

如您所见，我们有一个合并函数，它遍历左侧和右侧的列表，首先插入较小的值，从而得到一个较大的排序列表。

## 结论

所有这些内置的 JavaScript 方法都非常强大。通过从头开始重新实现它们，我们可以更好地有效使用它们。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.