# TypeScript - LogRocket 博客中的可选链接和 nullish 合并

> 原文：<https://blog.logrocket.com/optional-chaining-nullish-coalescing-typescript/>

在 TypeScript 中，可选链接被定义为当表达式的一部分计算为`null`或`undefined`时立即停止运行表达式的能力。它是在 TypeScript 3.7 中用`?.`操作符引入的。

可选链接通常与 nullish 合并一起使用，nullish 合并是指当主表达式的值为`null`或`undefined`时，能够返回到默认值。在这种情况下，要使用的操作员是`??`。

在本文中，我们将探索使用这两个操作符的方法，并将它们与遗留的 TypeScript 3.6 代码进行比较。

目录:

## 类型系统中的`null`和`undefined`

TypeScript 的类型系统定义了两个非常特殊的类型，`null`和`undefined`，分别表示值`null`和`undefined`。然而，默认情况下，TypeScript 的类型检查器认为`null`和`undefined`是任何类型的合法值。例如，我们可以将`null`赋值给`string`类型的值:

```
let str: string = "test";
str = null;

```

然而，如果我们启用了`strictNullChecks`标志，`null`和`undefined`类型将在类型检查中被考虑。在这种情况下，上面的代码片段不会再输入 check，我们会收到下面的错误消息:“Type `null`不能赋值给 type `string`”

类型`null`和`undefined`被区别对待，以匹配 JavaScript 使用的相同语义:`string | null`是不同于`string | undefined`的类型，而`string | undefined`又不同于`string | null | undefined`。例如，下面的表达式将不进行类型检查，错误消息将是“类型`undefined`不可分配给类型`string | null`”：

```
let nullableStr: string | null = undefined;
```

## 对`null`和`undefined`进行显式检查

在 3.7 版本之前，TypeScript 不包含任何运算符来帮助我们处理`null`或`undefined`值。因此，在访问假设它们的变量之前，我们必须添加显式检查:

```
type nullableUndefinedString = string | null | undefined;

function identityOrDefault(str: nullableUndefinedString): string {
    // Explicitly check for null and undefined
    // Handle the possible cases differently
    if (str === null || str === undefined) {
            return "default";
    } else {
            return str;
    }
}
```

我们首先声明一个新的类型，`nullableUndefinedString`，它代表一个字符串，也可能是`null`或`undefined`。然后，我们实现一个函数，如果定义了这个函数，它返回作为参数传入的相同字符串，否则返回字符串`"default"`。

这种方法工作得很好，但是会导致复杂的代码。此外，在呼叫链的不同部分可能是`null`或`undefined`的情况下，很难连接呼叫:

```
class Person {
    private fullName: nullableUndefinedString;

    constructor(fullName: nullableUndefinedString) {
        this.fullName = fullName;
    }

    getUppercaseFullname(): nullableUndefinedString {
        if (this.fullName === null || this.fullName === undefined) {
            return undefined;
        } else {
            return this.fullName.toUpperCase();
        }
    }

    getFullNameLength(): number {
        if (this.fullName === null || this.fullName === undefined) {
            return -1;
        } else {
            return this.fullName.length;
        }
}
```

在上面的例子中，我们使用之前介绍的类型`nullableUndefinedString`定义了一个类`Person`。然后，我们实现了一个方法，以大写形式返回这个人的全名。由于我们访问的字段可能是`null`或`undefined`，我们必须不断检查实际值。例如，在`Person::getUppercaseFullName()`中，如果没有定义全名，我们将返回`undefined`。

这种实现方式相当麻烦，阅读和维护都很困难。因此，从 3.7 版开始，TypeScript 引入了可选的链接和 nullish 合并。

## 可选链接

正如我们在简介中看到的，可选链接的核心是`?.`操作符，允许我们在运行时遇到`null`或`undefined`时停止运行表达式。在本节中，我们将看到这种操作符的三种可能的用法。

### 可选的属性访问和可选的调用

使用`?.`，我们可以用更简单的方式重写`Person::getUppercaseFullName()`:

```
class Person {
    private fullName: nullableUndefinedString;

    constructor(fullName: nullableUndefinedString) {
        this.fullName = fullName;
    }

    getUppercaseFullname(): nullableUndefinedString {
        return this.fullName?.toUpperCase();
    }

}
```

`Person::getUppercaseFullName()`的实现现在是一行程序。如果定义了`this.fullName`，那么将计算`this.fullName.toUpperCase()`。否则，`undefined`将返回，和以前一样。

尽管如此，第一次调用`?.`返回的表达式可能是`null`或`undefined`。因此，可选的属性访问操作符可以被链接:

```
let john = new Person("John Smith");
let nullPerson = new Person(null);
let people = [john, nullPerson];
let r = people.find(person => person.getUppercaseFullname() === "SOMEONE ELSE");
console.log(r?.getUppercaseFullname()?.length);
```

在上面的例子中，我们首先创建了一个数组`people`，包含两个类型为`Person`的对象。然后，使用`Array::find()`，我们搜索一个大写名字为`SOMEONE ELSE`的人。由于`Array.find()`返回`undefined`如果数组中没有元素满足约束，我们必须在打印名称的长度时考虑到这一点。在这个例子中，我们通过链接`?.`调用来实现。

再者，`r?.getUppercaseFullName()`代表`?.`的第二次使用，可选调用。这样，如果表达式不是`null`或`undefined`，我们可以有条件地调用它们。

如果我们不使用`?.`，我们将不得不使用更复杂的`if`语句:

```
if (r && r.getUppercaseFullname()) {
    console.log(r.getUppercaseFullname().length);
}
```

尽管如此，在`if`和`?.`调用链之间有一个重要的区别。前者短路，后者不短路。这是有意的，因为 new 运算符不会对有效数据(如 0 或空字符串)进行短路。

### 可选元素访问

`?.`的最后一个用途是可选的元素访问，允许我们访问非标识符属性，如果定义了的话:

```
function head<T>(list?: T[]) {
    return list?.[0];
    // equivalent to
    //   return (list === undefined) ? undefined : list[0]
}

console.log(head([1, 2, 3]));
console.log(head(undefined));
```

在上面的例子中，我们写了一个函数，如果定义了，它返回列表的第一个元素(假设列表被建模为数组)，否则返回`undefined`。`list`通过其名称后的问号表示为可选参数。因此，它的类型是`T[] | undefined`。

`head`的两个调用将分别打印`1`和`undefined`。

### 关于短路的一个注记

当在更大的表达式中包含可选链接时，它提供的短路不会比我们上面看到的三种情况扩展得更远:

```
function barPercentage(foo?: { bar: number }) {
    return foo?.bar / 100;
}

```

上面的示例显示了一个计算某个数值字段百分比的函数。尽管如此，由于`foo?.bar`可能是`undefined`，我们可能最终将`undefined`除以 100。这就是为什么在启用了`strictNullChecks`的情况下，上面的表达式不输入 check:“Object possible undefined。”

## 无效合并

Nullish 合并允许我们指定一种缺省值来代替另一个表达式，它被赋值为`null`或`undefined`。严格来说，`let x = foo ?? bar();`这个表达和`let x = foo !== null && foo !== undefined ? foo : bar();`是一样的。使用它，以及可选的属性访问，我们可以将`Person::getFullNameLength()`重写如下:

```
class Person {
    private fullName: nullableUndefinedString;

    constructor(fullName: nullableUndefinedString) {
        this.fullName = fullName;
    }

    getFullNameLength(): number {
        return this.fullName?.length ?? -1;
    }
}
```

这个新版本比以前可读性强得多。与`?.`类似，`??`只对`null`和`undefined`起作用。因此，例如，如果`this.fullName`是一个空字符串，返回值将是`0`，而不是`-1`。

## 结论

在本文中，我们分析了 TypeScript 3.7 中添加的两个特性:可选链接和无效合并。我们看到了如何将它们结合使用来编写简单易读的代码，而不用担心永无止境的条件句。我们还将它们与我们可以在 TypeScript 3.6 中编写的代码进行了比较，后者要冗长得多。

无论如何，总是建议启用`strictNullChecks`。这种标志附带了许多额外的类型检查，通过构造帮助我们编写更可靠、更安全的代码。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.