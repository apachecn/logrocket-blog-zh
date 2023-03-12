# 用于类型脚本运行时类型检查的方法

> 原文：<https://blog.logrocket.com/methods-for-typescript-runtime-type-checking/>

在编译时，用 TypeScript 编写的代码在执行之前会被检查是否有错误。在本文中，我们将展示运行时 TypeScript 中的类型检查。首先，我们将快速浏览一下这个广泛使用的 JavaScript 超集。

如果你使用的是 JavaScript，你就不必担心代码被破坏，因为程序直到运行时才把类型不匹配视为错误。作为动态类型检查的结果，您不必再担心将整个程序放入单一类型中。

另一方面，可伸缩性成为一个问题。当您无法控制类型时，跟踪它们就变得更加困难，代码库也失去了可维护性。

TypeScript 以一种非常巧妙的方式处理了这一点:通过使用静态类型检查来限制变量的类型。下面让我们看看关于编译时类型检查(静态类型检查)和运行时类型检查(动态类型检查)的两个常见误区:

### 误解 1:类型检查等同于类型系统

在强类型语言中，当表达式中变量的类型不匹配时，就会发生类型错误。具有高度类型安全性的类型化语言(如 TypeScript)被认为是强类型的，这可能导致显式类型错误，从而终止程序并迫使开发人员修复错误。

像 Java 这样的静态类型语言通常被认为是强类型的，这是事实，因为在变量的初始化过程中，您可以非常精确地定义数据类型。下面是一个例子:

```
String s = new String("Type checking!");

```

但是，JavaScript、Ruby 等语言。是动态类型的，也是强类型的，在变量初始化期间不需要声明类型。下面是一个例子:

```
  s = "Type checking!"

```

尽管两种语言都是严格类型化的，但类型检查在每种语言中的使用是不同的。

基于变量的值来推断变量的类型被称为[类型推断](https://blog.logrocket.com/type-flowing-rethinking-typescript-typing-system/)。一提到类型推断语言，一些程序员就错误地认为它们是弱类型的——这是不正确的。

### 误解 2:动态类型检查是针对解释型语言的

大多数动态类型的语言没有被编译；相反，它们是解释性语言。但情况并非总是如此。

静态和动态类型检查都将整个语言作为一个单独的实体。任何版本的 Java 语言本质上都是静态类型的。由于这种语言的实现规则，它与其他编译或解释语言不同。因此，在概念上，任何给定的语言都可以充当编译语言或解释语言。

用 Java 编写的代码用字节码编译，并让 JVM 执行生成的字节码的解释。但是也有 Java 的变体可以直接编译成机器码或者直接解释 Java 代码。

在 JavaScript 中，除以零会返回`infinity`而不是运行时错误，部分原因是 TypeScript 从不在运行时修改 JavaScript 代码的行为。下面的例子证明了这一点:

```
function divisionExample() {
    var x = 100 / 0;
    try {
        alert("Result is: " + x);
    } catch (e) {
        alert("Error is: " + e);
    }
}

```

这确保了从 JavaScript 移动到 TypeScript 的代码将始终正确执行，即使 TypeScript 检测到类型问题。

但是，TypeScript 中的规则和类型检查有什么关系呢？让我们来了解一下！

## TypeScript 中类型检查的需要

将代码从 JavaScript 文件移动到 TypeScript 文件可能会导致类型问题，有时是因为编码问题，有时是因为 TypeScript 过于谨慎。

您可能想知道为什么需要进一步的类型检查 TypeScript 不都是关于类型检查的吗？事实上，在 TypeScript 中检查静态类型的唯一时间是在编译期间。这是因为生成的 JavaScript 不知道您创建的任何类型。

虽然这对于内部代码类型检查非常有用，但是它不能防止您可能在外部遇到的无效输入。默认情况下， [TypeScript 不在运行时验证类型](https://github.com/Microsoft/TypeScript/wiki/TypeScript-Design-Goals)，以避免运行时开销，并作为其设计目标的一部分积极优化运行时性能。关于类型的上下文信息在编译期间被删除，因为我们编译到纯 JavaScript，它在运行时不知道类型，因为 JavaScript 中的类型检查不像其他语言中那样严格。您可以利用`typeof`操作符来检测类型，但是它有时会产生误导。

这种行为没有针对不正确或无效的输入提供任何类型的安全机制，例如来自未处理的 API 端点的输入。TypeScript 不是为了提供高级类型安全级别的输入约束而设计的。

## 运行时类型脚本类型检查的策略

那么，TypeScript 有没有办法娱乐一下呢？绝对的！

在这一节中，我们将介绍用实际的 TypeScript 类型处理不同约束的不同方法。下面是使之成为可能的支持性类型脚本机制:

*   **类型脚本类型守卫** : [类型守卫](https://blog.logrocket.com/when-to-use-never-and-unknown-in-typescript-5e4d6c5799ad/)通过条件块限制变量类型的范围，确定代码执行过程中预期的变量类型
*   **验证库**:这些库提供了现成的样板代码、方法，在某些情况下，还提供了为变量类型执行模式验证的接口
*   **Transpilation** :采用某种语言编写的代码库，转换成另一种具有相同抽象规则级别的语言
*   JSON 模式:流行的数据交换格式，用于快速、简单、轻量级和快速的数据传输以及不同实体之间的转换
*   **手动检查**:手动维护规则集，并将其应用于特定的语言来验证数据值的类型——这是一种繁琐的方式，在没有使用其他运行时类型检查方法或库的情况下单独管理规则，最终会创建一个不稳定的系统

有了这些支持机制，应该可以实现运行时类型安全。

### 使用 TypeScript 类型保护

在运行时检查特定值的类型是类型保护的主要功能。这有助于 TypeScript 编译器使用这些信息来更好地预测类型。

内置式防护罩包括`instanceof`和`typeof`。看看下面的每一个:

```
class ClassOne {
    methodOne() {
        return true;
    }
}

class ClassTwo {
    methodTwo() {
        return true;
    }
}

function testType(instance: ClassOne | ClassTwo) {
    instance.methodOne(); // compiler will throw error
    instance.methodTwo(); // compiler will throw error

    if (instance instanceof ClassOne) {
        instance.methodOne();
    } else {
        instance.methodTwo();
    }
}

```

上面的代码片段展示了类型守卫`instanceof`的例子。我为这个例子简单定义了两个类。函数`testType`检查创建的类的实例，并输出该类的相关方法的结果。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

与上述不同的是，防护类型`typeof`如下图所示:

```
function testType(data: string[] | string) {
    data.filter(() => false); // compiler will throw error
    data.split("Hello world!"); // compiler will throw error

    if (typeof data === "string") {
        return data.split("Hello world!");
    } else {
        return data.filter(() => false);
    }
}

```

代码片段使用`typeof`检查用作输入的`data`的类型。当这里的两个不同的输入被测试时，最终，条件返回一个基于类型的值。上面的代码返回输出:

```
Hello world!

```

如果我们想要维持编译时检查所提供的承诺，那么运行时检查必须至少像这样严格，这一点对您来说很重要。

### 使用验证库

`[io-ts](https://github.com/gcanti/io-ts)`是一个运行时类型检查库，它提供了执行数据验证的实用方法，并对被解析的数据进行编码/解码。`io-ts`本质上是用来精简运行时类型检查的 TypeScript 中的数据操作。

另一方面，`[ts-runtime](https://github.com/fabiandev/ts-runtime)`也是一个使用 TypeScript 编译器 API 为 TypeScript 执行运行时类型检查的库。

值得注意的是，与基于静态类型检查产生运行时类型检查的`ts-runtime`相反，`io-ts`使用相反的方法。

可以使用`io-ts`来提供运行时类型检查，这些检查在外观上与`ts-runtime`产生的检查非常相似，并且该库实际上使 TypeScript 能够自己推断相关的静态类型。

```
import x from "io-ts";

const CarType = x.type({
  model: x.string,
  company: x.string,
  seats: x.refinement(x.number, n => n >= 0, 'Positive')
})

interface Car extends t.TypeOf<typeof CarType> {}

```

上面的代码替换了下面的接口声明:

```
interface Car {
    model: string;
    company: string;
    seats: number;
}

```

这是处理接口的一个很好的方法。静态类型推断运行时类型，因为它们在代码更改时保持一致。此外，`io-ts`库支持递归类型和通用定义。

`io-ts`要求你的类型被声明为`io-ts`运行时类型，这对于类是不可能的。为了解决这个问题，你可以使用`io-ts`创建一个接口，然后要求这个类实现它。

然而，如果您向您的类添加属性，您必须改变`io-ts`类型:

```
import * as t from "io-ts";
export const Car = t.interface({
    type: t.union([t.literal("model")]),    // altered type
    company: t.string,
seats: t.number
});

export const Cars  = t.array(Car);

```

上面的接口改变输入数据的类型并返回适当的类型。

您可能在代码中提供了作为`io-ts`类型的接口，而不是标准的 TypeScript 接口。虽然简单的类型脚本接口可以在后端和前端之间共享[，`io-ts`类型使这个过程变得复杂。](https://blog.logrocket.com/make-sharing-typescript-code-types-quick-easy/)

### 蒸发

`[ts-runtime library](https://github.com/fabiandev/ts-runtime)`处理当前的类型脚本代码有点像类型保护，但是它将源代码转换成类似的代码，现在有运行时类型检查。

假设您有下面的类型脚本代码:

```
interface Car {
    model: string;
    company: string;
    seats: number;
}

const test: Car = {
    model: "X",
    company: "Tesla",
    seats: 4
}

```

使用`ts-runtime`时，代码被编译成以下版本:

```
import x from "ts-runtime/lib";

const Car = x.type(
    "Car",
    x.object(
        x.property("model", x.string()),
        x.property("company", x.string()),
        x.property("seats", x.number())
    )
);

const test = x.ref(Car).assert({
    model: "X",
    company: "Tesla",
    seats: 4
});

```

这种技术的一个缺点是无法控制类型检查发生的位置——每个类型检查在执行时都变成了运行时类型检查。

运行时类型检查通常是不必要的，因为你只需要检查程序边界的输入结构，而不是整个程序。

### 手动生成 JSON 模式

使用 JSON 模式进行运行时类型检查有几个好处:

*   有许多可用的库可以根据给定的模式验证输入
*   JSON 结构使得信息的存储和分发变得简单

JSON 模式是对从其他应用程序接收的 JSON 输入施加格式限制的一种常用方法。许多非 TypeScript 应用程序已经在使用这种方法来验证输入的数据。

下面是一个简单的 JSON 模式，用于验证本地 JavaScript 项目的传入数据:

```
{
    "$schema": "http://json-schema.org/draft-07/schema#",
"title": "demo-app",
    "description": "A demo app using JavaScript",
    "properties": {
        "id": {
                    "description": "Demo object 1",
                    "type": "integer"
            },
            "name": {
                    "description": "Name of the object",
                    "type": "string"
            },
            "amount": {
                    "type": "number",
                    "minimum": 0
            }
        },
    "required": ["id", "name", "amount"]
}

```

JSON 模式的缺点是冗长且难以手动创建。下面是一个非常基本的 JSON 模式输入的示例:

json

```
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "required": [
    "model",
    "company",
    "seats"
  ],
  "properties": {
    "model": {
      "type": "string"
    },
    "company": {
      "type": "string"
    },
    "seats": {
      "type": "integer",
      "minimum": 1
    }
  }
}

```

与原始的 TypeScript 类型一样，这个 JSON 模式要求`seats`是一个最小值为`1`的整数。这是可以的，只要我们的运行时类型检查至少和静态类型检查一样严格。

但是如果运行时类型检查不太严格，这就行不通了。例如，如果我们的 JSON 模式允许`model`是字符串以外的任何东西，那么在执行代码时，某些功能可能会抛出不匹配的类型错误。这将需要一个`try-catch`块来显示和优雅地处理错误。

```
try {
    Return response(result);    // Normal code execution
} catch (e) {
    return err(e.message());    // 
}

```

### 自动生成 JSON 模式

有些库会根据您提供的类型脚本代码为您生成 JSON 模式，比如[类型脚本-json-schema](https://github.com/YousefED/typescript-json-schema) 。它可以以编程方式使用，也可以直接从命令提示符使用。

该库旨在与现有代码结合使用，这些代码提供了将为其生成 JSON 模式的类型。但是，对代码的更改需要反映在新的 JSON 模式中，这意味着每次进行更改时都必须这样做，以便生成更新的模式。这是重复的，但是如果没有更新的模式，运行时类型检查将无法正确执行。

作为替代，有一些工具可以从您提供的 JSON 输入中自动推断出 JSON 模式。当然，这并没有利用您已经在 TypeScript 代码中建立的类型信息，如果您提供给工具的 JSON 输入与实际的 TypeScript 类型定义不匹配，这可能会导致问题。

```
{
    "$schema": "http://json-schema.org/draft-07/schema#",
"title": "demo-app",
“type”: “object”,
“properties”: {
    “id”: {
        “type”: “string”,
        “description”: “ID of the user”
    },
    “name”: {
        “type”: “string”,
        “description”: “Name of the user”
    },
    “birthdate”: {
        “type”: “string”,
        “description”: “Birth date of the user”
    }
},
“additionalProperties”: false,
“required”: [
    “id”,
    “name”,
    “birthdate”
]
}
```

上述示例将生成以下输出:

```
export interface DemoApp {
    id: string;
    name: string;
    birthdate: string
}

```

假设，如果`birthdate`的输入变量值是`DateFormat**,**` 比如`2022-01-01`而不是一个字符串，或者除了期望的字符串输入之外的任何东西。然后，JSON 模式的输入映射将失败，代码将抛出一个错误。

### 执行手动检查

显然，在这种情况下，最直接的方法是手动构建一个代码库，检查每个输入元素是否存在必要的属性，并确保它们是正确的类型。

另一方面，编写这样的代码可能很耗时，并且像我们手写的任何东西一样容易出错。随着对代码库的更改，错误检查代码也有可能与静态类型不同步。

例如，假设一个由三名开发人员组成的团队正在处理一个项目，并在代码中执行手动检查以确保运行时类型安全。最初，开发人员可能会就所开发的检查达成一致，但是随着代码库的扩展和更多功能的引入，每个开发人员都将负责更新一组功能，这些功能将再次依赖于项目的其余功能。

如果开发人员 A 编写检查以保持功能或业务逻辑在手边，可能会出现严重的问题，因为应用程序的其余部分可能无法部分或完全通过这些检查。开发人员 B 和 C 可能最终会开发他们自己的检查，认为他们正在修复一个问题，但却使代码库变得更加复杂和冗余。

这会导致很多事情——代码变得陈旧，偏离项目目标，花费更多的时间和资源来克服同步性的挑战。您还可能会遇到维护或实现整个应用程序的进一步通用检查的问题，这些检查将确保代码的质量和无错性。

如果没有很好地维护，这些检查可能会变得过时，导致应用程序中的重大更改、延迟和问题。

## 结论

在本文中，我们介绍了 TypeScript 中运行时类型检查的四种方法。一种策略可能不适合所有的挑战，因此您应该仔细权衡不同用例的选项。

您可以在官方文档中了解更多关于 TypeScript 中的[高级类型](https://www.typescriptlang.org/docs/handbook/2/types-from-types.html)的信息，因为这是一个基于类型的强大概念。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.