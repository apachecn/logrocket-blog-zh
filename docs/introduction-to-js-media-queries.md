# 如何以及为什么使用 JavaScript 媒体查询

> 原文：<https://blog.logrocket.com/introduction-to-js-media-queries/>

CSS3 中首次引入的媒体查询构成了响应式 web 设计的核心组件。应该定制应用程序以适应每种类型的设备(例如，移动电话、平板电脑、笔记本电脑、台式计算机)的限制，并且媒体查询提供了一种基于正在其上查看应用程序的设备的大小来设置视窗尺寸的简单方法。

媒体查询不仅允许您根据屏幕大小改变视窗尺寸，还可以帮助您为不同的设备设置不同的样式属性，包括配色方案、字体样式、动作设置和动画、边框和间距，以及您能想到的几乎任何其他 CSS 属性。

一些前端开发人员初看时忽略的一个事实是 JavaScript 也支持媒体查询。虽然不如 CSS 媒体查询流行，但 JavaScript 媒体查询提供了灵活性和许多优势，这使它们成为某些用例的更好选择。

## JavaScript 媒体查询的优势

此时，您可能会想:为什么开发人员会选择 JS 媒体查询，而 CSS3 途径可以做到这一点？

JavaScript 媒体查询有两个主要优势。

1.  **灵活性:**您可以通过编程方式将媒体查询合并到您的 JavaScript 代码中，以便它们只在特定事件发生时或满足特定条件时被触发。对于仅使用 CSS3 的方法，媒体查询所描述的更改会在每个屏幕调整事件中生效。
2.  **便利性:** JavaScript 媒体查询使用的语法与您使用 CSS 时使用的语法相同。

考虑一下:如果您想动态地改变不同屏幕尺寸的属性，该怎么办？您可能还在挠头，坚持认为这样的东西会工作得很好:

```
// A function we want executed based on changes in screen size 
function foo() {
   if (window.innerWidth < 1024) { 
               /* whatever you want to do here */ 
     }
}
// Set up a listener 
window.addEventListener('changesize', foo);
```

在上面的代码块中，我们有一个基于`window.innerWidth`小于 1024(即桌面显示器的标准屏幕尺寸)的“if”语句。据推测，该方法应该在应用程序运行在比台式计算机小的设备上的任何时候运行。

不幸的是，这种方法成本很高，因为它会在每次调整大小时被触发，而不仅仅是当用户在手机或平板电脑上打开应用程序时。没错——这个方法将在用户手动调整桌面计算机屏幕大小时运行。过多的此类操作最终会导致应用程序延迟。

谢天谢地，我们有完美的 API 来处理动态情况和响应设计:向 [matchMedia](https://developer.mozilla.org/en-US/docs/Web/API/Window/matchMedia) API 问好。

## 如何使用 JavaScript 媒体查询

我们可以使用 matchMedia API，而不是像上面的例子那样将侦听器附加到 resize 事件。

Window 接口的 matchMedia()方法本质上是将侦听器附加到媒体查询，但并不响应窗口或屏幕大小的每次变化，从而显著提高性能。如果我们利用这种方法，我们只负责开发我们希望为屏幕调整执行的逻辑，而不必担心其他条件、验证和代码优化。

要使用这个 API，我们调用`window.matchMedia()`并传入一个媒体查询字符串，指定我们想要响应的屏幕大小。

```
// This media query targets viewports that have a minimum width of 320px
const mQuery = window.matchMedia('(min-width: 320px)')
```

matchMedia()方法[返回一个新的 MediaQueryList 对象](https://developer.mozilla.org/en-US/docs/Web/API/MediaQueryList)，在上面的例子中我们将其命名为 mQuery。该对象存储有关应用于特定文档的媒体查询的信息，以及事件驱动和即时匹配的支持方法。这允许我们在 resize 事件开始时触发自定义逻辑。

为了执行必要的调整大小逻辑，[我们需要检查 window.matches](https://developer.mozilla.org/en-US/docs/Web/API/MediaQueryList/matches) ，这是一个布尔属性，如果媒体查询匹配，则返回“true”，如果不匹配，则返回“false”。在我们的示例中，该属性告诉我们是否存在与指定条件的实际匹配(例如，屏幕的最小宽度是 320px)。

```
// Check whether the media query has been matched 
if (mQuery.matches) { 
    // Print a message to the console 
    console.log('Media query matched!') 
}
```

很简单，对吧？只有一个问题:window.matches 只能执行一次这种检查。为了促进一个反应灵敏的网页设计，我们希望不断地检查任何正在发生的变化。幸运的是，还有另一个工具可以与 windows.matches 配对来帮助我们实现这一点:[addListener()方法](https://developer.mozilla.org/en-US/docs/Web/API/MediaQueryList/addListener)。

## addListener()方法

matchMedia API 提供了一个 addListener()方法以及一个[对应的 removeListener()方法](https://developer.mozilla.org/en-US/docs/Web/API/MediaQueryList/removeListener)。当我们调用 addListener()时，我们会传入一个回调函数，该函数会在检测到媒体查询匹配状态发生变化时运行。这个回调函数是我们希望在 resize 事件上触发的函数:

```
// This media query targets viewports that have a minimum width of 320px
const mQuery = window.matchMedia('(min-width: 320px)')

function handleMobilePhoneResize(e) {   
   // Check if the media query is true
   if (e.matches) {     
        // Then log the following message to the console     
        console.log('Media Query Matched!')   
   } 
} 

// Set up event listener 
mQuery.addListener(handleMobilePhoneResize)
```

这种技术允许我们响应媒体查询的变化，并根据需要动态调用其他方法。这些动态调用的方法可以改变各种文档属性，比如字体样式、边框和间距、动画等等。

例如，如果您想要合并动态字体样式，您可以通过如下方式实现:

```
function changeFontStyleMobile(e) {
   // Check whether the media query has been matched
   if (e.matches) {
      // Change font size
      document.getElementById("p2").style.color="blue";
      var span = document.document.getElementById("span");
      span.style.fontSize = "25px";
      span.style.fontcolor = "white"; span.style.fontSize = "36px";
   }
}

// Set up event listener
mQuery.addListener(changeFontStyleMobile)
```

## 结论

现在，您应该对 JavaScript 中的媒体查询以及它们如何实现高效、自适应的设计有了基本的了解。matchMedia API 可以帮助您用 JavaScript 创建媒体查询，addListener()可以让您通过调用回调函数来构建响应性的跨平台体验。

定期轮询文档状态更改是一种低效且资源密集型的方法，最终会导致应用程序滞后。使用 matchMedia()使我们能够观察特定的文档，检测媒体查询条件的变化，并根据媒体查询状态以编程方式改变文档属性。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.