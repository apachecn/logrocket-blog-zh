# 打字稿装饰者实用指南

> 原文：<https://blog.logrocket.com/a-practical-guide-to-typescript-decorators/>

我们都同意 JavaScript 是一种令人惊叹的编程语言，它允许您在几乎任何平台上构建应用程序。尽管 TypeScript 也有一些缺点，但它在弥补 JavaScript 固有的一些缺陷方面做得很好。它不仅为动态语言增加了类型安全性，还带来了一些 JavaScript 中尚不存在的很酷的特性，比如 decorators。

## 什么是装修工？

尽管对于不同的编程语言，定义可能会有所不同，但是 decorators 存在的原因大体上是相同的。简而言之，装饰器是编程中的一种模式，在这种模式下，您可以包装一些东西来改变它的行为。

在 JavaScript 中，这个特性目前处于第二个阶段。它在浏览器或 Node.js 中还不可用，但是您可以使用 Babel 这样的编译器来测试它。话虽如此，它并不完全是一个全新的东西；在 JavaScript 之前，Python、Java 和 C#等几种编程语言都采用了这种模式。

尽管 JavaScript 已经提出了这个特性，但 TypeScript 的装饰特性在几个重要方面有所不同。由于 TypeScript 是一种强类型语言，您可以访问一些与您的数据类型相关联的附加信息来做一些很酷的事情，例如运行时类型断言和依赖注入。

## 入门指南

首先创建一个空白的 Node.js 项目。

```
$ mkdir typescript-decorators
$ cd typescript decorators
$ npm init -y

```

接下来，安装 TypeScript 作为开发依赖项。

```
$ npm install -D typescript @types/node

```

`@types/node`包包含 TypeScript 的 Node.js 类型定义。我们需要这个包来访问一些 Node.js 标准库。

在`package.json`文件中添加一个 npm 脚本来编译您的类型脚本代码。

```
{
  // ...
  "scripts": {
    "build": "tsc"
  }
}

```

TypeScript 已将此功能标记为实验性的。尽管如此，它足够稳定，可以在生产中使用。事实上，开源社区使用它已经有一段时间了。

要激活该功能，您需要对您的`tsconfig.json`文件进行一些调整。

```
{
  "compilerOptions": {
    "target": "ES5",
    "experimentalDecorators": true
  }
}

```

创建一个简单的 TypeScript 文件来测试它。

```
console.log("Hello, world!");

$ npm run build
$ node index.js
Hello, world!

```

您可以使用一个名为`ts-node`的包来简化编译和执行过程，而不是一遍又一遍地重复这个命令。它是一个社区包，使您能够直接运行 TypeScript 代码，而无需首先编译它。

让我们把它作为开发依赖项来安装。

```
$ npm install -D ts-node

```

接下来，向`package.json`文件添加一个`start`脚本。

```
{
  "scripts": {
    "build": "tsc",
    "start": "ts-node index.ts"
  }
}

```

只需运行`npm start`来运行您的代码。

```
$ npm start
Hello, world!

```

作为参考，我在我的 [GitHub](https://github.com/rahmanfadhil/typescript-decorators) 上发布了本文的所有源代码。您可以使用下面的命令将其克隆到您的计算机上。

```
$ git clone https://github.com/rahmanfadhil/typescript-decorators.git

```

## 装饰者的类型

在 TypeScript 中，decorators 是可以附加到类及其成员的函数，例如方法和属性。让我们看一些例子。

### 类装饰者

当您将一个函数作为装饰器附加到一个类时，您将收到作为第一个参数的类构造函数。

```
const classDecorator = (target: Function) => {
  // do something with your class
}

@classDecorator
class Rocket {}

```

如果要重写类中的属性，可以返回一个扩展其构造函数并设置属性的新类。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
const addFuelToRocket = (target: Function) => {
  return class extends target {
    fuel = 100
  }
}

@addFuelToRocket
class Rocket {}

```

现在您的`Rocket`类将拥有一个默认值为`100`的`fuel`属性。

```
const rocket = new Rocket()
console.log((rocket).fuel) // 100

```

### 方法装饰者

附加装饰器的另一个好地方是类方法。这里，您在函数中得到三个参数:`target`、`propertyKey`和`descriptor`。

```
const myDecorator = (target: Object, propertyKey: string, descriptor: PropertyDescriptor) =>  {
  // do something with your method
}

class Rocket {
  @myDecorator
  launch() {
    console.log("Launching rocket in 3... 2... 1... 🚀")
  }
}

```

第一个参数包含该方法所在的类，在本例中是`Rocket`类。第二个参数包含字符串格式的方法名，最后一个参数是属性描述符，这是一组定义属性行为的信息。这可用于观察、修改或替换方法定义。

如果您想扩展方法的功能，方法装饰器会非常有用，我们将在后面介绍。

### 物业装饰

就像方法装饰器一样，您将获得`target`和`propertyKey`参数。唯一的区别是您没有得到属性描述符。

```
const propertyDecorator = (target: Object, propertyKey: string) => {
  // do something with your property
}

```

在 TypeScript 中还有几个地方可以附加您的装饰器，但这超出了本文的范围。如果你很好奇，你可以在 [TypeScript 文档](https://paper.dropbox.com/doc/A-practical-guide-to-TypeScript-decorators--Az3eBblNHj5Doz3Z4NyijRAuAQ-7CB7irprCVgmPH1mS0mY4)中了解更多。

## TypeScript 装饰器的用例

既然我们已经介绍了什么是装饰者以及如何正确使用它们，那么让我们来看看装饰者可以帮助我们解决的一些具体问题。

### **计算** **e** 执行 **t** 输入法

假设您想估计运行一个函数需要多长时间，以此来衡量您的应用程序性能。您可以创建一个装饰器来计算方法的执行时间，并在控制台上打印出来。

```
class Rocket {
  @measure
  launch() {
    console.log("Launching in 3... 2... 1... 🚀");
  }
}

```

`Rocket`类内部有一个`launch`方法。为了测量`launch`方法的执行时间，您可以附加`measure`装饰器。

```
import { performance } from "perf_hooks";

const measure = (
  target: Object,
  propertyKey: string,
  descriptor: PropertyDescriptor
) => {
  const originalMethod = descriptor.value;

  descriptor.value = function (...args) {
    const start = performance.now();
    const result = originalMethod.apply(this, args);
    const finish = performance.now();
    console.log(`Execution time: ${finish - start} milliseconds`);
    return result;
  };

  return descriptor;
};

```

如您所见，`measure` decorator 用一个新方法替换了原来的方法，使它能够计算原来方法的执行时间并将其记录到控制台。

为了计算执行时间，我们将使用 Node.js 标准库中的[性能挂钩 API](https://nodejs.org/api/perf_hooks.html) 。

实例化一个新的`Rocket`实例并调用`launch`方法。

```
const rocket = new Rocket();
rocket.launch();

```

你会得到下面的结果。

```
Launching in 3... 2... 1... 🚀
Execution time: 1.0407989993691444 milliseconds

```

### 装饰工厂

要配置您的装饰器，使其在特定场景中表现不同，您可以使用一个称为装饰器工厂的概念。

装饰工厂是一个返回装饰者的函数。这使您能够通过在工厂中传递一些参数来定制装饰者的行为。

看看下面的例子。

```
const changeValue = (value) => (target: Object, propertyKey: string) => {
  Object.defineProperty(target, propertyKey, { value });
};

```

`changeValue`函数返回一个装饰器，它根据从工厂传递的值改变属性值。

```
class Rocket {
  @changeValue(100)
  fuel = 50
}

const rocket = new Rocket()
console.log(rocket.fuel) // 100

```

现在，如果您将装饰工厂绑定到`fuel`属性，值将是`100`。

### 自动误差防护

让我们运用所学知识来解决现实世界中的问题。

```
class Rocket {
  fuel = 50;

  launchToMars() {
    console.log("Launching to Mars in 3... 2... 1... 🚀");
  }
}

```

假设您有一个包含`launchToMars`方法的`Rocket`类。要向火星发射火箭，燃料液面必须在 100 以上。

让我们为它创建装饰器。

```
const minimumFuel = (fuel: number) => (
  target: Object,
  propertyKey: string,
  descriptor: PropertyDescriptor
) => {
  const originalMethod = descriptor.value;

  descriptor.value = function (...args) {
    if (this.fuel > fuel) {
      originalMethod.apply(this, args);
    } else {
      console.log("Not enough fuel!");
    }
  };

  return descriptor;
}; 

```

`minimumFuel`是一名工厂装潢师。它采用`fuel`参数，该参数指示发射特定火箭需要多少燃料。

为了检查燃料状况，用一个新方法包装原来的方法，就像前面的用例一样。

现在，您可以将装饰器插入到`launchToMars`方法中，并设置最低燃料水平。

```
class Rocket {
  fuel = 50;

  @minimumFuel(100)
  launchToMars() {
    console.log("Launching to Mars in 3... 2... 1... 🚀");
  }
}

```

现在，如果您调用`launchToMars`方法，它不会将火箭发射到火星，因为当前的燃料水平是 50。

```
const rocket = new Rocket()
rocket.launchToMars()

Not enough fuel!

```

这个装饰器很酷的一点是，您可以将相同的逻辑应用到不同的方法中，而无需重写整个 if-else 语句。

比方说，你想制造一种新的方法来发射火箭到月球。为此，燃油油位必须高于 25。

重复相同的代码并更改参数。

```
class Rocket {
  fuel = 50;

  @minimumFuel(100)
  launchToMars() {
    console.log("Launching to Mars in 3... 2... 1... 🚀");
  }

  @minimumFuel(25)
  launchToMoon() {
    console.log("Launching to Moon in 3... 2... 1... 🚀")
  }
}

```

现在，这种火箭可以发射到月球。

```
const rocket = new Rocket()
rocket.launchToMoon()

Launching to Moon in 3... 2... 1... 🚀

```

这种类型的装饰器对于身份验证和授权非常有用，比如检查用户是否被允许访问一些私有数据。

## 结论

的确，在某些情况下，没有必要自己制作装饰器。许多类型脚本库/框架，比如 [TypeORM](https://typeorm.io) 和 [Angular](https://angular.io/) ，已经提供了你需要的所有装饰器。但是，总是值得付出额外的努力来理解幕后发生的事情，它甚至可能启发您构建自己的类型脚本框架。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.