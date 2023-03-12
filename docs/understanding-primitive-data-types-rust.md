# 理解 Rust 中的原始数据类型

> 原文：<https://blog.logrocket.com/understanding-primitive-data-types-rust/>

Rust 有一个称为原语的数据类型列表。在本文中，我们将讨论 Rust 中原始数据类型的列表——我们将把它分为标量和复合类型——以及 Rust 中原始数据类型的局限性。

我们将涵盖:

本文旨在帮助那些刚刚开始使用 Rust 并希望快速理解和能够使用 Rust 中的原始数据类型的人。

## 什么是原始数据类型？

顾名思义，原始数据类型基本上就是编程语言自带的数据类型。它们是内置的，当组合在一起时，可以形成更复杂的数据类型，这些数据类型被称为非原始数据类型。

正如我们提到的，Rust 编程语言附带了一系列内置的原始数据类型，开发人员可以将它们用作其他数据类型的构建块。

## Rust 程序设计语言综述

Rust 将自己描述为一种系统编程语言，运行速度快得惊人，可以防止所有崩溃，并消除数据竞争。它在内存效率方面很好，因为它没有运行时或垃圾收集器。由于这个原因和许多其他原因， [Rust 是一种流行且深受喜爱的编程语言](https://blog.logrocket.com/why-is-rust-popular/)。

目前，这种编程语言及其社区还相对年轻。这意味着有持续的开发来添加、改进和稳定各种特性、方法和特征实现。

通过简单的[命令行界面(CLI)](https://rust-cli.github.io/book/index.html) 、 [WebAssembly (Wasm)](https://blog.logrocket.com/getting-started-with-webassembly-and-rust/) 、联网、嵌入式，就可以很舒服地使用 Rust。

人们大多喜欢谈论 Rust 是否值得学习或者是否有一个陡峭的学习曲线。但最终，这是由每个人来决定是否值得为你的个人需求而学习。

当然，让 Rust 与众不同的是，还有一些最值得注意的事情:

*   这是一种设计良好的语言
*   与其他语言相比，它注重正确性和安全性
*   它具有良好的并发性和速度
*   它有一个非常强大的社区

Rust 由 Graydon Hoare 设计，于 2010 年 7 月 7 日首次亮相。截至本文发布时，这种编程语言是 2022 年 8 月 11 日发布的版本 1.63.0 。

## Rust 中的原始数据类型

让我们看看 Rust 提供的原始数据类型。

我们想首先将它们分为标量和复合数据类型。这两者的区别在于，复合类型在一个类型中包含多个值，而标量类型只包含一个值。

### Rust 中的标量原始类型

Rust 中有五种您应该熟悉的标量原语类型:

让我们看看每种类型的定义和例子。

#### `bool`数据类型

布尔数据类型可以分为真或假，如下所示:

```
let active:bool = true;
let inactive:bool = false;

```

如上所示，确保在声明布尔变量时使用了`bool`关键字。

布尔数据类型主要用于比较值或逻辑，例如，检查测试分数是 A、B 还是 c。

#### `char`数据类型

字符类型是 4 字节数据类型。它用于存储单个字符，例如:

```
let first:char = 'a';
let second:char = 'b';
let symbol:char = '∞';

```

字符数据类型用于存储单个字符，允许 Rust 中的[内存分配保持较小。](https://blog.logrocket.com/guide-using-arenas-rust/)

#### `integer`数据类型

整数数据类型有很多种，分为两类:有符号的(I)和无符号的(u)。它们包括:`i8`、`i16`、`i32`、`i64`、`isize`、`u8`、`u16`、`u32`、`u64`、`usize`。以下是一些例子:

```
let height:u8 = 172; //u8
let weight:u8 = 68; // u8 
let size:u8 = 23; // u8
let data:i8 = -128 // i8

```

#### `floating`数据类型

浮点数据类型总是`f32`或`f64`，其范围可以从负数到正数:

```
f32 ---> -3.8x10^8 to +3.8x10^8
f64 ---> -1.8x10^308 to +1.8x10^308 

```

浮点数就是我们所说的小数。参见下面的一些例子:

```
let interest:f32 = 1.20;
let returns:f32 = 2.80;
let agency:f64 = 10.0;

```

#### `unit`数据类型

在 Rust 中，单元数据类型使用符号`()`，它主要用作避免使用`null`的机制。

任何不返回任何内容的表达式实际上都返回 Rust 中的`()`。它更像是类 C 语言中的`void`。

另一个用例类似于`Response<(), String>`，这意味着响应可能失败，也可能成功。

### Rust 中的复合原始类型

下面是 Rust 中的四种复合原始数据类型，我们将在下面介绍:

正如我们在上一节中所做的那样，让我们看看每种类型的定义和示例。

#### `array`数据类型

数组是包含一组元素的数据类型。它的大小总是固定的，并且是相同的数据类型，就像这样:

```
let counts: [i32; 7] = [4, 2, 4, 8, 3, 2, 4, 8];
let grade: [i32; 4] = [20, 40, 34, 70];

```

在上面的例子中，`counts`数组包含数据类型`i32`(整数)的`7`元素，而`grade`数组包含数据类型`i32`的`4`元素。

#### `string`数据类型

Rust 中有[两种](https://blog.logrocket.com/understanding-rust-string-str/) `[string](https://blog.logrocket.com/understanding-rust-string-str/)` [数据类型:`String`(字符串对象)和`&str`(字符串文字)。](https://blog.logrocket.com/understanding-rust-string-str/)

`String`对象不在核心语言中，而是在标准库中提供。它也是最常见的字符串类型，因为它是可变的。要创建一个`String`:

```
String::new();

let name = String::new();
name.push_str = 'Victor Jonah';
println("{}", name);

```

Rust 中的`&str`数据类型被认为是 string slice，据说是不可变的，这意味着它们在程序的生命周期中不能被改变。看看下面的例子:

```
let name:&str = 'Victor Jonah';
let company:&str = 'LogRocket';

```

在上面的例子中，在程序的生命周期中，`name`将总是与字符串`Victor Jonah`相关联，而`company`将总是与字符串`LogRocket`相关联。

#### `slice`数据类型

切片类似于数组，但也有一些不同。

虽然数组大小是固定的，但切片的大小是动态的；在编译时长度是未知的，数据被分割到一个新的集合中。请参见下面的示例:

```
let grades: [132:6] = [20, 10, 30, 40, 50, 10, 20];
let slice = &[20...4]; // 20, 10, 30, 40

```

切片也是一个指向上面的字符串对象的指针，在这里我们可以检索字符串值中的某个字符。我们也可以借用切片中的元素在其他地方使用。

#### `tuple`数据类型

在 JavaScript 等其他语言中，元组被称为对象。它们是包含不同类型元素的固定数据类型，而数组只能包含相同类型的元素。

```
let employee: (&str, i32, &str) = ('Victor Jonah', 25, 'Technical Writer');

```

在上面的例子中，tuple `employee`包含三个元素:一个字符串(`Victor Jonah`)、一个整数(`25`)和另一个字符串(`Technical Writer`)。

## 铁锈的局限性及其原始类型

顺便提一下，讨论 Rust 编程语言的[限制是很重要的。大多数人都说或声称 Rust 非常可爱——这是真的——但是有几点需要考虑。](https://blog.logrocket.com/what-you-cant-do-in-rust-and-what-to-do-instead/)

首先要注意的是，它的学习曲线很陡；Rust 作为一门语言需要更多的时间去学习，因为它是一门系统编程语言，具有高级编程概念。

当涉及到信任原始数据类型并将它们组合在一起时，有很多东西需要学习，比如模式匹配、指针、字符串文字、三种类型的数组等等。然而，这是值得你花时间的。

根据我的观察，陡峭的学习曲线主要是由于在使用 Rust 的早期，Rust 文档缺乏清晰度。

这让我想到了第二点:Rust 社区可能在开始时不太引人注目，但是当你接触到它时，它是欢迎的、活跃的和有帮助的。

还有一点需要注意的是，Rust 是一种静态编程语言，它非常严格，以至于在编译之前所有的东西都要声明。这是 Rust 的主要原则之一，它强制要求在编译时检查一切。

这可能会减慢开发速度，但也是有好处的，因为当大多数事情都在编译时检查时，程序在运行时失败的可能性就更小了。

## 结论

Rust 原始数据类型是内置的，它们的用例正是典型编程语言所需要的。这些数据类型有两种形式:标量和复合。

了解和理解 Rust 中所有不同的原始数据类型对您的 Rust 之旅非常有帮助。为此，我写了这篇短文。感谢您的阅读。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。