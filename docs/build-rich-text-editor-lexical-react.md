# 使用词法和反应日志构建富文本编辑器

> 原文：<https://blog.logrocket.com/build-rich-text-editor-lexical-react/>

术语 WYSIWYG 在软件中非常常用，用来描述富文本编辑器或能够编辑富格式文本的系统。有了 WYSIWYG 文本编辑器，您基本上可以看到在处理文档时的最终结果。这种方法是有益的，可以提供即时的反馈，不像系统那样需要编写一些标记语言，比如 markdown。

所见即所得编辑器是非常重要的功能，你可以在许多类型的软件中找到，包括内容管理系统、web 构建器、复杂表单、笔记工具、看板等等。事实上，这篇文章是用 [Dropbox Paper](https://www.dropbox.com/paper) 写的，这是一个很好的所见即所得编辑器的例子。

在本文中，我们将使用[词法](https://github.com/facebook/lexical)和 React 来构建一个简化版的 Dropbox 纸张编辑器。你可以在这个 repo 查看这篇文章的[源代码。我们开始吧！](https://stackblitz.com/edit/react-4rxemf?file=src%2FEditor.js)

## 什么是词汇？

Lexical 是一个独立的、可扩展的文本编辑器框架，是由脸书在撰写本文时积极开发的。Lexical 为开发人员提供了低级 API 来构建他们自己的具有不同复杂程度的编辑器。值得注意的是，在撰写本文时，Lexical 仍处于早期开发阶段。然而，在我看来，现有的插件足以构建各种伟大的工具。

如果你熟悉像 [CKEditor](https://ckeditor.com/) 、 [Quill](https://quilljs.com/) 和 [ProseMirror](https://prosemirror.net/) 这样的工具，那么你就会意识到富文本编辑器有一些开发者经常需要的通用功能。Lexical 以一组独立的、模块化的包的形式提供了这些特性，您可以根据自己的需求轻松地将它们集成到您的项目中，从而消除了反复重写某些功能的需要。

Lexical 不是为任何特定平台构建的。相反，它被设计成完全跨平台和框架不可知的，这意味着底层 API 可以很容易地移植到移动或本机桌面，同时仍然保持与 web 版本的兼容性。它还可以无缝地插入不同的前端框架。

### 词汇特征

Lexical 是一个快速、可靠、轻量级且可访问的编辑器引擎，旨在提供出色的开发人员体验。Lexical 对编辑器用户界面的外观或样式没有主见。你可以把它想象成一个无头的富文本编辑器，它为你提供了构建任何你想要的东西的原语。它的无头方法使它具有高度的可扩展性，允许您构建新的功能或改进现有的功能以最大限度地满足您的需求。

词法公开的一些 API 包括:

*   纯文本
*   多信息文本
*   选择
*   历史
*   剪贴板
*   目录
*   桌子
*   密码
*   环

## 词汇入门

让我们通过构建一个 Dropbox Paper editor 的简单克隆来探索 Lexical 的一些特性。编辑器由底部的浮动工具栏组成，整个文档是编辑器视口。

在这个例子中，我们将对词法使用 React 绑定。为了使它易于访问，我们将把它放在 Stackblitz 上，并在本文末尾提供完整演示的链接。

首先，我们将创建一个 React 项目并安装必要的依赖项。您可以选择任何您喜欢的方法来启动 React 项目，包括[创建 React App](https://create-react-app.dev/) 、 [Stackblitz](https://stackblitz.com/) 或 [CodeSandbox](https://codesandbox.io/) :

```
$ npm install -S lexical @lexical/react @lexical/utils @fortawesome/fontawesome-svg-core @fortawesome/free-solid-svg-icons @fortawesome/react-fontawesome clsx 

```

接下来，我们将导入必要的组件来创建编辑器的实例:

```
Editor.jsx
import React from 'react';
import clsx from 'clsx';
import {
  $getRoot,
  $getSelection,
  $isRangeSelection,
  FORMAT_TEXT_COMMAND,
} from 'lexical';
import { LexicalComposer } from '@lexical/react/LexicalComposer';
import { useLexicalComposerContext } from '@lexical/react/LexicalComposerContext';
import { RichTextPlugin } from '@lexical/react/LexicalRichTextPlugin';
import { ContentEditable } from '@lexical/react/LexicalContentEditable';
import { OnChangePlugin } from '@lexical/react/LexicalOnChangePlugin';
import { HistoryPlugin } from '@lexical/react/LexicalHistoryPlugin';
import { mergeRegister } from '@lexical/utils';
import { FontAwesomeIcon } from '@fortawesome/react-fontawesome';

function onChange(state) {
  state.read(() =&gt; {
    const root = $getRoot();
    const selection = $getSelection();
    console.log(selection);
  });
}

export const Editor = () =&gt; {
  return (
    &lt;div className='bg-white relative rounded-sm'&gt;
      &lt;LexicalComposer
        initialConfig={{
          theme: {
            paragraph: 'mb-1', // tailwind classes work!
          },
          onError(error) {
            throw error;
          },
        }}
      &gt;
        &lt;RichTextPlugin
          contentEditable={
            &lt;ContentEditable className="h-[450px] outline-none py-[15px] px-2.5 resize-none overflow-hidden text-ellipsis" /&gt;
          }
          placeholder={
            &lt;div className="absolute top-[15px] left-[10px] pointer-events-none select-none"&gt;
              Now write something brilliant...
            &lt;/div&gt;
          }
        /&gt;
        &lt;OnChangePlugin onChange={onChange} /&gt;
        &lt;HistoryPlugin /&gt;
      &lt;/LexicalComposer&gt;
    &lt;/div&gt;
  )
}

```

我们已经导入了以下组件:

*   `LexicalComposer`:编辑器根组件，也是编辑器实例的上下文提供者
*   `LexicalRichTextPlugin`:展示一组支持富文本编辑的通用功能，包括粗体、斜体、下划线、删除线、对齐文本格式以及复制和粘贴
*   `LexicalOnChangePlugin`:每当编辑器状态改变时，执行一个回调函数，让您根据状态改变执行动作
*   `HistoryPlugin`:提供编辑器历史功能，公开撤销和重做命令

### 设计组件的样式

在上面的代码块中，我们创建了一个词法编辑器的简单实例，并在`LexicalComposer`上定义了初始的`config`。在`config`中，我们可以自定义编辑器的某些行为。例如，我们可以通过定义一个`theme`对象来定制[节点](https://lexical.dev/docs/concepts/nodes)的外观，该对象将 CSS 类名映射到编辑器。如果在`paragraph`节点上定义了一个类名，样式表中与该类名匹配的样式将应用于所有相应的节点。

更简单的方法是在我们的`theme`对象中使用 Tailwind CSS 类直接定义内联样式，就像我们在上面的`Editor.jsx`代码中所做的那样。这种方法也适用于您导入的任何组件；只是加了一个`className`道具。

此时，我们有了一个简单的编辑器，可以在其中输入文本，但是我们无法控制应用什么格式。为了解决这个问题，让我们构建一个类似于 Dropbox Paper 的浮动工具栏:

```
// Editor.jsx
//...
const Toolbar = () =&gt; {
  const [editor] = useLexicalComposerContext();
  const [isBold, setIsBold] = React.useState(false);

  const updateToolbar = React.useCallback(() =&gt; {
    const selection = $getSelection();
    if ($isRangeSelection(selection)) {
      setIsBold(selection.hasFormat('bold'));
    }
  }, [editor]);

  React.useEffect(() =&gt; {
    return mergeRegister(
      editor.registerUpdateListener(({ editorState }) =&gt; {
        editorState.read(() =&gt; {
          updateToolbar();
        });
      })
    );
  }, [updateToolbar, editor]);

  return (
    &lt;div className="absolute z-20 bottom-0 left-1/2 transform -translate-x-1/2 min-w-52 h-10 px-2 py-2 bg-[#1b2733] mb-4 space-x-2 flex items-center"&gt;
      &lt;button
        className={clsx(
          'px-1 hover:bg-gray-700 transition-colors duration-100 ease-in',
          isBold ? 'bg-gray-700' : 'bg-transparent'
        )}
        onClick={() =&gt; {
          editor.dispatchCommand(FORMAT_TEXT_COMMAND, 'bold');
        }}
      &gt;
        &lt;FontAwesomeIcon
          icon="fa-solid fa-bold"
          className="text-white w-3.5 h-3.5"
        /&gt;
      &lt;/button&gt;
      {/* ... */}
    &lt;/div&gt;
  );
};

```

让我们来分解这个代码。我们创建了一个返回一个`div`元素的`Toolbar`组件，它绝对位于编辑器的底部。我们定义了一个代表工具栏动作的`button`元素，即`bold`。我们还将在这里定义其他动作按钮。

`onClick`事件触发对`editor.dispatchCommand`的调用，后者对某些事件如`FORMAT_TEXT_COMMAND`作出反应，并相应地更新编辑器状态。`RichTextPlugin`已经在内部处理了`FORMAT_TEXT_COMMAND`，然而，我们可以[使用`editor.registerCommand`编写我们自己的定制命令](https://lexical.dev/docs/concepts/commands#createcommand)来显式处理更复杂的节点。

每次编辑器状态改变时，`updateToolbar`函数都会运行。通过这个功能，我们可以控制每种文本格式的当前状态，并在工具栏中显示一个指示器，可以是活动的，也可以是不活动的。

在`useEffect`回调中，我们实际上通过`registerUpdateListener`注册了监听器。我们只需要在编辑器中渲染`Toolbar`组件，如下所示:

```
//...
export const Editor = () =&gt; {
  return (
    &lt;div className='bg-white relative rounded-sm'&gt;
      &lt;LexicalComposer
        initialConfig={{
          theme: {
            paragraph: 'mb-1', // tailwind classes work!
          },
          onError(error) {
            throw error;
          },
        }}
      &gt;
        &lt;Toolbar/&gt;
        &lt;RichTextPlugin
          contentEditable={
            &lt;ContentEditable className="h-[450px] outline-none py-[15px] px-2.5 resize-none overflow-hidden text-ellipsis" /&gt;
          }
          placeholder={
            &lt;div className="absolute top-[15px] left-[10px] pointer-events-none select-none"&gt;
              Now write something brilliant...
            &lt;/div&gt;
          }
        /&gt;
        &lt;OnChangePlugin onChange={onChange} /&gt;
        &lt;HistoryPlugin /&gt;
      &lt;/LexicalComposer&gt;
    &lt;/div&gt;
  )
}

```

现在，我们可以按照相同的模式向工具栏添加更多的动作按钮。我们只需要复制`button`元素，然后更改`icon`和`onClick`处理程序来分派正确的事件。让我们为斜体文本格式添加一个操作按钮:

```
// Editor.jsx
//...
&lt;button
  className={clsx(
    'px-1 hover:bg-gray-700 transition-colors duration-100 ease-in',
    isItalic ? 'bg-gray-700' : 'bg-transparent'
  )}
  onClick={() =&gt; {
    editor.dispatchCommand(FORMAT_TEXT_COMMAND, 'italic');
  }}
&gt;
  &lt;FontAwesomeIcon
    icon="fa-solid fa-italic"
    className="text-white w-3.5 h-3.5"
  /&gt; 
&lt;/button&gt;
//...

```

就这样，我们结束了！如果您已经完成了，那么您应该有了一个简单的 WYSIWYG 编辑器的工作原型，它具有最少的功能，您可以在其上进行构建。词汇框架附带了各种各样的[预构建插件](https://lexical.dev/docs/react/plugins)，您可以使用它们为您的编辑器添加不同的功能。

您可以在 Stackblitz 上查看本文中[示例的完整代码。它包括更多的格式化特性，如`underline`、`strikethrough`、`left/right/center/justify alignment`等等。](https://stackblitz.com/edit/react-4rxemf?file=src%2FEditor.js)

## 词汇的替代品

除了词法工具之外，还有几个替代工具可以用来构建文本编辑器并提供类似的功能。

一个例子是同样在脸书开发的 Draft.js。与 Lexical 不同，Draft.js 框架是专门为 React 构建的。这意味着它的底层架构与 React 框架紧密耦合。

虽然 Draft.js 是可扩展和可定制的，但它不像 Lexical 那样提供跨平台和框架无关的好处。尽管如此，它仍然提供了许多构建任何类型的富文本编辑器所需的特性。如果您的项目不需要跨平台支持，我鼓励您探索 Draft.js 作为 Lexical 的替代。值得注意的是，Draft.js 现在处于[维护模式](https://github.com/facebook/draft-js#status)，不会添加新功能。

另一个替代词法的好方法是 [Editor.js](https://editorjs.io/) 。这个开源的文本编辑器框架产生 JSON 输出，而不是原始的 HTML，使得在不同于 web 的平台上无缝地呈现数据。

Editor.js 核心 API 提供了许多创建强大的富文本编辑器所需的基本工具；然而，它的设计也是可扩展和可插拔的，这意味着您可以在编辑器中轻松地为任何您需要的功能导入插件。这里有一个[快速入门指南](https://editorjs.io/getting-started)，用 Editor.js 创建你的第一个文本编辑器。

## 结论

在本文中，我们学习了如何使用带有 React 的词法框架来构建一个简单、丰富的文本编辑器。

请记住，由于在撰写本文时 Lexical 仍处于早期开发阶段，随着每个新版本的不断完善，API 必然会随着时间的推移而发生变化。然而，现在你可以用当前提供的特性和插件构建很多有趣的东西。感谢您的阅读，如果您有任何问题，请务必留下评论。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)