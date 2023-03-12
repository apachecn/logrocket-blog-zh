# 基数排序:不需要比较-日志火箭博客

> 原文：<https://blog.logrocket.com/radix-sort-no-comparisons-required/>

排序(以特定的顺序或次序排列数据)是计算机科学中非常重要的操作，因此，谈论计算机算法而不提及排序算法是非常罕见的。实际上，数据排序的方式有很多种，这也是为什么有这么多排序算法存在的原因——合并排序、快速排序、插入排序、堆排序等等。

与其他排序算法相比，排序算法的效率可能会因数据集的初始条件而异——接近排序、按相反顺序排序、包含重复项等。同样，对于较大的数据集，一些排序算法比其他算法更有效。

然而，在本教程中，我们将考虑一种特殊的排序算法，称为基数排序。我们将看看它是如何工作的，以及如何用 JavaScript 实现它。

## 需要对比吗？

大多数流行的排序算法通过比较数据集中的项目(哪个项目比另一个项目大)来执行排序，这可能是按顺序排列项目时最合理的方法。考虑一下这个数字列表:

```
75, 48, 137, 61, 206, 43, 8, 239, 124
```

例如，如果我们要使用插入排序算法对这个列表进行排序，我们将从第二个项目(48)开始遍历项目，然后通过向后查看项目之前的元素，尝试将每个项目放置在正确的排序位置，这通常需要一些比较。

下面是插入排序每次迭代后的结果(没有显示嵌套迭代的结果)。

```
75, 48, 137, 61, 206, 43, 8, 239, 124
48, 75, 137, 61, 206, 43, 8, 239, 124
48, 75, 137, 61, 206, 43, 8, 239, 124
48, 61, 75, 137, 206, 43, 8, 239, 124
48, 61, 75, 137, 206, 43, 8, 239, 124
43, 48, 61, 75, 137, 206, 8, 239, 124
8, 43, 48, 61, 75, 137, 206, 239, 124
8, 43, 48, 61, 75, 137, 206, 239, 124
8, 43, 48, 61, 75, 124, 137, 206, 239
```

既然大多数高效的排序算法都需要某种形式的项目间比较，那么这是否意味着排序总是需要比较呢？答案是否定的。尤其是当数据集只包含整数时，可以不用比较就对项目进行排序——使用基数排序。

## 基数排序

基数排序通过根据项目的[基数](https://en.wikipedia.org/wiki/Radix)将项目分组到桶中来对项目进行排序。这使得基数排序非常适合于排序那些可以根据其组成数字或字母(如整数、单词等)进行排序的项目。分组到桶中不涉及任何比较。

基数排序算法以数据集每一项的最低或最高有效位开始分组到桶中，然后将桶中的项折叠到一个新的数据集，该数据集包含根据起始位置的位数排序的项，这是第一次迭代。对每个项目中的其他数字重复该过程，直到数据集被完全排序。

### 基数排序示例

使用我们以前的数据集，下面是每次基数排序迭代后的逐步结果，直到数据集被完全排序。

```
*// Initial data set*
[75, 48, 137, 61, 206, 43, 8, 239, 124]

/* START ITERATION(#1) */
*// 1\. Group into buckets based on unit digit
// 2\. Collapse items in buckets to form new data set*
[[], [61], [], [43], [124], [75], [206], [137], [48, 8], [239]]
[61, 43, 124, 75, 206, 137, 48, 8, 239]
/* END ITERATION(#1) */

/* START ITERATION(#2) */
*// 1\. Group into buckets based on tens digit
// 2\. Collapse items in buckets to form new data set*
[[206, 8], [], [124], [137, 239], [43, 48], [], [61], [75], [], []]
[206, 8, 124, 137, 239, 43, 48, 61, 75]
/* END ITERATION(#2) */

/* START ITERATION(#3) */
*// 1\. Group into buckets based on hundreds digit
// 2\. Collapse items in buckets to form new data set*
[[8, 43, 48, 61, 75], [124, 137], [206, 239], [], [], [], [], [], [], []]
[8, 43, 48, 61, 75, 124, 137, 206, 239]
/* END ITERATION(#3) */

*// Final sorted data set*
[8, 43, 48, 61, 75, 124, 137, 206, 239]
```

从上面的逐步过程中可以看出，基数排序在任何时候都不比较项目——不需要比较。但是，上面的例子中有一些需要注意的地方:

#### 只有正整数

数据集中的所有项目都是正整数。需要注意的是，基数排序不能用于对包含非整数(带小数的数字)的数据集进行排序。但是，基数排序可以实现为对包含正整数和负整数的数据集进行排序。

#### 从最低有效数字开始

第一次迭代根据最低有效位将项目分组到桶中，然后继续迭代到每个项目的最高有效位。然而，基数排序可以实现为从最高有效位开始第一次迭代。

#### 使用 10 个铲斗

在每次迭代中，使用 10 个桶，因为我们处理的是十进制(基数为 10)数。存储桶按顺序(0-9)映射到相应的数字。因此，要使用的桶的数量取决于用于项目的数字系统的基数(基数)。

同样重要的是要注意，对于某些迭代，有些桶是空的，这意味着内存被分配了，但从未用于存储任何东西——这是一个很好的优化起点。

### 基数排序算法

现在我们已经看到了一个简单的例子，它演示了如何使用基数排序对数据集进行排序，我们可以继续描述基数排序的完整算法，如下所示:

1.  获取最大数字的最大位数
2.  从 *k* = 0 循环至最大位数。对于每次迭代:
    *   为每个数字创建存储桶(0–9 为 10 个存储桶)
    *   遍历这些项目，根据它们的第 *k* 位数字将它们分组到桶中。
    *   将桶中的项目(按顺序)折叠成平面数组，并用新数组更新当前数组引用
3.  返回排序后的数组

上面的算法需要一些辅助函数来使实现无缝。因此，在我们继续实现基数排序之前，让我们在下一节定义几个辅助函数。

## 基数排序辅助函数

### `asInteger()`

第一个助手函数是`asInteger()`，这是一个简单的实用函数，我们将在后续的助手函数中使用。它以一个数字作为参数，使用`Math.trunc()`移除数字的小数部分，并使用`Math.abs()`返回结果的绝对(正)表示。比如`asInteger(3.226)`应该返回`3`，而`asInteger(-12.035)`应该返回`12`。

```
function asInteger(num) {
  return Math.abs(Math.trunc(num));
}
```

### `digitAtPosition()`

第二个辅助函数是`digitAtPosition()`，它将一个数字(整数)和一个从零开始的位置(整数)作为它的第一个和第二个参数，并返回该位置的数字。个位数在位置`0`，十位数在位置`1`，百位在位置`2`，以此类推。例如，`digitAtPosition(3705, 2)`应该返回`7`，因为 7 是 3705 的百位数。

```
function digitAtPosition(num, pos) {
  return Math.floor(asInteger(num) / Math.pow(10, asInteger(pos))) % 10;
}
```

该函数使用前面定义的`asInteger()`函数来标准化数字输入和位置输入。它使用截断的位置整数来获得 10 的幂，并将其除以该数。最后，它对结果取整并返回除以 10 后的余数。

### `digitsCount()`

第三个辅助函数是`digitsCount()`，它以一个数字(integer)作为参数，并返回该整数的有效位数。例如，`digitsCount(3705)`应该返回`4`，因为 3705 有 4 个有效数字:3、7、0 和 5。

```
function digitsCount(num) {
  return ((num = asInteger(num)) === 0) ? 1 : Math.floor(Math.log10(num)) + 1;
}
```

再次注意，这个函数使用前面定义的`asInteger()`函数来确保数字被适当地截断为正整数。它还使用`Math.log10()`来获得等于截断数的 10 的近似幂。为了得到位数，它使用`Math.floor()`对对数取整，然后将`1`加到结果上。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

使用`Math.log10()`引入了边缘情况。当输入数字为`0`时，返回`-Infinity`。为了处理这个问题，如果截断的数字是 0，`digitsCount()`函数返回`1`,否则，它执行上述计算并返回结果。

### `maxDigitsCount()`

最后一个辅助函数是`maxDigitsCount()`，它接受一个数字(整数)数组，并返回数组中有效位数最高的整数的`digitsCount()`。例如，`maxDigitsCount([12, 5, 3048, 620])`应该返回`4`，因为 3048 是数组中最大有效位数(4)的数字。

```
function maxDigitsCount(nums) {
  return nums.reduce((max, num) => Math.max(max, digitsCount(num)), 0);
}
```

这个函数只是简化传递给它的数字数组，并返回 reducer 函数返回的最终`max`值。它使用 reducer 函数中的`digitsCount()`函数来获取位数，并根据需要更新最大位数。

## 基数排序实现

有了我们的助手函数，我们现在可以实现`radixSort()`函数。但是在我们这样做之前，重要的是要注意我们的基数排序版本只能正确地对包含正整数的数据集进行排序。

也就是说，下面的代码片段显示了基数排序算法的实现:

```
function radixSort(arr) {
  const len = arr.length; *// the length of the array*
  const max = maxDigitsCount(arr); *// the maximum digits count*

  for (let k = 0; k < max; k++) {
    *// initialize the buckets again for grouping
    // create an array of 10 buckets (one for each digit)*
    const buckets = Array(10).fill([]);

    for (let i = 0; i < len; i++) {
      *// get the digit at the kth position of the number
      // and push the number into the corresponding bucket
      // based on that digit*
      buckets[digitAtPosition(arr[i], k)].push(arr[i]);
    }

    *// collapse the items in the buckets to a flat array
    // updating the old array reference with the flat array
    // and continue to the next iteration*
    arr = [].concat(...buckets);
  }

  *// return the final sorted array*
  return arr;
}
```

实现本身非常简单明了。但是，代码中有几个部分值得强调。

### 创建存储桶

在每次迭代开始时重新创建(重置)存储桶。在重新创建时,`buckets`数组由 10 个空数组组成(每个数组对应一个 10 进制数字，0–9)。这里，我们使用`Array.prototype.fill()`用空数组填充槽。然而，这里有一些其他方法可以做到这一点:

```
*// using spread operator and Array.prototype.map()*
const buckets = [...Array(10)].map(() => []);

*// using Array.from() and Array constructor, with map function*
const buckets = Array.from(Array(10), () => []);

*// using Array.from() and array-like object, with map function*
const buckets = Array.from({ length: 10 }, () => []);
```

### 将项目推入桶中

在嵌套的`for`循环中，我们正在获取当前数字的第 *k* 位的数字，并基于该数字将其推入正确的桶中。假设当前数字是 137 ( `arr[i] = 137`)，当前位数是 1 ( `k = 1`)，那么它看起来是这样的:

```
buckets[digitAtPosition(arr[i], k)].push(arr[i]);
*// => buckets**[digitAtPosition(137, 1)]**.push(137);
// => buckets**[3]**.push(137);*
```

### 折叠存储桶中的项目

在每次迭代结束时，桶中的项目被折叠成一个平面数组，并用于更新`arr`。这里我们使用`Array.prototype.concat()`来展平`buckets`数组。注意这里如何使用 spread 运算符是很重要的:

```
const buckets = [[], [61], [], [43], [124], [75], [206], [137], [48, 8], [239]];

/* without spread operator */
[].concat(buckets); *// [[], [61], [], [43], [124], [75], [206], [137], [48, 8], [239]]*

/* with spread operator(...) */
[].concat(...buckets); *// [61, 43, 124, 75, 206, 137, 48, 8, 239]*
```

## 按字母顺序排序

让我们把基数排序向前推进一步。假设我们有一个要按字母顺序排列的单词列表。我们可以使用基数排序来实现。这是我们之前的基数排序函数的修改版本，它按照字母顺序对单词列表进行排序。

```
const radixSortAlphabetical = (() => {
  const PADDING_CHAR = '_';
  const REPLACE_REGEX = /[^a-z]/ig;

  const CHARS = [PADDING_CHAR].concat([
    'a','b','c','d','e','f','g','h','i','j','k','l','m',
    'n','o','p','q','r','s','t','u','v','w','x','y','z'
  ]);

  function _maxStringLength(arr) {
    return arr.reduce((max, str) => Math.max(max || 0, str.replace(REPLACE_REGEX, '').length));
  }

  function _charAtPosition(str, pos, maxlength = pos) {
    str = str.replace(REPLACE_REGEX, '').toLowerCase();
    str += PADDING_CHAR.repeat(maxlength - str.length);
    return str.slice(-(pos + 1))[0];
  }

  return function _radixSort(arr) {
    const len = arr.length;
    const maxlength = _maxStringLength(arr);

    for (let k = 0; k < maxlength; k++) {
      const buckets = {};

      for (let i = 0; i < len; i++) {
        const char = _charAtPosition(arr[i], k, maxlength);
        buckets[char] = (buckets[char] || []).concat(arr[i]);
      }

      arr = CHARS.reduce((arr, char) => arr.concat(buckets[char] || []), []);
    }

    return arr;
  }
})();
```

这里，我们使用了一个立即调用的函数表达式来封装排序逻辑并返回排序函数。逻辑与我们之前处理整数的逻辑非常相似，但是在处理字母时有一些小的不同。以下是所做的一些修改:

### 衬垫琴弦

在每次迭代中，每个字符串都在末尾用填充字符(在本例中为下划线)填充，直到字符串的长度达到数据集中最长字符串的长度。这是为了确保在分组完成之前所有的字符串长度相等。

### 字符序列

字符序列只包含按顺序排列的字母字符(从 a 到 z)。但是，在字符序列中，填充字符(本例中为下划线)位于字母之前。这实际上意味着数据集中的所有字符串必须只包含字母字符，排序才是可预测的。

### 桶对象

这里使用了一个对象将项目分组到桶中。字符用作键，项的数组用作值。如果一个字符的组中没有项目，它将被视为一个空数组。

### 从最后一个字符开始分组

填充字符串后，分组从字符串中的最后一个字符开始，直到第一个字符。请注意，因为较短的字符串在末尾被填充，所以它们的最后一个字符最初将是填充字符。

当所有字符串都只包含字母字符时，我们的`radixSortAlphabetical()`函数工作得最好。当其他字符如数字和符号出现时，它的行为是高度不可预测的。但是，可以改进该函数，以扩展到超出这些限制的范围。

## 结论

基数排序不同于流行的比较排序，它是一种非比较排序算法。在最坏的情况下，基数排序的时间复杂度是***O(k n)***，其中 ***k*** 是迭代次数，而 ***n*** 是项目数，这是线性的，并且比具有对数复杂度的排序更可取。

但是，基数排序的性能会受到项目的位数或组件大小变化的严重影响。基数排序在创建新的数组或对象来对项目进行分组时会占用大量空间。

此外，它不就地对数组进行排序，而是返回数组的排序副本。因此，对于非常大的数据集，需要优化空间，您应该考虑其他排序算法。虽然在本教程中我们能够提出基数排序的基本实现，但是可以改进这些实现，使其超越大多数固有的限制。

感谢您抽出时间阅读本教程。我真的很高兴你坚持到了最后，并希望这是值得的。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.