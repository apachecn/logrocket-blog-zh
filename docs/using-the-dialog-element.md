# 为什么你应该使用对话框元素

> 原文：<https://blog.logrocket.com/using-the-dialog-element/>

对话框是任何用户界面不可或缺的一部分，无论是网络、手机还是其他任何现有的界面。

你想从用户那里确认什么吗？您向用户显示一个带有可选选项的对话框。您想从用户那里收集信息吗？您使用一个带有可提交表单输入的对话框。在用户界面中使用对话框有很多用例。

您可以通过在项目中安装 JavaScript 库来使用对话框，并且在您使用的任何一种框架中都可以使用。例如，如果您的项目在 React 中，您可以使用 [react-aria-modal](https://github.com/davidtheclark/react-aria-modal) 。对于有角度的，可以用 [ngx-smart-modal](https://github.com/maximelafarie/ngx-smart-modal) 。或者，如果您正在使用优秀的旧 jQuery，您可以使用 [jquery-modal](https://jquerymodal.com/) 。

第三方库很棒，但是它们会给你的项目带来额外的开销，不仅会增加一定的复杂性，还会增加你的应用程序的整体大小。

最重要的是，即使您只打算使用这个库的某些特性，您也要安装包含所有附加功能的整个库。这在我看来并不伟大。

这就是浏览器的本地元素发挥作用的地方。这是你在对话框中想要的一切，现在 Safari 从 v15.4 开始增加了对`<dialog>`元素的支持，没有任何理由不在生产中使用它们！

## 什么是`<dialog>`元素？

`<dialog>`是一个 HTML 元素，表示一个对话框，如警告、确认、检查器或子窗口。

一个非常基本的`<dialog>`元素应该是这样的:

```
<dialog>
  Hey, this is an HTML dialog!
</dialog>

```

因为默认情况下`<dialog>`是不可见的，所以我们需要传入一个`open`属性来使它可见:

```
<dialog open>
  Hey, this is an HTML dialog!
</dialog>

```

下面是它的样子:

参见 Amit Merchant([@ Amit _ Merchant](https://codepen.io/amit_merchant))
上的 [CodePen](https://codepen.io) 中的<基本对话框> 。

## 定制`<dialog>`的外观

您可能想知道，示例中的黑色边框是从哪里来的？答案是每个用户代理(浏览器)都会对`<dialog>`元素应用默认样式，如下所示:

![Default Dialog CSS](img/b8637a37d509d02e8e85917b3df9f21c.png)

对话框的设计可以很容易地定制。例如，如果你想删除默认边框，并添加一个阴影和一些细节，你可以这样做:

```
dialog {
  border: none;
  box-shadow: #00000029 2px 2px 5px 2px;
  border-radius: 10px;
  padding: 30px;
  background-color: pink;
  font-family: sans-serif;
  font-size: 20px;
  font-weight: bold;
}

```

这是自定义对话框的外观:

参见 Amit Merchant([@ Amit _ Merchant](https://codepen.io/amit_merchant))[code Pen](https://codepen.io)上的笔 [定制<对话框>](https://codepen.io/amit_merchant/pen/KKZMdpO) 。

添加交互性

## 在现实世界中，你不会像这样直接使用对话框。您可能希望在某个动作上触发对话框。比如说点击一个按钮。

看看这个例子:

正如您所看到的，我们有一个 ID 为`demoDialog`的对话框。现在，如果我们想通过点击**打开对话框**按钮来打开这个对话框，我们可以这样做:

```
<dialog id="demoDialog">
  Hey there! This is a dialog
  <button id="closeDialog">Close dialog</button>
</dialog>
<menu>
  <button id="openDialogButton">Open dialog box</button>
</menu>

```

要打开对话框，我们可以通过 JavaScript 编程调用`<dialog>`实例上的`showModal()`方法。但在此之前，最好先通过检查对话框的对象中是否存在`showModal`来检查浏览器是否支持`<dialog>`。

```
var openDialogButton = document.getElementById('openDialogButton');
var demoDialog = document.getElementById('demoDialog');
var closeDialog = document.getElementById('closeDialog');
// "Open dialog box" button opens the <dialog> 
openDialogButton.addEventListener('click', function () {
  if (typeof demoDialog.showModal === "function") {
    demoDialog.showModal();
  } else {
    console.log("The <dialog> API is not supported by this browser");
  }
});
closeDialog.addEventListener('click', function() {
  demoDialog.close();
})

```

我们可以调用**关闭对话框**上的`close()`方法来关闭`<dialog>`。

注意，您也可以使用 Esc 键关闭`<dialog>`。

以下是所有正在运行的内容:

看到笔 [在](https://codepen.io/amit_merchant/pen/KKZMdqw) [CodePen](https://codepen.io) 上打开一个<对话框>由 Amit Merchant([@ Amit _ Merchant](https://codepen.io/amit_merchant))
编程。

处理用户输入

如果您想在`<dialog>`中处理用户输入的输入，您可以在其中定义一个`<form>`，并将其`method`设置为`"dialog"`。

## 当表单提交后，对话框将自动关闭，然后，您可以像这样处理对话框的`close`事件上的输入值:

将所有这些放在一起:

```
<dialog id="collectEmail">
  <form method="dialog">
    <p>
      <label>
        Email:
        <input type="email" name="email" id="email" placeholder="Enter your email">
      </label>
    </p>
    <menu>
      <button value="cancel">Cancel</button>
      <button id="confirmBtn" value="default">Confirm</button>
    </menu>
  </form>
</dialog>
<menu>
  <button id="openEmailDialog">Submit user's email</button>
</menu>
<output aria-live="polite"></output>

```

参见 [CodePen](https://codepen.io) 上的笔 [<对话框>Amit Merchant(](https://codepen.io/amit_merchant/pen/RwxRyvP)[@ Amit _ Merchant](https://codepen.io/amit_merchant))
输入演示。

```
collectEmail.addEventListener('close', function() {
  let email = document.getElementById('email').value;
  outputBox.innerHTML = `User's email: <b>${email}</b>`;
});

```

更多的调整

更改`<dialog>`的背景

也可以使用`::backdrop`伪 CSS 属性改变对话框打开时的背景:

## 这看起来是这样的:

### 参见 [CodePen](https://codepen.io) 上的笔 [<对话框>背景](https://codepen.io/amit_merchant/pen/popbVYa)由 Amit Merchant([@ Amit _ Merchant](https://codepen.io/amit_merchant))
。

It’s also possible to change the dialog’s backdrop when it’s opened using the `::backdrop` pseudo CSS property:

```
dialog::backdrop {
  background-color: #673ab752;
}

```

点击 *`<dialog>`* 外部时关闭

默认情况下，当`<dialog>`打开时，在对话框区域之外点击时无法关闭。如果我们想引入这种行为，我们可以通过点击对话框上的`getBoundingClientRect()`来获得对话框的 [DOMRect](https://developer.mozilla.org/en-US/docs/Web/API/DOMRect) 。

然后，我们可以获得单击的坐标，并检查它是否落在对话框的矩形之外。基于此，我们可以这样调用对话框的`close()`事件:

### 下面是实际的行为:

参见 [CodePen](https://codepen.io) 上 Amit Merchant([@ Amit _ Merchant](https://codepen.io/amit_merchant))
的笔 [<对话框>演示](https://codepen.io/amit_merchant/pen/ZEagBvV)。

We can then get the coordinates of the click and check if it falls outside of the dialog’s rectangle. And based on this, we can call the dialog’s `close()` event like so:

```
collectEmail.addEventListener("click", event => {
    const rect = collectEmail.getBoundingClientRect();
    if (event.clientY < rect.top || event.clientY > rect.bottom ||
        event.clientX < rect.left || event.clientX > rect.right) {
        favDialog.close();
    }
});

```

尝试在对话框外单击！

最后

这就是原生 HTML `<dialog>`元素的全部内容，它现在已经得到了主流浏览器的广泛支持，包括我们钟爱的 Safari 浏览器！

所以，如果你有需要使用对话框的用例，并且最终使用了`<dialog>`元素，这是一个显而易见的事情。编码快乐！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

## LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Then, use session replay with deep technical telemetry to see exactly what the user saw and what caused the problem, as if you were looking over their shoulder.

LogRocket automatically aggregates client side errors, JS exceptions, frontend performance metrics, and user interactions. Then LogRocket uses machine learning to tell you which problems are affecting the most users and provides the context you need to fix it.

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).