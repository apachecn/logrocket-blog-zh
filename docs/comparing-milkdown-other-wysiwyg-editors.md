# 将 Milkdown 与其他所见即所得编辑器进行比较

> 原文：<https://blog.logrocket.com/comparing-milkdown-other-wysiwyg-editors/>

有许多可用的所见即所得编辑器，这使得选择一个具有挑战性。在这篇文章中，我们将评估 Milkdown，一个新的所见即所得编辑器，并与它的一些顶级竞争对手进行比较。以下是我们将要介绍的内容:

## 什么是牛奶？

Milkdown 是一个开源的所见即所得的 Markdown 编辑器。它的目标是提供一个强大的、可扩展的、可以在任何地方使用的编辑器组件。它是:

*   **开源**:您可以[读取](https://github.com/Milkdown/milkdown)[Gi](https://github.com/Milkdown/milkdown)[t](https://github.com/Milkdown/milkdown)[H](https://github.com/Milkdown/milkdown)[ub](https://github.com/Milkdown/milkdown)上的代码，并根据您的需求进行扩展
*   **轻量级**:缩小后，库在 125kb 左右；缩小压缩后大概有 40kb 左右[来源](https://bundlephobia.com/package/@milkdown/core@7.0.1)。这些在未来可能会改变，因为该库仍处于早期开发阶段
*   可扩展性:它遵循基于插件的原则，所以你需要的任何特性都可以使用 npm 包来添加和安装，或者你可以创建自己的来支持你的想法
*   **可定制** : Milkdown 让你控制造型
*   **可靠** : Milkdown 基于[的 ProseMirror](https://prosemirror.net/) 和[的 Remark](https://remark.js.org/) ，这两者都已经有了庞大的支持性社区，并且经过了充分的检验
*   **集成**:支持所有流行的前端框架，包括 React、Vue 等。

Milkdown 内部由两部分组成，[根据官方网站](https://milkdown.dev/):

1.  一个微小的核心，它提供了一个插件加载器和各种内部插件
2.  许多插件提供语法、命令和组件

基于插件的系统的好处是你可以很容易地启用和禁用定制功能。这也将有助于确保您只发布您需要发布的代码。

它还支持一些很酷的功能，可以通过官方和社区插件获得:

1.  [数学](https://milkdown.dev/plugin-math):你可以在编辑器里面写你的数学文档；通过 [TeX](https://en.wikipedia.org/wiki/TeX) 支撑
2.  [表格](https://milkdown.dev/preset-gfm):可以在编辑器中添加表格
3.  slash 命令:你可以使用 Slash 命令，就像它在 idea、Slack 等应用中的实现方式一样。
4.  [协同](https://github.com/Milkdown/milkdown/tree/main/packages/plugin-collab):支持共享编辑

## 为什么是牛奶？

Milkdown 网站对此做了很好的解释:

> 有各种不同的 markdown 编辑器，如 [Typora](https://typora.io/) 、 [tui](https://github.com/nhn/tui.editor) 和 [Bear](https://bear.app/) 。它们在不同平台上的 Markdown 中写笔记都很好用。那么，为什么要费心制作牛奶呢？
> 
> Milkdown 旨在为开发人员提供一个开源解决方案，使他们的编辑器更强大、更有吸引力，并且可以在任何地方使用。

大多数编辑没有给你全部的权力来定制一切。有些是有报酬的。有些没有其他的功能丰富。Milkdown 采用了一种不同的方法，提供了许多现成的功能。如果你对现有的功能不满意，你可以在基于插件的系统的帮助下创建你正在寻找的特定功能。

你可以在 Milkdown 网站上找到官方插件，在 GitHub 上找到[社区插件。](https://github.com/Saul-Mirone/awesome-milkdown)

> 注意:因为 Milkdown 是 ProseMirror 的包装器，所以您也可以使用它的所有功能，比如协作、linters 等。

### 基本挤奶示例

下面是一个将 Milkdown 与 vanilla TypeScript 一起使用的基本示例。这个例子[是受 Milkdown 文档](https://github.com/Milkdown/examples/tree/main/vanilla-commonmark/src)的启发:

```
// These two imports are important to render the editor
import { Editor, defaultValueCtx, rootCtx } from "@milkdown/core";
import { commonmark } from "@milkdown/preset-commonmark";

// Plugins: Use based on your requirement
import { gfm } from "@milkdown/preset-gfm";
import { nord } from "@milkdown/theme-nord";
import { menu } from "@milkdown/plugin-menu";

import '@milkdown/theme-nord/style.css'
// Default value
const markdown = `
# Test demo.
- [x] Hello
- [ ] World
- [x] Testing
- Remirror
\`\`\`ts
const new = 1
\`\`\`
`;
async function main() {
  await Editor.make()
    .config((ctx) => {
      // By default it set to `document.body`. In this case we want to load editor on dom with id app
      ctx.set(rootCtx, "#app");
      ctx.set(defaultValueCtx, markdown.trim());
    })
    .use(commonmark)
    .use(nord)
    .use(menu)
    .use(gfm)
    .create();
}
main();

```

你可以在 StackBlitz 上玩我们的[示例演示，在 Milkdown 网站](https://stackblitz.com/edit/github-w8pjcn?file=README.md)上玩[官方演示](https://milkdown.dev/playground) [。](https://milkdown.dev/playground)

## Milkdown 与 Quill.js 相比如何

Quill.js 是一个流行的开源 JavaScript 库，用于在 web 上构建富文本编辑器。

它是轻量级的，易于使用，并且提供了一组强大的特性以及丰富的资源和社区支持。它适用于基本的文本格式化和编辑任务，使用模块化设计方法，并为开发人员提供定制和扩展编辑器功能的灵活性。Quill.js 也很好地支持跨浏览器。

### 关键特征

Quill.js 让你定制编辑器的界面。默认情况下，它支持最小的即插即用接口，并使用语义命名约定，因此不需要学习技术术语来使用编辑器，就像使用 ProseMirror 一样。

其他功能包括:

1.  支持常见的文本格式，如粗体、斜体和其他行业标准功能
2.  能够通过添加或删除模块来为编辑器功能添加自定义插件
3.  一个简单易用的 API，允许开发人员轻松地将编辑器与他们现有的应用程序工作流集成
4.  速度和效率，这使它成为需要处理大量用户文本的应用程序的好选择
5.  轻量级大小(缩小:~209kb，缩小+ gzipped: 45.2kb， [source](https://bundlephobia.com/package/quill@1.3.7) )
6.  所有核心功能都是现成的；不需要安装额外的插件

### 与牛奶稀释的比较

总的来说，Quill.js 的功能比 Milkdown 少:

1.  不支持国际化
2.  不支持拖放(有社区插件，但是你必须根据你的需求来使用它们)
3.  不支持集成前端框架
4.  不支持高级功能，如图形或数学，作为其官方插件的一部分

这个例子的灵感来自于 [Quill.js 文档](https://quilljs.com/docs/quickstart/):

```
<html>
  <head>
    <title>Quilljs Example</title>
    <meta charset="UTF-8" />
    <link
      href="https://cdn.quilljs.com/1.3.6/quill.snow.css"
      rel="stylesheet"
    />
  </head>
  <body>
    <!-- Include stylesheet -->
    <!-- Create the editor container -->
    <div id="editor">
      <p>Hello World!</p>
      <p>Some initial <strong>bold</strong> text</p>
      <p><br /></p>
    </div>
    <!-- Include the Quill library -->
    <script src="https://cdn.quilljs.com/1.3.6/quill.js"></script>
    <!-- Initialize Quill editor -->
    <script>
      var quill = new Quill("#editor", {
        theme: "snow"
      });
    </script>
  </body>
</html>

```

你可以在他们的网站上了解更多关于 T2 的信息。

## Milkdown 与 ProseMirror 相比如何

ProseMirror 也是一个开源的 JavaScript 库，由构建 [CodeMirror](https://codemirror.net/) 的同一作者构建。它也有点类似于 Quill.js，因为它也提供了一组用于构建富文本编辑器的工具和概念。这是另一个高度可定制和可扩展的库，拥有全面的文档和一个庞大的用户社区，他们帮助构建了广泛的文本编辑功能。

这里需要注意的是，Milkdown 是建立在 ProseMirror 之上的。这意味着无论 ProseMirror 提供什么，您也可以在 Milkdown 中使用。

### 关键特征

1.  支持协作编辑
2.  模块化架构:与其他架构相比，维护和开发更容易
3.  基于插件的系统
4.  为构建定制插件提供了一个健壮的 API
5.  [可扩展模式](https://prosemirror.net/examples/schema/) : ProseMirror 有助于编辑具有定制结构的文档，而无需从头开始编写自己的编辑器。它有更好的文档模型，也更灵活

这里有一个受 ProseMirror 文档启发的[基本示例:](https://prosemirror.net/examples/basic/)

```
你好，普罗塞米尔
这是可编辑的文本。你可以聚焦它，开始打字。

### 要应用样式，您可以选择一段文本，并从菜单中操作它的
样式。基本模式支持*强调*，
强文本 
 `code font`

块级结构可以通过键绑定(尝试使用 ctrl-shift-2 创建一个 2 级标题，或者输入一个空的 textblock 退出父块)或通过菜单来操作。

尝试使用菜单中的“列表”项将此段落包装在编号列表中。

Block-level structure can be manipulated with key bindings (try ctrl-shift-2 to create a level 2 heading, or enter in an empty textblock to exit the parent block), or through the menu.

更多来自 LogRocket 的精彩文章:

Milkdown 与 TinyMCE 相比如何

TinyMCE 是另一种挤奶替代品。根据[他们的 GitHub 页面](https://github.com/tinymce/tinymce):

TinyMCE 被数百万开发人员所使用和信任，它是世界上最具可定制性、可伸缩性和灵活性的富文本编辑器。

* * *

### 凭借每年超过 3.5 亿次的下载，我们也是互联网上最值得信赖的企业级开源 HTML 编辑器之一。目前，全球有超过 1 亿个产品由 Tiny 提供支持。作为一个强大的所见即所得编辑器，TinyMCE 是按比例构建的，旨在创新，并致力于为困难的边缘情况提供结果。

* * *

```
import { EditorState } from "prosemirror-state";
import { EditorView } from "prosemirror-view";
import { Schema, DOMParser } from "prosemirror-model";
import { schema } from "prosemirror-schema-basic";
import { addListNodes } from "prosemirror-schema-list";
import { exampleSetup } from "prosemirror-example-setup";
import "prosemirror-menu/style/menu.css";
// Mix the nodes from prosemirror-schema-list into the basic schema to
// create a schema with list support.
const mySchema = new Schema({
  nodes: addListNodes(schema.spec.nodes, "paragraph block*", "block"),
  marks: schema.spec.marks
});
window.view = new EditorView(document.querySelector("#editor"), {
  state: EditorState.create({
    doc: DOMParser.fromSchema(mySchema).parse(
      document.querySelector("#content")
    ),
    plugins: exampleSetup({ schema: mySchema })
  })
});
```

关键特征
你完全可以使用它的 CDN 或者自主机

与其他类似，它也遵循基于插件的架构，因此您可以根据自己的需求扩展该库

提供辅助功能支持

断开链接检查支持

1.  增强的图像编辑
2.  你可以在这里找到功能的完整列表。
3.  与牛奶稀释的比较
4.  TinyMCE 是一个高级编辑器，支持许多现成的功能，而 Milkdown 只支持基本的富文本编辑功能
5.  TinyMCE 中提供了可访问性支持

TinyMCE 提供高级图像支持；您可以在 TinyMCE 编辑器中编辑图像，它支持图像响应
许多功能，将来会派上用场。您不需要考虑从头开始创建特征

1.  比 Milkdown 更好的文档和支持
2.  基本示例
3.  基本的设置示例是从 TinyMCE 的文档中借用的[:](https://www.tiny.cloud/docs/tinymce/6/cloud-quick-start/)
4.  Milkdown 与 Toast UI 编辑器相比如何
5.  [Toast UI](https://ui.toast.com/) 是另一个流行的开源编辑器。它提供了两种编辑模式:Markdown 和 WYSIWYG，可以随时切换。它遵循 CommonMark 和 GFM (GitHub 风格的 Markdown)规范，这两个规范都在软件行业中广泛使用。

关键特征
 **Viewer** :支持只显示降价数据而不显示编辑区的模式

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <script
      src="https://cdn.tiny.cloud/1/no-api-key/tinymce/6/tinymce.min.js"
      referrerpolicy="origin"
    ></script>
    <script>
      tinymce.init({
        selector: "#mytextarea"
      });
    </script>
  </head>
  <body>
    <h1>TinyMCE Quick Start Guide</h1>
    <form method="post">
      <textarea id="mytextarea">Hello, World!</textarea>
    </form>
  </body>
</html>

```

国际化:支持英语、荷兰语、韩语、日语、中文、西班牙语、德语、俄语、法语、乌克兰语、土耳其语、芬兰语、捷克语、阿拉伯语、波兰语、加利西亚语、瑞典语、意大利语、挪威语和克罗地亚语，但是您可以扩展它以支持任何语言
**小部件**:这个特性允许您配置规则，用小部件节点替换与特定正则表达式匹配的字符串
**自定义块**:不支持 Markdown 的节点可以通过自定义块定义。您可以通过在自定义块中编写解析逻辑来显示您想要的节点

1.  借助官方插件，你也可以扩展编辑器的功能。一些扩展包括:
2.  图表
3.  代码语法突出显示
4.  颜色语法

表格合并单元格

*   用户模式
*   与牛奶稀释的比较
*   这两个库都是类似的编辑器，但是 Toast UI 在市场上已经存在很长时间了。它久经考验，并得到社区的支持。
*   在插件的帮助下，它对图表的支持是与 Milkdown 的一个区别，milk down 目前还不提供图表支持
*   它有先进的表格支持，也有插件的帮助

查看器模式仅允许您呈现降价内容
它支持颜色选择器！如果你想改变你正在编辑的文本的颜色，你可以使用它的插件来实现

1.  尽管支持其他一些重要的特性，Milkdown 比 Toast UI 有更好的图形支持
2.  两者都支持国际化、黑暗主题、代码语法高亮
3.  使用 Toast UI 的原因
4.  如果您的用例需要在编辑期间显示一个`iframe`、`highlight text color`或一些其他特殊类型的内容，Toast UI 编辑器会非常有帮助，因为它的 API 不太复杂，并且能够添加定制功能。与其他编辑器相比，创建小部件和插件的学习曲线没有那么复杂。
5.  基本示例
6.  结论

在这篇文章中，我们讨论了 Milkdown，一个新上市的具有强大功能的编辑器。它目前的主要缺点是它的社区不像其他编辑器那样强大:如果出现问题，你可能会花费额外的时间来解决它。因此，使用时风险自担。
最终，您对编辑器的选择取决于您的需求。虽然其他开源编辑器更适合基本应用程序，但如果您想构建一个具有许多功能的复杂应用程序，Toast UI 和 TinyMCE 是理想的选择。没有一个编辑器能满足所有需求。
在插件的帮助下，免费的编辑器可以和那些付费的编辑器表现一样好，特别是当社区像 ProseMirror 和 Quill.js 一样强大的时候

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
  </head>
  <body>
    <div id="editor"></div>
    <script src="src/index.ts" type="module"></script>
  </body>
</html>

```

```
import Editor from "@toast-ui/editor";
import "@toast-ui/editor/dist/toastui-editor.css";
const editor = new Editor({
  el: document.querySelector("#editor")
});

```

您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？
毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。
LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。
 [LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

## 自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。

There’s no doubt that frontends are getting more complex. As you add new JavaScript libraries and other dependencies to your app, you’ll need more visibility to ensure your users don’t run into unknown issues.

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) is a frontend application monitoring solution that lets you replay JavaScript errors as if they happened in your own browser so you can react to bugs more effectively. 

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)
[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) works perfectly with any app, regardless of framework, and has plugins to log additional context from Redux, Vuex, and @ngrx/store. Instead of guessing why problems happen, you can aggregate and report on what state your application was in when an issue occurred. LogRocket also monitors your app’s performance, reporting metrics like client CPU load, client memory usage, and more.

Build confidently — [Start monitoring for free](https://lp.logrocket.com/blg/javascript-signup).

```