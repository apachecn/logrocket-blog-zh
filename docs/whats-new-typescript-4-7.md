# TypeScript 4.7 中的新特性- LogRocket 博客

> 原文：<https://blog.logrocket.com/whats-new-typescript-4-7/>

TypeScript 4.7 中包含了许多重要的更新、介绍和改进。这次更新特别围绕类型推断、缩小分析、es 模块集成、实例化表达式等新特性。

在本文中，我们将看看每一个新的变化，并了解我们今天如何使用它们。让我们逐一查看，了解新版本的实现是什么样子的。

## 设置环境

从特性的实现开始，我们需要调至本地开发环境并配置一些文件。

首先，运行下面的命令，查看在根项目目录中为我们创建的一个`package.json`文件:

```
mkdir ts4.7_walkthrough 
cd ts4.7_walkthrough 
npm init -y
```

我们将安装带有`-D`标志的最新版本的 TypeScript，将其安装为一个开发依赖项:

```
npm install [email protected] -D
```

接下来，我们将运行`--init`命令来初始化 TypeScript:

```
npx tsc --init

```

这将为我们创建一个`tsconfig.json`文件。我们将在这里添加一个选项，以便我们可以指定文件的输出文件夹:

```
{
 "compilerOptions": {
"outDir": "./output"
  "declaration": true /* Generate .d.ts files from TypeScript and JavaScript files in your project. */,

 }
}

```

到目前为止，我们已经设置了输出目录，以获取将放入该文件夹的编译后的 JavaScript。我们还将更新我们的`package.json`文件的`scripts`部分，以包含构建和启动`script` :
<

```
"scripts": {
 "build": "tsc",
 "start": "tsc -w"
},

```

有了这些，我们就可以用命令`npm start`运行我们的应用程序，它监听所有对 TypeScript 文件的更改，并为我们编译它们。

现在，让我们开始探索新添加的特性和改进。

## 更好地控制模块检测

在这个版本之前，我们需要在 Node 中编写服务器端代码时添加`type=module`或`.mjs`扩展。但是，在普通 JavaScript 中，模块化代码的运行方式与传统脚本代码略有不同。由于它们有不同的作用域规则，我们需要决定每个文件如何运行。

在 TypeScript 中，如果在文件中写入任何导入和导出，则这些文件被视为模块。在这个最新版本中，我们有一个名为`moduleDetection`的选项来给我们更多的控制。该选项可以取三个值:`auto`、`legacy`和`force`。让我们了解一下他们每个人是做什么的。

当使用`auto`模式时，TypeScript 将检查导入和导出语句，以及在`--jsx react-jsx`下运行时当前文件是否在 JSX。它还会检查在`--module nodenext/--module node12`下运行时`package.json`中的`type`字段是否被设置为该模块。

另一方面，模式只检查导入和导出语句。

最后，`force`模式将强制每个文件被当作一个模块。

## 对象和方法在函数推理方面有所改进

有了新版本的 TypeScript，我们可以从对象、方法和数组中的函数执行更精确的推理。

我们倾向于从参数列表中任何位置的上下文不敏感的函数参数推断类型，例如当我们在通用函数参数列表中上下文键入箭头函数、对象文字和函数表达式的参数时，或者从参数列表中前面位置的上下文敏感的函数参数推断类型。

```
// Improved function inference in objects and methods
declare function myCharacter<T>(arg: {
 characterLetter: (a: string) => T,
 changeCharacter: (x: T) => void
}
): void;

// Works fine
myCharacter({
 characterLetter: () => "meow meow",
 changeCharacter: x => x.slice(0, 4),
});

// Works fine
myCharacter({
 characterLetter: (a: string) => a,
 changeCharacter: x => x.substring(0, 4),
});

// Was an error, but now it works.
myCharacter({
 characterLetter() { return "I love typescript" },
 changeCharacter: x => x.toUpperCase(),
});

// Now works with ^@4.7
myCharacter({
 characterLetter: a => a,
 changeCharacter: x => x.toUpperCase(),
});

// Now works with ^@4.7
myCharacter({
 characterLetter: function () { return "I love typescript"; },
 changeCharacter: x => x.toUpperCase(),
});

```

有了这些变化，我们现在可以在上下文敏感的、上下文类型的函数之间拥有相同的从左到右的信息流规则，而不管这些函数是作为对象或数组文字中的离散参数或属性出现的。

## 具有实例化表达式的专用通用函数

我们现在可以用实例化表达式来专门化通用函数。为了演示这一点，我们将创建一个名为`makeJuice`的通用类型接口。它将接受一个泛型，并将其传递给一个通用函数:

```
interface Fruits<T> {
  value: T;
}
function makeJuice<T>(value: T) {
  return { value };
}

```

经常会有这样的情况，我们想要创建一个专门化的函数，并对函数进行包装，使其更加专门化。为了实现这一点，我们可以写:

```
function orangeJuice(fruit: Orange) {
 return makeJuice(Orange);
}

// or can be written like

const appleJuice: (fruit: Fruits) => Fruits<Apple> = makeJuice;

```

这种方法肯定有效，但是创建一个新函数来包装另一个函数是浪费时间，而且坦率地说，工作量太大。在新版本中，我们可以通过获取函数和构造函数并直接向它们提供类型参数来简化这一过程:

```
const appleJuice= makeJuice<Apple>;
const orangeJuice= makeJuice<Orange>;

```

我们也可以只接收特定的类型，拒绝任何其他类型:

```
const makeAppleJuice = makeJuice<number>;

// TypeScript correctly rejects this.
makeAppleJuice('Apple');

```

这使得开发人员可以专注于通用函数，并接受和拒绝好的值。

## 对计算属性的更多控制流分析

随着 TypeScript 4.7 的发布，TypeScript 编译器现在可以分析计算属性的类型并正确地减少它们。你可以在下面看到一个例子:

```
const key = Symbol();
const objectKey = Math.random() <= 0.2 ? 90 : "Lucky person!";

let obj = {
 [key]: objectKey,
};

if (typeof obj[key] === "string") {
 let str = obj[key].toUpperCase();
 console.log(`You really are ${str}`);
}

```

[新版本](https://devblogs.microsoft.com/typescript/announcing-typescript-4-7-beta/#groups-aware-organize-imports)知道`obj[key]`是一个字符串。TypeScript 可以正确地检查计算属性是否在构造函数体结束时初始化。

## ide 中的对象方法片段自动完成

我们现在可以接收对象文字方法的代码片段完成。TypeScript 将为我们提供一个仅用于方法名称的典型完成条目，以及一个用于完整方法定义的单独完成条目。

![Object Method Snippet Autocompletion Inside IDE](img/28eef563ec017b15a31613a7eaa8ddee.png)

## 现在允许在`#private`字段中进行`typeof`查询

有了这次更新，我们现在可以对私有字段执行`typeof`查询。你可以在下面看到一个例子:

```
class Programming {
 #str = "Typescript rocks!";

 get ourString(): typeof this.#str {
     return this.#str;
 }

 set ourString(value: typeof this.#str) {
     this.#str = value;
 }
}

```

## 类型参数的可选方差注释

此外，我们现在能够显式地指定类型参数的变化:

```
interface Programming {
 langList: string[];
}

interface typescriptLang extends Programming {
 tsLang: string;
}

type Getter<T> = (value: T) => T;

type Setter<T> = (value: T) => void;

```

让我们假设我们有两个不同的可替换的`Getters`，这完全取决于泛型`T`。在这种情况下，我们可以检查`Getter<TypeScript> → Getter<Programming>`是否有效。换句话说，我们需要检查`TypeScript → Programming`是否有效。

检查`Setter<Typescript> → Setter<Programming>`是否有效包括查看`Typescript → Programming`是否也有效。方向的翻转类似于数学中的逻辑。本质上，我们看到的是 x<y 是否是同一个 y < x

当我们需要像这样翻转方向来比较`T`时，我们说`Setter`在`T`上是逆变的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

借助于`out`修饰语，我们现在可以明确地声明`Getter`在`T`上是协变的:

```
type Getter<out T> = () => T;

```

类似地，如果我们想明确表示`Setter`是`T`的逆变，我们可以给它一个修饰符:

```
type Setter<in T> = (value: T) => void;

```

我们在这里使用`out`和`in`修饰符，因为类型参数的方差依赖于它是用于输出还是输入。

## 可识别组的自动排序导入组织

TypeScript 现在有一个针对 JavaScript 和 TypeScript 的有组织的导入编辑器，但是，它可能不符合我们的期望。实际上，对我们的 import 语句进行本机排序更好。

让我们举一个实际例子:

```
// local code
import * as cost from "./cost";
import * as expenses from "./expenses";
import * as budget from "./budget";

// built-ins
import * as fs from "fs";
import * as http from "http"
import * as path from "path";
import * as crypto from "crypto";

```

如果我们对以下文件运行 organize imports，我们会看到以下变化:

```
// built-ins
import * as fs from "fs";
import * as http from "http"
import * as path from "path";
import * as crypto from "crypto";

//local code
import * as cost from "./cost";
import * as expenses from "./expenses";
import * as budget from "./budget";

```

导入按路径排序，我们的注释和换行符被保留。这以一种组感知的方式组织导入。

## `Resolution`模式可用于`import()`类型

TypeScript 现在允许`/// <reference types="…" />`指令和`import type`语句指定解析策略。这意味着我们可以解析导入的节点 ECMAScript 解析。但是，从 ECMAScript 模块引用通用 JavaScript 模块的类型会很有用，反之亦然。

在 TypeScript 的夜间版本中，`import type`可以指定一个导入断言来实现类似的结果:

```
// Resolve `pkg` as if we were importing with a `require()`
import type { TypeFromRequire } from "pkg" assert {
 "resolution-mode": "require"
};

// Resolve `pkg` as if we were importing with an `import`
import type { TypeFromImport } from "pkg" assert {
 "resolution-mode": "import"
};

export interface MergedType extends TypeFromRequire, TypeFromImport {}

```

这些导入断言也可以用在`import()`类型上:

```
export type TypeFromRequire =
   import("pkg", { assert: { "resolution-mode": "require" } }).TypeFromRequire;

export type TypeFromImport =
   import("pkg", { assert: { "resolution-mode": "import" } }).TypeFromImport;

export interface MergedType extends TypeFromRequire, TypeFromImport {}

```

注意，`import type`和`import()`语法只支持 TypeScript 每夜构建中的`resolution`模式。我们会得到如下错误:

```
**Resolution mode assertions are unstable. Use nightly TypeScript to silence this error. Try updating with 'npm install -D [email protected]'.

```

## 使用`moduleSuffixes`定制分辨率

TypeScript 现在支持一个`moduleSuffixes`选项来定制如何查找模块说明符。例如，如果我们正在导入像`import * as foo from "./foo"`和`moduleSuffixes`配置这样的文件，它看起来像这样:

```
{
 "compilerOptions": {
     "moduleSuffixes": [".ios", ".native", ""]
 }
}

```

在这种配置下，我们强制应用程序查看路径中的相关文件:

```
./example.ios.ts
./example.native.ts
./example.ts

```

这个特性将变得非常方便，特别是在 React 本地项目中，我们在`tsconfig.json`中添加每个带有不同`moduleSuffixes`的目标平台。

请注意，`moduleSuffixes`中的空字符串`""`是 TypeScript 查找`./example.ts`所必需的。

## `Extends`对`infer`类型变量的约束

对`infer`类型变量的约束允许开发人员根据类型的形状进行匹配和推断，并基于它们做出决定:

```
type FirstStringCheck<T> =
   T extends [infer S, ...unknown[]]
       ? S extends string ? S : never
       : never;

// string
type A = FirstStringCheck<[string, number, number]>;

// "typescript"
type B = FirstStringCheck<["typescript", number, number]>;

// "typescript" | "rocks"
type C = FirstStringCheck<["typescript" | "rocks", boolean]>;

// never
type D = FirstStringCheck<[boolean, number, string]>;

```

`FirstStringCheck`匹配任何至少有一个元素的元组类型，并获取第一个元素的类型作为`S`。然后，它将检查`S`是否与字符串兼容，如果兼容，则返回类型。

以前，我们需要这样写`FirstStringCheck`的相同逻辑:

```
type FirstStringCheck<T> =
   T extends [string, ...unknown[]]
       // Grab the first type out of `T`
       ? T[0]
       : never;

```

在这种情况下，我们变得越来越手动，越来越少声明。我们不是仅仅坚持在`type`定义上进行模式匹配，而是为第一个元素提供一个名称，并提取`T`的`[0]`元素。

这个新版本允许我们对任何推断类型进行约束，如下例所示:

```
type FirstStringCheck<T> =
 T extends [infer S extends string, ...unknown[]]
 ? S
 : never;

```

当`S`匹配时，它会确定`S`是一个字符串类型。如果不是这样，它就会走上一条`never`的错误道路。

## Node.js 中支持 ECMAScript 模块

对 Node.js 来说，支持 ECMAScript 是一项艰巨的任务，因为它的生态系统是围绕 CommonJS 构建的。Node.js 在其 v12 更新中扩展了对 ECMAScript 模块的支持。

[TypeScript v4.5 已经在 Node.js](https://blog.logrocket.com/typescript-4-7-ecmascript-module-support/) 中推出了对 ESM 的支持，作为每晚从用户那里获得反馈的功能。现在，它引入了两个新的编译器选项，`node12`和`nodenext`，以扩展 ECMAScript 模块支持。随着这两个特性的到来，它为我们带来了其他几个令人兴奋的特性。

```
{
 "compilerOptions": {
     "module": "nodenext",
 }
}

```

## 其他突破性变化

此更新还有一些其他令人兴奋的突破性变化。下面就来讨论其中的几个吧！

### JSX 更严格的价差检查

在 JSX 内部写一个`…spread`的时候，我们有一个更严格执行的内置规则。`unknown`和`never`(还有`null`和`undefined`的值已经不能分摊到 JSX 元素里了。这使得行为与对象文字的分布更加一致。

```
import React from "react";

interface Props {
   id?: string;
}

function Homepage(props: unknown) {
   return <div {...props} />;
}

```

使用上面的代码，我们会收到一个类似这样的错误:

```
Spread types may only be created from object types.

```

### 使用模板字符串表达式进行更严格的检查

如果我们在 JavaScript 中使用符号值，JavaScript 和 TypeScript 都会抛出错误。但是，TypeScript 现在会检查模板字符串中是否使用了符号中包含的泛型值。

```
function keyPropertyLogger<S extends string | symbol>(key: S): S {
 // Now an error.
 console.log(`${key} is the key`);
 return key;
}

function get<T, K extends keyof T>(obj: T, key: K) {
 // Now an error.
 console.log(`This is a key ${key}`);
 return obj[key];
}

```

TypeScript 现在将向我们抛出以下问题:

```
Implicit conversion of a 'symbol' to a 'string' will fail at runtime. Consider wrapping this expression in 'String(...)'.

```

### `readonly`元组具有只读长度属性

`readonly`元组现在将长度属性视为只读。这可以从具有可选尾部和 rest 元素类型的元组中看出。

```
function strLength(tuple: readonly [string, string, string]) {
 // Throws an error now
 tuple.length = 7;
}

```

### 在 LanguageServiceHost 上，方法不再是可选的

如果我们正在创建一个 LanguageService 实例，LanguageServiceHost 将需要提供一个`readFile`方法。为了支持新的模块检测编译器选项，这是一个必要的改变。

## 结论

通过团队和贡献者的大量努力和辛勤工作，我们现在可以尝试 TypeScript 4.7 令人兴奋的新功能和改进。

我们可以使用许多便利的功能来调整和优化我们的时间和效率。通过微软博客可以找到一个全面的发布主题列表，这是一个很好的资源。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.