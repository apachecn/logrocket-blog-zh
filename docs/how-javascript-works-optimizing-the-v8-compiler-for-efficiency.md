# JavaScript 如何工作:优化 V8 编译器以提高效率

> 原文：<https://blog.logrocket.com/how-javascript-works-optimizing-the-v8-compiler-for-efficiency/>

理解 Javascript 如何工作是编写高效 Javascript 的关键。

忘记微不足道的毫秒级改进:误用对象属性会导致简单的一行程序速度降低 7 倍。

鉴于 Javascript 在软件栈的所有级别中无处不在(a la [的意思是](https://en.wikipedia.org/wiki/MEAN_(software_bundle))或替代物 [1](https://reactjs.org/) 、 [2](https://www.apollographql.com/) 、 [3)，我](https://www.meteor.com/)认为严重的速度下降会困扰你的基础设施的任何级别(如果不是所有级别的话) — 不仅仅是你网站的菜单动画。

有许多方法可以编写更高效的 Javascript，但在本文中，我们将重点关注 J [avascript 优化方法](https://blog.logrocket.com/7-optimization-techniques-in-react/)，它们对编译器友好，这意味着源代码使编译器优化变得简单而有效。

我们将把讨论范围缩小到 V8——支持[电子](https://electronjs.org/)、 [Node.js、](https://nodejs.org/en/)和[谷歌 Chrome](https://www.google.com/chrome/) 的 Javascript 引擎。为了理解编译器友好的优化，我们首先需要讨论 Javascript 是如何编译的。

V8 中的 Javascript 执行分为三个阶段:

*   **源到语法树:**解析器从源生成抽象语法树([AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree)
*   **语法树到字节码:** V8 的解释器 **[点火](https://v8.dev/docs/ignition)** 从语法树生成字节码。注意，这个字节码步骤在 2017 之前是 [不存在的。此处](https://docs.google.com/presentation/d/1chhN90uB8yPaIhx_h2M3lPyxPgdPmkADqSNAoXYQiVE/edit#slide=id.g18d89eb289_1_362) 描述的是 2017 款前 V8[。](https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e)
*   **字节码到机器码:** V8 的编译器 [**涡扇**](https://v8.dev/docs/turbofan) 从字节码生成图形，用高度优化的机器码替换字节码的部分

第一阶段超出了本文的范围，但是第二和第三阶段对编写优化的 Javascript 有直接的影响。

我们将讨论这些优化方法，以及您的代码如何利用(或[误用)](https://github.com/davidmarkclements/v8-perf)这些优化。通过理解 Javascript 执行的基础，您不仅会理解这些性能建议，还会了解如何发现自己的一些建议。

实际上，第二阶段和第三阶段紧密相连。这两个阶段都在准时制(JIT)范式下运行。为了理解 JIT 的重要性，我们将考察以前将源代码翻译成机器码的方法。

### 准时制(JIT)范例

为了执行任何程序，计算机必须将源代码翻译成机器可以运行的机器语言。

有两种方法来完成这种翻译。

第一个选项包括使用一个**解释器**。解释器有效地逐行翻译和执行。

第二种方法是使用一个**编译器**。编译器在执行之前立即将所有源代码翻译成机器语言。鉴于下面描述的优点和缺点，每种方法都有它的位置。

### 解释器的利弊

解释器使用**读取-评估-打印循环** (REPL)进行操作——这种方法具有许多有利的特性:

*   易于实施和理解
*   即时反馈
*   更易修改的编程环境

然而，这些好处是以缓慢的执行为代价的，这是由于(eval 的开销，而不是运行机器码，以及(2)无法优化程序的各个部分。

更正式地说，当处理不同的代码段时，解释器不能识别重复的工作。如果您通过解释器运行同一行代码 100 次，解释器将翻译并执行同一行代码 100 次——不必要的重复翻译 99 次。

总之，解释器简单，启动快，但执行慢。

### 编译器的利与弊

相比之下，编译器在执行之前会一次翻译所有的源代码。

随着复杂性的增加，编译器可以进行全局优化(例如，为重复的代码行共享机器代码)。这为编译器提供了优于解释器的唯一优势——更快的执行时间。

本质上，编译器很复杂，启动起来很慢，但执行起来很快。

### 即时编译

一个[即时编译器](https://en.wikipedia.org/wiki/Just-in-time_compilation)试图结合解释器和编译器的最佳部分，使翻译和执行都很快。

基本思想是尽可能避免重译。首先，分析器只是通过解释器运行代码。在执行过程中，分析器跟踪运行几次的热代码段和运行很多次的热代码段。

JIT 将热代码段发送给基线编译器，尽可能重用编译后的代码。

JIT 还将热代码段发送给优化编译器。该编译器使用解释器收集的信息来(a)做出假设和(b)基于这些假设进行优化(例如，对象属性总是以特定的顺序出现)。

然而，如果这些假设无效，优化编译器会执行**去优化**，这意味着它会丢弃优化后的代码。

优化和去优化周期是昂贵的，并且产生了下面详细描述的一类 Javascript 优化方法。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

JIT 还引入了与存储优化的机器码和分析器的执行信息相关的内存开销。虽然这不能通过优化的 Javascript 来改善，但是这种内存消耗激发了 Ignition，V8 解释器。

### V8 编译

V8 的点火和涡轮风扇执行以下功能:

*   点火将 AST 翻译成字节码。然后执行字节码序列，并通过内联缓存收集反馈。这种反馈被(a)点火本身用于随后的解释和(b)涡轮风扇发动机用于推测性的优化。
*   基于反馈，涡扇通过将字节码翻译成特定于架构的机器代码来推测性地优化字节码。

### 点火

JIT 编译器会消耗大量内存。Ignition 通过实现三个目标([幻灯片)](https://docs.google.com/presentation/d/1HgDDXBYqCJNasBKBDf9szap1j4q4wnSHhOYpaNy5mHU/edit#slide=id.g18d89eb289_1_91)来解决这个问题:减少内存使用、减少启动时间和降低复杂性。

这三个目标都是通过将 AST 编译成字节码并在程序执行期间收集反馈来实现的。

*   这个字节码被用作事实的来源，消除了编译期间重新解析 Javascript 的需要。这意味着有了字节码，涡扇的去优化不再需要原始源代码。
*   作为基于程序执行反馈的优化示例，**内联缓存* *允许 V8 优化对具有相同类型参数的函数的重复调用。具体来说，内联缓存存储函数的输入类型。类型越少，需要的类型检查就越少。减少类型检查的次数可以显著提高性能。

AST 和字节码都暴露给了涡轮风扇优化编译器。

### 涡轮风扇发动机

随着 2008 年的发布，V8 引擎最初直接将源代码编译成机器码，跳过了中间字节码表示。发布时，根据谷歌伦敦主题演讲(麦克罗伊，2016 年 10 月)，V8 比竞争对手快 10 倍。

然而，今天的涡扇发动机接受 Ignition 的中间字节码，速度比 2008 年快 10 倍。同一主题演讲介绍了 V8 编译器过去的迭代及其失败之处:

*   2008 年–全代码
*   2010 年–曲轴
    *   使用类型反馈([幻灯片](https://docs.google.com/presentation/d/1HgDDXBYqCJNasBKBDf9szap1j4q4wnSHhOYpaNy5mHU/edit#slide=id.g17d335048f_1_3094))和去优化([幻灯片](https://docs.google.com/presentation/d/1HgDDXBYqCJNasBKBDf9szap1j4q4wnSHhOYpaNy5mHU/edit#slide=id.g17d335048f_1_3210))优化 JIT 编译器
    *   缺点:无法扩展到现代 Javascript，严重依赖于去优化，有限的静态类型分析，与 Codegen 紧密耦合，高移植开销
*   2015 年–涡轮风扇
    *   用类型和范围分析优化 JIT 编译器，节点的海洋

根据单独的[Google Munich technical talk(Titzer，2016 年 5 月](https://docs.google.com/presentation/d/1sOEF4MlF7LeO7uq-uThJSulJlTh--wgLeaVibsbb3tc/edit#slide=id.g5499b9c42_069))，涡扇发动机针对峰值性能、静态类型信息使用、编译器前端、中间和后端的分离以及可测试性进行了优化。振臂一呼的关键贡献，叫做一个**海**(或**汤**)的节点。

有了节点的海洋，节点代表计算，边代表依赖。

与[控制流图(CFG)](https://en.wikipedia.org/wiki/Control-flow_graph) 不同，节点的海洋放宽了大多数操作的评估顺序。像 CFG 一样，有状态操作的控制边和效果边在需要的地方约束执行顺序。

Titzer 进一步将这个定义细化为节点的集合，其中控制流子图被进一步放宽。这提供了许多优势，例如，这避免了冗余代码的消除。

通过自下而上或自上而下的图转换，图缩减被应用于这一堆节点。

涡扇管道遵循 4 个步骤将字节码翻译成机器码。请注意，以下管道中的优化是基于 Ignition 收集的反馈执行的:

*   将程序表示为 Javascript 操作符(例如 JSAdd)
*   将程序表示为中间操作符(VM 级操作符；不可知的数字表示(例如 NumberAdd)
*   将程序表示为机器操作符(对应于机器指令，例如 Int32Add)
*   使用顺序约束安排执行顺序。创建一个传统的 CFG。

涡扇发动机的在线 JIT 式编译和优化完成了 V8 从源代码到机器码的翻译。

### 如何优化你的 Javascript

TurboFan 的优化通过减轻不良 Javascript 的影响来提高 Javascript 的净性能。然而，理解这些优化可以提供进一步的加速。

这里有 7 个在 V8 中利用优化来提高性能的技巧。前四个重点是减少去优化。

**提示 1:在构造函数中声明对象属性**

更改对象属性会产生新的隐藏类。以下面这个来自 [Google I/O 2012](https://www.youtube.com/watch?v=UJPdhx5zTaw&feature=youtu.be&t=12m18s) 的例子为例。

```
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
}

var p1 = new Point(11, 22);  // hidden class Point created
var p2 = new Point(33, 44);

p1.z = 55;  // another hidden class Point created
```

如你所见，`p1`和`p2`现在有不同的隐藏类。这挫败了涡扇发动机的优化尝试:具体来说，任何接受`Point`对象的方法现在都不再优化。

所有这些函数都用两个隐藏类进行了重新优化。对物体形状的任何修改都是如此。

**提示 2:保持对象属性排序不变**

改变对象属性的顺序会产生新的隐藏类，因为顺序包含在对象形状中。

```
const a1 = { a: 1 };  # hidden class a1 created
a1.b = 3;

const a2 = { b: 3 };  # different hidden class a2 created
a2.a = 1;
```

在上面，`a1`和`a2`现在也有不同的隐藏类。固定顺序允许编译器重用相同的隐藏类，因为添加的字段(包括顺序)用于生成隐藏类的 ID。

**提示 3:修复函数参数类型**

函数根据特定参数位置的值类型更改对象形状。如果这种类型发生变化，函数将被取消优化并重新优化。

在看到四种不同的对象形状后，函数变为超对称，因此涡扇(TurboFan)不会尝试优化函数。

以下面的例子为例。

```
function add(x, y) {
  return x + y
}

add(1, 2);  # monomorphic
add("a", "b");  # polymorphic
add(true, false);
add([], []);
add({}, {});  # megamorphic
```

L9 以后涡扇不再优化`add`。

**提示 4:在脚本范围内声明类**

不要在函数范围内定义类。举下面的例子，说明这个病理情况:

```
function createPoint(x, y) {
  class Point {
    constructor(x, y) {
      this.x = x;
      this.y = y;
    }
  }
  return new Point(x, y);
}

function length(point) {
  ...
}
```

每次调用函数`createPoint`，都会创建一个新的`Point`原型。

每个新的原型对应一个新的对象形状，因此`length`函数可以看到每个新点对应一个新的对象形状。

像以前一样，在看到 4 个不同的物体形状后，函数变得巨型化，涡扇发动机不试图优化`length`。

通过将`class Point`放在脚本范围内，我们可以避免每次调用`createPoint`时创建新的对象形状。

下一个技巧是 V8 引擎的一个怪癖。

**提示 5:使用`for ... in`**

这是 V8 发动机的一个怪癖，这个特征包含在最初的曲轴中，后来[将](https://benediktmeurer.de/2017/09/07/restoring-for-in-peak-performance/)移植到点火和涡轮风扇上。

`for…in`循环比函数迭代、带箭头函数的函数迭代和 for 循环中的`Object.keys`快 4-6 倍。

由于现代 V8 的变化，以下是对不再相关的前神话的两个反驳。

**提示 6:无关字符不影响性能**

曲轴以前使用函数的字节数来决定是否内联一个函数。然而，涡扇发动机是建立在 AST 之上的，它使用 AST 节点的数量来确定功能大小。

因此，诸如空白、注释、变量名长度和函数签名等不相关的字符不会影响函数的性能。

**提示 7:尝试/捕捉/最终不是毁灭性的**

以前，Try 块容易出现代价高昂的优化-去优化周期。然而，今天的涡扇发动机在从一个`try`模块中调用一个函数时不再表现出显著的性能提升。

## 测量生产环境中的 JavaScript 和应用程序性能

虽然优化 JavaScript 是第一步，但监控应用程序的整体性能是关键。如果您有兴趣了解生产应用程序中的性能问题，请尝试 LogRocket 。[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://logrocket.com/signup/)[https://logrocket.com/signup/](https://logrocket.com/signup/)

LogRocket 就像是网络应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告性能问题以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 让你的应用程序记录带有标题+正文的请求/响应，以及关于用户的上下文信息，以全面了解问题。它还记录页面上的 HTML 和 CSS，甚至可以重建最复杂的单页面应用程序的像素级完美视频。

优先考虑性能–[开始免费监控](https://logrocket.com/signup/)。

### 结论

总之，优化方法通常集中于减少去优化和避免不可优化的巨型函数。

了解了 V8 引擎框架之后，我们还可以推导出上面没有列出的其他优化方法，并尽可能多地重用这些方法来利用内联。现在，您已经了解了 Javascript 编译及其对日常 Javascript 使用的影响。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.