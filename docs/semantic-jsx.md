# 语义 JSX 的现状

> 原文：<https://blog.logrocket.com/semantic-jsx/>

为 web 创建平台一直以来都很简单，只需学习编写 HTML，加入一些 CSS 的外观和感觉，然后，可能还需要一点 JavaScript 或任何其他功能性脚本语言。

然而，随着 JavaScript 组件框架的出现，许多概念开始发挥作用，比如样式化组件和 JSX( [React](https://www.reactenlightenment.com/react-jsx/5.1.html) )(以及许多其他概念)。这些框架最有趣的部分是将网页分解成特定的组件，只有当用户界面需要它们时才导入它们。

这就引出了一个问题，这会影响网络的工作方式吗？这样会好点吗？我们还能写语义代码吗？组件的单一入口点是否使得获取可访问的 [DOM](https://blog.logrocket.com/8-dom-features-you-didnt-know-existed-ec2a0a28fd89/) 树变得困难？让我们找出答案。

## TL；速度三角形定位法(dead reckoning)

在本文中，我们将在 [ReactJS](https://reactjs.org) 应用程序中实现可访问性特性。我们将看看如何在基于组件的应用程序中实现这一点，同时使用 [React 片段](https://reactjs.org/docs/fragments.html#short-syntax)避免不必要的 div 导入。我们还将了解焦点管理的概念以及`ref`如何在 ReactJS 中帮助实现这一概念。

## 先决条件

在我们进一步讨论之前，本文假设:

*   [Node.js ≥v6](https://nodejs.org/) 安装在您的机器上
*   您的机器上安装了 npm
*   您的机器上安装了 React 版本 16.8 或更高版本
*   [Create-react-app](https://www.npmjs.com/package/create-react-app) 已安装在您的机器上
*   你有网页可及性的基本知识
*   你对[反应](https://reactjs.org)有一个基本的了解

## 易接近

让一个 web 项目具有可访问性可能会让人感觉力不从心，但这真的就像实现一种更具语义的方法来编写代码以支持所有用户一样简单。这一点的基础是 [POUR](https://medium.com/the-school-of-do/making-accessibility-accessible-the-pour-principles-f5ad21eda12f) 原则，该原则指导建立可访问的网站。

简单来说就是倒的意思——*可感知的*、*可操作的*、*可理解的*、*健壮的*。

**可感知的**:这意味着网页应该可以通过浏览器或辅助技术(如屏幕阅读器和屏幕放大器)为感官(视觉、触觉和听觉)所用。

**可操作**:这意味着用户可以使用鼠标、键盘或辅助设备与所有控件和交互元素进行交互。在焦点管理部分，我们将探讨如何使平台具有可操作性。

可理解的:在这方面，我们考虑语言的使用，包括尽可能减少拼写错误和复杂的语法。

健壮:这意味着整个网络的一致性。这意味着你的平台必须在所有平台上以同样的方式工作。

## React 应用程序可以被访问吗？

这个问题我们听过很多次了。其原因是 React 应用程序依赖于所谓的虚拟 DOM，每当应用程序的一部分由于更改而必须重新呈现时，都会构建该虚拟 DOM。分解组件只接受单个根元素(主要是一个 div)。这不是语义性的，不会被屏幕阅读器之类的辅助工具识别。

然而，React 中的可访问性可以通过多种方式实现，本文将对此进行讨论。

## React JSX 的可访问性

为了实现语义 JSX，有一些工具和实践可以帮助您的 React 应用程序更加用户友好。我们将在这一部分讨论这些工具和实践。

## 在碎片反应之前

在我们全面了解 React 片段对于 React 可访问性的重要性之前，让我们先了解语义 JSX 在 React 片段之前是如何实现的，以便理解它的重要性。

**1。使用`Div`标签**导入元素

通常，当学习构建 React 应用程序时，我们会学习将特定组件的代码包装在一个`div`或`span`标签中。大多数 React 开发人员使用`div`标签来确保代码作为一个块到达其导入的位置。现在，所有这些只是在另一个`div`中返回一个`div`，这阻止了编写语义 JSX。

**这种方法的缺点**

当处理这样的导入时，我们所做的是用`div`标签构建 React 的虚拟 DOM，这些标签最终将作为非语义 HTML 呈现在主 Dom 中，这使得屏幕阅读器很难解释。

**2。使用数组作为入口点**

因为 React 组件只将单个根元素返回到另一个父组件中，所以我们可以决定通过返回一个值数组将子组件中的信息映射到父组件，如下所示:

```
import React from 'react'
    function NameList() {
     return (
      [
       <h2 key="1"> List of Developers in Nigeria </h2>,
       <li key="2"> Obinna </li>
       <li key="3"> Kofo </li>
       <li key="4"> Jola </li>
      ]
     )
    }
    export default NameList
```

这是可行的，尽管它导致在 DOM 中呈现可能不需要的额外元素。还必须总是映射出具有唯一键的元素数组，这可以看作是大量的语法使用。

## 使用反应片段

为了解决不必要的导入问题，在 [React v16.8](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html) 中引入了一个解决方案。[片段](https://reactjs.org/docs/fragments.html#short-syntax)帮助你在不增加额外节点到 DOM 的情况下对孩子列表进行分组。基本上，fragments 所做的是，通过完全按照我们编写的方式创建虚拟 DOM，帮助保证子组件到父组件的更多语义输入。

例如，一个简单的用例是使用旧语法调用`<tr>`(表格滚动)中的`<td>`(表格数据)标签:

```
class Table extends React.Component {
  render() {
    return (
      <table>
        <tr>
          <Columns />
        </tr>
      </table>
    );
  }
}
```

在列组件中，我们会有这样的内容:

```
class Columns extends React.Component {
  render() {
    return (
      <div>
        <td>Hello</td>
        <td>World</td>
      </div>
    );
  }
}
```

但是为了避免无效的 HTML 错误，`<Columns />`在呈现时需要返回多个`<td>`元素，而不是一个`div`。上面的输出将如下所示:

```
<table>
  <tr>
    <div>
      <td>Hello</td>
      <td>World</td>
    </div>
  </tr>
</table>
```

对于片段，这是这样解决的:

```
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}
```

片段可以像这样使用`<React.Fragment>`或者使用空标签`<>`。

## 使用片段时需要注意的事项

*   当使用空标签时，它不支持使用键来映射数据
*   键是目前唯一受`<React.Fragment>`标签支持的属性

## 这对编写语义 JSX 有什么帮助？

React Fragments 帮助改善语义 JSX 的明显方式是通过消除 DOM 树中不必要的`div`标签，它还允许我们编写更多的语义 HTML，如前所述，这是可访问代码的基础。实现可访问性的其他技巧包括:

*   将`div`改为一篇文章/部分会产生巨大的不同
*   使用 h1–h6 作为标题，通知屏幕阅读器页面部分
*   使用链接进行导航
*   用一个按钮处理所有点击功能
*   如果元素不在屏幕上，请确保将其可见性设置为隐藏
*   充分利用 [ARIA](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/heading_role) ,它有助于将语义行为添加到非隐式语义的元素中，例如，侧边栏应该使用 aria-expand 让屏幕读者知道
*   给按钮贴标签，提供按钮功能的附加信息

## 焦点和焦点管理

焦点是指当你发送信息时，接收输入的计算机屏幕的控制，这通常与键盘有关。每当你试图填写表格或使用网页的特定部分时，你必须把它放在焦点上。现在，这对于那些宁愿使用键盘使用`tab`和`shift`键来导航平台或者有某种运动障碍的用户来说很重要。

### 焦点管理:

精心规划的焦点管理对于确保舒适的用户体验非常重要。这意味着将光标从应用程序的一部分移动到另一部分。为了帮助用户(大多数是运动障碍者)在应用程序的预期流程中使用键盘导航平台。有些元素是隐式聚焦的，比如表单元素、锚元素，而其他元素则不是(比如`p`、`h2`标签)。

## 使用`ref`在 React 中实现焦点管理

为了使用 React 聚焦一个元素，我们创建了一个函数 [Ref](https://reactjs.org/docs/refs-and-the-dom.html) ，这是一个设置在我们想要引用的元素上的属性，它允许我们在 React 中选择并引用页面上的一个实际 DOM 节点。

```
<div
  ref ={
    (loadingNames)=> {
        this.loadingNames = loadingNames;
    }
  }
  tabIndex = "-1"
>
Loading List of Names...
</div>
```

上面的代码将 div 的`ref`分配给类属性`this.loadingNames`来实现一个 ref。我们使用[componentdimount](https://reactjs.org/docs/react-component.html)生命周期，然后像这样调用`ref`元素的 focus 元素:

```
componentDidMount(){
    this.loadingNames.focus()
    }
```

因此，当名字列表加载时，键盘焦点指示器会在内容上放置一个焦点环。

使用`[ref](https://reactjs.org/docs/refs-and-the-dom.html)`的另一个用例是确保我们在使用 [react-router](https://reactjs.org/docs/refs-and-the-dom.html) 时将焦点转移到新页面，通过调用页面顶部的`ref`并使用户从`<link>`连接到的新页面的顶部导航。

```
<div
ref={
  (topOfNewPage)=>{
    this.topOfNewPage = topOfNewPage;
  }
}
 tabIndex = "-1"
 aria-labelledby = "pageHeading"
>
  <Header / >
    <h1 id ="pageHeading"> </h1>
   <Footer/>
</div>
```

像这样使用 ref:

```
componentDidMount(){
this.topOfNewPage.focus()
}
```

随着 [React v16.8](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html) 的发布，有了一种使用`React.createRef()` API 编写`refs`的方法。你可以在[官方文档](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs)中找到更多信息。

## 实现语义 JSX 时的其他有用提示

### 1.使用 componentDidMount 设置页面标题

这有助于增加 [SEO](https://searchengineland.com/guide/what-is-seo) ，也可以更新浏览器标签中的内容，这也使屏幕阅读器能够更好地了解他们在应用程序中的当前位置。

```
componentDidMount(){
    document.title = 'Input your page title '
    }
```

其他的方法是使用像 [react-document-title](https://www.npmjs.com/package/react-document-title) 和[react-头盔](https://www.npmjs.com/package/react-helmet)这样的包，它们可以通过 [NPM 安装到项目中。](https://www.npmjs.com/)

### 2.react accessibility linter(eslint-plugin-jsx-a11y)

使用 linter 有助于通过检查由团队或个人确定的代码标准编写的代码来确定干净的代码。使用 eslint-plugin-jsx-a11ylinter，我们可以建立一个更容易使用的 react 应用程序。您还可以将它配置为与您的文本编辑器一起工作，以便获得实时错误输出。也可以使用 [NPM](https://www.npmjs.com/) 进行安装。

```
npm install eslint esline-plugin-jsx-a11y --save-dev
```

### 3.编写语义 HTML

大多数情况下，编写具有语义的代码可以大大有助于一个更易访问的平台，在本文的前面，我陈述了实现这一点的方法。同时参考本[指南](https://www.w3.org/WAI/standards-guidelines/wcag/)可以对此有所帮助。

## 结论

在本文中，我们试图理解应用于 React 应用程序的 web 可访问性的基础知识，并从总体上了解一些可访问性原则。我希望我们在构建 React 应用程序时将这些实践付诸实践，为每个人提供一个更好的 web。编码快乐！😄

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)