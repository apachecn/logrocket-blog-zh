# TypeScript 字符串枚举，以及何时以及如何使用它们

> 原文：<https://blog.logrocket.com/typescript-string-enums-guide/>

## 基于字符串的枚举介绍

在这一节中，我们将探索 TypeScript 中的字符串枚举。

如果您是 JavaScript/TypeScript 领域的新手，您可能想知道什么是枚举。关键字`enums`为我们提供了一种定义有限值集的方法——通常以强类型的方式命名为常量。它们还允许我们为特定的收集组或类型指定一个值列表。

JavaScript 本身并不支持枚举，但是有一个变通方法可以让我们使用`Object.freeze`构造在语言中复制它们的行为，因为 TypeScript 将枚举视为真实对象(尽管在运行时适用于[非常数枚举](https://www.typescriptlang.org/docs/handbook/enums.html#enums-at-runtime))。我们可以使用如下例所示的结构:

```
const directionEnum = Object.freeze({ 
    UP :  "UP", 
    DOWN:  "DOWN" 
});  

console.log(directionEnum) //{ UP: 'UP', DOWN: 'DOWN' } 

```

这样做，我们得到一个只读的受保护的对象。注意只读定义仅在鼠标悬停在`directionEnum`常量上时显示:

```
const directionEnum: Readonly<{ 
    UP: string; 
    DOWN: string; 
}> 

```

`Object.freeze`方法防止修改现有的属性和属性值，也防止添加新的属性。这反映了枚举背后的思想，因为对于给定的变量声明，它们应该有一定数量的固定值。

[枚举在编程中不是一个新概念](https://blog.logrocket.com/writing-readable-code-with-typescript-enums-a84864f340e9/)。正如我们可能已经知道的，大多数编程语言，如 Java、C 等等，在它们的核心中都定义了枚举的概念。我们也可以将枚举描述为人工创建的类型，它包含有限的一组值，就像我们拥有的布尔类型一样，它只包含`false`和`true`值。

由于它们在其他语言中的有用性和优势，枚举被引入并添加到类型脚本中。然而，枚举对于 TS 来说是非常独特和特定的，因为尽管 TS 是 JS 的类型化超集，但它们不是 JavaScript 语言中特定功能的类型化扩展，也不等同于或对应于特定功能。

注意:目前有一个 ECMAScript stage-0 提议，将枚举添加到 JavaScript 中。更多详情请见[本回购](https://github.com/rbuckton/proposal-enum)。

## 基于数字的枚举介绍

默认情况下，枚举是基于数字的。对于基于数字的枚举，由于其自动递增的特性，成员彼此是不同的。它们可以有一个初始化器(由此我们显式地指定枚举成员值)，也可以没有。

下面我们来看一个例子。

```
enum statusEnumWithInitializer = { 
"OPEN" = 10, 
"CLOSE", 
} 
//statusEnumWithInitializer.CLOSE = 11  
//Since the following members are auto incremented from that point on.  

```

如果我们不考虑初始化器，我们会得到如下结果:

```
enum statusEnumWithoutInitializer = { 
"OPEN", 
"CLOSE", 
} 
//statusEnumWithoutInitializer.OPEN = 0 
//statusEnumWithoutInitializer.CLOSE = 1 

```

从上面的例子中我们可以看到，访问一个枚举的成员就像访问对象的属性一样简单。我们前面提到过，这是因为非常数具有类似对象的行为。

注意上面代码片段中的[结尾逗号](https://blog.logrocket.com/best-practices-using-trailing-commas-javascript/)。在现实世界中使用这些枚举类型非常简单，因为我们可以简单地将它们声明为[类型](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)，并将它们作为参数传递给函数。

由于基于数字的枚举的自动递增行为，当没有初始化器时，对于这些枚举存在一个警告。它们要么需要首先声明，要么必须在用基于数字的常数初始化的数字枚举之后，这也适用于[异构枚举](https://www.typescriptlang.org/docs/handbook/enums.html#heterogeneous-enums)。这是因为枚举[需要在编译时](https://www.typescriptlang.org/docs/handbook/enums.html#computed-and-constant-members)被完全评估。

## 什么时候我们需要基于字符串的枚举？

通常，当我们打算声明必须满足枚举声明中定义的特定标准的特定类型时，[枚举类型](https://blog.logrocket.com/writing-readable-code-with-typescript-enums-a84864f340e9/)就派上了用场。正如我们前面提到的，虽然枚举默认是基于数字的，[TypeScript≥2.4 版支持基于字符串的枚举](https://devblogs.microsoft.com/typescript/announcing-typescript-2-4/#string-enums)。

与对象文字一样，基于字符串的枚举支持使用方括号符号的计算名称，而基于数字的枚举通常不是这种情况。因此，这限制了我们在 JSON 对象中使用基于数字的枚举的能力，因为通常不可能计算这些类型的枚举成员的名称。

字符串枚举在传输协议上是可序列化的，并且很容易调试——因为它们毕竟只是字符串。它们还允许在运行时有一个有意义和可读的值，与枚举成员的名称无关。

对于完全基于字符串的枚举，我们不能省略任何初始值设定项，因为所有枚举成员都必须带有值。但是对于数字枚举来说情况就不一样了，它们最终只是普通的数字，因此可能没什么用处。

在本文中，我们将关注基于字符串的枚举。关于基于数字的枚举的更多信息，请参考 TypeScript 文档。如果你需要一个关于枚举类型的[一般介绍](https://blog.logrocket.com/writing-readable-code-with-typescript-enums-a84864f340e9/)或者一个关于枚举在语言中可能被误用的方式的[概述，LogRocket 博客可以满足你。](https://blog.logrocket.com/why-typescript-enums-suck/)

## 常量和计算枚举

基于常数的枚举是只有一个成员而没有初始值设定项值的枚举。这意味着它们被自动分配了`0`的值。

它们也可以有多个成员值，其中第一个成员必须是数字常量。这意味着后续值是通过在前面的数字常数上加 1 来递增的，顺序如下。

总之，对于常量枚举，枚举成员值可以是第一个没有初始值设定项的成员，或者必须有一个数值初始值设定项，如果它前面有其他枚举成员的话。

请注意，常量枚举值可以在编译时计算。对于计算枚举的情况，它们可以通过表达式初始化。请参见下面的计算枚举示例:

```
enum computedEnum { 
 a = 10 
 str = "str".length // computed enum 
 add = 300 + 100 //computer enum 
} 

```

## 指定枚举成员值

根据需要，我们可以通过以下方式指定枚举成员值:

*   文字枚举成员:文字枚举类型是这样的枚举类型，其中每个成员或者没有初始值设定项，或者具有指定数值文字、字符串文字或命名枚举类型中另一个成员的标识符的初始值设定项。值通过数字文本隐式初始化，或通过字符串文本显式初始化
*   常量枚举成员:基于常量的枚举成员只能使用字符串进行访问。它们是通过表达式初始化的，表达式可以使用字符串、数字、括号等隐式或显式指定
*   计算枚举成员:我们可以通过[任意表达式](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-1-6.html#extending-expressions)初始化这些成员

## 字符串文字和联合类型

通常，文字类型是 JavaScript 原始值。从[TypeScript≥1.8 版本](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-1-8.html)开始，我们可以创建字符串文字类型。具体来说，字符串类型允许我们定义一个只接受一个特定字符串的类型。就其本身而言，它们通常不是很有用，但是当它们与联合类型结合时，它们变得非常强大。

当与联合类型结合使用时，它们模拟字符串枚举的预期行为，因为它们也为命名字符串值提供了可靠和安全的体验。请参见下面的示例:

```
type TimeDurations = 'hour' | 'day' | 'week' | 'month'; 

var time: TimeDurations; 
time = "hour"; // valid  
time = "day";  //  valid 
time = "dgdf"; // errors 

```

从上面可以看出，`TimeDurations`类型看起来像一个字符串枚举，因为它定义了几个字符串常量。

枚举类型可以有效地成为每个枚举成员的联合类型。字符串文字和联合类型的组合提供了与枚举一样的安全性，并且具有更直接地转换为 JavaScript 的优势。它还在各种 ide 中提供了类似的强大自动完成功能。如果你很好奇，可以在 [TypeScript Playground](https://www.typescriptlang.org/play/) 上快速查看一下这个。

对于一个字符串枚举，我们可以从它的值的文字类型中[产生一个联合类型，但是它不会以其他方式发生。](https://www.typescriptlang.org/docs/handbook/enums.html#union-enums-and-enum-member-types)

## 基于字符串的枚举的用例及其重要性

基于字符串的枚举只在 2.4 版的 TypeScript 中引入了，它们使得将字符串值赋给枚举成员成为可能。让我们看看下面文档中的一个示例:

```
 enum Direction { 
  Up = "UP", 
  Down = "DOWN", 
  Left = "LEFT", 
  Right = "RIGHT", 
} 

```

在此之前，开发人员必须依赖使用字符串文字和联合类型来描述有限的字符串值集，就像基于字符串的枚举一样。例如，我们可以这样定义一个类型:

```
type Direction = "UP" | "DOWN" | "LEFT" | "RIGHT";  

```

然后，我们可以将该类型作为一个函数参数来使用，并且该语言可以在编译时检查这些确切的类型是否在实例化时传递给了函数。

总之，为了利用基于字符串的枚举类型，我们可以通过使用枚举的名称和它们相应的值来引用它们，就像访问对象的属性一样。

在运行时，基于字符串的枚举的行为就像对象一样，可以像普通对象一样轻松地传递给函数。参见 TypeScript 文档中关于枚举在运行时如何表现为对象的示例。

让我们记住，对于基于字符串的枚举，不能省略初始化器，这与基于数字的枚举不同，后者提供的初始化器要么是第一个成员，要么是前面的成员，并且必须有一个数值。每个枚举成员都必须用一个常量初始化，该常量可以是字符串文字，也可以是另一个枚举成员，该成员是字符串和字符串枚举的一部分。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

JSON 对象中大量使用字符串枚举来验证 API 调用，以确保参数被正确传递。另一个精彩的用例是它们在为预定义 API 定义领域特定值方面的应用。

枚举类型的重要性不能被夸大。例如，每当我们在代码中使用 enum 成员进行验证时，TypeScript 会静态检查是否使用了其他值。

它们对于确保安全的字符串常量也很有用。枚举提供了比使用布尔值更能自我描述的选项。相反，我们可以指定该域特有的枚举，这使得我们的代码更具描述性。

因此，如果需要的话，我们可以决定以后添加更多的选项，与使用布尔检查相比。让我们研究下面的示例，在该示例中，我们可以通过布尔检查并使用枚举类型声明来检查操作是成功还是失败:

```
class Result { 
  success: boolean; // in our code we can set this to either true or false 
  // also we must have seen constructs like `isSuccess` = true or false 
} 

//compared to using enums which are more descriptive of our intentions 
enum ResultStatus { FAILURE, SUCCESS } 

class enumResult { 
  status: ResultStatus; 
} 

```

为了创建一个类型，它的值是枚举成员的键，我们可以通过`typeof`方法使用`keyof`。

```
enum Direction { 
  Up = "UP", 
  Down = "DOWN", 
  Left = "LEFT", 
  Right = "RIGHT", 
} 

 // this is same as 
 //type direction = 'Up' | 'Down' | 'Left' | 'Right'; 
type direction = keyof typeof Direction; 

```

正如 TypeScript 文档所说，即使枚举是运行时存在的真实对象，[`keyof`关键字的工作方式与您可能期望的典型对象的](https://www.typescriptlang.org/docs/handbook/enums.html#enums-at-runtime)不同。

相反，使用`keyof typeof`将得到一个将所有枚举键表示为字符串的类型，正如我们在上面看到的。

## 比较基于数字和基于字符串的枚举

基于数字的枚举和基于字符串的枚举之间的一个微妙区别是，对于基于数字的枚举，对于数值成员有[反向映射。](https://www.typescriptlang.org/docs/handbook/enums.html#reverse-mappings)

反向映射允许我们检查给定值在枚举的上下文中是否有效。为了更好地理解这一点，让我们看看基于数字的枚举的编译输出:

```
//As we can see for number-based Enums, we could decide to leave off the initializers and the members autoincrements from Unauthorized -401 and so on -
enum StatusCodes { 
  OK = 200, 
  BadRequest = 400, 
  Unauthorized, 
  Forbidden, 
  NotFound, 
} 

//the transpiled JavaScript file is shown below (ignore the wrong status code ;)) 
var StatusCodes; 
(function (StatusCodes) { 
    StatusCodes[StatusCodes["OK"] = 200] = "OK"; 
    StatusCodes[StatusCodes["BadRequest"] = 400] = "BadRequest"; 
    StatusCodes[StatusCodes["Unauthorized"] = 401] = "Unauthorized"; 
    StatusCodes[StatusCodes["Forbidden"] = 402] = "Forbidden"; 
    StatusCodes[StatusCodes["NotFound"] = 403] = "NotFound"; 
})(StatusCodes || (StatusCodes = {})); 

```

如果我们看上面的第`13-17`行，我们会发现我们可以通过键来解析值，也可以通过值来解析键。在`line 13`、`StatusCodes["OK"] = 200 is also equal to StatusCodes[200] = "OK"`上。

不幸的是，这不适用于基于字符串的枚举。相反，在基于字符串的枚举中，我们可以将字符串值直接赋给枚举成员。让我们看看下面的传输输出:

```
enum Direction { 
  Up = "UP", 
  Down = "DOWN", 
  Left = "LEFT", 
  Right = "RIGHT", 
}  

//the transpiled .js file is shown below - 
var Direction; 
(function (Direction) { 
    Direction["Up"] = "UP"; 
    Direction["Down"] = "DOWN"; 
    Direction["Left"] = "LEFT"; 
    Direction["Right"] = "RIGHT"; 
})(Direction || (Direction = {})); 

enum Direction { 
  Up, 
  Down, 
  Left, 
  Right, 
} 

//this will show direction '1' 
console.log(Direction.Down);  

// this will show message 'Down' as string representation of enum member 
console.log(Direction[Direction.Down]); 

```

字符串枚举可以很容易地从它们的索引或者通过查看它们的第一个成员的初始化器推断出来。枚举的自动初始化值是它的属性名。要从字符串值中获取枚举成员的数值，可以使用以下方法:

```
const directionStr = "Down"; 
// this will show direction '1' 
console.log(Direction[directionStr]); 

```

我们可以使用这些值进行简单的字符串比较，例如:

```
// Sample code to check if a user presses "UP" on say a game console 
const stringEntered = "UP" 

if (stringEntered === Direction.Up){ 
    console.log('The user has pressed key UP'); 
    console.log(stringEntered); //"UP" 
}

```

正如我们前面提到的，记录数字枚举的成员并不是很有用，因为我们看到的只是数字。当使用这些枚举类型时，还存在松散类型的问题，因为静态允许的值不仅仅是枚举成员的值，任何数字都是可以接受的。很奇怪，对吧？请参见下图:

```
const enum LogLevel { 
  ERROR, 
  WARN, 
  INFO, 
  DEBUG, 
} 

function logger(log: LogLevel) { 
  return 'different error types'  
} 
console.log(logger(LogLevel.ERROR)) // "different error types"  
console.log(logger(12)) // "different error types"  

```

## 运行时和编译时枚举

### 基于常量的枚举

使用基于常量的枚举，我们可以避免 TS 编译器生成的代码，这在访问枚举值时很有用。基于常量的枚举在运行时没有表示形式。而是直接使用其成员的值。

基于常量的枚举被定义为常规枚举，除非使用了`const`关键字。唯一的区别是它们的行为和用法。让我们看下面一个例子:

```
const enum test  { 
  A, 
  B, 
} 

//usage  
function testConst(val: test) { 
   if(test.A) { 
     return "A" 
   }  
   if(test.B) { 
     return "B" 
   } else { 
     return "Undefined" 
   } 
} 

```

基于常量的枚举只能使用常量枚举表达式，并且不会在编译期间添加，这与常规枚举声明不同。Const 枚举成员也在使用站点内联。因此，这意味着 const enums 不能有计算成员。编译后，它们表示如下:

```
console.log(testConst) 

function testConst(val) { 
    if (0 /* A */) { 
        return "A"; 
    } 
    if (1 /* B */) { 
        return "B"; 
    } 
}  

```

从上面我们可以看到，枚举成员值是由 TS 内联的。此外，const enums 不允许反向查找，反向查找的行为类似于常规的 JavaScript 对象。

枚举成员也可以成为类型。这适用于某些枚举成员只有相应值的情况。我们还应该注意，为了发出映射代码而不考虑常量，我们可以在我们的`tsconfig.json`文件中打开`preserveConstEnums`编译器选项。

如果我们使用 TS 配置文件中设置的`preserveConstEnums`选项再次编译我们的代码，编译器仍然会为我们内联代码，但它也会发出映射代码，这在一段 JavaScript 代码需要访问它时变得很有用。

## 传统支持的最佳实践

在现代 TypeScript 中，有一种实现基于字符串的枚举的方法，以便它们在 JavaScript 环境中向后兼容。这样做是希望当 ECMAScript 最终在 JavaScript 中实现枚举时，我们可以利用这种语法。这就是遗留支持发挥作用的地方。

要在 TypeScript 中做到这一点，我们可以对常规对象使用`as const`方法。让我们看下面一个例子:

```
const Direction = { 
  Up: 0, 
  Down: 1, 
  Left: 2, 
  Right: 3, 
} as const; 

```

然而，要使用这种方法来获取代码中的键，我们需要使用`keyof typeof`方法。参见下面的示例。

```
type Dir = typeof Direction[keyof typeof Direction]; 

```

## 结论

字符串枚举是灵活的，如果键值相同并且我们不关心枚举的大小写，我们可以将键值作为枚举添加，或者只添加键。当在 TypeScript 中使用字符串枚举时，我们不一定需要知道每个枚举值包含的确切字符串。

同样重要的是要指出，虽然枚举可以与字符串和数字成员混合使用(异类)，[但是不清楚为什么您会想要这样做](https://www.typescriptlang.org/docs/handbook/enums.html#heterogeneous-enums)。

正如我们之前讨论的，枚举成员值可以是常量或计算值。对于常量枚举，枚举必须是第一个成员，并且没有初始值设定项。此外，它们的值可以在编译时计算。如果你感兴趣的话，TypeScript 文档包含[更多关于常量枚举和计算枚举的细节。](https://www.typescriptlang.org/docs/handbook/enums.html#computed-and-constant-members)

使用像 [TypeScript](https://blog.logrocket.com/tag/typescript) 这样的类型化语言和使用这样的特性的美妙之处在于，像 Visual Studio 代码这样的流行 ide 可以帮助我们通过自动完成从值列表中选择枚举值。当我们的任务是在一个枚举的值之间进行比较，或者甚至在它们的常规用法中进行比较时，这尤其有用。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.