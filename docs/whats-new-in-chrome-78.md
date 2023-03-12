# Chrome 78 有什么新内容

> 原文：<https://blog.logrocket.com/whats-new-in-chrome-78/>

Chrome 78 稳定版将于 2019 年 10 月 22 日发布。很少有用户会注意到这些变化，但是对于 web 开发人员来说，有一些令人兴奋的新特性…

## CSS 不透明度百分比

opacity 属性当前接受 0(完全透明)和 1(完全不透明)之间的值。Chrome 78 还允许从 0%到 100%的百分比:

```
/* identical styles */
.style1 { opactity: 0.75; }
.style2 { opacity: 75%; }
```

这给 CSS 带来了一些急需的一致性，因为 rgba()函数已经接受了一个百分比，例如`rgba(100, 150, 200, 75%)`。

百分比也可以用于 SVG 属性 stop-opacity、fill-opacity、stroke-opacity 和 shape-image-threshold 中设置的不透明度。

## JavaScript 可选链接

可选链接是一个 [ES2019 提案](https://tc39.es/proposal-optional-chaining/)，它将为您节省数小时的键入和调试工作！假设您需要分析一个用户对象，该对象是在 Ajax 请求 REST API 调用后返回的:

```
let user = {
  firstname: 'Imaginary',
  lastname: 'Person',
  address: {
    street1: '123 Madeup Street',
    street2: '',
    city: 'Nowhereville',
    country: 'Antarctica'
  }
};

// output "Antarctica"
console.log( user.address.country );
```

如果没有设置国家值会怎样？JavaScript 将返回 undefined。

不幸的是，如果没有定义地址或用户对象，情况会更糟——代码会引发一个错误，并阻止进一步的 JavaScript 代码运行。

因此，您的代码必须检查每个父属性是否存在:

```
let country = (user && user.address && user.address.country) || undefined;
```

或者更可怕的:

```
let country =
  (user ?
    (user.address ?
    (user.address.country ?
          user.address.country : undefined
    ) :
      undefined
    ) :
    undefined
  );
```

Chrome 78 中的可选链接允许更简洁的链接操作符:

```
let country = user?.address?.country;
```

如果任何值为 falsy (null、undefined 等), JavaScript 不会抛出错误，而是将 country 变量设置为 undefined。)可以附加更多的方法而没有失败的风险:

```
let countryLength = user?.address?.country?.length;
```

虽然可选链接非常有用，但目前没有其他浏览器或 Node.js 支持这种语法。巴别塔插件将是必要的，直到支持变得更加广泛。

## 国际化 API 更新

Intl 对象提供特定于地区的字符串比较、数字格式、日期和时间格式，这在全球市场运营时特别有用。该 API 具有跨浏览器的合理支持，并慢慢出现在 Node.js 中。

```
// set date to 31 December 2020
let date = new Date(2020, 11, 31);

// outputs US date format - 12/31/2020
console.log( new Intl.DateTimeFormat('en-US').format(date) );

// outputs UK date format - 31/12/2020
console.log( new Intl.DateTimeFormat('en-GB').format(date) );

// set number
let num = 12345.67;

// output US number format - 12,345.67
console.log( new Intl.NumberFormat('en-US').format(number) );

// output German number format - 12.345,67
console.log( new Intl.NumberFormat('de-DE').format(number) );
```

[DateTimeFormat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/DateTimeFormat) 和 [NumberFormat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat) 接受可选的 options 对象作为第二个参数。Chrome 78 为使用两个或更多日历或编号系统的地区添加了`calendar`和`numberingSystem`选项。

## 深色图案限制

当用户离开页面时，触发[窗口卸载](https://developer.mozilla.org/en-US/docs/Web/API/Window/unload_event)事件:

```
window.addEventListener('unload', () => {
  // do something when the user leaves the page
});
```

类似的事件可以登记为:

*   [before unload](https://developer.mozilla.org/en-US/docs/Web/API/Window/beforeunload_event)–文档可见，即将被卸载，但事件仍可被取消
*   [页面隐藏](https://developer.mozilla.org/en-US/docs/Web/API/Window/pagehide_event)–浏览器正在导航至会话历史中的不同页面，例如，点击了后退按钮
*   [可见性变化](https://developer.mozilla.org/en-US/docs/Web/API/Document/visibilitychange_event)–用户切换到当前标签页或从当前标签页切换

通常，这些事件可用于检查已保存的数据或记录使用分析。

不幸的是，一些不太尽责的开发人员会添加代码来损害用户体验。从版本 78 开始，Chrome 将不允许:

1.  同步 Ajax 请求——这可以在`AllowSyncXHRInPageDismissal`策略标志中被覆盖，但是在 82 版中该标志也将被删除
2.  使用`window.open()`生成弹出窗口–弹出窗口拦截器通常会阻止这种情况，但现在即使拦截器处于非活动状态，它也会被禁止

## 独立滚动偏移

在 Chrome 78 之前， [window.scrollTo()](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollTo) 方法和滚动属性如 [scrollTop](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollTop) 和 [scrollLeft](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollLeft) 会计算最近的物理像素。例如，在双像素密度设备(devicePixelRatio 为 2)上，`window.scrollTo` (0，123.678)和`window.scrollTop`将等于 123.5。

从 Chrome 78 开始，不管物理像素是多少，都会返回实际传递的值(123.678)。这应该有助于防止滚动计算问题，尤其是当页面被缩放时。

## 用户定时级别 3

用户计时 API 允许开发人员通过创建自定义时间戳来测量应用程序性能。命名性能标记是在整个代码的关键点创建的，例如

```
performance.mark('markstart');
// ...processing...
performance.mark('markend');
```

然后，性能测量可以报告两个标记之间的持续时间，例如

```
performance.measure('m1', 'markstart', 'markend');
performance.getEntriesByName('m1');

/*
Returns something like:
(1) [...]
  0: PerformanceMeasure
     duration: 5153
     entryType: "measure"
     name: "m1"
     startTime: 7314
*/
```

第 3 级允许开发人员将自定义时间戳和任意元数据传递给性能标记和测量方法。

## 付款 API 更新

[支付请求 API](https://developer.mozilla.org/en-US/docs/Web/API/Payment_Request_API) 旨在为商家和客户提供一致且安全的在线交易体验。它允许用户选择他们喜欢的支付选项，并传递到电子商务网站。

Chrome 78 引入了几个新选项:

*   [PaymentRequest](https://developer.mozilla.org/en-US/docs/Web/API/PaymentRequest) 对象的 [hasEnrolledInstrument()](https://gist.github.com/rsolomakhin/d6d242cbb9306864ada5a29de7ab271e) 方法检查自动填写的支付数据，以确保其有效且完整。例如，它不允许选择过期的信用卡
*   [PaymentResponse](https://developer.mozilla.org/en-US/docs/Web/API/PaymentResponse) 对象的 [retry()](https://developer.mozilla.org/en-US/docs/Web/API/PaymentResponse/retry) 方法允许客户在出现处理错误时重试支付
*   payment request[shipping address change](https://developer.mozilla.org/en-US/docs/Web/API/PaymentRequest/shippingaddresschange_event)事件将送货地址发送给商家，以便计算送货成本、税费等。由于买家尚未承诺购买，他们的地址被编辑以删除收件人姓名、组织、完整地址和电话号码，这些都是运输计算不需要的

## WebSocketStream

[WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket) 在浏览器和服务器之间创建一个双向通信通道。这两种设备都可以在任何时候发送消息，因此该技术经常用于聊天室、多人游戏和实时数据更新。

不幸的是，消息到达的速度可能超过浏览器的处理能力。在这些情况下，内存缓冲区可能会溢出，或者 CPU 使用率会增加到浏览器无法响应的程度。

WebSocketStream 取代了 WebSocket API。它是基于承诺的，并集成了流，因此在收到完整消息之前可以解析大块数据。

该 API 是新的、实验性的，在成为 W3C 标准之前可能会有所发展或被放弃。更多信息，请参考 [WebSocketStream 解释](https://github.com/ricea/websocketstream-explainer/blob/master/README.md)。

## 媒体元素 seekto 事件

HTML 音频和视频媒体元素记录许多事件，例如:

*   [播放](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play_event)–当视频自动启动或暂停后恢复播放时触发
*   [时间更新](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/timeupdate_event)–播放时间已经改变
*   [音量改变](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/volumechange_event)–音量已被改变

Chrome 78 引入了一个新的 seekto 动作处理程序，当回放移动到时间轴上的特定点时会调用该处理程序。这可以用来改变 DOM，记录分析等。在撰写本文时，其他浏览器中没有任何文档或支持。

## 屏幕枚举 API

屏幕枚举 API 提供关于连接到用户设备的所有显示器的信息。它通常在应用程序可以使用多个监视器的情况下非常有用，例如提供面向公众的幻灯片和演讲者注释显示的演示文稿。

目前没有可用的文档，但它将与新的[窗口放置 API](https://github.com/spark008/window-placement/blob/master/EXPLAINER.md) 结合使用。

## 本地文件系统 API

[本地文件系统 API](https://developers.google.com/web/updates/2019/08/native-file-system) 允许浏览器直接与用户本地设备上选择的文件进行交互。它可以用来从客户端 JavaScript 编辑照片、视频或文本文档，而不需要上传和下载过程。

Chrome 78 提供了一个早期版本的 API 供试用，开发者必须从谷歌注册一个令牌才能使用。

## SMS 接收器 API

SMS 文本消息通常用于验证电话号码或发送一次性密码(OTP)代码。原生智能手机应用程序可以拦截、读取和回应这些信息，但 web 应用程序用户被迫手动复制/粘贴或重新输入信息。

新的 [SMS 接收器 API](https://github.com/samuelgoto/sms-receiver) 允许网络应用使用特定的格式约定读取发送给它们的 SMS 消息，以避免手动用户交互，例如

```
Your OTP is: 123ABC
For: https://example.com/verify?otp=123ABC&hash=9B3FF1C2
```

检索 JavaScript:

```
if (navigator.sms) {

  try {
    let { content } = await navigator.sms.receive();
    console.log('SMS text:', content);
  } catch (e) {
    console.log('error', e);
  }

}
```

## 杂项更新

如果您想了解更多，还可以使用一些次要的和实验性的功能:

1.  1.  输入和文本区域[::占位符](https://developer.mozilla.org/en-US/docs/Web/CSS/::placeholder)文本的默认颜色已从#757575 更改为 rgba(0，0，0，0.54)
    2.  使用 [importScripts()](https://developer.mozilla.org/en-US/docs/Web/API/WorkerGlobalScope/importScripts) 导入的服务工作者脚本现在可以检查更新，即使主服务工作者脚本没有被修改
    3.  可以从 JavaScript 调用一个 [registerProperty()](https://developer.mozilla.org/en-US/docs/Web/API/CSS/RegisterProperty) 函数来注册类型化和可动画化的自定义 CSS 属性(新 Houdini API 的一部分)
    4.  [WebAssembly 模块可以保存对 JavaScript 和 DOM 对象的引用](https://github.com/WebAssembly/reference-types/blob/master/proposals/reference-types/Overview.md)。它们可以作为参数传递，存储在局部变量、全局变量或`WebAssembly.Table`对象中
    5.  新的 [WebHID(人机界面设备)API](https://github.com/WICG/webhid/blob/master/EXPLAINER.md) 支持除键盘、鼠标、触摸屏和游戏手柄之外的输入和输出设备。本质上，它允许在 JavaScript 中实现低级的特定于设备的逻辑，而不依赖于浏览器支持
    6.  XSS 审计员已被撤职

## 这里有龙！

令人鼓舞的是，尽管 Chrome 浏览器占据垄断性的市场份额，但它仍在不断创新。

虽然很容易让人一头扎进去，但许多新功能都是实验性的，可能会彻底改变、消失，或者永远不会在其他浏览器中实现。实验是健康的，但要警惕把你的声誉赌在谷歌的突发奇想上！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)