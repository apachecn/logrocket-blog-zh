# 用 React Hooks 开发响应式布局

> 原文：<https://blog.logrocket.com/developing-responsive-layouts-with-react-hooks/>

CSS 是创建响应性网站和应用程序的完美工具，这不会很快改变。但是，有时在 React 应用程序中，您需要根据屏幕大小有条件地呈现不同的组件。

如果我们可以在 React 代码中创建这些响应性的布局，而不是必须使用 CSS 和媒体查询，这不是很好吗？让我们快速地看一下这样一个简单的实现，以确切地理解我的意思:

```
const MyComponent = () => {
  // The current width of the viewport
  const width = window.innerWidth;
  // The width below which the mobile view should be rendered
  const breakpoint = 620;

  /* If the viewport is more narrow than the breakpoint render the
     mobile component, else render the desktop component */
  return width < breakpoint ? <MobileComponent /> : <DesktopComponent />;
}
```

这个简单的解决方案肯定会奏效。根据用户设备的窗口宽度，我们呈现桌面或移动视图。但是有一个大问题，当调整窗口大小时，宽度值没有更新，错误的组件可能被渲染！

我们将使用 React 钩子创建一个优雅的，更重要的是，可重用的解决方案，来解决在 React 中创建响应性布局的问题。如果您还没有广泛地使用 React 钩子，那么这应该是钩子所能提供的灵活性和强大功能的一个很好的介绍和演示。

## 使用钩子的初始实现

上面例子的问题是，当窗口调整大小时，`width`的值没有更新。为了解决这个问题，我们可以在 React 状态下跟踪`width`,并使用`useEffect`钩子监听窗口宽度的变化:

```
const MyComponent = () => {
  // Declare a new state variable with the "useState" Hook
  const [width, setWidth] = React.useState(window.innerWidth);
  const breakpoint = 620;

  React.useEffect(() => {
    /* Inside of a "useEffect" hook add an event listener that updates
       the "width" state variable when the window size changes */
    window.addEventListener("resize", () => setWidth(window.innerWidth));

    /* passing an empty array as the dependencies of the effect will cause this
       effect to only run when the component mounts, and not each time it updates.
       We only want the listener to be added once */
  }, []);

  return width < breakpoint ? <MobileComponent /> : <DesktopComponent />;
}
```

现在，每当调整窗口大小时,`width`状态变量被更新以等于新的视窗宽度，我们的组件将重新渲染以显示正确的组件。到目前为止一切顺利！

不过，我们的代码仍然存在一个小问题。我们正在添加一个事件监听器，但是当不再需要它的时候，我们从来不通过移除它来清理自己。目前，当这个组件被卸载时,“调整大小”事件监听器将停留在内存中，在调整窗口大小时继续被调用，这可能会导致问题。在老式的 React 中，你可以在一个`componentWillUnmount`生命周期事件中移除事件监听器，但是使用`useEffect`钩子，我们需要做的就是从我们的`useEffect`返回一个清理函数。

```
const MyComponent = () => {
  const [width, setWidth] = React.useState(window.innerWidth);
  const breakpoint = 620;

  React.useEffect(() => {
    const handleWindowResize = () => setWidth(window.innerWidth)
    window.addEventListener("resize", handleWindowResize);

    // Return a function from the effect that removes the event listener
    return () => window.removeEventListener("resize", handleWindowResize);
  }, []);

  return width < breakpoint ? <MobileComponent /> : <DesktopComponent />;
}
```

现在看起来不错，我们的组件监听窗口大小调整事件，并将根据视窗宽度呈现适当的内容。它还通过在卸载时删除不再需要的事件侦听器来进行清理。

对于单个组件来说，这是一个很好的实现，但是我们很可能希望在应用程序的其他地方也使用这个功能，我们当然不希望每次都必须一遍又一遍地重写这个逻辑！

## 通过自定义挂钩使逻辑可重用

定制的 React 钩子是一个很好的工具，我们可以用它将组件逻辑提取到容易重用的函数中。让我们现在这样做，并使用我们上面写的窗口大小调整逻辑来创建一个可重用的`useViewport`钩子:

```
const useViewport = () => {
  const [width, setWidth] = React.useState(window.innerWidth);

  React.useEffect(() => {
    const handleWindowResize = () => setWidth(window.innerWidth);
    window.addEventListener("resize", handleWindowResize);
    return () => window.removeEventListener("resize", handleWindowResize);
  }, []);

  // Return the width so we can use it in our components
  return { width };
}
```

您可能已经注意到，上面的代码与我们之前编写的代码几乎相同，我们只是将逻辑提取到它自己的函数中，现在我们可以重用它了。钩子只是由其他钩子组成的函数，比如`useEffect`、`useState`，或者任何其他你自己编写的自定义钩子。

现在，我们可以在组件中使用新编写的钩子，代码看起来更加简洁优雅。

```
const MyComponent = () => {
  const { width } = useViewport();
  const breakpoint = 620;

  return width < breakpoint ? <MobileComponent /> : <DesktopComponent />;
}
```

我们不仅可以在这里使用`useViewport`钩子，还可以在任何需要响应的组件中使用它！

钩子的另一个优点是它们可以很容易地扩展。媒体查询不仅可以处理视口宽度，还可以查询视口高度。让我们通过在钩子中添加检查视口高度的功能来复制这种行为。

```
const useViewport = () => {
  const [width, setWidth] = React.useState(window.innerWidth);
  // Add a second state variable "height" and default it to the current window height
  const [height, setHeight] = React.useState(window.innerHeight);

  React.useEffect(() => {
    const handleWindowResize = () => {
      setWidth(window.innerWidth);
      // Set the height in state as well as the width
      setHeight(window.innerHeight);
    }

    window.addEventListener("resize", handleWindowResize);
    return () => window.removeEventListener("resize", handleWindowResize);
  }, []);

  // Return both the height and width
  return { width, height };
}
```

那很简单！这个挂钩现在运行良好，但仍有改进的空间。目前，每个使用这个钩子的组件都将为窗口调整大小事件创建一个全新的事件监听器。这是一种浪费，而且如果钩子同时用于许多不同的组件，可能会导致性能问题。如果我们能够让钩子依赖于整个应用程序可以共享的单个 resize 事件监听器，那就更好了。

## 通过上下文优化性能

我们希望通过在所有使用钩子的组件之间共享一个单窗口调整事件监听器来提高我们的`useViewport`钩子的性能。React Context 是我们带中的一个很好的工具，当状态需要与许多不同的组件共享时，我们可以利用它，所以我们将创建一个新的`viewportContext`,在那里我们可以存储当前视窗大小的状态和计算它的逻辑。

```
const viewportContext = React.createContext({});

const ViewportProvider = ({ children }) => {
  // This is the exact same logic that we previously had in our hook

  const [width, setWidth] = React.useState(window.innerWidth);
  const [height, setHeight] = React.useState(window.innerHeight);

  const handleWindowResize = () => {
    setWidth(window.innerWidth);
    setHeight(window.innerHeight);
  }

  React.useEffect(() => {
    window.addEventListener("resize", handleWindowResize);
    return () => window.removeEventListener("resize", handleWindowResize);
  }, []);

  /* Now we are dealing with a context instead of a Hook, so instead
     of returning the width and height we store the values in the
     value of the Provider */
  return (
    <viewportContext.Provider value={{ width, height }}>
      {children}
    </viewportContext.Provider>
  );
};

/* Rewrite the "useViewport" hook to pull the width and height values
   out of the context instead of calculating them itself */
const useViewport = () => {
  /* We can use the "useContext" Hook to acccess a context from within
     another Hook, remember, Hooks are composable! */
  const { width, height } = React.useContext(viewportContext);
  return { width, height };
}
```

确保您还将应用程序的根包装在新的`ViewportProvider`中，以便新重写的`useViewport`钩子在组件树中进一步使用时可以访问上下文。

```
const App = () => {
  return (
    <ViewportProvider>
      <AppComponent />
    </ViewportProvider>
  );
}
```

这应该可以了！您仍然可以像以前一样使用`useViewport`钩子，但是现在所有的数据和逻辑都保存在一个整洁的位置，并且只为整个应用程序添加了一个 resize 事件监听器。

```
const MyComponent = () => {
  const { width } = useViewport();
  const breakpoint = 620;

  return width < breakpoint ? <MobileComponent /> : <DesktopComponent />;
}
```

很简单。具有 React 挂钩的高性能、优雅且可重用的响应布局🎉

## 其他考虑

我们的钩子正在工作，但这并不意味着我们应该停止工作！仍然有一些可以改进的地方，但是它们超出了本文的范围。如果你想得到额外的积分(虽然没人会计算),这里有一些你可以做的事情来进一步提高这个挂钩:

*   通过限制窗口调整事件侦听器来提高性能，以便在调整浏览器窗口大小时有更少的重新呈现
*   编辑挂钩，使其支持服务器端呈现。这可以通过在尝试访问之前检查`window`是否存在来实现
*   浏览器 API 可以提供一个比检查窗口宽度更好的解决方案。挂钩也可以延伸以支持这一点

## 结论

我已经创建了一个代码沙箱，其中包含本教程的完整代码。

我希望这篇文章已经帮助你了解了更多关于 React 钩子的知识，以及如何利用它们的灵活性，以一种简洁的、可重用的方式在你的应用程序中实现各种令人兴奋的功能。今天，我们已经使用它们来构建响应性布局，而不需要 CSS 媒体查询，但它们真的可以用于任何数量的用例。所以要有创意！

快乐编码。✌️

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