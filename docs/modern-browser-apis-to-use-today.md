# 今天使用的现代浏览器 API-log rocket 博客

> 原文：<https://blog.logrocket.com/modern-browser-apis-to-use-today/>

近来，web 变得越来越强大，开发者和用户都从这些进步中受益。特别是，由于有了大量的现代浏览器 API，许多事情都变得非常容易实现。

## 什么是 API？

应用程序编程接口，通常称为 API，是指定各种软件组件应该如何交互的指令集或功能集。有多种类型的 API 可以帮助开发人员执行各种任务或利用各种服务。例子包括 GitHub API，它可以用来访问 repos、用户等等，还有 Twitter API，它可以帮助发送 tweets。

在这篇文章中，我们将关注那些帮助我们在浏览器上做出惊人之举的 API。

## 什么是浏览器 API？

在今天的网络上，我们可以构建应用程序，利用以前只有本地应用程序才知道的功能。我们现在可以构建使用本地对话框、系统通知等等的 web 应用程序。所有这些都是通过浏览器制造商添加现代浏览器 API 而成为可能的。

让我们来看看其中的几个。

### 页面可见性 API

页面可见性 API 为我们提供了检测用户何时离开当前选项卡的能力，无论是通过切换到另一个选项卡、窗口还是应用程序。我们还可以检测用户何时返回页面。

这个 API 发送一个`visibilitychange`事件，让事件监听器知道页面的状态已经改变，任何动作或者指令都可以根据状态来执行。在您只想在用户主动查看页面时执行任务的情况下，这很方便，例如，仅当用户在页面上时轮询服务器更新。

```
function pageChanged() {
        if (document.hidden) {
            console.log('User is on some other tab/out of focus') // line #1
        } else {
            console.log('Hurray! User returned') // line #2
        }
    }
document.addEventListener("visibilitychange", pageChanged);
```

### 获取 API

Fetch API 帮助我们发出网络请求，并替换`XMLHttpRequest()`。无论您试图向服务器请求数据还是发送数据，Fetch API 都会派上用场。Fetch API 是非常可定制的；我们可以在请求中包含我们自己的头，它返回一个承诺，这样我们就可以将`.then()`和`.catch()`方法链接到请求。

```
// url (required), options (optional)
fetch('/foo/bar', { method: 'get' }).then(function(response) {
//Do something with your response
}).catch(function(err) {
//Handle any errors here
});
```

### 广播频道 API

广播频道 API 提供了在多个浏览器上下文(跨选项卡、窗口或框架)和具有相同来源的工作人员之间进行基本通信的能力。考虑下面的用例。

您打开了多个相同来源的标签页(例如，Google Docs)。如果你在一个标签上注销，切换到另一个相同来源的标签，它会通知你你已经注销，需要重新登录。这些选项卡通过创建同名的`BroadcastChannel`对象来加入同一个广播。

```
const bc = new BroadcastChannel('my_channel');

//sending a message across connected clients
bc.postMessage("This is an example message");
```

有一个处理程序可以在发布消息的任何时候监听触发的事件。这是`onmessage()`事件处理程序。

```
bc.onmessage = function (event) { console.log(event); }
```

### 服务人员 API

服务人员正在改变我们构建 web 应用程序的方式。它们基本上充当代理服务器，位于 web 应用程序、网络和浏览器之间。它们有助于根据网络可用性采取行动，如提供有效的离线体验、拦截网络请求、推送通知等。

当用户第一次访问服务工作者控制的网站或页面时，服务工作者被立即下载、安装和激活。

## 更多 API

当然，还有许多其他的浏览器 API 可以帮助我们完成工作，使得在我们的 web 应用程序中构建和实现以前不可能实现的功能变得更加容易。这些 API 涵盖不同的类别，如下所示:

### 用于操作文档结构的 API

已经创建了许多这样的 API 来帮助我们以更独特和有效的方式操作和呈现文档。

诸如[文档对象模型(DOM)](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model) 之类的 API 提供了将网页连接到脚本的节点，以及对文档树的直接可编程访问。 [CSS 对象模型(CSSOM)](https://developer.mozilla.org/en-US/docs/Web/API/CSS_Object_Model) 允许从 JavaScript 操纵 CSS。使用 CSSOM，我们可以动态地以编程方式更新 CSS，就像我们使用 DOM 一样。

过去，拖放界面也需要使用第三方库，比如 jQuery。现在使用 [HTML 拖放 API](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API) 可以做到这一点。

### 用于绘制和操作图形的 API

网络最初只是为文本而设计的。但是从那以后我们已经走了很长的路。浏览器现在更多地支持在浏览器上绘制图形。 [WebGL API](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API) 和 [Canvas API](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API) 都提供了使用 JavaScript 和 HTML 绘制图形的方法。

Canvas API 主要关注 2D 图形，而 WebGL API 绘制硬件加速的 2D 和 3D 图形。

### 设备 API

今天，我们可以通过各种设备访问网络，从台式电脑到手机，甚至手表。已经开发了许多 API 来帮助扩展和塑造 web 的功能，以匹配这些无数现代设备的功能。

像[地理定位 API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API) 这样的 API 利用设备的能力来精确定位用户的位置。[振动 API](http://www.w3.org/TR/vibration/) 允许开发者通过利用设备的振动功能向用户提供非听觉反馈。

```
// vibrate the device for 500ms
window.navigator.vibrate(500);
```

另一个 API 是[环境光事件 API](https://developer.mozilla.org/en-US/docs/Web/API/Ambient_Light_Events) 。这仍然是实验性的，但它可以让你检测到光强度的变化。当试图在您的应用程序中实现白天和夜晚模式之间的自动切换时，这很方便。

```
if ('ondevicelight' in window) {
        window.addEventListener('devicelight', function(event) {
            // Get the ambient light level in lux.
            console.log(event.value);
        });
    } else {
        console.log('devicelight event not supported');
    }
```

### 存储 API

存储标准提供了一个共享存储系统，旨在供所有存储 web 应用程序数据的技术使用。这个系统被像 [Web 存储 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) 这样的 API 所利用，它允许浏览器以比使用 cookies 更直观的方式存储键值对。

Web 存储 API 利用`sessionStorage`和`localStorage`，前者在会话期间保存数据，后者甚至在浏览器或标签关闭并重新打开后仍保留数据。其他 API 包括[缓存 API](https://developer.mozilla.org/en-US/docs/Web/API/Cache) 和[索引数据库 API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) ，它们提供了存储大量结构化数据的解决方案。

### 视频和音频 API

与所有其他方面一样，web 管理、显示和创建不同媒体类型(包括视频和音频)的能力以指数速度增长。已经有相当多的 API 可用于有效地执行与媒体相关的操作。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

Web Audio API 提供了一个强大的系统来控制音频，并允许开发者选择音频源、为音频添加效果、创建音频可视化等等。 [WebRTC(网络实时通信)API](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API) 使得在互联网上的两个对等点之间传输实时音频和视频——以及传输任意数据——而不需要中介成为可能。

您还可以通过[媒体捕获和流 API](https://developer.mozilla.org/en-US/docs/Web/API/Media_Streams_API) 使用本地摄像机和麦克风捕获视频、音频和静态图像来捕获本地媒体。

## 结论

这并不是一个详尽的列表，而是旨在向您展示我们作为开发人员可以利用当今的 API 构建强大的 web 体验的当前和潜在能力。

要了解有关各种浏览器 API 的更多信息，包括一些仍在开发中且尚不可用的 API，您可以查看 [MDN Web API 文档](https://developer.mozilla.org/en-US/docs/Web/API),了解可能会使您的应用程序更容易实现梦想功能的规范。