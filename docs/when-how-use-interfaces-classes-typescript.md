# 何时以及如何在 TypeScript 中使用接口和类

> 原文：<https://blog.logrocket.com/when-how-use-interfaces-classes-typescript/>

接口和类是面向对象编程(OOP)的基础部分。 [TypeScript](https://blog.logrocket.com/whats-new-in-typescript-4-2/) 是一种面向对象的 JavaScript 语言，从 ES6 和更高版本开始，它支持 OOP 特性，如接口、类和封装。

但是什么时候我们应该同时使用接口、类或者两者呢？如果你是一个新手或者对使用接口和类感到困惑，这篇文章适合你。

在本文中，我将向您展示什么是接口和类，以及何时在 TypeScript 中使用它们中的一个或两个。

## 什么是 TypeScript 中的类？

在开始之前，我们需要知道什么是 TypeScript 类。在面向对象编程中，类是一个蓝图或模板，通过它我们可以创建具有特定属性和方法的对象。

Typescript 为类型检查提供了额外的语法，并将代码转换为可以在任何平台和浏览器上运行的干净的 JavaScript。代码的所有阶段都涉及到类。将类型脚本代码转换成 JavaScript 文件后，您可以在最终文件中找到它们。

类定义了对象的模板，或者它是什么，它做什么。让我们用属性和方法创建一个简单的类，这样我们可以看到它将如何表现。

首先，我将通过下面几行代码创建一个`Developer`类:

```
class Developer {
  name?: string; // string or undefined
  position?: string; // string or undefined
}

```

我们用属性`name`和`position`来描述这个类。它们包含像`string`和`undefined`这样的类型。

接下来，让我们使用`new`关键字通过`Developer`类创建一个对象:

```
const developer = new Developer();
developer.name // it outputs undefined
developer.position // it outputs undefined

```

当我们调用`developer.name`时，它返回`undefined`，因为我们没有赋值初始值。为了用 TypeScript 中的值创建一个对象，我们可以使用 [`constructor`](https://en.wikipedia.org/wiki/Constructor_(object-oriented_programming)) 方法。构造函数方法用于初始化和创建对象。

现在我们用下面的代码更新我们的`Developer`类:

```
class Developer {
  name: string; // only string
  position: string; // only string

  constructor(name: string, position: string) {
    this.name = name;
    this.position = position;
  }
}

```

在上面的代码中，我们添加了`constructor`方法来用值初始化属性。

现在，我们可以使用以下代码将`name`设置为`Gapur`并将`position`设置为`Frontend Developer`:

```
const developer = new Developer("Gapur", "Frontend Developer");
developer.name // it outputs Gapur
developer.position // it outputs Frontend Developer

```

最后，正如我前面提到的，这个类有对象应该如何操作的方法。在这种情况下，任何开发者开发应用程序，因此，`Developer`类有方法`develop`。

因此，`developer`对象可以执行开发动作:

```
class Developer {
  name: string;
  position: string;

  constructor(name: string, position: string) {
    this.name = name;
    this.position = position;
  }

  develop(): void {
    console.log('develop an app');
  }
}

```

如果我们运行`develop`方法，它将执行下面的`console.log`语句:

```
developer.develop() // it outputs develop an app

```

## 什么是 TypeScript 中的接口？

接口是一种结构，它的作用就像应用程序中的一个契约，或者是类要遵循的语法。该接口也被称为鸭打印，或子类型。

该接口包括一个`only`方法和没有实现的字段声明。我们不能用它来创造任何东西。实现接口的类必须具有所有字段和方法。因此，我们使用它们进行类型检查。

当 TypeScript 将所有代码转换为 JavaScript 时，接口将从 JavaScript 文件中消失。因此，它在开发阶段是一个有用的工具。

我们应该使用以下接口:

*   验证属性的特定结构
*   对象作为参数
*   从函数返回的对象

现在，让我们通过下面几行代码来声明该接口:

```
interface InterfaceName {
  // variables;
  // methods;
}

```

我们只能在接口体中包含变量和方法的声明。让我们为前面的`Developer`类创建一个`IDeveloper`接口:

```
interface IDeveloper {
  name: string
  position: string
  develop: () => void
}

class Developer implements IDeveloper {
  name: string;
  position: string;

  constructor(name: string, position: string) {
    this.name = name;
    this.position = position;
  }

  develop(): void {
    console.log('develop an app');
  }
}

```

在上面的代码中，我们的`IDeveloper`接口包含变量`name`和`position`。也包括`develop`法。所以`Developer`类实现了`IDeveloper`接口。因此，它必须定义两个变量和一个方法。

如果`Developer`类没有实现任何变量，TypeScript 将显示一个错误:

```
class Developer implements IDeveloper {
  // error Class 'Developer' incorrectly implements interface 'IDeveloper'.
  name: string;

  constructor(name: string, position: string) {
    this.name = name;
    this.position = position;
  }

  develop(): void {
    console.log('develop an app');
  }
}

```

## 接口与类

那么什么时候应该使用类，什么时候应该使用接口呢？

在我们开始之前，我想与你分享强大的 TypeScript `static`属性，它允许我们使用类的字段和方法，而无需创建类的实例。

我将使用前面的`Developer`类创建一个静态方法的类:

```
class Developer {
  static develop(app: { name: string, type: string }) {
    return { name: app.name, type: app.type };
  }
}

```

现在，我们可以只调用`Developer.develop()`方法而不实例化该类:

```
Developer.develop({ name: 'whatsapp', type: 'mobile' })
// outputs: { "name": "whatsapp", "type": "mobile" } 

```

太好了！

此外，我们可以在 TypeScript 中使用类进行类型检查。让我们使用下面的代码创建一个`App`类:

```
class App {
  name: string;
  type: string;

  constructor(name: string, type: string) {
    this.name = name;
    this.type = type;
  }
}

```

让我们修改我们的`Developer`类:

```
class Developer {
  static develop(app: App) {
    return { name: app.name, type: app.type }; // output the same
  }
}

```

现在我将创建一个`App`实例，并用一个参数对象调用`Developer.develop()`:

```
const app = new App('whatsapp', 'mobile');
Developer.develop(app);
// outputs the same: { "name": "whatsapp", "type": "mobile" } 

```

`Developer.develop(app)`和`Developer.develop({ name: 'whatsapp', type: 'mobile' })`输出相同的内容。这太棒了，但是第二种方法可读性更好，也更灵活。

另外，我们可以检查参数的类型。不幸的是，要做到这一点，我们需要创建一个对象。那么如何才能改善呢？这就是接口的用武之地！

首先，我将用下面的代码将`App`类改为一个接口:

```
interface App {
  name: string
  type: string
}

class Developer {
  static develop(app: App) {
    return { name: app.name, type: app.type }; // output the same
  }
}

```

在上面的代码中，我们没有改变`Developer`类的主体，也没有创建`App`的实例，但是结果是一样的。在这种情况下，我们节省了大量的时间和代码输入。

## 结论

我们什么时候应该使用类和接口？如果要创建和传递类型检查的类对象，应该使用 TypeScript 类。如果您需要在不创建对象的情况下工作，那么接口最适合您。

最终，我们打开了两个有用的途径:蓝图和合同。你可以两个一起用，也可以只用一个。由你来决定。

感谢阅读——我希望这篇文章对你有用。编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.