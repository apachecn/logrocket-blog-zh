# JavaScript 中的本地存储:一个完整的指南

> 原文：<https://blog.logrocket.com/localstorage-javascript-complete-guide/>

***编者按:**本帖更新于 2020 年 12 月 29 日。*

在本教程中，我们将向您展示如何使用`localStorage`机制和`Window.localStorage`属性，并回顾 JavaScript 中 web 存储的基础知识。

我们将详细介绍以下内容:

## 什么是 Web 存储 API？

[Web 存储 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API) 是一组使浏览器能够存储键值对的机制。它被设计得比使用 cookies 更直观。

键-值对表示存储对象，它类似于对象，只是在页面加载期间保持不变，并且始终是字符串。您可以像访问一个对象一样访问这些值，或者使用`getItem()`方法(稍后会详细介绍)。

### `sessionStorage`和`localStorage`有什么区别？

Web 存储 API 由两种机制组成:`sessionStorage`和`localStorage`。在页面会话期间，`sessionStorage`和`localStorage`都为每个可用的原点维护一个单独的存储区域。

`sessionStorage`和`localStorage`的主要区别在于`sessionStorage`只在浏览器打开时(包括页面重新加载或恢复时)维护一个存储区，而`localStorage`在浏览器关闭后继续存储数据。换句话说，当页面关闭时，存储在`sessionStorage`中的数据被清除，而存储在`localStorage`中的数据不会过期。

在本教程中，我们将关注如何在 JavaScript 中使用`localStorage`。

## JavaScript 中的`localStorage`是什么？

`localStorage`是一个属性，允许 JavaScript 站点和应用程序在 web 浏览器中保存键值对，并且没有截止日期。这意味着存储在浏览器中的数据即使在浏览器窗口关闭后仍将存在。

关于如何在 JavaScript 中使用`localStorage`的视觉复习，请看下面的视频教程:

[https://www.youtube.com/embed/LfeOLVGHiXI](https://www.youtube.com/embed/LfeOLVGHiXI)

视频

### `localStorage`存放在哪里？

在谷歌浏览器中，[网络存储](https://en.wikipedia.org/wiki/Web_storage)数据保存在用户档案子文件夹中的 SQLite 文件中。该子文件夹在 Windows 机器上位于`\AppData\Local\Google\Chrome\User Data\Default\Local Storage`，在 macOS 上位于`~/Library/Application Support/Google/Chrome/Default/Local Storage`

Firefox 将存储对象保存在名为`webappsstore.sqlite`的 SQLite 文件中，该文件也位于用户的 profile 文件夹中。

## 什么是`Window.localStorage`？

通过`[Window.localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)`属性可以使用`localStorage`机制。`Window.localStorage`是 JavaScript 中`[Window](https://developer.mozilla.org/en-US/docs/Web/API/Window)`接口的一部分，它代表一个包含 DOM 文档的窗口。

`Window`接口具有广泛的函数、构造函数、对象和名称空间。`Window.localStorage`是一个只读属性，返回对本地存储对象的引用，该对象用于存储只有创建它的源才能访问的数据。

## `localStorage`是如何工作的？

要在您的 web 应用程序中使用`localStorage`,有五种方法可供选择:

1.  `setItem()`:给`localStorage`添加键值
2.  `getItem()`:这是你从`localStorage`得到物品的方法
3.  `removeItem()`:从`localStorage`中按键删除一个项目
4.  `clear()`:全部清除`localStorage`
5.  `key()`:传递一个数字来检索一个`localStorage`的密钥

### `setItem()`:如何在`localStorage`中存储数值

顾名思义，这个方法允许你在`localStorage`对象中[存储值。](https://blog.logrocket.com/storing-retrieving-javascript-objects-localstorage/)

它有两个参数:一个键和一个值。稍后可以引用该键来获取附加到它的值。

```
window.localStorage.setItem('name', 'Obaseki Nosa');
```

其中`name`是键，`Obaseki Nosa`是值。还要注意，`localStorage`只能存储字符串。

要存储数组或对象，您必须将它们转换为字符串。

为此，我们在传递给`setItem()`之前使用`JSON.stringify()`方法。

```
const person = {
    name: "Obaseki Nosa",
    location: "Lagos",
}

window.localStorage.setItem('user', JSON.stringify(person));
```

### `getItem()`:如何从`localStorage`获取物品

要从 localStorage 获取项目，请使用`getItem()`方法。`getItem()`允许您访问存储在浏览器的`localStorage`对象中的数据。

`getItem()`只接受一个参数，即`key`，并以字符串形式返回`value`。

要检索用户密钥:

```
window.localStorage.getItem('user');
```

这将返回一个字符串，其值为:

```
“{“name”:”Obaseki Nosa”,”location”:”Lagos”}”
```

要使用该值，您必须将其转换回对象。

为此，我们使用了`JSON.parse()`方法，该方法将 JSON 字符串转换成 JavaScript 对象。

```
JSON.parse(window.localStorage.getItem('user'));
```

### `removeItem()`:如何删除`localStorage`个会话

要删除本地存储会话，请使用`removeItem()`方法。

当传递一个键名时，`removeItem()`方法从存储中删除这个键(如果它存在的话)。如果没有与给定键相关联的项，此方法将不执行任何操作。

```
window.localStorage.removeItem('name');
```

### `clear()`:如何删除`localStorage`中的所有项目

使用`clear()`方法删除`localStorage`中的所有项目。

调用此方法时，会清除该域的所有记录的整个存储。它不接收任何参数。

```
window.localStorage.clear();
```

### `key()`:如何获取`localStorage`中某个按键的名称

在需要循环遍历键的情况下，`key()`方法非常方便，它允许您向`localStorage`传递一个数字或索引来检索键的名称。

```
var KeyName = window.localStorage.key(index);
```

## `localStorage`浏览器支持

`localStorage`作为一种网络存储是 HTML5 规范。包括 IE8 在内的主流浏览器都支持。为了确保浏览器支持`localStorage`，您可以使用下面的代码片段进行检查:

```
if (typeof(Storage) !== "undefined") {
    // Code for localStorage
    } else {
    // No web storage Support.
}
```

## `localStorage`限制

使用`localStorage`很容易，但也很容易被误用。以下是限制，以及不使用`localStorage`的方法:

*   不要在`localStorage`中存储敏感的用户信息
*   它不能替代基于服务器的数据库，因为信息只存储在浏览器中
*   `localStorage`在所有主流浏览器中限制为 5MB
*   非常不安全，因为它没有任何形式的数据保护，可以被你网页上的任何代码访问
*   `localStorage`是同步的，意味着每个被调用的操作只会一个接一个地执行

有了这些，我们在 web 应用程序中就拥有了`[localStorage](https://blog.logrocket.com/web-storage-made-simple-use-local-storage-state/)`的力量。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.