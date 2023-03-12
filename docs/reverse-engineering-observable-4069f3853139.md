# 逆向工程观察-日志火箭博客

> 原文：<https://blog.logrocket.com/reverse-engineering-observable-4069f3853139/>

![](img/be1de63acd1e659e8db3f642be3df7e6.png)

**TL；DR:** 在本文中，我们将从 [*RxJS*](https://github.com/ReactiveX/rxjs) 库中逆向工程可观察对象。我们还将从库中重新创建几个操作符，并了解观察器和订阅。我们还将使用[类型脚本](https://www.typescriptlang.org/)来注释代码。最后，我们将编写一个非常小的示例代码来使用这个逆向工程库。您可以在 [StackBlitz](https://stackblitz.com/edit/rxjs-reverse?file=index.ts) 找到演示。

### 反应式编程和 RxJS 简介

让我们保持简单。

*   *反应式编程是用异步数据流编程—Andre Staltz(*[*cycle . js*](https://cycle.js.org/)*)*

我不会在这篇文章中给你一个冗长的解释(你可以在这里找到更多的信息)，但是反应式编程基本上是一种管理异步数据流的范例(或者方法)。

**RxJS** 是做反应式编程的库。它允许你用函数式方法编写反应式程序。

### 什么是可观察的？

[可观测](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html)是 RxJS 的核心要素。这或多或少有点像一个数组，数组中的项目在未来异步到达。

*   *Observable 代表未来价值或事件的可调用集合的概念。— RxJS 文档*

从 API 的角度来看，Observable 有一个 **subscribe** 方法。这个 subscribe 方法用于调用可观察对象的执行。

```
let observable; // somehow we have created it
observable.subscribe(doSomething); // doSomething does the heavy lifting for observable.
```

在上面的例子中，我们使用一些神奇的 rxjs 代码创建了一个名为 **observable** 的可观察对象，然后通过传递 **doSomething 调用 *subscribe* 方法。需要记住的一件重要事情是，当我们调用 subscribe 方法时，Observable 才开始工作。现在，忽略我们是如何创造出可观察的，以及什么是 doSomething。**

同样值得注意的是，这个 subscribe 方法返回一个叫做**订阅**的东西。基本上，这种订阅允许我们从可观察的事物中退订。换句话说，它用一个 **unsubscribe** 方法返回一个对象，这允许我们停止监听 Observable 发送的值。

### 什么是观察者？

Observer 是回调函数的集合，它对通过 Observable 到达的值做出反应。

*   Observer 是一个回调集合，它知道如何监听被观察对象传递的值。— RxJS 文档。

在 Observable 中，我们需要回调三件事:

*   **值** —可观察的将要发送/推送的未来值
*   **错误** —当可观察对象完成发送值时，调用可观察对象发出信号时可能发生的错误

因此， **Observer** 是如下所示的三个回调方法的集合:

```
{
  'next':  (value) => { // do whatever you want to here },
  'error': (error) => { // error occurred },
  'complete': () => { // complete}
}
```

#### 订阅方法和观察器

观察器和订阅方法之间存在关系。看一下下面的例子:

```
let observable; // somehow we have created it
let doSomething = {
  'next': (value) => { // do whatever you want to here },
  'error': (error) => { // error occurred },
  'complete': () => { // complete}
};

observable.subscribe(doSomething); // doSomething does the heavy lifting for observable.
```

这里，我们创建了一个**可观察对象**，然后通过调用**订阅**方法来执行它。如果您仔细观察，我们已经将一个**观察者**传递给了 subscribe 方法。

您可以在 TypeScript 中编写 subscribe 的类型定义，如下所示:

`Observable.subscribe(observer:Observer):Subscription;`

您可以将这种模式与任何 Push API 结合使用。

### 使用可观察和观察者模式

在下面的例子中，我们将围绕 JavaScript 的 **setInterval** API 包装 Observable:

```
function setIntervalObservable(time) {
  return {
    'subscribe': (observer) => {
      let timeId = setInterval(() => { observer.next(); }, time);
      let subscription = {
        'unsubscribe': () => {
          clearInterval(timeId);
        };
      };
      return subscription;
    };
  }
}
```

现在我们可以用**时间**调用这个 **setIntervalObservable** 方法并订阅它。它将在给定的**时间**的每个周期后触发 **observer.next** 回调，如下所示:

```
let interval$ = setIntervalObservable(1000);
let observer = {
  'next': (value) => { console.log("Hello World"); },
  'error': (error) => { // error occurred },
  'complete': () => { // complete}
};
interval$.subscribe(observer);

// Output
// Hello World 
// Hello World
// ...
```

### 可观察的逆向工程

到目前为止，您已经了解了观察者、可观察对象、订阅等内容。现在我们将使用 TypeScript 类和接口创建 Observable。

### 创建观察者界面

如前所述，观察者是回调的集合。你已经知道**下一个**、**错误**和**完成**但是有一个可选值叫做**关闭**。您将在本教程的后面使用它:

```
interface Observer {
  next: (value: any) => void;
  error: (err: any) => void;
  complete: () => void;
}
```

### 正在创建订阅类

如上所述， **subscribe** 方法返回一个**订阅**。因此，基本上，订阅将 **unsubscribe** 方法作为输入，以便用户稍后可以调用它:

```
class Subscription {
  unsubscribe:() => void;
  constructor(unsubscribe:() => void) {
    this.unsubscribe = unsubscribe;
  }
};
```

### 创建可观察类

在本节中，我们将创建一个可观察的类和一个构造函数，它将 **subscribe** 方法作为输入。subscribe 方法将**观察者**作为输入，并返回一个**订阅:**

```
class Observable {
  subscribe: (observer: Observer) => Subscription;
  constructor(subscribe) {
    this.subscribe = subscribe;
  };
}
```

### 在可观察类上创建创建静态方法

Observable 还附带了一个名为 ***create*** 的静态方法来创建新的 Observable。这个方法也采用了一个**订阅**方法，并返回一个**可观察值:**

```
class Observable {
...
  public static create(subscribe): Observable {
    return new Observable(subscribe);
  };
}
```

### RxJS 创建运算符

通常，在使用 RxJS 时，您不需要创建自己的自定义可观察对象。RxJS 附带了创建方法，允许您从不同类型的输入中创建可观察对象。根据需要，创建方法的输入可以是任何东西，但它必须返回一个可观察值。

您可以使用 TypeScript 描述创建运算符，如下所示:

`creationOperator(input:any): Observable;`

RxJS 中有很多创建操作符，比如来自事件的和来自的**等等。**

**setIntervalObservable** (我们之前用过的)其实是一个创建方法。我们可以使用我们的 Observable 和 Subscription 类轻松地重写它，如下所示:

```
function setIntervalObservable(time) {
  return Observable.create(observer => {
    let timeId = setInterval(() => observer.next(), time);
    return new Subscription(() => { 
      clearInterval(timeId);
    });
  });
}
```

### 创建运算符的逆向工程

RxJS 的创建操作符的**基本上以*多个值作为输入*，然后将这些值推/发送给观察者，如下所示:**

```
// example
of(1,2,3)
.subscribe(observer);

// output
// 1 2 3
```

我们必须做到以下几点:

*   对作为输入给出的每个值进行循环
*   用这些值触发 **observer.next**
*   之后，火 **observer.complete()**
*   返回一个**订阅**

以下是运算符的**的完整代码:**

```
let of = (...values) =>  {
  return new Observable((observer:Observer) => {
    values.forEach(value => {
      observer.next(value);
    });
    // complete observer
    observer.complete();
    return new Subscription(() => {
      // unsubscription action here
    });
  });
};
```

### 如何创建自定义创建运算符？

创建自定义创建操作符如下所示:

*   根据需要，操作员可以接受任何数量或类型的输入
*   它必须返回一个**可观察值**
*   通过调用 **observer.next** 发送/推送值
*   观察完成后，发射 **observer.complete()**
*   不要忘记从**可观察的**中返回一个**订阅**

### RxJS 中的可管道化运算符

到目前为止，我们已经创建了 Observable 并订阅了它们。但是 RxJS 的另一个重要元素允许我们用异步值进行函数式编程。所以我们基本上可以用 Array 的 map，filter 或者类似的方法/算子来修改原来的可观测值。

为了使用这些操作符，有一个名为 **pipe 的可观察类方法。**该管道方法将单个或多个操作符作为输入，并返回新的可观察值:

`Observable.pipe(...invokedOperators): Observable;`

下面是一个在 RxJS 中使用**过滤器**和**映射**操作符的例子:

```
let $stream = of(1,2,3,4);
$stream
.pipe(
  filter(x => x > 2),
  map(x => x * 2)
).subscribe(observer);

// Output
// of     1 2 3 4
// filter - - 3 4
// map    - - 6 8
```

### 创建自定义可管道化运算符

你必须首先理解 RxJS pipeable 操作符的结构和剖析，才能在 Observable 类上编写我们自己的定制 **pipe** 方法。

使用 TypeScript 的可管道化运算符的类型定义如下所示:

`type pipeableOperator = (input) => (source:Observable) => Observable;`

*   操作员接受一个**输入。**该输入可以是任何值，可以是单值或多值。这取决于你想做什么样的运营商。
*   operator 函数返回另一个函数。该返回函数将源可观察值作为输入，并通过基于操作员的 ***输入*** 执行所需动作来修改输入，从而返回新的可观察值。

### 创建过滤器运算符

为了创建过滤操作符，让我们先看看它的结构:

`filter(filterPredicate): (source:Observable) => Observable;`

1.  **filterPredicate** 是返回布尔值的函数。你必须将它应用到源可观测物发出的值上。
2.  我们可以通过订阅 source Observable 来访问它发出的值，如下所示:

```
source.subscribe({
  next(value) {
    if(filterPredicate(value) {
      // emit the value for new Observer here
      // using observer.next(value);
    });
  }
});
```

3.在上面所示的 *if 条件*中，发出新的可观察值。

下面是我们如何编写过滤器操作符的代码:

```
// type definition for pipeable operator
type pipeableOperator = (input) => (source:Observable) => Observable;

let filter:pipeableOperator = (filterFn) => {
  return (source) => {
    return new Observable((observer: Observer) => {
        let subscription = source.subscribe({ 
         next(value) { 
           if(filterFn(value)) {
             observer.next(value); 
           };
          },
         error(err) { observer.error(err); },
         complete() { observer.complete(); }
        });

        // Unsubscription
        return new Subscription(() => {
            subscription.unsubscribe();
        });
    });
  };
};
```

*   *同样，你可以创建其他操作符，比如* ***地图*** *等等。*

**创建管道方法**

现在我们可以逆向工程**管道**方法。但首先，我们必须做到以下几点:

1.  管道方法接受单个或多个输入。所以我们必须遍历所有这些操作符。我们可以使用 JavaScript 的 **spread** 操作符和 **forEach** 来实现，如下所示:

```
pipe(...operators) {
  operators.forEach(operator => {
  });
}
```

2.重要的是要认识到，在内部管道方法中，我们实际上并没有得到可管道化的操作符，而是得到了对它的调用。换句话说，我们基本上是在访问操作符返回的任何内容。它是一个函数，接受源观测值并返回新的修改后的观测值。

1.  我们可以通过**这个来访问源观测值。**
2.  基本上，我们将从**这个**作为第一个可观察值开始，然后调用它的第一个操作符。我们将使用这个新的可观察值作为下一个操作符的来源。

下面是我们将如何编写管道方法:

```
class Observable {
...
 public pipe(...invockedOps): Observable {
    let observable = this;
    invockedOps.forEach(invockedOp => {
      observable = invockedOp(observable);
    });
    return observable;
  }
}
```

### 最后一个例子

下面是一个使用我们的逆向工程库创建和消费可观察对象的示例:

```
// import the code that you have written 
// import { Observer, of }
// import { map, filter }
let observer: Observer =  {
  next(value) { console.log(value); },
  error(error) { console.warn(error) },
  complete() { console.log("completed") }
};

let myValues = of(1,2,3,4)
.pipe(
  filter(x => x > 2)
).subscribe(observer);
```

有趣的是，上面例子中的代码完全兼容 RxJS。所以基本上你可以将导入切换到 **RxJS** 库，一切都会正常工作。

### 结论

在本文中，我们编写了 RxJS 可观察对象的一个非常小的子集，创建了自定义创建操作符和自定义管道操作符，以及 RxJS 中操作符、过滤操作符的逆向工程。我们还了解了**观察者**和**订阅**。你可以在 [StackBlitz](https://stackblitz.com/edit/rxjs-reverse?file=index.ts) 查看演示。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)