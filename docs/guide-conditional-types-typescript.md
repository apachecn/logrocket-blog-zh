# TypeScript 中的条件类型指南

> 原文：<https://blog.logrocket.com/guide-conditional-types-typescript/>

从 2.8 版开始，TypeScript 引入了对条件类型的支持。它们可能是一个利基特性，但是，正如我们将看到的，它们是一个非常有用的附加功能，可以帮助我们编写可重用的代码。

在本文中，我们将了解什么是条件类型，以及为什么我们可能会频繁地使用它们，即使我们并不知道这一点。

## 什么是条件类型？

条件类型让我们根据条件确定性地定义类型转换。简而言之，它们是应用于类型级别而不是值级别的三元条件运算符。

条件类型定义如下:

```
type ConditionalType = SomeType extends OtherType ? TrueType : FalseType
```

简单地说，上面的定义如下:

> 如果一个给定类型`SomeType`扩展了另一个给定类型`OtherType`，那么`ConditionalType`就是`TrueType`，否则就是`FalseType`。

通常，`extends`在这里意味着任何类型为`SomeType`的值也是类型为`OtherType`的值。

条件类型可以是递归的；也就是说，一个或两个分支本身可以是条件类型:

```
type Recursive<T> = T extends string[] ? string : (T extends number[] ? number : never)

const a: Recursive<string[]> = "10" // works
const b: Recursive<string> = 10 // Error: Type 'number' is not assignable to type 'never'.
```

### 条件类型的约束

条件类型的主要优势之一是它们能够缩小泛型类型的可能实际类型。

例如，假设我们想要定义`ExtractIdType<T>`，从一个泛型`T`中提取一个名为`id`的属性的类型。在这种情况下，实际的泛型类型`T`必须有一个名为`id`的属性。起初，我们可能会想到类似下面的代码片段:

```
type ExtractIdType<T extends {id: string | number}> = T["id"]

interface NumericId {
    id: number
}

interface StringId {
    id: string
}

interface BooleanId {
    id: boolean
}

type NumericIdType = ExtractIdType<NumericId> // type NumericIdType = number
type StringIdType = ExtractIdType<StringId> // type StringIdType = string
type BooleanIdType = ExtractIdType<BooleanId> // won't work
```

这里，我们明确表示`T`必须有一个名为`id`的属性，类型可以是`string`或`number`。然后，我们定义了三个接口:`NumericId`、`StringId`和`BooleanId`。

如果我们试图提取`id`属性的类型，TypeScript 会正确地分别为`StringId`和`NumericId`返回`string`和`number`。但是对于`BooleanId` : `Type 'BooleanId' does not satisfy the constraint '{ id: string | number; }'. Types of property 'id' are incompatible. Type 'boolean' is not assignable to type 'string | number'`则不成立。

但是，如果`T`没有定义所需的`id`属性，我们如何增强我们的`ExtractIdType`来接受任何类型`T`，然后求助于类似于`never`的东西呢？我们可以使用条件类型来做到这一点:

```
type ExtractIdType<T> = T extends {id: string | number} ? T["id"] : never

interface NumericId {
    id: number
}

interface StringId {
    id: string
}

interface BooleanId {
    id: boolean
}

type NumericIdType = ExtractIdType<NumericId> // type NumericIdType = number
type StringIdType = ExtractIdType<StringId> // type StringIdType = string
type BooleanIdType = ExtractIdType<BooleanId> // type BooleanIdType = never
```

通过简单地移动条件类型中的约束，我们能够使`BooleanIdType`的定义起作用。在第二个版本中，TypeScript 知道如果第一个分支为真，那么`T`将有一个名为`id`的属性，类型为`string | number`。

## 条件类型中的类型推理

使用条件类型来应用约束和提取属性类型是如此普遍，以至于我们可以为此使用一个更好的语法。例如，我们可以将`ExtractIdType`的定义改写如下:

```
type ExtractIdType<T> = T extends {id: infer U} ? T["id"] : never

interface BooleanId {
    id: boolean
}

type BooleanIdType = ExtractIdType<BooleanId> // type BooleanIdType = boolean
```

在这种情况下，我们细化了`ExtractIdType`类型。我们没有强制 id 属性的类型为 string | number，而是使用`infer`关键字引入了一个新的类型`U`。因此，`BooleanIdType`不会再对`never`求值了。事实上，TypeScript 会像预期的那样提取`boolean`。

`infer`为我们提供了一种引入新的泛型类型的方法，而不是指定如何从真正的分支中检索元素类型。

在文章的最后，我们将看到一些有用的依赖于`infer`关键字的内置类型。

## 分配条件类型

在 TypeScript 中，条件类型分布在联合类型上。换句话说，当根据联合类型进行计算时，条件类型适用于联合的所有成员。让我们看一个例子:

```
type ToStringArray<T> = T extends string ? T[] : never

type StringArray = ToStringArray<string | number>
```

在上面的例子中，我们简单地定义了一个名为`ToStringArray`的条件类型，当且仅当它的泛型参数是`string`时，计算结果为`string[]`。否则评估为`never`。

现在让我们看看 TypeScript 如何评估`ToStringArray<string | number>`来定义`StringArray`。第一，`ToStringArray`分布在联邦:

```
type StringArray = ToStringArray<string> | ToStringArray<number>
```

然后，我们可以用它的定义代替`ToStringArray`:

```
type StringArray = (string extends string ? string[] : never) | (number extends string ? number[] : never)
```

评估条件句给我们留下以下定义:

```
type StringArray = string[] | never
```

由于`never`是任何类型的子类型，我们可以将其从联合中移除:

```
type StringArray = string[]
```

大多数情况下，条件类型的分配属性是需要的。尽管如此，为了避免这种情况，我们可以用方括号将`extends`关键字的两边括起来:

```
type ToStringArray<T> = [T] extends [string] ? T[] : never
```

在这种情况下，当评估`StringArray`时，`ToStringArray`的定义不再分布:

```
type StringArray = ((string | number) extends string ? (string | number)[] : never)
```

因此，由于`string | number`不延伸，`string, StringArray`将变成`never`。

最后，如果联合类型是一个更大的表达式(即函数、对象或元组)的一部分，那么分布式属性就不成立，不管这个更大的表达式是出现在`extends`之前还是之后。让我们看一个例子:

```
type NonDistributiveFunction<T> = (() => T) extends (() => string | number) ? T : never
```

```
type Fun1 = NonDistributiveFunction<string | boolean> // type Fun1 = never

type Fun2 = NonDistributiveFunction<string> // type Fun2 = string
```

## 内置条件类型

这最后一节展示了几个由 TypeScript 的标准库定义的条件类型的例子。

### `NonNullable<T>`

`NonNullable<T>`从类型`T`中过滤出`null`和`undefined`值:

```
type NonNullable<T> = T extends null | undefined ? never : T
type A = NonNullable<number> // number
type B = NonNullable<number | null> // number
type C = NonNullable<number | undefined> // number
type D = NonNullable<null | undefined> // never
```

### `Extract<T, U> and Exclude<T, U>`

`Extract<T, U>`和是彼此的对立面。前者过滤`T`类型，保留所有可赋给`U`的类型。另一方面，后者将保留不可分配给`U`的类型:

```
type Extract<T, U> = T extends U ? T : never
type Exclude<T, U> = T extends U ? never : T

type A = Extract<string | string[], any[]> // string[]
type B = Exclude<string | string[], any[]> // string

type C = Extract<number, boolean> // never
type D = Exclude<number, boolean> // number
```

在上面的例子中，当定义`A`时，我们要求 TypeScript 从`string | string[]`中过滤掉所有不可分配给`any[]`的类型。那只会是字符串，因为`string[]`完全可以分配给`any[]`。相反，当我们定义`B`时，我们要求 TypeScript 做正好相反的事情。不出所料，结果是字符串，而不是`string[]`。

同样的论点适用于`C`和`D`。在`C`的定义中，数字不可分配给`boolean`。因此，TypeScript 推断`never`是一种类型。到了定义`D`的时候，反而是 TypeScript 保留了`number`。

### `Parameters<T> and ReturnType<T>`

`Parameters<T>`和`ReturnType<T>`让我们分别提取一个函数类型的所有参数类型和返回类型:

```
type Parameters<T> = T extends (...args: infer P) => any ? P : never
type ReturnType<T> = T extends (...args: any) => infer R ? R : any
type A = Parameters<(n: number, s: string) => void> // [n: number, s: string]
type B = ReturnType<(n: number, s: string) => void> // void

type C = Parameters<() => () => void> // []
type D = ReturnType<() => () => void> // () => void
type E = ReturnType<D> // void
```

`Parameters<T>`的声明有点复杂。它基本上会产生一个包含所有参数类型的元组类型(如果`T`不是函数，则为`never`)。

特别地，`(...args: infer P) => any`表示一个函数类型，其中所有参数(`P`)的实际类型被推断出来。任何函数都可以赋给它，因为对参数的类型没有限制，返回类型是`any`。

类似地，`ReturnType<T>`提取函数的返回类型。在这种情况下，我们使用`any`来表示参数可以是任何类型。然后，我们推断返回类型`R`。

### `ConstructorParameters<T> and InstanceType<T>`

`ConstructorParameters<T>`和`InstanceType<T>`与`Parameters<T>`和`ReturnType<T>`是一样的东西，应用于构造函数类型而不是函数类型:

```
type ConstructorParameters<T> = T extends new (...args: infer P) => any ? P : never
type InstanceType<T> = T extends new (...args: any[]) => infer R ? R : any

interface PointConstructor {
    new (x: number, y: number): Point
}

class Point {
    private x: number;

    private y: number;

    constructor(x: number, y: number) {
            this.x = x;
            this.y = y
    }
}

type A = ConstructorParameters<PointConstructor> // [x: number, y: number]
type B = InstanceType<PointConstructor> // Point
```

## 结论

在本文中，我们探讨了 TypeScript 中的条件类型。我们从基本定义以及如何使用它来实施约束开始。然后我们看到了类型推理是如何工作的，并探索了[联合类型](https://blog.logrocket.com/understanding-discriminated-union-intersection-types-typescript/)的分布式属性的工作原理。最后，我们查看了由 TypeScript 定义的一些常见的实用程序条件类型:我们分析了它们的定义，并用几个例子对它们进行了补充。

正如我们在本文中看到的，条件类型是类型系统的一个非常高级的特性。然而，我们最终很可能几乎每天都要使用它们，因为 TypeScript 的标准库广泛使用它们。

希望这篇文章能帮助你编写你自己的类型来简化你的代码，并使它更具可读性和可维护性。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.