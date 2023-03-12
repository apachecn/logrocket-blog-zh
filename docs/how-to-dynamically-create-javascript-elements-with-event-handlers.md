# 如何用事件处理程序动态创建 JavaScript 元素

> 原文：<https://blog.logrocket.com/how-to-dynamically-create-javascript-elements-with-event-handlers/>

在本文中，我们将研究如何动态创建 JavaScript 元素，以及如何将事件处理程序附加到动态创建的 JavaScript 元素上。

### 介绍

在我们深入研究如何将事件处理程序附加到动态创建的 JavaScript 元素之前，理解 JavaScript 事件及其工作原理很重要。

根据 [MDN](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Event) web docs，事件是在你正在编程的系统中发生的动作或事件，系统会告诉你，如果你想的话，你可以以某种方式响应它们。

它可以以不同的方式发生。例如:当用户点击提交按钮提交表单时，当用户播放或暂停视频时，当用户按下键盘上的特定键时，或者当用户从网页上复制内容时。

在 JavaScript 中，在尚未创建或页面上不存在的元素上附加事件处理程序将引发错误。因此，事件处理程序代码将不起作用。

例如:

在上面的例子中，`btn`类在我们的`index.html`页面中不存在。因此，这将抛出一个 null 的`addEventListener`错误。

我们可以在 JavaScript 中动态创建一个元素，并用这两种方法附加事件处理程序:

`document.createElement()`方法

这个方法允许我们通过标签名来创建 HTML 元素，比如`<div>`、`<p>`、`<img>`和`<h1>`。

### 当我们用`document.createElement`创建一个元素时，它不会立即出现在页面上。它将存储在 JavaScript 内存中，直到被注入到 DOM 中。

在这篇文章中，我们将创建一个简单的狗的个人资料。

我们将动态创建我们的元素，将其注入 DOM，并附加事件处理程序。

[code pen 现场演示](https://codepen.io/amycruz/pen/MWyxaZd)。

这是我们的`index.html`，它的主体中没有任何元素。我们将从 JavaScript 动态创建元素，将其注入 DOM，并为动态创建的元素附加事件处理程序。

在我们的 index.js 中，我们使用`document.createElement()`来动态创建一个新的`div`。然后，我们给之前创建的`div`添加了一个狗类。

属性用于添加、移除和切换元素上的 CSS 类。

接下来，我们创建了一个带有`src`和`alt`属性的图像标签。

我们还创建了`h2`标签，并在其中添加了文本内容。此外，我们添加了一个新的`p`元素和一个包含更多关于狗的信息的`info`类。

接下来，我们将动态创建一个带有类`btn`的按钮。我们将添加文本`click here to read more`。

如果您检查您的浏览器，您不会看到我们创建的带有类`dog`的`div`，因为我们还没有将它附加到 DOM 中。

现在，我们已经动态创建了一个元素。问题是:我们如何将它注入页面？

为了将它注入 DOM，我们将使用`.appendChild()`方法添加动态创建的元素。

更多来自 LogRocket 的精彩文章:

如果您检查您的控制台，您会看到动态创建的元素被注入到您的 DOM 中。

* * *

### 现在我们可以添加事件处理程序。

* * *

要将事件处理程序附加到动态创建的按钮上，我们需要选择一个类为`btn`的按钮，并添加一个事件监听器`click`。

我们说的是按钮的`onclick`，带有`moreInfo`类的`p`标签应该显示`block`。

下面是为狗设定样式的 CSS 代码。

使用 JavaScript 反勾号字符串创建元素

JavaScript 反勾号字符串也称为模板文字。

## 根据 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) ，模板文字是允许嵌入表达式的字符串文字。您可以使用多行字符串和字符串插值功能。

我们将使用 JavaScript 模板文本动态创建一个元素，将它们注入 DOM，并附加事件处理程序。

在 codepen 查看这个[现场演示](https://codepen.io/amycruz/pen/KKzErrr)

这里，我们仍然要创建之前创建的狗配置文件，不同之处在于 JavaScript 文件。所以，我们的 HTML 和 CSS 保持不变。

**template literals.js**

在模板`literals.js`文件中，我们声明了`newDog`，并使用模板文字在其中创建多个元素。如果你`console.log(newDog)`，你会在控制台中看到像字符串一样创建的元素。

或者如果你`console.log(typeof newDog)`，它将返回一个字符串，表明它还不是一个 DOM 元素。

接下来，我们要用 [`document.createRange()`](https://developer.mozilla.org/en-US/docs/Web/API/Document/createRange) 把字符串变成一个 DOM 元素。

然后我们把 [`createContextualFragment()`](https://developer.mozilla.org/en-US/docs/Web/API/Range/createContextualFragment) )法叶放在靶场上。当您`console.log myFragment`时，您将看到一系列对象(元素的集合，或者我们可以使用的 HTML 的一部分)。

然后，我们将`myFragment`追加到页面的主体。

**注意**:如果你确实需要添加事件监听器或更改类，我们应该首先用一个`createcontextual`片段把它放在 DOM 中，然后把它放在正文或页面的任何地方。

接下来，我们选择带有`.btn`类的按钮，用户单击它可以阅读更多关于狗的信息。

如果你`console.log(btn)`，你会发现它在 DOM 中，因为我们已经通过`createContextual`片段将它注入到 DOM 中。

添加事件处理程序

我们添加了一个`click`的事件监听器，并调用了一个`showMore`函数。

### 在`showMore`函数中，我们选择了类`moreInfo`。如果你检查我们的`css`，你会发现文本被设置为无显示。所以现在我们说按钮的`onclick`，让显示`none`改成显示`block`。

我们已经创建了一个狗的个人资料。所有的元素都是动态创建的，事件处理程序`onclick`被添加到动态创建的元素中。

结论

恭喜你。您刚刚学习了如何使用`document.createElement`动态创建 JavaScript 元素，以及如何使用模板文字。

## 您还学习了如何将事件处理程序附加到动态创建的元素上。

通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

## LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

.

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket records console logs, page load times, stacktraces, slow network requests/responses with headers + bodies, browser metadata, and custom logs. Understanding the impact of your JavaScript code will never be easier!

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.