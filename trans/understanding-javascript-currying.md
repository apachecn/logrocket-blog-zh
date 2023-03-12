# 理解 JavaScript currying - LogRocket 博客

> 原文：<https://blog.logrocket.com/understanding-javascript-currying/>

Currying 是 lambda 微积分中的一个概念，但是不要让它吓到你——它实现起来非常简单。

Currying 是一个函数，它一次接受一个参数，然后返回一个新函数，期待下一个参数。它是将一个函数从可作为 f(a，b，c)调用翻译成可作为 f(a)(b)(c)调用的函数转换。

在本文中，我们将探讨 Javascript 中的 currying 是什么，为什么以及应该在哪里使用 currying，以及如何用代码示例实现它。

## JavaScript 中的 currying 是什么？

Currying 简单地说就是计算带有多个参数的函数，并将它们分解成一系列带有单个参数的函数。

换句话说，currying 就是一个函数——而不是一次接受所有参数——接受第一个参数并返回一个新函数，该函数接受第二个参数并返回一个新函数，该函数接受第三个参数，以此类推。直到所有论证完成。

## 我为什么要使用 currying？

有几个原因可以解释为什么奉承是理想的:

*   Currying 是一种检查方法，以确保您在继续之前得到了您需要的一切
*   它帮助你避免一次又一次地传递同一个变量
*   它将您的职能划分为多个较小的职能，可以处理一项职责。这使得你的函数更纯粹，更不容易出错和产生副作用
*   它在函数式编程中用于创建高阶函数
*   这可能是个人偏好，但我喜欢它使我的代码可读

## currying 是如何工作的？

Currying 是一个接受多个参数的函数。它会将这个函数转换成一系列函数，其中每个小函数都接受一个参数:

```
Noncurried version//
const add = (a, b, c)=>{
    return a+ b + c
}
console.log(add(2, 3, 5)) // 10

Curried version//
const addCurry =(a) => {
    return (b)=>{
        return (c)=>{
            return a+b+c
        }
    }
}
console.log(addCurry(2)(3)(5)) // 10

```

就定义而言，Javascript 中的 Currying 可能有点难以理解，但当我们实现它时，它就会变得清晰。

所以，让我们深入更多的代码示例。

### 示例 1:一个简单的三参数函数

首先，我将创建一个接受三个参数的简单函数:

```
const add =(a, b, c)=>{
    return a+b+c
}
console.log(add(2, 3, 5)) // 10

```

输出这个函数后，结果是`10`。

这里发生的事情是，这个函数将我们传递的数字的所有参数相加。

现在，第一个例子只是一个接受多个参数的简单函数。

如何将现有函数转换成 curried 版本？

### 示例 2:将现有函数转换为可定制函数

让我们试试第二个例子，看看如何实现 curry 函数。

在本例中，该函数将接受一个参数并返回一系列函数:

```
const addCurry =(a) => {
    return (b)=>{
        return (c)=>{
            return a+b+c
        }
    }
}

```

这是函数的 curry 实现。如果我们输出这个，结果将是`10`:

```
console.log(addCurry(2)(3)(5)) // 10

```

在第一个例子中，我们创建了一个函数`addCurry`，它接受三个参数`a`、`b`和`c`，将它们的和`a+b+c`、(2)+(3)+(5)相加，并返回输出作为`10`。

第二个例子展示了我们如何实现相同的函数，但是使用了一个带有一个参数`a`并返回一个带有另一个参数`b`的函数的简化版本，该函数返回一个带有另一个参数`c`的函数，该函数返回它们的和，这给出了与例子一相同的输出:`10`。

我们在这里做的是一个嵌套函数，所以每个函数都接受一个参数，这个参数返回另一个参数，这个函数直到收到所有的参数才会完成。

### 示例 3:创建一个好友请求库里函数

在这个例子中，我们将创建一个简单的 curry 函数，其中一个用户向他的朋友 John 发送一个好友请求:

```
function sendRequest(greet){
    return function(name){
        return function(message){
            return `${greet} ${name}, ${message}`
        }
    }
}
sendRequest('Hello')('John')('Please can you add me to your Linkedin network?')

```

输出:

```
"Hello John, Please can you add me to your Linkedin network?"

```

我们创建了一个函数`sendRequest`,它只需要一个参数`greet`,它返回我们想要发送给用户的人的名字和消息。然后，当我们调用该函数时，它输出消息。

## 基础与高级的 currying 技术

### 基本搬运

```
const getPanCakeIngredients = (ingredient1) =>{
    return (ingredient2) => {
        return (ingredient3) => {
            return ${ingredient1}, ${ingredient2}, ${ingredient3}; 
        } 
    } 
} 
getPanCakeIngredients('Egg')('flour')('milk');
```

这个代码示例是实现 currying 的基本方法。

在上面的例子中，我们创建了一个函数`getPanCakeIngredients`,它将`ingredient 1`作为一个参数，并返回一系列函数，这些函数包含了制作煎饼所需的其他配料。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

该函数在收到所有参数之前是不完整的，这意味着如果煎饼的配料不完整，该函数将不会返回任何好的结果。

### 先进运载

下面是一个高级奉承的代码示例:

```
const curry =(fn) =>{
    return curried = (...args) => {
        if (fn.length !== args.length){
            return curried.bind(null, ...args)
        }
    return fn(...args);
    };
}
const totalNum=(x,y,z) => {
    return x+y+z 
} 
const curriedTotal = curry(totalNum);
console.log(curriedTotal(10) (20) (30));

```

在上面的例子中，我们创建了一个需要固定数量参数的函数。

它接收一个函数`curry`作为外部函数。这个函数是一个包装函数。它返回另一个名为`curried`的函数，该函数接收带有扩展操作符`( ...args)`的参数，并比较函数长度`fn length`。

函数长度意味着无论我们在这里传递多少个参数，它都会反映在函数的长度属性中。

但是每次争论都会增加。如果我们需要的参数数量不相等，它将返回`curried`。如果我们调用`bind`，这将创建一个新函数，并且我们将传递`( ...args)`。

> **注意:**，`bind`创建一个新函数。

## 与 ES6 的现代搭配

作为我的额外提示，这里有一个使用 [ES6](https://blog.logrocket.com/javascript-es6-5-new-abstractions-to-improve-your-code-54a369e82407/) arrow 函数实现 currying 的现代方法。它帮助您编写更少的代码:

```
const sendRequest = greet => name => message =>
`${greet} ${name}, ${message}`
sendRequest('Hello')('John')('Please can you add me to your Linkedin network?')

```

输出:

```
"Hello John, Please can you add me to your Linkedin network?"

```

## Currying 可用于操纵 Javascript 中的 DOM

准备好行动了吗？下面是一个简单的 [CodePen 示例，展示了如何使用 currying](https://codepen.io/emilsone/pen/jOwNgde) 操作 DOM:

> 添加外部样式表/笔此处添加的任何 URL 都将按顺序添加，并放在编辑器中的 CSS 之前。通过使用其 URL 和适当的 URL 扩展名，您可以使用另一个笔中的 CSS。JavaScript 预处理器巴贝尔包括 JSX 处理。

## Currying 与部分应用

现在你知道了 curry 是如何工作的了，那么 curry 和部分应用程序的区别是什么呢？这是程序员一直在问的一个问题。

这个问题我终于有答案了。但是在我用一些代码示例深入解释之前，我们最好熟悉一下它们的定义。

*   Currying:接受多个参数的函数。它将把这个函数转换成一系列函数，其中每个小函数将接受一个参数，直到所有参数都完成
*   部分应用:当函数的参数比预期的少时，函数被部分应用，并返回一个期望剩余参数的新函数

知道定义并不足以让我们理解它们的区别。您已经看到了 currying 的实际应用，但这只是部分应用的一个示例:

```
const addPartial=(x,y,z) => {
    return x+y+z 
}
var partialFunc= addPartial.bind(this,2,3);
partialFunc(5); //returns 10

```

我们在这里所做的不是一个简化的版本，但是我们做了`addPartial`函数的部分应用。我们创建了一个简单的函数来添加一系列数字并返回它们的输出。

> **N.B.** ，当一个函数传递的某些自变量不完整时，这个函数就被称为部分应用。

涂抹和局部应用并没有什么不同；它们是相关的，但是它们有不同的理论和应用。

分部应用程序将一个函数转换为另一个函数，但 arity 较小。

## 结论

对于开发人员来说，阿谀奉承可能感觉很复杂。虽然很难理解，但是当您在 JavaScript 项目中实现它时，您会学得更好。

我已经在我的一些项目中实现了 currying，并通过实践进行了学习。这些是我用来拍马屁的一些东西:

*   Currying 可用于操纵 Javascript 中的 DOM
*   它可以用来触发事件侦听器
*   当您想要创建一个只接收单个参数的函数时，可以使用 Currying

感谢您阅读这篇文章，请随时留下您的任何意见。我愿意向你学习。干杯！

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.