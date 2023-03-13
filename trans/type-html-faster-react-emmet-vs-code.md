# 在 React with Emmet 和 VS 代码中更快地键入 HTML

> 原文：<https://blog.logrocket.com/type-html-faster-react-emmet-vs-code/>

Visual Studio 代码被 React 开发人员广泛认为是最受欢迎的 IDE。凭借其大量的插件和扩展， [VS Code 有助于加速开发过程](https://blog.logrocket.com/writing-vs-code-extensions-in-javascript/)并提高生产率。

该列表中一个有趣的插件是一个名为 [Emmet](https://emmet.io/) 的神奇工具，它通过使用简单的缩写然后转换成代码块来帮助你更快地编写 HTML 和 CSS。然而，有一个小缺点；默认情况下，Emmet 不会在 VS 代码中启用 React。

在本文中，我们将学习 React JSX，然后查看在 React for VS 代码中启用 Emmet 的解决方案。我们还将探索各种 Emmet 缩写。我们开始吧！

## React 中的 HTML

React 的一个独特特性是 [JSX](https://reactjs.org/docs/introducing-jsx.html) 的概念。JSX，代表 JavaScript XML，是 JavaScript 的简单语法扩展，允许你用 JavaScript 写 HTML。

使用 JSX，您可以通过将 HTML 标签转换为 React 元素来编写 HTML。在 React 中使用 JSX 有助于您为 React 应用程序创建更简单、更整洁的代码库，优化您的逻辑并使其更容易理解。

编写 JSX 时，需要遵循一些规则来防止不必要的控制台错误:

*   HTML 属性和 CSS 属性必须使用 camelCase 命名
*   在 JSX 中，JavaScript 代码必须用花括号`{}`括起来
*   对于 HTML 元素的每个开始标记，必须有一个相应的结束标记

使用 VS 代码中的 Emmet 插件，很容易遵循这些规则，帮助您更快、更有效地在 React 中键入 HTML。

## Visual Studio 代码中的 Emmet

Emmet 是 VS 代码的内置特性，所以不需要额外安装。通过使用速记和缩写，Emmet 极大地改善和加快了您的 HTML 和 CSS 工作流程，让您不必手动键入完整的代码:

![Emmet VSCode Abbreviation Example](img/8f15beb85acb4848df94efd12b7fe328.png)

Emmet 根据传递的内容使用不同的缩写和简短表达，然后动态地将缩写转换成完整的代码。Emmet 主要用于 HTML、XML 和 CSS，但也可以用于编程语言。

## 在 React 的 Visual Studio 代码中启用 Emmet

在 React 中构建 web 应用程序的 UI 需要使用 JSX 写出 React 中的 HTML，并使用 CSS 定义样式。

更大的代码库需要重复的语法，如果您必须重复输入每个部分，这可能会降低生产率。有了 Emmet，你可以轻松解决这个问题。

通过使用短表达式和缩写，您可以更快更容易地键入 HTML，同时也可以更高效地生成良好的代码库。只有一个小缺点；默认情况下，Emmet 没有配置为识别 React for VS 代码中的`.jsx`文件。然而，有一个简单的解决方法。

通过遵循这些简单的步骤，您可以轻松地配置 VS 代码来完全支持 React。

## 步骤 1:在 VS 代码中打开`settings.json`

要打开`setting.json`文件，你首先要打开**用户设置**页面，如果你用的是 Windows，输入`Ctrl` + `,`，如果你用的是 Mac，输入`⌘` + `,`:

![Open VSCode User Settings](img/8211df026c83ec9f272a835432b0c3b5.png)

在**用户设置**页面中，点击页面右上角的**新文件图标**:

![VSCode User Setting New File Icon](img/9d34c876594cf667c21063096be48d1d.png)

或者，您可以直接从命令面板打开`settings.json`文件。如果你是 Windows 用户，只需输入`Ctrl` + `Shift` + `P`，如果你是 Mac 用户，只需输入`⌘` + `shift` + `P`。该命令以输入框格式打开命令选项板。

接下来，在命令面板中，搜索`settings.json`并点击下拉菜单中的**首选项:** **打开用户设置** JSON 选项:

![Select User Settings Command Palette](img/8e53bd06c720b10927e2f1d0b9c1aa61.png)

Select user settings page from the Command Palette

## 第二步:配置`settings.json`

从`settings.json`文件中，您将能够看到已经为您的 IDE 设置的不同配置。为了在 VS 代码中为 React 启用 Emmet，我们将添加以下代码:

```
"emmet.includeLanguages": {
  "javascript": "javascriptreact",
  "typescript": "typescriptreact"
}
```

有了上面的代码，Emmet 现在可以用于在 VS 代码中被识别为`javascriptreact`或`typescriptreact`的文件，它们是`.jsx`和。`tsx`文件。您需要重新加载 IDE 来体验更新后的更改。

另一种方法是直接从 VS 代码 UI 处理这个问题。从用户设置页面，搜索 Emmet。然后，向下滚动到 **Emmet: Include Languages** 并点击 **add item** 将上面的代码作为键-值对包含进来:

![Enable Emmet Vscode React User Settings](img/8e5557bf0a25fd8760eb6e7ad469192d.png)

Enable Emmet in VSCode for React directly from the user settings page

## Emmet 缩写

既然我们已经成功地在 VSCode 中配置了 Emmet 来支持 JSX，我们可以直接在一个`jsx`文件中尝试 Emmet 缩写和表达式。根据您想要实现的内容，有不同的缩写。这些缩写然后被转换成结构化代码块。

让我们来看看一些基本的缩写和表达。

## 属性运算符

属性运算符允许您轻松定义特定元素的类和 ID:

*   `div.demo`=>= 
*   `div#demo`=>= 

这些属性还可以组合成如下表达式:

*   `div#headerId.headerClass`=>`<div id="headerId"`

## 嵌套运算符

嵌套运算符允许我们定位元素的放置方式和它们遵循的顺序。

### 孩子`>`

子元素用于按照`nav>ul>li`结构将元素嵌套在彼此内部:

```
   <nav>
        <ul>
          <li></li>
        </ul>
    </nav>

```

### 兄弟姐妹`+`

Sibling 将元素放在同一层，跟在`p+span`后面:

```
<p></p>
<span></span>

```

### 爬上去`^`

向上爬`^`将下面的元素在树上向上移动一级，`header+main>div^footer`:

```
  <header></header>
  <main>
    <div></div>
  </main>
  <footer></footer>   

```

### 乘法运算`*`

乘法`*`定义一个元素应该被创建的次数`li*2`:

```
<li></li>
<li></li>

```

### 项目编号`$`

项目编号`$`操作符允许我们为重复的元素分配唯一的值。它可以与乘法运算符一起使用，以输出重复元素`div.group$*5`的当前编号:

```
<div className="group1"></div>
<div className="group2"></div>
<div className="group3"></div>
<div className="group4"></div>
<div className="group5"></div>

```

### 文本格式`{}`

文本格式`{}`用于向元素添加文本，如下所示:

`p.demo{test}`=>= 

到目前为止，我们已经介绍了 Emmet 的基本用法。您也可以查看此[备忘单](https://docs.emmet.io/cheat-sheet/)以获得更多关于不同缩写语法的指南。

## React 片段扩展

另一个对 React 开发人员有益的扩展是 [React snippets 扩展](https://marketplace.visualstudio.com/items?itemName=ugross.vscode-react-snippets)。它的工作原理与 Emmet 相似；只需输入前缀，它就会自动为其生成代码片段。它提供了 React、Redux 和 React Router with Hooks 支持的代码片段:

![Open User Settings Command Palette](img/1084fe504f9e95cad647724619ecbb4a.png)

## 结论

Emmet 之所以与众不同，是因为它很容易提高输入 HTML 的速度；只需一个简单的表达式，我们就可以实现一个大的代码块。我们不需要进行额外的安装步骤，因为它在 VSCode 中是默认可用的。

Emmet 提高了 React 开发人员的工作效率，改善了开发人员在 JSX 打字的体验。我希望这篇文章为在 VS 代码中使用 Code for React 提供了一个更清晰的解决方案。如果您有任何问题，请随时通过 [Twitter](https://twitter.com/abiolaesther_) 联系我或在下面留言。编码快乐！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)