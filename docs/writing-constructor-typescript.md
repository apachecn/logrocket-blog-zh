# 在 TypeScript 中编写构造函数

> 原文：<https://blog.logrocket.com/writing-constructor-typescript/>

任何成熟的 TypeScript 代码库通常都会大量使用接口。

毕竟，它们是在您的代码上添加静态编译时检查的构建块，并且它们确保您明智地使用您在代码中定义的集合/自定义类型。

接口语法很简单，当在您的代码中使用时，接口提供了许多优势，例如:

*   生成简单易懂的错误消息
*   有时编译比`type`定义更快
*   被 TypeScript 社区大量使用，因此它们是一种常见的最佳实践，(TypeScript 文档也大量使用它们)
*   类型脚本团队也支持接口。TypeScript 的项目经理 Daniel Rosenwasser 已经认可了接口

让我们了解更多关于构造函数的知识，以及我们将如何在接口中使用构造函数:

## 构造器

构造函数也是 TypeScript 代码库中大量使用的代码特性。

TypeScript 文档有一个关于构造函数用法的很好的例子:

```
class Greeter {
  greeting: string;

  constructor(message: string) {
    this.greeting = message;
  }

  greet() {
    return "Hello, " + this.greeting;
  }
}

let greeter = new Greeter("world");

```

构造函数本质上允许从类创建对象。

类的作用就像一个蓝图，用必要的属性和方法键入您创建的对象。

## 构造函数依赖注入

构造函数经常使用一种叫做[依赖注入](https://blog.logrocket.com/roll-your-own-dependency-injection/)的代码技术——这是充分利用它们潜力的关键。

这是我们将要创建的对象所需的依赖项被传递到构造函数中的地方。

在上面的例子中，我们看到我们将`message`参数传入构造函数，以允许对对象进行独特的定制:

```
let computer = new Greeter("world");         // Hello world
let farewell = new Greeter("and goodbye!");  // Hello and goodbye!

```

同一个类(`Greeter`类)从一个方法调用中提供不同结果的能力通常被称为[多态性](https://en.wikipedia.org/wiki/Polymorphism_(computer_science))。

## 多个构造函数

使用构造函数时要记住的最后一件重要事情是，不能像在其他面向对象语言中那样使用多个构造函数实现。

多个构造函数的[示例如下:](https://www.typescriptlang.org/play?#code/MYGwhgzhAECCB2BLAtmE0DeBYAUL60wA9vBAC4BOArsGURQBQCU2eOBAvrvoSedbXrNWPLjg5A)

```
class Animal {

  constructor() {
  }

  constructor() {
  }
}

```

上面的代码不会编译并记录错误`Multiple constructor implementations are not allowed`。

如果您需要使用多个构造函数从基类提供不同的功能，有很多方法可以做到这一点，但是您只能使用一个实现。

如果你需要不同的构造函数——有办法解决这个问题，你可以输入多个构造函数——你只是不能实现它们。

一个真实的例子应该是这样的:

```
class DemoClassTest {
  constructor(x : string, y:string);
  constructor(x : number);
  constructor(x : number, y:string, z:string);
  constructor(...myarray: any[]) {

    if (myarray.length === 2) {
      console.log('two argument constructor called here !!');
      return;
    }
    if (myarray.length === 3) {
      console.log('three argument constructor called here !!');
      return;
    }
    if (myarray.length === 1) {
        console.log('one argument constructor called here !!');
        return;
      }
  }
}
let a = new DemoClassTest('hello', 'bye');
let b = new DemoClassTest(1);
let c = new DemoClassTest(100, 'str1', 'str2');

```

## 在接口上使用构造函数

我们已经讨论了利用构造函数的更常见的用例，但是它们的功能并没有到此为止。

有时，作为设计模式的一部分或者对于某些用例，开发人员可能希望从一个接口专门创建一个实例变量。

我们可能想要构建的接口的一个简单示例是:

```
interface Animal {
  numLegs: number,
  wings: boolean
}

```

但是我们如何给这个类型添加一个构造函数还不清楚。

更令人困惑的是，在编译后的 JavaScript 中，接口甚至不会存在。它的存在只是为了检查我们的类型，然后将被完全删除，这要感谢一个叫做类型擦除的过程。

所以，让我们从一个[失败的例子](https://www.typescriptlang.org/play?ssl=13&ssc=2&pln=1&pc=1#code/JYOwLgpgTgZghgYwgAgJLmvJB1YYAWAwgPYgDOYArlAmMVMgN4CwAUMh8gqTMAOYAuZBSig+AbjacupEZVr0AFNxC9BwsKJB8AlEMYzV-ISLHIAvpNbm2bBABs4ZMshJG+hR8+TAAtgAd7CF8IcBd0SFhECFwCNwpqBQYWdmQpZH9KACN7YARDNWQAXmQAclKrdJU5JOUeYw0tXSZ0jgJgMgA6FUKSnv4rDhtrIA)开始，迭代求解:

```
interface InterfaceWithConsturctor {
  config: string;
  constructor(config: string): { config: string };
}

class ConfigClass implements InterfaceWithConsturctor {

  public config = '';

  constructor(config: string) {
    this.config = config;
  }
}

```

我们目前面临的错误是:

```
- Class 'ConfigClass' incorrectly implements interface 'InterfaceWithConsturctor'.
- Types of property 'constructor' are incompatible.
- Type 'Function' is not assignable to type '(config: string) => { config: string; }'.
- Type 'Function' provides no match for the signature '(config: string): { config: string; }'.

```

即使我们的两个构造函数匹配(在接口中，而不是在实现接口的类中)，它也会抛出一个错误，并且不会编译。

您可以在这两个代码示例中看到，它们使用的是相同的类型，而且从表面上看，应该可以很好地编译。

## 向 TypeScript 接口添加构造函数

[文档](https://www.typescriptlang.org/docs/handbook/interfaces.html#difference-between-the-static-and-instance-sides-of-classes)包含了一个涵盖这一确切场景的示例。

我们之前的例子失败了，因为根据文档，“当一个类实现一个接口时，只检查该类的实例端。因为构造函数位于静态端，所以不包括在此检查中。

这听起来很奇怪，但它本质上意味着[构造函数不是实例类型方法](https://www.typescriptlang.org/docs/handbook/interfaces.html#difference-between-the-static-and-instance-sides-of-classes)。

对于实例类型方法，我们指的是一个“普通的”函数，它将被对象实例上存在的`obj.funcCall()`调用，这是使用`new`关键字的结果。构造函数实际上属于静态类型。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在这种情况下，静态类型是指它所属的类型，没有实例化，例如`InterfaceWithConsturctor`。

为了解决这个问题，我们需要创建两个接口:一个用于静态类型方法/属性，一个用于实例类型方法。

我们的[新工作](https://www.typescriptlang.org/play?ssl=7&ssc=2&pln=1&pc=1#code/JYOwLgpgTgZghgYwgAgMIHsQ2AcwJLjTxLIDeAsAFDLIKbY4BcyAzmFKDgNxUC+VVUJFiIUBYcQgB1YGAAWGEGwCuUBGHRQyVGiAgB3ABQhmbDiBwBKZqVr1cp9p2S8elfpSoIANnBYs0exxkYABbAAdvCFCIcADFBnEiUW1PamRw5QAjb2AEOywHVicLNx0Cs2V1TWRDOkKmYvMrVJoaeWAWADp6hmQAXgKGNxoPDyoYZRB1YExWCDAAFTkIAGUwOEhjZiSRJBl5RRU1DShLVuQoBdUQZD19O8MAcls2TYgbWlyEAGsIABNmOxlBAADQZVQIOR+AF4SChFhAqAglwuJ6WNzjSj1FjoKJdbzoHCGFgLZZrDZbBK4Sw9IIYoA)例子，[受](https://stackoverflow.com/questions/13407036/how-does-interfaces-with-construct-signatures-work)[打字稿](https://twitter.com/searyanc)工程负责人启发，看起来是这样的。

```
interface ConfigInterface {
  config: string;
}
interface InterfaceWithConsturctor {
  new(n: string): { config: string };
}
class Config implements ConfigInterface {
  public config: string;
  constructor (config: string) {
    this.config = config;
  }
}
function setTheState(n: InterfaceWithConsturctor) {
  return new n('{ state: { clicked: true, purchasedItems: true } }');
}
console.log(setTheState(Config).config);

```

这现在记录为`{ state: { clicked: true, purchasedItems: true } }`。

## 使用 TypeScript 接口构造函数的好处

通过使用这种语言特性，您可以创建更多不依赖于继承来共享代码的可组合对象。

通过接口上的构造函数，您可以指定所有类型都必须具有某些方法/属性(符合正常的接口规范),还可以通过键入接口来控制如何构造类型，就像您对任何其他方法/属性一样。

我们依赖抽象而不是具体。有一个[的例子](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgMIBsD2CDWrMgDOYUArgmJlMgN4BQyyIEA7sgBQAWmpUAXE1IBbAEbQANMiGhSkASGFioASgEZsOAJLho8JAG46AXzp1QkWIhTrc2i3pT1GYYLnarkAN0zAAJoZM6GFIQCmACZAQoCDhIGxx2BkjKfjQsXHwiEnIU8STuXnlFCSTpBTlBUWg6D3i7XStaJOiwXhAmVmSqLh4oSTLZCGUA0wR0OEJCZAARYABzYDA4dHjkYCEAB3QIIQhwKbqdSyQmxgQCYjIKbs4iqr6pO6VlWhNnVwSXp0ZIi8xtgB0WDm7AARGIIBtkBCNqDhkkTIExhMpgBBEDLTBzVbrLY7PZgA7pLRHBynX5ZK4pLhPCSPSrPV5JFxuL5JM5-QHAsEsnDISi4OGGRiI0zbMDIXzzRbLZAAXki0ViEHi7FmCyWK2JkgAjAAmXUAdnh4uQcAxwPlipicWJ7HRmOx2uQhskAGY9cMgA)来自旧的[打字稿文档](https://www.typescriptlang.org/docs/handbook/interfaces.html#difference-between-the-static-and-instance-sides-of-classes)来强调这一点。

旧文档仍然是有效的类型脚本，它们是我们正在讨论的内容的一个非常清楚的例子——所以为了清楚起见，我保留了传统的 URL。

```
//
// Instance and static interfaces
//
interface ClockConstructor {
  new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface {
  tick(): void;
}

function createClock(
  ctor: ClockConstructor,
  hour: number,
  minute: number
): ClockInterface {
  return new ctor(hour, minute);
}

//
// Clock implementation classes
//
class DigitalClock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log("beep beep");
  }
}
class AnalogClock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log("tick tock");
  }
}

// Now we allow relatively generic (but typed!) creation of Clock classes
let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);

```

在这里，我们用我们需要其他类使用的参数创建了一个严格类型的构造函数，但同时，允许它足够通用以适应多种用例。

这也确保了我们在代码中保持低耦合、高内聚。

## 结论

我希望这不仅解释了如何在一个接口上添加一个构造函数，还解释了何时和为什么要添加构造函数的一些常见用例，以及如何实现它的语法。

这种情况很常见，文档甚至解释了基本方法，理解底层 JavaScript/TypeScript 世界中静态和实例范围的两面性是很有用的。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.