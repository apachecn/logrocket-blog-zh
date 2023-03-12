# iframes 的最终指南

> 原文：<https://blog.logrocket.com/the-ultimate-guide-to-iframes/>

iframe 元素(inline frame 的缩写)可能是最古老的 HTML 标记之一，由 Microsoft Internet Explorer 在 1997 年随 HTML 4.01 引入。

尽管所有现代浏览器都支持它们，但许多开发人员写了无数文章建议不要使用它们。

我相信他们的坏名声不应该阻止你依赖他们。它们有许多合法的用例。

此外，保护它们并不难，所以你不必担心你的用户的计算机被感染。

为了帮助你形成自己的观点并提高你的开发技能，我们将涵盖你应该知道的关于这个有争议的标签的所有要点。

我们将介绍 iframe 元素提供的大多数特性，并讨论如何使用它们，以及 iframe 如何有助于克服一些棘手的情况。最后，我们将讨论如何保护 iframe 以避免潜在的漏洞。

### 什么是 iframe，什么时候使用它？

开发人员主要使用 iframe 标签在当前文档中嵌入另一个 HTML 文档。

当你不得不在你的网站上包含一个第三方部件(比如著名的脸书赞按钮)，一个 YouTube 视频，或者一个广告部分的时候，你可能会碰到它。

例如，下面的代码将显示一个 500 像素的正方形，其中包含 google 主页:

```
<iframe src="https://www.google.com/" height="500px" width="500px"></iframe>
```

这是另一个例子，我们显示了一个按钮来在 Twitter 上发布您的网页:

```
<iframe src="https://platform.twitter.com/widgets/tweet_button.html" style="border: 0; width:130px; height:20px;"></iframe>
```

在考虑 iframe 时，您必须记住的是，它允许您在浏览上下文中嵌入独立的 HTML 文档。

因此，它将从父对象的 JavaScript 和 CSS 中分离出来。这是使用 iframe 的有效目的之一:提供应用程序和 iframe 内容之间的隔离措施。

尽管如此，正如您将在本指南中看到的，这种分离并不完美。

iframe 仍然会以令人讨厌或恶意的方式运行:例如触发弹出窗口或自动播放视频。

为了说明这种与 JavaScript 和 CSS 的隔离是多么方便，让我们看一下这两种情况:

在应用程序中，用户可以创建电子邮件并将其保存为模板。在特定的页面上，我需要列出它们，让他预览并选择一个。

但是，为了防止当前网站的 CSS 影响这些模板的样式，我发现使用带有`srcdoc`属性的 iframe 是最干净的解决方案。

```
<iframe srcdoc="<html><body>The content you see here will never be affected by the CSS of its parent container. It supposed to be rendered in black on a white background.</body></html>"></iframe>
```

iframes 救了我一命的另一种情况是，我必须为客户构建一个 WYSIWYG 编辑器。但是这些编辑器的问题是，当用户点击界面上的所有按钮时，你必须找到一种方法来保持焦点和选择。

因为 iframe 提供了一个隔离的环境，这意味着当您在它之外单击时，焦点或选择永远不会丢失。

通过使用 iframe 和父类之间的通信事件(本文后面将详细介绍如何实现)，我很快就设计出了一个功能强大的编辑器。

### 你需要知道的属性

直到今天，我们还可以使用八个属性来定制 iframe 的行为或样式。

```
<iframe

  src="https://google.com/"    <!-- Sets the address of the document to embed --> 

  srcdoc="<p>Some html</p>"    <!-- Sets the HTML content of the page to show --> 

  height="100px"               <!-- Sets the iframe height in pixels -->

  width="100px"                <!-- Sets the iframe width in pixels -->

  name="my-iframe"             <!-- Sets the name of the iframe (mainly used to reference the element in JavaScript -->

  allow="fullscreen"           <!-- Sets the feature policy for the iframe. -->

  referrerpolicy="no-referrer" <!-- Set the referrer to send when fetching the iframe content -->

  sandbox="allow-same-origin"  <!-- Sets the restrictions of the iframe (more on this below) -->

></iframe>
```

你可能会发现不止上面列出的这些，但是记住 HTML5 不再支持它们:`align`、`frameborder`、`longdesc`、`marginheight`、`marginwidth`和`scrolling`。

**注意**:默认情况下，iframe 元素周围有边框。要移除它，可以使用 style 属性将 border CSS 属性设置为 none。

```
<iframe src="https://logrocket.com/" style="border: none;"></iframe>
```

### iframe 事件和通信

**加载和错误**

因为 iframe 是一个文档，所以您可以使用大多数的[全局事件处理程序](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers)。

当您启动 iframe 时，它们中的两个可以方便地改善体验，比如显示微调器或特定消息来帮助用户:

您可以分别使用`onload`和`onerror`属性来收听它们:

```
<iframe src="https://logrocket.com/" onload="onLoad()" onerror="onError()"></iframe>
```

或者是否可以通过编程方式将侦听器添加到 iframe 中。

```
// For a new iframe
const iframe = document.createElement("iframe");

iframe.onload = function() {
  console.log("The iframe is loaded");
};
iframe.onerror = function() {
  console.log("Something wrong happened");
};

iframe.src = "https://logrocket.com/";
document.body.appendChild(iframe);

// For an existing iframe
const iframe = document.querySelector('.my-iframe');

iframe.onload = function() {
  console.log("The iframe is loaded");
}
iframe.onerror = function() {
  console.log("Something wrong happened");
}
```

### **与 iframes 的通信**

在父框架和 iframe 之间发送消息是非常容易的。你必须使用`postMessage`函数，这里的[记录了](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage)。

**从父到 iframe**

从父元素发送消息:

```
const myiframe = document.getElementById('myIframe')

myIframe.contentWindow.postMessage('message', '*');
```

听听 iframe 中的内容:

```
window.onmessage = function(event){
    if (event.data == 'message') {
        console('Message received!');
    }
};
```

**从 iframe 到父级**

从 iframe 发送消息:

```
window.top.postMessage('reply', '*')
```

听听父母是怎么说的:

```
window.onmessage = function(event){
    if (event.data == 'reply') {
        console('Reply received!');
    }
};
```

**注意**:请记住，当您需要调试消息时，您可能会陷入一些棘手的情况(即，没有真正的错误处理)。

### 安全性

当您使用 iframe 时，您主要是在处理来自第三方的内容，而您无法控制这些内容。

因此，您增加了应用程序中存在潜在漏洞的风险，或者只是不得不处理糟糕的用户体验(比如烦人的视频自动播放)。

幸运的是，您可以将特定功能列入黑名单或白名单。

您必须使用我们之前讨论过的属性`sandbox`和`allow`。

> 请记住，一个好的经验法则是始终授予资源完成其工作所需的最低级别的能力。安全专家将这一概念称为“最小特权原则”

**沙盒属性**

以下是沙盒标志及其用途的完整列表:

| 旗 | 细节 |
| --- | --- |
| **允许表单** | 允许表单提交。 |
| **允许模态** | 允许资源打开新的模式窗口。 |
| **允许定位锁定** | 允许资源锁定屏幕方向。 |
| **允许指针锁定** | 允许资源使用指针锁 API。 |
| **允许弹出窗口** | 允许资源打开新的弹出窗口或选项卡。 |
| **允许弹出窗口逃离沙盒** | 允许资源打开不继承沙盒的新窗口。 |
| **允许演示** | 允许资源开始演示会话。 |
| **允许同源** | 允许资源保持其原始状态。 |
| **允许脚本** | 允许资源运行脚本。 |
| **允许顶部导航** | 允许资源在顶级浏览上下文中导航。 |
| **允许用户激活顶部导航** | 允许资源在顶级浏览上下文中导航，但仅当由用户手势启动时。 |

由您来定义可以授予每个 iframe 哪些特权。

例如，如果您的 iframe 只需要提交表单和打开新的模式窗口，下面是您将如何配置沙箱属性:

```
<iframe sandbox="allow-forms allow-modals" src="https://www.something.com/"></iframe>
```

对于配置了沙盒属性的情况，如果某个功能在资源中无法正常工作，可能是因为它缺少特定的标志。

确保您对它们有更多的了解，以便快速调试。

此外，请记住，使用空沙盒属性将完全沙盒 iframe。

这意味着 iframe 中的 JavaScript 将不会被执行，上面列出的所有权限都将受到限制(比如创建新窗口或加载插件)。

空沙盒属性主要用于静态内容，但会大大降低其他资源正常工作所需的能力。

```
<iframe sandbox="allow-forms allow-modals" src="https://www.something.com/"></iframe>
```

**注意**:Internet Explorer 9 及更早版本不支持沙盒属性。

**`allow`属性**

这个`allow`属性目前还处于试验阶段，只有基于 Chromium 的浏览器支持。它允许您允许白名单特定的功能，如让 iframe 访问加速度计、电池信息或相机。

有超过 25 个可用的标志，所以我不会在这里列出它们。你可以在 Mozilla 特性政策文档上浏览它们[。我在下表中总结了最流行的:](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Feature-Policy#Directives)

| 旗 | 细节 |
| --- | --- |
| **加速度计** | 允许访问加速度计接口 |
| **环境光传感器** | 允许访问 AmbientLightSensor 界面 |
| **自动播放** | 允许自动播放视频和音频文件 |
| **电池** | 允许访问电池状态 API |
| **摄像机** | 允许使用摄像机 |
| **全屏** | 允许访问全屏模式 |
| **地理定位** | 允许访问地理位置 API |
| **陀螺仪** | 允许访问传感器 API 陀螺仪接口 |
| **磁力计** | 允许访问传感器 API 磁力计接口 |
| **麦克风** | 允许访问设备麦克风 |
| **midi** | 允许访问 Web MIDI API |
| **付款** | 允许访问付款申请 API |
| **usb** | 允许访问 WebUSB API |
| **振动** | 允许访问振动 API |

### 关于 iframes 要知道的事情

**如何处理不支持 iframes 的浏览器**

如果浏览器不支持 iframe，它将显示开始的`<iframe>`标签和结束的`</iframe>`标签之间的内容。

因此，您应该始终考虑放置一条警告消息，作为那些贫穷用户的退路。

```
<iframe>
  <p>Your browser does not support iframes.</p>
</iframe>
```

如何让 iframe 看起来像是父文档的一部分(例如，没有边框和滚动条)？

🤓`seamless`属性就是为了这个目的而引入的。它仍然是实验性的，在浏览器中支持很差(只有基于 Chromium 的人理解它)。

在撰写本文时，它也不是 W3C HTML5 规范的一部分。

```
<iframe seamless src="https://logrocket.com/"></iframe>
```

iframes 会影响我网站的搜索引擎优化吗？

我对这个不太了解，所以我不得不挖掘一下。围绕这个话题有很多猜测。

很长一段时间，爬虫无法理解它们，但现在不再是这样了。我找到的最相关的答案是这篇文章中的[，今天的结论似乎是:](https://www.boostability.com/the-affect-of-iframes-on-seo/)

> 因为搜索引擎认为 iframes 中的内容属于另一个网站，所以你能期望的最好结果是没有效果。Iframes 既不会帮助也不会伤害你的搜索引擎排名。

因此，最好假设通过 iframes 显示的内容可能不会被编入索引或出现在 Google 的搜索结果中。一个解决办法是确保提供额外的基于文本的链接到他们显示的内容，以便 Googlebot 可以抓取和索引这些内容。

注意:你也不应该担心重复内容的问题，因为今天的网络爬虫通常能识别它们。

iframes 会影响我的网站的加载速度吗？

页面上的每个 iframe 都会增加使用的内存和其他计算资源，比如带宽。

所以，你不应该在没有监控发生了什么的情况下过度使用 iframe，否则你可能会损害你的页面性能。

为了避免 iframes 降低页面速度，一个好的方法是延迟加载(也就是说，只在需要的时候加载，比如当用户在页面附近滚动时)。

这可以很容易地通过给标签添加`loading="lazy"`属性来实现。

请记住，在撰写本文时，所有现代的基于 Chromium 的浏览器都支持这一点。你会对 [lazyload 库](https://github.com/verlok/lazyload)感兴趣，因为它可以在任何地方工作。

```
<iframe src="https://logrocket.com/" loading="lazy"></iframe>
```

**注意**:`loading="lazy"`属性也和`img`标签一起工作，如果你还不知道的话。😜

### 如何让 iframe 具有响应性？

随着越来越多的人使用他们的手机浏览网页，确保你的每一个界面都能响应是很重要的。

但是，当您的页面中有一个 iframe 时，您怎么能这样做呢？

我们可以提供一个完整的指南，介绍让 iframe 具有响应性的无数方法。相反，我将只链接到两篇优秀的文章:

**注意**:如果你在你的项目中使用引导库，你可以直接使用`embed-responsive`和`embed-responsive-16by9`来使你的 iframes 具有响应性。

```
<div class="embed-responsive embed-responsive-16by9">
  <iframe src="https://logrocket.com/" loading="lazy"></iframe>
</div>
```

### **如何防止加载 iframe 时出现白色闪光**

是的，我的朋友们，这有一个解决办法。[在这篇文章](https://css-tricks.com/prevent-white-flash-iframe/)，[克里斯·科伊尔](https://twitter.com/chriscoyier)分享了一个小片段，它用一些 CSS 隐藏了页面上的所有 iframes，并删除它，直到窗口被加载，然后使它们可见。

### **如何重新加载 iframe 的内容**

简单的柠檬汽水！因为可以用`contentWindow`访问 iframe 的窗口元素，所以必须这样做:

```
// Get the iframe
const iframe = document.getElementById('myIframe');

// Reload the iframe
iframe.contentWindow.location.reload();
```

### 额外收获:关于 iframe 可访问性

大多数情况下，屏幕阅读器会指出页面上存在 iframe。

许多甚至能让你在里面导航。如果您的 iframe 不包含其他网页，而是包含外部内容，如视频播放器或广告，则有必要为标签添加标题。

给标签添加一个标题给用户更多关于 iframe 的上下文。

```
html
<iframe src="an_ad.html" title="I contain an advertisement"></iframe>
```

有些人会说这个属性并不重要，因为大多数屏幕阅读器只会在文档标题可用时读取它，而忽略 iframe 标签上的 title 属性。

然而，为了在不同的屏幕阅读器上正确地阅读同一标题，提供两者并确保它们相互匹配仍然是一种好的做法。

另一件要记住的事情是，当你的 iframe 包含不可读的内容时(比如用 JavaScript 构建的视频游戏)，你必须使用 aria-hidden 属性对屏幕阅读器用户隐藏它的内容。

```
html
<iframe src="a_video_game.com" title="I contain a video game" aria-hidden="true"></iframe>
```

我们在本指南的开头谈到了这一点，但是请确保在 iframe 中包含一些内容，以供所有不支持它们的旧浏览器使用。

这将为每个人提供更多关于空间中应该展示什么的背景信息。

```
html
<iframe>
  <p>Your browser does not support iframes.</p>
</iframe>
```

## LogRocket :监控 iframe 的性能以及用户如何与它们交互

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://logrocket.com/signup/)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。在实现 iframes 时，理解它们如何影响整个应用程序或页面的性能以及用户体验是至关重要的。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

## 结论

我希望本指南有助于提高您对 iframes 的了解。

虽然如果你加载的是不可信的内容，它们可能是不安全的，但是它们也提供了一些显著的优势。所以你不应该完全禁止它们出现在你的开发库中，而应该只在相关的情况下使用它们。

如果你对这篇文章有什么要补充的，你可以在下面的评论中找到我，或者在 Twitter 上发短信给我