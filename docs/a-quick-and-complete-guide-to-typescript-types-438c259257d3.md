# 一个快速完整的类型脚本类型指南

> 原文：<https://blog.logrocket.com/a-quick-and-complete-guide-to-typescript-types-438c259257d3/>

### 使用类型

在 TypeScript 中，变量的类型在变量声明之前在右侧定义。如果我们想定义变量`name`的类型，它看起来会像下面的代码片段:

```
const name: string = 'John';
```

可以使用的类型有:

*   声明变量时
*   在函数参数中
*   对函数的返回值进行类型检查

### 变量声明

当在 TypeScript 中声明一个变量时，我们使用了`let`和`const`关键字。你可以输入校验数组，字符串，数字等。

**数组** TypeScript 和 JavaScript 一样，允许你处理数值数组。数组类型可以用两种方式之一编写。在第一个例子中，使用元素的类型后跟`[]`来表示该元素类型的数组:

```
const names: string[] = ['John', 'Peter', 'Mark'];
const ages: number[] = [23, 45, 56];
```

此外，我们可以使用通用数组类型`Array<elementType>`，其中`elementType`是数组中包含的元素的类型。一个例子是这样的:

```
const names: Array<string> = ['Mark', 'Peter', 'John'];
const ages: Array<number> = [56, 45, 23];
```

现在，如果您的数组将包含几个类型，元组开始发挥作用。

**元组**元组允许你声明一个数组，其中固定数量的元素的类型是已知的，但不需要相同。例如，您可能希望将一个值表示为一对`string`和`number`:

```
let names: [string, number];
names = ['peter', 23]; // Correct
names = [23, 'John']; // Error
names = [23, 'John', 33, 'Peter' ] // Correct
```

最后一个例子是一个多于两个字符的数组，这没有出错，因为我们提供了额外的元素，或者是一个`string`或者是一个`number`。如果将一个布尔值添加到数组中，将会抛出一个错误。

```
names = ['Peter', 24, false] // error
names = ['John', 34, {} ] // error
```

**布尔**布尔是最基本的数据类型。不是`true`就是`false`。

```
const isHappy: boolean = true;
const canDrive: boolean = 34; // error
```

TypeScript 中的字符串有三种用法:

*   双引号。
*   单引号。
*   模板文字。

双引号:

```
let name: string = "Peter";
```

单引号:

```
name = 'John';
```

[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals):这些是允许嵌入表达式的字符串文字。您可以使用多行字符串和字符串插值功能。在 ES2015 规范的早期版本中，它们被称为“模板字符串”。这些字符串用反勾/反引号(" ` ")字符括起来，嵌入表达式的形式是`${ expr }`。

```
let color: string = 'green';
let amount: number = 3;
let car: string = 'Benz';
let sentence: string = `John has ${amount} cars. They are all ${color}, his favorite is the ${car}`.
```

**数字**在 JavaScript 中，所有的数字都有确定的数字类型。所有的 JavaScript 数字都是浮点值，TypeScript 也一样。TypeScript 还支持二进制和八进制文本以及十六进制和十进制值。

```
let hexadecimal: number = 0xf00d;
let decimal: number = 23.34;
let binary: number = 0b1010;
let octal: number = 0o744;
```

枚举是一种命名数值集合的友好方式。枚举从 0 开始编号，但您可以手动设置其中一个成员的值。

```
enum Car {BENZ, TOYOTA, HONDA}
const myCar: Car = Car.TOYOTA;
```

或者我们可以手动设置枚举的值:

```
enum Car {BENZ = 2, TOYOTA = 4, HONDA = 6}
const myCar: Car = Car.HONDA;
```

枚举的一个便利特性是，您还可以在枚举中从一个数值转到该值的名称。例如，如果我们有值`6`，但不确定它映射到上面的`Car`枚举中的什么，我们可以查找相应的名称。

```
enum Car { BENZ=2, TOYOTA, HONDA=6 }
const myCar: string = Car[6];
```

**任何**有时候，我们可能不知道我们在和什么类型的人一起工作。这时我们就可以使用`any`类型了。`any`类型允许我们选择不进行类型检查。

```
let myCar: any = 'honda';
myCar = false; // correct
myCar = 34; // correct
```

`any`型非常灵活。比 JavaScript `object`更是如此。使用`any`类型，您可以在代码中不断地选择加入或退出类型检查。

```
let car: any = 'honda';
car.start(); // compiles
```

**Void** Void 几乎是`any`的直接反义词，它描绘了一种类型的缺失。它通常用于定义函数的返回类型。

```
function startCar():void {
    console.log('Car started');
}
```

当声明变量时，将变量类型定义为`void`并不真正有用，因为你只能将变量设置为`undefined`或`null`。

**Null 和 Undefined** `Null`和`Undefined`都有各自的类型命名。这些类型本身没有用，因为我们只能将`Null`和`Undefined`赋给定义为`Null`或`Undefined`类型的变量。

```
let n: null = null;
n = 43; //compile error
let u: undefined = undefined;
u = 'string'; // compile error
```

自然，`null`和`undefined`是任意类型的子类型。所以你可以把`null`或者`undefined`分配给`number`或者`string`。

**从不**`never`类型代表从不出现的值的类型。例如，`never`是函数表达式或箭头函数表达式的返回类型，它总是抛出异常或从不返回；当被任何不可能为真的类型守卫缩小时，变量也会获得类型`never`。

`never`类型是每个类型的子类型，并且可以分配给每个类型；然而，*没有*类型是`never`的子类型，或者可分配给`never`(除了`never`本身)。甚至`any`都不能分配给`never`。一些函数返回的例子`never`:

```
function throwIt(message: string): never{
    throw new Error(message);
}

// Inferred return type is never
function fail() {
    return error("Something failed");
}
```

**函数类型**我们可以定义函数参数和函数返回值的类型。当我们在一个没有任何值的函数上使用`void`时，我们做了类似的事情。

```
function returnValue(message: string): string{
    return message;
}
```

### 结论

我们快速浏览了打字稿的类型。在 TypeScript 中有更高级的类型应用，比如创建声明文件等。你可以在这里阅读更多关于创建声明文件的信息。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.