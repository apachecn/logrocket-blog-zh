# TypeScript 设计模式如何帮助您编写更好的代码

> 原文：<https://blog.logrocket.com/how-typescript-design-patterns-help-you-write-better-code/>

TypeScript 是一种语言，在现代软件工程领域已经有了很多应用。由于 TypeScript 的前身 JavaScript 中缺乏强类型，其强大、严格的类型系统降低了运行时出错或生产中隐藏错误的可能性。

虽然 TypeScript 本身使开发体验更加顺畅，但了解设计模式如何帮助保持或提高代码库的效率和有用性也很重要。在正确的情况下使用正确的设计模式，并知道如何这样做，是任何开发人员工具箱中的一项关键技能。

## 先决条件和结果

实现语言是 TypeScript，但是设计模式是语言不可知的，所以它们背后的概念和逻辑是最重要的。要跟进，您应该:

*   JavaScript 和 TypeScript 方面的深厚背景
*   对数据类型的良好理解

阅读完本教程后，您应该:

*   理解设计模式在现代代码库中的重要性
*   知道如何在 TypeScript 中实现观察者、构建者和原型设计模式
*   理解每个设计模式背后的概念，并能够用任何语言实现它们

## 观察者模式:我知道你发生了什么

观察者设计模式创建一个对象订阅的系统。当他们观察的对象发生变化时，所有订阅者都会立即得到通知。这避免了在特定对象发生变化时通知所有对象的必要性。

### 概念

观察者设计模式包括两组:一组订阅变更(观察者)，另一组被观察的变更(对象)。对象必须有一个数组或列表来保存观察点。这个列表或数组通过两种方法来添加或删除一个观察者。还必须有一个方法在对象改变时通知所有的观察者。

### 履行

想象一种情况，其中多个患者在医院进行 nCovid-19 病毒测试。他们现在正在等待测试结果。为了在结果准备好的时候通知他们，您可以使用观察者设计模式。

创建观察器的接口来定义其结构和类型。每个观察者都有一个在通知过程中调用的方法`sendMessage()`。它不返回任何内容。

```
interface IObserver {
    sendMessage(): void
}

```

`Hospital`是对象。它包含一个观察器数组、两个操作数组的方法和一个通知所有观察器的方法。

```
class Hospital {
    private observers: IObserver[];

    constructor() {
        this.observers = [];
    }

    addObserver(observer: IObserver) {
        this.observers.push(observer);
        console.log('Observer added!');
    }

    removeObserver(observer: IObserver) {
        const obsIndex = this.observers.indexOf(observer);
        if (obsIndex < 0) {
            console.error('Observer does not exist!');
            return;
        }
        this.observers = this.observers.filter(obs => obs !== observer);
        console.log('Observer has been removed!');
    }

    notifyPatients() {
        console.log('Sending test result status to all patients:');
        this.observers.forEach(observer => observer.sendMessage());
    }
}

```

`patients`是观察者。因此，它们必须遵循观察者接口定义的结构。

```
class Patient implements IObserver {
    name: string;

    constructor(name: string) {
        this.name = name;
    }

    sendMessage(): void {
        console.log(`${this.name}, your test results are now ready!`);
    }
}

```

假设患者约翰和杰克正在等待他们的测试结果，并且已经预订了**医院**，一旦测试结果出来，他们都会收到警报。

```
const hospital = new Hospital()
const john = new Patient('John')
const jack = new Patient('Jack')

hospital.addObserver(john)
hospital.addObserver(jack)

hospital.notifyPatients()

```

当单个对象的状态已经改变，而对象的总数未知时，如果需要改变一组对象的状态，可以使用观察者设计模式。大多数订阅系统在某种程度上都使用观察者模式。JavaScript 中的事件监听器也是观察者。

## 构建器模式:子类少，问题少

构建器设计模式是不言自明的。它创建对象，但真正的亮点是当需要创建多个具有相似性的对象时。本质上，它允许您编写所有的逻辑来在同一个地方创建多个不同的对象。

### 概念

构建器避免了从基类或大型构造器创建无数子类的需要，这些构造器使用方法链和用单个方法将每个属性添加到对象中来创建大量条件逻辑。还有最后一个方法可以返回已经构建好的对象。或者，您可以包含一个重置构建器的方法。

### 履行

想象一下医疗环境中的测试结果。一个测试结果对象可以有不同的属性和一些共同的属性，因为有不同类型的测试。妊娠测试的结果与贫血测试的结果具有不同的特性。有时医生可能会同时检查怀孕和贫血。这为使用构建器模式创造了机会。

像往常一样，我们必须首先为每个测试结果对象创建一个接口来定义它的结构。HCG 是一种激素，在怀孕测试中进行测试。RBC 代表红细胞，红细胞的可用性在贫血测试中进行测试。

```
interface ITestResult {
  name: string;
  rbcCount: string;
  hcgLevel: string;
}

```

每个属性都是使用其方法定义的。每个方法必须返回构建器以允许方法链接。一旦对象准备好了，就可以构建/返回了。

```
class TestResultBuilder {
  private readonly testResult: ITestResult;

  constructor() {
    this.testResult = {
      name: '',
      rbcCount: '',
      hcgLevel: '',
    };
  }

  name(name: string): TestResultBuilder {
    this.testResult.name = name;
    return this;
  }

  rbcCount(rbcCount: string): TestResultBuilder {
    this.testResult.rbcCount = rbcCount;
    return this;
  }

  hcgLevel(hcgLevel: string): TestResultBuilder {
    this.testResult.hcgLevel = hcgLevel;
    return this;
  }

  build(): ITestResult {
    return this.testResult;
  }
}

```

您可以使用生成器模式来避免一个常见的问题，即伸缩构造函数，这是指您创建一个大构造函数的较小版本来减少构造函数中的逻辑数量。通过使用构建器模式而不是伸缩构造器模式，您可以省去维护多个构造器的麻烦。

## 代理模式:理想的中间人

代理模式是一种被低估但功能强大的设计模式，它有助于解决一些非常困难的问题。顾名思义，代理模式的目标是创建其他东西的替代品，这样所有东西都必须首先通过替代品才能到达实际对象。

### 概念

代理设计模式背后的概念相当简单:您必须在实际对象之上创建一个层，其他所有东西都必须与之交互才能到达实际对象。为此，在代理对象中必须有对实际对象的引用。

### 履行

想象一个向控制台输出某种消息的记录器。您希望在打印每封邮件时包含日期和时间，但前提是启用了该功能。这个逻辑可以包含在代理中。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

使用不返回任何内容的`print()`方法为记录器创建一个接口。这个接口声明了代理和实际对象的结构。

```
export interface Logger {
    print(): void;
}

```

代理有一个名为`checkEnabled()`的方法，它有一些逻辑来确定该特性是否已被启用。如果启用了该功能，代理将使用相同的打印方法将日期和时间添加到消息中。

```
class Proxy implements Logger {
    private actualLogger: ActualLogger;

    private checkEnabled(): boolean {
        // Some logic to check if 
        // the feature is enabled
        return true;
    }

    public print(message: string): void {
        if(this.checkEnabled()) {
            const actualMessage = `
            [${new Date().toLocaleString()}]: ${message}`;
            this.actualLogger.print(actualMessage);
        }
    }
}

```

实际的记录器将具有相同的`print()`方法，而没有多余的逻辑来辨别该特性是启用还是禁用。

```
class ActualLogger implements Logger {
    public print(message: string): void {
        console.log(message);
    }
}

```

如果使用得当，代理是非常强大的。您可以使用代理来执行延迟加载(虚拟代理)、实现缓存(缓存代理)和控制对对象的访问(安全代理)。有各种类型的代理执行不同的任务，但基本概念保持一致。

## 在正确的情况下使用正确的设计模式

根据设计模式的预期目的和它们解决的问题类型，可以将设计模式分为三类。

*   **基于结构的**设计模式描述了将对象放在一起的过程的逻辑，同时确保了可重用性和效率(例如，代理模式)
*   基于行为的设计模式描述了对象之间的关系和责任(例如，观察者模式)
*   **基于创建的**设计模式描述了在确保代码可重用的同时制作对象的过程(例如，构建器模式)

知道如何对设计模式进行分类直接有助于确定何时使用或何时不使用设计模式。将这些知识与设计模式本身结合起来，将使您能够提高代码的质量。

## 摘要

让我们从较高的层次来看一下我们在本指南中学习的所有内容。

*   有三种类型的设计模式，根据它们的目的分类:基于结构的、基于行为的和基于创造的
*   观察者模式是一种基于行为的模式，它允许几个对象被通知与特定对象相关的状态变化
*   builder 模式是一种基于创建的模式，它允许您创建许多不同的对象，而不必重载构造函数或创建许多子类
*   代理模式是一种基于结构的模式，它允许替换一个对象，所有东西都必须通过它才能到达实际的对象

最重要的是，设计模式是语言无关的，可以用任何语言实现，以解决特定设计模式想要解决的问题。

查看以下资源以了解更多信息。

*   [TypeScript 文档](https://www.typescriptlang.org/docs/home)包含为想要学习 TypeScript 的 JavaScript 开发人员提供的资源，以及对 TypeScript 的初学者友好的介绍
*   Addy Osmani 所著的《JavaScript 设计模式》一书用引人入胜的视觉效果探索了用 JavaScript 实现的设计模式

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.