# 类型脚本枚举与用于编写可读代码的类型

> 原文：<https://blog.logrocket.com/writing-readable-code-with-typescript-enums-a84864f340e9/>

TypeScript(您可能已经知道)是一种开源、强类型、面向对象的编译语言，由微软团队开发和维护。它是带有静态类型选项的 JavaScript 的超集。它是为开发编译成 JavaScript 的大型可伸缩应用程序而设计的。

## 什么是枚举？

在大多数面向对象的编程语言中，如 C、C#和 Java，有一种数据类型我们称之为枚举，简称 enums。Java 枚举是一种特殊的 Java 类，用于定义常量集合。然而，JavaScript 没有 enum 数据类型，但是幸运的是，从版本 2.4 开始，它们现在[在 TypeScript 中可用。](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-4.html)

枚举允许我们定义或声明相关值的集合，这些值可以是数字或字符串，作为一组命名的常量。与 TypeScript 中的某些可用类型不同，枚举是经过预处理的，不会在编译时或运行时进行测试。

## 为什么是 enums？

枚举只是在 TypeScript 中组织代码的一种有用方式。以下是枚举派上用场的一些原因:

*   使用枚举，您可以创建易于关联的常数，使常数更易于阅读
*   开发人员可以自由地用 JavaScript 创建节省内存的自定义常量。众所周知，JavaScript 不支持枚举，但是 TypeScript 可以帮助我们访问它们
*   如前所述，TypeScript 枚举通过 JavaScript 中的内联代码节省了运行时和编译时间(我们将在本文后面看到)
*   类型脚本枚举还提供了一定的灵活性，这是我们以前只有在 Java 这样的语言中才有的。这种灵活性使得表达和记录我们的意图和用例变得容易

## 枚举语法

枚举是用 enum 关键字定义的，如下所示:

```
enum Continents {
    North_America,
    South_America,
    Africa,
    Asia,
    Europe,
    Antartica,
    Australia
}

// usage
var region = Continents.Africa;
```

## 类型脚本枚举的类型

有三种类型的类型脚本枚举，即:

1.  数字枚举
2.  字符串枚举
3.  异质枚举

### 数字枚举

默认情况下，TypeScript 枚举是基于数字的。这意味着它们可以将字符串值存储为数字。可以将数字以及与其兼容的任何其他类型分配给枚举的实例。假设我们想存储周末的天数。TypeScript 中表示枚举的形式如下:

```
enum Weekend {
  Friday,
  Saturday,
  Sunday
}
```

在上面的代码块中，我们有一个名为`Weekend`的枚举。枚举有三个值:`Friday`、`Saturday`和`Sunday`。在 TypeScript 中，就像在其他一些语言中一样，枚举值从零开始，每个成员增加 1。它们将按如下方式存储:

```
Friday = 0
Saturday = 1
Sunday = 2
```

我们看到枚举总是被分配存储的数字；尽管我们可以用自己的逻辑定制存储值，但该值总是取数值零。

#### 自定义数字枚举

在 TypeScript 中，允许我们指定枚举的第一个数值。使用上面的周末示例，我们可以像这样初始化第一个数值:

```
enum Weekend {
  Friday = 1,
  Saturday,
  Sunday
}
```

上面的代码块将把`Friday`存储为`1`、`Saturday`存储为`2`、`Sunday`存储为`3`。如果我们给第一个成员加一个数，我们仍然会给其余的成员加一。然而，我们有权力通过给它们任何数值来决定我们不需要一个连续的轨迹。下面的代码块是语义的，在 TypeScript 中工作:

```
enum Weekend {
  Friday = 1,
  Saturday = 13,
  Sunday = 5
}
```

就像 TypeScript 中的其他数据类型一样，我们可以使用枚举作为函数参数或返回类型，如下所示:

```
enum Weekend {
  Friday = 1,
  Saturday,
  Sunday
}
function getDate(Day: string): Weekend {
    if ( Day === 'TGIF') {
        return Weekend.Friday;
    }
 }
let DayType: Weekend = getDate('TGIF');
```

我们在上面声明了一个`Weekend`枚举。然后我们声明了一个接受输入`Day`的`getDate`函数，该函数返回一个`Weekend`枚举。在函数中，我们检查一些现在返回枚举成员的条件。

#### 计算枚举

与 TypeScript 中的任何其他数字数据类型一样，数值枚举的值可以是常量，也可以是计算值。您可以使用计算值定义或初始化数值枚举:

```
enum Weekend {
  Friday = 1,
  Saturday = getDate('TGIF'),
  Sunday = Saturday * 40
}

function getDate(day : string): number {
    if (day === 'TGIF') {
        return 3;
    }
}
Weekend.Saturday; // returns 3
Weekend.Sunday; // returns 120
```

当枚举包含计算成员和常数成员时，未初始化的枚举成员要么在前面，要么必须跟在其他具有数字常数的已初始化成员后面。

忽略上述规则会导致初始化错误；如果看到这种情况，记得相应地重新排列枚举成员。

#### 常数枚举

如果想提高数值枚举的性能，可以将它们声明为常量。让我们用周末的例子来说明:

```
enum Weekend {
  Friday = 1,
  Saturday,
  Sunday
}
var day = Weekend.Saturday;
```

编译成 JavaScript 时，运行时查找`Weekend`，执行时查找`Weekend.Saturday`。为了在运行时获得最佳性能，您可以将枚举改为常量，如下所示:

```
const enum Weekend {
  Friday = 1,
  Saturday,
  Sunday
}
var day = Weekend.Saturday;
```

编译时使用常数生成的 JavaScript 是:

```
var day = 2;
```

我们看到编译器只是内联 enum 用法，当它看到`const`时，甚至懒得为 enum 声明生成 JavaScript。当您有需要数字到字符串或字符串到数字查找的用例时，了解这种选择以及可能导致的后果是很重要的。

也可以传递编译器标志`preserveConstEnums`，它仍然会生成`Weekend`定义。

### 字符串枚举

到目前为止，我们只看了数字枚举，其中的成员值是数字。在 TypeScript 中，枚举成员也可以是字符串值。[字符串枚举](https://blog.logrocket.com/typescript-string-enums-guide/)由于其有意义的字符串值，所以在错误记录和调试期间，出于可读性的目的，字符串枚举是至关重要且易于处理的。

```
enum Weekend {
  Friday = 'FRIDAY',
  Saturday = 'SATURDAY',
  Sunday = 'SUNDAY'
}
```

然后，它可以用于比较条件语句中的字符串，如下所示:

```
enum Weekend {
  Friday = 'FRIDAY',
  Saturday = 'SATURDAY',
  Sunday ='SUNDAY'
}
const value = someString as Weekend;
if (value === Weekend.Friday || value === Weekend.Sunday){
    console.log('You choose a weekend');
    console.log(value); 
}
```

在上面的例子中，我们定义了一个字符串枚举，`Weekend`，就像上面的数字枚举一样，但是这次枚举值是字符串。

数字枚举和字符串枚举的明显区别在于，数字枚举值大多是自动按顺序递增的，而字符串枚举值不会递增；相反，每个值都是独立初始化的。

### 异质枚举

TypeScript 还允许混合使用字符串和数字，称为异类枚举值:

```
enum Weekend {
  Friday = 'FRIDAY',
  Saturday = 1,
  Sunday = 2
}
```

尽管这是可能的，但是可能需要这个用例的场景范围确实很小。因此，除非我们真的试图以一种巧妙的方式利用 JavaScript 的运行时行为，否则建议我们不要使用异构枚举。

## 反向映射

TypeScript 枚举支持反向映射，这仅仅意味着正如我们可以访问枚举成员的值一样，我们也可以访问枚举名称本身。我们将使用第一次演示的样本来描述这一点:

```
enum Weekend {
  Friday = 1,
  Saturday,
  Sunday
}
Weekend.Saturday     
Weekend["Saturday"];  
Weekend[2];
```

在上面的代码块中，`Weekend.Saturday`将返回`2`，然后`Weekend["Saturday"]`也将返回`2`。然而有趣的是，由于反向映射，`Weekend[2]`将返回其成员名称`Saturday`。我们可以看到 TypeScript 用 log 命令解释反向映射的一种简单方式:

```
enum Weekend {
  Friday = 1,
  Saturday,
  Sunday
}
console.log(Weekend);
```

如果在控制台中运行，您将看到以下输出:

```
{
  '1': 'Friday',
  '2': 'Saturday',
  '3': 'Sunday',
  Friday   : 1,
  Saturday : 2,
  Sunday  : 3
}
```

对象包含既作为值又作为名称出现的枚举，正如类型脚本所预期的那样。这显示了 TypeScript 中反向映射的能力。

## TypeScript 枚举最佳实践

在某些用例中，使用枚举是最佳和有效的。也有这样的情况，你应该[把枚举放在一边](https://blog.logrocket.com/put-the-typescript-enums-and-booleans-away/)。下面，我们将讨论 TypeScript 枚举的最佳实践以及何时使用替代方法。

### 何时使用类型脚本枚举

枚举应该理想地用在存在可以被视为常量的不同值的情况下，例如一周七天:

```
enum Days {
  Sunday = 1,
  Monday,
  Tuesday,
  Wednesday,
  Thursday,
  Friday,
  Saturday
}
```

枚举可以像其他 TypeScript 数据类型一样在数组初始化中使用。这里有一个简单的例子:

```
enum NigerianLanguage {
  Igbo,
  Hause, 
  Yoruba
}

//can be used in array initialisation 
let citizen = {
  Name: 'Ugwunna',
  Age: 75,
  Language: NigerianLanguage.Igbo
}

```

枚举也可以用在需要在变量中表示字符串或常量的地方。

### 类型脚本枚举与替代

TypeScript 枚举不适用于下列情况:

*   当您计划重新分配或更改枚举成员值时，枚举是类型安全的，因此在重新分配时会返回编译错误
*   当您想要记录动态值时，枚举最适合有限的项，其背后的一般思想是帮助创建一个用户定义的常量系统
*   枚举不能用作变量；这样做会返回错误

## 结论

我们已经能够很好地了解 TypeScript 中的枚举，包括它们的类型和属性。我们还看到了它们的语法和使用实例。我们看到了其他重要的枚举方面，如枚举中的常数、计算枚举，甚至反向映射。

值得注意的是，对于字符串枚举，不支持反向映射。此外，对于异类成员，它只支持数字类型成员，而不支持字符串类型成员。编码快乐！

### 延伸阅读:

[LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

## LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

.

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.