# 仔细看看 JavaScript 闭包、高阶函数和 currying - LogRocket 博客

> 原文：<https://blog.logrocket.com/a-closer-look-at-javascript-closures-higher-order-functions-and-currying/>

## 关闭

闭包是最强大的 JavaScript 特性之一，但是一开始可能会有点令人生畏。对闭包有扎实的理解为理解高阶函数和 currying 等主题铺平了道路。

我们将讨论一些概念，这些概念有助于说明闭包、高阶函数和 currying 的原理。

JavaScript 中的函数是一等公民，这意味着:

1.  函数可以分配给变量
2.  函数可以作为参数传递给其他函数
3.  函数可以返回其他函数

```
// functions can be assigned to variables
    const morningGreetings = (name) => {
      console.log(`Good morning ${name}`); 
    }
    const eveningGreeting = function (name) {
      console.log(`Good evening ${name}`);
    }

    // functions can be passed as arguments to other functions
    const todaysGreeting = (morningGreetings, eveningGreeting) => {
      morningGreetings('Barack')
      console.log(`Thanks for all you have done during the day`);
      eveningGreeting('Barack');
    }

    // functions can return other functions
     function myCounter ()  {
      let count = 0
      return function () {
         return ++count;
      }
    }
    const noOfTimes = myCounter();
    console.log(noOfTimes()); // 1
```

我们将仔细研究的特性允许函数返回函数。该特性的封闭性取决于 JavaScript 的独特特性。

在 JavaScript 中，函数能够引用一个没有在函数中定义的变量，但是在封闭函数或全局范围内是可用的。

考虑下面的例子:

```
const iamglobal = 'available throughout the programme';
    function funky() {
      const iamlocal = 'local to the function scope funky';
    }
    console.log(iamglobal);// available throughout the programme
    console.log(iamlocal); // iamlocal is not defined
```

如您所见，我们无法访问`function funky`范围之外的变量`iamlocal`。这是因为只有当 funky 处于活动状态时，变量才会保持“活动”状态。

一旦函数被调用，对其作用域内声明的任何变量的引用都被删除，内存被交还给计算机使用。

然而，有一种方法，即使在函数被调用之后，我们也可以访问函数中声明的变量。

这就是闭包的用处。

闭包是对在另一个函数的作用域中声明的变量的引用，该变量通过从现有函数的调用中返回一个新函数而保持活动。

让我们来看一个例子:

```
function outerScope() {
  const outside = 'i am outside';
  function innerScope() {
    const inside = 'i am inside';
    console.log('innerScope ➡️', outside);
    console.log('innerScope ➡️',inside);
  }
  console.log('outerScope ➡️', outside);
  innerScope();
}
outerScope();
// outerScope ➡️ i am outside
// innerScope ➡️ i am outside
// innerScope ➡️ i am inside
```

可以从函数`innerScope`中访问变量`outside`的值。闭包的概念依赖于这种能力。

从上面的例子中，我们可以返回函数`innerScope`而不是在`outerScope`中调用它，因为这接近真实世界的场景。

让我们修改上面的例子来反映这一变化:

```
function outerScope() {
  const outside = 'i am outside';
  function innerScope() {
    const inside = 'i am inside';
    console.log('innerScope ➡', outside);
    console.log('innerScope ➡',inside);
  }
  return innerScope
}

const inner = outerScope();
inner();
// outerScope ➡️ i am outside
// innerScope ➡️ i am outside
```

这类似于上面的例子，它说明了函数如何具有返回函数的能力。

让我们更进一步，看一个更真实的例子:

```
function closure(a) { 
  return function trapB (b) {
    return function trapC(c) {
      return c * a + b; 
    }
  }
}

const oneEight = closure(1.8);
const thirtyTwo = oneEight(32);
const degreeToFahrenheit = thirtyTwo(30);
console.log(degreeToFahrenheit); // 86
```

将每个函数声明视为一个循环是有用的，其中每个封闭循环都可以访问前一个循环中声明的变量:

在这种情况下，trapC 可以访问变量`a, b and c`，而 trapB 可以访问变量`a and b`，最后闭包只能访问`a`。

## 高阶函数

高阶函数是接受另一个函数作为参数、返回另一个函数作为结果或两者兼有的函数。

到目前为止，我们一直在使用高阶函数，如我们的`closure`、`outerScope`、`todaysGreeting`和`myCounter`示例所示。

闭包是高阶函数的组成部分。

高阶函数的核心好处之一是，它们允许我们自定义调用函数的方式。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

请考虑下图:

```
const multiply = (a , b) => {
  return a * b;
}
console.log(multiply(2,3)) // 6
```

如果我们只想在整个程序中得到 2 的所有倍数，你可以在整个程序中重复 2 作为参数之一:

```
multiply(2,1) // 2
multiply(2,2) // 4
multiply(2,3) // 6
```

虽然这很有效，但是它在我们的代码中引入了大量的重复，并且违反了 DRY(不要重复自己)原则。

你也可以说我们可以把值 2 硬编码到我们的函数定义中。嗯，这是真的，但这会使我们的功能不太容易重用。

让我们重新定义该函数以使用高阶函数，这样我们就可以看到它在调用该函数时提供的好处和灵活性:

```
const multiply = (a) => {
    return (b) => {
      return a * b;
    }
  }
```

以这种方式定义了上面的函数后，我们可以如下创建自定义函数调用:

```
const multiplyByTwo = multiply(2);
console.log(multiplyByTwo(3)) // 6

const multiplyByThree = multiply(3);
console.log(multiplyByThree(6)); // 18
```

我们可以创建具有实际用途的定制功能，也可以省去重复的麻烦。

## 携带

Currying 是一个涉及函数的局部应用的过程。

当调用一个函数所需的所有参数都没有被提供时，这个函数就被称为 curried。在这种情况下，它将返回另一个函数，该函数保留已经提供的参数，并期望在调用该函数之前提供剩余的省略参数。

只有在提供了所有参数的情况下，才会调用该函数。否则，将返回一个新函数，该函数保留现有参数并接受新参数。

当你调用一个函数时，你把它叫做`f(a)(b)(c)(d)`而不是`f(a, b, c , d)`。推而广之，所有的可化函数都是高阶函数，但不是所有的高阶函数都是可化的。

这里的底线是 currying 允许我们将单个函数变成一系列函数。

让我们考虑下面的例子:

```
function sum (a, b) {
  return a + b;
}
console.log(sum(4,5)) // 9
```

我们可以继续运行这个函数，这样当没有提供所有参数时，我们就可以灵活地部分调用它。

```
function curriedSum (x,y)  {
    if (y === undefined) {
      return function(z) {
        return x + z
      }
    } else {
      return x + y;
    }
  }

 console.log(curriedSum(4, 5)) // 9
console.log(curriedSum(4)(5)) // 9
```

我们不必每次需要函数时都编写另一个 curried 实现来部分调用它。相反，我们可以使用一个通用的 curry 函数，并将我们的原始函数作为参数传递给它。

方法如下:

```
function curry(func) {
  return function curried(...args) {
    if (args.length >= func.length) {
      return func.apply(this, args);
    } else {
      return function(...args2) {
        return curried.apply(this, args.concat(args2));
      }
    }
  };
}
```

让我们用一个例子来说明这是如何工作的。

```
function mean (a , b, c) {
return (a + b + c) / 3
}
const curriedMean = curry(mean);
console.log(curriedMean(1,2,3))
console.log(curriedMean(1,2)(3))
console.log(curriedMean(1)(2)(3))
```

## 结论

正如您所看到的，这些概念是建立在彼此之上的，因为闭包在高阶函数中被广泛使用，而高阶函数类似于 curried 函数。

对上述概念的深刻理解让我们深入了解流行的 JavaScript 库是如何实现一些函数的，例如 React-Redux 使用的 connect 函数。

```
connect(mapState)(MyComponent)
```

## 参考

> Currying 是一种使用函数的高级技术。它不仅在 JavaScript 中使用，也在其他语言中使用。Currying 是一种函数转换，它将函数从可作为 f(a，b，c)调用转换为可作为 f(a)(b)(c)调用。Currying 不调用函数。它只是改变了它。

> 越全面越好。在 600 多页中，你将从 JavaScript 新手变成忍者。涵盖了从数组、逻辑和循环到函数、对象、DOM、事件、测试和调试、Ajax 等等。这是你用 JavaScript 构建的所有东西。

## 实现新的 JS 特性？了解 JavaScript 错误如何影响用户。

追踪生产 JavaScript 异常或错误的原因是耗时且令人沮丧的。如果您对监控 JavaScript 错误感兴趣，并想看看它们是如何影响用户的，[试试 LogRocket](https://logrocket.com/signup/) 。[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://logrocket.com/signup/)[https://logrocket.com/signup/](https://logrocket.com/signup/)

LogRocket 就像是网络应用的 DVR，记录下你网站上发生的每一件事。LogRocket 使您能够聚合和报告错误，以查看它们发生的频率以及它们影响了多少用户群。您可以轻松地重放发生错误的特定用户会话，以查看导致错误的用户操作。

LogRocket 让你的应用程序记录带有标题+正文的请求/响应，以及关于用户的上下文信息，以全面了解问题。它还记录页面上的 HTML 和 CSS，甚至可以重建最复杂的单页面应用程序的像素级完美视频。

增强您的 JavaScript 错误监控能力—[开始免费监控](https://logrocket.com/signup/)。