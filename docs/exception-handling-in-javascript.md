# JavaScript 中的异常处理

> 原文：<https://blog.logrocket.com/exception-handling-in-javascript/>

错误是编程旅程的一部分。通过制造错误，我们实际上学会了如何不做某事，以及下次如何做得更好。

在 JavaScript 中，当代码语句紧密耦合并且其中一个生成错误时，继续执行剩余的代码语句是没有意义的。相反，我们试图尽可能优雅地从错误中恢复。JavaScript 解释器检查[异常处理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Control_flow_and_error_handling)代码，以防出现此类错误，如果没有异常处理程序，程序将返回导致错误的函数。

对[调用堆栈](https://developer.mozilla.org/en-US/docs/Glossary/Call_stack)上的每个函数重复这一过程，直到找到异常处理程序或到达顶层函数，这导致程序因出错而终止。

一般来说，异常有两种处理方式:

1.  **抛出异常**——如果在运行时出现了无法有效处理的问题，最好抛出异常

    ```
    function openFile(fileName) {     if (!exists(fileName)) {         throw new Error('Could not find file '+fileName); // (1)     }     ... }
    ```

2.  **捕捉异常** —抛出的异常在运行时更有意义的地方被捕捉和处理

    ```
    try {   openFile('../test.js'); } catch(e) { // gracefully handled the thrown expection  } 
    ```

让我们更详细地探究这些行为。

## 抛出异常

如果你长期使用 JavaScript，你可能会看到类似于`ReferenceError: fs is not defined`的东西。这表示通过 throw 语句引发的异常。

### 句法

```
throw «value»;
// Don't do this
if (somethingBadHappened) {
    throw 'Something bad happened';
}

```

对于可以作为异常抛出的数据类型没有限制，但是 JavaScript 有特殊的内置异常类型。其中一个是`Error`，正如你在前一个例子中看到的。这些内置的异常类型给了我们更多的细节，而不仅仅是一条异常消息。

### 错误

`Error`类型用于表示一般异常。这种类型的异常最常用于实现用户定义的异常。它有两个内置属性可以使用。

#### 1.`message`

这就是我们作为参数传递给`Error`构造函数的内容，例如`new Error('This is the message')`。您可以通过`message`属性访问该消息。

```
const myError = new Error(‘Error is created’)
console.log(myError.message) // Error is created
```

#### 2.`stack`

属性返回导致错误的文件的历史(调用栈)。栈顶还包括消息，后面是实际的栈，从最近的/孤立的错误点开始，到最外部负责的文件。

```
Error: Error is created
at Object. (/Users/deepak/Documents/error-handling/src/index.js:1:79)
at Module.*compile (internal/modules/cjs/loader.js:689:30)
at Object.Module.*extensions..js (internal/modules/cjs/loader.js:700:10)
at Module.load (internal/modules/cjs/loader.js:599:32)
at tryModuleLoad (internal/modules/cjs/loader.js:538:12)
at Function.Module._load (internal/modules/cjs/loader.js:530:3)
at Function.Module.runMain (internal/modules/cjs/loader.js:742:12)
at startup (internal/bootstrap/node.js:266:19)
at bootstrapNodeJSCore (internal/bootstrap/node.js:596:3)
```

注意:`new Error('...')`在抛出之前不做任何事情——也就是说，`throw new Error('error msg')`将在 JavaScript 中创建一个`Error`的实例，并停止执行您的脚本，除非您对`Error`做一些事情，比如捕捉它。

## 捕捉异常

现在我们知道了什么是异常以及如何抛出异常，让我们来讨论如何通过捕捉异常来阻止它们使我们的程序崩溃。

### `try-catch-finally`

这是处理异常的最简单的方法。让我们看看语法。

```
try {
    // Code to run
  } catch (e) {
    // Code to run if an exception occurs
  }
  [ // optional
    finally {
      // Code that is always executed regardless of 
      // an exception occurring
    }
  ]

```

在`try`子句中，我们添加了可能会产生异常的代码。如果出现异常，则执行`catch`子句。

有时，无论代码是否会生成异常，都有必要执行代码。然后我们可以使用可选块`finally`。

即使`try`或`catch`子句执行了一条`return`语句，`finally`块也会执行。例如，下面的函数返回 false，因为`finally`子句是最后执行的。

```
function foo() {
  try {
    return true;
  } finally {
    return false;
  }
}

```

我们在无法事先检查代码正确性的地方使用`try-catch`。

```
const user = '{"name": "Deepak gupta", "age": 27}';
try {
  // Code to run
  JSON.parse(params)
  // In case of error, the rest of code will never run
  console.log(params)
} catch (err) {
  // Code to run in case of exception
  console.log(err.message)
}

```

如上所示，在执行代码之前，不可能检查`JSON.parse`来获得 stringify 对象或字符串。

注意:您可以捕获程序员生成的异常和运行时异常，但是您不能捕获 JavaScript 语法错误。

`try-catch-finally`只能捕捉同步错误。如果我们尝试在异步代码中使用它，那么在异步代码完成它的执行之前，`try-catch-finally`可能已经被执行了。

## 如何在异步代码块中处理异常

JavaScript 提供了几种方法来处理[异步代码块](https://blog.logrocket.com/the-visual-learners-guide-to-async-js-62a0a03d1d57/)中的异常。

### 回调函数

对于回调函数(不推荐)，我们通常会收到如下两个参数:

```
asyncfunction(code, (err, result) => {
    if(err) return console.error(err);
    console.log(result);
})

```

我们可以看到有两个论点:`err`和`result`。如果有错误，`err`参数将等于该错误，我们可以抛出该错误来进行异常处理。

重要的是要么在`if(err)`块中返回一些东西，要么将其他指令包装在`else`块中。否则，您可能会得到另一个错误——例如，当您试图访问`result.data`时，`result`可能未定义。

### 承诺

有了承诺——`then`或`catch`——我们可以通过将错误处理程序传递给`then`方法或使用`catch`子句来处理错误。

```
promise.then(onFulfilled, onRejected)

```

也可以用`.catch(onRejected)`而不是`.then(null, onRejected)`添加一个错误处理程序，其工作方式相同。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们来看一个`.catch`拒绝承诺的例子。

```
Promise.resolve('1')
  .then(res => {
      console.log(res) // 1
      throw new Error('something went wrong'); // exception thrown 
})
.then(res => {
      console.log(res) // will not get executed
})
.catch(err => { 
      console.error(err) // exception catched and handled
});

```

### `async`和`await`同`try-catch`

有了`async` / `await`和`try-catch-finally`，处理异常变得轻而易举。

```
async function() {
    try {
        await someFuncThatThrowsAnError()
    } catch (err) {
        console.error(err) 
    }
})

```

## 如何处理未捕获的异常

现在我们已经很好地理解了如何在同步和异步代码块中执行异常处理，让我们来回答本文的最后一个紧迫问题:我们如何处理未捕获的异常？

### 在浏览器中

方法`window.onerror()`导致在运行时发生错误时，在窗口对象上触发错误事件。我们可以使用这个方法来处理未捕获的异常。

`onerror()`的另一个实用模式是在你的站点加载图像出错时显示一条消息。

```
<img src="testt.jpg" onerror="alert('An error occurred loading yor photo.')" /> 
```

### 在 Node.js 服务器上

从`EventEmitter`模块 *派生的流程对象可以订阅事件`uncaughtException`。*

```
process.on("uncaughtException", () => {})`

```

我们可以传递一个回调来处理这个异常。如果我们试图捕获这个未被捕获的异常，进程不会终止，所以我们必须手动完成。

`uncaughtException`只适用于同步代码。对于异步代码，还有一个事件叫做`unhandledRejection`。

```
process.on("unhandledRejection", () => {})

```

永远不要尝试为基本类型`Error`实现一个无所不包的处理程序。这将混淆所发生的一切，并损害代码的可维护性和可扩展性。

## 通过给出错误上下文，立即处理 JavaScript 异常

处理 JavaScript 异常是第一步——实际上修复它们是困难的地方。LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样。LogRocket 允许您重放带有 JavaScript 异常的会话，以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。
 [![](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://logrocket.com/signup/) 
LogRocket 记录控制台日志、页面加载次数、堆栈跟踪、慢速网络请求/响应，带有标题+正文、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

## 关键要点

让我们回顾一下本文中讨论的一些要点。

*   `throw`语句用于生成用户定义的异常。在运行时，当遇到一个`throw`语句时，当前函数的执行将停止，控制权将传递给调用堆栈中的第一个`catch`子句。如果没有`catch`子句，程序将终止
*   JavaScript 有一些内置的异常类型，最著名的是`Error`，它返回错误堆栈和消息
*   `try`子句将包含可能生成异常的代码
*   出现异常时，将执行`catch`子句
*   对于异步代码，最好使用`async-await`和`try-catch`
*   可以捕获未处理的异常，这可以防止应用程序崩溃

如果在整个过程中处理得当，异常处理可以帮助您提高代码的可维护性、可扩展性和可读性。*