# 在 React 中实现复制到剪贴板

> 原文：<https://blog.logrocket.com/implementing-copy-clipboard-react-clipboard-api/>

如果你正在阅读这篇博客，很有可能你已经从 GitHub 克隆了一个 git 库。GitHub 添加到其网站上的一个小而强大的功能允许用户点击一个按钮将 Git URL 复制到您的剪贴板上，这样您就可以轻松地将其粘贴到您的终端上，以将存储库克隆到您的本地机器上。

这比选择整行文本，然后点击`Control+C`或`Command+C`来复制它要容易得多。它还可以防止您在选择 URL 时遗漏任何字符。

现在，假设你正在为自己建立一个新的博客网站，你想让读者尽可能容易地与他人分享文章。一些社交媒体网站提供使用链接的方法，并自动在其网站上开始新的帖子，但不是每个应用程序都支持这种功能。

如果你想复制一个网址并通过 Zoom 或 iMessage 发送给某人，该怎么办？如果我们给用户一个复制文本的按钮，将会极大地改善用户体验。它对移动用户也特别有帮助。

在本文中，我将向您展示如何通过创建一个可重用的 React 组件来实现这一点，该组件将接受文本作为一个属性值，并在单击时将文本复制到用户的剪贴板。

## 什么是 JavaScript 剪贴板 API？

令人欣慰的是，在现代 web 开发时代，我们获得了许多有用的 web APIs，使得在浏览器中复制和粘贴文本等任务变得容易。当前用于剪贴板交互的事实上的 API 是[剪贴板 API](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API) ，它提供了一些简单的函数，您可以使用这些函数将文本复制到用户的剪贴板并从中读取项目。

不幸的是，在撰写本文时，并不是所有的 web 浏览器都支持这个 API。虽然有些浏览器完全或部分支持它，但 Internet Explorer 根本不支持它。

![browser support for clipboard API](img/5601771a377487042af9c36a0f7dee74.png)

Source: MDN

然而，如果你仍然必须支持 IE，有一个遗留功能可以作为备份，使用`document.execCommand('copy')`。

下面是一个接受文本作为参数并将其复制到用户剪贴板的函数示例:

```
export async function copyTextToClipboard(text) {
  if ('clipboard' in navigator) {
    return await navigator.clipboard.writeText(text);
  } else {
    return document.execCommand('copy', true, text);
  }
}

```

现在，让我们走一遍。

首先，我们有一个名为`copyTextToClipboard`的异步函数，它只有一个参数文本。它检查属性剪贴板是否存在于`navigator`对象上。这是检查当前浏览器是否支持剪贴板 API 的简单方法。

接下来，如果浏览器支持剪贴板 API，我们等待承诺`navigator.clipboard.writeText(text)`被解析并返回。

否则，我们用参数`'copy', true, text`调用`Document.execCommand`函数。你可以在[的 MDN 文档](https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand)中读到这个命令的参数。

当您在浏览器中运行此命令时，它会将作为参数传入的任何文本复制到用户的剪贴板，这样当用户粘贴文本时，它就会出现。

现在我们有了一个简单的工作函数，我们将把它实现到一个灵活的 React 组件中。

## 在 React 中使用剪贴板 API

让我们首先设置一个简单的 React 组件，直观地表示我们想要实现的目标:

```
function ClipboardCopy({ copyText }) {
  const [isCopied, setIsCopied] = useState(false);

  // TODO: Implement copy to clipboard functionality

  return (
    <div>
      <input type="text" value={copyText} readOnly />
      <button>
        <span>{isCopied ? 'Copied!' : 'Copy'}</span>
      </button>
    </div>
  );
}

```

这里，我已经设置了组件`ClipboardCopy`，它接受属性`copyText`。这将是我们希望用户复制的文本。它将呈现一个只读的`<input>`元素，显示我们的`copyText`字符串和一个按钮，我们将使用它来执行我们的复制功能。

我还添加了状态值`isCopied`来跟踪文本是否已经被复制到剪贴板，并改变显示在按钮`copy`上的文本。

现在，我们需要为我们的按钮创建一个`onClick`处理函数，它将异步调用我们的`copyToClipboard`函数并将`copyText,`作为参数传递。

基于函数的结果，我们将把`isCopied`的状态改为`true`，或者，如果出现错误，我们将把它打印到控制台。如果命令成功，下一次用户在他们的设备上粘贴文本时，`copyText`的值将是结果。

```
function ClipboardCopy({ copyText }) {
  const [isCopied, setIsCopied] = useState(false);

  // This is the function we wrote earlier
  async function copyTextToClipboard(text) {
    if ('clipboard' in navigator) {
      return await navigator.clipboard.writeText(text);
    } else {
      return document.execCommand('copy', true, text);
    }
  }

  // onClick handler function for the copy button
  const handleCopyClick = () => {
    // Asynchronously call copyTextToClipboard
    copyTextToClipboard(copyText)
      .then(() => {
        // If successful, update the isCopied state value
        setIsCopied(true);
        setTimeout(() => {
          setIsCopied(false);
        }, 1500);
      })
      .catch((err) => {
        console.log(err);
      });
  }

  return (
    <div>
      <input type="text" value={copyText} readOnly />
      {/* Bind our handler function to the onClick button property */}
      <button onClick={handleCopyClick}>
        <span>{isCopied ? 'Copied!' : 'Copy'}</span>
      </button>
    </div>
  );
}

```

我添加了一个新函数`handleCopyClick`，它充当按钮元素的`onClick`事件处理程序。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

当用户点击按钮时，事件处理函数被执行，我们的异步函数`copyTextToClipboard`被执行。

一旦该函数被解析，它或者将`isCopied`状态值设置为`true`，或者如果出现错误，则打印该错误。

我还添加了一个`setTimeout`函数，在 1.5 秒后将复制的状态值重置回`false`。这允许我们给用户一些交互反馈，因为将文本复制到剪贴板是一个“后台任务”，不提供任何固有的反馈。

现在，我们可以在 React 应用程序中的任何地方使用该组件，如下所示:

```
function App() {
  return (
    <div className="App">
      <ClipboardCopy copyText="https://google.com" />
      <ClipboardCopy copyText="https://logrocket.com" />
    </div>
  );
}

```

最后，当您运行应用程序时，您的用户应该会看到以下内容:

![Copy Button Next To Link Being Pressed](img/42491dad136889b872c26a273928a141.png)

您可以通过在调用`copyTextToClipboard`出错的情况下提供更好的反馈来改进这个解决方案。如果您的项目中有一个错误组件，您可以调用一个函数或更改状态来向用户显示错误，而不是将文本打印到用户很可能永远看不到的控制台。

不想手动编写实现复制和读取剪贴板功能的组件？嗯，你可以使用开源的替代方案。让我们来看看其中的一些。

## React 中将文本复制到剪贴板的替代方法

### 反应-复制到剪贴板

[React-copy-to-clipboard](https://www.npmjs.com/package/react-copy-to-clipboard) 是一个 React 组件，允许你将文本复制到剪贴板。它基于 JavaScript [复制到剪贴板 npm 包](https://www.npmjs.com/package/copy-to-clipboard)，与我们之前的例子不同，它使用实验性的[剪贴板数据](https://developer.mozilla.org/en-US/docs/Web/API/ClipboardEvent/clipboardData) API，大多数浏览器都支持。

![copy clipboard react third-party library browser compatibility](img/3ede38865a3200a42efb14028b87b0ff.png)

使用起来也很简单。首先，您需要在终端上运行`npm install react-copy-to-clipboard`来安装组件。现在您可以使用该组件了，如下所示:

```
import "./styles.css";
import { CopyToClipboard } from "react-copy-to-clipboard";

export default function App() {
 return (
   <div className="App">
     <h1>Clipboard Copy</h1>
     <CopyToClipboard
     text="text"
     onCopy={() => alert("Copied")}>
       <span>Copy to clipboard with span</span>
     </CopyToClipboard>
   </div>
 );
}
```

组件接受一个`text`和一个`onCopy`道具。文本道具允许您设置想要复制的文本，而`onCopy`道具是一个在复制活动成功执行时触发的事件。在我们的示例中，我们使用本机 JavaScript 警报。在实际的应用程序中，使用一些不太麻烦的东西，比如 [Snackbar。](https://www.polonel.com/snackbar/)

### `useCopy`反应钩

[`useCopy`](https://github.com/animify/useCopy) 是一个 React 钩子，可以让你将文本复制到用户的剪贴板上。它使用我们前面讨论过的复制到剪贴板 JavaScript 库，这意味着它不使用剪贴板 API。相反，它使用剪贴板数据 API。

实现很流畅，就像你通常使用 React 钩子一样。在你的终端上运行`npm install useCopy`来安装插件，导入`useCopy`钩子并像这样使用它:

```
import React from "react";
import useCopy from "use-copy";

export default function App() {
 const [copied, copy, setCopied] = useCopy("https://logrocket.com, this is the text to copy");

 const copyText = () => {
   copy();

   setTimeout(() => {
     setCopied(false);
   }, 3000);
 };

 return (
   <div>
     {copied ? "Copied to clipboard" : <a onClick={copyText}>Copy text</a>}
   </div>
 );
}

```

默认情况下，复制的状态变量被设置为`false`，直到`copy`函数被调用。然后，我们可以在三秒钟后将状态重置回`false`，以允许用户再次复制。

您可能也会对我们如何实现这个库感兴趣。我已经从项目的 GitHub 库中提取了代码，所以你可以看到它是多么优雅:

```
import copyToClipboard from 'copy-to-clipboard';
import { useState, useRef, useEffect, useCallback } from 'react';

export default function useCopy(str: string): [boolean, () => void, (value: boolean) => void] {
    const copyableString = useRef(str);
    const [copied, setCopied] = useState(false);

    const copyAction = useCallback(() => {
        const copiedString = copyToClipboard(copyableString.current);
        setCopied(copiedString);
    }, [copyableString]);

    useEffect(() => {
        copyableString.current = str;
    }, [str]);

    return [copied, copyAction, setCopied];
}

```

## 结论

现在我们已经介绍了 JavaScript 剪贴板 API，您可以看到在任何用例中实现它是多么容易，如果您愿意，甚至可以考虑第三方库。这改善了应用程序中的用户体验，当然，如果你想为你的内容赢得观众，让用户分享你的内容变得简单也可以增加你的网站曝光率。看完这些，希望你能为自己收获这些好处！

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