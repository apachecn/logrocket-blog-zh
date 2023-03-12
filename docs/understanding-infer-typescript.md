# 理解 TypeScript 中的推断

> 原文：<https://blog.logrocket.com/understanding-infer-typescript/>

我们都遇到过这样的情况，我们使用的库很少被输入。以下面的第三方函数为例:

```
function describePerson(person: {
  name: string;
  age: number;
  hobbies: [string, string]; // tuple
}) {
  return `${person.name} is ${person.age} years old and love ${person.hobbies.join(" and  ")}.`;
}

```

如果库没有为`describePerson`的`person`参数提供独立的类型，那么预先将变量定义为`person`参数将不会被 TypeScript 正确地推断出来。

```
const alex = {
  name: 'Alex',
  age: 20,
  hobbies: ['walking', 'cooking'] // type string[] != [string, string]
}

describePerson(alex) /* Type string[] is not assignable to type [string, string] */

```

TypeScript 会将`alex`的类型推断为`{ name: string; age: number; hobbies: string[] }`，并且不允许将其用作`describePerson`的参数。

而且，即使这样，最好对`alex`对象本身进行类型检查，以实现正确的自动完成。多亏了 TypeScript 中的`infer`关键字，我们可以很容易地实现这一点。

```
const alex: GetFirstArgumentOfAnyFunction<typeof describePerson> = {
  name: "Alex",
  age: 20,
  hobbies: ["walking", "cooking"],
};

describePerson(alex); /* No TypeScript errors */ 

```

TypeScript 中的`infer`关键字和条件类型允许我们获取一个类型并将其任何部分分离出来以备后用。

## 无值`never`类型

在 TypeScript 中，`never`被视为“无值”类型。你会经常看到它被当作死路一条的类型。TypeScript 中类似于`string | never`的联合类型将计算为`string`，丢弃`never`。

为了理解这一点，您可以将`string`和`never`视为数学集合，其中`string`是保存所有字符串值的集合，`never`是不保存任何值的集合( [∅集合](https://en.wikipedia.org/wiki/Empty_set))。这两个集合的联合显然只是前者。

相比之下，并集`string | any`的计算结果为`any`。同样，你可以认为这是`string`集合和[通用集合](https://en.wikipedia.org/wiki/Universal_set) (U)之间的联合，它包含所有集合，毫不奇怪，它对自身求值。

这就解释了为什么`never`会被用作逃生口，因为，结合其他类型，它会消失。

## 在 TypeScript 中使用条件类型

条件类型根据类型是否满足某个约束来修改类型。它的工作方式类似于 JavaScript 中的 ternaries。

### `extends`关键字

在 TypeScript 中，使用`extends`关键字来表示约束。`T extends K`意味着假设`T`类型的值也是`K`类型是安全的，例如`0 extends number`，因为`var zero: number = 0`是类型安全的。

因此，我们可以有一个检查是否满足约束的泛型，并返回不同的类型。

`StringFromType`根据收到的原始类型返回一个文字字符串:

```
type StringFromType<T> = T extends string ? 'string' : never

type lorem = StringFromType<'lorem ipsum'> // 'string'
type ten = StringFromType<10> // never

```

为了让我们的`StringFromType`泛型涵盖更多的情况，我们可以像 JavaScript 中嵌套三元运算符一样链接更多的条件。

```
type StringFromType<T> = T extends string
  ? 'string'
  : T extends boolean
  ? 'boolean'
  : T extends Error
  ? 'error'
  : never

type lorem = StringFromType<'lorem ipsum'> // 'string'
type isActive = StringFromType<false> // 'boolean'
type unassignable = StringFromType<TypeError> // 'error'

```

### 条件类型和联合

在将联合作为约束进行扩展的情况下，TypeScript 将对联合的每个成员进行循环，并返回自己的联合:

```
type NullableString = string | null | undefined

type NonNullable<T> = T extends null | undefined ? never : T // Built-in type, FYI

type CondUnionType = NonNullable<NullableString> // evalutes to `string`

```

TypeScript 将通过循环我们的 union`string | null | undefined`来测试约束`T extends null | undefined`，一次一个类型。

您可以将其视为以下说明性代码:

```
type stringLoop = string extends null | undefined ? never : string // string

type nullLoop = null extends null | undefined ? never : null // never

type undefinedLoop = undefined extends null | undefined ? never : undefined // never

type ReturnUnion = stringLoop | nullLoop | undefinedLoop // string

```

因为`ReturnUnion`是`string | never | never`的并集，所以它的值为`string`(参见上面的解释。)

您可以看到如何将扩展的 union 抽象成我们的泛型，从而允许我们在 TypeScript 中创建内置的`Extract`和`Exclude`实用程序类型:

```
type Extract<T, U> = T extends U ? T : never
type Exclude<T, U> = T extends U ? never : T

```

### 条件类型和函数

为了检查一个类型是否扩展了某个函数形状，不得使用`[Function](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/docs/rules/ban-types.md#:~:text=Avoid%20the%20Function,the%20new%20keyword)`[类型](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/docs/rules/ban-types.md#:~:text=Avoid%20the%20Function,the%20new%20keyword)。相反，以下签名可用于扩展所有可能的函数:

```
type AllFunctions = (…args: any[]) => any

```

`…args: any[]`将覆盖零个和更多的参数，而`=> any`将覆盖任何返回类型。

## 在 TypeScript 中使用`infer`

`infer`关键字补充了条件类型，不能在`extends`子句之外使用。`Infer`允许我们在约束中定义一个变量来引用或返回。

以内置的 TypeScript `ReturnType`实用程序为例。它接受一个函数类型并给出它的返回类型:

```
type a = ReturnType<() => void> // void
type b = ReturnType<() => string | number> // string | number
type c = ReturnType<() => any> // any

```

它首先检查您的类型参数(`T`)是否是一个函数，在检查过程中，返回类型被转换成一个变量，`infer R`，如果检查成功则返回:

```
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;

```

如前所述，这主要用于访问和使用我们不可用的类型。

### 反应正确类型

在 React 中，我们经常需要访问适当类型。为了做到这一点，React 提供了一个实用类型来访问由关键字`infer`驱动的属性类型，称为`ComponentProps`。

```
type ComponentProps<
  T extends keyof JSX.IntrinsicElements | JSXElementConstructor<any>
> = T extends JSXElementConstructor<infer P>
  ? P
  : T extends keyof JSX.IntrinsicElements
  ? JSX.IntrinsicElements[T]
  : {}

```

在检查了我们的类型参数是一个 React 组件之后，它推断出它的 props 并返回它们。如果失败，它检查类型参数是否是一个`IntrinsicElements` ( `div`，`button`，等等。)并返回其道具。如果全部失败，则返回`{}`，在 TypeScript 中，这意味着“任何非空值”。

## `Infer`关键词用例

使用`infer`关键字通常被描述为展开一个类型。以下是`infer`关键字的一些常见用法。

### 函数的第一个参数:

这是我们第一个例子的解决方案:

```
type GetFirstArgumentOfAnyFunction<T> = T extends (
  first: infer FirstArgument,
  ...args: any[]
) => any
  ? FirstArgument
  : never

type t = GetFirstArgumentOfAnyFunction<(name: string, age: number) => void> // string

```

### 函数的第二个参数:

```
type GetSecondArgumentOfAnyFunction<T> = T extends (
  first: any,
  second: infer SecondArgument,
  ...args: any[]
) => any
  ? SecondArgument
  : never

type t = GetSecondArgumentOfAnyFunction<(name: string, age: number) => void> // number

```

### 承诺返回类型

```
type PromiseReturnType<T> = T extends Promise<infer Return> ? Return : T

type t = PromiseReturnType<Promise<string>> // string 

```

### 数组类型

```
type ArrayType<T> = T extends (infer Item)[] ? Item : T

type t = ArrayType<[string, number]> // string | number

```

## 结论

关键字`infer`是一个强大的工具，它允许我们在处理第三方类型脚本代码时解包和存储类型。在本文中，我们解释了使用`never`关键字、`extends`关键字、联合和函数签名编写健壮的条件类型的各个方面。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.