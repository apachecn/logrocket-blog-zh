# 你可能错过的 ES2021 新功能- LogRocket 博客

> 原文：<https://blog.logrocket.com/new-es2021-features-you-may-have-missed/>

对于那些不知道的人来说， [TC39](https://tc39.es/) 是 ECMAScript (JavaScript)规范标准化背后的组织。自从 2015 年 ES6 发布以来，每年发布新功能已经成为常态。

对于要添加到年度版本中的特性，它必须经过四个提议阶段，最后一个阶段是批准。让我们看看目前处于[最后阶段](https://github.com/tc39/proposals/blob/master/finished-proposals.md)的五个功能提案，预计发布日期为 2021 年年中。

## 1.逻辑赋值运算符

新的[逻辑赋值运算符](https://tc39.es/proposal-logical-assignment/) `&&=`、`||=`和`??=`与现有的[逻辑运算符](https://blog.logrocket.com/a-comprehensive-guide-to-javascript-expressions/)非常相似，对于给变量赋值默认值非常有用。

### 逻辑或赋值运算符(`||=`)

```
x ||= y
```

逻辑 or 赋值运算符是一种短路运算，就像逻辑 OR 运算符(`||`)一样。上面的表达式与`x || (x = y)`相同，也就是说只有当`x`为 [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) 时，`y`才会被赋值给`x`。否则，`x`保留其原始值。

**举例:**

```
const giveKey = () => {
  //perform randon computations
  return "somekey";
}
let userDetails = {name:"chika", age:5, room:10, key:""}
userDetails.key ||= giveKey()
console.log(userDetails.key)

//output : somekey

```

`console.log(userDetails.key)`返回`"somekey"`，因为原始键值是一个空字符串，这是一个 falsy 值。

### 逻辑与赋值运算符(`&&=`)

```
x &&= y

```

逻辑 AND 赋值运算符与逻辑 or 赋值运算符相反。在这种情况下，当且仅当`x`是一个[真值](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)时，`y`才被赋值给`x`。否则，它将保留其原始值。

**举例:**

```
const deleteKey = () => {
  //perform randon computations
  return " ";
}
let userDetails = {name:"chika", age:5, room:10, key:"990000"}
userDetails.key &&= deleteKey()
console.log(userDetails.key)

//output : ""

```

`userDetails.key &&= deleteKey()`从`deleteKey`函数返回一个空字符串，因为`userDetails`的前一个值是一个数字，这是一个真值。

### 逻辑零赋值运算符(`??=`)

```
x ??= y

```

如果`x`为空(即空或未定义)，则[逻辑空运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_nullish_assignment) r 仅将`y`分配给`x`。

**举例:**

```
const getKey = () => {
  //perform randon computations
  return "somekey";
}
let userDetails = {name:"chika", age:5, room:10,}
userDetails.key ??= getKey()
console.log(userDetails.key)

//output : "somekey"

```

这里的输出是`"somekey"`，因为`userDetails.key`在响应对象中不存在(即，它是未定义的)。

## 2.`String.replaceAll`

```
const newString = oldString.replaceAll(pattern, replacement);

```

`[replaceAll](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replaceAll)`方法返回一个新字符串，其中所有出现的`pattern`都被传递给它的`replacement`替换。`pattern`参数可以是一个字符串或正则表达式模式，`replacement`可以是一个字符串或一个创建新字符串来替换`pattern`的函数。

`replaceAll`方法是`[String.replace](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)`方法的延续，只是用`replacement`替换了第一次出现的`pattern`。

**举例:**

```
const str = "Linda is a self-taught developer.Linda will rule the world";

let newStr = str.replace("Linda","Micheal")
//output: Micheal is a self-taught developer.Linda will rule the world

let newStr = str.replaceAll("Linda","Micheal")
//output: Micheal is a self-taught developer.Micheal will rule the world

```

## 3.数字分隔符

数字分隔符通过使用下划线(`_`)字符来分隔数字组，就像在书写中使用逗号来分隔数字一样，从而提高了大数字的可读性。考虑一下数字`1200044555`。乍一看，这个数字是 12 亿之类的，很难破译。

**举例:**

```
const billGatesNetWorth = 1_200_044_555;

```

现在这个可读性更强了。请注意，这不会带来任何性能优势，也不会影响平等性。`1_200_044_555`仍然等于`1200044555`。

## 4.`Promise.any`

```
Promise.any([promise1, promise2, promise3, ...]).then(....do something)

```

`[Promise.any()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/any)`方法是一种新的承诺方法，它接受一系列承诺，并解析到成功解析的第一个承诺的值。换句话说，如果任何一个承诺成功，则`Promise.any`成功；如果所有承诺都拒绝，则`Promise.any`拒绝。

**举例:**

```
const promise1 = new Promise((resolve) => setTimeout((resolve) => resolve, 300, 'faster');
const promise2 = new Promise((reject) => setTimeout( (reject) =>reject, 100,"fastest")
const promise3 = new Promise((resolve) => setTimeout( (resolve) => resolve,700,'fast');
const promises = [promise1, promise2, promise3];

Promise.any(promises).then((value)=>console.log(value));

//Output: faster

```

## 5.`WeakRef`

```
const weakRef = new WeakRef({
   name:"Linda";
});
console.log(weakRef.deref().name)

//output: Linda

```

根据 TC39 提案注释,`WeakRef`是一个应该避免的高级特性。要理解`WeakRef`做什么，首先需要理解 JavaScript 中对象引用和[垃圾收集](https://blog.logrocket.com/javascript-garbage-collection-browser-vs-server/)的概念。

```
const obj = {}

```

当您在 JavaScript 中创建一个对象并将其赋给一个变量时，浏览器上运行的 JavaScript 引擎会分配一个存储该对象的内存地址。另一方面，对象被赋给的变量存储的是对象的内存地址，而不是对象本身的值。所以，你可以说`obj`保存了一个对分配给它的对象的引用。

随着对象被创建并存储在内存中，在某个时候，浏览器可能会开始耗尽内存，需要释放内存空间。垃圾收集是浏览器引擎通过删除不再被任何变量引用的对象来释放内存空间的过程。

`WeakRef`创建传递给它的对象的弱引用。这意味着每当浏览器需要运行垃圾收集时，如果对该对象的唯一引用来自一个`WeakRef`变量，JavaScript 引擎可以安全地从内存中删除该对象并释放空间。这可能是 WebSocket 数据的理想选择，因为它们的生命周期很短。

用`new WeakRef`构造函数创建一个`WeakRef`，可以通过`deRef`方法访问`WeakRef`变量的值。

## 如何立即开始在您的代码库中使用 ES2021 功能

最新版本的主流浏览器，如 Chrome 85、Firefox 79 和 Safari 14，已经支持 ES2021 的新功能。然而，为了让你的代码能够在老版本的浏览器中运行，你需要用 [B](https://babeljs.io/) [abel](https://babeljs.io/) 编译器来设置你的项目。

安装以下软件包:

```
npm install --save-dev @babel/core @babel/cli @babel/preset-env 
npm install core-js

```

在项目的根目录下创建一个`babel.config.json`文件:

```
{
    "presets": [
        [
            "@babel/preset-env",
            {
                "useBuiltIns": "usage",
                "corejs": {
                    "version": "3.8",
                    "proposals": true
                }
            }
        ]
    ]
}

```

上面的配置指示 Babel 使用 env 预置，它包含所有最新 JavaScript 特性的转换，这些特性已经进入了 TC39 提案流程的第四阶段。它还指示 Babel 在需要时从 core JS 中检索 polyfills。

在项目的根目录下创建一个`.browserlistrc`文件，指定 Babel 转换代码的目标浏览器:

```
defaults
maintained node versions

```

`defaults`查询指示 Babel 仅转换和聚合填充以下代码:

*   最近两个版本的浏览器
*   市场份额使用率超过 0.5%的浏览器
*   没有死亡的浏览器

`maintained node versions`声明 Babel 应该为所有仍然由 Node.js 基金会维护的节点版本转换和聚合代码。

现在，您可以运行:

```
./node_modules/.bin/babel src --out-dir lib

```

这将解析并转换你在`src`目录中的所有 JavaScript 文件为适合旧浏览器的代码(由你的`.browserlistrc`文件指定),并将每个文件输出到`lib`目录。

> **注意事项**。，如果您使用的是 Create React App 或 vue-cli 之类的工具链，这些配置已经被抽象化了。

仅此而已。你可以走了。立即开始使用 ES2021 吧！

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.