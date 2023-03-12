# 黑格尔- LogRocket 博客简介

> 原文：<https://blog.logrocket.com/introduction-to-hegel/>

[Hegel](https://hegel.js.org/) 是一个静态类型检查器库，它可以帮助你在不实际运行你的代码的情况下尽可能早地识别打字错误。就像 TypeScript 和 Flow 一样，Hegel 会在您编写代码时检测出代码中存在的任何类型错误信息。

Hegel 合并了许多 TypeScript 和 Flow 的设计原则，比如拥有类型注释和类型推断，而没有引入像 [TypeScript 的 enum](https://blog.logrocket.com/why-typescript-enums-suck/)这样的新语言特性。当使用 Hegel 时，您甚至不需要添加注释就可以编写纯 JavaScript，就像 Flow 一样。

在本指南中，我们将展示 Hegel 与 TypeScript 和 Flow 的不同之处，并引导您在下一个项目中如何开始使用 Hegel。

## 黑格尔对打字稿

让我们来分解一下黑格尔和 TypeScript 之间的一些最显著的区别。

### 跳过类型注释

黑格尔有一个强大的类型推理系统，可以让你编写更少的类型注释。

```
// Hegel
const promisify = fn => arg => Promise.resolve(fn(arg));
const id = promisify(x => x);
// And "upperStr" will be inferred as "Promise<string>"
const upperStr = id("It will be inferred").then(str => str.toUpperCase());

// TypeScript
const promisify = fn => arg => Promise.resolve(fn(arg));
const id = promisify(x => x);
// And "upperStr" will be inferred as "Promise<any>"
const upperStr = id("It will be inferred").then(str => str.toUpperCase());

```

### 没有意外的运行时错误

TypeScript 的目的不是应用一个可靠的或可证明正确的类型系统，这意味着它不能保证您在运行时不会有任何类型错误。黑格尔反其道而行之，实现一个强类型系统来保证你的代码是有效的。

```
// Hegel
const doubles: Array<number> = [Math.PI, Math.E];
// Error: Type "Array<number>" is incompatible with type "Array<number | string>"
const numbersToShow: Array<number | string> = doubles;
numbersToShow.push(42..toString(2));
const rounded = doubles.map(double => double.toFixed());

// TypeScript
const doubles: Array<number> = [Math.PI, Math.E];
const numbersToShow: Array<number | string> = doubles;
numbersToShow.push(42..toString(2));
// Uncaught TypeError: double.toFixed is not a function
doubles.map(double => double.toFixed());

```

### 键入的错误

黑格尔为函数实现了推理和注释，这使您能够理解代码抛出了什么错误。

```
// Type of "assertIsTrue" function is "(boolean) => undefined throws TypeError"
function assertIsTrue(arg) {
    if (!arg) {
        throw new TypeError("arg is invalid")
    }
}
try {
    assertIsTrue(false);
} catch (e) {
    // Type of "e" variable is "TypeError | unknown"
}

// TypeScript
function assertIsTrue(arg) {
    if (!arg) {
        throw new TypeError("arg is invalid")
    }
}
try {
    assertIsTrue(false);
} catch (e) {
    // Type of "e" variable is "any"
}

```

### 没有新的构造函数

与 TypeScript 不同，黑格尔不是一种超集语言。这意味着 JavaScript 之外的构造函数和特性，比如 decorators、[私有类字段](https://www.typescriptlang.org/docs/handbook/classes.html#ecmascript-private-fields)、名称空间、枚举和 TypeScript 中的其他好东西，在 Hegel 中都是不可用的。

```
// TypeScript
enum UserStatus {
  Active,
  Muted,
  Banned
}
class User {
  constructor(
    public name: string,
    public status: UserStatus
  ) {}
}
const Anatoly = new User("Anatoly", UserStatus.Active);

// Hegel
const UserStatus = Object.freeze({
  Active: "Active",
  Muted: "Muted",
  Banned: "Banned"
});
class User {
    name: string;
    status: $Values<$TypeOf<UserStatus>>
    constructor(name, status) {
        this.name = name;
        this.status = status;
    }
}
const Anatoly = new User("Anatoly", UserStatus.Active);

```

### 无类型强制和`any`类型

因为黑格尔关心的是实现一个健全的类型系统，它没有类型强制或`any`类型。

```
// Error: There is no "any" type in Hegel.
const something: any = null;

// Error: Type cast does not exist in Hegel
(null: any).call();

```

## 黑格尔和心流

Hegel 与 Flow 有许多相似之处，因为它们都是静态类型检查器库。以下是黑格尔和心流之间的一些显著差异。

### 更好的类型推理

Flow 很难推断出泛型类型，所以如果您想拥有正确的类型，请对其进行注释。这是因为 Flow.js 是通过函数用法来推断函数类型的。

黑格尔通过函数声明来推断函数类型。因此，黑格尔的推论是多态型的。

```
// Hegel
// Type of "id" function is "<_a>(_a) => _a"
const id = x => x;
// Type of "num" variable is "number"
let num = id(4);
// Type of "str" variable is "string"
let str = id("4");
// Type of "anotherId" variable is "<_a>(_a) => _a"
let anotherId = id(id);

// Flow
// Type of "id" function is "(number | string | ((x: V$1) => V$2)) => (number | string | ((x: V$1) => V$2)"
const id = x => x;
// Type of "num" variable is "number | string | ((x: V$1) => V$2)"
let num = id(4);
// Type of "str" variable is "number | string | ((x: V$1) => V$2)"
let str = id("4");
// Type of "anotherId" variable is "number | string | ((x: V$1) => V$2)"
let anotherId = id(id);

```

### 键入的错误

就像使用 TypeScript 一样，Flow 对错误没有有用的类型推断，将返回一个`empty`类型。

```
// Type of "assertIsTrue" function is "(boolean) => undefined throws TypeError"
function assertIsTrue(arg) {
    if (!arg) {
        throw new TypeError("arg is invalid")
    }
}
try {
    assertIsTrue(false);
} catch (e) {
    // Type of "e" variable is "TypeError | unknown"
}

/* @flow */
function assertIsTrue(arg) {
    if (!arg) {
        throw new TypeError("arg is invalid")
    }
}
try {
    assertIsTrue(false);
} catch (e) {
    // Type of "e" variable is "empty"
}

```

### 没有自定义库定义语言

黑格尔没有像 Flow 一样创建自己的[自定义库定义](https://flow.org/en/docs/libdefs/)，而是实现了与 TypeScript 中相同的 [d.ts 定义](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html)。每一个有打字稿定义的库都应该和黑格尔合作。

### 黑格尔是用 JavaScript 实现的

Flow 主要是在 OCaml 中实现的，这使得 JavaScript 开发人员很难对项目做出贡献。

Hegel 是用 JavaScript 实现的，因此使用它的开发人员可以帮助解决任何 PRs 或将来出现的问题。

### 无类型强制和`any`类型

Flow 既有类型强制，也有`any`类型，就像 TypeScript 一样。

```
// Error: There is no "any" type in Hegel.
const something: any = null;

// Error: Type cast does not exist in Hegel
(null: any).call();

```

## 黑格尔入门

要开始在您的项目中使用 Hegel，请从终端安装它的 CLI 包。请注意，您需要 Node.js 版本 12 或更高版本。

```
# globally
$ npm install -g @hegel/cli

# locally
$ npm install -D @hegel/cli

```

安装完成后，创建一个新的`index.js`文件，并编写一个带有类型注释的变量。

```
let price :number = "7"

```

从项目的根目录运行`hegel`命令。它将扫描所有的`.js`文件，查找输入错误。

```
hegel
./index.js:1
> 1 | let price :number = "7"
    |    ^^^^^^^^^^^^^^^^^^^ Type "'7'" is incompatible with type "number"

```

就这样，你一切都准备好了！你不需要创建一个`.tsx`文件或者在你的文件上写`@flow`注释。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 为生产做准备

就像 Flow 一样，当您运行文件时，像 Node 这样的 JavaScript 运行时引擎会抛出一个错误，因为它不能识别注释语法。

为了让它正常运行，你必须用 [Babel](https://babeljs.io/) 或 [flow-remove-types](https://www.npmjs.com/package/flow-remove-types) 去掉黑格尔的类型语法。

### 使用巴别塔

安装所需的 Babel 包。

```
$ npm i -D @babel/core @babel/cli @babel/preset-flow

```

在项目的根目录下写一个`.babelrc`文件，使用下面的预置。

```
{
  "presets": [["@babel/preset-flow", { "all": true }]]
}

```

现在，您可以从终端运行它。

```
npx babel index.js -d build/

```

您也可以将其作为脚本添加到您的`package.json`中。

```
{
  "scripts": {
    "build": "babel index.js -d build/",
  }
}

```

然后，运行脚本。

```
npm run build
```

## 使用`flow-remove-types`

安装软件包。

```
npm i -D flow-remove-types

```

将构建脚本添加到您的`package.json`中，就像处理 Babel 一样。

```
{
  "scripts": {
    "build": "flow-remove-types index.js --out-dir build/",
  }
}

```

最后，运行脚本。

```
npm run build
```

## 结论

Hegel 是一个新的静态类型检查器库，它通过将静态的、强大的类型系统与强大的类型推理结合起来，试图将 TypeScript 的所有优点结合在一起。它试图使用纯 JavaScript 实现一个极简但完全静态的类型检查器，因此您不需要使用特定的文件扩展名或注释来使用它。

黑格尔还附带了一个[交互式在线编辑器](https://hegel.js.org/try#DYUwLgBADgTglgYxALggOwK4FsBGIYQC8EARAOwlA)，你可以在那里测试它的极限。别忘了查看官方的 [Hegel 文档](https://hegel.js.org/docs)来了解它的所有特性。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.