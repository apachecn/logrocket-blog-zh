# 带有 React 的 TDD:一种带有示例的低摩擦方法

> 原文：<https://blog.logrocket.com/tdd-with-react-low-friction-approach/>

> 先写测试。–碧昂斯(转述)

如果你像 Beyoncé一样，你知道只有在你有了一套可靠的测试之后，才应该编写你的 UI 组件代码。

理想情况下。

你知道吗，如果那样的话。你尽力了。

问题是，测试驱动开发(TDD)的正统观点认为，您可能不应该编写依赖于特定实现策略的测试。但是对于 React 开发人员来说，流行的工具如 Enzyme 和 React Test Renderer 使得编写与源代码细节紧密耦合的测试变得非常容易。

不要误解我——这些都是很棒的工具，但是它们的 API 使得编写一个即使组件名或 DOM 选择器改变也不会中断的单元测试变得几乎不可能。

## 什么是好的测试？

你不会发现任何关于好的单元测试实践的思考片段和书籍的短缺。他们说，如果你想写一个好的测试，瞄准公共接口；所以如果是一个类，测试那个类的公共方法，而不是私有方法。当测试传统的面向对象程序时，这种传统的智慧为我们提供了很好的服务。

然而，React 应用程序不是传统的面向对象程序。它们的公共接口不应该被认为是对象之间的消息。相反，将 React 组件的公共接口视为用户可以看到或与之交互的部分。

这是 UI 组件库的最终目的:它管理用户和应用程序之间的消息。所以，如果你想为你的 React 代码编写一个好的单元测试，就要瞄准公共接口——用户可以感知并与之交互的部分。

用户可以看到什么文本？当它们与一个元素相互作用时会发生什么变化？

这些是组件单元测试应该回答的问题。碰巧的是，这些类型的问题与产品涉众倾向于为用户界面编写的需求类型非常相似。

考虑一个简单天气应用程序的以下规范:

*   该页面应该有一个按钮，文本“获取天气”
*   当用户点击按钮时，应该会出现纽约的当前天气

像一个好的 TDD'er 一样，你开始为这些标准中的每一个写一个测试。让我们看看一种使用流行的[酶](https://enzymejs.github.io/enzyme/)库的可能方法:

```
import { mount } from "enzyme";

describe("App", () => {
  it("should render a button", () => {
    const element = mount(<App />);
    const button = element.find("button");
    expect(button).toHaveLength(1);
  });

  it("should fetch a weather forecast upon tapping the button", async () => {
    const element = mount(<App />);
    const button = element.find("button");
    button.simulate("click");
    await wait(2000); // homespun utility function that waits X milliseconds before resolving
    expect(element.text()).toContain("New York");
    expect(element.text()).toContain("°");
  });
});
```

这是一个非常有用的测试，但它很脆弱。这个测试依赖于一个交互的`<button>`元素。

我们都知道，当产品需求说“按钮”时，他们指的是“看起来和行为都像按钮的东西”

那么，如果在实现过程中，我们决定最好使用`<div tabindex="0"role="button">`或`<a href="/forecast-for-new-york">`呢？

如果请求时间超过 2s 怎么办？当然，我们需要更新测试。这不是世界末日，但也不是 TDD 理想的完成方式。

这个特性如此简单，以至于这些边缘情况肯定属于“吹毛求疵”的范畴，但是不难想象这些区别在哪里变得更加重要。`button`选择器是一个“私有”接口，所以用户不会察觉也不会关心用什么 HTML 标签来表达一个元素。

他们只想找到“天气预报”然后得到该死的天气！如果只需要 100 毫秒，他们也不会等 2 秒。

记住:一个组件真正的公共接口是能够被用户感知并与之交互的接口，而不是像选择器或组件名这样的抽象概念。

因此，如果酶和类似的工具如 [React 测试渲染器](https://blog.logrocket.com/tdd-with-react-test-renderer/)不能解决问题，那么什么能呢？

## TDD 的 React 测试库

[当然是反应测试库(RTL)](https://testing-library.com/docs/react-testing-library/intro) ！最初于 2018 年初发布，这个相对较新的产品正在 React 社区中获得大量支持，这是有充分理由的。

React 测试库的 API 为 Enzyme 等人的提供了一个令人耳目一新的、以用户为中心的[替代方案。让我们编写和以前一样的两个测试，这次使用 RTL:](https://blog.logrocket.com/top-testing-libraries-for-react-in-2021/)

```
import { render, fireEvent, screen } from "@testing-library/react";

describe("App", () => {
  it("should render a button", () => {
    render(<App />);
    const button = screen.queryByText("Get weather");
    expect(button).not.toBeNull();
  });

  it("should fetch a weather forecast upon tapping the button", async () => {
    render(<App />);
    const button = screen.queryByText("Get weather");
    fireEvent.click(button);
    await screen.findByText("New York", { exact: false });
    expect(screen.queryByText("°", { exact: false })).not.toBeNull();
  });
});
```

RTL 的空气污染指数很简单。我们通过标签、文本、aria-label 或其他面向用户的属性来查找元素。选择者完全靠边站。

用`find("DestinysChildImg")`出，用`findByAltText("Destiny's Child performing at their reunion tour in 2022")`入。

我还想提醒大家注意 RTL 给我们的处理异步功能的几个工具之一:

```
await screen.findByText("New York", { exact: false });
```

这和你想象的完全一样。

`findByText`每隔一段时间查询 DOM，直到它:1。)超时，或 2。)查找元素。在现实生活中，异步服务的响应时间会有很大的不同，这取决于服务的性能以及客户机设备的能力和带宽。

然而，JavaScript 单元测试经常通过模仿或最佳猜测等待函数来抑制异步效应。RTL 的异步工具解决了在测试套件中表达异步效果的问题。换句话说，它等待页面改变，就像你的用户一样。

这是 2020 年 JavaScript 测试套件所能达到的最接近简单英语的程度。使用 RTL 将产品需求转化为单元测试是如此直观，这使得它成为 TDD 实践者的理想选择。

所以，我们已经写好了测试。让我们结束循环，写一些源代码！

首先，我们将进行第一次测试:

```
import React from "react";

const App = () => {
  return (
    <div>
      Get weather
    </div>
  );
};

export default App;
```

然后，我们将通过引入交互元素进行第二次测试:

```
import React, { useState } from "react";

const App = () => {
  const [weather, updateWeather] = useState(null);

  const fetchAndUpdateWeather = async () => {
    const response = await fetch("https://wttr.in/New York?format=3");
    const body = await response.text();
    updateWeather(body);
  };

  return (
    <div>
      <div>{weather}</div>
      <div>
        <button onClick={fetchAndUpdateWeather}>Get weather</button>
      </div>
    </div>
  );
};
export default App;
```

我不在乎一个应用程序有多小，我永远不会厌倦看到测试第一次变成绿色。无比幸福。

我已经将测试和源代码整合到一个 CodeSandbox 中，供您参考:

结论

## 在 React Testing Library 中，您可以获得一个工具来实现真正的测试驱动开发。该 API 几乎强迫您按最终用户可感知的项目进行查询，异步效果得到了优雅的处理，同时接近真实世界的用户行为。

您终于可以先编写 React 单元测试，而不必担心源代码会是什么样子。

“不可替代。”–碧昂斯(但这次是真的)

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

## 调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).