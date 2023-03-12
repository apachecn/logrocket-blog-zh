# 如何写一个声明性的 JavaScript promise wrapper-log rocket 博客

> 原文：<https://blog.logrocket.com/write-declarative-javascript-promise-wrapper/>

JavaScript 是单线程编程语言，这意味着它只能同步执行代码，或者一次从上到下执行一行代码。然而，[异步编程](https://blog.logrocket.com/understanding-asynchronous-javascript/)被引入来解决这个问题。

这个核心 JavaScript 概念使一个函数能够在等待其他函数完成执行的同时执行。我们使用异步函数对后端进行 API 调用。我们还使用它们来读写文件或数据库。这个概念对于服务器端开发人员和客户端开发人员来说都很方便。

在本指南中，我们将演示如何用 JavaScript 编写声明性异步函数调用。我们还将展示它如何有助于使我们的代码更具可读性和更易于维护。

*向前跳转:*

## 声明式编程

在深入研究代码之前，让我们回顾一下声明式编程模式。

声明式编程是一种编程范式，它通常显示代码的逻辑，但不显示实现逻辑的步骤。对于这种类型的编程，通常看不出幕后发生了什么。

相反，命令式编程需要编写一步一步的代码，每一步都有详细的解释。这可以为将来可能需要处理代码的开发人员提供有用的背景知识，但是这会导致代码非常长。命令式编程通常是不必要的；这取决于我们的目标。

使用内置的 JavaScript 方法可以实现声明性编程。声明式编程允许我们编写可读性更强的代码，因此也更容易理解。

例如，使用声明式编程，我们不需要使用`for`循环来迭代数组。相反，我们可以简单地使用内置的数组方法，比如`map()`、`reduce()`和`forEach()`。

下面是一个命令式编程示例，展示了一个使用递减`for`循环反转字符串的函数:

```
const reverseString = (str) => {
    let reversedString = "";

    for (var i = str.length - 1; i >= 0; i--) { 
        reversedString += str[i];
    }
    return reversedString; 
}

```

但是，当我们只用两行代码就可以实现相同的解决方案时，为什么要写十行代码呢？

下面是相同代码的声明式编程版本，使用 JavaScript 内置数组方法:

```
const reverseString = (str) => {
  return str.split("").reverse().join("");  
} 

```

这个代码片段使用两行代码来反转一个字符串。它非常简短，开门见山。

## JavaScript 中的承诺是什么？

一个[承诺](https://blog.logrocket.com/understanding-promise-all-in-javascript/)是一个 JavaScript 对象，包含一个异步函数的结果。换句话说，它表示异步函数中已经完成或失败的任务。

```
const promise = new Promise (function (resolve, reject) {
    // code to execute
})

```

`promise`构造函数接受一个参数，一个回调函数，也称为 executor。执行器函数接受两个回调函数:`resolve`和`reject`。如果 executor 函数成功执行，则调用`resolve()`方法，并且`promise`状态从待定变为已完成。如果 executor 函数失败，那么调用`reject()`方法，并且`promise`状态从 pending 变为 failed。

要访问解析的值，使用`.then ()`方法链接`promise`，如下所示:

```
promise.then(resolvedData => {
  // do something with the resolved value
})

```

类似地，在拒绝值的情况下，使用`.catch()`方法:

```
promise.then(resolvedData => {
  // do something with the resolved value
}).catch(err => {
  // handle the rejected value
})

```

## `async/await`语法

当我们有几个嵌套的回调函数或`.then`函数时，维护代码及其可读性通常会变得很困难。

关键字`async`帮助我们在 JavaScript 中定义处理异步操作的函数。同时，`await`关键字用于指示 JavaScript 引擎在返回结果之前等待函数完成。

语法只是围绕承诺的语法糖。它帮助我们实现更清晰、更易于维护的代码。

```
const getUsers = async () => {
  const res = await fetch('https://jsonplaceholder.typicode.com/users');
  const data = await res.json();
  return data;
}

```

使承诺或异步功能以同步方式执行。然而，用一个`try...catch`块包装`await`关键字以避免意外错误总是一个好的做法。

这里有一个例子，我们在一个`try...catch`块中包装了`await`关键字和`getUsers()`函数，就像这样:

```
const onLoad = async () => {
  try {
    const users = await getUsers();
    // do something with the users
  } catch (err) {
    console.log(err)
    // handle the error
  }
}

```

## 自定义`promise`包装

`async/await`在现代 JavaScript 中是一个如此棒的特性的原因之一是它帮助我们避免了回调地狱。

尽管如此，处理来自多个`async`函数的错误可能会导致这样的结果:

```
try {
  const a = await asyncFuncOne();
} catch (errA) {
  // handle error
}

try {
  const b = await asyncFunctionTwo();
} catch (errB) {
  // handle error
}

try {
  const c = await asyncFunctionThree();
} catch (errC) {
  // handle error
}

```

如果我们在一个`try`块中添加所有的`async`函数，我们将在`catch`块中编写多个`if`条件，因为我们的`catch`块现在更通用了:

```
try {
  const a = await asyncFuncOne();
  const b = await asyncFunctionTwo();
  const c = await asyncFunctionThree();
} catch (err) {
  if(err.message.includes('A')) {
    // handle error for asyncFuncOne
  }
  if(err.message.includes('B')) {
    // handle error for asyncFunctionTwo
  }
  if(err.message.includes('C')) {
    // handle error for asyncFunctionThree
  }
}

```

这使得代码可读性更差，更难维护，即使使用`async/await`语法也是如此。

为了解决这个问题，我们可以写一个效用函数来包装承诺，避免重复的`try...catch`块。

效用函数将接受一个承诺作为参数，在内部处理错误，并返回一个包含两个元素的数组:解析值和拒绝值。

该函数将解析承诺并返回数组第一个元素中的数据。错误将在数组的第二个元素中返回。如果承诺被解决，第二个元素将作为`null`返回。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
const promiser = async (promise) => {
  try {
    const data = await promise;
    return [data, null]
  } catch (err){
    return [null, error]
  }
}

```

我们可以进一步重构上面的代码，通过使用`.then()`和`.catch()`处理程序方法返回`promise`来移除`try...catch`块:

```
const promiser = (promise) => {
  return promise.then((data) => [data, null]).catch((error) => [null, error]);
};

```

我们可以在下面看到实用程序的用法:

```
const demoPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    // resolve("Yaa!!");
    reject("Naahh!!");
  }, 5000);
});

const runApp = async () => {
  const [data, error] = await promiser(demoPromise);
  if (error) {
    console.log(error);
    return;
  }
  // do something with the data
};

runApp();

```

现在，让我们来看一个真实的用例。下面，`generateShortLink`函数使用 URL shortener 服务来缩短一个完整长度的 URL。

这里，`axios.get()`方法由`promiser()`函数包装，以返回来自 URL shortener 服务的响应。

```
import promiser from "./promise-wrapper";
import axios from "axios";

const generateShortLink = async (longUrl) => {
  const [response, error] = await promiser(
    axios.get(`https://api.1pt.co/addURL?long=${longUrl}`)
  );

  if (error) return null;

  return `https://1pt.co/${response.data.short}`;
};

```

为了比较，下面是没有`promiser()`包装函数的情况:

```
const generateShortLink = async (longUrl) => {
  try {
    const response = await axios.get(
      `https://api.1pt.co/addURL?long=${longUrl}`
    );
    return `https://1pt.co/${response.data.short}`;
  } catch (err) {
    return null;
  }
};

```

现在，让我们通过创建一个使用`generateShortLink()`方法的表单来完成这个例子:

```
const form = document.getElementById("shortLinkGenerator");

const longUrlField = document.getElementById("longUrl");

const result = document.getElementById("result");

form.addEventListener("submit", async (e) => {
  e.preventDefault();
  const longUrl = longUrlField.value;
  const shortLink = await generateShortLink(longUrl);
  if (!shortLink) result.innerText = "Could not generate short link";
  else result.innerHTML = `<a href="${shortLink}">${shortLink}</a>`;
});

<!-- HTML -->
<!DOCTYPE html>
<html>
  <head>
    <title>Demo</title>
    <meta charset="UTF-8" />
  </head>
  <body>
    <div id="app">
      <form id="shortLinkGenerator">
        <input type="url" id="longUrl" />
        <button>Generate Short Link</button>
      </form>
      <div id="result"></div>
    </div>
    <script src="src/index.js"></script>
  </body>
</html>

```

下面是[完整代码和演示](https://codesandbox.io/s/great-pare-lmr09j?file=/src/index.js)供大家参考。

到目前为止，`promiser()`函数只能包装一个`async`函数。然而，大多数用例会要求它处理多个独立的`async`功能。

为了处理许多承诺，我们可以使用`Promise.all()`方法，并将一组`async`函数传递给`promiser`函数:

```
const promiser = (promise) => {
  if (Array.isArray(promise)) promise = Promise.all(promise);
  return promise.then((data) => [data, null]).catch((error) => [null, error]);
};

```

下面是一个与多个`async`函数一起使用的`promiser()`函数的例子:

```
import axios from "axios";
import promiser from "./promiser";

const categories = ["science", "sports", "entertainment"];

const requests = categories.map((category) =>
  axios.get(`https://inshortsapi.vercel.app/news?category=${category}`)
);

const runApp = async () => {
  const [data, error] = await promiser(requests);
  if (error) {
    console.error(error?.response?.data);
    return;
  }
  console.log(data);
};

runApp();

```

## `Promise.all()`对`Promise.allSettled()`

这可能是澄清关于两个方法的任何混淆的好时机:`Promise.all()`和`Promise.allSettled()`。

只有当所有给定的承诺都兑现时才会解决。如果任何一个承诺被拒绝，`Promise.all()`将作为一个整体拒绝，这在任务相互依赖的情况下非常有用，如果一个任务失败，您希望停止一系列功能。

要处理多个承诺，还可以使用`Promise.allSettled()`方法。

`Promise.allSettled()`用于多个互不依赖的异步任务。`Promise.allSettled()`不拒绝；相反，它等待所有承诺完成并返回，不管它们是否成功。`Promise.allSettled()`一旦所有给定的承诺完成，包括完成的和拒绝的实例，就将自己标记为完成。

```
Promise.allSettled([
  Promise.resolve(39),
  Promise.reject("An error occurred"),
  new Promise(resolve => setTimeout(() => resolve(100), 2000)),
  70
]).then(v => console.log(v))

```

如下所示，`Promise.allSettled()`方法返回一个对象数组，该数组指定每个承诺的状态及其解析值或拒绝的错误消息。

```
[{
  status: "fulfilled",
  value: 39
}, {
  reason: "An error occurred",
  status: "rejected"
}, {
  status: "fulfilled",
  value: 100
}, {
  status: "fulfilled",
  value: 70
}]

```

下面是一个用于`Promise.allSettled()`的包装函数，它将只返回解析的值，并且在拒绝承诺的情况下将返回 null。

```
const settler = (promise) => {
  if (Array.isArray(promise)) {
    return Promise.allSettled(promise).then(settledPromises => settledPromises.map((settledPromise) =>
      (settledPromise.status === 'fulfilled') ? settledPromise.value : null
    ))
  }
  return promise.then((data) => data).catch((error) => null);
};

settler([
  Promise.resolve(39),
  Promise.reject("An error occurred"),
  new Promise(resolve => setTimeout(() => resolve(100), 3000)),
  70
]).then(v => console.log(v))

// [39, null, 100, 70]

```

那么应该用哪种方法呢？这取决于你的需求。如果你想让你的职能继续下去，即使有些承诺被拒绝，那么`Promise.allSettled()`就是你要走的路。但是如果你需要实现所有的承诺来让你的功能按预期工作，你应该选择`Promise.all()`。

## 结论

本指南中分享的用 JavaScript 编写声明性异步函数调用的解决方案对于大多数场景都是理想的。但是，您可能需要考虑一些额外的用例。例如，您可能只想处理预期的错误，并抛出在 promise 执行期间发生的任何异常错误。

任何方法都有利弊。对于您的特定用例，理解并考虑它们是很重要的。

在继续您的编码之旅时，本文中分享的知识是创建更复杂的 API 和实用函数的一个很好的切入点。祝你好运，编码快乐！

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.