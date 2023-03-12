# 使用 react-testing-library-log rocket 博客进行语义测试

> 原文：<https://blog.logrocket.com/semantic-tests-with-react-testing-library/>

测试代码可能是一个有争议的话题，很大程度上是因为编写测试有多种方法。

没有明确的规则，最终您是决定什么值得测试以及如何测试的负责人。

一个常见的错误是测试实现细节，但也许你已经读过了。

让我后退一步，测试的最终目标是什么？

## 编写用户故事

一个常见的软件抽象是编写用户故事——也就是说，用户在与应用程序交互时可能采取的行动。

假设您要构建一个摄氏到华氏的转换器。一个合理的故事可能是这样的:

“作为一名用户，我希望能够将摄氏温度转换成华氏温度。”

自然，作为一个谨慎的开发人员，您希望断言对于一组给定的数字和输入，转换是有效的(或者对于像“banana”这样的无效输入，转换会失败)。

但是，请注意，测试一个函数是否能够成功地处理从摄氏温度到华氏温度的转换只是事情的一半。

如果您能够执行最昂贵和最相关的计算，但您的最终用户无法访问它，所有的努力都将是徒劳的。

这是为什么呢？

作为一名前端开发人员，你的工作不仅是确保用户得到他们问题的正确答案，还要确保他们可以使用你的应用程序。

因此，您需要评估用户是否按照预期与您的应用程序进行了交互。

在我们的例子中，这意味着在屏幕的某个地方，你会看到这样的文本:“25°C 等于 77°f。”

这是一个相关的测试。您刚刚评估，对于给定的输入，用户在屏幕上满意地得到了正确的答案。

### 故事(大多)不在乎细节

这里的主要要点是，用户故事不是以您的开发实现为中心的，所以您的测试也不应该如此。

当然，讨论中的场景与应用程序范围的测试(有上下文的东西)有关，而不是基本的库。

如果你的目标是创建一个库，将摄氏温度转换为华氏温度，转换为开尔文温度，那么一旦你脱离了上下文，就可以测试细节。

既然我们知道测试应该类似于用户故事，那么您可以预测语义来自哪里。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

最终，你的测试应该有清晰的语义，这样你就可以用简单的英语阅读它们——就像你描述用户故事一样。

我们将看到如何利用 react-testing-library API 来编写有意义的语义测试。

## 案例研究:温度转换器

让我们深入探讨温度转换器的应用。

我们假设一个有能力的项目经理听到了他们客户(可能是任何一个最近搬到美国的非美国人)的抱怨，并提出了以下要求:

*   作为用户，我希望能够将摄氏温度转换为华氏温度
*   作为用户，我希望能够将华氏温度转换为摄氏温度
*   作为一个用户，我想点击一个重置按钮，这样我就可以用最少的努力轻松地转换许多值

除了在编写故事时缺乏创造力的总理，要求是非常简单的。

我们将绘制一个简单的应用程序，做一个好的 ol' [冒烟测试](https://en.wikipedia.org/wiki/Smoke_testing_(software))来检查一切看起来正常，然后应用我们刚刚学到的知识来编写更好的测试。

为我们的示例应用程序考虑下面的 code sandbox:
[https://codesandbox.io/s/temperature-converter-mw7se](https://codesandbox.io/s/temperature-converter-mw7se)

深入研究代码的细节超出了本文的范围(查看"[如何使用 React 钩子重用逻辑](https://rafaelquintanilha.com/how-to-reuse-logic-with-react-hooks/)"了解更多关于如何使用钩子创建 React 应用程序的上下文)。

然而，代码应该非常简单。我们基本上要求用户输入，并允许他们从摄氏温度转换到华氏温度，反之亦然。

然后我们显示结果，一个重置按钮出现。单击该按钮后，输入将被清除并重新获得焦点。

这与我们的用户正在寻找的一致:我们将提高应用程序的可用性，最重要的是，保持其可访问性。

既然我们已经有了一个运行良好的应用程序，让我们成为负责任的开发人员，编写一些测试。

我们将尝试将每个用户故事与一个测试相匹配。通过这样做，我们将会确信每一个需求都被一组支持我们的测试所满足。

考虑一下`App.test.js`的基本框架:

```
import React from "react";
import { cleanup } from "@testing-library/react";

afterEach(cleanup);

test("user is able to convert from celsius to fahrenheit", () => {
  /* story 1 goes here */
});

test("user is able to convert from fahrenheit to celsius", () => {
  /* story 2 goes here */
});

test("user can reset calculation and automatically focus on the input", () => {
  /* story 3 goes here */
});
```

(我们使用 [Jest](https://jestjs.io/) 作为我们的测试程序，但这与文章中提出的要点无关。)

请注意，我们的三个测试非常简单，任何失败都会很快暴露出实际情况。

现在我们将利用 RTL，以一种有意义的方式编写第一个测试:

```
import React from "react";
import App from "./App.js";
import { cleanup, render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

afterEach(cleanup);

test("user is able to convert from celsius to fahrenheit", () => {
  render(<App />);
  const input = screen.getByLabelText("Temperature:");
  userEvent.type(input, "25");
  expect(screen.getByText("25ºC equals to 77ºF")).toBeTruthy();
  userEvent.type(input, "0");
  expect(screen.getByText("0ºC equals to 32ºF")).toBeTruthy();
  userEvent.type(input, "banana");
  expect(screen.queryByTestId("result")).toBeFalsy();
});

/* code goes on */
```

关于依赖关系，有一些事情需要注意:

首先，我们导入有问题的组件`App.js`。

然后，注意我们从 RTL 导入了`render`和`screen`。虽然第一个版本在该库第一次发布时就已经存在了，但是`screen`是在版本 9.4.0 中新增加的[。我们很快就会看到它的主要优点。](https://github.com/testing-library/react-testing-library/releases/tag/v9.4.0)

我们还直接从`@testing-library/user-event`导入了一个新的依赖项`[userEvents](https://github.com/testing-library/user-event)`。这个库将提高我们测试的可读性，并帮助我们实现改进语义的目标。

让我们开始测试吧。如果你习惯于 RTL，你会注意到的第一件事就是`render`不返回任何东西。其实这才是进口`screen`的主要优势。

screen 所做的基本上是公开所有允许您在屏幕中选择元素的查询(因此得名)。

这是一个很好的改变，因为它帮助您避免了用大量的[析构](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)来膨胀测试，当您还不确定使用哪个查询时，这总是很烦人。

此外，代码看起来更干净。(注意:肯特·c·多兹[在这条推文](https://twitter.com/kentcdodds/status/1224790664354779136)中提到的`container`和`rerender`仍有拆分的理由。)

您可能已经编写的与传统测试的另一个不同之处是`userEvent`对象。

该对象提供了一些语义上可以理解的用户交互，并隐藏了实现细节。考虑下面的例子:

```
// Previously
fireEvent.change(input, { target: { value: "25" } });

// With userEvents
userEvent.type(input, "25");
```

我们的代码不仅更短，而且现在更有意义了。

请记住，我们的目标是编写一个尽可能接近简单英语的测试。通过封装实现细节，`userEvent`真正让我们走上了正轨。

如果您有兴趣，请继续查看他们的文档。

一旦我们能够填充输入，我们现在可以断言显示的是正确的文本。

现在我们可以测试一系列其他选项，并确认屏幕中显示的内容是预期的(例如，`banana`这样的无效输入不起作用)。

注意:在一个模块化的应用程序中，转换函数可以被提取到它们自己的文件中，并且有它们自己的测试(有更多的测试场景)。

如果您单独测试该功能，也不需要在用户故事中进行多余的检查(测试是代码，您希望它同样可维护)。

通过一个只有八行代码的测试，我们能够检查我们的第一个场景是否像预期的那样工作。

让我们进入第二个用户故事——从华氏温度转换到摄氏温度(也许是一个纽约人在南美洲的海滩上玩得开心)。

该测试应该与我们的第一个非常相似，只有一个警告:我们需要确保用户选择了正确的选项。

```
test("user is able to convert from fahrenheit to celsius", () => {
  render(<App />);
  const fahrenheitOption = screen.getByLabelText("Fahrenheit to Celsius");
  userEvent.click(fahrenheitOption);
  const input = screen.getByLabelText("Temperature:");
  userEvent.type(input, "77");
  expect(screen.getByText("77ºF equals to 25ºC")).toBeTruthy();
  userEvent.type(input, "32");
  expect(screen.getByText("32ºF equals to 0ºC")).toBeTruthy();
  userEvent.type(input, "banana");
  expect(screen.queryByTestId("result")).toBeFalsy();
});
```

就是这样。通过再次利用`userEvent`，模拟点击事件变得微不足道。

我们的代码可读性很好，并保证反向(F 到 C)如预期的那样工作。

我们的第三个也是最后一个测试略有不同——现在我们的目标是测试用户体验，而不是或计算器是否工作。

我们希望确保我们的应用程序是可访问的，并且用户可以快速测试几个值:

```
test("user can reset calculation and automatically focus on the input", () => {
  render(<App />);
  const input = screen.getByLabelText("Temperature:");
  userEvent.type(input, "25");
  expect(screen.queryByTestId("result")).toBeTruthy();
  const resetButton = screen.getByText("Reset");
  userEvent.click(resetButton);
  expect(screen.queryByTestId("result")).toBeFalsy();
  expect(document.activeElement).toBe(input);
});
```

这就是了。我们基本上做了三个检查:

*   每当用户添加一些输入时，就会显示一个结果(显示的实际消息将从测试中省略，因为这不是这里要检查的内容)
*   当单击重置按钮时，结果不再存在
*   屏幕上的焦点回到输入上

关于 RTL，我最喜欢的一点是它很容易确定一个焦点到底在哪里。

请注意`expect(document.activeElement).toBe(input)`的语义。对我来说这看起来很像普通英语。

仅此而已。我们的三个故事都被覆盖了，项目经理更高兴了，希望我们的测试能在很长一段时间内保持代码的整洁。

## 结论

本文的目的是揭示 react-testing-library 的 API 中最近的修改，并向您展示如何探索它来为您和您的团队编写更好的测试。

当我编写我理解的测试时，我会感到更加自信，因为我不再追逐无意义的度量(例如，代码覆盖率)，而是关注真正重要的东西(例如，我设计的场景是否如预期的那样工作)。

react-testing-library 是朝着正确方向迈出的一大步，主要是如果您有一些酶的背景知识(在这种情况下，您可能希望查看" [react Testing Library 常见场景](https://rafaelquintanilha.com/react-testing-library-common-scenarios/)，"在这里，我探讨了如何在 React 应用程序中处理日常场景)。

它确实有助于测试应用程序应该做什么，而不是如何做。语义很重要。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。