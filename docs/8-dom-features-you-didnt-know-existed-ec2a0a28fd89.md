# 你不知道的 8 个 DOM 特性

> 原文：<https://blog.logrocket.com/8-dom-features-you-didnt-know-existed-ec2a0a28fd89/>

随着最近对工具的关注越来越多，从所有的 React 和 npm-install-everything 帖子中休息一下，仔细看看一些在现代浏览器中工作的纯 DOM 和 Web API 特性是很好的。

这篇文章将考虑八个鲜为人知的 DOM 特性，它们具有强大的浏览器支持。为了帮助解释每一个是如何工作的，我将包含许多交互式演示，供您自己尝试代码。

这些方法和属性没有陡峭的学习曲线，并且将很好地与您碰巧引入到项目中的任何工具集一起工作。

您肯定使用过`addEventListener()`来处理将事件附加到 web 文档中的元素。通常，`addEventListener()`呼叫看起来像这样:

```
element.addEventListener('click', doSomething, false);
```

第一个参数是我要监听的事件。第二个参数是事件发生时将执行的回调函数。第三个参数是一个名为`useCapture`的布尔值，它指示您是否想要使用[事件冒泡或捕获](https://www.sitepoint.com/event-bubbling-javascript/)。

这些都是众所周知的(尤其是前两个)。但是也许你不知道`addEventListener()`也接受一个替换最终布尔值的参数。这个新参数是一个看起来像这样的`options`对象:

```
element.addEventListener('click', doSomething, {
  capture: false,
  once: true,
  passive: false
});
```

注意，该语法允许定义三种不同的属性。下面是每个词的意思的简要概述:

*   **捕获** —一个布尔值，与前面提到的`[useCapture](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Parameters)` [参数](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Parameters)相同
*   **once** —一个布尔值，如果设置为`true`，则表示该事件只应在目标元素上运行一次，然后被删除
*   **被动** —一个最终布尔值，如果设置为`true`，则表明该函数永远不会调用`preventDefault()`，即使它包含在函数体中

这三个选项中最有趣的是`once`选项。这在许多情况下肯定会派上用场，并使您不必使用`removeEventListener()`或其他复杂的技术来强制单个事件触发。如果您使用过 jQuery，您可能会熟悉该库中的一个类似特性，[。一()方法](http://api.jquery.com/one/)。

您可以在下面的代码栏中尝试一些使用`options`对象的代码:

请看 [CodePen](https://codepen.io) 上 Louis Lazaris([@ impressive webs](https://codepen.io/impressivewebs))
使用 addEventListener()的笔[，带一个‘options’对象作为第三参数](https://codepen.io/impressivewebs/pen/GeJZYz/)。

请注意，演示页面上的按钮只会追加一次文本。如果您将`once`值更改为`false`，然后多次单击按钮，则每次单击按钮时都会追加文本。

浏览器对`options`对象的支持非常好:除了 IE11 和更早的版本，所有的浏览器都支持它[，所以如果你不担心微软浏览器的早期版本，使用它是非常安全的。](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Browser_compatibility)

### scrollTo()方法用于在窗口或元素中平滑滚动

平滑滚动一直是一种需要。当一个本地页面链接立即跳转到一个指定的位置时，这是不和谐的(如果你眨眼，你甚至可能错过跳转)。平滑滚动不仅好看，还能提高页面的 UX。

虽然这在过去已经使用 jQuery 插件完成了，但是现在使用`window.scrollTo()`方法只用一行 JavaScript 就可以实现。

将`scrollTo()`方法应用于 Window 对象，告诉浏览器滚动到页面上的指定位置。例如，下面是一个最简单的语法示例:

```
window.scrollTo(0, 1000);
```

这将使窗口`0px`向右滚动(代表 x 坐标，或水平滚动)并使`1000px`向下滚动(垂直，这通常是您想要的)。但那样的话，滚动就不会是流畅的动画效果；页面会突然滚动，就像你使用一个指向指定散列 URL 的本地链接一样。

有时候这就是你想要的。但是为了获得平滑的滚动，你必须加入不太为人所知的`ScrollToOptions`对象，就像这样:

```
window.scrollTo({
  top: 0,
  left: 1000,
  behavior: 'smooth'
});
```

这段代码相当于前面的例子，但是增加了`options`对象中`behavior`属性的`smooth`值。

使用选项与窗口一起查看笔 [。路易·拉扎勒斯(](https://codepen.io/impressivewebs/pen/pGYXgj/) [@impressivewebs](https://codepen.io/impressivewebs) )
在 [CodePen](https://codepen.io) 上的 ScrollTo() 。

尝试在框中输入一个数字(最好是像`4000`这样的大数字)，并将“行为”选择框改为使用`smooth`或`auto`(这是`behavior`属性仅有的两个选项)。

关于此功能的一些注意事项:

*   对`scrollTo()`的基本支持是全面的，但是[并不是所有的](https://caniuse.com/#feat=element-scroll-methods)浏览器都支持`options`对象
*   当应用于一个元素而不是窗口时，这个方法也可以工作
*   这些选项也适用于`scroll()`和`scrollBy()`方法

### 带有可选参数的 setTimeout()和 setInterval()

在许多情况下，使用`window.setTimeout()`和`window.setInterval()`制作基于时间的动画现在已经被性能更友好的`window.requestAnimationFrame()`所取代。但是有些情况下`setTimeout()`或`setInterval()`是正确的选择，所以了解一下这些方法鲜为人知的特性是有好处的。

通常你会看到这些方法的语法如下:

```
let timer = window.setInterval(doSomething, 3000);
function doSomething () {
  // Something happens here...
}
```

这里,`setInterval()`调用传入两个参数:回调函数和时间间隔。有了`setTimeout()`,这将运行一次，而在这种情况下，它将无限期地运行，直到我在传入定时器变量的同时调用`window.clearTimeout()`。

很简单。但是如果我想让我的回调函数接受参数呢？好吧，这些定时器方法最近增加了以下功能:

```
let timer = window.setInterval(doSomething, 3000, 10, 20);
function doSomething (a, b) {
  // Something happens here…
}
```

注意，我在我的`setInterval()`调用中增加了两个参数。然后，我的`doSomething()`函数接受这些参数，并根据需要操纵它们。

这里有一个 CodePen 演示，它使用`setTimeout()`演示了这是如何工作的:

参见 [CodePen](https://codepen.io) 上 Louis Lazaris([@ impressive webs](https://codepen.io/impressivewebs))【T5)的 window.setTimeout() 的 Pen [可选参数。](https://codepen.io/impressivewebs/pen/PgoNEj/)

当您单击该按钮时，将使用两个传入的值进行计算。这些值可以通过页面上的数字输入进行更改。

至于浏览器支持，这方面似乎有不一致的信息，但似乎所有正在使用的浏览器都支持可选参数功能，包括 IE10。

### 单选按钮和复选框的 defaultChecked 属性

你可能知道，对于单选按钮和复选框，如果你想获取或设置`checked`属性，你可以使用`checked`属性，就像这样(假设`radioButton`是对一个特定表单输入的引用):

```
console.log(radioButton.checked); // true
radioButton.checked = false;
console.log(radioButton.checked); // false
```

但是还有一个名为`defaultChecked`的属性，它可以应用于单选按钮或复选框组，以找出组中的哪一个最初被设置为`checked`。

以下是一些 HTML 示例:

```
<form id="form">
  <input type="radio" value="one" name="setOne"> One
  <input type="radio" value="two" name="setOne" checked> Two<br />
  <input type="radio" value="three" name="setOne"> Three
</form>
```

这样，即使在选中的单选按钮被更改后，我也可以遍历输入并找出最初选中的是哪一个，如下所示:

```
for (i of myForm.setOne) {
  if (i.defaultChecked === true) {
    console.log(‘i.value’);
  }
}
```

下面是一个 CodePen 演示程序，它将显示当前选中的单选按钮或默认选中的单选按钮，这取决于您使用的按钮:

请看 [CodePen](https://codepen.io) 上 Louis Lazaris([@ impressive webs](https://codepen.io/impressivewebs))
的笔 [默认勾选单选按钮](https://codepen.io/impressivewebs/pen/qwWoOr/)。

该示例中的`defaultChecked`选项将始终是“Two”单选按钮。如上所述，这也可以通过复选框组来实现。尝试更改 HTML 中的默认选中选项，然后再次尝试该按钮。

这是另一个用一组复选框做同样事情的演示:

请看 [CodePen](https://codepen.io) 上 Louis Lazaris([@ impressive webs](https://codepen.io/impressivewebs))
的[笔
默认勾选复选框](https://codepen.io/impressivewebs/pen/wZMJYQ/)。

在这种情况下，您会注意到其中两个复选框是默认选中的，所以当使用`defaultChecked`查询时，这两个复选框都将返回`true`。

### 用 normalize()和 wholeText 操作文本节点

HTML 文档中的文本节点可能很挑剔，尤其是在动态插入或创建节点时。例如，如果我有下面的 HTML:

```
<p id="el">This is the initial text.</p>
```

然后，我可以向段落元素添加一个文本节点:

```
let el = document.getElementById('el');
el.appendChild(document.createTextNode(' Some more text.'));
console.log(el.childNodes.length); // 2
```

请注意，在追加了文本节点之后，我记录了段落中子节点的长度，它显示有两个节点。这些节点是单个文本字符串，但是因为文本是动态追加的，所以它们被视为单独的节点。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在某些情况下，如果将文本视为单个文本节点会更有帮助，这使得文本更容易操作。这就是`normalize()`和`wholeText()`的用武之地。

`normalize()`方法可用于合并单独的文本节点:

```
el.normalize();
console.log(el.childNodes.length); // 1
```

在一个元素上调用`normalize()`将会合并该元素中任何相邻的文本节点。如果碰巧有一些 HTML 散布在相邻的文本节点中，HTML 将保持原样，而所有相邻的文本节点将被合并。

但是，如果出于某种原因，我希望保持文本节点分离，但我仍然希望能够将文本作为一个单元来获取，那么这就是`wholeText`的用处。因此，我可以在相邻的文本节点上这样做，而不是调用`normalize()`:

```
console.log(el.childNodes[0].wholeText);
// This is the initial text. Some more text.
console.log(el.childNodes.length); // 2
```

只要我没有调用`normalize()`，文本节点的长度将保持在`2`，我可以使用`wholeText`记录整个文本。但是请注意一些事情:

*   我必须在一个文本节点上调用`wholeText`，而不是元素(因此代码中的`el.childNodes[0]`；`el.childNodes[1]`也行)
*   文本节点必须是相邻的，没有 HTML 分隔它们

在这个 CodePen 演示中，你可以看到这两个特性，以及正在使用的[方法。打开 CodePen 控制台或浏览器的开发人员工具控制台，查看生成的日志。](https://codepen.io/impressivewebs/pen/ZZzrBV?editors=0011)

### insertadjacentelemont_)和 insertadjacenttext_)

你们中的许多人可能都熟悉`insertAdjacentHTML()`方法，这种方法允许你轻松地将一串文本或 HTML 添加到页面中与其他元素相关的特定位置。

但是您可能没有意识到，该规范还包括两个以相似方式工作的相关方法:`insertAdjacentElement()`和`insertAdjacentText()`。

`insertAdjacentHTML()`的缺陷之一是插入的内容必须是字符串的形式。所以如果你包含 HTML，它必须这样声明:

```
el.insertAdjacentHTML('beforebegin', '<p><b>Some example</b> text goes here.</p>');
```

然而，使用`insertAdjacentElement()`，第二个参数可以是一个元素引用:

```
let el = document.getElementById('example'),
addEl = document.getElementById('other');
el.insertAdjacentElement('beforebegin', addEl);
```

这个方法的有趣之处在于，它不仅会将引用的元素添加到指定的位置，还会将元素从文档中的原始位置移除。所以这是一种将元素从 DOM 中的一个位置转移到另一个位置的简单方法。

这里有一个使用`insertAdjacentElement()`的 CodePen 演示。按钮点击有效地“移动”了目标元素:

在 [CodePen](https://codepen.io) 上看到 Louis Lazaris([@ impressive webs](https://codepen.io/impressivewebs))
用 insertAdjacentElement()改变一个元素的位置的笔[。](https://codepen.io/impressivewebs/pen/MRjOpj/)

`insertAdjacentText()`方法的工作方式类似，但是所提供的文本字符串将被专门作为文本插入，即使它包含 HTML。请注意下面的演示:

在 [CodePen](https://codepen.io) 上看到 Louis Lazaris([@ impressive webs](https://codepen.io/impressivewebs))
使用带有 HTML 标签的 insertAdjacentText()的笔[。](https://codepen.io/impressivewebs/pen/MRymba/)

您可以将自己的文本添加到输入字段，然后使用按钮将其添加到文档中。请注意，任何特殊字符(如 HTML 标签)都将作为 HTML 实体插入，与`insertAdjacentHTML()`相比，该方法的行为有所不同。

所有三种方法(`insertAdjacentHTML()`、`insertAdjacentElement()`和`insertAdjacentText()`)都为第一个参数取相同的值。这些论点是:

*   `beforebegin`:插入在方法被调用的元素之前
*   `afterbegin`:插入元素内部，第一个子元素之前
*   `beforeend`:插入元素内部，在它的最后一个子元素之后
*   `afterend`:插入元素后

### event.detail 属性

正如已经讨论过的，使用熟悉的`addEventListener()`方法将事件附加到 web 页面的元素上。例如:

```
btn.addEventListener('click', function () {
  // do something here...
}, false);
```

当使用`addEventListener()`时，您可能有必要防止函数调用中的默认浏览器行为。例如，也许您想拦截对`<a>`元素的点击，并用 JavaScript 处理这些点击。你应该这样做:

```
btn.addEventListener('click', function (e) {
  // do something here...
  e.preventDefault();
}, false);
```

这里使用的是`preventDefault()`，它是传统说法`return false`的现代翻版。这要求您将`event`对象传递到函数中，因为在该对象上调用了`preventDefault()`方法。

但是你可以用那个`event`对象做更多的事情。事实上，当使用某些事件时(例如，`click`、`dbclick`、`mouseup`、`mousedown`)，这些事件会公开一个叫做 [UIEvent 接口](https://developer.mozilla.org/en-US/docs/Web/API/UIEvent)的东西。正如 MDN 所指出的，这个界面上的许多特性都被否决或者没有标准化。但是最有趣和有用的是`detail`属性，它是官方规范的[部分。](https://w3c.github.io/uievents/#dom-uievent-detail)

下面是它在同一个事件侦听器示例中的样子:

```
btn.addEventListener('click', function (e) {
  // do something here...
  console.log(e.detail);
}, false);
```

我已经设置了一个 CodePen 演示程序，使用许多不同的事件来演示结果:

在 [CodePen](https://codepen.io) 上查看 Louis Lazaris([@ impressive webs](https://codepen.io/impressivewebs))
使用 event.detail 属性的笔[。](https://codepen.io/impressivewebs/pen/QoZoQe/)

演示中的每个按钮都将按照按钮文本描述的方式做出响应，并显示一条显示当前点击次数的消息。一些需要注意的事项:

*   WebKit 浏览器允许无限制的点击次数，除了在`dbclick`上，它总是两次。Firefox 最多只允许点击三次，然后重新开始计数
*   我已经包括了`blur`和`focus`来证明这些不符合条件，并且将总是返回 0(即没有点击)
*   IE11 等老浏览器的行为非常不一致

请注意，该演示包括一个很好的使用案例，即模拟三次点击事件的能力:

```
btnT.addEventListener('click', function (e) {
  if (e.detail === 3) {
    trpl.value = 'Triple Click Successful!';
  }
}, false);
```

如果所有浏览器的点击次数都超过了三次，那么您也可以检测到更高的点击次数，但我认为对于大多数目的来说，三次点击事件就足够了。

### scrollHeight 和 scrollWidth 属性

`scrollHeight`和`scrollWidth`属性可能听起来很熟悉，因为您可能会将它们与其他与宽度和高度相关的 DOM 特性相混淆。例如，`offsetWidth`和`offsetHeight`属性将返回一个元素的高度或宽度，而不考虑溢出。

例如，请注意下面的演示:

请看 [CodePen](https://codepen.io) 上 Louis Lazaris([@ impressive webs](https://codepen.io/impressivewebs))
的笔[off the height not Count Past CSS 溢出](https://codepen.io/impressivewebs/pen/WWxEpX/)。

演示中的列具有相同的内容。左侧栏的`overflow`设置为`auto`，而右侧栏的`overflow`设置为`hidden`。`offsetHeight`属性为每个返回相同的值，因为它不考虑可滚动或隐藏区域；它只测量元素的实际高度，包括任何垂直填充和边框。

另一方面，名副其实的`scrollHeight`属性将计算元素的完整高度，包括可滚动(或隐藏)区域:

参见 [CodePen](https://codepen.io) 上 Louis Lazaris([@ impressive webs](https://codepen.io/impressivewebs))
的 Pen [scrollHeight 测量一个元素的全滚动区](https://codepen.io/impressivewebs/pen/EJyvoB/)。

上面的演示和前面的一样，只是它使用了`scrollHeight`来获取每一列的高度。再次注意，两列的值是相同的。但这一次是一个高得多的数字，因为溢出区也被算作高度的一部分。

上面的例子集中在元素高度上，这是最常见的用例，但是你也可以使用`offsetWidth`和`scrollWidth`，它们可以以同样的方式应用于水平滚动。

### 结论

这就是 DOM 特性的列表，这些可能是我在过去几年中遇到的最有趣的特性，所以我希望在不久的将来，这些特性中至少有一个是你能够在项目中使用的。

请在评论中告诉我你以前是否使用过其中的一个，或者你是否能为其中的任何一个想到一些有趣的用例。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.