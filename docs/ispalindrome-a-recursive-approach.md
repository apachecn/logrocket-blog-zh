# isPalindrome():递归方法

> 原文：<https://blog.logrocket.com/ispalindrome-a-recursive-approach/>

回文是一个字符序列，向前和向后读是一样的。这个字符序列可以是单词、短语、数字等。例如，单词`rotor`即使在倒着读字符时也保持不变。

在本教程中，我们将编写一个名为`isPalindrome(chars)`的简单函数，它将一个字符序列作为输入，如果该序列是回文，则返回`true`，如果不是，则返回`false`。

我们将使用递归在 JavaScript 中实现该函数的算法，但它也可以在您选择的任何其他语言中实现。

## 规范化字符串

首先，让我们假设传递给函数的字符序列是一个`string`。该字符串可能包含非字母数字字符，如空格、下划线等。在这种情况下，需要对字符串进行清理和规范化。

因此，对于大多数算法，逻辑上的第一步是从字符串中删除所有非字母数字字符，并将字符串转换为小写。例如，这使得可能包含空格的回文短语也可以通过检查。

在 JavaScript 中，我们可以使用这个正则表达式(`/[^a-z0-9]/i`)从字符串中去除非字母数字字符。给定一个字符串`string`，下面是我们如何得到它的规范化形式:

```
// remove non-alphanumeric characters and
// change the string to lowercase
string.replace(/[^a-z0-9]/i, '').toLowerCase()
```

## 流行算法

有很多算法可以使用内置的语言方法和循环来检查一个字符串是否是回文。这里有两个最受欢迎的:

### 反向字符串比较

最简单的算法是将字符串与其反向字符串进行比较。如果它们匹配，字符串就是一个回文；否则，就不是。这个算法的实现可以使用内置的 JavaScript 方法和实用程序来实现。

算法如下:

*   **反转规范化字符串:**创建规范化字符串的副本，反转字符。JavaScript 字符串没有内置的反转机制，但是数组有。所以，我们用一点小技巧将字符串转换成它的字符数组，反转数组，并将反转数组中的字符粘回到字符串
*   **比较字符串:**比较反转的字符串和正常化的字符串，并根据比较结果返回一个布尔值，如果匹配则返回`true`，否则返回`false`

下面是该算法的实现:

```
function isPalindrome (str) {
  // remove non-alphanumeric characters and
  // change the string to lowercase
  str = str.replace(/[^a-z0-9]/i, '').toLowerCase();

  // compare the string to the reversed string (if not empty)
  // `Array.from(str)` is ES6 syntax for creating array of string characters.
  // The ES5 equivalent will be to use: `str.split('')`
  return (str.length > 0) && Array.from(str).reverse().join('') === str;
}
```

### 带字符比较的循环

另一种非常流行的算法是循环遍历字符串的字符，从第一个字符开始直到中点的字符，将每个字符与字符串末尾相应位置的字符进行比较。

算法如下:

下面是该算法的实现:

```
function isPalindrome (str) {
  let len = 0;

  // remove non-alphanumeric characters and
  // change the string to lowercase
  // and get the length of the string
  str = str.replace(/[^a-z0-9]/i, '').toLowerCase();
  len = str.length;

  // calculate the string midpoint position and
  // loop through the characters up to the midpoint
  // comparing characters in corresponding positions
  // from the start of the string and the end of the string
  for (let i = 0, mid = len >> 1; i < mid; i++) {
    if (str[i] !== str[len - i - 1]) return false;
  }  

  // if execution reaches here, the character comparisons matched
  // and the string (if not empty) must be a palindrome
  return len > 0;
}
```

## 递归算法

您可能已经知道，许多可以用循环实现的算法也可以用某种形式的递归来实现。让我们看看如何使用递归来重新实现`isPalindrome()`函数。

### 界限条件

对于我们的递归解决方案，我们可以确定两个导致递归停止并立即返回结果的终止条件:

*   首先，我们知道如果字符串只包含一个字符，那么它应该被认为是一个回文。因此，合理的终止条件应该是字符串长度小于或等于 1 ( `<=1`)，为此我们返回`true`。
*   第二，我们知道如果第一个和最后一个字符不匹配，字符串不能被认为是一个回文。因此，递归应该被终止，函数应该返回`false`。

### 基本实现

对于递归解决方案的基本实现，当用给定的字符串调用函数时，按顺序执行以下步骤:

1.  用字符串的规范化形式替换它的值
2.  存储字符串的长度(终端条件所需的
**   检查字符串是否满足任何终止条件；如果是，从函数返回适当的结果*   如果上面第 3 步中的条件都不满足，那么使用原始字符串的子字符串作为参数(*没有第一个和最后一个字符*)再次调用函数，循环继续*

 *上面描述的实现如下所示:

```
function isPalindrome (str) {
  // remove non-alphanumeric characters and
  // change the string to lowercase
  str = str.replace(/[^a-z0-9]/i, '').toLowerCase();

  // and get the length of the string
  const len = str.length;

  if (len <= 1) return true;
  if (str[0] !== str[len - 1]) return false;

  // proper tail call optimized recursion
  return isPalindrome(str.slice(1, -1));
}
```

### 实施改进

我们的函数按预期工作，但仍有一些问题需要解决，我们可以做一些优化来进一步改进它:

*   首先，当传递一个空字符串时，我们的函数当前返回`true`而不是`false`
*   其次，对于函数的每次调用，我们都试图再次规范化输入字符串，即使它在第一次调用中已经被规范化了。此外，我们在规范化过程中扫描字符串以查找正则表达式的匹配，对于较长的字符串，这可能会稍微贵一些

我们可以使用一个立即调用的函数表达式(IIFE)来返回一个`isPalindrome()`函数，它实现了这些问题的解决方法。

在返回的`isPalindrome()`函数中，我们将只对字符串进行一次规范化，如果规范化的字符串为空，我们将立即返回`false`。否则，我们将把规范化的字符串传递给一个内部递归`_isPalindrome()`函数，这个函数只能通过闭包在 IIFE 的范围内访问。

技术术语已经说得够多了——下面是之前的`isPalindrome()`函数的修改版本，并进行了一些优化:

```
const isPalindrome = (() => {
  /**
   * This function is returned immediately
   * from the invocation of the outer arrow function
   * and is assigned to the `isPalindrome` identifier.
   */
  return function isPalindrome (str) {
    // remove non-alphanumeric characters and
    // change the string to lowercase
    str = str.replace(/[^a-z0-9]/i, '').toLowerCase();

    // call the recursive _isPalindrome function with string (if not empty)
    // and return the result
    return (str.length > 0) && _isPalindrome(str);
  };

  /**
   * Internal recursive `_isPalindrome()` function
   * optimized for recursion with proper tail call.
   *
   * A single reference to this function is created and stored
   * after the immediate invocation of the outer arrow function,
   * not accessible outside the scope of the outer arrow function,
   * but accessible to `isPalindrome()` via closure.
   */
  function _isPalindrome (str) {
    const len = str.length;

    if (len <= 1) return true;
    if (str[0] !== str[len - 1]) return false;

    // proper tail call
    return _isPalindrome(str.slice(1, -1));
  }
})();
```

### 进一步优化

到目前为止，我们的递归解决方案工作得很好，并且已经针对尾部调用消除进行了优化([适当的尾部调用](https://webkit.org/blog/6240/ecmascript-6-proper-tail-calls-in-webkit/))。尾部调用优化是 ES6 规范中 JavaScript 函数的一个新增功能，旨在消除 JavaScript 引擎为递归函数创建过多堆栈帧的问题。

就支持而言，尾调用消除在主流浏览器中是落后的。在撰写本文时，Safari 是唯一一个提供合理支持的浏览器。

然而，如果我们是偏执狂，想要一个优化版本的递归函数，可以在所有浏览器上运行，我们可以[将我们的函数包装在一个蹦床](https://blog.logrocket.com/using-trampolines-to-manage-large-recursive-loops-in-javascript-d8c9db095ae3/)中。一个 trampoline 可以用来包装一个函数，这样它就可以像尾部调用优化一样运行。

trampoline 是一个高阶函数——它接受递归函数作为其参数，并返回另一个函数。返回的函数使用一个`while`循环来重复调用从上一次函数调用返回的函数(从递归函数开始)，直到不再返回一个函数。

这是一个典型的蹦床:

```
const trampoline = fn => (...args) => {
  let result = fn(...args);
  while (typeof result === 'function') {
    result = result();
  }
  return result;
}
```

为了让蹦床和我们的递归函数一起工作，我们必须从我们的递归函数中返回一个函数。所以代替这个的是:

```
{
  /* other code here */
  return _isPalindrome(str.slice(1, -1));
}
```

我们会有这个:

```
{
  /* other code here */
  // return a function that calls the recursive function
  return () => _isPalindrome(str.slice(1, -1));
}
```

下面的代码片段显示了使用蹦床的递归函数的新的优化版本:

```
const isPalindrome = (() => {
  return function isPalindrome (str) {
    str = str.replace(/[^a-z0-9]/i, '').toLowerCase();
    // wrap the recursive _isPalindrome function with _trampoline()
    return (str.length > 0) && _trampoline(_isPalindrome)(str);
  };

  // trampoline() — higher-order function
  function _trampoline (fn) {
    return function _trampolined (...args) {
      let result = fn(...args);
      while (typeof result === 'function') {
        result = result();
      }
      return result;
    }
  }

  function _isPalindrome (str) {
    const len = str.length;

    if (len <= 1) return true;
    if (str[0] !== str[len - 1]) return false;

    // return a function that calls the recursive function
    return () => _isPalindrome(str.slice(1, -1));
  }
})();
```

## 结论

实际上来说，使用`isPalindrome()`不太可能像使用典型的递归函数`factorial()`那样遇到堆栈溢出问题。

因此，我们在本教程中为`isPalindrome()`函数提出的递归解决方案似乎不会从所使用的优化技术中受益多少。然而，这并不是要打击你或者贬低我们的努力，因为我们在这里强调的优化技术可以用来延迟大多数递归函数的堆栈溢出。

感谢您抽出时间阅读本教程。我真的很高兴你坚持到了最后，并希望这是值得的。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.*