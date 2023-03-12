# 享受 ES6 代理的乐趣

> 原文：<https://blog.logrocket.com/having-fun-with-es6-proxies/>

`Proxy`是 ES6 版 JavaScript 中引入的最容易被忽视的概念之一。

不可否认，它在日常生活中并不是特别有用，但在未来的某个时候它肯定会派上用场。

## 基础知识

`Proxy`对象用于定义基本操作的自定义行为，如属性查找、赋值和函数调用。

代理最基本的例子是:

```
const obj = {
 a: 1,
 b: 2,
};

const proxiedObj = new Proxy(obj, {
 get: (target, propertyName) => {
   // get the value from the "original" object
   const value = target[propertyName];

   if (!value && value !== 0) {
     console.warn('Trying to get non-existing property!');

     return 0;
   }

   // return the incremented value
   return value + 1;
 },
 set: (target, key, value) => {
   // decrement each value before saving
   target[key] = value - 1;

   // return true to indicate successful operation
   return true;
 },
});

proxiedObj.a = 5;

console.log(proxiedObj.a); // -> incremented obj.a (5)
console.log(obj.a); // -> 4

console.log(proxiedObj.c); // -> 0, logs the warning (the c property doesn't exist)
```

我们通过在提供给代理构造函数的对象中定义带有各自名称的处理程序，截取了`get`和`set`操作的默认行为。现在，每个`get`操作将返回属性的增量值，而`set`将在保存到目标对象之前递减该值。

对于代理，重要的是要记住，一旦创建了代理，它应该是与对象交互的唯一方式。

### 不同种类的陷阱

除了`get`和`set`之外，还有许多陷阱(拦截对象默认行为的处理程序)，但在本文中我们不会用到它们。话虽如此，如果你有兴趣阅读更多关于它们的内容，这里有[文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)。

## 开心

现在我们知道了代理是如何工作的，让我们来玩玩吧。

### 观察对象的状态

如前所述，用代理拦截操作是非常容易的。观察一个对象的状态就是在每次有赋值操作的时候得到通知。

```
const observe = (object, callback) => {
 return new Proxy(object, {
   set(target, propKey, value) {
     const oldValue = target[propKey];

     target[propKey] = value;

     callback({
       property: propKey,
       newValue: value,
       oldValue,
     });

     return true;
   }
 });
};

const a = observe({ b: 1 }, arg => {
 console.log(arg);
});

a.b = 5; // -> logs from the provided callback: {property: "b", oldValue: 1, newValue: 5}
```

这就是我们所要做的——每次触发`set`处理程序时调用提供的回调。

作为`callback`的一个参数，我们提供了一个具有三个属性的对象:更改属性的名称、旧值和新值。

在执行`callback`之前，我们在目标对象中分配新值，这样分配就实际发生了。我们必须返回`true`来表明操作已经成功；否则，就会抛出一个`TypeError`。

这里有一个[活生生的例子](https://codesandbox.io/s/proxy-observe-s5sjv?fontsize=14)。

### 验证`set`上的属性

仔细想想，代理是实现验证的好地方——它们与数据本身没有紧密耦合。让我们实现一个简单的验证代理。

和前面的例子一样，我们必须拦截`set`操作。我们希望以下面的方式来结束声明数据验证:

```
const personWithValidation = withValidation(person, {
 firstName: [validators.string.isString(), validators.string.longerThan(3)],
 lastName: [validators.string.isString(), validators.string.longerThan(7)],
 age: [validators.number.isNumber(), validators.number.greaterThan(0)]
});
```

为了实现这一点，我们将`withValidation`函数定义如下:

```
const withValidation = (object, schema) => {
 return new Proxy(object, {
   set: (target, key, value) => {
     const validators = schema[key];

     if (!validators || !validators.length) {
       target[key] = value;

       return true;
     }

     const shouldSet = validators.every(validator => validator(value));

     if (!shouldSet) {
       // or get some custom error
       return false;
     }

     target[key] = value;
     return true;
   }
 });
};
```

首先，我们检查所提供的模式中是否有当前正在分配的属性的`validators`——如果没有，就没有什么需要验证的，我们只是简单地分配值。

如果确实有为属性定义的`validators`，我们断言它们在赋值前都返回`true`。如果其中一个验证器返回`false`，整个`set`操作返回 false，导致代理抛出一个错误。

最后要做的是创建`validators`对象。

```
const validators = {
 number: {
   greaterThan: expectedValue => {
     return value => {
       return value > expectedValue;
     };
   },
   isNumber: () => {
     return value => {
       return Number(value) === value;
     };
   }
 },
 string: {
   longerThan: expectedLength => {
     return value => {
       return value.length > expectedLength;
     };
   },
   isString: () => {
     return value => {
       return String(value) === value;
     };
   }
 }
};
```

`validators`对象包含验证函数，这些函数按照它们应该验证的类型进行分组。每个验证器在调用时接受必要的参数，如`validators.number.greaterThan(0)`，并返回一个函数。验证发生在返回的函数中。

我们可以用各种令人惊奇的特性来扩展验证，比如虚拟字段或者从验证器内部抛出错误来指出哪里出错了，但是这样会降低代码的可读性，这超出了本文的范围。

这里有一个[活生生的例子](https://codesandbox.io/s/proxy-validation-2858r?fontsize=14)。

### 让代码懒惰

对于最后一个——希望也是最有趣的——例子，让我们创建一个代理，让所有操作都变得懒惰。

这里有一个非常简单的类叫做`Calculator`，它包含了一些基本的算术运算。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
class Calculator {
 add(a, b) {
   return a + b;
 }

 subtract(a, b) {
   return a - b;
 }

 multiply(a, b) {
   return a * b;
 }

 divide(a, b) {
   return a / b;
 }
}
```

通常，如果我们运行下面的代码行:

```
new Calculator().add(1, 5) // -> 6
```

结果将是 6。

代码当场执行。我们想要的是让代码等待信号运行，就像一个`run`方法。通过这种方式，操作将被推迟到需要的时候——或者根本不执行，如果根本不需要的话。

所以下面的代码，而不是 6，将返回`Calculator`类本身的实例:

```
lazyCalculator.add(1, 5) // -> Calculator {}
```

这给了我们另一个很好的特性:方法链接。

```
lazyCalculator.add(1, 5).divide(10, 10).run() // -> 1

```

这种方法的问题是，在`divide`中，我们不知道`add`的结果是什么，这使得它有点没用。因为我们控制了参数，所以我们可以很容易地提供一种方法，通过一个预先定义的变量来获得结果，例如`$`。

```
lazyCalculator.add(5, 10).subtract($, 5).multiply($, 10).run(); // -> 100
```

`$`这里只是一个常数`Symbol`。在执行过程中，我们用前一个方法返回的结果动态替换它。

```
const $ = Symbol('RESULT_ARGUMENT');
```

现在我们已经对我们想要实现什么有了一个公平的理解，让我们开始吧。

让我们创建一个名为`lazify`的函数。该函数创建一个代理来拦截`get`操作。

```
function lazify(instance) {
 const operations = [];

 const proxy = new Proxy(instance, {
   get(target, propKey) {
     const propertyOrMethod = target[propKey];

     if (!propertyOrMethod) {
       throw new Error('No property found.');
     }

     // is not a function
     if (typeof propertyOrMethod !== 'function') {
       return target[propKey];
     }

     return (...args) => {
       operations.push(internalResult => {
         return propertyOrMethod.apply(
           target,
           [...args].map(arg => (arg === $ ? internalResult : arg))
         );
       });

       return proxy;
     };
   }
 });

 return proxy;
}
```

在`get`陷阱中，我们检查请求的属性是否存在；如果没有，我们抛出一个错误。如果属性不是函数，我们不做任何事情就返回它。

代理没有拦截方法调用的方法。相反，他们将它们视为两个操作:`get`操作和一个函数调用。我们的`get`处理程序必须相应地采取行动。

既然我们已经确定该属性是一个函数，我们就返回我们自己的函数，它充当包装器。当包装器函数被执行时，它向操作数组添加另一个新函数。包装函数必须返回代理才能链接方法。

在提供给操作数组的函数中，我们使用提供给包装器的参数来执行方法。将使用 result 参数调用该函数，允许我们用前面方法返回的结果替换所有的`$`。

通过这种方式，我们延迟执行，直到被请求。

既然我们已经构建了存储操作的底层机制，我们需要添加一种方法来运行函数——`.run()`方法。

这很容易做到。我们所要做的就是检查所请求的属性名是否等于 run。如果是，我们返回一个包装函数(因为 run 充当了一个方法)。在包装器内部，我们执行操作数组中的所有函数。

最终的代码如下所示:

```
const executeOperations = (operations, args) => {
 return operations.reduce((args, method) => {
   return [method(...args)];
 }, args);
};

const $ = Symbol('RESULT_ARGUMENT');

function lazify(instance) {
 const operations = [];

 const proxy = new Proxy(instance, {
   get(target, propKey) {
     const propertyOrMethod = target[propKey];

     if (propKey === 'run') {
       return (...args) => {
         return executeOperations(operations, args)[0];
       };
     }

     if (!propertyOrMethod) {
       throw new Error('No property found.');
     }

     // is not a function
     if (typeof propertyOrMethod !== 'function') {
       return target[propKey];
     }

     return (...args) => {
       operations.push(internalResult => {
         return propertyOrMethod.apply(
           target,
           [...args].map(arg => (arg === $ ? internalResult : arg))
         );
       });

       return proxy;
     };
   }
 });

 return proxy;
}
```

`executeOperations`函数接受一个函数数组并逐个执行它们，将前一个函数的结果传递给下一个函数的调用。

现在是最后一个例子:

```
const lazyCalculator = lazify(new Calculator());

const a = lazyCalculator
 .add(5, 10)
 .subtract($, 5)
 .multiply($, 10);

console.log(a.run()); // -> 100
```

如果你对添加更多的功能感兴趣，我已经为`lazify`函数添加了一些特性——异步执行、自定义方法名，以及通过`.chain()`方法添加自定义函数的可能性。`lazify`功能的两个版本都可以在[实例](https://codesandbox.io/s/lazy-proxy-q7tbn?fontsize=14)中找到。

## 摘要

既然您已经看到了代理的运行，我希望您可以在自己的代码库中很好地使用它们。

代理有许多比这里介绍的更有趣的用途，比如实现负索引和捕捉对象中所有不存在的属性。但是要小心:当[性能](http://thecodebarbarian.com/thoughts-on-es6-proxies-performance)是一个重要因素时，代理是一个糟糕的选择。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)