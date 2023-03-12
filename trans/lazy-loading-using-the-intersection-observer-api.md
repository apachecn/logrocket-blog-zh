# 使用交叉点观察器 API 的延迟加载

> 原文：<https://blog.logrocket.com/lazy-loading-using-the-intersection-observer-api/>

惰性加载是一种技术，它允许我们[延迟加载](https://blog.logrocket.com/lazy-loading-components-in-react-16-6-6cea535c0b52/)应用程序中不重要的内容，直到初始页面加载之后。通过首先只加载最重要的内容，然后在用户需要时加载剩余的内容，我们可以减少应用程序的捆绑包大小，从而在用户首次部署应用程序时节省宝贵的时间。

在现代应用程序中，有许多不同的方法来添加延迟加载，但最常用的方法之一是无限滚动。页面的内容已经呈现，当用户向下滚动页面时，会加载更多的内容。

我们还可以通过一种叫做[代码分割](https://blog.logrocket.com/code-splitting-in-react-an-overview/)的技术来利用延迟加载。通常在 React 应用程序中使用，它通过将应用程序的代码分割成延迟加载的部分来帮助我们减少包的大小。

在本教程中，我们将使用[交叉点观察器 API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) 在普通 JavaScript 应用程序中应用延迟加载技术。

## 使用交叉点观察器 API

随着 web 的不断发展，浏览器获得了更多的支持、更新的特性和更新的 API。对于 web 开发人员来说，创建任何依赖于特定 DOM 元素位置可见性的功能都是一个棘手的问题。

以前，浏览器没有任何检查元素可见性的 API，我们必须依赖 JavaScript 和 jQuery 这样的解决方案。很难实现这个问题的解决方案。现代浏览器通过实现每天更新的新 API，让我们的生活变得更加轻松。交叉点观察器 API 就是其中之一。

交叉点观察器 API 提供了一种观察特定元素交叉点变化的方法。我们可以使用这个 API 来检查应用程序中特定元素的可见性，而不必创建混乱的代码和变通的功能。

![Intersection Observer Api Browser Viewport Example](img/f571d1da1ff5b38d19fceb42a99a58ec.png)

交叉点观察器 API 可以多种方式使用:

*   当页面滚动时，延迟加载图像或其他内容
*   实现无限滚动
*   检查广告的元素可见性以计算收入
*   告诉浏览器仅在元素在视窗中可见时才执行代码

现在，我们将使用`IntersectionObserver`接口创建一个新的观察者，并跟踪目标元素交集的变化:

```
let observer = new IntersectionObserver(callback, options);

```

`IntersectionObserver`的第一个参数是一个回调函数，当我们的目标元素与我们的设备视口或特定元素相交时会调用这个函数。第二个参数是一个我们称之为`options`的对象。该对象负责控制如何调用回调的环境，并具有以下字段:

*   `root`:设置为用于检查目标可见性的视口的元素。这个元素应该是`target`元素的祖先
*   `rootMargin`:围绕`root`元素定义的边距。该元素可以接收 CSS 值，如像素或百分比
*   `thresold`:一个数字或数字数组，表示观察者的回调函数应该调用的目标可见性的百分比

![Intersection Observer Fields Root Rootmargin Threshold](img/427bfa9b11d7e94eab928e5362234fdb.png)

让我们使用交叉点观察器 API 创建我们的第一个观察器。

我们将有一个名为`options`的对象，在那里我们将为我们的观察者传递选项。然后，我们将通过创建一个新的`IntersectionObserver`并传递一个回调函数来创建我们的第一个观察者:

```
const options = {
  root: document.querySelector(".container"),
  rootMargin: "10px",
  threshold: 0.5
};
const myFirstObserver = new IntersectionObserver((elements) => {
  elements.forEach((element) => console.log("element", element));
}, options);

```

现在，我们需要得到我们要观察的目标元素:

```
const target = document.querySelector(".header");

```

之后，我们使用`observe`方法来观察目标元素交集的变化:

```
observer.observe(target);

```

这是我们最终代码的样子:

```
const options = {
  root: document.querySelector(".container"),
  rootMargin: "10px",
  threshold: 0.5
};
const observer = new IntersectionObserver((elements) => {
  elements.forEach((element) => console.log("element", element));
}, options);
const target = document.querySelector(".header");
observer.observe(target);

```

使用交叉点观察器 API 时，我们需要记住几件事情:

*   当目标达到一个阈值时，回调函数将被调用并接收一个`IntersectionObserverEntry`对象列表
*   在`IntersectionObserver`界面的第二个参数中，如果你不设置根元素，它将默认为浏览器视窗或`null`
*   目标元素应该在根元素的 DOM 树中
*   `IntersectionObserver`界面不允许你一次观察多个元素。要观察多个，您必须迭代并逐个观察它们

## 真实世界的应用用例

如何使用交叉点观察器 API 取决于您在应用程序中使用的技术。如果你使用普通的 JavaScript，你可以很容易地使用浏览器 API 本身。如果您正在使用一些 JavaScript 框架，有一些[库](https://www.npmjs.com/search?q=intersection%20observer%20api)可以帮助您使用交叉点观察器 API。

我们将使用 Intersection Observer API 和 React 创建一个示例，因为它是一个流行且常用的 JavaScript UI 库。但是，您可以轻松地将交叉点观察器 API 用于任何您想要的框架或库。

让我们使用以下命令创建一个新的 Create React 应用程序项目:

```
npx create-react-app intersection-observer-in-practice

```

我们将在我们的`App`组件中编写所有的代码。首先，我们需要使用 CSS 对我们的应用程序进行一点样式化，从我们的部分和我们的目标元素开始。在目标元素内部，当元素可见时，我们将添加`.isVisible`类。

在默认的`styles.css`文件中，粘贴以下代码:

```
.root {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: #fff;
}

.section {
  height: 100vh;
  width: 100%;
}

.target {
  display: flex;
  justify-content: center;
  align-items: center;
  background: #151419;
  color: #fff;
  font-weight: 900;
  font-size: 20px;
  width: 300px;
  height: 200px;
  border-radius: 5px;
}

.isVisible {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  top: 0;
  width: 100%;
  background: #151419;
  color: #fff;
  font-size: 20px;
  font-weight: 900;
  padding: 40px;
}

```

我们将使用 Intersection Observer API 创建一个简单的例子，当我们的目标元素对我们可见时，它会改变我们的 header 组件中的内容。

在我们的`App`组件中，我们将创建一些 HTML 元素:

```
<div className="root">
  <div className="isVisible">
    {isVisible ? "✅ Visible" : "❌ Not visible"}
  </div>
  <div className="section"></div>
  <div className="target" ref={targetRef}>
    target element
  </div>
</div>

```

现在，我们将创建一个名为`rootRef`的引用，并将其传递给我们的目标元素。之后，我们将创建一个名为`isVisible`的状态来检查目标元素在页面上是否可见:

```
const rootRef = useRef(null);
const [isVisible, setIsVisible] = useState(false);

```

接下来，我们将使用 [`useEffect`](https://reactjs.org/docs/hooks-effect.html) 钩子来创建我们的观察者。在钩子内部，我们将创建我们的`options`对象:

```
const options = {
  root: null,
  rootMargin: "0px",
  threshold: 1.0
};

```

记住，当我们的回调函数被调用时，它接收一个`IntersectionObserverEntry`对象列表。这些对象有很多属性，其中一个叫做`isIntersecting`。`isIntersecting`属性是一个布尔值，如果目标元素与观察者的根元素相交，则该值为真。

现在让我们创建我们的观察者。对于我们的回调函数，我们将获取`isIntersecting`属性的值并设置`isVisible`状态:

```
const observer = new IntersectionObserver((entries: any) => {
  const [entry] = entries;
  setIsVisible(entry.isIntersecting);
}, options);

```

现在，我们将使用`observe`函数和`unobserve`方法进行清理:

```
if (targetRef.current) observer.observe(targetRef.current);
return () => {
  if (targetRef.current) observer.unobserve(targetRef.current);
};

```

我们所需要做的就是将我们的`targetRef`作为一个依赖项传递给我们的`useEffect`钩子，这样我们就有了一个完整的交叉点观察器 API 工作顺利的例子。您可以注意到，当目标元素在页面上可见时，内容会发生变化。

如果你不想使用浏览器 API，而想使用一个库来加速开发，有一个非常好的库叫做[react-intersection-observer](https://www.npmjs.com/package/react-intersection-observer)。

要使用这个库，您需要导入`useInView`钩子并传递一个`options`对象作为参数:

```
const options = {
  root: null,
  rootMargin: "0px",
  threshold: 1.0
};
const { ref, inView, entry } = useInView(options);

```

最后，您所要做的就是将`ref`分配给想要观察的 DOM 元素，钩子将报告状态:

```
import React from 'react';
import { useInView } from 'react-intersection-observer';
const Component = () => {
  const options = {
    root: null,
    rootMargin: "0px",
    threshold: 1.0
  };
  const { ref, inView, entry } = useInView(options);
  return (
    <div ref={ref}>
      <h2>{`Header inside viewport ${inView}.`}</h2>
    </div>
  );
};

```

## 结论

使用交叉点观察器 API 有无限的可能性。只需几行额外的代码，您就可以通过减少加载时间来极大地改善应用程序的用户体验。您可以延迟加载图像，当图像在页面上可见时实现链接预取，等等。

对[交叉点观察器 API](https://caniuse.com/intersectionobserver) 的支持非常强大。在撰写本文时，大多数现代浏览器都支持它。这个 API 值得考虑，以帮助观察我们的网页上的变化。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.