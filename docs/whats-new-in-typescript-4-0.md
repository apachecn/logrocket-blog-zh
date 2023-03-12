# TypeScript 4.0 中的新特性

> 原文：<https://blog.logrocket.com/whats-new-in-typescript-4-0/>

TypeScript 4.0 是 TypeScript 编程语言的一个重要里程碑，目前已经超越 3.9 成为最新的稳定版本。在这篇文章中，我们将看看 TypeScript 4.0 提供的新特性。

## 入门指南

要开始使用 4.0，您可以通过 NuGet 或通过 NPM 安装它[:](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/4.0.0-beta)

```
npm i typescript
```

您可以使用 [TypeScript playground](https://www.typescriptlang.org/play?ts=4.0.0-beta) 或支持 TypeScript 的文本编辑器来测试代码。我推荐使用[的 Visual Studio 代码](https://marketplace.visualstudio.com/items?itemName=TypeScriptTeam.TypeScript-40beta)，你可以在这里得到设置[的说明](https://code.visualstudio.com/Docs/languages/typescript#_using-newer-typescript-versions)。

## 特征

### 可变元组类型

简而言之，我们可以说 TypeScript 是强类型 JavaScript。这意味着它要求开发人员准确地指定他们的数据类型的格式，因此，它允许编译器在编译时捕捉类型错误，从而提供更好的开发人员体验。

> 这种精确指定数据类型格式的过程称为类型声明或类型定义，也称为类型化或简单类型。

利用这个特性，TypeScript 将类型赋予高阶函数，如`curry`、`concat`和`apply`。这些函数采用可变数量的参数。

考虑下面的`concat`函数的一个小例子:

```
function simpleConcat(arr1, arr2) {
  return [...arr1, ...arr2];
}
console.log(simpleConcat([1,2,3], [5,6])) // [1, 2, 3, 5, 6]
```

目前没有简单的方法在 TypeScript 中键入它。目前唯一可用的类型策略是编写重载。

> [函数或方法重载](https://www.typescriptlang.org/docs/handbook/functions.html#overloads)指的是 TypeScript 中的一个特性，它允许我们创建多个同名但参数或类型数量不同的函数。

考虑一下这个:

```
function concat1<T>(arr1: [T], arr2: []): [T] {
    return [...arr1, ...arr2]
}
function concat2<T1, T2>(arr1: [T1, T2], arr2: []): [T1, T2] {
    return [...arr1, ...arr2]
};
function concat6<T1, T2, T3, T4, T5, T6>(arr1: [T1, T2, T3, T4, T5, T6], arr2: []): [T1, T2, T3, T4, T5, T6] {
    return [...arr1, ...arr2]
}
function concat7<T1, T2, T3, T4, T5, T6, A1, A2, A3, A4>(arr1: [T1, T2, T3, T4, T5, T6], arr2: [A1, A2, A3, A4]): [T1, T2, T3, T4, T5, T6, A1, A2, A3, A4] {
    return [...arr1, ...arr2]
}
console.log("concated 1", concat1([1], []))
console.log("concated 2", concat2([1,2], []))
console.log("concated 6", concat6([1,2,3,4,5,6], []))
console.log("concated 10", concat10([1,2,3,4,5,6], [10, 11, 12, 13]))
```

从上面的例子中我们可以看到，重载的数量随着数组中项目数量的增加而增加，这是次优的。在`concat6`中，即使第二个数组为空，我们也不得不编写 6 个重载，而在`concat10`中，当第二个数组只有 4 个元素时，这很快就增加了 10 个重载。

此外，我们只能为我们编写的重载获取正确的类型。

TypeScript 4.0 带来了显著的推理改进。它允许元组类型中的 spread 元素是通用的，并且可以出现在元组中的任何位置。

在旧版本中，REST 元素必须是元组类型中的最后一个。如果不是这样，TypeScript 将抛出一个错误:

```
// Tuple speard items are generic
function concatNumbers<T extends Number[]>(arr: readonly [Number, ...T]) {
  // return something
}

// spread occuring anywhere in the tuble valid in 4.0 beta.
type Name = [string, string];
type ID = [number, number];
type DevTuples = [...Name, ...Numbers]
```

有了这两个补充，我们可以为我们的`concat`函数编写一个更好的函数签名:

```
type Arr = readonly any[];
function typedConcat<T extends Arr, U extends Arr>(arr1: T, arr2: U): [...T, ...U] {
    return [...arr1, ...arr2];
}
console.log("concated", typedConcat([1,2,3,4,5], [66,77,88,99]))

```

### 标记元组元素

这是对 TypeScript 的一个简洁的补充，旨在提高代码的可读性。

考虑下面的代码:

```
type Period = [Date, Date]; // Example 1 older version

type Period = [StartDate: Date, EndDate: Date]; // Example 2 4.0 beta

function getAge(): [birthDay: Date, today: Date] {
  // ...
}
```

以前，TypeScript 开发人员使用注释来描述元组，因为类型本身(日期、数字、字符串)不足以描述元素所代表的内容。

从我们上面的小例子来看，“例 2”可读性更好，因为在元组中添加了标签。

> 当标记元组时，必须标记元组中的所有项目。

考虑下面的代码:

```
type Period = [startDate: Date, Date]; // incorrect
type Period = [StartDate: Date, EndDate: Date]; // correct
```

### 从构造函数推断类属性

在 TypeScript 4.0 中，当`noImplicitAny`被启用时，我们现在可以使用控制流分析来确定类中属性的类型。让我们用一些代码示例来详细说明这一点。

考虑下面的代码:

```
// Compile with --noImplicitAny
class CalArea {
    Square;  // string | number
    constructor(area: boolean, length: number, breadth: number) {
        if (!area) {
            this.Square = "No area available";
        }
        else {
            this.Square = length * breadth;
        }
    }
}
```

以前，如果启用了`noImplicitAny`，上面的代码将无法编译。这是因为属性类型只能从直接初始化中推断出来，所以它们的类型必须要么显式定义，要么使用初始初始化器。

但是 TypeScript 4.0 可以使用构造函数中`this.Square`赋值的控制流分析来确定`Square`的类型。

### 短路赋值运算符

目前，在 JavaScript 中，许多二元运算符可以与赋值运算符组合在一起，形成复合赋值运算符。这些运算符对两个操作数执行二元运算符的运算，并将值赋给左操作数:

```
// compound operators
foo += bar // foo = foo + bar
foo -= bar // foo = foo - bar
foo *= bar // foo = foo * bar
foo /= bar // foo = foo/bar
foo %= bar // foo = foo % bar
```

这份清单还在继续，但有三个例外:

```
|| // logical or operator
&& // logical and operator
?? // nullish coalescing operator
```

TypeScript 4.0 beta 允许我们将这三个运算符与赋值运算符结合起来，从而形成三个新的复合运算符:

```
x ||= y // x || (x = y)
x &&= y // x && (x = y)
x ??= y // x ?? (x = y )
```

### `unknown`上的`catch`子句绑定

以前，当我们在 TypeScript 中使用`try … catch`语句时，catch 子句总是被类型化为`any`，因此，我们的错误处理代码缺乏任何类型安全，这应该可以防止无效操作。我将在下面详细介绍一些代码示例:

```
try {
  // ...
}catch(error) {
  error.message
  error.toUpperCase()
  error.toFixed()
  // ...
}
```

从上面的代码中我们可以看到，我们被允许做任何我们想做的事情——这实际上是我们不想要的。

TypeScript 4.0 旨在通过允许我们将 catch 变量的类型设置为`unknown`来解决这个问题。这更安全，因为这意味着提醒我们在代码中进行手动类型检查:

```
 try {
  // ...
}catch(error: unknown) {
  if(typeof error === "String") {
    error.toUpperCase()
  }

  if(typeof error === "number") {
    error.toFixed()
  }
  // ...
}
```

## 定制 JSX 工厂

TypeScript 已经支持`jsxFactory`编译器选项，但是，这个特性添加了一个新的编译器选项，称为`jsxFragmentFactory`，它使用户能够在`tsconfig.json`中定制 React.js 片段工厂:

```
{
  "compilerOptions": {
    "target": "esnext",
    "module": "commonjs",
    "jsx": "react", // React jsx compiler option
    "jsxFactory": "createElement", // transforms jsx using createElement
    "jsxFragmentFactory": "Fragment" // transforms jsx using Fragment
  }
}
```

上面的`tsconfig.json`配置以一种与 React 兼容的方式转换`JSX`，因此像`<article />`这样的`JSX`片段将会用`createElement`而不是`React.createElement`来转换。此外，它告诉 TypeScript 使用`Fragment`而不是`React.Fragment`进行`JSX`转换。

## 值得注意的提及

TypeScript 4.0 还在`--build`模式场景中提供了巨大的性能改进，并允许我们在利用`--incremental`编译的同时使用`--noEmit`标志。这在旧版本中是可能的。

此外，还有一些编辑器改进，如`@deprecated` JSDoc 注释识别，更智能的自动导入，启动时的部分编辑模式(旨在加快启动时间)。

## 结论

TypeScript 4.0 于 8 月 18 日发布，所有这些令人兴奋的功能和改进都随之推出。毫无疑问，这些将改善开发人员的体验和使用 TypeScript 的效率。你可以在这里找到更多关于 4.0 [的细节。](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-0.html)

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.