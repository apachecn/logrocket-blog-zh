# 使用自定义 React 挂钩构建可调整大小的 React 组件

> 原文：<https://blog.logrocket.com/building-resizable-react-component-using-custom-react-hooks/>

## 定制挂钩

我们将讨论一些自定义 React 挂钩的很酷的例子，并使用它们构建一个可调整大小的 React 组件。如果你不熟悉钩子的概念，请在继续本文之前回顾一下[钩子的基本定义](https://reactjs.org/docs/hooks-intro.html)和[规则](https://reactjs.org/docs/hooks-rules.html)。

钩子让我们以新的方式思考。现在，我们可以将 React 组件逻辑中与特定上下文相关的特定部分(如获取数据或页面事件)分组到自定义 React 挂钩中。这不需要重构需要彼此共享状态的组件层次结构。此外，我们不需要在生命周期方法中重复和使用不相关的逻辑。

## 可调整大小的反应组件示例

我们将一起构建一个简单的 React 组件，它使用来自`[beautiful-react-hooks](https://github.com/beautifulinteractions/beautiful-react-hooks)`库的一些定制钩子。我们将分别覆盖这些定制钩子，最后将所有东西粘合在一起，构建我们的组件。作为一个学习练习，我们也将使用 [React 类](https://github.com/beautifulinteractions/beautiful-react-hooks)和[生命周期方法](https://github.com/beautifulinteractions/beautiful-react-hooks)来构建这些例子，看看我们可以通过使用钩子获得什么好处。

例如，如果列表的总宽度大于当前窗口的宽度，这个组件将显示一个被截断的元素的动态列表。万一列表被截断，我们想向用户显示列表中还有多少剩余的条目。最终结果可能如下所示:

![Image Source: Assets in https://picturepan2.github.io/spectre/](img/f72052b9ef7da6d30224e64f38002acb.png)

Image Source: Assets in [https://picturepan2.github.io/spectre/](https://picturepan2.github.io/spectre/)

让我们开始吧。

## useGlobalEvent 和 useWindowResize

为了构建我们的组件，我们需要一种在`[global window object](https://developer.mozilla.org/en-US/docs/Web/API/Window#Event_handlers)`的上下文中监听`[resize event](https://developer.mozilla.org/en-US/docs/Web/API/Window/resize_event)`并对其做出反应的机制。事实证明，有一个非常有用的定制钩子叫做`[useGlobalEvent](https://github.com/beautifulinteractions/beautiful-react-hooks/blob/master/docs/useGlobalEvent.md)`，它可以帮助我们。您只需传递一个事件的名称，钩子就会在窗口对象上为该事件添加一个监听器。它为它返回一个处理程序设置器(在下面的例子中是`onWindowResize`，这个设置器会被立即调用。

请记住，该处理程序不应该异步运行，它不会导致组件重新呈现。我们通过使用`[useState](https://reactjs.org/docs/hooks-state.html)`在组件中设置新的状态来确保组件响应`resize`的变化。这样，作为钩子引用的处理程序设置器将被再次调用，并带有一个新的`windowWidth`状态。

在组件被卸载后，我们需要通过移除附加的事件侦听器来进行清理。但这是为什么呢？

请记住，在由`setWindowWidth`和 new `windowWidth`导致的每次重新渲染之后，我们将再次调用我们的钩子。这将导致对`resize event`的`n`数量的绑定，这将导致我们的应用程序出现内存泄漏。`useGlobalEvent`通过移除 new re-renders 的事件处理程序来为我们处理这个问题。

下面是[使用`useGlobalEvent`钩子的一个例子](https://github.com/beautifulinteractions/beautiful-react-hooks/blob/master/docs/useGlobalEvent.md):

```
// global dependencies
import * as React from "react";
import { useGlobalEvent } from "beautiful-react-hooks";

// initalization
const { useState } = React;

const App = () => {
  const [windowWidth, setWindowWidth] = useState(window.innerWidth);
  const onWindowResize = useGlobalEvent("resize");

  onWindowResize((event: React.SyntheticEvent) => {
    setWindowWidth(window.innerWidth);
  });

  return (
    <div className="toast toast-primary">
      Current window width: {windowWidth}
    </div>
  );
};

export default App;
```

下面是另一个定制钩子`[useWindowResize](https://github.com/beautifulinteractions/beautiful-react-hooks/blob/master/docs/useWindowResize.md)`的例子，它构建在`useGlobalEvent`之上，使得组件更加简单:

```
// global dependencies
import * as React from "react";
import { useWindowResize } from "beautiful-react-hooks";

// initalization
const { useState } = React;

const App = () => {
  const [windowWidth, setWindowWidth] = useState(window.innerWidth);

  useWindowResize((event: React.SyntheticEvent) => {
    setWindowWidth(window.innerWidth);
  });

  return (
    <div className="toast toast-primary">
      Current window width: {windowWidth}
    </div>
  );
};

export default App;
```

下面是使用 class 和 React 生命周期方法的例子。这是一个简单的例子，但是您可以看到上面的自定义 React 钩子在下一个组件重新呈现之前自动进行清理。这是我们需要手动在 React 生命周期方法中涵盖的内容:

```
// global dependencies
import * as React from "react";

// interface
interface IProps {}

interface IState {
  width?: number;
}

class App extends React.Component<IProps, IState> {
  constructor(props: any) {
    super(props);
    this.state = {
      width: window.innerWidth
    };
  }

  // local methods
  setWindowWidth = () => {
    this.setState({
      width: window.innerWidth
    });
  };

  // lifecycle methods
  componentDidMount() {
    window.addEventListener("resize", this.setWindowWidth);
  }

  componentWillUnmount() {
    window.removeEventListener("resize", this.setWindowWidth);
  }

  render() {
    return (
      <div className="toast toast-primary">
        Current window width: {this.state.width}
      </div>
    );
  }
}

export default App;
```

到目前为止，我们已经设法为`resize events`设置了一个处理程序，这将帮助我们构建我们的组件。但是首先，对于上面的例子，我们有没有可以做的优化？

## useDebouncedFn 和 useThrottleFn

你可能已经注意到，在上面调整窗口大小的例子中，我们为每个在[事件循环](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)中处理的`resize`事件调用`setWindowWidth`。我们可能需要减少处理`setWindowWidth`的频率，这样可以获得一些渲染性能。我们可以在`[useDebouncedFn](https://github.com/beautifulinteractions/beautiful-react-hooks/blob/master/docs/useDebouncedFn.md)`和`[useThrottleFn](https://github.com/beautifulinteractions/beautiful-react-hooks/blob/master/docs/useThrottledFn.md)`的帮助下做到这一点，以随着时间的推移延迟`setWindowWidth`功能的执行。

## 去抖动

当谈到[去抖一个函数](https://css-tricks.com/debouncing-throttling-explained-examples/)的执行时，我们试图将多个函数调用批处理成一个来提高性能。这样，当用户改变窗口的宽度时，我们确保每隔 **0.25 秒**将所有对`setWindowWidth`的调用批处理成一个调用。如果`resize events`快速发生，则发生去抖；否则不(检查下面沙盒中的`console.log`值，并与下面的`throttle`示例进行比较)。

以下是使用此自定义挂钩的示例:

```
// global dependencies
import * as React from "react";
import { useGlobalEvent, useDebouncedFn } from "beautiful-react-hooks";

// initalization
const { useState } = React;

const App = () => {
  const [windowWidth, setWindowWidth] = useState(window.innerWidth);
  const onWindowResize = useGlobalEvent("resize");

  const onWindowResizeHandler = useDebouncedFn(() => {
    console.log("I am debouncing", windowWidth);
    setWindowWidth(window.innerWidth);
  }, 250);

  onWindowResize(onWindowResizeHandler);

  return (
    <div className="toast toast-primary">
      Current window width: {windowWidth}
    </div>
  );
};

export default App;
```

## 节流

## [节流概念](https://css-tricks.com/debouncing-throttling-explained-examples/)，虽然与`debounce`相似，但也有不同之处。例如对于`throttle`，你不允许每 0.25 秒执行`setWindowWidth`一次以上。但是，保证每 0.25 秒定期执行一次函数。

通过检查以下示例中的`console.log`来检查该场景:

最后，让我们看看`debouncing`在使用生命周期方法的背景下。我们将使用`[lodash.debounce](https://www.npmjs.com/package/lodash.debounce)`。我们所需要做的就是在`[componentDidMount](https://reactjs.org/docs/react-component.html#componentdidmount)`收听`resize event`时`debounce`我们对`setWindowWidth`的呼叫:

```
// global dependencies
import * as React from "react";
import { useGlobalEvent, useThrottledFn } from "beautiful-react-hooks";

// initalization
const { useState } = React;

const App = () => {
  const [windowWidth, setWindowWidth] = useState(window.innerWidth);
  const onWindowResize = useGlobalEvent("resize");

  const onWindowResizeHandler = useThrottledFn(() => {
    console.log("I am throttling", windowWidth);
    setWindowWidth(window.innerWidth);
  }, 250);

  onWindowResize(onWindowResizeHandler);

  return (
    <div className="toast toast-primary">
      Current window width: {windowWidth}
    </div>
  );
};

export default App;
```

以下是完整的示例:

```
import _debounce from "lodash.debounce";

componentDidMount() {
    window.addEventListener(
      "resize",
      _debounce(() => {
        this.setWindowWidth();
      }, 250)
    );
  }
```

决赛成绩

```
// global dependencies
import * as React from "react";
import _debounce from "lodash.debounce";

// interface
interface IProps {}

interface IState {
  width?: number;
}

class App extends React.Component<IProps, IState> {
  constructor(props: any) {
    super(props);
    this.state = {
      width: window.innerWidth
    };
  }

  // local methods
  setWindowWidth = () => {
    this.setState({
      width: window.innerWidth
    });
  };

  // lifecycle methods
  componentDidMount() {
    window.addEventListener(
      "resize",
      _debounce(() => {
        this.setWindowWidth();
      }, 250)
    );
  }

  componentWillUnmount() {
    window.removeEventListener("resize", this.setWindowWidth);
  }

  render() {
    return (
      <div className="toast toast-primary">
        Current window width: {this.state.width}
      </div>
    );
  }
}

export default App;
```

## 到目前为止，我们已经设置了一个去抖处理器来监听`resize events`并设置`windowWidth`状态。现在，我们将把所有东西放在一起，构建我们在文章开头描述的可调整大小的 React 组件。需要注意一些事情:

## 我们想要显示的项目数量是动态的，这意味着它在每次初始渲染时都是不同的。这将要求我们在触发去抖动`resize events`后，在一行中显示一定数量的项目

我们利用了一些已经集成到 [React 库(来自 16.8)](https://reactjs.org/docs/hooks-effect.html) 中的钩子。你可能已经很熟悉它们了，但是这里有一个来自[官方文件](https://reactjs.org/docs/hooks-reference.html)的简短描述:

*   `[useState](https://reactjs.org/docs/hooks-reference.html#usestate)`返回一个有状态的值，以及一个更新它的函数
*   接受包含命令性的、可能有效的代码的函数
    *   [`useRef`](https://reactjs.org/docs/hooks-reference.html#useeffect) 返回一个可变的 ref 对象，其`.current`属性被初始化为传递的参数(`initialValue` )
    *   我们使用模拟的`dynamicData`及其总数来模拟 API 调用的行为:
    *   我们构建了一个`handleView`函数来计算可能显示的项目总数。如果它们的总宽度超过了`window width`，我们会在条目列表中添加一个新元素，显示隐藏了多少条目。如果没有，我们只返回项目列表。这个帮手的想法是看了[这篇文章](https://medium.com/hootsuite-engineering/resizing-react-components-6f911ba39b59)后形成的。请务必从另一个角度来看:
*   最终的代码看起来像这样:

```
// helpers
const integerGenerator = (n: number) => Math.ceil(Math.random() * n);

// faking a dynamic data count which in real life
// scenario would come from an api endpoint
const dynamicDataCount = integerGenerator(100);

// data mocks
const mockedData =  () => {
  const data = [];
  for (let i = 0; i < dynamicDataCount; i++) {
    const image : any = (
      <figure className="avatar mr-2" data-initial="...">
        <img src="https://picturepan2.github.io/spectre/img/avatar-1.png" alt="YZ" />
      </figure>
    );
    data.push(image);
  };
  return data;
};

// this would generate an array of mockedData
// elements with a length of random dynamicDataCount
mockedData();
```

*   We built a `handleView` function to calculate the total number of items that are possible to be shown. If their total width exceeds `window width`, we attach a new element to the list of items which shows how many items are hidden from view. If not, we just return the list of items. The idea of this helper was formed after reading [this article](https://medium.com/hootsuite-engineering/resizing-react-components-6f911ba39b59). Make sure to check it out for another perspective:

```
const handleView = (items: Array<Element>) => {  
    // 8 is the value of margin right applied to image elements (8px)
    var maxItemsToShow = Math.floor(windowWidth / (elementWidth + 8));

    // return current view if total number of items is less than maximum possible
    // number of items that can be shown based on the current window width
    if (items.length <= maxItemsToShow) {
      return items;
    }

    // if not, we need a new element which shows how many more items are in the list that are now shown
    const moreDataPlaceholder = 1;
    const numberOfRemainingItems = items.length - maxItemsToShow + moreDataPlaceholder;
    const truncatedItems = items.slice(0, maxItemsToShow - moreDataPlaceholder);
    const displayNumberHtml : any = (
      <figure className="avatar badge" data-badge={numberOfRemainingItems} data-initial="..." />
    );

    truncatedItems.push(displayNumberHtml);
    return truncatedItems;
  }
```

现在是时候看看这个例子了，使用类和生命周期方法。乍一看，你会发现像`componentDidMount`这样的生命周期方法变得有点复杂。这是因为类组件的逻辑是关于在组件生命周期的不同阶段对副作用管理进行分组，而不是基于单个的效果(像设置`window width`和单个的`element width`):

```
// global dependencies
import * as React from "react";
import { useGlobalEvent, useDebouncedFn } from "beautiful-react-hooks";

// initalization
const { useState, useRef, useEffect } = React;

// helpers
const integerGenerator = (n: number) => Math.ceil(Math.random() * n);

// faking a dynamic data count which in real life 
// scenario would come from an api endpoint
const dynamicDataCount = integerGenerator(100);

// data mocks
const mockedData =  (ref: any) => {
  const data = [];
  for (let i = 0; i < dynamicDataCount; i++) {
    const image : any = (
      <figure ref={ref} className="avatar mr-2" data-initial="...">
        <img src="https://picturepan2.github.io/spectre/img/avatar-1.png" alt="YZ" />
      </figure>
    );
    data.push(image);
  };
  return data;
};

const App = () => {
  // component initialization
  const ref = useRef<HTMLInputElement>(null);
  const [windowWidth, setWindowWidth] = useState(window.innerWidth);
  const [elementWidth, setElementWidth] = useState(0);
  const onWindowResize = useGlobalEvent("resize");

  // handler for initially calculating individual elements width 
  useEffect(() => {
    const width = ref.current ? ref.current.offsetWidth : 0;
    setElementWidth(width);
  }, []);

  // handler for calculating window width on resize event
  const onWindowResizeHandler = useDebouncedFn(() => {
    setWindowWidth(window.innerWidth);
  }, 250);
  onWindowResize(onWindowResizeHandler);

  const handleView = (items: Array<Element>) => {  
    // 8 is the value of margin right applied to image elements (8px)
    var maxItemsToShow = Math.floor(windowWidth / (elementWidth + 8));

    // return current view if total number of items is less than maximum possible
    // number of items that can be shown based on the current window width
    if (items.length <= maxItemsToShow) {
      return items;
    }

    // if not, we need a new element which shows how many more items are in the list that are now shown
    const moreDataPlaceholder = 1;
    const numberOfRemainingItems = items.length - maxItemsToShow + moreDataPlaceholder;
    const truncatedItems = items.slice(0, maxItemsToShow - moreDataPlaceholder);
    const displayNumberHtml : any = (
      <figure className="avatar badge" data-badge={numberOfRemainingItems} data-initial="..." />
    );

    truncatedItems.push(displayNumberHtml);
    return truncatedItems;
  }

  return (
    <div className="toast toast-primary px-0 mx-0">
      {handleView(mockedData(ref)).map((element : Element) => element)}
    </div>
  );
};

export default App;
```

结论

让我们一起回顾一下我们所学的内容:

```
// global dependencies
import * as React from "react";
import _debounce from "lodash.debounce";

// helpers
const integerGenerator = (n: number) => Math.ceil(Math.random() * n);

// faking a dynamic data count which in real life
// scenario would come from an api endpoint
const dynamicDataCount = integerGenerator(100);

// data mocks
const mockedData = (ref: any) => {
  const data = [];
  for (let i = 0; i < dynamicDataCount; i++) {
    const image: any = (
      <figure ref={ref} className="avatar mr-2" data-initial="...">
        <img
          src="https://picturepan2.github.io/spectre/img/avatar-1.png"
          alt="YZ"
        />
      </figure>
    );
    data.push(image);
  }
  return data;
};

// interface
interface IProps {}

interface IState {
  windowWidth?: number;
  elementWidth?: number;
}

class App extends React.Component<IProps, IState> {
  private ref = React.createRef<HTMLDivElement>();

  constructor(props: any) {
    super(props);
    this.state = {
      windowWidth: window.innerWidth,
      elementWidth: 0
    };
  }

  // local methods
  setWindowWidth = () => {
    this.setState({
      windowWidth: window.innerWidth
    });
  };

  setElementWidth = (elementWidth: number) => {
    this.setState({
      elementWidth: elementWidth
    });
  };

  // lifecycle methods
  componentDidMount() {
    const elementWidth = this.ref.current ? this.ref.current.offsetWidth : 0;
    this.setElementWidth(elementWidth);
    window.addEventListener(
      "resize",
      _debounce(() => {
        this.setWindowWidth();
      }, 250)
    );
  }

  componentWillUnmount() {
    window.removeEventListener("resize", this.setWindowWidth);
  }

  handleView = (items: Array<Element>) => {
    // 8 is the value of margin right applied to image elements (8px)
    let maxItemsToShow = 0;

    if (this.state.windowWidth && this.state.elementWidth) {
      maxItemsToShow = Math.floor(
        this.state.windowWidth / (this.state.elementWidth + 8)
      );
    }

    // return current view if total number of items is less than maximum possible
    // number of items that can be shown based on the current window width
    if (items.length <= maxItemsToShow) {
      return items;
    }

    // if not, we need a new element which shows how many more items are in the list that are now shown
    const moreDataPlaceholder = 1;
    const numberOfRemainingItems =
      items.length - maxItemsToShow + moreDataPlaceholder;
    const truncatedItems = items.slice(0, maxItemsToShow - moreDataPlaceholder);
    const displayNumberHtml: any = (
      <figure
        className="avatar badge"
        data-badge={numberOfRemainingItems}
        data-initial="..."
      />
    );

    truncatedItems.push(displayNumberHtml);
    return truncatedItems;
  };

  render() {
    return (
      <div className="toast toast-primary px-0 mx-0">
        {this.handleView(mockedData(this.ref)).map(
          (element: Element) => element
        )}
      </div>
    );
  }
}

export default App;
```

我们一起构建了一个简单的 React 组件，它可以适应不同的窗口宽度大小，并显示动态数量的项目。我们还学习了如何通过延迟对事件处理程序的函数调用来优化这个过程。

## 实际上，我们看到了钩子如何使构建组件变得更容易，以及定制钩子如何使构建更加流畅。但是改变方向并决定使用钩子编写或重写组件并不是很简单。在做出任何决定之前，请务必阅读 [React 的官方改编指南](https://beautifulinteractions.github.io/beautiful-react-hooks/)。记住，要多试验这个新概念，以便更好地了解它的优点和缺点。

参考

*   [https://reactjs.org/docs/hooks-intro.html](https://reactjs.org/docs/hooks-intro.html)
*   [https://github . com/beautiful interactions/beautiful-react-hooks](https://github.com/beautifulinteractions/beautiful-react-hooks)

## [https://CSS-tricks . com/de bounding-throttling-explained-examples/](https://css-tricks.com/debouncing-throttling-explained-examples/)

[https://www . plural sight . com/guides/re-render-react-component-on-window-resize](https://www.pluralsight.com/guides/re-render-react-component-on-window-resize)

[https://medium . com/hootsuite-engineering/resizing-react-components-6f 911 ba 39 b 59](https://medium.com/hootsuite-engineering/resizing-react-components-6f911ba39b59)

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

.

## LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

No more noisy alerting. Smart error tracking lets you triage and categorize issues, then learns from this. Get notified of impactful user issues, not false positives. Less alerts, way more useful signal.

The LogRocket Redux middleware package adds an extra layer of visibility into your user sessions. LogRocket logs all actions and state from your Redux stores.

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).