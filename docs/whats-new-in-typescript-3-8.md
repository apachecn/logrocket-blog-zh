# TypeScript 3.8 中的新特性- LogRocket 博客

> 原文：<https://blog.logrocket.com/whats-new-in-typescript-3-8/>

[Typescript 3.8](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-8.html) 发布于 2020 年 2 月 20 日。此版本包括对编译器、性能和编辑器的更改。

在这篇文章中，我将回顾编译器的五个重要变化:

*   仅键入的导入和导出
*   将*导出为 ns 语法
*   ES2020 私有字段
*   顶级等待
*   JSDoc 属性修饰符

在撰写本文时，[版本 3.8.3](https://github.com/microsoft/TypeScript/releases/tag/v3.8.3) 已经发布。所以首先，升级到最新版本:

在终端窗口中，您可以使用以下命令来确认您使用的是最新版本:

```
tsc --version
```

现在，让我们先来回顾一下仅限类型的导入/导出功能。

## 仅键入的导入和导出

这个特性为导入和导出声明带来了新的语法，以及一个新的编译器选项`importsNotUsedAsValues`:

```
import type { MyType } from "./my-module.js";
// ...
export type { MyType };
```

这让您可以更好地控制如何在输出中处理导入语句，这在使用`--isolatedModules`选项、`transpileModule` API 或 Babel 进行编译时特别有用。

默认情况下，当导入仅用作类型时，TypeScript 会删除导入语句。例如，考虑以下情况:

```
// lion.ts
export default class Lion {}

// zoo.ts
import Lion from './lion';
let myLion: Lion;
```

这将是您编译`zoo.ts`时的输出:

```
// zoo.js
let myLion: Lion;
```

通常，这不会是一个问题。但是如果在`Lion`模块中有副作用呢:

```
// lion.ts
export default class Lion {}
console.log("Here's an important message about lions: ... ");
```

在这种情况下，如果从输出中删除 import 语句，则`console.log`语句将永远不会执行。

如果像下面这样声明，import 语句也将被删除:

```
import {TypeA, Type2} from "./my-module";
```

但是，如果您像这样声明它，它将保留在输出中:

```
import "./my-module";
```

有点困惑，对吧？

还有一个问题。在下面的代码中，你能分辨出`X`是数值还是类型吗？

```
import { X } from "./my-module.js";

export { X };
```

知道这一点可能很重要，如果`X`只是一个类型，Babel 和 TypeScript 的`transpileModule` API 将输出不能正确工作的代码，TypeScript 的`isolatedModules`标志将生成一个警告。

另一方面，我们在导出方面也有类似的问题，例如，类型的重新导出应该被省略，但是编译器不能判断我们只是在单文件 Babel 编译期间重新导出了一个类型。

在 TypeScript 3.8 中，`import type`和`export type`明确了类型的导入/导出。

以下是一些有效的使用方法:

```
import type MyType from './my-module';
import type { MyTypeA, MyTypeB } from './my-module';
import type * as Types from './my-module';

export type { MyType };
export type { MyType } from './module';
```

以下是一些无效的方法:

```
import { type MyType } from './my-module';
import type MyType, { FunctionA } from './my-module';

export { FunctionA, type MyType } from './my-module';
```

请记住，如果该类型未用作类型，编译器会将其标记为错误:

```
import type Lion from './lion';
let myLion: Lion; // Valid
myLion = new Lion(); // Invalid: 'Lion' cannot be used as a value because it was imported using 'import type'.
```

使用`import type`时，行为是像往常一样从 JavaScript 文件中删除导入声明。但是在 TypeScript 3.8 中，当使用`import`时，行为可以用编译器选项`importsNotUsedAsValue`来控制，它可以取值:

*   `default`，省略进口申报
*   `preserve`，保存进口申报单，对执行副作用有用
*   `error`，类似于`preserve`，但是每当一个`import`可以被写成一个`import type`时就会增加一个错误

这样，通过将选项`"importsNotUsedAsValue":"preserve"`添加到`tsconfig.json`文件:

```
// tsconfig.json
{
    "compilerOptions": {
      // ...
      "importsNotUsedAsValues": "preserve"
    },
    // ...
}
```

这段打字稿代码:

```
import Lion from './lion';
let myLion: Lion;
```

编译成这段 JavaScript 代码:

```
import './Lion';
let myLion;
```

## 将*导出为 ns 语法

TypeScript 支持一些更新的 [ECMAScript 2020 特性](https://tc39.es/ecma262/)，比如`export * as namespace`声明。

有时候，像这样的东西很有用:

```
import * as animals from "./animals.js";
export { animals };
```

它将另一个模块的所有成员作为单个成员公开。

在 ES2020 中，这可以用一句话来表达:

```
export * as animals from "./animals";
```

TypeScript 3.8 支持此语法。

如果您为 ES2020 配置模块:

```
// tsconfig.json
{
    "compilerOptions": {
      "module": "ES2020",
      // ...
    }
}
```

TypeScript 将输出没有修改的语句:

```
// allAnimals.ts
export * as animals from "./animals";

// allAnimals.js
export * as animals from "./animals";
```

但是，如果您之前对模块进行了配置，例如:

```
// tsconfig.json
{
    "compilerOptions": {
      "module": "ES2015",
      // ...
    }
}
```

TypeScript 将输出这两个声明:

```
import * as animals_1 from "./animals";
export { animals_1 as animals };
```

## ES2020 私有字段

ES2020 还为私有字段带来了新的语法。这里有一个例子:

```
class Lion {
    #age: number;
    constructor(age: number) {
        this.#age = age;
    }
    getAge() {
        return this.#age;
    }
}
```

私有字段以`#`字符开始，就像用`private`关键字标记的字段一样，它们的作用域是它们包含的类。

为什么是`#`字？嗯，显然所有其他酷字符都已经被[占用或者可能导致无效代码](https://github.com/tc39/proposal-class-fields/blob/master/PRIVATE_SYNTAX_FAQ.md#why-was-the-sigil--chosen-among-all-the-unicode-code-points)。

但是，有一些规则。

首先，你不能在同一个字段上同时使用`private`修饰符和`#`字符(或者`public`修饰符，尽管这种组合无论如何都没有意义)。

这是否意味着`private`修改器最终会消失？

在写这篇文章的时候，有一个关于这个的[公开讨论，但是当前的计划是让它保持原样。](https://github.com/microsoft/TypeScript/issues/31670)

那么，你应该用哪一个呢？

嗯，这取决于你对隐私的要求有多严格。

关于`private`修饰符的事情是，它只被 TypeScript 识别，这意味着访问限制只在编译时执行，私有约束将从生成的 JavaScript 代码中删除，在那里私有字段可以毫无问题地被访问。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

另一方面，`#`字符将保留在 JavaScript 代码中，使得该字段在类之外完全不可访问。

使用`#`的另一个规则是私有字段必须在使用前声明:

```
class Lion {
    constructor(age: number) {
          // Error: Property '#age' does not exist on type 'Lion'.ts
          this.#age = age;
    }
}

```

另外，请注意，您必须用`this`引用私有字段，否则，将会标记一个错误:

```
class Lion {
    #age: number;
    // ...
    getAge() {
        // The following line throws two errors:
        // 1\. Private identifiers are not allowed outside class bodies.
        // 2\. Cannot find name '#age'
        return #age;
    }
}
```

为了使用标有`#`的字段，您必须以 ECMAScript 2015 (ES6)或更高版本为目标:

```
// tsconfig.json
{
    "compilerOptions": {
      "target": "ES6",
      // ...
    }
}
```

原因是实施隐私的实现使用了`WeakMap` s，不能以不导致内存泄漏的方式进行聚合填充，不是所有的运行时都优化了`WeakMap` s 的使用。相反，带有`private`修饰符的字段适用于所有目标，并且速度更快。

例如，这个 TypeScript 类:

```
// lion.ts
class Lion {
    #age: number;
    constructor(age: number) {
        this.#age = age;
    }
    getAge() {
        return this.#age;
    }
}
```

输出这个:

```
// lion.js
var __classPrivateFieldSet = (this && this.__classPrivateFieldSet) || function (receiver, privateMap, value) {
    if (!privateMap.has(receiver)) {
        throw new TypeError("attempted to set private field on non-instance");
    }
    privateMap.set(receiver, value);
    return value;
};
var __classPrivateFieldGet = (this && this.__classPrivateFieldGet) || function (receiver, privateMap) {
    if (!privateMap.has(receiver)) {
        throw new TypeError("attempted to get private field on non-instance");
    }
    return privateMap.get(receiver);
};
var _age;
class Lion {
    constructor(age) {
        _age.set(this, void 0);
        __classPrivateFieldSet(this, _age, age);
    }
    getAge() {
        return __classPrivateFieldGet(this, _age);
    }
}
_age = new WeakMap();
```

从以下代码的输出中，您可以看到`age`属性在它的类之外是不可见的:

```
const lion = new Lion(5);
console.log(lion); // 'Lion {}'
console.log(Object.keys(lion)); // '[]'
console.log(lion.getAge()); // 5
```

## 顶级等待

在 JavaScript 中，我们可以使用带有 await 表达式的`async`函数来执行异步操作:

```
async function getLionInformation() {
    let response = await fetch('/animals/lion.json');
    let lion = await response.json();
    return lion;
}

getLionInformation().then((value) => console.log(value));
```

但是`await`表达式只允许在`async`函数体中使用，我们不能在顶级代码中使用它们(例如，在 Chrome 上使用开发人员控制台时，这可能很有用):

```
// Syntax error
let response = await fetch('/animals/lion.json');
let lion = await response.json();
console.log(lion);
```

然而，[顶层等待](https://tc39.es/proposal-top-level-await/)(目前是 ECMAScript 的第三阶段提案)允许我们在模块或脚本的顶层直接使用`await`。

TypeScript 3.8 支持顶级 await，由于带有`import`和`export`表达式的文件被认为是模块，所以即使一个简单的`export {}`也足以使这种语法起作用:

```
let response = await fetch('/animals/lion.json');
let lion = await response.json();
console.log(lion);

export {}
```

TypeScript 中唯一的限制是:

*   `target`编译器选项必须是`es2017`或以上，
*   `module`编译器选项必须是`esnext`或`system`

## JSDoc 属性修饰符

JSDoc 允许我们将文档注释直接添加到 JavaScript 源代码中，因此 JSDoc 工具可以扫描代码并生成 HTML 文档网站。

但是 TypeScript 使用 JSDoc 对 JavaScript 文件进行类型检查不仅仅是为了文档目的。

这是可能的，因为有两个[编译器选项](https://www.typescriptlang.org/docs/handbook/compiler-options.html):

*   `allowJs`，允许 TypeScript 编译 JavaScript 文件
*   `checkJs`，与上面的选项结合使用，允许 TypeScript 报告 JavaScript 文件中的错误

TypeScript 3.8 增加了对三种可访问性修饰符的支持:

*   `@public`，这意味着该属性可以在任何地方使用(默认行为)
*   `@private`，这意味着一个属性只能在定义它的类中使用
*   `@protected`，这意味着一个属性只能在定义它的类和所有派生的子类中使用

例如:

```
// lion.js
// @ts-check
class Lion {
    constructor() {
        /** @private */
        this.age = 5;
    }
}

// Error: Property 'age' is private and only accessible within class 'Lion'.
console.log(new Lion().age);
```

和`@readonly`修饰符，它确保属性只在初始化期间赋值:

```
// lion.js
// @ts-check
class Lion {
    constructor(ageParam) {
        /** @readonly */
        this.age = ageParam;
    }
    setAge(ageParam) {
        // Error: Cannot assign to 'age' because it is a read-only property
        this.age = ageParam;
    }
}
```

你可以在这里了解更多关于类型检查 JavaScript 文件[，以及](https://www.typescriptlang.org/docs/handbook/type-checking-javascript-files.html)[支持的 JSDoc 标签](https://www.typescriptlang.org/docs/handbook/type-checking-javascript-files.html#supported-jsdoc)。

## 结论

在这篇文章中，您了解了 TypeScript 3.8 中的五个新特性:仅类型导入和导出、`export * as ns`语法、ES2020 私有字段、顶级 await 和 JSDoc 属性修饰符。

当然还有更多的新功能。

**对于编译器**:

*   在 Linux 和`watchOptions`上更好的目录监视(更多信息[在这里](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-8.html#-better-directory-watching-on-linux-and-watchoptions))
*   “快速和宽松”的增量检查(更多信息[此处](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-8.html#-fast-and-loose-incremental-checking)

**为编者(更多信息[此处](https://devblogs.microsoft.com/typescript/announcing-typescript-3-8/#editor-features) )** :

**和一些突破性的变化**:

*   对具有索引签名的联合进行更严格的可分配性检查
*   没有推论的可选参数被正确地标记为隐式`any`
*   JSDoc 中的`object`不再是`noImplicitAny`下的`any`

你可以在这里找到关于这些[突破性变化的更多信息。](https://github.com/microsoft/TypeScript/wiki/Breaking-Changes)

编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.