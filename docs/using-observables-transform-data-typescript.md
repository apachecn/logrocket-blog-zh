# 使用 RxJS 观察值转换 TypeScript 中的数据

> 原文：<https://blog.logrocket.com/using-observables-transform-data-typescript/>

## 介绍

事件处理是构建 TypeScript 应用程序时的常见实现之一。

注册一个事件以及在事件被触发时执行一些动作需要一个可观察的模式，它提供了一个到事件流的单向流，使得调试和错误处理事件流变得容易得多。

在本文中，我们将探索可观察对象，它们如何用于处理事件驱动的数据，以及错误和异步数据。

1.  1.  [什么是 RxJS 可观测量？](#what-are-rxjs-observables)
    2.  [可观察算子](#observable-operators)
    3.  [用可观测值变换数组](#transforming-arrays-observable)
    4.  [组合多个操作符进行事件流转换](#combining-multiple-operators-event-stream-information)
    5.  [处理可观察到的错误](#handling-errors-observables)

## 什么是 RxJS 可观测量？

JavaScript 的一个有趣但有时令人费解的部分是处理事件。

考虑一个像脸书这样的实时聊天应用程序，它在特定的时间范围内有多个事件发生。用户可以在他的新闻提要上键入一些文本，同时接收来自其他朋友的一些通知和消息，没有特定的顺序。现在，处理这些事件是应用程序的责任。这就是可观性的来源。

一个[可观察的](https://blog.logrocket.com/understanding-rxjs-observables/)是多个输入值的集合，这些输入值使用数组方法进行处理，比如`map`、`reduce`、`filter`等等。在处理异步操作时，例如发出 HTTP 请求、用户输入事件等，它非常方便。

可观察模式是一种设计模式，用于注册事件，以及在事件被触发时实现流程。专门从事事件处理的最强大和最流行的 JavaScript 库之一是 JavaScript 库的反应扩展，也称为 RxJS。

为了开始，我们需要确保已经安装了 RxJS 库。因此，我们使用以下命令安装该库:

```
npm install rxjs

```

这个库已经有了 TypeScript 需要的所有声明文件，所以没有必要单独安装它们。

为了创建一个可观察对象，我们需要来自 RxJS 的`Observable`类型和`of`方法，如下所示:

```
import { of, Observable } from "rxjs"; 
const emitter : Observable<string> = of("Sam", "Ray", "Thomas");

```

在上面的代码片段中，我们从 RxJS 库中导入了`of`函数和`Observable`类型，然后从字符串`"Sam"`、`"Ray"`和`"Thomas"`中创建了一个`Observable`。

接下来，我们将订阅一个可观察值，如下所示:

```
emitter.subscribe((value: string) => {
  console.log(`Name: ${value}`)
})

```

在上面的代码片段中，我们通过对`emitter`变量调用`subscribe`方法注册了一个观察者。由于`emitter`变量是可观察的类型，我们可以自动访问`subscribe`方法。`subscribe`方法接收一个函数作为参数，这个函数将为观察对象发出的每个值调用一次。

上述代码将输出以下可观察到的流:

```
Name: Sam
Name: Ray
Name: Thomas

```

## 可观察算子

可管道操作符和创建操作符是 RxJS 中的两种操作符。

可管道操作符是将一个可观察值作为输入并返回另一个可观察值的方法。可以使用语法`observableInstance.pipe(operator())`将它们传送到 Observables。它包括`[filter(...)](https://rxjs.dev/api/operators/filter)`和`[mergeMap(...)](https://rxjs.dev/api/operators/mergeMap)`方法。

创建操作符是在被调用时创建新的可观察对象的操作符。

创建运算符包括以下内容:

您可以参考 [RxJS 官方文档](https://rxjs.dev/guide/operators)以获得完整的操作员列表。

## 用可观测值变换数组

RxJS `from`方法允许对数组中的数据进行转换。它接收一个数组作为输入，并将数组中的每个数据转换为可观察数据。

让我们考虑下面的代码片段:

```
const transformArray: Observable<number> = from([1, 2, 3, 4]);
transformArray.subscribe((value: number) => {
  console.log(`value: ${value}`);
});

```

上面的代码片段使用`from`方法将数组中的值转换为可观察值，然后对`transformArray`变量调用`subscribe`方法。`subscribe`方法接受一个函数，该函数为可观察对象发出的每个值执行。

## 组合多个操作符进行事件流转换

RxJS 为我们提供了`pipe`方法，该方法允许将多个操作符方法组合起来进行事件流的复杂转换。

让我们考虑下面的代码:

```
const emitter = of(4, 9, 16, 25)
const mapValue = emitter.pipe(
  map((value: number) => {
    return Math.sqrt(value)
  }),
  map((value: number) => {
    return `square root: ${value}`
  })
)
mapValue.subscribe((value: string) => {
  console.log(`string value emitted ${value}`)
})

```

这里，RxJS `of`方法用于从数字`4`、`9`、`16`和`25`中创建一个可观察值。`pipe`方法接受两个`map`方法。第一个`map`方法返回输入值的平方根，第二个`map`方法将第一个`map`方法的输出转换为字符串。

最后，对发出的每个可观察对象调用`subscribe`方法。

运行上面的代码将输出如下:

```
string value emitted square root: 2 
string value emitted square root: 3 
string value emitted square root: 4
string value emitted square root: 5

```

## 处理可观察到的错误

处理可观察流中的异常需要一个结构良好的机制来捕捉这些异常。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们考虑下面的代码片段:

```
interface IName {
  value: string;
}
interface IObj {
  name?: IName;
}
const emitObj: Observable<IObj> = of(
  { name: { value: "Bob" } },
  {},
  { name: { value: "Sam" } }
);

```

在上面的代码片段中，我们用类型为`string`的属性`value`创建了`IName`。此外，我们创建了具有可选属性`IName`的`IObj`。然后我们创建了`emitObj`可观察对象，它发出三个值。

现在让我们考虑以下可观察的流:

```
const returnName = emitObj.pipe(
    map((value: IObj) => {
        return value.name!.value;
    })
);
returnName.subscribe((value: string) => {
    console.log(`name: ${value} `)
});

```

在上面的代码片段中，我们创建了一个可观察的流`returnName`，它返回输入流值的`name.value`属性。然后，我们订阅这个流，并将收到的值记录到控制台。

当我们运行这个代码片段时，我们将得到以下输出:

```
name: Bob
TypeError: Cannot read property 'value' of undefined

```

发生这个错误是因为在我们的可观察流中发出的第二个值没有 name 属性，导致一个未定义的值。

为了修复这个错误，我们可以在我们的可观察流中为`subscribe`函数创建一个错误处理程序。

让我们考虑以下情况:

```
returnName.subscribe(
  // called for each observable value
  (value: string| null) => {
    console.log(`name: ${value} `);
  },
  // called if an error occurs
  (error: unknown) => {
    console.log(`error : ${error}`);
  },
  // called when execution is done
  () => {
    console.log(`done`);
  }
);

```

这里，我们为`subscribe`方法提供了三个函数作为参数，第一个函数为可观察流发出的每个值调用，第二个函数在错误发生时调用，最后一个函数在`subscribe`方法执行完成时调用。

当我们运行这个代码片段时，我们将得到以下输出:

```
name: Bob
TypeError: Cannot read property 'value' of undefined

```

这里发生的是由可观察流发出的第一个值由提供给`subscribe`方法的第一个函数处理。由可观察流发出的第二个值导致一个错误，这个错误被提供给`subscribe`方法的错误函数捕获。最后一个函数没有被调用，因为在可观察的流中出现了错误。

### `catchError`

处理可观察错误的另一种方法是在可观察流本身中使用`catchError`操作符，这样我们就可以尽早发现这些错误。

让我们考虑下面的代码片段:

```
const returnName = emitObj.pipe(
    map((value: IObj) => {
        return value!.name!.value;
    }),
    catchError((error: unknown) => {
        console.log(`stream caught : ${error}`);
        return of(null);
    })
);

```

这里，我们向可观察的流添加了一个`catchError`操作符。在这个函数中，我们将错误消息记录到控制台，然后返回一个可观察到的值`null`。通过这种实现，即使在可观察对象中出现错误，最后一个函数也会被触发。

当我们运行这个代码片段时，我们将得到以下输出:

```
Name: Bob
stream caught : TypeError: Cannot read property 'value' of undefined
received null
done

```

这里发生的事情是，`map`方法为可观察流的第二个值生成一个错误，之后我们的`catchError`函数被调用，并出现以下错误:“无法读取 undefined 的属性‘value’。”同样，最后一个方法被触发。

一旦一个可观察的流中出现错误，该流将停止发出值。这就是为什么`emitObj`可观察流不发出它最后一个参数的值。

## 结论

在本文中，我们探索了使用可观察流的数据转换以及如何实现它们。此外，我们还讨论了使用可观察对象的错误处理，并提供了在处理可观察流时实现错误处理程序的策略。为了避免内存泄漏和意外结果，请确保取消订阅您手动订阅的任何订阅。

希望这篇文章对你有所帮助。你也可以查看官方的 RxJS 文档,深入了解 RxJS 可观察模块。另外，我推荐 Brian Troncone 的 [learnrxjs.io](https://www.learnrxjs.io) 对 rxjs 概念进行更直观的解释。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.