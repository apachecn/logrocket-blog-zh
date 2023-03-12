# 用彻底的类型检查改进 TypeScript 错误处理

> 原文：<https://blog.logrocket.com/improve-error-handling-typescript-exhaustive-type-checking/>

很少有程序是完全独立工作的。即使开发人员总是编写完美的代码，当代码与外部组件(如数据库、REST APIs，甚至那个有一堆星星的流行 npm 包)交互时，也很有可能遇到错误！

当服务器崩溃时，一个负责任的开发人员不会袖手旁观，而是进行防御性的思考，并为错误的请求到来做好准备。在本文中，我们将介绍 TypeScript (TS)中几种最常见的错误处理方法。我们将讨论最常见的错误类型，null 和基于`try...catch`的错误处理的缺点，最后，提出一种更简洁的方法来管理错误。

我们开始吧！

> 注意:如果您不熟悉 TypeScript，这是个好消息。导致错误的条件和解决方案也适用于 JavaScript。

## 让我们谈谈错误

为了有效地讨论和评估 TypeScript 中不同的错误处理方法，有必要首先讨论错误的本质。并非所有的错误都是相同的，我们可以将它们分为两大类:已知错误和程序员错误。

### 已知错误

已知错误是我们知道(最终)会发生的错误:数据库连接失败、HTTP 调用返回错误代码、用户在表单中输入无效的电子邮件……所有这些错误都有一个共同点，即在开发过程中，开发人员会意识到它们。当它们发生时，它们被处理，并且可以尝试恢复(例如，通过重试 HTTP 调用)。未能正确处理这些错误会产生程序员错误(也称为 bug)。

### 程序员错误

程序员错误(bug)是未被发现的已知错误。如果一个数组的长度为 10，并且我们试图访问第 11 个元素，那么它将会产生一个异常，这个异常会导致运行时间停止。bug 是事先不知道的(或者它们会被处理并成为已知的错误)，所以它们不能被计划。未处理的内存不足和其他环境错误也被认为是程序员错误，因此从这些错误中正常恢复通常是不可能的。有必要记住这种区别，因为我们将在文章的剩余部分解决已知的错误。根据定义，程序员的错误是事先不知道的，所以它们不应该和已知的错误一样对待。我们将在下一节详述混合错误类型的危险，所以请继续阅读！​📚​

## 流行的类型脚本错误处理方法

在我们深入研究之前，请记住下面的列表绝非详尽无遗。这里介绍的错误和解决方案都是基于我的主观经验，所以你的里程可能会有所不同。🏎️

### 返回空值

返回 null 是表明代码出错的一种常见方式。当一个函数只有一种失败方式时最好使用它，然而，一些开发人员在一个函数有很多错误时使用它。

在 TypeScript 中返回 null 会强制在代码中的任何地方进行 null 检查，从而导致有关错误原因的特定信息丢失。返回 null 是错误的任意表示，所以如果您尝试返回`0`、`-1`或`false`，您将得到相同的结果。

在下面的代码块中，我们将编写一个函数来检索给定城市的温度和湿度数据。`getWeather`函数通过两个函数`externalTemperatureAPI`和`externalHumidityAPI`与两个外部 API 交互，并聚合结果:

```
const getWeather = async (
  city: string
): Promise<{ temp: number; humidity: number } | null> => {
  const temp = await externalTemperatureAPI(city);
  if (!temp) {
    console.log(`Error fetching temperature for ${city}`);
    return null;
  }
  const humidity = await externalHumidityAPI(city);
  if (!humidity) {
    console.log(`Error fetching humidity for ${city}`);
    return null;
  }
  return { temp, humidity };
};
// ...
const weather = await getWeather("Berlin");
if (weather === null) console.log("getWeather() failed");

```

我们可以看到，在进入`Berlin`时，我们收到了错误消息`Error fetching temperature for ${city}`和`Error fetching humidity for ${city}`。

我们的两个外部 API 函数都可能失败，所以`getWeather`被迫检查这两个函数是否为空。尽管检查 null 比根本不处理错误要好，但它会迫使调用者做出一些猜测。如果一个函数被扩展以支持新的错误，调用者不会知道，除非他们检查函数的内部。

假设`externalTemperatureAPI`最初在 temperature API 返回`HTTP code 500`时抛出一个 null，表示内部服务器错误。如果我们扩展我们的函数来检查 API 响应的结构和类型有效性(即，检查响应是否属于`type number`，调用者将不会知道函数是否由于`HTTP code 500`或意外的 API 响应结构而返回 null。

### 使用`try...catch`抛出自定义错误

创建自定义错误并抛出它们是比返回 null 更好的选择，因为我们可以实现错误粒度，这使函数能够抛出不同的错误，并允许函数的调用方单独处理不同的错误。

但是，任何抛出错误的函数的执行都将被停止，错误会向上传播，从而中断代码的正常流程。虽然这看起来没什么大不了的，尤其是在小应用程序中，但是随着代码在`try...catch`之后继续层叠`try...catch`，可读性和整体性能将会下降。

让我们尝试用`try...catch`方法来解决天气示例中的错误:

```
const getWeather = async (
  city: string
): Promise<{ temp: number; humidity: number }> => {
  try {
    const temp = await externalTemperatureAPI(city);
    try {
      const humidity = await externalHumidityAPI(city);
    } catch (error) {
      console.log(`Error fetching humidity for ${city}`);
      return new Error(`Error fetching humidity for ${city}`);
    }
    return { temp, humidity };
  } catch (error) {
    console.log(`Error fetching temperature for ${city}`);
    return new Error(`Error fetching temperature for ${city}`);
  }
};
// ...
try {
  const weather = await getWeather("Berlin");
} catch (error) {
  console.log("getWeather() failed");
}

```

在上面的代码块中，当我们试图访问`externalTemperatureAPI`和`externalHumidityAPI`时，我们在`console.log`中遇到了两个错误，然后这两个错误被停止并传播了几次。

我们已经讨论了`try...catch`的缺点，即不知道一个函数是否可以通过，因此不必要的检查会污染代码库。这太令人失望了，但是`try...catch`给我们带来了另一个令人毛骨悚然的惊喜。🎃

还记得`known`和`programmer errors`的区别吗？
`try...catch`抓住所有的错误，把它们混合起来。这是很危险的，因为 bug 会被标记为`known errors`，引发意想不到的副作用，使调试原始问题变得更加困难:

```
try{
  // as in the example above, `getWeather` will throw an exception when the 
  weather = await getWeather('Berlin')

  // `programmer error`, the accessed property is not there -> TypeError is thrown
  const userId = weather.notHere.norHere    
  // TypeError: Cannot read properties of undefined (reading 'norHere')
}catch(error){
  // at runtime, the TypeError from `weather.notHereNorHere` is caught here
  // TypeError is now mixed with the `known error`

  // `known error`, retry the request
  weather = await getWeather('Berlin')
}

```

但是不要绝望，让我们看看如何避免混合这两种错误类型！

## `Result`类

当您使用上面讨论的两种错误处理方法中的任何一种时，一个简单的错误都会在原有错误的基础上增加不必要的复杂性。当返回 null 并抛出一个`try...catch`时出现的问题在其他前端语言中也有，如[、Kotlin](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-result/) 、Rust 和 C#。这三种语言使用`Result`类作为一个相当通用的解决方案。

不管执行是成功还是失败，`Result`类将封装给定函数的结果，允许函数调用方将错误作为正常执行流程的一部分来处理，而不是作为异常来处理。

当与 TypeScript 配对时，`Result`类提供了[类型安全](https://blog.logrocket.com/pattern-matching-and-type-safety-in-typescript-1da1231a2e34/)和关于函数可能导致的错误的详细信息。当我们修改函数的错误结果时，`Result`类在我们代码库受影响的地方为我们提供了编译时错误。

让我们回头看看我们的天气例子。我们将使用 Rust 的结果和选项对象的类型脚本实现， [ts-results](https://github.com/vultix/ts-results) :

> 还有其他类似 API 的 TypeScript 包，比如 [NeverThrow](https://github.com/supermacro/neverthrow) ，所以你可以随意使用。

```
import { Ok, Err, Result } from "ts-results";

type Errors = "CANT_FETCH_TEMPERATURE" | "CANT_FETCH_HUMIDITY";

const getWeather = async (
  city: string
): Promise<Result<{ temp: number; humidity: number }, Errors>> => {
  const temp = await externalTemperatureAPI(city);
  if (!temp) return Err("CANT_FETCH_TEMPERATURE");

  const humidity = await externalHumidityAPI(city);
  if (!humidity) return Err("CANT_FETCH_HUMIDITY");

  return Ok({ temp, humidity });
};

// ...

const weatherResult = await getWeather("Berlin"); // weatherResult is fully typed
if (weatherResult.err) console.log(`getWeather() failed: ${weatherResult.val}`);
if (weatherResult.ok) console.log(`Weather is: ${JSON.stringify(weather.val)}`);

```

从函数中添加类型安全的结果，并在代码中对错误处理进行优先级排序，这是对前面示例的改进。然而，我们仍有工作要做。让我们探索一下如何使我们的类型检查详尽无遗。

需要注意的是，偏爱`Result`类并不意味着你不会使用`try...catch`结构。`try...catch`使用外部包时，仍然需要结构。

如果您认为`Result`类值得关注，您可以尝试将这些接触点封装在模块中，并在内部使用`Result`类。

### 添加穷举类型检查

当处理可能返回多个错误的函数时，提供覆盖所有错误情况的类型检查会很有帮助。这样做可以确保函数的调用者能够对错误类型做出动态反应，并确保不会忽略任何错误情况。

我们可以通过一个详尽的开关实现这一点:

```
// Exhaustive switch helper
class UnreachableCaseError extends Error {
  constructor(val: never) {
    super(`Unreachable case: ${val}`);
  }
}

// ...

const weatherResult = getWeather("Berlin");
if (weatherResult.err) {
  // handle errors
  const errValue = weatherResult.val;
  switch (errValue) {
    case "CANT_FETCH_TEMPERATURE":
      console.error("getWeather() failed with: CANT_FETCH_TEMPERATURE");
      break;
    case "CANT_FETCH_HUMIDITY":
      console.error("getWeather() failed with: CANT_FETCH_HUMIDITY");
      break;
    default:
      throw new UnreachableCaseError(errValue); // runtime type check for catching all errors
  }
}

```

使用 exhaustive 开关运行我们的天气示例将在两种情况下提供编译时错误。一种是所有错误情况都没有处理，另一种是原函数中的错误发生变化。

## 摘要

现在，您知道了处理 TypeScript 中常见错误的改进解决方案！知道了错误处理的重要性，我希望您能使用这种方法来获得关于应用程序中任何错误的最具体的信息。

在本教程中，我们讨论了一些广泛使用的方法的缺点，比如返回 null 和`try...catch`方法。最后，我们学习了如何使用 TypeScript 结果类和一个详尽的错误捕获开关。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.