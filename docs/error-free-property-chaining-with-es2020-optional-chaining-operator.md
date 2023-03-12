# ES2020 可选链接操作器的无错属性链接

> 原文：<https://blog.logrocket.com/error-free-property-chaining-with-es2020-optional-chaining-operator/>

在 JavaScript 中，访问深度嵌套的属性通常涉及检查链中的每个属性是否有效。

这种策略背后的逻辑很简单:如果其中一个属性的值为`null`或`undefined`，代码就会抛出一个`TypeError`。`null`和`undefined`是不能有任何属性的原始值。

因此，将这些值视为对象是有问题的，这并不奇怪。

在本文中，我们将首先看看 JavaScript 中处理属性链的现有方法，然后看看可选的链接操作符如何通过更短、更直观的语法简化过程并提高代码的可读性。

## 问题是

理解为什么直接访问嵌套属性可能不安全的最好方法是通过一个示例。假设您想使用一个 web 服务来检索日本东京的当前时间。服务返回一个 JSON 响应，如下所示:

```
{
    "data": {
        "datetime": "2020-06-26T21:04:47.546298+09:00",
        "day_of_week": 5,
        "day_of_year": 178,
        "timezone": "Asia/Tokyo",
        "utc_datetime": "2020-06-26T12:04:47.546298+00:00",
        "utc_offset": "+09:00",
        "week_number": 26
    }
}
```

您只对`datetime`属性的值感兴趣，所以您将它赋给一个变量来处理它:

`const datetime = response.data.datetime`

但是，如果 API 改变了响应的结构，并且您正在寻找的属性在`response.data.datetime`不再可用，该怎么办呢？

那会导致这样的错误:`TypeError: Cannot read property 'datetime' of undefined`。

为了编写不易出错的代码，JavaScript 开发人员通常会检查链中每个属性的存在，如下所示:

```
let datetime;
const response = {
    //…
};

if (response && response.data) {
    datetime = response.data.datetime;
}
```

这段代码确保在访问`response.data.datetime`的值之前`response`和`response.data`是非`null`和非`undefined`属性。

另一种实现方法是使用三元运算符:

```
const response = {
    //…
};

const datetime =
    (response ?
        (response.data ?
            response.data.datetime :
            undefined) :
        undefined);
```

这两种方法看起来都很粗糙，并且会影响代码的可读性，尤其是在属性嵌套很深的情况下。幸运的是，现在有更好的方法来处理这个讨厌的问题。

## 介绍可选的链接运算符

可选的 chaining 运算符是 ES2020 建议，它提供了一种简单的语法来访问嵌套属性，而无需显式检查链中的每个对象是否存在。

该提案目前处于第 4 阶段，这意味着它已经准备好包含在 JavaScript 规范中了。好消息是，包括 Chrome 80+、Firefox 74+和 Safari 13.1+在内的所有现代浏览器都已经实现了该功能。

要使用可选的 changing 操作符，请在一个或多个属性访问链之前加上`?.`标记。这里有一个例子:

```
const obj = {};
const city = obj?.user?.address?.city;

console.log(city);    // => undefined
```

这段代码试图访问一个不存在的嵌套属性。但是 JavaScript 返回一个`undefined`值，而不是抛出一个错误。如您所见，语法不仅更短，而且可读性更好。

从技术上讲，`obj?.user`相当于`obj == null ? undefined : obj.user`。`?.`令牌只是为我们提供了一条捷径。

请记住，您不能在赋值的左侧使用可选的链接运算符。试图这样做将导致`SyntaxError`:

```
const obj = {};

obj?.property = 123;    // => SyntaxError: Invalid left-hand side in assignment
```

### 可选方法调用

还有一个可选的链接操作符版本，在调用可能不存在的对象方法时很有用。考虑这个例子:

```
const obj = {};
const value = obj.undefinedMethod?.();

console.log(value);    // => undefined
```

这里，`obj.undefinedMethod?.()`试图调用一个未定义的方法。但是因为表达式使用了`?.()`标记，所以它返回了`undefined`。

如果没有可选的链接操作符，这段代码将抛出一个错误:

```
const obj = {};
const value = obj.undefinedMethod();    // => TypeError: obj.undefinedMethod is not a function

// the console.log() method won’t have a chance to run
console.log(value);
```

请记住，在一些特殊情况下，`?.`会抛出一个错误，而不是返回`undefined`。

例如，如果您试图访问一个不存在的方法，但是对象有一个同名的属性，那么将会发生一个`TypeError`:

```
const user = {
    name: "Joe"
};

const value = user.name?.();    // => TypeError: user.name is not a function
```

还要注意的是`obj.a?.().x`的结果和`obj.a()?.x`的结果完全不同。如果`obj.a()`不存在，或者`obj.a`的值为`null`或`undefined`，前者返回`undefined`。

另一方面，如果`obj.a()`返回除了包含`x`属性的对象之外的任何内容，后者将返回`undefined`。

例如，您可以使用它来检索可能不存在的 HTML 元素的值:

```
// querySelector() returns null if the element doesn't exist on the page

const elem = document.querySelector('.abc')?.innerHTML;
// No error. elem will have a value of undefined

const elem = document.querySelector('.abc').innerHTML;
// => TypeError: Cannot read property 'innerHTML' of null
```

### 可选的动态属性访问

可选链接操作符还有一个变体:`?.[]`。当
使用括号符号访问一个对象的属性时，这个标记很有用。让我们看一个例子:

```
const obj = {
    user: {
      name: "joe"
    }
};

const value = obj?.user?.address?.["city"];

console.log(value);    // => undefined
```

这段代码试图访问`city`属性的值。但是因为`user`没有名为`address`的属性，所以它返回`undefined`。与常规的属性访问相比，这更不容易出错:

```
const obj = {
    user: {
        name: "joe"
    }
};

const value = obj.user.address["city"];    // => TypeError: Cannot read property 'city' of undefined
```

这种语法的另一个优点是能够使用动态生成的属性名。例如:

```
const config = {
    darkMode: {
         default: 0,
         state: 1
    },
    notifications: {
        default: 1,
        state: 0
    }
};

const option = 'darkMode';
const state = config?.[option].state;

console.log(state);    // => 1
```

但是数组项呢？我们可以使用可选的链接操作符安全地访问数组元素吗？答案是肯定的:

```
const arr = null;
let index = 2;
let item = arr?.[index];

console.log(item);    // => undefined
```

### 将可选的链接运算符与 nullish 合并运算符一起使用

与可选的链接操作符一样，nullish 合并(`??`)操作符是第 4 阶段 ES2020 提案，已经被所有现代浏览器实现。

该操作符的作用与逻辑 OR ( `||`)操作符非常相似，只是它不基于值是否为真来工作。相反，操作符的结果取决于值是否为 nullish，这意味着`null`或`undefined`。

因此，在表达式`a ?? b`中，只有当`a`的计算结果为`undefined`或`null`时，结果值才是`b`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

比较以下内容:

```
false || true;    // => true
false ?? true;    // => false

0 || 1;           // => 1
0 ?? 1;           // => 0

null || [];       // => []
null ?? [];       // => []

undefined || [];  // => []
undefined ?? [];  // => []
```

现在，当我们需要某个值而不是`undefined`作为缺失属性时，我们可以将 nullish 合并操作符与可选的链接操作符结合起来。

例如:

```
const config = {
    general: {
        language: null
    }
};

const language = config?.general?.language ?? "English";

console.log(language);    // => English
```

该代码将`English`设置为`config.general.language`的默认值。因此，当属性为`undefined`或`null`时，将使用默认值。

### 短路评估

可选的 chaining 操作符的一个有趣的方面是它能够用于短路计算。这意味着如果一个可选的链接操作符提前返回，表达式的其余部分将不会被计算。考虑以下代码:

```
const obj = null;
let a = 0;

obj?.[++a];

console.log(a);    // => 0
```

在这个例子中，`a`没有递增，因为`obj`具有一个`null`值。

此代码相当于:

```
const obj = null;
let a = 0;

obj == null ? undefined : obj[++a];

console.log(a);    // => 0
```

需要记住的重要一点是，当短路发生时，JavaScript 会忽略可选链接操作符后面的表达式。

### 限制短路的范围

正如我们所学的，我们可以使用短路来跳过表达式的其余部分。但是，有可能限制它的范围吗？与 JavaScript 中的任何表达式一样，我们可以使用分组操作符`( )`来控制求值:

```
(obj?.user).name;
```

然而，在实践中，很难找到使用这一特性的真实用例或令人信服的理由。

### 可选删除

可选链接操作符的另一个有趣特征是，您可以将它与`delete`操作符结合使用:

```
const obj = null;

// no error.
// even though obj.user doesn’t exist.
delete obj?.user;    // => true

// equivalent to
// obj == null ? true : delete obj.user
```

注意`delete`操作符是如何返回`true`的，尽管没有从`obj`中删除任何东西。如果没有可选的链接操作符，代码将抛出一个`TypeError`:

```
const obj = null;

delete obj.user;    // => TypeError: Cannot convert undefined or null to object
```

### 堆垛

Stacking 只是在一系列属性访问中使用多个可选链接操作符的能力的一个花哨名称。

当堆叠时，您应该问自己一个属性是否有机会包含空值。如果没有，那么就没有理由应用可选的链接操作符。

以下面的对象为例。如果`data`属性总是保证存在并且包含一个非空值，那么就不应该使用可选的链接:

```
const obj = {
    data: {}
};
```

### 先前技术

对于来自 C#、Swift 或 CoffeeScript 的开发人员来说，可选的链接操作符并不新鲜。类似的特征在那些语言中早已存在。

事实上，通过模仿这些语言，JavaScript 已经形成了可选链接操作符的一般语义。

也有一些语言，比如 Kotlin、Dart 和 Ruby，提供了类似的特性，但是有一个关键的区别:当属性链超过一个元素时，它们不会短路整个属性链。

## 结论

可选的链接操作符提供了一种健壮而简洁的方式来编写更安全的代码。

虽然它还不是正式的 JavaScript 特性，但是浏览器已经开始实现它了，JavaScript 社区似乎也欢迎这种语言的新特性。

如果你有任何问题，欢迎在评论中提问，我也在推特上。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.