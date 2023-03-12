# 在 React 应用程序中使用 dangerouslySetInnerHTML

> 原文：<https://blog.logrocket.com/using-dangerouslysetinnerhtml-in-a-react-application/>

本文介绍了在 React 应用程序中使用`dangerouslySetInnerHTML`属性的原因，它相当于浏览器 DOM 中的`innerHTML`属性。

## 什么是`dangerouslySetInnerHTML`？

`dangerouslySetInnerHTML`是一个属性，可以在 React 应用程序中的 HTML 元素上使用，以编程方式设置它们的内容。您可以直接在元素上使用这个属性，而不是使用选择器来获取 HTML 元素，然后设置它的`innerHTML`。

当使用`dangerouslySetInnerHTML`时，React 也知道该特定元素的内容是动态的，并且对于该节点的子节点，它简单地跳过与虚拟 DOM 的比较以获得一些额外的性能。

正如该属性的名称所暗示的，使用它可能很危险，因为它使您的代码容易受到跨站点脚本(XSS)攻击。这就成了一个问题，尤其是当您从第三方数据源获取数据或呈现用户提交的内容时。

## 何时使用`dangerouslySetInnerHTML`

需要设置 DOM 元素的 HTML 内容的一个用例是用来自富文本编辑器的数据填充`<div>`元素。假设您有一个网页，人们可以在其中提交评论，并且您允许他们使用富文本编辑器。在这种情况下，富文本编辑器的输出很可能是带有标签如`<p>`、`<b>`和`<img>`的 HTML。

考虑下面的代码片段，它将呈现字符串，而不知道其中的`<b>`标记——这意味着输出将只是字符串本身，没有任何粗体文本，如下所示:lorem < b > ipsum < /b >。

```
const App = () => {
  const data = 'lorem <b>ipsum</b>';

  return (
    <div>
      {data}
    </div>
  );
}

export default App;

```

但是当使用`dangerouslySetInnerHTML`时，React 会意识到 HTML 标签并正确地呈现它们。这一次，输出将使用粗体文本正确呈现(例如，lorem **ipsum** )。

```
const App = () => {
  const data = 'lorem <b>ipsum</b>';

  return (
    <div
      dangerouslySetInnerHTML={{__html: data}}
    />
  );
}

export default App;

```

注意，它应该是一个将`__html`键传递给`dangerouslySetInnerHTML`的对象。除此之外，您使用`dangerouslySetInnerHTML`属性的元素不应该有任何子元素，因此使用`<div>`元素作为自结束标记。

传递对象的要求只是防止开发人员在没有浏览文档和意识到潜在危险的情况下使用它的另一种保护措施。

## 使用`dangerouslySetInnerHTML`时的消毒

上述示例在渲染时不会造成危险。但是，在某些情况下，HTML 元素可能会执行脚本。

考虑下面的例子，其中 JavaScript 事件被附加到 HTML 元素。虽然这些都是无害的例子，但它们是概念的证明，展示了 HTML 元素如何被利用来运行恶意脚本。

```
const App = () => {
  const data = `lorem <b onmouseover="alert('mouseover');">ipsum</b>`;

  return (
    <div
      dangerouslySetInnerHTML={{__html: data}}
    />
  );
}

export default App;

const App = () => {
  const data = `lorem ipsum <img src="" onerror="alert('message');" />`;

  return (
    <div
      dangerouslySetInnerHTML={{__html: data}}
    />
  );
}

export default App;

```

幸运的是，HTML 有净化工具，可以检测 HTML 代码中潜在的恶意部分，然后输出干净安全的版本。最受欢迎的 HTML 杀毒软件是 DOMPurify。

让我们用它的[在线演示](https://cure53.de/purify)来净化上面提到的 HTML 代码，看看它是如何检测和过滤掉执行时可能有危险的代码部分的。

```
Original
lorem <b onmouseover="alert('mouseover');">ipsum</b>

Sanitized
lorem <b>ipsum</b>

```

```
Original
lorem ipsum <img src="" onerror="alert('message');" />

Sanitized
lorem ipsum <img src="">

```

即使我们信任数据的来源，使用杀毒软件也是一个好习惯。使用 DOMPurify 包，上面的一个例子如下:

```
import DOMPurify from 'dompurify'

const App = () => {
  const data = `lorem <b onmouseover="alert('mouseover');">ipsum</b>`
  const sanitizedData = () => ({
    __html: DOMPurify.sanitize(data)
  })

  return (
    <div
      dangerouslySetInnerHTML={sanitizedData()}
    />
  );
}

export default App;

```

`sanitizedData`函数返回一个带有`__html`键的对象，该对象有一个从`DOMPurify.sanitize`函数返回的值。

正如所料，当我们悬停在粗体文本上时，不会执行任何警告功能。

注意，因为 DOMPurify 需要一个 DOM 树，而节点环境没有，您要么必须使用`jsdom`包来创建一个`window`对象并用它初始化`DOMPurify`，要么单独使用`isomorphic-dompurify`包，它封装了`DOMPurify`和`jsdom`包。

如果你倾向于第一种选择，你可以参考下面来自`DOMPurify`文档的片段。

```
const createDOMPurify = require('dompurify');
const { JSDOM } = require('jsdom');

const window = new JSDOM('').window;
const DOMPurify = createDOMPurify(window);

const clean = DOMPurify.sanitize(dirty);

```

## 结论

总之，`dangerouslySetInnerHTML`不过是 React 中`innerHTML`的替代品，应该小心使用。尽管其名称暗示了其使用中的危险，但是通过使用开发良好的杀毒程序来采取必要的措施可以确保代码是干净的，并且在 React 节点中呈现时不会运行意外的脚本。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)