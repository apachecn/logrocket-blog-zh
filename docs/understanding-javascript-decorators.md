# 理解 JavaScript decorators - LogRocket 博客

> 原文：<https://blog.logrocket.com/understanding-javascript-decorators/>

***编者按:**这篇文章于 2022 年 2 月 11 日被审查，以更新代码并包括关于巴别塔最新突破性变化的信息。*

## 介绍

根据剑桥词典的解释，装饰某物的意思是“给一个物体或地方增加一些东西，特别是为了使它更有吸引力。”

编程中的修饰就是用一段代码包装另一段代码，从而对其进行修饰。装饰器(也称为装饰函数)还可以指用另一个函数包装一个函数以扩展其功能的设计模式。

这个概念在 JavaScript 中是可能的，因为一等函数——被视为[一等公民](https://en.wikipedia.org/wiki/First-class_citizen)的 JavaScript 函数。

装饰器的概念在 JavaScript 中并不新鲜，因为高阶函数是函数装饰器的一种形式。

让我们在下一节详细阐述这一点，或者跳到下面您感兴趣的部分:

## 功能装饰者

函数装饰器是函数。它们将一个函数作为参数，并返回一个新函数，这个新函数增强了函数参数，但没有修改它。

### 高阶函数

在 JavaScript 中，高阶函数将一个一级函数作为参数和/或返回其他函数。

考虑下面的代码:

```
const logger = (message) => console.log(message)

function loggerDecorator (logger) {
    return function (message) {
        logger.call(this, message)
        console.log("message logged at:", new Date().toLocaleString())
    }
}

const decoratedLogger = loggerDecorator(logger);

```

我们通过使用`loggerDecorator`函数修饰了`logger`函数。返回的函数——现在存储在`decoratedLogger`变量中——不会修改`logger`函数。相反，返回的函数用打印消息记录时间的能力来修饰它。

考虑下面的代码:

```
logger("Lawrence logged in: logger") // returns Lawrence logged in: logger

decoratedLogger("Lawrence logged in: decoratedLogger") 
// returns:
// Lawrence logged in: decoratedLogger
// message logged at: 6/20/2021, 9:18:39 PM

```

我们看到，当调用`logger`函数时，它将消息记录到控制台。但是当调用`decoratedLogger`函数时，它会将消息和当前时间记录到控制台。

下面是函数装饰器的另一个明智的例子:

```
//ordinary multiply function
let Multiply = (...args) => {
    return args.reduce((a, b) => a * b)
}

// validated integers
const Validator = (fn) => {
  return function(...args) {
    const validArgs = args.every(arg => Number.isInteger(arg));
    if (!validArgs) {
      throw new TypeError('Argument cannot be a non-integer');
    }
    return fn(...args);
  }
}

//decorated multiply function that only multiplies integers
MultiplyValidArgs = Validator(Multiply);
MultiplyValidArgs(6, 8, 2, 10);

```

在上面的代码中，我们有一个普通的`Multiply`函数，它给出了所有参数的乘积。然而，使用我们的`Validator`函数——它是一个装饰器——我们扩展了我们的`Multiply`函数的功能来验证它的输入并且只乘整数。

## 班级装饰者

在 JavaScript 中，函数装饰器的存在是因为该语言支持高阶函数。函数装饰器中使用的模式不容易用在 JavaScript 类上。因此， [TC39 类装饰提议](https://tc39.es/proposal-decorators/)。你可以[在这里](https://tc39.es/process-document/)了解更多关于 TC39 工艺的信息。

TC39 类装饰提议旨在解决这个问题:

```
function log(fn) {
  return function() {
    console.log("Logged at: " + new Date().toLocaleString());
    return fn();
  }
}
class Person {
  constructor(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
  }
  getBio() {
    return `${this.name} is a ${this.age} years old ${this.job}`;
  }
}

// creates a new person
let man = new Person("Lawrence", 20, "developer");

// decorates the getBio method
let decoratedGetBio = log(man.getBio); 
decoratedGetBio(); // TypeError: Cannot read property 'name' of undefined at getBio

```

我们试图使用函数装饰技术来装饰`getBio`方法，但是它不起作用。我们得到一个`TypeError`,因为当在`log`函数内部调用`getBio`方法时，`this`变量将内部函数指向全局对象。

我们可以通过将`this`变量绑定到`Person`类的`man`实例来解决这个问题，如下所示:

```
// decorates the getBio method
let decoratedGetBio = log(man.getBio.bind(man));

decoratedGetBio(); // returns
// Logged at: 6/22/2021, 11:56:57 AM
// Lawrence is a 20 years old developer

```

尽管这可行，但需要一点技巧和对 JavaScript `this`变量的良好理解。因此，需要一种更干净、更容易理解的方法来使用 decorators 和类。

类装饰器——或者严格地说是装饰器——是一种扩展 JavaScript 类的提议。TC39 目前是第二阶段提案，这意味着它们有望被开发并最终包含在该语言中。

然而，随着 ES2015+的推出，随着 transpilation 变得司空见惯，我们可以通过使用[@ Babel/plugin-proposal-decorators](https://babeljs.io/docs/en/babel-plugin-proposal-decorators)在 [Babel](https://babeljs.io) 等工具的帮助下使用这一功能。

此外，重要的是要注意，阶段 2 装饰提案有一个新的和稳定的实现来处理多种约束和期望。新的语义不同于旧的提议——已经为 Babel legacy 或 TypeScript 编写的 decorators 将不能与新的提议一起工作。

根据 Babel-rewrite 文档，为了支持这个新的实现，我们需要将`"version": "2021-12"`选项传递给`@babel/plugin-proposal-decorators`，如下所示:

```
{
  "plugins": [
    ["@babel/plugin-proposal-decorators", {
        "version": "2021-12"
    }]
  ]
}

```

注意:出于开发目的，你可以使用新的[装饰者游乐场](https://javascriptdecorators.org)。

### JavaScript 装饰者 API

装饰者使用一种特殊的语法,以符号`@`为前缀，直接放在被装饰的代码上面，如下所示:

```
@log
class ExampleClass {
  doSomething() {
    //
  }
}

```

另外，类装饰器在`export`和`default`之后，可以同时装饰类声明和类表达式。

当一个装饰器被调用时，它接收两个参数:`value`和`context`。`value`参数指的是被修饰的值，但是如果它是一个类字段，并且上下文指的是包含关于被修饰的值的元数据的对象，那么它就是`undefined`。

在新的实现中，decorators 支持类以及公共、私有和静态类成员，如方法、访问器和类字段。

下面的代码显示了一个简单的实现:

```
function decorator (value, context) {
  console.log("decorated value is:", value);
  console.log("context is: ", context);
}

@decorator
class C {
  @decorator // decorates a class field
  p = 5;

  @decorator // decorates a method
  m() {}

  @decorator // decorates a getter
  get x() {}

  @decorator // decorates a setter
  set x(v) {}
}

```

让我们通过下面给出的详细例子来了解不同类型的装饰者:

#### 类成员装饰者

类成员装饰器是应用于类成员的二元函数。

第一个参数`value`，指的是我们正在装饰的类的成员属性。这使得我们可以选择返回一个新方法或者替换修饰函数的模式成为可能。

如果我们返回一个新方法，它将替换原型上的原始方法，但如果它是一个静态方法，它将替换类本身上的原始方法。然而，如果我们返回任何其他类型的值，将会抛出一个错误。

让我们通过重写我们的`log`装饰器来了解这一点:

```
function log(value, {kind, name}) {
  if (kind === "method") {
    return function(...args) {
      console.log("Logged at: " + new Date().toLocaleString());
      try {
        const result = value.apply(this, args);
        return result;
      } catch (e) {
        console.log(`Error: ${e}`);
        throw e;
      } 
    } 
  }  
}

class Person {
  constructor(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
  }

  @log
  getBio() {
    return `${this.name} is a ${this.age} years old ${this.job}`;
  }
}

// creates a new person
let man = new Person("Lawrence", 20, "developer");

man.getBio()

```

在上面的代码中，我们成功地重构了我们的`log`装饰器——从函数装饰器模式到成员类装饰器。

我们简单地用`descriptor value`访问成员类属性——在本例中是`getBio`方法——并用一个新函数替换它。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这比普通的高阶函数更简洁，也更容易重用。

#### 班级装饰者

这些装饰器应用于整个类，使我们能够装饰类。

类装饰器接收一个类作为第一个参数。它可以选择返回一个新类或替换修饰类，但是如果返回一个不可构造的值，它将抛出一个错误。

考虑下面的代码:

```
function log(value, { kind, name }) {
  if (kind === "class") {
    const newClass = class extends value {
      constructor(...args) {
        super(...args);
        console.log(`constructing a class with arguments: ${args.join(", ")}`);
      }
    }
      console.log(`An instance of the ${name} ${kind} has been created`)
      return newClass;
  }
}

@log
class Person {
  constructor(name, profession) {
  }
}
const lawrence = new Person('Lawrence Eagles', "Developer");

```

在我们设计的小例子中，`log` decorator 接收类作为第一个参数，并在创建新类时记录实例的信息。

## 为什么是装修工？

装饰者通过提供一种有效的和可理解的方式将一段代码包装成另一段代码，使我们能够编写更干净的代码。它还为应用这个包装器提供了清晰的语法。

这种语法使我们的代码不那么令人分心，因为它将功能增强代码从核心函数中分离出来。它使我们能够在不增加代码复杂性的情况下添加新功能。

此外，decorators 帮助我们将相同的功能扩展到几个函数和类，从而使我们能够编写更易于调试和维护的代码。

虽然装饰器已经作为[高阶函数](https://blog.logrocket.com/a-closer-look-at-javascript-closures-higher-order-functions-and-currying/)存在于 JavaScript 中，但是很难甚至不可能在类中实现这种技术。因此，TC39 提供的特殊语法便于与类一起使用。

## 结论

尽管 decorators 是第二阶段的提议，但由于 Angular 和 [TypeScript](https://blog.logrocket.com/a-practical-guide-to-typescript-decorators/) ，它们已经在 JavaScript 世界中流行了。

从这篇文章中，我们可以看到它们促进了代码的可重用性，从而保持了代码的干爽。

新的实现使我们能够修饰一个类和类中的一切，包括新的[类自动访问器](https://github.com/tc39/proposal-decorators#class-auto-accessors)。

当我们等待装饰器在 JavaScript 中正式可用时，您可以通过使用 Babel 开始使用它们。我相信你已经从这篇文章中学到了足够的东西，可以在你的下一个项目中给装饰者一个尝试。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.