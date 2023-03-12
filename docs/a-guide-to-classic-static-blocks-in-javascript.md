# JavaScript - LogRocket 博客中的经典静态块指南

> 原文：<https://blog.logrocket.com/a-guide-to-classic-static-blocks-in-javascript/>

## 介绍

JavaScript 中的`Class static blocks`允许您在评估类定义的过程中执行额外的静态初始化。然而，类静态块目前仍然包含在[第二阶段提案](https://tc39.es/process-document/)中，这并不是为了替代`[static fields](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Public_class_fields)`，而是为了提供使用`static fields`无法完成的新用例。因此，`class static blocks`让面向对象编程(OOP) JavaScript 变得有趣和强大得多。

Java 和 C#等使用`classical inheritance`的编程语言已经有了这样的实现。在 Java 中，它们是`[static initializers](https://docs.oracle.com/javase/specs/jls/se10/html/jls-8.html#jls-8.7)`，而在 C#中，它们是`[static constructors](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-constructors)`。

与这些语言不同，JavaScript 中的 OOP 使用`prototypal inheritance`。通常，这样的特征不应该出现。然而，随着[Ecmascript 2015](http://es6-features.org/#Constants)[【es6】](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)中[类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)的出现，已经出现了类似于`classical inheritance`中所见的特性的实现。有些，如`[static methods](https://developer.mozilla.org/en-US/docs/Glossary/Static_method)`、`[extends](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/extends)`，已经实施。而现在，甚至有更多的实验特性，如`[static fields](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Public_class_fields#:~:text=The%20static%20keyword%20defines%20a,to%20create%20or%20clone%20objects.)`、`[private fields](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Private_class_fields)`和`class static blocks`。

尽管 OOP JavaScript 有了这些巨大的进步，但重要的是要注意，在幕后，JavaScript 仍然使用原型继承。因此，许多这些仅仅是`[syntatic sugar](https://en.wikipedia.org/wiki/Syntactic_sugar#:~:text=In%20computer%20science%2C%20syntactic%20sugar,style%20that%20some%20may%20prefer.)`。

> 语法糖指的是一种新的、视觉上吸引人的语法(通常是一种快捷方式)来执行旧的操作。–[维基百科](https://en.wikipedia.org/wiki/Syntactic_sugar#:~:text=In%20computer%20science%2C%20syntactic%20sugar,style%20that%20some%20may%20prefer.)

让我们在下一节考虑 JavaScript 中`class static blocks`的语法和语义。

## `class static blocks`的语法和语义

#### 句法

下面是建议的语法:

```
class NewClass {
  static {
     // do something 
  }
}

```

#### 语义学

无退货声明:

```
class NewClass {
  static {
    return // syntax error
  }
}
```

一个类定义应该只有一个`static block {}`。

```
class NewClass {
  static {}
  static {} // throws and error.
}
```

一个`static block {}`创建一个新的[变量环境](https://tc39.es/proposal-class-static-block/)嵌套在类的范围内。

```
var age = 23
class NewClass {
  static {
      var age; // can still use age inside the static block 
              //because it creates as new lexical scope
      var name = "Lawrence Eagles"
  }
}

console.log(name) // reference error. 
                  // cannot access name inside the static block's lexical scope.
```

从上面的代码中，我们可以看到，尽管`var age`是在与类相同的作用域中声明的，我们仍然在`class static block`中创建了一个新的`age`变量。这是因为`class static block {}`有自己的可变环境。

然而，我们无法访问在局部范围之外的`class static block {}`中初始化的`var name`变量。

A `static block {}`不应该有装修工。你要装饰类本身，如下所示:

```
@decorator // ok
class NewClass {
  @decorator // error. not allowed
  static {
  }
}
```

求值时，`static block {}`的`this`变量指向类的`constructor function`。

你可以在这里获得更多关于它的语义[。](https://github.com/tc39/proposal-class-static-block#semantics)

## 静态块的用例

如前所述，`static blocks`不是`static fields`或`static private fields`的替代品。

然而，它们意味着支持更多的用例，如下所示:

在`class initialization`期间评估 a `statement`:

```
class NewClass {
  static square = {L: 8, B: 6};
  static y;
  static z;
  // wrong code would throw an error
  try {
      // do something here
    }catch (error) {
      // handle error here
  }
}
```

上面的代码会抛出一个错误。我们不能在类初始化期间评估那个`try…catch`语句。必须将`try…catch` `statement`移到类声明之外。

然而，如果我们需要评估一个类初始化中的语句(例如，`try..catch`)，我们可以使用如下所示的`static block`:

```
class NewClass {
  static square = {L: 8, B: 6};
  static y;
  static z;
  static {
    try {
      // do something here
    }catch (error) {
      // handle error here
    }
  }
}

```

当我们需要从一个值设置两个字段时，如下所示:

```
class NewClass {
  static square = {L: 8, B: 6};
  static y;
  static z;
  NewClass.y = square.L // throws an error
  NewClass.z = square.B // throws an error
}
```

然而，我们可以使用如下所示的`static blocks`设置这些值:

```
class NewClass {
  static square = {L: 8, B: 6};
  static y;
  static z;
  static {
    NewClass.y = square.L // correct
    NewClass.z = square.B // correct
  }
}

```

当一个带有`instance private field`的类和另一个在相同作用域中声明的类或函数之间需要共享信息时，如下所示:

```
let getName;
export class NewClass {
  #name
  constructor(devName) {
    this.#name = { data: devName };
  }
  static {
    // getName has privileged access to the private state (#name)
    getName = (obj) => obj.#name;
  }
}

export function getNameData(obj) {
  return getName(obj).data;
}
```

从上面我们可以看到，`static blocks`允许你在当前类声明的上下文中使用特权访问(`instance`或`static` ) [私有状态](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Private_class_fields)来评估语句。

尽管在`class static block {}`中对`getName`函数求值，但它仍然可以优先访问该类的名称`private state`。

你可以在这里了解更多关于`class static block {}` [的可能用法。](https://github.com/tc39/proposal-class-static-block#motivations)

## 结论

JavaScript 的开发一直在不断发展，尤其是在 OOP JavaScript 中。虽然 JavaScript 保持了它的`prototypal inheritance`实现，但是许多新的和提议的特性与那些在`classical inheritance`中看到的类似。

`Class static block {}`没什么不同。这种发展对语言是有益的，因为它现在吸引了更多的开发者，他们发现`prototypal inheritance`是采用 JavaScript 的一个障碍。

最后，`class static block {}`是对 OOP JavaScript 的一个强大补充，但它仍然是第二阶段提案的特性。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.