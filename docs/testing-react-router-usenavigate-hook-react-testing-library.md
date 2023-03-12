# 测试 React 路由器使用导航钩子与 React-testing-library-log rocket 博客

> 原文：<https://blog.logrocket.com/testing-react-router-usenavigate-hook-react-testing-library/>

***编者按**:本文最后一次更新于 2022 年 4 月 28 日，删除了对已弃用的* *`useHistory`钩子的引用。*

在版本 6 中， [React Router 引入了一个新的钩子家族](https://blog.logrocket.com/react-router-v6-future-reach-router/),它简化了组件感知路由的过程。在本文中，我们将探索这些挂钩，查看一些代码示例和用例。我们开始吧！

## `useNavigate`

在 React 路由器 v6 中，`useNavigate`钩子取代了`useHistory`钩子。您可以使用`useNavigate`钩子导航到其他页面，如下面的代码块所示:

```
import { useNavigate } from "react-router-dom";

function HomeButton() {
  let navigate = useNavigate();

  function handleClick() {
    navigate("/home");
  }

  return (
    <button type="button" onClick={handleClick}>
      Go home
    </button>
  );
}

```

### 用`jest.mock`测试`useNavigate`挂钩

我对测试`useNavigate`钩子[的最初研究返回了这个 StackOverflow 线程](https://stackoverflow.com/questions/58392815/how-to-mock-usehistory-hook-in-jest)，它提倡使用`[jest.mock](https://jestjs.io/docs/en/jest-object#jestmockmodulename-factory-options)`。让我们试一试:

```
jest.mock('react-router-dom', () => ({
  ...jest.requireActual('react-router-dom'),
  useNavigate: () => (jest.fn())
}));

```

尽管上述方法可行，但我会不惜一切代价避免它，因为我希望我的测试模拟真实世界的使用。将从模拟现实中删除一个重要组件，我可能会错过一些关键的回归。相反，我将主要依靠 React 路由器的`MemoryHistory`。

## 示例应用程序

我已经创建了下面的 CodeSandbox:

它包括一个名为`useStepper`的简单挂钩，允许用户通过几个应用步骤向前和向后导航:

![Usestepper Hook Navig](img/0060e38f85fb0eb5bc0291a6a2003c46.png)

每次向前或向后导航都使用从`useNavigate`返回的`navigate()`函数导航到新 URL 的新组件:

```
export const useStepper = () => {
  const navigate = useNavigate();
  const location = useLocation();

  const nextStepAction = useCallback(() => {
    setCurrentStep(index + 1);
  }, [getCurrentStepIndex]);

  const previousStepAction = useCallback(() => {
    setCurrentStep(index - 1);
  }, [getCurrentStepIndex]);

  useEffect(() => {
    const { path } = currentSteps[currentStep];

    navigate(
      path,
      {state: { previousPath: location.pathname }}
    );
  }, [currentStep, navigate]);

  // rest
};

```

我们可以遵循几个简单的步骤来控制`useNavigate`而不用`jest.mock`。

## 集中历史对象

首先，我将对`history`对象的所有访问集中到位于`[src/history/index.ts](https://codesandbox.io/s/react-ts-unit-test-example-kmubx?file=/src/history/index.ts:0-272)`的一个文件的单个导出中:

```
import { createBrowserHistory, createMemoryHistory } from "history";
import { Urls } from "../types/urls";

const isTest = process.env.NODE_ENV === "test";

export const history = isTest
  ? createMemoryHistory({ initialEntries: ['/'] })
  : createBrowserHistory();

```

使用这种方法，我保证所有的测试和应用程序代码都处理同一个`history`对象。我通常将类似`process.env.NODE_ENV === "test";`的条件排除在应用程序代码之外，但是这次我破例了。

## 创建一个更高阶的组件来包装路由器中的任何被测组件

如前所述，下面的导入将解析为应用程序代码和测试代码现在都引用的中央导出:

```
import { history } from "../history";
import React from "react";
import { render } from "@testing-library/react";
import { Router } from "react-router-dom";

export const renderInRouter = (Comp: React.FC) =>
  render(
    <Router history={history}>
      <Comp />
    </Router>
  );
renderInRouter is a simple function that takes a component and wraps it in a router. The critical thing to note here is the import in line 1:
javascript
import { history } from "../history";

```

## 用`useNavigate`测试组件

随着前面两个步骤的完成，测试使用`useNavigate`的组件将变得容易。
为了建立测试场景，`[useStepper.test.tsx](https://codesandbox.io/s/react-ts-unit-test-example-kmubx?file=/src/hooks/useStepper/useStepper.test.tsx)`测试引用了与应用程序代码相同的`history`对象:

```
import React from "react";
import { history } from "../../history";
import { useStepper } from "./useStepper";
import { Urls } from "../../types/urls";
import { renderInRouter } from "../../tests";
import { renderHook, act } from "@testing-library/react-hooks";
import { ApplicationNavigator } from "../../Containers/Application";
import { screen, fireEvent } from "@testing-library/react";
import { Router } from "react-router-dom";

const render = () => renderInRouter(ApplicationNavigator);

describe("useStepper", async () => {
  beforeEach(() => {
    history.push(Urls.Home);
  });

  it("should go forward and should go back", async () => {
    render();

    const back = screen.getByText("BACK");
    const next = screen.getByText("NEXT");

    fireEvent.click(next);

    expect(history.location.pathname).toBe(Urls.About);

    fireEvent.click(next);

    expect(history.location.pathname).toBe(Urls.Start);

    fireEvent.click(back);

    expect(history.location.pathname).toBe(Urls.About);
  });
});

```

`render`函数将调用`renderInRouter`高阶组件，并为组件提供测试路由。所有代码引用的单个历史对象以与应用程序代码相同的方式导入:

```
import { history } from "../../history";

```

## 测试挂钩

[react-Hooks-testing-library](https://github.com/testing-library/react-hooks-testing-library)允许我们用`renderHook`函数单独测试钩子:

```
javascript

  it("should provide forward and backwards navigation", async () => {
    const { result } = renderHook(() => useStepper(), {
      wrapper: ({ children }) => (
        <>
          <Router history={history}>
            <ApplicationNavigator />
            {children}
          </Router>
        </>
      )
    });

    expect(result.current.currentStep).toBe(0);
    expect(result.current.cantGoBack).toBe(true);
    expect(result.current.cantProceed).toBe(false);

    await act(async () => {
      result.current.nextStepAction();
    });

    expect(result.current.currentStep).toBe(1);
    expect(result.current.cantGoBack).toBe(false);
    expect(result.current.cantProceed).toBe(false);

    expect(history.location.pathname).toBe(Urls.About);

    await act(async () => {
      result.current.previousStepAction();
    });

    expect(history.location.pathname).toBe(Urls.Home);
  });

```

现在可以测试`useStepper`钩子，而不需要从特定的组件调用。
需要注意的重要一点是[包装器](https://github.com/testing-library/react-hooks-testing-library/blob/6f735133e3a4fb6af6b641a7e25b2cb950a0b8b7/docs/usage/advanced-hooks.md)选项被提供给 [`renderHook`函数](https://github.com/testing-library/react-hooks-testing-library/blob/7c98bafd2c98ea604eb2c7e64c0872ecb67fcafe/README.md)。我们需要将钩子包装在一个`Router`中，给`useStepper`钩子正确的上下文以允许测试流动。

## 结论

以我的经验来看，`jest.mock`是应该避免的，因为它给了测试一个错误的错觉。使用本文中概述的使用`MemoryHistory`的方法，您不需要这样做。在本文中，我们探索了测试 React Router v6 中引入的`useNavigate`挂钩。我希望你喜欢这篇文章，如果你有任何问题，欢迎留下评论。测试愉快！

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