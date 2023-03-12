# 使用片段在 React 中呈现子元素

> 原文：<https://blog.logrocket.com/rendering-child-elements-react-fragments/>

使用 React 呈现用户界面的一个优点是很容易将 UI 分解成组件。然而，一个常见的问题是当我们想要返回没有父元素的兄弟元素时。React 有一个叫做片段的特性提供了解决方案。

## 问题是

如果你不熟悉这个问题，考虑这个例子。

```
function FAQ() {
    return (
        <p>Q. What is React?</p>
        <p>A. A JavaScript library for building user interfaces</p>
        <p>Q. How do I render sibling elements?</p>
        <p>A. Use Fragments</p>
    );
}
```

该代码导致一个错误:`Adjacent JSX elements must be wrapped in an enclosing tag.`

## 解决方案

一种选择是用父元素包装元素。

```
function FAQ() {
    return (
        <div>
            <p>Q. What is React?</p>
            <p>A. A JavaScript library for building user interfaces</p>
            <p>Q. How do I render sibling elements?</p>
            <p>A. Use Fragments</p>
        </div>
    );
}
```

但是这种方法在 DOM 中引入了一个额外的元素，增加了一个我们实际上并不需要的嵌套层次。

React [v16.0](https://github.com/facebook/react/releases/tag/v16.0.0) 引入了另一个选项，就是返回一个元素数组。

```
function FAQ() {
    return [
        <p>Q. What is React?</p>,
        <p>A. A JavaScript library for building user interfaces</p>,
        <p>Q. How do I render sibling elements?</p>,
        <p>A. Use Fragments</p>
    ];
}
```

现在我们需要在元素之间添加逗号。更重要的是，这段代码产生了一个[键](https://reactjs.org/docs/lists-and-keys.html#keys)警告:`Warning: Each child in a list should have a unique "key" prop.`给每个元素添加一个键可以解决这个问题，但是非常麻烦。

```
function FAQ() {
    return [
        <p key="q1">Q. What is React?</p>,
        <p key="a1">A. A JavaScript library for building user interfaces</p>,
        <p key="q2">Q. How do I render sibling elements?</p>,
        <p key="a2">A. Use Fragments</p>
    ];
}
```

React [v16.2](https://github.com/facebook/react/releases/tag/v16.2.0) 给了我们一个更干净的解决方案，就是使用碎片。

```
function FAQ() {
    return (
        <React.Fragment>
            <p>Q. What is React?</p>
            <p>A. A JavaScript library for building user interfaces</p>
            <p>Q. How do I render sibling elements?</p>
            <p>A. Use Fragments</p>
        </React.Fragment>
    );
}
```

我们不再需要提供键，我们不需要添加数组逗号，我们仍然避免添加额外的 DOM 元素，因为`React.Fragment`在呈现期间不会变成实际的元素。

我们可以导入`Fragment`来避免完全写出`React.Fragment`。

```
import React, {Fragment} from "react";

function FAQ() {
    return (
        <Fragment>
            <p>Q. What is React?</p>
            <p>A. A JavaScript library for building user interfaces</p>
            <p>Q. How do I render sibling elements?</p>
            <p>A. Use Fragments</p>
        </Fragment>
    );
}
```

然而，更好的方法是使用片段的简写语法，它看起来像空标签:`<>`和`</>`。

```
function FAQ() {
    return (
        <>
            <p>Q. What is React?</p>
            <p>A. A JavaScript library for building user interfaces</p>
            <p>Q. How do I render sibling elements?</p>
            <p>A. Use Fragments</p>
        </>
    );
}
```

这给了我们与使用`React.Fragment`相同的好处，但是我们不需要导入任何其他东西，我们的代码看起来更干净。

速记语法目前唯一不支持的是添加一个`key`。例如，如果你正在创建一个[描述列表](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dl)，这可能会有问题。在这种情况下，使用标准语法。

```
function Dictionary(props) {
    return (
        <dl>
            {props.terms.map(({word, definition}) => 
                <React.Fragment key={word}>
                    <dt>{word}</dt>
                    <dd>{definition}</dd>
                </React.Fragment>
            )}
        </dl>
    );
}
```

我们需要提供一个密钥来避免密钥警告。

然而，在大多数情况下，简写方法是我们返回兄弟元素的原始问题的最佳解决方案。这个解决方案已经做得足够好，以至于当 v3 发布时，Vue.js 也将原生支持片段[的概念。](https://medium.com/the-vue-point/plans-for-the-next-iteration-of-vue-js-777ffea6fabf)

## 使用片段

在过去的两年中，JavaScript 生态系统增加了对片段的广泛支持。

如果您升级您的依赖项，您将可能能够使用片段而无需任何额外的配置。

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